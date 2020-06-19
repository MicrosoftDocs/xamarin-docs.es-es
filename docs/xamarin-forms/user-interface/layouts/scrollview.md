---
title: Xamarin.FormsScrollView
description: Xamarin.FormsScrollView es un diseño que es capaz de desplazarse por el contenido.
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/27/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f527acabe3b051cbfd6450ba6f5328449b1a728c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84199198"
---
# <a name="xamarinforms-scrollview"></a>Xamarin.FormsScrollView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-scrollviewdemos)

[![Xamarin.FormsScrollView](scrollview-images/layouts.png "[! Operador. NO-LOC (Xamarin. Forms)] ScrollView")](scrollview-images/layouts-large.png#lightbox "[! Operador. NO-LOC (Xamarin. Forms)] ScrollView")

[`ScrollView`](xref:Xamarin.Forms.ScrollView)es un diseño capaz de desplazarse por el contenido. La `ScrollView` clase se deriva de la [`Layout`](xref:Xamarin.Forms.Layout) clase y, de forma predeterminada, desplaza el contenido verticalmente. Un `ScrollView` solo puede tener un único elemento secundario, aunque puede ser otros diseños.

> [!WARNING]
> [`ScrollView`](xref:Xamarin.Forms.ScrollView)los objetos no deben estar anidados. Además, `ScrollView` los objetos no se deben anidar con otros controles que proporcionen desplazamiento, como [`CollectionView`](xref:Xamarin.Forms.CollectionView) , [`ListView`](xref:Xamarin.Forms.ListView) y [`WebView`](xref:Xamarin.Forms.WebView) .

[`ScrollView`](xref:Xamarin.Forms.ScrollView)define las siguientes propiedades:

- [`Content`](xref:Xamarin.Forms.ScrollView.Content), de tipo [`View`](xref:Xamarin.Forms.View) , representa el contenido que se va a mostrar en [`ScrollView`](xref:Xamarin.Forms.ScrollView) .
- [`ContentSize`](xref:Xamarin.Forms.ScrollView), de tipo [`Size`](xref:Xamarin.Forms.Size) , representa el tamaño del contenido. Se trata de una propiedad de solo lectura.
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView), de tipo [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibility) , representa cuando la barra de desplazamiento horizontal está visible.
- [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation), de tipo [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) , representa la dirección de desplazamiento de [`ScrollView`](xref:Xamarin.Forms.ScrollView) . El valor predeterminado de esta propiedad es `Vertical`.
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollX), de tipo `double` , indica la posición de desplazamiento X actual. El valor predeterminado de esta propiedad de solo lectura es 0.
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollY), de tipo `double` , indica la posición de desplazamiento actual. El valor predeterminado de esta propiedad de solo lectura es 0.
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView), de tipo [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibility) , representa cuando la barra de desplazamiento vertical está visible.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, con la excepción de la [`Content`](xref:Xamarin.Forms.ScrollView.Content) propiedad, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

La [`Content`](xref:Xamarin.Forms.ScrollView.Content) propiedad es [`ContentProperty`](xref:Xamarin.Forms.ContentPropertyAttribute) de la [`ScrollView`](xref:Xamarin.Forms.ScrollView) clase y, por tanto, no es necesario establecer explícitamente desde XAML.

> [!TIP]
> Para obtener el mejor rendimiento posible del diseño, siga las instrucciones que se indican en [optimizar el rendimiento del diseño](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance).

## <a name="scrollview-as-a-root-layout"></a>ScrollView como un diseño raíz

Un [`ScrollView`](xref:Xamarin.Forms.ScrollView) solo puede tener un único elemento secundario, que puede ser otros diseños. Por lo tanto, es habitual que `ScrollView` sea el diseño raíz en una página. Para desplazar el contenido secundario, [`ScrollView`](xref:Xamarin.Forms.ScrollView) calcula la diferencia entre el alto de su contenido y su propio alto. Esta diferencia es la cantidad en la que `ScrollView` puede desplazarse por el contenido.

[`StackLayout`](xref:Xamarin.Forms.StackLayout)A menudo será el elemento secundario de `ScrollView` . En este escenario, el `ScrollView` hace que `StackLayout` sea tan alto como la suma de los altos de sus elementos secundarios. A continuación, el `ScrollView` puede determinar la cantidad que se puede desplazar su contenido. Para obtener más información sobre `StackLayout` , vea [ Xamarin.Forms StackLayout](stacklayout.md).

> [!CAUTION]
> En un vertical [`ScrollView`](xref:Xamarin.Forms.ScrollView) , evite establecer la `VerticalOptions` propiedad en `Start` , `Center` o `End` . Al hacerlo `ScrollView` , se indica a que solo sea tan alto como sea necesario, que podría ser cero. Aunque Xamarin.Forms protege contra esta eventualidad, es mejor evitar el código que sugiere algo que no quiere que suceda.

