---
title: Resumen del capítulo 14. Diseño absoluto
description: 'Creación de aplicaciones móviles con Xamarin.Forms: Resumen del capítulo 14. Diseño absoluto'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 88882A48-3226-42D1-96ED-241250B64A84
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c489bf244396cf180ed8e1272308048a14b67300
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "70771131"
---
# <a name="summary-of-chapter-14-absolute-layout"></a>Resumen del capítulo 14. Diseño absoluto

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)

Como `StackLayout`, [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) se deriva de `Layout<View>` y hereda una propiedad `Children`. `AbsoluteLayout` implementa un sistema de diseño que requiere que el programador especifique las posiciones de sus elementos secundarios y, opcionalmente, su tamaño. La posición se especifica en la esquina superior izquierda del elemento secundario en relación con la esquina superior izquierda de `AbsoluteLayout` en unidades independientes del dispositivo. `AbsoluteLayout` también implementa una característica de posicionamiento y ajuste de tamaño proporcional.

`AbsoluteLayout` debe considerarse como un sistema de diseño de propósito especial que se utilizará solo cuando el programador pueda imponer un tamaño en los elementos secundarios (por ejemplo, elementos `BoxView`) o cuando el tamaño del elemento no afecte a la posición de otros elementos secundarios. Las propiedades `HorizontalOptions` y `VerticalOptions` no tienen ningún efecto en los elementos secundarios de un `AbsoluteLayout`.

En este capítulo también se presenta la característica importante de *propiedades enlazables asociadas* que permiten que las propiedades definidas en una clase (en este caso `AbsoluteLayout`) se adjunten a otra clase (un elemento secundario del `AbsoluteLayout`).

## <a name="absolutelayout-in-code"></a>AbsoluteLayout en el código

Puede agregar un elemento secundario a la colección de `Children` de `AbsoluteLayout` con el método [`Add`](xref:System.Collections.Generic.ICollection`1.Add*) estándar, pero `AbsoluteLayout` también proporciona un método [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) que le permite especificar [`Rectangle`](xref:Xamarin.Forms.Rectangle). Otro método [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) solo requiere [`Point`](xref:Xamarin.Forms.Point), en cuyo caso el elemento secundario no tiene restricciones y establece su propio tamaño.

Puede crear un valor de `Rectangle` con un [constructor](xref:Xamarin.Forms.Rectangle.%23ctor(System.Double,System.Double,System.Double,System.Double)) que requiera cuatro valores &mdash; los dos primeros que indican la posición de la esquina superior izquierda del elemento secundario en relación con su elemento primario y los dos segundos que indican el tamaño del elemento secundario. O bien, puede usar un [constructor](xref:Xamarin.Forms.Rectangle.%23ctor(Xamarin.Forms.Point,Xamarin.Forms.Size)) que requiera `Point` y un valor [`Size`](xref:Xamarin.Forms.Size).

Estos métodos de `Add` se muestran en [**AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo), que coloca elementos `BoxView` con valores `Rectangle` y un elemento `Label` usando solo un valor `Point`.

El ejemplo [**ChessboardFixed**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed) usa 32 elementos `BoxView` para crear el patrón del tablero de ajedrez. El programa proporciona a los elementos `BoxView` un tamaño codificado de forma rígida de 35 unidades cuadradas. `AbsoluteLayout` tiene sus `HorizontalOptions` y `VerticalOptions` establecidos en `LayoutOptions.Center`, lo que hace que `AbsoluteLayout` tenga un tamaño total de 280 unidades cuadradas.

## <a name="attached-bindable-properties"></a>Propiedades enlazables asociadas

También es posible establecer la posición y, opcionalmente, el tamaño de un elemento secundario de `AbsoluteLayout` una vez que se ha agregado a la colección de `Children` mediante el método estático [`AbsoluteLayout.SetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds(Xamarin.Forms.BindableObject,Xamarin.Forms.Rectangle)). El primer argumento es el elemento secundario; el segundo es un objeto de `Rectangle`. Puede especificar que los tamaños secundarios se ajusten horizontal o verticalmente a los valores de ancho y alto en la constante [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize).

El ejemplo [**ChessboardDynamic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic) coloca `AbsoluteLayout` en `ContentView` con un controlador `SizeChanged` para llamar a `AbsoluteLayout.SetLayoutBounds` en todos los elementos secundarios para que sean lo más grandes posible.  

La propiedad enlazable asociada que define `AbsoluteLayout` es el campo estático de solo lectura de tipo `BindableProperty` denominado [`AbsoluteLayout.LayoutBoundsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty). El método estático de `AbsoluteLayout.SetLayoutBounds` se implementa llamando a `SetValue` en el elemento secundario con `AbsoluteLayout.LayoutBoundsProperty`. El elemento secundario contiene un diccionario en el que se almacenan la propiedad enlazable asociada y su valor. Durante el diseño, `AbsoluteLayout` puede obtener ese valor llamando a [`AbsoluteLayout.GetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutBounds(Xamarin.Forms.BindableObject)), que se implementa con una llamada `GetValue`.

## <a name="proportional-sizing-and-positioning"></a>Tamaño y posicionamiento proporcionales

