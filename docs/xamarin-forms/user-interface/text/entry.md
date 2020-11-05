---
title: Xamarin.Forms Movimientos
description: En este artículo se explica cómo usar la Xamarin.Forms clase entry para aceptar texto de una sola línea o la entrada de contraseña en una aplicación.
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 625dd57d1f84b95cef1c6513ae832af805bf565a
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375036"
---
# <a name="no-locxamarinforms-entry"></a>Xamarin.Forms Movimientos

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-text)

Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry) Se utiliza para la entrada de texto de una sola línea. `Entry`, Al igual que la [`Editor`](xref:Xamarin.Forms.Editor) vista, admite varios tipos de teclado. Además, `Entry` se puede usar como un campo de contraseña.

## <a name="set-and-read-text"></a>Establecer y leer texto

`Entry`, Al igual que otras vistas de presentación de texto, expone la [`Text`](xref:Xamarin.Forms.InputView.Text) propiedad. Esta propiedad se puede utilizar para establecer y leer el texto presentado por `Entry` . En el ejemplo siguiente se muestra cómo establecer la `Text` propiedad en XAML:

```xaml
<Entry Text="I am an Entry" />
```

En C#:

```csharp
var MyEntry = new Entry { Text = "I am an Entry" };
```

Para leer texto, acceda a la `Text` propiedad en C#:

```csharp
var text = MyEntry.Text;
```

## <a name="set-placeholder-text"></a>Establecer texto de marcador de posición

[`Entry`](xref:Xamarin.Forms.Entry)Se puede establecer para mostrar el texto del marcador de posición cuando no se almacena la entrada del usuario. Esto se logra estableciendo la [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) propiedad en `string` y, a menudo, se usa para indicar el tipo de contenido adecuado para `Entry` . Además, el color del texto del marcador de posición se puede controlar estableciendo la [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) propiedad en [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<Entry Placeholder="Username" PlaceholderColor="Olive" />
```

```csharp
var entry = new Entry { Placeholder = "Username", PlaceholderColor = Color.Olive };
```

> [!NOTE]
> El ancho de un `Entry` se puede definir estableciendo su `WidthRequest` propiedad. No dependa del ancho de `Entry` que se esté definiendo según el valor de su `Text` propiedad.

## <a name="prevent-text-entry"></a>Impedir la entrada de texto

Se puede impedir que los usuarios modifiquen el texto en un estableciendo [`Entry`](xref:Xamarin.Forms.Entry) la `IsReadOnly` propiedad, que tiene un valor predeterminado de `false` , en `true` :

```xaml
<Entry Text="This is a read-only Entry"
       IsReadOnly="true" />
```

```csharp
var entry = new Entry { Text = "This is a read-only Entry", IsReadOnly = true });
```

> [!NOTE]
> La `IsReadonly` propiedad no modifica la apariencia visual de un [`Entry`](xref:Xamarin.Forms.Entry) , a diferencia de la `IsEnabled` propiedad que también cambia la apariencia visual del `Entry` a gris.

## <a name="transform-text"></a>Transformar texto

[`Entry`](xref:Xamarin.Forms.Entry)Puede transformar el uso de mayúsculas y minúsculas de su texto, almacenado en la `Text` propiedad, estableciendo la `TextTransform` propiedad en un valor de la `TextTransform` enumeración. Esta enumeración tiene cuatro valores:

- `None` indica que el texto no se transformará.
- `Default` indica que se utilizará el comportamiento predeterminado para la plataforma. Este es el valor predeterminado de la propiedad `TextTransform`.
- `Lowercase` indica que el texto se transformará a minúsculas.
- `Uppercase` indica que el texto se transformará en mayúsculas.

En el ejemplo siguiente se muestra cómo transformar texto a mayúsculas:

```xaml
<Entry Text="This text will be displayed in uppercase."
       TextTransform="Uppercase" />
```

El código de C# equivalente es el siguiente:

```csharp
Entry entry = new Entry
{
    Text = "This text will be displayed in uppercase.",
    TextTransform = TextTransform.Uppercase
};
```

## <a name="limit-input-length"></a>Limitar la longitud de entrada

La [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) propiedad se puede utilizar para limitar la longitud de entrada permitida para [`Entry`](xref:Xamarin.Forms.Entry) . Esta propiedad debe establecerse en un entero positivo:

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

Un [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) valor de propiedad de 0 indica que no se permitirá ninguna entrada, y un valor de `int.MaxValue` , que es el valor predeterminado para [`Entry`](xref:Xamarin.Forms.Entry) , indica que no hay ningún límite efectivo en el número de caracteres que se pueden escribir.

## <a name="character-spacing"></a>espaciado entre caracteres

El espaciado de caracteres se puede aplicar a [`Entry`](xref:Xamarin.Forms.Entry) si se establece la `Entry.CharacterSpacing` propiedad en un `double` valor:

```xaml
<Entry ...
       CharacterSpacing="10" />
