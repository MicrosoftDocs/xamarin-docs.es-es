---
title: Parte 2. Sintaxis XAML esencial
description: En este artículo se explica las características de sintaxis XAML esencial de los elementos de propiedad y las propiedades adjuntas.
ms.prod: xamarin
ms.assetid: 4022F1DC-3802-4635-A553-688ABD3F0D5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: 7526349c1b4b61495af95dfc200a5055cea5650e
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69529301"
---
# <a name="part-2-essential-xaml-syntax"></a>Parte 2. Sintaxis XAML esencial

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_XAML está diseñado principalmente para crear instancias e inicializar objetos. Pero a menudo, se deben establecer propiedades en objetos complejos que no pueden representarse fácilmente como cadenas XML y a veces se deben establecer las propiedades definidas por una clase en una clase secundaria. Estas dos necesidades requieren las características de sintaxis XAML esencial de los elementos de propiedad y las propiedades adjuntas._

## <a name="property-elements"></a>Elementos de propiedad

En XAML, las propiedades de las clases normalmente se establecen como atributos XML:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large"
       TextColor="Aqua" />
```

Sin embargo, hay una manera alternativa de establecer una propiedad en XAML. Para probar esta alternativa con `TextColor`, primero elimine existente `TextColor` configuración:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large" />
```

Abra el elemento vacío `Label` etiqueta separando en etiquetas de inicio y finalización:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">

</Label>
```

Dentro de estas etiquetas, agregue las etiquetas inicial y final que se componen de nombre de clase y un nombre de propiedad separados por un período de:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>

    </Label.TextColor>
</Label>
```

Establezca el valor de propiedad como contenido de estas nuevas etiquetas, similar al siguiente:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>
        Aqua
    </Label.TextColor>
</Label>
```

Estas dos maneras de especificar el `TextColor` propiedad son funcionalmente equivalentes, pero no use las dos maneras para la misma propiedad ya que podría ser establecer de forma efectiva la propiedad dos veces y podría ser ambiguo.

Con esta nueva sintaxis, se puede introducir cierta terminología útil:

- `Label` es un *elemento object*. Es un objeto Xamarin.Forms expresado como un elemento XML.
- `Text`, `VerticalOptions`, `FontAttributes` y `FontSize` son *atributos de la propiedad*. Son propiedades de Xamarin.Forms expresadas como atributos XML.
- En ese fragmento final, `TextColor` se ha convertido en un *elemento property*. Es una propiedad de Xamarin.Forms, pero ahora es un elemento XML.


En primer lugar parecer la definición de propiedad, es posible que los elementos en sean una infracción de la sintaxis XML, pero no lo es. El período no tiene ningún significado especial en XML. Para un descodificador de XML, `Label.TextColor` es simplemente un elemento secundario normal.

En XAML, sin embargo, esta sintaxis es muy especial. Una de las reglas de los elementos de propiedad es que nada puede aparecer en la `Label.TextColor` etiqueta. El valor de la propiedad siempre se define como contenido entre el elemento de propiedad etiquetas inicial y final.

Puede usar la sintaxis de elemento de propiedad en más de una propiedad:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center">
    <Label.FontAttributes>
        Bold
    </Label.FontAttributes>
    <Label.FontSize>
        Large
    </Label.FontSize>
    <Label.TextColor>
        Aqua
    </Label.TextColor>
</Label>
```

O bien, puede usar sintaxis de elemento de propiedad para todas las propiedades:

```xaml
<Label>
    <Label.Text>
        Hello, XAML!
    </Label.Text>
    <Label.FontAttributes>
        Bold
    </Label.FontAttributes>
    <Label.FontSize>
        Large
    </Label.FontSize>
    <Label.TextColor>
        Aqua
    </Label.TextColor>
    <Label.VerticalOptions>
        Center
    </Label.VerticalOptions>
</Label>
```

En primer lugar, sintaxis de elemento de propiedad pueden parecer un reemplazo extiende innecesario para algo comparativamente bastante simple y, en estos ejemplos que es ciertamente el caso.

