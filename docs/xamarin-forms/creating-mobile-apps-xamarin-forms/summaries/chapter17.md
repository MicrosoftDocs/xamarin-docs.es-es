---
title: Resumen del capítulo 17. Dominio de la cuadrícula
description: 'Creación de aplicaciones móviles con Xamarin.Forms: Resumen del capítulo 17. Dominio de la cuadrícula'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d2904270b601b6602457873e8b0180c13a0d6d95
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373463"
---
# <a name="summary-of-chapter-17-mastering-the-grid"></a>Resumen del capítulo 17. Dominio de la cuadrícula

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)

> [!NOTE]
> Este libro se publicó en la primavera de 2016 y no se ha actualizado desde entonces. Gran parte del libro sigue siendo útil, pero algunos de los materiales están anticuados y algunos temas ya no son completamente correctos o completos.

[`Grid`](xref:Xamarin.Forms.Grid) es un eficaz mecanismo de diseño que organiza sus elementos secundarios en filas y columnas de celdas. A diferencia del elemento `table` de HTML similar, `Grid` es exclusivamente para fines de diseño y no para presentación.

## <a name="the-basic-grid"></a>Cuadrícula básica

`Grid` se deriva de [`Layout<View>`](xref:Xamarin.Forms.Layout`1), que define una propiedad [`Children`](xref:Xamarin.Forms.Layout`1.Children) que hereda `Grid`. Puede rellenar esta colección en XAML o en código.

### <a name="the-grid-in-xaml"></a>Cuadrícula en XAML

La definición de `Grid` en XAML comienza generalmente con el rellenado de las colecciones [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) y [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) de `Grid` con los objetos [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) y [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition). Así es como se establece el número de filas y columnas de `Grid`, y sus propiedades.

`RowDefinition` tiene una propiedad [`Height`](xref:Xamarin.Forms.RowDefinition.Height) y `ColumnDefinition` tiene una propiedad [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width), ambas de tipo [`GridLength`](xref:Xamarin.Forms.GridLength), una estructura.

En XAML, [`GridLengthTypeConverter`](xref:Xamarin.Forms.GridLengthTypeConverter) convierte las cadenas de texto simples en valores de `GridLength`. En segundo plano, el [`GridLength` constructor](xref:Xamarin.Forms.GridLength.%23ctor(System.Double,Xamarin.Forms.GridUnitType)) crea el valor `GridLength` basándose en un número y un valor de tipo [`GridUnitType`](xref:Xamarin.Forms.GridUnitType), una enumeración con tres miembros:

- [`Absolute`](xref:Xamarin.Forms.GridUnitType.Absolute) &mdash; el ancho o el alto se especifica en unidades independientes del dispositivo (un número en XAML)
- [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto) &mdash; el alto o ancho se ajusta automáticamente según el contenido de la celda ("auto" en XAML)
- [`Star`](xref:Xamarin.Forms.GridUnitType.Star) &mdash; el alto o el ancho sobrante se asigna proporcionalmente (un número con "\*", denominado *star*, en XAML)

A cada elemento secundario de `Grid` también se le debe asignar una fila y una columna (ya sea de forma explícita o implícita). Los intervalos de filas y los intervalos de columnas son opcionales. Todos ellos se especifican mediante propiedades enlazables asociadas &mdash; propiedades definidas por `Grid` pero establecidas en elementos secundarios de `Grid`. `Grid` define cuatro propiedades enlazables estáticas asociadas:

- [`RowProperty`](xref:Xamarin.Forms.Grid.RowProperty) &mdash; fila de base cero; el valor predeterminado es 0.
- [`ColumnProperty`](xref:Xamarin.Forms.Grid.ColumnProperty) &mdash; columna de base cero; el valor predeterminado es 0.
- [`RowSpanProperty`](xref:Xamarin.Forms.Grid.RowSpanProperty) &mdash; número de filas que abarca el elemento secundario; el valor predeterminado es 1.
- [`ColumnSpanProperty`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) &mdash; número de columnas que abarca el elemento secundario; el valor predeterminado es 1.

En el código, un programa puede usar ocho métodos estáticos para establecer y obtener estos valores:

- [`Grid.SetRow`](xref:Xamarin.Forms.Grid.SetRow(Xamarin.Forms.BindableObject,System.Int32)) y [`Grid.GetRow`](xref:Xamarin.Forms.Grid.GetRow(Xamarin.Forms.BindableObject))
- [`Grid.SetColumn`](xref:Xamarin.Forms.Grid.SetColumn(Xamarin.Forms.BindableObject,System.Int32)) y [`Grid.GetColumn`](xref:Xamarin.Forms.Grid.GetColumn(Xamarin.Forms.BindableObject))
- [`Grid.SetRowSpan`](xref:Xamarin.Forms.Grid.SetRowSpan(Xamarin.Forms.BindableObject,System.Int32)) y [`Grid.GetRowSpan`](xref:Xamarin.Forms.Grid.GetRowSpan(Xamarin.Forms.BindableObject))
- [`Grid.SetColumnSpan`](xref:Xamarin.Forms.Grid.SetColumnSpan(Xamarin.Forms.BindableObject,System.Int32)) y [`Grid.GetColumnSpan`](xref:Xamarin.Forms.Grid.GetColumnSpan(Xamarin.Forms.BindableObject))

