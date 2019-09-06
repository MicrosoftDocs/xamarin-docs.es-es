---
title: Administración de notificaciones en Xamarin. iOS
description: En este documento se describe cómo usar Xamarin. iOS para aprovechar las nuevas características de administración de notificaciones introducidas en iOS 12.
ms.prod: xamarin
ms.assetid: F1D90729-F85A-425B-B633-E2FA38FB4A0C
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/04/2018
ms.openlocfilehash: b6c6baad2cbd923bde4dab3766040b5df4351787
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282114"
---
# <a name="notification-management-in-xamarinios"></a>Administración de notificaciones en Xamarin. iOS

En iOS 12, el sistema operativo puede vincular en profundidad desde el centro de notificaciones y la aplicación de configuración hasta la pantalla de administración de notificaciones de una aplicación. Esta pantalla debe permitir a los usuarios participar y excluir los distintos tipos de notificaciones que envía la aplicación.

## <a name="sample-app-redgreennotifications"></a>Aplicación de ejemplo: RedGreenNotifications

Para ver un ejemplo de cómo funciona la administración de notificaciones, eche un vistazo a la aplicación de ejemplo [RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications) .

Esta aplicación de ejemplo envía dos tipos de notificaciones: rojo y verde, y proporciona una pantalla que permite a los usuarios participar o no de cualquier tipo.

Los fragmentos de código de esta guía proceden de esta aplicación de ejemplo.

## <a name="notification-management-screen"></a>Pantalla de Notification Management

En la aplicación de ejemplo `ManageNotificationsViewController` , define una interfaz de usuario que permite a los usuarios habilitar y deshabilitar de forma independiente las notificaciones rojas y las notificaciones verdes. Es un estándar[`UIViewController`](xref:UIKit.UIViewController)
que contiene [`UISwitch`](xref:UIKit.UISwitch) un para cada tipo de notificación. Al alternar el conmutador para cualquier tipo de notificación se guarda, en valores predeterminados del usuario, la preferencia del usuario para ese tipo de notificación:

```csharp
partial void HandleRedNotificationsSwitchValueChange(UISwitch sender)
{
    NSUserDefaults.StandardUserDefaults.SetBool(sender.On, RedNotificationsEnabledKey);
}
```

> [!NOTE]
> En la pantalla Notification Management también se comprueba si el usuario ha deshabilitado o no las notificaciones por completo de la aplicación. Si es así, oculta los alternancias para los tipos de notificación individuales. Para ello, en la pantalla Notification Management:
>
> - Llama [`UNUserNotificationCenter.Current.GetNotificationSettingsAsync`](xref:UserNotifications.UNUserNotificationCenter.GetNotificationSettingsAsync) a y examina la [`AuthorizationStatus`](xref:UserNotifications.UNNotificationSettings.AuthorizationStatus) propiedad.
> - Oculta los alternancias para los tipos de notificación individuales si las notificaciones se han deshabilitado por completo para la aplicación.
> - Vuelve a comprobar si se han deshabilitado las notificaciones cada vez que la aplicación se mueve al primer plano, ya que el usuario puede habilitar o deshabilitar las notificaciones en la configuración de iOS en cualquier momento.

La clase de la `ViewController` aplicación de ejemplo, que envía las notificaciones, comprueba las preferencias del usuario antes de enviar una notificación local para asegurarse de que la notificación es de un tipo que el usuario realmente desea recibir:

```csharp
partial void HandleTapRedNotificationButton(UIButton sender)
{
    bool redEnabled = NSUserDefaults.StandardUserDefaults.BoolForKey(ManageNotificationsViewController.RedNotificationsEnabledKey);
    if (redEnabled)
    {
        // ...
```

## <a name="deep-link"></a>Vínculo profundo

iOS: vínculos profundos a la pantalla de administración de notificaciones de la aplicación desde el centro de notificaciones y la configuración de notificaciones de la aplicación en la aplicación de configuración. Para facilitar esto, una aplicación debe:

- Indique que hay disponible una pantalla de administración de notificaciones pasando `UNAuthorizationOptions.ProvidesAppNotificationSettings` a la solicitud de autorización de notificación de la aplicación.
- Implemente `OpenSettings` el método [`IUNUserNotificationCenterDelegate`](xref:UserNotifications.IUNUserNotificationCenterDelegate)desde.

### <a name="authorization-request"></a>Solicitud de autorización

Para indicar al sistema operativo que hay disponible una pantalla de administración de notificaciones, una aplicación debe `UNAuthorizationOptions.ProvidesAppNotificationSettings` pasar la opción (junto con cualquier otra opción de entrega de notificación que `RequestAuthorization` necesite) al `UNUserNotificationCenter`método en.

Por ejemplo, en la aplicación de `AppDelegate`ejemplo:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.ProvidesAppNotificationSettings | UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional;
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        // ...
```

### <a name="opensettings-method"></a>Método OpenSettings

El `OpenSettings` método, al que llama el sistema para establecer un vínculo profundo a la pantalla de administración de notificaciones de la aplicación, debe navegar directamente al usuario a esa pantalla.

En la aplicación de ejemplo, este método realiza el segue en `ManageNotificationsViewController` si es necesario:

```csharp
[Export("userNotificationCenter:openSettingsForNotification:")]
public void OpenSettings(UNUserNotificationCenter center, UNNotification notification)
{
    var navigationController = Window.RootViewController as UINavigationController;
    if (navigationController != null)
    {
        var currentViewController = navigationController.VisibleViewController;
        if (currentViewController is ViewController)
        {
            currentViewController.PerformSegue(ManageNotificationsViewController.ShowManageNotificationsSegue, this);
        }

    }
}
```

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo: RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Marco de notificaciones de usuario en Xamarin. iOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Novedades de las notificaciones de usuario (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Prácticas recomendadas y novedades en las notificaciones de usuario (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Generación de una notificación remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
