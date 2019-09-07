---
title: Resumen del capítulo 14. Diseño absoluto
description: 'Crear Mobile Apps con Xamarin. Forms: Resumen del capítulo 14. Diseño absoluto'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 88882A48-3226-42D1-96ED-241250B64A84
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c489bf244396cf180ed8e1272308048a14b67300
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771131"
---
# <a name="summary-of-chapter-14-absolute-layout"></a>Resumen del capítulo 14. Diseño absoluto

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)

Al igual que `StackLayout`, [ `AbsoluteLayout` ](xref:Xamarin.Forms.AbsoluteLayout) deriva `Layout<View>` y hereda un `Children` propiedad. `AbsoluteLayout` implementa un sistema de diseño que requiere que el programador especificar las posiciones de sus elementos secundarios y, opcionalmente, su tamaño. La posición se especifica mediante la esquina superior izquierda del elemento secundario respecto a la esquina superior izquierda de la `AbsoluteLayout` en unidades independientes del dispositivo. `AbsoluteLayout` También implementa una característica de ajuste de tamaño y posición proporcional.

`AbsoluteLayout` debe considerarse como un sistema de diseño especial que se usará sólo cuando el programador puede imponer un tamaño de los elementos secundarios (por ejemplo, `BoxView` elementos) o cuando el tamaño del elemento no afecta a la posición de los demás elementos secundarios. El `HorizontalOptions` y `VerticalOptions` propiedades no tienen ningún efecto en los elementos secundarios de un `AbsoluteLayout`.

Este capítulo también introduce la característica importante de *adjunta propiedades enlazables* que permitir que las propiedades definidas en una clase (en este caso `AbsoluteLayout`) para adjuntarlo a otra clase (un elemento secundario de la `AbsoluteLayout`).

## <a name="absolutelayout-in-code"></a>AbsoluteLayout en código

Puede agregar un elemento secundario de la `Children` colección de un `AbsoluteLayout` utilizando el estándar [ `Add` ](xref:System.Collections.Generic.ICollection`1.Add*) método, pero `AbsoluteLayout` también proporciona extendido [ `Add` ](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) método que le permite especificar un [ `Rectangle` ](xref:Xamarin.Forms.Rectangle). Otro [ `Add` ](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) método sólo requiere un [ `Point` ](xref:Xamarin.Forms.Point), en cuyo caso el elemento secundario no está restringido y ajusta su tamaño.

Puede crear un `Rectangle` valor con un [constructor](xref:Xamarin.Forms.Rectangle.%23ctor(System.Double,System.Double,System.Double,System.Double)) que requiere cuatro valores &mdash; los dos primeros, que indica la posición de la esquina superior izquierda del elemento secundario con respecto a su elemento primario y el segundo (dos) que indica el tamaño del elemento secundario. O bien puede usar un [constructor](xref:Xamarin.Forms.Rectangle.%23ctor(Xamarin.Forms.Point,Xamarin.Forms.Size)) que requiere un `Point` y un [ `Size` ](xref:Xamarin.Forms.Size) valor.

Estos `Add` métodos se muestran en [ **AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo), qué posiciones `BoxView` elementos mediante `Rectangle` valores y un `Label` elemento utilizando solo un `Point` valor.

El [ **ChessboardFixed** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed) ejemplo usa 32 `BoxView` elementos que se va a crear el patrón de tablero de ajedrez. El programa le ofrece la `BoxView` tamaño de los elementos de un disco duro de forma rígida del cuadrado de 35 unidades. El `AbsoluteLayout` tiene su `HorizontalOptions` y `VerticalOptions` establecido en `LayoutOptions.Center`, lo que hace que el `AbsoluteLayout` para tener un tamaño total del cuadrado de 280 unidades.

## <a name="attached-bindable-properties"></a>Adjunta propiedades enlazables

También es posible establecer la posición y, opcionalmente, el tamaño de un elemento secundario de un `AbsoluteLayout` después de que se agregó a la `Children` colección utilizando el método estático [ `AbsoluteLayout.SetLayoutBounds` ](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds(Xamarin.Forms.BindableObject,Xamarin.Forms.Rectangle)). El primer argumento es el elemento secundario; el segundo es un `Rectangle` objeto. Puede especificar que el elemento secundario ajusta su tamaño horizontalmente o verticalmente estableciendo los valores de ancho y alto en la [ `AbsoluteLayout.AutoSize` ](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) constante.

El [ **ChessboardDynamic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic) ejemplo pone el `AbsoluteLayout` en un `ContentView` con un `SizeChanged` controlador para llamar a `AbsoluteLayout.SetLayoutBounds` en todos los elementos secundarios para que sean tan grande como sea posible.  

La propiedad adjunta enlazable que `AbsoluteLayout` define es el campo estático de solo lectura de tipo `BindableProperty` denominado [ `AbsoluteLayout.LayoutBoundsProperty` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty). Estático `AbsoluteLayout.SetLayoutBounds` método se implementa mediante una llamada a `SetValue` secundario con el `AbsoluteLayout.LayoutBoundsProperty`. El elemento secundario contiene un diccionario en el que se almacenan la propiedad adjunta se puede enlazar y su valor. Durante el diseño, la `AbsoluteLayout` puede obtener ese valor mediante una llamada a [ `AbsoluteLayout.GetLayoutBounds` ](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutBounds(Xamarin.Forms.BindableObject)), que se implementa con un `GetValue` llamar.

## <a name="proportional-sizing-and-positioning"></a>Posición y dimensionamiento proporcional

`AbsoluteLayout` implementa un cambio de tamaño proporcional y la posición de la característica. La clase define una segunda propiedad enlazable adjunta, [ `LayoutFlagsProperty` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty), con los métodos estáticos relacionados [ `AbsoluteLayout.SetLayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutFlags(Xamarin.Forms.BindableObject,Xamarin.Forms.AbsoluteLayoutFlags)) y [ `AbsoluteLayout.GetLayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutFlags(Xamarin.Forms.BindableObject)).

El argumento `AbsoluteLayout.SetLayoutFlags` y el valor devuelto de `AbsoluteLayout.GetLayoutFlags` es un valor de tipo [ `AbsoluteLayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayoutFlags), una enumeración con los miembros siguientes:

- [`None`](xref:Xamarin.Forms.AbsoluteLayoutFlags.None) (igual a 0)
- [`XProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.XProportional) (1)
- [`YProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.YProportional) (2)
- [`PositionProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional) (3)
- [`WidthProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional) (4)
- [`HeightProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional) (8)
- [`SizeProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional) (12)
- [`All`](xref:Xamarin.Forms.AbsoluteLayoutFlags.All) (\xFFFFFFFF)

Puede combinar estos elementos con el bit a bit OR operadores de C#.

Con estos conjuntos de indicadores, determinadas propiedades de la `Rectangle` estructura de los límites de diseño utiliza para colocar y dimensionar el elemento secundario se interpretan proporcionalmente.

Cuando el `WidthProportional` se establece la marca, un `Width` valor 1 significa que el elemento secundario es el mismo ancho que el `AbsoluteLayout`. Se usa un enfoque similar para el alto.

El posicionamiento proporcional, el tamaño tiene en cuenta. Cuando el `XProportional` marca se establece, el `X` propiedad de la `Rectangle` límites de diseño es proporcional. Un valor de 0 significa que el elemento secundario izquierdo del borde se coloca en el borde izquierdo de la `AbsoluteLayout`, pero una posición de 1 significa que el borde derecho del elemento secundario se coloca en el borde derecho de la `AbsoluteLayout`, no más allá del borde derecho de la `AbsoluteLayout` como podría expec t. Un `X` secundario horizontalmente en los centros de propiedad de 0,5 el `AbsoluteLayout`.

El [ **ChessboardProportional** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional) ejemplo muestra el uso de ajuste de tamaño proporcional y colocarlos.

## <a name="working-with-proportional-coordinates"></a>Trabajar con coordenadas proporcionales

A veces, resulta más fácil pensar en forma diferente a cómo se implementa en un posicionamiento proporcional el `AbsoluteLayout`. Es preferible trabajar con coordenadas proporcionales donde un `X` propiedad 1 coloca el borde izquierdo del elemento secundario (en lugar del borde derecho) en el borde derecho de la `AbsoluteLayout`.

Este esquema de colocación alternativo puede llamarse "coordinates fraccionario secundarios." Puede convertir de coordenadas fraccionarios secundarios a los límites de diseño necesarios para `AbsoluteLayout` con las fórmulas siguientes:

layoutBounds.X = (fractionalChildCoordinate.X / (1 - layoutBounds.Width))

layoutBounds.Y = (fractionalChildCoordinate.Y / (1 - layoutBounds.Height))

El [ **ProportionalCoordinateCalc** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc) muestra esto.

## <a name="absolutelayout-and-xaml"></a>AbsoluteLayout y XAML

Puede usar un `AbsoluteLayout` en XAML y establecer las propiedades enlazables asociadas en los elementos secundarios de un `AbsoluteLayout` con los valores de atributo de `AbsoluteLayout.LayoutBounds` y `AbsoluteLayout.LayoutFlags`. Esto se muestra en el [ **AbsoluteXamlDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo) y [ **ChessboardXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml) ejemplos. El último programa contiene 32 `BoxView` elementos, pero usa implícita `Style` que incluye el `AbsoluteLayout.LayoutFlags` propiedad para mantener el marcado que se reduce al mínimo.

Es un atributo en XAML que consta de un nombre de clase, un punto y un nombre de propiedad *siempre* una propiedad enlazable adjunta.

## <a name="overlays"></a>Superposiciones

Puede usar `AbsoluteLayout` para construir un *superposición*, que cubre la página con otros controles, quizás para impedir que el usuario interactúa con los controles normales en la página.

El [ **SimpleOverlay** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay) ejemplo demuestra esta técnica y también se muestra la [ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar), que muestra la extensión a la que se ha completado un programa una tarea.

## <a name="some-fun"></a>Divertirse un poco

El [ **DotMatrixClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock) ejemplo muestra la hora actual con una pantalla simulado matricial de 5 x 7. Cada punto es un `BoxView` (hay 228 de ellos) tamaño y se colocan en el `AbsoluteLayout`.

[![Captura de pantalla del reloj matricial triple](images/ch14fg08-small.png "matricial reloj")](images/ch14fg08-large.png#lightbox "reloj matricial")

El [ **BouncingText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText) programa anima dos `Label` objetos rebota horizontal y verticalmente por la pantalla.

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 14 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [Ejemplos de capítulo 14](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)
- [Propiedades asociadas](~/xamarin-forms/xaml/attached-properties.md)
