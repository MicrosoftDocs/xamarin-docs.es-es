---
title: ''Xamarin.Essentials: MainThread'' description: ms.assetid: author: ms.custom: ms.author: ms.date: no-loc:
- "Xamarin.Forms"
- "Xamarin.Essentials"

---

# <a name="xamarinessentials-mainthread"></a>Xamarin.Essentials: MainThread

La clase **MainThread** permite que las aplicaciones ejecuten código en el subproceso de ejecución principal y determinen si un bloque de código determinado se ejecuta actualmente en el subproceso principal.

## <a name="background"></a>Fondo

La mayoría de los sistemas operativos, incluidos iOS, Android y Plataforma universal de Windows, usan un modelo de un único subproceso para el código que participa en la interfaz de usuario. Este modelo resulta necesario para serializar de manera adecuada los eventos de la interfaz de usuario, incluidas pulsaciones de teclas y entradas táctiles. Con frecuencia, este subproceso se denomina el _subproceso principal_, el _subproceso de interfaz de usuario_ o el _subproceso de UI_. La desventaja que presenta este modelo es que todo el código que accede a los elementos de la interfaz de usuario se deben ejecutar en el subproceso principal de la aplicación.

Algunas veces, las aplicaciones deben usar eventos que llamar al controlador de eventos en un subproceso de ejecución secundario. (Es posible que las clases [`Accelerometer`](accelerometer.md), [`Compass`](compass.md), [`Gyroscope`](gyroscope.md), [`Magnetometer`](magnetometer.md) y [`OrientationSensor`](orientation-sensor.md) de Xamarin.Essentials devuelvan información en un subproceso secundario cuando se usa con velocidades más rápidas). Si el controlador de eventos debe acceder a los elementos de la interfaz de usuario, debe ejecutar ese código en el subproceso principal. La clase **MainThread** permite que la aplicación ejecute este código en el subproceso principal.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="running-code-on-the-main-thread"></a>Ejecución de código en el subproceso principal

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

Para ejecutar código en el subproceso principal, llame al método `MainThread.BeginInvokeOnMainThread` estático. El argumento es un objeto [`Action`](xref:System.Action), que no es más que un método sin argumentos y sin valor devuelto:

```csharp
MainThread.BeginInvokeOnMainThread(() =>
{
    // Code to run on the main thread
});
```

También es posible definir un método independiente para el código que se debe ejecutar en el subproceso principal:

```csharp
void MyMainThreadCode()
{
    // Code to run on the main thread
}
```

Luego, para ejecutar este método en el subproceso principal, haga referencia a él en el método `BeginInvokeOnMainThread`:

```csharp
MainThread.BeginInvokeOnMainThread(MyMainThreadCode);
```

> [!NOTE]
> Xamarin.Forms tiene un método denominado [`Device.BeginInvokeOnMainThread(Action)`](https://docs.microsoft.com/dotnet/api/xamarin.forms.device.begininvokeonmainthread)
> que hace lo mismo que `MainThread.BeginInvokeOnMainThread(Action)`.
> Si bien puede usar cualquier método en una aplicación de Xamarin.Forms, considere si el código de llamada necesita una dependencia de Xamarin.Forms. Si no es así, `MainThread.BeginInvokeOnMainThread(Action)` probablemente sea una mejor opción.

## <a name="determining-if-code-is-running-on-the-main-thread"></a>Determinación de si el código se ejecuta en el subproceso principal

La clase `MainThread` también permite que una aplicación determine si un bloque de código determinado se ejecuta en el subproceso principal. La propiedad `IsMainThread` devuelve `true` si el código que llama a la propiedad se ejecuta en el subproceso principal. Un programa puede usar esta propiedad para ejecutar código diferente para el subproceso principal o secundario:

```csharp
if (MainThread.IsMainThread)
{
    // Code to run if this is the main thread
}
else
{
    // Code to run if this is a secondary thread
}
```

Tal vez se pregunte si debe comprobar que el código se esté ejecutando en un subproceso secundario antes de llamar a `BeginInvokeOnMainThread`, por ejemplo, de esta manera:

```csharp
if (MainThread.IsMainThread)
{
    MyMainThreadCode();
}
else
{
    MainThread.BeginInvokeOnMainThread(MyMainThreadCode);
}
```

Quizás sospeche que esta comprobación puede mejorar el rendimiento si el bloque de código ya se ejecuta en el subproceso principal.

_Sin embargo, esta comprobación no es necesaria._ Las implementaciones de plataforma de los `BeginInvokeOnMainThread` mismos comprueban si la llamada se realiza en el subproceso principal. La penalización de rendimiento si llama a `BeginInvokeOnMainThread` cuando no es realmente necesario es muy pequeña.

## <a name="additional-methods"></a>Otros métodos

La clase `MainThread` incluye los siguientes métodos `static` adicionales, que se pueden usar para interactuar con los elementos de la interfaz de usuario de los subprocesos de fondo:

| Método | Argumentos | Valores devueltos | Propósito |
|---|---|---|---|
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | Invoca un elemento `Func<T>` en el subproceso principal y espera a que se complete. |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | Invoca un elemento `Action` en el subproceso principal y espera a que se complete. |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | Invoca un elemento `Func<Task<T>>` en el subproceso principal y espera a que se complete. |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | Invoca un elemento `Func<Task>` en el subproceso principal y espera a que se complete. |
| `GetMainThreadSynchronizationContextAsync` | | `Task<SynchronizationContext>` | Devuelve el elemento `SynchronizationContext` para el subproceso principal. |

## <a name="api"></a>API

- [Código fuente de MainThread](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/MainThread)
- [Documentación de API de MainThread](xref:Xamarin.Essentials.MainThread)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Main-Thread-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
