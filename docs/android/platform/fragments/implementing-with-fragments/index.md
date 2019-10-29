---
title: 'Implementación de fragmentos: tutorial'
description: En este artículo se explica cómo usar fragmentos para desarrollar aplicaciones de Xamarin. Android.
ms.topic: tutorial
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: b601fc37cc75dcd43c3688de8d302f0a47a06b35
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027422"
---
# <a name="implementing-fragments---walkthrough"></a>Implementación de fragmentos: tutorial

_Los fragmentos son componentes modulares e independientes que pueden ayudar a abordar la complejidad de las aplicaciones Android que tienen como destino dispositivos con diversos tamaños de pantalla. En este artículo se explica cómo crear y usar fragmentos al desarrollar aplicaciones de Xamarin. Android._

## <a name="overview"></a>Información general

En esta sección, recorreremos cómo crear y usar fragmentos en una aplicación de Xamarin. Android. Esta aplicación mostrará los títulos de varios reproducciones de William Shakespeare en una lista. Cuando el usuario pulsa el título de una reproducción, la aplicación mostrará una oferta de la misma en una actividad independiente:

[![aplicación que se ejecuta en un teléfono Android en modo vertical](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

Cuando el teléfono se gira en el modo horizontal, la apariencia de la aplicación cambiará: la lista de reproducciones y las comillas se mostrarán en la misma actividad. Cuando se selecciona reproducir, la oferta se mostrará en la misma actividad:

[![aplicación que se ejecuta en un teléfono Android en modo horizontal](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

Por último, si la aplicación se ejecuta en una tableta:

[![aplicación que se ejecuta en una tableta Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)

Esta aplicación de ejemplo se puede adaptar fácilmente a los distintos factores de forma y orientaciones con cambios mínimos en el código mediante fragmentos y [diseños alternativos](/xamarin/android/app-fundamentals/resources-in-android/alternate-resources).

Los datos de la aplicación se encontrarán en dos matrices de cadenas codificadas de forma rígida C# en la aplicación como matrices de cadenas. Cada una de las matrices actuará como origen de datos para un fragmento.  Una matriz contendrá el nombre de algunas jugadas por Shakespeare y la otra matriz contendrá una comilla de esa jugada. Cuando se inicia la aplicación, se muestran los nombres de reproducción en un `ListFragment`. Cuando el usuario hace clic en una reproducción en el `ListFragment`, la aplicación iniciará otra actividad que mostrará la cotización.

La interfaz de usuario de la aplicación constará de dos diseños, uno para vertical y otro para el modo horizontal. En tiempo de ejecución, Android determinará qué diseño se va a cargar en función de la orientación del dispositivo y proporcionará ese diseño a la actividad que se va a representar. Toda la lógica para responder a los clics del usuario y mostrar los datos se incluirá en fragmentos. Las actividades de la aplicación solo existen como contenedores que hospedarán los fragmentos.

Este tutorial se dividirá en dos guías. La [primera parte](./walkthrough.md) se centrará en las partes principales de la aplicación. Se creará un único conjunto de diseños (optimizado para el modo vertical), junto con dos fragmentos y dos actividades:

1. `MainActivity` &nbsp; esta es la actividad de inicio de la aplicación.
1. `TitlesFragment` &nbsp; este fragmento mostrará una lista de los títulos de las reproducciones escritas por William Shakespeare. Se hospedará en `MainActivity`.
1. `PlayQuoteActivity` &nbsp; `TitlesFragment` iniciará el `PlayQuoteActivity` en respuesta al usuario que selecciona una reproducción en `TitlesFragment`.
1. `PlayQuoteFragment` &nbsp; este fragmento mostrará una comilla de un juego de William Shakespeare. Se hospedará en `PlayQuoteActivity`.

En la [segunda parte de este tutorial](./walkthrough-landscape.md) se explica cómo agregar un diseño alternativo (optimizado para el modo horizontal) que mostrará ambos fragmentos en la pantalla. Además, se realizarán algunos cambios de código secundarios en el código para que la aplicación adapte su comportamiento al número de fragmentos que se muestran simultáneamente en la pantalla.

## <a name="related-links"></a>Vínculos relacionados

- [FragmentsWalkthrough (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fragmentswalkthrough)
- [Información general del diseñador](~/android/user-interface/android-designer/index.md)
- [Implementar fragmentos](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Paquete de soporte](https://developer.android.com/sdk/compatibility-library.html)
