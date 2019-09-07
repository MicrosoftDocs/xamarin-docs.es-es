---
title: Resumen del capítulo 17. Dominar la cuadrícula
description: 'Crear Mobile Apps con Xamarin. Forms: Resumen del capítulo 17. Dominar la cuadrícula'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 37b5e2bbafa816de27390771ae6daa33c74f7651
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760625"
---
# <a name="summary-of-chapter-17-mastering-the-grid"></a>Resumen del capítulo 17. Dominar la cuadrícula

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)

El [ `Grid` ](xref:Xamarin.Forms.Grid) es un mecanismo eficaz de diseño que organiza sus elementos secundarios en filas y columnas de celdas. Al contrario que el código HTML similar `table` elemento, el `Grid` es únicamente para fines de diseño en lugar de presentación.

## <a name="the-basic-grid"></a>La cuadrícula básica

`Grid` se deriva de [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1), que define un [ `Children` ](xref:Xamarin.Forms.Layout`1.Children) propiedad que `Grid` hereda. Puede rellenar esta recopilación en XAML o código.

### <a name="the-grid-in-xaml"></a>La cuadrícula en XAML

La definición de un `Grid` en XAML comienza generalmente con llene la [ `RowDefinitions` ](xref:Xamarin.Forms.Grid.RowDefinitions) y [ `ColumnDefinitions` ](xref:Xamarin.Forms.Grid.ColumnDefinitions) colecciones de la `Grid` con [ `RowDefinition` ](xref:Xamarin.Forms.RowDefinition) y [ `ColumnDefinition` ](xref:Xamarin.Forms.ColumnDefinition) objetos. Se trata de cómo establecer el número de filas y columnas de la `Grid`y sus propiedades.

`RowDefinition` tiene un [ `Height` ](xref:Xamarin.Forms.RowDefinition.Height) propiedad y `ColumnDefinition` tiene un [ `Width` ](xref:Xamarin.Forms.ColumnDefinition.Width) propiedad, ambos de tipo [ `GridLength` ](xref:Xamarin.Forms.GridLength), una estructura.

En XAML, el [ `GridLengthTypeConverter` ](xref:Xamarin.Forms.GridLengthTypeConverter) convierte las cadenas de texto simple en `GridLength` valores. En segundo plano, el [ `GridLength` constructor](xref:Xamarin.Forms.GridLength.%23ctor(System.Double,Xamarin.Forms.GridUnitType)) crea el `GridLength` valor basado en un número y un valor de tipo [ `GridUnitType` ](xref:Xamarin.Forms.GridUnitType), una enumeración con tres miembros:

- [`Absolute`](xref:Xamarin.Forms.GridUnitType.Absolute) &mdash; el ancho o alto se especifica en unidades independientes del dispositivo (un número en XAML)
- [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto) &mdash; el alto o ancho es situado según el contenido de la celda ("Auto" en XAML)
- [`Star`](xref:Xamarin.Forms.GridUnitType.Star) &mdash; se asigna proporcionalmente sobrante alto o ancho (un número con "\*", denominado *estrella*, en XAML)

Cada miembro secundario de la `Grid` deben asignarse también una fila y columna (explícita o implícitamente). Abarca la fila y columna intervalos son opcionales. Estos se especifican mediante las propiedades enlazables adjuntas &mdash; propiedades definidas por el `Grid` pero establezca en elementos secundarios de la `Grid`. `Grid` define cuatro propiedades enlazables adjuntas estáticas:

- [`RowProperty`](xref:Xamarin.Forms.Grid.RowProperty) &mdash; la fila de base cero; el valor predeterminado es 0
- [`ColumnProperty`](xref:Xamarin.Forms.Grid.ColumnProperty) &mdash; la columna de base cero; el valor predeterminado es 0
- [`RowSpanProperty`](xref:Xamarin.Forms.Grid.RowSpanProperty) &mdash; el número de filas abarca el elemento secundario; el valor predeterminado es 1
- [`ColumnSpanProperty`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) &mdash; el número de columnas abarca el elemento secundario; el valor predeterminado es 1

En el código, un programa puede usar los ocho métodos estáticos para establecer y obtener estos valores:

- [`Grid.SetRow`](xref:Xamarin.Forms.Grid.SetRow(Xamarin.Forms.BindableObject,System.Int32)) y [`Grid.GetRow`](xref:Xamarin.Forms.Grid.GetRow(Xamarin.Forms.BindableObject))
- [`Grid.SetColumn`](xref:Xamarin.Forms.Grid.SetColumn(Xamarin.Forms.BindableObject,System.Int32)) y [`Grid.GetColumn`](xref:Xamarin.Forms.Grid.GetColumn(Xamarin.Forms.BindableObject))
- [`Grid.SetRowSpan`](xref:Xamarin.Forms.Grid.SetRowSpan(Xamarin.Forms.BindableObject,System.Int32)) y [`Grid.GetRowSpan`](xref:Xamarin.Forms.Grid.GetRowSpan(Xamarin.Forms.BindableObject))
- [`Grid.SetColumnSpan`](xref:Xamarin.Forms.Grid.SetColumnSpan(Xamarin.Forms.BindableObject,System.Int32)) y [`Grid.GetColumnSpan`](xref:Xamarin.Forms.Grid.GetColumnSpan(Xamarin.Forms.BindableObject))

En XAML use los siguientes atributos para establecer estos valores:

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

El [ **SimpleGridDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo) ejemplo muestra cómo crear e inicializar un `Grid` en XAML.

El `Grid` hereda el [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) propiedad desde `Layout` y define dos propiedades adicionales que proporcionan el espaciado entre las filas y columnas:

- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) tiene un valor predeterminado de 6
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) tiene un valor predeterminado de 6

El `RowDefinitions` y `ColumnDefinitions` colecciones no son estrictamente necesarias. Si está ausente, el `Grid` crea filas y columnas para la `Grid` elementos secundarios y todas ellas proporciona un valor predeterminado `GridLength` de "\*" (asterisco).

### <a name="the-grid-in-code"></a>La cuadrícula en el código

El [ **GridCodeDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo) ejemplo muestra cómo crear y rellenar un `Grid` en el código. Puede establecer las propiedades adjuntas para cada elemento secundario directamente o indirectamente mediante una llamada adicional `Add` métodos como [ `Add` ](xref:Xamarin.Forms.Grid.IGridList`1.Add*) definido por el [Grid.IGridList<T> ](xref:Xamarin.Forms.Grid.IGridList`1) interfaz.

### <a name="the-grid-bar-chart"></a>El gráfico de barras de la cuadrícula

El [ **GridBarChart** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart) ejemplo muestra cómo agregar varios `BoxView` elementos a un `Grid` con la mayor parte [ `AddHorizontal` ](xref:Xamarin.Forms.Grid.IGridList`1.AddHorizontal*) método. De forma predeterminada, estos `BoxView` elementos tienen el mismo ancho. El alto de cada `BoxView` , a continuación, se puede controlar que se asemeje a un gráfico de barras.

