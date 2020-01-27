---
title: Cambios adicionales de los marcos de trabajo de iOS 10
description: En este documento se describen los cambios menores y las mejoras realizadas en los marcos de trabajo existentes en iOS 10 y se describe cómo hacer uso de estas actualizaciones en Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 4a6ec3c34afc0c017d5b37eec080f7f9bad08c0c
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725409"
---
# <a name="additional-ios-10-frameworks-changes"></a>Cambios adicionales de los marcos de trabajo de iOS 10

_En este artículo se describen cambios más pequeños o mejoras en los marcos de trabajo existentes para iOS 10._

## <a name="av-foundation-framework-additions"></a>Adiciones del marco AV Foundation

El marco de trabajo de AVFoundation incluye las siguientes mejoras:

- En iOS 10, el desarrollador ya no tiene que implementar distintos comportamientos de [AVPlayerItem](xref:AVFoundation.AVPlayerItem) basados en el tipo de contenido. Basta con establecer la propiedad `Rate` y AVFoundation determinará si hay suficiente contenido disponible para la reproducción sin detenerse.
- La nueva clase [AVCapturePhotoOutput](xref:AVFoundation.AVCaptureFileOutput) reemplaza a la clase de `AVCaptureStillImageOutput` en desuso y proporciona un método unificado para controlar todos los flujos de trabajo fotográficos al proporcionar un sofisticado control y supervisión del proceso de captura y compatibilidad con nuevas características, como las fotos en directo y el formato de captura sin formato.
- La nueva clase de `AVPlayerLooper` facilita el bucle de una parte determinada del medio durante la reproducción.
- La clase `AVAssetDownloadURLSession` permite la descarga y posterior reproducción de secuencias HLS cifradas FairPlay.
- De forma predeterminada, la clase [AVCaptureSession](xref:AVFoundation.AVCaptureSession) es compatible automáticamente con la captura de ancho ancho y ancho cuando el hardware del dispositivo lo admite. Consulte referencia de [compatibilidad de dispositivos iOS](https://developer.apple.com/library/prerelease/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013599) de Apple para obtener más detalles.

## <a name="avkit-additions"></a>AVKit Additions

El marco de trabajo de AVKit ahora incluye la nueva propiedad `UpdatesNowPlayingInfoCenter` para indicar cuándo se debe actualizar el centro de información en curso.

## <a name="core-data-enhancements"></a>Mejoras en los datos principales

iOS 10 incluye las siguientes mejoras en el marco de datos principal:

- Los objetos [NSManagedObjectContext](xref:CoreData.NSManagedObjectContext) con almacenes de datos de SQLite en el modo de diario de Wal admiten la nueva característica de generación de consultas, donde los contextos de objeto administrados (MOC) se pueden anclar a versiones de base de datos específicas para futuras capturas y transacciones con errores.
- Los objetos [NSManagedObjectContext](xref:CoreData.NSManagedObjectContext) raíz admiten errores y recuperaciones simultáneas sin serialización.
- La clase [NSPersistentStoreCoordinator](xref:CoreData.NSPersistentStoreCoordinator) mantiene un grupo de almacenes de datos de SQLite.
- Se han agregado varios métodos útiles nuevos a `NSManagedObject` que facilitan la realización de capturas y la creación de subclases.
- Usar el `NSPersistenceContainer` de alto nivel para hacer referencia a la `NSPersistentStoreCoordinator`, [NSManagedObjectModel](xref:CoreData.NSManagedObjectModel) y otros recursos de configuración de datos principales.

Para obtener más información, consulte la referencia de la [plataforma principal de datos](https://developer.apple.com/reference/coredata)de Apple.

## <a name="core-image-enhancements"></a>Mejoras en las imágenes principales

iOS 10 realiza las siguientes mejoras en el marco de imagen principal:

- Ahora, el desarrollador puede procesar imágenes en un espacio de colores fuera del espacio de colores de trabajo del contexto de la imagen principal convirtiendo dentro y fuera del espacio de colores antes y después del procesamiento.
- En el caso de los dispositivos iOS que usan las CPU A8 o A9, ahora se admite el formato de imagen sin procesar. La imagen principal ahora proporciona compatibilidad para descodificar imágenes sin procesar de la cámara iSight integrada o de una cámara de terceros. Use los métodos `FilterWithImageData` o `FilterWithImageURL` de la clase [CIFilter](xref:CoreImage.CIFilter) para procesar imágenes sin formato.
- Se han realizado varias mejoras de rendimiento de representación en `UIImage` representación (cuando están respaldadas por almacenes de imágenes de imagen principales) en `UIImageView` objetos.
- los objetos de `UIImage` con etiquetas anchas se representarán como colores de gama ancha en objetos de `UIImageView` en dispositivos iOS que admiten el color ancho.
- El código de kernel de imagen principal ahora puede solicitar formatos de salida de píxeles específicos.
- El método `ImageWithExtent` de la clase [CIFilter](xref:CoreImage.CIFilter) se puede usar para insertar el procesamiento personalizado en la operación de filtro. La imagen principal invocará la devolución de llamada especificada entre filtros al procesar una imagen para la salida o la presentación.

Además, se han agregado los siguientes nuevos filtros de imagen principal:

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

## <a name="core-motion-additions"></a>Adiciones de movimiento básico

Como novedad de iOS 10, el marco de trabajo básico de Motion incluye eventos pedometer que permiten a una aplicación recibir notificaciones rápidas y en tiempo real del usuario en pausa y reanudar el seguimiento mientras se ejecuta. Use [CMPedometer](xref:CoreMotion.CMPedometer) para registrarse para eventos pedometer de primer o segundo plano.

## <a name="foundation-enhancements"></a>Mejoras de Foundation

Se han realizado las siguientes mejoras en Foundation Framework para iOS 10:

- Use la nueva clase [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) para dar formato a las medidas localizadas que se van a mostrar al usuario final.
- Use la nueva clase [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) para realizar cálculos de intervalos de fecha y hora, como duraciones, para comparar intervalos y pruebas de intersecciones de intervalo.
- Use la nueva clase [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) para realizar la conversión entre las diferentes unidades de medida (unidad de medida) o realice cálculos en los valores de UOMs diferentes.

- Use las nuevas clases [NSUnit](https://developer.apple.com/reference/foundation/nsunit) y [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) para representar UOMs específicas.
- Se han agregado varias propiedades nuevas a la clase [NSLocal](https://developer.apple.com/reference/foundation/nslocale) para adquirir información local y los formatos de presentación disponibles.

## <a name="gamekit-enhancements"></a>Mejoras de GameKit

Se han realizado las siguientes mejoras en el marco de trabajo de GameKit en iOS 10:

- La **aplicación Game Center** está en desuso y se ha quitado de iOS. Si la aplicación usa GameKit, _debe_ presentar su propia interfaz para mostrar las características de GameKit, como los marcadores, etc.
- La clase [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) ha implementado un nuevo tipo de cuenta solo iCloud.
- La nueva clase [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) proporciona una solución generalizada para administrar el almacenamiento de datos persistentes en Game Center. `GKGameSession` mantiene una lista de reproductores y la aplicación es responsable de implementar cómo y cuándo se almacena, recupera o intercambia la fecha del participante entre jugadores. En muchas instancias, las sesiones de juego pueden reemplazar las coincidencias existentes basadas en turnos, las coincidencias en tiempo real o los métodos de guardado de juegos persistentes.

## <a name="gameplaykit-enhancements"></a>Mejoras de GameplayKit

Se han realizado las siguientes mejoras en el marco de trabajo de GameplayKit en iOS 10:

- Use la nueva clase [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) para proporcionar rutas de acceso natural y de alto rendimiento.
- Se ha agregado la generación de ruido de procedimiento y se puede usar para mejorar el realismo en las texturas de aspecto natural, agregar realismo a los movimientos de la cámara y ayudar a generar sofisticados mundos de juego.
- Use la creación de particiones espaciales para particionar los datos del mundo de juego para una búsqueda eficaz.
- Se ha agregado un nuevo estratega de Monte Carlo ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) para realizar cálculos de movimiento exhaustivos.
- se ha agregado compatibilidad con 3D a los comportamientos existentes de agente y ruta de acceso mediante las nuevas clases [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) y [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) .
- Las nuevas clases [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) y [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) hacen que la combinación de GameplayKit y SpriteKit sea más fácil que nunca.
- Se ha agregado una nueva API de árbol de decisión ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) y [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) para mejorar la generación de juegos de inteligencia artificial.

## <a name="healthkit-enhancements"></a>Mejoras de HealthKit

Se han realizado las siguientes mejoras en el marco de trabajo de HealthKit en iOS 10:

- Se han agregado nuevas claves de metadatos para los tipos meteorológicos (como `HKWeatherConditionClear` y `HKWeatherConditionCloudy`) y los tipos de entrenamiento (como `HKWorkoutActivityTypeFlexibility` y `HKWorkoutActivityTypeWheelchairRunPace`).
- La nueva clase de `HKCDADocument` se ha agregado para representar un documento con formato de arquitectura de documentos clínicos (CDA).
- Use la nueva clase [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) para especificar el `ActivityType` y `LocationType` de un entrenamiento.
- Se han agregado los nuevos [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) y el método `WheelchairUse` de la clase [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) para trabajar con los datos de mantenimiento relacionados con la silla.

## <a name="homekit-enhancements"></a>Mejoras de HomeKit

Se han realizado las siguientes mejoras en el marco de trabajo de HomeKit en iOS 10:

- Se han agregado nuevos servicios y características.
- Un iPad puede configurarse para que actúe como un concentrador de HomeKit para proporcionar acceso de accesorio remoto, ejecutar desencadenadores de automatización y habilitar permisos de usuario compartidos.
- Se ha agregado compatibilidad con los accesorios de cámara y doorbell.
- Se han proporcionado más contextos y configuraciones para los accesorios.

Consulte nuestra [Introducción a](~/ios/platform/homekit.md) la documentación de HomeKit para obtener más información.

## <a name="metal-enhancements"></a>Mejoras de metal

Se han realizado las siguientes mejoras en el marco de metal en iOS 10:

- las aplicaciones 3D y los juegos ahora pueden usar _teselación_ para representar eficazmente escenas complejas y geometría a través de la GPU.
- Proporcionar un control específico de la asignación de recursos para optimizar el rendimiento de las aplicaciones basadas en metal mediante montones de recursos y destinos de representación de memoria.
- Utilice la especialización de función para crear una colección altamente optimizada de funciones de combinación de materiales y claros para una escena.

Para obtener más información, consulte la [Guía de programación de metal](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)de Apple.

## <a name="modelio-enhancements"></a>Mejoras de ModelIO

Se han realizado las siguientes mejoras en el marco de trabajo de ModelIO en iOS 10:

- Ahora se admite el formato de archivo USD.
- Se ha agregado compatibilidad con el campo de distancia con signo a la clase [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) .
- Use la nueva clase de `MDLLightProbeIrradianceDataSource` para ayudar en la colocación de sondeos ligeros.
- Use la nueva clase de `MDLMaterialPropertyGraph` para admitir fácilmente los cambios en tiempo de ejecución de los modelos.

## <a name="photos-enhancements"></a>Mejoras en las fotos

Se han realizado las siguientes mejoras en el marco photos en iOS 10:

- Use las clases [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) y [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) para aprovechar las ventajas de la nueva característica procesador de imagen principal para realizar modificaciones.
- Live Photo Editing ahora está disponible para las aplicaciones que admiten el marco de fotografías y para las extensiones de edición de fotografías (para su uso en las fotos y las aplicaciones de cámara).
- Use la nueva clase [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) para aplicar ediciones al vídeo y al contenido de las fotos en directo.

## <a name="replaykit-enhancements"></a>Mejoras de ReplayKit

Se han realizado las siguientes mejoras en el marco de trabajo de ReplayKit en iOS 10:

- Use las clases [RPScreenRecorder](https://developer.apple.com/reference/replaykit/rpscreenrecorder), [RPBroadcastActivityViewController](https://developer.apple.com/reference/replaykit/rpbroadcastactivityviewcontroller) y [RPBroadcastController](https://developer.apple.com/reference/replaykit/rpbroadcastcontroller) para admitir la difusión de medios grabados a través de sitios de terceros.
- La interfaz de usuario de difusión y las extensiones de Broadcast Upload son necesarias para admitir servicios de difusión de terceros de ReplayKit en la aplicación.

## <a name="scenekit-enhancements"></a>Mejoras de SceneKit

Se han realizado las siguientes mejoras en el marco de trabajo de SceneKit en iOS 10:

- La clase [SCNCamera](xref:SceneKit.SCNCamera) puede proporcionar mayor realismo mediante el uso de las características y los efectos de HDR. Use la exposición adaptable para crear efectos automáticos o usar viñetas, los halos de color y la clasificación de color para agregar efectos de fillmatic al juego.
- SceneKit Now incluye un nuevo sistema de representación en cinta (PBR) para obtener resultados más realistas con la creación de recursos más sencilla.
- Use el nuevo modelo de sombreado de [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) para producir una amplia gama de efectos de sombreado realistas, al tiempo que solo requiere tres propiedades fundamentales (`Diffuse`, `Metalness` y `Roughness`).
- Puesto que el sombreado de PBR funciona mejor con la iluminación basada en el entorno, use la propiedad `LightingEnvironment` para asignar la iluminación basada en imagen a toda la escena.
- Use la propiedad `IESProfileURL` para importar los accesorios de luz del mundo real que definen la iluminación en función de los valores reales, como la intensidad (en lúmenes) y la temperatura del color (en grados Kelvin).
- Las características de la cámara de PBR y HDR proporcionan mejores resultados que las técnicas de representación tradicionales y, como resultado, SceneKit ahora realiza todos los cálculos de color en un espacio de colores lineal (mediante la gama de colores P3 en pantallas de dispositivos anchos).
- El color SceneKit Now coincide con todos los colores mediante la lectura de la información del perfil de color.
- SceneKit interpreta los valores de los componentes de color en un espacio de colores RGB lineal para todos los tipos de sombreador.
- Tanto la representación del espacio de colores lineal como el color ancho pueden deshabilitarse mediante la especificación de las claves `SCNDisableLinearSpaceRendering` y `SCNDisableWideGamut` en la `Info.plist`de la aplicación.
- Cree primates polígono arbitrario (se carga desde archivos o se genera mediante programación) para especificar la geometría con la nueva clase [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/documentation/scenekit/scngeometryprimitivetype/scngeometryprimitivetypepolygon) .
- Dado que SceneKit Lee y ajusta la información de Perfil de color en imágenes de textura, use catálogos de recursos para todas las imágenes para asegurarse de que se proporciona esta información.

## <a name="spritekit-enhancements"></a>Mejoras de SpriteKit

Se han realizado las siguientes mejoras en el marco de trabajo de SpriteKit en iOS 10:

- Los sombreadores personalizados pueden proporcionar atributos (`SKAttribute`) que se pueden configurar por separado en cada nodo que use el sombreador proporcionando un valor de atributo (`SKAttributeValue`).
- Ahora, Tilemaps admite formas de mosaicos cuadradas, hexagonales y isométricos para juegos en 2D, 2,5 D y de desplazamiento lateral mediante las clases `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` y `SKTileSet`.
- Use la nueva clase de `SKWarpGeometry` para expandir o distorsionar la representación de [SKSpriteNode](xref:SpriteKit.SKSpriteNode) o [SKEffectNode](xref:SpriteKit.SKEffectNode) . La nueva clase [SKAction](xref:SpriteKit.SKAction) se puede usar para animar las transiciones entre los efectos de alabeo.
- La clase [SKView](xref:SpriteKit.SKView) proporciona varios métodos nuevos para proporcionar un control exhaustivo sobre cuándo y cómo se representa una escena.

## <a name="scrollview-enhancements"></a>Mejoras de ScrollView

Se han realizado las siguientes mejoras en el control ScrollView en iOS 10,3:

- `UIScrollView` ahora incluya la propiedad `IndexDisplayMode` para controlar cómo se muestra el índice mientras el usuario se desplaza como `UIScrollViewIndexDisplayMode` de:
  - `Automatic`: el sistema operativo controla la presentación del índice.
  - `AlwaysHidden`: la presentación del índice siempre está oculta.

Vea el [ejemplo de iOSTenThree](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-iostenthree) para obtener más uso.

## <a name="uikit-enhancements"></a>Mejoras de UIKit

Se han realizado las siguientes mejoras en el marco de trabajo de UIKit en iOS 10:

- La nueva API de [UIPasteboard](xref:UIKit.UIPasteboard) proporciona nuevas opciones (como las limitaciones de duración) y declarará automáticamente los tipos de contenido compatibles para los tipos de clase comunes.
- Se ha agregado una nueva compatibilidad de animación interrumpida, basada en objetos y totalmente interactiva y se puede vincular a movimientos. Consulte referencia del [Protocolo UIViewAnimating](https://developer.apple.com/reference/uikit/uiviewanimating)de Apple, referencia de [clase UIViewPropertyAnimator](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), referencia del [Protocolo UITimingCurveProvider](https://developer.apple.com/reference/uikit/uitimingcurveprovider), referencia de [clase UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) y [referencia de clase UISpringTimingParameter](https://developer.apple.com/reference/uikit/uispringtimingparameters) para obtener más información.
- Las nuevas `UIPreviewInteraction` y `UIPreviewInteractionDelegate` permiten que la aplicación de Desarrollador proporcione una interfaz personalizada para las operaciones de lectura y de extracción.
- La nueva clase de `UIAccessibilityCustomRotor` permite a la aplicación proporcionar una funcionalidad personalizada específica del contexto a tecnologías de ayuda como VoiceOver.
- Use los símbolos `UIAccessibilityIsAssistiveTouchRunning` y `UIAccessibilityAssistiveTouchStatusDidChangeNotification` para determinar si AssistiveTouch está habilitado.
- Use los símbolos `UIAccessibilityHearingDevicePairedEar` y `UIAccessibilityHearingDevicePairedEarDidChangeNotification` para obtener el estado de cualquier ayuda de audición accesorios MFI emparejada.
- Para admitir el tipo dinámico en etiquetas, los campos de texto y los cuadros de texto utilizan el nuevo método `PreferredFontForTextStyle` de la clase `UIFont`.
- Para decidir si un elemento debe actualizar su fuente cuando cambie el `UIContentSizeCategory` del dispositivo, use la propiedad `AdjustsFontForContentSizeCategory` del delegado `UIContentSizeCategoryAdjusting`.
- Se llama de forma asincrónica al método `OpenURL` de la clase `UIApplication` y ahora es compatible con un controlador de finalización al que se llama después de que se haya completado la acción de apertura.
- Inicie el uso compartido de CloudKit y modifique sus propiedades con las nuevas clases `UICloudSharingController` y `UICloudSharingControllerDelegate`.
- Aproveche las ventajas de las celdas capturadas previamente para mejorar la experiencia de desplazamiento de `UICollectionViews` con el nuevo delegado de `UICollectionViewDataSourcePrefetching`.
- Ahora, el desarrollador puede controlar la apariencia del distintivo de los elementos de la barra de pestañas (por ejemplo, el texto y el color de fondo).
- Ahora se admite el control de actualización en todas las subclases de vista de desplazamiento y de vista de desplazamiento (como `UICollectionView`).

## <a name="webkit-enhancements"></a>Mejoras de WebKit

Se han realizado las siguientes mejoras en el marco de WebKit en iOS 10:

- Se ha agregado compatibilidad con PEEK y pop a la clase `WKWebView`. Use el método `ShouldPreviewElement` para determinar si una vista Web determinada debe mostrar una vista previa.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [Novedades de iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