El siguiente ejemplo de XAML tiene [`ScrollView`](xref:Xamarin.Forms.ScrollView) como diseño raíz en una página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ScrollViewDemos"
             x:Class="ScrollViewDemos.Views.ColorListPage"
             Title="ScrollView demo">
    <ScrollView>
        <StackLayout BindableLayout.ItemsSource="{x:Static local:NamedColor.All}">
            <BindableLayout.ItemTemplate>
                <DataTemplate>
                    <StackLayout Orientation="Horizontal">
                        <BoxView Color="{Binding Color}"
                                 HeightRequest="32"
                                 WidthRequest="32"
                                 VerticalOptions="Center" />
                        <Label Text="{Binding FriendlyName}"
                               FontSize="24"
                               VerticalOptions="Center" />
                    </StackLayout>
                </DataTemplate>
            </BindableLayout.ItemTemplate>
        </StackLayout>
    </ScrollView>
</ContentPage>
```

En este ejemplo, [`ScrollView`](xref:Xamarin.Forms.ScrollView) su contenido se establece en un [`StackLayout`](xref:Xamarin.Forms.StackLayout) que usa un diseño enlazable para mostrar los [`Color`](xref:Xamarin.Forms.Color) campos definidos por Xamarin.Forms . De forma predeterminada, un se `ScrollView` desplaza verticalmente, lo que revela más contenido:

[![Captura de pantalla de un diseño de ScrollView raíz](scrollview-images/root-layout.png "Diseño de ScrollView raíz")](scrollview-images/root-layout-large.png#lightbox "Diseño de ScrollView raíz")

El código de C# equivalente es el siguiente:

```csharp
public class ColorListPageCode : ContentPage
{
    public ColorListPageCode()
    {
        DataTemplate dataTemplate = new DataTemplate(() =>
        {
            BoxView boxView = new BoxView
            {
                HeightRequest = 32,
                WidthRequest = 32,
                VerticalOptions = LayoutOptions.Center
            };
            boxView.SetBinding(BoxView.ColorProperty, "Color");

            Label label = new Label
            {
                FontSize = 24,
                VerticalOptions = LayoutOptions.Center
            };
            label.SetBinding(Label.TextProperty, "FriendlyName");

            StackLayout horizontalStackLayout = new StackLayout
            {
                Orientation = StackOrientation.Horizontal,
                Children = { boxView, label }
            };
            return horizontalStackLayout;
        });

        StackLayout stackLayout = new StackLayout();
        BindableLayout.SetItemsSource(stackLayout, NamedColor.All);
        BindableLayout.SetItemTemplate(stackLayout, dataTemplate);

        ScrollView scrollView = new ScrollView { Content = stackLayout };

        Title = "ScrollView demo";
        Content = scrollView;
    }
}
```

Para obtener más información sobre los diseños enlazables, vea [diseños enlazables en Xamarin.Forms ](bindable-layouts.md).

## <a name="scrollview-as-a-child-layout"></a>ScrollView como diseño secundario

[`ScrollView`](xref:Xamarin.Forms.ScrollView)Puede ser un diseño secundario para un diseño primario diferente.

[`ScrollView`](xref:Xamarin.Forms.ScrollView)A menudo será el elemento secundario de [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Un `ScrollView` requiere un alto específico para calcular la diferencia entre el alto de su contenido y su propio alto, con la diferencia de que `ScrollView` puede desplazarse por el contenido. Cuando `ScrollView` es el elemento secundario de `StackLayout` , no recibe un alto específico. `StackLayout`Quiere que `ScrollView` sea lo más corto posible, que es el alto del `ScrollView` contenido o cero. Para controlar este escenario, la `VerticalOptions` propiedad de `ScrollView` debe establecerse en `FillAndExpand` . Esto hará que el `StackLayout` elemento proporcione el `ScrollView` espacio adicional no requerido por los demás elementos secundarios y `ScrollView` , a continuación, tendrá un alto específico.

El siguiente ejemplo de XAML tiene [`ScrollView`](xref:Xamarin.Forms.ScrollView) como diseño secundario a [`StackLayout`](xref:Xamarin.Forms.StackLayout) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ScrollViewDemos.Views.BlackCatPage"
             Title="ScrollView as a child layout demo">
    <StackLayout Margin="20">
        <Label Text="THE BLACK CAT by Edgar Allan Poe"
               FontSize="Medium"
               FontAttributes="Bold"
               HorizontalOptions="Center" />
        <ScrollView VerticalOptions="FillAndExpand">
            <StackLayout>
                <Label Text="FOR the most wild, yet most homely narrative which I am about to pen, I neither expect nor solicit belief. Mad indeed would I be to expect it, in a case where my very senses reject their own evidence. Yet, mad am I not -- and very surely do I not dream. But to-morrow I die, and to-day I would unburthen my soul. My immediate purpose is to place before the world, plainly, succinctly, and without comment, a series of mere household events. In their consequences, these events have terrified -- have tortured -- have destroyed me. Yet I will not attempt to expound them. To me, they have presented little but Horror -- to many they will seem less terrible than barroques. Hereafter, perhaps, some intellect may be found which will reduce my phantasm to the common-place -- some intellect more calm, more logical, and far less excitable than my own, which will perceive, in the circumstances I detail with awe, nothing more than an ordinary succession of very natural causes and effects." />
                <!-- More Label objects go here -->
            </StackLayout>
        </ScrollView>
    </StackLayout>
</ContentPage>
```

