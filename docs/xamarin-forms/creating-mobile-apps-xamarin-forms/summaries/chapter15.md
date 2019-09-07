---
title: Resumen del capítulo 15. La interfaz interactiva
description: 'Crear Mobile Apps con Xamarin. Forms: Resumen del capítulo 15. La interfaz interactiva'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 1c30f87b9173d2ca4de0b2d91ad13145031e9b0a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760757"
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>Resumen del capítulo 15. La interfaz interactiva

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)

Este capítulo analiza ocho `View` derivados que permiten la interacción con el usuario.

## <a name="view-overview"></a>Ver información general

Xamarin.Forms contiene 20 clases instanciables que derivan de `View` pero no `Layout`. Seis de estos se han tratado en los capítulos anteriores:

- `Label`: [**Capítulo 2. Anatomía de una aplicación**](chapter02.md)
- `BoxView`: [**Capítulo 3. Desplazamiento de la pila**](chapter03.md)
- `Button`: [**Capítulo 6. Clics de botón**](chapter06.md)
- `Image`: [**Capítulo 13. Mapas de bits**](chapter13.md)
- `ActivityIndicator`: [**Capítulo 13. Mapas de bits**](chapter13.md)
- `ProgressBar`: [**Capítulo 14. AbsoluteLayout**](chapter14.md)

Las vistas de ocho en este capítulo eficazmente permiten al usuario interactuar con tipos de datos básicos. NET:

|Tipo de datos|Vistas|
|--- |--- |
|`Double`|[`Slider`](xref:Xamarin.Forms.Slider), [`Stepper`](xref:Xamarin.Forms.Stepper)|
|`Boolean`|[`Switch`](xref:Xamarin.Forms.Switch)|
|`String`|[`Entry`](xref:Xamarin.Forms.Entry), [`Editor`](xref:Xamarin.Forms.Editor), [`SearchBar`](xref:Xamarin.Forms.SearchBar)|
|`DateTime`|[`DatePicker`](xref:Xamarin.Forms.DatePicker), [`TimePicker`](xref:Xamarin.Forms.TimePicker)|

Estas vistas se pueden considerar como representaciones visuales de interactivas de los tipos de datos subyacente. Este concepto se explora más en el próximo capítulo, [ **capítulo 16. Enlace de datos**](chapter16.md).

Las vistas de seis restantes se tratan en los capítulos siguientes:

- `WebView`: [**Capítulo 16. Enlace de datos**](chapter16.md)
- `Picker`: [**Capítulo 19. Vistas de colección**](chapter19.md)
- `ListView`: [**Capítulo 19. Vistas de colección**](chapter19.md)
- `TableView`: [**Capítulo 19. Vistas de colección**](chapter19.md)
- `Map`: [**Capítulo 28. Ubicación y mapas**](chapter28.md)
- `OpenGLView`: No se trata en este libro (y no es compatible con las plataformas Windows)

## <a name="slider-and-stepper"></a>Control deslizante y motor paso a paso

Ambos [ `Slider` ](xref:Xamarin.Forms.Slider) y [ `Stepper` ](xref:Xamarin.Forms.Stepper) permiten al usuario elegir un valor numérico de un intervalo. El `Slider` es un intervalo continuo mientras el `Stepper` implica valores discretos.

### <a name="slider-basics"></a>Conceptos básicos del control deslizante

El [ `Slider` ](xref:Xamarin.Forms.Slider) es un valor que representa un intervalo de valores desde un mínimo de la izquierda hasta un máximo de la derecha de la barra horizontal. Define tres propiedades públicas:

- [`Value`](xref:Xamarin.Forms.Slider.Value) de tipo `double`, valor predeterminado de 0
- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) de tipo `double`, valor predeterminado de 0
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) de tipo `double`, valor predeterminado de 1

Asegurarse de que son coherentes con las propiedades enlazables apoyar estas propiedades:

- Para las tres propiedades, el [ `coerceValue` ](xref:Xamarin.Forms.BindableProperty.CoerceValueDelegate) especificado para la propiedad enlazable garantiza que el método `Value` entre `Minimum` y `Maximum`.
- El [ `validateValue` ](xref:Xamarin.Forms.BindableProperty.ValidateValueDelegate) método `MinimumProperty` devuelve `false` si `Minimum` se establece en un valor mayor o igual a `Maximum`y otros similares para `MaximumProperty`. Devolver `false` desde el `validateValue` método causas un `ArgumentException` a generarse.

