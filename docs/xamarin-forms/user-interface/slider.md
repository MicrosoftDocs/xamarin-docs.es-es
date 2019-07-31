---
title: Control deslizante de Xamarin.Forms
description: El control deslizante de Xamarin.Forms es una barra horizontal que se puede manipular por el usuario para seleccionar un valor doble de un intervalo continuo. En este artículo se explica cómo usar la clase de control deslizante para seleccionar un valor de un intervalo de valores continuos.
ms.prod: xamarin
ms.assetid: 36B1C645-26E0-4874-B6B6-BDBF77662878
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2019
ms.openlocfilehash: e54b36989f5b4694da5d46a89391d56920b8d6a9
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656248"
---
# <a name="xamarinforms-slider"></a>Control deslizante de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)

_Usar un control deslizante para seleccionar un intervalo de valores continuos._

Xamarin.Forms [ `Slider` ](xref:Xamarin.Forms.Slider) es una barra horizontal que se puede manipular por el usuario para seleccionar un `double` valor desde un intervalo continuo.

El `Slider` define tres propiedades de tipo `double`:

- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) es el mínimo del intervalo, con un valor predeterminado de 0.
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) es el máximo del intervalo, con un valor predeterminado de 1.
- [`Value`](xref:Xamarin.Forms.Slider.Value) es el valor del control deslizante, que puede oscilar entre `Minimum` y `Maximum` y tiene un valor predeterminado de 0.

Las tres propiedades están respaldadas por `BindableProperty` objetos. El `Value` propiedad tiene un modo de enlace predeterminada de `BindingMode.TwoWay`, lo que significa que es adecuado como origen de enlace en una aplicación que utiliza el [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) arquitectura.

