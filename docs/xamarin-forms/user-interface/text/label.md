---
title: Etiqueta de Xamarin. Forms
description: En este artículo se explica cómo usar la clase de etiqueta de Xamarin. Forms para mostrar texto de una sola línea y de varias líneas en las aplicaciones.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/26/2019
ms.openlocfilehash: 6ea8195d422da3c64175b164c5fbf2885eb234ab
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696390"
---
# <a name="xamarinforms-label"></a>Etiqueta de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Mostrar texto en Xamarin. Forms_

La vista de [`Label`](xref:Xamarin.Forms.Label) se usa para mostrar texto, tanto de una sola línea como de varias líneas. Las etiquetas pueden tener decoraciones de texto, texto de color y usar fuentes personalizadas (familias, tamaños y opciones).

## <a name="text-decorations"></a>Decoraciones de texto

Las decoraciones de texto de subrayado y tachado se pueden aplicar a [`Label`](xref:Xamarin.Forms.Label) instancias estableciendo la propiedad `Label.TextDecorations` en uno o varios miembros de la enumeración `TextDecorations`:

- `None`
- `Underline`
- `Strikethrough`

En el siguiente ejemplo de XAML se muestra cómo establecer la propiedad `Label.TextDecorations`:

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

Las capturas de pantallas siguientes muestran los miembros de enumeración de `TextDecorations` aplicados a [`Label`](xref:Xamarin.Forms.Label) instances:

![Etiquetas con decoraciones de texto](label-images/label-textdecorations.png)

