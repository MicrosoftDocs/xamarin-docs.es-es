---
title: Xamarin.Forms TimePicker
description: El TimePicker es una Xamarin.Forms vista que permite al usuario seleccionar una hora. En este artículo se explica cómo consumir un TimePicker en una Xamarin.Forms aplicación.
ms.prod: xamarin
ms.assetid: 2E99FB23-B82D-4EB4-AFB3-5002E736E7B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2f8452ea36d6fe376880b8882652c59fcb2ed23b
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371890"
---
# <a name="xamarinforms-timepicker"></a>Xamarin.Forms TimePicker

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)

_Una Xamarin.Forms vista que permite al usuario seleccionar una hora._

Xamarin.Forms [`TimePicker`](xref:Xamarin.Forms.TimePicker) Invoca el control de selector de tiempo de la plataforma y permite al usuario seleccionar una hora. `TimePicker` define las siguientes propiedades:

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) de tipo `TimeSpan` , la hora seleccionada, cuyo valor predeterminado es `TimeSpan` 0. El `TimeSpan` tipo indica una duración de tiempo desde la medianoche.
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) de tipo `string` , una cadena de formato .net [estándar](/dotnet/standard/base-types/standard-date-and-time-format-strings/) o [personalizada](/dotnet/standard/base-types/custom-date-and-time-format-strings/) , cuyo valor predeterminado es "t", el patrón de hora corta.
- [`TextColor`](xref:Xamarin.Forms.TimePicker.TextColor) de tipo [`Color`](xref:Xamarin.Forms.Color) , el color que se usa para mostrar la hora seleccionada, cuyo valor predeterminado es [`Color.Default`](xref:Xamarin.Forms.Color.Default) .
- [`FontAttributes`](xref:Xamarin.Forms.TimePicker.FontAttributes) de tipo [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) , cuyo valor predeterminado es [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None) .
- [`FontFamily`](xref:Xamarin.Forms.TimePicker.FontFamily) de tipo `string` , cuyo valor predeterminado es `null` .
- [`FontSize`](xref:Xamarin.Forms.TimePicker.FontSize) de tipo `double` , que tiene como valor predeterminado-1,0.
- `CharacterSpacing`, del tipo `double`, es el espaciado entre los caracteres del texto de `TimePicker`.

Todas estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que se pueden aplicar estilos y las propiedades pueden ser destinos de los enlaces de datos. La [`Time`](xref:Xamarin.Forms.TimePicker.Time) propiedad tiene un modo de enlace predeterminado de [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) , lo que significa que puede ser un destino de un enlace de datos en una aplicación que usa la arquitectura [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) .

[`TimePicker`](xref:Xamarin.Forms.TimePicker)No incluye un evento para indicar un nuevo valor seleccionado [`Time`](xref:Xamarin.Forms.TimePicker.Time) . Si necesita recibir una notificación de esto, puede Agregar un controlador para el [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) evento.

## <a name="initializing-the-time-property"></a>Inicializando la propiedad Time

En el código, puede inicializar la [`Time`](xref:Xamarin.Forms.TimePicker.Time) propiedad en un valor de tipo `TimeSpan` :

```csharp
TimePicker timePicker = new TimePicker
{
  Time = new TimeSpan(4, 15, 26) // Time set to "04:15:26"
};
```

Cuando la [`Time`](xref:Xamarin.Forms.TimePicker.Time) propiedad se especifica en XAML, el valor se convierte en `TimeSpan` y se valida para asegurarse de que el número de milisegundos sea mayor o igual que 0 y que el número de horas sea menor que 24. Los componentes de hora deben separarse con dos puntos:

```xaml
<TimePicker Time="4:15:26" />
```

Si la [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) propiedad de [`TimePicker`](xref:Xamarin.Forms.TimePicker) se establece en una instancia de un ViewModel que contiene una propiedad de tipo `TimeSpan` denominada `SelectedTime` (por ejemplo,), puede crear una instancia de de la `TimePicker` manera siguiente:

```xaml
<TimePicker Time="{Binding SelectedTime}" />
```

En este ejemplo, la [`Time`](xref:Xamarin.Forms.TimePicker.Time) propiedad se inicializa en la `SelectedTime` propiedad en el ViewModel. Dado `Time` que la propiedad tiene un modo de enlace de [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) , cualquier nueva hora que seleccione el usuario se propagará automáticamente al ViewModel.

Si no [`TimePicker`](xref:Xamarin.Forms.TimePicker) contiene un enlace en su [`Time`](xref:Xamarin.Forms.TimePicker.Time) propiedad, una aplicación debe adjuntar un controlador al [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) evento que se va a notificar cuando el usuario seleccione una nueva hora.

Para obtener información sobre cómo establecer las propiedades de la fuente, vea [fuentes](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="timepicker-and-layout"></a>TimePicker y diseño

Es posible utilizar una opción de diseño horizontal sin restricciones como `Center` , `Start` o `End` con [`TimePicker`](xref:Xamarin.Forms.TimePicker) :

```xaml
<TimePicker ···
            HorizontalOptions="Center"
            ··· />
```

Sin embargo, esto no es recomendable. Dependiendo del valor de la [`Format`](xref:Xamarin.Forms.TimePicker.Format) propiedad, las horas seleccionadas pueden requerir anchos de pantalla diferentes. Por ejemplo, la cadena de formato "T" hace que la [`TimePicker`](xref:Xamarin.Forms.TimePicker) vista muestre horas en un formato largo y "4:15:26 AM" requiere un ancho de pantalla mayor que el formato de hora corta ("T") de "4:15 AM". Dependiendo de la plataforma, esta diferencia podría hacer que la `TimePicker` vista cambie el ancho en el diseño o que la pantalla se trunque.

> [!TIP]
> Es preferible usar la configuración predeterminada `HorizontalOptions` de `Fill` con [`TimePicker`](xref:Xamarin.Forms.TimePicker) y no utilizar un ancho `Auto` cuando se coloca `TimePicker` en una `Grid` celda.

## <a name="timepicker-in-an-application"></a>TimePicker en una aplicación

En el ejemplo [**SetTimer**](/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) se incluyen las [`TimePicker`](xref:Xamarin.Forms.TimePicker) [`Entry`](xref:Xamarin.Forms.Entry) vistas, y [`Switch`](xref:Xamarin.Forms.Switch) en su página. Se `TimePicker` puede usar para seleccionar una hora y, cuando esa hora se produce, se muestra un cuadro de diálogo de alerta que recuerda al usuario del texto de `Entry` , siempre y cuando `Switch` se alterne. Este es el archivo XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SetTimer"
             x:Class="SetTimer.MainPage">
    <StackLayout>
        ...
        <Entry x:Name="_entry"
               Placeholder="Enter event to be reminded of" />
        <Label Text="Select the time below to be reminded at." />
        <TimePicker x:Name="_timePicker"
                    Time="11:00:00"
                    Format="T"
                    PropertyChanged="OnTimePickerPropertyChanged" />
        <StackLayout Orientation="Horizontal">
            <Label Text="Enable timer:" />
            <Switch x:Name="_switch"
                    HorizontalOptions="EndAndExpand"
                    Toggled="OnSwitchToggled" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

[`Entry`](xref:Xamarin.Forms.Entry)Permite escribir el texto del recordatorio que se mostrará cuando se produzca la hora seleccionada. [`TimePicker`](xref:Xamarin.Forms.TimePicker)Se le asigna una [`Format`](xref:Xamarin.Forms.TimePicker.Format) propiedad de "T" para el formato de hora larga. Tiene un controlador de eventos adjuntado al [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) evento y [`Switch`](xref:Xamarin.Forms.Switch) tiene un controlador asociado a su [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) evento. Estos controladores de eventos se encuentran en el archivo de código subyacente y llaman al `SetTriggerTime` método:

```csharp
public partial class MainPage : ContentPage
{
    DateTime _triggerTime;

    public MainPage()
    {
        InitializeComponent();

        Device.StartTimer(TimeSpan.FromSeconds(1), OnTimerTick);
    }

    bool OnTimerTick()
    {
        if (_switch.IsToggled && DateTime.Now >= _triggerTime)
        {
            _switch.IsToggled = false;
            DisplayAlert("Timer Alert", "The '" + _entry.Text + "' timer has elapsed", "OK");
        }
        return true;
    }

    void OnTimePickerPropertyChanged(object sender, PropertyChangedEventArgs args)
    {
        if (args.PropertyName == "Time")
        {
            SetTriggerTime();
        }
    }

    void OnSwitchToggled(object sender, ToggledEventArgs args)
    {
        SetTriggerTime();
    }

    void SetTriggerTime()
    {
        if (_switch.IsToggled)
        {
            _triggerTime = DateTime.Today + _timePicker.Time;
            if (_triggerTime < DateTime.Now)
            {
                _triggerTime += TimeSpan.FromDays(1);
            }
        }
    }
}
```

El `SetTriggerTime` método calcula un tiempo de temporizador basado en el `DateTime.Today` valor de propiedad y el `TimeSpan` valor devuelto de [`TimePicker`](xref:Xamarin.Forms.TimePicker) . Esto es necesario porque la `DateTime.Today` propiedad devuelve un `DateTime` que indica la fecha actual, pero con una hora de medianoche. Si el tiempo del temporizador ya se ha pasado hoy, se supone que es mañana.

El temporizador se marca cada segundo, ejecutando el `OnTimerTick` método que comprueba si [`Switch`](xref:Xamarin.Forms.Switch) está activado y si la hora actual es mayor o igual que el tiempo del temporizador. Cuando se produce la hora del temporizador, el [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) método presenta un cuadro de diálogo de alerta al usuario como recordatorio.

Cuando el ejemplo se ejecuta por primera vez, la [`TimePicker`](xref:Xamarin.Forms.TimePicker) vista se inicializa en 11. Al pulsar el se `TimePicker` invoca el selector de tiempo de plataforma. Las plataformas implementan el selector de tiempo de maneras muy diferentes, pero cada enfoque es familiar para los usuarios de esa plataforma:

[![Seleccionar hora](timepicker-images/timepicker-open.png "Seleccionar hora")](timepicker-images/timepicker-open-large.png#lightbox "Seleccionar hora")

> [!TIP]
> En Android, el [`TimePicker`](xref:Xamarin.Forms.TimePicker) cuadro de diálogo se puede personalizar invalidando el `CreateTimePickerDialog` método en un representador personalizado. Esto permite, por ejemplo, agregar botones adicionales al cuadro de diálogo.

Después de seleccionar una hora, la hora seleccionada se muestra en la [`TimePicker`](xref:Xamarin.Forms.TimePicker) :

[![Hora seleccionada](timepicker-images/timepicker-selected.png "Hora seleccionada")](timepicker-images/timepicker-selected-large.png#lightbox "Hora seleccionada")

Siempre que el [`Switch`](xref:Xamarin.Forms.Switch) se alterne a la posición activada, la aplicación muestra un cuadro de diálogo de alerta que recuerda al usuario del texto de [`Entry`](xref:Xamarin.Forms.Entry) cuando se produce la hora seleccionada:

[![Cuadro emergente del temporizador](timepicker-images/timer-test.png "Cuadro emergente del temporizador")](timepicker-images/timer-test-large.png#lightbox "Cuadro emergente del temporizador")

En cuanto se muestra el cuadro de diálogo de alerta, el [`Switch`](xref:Xamarin.Forms.Switch) se cambia a la posición de apagado.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de SetTimer](/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)
- [API de TimePicker](xref:Xamarin.Forms.TimePicker)