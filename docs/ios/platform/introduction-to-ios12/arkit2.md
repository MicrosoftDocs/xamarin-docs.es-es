---
title: ARKit 2 en Xamarin. iOS
description: En este documento se describen las actualizaciones de ARKit en iOS 12. Se centra en el uso de imágenes y objetos de referencia para la detección, incluye código para la texturización del entorno y describe problemas comunes en la programación de ARKit.
ms.prod: xamarin
ms.assetid: af758092-1523-4ab7-aa53-c37a81fb156a
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/22/2018
ms.openlocfilehash: c460eb28197ded7e56c6e11cde98abe201bd06b5
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434276"
---
# <a name="arkit-2-in-xamarinios"></a>ARKit 2 en Xamarin. iOS

ARKit ha madurado considerablemente desde su lanzamiento el último año en iOS 11. En primer lugar, ahora puede detectar planos verticales y horizontales, lo que mejora en gran medida la práctica de experiencias de realidad aumentada en el interior. Además, hay nuevas capacidades:

- Reconocimiento de imágenes y objetos de referencia como unión entre el mundo real y el de imágenes digitales
- Un nuevo modo de iluminación que simula luz del mundo real
- La capacidad de compartir y conservar entornos AR
- Un nuevo formato de archivo preferido para almacenar contenido de AR

## <a name="recognizing-reference-objects"></a>Reconocer objetos de referencia

