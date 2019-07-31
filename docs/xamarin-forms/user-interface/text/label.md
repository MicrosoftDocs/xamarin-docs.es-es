---
title: Etiqueta de Xamarin.Forms
description: En este artículo se explica cómo usar la clase de etiqueta de Xamarin.Forms para mostrar único y varias línea de texto en aplicaciones.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
ms.openlocfilehash: 5c12b5a5c0e558403362bd12baddfb8201f40759
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642922"
---
# <a name="xamarinforms-label"></a>Etiqueta de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Mostrar texto en Xamarin.Forms_

El [ `Label` ](xref:Xamarin.Forms.Label) vista se utiliza para mostrar texto, único y varias líneas. Las etiquetas pueden tener decoraciones de texto, texto de color y usar las fuentes personalizadas (familias de tamaños y opciones).

## <a name="text-decorations"></a>Decoraciones de texto

Se pueden aplicar las decoraciones de texto subrayado y tachado a [ `Label` ](xref:Xamarin.Forms.Label) instancias estableciendo el `Label.TextDecorations` propiedad a uno o varios `TextDecorations` miembros de enumeración:

- `None`
- `Underline`
- `Strikethrough`

En el siguiente ejemplo XAML se muestra cómo establecer el `Label.TextDecorations` propiedad:

```xaml
<Label Text="This is underlined text." TextDecorations="Underline"  />
<Label Text="This is text with strikethrough." TextDecorations="Strikethrough" />
<Label Text="This is underlined text with strikethrough." TextDecorations="Underline, Strikethrough" />
```

El código de C# equivalente es:

```csharp
var underlineLabel = new Label { Text = "This is underlined text.", TextDecorations = TextDecorations.Underline };
var strikethroughLabel = new Label { Text = "This is text with strikethrough.", TextDecorations = TextDecorations.Strikethrough };
var bothLabel = new Label { Text = "This is underlined text with strikethrough.", TextDecorations = TextDecorations.Underline | TextDecorations.Strikethrough };
```

Capturas de pantalla siguientes se muestra el `TextDecorations` aplicados a miembros de la enumeración [ `Label` ](xref:Xamarin.Forms.Label) instancias:

![](label-images/label-textdecorations.png "Etiquetas de decoración de texto")

> [!NOTE]
> También se pueden aplicar las decoraciones de texto a [ `Span` ](xref:Xamarin.Forms.Span) instancias. Para obtener más información sobre la `Span` de clases, vea [texto con formato](#Formatted_Text).

## <a name="colors"></a>Colores

Las etiquetas se pueden establecer para usar un color de texto personalizado a través de la enlazable [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor) propiedad.

Atención especial es necesaria para asegurarse de que los colores se podrán usar en cada plataforma. Dado que cada plataforma tiene distintos valores predeterminados para los colores de texto y fondo, deberá tener cuidado al elegir un valor predeterminado que funciona en cada uno.

En el siguiente ejemplo XAML establece el color del texto de un `Label`:

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

El código de C# equivalente es:

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

Las capturas de pantalla siguientes muestran el resultado de establecer el `TextColor` propiedad:

![](label-images/textcolor.png "Ejemplo de etiqueta TextColor")

Para obtener más información acerca de los colores, consulte [colores](~/xamarin-forms/user-interface/colors.md).

## <a name="fonts"></a>Fuentes

Para obtener más información acerca de cómo especificar las fuentes en un `Label`, consulte [fuentes](~/xamarin-forms/user-interface/text/fonts.md).

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Truncamiento y ajuste

Se pueden establecer etiquetas para controlar el texto que no cabe en una sola línea en una de varias maneras, expuestos por la `LineBreakMode` propiedad. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) es una enumeración con los valores siguientes:

- **HeadTruncation** &ndash; trunca el encabezado del texto, que muestra el extremo.
- **CharacterWrap** &ndash; ajusta el texto en una nueva línea en un límite de caracteres.
- **MiddleTruncation** &ndash; muestra el principio y al final del texto, con el reemplazo intermedio mediante puntos suspensivos.
- **NoWrap** &ndash; no ajusta el texto, mostrar solo mayor cantidad de texto que puede cabe en una sola línea.
- **TailTruncation** &ndash; muestra el principio del texto, truncar final.
- **WordWrap** &ndash; ajusta el texto en el límite de palabras.

