---
Título: " Xamarin.Forms marcado en c#" Descripción: "el marcado de c# es un conjunto opcional de métodos y clases auxiliares fluidas para simplificar el proceso de creación de interfaces de usuario declarativas Xamarin.Forms en C#".
MS. Prod: Xamarin ms. AssetID: D41B9DCD-5C34-4C2F-B177-FC082AB2E9E0 ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 05/15/2020 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-c-markup"></a>Xamarin.FormsMarcado de C#

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-csharpmarkupdemos/)

El marcado de C# es un conjunto opcional de métodos y clases auxiliares fluidas para simplificar el proceso de creación de interfaces de usuario declarativas Xamarin.Forms en C#. La API fluida proporcionada por el marcado de C# está disponible en el `Xamarin.Forms.Markup` espacio de nombres.

Al igual que con XAML, el marcado de C# permite una separación limpia entre el marcado de la interfaz de usuario y la lógica de la interfaz de usuario. Esto se puede lograr separando el marcado de la interfaz de usuario y la lógica de la interfaz de usuario en archivos de clase parcial distintos. Por ejemplo, para una página de inicio de sesión, el marcado de la interfaz de usuario se encontraba en un archivo denominado *LoginPage.CS*, mientras que la lógica de la interfaz de usuario se encontraba en un archivo denominado *LoginPage.Logic.CS*.

El marcado de C# está disponible en Xamarin.Forms 4,6. Sin embargo, actualmente es experimental y solo se puede usar agregando la siguiente línea de código al archivo *app.CS* :

```csharp
Device.SetFlags(new string[]{ "Markup_Experimental" });
```

> [!NOTE]
> El marcado de C# está disponible en todas las plataformas admitidas por Xamarin.Forms .

## <a name="basic-example"></a>Ejemplo básico

En el ejemplo siguiente se muestra cómo establecer el contenido de la página en un nuevo [`Grid`](xref:Xamarin.Forms.Grid) que contiene un [`Label`](xref:Xamarin.Forms.Label) y un [`Entry`](xref:Xamarin.Forms.Entry) , en C#:

```csharp
Grid grid = new Grid();

Label label = new Label { Text = "Code: " };
grid.Children.Add(label, 0, 1);

Entry entry = new Entry
{
    Placeholder = "Enter number",
    Keyboard = Keyboard.Numeric,
    BackgroundColor = Color.AliceBlue,
    TextColor = Color.Black,
    FontSize = 15,
    HeightRequest = 44,
    Margin = fieldMargin
};
grid.Children.Add(entry, 0, 2);
Grid.SetColumnSpan(entry, 2);
entry.SetBinding(Entry.TextProperty, new Binding("RegistrationCode"));

Content = grid;
```

En este ejemplo se crea un [`Grid`](xref:Xamarin.Forms.Grid) objeto, [`Label`](xref:Xamarin.Forms.Label) con [`Entry`](xref:Xamarin.Forms.Entry) objetos secundarios y. `Label`Muestra el texto y los `Entry` datos se enlazan a la `RegistrationCode` propiedad del ViewModel. Cada vista secundaria se establece para que aparezca en una fila específica de `Grid` , y `Entry` abarca todas las columnas de `Grid` . Además, se establece el alto de `Entry` , junto con el teclado, los colores, el tamaño de fuente del texto y su `Margin` . Por último, la `Page.Content` propiedad se establece en el `Grid` objeto.

El marcado de C# permite que este código se vuelva a escribir mediante su API fluida:

```csharp
using Xamarin.Forms.Markup;
using static Xamarin.Forms.Markup.GridRowsColumns;

Content = new Grid
{
  Children =
  {
    new Label { Text = "Code:" }
               .Row (BodyRow.CodeHeader) .Column (BodyCol.Header),

    new Entry { Placeholder = "Enter number", Keyboard = Keyboard.Numeric, BackgroundColor = Color.AliceBlue, TextColor = Color.Black } .Font (15)
               .Row (BodyRow.CodeEntry) .ColumnSpan (All<BodyCol>()) .Margin (fieldMargin) .Height (44)
               .Bind (nameof(vm.RegistrationCode))
  }
}};
```

