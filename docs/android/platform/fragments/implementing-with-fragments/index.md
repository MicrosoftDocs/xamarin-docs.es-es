---
title: 'Implementación de fragmentos: tutorial'
description: En este artículo se explica cómo usar fragmentos para desarrollar aplicaciones de Xamarin.Android.
ms.topic: tutorial
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: ab680020f62548eed4d1da0e4dbb13434a6d8ce7
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453953"
---
# <a name="implementing-fragments---walkthrough"></a>Implementación de fragmentos: tutorial

_Los fragmentos son componentes modulares e independientes que pueden ayudar a abordar la complejidad de las aplicaciones Android que tienen como destino dispositivos con diversos tamaños de pantalla. En este artículo se explica cómo crear y usar fragmentos para desarrollar aplicaciones de Xamarin.Android._

## <a name="overview"></a>Información general

En esta sección se explica cómo crear y usar fragmentos en una aplicación de Xamarin.Android. Esta aplicación mostrará los títulos de varias obras de William Shakespeare en una lista. Cuando el usuario pulsa el título de una obra, la aplicación mostrará una cita de la misma en una actividad independiente:

[![Aplicación que se ejecuta en un teléfono Android en modo vertical](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

Cuando el teléfono se gira al modo horizontal, la apariencia de la aplicación cambiará: tanto la lista de obras como las citas se mostrarán en la misma actividad. Cuando se selecciona una obra, la cita se mostrará en la misma actividad:

[![Aplicación que se ejecuta en un teléfono Android en modo horizontal](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

Por último, si la aplicación se ejecuta en una tableta:

[![Aplicación que se ejecuta en una tableta Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)

Esta aplicación de ejemplo se puede adaptar fácilmente a los distintos factores de forma y orientaciones con cambios mínimos en el código mediante el uso de fragmentos y [Diseños alternativos](../../../app-fundamentals/resources-in-android/alternate-resources.md).

Los datos de la aplicación se encontrarán en dos matrices de cadenas codificadas de forma rígida en la aplicación como matrices de cadenas de C#. Cada una de las matrices actuará como el origen de datos para un fragmento.  Una matriz contendrá el nombre de algunas obras de Shakespeare y la otra matriz contendrá una cita de esa obra. Cuando se inicia la aplicación, mostrará los nombres de las obras en `ListFragment`. Cuando el usuario hace clic en una obra en `ListFragment`, la aplicación iniciará otra actividad que mostrará la cita.

La interfaz de usuario de la aplicación constará de dos diseños, uno para el modo vertical y otro para el modo horizontal. En tiempo de ejecución, Android determinará qué diseño se va a cargar en función de la orientación del dispositivo y proporcionará ese diseño a la actividad que se va a representar. Toda la lógica para responder a los clics del usuario y mostrar los datos se incluirá en fragmentos. Las actividades de la aplicación solo existen como contenedores que hospedarán los fragmentos.

Este tutorial se dividirá en dos guías. La [primera parte](./walkthrough.md) se centrará en las partes centrales de la aplicación. Se creará un único conjunto de diseños (optimizado para el modo vertical), junto con dos fragmentos y dos actividades:

1. `MainActivity` &nbsp; Esta es la actividad de inicio de la aplicación.
1. `TitlesFragment` &nbsp; Este fragmento mostrará una lista de los títulos de las obras que ha escrito William Shakespeare. Se hospedará en `MainActivity`.
1. `PlayQuoteActivity` &nbsp; `TitlesFragment` iniciará `PlayQuoteActivity` como respuesta al usuario seleccionando una obra en `TitlesFragment`.
1. `PlayQuoteFragment` &nbsp; Este fragmento mostrará una cita de una obra de William Shakespeare. Se hospedará en `PlayQuoteActivity`.

En la [segunda parte de este tutorial](./walkthrough-landscape.md) se explicará cómo agregar un diseño alternativo (optimizado para el modo horizontal) que mostrará ambos fragmentos en la pantalla. Además, se realizarán algunos cambios menores en el código para que la aplicación adapte su comportamiento al número de fragmentos que se muestran simultáneamente en la pantalla.

## <a name="related-links"></a>Vínculos relacionados

- [FragmentsWalkthrough (ejemplo)](/samples/xamarin/monodroid-samples/fragmentswalkthrough)
- [Información general del diseñador](~/android/user-interface/android-designer/index.md)
- [Implementación de fragmentos](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Paquete de soporte técnico](https://developer.android.com/sdk/compatibility-library.html)