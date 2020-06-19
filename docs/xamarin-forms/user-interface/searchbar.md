---
title: Xamarin.FormsBarra
description: Xamarin.FormsBarra es un control de entrada de usuario que se usa para iniciar una búsqueda. El control barra admite texto de marcador de posición, entrada de consulta, ejecución y cancelación. En este artículo se explica cómo usar barra en XAML y en código.
ms.prod: xamarin
ms.assetId: F5EFEA72-CB23-4DD6-9545-D9BB755AF3CB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/04/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d8ceb139b1b9cd77aa922f98c80884d5c3e1a474
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84127548"
---
# <a name="xamarinforms-searchbar"></a>Xamarin.FormsBarra

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)

Xamarin.Forms [`SearchBar`](xref:Xamarin.Forms.SearchBar) Es un control de entrada de usuario que se usa para iniciar una búsqueda. El `SearchBar` control admite el texto de marcador de posición, la entrada de consulta, la ejecución de búsqueda y la cancelación. En la captura de pantalla siguiente se muestra una `SearchBar` consulta con los resultados mostrados en un `ListView` :

[![Captura de pantalla de barra en iOS y Android](searchbar-images/device-searchbars-cropped.png "Barra en iOS y Android")](searchbar-images/device-searchbars.png#lightbox "Barra en iOS y Android")

La `SearchBar` clase define las siguientes propiedades:

* [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor)es un `Color` que define el color del botón Cancelar.
* `CharacterSpacing`, del tipo `double`, es el espaciado entre los caracteres del texto de `SearchBar`.
* [`FontAttributes`](xref:Xamarin.Forms.SearchBar.FontAttributes)es un `FontAttributes` valor de enumeración que determina si la `SearchBar` fuente está en negrita, en cursiva o en ninguna de ellas.
* [`FontFamily`](xref:Xamarin.Forms.SearchBar.FontFamily)es un `string` que determina la familia de fuentes utilizada por `SearchBar` .
* [`FontSize`](xref:Xamarin.Forms.SearchBar.FontSize)puede ser un `NamedSize` valor de enumeración o un `double` valor que represente tamaños de fuente específicos entre plataformas.
* [`HorizontalTextAlignment`](xref:Xamarin.Forms.SearchBar.HorizontalTextAlignment)es un `TextAlignment` valor de enumeración que define la alineación horizontal del texto de la consulta.
* `VerticalTextAlignment`es un `TextAlignment` valor de enumeración que define la alineación vertical del texto de la consulta.
* [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder)es un `string` que define el texto del marcador de posición, como "Buscar...".
* [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor)es un `Color` que define el color del texto del marcador de posición.
* [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand)es un `ICommand` que permite enlazar acciones de usuario, como pulsaciones de dedo o clics, a comandos definidos en un ViewModel.
* [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter)es un `object` que especifica el parámetro que se debe pasar a `SearchCommand` .
* [`Text`](xref:Xamarin.Forms.InputView.Text)es un valor de tipo `string` que contiene el texto de la consulta en `SearchBar` .
* [`TextColor`](xref:Xamarin.Forms.InputView.TextColor)es un `Color` que define el color del texto de la consulta.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que se `SearchBar` puede personalizar y ser el destino de los enlaces de datos. Especificar las propiedades de fuente en `SearchBar` es coherente con la personalización de texto en otros [ Xamarin.Forms controles de texto](~/xamarin-forms/user-interface/text/index.md). Para obtener más información, vea [fuentes Xamarin.Forms en ](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="create-a-searchbar"></a>Creación de un barra

`SearchBar`Se puede crear una instancia de en XAML. Su `Placeholder` propiedad opcional se puede establecer para definir el texto de la sugerencia en el cuadro de entrada de la consulta. El valor predeterminado de `Placeholder` es una cadena vacía, por lo que no aparecerá ningún marcador de posición si no se establece. En el ejemplo siguiente se muestra cómo crear una instancia `SearchBar` de en XAML con el `Placeholder` conjunto de propiedades opcional:

```xaml
<SearchBar Placeholder="Search items..." />
```

`SearchBar`También se puede crear en el código:

```csharp
SearchBar searchBar = new SearchBar{ Placeholder = "Search items..." };
```

### <a name="searchbar-appearance-properties"></a>Propiedades de apariencia de barra

El `SearchBar` control define muchas propiedades que personalizan la apariencia del control. En el ejemplo siguiente se muestra cómo crear una instancia `SearchBar` de en XAML con varias propiedades especificadas:

```xaml
<SearchBar Placeholder="Search items..."
           CancelButtonColor="Orange"
           PlaceholderColor="Orange"
           TextColor="Orange"
           HorizontalTextAlignment="Center"
           FontSize="Medium"
           FontAttributes="Italic" />
```

Estas propiedades también se pueden especificar al crear un `SearchBar` objeto en el código:

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

En la captura de pantalla siguiente se muestra el `SearchBar` control resultante:

[![Captura de pantalla de barra personalizado en iOS y Android](searchbar-images/device-searchbars-styled-cropped.png "Barra personalizado en iOS y Android")](searchbar-images/device-searchbars-styled.png#lightbox "Barra personalizado en iOS y Android")

> [!NOTE]
> En iOS, la `SearchBarRenderer` clase contiene un método reemplazable `UpdateCancelButton` . Este método controla cuándo aparece el botón Cancelar y se puede invalidar en un representador personalizado. Para obtener más información acerca de los representadores personalizados, vea [ Xamarin.Forms representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="perform-a-search-with-event-handlers"></a>Realizar una búsqueda con controladores de eventos

Se puede ejecutar una búsqueda mediante el `SearchBar` control adjuntando un controlador de eventos a uno de los siguientes eventos:

* [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)se llama a cuando el usuario hace clic en el botón buscar o presiona la tecla entrar.
* [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)se llama siempre que se cambia el texto del cuadro consulta.

En el ejemplo siguiente se muestra un controlador de eventos asociado al `TextChanged` evento en XAML y `ListView` se usa para mostrar los resultados de la búsqueda:

```xaml
<SearchBar TextChanged="OnTextChanged" />
<ListView x:Name="searchResults" >
```

También se puede adjuntar un controlador de eventos a un `SearchBar` creado en el código:

```csharp
SearchBar searchBar = new SearchBar {/*...*/};
searchBar.TextChanged += OnTextChanged;
```

El `TextChanged` controlador de eventos del archivo de código subyacente es el mismo, independientemente de que `SearchBar` se cree a través de XAML o código:

```csharp
void OnTextChanged(object sender, EventArgs e)
{
    SearchBar searchBar = (SearchBar)sender;
    searchResults.ItemsSource = DataService.GetSearchResults(searchBar.Text);
}
```

El ejemplo anterior implica la existencia de una `DataService` clase con un `GetSearchResults` método capaz de devolver elementos que coinciden con una consulta. El `SearchBar` valor de la propiedad del control `Text` se pasa al `GetSearchResults` método y el resultado se utiliza para actualizar la `ListView` propiedad del control `ItemsSource` . El efecto general es que los resultados de la búsqueda se muestran en el `ListView` control.

La aplicación de ejemplo proporciona una `DataService` implementación de clase que se puede usar para probar la funcionalidad de búsqueda.

## <a name="perform-a-search-using-a-viewmodel"></a>Realizar una búsqueda con un ViewModel

Se puede ejecutar una búsqueda sin controladores de eventos enlazando las `SearchCommand` `SearchCommandParameter` propiedades y a las `ICommand` implementaciones. En el proyecto de ejemplo se muestran estas implementaciones mediante el patrón Model-View-ViewModel (MVVM). Para obtener más información sobre los enlaces de datos con MVVM, consulte [enlaces de datos con MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

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

En el siguiente código XAML se muestra cómo enlazar un `SearchBar` al modelo de presentación de ejemplo, con un `ListView` control que muestra los resultados de la búsqueda:

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
                  ItemsSource="{Binding SearchResults}" />
    </StackLayout>
</ContentPage>
```

En este ejemplo `BindingContext` se establece para que sea una instancia de la `SearchViewModel` clase. Enlaza la `SearchCommand` propiedad a `PerformSearch` `ICommand` en el ViewModel y enlaza la `SearchBar` `Text` propiedad a la `SearchCommandParameter` propiedad. La `ListView.ItemsSource` propiedad está enlazada a la `SearchResults` propiedad del ViewModel.

Para obtener más información sobre la `ICommand` interfaz y los enlaces, consulte [ Xamarin.Forms enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md) y [la interfaz Icommand](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="related-links"></a>Vínculos relacionados

* [Demostraciones de barra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)
* [Xamarin.FormsControles de texto](~/xamarin-forms/user-interface/text/index.md)
* [Fuentes enXamarin.Forms](~/xamarin-forms/user-interface/text/fonts.md)
* [Xamarin.Formsenlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md)
