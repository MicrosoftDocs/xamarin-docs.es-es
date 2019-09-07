---
title: Editor de Xamarin.Forms
description: En este artículo se explica cómo usar el control del Editor de Xamarin.Forms para aceptar la entrada de texto de varias líneas en una aplicación.
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2018
ms.openlocfilehash: 684bdb21309028e02362dcc6bfd8c91c539430bd
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770181"
---
# <a name="xamarinforms-editor"></a>Editor de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Entrada de texto multilínea_

El [ `Editor` ](xref:Xamarin.Forms.Editor) control se usa para aceptar la entrada de varias líneas. En este artículo se tratan los aspectos siguientes:

- **[Personalización](#customization)**  &ndash; opciones de color y el teclado.
- **[Interactividad](#interactivity)**  &ndash; eventos que se pueden escuchar para proporcionar interactividad.

## <a name="customization"></a>Personalización

### <a name="setting-and-reading-text"></a>Establecimiento y lectura de texto

El [ `Editor` ](xref:Xamarin.Forms.Editor), al igual que otras vistas de presentación de texto, expone el `Text` propiedad. Esta propiedad se puede utilizar para establecer y leer el texto presentado por el `Editor`. El ejemplo siguiente se muestra cómo establecer el `Text` propiedad en XAML:

```xaml
<Editor Text="I am an Editor" />
```

En C#:

```csharp
var MyEditor = new Editor { Text = "I am an Editor" };
```

Para leer el texto, obtener acceso a la `Text` propiedad en C#:

```csharp
var text = MyEditor.Text;
```

### <a name="setting-placeholder-text"></a>Establecer el texto de marcador de posición

El [ `Editor` ](xref:Xamarin.Forms.Editor) puede establecerse para mostrar texto de marcador de posición cuando no está almacenando proporcionados por el usuario. Esto se consigue estableciendo la [ `Placeholder` ](xref:Xamarin.Forms.Editor.Placeholder) propiedad a un `string`y a menudo se usa para indicar el tipo de contenido que sea adecuada para el `Editor`. Además, el color del texto de marcador de posición puede controlarse estableciendo la [ `PlaceholderColor` ](xref:Xamarin.Forms.Editor.PlaceholderColor) propiedad a un [ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<Editor Placeholder="Enter text here" PlaceholderColor="Olive" />
```

```csharp
var editor = new Editor { Placeholder = "Enter text here", PlaceholderColor = Color.Olive };
```

### <a name="preventing-text-entry"></a>Impedir la entrada de texto

Se puede impedir que los usuarios modifiquen el texto en [`Editor`](xref:Xamarin.Forms.Editor) un estableciendo la `IsReadOnly` propiedad, que tiene un valor predeterminado de `false`, en `true`:

```xaml
<Editor Text="This is a read-only Editor"
        IsReadOnly="true" />
```

```csharp
var editor = new Editor { Text = "This is a read-only Editor", IsReadOnly = true });
```

> [!NOTE]
> La `IsReadonly` propiedad no modifica la apariencia visual de un [`Editor`](xref:Xamarin.Forms.Editor), `Editor` a diferencia de la `IsEnabled` propiedad que también cambia la apariencia visual del a gris.

### <a name="limiting-input-length"></a>Limitar la longitud de entrada

El [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) propiedad puede utilizarse para limitar la longitud de entrada que sea aceptable para el [ `Editor` ](xref:Xamarin.Forms.Editor). Esta propiedad debe establecerse en un entero positivo:

```xaml
<Editor ... MaxLength="10" />
```

```csharp
var editor = new Editor { ... MaxLength = 10 };
```

Un [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) valor de propiedad de 0 indica que no se permitirá ninguna entrada y un valor de `int.MaxValue`, que es el valor predeterminado para un [ `Editor` ](xref:Xamarin.Forms.Editor), indica que no hay ninguna límite real del número de caracteres que pueden especificarse.

### <a name="auto-sizing-an-editor"></a>Un Editor de tamaño automático

Un [ `Editor` ](xref:Xamarin.Forms.Editor) pueden realizarse en el ajuste de tamaño automático a su contenido estableciendo el [ `Editor.AutoSize` ](xref:Xamarin.Forms.Editor.AutoSize) propiedad [ `TextChanges` ](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges), que es un valor de la [ `EditoAutoSizeOption` ](xref:Xamarin.Forms.EditorAutoSizeOption) enumeración. Esta enumeración tiene dos valores:

- [`Disabled`](xref:Xamarin.Forms.EditorAutoSizeOption.Disabled) indica que el cambio de tamaño automático está deshabilitado y es el valor predeterminado.
- [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges) indica que el cambio de tamaño automático está habilitado.

Esto puede realizarse en el código siguiente:

```xaml
<Editor Text="Enter text here" AutoSize="TextChanges" />
```

```csharp
var editor = new Editor { Text = "Enter text here", AutoSize = EditorAutoSizeOption.TextChanges };
```

Cuando el cambio de tamaño automático está habilitado, el alto de la [ `Editor` ](xref:Xamarin.Forms.Editor) aumentará cuando el usuario lo rellena con texto y se reducirá el alto como el usuario elimina el texto.

> [!NOTE]
> Un [ `Editor` ](xref:Xamarin.Forms.Editor) le if no redimensionar el [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) se estableció la propiedad.

### <a name="customizing-the-keyboard"></a>Personalizar el teclado

El teclado que aparece cuando los usuarios interactúan con un [ `Editor` ](xref:Xamarin.Forms.Editor) se puede establecer mediante programación a través de la [ `Keyboard` ](xref:Xamarin.Forms.InputView.Keyboard) propiedad a una de las siguientes propiedades desde el [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) clase:

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat) : se usa para el texto y los lugares donde emoji son útiles.
- [`Default`](xref:Xamarin.Forms.Keyboard.Default): el teclado predeterminado.
- [`Email`](xref:Xamarin.Forms.Keyboard.Email): se usa al especificar direcciones de correo electrónico.
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric): se usa al escribir números.
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain): se usa al escribir texto, sin ningún [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) especificado.
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone): se usa al escribir números de teléfono.
- [`Text`](xref:Xamarin.Forms.Keyboard.Text): se usa al escribir texto.
- [`Url`](xref:Xamarin.Forms.Keyboard.Url) : se usa para especificar las rutas de acceso de archivo y direcciones web.

Esto puede realizarse en XAML como sigue:

```xaml
<Editor Keyboard="Chat" />
```

El código de C# equivalente es:

```csharp
var editor = new Editor { Keyboard = Keyboard.Chat };
```

Ejemplos de cada teclado pueden encontrarse en nuestra [recetas](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry) repositorio.

El [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) clase también tiene un [ `Create` ](xref:Xamarin.Forms.Keyboard.Create*) método generador que puede usarse para personalizar un teclado mediante la especificación del comportamiento de mayúsculas y minúsculas, corrector ortográfico y sugerencias. Los valores de enumeración [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) se especifican como argumentos para el método, con la devolución de un `Keyboard` personalizado. La enumeración `KeyboardFlags` contiene los valores siguientes:

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

El código de C# equivalente es:

```csharp
var editor = new Editor();
editor.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

### <a name="enabling-and-disabling-spell-checking"></a>Habilitar y deshabilitar el corrector ortográfico

El [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propiedad controla si corrector ortográfico está habilitado. De forma predeterminada, la propiedad se establece en `true`. Cuando el usuario escriba texto, se indican los errores de ortografía.

Sin embargo, algunos escenarios de entrada de texto, como escribir un nombre de usuario, corrector ortográfico ofrece una experiencia negativa de modo que deben deshabilitarse estableciendo el [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propiedad `false`:

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Cuando el [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propiedad está establecida en `false`y no se usa un teclado personalizado, se deshabilitará el corrector ortográfico nativo. Sin embargo, si un [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) ha sido conjunto que deshabilita el corrector comprobación, como [ `Keyboard.Chat` ](xref:Xamarin.Forms.Keyboard.Chat), el `IsSpellCheckEnabled` se omite. Por lo tanto, no se puede usar la propiedad para habilitar el corrector ortográfico para un `Keyboard` que deshabilita de forma explícita.

### <a name="enabling-and-disabling-text-prediction"></a>Habilitación y deshabilitación de la predicción de texto

La `IsTextPredictionEnabled` propiedad controla si está habilitada la predicción de texto y la corrección de texto automática. De forma predeterminada, la propiedad se establece en `true`. Cuando el usuario escriba texto, se presentan las predicciones de word.

Sin embargo, en algunos escenarios de entrada de texto, como la especificación de un nombre de usuario, la predicción de texto y la corrección automática de texto proporcionan `IsTextPredictionEnabled` una experiencia `false`negativa y se deben deshabilitar estableciendo la propiedad en:

```xaml
<Editor ... IsTextPredictionEnabled="false" />
```

```csharp
var editor = new Editor { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> Cuando la `IsTextPredictionEnabled` propiedad se establece en `false`y no se usa un teclado personalizado, se deshabilita la predicción de texto y la corrección de texto automática. Sin embargo, si un [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) se ha establecido que deshabilita la predicción de texto, el `IsTextPredictionEnabled` se omite. Por lo tanto, no se puede usar la propiedad para habilitar la predicción de texto para un `Keyboard` que deshabilita de forma explícita.

### <a name="colors"></a>Colores

`Editor` puede establecerse para usar un color de fondo a través de la `BackgroundColor` propiedad. Atención especial es necesaria para asegurarse de que los colores se podrán usar en cada plataforma. Dado que cada plataforma tiene distintos valores predeterminados para el color de texto, deberá establecer un color de fondo personalizado para cada plataforma. Consulte [trabajar con ajustes de la plataforma](~/xamarin-forms/platform/device.md) para obtener más información sobre la optimización de la interfaz de usuario para cada plataforma.

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

![](editor-images/textbackgroundcolor.png "Editor de ejemplo BackgroundColor")

Asegúrese de que los colores de fondo y de texto que elija puede utilizar en cada plataforma y no interferir con cualquier texto de marcador de posición.

## <a name="interactivity"></a>Interactividad

`Editor` expone dos eventos:

- [TextChanged](xref:Xamarin.Forms.Editor.TextChanged) &ndash; se genera cuando cambia el texto en el editor. Proporciona el texto antes y después del cambio.
- [Completado](xref:Xamarin.Forms.Editor.Completed) &ndash; se genera cuando el usuario ha finalizado la entrada presionando la tecla ENTRAR del teclado.

> [!NOTE]
> La [`VisualElement`](xref:Xamarin.Forms.VisualElement) clase, de la [`Entry`](xref:Xamarin.Forms.Entry) que hereda, también tiene [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) eventos [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) y.

### <a name="completed"></a>Completada

El `Completed` evento sirve para reaccionar a la finalización de una interacción con un `Editor`. `Completed`se genera cuando el usuario finaliza la entrada con un campo escribiendo la tecla RETURN en el teclado (o presionando la tecla TAB en UWP). El controlador para el evento es un controlador de eventos genéricos, tomando el remitente y `EventArgs`:

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

El `TextChanged` evento sirve para reaccionar ante un cambio en el contenido de un campo.

`TextChanged` se produce cada vez que el `Text` de la `Editor` cambios. El controlador para el evento toma una instancia de `TextChangedEventArgs`. `TextChangedEventArgs` proporciona acceso a los valores antiguos y nuevos de la `Editor` `Text` a través de la `OldTextValue` y `NewTextValue` propiedades:

```csharp
void EditorTextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

En el código y XAML, se puede suscribir el evento completado para:

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

- [Texto (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Editor de API](xref:Xamarin.Forms.Editor)