> [!NOTE]
> También se pueden aplicar decoraciones de texto a instancias de [`Span`](xref:Xamarin.Forms.Span) . Para obtener más información sobre la clase `Span`, vea [texto con formato](#Formatted_Text).

## <a name="character-spacing"></a>Espaciado de caracteres

El espaciado de caracteres se puede aplicar a las instancias de [`Label`](xref:Xamarin.Forms.Label) estableciendo la propiedad `Label.CharacterSpacing` en un valor `double`:

```xaml
<Label Text="Character spaced text"
       CharacterSpacing="10" />
```

El código de C# equivalente es el siguiente:

```csharp
Label label = new Label { Text = "Character spaced text", CharacterSpacing = 10 };
```

El resultado es que los caracteres del texto que muestra el [`Label`](xref:Xamarin.Forms.Label) se espacian `CharacterSpacing` unidades independientes del dispositivo.

## <a name="padding"></a>Relleno

El relleno representa el espacio que hay entre un elemento y sus elementos secundarios, y se utiliza para separar el elemento de su propio contenido. El relleno se puede aplicar a las instancias de [`Label`](xref:Xamarin.Forms.Label) estableciendo la propiedad `Label.Padding` en un valor [`Thickness`](xref:Xamarin.Forms.Thickness) :

```xaml
<Label Text="Padded text"
       Padding="20" />
```

El código de C# equivalente es el siguiente:

```csharp
Label label = new Label
{
    Text = "Padded text",
    Padding = new Thickness(20)
};
```

> [!IMPORTANT]
> En iOS, cuando se crea un [`Label`](xref:Xamarin.Forms.Label) que establece la propiedad `Padding`, se aplicará el relleno y el valor de relleno se podrá actualizar más adelante. Sin embargo, cuando se crea una `Label` que no establece la propiedad `Padding`, intentar establecerla más tarde no tendrá ningún efecto.
>
> En Android y en el Plataforma universal de Windows, se puede especificar el valor de la propiedad `Padding` cuando se crea la `Label` o una versión posterior.

Para obtener más información sobre el relleno, vea [márgenes y relleno](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="colors"></a>Colores

Las etiquetas se pueden establecer para usar un color de texto personalizado a través de la propiedad [`TextColor`](xref:Xamarin.Forms.Label.TextColor) enlazable.

Es necesario tener especial cuidado para asegurarse de que se puedan usar los colores en cada plataforma. Dado que cada plataforma tiene valores predeterminados diferentes para los colores de texto y de fondo, deberá tomar precauciones para elegir un valor predeterminado que funcione en cada uno de ellos.

En el siguiente ejemplo de XAML se establece el color de texto de un `Label`:

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

Las capturas de pantallas siguientes muestran el resultado de establecer la propiedad `TextColor`:

![Ejemplo de etiqueta TextColor](label-images/textcolor.png)

Para obtener más información sobre los colores, vea [colores](~/xamarin-forms/user-interface/colors.md).

## <a name="fonts"></a>Fuentes

Para obtener más información sobre cómo especificar fuentes en un `Label`, vea [fuentes](~/xamarin-forms/user-interface/text/fonts.md).

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Truncamiento y ajuste

Las etiquetas se pueden establecer para controlar el texto que no cabe en una línea de varias maneras, expuesta por la propiedad `LineBreakMode`. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) es una enumeración con los valores siguientes:

- **HeadTruncation** &ndash; trunca el encabezado del texto y muestra el final.
- **CharacterWrap** &ndash; ajusta el texto en una nueva línea en un límite de caracteres.
- **MiddleTruncation** &ndash; muestra el principio y el final del texto, con el medio de reemplazar por puntos suspensivos.
- **NoWrap** &ndash; no ajusta el texto, mostrando solo el texto que quepa en una línea.
- **TailTruncation** &ndash; muestra el principio del texto y trunca el final.
- **WordWrap** &ndash; ajusta el texto en el límite de palabras.

## <a name="display-a-specific-number-of-lines"></a>Mostrar un número específico de líneas

El número de líneas que muestra un [`Label`](xref:Xamarin.Forms.Label) se puede especificar estableciendo la propiedad `Label.MaxLines` en un valor `int`:

- Cuando `MaxLines` es 0, el `Label` respeta el valor de la propiedad [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) para mostrar solo una línea, posiblemente truncada, o todas las líneas con todo el texto.
- Cuando `MaxLines` es 1, el resultado es idéntico al establecimiento de la propiedad [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) en [`NoWrap`](xref:Xamarin.Forms.LineBreakMode), [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode), [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode)o [0](xref:Xamarin.Forms.LineBreakMode). Sin embargo, el `Label` respetará el valor de la propiedad [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) con respecto a la selección de ubicación de puntos suspensivos, si procede.
- Cuando `MaxLines` es mayor que 1, el `Label` mostrará hasta el número especificado de líneas, respetando el valor de la propiedad [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) con respecto a la posición de los puntos suspensivos, si procede. Sin embargo, si se establece la propiedad `MaxLines` en un valor mayor que 1, no se produce ningún efecto si la propiedad [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) está establecida en [`NoWrap`](xref:Xamarin.Forms.LineBreakMode).

En el siguiente ejemplo de XAML se muestra cómo establecer la propiedad `MaxLines` en un [`Label`](xref:Xamarin.Forms.Label):

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

Las siguientes capturas de pantallas muestran el resultado de establecer la propiedad `MaxLines` en 2, cuando el texto es lo suficientemente largo como para ocupar más de 2 líneas:

![Ejemplo de etiqueta MaxLines](label-images/label-maxlines.png)


## <a name="display-html"></a>Mostrar HTML

La clase [`Label`](xref:Xamarin.Forms.Label) tiene una propiedad `TextType`, que determina si la instancia de `Label` debe mostrar texto sin formato o texto HTML. Esta propiedad debe establecerse en uno de los miembros de la enumeración `TextType`:

- `Text` indica que el `Label` mostrará el texto sin formato y es el valor predeterminado de la propiedad `Label.TextType`.
- `Html` indica que el `Label` mostrará el texto HTML.

Por lo tanto, las instancias de [`Label`](xref:Xamarin.Forms.Label) pueden mostrar HTML estableciendo la propiedad `Label.TextType` en `Html` y la propiedad `Label.Text` en una cadena HTML:

```csharp
Label label = new Label
{
    Text = "This is <strong style=\"color:red\">HTML</strong> text.",
    TextType = TextType.Html
};
```

En el ejemplo anterior, los caracteres de comillas dobles del código HTML deben usarse con el símbolo de `\`.

En XAML, las cadenas HTML se pueden volver ilegibles debido a la escape adicional de los símbolos `<` y `>`:

```xaml
<Label Text="This is &lt;strong style=&quot;color:red&quot;&gt;HTML&lt;/strong&gt; text."
       TextType="Html"  />
```

Como alternativa, para mayor legibilidad, el código HTML se puede insertar en una `CDATA` sección:

```xaml
<Label TextType="Html">
    <x:String>
        <![CDATA[
        This is <strong style="color:red">HTML</strong> text.
        ]]>
    </x:String>
</Label>
```

En este ejemplo, la propiedad `Label.Text` se establece en la cadena HTML que se inserta en la sección `CDATA`. Esto funciona porque la propiedad `Text` es el `ContentProperty` de la clase `Label`.

Las capturas de pantallas siguientes muestran un [`Label`](xref:Xamarin.Forms.Label) mostrar HTML:

![Capturas de pantallas de una etiqueta que muestra HTML, en iOS y Android](label-images/label-html.png)

> [!IMPORTANT]
> La visualización de HTML en un [`Label`](xref:Xamarin.Forms.Label) se limita a las etiquetas HTML admitidas por la plataforma subyacente.

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Texto con formato

Las etiquetas exponen una propiedad [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) que permite la presentación de texto con varias fuentes y colores en la misma vista.

La propiedad `FormattedText` es de tipo [`FormattedString`](xref:Xamarin.Forms.FormattedString), que consta de una o varias instancias de [`Span`](xref:Xamarin.Forms.Span) , establecidas a través de la propiedad [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) . Se pueden usar las siguientes propiedades de `Span` para establecer la apariencia visual:

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) : el color del fondo del intervalo.
- `CharacterSpacing`, de tipo `double`, es el espaciado entre los caracteres del texto `Span`.
- [`Font`](xref:Xamarin.Forms.Span.Font) : la fuente del texto del intervalo.
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes) : los atributos de fuente para el texto en el intervalo.
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily) : la familia de fuentes a la que pertenece la fuente del texto del intervalo.
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize) : tamaño de la fuente del texto del intervalo.
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor) : el color del texto del intervalo. Esta propiedad está obsoleta y se ha reemplazado por la propiedad `TextColor`.
- [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) : el multiplicador que se aplica al alto de línea predeterminado del intervalo. Para obtener más información, consulte [alto de línea](#line-height).
- [`Style`](xref:Xamarin.Forms.Span.Style) : el estilo que se va a aplicar al intervalo.
- [`Text`](xref:Xamarin.Forms.Span.Text) : el texto del intervalo.
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor) : el color del texto del intervalo.
- `TextDecorations`: las decoraciones que se aplican al texto en el intervalo. Para obtener más información, vea [decoraciones de texto](#text-decorations).

Las propiedades enlazables [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor), [`Text`](xref:Xamarin.Forms.Span.Text)y [`Text`](xref:Xamarin.Forms.Span.Text) tienen un modo de enlace predeterminado de [`OneWay`](xref:Xamarin.Forms.BindingMode). Para obtener más información acerca de este modo de enlace, vea [el modo de enlace predeterminado](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#the-default-binding-mode) en la guía del [modo de enlace](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md) .

Además, la propiedad [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) se puede utilizar para definir una colección de reconocedores de gestos que responderán a los gestos en el [`Span`](xref:Xamarin.Forms.Span).

> [!NOTE]
> No es posible mostrar HTML en un [`Span`](xref:Xamarin.Forms.Span).

En el siguiente ejemplo de XAML se muestra una propiedad `FormattedText` que consta de tres instancias [`Span`](xref:Xamarin.Forms.Span) :

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
> La propiedad [`Text`](xref:Xamarin.Forms.Span.Text) de una `Span` se puede establecer mediante el enlace de datos. Para obtener más información, vea [Enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Tenga en cuenta que un [`Span`](xref:Xamarin.Forms.Span) también puede responder a cualquier movimiento que se agregue a la colección de [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) del intervalo. Por ejemplo, se ha agregado un [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) al segundo `Span` en los ejemplos de código anteriores. Por lo tanto, cuando se puntea este `Span`, el `TapGestureRecognizer` responderá ejecutando el `ICommand` definido por la propiedad [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) . Para obtener más información sobre los reconocedores de gestos, consulte [gestos de Xamarin. Forms](~/xamarin-forms/app-fundamentals/gestures/index.md).

Las siguientes capturas de pantallas muestran el resultado de establecer la propiedad `FormattedString` en tres instancias `Span`:

![Ejemplo de etiqueta de FormattedText](label-images/formattedtext.png)

## <a name="line-height"></a>Alto de línea

El alto vertical de un [`Label`](xref:Xamarin.Forms.Label) y un [`Span`](xref:Xamarin.Forms.Span) pueden personalizarse estableciendo la propiedad de [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) o [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) en un valor de `double`. En iOS y Android, estos valores son multiplicadores del alto de línea original y en el Plataforma universal de Windows (UWP) el valor de la propiedad `Label.LineHeight` es un multiplicador del tamaño de fuente de la etiqueta.

> [!NOTE]
>
> - En iOS, las propiedades [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) y [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) cambian el alto de línea del texto que cabe en una sola línea y el texto que se ajusta en varias líneas.
> - En Android, las propiedades [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) y [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) solo cambian el alto de línea del texto que se ajusta en varias líneas.
> - En UWP, la propiedad [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) cambia el alto de línea del texto que se ajusta en varias líneas, y la propiedad [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) no tiene ningún efecto.

En el siguiente ejemplo de XAML se muestra cómo establecer la propiedad [`LineHeight`](xref:Xamarin.Forms.Label.LineHeight) en un [`Label`](xref:Xamarin.Forms.Label):

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

Las siguientes capturas de pantallas muestran el resultado de establecer la propiedad [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) en 1,8:

![Ejemplo de LineHeight de etiqueta](label-images/label-lineheight.png)

En el siguiente ejemplo de XAML se muestra cómo establecer la propiedad [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) en un [`Span`](xref:Xamarin.Forms.Span):

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

Las siguientes capturas de pantallas muestran el resultado de establecer la propiedad [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) en 1,8:

![Ejemplo de LineHeight de intervalo](label-images/span-lineheight.png)

## <a name="hyperlinks"></a>Hipervínculos

El texto que se muestra en las instancias de [`Label`](xref:Xamarin.Forms.Label) y [`Span`](xref:Xamarin.Forms.Span) se puede convertir en hipervínculos con el siguiente enfoque:

1. Establezca las propiedades `TextColor` y `TextDecoration` de la [`Label`](xref:Xamarin.Forms.Label) o [`Span`](xref:Xamarin.Forms.Span).
1. Agregue un [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) a la colección [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) de la [`Label`](xref:Xamarin.Forms.Label) o [`Span`](xref:Xamarin.Forms.Span), cuya propiedad [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) enlaza a un 0 y cuya propiedad [2](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) contiene la dirección URL que se va a abrir.
1. Defina el `ICommand` que se ejecutará en el [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer).
1. Escriba el código que se ejecutará en el `ICommand`.

En el ejemplo de código siguiente, tomado del ejemplo de [demostraciones de hipervínculo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) , se muestra un [`Label`](xref:Xamarin.Forms.Label) cuyo contenido se establece a partir de varias instancias de [`Span`](xref:Xamarin.Forms.Span) :

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

En este ejemplo, el primer y el tercer [`Span`](xref:Xamarin.Forms.Span) instancias de incluyen texto, mientras que el segundo `Span` representa un hipervínculo tappable. Su color de texto se establece en azul y tiene una decoración de texto de subrayado. Esto crea la apariencia de un hipervínculo, tal como se muestra en las siguientes capturas de pantallas:

[![Hipervínculos](label-images/hyperlinks-small.png "Hipervínculos")](label-images/hyperlinks-large.png#lightbox)

Cuando se puntea el hipervínculo, el [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) responderá ejecutando el `ICommand` definido por su propiedad [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) . Además, la dirección URL especificada por la propiedad [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) se pasará al `ICommand` como un parámetro.

El código subyacente de la página XAML contiene la implementación de `TapCommand`:

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

El `TapCommand` ejecuta el método `Launcher.OpenAsync`, pasando el valor de la propiedad [`TapGestureRecognizer.CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) como parámetro. Xamarin. Essentials proporciona el método `Launcher.OpenAsync` y abre la dirección URL en un explorador Web. Por lo tanto, el efecto general es que cuando se puntea el hipervínculo en la página, aparece un explorador Web y se navega a la dirección URL asociada al hipervínculo.

