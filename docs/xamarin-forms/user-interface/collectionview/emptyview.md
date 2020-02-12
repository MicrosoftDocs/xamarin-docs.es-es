---
title: EmptyView de Xamarin. Forms CollectionView
description: En CollectionView, se puede especificar una vista vacía que proporciona comentarios al usuario cuando no hay datos disponibles para mostrar. La vista vacía puede ser una cadena, una vista o varias vistas.
ms.prod: xamarin
ms.assetid: 6CEBCFE6-5577-4F68-9709-431062609153
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: f58f160594d21b1cf8826017f19ad31beb8eeda1
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/11/2020
ms.locfileid: "77130981"
---
# <a name="xamarinforms-collectionview-emptyview"></a>EmptyView de Xamarin. Forms CollectionView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define las siguientes propiedades que se pueden usar para proporcionar comentarios del usuario cuando no hay datos para mostrar:

- [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView), de tipo `object`, cadena, enlace o vista que se mostrarán cuando se `null`la propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) , o cuando la colección especificada por la propiedad `ItemsSource` es `null` o está vacía. El valor predeterminado es `null`.
- [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate), de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), plantilla que se va a utilizar para dar formato al `EmptyView`especificado. El valor predeterminado es `null`.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que las propiedades pueden ser destinos de enlaces de datos.

Los principales escenarios de uso para establecer la propiedad [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) muestran los comentarios de los usuarios cuando una operación de filtrado en un [`CollectionView`](xref:Xamarin.Forms.CollectionView) no produce datos y muestra los comentarios de los usuarios mientras se recuperan los datos de un servicio Web.

> [!NOTE]
> La propiedad [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) se puede establecer en una vista que incluya contenido interactivo si es necesario.

Para obtener más información sobre las plantillas de datos, consulte [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="display-a-string-when-data-is-unavailable"></a>Mostrar una cadena cuando los datos no estén disponibles

La propiedad [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) se puede establecer en una cadena, que se mostrará cuando se `null`la propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) , o cuando la colección especificada por la propiedad `ItemsSource` es `null` o está vacía. En el código XAML siguiente se muestra un ejemplo de este escenario:

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

El resultado es que, dado que la colección enlazada a datos es `null`, se muestra la cadena establecida como el valor de la propiedad [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) :

[![Captura de pantalla de una lista de CollectionView vertical con una vista de texto vacía, en iOS y Android](emptyview-images/null-itemssource.png "Lista vertical de CollectionView con vista de texto vacía")](emptyview-images/null-itemssource-large.png#lightbox "Lista vertical de CollectionView con vista de texto vacía")

## <a name="display-views-when-data-is-unavailable"></a>Mostrar vistas cuando los datos no están disponibles

La propiedad [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) se puede establecer en una vista, que se mostrará cuando se `null`la propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) o cuando la colección especificada por la propiedad `ItemsSource` es `null` o está vacía. Puede ser una vista única o una vista que contiene varias vistas secundarias. En el ejemplo de XAML siguiente se muestra la propiedad `EmptyView` establecida en una vista que contiene varias vistas secundarias:

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

Cuando el [`SearchBar`](xref:Xamarin.Forms.SearchBar) ejecuta el `FilterCommand`, la colección mostrada por el [`CollectionView`](xref:Xamarin.Forms.CollectionView) se filtra para el término de búsqueda almacenado en la propiedad [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) . Si la operación de filtrado no produce datos, se muestra la [`StackLayout`](xref:Xamarin.Forms.StackLayout) establecida como el valor de la propiedad [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) :

[![Captura de pantalla de una lista de CollectionView vertical con vista vacía personalizada, en iOS y Android](emptyview-images/filter-multiple-views.png "Lista vertical de CollectionView con vista vacía personalizada")](emptyview-images/filter-multiple-views-large.png#lightbox "Lista vertical de CollectionView con vista vacía personalizada")

## <a name="display-a-templated-custom-type-when-data-is-unavailable"></a>Mostrar un tipo personalizado con plantilla cuando los datos no estén disponibles

La propiedad [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) se puede establecer en un tipo personalizado, cuya plantilla se muestra cuando se `null`la propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) , o cuando la colección especificada por la propiedad `ItemsSource` es `null` o está vacía. La propiedad [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) se puede establecer en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que define la apariencia de la `EmptyView`. En el código XAML siguiente se muestra un ejemplo de este escenario:

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

El tipo de `FilterData` define una propiedad `Filter` y una [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)correspondiente:

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