```

El código de C# equivalente es el siguiente:

```csharp
Entry entry = new Entry { CharacterSpacing = 10 };
```

El resultado es que los caracteres del texto mostrados por [`Entry`](xref:Xamarin.Forms.Entry) están separados por las `CharacterSpacing` unidades independientes del dispositivo.

> [!NOTE]
> El `CharacterSpacing` valor de la propiedad se aplica al texto mostrado por `Text` las `Placeholder` propiedades y.

## <a name="password-fields"></a>Campos de contraseña

`Entry` proporciona la `IsPassword` propiedad. Cuando `IsPassword` es `true` , el contenido del campo se presentará como círculos negros:

En XAML:

```xaml
<Entry IsPassword="true" />
```

En C#:

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![Ejemplo de IsPassword de entrada](entry-images/password.png)

Los marcadores de posición se pueden usar con instancias de `Entry` que están configuradas como campos de contraseña:

En XAML:

```xaml
<Entry IsPassword="true" Placeholder="Password" />
```

En C#:

```csharp
var MyEntry = new Entry { IsPassword = true, Placeholder = "Password" };
```

![Ejemplo de IsPassword de entrada y marcador de posición](entry-images/passwordplaceholder.png)

## <a name="set-the-cursor-position-and-text-selection-length"></a>Establecer la posición del cursor y la longitud de la selección de texto

La [`CursorPosition`](xref:Xamarin.Forms.Entry.CursorPosition) propiedad se puede utilizar para devolver o establecer la posición en la que se insertará el siguiente carácter en la cadena almacenada en la [`Text`](xref:Xamarin.Forms.InputView.Text) propiedad:

```xaml
<Entry Text="Cursor position set" CursorPosition="5" />
```

```csharp
var entry = new Entry { Text = "Cursor position set", CursorPosition = 5 };
```

El valor predeterminado de la [`CursorPosition`](xref:Xamarin.Forms.Entry.CursorPosition) propiedad es 0, que indica que el texto se insertará al principio del `Entry` .

Además, la [`SelectionLength`](xref:Xamarin.Forms.Entry.SelectionLength) propiedad se puede utilizar para devolver o establecer la longitud de la selección de texto dentro de `Entry` :

```xaml
<Entry Text="Cursor position and selection length set" CursorPosition="2" SelectionLength="10" />
```

```csharp
var entry = new Entry { Text = "Cursor position and selection length set", CursorPosition = 2, SelectionLength = 10 };
```

El valor predeterminado de la [`SelectionLength`](xref:Xamarin.Forms.Entry.SelectionLength) propiedad es 0, que indica que no se ha seleccionado ningún texto.

## <a name="display-a-clear-button"></a>Mostrar un botón borrar

La `ClearButtonVisibility` propiedad se puede utilizar para controlar si [`Entry`](xref:Xamarin.Forms.Entry) muestra un botón borrar, lo que permite al usuario borrar el texto. Esta propiedad se debe establecer en un `ClearButtonVisibility` miembro de enumeración:

- `Never` indica que nunca se mostrará un botón CLEAR. Este es el valor predeterminado de la propiedad `Entry.ClearButtonVisibility`.
- `WhileEditing` indica que se mostrará un botón borrar en [`Entry`](xref:Xamarin.Forms.Entry) , mientras que tiene el foco y el texto.

En el ejemplo siguiente se muestra cómo establecer la propiedad en XAML:

```xaml
<Entry Text="Xamarin.Forms"
       ClearButtonVisibility="WhileEditing" />
```

El código de C# equivalente es el siguiente:

```csharp
var entry = new Entry { Text = "Xamarin.Forms", ClearButtonVisibility = ClearButtonVisibility.WhileEditing };
```

Las capturas de pantallas siguientes muestran un [`Entry`](xref:Xamarin.Forms.Entry) con el botón Borrar habilitado:

![Captura de pantalla de una entrada con un botón borrar, en iOS y Android](entry-images/entry-clear-button.png)

## <a name="customize-the-keyboard"></a>Personalizar el teclado

El teclado que se presenta cuando los usuarios interactúan con [`Entry`](xref:Xamarin.Forms.Entry) se puede establecer mediante programación a través de la [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) propiedad, en una de las siguientes propiedades de la [`Keyboard`](xref:Xamarin.Forms.Keyboard) clase:

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
<Entry Keyboard="Chat" />
```

