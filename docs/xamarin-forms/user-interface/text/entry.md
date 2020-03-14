---
title: Entrada de Xamarin.Forms
description: En este artículo se explica cómo usar la clase de entrada de Xamarin.Forms para aceptar el texto de una línea o una entrada de contraseña en una aplicación.
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2019
ms.openlocfilehash: c7daad8898d3048f10c9489ee4e2c78f147c6e52
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79306618"
---
# <a name="xamarinforms-entry"></a>Entrada de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Entrada de texto o de contraseña de una sola línea_

La [`Entry`](xref:Xamarin.Forms.Entry) de Xamarin. Forms se usa para la entrada de texto de una sola línea. La `Entry`, al igual que la vista de [`Editor`](xref:Xamarin.Forms.Editor) , admite varios tipos de teclado. Además, el `Entry` se puede usar como campo de contraseña.

## <a name="display-customization"></a>Personalización de la presentación

### <a name="setting-and-reading-text"></a>Establecimiento y lectura de texto

La `Entry`, al igual que otras vistas de presentación de texto, expone la propiedad [`Text`](xref:Xamarin.Forms.InputView.Text) . Esta propiedad se puede utilizar para establecer y leer el texto presentado por el `Entry`. En el ejemplo siguiente se muestra cómo establecer la propiedad `Text` en XAML:

```xaml
<Entry Text="I am an Entry" />
```

En C#:

```csharp
var MyEntry = new Entry { Text = "I am an Entry" };
```

Para leer texto, acceda a la propiedad `Text` C#en:

```csharp
var text = MyEntry.Text;
```

### <a name="setting-placeholder-text"></a>Establecer el texto de marcador de posición

El [`Entry`](xref:Xamarin.Forms.Entry) se puede establecer para mostrar el texto del marcador de posición cuando no almacena los datos proporcionados por el usuario. Esto se consigue estableciendo la propiedad [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) en un `string`y a menudo se usa para indicar el tipo de contenido adecuado para el `Entry`. Además, el color del texto del marcador de posición se puede controlar estableciendo la propiedad [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) en un [`Color`](xref:Xamarin.Forms.Color):

```xaml
<Entry Placeholder="Username" PlaceholderColor="Olive" />
```

```csharp
var entry = new Entry { Placeholder = "Username", PlaceholderColor = Color.Olive };
```

> [!NOTE]
> El ancho de un `Entry` se puede definir estableciendo su propiedad `WidthRequest`. No dependa del ancho de una `Entry` que se está definiendo en función del valor de su propiedad `Text`.

### <a name="preventing-text-entry"></a>Impedir la entrada de texto

Se puede impedir que los usuarios modifiquen el texto en un [`Entry`](xref:Xamarin.Forms.Entry) estableciendo la propiedad `IsReadOnly`, que tiene un valor predeterminado de `false`, para `true`:

```xaml
<Entry Text="This is a read-only Entry"
       IsReadOnly="true" />
```

```csharp
var entry = new Entry { Text = "This is a read-only Entry", IsReadOnly = true });
```

> [!NOTE]
> La propiedad `IsReadonly` no modifica la apariencia visual de un [`Entry`](xref:Xamarin.Forms.Entry), a diferencia de la propiedad `IsEnabled`, que también cambia la apariencia visual del `Entry` a gris.

### <a name="limiting-input-length"></a>Limitar la longitud de entrada

La propiedad [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) se puede utilizar para limitar la longitud de entrada permitida para el [`Entry`](xref:Xamarin.Forms.Entry). Esta propiedad debe establecerse en un entero positivo:

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

Un valor de propiedad [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) de 0 indica que no se permitirá ninguna entrada y un valor de `int.MaxValue`, que es el valor predeterminado para un [`Entry`](xref:Xamarin.Forms.Entry), indica que no hay ningún límite efectivo en el número de caracteres que se pueden escribir.

### <a name="character-spacing"></a>Espaciado de caracteres

El espaciado de caracteres se puede aplicar a un [`Entry`](xref:Xamarin.Forms.Entry) estableciendo la propiedad `Entry.CharacterSpacing` en un valor `double`:

```xaml
<Entry ...
       CharacterSpacing="10" />
```

El código de C# equivalente es el siguiente:

```csharp
Entry entry = new Entry { CharacterSpacing = 10 };
```

El resultado es que los caracteres del texto que muestra el [`Entry`](xref:Xamarin.Forms.Entry) se espacian `CharacterSpacing` unidades independientes del dispositivo.

> [!NOTE]
> El valor de la propiedad `CharacterSpacing` se aplica al texto que se muestra en las propiedades `Text` y `Placeholder`.

### <a name="password-fields"></a>Campos de contraseña

`Entry` proporciona la propiedad `IsPassword`. Cuando `IsPassword` se `true`, el contenido del campo se presentará como círculos negros:

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

