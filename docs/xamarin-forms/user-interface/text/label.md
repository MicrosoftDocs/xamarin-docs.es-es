---
title: " Xamarin.Forms etiqueta" Description: "en este artículo se explica cómo usar la Xamarin.Forms clase Label para mostrar texto de una sola línea y de varias líneas en aplicaciones".
MS. Prod: Xamarin ms. AssetID: 02E6C553-5670-49A0-8EE9-5153ED21EA91 ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 04/09/2020 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-label"></a>Xamarin.FormsRótulo

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Mostrar texto en Xamarin. Forms_

La [`Label`](xref:Xamarin.Forms.Label) vista se usa para mostrar texto, tanto de una sola línea como de varias líneas. Las etiquetas pueden tener decoraciones de texto, texto de color y usar fuentes personalizadas (familias, tamaños y opciones).

## <a name="text-decorations"></a>Decoraciones de texto

Las decoraciones de texto de subrayado y tachado se pueden aplicar a [`Label`](xref:Xamarin.Forms.Label) las instancias estableciendo la `Label.TextDecorations` propiedad en uno o varios miembros de la `TextDecorations` enumeración:

- `None`
- `Underline`
- `Strikethrough`

En el siguiente ejemplo de XAML se muestra cómo establecer la `Label.TextDecorations` propiedad:

```xaml
<Label Text="This is underlined text." TextDecorations="Underline"  />
<Label Text="This is text with strikethrough." TextDecorations="Strikethrough" />
<Label Text="This is underlined text with strikethrough." TextDecorations="Underline, Strikethrough" />
```

El código de C# equivalente es el siguiente:

```csharp
var underlineLabel = new Label { Text = "This is underlined text.", TextDecorations = TextDecorations.Underline };
var strikethroughLabel = new Label { Text = "This is text with strikethrough.", TextDecorations = TextDecorations.Strikethrough };
var bothLabel = new Label { Text = "This is underlined text with strikethrough.", TextDecorations = TextDecorations.Underline | TextDecorations.Strikethrough };
```

Las capturas de pantallas siguientes muestran los `TextDecorations` miembros de enumeración que se aplican a [`Label`](xref:Xamarin.Forms.Label) las instancias:

![Etiquetas con decoraciones de texto](label-images/label-textdecorations.png)

