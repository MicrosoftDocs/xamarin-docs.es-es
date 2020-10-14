---
title: Xamarin.Forms Editor
description: En este artículo se explica cómo usar el Xamarin.Forms control de editor para aceptar la entrada de texto de varias líneas en una aplicación.
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 257962516b8a183e63c49b22bc2be108364d9425
ms.sourcegitcommit: e7d6f8bb074c08d87f7507ad5f1377cfacefa32a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92041891"
---
# <a name="no-locxamarinforms-editor"></a>Xamarin.Forms Editor

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

El [`Editor`](xref:Xamarin.Forms.Editor) control se usa para aceptar la entrada de varias líneas.

## <a name="set-and-read-text"></a>Establecer y leer texto

[`Editor`](xref:Xamarin.Forms.Editor), Al igual que otras vistas de presentación de texto, expone la `Text` propiedad. Esta propiedad se puede utilizar para establecer y leer el texto presentado por `Editor` . En el ejemplo siguiente se muestra cómo establecer la `Text` propiedad en XAML:

```xaml
<Editor Text="I am an Editor" />
```

En C#:

```csharp
var MyEditor = new Editor { Text = "I am an Editor" };
```

Para leer texto, acceda a la `Text` propiedad en C#:

```csharp
var text = MyEditor.Text;
```

## <a name="set-placeholder-text"></a>Establecer texto de marcador de posición

[`Editor`](xref:Xamarin.Forms.Editor)Se puede establecer para mostrar el texto del marcador de posición cuando no se almacena la entrada del usuario. Esto se logra estableciendo la [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) propiedad en `string` y, a menudo, se usa para indicar el tipo de contenido adecuado para `Editor` . Además, el color del texto del marcador de posición se puede controlar estableciendo la [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) propiedad en [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<Editor Placeholder="Enter text here" PlaceholderColor="Olive" />
```

```csharp
var editor = new Editor { Placeholder = "Enter text here", PlaceholderColor = Color.Olive };
```

## <a name="prevent-text-entry"></a>Impedir la entrada de texto

Se puede impedir que los usuarios modifiquen el texto en un estableciendo [`Editor`](xref:Xamarin.Forms.Editor) la `IsReadOnly` propiedad, que tiene un valor predeterminado de `false` , en `true` :

```xaml
<Editor Text="This is a read-only Editor"
        IsReadOnly="true" />
```

```csharp
var editor = new Editor { Text = "This is a read-only Editor", IsReadOnly = true });
```

> [!NOTE]
> La `IsReadonly` propiedad no modifica la apariencia visual de un [`Editor`](xref:Xamarin.Forms.Editor) , a diferencia de la `IsEnabled` propiedad que también cambia la apariencia visual del `Editor` a gris.

## <a name="transform-text"></a>Transformar texto

[`Editor`](xref:Xamarin.Forms.Editor)Puede transformar el uso de mayúsculas y minúsculas de su texto, almacenado en la `Text` propiedad, estableciendo la `TextTransform` propiedad en un valor de la `TextTransform` enumeración. Esta enumeración tiene cuatro valores:

- `None` indica que el texto no se transformará.
- `Default` indica que se utilizará el comportamiento predeterminado para la plataforma. Este es el valor predeterminado de la propiedad `TextTransform`.
- `Lowercase` indica que el texto se transformará a minúsculas.
- `Uppercase` indica que el texto se transformará en mayúsculas.

En el ejemplo siguiente se muestra cómo transformar texto a mayúsculas:

```xaml
<Editor Text="This text will be displayed in uppercase."
        TextTransform="Uppercase" />
