---
title: Notificaciones provisionales en Xamarin. iOS
description: En este documento se describe cómo usar Xamarin. iOS para trabajar con notificaciones provisionales. Las notificaciones provisionales, introducidas en iOS 12, permiten que las aplicaciones envíen notificaciones silenciosamente sin permiso explícito de usuario.
ms.prod: xamarin
ms.assetid: 5DCB36B9-2637-48AE-8FC0-F6124F08AC48
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: 7d9fe2a651d8d75d8dd9d8c0dd1225350a58373d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031886"
---
# <a name="provisional-notifications-in-xamarinios"></a>Notificaciones provisionales en Xamarin. iOS

Las notificaciones provisionales permiten a las aplicaciones proporcionar notificaciones sin el consentimiento explícito de un usuario. Estas notificaciones llegan de forma silenciosa y solo se muestran en el centro de notificaciones, lo que permite a los usuarios obtener una vista previa de ellas antes de optar por la entrega continuada.

En el centro de notificaciones, los usuarios pueden especificar que una aplicación debe detener la entrega de notificaciones provisionales, continuar ofreciéndola de forma aprovisionada o comenzar a entregarlas de forma más destacada.

## <a name="sample-app-redgreennotifications"></a>Aplicación de ejemplo: RedGreenNotifications

Eche un vistazo a la aplicación de ejemplo [RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications) , que envía notificaciones provisionales.

## <a name="sending-provisional-notifications"></a>Envío de notificaciones provisionales

Para enviar notificaciones provisionales, proporcione `UNAuthorizationOptions.Provisional` como opción al [`RequestAuthorization`](xref:UserNotifications.UNUserNotificationCenter.RequestAuthorization*)
método de `UNUserNotificationCenter`:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional;
    center.RequestAuthorization(options, (bool success, NSError error) => {
        // ...
    );
    return true;
}
```

Si el usuario promueve notificaciones provisionales a entregas destacadas, los `UNAuthorizationOptions` valores pasados a `RequestAuthorization` determinarán la nueva configuración de entrega de notificaciones (en el código anterior, `UNAuthorizationOptions.Alert` y `UNAuthorizationOptions.Sound`).

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo: RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Marco de notificaciones de usuario en Xamarin. iOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Novedades de las notificaciones de usuario (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Prácticas recomendadas y novedades en las notificaciones de usuario (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Generación de una notificación remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
