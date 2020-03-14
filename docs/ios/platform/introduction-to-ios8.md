---
title: Introducción a iOS 8
description: Con iOS 8, Apple ha proporcionado una gran cantidad de nuevas plataformas y API para entusiasmar a los desarrolladores. En esta guía se presentarán estas nuevas API y verá cómo iOS 8 puede beneficiar a los desarrolladores y a los usuarios.
ms.prod: xamarin
ms.assetid: 33AD66C0-3743-49FE-9DCE-88ED3A16BA63
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: 9353cffd924688058c1495b9258cc7f0e0ce7b82
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79306192"
---
# <a name="introduction-to-ios-8"></a>Introducción a iOS 8

_Con iOS 8, Apple ha proporcionado una gran cantidad de nuevas plataformas y API para entusiasmar a los desarrolladores. En esta guía se presentarán estas nuevas API y verá cómo iOS 8 puede beneficiar a los desarrolladores y a los usuarios._

iOS 7 ha cambiado visualmente la interfaz de usuario de iOS completa de lo que los usuarios y desarrolladores tuvieron previsto, directamente desde el primer SO de iPhone. IOS 8 continúa con esto proporcionando muchos marcos de trabajo a los desarrolladores, lo que permite a los usuarios controlar casi todos los aspectos de su vida directo desde su iPhone. Por ejemplo, el mantenimiento y la idoneidad se pueden analizar con *HealthKit*, los códigos de acceso se obsoleta con autenticación biométrica mediante *LocalAuthentication*, *las extensiones de aplicaciones* abren un canal de comunicación entre aplicaciones de terceros y *HomeKit* permiten la posibilidad de convertir su casa en una casa. 

Si iOS 7 fuera a la luz de los usuarios, iOS 8 se centra en la imluz de los desarrolladores con una amplia gama de herramientas de Tasty nuevas. 

En esta guía se presentan las nuevas API para desarrolladores de Xamarin. iOS.  

También hay algunas API que han quedado en desuso en iOS 8, que se detallan al final de este documento.

## <a name="requirements"></a>Requisitos

Los siguientes elementos son necesarios para crear aplicaciones de iOS 8 en Visual Studio para Mac:

- **Xcode 7 e iOS 8 o posterior** : las API de Xcode y iOS más recientes de Apple deben instalarse y configurarse en el equipo del desarrollador.
- **Visual Studio para Mac** : la versión más reciente de Visual Studio para Mac debe estar instalada y configurada en el dispositivo del usuario.
- **dispositivo o simulador de iOS 8** : un dispositivo iOS que ejecuta la versión más reciente de iOS 8 para realizar pruebas.

## <a name="home-and-leisure"></a>Hogar y ocio

iOS 8 ha ayudado a la plantación sólida de Apple y al dispositivo iOS directamente en el corazón de su hogar mediante el uso de HomeKit y HealthKit. En esta sección, veremos cómo funcionan ambos marcos de trabajo nuevos y cómo se pueden integrar en la aplicación de Xamarin. iOS.

## <a name="homekit"></a>HomeKit

El control de sus dispositivos desde su iPhone no es una nueva aplicación de tecnología; muchos productos conectados-Home pueden controlarse a través de una aplicación iOS. Sin embargo, HomeKit ahora lleva un paso más allá mediante la promoción de un protocolo común para dispositivos de automatización de inicio y la disponibilidad de una API pública para determinados fabricantes, como iHome, Philips y Honeywell. Para el usuario, esto significa que pueden controlar casi todos los aspectos de su hogar sin problemas desde dentro de una aplicación. Es irrelevante que sepan que usan una bombilla de Philips Hue o una alarma anidada. Los usuarios también pueden encadenar varios procesos de inicio inteligente juntos en "escenas".

Con HomeKit, las aplicaciones de terceros y Siri pueden detectar accesorios y agregarlos a su base de datos de configuración de inicio personal, editarlos y actuar sobre ellos, y comunicarse con los accesorios y sus servicios para realizar una acción.

### <a name="configuration"></a>Configuración

En el diagrama siguiente se muestra la jerarquía básica de la configuración de HomeKit Accessories:

![](introduction-to-ios8-images/image1.png "This diagram shows the basic hierarchy of the configuration of HomeKit accessories")

