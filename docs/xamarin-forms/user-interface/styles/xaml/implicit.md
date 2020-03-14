---
title: Estilos implícitos en Xamarin.Forms
description: Un estilo implícito es aquella que se usa por todos los controles de la mismo TargetType, sin necesidad de cada control para hacer referencia al estilo.
ms.prod: xamarin
ms.assetid: 02A75F3B-4389-49D4-A2F4-AFD473A4A161
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
ms.openlocfilehash: d58ba81596cccf470b7246514d71f35968599880
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79306390"
---
# <a name="implicit-styles-in-xamarinforms"></a>Estilos implícitos en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Un estilo implícito es aquél que usan todos los controles del mismo TargetType, sin necesidad de que cada control haga referencia al estilo._

## <a name="create-an-implicit-style-in-xaml"></a>Crear un estilo implícito en XAML

Para declarar un [`Style`](xref:Xamarin.Forms.Style) en el nivel de página, se debe agregar una [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) a la página y, a continuación, se pueden incluir una o varias declaraciones de `Style` en el `ResourceDictionary`. Un `Style` se hace *implícito* si no se especifica un atributo de `x:Key`. A continuación, se aplicará el estilo a los elementos visuales que coincidan exactamente con el `TargetType`, pero no a los elementos que se derivan del valor de `TargetType`.

En el ejemplo de código siguiente se muestra un estilo *implícito* declarado en XAML en el `ResourceDictionary`de una página y que se aplica a las instancias de [`Entry`](xref:Xamarin.Forms.Entry) de la página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Entry">
                <Setter Property="HorizontalOptions" Value="Fill" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                <Setter Property="BackgroundColor" Value="Yellow" />
                <Setter Property="FontAttributes" Value="Italic" />
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Entry Text="These entries" />
            <Entry Text="are demonstrating" />
            <Entry Text="implicit styles," />
            <Entry Text="and an implicit style override" BackgroundColor="Lime" TextColor="Red" />
            <local:CustomEntry Text="Subclassed Entry is not receiving the style" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

El [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) define un estilo *implícito* único que se aplica a las instancias de [`Entry`](xref:Xamarin.Forms.Entry) de la página. El `Style` se usa para mostrar el texto azul sobre un fondo amarillo, mientras que también se establecen otras opciones de apariencia. El `Style` se agrega al [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de la página sin especificar un atributo `x:Key`. Por lo tanto, el `Style` se aplica a todas las instancias de `Entry` de forma implícita, ya que coinciden exactamente con la propiedad [`TargetType`](xref:Xamarin.Forms.Style.TargetType) del `Style`. Sin embargo, el `Style` no se aplica a la instancia de `CustomEntry`, que es un `Entry`de subclase. El resultado es el aspecto que se muestra en las capturas de pantalla siguientes:

[![ejemplo de estilos implícitos](implicit-images/implicit-styles.png)](implicit-images/implicit-styles-large.png#lightbox)

Además, la cuarta [`Entry`](xref:Xamarin.Forms.Entry) invalida las propiedades [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) y [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) del estilo implícito a diferentes valores de `Color`.

### <a name="create-an-implicit-style-at-the-control-level"></a>Crear un estilo implícito en el nivel de control

Además de crear estilos *implícitos* en el nivel de página, también se pueden crear en el nivel de control, como se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style TargetType="Entry">
                        <Setter Property="HorizontalOptions" Value="Fill" />
                        ...
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            <Entry Text="These entries" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

En este ejemplo, el *implicit* [`Style`](xref:Xamarin.Forms.Style) implícito se asigna a la colección [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) del control [`StackLayout`](xref:Xamarin.Forms.StackLayout) . A continuación, el estilo *implícito* se puede aplicar al control y a sus elementos secundarios.

Para obtener información sobre cómo crear estilos en la [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de una aplicación, vea [estilos globales](~/xamarin-forms/user-interface/styles/application.md).

## <a name="create-an-implicit-style-in-c35"></a>Crear un estilo implícito en C&#35;

[`Style`](xref:Xamarin.Forms.Style) instancias se pueden agregar a la colección de [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) de una C# página en mediante la creación de un nuevo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)y, a continuación, agregando las instancias de `Style` al `ResourceDictionary`, como se muestra en el ejemplo de código siguiente:

```csharp
public class ImplicitStylesPageCS : ContentPage
{
    public ImplicitStylesPageCS ()
    {
        var entryStyle = new Style (typeof(Entry)) {
            Setters = {
                ...
                new Setter { Property = Entry.TextColorProperty, Value = Color.Blue }
            }
        };

        ...
        Resources = new ResourceDictionary ();
        Resources.Add (entryStyle);

        Content = new StackLayout {
            Children = {
                new Entry { Text = "These entries" },
                new Entry { Text = "are demonstrating" },
                new Entry { Text = "implicit styles," },
                new Entry { Text = "and an implicit style override", BackgroundColor = Color.Lime, TextColor = Color.Red },
                new CustomEntry  { Text = "Subclassed Entry is not receiving the style" }
            }
        };
    }
}
```

El constructor define un solo estilo *implícito* que se aplica a las instancias de [`Entry`](xref:Xamarin.Forms.Entry) de la página. El `Style` se usa para mostrar el texto azul sobre un fondo amarillo, mientras que también se establecen otras opciones de apariencia. El `Style` se agrega al [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de la página sin especificar una cadena de `key`. Por lo tanto, el `Style` se aplica a todas las instancias de `Entry` de forma implícita, ya que coinciden exactamente con la propiedad [`TargetType`](xref:Xamarin.Forms.Style.TargetType) del `Style`. Sin embargo, el `Style` no se aplica a la instancia de `CustomEntry`, que es un `Entry`de subclase.

## <a name="apply-a-style-to-derived-types"></a>Aplicar un estilo a los tipos derivados

La propiedad [`Style.ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) permite aplicar un estilo a los controles que se derivan del tipo base al que hace referencia la propiedad [`TargetType`](xref:Xamarin.Forms.Style.TargetType) . Por lo tanto, establecer esta propiedad en `true` permite que un único estilo tenga como destino varios tipos, siempre que los tipos deriven del tipo base especificado en la propiedad `TargetType`.

En el ejemplo siguiente se muestra un estilo implícito que establece el color de fondo de [`Button`](xref:Xamarin.Forms.Button) instancias en rojo:

```xaml
<Style TargetType="Button"
       ApplyToDerivedTypes="True">
    <Setter Property="BackgroundColor"
            Value="Red" />
</Style>
```

Al colocar este estilo en un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) en el nivel de página, se aplicará a todas las instancias de [`Button`](xref:Xamarin.Forms.Button) en la página y también a los controles que se deriven de `Button`. Sin embargo, si la propiedad [`ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) permaneció sin establecer, el estilo solo se aplicaría a `Button` instances.

El código de C# equivalente es el siguiente:

```csharp
var buttonStyle = new Style(typeof(Button))
{
    ApplyToDerivedTypes = true,
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.Red
        }
    }
};

Resources = new ResourceDictionary { buttonStyle };
```

## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos básicos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Trabajar con estilos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Establecedor](xref:Xamarin.Forms.Setter)
