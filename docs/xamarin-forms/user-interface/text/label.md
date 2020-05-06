---
title: Etiqueta de Xamarin. Forms
description: En este artículo se explica cómo usar la clase de etiqueta de Xamarin. Forms para mostrar texto de una sola línea y de varias líneas en las aplicaciones.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/09/2020
ms.openlocfilehash: 6ce4e39986afb7444e7d126e9789760d9311ca45
ms.sourcegitcommit: 737c7fbbe8aed1f33110a5217d7e6d6ef3e5b785
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793227"
---
# <a name="xamarinforms-label"></a>Etiqueta de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Mostrar texto en Xamarin. Forms_

La [`Label`](xref:Xamarin.Forms.Label) vista se usa para mostrar texto, tanto de una sola línea como de varias líneas. Las etiquetas pueden tener decoraciones de texto, texto de color y usar fuentes personalizadas (familias, tamaños y opciones).

## <a name="text-decorations"></a>Decoraciones de texto

Las decoraciones de texto de subrayado [`Label`](xref:Xamarin.Forms.Label) y tachado se `Label.TextDecorations` pueden aplicar a las instancias `TextDecorations` estableciendo la propiedad en uno o varios miembros de la enumeración:

- `None`
- `Underline`
- `Strikethrough`

En el siguiente ejemplo de XAML se `Label.TextDecorations` muestra cómo establecer la propiedad:

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

Las capturas de pantallas `TextDecorations` siguientes muestran los miembros [`Label`](xref:Xamarin.Forms.Label) de enumeración que se aplican a las instancias:

![Etiquetas con decoraciones de texto](label-images/label-textdecorations.png)

> [!NOTE]
> También se pueden aplicar decoraciones de [`Span`](xref:Xamarin.Forms.Span) texto a las instancias de. Para obtener más información sobre `Span` la clase, vea [texto con formato](#Formatted_Text).

## <a name="character-spacing"></a>espaciado entre caracteres

El espaciado de caracteres se puede [`Label`](xref:Xamarin.Forms.Label) aplicar a las instancias `Label.CharacterSpacing` estableciendo la propiedad `double` en un valor:

```xaml
<Label Text="Character spaced text"
       CharacterSpacing="10" />
```

El código de C# equivalente es el siguiente:

```csharp
Label label = new Label { Text = "Character spaced text", CharacterSpacing = 10 };
```

El resultado es que los caracteres del texto mostrados por [`Label`](xref:Xamarin.Forms.Label) están separados por `CharacterSpacing` las unidades independientes del dispositivo.

## <a name="new-lines"></a>Nuevas líneas

Hay dos técnicas principales para forzar el texto de [`Label`](xref:Xamarin.Forms.Label) una en una nueva línea, desde XAML:

1. Use el carácter de salto de línea Unicode, que&amp;es "#10;".
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

En el siguiente ejemplo de XAML se establece el color `Label`del texto de un:

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

Las siguientes capturas de pantallas muestran el resultado `TextColor` de establecer la propiedad:

![Ejemplo de etiqueta TextColor](label-images/textcolor.png)

Para obtener más información sobre los colores, vea [colores](~/xamarin-forms/user-interface/colors.md).

## <a name="fonts"></a>Fuentes

Para obtener más información sobre cómo especificar fuentes en `Label`un, vea [fuentes](~/xamarin-forms/user-interface/text/fonts.md).

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Truncamiento y ajuste

Las etiquetas se pueden establecer para controlar el texto que no cabe en una línea de una de varias maneras, expuesta `LineBreakMode` por la propiedad. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode)es una enumeración con los valores siguientes:

- **HeadTruncation** &ndash; trunca el encabezado del texto y muestra el final.
- **CharacterWrap** &ndash; ajusta el texto en una nueva línea en un límite de caracteres.
- **MiddleTruncation** &ndash; muestra el principio y el final del texto, con el medio de reemplazar por puntos suspensivos.
- **NoWrap** &ndash; no ajusta el texto y muestra solo el texto que quepa en una línea.
- **TailTruncation** &ndash; muestra el principio del texto, truncando el final.
- **WordWrap** &ndash; ajusta el texto en el límite de palabras.

