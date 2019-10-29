---
title: Alertas críticas en Xamarin. iOS
description: En este documento se describe cómo usar las alertas críticas con Xamarin. iOS. Las alertas críticas, introducidas con iOS 12, son notificaciones molestas que reproducen un sonido independientemente de si la opción no molestar está activada o si el interruptor del timbre está desactivado.
ms.prod: xamarin
ms.assetid: 75742257-081D-44F4-B49E-FB807DF85262
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: 43b810b95e4da2927030617e68c0ade824a0beaa
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031979"
---
# <a name="critical-alerts-in-xamarinios"></a>Alertas críticas en Xamarin. iOS

Con iOS 12, las aplicaciones pueden enviar alertas críticas. Las alertas críticas reproducen un sonido independientemente de si está habilitado o no molestar o si el interruptor del timbre está desactivado. Estas notificaciones son perjudiciales y solo deben usarse cuando los usuarios deben realizar una acción inmediata.

## <a name="custom-critical-alert-entitlement"></a>Derecho de alerta crítica personalizada

Para mostrar las alertas críticas en la aplicación, primero [solicite un derecho a las notificaciones de alerta críticas](https://developer.apple.com/contact/request/notifications-critical-alerts-entitlement/) de Apple.

Después de recibir este derecho de Apple y de seguir las instrucciones asociadas sobre cómo configurar la aplicación para que la use, agregue el [derecho](~/ios/deploy-test/provisioning/entitlements.md) personalizado a los archivos **. plist de derechos** de la aplicación. Después, configure las opciones de **firma del lote de iOS** para usar los **derechos. plist** al firmar la aplicación en el simulador y el dispositivo.

## <a name="request-authorization"></a>Solicitar autorización

La solicitud de autorización de notificación de una aplicación solicita al usuario que permita o deniega las notificaciones de la aplicación. Si la solicitud de autorización de notificación solicita permiso para enviar alertas críticas, la aplicación también dará al usuario la oportunidad de participar en las alertas críticas.

El código siguiente solicita permiso para enviar alertas críticas y notificaciones y sonidos estándar pasando el [`UNAuthorizationOptions`](xref:UserNotifications.UNAuthorizationOptions) adecuado
valores para [`RequestAuthorization`](xref:UserNotifications.UNUserNotificationCenter.RequestAuthorization*):

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.CriticalAlert;
    center.RequestAuthorization(options, (bool success, NSError error) => {
        // ...
    );
    return true;
}
```

## <a name="local-critical-alerts"></a>Alertas críticas locales

Para enviar una alerta crítica local, cree una [`UNMutableNotificationContent`](xref:UserNotifications.UNMutableNotificationContent)
y establezca su propiedad `Sound` en:

- `UNNotificationSound.DefaultCriticalSound`, que usa el sonido de notificación crítico predeterminado.
- `UNNotificationSound.GetCriticalSound`, que le permite especificar un sonido personalizado que se incluye en la aplicación y en un volumen.

A continuación, cree un `UNNotificationRequest` a partir del contenido de la notificación y agréguelo al centro de notificaciones:

```csharp
var content = new UNMutableNotificationContent()
{
    Title = "Critical alert title",
    Body = "Text of the critical alert",
    CategoryIdentifier = "my-critical-alert-category",
    // Sound = UNNotificationSound.DefaultCriticalSound
    Sound = UNNotificationSound.GetCriticalSound("my_critical_sound.m4a", 1.0f)
};

var request = UNNotificationRequest.FromIdentifier(
    Guid.NewGuid().ToString(),
    content,
    UNTimeIntervalNotificationTrigger.CreateTrigger(3, false)
);

var center = UNUserNotificationCenter.Current;
center.AddNotificationRequest(request, null);
```

> [!IMPORTANT]
> Las alertas críticas no se entregarán si no están habilitadas para la aplicación. Junto con el mensaje que aparece la primera vez que una aplicación solicita permiso para enviar alertas críticas, un usuario también puede habilitar o deshabilitar las alertas críticas en la sección **notificaciones** de la aplicación de la aplicación de **configuración** de iOS.

## <a name="remote-critical-alerts"></a>Alertas críticas remotas

Para obtener información acerca de las alertas críticas remotas, consulte la sesión [novedades en las notificaciones de usuario](https://developer.apple.com/videos/play/wwdc2018/710/) de WWDC 2018 y el documento [generación de una notificación remota](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification) .

## <a name="related-links"></a>Vínculos relacionados

- [Marco de notificaciones de usuario en Xamarin. iOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Novedades de las notificaciones de usuario (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Prácticas recomendadas y novedades en las notificaciones de usuario (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Generación de una notificación remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
