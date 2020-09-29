---
title: Microsoft Azure y Xamarin
description: En este documento se incluyen vínculos a documentación sobre Servicios conectados en Visual Studio para Mac, Azure Mobile Apps, la autenticación de Active Directory y WebAPI.
ms.prod: xamarin
ms.assetid: 7b9aa8d9-c181-4c33-8ab0-2f56e4dbfc04
author: davidortinau
ms.author: daortin
ms.date: 10/09/2017
ms.openlocfilehash: 9439b33c0e2c8458fd1308003aa9cd93148b6eac
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457320"
---
# <a name="microsoft-azure-and-xamarin"></a>Microsoft Azure y Xamarin

[![Las características de App de Azure Services son fáciles de agregar a las aplicaciones de Xamarin, como el almacenamiento de datos en la nube y las notificaciones de envío entre plataformas.](images/evolve-mikej-azure-sml.png)](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

[Evolucione 2016: desarrollo de aplicaciones conectadas con Azure y Xamarin](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

## <a name="connected-services-in-visual-studio-for-mac"></a>Servicios conectados en Visual Studio para Mac

La nueva característica [servicios conectados](/visualstudio/mac/connected-services) de Visual Studio para Mac ayuda a los desarrolladores a agregar rápida y fácilmente la funcionalidad de Azure a las aplicaciones móviles desde el IDE. Actualmente disponible para pruebas en el canal alfa.

## <a name="azure-app-services"></a>Azure App Services

Hay una colección de [documentación de azure Mobile Apps](~/cross-platform/data-cloud/mobile-apps.md) que le guía por el proceso de implementación del [cliente móvil de Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).
Xamarin también ofrece paquetes NuGet de mensajería de Azure para [iOS](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.iOS/) y [Android](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.Android/) para ayudar a implementar notificaciones de envío entre plataformas.

Configure las aplicaciones en el [portal de App de Azure Services](https://portal.azure.com/) para acceder a Mobile Apps, API Web, almacenamiento y mucho más. Obtenga información sobre el modo en que los [servicios de aplicaciones son diferentes](https://azure.microsoft.com/updates/whats-new-with-azure-app-service/) y vea [estos vídeos de Microsoft](https://azure.microsoft.com/campaigns/azure-march-announcement/).

## <a name="active-directory-authentication"></a>Autenticación de Active Directory

[Azure Active Directory](~/cross-platform/data-cloud/active-directory/index.md) puede usarse para iniciar sesión de usuarios en aplicaciones de Xamarin. Las aplicaciones pueden tener acceso a servicios adicionales como Office 365.

## <a name="webapi"></a>WebAPI

La API Web de Microsoft expone una interfaz similar a REST que las aplicaciones de Xamarin pueden usar fácilmente.
Puede poner en marcha fácilmente un [sitio web de Azure](https://trywebsites.azurewebsites.net/) y crear una aplicación basada en WebAPI para conectarse a las aplicaciones de Xamarin.

### <a name="introduction-to-web-services"></a>[Introducción a los servicios Web](~/cross-platform/data-cloud/web-services/index.md)

En este tutorial se presenta cómo integrar las tecnologías REST, WCF y SOAP Web Service con aplicaciones móviles de Xamarin. Examina varias implementaciones de servicio, evalúa las herramientas y bibliotecas disponibles para integrarlas y proporciona patrones de ejemplo para consumir datos de servicio. Por último, proporciona información general básica sobre la creación de un servicio web RESTful para su consumo con una aplicación móvil de Xamarin.

## <a name="samples"></a>Ejemplos

Además de los [ejemplos de documentación](https://github.com/xamarin/mobile-samples/tree/master/Azure), las siguientes aplicaciones completas muestran varias características de Azure incorporadas en las aplicaciones de Xamarin:

- Deportes [: aplicación](https://github.com/xamarin/Sport) de seguimiento de la liga que usa el almacenamiento de datos & las notificaciones de envío.
- [Momentos](https://github.com/pierceboggan/Moments) : uso compartido de fotografías instantáneo que usa Azure Storage para las imágenes.
- [XAMARIN CRM](https://github.com/xamarin/app-crm) : usa la API Web para el back-end.
- Mis [compradores](https://github.com/jamesmontemagno/MyShoppe) : Azure Mobile Apps.

- [eShop](https://github.com/dotnet-architecture/eShopOnContainers) : muestra para la [serie de arquitectura](https://www.microsoft.com/net/learn/architecture) de libros electrónicos.
- Ejemplo de [Azure y IOT](https://azure.microsoft.com/campaigns/mydriving/) de la compilación 2016.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Azure PCL (por @paulbatum ) (ejemplo)](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Azure Portal](https://azure.microsoft.com/)
- [Cliente móvil para Xamarin (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)