---
title: Barra de Xamarin. Forms
description: Xamarin. Forms barra es un control de entrada de usuario que se usa para iniciar una búsqueda. El control barra admite texto de marcador de posición, entrada de consulta, ejecución y cancelación. En este artículo se explica cómo usar barra en XAML y en código.
ms.prod: xamarin
ms.assetId: F5EFEA72-CB23-4DD6-9545-D9BB755AF3CB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/12/2019
ms.openlocfilehash: 391820cf2e94c1131f4082798ee9efa05d8489b8
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739393"
---
# <a name="xamarinforms-searchbar"></a>Barra de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)

Xamarin. Forms [`SearchBar`](xref:Xamarin.Forms.SearchBar) es un control de entrada de usuario que se usa para iniciar una búsqueda. El `SearchBar` control admite el texto de marcador de posición, la entrada de consulta, la ejecución de búsqueda y la cancelación. En la captura de pantalla `SearchBar` siguiente se muestra una consulta con `ListView`los resultados mostrados en un:

[ ![Captura de pantalla de barra en iOS y Android](searchbar-images/device-searchbars-cropped.png "barra en iOS y Android") ] (searchbar-images/device-searchbars.png#lightbox "Barra en iOS y Android")

`SearchBar` Define las siguientes propiedades:

* [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor)es un `Color` que define el color del botón Cancelar.
* [`FontAttributes`](xref:Xamarin.Forms.SearchBar.FontAttributes)es un `FontAttributes` valor de enumeración que determina `SearchBar` si la fuente está en negrita, en cursiva o en ninguna de ellas.
* [`FontFamily`](xref:Xamarin.Forms.SearchBar.FontFamily)es un `string` que determina la familia de fuentes utilizada `SearchBar`por.
* [`FontSize`](xref:Xamarin.Forms.SearchBar.FontSize)puede ser un `NamedSize` valor de enumeración `double` o un valor que represente tamaños de fuente específicos entre plataformas.
* [`HorizontalTextAlignment`](xref:Xamarin.Forms.SearchBar.HorizontalTextAlignment)es un `TextAlignment` valor de enumeración que define la alineación horizontal del texto de la consulta.
* [`Placeholder`](xref:Xamarin.Forms.SearchBar.Placeholder)es un `string` que define el texto del marcador de posición, como "Buscar...".
* [`PlaceholderColor`](xref:Xamarin.Forms.SearchBar.PlaceholderColor)es un `Color` que define el color del texto del marcador de posición.
* [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand)es un `ICommand` que permite enlazar acciones de usuario, como pulsaciones de dedo o clics, a comandos definidos en un ViewModel.
* [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter)es un `object` que especifica el parámetro que se debe pasar `SearchCommand`a.
* [`Text`](xref:Xamarin.Forms.SearchBar.Text)es un `string` valor de tipo que contiene el `SearchBar`texto de la consulta en.
* [`TextColor`](xref:Xamarin.Forms.SearchBar.TextColor)es un `Color` que define el color del texto de la consulta.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que `SearchBar` significa que se puede personalizar y ser el destino de los enlaces de datos. Especificar las propiedades de fuente en `SearchBar` es coherente con la personalización de texto en otros [controles de texto de Xamarin. Forms](~/xamarin-forms/user-interface/text/index.md). Para obtener más información, vea [fuentes en Xamarin. Forms](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="create-a-searchbar"></a>Creación de un barra

Se `SearchBar` puede crear una instancia de en XAML. Su propiedad `Placeholder` opcional se puede establecer para definir el texto de la sugerencia en el cuadro de entrada de la consulta. El valor predeterminado de `Placeholder` es una cadena vacía, por lo que no aparecerá ningún marcador de posición si no se establece. En el ejemplo siguiente se muestra cómo crear una `SearchBar` instancia de en XAML con `Placeholder` el conjunto de propiedades opcional:

```xaml
<SearchBar Placeholder="Search items..." />
```

También `SearchBar` se puede crear en el código:

```csharp
SearchBar searchBar = new SearchBar{ Placeholder = "Search items..." };
```

### <a name="searchbar-appearance-properties"></a>Propiedades de apariencia de barra

El `SearchBar` control define muchas propiedades que personalizan la apariencia del control. En el ejemplo siguiente se muestra cómo crear una `SearchBar` instancia de en XAML con varias propiedades especificadas:

```xaml
<SearchBar Placeholder="Search items..."
           CancelButtonColor="Orange"
           PlaceholderColor="Orange"
           TextColor="Orange"
           HorizontalTextAlignment="Center"
           FontSize="Medium"
           FontAttributes="Italic" />
```

Estas propiedades también se pueden especificar al crear un `SearchBar` en el código:

```csharp
SearchBar searchBar = new SearchBar
{
    Placeholder = "Search items...",
    PlaceholderColor = Color.Orange,
    TextColor = Color.Orange,
    HorizontalTextAlignment = TextAlignment.Center,
    FontSize = Device.GetNamedSize(NamedSize.Medium, typeof(SearchBar)),
    FontAttributes = FontAttributes.Italic
};
```

En la captura de pantalla siguiente `SearchBar`se muestra el resultado:

[ ![Captura de pantalla de barra personalizado en iOS y Android](searchbar-images/device-searchbars-styled-cropped.png "personalizado barra en iOS y Android") ] (searchbar-images/device-searchbars-styled.png#lightbox "Barra personalizado en iOS y Android")

## <a name="perform-a-search-with-event-handlers"></a>Realizar una búsqueda con controladores de eventos

Se puede ejecutar una búsqueda mediante el `SearchBar` control adjuntando un controlador de eventos a uno de los siguientes eventos:

* [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)se llama a cuando el usuario hace clic en el botón buscar o presiona la tecla entrar.
* [`TextChanged`](xref:Xamarin.Forms.SearchBar.TextChanged)se llama siempre que se cambia el texto del cuadro consulta.

En el ejemplo siguiente `TextChanged` `ListView` se muestra un controlador de eventos asociado al evento en XAML y se usa para mostrar los resultados de la búsqueda:

```xaml
<SearchBar TextChanged="OnTextChanged" />
<ListView x:Name="searchResults" >
```

También se puede adjuntar un controlador de `SearchBar` eventos a un creado en el código:

```csharp
SearchBar searchBar = new SearchBar {/*...*/};
searchBar.TextChanged += OnTextChanged;
```

El `TextChanged` controlador de eventos del archivo de código subyacente es el mismo, `SearchBar` independientemente de que se cree a través de XAML o código:

```csharp
void OnTextChanged(object sender, EventArgs e)
{
    SearchBar searchBar = (SearchBar)sender;
    searchResults.ItemsSource = DataService.GetSearchResults(searchBar.Text);
}
```

El ejemplo anterior implica la existencia de una `DataService` clase con un `GetSearchResults` método capaz de devolver elementos que coinciden con una consulta. El `SearchBar` `Text` `ListView` valorde`ItemsSource` la propiedad del control se pasa al métodoyelresultadoseutilizaparaactualizarlapropiedaddelcontrol.`GetSearchResults` El efecto general es que los resultados de la búsqueda se `ListView` muestran en el control.

La aplicación de ejemplo proporciona `DataService` una implementación de clase que se puede usar para probar la funcionalidad de búsqueda.

## <a name="perform-a-search-using-a-viewmodel"></a>Realizar una búsqueda con un ViewModel

Se puede ejecutar una búsqueda sin controladores de eventos enlazando las `SearchCommand` propiedades `SearchCommandParameter` y a `ICommand` las implementaciones. En el proyecto de ejemplo se muestran estas implementaciones mediante el patrón Model-View-ViewModel (MVVM). Para obtener más información sobre los enlaces de datos con MVVM, consulte [enlaces de datos con MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

El ViewModel en la aplicación de ejemplo contiene el código siguiente:

```csharp
public class SearchViewModel : INotifyPropertyChanged
{
    public event PropertyChangedEventHandler PropertyChanged;

    protected virtual void NotifyPropertyChanged([CallerMemberName] string propertyName = "")
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }

    public ICommand PerformSearch => new Command<string>((string query) =>
    {
        SearchResults = DataService.GetSearchResults(query);
    });

    private List<string> searchResults = DataService.Fruits;
    public List<string> SearchResults
    {
        get
        {
            return searchResults;
        }
        set
        {
            searchResults = value;
            NotifyPropertyChanged();
        }
    }
}
```

> [!NOTE]
> El ViewModel supone la existencia de una `DataService` clase capaz de realizar búsquedas. La `DataService` clase, incluidos los datos de ejemplo, está disponible en la aplicación de ejemplo.

En el siguiente código XAML se muestra cómo `SearchBar` enlazar un al modelo de presentación `ListView` de ejemplo, con un control que muestra los resultados de la búsqueda:

```xaml
<ContentPage ...>
    <ContentPage.BindingContext>
        <viewmodels:SearchViewModel />
    </ContentPage.BindingContext>
    <StackLayout ...>
        <SearchBar x:Name="searchBar"
                   ...
                   SearchCommand="{Binding PerformSearch}"
                   SearchCommandParameter="{Binding Text, Source={x:Reference searchBar}}"/>
        <ListView x:Name="searchResults"
                  ...
                  ItemsSource="{Binding SearchResults} />
    </StackLayout>
</ContentPage>
```

`BindingContext` En este ejemplo se establece para que sea una instancia `SearchViewModel` de la clase. Enlaza `SearchCommand` la `SearchCommandParameter` propiedad `PerformSearch` `SearchBar` a en el ViewModel y enlaza la `Text` propiedad a la propiedad. `ICommand` La `ListView.ItemsSource` propiedad está enlazada a `SearchResults` la propiedad del ViewModel.

Para obtener más información sobre `ICommand` la interfaz y los enlaces, consulte [enlace de datos de Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md) y [la interfaz Icommand](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="related-links"></a>Vínculos relacionados

* [Demostraciones de barra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)
* [Controles de texto de Xamarin. Forms](~/xamarin-forms/user-interface/text/index.md)
* [Fuentes de Xamarin. Forms](~/xamarin-forms/user-interface/text/fonts.md)
* [Enlace de datos de Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