```

El código de C# equivalente es el siguiente:

```csharp
Editor editor = new Editor
{
    Text = "This text will be displayed in uppercase.",
    TextTransform = TextTransform.Uppercase
};
```

## <a name="limit-input-length"></a>Limitar la longitud de entrada

La [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) propiedad se puede utilizar para limitar la longitud de entrada permitida para [`Editor`](xref:Xamarin.Forms.Editor) . Esta propiedad debe establecerse en un entero positivo:

```xaml
<Editor ... MaxLength="10" />
```

```csharp
var editor = new Editor { ... MaxLength = 10 };
```

Un [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) valor de propiedad de 0 indica que no se permitirá ninguna entrada, y un valor de `int.MaxValue` , que es el valor predeterminado para [`Editor`](xref:Xamarin.Forms.Editor) , indica que no hay ningún límite efectivo en el número de caracteres que se pueden escribir.

## <a name="character-spacing"></a>espaciado entre caracteres

El espaciado de caracteres se puede aplicar a [`Editor`](xref:Xamarin.Forms.Editor) si se establece la `Editor.CharacterSpacing` propiedad en un `double` valor:

```xaml
<Editor ...
        CharacterSpacing="10" />
```

El código de C# equivalente es el siguiente:

```csharp
Editor editor = new editor { CharacterSpacing = 10 };
```

El resultado es que los caracteres del texto mostrados por [`Editor`](xref:Xamarin.Forms.Editor) están separados por las `CharacterSpacing` unidades independientes del dispositivo.

> [!NOTE]
> El `CharacterSpacing` valor de la propiedad se aplica al texto mostrado por `Text` las `Placeholder` propiedades y.

## <a name="auto-size-an-editor"></a>Cambiar automáticamente el tamaño de un editor

Se [`Editor`](xref:Xamarin.Forms.Editor) puede establecer el tamaño automático de su contenido estableciendo la [`Editor.AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) propiedad en [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges) , que es un valor de la [`EditorAutoSizeOption`](xref:Xamarin.Forms.EditorAutoSizeOption) enumeración. Esta enumeración tiene dos valores:

- [`Disabled`](xref:Xamarin.Forms.EditorAutoSizeOption.Disabled) indica que el cambio de tamaño automático está deshabilitado y es el valor predeterminado.
- [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges) indica que el cambio de tamaño automático está habilitado.

Esto puede realizarse en el código como se indica a continuación:

```xaml
<Editor Text="Enter text here" AutoSize="TextChanges" />
```

```csharp
var editor = new Editor { Text = "Enter text here", AutoSize = EditorAutoSizeOption.TextChanges };
```

Cuando el cambio de tamaño automático está habilitado, el alto de [`Editor`](xref:Xamarin.Forms.Editor) aumentará cuando el usuario lo rellene con texto y el alto disminuirá a medida que el usuario elimine el texto.

> [!NOTE]
> No cambiará [`Editor`](xref:Xamarin.Forms.Editor) el tamaño de forma automática si se ha [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) establecido la propiedad.

## <a name="customize-the-keyboard"></a>Personalizar el teclado

El teclado que se presenta cuando los usuarios interactúan con [`Editor`](xref:Xamarin.Forms.Editor) se puede establecer mediante programación a través de la [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) propiedad, en una de las siguientes propiedades de la [`Keyboard`](xref:Xamarin.Forms.Keyboard) clase:

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

## <a name="enable-and-disable-spell-checking"></a>Habilitar y deshabilitar la revisión ortográfica

