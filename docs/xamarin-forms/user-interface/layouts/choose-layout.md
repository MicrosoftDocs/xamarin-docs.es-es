---
title: Elegir un Xamarin.Forms diseño
description: Xamarin.Forms las clases de diseño permiten organizar y agrupar los controles de interfaz de usuario en la aplicación.
ms.prod: xamarin
ms.assetid: 05A39752-A174-447E-A30D-3CC9EF98CB96
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/21/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6f5702695698881a30cfbf3e63110856a97a2c24
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555352"
---
# <a name="choose-a-no-locxamarinforms-layout"></a>Elegir un Xamarin.Forms diseño

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

Xamarin.Forms las clases de diseño permiten organizar y agrupar los controles de interfaz de usuario en la aplicación. La elección de una clase de diseño requiere saber cómo el diseño coloca sus elementos secundarios y cómo el diseño dimensiona sus elementos secundarios. Además, puede ser necesario anidar diseños para crear el diseño deseado.

En la imagen siguiente se muestran los diseños típicos que se pueden lograr con las Xamarin.Forms clases de diseño principales:

[![Las clases de diseño principales en::: no-LOC (Xamarin. Forms):::](images/layouts.png "::: no-LOC (Xamarin. Forms)::: Layout (clases)")](images/layouts-large.png#lightbox "::: no-LOC (Xamarin. Forms)::: Layout (clases)")

## <a name="stacklayout"></a>StackLayout

Un [`StackLayout`](xref:Xamarin.Forms.StackLayout) organiza los elementos de una pila unidimensional, ya sea horizontal o verticalmente. La [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) propiedad especifica la dirección de los elementos y la orientación predeterminada es [`Vertical`](xref:Xamarin.Forms.StackOrientation) . `StackLayout` normalmente se usa para organizar una subsección de la interfaz de usuario en una página.

En el código XAML siguiente se muestra cómo crear un [`StackLayout`](xref:Xamarin.Forms.StackLayout) objeto vertical que contiene tres [`Label`](xref:Xamarin.Forms.Label) objetos:

```xaml
<StackLayout Margin="20,35,20,25">
    <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
    <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
    <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
</StackLayout>
```

En [`StackLayout`](xref:Xamarin.Forms.StackLayout) , si el tamaño de un elemento no se establece explícitamente, se expande para rellenar el ancho disponible o el alto si la [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) propiedad está establecida en [`Horizontal`](xref:Xamarin.Forms.StackOrientation) .

A [`StackLayout`](xref:Xamarin.Forms.StackLayout) menudo se usa como diseño primario, que contiene otros diseños secundarios. Sin embargo, `StackLayout` no se debe utilizar para reproducir un [`Grid`](xref:Xamarin.Forms.Grid) diseño utilizando una combinación de `StackLayout` objetos. En el código siguiente se muestra un ejemplo de esta mala práctica:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Name:" />
            <Entry Placeholder="Enter your name" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Age:" />
            <Entry Placeholder="Enter your age" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Occupation:" />
            <Entry Placeholder="Enter your occupation" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Address:" />
            <Entry Placeholder="Enter your address" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

Es una pérdida de tiempo porque se realizan cálculos de diseño innecesarios. En su lugar, el diseño deseado puede lograrse mejor mediante el uso de [`Grid`](xref:Xamarin.Forms.Grid) .

> [!TIP]
> Al utilizar [`StackLayout`](xref:Xamarin.Forms.StackLayout) , asegúrese de que solo un elemento secundario está establecido en [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) . Esta propiedad garantiza que el elemento secundario especificado ocupa el mayor espacio que el `StackLayout` puede asignarle y es poco rentable realizar estos cálculos más de una vez.

Para obtener más información, vea [ Xamarin.Forms StackLayout](stacklayout.md).

## <a name="grid"></a>Cuadrícula

[`Grid`](xref:Xamarin.Forms.Grid)Se usa para mostrar elementos en filas y columnas, que pueden tener tamaños proporcionales o absolutos. Las filas y columnas de una cuadrícula se especifican con las [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) propiedades y.

Para colocar los elementos en [`Grid`](xref:Xamarin.Forms.Grid) celdas específicas, use [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) las [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) propiedades adjuntas y. Para que los elementos abarquen varias filas y columnas, utilice [`Grid.RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) y [`Grid.ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) las propiedades adjuntas.

> [!NOTE]
> Un [`Grid`](xref:Xamarin.Forms.Grid) diseño no se debe confundir con las tablas y no está diseñado para presentar datos tabulares. A diferencia de las tablas HTML, una `Grid` está pensada para diseñar el contenido. Para Mostrar datos tabulares, considere la posibilidad de usar un [control ListView](~/xamarin-forms/user-interface/listview/index.md), [CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)o [TableView](~/xamarin-forms/user-interface/tableview.md).

En el siguiente código XAML se muestra cómo crear un [`Grid`](xref:Xamarin.Forms.Grid) con dos filas y dos columnas:

```xaml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="50" />
        <RowDefinition Height="50" />
    </Grid.RowDefinitions>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="Auto" />
        <ColumnDefinition />
    </Grid.ColumnDefinitions>    
    <Label Text="Column 0, Row 0"
           WidthRequest="200" />
    <Label Grid.Column="1"
           Text="Column 1, Row 0" />
    <Label Grid.Row="1"
           Text="Column 0, Row 1" />
    <Label Grid.Column="1"
           Grid.Row="1"
           Text="Column 1, Row 1" />
</Grid>
```

En este ejemplo, el ajuste de tamaño funciona de la siguiente manera:

- Cada fila tiene un alto explícito de 50 unidades independientes del dispositivo.
- El ancho de la primera columna se establece en [`Auto`](xref:Xamarin.Forms.GridLength.Auto) y, por tanto, es tan ancha como sea necesario para sus elementos secundarios. En este caso, 200 unidades independientes del dispositivo de ancho para acomodar el ancho de la primera [`Label`](xref:Xamarin.Forms.Label) .

El espacio se puede distribuir dentro de una columna o fila mediante el ajuste automático de tamaño, lo que permite que las columnas y las filas se ajusten a su contenido. Esto se logra estableciendo el alto de un [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) , o el ancho de un [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) , en [`Auto`](xref:Xamarin.Forms.GridLength.Auto) . El ajuste de tamaño proporcional también se puede usar para distribuir el espacio disponible entre las filas y las columnas de la cuadrícula mediante proporciones ponderadas. Esto se logra estableciendo el alto de un `RowDefinition` , o el ancho de `ColumnDefinition` , en un valor que usa el `*` operador.

> [!CAUTION]
> Intente asegurarse de que el tamaño de las filas y columnas es el máximo posible [`Auto`](xref:Xamarin.Forms.GridLength.Auto) . Cada fila o columna de tamaño automático hará que el motor de diseño tenga que realizar cálculos de diseño adicionales. En su lugar, use filas y columnas de tamaño fijo si es posible. También puede establecer filas y columnas para ocupar una cantidad proporcional de espacio con el [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) valor de enumeración.

Para obtener más información, vea [ Xamarin.Forms Grid](grid.md).

## <a name="flexlayout"></a>FlexLayout

Un [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) es similar a un [`StackLayout`](xref:Xamarin.Forms.StackLayout) en que muestra los elementos secundarios de forma horizontal o vertical en una pila. Sin embargo, `FlexLayout` también puede ajustar sus elementos secundarios si hay demasiados para caber en una sola fila o columna, y también permite un control más granular del tamaño, la orientación y la alineación de sus elementos secundarios.

En el código XAML siguiente se muestra cómo crear un [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) que muestra sus vistas en una sola columna:

```xaml
<FlexLayout Direction="Column"
            AlignItems="Center"
            JustifyContent="SpaceEvenly">
    <Label Text="FlexLayout in Action" />
    <Button Text="Button" />
    <Label Text="Another Label" />
</FlexLayout>
```

En este ejemplo, el diseño funciona de la siguiente manera:

- La [`Direction`](xref:Xamarin.Forms.FlexLayout.Direction) propiedad se establece en `Column` , lo que hace que los elementos secundarios de `FlexLayout` se organicen en una sola columna de elementos.
- La [`AlignItems`](xref:Xamarin.Forms.FlexLayout.AlignItems) propiedad se establece en `Center` , lo que hace que cada elemento se Centre horizontalmente.
- La [`JustifyContent`](xref:Xamarin.Forms.FlexLayout.JustifyContent) propiedad se establece en `SpaceEvenly` , que asigna todo el espacio vertical sobrante por igual entre todos los elementos y por encima del primer elemento, y por debajo del último elemento.

Para obtener más información, vea [ Xamarin.Forms FlexLayout](flex-layout.md).

## <a name="relativelayout"></a>RelativeLayout

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)Se utiliza para colocar y ajustar el tamaño de los elementos en relación con las propiedades del diseño o de los elementos del mismo nivel. De forma predeterminada, un elemento se coloca en la esquina superior izquierda del diseño. `RelativeLayout`Se puede usar para crear interfaces de IU que escalen proporcionalmente entre tamaños de dispositivo.

