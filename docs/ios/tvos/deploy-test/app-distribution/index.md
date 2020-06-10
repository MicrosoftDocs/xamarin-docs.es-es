---
title: Introducción a la distribución de aplicaciones de tvOS
description: En este documento se proporciona información general sobre las técnicas de distribución que están disponibles para la aplicación Xamarin. tvOS y sirve como un puntero a documentos más detallados sobre el tema.
ms.prod: xamarin
ms.assetid: D5E0F446-C083-4E21-9788-FC84D32D00C4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: b311f3121ea6a58975d41b9690e31a44daa0951e
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84573708"
---
# <a name="tvos-app-distribution-overview"></a>Introducción a la distribución de aplicaciones de tvOS

_En este documento se proporciona información general sobre las técnicas de distribución que están disponibles para la aplicación Xamarin. tvOS y sirve como un puntero a documentos más detallados sobre el tema._

Una vez que se ha desarrollado la aplicación de Xamarin. tvOS, el siguiente paso del ciclo de vida de desarrollo de software es distribuir la aplicación a los usuarios, tal como se muestra en la sección resaltada del diagrama siguiente:

[![Información general del ciclo de vida de desarrollo de software](images/publishingdiagram.png)](images/publishingdiagram.png#lightbox)

Apple proporciona las siguientes formas de distribuir una aplicación de tvOS, que es compatible con Xamarin. tvOS:

1. [**La App Store**](#Apple-TV-App-Store-Distribution)
2. [**Interna (Enterprise)**](#In-House-Distribution) 
3. [**Ad hoc**](#Ad_Hoc_Distribution) 

Todos estos escenarios requieren que las aplicaciones se aprovisionen mediante el correspondiente *perfil de aprovisionamiento*. Los perfiles de aprovisionamiento son archivos que contienen información de firma de código, así como la identidad de la aplicación y el mecanismo de distribución previsto. También contienen información sobre en qué dispositivos se puede implementar la aplicación para la distribución que no se realice a través del App Store.

<a name="Apple-TV-App-Store-Distribution"></a>

## <a name="apple-tv-app-store-distribution"></a>Distribución de Apple TV App Store

Esta es la forma principal en que las aplicaciones de tvOS se distribuyen a los consumidores en dispositivos Apple TV. Todas las aplicaciones enviadas a la tienda de aplicaciones de Apple TV requieren la aprobación de Apple.

Las aplicaciones se envían a la App Store a través de un portal llamado *iTunes Connect*. Consulte la guía de [configuración de la aplicación en iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) para obtener información sobre los temas siguientes:

- Administración de contratos, impuestos y banca.
- Creación de un registro de iTunes Connect.
- Administración de vídeos y capturas de pantallas de aplicaciones.
- Administrar el nombre de la aplicación, la descripción, las novedades, las palabras clave y las direcciones URL.
- Mantenimiento de la información general de la aplicación.
- Mantenimiento de la información de Game Center.
- Mantenimiento de la información de revisión de la aplicación.
- Mantenimiento de la información de precios.
- Mantenimiento de la información de compras desde la aplicación.
- Ver las revisiones de la aplicación.

Aunque no se escribe específicamente para tvOS, en este documento se proporciona información sobre cómo configurar y usar este portal para preparar la aplicación para su publicación en el App Store de Apple TV. Muchos de los mismos pasos son necesarios si va a configurar una aplicación de iOS o tvOS.

Una vez que haya completado todos los pasos indicados anteriormente, consulte la [configuración de la aplicación tvOS en iTunes Connect](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) para agregar la información específica de la aplicación tvOS que se requerirá.

Es importante tener en cuenta que solo los desarrolladores que pertenecen al **Programa para desarrolladores de Apple** tienen acceso a iTunes Connect. Los miembros del **Programa para desarrolladores empresariales de Apple** no tienen acceso.

Si tiene problemas para enviar la aplicación Xamarin. tvOS a la tienda de aplicaciones de Apple TV, consulte nuestra guía de [solución de problemas](~/ios/tvos/troubleshooting.md) . Contiene varios problemas conocidos que pueden surgir y cómo resolverlos en Xamarin. tvOS.

Para obtener más información, visite la guía [publicación en la App Store de Apple TV](~/ios/tvos/deploy-test/app-distribution/app-store-publishing.md) .

<a name="In-House-Distribution"></a>

## <a name="in-house-distribution"></a>Distribución interna

A veces denominada *Distribución empresarial*, la distribución interna permite a los miembros del **Programa para desarrolladores empresariales de Apple** distribuir aplicaciones internamente a otros miembros de la misma organización. La distribución interna tiene las ventajas de no requerir una revisión de la App Store y no tener ningún límite en el número de dispositivos en los que se puede instalar una aplicación. Sin embargo, es importante tener en cuenta que los miembros del **Programa para desarrolladores empresariales de Apple****no** tienen acceso a iTunes Connect y, por lo tanto, el licenciatario es responsable de distribuir la aplicación.

Para obtener más información sobre cómo configurar y cómo distribuir la aplicación internamente, consulte la [Guía de distribución interna](~/ios/deploy-test/app-distribution/in-house-distribution.md). Este documento es específico de iOS, pero se usan las mismas técnicas para las aplicaciones de tvOS.

<a name="Ad_Hoc_Distribution"></a>

## <a name="ad-hoc-distribution"></a>Distribución ad hoc

Las aplicaciones de Xamarin. tvOS pueden ser probadas por el usuario mediante la distribución ad hoc, que está disponible tanto en el **programa para desarrolladores de Apple**como en el programa para desarrolladores **empresariales**de Apple, y permite la prueba de hasta 100 dispositivos Apple TV. El mejor caso de uso de la distribución ad hoc es la distribución dentro de la empresa cuando iTunes Connect no es una opción.

Para obtener más información sobre cómo configurar y cómo distribuir la aplicación internamente, consulte la [Guía de distribución ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md). De nuevo, este documento es específico de iOS, pero se usan las mismas técnicas para las aplicaciones de tvOS.

<a name="Summary"></a>

## <a name="summary"></a>Resumen

En este artículo se proporciona una breve descripción de los mecanismos de distribución que están disponibles para las aplicaciones de Xamarin. tvOS. Se presentó la tienda de aplicaciones de Apple TV, la implementación ad hoc y interna, y se proporcionan vínculos a información más detallada.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de la interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