### <a name="setting-the-cursor-position-and-text-selection-length"></a>Establecer la posición del Cursor y la longitud de la selección de texto

La propiedad [`CursorPosition`](xref:Xamarin.Forms.Entry.CursorPosition) se puede utilizar para devolver o establecer la posición en la que se insertará el siguiente carácter en la cadena almacenada en la propiedad [`Text`](xref:Xamarin.Forms.InputView.Text) :

```xaml
<Entry Text="Cursor position set" CursorPosition="5" />
```

```csharp
var entry = new Entry { Text = "Cursor position set", CursorPosition = 5 };
```

El valor predeterminado de la propiedad [`CursorPosition`](xref:Xamarin.Forms.Entry.CursorPosition) es 0, que indica que el texto se insertará al principio del `Entry`.

Además, la propiedad [`SelectionLength`](xref:Xamarin.Forms.Entry.SelectionLength) se puede utilizar para devolver o establecer la longitud de la selección de texto dentro de la `Entry`:

```xaml
<Entry Text="Cursor position and selection length set" CursorPosition="2" SelectionLength="10" />
```

```csharp
var entry = new Entry { Text = "Cursor position and selection length set", CursorPosition = 2, SelectionLength = 10 };
```

El valor predeterminado de la propiedad [`SelectionLength`](xref:Xamarin.Forms.Entry.SelectionLength) es 0, que indica que no se ha seleccionado ningún texto.

### <a name="displaying-a-clear-button"></a>Mostrar un botón borrar

La propiedad `ClearButtonVisibility` se puede utilizar para controlar si un [`Entry`](xref:Xamarin.Forms.Entry) muestra un botón borrar, lo que permite al usuario borrar el texto. Esta propiedad se debe establecer en un miembro de enumeración de `ClearButtonVisibility`:

- `Never` indica que nunca se mostrará un botón CLEAR. Este es el valor predeterminado de la propiedad `Entry.ClearButtonVisibility`.
- `WhileEditing` indica que se mostrará un botón borrar en el [`Entry`](xref:Xamarin.Forms.Entry), mientras que tiene el foco y el texto.

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

### <a name="customizing-the-keyboard"></a>Personalizar el teclado

El teclado que se muestra cuando los usuarios interactúan con un [`Entry`](xref:Xamarin.Forms.Entry) se pueden establecer mediante programación a través de la propiedad [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) , en una de las siguientes propiedades de la clase [`Keyboard`](xref:Xamarin.Forms.Keyboard) :

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

#### <a name="customizing-the-return-key"></a>Personalización de la tecla ENTRAR

La apariencia de la tecla entrar en el teclado en pantalla, que se muestra cuando un [`Entry`](xref:Xamarin.Forms.Entry) tiene el foco, puede personalizarse estableciendo la propiedad [`ReturnType`](xref:Xamarin.Forms.Entry.ReturnType) en un valor de la enumeración [`ReturnType`](xref:Xamarin.Forms.ReturnType) :

- [`Default`](xref:Xamarin.Forms.ReturnType.Default) : indica que no se requiere ninguna clave de devolución específica y que se usará el valor predeterminado de la plataforma.
- [`Done`](xref:Xamarin.Forms.ReturnType.Done) : indica una tecla de retorno "Done".
- [`Go`](xref:Xamarin.Forms.ReturnType.Go) : indica una tecla de retorno "Go".
- [`Next`](xref:Xamarin.Forms.ReturnType.Next) : indica una clave de retorno "Next".
- [`Search`](xref:Xamarin.Forms.ReturnType.Search) : indica una clave de "búsqueda".
- [`Send`](xref:Xamarin.Forms.ReturnType.Send) : indica una clave de "envío".

En el siguiente ejemplo XAML se muestra cómo establecer la tecla ENTRAR:

```xaml
<Entry ReturnType="Send" />
```

El código de C# equivalente es el siguiente:

```csharp
var entry = new Entry { ReturnType = ReturnType.Send };
```

> [!NOTE]
> El aspecto exacto de la clave de valor devuelto depende de la plataforma. En iOS, la clave de valor devuelta es un botón basado en texto. Sin embargo, en plataformas de Windows Universal y Android, la clave devuelta es un botón de icono.

Cuando se presiona la tecla entrar, el evento [`Completed`](xref:Xamarin.Forms.Entry.Completed) se activa y se ejecuta cualquier `ICommand` especificado por la propiedad [`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand) . Además, los `object` especificados por la propiedad [`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter) se pasarán a la `ICommand` como un parámetro. Para obtener más información sobre los comandos, consulte [The Command Interface](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) (La interfaz de comandos).

### <a name="enabling-and-disabling-spell-checking"></a>Habilitar y deshabilitar el corrector ortográfico

La propiedad [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) controla si está habilitada la revisión ortográfica. De forma predeterminada, la propiedad se establece en `true`. Cuando el usuario escriba texto, se indican los errores de ortografía.

