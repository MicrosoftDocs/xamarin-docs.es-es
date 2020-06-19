---
title: Xamarin.FormsHasta
description: El Xamarin.Forms control deslizante es una barra horizontal que el usuario puede manipular para seleccionar un valor doble de un intervalo continuo. En este artículo se explica cómo usar la clase Slider para seleccionar un valor de un intervalo de valores continuos.
ms.prod: xamarin
ms.assetid: 36B1C645-26E0-4874-B6B6-BDBF77662878
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9fc61dcac588f49fc1df7ee96792cf55bdfa46c5
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84572044"
---
# <a name="xamarinforms-slider"></a>Xamarin.FormsHasta

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)

_Use un control deslizante para seleccionar entre un intervalo de valores continuos._

Xamarin.Forms [`Slider`](xref:Xamarin.Forms.Slider) Es una barra horizontal que el usuario puede manipular para seleccionar un `double` valor de un intervalo continuo.

`Slider`Define tres propiedades de tipo `double` :

- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum)es el mínimo del intervalo, con un valor predeterminado de 0.
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum)es el máximo del intervalo, con un valor predeterminado de 1.
- [`Value`](xref:Xamarin.Forms.Slider.Value)es el valor del control deslizante, que puede oscilar entre `Minimum` y `Maximum` y tiene un valor predeterminado de 0.

Las tres propiedades están respaldadas por `BindableProperty` objetos. La `Value` propiedad tiene un modo de enlace predeterminado de `BindingMode.TwoWay` , lo que significa que es adecuado como origen de enlace en una aplicación que usa la arquitectura [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) .

