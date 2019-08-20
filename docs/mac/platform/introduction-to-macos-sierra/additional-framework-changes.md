---
title: Cambios adicionales en el marco de macOS Sierra
description: En este documento se describen los cambios y las mejoras menores de los marcos de trabajo existentes introducidos en macOS Sierra. Examina los cambios en el marco acelerado, AppKit, AVFoundation, Core Data, Core Image, Foundation, etc.
ms.prod: xamarin
ms.assetid: CA701269-D11E-4DE3-89C1-58EF8993A482
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: b382b7dd66722df6acb4bb7c90e401e5895c0947
ms.sourcegitcommit: 0df727caf941f1fa0aca680ec871bfe7a9089e7c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2019
ms.locfileid: "69620904"
---
# <a name="additional-macos-sierra-framework-changes"></a>Cambios adicionales en el marco de macOS Sierra

<a name="Accelerate-Framework-Enhancements" />

## <a name="accelerate-framework-enhancements"></a>Acelere las mejoras del marco de trabajo

Se ha realizado la siguiente mejora en el marco de trabajo acelerado para macOS Sierra:

- Se ha agregado Quadrature (cálculo integral).
- Se han agregado funciones básicas para construir redes neuronal.
- Se han agregado funciones de predicado geométrico para probar aspectos como la intersección de dos objetos geométricos.

<a name="AppKit-Framework-Enhancements" />

## <a name="appkit-framework-enhancements"></a>Mejoras de AppKit Framework

Se ha realizado la siguiente mejora en el marco de trabajo de AppKit para macOS Sierra:

- Varias mejoras en `NSCollectionView` , como:
  - **Secciones contraíbles** : permite al usuario contraer una sección de vista de colección en una sola fila horizontal.
  - Los **encabezados flotantes** : los encabezados y pies de página ahora se pueden flotar (en un diseño de flujo) con la misma API que [UICollectionView](https://developer.apple.com/reference/uikit/uicollectionview) en iOS.
  - **Vistas de fondo** desplazables: ahora se puede establecer un fondo de vistas de colección para desplazarse junto con el contenido.
- El paso de diseño de vista diferida se ha optimizado y ampliado.
- La API de arrastrar y colocar ahora incluye las nuevas `NSFilePromiseProvider` clases y `NSFilePromiseReceiver` para admitir la rebaño de arrastre.
- Se han agregado varios constructores de conveniencia a los controles existentes:
  - `NSButton`incluye nuevos constructores para crear botones de reenvío, casillas y botones de radio.
  - `NSTextField`incluye nuevos constructores para crear etiquetas de ajuste y sin ajuste, etiquetas con atributos y campos de texto editables.
  - `NSSegmentedControl`incluye nuevos constructores para crear controles segmentados a partir de un grupo de etiquetas o imágenes.
  - `NSSlider`incluye nuevos constructores para crear controles deslizantes lineales horizontales.
  - `NSImageView`incluye nuevos constructores para crear vistas de imagen no editables a partir `NSImage`de un determinado.
- El nuevo `NSGridView` elemento se ha agregado a la disposición automática de una colección de subvistas en una cuadrícula con filas y columnas de tamaño variable que se pueden ocultar o mostrar dinámicamente.

<a name="AVFoundation-Framework-Enhancements" />

## <a name="avfoundation-framework-enhancements"></a>Mejoras de AVFoundation Framework

Se ha realizado la siguiente mejora en el marco de trabajo de AVFoundation para macOS Sierra:

- En macOS, la aplicación ya no tiene que implementar distintos comportamientos de [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) basados en el tipo de contenido. Basta con establecer `Rate` la propiedad y AVFoundation determinará si hay suficiente contenido disponible para la reproducción sin detenerse.
- La nueva `AVPlayerLooper` clase facilita el bucle de una parte determinada del medio durante la reproducción.
- La `AVAssetDownloadURLSession` clase permite la descarga y posterior reproducción de secuencias HLS cifradas Fairplay.

<a name="Core-Data-Framework-Enhancements" />

## <a name="core-data-framework-enhancements"></a>Mejoras en el marco de datos principal

Se ha realizado la siguiente mejora en el marco de datos principal de macOS Sierra:

- Los objetos [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) raíz admiten errores y recuperaciones simultáneas sin serialización.
- La clase [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) mantiene un grupo de almacenes de datos de SQLite.
- Los objetos [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) con almacenes de datos de SQLite en el modo de diario de Wal admiten la nueva característica de generación de consultas, donde los contextos de objeto administrados (MOC) se pueden anclar a versiones de base de datos específicas para futuras capturas y transacciones con errores.
- Usar el nivel `NSPersistenceContainer` alto para `NSPersistentStoreCoordinator`hacer referencia a, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) y otros recursos de configuración de datos principales.
- Se han agregado varios métodos de conveniencia para `NSManagedObject` facilitar la realización de capturas y la creación de subclases.

