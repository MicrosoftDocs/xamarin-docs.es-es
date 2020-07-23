---
title: Introducción a iOS 9
description: En este artículo se presentan todas las API y características nuevas y modificadas disponibles en iOS 9 para desarrolladores de Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 4D71BBD9-B948-4B59-9AF5-F199C51CBEB3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: bfa2a74bf9cd30fb7b9888ecd7339a80fe472c9e
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939092"
---
# <a name="introduction-to-ios-9"></a>Introducción a iOS 9

_En este artículo se presentan todas las API y características nuevas y modificadas disponibles en iOS 9 para desarrolladores de Xamarin. iOS._

![El logotipo de iOS 9](images/ios9-sml.png)

Apple ha agregado varias API y servicios nuevos en iOS 9 junto con muchas mejoras en las características existentes.

## <a name="3d-touch"></a>Entrada táctil 3D

Como novedad de iOS 9 y iPhone 6S y iPhone 6S Plus, 3D Touch agrega gestos sensibles a la presión a las aplicaciones de iOS. Con el toque 3D, una aplicación de iPhone ahora no solo puede indicar que el usuario está tocando la pantalla del dispositivo, sino también la cantidad de presión que el usuario está ejerciendo y responder a los diferentes niveles de presión.

en 3D Touch se proporcionan las siguientes características a la aplicación:

- **Sensibilidad** a la presión: ahora, las aplicaciones pueden medir el grado de dificultad o luz que el usuario toca con la pantalla y aprovechar esa información. Por ejemplo, una aplicación de dibujo puede hacer que una línea sea más gruesa o más delgada en función de cómo toque el usuario a la pantalla.
- **PEEK y pop** : la aplicación ahora puede permitir que el usuario interactúe con sus datos sin tener que navegar fuera de su contexto actual. Al presionar el dedo en la pantalla, pueden *inspeccionarse* en el elemento que le interesan (por ejemplo, al obtener una vista previa de un mensaje). Al presionar más duro, *pueden entrar en* el elemento.
- **Acciones rápidas** : Piense en acciones rápidas como los menús contextuales que se pueden desplegar cuando un usuario hace clic con el botón derecho en un elemento de una aplicación de escritorio. Con las acciones rápidas, puede agregar accesos directos comunes y rápidos y fáciles de acceder a las funciones de la aplicación desde el icono de la pantalla principal del dispositivo iOS.

Para obtener más información, consulte nuestra [Introducción a la guía táctil 3D](~/ios/platform/3d-touch.md) .

## <a name="app-transport-security"></a>Seguridad de transporte de aplicación

Como novedad de iOS 9, la seguridad de transporte de aplicaciones (ATS) exige conexiones seguras entre recursos de Internet (como el servidor back-end de la aplicación) y la aplicación. ATS garantiza que todas las comunicaciones de Internet se ajusten a los procedimientos recomendados de conexión segura, lo que evita la divulgación accidental de información confidencial, ya sea directamente a través de la aplicación o de una biblioteca que consume.

Dado que ATS está habilitado de forma predeterminada en las aplicaciones compiladas para iOS 9 y OS X 10,11 (el Capitan), todas las conexiones que usan [NSUrlConnection](xref:Foundation.NSUrlConnection), [CFUrl](xref:CoreFoundation.CFUrl) o [NSUrlSession](xref:Foundation.NSUrlSession) estarán sujetas a los requisitos de seguridad de ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.

Para obtener más información sobre ATS, consulte nuestra guía de [seguridad de transporte de aplicaciones](~/ios/app-fundamentals/ats.md) .

<a name="multitasking"></a>

## <a name="multitasking-for-ipad"></a>Multitarea para iPad

Con iOS 9, Apple ha agregado compatibilidad con la multitarea para ejecutar dos aplicaciones al mismo tiempo en hardware de iPad específico. Como resultado, las aplicaciones de Xamarin. iOS ya no suponen que se trata de la única aplicación que se ejecuta en un momento dado o que tienen acceso a la pantalla completa o a los recursos del dispositivo.

La multitarea se admite a través de las siguientes características:

- **Deslizar sobre** : permite al usuario ejecutar temporalmente una segunda aplicación iOS en un panel deslizante (en el lado derecho o izquierdo de la pantalla en función de la dirección del idioma) que cubre aproximadamente el 25% de la aplicación principal que se está ejecutando actualmente. La diapositiva solo está disponible en un iPad Pro, iPad Air, iPad Air 2, iPad mini 2, iPad mini 3 o iPad Mini 4.
- **Vista en dos paneles** : en el hardware de iPad compatible (solo iPad Air 2, iPad Mini 4 y iPad Pro), el usuario puede elegir una segunda aplicación y ejecutarla en paralelo con la aplicación que se está ejecutando en el modo de pantalla dividida. El usuario puede controlar el porcentaje de la pantalla principal que ocupa cada aplicación.
- **Imagen** : para las aplicaciones que reproducen el contenido de vídeo, el vídeo ahora se puede reproducir en una ventana móvil y de tamaño variable que flota por las demás aplicaciones que se ejecutan actualmente en el dispositivo iOS. El usuario tiene control total sobre el tamaño y la posición de esta ventana. Imagen en imagen solo está disponible en un iPad Pro, iPad Air, iPad Air 2, iPad mini 2, iPad mini 3 o iPad Mini 4.

Para obtener más información acerca de las nuevas capacidades de multitarea de iOS 9, consulte la guía [de multitarea para iPad](~/ios/platform/multitasking.md) .

## <a name="new-contacts-and-contacts-ui-frameworks"></a>Marcos de interfaz de usuario nuevos contactos y contactos

Con la introducción de iOS 9, Apple ha lanzado dos nuevos marcos de trabajo, [contactos](xref:Contacts) y [ContactsUI](xref:ContactsUI), que reemplazan los marcos de interfaz de usuario de la libreta de direcciones y la libreta de direcciones existentes usados por iOS 8 y versiones anteriores.

Estos nuevos marcos orientados a objetos proporcionan lo siguiente:

- **Contactos** : proporciona acceso de Xamarin. iOS a la información de contacto del usuario. Dado que la mayoría de las aplicaciones solo requieren acceso de solo lectura, este marco de trabajo se ha optimizado para el acceso seguro para subprocesos y de solo lectura.
- **ContactsUI** : proporciona elementos de interfaz de usuario de Xamarin. iOS para mostrar, editar, seleccionar y crear contactos en dispositivos iOS.

Para obtener más información, consulte nuestra documentación de la interfaz de usuario de contactos [y contactos](~/ios/platform/contacts.md) .

## <a name="new-search-apis"></a>Nuevas API de búsqueda

La búsqueda se ha ampliado en iOS 9 para proporcionar nuevas formas de acceder a la información dentro de la aplicación de Xamarin. iOS. Con las nuevas API de búsqueda, puede realizar búsquedas en el contenido de la aplicación a través de los resultados de búsqueda de Spotlight y de Safari, los recordatorios y las sugerencias de entrega y Siri. Esto permite a los usuarios acceder rápidamente a las actividades y la información en la aplicación.

Además, las nuevas API de búsqueda facilitan la integración de la búsqueda en la aplicación sin una experiencia de implementación de búsqueda anterior. Por este motivo, Apple notifica que normalmente tarda unas horas en hacer que el contenido de una aplicación de iOS 9 se pueda buscar de forma universal mediante la búsqueda de aplicaciones.

Para obtener más información, consulte nuestra documentación sobre [mejoras de búsqueda](~/ios/platform/search/index.md) .

## <a name="new-stack-view"></a>Nueva vista de pila