## <a name="displaying-a-specific-number-of-lines"></a>Mostrar un número específico de líneas

El número de líneas muestra un [ `Label` ](xref:Xamarin.Forms.Label) se puede especificar estableciendo el `Label.MaxLines` propiedad a un `int` valor:

- Cuando `MaxLines` es 0, el `Label` respeta el valor de la [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) propiedad que se va a mostrar ya sea una sola línea, posiblemente truncada, o todas las líneas con todo el texto.
- Cuando `MaxLines` es 1, el resultado es idéntico al valor de la [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) propiedad [ `NoWrap` ](xref:Xamarin.Forms.LineBreakMode), [ `HeadTruncation` ](xref:Xamarin.Forms.LineBreakMode), [ `MiddleTruncation` ](xref:Xamarin.Forms.LineBreakMode), o [ `TailTruncation` ](xref:Xamarin.Forms.LineBreakMode). Sin embargo, el `Label` respetará el valor de la [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) propiedad con respecto a la selección de ubicación de puntos suspensivos, si procede.
- Cuando `MaxLines` es mayor que 1, el `Label` mostrará hasta el número especificado de líneas, respetando el valor de la [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) propiedad con respecto a la selección de ubicación de puntos suspensivos, si procede. Sin embargo, establecer el `MaxLines` propiedad a un valor mayor que 1 no tiene ningún efecto si la [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) propiedad está establecida en [ `NoWrap` ](xref:Xamarin.Forms.LineBreakMode).

En el siguiente ejemplo XAML se muestra cómo establecer el `MaxLines` propiedad en un [ `Label` ](xref:Xamarin.Forms.Label):

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       MaxLines="2" />
```

El código de C# equivalente es:

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  MaxLines = 2
};
```

Las capturas de pantalla siguientes muestran el resultado de establecer el `MaxLines` propiedad en 2, cuando el texto es lo suficientemente largo para ocupar más de 2 líneas:

![](label-images/label-maxlines.png "Ejemplo de etiqueta MaxLines")

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Texto con formato

Las etiquetas exponen una [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) propiedad que permite la presentación de texto con varias fuentes y colores en la misma vista.