### <a name="creating-a-reusable-hyperlink-class"></a>Crear una clase de hipervínculo reutilizable

El enfoque anterior para crear un hipervínculo requiere la escritura de código repetitivo cada vez que se requiere un hipervínculo en la aplicación. Sin embargo, se pueden crear subclases de las clases [`Label`](xref:Xamarin.Forms.Label) y [`Span`](xref:Xamarin.Forms.Span) para crear `HyperlinkLabel` y `HyperlinkSpan` clases, con el reconocedor de gestos y el código de formato de texto agregado allí.

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

La clase `HyperlinkSpan` define una propiedad `Url` y [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)asociados, y el constructor establece la apariencia del hipervínculo y el [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) que responderá cuando se puntee en el hipervínculo. Cuando se puntea un `HyperlinkSpan`, el `TapGestureRecognizer` responderá ejecutando el método `Launcher.OpenAsync` para abrir la dirección URL, especificada por la propiedad `Url`, en un explorador Web.

La clase `HyperlinkSpan` se puede consumir agregando una instancia de la clase a XAML:

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

En las secciones anteriores se describe la configuración de las propiedades [`Label`](xref:Xamarin.Forms.Label) y [`Span`](xref:Xamarin.Forms.Span) en cada instancia. Sin embargo, los conjuntos de propiedades se pueden agrupar en un estilo que se aplica de forma coherente a una o varias vistas. Esto puede aumentar la legibilidad del código y facilitar la implementación de los cambios de diseño. Para obtener más información, vea [estilos](~/xamarin-forms/user-interface/text/styles.md).

## <a name="related-links"></a>Vínculos relacionados

- [Texto (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Hipervínculos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks)
- [Crear Mobile Apps con Xamarin. Forms, capítulo 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [API de las etiquetas](xref:Xamarin.Forms.Label)
- [API de span](xref:Xamarin.Forms.Span)
