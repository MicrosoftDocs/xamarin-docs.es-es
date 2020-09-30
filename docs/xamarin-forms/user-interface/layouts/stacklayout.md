---
title: Xamarin.Forms StackLayout
description: Un StackLayout organiza las vistas secundarias en una pila de una dimensión, ya sea horizontal o verticalmente.
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/11/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0c8e7c0e3edb581e13944ce48e2241f035791574
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556859"
---
# <a name="no-locxamarinforms-stacklayout"></a>Xamarin.Forms StackLayout

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stacklayoutdemos)

[![::: no-LOC (Xamarin. Forms)::: StackLayout](stacklayout-images/layouts.png "::: no-LOC (Xamarin. Forms)::: StackLayout")](stacklayout-images/layouts-large.png#lightbox "::: no-LOC (Xamarin. Forms)::: StackLayout")

Un [`StackLayout`](xref:Xamarin.Forms.StackLayout) organiza las vistas secundarias en una pila unidimensional, ya sea horizontal o verticalmente. De forma predeterminada, una `StackLayout` está orientada verticalmente. Además, `StackLayout` se puede utilizar como un diseño primario que contiene otros diseños secundarios.

La [`StackLayout`](xref:Xamarin.Forms.StackLayout) clase define las siguientes propiedades:

- [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation), de tipo [`StackOrientation`](xref:Xamarin.Forms.StackOrientation) , representa la dirección en la que se colocan las vistas secundarias. El valor predeterminado de esta propiedad es `Vertical`.
- [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing), de tipo `double` , indica la cantidad de espacio entre cada vista secundaria. El valor predeterminado de esta propiedad es de seis unidades independientes del dispositivo.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos y con estilo.

La [`StackLayout`](xref:Xamarin.Forms.StackLayout) clase se deriva de la `Layout<T>` clase, que define una `Children` propiedad de tipo `IList<T>` . La `Children` propiedad es `ContentProperty` de la `Layout<T>` clase y, por tanto, no es necesario establecer explícitamente desde XAML.

> [!TIP]
> Para obtener el mejor rendimiento posible del diseño, siga las instrucciones que se indican en [optimizar el rendimiento del diseño](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance).

## <a name="vertical-orientation"></a>Orientación vertical

En el código XAML siguiente se muestra cómo crear una orientada verticalmente [`StackLayout`](xref:Xamarin.Forms.StackLayout) que contiene diferentes vistas secundarias:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.VerticalStackLayoutPage"
             Title="Vertical StackLayout demo">
    <StackLayout Margin="20">
        <Label Text="Primary colors" />
        <BoxView Color="Red" />
        <BoxView Color="Yellow" />
        <BoxView Color="Blue" />
        <Label Text="Secondary colors" />
        <BoxView Color="Green" />
        <BoxView Color="Orange" />
        <BoxView Color="Purple" />
    </StackLayout>
</ContentPage>
```

En este ejemplo se crea un [`StackLayout`](xref:Xamarin.Forms.StackLayout) objeto vertical que contiene [`Label`](xref:Xamarin.Forms.Label) [`BoxView`](xref:Xamarin.Forms.BoxView) objetos y. De forma predeterminada, hay seis unidades de espacio independientes del dispositivo entre las vistas secundarias:

[![Captura de pantalla de un StackLayout orientado verticalmente](stacklayout-images/vertical.png "StackLayout orientada verticalmente")](stacklayout-images/vertical-large.png#lightbox "StackLayout orientada verticalmente")

El código de C# equivalente es el siguiente:

```csharp
public class VerticalStackLayoutPageCS : ContentPage
{
    public VerticalStackLayoutPageCS()
    {
        Title = "Vertical StackLayout demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children =
            {
                new Label { Text = "Primary colors" },
                new BoxView { Color = Color.Red },
                new BoxView { Color = Color.Yellow },
                new BoxView { Color = Color.Blue },
                new Label { Text = "Secondary colors" },
                new BoxView { Color = Color.Green },
                new BoxView { Color = Color.Orange },
                new BoxView { Color = Color.Purple }
            }
        };
    }
}
```

> [!NOTE]
> El valor de la [`Margin`](xref:Xamarin.Forms.View.Margin) propiedad representa la distancia entre un elemento y sus elementos adyacentes. Para obtener más información, vea [Márgenes y relleno](margin-and-padding.md).

## <a name="horizontal-orientation"></a>Orientación horizontal

En el siguiente código XAML se muestra cómo crear una orientada horizontalmente estableciendo [`StackLayout`](xref:Xamarin.Forms.StackLayout) su [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) propiedad en `Horizontal` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.HorizontalStackLayoutPage"
             Title="Horizontal StackLayout demo">
    <StackLayout Margin="20"
                 Orientation="Horizontal"
                 HorizontalOptions="Center">
        <BoxView Color="Red" />
        <BoxView Color="Yellow" />
        <BoxView Color="Blue" />
        <BoxView Color="Green" />
        <BoxView Color="Orange" />
        <BoxView Color="Purple" />
    </StackLayout>
</ContentPage>
```

