---
title: Información general sobre las características de la plataforma iOS
description: Este documento contiene vínculos a varias guías que describen las características introducidas en varias versiones de iOS y otras características de la plataforma iOS.
ms.prod: xamarin
ms.assetid: 9F6A27E5-8A87-ADE2-D1EF-5684E7B8C999
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 78f8c8d1340c6bb1063e59ad03485d8b9b807c58
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489003"
---
# <a name="ios-platform-features-overview"></a>Información general sobre las características de la plataforma iOS

En esta página se enumeran las versiones recientes de iOS y se resaltan algunos de los marcos de trabajo de Apple a los que puede acceder con Xamarin. iOS.

## <a name="ios-releases"></a>versiones de iOS

|  |  |
|-------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Introducción a iOS 13](~/ios/platform/ios13/index.md) | En este documento se describe Xamarin. iOS 13.|
| [Introducción a iOS 12](~/ios/platform/introduction-to-ios12/index.md) | En este documento se describen las características de iOS 12 que se pueden usar al compilar aplicaciones de Xamarin. iOS.|
| [Introducción a iOS 11](~/ios/platform/introduction-to-ios11/index.md) | En este documento se describen las características nuevas y actualizadas de iOS 11 y Xcode 9, como ARKit, Core ML, Core NFC, arrastrar y colocar, MapKit, PDFKit, SiriKit y Vision. Se vincula a las guías que describen cómo usar estas características con Xamarin. iOS. |
| [Introducción a iOS 10](~/ios/platform/introduction-to-ios10/index.md) | iOS 10 incluye varias API y servicios nuevos que le permiten desarrollar aplicaciones con nuevas características y funcionalidades. Con iOS 10, las aplicaciones tienen nuevas capacidades, como la extensión de mapas, mensajes, teléfono y Siri. En esta sección se muestra muestra para aprovechar las ventajas de estas características en una aplicación de Xamarin. iOS. |
| [Introducción a iOS 9](~/ios/platform/introduction-to-ios9/index.md)   | En esta sección se definen los cambios realizados en iOS 9 al actualizar desde iOS 8 y cómo usar estas características en una aplicación de Xamarin. iOS. |
| [Introducción a iOS 8](~/ios/platform/introduction-to-ios8.md)         | iOS 8 realizó un gran número de cambios en el sistema operativo desde iOS 7. Aquí se muestra lo que son y cómo se usan. |
| [Introducción a iOS 7](~/ios/platform/introduction-to-ios7/index.md)   | Acerca de las nuevas API principales introducidas en iOS 7, incluidas las transiciones de los controladores de vistas, mejoras en las animaciones de UIView, UIKit Dynamics y el kit de texto. |
| [Introducción a iOS 6](~/ios/platform/introduction-to-ios6/index.md)   | Explicaciones de las características introducidas en iOS 6, incluidas las vistas de colección, el kit de Pass, el kit de eventos y el marco social. |

## <a name="apple-payiosplatformapple-paymd"></a>[Apple Pay](~/ios/platform/apple-pay.md)

Apple Pay se presentó junto con iOS 8, lo que permite a los usuarios pagar por bienes físicos como alimentos, ocio y pertenencias a través de sus dispositivos iOS. Está disponible en iPhone 6 y iPhone 6 Plus, y también se puede emparejar con el Apple Watch para las compras en la tienda. Cuando se usa en un iPhone, usa Touch ID como una manera de confirmar y autorizar las transacciones a la tarjeta de crédito o débito de un usuario.

## <a name="callkitiosplatformcallkitmd"></a>[CallKit](~/ios/platform/callkit.md)

La nueva API de CallKit en iOS 10 proporciona una manera para que las aplicaciones de VOIP se integren con la interfaz de usuario de iPhone y proporcionen una interfaz y una experiencia familiares al usuario final. Con esta API, los usuarios pueden ver las llamadas VOIP e interactuar con ellas desde la pantalla de bloqueo del dispositivo iOS y administrar contactos con las vistas **Favoritos** y **recientes** de la aplicación de teléfono.

