---
title: Elegir un diseño de Xamarin. Forms
description: Las clases de diseño de Xamarin. Forms permiten organizar y agrupar los controles de interfaz de usuario en la aplicación.
ms.prod: xamarin
ms.assetid: 05A39752-A174-447E-A30D-3CC9EF98CB96
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/21/2018
ms.openlocfilehash: 161da8948f356fef997a411855598bc99d2f49b7
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "69894002"
---
# <a name="choose-a-xamarinforms-layout"></a>Elegir un diseño de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

Las clases de diseño de Xamarin. Forms permiten organizar y agrupar los controles de interfaz de usuario en la aplicación. La elección de una clase de diseño requiere saber cómo el diseño coloca sus elementos secundarios y cómo el diseño dimensiona sus elementos secundarios. Además, puede ser necesario anidar diseños para crear el diseño deseado.

En la imagen siguiente se muestran los diseños típicos que se pueden lograr con las clases de diseño principales de Xamarin. Forms:

[![Clases de diseño principales de Xamarin. Forms](images/layouts.png "Clases de diseño de Xamarin. Forms")](images/layouts-large.png#lightbox "Clases de diseño de Xamarin. Forms")

## <a name="stacklayout"></a>StackLayout

[@No__t_1](xref:Xamarin.Forms.StackLayout) organiza los elementos de una pila unidimensional, ya sea horizontal o verticalmente. La propiedad [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) especifica la dirección de los elementos y la orientación predeterminada es [`Vertical`](xref:Xamarin.Forms.StackOrientation). normalmente `StackLayout` se usa para organizar una subsección de la interfaz de usuario en una página.

En el código XAML siguiente se muestra cómo crear un [`StackLayout`](xref:Xamarin.Forms.StackLayout) vertical que contiene tres objetos [`Label`](xref:Xamarin.Forms.Label) :

```xaml
<StackLayout Margin="20,35,20,25">
    <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
    <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
    <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
</StackLayout>
```

En un [`StackLayout`](xref:Xamarin.Forms.StackLayout), si el tamaño de un elemento no se establece explícitamente, se expande para rellenar el ancho disponible o el alto si la propiedad [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) está establecida en [`Horizontal`](xref:Xamarin.Forms.StackOrientation).

Un [`StackLayout`](xref:Xamarin.Forms.StackLayout) se usa a menudo como diseño primario, que contiene otros diseños secundarios. Sin embargo, no se debe usar un `StackLayout` para reproducir un diseño de [`Grid`](xref:Xamarin.Forms.Grid) mediante una combinación de objetos `StackLayout`. En el código siguiente se muestra un ejemplo de esta mala práctica:

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

Es una pérdida de tiempo porque se realizan cálculos de diseño innecesarios. En su lugar, el diseño deseado puede lograrse mejor mediante el uso de un [`Grid`](xref:Xamarin.Forms.Grid).

> [!TIP]
> Al utilizar un [`StackLayout`](xref:Xamarin.Forms.StackLayout), asegúrese de que solo se establece un elemento secundario en [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands). Esta propiedad garantiza que el elemento secundario especificado ocupa el mayor espacio que el `StackLayout` puede asignarle y es poco rentable realizar estos cálculos más de una vez.

Para obtener más información, consulte [Xamarin. Forms StackLayout](stack-layout.md).

## <a name="grid"></a>Cuadrícula

Un [`Grid`](xref:Xamarin.Forms.Grid) se usa para mostrar elementos en filas y columnas, que pueden tener tamaños proporcionales o absolutos. Las filas y columnas de una cuadrícula se especifican con las propiedades [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) y [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) .

Para colocar los elementos en celdas de [`Grid`](xref:Xamarin.Forms.Grid) específicas, utilice las propiedades adjuntas [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) y [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) . Para que los elementos abarquen varias filas y columnas, use las propiedades adjuntas [`Grid.RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) y [`Grid.ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) .

> [!NOTE]
> Un diseño [`Grid`](xref:Xamarin.Forms.Grid) no debe confundirse con las tablas y no está diseñado para presentar datos tabulares. A diferencia de las tablas HTML, una `Grid` está diseñada para diseñar el contenido. Para Mostrar datos tabulares, considere la posibilidad de usar un [control ListView](~/xamarin-forms/user-interface/listview/index.md), [CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)o [TableView](~/xamarin-forms/user-interface/tableview.md).

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
           Width="200" />
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
- El ancho de la primera columna se establece en [`Auto`](xref:Xamarin.Forms.GridLength.Auto)y, por tanto, es tan ancha como sea necesario para sus elementos secundarios. En este caso, 200 unidades independientes del dispositivo de ancho para acomodar el ancho del primer [`Label`](xref:Xamarin.Forms.Label).

El espacio se puede distribuir dentro de una columna o fila mediante el ajuste automático de tamaño, lo que permite que las columnas y las filas se ajusten a su contenido. Esto se logra estableciendo el alto de un [`RowDefinition`](xref:Xamarin.Forms.RowDefinition), o el ancho de un [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition), en [`Auto`](xref:Xamarin.Forms.GridLength.Auto). El ajuste de tamaño proporcional también se puede usar para distribuir el espacio disponible entre las filas y las columnas de la cuadrícula mediante proporciones ponderadas. Esto se logra estableciendo el alto de un `RowDefinition`, o el ancho de un `ColumnDefinition`, en un valor que usa el operador `*`.

> [!CAUTION]
> Intente asegurarse de que la cantidad de filas y columnas que sea posible está establecida en [`Auto`](xref:Xamarin.Forms.GridLength.Auto) tamaño. Cada fila o columna de tamaño automático hará que el motor de diseño tenga que realizar cálculos de diseño adicionales. En su lugar, use filas y columnas de tamaño fijo si es posible. También puede establecer filas y columnas para ocupar una cantidad proporcional de espacio con el valor de enumeración [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) .

Para obtener más información, consulte [cuadrícula de Xamarin. Forms](grid.md).

## <a name="flexlayout"></a>FlexLayout

Un [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) es similar a un [`StackLayout`](xref:Xamarin.Forms.StackLayout) en que muestra los elementos secundarios de forma horizontal o vertical en una pila. Sin embargo, un `FlexLayout` también puede ajustar sus elementos secundarios si hay demasiados para caber en una sola fila o columna, y también permite un control más granular del tamaño, la orientación y la alineación de sus elementos secundarios.

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

- La propiedad [`Direction`](xref:Xamarin.Forms.FlexLayout.Direction) está establecida en `Column`, lo que hace que los elementos secundarios de la `FlexLayout` se organicen en una sola columna de elementos.
- La propiedad [`AlignItems`](xref:Xamarin.Forms.FlexLayout.AlignItems) se establece en `Center`, lo que hace que cada elemento se Centre horizontalmente.
- La propiedad [`JustifyContent`](xref:Xamarin.Forms.FlexLayout.JustifyContent) está establecida en `SpaceEvenly`, que asigna todo el espacio vertical sobrante por igual entre todos los elementos y por encima del primer elemento, y por debajo del último elemento.

Para obtener más información, consulte [Xamarin. Forms FlexLayout](flex-layout.md).

## <a name="relativelayout"></a>RelativeLayout

Un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) se utiliza para colocar y ajustar el tamaño de los elementos en relación con las propiedades del diseño o de los elementos del mismo nivel. De forma predeterminada, un elemento se coloca en la esquina superior izquierda del diseño. Un `RelativeLayout` se puede usar para crear interfaces de IU que escalen proporcionalmente entre tamaños de dispositivo.