El control de vista de pila ([UIStackView](xref:UIKit.UIStackView) aprovecha la eficacia de las clases de diseño y tamaño automáticos para administrar una pila de subvistas (ya sea horizontal o verticalmente) que responde dinámicamente a la orientación y el tamaño de la pantalla del dispositivo iOS.

Con el control de vista de pila, se reduce considerablemente la cantidad de trabajo necesaria para diseñar una interfaz de usuario. El diseño de todas las subvistas adjuntas a una vista de pila se administra automáticamente en función de las propiedades definidas por el desarrollador como el eje, la distribución, la alineación y el espaciado.

Para obtener más información, consulte nuestra [Introducción a](~/ios/user-interface/controls/uistackview.md) la documentación de la vista de pila.

## <a name="collection-view-changes"></a>Cambios en la vista de colección

En iOS 9, la vista de colección ([UICollectionView](xref:UIKit.UICollectionView) ahora permite arrastrar la reordenación de los elementos de forma predeterminada agregando un nuevo reconocedor de gestos predeterminado y varios métodos de compatibilidad nuevos.

Con estos nuevos métodos, puede implementar fácilmente arrastrar y reordenar en la vista de colección y tener la opción de personalizar la apariencia de los elementos durante cualquier fase del proceso de reordenación.

Para obtener más información sobre los cambios de la vista de recopilación para iOS 9, consulte nuestra guía de cambios en la [vista de recopilación](~/ios/user-interface/controls/uicollectionview.md) .

## <a name="game-enhancements"></a>Mejoras en los juegos

Con iOS 9, Apple ha realizado varias mejoras tecnológicas en las API de juegos que facilitan la implementación de gráficos de juegos y audio en la aplicación de Xamarin. iOS. Entre ellas se incluyen la facilidad de desarrollo a través de los marcos de trabajo de alto nivel y el aprovechamiento de la capacidad de la GPU del dispositivo iOS para mejorar la velocidad y las capacidades gráficas con mejoras de bajo nivel.

Esto incluye GameplayKit, ReplayKit, e/s de modelo, MetalKit y los sombreadores de rendimiento de metal junto con las nuevas características mejoradas de metal, SceneKit y SpriteKit.

Para obtener más información, consulte la documentación sobre [mejoras de juegos](~/ios/platform/gaming/index.md) .

## <a name="homekit-framework-changes"></a>Cambios de HomeKit Framework

El marco de trabajo de [HomeKit](xref:HomeKit) , introducido en iOS 8, proporciona la capacidad de configurar y controlar varios accesorios habilitados para HomeKit (por ejemplo, luces automatizadas, bloqueos de puertas y aperturas de puertas de garaje) desde una aplicación de Xamarin. iOS. Además de ser fácil de instalar y configurar, los accesorios de HomeKit se pueden controlar a través de comandos Siri orales.

En iOS 9, Apple ha facilitado la instalación, amplió los tipos de accesorios admitidos y proporcionó más interacciones de accesorios (por ejemplo, el control de un accesorio de forma remota a través de iCloud).

Para obtener más información, consulte nuestra [Introducción a HomeKit](~/ios/platform/homekit.md), [aplicación de ejemplo de HomeKitIntro iOS](https://docs.microsoft.com/samples/xamarin/ios-samples/homekit-homekitintro) y documentación de [HomeKit](https://developer.apple.com/homekit/) de Apple.

## <a name="handoff-framework-changes"></a>Cambios en el marco de entrega

Apple ha introducido la entrega (también conocida como continuidad) en iOS 8 y OS X Yosemite (10,10) como una manera para que el usuario inicie una actividad en uno de sus dispositivos (iOS o Mac) y continúe esa misma actividad en otro de sus dispositivos (tal y como lo identificó la cuenta de iCloud del usuario).

La entrega se amplió en iOS 9 para admitir también nuevas funcionalidades de búsqueda mejoradas. Para obtener más información, consulte nuestra documentación sobre [mejoras de búsqueda](~/ios/platform/search/index.md) . Para obtener más información sobre el uso de la entrega, consulte nuestra [Introducción a](~/ios/platform/handoff.md) la documentación de la entrega.

## <a name="new-extension-points"></a>Nuevos puntos de extensión

En iOS 8, Apple presentó las extensiones: las bibliotecas que presenta el sistema operativo en contextos estándar, como en el centro de notificaciones, cuando el usuario solicita un teclado o cuando edita una foto.

Con iOS 9, Apple amplía la compatibilidad con las extensiones al proporcionar varios _puntos de extensión_ nuevos que definen las directivas de uso y proporcionan las API para trabajar en un área determinada de la manera siguiente:

- **Nuevo punto de extensión de unidad de audio** : Use este punto de extensión para proporcionar efectos de audio, instrumentos musicales, generadores de sonido, etc., para su uso en otras aplicaciones de host de unidad de audio (como GarageBand). Este punto de extensión también le permite vender _unidades de audio_ (complementos de audio) en la tienda de aplicaciones.
- **Nuevo punto de extensión de mantenimiento de índices** : Use este punto de extensión para admitir la reindexación de datos de aplicaciones sin necesidad de volver a iniciar la aplicación.
- **Nuevos puntos de extensión de red** (estos requieren permisos especiales de Apple):
  - **Extensión de proveedor de proxy de aplicación** : Use este punto de extensión para implementar un proxy de red transparente personalizado del lado cliente.
  - **Extensión de proveedor de datos de filtro o de proveedor de control de filtros** : Use estos puntos de extensión para implementar el filtrado de contenido de red dinámico en el dispositivo.
  - **Extensión de proveedor de túnel de paquetes** : Use este punto de extensión para implementar un cliente del Protocolo de túnel VPN personalizado.
- **Nuevos puntos de extensión de Safari**:
  - **Extensión de bloqueo de contenido** : Use este punto de extensión para definir una lista de contenido bloqueado que no se mostrará cuando el usuario Explore la Web.
  - **Extensión de vínculos compartidos** : Use este punto de extensión para habilitar la visualización del contenido de la aplicación en los vínculos compartidos de Safari.

Para obtener más información, consulte nuestra [Introducción a las extensiones](~/ios/platform/extensions.md) y la documentación de la guía de programación de la [extensión de aplicaciones](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214) de Apple.

## <a name="keychain-enhancements"></a>Mejoras de cadena de claves

En iOS 9, Apple ha mejorado la cadena de claves para proporcionar un nuevo tipo de clave de cifrado para las opciones Secure enclave y more protección de elementos como se indica a continuación:

- Nueva restricción de Touch ID que invalida los elementos de cadena de claves cuando se modifica la base de datos de huellas digitales.
- Nuevas restricciones que permiten crear Access Control lista de entradas solo con Touch ID o el código de acceso.
- Nuevo contexto de autenticación que permite invocar la autenticación independiente de las `SecItem` llamadas.
- Access Control enumeración entropía (mediante la opción de contraseña de aplicación) para el cifrado de elementos de cadena de claves proporcionado por la aplicación.
- Compatibilidad para generar y usar claves dentro del enclave seguro (mediante el `kSecAttrTokenIDSecureEnclave` atributo).

Para obtener más información, consulte [Touch ID and facial ID en Xamarin. iOS](~/ios/platform/touch-id-face-id.md).

## <a name="right-to-left-language-support"></a>Compatibilidad con idiomas de derecha a izquierda

En iOS 9, Apple ha facilitado la presentación de una interfaz de usuario volteada con más facilidad que nunca, ya que proporciona compatibilidad total con los idiomas de derecha a izquierda. Incluye lo siguiente:

- Los controles estándar de [UIKit](xref:UIKit) se voltearán de derecha a izquierda en función de la configuración regional y de idioma de los dispositivos iOS.
- La clase [UIView](xref:UIKit.UIView) proporciona atributos que permiten definir cómo debe aparecer una vista determinada cuando se voltea de derecha a izquierda.
- La capacidad de voltear una imagen mediante programación usando la propiedad [FlipsForRightToLeftLayoutDirection](xref:UIKit.UIImage.FlipsForRightToLeftLayoutDirection) de la clase [UIImage](xref:UIKit.UIImage) .

Para obtener más información, consulte la documentación del soporte técnico de Apple de [derecha a izquierda](https://developer.apple.com/library/prerelease/ios/documentation/MacOSX/Conceptual/BPInternational/SupportingRight-To-LeftLanguages/SupportingRight-To-LeftLanguages.html#//apple_ref/doc/uid/10000171i-CH17) .

## <a name="additional-framework-changes"></a>Cambios adicionales en el marco de trabajo

Además de los cambios más importantes que se han descrito anteriormente, Apple ha realizado modificaciones y mejoras en varios marcos de trabajo existentes para iOS 9, entre los que se incluyen los siguientes:

- Marco de trabajo de AV Foundation
- Marco de AVKit
- Marco de CloudKit
- Marco de trabajo
- Marco de entrega
- Marco de HealthKit
- Marco de HomeKit
- Marco de autenticación local
- Marco de MapKit
- Marco de PassKit
- Marco de servicios Safari
- Marco de UIKit

Para obtener más información, consulte la documentación [adicional sobre los cambios en el marco de iOS 9](~/ios/platform/introduction-to-ios9/additional-framework-changes.md) .

## <a name="deprecated-apis-and-functions"></a>Funciones y API en desuso

Apple ha dejado de usar las siguientes API y funciones en iOS 9:

- **Libreta de direcciones & interfaz de usuario** de la libreta de direcciones: estas API se han sustituido por los marcos de IU Contact y Contact UI. Para obtener más información, consulte nuestra documentación de la interfaz de usuario de contactos [y contactos](~/ios/platform/contacts.md) .
- **CBCentralManager** : `RetrievePeripherals` se han `RetrieveConnectedPeripherals` quitado los métodos y de la `CBCentralManager` clase en iOS 9. Llamar a estos métodos hará que una aplicación se bloquee al emparejar un accesorio o el inicio de la aplicación.
- **FetchAllChanges** : el `FetchAllChanges` de la `CKFetchRecordChangesOperation` clase se devolvió y se quitará en iOS 9.
- **Media Player** : el marco de Media Player está en desuso en iOS 9. En su lugar, use las API AVKit o AV Foundation.

Para obtener una lista completa de las desuso específicas de la API, consulte la documentación de diferencias de la [API de iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/iOS90APIDiffs/index.html#//apple_ref/doc/uid/TP40016222) de Apple.

## <a name="ios-9-sample-apps"></a>Aplicaciones de ejemplo de iOS 9

Tenemos algunas [muestras específicas de iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9) para empezar:

- [AstroLayout](https://github.com/xamarin/monotouch-samples/tree/master/ios9/AstroLayout)
- [CollectionView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/CollectionView)
- [MetalPerformanceShadersHelloWorld](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-metalperformanceshadershelloworld)
- [MusicMotion](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-musicmotion)
- [Fotoprogreso](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-photoprogress)
- [SegueCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-seguecatalog)
- [StackView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StackView)
- [StickyCorners](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StickyCorners)

Consulte también las partes de iOS de estos ejemplos (Companion Mac OS X versiones disponibles):

- [AgentsCatalog](https://github.com/xamarin/mac-ios-samples/tree/master/AgentsCatalog)
- [MetalKitEssentials](https://github.com/xamarin/mac-ios-samples/tree/master/MetalKitEssentials)

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [Introducción a la función táctil 3D](~/ios/platform/3d-touch.md)
- [Seguridad de transporte de aplicación](~/ios/app-fundamentals/ats.md)
- [Multitarea para iPad](~/ios/platform/multitasking.md)
- [Interfaz de usuario de contactos y contactos](~/ios/platform/contacts.md)
- [Nuevas API de búsqueda](~/ios/platform/search/index.md)
- [Introducción a la vista de pila](~/ios/user-interface/controls/uistackview.md)
- [Cambios en la vista de colección](~/ios/user-interface/controls/uicollectionview.md)
- [Mejoras en los juegos](~/ios/platform/gaming/index.md)
- [Introducción a HomeKit](~/ios/platform/homekit.md)
- [Introducción a la entrega](~/ios/platform/handoff.md)
- [Cambios adicionales en la plataforma iOS 9](~/ios/platform/introduction-to-ios9/additional-framework-changes.md)
- [Solución de problemas](~/ios/platform/introduction-to-ios9/troubleshooting.md)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [Novedades de iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Actualización de las aplicaciones de Xamarin. iOS a iOS9 (vídeo)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
