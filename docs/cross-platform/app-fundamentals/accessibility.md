---
title: Accesibilidad en aplicaciones de Xamarin
description: En este documento se proporcionan varias sugerencias para la creación de aplicaciones accesibles. Por ejemplo, incluye recomendaciones sobre fuentes grandes, contraste alto, interfaces autodescriptivas, etc.
ms.prod: xamarin
ms.assetid: E587F0CF-7C1D-41F8-B5A8-DA3E738EDA81
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 23ef499005994c33586e1918ac3b2a9d28640d04
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030277"
---
# <a name="accessibility-in-xamarin-apps"></a>Accesibilidad en aplicaciones de Xamarin

_Asegúrese de que las aplicaciones puedan ser utilizables por la audiencia más amplia posible_

La accesibilidad hace referencia al concepto de diseñar interfaces de usuario de la aplicación que funcionan bien con las características de visualización del sistema operativo y de la asistencia de entrada, como tipo grande, contraste alto, zoom, lectura de pantalla (texto a voz), indicaciones de comentarios visuales o hápticos, y métodos de entrada alternativos.

Las plataformas de escritorio y móviles como iOS, Android y Windows proporcionan API integradas que ayudan a los desarrolladores a crear aplicaciones accesibles, como [Google talkback](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) y [Apple VoiceOver](https://www.apple.com/accessibility/ios/voiceover/).

## <a name="platform-specific-apis"></a>API específicas de la plataforma

Para implementar las instrucciones de este documento, use las API proporcionadas por cada plataforma:

- [**Accesibilidad de Android**](~/android/app-fundamentals/accessibility.md)
- [**Accesibilidad de iOS**](~/ios/app-fundamentals/accessibility.md)
- [**Accesibilidad de OS X**](~/mac/app-fundamentals/accessibility.md)
- [**Xamarin. Forms**](~/xamarin-forms/app-fundamentals/accessibility/index.md)

<a name="checklist" />

## <a name="accessibility-checklist"></a>Lista de comprobación de accesibilidad

Siga estas sugerencias para asegurarse de que las aplicaciones sean accesibles para el público más amplio posible. Consulte la lista de comprobación de la [prueba de accesibilidad de Android](https://developer.android.com/training/accessibility/testing.html) y la página de accesibilidad de [Apple](https://www.apple.com/accessibility/) para obtener información adicional.

### <a name="support-large-fonts-and-high-contrast"></a>Admitir fuentes grandes y contraste alto

Evite las dimensiones de control de codificar y, en su lugar, prefiera diseños que puedan cambiar de tamaño para dar cabida a tamaños de fuente mayores.
Pruebe las combinaciones de colores en el modo de alto contraste para asegurarse de que sean legibles.

### <a name="make-the-user-interface-self-describing"></a>Autodescribir la interfaz de usuario

Etiquete todos los elementos de la interfaz de usuario con texto descriptivo y sugerencias que sean compatibles con las API de lectura de pantalla en cada plataforma.

### <a name="ensure-that-images-and-icons-have-an-alternate-text-description"></a>Asegurarse de que las imágenes e iconos tienen una descripción de texto alternativa

Las imágenes e iconos que forman parte de la interfaz de usuario de la aplicación (como botones o indicadores de estado, por ejemplo) se deben etiquetar con una descripción accesible.

### <a name="design-the-visual-tree-with-accessible-navigation-in-mind"></a>Diseñar el árbol visual con la navegación accesible en mente

Use los controles de diseño o las API adecuados para que la navegación entre los controles que usan métodos de entrada alternativos siga el mismo flujo lógico que el uso de la pantalla táctil.

Excluya los elementos innecesarios de los lectores de pantalla (imágenes decorativas o etiquetas para los campos a los que ya se puede tener acceso, por ejemplo).

### <a name="dont-rely-on-audio-or-color-cues-alone"></a>No confíe solo en las señales de audio o de color

Evite situaciones en las que la única indicación del progreso, la finalización o algún otro Estado es un cambio de color o de sonido. Diseñe la interfaz de usuario para que incluya indicaciones visuales claras (con sonido y color solo para el refuerzo) o agregue indicadores de accesibilidad específicos.

Al elegir los colores, intente evitar una paleta que sea difícil de distinguir para los usuarios con ceguera de color.

### <a name="captioning-for-video-text-for-audio"></a>Subtítulos para vídeo, texto para audio

Proporcione leyendas para el contenido de vídeo y un script legible para el contenido de audio. También resulta útil proporcionar controles que ajusten la velocidad del contenido de audio o vídeo, y asegurarse de que los botones de volumen y de reproducción/pausa son fáciles de encontrar y usar.

### <a name="localize"></a>Localize

Las descripciones de accesibilidad se pueden localizar (y deben) en el caso de que la aplicación admita varios idiomas.

## <a name="related-links"></a>Vínculos relacionados

- [Accesibilidad de Android](~/android/app-fundamentals/accessibility.md)
- [Accesibilidad de iOS](~/ios/app-fundamentals/accessibility.md)
- [Accesibilidad de OS X](~/mac/app-fundamentals/accessibility.md)
- [Accesibilidad de Xamarin. Forms](~/xamarin-forms/app-fundamentals/accessibility/index.md)
