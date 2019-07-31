---
title: Motor Xamarin.Forms paso a paso
description: El motor Xamarin.Forms paso a paso permite al usuario seleccionar un valor numérico de un intervalo de valores. Consta de dos botones etiquetados con menos y signos más. Manipulación de los dos botones cambia el valor seleccionado de forma incremental.
ms.prod: xamarin
ms.assetid: 62571B3E-D84B-4F52-9FC7-C105D6733B16
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/17/2018
ms.openlocfilehash: 6c89f04b1d1d87fed8d86d50cb68527391a7f317
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656203"
---
# <a name="xamarinforms-stepper"></a>Motor Xamarin.Forms paso a paso

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)

_Use un motor paso a paso para seleccionar un valor numérico de un intervalo de valores._

Xamarin.Forms [ `Stepper` ](xref:Xamarin.Forms.Stepper) consta de dos botones etiquetados con menos y signos más. Estos botones pueden ser manipulados por el usuario para seleccionar de forma incremental un `double` valor desde un intervalo de valores.

El [ `Stepper` ](xref:Xamarin.Forms.Stepper) define cuatro propiedades de tipo `double`:

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment) es la cantidad para cambiar el valor seleccionado, su valor predeterminado de 1.
- [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) es el mínimo del intervalo, con un valor predeterminado de 0.
- [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) es el máximo del intervalo, con un valor predeterminado de 100.
- [`Value`](xref:Xamarin.Forms.Stepper.Value) es el valor del componente, que puede oscilar entre `Minimum` y `Maximum` y tiene un valor predeterminado de 0.

