---
title: "estilos globales en Xamarin.Forms " Description: "los estilos pueden estar disponibles globalmente agregándolos al Diccionario de recursos de la aplicación. Esto ayuda a evitar la duplicación de estilos en páginas o controles ".
MS. Prod: Xamarin ms. AssetID: BDC65F82-65E0-4C8E-BB91-8E340EB2D15A ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 02/17/2016 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="global-styles-in-xamarinforms"></a>Estilos globales enXamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Los estilos se pueden poner a disposición globalmente agregándolos al Diccionario de recursos de la aplicación. Esto ayuda a evitar la duplicación de estilos en páginas o controles._

## <a name="create-a-global-style-in-xaml"></a>Crear un estilo global en XAML

De forma predeterminada, todas Xamarin.Forms las aplicaciones creadas a partir de una plantilla usan la clase **App** para implementar la [`Application`](xref:Xamarin.Forms.Application) subclase. Para declarar un [`Style`](xref:Xamarin.Forms.Style) en el nivel de aplicación, en el uso de XAML de la aplicación [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , la clase de **aplicación** predeterminada debe reemplazarse por una clase de **aplicación** XAML y el código subyacente asociado. Para obtener más información, vea [trabajar con la clase App](~/xamarin-forms/app-fundamentals/application-class.md).

En el ejemplo de código siguiente se muestra un [`Style`](xref:Xamarin.Forms.Style) declarado en el nivel de aplicación:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.App">
    <Application.Resources>
        <ResourceDictionary>
            <Style x:Key="buttonStyle" TargetType="Button">
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                <Setter Property="BorderColor" Value="Lime" />
                <Setter Property="BorderRadius" Value="5" />
                <Setter Property="BorderWidth" Value="5" />
                <Setter Property="WidthRequest" Value="200" />
                <Setter Property="TextColor" Value="Teal" />
            </Style>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

Esto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) define un solo estilo *explícito* , `buttonStyle` , que se usará para establecer la apariencia de [`Button`](xref:Xamarin.Forms.Button) las instancias. Sin embargo, los estilos globales pueden ser *explícitos* o *implícitos*.

En el ejemplo de código siguiente se muestra una página XAML `buttonStyle` que aplica a las instancias de la página [`Button`](xref:Xamarin.Forms.Button) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Button Text="These buttons" Style="{StaticResource buttonStyle}" />
            <Button Text="are demonstrating" Style="{StaticResource buttonStyle}" />
            <Button Text="application style overrides" Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

El resultado es el aspecto que se muestra en las capturas de pantalla siguientes:

[![](application-images/application-styles-1.png "Global Styles Example")](application-images/application-styles-1-large.png#lightbox "Global Styles Example")

Para obtener información sobre cómo crear estilos en la de una página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , vea [estilos explícitos](~/xamarin-forms/user-interface/styles/explicit.md) y [estilos implícitos](~/xamarin-forms/user-interface/styles/implicit.md).

### <a name="override-styles"></a>Reemplazar estilos

Los estilos inferiores de la jerarquía de vistas tienen prioridad sobre las definidas más arriba. Por ejemplo, el establecimiento de un [`Style`](xref:Xamarin.Forms.Style) que establezca en [`Button.TextColor`](xref:Xamarin.Forms.Button.TextColor) `Red` en el nivel de aplicación será reemplazado por un estilo de nivel de página que establezca `Button.TextColor` en `Green` . De igual forma, un estilo de nivel de página se reemplazará por un estilo de nivel de control. Además, si `Button.TextColor` se establece directamente en una propiedad de control, tendrá prioridad sobre los estilos. Esta prioridad se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="buttonStyle" TargetType="Button">
                ...
                <Setter Property="TextColor" Value="Red" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="buttonStyle" TargetType="Button">
                        ...
                        <Setter Property="TextColor" Value="Blue" />
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            <Button Text="These buttons" Style="{StaticResource buttonStyle}" />
            <Button Text="are demonstrating" Style="{StaticResource buttonStyle}" />
            <Button Text="application style overrides" Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

El original `buttonStyle` , definido en el nivel de la aplicación, se reemplaza por la `buttonStyle` instancia definida en el nivel de página. Además, el estilo de nivel de página se reemplaza por el nivel de control `buttonStyle` . Por lo tanto, las [`Button`](xref:Xamarin.Forms.Button) instancias se muestran con texto azul, tal como se muestra en las siguientes capturas de pantalla:

[![](application-images/application-styles-2.png "Overriding Styles Example")](application-images/application-styles-2-large.png#lightbox "Overriding Styles Example")

## <a name="create-a-global-style-in-c35"></a>Crear un estilo global en C&#35;

[`Style`](xref:Xamarin.Forms.Style)las instancias se pueden agregar a la colección de la aplicación [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) en C# mediante la creación de una nueva [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) y, a continuación, agregando las `Style` instancias a `ResourceDictionary` , tal y como se muestra en el ejemplo de código siguiente:

```csharp
public class App : Application
{
    public App ()
    {
        var buttonStyle = new Style (typeof(Button)) {
            Setters = {
                ...
                new Setter { Property = Button.TextColorProperty,    Value = Color.Teal }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("buttonStyle", buttonStyle);
        ...
    }
    ...
}
```

El constructor define un solo estilo *explícito* para aplicar a [`Button`](xref:Xamarin.Forms.Button) las instancias en toda la aplicación. *Explícito* [`Style`](xref:Xamarin.Forms.Style) las instancias se agregan a [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) mediante el [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) método, especificando una `key` cadena para hacer referencia a la `Style` instancia. `Style`A continuación, la instancia se puede aplicar a cualquier control del tipo correcto en la aplicación. Sin embargo, los estilos globales pueden ser *explícitos* o *implícitos*.

En el ejemplo de código siguiente se muestra una página de C# `buttonStyle` que aplica a las instancias de la página [`Button`](xref:Xamarin.Forms.Button) :

```csharp
public class ApplicationStylesPageCS : ContentPage
{
    public ApplicationStylesPageCS ()
    {
        ...
        Content = new StackLayout {
            Children = {
                new Button { Text = "These buttons", Style = (Style)Application.Current.Resources ["buttonStyle"] },
                new Button { Text = "are demonstrating", Style = (Style)Application.Current.Resources ["buttonStyle"] },
                new Button { Text = "application styles", Style = (Style)Application.Current.Resources ["buttonStyle"]
                }
            }
        };
    }
}
```

`buttonStyle`Se aplica a las [`Button`](xref:Xamarin.Forms.Button) instancias mediante el establecimiento [`Style`](xref:Xamarin.Forms.NavigableElement.Style) de sus propiedades y controla la apariencia de las `Button` instancias.

## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos básicos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Trabajar con estilos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
