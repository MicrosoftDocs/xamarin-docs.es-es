---
title: Estilos globales en Xamarin.Forms
description: Los estilos pueden estar disponibles globalmente agregándolas al diccionario de recursos de la aplicación. Esto ayuda a evitar la duplicación de estilos a través de las páginas o controles.
ms.prod: xamarin
ms.assetid: BDC65F82-65E0-4C8E-BB91-8E340EB2D15A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: f8fb026cd9a8dfbfd5abf13c9b11463bf84f7e0b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647246"
---
# <a name="global-styles-in-xamarinforms"></a>Estilos globales en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Los estilos pueden estar disponibles globalmente agregándolas al diccionario de recursos de la aplicación. Esto ayuda a evitar la duplicación de estilos a través de las páginas o controles._

## <a name="create-a-global-style-in-xaml"></a>Crear un estilo global en XAML

De forma predeterminada, en todas las aplicaciones de Xamarin.Forms creadas a partir de una plantilla se usa la clase **App** para implementar la subclase [`Application`](xref:Xamarin.Forms.Application). Para declarar un [ `Style` ](xref:Xamarin.Forms.Style) en el nivel de aplicación, en la aplicación [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) mediante XAML, el valor predeterminado **aplicación** clase debe reemplazarse por un XAML **Aplicación** clase y código subyacente asociado. Para obtener más información, consulte [trabajar con la clase App](~/xamarin-forms/app-fundamentals/application-class.md).

El siguiente ejemplo de código muestra un [ `Style` ](xref:Xamarin.Forms.Style) declarado en el nivel de aplicación:

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

Esto [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) define una sola *explícita* estilo, `buttonStyle`, que se usará para establecer el aspecto de [ `Button` ](xref:Xamarin.Forms.Button) instancias. Sin embargo, pueden ser estilos globales *explícita* o *implícita*.

En el ejemplo de código siguiente se muestra una aplicación de página XAML el `buttonStyle` a la página [ `Button` ](xref:Xamarin.Forms.Button) instancias:

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

El resultado es el aspecto que se muestra en las capturas de pantalla siguiente:

[![](application-images/application-styles-1.png "Ejemplo de estilos globales")](application-images/application-styles-1-large.png#lightbox "ejemplo estilos globales")

Para obtener información sobre cómo crear estilos en una página [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), consulte [estilos explícitos](~/xamarin-forms/user-interface/styles/explicit.md) y [los estilos implícitos](~/xamarin-forms/user-interface/styles/implicit.md).

### <a name="override-styles"></a>Reemplazar estilos

Estilos de más abajo en la jerarquía de vistas tienen prioridad sobre las define mayor seguridad. Por ejemplo, si se establece un [ `Style` ](xref:Xamarin.Forms.Style) que establece [ `Button.TextColor` ](xref:Xamarin.Forms.Button.TextColor) a `Red` en la aplicación de nivel serán reemplazado por un estilo de nivel de página establece `Button.TextColor` a `Green`. De forma similar, un estilo de nivel de página se reemplazará por un estilo de nivel de control. Además, si `Button.TextColor` se establece directamente en una propiedad de control, esto tendrá prioridad sobre los estilos. Esta prioridad se muestra en el ejemplo de código siguiente:

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

La versión original `buttonStyle`, definido en el nivel de aplicación, se reemplaza por la `buttonStyle` instancia definida en el nivel de página. Además, el estilo de nivel de página se haya reemplazado por el nivel de control `buttonStyle`. Por lo tanto, el [ `Button` ](xref:Xamarin.Forms.Button) se muestran las instancias con texto azul, como se muestra en las capturas de pantalla siguiente:

[![](application-images/application-styles-2.png "Invalidación de ejemplo de estilos")](application-images/application-styles-2-large.png#lightbox "invalidar el ejemplo de estilos")

## <a name="create-a-global-style-in-c35"></a>Crear un estilo global en C&#35;

[`Style`](xref:Xamarin.Forms.Style) las instancias se pueden agregar a la aplicación [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) colección en C# mediante la creación de un nuevo [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)y, a continuación, agregando el `Style` instancias para el `ResourceDictionary`, como se muestra en el ejemplo de código siguiente:

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

El constructor define una sola *explícita* estilo para aplicar a [ `Button` ](xref:Xamarin.Forms.Button) instancias a lo largo de la aplicación. *Explícita* [ `Style` ](xref:Xamarin.Forms.Style) instancias se agregan a la [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) utilizando el [ `Add` ](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) método, especificando un `key`cadena para hacer referencia a la `Style` instancia. El `Style` instancia, a continuación, se puede aplicar a todos los controles del tipo correcto en la aplicación. Sin embargo, pueden ser estilos globales *explícita* o *implícita*.

El ejemplo de código siguiente muestra un C# página aplicando el `buttonStyle` a la página [ `Button` ](xref:Xamarin.Forms.Button) instancias:

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

El `buttonStyle` se aplica a la [ `Button` ](xref:Xamarin.Forms.Button) instancias estableciendo sus [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propiedades y controla la apariencia de la `Button` instancias.

## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos básicos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Trabajar con estilos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Establecedor](xref:Xamarin.Forms.Setter)
