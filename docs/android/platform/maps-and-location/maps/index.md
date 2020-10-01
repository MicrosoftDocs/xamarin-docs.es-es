---
title: Cómo usar Google Maps y la ubicación con Xamarin.Android
description: En este artículo se describe cómo usar los mapas y la ubicación con Xamarin.Android. Abarca todo, desde la utilización de la aplicación de mapas integrada hasta el uso directo de la API de Android V2 de Google Maps.
ms.prod: xamarin
ms.assetid: 425E0ED2-5380-6EBE-7059-256B6E9128B8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 11feb8e3e10e12bfb2c647182ec4ba953ab34e24
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456904"
---
# <a name="how-to-use-google-maps-and-location-with-xamarinandroid"></a>Cómo usar Google Maps y la ubicación con Xamarin.Android

_En este artículo se explica cómo usar los mapas y la ubicación con Xamarin.Android. Abarca todo, desde la utilización de la aplicación de mapas integrada hasta el uso directo de la API de Android V2 de Google Maps._

## <a name="maps-overview"></a>Información general sobre mapas

Las tecnologías de mapas son un complemento omnipresente en los dispositivos móviles. Los equipos de escritorio y portátiles no suelen tener un reconocimiento de ubicación integrado. Por otro lado, los dispositivos móviles usan estas aplicaciones para ubicar los dispositivos y mostrar la información de ubicación cambiante. Android contiene tecnología integrada eficaz que muestra los datos de ubicación en mapas mediante el hardware de ubicación disponible en el dispositivo. En este artículo se describe un espectro de lo que las aplicaciones de mapas de Xamarin.Android ofrecen, incluidas lo siguiente: 

- Usar la aplicación de mapas integrada para agregar rápidamente funcionalidad de mapas.
- Trabajar con la API Maps para controlar la presentación de un mapa.
- Usar diversas técnicas para agregar superposiciones gráficas.

Los temas de esta sección cubren una amplia gama de características de mapas.
En primer lugar, se explica cómo aprovechar la aplicación de mapas integrada de Android y cómo mostrar una vista panorámica de una ubicación. A continuación, se explica cómo usar la API Maps para incorporar características de mapas directamente dentro de una aplicación, que abarcan cómo controlar la posición y la presentación de un mapa, además de cómo agregar superposiciones gráficas.

## <a name="related-links"></a>Vínculos relacionados

- [MapsAndLocationDemo_v3 (ejemplo)](/samples/xamarin/monodroid-samples/mapsandlocationdemo-v3)
- [Ciclo de vida de la actividad](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Obtención de una clave de la API de Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [Lista de intenciones: invocación de aplicaciones de Google en dispositivos Android](https://developer.android.com/guide/appendix/g-app-intents.html)
- [Ubicación y mapas](https://developer.android.com/guide/topics/location/index.html)