Este ejemplo es idéntico al ejemplo anterior, pero la API fluida de marcado en C# simplifica el proceso de creación de la interfaz de usuario en C#.

> [!NOTE]
> El marcado de C# incluye métodos de extensión que establecen propiedades específicas de la vista. Estos métodos de extensión no están diseñados para reemplazar todos los establecedores de propiedad. En su lugar, están diseñados para mejorar la legibilidad del código y se pueden usar en combinación con los establecedores de propiedad. Se recomienda usar siempre un método de extensión cuando exista uno para una propiedad, pero puede elegir el saldo que prefiera.

## <a name="data-binding"></a>Enlace de datos

El marcado de C# incluye un `Bind` método de extensión, junto con sobrecargas, que crea un enlace de datos entre una propiedad de vista enlazable y una propiedad especificada. El `Bind` método conoce la propiedad enlazable predeterminada para la mayoría de los controles que se incluyen en Xamarin.Forms . Por lo tanto, normalmente no es necesario especificar la propiedad de destino al utilizar este método. Sin embargo, también puede registrar la propiedad enlazable predeterminada para controles adicionales:

```csharp
using Xamarin.Forms.Markup;
//...

DefaultBindableProperties.Register(HoverButton.CommandProperty, RadialGauge.ValueProperty);
```

El `Bind` método se puede utilizar para enlazar a cualquier propiedad enlazable:

```csharp
using Xamarin.Forms.Markup;
// ...

new Label { Text = "No data available" }
           .Bind (Label.IsVisibleProperty, nameof(vm.Empty))
```

Además, el `BindCommand` método de extensión se puede enlazar al valor predeterminado `Command` y a las propiedades de un control `CommandParameter` en una única llamada al método:

```csharp
using Xamarin.Forms.Markup;
// ...

new TextCell { Text = "Tap me" }
              .BindCommand (nameof(vm.TapCommand))
```

De forma predeterminada, `CommandParameter` se enlaza al contexto de enlace. También puede especificar la ruta de acceso de enlace y el origen de los `Command` `CommandParameter` enlaces y:

```csharp
using Xamarin.Forms.Markup;
// ...

new TextCell { Text = "Tap Me" }
              .BindCommand (nameof(vm.TapCommand), vm, nameof(Item.Id))
```

En este ejemplo, el contexto de enlace es una `Item` instancia de, por lo que no es necesario especificar un origen para el `Id` `CommandParameter` enlace.

Si solo necesita enlazar a `Command` , puede pasar `null` al `parameterPath` argumento del `BindCommand` método. Como alternativa, use el `Bind` método.

También puede registrar las propiedades y predeterminadas `Command` `CommandParameter` para los controles adicionales:

```csharp
using Xamarin.Forms.Markup;
//...

DefaultBindableProperties.RegisterCommand(
    (CustomViewA.CommandProperty, CustomViewA.CommandParameterProperty),
    (CustomViewB.CommandProperty, CustomViewB.CommandParameterProperty)
);
```

El código de convertidor en línea se puede pasar al `Bind` método con `convert` los `convertBack` parámetros y:

```csharp
using Xamarin.Forms.Markup;
//...

new Label { Text = "Tree" }
           .Bind (Label.MarginProperty, nameof(TreeNode.TreeDepth),
                  convert: (int depth) => new Thickness(depth * 20, 0, 0, 0))
```

También se admiten los parámetros de convertidor Safe Type:

```csharp
using Xamarin.Forms.Markup;
//...

new Label { }
           .Bind (nameof(viewModel.Text),
                  convert: (string text, int repeat) => string.Concat(Enumerable.Repeat(text, repeat)))
```

Además, el código y las instancias del convertidor se pueden volver a usar con la `FuncConverter` clase:

```csharp
using Xamarin.Forms.Markup;
//...

FuncConverter<int, Thickness> treeMarginConverter = new FuncConverter<int, Thickness>(depth => new Thickness(depth * 20, 0, 0, 0));
new Label { Text = "Tree" }
           .Bind (Label.MarginProperty, nameof(TreeNode.TreeDepth), converter: treeMarginConverter),
```

La `FuncConverter` clase también admite `CultureInfo` objetos:

```csharp
using Xamarin.Forms.Markup;
//...

cultureAwareConverter = new FuncConverter<DateTimeOffset, string, int>(
    (date, daysToAdd, culture) => date.AddDays(daysToAdd).ToString(culture)
);
```

También es posible enlazar datos a `Span` objetos que se especifican con la `FormattedText` propiedad:

```csharp
using Xamarin.Forms.Markup;
//...

new Label { } .FormattedText (
    new Span { Text = "Built with " },
    new Span { TextColor = Color.Blue, TextDecorations = TextDecorations.Underline }
              .BindTapGesture (nameof(vm.ContinueToCSharpForMarkupCommand))
              .Bind (nameof(vm.Title))
)
```

### <a name="gesture-recognizers"></a>Reconocedores de gestos

`Command``CommandParameter`las propiedades y pueden ser datos enlazados a `GestureElement` `View` tipos y mediante los `BindClickGesture` métodos de `BindSwipeGesture` extensión, y `BindTapGesture` :

```csharp
using Xamarin.Forms.Markup;
//...

new Label { Text = "Tap Me" }
           .BindTapGesture (nameof(vm.TapCommand))
```

En este ejemplo se crea un reconocedor de gestos del tipo especificado y se agrega a [`Label`](xref:Xamarin.Forms.Label) . Los `Bind*Gesture` métodos de extensión ofrecen los mismos parámetros que los `BindCommand` métodos de extensión. Sin embargo, de forma predeterminada no se `Bind*Gesture` enlaza `CommandParameter` , mientras que `BindCommand` sí.

Para inicializar un reconocedor de gestos con parámetros, use los `ClickGesture` métodos de extensión,, `PanGesture` `PinchGesture` , `SwipeGesture` y `TapGesture` :

```csharp
using Xamarin.Forms.Markup;
//...

new Label { Text = "Tap Me" }
           .TapGesture (g => g.Bind(nameof(vm.DoubleTapCommand)).NumberOfTapsRequired = 2)
```

Dado que un reconocedor de gestos es un `BindableObject` , puede utilizar los `Bind` `BindCommand` métodos de extensión y al inicializarlo. También puede inicializar tipos de reconocedor de gestos personalizados con el `Gesture<TGestureElement, TGestureRecognizer>` método de extensión.

## <a name="layout"></a>Layout

El marcado de C# incluye una serie de métodos de extensión de diseño que admiten la colocación de vistas en diseños y contenido en vistas:

| Tipo | Métodos de extensión |
|---|---|
| `FlexLayout` | `AlignSelf`, `Basis`, `Grow`, `Menu`, `Order`, `Shrink` |
| `Grid` | `Row`, `Column`, `RowSpan`, `ColumnSpan` |
| `Label` | `TextLeft`, `TextCenterHorizontal`, `TextRight` <br/> `TextTop`, `TextCenterVertical`, `TextBottom` <br/> `TextCenter` |
| `Layout` | `Padding`, `Paddings` |
| `LayoutOptions` | `Left`, `CenterHorizontal`, `FillHorizontal`, `Right` <br/> `LeftExpand`, `CenterExpandHorizontal`, `FillExpandHorizontal`, `RightExpand` <br /> `Top`, `Bottom`, `CenterVertical`, `FillVertical` <br /> `TopExpand`, `BottomExpand`, `CenterExpandVertical`, `FillExpandVertical` <br /> `Center`, `Fill`, `CenterExpand`, `FillExpand` |
| `View` | `Margin`, `Margins` |
| `VisualElement` | `Height`, `Width`, `MinHeight`, `MinWidth`, `Size`, `MinSize` |

### <a name="left-to-right-and-right-to-left-support"></a>Compatibilidad de izquierda a derecha y de derecha a izquierda

En el marcado de C# diseñado para admitir la dirección de flujo de izquierda a derecha (LTR) o de derecha a izquierda (RTL), los métodos de extensión enumerados anteriormente ofrecen el conjunto de nombres más intuitivo `Left` : `Right` , `Top` y `Bottom` .

Para que esté disponible el conjunto correcto de métodos de extensión izquierdo y derecho y, en el proceso, haga que sea explícito en qué dirección de flujo está diseñada el marcado, incluya una de las dos `using` directivas siguientes: `using Xamarin.Forms.Markup.LeftToRight;` , o `using Xamarin.Forms.Markup.RightToLeft;` .