La [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propiedad controla si está habilitada la revisión ortográfica. De forma predeterminada, la propiedad se establece en `true` . Cuando el usuario escribe texto, se indican errores ortográficos.

Sin embargo, en algunos escenarios de entrada de texto, como la especificación de un nombre de usuario, la revisión ortográfica proporciona una experiencia negativa, por lo que debe deshabilitarse estableciendo la [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propiedad en `false` :

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Cuando la [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propiedad está establecida en `false` y no se usa un teclado personalizado, se deshabilitará el corrector ortográfico nativo. Sin embargo, si se ha [`Keyboard`](xref:Xamarin.Forms.Keyboard) establecido un que deshabilita la revisión ortográfica, como [`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat) , `IsSpellCheckEnabled` se omite la propiedad. Por consiguiente, la propiedad no se puede usar para habilitar la revisión ortográfica de un `Keyboard` que lo deshabilita explícitamente.

## <a name="enable-and-disable-text-prediction"></a>Habilitar y deshabilitar la predicción de texto

La `IsTextPredictionEnabled` propiedad controla si está habilitada la predicción de texto y la corrección de texto automática. De forma predeterminada, la propiedad se establece en `true` . A medida que el usuario escribe texto, se presentan las predicciones de palabras.

Sin embargo, en algunos escenarios de entrada de texto, como la especificación de un nombre de usuario, la predicción de texto y la corrección automática de texto proporcionan una experiencia negativa y se deben deshabilitar estableciendo la `IsTextPredictionEnabled` propiedad en `false` :

```xaml
<Editor ... IsTextPredictionEnabled="false" />
```

```csharp
var editor = new Editor { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> Cuando la `IsTextPredictionEnabled` propiedad se establece en `false` y no se usa un teclado personalizado, se deshabilita la predicción de texto y la corrección de texto automática. Sin embargo, si se ha [`Keyboard`](xref:Xamarin.Forms.Keyboard) establecido un que deshabilita la predicción de texto, `IsTextPredictionEnabled` se omite la propiedad. Por consiguiente, la propiedad no se puede usar para habilitar la predicción de texto para un `Keyboard` que lo deshabilita explícitamente.

## <a name="colors"></a>Colores

`Editor` se puede establecer para utilizar un color de fondo personalizado a través de la `BackgroundColor` propiedad. Es necesario tener especial cuidado para asegurarse de que se puedan usar los colores en cada plataforma. Dado que cada plataforma tiene valores predeterminados diferentes para el color del texto, puede que tenga que establecer un color de fondo personalizado para cada plataforma. Vea [trabajar con ajustes de plataforma](~/xamarin-forms/platform/device.md) para obtener más información sobre la optimización de la interfaz de usuario para cada plataforma.

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

![Editor con el ejemplo de BackgroundColor](editor-images/textbackgroundcolor.png)

Asegúrese de que los colores de fondo y de texto que elija se pueden usar en cada plataforma y no oculte ningún texto de marcador de posición.

## <a name="events-and-interactivity"></a>Eventos e interactividad

`Editor` expone dos eventos:

- [TextChanged](xref:Xamarin.Forms.InputView.TextChanged) &ndash; se genera cuando cambia el texto en el editor. Proporciona el texto antes y después del cambio.
- [Completado](xref:Xamarin.Forms.Editor.Completed) &ndash; se genera cuando el usuario ha finalizado la entrada presionando la tecla RETURN del teclado.

> [!NOTE]
> La [`VisualElement`](xref:Xamarin.Forms.VisualElement) clase, de la que [`Entry`](xref:Xamarin.Forms.Entry) hereda, también tiene [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) eventos y.

### <a name="completed"></a>Completed

El `Completed` evento se utiliza para reaccionar a la finalización de una interacción con `Editor` . `Completed` se genera cuando el usuario finaliza la entrada con un campo escribiendo la tecla RETURN en el teclado (o presionando la tecla TAB en UWP). El controlador del evento es un controlador de eventos genérico que toma el remitente y `EventArgs` :

```csharp
void EditorCompleted (object sender, EventArgs e)
{
    var text = ((Editor)sender).Text; // sender is cast to an Editor to enable reading the `Text` property of the view.
}
```

Se puede suscribir el evento completado en código y XAML:

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

El `TextChanged` evento se utiliza para reaccionar a un cambio en el contenido de un campo.

`TextChanged` se genera cuando `Text` cambia el de `Editor` . El controlador del evento toma una instancia de `TextChangedEventArgs` . `TextChangedEventArgs` proporciona acceso a los valores antiguos y nuevos de `Editor` `Text` a través de `OldTextValue` las `NewTextValue` propiedades y:

```csharp
void EditorTextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

Se puede suscribir el evento completado en código y XAML:

Mediante código:

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

- [Texto (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [API de los editores](xref:Xamarin.Forms.Editor)
