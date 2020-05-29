---
title: Estilos dinámicos enXamarin.Forms
description: En este artículo se explica cómo una Xamarin.Forms aplicación puede responder dinámicamente a los cambios de estilo en tiempo de ejecución mediante recursos dinámicos.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.custom: ''
ms.openlocfilehash: d40ca3423cca68757cf458faf5cca1138aec5461
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140093"
---
# <a name="dynamic-styles-in-xamarinforms"></a>Estilos dinámicos enXamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)

_Los estilos no responden a los cambios de propiedad y permanecen sin cambios durante la ejecución de una aplicación. Por ejemplo, después de asignar un estilo a un elemento visual, si una de las instancias del establecedor se modifica, se quita o se agrega una nueva instancia de establecedor, los cambios no se aplicarán al elemento visual. Sin embargo, las aplicaciones pueden responder dinámicamente a los cambios de estilo en tiempo de ejecución mediante recursos dinámicos._

La `DynamicResource` extensión de marcado es similar a la `StaticResource` extensión de marcado en que ambos usan una clave de diccionario para capturar un valor de [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . Sin embargo, mientras `StaticResource` realiza una búsqueda de un solo diccionario, `DynamicResource` mantiene un vínculo a la clave del diccionario. Por lo tanto, si se reemplaza la entrada del diccionario asociada a la clave, el cambio se aplica al elemento visual. Esto permite realizar cambios de estilo en tiempo de ejecución en una aplicación.

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

Las [`SearchBar`](xref:Xamarin.Forms.SearchBar) instancias de usan la `DynamicResource` extensión de marcado para hacer referencia a un [`Style`](xref:Xamarin.Forms.Style) denominado `searchBarStyle` , que no está definido en el XAML. Sin embargo, dado que las [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propiedades de las `SearchBar` instancias se establecen mediante `DynamicResource` , la clave del diccionario que falta no produce una excepción.

En su lugar, en el archivo de código subyacente, el constructor crea una [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) entrada con la clave `searchBarStyle` , como se muestra en el ejemplo de código siguiente:

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

Cuando `OnButtonClicked` se ejecuta el controlador de eventos, cambiará `searchBarStyle` entre `blueSearchBarStyle` y `greenSearchBarStyle` . El resultado es el aspecto que se muestra en las capturas de pantalla siguientes:

Ejemplo de estilo dinámico [ ![ azul ejemplo](dynamic-images/dynamic-style-blue.png)](dynamic-images/dynamic-style-blue-large.png#lightbox)de 
 [ ![ estilo dinámico verde](dynamic-images/dynamic-style-green.png)](dynamic-images/dynamic-style-green-large.png#lightbox)

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

En C#, las [`SearchBar`](xref:Xamarin.Forms.SearchBar) instancias de usan el [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource*) método para hacer referencia a `searchBarStyle` . El `OnButtonClicked` código del controlador de eventos es idéntico al ejemplo XAML y, cuando se ejecuta, cambiará `searchBarStyle` entre `blueSearchBarStyle` y `greenSearchBarStyle` .

## <a name="dynamic-style-inheritance"></a>Herencia de estilo dinámico

No se puede derivar un estilo a partir de un estilo dinámico mediante la [`Style.BasedOn`](xref:Xamarin.Forms.Style.BasedOn) propiedad. En su lugar, la [`Style`](xref:Xamarin.Forms.Style) clase incluye la [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) propiedad, que se puede establecer en una clave de diccionario cuyo valor puede cambiar dinámicamente.

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

Las [`SearchBar`](xref:Xamarin.Forms.SearchBar) instancias de usan la `StaticResource` extensión de marcado para hacer referencia a un [`Style`](xref:Xamarin.Forms.Style) denominado `tealSearchBarStyle` . Esto `Style` establece algunas propiedades adicionales y usa la [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) propiedad como referencia `searchBarStyle` . La `DynamicResource` extensión de marcado no es necesaria porque `tealSearchBarStyle` no cambiará, salvo `Style` que se derive de. Por lo tanto, `tealSearchBarStyle` mantiene un vínculo a `searchBarStyle` y se modifica cuando cambia el estilo base.

En el archivo de código subyacente, el constructor crea una [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) entrada con la clave `searchBarStyle` , tal y como se muestra en el ejemplo anterior que demostró los estilos dinámicos. Cuando `OnButtonClicked` se ejecuta el controlador de eventos, cambiará `searchBarStyle` entre `blueSearchBarStyle` y `greenSearchBarStyle` . El resultado es el aspecto que se muestra en las capturas de pantalla siguientes:

Ejemplo de herencia de [ ![ estilo dinámico azul ejemplo](dynamic-images/dynamic-style-inheritance-blue.png)](dynamic-images/dynamic-style-inheritance-blue-large.png#lightbox)de 
 [ ![ herencia de estilo dinámico verde](dynamic-images/dynamic-style-inheritance-green.png)](dynamic-images/dynamic-style-inheritance-green-large.png#lightbox)

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

`tealSearchBarStyle`Se asigna directamente a la [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propiedad de las [`SearchBar`](xref:Xamarin.Forms.SearchBar) instancias de. Esto `Style` establece algunas propiedades adicionales y usa la [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) propiedad como referencia `searchBarStyle` . El [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource*) método no es necesario aquí porque `tealSearchBarStyle` no cambiará, salvo `Style` que se derive de. Por lo tanto, `tealSearchBarStyle` mantiene un vínculo a `searchBarStyle` y se modifica cuando cambia el estilo base.

## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos dinámicos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)
- [Trabajar con estilos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Dynamic-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
