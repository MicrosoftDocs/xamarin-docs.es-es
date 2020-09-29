---
title: Introducción a tvOS 12
description: En este documento se proporciona información general de alto nivel de las características nuevas y actualizadas de tvOS 12 para las que la versión preliminar de Xamarin proporciona actualmente enlaces de C#.
ms.prod: xamarin
ms.assetid: 037F7FFF-2155-4017-B99A-839CE7EC5C9C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 2ea9ddcf5f8a2412502c1133fdc9f87474bdd830
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434748"
---
# <a name="introduction-to-tvos-12"></a>Introducción a tvOS 12

En este documento se proporciona información general de alto nivel sobre tvOS 12 nuevos y actualizados.

Para empezar a compilar aplicaciones de tvOS 12 con Xamarin, eche un vistazo a la [Guía de introducción](~/ios/platform/introduction-to-ios12/get-started.md).

## <a name="tvuikit"></a>TVUIKit

tvOS 12 incluye TVUIKit, un conjunto de API que permiten a los desarrolladores de tvOS usar controles comunes de tvOS como vistas de póster, botones de título, vistas de tarjeta y vistas de monograma. tvOS 12 también incluye una propiedad que permite que las etiquetas desplazan el texto que es demasiado largo para que esté completamente visible.

## <a name="password-autofill"></a>Autorrellenar de contraseña

Con tvOS 12, los usuarios pueden usar sus dispositivos iOS para iniciar sesión en una aplicación de tvOS con una sola derivación. Esto se habilita a través de una combinación de `UITextContentType` uso para especificar los campos de nombre de usuario y contraseña, dominios asociados para establecer una relación entre una aplicación de iOS y una aplicación de tvOS, y los entornos de enfoque preferidos para seleccionar un elemento para recibir el foco después de que un usuario proporcione un nombre de usuario y una contraseña.

## <a name="focus-engine-enhancements"></a>Mejoras en el motor de enfoque

tvOS 12 permite todas las aplicaciones, independientemente de cómo se representen, para interactuar con el motor de foco. A través de las interacciones de un usuario con el Siri remoto, el motor de foco puede usarse con cualquier aplicación para seleccionar un elemento, la sugerencia en los cambios de foco posibles y la actualización natural del foco. Esto está habilitado en las aplicaciones personalizadas a través de `IUIFocusItemContainer` la interfaz de UIKit, la `UIFocusMovementHint` clase, la `IUIFocusItemScrollableContainer` interfaz y otras clases y métodos relacionados.

## <a name="vision-framework"></a>Vision Framework

Vision Framework incluye un detector de rostros mejorado que puede detectar rostros en diversas orientaciones. Además, ahora se pueden usar las revisiones de solicitud para seleccionar una revisión específica del algoritmo de Vision Framework.

## <a name="natural-language-framework"></a>Marco de lenguaje natural

El marco de lenguaje natural permite a las aplicaciones realizar varios tipos de análisis de lenguaje. Por ejemplo, se puede usar para identificar partes de la voz y determinar el idioma representado por un bloque de texto.

## <a name="deprecations"></a>Elementos obsoletos

Con tvOS 12, Apple ha dejado de usar OpenGL ES y [anima a los desarrolladores](https://developer.apple.com/tvos/whats-new/) a adoptar metal.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS: Apple Developer (Apple)](https://developer.apple.com/tvos/)
- [Novedades de tvOS 12 (Apple) (vídeo)](https://developer.apple.com/videos/play/wwdc2018/208/)
- [TV (Apple)](https://www.apple.com/tv/)