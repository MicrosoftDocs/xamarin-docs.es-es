---
title: Herencia de estilo en Xamarin.Forms
description: Los estilos pueden heredar de otros estilos para reducir la duplicación y habilitar la reutilización. En este artículo se explica cómo realizar la herencia de estilos en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 67A3A39C-8CC0-446D-8162-FFA73582D3B8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 24f6eac93dd3095df70bbb326388cd9ed47f395c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657054"
---
# <a name="style-inheritance-in-xamarinforms"></a>Herencia de estilo en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Los estilos pueden heredar de otros estilos para reducir la duplicación y habilitar la reutilización._

## <a name="style-inheritance-in-xaml"></a>Herencia de estilo en XAML

Herencia de estilo se realiza estableciendo la [ `Style.BasedOn` ](xref:Xamarin.Forms.Style.BasedOn) propiedad a una existente [ `Style` ](xref:Xamarin.Forms.Style). En XAML, esto se logra estableciendo el `BasedOn` propiedad a un `StaticResource` extensión de marcado que se hace referencia a una previamente creada `Style`. En C#, esto se logra estableciendo el `BasedOn` propiedad a un `Style` instancia.

Pueden incluir los estilos que se heredan de un estilo base [ `Setter` ](xref:Xamarin.Forms.Setter) instancias para las nuevas propiedades, o usarlos para invalidar los estilos al estilo de base. Además, los estilos que se heredan de un estilo base deben tener como destino el mismo tipo o un tipo que deriva el tipo de destino en el estilo de base. Por ejemplo, si el destino de una base de estilo [ `View` ](xref:Xamarin.Forms.View) instancias, los estilos que se basan en el estilo base pueden tener como destino `View` instancias o tipos que derivan de la `View` clase, como [ `Label` ](xref:Xamarin.Forms.Label) y [ `Button` ](xref:Xamarin.Forms.Button) instancias.

El siguiente código muestra *explícita* herencia de estilo en una página XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
                <Setter Property="HorizontalOptions"
                        Value="Center" />
                <Setter Property="VerticalOptions"
                        Value="CenterAndExpand" />
            </Style>
            <Style x:Key="labelStyle" TargetType="Label"
                   BasedOn="{StaticResource baseStyle}">
                ...
                <Setter Property="TextColor" Value="Teal" />
            </Style>
            <Style x:Key="buttonStyle" TargetType="Button"
                   BasedOn="{StaticResource baseStyle}">
                <Setter Property="BorderColor" Value="Lime" />
                ...
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   Style="{StaticResource labelStyle}" />
            ...
            <Button Text="So is the button"
                    Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

El `baseStyle` destinos [ `View` ](xref:Xamarin.Forms.View) instancias y establece el [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) y [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propiedades. El `baseStyle` no se establece directamente en los controles. En su lugar, `labelStyle` y `buttonStyle` heredan de él, establecer valores de propiedad enlazable adicionales. El `labelStyle` y `buttonStyle` , a continuación, se aplican a la [ `Label` ](xref:Xamarin.Forms.Label) instancias y [ `Button` ](xref:Xamarin.Forms.Button) instancia estableciendo sus [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propiedades. El resultado es el aspecto que se muestra en las capturas de pantalla siguiente:

[![](inheritance-images/style-inheritance.png)](inheritance-images/style-inheritance-large.png#lightbox)

> [!NOTE]
> Un estilo implícito se puede derivar de un estilo explícito, pero no puede derivar un estilo explícito de un estilo implícito.

### <a name="respecting-the-inheritance-chain"></a>Respetar la cadena de herencia

Solo puede heredar un estilo de estilos en el mismo nivel o superior, en la jerarquía de vistas. Esto significa que:

- Un recurso de nivel de aplicación solo puede heredar de otros recursos de nivel de aplicación.
- Un recurso de nivel de página puede heredar de los recursos de nivel de aplicación y otros recursos de nivel de página.
- Un recurso de nivel de control puede heredar de los recursos de nivel de aplicación, los recursos de nivel de página y otros recursos de nivel de control.

Esta cadena de herencia se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="labelStyle" TargetType="Label" BasedOn="{StaticResource baseStyle}">
                      ...
                    </Style>
                    <Style x:Key="buttonStyle" TargetType="Button" BasedOn="{StaticResource baseStyle}">
                      ...
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

En este ejemplo, `labelStyle` y `buttonStyle` son los recursos de nivel de control mientras `baseStyle` es un recurso de nivel de página. Sin embargo, mientras `labelStyle` y `buttonStyle` heredar `baseStyle`, no es posible que `baseStyle` va a heredar `labelStyle` o `buttonStyle`, debido a sus respectivas ubicaciones en la jerarquía de vistas.

## <a name="style-inheritance-in-c35"></a>Herencia de estilo en C&#35;

La página C# equivalente, donde [ `Style` ](xref:Xamarin.Forms.Style) instancias se asignan directamente a la [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propiedades de los controles necesarios, se muestra en el ejemplo de código siguiente:

```csharp
public class StyleInheritancePageCS : ContentPage
{
    public StyleInheritancePageCS ()
    {
        var baseStyle = new Style (typeof(View)) {
            Setters = {
                new Setter {
                    Property = View.HorizontalOptionsProperty, Value = LayoutOptions.Center    },
                ...
            }
        };

        var labelStyle = new Style (typeof(Label)) {
            BasedOn = baseStyle,
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Teal    }
            }
        };

        var buttonStyle = new Style (typeof(Button)) {
            BasedOn = baseStyle,
            Setters = {
                new Setter { Property = Button.BorderColorProperty, Value =    Color.Lime },
                ...
            }
        };
        ...

        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels", Style = labelStyle },
                ...
                new Button { Text = "So is the button", Style = buttonStyle }
            }
        };
    }
}
```

El `baseStyle` destinos [ `View` ](xref:Xamarin.Forms.View) instancias y establece el [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) y [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propiedades. El `baseStyle` no se establece directamente en los controles. En su lugar, `labelStyle` y `buttonStyle` heredan de él, establecer valores de propiedad enlazable adicionales. El `labelStyle` y `buttonStyle` , a continuación, se aplican a la [ `Label` ](xref:Xamarin.Forms.Label) instancias y [ `Button` ](xref:Xamarin.Forms.Button) instancia estableciendo sus [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propiedades.

## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos básicos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Trabajar con estilos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Establecedor](xref:Xamarin.Forms.Setter)
