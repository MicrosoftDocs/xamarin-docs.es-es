---
title: Mensajería de Google
description: Esta sección contiene guías que describen cómo implementar aplicaciones de Xamarin. Android con Google Messaging Services.
ms.prod: xamarin
ms.assetid: 85E8DF92-D160-4763-A7D3-458B4C31635F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/12/2018
ms.openlocfilehash: bfcc526d1787caede4361030f5bf6718a59672b1
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754391"
---
# <a name="google-messaging"></a>Mensajería de Google

_Esta sección contiene guías que describen cómo implementar aplicaciones de Xamarin. Android con Google Messaging Services._

## <a name="firebase-cloud-messagingfirebase-cloud-messagingmd"></a>[Mensajería en la nube de Firebase](firebase-cloud-messaging.md)

Firebase Cloud Messaging (FCM) es un servicio que facilita la mensajería entre aplicaciones móviles y aplicaciones de servidor. FCM es la sucesora de Google de Google Cloud Messaging. En este artículo se proporciona información general sobre cómo funciona FCM y se proporciona un procedimiento paso a paso para adquirir credenciales para que la aplicación pueda usar los servicios de FCM.

## <a name="remote-notifications-with-firebase-cloud-messagingremote-notifications-with-fcmmd"></a>[Notificaciones remotas con mensajería en la nube de Firebase](remote-notifications-with-fcm.md)

En este tutorial se proporciona una explicación paso a paso de cómo usar la mensajería en la nube Firebase para implementar notificaciones remotas (también denominadas notificaciones de envío) en una aplicación de Xamarin. Android. Muestra cómo implementar las distintas clases que se necesitan para las comunicaciones con Firebase Cloud Messaging (FCM), proporciona ejemplos de cómo configurar el manifiesto de Android para el acceso a FCM y muestra la mensajería de nivel inferior mediante Firebase Console.

## <a name="google-cloud-messaginggoogle-cloud-messagingmd"></a>[Google Cloud Messaging](google-cloud-messaging.md)

En esta sección se proporciona información general de alto nivel sobre cómo el Google Cloud Messaging (GCM) enruta los mensajes entre la aplicación y un servidor de aplicaciones, y proporciona un procedimiento paso a paso para adquirir credenciales para que la aplicación pueda usar los servicios de GCM. (Tenga en cuenta que GCM se ha sustituido por FCM).

> [!NOTE]
> GCM se ha sustituido por [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM).
> Las API de cliente y servidor de GCM han [quedado en desuso](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html) y ya no estarán disponibles en cuanto 11 de abril de 2019.

## <a name="remote-notifications-with-google-cloud-messagingremote-notifications-with-gcmmd"></a>[Notificaciones remotas con Google Cloud Messaging](remote-notifications-with-gcm.md)

En esta sección se proporciona una explicación paso a paso de cómo implementar notificaciones remotas en Xamarin. Android mediante Google Cloud Messaging.
Explica los distintos componentes que se deben aprovechar para habilitar Google Cloud Messaging en una aplicación de Android.