Dentro de [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) , las posiciones y los tamaños se especifican como restricciones. Las restricciones tienen [`Factor`](xref:Xamarin.Forms.ConstraintExpression.Factor) propiedades y [`Constant`](xref:Xamarin.Forms.ConstraintExpression.Constant) , que se pueden usar para definir posiciones y tamaños como múltiplos (o fracciones) de propiedades de otros objetos, además de una constante. Además, las constantes pueden ser negativas.

> [!NOTE]
> Un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) admite la colocación de elementos fuera de sus propios límites.

En el código XAML siguiente se muestra cómo organizar los elementos en [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) :

```xaml
<RelativeLayout>
    <BoxView Color="Blue"
             HeightRequest="50"
             WidthRequest="50"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0}" />
    <BoxView Color="Red"
             HeightRequest="50"
             WidthRequest="50"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=.85}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0}" />
    <BoxView x:Name="pole"
             Color="Gray"
             WidthRequest="15"
             RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.75}"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=.45}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.25}" />
    <BoxView Color="Green"
             RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.10, Constant=10}"
             RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=.2, Constant=20}"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=X, Constant=15}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=Y, Constant=0}" />
</RelativeLayout>
```

En este ejemplo, el diseño funciona de la siguiente manera:

- El azul [`BoxView`](xref:Xamarin.Forms.BoxView) recibe un tamaño explícito de unidades independientes del dispositivo 50x50. Se coloca en la esquina superior izquierda del diseño, que es la posición predeterminada.
- Al rojo [`BoxView`](xref:Xamarin.Forms.BoxView) se le asigna un tamaño explícito de unidades independientes del dispositivo 50x50. Se coloca en la esquina superior derecha del diseño.
- Al gris [`BoxView`](xref:Xamarin.Forms.BoxView) se le asigna un ancho explícito de 15 unidades independientes del dispositivo, y su alto se establece en el 75% del alto de su elemento primario.
- El color verde [`BoxView`](xref:Xamarin.Forms.BoxView) no tiene un tamaño explícito. Su posición se establece en relación con el `BoxView` nombre `pole` .