## <a name="contacts-and-contactsuiiosplatformcontactsmd"></a>[Contacts y ContactsUI](~/ios/platform/contacts.md)

Con la introducción de iOS 9, Apple ha publicado dos nuevos marcos de trabajo, `Contacts` y `ContactsUI`, que reemplazan los marcos de interfaz de usuario existentes de libreta de direcciones y de libreta de direcciones usados por iOS 8 y versiones anteriores.

## <a name="document-pickeriosplatformdocument-pickermd"></a>[Selector de documentos](~/ios/platform/document-picker.md)

El selector de documentos permite que los documentos se compartan entre las aplicaciones. Estos documentos pueden almacenarse en iCloud o en otro directorio de la aplicación. Los documentos se comparten mediante el conjunto de [extensiones de proveedor de documentos](~/ios/platform/extensions.md) que el usuario ha instalado en su dispositivo.

## <a name="eventkitiosplatformeventkitmd"></a>[EventKit](~/ios/platform/eventkit.md)

iOS tiene dos aplicaciones relacionadas con el calendario integradas: la aplicación de calendario y la aplicación de recordatorios. Es lo suficientemente sencillo para comprender cómo la aplicación de calendario administra los datos de calendario, pero la aplicación de recordatorios es menos obvia. En realidad, los recordatorios pueden tener fechas asociadas en cuanto a su vencimiento, cuando se completan, etc. Como tal, iOS almacena todos los datos de calendario, ya sean eventos de calendario o recordatorios, en una ubicación, denominada *base de datos de calendario*.

## <a name="ios-extensionsiosplatformextensionsmd"></a>[extensiones de iOS](~/ios/platform/extensions.md)

Las extensiones, como se introdujeron en iOS 8, son `UIViewControllers` especializadas que se presentan en iOS dentro de contextos estándar, como en el **centro de notificaciones**, como tipos de teclado personalizados solicitados por el usuario para realizar entradas especializadas u otros contextos, como editar una fotografía en la que la extensión puede proporcionar filtros de efectos especiales.

## <a name="graphics-and-animation-in-iosiosplatformgraphics-animation-iosindexmd"></a>[Gráficos y animación en iOS](~/ios/platform/graphics-animation-ios/index.md)

Gráficos y animación en iOS trata conceptos básicos sobre gráficos en iOS como CoreImage, Core Graphics y Core Animation.

## <a name="handoffiosplatformhandoffmd"></a>[Handoff](~/ios/platform/handoff.md)

Apple presentó la entrega en iOS 8 y OS X Yosemite (10,10) para proporcionar un mecanismo común para que el usuario transfiera las actividades iniciadas en uno de sus dispositivos, a otro dispositivo que ejecute la misma aplicación u otra aplicación que admita la misma actividad.

## <a name="healthkitiosplatformhealthkitmd"></a>[HealthKit](~/ios/platform/healthkit.md)

El kit de mantenimiento proporciona un almacén de datos seguro para la información relacionada con el estado del usuario. Las aplicaciones del kit de mantenimiento pueden, con el permiso explícito del usuario, leer y escribir en este almacén de datos y recibir notificaciones cuando se agregan los datos pertinentes. Las aplicaciones pueden presentar los datos o el usuario puede usar la aplicación de mantenimiento proporcionada por Apple para ver un panel de todos sus datos.

## <a name="homekitiosplatformhomekitmd"></a>[HomeKit](~/ios/platform/homekit.md)

Apple presentó HomeKit en iOS 8 para proporcionar un marco común para detectar y comunicarse con dispositivos de automatización de inicio en el hogar de un usuario. HomeKit proporciona una plataforma común para la configuración de dispositivos y la configuración de acciones para controlarlos.

## <a name="in-app-purchasingiosplatformin-app-purchasingindexmd"></a>[Compras desde la aplicación](~/ios/platform/in-app-purchasing/index.md)

las aplicaciones de iOS pueden vender productos o servicios digitales mediante StoreKit: un conjunto de API proporcionadas por iOS que se comunican con los servidores de Apple para realizar transacciones financieras con el usuario a través de su identificador de Apple. Las API de StoreKit están preocupadas principalmente por la recuperación de información del producto y la realización de transacciones, ya que no hay ningún componente de la interfaz de usuario. Las aplicaciones que implementan la compra desde la aplicación deben crear su propia interfaz de usuario y realizar un seguimiento de los elementos adquiridos con código personalizado para proporcionar al usuario los productos o servicios necesarios.