> [!NOTE]
> También se pueden aplicar decoraciones de texto a [`Span`](xref:Xamarin.Forms.Span) las instancias de. Para obtener más información sobre la `Span` clase, vea [texto con formato](#formatted-text).

## <a name="character-spacing"></a>espaciado entre caracteres

El espaciado de caracteres se puede aplicar a [`Label`](xref:Xamarin.Forms.Label) las instancias estableciendo la `Label.CharacterSpacing` propiedad en un `double` valor:

```xaml
<Label Text="Character spaced text"
       CharacterSpacing="10" />
```

El código de C# equivalente es el siguiente:

```csharp
Label label = new Label { Text = "Character spaced text", CharacterSpacing = 10 };
```

El resultado es que los caracteres del texto mostrados por [`Label`](xref:Xamarin.Forms.Label) están separados por las `CharacterSpacing` unidades independientes del dispositivo.

## <a name="new-lines"></a>Nuevas líneas

Hay dos técnicas principales para forzar el texto de una en [`Label`](xref:Xamarin.Forms.Label) una nueva línea, desde XAML:

1. Use el carácter de salto de línea Unicode, que es " &amp; #10;".
1. Especifique el texto mediante la sintaxis del *elemento de propiedad* .

En el código siguiente se muestra un ejemplo de ambas técnicas:

```xaml
<!-- Unicode line feed character -->
<Label Text="First line &#10; Second line" />

<!-- Property element syntax -->
<Label>
    <Label.Text>
        First line
        Second line
    </Label.Text>
</Label>
```

En C#, el texto se puede forzar en una nueva línea con el carácter "\n":

```csharp
Label label = new Label { Text = "First line\nSecond line" };
```

## <a name="colors"></a>Colores

Las etiquetas se pueden establecer para usar un color de texto personalizado mediante la [`TextColor`](xref:Xamarin.Forms.Label.TextColor) propiedad enlazable.

Es necesario tener especial cuidado para asegurarse de que se puedan usar los colores en cada plataforma. Dado que cada plataforma tiene valores predeterminados diferentes para los colores de texto y de fondo, deberá tomar precauciones para elegir un valor predeterminado que funcione en cada uno de ellos.

En el siguiente ejemplo de XAML se establece el color del texto de un `Label` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo">
    <StackLayout Padding="5,10">
      <Label TextColor="#77d065" FontSize = "20" Text="This is a green label." />
    </StackLayout>
</ContentPage>
```

El código de C# equivalente es el siguiente:

```csharp
public partial class LabelPage : ContentPage
{
    public LabelPage ()
    {
        InitializeComponent ();

        var layout = new StackLayout { Padding = new Thickness(5,10) };
        var label = new Label { Text="This is a green label.", TextColor = Color.FromHex("#77d065"), FontSize = 20 };
        layout.Children.Add(label);
        this.Content = layout;
    }
}
```

Las siguientes capturas de pantallas muestran el resultado de establecer la `TextColor` propiedad:

![Ejemplo de etiqueta TextColor](label-images/textcolor.png)

Para obtener más información sobre los colores, vea [colores](~/xamarin-forms/user-interface/colors.md).

## <a name="fonts"></a>Fuentes

Para obtener más información sobre cómo especificar fuentes en un `Label` , vea [fuentes](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="truncation-and-wrapping"></a>Truncamiento y ajuste

Las etiquetas se pueden establecer para controlar el texto que no cabe en una línea de una de varias maneras, expuesta por la `LineBreakMode` propiedad. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode)es una enumeración con los valores siguientes:

- **HeadTruncation** &ndash; trunca el encabezado del texto, mostrando el final.
- **CharacterWrap** &ndash; ajusta el texto en una nueva línea en el límite de un carácter.
- **MiddleTruncation** &ndash; muestra el principio y el final del texto, con el reemplazo central por puntos suspensivos.
- **NoWrap** &ndash; no ajusta el texto y muestra solo el texto que cabe en una línea.
- **TailTruncation** &ndash; muestra el principio del texto, truncando el final.
- **WordWrap** &ndash; ajusta el texto en el límite de palabras.

## <a name="display-a-specific-number-of-lines"></a>Mostrar un número específico de líneas

Se puede especificar el número de líneas que se muestran mediante [`Label`](xref:Xamarin.Forms.Label) el establecimiento de la `Label.MaxLines` propiedad en un `int` valor:

- Cuando `MaxLines` es-1, que es su valor predeterminado, `Label` respeta el valor de la [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) propiedad para mostrar solo una línea, posiblemente truncada, o todas las líneas con todo el texto.
- Cuando `MaxLines` es 0, `Label` no se muestra.
- Cuando `MaxLines` es 1, el resultado es idéntico al establecimiento de la [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) propiedad en [`NoWrap`](xref:Xamarin.Forms.LineBreakMode) , [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode) , [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode) o [`TailTruncation`](xref:Xamarin.Forms.LineBreakMode) . Sin embargo, el `Label` respetará el valor de la [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) propiedad con respecto a la colocación de puntos suspensivos, si procede.
- Cuando `MaxLines` es mayor que 1, mostrará `Label` hasta el número especificado de líneas, respetando el valor de la [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) propiedad con respecto a la posición de los puntos suspensivos, si procede. Sin embargo, `MaxLines` si se establece la propiedad en un valor mayor que 1, no se produce ningún efecto si la [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) propiedad está establecida en [`NoWrap`](xref:Xamarin.Forms.LineBreakMode) .

En el siguiente ejemplo de XAML se muestra cómo establecer la `MaxLines` propiedad en un [`Label`](xref:Xamarin.Forms.Label) :

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       MaxLines="2" />
```

El código de C# equivalente es el siguiente:

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  MaxLines = 2
};
```

Las siguientes capturas de pantallas muestran el resultado de establecer la `MaxLines` propiedad en 2, cuando el texto es lo suficientemente largo como para ocupar más de 2 líneas:

![Ejemplo de etiqueta MaxLines](label-images/label-maxlines.png)

## <a name="display-html"></a>mostrar HTML

La [`Label`](xref:Xamarin.Forms.Label) clase tiene una `TextType` propiedad, que determina si la `Label` instancia debe mostrar texto sin formato o texto HTML. Esta propiedad debe establecerse en uno de los miembros de la `TextType` enumeración:

- `Text`indica que mostrará el `Label` texto sin formato y es el valor predeterminado de la `Label.TextType` propiedad.
- `Html`indica que mostrará el `Label` texto HTML.

Por lo tanto, [`Label`](xref:Xamarin.Forms.Label) las instancias de pueden mostrar HTML estableciendo la `Label.TextType` propiedad en `Html` y la `Label.Text` propiedad en una cadena HTML:

```csharp
Label label = new Label
{
    Text = "This is <strong style=\"color:red\">HTML</strong> text.",
    TextType = TextType.Html
};
```

En el ejemplo anterior, los caracteres de comillas dobles del código HTML deben usarse con el símbolo de escape `\` .

En XAML, las cadenas HTML se pueden volver ilegibles debido a la escape adicional de los `<` `>` símbolos y:

```xaml
<Label Text="This is &lt;strong style=&quot;color:red&quot;&gt;HTML&lt;/strong&gt; text."
       TextType="Html"  />
