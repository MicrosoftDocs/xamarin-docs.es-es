---
title: Xamarin.Forms CollectionView EmptyView
description: En CollectionView, se puede especificar una vista vacía que proporciona comentarios al usuario cuando no hay datos disponibles para mostrar. La vista vacía puede ser una cadena, una vista o varias vistas.
ms.prod: xamarin
ms.assetid: 6CEBCFE6-5577-4F68-9709-431062609153
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/05/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e689620a943719f769e897676dbd2628e5f1558c
ms.sourcegitcommit: f2942b518f51317acbb263be5bc0c91e66239f50
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2020
ms.locfileid: "94590455"
---
# <a name="xamarinforms-collectionview-emptyview"></a>Xamarin.Forms CollectionView EmptyView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define las siguientes propiedades que se pueden usar para proporcionar comentarios del usuario cuando no hay datos para mostrar:

- [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView), de tipo `object` , la cadena, el enlace o la vista que se mostrarán cuando la [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propiedad sea `null` , o cuando la colección especificada por la `ItemsSource` propiedad sea o esté `null` vacía. El valor predeterminado es `null`.
- [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate), de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , la plantilla que se va a utilizar para dar formato al especificado `EmptyView` . El valor predeterminado es `null`.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos.

Los principales escenarios de uso para establecer la propiedad muestran los [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) comentarios de los usuarios cuando una operación de filtrado en [`CollectionView`](xref:Xamarin.Forms.CollectionView) no produce datos y muestra los comentarios de los usuarios mientras se recuperan los datos de un servicio Web.

> [!NOTE]
> La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad se puede establecer en una vista que incluya contenido interactivo si es necesario.

Para obtener más información sobre las plantillas de datos, consulte [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="display-a-string-when-data-is-unavailable"></a>Mostrar una cadena cuando los datos no estén disponibles

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad se puede establecer en una cadena, que se mostrará cuando la [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propiedad sea `null` o cuando la colección especificada por la `ItemsSource` propiedad sea o esté `null` vacía. En el código XAML siguiente se muestra un ejemplo de este escenario:

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

El resultado es que, dado que la colección enlazada a datos es `null` , se muestra la cadena establecida como el valor de la [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad:

[![Captura de pantalla de una lista de CollectionView vertical con una vista de texto vacía, en iOS y Android](emptyview-images/null-itemssource.png "Lista vertical de CollectionView con vista de texto vacía")](emptyview-images/null-itemssource-large.png#lightbox "Lista vertical de CollectionView con vista de texto vacía")

## <a name="display-views-when-data-is-unavailable"></a>Mostrar vistas cuando los datos no están disponibles

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad se puede establecer en una vista, que se mostrará cuando la [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propiedad sea `null` o cuando la colección especificada por la `ItemsSource` propiedad sea o esté `null` vacía. Puede ser una vista única o una vista que contiene varias vistas secundarias. En el ejemplo de XAML siguiente `EmptyView` se muestra la propiedad establecida en una vista que contiene varias vistas secundarias:

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
            <ContentView>
                <StackLayout HorizontalOptions="CenterAndExpand"
                             VerticalOptions="CenterAndExpand">
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
        </CollectionView.EmptyView>
    </CollectionView>
</StackLayout>
```

En este ejemplo, lo que parece que se ha agregado un redundante es [`ContentView`](xref:Xamarin.Forms) el elemento raíz de [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) . Esto se debe a que internamente, `EmptyView` se agrega a un contenedor nativo que no proporciona ningún contexto para el Xamarin.Forms diseño. Por lo tanto, para colocar las vistas que componen su `EmptyView` , debe agregar un diseño raíz, cuyo elemento secundario es un diseño que puede colocarse en el diseño raíz.

El código de C# equivalente es el siguiente:

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = new ContentView
    {
        Content = new StackLayout
        {
            Children =
            {
                new Label { Text = "No results matched your filter.", ... },
                new Label { Text = "Try a broader filter?", ... }
            }
        }
    }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Cuando [`SearchBar`](xref:Xamarin.Forms.SearchBar) ejecuta `FilterCommand` , la colección que muestra [`CollectionView`](xref:Xamarin.Forms.CollectionView) se filtra para el término de búsqueda almacenado en la [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) propiedad. Si la operación de filtrado no produce datos, [`StackLayout`](xref:Xamarin.Forms.StackLayout) se muestra el conjunto como el valor de la [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad:

[![Captura de pantalla de una lista de CollectionView vertical con vista vacía personalizada, en iOS y Android](emptyview-images/filter-multiple-views.png "Lista vertical de CollectionView con vista vacía personalizada")](emptyview-images/filter-multiple-views-large.png#lightbox "Lista vertical de CollectionView con vista vacía personalizada")

## <a name="display-a-templated-custom-type-when-data-is-unavailable"></a>Mostrar un tipo personalizado con plantilla cuando los datos no estén disponibles

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad se puede establecer en un tipo personalizado, cuya plantilla se muestra cuando la [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propiedad es `null` , o cuando la colección especificada por la `ItemsSource` propiedad es `null` o está vacía. La [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propiedad se puede establecer en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que define la apariencia de `EmptyView` . En el código XAML siguiente se muestra un ejemplo de este escenario:

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

El `FilterData` tipo define una `Filter` propiedad y un correspondiente [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) :

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

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad se establece en un `FilterData` objeto y los datos de la `Filter` propiedad se enlazan a la [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) propiedad. Cuando [`SearchBar`](xref:Xamarin.Forms.SearchBar) ejecuta `FilterCommand` , la colección que muestra [`CollectionView`](xref:Xamarin.Forms.CollectionView) se filtra para el término de búsqueda almacenado en la `Filter` propiedad. Si la operación de filtrado no produce datos, [`Label`](xref:Xamarin.Forms.Label) se muestra el definido en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , que se establece como el valor de la [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propiedad:

[![Captura de pantalla de una lista de CollectionView vertical con una plantilla de vista vacía, en iOS y Android](emptyview-images/emptyviewtemplate.png "Lista de CollectionViews verticales con plantilla de vista vacía")](emptyview-images/emptyviewtemplate-large.png#lightbox "Lista de CollectionViews verticales con plantilla de vista vacía")

> [!NOTE]
> Cuando se muestra un tipo personalizado con plantilla cuando los datos no están disponibles, la [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propiedad se puede establecer en una vista que contiene varias vistas secundarias.

## <a name="choose-an-emptyview-at-runtime"></a>Elegir un EmptyView en tiempo de ejecución

Las vistas que se mostrarán como [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) cuando los datos no estén disponibles, se pueden definir como [`ContentView`](xref:Xamarin.Forms.ContentView) objetos en un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . `EmptyView`A continuación, la propiedad se puede establecer en un específico `ContentView` , en función de alguna lógica de negocios, en tiempo de ejecución. En el código XAML siguiente se muestra un ejemplo de este escenario:

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

Este código XAML define dos [`ContentView`](xref:Xamarin.Forms.ContentView) objetos en el nivel de página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , con el [`Switch`](xref:Xamarin.Forms.Switch) objeto que controla qué `ContentView` objeto se establecerá como el valor de la [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad. Cuando [`Switch`](xref:Xamarin.Forms.Switch) se alterna, el controlador de `OnEmptyViewSwitchToggled` eventos ejecuta el `ToggleEmptyView` método:

```csharp
void ToggleEmptyView(bool isToggled)
{
    collectionView.EmptyView = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
}
```

El `ToggleEmptyView` método establece la [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad del `collectionView` objeto en uno de los dos [`ContentView`](xref:Xamarin.Forms.ContentView) objetos almacenados en [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , basándose en el valor de la [`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) propiedad. Cuando [`SearchBar`](xref:Xamarin.Forms.SearchBar) ejecuta `FilterCommand` , la colección que muestra [`CollectionView`](xref:Xamarin.Forms.CollectionView) se filtra para el término de búsqueda almacenado en la [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) propiedad. Si la operación de filtrado no produce datos, `ContentView` se muestra el objeto establecido como la `EmptyView` propiedad:

[![Captura de pantalla de una lista de CollectionView vertical con vistas vacías intercambiadas, en iOS y Android](emptyview-images/swap.png "Lista de CollectionView vertical con vistas vacías intercambiadas")](emptyview-images/swap-large.png#lightbox "Lista de CollectionView vertical con vistas vacías intercambiadas")

Para obtener más información acerca de los diccionarios de recursos, consulte [ Xamarin.Forms diccionarios de recursos](~/xamarin-forms/xaml/resource-dictionaries.md).

## <a name="choose-an-emptyviewtemplate-at-runtime"></a>Elegir un EmptyViewTemplate en tiempo de ejecución

La apariencia de [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) se puede elegir en tiempo de ejecución, en función de su valor, estableciendo la [`CollectionView.EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propiedad en un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) objeto:

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

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad se establece en la [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) propiedad y la [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propiedad se establece en un `SearchTermDataTemplateSelector` objeto.

Cuando [`SearchBar`](xref:Xamarin.Forms.SearchBar) ejecuta `FilterCommand` , la colección que muestra [`CollectionView`](xref:Xamarin.Forms.CollectionView) se filtra para el término de búsqueda almacenado en la [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) propiedad. Si la operación de filtrado no produce datos, el [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) elegido por el `SearchTermDataTemplateSelector` objeto se establece como la [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propiedad y se muestra.

En el ejemplo siguiente se muestra la `SearchTermDataTemplateSelector` clase:

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

La `SearchTermTemplateSelector` clase define `DefaultTemplate` `OtherTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) las propiedades y que se establecen en distintas plantillas de datos. La `OnSelectTemplate` invalidación devuelve `DefaultTemplate` , que muestra un mensaje al usuario, cuando la consulta de búsqueda no es igual a "Xamarin". Cuando la consulta de búsqueda es igual a "Xamarin", la `OnSelectTemplate` invalidación devuelve `OtherTemplate` , que muestra un mensaje básico al usuario:

[![Captura de pantalla de una selección de plantilla de vista vacía en tiempo de ejecución de CollectionView, en iOS y Android](emptyview-images/datatemplateselector.png "Selección de plantilla de vista vacía en tiempo de ejecución en una colección CollectionView")](emptyview-images/datatemplateselector-large.png#lightbox "Selección de plantilla de vista vacía en tiempo de ejecución en una colección CollectionView")

Para más información sobre los selectores de plantilla de datos, consulte [creación de un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin.Forms Plantillas de datos](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Diccionarios de recursos de Xamarin.Forms](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Creación de un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
