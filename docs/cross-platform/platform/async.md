---
title: Información general sobre la compatibilidad con Async
description: En este documento se describe la programación con Async y Await, los conceptos presentados en C# 5 para facilitar la escritura de código asincrónico.
ms.prod: xamarin
ms.assetid: F87BF587-AB64-4C60-84B1-184CAE36ED65
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 9821519c680de61767792c8122938bf826e457f5
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453251"
---
# <a name="async-support-overview"></a>Información general sobre la compatibilidad con Async

_C# 5 presentó dos palabras clave para simplificar el programa asincrónico: Async y Await. Estas palabras clave permiten escribir código simple que utiliza la biblioteca TPL para ejecutar operaciones de ejecución prolongada (como el acceso a la red) en otro subproceso y acceder fácilmente a los resultados al finalizar. Las versiones más recientes de Xamarin. iOS y Xamarin. Android admiten Async y Await: este documento proporciona explicaciones y un ejemplo del uso de la nueva sintaxis con Xamarin._

La compatibilidad con Async de Xamarin se basa en la base mono 3,0 y actualiza el perfil de API de la versión de Silverlight de la que es fácil de usar para ser una versión móvil de .NET 4,5.

## <a name="overview"></a>Información general

En este documento se presentan las nuevas palabras clave Async y Await y, a continuación, se explican algunos ejemplos sencillos que implementan métodos asincrónicos en Xamarin. iOS y Xamarin. Android.

Para obtener una explicación más completa de las nuevas características asincrónicas de C# 5 (incluidos muchos ejemplos y distintos escenarios de uso), consulte el artículo [programación asincrónica](/dotnet/csharp/async).

