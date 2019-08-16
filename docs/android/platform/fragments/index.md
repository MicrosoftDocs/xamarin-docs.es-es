---
title: Fragments
description: Android 3,0 presentó fragmentos, en los que se muestra cómo admitir diseños más flexibles para los diferentes tamaños de pantalla que se encuentran en teléfonos y tabletas. En este artículo se explica cómo usar fragmentos para desarrollar aplicaciones de Xamarin. Android y cómo admitir fragmentos en dispositivos anteriores a Android 3,0 (nivel de API 11).
ms.prod: xamarin
ms.assetid: 1AFB4242-A337-F8E0-83D9-B8D850D7F384
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: b04ecf0685e78b73346ea5af815ed46f98b5da0f
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524357"
---
# <a name="fragments"></a>Fragments

_Android 3,0 presentó fragmentos, en los que se muestra cómo admitir diseños más flexibles para los diferentes tamaños de pantalla que se encuentran en teléfonos y tabletas. En este artículo se explica cómo usar fragmentos para desarrollar aplicaciones de Xamarin. Android y cómo admitir fragmentos en dispositivos anteriores a Android 3,0 (nivel de API 11)._

## <a name="fragments-overview"></a>Información general sobre fragmentos

Los tamaños de pantalla más grandes que se encuentran en la mayoría de las tabletas agregan una capa adicional de complejidad al desarrollo de Android: un diseño diseñado para la pantalla pequeña no funciona también en pantallas más grandes y viceversa. Para reducir el número de complicaciones que se introdujeron en, Android 3,0 agregó dos nuevas características, *fragmentos* y *paquetes de soporte técnico*.

Los fragmentos se pueden considerar como módulos de interfaz de usuario. Permiten al desarrollador dividir la interfaz de usuario en elementos aislados y reutilizables que se pueden ejecutar en actividades independientes. En tiempo de ejecución, las propias actividades decidirán qué fragmentos se van a utilizar.

Los paquetes de soporte técnico se denominaban originalmente *bibliotecas de compatibilidad* y fragmentos permitidos que se usarán en dispositivos que ejecutan versiones de Android anteriores a Android 3,0 (nivel de API 11).

Por ejemplo, en la imagen siguiente se muestra cómo una sola aplicación utiliza fragmentos en diversos factores de forma de dispositivo.

[![Diagrama de cómo se usan los fragmentos en tabletas y auriculares](images/00.png)](images/00.png#lightbox)

El *fragmento A* contiene una lista, mientras que el *fragmento B* contiene detalles de un elemento seleccionado en esa lista. Cuando la aplicación se ejecuta en una tableta, puede mostrar ambos fragmentos en la misma actividad. Cuando se ejecuta la misma aplicación en un auricular (con un tamaño de pantalla menor), los fragmentos se hospedan en dos actividades independientes. Los fragmentos A y B son los mismos en ambos factores de forma, pero las actividades que los hospedan son diferentes.

Para ayudar a que una actividad coordine y administre todos estos fragmentos, Android presentó una nueva clase denominada *FragmentManager*. Cada actividad tiene su propia instancia de `FragmentManager` para agregar, eliminar y buscar fragmentos hospedados. En el diagrama siguiente se ilustra la relación entre los fragmentos y las actividades:

[![Diagrama que ilustra las relaciones entre la actividad, el administrador de fragmentos y los fragmentos](images/01.png)](images/01.png#lightbox)

En algunos aspectos, los fragmentos se pueden considerar como controles compuestos o como actividades mínimas. Agrupan partes de la interfaz de usuario en módulos reutilizables que se pueden usar de forma independiente por parte de los desarrolladores en las actividades. Un fragmento tiene una jerarquía de vistas, al igual que una actividad, pero, a diferencia de una actividad, se puede compartir entre pantallas. Las vistas difieren de los fragmentos en que los fragmentos tienen su propio ciclo de vida; las vistas no lo hacen.

Aunque la actividad es un host a uno o varios fragmentos, no es directamente consciente de los propios fragmentos. Del mismo modo, los fragmentos no reconocen directamente otros fragmentos en la actividad de hospedaje. Sin embargo, los `FragmentManager` fragmentos y las actividades son conscientes del de su actividad. Mediante el uso `FragmentManager`de, es posible que una actividad o un fragmento obtengan una referencia a una instancia específica de un fragmento y, a continuación, llame a métodos en esa instancia. De esta manera, la actividad o los fragmentos pueden comunicarse e interactuar con otros fragmentos.

Esta guía contiene una cobertura completa sobre cómo usar fragmentos, entre los que se incluyen:

- **Crear fragmentos** : Cómo crear un fragmento básico y métodos clave que se deben implementar.
- **Administración de fragmentos y transacciones** : Cómo manipular fragmentos en tiempo de ejecución.
- **Paquete de soporte de Android** : Cómo usar las bibliotecas que permiten usar fragmentos en versiones anteriores de Android.


## <a name="requirements"></a>Requisitos

Los fragmentos están disponibles en la Android SDK a partir del nivel de API 11 (Android 3,0), tal como se muestra en la siguiente captura de pantalla:

[![Selección del nivel de API en el administrador de Android SDK](images/02.png)](images/02.png#lightbox)

Los fragmentos están disponibles en Xamarin. Android 4,0 y versiones posteriores. Una aplicación de Xamarin. Android debe tener como destino al menos el nivel de API 11 (Android 3,0) o superior para poder usar fragmentos. La plataforma de destino se puede establecer en las propiedades del proyecto, como se muestra a continuación:

[![Establecimiento del nivel de API de la plataforma de destino en las opciones del proyecto](images/03-sml.png)](images/03.png#lightbox)

Es posible usar fragmentos en versiones anteriores de Android mediante el paquete de soporte de Android y Xamarin. Android 4,2 o superior. La forma de hacerlo se describe con más detalle en los documentos de esta sección.


## <a name="related-links"></a>Vínculos relacionados

- [Galería de Honeycomb (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/honeycombgallery)
- [Fragmentos](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Paquete de soporte](https://developer.android.com/sdk/compatibility-library.html)
- [Seminario Web de MOTODEV: Presentación de fragmentos](http://motodev.adobeconnect.com/p9h1aqk3ttn/)