`Slider` se desencadena la [ `ValueChanged` ](xref:Xamarin.Forms.Slider.ValueChanged) eventos con un [ `ValueChangedEventArgs` ](xref:Xamarin.Forms.ValueChangedEventArgs) argumento cuando el `Value` los cambios de propiedad mediante programación o cuando el usuario manipula el `Slider`.

El [ **SliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo) ejemplo muestra el uso simple de la `Slider`.

### <a name="common-pitfalls"></a>Problemas comunes

En el código y en XAML, el `Minimum` y `Maximum` propiedades se establecen en el orden que especifique. No olvide inicializar estas propiedades para que `Maximum` siempre es mayor que `Minimum`. De lo contrario, se producirá una excepción.

Inicializar el `Slider` propiedades pueden provocar la `Value` propiedad quiere cambiar y el `ValueChanged` eventos se activen. Debe asegurarse de que el `Slider` controlador de eventos no accede a las vistas que todavía no ha sido creadas durante la inicialización de la página.

El `ValueChanged` evento no se desencadena durante `Slider` inicialización a menos que el `Value` los cambios de propiedad. Puede llamar a la `ValueChanged` controlador directamente desde el código.

### <a name="slider-color-selection"></a>Selección de color del control deslizante

El [ **RgbSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders) programa contiene tres `Slider` elementos que permiten seleccionar un color de forma interactiva mediante la especificación de sus valores RGB:

[![Captura de pantalla triple de controles deslizantes R G B](images/ch15fg03-small.png "controles deslizantes de RGB")](images/ch15fg03-large.png#lightbox "controles deslizantes de RGB")

El [ **TextFade** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade) ejemplo utiliza dos `Slider` elementos que se va a mover dos `Label` elementos a través de un `AbsoluteLayout` y atenuar uno a otro.

### <a name="the-stepper-difference"></a>La diferencia del motor paso a paso

El [ `Stepper` ](xref:Xamarin.Forms.Stepper) define las mismas propiedades y eventos como `Slider` pero la `Maximum` propiedad se inicializa en 100 y `Stepper` define una propiedad cuarta:

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment) de tipo `double`, inicializado en 1

Visualmente, la `Stepper` consta de dos botones etiquetados **&ndash;** y **+** . Al presionar **&ndash;** disminuye `Value` por `Increment` en un mínimo de `Minimum`. Al presionar **+** aumenta `Value` por `Increment` hasta un máximo de `Maximum`.

Esto se demuestra el [ **StepperDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo) ejemplo.

## <a name="switch-and-checkbox"></a>Casilla de verificación y conmutador

El [ `Switch` ](xref:Xamarin.Forms.Switch) permite al usuario especificar un valor booleano.

### <a name="switch-basics"></a>Conceptos básicos de conmutador

Visualmente, la `Switch` consta de un control de alternancia que se puede activar y encenderlo. La clase define una propiedad:

- [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) de tipo `bool`

`Switch` define un evento:

- [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) acompañado por un [ `ToggledEventArgs` ](xref:Xamarin.Forms.ToggledEventArgs) objeto, que se desencadena cuando el `IsToggled` los cambios de propiedad.

El [ **SwitchDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo) programa muestra el `Switch`.

### <a name="a-traditional-checkbox"></a>Una casilla de verificación tradicional

