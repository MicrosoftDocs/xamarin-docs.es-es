---
title: Cambios adicionales de tvOS 10 frameworks
description: En este documento se describen los cambios menores y las mejoras realizadas en los marcos de trabajo existentes en iOS 10. Describe las actualizaciones de AVFoundation, AVKit, Core Data, Core Graphics, Foundation, GameKit, GameplayKit, etc.
ms.prod: xamarin
ms.assetid: F771640A-F92E-4954-82D5-2D720434971E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 4eb81a074adb6d2b828bb74edfde9916d69960b1
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434965"
---
# <a name="additional-tvos-10-frameworks-changes"></a>Cambios adicionales de tvOS 10 frameworks

Además de los principales cambios en tvOS, Apple ha realizado modificaciones y mejoras en varios marcos de trabajo existentes en tvOS 10.

<a name="AV-Foundation-Framework"></a>

## <a name="avfoundation-framework-additions"></a>Adiciones de AVFoundation Framework

El marco de trabajo de AVFoundation incluye las siguientes mejoras:

- En tvOS 10, la aplicación ya no implementa distintos comportamientos de [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) en función del tipo de contenido. Basta con establecer la `Rate` propiedad y AVFoundation determinará si hay suficiente contenido disponible para la reproducción sin detenerse.
- La nueva `AVPlayerLooper` clase facilita el bucle de una parte determinada del medio durante la reproducción.

<a name="AVKit-Framework-Enhancements"></a>

## <a name="avkit-framework-enhancements"></a>Mejoras de AVKit Framework

El marco de trabajo de AVKit incluye las siguientes mejoras:

