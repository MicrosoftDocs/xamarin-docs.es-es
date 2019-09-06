---
title: Notificaciones en Xamarin. iOS
description: En esta sección se muestra cómo implementar las notificaciones locales en Xamarin. iOS. Se explican los distintos elementos de la interfaz de usuario de una notificación de iOS y se describen las API implicadas en la creación y visualización de una notificación.
ms.prod: xamarin
ms.assetid: 5BB76915-5DB0-48C7-A267-FA9F7C50793E
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/13/2018
ms.openlocfilehash: eb8956e44a3148d79fff59d728cb63818b9bab60
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292748"
---
# <a name="notifications-in-xamarinios"></a>Notificaciones en Xamarin. iOS

> [!IMPORTANT]
> La información de esta sección se refiere a iOS 9 y versiones anteriores. Para iOS 10 y versiones posteriores, consulte la [Guía del marco de trabajo de notificaciones de usuario](~/ios/platform/user-notifications/index.md).

iOS tiene tres formas de indicar al usuario que se ha recibido una notificación:

- **Sonido o vibración** : iOS puede reproducir un sonido para notificar a los usuarios. Si el sonido está deshabilitado, el dispositivo puede configurarse para vibrar.
- **Alertas** : es posible mostrar un cuadro de diálogo en la pantalla con información sobre la notificación.
- **Distintivos** : cuando se publica una notificación, se puede mostrar un número (distintivo) en el icono de la aplicación.

iOS también proporciona un *centro de notificaciones* que mostrará todas las notificaciones, tanto locales como remotas, al usuario. Los usuarios pueden tener acceso a esta tecla deslizando el dedo hacia abajo desde la parte superior de la pantalla:

![Centro de notificaciones](local-notifications-in-ios-images/image13.png "Centro de notificaciones")

## <a name="creating-local-notifications-in-ios"></a>Creación de notificaciones locales en iOS

