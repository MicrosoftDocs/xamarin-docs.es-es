---
title: EmptyView de Xamarin. Forms CollectionView
description: En CollectionView, se puede especificar una vista vacía que proporciona comentarios al usuario cuando no hay datos disponibles para mostrar. La vista vacía puede ser una cadena, una vista o varias vistas.
ms.prod: xamarin
ms.assetid: 6CEBCFE6-5577-4F68-9709-431062609153
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: c6a2a53f267a7f6764ec441944193e8c5ecd9189
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "68739189"
---
# <a name="xamarinforms-collectionview-emptyview"></a>EmptyView de Xamarin. Forms CollectionView

![](~/media/shared/preview.png "Esta API se encuentra actualmente en versión preliminar")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define las siguientes propiedades que se pueden usar para proporcionar comentarios del usuario cuando no hay datos para mostrar:

- [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView), de tipo `object`, la cadena, el enlace o la vista que se mostrarán cuando [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) la propiedad `null`sea, o cuando la colección especificada por `ItemsSource` la propiedad `null` sea o esté vacía. El valor predeterminado es `null`.
- [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate), de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), la plantilla que se va a utilizar para `EmptyView`dar formato al especificado. El valor predeterminado es `null`.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos.

Los principales escenarios de uso para establecer [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) la propiedad muestran los comentarios [`CollectionView`](xref:Xamarin.Forms.CollectionView) de los usuarios cuando una operación de filtrado en no produce datos y muestra los comentarios de los usuarios mientras se recuperan los datos de un servicio Web.

> [!NOTE]
> La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad se puede establecer en una vista que incluya contenido interactivo si es necesario.

