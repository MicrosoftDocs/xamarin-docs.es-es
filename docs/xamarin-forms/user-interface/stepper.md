---
title: Xamarin.FormsHacer
description: El Xamarin.Forms stepper permite al usuario seleccionar un valor numérico de un intervalo de valores. Consta de dos botones etiquetados con signos menos y más. La manipulación de los dos botones cambia el valor seleccionado incrementalmente.
ms.prod: xamarin
ms.assetid: 62571B3E-D84B-4F52-9FC7-C105D6733B16
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4f071530fb17de44d8ede786ca1b42f5e11f4f7c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84130551"
---
# <a name="xamarinforms-stepper"></a>Xamarin.FormsHacer

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)

_Use un stepper para seleccionar un valor numérico de un intervalo de valores._

Xamarin.Forms [`Stepper`](xref:Xamarin.Forms.Stepper) Consta de dos botones etiquetados con signos menos y más. El usuario puede manipular estos botones para seleccionar incrementalmente un `double` valor de un intervalo de valores.

[`Stepper`](xref:Xamarin.Forms.Stepper)Define cuatro propiedades de tipo `double` :

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment)es la cantidad por la que se va a cambiar el valor seleccionado, con un valor predeterminado de 1.
- [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum)es el mínimo del intervalo, con un valor predeterminado de 0.
- [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum)es el máximo del intervalo, con un valor predeterminado de 100.
- [`Value`](xref:Xamarin.Forms.Stepper.Value)es el valor del stepper, que puede oscilar entre `Minimum` y `Maximum` y tiene un valor predeterminado de 0.

Todas estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos. La [`Value`](xref:Xamarin.Forms.Stepper.Value) propiedad tiene un modo de enlace predeterminado de [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) , lo que significa que es adecuado como origen de enlace en una aplicación que usa la arquitectura [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) .

