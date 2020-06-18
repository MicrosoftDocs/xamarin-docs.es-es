---
title: 'title: "Xamarin.Forms: Enlace de convertidores de valores" description: "En este artículo se explica cómo convertir los valores dentro de un enlace de datos de Xamarin.Forms mediante la implementación de un convertidor de valores (que también se denomina convertidor de enlaces o convertidor de valores de enlace)."'
description: 'ms.prod: xamarin ms.assetid: 02B1BBE6-D804-490D-BDD4-8ACED8B70C92 ms.technology: xamarin-forms author: davidbritch ms.author: dabritch ms.date: 01/05/2018 no-loc: [Xamarin.Forms, Xamarin.Essentials]'
ms.prod: xamarin
ms.assetid: 02B1BBE6-D804-490D-BDD4-8ACED8B70C92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e1a4faabc8f0703b497062a8c5d587221692dab7
ms.sourcegitcommit: ea9269b5d9e3d68b61bb428560a10034117ee457
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2020
ms.locfileid: "84139768"
---
# <a name="xamarinforms-binding-value-converters"></a>Enlace de convertidores de valores de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Los enlaces de datos normalmente transfieren datos desde una propiedad de origen a una propiedad de destino y, en algunos casos, desde la propiedad de destino a la propiedad de origen. Esta transferencia es sencilla cuando las propiedades de origen y destino son del mismo tipo, o cuando un tipo se puede convertir al otro mediante una conversión implícita. Cuando no es así, debe realizarse una conversión de tipos.

En el artículo [**String Formatting**](string-formatting.md) (Formato de cadena), vio cómo puede usar la propiedad `StringFormat` de un enlace de datos para convertir cualquier tipo en una cadena. Para otros tipos de conversiones, deberá escribir código especializado en una clase que implementa la interfaz de [`IValueConverter`](xref:Xamarin.Forms.IValueConverter). (La Plataforma universal de Windows contiene una clase similar denominada [`IValueConverter`](/uwp/api/Windows.UI.Xaml.Data.IValueConverter/) en el espacio de nombres `Windows.UI.Xaml.Data`, pero este `IValueConverter` está en el espacio de nombres `Xamarin.Forms`.) Las clases que implementan `IValueConverter` se denominan *convertidores de valores*, pero también se denominan a menudo *convertidores de enlaces* o *convertidores de valores de enlace*.

## <a name="the-ivalueconverter-interface"></a>La interfaz de IValueConverter

Suponga que desea definir un enlace de datos cuya propiedad de origen es del tipo `int` pero la propiedad de destino es un `bool`. Desea que este enlace de datos genere un valor `false` cuando el origen del entero es igual a 0 y `true` en caso contrario.  

Puede hacerlo con una clase que implementa la interfaz `IValueConverter`:

```csharp
public class IntToBoolConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)value != 0;
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (bool)value ? 1 : 0;
    }
}
```

Establezca una instancia de esta clase en la propiedad [`Converter`](xref:Xamarin.Forms.Binding.Converter) de la clase `Binding` o en la propiedad [`Converter`](xref:Xamarin.Forms.Xaml.BindingExtension.Converter) de la extensión de marcado `Binding`. Esta clase se convierte en parte del enlace de datos.

Se llama al método `Convert` cuando los datos se mueven desde el origen al destino en los enlaces `OneWay` o `TwoWay`. El parámetro `value` es el objeto o el valor del origen de enlace de datos. El método debe devolver un valor del tipo del destino de enlace de datos. El método que se muestra aquí convierte el parámetro `value` a un `int` y después lo compara con 0 para un valor devuelto `bool`.

Se llama al método `ConvertBack` cuando los datos se mueven desde el destino al origen en los enlaces `TwoWay` o `OneWayToSource`. `ConvertBack` realiza la conversión opuesta: supone que el parámetro `value` es un `bool` desde el destino y lo convierte en un valor devuelto `int` para el origen.

Si el enlace de datos también incluye una configuración `StringFormat`, se invoca el convertidor de valores antes de que se le dé formato de cadena al resultado.