## <a name="display-a-specific-number-of-lines"></a>Mostrar un número específico de líneas

Se [`Label`](xref:Xamarin.Forms.Label) puede especificar el número de líneas que se muestran mediante el establecimiento `Label.MaxLines` de la propiedad `int` en un valor:

- Cuando `MaxLines` es-1, que es su valor predeterminado, `Label` respeta el valor de la [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) propiedad para mostrar solo una línea, posiblemente truncada, o todas las líneas con todo el texto.
- Cuando `MaxLines` es 0, `Label` no se muestra.
- Cuando `MaxLines` es 1, el resultado es idéntico al establecimiento de [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) la propiedad [`NoWrap`](xref:Xamarin.Forms.LineBreakMode)en [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode), [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode), o [`TailTruncation`](xref:Xamarin.Forms.LineBreakMode). Sin embargo, `Label` el respetará el valor de [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) la propiedad con respecto a la colocación de puntos suspensivos, si procede.
- Cuando `MaxLines` es mayor que 1, `Label` mostrará hasta el número especificado de líneas, respetando el valor de la propiedad con respecto a [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) la posición de los puntos suspensivos, si procede. Sin embargo, si `MaxLines` se establece la propiedad en un valor mayor que 1, no [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) se produce ningún efecto [`NoWrap`](xref:Xamarin.Forms.LineBreakMode)si la propiedad está establecida en.

En el siguiente ejemplo de XAML se `MaxLines` muestra cómo establecer [`Label`](xref:Xamarin.Forms.Label)la propiedad en un:

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

Las siguientes capturas de pantallas muestran el resultado `MaxLines` de establecer la propiedad en 2, cuando el texto es lo suficientemente largo como para ocupar más de 2 líneas:

![Ejemplo de etiqueta MaxLines](label-images/label-maxlines.png)

## <a name="display-html"></a>mostrar HTML

La [`Label`](xref:Xamarin.Forms.Label) clase tiene una `TextType` propiedad, que determina si la `Label` instancia debe mostrar texto sin formato o texto HTML. Esta propiedad debe establecerse en uno de los miembros de la `TextType` enumeración:

- `Text`indica que `Label` mostrará el texto sin formato y es el valor predeterminado de la `Label.TextType` propiedad.
- `Html`indica que mostrará `Label` el texto HTML.

Por lo [`Label`](xref:Xamarin.Forms.Label) tanto, las instancias de pueden mostrar `Label.TextType` HTML estableciendo `Html`la propiedad en `Label.Text` y la propiedad en una cadena HTML:

```csharp
Label label = new Label
{
    Text = "This is <strong style=\"color:red\">HTML</strong> text.",
    TextType = TextType.Html
};
```