En el marcado de C# diseñado para admitir la dirección de flujo de izquierda a derecha y de derecha a izquierda, se recomienda usar los métodos de extensión en la tabla siguiente, en lugar de uno de los espacios de nombres anteriores:

| Tipo | Métodos de extensión |
|---|---|
| `Label` | `TextStart`, `TextEnd` |
| `LayoutOptions` | `Start`, `End` <br/> `StartExpand`, `EndExpand` |

### <a name="layout-line-convention"></a>Convención de línea de diseño

La Convención recomendada es colocar todos los métodos de extensión de diseño para una vista en una sola línea en el orden siguiente:

1. La fila y la columna que contienen la vista.
1. Alineación dentro de la fila y la columna.
1. Márgenes alrededor de la vista.
1. Tamaño de la vista.
1. Relleno dentro de la vista.
1. Alineación del contenido dentro del relleno.

En el código siguiente se muestra un ejemplo de esta Convención:

```csharp
new Label { }
           .Row (BodyRow.Prompt) .ColumnSpan (All<BodyCol>()) .FillExpandHorizontal () .CenterVertical () .Margin (fieldNameMargin) .TextCenterHorizontal () // Layout line
```

De forma coherente, la Convención le permite leer rápidamente el marcado de C# y crear una asignación mental de la ubicación en la que se encuentra el contenido de la vista en la interfaz de usuario.

## <a name="grid-rows-and-columns"></a>Filas y columnas de cuadrícula

Las enumeraciones se pueden utilizar para definir [`Grid`](xref:Xamarin.Forms.Grid) filas y columnas, en lugar de usar números. Esto ofrece la ventaja de que la renumeración no es necesaria al agregar o quitar filas o columnas.

> [!IMPORTANT]
> [`Grid`](xref:Xamarin.Forms.Grid)La definición de filas y columnas mediante enumeraciones requiere la siguiente `using` Directiva:`using static Xamarin.Forms.Markup.GridRowsColumns;`

En el código siguiente se muestra un ejemplo de cómo definir y consumir [`Grid`](xref:Xamarin.Forms.Grid) filas y columnas mediante enumeraciones:

```csharp
using Xamarin.Forms.Markup;
using Xamarin.Forms.Markup.LeftToRight;
using static Xamarin.Forms.Markup.GridRowsColumns;
// ...

enum BodyRow
{
    Prompt,
    CodeHeader,
    CodeEntry,
    Button
}

enum BodyCol
{
    FieldLabel,
    FieldValidation
}

View Build() => new Grid
{
    RowDefinitions = Rows.Define(
        (BodyRow.Prompt    , 170 ),
        (BodyRow.CodeHeader, 75  ),
        (BodyRow.CodeEntry , Auto),
        (BodyRow.Button    , Auto)
    ),

    ColumnDefinitions = Columns.Define(
        (BodyCol.FieldLabel     , 160 ),
        (BodyCol.FieldValidation, Star)
    ),

    Children =
    {
        new Label { LineBreakMode = LineBreakMode.WordWrap } .Font (15) .Bold ()
                   .Row (BodyRow.Prompt) .ColumnSpan (All<BodyCol>()) .FillExpandHorizontal () .CenterVertical () .Margin (fieldNameMargin) .TextCenterHorizontal ()
                   .Bind (nameof(vm.RegistrationPrompt)),

        new Label { Text = "Registration code" } .Bold ()
                   .Row (BodyRow.CodeHeader) .Column(BodyCol.FieldLabel) .Bottom () .Margin (fieldNameMargin),

        new Label { } .Italic ()
                   .Row (BodyRow.CodeHeader) .Column (BodyCol.FieldValidation) .Right () .Bottom () .Margin (fieldNameMargin)
                   .Bind (nameof(vm.RegistrationCodeValidationMessage)),

        new Entry { Placeholder = "E.g. 123456", Keyboard = Keyboard.Numeric, BackgroundColor = Color.AliceBlue, TextColor = Color.Black } .Font (15)
                   .Row (BodyRow.CodeEntry) .ColumnSpan (All<BodyCol>()) .Margin (fieldMargin) .Height (44)
                   .Bind (nameof(vm.RegistrationCode), BindingMode.TwoWay),

        new Button { Text = "Verify" } .Style (FilledButton)
                    .Row (BodyRow.Button) .ColumnSpan (All<BodyCol>()) .FillExpandHorizontal () .Margin (PageMarginSize)
                    .Bind (Button.IsVisibleProperty, nameof(vm.CanVerifyRegistrationCode))
                    .Bind (nameof(vm.VerifyRegistrationCodeCommand)),
    }
};
```

