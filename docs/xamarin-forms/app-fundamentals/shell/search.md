---
title: "Xamarin.Forms Búsqueda en Shell" description: "Xamarin.Forms Las aplicaciones de Shell pueden usar la funcionalidad de búsqueda integrada que se proporciona en un cuadro de búsqueda que se puede agregar en la parte superior de cada página".
ms.prod: ms.assetid: ms.technology: author: ms.author: ms.date: no-loc:
- "Xamarin.Forms"
- "Xamarin.Essentials"

---

# <a name="xamarinforms-shell-search"></a>Búsqueda en Xamarin.Forms Shell

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.Forms Shell incluye la funcionalidad de búsqueda integrada que proporciona la clase `SearchHandler`. La funcionalidad de búsqueda se puede agregar a una página mediante el establecimiento de la propiedad adjunta `Shell.SearchHandler` en un objeto `SearchHandler` en subclase. El resultado es un cuadro de búsqueda que se agrega en la parte superior de la página:

[![Captura de pantalla de una clase SearchHandler de Shell en iOS y Android](search-images/searchhandler.png "Clase SearchHandler de Shell")](search-images/searchhandler-large.png#lightbox "Clase SearchHandler de Shell")

Cuando se escribe una consulta en el cuadro de búsqueda, la propiedad `Query` se actualiza y, en cada actualización, se ejecuta el método `OnQueryChanged`. Este método se puede invalidar para rellenar el área de sugerencias de búsqueda con datos:

[![Captura de pantalla de resultados de la búsqueda en una clase SearchHandler de Shell, en iOS y Android](search-images/search-suggestions.png "Resultados de la búsqueda de la clase SearchHandler de Shell")](search-images/search-suggestions-large.png#lightbox "Resultados de la búsqueda de la clase SearchHandler de Shell")

Luego, cuando se selecciona un resultado del área de sugerencias de búsqueda, se ejecuta el método `OnItemSelected`. Este método se puede invalidar para responder de forma adecuada; por ejemplo, navegando a una página de detalles.

## <a name="create-a-searchhandler"></a>Creación de una clase SearchHandler

La funcionalidad de búsqueda se puede agregar a una aplicación de Shell mediante la creación de subclases de la clase `SearchHandler` y la invalidación de los métodos `OnQueryChanged` y `OnItemSelected`:

```csharp
public class MonkeySearchHandler : SearchHandler
{
    protected override void OnQueryChanged(string oldValue, string newValue)
    {
        base.OnQueryChanged(oldValue, newValue);

        if (string.IsNullOrWhiteSpace(newValue))
        {
            ItemsSource = null;
        }
        else
        {
            ItemsSource = MonkeyData.Monkeys
                .Where(monkey => monkey.Name.ToLower().Contains(newValue.ToLower()))
                .ToList<Animal>();
        }
    }

    protected override async void OnItemSelected(object item)
    {
        base.OnItemSelected(item);

        // Note: strings will be URL encoded for navigation (e.g. "Blue Monkey" becomes "Blue%20Monkey"). Therefore, decode at the receiver.
        await (App.Current.MainPage as Xamarin.Forms.Shell).GoToAsync($"monkeydetails?name={((Animal)item).Name}");
    }
}
```

La invalidación `OnQueryChanged` tiene dos argumentos: `oldValue`, que contiene la consulta de búsqueda anterior, y `newValue`, que contiene la consulta de búsqueda actual. El área de sugerencias de búsqueda se puede actualizar mediante el establecimiento de la propiedad `SearchHandler.ItemsSource` en una colección `IEnumerable` que contiene elementos que coinciden con la consulta de búsqueda actual.

Cuando el usuario selecciona un resultado de búsqueda, se ejecuta la invalidación `OnItemSelected` y se establece la propiedad `SelectedItem`. En este ejemplo, el método dirige a otra página que muestra datos sobre el elemento `Animal` seleccionado. Para obtener más información sobre la navegación, consulte [Navegación en Xamarin.Forms Shell](navigation.md).

> [!NOTE]
> Se pueden establecer propiedades `SearchHandler` adicionales para controlar la apariencia del cuadro de búsqueda.

## <a name="consume-a-searchhandler"></a>Consumo de una clase SearchHandler

La clase `SearchHandler` en subclase se puede consumir mediante el establecimiento de la propiedad adjunta `Shell.SearchHandler` en un objeto del tipo en subclase:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">
    <Shell.SearchHandler>
        <controls:MonkeySearchHandler Placeholder="Enter search term"
                                      ShowsResults="true"
                                      DisplayMemberName="Name" />
    </Shell.SearchHandler>
    ...
</ContentPage>
```

El código de C# equivalente es el siguiente:

```csharp
Shell.SetSearchHandler(this, new MonkeySearchHandler
{
    Placeholder = "Enter search term",
    ShowsResults = true,
    DisplayMemberName = "Name"
});
```

El método `MonkeySearchHandler.OnQueryChanged` devuelve un elemento `List` de objetos `Animal`. La propiedad `DisplayMemberName` se establece en la propiedad `Name` de cada objeto `Animal`, por lo que los datos mostrados en el área de sugerencias serán el nombre de cada animal.

La propiedad `ShowsResults` está establecida en `true`, de modo que se muestran sugerencias de búsqueda cuando el usuario escribe una consulta de búsqueda:

[![Captura de pantalla de resultados de la búsqueda en una clase SearchHandler de Shell, en iOS y Android](search-images/search-results.png "Resultados de la búsqueda de la clase SearchHandler de Shell")](search-images/search-results-large.png#lightbox "Resultados de la búsqueda de la clase SearchHandler de Shell")

A medida que cambia la consulta de búsqueda, se actualiza el área de sugerencias de búsqueda:

[![Captura de pantalla de resultados de la búsqueda en una clase SearchHandler de Shell, en iOS y Android](search-images/search-results-change.png "Resultados de la búsqueda de la clase SearchHandler de Shell")](search-images/search-results-change-large.png#lightbox "Resultados de la búsqueda de la clase SearchHandler de Shell")

Cuando se selecciona un resultado de búsqueda, hay un desplazamiento hasta `MonkeyDetailPage` y se muestran datos sobre el mono seleccionado:

[![Captura de pantalla de detalles de mono en iOS y Android](search-images/detailpage.png "Detalles de mono")](search-images/detailpage-large.png#lightbox "Detalles de mono")

## <a name="define-search-results-item-appearance"></a>Definición de la apariencia de los elemento de los resultados de búsqueda

Además de mostrar datos de `string` en los resultados de búsqueda, se puede definir la apariencia de cada uno de los elementos de los resultados de búsqueda mediante el establecimiento de la propiedad `SearchHandler.ItemTemplate` en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">    
    <Shell.SearchHandler>
        <controls:MonkeySearchHandler Placeholder="Enter search term"
                                      ShowsResults="true">
            <controls:MonkeySearchHandler.ItemTemplate>
                <DataTemplate>
                    <Grid Padding="10">
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="0.15*" />
                            <ColumnDefinition Width="0.85*" />
                        </Grid.ColumnDefinitions>
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="40"
                               WidthRequest="40" />
                        <Label Grid.Column="1"
                               Text="{Binding Name}"
                               FontAttributes="Bold" />
                    </Grid>
                </DataTemplate>
            </controls:MonkeySearchHandler.ItemTemplate>
       </controls:MonkeySearchHandler>
    </Shell.SearchHandler>
    ...
</ContentPage>
```

El código de C# equivalente es el siguiente:

```csharp
Shell.SetSearchHandler(this, new MonkeySearchHandler
{
    Placeholder = "Enter search term",
    ShowsResults = true,
    DisplayMemberName = "Name",
    ItemTemplate = new DataTemplate(() =>
    {
        Grid grid = new Grid { Padding = 10 };
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(0.15, GridUnitType.Star) });
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(0.85, GridUnitType.Star) });

        Image image = new Image { Aspect = Aspect.AspectFill, HeightRequest = 40, WidthRequest = 40 };
        image.SetBinding(Image.SourceProperty, "ImageUrl");
        Label nameLabel = new Label { FontAttributes = FontAttributes.Bold };
        nameLabel.SetBinding(Label.TextProperty, "Name");

        grid.Children.Add(image);
        grid.Children.Add(nameLabel, 1, 0);
        return grid;
    })
});
```

Los elementos especificados en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definen la apariencia de cada elemento en el área de sugerencias. En este ejemplo, el diseño dentro de `DataTemplate` se administra mediante un objeto [`Grid`](xref:Xamarin.Forms.Grid). El objeto `Grid` contiene un objeto [`Image`](xref:Xamarin.Forms.Image) y un objeto [`Label`](xref:Xamarin.Forms.Label), que enlazan ambos con las propiedades de cada objeto `Monkey`.

Las capturas de pantalla siguientes muestran el resultado de crear plantillas para cada elemento del área de sugerencias:

[![Captura de pantalla de resultados de la búsqueda con plantilla en una clase SearchHandler de Shell, en iOS y Android](search-images/search-results-template.png "Resultados de la búsqueda con plantilla de la clase SearchHandler de Shell")](search-images/search-results-template-large.png#lightbox "Resultados de la búsqueda con plantilla de la clase SearchHandler de Shell")

Para obtener más información sobre las plantillas de datos, consulte [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="search-box-visibility"></a>Visibilidad del cuadro de búsqueda

Cuando se agrega un `SearchHandler` en la parte superior de una página, de forma predeterminada el cuadro de búsqueda está visible y totalmente expandido. Pero este comportamiento se puede cambiar estableciendo la propiedad `SearchHandler.SearchBoxVisibility` en uno de los miembros de la enumeración `SearchBoxVisibility`:

- `Hidden`: el cuadro de búsqueda no es visible ni accesible.
- `Collapsible`: el cuadro de búsqueda está oculto hasta que el usuario realiza una acción para mostrarlo. En iOS, el cuadro de búsqueda aparece rebotando verticalmente el contenido de la página y, en Android, el cuadro de búsqueda aparece al pulsar el icono del signo de interrogación.
- `Expanded`: el cuadro de búsqueda está visible y totalmente expandido. Este es el valor predeterminado de la propiedad `SearchHandler.SearchBoxVisibility`.

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

Al pulsar en un cuadro de búsqueda aparece el teclado en pantalla, y el cuadro de búsqueda adquiere el foco de entrada. Esto también se consigue mediante programación llamando al método `Focus`, que intenta establecer el foco de entrada en el cuadro de búsqueda, y devuelve `true` si se realiza correctamente. Cuando un cuadro de búsqueda obtiene el foco, se desencadena el evento `Focus` y se llama al método `OnFocused` reemplazable.

Cuando un cuadro de búsqueda tiene el foco de entrada, al pulsar en otro lugar en la pantalla desaparece el teclado en pantalla y el cuadro de búsqueda pierde el foco de entrada. Esto también se consigue mediante programación llamando al método `Unfocus`. Cuando un cuadro de búsqueda pierde el foco, se desencadena el evento `Unfocused` y se llama al método `OnUnfocus` reemplazable.

Se puede recuperar el estado del foco de un cuadro de búsqueda a través de la propiedad `IsFocused`, que devuelve `true` si un `SearchHandler` tiene actualmente el foco de entrada.

## <a name="searchhandler-appearance"></a>Apariencia SearchHandler

La clase `SearchHandler` define las siguientes propiedades que afectan a su apariencia:

- `BackgroundColor`, del tipo `Color`, es el color de fondo para el texto del cuadro de búsqueda.
- `CancelButtonColor`, del tipo `Color`, es el color del botón Cancelar.
- `CharacterSpacing`, del tipo `double`, es el espaciado entre los caracteres del texto de `SearchHandler`.
- `FontAttributes`, del tipo `FontAttributes`, indica si el texto del cuadro de búsqueda está en negrita o cursiva.
- `FontFamily`, del tipo `string`, es la familia de fuentes utilizada para el texto del cuadro de búsqueda.
- `FontSize`, del tipo `double`, es el tamaño del texto del cuadro de búsqueda.
- `HorizontalTextAlignment`, del tipo `TextAlignment`, es la alineación horizontal del texto del cuadro de búsqueda.
- `PlaceholderColor`, del tipo `Color`, es el color del texto del cuadro de búsqueda del marcador de posición.
- `TextColor`, del tipo `Color`, es el color del texto del cuadro de búsqueda.
- `VerticalTextAlignment`, del tipo `TextAlignment`, es la alineación vertical del texto del cuadro de búsqueda.

## <a name="searchhandler-keyboard"></a>Teclado SearchHandler

El teclado que aparece cuando los usuarios interactúan con un `SearchHandler` se puede establecer mediante programación a través de la propiedad `Keyboard` en una de las siguientes propiedades desde la clase [`Keyboard`](xref:Xamarin.Forms.Keyboard):

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

## <a name="searchhandler-reference"></a>Referencia de SearchHandler

La clase `SearchHandler` define las siguientes propiedades que controlan su apariencia y comportamiento:

- `BackgroundColor`, del tipo `Color`, es el color de fondo para el texto del cuadro de búsqueda.
- `CancelButtonColor`, del tipo `Color`, es el color del botón Cancelar.
- `ClearIcon`, de tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), el icono que aparece para borrar el contenido del cuadro de búsqueda.
- `ClearIconHelpText`, de tipo `string`, el texto de ayuda accesible para el icono de borrar.
- `ClearIconName`, de tipo `string`, el nombre del icono de borrar para usar con los lectores de pantalla.
- `ClearPlaceholderCommand`, de tipo `ICommand`, que se ejecuta cuando se pulsa `ClearPlaceholderIcon`.
- `ClearPlaceholderCommandParameter`, de tipo `object`, que es el parámetro que se pasa a `ClearPlaceholderCommand`.
- `ClearPlaceholderEnabled`, de tipo `bool`, que determina si se puede ejecutar `ClearPlaceholderCommand`. El valor predeterminado es `true`.
- `ClearPlaceholderHelpText`, de tipo `string`, el texto de ayuda accesible para el icono de borrar marcador de posición.
- `ClearPlaceholderIcon`, de tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), el icono de borrar marcador de posición que se muestra cuando el cuadro de búsqueda está vacío.
- `ClearPlaceholderName`, de tipo `string`, el nombre del icono de borrar marcador de posición para su uso con los lectores de pantalla.
- `Command`, de tipo `ICommand`, que se ejecuta cuando se confirma la consulta de búsqueda.
- `CommandParameter`, de tipo `object`, que es el parámetro que se pasa a `Command`.
- `DisplayMemberName`, de tipo `string`, que representa el nombre o la ruta de acceso de la propiedad que se muestra para cada elemento de datos de la colección `ItemsSource`.
- `FontAttributes`, del tipo `FontAttributes`, indica si el texto del cuadro de búsqueda está en negrita o cursiva.
- `FontFamily`, del tipo `string`, es la familia de fuentes utilizada para el texto del cuadro de búsqueda.
- `FontSize`, del tipo `double`, es el tamaño del texto del cuadro de búsqueda.
- `HorizontalTextAlignment`, del tipo `TextAlignment`, es la alineación horizontal del texto del cuadro de búsqueda.
- `IsFocused`, del tipo `bool`, que representa si un `SearchHandler` actualmente tiene foco de entrada.
- `IsSearchEnabled`, de tipo `bool`, que representa el estado habilitado del cuadro de búsqueda. El valor predeterminado es `true`.
- `ItemsSource`, de tipo `IEnumerable`, especifica la colección de elementos que se mostrarán en el área de sugerencias, y tiene un valor predeterminado de `null`.
- `ItemTemplate`, de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), especifica la plantilla que se aplicará a cada elemento de la colección de elementos que se mostrará en el área de sugerencias.
- `Keyboard`, del tipo `Keyboard`, es el teclado para el `SearchHandler`.
- `Placeholder`, de tipo `string`, el texto que se muestra cuando el cuadro de búsqueda está vacío.
- `PlaceholderColor`, del tipo `Color`, es el color del texto del cuadro de búsqueda del marcador de posición.
- `Query`, de tipo `string`, el texto especificado por el usuario en el cuadro de búsqueda.
- `QueryIcon`, de tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), el icono utilizado para indicar al usuario que la búsqueda está disponible.
- `QueryIconHelpText`, de tipo `string`, el texto de ayuda accesible para el icono de consulta.
- `QueryIconName`, de tipo `string`, el nombre del icono de consulta para su uso con los lectores de pantalla.
- `SearchBoxVisibility`, de tipo `SearchBoxVisibility`, la visibilidad del cuadro de búsqueda. De forma predeterminada, el cuadro de búsqueda está visible y totalmente expandido.
- `SelectedItem`, de tipo `object`, el elemento seleccionado en los resultados de búsqueda. Esta propiedad es de solo lectura y tiene un valor predeterminado de `null`.
- `ShowsResults`, de tipo `bool`, indica si se deben esperar resultados de búsqueda en el área de sugerencias, al escribir texto. El valor predeterminado es `false`.
- `TextColor`, del tipo `Color`, es el color del texto del cuadro de búsqueda.

Todas estas propiedades están respaldados por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que las propiedades pueden ser destinos de los enlaces de datos.

Además, la clase `SearchHandler` proporciona los métodos reemplazables siguientes:

- `OnClearPlaceholderClicked`, que se llama cada vez que se pulsa `ClearPlaceholderIcon`.
- `OnItemSelected`, que se llama cada vez que el usuario selecciona un resultado de búsqueda.
- `OnFocused`, al que se llama cuando un `SearchHandler` adquiere el foco de entrada.
- `OnQueryChanged`, que se llama cuando cambia la propiedad `Query`.
- `OnQueryConfirmed`, que se llama cada vez que el usuario presiona Entrar o confirma su consulta en el cuadro de búsqueda.
- `OnUnfocus`, al que se llama cuando un `SearchHandler` pierde el foco de entrada.

## <a name="related-links"></a>Vínculos relacionados

- [Xaminals (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Navegación en Xamarin.Forms Shell](navigation.md)
