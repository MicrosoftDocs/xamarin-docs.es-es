---
title: Xamarin.Forms TimePicker
description: El TimePicker es una vista de Xamarin. Forms que permite al usuario seleccionar una hora. En este artículo se explica cómo consumir un TimePicker en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 2E99FB23-B82D-4EB4-AFB3-5002E736E7B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2018
ms.openlocfilehash: d5c4cc6600c8192718257abf4ef1cbec49c12eee
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656484"
---
# <a name="xamarinforms-timepicker"></a>Xamarin.Forms TimePicker

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)

_Una vista de Xamarin.Forms que permite al usuario seleccionar una hora._

Xamarin.Forms [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) invoca el control de selector de hora de la plataforma y permite al usuario seleccionar una hora. `TimePicker` define las siguientes propiedades:

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) de tipo `TimeSpan`, la hora seleccionada, cuyo valor predeterminado es un `TimeSpan` de 0. El `TimeSpan` tipo indica una duración de tiempo desde la medianoche.
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) de tipo `string`, un [estándar](/dotnet/standard/base-types/standard-date-and-time-format-strings/) o [personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings/) .NET formato de cadena, cuyo valor predeterminado es "t", el patrón de hora corta.
- [`TextColor`](xref:Xamarin.Forms.TimePicker.TextColor) de tipo [ `Color` ](xref:Xamarin.Forms.Color), el color utilizado para mostrar la hora seleccionada, cuyo valor predeterminado es [ `Color.Default` ](xref:Xamarin.Forms.Color.Default).
- [`FontAttributes`](xref:Xamarin.Forms.TimePicker.FontAttributes) de tipo [ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes), cuyo valor predeterminado es [ `FontAtributes.None` ](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.TimePicker.FontFamily) de tipo `string`, cuyo valor predeterminado es `null`.
- [`FontSize`](xref:Xamarin.Forms.TimePicker.FontSize) de tipo `double`, que de forma predeterminada va de -1,0.

