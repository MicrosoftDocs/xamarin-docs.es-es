---
title: Introducción a los estilos de Xamarin. Forms
description: Los estilos permiten personalizar el aspecto de los elementos visuales. Los estilos se definen para un tipo específico y contienen valores para las propiedades disponibles en ese tipo.
ms.prod: xamarin
ms.assetid: 3FF899C0-6CFB-4C1D-837D-9E9E10181967
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 35f8dad3590c07ceb3c93aa735b8c02d75098498
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "70228171"
---
# <a name="introduction-to-xamarinforms-styles"></a>Introducción a los estilos de Xamarin. Forms

_estilos permiten personalizar los elementos visuales. Los estilos se definen para un tipo específico y contienen valores para las propiedades disponibles en ese tipo._

Las aplicaciones de Xamarin. Forms suelen contener varios controles que tienen un aspecto idéntico. Por ejemplo, una aplicación puede tener varias instancias de [`Label`](xref:Xamarin.Forms.Label) que tienen las mismas opciones de fuente y opciones de diseño, tal y como se muestra en el siguiente ejemplo de código XAML:

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

Cada instancia de [`Label`](xref:Xamarin.Forms.Label) tiene valores de propiedad idénticos para controlar la apariencia del texto que muestra el `Label`. El resultado es el aspecto que se muestra en las capturas de pantalla siguientes:

[![la apariencia de la etiqueta sin estilos](introduction-images/no-styles.png)](introduction-images/no-styles-large.png#lightbox)

Establecer la apariencia de cada control individual puede ser repetitivo y propenso a errores. En su lugar, se puede crear un estilo que defina la apariencia y se aplique a los controles necesarios.

## <a name="create-a-style"></a>Crear un estilo

La clase [`Style`](xref:Xamarin.Forms.Style) agrupa una colección de valores de propiedad en un objeto que puede aplicarse a varias instancias de elementos visuales. Esto ayuda a reducir el marcado repetitivo y permite cambiar la apariencia de las aplicaciones más fácilmente.

Aunque los estilos se diseñaron principalmente para las aplicaciones basadas en XAML, también se pueden C#crear en:

- las instancias de [`Style`](xref:Xamarin.Forms.Style) creadas en XAML se definen normalmente en una [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) asignada a la colección de [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) de un control, página o a la colección de [`Resources`](xref:Xamarin.Forms.Application.Resources) de la aplicación.
- [`Style`](xref:Xamarin.Forms.Style) las instancias de C# creadas en se definen normalmente en la clase de la página o en una clase a la que se puede tener acceso de forma global.

La elección de dónde se puede definir una instancia de [`Style`](xref:Xamarin.Forms.Style) afecta a dónde se puede usar:

- [`Style`](xref:Xamarin.Forms.Style) instancias definidas en el nivel de control solo se pueden aplicar al control y a sus elementos secundarios.
- [`Style`](xref:Xamarin.Forms.Style) instancias definidas en el nivel de página solo se pueden aplicar a la página y a sus elementos secundarios.
- [`Style`](xref:Xamarin.Forms.Style) instancias definidas en el nivel de aplicación se pueden aplicar en toda la aplicación.

Cada instancia de [`Style`](xref:Xamarin.Forms.Style) contiene una colección de uno o varios objetos [`Setter`](xref:Xamarin.Forms.Setter) , donde cada `Setter` tiene un [`Property`](xref:Xamarin.Forms.Setter.Property) y un [`Value`](xref:Xamarin.Forms.Setter.Value). El `Property` es el nombre de la propiedad enlazable del elemento al que se aplica el estilo y el `Value` es el valor que se aplica a la propiedad.

Cada instancia de [`Style`](xref:Xamarin.Forms.Style) puede ser *explícita*o *implícita*:

- Se define una instancia de [`Style`](xref:Xamarin.Forms.Style) *explícita* especificando un [`TargetType`](xref:Xamarin.Forms.Style.TargetType) y un valor de `x:Key` y estableciendo la propiedad [`Style`](xref:Xamarin.Forms.NavigableElement.Style) del elemento de destino en la referencia de `x:Key`. Para obtener más información sobre los estilos *explícitos* , vea [estilos explícitos](~/xamarin-forms/user-interface/styles/explicit.md).
- Una instancia de [`Style`](xref:Xamarin.Forms.Style) *implícita* se define especificando solo un [`TargetType`](xref:Xamarin.Forms.Style.TargetType). La instancia de `Style` se aplicará automáticamente a todos los elementos de ese tipo. Tenga en cuenta que las subclases de la `TargetType` no tienen automáticamente el `Style` aplicado. Para obtener más información sobre los estilos *implícitos* , vea [estilos implícitos](~/xamarin-forms/user-interface/styles/implicit.md).

Al crear un [`Style`](xref:Xamarin.Forms.Style), siempre se requiere la propiedad [`TargetType`](xref:Xamarin.Forms.Style.TargetType) . En el ejemplo de código siguiente se muestra un estilo *explícito* (tenga en cuenta el `x:Key`) creado en XAML:

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

Para aplicar un `Style`, el objeto de destino debe ser un [`VisualElement`](xref:Xamarin.Forms.VisualElement) que coincida con el valor de la propiedad [`TargetType`](xref:Xamarin.Forms.Style.TargetType) del `Style`, como se muestra en el siguiente ejemplo de código XAML:

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

Los estilos inferiores de la jerarquía de vistas tienen prioridad sobre las definidas más arriba. Por ejemplo, el establecimiento de un [`Style`](xref:Xamarin.Forms.Style) que establezca [`Label.TextColor`](xref:Xamarin.Forms.Label.TextColor) en `Red` en el nivel de aplicación se reemplazará por un estilo de nivel de página que establezca `Label.TextColor` en `Green`. De igual forma, un estilo de nivel de página se reemplazará por un estilo de nivel de control. Además, si `Label.TextColor` se establece directamente en una propiedad de control, tiene prioridad sobre cualquier estilo.

En los artículos de esta sección se muestra y se explica cómo crear y aplicar estilos *explícitos* e *implícitos* , cómo crear estilos globales, la herencia de estilo, cómo responder a los cambios de estilo en tiempo de ejecución y cómo usar los estilos integrados incluidos en Xamarin. Forms.

> [!NOTE]
> **¿Qué es StyleId?**
>
> Antes de Xamarin. Forms 2,2, la propiedad [`StyleId`](xref:Xamarin.Forms.Element.StyleId) se usaba para identificar elementos individuales en una aplicación para su identificación en las pruebas de IU y en motores de tema como Pixate. Sin embargo, Xamarin. Forms 2,2 presentó la propiedad [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) , que ha reemplazado la propiedad [`StyleId`](xref:Xamarin.Forms.Element.StyleId) .

## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilo](xref:Xamarin.Forms.Style)
- [Establecedor](xref:Xamarin.Forms.Setter)
