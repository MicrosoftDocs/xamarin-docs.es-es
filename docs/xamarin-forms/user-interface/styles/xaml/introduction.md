---
title: "Introducción a Xamarin.Forms los estilos" Descripción: "los estilos permiten personalizar la apariencia de los elementos visuales. Los estilos se definen para un tipo específico y contienen valores para las propiedades disponibles en ese tipo. "
MS. Prod: Xamarin ms. AssetID: 3FF899C0-6CFB-4C1D-837D-9E9E10181967 ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 04/27/2016 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="introduction-to-xamarinforms-styles"></a>Introducción a los Xamarin.Forms estilos

_Los estilos permiten personalizar el aspecto de los elementos visuales. Los estilos se definen para un tipo específico y contienen valores para las propiedades disponibles en ese tipo._

Xamarin.Formslas aplicaciones suelen contener varios controles que tienen un aspecto idéntico. Por ejemplo, una aplicación puede tener varias [`Label`](xref:Xamarin.Forms.Label) instancias que tienen las mismas opciones de fuente y opciones de diseño, tal y como se muestra en el siguiente ejemplo de código XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="Styles.NoStylesPage"
    Title="No Styles"
    IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
            <Label Text="are not"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
            <Label Text="using styles"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

En el ejemplo de código siguiente se muestra la página equivalente creada en C#:

```csharp
public class NoStylesPageCS : ContentPage
{
    public NoStylesPageCS ()
    {
        Title = "No Styles";
        IconImageSource = "csharp.png";
        Padding = new Thickness (0, 20, 0, 0);

        Content = new StackLayout {
            Children = {
                new Label {
                    Text = "These labels",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                },
                new Label {
                    Text = "are not",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                },
                new Label {
                    Text = "using styles",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                }
            }
        };
    }
}
```

Cada [`Label`](xref:Xamarin.Forms.Label) instancia tiene valores de propiedad idénticos para controlar la apariencia del texto que muestra `Label` . El resultado es el aspecto que se muestra en las capturas de pantalla siguientes:

[![Apariencia de etiqueta sin estilos](introduction-images/no-styles.png)](introduction-images/no-styles-large.png#lightbox)

Establecer la apariencia de cada control individual puede ser repetitivo y propenso a errores. En su lugar, se puede crear un estilo que defina la apariencia y se aplique a los controles necesarios.

## <a name="create-a-style"></a>Crear un estilo

La clase [`Style`](xref:Xamarin.Forms.Style) agrupa una colección de valores de propiedad en un objeto que después se puede aplicar a varias instancias de elementos visuales. Esto ayuda a reducir el marcado repetitivo y permite cambiar la apariencia de las aplicaciones más fácilmente.

Aunque los estilos se diseñaron principalmente para las aplicaciones basadas en XAML, también se pueden crear en C#:

- [`Style`](xref:Xamarin.Forms.Style)las instancias creadas en XAML se definen normalmente en un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) que se asigna a la [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) colección de un control, una página o la [`Resources`](xref:Xamarin.Forms.Application.Resources) colección de la aplicación.
- [`Style`](xref:Xamarin.Forms.Style)las instancias creadas en C# normalmente se definen en la clase de la página o en una clase a la que se puede tener acceso de forma global.

La elección de dónde se puede definir una instancia de [`Style`](xref:Xamarin.Forms.Style) afecta a dónde se puede usar:

- [`Style`](xref:Xamarin.Forms.Style)las instancias definidas en el nivel de control solo se pueden aplicar al control y a sus elementos secundarios.
- [`Style`](xref:Xamarin.Forms.Style)las instancias definidas en el nivel de página solo se pueden aplicar a la página y a sus elementos secundarios.
- Las instancias de [`Style`](xref:Xamarin.Forms.Style) definidas en el nivel de aplicación se pueden aplicar en toda la aplicación.

Cada instancia de [`Style`](xref:Xamarin.Forms.Style) contiene una colección de uno o varios objetos [`Setter`](xref:Xamarin.Forms.Setter), donde cada objeto `Setter` tiene un elemento [`Property`](xref:Xamarin.Forms.Setter.Property) y un elemento [`Value`](xref:Xamarin.Forms.Setter.Value). `Property` es el nombre de la propiedad enlazable del elemento al que se aplica el estilo y `Value` es el valor que se aplica a la propiedad.

Cada [`Style`](xref:Xamarin.Forms.Style) instancia puede ser *explícita*o *implícita*:

- Una instancia *explícita* [`Style`](xref:Xamarin.Forms.Style) se define especificando un [`TargetType`](xref:Xamarin.Forms.Style.TargetType) valor de y `x:Key` , y estableciendo la propiedad del elemento de destino [`Style`](xref:Xamarin.Forms.NavigableElement.Style) en la `x:Key` referencia. Para obtener más información sobre los estilos *explícitos* , vea [estilos explícitos](~/xamarin-forms/user-interface/styles/explicit.md).
- Una instancia *implícita* [`Style`](xref:Xamarin.Forms.Style) se define especificando solo un [`TargetType`](xref:Xamarin.Forms.Style.TargetType) . La `Style` instancia se aplicará automáticamente a todos los elementos de ese tipo. Tenga en cuenta que las subclases de no `TargetType` tienen el `Style` aplicado automáticamente. Para obtener más información sobre los estilos *implícitos* , vea [estilos implícitos](~/xamarin-forms/user-interface/styles/implicit.md).

Al crear un elemento [`Style`](xref:Xamarin.Forms.Style), siempre se requiere la propiedad [`TargetType`](xref:Xamarin.Forms.Style.TargetType). En el ejemplo de código siguiente se muestra un estilo *explícito* (observe el `x:Key` ) creado en XAML:

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

Para aplicar `Style` , el objeto de destino debe ser un [`VisualElement`](xref:Xamarin.Forms.VisualElement) que coincida con el [`TargetType`](xref:Xamarin.Forms.Style.TargetType) valor de propiedad de `Style` , tal y como se muestra en el siguiente ejemplo de código XAML:

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

Los estilos inferiores de la jerarquía de vistas tienen prioridad sobre las definidas más arriba. Por ejemplo, el establecimiento de un [`Style`](xref:Xamarin.Forms.Style) que establezca en [`Label.TextColor`](xref:Xamarin.Forms.Label.TextColor) `Red` en el nivel de aplicación será reemplazado por un estilo de nivel de página que establezca `Label.TextColor` en `Green` . De igual forma, un estilo de nivel de página se reemplazará por un estilo de nivel de control. Además, si `Label.TextColor` se establece directamente en una propiedad de control, tiene prioridad sobre cualquier estilo.

En los artículos de esta sección se muestra y se explica cómo crear y aplicar estilos *explícitos* e *implícitos* , cómo crear estilos globales, la herencia de estilo, cómo responder a los cambios de estilo en tiempo de ejecución y cómo usar los estilos integrados incluidos en Xamarin.Forms .

> [!NOTE]
> **¿Qué es StyleId?**
>
> Antes de Xamarin.Forms 2,2, la [`StyleId`](xref:Xamarin.Forms.Element.StyleId) propiedad se usaba para identificar elementos individuales en una aplicación para su identificación en las pruebas de IU y en motores de tema como Pixate. Sin embargo, Xamarin.Forms 2,2 presentó la [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) propiedad, que ha reemplazado la [`StyleId`](xref:Xamarin.Forms.Element.StyleId) propiedad.

## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilo](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