> [!WARNING]
> Evite el uso de un `RelativeLayout` siempre que sea posible. Como resultado, la CPU tendrá que realizar mucho más trabajo.

Para obtener más información, vea [ Xamarin.Forms RelativeLayout](relativelayout.md).

## <a name="absolutelayout"></a>AbsoluteLayout

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)Se utiliza para colocar y ajustar el tamaño de los elementos mediante valores explícitos o valores relativos al tamaño del diseño. La posición se especifica en la esquina superior izquierda del elemento secundario en relación con la esquina superior izquierda de `AbsoluteLayout` .

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)Debe considerarse como un diseño especial para usarse solo cuando se puede imponer un tamaño en los elementos secundarios o cuando el tamaño del elemento no afecta a la posición de otros elementos secundarios. Un uso estándar de este diseño es crear una superposición, que abarca la página con otros controles, quizás para impedir que el usuario interactúe con los controles normales de la página.

> [!IMPORTANT]
> Las propiedades `HorizontalOptions` y `VerticalOptions` no tienen ningún efecto en los elementos secundarios de un `AbsoluteLayout`.

Dentro de [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) , la [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) propiedad adjunta se utiliza para especificar la posición horizontal, la posición vertical, el ancho y el alto de un elemento. Además, la [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) propiedad adjunta especifica cómo se interpretarán los límites del diseño.