- La aplicación ahora tiene control sobre el comportamiento de omisión de [AVPlayerViewController](https://developer.apple.com/reference/avkit/avplayerviewcontroller) , por lo que un gesto de omisión podría desplazarse al siguiente elemento de la lista de reproducción o avanzar dentro del elemento actual.

<a name="Core-Data-Enhancements"></a>

## <a name="core-data-enhancements"></a>Mejoras en los datos principales

tvOS 10 incluye las siguientes mejoras en el marco de datos principal:

- Los objetos [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) raíz admiten errores y recuperaciones simultáneas sin serialización.
- La clase [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) mantiene un grupo de almacenes de datos de SQLite.
- Los objetos [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) con almacenes de datos de SQLite en el modo de diario de Wal admiten la nueva característica de generación de consultas, donde los contextos de objeto administrados (MOC) se pueden anclar a versiones de base de datos específicas para futuras capturas y transacciones con errores.
- Usar el nivel alto `NSPersistenceContainer` para hacer referencia a `NSPersistentStoreCoordinator` , [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) y otros recursos de configuración de datos principales.
- Se han agregado varios métodos de conveniencia para facilitar la `NSManagedObject` realización de capturas y la creación de subclases.

Para obtener más información, consulte la referencia de la [plataforma principal de datos](https://developer.apple.com/reference/coredata)de Apple.

<a name="Core-Graphics-Enhancements"></a>

## <a name="core-graphics-enhancements"></a>Mejoras en los gráficos principales

tvOS 10 incluye las siguientes mejoras en el marco de trabajo de gráficos básico:

- La nueva clase CGColorConverterRef se puede usar para realizar una serie de conversiones de color.

<a name="Core-Image-Enhancements"></a>

## <a name="core-image-enhancements"></a>Mejoras en las imágenes principales

tvOS 10 realiza las siguientes mejoras en el marco de imagen principal:

- El `ImageWithExtent` método de la clase [CIFilter](https://developer.apple.com/reference/coreimage/cifilter) se puede usar para insertar el procesamiento personalizado en la operación de filtro. La imagen principal invocará la devolución de llamada especificada entre filtros al procesar una imagen para la salida o la presentación.
- Ahora, la aplicación puede procesar imágenes en un espacio de colores fuera del espacio de colores de trabajo del contexto de la imagen principal convirtiendo dentro y fuera del espacio de colores antes y después del procesamiento.
- Se han realizado varias mejoras de rendimiento en la representación `UIImage` (cuando están respaldadas por los almacenes de imágenes de imagen principales) en los `UIImageView` objetos.
- `UIImage` los objetos con etiquetas anchas se representarán como un color de gama ancha en los `UIImageView` objetos de dispositivos iOS que admiten el color ancho.
- El código de kernel de imagen principal ahora puede solicitar formatos de salida de píxeles específicos.

Además, se han agregado los siguientes nuevos filtros de imagen principal:

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

<a name="Foundation-Enhancements"></a>

## <a name="foundation-enhancements"></a>Mejoras de Foundation

Se han realizado las siguientes mejoras en Foundation Framework para tvOS 10:

- Use la nueva clase [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) para realizar cálculos de intervalos de fecha y hora, como duraciones, para comparar intervalos y pruebas de intersecciones de intervalo.
- Se han agregado varias propiedades nuevas a la clase [NSLocal](https://developer.apple.com/reference/foundation/nslocale) para adquirir información local y los formatos de presentación disponibles.
- Use la nueva clase [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) para realizar la conversión entre las diferentes unidades de medida (unidad de medida) o realice cálculos en los valores de UOMs diferentes.
- Use la nueva clase [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) para dar formato a las medidas localizadas que se van a mostrar al usuario final.
- Use las nuevas clases [NSUnit](https://developer.apple.com/reference/foundation/nsunit) y [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) para representar UOMs específicas.

<a name="GameKit-Enhancements"></a>

## <a name="gamekit-enhancements"></a>Mejoras de GameKit

Se han realizado las siguientes mejoras en el marco de trabajo de GameKit en tvOS 10:

- La clase [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) ha implementado un nuevo tipo de cuenta solo iCloud.
- La nueva clase [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) proporciona una solución generalizada para administrar el almacenamiento de datos persistentes en Game Center. `GKGameSession` mantiene una lista de reproductores y la aplicación es responsable de la implementación de la forma y el momento en que se almacena, recupera o intercambia la fecha del participante entre los reproductores. En muchas instancias, las sesiones de juego pueden reemplazar las coincidencias existentes basadas en turnos, las coincidencias en tiempo real o los métodos de guardado de juegos persistentes.

<a name="GameplayKit-Enhancements"></a>

## <a name="gameplaykit-enhancements"></a>Mejoras de GameplayKit

Se han realizado las siguientes mejoras en el marco de trabajo de GameplayKit en tvOS 10:

- Se ha agregado la generación de ruido de procedimiento y se puede usar para mejorar el realismo en las texturas de aspecto natural, agregar realismo a los movimientos de la cámara y ayudar a generar sofisticados mundos de juego.
- Use la creación de particiones espaciales para particionar los datos del mundo de juego para una búsqueda eficaz.
- Se ha agregado un nuevo estratega de Monte Carlo ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) para realizar cálculos de movimiento exhaustivos.
- Se ha agregado una nueva API de árbol de decisión ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) y [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) para mejorar la generación de juegos de inteligencia artificial.
- se ha agregado compatibilidad con 3D a los comportamientos existentes de agente y ruta de acceso mediante las nuevas clases [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) y [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) .
- Use la nueva clase [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) para proporcionar rutas de acceso natural y de alto rendimiento.
- Las nuevas clases [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) y [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) hacen que la combinación de GameplayKit y SpriteKit sea más fácil que nunca.

<a name="Metal-Enhancements"></a>

## <a name="metal-enhancements"></a>Mejoras de metal

Se han realizado las siguientes mejoras en el marco de metal en tvOS 10:

- las aplicaciones 3D y los juegos ahora pueden usar _teselación_ para representar eficazmente escenas complejas y geometría a través de la GPU.
- Utilice la especialización de función para crear una colección altamente optimizada de funciones de combinación de materiales y claros para una escena.
- Proporcionar un control específico de la asignación de recursos para optimizar el rendimiento de las aplicaciones basadas en metal mediante montones de recursos y destinos de representación de memoria.

Para obtener más información, consulte la [Guía de programación de metal](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)de Apple.

<a name="Metal-Performance-Shaders-Enhancements"></a>

## <a name="metal-performance-shaders-enhancements"></a>Mejoras en los sombreadores de rendimiento de metal

Se han realizado las siguientes mejoras en el marco de sombreadores de rendimiento de metal en tvOS 10:

- Se han agregado muchos kernels nuevos al marco de sombreadores de rendimiento de metal para permitir que la aplicación aproveche los cálculos de alta optimización en paralelo de datos, como las conversiones de espacio de color y las operaciones de red neuronal.

<a name="ModelIO-Enhancements"></a>

## <a name="modelio-enhancements"></a>Mejoras de ModelIO

Se han realizado las siguientes mejoras en el marco de trabajo de ModelIO en tvOS 10:

- Ahora se admite el formato de archivo USD.
- Use la nueva `MDLMaterialPropertyGraph` clase para admitir fácilmente los cambios de tiempo de ejecución en los modelos.
- Se ha agregado compatibilidad con el campo de distancia con signo a la clase [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) .
- Use la nueva `MDLLightProbeIrradianceDataSource` clase para ayudar en la colocación de sondeos ligeros.

<a name="SceneKit-Enhancements"></a>

## <a name="scenekit-enhancements"></a>Mejoras de SceneKit

Se han realizado las siguientes mejoras en el marco de trabajo de SceneKit en tvOS 10:

- SceneKit Now incluye un nuevo sistema de representación en cinta (PBR) para obtener resultados más realistas con la creación de recursos más sencilla.
- Use el nuevo modelo de sombreado de [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) para producir una amplia gama de efectos de sombreado realistas, al tiempo que solo requiere tres propiedades fundamentales ( `Diffuse` , `Metalness` y `Roughness` ).
- Puesto que el sombreado de PBR funciona mejor con la iluminación basada en el entorno, use la `LightingEnvironment` propiedad para asignar la iluminación basada en imagen a tan solo una escena entera.
- Utilice la `IESProfileURL` propiedad para importar los accesorios de luz del mundo real que definen la base de iluminación en valores reales como la intensidad (en lúmenes) y la temperatura de color (en grados Kelvin).
- La clase [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera) puede proporcionar mayor realismo mediante el uso de las características y los efectos de HDR. Use la exposición adaptable para crear efectos automáticos o usar viñetas, los halos de color y la clasificación de color para agregar efectos de filmatic al juego.
- Las características de la cámara de PBR y HDR proporcionan mejores resultados que las técnicas de representación tradicionales y, como resultado, SceneKit ahora realiza todos los cálculos de color en un espacio de colores lineal (mediante la gama de colores P3 en pantallas de dispositivos anchos).
- El color SceneKit Now coincide con todos los colores mediante la lectura de la información del perfil de color.
- SceneKit interpreta los valores de los componentes de color en un espacio de colores RGB lineal para todos los tipos de sombreador.
- Dado que SceneKit Lee y ajusta la información de Perfil de color en imágenes de textura, use catálogos de recursos para todas las imágenes para asegurarse de que se proporciona esta información.
- Tanto la representación del espacio de colores lineal como el color ancho pueden deshabilitarse mediante la especificación de las `SCNDisableLinearSpaceRendering` `SCNDisableWideGamut` claves y en la de la aplicación `Info.plist` .
- Cree primates polígono arbitrario (se carga desde archivos o se genera mediante programación) para especificar la geometría con la nueva clase [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/documentation/scenekit/scngeometryprimitivetype/scngeometryprimitivetypepolygon) .

<a name="SpriteKit-Enhancements"></a>

## <a name="spritekit-enhancements"></a>Mejoras de SpriteKit

Se han realizado las siguientes mejoras en el marco de trabajo de SpriteKit en tvOS 10:

- Ahora, Tilemaps admite formas de mosaico cuadrada, hexagonal y isométrica para juegos de 2D, 2,5 d y de desplazamiento lateral con `SKTileMapMode` las `SKTileGroup` `SKTileGroupRule` clases, y `SKTileSet` .
- Use la nueva `SKWarpGeometry` clase para expandir o distorsionar la representación de [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) o [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) . La nueva clase [SKAction](https://developer.apple.com/reference/spritekit/skaction) se puede usar para animar las transiciones entre los efectos de alabeo.
- Los sombreadores personalizados pueden proporcionar atributos ( `SKAttribute` ) que se pueden configurar por separado en cada nodo que use el sombreador proporcionando un valor de atributo ( `SKAttributeValue` ).
- La clase [SKView](https://developer.apple.com/reference/spritekit/skview) proporciona varios métodos nuevos para proporcionar un control exhaustivo sobre cuándo y cómo se representa una escena.

<a name="UIKit-Enhancements"></a>

## <a name="uikit-enhancements"></a>Mejoras de UIKit

Se han realizado las siguientes mejoras en el marco de trabajo de UIKit en tvOS 10:

- La API de enfoque se ha mejorado para admitir el foco de elemento que no es de vista, además de `UIViews` . Los elementos que admiten el foco _deben_ implementar la `IUIFocusItem` interfaz.
- La nueva `UIGraphicsRender` clase proporciona un método orientado a objetos para crear mapas de bits o archivos PDF a partir de representación UIKit o gráficos principales y reemplaza el método desusado `UIGraphicsBeginImageContext` .
- La `UIUserInterfaceStyle` clase se ha agregado para determinar qué tema de la interfaz de usuario (oscuro o claro) está activo actualmente.
- Se ha agregado una nueva compatibilidad de animación interrumpida, basada en objetos y totalmente interactiva y se ha vinculado a los gestos. Agradables consulte referencia del [Protocolo UIViewAnimating](https://developer.apple.com/reference/uikit/uiviewanimating)de Apple, referencia de [clase de UIViewPropertyAnimator](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), referencia del [Protocolo UITimingCurveProvider](https://developer.apple.com/reference/uikit/uitimingcurveprovider), referencia de [clase UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) y [referencia de clase de UISpringTimingParameter](https://developer.apple.com/reference/uikit/uispringtimingparameters) para obtener más información.
- El nuevo `UIPreviewInteraction` y `UIPreviewInteractionDelegate` permitir que la aplicación proporcione una interfaz personalizada para las operaciones de lectura y de extracción.
- La nueva `UIAccessibilityCustomRotor` clase permite a la aplicación proporcionar una funcionalidad personalizada específica del contexto a las tecnologías de asistencia, como la voz sobre.
- Use los `UIAccessibilityIsAssistiveTouchRunning` `UIAccessibilityAssistiveTouchStatusDidChangeNotification` símbolos y para determinar si AssistiveTouch está habilitado.
- Use los `UIAccessibilityHearingDevicePairedEar` `UIAccessibilityHearingDevicePairedEarDidChangeNotification` símbolos y para obtener el estado de cualquier ayuda de audición accesorios MFI emparejada.
- La nueva API de [UIPasteboard](https://developer.apple.com/reference/uikit/uipasteboard) proporciona nuevas opciones (como las limitaciones de duración) y declarará automáticamente los tipos de contenido compatibles para los tipos de clase comunes.
- Para admitir el tipo dinámico en etiquetas, los campos de texto y los cuadros de texto utilizan el nuevo `PreferredFontForTextStyle` método de la `UIFont` clase.
- Para decidir si un elemento debe actualizar su fuente cuando `UIContentSizeCategory` cambien los dispositivos, use `AdjustsFontForContentSizeCategory` la propiedad del `UIContentSizeCategoryAdjusting` delegado.
- La aplicación ahora puede controlar la apariencia del distintivo de los elementos de la barra de pestañas, como el color de texto y de fondo.
- Ahora se admite el control de actualización en todas las subclases vista de desplazamiento y vista de desplazamiento (como `UICollectionView` ).
- `OpenURL`Ahora se llama al método de la `UIApplication` clase de forma asincrónica y admite un controlador de finalización al que se llama después de que se haya completado la operación de apertura.
- Inicie el uso compartido de CloudKit y modifique sus propiedades con las nuevas `UICloudSharingController` `UICloudSharingControllerDelegate` clases y.
- Aproveche las ventajas de las celdas capturadas previamente para mejorar la experiencia de desplazamiento de `UICollectionViews` con el nuevo `UICollectionViewDataSourcePrefetching` delegado.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [Novedades de tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)