Sin embargo, la sintaxis de elemento de propiedad es de importancia fundamental cuando el valor de una propiedad es demasiado complejo para expresarse como una cadena simple. Dentro de las etiquetas de elemento de propiedad puede crear una instancia de otro objeto y establecer sus propiedades. Por ejemplo, se puede establecer explícitamente una propiedad como `VerticalOptions` a un `LayoutOptions` valor con valores de propiedad:

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

Otro ejemplo: Tiene dos propiedades denominadas `RowDefinitions` y `ColumnDefinitions`. `Grid` Estas dos propiedades son de tipo `RowDefinitionCollection` y `ColumnDefinitionCollection`, que son colecciones de `RowDefinition` y `ColumnDefinition` objetos. Deberá utilizar la sintaxis de elemento de propiedad para establecer estas colecciones.

Este es el comienzo del archivo XAML para un `GridDemoPage` (clase), que muestra las etiquetas de elemento de propiedad para el `RowDefinitions` y `ColumnDefinitions` colecciones:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.GridDemoPage"
             Title="Grid Demo Page">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="100" />
        </Grid.ColumnDefinitions>
        ...
    </Grid>
</ContentPage>
```

Tenga en cuenta la sintaxis abreviada para la definición de celdas de tamaño automático, las de ancho de píxel y el alto y configuración de estrella.

## <a name="attached-properties"></a>Propiedades asociadas

Ya ha visto que la `Grid` requiere que los elementos de propiedad para el `RowDefinitions` y `ColumnDefinitions` recopilaciones para definir las filas y columnas. Sin embargo, también debe haber alguna manera para el programador indicar la fila y columna donde cada miembro secundario de la `Grid` reside.

Dentro de la etiqueta para cada miembro secundario de la `Grid` especificar la fila y columna de ese elemento secundario con los siguientes atributos:

- `Grid.Row`
- `Grid.Column`

Los valores predeterminados de estos atributos son 0. También puede indicar si un elemento secundario abarca más de una fila o columna con estos atributos:

- `Grid.RowSpan`
- `Grid.ColumnSpan`

Estos dos atributos tienen valores predeterminados de 1.

Este es el archivo GridDemoPage.xaml completo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.GridDemoPage"
             Title="Grid Demo Page">

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="100" />
        </Grid.ColumnDefinitions>

        <Label Text="Autosized cell"
               Grid.Row="0" Grid.Column="0"
               TextColor="White"
               BackgroundColor="Blue" />

        <BoxView Color="Silver"
                 HeightRequest="0"
                 Grid.Row="0" Grid.Column="1" />

        <BoxView Color="Teal"
                 Grid.Row="1" Grid.Column="0" />

        <Label Text="Leftover space"
               Grid.Row="1" Grid.Column="1"
               TextColor="Purple"
               BackgroundColor="Aqua"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Span two rows (or more if you want)"
               Grid.Row="0" Grid.Column="2" Grid.RowSpan="2"
               TextColor="Yellow"
               BackgroundColor="Blue"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Span two columns"
               Grid.Row="2" Grid.Column="0" Grid.ColumnSpan="2"
               TextColor="Blue"
               BackgroundColor="Yellow"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Fixed 100x100"
               Grid.Row="2" Grid.Column="2"
               TextColor="Aqua"
               BackgroundColor="Red"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

    </Grid>
</ContentPage>
```

El `Grid.Row` y `Grid.Column` configuración 0 no son necesarias, pero se incluyen con carácter general para una mayor claridad.

Este es su aspecto:

[![](essential-xaml-syntax-images/griddemo.png "Diseño de cuadrícula")](essential-xaml-syntax-images/griddemo-large.png#lightbox "diseño de cuadrícula")

A juzgar únicamente por la sintaxis, estos `Grid.Row`, `Grid.Column`, `Grid.RowSpan`, y `Grid.ColumnSpan` atributos aparecen como campos estáticos ni propiedades de `Grid`, pero, curiosamente, `Grid` no define nada con el nombre `Row`, `Column`, `RowSpan`, o `ColumnSpan`.

En su lugar, `Grid` define cuatro propiedades enlazables denominadas `RowProperty`, `ColumnProperty`, `RowSpanProperty`, y `ColumnSpanProperty`. Estos son tipos especiales de propiedades enlazables conocidos como *propiedades adjuntas*. Que define el `Grid` clase pero establece en elementos secundarios de la `Grid`.

Cuando se desea usarlas las propiedades adjuntas de código, el `Grid` clase proporciona métodos estáticos denominados `SetRow`, `GetColumn`, y así sucesivamente. Pero en XAML, estas propiedades adjuntas se establecen como atributos en los elementos secundarios de la `Grid` mediante nombres de propiedades simples.

Las propiedades adjuntas siempre son reconocibles en archivos XAML como atributos que contiene una clase y un nombre de propiedad separados por un punto. Se denominan *propiedades adjuntas* porque ya están definidas por una clase (en este caso, `Grid`) pero conectado a otros objetos (en este caso, los elementos secundarios de la `Grid`). Durante el diseño, el `Grid` puede consultar los valores de estas propiedades adjuntas para saber dónde colocar cada elemento secundario.

El `AbsoluteLayout` clase define dos propiedades adjuntas denominadas `LayoutBounds` y `LayoutFlags`. Este es un patrón de tablero logrado usando el posicionamiento proporcional y las características de ajuste de tamaño de `AbsoluteLayout`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.AbsoluteDemoPage"
             Title="Absolute Demo Page">

    <AbsoluteLayout BackgroundColor="#FF8080">
        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.33, 0, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="1, 0, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0, 0.33, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.67, 0.33, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.33, 0.67, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="1, 0.67, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0, 1, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.67, 1, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

  </AbsoluteLayout>
</ContentPage>
```

Y aquí es:

[![](essential-xaml-syntax-images/absolutedemo-large.png "Diseño absoluto")](essential-xaml-syntax-images/absolutedemo-large.png#lightbox "diseño absoluto")

Para algo parecido a esto, podría preguntarse la conveniencia de usar de XAML. Sin duda, la repetición y la regularidad del `LayoutBounds` rectángulo sugiere que se podría generarse mejor en código.

Sin duda es una preocupación legítima y no hay ningún problema con el uso de código y marcado de equilibrio al definir las interfaces de usuario. Es fácil definir algunos de los objetos visuales en XAML y, a continuación, utilice el constructor del archivo de código subyacente para agregar algunos elementos visuales más que podrían generar mejor en bucles.

## <a name="content-properties"></a>Propiedades de contenido

En los ejemplos anteriores, el `StackLayout`, `Grid`, y `AbsoluteLayout` objetos se establecen en el `Content` propiedad de la `ContentPage`, y los elementos secundarios de estos diseños son realmente los elementos de la `Children` colección. Aunque estos `Content` y `Children` propiedades son ningún destino en el archivo XAML.

Sin duda puede incluir el `Content` y `Children` propiedades como elementos de propiedad, como en el **XamlPlusCode** ejemplo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <ContentPage.Content>
        <StackLayout>
            <StackLayout.Children>
                <Slider VerticalOptions="CenterAndExpand"
                        ValueChanged="OnSliderValueChanged" />

                <Label x:Name="valueLabel"
                       Text="A simple Label"
                       FontSize="Large"
                       HorizontalOptions="Center"
                       VerticalOptions="CenterAndExpand" />

                <Button Text="Click Me!"
                      HorizontalOptions="Center"
                      VerticalOptions="CenterAndExpand"
                      Clicked="OnButtonClicked" />
            </StackLayout.Children>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

La pregunta real es: ¿Por qué estos elementos de propiedad *no* son necesarios en el archivo XAML?

Los elementos definidos en Xamarin.Forms para su uso en XAML pueden tener una propiedad de marca en el `ContentProperty` atributo de la clase. Si busca la `ContentPage` clase en la documentación de Xamarin.Forms en línea, verá que este atributo:

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

Esto significa que el `Content` no son necesarias las etiquetas de elemento de propiedad. Cualquier contenido XML que aparece entre el inicio y finalización `ContentPage` etiquetas se supone que se asignará a la `Content` propiedad.

 `StackLayout`, `Grid`, `AbsoluteLayout`, y `RelativeLayout` todos se derivan `Layout<View>`, y si busca `Layout<T>` en la documentación de Xamarin.Forms, verá otro `ContentProperty` atributo:

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

Que permite que el contenido del diseño se agregan automáticamente a la `Children` colección sin explícita `Children` etiquetas de elemento de propiedad.

También tienen otras clases `ContentProperty` definiciones de atributo. Por ejemplo, la propiedad content de `Label` es `Text`. Consulte la documentación de API para que otros usuarios.

## <a name="platform-differences-with-onplatform"></a>Diferencias entre las plataformas con OnPlatform

En las aplicaciones de página única, es común para establecer el `Padding` propiedad en la página para evitar sobrescribir la barra de estado de iOS. En el código, puede usar el `Device.RuntimePlatform` propiedad para este propósito:

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

También puede hacer algo similar en XAML mediante las [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) clases y. [`On`](xref:Xamarin.Forms.On) En primer lugar, incluya los elementos de propiedad para el `Padding` propiedad cerca de la parte superior de la página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

Dentro de estas etiquetas, incluyen una `OnPlatform` etiqueta. `OnPlatform` es una clase genérica. Debe especificar el argumento de tipo genérico, en este caso, `Thickness`, que es el tipo de `Padding` propiedad. Afortunadamente, hay un atributo XAML específicamente para definir argumentos genéricos llama `x:TypeArguments`. Debe coincidir con el tipo de la propiedad que está configurando:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">

        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

`OnPlatform` tiene una propiedad denominada `Platforms` que es un `IList` de `On` objetos. Use las etiquetas de elemento de propiedad para la propiedad:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <OnPlatform.Platforms>

            </OnPlatform.Platforms>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Ahora agregue `On` elementos. Para cada uno de ellos, establezca el `Platform` propiedad y el `Value` propiedad al código de marcado para el `Thickness` propiedad:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <OnPlatform.Platforms>
                <On Platform="iOS" Value="0, 20, 0, 0" />
                <On Platform="Android" Value="0, 0, 0, 0" />
                <On Platform="UWP" Value="0, 0, 0, 0" />
            </OnPlatform.Platforms>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Se puede simplificar este marcado. La propiedad content de `OnPlatform` es `Platforms`, por lo que se pueden quitar esas etiquetas de elemento de propiedad:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
            <On Platform="Android" Value="0, 0, 0, 0" />
            <On Platform="UWP" Value="0, 0, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

El `Platform` propiedad de `On` es de tipo `IList<string>`, por lo que puede incluir varias plataformas, si los valores son iguales:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
            <On Platform="Android, UWP" Value="0, 0, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Dado que Android y UWP se establecen en el valor predeterminado de `Padding`, que se puede quitar la etiqueta:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Esta es la manera estándar para establecer un depende de la plataforma `Padding` propiedad en XAML. Si el `Value` configuración no puede representarse mediante una sola cadena, puede definir los elementos de propiedad para él:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS">
                <On.Value>
                    0, 20, 0, 0
                </On.Value>
            </On>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

> [!NOTE]
> La `OnPlatform` extensión de marcado también se puede usar en XAML para personalizar la apariencia de la interfaz de usuario en cada plataforma. Proporciona la misma funcionalidad que las `OnPlatform` clases y `On` , pero con una representación más concisa. Para obtener más información, consulte [OnPlatform Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform).

## <a name="summary"></a>Resumen

Con los elementos de propiedad y las propiedades adjuntas, gran parte de la sintaxis XAML básica se ha establecido. Sin embargo, a veces, deberá establecer propiedades a los objetos de una manera indirecta, por ejemplo, en un diccionario de recursos. Este enfoque se trata en la siguiente parte, parte [3. Las extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md).

## <a name="related-links"></a>Vínculos relacionados

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 1. Introducción a XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 3. Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Conceptos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Desde el enlace de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
