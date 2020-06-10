---
Título: "parte 2. Sintaxis XAML básica "Description:" en este artículo se explican las características esenciales de la sintaxis XAML de los elementos de propiedad y las propiedades adjuntas.
MS. Prod: Xamarin ms. AssetID: 4022F1DC-3802-4635-A553-688ABD3F0D5A ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/25/2017 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="part-2-essential-xaml-syntax"></a>Parte 2. Sintaxis XAML esencial

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_XAML está diseñado principalmente para crear instancias e inicializar objetos. Pero a menudo, las propiedades se deben establecer en objetos complejos que no se pueden representar fácilmente como cadenas XML y, en ocasiones, las propiedades definidas por una clase deben establecerse en una clase secundaria. Estas dos necesidades requieren las características esenciales de la sintaxis XAML de los elementos de propiedad y las propiedades adjuntas._

## <a name="property-elements"></a>Elementos de propiedad

En XAML, las propiedades de las clases se establecen normalmente como atributos XML:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large"
       TextColor="Aqua" />
```

Sin embargo, hay una manera alternativa de establecer una propiedad en XAML. Para probar esta alternativa con `TextColor` , elimine primero la `TextColor` configuración existente:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large" />
```

Abra la etiqueta de elemento vacío `Label` separándolos en etiquetas de inicio y fin:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">

</Label>
```

Dentro de estas etiquetas, agregue las etiquetas inicial y final que consten del nombre de clase y un nombre de propiedad separados por un punto:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>

    </Label.TextColor>
</Label>
```

Establezca el valor de la propiedad como contenido de estas etiquetas nuevas, de la siguiente manera:

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

Estas dos maneras de especificar la `TextColor` propiedad son funcionalmente equivalentes, pero no usan las dos maneras para la misma propiedad, ya que en realidad establecería la propiedad dos veces y podría ser ambigua.

Con esta nueva sintaxis, se puede introducir alguna terminología útil:

- `Label`es un *elemento de objeto*. Es un Xamarin.Forms objeto expresado como un elemento XML.
- `Text`, `VerticalOptions` `FontAttributes` y `FontSize` son atributos de *propiedad*. Son Xamarin.Forms propiedades expresadas como atributos XML.
- En ese fragmento de código final, `TextColor` se ha convertido en un *elemento de propiedad*. Es una Xamarin.Forms propiedad, pero ahora es un elemento XML.

En primer lugar, la definición de los elementos de propiedad podría ser una infracción de la sintaxis XML, pero no es así. El punto no tiene ningún significado especial en XML. En un descodificador XML, `Label.TextColor` es simplemente un elemento secundario normal.

En XAML, sin embargo, esta sintaxis es muy especial. Una de las reglas para los elementos de propiedad es que nada más puede aparecer en la `Label.TextColor` etiqueta. El valor de la propiedad siempre se define como contenido entre las etiquetas de inicio y finalización del elemento de propiedad.

Puede usar la sintaxis de elementos de propiedad en más de una propiedad:

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

O bien, puede usar la sintaxis de elementos de propiedad para todas las propiedades:

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

En primer lugar, la sintaxis del elemento de propiedad podría parecerse a un reemplazo innecesario de tiempo de inactividad en algo relativamente sencillo, y en estos ejemplos es ciertamente el caso.

Sin embargo, la sintaxis del elemento de propiedad se convierte en esencial cuando el valor de una propiedad es demasiado complejo para expresarse como una cadena simple. Dentro de las etiquetas de elemento de propiedad puede crear instancias de otro objeto y establecer sus propiedades. Por ejemplo, puede establecer explícitamente una propiedad como `VerticalOptions` para un `LayoutOptions` valor con la configuración de la propiedad:

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

Otro ejemplo: `Grid` tiene dos propiedades denominadas `RowDefinitions` y `ColumnDefinitions` . Estas dos propiedades son del tipo `RowDefinitionCollection` y `ColumnDefinitionCollection` , que son colecciones de `RowDefinition` `ColumnDefinition` objetos y. Debe usar la sintaxis del elemento de propiedad para establecer estas colecciones.