La propiedad [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) se establece en un objeto `FilterData` y los datos de la propiedad `Filter` se enlazan a la propiedad [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) . Cuando el [`SearchBar`](xref:Xamarin.Forms.SearchBar) ejecuta el `FilterCommand`, la colección mostrada por el [`CollectionView`](xref:Xamarin.Forms.CollectionView) se filtra para el término de búsqueda almacenado en la propiedad `Filter`. Si la operación de filtrado no produce datos, se muestra el [`Label`](xref:Xamarin.Forms.Label) definido en el [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que se establece como el valor de la propiedad [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) :

[![Captura de pantalla de una lista de CollectionView vertical con una plantilla de vista vacía, en iOS y Android](emptyview-images/emptyviewtemplate.png "Lista de CollectionViews verticales con plantilla de vista vacía")](emptyview-images/emptyviewtemplate-large.png#lightbox "Lista de CollectionViews verticales con plantilla de vista vacía")

> [!NOTE]
> Cuando se muestra un tipo personalizado con plantilla cuando los datos no están disponibles, la propiedad [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) se puede establecer en una vista que contiene varias vistas secundarias.

## <a name="choose-an-emptyview-at-runtime"></a>Elegir un EmptyView en tiempo de ejecución

Las vistas que se mostrarán como un [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) cuando los datos no estén disponibles, se pueden definir como objetos [`ContentView`](xref:Xamarin.Forms.ContentView) en un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). A continuación, la propiedad `EmptyView` se puede establecer en un `ContentView`específico, en función de alguna lógica de negocios, en tiempo de ejecución. En el ejemplo de XAML siguiente se muestra un ejemplo de este escenario:

```xaml
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

Este código XAML define dos objetos [`ContentView`](xref:Xamarin.Forms.ContentView) en el [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de nivel de página, con el objeto [`Switch`](xref:Xamarin.Forms.Switch) que controla qué objeto `ContentView` se establecerá como el valor de la propiedad [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) . Cuando se alterna el [`Switch`](xref:Xamarin.Forms.Switch) , el controlador de eventos `OnEmptyViewSwitchToggled` ejecuta el método `ToggleEmptyView`:

```csharp
void ToggleEmptyView(bool isToggled)
{
    collectionView.EmptyView = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
}
```

El método `ToggleEmptyView` establece la propiedad [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) del objeto `collectionView` en uno de los dos objetos [`ContentView`](xref:Xamarin.Forms.ContentView) almacenados en la [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), en función del valor de la propiedad [`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) . Cuando el [`SearchBar`](xref:Xamarin.Forms.SearchBar) ejecuta el `FilterCommand`, la colección mostrada por el [`CollectionView`](xref:Xamarin.Forms.CollectionView) se filtra para el término de búsqueda almacenado en la propiedad [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) . Si la operación de filtrado no produce datos, se muestra el objeto `ContentView` establecido como la propiedad `EmptyView`:

[![Captura de pantalla de una lista de CollectionView vertical con vistas vacías intercambiadas, en iOS y Android](emptyview-images/swap.png "Lista de CollectionView vertical con vistas vacías intercambiadas")](emptyview-images/swap-large.png#lightbox "Lista de CollectionView vertical con vistas vacías intercambiadas")

Para obtener más información sobre los diccionarios de recursos, consulte [diccionarios de recursos de Xamarin. Forms](~/xamarin-forms/xaml/resource-dictionaries.md).

## <a name="choose-an-emptyviewtemplate-at-runtime"></a>Elegir un EmptyViewTemplate en tiempo de ejecución

La apariencia del [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) se puede elegir en tiempo de ejecución, en función de su valor, estableciendo la propiedad [`CollectionView.EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) en un objeto [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) :

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

La propiedad [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) está establecida en la propiedad [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) y la propiedad [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) está establecida en un objeto `SearchTermDataTemplateSelector`.

Cuando el [`SearchBar`](xref:Xamarin.Forms.SearchBar) ejecuta el `FilterCommand`, la colección mostrada por el [`CollectionView`](xref:Xamarin.Forms.CollectionView) se filtra para el término de búsqueda almacenado en la propiedad [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) . Si la operación de filtrado no produce datos, los [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) elegidos por el objeto `SearchTermDataTemplateSelector` se establecen como la propiedad [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) y se muestran.

En el ejemplo siguiente se muestra la clase `SearchTermDataTemplateSelector`:

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

La clase `SearchTermTemplateSelector` define `DefaultTemplate` y `OtherTemplate` propiedades de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que se establecen en distintas plantillas de datos. La invalidación `OnSelectTemplate` devuelve `DefaultTemplate`, que muestra un mensaje al usuario, cuando la consulta de búsqueda no es igual a "Xamarin". Cuando la consulta de búsqueda es igual a "Xamarin", la invalidación de `OnSelectTemplate` devuelve `OtherTemplate`, que muestra un mensaje básico al usuario:

[![Captura de pantalla de una selección de plantilla de vista vacía en tiempo de ejecución de CollectionView, en iOS y Android](emptyview-images/datatemplateselector.png "Selección de plantilla de vista vacía en tiempo de ejecución en una colección CollectionView")](emptyview-images/datatemplateselector-large.png#lightbox "Selección de plantilla de vista vacía en tiempo de ejecución en una colección CollectionView")

Para obtener más información sobre los selectores de plantilla de datos, vea [Create a Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Plantillas de datos de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Diccionarios de recursos de Xamarin.Forms](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Creación de un DataTemplateSelector de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