iOS hace que sea bastante sencillo crear y controlar las notificaciones locales.
En primer lugar, iOS 8 requiere que las aplicaciones soliciten el permiso del usuario para mostrar las notificaciones. Agregue el código siguiente a la aplicación antes de intentar enviar una notificación local: el [ejemplo adjunto](https://docs.microsoft.com/samples/xamarin/ios-samples/localnotifications) lo coloca en el método **FinishedLaunching** de **AppDelegate**.

```csharp
var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes(
    UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
);
application.RegisterUserNotificationSettings(notificationSettings);
```

[![Confirmar la capacidad de enviar una notificación local](local-notifications-in-ios-images/image0-sml.png "Confirmar la capacidad de enviar una notificación local")](local-notifications-in-ios-images/image0.png#lightbox)

Para programar una notificación local, cree un `UILocalNotification` objeto, `FireDate`establezca y programe mediante el `ScheduleLocalNotification` método en el `UIApplication.SharedApplication` objeto. En el fragmento de código siguiente se muestra cómo programar una notificación que se activará un minuto en el futuro y mostrar una alerta con un mensaje:

```csharp
UILocalNotification notification = new UILocalNotification();
notification.FireDate = NSDate.FromTimeIntervalSinceNow(15);
//notification.AlertTitle = "Alert Title"; // required for Apple Watch notifications
notification.AlertAction = "View Alert";
notification.AlertBody = "Your 15 second alert has fired!";
UIApplication.SharedApplication.ScheduleLocalNotification(notification);
```

En la captura de pantalla siguiente se muestra el aspecto de esta alerta:

[![](local-notifications-in-ios-images/image2-sml.png "Una alerta de ejemplo")](local-notifications-in-ios-images/image2.png#lightbox)

Tenga en cuenta que si el usuario decide *no permitir* las notificaciones, no se mostrará nada.

Si desea aplicar un distintivo al icono de la aplicación con un número, puede establecerlo tal como se muestra en el siguiente código de línea:

```csharp
notification.ApplicationIconBadgeNumber = 1;
```

Para reproducir un sonido con el icono, establezca la propiedad SoundName en la notificación, tal como se muestra en el siguiente fragmento de código:

```csharp
notification.SoundName = UILocalNotification.DefaultSoundName;
```

Si el sonido de la notificación es superior a 30 segundos, iOS reproducirá el sonido predeterminado en su lugar.

> [!IMPORTANT]
> Hay un error en el simulador de iOS que activará la notificación de delegado dos veces. Este problema no debería producirse al ejecutar la aplicación en un dispositivo.

## <a name="handling-notifications"></a>Control de notificaciones

las aplicaciones de iOS controlan las notificaciones remotas y locales casi exactamente igual. Cuando una aplicación se está ejecutando, `ReceivedLocalNotification` se llamará `ReceivedRemoteNotification` al método o `AppDelegate` al método en la clase, y la información de notificación se pasará como parámetro.

Una aplicación puede controlar una notificación de maneras diferentes. Por ejemplo, la aplicación podría mostrar simplemente una alerta para recordar a los usuarios sobre algún evento. O bien, la notificación podría usarse para mostrar una alerta al usuario que ha finalizado un proceso, como la sincronización de archivos en un servidor.

En el código siguiente se muestra cómo controlar una notificación local y mostrar una alerta y restablecer el número de identificación en cero:

```csharp
public override void ReceivedLocalNotification(UIApplication application, UILocalNotification notification)
{
    // show an alert
    UIAlertController okayAlertController = UIAlertController.Create(notification.AlertAction, notification.AlertBody, UIAlertControllerStyle.Alert);
    okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

    Window.RootViewController.PresentViewController(okayAlertController, true, null);

    // reset our badge
    UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
}
```

Si la aplicación no se está ejecutando, iOS reproducirá el sonido y/o actualizará el distintivo del icono según corresponda. Cuando el usuario inicia la aplicación asociada a la alerta, se iniciará la aplicación y se `FinishedLaunching` llamará al método en el delegado de la aplicación y la información de notificación se pasará a `launchOptions` través del parámetro. Si el Diccionario de opciones contiene la `UIApplication.LaunchOptionsLocalNotificationKey`clave, `AppDelegate` sabe que la aplicación se inició desde una notificación local. En el siguiente fragmento de código se muestra este proceso:

```csharp
// check for a local notification
if (launchOptions.ContainsKey(UIApplication.LaunchOptionsLocalNotificationKey))
{
    var localNotification = launchOptions[UIApplication.LaunchOptionsLocalNotificationKey] as UILocalNotification;
    if (localNotification != null)
    {
        UIAlertController okayAlertController = UIAlertController.Create(localNotification.AlertAction, localNotification.AlertBody, UIAlertControllerStyle.Alert);
        okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

        Window.RootViewController.PresentViewController(okayAlertController, true, null);

        // reset our badge
        UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
    }
}
```

En el caso de una `launchOptions` notificación remota, `LaunchOptionsRemoteNotificationKey` tendrá un objeto `NSDictionary` con un objeto asociado que contiene la carga de notificación remota. Puede extraer la carga de notificación a través `alert`de `badge`las claves `sound` , y. En el fragmento de código siguiente se muestra cómo obtener notificaciones remotas:

```csharp
NSDictionary remoteNotification = options[UIApplication.LaunchOptionsRemoteNotificationKey];
if(remoteNotification != null)
{
    string alert = remoteNotification["alert"];
}
```

## <a name="summary"></a>Resumen

En esta sección se ha mostrado cómo crear y publicar una notificación en Xamarin. iOS. Muestra cómo una aplicación puede reaccionar ante las notificaciones invalidando el `ReceivedLocalNotification` método o el `ReceivedRemoteNotification` método `AppDelegate`en.

## <a name="related-links"></a>Vínculos relacionados

- [Notificaciones locales (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/localnotifications)
- [Notificaciones de envío y locales para desarrolladores](https://developer.apple.com/notifications/)
- [Guía de programación de notificaciones de envío y locales](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UILocalNotification](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UILocalNotification)
