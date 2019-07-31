---
title: Estilos explícitos en Xamarin.Forms
description: Un estilo explícito es aquella que se aplica de manera selectiva a los controles estableciendo sus propiedades de estilo. En este artículo se explica cómo consumir estilos explícitos en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: C0DF9F8F-B431-4374-A574-325BC3C41A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 567985b1d6c6aff87e0a6879f05ab711a0095d6f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647200"
---
# <a name="explicit-styles-in-xamarinforms"></a>Estilos explícitos en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Un estilo explícito es aquella que se aplica de manera selectiva a los controles estableciendo sus propiedades de estilo._

## <a name="create-an-explicit-style-in-xaml"></a>Crear un estilo explícito en XAML

Para declarar un [ `Style` ](xref:Xamarin.Forms.Style) en el nivel de página, un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) debe agregarse a la página y, a continuación, uno o varios `Style` declaraciones pueden incluirse en el `ResourceDictionary`. Un `Style` estará *explícita* proporcionando su declaración un `x:Key` atributo, que proporciona una clave descriptiva en el `ResourceDictionary`. *Explícita* estilos, a continuación, se deben aplicar a los elementos visuales específicos estableciendo sus [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propiedades.

El siguiente ejemplo de código muestra *explícita* estilos declaran en XAML en una página `ResourceDictionary` y se aplica a la página [ `Label` ](xref:Xamarin.Forms.Label) instancias:

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

El [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) define tres *explícita* estilos que se aplican a la página [ `Label` ](xref:Xamarin.Forms.Label) instancias. Cada `Style` se utiliza para mostrar texto en un color diferente, y también establece la fuente de las opciones de diseño de tamaño y horizontales y verticales. Cada `Style` se aplica a otro `Label` estableciendo su [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propiedades mediante el `StaticResource` extensión de marcado. El resultado es el aspecto que se muestra en las capturas de pantalla siguiente:

[![](explicit-images/explicit-styles.png "Ejemplo de estilos explícitos")](explicit-images/explicit-styles-large.png#lightbox "ejemplo estilos explícitos")

Además, el último [ `Label` ](xref:Xamarin.Forms.Label) tiene un [ `Style` ](xref:Xamarin.Forms.Style) aplicado a él, pero también invalida la [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor) propiedad a una diferente `Color`valor.

### <a name="create-an-explicit-style-at-the-control-level"></a>Crear un estilo explícito en el nivel de control

Además de crear *explícita* estilos en el nivel de página, también pueden crearse en el nivel de control, tal como se muestra en el ejemplo de código siguiente:

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

En este ejemplo, el *explícita* [ `Style` ](xref:Xamarin.Forms.Style) instancias se asignan a la [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) colección de la [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) control. Los estilos, a continuación, se aplican al control y sus elementos secundarios.

Para obtener información sobre cómo crear estilos en una aplicación [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), consulte [estilos globales](~/xamarin-forms/user-interface/styles/application.md).

## <a name="create-an-explicit-style-in-c35"></a>Crear un estilo explícito en C&#35;

[`Style`](xref:Xamarin.Forms.Style) las instancias se pueden agregar a una página [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) colección en C# mediante la creación de un nuevo [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)y, a continuación, agregando el `Style` instancias para el `ResourceDictionary`, tal y como se muestra en el ejemplo de código siguiente:

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

El constructor define tres *explícita* estilos que se aplican a la página [ `Label` ](xref:Xamarin.Forms.Label) instancias. Cada *explícita* [ `Style` ](xref:Xamarin.Forms.Style) se agrega a la [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) utilizando el [ `Add` ](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) método, especifica un `key` cadena para hacer referencia a la `Style` instancia. Cada `Style` se aplica a otro `Label` estableciendo sus [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propiedades.

Sin embargo, no hay ninguna ventaja en utilizar un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) aquí. En su lugar, [ `Style` ](xref:Xamarin.Forms.Style) instancias pueden asignarse directamente a la [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) las propiedades de los elementos visuales necesarios y el `ResourceDictionary` puede quitarse, como se muestra en la siguiente ejemplo de código:

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

El constructor define tres *explícita* estilos que se aplican a la página [ `Label` ](xref:Xamarin.Forms.Label) instancias. Cada `Style` se utiliza para mostrar texto en un color diferente, y también establece la fuente de las opciones de diseño de tamaño y horizontales y verticales. Cada `Style` se aplica a otro `Label` estableciendo su [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propiedades. Además, el último `Label` tiene un `Style` aplicado a él, pero también invalida la `TextColor` propiedad en otro `Color` valor.

## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos básicos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Trabajar con estilos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Establecedor](xref:Xamarin.Forms.Setter)
