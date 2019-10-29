---
title: Control de la entrada táctil en aplicaciones de Xamarin. iOS
description: Este documento contiene vínculos a guías que describen cómo trabajar con funciones táctiles, multitáctiles, gestos y toque 3D en una aplicación de Xamarin. iOS.
ms.prod: xamarin
ms.assetid: E3904713-6018-4755-A315-EB045DFB3500
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/23/2017
ms.openlocfilehash: 7aac9a3e2a86f5cdcfa2d6ab27961dd83998bb0f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73009361"
---
# <a name="handling-touch-in-xamarinios-apps"></a>Control de la entrada táctil en aplicaciones de Xamarin. iOS

Al igual que otras plataformas móviles, iOS tiene varias formas de controlar la interacción táctil. Puede admitir multitáctiles (muchos puntos de contacto en la pantalla) y gestos complejos. En esta guía se presentan algunos de los conceptos, así como las peculiaridades de la implementación de funciones táctiles y gestos en iOS.

iOS encapsula los datos táctiles en la clase `UITouch`, que se pone a disposición de las aplicaciones a través de una serie de métodos de `UIResponder`. Las aplicaciones pueden invalidar estos métodos en las subclases de `UIView` y `UIViewController`, que heredan de `UIResponder`.

Además de capturar los datos táctiles, iOS proporciona medios para interpretar los patrones de los toques en los movimientos. Estos reconocedores de gestos se pueden usar a su vez para interpretar comandos específicos de la aplicación, como un giro de una imagen o un turno de una página. iOS proporciona una colección enriquecida de clases para controlar gestos comunes con el código agregado mínimo.

La elección entre los tocadores y los reconocedores de gestos puede ser una confusión. En esta guía se recomienda que, en general, se proporcione la preferencia a los reconocedores de gestos. Los reconocedores de gestos se implementan como clases discretas, que proporcionan una mayor separación de preocupaciones y una mejor encapsulación. Esto hace que resulte sencillo compartir la lógica entre diferentes vistas, lo que reduce la cantidad de código escrito.

Sin embargo, hay ocasiones en las que es necesario usar el procesamiento táctil de bajo nivel e incluso realizar un seguimiento de varios dedos, por ejemplo, para crear un programa de pintado con el dedo.

## <a name="sections"></a>Secciones

- [Entrada táctil en iOS](touch-in-ios.md)
- [Tutorial: uso de la entrada táctil en iOS](ios-touch-walkthrough.md)
- [Seguimiento multitáctil](touch-tracking.md)

Esta guía sirve como introducción a la entrada táctil en iOS. Para obtener más información sobre el uso de comentarios táctiles 3D e hápticos en iOS, que se introdujeron en iOS 9 y 10, consulte las guías específicas siguientes:

- [Entrada táctil 3D](~/ios/platform/3d-touch.md)
- [Provisión de comentarios hápticos](~/ios/user-interface/ios-ui/haptic-feedback.md)

## <a name="related-links"></a>Vínculos relacionados

- [iOS Touch Start (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-start)
- [iOS Touch final (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-final)
- [Pintura (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint)
