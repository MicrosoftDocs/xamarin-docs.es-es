---
title: Xamarin. Forms CarouselView EmptyView
description: En CarouselView, se puede especificar una vista vacía que proporciona comentarios al usuario cuando no hay datos disponibles para mostrar. La vista vacía puede ser una cadena, una vista o varias vistas.
ms.prod: xamarin
ms.assetid: C6DEE1A9-63FC-4889-BC77-F401D5D7DF32
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/03/2019
ms.openlocfilehash: 55944b422495c9c3a7c93c6a2eab90a2db790780
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697846"
---
# <a name="xamarinforms-carouselview-emptyview"></a>Xamarin. Forms CarouselView EmptyView

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define las siguientes propiedades que se pueden usar para proporcionar comentarios del usuario cuando no hay datos para mostrar:

- [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView), de tipo `object`, cadena, enlace o vista que se mostrarán cuando se `null` la propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) , o cuando la colección especificada por la propiedad `ItemsSource` es `null` o está vacía. El valor predeterminado es `null`.
- [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate), de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), plantilla que se va a utilizar para dar formato al `EmptyView` especificado. El valor predeterminado es `null`.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que las propiedades pueden ser destinos de enlaces de datos.

Los principales escenarios de uso para establecer la propiedad [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) muestran los comentarios de los usuarios cuando una operación de filtrado en un [`CarouselView`](xref:Xamarin.Forms.CarouselView) no produce datos y muestra los comentarios de los usuarios mientras se recuperan los datos de un servicio Web.

> [!NOTE]
> La propiedad [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) se puede establecer en una vista que incluya contenido interactivo si es necesario.

Para obtener más información sobre las plantillas de datos, consulte [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="display-a-string-when-data-is-unavailable"></a>Mostrar una cadena cuando los datos no estén disponibles

La propiedad [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) se puede establecer en una cadena, que se mostrará cuando se `null` la propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) , o cuando la colección especificada por la propiedad `ItemsSource` es `null` o está vacía. En el código XAML siguiente se muestra un ejemplo de este escenario:

```xaml
<CarouselView ItemsSource="{Binding EmptyMonkeys}"
              EmptyView="No items to display." />
```

El código de C# equivalente es el siguiente:

```csharp
CarouselView carouselView = new CarouselView
{
    EmptyView = "No items to display."
};
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "EmptyMonkeys");
```

El resultado es que, dado que la colección enlazada a datos es `null`, se muestra la cadena establecida como el valor de la propiedad [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) .

## <a name="display-views-when-data-is-unavailable"></a>Mostrar vistas cuando los datos no están disponibles

La propiedad [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) se puede establecer en una vista, que se mostrará cuando se `null` la propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) o cuando la colección especificada por la propiedad `ItemsSource` es `null` o está vacía. Puede ser una vista única o una vista que contiene varias vistas secundarias. En el ejemplo de XAML siguiente se muestra la propiedad `EmptyView` establecida en una vista que contiene varias vistas secundarias:

```xaml
<StackLayout Margin="20">
    <SearchBar SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={RelativeSource Self}, Path=Text}"
               Placeholder="Filter" />
    <CarouselView ItemsSource="{Binding Monkeys}">
        <CarouselView.EmptyView>
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
        </CarouselView.EmptyView>
        <CarouselView.ItemTemplate>
            ...
        </CarouselView.ItemTemplate>
    </CarouselView>
</StackLayout>
```

El código de C# equivalente es el siguiente:

```csharp
SearchBar searchBar = new SearchBar { ... };
CarouselView carouselView = new CarouselView
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
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Cuando el [`SearchBar`](xref:Xamarin.Forms.SearchBar) ejecuta el `FilterCommand`, la colección mostrada por el [`CarouselView`](xref:Xamarin.Forms.CarouselView) se filtra para el término de búsqueda almacenado en la propiedad [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) . Si la operación de filtrado no produce datos, se muestra la [`StackLayout`](xref:Xamarin.Forms.StackLayout) establecida como el valor de la propiedad [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) .

## <a name="display-a-templated-custom-type-when-data-is-unavailable"></a>Mostrar un tipo personalizado con plantilla cuando los datos no estén disponibles

La propiedad [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) se puede establecer en un tipo personalizado, cuya plantilla se muestra cuando se `null` la propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) , o cuando la colección especificada por la propiedad `ItemsSource` es `null` o está vacía. La propiedad [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) se puede establecer en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que define la apariencia de la `EmptyView`. En el código XAML siguiente se muestra un ejemplo de este escenario:

```xaml
<StackLayout Margin="20">
    <SearchBar x:Name="searchBar"
               SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={RelativeSource Self}, Path=Text}"
               Placeholder="Filter" />
    <CarouselView ItemsSource="{Binding Monkeys}">
        <CarouselView.EmptyView>
            <controls:FilterData Filter="{Binding Source={x:Reference searchBar}, Path=Text}" />
        </CarouselView.EmptyView>
        <CarouselView.EmptyViewTemplate>
            <DataTemplate>
                <Label Text="{Binding Filter, StringFormat='Your filter term of {0} did not match any records.'}"
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </DataTemplate>
        </CarouselView.EmptyViewTemplate>
        <CarouselView.ItemTemplate>
            ...
        </CarouselView.ItemTemplate>
    </CarouselView>
</StackLayout>
```

El código de C# equivalente es el siguiente:

```csharp
SearchBar searchBar = new SearchBar { ... };
CarouselView carouselView = new CarouselView
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

La propiedad [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) se establece en un objeto `FilterData` y los datos de la propiedad `Filter` se enlazan a la propiedad [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) . Cuando el [`SearchBar`](xref:Xamarin.Forms.SearchBar) ejecuta el `FilterCommand`, la colección mostrada por el [`CarouselView`](xref:Xamarin.Forms.CarouselView) se filtra para el término de búsqueda almacenado en la propiedad `Filter`. Si la operación de filtrado no produce datos, se muestra el [`Label`](xref:Xamarin.Forms.Label) definido en el [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que se establece como el valor de la propiedad [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) .

> [!NOTE]
> Cuando se muestra un tipo personalizado con plantilla cuando los datos no están disponibles, la propiedad [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) se puede establecer en una vista que contiene varias vistas secundarias.

## <a name="choose-an-emptyview-at-runtime"></a>Elegir un EmptyView en tiempo de ejecución

Las vistas que se mostrarán como un [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) cuando los datos no estén disponibles, se pueden definir como objetos [`ContentView`](xref:Xamarin.Forms.ContentView) en un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). A continuación, la propiedad `EmptyView` se puede establecer en un `ContentView` específico, en función de alguna lógica de negocios, en tiempo de ejecución. En el ejemplo de XAML siguiente se muestra un ejemplo de este escenario:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:viewmodels="clr-namespace:CarouselViewDemos.ViewModels"
             x:Class="CarouselViewDemos.Views.EmptyViewSwapPage"
             Title="EmptyView (swap)">
    <ContentPage.BindingContext>
        <viewmodels:MonkeysViewModel />
    </ContentPage.BindingContext>
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
        <SearchBar SearchCommand="{Binding FilterCommand}"
                   SearchCommandParameter="{Binding Source={RelativeSource Self}, Path=Text}"
                   Placeholder="Filter" />
        <StackLayout Orientation="Horizontal">
            <Label Text="Toggle EmptyViews" />
            <Switch Toggled="OnEmptyViewSwitchToggled" />
        </StackLayout>
        <CarouselView x:Name="carouselView"
                      ItemsSource="{Binding Monkeys}">
            <CarouselView.ItemTemplate>
                ...
            </CarouselView.ItemTemplate>
        </CarouselView>
    </StackLayout>