## <a name="ios-gaming-apisiosplatformgamingindexmd"></a>[API de juegos de iOS](~/ios/platform/gaming/index.md)

Apple ha realizado varias mejoras tecnológicas en las API de juegos de iOS 9 que facilitan la implementación de gráficos y audio de juegos en una aplicación de Xamarin. iOS. Entre ellas se incluyen la facilidad de desarrollo a través de los marcos de trabajo de alto nivel y el aprovechamiento de la capacidad de la GPU del dispositivo iOS para mejorar la velocidad y las capacidades gráficas.

## <a name="message-app-integrationiosplatformmessage-app-integrationindexmd"></a>[Integración de aplicaciones de mensajes](~/ios/platform/message-app-integration/index.md)

Novedad de iOS 10, una extensión de aplicación de mensaje se integra con la aplicación de **mensajes** y presenta una nueva funcionalidad al usuario. La extensión puede enviar texto, adhesivos, archivos multimedia y mensajes interactivos.

## <a name="multitasking-for-ipadiosplatformmultitaskingmd"></a>[Multitarea para iPad](~/ios/platform/multitasking.md)

iOS 9 agrega compatibilidad con la multitarea para ejecutar dos aplicaciones al mismo tiempo en hardware de iPad específico. Se admite la multitarea para iPad a través de las siguientes características: desplazarse por la vista en dos paneles & imagen de la imagen.

## <a name="passkitiosplatformpasskitmd"></a>[PassKit](~/ios/platform/passkit.md)

Passbook es una aplicación para iPhone y el toque de iPod con iOS 6. Almacena y muestra códigos de barras y otra información para vincular las transacciones del cliente en su teléfono con el "mundo real". Los comerciantes generan las pasadas y se envían al cliente por correo electrónico, direcciones URL o desde dentro de la propia aplicación iOS de un comerciante. Passbook almacena y organiza todas las pasadas en un teléfono, y muestra los recordatorios de la pantalla de bloqueo en función de la fecha y la hora o la ubicación del dispositivo.

En este documento se presenta passbook, mediante la API de Pass kit con Xamarin. iOS, y se explica cómo implementar las pasadas en el servidor.

## <a name="photokitiosplatformphotokitmd"></a>[PhotoKit](~/ios/platform/photokit.md)

Photo kit es un nuevo marco que permite que las aplicaciones consulten la biblioteca de imágenes del sistema y creen interfaces de usuario personalizadas para ver y modificar su contenido. Incluye varias clases que representan recursos de imagen y vídeo, así como colecciones de recursos como álbumes y carpetas.

## <a name="request-app-reviewiosplatformrequest-app-reviewmd"></a>[Solicitar revisión de la aplicación](~/ios/platform/request-app-review.md)

Como novedad de iOS 10,3, el método de `RequestReview()` permite que una aplicación de iOS solicite al usuario que califique o revise. Cuando se llama a este método en una aplicación de envío que el usuario ha instalado desde la tienda de aplicaciones, iOS 10 tratará todo el proceso de evaluación y valoración del desarrollador. Dado que este proceso se rige por la Directiva de App Store, es posible que se muestre una alerta.

## <a name="search-apisiosplatformsearchindexmd"></a>[API de búsqueda](~/ios/platform/search/index.md)

La búsqueda se ha ampliado en iOS 9 para proporcionar nuevas formas de acceder a la información y las características dentro de una aplicación de Xamarin. iOS. Con las nuevas API de búsqueda de aplicaciones, se pueden realizar búsquedas en el contenido de la aplicación a través de los resultados de búsqueda de Spotlight y de Safari, los recordatorios y las sugerencias de entrega y Siri. Esto permite a los usuarios acceder rápidamente a las actividades y a la información en la aplicación.

## <a name="sirikitiosplatformsirikitindexmd"></a>[SiriKit](~/ios/platform/sirikit/index.md)