Además, puede definir de manera concisa filas y columnas sin enumeraciones:

```csharp
new Grid
{
    RowDefinitions = Rows.Define (Auto, Star, 20),
    ColumnDefinitions = Columns.Define (Auto, Star, 20, 40)
    // ...
}
```

## <a name="fonts"></a>Fuentes

Los controles de la lista siguiente pueden llamar a `FontSize` los `Bold` métodos de extensión,, `Italic` y `Font` para establecer la apariencia del texto que muestra el control:

- `Button`
- `DatePicker`
- `Editor`
- `Entry`
- `Label`
- `Picker`
- `SearchBar`
- `Span`
- `TimePicker`

## <a name="effects"></a>Efectos

Los efectos se pueden adjuntar a los controles con el `Effect` método de extensión:

```csharp
using Xamarin.Forms.Markup;
// ...

new Button { Text = "Tap Me" }
            .Effects (new ButtonMixedCaps())
```

## <a name="logic-integration"></a>Integración de lógica

El `Invoke` método de extensión se puede usar para ejecutar código insertado en el marcado de C#:

```csharp
using Xamarin.Forms.Markup;
// ...

new ListView { } .Invoke (l => l.ItemTapped += OnListViewItemTapped)
```

Además, puede usar el método de `Assign` extensión para tener acceso a un control desde fuera del marcado de la interfaz de usuario (en el archivo de lógica de la interfaz de usuario):

```csharp
using Xamarin.Forms.Markup;
// ...

new ListView { } .Assign (out MyListView)
```

## <a name="styles"></a>Estilos

En el ejemplo siguiente se muestra cómo crear estilos implícitos y explícitos mediante el marcado de C#:

```csharp
using Xamarin.Forms.Markup;
using Xamarin.Forms;

namespace CSharpForMarkupDemos
{
    public static class Styles
    {
        static Style<Button> buttons, filledButton;
        static Style<Label> labels;
        static Style<Span> link;

        #region Implicit styles

        public static ResourceDictionary Implicit => new ResourceDictionary { Buttons, Labels };

        public static Style<Button> Buttons => buttons ?? (buttons = new Style<Button>(
            (Button.HeightRequestProperty, 44),
            (Button.FontSizeProperty, 13),
            (Button.HorizontalOptionsProperty, LayoutOptions.Center),
            (Button.VerticalOptionsProperty, LayoutOptions.Center)
        ));

        public static Style<Label> Labels => labels ?? (labels = new Style<Label>(
            (Label.FontSizeProperty, 13),
            (Label.TextColorProperty, Color.Black)
        ));

        #endregion Implicit styles

        #region Explicit styles

        public static Style<Button> FilledButton => filledButton ?? (filledButton = new Style<Button>(
            (Button.TextColorProperty, Color.White),
            (Button.BackgroundColorProperty, Color.FromHex("#1976D2")),
            (Button.CornerRadiusProperty, 5)
        )).BasedOn(Buttons);

        public static Style<Span> Link => link ?? (link = new Style<Span>(
            (Span.TextColorProperty, Color.Blue),
            (Span.TextDecorationsProperty, TextDecorations.Underline)
        ));

        #endregion Explicit styles
    }
}
```

Los estilos implícitos se pueden consumir cargándolo en el Diccionario de recursos de la aplicación:

```csharp
public App()
{
    Resources = Styles.Implicit;
    // ...
}
```

Los estilos explícitos se pueden usar con el `Style` método de extensión.

```csharp
using static CSharpForMarkupExample.Styles;
// ...

new Button { Text = "Tap Me" } .Style (FilledButton),
```

> [!NOTE]
> Además del método de `Style` extensión, también hay métodos de `ApplyToDerivedTypes` extensión,, `BasedOn` `Add` y `CanCascade` .