Para empezar a trabajar con HomeKit, los desarrolladores deberán asegurarse de que el perfil de aprovisionamiento tenga seleccionado el servicio HomeKit. Apple también ha proporcionado a los desarrolladores un complemento de HomeKit Simulator para Xcode. Esto se puede encontrar en el [Centro para desarrolladores de Apple](https://developer.apple.com/downloads/index.action), en `Hardware IO Tools for Xcode`. 

Para obtener más información, consulte nuestra guía de [HomeKit](~/ios/platform/homekit.md) .

## <a name="healthkit"></a>HealthKit

HealthKit es un marco de trabajo incluido en iOS 8 que proporciona un almacén de datos centralizado, coordinado y seguro para la información relacionada con el estado. El sistema operativo garantiza la privacidad y seguridad de la información de estado y, con la aplicación de mantenimiento, un panel para el usuario. Con el permiso del usuario, las aplicaciones pueden leer y escribir una amplia variedad de información de estado.

Para más información sobre cómo usar esto en la aplicación de Xamarin. iOS, consulte la [Introducción a](~/ios/platform/healthkit.md) la guía de HealthKit.

## <a name="extending-iphone-functionality"></a>Extender la funcionalidad de iPhone
Con iOS8, se proporciona a los desarrolladores un mayor control sobre quién puede usar su aplicación y una mayor capacidad para la comunicación más abierta entre aplicaciones de terceros. Características como extensiones de aplicación y selector de documentos abren un mundo de posibilidades para cómo se pueden usar las aplicaciones en el ecosistema de Apple.

### <a name="app-extensions"></a>Extensiones de aplicaciones
Las extensiones de aplicación, para simplificar, son una manera de que las aplicaciones de terceros se comuniquen entre sí. Para mantener los estándares de alta seguridad y para mantener la integridad de las aplicaciones en espacio aislado, esta comunicación no se produce directamente entre las aplicaciones. En su lugar, se lleva a cabo mediante una *extensión* en el centro.

El primer paso para crear una extensión de aplicación es definir el punto de extensión correcto, lo que es importante para garantizar el comportamiento y la disponibilidad de las API correctas. Para crear una extensión de aplicación en Visual Studio para Mac, agréguela a una aplicación existente agregando un nuevo proyecto a la solución.

En el cuadro de diálogo **nuevo proyecto** , vaya a **C#**  > **iOS** > **Unified API** > **extensiones**, como se muestra en la siguiente captura de pantalla:

![](introduction-to-ios8-images/image2.png "Creating a new extension")

El cuadro de diálogo nuevo proyecto proporciona siete plantillas de proyecto nuevas para crear extensiones de aplicación y se describen a continuación. Tenga en cuenta que muchas de las extensiones se relacionan con otras API nuevas de iOS, como selector de documentos:

- **Acción** : permite a los desarrolladores crear botones de acción personalizados únicos que permiten a los usuarios realizar ciertas tareas.
- **Teclado personalizado** : permite a los desarrolladores agregar a la gama de teclados de Apple integrados mediante la adición de uno personalizado. El teclado popular, Swype, lo usa para traer su teclado a iOS.
- **Selector de documentos** : contiene un controlador de vista selector de documentos que permite a los usuarios tener acceso a archivos fuera del espacio aislado de la aplicación.
- **Proveedor de archivos de selector de documentos** : proporciona almacenamiento seguro para los archivos que usan el selector de documentos.
- **Edición de fotografías** : expande los filtros y las herramientas de edición que proporciona Apple en la aplicación fotos para ofrecer a los usuarios más control y más opciones al editar sus fotos.
- **Hoy en día** , esto proporciona a las aplicaciones la capacidad de mostrar widgets en la sección hoy del centro de notificaciones.

Para obtener más información sobre el uso de extensiones de aplicaciones en Xamarin, consulte la guía [Introducción a las extensiones de aplicaciones](~/ios/platform/extensions.md) .

### <a name="touch-id"></a>Touch ID

Touch ID se incorporó en iOS 7 como medio para autenticar al usuario, similar a un código de acceso. Sin embargo, se limitó a desbloquear el dispositivo, usar la tienda de aplicaciones, usar iTunes y autenticar solo la cadena de claves de iCloud 

Ahora hay dos maneras de usar Touch ID como mecanismo de autenticación en aplicaciones de iOS 8 mediante la API de autenticación local. Actualmente no es posible usar la autenticación local para autenticarse de forma remota.

En primer lugar, ayuda a los servicios de cadena de claves existentes mediante el uso de nuevas listas de claves Access Controls (ACL). Los datos de cadena de claves se pueden desbloquear con la autenticación correcta de la huella digital de los usuarios.

En segundo lugar, LocalAuthentication proporciona dos métodos para autenticar la aplicación localmente. Los desarrolladores deben usar `CanEvaluatePolicy` para determinar si el dispositivo es capaz de aceptar el ID. de Touch y, a continuación, `EvaluatePolicy` para iniciar la operación de autenticación.

Para obtener más información sobre Touch ID e información sobre cómo integrarlo en una aplicación de Xamarin. iOS, consulte [Touch ID and facial ID en las guías de Xamarin. iOS](~/ios/platform/touch-id-face-id.md) .

### <a name="document-picker"></a>Selector de documentos

El selector de documentos funciona con una unidad de usuarios iCloud para permitir al usuario abrir archivos que se han creado en otra aplicación, importarlos y manipularlos y volver a exportarlos. Esto crea un flujo de trabajo intuitivo y, por lo tanto, una experiencia mucho mejor para los usuarios. la sincronización de iCloud lleva un paso más allá: los cambios realizados en una aplicación también se reflejarán de forma coherente en todos los dispositivos.

Para obtener más información sobre el selector de documentos con más detalle y obtener información sobre cómo integrarlo en una aplicación de Xamarin. iOS, consulte la guía de [Introducción a selector de documentos](~/ios/platform/document-picker.md) .

### <a name="handoff"></a>Handoff

La entrega, que forma parte de la característica de continuidad más grande, lleva un paso más allá de la integración de OS X e iOS. Esto incluye la eliminación multiplataforma, la capacidad de realizar llamadas de iPhone, SMS en el iPad y Mac y mejoras en el tethering del iPhone.

La entrega funciona con iOS 8 y Yosemite, y requiere que una cuenta de iCloud inicie sesión en todos los dispositivos que quiera usar. Debe funcionar con la mayoría de las aplicaciones de Apple preinstaladas, como Safari, iWork, mapas, calendarios y contactos.

Para obtener más información, consulte nuestra guía de [entrega](~/ios/platform/handoff.md) .

## <a name="unified-storyboards"></a>Guiones gráficos unificados
iOS 8 incluye un nuevo mecanismo más sencillo de usar para crear la interfaz de usuario, el guión gráfico unificado. Con un solo guión gráfico para cubrir todos los tamaños de pantalla de hardware diferentes, se pueden crear vistas rápidas y receptivas en un estilo real "diseñar una vez, usar muchos".

Antes de iOS8, los desarrolladores usaban `UIInterfaceOrientation` para distinguir entre los modos vertical y horizontal, y `UIInterfaceIdiom` para distinguir entre los dispositivos iOS. En iOS8, ya no es necesario crear guiones gráficos independientes para dispositivos iPhone e iPad; la orientación y el dispositivo se determinan mediante el uso de *clases de tamaño*.

Cada dispositivo se define mediante una clase de tamaño, en el eje vertical y horizontal, y hay dos tipos de clases de tamaño en iOS 8:

- **Normal** : es para un tamaño de pantalla grande (por ejemplo, un iPad) o un gadget que da la impresión de un gran tamaño (por ejemplo, un UIScrollView
- **Compacto** : es para dispositivos más pequeños (por ejemplo, el iPhone). Este tamaño tiene en cuenta la orientación del dispositivo.

Si se usan juntos los dos conceptos, el resultado es una cuadrícula de 2 x 2 que define los diferentes tamaños posibles que se pueden usar en las distintas orientaciones, como se puede ver en el diagrama siguiente:

![](introduction-to-ios8-images/image3.png "A diagram representing the 2 x 2 grid that defines the different possible sizes that can be used in both the differing orientations")

Para obtener más información sobre las clases de tamaño, consulte la [Introducción a los guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md).

## <a name="photo-kit"></a>Kit fotográfico
Photo kit es un nuevo marco que permite que las aplicaciones consulten la biblioteca de imágenes del sistema y creen interfaces de usuario personalizadas para ver y modificar su contenido. Incluye varias clases que representan recursos de imagen y vídeo, así como colecciones de recursos como álbumes y carpetas.

Para obtener más información, consulte nuestra guía de [PhotoKit](~/ios/platform/photokit.md) .

## <a name="games"></a>Juegos

<a name="scenekit" />

### <a name="scene-kit"></a>Kit de escenas

El kit de escenas es una API de gráficos de escenas 3D que simplifica el trabajo con gráficos 3D. Se presentó por primera vez en OS X 10,8 y ahora se ha incorporado a iOS 8. Con el kit de escenas, crear visualizaciones 3D envolventes y juegos en 3D esporádicos no requiere experiencia en OpenGL. Al basarse en conceptos comunes de los gráficos de escenas, el kit de escenas abstrae las complejidades de OpenGL y OpenGL ES, lo que facilita enormemente la adición de contenido 3D a una aplicación. Sin embargo, si es un experto en OpenGL, el kit de escenas también tiene una gran compatibilidad para la vinculación directa con OpenGL. También incluye numerosas características que complementan los gráficos 3D, como la física, y se integran con muchos otros marcos de trabajo de Apple, como la animación básica, la imagen principal y el kit de Sprite.

Para obtener más información, consulte nuestra documentación de [SceneKit](~/ios/platform/gaming/scenekit.md) .

<a name="spritekit" />

### <a name="sprite-kit"></a>Kit de Sprite

El kit de sprites, el marco de juego de 2D de Apple, tiene algunas nuevas características interesantes en iOS 8 y OS X Yosemite. Entre ellas se incluyen la integración con el kit de escenas, compatibilidad con el sombreador, iluminación, sombras, restricciones, generación normal de mapas y mejoras físicas. En concreto, las nuevas características de la física hacen que sea muy fácil agregar efectos realistas a un juego.

Para obtener más información, consulte nuestra documentación de [SpriteKit](~/ios/platform/gaming/spritekit.md) .

## <a name="other-changes"></a>Otros cambios
Además de los principales cambios en iOS 8 que se han descrito anteriormente, Apple ha actualizado Adicionalmente muchos marcos de trabajo existentes. Estos se detallan a continuación:

- **[Imagen principal](https://developer.apple.com/library/prerelease/ios/documentation/GraphicsImaging/Reference/CoreImagingRef/index.html#//apple_ref/doc/uid/TP40001171)** : Apple se ha ampliado en su marco de procesamiento de imágenes agregando mejor compatibilidad para la detección de regiones rectangulares y códigos QR dentro de las imágenes. Mike Bluestein explora esta entrada de blog titulada [detección de imágenes en iOS 8](https://blog.xamarin.com/image-detection-in-ios-8/)

## <a name="deprecated-apis"></a>Interfaces API desusadas
Con todas las mejoras realizadas en iOS 8, una serie de API han quedado en desuso. Algunos de ellos se detallan a continuación.

- **[UIApplication](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplication_Class/index.html#//apple_ref/occ/cl/UIApplication)** : los métodos y las propiedades que se usan para registrar notificaciones remotas han quedado en desuso. Son registerForRemoteNotificationTypes y enabledRemoteNotificationTypes.
- **[UIViewController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/cl/UIViewController)** : las clases traits y size han reemplazado a los métodos y las propiedades que se usan para describir la orientación de la interfaz. Consulte la [Introducción a los guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) para obtener más información sobre cómo usarlos.

- **[UISearchDisplayController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayController_Class/index.html#//apple_ref/occ/cl/UISearchDisplayController)** : se ha reemplazado por UISearchController en iOS8.

## <a name="summary"></a>Resumen
En este artículo se han examinado algunas de las nuevas características introducidas por Apple en iOS 8.

## <a name="related-links"></a>Vínculos relacionados

- [UIKitEnhancements (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-uikitenhancements)
- [Introducción a las extensiones de aplicaciones](~/ios/platform/extensions.md)
- [Introduction to CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) (Introducción a CloudKit)
- [Introducción al selector de documentos](~/ios/platform/document-picker.md)
- [Introducción a HealthKit](~/ios/platform/healthkit.md)
- [Introducción a los controles de cámara manual](~/ios/user-interface/controls/intro-to-manual-camera-controls.md)
- [Touch ID y facial ID con Xamarin. iOS](~/ios/platform/touch-id-face-id.md)
- [Introducción a los guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md)