</ContentPage>
```

Este código XAML define dos objetos [`ContentView`](xref:Xamarin.Forms.ContentView) en el [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de nivel de página, con el objeto [`Switch`](xref:Xamarin.Forms.Switch) que controla qué objeto `ContentView` se establecerá como el valor de la propiedad [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) . Cuando se alterna el [`Switch`](xref:Xamarin.Forms.Switch) , el controlador de eventos `OnEmptyViewSwitchToggled` ejecuta el método `ToggleEmptyView`:

```csharp
void ToggleEmptyView(bool isToggled)
{
    carouselView.EmptyView = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
}
```

El método `ToggleEmptyView` establece la propiedad [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) del objeto `carouselView` en uno de los dos objetos [`ContentView`](xref:Xamarin.Forms.ContentView) almacenados en la [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), en función del valor de la propiedad [`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) . Cuando el [`SearchBar`](xref:Xamarin.Forms.SearchBar) ejecuta el `FilterCommand`, la colección mostrada por el [`CarouselView`](xref:Xamarin.Forms.CarouselView) se filtra para el término de búsqueda almacenado en la propiedad [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) . Si la operación de filtrado no produce datos, se muestra el objeto `ContentView` establecido como la propiedad `EmptyView`.

Para obtener más información sobre los diccionarios de recursos, consulte [diccionarios de recursos de Xamarin. Forms](~/xamarin-forms/xaml/resource-dictionaries.md).

## <a name="choose-an-emptyviewtemplate-at-runtime"></a>Elegir un EmptyViewTemplate en tiempo de ejecución

La apariencia del [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) se puede elegir en tiempo de ejecución, en función de su valor, estableciendo la propiedad [`CarouselView.EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) en un objeto [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) :

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CarouselViewDemos.Controls">
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
                   SearchCommandParameter="{Binding Source={RelativeSource Self}, Path=Text}"
                   Placeholder="Filter" />
        <CarouselView ItemsSource="{Binding Monkeys}"
                      EmptyView="{Binding Source={x:Reference searchBar}, Path=Text}"
                      EmptyViewTemplate="{StaticResource SearchSelector}">
            <CarouselView.ItemTemplate>
                ...
            </CarouselView.ItemTemplate>
        </CarouselView>
    </StackLayout>
</ContentPage>
```

El código de C# equivalente es el siguiente:

```csharp
SearchBar searchBar = new SearchBar { ... };
CarouselView carouselView = new CarouselView()
{
    EmptyView = searchBar.Text,
    EmptyViewTemplate = new SearchTermDataTemplateSelector { ... }
};
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

La propiedad [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) está establecida en la propiedad [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) y la propiedad [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) está establecida en un objeto `SearchTermDataTemplateSelector`.

Cuando el [`SearchBar`](xref:Xamarin.Forms.SearchBar) ejecuta el `FilterCommand`, la colección mostrada por el [`CarouselView`](xref:Xamarin.Forms.CarouselView) se filtra para el término de búsqueda almacenado en la propiedad [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) . Si la operación de filtrado no produce datos, los [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) elegidos por el objeto `SearchTermDataTemplateSelector` se establecen como la propiedad [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) y se muestran.

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

La clase `SearchTermTemplateSelector` define `DefaultTemplate` y `OtherTemplate` propiedades de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que se establecen en distintas plantillas de datos. La invalidación `OnSelectTemplate` devuelve `DefaultTemplate`, que muestra un mensaje al usuario, cuando la consulta de búsqueda no es igual a "Xamarin". Cuando la consulta de búsqueda es igual a "Xamarin", la invalidación de `OnSelectTemplate` devuelve `OtherTemplate`, que muestra un mensaje básico al usuario.

Para obtener más información sobre los selectores de plantilla de datos, vea [Create a Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Vínculos relacionados

- [CarouselView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Plantillas de datos de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Diccionarios de recursos de Xamarin. Forms](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Creación de un DataTemplateSelector de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
