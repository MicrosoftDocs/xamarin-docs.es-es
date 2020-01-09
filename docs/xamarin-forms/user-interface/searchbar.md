---
title: Barra de Xamarin. Forms
description: Xamarin. Forms barra es un control de entrada de usuario que se usa para iniciar una búsqueda. El control barra admite texto de marcador de posición, entrada de consulta, ejecución y cancelación. En este artículo se explica cómo usar barra en XAML y en código.
ms.prod: xamarin
ms.assetId: F5EFEA72-CB23-4DD6-9545-D9BB755AF3CB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/04/2019
ms.openlocfilehash: 8888f1615f250a908930cec9058a54bd6e7fedc2
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490082"
---
# <a name="xamarinforms-searchbar"></a>Barra de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)

Xamarin. Forms [`SearchBar`](xref:Xamarin.Forms.SearchBar) es un control de entrada de usuario que se usa para iniciar una búsqueda. El control `SearchBar` admite texto de marcador de posición, entrada de consulta, ejecución de búsqueda y cancelación. En la captura de pantalla siguiente se muestra una consulta de `SearchBar` con los resultados mostrados en un `ListView`:

[![Captura de pantalla de barra en iOS y Android](searchbar-images/device-searchbars-cropped.png "Barra en iOS y Android")](searchbar-images/device-searchbars.png#lightbox "Barra en iOS y Android")

La clase `SearchBar` define las siguientes propiedades:

* [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor) es una `Color` que define el color del botón Cancelar.
* `CharacterSpacing`, del tipo `double`, es el espaciado entre los caracteres del texto de `SearchBar`.
* [`FontAttributes`](xref:Xamarin.Forms.SearchBar.FontAttributes) es un valor de enumeración `FontAttributes` que determina si la fuente `SearchBar` está en negrita, cursiva o ninguna de ellas.
* [`FontFamily`](xref:Xamarin.Forms.SearchBar.FontFamily) es una `string` que determina la familia de fuentes utilizada por la `SearchBar`.
* [`FontSize`](xref:Xamarin.Forms.SearchBar.FontSize) puede ser un valor de enumeración `NamedSize` o un valor `double` que representa tamaños de fuente específicos entre plataformas.
* [`HorizontalTextAlignment`](xref:Xamarin.Forms.SearchBar.HorizontalTextAlignment) es un valor de enumeración `TextAlignment` que define la alineación horizontal del texto de la consulta.
* `VerticalTextAlignment` es un valor de enumeración `TextAlignment` que define la alineación vertical del texto de la consulta.
* [`Placeholder`](xref:Xamarin.Forms.SearchBar.Placeholder) es una `string` que define el texto del marcador de posición, como "Buscar...".
* [`PlaceholderColor`](xref:Xamarin.Forms.SearchBar.PlaceholderColor) es una `Color` que define el color del texto del marcador de posición.
* [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand) es una `ICommand` que permite enlazar acciones de usuario, como pulsaciones de dedo o clics, a comandos definidos en un ViewModel.
* [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) es una `object` que especifica el parámetro que se debe pasar a la `SearchCommand`.
* [`Text`](xref:Xamarin.Forms.SearchBar.Text) es un `string` que contiene el texto de la consulta en el `SearchBar`.
* [`TextColor`](xref:Xamarin.Forms.SearchBar.TextColor) es una `Color` que define el color del texto de la consulta.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que el `SearchBar` se puede personalizar y ser el destino de los enlaces de datos. Especificar las propiedades de fuente en el `SearchBar` es coherente con la personalización de texto en otros [controles de texto de Xamarin. Forms](~/xamarin-forms/user-interface/text/index.md). Para obtener más información, vea [fuentes en Xamarin. Forms](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="create-a-searchbar"></a>Creación de un barra

Se puede crear una instancia de `SearchBar` en XAML. Se puede establecer su propiedad `Placeholder` opcional para definir el texto de la sugerencia en el cuadro de entrada de la consulta. El valor predeterminado del `Placeholder` es una cadena vacía, por lo que no aparecerá ningún marcador de posición si no se establece. En el ejemplo siguiente se muestra cómo crear una instancia de un `SearchBar` en XAML con el conjunto de propiedades de `Placeholder` opcional:

```xaml
<SearchBar Placeholder="Search items..." />
```

También se puede crear un `SearchBar` en el código:

```csharp
SearchBar searchBar = new SearchBar{ Placeholder = "Search items..." };
```

### <a name="searchbar-appearance-properties"></a>Propiedades de apariencia de barra

El control `SearchBar` define muchas propiedades que personalizan la apariencia del control. En el ejemplo siguiente se muestra cómo crear una instancia de un `SearchBar` en XAML con varias propiedades especificadas:

```xaml
<SearchBar Placeholder="Search items..."
           CancelButtonColor="Orange"
           PlaceholderColor="Orange"
           TextColor="Orange"
           HorizontalTextAlignment="Center"
           FontSize="Medium"
           FontAttributes="Italic" />
```

Estas propiedades también se pueden especificar al crear un objeto `SearchBar` en el código:

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

En la captura de pantalla siguiente se muestra el control de `SearchBar` resultante:

[![Captura de pantalla de barra personalizado en iOS y Android](searchbar-images/device-searchbars-styled-cropped.png "Barra personalizado en iOS y Android")](searchbar-images/device-searchbars-styled.png#lightbox "Barra personalizado en iOS y Android")

> [!NOTE]
> En iOS, la clase `SearchBarRenderer` contiene un método de `UpdateCancelButton` reemplazable. Este método controla cuándo aparece el botón Cancelar y se puede invalidar en un representador personalizado. Para obtener más información sobre los representadores personalizados, vea [representadores personalizados de Xamarin. Forms](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="perform-a-search-with-event-handlers"></a>Realizar una búsqueda con controladores de eventos

Se puede ejecutar una búsqueda mediante el control de `SearchBar` adjuntando un controlador de eventos a uno de los siguientes eventos:

* se llama a [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) cuando el usuario hace clic en el botón buscar o presiona la tecla entrar.
* se llama a [`TextChanged`](xref:Xamarin.Forms.SearchBar.TextChanged) cada vez que cambia el texto del cuadro consulta.

En el ejemplo siguiente se muestra un controlador de eventos asociado al evento de `TextChanged` en XAML y se usa un `ListView` para mostrar los resultados de la búsqueda:

```xaml
<SearchBar TextChanged="OnTextChanged" />
<ListView x:Name="searchResults" >
```

También se puede adjuntar un controlador de eventos a un `SearchBar` creado en el código:

```csharp
SearchBar searchBar = new SearchBar {/*...*/};
searchBar.TextChanged += OnTextChanged;
```

El controlador de eventos `TextChanged` del archivo de código subyacente es el mismo, independientemente de que el `SearchBar` se cree a través de XAML o código:

```csharp
void OnTextChanged(object sender, EventArgs e)
{
    SearchBar searchBar = (SearchBar)sender;
    searchResults.ItemsSource = DataService.GetSearchResults(searchBar.Text);
}
```

En el ejemplo anterior se implica la existencia de una clase `DataService` con un método `GetSearchResults` capaz de devolver elementos que coinciden con una consulta. El valor de la propiedad `Text` del control `SearchBar` se pasa al método `GetSearchResults` y el resultado se utiliza para actualizar la propiedad `ItemsSource` del control `ListView`. El efecto general es que los resultados de la búsqueda se muestran en el control `ListView`.

La aplicación de ejemplo proporciona una implementación de clase `DataService` que se puede usar para probar la funcionalidad de búsqueda.

## <a name="perform-a-search-using-a-viewmodel"></a>Realizar una búsqueda con un ViewModel

Se puede ejecutar una búsqueda sin controladores de eventos enlazando las propiedades `SearchCommand` y `SearchCommandParameter` a las implementaciones de `ICommand`. En el proyecto de ejemplo se muestran estas implementaciones mediante el patrón Model-View-ViewModel (MVVM). Para obtener más información sobre los enlaces de datos con MVVM, consulte [enlaces de datos con MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

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
> El ViewModel supone la existencia de una clase `DataService` capaz de realizar búsquedas. La clase `DataService`, incluidos los datos de ejemplo, está disponible en la aplicación de ejemplo.

En el siguiente código XAML se muestra cómo enlazar un `SearchBar` al ViewModel de ejemplo, con un control `ListView` que muestra los resultados de la búsqueda:

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

En este ejemplo se establece el `BindingContext` como una instancia de la clase `SearchViewModel`. Enlaza la propiedad `SearchCommand` al `ICommand` `PerformSearch` en el ViewModel y enlaza la propiedad `Text` `SearchBar` a la propiedad `SearchCommandParameter`. La propiedad `ListView.ItemsSource` se enlaza a la propiedad `SearchResults` de ViewModel.

Para obtener más información sobre la interfaz `ICommand` y los enlaces, consulte [enlace de datos de Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md) y [la interfaz Icommand](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="related-links"></a>Vínculos relacionados

* [Demostraciones de barra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)
* [Controles de texto de Xamarin. Forms](~/xamarin-forms/user-interface/text/index.md)
* [Fuentes de Xamarin. Forms](~/xamarin-forms/user-interface/text/fonts.md)
* [Enlace de datos de Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