Una característica de presentación de ARKit 2 es la capacidad de reconocer objetos y imágenes de referencia. Las imágenes de referencia se pueden cargar desde archivos de imagen normales (que se[describen más adelante](#more-tracking-configurations)), pero se deben analizar objetos de referencia con el foco del desarrollador [`ARObjectScanningConfiguration`](xref:ARKit.ARObjectScanningConfiguration) .

### <a name="sample-app-scanning-and-detecting-3d-objects"></a>Aplicación de ejemplo: examinar y detectar objetos 3D

El ejemplo de [detección y detección de objetos 3D](/samples/xamarin/ios-samples/ios12-scanninganddetecting3dobjects) es un puerto de un [proyecto de Apple](https://developer.apple.com/documentation/arkit/scanning_and_detecting_3d_objects?language=objc) que muestra:

- Administración del estado de la aplicación mediante [`NSNotification`](xref:Foundation.NSNotification) objetos
- Visualización personalizada
- Gestos complejos
- Examen de objetos
- Almacenar un [`ARReferenceObject`](xref:ARKit.ARReferenceObject)

El análisis de un objeto de referencia es un uso intensivo del procesador y de la batería, y los dispositivos más antiguos suelen tener problemas para lograr un seguimiento estable.

### <a name="state-management-using-nsnotification-objects"></a>Administración de Estados mediante objetos NSNotification

Esta aplicación usa una máquina de Estados que realiza la transición entre los Estados siguientes:

- `AppState.StartARSession`
- `AppState.NotReady`
- `AppState.Scanning`
- `AppState.Testing`

Además, usa un conjunto incrustado de Estados y transiciones cuando está en `AppState.Scanning` :

- `Scan.ScanState.Ready`
- `Scan.ScanState.DefineBoundingBox`
- `Scan.ScanState.Scanning`
- `Scan.ScanState.AdjustingOrigin`

La aplicación usa una arquitectura reactiva que envía notificaciones de transición de estado [`NSNotificationCenter`](xref:Foundation.NSNotificationCenter) y se suscribe a estas notificaciones. La configuración es similar a la de este fragmento de código `ViewController.cs` :

```csharp
// Configure notifications for application state changes
var notificationCenter = NSNotificationCenter.DefaultCenter;

notificationCenter.AddObserver(Scan.ScanningStateChangedNotificationName, State.ScanningStateChanged);
notificationCenter.AddObserver(ScannedObject.GhostBoundingBoxCreatedNotificationName, State.GhostBoundingBoxWasCreated);
notificationCenter.AddObserver(ScannedObject.GhostBoundingBoxRemovedNotificationName, State.GhostBoundingBoxWasRemoved);
notificationCenter.AddObserver(ScannedObject.BoundingBoxCreatedNotificationName, State.BoundingBoxWasCreated);
notificationCenter.AddObserver(BoundingBox.ScanPercentageChangedNotificationName, ScanPercentageChanged);
notificationCenter.AddObserver(BoundingBox.ExtentChangedNotificationName, BoundingBoxExtentChanged);
notificationCenter.AddObserver(BoundingBox.PositionChangedNotificationName, BoundingBoxPositionChanged);
notificationCenter.AddObserver(ObjectOrigin.PositionChangedNotificationName, ObjectOriginPositionChanged);
notificationCenter.AddObserver(NSProcessInfo.PowerStateDidChangeNotification, DisplayWarningIfInLowPowerMode);

```

Un controlador de notificación típico actualizará la interfaz de usuario y, posiblemente, modificará el estado de la aplicación, como este controlador que se actualiza a medida que se examina el objeto:

```csharp
private void ScanPercentageChanged(NSNotification notification)
{
    var pctNum = TryGet<NSNumber>(notification.UserInfo, BoundingBox.ScanPercentageUserKey);
    if (pctNum == null)
    {
        return;
    }
    double percentage = pctNum.DoubleValue;
    // Switch to the next state if scan is complete
    if (percentage >= 100.0)
    {
        State.SwitchToNextState();
    }
    else
    {
        DispatchQueue.MainQueue.DispatchAsync(() => navigationBarController.SetNavigationBarTitle($"Scan ({percentage})"));
    }
}

```

Por último, los `Enter{State}` métodos modifican el modelo y la experiencia de usuario según corresponda al nuevo estado:

```csharp
internal void EnterStateTesting()
{
    navigationBarController.SetNavigationBarTitle("Testing");
    navigationBarController.ShowBackButton(false);
    loadModelButton.Hidden = true;
    flashlightButton.Hidden = false;
    nextButton.Enabled = true;
    nextButton.SetTitle("Share", UIControlState.Normal);

    testRun = new TestRun(sessionInfo, sceneView);
    TestObjectDetection();
    CancelMaxScanTimeTimer();
}
```

### <a name="custom-visualization"></a>Visualización personalizada

La aplicación muestra la "nube de punto de bajo nivel" del objeto contenido dentro de un cuadro de límite proyectado en un plano horizontal detectado.

Esta nube de punto está disponible para los desarrolladores en la [`ARFrame.RawFeaturePoints`](xref:ARKit.ARFrame.RawFeaturePoints) propiedad. La visualización eficaz de la nube puntual puede ser un problema complicado. La iteración sobre los puntos y la creación y colocación de un nuevo nodo SceneKit para cada punto eliminaría la velocidad de fotogramas. Como alternativa, si se realiza de forma asincrónica, se producirá un retraso. El ejemplo mantiene el rendimiento con una estrategia de tres partes:

- Usar código no seguro para anclar los datos en su lugar e interpretar los datos como un búfer sin formato de bytes.
- Convertir ese búfer sin formato en [`SCNGeometrySource`](xref:SceneKit.SCNGeometrySource) y crear un objeto "template" [`SCNGeometryElement`](xref:SceneKit.SCNGeometryElement) .
- "Unir juntos" rápidamente los datos sin procesar y la plantilla mediante [`SCNGeometry.Create(SCNGeometrySource[], SCNGeometryElement[])`](xref:SceneKit.SCNGeometry.Create(SceneKit.SCNGeometrySource[],SceneKit.SCNGeometryElement[]))

```csharp
internal static SCNGeometry CreateVisualization(NVector3[] points, UIColor color, float size)
{
  if (points.Length == 0)
  {
    return null;
  }

  unsafe
  {
    var stride = sizeof(float) * 3;

    // Pin the data down so that it doesn't move
    fixed (NVector3* pPoints = &amp;points[0])
    {
      // Important: Don't unpin until after `SCNGeometry.Create`, because geometry creation is lazy

      // Grab a pointer to the data and treat it as a byte buffer of the appropriate length
      var intPtr = new IntPtr(pPoints);
      var pointData = NSData.FromBytes(intPtr, (System.nuint) (stride * points.Length));

      // Create a geometry source (factory) configured properly for the data (3 vertices)
      var source = SCNGeometrySource.FromData(
        pointData,
        SCNGeometrySourceSemantics.Vertex,
        points.Length,
        true,
        3,
        sizeof(float),
        0,
        stride
      );

      // Create geometry element
      // The null and bytesPerElement = 0 look odd, but this is just a template object
      var template = SCNGeometryElement.FromData(null, SCNGeometryPrimitiveType.Point, points.Length, 0);
      template.PointSize = 0.001F;
      template.MinimumPointScreenSpaceRadius = size;
      template.MaximumPointScreenSpaceRadius = size;

      // Stitch the data (source) together with the template to create the new object
      var pointsGeometry = SCNGeometry.Create(new[] { source }, new[] { template });
      pointsGeometry.Materials = new[] { Utilities.Material(color) };
      return pointsGeometry;
    }
  }
}
```

El resultado tiene el aspecto siguiente:

![point_cloud](images/arkit_point_cloud.jpeg)

### <a name="complex-gestures"></a>Gestos complejos

El usuario puede escalar, girar y arrastrar el rectángulo de selección que rodea el objeto de destino. Hay dos cosas interesantes en los reconocedores de gestos asociados.

En primer lugar, todos los reconocedores de gestos se activan solo después de que se haya pasado un umbral; por ejemplo, un dedo ha arrastrado muchos píxeles o la rotación supera un ángulo. La técnica consiste en acumular el movimiento hasta que se supere el umbral y, a continuación, aplicarlo incrementalmente:

```csharp
// A custom rotation gesture recognizer that fires only when a threshold is passed
internal partial class ThresholdRotationGestureRecognizer : UIRotationGestureRecognizer
{
    // The threshold after which this gesture is detected.
    const double threshold = Math.PI / 15; // (12°)

    // Indicates whether the currently active gesture has exceeded the threshold
    private bool thresholdExceeded = false;

    private double previousRotation = 0;
    internal double RotationDelta { get; private set; }

    internal ThresholdRotationGestureRecognizer(IntPtr handle) : base(handle)
    {
    }

    // Observe when the gesture's state changes to reset the threshold
    public override UIGestureRecognizerState State
    {
        get => base.State;
        set
        {
            base.State = value;

            switch(value)
            {
                case UIGestureRecognizerState.Began :
                case UIGestureRecognizerState.Changed :
                    break;
                default :
                    // Reset threshold check
                    thresholdExceeded = false;
                    previousRotation = 0;
                    RotationDelta = 0;
                    break;
            }
        }
    }

    public override void TouchesMoved(NSSet touches, UIEvent evt)
    {
        base.TouchesMoved(touches, evt);

        if (thresholdExceeded)
        {
            RotationDelta = Rotation - previousRotation;
            previousRotation = Rotation;
        }

        if (! thresholdExceeded && Math.Abs(Rotation) > threshold)
        {
            thresholdExceeded = true;
            previousRotation = Rotation;
        }
    }
}
```

La segunda cosa interesante que se lleva a cabo en relación con los gestos es la manera en que el cuadro de límite se mueve con respecto a los planos del mundo real detectados. Este aspecto se describe en [esta entrada de blog de Xamarin](https://blog.xamarin.com/exploring-new-ios-12-arkit-capabilities-with-xamarin/).

## <a name="other-new-features-in-arkit-2"></a>Otras características nuevas de ARKit 2

### <a name="more-tracking-configurations"></a>Más configuraciones de seguimiento

Ahora puede usar cualquiera de los siguientes como base para una experiencia de realidad mixta:

- Solo el acelerómetro del dispositivo ( [`AROrientationTrackingConfiguration`](xref:ARKit.AROrientationTrackingConfiguration) , iOS 11)
- Caras ( [`ARFaceTrackingConfiguration`](xref:ARKit.ARFaceTrackingConfiguration) , iOS 11)
- Imágenes de referencia ( [`ARImageTrackingConfiguration`](xref:ARKit.ARImageTrackingConfiguration) , iOS 12)
- Examinar objetos 3D ( [`ARObjectScanningConfiguration`](xref:ARKit.ARObjectScanningConfiguration) , iOS 12)
- Odometry inercial visual ( [`ARWorldTrackingConfiguration`](xref:ARKit.ARWorldTrackingConfiguration) , mejorado en iOS 12)

`AROrientationTrackingConfiguration`, que se describe en [esta entrada de blog y el ejemplo de F #](https://github.com/lobrien/FSharp_Face_AR), es el más limitado y proporciona una experiencia de realidad mixta insuficiente, ya que solo coloca objetos digitales en relación con el movimiento del dispositivo, sin intentar unir el dispositivo y la pantalla en el mundo real.

`ARImageTrackingConfiguration`Le permite reconocer imágenes 2D del mundo real (dibujos, logotipos, etc.) y usarlas para delimitar imágenes digitales:

```csharp
var imagesAndWidths = new[] {
    ("cover1.jpg", 0.185F),
    ("cover2.jpg", 0.185F),
     //...etc...
    ("cover100.jpg", 0.185F),
};

var referenceImages = new NSSet<ARReferenceImage>(
    imagesAndWidths.Select( imageAndWidth =>
    {
      // Tuples cannot be destructured in lambda arguments
        var (image, width) = imageAndWidth;
        // Read the image
        var img = UIImage.FromFile(image).CGImage;
        return new ARReferenceImage(img, ImageIO.CGImagePropertyOrientation.Up, width);
    }).ToArray());

configuration.TrackingImages = referenceImages;
```

Hay dos aspectos interesantes para esta configuración:

- Es eficaz y se puede usar con un gran número de imágenes de referencia
- La imagen digital está anclada a la imagen, incluso si esa imagen se mueve en el mundo real (por ejemplo, si se reconoce la portada de un libro, realizará un seguimiento del libro mientras se saca del estante, se establece, etc.).

`ARObjectScanningConfiguration`Se explicó [anteriormente](#recognizing-reference-objects) y es una configuración centrada en el desarrollador para la exploración de objetos 3D. Tiene un uso intensivo del procesador y de la batería, y no debe usarse en las aplicaciones de usuario final. En el ejemplo de [análisis y detección de objetos 3D](/samples/xamarin/ios-samples/ios12-scanninganddetecting3dobjects) se muestra el uso de esta configuración.

La configuración de seguimiento final, `ARWorldTrackingConfiguration` , es la potencia de la mayoría de las experiencias de realidad mixta. Esta configuración usa "visual inercial Odometry" para relacionar los "puntos de características" del mundo real con las imágenes digitales. La geometría digital o los sprites se delimitan en relación con los planos horizontales y verticales del mundo real o con respecto a `ARReferenceObject` las instancias detectadas. En esta configuración, el origen mundial es la posición original de la cámara en el espacio con el eje Z alineado con la gravedad y los objetos digitales "permanecen en su lugar" con respecto a los objetos del mundo real.

### <a name="environmental-texturing"></a>Texturización medioambiental

ARKit 2 admite "texturización medioambiental", que usa imágenes capturadas para estimar la iluminación e incluso aplicar resaltes especulares a objetos brillantes. El mapa ambiental se crea dinámicamente y, una vez que la cámara ha buscado en todas las direcciones, puede generar una experiencia impresionantemente realista:

![imagen de demostración de la texturización medioambiental](images/arkit_env_texturing.png)

Para usar la texturización medioambiental:

- Los [`SCNMaterial`](xref:SceneKit.SCNMaterial) objetos deben usar [`SCNLightingModel.PhysicallyBased`](xref:SceneKit.SCNLightingModel.PhysicallyBased) y asignar un valor en el intervalo de 0 a 1 para [`Metalness.Contents`](xref:SceneKit.SCNMaterial.Metalness) y [`Roughness.Contents`](xref:SceneKit.SCNMaterialProperty.Contents) y
- La configuración de seguimiento debe establecer [`EnvironmentTexturing`](xref:ARKit.ARWorldTrackingConfiguration.EnvironmentTexturing)  =  [`AREnvironmentTexturing.Automatic`](xref:ARKit.AREnvironmentTexturing.Automatic) :

```csharp
var sphere = SCNSphere.Create(0.33F);
sphere.FirstMaterial.LightingModelName = SCNLightingModel.PhysicallyBased;
// Shiny metallic sphere
sphere.FirstMaterial.Metalness.Contents = new NSNumber(1.0F);
sphere.FirstMaterial.Roughness.Contents = new NSNumber(0.0F);

// Session configuration:
var configuration = new ARWorldTrackingConfiguration
{
    PlaneDetection = ARPlaneDetection.Horizontal | ARPlaneDetection.Vertical,
    LightEstimationEnabled = true,
    EnvironmentTexturing = AREnvironmentTexturing.Automatic
};
```

Aunque la textura perfectamente reflectante que se muestra en el fragmento de código anterior es divertida en un ejemplo, es probable que la texturización medioambiental se use mejor con la retención consulta que desencadene una respuesta "no relatada del valle" (la textura es solo una estimación basada en lo que se grabó en la cámara).

### <a name="shared-and-persistent-ar-experiences"></a>Experiencias de AR compartidas y persistentes

Otra adición importante a ARKit 2 es la [`ARWorldMap`](xref:ARKit.ARWorldMap) clase, que permite compartir o almacenar datos de seguimiento mundial. Obtiene el mapa del mundo actual con [`ARSession.GetCurrentWorldMapAsync`](xref:ARKit.ARSession.GetCurrentWorldMapAsync) o [`GetCurrentWorldMap(Action<ARWorldMap,NSError>)`](xref:ARKit.ARSession.GetCurrentWorldMap(System.Action{ARKit.ARWorldMap,Foundation.NSError})) :

```csharp
// Local storage
var PersistentWorldPath => Environment.GetFolderPath(Environment.SpecialFolder.Personal) + "/arworldmap";

// Later, after scanning the environment thoroughly...
var worldMap = await Session.GetCurrentWorldMapAsync();
if (worldMap != null)
{
    var data = NSKeyedArchiver.ArchivedDataWithRootObject(worldMap, true, out var err);
    if (err != null)
    {
        Console.WriteLine(err);
    }
    File.WriteAllBytes(PersistentWorldPath, data.ToArray());
}
```

Para compartir o restaurar el mapa del mundo:

1. Cargue los datos del archivo.
2. Desarchivelo en un `ARWorldMap` objeto,
3. Úselo como el valor de la [`ARWorldTrackingConfiguration.InitialWorldMap`](xref:ARKit.ARWorldTrackingConfiguration.InitialWorldMap) propiedad:

```csharp
var data = NSData.FromArray(File.ReadAllBytes(PersistentWorldController.PersistenWorldPath));
var worldMap = (ARWorldMap)NSKeyedUnarchiver.GetUnarchivedObject(typeof(ARWorldMap), data, out var err);

var configuration = new ARWorldTrackingConfiguration
{
    PlaneDetection = ARPlaneDetection.Horizontal | ARPlaneDetection.Vertical,
    LightEstimationEnabled = true,
    EnvironmentTexturing = AREnvironmentTexturing.Automatic,
    InitialWorldMap = worldMap
};
```

`ARWorldMap`Solo contiene datos de seguimiento mundial no visibles y los [`ARAnchor`](xref:ARKit.ARAnchor) objetos, _no_ contiene recursos digitales. Para compartir geometría o imagen, tendrá que desarrollar su propia estrategia adecuada para su caso de uso (quizás mediante el almacenamiento y la transmisión solo de la ubicación y la orientación de la geometría y su aplicación a estática `SCNGeometry` o quizás mediante el almacenamiento y la transmisión de objetos serializados). La ventaja de `ARWorldMap` es que los activos, una vez colocados en relación con un compartido `ARAnchor` , aparecerán de forma coherente entre los dispositivos o las sesiones.

### <a name="universal-scene-description-file-format"></a>Formato de archivo de descripción de escena universal

La característica de encabezado final de ARKit 2 es la adopción de Apple del formato de archivo de descripción de la [escena universal](https://graphics.pixar.com/usd/docs/Introduction-to-USD.html) de Pixar. Este formato reemplaza el formato de DAE de Collada como el formato preferido para compartir y almacenar recursos de ARKit. La compatibilidad con la visualización de recursos se integra en iOS 12 y Mojave. La extensión de archivo USDZ es un archivo zip sin comprimir y sin cifrar que contiene archivos USD. Pixar [proporciona herramientas para trabajar con archivos USD](https://graphics.pixar.com/usd/docs/USD-Toolset.html#USDToolset-usdedit) , pero todavía no hay mucha compatibilidad de terceros.

## <a name="arkit-programming-tips"></a>Sugerencias de programación de ARKit

### <a name="manual-resource-management"></a>Administración manual de recursos

En ARKit, es fundamental administrar los recursos manualmente. Esto no solo permite velocidades de fotogramas altas, sino que realmente es _necesario_ evitar una "inmovilización de pantalla" confusa. El marco de trabajo de ARKit es perezoso al proporcionar un nuevo fotograma de cámara ( [`ARSession.CurrentFrame`](xref:ARKit.ARSession.CurrentFrame) . Hasta que [`ARFrame`](xref:ARKit.ARFrame) se haya `Dispose()` llamado al método actual, ARKit no proporcionará un nuevo marco. Esto hace que el vídeo se "inmovilice", aunque el resto de la aplicación responda. La solución consiste siempre en acceder `ARSession.CurrentFrame` con un `using` bloque o llamar manualmente a `Dispose()` en él.

Todos los objetos derivados de `NSObject` son `IDisposable` e `NSObject` implementan el [patrón Dispose](/dotnet/standard/design-guidelines/dispose-pattern), por lo que normalmente debe seguir [este patrón para implementar `Dispose` en una clase derivada](/dotnet/standard/garbage-collection/implementing-dispose).

### <a name="manipulating-transform-matrices"></a>Manipular matrices de transformación

En cualquier aplicación 3D, va a trabajar con matrices de transformación 4x4 que describen de forma compacta cómo moverse, girar y distorsionar un objeto a través de un espacio 3D. En SceneKit, se trata de [`SCNMatrix4`](xref:SceneKit.SCNMatrix4) objetos.  

La [`SCNNode.Transform`](xref:SceneKit.SCNNode.Transform) propiedad devuelve la `SCNMatrix4` matriz de transformación de [`SCNNode`](xref:SceneKit.SCNNode) _según_ el tipo de fila principal `simdfloat4x4` . Por lo tanto, por ejemplo:

```csharp
var node = new SCNNode { Position = new SCNVector3(2, 3, 4) };  
var xform = node.Transform;
Console.WriteLine(xform);
// Output is: "(1, 0, 0, 0)\n(0, 1, 0, 0)\n(0, 0, 1, 0)\n(2, 3, 4, 1)"
```  

Como puede ver, la posición se codifica en los tres primeros elementos de la fila inferior.

En Xamarin, el tipo común para manipular matrices de transformación es `NVector4` , que por Convención se interpreta de forma principal de columna. Es decir, el componente Translation/Position se espera en M14, M24, M34, not M41, M42, M43:

![fila: principal y columna principal](images/arkit_row_vs_column.png)

Ser coherente con la elección de la interpretación de la matriz es vital para el comportamiento adecuado. Dado que las matrices de transformación 3D son 4x4, los errores de coherencia no producirán ningún tipo de excepción en tiempo de compilación ni siquiera en tiempo de ejecución, sino que las operaciones actuarán de forma inesperada. Si parece que los objetos SceneKit/ARKit se bloquean, desaparecen o vibran, una matriz de transformación incorrecta es una buena posibilidad. La solución es sencilla: [`NMatrix4.Transpose`](xref:OpenTK.NMatrix4.Transpose*) realizará una transposición en contexto de los elementos.

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo: examinar y detectar objetos 3D](/samples/xamarin/ios-samples/ios12-scanninganddetecting3dobjects)
- [Novedades de ARKit 2 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/602/)
- [Descripción del seguimiento y la detección de ARKit (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/610/)
- [Introducción a ARKit en Xamarin. iOS](../introduction-to-ios11/arkit/index.md)