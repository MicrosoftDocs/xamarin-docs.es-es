---
title: Notificaciones agrupadas en Xamarin. iOS
description: Con iOS 12, es posible agrupar las notificaciones en el centro de notificaciones o la pantalla de bloqueo por aplicación o por subproceso. En este documento se describe cómo enviar notificaciones de subprocesos y no subprocesos con Xamarin. iOS.
ms.prod: xamarin
ms.assetid: C6FA7C25-061B-4FD7-8E55-88597D512F3C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: 6352de1483aea49a628cbb30d104906fde767afa
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031956"
---
# <a name="grouped-notifications-in-xamarinios"></a>Notificaciones agrupadas en Xamarin. iOS

De forma predeterminada, iOS 12 coloca todas las notificaciones de una aplicación en un grupo. La pantalla de bloqueo y el centro de notificaciones muestran este grupo como una pila con la notificación más reciente en primer lugar. Los usuarios pueden expandir el grupo para ver todas las notificaciones que contiene y descartar el grupo en su conjunto.

Las aplicaciones también pueden agrupar las notificaciones por subproceso, lo que facilita a los usuarios la búsqueda e interacción con la información específica que le interesan.

## <a name="sample-app-groupednotifications"></a>Aplicación de ejemplo: GroupedNotifications

Para obtener información sobre cómo usar las notificaciones agrupadas con Xamarin. iOS, eche un vistazo a la aplicación de ejemplo [GroupedNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-groupednotifications) .

En esta aplicación de ejemplo se simulan conversaciones con varios amigos, se envía una notificación para cada mensaje y se agrupan por subproceso. También muestra cómo las notificaciones no subprocesos se encuentran en un grupo de nivel de aplicación.

Los fragmentos de código de esta guía proceden de esta aplicación de ejemplo.

## <a name="request-authorization-and-allow-foreground-notifications"></a>Solicitar autorización y permitir notificaciones en primer plano