En este ejemplo se crea [`StackLayout`](xref:Xamarin.Forms.StackLayout) un [`BoxView`](xref:Xamarin.Forms.BoxView) objeto que contiene objetos, con seis unidades de espacio independientes del dispositivo entre las vistas secundarias:

[![Captura de pantalla de un StackLayout orientado horizontalmente](stacklayout-images/horizontal.png "StackLayout orientada horizontalmente")](stacklayout-images/horizontal-large.png#lightbox "StackLayout orientada horizontalmente")

El código de C# equivalente es el siguiente:

```csharp
public HorizontalStackLayoutPageCS()
{
    Title = "Horizontal StackLayout demo";
    Content = new StackLayout
    {
        Margin = new Thickness(20),
        Orientation = StackOrientation.Horizontal,
        HorizontalOptions = LayoutOptions.Center,
        Children =
        {
            new BoxView { Color = Color.Red },
            new BoxView { Color = Color.Yellow },
            new BoxView { Color = Color.Blue },
            new BoxView { Color = Color.Green },
            new BoxView { Color = Color.Orange },
            new BoxView { Color = Color.Purple }
        }
    };
}
```

## <a name="space-between-child-views"></a>Espacio entre vistas secundarias

El espaciado entre las vistas secundarias de un [`StackLayout`](xref:Xamarin.Forms.StackLayout) se puede cambiar estableciendo la [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) propiedad en un `double` valor:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.StackLayoutSpacingPage"
             Title="StackLayout Spacing demo">
    <StackLayout Margin="20"
                 Spacing="0">
        <Label Text="Primary colors" />
        <BoxView Color="Red" />
        <BoxView Color="Yellow" />
        <BoxView Color="Blue" />
        <Label Text="Secondary colors" />
        <BoxView Color="Green" />
        <BoxView Color="Orange" />
        <BoxView Color="Purple" />
    </StackLayout>
</ContentPage>
```

En este ejemplo se crea un objeto vertical que [`StackLayout`](xref:Xamarin.Forms.StackLayout) contiene [`Label`](xref:Xamarin.Forms.Label) [`BoxView`](xref:Xamarin.Forms.BoxView) objetos y que no tienen espaciado entre ellos:

[![Captura de pantalla de un StackLayout sin espaciado](stacklayout-images/spacing.png "StackLayout sin espaciado")](stacklayout-images/spacing-large.png#lightbox "StackLayout sin espaciado")

> [!TIP]
> La [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) propiedad se puede establecer en valores negativos para que las vistas secundarias se superpongan.

El código de C# equivalente es el siguiente:

```csharp
public class StackLayoutSpacingPageCS : ContentPage
{
    public StackLayoutSpacingPageCS()
    {
        Title = "StackLayout Spacing demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Spacing = 0,
            Children =
            {
                new Label { Text = "Primary colors" },
                new BoxView { Color = Color.Red },
                new BoxView { Color = Color.Yellow },
                new BoxView { Color = Color.Blue },
                new Label { Text = "Secondary colors" },
                new BoxView { Color = Color.Green },
                new BoxView { Color = Color.Orange },
                new BoxView { Color = Color.Purple }
            }
        };
    }
}
```

## <a name="position-and-size-of-child-views"></a>Posición y tamaño de las vistas secundarias

El tamaño y la posición de las vistas secundarias dentro de [`StackLayout`](xref:Xamarin.Forms.StackLayout) depende de los valores de las propiedades y de las vistas secundarias [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) , así como de los valores de sus [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) propiedades y. En una [`StackLayout`](xref:Xamarin.Forms.StackLayout) vista vertical, las vistas secundarias se expanden para rellenar el ancho disponible cuando su tamaño no se establece explícitamente. Del mismo modo, en una `StackLayout` vista horizontal, las vistas secundarias se expanden para rellenar el alto disponible cuando su tamaño no se establece explícitamente.

Las [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) propiedades y de un [`StackLayout`](xref:Xamarin.Forms.StackLayout) , y sus vistas secundarias, se pueden establecer en campos del [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) struct, que encapsula dos preferencias de diseño:

- La *alineación* determina la posición y el tamaño de una vista secundaria dentro de su diseño primario.
- La *expansión* indica si la vista secundaria debe utilizar espacio adicional, si está disponible.

> [!TIP]
> No establezca las [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) propiedades y de a [`StackLayout`](xref:Xamarin.Forms.StackLayout) menos que necesite. Los valores predeterminados de `LayoutOptions.Fill` y `LayoutOptions.FillAndExpand` permiten la optimización de diseño óptima. Cambiar estas propiedades tiene un costo y consume memoria, incluso cuando se vuelven a establecer en los valores predeterminados.

### <a name="alignment"></a>Alignment

En el siguiente ejemplo de XAML se establecen las preferencias de alineación en cada vista secundaria del [`StackLayout`](xref:Xamarin.Forms.StackLayout) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.AlignmentPage"
             Title="Alignment demo">
    <StackLayout Margin="20">
        <Label Text="Start"
               BackgroundColor="Gray"
               HorizontalOptions="Start" />
        <Label Text="Center"
               BackgroundColor="Gray"
               HorizontalOptions="Center" />
        <Label Text="End"
               BackgroundColor="Gray"
               HorizontalOptions="End" />
        <Label Text="Fill"
               BackgroundColor="Gray"
               HorizontalOptions="Fill" />
    </StackLayout>
</ContentPage>
```