El código de C# equivalente es el siguiente:

```csharp
var entry = new Entry { Keyboard = Keyboard.Chat };
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
<Entry Placeholder="Enter text here">
    <Entry.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </Entry.Keyboard>
</Entry>
```

El código de C# equivalente es el siguiente:

```csharp
var entry = new Entry { Placeholder = "Enter text here" };
entry.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

### <a name="customize-the-return-key"></a>Personalizar la tecla entrar

La apariencia de la tecla retorno en el teclado para software, que se muestra cuando [`Entry`](xref:Xamarin.Forms.Entry) tiene el foco, puede personalizarse estableciendo la [`ReturnType`](xref:Xamarin.Forms.Entry.ReturnType) propiedad en un valor de la [`ReturnType`](xref:Xamarin.Forms.ReturnType) enumeración:

- [`Default`](xref:Xamarin.Forms.ReturnType.Default) : indica que no se requiere ninguna clave de devolución específica y que se usará el valor predeterminado de la plataforma.
- [`Done`](xref:Xamarin.Forms.ReturnType.Done) : indica una tecla de retorno "Done".
- [`Go`](xref:Xamarin.Forms.ReturnType.Go) : indica una tecla de retorno "Go".
- [`Next`](xref:Xamarin.Forms.ReturnType.Next) : indica una clave de retorno "Next".
- [`Search`](xref:Xamarin.Forms.ReturnType.Search) : indica una clave de "búsqueda".
- [`Send`](xref:Xamarin.Forms.ReturnType.Send) : indica una clave de "envío".

En el siguiente ejemplo de XAML se muestra cómo establecer la clave de devolución:

```xaml
<Entry ReturnType="Send" />
```

El código de C# equivalente es el siguiente:

```csharp
var entry = new Entry { ReturnType = ReturnType.Send };
```

> [!NOTE]
> La apariencia exacta de la tecla RETURN depende de la plataforma. En iOS, la tecla RETURN es un botón basado en texto. Sin embargo, en las plataformas Android y universal de Windows, la tecla RETURN es un botón basado en iconos.

Cuando se presiona la tecla entrar, el [`Completed`](xref:Xamarin.Forms.Entry.Completed) evento se activa y `ICommand` se ejecuta cualquier valor especificado por la [`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand) propiedad. Además, cualquier `object` especificado por la [`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter) propiedad se pasará a `ICommand` como un parámetro. Para obtener más información sobre los comandos, consulte [The Command Interface](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) (La interfaz de comandos).

## <a name="enable-and-disable-spell-checking"></a>Habilitar y deshabilitar la revisión ortográfica

La [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propiedad controla si está habilitada la revisión ortográfica. De forma predeterminada, la propiedad se establece en `true` . Cuando el usuario escribe texto, se indican errores ortográficos.

Sin embargo, en algunos escenarios de entrada de texto, como la especificación de un nombre de usuario, la revisión ortográfica proporciona una experiencia negativa y debe deshabilitarse estableciendo la [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propiedad en `false` :

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Cuando la [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propiedad está establecida en `false` y no se usa un teclado personalizado, se deshabilitará el corrector ortográfico nativo. Sin embargo, si se ha [`Keyboard`](xref:Xamarin.Forms.Keyboard) establecido un que deshabilita la revisión ortográfica, como [`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat) , `IsSpellCheckEnabled` se omite la propiedad. Por consiguiente, la propiedad no se puede usar para habilitar la revisión ortográfica de un `Keyboard` que lo deshabilita explícitamente.

## <a name="enable-and-disable-text-prediction"></a>Habilitar y deshabilitar la predicción de texto

La [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) propiedad controla si está habilitada la predicción de texto y la corrección de texto automática. De forma predeterminada, la propiedad se establece en `true` . A medida que el usuario escribe texto, se presentan las predicciones de palabras.

Sin embargo, en algunos escenarios de entrada de texto, como la especificación de un nombre de usuario, la predicción de texto y la corrección automática de texto proporcionan una experiencia negativa y se deben deshabilitar estableciendo la [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) propiedad en `false` :