Para que una aplicación pueda enviar notificaciones locales, debe solicitar permiso para hacerlo. En el [`AppDelegate`](xref:UIKit.UIApplicationDelegate)de la aplicación de ejemplo, el método [`FinishedLaunching`](xref:UIKit.UIApplicationDelegate.FinishedLaunching(UIKit.UIApplication,Foundation.NSDictionary)) solicita este permiso:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    center.RequestAuthorization(UNAuthorizationOptions.Alert, (bool success, NSError error) =>
    {
        // Set the Delegate regardless of success; users can modify their notification
        // preferences at any time in the Settings app.
        center.Delegate = this;
    });
    return true;
}
```

El [`Delegate`](xref:UserNotifications.UNUserNotificationCenter.Delegate) (establecido anteriormente) para un [`UNUserNotificationCenter`](xref:UserNotifications.UNUserNotificationCenter) decide si una aplicación en primer plano debe mostrar una notificación entrante llamando al controlador de finalización que se pasa a [`WillPresentNotification`](xref:UserNotifications.UNUserNotificationCenterDelegate_Extensions.WillPresentNotification(UserNotifications.IUNUserNotificationCenterDelegate,UserNotifications.UNUserNotificationCenter,UserNotifications.UNNotification,System.Action{UserNotifications.UNNotificationPresentationOptions})):

```csharp
[Export("userNotificationCenter:willPresentotification:withCompletionHandler:")]
public void WillPresentNotification(UNUserNotificationCenter center, UNNotification notification, System.Action<UNNotificationPresentationOptions> completionHandler)
{
    completionHandler(UNNotificationPresentationOptions.Alert);
}
```

El parámetro [`UNNotificationPresentationOptions.Alert`](xref:UserNotifications.UNNotificationPresentationOptions) indica que la aplicación debe mostrar la alerta pero no reproducir un sonido ni actualizar un distintivo.

## <a name="threaded-notifications"></a>Notificaciones de subprocesos

Pulse el botón mensaje de la aplicación de ejemplo **con Alice** varias veces para que envíe notificaciones de una conversación con un amigo denominado Alice.
Dado que las notificaciones de esta conversación forman parte del mismo subproceso, la pantalla de bloqueo y el centro de notificaciones se agrupan juntas.

Para iniciar una conversación con un amigo diferente, puntee en el botón **elegir un nuevo amigo** . Las notificaciones de esta conversación aparecen en un grupo independiente.

### <a name="threadidentifier"></a>ThreadIdentifier

Cada vez que la aplicación de ejemplo inicia un nuevo subproceso, crea un identificador de subproceso único:

```csharp
void StartNewThread()
{
    threadId = $"message-{friend}";
    // ...
}
```

Para enviar una notificación de subprocesos, la aplicación de ejemplo:

- Comprueba si la aplicación tiene autorización para enviar una notificación.
- Crea un [`UNMutableNotificationContent`](xref:UserNotifications.UNMutableNotificationContent)
objeto para el contenido de la notificación y establece su [`ThreadIdentifier`](xref:UserNotifications.UNMutableNotificationContent.ThreadIdentifier)
al identificador de subproceso creado anteriormente.
- Crea una solicitud y programa la notificación:

```csharp
async partial void ScheduleThreadedNotification(UIButton sender)
{
    var center = UNUserNotificationCenter.Current;

    UNNotificationSettings settings = await center.GetNotificationSettingsAsync();
    if (settings.AuthorizationStatus != UNAuthorizationStatus.Authorized)
    {
        return;
    }

    string author =  // ...
    string message = // ...

    var content = new UNMutableNotificationContent()
    {
        ThreadIdentifier = threadId,
        Title = author,
        Body = message,
        SummaryArgument = author
    };

    var request = UNNotificationRequest.FromIdentifier(
        Guid.NewGuid().ToString(),
        content,
        UNTimeIntervalNotificationTrigger.CreateTrigger(1, false)
    );

    center.AddNotificationRequest(request, null);

    // ...
}
```

Todas las notificaciones de la misma aplicación con el mismo identificador de subproceso aparecerán en el mismo grupo de notificación.

> [!NOTE]
> Para establecer un identificador de subproceso en una notificación remota, agregue la clave `thread-id` a la carga JSON de la notificación. Vea el documento [generación de una notificación remota](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification) de Apple para obtener más detalles.

### <a name="summaryargument"></a>SummaryArgument

`SummaryArgument` especifica cómo afectará una notificación al texto de resumen que aparece en la esquina inferior izquierda de un grupo de notificaciones al que pertenece la notificación. iOS agrega texto de Resumen de las notificaciones del mismo grupo para crear una descripción general del Resumen.

La aplicación de ejemplo usa el autor del mensaje como argumento de resumen. Con este enfoque, el texto de Resumen de un grupo de seis notificaciones con Alice podría ser **de 5 más notificaciones de Alice y me**.

## <a name="unthreaded-notifications"></a>Notificaciones no subproceso

Cada vez que se pulsa el botón **recordatorio de cita** de la aplicación de ejemplo envía una de las distintas notificaciones de recordatorio de cita. Dado que estos recordatorios no son subprocesos, aparecen en el grupo de notificación de nivel de aplicación en la pantalla de bloqueo y en el centro de notificaciones.

Para enviar una notificación de no subproceso, el método de `ScheduleUnthreadedNotification` de la aplicación de ejemplo usa un código similar al anterior.
Sin embargo, no establece el `ThreadIdentifier` en el objeto `UNMutableNotificationContent`.

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo: GroupedNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-groupednotifications)
- [Marco de notificaciones de usuario en Xamarin. iOS](~/ios/platform/user-notifications/index.md)
- [Novedades de las notificaciones de usuario (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Uso de notificaciones agrupadas (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/711/)
- [Prácticas recomendadas y novedades en las notificaciones de usuario (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Generación de una notificación remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
