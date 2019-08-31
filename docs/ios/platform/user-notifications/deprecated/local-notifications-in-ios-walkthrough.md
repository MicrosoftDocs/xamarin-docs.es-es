---
title: 'Tutorial: uso de notificaciones locales en Xamarin. iOS'
description: En esta sección veremos cómo usar las notificaciones locales en una aplicación de Xamarin. iOS. Se mostrarán los aspectos básicos de la creación y publicación de una notificación que mostrará una alerta cuando la reciba la aplicación.
ms.prod: xamarin
ms.assetid: 32B9C6F0-2BB3-4295-99CB-A75418969A62
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 376f046769e27586d9611d634aef89811c7b3be8
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200382"
---
# <a name="walkthrough---using-local-notifications-in-xamarinios"></a>Tutorial: uso de notificaciones locales en Xamarin. iOS

_En esta sección veremos cómo usar las notificaciones locales en una aplicación de Xamarin. iOS. Se mostrarán los aspectos básicos de la creación y publicación de una notificación que mostrará una alerta cuando la reciba la aplicación._

> [!IMPORTANT]
> La información de esta sección se refiere a iOS 9 y versiones anteriores, se ha dejado aquí para admitir versiones anteriores de iOS. Para iOS 10 y versiones posteriores, consulte la [Guía del marco de trabajo](~/ios/platform/user-notifications/index.md) de notificaciones de usuario para admitir la notificación local y remota en un dispositivo iOS.

## <a name="walkthrough"></a>Tutorial

Permita crear una aplicación sencilla que mostrará las notificaciones locales en acción. Esta aplicación tendrá un solo botón. Cuando hacemos clic en el botón, se creará una notificación local. Una vez transcurrido el período de tiempo especificado, verá que aparece la notificación.


1. En Visual Studio para Mac, cree una nueva solución de iOS de vista única y `Notifications`llámela.
1. Abra el `Main.storyboard` archivo y arrastre un botón hasta la vista. Asigne un nombre al **botón**del botón y asígnele el título **Agregar notificación**. También puede establecer algunas [restricciones](~/ios/user-interface/designer/designer-auto-layout.md) en el botón en este punto: 

    ![](local-notifications-in-ios-walkthrough-images/image3.png "Establecer algunas restricciones en el botón")
1. Edite `ViewController` la clase y agregue el siguiente controlador de eventos al método ViewDidLoad:

    ```csharp
    button.TouchUpInside += (sender, e) =>
    {
        // create the notification
        var notification = new UILocalNotification();

        // set the fire date (the date time in which it will fire)
        notification.FireDate = NSDate.FromTimeIntervalSinceNow(60);

        // configure the alert
        notification.AlertAction = "View Alert";
        notification.AlertBody = "Your one minute alert has fired!";

        // modify the badge
        notification.ApplicationIconBadgeNumber = 1;

        // set the sound to be the default sound
        notification.SoundName = UILocalNotification.DefaultSoundName;

        // schedule it
        UIApplication.SharedApplication.ScheduleLocalNotification(notification);
    };
    ```

    Este código creará una notificación que utiliza un sonido, establece el valor de la distintivo del icono en 1 y muestra una alerta al usuario.

1. Después, edite `AppDelegate.cs`el archivo, agregue primero el siguiente código `FinishedLaunching` al método. Hemos comprobado que el dispositivo ejecuta iOS 8, en caso de que sea **necesario** solicitar el permiso del usuario para recibir notificaciones:

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
        var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes (
            UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
        );

        application.RegisterUserNotificationSettings (notificationSettings);
    }
    ```

1. Todavía en `AppDelegate.cs`, agregue el siguiente método al que se llamará cuando se reciba una notificación:

    ```csharp
    public override void ReceivedLocalNotification(UIApplication application, UILocalNotification notification)
    {
        // show an alert
        UIAlertController okayAlertController = UIAlertController.Create(notification.AlertAction, notification.AlertBody, UIAlertControllerStyle.Alert);
        okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

        UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(okayAlertController, true, null);

        // reset our badge
        UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
    }
    ```

1. Necesitamos controlar el caso en el que se inició la notificación debido a una notificación local. Edite el `FinishedLaunching` método `AppDelegate` en para incluir el siguiente fragmento de código:


    ```csharp
    // check for a notification

    if (launchOptions != null)
    {
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
    }
    ```

1. Por último, ejecute la aplicación. En iOS 8 se le pedirá que permita las notificaciones. Haga clic en **Aceptar** y, a continuación, haga clic en el botón **Agregar notificación** . Después de una pausa breve, debería ver el cuadro de diálogo de alerta, tal como se muestra en las siguientes capturas de pantallas:

    ![](local-notifications-in-ios-walkthrough-images/image0.png "Confirmar la capacidad de enviar notificaciones") ![](local-notifications-in-ios-walkthrough-images/image1.png "botón Agregar notificación") ![](local-notifications-in-ios-walkthrough-images/image2.png "el cuadro de diálogo de alerta de notificación")

## <a name="summary"></a>Resumen

En este tutorial se ha mostrado cómo usar las distintas API para crear y publicar notificaciones en iOS. También se ha mostrado cómo actualizar el icono de la aplicación con un distintivo para proporcionar algunos comentarios específicos de la aplicación al usuario.


## <a name="related-links"></a>Vínculos relacionados

- [Notificaciones locales (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/localnotifications)
- [Guía de programación de notificaciones de envío y locales](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