```xaml
<Entry ... IsTextPredictionEnabled="false" />
```

```csharp
var entry = new Entry { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> Cuando la [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) propiedad se establece en `false` y no se usa un teclado personalizado, se deshabilita la predicción de texto y la corrección de texto automática. Sin embargo, si se ha [`Keyboard`](xref:Xamarin.Forms.Keyboard) establecido un que deshabilita la predicción de texto, `IsTextPredictionEnabled` se omite la propiedad. Por consiguiente, la propiedad no se puede usar para habilitar la predicción de texto para un `Keyboard` que lo deshabilita explícitamente.

## <a name="colors"></a>Colores

La entrada se puede establecer para utilizar un fondo personalizado y colores de texto a través de las siguientes propiedades enlazables:

- **TextColor** &ndash; establece el color del texto.
- **BackgroundColor** &ndash; establece el color que se muestra detrás del texto.

Es necesario tener especial cuidado para asegurarse de que se puedan usar los colores en cada plataforma. Dado que cada plataforma tiene valores predeterminados diferentes para los colores de texto y de fondo, a menudo necesitará establecer ambos si establece uno.

Use el código siguiente para establecer el color del texto de una entrada:

En XAML:

```xaml
<Entry TextColor="Green" />
```

En C#:

```csharp
var entry = new Entry();
entry.TextColor = Color.Green;
```

![Ejemplo de TextColor de entrada](entry-images/textcolor.png)

Tenga en cuenta que el marcador de posición no se ve afectado por el especificado `TextColor` .

Para establecer el color de fondo en XAML:

```xaml
<Entry BackgroundColor="#2c3e50" />
```

En C#:

```csharp
var entry = new Entry();
entry.BackgroundColor = Color.FromHex("#2c3e50");
```

![Ejemplo de BackgroundColor de entrada](entry-images/textbackgroundcolor.png)

Asegúrese de que los colores de fondo y de texto que elija se pueden usar en cada plataforma y no oculte ningún texto de marcador de posición.

## <a name="events-and-interactivity"></a>Eventos e interactividad

La entrada expone dos eventos:

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)se &ndash; genera cuando el texto cambia en la entrada. Proporciona el texto antes y después del cambio.
- [`Completed`](xref:Xamarin.Forms.Entry.Completed)&ndash;se genera cuando el usuario ha finalizado la entrada presionando la tecla RETURN del teclado.

> [!NOTE]
> La [`VisualElement`](xref:Xamarin.Forms.VisualElement) clase, de la que [`Entry`](xref:Xamarin.Forms.Entry) hereda, también tiene [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) eventos y.

### <a name="completed"></a>Completado

El `Completed` evento se utiliza para reaccionar a la finalización de una interacción con una entrada. `Completed` se genera cuando el usuario finaliza la entrada con un campo presionando la tecla entrar en el teclado (o presionando la tecla TAB en UWP). El controlador del evento es un controlador de eventos genérico que toma el remitente y `EventArgs` :

```csharp
void Entry_Completed (object sender, EventArgs e)
{
    var text = ((Entry)sender).Text; //cast sender to access the properties of the Entry
}
```

Se puede suscribir el evento completado a en XAML:

```xaml
<Entry Completed="Entry_Completed" />
```

y C#:

```csharp
var entry = new Entry ();
entry.Completed += Entry_Completed;
```

Una vez que [`Completed`](xref:Xamarin.Forms.Entry.Completed) se desencadena el evento, `ICommand` se ejecuta cualquier especificado por la [`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand) propiedad, con el `object` especificado por la [`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter) propiedad que se pasa a `ICommand` .

### <a name="textchanged"></a>TextChanged

El `TextChanged` evento se utiliza para reaccionar a un cambio en el contenido de un campo.

`TextChanged` se genera cuando `Text` cambia el de `Entry` . El controlador del evento toma una instancia de `TextChangedEventArgs` . `TextChangedEventArgs` proporciona acceso a los valores antiguos y nuevos de `Entry` `Text` a través de `OldTextValue` las `NewTextValue` propiedades y:

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

El `TextChanged` evento se puede suscribir a en XAML:

```xaml
<Entry TextChanged="Entry_TextChanged" />
```

y C#:

```csharp
var entry = new Entry ();
entry.TextChanged += Entry_TextChanged;
```

## <a name="related-links"></a>Vínculos relacionados

- [Texto (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [API de las entradas](xref:Xamarin.Forms.Entry)