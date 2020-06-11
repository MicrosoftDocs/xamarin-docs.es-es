---
title: "estilos explícitos en Xamarin.Forms " Description: "un estilo explícito es uno que se aplica de forma selectiva a los controles estableciendo sus propiedades de estilo. En este artículo se explica cómo consumir estilos explícitos en una Xamarin.Forms aplicación ".
MS. Prod: Xamarin ms. AssetID: C0DF9F8F-B431-4374-A574-325BC3C41A3B ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 02/17/2016 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="explicit-styles-in-xamarinforms"></a>Estilos explícitos enXamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Un estilo explícito es aquel que se aplica de forma selectiva a los controles estableciendo sus propiedades de estilo._

## <a name="create-an-explicit-style-in-xaml"></a>Crear un estilo explícito en XAML

Para declarar un [`Style`](xref:Xamarin.Forms.Style) en el nivel de página, [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) se debe agregar a la página y, a continuación, `Style` se pueden incluir una o más declaraciones en `ResourceDictionary` . Un `Style` se hace *explícito* proporcionando a su declaración un `x:Key` atributo, que le da una clave descriptiva en `ResourceDictionary` . Los estilos *explícitos* se deben aplicar a los elementos visuales concretos estableciendo sus [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propiedades.

En el ejemplo de código siguiente se muestran los estilos *explícitos* declarados en XAML en una página `ResourceDictionary` y se aplican a las instancias de la página [`Label`](xref:Xamarin.Forms.Label) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="labelRedStyle" TargetType="Label">
                <Setter Property="HorizontalOptions"
                        Value="Center" />
                <Setter Property="VerticalOptions"
                        Value="CenterAndExpand" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
            <Style x:Key="labelGreenStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Green" />
            </Style>
            <Style x:Key="labelBlueStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   Style="{StaticResource labelRedStyle}" />
            <Label Text="are demonstrating"
                   Style="{StaticResource labelGreenStyle}" />
            <Label Text="explicit styles,"
                   Style="{StaticResource labelBlueStyle}" />
            <Label Text="and an explicit style override"
                   Style="{StaticResource labelBlueStyle}"
                   TextColor="Teal" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)Define tres estilos *explícitos* que se aplican a las instancias de la página [`Label`](xref:Xamarin.Forms.Label) . Cada `Style` se usa para mostrar texto en un color diferente, a la vez que se establece el tamaño de fuente y las opciones de diseño horizontal y vertical. Cada `Style` se aplica a un distinto estableciendo `Label` sus [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propiedades mediante la `StaticResource` extensión de marcado. El resultado es el aspecto que se muestra en las capturas de pantalla siguientes:

[![Ejemplo de estilos explícitos](explicit-images/explicit-styles.png)](explicit-images/explicit-styles-large.png#lightbox)

Además, al final [`Label`](xref:Xamarin.Forms.Label) se le ha [`Style`](xref:Xamarin.Forms.Style) aplicado, pero también invalida la [`TextColor`](xref:Xamarin.Forms.Label.TextColor) propiedad a un `Color` valor diferente.

### <a name="create-an-explicit-style-at-the-control-level"></a>Crear un estilo explícito en el nivel de control

Además de crear estilos *explícitos* en el nivel de página, también se pueden crear en el nivel de control, como se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="labelRedStyle" TargetType="Label">
                      ...
                    </Style>
                    ...
                </ResourceDictionary>
            </StackLayout.Resources>
            <Label Text="These labels" Style="{StaticResource labelRedStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

En este ejemplo, las instancias *explícitas* [`Style`](xref:Xamarin.Forms.Style) se asignan a la [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) colección del [`StackLayout`](xref:Xamarin.Forms.StackLayout) control. Después, los estilos se pueden aplicar al control y a sus elementos secundarios.

Para obtener información sobre cómo crear estilos en la de una aplicación [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , vea [estilos globales](~/xamarin-forms/user-interface/styles/application.md).

## <a name="create-an-explicit-style-in-c35"></a>Crear un estilo explícito en C&#35;

[`Style`](xref:Xamarin.Forms.Style)las instancias se pueden agregar a la colección de una página [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) en C# mediante la creación de una nueva [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) y, a continuación, agregando las `Style` instancias a `ResourceDictionary` , tal y como se muestra en el ejemplo de código siguiente:

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Red    }
            }
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Green }
            }
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Blue }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("labelRedStyle", labelRedStyle);
        Resources.Add ("labelGreenStyle", labelGreenStyle);
        Resources.Add ("labelBlueStyle", labelBlueStyle);
        ...

        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels",
                            Style = (Style)Resources ["labelRedStyle"] },
                new Label { Text = "are demonstrating",
                            Style = (Style)Resources ["labelGreenStyle"] },
                new Label { Text = "explicit styles,",
                            Style = (Style)Resources ["labelBlueStyle"] },
                new Label {    Text = "and an explicit style override",
                            Style = (Style)Resources ["labelBlueStyle"], TextColor = Color.Teal }
            }
        };
    }
}
```

El constructor define tres estilos *explícitos* que se aplican a las instancias de la página [`Label`](xref:Xamarin.Forms.Label) . Cada *explícito* [`Style`](xref:Xamarin.Forms.Style) se agrega a [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) mediante el [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) método, especificando una `key` cadena para hacer referencia a la `Style` instancia. Cada `Style` se aplica a un distinto `Label` estableciendo sus [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propiedades.

Sin embargo, no hay ninguna ventaja en utilizar [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) aquí. En su lugar, las [`Style`](xref:Xamarin.Forms.Style) instancias se pueden asignar directamente a las [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propiedades de los elementos visuales necesarios y `ResourceDictionary` se puede quitar, tal y como se muestra en el ejemplo de código siguiente:

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            ...
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            ...
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            ...
        };
        ...
        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels", Style = labelRedStyle },
                new Label { Text = "are demonstrating", Style = labelGreenStyle },
                new Label { Text = "explicit styles,", Style = labelBlueStyle },
                new Label { Text = "and an explicit style override", Style = labelBlueStyle,
                            TextColor = Color.Teal }
            }
        };
    }
}
```

El constructor define tres estilos *explícitos* que se aplican a las instancias de la página [`Label`](xref:Xamarin.Forms.Label) . Cada `Style` se usa para mostrar texto en un color diferente, a la vez que se establece el tamaño de fuente y las opciones de diseño horizontal y vertical. Cada `Style` se aplica a un distinto `Label` estableciendo sus [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propiedades. Además, al final `Label` se le ha `Style` aplicado, pero también invalida la `TextColor` propiedad a un `Color` valor diferente.

## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos básicos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Trabajar con estilos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