```

Como alternativa, para mayor legibilidad, el código HTML se puede incluir en una `CDATA` sección:

```xaml
<Label TextType="Html">
    <![CDATA[
    This is <strong style="color:red">HTML</strong> text.
    ]]>
</Label>
```

En este ejemplo, la `Label.Text` propiedad se establece en la cadena HTML que se inserta en la `CDATA` sección. Esto funciona porque la `Text` propiedad es `ContentProperty` para la `Label` clase.

Las capturas de pantallas siguientes muestran un [`Label`](xref:Xamarin.Forms.Label) código HTML que muestra:

![Capturas de pantallas de una etiqueta que muestra HTML, en iOS y Android](label-images/label-html.png)

> [!IMPORTANT]
> La visualización de HTML en un [`Label`](xref:Xamarin.Forms.Label) se limita a las etiquetas HTML admitidas por la plataforma subyacente.

## <a name="formatted-text"></a>Texto con formato

Las etiquetas exponen una [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) propiedad que permite la presentación de texto con varias fuentes y colores en la misma vista.

La `FormattedText` propiedad es de tipo [`FormattedString`](xref:Xamarin.Forms.FormattedString) , que consta de una o más [`Span`](xref:Xamarin.Forms.Span) instancias, establecida a través de la [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) propiedad. Las siguientes `Span` propiedades se pueden usar para establecer la apariencia visual:

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor): color del fondo del intervalo.
- `CharacterSpacing`, del tipo `double`, es el espaciado entre los caracteres del texto de `Span`.
- [`Font`](xref:Xamarin.Forms.Span.Font): la fuente del texto del intervalo.
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes): los atributos de fuente para el texto del intervalo.
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily): la familia de fuentes a la que pertenece la fuente del texto del intervalo.
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize): tamaño de la fuente del texto del intervalo.
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor): color del texto del intervalo. Esta propiedad está obsoleta y se ha reemplazado por la `TextColor` propiedad.
- [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight): multiplicador que se va a aplicar al alto de línea predeterminado del intervalo. Para obtener más información, consulte [alto de línea](#line-height).
- [`Style`](xref:Xamarin.Forms.Span.Style): el estilo que se va a aplicar al intervalo.
- [`Text`](xref:Xamarin.Forms.Span.Text): el texto del intervalo.
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor): color del texto del intervalo.
- `TextDecorations`: las decoraciones que se aplican al texto en el intervalo. Para obtener más información, vea [decoraciones de texto](#text-decorations).

Las [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) [`Text`](xref:Xamarin.Forms.Span.Text) propiedades, y [`Text`](xref:Xamarin.Forms.Span.Text) enlazables tienen un modo de enlace predeterminado de [`OneWay`](xref:Xamarin.Forms.BindingMode) . Para obtener más información acerca de este modo de enlace, vea [el modo de enlace predeterminado](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#the-default-binding-mode) en la guía del [modo de enlace](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md) .

Además, la [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) propiedad se puede utilizar para definir una colección de reconocedores de gestos que responderán a los gestos en [`Span`](xref:Xamarin.Forms.Span) .

> [!NOTE]
> No es posible mostrar HTML en [`Span`](xref:Xamarin.Forms.Span) .

En el siguiente ejemplo de XAML se muestra una `FormattedText` propiedad que consta de tres [`Span`](xref:Xamarin.Forms.Span) instancias:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo - XAML">
    <StackLayout Padding="5,10">
        ...
        <Label LineBreakMode="WordWrap">
            <Label.FormattedText>
                <FormattedString>
                    <Span Text="Red Bold, " TextColor="Red" FontAttributes="Bold" />
                    <Span Text="default, " Style="{DynamicResource BodyStyle}">
                        <Span.GestureRecognizers>
                            <TapGestureRecognizer Command="{Binding TapCommand}" />
                        </Span.GestureRecognizers>
                    </Span>
                    <Span Text="italic small." FontAttributes="Italic" FontSize="Small" />
                </FormattedString>
            </Label.FormattedText>
        </Label>
    </StackLayout>
</ContentPage>
```

