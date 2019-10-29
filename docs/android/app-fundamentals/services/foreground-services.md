---
title: Servicios de primer plano
ms.prod: xamarin
ms.assetid: C10FD999-7A91-4708-B642-0C1B0901BD24
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/19/2018
ms.openlocfilehash: aaef03d68f4ec374a3bc706daffe636e575e42ff
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024757"
---
# <a name="foreground-services"></a>Servicios de primer plano

Un servicio en primer plano es un tipo especial de un servicio enlazado o un servicio iniciado. En ocasiones, los servicios realizarán tareas que los usuarios deben tener en cuenta activamente, estos servicios se denominan _servicios en primer plano_. Un ejemplo de un servicio en primer plano es una aplicación que proporciona al usuario las instrucciones mientras conduce o se dirige. Incluso si la aplicación está en segundo plano, todavía es importante que el servicio tenga suficientes recursos para funcionar correctamente y que el usuario tenga una forma rápida y cómoda de acceder a la aplicación. En el caso de una aplicación de Android, esto significa que un servicio en primer plano debe recibir mayor prioridad que un servicio "normal" y un servicio en primer plano debe proporcionar una `Notification` que Android mostrará siempre que se esté ejecutando el servicio.

Para iniciar un servicio en primer plano, la aplicación debe enviar un intento que indique a Android que inicie el servicio. Después, el servicio debe registrarse como servicio en primer plano con Android. Las aplicaciones que se ejecutan en Android 8,0 (o posterior) deben usar el método `Context.StartForegroundService` para iniciar el servicio, mientras que las aplicaciones que se ejecutan en dispositivos con una versión anterior de Android deben usar `Context.StartService`

Este C# método de extensión es un ejemplo de cómo iniciar un servicio en primer plano. En Android 8,0 y versiones posteriores, usará el método `StartForegroundService`; de lo contrario, se utilizará el método de `StartService` anterior.

```csharp
public static void StartForegroundServiceCompat<T>(this Context context, Bundle args = null) where T : Service
{
    var intent = new Intent(context, typeof(T));
    if (args != null) 
    {
        intent.PutExtras(args);
    }

    if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.O)
    {
        context.StartForegroundService(intent);
    }
    else
    {
        context.StartService(intent);
    }
}
```

## <a name="registering-as-a-foreground-service"></a>Registro como servicio en primer plano

Una vez que se inicia un servicio en primer plano, debe registrarse en Android mediante la invocación del [`StartForeground`](xref:Android.App.Service.StartForeground*). Si el servicio se inicia con el método `Service.StartForegroundService` pero no se registra a sí mismo, Android detendrá el servicio y marcará la aplicación como no responde.

`StartForeground` toma dos parámetros, los cuales son obligatorios:

- Valor entero que es único dentro de la aplicación para identificar el servicio.
- Objeto `Notification` que Android mostrará en la barra de estado mientras se esté ejecutando el servicio.

Android mostrará la notificación en la barra de estado durante el tiempo que se esté ejecutando el servicio. La notificación, como mínimo, proporcionará una indicación visual al usuario de que el servicio se está ejecutando. Idealmente, la notificación debe proporcionar al usuario un acceso directo a la aplicación o, posiblemente, algunos botones de acción para controlar la aplicación. Un ejemplo de esto es un reproductor de música &ndash; la notificación que se muestra puede tener botones para pausar o reproducir música, rebobinar a la canción anterior o pasar a la siguiente canción. 

Este fragmento de código es un ejemplo de registro de un servicio como servicio en primer plano:   

```csharp
// This is any integer value unique to the application.
public const int SERVICE_RUNNING_NOTIFICATION_ID = 10000;

public override StartCommandResult OnStartCommand(Intent intent, StartCommandFlags flags, int startId)
{
    // Code not directly related to publishing the notification has been omitted for clarity.
    // Normally, this method would hold the code to be run when the service is started.

    var notification = new Notification.Builder(this)
        .SetContentTitle(Resources.GetString(Resource.String.app_name))
        .SetContentText(Resources.GetString(Resource.String.notification_text))
        .SetSmallIcon(Resource.Drawable.ic_stat_name)
        .SetContentIntent(BuildIntentToShowMainActivity())
        .SetOngoing(true)
        .AddAction(BuildRestartTimerAction())
        .AddAction(BuildStopServiceAction())
        .Build();

    // Enlist this instance of the service as a foreground service
    StartForeground(SERVICE_RUNNING_NOTIFICATION_ID, notification);
}
```

La notificación anterior mostrará una notificación de la barra de estado similar a la siguiente:

![Imagen que muestra la notificación en la barra de estado](foreground-services-images/foreground-services-01.png "Imagen que muestra la notificación en la barra de estado")

Esta captura de pantalla muestra la notificación expandida en la bandeja de notificación con dos acciones que permiten al usuario controlar el servicio:

![Imagen que muestra la notificación expandida](foreground-services-images/foreground-services-02.png "Imagen que muestra la notificación expandida.")

Puede encontrar más información sobre las notificaciones en la sección [notificaciones locales](~/android/app-fundamentals/notifications/local-notifications.md) de la guía de [notificaciones de Android](~/android/app-fundamentals/notifications/index.md) .

## <a name="unregistering-as-a-foreground-service"></a>Anular el registro como servicio en primer plano

Un servicio se puede quitar de la lista como un servicio en primer plano llamando al método `StopForeground`. `StopForeground` no detendrá el servicio, pero quitará el icono de notificación y indicará a Android que este servicio se puede apagar si es necesario.

La notificación de la barra de estado que se muestra también se puede quitar pasando `true` al método: 

```csharp
StopForeground(true);
```

Si el servicio se detiene con una llamada a `StopSelf` o `StopService`, se quitará la notificación de la barra de estado.

## <a name="related-links"></a>Vínculos relacionados

- [Android. app. Service](xref:Android.App.Service)
- [Android. app. Service. StartForeground](xref:Android.App.Service.StartForeground*)
- [Notificaciones locales](~/android/app-fundamentals/notifications/local-notifications.md)
- [ForegroundServiceDemo (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-foregroundservicedemo)