En este ejemplo, hay dos [`StackLayout`](xref:Xamarin.Forms.StackLayout) objetos. El primero `StackLayout` es el objeto de diseño raíz, que tiene un [`Label`](xref:Xamarin.Forms.Label) objeto y un [`ScrollView`](xref:Xamarin.Forms.ScrollView) como sus elementos secundarios. `ScrollView`Tiene `StackLayout` como contenido, con el `StackLayout` que contiene varios `Label` objetos. Esta disposición garantiza que la primera `Label` esté siempre en pantalla, mientras que el texto mostrado por los demás `Label` objetos se puede desplazar:

[![Captura de pantalla de un diseño de ScrollView secundario](scrollview-images/child-layout.png "Diseño de ScrollView secundario")](scrollview-images/child-layout-large.png#lightbox "Diseño de ScrollView secundario")

El código de C# equivalente es el siguiente:

```csharp
public class BlackCatPageCS : ContentPage
{
    public BlackCatPageCS()
    {
        Label titleLabel = new Label
        {
            Text = "THE BLACK CAT by Edgar Allan Poe",
            // More properties set here to define the Label appearance
        };

        ScrollView scrollView = new ScrollView
        {
            VerticalOptions = LayoutOptions.FillAndExpand,
            Content = new StackLayout
            {
                Children =
                {
                    new Label
                    {
                        Text = "FOR the most wild, yet most homely narrative which I am about to pen, I neither expect nor solicit belief. Mad indeed would I be to expect it, in a case where my very senses reject their own evidence. Yet, mad am I not -- and very surely do I not dream. But to-morrow I die, and to-day I would unburthen my soul. My immediate purpose is to place before the world, plainly, succinctly, and without comment, a series of mere household events. In their consequences, these events have terrified -- have tortured -- have destroyed me. Yet I will not attempt to expound them. To me, they have presented little but Horror -- to many they will seem less terrible than barroques. Hereafter, perhaps, some intellect may be found which will reduce my phantasm to the common-place -- some intellect more calm, more logical, and far less excitable than my own, which will perceive, in the circumstances I detail with awe, nothing more than an ordinary succession of very natural causes and effects.",
                    },
                    // More Label objects go here
                }
            }
        };

        Title = "ScrollView as a child layout demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = { titleLabel, scrollView }
        };
    }
}
```

## <a name="orientation"></a>Orientación

[`ScrollView`](xref:Xamarin.Forms.ScrollView)tiene una [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) propiedad, que representa la dirección de desplazamiento de `ScrollView` . Esta propiedad es de tipo [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) , que define los siguientes miembros:

- `Vertical`indica que `ScrollView` se desplazará verticalmente. Este miembro es el valor predeterminado de la [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) propiedad.
- `Horizontal`indica que `ScrollView` se desplazará horizontalmente.
- `Both`indica que `ScrollView` se desplazará horizontal y verticalmente.
- `Neither`indica que `ScrollView` no se desplazará.

> [!TIP]
> El desplazamiento se puede deshabilitar estableciendo la [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) propiedad en `Neither` .

## <a name="detect-scrolling"></a>Detectar desplazamiento

[`ScrollView`](xref:Xamarin.Forms.ScrollView)define un [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) evento que se desencadena para indicar que se ha producido el desplazamiento. El [`ScrolledEventArgs`](xref:Xamarin.Forms.ScrolledEventArgs) objeto que acompaña al `Scrolled` evento tiene `ScrollX` propiedades y `ScrollY` , ambos de tipo `double` .

> [!IMPORTANT]
> Las `ScrolledEventArgs.ScrollX` `ScrolledEventArgs.ScrollY` propiedades y pueden tener valores negativos, debido al efecto de rebote que se produce al desplazarse de nuevo al inicio de [`ScrollView`](xref:Xamarin.Forms.ScrollView) .

En el ejemplo de XAML siguiente se muestra un [`ScrollView`](xref:Xamarin.Forms.ScrollView) que establece un controlador de eventos para el [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) evento:

```xaml
<ScrollView Scrolled="OnScrollViewScrolled">
        ...
</ScrollView>
```

El código de C# equivalente es el siguiente:

```csharp
ScrollView scrollView = new ScrollView();
scrollView.Scrolled += OnScrollViewScrolled;
```

En este ejemplo, el `OnScrollViewScrolled` controlador de eventos se ejecuta cuando se [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) activa el evento:

```csharp
void OnScrollViewScrolled(object sender, ScrolledEventArgs e)
{
    Console.WriteLine($"ScrollX: {e.ScrollX}, ScrollY: {e.ScrollY}");
}
```

En este ejemplo, el `OnScrollViewScrolled` controlador de eventos genera los valores del [`ScrolledEventArgs`](xref:Xamarin.Forms.ScrolledEventArgs) objeto que acompaña al evento.

> [!NOTE]
> El [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) evento se desencadena para los desplazamientos iniciados por el usuario y para los desplazamientos mediante programación.

## <a name="scroll-programmatically"></a>Desplazarse mediante programación

[`ScrollView`](xref:Xamarin.Forms.ScrollView)define dos [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) métodos que desplazan de forma asincrónica `ScrollView` . Una de las sobrecargas se desplaza hasta una posición especificada en `ScrollView` , mientras que la otra desplaza un elemento especificado en la vista. Ambas sobrecargas tienen un argumento adicional que se puede usar para indicar si se debe animar el desplazamiento.

> [!IMPORTANT]
> Los [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) métodos no provocarán un desplazamiento cuando la [`ScrollView.Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) propiedad esté establecida en `Neither` .

### <a name="scroll-a-position-into-view"></a>Desplazar una posición en la vista

[`ScrollView`](xref:Xamarin.Forms.ScrollView)Se puede desplazar una posición dentro de con el [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) método que acepta los `double` `x` `y` argumentos y. Dado un `ScrollView` objeto vertical denominado `scrollView` , en el ejemplo siguiente se muestra cómo desplazarse hasta 150 unidades independientes del dispositivo desde la parte superior del `ScrollView` :

```csharp
await scrollView.ScrollToAsync(0, 150, true);
```

El tercer argumento de [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) es el `animated` argumento, que determina si se muestra una animación de desplazamiento cuando se desplaza mediante programación un [`ScrollView`](xref:Xamarin.Forms.ScrollView) .

### <a name="scroll-an-element-into-view"></a>Desplazar un elemento a la vista

Un elemento dentro de un [`ScrollView`](xref:Xamarin.Forms.ScrollView) se puede desplazar en la vista con el [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) método que acepta los [`Element`](xref:Xamarin.Forms.Element) [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) argumentos y. Dado un `ScrollView` elemento con nombre vertical `scrollView` y un [`Label`](xref:Xamarin.Forms.Label) denominado, en `label` el ejemplo siguiente se muestra cómo desplazar un elemento a la vista:

```csharp
await scrollView.ScrollToAsync(label, ScrollToPosition.End, true);
```

El tercer argumento de [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) es el `animated` argumento, que determina si se muestra una animación de desplazamiento cuando se desplaza mediante programación un [`ScrollView`](xref:Xamarin.Forms.ScrollView) .

Al desplazar un elemento en la vista, la posición exacta del elemento después del desplazamiento se puede establecer con el segundo argumento, `position` , del [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) método. Este argumento acepta un [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) miembro de enumeración:

- `MakeVisible`indica que el elemento debe desplazarse hasta que esté visible en `ScrollView` .
- `Start`indica que el elemento se debe desplazar al principio de `ScrollView` .
- `Center`indica que el elemento se debe desplazar al centro de `ScrollView` .
- `End`indica que el elemento se debe desplazar hasta el final de `ScrollView` .

## <a name="scroll-bar-visibility"></a>Visibilidad de la barra de desplazamiento

[`ScrollView`](xref:Xamarin.Forms.ScrollView)define [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView) [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView) las propiedades y, que están respaldadas por propiedades enlazables. Estas propiedades obtienen o establecen un [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibility) valor de enumeración que representa si está visible la barra de desplazamiento horizontal o vertical. La enumeración `ScrollBarVisibility` define los miembros siguientes:

- `Default`indica el comportamiento predeterminado de la barra de desplazamiento para la plataforma y es el valor predeterminado de las `HorizontalScrollBarVisibility` `VerticalScrollBarVisibility` propiedades y.
- `Always`indica que las barras de desplazamiento serán visibles, incluso cuando el contenido quepa en la vista.
- `Never`indica que las barras de desplazamiento no estarán visibles, incluso si el contenido no cabe en la vista.

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de ScrollView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-scrollviewdemos)
- [Xamarin.FormsStackLayout](stacklayout.md)
- [Diseños enlazables enXamarin.Forms](bindable-layouts.md)