> [!WARNING]
> Internamente, el `Slider` garantiza que `Minimum` es menor que `Maximum`. Si `Minimum` o `Maximum` nunca se establecen para que `Minimum` es menos `Maximum`, se produce una excepción. Consulte la [ **precauciones** ](#precautions) sección para obtener más información sobre cómo el `Minimum` y `Maximum` propiedades.

El `Slider` convierte la `Value` propiedad para que esté entre `Minimum` y `Maximum`, ambos inclusive. Si el `Minimum` propiedad se establece en un valor mayor que el `Value` propiedad, el `Slider` establece la `Value` propiedad `Minimum`. De forma similar, si `Maximum` está establecido en un valor menor que `Value`, a continuación, `Slider` establece la `Value` propiedad a `Maximum`.

`Slider` define un [ `ValueChanged` ](xref:Xamarin.Forms.Slider.ValueChanged) evento que se desencadena cuando el `Value` cambios, ya sea a través de la manipulación del usuario de la `Slider` o cuando el programa establece la `Value` propiedad directamente. Un `ValueChanged` evento se desencadena cuando el `Value` propiedad se convierte como se describe en el párrafo anterior.

El [ `ValueChangedEventArgs` ](xref:Xamarin.Forms.ValueChangedEventArgs) objeto que acompaña a la `ValueChanged` eventos tiene dos propiedades, ambos de tipo `double`: [ `OldValue` ](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue) y [ `NewValue` ](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue). En el momento en el evento se desencadena, el valor de `NewValue` es el mismo que el `Value` propiedad de la `Slider` objeto.

`Slider`también define `DragStarted` los `DragCompleted` eventos y, que se desencadenan al principio y al final de la acción de arrastrar. A diferencia del [`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) evento, los `DragStarted` eventos `DragCompleted` y solo se activan a través de la `Slider`manipulación de usuarios de. Cuando se `DragStarted` activa el evento, `DragStartedCommand`se ejecuta el `ICommand`tipo de. De forma similar, `DragCompleted` cuando se desencadena el `DragCompletedCommand`evento, se `ICommand`ejecuta el tipo de.

> [!WARNING]
> No utilice opciones de diseño horizontal sin restricciones de `Center`, `Start`, o `End` con `Slider`. En Android y UWP, la `Slider` contrae a una barra de longitud cero y en iOS, la barra es muy breve. Mantenga el valor predeterminado `HorizontalOptions` de `Fill`y no use un ancho de `Auto` al poner `Slider` en un `Grid` diseño.

El `Slider` también define varias propiedades que afectan a su apariencia:

- [`MinimumTrackColor`](xref:Xamarin.Forms.Slider.MinimumTrackColorProperty) es la barra de color en el lado izquierdo del control.
- [`MaximumTrackColor`](xref:Xamarin.Forms.Slider.MaximumTrackColorProperty) es la barra de color en el lado derecho del control.
- [`ThumbColor`](xref:Xamarin.Forms.Slider.ThumbColorProperty) es el color del control de posición.
- [`ThumbImageSource`](xref:Xamarin.Forms.Slider.ThumbImageSourceProperty) es la imagen que se utilizará para el control de posición de tipo [ `ImageSource` ](xref:Xamarin.Forms.ImageSource).

> [!NOTE]
> El `ThumbColor` y `ThumbImageSource` propiedades son mutuamente excluyentes. Si se establecen ambas propiedades, la `ThumbImageSource` propiedad tendrá prioridad.

## <a name="basic-slider-code-and-markup"></a>Marcado y código básico de control deslizante

El [ **SliderDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) ejemplo comienza con tres páginas que son funcionalmente idénticos, pero se implementan de maneras diferentes. La primera página usa solo código de C#, la segunda usa XAML con un controlador de eventos en el código y el tercero es para evitar el controlador de eventos mediante el uso de enlace de datos en el archivo XAML.

### <a name="creating-a-slider-in-code"></a>Creación de un control deslizante en el código

El **código básico de control deslizante** página en el [ **SliderDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) ejemplo muestra cómo crear un `Slider` y dos `Label` objetos en el código:

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

El `Slider` se inicializa para tener un `Maximum` propiedad de 360. El `ValueChanged` controlador de la `Slider` usa el `Value` propiedad de la `slider` objeto para establecer el `Rotation` propiedad de la primera `Label` y usa el `String.Format` método con el `NewValue` propiedad de la argumentos de evento para establecer el `Text` propiedad del segundo `Label`. Estos dos enfoques para obtener el valor actual de la `Slider` son intercambiables.

Este es el programa que se ejecutan en iOS, Android y plataforma Universal de Windows (UWP) dispositivos:

[![Código de control deslizante básica](slider-images/BasicSliderCode.png "código básico de control deslizante")](slider-images/BasicSliderCode-Large.png#lightbox)

El segundo `Label` muestra el texto "(no inicializado)" hasta que el `Slider` se manipula, lo que hace que la primera `ValueChanged` eventos se activen. Tenga en cuenta que el número de posiciones decimales que se muestran es diferente para cada plataforma. Estas diferencias están relacionadas con las implementaciones de la plataforma de la `Slider` y se describen más adelante en este artículo en la sección [diferencias de implementación de la plataforma](#implementations).

### <a name="creating-a-slider-in-xaml"></a>Creación de un control deslizante en XAML

El **XAML básica de control deslizante** página funcionalmente es igual a **código básico de control deslizante** pero se implementan principalmente en XAML:

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

El archivo de código subyacente contiene el controlador para el `ValueChanged` eventos:

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

También es posible que el controlador de eventos obtener el `Slider` que está desencadenando el evento a través de la `sender` argumento. El `Value` propiedad contiene el valor actual:

```csharp
double value = ((Slider)sender).Value;
```

Si el `Slider` objeto se asignó un nombre en el archivo XAML con un `x:Name` atributo (por ejemplo, "slider") y, a continuación, el controlador de eventos podría hacer referencia a ese objeto directamente:

```csharp
double value = slider.Value;
```

### <a name="data-binding-the-slider"></a>El control deslizante de enlace de datos

El **enlaces básicos de control deslizante** página muestra cómo escribir un programa casi equivalente que elimina la `Value` controlador de eventos mediante el uso de [enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md):

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

El `Rotation` propiedad de la primera `Label` está enlazado a la `Value` propiedad de la `Slider`, ya que es el `Text` propiedad del segundo `Label` con un `StringFormat` especificación. La página de **enlaces del control deslizante básico** funciona de forma ligeramente diferente a la de las dos páginas anteriores: Cuando la página aparece por primera vez, `Label` la segunda muestra la cadena de texto con el valor. Esta es una ventaja del uso de enlace de datos. Para mostrar texto sin el enlace de datos, deberá inicializar específicamente el `Text` propiedad de la `Label` o simular una activación de la `ValueChanged` eventos llamando al controlador de eventos desde el constructor de clase.

<a name="precautions" />

## <a name="precautions"></a>Precauciones

El valor de la `Minimum` propiedad siempre debe ser menor que el valor de la `Maximum` propiedad. Causas de fragmento de código siguiente el `Slider` para generar una excepción:

```csharp
// Throws an exception!
Slider slider = new Slider
{
    Minimum = 10,
    Maximum = 20
};
```

El compilador de C# genera código que establece estas dos propiedades en la secuencia, y cuándo el `Minimum` propiedad se establece en 10, es mayor que el valor predeterminado `Maximum` valor 1. Puede evitar la excepción en este caso, establezca el `Maximum` propiedad primera:

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

Establecer `Maximum` a 20 no es un problema porque es mayor que el valor predeterminado `Minimum` el valor 0. Cuando `Minimum` está establecido, el valor es menor que el `Maximum` valor de 20.

El mismo problema existe en XAML. Establecer las propiedades en un orden que garantiza que `Maximum` siempre es mayor que `Minimum`:

```xaml
<Slider Maximum="20"
        Minimum="10" ... />
```

Puede establecer el `Minimum` y `Maximum` valores para números negativos, pero solo en un orden donde `Minimum` es siempre menor que `Maximum`:

```xaml
<Slider Minimum="-20"
        Maximum="-10" ... />
```

El `Value` propiedad siempre es mayor o igual que el `Minimum` valor y menor o igual que `Maximum`. Si `Value` se establece en un valor fuera de ese intervalo, el valor se convertirán para que se encuentran dentro del intervalo, pero no se produce ninguna excepción. Por ejemplo, este código le *no* genere una excepción:

```csharp
Slider slider = new Slider
{
    Value = 10
};
```

En su lugar, el `Value` propiedad se convierte en el `Maximum` valor 1.

Este es un fragmento de código mostrado anteriormente:

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

Cuando `Minimum` se establece en 10, a continuación, `Value` también se establece en 10.

Si un `ValueChanged` se ha adjuntado el controlador de eventos en el momento en que el `Value` propiedad se convierte en algo distinto de su valor predeterminado de 0, entonces un `ValueChanged` desencadena el evento. Este es un fragmento de XAML:

```xaml
<Slider ValueChanged="OnSliderValueChanged"
        Maximum="20"
        Minimum="10" />
```

Cuando `Minimum` se establece en 10, `Value` también se establece en 10 y el `ValueChanged` desencadena el evento. Esto puede ocurrir antes de que se ha construido el resto de la página y el controlador puede intentar hacer referencia a otros elementos en la página que todavía no se han creado. Es posible que desea agregar algún código para el `ValueChanged` controlador que busca `null` valores de otros elementos en la página. O bien, puede establecer el `ValueChanged` controlador de eventos después de la `Slider` valores se hayan inicializado.

<a name="implementations" />

## <a name="platform-implementation-differences"></a>Diferencias de implementación de la plataforma

Las capturas de pantalla anterior muestran el valor de la `Slider` con un número diferente de cifras decimales. Esto se relaciona con cómo el `Slider` se implementa en las plataformas Android y UWP.

### <a name="the-android-implementation"></a>La implementación de Android

La implementación de Android `Slider` se basa en el Android [ `SeekBar` ](xref:Android.Widget.SeekBar) y siempre establece la [ `Max` ](xref:Android.Widget.ProgressBar.Max) propiedad a 1000. Esto significa que el `Slider` en Android tiene solo 1.001 valores discretos. Si establece el `Slider` para tener un `Minimum` 0 y un `Maximum` de 5.000, posteriormente, como el `Slider` se manipula el `Value` propiedad tiene valores de 0, 5, 10, 15 y así sucesivamente.

### <a name="the-uwp-implementation"></a>La implementación de UWP

La implementación de UWP de `Slider` se basa en la UWP [ `Slider` ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.slider) control. El `StepFrequency` propiedad de la UWP `Slider` se establece en la diferencia entre el `Maximum` y `Minimum` propiedades se dividen por 10, pero no mayor que 1.

Por ejemplo, para el intervalo predeterminado de 0 a 1, el `StepFrequency` propiedad está establecida en 0,1. Como el `Slider` se manipula el `Value` propiedad está restringida a 0, 0.1, 0.2, 0.3, 0,4, 0,5, 0.6, 0,7, 0,8, 0,9 y 1.0. (Esto es evidente en la última página en el [ **SliderDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) ejemplo.) Cuando la diferencia entre el `Maximum` y `Minimum` propiedades es 10 o superior, a continuación, `StepFrequency` está establecido en 1 y el `Value` propiedad tiene valores enteros.

### <a name="the-stepslider-solution"></a>La solución StepSlider

Más versátil `StepSlider` se describe en [capítulo 27. Los representadores personalizados](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf) del libro *Creating Mobile Apps with Xamarin.Forms*. El `StepSlider` es similar a `Slider` pero agrega un `Steps` propiedad para especificar el número de valores entre `Minimum` y `Maximum`.

## <a name="sliders-for-color-selection"></a>Controles deslizantes para selección de color

El último dos páginas en el [ **SliderDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) ejemplo usan tres `Slider` instancias para la selección de color. La primera página controla todas las interacciones en el archivo de código subyacente, mientras que la segunda página muestra cómo usar el enlace de datos con un modelo de vista.

### <a name="handling-sliders-in-the-code-behind-file"></a>Control de los controles deslizantes en el archivo de código subyacente

El **reguladores de Color RGB** crea una instancia de la página de un `BoxView` para mostrar un color, tres `Slider` instancias para seleccionar los componentes rojos, verde y azules del color y tres `Label` elementos para mostrar esos colores valores:

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

Un `Style` ofrece tres `Slider` elementos de un intervalo de 0 a 255. El `Slider` elementos comparten el mismo `ValueChanged` controlador, que se implementa en el archivo de código subyacente:

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

Los primeros conjuntos de sección la `Text` propiedad de uno de los `Label` instancias en una cadena de texto breve que indica el valor de la `Slider` en formato hexadecimal. A continuación, las tres `Slider` se tiene acceso a las instancias para crear un `Color` valor de los componentes RGB:

[![Controles deslizantes de Color RGB](slider-images/RgbColorSliders.png "reguladores de Color RGB")](slider-images/RgbColorSliders-Large.png#lightbox)

### <a name="binding-the-slider-to-a-viewmodel"></a>Enlazar el control deslizante a una clase ViewModel

El **reguladores de Color HSL** página muestra cómo usar un modelo de vista para realizar los cálculos utilizados para crear un `Color` valor a partir de los valores de matiz, saturación y luminosidad. Al igual que todos los modelos de vista, el `HSLColorViewModel` la clase implementa la `INotifyPropertyChanged` interfaz y se activa un `PropertyChanged` cada vez que cambia una de las propiedades de evento:

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

ViewModels y `INotifyPropertyChanged` interfaz se tratan en el artículo [enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md).

El **HslColorSlidersPage.xaml** crea una instancia de archivo del `HslColorViewModel` y lo establece en la página `BindingContext` propiedad. Esto permite que todos los elementos en el archivo XAML para enlazar a propiedades en ViewModel:

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

Como el `Slider` se manipulan elementos, el `BoxView` y `Label` elementos se actualizan desde el ViewModel:

[![Controles deslizantes de Color HSL](slider-images/HslColorSliders.png "reguladores de Color HSL")](slider-images/HslColorSliders-Large.png#lightbox)

El `StringFormat` componente de la `Binding` extensión de marcado está establecida para un formato de "F2" para mostrar dos posiciones decimales. (Cadena de formato en los enlaces de datos se describe en el artículo [aplicarles un formato](~/xamarin-forms/app-fundamentals/data-binding/string-formatting.md).) Sin embargo, la versión de UWP del programa se limita a los valores de 0, 0.1, 0.2... 0.9 y 1.0. Se trata de un resultado directo de la implementación de la UWP `Slider` como se describió anteriormente en la sección [diferencias de implementación de la plataforma](#implementations).

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de demostraciones de control deslizante](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)
- [API de control deslizante](xref:Xamarin.Forms.Slider)
