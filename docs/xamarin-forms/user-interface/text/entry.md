---
title: Xamarin.FormsMovimientos
description: En este artículo se explica cómo usar la Xamarin.Forms clase entry para aceptar texto de una sola línea o la entrada de contraseña en una aplicación.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5c17d6a106474c4c5b183bd41923533ffc95789b
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136206"
---
# <a name="xamarinforms-entry"></a>Xamarin.FormsMovimientos

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Entrada de texto o de contraseña de una sola línea_

Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry) Se utiliza para la entrada de texto de una sola línea. `Entry`, Al igual que la [`Editor`](xref:Xamarin.Forms.Editor) vista, admite varios tipos de teclado. Además, `Entry` se puede usar como un campo de contraseña.

## <a name="display-customization"></a>Personalizar la visualización

### <a name="setting-and-reading-text"></a>Establecer y leer texto

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

### <a name="setting-placeholder-text"></a>Establecer el texto del marcador de posición

[`Entry`](xref:Xamarin.Forms.Entry)Se puede establecer para mostrar el texto del marcador de posición cuando no se almacena la entrada del usuario. Esto se logra estableciendo la [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) propiedad en `string` y, a menudo, se usa para indicar el tipo de contenido adecuado para `Entry` . Además, el color del texto del marcador de posición se puede controlar estableciendo la [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) propiedad en [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<Entry Placeholder="Username" PlaceholderColor="Olive" />
```

```csharp
var entry = new Entry { Placeholder = "Username", PlaceholderColor = Color.Olive };
```

> [!NOTE]
> El ancho de un `Entry` se puede definir estableciendo su `WidthRequest` propiedad. No dependa del ancho de `Entry` que se esté definiendo según el valor de su `Text` propiedad.

### <a name="preventing-text-entry"></a>impedir la entrada de texto

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

### <a name="limiting-input-length"></a>Limitar la longitud de entrada

La [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) propiedad se puede utilizar para limitar la longitud de entrada permitida para [`Entry`](xref:Xamarin.Forms.Entry) . Esta propiedad debe establecerse en un entero positivo:

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

Un [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) valor de propiedad de 0 indica que no se permitirá ninguna entrada, y un valor de `int.MaxValue` , que es el valor predeterminado para [`Entry`](xref:Xamarin.Forms.Entry) , indica que no hay ningún límite efectivo en el número de caracteres que se pueden escribir.

### <a name="character-spacing"></a>espaciado entre caracteres

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

### <a name="password-fields"></a>Campos de contraseña

`Entry`proporciona la `IsPassword` propiedad. Cuando `IsPassword` es `true` , el contenido del campo se presentará como círculos negros:

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

### <a name="setting-the-cursor-position-and-text-selection-length"></a>Establecer la posición del cursor y la longitud de la selección de texto

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

### <a name="displaying-a-clear-button"></a>Mostrar un botón borrar

La `ClearButtonVisibility` propiedad se puede utilizar para controlar si [`Entry`](xref:Xamarin.Forms.Entry) muestra un botón borrar, lo que permite al usuario borrar el texto. Esta propiedad se debe establecer en un `ClearButtonVisibility` miembro de enumeración:

- `Never`indica que nunca se mostrará un botón CLEAR. Este es el valor predeterminado de la propiedad `Entry.ClearButtonVisibility`.
- `WhileEditing`indica que se mostrará un botón borrar en [`Entry`](xref:Xamarin.Forms.Entry) , mientras que tiene el foco y el texto.

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

### <a name="customizing-the-keyboard"></a>Personalización del teclado

El teclado que se presenta cuando los usuarios interactúan con [`Entry`](xref:Xamarin.Forms.Entry) se puede establecer mediante programación a través de la [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) propiedad, en una de las siguientes propiedades de la [`Keyboard`](xref:Xamarin.Forms.Keyboard) clase:

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat): se usa para texto y lugares en los que los Emoji son útiles.
- [`Default`](xref:Xamarin.Forms.Keyboard.Default): teclado predeterminado.
- [`Email`](xref:Xamarin.Forms.Keyboard.Email): se usa al escribir direcciones de correo electrónico.
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric): se usa al escribir números.
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain): se usa al escribir texto, sin [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) especificar ningún parámetro.
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone): se usa al especificar números de teléfono.
- [`Text`](xref:Xamarin.Forms.Keyboard.Text): se usa al escribir texto.
- [`Url`](xref:Xamarin.Forms.Keyboard.Url): se usa para especificar rutas de acceso de archivo & direcciones web.

Esto se puede lograr en XAML de la siguiente manera:

```xaml
<Entry Keyboard="Chat" />
```

El código de C# equivalente es el siguiente:

```csharp
var entry = new Entry { Keyboard = Keyboard.Chat };
```

Puede encontrar ejemplos de cada teclado en nuestro repositorio de [recetas](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry) .

La [`Keyboard`](xref:Xamarin.Forms.Keyboard) clase también tiene una [`Create`](xref:Xamarin.Forms.Keyboard.Create*) Factory Method que se puede utilizar para personalizar un teclado mediante la especificación de mayúsculas, corrector ortográfico y comportamiento de sugerencias. [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags)los valores de enumeración se especifican como argumentos para el método, con un personalizado `Keyboard` que se va a devolver. La enumeración `KeyboardFlags` contiene los valores siguientes:

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None): no se agregan características al teclado.
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence): indica que la primera letra de la primera palabra de cada oración introducida se pondrá en mayúsculas automáticamente.
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck): indica que se realizará la revisión ortográfica en el texto escrito.
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions): indica que se ofrecerán finalizaciones de palabras en el texto escrito.
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord): indica que la primera letra de cada palabra se pondrá en mayúsculas automáticamente.
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter): indica que se pone en mayúsculas todos los caracteres automáticamente.
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone): indica que no se producirá ninguna capitalización automática.
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All): indica que el corrector ortográfico, las finalizaciones de palabras y las mayúsculas de frases se producirán en el texto escrito.

En el siguiente ejemplo de código XAML se muestra cómo personalizar el predeterminado [`Keyboard`](xref:Xamarin.Forms.Keyboard) para ofrecer finalizaciones de palabras y poner en mayúsculas todos los caracteres especificados:

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

#### <a name="customizing-the-return-key"></a>Personalización de la tecla RETURN

La apariencia de la tecla retorno en el teclado para software, que se muestra cuando [`Entry`](xref:Xamarin.Forms.Entry) tiene el foco, puede personalizarse estableciendo la [`ReturnType`](xref:Xamarin.Forms.Entry.ReturnType) propiedad en un valor de la [`ReturnType`](xref:Xamarin.Forms.ReturnType) enumeración:

- [`Default`](xref:Xamarin.Forms.ReturnType.Default): indica que no se requiere ninguna clave de devolución específica y que se usará el valor predeterminado de la plataforma.
- [`Done`](xref:Xamarin.Forms.ReturnType.Done): indica una tecla de retorno "Done".
- [`Go`](xref:Xamarin.Forms.ReturnType.Go): indica una tecla de retorno "Go".
- [`Next`](xref:Xamarin.Forms.ReturnType.Next): indica una clave de retorno "Next".
- [`Search`](xref:Xamarin.Forms.ReturnType.Search): indica una clave de "búsqueda".
- [`Send`](xref:Xamarin.Forms.ReturnType.Send): indica una clave de "envío".

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

### <a name="enabling-and-disabling-spell-checking"></a>Habilitar y deshabilitar la revisión ortográfica

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

### <a name="enabling-and-disabling-text-prediction"></a>habilitar y deshabilitar la predicción de texto

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

### <a name="colors"></a>Colores

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

El `Completed` evento se utiliza para reaccionar a la finalización de una interacción con una entrada. `Completed`se genera cuando el usuario finaliza la entrada con un campo presionando la tecla entrar en el teclado (o presionando la tecla TAB en UWP). El controlador del evento es un controlador de eventos genérico que toma el remitente y `EventArgs` :

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

`TextChanged`se genera cuando `Text` cambia el de `Entry` . El controlador del evento toma una instancia de `TextChangedEventArgs` . `TextChangedEventArgs`proporciona acceso a los valores antiguos y nuevos de `Entry` `Text` a través de `OldTextValue` las `NewTextValue` propiedades y:

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

- [Texto (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [API de las entradas](xref:Xamarin.Forms.Entry)
