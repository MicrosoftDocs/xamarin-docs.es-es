---
title: 'Parte 2: Sintaxis XAML esencial'
description: En este artículo se explica las características de sintaxis XAML esencial de los elementos de propiedad y las propiedades adjuntas.
ms.prod: xamarin
ms.assetid: 4022F1DC-3802-4635-A553-688ABD3F0D5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: f79a07a04eddeea1441f7938fdef210a37fb920a
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79306576"
---
# <a name="part-2-essential-xaml-syntax"></a>Parte 2: Sintaxis XAML esencial

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_XAML está diseñado principalmente para crear instancias e inicializar objetos. Pero a menudo, las propiedades se deben establecer en objetos complejos que no se pueden representar fácilmente como cadenas XML y, en ocasiones, las propiedades definidas por una clase deben establecerse en una clase secundaria. Estas dos necesidades requieren las características esenciales de la sintaxis XAML de los elementos de propiedad y las propiedades adjuntas._

## <a name="property-elements"></a>Elementos de propiedad

En XAML, las propiedades de las clases normalmente se establecen como atributos XML:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large"
       TextColor="Aqua" />
```

Sin embargo, hay una manera alternativa de establecer una propiedad en XAML. Para probar esta alternativa con `TextColor`, elimine primero el valor de `TextColor` existente:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large" />
```

Abra el elemento vacío `Label` etiqueta separándolos en las etiquetas inicial y final:

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

Estas dos maneras de especificar la propiedad `TextColor` son funcionalmente equivalentes, pero no usan las dos maneras para la misma propiedad, ya que de este modo se establecería la propiedad dos veces y podría ser ambigua.

Con esta nueva sintaxis, se puede introducir cierta terminología útil:

- `Label` es un *elemento de objeto*. Es un objeto Xamarin.Forms expresado como un elemento XML.
- `Text`, `VerticalOptions`, `FontAttributes` y `FontSize` son *atributos de propiedad*. Son propiedades de Xamarin.Forms expresadas como atributos XML.
- En ese fragmento de código final, `TextColor` se ha convertido en un *elemento de propiedad*. Es una propiedad de Xamarin.Forms, pero ahora es un elemento XML.

En primer lugar parecer la definición de propiedad, es posible que los elementos en sean una infracción de la sintaxis XML, pero no lo es. El período no tiene ningún significado especial en XML. En un descodificador XML, `Label.TextColor` es simplemente un elemento secundario normal.

En XAML, sin embargo, esta sintaxis es muy especial. Una de las reglas para los elementos de propiedad es que nada más puede aparecer en la etiqueta `Label.TextColor`. El valor de la propiedad siempre se define como contenido entre el elemento de propiedad etiquetas inicial y final.

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

Sin embargo, la sintaxis de elemento de propiedad es de importancia fundamental cuando el valor de una propiedad es demasiado complejo para expresarse como una cadena simple. Dentro de las etiquetas de elemento de propiedad puede crear una instancia de otro objeto y establecer sus propiedades. Por ejemplo, puede establecer explícitamente una propiedad como `VerticalOptions` en un valor `LayoutOptions` con la configuración de la propiedad:

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

Otro ejemplo: el `Grid` tiene dos propiedades denominadas `RowDefinitions` y `ColumnDefinitions`. Estas dos propiedades son del tipo `RowDefinitionCollection` y `ColumnDefinitionCollection`, que son colecciones de objetos `RowDefinition` y `ColumnDefinition`. Deberá utilizar la sintaxis de elemento de propiedad para establecer estas colecciones.

Aquí se muestra el principio del archivo XAML para una clase `GridDemoPage`, que muestra las etiquetas del elemento de propiedad para las colecciones `RowDefinitions` y `ColumnDefinitions`:

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

Acaba de observar que el `Grid` requiere que los elementos de propiedad para las colecciones `RowDefinitions` y `ColumnDefinitions` definan las filas y columnas. Sin embargo, también debe haber alguna manera para que el programador indique la fila y la columna donde reside cada elemento secundario del `Grid`.

Dentro de la etiqueta para cada elemento secundario del `Grid` especifique la fila y la columna de ese elemento secundario mediante los siguientes atributos:

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

Los valores de `Grid.Row` y `Grid.Column` de 0 no son necesarios, pero generalmente se incluyen para fines de claridad.

Este es su aspecto:

[![diseño de cuadrícula](essential-xaml-syntax-images/griddemo.png)](essential-xaml-syntax-images/griddemo-large.png#lightbox)

La valoración exclusiva de la sintaxis de estos atributos `Grid.Row`, `Grid.Column`, `Grid.RowSpan`y `Grid.ColumnSpan` parece ser campos estáticos o propiedades de `Grid`, pero lo que es interesante, `Grid` no define nada con nombre `Row`, `Column`, `RowSpan`o `ColumnSpan`.

En su lugar, `Grid` define cuatro propiedades enlazables denominadas `RowProperty`, `ColumnProperty`, `RowSpanProperty`y `ColumnSpanProperty`. Se trata de tipos especiales de propiedades enlazables conocidas como *propiedades adjuntas*. Se definen mediante la clase `Grid` pero se establecen en los elementos secundarios de la `Grid`.

Cuando desea usar estas propiedades adjuntas en el código, la clase `Grid` proporciona métodos estáticos denominados `SetRow`, `GetColumn`, etc. Pero en XAML, estas propiedades adjuntas se establecen como atributos en los elementos secundarios del `Grid` mediante nombres de propiedades simples.

Las propiedades adjuntas siempre son reconocibles en archivos XAML como atributos que contiene una clase y un nombre de propiedad separados por un punto. Se denominan *propiedades adjuntas* porque están definidas por una clase (en este caso, `Grid`) pero se asocian a otros objetos (en este caso, los elementos secundarios del `Grid`). Durante el diseño, el `Grid` puede interrogar los valores de estas propiedades adjuntas para saber dónde colocar cada elemento secundario.

La clase `AbsoluteLayout` define dos propiedades adjuntas denominadas `LayoutBounds` y `LayoutFlags`. Este es un patrón de tablero de ajedrez realizado mediante las características de posicionamiento y ajuste de tamaño proporcional de `AbsoluteLayout`:

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

[![diseño absoluto](essential-xaml-syntax-images/absolutedemo-large.png)](essential-xaml-syntax-images/absolutedemo-large.png#lightbox)

Para algo parecido a esto, podría preguntarse la conveniencia de usar de XAML. En realidad, la repetición y la regularidad del rectángulo `LayoutBounds` sugiere que podría ser mejor en el código.

Sin duda es una preocupación legítima y no hay ningún problema con el uso de código y marcado de equilibrio al definir las interfaces de usuario. Es fácil definir algunos de los objetos visuales en XAML y, a continuación, utilice el constructor del archivo de código subyacente para agregar algunos elementos visuales más que podrían generar mejor en bucles.

## <a name="content-properties"></a>Propiedades de contenido

En los ejemplos anteriores, los objetos `StackLayout`, `Grid`y `AbsoluteLayout` se establecen en la propiedad `Content` del `ContentPage`y los elementos secundarios de estos diseños son realmente elementos de la colección `Children`. Sin embargo, estas propiedades `Content` y `Children` no se encuentran en el archivo XAML.

En realidad, puede incluir las propiedades `Content` y `Children` como elementos de propiedad, como en el ejemplo **XamlPlusCode** :

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

La pregunta real es: ¿por qué estos elementos de propiedad *no* son necesarios en el archivo XAML?

Los elementos definidos en Xamarin. Forms para su uso en XAML pueden tener una propiedad marcada en el `ContentProperty` atributo en la clase. Si busca la clase `ContentPage` en la documentación en línea de Xamarin. Forms, verá este atributo:

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

Esto significa que no es necesario el `Content` etiquetas de elemento de propiedad. Se supone que el contenido XML que aparece entre las etiquetas de inicio y finalización `ContentPage` se asignará a la propiedad `Content`.

 `StackLayout`, `Grid`, `AbsoluteLayout`y `RelativeLayout` derivan de `Layout<View>`y, si busca `Layout<T>` en la documentación de Xamarin. Forms, verá otro atributo de `ContentProperty`:

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

Que permite agregar automáticamente el contenido del diseño a la colección de `Children` sin etiquetas de elemento de propiedad `Children` explícitas.

Otras clases también tienen `ContentProperty` definiciones de atributo. Por ejemplo, la propiedad content de `Label` se `Text`. Consulte la documentación de API para que otros usuarios.

## <a name="platform-differences-with-onplatform"></a>Diferencias entre las plataformas con OnPlatform

En las aplicaciones de una sola página, es habitual establecer la propiedad `Padding` en la página para evitar sobrescribir la barra de estado de iOS. En el código, puede usar la propiedad `Device.RuntimePlatform` para este propósito:

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

También puede hacer algo similar en XAML mediante las clases [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) y [`On`](xref:Xamarin.Forms.On) . En primer lugar, incluya los elementos de propiedad para la propiedad `Padding` situada cerca de la parte superior de la página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

Dentro de estas etiquetas, incluya una etiqueta `OnPlatform`. `OnPlatform` es una clase genérica. Debe especificar el argumento de tipo genérico, en este caso `Thickness`, que es el tipo de propiedad `Padding`. Afortunadamente, hay un atributo XAML específicamente para definir argumentos genéricos denominados `x:TypeArguments`. Debe coincidir con el tipo de la propiedad que está configurando:

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

`OnPlatform` tiene una propiedad denominada `Platforms` que es un `IList` de objetos `On`. Use las etiquetas de elemento de propiedad para la propiedad:

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

Ahora, agregue `On` elementos. Para cada uno de ellos, establezca la propiedad `Platform` y la propiedad `Value` en el marcado para la propiedad `Thickness`:

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

Se puede simplificar este marcado. La propiedad de contenido de `OnPlatform` es `Platforms`, por lo que se pueden quitar esas etiquetas de elemento de propiedad:

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

La propiedad `Platform` de `On` es de tipo `IList<string>`, por lo que puede incluir varias plataformas si los valores son los mismos:

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

Como Android y UWP se establecen en el valor predeterminado de `Padding`, se puede quitar esa etiqueta:

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

Esta es la manera estándar de establecer una propiedad de `Padding` dependiente de la plataforma en XAML. Si el valor de `Value` no se puede representar mediante una sola cadena, puede definir elementos de propiedad para él:

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
> La extensión de marcado de `OnPlatform` también se puede usar en XAML para personalizar la apariencia de la interfaz de usuario en cada plataforma. Proporciona la misma funcionalidad que las clases `OnPlatform` y `On`, pero con una representación más concisa. Para obtener más información, consulte [extensión de marcado](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform)en la plataforma.

## <a name="summary"></a>Resumen

Con los elementos de propiedad y las propiedades adjuntas, gran parte de la sintaxis XAML básica se ha establecido. Sin embargo, a veces, deberá establecer propiedades a los objetos de una manera indirecta, por ejemplo, en un diccionario de recursos. Este enfoque se trata en la siguiente parte, parte [3. Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md).

## <a name="related-links"></a>Vínculos relacionados

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 1. Introducción con XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 3. Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Conceptos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Del enlace de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
