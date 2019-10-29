---
title: Selector de fecha
description: Seleccionar fechas del calendario mediante DatePickerDialog y DialogFragment
ms.prod: xamarin
ms.assetid: F2BCD8D4-8957-EA53-C5A8-6BB603ADB47B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/22/2018
ms.openlocfilehash: b54a0795dee0bd7a02b419da497f9a1b3f3ee7ff
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029215"
---
# <a name="android-date-picker"></a>Selector de fecha de Android

## <a name="overview"></a>Información general

Hay ocasiones en las que un usuario debe introducir datos en una aplicación Android. Para ayudarlo, el marco de trabajo de Android proporciona el widget de [`DatePicker`](xref:Android.Widget.DatePicker) y el [`DatePickerDialog`](xref:Android.App.DatePickerDialog) . El `DatePicker` permite a los usuarios seleccionar el año, el mes y el día en una interfaz coherente entre dispositivos y aplicaciones. El `DatePickerDialog` es una clase auxiliar que encapsula el `DatePicker` en un cuadro de diálogo.

Las aplicaciones modernas de Android deben mostrar el `DatePickerDialog` en un [`DialogFragment`](xref:Android.App.DialogFragment). Esto permitirá que una aplicación muestre el DatePicker como un cuadro de diálogo emergente o incrustado en una actividad. Además, el `DialogFragment` administrará el ciclo de vida y la presentación del cuadro de diálogo, lo que reduce la cantidad de código que se debe implementar.

En esta guía se muestra cómo usar el `DatePickerDialog`, encapsulado en un `DialogFragment`. La aplicación de ejemplo mostrará el `DatePickerDialog` como un cuadro de diálogo modal cuando el usuario haga clic en un botón de una actividad. Cuando el usuario establece la fecha, un `TextView` se actualizará con la fecha seleccionada.

[![captura de pantalla del botón seleccionar fecha seguido del cuadro de diálogo Selector de fecha](date-picker-images/image-01-sml.png)](date-picker-images/image-01.png#lightbox)

## <a name="requirements"></a>Requisitos

La aplicación de ejemplo de esta guía tiene como destino Android 4,1 (nivel de API).
16) o superior, pero es aplicable a Android 3,0 (nivel de API 11 o superior). Es posible admitir versiones anteriores de Android con la incorporación de la biblioteca de compatibilidad de Android V4 al proyecto y algunos cambios de código.

## <a name="using-the-datepicker"></a>Usar DatePicker

Este ejemplo extenderá `DialogFragment`. La subclase hospedará y mostrará un `DatePickerDialog`:

![Primer plano cuadro de diálogo de selector de fecha](date-picker-images/image-02.png)

Cuando el usuario selecciona una fecha y hace clic en el botón **Aceptar** , el `DatePickerDialog` llamará al método [`IOnDateSetListener.OnDateSet`](xref:Android.App.DatePickerDialog.IOnDateSetListener.OnDateSet*).
La `DialogFragment`de hospedaje implementa esta interfaz. Si el usuario hace clic en el botón **Cancelar** , el fragmento y el cuadro de diálogo se descartarán por sí mismos.

Hay varias maneras en las que `DialogFragment` puede devolver la fecha seleccionada a la actividad de hospedaje:

1. **Invocar un método o establecer una propiedad** &ndash; la actividad puede proporcionar una propiedad o un método específicamente para establecer este valor.

2. **Genera un evento** &ndash; el `DialogFragment` puede definir un evento que se generará cuando se invoque `OnDateSet`.

3. **Use un `Action`** &ndash; la `DialogFragment` puede invocar un `Action<DateTime>` para mostrar la fecha en la actividad. La actividad proporcionará el `Action<DateTime` al crear instancias del `DialogFragment`. En este ejemplo se utiliza la tercera técnica y se requiere que la actividad proporcione una `Action<DateTime>` a la `DialogFragment`.

### <a name="extending-dialogfragment"></a>Extender DialogFragment

El primer paso para mostrar un `DatePickerDialog` es crear una subclase `DialogFragment` y hacer que implemente la interfaz `IOnDateSetListener`:

```csharp
public class DatePickerFragment : DialogFragment, 
                                  DatePickerDialog.IOnDateSetListener
{
    // TAG can be any string of your choice.
    public static readonly string TAG = "X:" + typeof (DatePickerFragment).Name.ToUpper();
    
    // Initialize this value to prevent NullReferenceExceptions.
    Action<DateTime> _dateSelectedHandler = delegate { };
    
    public static DatePickerFragment NewInstance(Action<DateTime> onDateSelected)
    {
        DatePickerFragment frag = new DatePickerFragment();
        frag._dateSelectedHandler = onDateSelected;
        return frag;
    }
    
    public override Dialog OnCreateDialog(Bundle savedInstanceState)
    {
        DateTime currently = DateTime.Now;
        DatePickerDialog dialog = new DatePickerDialog(Activity, 
                                                       this, 
                                                       currently.Year, 
                                                       currently.Month - 1,
                                                       currently.Day);
        return dialog;
    }
    
    public void OnDateSet(DatePicker view, int year, int monthOfYear, int dayOfMonth)
    {
        // Note: monthOfYear is a value between 0 and 11, not 1 and 12!
        DateTime selectedDate = new DateTime(year, monthOfYear + 1, dayOfMonth);
        Log.Debug(TAG, selectedDate.ToLongDateString());
        _dateSelectedHandler(selectedDate);
    }
}
```

Se invoca al método `NewInstance` para crear una instancia de un nuevo `DatePickerFragment`. Este método toma un `Action<DateTime>` que se invocará cuando el usuario haga clic en el botón **Aceptar** en el `DatePickerDialog`.

Cuando se va a mostrar el fragmento, Android llamará al método `OnCreateDialog`. Este método creará un nuevo objeto `DatePickerDialog` e inicializarlo con la fecha actual y el objeto de devolución de llamada (que es la instancia actual del `DatePickerFragment`).

> [!NOTE]
> Tenga en cuenta que el valor del mes en el que se invoca `IOnDateSetListener.OnDateSet` está en el intervalo comprendido entre 0 y 11, y no de 1 a 12. El día del mes estará en el intervalo de 1 a 31 (según el mes seleccionado).

### <a name="showing-the-datepickerfragment"></a>Mostrar el DatePickerFragment

Ahora que se ha implementado el `DialogFragment`, en esta sección se examinará cómo usar el fragmento en una actividad. En la aplicación de ejemplo que acompaña a esta guía, la actividad creará una instancia del `DialogFragment` mediante el Factory Method de `NewInstance` y, a continuación, mostrará la invocación de `DialogFragment.Show`. Como parte de la creación de instancias del `DialogFragment`, la actividad pasa un `Action<DateTime>`, que mostrará la fecha en un `TextView` que se hospeda en la actividad:

```csharp
[Activity(Label = "@string/app_name", MainLauncher = true, Icon = "@drawable/icon")]
public class MainActivity : Activity
{
    TextView _dateDisplay;
    Button _dateSelectButton;

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.Main);

        _dateDisplay = FindViewById<TextView>(Resource.Id.date_display);
        _dateSelectButton = FindViewById<Button>(Resource.Id.date_select_button);
        _dateSelectButton.Click += DateSelect_OnClick;
    }

    void DateSelect_OnClick(object sender, EventArgs eventArgs)
    {
        DatePickerFragment frag = DatePickerFragment.NewInstance(delegate(DateTime time)
                                                                 {
                                                                     _dateDisplay.Text = time.ToLongDateString();
                                                                 });
        frag.Show(FragmentManager, DatePickerFragment.TAG);
    }
}
```

## <a name="summary"></a>Resumen

En este ejemplo se describe cómo mostrar un widget `DatePicker` como un cuadro de diálogo modal emergente como parte de una actividad de Android. Proporcionó una implementación de DialogFragment de ejemplo y explicó la interfaz `IOnDateSetListener`. En este ejemplo también se muestra cómo DialogFragment puede interactuar con la actividad del host para mostrar la fecha seleccionada.

## <a name="related-links"></a>Vínculos relacionados

- [DialogFragment](xref:Android.App.DialogFragment)
- [DatePicker](xref:Android.Widget.DatePicker)
- [DatePickerDialog](xref:Android.App.DatePickerDialog)
- [DatePickerDialog.IOnDateSetListener](xref:Android.App.DatePickerDialog.IOnDateSetListener)
- [Seleccionar una fecha](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/datepicker/select_a_date)
