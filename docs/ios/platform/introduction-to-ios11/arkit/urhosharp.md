---
title: Uso de ARKit con UrhoSharp en Xamarin. iOS
description: En este documento se describe cómo configurar una aplicación de ARKit en Xamarin. iOS y, a continuación, se examina cómo se representan los fotogramas, cómo se ajusta la cámara, cómo se detectan los planos, cómo se trabaja con la iluminación, etc. También se describe UrhoSharp y la escritura de código para HoloLens.
ms.prod: xamarin
ms.assetid: 877AF974-CC2E-48A2-8E1A-0EF9ABF2C92D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/01/2017
ms.openlocfilehash: 67ee62fe18385f3a79f4afcb26299990f4666763
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032236"
---
# <a name="using-arkit-with-urhosharp-in-xamarinios"></a>Uso de ARKit con UrhoSharp en Xamarin. iOS

Con la introducción de [ARKit](https://developer.apple.com/arkit/), Apple ha facilitado a los desarrolladores la creación de aplicaciones de realidad aumentadas. ARKit puede realizar un seguimiento de la posición exacta del dispositivo y detectar varias superficies del mundo, y es el desarrollador quien combina los datos procedentes de ARKit en el código.

[UrhoSharp](~/graphics-games/urhosharp/index.md) proporciona una API 3D completa y fácil de usar que puede usar para crear aplicaciones 3D.   Ambos se pueden mezclar juntos, ARKit para proporcionar la información física sobre el mundo y Urho para representar los resultados.

En esta página se explica cómo conectar estos dos mundos juntos para crear grandes aplicaciones de realidad ampliada.

## <a name="the-basics"></a>Conceptos básicos

Lo que queremos hacer es presentar contenido 3D en la parte superior del mundo, tal como se muestra en el iPhone o iPad.   La idea es mezclar el contenido procedente de la cámara del dispositivo con el contenido 3D y, a medida que el usuario del dispositivo se desplaza por la habitación, para asegurarse de que el objeto 3D se comporte como parte de ese salón; esto se hace delimitando los objetos en este mundo.

![Ilustración animada en ARKit](urhosharp-images/image1.gif)

Usaremos la biblioteca Urho para cargar nuestros activos en 3D y colocarlos en todo el mundo y usaremos ARKit para obtener el flujo de vídeo procedente de la cámara, así como la ubicación del teléfono en el mundo.   A medida que el usuario se mueve con su teléfono, se usarán los cambios en la ubicación para actualizar el sistema de coordenadas que se muestra en el motor de Urho.

De este modo, cuando se coloca un objeto en el espacio 3D y el usuario se mueve, la ubicación del objeto 3D refleja el lugar y la ubicación donde se colocó.

## <a name="setting-up-your-application"></a>Configuración de la aplicación

### <a name="ios-application-launch"></a>Inicio de la aplicación de iOS

La aplicación de iOS debe crear e iniciar el contenido 3D. para ello, cree una clase que implemente una subclase de la `Urho.Application` y proporcione el código de instalación invalidando el método `Start`.  Aquí es donde la escena se rellena con datos, los controladores de eventos se configuran y así sucesivamente.

Hemos introducido una `Urho.ArkitApp` clase que subclase `Urho.Application` y en su método `Start` realiza el pesado trabajo.   Todo lo que necesita hacer en la aplicación Urho existente es cambiar la clase base para que sea de tipo `Urho.ArkitApp` y tener una aplicación que ejecute su escena de Urho en el mundo.

### <a name="the-arkitapp-class"></a>La clase ArkitApp

Esta clase proporciona un conjunto de valores predeterminados cómodos, tanto una escena con algunos objetos clave como el procesamiento de eventos ARKit, ya que el sistema operativo los entrega.

La instalación se realiza en el método virtual `Start`.   Cuando invalide este método en la subclase, debe asegurarse de encadenar a su elemento primario mediante `base.Start()` en su propia implementación.

El método `Start` configura la escena, la ventanilla, la cámara y una luz direccional, y las muestra como propiedades públicas:

- `Scene` para contener los objetos,
- `Light` direccional con sombras y cuya ubicación está disponible a través de la propiedad `LightNode`
- `Camera` cuyos componentes se actualizan cuando ARKit entrega una actualización a la aplicación y
- `ViewPort` que muestra los resultados.

### <a name="your-code"></a>El código

A continuación, debe subclaser la `ArkitApp` clase e invalidar el método `Start`.   Lo primero que debe hacer el método es encadenar al `ArkitApp.Start` llamando a `base.Start()`.  Después, puede usar cualquiera de las propiedades de configuración de ArkitApp para agregar los objetos a la escena, personalizar las luces, las sombras o los eventos que desea controlar.

El ejemplo ARKit/UrhoSharp carga un carácter animado con texturas y reproduce la animación, con la siguiente implementación:

```csharp
public class MutantDemo : ArkitApp
{
    [Preserve]
    public MutantDemo(ApplicationOptions opts) : base(opts) { }

    Node mutantNode;

    protected override void Start()
    {
        base.Start ();

        // Mutant
        mutantNode = Scene.CreateChild();
        mutantNode.Rotation = new Quaternion(x: 0, y:15, z:0);
        mutantNode.Position = new Vector3(0, -1f, 2f); /*two meters away*/
        mutantNode.SetScale(0.5f);

        var mutant = mutantNode.CreateComponent<AnimatedModel>();
        mutant.Model = ResourceCache.GetModel("Models/Mutant.mdl");
        mutant.Material = ResourceCache.GetMaterial("Materials/mutant_M.xml");

        var animation = mutantNode.CreateComponent<AnimationController>();
        animation.Play("Animations/Mutant_HipHop1.ani", 0, true, 0.2f);
    }
}
```

Y eso es realmente todo lo que tiene que hacer en este momento para que el contenido 3D se muestre en realidad aumentada.

Urho usa formatos personalizados para modelos 3D y animaciones, por lo que debe exportar los recursos a este formato.   Puede usar herramientas como el [complemento Urho3D de mezclador](https://github.com/reattiva/Urho3D-Blender) y [UrhoAssetImporter](https://github.com/EgorBo/UrhoAssetImporter) que pueden convertir estos recursos de formatos populares como dbx, DAE, OBJ, Blend, 3D-max en el formato requerido por Urho.

Para obtener más información sobre la creación de aplicaciones 3D con Urho, visite la guía de [Introducción a UrhoSharp](~/graphics-games/urhosharp/introduction.md) .

## <a name="arkitapp-in-depth"></a>ArkitApp en profundidad

> [!NOTE]
> Esta sección está dirigida a los desarrolladores que quieren personalizar la experiencia predeterminada de UrhoSharp y ARKit o que desean obtener información más detallada sobre cómo funciona la integración.   No es necesario leer esta sección.

La API de ARKit es bastante sencilla, cree y configure un objeto [ARSession](https://developer.apple.com/documentation/arkit/arsession) que empiece a entregar objetos [ARFrame](https://developer.apple.com/documentation/arkit/arframe) .   Contienen tanto la imagen capturada por la cámara como la posición real del dispositivo estimada.

Vamos a crear las imágenes que va a entregar la cámara a nosotros con nuestro contenido 3D y ajustaremos la cámara en UrhoSharp para que se ajuste a las posibilidades de la ubicación y la posición del dispositivo.

En el diagrama siguiente se muestra lo que se está llevando a cabo en la clase `ArkitApp`:

[![diagrama de clases y pantallas de ArkitApp](urhosharp-images/image2.png)](urhosharp-images/image2.png#lightbox)

### <a name="rendering-the-frames"></a>Representación de los marcos

La idea es sencilla. Combine el vídeo que sale de la cámara con nuestros gráficos 3D para generar la imagen combinada.     Se va a obtener una serie de estas imágenes capturadas en secuencia y se combinará esta entrada con la escena Urho.

La manera más sencilla de hacerlo es insertar un `RenderPathCommand` en el `RenderPath`principal.  Se trata de un conjunto de comandos que se realizan para dibujar un solo fotograma.  Este comando rellenará la ventanilla con cualquier textura que se le pase.    Lo configuramos en el primer fotograma que se está procesando y la definición real se realiza en el archivo **ARRenderPath. XML** que se carga en este momento.

Sin embargo, nos enfrentamos a dos problemas para mezclar estos dos mundos juntos:

1. En iOS, las texturas de GPU deben tener una resolución que sea una potencia de dos, pero los fotogramas que se van a obtener de la cámara no tendrán una solución que sea una potencia de dos, por ejemplo: 1280x720.
2. Los fotogramas se codifican en formato [YUV](https://en.wikipedia.org/wiki/YUV) , representado por dos imágenes: luminancia y croma.

Los fotogramas YUV tienen dos resoluciones diferentes.  una imagen de 1280x720 que representa la luminancia (básicamente, una imagen de escala de grises) y un 640 x 360 mucho más pequeño para el componente de crominancia:

![Imagen que muestra la combinación de componentes y y UV](urhosharp-images/image3.png)

Para dibujar una imagen de color completo con OpenGL ES, es necesario escribir un sombreador pequeño que tome luminancia (componente Y) y crominancia (aviones UV) de las ranuras de textura.  En UrhoSharp, tienen nombres: "sDiffMap" y "sNormalMap" y los convierten en formato RGB:

```csharp
mat4 ycbcrToRGBTransform = mat4(
    vec4(+1.0000, +1.0000, +1.0000, +0.0000),
    vec4(+0.0000, -0.3441, +1.7720, +0.0000),
    vec4(+1.4020, -0.7141, +0.0000, +0.0000),
    vec4(-0.7010, +0.5291, -0.8860, +1.0000));

vec4 ycbcr = vec4(texture2D(sDiffMap, vTexCoord).r,
                    texture2D(sNormalMap, vTexCoord).ra, 1.0);
gl_FragColor = ycbcrToRGBTransform * ycbcr;
```

Para representar la textura que no tiene una potencia de dos resoluciones, tenemos que definir Texture2D con los parámetros siguientes:

```chsarp
// texture for UV-plane;
cameraUVtexture = new Texture2D();
cameraUVtexture.SetNumLevels(1);
cameraUVtexture.SetSize(640, 360, Graphics.LuminanceAlphaFormat, TextureUsage.Dynamic);
cameraUVtexture.FilterMode = TextureFilterMode.Bilinear;
cameraUVtexture.SetAddressMode(TextureCoordinate.U, TextureAddressMode.Clamp);
cameraUVtexture.SetAddressMode(TextureCoordinate.V, TextureAddressMode.Clamp);
```

Por lo tanto, podemos representar las imágenes capturadas como un fondo y representar cualquier escena por encima de ella como el mutante.

### <a name="adjusting-the-camera"></a>Ajuste de la cámara

Los objetos `ARFrame` también contienen la posición estimada del dispositivo.  Ahora tenemos que trasladar la cámara de juego ARFrame-Before ARKit no es una gran cantidad de seguimiento de la orientación del dispositivo (rollo, tono y guiñada) y representa un holograma anclado en la parte superior del vídeo, pero si mueve el dispositivo, se desplazarán los hologramas.

Esto sucede porque los sensores integrados, como giroscopio, no pueden realizar un seguimiento de los movimientos, solo pueden acelerar.  ARKit analiza cada fotograma y extrae los puntos de características para realizar el seguimiento y, por lo tanto, puede proporcionarnos una matriz de transformación precisa que contenga datos de movimiento y rotación.

Por ejemplo, este es el procedimiento para obtener la posición actual:

```csharp
var row = arCamera.Transform.Row3;
CameraNode.Position = new Vector3(row.X, row.Y, -row.Z);
```

Usamos `-row.Z` porque ARKit usa un sistema de coordenadas de la mano derecha.

### <a name="plane-detection"></a>Detección de plano

ARKit puede detectar planos horizontales y esta capacidad le permite interactuar con el mundo real, por ejemplo, podemos colocar el mutante en una tabla real o en una planta. La manera más sencilla de hacerlo es usar el método HitTest (raycasting). Convierte las coordenadas de pantalla (0.5; 0.5 es el centro) en las coordenadas del mundo real (0; 0; 0 es la ubicación del primer fotograma).

```chsarp
protected Vector3? HitTest(float screenX = 0.5f, float screenY = 0.5f)
{
    var result = ARSession.CurrentFrame.HitTest(new CGPoint(screenX, screenY),
        ARHitTestResultType.ExistingPlaneUsingExtent)?.FirstOrDefault();
    if (result != null)
    {
        var row = result.WorldTransform.Row3;
        return new Vector3(row.X, row.Y, -row.Z);
    }
    return null;
}
```

Ahora podemos colocar el mutante en una superficie horizontal en función de dónde se puntee en la pantalla del dispositivo:

```chsarp
void OnTouchEnd(TouchEndEventArgs e)
{
    float x = e.X / (float)Graphics.Width;
    float y = e.Y / (float)Graphics.Height;
    var pos = HitTest(x, y);
    if (pos != null)
    mutantNode.Position = pos.Value;
}
```

![Ilustración animada cambiar planos a medida que se mueve la vista](urhosharp-images/image4.gif)

### <a name="realistic-lighting"></a>Iluminación realista

En función de las condiciones de iluminación real, la escena virtual debe ser más clara o más oscura para adaptarse mejor a su entorno. ARFrame contiene una propiedad LightEstimate que se puede usar para ajustar la luz ambiente Urho; esto se hace así:

```csharp
var ambientIntensity = (float) frame.LightEstimate.AmbientIntensity / 1000f;
var zone = Scene.GetComponent<Zone>();
zone.AmbientColor = Color.White * ambientIntensity;
```

### <a name="beyond-ios---hololens"></a>Más allá de iOS-HoloLens

UrhoSharp [se ejecuta en todos los sistemas operativos principales](~/graphics-games/urhosharp/platform/index.md), por lo que puede volver a usar el código existente en otro lugar.

HoloLens es una de las plataformas más emocionantes en las que se ejecuta.   Esto significa que puede cambiar fácilmente entre iOS y HoloLens para compilar aplicaciones de realidad aumentada con UrhoSharp.

Puede encontrar el origen de MutantDemo en [github.com/EgorBo/ARKitXamarinDemo](https://github.com/EgorBo/ARKitXamarinDemo).

## <a name="related-links"></a>Vínculos relacionados

- [UrhoSharp](~/graphics-games/urhosharp/index.md)
- [ARKitXamarinDemo (con UrhoSharp) (ejemplo)](https://github.com/EgorBo/ARKitXamarinDemo)
