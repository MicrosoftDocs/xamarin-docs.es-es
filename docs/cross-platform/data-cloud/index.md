---
title: Microsoft Azure y Xamarin
description: Este documento se vincula a documentación sobre Connected Services en Visual Studio para Mac, Azure Mobile Apps, Autenticación de Active Directory y WebAPI.
ms.prod: xamarin
ms.assetid: 7b9aa8d9-c181-4c33-8ab0-2f56e4dbfc04
author: davidortinau
ms.author: daortin
ms.date: 10/09/2017
ms.openlocfilehash: 2b6dfeb0de0fac59556280609dbf870c23a9298b
ms.sourcegitcommit: 2a8eb8bce427e72d4e7edd06ce432e19f17dcdd7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388530"
---
# <a name="microsoft-azure-and-xamarin"></a>Microsoft Azure y Xamarin

[![](images/evolve-mikej-azure-sml.png "Azure App Services features are easy to add to Xamarin apps, including cloud data storage and cross-platform push notifications")](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

[Evolve 2016: Desarrollo de aplicaciones conectadas mediante Azure y Xamarin](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

## <a name="connected-services-in-visual-studio-for-mac"></a>Servicios conectados en Visual Studio para Mac

La nueva característica [de servicios conectados](connected-services.md) de Visual Studio para Mac ayuda a los desarrolladores a agregar rápida y fácilmente funcionalidad de Azure a aplicaciones móviles desde el IDE. Actualmente disponible para pruebas en el canal Alfa.

## <a name="azure-app-services"></a>Azure App Services

Hay una colección de documentación de [Azure Mobile Apps](~/cross-platform/data-cloud/mobile-apps.md) que le guía a través del proceso de implementación de Azure Mobile [Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).
Xamarin también ofrece paquetes NuGet de azure Messaging para [iOS](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.iOS/) y [Android](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.Android/) para ayudar a implementar notificaciones push entre plataformas.

Configure las aplicaciones en el portal de [Azure App Services](https://portal.azure.com/) para tener acceso a aplicaciones móviles, API web, almacenamiento y mucho más. Obtén información sobre [cómo los servicios](https://azure.microsoft.com/updates/whats-new-with-azure-app-service/) de aplicaciones son diferentes y ver en estos [vídeos de Microsoft](https://azure.microsoft.com/campaigns/azure-march-announcement/).

## <a name="active-directory-authentication"></a>Autenticación de Active Directory

[Azure Active Directory](~/cross-platform/data-cloud/active-directory/index.md) se puede usar para iniciar sesión en los usuarios de las aplicaciones de Xamarin. A continuación, las aplicaciones pueden tener acceso a servicios adicionales como Office 365.

## <a name="webapi"></a>WebAPI

API web de Microsoft expone una interfaz similar a REST que pueden ser fácilmente consumidos por las aplicaciones de Xamarin.
Puede crear fácilmente un [sitio web](https://trywebsites.azurewebsites.net/) de Azure y crear una aplicación basada en WebAPI para conectarse a aplicaciones de Xamarin.

### <a name="introduction-to-web-services"></a>[Introducción a los servicios web](~/cross-platform/data-cloud/web-services/index.md)

En este tutorial se presenta cómo integrar las tecnologías de servicio web REST, WCF y SOAP con aplicaciones móviles de Xamarin. Examina varias implementaciones de servicio, evalúa las herramientas y bibliotecas disponibles para integrarlas y proporciona patrones de ejemplo para consumir datos de servicio. Por último, proporciona una introducción básica de la creación de un servicio web RESTful para el consumo con una aplicación móvil de Xamarin.

## <a name="samples"></a>Ejemplos

Además de los ejemplos de [documentación,](https://github.com/xamarin/mobile-samples/tree/master/Azure)las siguientes aplicaciones completas muestran varias características de Azure incorporadas en aplicaciones de Xamarin:

- [Deporte](https://github.com/xamarin/Sport) – aplicación de seguimiento de ligas deportivas amigable que utiliza el almacenamiento de datos & notificaciones push.
- [Momentos:](https://github.com/pierceboggan/Moments) uso compartido instantáneo de fotos que usa Azure Storage para imágenes.
- [Xamarin CRM:](https://github.com/xamarin/app-crm) usa la API web para el back-end.
- [MyShoppe](https://github.com/jamesmontemagno/MyShoppe) - Azure Mobile Apps.

- [eShop](https://github.com/dotnet-architecture/eShopOnContainers) – ejemplo de la serie de libros electrónicos [Architecture.](https://www.microsoft.com/net/learn/architecture)
- [MyDriving:](https://azure.microsoft.com/campaigns/mydriving/) ejemplo de Azure + IoT de la compilación 2016.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de PCL @paulbatumde Azure (por ) (ejemplo)](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Azure Portal](https://azure.microsoft.com/)
- [Cliente móvil para Xamarin (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
