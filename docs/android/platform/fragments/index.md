---
title: Fragments
description: Android 3.0 presentó los fragmentos, en los que se muestra cómo admitir diseños más flexibles para los diferentes tamaños de pantalla de los teléfonos y las tabletas. En este artículo se explica cómo usar los fragmentos para desarrollar aplicaciones de Xamarin.Android y cómo admitir fragmentos en dispositivos anteriores a Android 3.0 (nivel de API 11).
ms.prod: xamarin
ms.assetid: 1AFB4242-A337-F8E0-83D9-B8D850D7F384
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/15/2018
ms.openlocfilehash: 960f64c05bcc474a7e197c06990840d5e9b88caf
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453849"
---
# <a name="fragments"></a>Fragments

_Android 3.0 presentó los fragmentos, en los que se muestra cómo admitir diseños más flexibles para los diferentes tamaños de pantalla de los teléfonos y las tabletas. En este artículo se explica cómo usar los fragmentos para desarrollar aplicaciones de Xamarin.Android y cómo admitir fragmentos en dispositivos anteriores a Android 3.0 (nivel de API 11)._

## <a name="fragments-overview"></a>Información general sobre los fragmentos

Los tamaños de pantalla más grandes de la mayoría de las tabletas agregan una capa adicional de complejidad al desarrollo de Android: un diseño dirigido a las pantallas pequeñas no tiene porqué funcionar también en pantallas más grandes y viceversa. Para reducir el número de complicaciones, Android 3.0 agregó dos nuevas características, *Fragmentos* y *Support Packages* (Paquetes de soporte técnico).

Los fragmentos se pueden considerar como módulos de interfaz de usuario. Permiten al desarrollador dividir la interfaz de usuario en partes aisladas y reutilizables que se pueden ejecutar en actividades independientes. En tiempo de ejecución, las propias actividades decidirán qué Fragmentos se van a utilizar.

Los paquetes de soporte técnico se llamaron originalmente *Bibliotecas de compatibilidad* y permitían usar fragmentos en dispositivos que ejecutaban versiones de Android anteriores a Android 3.0 (nivel de API 11).

Por ejemplo, en la imagen siguiente se ilustra cómo una sola aplicación utiliza fragmentos en diversos factores de forma de dispositivos.

[![Diagrama de cómo se usan fragmentos en tabletas y móviles](images/00.png)](images/00.png#lightbox)

El *fragmento A* contiene una lista, mientras que el *fragmento B* contiene detalles de un elemento seleccionado en la lista. Cuando la aplicación se ejecuta en una tableta, puede mostrar ambos fragmentos en la misma actividad. Cuando se ejecuta la misma aplicación en un móvil (con un tamaño de pantalla menor), los fragmentos se hospedan en dos actividades independientes. Los fragmentos A y B son los mismos en ambos factores de forma, pero las actividades que los hospedan son diferentes.

Para ayudar a que una actividad coordine y administre todos estos fragmentos, Android presentó una nueva clase denominada *FragmentManager*. Cada actividad tiene su propia instancia de `FragmentManager` para agregar, eliminar y buscar fragmentos hospedados. En el diagrama siguiente se ilustra la relación entre los fragmentos y las actividades:

[![Diagrama que ilustra las relaciones entre la actividad, el administrador de fragmentos y los fragmentos](images/01.png)](images/01.png#lightbox)

En algunos aspectos, los fragmentos se pueden considerar como controles compuestos o como mini actividades. Agrupan partes de la interfaz de usuario en módulos reutilizables que los desarrolladores pueden usar de forma independiente en las actividades. Un fragmento tiene una jerarquía de vistas, igual que una actividad, pero, a diferencia de una actividad, se puede compartir entre pantallas. Las vistas difieren de los fragmentos en que los fragmentos tienen su propio ciclo de vida; las vistas no.

Aunque la actividad es un host para uno o varios fragmentos, no reconoce directamente los fragmentos en sí. Del mismo modo, los fragmentos no reconocen directamente otros fragmentos de la actividad de hospedaje. Sin embargo, los fragmentos y las actividades reconocen `FragmentManager` en su actividad. Mediante el uso de `FragmentManager`, es posible que una actividad o un fragmento obtenga una referencia a una instancia específica de un fragmento y, a continuación, llame a métodos de esa instancia. De esta manera, la actividad o los fragmentos pueden comunicarse e interactuar con otros fragmentos.

Esta guía contiene una cobertura completa de cómo usar fragmentos, que incluye:

- **Creación de fragmentos**: cómo crear un fragmento básico y los métodos clave que se deben implementar.
- **Administración de fragmentos y transacciones**: cómo manipular fragmentos en tiempo de ejecución.
- **Paquete de compatibilidad de Android**: cómo usar las bibliotecas que permiten usar fragmentos en versiones anteriores de Android.

## <a name="requirements"></a>Requisitos

Los fragmentos están disponibles en Android SDK a partir del nivel de API 11 (Android 3.0), tal como se muestra en la siguiente captura de pantalla:

[![Selección del nivel de API en el Administrador de Android SDK](images/02.png)](images/02.png#lightbox)

Los fragmentos están disponibles en Xamarin.Android 4.0 y versiones posteriores. Una aplicación de Xamarin.Android debe tener como destino al menos el nivel de API 11 (Android 3.0) o superior para poder usar fragmentos. La plataforma de destino se puede establecer en las propiedades del proyecto, como se muestra a continuación:

[![Establecimiento del nivel de API de la plataforma de destino en las opciones del proyecto](images/03-sml.png)](images/03.png#lightbox)

Es posible usar fragmentos en versiones anteriores de Android mediante el paquete de soporte técnico de Android y Xamarin.Android 4.2 o superior. La forma de hacerlo se describe con más detalle en los documentos de esta sección.

## <a name="related-links"></a>Vínculos relacionados

- [Galería de Honeycomb (ejemplo)](/samples/xamarin/monodroid-samples/honeycombgallery)
- [Fragmentos](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Paquete de soporte técnico](https://developer.android.com/sdk/compatibility-library.html)