El `Grid` en el **GridBarChart** recursos compartidos de ejemplo una `AbsoluteLayout` primario con un inicialmente invisible `Frame`. El programa también establece una `TapGestureRecognizer` en cada `BoxView` para usar el `Frame` para mostrar información acerca de la barra de derivados.

### <a name="alignment-in-the-grid"></a>Alineación de la cuadrícula

El [ **GridAlignment** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment) ejemplo muestra cómo usar el `VerticalOptions` y `HorizontalOptions` propiedades para alinear los elementos secundarios en un `Grid` celda.

El [ **SpacingButtons** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons) ejemplo igualmente espacios `Button` elementos en el centro de `Grid` celdas.

### <a name="cell-dividers-and-borders"></a>Separadores de celdas y bordes

El `Grid` no incluye una característica que dibuja los bordes o separadores de celdas. Sin embargo, puede crear sus propios.

El [ **GridCellDividers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers) muestra cómo definir otras filas y columnas específicamente para fino `BoxView` elementos para imitar líneas divisorias.

El [ **GridCellBorders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders) programa no crea ninguna de las celdas adicional, pero en su lugar se alinea `BoxView` elementos en cada celda para imitar un borde de celda.

## <a name="almost-real-life-grid-examples"></a>Ejemplos de la cuadrícula de casi reales

El [ **KeypadGrid** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid) ejemplo usa un `Grid` para mostrar un teclado numérico:

[![Triple captura de pantalla de cuadrícula de teclado](images/ch17fg12-small.png "teclado cuadrícula")](images/ch17fg12-large.png#lightbox "cuadrícula de teclado")

### <a name="responding-to-orientation-changes"></a>Responder a los cambios de orientación

El `Grid` puede ayudar a estructurar un programa para responder a los cambios de orientación. El [ **GridRgbSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders) muestra una técnica que mueve un elemento entre una segunda fila de un teléfono con orientación vertical y la segunda columna de un teléfono orientado a horizontal.

Inicializa el programa `Slider` elementos a un intervalo de 0 a 255 y utiliza los enlaces de datos para mostrar el valor de los controles deslizantes en formato hexadecimal. Dado que el `Slider` valores son de punto flotante y la cadena de formato para hexadecimal solo funciona con números enteros, de .NET un [ `DoubleToIntConvert` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca de ayuda.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 17 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [Ejemplos de capítulo 17](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [Grid](~/xamarin-forms/user-interface/layouts/grid.md)