El código de C# equivalente es el siguiente:

```csharp
public class LabelPageCode : ContentPage
{
    public LabelPageCode ()
    {
        var layout = new StackLayout{ Padding = new Thickness (5, 10) };
        ...
        var formattedString = new FormattedString ();
        formattedString.Spans.Add (new Span{ Text = "Red bold, ", ForegroundColor = Color.Red, FontAttributes = FontAttributes.Bold });

        var span = new Span { Text = "default, " };
        span.GestureRecognizers.Add(new TapGestureRecognizer { Command = new Command(async () => await DisplayAlert("Tapped", "This is a tapped Span.", "OK")) });
        formattedString.Spans.Add(span);
        formattedString.Spans.Add (new Span { Text = "italic small.", FontAttributes = FontAttributes.Italic, FontSize =  Device.GetNamedSize(NamedSize.Small, typeof(Label)) });

        layout.Children.Add (new Label { FormattedText = formattedString });
        this.Content = layout;
    }
}
```

> [!IMPORTANT]
> La [`Text`](xref:Xamarin.Forms.Span.Text) propiedad de `Span` se puede establecer mediante el enlace de datos. Para obtener más información, vea [Enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Tenga en cuenta que [`Span`](xref:Xamarin.Forms.Span) también puede responder a cualquier gesto que se agregue a la colección del intervalo [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) . Por ejemplo, se ha [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) agregado a la segunda `Span` en los ejemplos de código anteriores. Por lo tanto, cuando `Span` se puntea `TapGestureRecognizer` en, responderá ejecutando el `ICommand` definido por la [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) propiedad. Para obtener más información acerca de los reconocedores de gestos, consulte [ Xamarin.Forms gestos](~/xamarin-forms/app-fundamentals/gestures/index.md).

Las siguientes capturas de pantallas muestran el resultado de establecer la `FormattedString` propiedad en tres `Span` instancias:

![Ejemplo de etiqueta de FormattedText](label-images/formattedtext.png)

## <a name="line-height"></a>Alto de línea

El alto vertical de un [`Label`](xref:Xamarin.Forms.Label) y un [`Span`](xref:Xamarin.Forms.Span) se puede personalizar estableciendo la [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) propiedad o [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) en un `double` valor. En iOS y Android, estos valores son multiplicadores del alto de línea original y, en el Plataforma universal de Windows (UWP), el `Label.LineHeight` valor de propiedad es un multiplicador del tamaño de fuente de la etiqueta.

> [!NOTE]
>
> - En iOS, las [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) propiedades y cambian el alto de línea del texto que cabe en una sola línea y el texto que se ajusta en varias líneas.
> - En Android, las [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) propiedades y solo cambian el alto de línea del texto que se ajusta en varias líneas.
> - En UWP, la [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) propiedad cambia el alto de línea del texto que se ajusta en varias líneas, y la [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) propiedad no tiene ningún efecto.

En el siguiente ejemplo de XAML se muestra cómo establecer la [`LineHeight`](xref:Xamarin.Forms.Label.LineHeight) propiedad en un [`Label`](xref:Xamarin.Forms.Label) :

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       LineHeight="1.8" />
```

El código de C# equivalente es el siguiente:

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  LineHeight = 1.8
};
```

Las capturas de pantallas siguientes muestran el resultado de establecer la [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) propiedad en 1,8:

![Ejemplo de LineHeight de etiqueta](label-images/label-lineheight.png)

En el siguiente ejemplo de XAML se muestra cómo establecer la [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) propiedad en un [`Span`](xref:Xamarin.Forms.Span) :

```xaml
<Label LineBreakMode="WordWrap">
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In a tincidunt sem. Phasellus mollis sit amet turpis in rutrum. Sed aliquam ac urna id scelerisque. "
                  LineHeight="1.8"/>
            <Span Text="Nullam feugiat sodales elit, et maximus nibh vulputate id."
                  LineHeight="1.8" />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

El código de C# equivalente es el siguiente:

```csharp
var formattedString = new FormattedString();
formattedString.Spans.Add(new Span
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In a tincidunt sem. Phasellus mollis sit amet turpis in rutrum. Sed aliquam ac urna id scelerisque. ",
  LineHeight = 1.8
});
formattedString.Spans.Add(new Span
{
  Text = "Nullam feugiat sodales elit, et maximus nibh vulputate id.",
  LineHeight = 1.8
});
var label = new Label
{
  FormattedText = formattedString,
  LineBreakMode = LineBreakMode.WordWrap
};
```

Las capturas de pantallas siguientes muestran el resultado de establecer la [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) propiedad en 1,8:

![Ejemplo de LineHeight de intervalo](label-images/span-lineheight.png)

## <a name="padding"></a>Espaciado interno

El relleno representa el espacio que hay entre un elemento y sus elementos secundarios, y se utiliza para separar el elemento de su propio contenido. El relleno se puede aplicar a [`Label`](xref:Xamarin.Forms.Label) las instancias estableciendo la `Label.Padding` propiedad en un [`Thickness`](xref:Xamarin.Forms.Thickness) valor:

```xaml
<Label Padding="10">
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Lorem ipsum" />
            <Span Text="dolor sit amet." />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