La página **Enable Buttons** (Habilitar botones) en el ejemplo de [**Data Binding Demos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demostraciones de enlace de datos) muestra cómo utilizar este convertidor de valores en un enlace de datos. Se crea una instancia de `IntToBoolConverter` en el diccionario de recursos de la página. Después se le hace referencia con una extensión de marcado `StaticResource` para establecer la propiedad `Converter` en dos enlaces de datos. Es muy común compartir los convertidores de tipos de datos entre varios enlaces de datos en la página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.EnableButtonsPage"
             Title="Enable Buttons">
    <ContentPage.Resources>
        <ResourceDictionary>
            <local:IntToBoolConverter x:Key="intToBool" />
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <Entry x:Name="entry1"
               Text=""
               Placeholder="enter search term"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Search"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                IsEnabled="{Binding Source={x:Reference entry1},
                                    Path=Text.Length,
                                    Converter={StaticResource intToBool}}" />

        <Entry x:Name="entry2"
               Text=""
               Placeholder="enter destination"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Submit"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                IsEnabled="{Binding Source={x:Reference entry2},
                                    Path=Text.Length,
                                    Converter={StaticResource intToBool}}" />
    </StackLayout>
</ContentPage>
```

Si se usa un convertidor de valores en varias páginas de la aplicación, puede crear una instancia de él en el diccionario de recursos en el archivo **App.xaml**.

La página **Enable Buttons** (Habilitar botones) muestra una necesidad común cuando un `Button` realiza una operación basada en texto que el usuario escribe en un vista `Entry`. Si no se ha escrito nada en el `Entry`, el `Button` debe deshabilitarse. Cada `Button` contiene un enlace de datos en su propiedad `IsEnabled`. El origen de enlace de datos es la propiedad `Length` de la propiedad `Text` de la `Entry` correspondiente. Si esa propiedad `Length` no es 0, el convertidor de valores devuelve `true` y se habilita el `Button`:

[![Habilitar botones](converters-images/enablebuttons-small.png "Habilitar botones")](converters-images/enablebuttons-large.png#lightbox "Habilitar botones")

Tenga en cuenta que la propiedad `Text` en cada `Entry` se inicializa en una cadena vacía. La propiedad `Text` es `null` de forma predeterminada, y los datos de enlace no funcionarán en ese caso.

Algunos convertidores de valores se escriben específicamente para determinadas aplicaciones, mientras que otros están generalizados. Si sabe que un convertidor de valores solo se usará en los enlaces `OneWay`, el método `ConvertBack` puede devolver simplemente `null`.

El método `Convert` mostrado anteriormente supone implícitamente que el argumento `value` es de tipo `int` y el valor devuelto debe ser de tipo `bool`. De forma similar, el método `ConvertBack` supone que el argumento `value` es de tipo `bool` y el valor devuelto es `int`. Si no es así, se producirá una excepción en tiempo de ejecución.

Puede escribir los convertidores de valores para que sean más generalizados y acepten diferentes tipos de datos. Los métodos `Convert` y `ConvertBack` pueden usar los operadores `as` o `is` con el parámetro `value`, o pueden llamar a `GetType` en ese parámetro para determinar su tipo y después realizar algo adecuado. El tipo esperado del valor devuelto de cada método viene dado por el parámetro `targetType`. A veces, los convertidores de valores se utilizan con los enlaces de datos de diferentes tipos de destino; el convertidor de valores puede usar el argumento `targetType` para realizar una conversión del tipo correcto.

Si la conversión que se realiza es diferente para distintas referencias culturales, utilice el parámetro `culture` para este propósito. El argumento `parameter` para `Convert` y `ConvertBack` se explica más adelante en este artículo.

## <a name="binding-converter-properties"></a>Propiedades de convertidor de tipos de enlace

Las clases de convertidor de valores pueden tener propiedades y parámetros genéricos. Este convertidor de valores determinado convierte un `bool` desde el origen a un objeto de tipo `T` para el destino:

```csharp
public class BoolToObjectConverter<T> : IValueConverter
{
    public T TrueObject { set; get; }

