---
title: Resumen del capítulo 5. Tratar con tamaños
description: 'Crear Mobile Apps con Xamarin. Forms: Resumen del capítulo 5. Tratar con tamaños'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 486800E9-C09F-4B95-9AC2-C0F8FE563BCF
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c082bdb10732e42b37511cf050e50f46990a5b5b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771141"
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>Resumen del capítulo 5. Tratar con tamaños

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)

> [!NOTE]
> Notas de esta página indican áreas donde se ha dividido Xamarin.Forms desde el material presentado en el libro.

Hasta ahora se han detectado varios tamaños de Xamarin.Forms:

- El alto de la barra de estado iOS es 20
- El `BoxView` tiene un alto de 40 y el ancho predeterminado
- El valor predeterminado `Padding` en un `Frame` es 20
- El valor predeterminado `Spacing` en el `StackLayout` es 6
- El `Device.GetNamedSize` método devuelve un tamaño de fuente numérico

Estos tamaños no son píxeles. En su lugar, son unidades independientes del dispositivo reconocidas por separado por cada plataforma.

## <a name="pixels-points-dps-dips-and-dius"></a>Píxeles, puntos, dps, DIP y DIUs

Al principio de los historiales de Mac de Apple y Microsoft Windows, los programadores trabajaban en unidades de píxeles. Sin embargo, la llegada de mayor resolución muestra requiere un enfoque más virtualizado y abstracto a coordenadas de pantalla. En el mundo de Mac, los programadores trabajaban en unidades de *puntos*, tradicionalmente 1/72 de pulgada, mientras que los desarrolladores de Windows usa *unidades independientes del dispositivo* (DIUs) basado en 1/96 de pulgada.

Dispositivos móviles, sin embargo, por lo general se mantienen mucho más cerca de la cara y tienen una resolución mayor que el escritorio las pantallas, lo que implica que se puede tolerar una mayor densidad de píxeles.

Los programadores usar como destino dispositivos iPhone y iPad de Apple seguirán trabajando en unidades de *puntos*, pero hay 160 de estos puntos por pulgada. En función del dispositivo, puede ser 1, 2 o 3 píxeles para el punto.

Android es similar. Los programadores que trabajan en unidades de *píxeles independientes de densidad* (dps), y la relación entre el dps y píxeles se basa en 160 DP pulgada.

Teléfonos de Windows y dispositivos móviles también se han establecido los factores de escala que implican algo similar a 160 unidades independientes del dispositivo por pulgada.

> [!NOTE]
> Xamarin.Forms ya no es compatible con cualquier basado en Windows phone o dispositivo móvil.

En resumen, un programador de Xamarin.Forms tienen como destino teléfonos y tabletas puede suponer que todas las unidades de medida se basan en el siguiente criterio:

- 160 unidades por pulgada, equivalente a
- 64 unidades para el centímetro

Solo lectura [ `Width` ](xref:Xamarin.Forms.VisualElement.Width) y [ `Height` ](xref:Xamarin.Forms.VisualElement.Height) propiedades definidas por `VisualElement` tenga el valor predeterminado "ficticios" valores de &ndash;1. Solo cuando un elemento ha tamaño y se implementan en el diseño de estas propiedades refleja el tamaño real del elemento en unidades independientes del dispositivo. Este tamaño incluye cualquier `Padding` establecido en el elemento, pero no el `Margin`.

Un elemento visual se activa el [ `SizeChanged` ](xref:Xamarin.Forms.VisualElement.SizeChanged) eventos cuando su `Width` o `Height` ha cambiado. El [ **WhatSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize) ejemplo utiliza este evento para mostrar el tamaño de la pantalla del programa.

## <a name="metrical-sizes"></a>Tamaños de métrica:

El [ **MetricalBoxView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView) usa [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) y [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) para mostrar una `BoxView` una pulgada de alto y uno centímetro amplia.

## <a name="estimated-font-sizes"></a>Tamaños de fuente estimado

El [ **FontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes) ejemplo muestra cómo usar la regla de 160 unidades-a-the-pulgadas para especificar los tamaños de fuente en unidades de puntos. La coherencia visual entre las plataformas que usan esta técnica es mejor que `Device.GetNamedSize`.

## <a name="fitting-text-to-available-size"></a>Ajustar el texto al tamaño disponible

Es posible ajustar un bloque de texto dentro de un rectángulo determinado calculando una `FontSize` de la `Label` con los siguientes criterios:

- El interlineado es 120% del tamaño de fuente (130% en las plataformas de Windows).
- Promedio de carácter ancho es un 50% del tamaño de fuente.

El [ **EstimatedFontSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize) muestra esta técnica. Este programa se escribió antes el [ `Margin` ](xref:Xamarin.Forms.View.Margin) propiedad estaba disponible, por lo que usa un [ `ContentView` ](xref:Xamarin.Forms.ContentView) con un [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) configuración para simular un margen.

[![Captura de pantalla de triple del tamaño de fuente estimado](images/ch05fg07-small.png "texto se ajusta al tamaño disponible")](images/ch05fg07-large.png#lightbox "texto se ajusta al tamaño disponible")

## <a name="a-fit-to-size-clock"></a>Un reloj de ajustar al tamaño

El [ **FitToSizeClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock) ejemplo muestra cómo utilizar [ `Device.StartTimer` ](xref:Xamarin.Forms.Device.StartTimer(System.TimeSpan,System.Func{System.Boolean})) para iniciar un temporizador que notifica periódicamente a la aplicación cuando llega el momento de actualizar el reloj. El tamaño de fuente se establece en un sexto del ancho de página para realizar la presentación de mayor tamaño posible.

## <a name="accessibility-issues"></a>Problemas de accesibilidad

El programa **EstimatedFontSize** y el programa **FitToSizeClock** contienen un error sutil: Si el usuario cambia la configuración de accesibilidad del teléfono en Android o Windows 10 Mobile, el programa ya no podrá calcular el tamaño del texto en función del tamaño de fuente. El [ **AccessibilityTest** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest) muestra este problema.

## <a name="empirically-fitting-text"></a>Empíricamente ajustar texto

Otra forma de ajustar el texto a un rectángulo es empíricamente calcular el tamaño del texto representado y ajustarla hacia arriba o hacia abajo. El programa en las llamadas de libro [ `GetSizeRequest` ](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double)) en un elemento visual para obtener el tamaño del elemento deseado. Que método está desusado y programas en su lugar, deben llamar a [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)).

Para un `Label`, el primer argumento debe ser el ancho del contenedor (para permitir el ajuste), mientras que el segundo argumento debe establecerse en `Double.PositiveInfinity` para hacer que el alto sin restricciones. El [ **EmpiricalFontSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize) muestra esta técnica.

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 5 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [Capítulo 5 ejemplos](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [Capítulo 5 F# ejemplos](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)