El `FormattedText` propiedad es de tipo [ `FormattedString` ](xref:Xamarin.Forms.FormattedString), que consta de uno o varios [ `Span` ](xref:Xamarin.Forms.Span) instancias, se establece a través de la [ `Spans` ](xref:Xamarin.Forms.FormattedString.Spans) propiedad . La siguiente `Span` propiedades pueden usarse para establecer la apariencia visual:

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) : el color del fondo del intervalo.
- [`Font`](xref:Xamarin.Forms.Span.Font) : la fuente para el texto del intervalo.
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes) : los atributos de fuente para el texto del intervalo.
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily) – la familia de fuentes a la que pertenece la fuente para el texto del intervalo.
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize) – el tamaño de la fuente para el texto del intervalo.
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor) : el color del texto en el intervalo. Esta propiedad está obsoleta y se ha reemplazado por el `TextColor` propiedad.
- [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) -el multiplicador que se aplican en el alto de línea predeterminado del intervalo. Para obtener más información, consulte [alto de línea](#line-height).
- [`Style`](xref:Xamarin.Forms.Span.Style): el estilo que se va a aplicar al intervalo.
- [`Text`](xref:Xamarin.Forms.Span.Text) : el texto del intervalo.
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor) : el color del texto en el intervalo.
- `TextDecorations` -las decoraciones para aplicar al texto en el intervalo. Para obtener más información, consulte [decoraciones de texto](#text-decorations).

> [!NOTE]
> Las [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor)propiedades [`Text`](xref:Xamarin.Forms.Span.Text), [`OneWay`](xref:Xamarin.Forms.BindingMode)y [`Text`](xref:Xamarin.Forms.Span.Text) enlazables tienen un modo de enlace predeterminado de. Para obtener más información acerca de este modo de enlace, vea [el modo de enlace predeterminado](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#the-default-binding-mode) en la guía del [modo de enlace](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md) .

Además, el [ `GestureRecognizers` ](xref:Xamarin.Forms.GestureElement.GestureRecognizers) propiedad puede usarse para definir una colección de los reconocedores de gestos que responderá a los movimientos en el [ `Span` ](xref:Xamarin.Forms.Span).

En el ejemplo de XAML siguiente se muestra un `FormattedText` propiedad que consta de tres [ `Span` ](xref:Xamarin.Forms.Span) instancias:

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

El código de C# equivalente es:

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
> El [ `Text` ](xref:Xamarin.Forms.Span.Text) propiedad de un `Span` puede establecerse mediante enlace de datos. Para obtener más información, consulte [enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Tenga en cuenta que un [ `Span` ](xref:Xamarin.Forms.Span) también puede responder a los gestos que se agregan en el intervalo [ `GestureRecognizers` ](xref:Xamarin.Forms.GestureElement.GestureRecognizers) colección. Por ejemplo, un [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) se ha agregado a la segunda `Span` en los ejemplos de código anteriores. Por lo tanto, cuando esto `Span` se pulsa la `TapGestureRecognizer` responderá mediante la ejecución de la `ICommand` definido por el [ `Command` ](xref:Xamarin.Forms.TapGestureRecognizer.Command) propiedad. Para obtener más información acerca de los reconocedores de gestos, vea [Xamarin.Forms gestos](~/xamarin-forms/app-fundamentals/gestures/index.md).

Las capturas de pantalla siguientes muestran el resultado de la configuración de la `FormattedString` propiedad a tres `Span` instancias:

![](label-images/formattedtext.png "Ejemplo de etiqueta FormattedText")

## <a name="line-height"></a>Alto de línea

La altura vertical de un [ `Label` ](xref:Xamarin.Forms.Label) y un [ `Span` ](xref:Xamarin.Forms.Span) puede personalizarse configurando el [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) propiedad o [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) a un `double` valor. En iOS y Android, estos valores son multiplicadores del alto de línea original y en la plataforma Universal de Windows (UWP) la `Label.LineHeight` valor de propiedad es un multiplicador del tamaño de fuente de etiqueta.

> [!NOTE]
> - En iOS, el [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) y [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) propiedades cambian el alto de línea de texto que encaja en una sola línea y el texto que se ajusta en varias líneas.
> - En Android, el [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) y [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) propiedades sólo cambian el alto de línea de texto que se ajusta en varias líneas.
> - En UWP, la [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) propiedad cambia el alto de línea de texto que se ajusta en varias líneas, y el [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) propiedad no tiene ningún efecto.

En el siguiente ejemplo XAML se muestra cómo establecer el [ `LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) propiedad en un [ `Label` ](xref:Xamarin.Forms.Label):

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       LineHeight="1.8" />
```

El código de C# equivalente es:

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  LineHeight = 1.8
};
```

Las capturas de pantalla siguientes muestran el resultado de la configuración de la [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) propiedad a 1.8:

![](label-images/label-lineheight.png "Ejemplo de etiqueta LineHeight")

En el siguiente ejemplo XAML se muestra cómo establecer el [ `LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) propiedad en un [ `Span` ](xref:Xamarin.Forms.Span):

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

El código de C# equivalente es:

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

Las capturas de pantalla siguientes muestran el resultado de la configuración de la [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) propiedad a 1.8:

![](label-images/span-lineheight.png "Ejemplo de span LineHeight")

## <a name="hyperlinks"></a>Hipervínculos

El texto que [`Label`](xref:Xamarin.Forms.Label) se muestra [`Span`](xref:Xamarin.Forms.Span) en las instancias de y se puede convertir en hipervínculos con el siguiente enfoque:

1. Establezca las `TextColor` propiedades `TextDecoration` y de [`Label`](xref:Xamarin.Forms.Label) o. [`Span`](xref:Xamarin.Forms.Span)
1. Agregue un [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) a la [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) colección de [`Label`](xref:Xamarin.Forms.Label) o [`Span`](xref:Xamarin.Forms.Span), cuya [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) propiedad se enlaza a un `ICommand`, y cuya [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) propiedad contiene la dirección URL que se va a abrir.
1. Defina el `ICommand` que va [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)a ejecutar.
1. Escriba el código que va a `ICommand`ejecutar.

En el ejemplo de código siguiente, tomado del ejemplo de [demostraciones](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) de hipervínculo [`Label`](xref:Xamarin.Forms.Label) , se muestra un cuyo contenido [`Span`](xref:Xamarin.Forms.Span) se establece a partir de varias instancias:

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

[![] Hipervínculos (label-images/hyperlinks-small.png "") Hipervínculos](label-images/hyperlinks-large.png#lightbox)

Cuando se puntea el hipervínculo, [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) responderá ejecutando el `ICommand` definido por su [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) propiedad. Además, la dirección URL especificada por la [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) propiedad se pasará `ICommand` a como un parámetro.

El código subyacente de la página XAML contiene la `TapCommand` implementación:

```csharp
public partial class MainPage : ContentPage
{
    public ICommand TapCommand => new Command<string>(OpenBrowser);

    public MainPage()
    {
        InitializeComponent();
        BindingContext = this;
    }

    void OpenBrowser(string url)
    {
        Device.OpenUri(new Uri(url));
    }
}
```

Ejecuta el `OpenBrowser` método, pasando el valor de [`TapGestureRecognizer.CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) la propiedad como un parámetro. `TapCommand` A su vez, este método llama [`Device.OpenUri`](xref:Xamarin.Forms.Device.OpenUri*) al método para abrir la dirección URL en un explorador Web. Por lo tanto, el efecto general es que cuando se puntea el hipervínculo en la página, aparece un explorador Web y se navega a la dirección URL asociada al hipervínculo.

### <a name="creating-a-reusable-hyperlink-class"></a>Crear una clase de hipervínculo reutilizable

El enfoque anterior para crear un hipervínculo requiere la escritura de código repetitivo cada vez que se requiere un hipervínculo en la aplicación. Sin [`Label`](xref:Xamarin.Forms.Label) embargo, [`Span`](xref:Xamarin.Forms.Span) se pueden crear `HyperlinkLabel` `HyperlinkSpan` subclases para las clases y, con el reconocedor de gestos y el código de formato de texto agregado allí.

En el ejemplo de código siguiente, tomado del ejemplo de [demostraciones](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) de hipervínculo `HyperlinkSpan` , se muestra una clase:

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
            Command = new Command(() => Device.OpenUri(new Uri(Url)))
        });
    }
}
```

La `HyperlinkSpan` clase define una `Url` propiedad, y se [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)asocia, y el constructor establece la apariencia del hipervínculo y el [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) objeto que responderá cuando se puntee en el hipervínculo. Cuando se puntea en, el `TapGestureRecognizer` responderá ejecutando el [`Device.OpenUri`](xref:Xamarin.Forms.Device.OpenUri*) método para abrir la dirección URL, especificada por la `Url` propiedad, en un explorador Web. `HyperlinkSpan`

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

## <a name="styling-labels"></a>Etiquetas de estilo

Las secciones anteriores tratan la configuración [ `Label` ](xref:Xamarin.Forms.Label) y [ `Span` ](xref:Xamarin.Forms.Span) las propiedades de por instancia. Sin embargo, los conjuntos de propiedades se pueden agrupar en un estilo que se aplica de manera coherente a una o varias vistas. Esto puede mejorar la legibilidad del código y realizar cambios de diseño sea más fácil de implementar. Para obtener más información, consulte [estilos](~/xamarin-forms/user-interface/text/styles.md).

## <a name="related-links"></a>Vínculos relacionados

- [Texto (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Hipervínculos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks)
- [Creación de aplicaciones móviles con Xamarin.Forms, capítulo 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [API de etiqueta](xref:Xamarin.Forms.Label)
- [Intervalo de API](xref:Xamarin.Forms.Span)
