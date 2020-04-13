---
title: Xamarin.Forms y servicios web
description: Esta guía explica cómo comunicarse con diferentes servicios web para proporcionar la funcionalidad de creación, lectura, actualización y eliminación (CRUD) a una aplicación de Xamarin.Forms. Los temas tratados incluyen la comunicación con servicios ASMX, servicios WCF, servicios REST.
ms.prod: xamarin
ms.assetid: 8B360BDA-E4E3-4A3F-9004-0E35362F49F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2019
ms.openlocfilehash: 799a0a97f7c1a212b10dc62f8b3e7bd2cf2060c8
ms.sourcegitcommit: bf3b5925018e1bd6a00505e9f37500761b66809d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2020
ms.locfileid: "80395454"
---
# <a name="xamarinforms-and-web-services"></a>Xamarin.Forms y servicios web

## <a name="introduction"></a>[Introducción](introduction.md)

En este artículo se proporciona un tutorial de la aplicación de ejemplo Xamarin.Forms que muestra cómo comunicarse con diferentes servicios web. Los temas tratados incluyen la anatomía de la aplicación, las páginas, el modelo de datos y la invocación de operaciones de servicio web.

## <a name="consume-an-aspnet-web-service-asmx"></a>[Consumo de un servicio web ASP.NET (ASMX)](~/xamarin-forms/data-cloud/web-services/asmx.md)

ASP.NET Web Services (ASMX) proporcionan la capacidad de crear servicios web que envían mensajes a través de HTTP mediante el Protocolo simple de acceso a objetos (SOAP). SOAP es un protocolo independiente de la plataforma e independiente del lenguaje para crear y acceder a servicios web. Los consumidores de un servicio ASMX no necesitan saber nada sobre la plataforma, el modelo de objetos o el lenguaje de programación utilizado para implementar el servicio. Solo necesitan entender cómo enviar y recibir mensajes SOAP. En este artículo se muestra cómo consumir un servicio web ASMX desde una aplicación de Xamarin.Forms.

## <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>[Consumir un servicio web de Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/web-services/wcf.md)

WCF es el marco unificado de Microsoft para crear aplicaciones orientadas a servicios. Permite a los desarrolladores crear aplicaciones distribuidas seguras, confiables, transaccionales e interoperables. Hay diferencias entre ASP.NET Web Services (ASMX) y WCF, pero es importante comprender que WCF admite las mismas capacidades que proporciona ASMX: mensajes SOAP a través de HTTP. En este artículo se muestra cómo consumir un servicio SOAP de WCF desde una aplicación de Xamarin.Forms.

## <a name="consume-a-restful-web-service"></a>[Consumir un servicio web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)

Representational State Transfer (REST) es un estilo arquitectónico para la construcción de servicios web. Las solicitudes REST se realizan a través de HTTP utilizando los mismos verbos HTTP que los exploradores web utilizan para recuperar páginas web y enviar datos a los servidores. En este artículo se muestra cómo consumir un servicio web RESTful desde una aplicación de Xamarin.Forms.
