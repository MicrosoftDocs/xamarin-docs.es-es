---
title: Introducción a iOS 10
description: En este artículo se presentan todas las API y características nuevas y modificadas disponibles en iOS 10 para desarrolladores de Xamarin. iOS.
ms.prod: xamarin
ms.assetid: FB91DFFE-CF5E-4253-92CB-78A6371259D9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: edc585364df2e0b2129135e7bf5977c33a77a6e0
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647424"
---
# <a name="introduction-to-ios-10"></a>Introducción a iOS 10

Con el nuevo SDK de iOS 10, Apple ha incluido nuevas API y servicios que permiten al desarrollador crear nuevas categorías de aplicaciones y características. Ahora, una aplicación de iOS puede ampliar las aplicaciones de mensajes, Siri, Phone y Maps para proporcionar una funcionalidad enriquecida y atractiva al usuario final que anteriormente no estaba disponible.

Para más información sobre iOS 10, consulte la documentación de [iOS y aplicaciones](https://developer.apple.com/ios/) de Apple.

## <a name="whats-new-in-ios-10"></a>Novedades de iOS 10

Apple ha agregado varias API y servicios nuevos en iOS 10 junto con muchas mejoras en las características existentes, entre las que se incluyen:

## <a name="adapting-to-the-true-tone-display"></a>Adaptación a la pantalla de tono verdadero

La tecnología de visualización de tono verdadero de Apple usa el sensor de luz ambiente en un dispositivo iOS para ajustar de forma dinámica el color y la intensidad de la pantalla para que coincida con las condiciones de iluminación actuales. iOS 10 proporciona la nueva clave [UIWhitePointAdaptivityStyle](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW31) que se puede Agregar al archivo de `Info.plist` la aplicación y controla el modo en que el tono real aplica el cambio de color estándar. 

Están disponibles los siguientes valores:

- `UIWhitePointAdaptivityStyleStandard`**Predeterminado** : Use el adaptivity de punto blanco estándar.
- `UIWhitePointAdaptivityStyleReading`: Se usa para aplicaciones con el foco de lectura.
- `UIWhitePointAdaptivityStyleGame`: Se usa para aplicaciones centradas en juegos.
- `UIWhitePointAdaptivityStyleVideo`: Se usa para aplicaciones centradas en vídeo.
- `UIWhitePointAdaptivityStylePhoto`: Se usa para aplicaciones centradas en la fotografía en las que la fidelidad de color es más importante que los ajustes de punto blanco del entorno.

## <a name="app-extensions"></a>Extensiones de aplicaciones

Apple ha proporcionado varios puntos de extensión de aplicación nuevos en iOS 10:

- Directorio de llamadas
- Interfaz de usuario de intenciones e intents
- Mensajes
- Contenido de notificación
- Notification Services
- Paquete de adhesivo

Además, las extensiones de aplicación de teclado de terceros tienen las siguientes mejoras:

- La nueva `DocumentInputMode` propiedad de la `UITextDocumentProxy` clase puede determinar el idioma de entrada de un documento y permitir que la extensión de teclado se alinee con ese idioma.
- El nuevo `HandleInputModeList` método permite que la extensión de teclado muestre el menú de selector de teclado del sistema en respuesta a la tecla de globo terráqueo que se está punteando.

Para obtener más información, consulte la [Introducción a las extensiones](~/ios/platform/extensions.md), la [integración de aplicaciones de mensajes](~/ios/platform/message-app-integration/index.md), [Introducción a las sugerencias](~/ios/platform/search/proactive-suggestions.md)proactivas, [Introducción a SiriKit](~/ios/platform/sirikit/index.md), [Introducción a](~/ios/platform/user-notifications/index.md) las notificaciones de usuario y Apple [ Guía de programación de extensiones de aplicaciones](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214).

## <a name="app-search-enhancements"></a>Mejoras en las búsquedas de aplicaciones

Los principales avances en iOS 10 proporcionan varias mejoras en la búsqueda de aplicaciones, como:

- **Micromecenazgo popularidad de vínculos profundos (con privacidad diferencial)** : proporciona una manera de promover el contenido de una aplicación vinculada en profundidad en los resultados de la búsqueda.
- **Búsqueda en la aplicación** : Use la nueva `CSSearchQuery` clase para proporcionar una capacidad de búsqueda de contenido destacado en la aplicación similar a la forma en que funcionan las aplicaciones mail, messages y Notes.
- **Continuación** de la búsqueda: permite que un usuario inicie una búsqueda en Spotlight o Safari, abra una aplicación y continúe con la búsqueda.
- **Visualización de los resultados de la validación** : la herramienta de validación de la [API de búsqueda de aplicaciones](https://search.developer.apple.com/appsearch-validation-tool) de Apple ahora muestra una representación visual del marcado de un sitio web y de la vinculación profunda al realizar pruebas.
- **Uso compartido de imágenes de aplicación de mensajes** : permite que las imágenes de la aplicación populares que se proporcionan para compartir mensajes (a través de una extensión de aplicación de mensajes) aparezcan en búsquedas de Spotlight.

Para obtener más información, consulte nuestra guía de mejoras en la [búsqueda de aplicaciones](~/ios/platform/search/app-search-enhancements.md) .

## <a name="apple-pay-enhancements"></a>Apple Pay mejoras

Apple ha realizado varias mejoras en Apple Pay en iOS 10 que permiten al usuario realizar pagos seguros desde sitios web y a través de la interacción con Siri y Maps.

Con iOS 10, se han agregado varias API nuevas que funcionan con iOS y watchos para admitir redes de pago dinámico y un nuevo entorno de prueba de espacio aislado.

Además, el marco de trabajo de PassKit se ha ampliado para admitir `UIKit` Apple Pay fuera de y para permitir que los emisores de tarjetas presenten sus tarjetas en sus aplicaciones.

Para obtener más información, consulte nuestra guía de [Apple Pay mejoras](~/ios/platform/apple-pay.md) .

## <a name="alternate-app-icons"></a>Iconos de aplicación alternativos

Apple ha agregado varias mejoras a iOS 10,3 que permiten a una aplicación administrar su icono:

- `ApplicationIconBadgeNumber`: Obtiene o establece el distintivo del icono de la aplicación en el Springboard.
- `SupportsAlternateIcons`: Si `true` la aplicación tiene un conjunto alternativo de iconos.
- `AlternateIconName`: Devuelve el nombre del icono alternativo seleccionado actualmente o `null` si se usa el icono principal.
- `SetAlternameIconName`: Use este método para cambiar el icono de la aplicación al icono alternativo determinado.

Para obtener más información, consulte nuestra guía [alternativa de iconos de aplicación](~/ios/app-fundamentals/images-icons/alternate-app-icons.md) .

## <a name="introduction-to-callkit"></a>Introducción a CallKit

La nueva API de CallKit en iOS 10 proporciona una manera para que las aplicaciones de VOIP se integren con la interfaz de usuario de iPhone y proporcionen una interfaz y una experiencia familiares al usuario final. Con esta API, los usuarios pueden ver las llamadas VOIP e interactuar con ellas desde la pantalla de bloqueo del dispositivo iOS y administrar contactos con las vistas **Favoritos** y **recientes** de la aplicación de teléfono.

Además, la API de CallKit ofrece la posibilidad de crear extensiones de aplicación que pueden asociar un número de teléfono con un nombre (identificador de llamada) o indicar al sistema Cuándo debe bloquearse un número (bloqueo de llamadas).

Para obtener más información, consulte la guía [de introducción a Callkit](~/ios/platform/callkit.md) .

## <a name="message-app-integration"></a>Integración de aplicaciones de mensajes

iOS 10 permite la inclusión de una extensión de aplicación de mensajes en la solución de Xamarin. iOS que se integra con la aplicación de **mensajes** y presenta una nueva funcionalidad al usuario. La extensión puede enviar texto, adhesivos, archivos multimedia y mensajes interactivos. Hay disponibles dos tipos de extensiones de aplicación de mensajes:

- **Paquetes** de adhesivos: contiene una colección de adhesivos que el usuario puede Agregar a un mensaje. Los paquetes de adhesivos se pueden crear sin necesidad de escribir ningún código.
- **aplicación IMessage** : puede presentar una interfaz de usuario personalizada dentro de la aplicación mensajes para seleccionar adhesivos, escribir texto, incluidos archivos multimedia (con conversiones de tipo opcionales) y crear, editar y enviar mensajes de interacción.

Para obtener más información, consulte la guía de [integración de aplicaciones de mensajes](~/ios/platform/message-app-integration/index.md) .

## <a name="news-publisher-enhancements"></a>Mejoras del editor de noticias

Con iOS 10, Apple permitirá a cualquier usuario de los principales revistas y a las nuevas organizaciones, a los bloggers y a los editores independientes registrarse y enviar contenido a la aplicación Apple News. Para obtener más información, consulte la documentación sobre [los recursos de noticias](https://newsresources.apple.com/) de Apple.

## <a name="providing-haptic-feedback"></a>Provisión de comentarios hápticos

En iPhone 7 y iPhone 7 Plus, Apple ha incluido nuevas respuestas hápticos que proporcionan maneras adicionales de atraer físicamente al usuario. Use las nuevas opciones de comentarios táctiles para obtener la atención del usuario y reforzar sus acciones.

Varios elementos integrados de la interfaz de usuario ya proporcionan comentarios hápticos, como los selectores, los conmutadores y los controles deslizantes. iOS 10 ahora agrega la capacidad de desencadenar los hápticos mediante programación con una subclase concreta de `UIFeedbackGenerator` la clase.

Para obtener más información, vea nuestra guía de [comentarios de hápticos](~/ios/user-interface/ios-ui/haptic-feedback.md) .

## <a name="proactive-suggestions"></a>Sugerencias proactivas

iOS 10 presenta nuevas formas de impulsar la interacción con una aplicación, ya que permite que el sistema presente de forma proactiva información útil al usuario en los momentos adecuados. Del mismo modo que iOS 9 proporcionó la capacidad de agregar una búsqueda en profundidad a la aplicación mediante las sugerencias de Spotlight, de entrega y Siri, con iOS 10 una aplicación puede exponer la funcionalidad que el sistema puede presentar al usuario desde las siguientes ubicaciones:

- El conmutador de aplicación
- La pantalla de bloqueo
- CarPlay
- Asignaciones
- Interacciones de Siri
- Sugerencias de QuickType

Una aplicación expone esta funcionalidad al sistema mediante una colección de tecnologías como [NSUserActivity](xref:Foundation.NSUserActivity), marcado Web, Spotlight principal, MapKit, Media Player y UIKit.

Para obtener más información, consulte la guía [de introducción a las sugerencias](~/ios/platform/search/proactive-suggestions.md) proactivas.

## <a name="request-app-review"></a>Solicitud de revisión de aplicaciones

Novedad de iOS 10,3, el `RequestReview()` método permite que una aplicación de iOS solicite al usuario que califique o revise. Aunque se puede llamar a este método en cualquier punto en el que tenga sentido en la experiencia del usuario, el proceso de revisión se rige y controla mediante la Directiva de App Store. Como resultado, este método puede o no mostrar una alerta y nunca debe llamarse como respuesta a una acción del usuario, como pulsar un botón.

Para obtener más información, consulte nuestra guía de [solicitud de revisión](~/ios/platform/request-app-review.md) de la aplicación.

## <a name="security-and-privacy-enhancements"></a>Mejoras en la seguridad y la privacidad

Apple ha realizado varias mejoras en la seguridad y la privacidad en iOS 10 que le ayudarán a los desarrolladores a mejorar la seguridad de sus aplicaciones y a garantizar la privacidad del usuario final.

Como resultado, las aplicaciones que se ejecutan en iOS 10 (o posterior) deben declarar estáticamente su intención de acceder a características específicas o información de usuario especificando una o más `Info.plist` claves específicas de la privacidad en sus archivos que expliquen al usuario por qué la aplicación quiere obtener acceso.

Para obtener más información, consulte nuestra guía de [mejoras de seguridad y privacidad](~/ios/app-fundamentals/security-privacy.md) .

## <a name="sirikit"></a>SiriKit

Novedad de iOS 10, SiriKit permite que una aplicación de Xamarin. iOS proporcione servicios que son accesibles para el usuario mediante Siri en un dispositivo iOS. Esta funcionalidad se proporciona en una o más extensiones de aplicación mediante los **nuevos marcos** de **interfaz de usuario** de intents y intents.

SiriKit admite los siguientes dominios de servicio:

- Llamada de audio o vídeo.
- Reservar un viaje.
- Administrar entrenamientos.
- Correo.
- Buscar fotografías.
- Enviar o recibir pagos.

Cuando el usuario realiza una solicitud de Siri que implica a uno de los servicios de la extensión de la aplicación, SiriKit envía a la extensión un objeto de **intención** que describe la solicitud del usuario junto con los datos auxiliares. A continuación, la extensión de la aplicación genera el objeto de **respuesta** adecuado para el **objetivo**dado, lo que detalla cómo la extensión puede controlar la solicitud.

Aunque Siri normalmente controla toda la interacción del usuario, la extensión de la aplicación puede usar el marco de trabajo de **interfaz** de usuario para presentar una interfaz de usuario enriquecida y personalizada que incluye la personalización de marca y la información adicional de la aplicación.

Para obtener más información, consulte la guía [de introducción a SiriKit](~/ios/platform/sirikit/index.md) .

## <a name="speech-recognition"></a>Reconocimiento de voz

iOS 10 incluye un nuevo Speech API que permite que la aplicación admita el reconocimiento de voz continuo y la conversión de voz (desde secuencias de audio en directo o grabadas) en texto.

Dado que el reconocimiento de voz requiere la transmisión y el almacenamiento temporal de los datos en los servidores de Apple, la aplicación _debe_ solicitar el permiso del usuario `NSSpeechRecognitionUsageDescription` para realizar el `Info.plist` reconocimiento incluyendo la clave en su archivo y llamando al `SFSpeechRecognizer.RequestAutorization` método.

Para obtener más información, consulte la guía [de introducción a reconocimiento de voz](~/ios/platform/speech.md) .

## <a name="user-notifications"></a>Notificaciones de usuario

Como novedad de iOS 10, el marco de trabajo de notificaciones de usuario permite la entrega y el control de las notificaciones locales y remotas. Con este marco de trabajo, la extensión de aplicación o aplicación puede programar la entrega de notificaciones locales especificando un conjunto de condiciones como la ubicación o la hora del día.

Además, la aplicación o la extensión pueden recibir (y potencialmente modificar) las notificaciones locales y remotas, ya que se entregan al dispositivo iOS del usuario.

El marco de trabajo de la interfaz de usuario de notificaciones de usuario permite personalizar la apariencia de las notificaciones locales y remotas cuando se presentan al usuario.

Para obtener más información, consulte la guía de la plataforma de notificaciones de [usuario](~/ios/platform/user-notifications/index.md) .

## <a name="video-subscriber-account"></a>Cuenta de suscriptor de vídeo

Novedad de iOS 10, el marco de cuentas del suscriptor de vídeo permite que las aplicaciones que admiten streaming autenticado o vídeo bajo demanda se autentiquen con su proveedor de TV por cable o satélite usando una experiencia de inicio de sesión único para el usuario final.

## <a name="wide-color"></a>Color amplio

iOS 10 amplía la compatibilidad con los formatos de píxeles de rango extendido y los espacios de colores de amplia gama en todo el sistema, incluidos marcos como gráficos principales, imagen básica, metal y AVFoundation. La compatibilidad con dispositivos con pantallas de color ancho se facilita aún más proporcionando este comportamiento en toda la pila de gráficos.

Además, [UIKit](xref:UIKit) se ha modificado para que funcione en el nuevo **sRGB** ColorSpace extendido, lo que facilita la combinación de colores en gamas de colores anchos sin pérdida significativa de rendimiento.

Apple ofrece las siguientes prácticas recomendadas al trabajar con colores anchos:

- [UIColor](xref:UIKit.UIColor) ahora usa el espacio de color sRGB y dejará de fijar los valores `0.0` en `1.0` el intervalo to. Si la aplicación se basa en el comportamiento anterior de la abrazadera, deberá modificarla para iOS 10.
- El entorno de dibujo se configurará para el espacio de color sRGB `UIView` al realizar dibujos personalizados en un dispositivo iPad Pro.
- Si la aplicación realiza una representación personalizada de `UIImages`, use la nueva clase [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) para especificar el uso de los formatos de intervalo extendido o de intervalo estándar.
- Al usar una API de bajo nivel como gráficos principales o metal para proporcionar el procesamiento de imágenes, el desarrollador debe usar un espacio de colores y un formato de píxel extendidos que admita valores de punto flotante de 16 bits. Cuando sea necesario, el desarrollador tendrá que fijar manualmente los valores de los componentes de color.
- Los gráficos principales, los sombreadores de imagen principal y de rendimiento de metal proporcionan nuevos métodos para la conversión entre los dos espacios de color.

Para obtener más información, consulte la guía [de introducción a la amplia gama de colores](~/ios/platform/wide-color.md) .

## <a name="widget-enhancements"></a>Mejoras de widgets

Apple ha introducido varias mejoras en el sistema de widgets para asegurarse de que los widgets tengan un aspecto excelente sobre cualquier fondo que exista en la nueva pantalla de bloqueo de iOS 10. La propiedad [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) está en desuso y se ha reemplazado por las nuevas propiedades [WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect) o [WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect) . Además, los widgets ahora contienen una propiedad [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) que permite al desarrollador describir la cantidad de contenido disponible y permite al usuario expandir y contraer el contenido.

Para obtener más información, consulte la guía de [mejoras del widget de pantalla principal y de búsqueda](~/ios/platform/search/widgets.md) .

## <a name="additional-framework-changes"></a>Cambios adicionales en el marco de trabajo

Además de los principales cambios en el marco de trabajo y las adiciones enumeradas anteriormente, Apple ha realizado muchos cambios adicionales en el marco de trabajo de iOS 10.

Para obtener más información, consulte nuestra guía [adicional de cambios](~/ios/platform/introduction-to-ios10/additional-framework-changes.md) en el marco de trabajo.

## <a name="deprecated-apis"></a>Interfaces API desusadas

Las siguientes API están desusadas en iOS 10:

- Las `CKDiscoverAllContactsOperation`clases `CKDiscoveredUserInfo` ,y`CKFetchRecordChangesOperation`han quedado en desuso en CloudKit para iOS 10. `CKDiscoverUserInfosOperation` En su lugar, use las clases [CKDiscoverAllUserIdentitiesOperation](xref:CloudKit.CKDiscoverUserIdentitiesOperation), [CKUserIdentity](xref:CloudKit.CKUserIdentity) y [CKFetchRecordZoneChangesOperation](xref:CloudKit.CKFetchRecordZoneChangesOperation) (que admiten el uso compartido de registros).
- Varias API de [CKSubscription](https://developer.apple.com/reference/cloudkit/cksubscription) (como las suscripciones basadas en zonas y basadas en consultas) han quedado en desuso. Use las API [CKRecordZoneSubscription](xref:CloudKit.CKRecordZoneSubscription) y [CKQuerySubscription](xref:CloudKit.CKQuerySubscription) en su lugar.
- Los símbolos de [NSPersistentStoreCoordinator](xref:CoreData.NSPersistentStoreCoordinator) relacionados con el contenido omnipresente están desusados.
- `ADBannerView`, `ADInterstitialAd` y los símbolos relacionados en la clase [UIViewController](xref:UIKit.UIViewController) están desusados.
- Los símbolos de [SKUniform](https://developer.apple.com/reference/spritekit/skuniform) relacionados con los valores de punto flotante están desusados.
- Las `UILocalNotification`clases `UIMutableUserNotificationAction`,, `UIMutableUserNotificationCategory`, ydeUIKithan`UIUserNotificationCategory`quedado endesuso.`UIUserNotificationAction` `UIUserNotificationSettings` Use el marco de trabajo de notificaciones de [usuario](#user-notifications) en su lugar.
- Los `HandleActionForLocalNotification`métodos `HandleActionForRemoteNotification` ,y`DidReceiveRemoteNotification`WatchKit han quedado desusados. `DidReceiveLocalNotification` Use los `HandleActionForNotification` métodos `DidReceiveNotification` y en su lugar.
- Los `DidReceiveLocalNotification` métodos `DidReceiveRemoteNotification` y de [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate) han quedado en desuso. Cree una instancia de [UNUserNotificationCenterDelegate](https://developer.apple.com/reference/usernotifications/unusernotificationcenterdelegate) que implemente los métodos adecuados y asígnela a la `Delegate` propiedad del objeto [UNUserNotificationCenter](https://developer.apple.com/reference/usernotifications/unusernotificationcenter) .
- La **aplicación Game Center** está en desuso y se ha quitado de iOS. Si la aplicación usa GameKit, _debe_ presentar su propia interfaz para mostrar las características de GameKit, como los marcadores, etc.

Consulte la documentación sobre las diferencias de la [API ios 9,3 de Apple para 10,0 IOS](https://developer.apple.com/library/prerelease/content/releasenotes/General/iOS10APIDiffs/index.html) para obtener una lista completa de las desusadas.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
