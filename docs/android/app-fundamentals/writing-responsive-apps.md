---
title: Escribir aplicaciones con capacidad de respuesta
ms.prod: xamarin
ms.assetid: 452DF940-6331-55F0-D130-002822BBED55
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: 1900a4fc42778db07e78c41bbc0acfafbd594cdf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024194"
---
# <a name="writing-responsive-applications"></a>Escribir aplicaciones con capacidad de respuesta

Una de las claves para mantener una GUI receptiva es realizar tareas de ejecución prolongada en un subproceso en segundo plano para que la GUI no se bloquee. Supongamos que queremos calcular un valor para mostrar al usuario, pero ese valor tarda 5 segundos en calcularse:

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        SlowMethod ();
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        textview.Text = "Method Complete";
    }
}
```

Esto funcionará, pero la aplicación se "bloqueará" durante 5 segundos mientras se calcula el valor. Durante este tiempo, la aplicación no responderá a ninguna interacción del usuario. Para solucionar este tema, queremos realizar los cálculos en un subproceso en segundo plano:

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        ThreadPool.QueueUserWorkItem (o => SlowMethod ());
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        textview.Text = "Method Complete";
    }
}
```

Ahora se calcula el valor en un subproceso en segundo plano para que la interfaz de usuario siga respondiendo durante el cálculo. Sin embargo, cuando se realiza el cálculo, nuestra aplicación se bloquea, lo que deja en el registro:

```shell
E/mono    (11207): EXCEPTION handling: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):
E/mono    (11207): Unhandled Exception: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):   at Android.Runtime.JNIEnv.CallVoidMethod (IntPtr jobject, IntPtr jmethod, Android.Runtime.JValue[] parms)
E/mono    (11207):   at Android.Widget.TextView.set_Text (IEnumerable`1 value)
E/mono    (11207):   at MonoDroidDebugging.Activity1.SlowMethod ()
```

Esto se debe a que debe actualizar la GUI desde el subproceso de la GUI. Nuestro código actualiza la GUI desde el subproceso ThreadPool, lo que hace que la aplicación se bloquee. Necesitamos calcular nuestro valor en el subproceso en segundo plano, pero luego realizar nuestra actualización en el subproceso de la GUI, que se controla con [Activity. RunOnUIThread](xref:Android.App.Activity.RunOnUiThread*):

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        ThreadPool.QueueUserWorkItem (o => SlowMethod ());
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        RunOnUiThread (() => textview.Text = "Method Complete");
    }
}
```

Este código funciona según lo esperado. Esta GUI sigue respondiendo y se actualiza correctamente una vez que se com el cálculo.

Tenga en cuenta que esta técnica no se utiliza solo para calcular un valor caro. Se puede usar para cualquier tarea de ejecución prolongada que se pueda realizar en segundo plano, como una llamada de servicio web o la descarga de datos de Internet.
