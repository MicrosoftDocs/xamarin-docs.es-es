---
title: Selector de hora
description: Selección de una hora con TimePickerDialog y DialogFragment
ms.prod: xamarin
ms.assetid: EB4E8206-E8AD-9F04-AC1C-82AC9364A9DD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 1f55e5d97058d07926233e8b116663e6674a441c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764958"
---
# <a name="android-time-picker"></a>Selector de tiempo de Android

Para proporcionar una manera para que el usuario seleccione una hora, puede usar [TimePicker](xref:Android.Widget.TimePicker). Las aplicaciones de Android `TimePicker` suelen usar con [TimePickerDialog](xref:Android.App.TimePickerDialog) para seleccionar un &ndash; valor de tiempo, lo que ayuda a garantizar una interfaz coherente entre dispositivos y aplicaciones. `TimePicker`permite a los usuarios seleccionar la hora del día en el modo de 24 horas o de 12 horas.
`TimePickerDialog`es una clase auxiliar que encapsula el `TimePicker` en un cuadro de diálogo.

[![Captura de pantalla de ejemplo del cuadro de diálogo Selector de tiempo en acción](time-picker-images/01-example-screen-sml.png)](time-picker-images/01-example-screen.png#lightbox)

## <a name="overview"></a>Información general

Las aplicaciones modernas de Android `TimePickerDialog` muestran el en un [DialogFragment](xref:Android.App.DialogFragment). Esto hace posible que una aplicación muestre el `TimePicker` como un cuadro de diálogo emergente o lo inserte en una actividad. Además, `DialogFragment` administra el ciclo de vida y la presentación del cuadro de diálogo, lo que reduce la cantidad de código que se debe implementar.

En esta guía se muestra cómo usar `TimePickerDialog`, encapsulado en `DialogFragment`un. La aplicación de ejemplo muestra `TimePickerDialog` como un cuadro de diálogo modal cuando el usuario hace clic en un botón de una actividad. Cuando el usuario establece la hora, el cuadro de diálogo se cierra y un controlador actualiza `TextView` en la pantalla de actividad con el tiempo seleccionado.

## <a name="requirements"></a>Requisitos

La aplicación de ejemplo de esta guía tiene como destino Android 4,1 (nivel de API).
16) o superior, pero se puede usar con Android 3,0 (nivel de API 11 o superior). Es posible admitir versiones anteriores de Android con la incorporación de la biblioteca de compatibilidad de Android V4 al proyecto y algunos cambios de código.

## <a name="using-the-timepicker"></a>Usar el TimePicker

Este ejemplo extiende `DialogFragment`; la implementación de subclases de `TimePickerFragment` `DialogFragment` (a la que se llama `TimePickerDialog`a continuación) hospeda y muestra un. Cuando se inicia la aplicación de ejemplo por primera vez, se muestra un botón de `TextView` **hora de picking** encima de un que se usará para mostrar la hora seleccionada:

[![Pantalla inicial de aplicación de ejemplo](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

Al hacer clic en el botón de **hora de selección** , en la `TimePickerDialog` aplicación de ejemplo se inicia el tal como aparece en esta captura de pantalla:

[![Captura de pantalla del cuadro de diálogo de selector de tiempo predeterminado que muestra la aplicación](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)

En, si selecciona una hora y hace clic en el botón Aceptar `TimePickerDialog` , el invocará el método [IOnTimeSetListener. OnTimeSet.](xref:Android.App.TimePickerDialog.IOnTimeSetListener.OnTimeSet*) `TimePickerDialog`
Esta interfaz se implementa mediante el hospedaje `DialogFragment` (`TimePickerFragment`, que se describe a continuación). Al hacer clic en el botón **Cancelar** , se descartan el fragmento y el cuadro de diálogo.

`DialogFragment`Devuelve la hora seleccionada a la actividad de hospedaje de una de estas tres maneras:

1. **Invocar un método o establecer una propiedad** &ndash; La actividad puede proporcionar una propiedad o un método específicamente para establecer este valor.

2. **Generar un evento** Puede definir un evento que se generará cuando `OnTimeSet` se invoque. &ndash; `DialogFragment`

3. Mediante, puede invocar un`Action<DateTime>` para mostrar la hora en la actividad. **`Action`** &ndash; `DialogFragment` La actividad proporcionará el `Action<DateTime` al crear instancias de `DialogFragment`.

En este ejemplo se utiliza la tercera técnica, que requiere que la actividad proporcione `Action<DateTime>` un controlador a. `DialogFragment`

## <a name="start-an-app-project"></a>Iniciar un proyecto de aplicación

Inicie un nuevo proyecto de Android llamado **TimePickerDemo** (si no está familiarizado con la creación de proyectos de Xamarin. Android, consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) para obtener información sobre cómo crear un nuevo proyecto).

Edite **Resources/layout/main. axml** y reemplace su contenido por el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_gravity="center_horizontal"
    android:padding="16dp">
    <Button
        android:id="@+id/select_button"
        android:paddingLeft="24dp"
        android:paddingRight="24dp"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="PICK TIME"
        android:textSize="20dp" />
    <TextView
        android:id="@+id/time_display"
        android:layout_height="wrap_content"
        android:layout_width="match_parent"
        android:paddingTop="22dp"
        android:text="Picked time will be displayed here"
        android:textSize="24dp" />
</LinearLayout>
```

Se trata de un [LinearLayout](xref:Android.Widget.LinearLayout) básico con una [TextView](xref:Android.Widget.TextView) que muestra la hora y un [botón](xref:Android.Widget.Button) que abre `TimePickerDialog`. Tenga en cuenta que este diseño utiliza cadenas y dimensiones codificadas de forma rígida para que la aplicación sea más sencilla &ndash; y más fácil de entender. normalmente, una aplicación de producción usa recursos para estos valores (como se puede ver en el ejemplo de código [DatePicker](https://github.com/xamarin/recipes/blob/master/Recipes/android/controls/datepicker/select_a_date/Resources/layout/Main.axml) ).

Edite **MainActivity.CS** y reemplace su contenido por el código siguiente:

```csharp
using Android.App;
using Android.Widget;
using Android.OS;
using System;
using Android.Util;
using Android.Text.Format;

namespace TimePickerDemo
{
    [Activity(Label = "TimePickerDemo", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        TextView timeDisplay;
        Button timeSelectButton;

        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            SetContentView(Resource.Layout.Main);
            timeDisplay = FindViewById<TextView>(Resource.Id.time_display);
            timeSelectButton = FindViewById<Button>(Resource.Id.select_button);
        }
    }
}
```

Al compilar y ejecutar este ejemplo, debería ver una pantalla inicial similar a la siguiente captura de pantalla:

[![Pantalla inicial de la aplicación](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

Al hacer clic en el botón de **hora** de `DialogFragment` selección no se hace nada porque aún no `TimePicker`se ha implementado para mostrar.
El siguiente paso es crear este `DialogFragment`.

## <a name="extending-dialogfragment"></a>Extender DialogFragment

Para extender `DialogFragment` para su uso `TimePicker`con, es necesario crear una subclase `TimePickerDialog.IOnTimeSetListener`derivada de `DialogFragment` e implementa. Agregue la siguiente clase a **MainActivity.CS**:

```csharp
public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
{
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };

    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }

    public override Dialog OnCreateDialog (Bundle savedInstanceState)
    {
        DateTime currentTime = DateTime.Now;
        bool is24HourFormat = DateFormat.Is24HourFormat(Activity);
        TimePickerDialog dialog = new TimePickerDialog
            (Activity, this, currentTime.Hour, currentTime.Minute, is24HourFormat);
        return dialog;
    }

    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
        Log.Debug(TAG, selectedTime.ToLongTimeString());
        timeSelectedHandler (selectedTime);
    }
}
```

Esta `TimePickerFragment` clase se divide en partes más pequeñas y se explica en la sección siguiente.

### <a name="dialogfragment-implementation"></a>Implementación de DialogFragment

`TimePickerFragment`implementa varios métodos: un Factory Method, un método de creación de instancias de diálogo y `OnTimeSet` el método de controlador `TimePickerDialog.IOnTimeSetListener`que requiere.

- `TimePickerFragment`es una subclase de `DialogFragment`. También implementa la `TimePickerDialog.IOnTimeSetListener` interfaz (es decir, proporciona el método necesario `OnTimeSet` ):

    ```csharp
    public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
    ```

- `TAG`se inicializa con fines de registro (*MyTimePickerFragment* se puede cambiar a cualquier cadena que desee usar). La `timeSelectedHandler` acción se inicializa en un delegado vacío para evitar excepciones de referencia nulas:

    ```csharp
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };
    ```

- Se llama al `TimePickerFragment` FactoryMethodparacrearunainstancia`NewInstance` de un nuevo. Este método toma un `Action<DateTime>` controlador que se invoca cuando el usuario hace clic en el botón **Aceptar** en el `TimePickerDialog`:

    ```csharp
    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }
    ```

- Cuando se va a mostrar el fragmento, Android llama al `DialogFragment` método [OnCreateDialog](xref:Android.App.DialogFragment.OnCreateDialog*).
    Este método crea un nuevo `TimePickerDialog` objeto y lo inicializa con la actividad, el objeto de devolución `TimePickerFragment`de llamada (que es la instancia actual de) y la hora actual:

    ```csharp
    public override Dialog OnCreateDialog (Bundle savedInstanceState)
    {
        DateTime currentTime = DateTime.Now;
        bool is24HourFormat = DateFormat.Is24HourFormat(Activity);
        TimePickerDialog dialog = new TimePickerDialog
            (Activity, this, currentTime.Hour, currentTime.Minute, is24HourFormat);
        return dialog;
    }
    ```

- Cuando el usuario cambia la configuración de hora en `TimePicker` el cuadro de `OnTimeSet` diálogo, se invoca el método. `OnTimeSet`crea un `DateTime` objeto con la fecha actual y se combina en la hora (hora y minuto) seleccionada por el usuario:

    ```csharp
    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
    ```

- Este `DateTime` objeto se pasa a la `timeSelectedHandler` que se registra con el `TimePickerFragment` objeto en el momento de su creación. `OnTimeSet`invoca este controlador para actualizar la presentación de la hora de la actividad a la hora seleccionada (este controlador se implementa en la sección siguiente):

    ```csharp
    timeSelectedHandler (selectedTime);
    ```

## <a name="displaying-the-timepickerfragment"></a>Mostrar el TimePickerFragment

Ahora que `DialogFragment` se ha implementado, es el momento de crear una instancia `DialogFragment` del mediante `NewInstance` el Factory Method y mostrarlo mediante la invocación de [DialogFragment. show](xref:Android.App.DialogFragment.Show*):

Agregue el método siguiente a `MainActivity`:

```csharp
void TimeSelectOnClick (object sender, EventArgs eventArgs)
{
    TimePickerFragment frag = TimePickerFragment.NewInstance (
        delegate (DateTime time)
        {
            timeDisplay.Text = time.ToShortTimeString();
        });

    frag.Show(FragmentManager, TimePickerFragment.TAG);
}
```

Después `TimeSelectOnClick` de crear una `TimePickerFragment`instancia de, se crea y pasa un delegado para un método anónimo que actualiza la presentación de la hora de la actividad con el valor de tiempo pasado. Por último, inicia el `TimePicker` fragmento de diálogo ( `DialogFragment.Show`Via) para mostrar `TimePicker` al usuario.

Al final del `OnCreate` método, agregue la siguiente línea para adjuntar el controlador de eventos al botón de **hora de selección** que inicia el cuadro de diálogo:

```csharp
timeSelectButton.Click += TimeSelectOnClick;
```

Cuando se haga clic en el botón de hora `TimeSelectOnClick` de **selección** , se invocará para `TimePicker` mostrar el fragmento de diálogo al usuario.

## <a name="try-it"></a>¡Inténtelo!

Compile y ejecute la aplicación. Al hacer clic en el botón **elegir hora** , `TimePickerDialog` se muestra en el formato de hora predeterminado de la actividad (en este caso, el modo de 12 horas AM/PM):

[![El cuadro de diálogo de hora se muestra en el modo AM/PM](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)
   
Al hacer clic en **Aceptar** en `TimePicker` el cuadro de diálogo, el controlador `TextView` actualiza la actividad con la hora seleccionada y, a continuación, se cierra:

[![Se muestra la hora A/M en el TextView de actividad](time-picker-images/04-after-time-dialog-sml.png)](time-picker-images/04-after-time-dialog.png#lightbox)

Después, agregue la siguiente línea de código a `OnCreateDialog` inmediatamente después `is24HourFormat` de que se declare e inicialice:

```csharp
is24HourFormat = true;
```

Este cambio obliga a que la marca pasada `TimePickerDialog` al constructor sea `true` para que se utilice el modo de 24 horas en lugar del formato de hora de la actividad de hospedaje. Al compilar y ejecutar la aplicación de nuevo, haga clic en el botón `TimePicker` de hora de **selección** , el cuadro de diálogo se muestra ahora en formato de 24 horas:

[![Cuadro de diálogo TimePicker en formato de 24 horas](time-picker-images/05-24hr-time-dialog-sml.png)](time-picker-images/05-24hr-time-dialog.png#lightbox)

Dado que el controlador llama a [DateTime. ToShortTimeString](xref:System.DateTime.ToShortDateString*) para imprimir la hora en la `TextView`de la actividad, la hora se sigue imprimiendo en el formato predeterminado de 12 horas AM/PM.

## <a name="summary"></a>Resumen

En este artículo se ha explicado `TimePicker` cómo mostrar un widget como un cuadro de diálogo modal emergente desde una actividad de Android. Proporcionó una implementación `DialogFragment` de ejemplo y explicó `IOnTimeSetListener` la interfaz. En este ejemplo también se muestra `DialogFragment` cómo puede interactuar con la actividad del host para mostrar la hora seleccionada.

## <a name="related-links"></a>Vínculos relacionados

- [DialogFragment](xref:Android.App.DialogFragment)
- [TimePicker](xref:Android.Widget.TimePicker)
- [TimePickerDialog](xref:Android.App.TimePickerDialog)
- [TimePickerDialog.IOnTimeSetListener](xref:Android.App.TimePickerDialog.IOnTimeSetListener)
- [TimePickerDemo (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-timepickerdemo)