Todas estas propiedades están respaldados por [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que puede cambiar el estilo y las propiedades pueden ser destinos de enlaces de datos. El [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) propiedad tiene un modo de enlace predeterminada de [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), lo que significa que puede ser un destino de enlace de datos en una aplicación que utiliza el [ Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) arquitectura.

El [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) no incluye un evento para indicar un nuevo seleccionado [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) valor. Si necesita recibir una notificación de esto, puede agregar un controlador para el [ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged) eventos.

## <a name="initializing-the-time-property"></a>Inicializando la propiedad de tiempo

En el código, se puede inicializar el [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) propiedad con un valor de tipo `TimeSpan`:

```csharp
TimePicker timePicker = new TimePicker
{
  Time = new TimeSpan(4, 15, 26) // Time set to "04:15:26"
};
```

Cuando el [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) se especifica la propiedad en XAML, el valor se convierte en un `TimeSpan` y validado para asegurarse de que el número de milisegundos es mayor o igual que 0 y que el número de horas es menor que 24. Los componentes de tiempo deben estar separados por signos de dos puntos:

```xaml
<TimePicker Time="4:15:26" />
```

Si el [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) propiedad de [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) está establecida en una instancia de un modelo de vista que contiene una propiedad de tipo `TimeSpan` denominado `SelectedTime` (por ejemplo), puede crear una instancia el `TimePicker` similar al siguiente:

```xaml
<TimePicker Time="{Binding SelectedTime}" />
```

En este ejemplo, el [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) propiedad se inicializa en el `SelectedTime` propiedad en el modelo de vista. Dado que el `Time` propiedad tiene un modo de enlace de [ `TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay)y en cualquier momento nuevo que el usuario selecciona se propagará automáticamente al ViewModel.

Si el [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) no contiene un enlace en su [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) propiedad, una aplicación debe adjuntar un controlador para el [ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged) eventos a Manténgase informado cuando el usuario selecciona uno nuevo.

Para obtener información sobre cómo establecer las propiedades de fuente, consulte [fuentes](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="timepicker-and-layout"></a>TimePicker y diseño

Es posible usar una opción de diseño horizontal sin restricciones, como `Center`, `Start`, o `End` con [ `TimePicker` ](xref:Xamarin.Forms.TimePicker):

```xaml
<TimePicker ···
            HorizontalOptions="Center"
            ··· />
```

Sin embargo, esto no se recomienda. Según la configuración de la [ `Format` ](xref:Xamarin.Forms.TimePicker.Format) propiedad, selecciona veces pueden requerir los anchos de pantalla diferente. Por ejemplo, la cadena de formato "T" hace que el [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) vista para mostrar veces en un formato largo y "4:15:26 A.M." requiere un mayor ancho de pantalla que el formato de hora corta ("t") de "4:15 A.M.". Según la plataforma, esta diferencia puede provocar la `TimePicker` vista para cambiar el ancho de diseño o en la presentación se trunque.

> [!TIP]
> Es mejor usar el valor predeterminado `HorizontalOptions` de `Fill` con [ `TimePicker` ](xref:Xamarin.Forms.TimePicker)y no se debe utilizar un ancho de `Auto` al poner `TimePicker` en un `Grid` celda.

## <a name="timepicker-in-an-application"></a>TimePicker en una aplicación

El [ **SetTimer** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) ejemplo incluye [ `TimePicker` ](xref:Xamarin.Forms.TimePicker), [ `Entry` ](xref:Xamarin.Forms.Entry), y [ `Switch` ](xref:Xamarin.Forms.Switch) vistas en su página. El `TimePicker` se puede usar para seleccionar una hora y, al tiempo que se produce se muestra un cuadro de diálogo de alerta que avisa al usuario del texto en el `Entry`, que proporciona el `Switch` se alterna. Este es el archivo XAML:

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

El [ `Entry` ](xref:Xamarin.Forms.Entry) permite escribir el texto de recordatorio que se mostrará cuando se produce el tiempo seleccionado. El [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) se asigna un [ `Format` ](xref:Xamarin.Forms.TimePicker.Format) propiedad de "T" para el formato de hora larga. Tiene un controlador de eventos asociado a la [ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged) eventos y el [ `Switch` ](xref:Xamarin.Forms.Switch) ha adjuntado un controlador a su [ `Toggled` ](xref:Xamarin.Forms.Switch.Toggled) eventos. Estos controladores de eventos están en el archivo de código subyacente y llamar a la `SetTriggerTime` método:

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

El `SetTriggerTime` método calcula un tiempo de temporizador en función de la `DateTime.Today` valor de propiedad y el `TimeSpan` valor devuelto desde el [ `TimePicker` ](xref:Xamarin.Forms.TimePicker). Esto es necesario porque el `DateTime.Today` propiedad devuelve un `DateTime` que indica la fecha actual, pero con una hora de medianoche. Si ya ha transcurrido el tiempo de temporizador de hoy en día, se supone que mañana.

Tics del temporizador cada segundo, ejecuta el `OnTimerTick` método que comprueba si el [ `Switch` ](xref:Xamarin.Forms.Switch) es on y si la hora actual es mayor que o igual que el tiempo del temporizador. Cuando se produce el tiempo del temporizador, el [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*) método presenta un cuadro de diálogo de alerta al usuario como un recordatorio.

Cuando el ejemplo se ejecuta en primer lugar, el [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) vista se inicializa en 11 am. Pulsar el `TimePicker` invoca el selector de hora de la plataforma. Las plataformas de implementan el selector de hora de maneras muy diferentes, pero cada enfoque es familiar para los usuarios de esa plataforma:

[![Seleccione tiempo](timepicker-images/timepicker-open.png "seleccione hora")](timepicker-images/timepicker-open-large.png#lightbox "seleccionar tiempo")

> [!TIP]
> En Android, el [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) cuadro de diálogo se puede personalizar invalidando el `CreateTimePickerDialog` método en un representador personalizado. Esto permite, por ejemplo, botones adicionales que se agregan al cuadro de diálogo.

Después de seleccionar una hora, se muestra la hora seleccionada en el [ `TimePicker` ](xref:Xamarin.Forms.TimePicker):

[![Tiempo seleccionado](timepicker-images/timepicker-selected.png "tiempo seleccionado")](timepicker-images/timepicker-selected-large.png#lightbox "tiempo seleccionado")

Siempre que el [ `Switch` ](xref:Xamarin.Forms.Switch) se alterna en la posición, la aplicación muestra un cuadro de diálogo de alerta recuerda al usuario del texto en el [ `Entry` ](xref:Xamarin.Forms.Entry) cuando se produce la hora seleccionada:

[![Menú emergente de temporizador](timepicker-images/timer-test.png "temporizador emergente")](timepicker-images/timer-test-large.png#lightbox "emergente de temporizador")

Tan pronto como se muestra el cuadro de diálogo de alerta, el [ `Switch` ](xref:Xamarin.Forms.Switch) cambia a la posición de apagado.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de SetTimer](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)
- [TimePicker API](xref:Xamarin.Forms.TimePicker)