> [!WARNING]
> Internamente, `Slider` garantiza que `Minimum` es menor que `Maximum` . Si `Minimum` o `Maximum` se establecen en cualquier momento para que `Minimum` no sea menor que `Maximum` , se produce una excepción. Vea la sección [**precauciones**](#precautions) a continuación para obtener más información sobre cómo establecer las `Minimum` `Maximum` propiedades y.

`Slider`Convierte la propiedad de `Value` modo que esté entre `Minimum` y `Maximum` , ambos incluidos. Si la `Minimum` propiedad se establece en un valor mayor que la `Value` propiedad, `Slider` establece la `Value` propiedad en `Minimum` . Del mismo modo, si `Maximum` se establece en un valor menor que `Value` , `Slider` establece la `Value` propiedad en `Maximum` .

`Slider`define un [`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) evento que se desencadena cuando `Value` cambia, ya sea a través de la manipulación del usuario `Slider` o cuando el programa establece la `Value` propiedad directamente. `ValueChanged`También se desencadena un evento cuando la `Value` propiedad se convierte como se describe en el párrafo anterior.

El [`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs) objeto que acompaña al `ValueChanged` evento tiene dos propiedades, ambas de tipo `double` : [`OldValue`](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue) y [`NewValue`](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue) . En el momento en que se desencadena el evento, el valor de `NewValue` es igual que la `Value` propiedad del `Slider` objeto.

`Slider`también define `DragStarted` `DragCompleted` los eventos y, que se desencadenan al principio y al final de la acción de arrastrar. A diferencia del [`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) evento, los `DragStarted` `DragCompleted` eventos y solo se activan a través de la manipulación de usuarios de `Slider` . Cuando `DragStarted` se activa el evento, `DragStartedCommand` se ejecuta el tipo de `ICommand` . De forma similar, cuando `DragCompleted` se desencadena el evento, `DragCompletedCommand` se ejecuta el tipo de `ICommand` .

> [!WARNING]
> No utilice las opciones de diseño horizontal sin restricciones de `Center` , `Start` o `End` con `Slider` . Tanto en Android como en UWP, `Slider` se contrae en una barra de longitud cero y, en iOS, la barra es muy breve. Mantenga el `HorizontalOptions` valor predeterminado de `Fill` y no use el ancho `Auto` al colocar `Slider` un `Grid` diseño.

`Slider`También define varias propiedades que afectan a su apariencia:

- [`MinimumTrackColor`](xref:Xamarin.Forms.Slider.MinimumTrackColorProperty)es el color de la barra que se encuentra en el lado izquierdo del control.
- [`MaximumTrackColor`](xref:Xamarin.Forms.Slider.MaximumTrackColorProperty)es el color de la barra que se encuentra en el lado derecho del control.
- [`ThumbColor`](xref:Xamarin.Forms.Slider.ThumbColorProperty)es el color del control de posición.
- [`ThumbImageSource`](xref:Xamarin.Forms.Slider.ThumbImageSourceProperty)es la imagen que se va a usar para el control de posición, de tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource) .

> [!NOTE]
> Las `ThumbColor` `ThumbImageSource` propiedades y son mutuamente excluyentes. Si se establecen ambas propiedades, la `ThumbImageSource` propiedad tendrá prioridad.

## <a name="basic-slider-code-and-markup"></a>Código y marcado del control deslizante básico

El ejemplo [**SliderDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) comienza con tres páginas que son funcionalmente idénticas, pero se implementan de maneras diferentes. La primera página solo usa código C#, la segunda usa XAML con un controlador de eventos en el código y la tercera puede evitar el controlador de eventos mediante el enlace de datos en el archivo XAML.

### <a name="creating-a-slider-in-code"></a>Crear un control deslizante en el código

La página de **códigos del control deslizante básico** en el ejemplo [**SliderDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) muestra Mostrar para crear un `Slider` y dos `Label` objetos en el código:

```csharp
public class BasicSliderCodePage : ContentPage
{
    public BasicSliderCodePage()
    {
        Label rotationLabel = new Label
        {
            Text = "ROTATING TEXT",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Label displayLabel = new Label
        {
            Text = "(uninitialized)",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Slider slider = new Slider
        {
            Maximum = 360
        };
        slider.ValueChanged += (sender, args) =>
        {
            rotationLabel.Rotation = slider.Value;
            displayLabel.Text = String.Format("The Slider value is {0}", args.NewValue);
        };

        Title = "Basic Slider Code";
        Padding = new Thickness(10, 0);
        Content = new StackLayout
        {
            Children =
            {
                rotationLabel,
                slider,
                displayLabel
            }
        };
    }
}
```

`Slider`Se inicializa para tener una `Maximum` propiedad de 360. El `ValueChanged` controlador de `Slider` utiliza la `Value` propiedad del `slider` objeto para establecer la `Rotation` propiedad de la primera `Label` y usa el `String.Format` método con la `NewValue` propiedad de los argumentos del evento para establecer la `Text` propiedad de la segunda `Label` . Estos dos enfoques para obtener el valor actual de `Slider` son intercambiables.

Este es el programa que se ejecuta en dispositivos iOS y Android:

[![Código de control deslizante básico](slider-images/BasicSliderCode.png "Código de control deslizante básico")](slider-images/BasicSliderCode-Large.png#lightbox)

En el segundo `Label` se muestra el texto "(no inicializado)" hasta que `Slider` se manipula, lo que hace que se desencadene el primer `ValueChanged` evento. Tenga en cuenta que el número de posiciones decimales que se muestran es diferente para cada plataforma. Estas diferencias están relacionadas con las implementaciones de la plataforma de `Slider` y se describen más adelante en este artículo en la sección [diferencias de implementación de plataforma](#platform-implementation-differences).

### <a name="creating-a-slider-in-xaml"></a>Crear un control deslizante en XAML

La página **XAML del control deslizante básico** es funcionalmente igual que el **código deslizante básico** , pero se implementa principalmente en XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.BasicSliderXamlPage"
             Title="Basic Slider XAML"
             Padding="10, 0">
    <StackLayout>
        <Label x:Name="rotatingLabel"
               Text="ROTATING TEXT"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider Maximum="360"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="displayLabel"
               Text="(uninitialized)"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

El archivo de código subyacente contiene el controlador del `ValueChanged` evento:

```csharp
public partial class BasicSliderXamlPage : ContentPage
{
    public BasicSliderXamlPage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        double value = args.NewValue;
        rotatingLabel.Rotation = value;
        displayLabel.Text = String.Format("The Slider value is {0}", value);
    }
}
```

También es posible que el controlador de eventos obtenga el `Slider` que está desencadenando el evento a través del `sender` argumento. La `Value` propiedad contiene el valor actual:

```csharp
double value = ((Slider)sender).Value;
```

Si al `Slider` objeto se le asignó un nombre en el archivo XAML con un `x:Name` atributo (por ejemplo, "slider"), el controlador de eventos podría hacer referencia a ese objeto directamente:

```csharp
double value = slider.Value;
```

### <a name="data-binding-the-slider"></a>Enlace de datos del control deslizante

En la página de **enlaces del control deslizante básico** se muestra cómo escribir un programa casi equivalente que elimina el controlador de eventos mediante el `Value` [enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.BasicSliderBindingsPage"
             Title="Basic Slider Bindings"
             Padding="10, 0">
    <StackLayout>
        <Label Text="ROTATING TEXT"
               Rotation="{Binding Source={x:Reference slider},
                                  Path=Value}"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360" />

        <Label x:Name="displayLabel"
               Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='The Slider value is {0:F0}'}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

La `Rotation` propiedad de la primera `Label` se enlaza a la `Value` propiedad de `Slider` , como es la `Text` propiedad del segundo `Label` con una `StringFormat` especificación. La página de **enlaces del control deslizante básico** funciona de forma ligeramente diferente a la de las dos páginas anteriores: cuando la página aparece por primera vez, la segunda `Label` muestra la cadena de texto con el valor. Esta es una de las ventajas de usar el enlace de datos. Para mostrar texto sin enlace de datos, tendría que inicializar específicamente la `Text` propiedad de `Label` o simular una activación del evento llamando `ValueChanged` al controlador de eventos desde el constructor de clase.

## <a name="precautions"></a>PRECAUCIONES

El valor de la `Minimum` propiedad siempre debe ser menor que el valor de la `Maximum` propiedad. El fragmento de código siguiente hace que `Slider` genere una excepción:

```csharp
// Throws an exception!
Slider slider = new Slider
{
    Minimum = 10,
    Maximum = 20
};
```

El compilador de C# genera código que establece estas dos propiedades en secuencia y, cuando la `Minimum` propiedad se establece en 10, es mayor que el `Maximum` valor predeterminado de 1. Puede evitar la excepción en este caso estableciendo `Maximum` primero la propiedad:

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

Establecer `Maximum` en 20 no es un problema porque es mayor que el valor predeterminado `Minimum` de 0. Cuando `Minimum` se establece, el valor es menor que el `Maximum` valor de 20.

El mismo problema existe en XAML. Establezca las propiedades en un orden que garantice que `Maximum` siempre sea mayor que `Minimum` :

```xaml
<Slider Maximum="20"
        Minimum="10" ... />
```

Puede establecer los `Minimum` valores y `Maximum` en números negativos, pero solo en un orden en el que `Minimum` siempre sea menor que `Maximum` :

```xaml
<Slider Minimum="-20"
        Maximum="-10" ... />
```

La `Value` propiedad siempre es mayor o igual que el `Minimum` valor y menor o igual que `Maximum` . Si `Value` se establece en un valor fuera de ese intervalo, el valor se convertirá para que se encuentre dentro del intervalo, pero no se genera ninguna excepción. Por ejemplo, este código *no* producirá una excepción:

```csharp
Slider slider = new Slider
{
    Value = 10
};
```

En su lugar, la `Value` propiedad se convierte en el `Maximum` valor de 1.

A continuación se muestra un fragmento de código mostrado anteriormente:

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

Cuando `Minimum` está establecido en 10, `Value` también se establece en 10.

Si se ha `ValueChanged` adjuntado un controlador de eventos en el momento en que la `Value` propiedad se convierte en algo distinto de su valor predeterminado de 0, `ValueChanged` se desencadena un evento. A continuación se muestra un fragmento de código XAML:

```xaml
<Slider ValueChanged="OnSliderValueChanged"
        Maximum="20"
        Minimum="10" />
```

Cuando `Minimum` se establece en 10, `Value` también se establece en 10 y `ValueChanged` se desencadena el evento. Esto puede ocurrir antes de que se haya construido el resto de la página y el controlador podría intentar hacer referencia a otros elementos de la página que aún no se han creado. Es posible que desee agregar código al `ValueChanged` controlador que compruebe `null` los valores de otros elementos de la página. O bien, puede establecer el `ValueChanged` controlador de eventos después de `Slider` inicializar los valores.

## <a name="platform-implementation-differences"></a>Diferencias de implementación de plataforma

Las capturas de pantalla mostradas anteriormente muestran el valor de `Slider` con un número diferente de separadores decimales. Esto se refiere al modo `Slider` en que se implementa en las plataformas Android y UWP.

### <a name="the-android-implementation"></a>La implementación de Android

La implementación de Android de `Slider` se basa en Android [`SeekBar`](xref:Android.Widget.SeekBar) y siempre establece la [`Max`](xref:Android.Widget.ProgressBar.Max) propiedad en 1000. Esto significa que `Slider` en Android solo tiene 1.001 valores discretos. Si establece para que `Slider` tenga un `Minimum` de 0 y un `Maximum` de 5000, a medida que `Slider` se manipule, la `Value` propiedad tiene los valores 0, 5, 10, 15, etc.

### <a name="the-uwp-implementation"></a>La implementación de UWP

La implementación de UWP de `Slider` se basa en el [`Slider`](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.slider) control de UWP. La `StepFrequency` propiedad de UWP `Slider` se establece en la diferencia de las `Maximum` propiedades y `Minimum` divididas entre 10, pero no mayor que 1.

Por ejemplo, para el intervalo predeterminado de 0 a 1, la `StepFrequency` propiedad se establece en 0,1. A medida que `Slider` se manipula, la `Value` propiedad está restringida a 0, 0,1, 0,2, 0,3, 0,4, 0,5, 0,6, 0,7, 0,8, 0,9 y 1,0. (Esto es evidente en la última página del ejemplo [**SliderDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) ). Cuando la diferencia entre las `Maximum` `Minimum` propiedades y es 10 o superior, `StepFrequency` se establece en 1 y la `Value` propiedad tiene valores enteros.

### <a name="the-stepslider-solution"></a>La solución StepSlider

Un más versátil `StepSlider` se describe en el [capítulo 27. Los representadores personalizados](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf) del libro que *crean Xamarin.Forms Mobile Apps con *. `StepSlider`Es similar a `Slider` , pero agrega una `Steps` propiedad para especificar el número de valores entre `Minimum` y `Maximum` .

## <a name="sliders-for-color-selection"></a>Controles deslizantes para la selección de color

Las dos páginas finales del ejemplo [**SliderDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) usan tres `Slider` instancias para la selección de color. La primera página controla todas las interacciones del archivo de código subyacente, mientras que la segunda página muestra cómo usar el enlace de datos con un ViewModel.

### <a name="handling-sliders-in-the-code-behind-file"></a>Control de controles deslizantes en el archivo de código subyacente

La página de **controles deslizantes de color RGB** crea una instancia de `BoxView` para mostrar un color, tres `Slider` instancias para seleccionar los componentes rojo, verde y azul del color, y tres `Label` elementos para mostrar esos valores de color:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.RgbColorSlidersPage"
             Title="RGB Color Sliders">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="Maximum" Value="255" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <BoxView x:Name="boxView"
                 Color="Black"
                 VerticalOptions="FillAndExpand" />

        <Slider x:Name="redSlider"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="redLabel" />

        <Slider x:Name="greenSlider"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="greenLabel" />

        <Slider x:Name="blueSlider"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="blueLabel" />
    </StackLayout>
</ContentPage>
```

Una `Style` asigna a los tres `Slider` elementos un intervalo de 0 a 255. Los `Slider` elementos comparten el mismo `ValueChanged` controlador, que se implementa en el archivo de código subyacente:

```csharp
public partial class RgbColorSlidersPage : ContentPage
{
    public RgbColorSlidersPage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (sender == redSlider)
        {
            redLabel.Text = String.Format("Red = {0:X2}", (int)args.NewValue);
        }
        else if (sender == greenSlider)
        {
            greenLabel.Text = String.Format("Green = {0:X2}", (int)args.NewValue);
        }
        else if (sender == blueSlider)
        {
            blueLabel.Text = String.Format("Blue = {0:X2}", (int)args.NewValue);
        }

        boxView.Color = Color.FromRgb((int)redSlider.Value,
                                      (int)greenSlider.Value,
                                      (int)blueSlider.Value);
    }
}
```

En la primera sección se establece la `Text` propiedad de una de las `Label` instancias de en una cadena de texto corta que indica el valor de `Slider` en hexadecimal. A continuación, `Slider` se tiene acceso a las tres instancias para crear un `Color` valor a partir de los componentes RGB:

[![Controles deslizantes de color RGB](slider-images/RgbColorSliders.png "Controles deslizantes de color RGB")](slider-images/RgbColorSliders-Large.png#lightbox)

### <a name="binding-the-slider-to-a-viewmodel"></a>Enlazar el control deslizante a un ViewModel

La página de **controles deslizantes de color HSL** muestra cómo usar un ViewModel para realizar los cálculos utilizados para crear un `Color` valor a partir de los valores de matiz, saturación y luminosidad. Al igual que todos los ViewModels, la `HSLColorViewModel` clase implementa la `INotifyPropertyChanged` interfaz y activa un `PropertyChanged` evento cada vez que cambia una de las propiedades:

```csharp
public class HslColorViewModel : INotifyPropertyChanged
{
    Color color;

    public event PropertyChangedEventHandler PropertyChanged;

    public double Hue
    {
        set
        {
            if (color.Hue != value)
            {
                Color = Color.FromHsla(value, color.Saturation, color.Luminosity);
            }
        }
        get
        {
            return color.Hue;
        }
    }

    public double Saturation
    {
        set
        {
            if (color.Saturation != value)
            {
                Color = Color.FromHsla(color.Hue, value, color.Luminosity);
            }
        }
        get
        {
            return color.Saturation;
        }
    }

    public double Luminosity
    {
        set
        {
            if (color.Luminosity != value)
            {
                Color = Color.FromHsla(color.Hue, color.Saturation, value);
            }
        }
        get
        {
            return color.Luminosity;
        }
    }

    public Color Color
    {
        set
        {
            if (color != value)
            {
                color = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Hue"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Saturation"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Luminosity"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Color"));
            }
        }
        get
        {
            return color;
        }
    }
}
```

ViewModels y la `INotifyPropertyChanged` interfaz se describen en el artículo [enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md).

El archivo **HslColorSlidersPage. Xaml** crea una instancia de `HslColorViewModel` y lo establece en la propiedad de la página `BindingContext` . Esto permite que todos los elementos del archivo XAML se enlacen a las propiedades de ViewModel:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SliderDemos"
             x:Class="SliderDemos.HslColorSlidersPage"
             Title="HSL Color Sliders">

    <ContentPage.BindingContext>
        <local:HslColorViewModel Color="Chocolate" />
    </ContentPage.BindingContext>

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <Slider Value="{Binding Hue}" />
        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />

        <Slider Value="{Binding Saturation}" />
        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />

        <Slider Value="{Binding Luminosity}" />
        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
    </StackLayout>
</ContentPage>
```

A medida que `Slider` se manipulan los elementos, los `BoxView` `Label` elementos y se actualizan desde el ViewModel:

[![Controles deslizantes de color HSL](slider-images/HslColorSliders.png "Controles deslizantes de color HSL")](slider-images/HslColorSliders-Large.png#lightbox)

El `StringFormat` componente de la `Binding` extensión de marcado se establece para que el formato "F2" muestre dos posiciones decimales. (El formato de cadena en los enlaces de datos se describe en el artículo [formato de cadena](~/xamarin-forms/app-fundamentals/data-binding/string-formatting.md)). Sin embargo, la versión de UWP del programa se limita a los valores 0, 0,1, 0,2,... 0,9 y 1,0. Este es un resultado directo de la implementación de UWP `Slider` tal y como se ha descrito anteriormente en la sección [diferencias de implementación de plataforma](#platform-implementation-differences).

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de demostraciones de control deslizante](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)
- [Slider API](xref:Xamarin.Forms.Slider)
