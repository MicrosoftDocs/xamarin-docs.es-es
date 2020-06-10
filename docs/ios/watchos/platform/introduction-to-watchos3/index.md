---
title: Introducción a watchOS 3
description: En este artículo se presentan todas las API y características nuevas y modificadas disponibles en watchos 3 para desarrolladores de Xamarin.
ms.prod: xamarin
ms.assetid: B8ABE1E1-8688-4262-BE66-A16813C2D671
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/07/2017
ms.openlocfilehash: abe9c95c889aed5258ea3a5367e05368ddb7c77f
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574176"
---
# <a name="introduction-to-watchos-3"></a>Introducción a watchOS 3

_En este artículo se presentan todas las API y características nuevas y modificadas disponibles en watchos 3 para desarrolladores de Xamarin._

En este documento se tratarán los siguientes temas:

- [Novedades de watchos 3](#Whats-New-in-watchOS-3)
  - [Apple Pay mejoras](#Apple-Pay-Enhancements) agrega compatibilidad con los pagos desde la aplicación en el Apple Watch.
  - Las [tareas en segundo plano](#Background-Tasks) proporcionan a la aplicación la capacidad de actualizar su información en segundo plano para que esté lista cuando el usuario la necesite.
  - Se han realizado [mejoras de complicaciones](#Complications-Enhancements) para watchos 3 que proporcionan nuevas características para las aplicaciones.
  - Los [marcos de trabajo recién disponibles](#Newly-Available-Frameworks) se han expuesto para las aplicaciones de watchos.
  - Las [sugerencias proactivas](#Proactive-Suggestions) permiten a la aplicación Mostrar información de forma proactiva al usuario.
  - Se han realizado varias [mejoras de seguridad y privacidad](#Security-and-Privacy-Enhancements) en watchos 3.
  - Las [instantáneas y el Dock](#Snapshots-and-Dock) proporcionan al usuario acceso rápido a las aplicaciones de App watchos.
  - Las [notificaciones de usuario](#User-Notifications) proporcionan a los usuarios notificaciones locales y remotas.
  - Se han realizado varias [mejoras del marco de conectividad de inspección](#Watch-Connectivity-Framework-Enhancements) en watchos 3.
  - Se han realizado varias [mejoras de WatchKit Framework](#WatchKit-Framework-Enhancements) en watchos 3.
  - Las mejoras de la [aplicación de entrenamiento](#Workout-App-Enhancements) ofrecen nuevas capacidades para el entrenamiento relacionado con las aplicaciones Apple Watch.
- Se han realizado cambios en el [marco de trabajo adicionales en los](#Additional-Framework-Changes) monitores 3.
- [API en desuso](#Deprecated-APIs) en watchos 3.

<a name="Whats-New-in-watchOS-3"></a>

## <a name="whats-new-in-watchos-3"></a>Novedades de watchos 3

Apple ha agregado varias API y servicios nuevos en watchos 3 junto con muchas mejoras en las características existentes, entre las que se incluyen:

<a name="Apple-Pay-Enhancements"></a>

## <a name="apple-pay-enhancements"></a>Apple Pay mejoras

En watchos 3, el marco de trabajo de PassKit se ha ampliado para permitir la compatibilidad con pagos desde la aplicación y seguros (de bienes y servicios físicos) para las aplicaciones que se ejecutan en el Apple Watch.

Use las nuevas clases [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) y [PKPaymentAuthorizationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate) para presentar y responder a una interfaz en la que el usuario puede autorizar las solicitudes de pago.

Para obtener más información, consulte nuestra guía de [Apple Pay mejoras](~/ios/watchos/platform/apple-pay.md) .

<a name="Background-Tasks"></a>

## <a name="background-tasks"></a>Tareas en segundo plano

watchos 3 presenta varias tareas en segundo plano que una aplicación puede usar para actualizar su información, lo que garantiza que tiene el contenido que necesita el usuario antes de abrirlo.

Están disponibles las siguientes tareas en segundo plano:

- **Actualización de la aplicación en segundo plano** : la tarea [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) permite que la aplicación actualice su estado en segundo plano. Normalmente esto incluirá otra tarea, como la descarga de contenido nuevo desde Internet mediante un [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession).
- **Actualización de instantánea de fondo** : la tarea [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) permite que la aplicación actualice su contenido y la interfaz de usuario antes de que el sistema tome una instantánea que se usará para rellenar el Dock.
- **Conectividad de inspección en segundo plano** : la tarea [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) se inicia para la aplicación cuando recibe datos de fondo del iPhone emparejado.
- **Sesión de URL en segundo plano** : la tarea [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) se inicia para la aplicación cuando una transferencia en segundo plano requiere autorización o se completa (correctamente o con errores).

Para obtener más información, consulte nuestra guía de [tareas en segundo plano](~/ios/watchos/platform/background-tasks.md) .

<a name="Complications-Enhancements"></a>

## <a name="complications-enhancements"></a>Mejoras en las complicaciones

Las complicaciones son pequeños elementos visuales que proporcionan información útil de un vistazo. En función de la superficie de inspección seleccionada, el usuario tiene la capacidad de personalizar una esfera de inspección con una o más complicaciones.

watchos 3 ofrece a la aplicación la posibilidad de crear una o más complicaciones para la aplicación de inspección, de modo que el usuario pueda acceder a su información de un vistazo desde una esfera de inspección.

Además, las complicaciones ofrecen las siguientes ventajas:

- El usuario puede iniciar rápidamente la aplicación pulsando en la complicación directamente desde una esfera de inspección.
- Tener una de las complicaciones de la aplicación en la esfera de inspección hace que el sistema mantenga la aplicación en un estado listo para iniciarse en el que intenta iniciar la aplicación en segundo plano, guárdela en memoria y le da tiempo adicional para actualizar.
- Se garantizan complicaciones al menos 50 actualizaciones de las inserciones al día.
- Cuando la aplicación incluye complicaciones, se incluirá en el Apple Watch Galería de caras.

En watchos 3, el marco de trabajo de ClockKit ahora incluye varias plantillas nuevas para las complicaciones extra grandes como [CLKComplicationTemplateExtraLargeColumnsText](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargecolumnstext) y [CLKComplicationTemplateExtraLargeRingImage](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargeringimage). Además, para crear texto localizable, use los nuevos métodos de la clase [CLKTextProvider](https://developer.apple.com/reference/clockkit/clktextprovider) .

Para obtener más información, consulte nuestra guía [de técnicas de interacción rápida para watchos 3](~/ios/watchos/platform/quick-interaction-techniques.md) .

<a name="Newly-Available-Frameworks"></a>

## <a name="newly-available-frameworks"></a>Marcos de trabajo recién disponibles

watchos 3 incluye varios marcos existentes de Apple que antes no estaban disponibles, como los siguientes:

- **SceneKit** : Use SceneKit para incluir modelos 3D en la interfaz de usuario de la aplicación de inspección, incluidas la mayoría de las características disponibles en otras plataformas, como la iluminación, el sombreado, la animación, la física y los sistemas de partículas. no se admiten el audio espacial 3D, los sombreadores de metal o metal personalizados, los filtros de imagen principal y los materiales basados físicamente.
- **SpriteKit** : Use SpriteKit para representar y animar los objetos Sprite en la interfaz de usuario de la aplicación de App Watch, incluidas la mayoría de las características disponibles en otras plataformas, como acciones, física, alumbrado y sistemas de partículas. no se admiten los filtros de audio espacial 3D, de reproducción de vídeo y de imagen principal.
- **AVFoundation** : para administrar y reproducir audio.
- **CloudKit** : para trasladar datos entre la aplicación de inspección y los contenedores de iCloud.
- **Core Audio** : para administrar tipos de datos para representar secuencias de audio, búferes complejos y valores de hora.
- **GameKit** : para crear juegos sociales.

<a name="Proactive-Suggestions"></a>

## <a name="proactive-suggestions"></a>Sugerencias proactivas

watchos 3 permite que la aplicación presente de forma proactiva información al usuario en contextos determinados. Para admitir esta característica, [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) ahora incluye la `MapItem` propiedad que permite que la aplicación proporcione información de ubicación para su uso posterior por parte de otras aplicaciones.

Para obtener más información, consulte la guía [de introducción a las sugerencias proactivas](~/ios/watchos/platform/proactive-suggestions.md) .

<a name="Security-and-Privacy-Enhancements"></a>

## <a name="security-and-privacy-enhancements"></a>Mejoras en la seguridad y la privacidad

Apple ha realizado varias mejoras en la seguridad y la privacidad en watchos 3 que le ayudarán a los desarrolladores a mejorar la seguridad de sus aplicaciones y a garantizar la privacidad del usuario final.

Como resultado, las aplicaciones que se ejecutan en watchos 3 (o posterior) deben declarar estáticamente su intención de acceder a características específicas o información de usuario especificando una o más claves específicas de la privacidad en sus `Info.plist` archivos que expliquen al usuario por qué la aplicación quiere obtener acceso.

Como watchos 3 comparte estos cambios con iOS 10, consulte nuestra guía de [mejoras de seguridad y privacidad](~/ios/app-fundamentals/security-privacy.md) de iOS 10 para obtener más información.

<a name="Snapshots-and-Dock"></a>

## <a name="snapshots-and-dock"></a>Instantáneas y Dock

En watchos 3, Apple ha agregado el Dock en el que los usuarios pueden anclar sus aplicaciones favoritas y acceder a ellas rápidamente. Cuando el usuario presiona el botón lateral en el Apple Watch, se mostrará una galería de instantáneas de aplicación ancladas. El usuario puede deslizar el dedo hacia la izquierda o la derecha para encontrar la aplicación deseada y, a continuación, puntear en la aplicación para iniciarla y reemplazar la instantánea por la interfaz de la aplicación en ejecución.

El sistema toma instantáneas de la interfaz de usuario de la aplicación de forma periódica y las usa para rellenar los documentos. watchos ofrece a la aplicación la oportunidad de actualizar su contenido y la interfaz de usuario antes de tomar esta instantánea.

Para obtener más información, consulte nuestra guía de [tareas en segundo plano](~/ios/watchos/platform/background-tasks.md) y la [referencia de WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) de Apple.

<a name="User-Notifications"></a>

## <a name="user-notifications"></a>Notificaciones de usuario

El marco de notificaciones de usuario introducido en watchos 3 admite la entrega de notificaciones locales y remotas al Apple Watch. Use este marco de trabajo para programar notificaciones basadas en condiciones específicas, como la hora del día o la ubicación, y para recibir y controlar las notificaciones.

Para obtener más información, consulte nuestra guía [de técnicas de interacción rápida para watchos 3](~/ios/watchos/platform/quick-interaction-techniques.md) .

<a name="Watch-Connectivity-Framework-Enhancements"></a>

## <a name="watch-connectivity-framework-enhancements"></a>Vea las mejoras del marco de conectividad

La nueva `HasContentPending` propiedad de la clase [WCSession](https://developer.apple.com/reference/watchconnectivity/wcsession) indica que la sesión ha recibido datos en segundo plano que deben procesarse. Y la `RemainingComplicationUserInfoTransfers` propiedad devuelve las horas restantes en las que la aplicación iOS puede actualizar su complicación de watchos.

Para obtener más información, consulte nuestra guía de [tareas en segundo plano](~/ios/watchos/platform/background-tasks.md) .

<a name="WatchKit-Framework-Enhancements"></a>

## <a name="watchkit-framework-enhancements"></a>Mejoras de WatchKit Framework

watchos 3 incluye varias mejoras en el marco de trabajo de WatchKit, entre las que se incluyen las siguientes:

- La aplicación puede obtener el estado de la Digital Crown con la nueva clase [WKCrownSequencer](https://developer.apple.com/reference/watchkit/wkcrownsequencer) y recibir actualizaciones cuando el usuario gira la corona con la clase [WKCrownDelegate](https://developer.apple.com/reference/watchkit/wkcrowndelegate) .
- La clase [WKExtension](https://developer.apple.com/reference/watchkit/wkextension) ahora incluye el `ApplicationState` método y la constante [WKApplicationState](https://developer.apple.com/reference/watchkit/wkapplicationstate) que la aplicación puede usar para realizar un seguimiento del estado de tiempo de ejecución de la aplicación. `WKExtension`también proporciona dos nuevos métodos que se pueden usar para programar tareas en segundo plano.
- Ahora, [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate) incluye los nuevos `ApplicationWillEnterForeground` `ApplicationDidEnterBackground` métodos, y `HandleBackgroundTasks` para supervisar los cambios en el estado de la aplicación y controlar las actualizaciones de las tareas en segundo plano.
- Se ha agregado una nueva clase [WKGestureRecognizer](https://developer.apple.com/reference/watchkit/wkgesturerecognizer) para proporcionar los siguientes tipos de reconocimiento de gestos a las aplicaciones de inspección: [WKLongPressGestureRecognizer](https://developer.apple.com/reference/watchkit/wklongpressgesturerecognizer), [WKPanGestureRecognizer](https://developer.apple.com/reference/watchkit/wkpangesturerecognizer), [WKSwipeGestureRecognizer](https://developer.apple.com/reference/watchkit/wkswipegesturerecognizer) y [WKTapGestureRecognizer](https://developer.apple.com/reference/watchkit/wktapgesturerecognizer).
- La nueva clase [WKinterfaceHMCamera](https://developer.apple.com/reference/watchkit/wkinterfacehmcamera) proporciona una interfaz para cualquier cámara IP asociada a HomeKit.
- La nueva clase [WKInterfaceInlineMovie](https://developer.apple.com/reference/watchkit/wkinterfaceinlinemovie) permite a la aplicación mostrar una película "póster" que se reemplaza por la película en ejecución cuando el usuario la puntea.
- La nueva clase [WKInterfacePaymentButton](https://developer.apple.com/reference/watchkit/wkinterfacepaymentbutton) permite a la aplicación presentar un botón Apple Pay en su interfaz de usuario que iniciará una solicitud de pago cuando se Puntee.
- La nueva clase [WKInterfaceSCNScene](https://developer.apple.com/reference/watchkit/wkinterfacescnscene) presenta una interfaz para mostrar una escena de SceneKit en el Apple Watch.
- La nueva clase [WKInterfaceSKScene](https://developer.apple.com/reference/watchkit/wkinterfaceskscene) presenta una interfaz para mostrar una escena de SpriteKit en el Apple Watch.

Para obtener más información, consulte nuestra guía [de técnicas de interacción rápida para watchos 3](~/ios/watchos/platform/quick-interaction-techniques.md) .

<a name="Workout-App-Enhancements"></a>

## <a name="workout-app-enhancements"></a>Mejoras de la aplicación Workout

Novedad de watchos 3, las aplicaciones relacionadas con el entrenamiento tienen la capacidad de ejecutarse en segundo plano en el Apple Watch. Para habilitar esta característica (y obtener acceso a los datos de HealthKit), la aplicación debe incluir la `WKBackgroundModes` clave en el `Info.plist` archivo con el valor `workout-processing` .

Además, ahora el desarrollador tiene la capacidad de iniciar la aplicación de entrenamiento de watchos desde la versión de la aplicación iOS en el iPhone emparejado.

Para obtener más información, consulte nuestra guía de mejoras en la [aplicación de entrenamiento](~/ios/watchos/platform/workout-apps.md) .

<a name="Additional-Framework-Changes"></a>

## <a name="additional-framework-changes"></a>Cambios adicionales en el marco de trabajo

Además de los principales cambios en el marco de trabajo y las adiciones enumeradas anteriormente, Apple ha realizado muchos cambios en el marco de trabajo más pequeños en watchos 3.

Para obtener más información, consulte nuestra guía [adicional de cambios](~/ios/watchos/platform/introduction-to-watchos3/additional-framework-changes.md) en el marco de trabajo.

<a name="Deprecated-APIs"></a>

## <a name="deprecated-apis"></a>Interfaces API desusadas

Las siguientes API están en desuso en watchos 3:

- La `UILocalNotification` clase de UIKit está en desuso y debe reemplazarse por el marco de trabajo de notificaciones del usuario.

Consulte la documentación de las diferencias de API de los monitores de Apple [2,2 a watchos 3,0](https://developer.apple.com/library/prerelease/content/releasenotes/General/watchOS30APIDiffs/index.html) para obtener una lista completa de los desuso y los cambios.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de watchOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+watchOS)
- [Novedades de watchos 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