En este ejemplo, las preferencias de alineación se establecen en los [`Label`](xref:Xamarin.Forms.Label) objetos para controlar su posición dentro de [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Los [`Start`](xref:Xamarin.Forms.LayoutOptions.Start) [`Center`](xref:Xamarin.Forms.LayoutOptions.Center) campos,, [`End`](xref:Xamarin.Forms.LayoutOptions.End) y [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) se utilizan para definir la alineación de los [`Label`](xref:Xamarin.Forms.Label) objetos en el elemento primario `StackLayout` :

[![Captura de pantalla de un StackLayout con el conjunto de opciones de alineación](stacklayout-images/alignment.png "StackLayout con opciones de alineación")](stacklayout-images/alignment-large.png#lightbox "StackLayout con opciones de alineación")

Un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) solo respeta las preferencias de alineación de las vistas secundarias que tienen la dirección opuesta a la orientación del `StackLayout`. Por lo tanto, las vistas secundarias de [`Label`](xref:Xamarin.Forms.Label) dentro del `StackLayout` con orientación vertical establecen sus propiedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) en uno de los campos de alineación siguientes:

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start), que coloca el control [`Label`](xref:Xamarin.Forms.Label) en el lado izquierdo de [`StackLayout`](xref:Xamarin.Forms.StackLayout) .
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center), que centra [`Label`](xref:Xamarin.Forms.Label) en el [`StackLayout`](xref:Xamarin.Forms.StackLayout).
- [`End`](xref:Xamarin.Forms.LayoutOptions.End), que coloca [`Label`](xref:Xamarin.Forms.Label) en el lado derecho de [`StackLayout`](xref:Xamarin.Forms.StackLayout) .
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill), que garantiza que [`Label`](xref:Xamarin.Forms.Label) llena el ancho del [`StackLayout`](xref:Xamarin.Forms.StackLayout).

