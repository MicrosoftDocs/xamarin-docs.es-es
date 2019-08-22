---
title: 'Tutorial: guardado del estado de actividad'
description: Hemos tratado la teoría detrás de guardar el estado en la guía del ciclo de vida de la actividad. Ahora, vamos a examinar un ejemplo.
ms.prod: xamarin
ms.assetid: A6090101-67C6-4BDD-9416-F2FB74805A87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: febb9b297712a97c03613468b79ca583ec300e77
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887723"
---
# <a name="walkthrough---saving-the-activity-state"></a>Tutorial: guardado del estado de actividad

_Hemos tratado la teoría detrás de guardar el estado en la guía del ciclo de vida de la actividad. Ahora, vamos a examinar un ejemplo._

## <a name="activity-state-walkthrough"></a>Tutorial de estado de actividad

Vamos a abrir el proyecto **ActivityLifecycle_Start** (en el ejemplo [ActivityLifecycle](https://docs.microsoft.com/samples/xamarin/monodroid-samples/activitylifecycle) ), compilarlo y ejecutarlo. Se trata de un proyecto muy sencillo que tiene dos actividades para demostrar el ciclo de vida de la actividad y cómo se llama a los distintos métodos del ciclo de vida. Al iniciar la aplicación, se muestra la pantalla `MainActivity` de:

[![Actividad de una pantalla](saving-state-images/01-activity-a-sml.png)](saving-state-images/01-activity-a.png#lightbox)

### <a name="viewing-state-transitions"></a>Ver transiciones de estado

Cada método de este ejemplo escribe en la ventana de salida de la aplicación IDE para indicar el estado de la actividad. (Para abrir la ventana de salida en Visual Studio, presione **CTRL-ALT-O**; para abrir la ventana de salida en Visual Studio para Mac, haga clic en **ver > paneles > salida**de la aplicación).

Cuando la aplicación se inicia por primera vez, la ventana de salida muestra los cambios de estado de la *actividad A*: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

Cuando hacemos clic en el botón **iniciar actividad b** , vemos *actividad* en pausa y detener mientras la *actividad B* pasa por sus cambios de estado: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.SecondActivity] Activity B - OnCreate
[ActivityLifecycle.SecondActivity] Activity B - OnStart
[ActivityLifecycle.SecondActivity] Activity B - OnResume
[ActivityLifecycle.MainActivity] Activity A - OnStop
```

Como resultado, la *actividad B* se inicia y se muestra en lugar de *la actividad a*: 

[![Pantalla de actividad B](saving-state-images/02-activity-b-sml.png)](saving-state-images/02-activity-b.png#lightbox)

Cuando hacemos clic en el botón **atrás** , la *actividad B* se destruye y se reanuda *la actividad A* : 

```shell
[ActivityLifecycle.SecondActivity] Activity B - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnRestart
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
[ActivityLifecycle.SecondActivity] Activity B - OnStop
[ActivityLifecycle.SecondActivity] Activity B - OnDestroy
```

### <a name="adding-a-click-counter"></a>Agregar un contador de clics

A continuación, vamos a cambiar la aplicación para que tengamos un botón que cuente y muestre el número de veces que se hace clic en él. En primer lugar, vamos a `_counter` agregar una variable `MainActivity`de instancia a:

```csharp
int _counter = 0;
```

A continuación, vamos a editar el archivo de diseño **Resource/layout/main. axml** y agregar `clickButton` un nuevo que muestre el número de veces que el usuario hizo clic en el botón. El **Main. axml** resultante debe ser similar al siguiente: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <Button
        android:id="@+id/myButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/mybutton_text" />
    <Button
        android:id="@+id/clickButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/counterbutton_text" />
</LinearLayout>
```

Vamos a agregar el código siguiente al final del método de [creación](xref:Android.App.Activity.OnCreate*) en `MainActivity` &ndash; `clickButton`este código que controla los eventos de clic de:

```csharp
var clickbutton = FindViewById<Button> (Resource.Id.clickButton);
clickbutton.Text = Resources.GetString (
    Resource.String.counterbutton_text, _counter);
clickbutton.Click += (object sender, System.EventArgs e) =>
{
    _counter++;
    clickbutton.Text = Resources.GetString (
        Resource.String.counterbutton_text, _counter);
} ;
```

Al compilar y ejecutar la aplicación de nuevo, aparece un botón nuevo que se incrementa y muestra el `_counter` valor de en cada clic:

[![Agregar recuento de entradas táctiles](saving-state-images/03-touched-sml.png)](saving-state-images/03-touched.png#lightbox)

Pero cuando giramos el dispositivo al modo horizontal, se pierde este recuento:

[![Al girar a horizontal, el recuento se vuelve a establecer en cero.](saving-state-images/05-rotate-nosave-sml.png)](saving-state-images/05-rotate-nosave.png#lightbox)

Al examinar la salida de la aplicación, vemos que la *actividad A* se pausó, se detuvo, se destruye, se vuelve a crear, se reinicia y luego se reanuda durante la rotación del modo vertical a horizontal: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

Dado que la *actividad A* se destruye y vuelve a crear cuando se gira el dispositivo, se pierde el estado de la instancia. A continuación, agregaremos código para guardar y restaurar el estado de la instancia.

### <a name="adding-code-to-preserve-instance-state"></a>Agregar código para conservar el estado de la instancia

Vamos a agregar un método a `MainActivity` para guardar el estado de la instancia. Antes de que se destruya *la actividad A* , Android llama automáticamente a [OnSaveInstanceState](xref:Android.App.Activity.OnSaveInstanceState*) y pasa una [agrupación](xref:Android.OS.Bundle) que se puede usar para almacenar el estado de la instancia. Vamos a usarlo para guardar nuestro recuento de clics como un valor entero:

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
    outState.PutInt ("click_count", _counter);
    Log.Debug(GetType().FullName, "Activity A - Saving instance state");

    // always call the base implementation!
    base.OnSaveInstanceState (outState);    
}
```

Cuando se vuelve a crear y reanudar la *actividad a* , Android lo `Bundle` pasa a nuestro `OnCreate` método. Vamos a agregar código a `OnCreate` para restaurar el `_counter` valor `Bundle`desde el pasado. Agregue el código siguiente justo antes de la línea `clickbutton` donde se define: 

```csharp
if (bundle != null)
{
    _counter = bundle.GetInt ("click_count", 0);
    Log.Debug(GetType().FullName, "Activity A - Recovered instance state");
}
```

Vuelva a compilar y ejecutar la aplicación y, a continuación, haga clic en el segundo botón varias veces. Cuando giramos el dispositivo al modo horizontal, se conserva el recuento.

[![Al girar la pantalla se muestra el recuento de cuatro conservadas](saving-state-images/06-rotate-save-sml.png)](saving-state-images/06-rotate-save.png#lightbox)

Echemos un vistazo a la ventana de salida para ver lo que ha sucedido:

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - Saving instance state
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - Recovered instance state
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

Antes de llamar al método [OnStop](xref:Android.App.Activity.OnStop) , se llamó a `OnSaveInstanceState` nuestro nuevo método para guardar `Bundle`el `_counter` valor en. Android lo pasó `Bundle` de nuevo a nosotros cuando llamó a `OnCreate` nuestro método, y pudimos usarlo para restaurar el `_counter` valor al lugar donde se quedó.

## <a name="summary"></a>Resumen

En este tutorial, hemos usado nuestro conocimiento del ciclo de vida de la actividad para conservar los datos de estado.

## <a name="related-links"></a>Vínculos relacionados

- [ActivityLifecycle (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/activitylifecycle)
- [Ciclo de vida de la actividad](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Actividad de Android](xref:Android.App.Activity)