> [!WARNING]
> Internamente, [`Stepper`](xref:Xamarin.Forms.Stepper) garantiza que [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) es menor que [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) . Si `Minimum` o `Maximum` se establecen en cualquier momento para que `Minimum` no sea menor que `Maximum` , se produce una excepción. Para obtener más información sobre cómo establecer las `Minimum` `Maximum` propiedades y, consulte la sección [precauciones](#precautions) .

[`Stepper`](xref:Xamarin.Forms.Stepper)Convierte la propiedad de [`Value`](xref:Xamarin.Forms.Stepper.Value) modo que esté entre [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) y [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) , ambos incluidos. Si la `Minimum` propiedad se establece en un valor mayor que la `Value` propiedad, `Stepper` establece la `Value` propiedad en `Minimum` . Del mismo modo, si `Maximum` se establece en un valor menor que `Value` , `Stepper` establece la `Value` propiedad en `Maximum` .

[`Stepper`](xref:Xamarin.Forms.Stepper)define un [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) evento que se desencadena cuando [`Value`](xref:Xamarin.Forms.Stepper.Value) cambia, ya sea a través de la manipulación del usuario `Stepper` o cuando la aplicación establece la `Value` propiedad directamente. `ValueChanged`También se desencadena un evento cuando la `Value` propiedad se convierte como se describe en el párrafo anterior.

El [`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs) objeto que acompaña al [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) evento tiene dos propiedades, ambas de tipo `double` : [`OldValue`](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue) y [`NewValue`](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue) . En el momento en que se desencadena el evento, el valor de `NewValue` es igual que la [`Value`](xref:Xamarin.Forms.Stepper.Value) propiedad del [`Stepper`](xref:Xamarin.Forms.Stepper) objeto.

## <a name="basic-stepper-code-and-markup"></a>Código y marcado de stepper básico

El ejemplo [**StepperDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) contiene tres páginas que son funcionalmente idénticas, pero se implementan de maneras diferentes. En la primera página solo se usa código C#, el segundo usa XAML con un controlador de eventos en el código y el tercero puede evitar el controlador de eventos mediante el enlace de datos en el archivo XAML.

### <a name="creating-a-stepper-in-code"></a>Creación de un stepper en el código

La página de **códigos de stepper Básica** en el ejemplo [**StepperDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) muestra cómo crear un [`Stepper`](xref:Xamarin.Forms.Stepper) y dos [`Label`](xref:Xamarin.Forms.Label) objetos en el código:

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

[`Stepper`](xref:Xamarin.Forms.Stepper)Se inicializa para tener una [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) propiedad de 360 y una [`Increment`](xref:Xamarin.Forms.Stepper.Increment) propiedad de 30. La manipulación de `Stepper` cambia el valor seleccionado incrementalmente entre [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) a `Maximum` basándose en el valor de la `Increment` propiedad. El [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) controlador de `Stepper` utiliza la [`Value`](xref:Xamarin.Forms.Stepper.Value) propiedad del `stepper` objeto para establecer la [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propiedad de la primera [`Label`](xref:Xamarin.Forms.Label) y usa el `string.Format` método con la `NewValue` propiedad de los argumentos del evento para establecer la [`Text`](xref:Xamarin.Forms.Label.Text) propiedad de la segunda `Label` . Estos dos enfoques para obtener el valor actual de `Stepper` son intercambiables.

Las capturas de pantallas siguientes muestran la página de **códigos de stepper Básica** :

[![Código de stepper básico](stepper-images/basic-stepper-code.png "Código de stepper básico")](stepper-images/basic-stepper-code-large.png#lightbox)

En el segundo [`Label`](xref:Xamarin.Forms.Label) se muestra el texto "(no inicializado)" hasta que [`Stepper`](xref:Xamarin.Forms.Stepper) se manipula, lo que hace que se desencadene el primer [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) evento.

### <a name="creating-a-stepper-in-xaml"></a>Crear un stepper en XAML

La página **XAML de stepper Básica** es funcionalmente igual que el **código de stepper básico** , pero se implementa principalmente en XAML:

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

El archivo de código subyacente contiene el controlador del [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) evento:

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

También es posible que el controlador de eventos obtenga el [`Stepper`](xref:Xamarin.Forms.Stepper) que está desencadenando el evento a través del `sender` argumento. La [`Value`](xref:Xamarin.Forms.Stepper.Value) propiedad contiene el valor actual:

```csharp
double value = ((Stepper)sender).Value;
```

Si al [`Stepper`](xref:Xamarin.Forms.Stepper) objeto se le asignó un nombre en el archivo XAML con un `x:Name` atributo (por ejemplo, "stepper"), el controlador de eventos podría hacer referencia a ese objeto directamente:

```csharp
double value = stepper.Value;
```

### <a name="data-binding-the-stepper"></a>Enlazar datos al stepper

La página de **enlaces de stepper Básica** muestra cómo escribir una aplicación casi equivalente que elimina el [`Value`](xref:Xamarin.Forms.Stepper.Value) controlador de eventos mediante el [enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md):

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

La [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propiedad de la primera [`Label`](xref:Xamarin.Forms.Label) se enlaza a la [`Value`](xref:Xamarin.Forms.Stepper.Value) propiedad de [`Stepper`](xref:Xamarin.Forms.Stepper) , como es la [`Text`](xref:Xamarin.Forms.Label.Text) propiedad del segundo `Label` con una `StringFormat` especificación. La página de **enlaces de stepper Básica** funciona de forma ligeramente diferente a la de las dos páginas anteriores: cuando la página aparece por primera vez, la segunda `Label` muestra la cadena de texto con el valor. Esta es una de las ventajas de usar el enlace de datos. Para mostrar texto sin enlace de datos, tendría que inicializar específicamente la `Text` propiedad de `Label` o simular una activación del evento llamando [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) al controlador de eventos desde el constructor de clase.

## <a name="precautions"></a>PRECAUCIONES

El valor de la [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) propiedad siempre debe ser menor que el valor de la [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) propiedad. El fragmento de código siguiente hace que [`Stepper`](xref:Xamarin.Forms.Stepper) genere una excepción:

```csharp
// Throws an exception!
Stepper stepper = new Stepper
{
    Minimum = 180,
    Maximum = 360
};
```

El compilador de C# genera código que establece estas dos propiedades en secuencia y, cuando la [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) propiedad se establece en 180, es mayor que el [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) valor predeterminado de 100. Puede evitar la excepción en este caso estableciendo `Maximum` primero la propiedad:

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

Establecer [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) en 360 no es un problema porque es mayor que el valor predeterminado [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) de 0. Cuando `Minimum` se establece, el valor es menor que el `Maximum` valor de 360.

El mismo problema existe en XAML. Establezca las propiedades en un orden que garantice que [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) siempre sea mayor que `Minimum` :

```xaml
<Stepper Maximum="360"
         Minimum="180" ... />
```

Puede establecer los [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) valores y [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) en números negativos, pero solo en un orden en el que `Minimum` siempre sea menor que `Maximum` :

```xaml
<Stepper Minimum="-360"
         Maximum="-180" ... />
```

La [`Value`](xref:Xamarin.Forms.Stepper.Value) propiedad siempre es mayor o igual que el [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) valor y menor o igual que [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) . Si `Value` se establece en un valor fuera de ese intervalo, el valor se convertirá para que se encuentre dentro del intervalo, pero no se genera ninguna excepción. Por ejemplo, este código *no* producirá una excepción:

```csharp
Stepper stepper = new Stepper
{
    Value = 180
};
```

En su lugar, la [`Value`](xref:Xamarin.Forms.Stepper.Value) propiedad se convierte en el [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) valor de 100.

A continuación se muestra un fragmento de código mostrado anteriormente:

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

Cuando [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) se establece en 180, [`Value`](xref:Xamarin.Forms.Stepper.Value) también se establece en 180.

Si se ha [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) adjuntado un controlador de eventos en el momento en que la [`Value`](xref:Xamarin.Forms.Stepper.Value) propiedad se convierte en algo distinto de su valor predeterminado de 0, `ValueChanged` se desencadena un evento. A continuación se muestra un fragmento de código XAML:

```xaml
<Stepper ValueChanged="OnStepperValueChanged"
         Maximum="360"
         Minimum="180" />
```

Cuando [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) se establece en 180, [`Value`](xref:Xamarin.Forms.Stepper.Value) también se establece en 180 y [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) se desencadena el evento. Esto puede ocurrir antes de que se haya construido el resto de la página y el controlador podría intentar hacer referencia a otros elementos de la página que aún no se han creado. Es posible que desee agregar código al `ValueChanged` controlador que compruebe `null` los valores de otros elementos de la página. O bien, puede establecer el `ValueChanged` controlador de eventos después de [`Stepper`](xref:Xamarin.Forms.Stepper) inicializar los valores.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de demostraciones de stepper](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)
- [API de stepper](xref:Xamarin.Forms.Stepper)