Dentro de un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout), las posiciones y los tamaños se especifican como restricciones. Las restricciones tienen [`Factor`](xref:Xamarin.Forms.ConstraintExpression.Factor) y [`Constant`](xref:Xamarin.Forms.ConstraintExpression.Constant) propiedades, que se pueden usar para definir posiciones y tamaños como múltiplos (o fracciones) de propiedades de otros objetos, además de una constante. Además, las constantes pueden ser negativas.

> [!NOTE]
> Un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) admite la colocación de elementos fuera de sus propios límites.

En el siguiente código XAML se muestra cómo organizar los elementos de un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout):

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

- El [`BoxView`](xref:Xamarin.Forms.BoxView) azul recibe un tamaño explícito de unidades independientes del dispositivo 50x50. Se coloca en la esquina superior izquierda del diseño, que es la posición predeterminada.
- El [`BoxView`](xref:Xamarin.Forms.BoxView) rojo tiene un tamaño explícito de unidades independientes del dispositivo 50x50. Se coloca en la esquina superior derecha del diseño.
- A la [`BoxView`](xref:Xamarin.Forms.BoxView) gris se le asigna un ancho explícito de 15 unidades independientes del dispositivo, y su alto se establece en el 75% del alto de su elemento primario.
- El [`BoxView`](xref:Xamarin.Forms.BoxView) verde no tiene un tamaño explícito. Su posición se establece en relación con el `BoxView` denominado `pole`.

