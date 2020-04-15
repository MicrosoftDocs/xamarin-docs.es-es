---
title: Resumen del capítulo 5. La cuestión de los tamaños
description: 'Creación de aplicaciones móviles con Xamarin.Forms: Resumen del capítulo 5. La cuestión de los tamaños'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 486800E9-C09F-4B95-9AC2-C0F8FE563BCF
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c082bdb10732e42b37511cf050e50f46990a5b5b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771141"
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>Resumen del capítulo 5. La cuestión de los tamaños

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)

> [!NOTE]
> En las notas de esta página se indican las áreas en las que Xamarin.Forms difiere del material presentado en el libro.

Hasta ahora hemos visto varios tamaños de Xamarin.Forms:

- El alto de la barra de estado de iOS es 20.
- `BoxView` tiene un ancho y un alto predeterminados de 40.
- `Padding` predeterminado en `Frame` es 20.
- `Spacing` predeterminado en `StackLayout` es 6.
- El método `Device.GetNamedSize` devuelve un tamaño de fuente numérico.

Estos tamaños no son píxeles, sino que son unidades independientes del dispositivo reconocidas de forma independiente por cada plataforma.

## <a name="pixels-points-dps-dips-and-dius"></a>Píxeles, puntos, DPS, DIP y DIU

Al principio de los tiempos de Apple Mac y Microsoft Windows, los programadores trabajaban en unidades de píxeles. Sin embargo, con la llegada de las pantallas de mayor resolución se hizo necesario un enfoque más virtualizado y abstracto a las coordenadas de pantalla. En el mundo de Mac, los programadores han trabajado en unidades de *puntos*, tradicionalmente 1/72 pulgadas, mientras que los desarrolladores de Windows usaban *unidades independientes del dispositivo* (DIU) basadas en 1/96 pulgadas.

Sin embargo, los dispositivos móviles se suelen mantener mucho más cerca de la superficie y tienen una resolución más alta que las pantallas de escritorio, lo que implica que se puede tolerar una densidad de píxeles mayor.

Los programadores que tienen como destino dispositivos iPhone y iPad de Apple continúan trabajando con unidades de *puntos*, pero hay 160 de estos puntos por pulgada. En función del dispositivo, puede haber 1, 2 o 3 píxeles por punto.

En Android es similar. Los programadores trabajan en unidades de *píxeles independientes de la densidad* (DPS), y la relación entre DPS y píxeles se basa en 160 DPS por pulgada.

Los teléfonos y dispositivos móviles de Windows también han establecido factores de escala que implican algo cercano a 160 unidades independientes del dispositivo por pulgada.

> [!NOTE]
> Xamarin.Forms ya no es compatible con ningún teléfono o dispositivo móvil basado en Windows.

En resumen, un programador de Xamarin.Forms que trabaje para teléfonos y tabletas puede asumir que todas las unidades de medida se basan en el siguiente criterio:

- 160 unidades por pulgada, equivalente a
- 64 unidades por centímetro

Las propiedades [`Width`](xref:Xamarin.Forms.VisualElement.Width) y [`Height`](xref:Xamarin.Forms.VisualElement.Height) definidas por `VisualElement` tienen los valores "ficticios" predeterminados de &ndash;1. Solo cuando se ha fijado el tamaño de un elemento y este se ha acomodado en el diseño, estas propiedades reflejan el tamaño real del elemento en unidades independientes del dispositivo. Este tamaño incluye cualquier `Padding` establecido en el elemento, pero no `Margin`.

Un elemento visual activa el evento [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged) cuando su `Width` o `Height` ha cambiado. En el ejemplo [**WhatSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize) se usa este evento para mostrar el tamaño de la pantalla del programa.

## <a name="metrical-sizes"></a>Tamaños de métrica

En [**MetricalBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView) se usa [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) y [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) para mostrar un elemento `BoxView` de una pulgada de alto y un centímetro de ancho.

## <a name="estimated-font-sizes"></a>Tamaños de fuente estimados

En el ejemplo [**FontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes) se muestra cómo usar la regla de 160 unidades por pulgada para especificar tamaños de fuente en unidades de puntos. La coherencia visual entre las plataformas que usan esta técnica es mejor que `Device.GetNamedSize`.

## <a name="fitting-text-to-available-size"></a>Ajuste del texto al tamaño disponible

Es posible ajustar un bloque de texto dentro de un rectángulo determinado calculando un `FontSize` de `Label` mediante los siguientes criterios:

- El interlineado es el 120 % del tamaño de fuente (130 % en las plataformas de Windows).
- El ancho medio de caracteres es el 50 % del tamaño de fuente.

En el ejemplo [**EstimatedFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize) se muestra esta técnica. Este programa se escribió antes de que la propiedad [`Margin`](xref:Xamarin.Forms.View.Margin) estuviera disponible, por lo que utiliza un [`ContentView`](xref:Xamarin.Forms.ContentView) con un valor [`Padding`](xref:Xamarin.Forms.Layout.Padding) para simular un margen.

[![Captura de pantalla triple del tamaño de fuente estimado](images/ch05fg07-small.png "Ajuste de texto al tamaño disponible")](images/ch05fg07-large.png#lightbox "Ajuste de texto al tamaño disponible")

## <a name="a-fit-to-size-clock"></a>Reloj ajustado al tamaño

En el ejemplo [**FitToSizeClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock) se muestra cómo usar [`Device.StartTimer`](xref:Xamarin.Forms.Device.StartTimer(System.TimeSpan,System.Func{System.Boolean})) para iniciar un temporizador que notifica periódicamente a la aplicación cuándo es el momento de actualizar el reloj. El tamaño de fuente se establece en un sexto del ancho de página para que la pantalla sea lo más grande posible.

## <a name="accessibility-issues"></a>Problemas de accesibilidad

Los programas **EstimatedFontSize** y **FitToSizeClock** contienen un error sutil: Si el usuario cambia la configuración de accesibilidad del teléfono en Android o Windows 10 Mobile, el programa ya no podrá calcular el tamaño del texto en función del tamaño de fuente. En el ejemplo [**AccessibilityTest**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest) se muestra este problema.

## <a name="empirically-fitting-text"></a>Texto de ajuste empírico

Otra forma de ajustar el texto a un rectángulo es calcular empíricamente el tamaño del texto representado y ajustarlo hacia arriba o hacia abajo. El programa del libro llama a [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double)) en un elemento visual para obtener el tamaño deseado del elemento. Ese método está en desuso y, en su lugar, los programas deben llamar a [`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)).

En el caso de `Label`, el primer argumento debe ser el ancho del contenedor (para permitir el ajuste), mientras que el segundo argumento se debe establecer en `Double.PositiveInfinity` para que el alto no esté restringido. En el ejemplo [**EmpiricalFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize) se muestra esta técnica.

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 5 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [Ejemplos del capítulo 5](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [Ejemplos de F# del capítulo 5](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)
