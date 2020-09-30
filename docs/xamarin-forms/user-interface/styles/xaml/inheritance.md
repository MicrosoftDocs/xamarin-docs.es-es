---
title: Herencia de estilo en Xamarin.Forms
description: Los estilos pueden heredar de otros estilos para reducir la duplicación y habilitar la reutilización. En este artículo se explica cómo realizar la herencia de estilo en una Xamarin.Forms aplicación.
ms.prod: xamarin
ms.assetid: 67A3A39C-8CC0-446D-8162-FFA73582D3B8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a8aea2617d31289266e792afba773973eddb2d93
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562241"
---
# <a name="style-inheritance-in-no-locxamarinforms"></a>Herencia de estilo en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Los estilos pueden heredar de otros estilos para reducir la duplicación y habilitar la reutilización._

## <a name="style-inheritance-in-xaml"></a>Herencia de estilo en XAML

La herencia de estilo se realiza estableciendo la [`Style.BasedOn`](xref:Xamarin.Forms.Style.BasedOn) propiedad en un existente [`Style`](xref:Xamarin.Forms.Style) . En XAML, esto se logra estableciendo la `BasedOn` propiedad en una `StaticResource` extensión de marcado que hace referencia a un creado previamente `Style` . En C#, esto se consigue estableciendo la `BasedOn` propiedad en una `Style` instancia de.

Los estilos que heredan de un estilo base pueden incluir [`Setter`](xref:Xamarin.Forms.Setter) instancias de nuevas propiedades o usarse para invalidar estilos del estilo base. Además, los estilos que heredan de un estilo base deben tener como destino el mismo tipo o un tipo que se derive del tipo de destino del estilo base. Por ejemplo, si un estilo base tiene como [`View`](xref:Xamarin.Forms.View) destino instancias, los estilos basados en el estilo base pueden tener como destino `View` instancias o tipos que derivan de la `View` clase, como [`Label`](xref:Xamarin.Forms.Label) instancias de y [`Button`](xref:Xamarin.Forms.Button) .

En el código siguiente se muestra la herencia de estilo *explícita* en una página XAML:

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

Las `baseStyle` instancias de destino [`View`](xref:Xamarin.Forms.View) y establecen las [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) propiedades y. `baseStyle`No se establece directamente en ningún control. En su lugar, `labelStyle` y `buttonStyle` heredan de él y establecen valores de propiedades enlazables adicionales. `labelStyle`Y `buttonStyle` se aplican a las [`Label`](xref:Xamarin.Forms.Label) instancias y la [`Button`](xref:Xamarin.Forms.Button) instancia, estableciendo sus [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propiedades. El resultado es el aspecto que se muestra en las capturas de pantalla siguientes:

[![Captura de pantalla de herencia de estilo](inheritance-images/style-inheritance.png)](inheritance-images/style-inheritance-large.png#lightbox)

> [!NOTE]
> Un estilo implícito se puede derivar de un estilo explícito, pero no se puede derivar un estilo explícito de un estilo implícito.

### <a name="respecting-the-inheritance-chain"></a>Respetar la cadena de herencia

Un estilo solo puede heredar de estilos en el mismo nivel, o superior, en la jerarquía de vistas. Esto significa que:

- Un recurso de nivel de aplicación solo puede heredar de otros recursos de nivel de aplicación.
- Un recurso de nivel de página puede heredar de recursos de nivel de aplicación y otros recursos de nivel de página.
- Un recurso de nivel de control puede heredar de recursos de nivel de aplicación, recursos de nivel de página y otros recursos de nivel de control.

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

En este ejemplo, `labelStyle` y `buttonStyle` son recursos de nivel de control, mientras que `baseStyle` es un recurso de nivel de página. Sin embargo, while `labelStyle` y `buttonStyle` heredan de `baseStyle` , no es posible `baseStyle` heredar de `labelStyle` o `buttonStyle` , debido a sus respectivas ubicaciones en la jerarquía de vistas.

## <a name="style-inheritance-in-c35"></a>Herencia de estilo en C&#35;

En el ejemplo de código siguiente se muestra la página de C# equivalente, donde [`Style`](xref:Xamarin.Forms.Style) las instancias se asignan directamente a las [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propiedades de los controles necesarios:

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

Las `baseStyle` instancias de destino [`View`](xref:Xamarin.Forms.View) y establecen las [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) propiedades y. `baseStyle`No se establece directamente en ningún control. En su lugar, `labelStyle` y `buttonStyle` heredan de él y establecen valores de propiedades enlazables adicionales. `labelStyle`Y `buttonStyle` se aplican a las [`Label`](xref:Xamarin.Forms.Label) instancias y la [`Button`](xref:Xamarin.Forms.Button) instancia, estableciendo sus [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propiedades.

## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos básicos (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Trabajar con estilos (ejemplo)](/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)