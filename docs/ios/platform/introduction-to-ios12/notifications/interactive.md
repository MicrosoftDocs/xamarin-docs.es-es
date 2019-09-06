---
title: Interfaces de usuario de notificación interactiva en Xamarin. iOS
description: Con iOS 12, es posible crear interfaces de usuario interactivas para las notificaciones locales y remotas. En esta guía se describe cómo usar estas características con Xamarin. iOS.
ms.prod: xamarin
ms.assetid: E3562E1B-E0EF-4C99-9F51-59DE22AFDE46
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/04/2018
ms.openlocfilehash: 572b369755e37f123fbfdf5850a635e7ada12a9b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291237"
---
# <a name="interactive-notification-user-interfaces-in-xamarinios"></a>Interfaces de usuario de notificación interactiva en Xamarin. iOS

[Las extensiones de contenido de notificación](~/ios/platform/user-notifications/advanced-user-notifications.md), introducidas en iOS 10, permiten crear interfaces de usuario personalizadas para las notificaciones. A partir de iOS 12, las interfaces de usuario de notificación pueden contener elementos interactivos como botones y controles deslizantes.

## <a name="sample-app-redgreennotifications"></a>Aplicación de ejemplo: RedGreenNotifications

La aplicación de ejemplo [RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications) contiene una extensión de contenido de notificación con una interfaz de usuario interactiva.

Los fragmentos de código de esta guía proceden de este ejemplo.

## <a name="notification-content-extension-infoplist-file"></a>Archivo info. plist de la extensión de contenido de notificación

En la aplicación de ejemplo, el archivo **info. plist** del proyecto **RedGreenNotificationsContentExtension** contiene la configuración siguiente:

```xml
<!-- ... -->
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>UNNotificationExtensionCategory</key>
        <array>
            <string>red-category</string>
            <string>green-category</string>
        </array>
        <key>UNNotificationExtensionUserInteractionEnabled</key>
        <true/>
        <key>UNNotificationExtensionDefaultContentHidden</key>
        <true/>
        <key>UNNotificationExtensionInitialContentSizeRatio</key>
        <real>0.6</real>
    </dict>
    <key>NSExtensionMainStoryboard</key>
    <string>MainInterface</string>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.usernotifications.content-extension</string>
    <key></key>
    <true/>
</dict>
<!-- ... -->
```

Tenga en cuenta las siguientes características:

- La `UNNotificationExtensionCategory` matriz especifica el tipo de categorías de notificación que controla la extensión de contenido.
- Con el fin de admitir contenido interactivo, la extensión de contenido de `UNNotificationExtensionUserInteractionEnabled` notificación establece `true`la clave en.
- La `UNNotificationExtensionInitialContentSizeRatio` clave especifica la relación de alto/ancho inicial para la interfaz de la extensión de contenido.

## <a name="interactive-interface"></a>Interfaz interactiva

**MainInterface. Storyboard**, que define la interfaz para una extensión de contenido de notificación, es un guion gráfico estándar que contiene un controlador de vista único. En la aplicación de ejemplo, el controlador de vista es `NotificationViewController`de tipo y contiene una vista de imagen, tres botones y un control deslizante. El guión gráfico asocia estos controles a los controladores definidos en **NotificationViewController.CS**:

- El controlador del botón **Iniciar aplicación** llama `PerformNotificationDefaultAction` al método de `ExtensionContext`acción en, que inicia la aplicación:

    ```csharp
    partial void HandleLaunchAppButtonTap(UIButton sender)
    {
        ExtensionContext.PerformNotificationDefaultAction();
    }
    ```

    En la aplicación, el centro `Delegate` de notificaciones de usuario (en la aplicación de ejemplo `AppDelegate`,) puede responder a la interacción `DidReceiveNotificationResponse` en el método:

    ```csharp
    [Export("userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:")]
    public void DidReceiveNotificationResponse(UNUserNotificationCenter center, UNNotificationResponse response, System.Action completionHandler)
    {
        if (response.IsDefaultAction)
        {
            Console.WriteLine("ACTION: Default");
            // ...
    ```

- El controlador del botón de notificación `DismissNotificationContentExtension` para `ExtensionContext` **descartar** llama a en, que cierra la notificación:

    ```csharp
    partial void HandleDismissNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
    }
    ```

- El controlador del botón **quitar notificación** descarta la notificación y la quita del centro de notificaciones:

    ```csharp
    partial void HandleRemoveNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
        UNUserNotificationCenter.Current.RemoveDeliveredNotifications(new string[] { notification.Request.Identifier });
    }
    ```

- El método que controla los cambios de valor en el control deslizante actualiza el alfa de la imagen que se muestra en la interfaz de la notificación:

    ```csharp
    partial void HandleSliderValueChanged(UISlider sender)
    {
        Xamagon.Alpha = sender.Value;
    }
    ```

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo: RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Marco de notificaciones de usuario en Xamarin. iOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Novedades de las notificaciones de usuario (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Prácticas recomendadas y novedades en las notificaciones de usuario (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Notificaciones enriquecidas (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/817/)
- [Generación de una notificación remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