Todas estas propiedades están respaldados por [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos. El [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propiedad tiene un modo de enlace predeterminada de [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), lo que significa que es adecuado como origen de enlace en una aplicación que utiliza el [ Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) arquitectura.

> [!WARNING]
> Internamente, el [ `Stepper` ](xref:Xamarin.Forms.Stepper) garantiza que [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) es menor que [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum). Si `Minimum` o `Maximum` nunca se establecen para que `Minimum` es menos `Maximum`, se produce una excepción. Para obtener más información sobre cómo el `Minimum` y `Maximum` propiedades, consulte [precauciones](#precautions) sección.

El [ `Stepper` ](xref:Xamarin.Forms.Stepper) convierte la [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propiedad para que esté entre [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) y [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum), ambos inclusive. Si el `Minimum` propiedad se establece en un valor mayor que el `Value` propiedad, el `Stepper` establece la `Value` propiedad `Minimum`. De forma similar, si `Maximum` está establecido en un valor menor que `Value`, a continuación, `Stepper` establece la `Value` propiedad a `Maximum`.

[`Stepper`](xref:Xamarin.Forms.Stepper) define un [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) evento que se desencadena cuando el [ `Value` ](xref:Xamarin.Forms.Stepper.Value) cambios, ya sea a través de la manipulación del usuario de la `Stepper` o cuando la aplicación establece el `Value` propiedad directamente. Un `ValueChanged` evento se desencadena cuando el `Value` propiedad se convierte como se describe en el párrafo anterior.

El [ `ValueChangedEventArgs` ](xref:Xamarin.Forms.ValueChangedEventArgs) objeto que acompaña a la [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) eventos tiene dos propiedades, ambos de tipo `double`: [ `OldValue` ](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue) y [ `NewValue`](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue). En el momento en el evento se desencadena, el valor de `NewValue` es el mismo que el [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propiedad de la [ `Stepper` ](xref:Xamarin.Forms.Stepper) objeto.

## <a name="basic-stepper-code-and-markup"></a>Marcado y código básico de motor paso a paso

El [ **StepperDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) ejemplo contiene tres páginas que son funcionalmente idénticos, pero se implementan de maneras diferentes. La primera página se usa solo C# código, el segundo usa XAML con un controlador de eventos en código y la tercera es capaz de evitar el controlador de eventos mediante el uso de enlace de datos en el archivo XAML.

### <a name="creating-a-stepper-in-code"></a>Creación de un motor paso a paso en el código

El **motor paso a paso de código básico** página en el [ **StepperDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) ejemplo muestra cómo crear un [ `Stepper` ](xref:Xamarin.Forms.Stepper) y dos [ `Label` ](xref:Xamarin.Forms.Label) objetos en el código:

```csharp
public class BasicStepperCodePage : ContentPage
{
    public BasicStepperCodePage()
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

        Stepper stepper = new Stepper
        {
            Maximum = 360,
            Increment = 30,
            HorizontalOptions = LayoutOptions.Center
        };
        stepper.ValueChanged += (sender, e) =>
        {
            rotationLabel.Rotation = stepper.Value;
            displayLabel.Text = string.Format("The Stepper value is {0}", e.NewValue);
        };

        Title = "Basic Stepper Code";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = { rotationLabel, stepper, displayLabel }
        };
    }
}
```

El [ `Stepper` ](xref:Xamarin.Forms.Stepper) se inicializa para tener un [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) propiedad de 360 y un [ `Increment` ](xref:Xamarin.Forms.Stepper.Increment) propiedad de 30. Manipular el `Stepper` cambia el valor seleccionado de forma incremental entre [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) a `Maximum` según el valor de la `Increment` propiedad. El [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) controlador de la `Stepper` usa el [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propiedad de la `stepper` objeto para establecer el [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propiedad de la primera [ `Label` ](xref:Xamarin.Forms.Label) y usa el `string.Format` método con el `NewValue` propiedad de los argumentos de evento para establecer el [ `Text` ](xref:Xamarin.Forms.Label.Text) propiedad de la segundo `Label`. Estos dos enfoques para obtener el valor actual de la `Stepper` son intercambiables.

Capturas de pantalla siguientes se muestra el **motor paso a paso de código básico** página:

[![Motor básico paso a paso código](stepper-images/basic-stepper-code.png "código motor paso a paso básica")](stepper-images/basic-stepper-code-large.png#lightbox)

El segundo [ `Label` ](xref:Xamarin.Forms.Label) muestra el texto "(no inicializado)" hasta que el [ `Stepper` ](xref:Xamarin.Forms.Stepper) se manipula, lo que hace que la primera [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) eventos en activarse.

### <a name="creating-a-stepper-in-xaml"></a>Creación de un motor paso a paso en XAML

El **XAML básica de motor paso a paso** página funcionalmente es igual a **motor paso a paso de código básico** pero se implementan principalmente en XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StepperDemo.BasicStepperXAMLPage"
             Title="Basic Stepper XAML">
    <StackLayout Margin="20">
        <Label x:Name="_rotatingLabel"
               Text="ROTATING TEXT"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
        <Stepper Maximum="360"
                 Increment="30"
                 HorizontalOptions="Center"
                 ValueChanged="OnStepperValueChanged" />
        <Label x:Name="_displayLabel"
               Text="(uninitialized)"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />        
    </StackLayout>
</ContentPage>
```

El archivo de código subyacente contiene el controlador para el [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) eventos:

```csharp
public partial class BasicStepperXAMLPage : ContentPage
{
    public BasicStepperXAMLPage()
    {
        InitializeComponent();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs e)
    {
        double value = e.NewValue;
        _rotatingLabel.Rotation = value;
        _displayLabel.Text = string.Format("The Stepper value is {0}", value);
    }
}
```

También es posible que el controlador de eventos obtener el [ `Stepper` ](xref:Xamarin.Forms.Stepper) que está desencadenando el evento a través de la `sender` argumento. El [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propiedad contiene el valor actual:

```csharp
double value = ((Stepper)sender).Value;
```

Si el [ `Stepper` ](xref:Xamarin.Forms.Stepper) objeto se asignó un nombre en el archivo XAML con un `x:Name` atributo (por ejemplo, "componente") y, a continuación, el controlador de eventos podría hacer referencia a ese objeto directamente:

```csharp
double value = stepper.Value;
```

### <a name="data-binding-the-stepper"></a>El motor paso a paso de enlace de datos

El **enlaces básicos de motor paso a paso** página muestra cómo escribir una aplicación casi equivalente que elimina la [ `Value` ](xref:Xamarin.Forms.Stepper.Value) controlador de eventos mediante el uso de [enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StepperDemo.BasicStepperBindingsPage"
             Title="Basic Stepper Bindings">
    <StackLayout Margin="20">
        <Label Text="ROTATING TEXT"
               Rotation="{Binding Source={x:Reference _stepper}, Path=Value}"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
        <Stepper x:Name="_stepper"
                 Maximum="360"
                 Increment="30"
                 HorizontalOptions="Center" />
        <Label Text="{Binding Source={x:Reference _stepper}, Path=Value, StringFormat='The Stepper value is {0:F0}'}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

El [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propiedad de la primera [ `Label` ](xref:Xamarin.Forms.Label) está enlazado a la [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propiedad de la [ `Stepper` ](xref:Xamarin.Forms.Stepper), ya que es el [ `Text` ](xref:Xamarin.Forms.Label.Text) propiedad del segundo `Label` con un `StringFormat` especificación. La página de **enlaces de stepper Básica** funciona de forma ligeramente diferente a la de las dos páginas anteriores: Cuando la página aparece por primera vez, `Label` la segunda muestra la cadena de texto con el valor. Esta es una ventaja del uso de enlace de datos. Para mostrar texto sin el enlace de datos, deberá inicializar específicamente el `Text` propiedad de la `Label` o simular una activación de la [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) eventos llamando al controlador de eventos desde el constructor de clase .

## <a name="precautions"></a>Precauciones

El valor de la [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) propiedad siempre debe ser menor que el valor de la [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) propiedad. Causas de fragmento de código siguiente el [ `Stepper` ](xref:Xamarin.Forms.Stepper) para generar una excepción:

```csharp
// Throws an exception!
Stepper stepper = new Stepper
{
    Minimum = 180,
    Maximum = 360
};
```

El C# compilador genera código que establece estas dos propiedades en la secuencia, y cuándo el [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) propiedad está establecida en 180, es mayor que el valor predeterminado [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) valor de 100. Puede evitar la excepción en este caso, establezca el `Maximum` propiedad primera:

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

Establecer [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) a 360 no es un problema porque es mayor que el valor predeterminado [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) el valor 0. Cuando `Minimum` está establecido, el valor es menor que el `Maximum` valor de 360.

El mismo problema existe en XAML. Establecer las propiedades en un orden que garantiza que [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) siempre es mayor que `Minimum`:

```xaml
<Stepper Maximum="360"
         Minimum="180" ... />
```

Puede establecer el [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) y [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) valores para números negativos, pero solo en un orden donde `Minimum` es siempre menor que `Maximum`:

```xaml
<Stepper Minimum="-360"
         Maximum="-180" ... />
```

El [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propiedad siempre es mayor o igual que el [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) valor y menor o igual a [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum). Si `Value` se establece en un valor fuera de ese intervalo, el valor se convertirán para que se encuentran dentro del intervalo, pero no se produce ninguna excepción. Por ejemplo, este código le *no* genere una excepción:

```csharp
Stepper stepper = new Stepper
{
    Value = 180
};
```

En su lugar, el [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propiedad se convierte en el [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) valor de 100.

Este es un fragmento de código mostrado anteriormente:

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

Cuando [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) está establecido en 180, a continuación, [ `Value` ](xref:Xamarin.Forms.Stepper.Value) también está establecido en 180.

Si un [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) se ha adjuntado el controlador de eventos en el momento en que el [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propiedad se convierte en algo distinto de su valor predeterminado de 0, entonces un `ValueChanged` desencadena el evento. Este es un fragmento de XAML:

```xaml
<Stepper ValueChanged="OnStepperValueChanged"
         Maximum="360"
         Minimum="180" />
```

Cuando [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) está establecido en 180, [ `Value` ](xref:Xamarin.Forms.Stepper.Value) también está establecido en 180 y el [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) desencadena el evento. Esto puede ocurrir antes de que se ha construido el resto de la página y el controlador puede intentar hacer referencia a otros elementos en la página que todavía no se han creado. Es posible que desea agregar algún código para el `ValueChanged` controlador que busca `null` valores de otros elementos en la página. O bien, puede establecer el `ValueChanged` controlador de eventos después de la [ `Stepper` ](xref:Xamarin.Forms.Stepper) valores se hayan inicializado.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de demostraciones de motor paso a paso](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)
- [API de motor paso a paso](xref:Xamarin.Forms.Stepper)
