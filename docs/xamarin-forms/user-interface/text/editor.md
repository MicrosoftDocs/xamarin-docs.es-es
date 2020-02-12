---
title: Editor de Xamarin.Forms
description: En este artículo se explica cómo usar el control del Editor de Xamarin.Forms para aceptar la entrada de texto de varias líneas en una aplicación.
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/26/2019
ms.openlocfilehash: 1ae176cfebdde31038c30895d1bf562ff3396eaa
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/11/2020
ms.locfileid: "77131069"
---
# <a name="xamarinforms-editor"></a>Editor de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Entrada de texto de varias líneas_

El control [`Editor`](xref:Xamarin.Forms.Editor) se utiliza para aceptar la entrada de varias líneas. En este artículo se describe:

- **[Personalización](#customization)** &ndash; opciones de teclado y color.
- **[Interactividad](#interactivity)** &ndash; eventos que se pueden escuchar para que proporcionen interactividad.

## <a name="customization"></a>Personalización

### <a name="setting-and-reading-text"></a>Establecimiento y lectura de texto

La [`Editor`](xref:Xamarin.Forms.Editor), al igual que otras vistas de presentación de texto, expone la propiedad `Text`. Esta propiedad se puede utilizar para establecer y leer el texto presentado por el `Editor`. En el ejemplo siguiente se muestra cómo establecer la propiedad `Text` en XAML:

```xaml
<Editor Text="I am an Editor" />
```

En C#:

```csharp
var MyEditor = new Editor { Text = "I am an Editor" };
```

Para leer texto, acceda a la propiedad `Text` C#en:

```csharp
var text = MyEditor.Text;
```

### <a name="setting-placeholder-text"></a>Establecer el texto de marcador de posición

El [`Editor`](xref:Xamarin.Forms.Editor) se puede establecer para mostrar el texto del marcador de posición cuando no almacena los datos proporcionados por el usuario. Esto se consigue estableciendo la propiedad [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) en un `string`y a menudo se usa para indicar el tipo de contenido adecuado para el `Editor`. Además, el color del texto del marcador de posición se puede controlar estableciendo la propiedad [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) en un [`Color`](xref:Xamarin.Forms.Color):

```xaml
<Editor Placeholder="Enter text here" PlaceholderColor="Olive" />
```

```csharp
var editor = new Editor { Placeholder = "Enter text here", PlaceholderColor = Color.Olive };
```

### <a name="preventing-text-entry"></a>Impedir la entrada de texto

Se puede impedir que los usuarios modifiquen el texto en un [`Editor`](xref:Xamarin.Forms.Editor) estableciendo la propiedad `IsReadOnly`, que tiene un valor predeterminado de `false`, para `true`:

```xaml
<Editor Text="This is a read-only Editor"
        IsReadOnly="true" />
```

```csharp
var editor = new Editor { Text = "This is a read-only Editor", IsReadOnly = true });
```

> [!NOTE]
> La propiedad `IsReadonly` no modifica la apariencia visual de un [`Editor`](xref:Xamarin.Forms.Editor), a diferencia de la propiedad `IsEnabled`, que también cambia la apariencia visual del `Editor` a gris.

### <a name="limiting-input-length"></a>Limitar la longitud de entrada

La propiedad [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) se puede utilizar para limitar la longitud de entrada permitida para el [`Editor`](xref:Xamarin.Forms.Editor). Esta propiedad debe establecerse en un entero positivo:

```xaml
<Editor ... MaxLength="10" />
```

```csharp
var editor = new Editor { ... MaxLength = 10 };
```

Un valor de propiedad [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) de 0 indica que no se permitirá ninguna entrada y un valor de `int.MaxValue`, que es el valor predeterminado para un [`Editor`](xref:Xamarin.Forms.Editor), indica que no hay ningún límite efectivo en el número de caracteres que se pueden escribir.

### <a name="character-spacing"></a>Espaciado de caracteres

El espaciado de caracteres se puede aplicar a un [`Editor`](xref:Xamarin.Forms.Editor) estableciendo la propiedad `Editor.CharacterSpacing` en un valor `double`:

```xaml
<Editor ...
        CharacterSpacing="10" />
```

El código de C# equivalente es el siguiente:

```csharp
Editor editor = new editor { CharacterSpacing = 10 };
```

El resultado es que los caracteres del texto que muestra el [`Editor`](xref:Xamarin.Forms.Editor) se espacian `CharacterSpacing` unidades independientes del dispositivo.

> [!NOTE]
> El valor de la propiedad `CharacterSpacing` se aplica al texto que se muestra en las propiedades `Text` y `Placeholder`.

### <a name="auto-sizing-an-editor"></a>Un Editor de tamaño automático

Se puede crear un [`Editor`](xref:Xamarin.Forms.Editor) de tamaño automático para su contenido estableciendo la propiedad [`Editor.AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) en [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges), que es un valor de la enumeración [`EditoAutoSizeOption`](xref:Xamarin.Forms.EditorAutoSizeOption) . Esta enumeración tiene dos valores:

- [`Disabled`](xref:Xamarin.Forms.EditorAutoSizeOption.Disabled) indica que el cambio de tamaño automático está deshabilitado y es el valor predeterminado.
- [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges) indica que el cambio de tamaño automático está habilitado.

Esto puede realizarse en el código siguiente:

```xaml
<Editor Text="Enter text here" AutoSize="TextChanges" />
```

```csharp
var editor = new Editor { Text = "Enter text here", AutoSize = EditorAutoSizeOption.TextChanges };
```

Cuando el cambio de tamaño automático está habilitado, el alto del [`Editor`](xref:Xamarin.Forms.Editor) aumentará cuando el usuario lo llene con texto y el alto disminuirá a medida que el usuario elimine el texto.

> [!NOTE]
> Un [`Editor`](xref:Xamarin.Forms.Editor) no cambiará de tamaño automáticamente si se ha establecido la propiedad [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) .

### <a name="customizing-the-keyboard"></a>Personalizar el teclado

El teclado que se muestra cuando los usuarios interactúan con un [`Editor`](xref:Xamarin.Forms.Editor) se pueden establecer mediante programación a través de la propiedad [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) , en una de las siguientes propiedades de la clase [`Keyboard`](xref:Xamarin.Forms.Keyboard) :

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
<Editor Keyboard="Chat" />
```

El código de C# equivalente es el siguiente:

```csharp
var editor = new Editor { Keyboard = Keyboard.Chat };
```

Puede encontrar ejemplos de cada teclado en nuestro repositorio de [recetas](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry) .

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
<Editor>
    <Editor.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </Editor.Keyboard>
</Editor>
```

El código de C# equivalente es el siguiente:

```csharp
var editor = new Editor();
editor.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

### <a name="enabling-and-disabling-spell-checking"></a>Habilitar y deshabilitar el corrector ortográfico

La propiedad [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) controla si está habilitada la revisión ortográfica. De forma predeterminada, la propiedad se establece en `true`. Cuando el usuario escriba texto, se indican los errores de ortografía.

Sin embargo, en algunos escenarios de entrada de texto, como la especificación de un nombre de usuario, la revisión ortográfica proporciona una experiencia negativa, por lo que debe deshabilitarse estableciendo la propiedad [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) en `false`:

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Cuando la propiedad [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) está establecida en `false`y no se utiliza un teclado personalizado, se deshabilitará el corrector ortográfico nativo. Sin embargo, si se ha establecido un [`Keyboard`](xref:Xamarin.Forms.Keyboard) que deshabilita la revisión ortográfica, como [`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat), se omite la propiedad `IsSpellCheckEnabled`. Por lo tanto, la propiedad no se puede usar para habilitar la revisión ortográfica de un `Keyboard` que lo deshabilita explícitamente.

### <a name="enabling-and-disabling-text-prediction"></a>Habilitación y deshabilitación de la predicción de texto

La propiedad `IsTextPredictionEnabled` controla si está habilitada la predicción de texto y la corrección de texto automática. De forma predeterminada, la propiedad se establece en `true`. Cuando el usuario escriba texto, se presentan las predicciones de word.

Sin embargo, en algunos escenarios de entrada de texto, como la especificación de un nombre de usuario, la predicción de texto y la corrección automática de texto proporcionan una experiencia negativa y se deben deshabilitar estableciendo la propiedad `IsTextPredictionEnabled` en `false`:

```xaml
<Editor ... IsTextPredictionEnabled="false" />
```

```csharp
var editor = new Editor { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> Cuando la propiedad `IsTextPredictionEnabled` está establecida en `false`y no se utiliza un teclado personalizado, se deshabilita la predicción de texto y la corrección de texto automática. Sin embargo, si se ha establecido un [`Keyboard`](xref:Xamarin.Forms.Keyboard) que deshabilita la predicción de texto, se omite la propiedad `IsTextPredictionEnabled`. Por lo tanto, la propiedad no se puede usar para habilitar la predicción de texto para un `Keyboard` que lo deshabilita explícitamente.

### <a name="colors"></a>Colores

`Editor` se puede establecer para utilizar un color de fondo personalizado a través de la propiedad `BackgroundColor`. Atención especial es necesaria para asegurarse de que los colores se podrán usar en cada plataforma. Dado que cada plataforma tiene distintos valores predeterminados para el color de texto, deberá establecer un color de fondo personalizado para cada plataforma. Vea [trabajar con ajustes de plataforma](~/xamarin-forms/platform/device.md) para obtener más información sobre la optimización de la interfaz de usuario para cada plataforma.

En C#:

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        //dark blue on UWP & Android, light blue on iOS
        var editor = new Editor { BackgroundColor = Device.RuntimePlatform == Device.iOS ? Color.FromHex("#A4EAFF") : Color.FromHex("#2c3e50") };
        layout.Children.Add(editor);
    }
}
```

En XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="TextSample.EditorPage"
    Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor>
                <Editor.BackgroundColor>
                    <OnPlatform x:TypeArguments="x:Color">
                        <On Platform="iOS" Value="#a4eaff" />
                        <On Platform="Android, UWP" Value="#2c3e50" />
                    </OnPlatform>
                </Editor.BackgroundColor>
            </Editor>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

![](editor-images/textbackgroundcolor.png "Editor with BackgroundColor Example")

Asegúrese de que los colores de fondo y de texto que elija puede utilizar en cada plataforma y no interferir con cualquier texto de marcador de posición.

## <a name="interactivity"></a>Interactividad

`Editor` expone dos eventos:

- [TextChanged](xref:Xamarin.Forms.InputView.TextChanged) &ndash; genera cuando cambia el texto en el editor. Proporciona el texto antes y después del cambio.
- [Completado](xref:Xamarin.Forms.Editor.Completed) &ndash; que se genera cuando el usuario ha finalizado la entrada presionando la tecla entrar en el teclado.

> [!NOTE]
> La clase [`VisualElement`](xref:Xamarin.Forms.VisualElement) , de la que [`Entry`](xref:Xamarin.Forms.Entry) hereda, también tiene eventos [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) y [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) .

### <a name="completed"></a>Completed

El evento `Completed` se utiliza para reaccionar a la finalización de una interacción con un `Editor`. `Completed` se produce cuando el usuario finaliza la entrada con un campo escribiendo la tecla RETURN en el teclado (o presionando la tecla TAB en UWP). El controlador del evento es un controlador de eventos genérico que toma al remitente y `EventArgs`:

```csharp
void EditorCompleted (object sender, EventArgs e)
{
    var text = ((Editor)sender).Text; // sender is cast to an Editor to enable reading the `Text` property of the view.
}
```

En el código y XAML, se puede suscribir el evento completado para:

En C#:

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var editor = new Editor ();
        editor.Completed += EditorCompleted;
        layout.Children.Add(editor);
    }
}
```

En XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.EditorPage"
Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor Completed="EditorCompleted" />
        </StackLayout>
    </ContentPage.Content>
</Contentpage>
```

### <a name="textchanged"></a>TextChanged

El evento `TextChanged` se utiliza para reaccionar a un cambio en el contenido de un campo.

`TextChanged` se produce cuando cambia el `Text` de la `Editor`. El controlador del evento toma una instancia de `TextChangedEventArgs`. `TextChangedEventArgs` proporciona acceso a los valores antiguos y nuevos del `Editor` `Text` a través de las propiedades `OldTextValue` y `NewTextValue`:

```csharp
void EditorTextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

En el código y XAML, se puede suscribir el evento completado para:

En el código:

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var editor = new Editor ();
        editor.TextChanged += EditorTextChanged;
        layout.Children.Add(editor);
    }
}
```

En XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.EditorPage"
Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor TextChanged="EditorTextChanged" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

## <a name="related-links"></a>Vínculos relacionados

- [Texto (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [API de los editores](xref:Xamarin.Forms.Editor)