En el siguiente código XAML se muestra cómo organizar los elementos de [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) :

```xaml
<AbsoluteLayout Margin="40">
    <BoxView Color="Red"
             AbsoluteLayout.LayoutFlags="PositionProportional"
             AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100"
             Rotation="30" />
    <BoxView Color="Green"
             AbsoluteLayout.LayoutFlags="PositionProportional"
             AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100"
             Rotation="60" />
    <BoxView Color="Blue"
             AbsoluteLayout.LayoutFlags="PositionProportional"
             AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" />
</AbsoluteLayout>
```

En este ejemplo, el diseño funciona de la siguiente manera:

- [`BoxView`](xref:Xamarin.Forms.BoxView)A cada una se le asigna un tamaño explícito de 100x100 y se muestra en la misma posición, centrado horizontalmente.
- El color rojo [`BoxView`](xref:Xamarin.Forms.BoxView) gira 30 grados y el verde `BoxView` gira 60 grados.
- En cada [`BoxView`](xref:Xamarin.Forms.BoxView) , la [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) propiedad adjunta se establece en `PositionProportional` , lo que indica que la posición es proporcional al espacio restante después de que se tenga en cuenta el ancho y el alto.

> [!CAUTION]
> Evite utilizar la [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) propiedad siempre que sea posible, ya que hará que el motor de diseño realice cálculos de diseño adicionales.

Para obtener más información, vea [ Xamarin.Forms AbsoluteLayout](absolutelayout.md).

## <a name="input-transparency"></a>Transparencia de entrada

Cada elemento visual tiene una [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent) propiedad que se utiliza para definir si el elemento recibe la entrada. Su valor predeterminado es `false` , asegurándose de que el elemento recibe la entrada.

Cuando esta propiedad se establece en una clase de diseño, su valor se transfiere a los elementos secundarios. Por consiguiente, si [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent) se establece la propiedad en `true` en una clase de diseño, todos los elementos del diseño no recibirán una entrada.

## <a name="layout-performance"></a>Rendimiento del diseño

Para obtener el mejor rendimiento posible del diseño, siga las instrucciones que se indican en [optimizar el rendimiento del diseño](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance).

Además, el rendimiento de la representación de páginas también se puede mejorar mediante la compresión de diseño, que quita los diseños especificados del árbol visual. Para obtener más información, vea [compresión de diseño](layout-compression.md).

## <a name="related-links"></a>Vínculos relacionados

- [Diseño (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Xamarin.Forms Diseños (vídeo)](https://youtu.be/4HlLjTZQzjM)
- [Xamarin.Forms StackLayout](stacklayout.md)
- [Xamarin.Forms Cuadrícula](grid.md)
- [Xamarin.Forms FlexLayout](flex-layout.md)
- [Xamarin.Forms AbsoluteLayout](absolutelayout.md)
- [Xamarin.Forms RelativeLayout](relativelayout.md)
- [Optimización del rendimiento del diseño](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)
- [Compresión de diseño](layout-compression.md)