El código de C# equivalente es el siguiente:

```csharp
public class AlignmentPageCS : ContentPage
{
    public AlignmentPageCS()
    {
        Title = "Alignment demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children =
            {
                new Label { Text = "Start", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Start },
                new Label { Text = "Center", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Center },
                new Label { Text = "End", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.End },
                new Label { Text = "Fill", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Fill }
            }
        };
    }
}
```

### <a name="expansion"></a>Expansión

En el siguiente ejemplo de XAML se establecen las preferencias de expansión en cada [`Label`](xref:Xamarin.Forms.Label) de [`StackLayout`](xref:Xamarin.Forms.StackLayout) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.ExpansionPage"
             Title="Expansion demo">
    <StackLayout Margin="20">
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="Start"
               BackgroundColor="Gray"
               VerticalOptions="StartAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="Center"
               BackgroundColor="Gray"
               VerticalOptions="CenterAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="End"
               BackgroundColor="Gray"
               VerticalOptions="EndAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="Fill"
               BackgroundColor="Gray"
               VerticalOptions="FillAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
    </StackLayout>
</ContentPage>
```

En este ejemplo, se establecen las preferencias de expansión en los [`Label`](xref:Xamarin.Forms.Label) objetos para controlar su tamaño dentro de [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Los [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand) [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand) campos,, [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand) y [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) se usan para definir las preferencias de alineación y si el `Label` ocupará más espacio si está disponible en el elemento primario `StackLayout` :

[![Captura de pantalla de un StackLayout con opciones de expansión establecidas](stacklayout-images/expansion.png "StackLayout con opciones de expansión")](stacklayout-images/expansion-large.png#lightbox "StackLayout con opciones de expansión")

Un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) solo puede expandir las vistas secundarias en la dirección de su orientación. Por lo tanto, un `StackLayout` con orientación vertical puede expandir las vistas secundarias de [`Label`](xref:Xamarin.Forms.Label) que establecen sus propiedades [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) en uno de los campos de alineación. Esto significa que, para la alineación vertical, cada `Label` ocupa la misma cantidad de espacio en el `StackLayout`. Aun así, solo el último elemento `Label`, que establece su propiedad [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) en [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand), tiene un tamaño diferente.

> [!TIP]
> Al utilizar [`StackLayout`](xref:Xamarin.Forms.StackLayout) , asegúrese de que solo una vista secundaria está establecida en [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) . Esta propiedad garantiza que el elemento secundario especificado ocupa el mayor espacio que el `StackLayout` puede asignarle y es poco rentable realizar estos cálculos más de una vez.

El código de C# equivalente es el siguiente:

```csharp
public ExpansionPageCS()
{
    Title = "Expansion demo";
    Content = new StackLayout
    {
        Margin = new Thickness(20),
        Children =
        {
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "StartAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.StartAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "CenterAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.CenterAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "EndAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.EndAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "FillAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.FillAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 }
        }
    };
}
```

> [!IMPORTANT]
> Cuando se usa todo el espacio de un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout), las preferencias de expansión no tienen ningún efecto.

Para obtener más información acerca de la alineación y la expansión, vea [Opciones de diseño en Xamarin.Forms ](layout-options.md).

## <a name="nested-stacklayout-objects"></a>Objetos StackLayout anidados

[`StackLayout`](xref:Xamarin.Forms.StackLayout)Se puede utilizar como un diseño primario que contiene objetos secundarios anidados `StackLayout` u otros diseños secundarios.

En el código XAML siguiente se muestra un ejemplo de anidamiento de [`StackLayout`](xref:Xamarin.Forms.StackLayout) objetos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.CombinedStackLayoutPage"
             Title="Combined StackLayouts demo">
    <StackLayout Margin="20">
        ...
        <Frame BorderColor="Black"
               Padding="5">
            <StackLayout Orientation="Horizontal"
                         Spacing="15">
                <BoxView Color="Red" />
                <Label Text="Red"
                       FontSize="Large"
                       VerticalOptions="Center" />
            </StackLayout>
        </Frame>
        <Frame BorderColor="Black"
               Padding="5">
            <StackLayout Orientation="Horizontal"
                         Spacing="15">
                <BoxView Color="Yellow" />
                <Label Text="Yellow"
                       FontSize="Large"
                       VerticalOptions="Center" />
            </StackLayout>
        </Frame>
        <Frame BorderColor="Black"
               Padding="5">
            <StackLayout Orientation="Horizontal"
                         Spacing="15">
                <BoxView Color="Blue" />
                <Label Text="Blue"
                       FontSize="Large"
                       VerticalOptions="Center" />
            </StackLayout>
        </Frame>
        ...
    </StackLayout>
</ContentPage>
```