Sin embargo, en algunos escenarios de entrada de texto, como la especificación de un nombre de usuario, la revisión ortográfica proporciona una experiencia negativa y debe deshabilitarse estableciendo la propiedad [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) en `false`:

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Cuando la propiedad [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) está establecida en `false`y no se utiliza un teclado personalizado, se deshabilitará el corrector ortográfico nativo. Sin embargo, si se ha establecido un [`Keyboard`](xref:Xamarin.Forms.Keyboard) que deshabilita la revisión ortográfica, como [`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat), se omite la propiedad `IsSpellCheckEnabled`. Por lo tanto, la propiedad no se puede usar para habilitar la revisión ortográfica de un `Keyboard` que lo deshabilita explícitamente.

### <a name="enabling-and-disabling-text-prediction"></a>Habilitación y deshabilitación de la predicción de texto

La propiedad [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) controla si está habilitada la predicción de texto y la corrección de texto automática. De forma predeterminada, la propiedad se establece en `true`. Cuando el usuario escriba texto, se presentan las predicciones de word.

Sin embargo, en algunos escenarios de entrada de texto, como la especificación de un nombre de usuario, la predicción de texto y la corrección automática de texto proporcionan una experiencia negativa y se deben deshabilitar estableciendo la propiedad [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) en `false`:

```xaml
<Entry ... IsTextPredictionEnabled="false" />
```

```csharp
var entry = new Entry { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> Cuando la propiedad [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) está establecida en `false`y no se utiliza un teclado personalizado, se deshabilita la predicción de texto y la corrección de texto automática. Sin embargo, si se ha establecido un [`Keyboard`](xref:Xamarin.Forms.Keyboard) que deshabilita la predicción de texto, se omite la propiedad `IsTextPredictionEnabled`. Por lo tanto, la propiedad no se puede usar para habilitar la predicción de texto para un `Keyboard` que lo deshabilita explícitamente.

### <a name="colors"></a>Colores

Puede establecerse una entrada para usar un fondo personalizado y los colores de texto a través de las propiedades enlazables siguientes:

- **TextColor** &ndash; establece el color del texto.
- **BackgroundColor** &ndash; establece el color que se muestra detrás del texto.

Atención especial es necesaria para asegurarse de que los colores se podrán usar en cada plataforma. Dado que cada plataforma tiene distintos valores predeterminados para los colores de texto y fondo, a menudo deberá establecer ambos si establece uno.

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

Tenga en cuenta que el marcador de posición no se ve afectado por el `TextColor`especificado.

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

Tenga cuidado para asegurarse de que los colores de fondo y de texto que elija puede utilizar en cada plataforma y no interferir con cualquier texto de marcador de posición.

## <a name="events-and-interactivity"></a>Eventos e interactividad

Entrada expone dos eventos:

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) &ndash; genera cuando cambia el texto en la entrada. Proporciona el texto antes y después del cambio.
- [`Completed`](xref:Xamarin.Forms.Entry.Completed) &ndash; genera cuando el usuario finaliza la entrada presionando la tecla entrar en el teclado.

> [!NOTE]
> La clase [`VisualElement`](xref:Xamarin.Forms.VisualElement) , de la que [`Entry`](xref:Xamarin.Forms.Entry) hereda, también tiene eventos [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) y [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) .

### <a name="completed"></a>Completed

El evento `Completed` se utiliza para reaccionar a la finalización de una interacción con una entrada. `Completed` se produce cuando el usuario finaliza la entrada con un campo presionando la tecla entrar en el teclado (o presionando la tecla TAB en UWP). El controlador del evento es un controlador de eventos genérico que toma al remitente y `EventArgs`:

```csharp
void Entry_Completed (object sender, EventArgs e)
{
    var text = ((Entry)sender).Text; //cast sender to access the properties of the Entry
}
```

El evento completado se puede suscribir en XAML:

```xaml
<Entry Completed="Entry_Completed" />
```

y C#:

```csharp
var entry = new Entry ();
entry.Completed += Entry_Completed;
```

Una vez que se desencadena el evento [`Completed`](xref:Xamarin.Forms.Entry.Completed) , se ejecuta cualquier `ICommand` especificado por la propiedad [`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand) , con el `object` especificado por la propiedad [`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter) que se pasa al `ICommand`.

### <a name="textchanged"></a>TextChanged

El evento `TextChanged` se utiliza para reaccionar a un cambio en el contenido de un campo.

`TextChanged` se produce cuando cambia el `Text` de la `Entry`. El controlador del evento toma una instancia de `TextChangedEventArgs`. `TextChangedEventArgs` proporciona acceso a los valores antiguos y nuevos del `Entry` `Text` a través de las propiedades `OldTextValue` y `NewTextValue`:

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

El evento `TextChanged` se puede suscribir a en XAML:

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