Como alternativa, puede crear sus propios métodos de extensión de estilo:

```csharp
public static TButton Filled<TButton>(this TButton button) where TButton : Button
{
    button.Buttons(); // Equivalent to Style .BasedOn (Buttons)
    button.TextColor = Color.White;
    button.BackgroundColor = Color.Red;
    return button;
}
```

El `Filled` método de extensión se puede consumir de la siguiente manera:

```csharp
new Button { Text = "Tap Me" } .Filled ()
```

## <a name="platform-specifics"></a>Características específicas de las plataformas

El `Invoke` método de extensión se puede usar para aplicar características específicas de la plataforma. Sin embargo, para evitar errores de ambigüedad, no incluya `using` directivas para los `Xamarin.Forms.PlatformConfiguration.*Specific` espacios de nombres directamente. En su lugar, cree un alias de espacio de nombres y consuma los específicos de la plataforma mediante el alias:

```csharp
using Xamarin.Forms.Markup;
using PciOS = Xamarin.Forms.PlatformConfiguration.iOSSpecific;
// ...

new ListView { } .Invoke (l => PciOS.ListView.SetGroupHeaderStyle(l, PciOS.GroupHeaderStyle.Grouped))
```

Además, si usa ciertos específicos de la plataforma con frecuencia, puede crear métodos de extensión fluida para ellos en su propia clase de extensiones:

```csharp
public static T iOSGroupHeaderStyle<T>(this T listView, PciOS.GroupHeaderStyle style) where T : Forms.ListView
{
  PciOS.ListView.SetGroupHeaderStyle(listView, style);
  return listView;
}
```

El método de extensión se puede consumir de la siguiente manera:

```csharp
new ListView { } .iOSGroupHeaderStyle(PciOS.GroupHeaderStyle.Grouped)
```

Para obtener más información sobre las características específicas de la plataforma, vea características de la plataforma [Android](~/xamarin-forms/platform/android/index.md), características de la plataforma [iOS](~/xamarin-forms/platform/ios/index.md)y características de la [plataforma Windows](~/xamarin-forms/platform/windows/index.md).

## <a name="recommended-convention"></a>Convención recomendada

Un orden recomendado y una agrupación de propiedades y métodos auxiliares es:

- **Propósito**: cualquier propiedad o método auxiliar cuyo valor identifique el propósito del control (por ejemplo `Text` ,, `Placeholder` `Assign` ).
- **Otros**: todas las propiedades o métodos auxiliares que no son de diseño ni de enlace, en la misma línea o en varias líneas.
- **Diseño**: el diseño se ordena de forma ascendente: filas y columnas, opciones de diseño, margen, tamaño, relleno y alineación del contenido.
- **BIND**: el enlace de datos se realiza al final de la cadena de métodos, con una propiedad enlazada por línea. Si la propiedad enlazable *predeterminada* está enlazada, debe estar al final de la cadena de métodos.

En el código siguiente se muestra un ejemplo de la siguiente Convención:

```csharp
new Button { Text = "Verify" /* purpose */ } .Style (FilledButton) // other
            .Row (BodyRow.Button) .ColumnSpan (All<BodyCol>()) .FillExpandHorizontal () .Margin (10) // layout
            .Bind (Button.IsVisibleProperty, nameof(vm.CanVerifyRegistrationCode)) // bind
            .Bind (nameof(vm.VerifyRegistrationCodeCommand)), // bind default

new Label { }
           .Assign (out animatedMessageLabel) // purpose
           .Invoke (label => label.SizeChanged += MessageLabel_SizeChanged) // other
           .Row (BodyRow.Message) .ColumnSpan (All<BodyCol>()) // layout
           .Bind (nameof(vm.Message)), // bind default
```

Aplicar de forma coherente esta Convención permite examinar rápidamente el marcado de C# y compilar una imagen mental del diseño de la interfaz de usuario.

## <a name="related-links"></a>Vínculos relacionados

- [CSharpForMarkupDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-csharpmarkupdemos/)
- [Características de la plataforma Android](~/xamarin-forms/platform/android/index.md)
- [características de la plataforma iOS](~/xamarin-forms/platform/ios/index.md)
- [Características de la plataforma Windows](~/xamarin-forms/platform/windows/index.md)
