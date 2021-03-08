---
title: Búsqueda en Xamarin.Forms Shell
description: Las aplicaciones de Xamarin.Forms Shell pueden usar la funcionalidad de búsqueda integrada que se proporciona en un cuadro de búsqueda que se puede agregar en la parte superior de cada página.
ms.prod: xamarin
ms.assetid: F8F9471D-6771-4D23-96C0-2B79473A06D4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cae7b1e86db6dfceb04c8298c116b6e7de32e5a1
ms.sourcegitcommit: 1b542afc0f6f2f6adbced527ae47b9ac90eaa1de
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/03/2021
ms.locfileid: "101758121"
---
# <a name="xamarinforms-shell-search"></a>Búsqueda en Xamarin.Forms Shell

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.Forms Shell incluye la funcionalidad de búsqueda integrada que proporciona la clase [`SearchHandler`](xref:Xamarin.Forms.SearchHandler). Se puede agregar funcionalidad de búsqueda a una página estableciendo la propiedad adjunta [`Shell.SearchHandler`](xref:Xamarin.Forms.SearchHandler) en un objeto `SearchHandler` con subclases. El resultado es un cuadro de búsqueda que se agrega en la parte superior de la página:

[![Captura de pantalla de una clase SearchHandler de Shell en iOS y Android](search-images/searchhandler.png)](search-images/searchhandler-large.png#lightbox)

Cuando se escribe una consulta en el cuadro de búsqueda, la propiedad [`Query`](xref:Xamarin.Forms.SearchHandler.Query) se actualiza y, en cada actualización, se ejecuta el método [`OnQueryChanged`](xref:Xamarin.Forms.SearchHandler.OnQueryChanged*). Este método se puede invalidar para rellenar el área de sugerencias de búsqueda con datos:

[![Captura de pantalla de resultados de la búsqueda en una clase SearchHandler de Shell, en iOS y Android](search-images/search-suggestions.png)](search-images/search-suggestions-large.png#lightbox)

Luego, cuando se seleccione un resultado del área de sugerencias de búsqueda, se ejecutará el método [`OnItemSelected`](xref:Xamarin.Forms.SearchHandler.OnItemSelected*). Este método se puede invalidar para responder de forma adecuada; por ejemplo, navegando a una página de detalles.

## <a name="create-a-searchhandler"></a>Creación de una clase SearchHandler

La funcionalidad de búsqueda se puede agregar a una aplicación de Shell mediante la creación de subclases de la clase [`SearchHandler`](xref:Xamarin.Forms.SearchHandler) y la invalidación de los métodos [`OnQueryChanged`](xref:Xamarin.Forms.SearchHandler.OnQueryChanged*) y [`OnItemSelected`](xref:Xamarin.Forms.SearchHandler.OnItemSelected*):

```csharp
public class AnimalSearchHandler : SearchHandler
{
    public IList<Animal> Animals { get; set; }
    public Type SelectedItemNavigationTarget { get; set; }

    protected override void OnQueryChanged(string oldValue, string newValue)
    {
        base.OnQueryChanged(oldValue, newValue);

        if (string.IsNullOrWhiteSpace(newValue))
        {
            ItemsSource = null;
        }
        else
        {
            ItemsSource = Animals
                .Where(animal => animal.Name.ToLower().Contains(newValue.ToLower()))
                .ToList<Animal>();
        }
    }

    protected override async void OnItemSelected(object item)
    {
        base.OnItemSelected(item);

        // Let the animation complete
        await Task.Delay(1000);

        ShellNavigationState state = (App.Current.MainPage as Shell).CurrentState;
        // The following route works because route names are unique in this application.
        await Shell.Current.GoToAsync($"{GetNavigationTarget()}?name={((Animal)item).Name}");
    }

    string GetNavigationTarget()
    {
        return (Shell.Current as AppShell).Routes.FirstOrDefault(route => route.Value.Equals(SelectedItemNavigationTarget)).Key;
    }
}
```

La invalidación [`OnQueryChanged`](xref:Xamarin.Forms.SearchHandler.OnQueryChanged*) tiene dos argumentos: `oldValue`, que contiene la consulta de búsqueda anterior, y `newValue`, que contiene la consulta de búsqueda actual. El área de sugerencias de búsqueda se puede actualizar mediante el establecimiento de la propiedad [`SearchHandler.ItemsSource`](xref:Xamarin.Forms.SearchHandler.ItemsSource) en una colección `IEnumerable` que contiene elementos que coinciden con la consulta de búsqueda actual.

Cuando el usuario selecciona un resultado de búsqueda, se ejecuta la invalidación [`OnItemSelected`](xref:Xamarin.Forms.SearchHandler.OnItemSelected*) y se establece la propiedad [`SelectedItem`](xref:Xamarin.Forms.SearchHandler.SelectedItem). En este ejemplo, el método dirige a otra página que muestra datos sobre el elemento `Animal` seleccionado. Para obtener más información sobre la navegación, consulte [Navegación en Xamarin.Forms Shell](navigation.md).

> [!NOTE]
> Se pueden establecer propiedades [`SearchHandler`](xref:Xamarin.Forms.SearchHandler) adicionales para controlar la apariencia del cuadro de búsqueda.

## <a name="consume-a-searchhandler"></a>Consumo de una clase SearchHandler

La clase [`SearchHandler`](xref:Xamarin.Forms.SearchHandler) con subclases se puede consumir mediante el establecimiento de la propiedad adjunta [`Shell.SearchHandler`](xref:Xamarin.Forms.SearchHandler) en un objeto del tipo con subclases, en la página de consumo:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">
    <Shell.SearchHandler>
        <controls:AnimalSearchHandler Placeholder="Enter search term"
                                      ShowsResults="true"
                                      DisplayMemberName="Name" />
    </Shell.SearchHandler>
    ...
</ContentPage>
```

El código de C# equivalente es el siguiente:

```csharp
Shell.SetSearchHandler(this, new AnimalSearchHandler
{
    Placeholder = "Enter search term",
    ShowsResults = true,
    DisplayMemberName = "Name"
});
```

El método `AnimalSearchHandler.OnQueryChanged` devuelve un elemento `List` de objetos `Animal`. La propiedad [`DisplayMemberName`](xref:Xamarin.Forms.SearchHandler.DisplayMemberName) se establece en la propiedad `Name` de cada objeto `Animal`, por lo que los datos mostrados en el área de sugerencias serán el nombre de cada animal.

La propiedad [`ShowsResults`](xref:Xamarin.Forms.SearchHandler.ShowsResults) está establecida en `true`, de modo que se muestran sugerencias de búsqueda cuando el usuario escribe una consulta de búsqueda:

[![Captura de pantalla de resultados de la búsqueda en una clase SearchHandler de Shell, en iOS y Android, con resultados para la cadena parcial M.](search-images/search-results.png)](search-images/search-results-large.png#lightbox)

A medida que cambia la consulta de búsqueda, se actualiza el área de sugerencias de búsqueda:

[![Captura de pantalla de resultados de la búsqueda en una clase SearchHandler de Shell, en iOS y Android, con resultados para la cadena parcial M o n.](search-images/search-results-change.png)](search-images/search-results-change-large.png#lightbox)

Cuando se selecciona un resultado de la búsqueda, se navega a `MonkeyDetailPage` y se muestra una página de detalles del mono seleccionado:

[![Captura de pantalla de detalles de mono en iOS y Android](search-images/detailpage.png)](search-images/detailpage-large.png#lightbox)

## <a name="define-search-results-item-appearance"></a>Definición de la apariencia de los elemento de los resultados de búsqueda

Además de mostrar datos de `string` en los resultados de la búsqueda, se puede definir la apariencia de cada uno de los elementos de los resultados de la búsqueda mediante el establecimiento de la propiedad [ `SearchHandler.ItemTemplate`](xref:Xamarin.Forms.SearchHandler.ItemTemplate) en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">    
    <Shell.SearchHandler>
        <controls:AnimalSearchHandler Placeholder="Enter search term"
                                      ShowsResults="true">
            <controls:AnimalSearchHandler.ItemTemplate>
                <DataTemplate>
                    <Grid Padding="10"
                          ColumnDefinitions="0.15*,0.85*">
                        <Image Source="{Binding ImageUrl}"
                               HeightRequest="40"
                               WidthRequest="40" />
                        <Label Grid.Column="1"
                               Text="{Binding Name}"
                               FontAttributes="Bold"
                               VerticalOptions="Center" />
                    </Grid>
                </DataTemplate>
            </controls:AnimalSearchHandler.ItemTemplate>
       </controls:AnimalSearchHandler>
    </Shell.SearchHandler>
    ...
</ContentPage>
```

El código de C# equivalente es el siguiente:

```csharp
Shell.SetSearchHandler(this, new AnimalSearchHandler
{
    Placeholder = "Enter search term",
    ShowsResults = true,
    ItemTemplate = new DataTemplate(() =>
    {
        Grid grid = new Grid { Padding = 10 };
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(0.15, GridUnitType.Star) });
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(0.85, GridUnitType.Star) });

        Image image = new Image { HeightRequest = 40, WidthRequest = 40 };
        image.SetBinding(Image.SourceProperty, "ImageUrl");
        Label nameLabel = new Label { FontAttributes = FontAttributes.Bold, VerticalOptions = LayoutOptions.Center };
        nameLabel.SetBinding(Label.TextProperty, "Name");

        grid.Children.Add(image);
        grid.Children.Add(nameLabel, 1, 0);
        return grid;
    })
});
```

Los elementos especificados en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definen la apariencia de cada elemento en el área de sugerencias. En este ejemplo, el diseño dentro de `DataTemplate` se administra mediante un objeto [`Grid`](xref:Xamarin.Forms.Grid). El objeto `Grid` contiene un objeto [`Image`](xref:Xamarin.Forms.Image) y un objeto [`Label`](xref:Xamarin.Forms.Label), que enlazan ambos con las propiedades de cada objeto `Monkey`.

Las capturas de pantalla siguientes muestran el resultado de crear plantillas para cada elemento del área de sugerencias:

[![Captura de pantalla de resultados de la búsqueda con plantilla en una clase SearchHandler de Shell, en iOS y Android](search-images/search-results-template.png)](search-images/search-results-template-large.png#lightbox)

Para obtener más información sobre las plantillas de datos, consulte [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="search-box-visibility"></a>Visibilidad del cuadro de búsqueda

Cuando se agrega un objeto [`SearchHandler`](xref:Xamarin.Forms.SearchHandler) en la parte superior de una página, el cuadro de búsqueda está visible y totalmente expandido de forma predeterminada. Pero este comportamiento se puede cambiar estableciendo la propiedad [`SearchHandler.SearchBoxVisibility`](xref:Xamarin.Forms.SearchHandler.SearchBoxVisibility) en uno de los miembros de la enumeración [`SearchBoxVisibility`](xref:Xamarin.Forms.SearchBoxVisibility):

- `Hidden`: el cuadro de búsqueda no es visible ni accesible.
- `Collapsible`: el cuadro de búsqueda está oculto hasta que el usuario realiza una acción para mostrarlo. En iOS, el cuadro de búsqueda aparece rebotando verticalmente el contenido de la página y, en Android, el cuadro de búsqueda aparece al pulsar el icono del signo de interrogación.
- `Expanded`: el cuadro de búsqueda está visible y totalmente expandido. Se trata del valor predeterminado de la propiedad [`SearchBoxVisibility`](xref:Xamarin.Forms.SearchHandler.SearchBoxVisibility).

> [!IMPORTANT]
> En iOS, un cuadro de búsqueda contraíble requiere iOS 11 o superior.

En el ejemplo siguiente se muestra cómo ocultar el cuadro de búsqueda:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">
    <Shell.SearchHandler>
        <controls:AnimalSearchHandler SearchBoxVisibility="Hidden"
                                      ... />
    </Shell.SearchHandler>
    ...
</ContentPage>
```

## <a name="search-box-focus"></a>Enfoque en el cuadro de búsqueda

Al pulsar en un cuadro de búsqueda aparece el teclado en pantalla, y el cuadro de búsqueda adquiere el foco de entrada. Esto también se consigue mediante programación llamando al método [`Focus`](xref:Xamarin.Forms.SearchHandler.Focus), que intenta establecer el foco de entrada en el cuadro de búsqueda y, si se realiza correctamente, devuelve `true`. Cuando un cuadro de búsqueda obtiene el foco, se desencadena el evento [`Focused`](xref:Xamarin.Forms.SearchHandler.Focused) y se llama al método `OnFocused` reemplazable.

Cuando un cuadro de búsqueda tiene el foco de entrada, al pulsar en otro lugar en la pantalla desaparece el teclado en pantalla y el cuadro de búsqueda pierde el foco de entrada. Esto también se consigue mediante programación llamando al método [`Unfocus`](xref:Xamarin.Forms.SearchHandler.Unfocus). Cuando un cuadro de búsqueda pierde el foco, se desencadena el evento [`Unfocused`](xref:Xamarin.Forms.SearchHandler.Unfocused) y se llama al método `OnUnfocus` reemplazable.

Se puede recuperar el estado del foco de un cuadro de búsqueda a través de la propiedad [`IsFocused`](xref:Xamarin.Forms.SearchHandler.IsFocused), que devuelve `true` si un objeto [`SearchHandler`](xref:Xamarin.Forms.SearchHandler) tiene actualmente el foco de entrada.

## <a name="searchhandler-keyboard"></a>Teclado SearchHandler

El teclado que aparece cuando los usuarios interactúan con un objeto [`SearchHandler`](xref:Xamarin.Forms.SearchHandler) se puede establecer mediante programación a través de la propiedad [`Keyboard`](xref:Xamarin.Forms.SearchHandler.Keyboard) en una de las siguientes propiedades de la clase [`Keyboard`](xref:Xamarin.Forms.Keyboard):

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat): se usa para el texto y los lugares donde los emoji son útiles.
- [`Default`](xref:Xamarin.Forms.Keyboard.Default): el teclado predeterminado.
- [`Email`](xref:Xamarin.Forms.Keyboard.Email): se usa al especificar direcciones de correo electrónico.
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric): se usa al escribir números.
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain): se usa al escribir texto, sin ningún [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) especificado.
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone): se usa al escribir números de teléfono.
- [`Text`](xref:Xamarin.Forms.Keyboard.Text): se usa al escribir texto.
- [`Url`](xref:Xamarin.Forms.Keyboard.Url) : se usa para especificar las rutas de acceso de archivo y direcciones web.

Esto se puede lograr en XAML de la siguiente manera:

```xaml
<SearchHandler Keyboard="Email" />
```

El código de C# equivalente es el siguiente:

```csharp
SearchHandler searchHandler = new SearchHandler { Keyboard = Keyboard.Email };
```

La clase [`Keyboard`](xref:Xamarin.Forms.Keyboard) tiene también un patrón de diseño Factory Method [`Create`](xref:Xamarin.Forms.Keyboard.Create*) que puede usarse para personalizar un teclado mediante la especificación del comportamiento de las mayúsculas y minúsculas, el corrector ortográfico y las sugerencias. Los valores de enumeración [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) se especifican como argumentos para el método, con la devolución de un `Keyboard` personalizado. La enumeración `KeyboardFlags` contiene los valores siguientes:

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None): no se agregan características al teclado.
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence): indica que la primera letra de la primera palabra de cada frase se escribirá automáticamente en mayúsculas.
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck): indica que se pasará el corrector ortográfico al texto especificado.
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions): indica que se ofrecerán finalizaciones de palabra para el texto especificado.
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord): indica que las primeras letras de todas las palabras se escribirán automáticamente en mayúsculas.
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter): indica que todos los caracteres se escribirán automáticamente en mayúsculas.
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone): indica que no se producirá ningún uso automático de mayúsculas.
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All): indica que se pasará el corrector automático, se ofrecerán finalizaciones de palabras y las frases empezarán en mayúsculas en el texto especificado.

El ejemplo de código XAML siguiente muestra cómo personalizar el [`Keyboard`](xref:Xamarin.Forms.Keyboard) predeterminado para ofrecer finalizaciones de palabras y poner en mayúsculas todos los caracteres especificados:

```xaml
<SearchHandler Placeholder="Enter search terms">
    <SearchHandler.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </SearchHandler.Keyboard>
</SearchHandler>
```

El código de C# equivalente es el siguiente:

```csharp
SearchHandler searchHandler = new SearchHandler { Placeholder = "Enter search terms" };
searchHandler.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

## <a name="related-links"></a>Vínculos relacionados

- [Xaminals (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Navegación en Xamarin.Forms Shell](navigation.md)
