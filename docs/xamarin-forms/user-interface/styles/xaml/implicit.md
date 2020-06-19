---
title: Estilos implícitos enXamarin.Forms
description: Un estilo implícito es aquél que usan todos los controles del mismo TargetType, sin necesidad de que cada control haga referencia al estilo.
ms.prod: xamarin
ms.assetid: 02A75F3B-4389-49D4-A2F4-AFD473A4A161
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3fb6ea40ced93103ec9cc92fa707f68c674d7826
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139014"
---
# <a name="implicit-styles-in-xamarinforms"></a>Estilos implícitos enXamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Un estilo implícito es aquél que usan todos los controles del mismo TargetType, sin necesidad de que cada control haga referencia al estilo._

## <a name="create-an-implicit-style-in-xaml"></a>Crear un estilo implícito en XAML

Para declarar un [`Style`](xref:Xamarin.Forms.Style) en el nivel de página, [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) se debe agregar a la página y, a continuación, `Style` se pueden incluir una o más declaraciones en `ResourceDictionary` . `Style`Se hace *implícito* si no se especifica un `x:Key` atributo. A continuación, se aplicará el estilo a los elementos visuales que coinciden `TargetType` exactamente, pero no a los elementos que se derivan del `TargetType` valor.

En el ejemplo de código siguiente se muestra un estilo *implícito* declarado en XAML en una página `ResourceDictionary` y se aplica a las instancias de la página [`Entry`](xref:Xamarin.Forms.Entry) :

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

[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)Define un estilo *implícito* único que se aplica a las instancias de la página [`Entry`](xref:Xamarin.Forms.Entry) . `Style`Se usa para mostrar el texto azul sobre un fondo amarillo, mientras que también se establecen otras opciones de apariencia. `Style`Se agrega al de la página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) sin especificar un `x:Key` atributo. Por lo tanto, `Style` se aplica a todas las `Entry` instancias de forma implícita, ya que coinciden exactamente con la [`TargetType`](xref:Xamarin.Forms.Style.TargetType) propiedad de `Style` . Sin embargo, `Style` no se aplica a la `CustomEntry` instancia de, que es una subclase `Entry` . El resultado es el aspecto que se muestra en las capturas de pantalla siguientes:

[![Ejemplo de estilos implícitos](implicit-images/implicit-styles.png)](implicit-images/implicit-styles-large.png#lightbox)

Además, el cuarto [`Entry`](xref:Xamarin.Forms.Entry) invalida las [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) propiedades y del estilo implícito en `Color` valores diferentes.

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

En este ejemplo, el *implícito* [`Style`](xref:Xamarin.Forms.Style) se asigna a la [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) colección del [`StackLayout`](xref:Xamarin.Forms.StackLayout) control. A continuación, el estilo *implícito* se puede aplicar al control y a sus elementos secundarios.

Para obtener información sobre cómo crear estilos en la de una aplicación [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , vea [estilos globales](~/xamarin-forms/user-interface/styles/application.md).

## <a name="create-an-implicit-style-in-c35"></a>Crear un estilo implícito en C&#35;

[`Style`](xref:Xamarin.Forms.Style)las instancias se pueden agregar a la colección de una página [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) en C# mediante la creación de una nueva [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) y, a continuación, agregando las `Style` instancias a `ResourceDictionary` , tal y como se muestra en el ejemplo de código siguiente:

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

El constructor define un estilo *implícito* único que se aplica a las instancias de la página [`Entry`](xref:Xamarin.Forms.Entry) . `Style`Se usa para mostrar el texto azul sobre un fondo amarillo, mientras que también se establecen otras opciones de apariencia. `Style`Se agrega al de la página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) sin especificar una `key` cadena. Por lo tanto, `Style` se aplica a todas las `Entry` instancias de forma implícita, ya que coinciden exactamente con la [`TargetType`](xref:Xamarin.Forms.Style.TargetType) propiedad de `Style` . Sin embargo, `Style` no se aplica a la `CustomEntry` instancia de, que es una subclase `Entry` .

## <a name="apply-a-style-to-derived-types"></a>Aplicar un estilo a los tipos derivados

La [`Style.ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) propiedad permite aplicar un estilo a los controles que se derivan del tipo base al que hace referencia la [`TargetType`](xref:Xamarin.Forms.Style.TargetType) propiedad. Por lo tanto, establecer esta propiedad en `true` permite que un único estilo tenga como destino varios tipos, siempre que los tipos deriven del tipo base especificado en la `TargetType` propiedad.

En el ejemplo siguiente se muestra un estilo implícito que establece el color de fondo de [`Button`](xref:Xamarin.Forms.Button) las instancias en rojo:

```xaml
<Style TargetType="Button"
       ApplyToDerivedTypes="True">
    <Setter Property="BackgroundColor"
            Value="Red" />
</Style>
```

Al colocar este estilo en el nivel de página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , se aplicará a todas [`Button`](xref:Xamarin.Forms.Button) las instancias de la página y también a los controles que deriven de `Button` . Sin embargo, si la [`ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) propiedad permanece sin establecer, el estilo solo se aplicaría a `Button` las instancias de.

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
- [Setter](xref:Xamarin.Forms.Setter)
