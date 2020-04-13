---
title: Etiqueta de Xamarin.Forms
description: En este artículo se explica cómo usar la clase Label de Xamarin.Forms para mostrar texto de una y varias líneas en las aplicaciones.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/09/2020
ms.openlocfilehash: bed6b8a774ecb352427f16b78d10e50821f92701
ms.sourcegitcommit: 765b69ed451a0f48625ea597c3f39de95f3ae693
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/09/2020
ms.locfileid: "80987600"
---
# <a name="xamarinforms-label"></a>Etiqueta de Xamarin.Forms

[![Descargar](~/media/shared/download.png) ejemplo Descargue el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Mostrar texto en Xamarin.Forms_

La [`Label`](xref:Xamarin.Forms.Label) vista se utiliza para mostrar texto, tanto de una sola como de varias líneas. Las etiquetas pueden tener decoraciones de texto, texto de color y usar fuentes personalizadas (familias, tamaños y opciones).

## <a name="text-decorations"></a>Decoraciones de texto

Las decoraciones de texto de [`Label`](xref:Xamarin.Forms.Label) subrayado y tachado se pueden aplicar a las instancias estableciendo la `Label.TextDecorations` propiedad en uno o varios `TextDecorations` miembros de enumeración:

- `None`
- `Underline`
- `Strikethrough`

En el ejemplo XAML `Label.TextDecorations` siguiente se muestra cómo establecer la propiedad:

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

Las capturas de `TextDecorations` pantalla siguientes [`Label`](xref:Xamarin.Forms.Label) muestran los miembros de enumeración aplicados a las instancias:

![Etiquetas con decoraciones de texto](label-images/label-textdecorations.png)

> [!NOTE]
> Las decoraciones de texto [`Span`](xref:Xamarin.Forms.Span) también se pueden aplicar a instancias. Para obtener más `Span` información acerca de la clase, vea [Texto con formato](#Formatted_Text).

## <a name="character-spacing"></a>espaciado entre caracteres

El espaciado de [`Label`](xref:Xamarin.Forms.Label) caracteres se `Label.CharacterSpacing` puede aplicar `double` a las instancias estableciendo la propiedad en un valor:

```xaml
<Label Text="Character spaced text"
       CharacterSpacing="10" />
```

El código de C# equivalente es el siguiente:

```csharp
Label label = new Label { Text = "Character spaced text", CharacterSpacing = 10 };
```

El resultado es que los caracteres [`Label`](xref:Xamarin.Forms.Label) del `CharacterSpacing` texto mostrado por las unidades independientes del dispositivo están separadas.

## <a name="new-lines"></a>Nuevas líneas

Hay dos técnicas principales para [`Label`](xref:Xamarin.Forms.Label) forzar texto en una nueva línea, desde XAML:

1. Utilice el carácter de avance de línea unicode, que es "&#10;".
1. Especifique el texto mediante la sintaxis de elemento de *propiedad.*

El código siguiente muestra un ejemplo de ambas técnicas:

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

En C, el texto se puede forzar a una nueva línea con el carácter "n":

```csharp
Label label = new Label { Text = "First line\nSecond line" };
```

## <a name="colors"></a>Colores

Las etiquetas se pueden establecer para usar [`TextColor`](xref:Xamarin.Forms.Label.TextColor) un color de texto personalizado a través de la propiedad enlazable.

Se recomienda un cuidado especial para garantizar que los colores sean utilizables en cada plataforma. Debido a que cada plataforma tiene diferentes valores predeterminados para el texto y los colores de fondo, tendrá que tener cuidado de elegir un valor predeterminado que funcione en cada uno.

En el siguiente ejemplo XAML `Label`se establece el color de texto de un:

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

Las siguientes capturas de pantalla `TextColor` muestran el resultado de establecer la propiedad:

![Ejemplo de TextColor de etiqueta](label-images/textcolor.png)

Para obtener más información acerca de los colores, consulte [Colores](~/xamarin-forms/user-interface/colors.md).

## <a name="fonts"></a>Fuentes

Para obtener más información sobre `Label`cómo especificar fuentes en un , vea [Fuentes](~/xamarin-forms/user-interface/text/fonts.md).

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Truncamiento y envoltura

Las etiquetas se pueden establecer para controlar el texto que no cabe `LineBreakMode` en una línea de una de varias maneras, expuestas por la propiedad. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode)es una enumeración con los siguientes valores:

- **HeadTruncation** &ndash; trunca la cabeza del texto, mostrando el final.
- **CharacterWrap** &ndash; ajusta el texto en una nueva línea en un límite de caracteres.
- **MiddleTruncation** &ndash; muestra el principio y el final del texto, con el medio reemplazar por puntos suspensivos.
- **NoWrap** &ndash; no ajusta el texto, mostrando solo tanto texto como pueda caber en una línea.
- **TailTruncation** &ndash; muestra el principio del texto, truncando el final.
- **WordWrap** &ndash; ajusta el texto en el límite de la palabra.

## <a name="display-a-specific-number-of-lines"></a>Mostrar un número específico de líneas

El número de líneas [`Label`](xref:Xamarin.Forms.Label) mostradas por a `Label.MaxLines` se puede `int` especificar estableciendo la propiedad en un valor:

- Cuando `MaxLines` es -1, que es `Label` su valor predeterminado, [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) respeta el valor de la propiedad para mostrar solo una línea, posiblemente truncada, o todas las líneas con todo el texto.
- Cuando `MaxLines` es 0, no se muestra el. `Label`
- Cuando `MaxLines` es 1, el resultado [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) es [`NoWrap`](xref:Xamarin.Forms.LineBreakMode)idéntico [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode) [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode)a [`TailTruncation`](xref:Xamarin.Forms.LineBreakMode)establecer la propiedad en , , , o . Sin `Label` embargo, el respetará el valor de la propiedad con respecto a la [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) colocación de un punto suspensivo, si corresponde.
- Cuando `MaxLines` es mayor que `Label` 1, el mostrará hasta el número especificado de [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) líneas, respetando el valor de la propiedad con respecto a la colocación de puntos suspensivos, si corresponde. Sin embargo, establecer la `MaxLines` propiedad en un [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) valor mayor [`NoWrap`](xref:Xamarin.Forms.LineBreakMode)que 1 no tiene ningún efecto si la propiedad se establece en .

En el ejemplo XAML `MaxLines` siguiente se [`Label`](xref:Xamarin.Forms.Label)muestra cómo establecer la propiedad en:

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

Las siguientes capturas de pantalla `MaxLines` muestran el resultado de establecer la propiedad en 2, cuando el texto es lo suficientemente largo como para ocupar más de 2 líneas:

![Ejemplo de MaxLines de etiqueta](label-images/label-maxlines.png)

## <a name="display-html"></a>mostrar HTML

La [`Label`](xref:Xamarin.Forms.Label) clase `TextType` tiene una propiedad, `Label` que determina si la instancia debe mostrar texto sin formato o texto HTML. Esta propiedad debe establecerse en uno `TextType` de los miembros de la enumeración:

- `Text`indica que `Label` el mostrará texto sin formato y `Label.TextType` es el valor predeterminado de la propiedad.
- `Html`indica que `Label` el texto HTML mostrará.

Por [`Label`](xref:Xamarin.Forms.Label) lo tanto, las instancias pueden mostrar HTML estableciendo la `Label.TextType` propiedad en `Html`, y la `Label.Text` propiedad en una cadena HTML:

```csharp
Label label = new Label
{
    Text = "This is <strong style=\"color:red\">HTML</strong> text.",
    TextType = TextType.Html
};
```

En el ejemplo anterior, los caracteres de comillas `\` dobles en el HTML tienen que ser escapados mediante el símbolo.

En XAML, las cadenas HTML pueden volverse ilegibles debido a que además escapan de los `<` símbolos y: `>`

```xaml
<Label Text="This is &lt;strong style=&quot;color:red&quot;&gt;HTML&lt;/strong&gt; text."
       TextType="Html"  />
```

Alternativamente, para una mayor legibilidad, el `CDATA` HTML se puede insertar en una sección:

```xaml
<Label TextType="Html">
    <![CDATA[
    This is <strong style="color:red">HTML</strong> text.
    ]]>
</Label>
```

En este ejemplo, la `Label.Text` propiedad se establece en la `CDATA` cadena HTML que está insertada en la sección. Esto funciona `Text` porque la `ContentProperty` propiedad `Label` es la para la clase.

Las siguientes capturas [`Label`](xref:Xamarin.Forms.Label) de pantalla muestran un HTML que muestra:

![Capturas de pantalla de una etiqueta que muestra HTML, en iOS y Android](label-images/label-html.png)

> [!IMPORTANT]
> La visualización [`Label`](xref:Xamarin.Forms.Label) de HTML en a se limita a las etiquetas HTML admitidas por la plataforma subyacente.

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Texto con formato

Las etiquetas [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) exponen una propiedad que permite la presentación de texto con varias fuentes y colores en la misma vista.

La `FormattedText` propiedad es [`FormattedString`](xref:Xamarin.Forms.FormattedString)de tipo , [`Span`](xref:Xamarin.Forms.Span) que consta de [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) una o varias instancias, establecidas a través de la propiedad. Las `Span` siguientes propiedades se pueden utilizar para establecer la apariencia visual:

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor)– el color del fondo del palmo.
- `CharacterSpacing`, del tipo `double`, es el espaciado entre los caracteres del texto de `Span`.
- [`Font`](xref:Xamarin.Forms.Span.Font)– la fuente del texto en el intervalo.
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes)– los atributos de fuente para el texto en el intervalo.
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily)– la familia de fuentes a la que pertenece la fuente del texto del intervalo.
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize)– el tamaño de la fuente para el texto en el intervalo.
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor)– el color del texto en el intervalo. Esta propiedad está obsoleta y se `TextColor` ha reemplazado por la propiedad.
- [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight)- el multiplicador que se aplicará a la altura de línea predeterminada del intervalo. Para obtener más información, consulte [Altura de línea](#line-height).
- [`Style`](xref:Xamarin.Forms.Span.Style)– el estilo que se aplicará al intervalo.
- [`Text`](xref:Xamarin.Forms.Span.Text)– el texto del intervalo.
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor)– el color del texto en el intervalo.
- `TextDecorations`- las decoraciones para aplicar al texto en el intervalo. Para obtener más información, consulte [Decoraciones](#text-decorations)de texto .

Las [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) [`Text`](xref:Xamarin.Forms.Span.Text)propiedades [`Text`](xref:Xamarin.Forms.Span.Text) , , y enlazables [`OneWay`](xref:Xamarin.Forms.BindingMode)tienen un modo de enlace predeterminado de . Para obtener más información acerca de este modo de enlace, vea el modo de [enlace predeterminado](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#the-default-binding-mode) en el modo de [enlace](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md) guía.

Además, [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) la propiedad se puede utilizar para definir una colección de [`Span`](xref:Xamarin.Forms.Span)reconocedores de gestos que responderán a los gestos en el archivo .

> [!NOTE]
> No es posible mostrar HTML [`Span`](xref:Xamarin.Forms.Span)en un archivo .

En el ejemplo XAML `FormattedText` siguiente se [`Span`](xref:Xamarin.Forms.Span) muestra una propiedad que consta de tres instancias:

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
> La [`Text`](xref:Xamarin.Forms.Span.Text) propiedad `Span` de un se puede establecer a través del enlace de datos. Para obtener más información, vea [Enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Tenga en [`Span`](xref:Xamarin.Forms.Span) cuenta que a también puede responder a [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) los gestos que se agregan a la colección del intervalo. Por ejemplo, [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) a se ha `Span` agregado a la segunda en los ejemplos de código anteriores. Por lo `Span` tanto, `TapGestureRecognizer` cuando se toca `ICommand` el responderá [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) ejecutando el definido por la propiedad. Para obtener más información acerca de los reconocedores de gestos, vea [Xamarin.Forms Gestures](~/xamarin-forms/app-fundamentals/gestures/index.md).

Las capturas de pantalla siguientes `FormattedString` muestran `Span` el resultado de establecer la propiedad en tres instancias:

![Ejemplo de Label FormattedText](label-images/formattedtext.png)

## <a name="line-height"></a>Alto de línea

La altura vertical [`Label`](xref:Xamarin.Forms.Label) de [`Span`](xref:Xamarin.Forms.Span) a y a [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) se [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) puede `double` personalizar estableciendo la propiedad o en un valor. En iOS y Android, estos valores son multiplicadores de la altura de `Label.LineHeight` línea original y en la Plataforma universal de Windows (UWP) el valor de propiedad es un multiplicador del tamaño de fuente de la etiqueta.

> [!NOTE]
>
> - En iOS, [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) las propiedades y cambian la altura de línea del texto que se ajusta a una sola línea y el texto que se ajusta en varias líneas.
> - En Android, [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) las propiedades y solo cambian el alto de línea del texto que se ajusta a varias líneas.
> - En UWP, [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) la propiedad cambia el alto de línea del [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) texto que se ajusta a varias líneas y la propiedad no tiene ningún efecto.

En el ejemplo XAML [`LineHeight`](xref:Xamarin.Forms.Label.LineHeight) siguiente se [`Label`](xref:Xamarin.Forms.Label)muestra cómo establecer la propiedad en:

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

Las capturas de pantalla siguientes [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) muestran el resultado de establecer la propiedad en 1.8:

![Ejemplo de Label LineHeight](label-images/label-lineheight.png)

En el ejemplo XAML [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) siguiente se [`Span`](xref:Xamarin.Forms.Span)muestra cómo establecer la propiedad en:

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

Las capturas de pantalla siguientes [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) muestran el resultado de establecer la propiedad en 1.8:

![Ejemplo de Span LineHeight](label-images/span-lineheight.png)

## <a name="padding"></a>Relleno

El relleno representa el espacio entre un elemento y sus elementos secundarios y se utiliza para separar el elemento de su propio contenido. El relleno se [`Label`](xref:Xamarin.Forms.Label) puede aplicar `Label.Padding` a las [`Thickness`](xref:Xamarin.Forms.Thickness) instancias estableciendo la propiedad en un valor:

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
> En iOS, [`Label`](xref:Xamarin.Forms.Label) cuando se crea `Padding` un que establece la propiedad, se aplicará el relleno y el valor de relleno se puede actualizar más adelante. Sin embargo, cuando se crea `Label` `Padding` un que no establece la propiedad, intentar establecerla más adelante no tendrá ningún efecto.
>
> En Android y la Plataforma `Padding` universal de Windows, `Label` el valor de propiedad se puede especificar cuando se crea o posterior.

Para obtener más información sobre el relleno, vea [Márgenes y relleno](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="hyperlinks"></a>Hipervínculos

El texto mostrado [`Label`](xref:Xamarin.Forms.Label) [`Span`](xref:Xamarin.Forms.Span) por las instancias y las instancias se puede convertir en hipervínculos con el siguiente enfoque:

1. Establezca `TextColor` las `TextDecoration` propiedades [`Label`](xref:Xamarin.Forms.Label) y [`Span`](xref:Xamarin.Forms.Span)de la o .
1. Agregue [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) a [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) la colección [`Span`](xref:Xamarin.Forms.Span)de [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) la [`Label`](xref:Xamarin.Forms.Label) o `ICommand`, cuya [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) propiedad se enlaza a un , y cuya propiedad contiene la dirección URL que se va a abrir.
1. Defina `ICommand` el [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)archivo .
1. Escriba el código que ejecutará `ICommand`el archivo .

En el ejemplo de código siguiente, tomado [`Label`](xref:Xamarin.Forms.Label) del ejemplo de [`Span`](xref:Xamarin.Forms.Span) demostraciones de [hipervínculo,](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) se muestra un cuyo contenido se establece desde varias instancias:

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

En este ejemplo, la [`Span`](xref:Xamarin.Forms.Span) primera y la tercera `Span` instancias comprenden texto, mientras que la segunda representa un hipervínculo que se puede aplicar. Tiene su color de texto establecido en azul, y tiene una decoración de texto subrayado. Esto crea la apariencia de un hipervínculo, como se muestra en las siguientes capturas de pantalla:

[![Hipervínculos](label-images/hyperlinks-small.png "Hipervínculos")](label-images/hyperlinks-large.png#lightbox)

Cuando se toca el [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) hipervínculo, el `ICommand` responderá ejecutando el definido por su [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) propiedad. Además, la dirección [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) URL especificada por `ICommand` la propiedad se pasará a como parámetro.

El código subyacente de la `TapCommand` página XAML contiene la implementación:

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

El `TapCommand` ejecuta `Launcher.OpenAsync` el método, [`TapGestureRecognizer.CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) pasando el valor de propiedad como un parámetro. Xamarin.Essentials `Launcher.OpenAsync` proporciona el método y abre la dirección URL en un explorador web. Por lo tanto, el efecto general es que cuando se toca el hipervínculo en la página, aparece un explorador web y se navega la dirección URL asociada con el hipervínculo.