La aplicación de ejemplo realiza una solicitud Web asincrónica simple (sin bloquear el subproceso principal) y, a continuación, actualiza la interfaz de usuario con el número de caracteres y HTML descargados.

 [![La aplicación de ejemplo realiza una solicitud Web asincrónica simple sin bloquear el subproceso principal y, a continuación, actualiza la interfaz de usuario con el número de caracteres y HTML descargados.](async-images/AsyncAwait_427x368.png)](async-images/AsyncAwait.png#lightbox)

La compatibilidad con Async de Xamarin se basa en la base mono 3,0 y actualiza el perfil de API de la versión de Silverlight de la que es fácil de usar para ser una versión móvil de .NET 4,5.

## <a name="requirements"></a>Requisitos

Las características de C# 5 requieren mono 3,0, que se incluye en Xamarin. iOS 6,4 y Xamarin. Android 4,8. Se le pedirá que actualice los mono, Xamarin. iOS, Xamarin. Android y Xamarin. Mac para aprovecharlo.

## <a name="using-async-amp-await"></a>Usar Await de Async &amp;

 `async` y `await` son nuevas características del lenguaje C# que funcionan en conjunción con la biblioteca de Task Parallel para facilitar la escritura de código de subprocesos con el fin de realizar tareas de ejecución prolongada sin bloquear el subproceso principal de la aplicación.

## <a name="async"></a>async

### <a name="declaration"></a>Declaración

La `async` palabra clave se coloca en una declaración de método (o en un método lambda o anónimo) para indicar que contiene código que se puede ejecutar de forma asincrónica, es decir, no bloquea el subproceso del llamador.

Un método marcado con `async` debe contener al menos una expresión o instrucción Await. Si no hay ninguna `await` instrucción en el método, se ejecutará de forma sincrónica (igual que si no hubiera ningún `async` modificador). Esto también producirá una advertencia del compilador (pero no un error).

### <a name="return-types"></a>Tipos de valor devuelto

Un método asincrónico debe devolver `Task` , `Task<TResult>` o `void` .

Especifique el `Task` tipo de valor devuelto si el método no devuelve ningún otro valor.

Especifique `Task<TResult>` si el método debe devolver un valor, donde `TResult` es el tipo que se devuelve (como `int` , por ejemplo,).

El `void` tipo de valor devuelto se utiliza principalmente para los controladores de eventos que lo requieren. El código que llama a métodos asincrónicos que devuelven void no puede `await` en el resultado.

### <a name="parameters"></a>Parámetros

Los métodos asincrónicos no pueden declarar `ref` `out` parámetros o.

## <a name="await"></a>await

El operador Await se puede aplicar a una tarea dentro de un método marcado como Async. Hace que el método detenga la ejecución en ese momento y espere hasta que se complete la tarea.

El uso de Await no bloquea el subproceso del llamador, sino que el control se devuelve al autor de la llamada. Esto significa que el subproceso que realiza la llamada no está bloqueado, por lo que, por ejemplo, el subproceso de la interfaz de usuario no se bloqueaba al esperar una tarea.

Cuando se completa la tarea, el método se reanuda la ejecución en el mismo punto del código. Esto incluye volver al ámbito try de un bloque try-catch-finally (si hay alguno presente). Await no se puede usar en un bloque catch o Finally.

Obtenga más información sobre [Await](/dotnet/csharp/language-reference/keywords/await) en Microsoft docs.

## <a name="exception-handling"></a>Control de excepciones

Las excepciones que se producen dentro de un método asincrónico se almacenan en la tarea y se producen cuando la tarea es `await` Ed. Estas excepciones se pueden detectar y controlar dentro de un bloque try-catch.

## <a name="cancellation"></a>Cancelación

Los métodos asincrónicos que tardan mucho tiempo en completarse deben admitir la cancelación. Normalmente, la cancelación se invoca de la siguiente manera:

- `CancellationTokenSource`Se crea un objeto.
- La `CancellationTokenSource.Token` instancia se pasa a un método asincrónico que se pueda cancelar.
- La cancelación se solicita llamando al `CancellationTokenSource.Cancel` método.

A continuación, la tarea se cancela y confirma la cancelación.

Para más información sobre la cancelación, vea [Fine-Tuning Your Async Application (C#)](/dotnet/csharp/programming-guide/concepts/async/fine-tuning-your-async-application) (Ajustar la aplicación asincrónica [C#]).

## <a name="example"></a>Ejemplo

Descargue la [solución Xamarin de ejemplo](/samples/xamarin/mobile-samples/asyncawait/) (para iOS y Android) para ver un ejemplo de trabajo de `async` y `await` en Mobile Apps. El código de ejemplo se describe con más detalle en esta sección.

### <a name="writing-an-async-method"></a>Escribir un método asincrónico

El método siguiente muestra cómo codificar un `async` método con una `await` tarea de Ed:

```csharp
public async Task<int> DownloadHomepage()
{
    var httpClient = new HttpClient(); // Xamarin supports HttpClient!

    Task<string> contentsTask = httpClient.GetStringAsync("https://visualstudio.microsoft.com/xamarin"); // async method!

    // await! control returns to the caller and the task continues to run on another thread
    string contents = await contentsTask;

    ResultEditText.Text += "DownloadHomepage method continues after async call. . . . .\n";

    // After contentTask completes, you can calculate the length of the string.
    int exampleInt = contents.Length;

    ResultEditText.Text += "Downloaded the html and found out the length.\n\n\n";

    ResultEditText.Text += contents; // just dump the entire HTML

    return exampleInt; // Task<TResult> returns an object of type TResult, in this case int
}
```

Tenga en cuenta estos puntos:

- La declaración del método incluye la  `async` palabra clave.
- El tipo de valor devuelto es  `Task<int>` para que el código de llamada pueda tener acceso al  `int` valor que se calcula en este método.
- La instrucción return es  `return exampleInt;` un objeto entero (el hecho que devuelve el método  `Task<int>` forma parte de las mejoras del lenguaje).

### <a name="calling-an-async-method-1"></a>Llamar a un método asincrónico 1

Este controlador de eventos de clic de botón puede encontrarse en la aplicación de ejemplo de Android para llamar al método descrito anteriormente:

```csharp
GetButton.Click += async (sender, e) => {

    Task<int> sizeTask = DownloadHomepage();

    ResultTextView.Text = "loading...";
    ResultEditText.Text = "loading...\n";

    // await! control returns to the caller
    var intResult = await sizeTask;

    // when the Task<int> returns, the value is available and we can display on the UI
    ResultTextView.Text = "Length: " + intResult ;
    // "returns" void, since it's an event handler
};
```

Notas:

- El delegado anónimo tiene el prefijo de palabra clave Async.
- El método asincrónico DownloadHomepage devuelve una tarea \<int> que se almacena en la variable sizeTask.
- El código espera en la variable sizeTask.  *Esta* es la ubicación en la que se suspende el método y el control se devuelve al código de llamada hasta que la tarea asincrónica finaliza en su propio subproceso.
- La ejecución  *no se detiene cuando* la tarea se crea en la primera línea del método, a pesar de que la tarea se crea allí. La palabra clave Await representa la ubicación en la que se pausa la ejecución.
- Cuando finaliza la tarea asincrónica, se establece intResult y la ejecución continúa en el subproceso original, desde la línea Await.

### <a name="calling-an-async-method-2"></a>Llamar a un método asincrónico 2

En la aplicación de ejemplo de iOS, el ejemplo se escribe de forma ligeramente diferente para mostrar un enfoque alternativo. En lugar de usar un delegado anónimo, este ejemplo declara un `async` controlador de eventos que se asigna como un controlador de eventos normal:

```csharp
GetButton.TouchUpInside += HandleTouchUpInside;
```

A continuación, el método de control de eventos se define como se muestra aquí:

```csharp
async void HandleTouchUpInside (object sender, EventArgs e)
{
    ResultLabel.Text = "loading...";
    ResultTextView.Text = "loading...\n";

    // await! control returns to the caller
    var intResult = await DownloadHomepage();

    // when the Task<int> returns, the value is available and we can display on the UI
    ResultLabel.Text = "Length: " + intResult ;
}
```

Algunos puntos importantes:

- El método se marca como  `async` pero devuelve  `void` . Normalmente, esto solo se hace para los controladores de eventos (de lo contrario, se devuelve  `Task` o  `Task<TResult>` ).
- La `await` palabra clave en el  `DownloadHomepage` método se asigna directamente a una variable ( `intResult` ), a diferencia del ejemplo anterior, en el que se usa una  `Task<int>` variable intermedia para hacer referencia a la tarea.  *Esta* es la ubicación donde se devuelve el control al autor de la llamada hasta que el método asincrónico se ha completado en otro subproceso.
- Cuando el método asincrónico se completa y devuelve, la ejecución se reanuda en,  `await` lo que significa que se devuelve el resultado entero y, a continuación, se representa en un widget de interfaz de usuario.

## <a name="summary"></a>Resumen

El uso de Async y Await simplifica en gran medida el código necesario para generar operaciones de ejecución prolongada en subprocesos en segundo plano sin bloquear el subproceso principal. También facilitan el acceso a los resultados cuando se completa la tarea.

En este documento se proporciona información general sobre las nuevas palabras clave del lenguaje y ejemplos de Xamarin. iOS y Xamarin. Android.

## <a name="related-links"></a>Vínculos relacionados

- [AsyncAwait (ejemplo)](/samples/xamarin/mobile-samples/asyncawait/)
- [Devoluciones de llamada como la instrucción Go to de las generaciones](https://tirania.org/blog/archive/2013/Aug-15.html)
- [Datos (iOS) (ejemplo)](/samples/xamarin/ios-samples/data/)
- [HttpClient (iOS) (ejemplo)](/samples/xamarin/ios-samples/httpclient/)
- [MapKitSearch (iOS) (ejemplo)](https://github.com/xamarin/monotouch-samples/tree/master/MapKitSearch)
- [Programación asincrónica](/dotnet/csharp/async)
- [Ajustar una aplicación asincrónica (C#)](/dotnet/csharp/programming-guide/concepts/async/fine-tuning-your-async-application)
- [Await, y UI e interbloqueos. ¡ Mi!](https://devblogs.microsoft.com/pfxteam/await-and-ui-and-deadlocks-oh-my/)
- [Procesamiento de tareas a medida que se completan)](https://devblogs.microsoft.com/pfxteam/processing-tasks-as-they-complete/)
- [Modelo asincrónico basado en tareas (TAP)](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)
- [Asincronía en C# 5 (blog de Eric Lippert): acerca de la introducción de las palabras clave](/archive/blogs/ericlippert/asynchrony-in-c-5-part-six-whither-async)