En este ejemplo, el elemento primario [`StackLayout`](xref:Xamarin.Forms.StackLayout) contiene objetos anidados dentro de los `StackLayout` [`Frame`](xref:Xamarin.Forms.Frame) objetos. El elemento primario `StackLayout` está orientado verticalmente, mientras que los `StackLayout` objetos secundarios se orientan horizontalmente:

[![Captura de pantalla de objetos StackLayout anidados](stacklayout-images/combined.png "StackLayouts anidado")](stacklayout-images/combined-large.png#lightbox "StackLayouts anidado")

> [!IMPORTANT]
> Cuanto más profundo sea el anidamiento de [`StackLayout`](xref:Xamarin.Forms.StackLayout) objetos y otros diseños, más los diseños anidados afectarán al rendimiento. Para obtener más información, vea [elegir el diseño correcto](~/xamarin-forms/deploy-test/performance.md#choose-the-correct-layout).

El código de C# equivalente es el siguiente:

```csharp
public class CombinedStackLayoutPageCS : ContentPage
{
    public CombinedStackLayoutPageCS()
    {
        Title = "Combined StackLayouts demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children =
            {
                new Label { Text = "Primary colors" },
                new Frame
                {
                    BorderColor = Color.Black,
                    Padding = new Thickness(5),
                    Content = new StackLayout
                    {
                        Orientation = StackOrientation.Horizontal,
                        Spacing = 15,
                        Children =
                        {
                            new BoxView { Color = Color.Red },
                            new Label { Text = "Red", FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)), VerticalOptions = LayoutOptions.Center }
                        }
                    }
                },
                new Frame
                {
                    BorderColor = Color.Black,
                    Padding = new Thickness(5),
                    Content = new StackLayout
                    {
                        Orientation = StackOrientation.Horizontal,
                        Spacing = 15,
                        Children =
                        {
                            new BoxView { Color = Color.Yellow },
                            new Label { Text = "Yellow", FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)), VerticalOptions = LayoutOptions.Center }
                        }
                    }
                },
                new Frame
                {
                    BorderColor = Color.Black,
                    Padding = new Thickness(5),
                    Content = new StackLayout
                    {
                        Orientation = StackOrientation.Horizontal,
                        Spacing = 15,
                        Children =
                        {
                            new BoxView { Color = Color.Blue },
                            new Label { Text = "Blue", FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)), VerticalOptions = LayoutOptions.Center }
                        }
                    }
                },
                // ...
            }
        };
    }
}
```

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de StackLayout (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-stacklayoutdemos)
- [Opciones de diseño en Xamarin.Forms](layout-options.md)
- [Elegir un Xamarin.Forms diseño](choose-layout.md)
- [Mejorar el rendimiento de las Xamarin.Forms aplicaciones](~/xamarin-forms/deploy-test/performance.md)