### <a name="creating-a-reusable-hyperlink-class"></a>Creación de una clase de hipervínculo reutilizable

El enfoque anterior para crear un hipervínculo requiere escribir código repetitivo cada vez que se requiere un hipervínculo en la aplicación. Sin [`Label`](xref:Xamarin.Forms.Label) embargo, [`Span`](xref:Xamarin.Forms.Span) tanto las clases `HyperlinkLabel` como `HyperlinkSpan` las pueden subclases para crear y clases, con el reconocedor de gestos y el código de formato de texto agregado allí.

En el ejemplo de código siguiente, tomado `HyperlinkSpan` del ejemplo [de demostraciones](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) de hipervínculo, se muestra una clase:

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

La `HyperlinkSpan` clase `Url` define una [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)propiedad y asociada , y [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) el constructor establece la apariencia del hipervínculo y la que responderá cuando se toca el hipervínculo. Cuando `HyperlinkSpan` se toca `TapGestureRecognizer` a, el responderá `Launcher.OpenAsync` ejecutando el método para `Url` abrir la dirección URL, especificada por la propiedad, en un explorador web.

La `HyperlinkSpan` clase se puede consumir agregando una instancia de la clase al XAML:

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

## <a name="styling-labels"></a>Etiquetas de estilo

Las secciones [`Label`](xref:Xamarin.Forms.Label) anteriores abarcaba la configuración y [`Span`](xref:Xamarin.Forms.Span) las propiedades por instancia. Sin embargo, los conjuntos de propiedades se pueden agrupar en un estilo que se aplica de forma coherente a una o varias vistas. Esto puede aumentar la legibilidad del código y facilitar la implementación de los cambios de diseño. Para obtener más información, consulte [Estilos](~/xamarin-forms/user-interface/text/styles.md).

## <a name="related-links"></a>Vínculos relacionados

- [Texto (muestra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Hipervínculos (muestra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks)
- [Creación de aplicaciones móviles con Xamarin.Forms, capítulo 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [API de las etiquetas](xref:Xamarin.Forms.Label)
- [Span API](xref:Xamarin.Forms.Span)