En el ejemplo anterior, los caracteres de comillas dobles del código HTML deben usarse con `\` el símbolo de escape.

En XAML, las cadenas HTML se pueden volver ilegibles debido a la escape adicional `<` de `>` los símbolos y:

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

Las capturas de pantallas [`Label`](xref:Xamarin.Forms.Label) siguientes muestran un código HTML que muestra:

![Capturas de pantallas de una etiqueta que muestra HTML, en iOS y Android](label-images/label-html.png)

> [!IMPORTANT]
> La visualización de HTML [`Label`](xref:Xamarin.Forms.Label) en un se limita a las etiquetas HTML admitidas por la plataforma subyacente.

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Texto con formato

Las etiquetas exponen una [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) propiedad que permite la presentación de texto con varias fuentes y colores en la misma vista.

La `FormattedText` propiedad es de tipo [`FormattedString`](xref:Xamarin.Forms.FormattedString), que consta de una o [`Span`](xref:Xamarin.Forms.Span) más instancias, establecida a [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) través de la propiedad. Las siguientes `Span` propiedades se pueden usar para establecer la apariencia visual:

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

Las [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor)propiedades [`Text`](xref:Xamarin.Forms.Span.Text), y [`Text`](xref:Xamarin.Forms.Span.Text) enlazables tienen un modo de enlace predeterminado de [`OneWay`](xref:Xamarin.Forms.BindingMode). Para obtener más información acerca de este modo de enlace, vea [el modo de enlace predeterminado](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#the-default-binding-mode) en la guía del [modo de enlace](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md) .

Además, la [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) propiedad se puede utilizar para definir una colección de reconocedores de gestos que responderán a los [`Span`](xref:Xamarin.Forms.Span)gestos en.

> [!NOTE]
> No es posible mostrar HTML en [`Span`](xref:Xamarin.Forms.Span).

En el siguiente ejemplo de XAML `FormattedText` se muestra una propiedad que [`Span`](xref:Xamarin.Forms.Span) consta de tres instancias:

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
> La [`Text`](xref:Xamarin.Forms.Span.Text) propiedad de se `Span` puede establecer mediante el enlace de datos. Para obtener más información, vea [Enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Tenga en cuenta [`Span`](xref:Xamarin.Forms.Span) que también puede responder a cualquier gesto que se agregue a la colección del [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) intervalo. Por ejemplo, [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) se ha agregado a la segunda `Span` en los ejemplos de código anteriores. Por lo tanto, `Span` cuando se puntea `TapGestureRecognizer` en, responderá ejecutando el `ICommand` definido por la [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) propiedad. Para obtener más información sobre los reconocedores de gestos, consulte [gestos de Xamarin. Forms](~/xamarin-forms/app-fundamentals/gestures/index.md).

Las siguientes capturas de pantallas muestran el resultado `FormattedString` de establecer la `Span` propiedad en tres instancias:

![Ejemplo de etiqueta de FormattedText](label-images/formattedtext.png)

## <a name="line-height"></a>Alto de línea

El alto vertical de un [`Label`](xref:Xamarin.Forms.Label) y un [`Span`](xref:Xamarin.Forms.Span) se puede personalizar estableciendo la [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) propiedad o [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) en un `double` valor. En iOS y Android, estos valores son multiplicadores del alto de línea original y, en el Plataforma universal de Windows (UWP), `Label.LineHeight` el valor de propiedad es un multiplicador del tamaño de fuente de la etiqueta.

> [!NOTE]
>
> - En iOS, las [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) propiedades [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) y cambian el alto de línea del texto que cabe en una sola línea y el texto que se ajusta en varias líneas.
> - En Android, las [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) propiedades [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) y solo cambian el alto de línea del texto que se ajusta en varias líneas.
> - En UWP, la [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) propiedad cambia el alto de línea del texto que se ajusta en varias líneas, y [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) la propiedad no tiene ningún efecto.

En el siguiente ejemplo de XAML se [`LineHeight`](xref:Xamarin.Forms.Label.LineHeight) muestra cómo establecer [`Label`](xref:Xamarin.Forms.Label)la propiedad en un:

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

Las capturas de pantallas siguientes muestran el resultado [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) de establecer la propiedad en 1,8:

![Ejemplo de LineHeight de etiqueta](label-images/label-lineheight.png)

En el siguiente ejemplo de XAML se [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) muestra cómo establecer [`Span`](xref:Xamarin.Forms.Span)la propiedad en un:

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

Las capturas de pantallas siguientes muestran el resultado [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) de establecer la propiedad en 1,8:

![Ejemplo de LineHeight de intervalo](label-images/span-lineheight.png)

## <a name="padding"></a>Relleno

El relleno representa el espacio que hay entre un elemento y sus elementos secundarios, y se utiliza para separar el elemento de su propio contenido. El relleno se puede aplicar [`Label`](xref:Xamarin.Forms.Label) a las instancias estableciendo `Label.Padding` la propiedad en [`Thickness`](xref:Xamarin.Forms.Thickness) un valor:

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
> En iOS, cuando se [`Label`](xref:Xamarin.Forms.Label) crea un que establece la `Padding` propiedad, se aplicará el relleno y el valor de relleno se podrá actualizar más adelante. Sin embargo, cuando `Label` se crea una que no establece `Padding` la propiedad, intentar establecerla más tarde no tendrá ningún efecto.
>
> En Android y en el Plataforma universal de Windows, `Padding` el valor de la propiedad se puede `Label` especificar cuando se crea o después.

Para obtener más información sobre el relleno, vea [márgenes y relleno](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="hyperlinks"></a>Hipervínculos

El texto que [`Label`](xref:Xamarin.Forms.Label) se muestra [`Span`](xref:Xamarin.Forms.Span) en las instancias de y se puede convertir en hipervínculos con el siguiente enfoque:

1. Establezca las `TextColor` propiedades `TextDecoration` y de [`Label`](xref:Xamarin.Forms.Label) o [`Span`](xref:Xamarin.Forms.Span).
1. Agregue un [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) a la [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) colección [`Label`](xref:Xamarin.Forms.Label) de [`Span`](xref:Xamarin.Forms.Span)o, [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) cuya propiedad se enlaza a un `ICommand`, y cuya [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) propiedad contiene la dirección URL que se va a abrir.
1. Defina el `ICommand` que va a ejecutar [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer).
1. Escriba el código que va a ejecutar `ICommand`.

En el ejemplo de código siguiente, tomado del ejemplo de [demostraciones de hipervínculo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) , se muestra un [`Label`](xref:Xamarin.Forms.Label) cuyo contenido [`Span`](xref:Xamarin.Forms.Span) se establece a partir de varias instancias:

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

En este ejemplo, la primera y la [`Span`](xref:Xamarin.Forms.Span) tercera instancia comprenden el texto, mientras `Span` que la segunda representa un hipervínculo tappable. Su color de texto se establece en azul y tiene una decoración de texto de subrayado. Esto crea la apariencia de un hipervínculo, tal como se muestra en las siguientes capturas de pantallas:

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

`TapCommand` Ejecuta el `Launcher.OpenAsync` método, pasando el valor de [`TapGestureRecognizer.CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) la propiedad como un parámetro. Xamarin `Launcher.OpenAsync` . Essentials proporciona el método y abre la dirección URL en un explorador Web. Por lo tanto, el efecto general es que cuando se puntea el hipervínculo en la página, aparece un explorador Web y se navega a la dirección URL asociada al hipervínculo.

