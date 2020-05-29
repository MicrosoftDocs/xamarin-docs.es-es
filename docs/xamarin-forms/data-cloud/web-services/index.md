---
title: Xamarin.Formsy servicios Web
description: En esta guía se explica cómo comunicarse con distintos servicios web para proporcionar la funcionalidad de creación, lectura, actualización y eliminación (CRUD) a una Xamarin.Forms aplicación. Entre los temas tratados se incluyen la comunicación con los servicios ASMX, los servicios WCF y los servicios REST.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5b2613b94d2c347d9bc6a94086f869b07ab8a55b
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84131890"
---
# <a name="xamarinforms-and-web-services"></a>Xamarin.Formsy servicios Web

## <a name="introduction"></a>[Introducción](introduction.md)

En este artículo se proporciona un tutorial de la Xamarin.Forms aplicación de ejemplo que muestra cómo comunicarse con distintos servicios Web. Entre los temas tratados se incluyen la anatomía de la aplicación, las páginas, el modelo de datos y la invocación de operaciones del servicio Web.

## <a name="consume-an-aspnet-web-service-asmx"></a>[Consumo de un servicio web ASP.NET (ASMX)](~/xamarin-forms/data-cloud/web-services/asmx.md)

Los servicios Web de ASP.NET (ASMX) proporcionan la capacidad de crear servicios web que envíen mensajes a través de HTTP mediante el Protocolo simple de acceso a objetos (SOAP). SOAP es un protocolo independiente de la plataforma e independiente del lenguaje para crear y obtener acceso a servicios Web. No es necesario que los consumidores de un servicio ASMX sepan nada sobre la plataforma, el modelo de objetos o el lenguaje de programación usado para implementar el servicio. Solo necesitan saber cómo enviar y recibir mensajes SOAP. En este artículo se muestra cómo consumir un servicio Web ASMX desde una Xamarin.Forms aplicación de.

## <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>[Consumir un servicio Web de Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/web-services/wcf.md)

WCF es el marco unificado de Microsoft para la creación de aplicaciones orientadas a servicios. Permite a los desarrolladores compilar aplicaciones distribuidas seguras, con transacciones y de confianza, e interoperables. Hay diferencias entre los servicios Web de ASP.NET (ASMX) y WCF, pero es importante comprender que WCF es compatible con las mismas funcionalidades que ASMX proporciona: mensajes SOAP a través de HTTP. En este artículo se muestra cómo consumir un servicio SOAP de WCF desde una Xamarin.Forms aplicación.

## <a name="consume-a-restful-web-service"></a>[Consumo de un servicio web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)

La transferencia de estado representacional (REST) es un estilo arquitectónico para la creación de servicios Web. Las solicitudes REST se realizan a través de HTTP usando los mismos verbos HTTP que los exploradores web utilizan para recuperar páginas web y enviar datos a los servidores. En este artículo se muestra cómo consumir un servicio web RESTful desde una Xamarin.Forms aplicación.