Este es el principio del archivo XAML para una `GridDemoPage` clase, que muestra las etiquetas del elemento de propiedad para las `RowDefinitions` `ColumnDefinitions` colecciones y:

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

Observe la sintaxis abreviada para definir celdas de tamaño automático, celdas de anchos de píxel y alto y configuración de estrella.

## <a name="attached-properties"></a>Propiedades adjuntas

Acaba de observar que `Grid` requiere elementos de propiedad para las `RowDefinitions` colecciones y `ColumnDefinitions` para definir las filas y columnas. Sin embargo, también debe haber alguna manera para que el programador indique la fila y la columna donde reside cada elemento secundario de `Grid` .

Dentro de la etiqueta para cada elemento secundario de `Grid` , especifique la fila y la columna de ese elemento secundario con los siguientes atributos:

- `Grid.Row`
- `Grid.Column`

Los valores predeterminados de estos atributos son 0. También puede indicar si un elemento secundario abarca más de una fila o columna con estos atributos:

- `Grid.RowSpan`
- `Grid.ColumnSpan`

Estos dos atributos tienen valores predeterminados de 1.

Este es el archivo GridDemoPage. Xaml completo:

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

Los `Grid.Row` `Grid.Column` valores de y de 0 no son necesarios, pero generalmente se incluyen para fines de claridad.

Este es su aspecto:

[![Diseño de cuadrícula](essential-xaml-syntax-images/griddemo.png)](essential-xaml-syntax-images/griddemo-large.png#lightbox)

Al juzgar únicamente a partir de la sintaxis, estos atributos,, `Grid.Row` `Grid.Column` `Grid.RowSpan` y `Grid.ColumnSpan` parecen ser campos estáticos o propiedades de `Grid` , pero lo que es interesante, no `Grid` define nada denominado `Row` , `Column` , `RowSpan` o `ColumnSpan` .

En su lugar, `Grid` define cuatro propiedades enlazables denominadas `RowProperty` ,, `ColumnProperty` `RowSpanProperty` y `ColumnSpanProperty` . Se trata de tipos especiales de propiedades enlazables conocidas como *propiedades adjuntas*. Se definen mediante la `Grid` clase, pero se establecen en los elementos secundarios de `Grid` .

Cuando desea usar estas propiedades adjuntas en el código, la `Grid` clase proporciona métodos estáticos denominados `SetRow` , `GetColumn` , etc. Pero en XAML, estas propiedades adjuntas se establecen como atributos en los elementos secundarios del `Grid` mediante nombres de propiedades simples.

Las propiedades adjuntas siempre son reconocibles en los archivos XAML como atributos que contienen una clase y un nombre de propiedad separados por un punto. Se denominan *propiedades adjuntas* porque están definidas por una clase (en este caso, `Grid` ) pero se asocian a otros objetos (en este caso, secundarios del `Grid` ). Durante el diseño, `Grid` puede consultar los valores de estas propiedades adjuntas para saber dónde colocar cada elemento secundario.

La `AbsoluteLayout` clase define dos propiedades adjuntas denominadas `LayoutBounds` y `LayoutFlags` . A continuación se muestra un patrón de tablero de ajedrez realizado mediante las características de posicionamiento y ajuste de tamaño proporcional de `AbsoluteLayout` :

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

[![Diseño absoluto](essential-xaml-syntax-images/absolutedemo-large.png)](essential-xaml-syntax-images/absolutedemo-large.png#lightbox)

Para algo parecido a esto, es posible que le resulte más importante usar XAML. En realidad, la repetición y la regularidad del `LayoutBounds` rectángulo sugieren que es posible que se produzca mejor en el código.

Eso es ciertamente una preocupación legítima y no hay ningún problema con el equilibrio del uso del código y el marcado al definir las interfaces de usuario. Es fácil definir algunos de los objetos visuales en XAML y, a continuación, usar el constructor del archivo de código subyacente para agregar algunos objetos visuales más que se puedan generar mejor en los bucles.

## <a name="content-properties"></a>Propiedades de contenido

En los ejemplos anteriores, los `StackLayout` `Grid` objetos, y `AbsoluteLayout` se establecen en la `Content` propiedad de `ContentPage` , y los elementos secundarios de estos diseños son realmente elementos de la `Children` colección. Sin embargo `Content` , estas propiedades y no `Children` están en el archivo XAML.

En realidad, puede incluir `Content` las `Children` propiedades y como elementos de propiedad, como en el ejemplo **XamlPlusCode** :

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

Los elementos definidos en Xamarin.Forms para su uso en XAML pueden tener una propiedad marcada en el `ContentProperty` atributo en la clase. Si busca la `ContentPage` clase en la documentación en línea Xamarin.Forms , verá este atributo:

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

Esto significa que `Content` no se requieren etiquetas de elemento de propiedad. Se supone que el contenido XML que aparece entre las etiquetas inicial y final se `ContentPage` asigna a la `Content` propiedad.

 `StackLayout`, `Grid` , `AbsoluteLayout` y se `RelativeLayout` derivan de y, `Layout<View>` si busca `Layout<T>` en la Xamarin.Forms documentación, verá otro `ContentProperty` atributo:

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

Que permite agregar automáticamente el contenido del diseño a la `Children` colección sin `Children` etiquetas de elemento de propiedad explícitas.

Otras clases también tienen `ContentProperty` definiciones de atributos. Por ejemplo, la propiedad de contenido de `Label` es `Text` . Consulte la documentación de la API para obtener más información.

## <a name="platform-differences-with-onplatform"></a>Diferencias de plataforma con la plataforma

En las aplicaciones de una sola página, es habitual establecer la `Padding` propiedad en la página para evitar sobrescribir la barra de estado de iOS. En el código, puede usar la `Device.RuntimePlatform` propiedad para este propósito:

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

También puede hacer algo similar en XAML mediante las [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) clases y [`On`](xref:Xamarin.Forms.On) . En primer lugar, incluya los elementos de propiedad de la `Padding` propiedad cerca de la parte superior de la página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

Dentro de estas etiquetas, incluya una `OnPlatform` etiqueta. `OnPlatform`es una clase genérica. Debe especificar el argumento de tipo genérico, en este caso, `Thickness` , que es el tipo de `Padding` propiedad. Afortunadamente, hay un atributo XAML específicamente para definir argumentos genéricos denominados `x:TypeArguments` . Debe coincidir con el tipo de la propiedad que está estableciendo:

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

`OnPlatform`tiene una propiedad denominada `Platforms` que es `IList` de `On` objetos. Use etiquetas de elemento de propiedad para esa propiedad:

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

Ahora, agregue `On` elementos. Para cada uno de ellos, establezca la propiedad `Platform` y la `Value` propiedad en marcado para la `Thickness` propiedad:

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

Este marcado se puede simplificar. La propiedad de contenido de `OnPlatform` es `Platforms` , por lo que se pueden quitar esas etiquetas de elemento de propiedad:

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

La `Platform` propiedad de `On` es de tipo `IList<string>` , por lo que puede incluir varias plataformas si los valores son los mismos:

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

Como Android y UWP se establecen en el valor predeterminado de `Padding` , se puede quitar esa etiqueta:

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

Esta es la manera estándar de establecer una propiedad dependiente de la plataforma `Padding` en XAML. Si la `Value` configuración no se puede representar mediante una sola cadena, puede definir elementos de propiedad para ella:

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
> La `OnPlatform` extensión de marcado también se puede usar en XAML para personalizar la apariencia de la interfaz de usuario en cada plataforma. Proporciona la misma funcionalidad que las `OnPlatform` clases y `On` , pero con una representación más concisa. Para obtener más información, consulte [extensión de marcado](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)en la plataforma.

## <a name="summary"></a>Resumen

Con elementos de propiedad y propiedades adjuntas, gran parte de la sintaxis básica de XAML se ha establecido. Sin embargo, a veces es necesario establecer propiedades en los objetos de una manera indirecta, por ejemplo, desde un diccionario de recursos. Este enfoque se trata en la siguiente parte, parte [3. Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md).

## <a name="related-links"></a>Vínculos relacionados

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 1. Introducción con XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 3. Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Conceptos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Del enlace de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
