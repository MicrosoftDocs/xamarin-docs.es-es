---
title: Preceder al desarrollo de aplicaciones empresariales
description: En este capítulo se proporciona un prefijo a los patrones de aplicaciones empresariales mediante Xamarin.Forms .
ms.prod: xamarin
ms.assetid: fbf32a44-1d33-4e16-a904-dc7ee5991e7c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6ea63fc483025fc6f9b0c7f379b6dfdc6ca30de8
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84198084"
---
# <a name="preface-to-enterprise-app-development"></a>Preceder al desarrollo de aplicaciones empresariales

En este libro electrónico se proporcionan instrucciones sobre la creación de aplicaciones empresariales multiplataforma con Xamarin.Forms . Xamarin.Formses un kit de herramientas de interfaz de usuario multiplataforma que permite a los desarrolladores crear fácilmente diseños de interfaz de usuario nativa que se pueden compartir entre plataformas, como iOS, Android y el Plataforma universal de Windows (UWP). Proporciona una solución completa para aplicaciones de negocio a empleado (B2E), de negocio a negocio (B2B) y de negocio a consumidor (B2C), lo que proporciona la capacidad de compartir código en todas las plataformas de destino y ayudar a reducir el costo total de propiedad (TCO).

En esta guía se proporcionan instrucciones arquitectónicas para desarrollar aplicaciones empresariales adaptables, mantenibles y comprobables Xamarin.Forms . Se proporciona orientación sobre cómo implementar MVVM, la inserción de dependencias, la navegación, la validación y la administración de la configuración, mientras se mantiene el acoplamiento flexible. Además, también hay instrucciones sobre cómo realizar la autenticación y la autorización con IdentityServer, el acceso a los datos de microservicios en contenedor y las pruebas unitarias.

La guía incluye el código fuente de la [aplicación móvil de eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Mobile)y el código fuente de la [aplicación de referencia de eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers). La aplicación móvil eShopOnContainers es una aplicación empresarial multiplataforma desarrollada con Xamarin.Forms , que se conecta a una serie de microservicios en contenedores conocido como la aplicación de referencia eShopOnContainers. Sin embargo, la aplicación móvil eShopOnContainers puede configurarse para consumir datos de servicios ficticios para aquellos que quieren evitar la implementación de microservicios en contenedores.

## <a name="whats-left-out-of-this-guides-scope"></a>Lo que queda fuera del ámbito de esta guía

Esta guía está dirigida a los lectores que ya están familiarizados con Xamarin.Forms . Para obtener una introducción detallada a Xamarin.Forms , vea la [ Xamarin.Forms documentación](~/xamarin-forms/index.yml)y [crear Mobile Apps con Xamarin.Forms ](https://aka.ms/xamformsebook).

La guía es complementaria a los [microservicios de .net: arquitectura para aplicaciones .net en contenedores](https://aka.ms/microservicesebook), que se centra en el desarrollo y la implementación de microservicios en contenedores. Otras guías que merece la pena leer incluyen la [arquitectura y el desarrollo de aplicaciones web modernas con ASP.net Core y Microsoft Azure](https://aka.ms/WebAppEbook), el [ciclo de vida de aplicaciones de Docker en contenedor con la plataforma y las herramientas](https://aka.ms/dockerlifecycleebook)de Microsoft, y la [plataforma y las herramientas de Microsoft para el desarrollo de aplicaciones móviles](https://aka.ms/MobAppDev/StndPDF).

## <a name="who-should-use-this-guide"></a>¿Quién debe usar esta guía?

La audiencia de esta guía está dirigida principalmente a desarrolladores y arquitectos que desean aprender a diseñar e implementar aplicaciones empresariales multiplataforma con Xamarin.Forms .

Una audiencia secundaria son responsables de la toma de decisiones técnicas que desean recibir información general sobre la arquitectura y la tecnología antes de decidir qué enfoque elegir para el desarrollo de aplicaciones empresariales multiplataforma con Xamarin.Forms .

## <a name="how-to-use-this-guide"></a>Cómo usar esta guía

Esta guía se centra en la creación de aplicaciones empresariales multiplataforma con Xamarin.Forms . Como tal, se debe leer en su totalidad para proporcionar una base de conocimiento de esas aplicaciones y sus consideraciones técnicas. La guía, junto con su aplicación de ejemplo, también puede servir como punto de partida o como referencia para crear una nueva aplicación empresarial. Use la aplicación de ejemplo asociada como plantilla para la nueva aplicación o para ver cómo organizar las partes de componentes de una aplicación. Después, consulte esta guía para obtener instrucciones arquitectónicas.

No dude en reenviar esta guía a los miembros del equipo para ayudar a garantizar una comprensión común del desarrollo de aplicaciones empresariales entre plataformas mediante Xamarin.Forms . Hacer que todo el mundo trabaje con un conjunto común de terminología y principios subyacentes le ayudará a garantizar una aplicación coherente de los patrones y prácticas de arquitectura.

## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (2 MB PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
