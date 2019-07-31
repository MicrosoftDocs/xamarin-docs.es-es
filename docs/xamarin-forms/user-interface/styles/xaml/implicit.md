---
title: Estilos implícitos en Xamarin.Forms
description: Un estilo implícito es aquella que se usa por todos los controles de la mismo TargetType, sin necesidad de cada control para hacer referencia al estilo.
ms.prod: xamarin
ms.assetid: 02A75F3B-4389-49D4-A2F4-AFD473A4A161
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
ms.openlocfilehash: 328063fd6924902738722813cfb961e56af5385e
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644475"
---
# <a name="implicit-styles-in-xamarinforms"></a>Estilos implícitos en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Un estilo implícito es aquella que se usa por todos los controles de la mismo TargetType, sin necesidad de cada control para hacer referencia al estilo._

## <a name="create-an-implicit-style-in-xaml"></a>Crear un estilo implícito en XAML

Para declarar un [ `Style` ](xref:Xamarin.Forms.Style) en el nivel de página, un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) debe agregarse a la página y, a continuación, uno o varios `Style` declaraciones pueden incluirse en el `ResourceDictionary`. Un `Style` estará *implícita* al no especificar un `x:Key` atributo. A continuación, se aplicará el estilo a los elementos visuales que coinciden con el `TargetType` exactamente, pero no a los elementos que se derivan de la `TargetType` valor.

El siguiente ejemplo de código muestra un *implícita* estilo declarado en XAML en una página `ResourceDictionary`y se aplica a la página [ `Entry` ](xref:Xamarin.Forms.Entry) instancias:

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

El [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) define una sola *implícita* estilo que se aplica a la página [ `Entry` ](xref:Xamarin.Forms.Entry) instancias. El `Style` se usa para mostrar el texto azul sobre un fondo amarillo, y también establece otras opciones de apariencia. El `Style` se agrega a la página [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) sin especificar un `x:Key` atributo. Por lo tanto, el `Style` se aplica a todas la `Entry` implícitamente instancias que coinciden con el [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) propiedad de la `Style` exactamente. Sin embargo, el `Style` no se aplica a la `CustomEntry` instancia, que es una subclase `Entry`. El resultado es el aspecto que se muestra en las capturas de pantalla siguiente:

[![](implicit-images/implicit-styles.png "Ejemplo de los estilos implícitos")](implicit-images/implicit-styles-large.png#lightbox "ejemplo los estilos implícitos")

Además, la cuarta [ `Entry` ](xref:Xamarin.Forms.Entry) invalida la [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor) y [ `TextColor` ](xref:Xamarin.Forms.Entry.TextColor) propiedades del estilo implícito a diferentes `Color`valores.

### <a name="create-an-implicit-style-at-the-control-level"></a>Crear un estilo implícito en el nivel de control

Además de crear *implícita* estilos en el nivel de página, también pueden crearse en el nivel de control, tal como se muestra en el ejemplo de código siguiente:

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

En este ejemplo, el *implícita* [ `Style` ](xref:Xamarin.Forms.Style) se asigna a la [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) colección de la [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)control. El *implícita* , a continuación, se puede aplicar estilo al control y sus elementos secundarios.

Para obtener información sobre cómo crear estilos en una aplicación [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), consulte [estilos globales](~/xamarin-forms/user-interface/styles/application.md).

## <a name="create-an-implicit-style-in-c35"></a>Crear un estilo implícito en C&#35;

[`Style`](xref:Xamarin.Forms.Style) las instancias se pueden agregar a una página [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) colección en C# mediante la creación de un nuevo [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)y, a continuación, agregando el `Style` instancias para el `ResourceDictionary`, tal y como se muestra en el ejemplo de código siguiente:

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

El constructor define una sola *implícita* estilo que se aplica a la página [ `Entry` ](xref:Xamarin.Forms.Entry) instancias. El `Style` se usa para mostrar el texto azul sobre un fondo amarillo, y también establece otras opciones de apariencia. El `Style` se agrega a la página [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) sin especificar un `key` cadena. Por lo tanto, el `Style` se aplica a todas la `Entry` implícitamente instancias que coinciden con el [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) propiedad de la `Style` exactamente. Sin embargo, el `Style` no se aplica a la `CustomEntry` instancia, que es una subclase `Entry`.

## <a name="apply-a-style-to-derived-types"></a>Aplicar un estilo a los tipos derivados

La [`Style.ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) propiedad permite aplicar un estilo a los controles que se derivan del tipo base al que hace referencia la [`TargetType`](xref:Xamarin.Forms.Style.TargetType) propiedad. Por lo tanto, establecer esta `true` propiedad en permite que un único estilo tenga como destino varios tipos, siempre que los tipos deriven del tipo `TargetType` base especificado en la propiedad.

En el ejemplo siguiente se muestra un estilo implícito que establece el color [`Button`](xref:Xamarin.Forms.Button) de fondo de las instancias en rojo:

```xaml
<Style TargetType="Button"
       ApplyToDerivedTypes="True">
    <Setter Property="BackgroundColor"
            Value="Red" />
</Style>
```

Al colocar este estilo en el nivel [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de página, se aplicará a todas [`Button`](xref:Xamarin.Forms.Button) las instancias de la página y también a los controles que deriven de `Button`. Sin embargo, si [`ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) la propiedad permanece sin establecer, el estilo solo se aplicaría a `Button` las instancias de.

El código de C# equivalente es:

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
