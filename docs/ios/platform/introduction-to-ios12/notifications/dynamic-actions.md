---
title: Botones de acción de notificación dinámica en Xamarin. iOS
description: Con iOS 12, una extensión de contenido de notificación puede Agregar, quitar y actualizar los botones de acción que se muestran junto con una notificación. En este documento se describe cómo usar los botones de acción de notificación dinámica con Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 6B34AD78-5117-42D0-B6E7-C8B4B453EAFF
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/04/2018
ms.openlocfilehash: 5be233e9b07069dc7c9842a3ddd00e7d46d9c22f
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291283"
---
# <a name="dynamic-notification-action-buttons-in-xamarinios"></a>Botones de acción de notificación dinámica en Xamarin. iOS

En iOS 12, las notificaciones pueden agregar, quitar y actualizar dinámicamente sus botones de acción asociados. Esta personalización permite proporcionar a los usuarios acciones que son directamente relevantes para el contenido de la notificación y la interacción del usuario con ella.

## <a name="sample-app-redgreennotifications"></a>Aplicación de ejemplo: RedGreenNotifications

Los fragmentos de código de esta guía proceden de la aplicación de ejemplo [RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications) , que muestra cómo usar Xamarin. iOS para trabajar con botones de acción de notificación en iOS 12.

Esta aplicación de ejemplo envía dos tipos de notificaciones locales: rojo y verde.
Después de que la aplicación envíe una notificación, use la función táctil 3D para ver su interfaz de usuario personalizada. A continuación, use los botones de acción de la notificación para girar la imagen que se muestra. A medida que gira la imagen, aparece un botón **restablecer giro** y desaparece según sea necesario.

Los fragmentos de código de esta guía proceden de esta aplicación de ejemplo.

## <a name="default-action-buttons"></a>Botones de acción predeterminados

La categoría de una notificación determina sus botones de acción predeterminados.

Crear y registrar categorías de notificación mientras se inicia una aplicación.
Por ejemplo, en la [aplicación de ejemplo](#sample-app-redgreennotifications), `FinishedLaunching` el método `AppDelegate` de hace lo siguiente:

- Define una categoría para las notificaciones rojas y otra para las notificaciones verdes
- Registra estas categorías mediante una llamada a la[`SetNotificationCategories`](xref:UserNotifications.UNUserNotificationCenter.SetNotificationCategories*)
método de`UNUserNotificationCenter`
- Adjunta un solo[`UNNotificationAction`](xref:UserNotifications.UNNotificationAction)
a cada categoría

En el código de ejemplo siguiente se muestra cómo funciona:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional | UNAuthorizationOptions.ProvidesAppNotificationSettings;
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        // ...
        var rotateTwentyDegreesAction = UNNotificationAction.FromIdentifier("rotate-twenty-degrees-action", "Rotate 20°", UNNotificationActionOptions.None);

        var redCategory = UNNotificationCategory.FromIdentifier(
            "red-category",
            new UNNotificationAction[] { rotateTwentyDegreesAction },
            new string[] { },
            UNNotificationCategoryOptions.CustomDismissAction
        );

        var greenCategory = UNNotificationCategory.FromIdentifier(
            "green-category",
            new UNNotificationAction[] { rotateTwentyDegreesAction },
            new string[] { },
            UNNotificationCategoryOptions.CustomDismissAction
        );

        var set = new NSSet<UNNotificationCategory>(redCategory, greenCategory);
        center.SetNotificationCategories(set);
    });
    // ...
}
```

Según este código, cualquier notificación cuyo[`Content.CategoryIdentifier`](xref:UserNotifications.UNNotificationContent.CategoryIdentifier)
el valor de "categoría roja" o "verde-Categoría" muestra, de forma predeterminada, un botón de acción **girar 20 °** .

## <a name="in-app-handling-of-notification-action-buttons"></a>Control en la aplicación de los botones de acción de notificación

`UNUserNotificationCenter`tiene una `Delegate` propiedad de tipo [`IUNUserNotificationCenterDelegate`](xref:UserNotifications.IUNUserNotificationCenterDelegate).

En la aplicación de ejemplo `AppDelegate` , se establece como delegado del centro de notificaciones `FinishedLaunching`de usuario en:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = // ...
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        center.Delegate = this;
        // ...
```

A continuación `AppDelegate` , implementa[`DidReceiveNotificationResponse`](xref:UserNotifications.UNUserNotificationCenterDelegate_Extensions.DidReceiveNotificationResponse*)
para controlar los grifos de botones de acción:

