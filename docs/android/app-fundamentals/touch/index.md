---
title: Toque y gestos en Xamarin. Android
description: 'Las pantallas táctiles en muchos de los dispositivos de hoy en día permiten a los usuarios interactuar de forma rápida y eficaz con los dispositivos de una manera natural e intuitiva. Esta interacción no se limita solo a la sencilla detección táctil: también es posible usar movimientos. Por ejemplo, el gesto de acercamiento a zoom es un ejemplo muy común de esto al reducir una parte de la pantalla con dos dedos que el usuario puede acercar o alejar. En esta guía se examinan los gestos táctiles y en Android.'
ms.prod: xamarin
ms.assetid: 61874769-978A-4562-9B2A-7FFD45F58B38
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: b4740b91b3d59a3c50696af06eec4ff82bbf9b1e
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455201"
---
# <a name="touch-and-gestures-in-xamarinandroid"></a>Toque y gestos en Xamarin. Android

_Las pantallas táctiles en muchos de los dispositivos de hoy en día permiten a los usuarios interactuar de forma rápida y eficaz con los dispositivos de una manera natural e intuitiva. Esta interacción no se limita solo a la sencilla detección táctil: también es posible usar movimientos. Por ejemplo, el gesto de acercamiento a zoom es un ejemplo muy común de esto al reducir una parte de la pantalla con dos dedos que el usuario puede acercar o alejar. En esta guía se examinan los gestos táctiles y en Android._

## <a name="touch-overview"></a>Información general táctil

iOS y Android son similares en las maneras en que controlan el toque. Ambos pueden admitir puntos de contacto con varios toques en los gestos complejos y de pantalla. En esta guía se presentan algunas de las similitudes en los conceptos, así como las peculiaridades de la implementación de funciones táctiles y gestos en ambas plataformas.

Android usa un `MotionEvent` objeto para encapsular los datos táctiles y métodos en el objeto de vista para escuchar los toques.

Además de capturar los datos táctiles, tanto iOS como Android proporcionan medios para interpretar los patrones de los toques en los gestos. Estos reconocedores de gestos se pueden usar a su vez para interpretar comandos específicos de la aplicación, como un giro de una imagen o un turno de una página. Android proporciona una serie de gestos admitidos, así como recursos para facilitar la adición de gestos personalizados complejos.

Tanto si trabaja en Android o iOS, la elección entre los reconocedores de gestos y los toques puede ser una confusión. En esta guía se recomienda que, en general, se proporcione la preferencia a los reconocedores de gestos. Los reconocedores de gestos se implementan como clases discretas, que proporcionan una mayor separación de preocupaciones y una mejor encapsulación. Esto facilita el uso compartido de la lógica entre diferentes vistas, lo que reduce la cantidad de código escrito.

En esta guía se sigue un formato similar para cada sistema operativo: en primer lugar, se introducen y explican las API táctiles de la plataforma, ya que son la base sobre la que se compilan las interacciones táctiles. A continuación, profundizamos en el mundo de los reconocedores de gestos: primero, explorando algunos gestos comunes y terminando con la creación de gestos personalizados para las aplicaciones. Por último, verá cómo realizar un seguimiento de los dedos individuales mediante el seguimiento táctil de bajo nivel para crear un programa de pintado con el dedo.

## <a name="sections"></a>Secciones

- [Entrada táctil de Android](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
- [Tutorial: uso de Touch en Android](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
- [Seguimiento multitáctil](touch-tracking.md)

## <a name="summary"></a>Resumen

En esta guía se ha examinado el toque en Android. En ambos sistemas operativos, hemos aprendido a habilitar Touch y a responder a los eventos Touch. A continuación, hemos aprendido sobre los gestos y algunos de los reconocedores de gestos que proporcionan Android e iOS para administrar algunos de los escenarios más comunes. Hemos examinado cómo crear gestos personalizados e implementarlos en aplicaciones. En un tutorial se han mostrado los conceptos y las API de cada sistema operativo en acción, y también se ha visto cómo realizar un seguimiento de los dedos individuales.

## <a name="related-links"></a>Vínculos relacionados

- [Inicio táctil de Android (ejemplo)](/samples/xamarin/monodroid-samples/applicationfundamentals-touch-start)
- [Android Touch final (ejemplo)](/samples/xamarin/monodroid-samples/applicationfundamentals-touch-final)
- [Pintura (ejemplo)](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)