En XAML, use los siguientes atributos para establecer estos valores:

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

En el ejemplo [**SimpleGridDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo) se muestra cómo crear e inicializar `Grid` en XAML.

`Grid` hereda la propiedad [`Padding`](xref:Xamarin.Forms.Layout.Padding) de `Layout` y define dos propiedades adicionales que proporcionan espaciado entre las filas y las columnas:

- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) tiene un valor predeterminado de 6.
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) tiene un valor predeterminado de 6.

Las colecciones `RowDefinitions` y `ColumnDefinitions` no son estrictamente necesarias. En su ausencia, `Grid` crea filas y columnas para los elementos secundarios de `Grid` y les asigna un valor `GridLength` predeterminado de "\*" (star).

### <a name="the-grid-in-code"></a>La cuadrícula en el código

En el ejemplo [**GridCodeDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo) se muestra cómo crear y rellenar `Grid` en el código. Puede establecer las propiedades asociadas para cada elemento secundario directamente, o indirectamente llamando a métodos `Add` adicionales, como [`Add`](xref:Xamarin.Forms.Grid.IGridList`1.Add*) definidos por la interfaz [Grid.IGridList<T>](xref:Xamarin.Forms.Grid.IGridList`1).

### <a name="the-grid-bar-chart"></a>Gráfico de barras de cuadrícula

En el ejemplo [**GridBarChart**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart) se muestra cómo agregar varios elementos `BoxView` a `Grid` mediante el método [`AddHorizontal`](xref:Xamarin.Forms.Grid.IGridList`1.AddHorizontal*) masivo. De forma predeterminada, estos elementos `BoxView` tienen el mismo ancho. El alto de cada `BoxView` se puede controlar para que se parezca a un gráfico de barras.

`Grid` del ejemplo **GridBarChart** comparte un elemento `AbsoluteLayout` primario con un elemento `Frame` inicialmente invisible. El programa también establece un elemento `TapGestureRecognizer` en cada `BoxView` para usar `Frame` a fin de mostrar información sobre la barra punteada.

### <a name="alignment-in-the-grid"></a>Alineación en la cuadrícula

En el ejemplo [**GridAlignment**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment) se muestra cómo usar las propiedades `VerticalOptions` y `HorizontalOptions` para alinear los elementos secundarios en una celda de `Grid`.

En el ejemplo [**SpacingButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons) se espacian uniformemente los elementos `Button` centrados en las celdas de `Grid`.

### <a name="cell-dividers-and-borders"></a>Separadores de celdas y bordes

El elemento `Grid` no incluye una característica que dibuje separadores de celdas y bordes. Sin embargo, puede crear la suya propia.

[**GridCellDividers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers) muestra cómo definir filas y columnas adicionales específicamente para que los elementos de `BoxView` finos imiten las líneas de división.

El programa [**GridCellBorders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders) no crea ninguna celda adicional, sino que alinea los elementos `BoxView` de cada celda para imitar el borde de una celda.

## <a name="almost-real-life-grid-examples"></a>Ejemplos de cuadrícula casi de la vida real

En el ejemplo [**KeypadGrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid) se usa un elemento `Grid` para mostrar un teclado:

[![Captura de pantalla triple de la cuadrícula del teclado](images/ch17fg12-small.png "Cuadrícula del teclado")](images/ch17fg12-large.png#lightbox "Cuadrícula del teclado")

### <a name="responding-to-orientation-changes"></a>Respuesta a los cambios de orientación

`Grid` puede ayudar a estructurar un programa para que responda a los cambios de orientación. En el ejemplo [**GridRgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders) se muestra una técnica que mueve un elemento entre una segunda fila de un teléfono con orientación vertical y la segunda columna de un teléfono con orientación horizontal.

El programa inicializa `Slider` elementos en un intervalo de 0 a 255 y usa enlaces de datos para mostrar el valor de los controles deslizantes en formato hexadecimal. Dado que los valores de `Slider` son números de punto flotante, y la cadena de formato .NET para el formato hexadecimal solo funciona con enteros, una clase [`DoubleToIntConvert`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs) en la biblioteca [ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) sirve de ayuda.

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 17 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [Ejemplos del capítulo 17](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [Grid](~/xamarin-forms/user-interface/layouts/grid.md)