`AbsoluteLayout` implementa una característica de ajuste de tamaño y posición proporcional. La clase define una segunda propiedad enlazable asociada, [`LayoutFlagsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty), con los métodos estáticos relacionados [`AbsoluteLayout.SetLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutFlags(Xamarin.Forms.BindableObject,Xamarin.Forms.AbsoluteLayoutFlags)) y [`AbsoluteLayout.GetLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutFlags(Xamarin.Forms.BindableObject)).

El argumento para `AbsoluteLayout.SetLayoutFlags` y el valor devuelto de `AbsoluteLayout.GetLayoutFlags` es un valor de tipo [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags), una enumeración con los siguientes miembros:

- [`None`](xref:Xamarin.Forms.AbsoluteLayoutFlags.None) (igual a 0)
- [`XProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.XProportional) (1)
- [`YProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.YProportional) (2)
- [`PositionProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional) (3)
- [`WidthProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional) (4)
- [`HeightProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional) (8)
- [`SizeProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional) (12)
- [`All`](xref:Xamarin.Forms.AbsoluteLayoutFlags.All) (\xFFFFFFFF)

Puede combinarlos con el operador OR bit a bit de C#.

Con estas marcas establecidas, ciertas propiedades de la estructura de límites de diseño `Rectangle` utilizadas para colocar y ajustar el tamaño del elemento secundario se interpretan proporcionalmente.

Cuando se establece la marca de `WidthProportional`, un valor `Width` de 1 significa que el elemento secundario tiene el mismo ancho que `AbsoluteLayout`. Se usa un enfoque similar para el alto.

El posicionamiento proporcional tiene en cuenta el tamaño. Cuando se establece la marca `XProportional`, la propiedad `X` de los límites del diseño de `Rectangle` es proporcional. Un valor de 0 significa que el borde izquierdo del elemento secundario se coloca en el borde izquierdo de `AbsoluteLayout`, pero una posición de 1 significa que el borde derecho del elemento secundario se coloca en el borde derecho de `AbsoluteLayout`, no más allá del borde derecho de `AbsoluteLayout` como cabría esperar. Una propiedad `X` de 0,5 centra el elemento secundario horizontalmente en `AbsoluteLayout`.

En el ejemplo [**ChessboardProportional**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional) se muestra el uso del tamaño y el posicionamiento proporcionales.

## <a name="working-with-proportional-coordinates"></a>Trabajo con coordenadas proporcionales

A veces, es más fácil pensar en una posición proporcional de forma diferente de cómo se implementa en `AbsoluteLayout`. Podría preferir trabajar con coordenadas proporcionales donde una propiedad `X` de 1 coloca el borde izquierdo del elemento secundario (en lugar del borde derecho) en el borde derecho de `AbsoluteLayout`.

Este esquema de posicionamiento alternativo se puede denominar "coordenadas secundarias fraccionarias". Puede convertir a partir de coordenadas secundarias fraccionarias a los límites de diseño necesarios para `AbsoluteLayout` mediante las siguientes fórmulas:

layoutBounds.X = (fractionalChildCoordinate.X / (1 - layoutBounds.Width))

layoutBounds.Y = (fractionalChildCoordinate.Y / (1 - layoutBounds.Height))

En el ejemplo [**ProportionalCoordinateCalc**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc) se muestra esto.

## <a name="absolutelayout-and-xaml"></a>AbsoluteLayout y XAML

Puede usar `AbsoluteLayout` en XAML y establecer las propiedades enlazables asociadas en los elementos secundarios de `AbsoluteLayout` utilizando los valores de atributo de `AbsoluteLayout.LayoutBounds` y `AbsoluteLayout.LayoutFlags`. Esto se muestra en los ejemplos [**AbsoluteXamlDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo) y [**ChessboardXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml). El último programa contiene 32 elementos `BoxView`, pero usa un `Style` implícito que incluye la propiedad `AbsoluteLayout.LayoutFlags` para mantener el marcado en un nivel mínimo.

Un atributo de XAML que consta de un nombre de clase, un punto y un nombre de propiedad es *siempre* una propiedad enlazable asociada.

## <a name="overlays"></a>Superposiciones

Puede usar `AbsoluteLayout` para construir una *superposición*, que abarca la página con otros controles, quizás para impedir que el usuario interactúe con los controles normales de la página.

En el ejemplo [**SimpleOverlay**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay) se muestra esta técnica y también se muestra [`ProgressBar`](xref:Xamarin.Forms.ProgressBar), que revela la medida en que un programa ha completado una tarea.

## <a name="some-fun"></a>Algo divertido

En el ejemplo [**DotMatrixClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock) se muestra la hora actual con una pantalla de matriz de puntos de 5x7 simulada. Cada punto es `BoxView` (hay 228) y su tamaño se coloca en `AbsoluteLayout`.

[![Captura de pantalla triple del reloj de la matriz de puntos](images/ch14fg08-small.png "Reloj de la matriz de puntos")](images/ch14fg08-large.png#lightbox "Reloj de la matriz de puntos")

El programa [**BouncingText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText) anima dos objetos `Label` para rebotar horizontal y verticalmente a lo largo de la pantalla.

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 14 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [Ejemplos del capítulo 14](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)
- [Propiedades asociadas](~/xamarin-forms/xaml/attached-properties.md)