Algunos desarrolladores preferible más tradicional `CheckBox` a la `Switch`. El [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca contiene un `CheckBox` clase que derive de `ContentView`. `CheckBox` se implementa mediante el [CheckBox.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml) y [CheckBox.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs) archivos. `CheckBox` define tres propiedades (`Text`, `FontSize`, y `IsChecked`) y un `CheckedChanged` eventos.

El [ **CheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo) muestra esto `CheckBox`.

## <a name="typing-text"></a>Escribir texto

Xamarin.Forms define tres vistas que permiten al usuario escribir y editar texto:

- [`Entry`](xref:Xamarin.Forms.Entry) para una sola línea de texto
- [`Editor`](xref:Xamarin.Forms.Editor) varias líneas de texto
- [`SearchBar`](xref:Xamarin.Forms.SearchBar) para una sola línea de texto para realizar búsquedas.

`Entry` y `Editor` derivan [ `InputView` ](xref:Xamarin.Forms.InputView), que se deriva de `View`. `SearchBar` se deriva directamente de `View`.

### <a name="keyboard-and-focus"></a>Teclado y el foco

Teléfonos y tabletas sin teclados físicos, el `Entry`, `Editor`, y `SearchBar` un teclado virtual que emerja de hacer que todos los elementos. La presencia de este teclado en la pantalla está relacionado con el foco de entrada. Una vista debe tener tanto su [ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible) y [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) propiedades establecidas en `true` para obtener el foco de entrada.

Dos métodos, una propiedad de solo lectura y dos eventos relacionados con el foco de entrada. Estos se definen mediante `VisualElement`:

- El [ `Focus` ](xref:Xamarin.Forms.VisualElement.Focus) método intenta establecer el foco de entrada a un elemento y devuelve `true` si es correcto
- El [ `Unfocus` ](xref:Xamarin.Forms.VisualElement.Unfocus) método quita el foco de entrada de un elemento
- El [ `IsFocused` ](xref:Xamarin.Forms.VisualElement.IsFocused) propiedad de solo lectura indica si el elemento tiene el foco de entrada
- El [ `Focused` ](xref:Xamarin.Forms.VisualElement.Focused) evento indica que cuando un elemento recibe el foco de entrada
- El [ `Unfocused` ](xref:Xamarin.Forms.VisualElement.Unfocused) evento indica que cuando un elemento pierde el foco de entrada

### <a name="choosing-the-keyboard"></a>Elegir el teclado

El [ `InputView` ](xref:Xamarin.Forms.InputView) clase desde el que `Entry` y `Editor` derivar define sólo una propiedad:

- [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) de tipo [`Keyboard`](xref:Xamarin.Forms.Keyboard)

Indica el tipo de teclado que se muestra. Algunos teclados están optimizados para números o URI.

El `Keyboard` clase permite definir un teclado con estático [ `Keyboard.Create` ](xref:Xamarin.Forms.Keyboard.Create(Xamarin.Forms.KeyboardFlags)) método con un argumento de tipo [ `KeyboardFlags` ](xref:Xamarin.Forms.KeyboardFlags), una enumeración con las siguientes marcas de bits:

- `None` establecido en 0
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) se establece en 1
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) establecido en 2
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) establecido en 4
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) establecido en \xFFFFFFFF

Cuando se usa la propiedad multilínea [ `Editor` ](xref:Xamarin.Forms.Editor) cuando se espera un párrafo o más de texto, una llamada a `Keyboard.Create` es un buen enfoque para seleccionar un teclado. Para la línea [ `Entry` ](xref:Xamarin.Forms.Entry), las siguientes propiedades estáticas de sólo lectura de `Keyboard` son útiles:

- [`Default`](xref:Xamarin.Forms.Keyboard.Default)
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)
- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) para los números positivos con o sin un separador decimal.

El [ `KeyboardTypeConverter` ](xref:Xamarin.Forms.KeyboardTypeConverter) permite especificar estas propiedades en XAML, tal como lo demuestra el [ **EntryKeyboards** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards) programa.

### <a name="entry-properties-and-events"></a>Eventos y propiedades de entrada

La línea [ `Entry` ](xref:Xamarin.Forms.Entry) define las siguientes propiedades:

- [`Text`](xref:Xamarin.Forms.Entry.Text) de tipo `string`, el texto que aparece en el `Entry`
- [`TextColor`](xref:Xamarin.Forms.Entry.TextColor) de tipo `Color`
- [`FontFamily`](xref:Xamarin.Forms.Entry.FontFamily) de tipo `string`
- [`FontSize`](xref:Xamarin.Forms.Entry.FontSize) de tipo `double`
- [`FontAttributes`](xref:Xamarin.Forms.Entry.FontAttributes) de tipo `FontAttributes`
- [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) de tipo `bool`, lo que hace que se enmascare caracteres adicionales.
- [`Placeholder`](xref:Xamarin.Forms.Entry.Placeholder) de tipo `string`, coloreado tenue texto que aparece en la `Entry` antes de que se escribe nada
- [`PlaceholderColor`](xref:Xamarin.Forms.Entry.PlaceholderColor) de tipo `Color`