    public T FalseObject { set; get; }

    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (bool)value ? TrueObject : FalseObject;
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return ((T)value).Equals(TrueObject);
    }
}
```

La página **Switch Indicators** (Indicadores de conmutador) muestra cómo puede usarse para mostrar el valor de una vista `Switch`. Aunque es común crear instancias de los convertidores de valores como recursos en un diccionario de recursos, esta página muestra una alternativa: se crea una instancia de cada convertidor de valores entre etiquetas de elemento de propiedad `Binding.Converter`. El `x:TypeArguments` indica el argumento genérico, y `TrueObject` y `FalseObject` se establecen en objetos de ese tipo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SwitchIndicatorsPage"
             Title="Switch Indicators">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="18" />
                <Setter Property="VerticalOptions" Value="Center" />
            </Style>

            <Style TargetType="Switch">
                <Setter Property="VerticalOptions" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Subscribe?" />
            <Switch x:Name="switch1" />
            <Label>
                <Label.Text>
                    <Binding Source="{x:Reference switch1}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="x:String"
                                                         TrueObject="Of course!"
                                                         FalseObject="No way!" />
                        </Binding.Converter>
                    </Binding>
                </Label.Text>
            </Label>
        </StackLayout>

        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Allow popups?" />
            <Switch x:Name="switch2" />
            <Label>
                <Label.Text>
                    <Binding Source="{x:Reference switch2}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="x:String"
                                                         TrueObject="Yes"
                                                         FalseObject="No" />
                        </Binding.Converter>
                    </Binding>
                </Label.Text>
                <Label.TextColor>
                    <Binding Source="{x:Reference switch2}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="Color"
                                                         TrueObject="Green"
                                                         FalseObject="Red" />
                        </Binding.Converter>
                    </Binding>
                </Label.TextColor>
            </Label>
        </StackLayout>

        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Learn more?" />
            <Switch x:Name="switch3" />
            <Label FontSize="18"
                   VerticalOptions="Center">
                <Label.Style>
                    <Binding Source="{x:Reference switch3}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="Style">
                                <local:BoolToObjectConverter.TrueObject>
                                    <Style TargetType="Label">
                                        <Setter Property="Text" Value="Indubitably!" />
                                        <Setter Property="FontAttributes" Value="Italic, Bold" />
                                        <Setter Property="TextColor" Value="Green" />
                                    </Style>                                    
                                </local:BoolToObjectConverter.TrueObject>

                                <local:BoolToObjectConverter.FalseObject>
                                    <Style TargetType="Label">
                                        <Setter Property="Text" Value="Maybe later" />
                                        <Setter Property="FontAttributes" Value="None" />
                                        <Setter Property="TextColor" Value="Red" />
                                    </Style>
                                </local:BoolToObjectConverter.FalseObject>
                            </local:BoolToObjectConverter>
                        </Binding.Converter>
                    </Binding>
                </Label.Style>
            </Label>
        </StackLayout>
    </StackLayout>
</ContentPage>
```

En el último de los tres pares `Switch` y `Label`, el argumento genérico se establece en `Style` y se proporcionan objetos `Style` completos para los valores de `TrueObject` y `FalseObject`. Esto ignora el estilo implícito para `Label` que se establece en el diccionario de recursos, por lo que las propiedades de ese estilo están asignadas explícitamente a la `Label`. Activar o desactivar el `Switch` hace que el correspondiente `Label` refleje el cambio:

[![Indicadores de conmutador](converters-images/switchindicators-small.png "Indicadores de conmutador")](converters-images/switchindicators-large.png#lightbox "Indicadores de conmutador")

También es posible usar [`Triggers`](~/xamarin-forms/app-fundamentals/triggers.md) para implementar cambios similares en la interfaz de usuario en función de otras vistas.

## <a name="binding-converter-parameters"></a>Parámetros de convertidor de tipos de enlace

La clase `Binding` define una propiedad [`ConverterParameter`](xref:Xamarin.Forms.Binding.ConverterParameter) y la extensión de marcado `Binding` también define una propiedad [`ConverterParameter`](xref:Xamarin.Forms.Xaml.BindingExtension.ConverterParameter). Si se establece esta propiedad, el valor se pasa a los métodos `Convert` y `ConvertBack` como el argumento `parameter`. Incluso si la instancia del convertidor se comparte entre varios enlaces de datos, el `ConverterParameter` puede ser diferente para realizar conversiones algo diferentes.

El uso de `ConverterParameter` se muestra con un programa de selección de color. En este caso, el `RgbColorViewModel` tiene tres propiedades de tipo `double` denominadas `Red`, `Green` y `Blue` que usa para construir un valor `Color`:

```csharp
public class RgbColorViewModel : INotifyPropertyChanged
{
    Color color;
    string name;

    public event PropertyChangedEventHandler PropertyChanged;

    public double Red
    {
        set
        {
            if (color.R != value)
            {
                Color = new Color(value, color.G, color.B);
            }
        }
        get
        {
            return color.R;
        }
    }

    public double Green
    {
        set
        {
            if (color.G != value)
            {
                Color = new Color(color.R, value, color.B);
            }
        }
        get
        {
            return color.G;
        }
    }

    public double Blue
    {
        set
        {
            if (color.B != value)
            {
                Color = new Color(color.R, color.G, value);
            }
        }
        get
        {
            return color.B;
        }
    }

    public Color Color
    {
        set
        {
            if (color != value)
            {
                color = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Red"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Green"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Blue"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Color"));

                Name = NamedColor.GetNearestColorName(color);
            }
        }
        get
        {
            return color;
        }
    }

    public string Name
    {
        private set
        {
            if (name != value)
            {
                name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Name"));
            }
        }
        get
        {
            return name;
        }
    }
}
```

Las propiedades `Red`, `Green` y `Blue` oscilan entre 0 y 1. Con todo, es preferible que los componentes se muestren como valores hexadecimales de dos dígitos.

Para mostrar estos elementos como valores hexadecimales en XAML, deben multiplicarse por 255, convertirse en un entero y después debe aplicárseles formato con la especificación de "X2" en la propiedad `StringFormat`. Las dos primeras tareas (multiplicar por 255 y convertir en un entero) pueden controlarse mediante el convertidor de valores. Para hacer el convertidor de valores tan generalizado como sea posible, puede especificarse el factor de multiplicación con la propiedad `ConverterParameter`, lo que significa que introduce los métodos `Convert` y `ConvertBack` como el argumento `parameter`:

```csharp
public class DoubleToIntConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)Math.Round((double)value * GetParameter(parameter));
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)value / GetParameter(parameter);
    }

    double GetParameter(object parameter)
    {
        if (parameter is double)
            return (double)parameter;

        else if (parameter is int)
            return (int)parameter;

        else if (parameter is string)
            return double.Parse((string)parameter);

        return 1;
    }
}
```

El `Convert` convierte de un `double` a `int` al multiplicar por el valor `parameter`; el `ConvertBack` divide el argumento `value` entero entre `parameter` y devuelve un resultado `double`. (En el programa que se muestra debajo, el convertidor de valores se usa solo en relación con el formato de cadenas, por lo que `ConvertBack` no se usa.)

Es probable que el tipo del argumento `parameter` sea diferente en función de si se ha definido el enlace de datos en el código o en XAML. Si la propiedad `ConverterParameter` de `Binding` se establece en código, es probable que se establezca en un valor numérico:

```csharp
binding.ConverterParameter = 255;
```

La propiedad `ConverterParameter` es de tipo `Object`, por lo que el compilador C# interpreta el literal 255 como un entero y establece la propiedad en ese valor.

Pero en XAML es probable que el `ConverterParameter` se establezca de este modo:

```xaml
<Label Text="{Binding Red,
                      Converter={StaticResource doubleToInt},
                      ConverterParameter=255,
                      StringFormat='Red = {0:X2}'}" />
```

El 255 parece un número, pero dado que `ConverterParameter` es de tipo `Object`, el analizador XAML trata el 255 como una cadena.

Por ese motivo, el convertidor de valores mostrado anteriormente incluye un método `GetParameter` independiente que controla los casos para `parameter` de tipo `double`, `int` o `string`.  

La página **RGB Color Selector** (Selector de colores RGB) crea una instancia de la página `DoubleToIntConverter` en su diccionario de recursos siguiendo la definición de dos estilos implícitos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.RgbColorSelectorPage"
             Title="RGB Color Selector">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>

            <local:DoubleToIntConverter x:Key="doubleToInt" />
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <StackLayout.BindingContext>
            <local:RgbColorViewModel Color="Gray" />
        </StackLayout.BindingContext>

        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />

            <Slider Value="{Binding Red}" />
            <Label Text="{Binding Red,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Red = {0:X2}'}" />

            <Slider Value="{Binding Green}" />
            <Label Text="{Binding Green,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Green = {0:X2}'}" />

            <Slider Value="{Binding Blue}" />
            <Label>
                <Label.Text>
                    <Binding Path="Blue"
                             StringFormat="Blue = {0:X2}"
                             Converter="{StaticResource doubleToInt}">
                        <Binding.ConverterParameter>
                            <x:Double>255</x:Double>
                        </Binding.ConverterParameter>
                    </Binding>
                </Label.Text>
            </Label>
        </StackLayout>
    </StackLayout>
</ContentPage>    
```

Los valores de las propiedades `Red` y `Green` se muestran con una extensión de marcado `Binding`. Empero, la propiedad `Blue` crea una instancia de la clase `Binding` para demostrar cómo un valor `double` explícito puede establecerse en la propiedad `ConverterParameter`.

Este es el resultado:

[![Selector de colores RGB](converters-images/rgbcolorselector-small.png "Selector de colores RGB")](converters-images/rgbcolorselector-large.png#lightbox "Selector de colores RGB")

## <a name="related-links"></a>Vínculos relacionados

- [Data Binding Demos (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demos de enlace de datos [ejemplo])
- [Capítulo sobre el enlace de datos del libro Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