El código de C# equivalente es el siguiente:

```csharp
FormattedString formattedString = new FormattedString();
formattedString.Spans.Add(new Span
{
  Text = "Lorem ipsum"
});
formattedString.Spans.Add(new Span
{
  Text = "dolor sit amet."
});
Label label = new Label
{
    FormattedText = formattedString,
    Padding = new Thickness(20)
};
```

> [!IMPORTANT]
> En iOS, cuando [`Label`](xref:Xamarin.Forms.Label) se crea un que establece la `Padding` propiedad, se aplicará el relleno y el valor de relleno se podrá actualizar más adelante. Sin embargo, cuando `Label` se crea una que no establece la `Padding` propiedad, intentar establecerla más tarde no tendrá ningún efecto.
>
> En Android y en el Plataforma universal de Windows, el valor de la `Padding` propiedad se puede especificar cuando `Label` se crea o después.

Para obtener más información sobre el relleno, vea [márgenes y relleno](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="hyperlinks"></a>Hipervínculos

El texto que se muestra en [`Label`](xref:Xamarin.Forms.Label) [`Span`](xref:Xamarin.Forms.Span) las instancias de y se puede convertir en hipervínculos con el siguiente enfoque:

1. Establezca las `TextColor` `TextDecoration` propiedades y de [`Label`](xref:Xamarin.Forms.Label) o [`Span`](xref:Xamarin.Forms.Span) .
1. Agregue un [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) a la [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) colección de [`Label`](xref:Xamarin.Forms.Label) o [`Span`](xref:Xamarin.Forms.Span) , cuya [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) propiedad se enlaza a un `ICommand` , y cuya [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) propiedad contiene la dirección URL que se va a abrir.
1. Defina el `ICommand` que va a ejecutar [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) .
1. Escriba el código que va a ejecutar `ICommand` .

En el ejemplo de código siguiente, tomado del ejemplo de [demostraciones de hipervínculo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) , se muestra un [`Label`](xref:Xamarin.Forms.Label) cuyo contenido se establece a partir de varias [`Span`](xref:Xamarin.Forms.Span) instancias:

```xaml
<Label>
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Alternatively, click " />
            <Span Text="here"
                  TextColor="Blue"
                  TextDecorations="Underline">
                <Span.GestureRecognizers>
                    <TapGestureRecognizer Command="{Binding TapCommand}"
                                          CommandParameter="https://docs.microsoft.com/xamarin/" />
                </Span.GestureRecognizers>
            </Span>
            <Span Text=" to view Xamarin documentation." />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

En este ejemplo, la primera y la tercera [`Span`](xref:Xamarin.Forms.Span) instancia comprenden el texto, mientras que la segunda `Span` representa un hipervínculo tappable. Su color de texto se establece en azul y tiene una decoración de texto de subrayado. Esto crea la apariencia de un hipervínculo, tal como se muestra en las siguientes capturas de pantallas:

[![Hipervínculos](label-images/hyperlinks-small.png "Hipervínculos")](label-images/hyperlinks-large.png#lightbox)

Cuando se puntea el hipervínculo, [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) responderá ejecutando el `ICommand` definido por su [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) propiedad. Además, la dirección URL especificada por la [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) propiedad se pasará a `ICommand` como un parámetro.

El código subyacente de la página XAML contiene la `TapCommand` implementación:

```csharp
public partial class MainPage : ContentPage
{
    // Launcher.OpenAsync is provided by Xamarin.Essentials.
    public ICommand TapCommand => new Command<string>(async (url) => await Launcher.OpenAsync(url));

    public MainPage()
    {
        InitializeComponent();
        BindingContext = this;
    }
}
```

`TapCommand`Ejecuta el `Launcher.OpenAsync` método, pasando el valor de la [`TapGestureRecognizer.CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) propiedad como un parámetro. `Launcher.OpenAsync`Proporciona el método Xamarin.Essentials y abre la dirección URL en un explorador Web. Por lo tanto, el efecto general es que cuando se puntea el hipervínculo en la página, aparece un explorador Web y se navega a la dirección URL asociada al hipervínculo.