### <a name="creating-a-reusable-hyperlink-class"></a>Crear una clase de hipervínculo reutilizable

El enfoque anterior para crear un hipervínculo requiere la escritura de código repetitivo cada vez que se requiere un hipervínculo en la aplicación. Sin [`Label`](xref:Xamarin.Forms.Label) embargo, se pueden [`Span`](xref:Xamarin.Forms.Span) crear `HyperlinkLabel` `HyperlinkSpan` subclases para las clases y, con el reconocedor de gestos y el código de formato de texto agregado allí.

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

La `HyperlinkSpan` clase define una `Url` propiedad, y se [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)asocia, y el constructor establece la apariencia del hipervínculo y el [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) objeto que responderá cuando se puntee en el hipervínculo. Cuando `HyperlinkSpan` se puntea en, el `TapGestureRecognizer` responderá ejecutando el `Launcher.OpenAsync` método para abrir la dirección URL, especificada por la `Url` propiedad, en un explorador Web.

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

En las secciones anteriores se [`Label`](xref:Xamarin.Forms.Label) describe [`Span`](xref:Xamarin.Forms.Span) la configuración y las propiedades en cada instancia. Sin embargo, los conjuntos de propiedades se pueden agrupar en un estilo que se aplica de forma coherente a una o varias vistas. Esto puede aumentar la legibilidad del código y facilitar la implementación de los cambios de diseño. Para obtener más información, vea [estilos](~/xamarin-forms/user-interface/text/styles.md).

## <a name="related-links"></a>Vínculos relacionados

- [Texto (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Hipervínculos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks)
- [Crear Mobile Apps con Xamarin. Forms, capítulo 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [API de las etiquetas](xref:Xamarin.Forms.Label)
- [API de span](xref:Xamarin.Forms.Span)
