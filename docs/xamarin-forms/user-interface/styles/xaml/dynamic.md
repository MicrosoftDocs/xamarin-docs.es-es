---
title: Estilos dinámicos en Xamarin.Forms
description: En este artículo se explica cómo una aplicación de Xamarin.Forms puede responder a los cambios de estilo dinámicamente en tiempo de ejecución mediante el uso de recursos dinámicos.
ms.prod: xamarin
ms.assetid: 13D4FA4B-DF10-42BF-B001-2C49367FC216
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: 9a26532d13b843b812da94739be071c7accac212
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/07/2020
ms.locfileid: "78918693"
---
# <a name="dynamic-styles-in-xamarinforms"></a>Estilos dinámicos en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)

_Los estilos no responden a los cambios de propiedad y permanecen sin cambios durante la ejecución de una aplicación. Por ejemplo, después de asignar un estilo a un elemento visual, si una de las instancias del establecedor se modifica, se quita o se agrega una nueva instancia de establecedor, los cambios no se aplicarán al elemento visual. Sin embargo, las aplicaciones pueden responder dinámicamente a los cambios de estilo en tiempo de ejecución mediante recursos dinámicos._

La extensión de marcado `DynamicResource` es similar a la extensión de marcado `StaticResource` en que ambos usan una clave de diccionario para capturar un valor de un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). Sin embargo, mientras que el `StaticResource` realiza una búsqueda de diccionario única, el `DynamicResource` mantiene un vínculo a la clave del diccionario. Por lo tanto, si se reemplaza la entrada del diccionario asociada con la clave, el cambio se aplica al elemento visual. Esto permite que los cambios de estilo en tiempo de ejecución que se realiza en una aplicación.

En el ejemplo de código siguiente se muestran los estilos *dinámicos* en una página XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesPage" Title="Dynamic" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle"
                   TargetType="SearchBar"
                   BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle"
                   TargetType="SearchBar">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Placeholder="These SearchBar controls"
                       Style="{DynamicResource searchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Las instancias de [`SearchBar`](xref:Xamarin.Forms.SearchBar) utilizan la extensión de marcado `DynamicResource` para hacer referencia a una [`Style`](xref:Xamarin.Forms.Style) denominada `searchBarStyle`, que no está definida en el código XAML. Sin embargo, dado que las propiedades de [`Style`](xref:Xamarin.Forms.NavigableElement.Style) de las instancias de `SearchBar` se establecen con un `DynamicResource`, la clave del diccionario que falta no produce una excepción.

En su lugar, en el archivo de código subyacente, el constructor crea una entrada de [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) con la clave `searchBarStyle`, como se muestra en el ejemplo de código siguiente:

```csharp
public partial class DynamicStylesPage : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPage ()
    {
        InitializeComponent ();
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
    }

    void OnButtonClicked (object sender, EventArgs e)
    {
        if (originalStyle) {
            Resources ["searchBarStyle"] = Resources ["greenSearchBarStyle"];
            originalStyle = false;
        } else {
            Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
            originalStyle = true;
        }
    }
}
```

Cuando se ejecuta el controlador de eventos `OnButtonClicked`, `searchBarStyle` cambiará entre `blueSearchBarStyle` y `greenSearchBarStyle`. El resultado es el aspecto que se muestra en las capturas de pantalla siguientes:

[![ejemplo de estilo dinámico azul](dynamic-images/dynamic-style-blue.png)](dynamic-images/dynamic-style-blue-large.png#lightbox)
[![ejemplo de estilo dinámico verde](dynamic-images/dynamic-style-green.png)](dynamic-images/dynamic-style-green-large.png#lightbox)

En el ejemplo de código siguiente se muestra la página equivalente en C#:

```csharp
public class DynamicStylesPageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        ...
        var searchBar1 = new SearchBar { Placeholder = "These SearchBar controls" };
        searchBar1.SetDynamicResource (VisualElement.StyleProperty, "searchBarStyle");
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = { searchBar1, searchBar2, searchBar3, searchBar4,    button    }
        };
    }
    ...
}
```

En C#, las instancias de [`SearchBar`](xref:Xamarin.Forms.SearchBar) utilizan el método [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource*) para hacer referencia a `searchBarStyle`. El código del controlador de eventos `OnButtonClicked` es idéntico al ejemplo XAML y, cuando se ejecuta, `searchBarStyle` cambiará entre `blueSearchBarStyle` y `greenSearchBarStyle`.

## <a name="dynamic-style-inheritance"></a>Herencia de estilo dinámico

No se puede derivar un estilo a partir de un estilo dinámico mediante la propiedad [`Style.BasedOn`](xref:Xamarin.Forms.Style.BasedOn) . En su lugar, la clase [`Style`](xref:Xamarin.Forms.Style) incluye la propiedad [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) , que se puede establecer en una clave de diccionario cuyo valor puede cambiar dinámicamente.

En el ejemplo de código siguiente se muestra la herencia de estilo *dinámico* en una página XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesInheritancePage" Title="Dynamic Inheritance" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle" TargetType="SearchBar" BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle" TargetType="SearchBar">
              ...
            </Style>
            <Style x:Key="tealSearchBarStyle" TargetType="SearchBar" BaseResourceKey="searchBarStyle">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Text="These SearchBar controls" Style="{StaticResource tealSearchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Las instancias de [`SearchBar`](xref:Xamarin.Forms.SearchBar) utilizan la extensión de marcado `StaticResource` para hacer referencia a una [`Style`](xref:Xamarin.Forms.Style) denominada `tealSearchBarStyle`. Esta `Style` establece algunas propiedades adicionales y usa la propiedad [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) para hacer referencia a `searchBarStyle`. La extensión de marcado `DynamicResource` no es necesaria porque `tealSearchBarStyle` no cambiará, excepto por el `Style` del que se deriva. Por lo tanto, `tealSearchBarStyle` mantiene un vínculo a `searchBarStyle` y se modifica cuando cambia el estilo base.

En el archivo de código subyacente, el constructor crea una entrada de [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) con el `searchBarStyle`de clave, como se muestra en el ejemplo anterior que demostró los estilos dinámicos. Cuando se ejecuta el controlador de eventos `OnButtonClicked`, `searchBarStyle` cambiará entre `blueSearchBarStyle` y `greenSearchBarStyle`. El resultado es el aspecto que se muestra en las capturas de pantalla siguientes:

[![ejemplo de herencia de estilo dinámico azul](dynamic-images/dynamic-style-inheritance-blue.png)](dynamic-images/dynamic-style-inheritance-blue-large.png#lightbox)
[![ejemplo de herencia de estilo dinámico verde](dynamic-images/dynamic-style-inheritance-green.png)](dynamic-images/dynamic-style-inheritance-green-large.png#lightbox)

En el ejemplo de código siguiente se muestra la página equivalente en C#:

```csharp
public class DynamicStylesInheritancePageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesInheritancePageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var tealSearchBarStyle = new Style (typeof(SearchBar)) {
            BaseResourceKey = "searchBarStyle",
            ...
        };
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = {
                new SearchBar { Text = "These SearchBar controls", Style = tealSearchBarStyle },
                ...
            }
        };
    }
    ...
}
```

El `tealSearchBarStyle` se asigna directamente a la propiedad [`Style`](xref:Xamarin.Forms.NavigableElement.Style) de las instancias de [`SearchBar`](xref:Xamarin.Forms.SearchBar) . Esta `Style` establece algunas propiedades adicionales y usa la propiedad [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) para hacer referencia a `searchBarStyle`. El método [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource*) no es necesario aquí porque no cambiará `tealSearchBarStyle`, excepto el `Style` del que se deriva. Por lo tanto, `tealSearchBarStyle` mantiene un vínculo a `searchBarStyle` y se modifica cuando cambia el estilo base.

## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos dinámicos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)
- [Trabajar con estilos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Establecedor](xref:Xamarin.Forms.Setter)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Dynamic-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
