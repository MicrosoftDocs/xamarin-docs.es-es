---
title: Notificaciones del servicio
description: En esta guía se describe cómo un servicio Android puede usar notificaciones locales para enviar información a un usuario.
ms.prod: xamarin
ms.assetid: 6C06FDE7-6385-40EF-AC7C-8EFB54E29F45
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: b02785863f89ef6a273c52c09f45a99c17cb6242
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024532"
---
# <a name="service-notifications"></a>Notificaciones del servicio

_En esta guía se describe cómo un servicio Android puede usar notificaciones locales para enviar información a un usuario._

## <a name="service-notifications-overview"></a>Información general de notificaciones de servicio

Las notificaciones de servicio permiten que una aplicación muestre información al usuario, incluso si la aplicación Android no está en primer plano. Es posible que una notificación proporcione acciones al usuario, como mostrar una actividad de una aplicación. En el ejemplo de código siguiente se muestra cómo un servicio puede enviar una notificación a un usuario:

```csharp
[Service]
public class MyService: Service 
{
    // A notification requires an id that is unique to the application.
    const int NOTIFICATION_ID = 9000;
    
    public override StartCommandResult OnStartCommand(Intent intent, StartCommandFlags flags, int startId)
    {
        // Code omitted for clarity - here is where the service would do something.
    
        // Work has finished, now dispatch anotification to let the user know.
        Notification.Builder notificationBuilder = new Notification.Builder(this)
            .SetSmallIcon(Resource.Drawable.ic_notification_small_icon)
            .SetContentTitle(Resources.GetString(Resource.String.notification_content_title))
            .SetContentText(Resources.GetString(Resource.String.notification_content_text));
        
        var notificationManager = (NotificationManager)GetSystemService(NotificationService);
        notificationManager.Notify(NOTIFICATION_ID, notificationBuilder.Build());
    }
}
```

Esta captura de pantalla es un ejemplo de la notificación que se muestra:

[![icono de notificación que se muestra en la barra de estado](service-notifications-images/01-notification-sml.png)](service-notifications-images/01-notification.png#lightbox)

Cuando el usuario se desplaza hacia abajo en la pantalla de notificación desde la parte superior, se muestra la notificación completa:

![Aviso mostrado en la bandeja de notificación](service-notifications-images/02-fullnotification.png)

## <a name="updating-a-notification"></a>Actualización de una notificación

Para actualizar una notificación, el servicio volverá a publicar la notificación con el mismo identificador de notificación. Android mostrará o actualizará la notificación en la barra de estado según sea necesario.

```csharp 
void UpdateNotification(string content)
{
   var notification = GetNotification(content, pendingIntent);

   NotificationManager notificationManager = (NotificationManager)GetSystemService(Context.NotificationService);
   notificationManager.Notify(NOTIFICATION_ID, notification);
}

Notification GetNotification(string content, PendingIntent intent)
{
   return new Notification.Builder(this)
           .SetContentTitle(tag)
           .SetContentText(content)
           .SetSmallIcon(Resource.Drawable.NotifyLg)
           .SetLargeIcon(BitmapFactory.DecodeResource(Resources, Resource.Drawable.Icon))
           .SetContentIntent(intent).Build();
}
```

Puede encontrar más información sobre las notificaciones en la sección [notificaciones locales](~/android/app-fundamentals/notifications/local-notifications.md) de la guía de [notificaciones de Android](~/android/app-fundamentals/notifications/index.md) .

## <a name="related-links"></a>Vínculos relacionados

- [Notificaciones locales en Android](~/android/app-fundamentals/notifications/local-notifications.md)
