---
title: Tecnologías de notificación en desuso en Xamarin. iOS
description: En este documento se describen las tecnologías de notificación de iOS que han quedado en desuso en favor del marco de trabajo de notificaciones de usuario, introducidos en iOS 10.
ms.prod: xamarin
ms.assetid: 20C4F6E5-56DF-4A85-BBF0-E38C88586307
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/07/2016
ms.openlocfilehash: af9f4d606dbe258b224d09f68f9b65d6f56ab28f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653895"
---
# <a name="deprecated-notification-technologies-in-xamarinios"></a>Tecnologías de notificación en desuso en Xamarin. iOS

En esta sección se muestra cómo implementar notificaciones de envío y locales en Xamarin. iOS. Se explican los distintos elementos de la interfaz de usuario de una notificación de iOS y se describen las API implicadas en la creación y visualización de una notificación.

> [!IMPORTANT]
> La información de esta sección se refiere a iOS 9 y versiones anteriores, se ha dejado aquí para admitir versiones anteriores de iOS. Para iOS 10 y versiones posteriores, consulte la [Guía del marco de trabajo](~/ios/platform/user-notifications/index.md) de notificaciones de usuario para admitir la notificación local y remota en un dispositivo iOS.

## <a name="sections"></a>Secciones

<a name="Local Notifications In iOS" />

## <a name="local-notifications-in-ioslocal-notifications-in-iosmd"></a>[Notificaciones locales en iOS](local-notifications-in-ios.md)

En esta sección se explica cómo implementar notificaciones locales en Xamarin. iOS. Se explican los distintos elementos de la interfaz de usuario de una notificación de iOS y se describen las API implicadas en la creación y visualización de una notificación.

<a name="Local Notifications Walkthrough" />

## <a name="walkthrough---using-local-notifications-in-xamarinioslocal-notifications-in-ios-walkthroughmd"></a>[Tutorial: uso de notificaciones locales en Xamarin.iOS](local-notifications-in-ios-walkthrough.md)

En esta sección veremos cómo usar las notificaciones locales en una aplicación de Xamarin. iOS. Se mostrarán los aspectos básicos de la creación y publicación de una notificación que mostrará una alerta cuando la reciba la aplicación.

<a name="Remote Notifications In iOS" />

## <a name="remote-notifications-in-iosremote-notifications-in-iosmd"></a>[Notificaciones remotas en iOS](remote-notifications-in-ios.md)

En esta sección se tratarán las notificaciones de envío en iOS. Presenta el servicio de puerta de enlace de notificaciones push de Apple (APNS) y el rol que desempeña en las notificaciones de publicación para las aplicaciones de iOS. Se explicará cómo crear los certificados de seguridad necesarios para habilitar las notificaciones de envío y discutir. Por último, en esta sección se describen algunas de las tareas de mantenimiento que los servidores de aplicaciones deben realizar para realizar un seguimiento de los dispositivos móviles del cliente.

## <a name="related-links"></a>Vínculos relacionados

- [Notificaciones (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/notifications)
