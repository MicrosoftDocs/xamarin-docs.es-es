---
title: Microsoft Azure Mobile Apps
description: En este documento se proporcionan vínculos a guías que describen cómo compilar una aplicación de Xamarin que está conectada a Azure. Describe cómo trabajar con el componente, los usuarios y las notificaciones de envío de Xamarin Azure.
ms.prod: xamarin
ms.assetid: 7B9AA8D9-C181-4C33-8AB0-2F56E4DBFC03
author: conceptdev
ms.author: crdun
ms.date: 04/02/2017
ms.openlocfilehash: 093e2ad9cf18abecc4eb1b63690c016e8ed21a0c
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227778"
---
# <a name="microsoft-azure-mobile-apps"></a>Microsoft Azure Mobile Apps

_Ejemplos y descargas de código para la documentación de Azure Portal._

<!--
NOTE TO AUTHORS: this page is referenced from
https://azure.microsoft.com/develop/mobile/xamarin/
as https://developer xamarin com/guides/cross-platform/data-cloud/mobile-services/
A redirect has been put in place to /mobile-apps/ HOWEVER the /Resources/ .ZIP files are still located in /mobile-services/ so that the following permalinks don't break

The ZIPs in /Resources/ are also referenced by inbound links
Getting Started http://go.microsoft.com/fwlink/p/?LinkId=331359
Get started with data http://go.microsoft.com/fwlink/p/?LinkId=331302
Get started with push http://go.microsoft.com/fwlink/p/?LinkId=331303
Get started with authentication http://go.microsoft.com/fwlink/p/?LinkId=331328
Get started with Notification Hubs http://go.microsoft.com/fwlink/p/?LinkId=331329
Validate and modify data  http://go.microsoft.com/fwlink/p/?LinkId=331330
-->


Estos vínculos son para la documentación de Xamarin disponible en el sitio web de [Azure Mobile Apps](https://docs.microsoft.com/azure/app-service-mobile/) .
Agregar la funcionalidad de Azure a una aplicación de Xamarin mediante la descarga del [cliente móvil de Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).

## <a name="working-with-the-xamarin-azure-component"></a>Trabajar con el componente de Azure de Xamarin

Documentación general sobre cómo [trabajar con la biblioteca de cliente de Xamarin (componente)](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library) para realizar diversas tareas con Azure Mobile Apps. Esta página contiene muchos fragmentos de código de ejemplo, sin las explicaciones y los ejemplos detallados disponibles en cada uno de los artículos del tutorial que se enumeran a continuación.

## <a name="getting-started"></a>Introducción

En este artículo se proporcionan instrucciones paso a paso para poner en marcha su primera aplicación de Xamarin Azure.
Describe la creación de una nueva aplicación móvil de Azure en el portal y, después, la descarga y ejecución de la aplicación preconfigurada.

- [iOS](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-ios-get-started/)
- [Android](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-android-get-started/)
- [Xamarin.Forms](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started)

<!--
## Validate, Modify and Augment Data in Scripts

Demonstrates how to add server-side scripts to Azure Mobile Services data tables to implement server-side validation and other functionality.

- [iOS](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
- [Android](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
-->

<!--
## Add Paging to Data

A quick example of paging large sets of data using Skip() and Take().

- [iOS](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
- [Android](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
-->

## <a name="get-started-with-users"></a>Introducción a los usuarios

Proporciona instrucciones completas para configurar y codificar una pantalla de inicio de sesión con Azure Mobile Services. Entre los proveedores de autenticación admitidos se incluyen Microsoft, Google, Facebook y Twitter.

- [iOS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-ios-get-started-users/)
- [Android](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-android-get-started-users/)


## <a name="authorize-users-in-scripts"></a>Autorización de usuarios en scripts

Código de ejemplo de back-ends de JavaScript

- [Todo.js](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/personal-table/tables/TodoItem.js#L38)


## <a name="get-started-with-push"></a>Introducción a la instalación

Complete las instrucciones para configurar las notificaciones de envío en los sitios web de Apple y Google y envíe una notificación de envío desde Azure Mobile Services a un dispositivo.

- [iOS](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-ios-get-started-push)
- [Android](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-android-get-started-push)


## <a name="get-started-with-notification-hubs"></a>Introducción a Notification Hubs

Complete las instrucciones para configurar las notificaciones de envío en los sitios web de Apple y Google, configure el centro de notificaciones de Azure y, después, genere notificaciones de envío a los dispositivos.

- [iOS](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started)
- [Android](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm)



## <a name="related-links"></a>Vínculos relacionados

- [GettingStarted (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GettingStarted)
- [GetStartedWithData (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithData)
- [GetStartedWithUsers (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithUsers)
- [GetStartedWithPush (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithPush)
- [NotificationHubs (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs)
- [Cliente móvil de Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [Ruta de aprendizaje de Azure Mobile Apps](https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/)

<!--
- [ValidateModifyData (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/ValidateModifyData)
-->