### <a name="creating-a-reusable-hyperlink-class"></a>Crear una clase de hipervínculo reutilizable

El enfoque anterior para crear un hipervínculo requiere la escritura de código repetitivo cada vez que se requiere un hipervínculo en la aplicación. Sin embargo, [`Label`](xref:Xamarin.Forms.Label) [`Span`](xref:Xamarin.Forms.Span) se pueden crear subclases para las clases y `HyperlinkLabel` `HyperlinkSpan` , con el reconocedor de gestos y el código de formato de texto agregado allí.

En el ejemplo de código siguiente, tomado del ejemplo de [demostraciones de hipervínculo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) , se muestra una `HyperlinkSpan` clase:

```csharp
public class HyperlinkSpan : Span
{
    public static readonly BindableProperty UrlProperty =
        BindableProperty.Create(nameof(Url), typeof(string), typeof(HyperlinkSpan), null);

    public string Url
    {
        get { return (string)GetValue(UrlProperty); }
        set { SetValue(UrlProperty, value); }
    }

    public HyperlinkSpan()
    {
        TextDecorations = TextDecorations.Underline;
        TextColor = Color.Blue;
        GestureRecognizers.Add(new TapGestureRecognizer
        {
            // Launcher.OpenAsync is provided by Xamarin.Essentials.
            Command = new Command(async () => await Launcher.OpenAsync(Url))
        });
    }
}
```

La `HyperlinkSpan` clase define una `Url` propiedad, y [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) se asocia, y el constructor establece la apariencia del hipervínculo y el objeto [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) que responderá cuando se puntee en el hipervínculo. Cuando `HyperlinkSpan` se puntea en, el `TapGestureRecognizer` responderá ejecutando el `Launcher.OpenAsync` método para abrir la dirección URL, especificada por la `Url` propiedad, en un explorador Web.

La `HyperlinkSpan` clase se puede consumir agregando una instancia de la clase a XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:HyperlinkDemo"
             x:Class="HyperlinkDemo.MainPage">
    <StackLayout>
        ...
        <Label>
            <Label.FormattedText>
                <FormattedString>
                    <Span Text="Alternatively, click " />
                    <local:HyperlinkSpan Text="here"
                                         Url="https://docs.microsoft.com/appcenter/" />
                    <Span Text=" to view AppCenter documentation." />
                </FormattedString>
            </Label.FormattedText>
        </Label>
    </StackLayout>
</ContentPage>
```

## <a name="styling-labels"></a>Aplicar estilo a etiquetas

En las secciones anteriores se describe la configuración [`Label`](xref:Xamarin.Forms.Label) y [`Span`](xref:Xamarin.Forms.Span) las propiedades en cada instancia. Sin embargo, los conjuntos de propiedades se pueden agrupar en un estilo que se aplica de forma coherente a una o varias vistas. Esto puede aumentar la legibilidad del código y facilitar la implementación de los cambios de diseño. Para obtener más información, vea [estilos](~/xamarin-forms/user-interface/text/styles.md).

## <a name="related-links"></a>Vínculos relacionados

- [Texto (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Hipervínculos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks)
- [Creación de Mobile Apps con Xamarin.Forms , capítulo 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [API de las etiquetas](xref:Xamarin.Forms.Label)
- [API de span](xref:Xamarin.Forms.Span)