El `Entry` también define dos eventos:

- [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) con un [ `TextChangedEventArgs` ](xref:Xamarin.Forms.TextChangedEventArgs) objeto, se desencadena cada vez que el `Text` los cambios de propiedad
- [`Completed`](xref:Xamarin.Forms.Entry.Completed), que se desencadena cuando el usuario ha terminado y se descarta el teclado. El usuario indica la finalización de una manera específica de la plataforma

El [ **QuadraticEquations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations) muestra estos dos eventos.

### <a name="the-editor-difference"></a>La diferencia del Editor

La propiedad multilínea [ `Editor` ](xref:Xamarin.Forms.Editor) define los mismos `Text` y `Font` propiedades como `Entry` , pero no las demás propiedades. `Editor` También define las dos mismas propiedades que `Entry`.

[**JustNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes) es un programa de toma de notas de la forma libre que se guarda y restaura el contenido de la `Editor`.

### <a name="the-searchbar"></a>El SearchBar

El [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) no se deriva de `InputView`, por lo que no tiene un `Keyboard` propiedad. Pero tiene todos los `Text`, `Font`, y `Placeholder` propiedades que `Entry` define. Además, `SearchBar` define tres propiedades adicionales:

- [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor) de tipo `Color`
- [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand) de tipo [ `ICommand` ](xref:System.Windows.Input.ICommand) para su uso con enlaces de datos y MVVM
- [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) de tipo `Object`, para su uso con `SearchCommand`

El específico de la plataforma Cancelar botón borra el texto. El `SearchBar` también tiene un botón de búsqueda específicos de la plataforma. Al presionar cualquiera de esos botones genera uno de los dos eventos que `SearchBar` define:

- [`TextChanged`](xref:Xamarin.Forms.SearchBar.TextChanged) acompañado por un [ `TextChangedEventArgs` ](xref:Xamarin.Forms.TextChangedEventArgs) objeto
- [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)

El [ **SearchBarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo) muestra el `SearchBar`.

## <a name="date-and-time-selection"></a>Selección de fecha y hora

El [ `DatePicker` ](xref:Xamarin.Forms.DatePicker) y [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) vistas implementan controles específicos de la plataforma que permiten al usuario especificar una fecha u hora.

### <a name="the-datepicker"></a>DatePicker

[`DatePicker`](xref:Xamarin.Forms.DatePicker) define cuatro propiedades:

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) de tipo `DateTime`, inicializado en el 1 de enero de 1900
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) de tipo `DateTime`, inicializado en el 31 de diciembre de 2100
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) de tipo `DateTime`, inicializado en `DateTime.Today`
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) de tipo `string`, .NET inicializado en "d", el patrón de fecha corta de cadena de formato resultante en una presentación de fecha, como "7/20/1969" en Estados Unidos.

Puede establecer el `DateTime` dar formato a las propiedades de XAML expresar las propiedades como los elementos de propiedad y usando la referencia cultural invariable fecha corta ("7/20/1969").   

El [ **DaysBetweenDates** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates) ejemplo calcula el número de días entre dos fechas seleccionadas por el usuario.

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>El objeto TimePicker (o es una TimeSpanPicker?)

[`TimePicker`](xref:Xamarin.Forms.TimePicker) define dos propiedades y no hay eventos:

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) es de tipo `TimeSpan` lugar `DateTime`, que indica el tiempo transcurrido desde medianoche
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) de tipo `string`, .NET inicializado en "t", el patrón de hora corta, lo que resulta en una visualización del tiempo, como "1:45 PM" de cadena de formato en Estados Unidos.

El [ **SetTimer** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer) programa muestra cómo usar el `TimePicker` para especificar una hora para un temporizador. El programa sólo funciona si la mantiene en primer plano.

**SetTimer** también muestra cómo utilizar el [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) método `Page` para mostrar un cuadro de alerta.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 15 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [Capítulo 15 muestras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [Slider](~/xamarin-forms/user-interface/slider.md)
- [Entrada](~/xamarin-forms/user-interface/text/entry.md)
- [Editor](~/xamarin-forms/user-interface/text/editor.md)
- [DatePicker](~/xamarin-forms/user-interface/datepicker.md)