```csharp
[Export("userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:")]
public void DidReceiveNotificationResponse(UNUserNotificationCenter center, UNNotificationResponse response, System.Action completionHandler)
{
    if (response.IsDefaultAction)
    {
        Console.WriteLine("ACTION: Default");
    }
    if (response.IsDismissAction)
    {
        Console.WriteLine("ACTION: Dismiss");
    }
    else
    {
        Console.WriteLine($"ACTION: {response.ActionIdentifier}");
    }

    completionHandler();
        }
```

Esta implementación de `DidReceiveNotificationResponse` no controla el botón de acción **rotar 20 °** de la notificación. En su lugar, la extensión de contenido de la notificación controla las pulsaciones de este botón. En la siguiente sección se describe con más detalle el control de los botones de acción de notificación.

## <a name="action-buttons-in-the-notification-content-extension"></a>Botones de acción en la extensión de contenido de notificación

Una extensión de contenido de notificación contiene un controlador de vista que define la interfaz personalizada para una notificación.

Este controlador de vista puede usar `GetNotificationActions` los `SetNotificationActions` métodos y en su[`ExtensionContext`](xref:UIKit.UIViewController.ExtensionContext)
propiedad para tener acceso a los botones de acción de la notificación y modificarlos.

En la aplicación de ejemplo, el controlador de vista de la extensión de contenido de notificación modifica los botones de acción solo cuando responde a una pulsación de un botón de acción ya existente.

> [!NOTE]
> Una extensión de contenido de notificación puede responder a una TAP del botón de acción en [`DidReceiveNotificationResponse`](xref:UserNotificationsUI.UNNotificationContentExtension_Extensions.DidReceiveNotificationResponse*) el método de su controlador de vista, declarado como parte de [IUNNotificationContentExtension](xref:UserNotificationsUI.IUNNotificationContentExtension).
>
> Aunque comparte un nombre con el `DidReceiveNotificationResponse` método [descrito anteriormente](#in-app-handling-of-notification-action-buttons), se trata de un método diferente.
>
> Una vez que una extensión de contenido de notificación finaliza el procesamiento de una derivación de botones, puede elegir si quiere indicar a la aplicación principal que controle esa misma punteo de botón. Para ello, debe pasar un valor adecuado de [UNNotificationContentExtensionResponseOption](xref:UserNotificationsUI.UNNotificationContentExtensionResponseOption) a su controlador de finalización:
>
> - `Dismiss`indica que se debe descartar la interfaz de notificación y que no es necesario que la aplicación principal controle la derivación de botones.
> - `DismissAndForwardAction`indica que se debe descartar la interfaz de notificación y que la aplicación principal también debe controlar la derivación de botones.
> - `DoNotDismiss`indica que no se debe descartar la interfaz de notificación y que no es necesario que la aplicación principal controle la derivación de botones.

El método de la `DidReceiveNotificationResponse` extensión de contenido determina qué botón de acción se ha punteado, gira la imagen en la interfaz de la notificación y muestra u oculta un botón de acción de **restablecimiento** :

```csharp
[Export("didReceiveNotificationResponse:completionHandler:")]
public void DidReceiveNotificationResponse(UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
{
    var rotationAction = ExtensionContext.GetNotificationActions()[0];

    if (response.ActionIdentifier == "rotate-twenty-degrees-action")
    {
        rotationButtonTaps += 1;

        double radians = (20 * rotationButtonTaps) * (2 * Math.PI / 360.0);
        Xamagon.Transform = CGAffineTransform.MakeRotation((float)radians);

        // 9 rotations * 20 degrees = 180 degrees. No reason to
        // show the reset rotation button when the image is half
        // or fully rotated.
        if (rotationButtonTaps % 9 == 0)
        {
            ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction });
        }
        else if (rotationButtonTaps % 9 == 1)
        {
            var resetRotationAction = UNNotificationAction.FromIdentifier("reset-rotation-action", "Reset rotation", UNNotificationActionOptions.None);
            ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction, resetRotationAction });
        }
    }

    if (response.ActionIdentifier == "reset-rotation-action")
    {
        rotationButtonTaps = 0;

        double radians = (20 * rotationButtonTaps) * (2 * Math.PI / 360.0);
        Xamagon.Transform = CGAffineTransform.MakeRotation((float)radians);

        ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction });
    }

    completionHandler(UNNotificationContentExtensionResponseOption.DoNotDismiss);
}
```

En este caso, el método pasa `UNNotificationContentExtensionResponseOption.DoNotDismiss` a su controlador de finalización. Esto significa que la interfaz de la notificación permanecerá abierta.

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo: RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Marco de notificaciones de usuario en Xamarin. iOS](~/ios/platform/user-notifications/index.md)
- [Declarar los tipos de notificación procesables](https://developer.apple.com/documentation/usernotifications/declaring_your_actionable_notification_types?language=objc)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Novedades de las notificaciones de usuario (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Prácticas recomendadas y novedades en las notificaciones de usuario (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Generación de una notificación remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