> [!WARNING]
> Evite el uso de una `RelativeLayout` siempre que sea posible. Como resultado, la CPU tendrá que realizar mucho más trabajo.

Para obtener más información, consulte [Xamarin. Forms RelativeLayout](relative-layout.md).

## <a name="absolutelayout"></a>AbsoluteLayout

Un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) se utiliza para colocar y ajustar el tamaño de los elementos mediante valores explícitos o valores relativos al tamaño del diseño. La posición se especifica en la esquina superior izquierda del elemento secundario en relación con la esquina superior izquierda del `AbsoluteLayout`.

Una [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) debe considerarse como un diseño especial para usarse solo cuando se puede imponer un tamaño en elementos secundarios o cuando el tamaño del elemento no afecta a la posición de otros elementos secundarios. Un uso estándar de este diseño es crear una superposición, que abarca la página con otros controles, quizás para impedir que el usuario interactúe con los controles normales de la página.

> [!IMPORTANT]
> Las propiedades `HorizontalOptions` y `VerticalOptions` no tienen ningún efecto en los elementos secundarios de un `AbsoluteLayout`.

Dentro de un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout), la propiedad adjunta [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) se utiliza para especificar la posición horizontal, la posición vertical, el ancho y el alto de un elemento. Además, el [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) propiedad adjunta especifica cómo se interpretarán los límites del diseño.

En el siguiente código XAML se muestra cómo organizar los elementos de un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout):

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

- A cada [`BoxView`](xref:Xamarin.Forms.BoxView) se le asigna un tamaño explícito de 100x100 y se muestra en la misma posición, centrado horizontalmente.
- El [`BoxView`](xref:Xamarin.Forms.BoxView) rojo gira 30 grados y el `BoxView` verde gira 60 grados.
- En cada [`BoxView`](xref:Xamarin.Forms.BoxView), el [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) propiedad adjunta se establece en `PositionProportional`, lo que indica que la posición es proporcional al espacio restante una vez que se han contabilizado el ancho y el alto.

> [!CAUTION]
> Evite el uso de la propiedad [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) siempre que sea posible, ya que hará que el motor de diseño realice cálculos de diseño adicionales.

Para obtener más información, consulte [Xamarin. Forms AbsoluteLayout](absolute-layout.md).

## <a name="input-transparency"></a>Transparencia de entrada

Cada elemento visual tiene una propiedad [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent) que se utiliza para definir si el elemento recibe entradas. Su valor predeterminado es `false`, asegurándose de que el elemento recibe la entrada.

Cuando esta propiedad se establece en una clase de diseño, su valor se transfiere a los elementos secundarios. Por lo tanto, si se establece la propiedad [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent) en `true` en una clase de diseño, todos los elementos del diseño no recibirán una entrada.

## <a name="layout-performance"></a>Rendimiento del diseño

Para obtener el mejor rendimiento posible del diseño, siga las instrucciones que se indican en [optimizar el rendimiento del diseño](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance).

Además, el rendimiento de la representación de páginas también se puede mejorar mediante la compresión de diseño, que quita los diseños especificados del árbol visual. Para obtener más información, vea [compresión de diseño](layout-compression.md).

## <a name="related-links"></a>Vínculos relacionados

- [Diseño (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Diseños de Xamarin. Forms (vídeo)](https://youtu.be/4HlLjTZQzjM)
- [StackLayout de Xamarin. Forms](stack-layout.md)
- [Cuadrícula de Xamarin. Forms](grid.md)
- [FlexLayout de Xamarin. Forms](flex-layout.md)
- [AbsoluteLayout de Xamarin. Forms](absolute-layout.md)
- [RelativeLayout de Xamarin. Forms](relative-layout.md)
- [Optimizar el rendimiento del diseño](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)
- [Compresión de diseño](layout-compression.md)