Para obtener más información, consulte la referencia de la [plataforma principal de datos](https://developer.apple.com/reference/coredata)de Apple.

<a name="Core-Image-Framework-Enhancements" />

## <a name="core-image-framework-enhancements"></a>Mejoras de Core Image Framework

Se ha realizado la siguiente mejora en el marco de imagen principal para macOS Sierra:

- El `ImageWithExtent` método de la clase [CIFilter](https://developer.apple.com/reference/coreimage/cifilter) se puede usar para insertar el procesamiento personalizado en la operación de filtro. La imagen principal invocará la devolución de llamada especificada entre filtros al procesar una imagen para la salida o la presentación.
- Ahora, la aplicación puede procesar imágenes en un espacio de colores fuera del espacio de colores de trabajo del contexto de la imagen principal convirtiendo dentro y fuera del espacio de colores antes y después del procesamiento.
- Ahora, el kernel de imagen principal puede solicitar un formato de salida de píxel específico.
- Se han agregado los siguientes filtros de imagen nuevos `CINinePartTitled`: `CINinePartStretched`, `CIHueSaturationValueGradient`, `CIEdgePreserveUpsampleFilter` y `CIClamp`.

<a name="Foundation-Framework-Enhancements" />

## <a name="foundation-framework-enhancements"></a>Mejoras de Foundation Framework

Se ha realizado la siguiente mejora en Foundation Framework para macOS Sierra:

- Use la API de [NSDimentions](https://developer.apple.com/reference/foundation/nsdimension) para representar, convertir y mostrar muchas de las unidades físicas más comunes, como la masa, la longitud, la velocidad, la duración y la temperatura.
- Use la clase [NSISO8601DateFormatter](https://developer.apple.com/reference/foundation/nsiso8601dateformatter) para analizar y generar fechas con formato ISO 8601.
- Use la nueva clase [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) para realizar cálculos de intervalos de fecha y hora, como duraciones, para comparar intervalos y pruebas de intersecciones de intervalo.
- Use la clase [NSPersonNameComponentsFormatter](https://developer.apple.com/reference/foundation/nspersonnamecomponentsformatter) para analizar los elementos del nombre de una persona a partir de una cadena.
- Use la nueva clase [NSURLSessionTaskMetrics](https://developer.apple.com/reference/foundation/nsurlsessiontaskmetrics) para obtener las métricas de una sesión de red de direcciones URL.

Para obtener más información, consulte las notas de la versión de Apple [Foundation para OS X v 10.12 y iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/RN-Foundation-OSX10.12/index.html).

<a name="GameKit-Framework-Enhancements" />

## <a name="gamekit-framework-enhancements"></a>Mejoras de GameKit Framework

Se ha realizado la siguiente mejora en el marco de trabajo de GameKit para macOS Sierra:

- La **aplicación Game Center** ha quedado en desuso y se ha quitado de MacOS. Si la aplicación usa GameKit, _debe_ presentar su propia interfaz para mostrar las características de GameKit, como los marcadores, etc. 
- La clase [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) ha implementado un nuevo tipo de cuenta solo iCloud.
- La nueva clase [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) proporciona una solución generalizada para administrar el almacenamiento de datos persistentes en Game Center. `GKGameSession`mantiene una lista de reproductores y la aplicación es responsable de la implementación de la forma y el momento en que se almacena, recupera o intercambia la fecha del participante entre los reproductores. En muchas instancias, las sesiones de juego pueden reemplazar las coincidencias existentes basadas en turnos, las coincidencias en tiempo real o los métodos de guardado de juegos persistentes.

<a name="GamePlayKit-Framework-Enhancements" />

## <a name="gameplaykit-framework-enhancements"></a>Mejoras de GamePlayKit Framework

Se ha realizado la siguiente mejora en el marco de trabajo de GamePlayKit para macOS Sierra:

- Se ha agregado la generación de ruido de procedimiento y se puede usar para mejorar el realismo en las texturas de aspecto natural, agregar realismo a los movimientos de la cámara y ayudar a generar sofisticados mundos de juego.
- Use la creación de particiones espaciales para particionar los datos del mundo de juego para una búsqueda eficaz.
- Se ha agregado un nuevo estratega de Monte Carlo ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) para realizar cálculos de movimiento exhaustivos.
- Se ha agregado una nueva API de árbol de decisión ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) y [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) para mejorar la generación de juegos de inteligencia artificial.
- se ha agregado compatibilidad con 3D a los comportamientos existentes de agente y ruta de acceso mediante las nuevas clases [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) y [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) .
- Use la nueva clase [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) para proporcionar rutas de acceso natural y de alto rendimiento.
- Las nuevas clases [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) y [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) hacen que la combinación de GameplayKit y SpriteKit sea más fácil que nunca.

<a name="Metal-Framework-Enhancements" />

## <a name="metal-framework-enhancements"></a>Mejoras en el marco de metal

Se ha realizado la siguiente mejora en el marco de metal para macOS Sierra:

- las aplicaciones 3D y los juegos ahora pueden usar teselación para representar eficazmente escenas complejas y geometría a través de la GPU.
- Utilice la especialización de función para crear una colección altamente optimizada de funciones de combinación de materiales y claros para una escena.
- Proporcionar un control específico de la asignación de recursos para optimizar el rendimiento de las aplicaciones basadas en metal mediante montones de recursos y destinos de representación de memoria.

Para obtener más información, consulte la [Guía de programación de metal](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)de Apple.

<a name="ModelIO-Framework-Enhancements" />

## <a name="model-io-framework-enhancements"></a>Mejoras en el marco de e/s de modelo

Se ha realizado la siguiente mejora en el marco de e/s de modelo para macOS Sierra:

- Ahora se admite el formato de archivo USD.
- Use la nueva `MDLMaterialPropertyGraph` clase para admitir fácilmente los cambios de tiempo de ejecución en los modelos.
- Se ha agregado compatibilidad con el campo de distancia con signo a la clase [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) .
- Use la nueva `MDLLightProbeIrradianceDataSource` clase para ayudar en la colocación de sondeos ligeros.

<a name="Photos-Framework-Enhancements" />

## <a name="photos-framework-enhancements"></a>Mejoras en el marco de fotos

Se ha realizado la siguiente mejora en el marco de fotos para macOS Sierra:

- Live Photo Editing ahora está disponible para las aplicaciones que admiten el marco de fotografías y para las extensiones de edición de fotografías (para su uso en las fotos y las aplicaciones de cámara).
- Use la nueva clase [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) para aplicar ediciones al vídeo y al contenido de las fotos en directo.
- Use las clases [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) y [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) para aprovechar las ventajas de la nueva característica procesador de imagen principal para realizar modificaciones.
- Para admitir fotos en directo, las clases [PHLivePhoto](https://developer.apple.com/reference/photos/phlivephoto) y [PHLivePhotoView](https://developer.apple.com/reference/photosui/phlivephotoview) se han trasladado de iOS a MacOS.

<a name="SceneKit-Framework-Enhancements" />

## <a name="scenekit-framework-enhancements"></a>Mejoras de SceneKit Framework

Se ha realizado la siguiente mejora en el marco de trabajo de SceneKit para macOS Sierra:

- Ahora incluye un nuevo sistema de representación en cinta (PBR) para obtener resultados más realistas con la creación de recursos más sencilla.
- Use el nuevo modelo de sombreado de [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) para producir una amplia gama de efectos de sombreado realistas, al tiempo`Diffuse`que solo `Roughness`requiere tres propiedades fundamentales (, `Metalness` y).
- Puesto que el sombreado de PBR funciona mejor con la iluminación basada en `LightingEnvironment` el entorno, use la propiedad para asignar la iluminación basada en imagen a tan solo una escena entera.
- Utilice la `IESProfileURL` propiedad para importar los accesorios de luz del mundo real que definen la base de iluminación en valores reales como la intensidad (en lúmenes) y la temperatura de color (en grados Kelvin).
- La clase [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera) puede proporcionar mayor realismo mediante el uso de las características y los efectos de HDR. Use la exposición adaptable para crear efectos automáticos o usar viñetas, los halos de color y la clasificación de color para agregar efectos de filmatic al juego.
- Las características de la cámara de PBR y HDR proporcionan mejores resultados que las técnicas de representación tradicionales y, como resultado, SceneKit ahora realiza todos los cálculos de color en un espacio de colores lineal (mediante la gama de colores P3 en pantallas de dispositivos anchos).
- El color SceneKit Now coincide con todos los colores mediante la lectura de la información del perfil de color.
- SceneKit interpreta los valores de los componentes de color en un espacio de colores RGB lineal para todos los tipos de sombreador.
- Dado que SceneKit Lee y ajusta la información de Perfil de color en imágenes de textura, use catálogos de recursos para todas las imágenes para asegurarse de que se proporciona esta información.
- Tanto la representación del espacio de colores lineal como el color ancho pueden deshabilitarse mediante `SCNDisableLinearSpaceRendering` la `SCNDisableWideGamut` especificación de las claves y `Info.plist`en la de la aplicación.
- Cree primates polígono arbitrario (se carga desde archivos o se genera mediante programación) para especificar la geometría con la nueva clase [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon) .

<a name="Security-Framework-Enhancements" />

## <a name="security-framework-enhancements"></a>Mejoras en el marco de seguridad

Se ha realizado la siguiente mejora en el marco de seguridad para macOS Sierra:

- La `SecKey` interfaz se ha modernizado y unificado en todas las plataformas (iOS, tvOS, watchos y MacOS).

<a name="SpriteKit-Framework-Enhancements" />

## <a name="spritekit-framework-enhancements"></a>Mejoras de SpriteKit Framework

Se ha realizado la siguiente mejora en el marco de trabajo de SpriteKit para macOS Sierra:

- Ahora, Tilemaps admite formas de mosaico cuadrada, hexagonal y isométrica para juegos de 2D, 2,5 d y de desplazamiento lateral con `SKTileMapMode`las `SKTileGroup`clases `SKTileGroupRule` , `SKTileSet` y.
- Use la nueva `SKWarpGeometry` clase para expandir o distorsionar la representación de [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) o [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) . La nueva clase [SKAction](https://developer.apple.com/reference/spritekit/skaction) se puede usar para animar las transiciones entre los efectos de alabeo.
- Los sombreadores personalizados pueden proporcionar atributos`SKAttribute`() que se pueden configurar por separado en cada nodo que use el sombreador proporcionando un valor de atributo`SKAttributeValue`().
- La clase [SKView](https://developer.apple.com/reference/spritekit/skview) proporciona varios métodos nuevos para proporcionar un control exhaustivo sobre cuándo y cómo se representa una escena.

<a name="New-Frameworks" />

## <a name="new-frameworks"></a>Nuevos marcos de trabajo

Se han agregado los siguientes marcos de trabajo a macOS Sierra:

- **Marco de trabajo** de intents: este marco permite que la aplicación examine las interacciones (por ejemplo, la ubicación o las acciones del usuario) y tome medidas en función de esa información.
- **SafariServices Framework** : este marco de trabajo permite a la aplicación desarrollar extensiones de aplicaciones para Safari (como los bloqueadores de contenido) tanto para MacOS como para iOS.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de Mac](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [Novedades de OS X 10,12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