Novedad de iOS 10, SiriKit permite que una aplicación de iOS proporcione servicios que son accesibles para el usuario mediante Siri y la aplicación Maps en un dispositivo iOS con las extensiones de aplicación y **los nuevos marcos** de interfaz de usuario de intents y **intents** .

## <a name="social-frameworkiosplatformsocial-frameworkmd"></a>[Marco de redes sociales](~/ios/platform/social-framework.md)

El marco de redes sociales proporciona una API unificada para interactuar con redes sociales, como _Twitter_ y _Facebook_, así como _SinaWeibo_ para los usuarios de China.

## <a name="speech-recognitioniosplatformspeechmd"></a>[Reconocimiento de voz](~/ios/platform/speech.md)

iOS 10 incluye un nuevo Speech API que permite que la aplicación admita el reconocimiento de voz continuo y la conversión de voz (desde secuencias de audio en directo o grabadas) en texto.

## <a name="textkitiosplatformtextkitmd"></a>[TextKit](~/ios/platform/textkit.md)

El kit de texto es una nueva API que ofrece características eficaces de diseño y representación de texto. Se basa en el marco de texto básico de nivel inferior, pero es mucho más fácil de usar que el texto principal.

## <a name="3d-touchiosplatform3d-touchmd"></a>[Entrada táctil 3D](~/ios/platform/3d-touch.md)

En este artículo se proporciona una introducción al uso de las nuevas API de toque 3D para agregar gestos sensibles a la presión a las aplicaciones de Xamarin. iOS que se ejecutan en los nuevos dispositivos iPhone 6S y iPhone 6S Plus.

## <a name="touch-id-and-face-id-with-xamariniosiosplatformtouch-id-face-idmd"></a>[Touch ID y facial ID con Xamarin. iOS](~/ios/platform/touch-id-face-id.md)

Touch ID y facial ID son sistemas de autenticación biométricos disponibles desde iOS 8. En este artículo y en el ejemplo se describe cómo usar Touch ID y facial ID con Xamarin. iOS.

## <a name="user-notificationsiosplatformuser-notificationsindexmd"></a>[Notificaciones de usuario](~/ios/platform/user-notifications/index.md)

Como novedad de iOS 10, el marco de trabajo de notificaciones de usuario permite la entrega y el control de las notificaciones locales y remotas. Con este marco de trabajo, la extensión de aplicación o aplicación puede programar la entrega de notificaciones locales especificando un conjunto de condiciones como la ubicación o la hora del día.

## <a name="wide-coloriosplatformwide-colormd"></a>[Color amplio](~/ios/platform/wide-color.md)

iOS 10 y macOS Sierra mejora la compatibilidad con los formatos de píxeles de rango extendido y los espacios de colores de gama extensa en todo el sistema, incluidos marcos como gráficos principales, imagen básica, metal y AVFoundation. La compatibilidad con dispositivos con pantallas de color ancho se facilita aún más proporcionando este comportamiento en toda la pila de gráficos.

## <a name="binding-objective-cbinding-objective-cindexmd"></a>[Enlace de Objective-C](binding-objective-c/index.md)

Cuando se trabaja en iOS, pueden darse casos en los que se desea consumir una biblioteca de Objective-C de terceros. En esas situaciones, puede usar los proyectos de enlace de MonoTouch para crear C# un enlace a las bibliotecas nativas de Objective-C. El proyecto usa las mismas herramientas que usamos para traer las API de iOS C#. En este documento se describe cómo enlazar API de Objective-C.

## <a name="referencing-native-librariesnative-interopmd"></a>[Referencia a bibliotecas nativas](native-interop.md)

Xamarin. iOS admite la vinculación con bibliotecas nativas de C y con las bibliotecas de Objective-C. En este documento se describe cómo vincular las bibliotecas nativas de C con el proyecto de Xamarin. iOS.

## <a name="embedded-frameworksembedded-frameworksmd"></a>[Plataformas admitidas](embedded-frameworks.md)

Explica cómo insertar marcos de usuario de Objective-C en aplicaciones de Xamarin. iOS.