Para obtener más información sobre las plantillas de datos, consulte [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="display-a-string-when-data-is-unavailable"></a>Mostrar una cadena cuando los datos no estén disponibles

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad se puede establecer en una cadena, que se mostrará cuando la [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propiedad sea `null`o cuando la colección especificada por la `ItemsSource` propiedad sea `null` o esté vacía. En el código XAML siguiente se muestra un ejemplo de este escenario:

```xaml
<CollectionView ItemsSource="{Binding EmptyMonkeys}"
                EmptyView="No items to display" />
```

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView
{
    EmptyView = "No items to display"
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "EmptyMonkeys");
```

El resultado es que, dado que la colección enlazada `null`a datos es, se muestra [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) la cadena establecida como el valor de la propiedad:

[![Captura de pantalla de una lista de CollectionView vertical con una vista de texto vacía, en iOS y Android](emptyview-images/null-itemssource.png "Lista vertical de CollectionView con vista de texto vacía")](emptyview-images/null-itemssource-large.png#lightbox "Lista vertical de CollectionView con vista de texto vacía")

## <a name="display-views-when-data-is-unavailable"></a>Mostrar vistas cuando los datos no están disponibles

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad se puede establecer en una vista, que se mostrará cuando la [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propiedad sea `null`o cuando la colección especificada por la `ItemsSource` propiedad sea `null` o esté vacía. Puede ser una vista única o una vista que contiene varias vistas secundarias. En el ejemplo de XAML siguiente `EmptyView` se muestra la propiedad establecida en una vista que contiene varias vistas secundarias:

```xaml
<StackLayout Margin="20">
    <SearchBar x:Name="searchBar"
               SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
               Placeholder="Filter" />
    <CollectionView ItemsSource="{Binding Monkeys}">
        <CollectionView.ItemTemplate>
            <DataTemplate>
                ...
            </DataTemplate>
        </CollectionView.ItemTemplate>
        <CollectionView.EmptyView>
            <StackLayout>
                <Label Text="No results matched your filter."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
                <Label Text="Try a broader filter?"
                       FontAttributes="Italic"
                       FontSize="12"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </CollectionView.EmptyView>
    </CollectionView>
</StackLayout>
```

El código de C# equivalente es el siguiente:

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = new StackLayout
    {
        Children =
        {
            new Label { Text = "No results matched your filter.", ... },
            new Label { Text = "Try a broader filter?", ... }
        }
    }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

[`SearchBar`](xref:Xamarin.Forms.SearchBar) Cuando ejecuta `FilterCommand`, la colección que muestra [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) [sefiltraparaeltérminodebúsquedaalmacenadoenlapropiedad.`CollectionView`](xref:Xamarin.Forms.CollectionView) Si la operación de filtrado no produce datos, se [`StackLayout`](xref:Xamarin.Forms.StackLayout) muestra el conjunto [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) como el valor de la propiedad:

[![Captura de pantalla de una lista de CollectionView vertical con vista vacía personalizada, en iOS y Android](emptyview-images/filter-multiple-views.png "Lista vertical de CollectionView con vista vacía personalizada")](emptyview-images/filter-multiple-views-large.png#lightbox "Lista vertical de CollectionView con vista vacía personalizada")

## <a name="display-a-templated-custom-type-when-data-is-unavailable"></a>Mostrar un tipo personalizado con plantilla cuando los datos no estén disponibles

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad se puede establecer en un tipo personalizado, cuya plantilla se muestra cuando la [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propiedad es `null`, o cuando la colección especificada por la `ItemsSource` propiedad es `null` o está vacía. La [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propiedad se puede establecer en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que `EmptyView`define la apariencia de. En el código XAML siguiente se muestra un ejemplo de este escenario:

```xaml
<StackLayout Margin="20">
    <SearchBar x:Name="searchBar"
               SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
               Placeholder="Filter" />
    <CollectionView ItemsSource="{Binding Monkeys}">
        <CollectionView.ItemTemplate>
            <DataTemplate>
                ...
            </DataTemplate>
        </CollectionView.ItemTemplate>
        <CollectionView.EmptyView>
            <views:FilterData Filter="{Binding Source={x:Reference searchBar}, Path=Text}" />
        </CollectionView.EmptyView>
        <CollectionView.EmptyViewTemplate>
            <DataTemplate>
                <Label Text="{Binding Filter, StringFormat='Your filter term of {0} did not match any records.'}"
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </DataTemplate>
        </CollectionView.EmptyViewTemplate>
    </CollectionView>
</StackLayout>
```

El código de C# equivalente es el siguiente:

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = new FilterData { Filter = searchBar.Text },
    EmptyViewTemplate = new DataTemplate(() =>
    {
        return new Label { ... };
    })
};
```

El `FilterData` tipo define una `Filter` propiedad y un correspondiente [`BindableProperty`](xref:Xamarin.Forms.BindableProperty):

```csharp
public class FilterData : BindableObject
{
    public static readonly BindableProperty FilterProperty = BindableProperty.Create(nameof(Filter), typeof(string), typeof(FilterData), null);

    public string Filter
    {
        get { return (string)GetValue(FilterProperty); }
        set { SetValue(FilterProperty, value); }
    }
}
```

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad se establece en un `FilterData` objeto y los datos `Filter` de la propiedad se enlazan [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) a la propiedad. [`SearchBar`](xref:Xamarin.Forms.SearchBar) Cuando ejecuta `FilterCommand`, la colección que muestra `Filter` [sefiltraparaeltérminodebúsquedaalmacenadoenlapropiedad.`CollectionView`](xref:Xamarin.Forms.CollectionView) Si la operación de filtrado no produce datos, se [`Label`](xref:Xamarin.Forms.Label) muestra el definido [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)en, que se establece como [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) el valor de la propiedad:

[![Captura de pantalla de una lista de CollectionView vertical con una plantilla de vista vacía, en iOS y Android](emptyview-images/emptyviewtemplate.png "Lista de collectionviews verticales con plantilla de vista vacía")](emptyview-images/emptyviewtemplate-large.png#lightbox "Lista de CollectionViews verticales con plantilla de vista vacía")

> [!NOTE]
> Cuando se muestra un tipo personalizado con plantilla cuando los datos no están [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) disponibles, la propiedad se puede establecer en una vista que contiene varias vistas secundarias.

## <a name="choose-an-emptyview-at-runtime"></a>Elegir un EmptyView en tiempo de ejecución

Las vistas que se mostrarán como [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) cuando los datos no estén disponibles, se pueden [`ContentView`](xref:Xamarin.Forms.ContentView) definir como objetos [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)en un. A `EmptyView` continuación, la propiedad se puede establecer en `ContentView`un específico, en función de alguna lógica de negocios, en tiempo de ejecución. En el ejemplo de XAML siguiente se muestra un ejemplo de este escenario:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CollectionViewDemos.Views.EmptyViewSwapPage"
             Title="EmptyView (swap)">
    <ContentPage.Resources>
        <ContentView x:Key="BasicEmptyView">
            <StackLayout>
                <Label Text="No items to display."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </ContentView>
        <ContentView x:Key="AdvancedEmptyView">
            <StackLayout>
                <Label Text="No results matched your filter."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
                <Label Text="Try a broader filter?"
                       FontAttributes="Italic"
                       FontSize="12"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </ContentView>
    </ContentPage.Resources>

    <StackLayout Margin="20">
        <SearchBar x:Name="searchBar"
                   SearchCommand="{Binding FilterCommand}"
                   SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
                   Placeholder="Filter" />
        <StackLayout Orientation="Horizontal">
            <Label Text="Toggle EmptyViews" />
            <Switch Toggled="OnEmptyViewSwitchToggled" />
        </StackLayout>
        <CollectionView x:Name="collectionView"
                        ItemsSource="{Binding Monkeys}">
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    ...
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </StackLayout>
</ContentPage>
```

Este código XAML define [`ContentView`](xref:Xamarin.Forms.ContentView) dos objetos en el nivel [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de página, con [`Switch`](xref:Xamarin.Forms.Switch) el objeto que `ContentView` controla qué objeto se establecerá [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) como el valor de la propiedad. Cuando se alterna, el controlador de `OnEmptyViewSwitchToggled` eventos ejecuta el `ToggleEmptyView` método: [`Switch`](xref:Xamarin.Forms.Switch)

```csharp
void ToggleEmptyView(bool isToggled)
{
    collectionView.EmptyView = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
}
```

El `ToggleEmptyView` método establece la [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad del `collectionView` objeto en [`ContentView`](xref:Xamarin.Forms.ContentView) [`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) uno de los dos objetos almacenados en [,basándoseenelvalordelapropiedad.`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) [`SearchBar`](xref:Xamarin.Forms.SearchBar) Cuando ejecuta `FilterCommand`, la colección que muestra [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) [sefiltraparaeltérminodebúsquedaalmacenadoenlapropiedad.`CollectionView`](xref:Xamarin.Forms.CollectionView) Si la operación de filtrado no produce datos, se `ContentView` muestra el objeto establecido `EmptyView` como la propiedad:

[![Captura de pantalla de una lista de CollectionView vertical con vistas vacías intercambiadas, en iOS y Android](emptyview-images/swap.png "Lista de CollectionView vertical con vistas vacías intercambiadas")](emptyview-images/swap-large.png#lightbox "Lista de CollectionView vertical con vistas vacías intercambiadas")

Para obtener más información sobre los diccionarios de recursos, consulte [diccionarios de recursos de Xamarin. Forms](~/xamarin-forms/xaml/resource-dictionaries.md).

## <a name="choose-an-emptyviewtemplate-at-runtime"></a>Elegir un EmptyViewTemplate en tiempo de ejecución

La apariencia de se [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) puede elegir en tiempo de ejecución, en función de su valor, estableciendo [`CollectionView.EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) la propiedad en [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) un objeto:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CollectionViewDemos.Controls">
    <ContentPage.Resources>
        <DataTemplate x:Key="AdvancedTemplate">
            ...
        </DataTemplate>

        <DataTemplate x:Key="BasicTemplate">
            ...
        </DataTemplate>

        <controls:SearchTermDataTemplateSelector x:Key="SearchSelector"
                                                 DefaultTemplate="{StaticResource AdvancedTemplate}"
                                                 OtherTemplate="{StaticResource BasicTemplate}" />
    </ContentPage.Resources>

    <StackLayout Margin="20">
        <SearchBar x:Name="searchBar"
                   SearchCommand="{Binding FilterCommand}"
                   SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
                   Placeholder="Filter" />
        <CollectionView ItemsSource="{Binding Monkeys}"
                        EmptyView="{Binding Source={x:Reference searchBar}, Path=Text}"
                        EmptyViewTemplate="{StaticResource SearchSelector}" />
    </StackLayout>
</ContentPage>
```

El código de C# equivalente es el siguiente:

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = searchBar.Text,
    EmptyViewTemplate = new SearchTermDataTemplateSelector { ... }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad se establece en la [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) propiedad y la [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propiedad se establece en un `SearchTermDataTemplateSelector` objeto.

[`SearchBar`](xref:Xamarin.Forms.SearchBar) Cuando ejecuta `FilterCommand`, la colección que muestra [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) [sefiltraparaeltérminodebúsquedaalmacenadoenlapropiedad.`CollectionView`](xref:Xamarin.Forms.CollectionView) Si la operación de filtrado no produce datos, el [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) elegido por el `SearchTermDataTemplateSelector` objeto se establece como la [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propiedad y se muestra.

En el ejemplo siguiente se `SearchTermDataTemplateSelector` muestra la clase:

```csharp
public class SearchTermDataTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate OtherTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        string query = (string)item;
        return query.ToLower().Equals("xamarin") ? OtherTemplate : DefaultTemplate;
    }
}
```

La clase `SearchTermTemplateSelector` define las propiedades `DefaultTemplate` y `OtherTemplate` de tipo [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) que se establecen para diferentes plantillas de datos. La `OnSelectTemplate` invalidación `DefaultTemplate`devuelve, que muestra un mensaje al usuario, cuando la consulta de búsqueda no es igual a "Xamarin". Cuando la consulta de búsqueda es igual a "Xamarin", `OnSelectTemplate` la invalidación devuelve `OtherTemplate`, que muestra un mensaje básico al usuario:

[![Captura de pantalla de una selección de plantilla de vista vacía en tiempo de ejecución de CollectionView, en iOS y Android](emptyview-images/datatemplateselector.png "Selección de plantilla de vista vacía en tiempo de ejecución en una colección CollectionView")](emptyview-images/datatemplateselector-large.png#lightbox "Selección de plantilla de vista vacía en tiempo de ejecución en una colección CollectionView")

Para obtener más información sobre los selectores de plantilla de datos, vea [Create a Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Plantillas de datos de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Diccionarios de recursos de Xamarin. Forms](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Creación de un DataTemplateSelector de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
