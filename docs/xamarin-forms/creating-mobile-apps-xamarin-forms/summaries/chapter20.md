---
title: Resumen del capítulo 20. Asincronía y E/S de archivos
description: 'Creación de aplicaciones móviles con Xamarin.Forms: Resumen del capítulo 20. Asincronía y E/S de archivos'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D595862D-64FD-4C0D-B0AD-C1F440564247
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 283273e6ee28cc5cd1a61169f38bfcd1dd1726d8
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771037"
---
# <a name="summary-of-chapter-20-async-and-file-io"></a>Resumen del capítulo 20. Asincronía y E/S de archivos

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)

> [!NOTE] 
> En las notas de esta página se indican las áreas en las que Xamarin.Forms difiere del material presentado en el libro.

 Una interfaz gráfica de usuario debe responder de forma secuencial a los eventos de entrada del usuario. Esto implica que todo el procesamiento de eventos de entrada del usuario debe realizarse en un único subproceso, a menudo denominado *subproceso principal* o *subproceso de interfaz de usuario*.

Los usuarios esperan que las interfaces gráficas de usuario respondan. Esto significa que un programa debe procesar rápidamente los eventos de entrada del usuario. Si eso no es posible, el procesamiento se debe relegar a subprocesos secundarios de ejecución.

Varios programas de ejemplo de este libro han utilizado la clase de [`WebRequest`](xref:System.Net.WebRequest). En esta clase, el método [`BeginGetResponse`](xref:System.Net.WebRequest.BeginGetResponse(System.AsyncCallback,System.Object)) inicia un subproceso de trabajo, que llama a una función de devolución de llamada cuando está completa. Sin embargo, esa función de devolución de llamada se ejecuta en el subproceso de trabajo, por lo que el programa debe llamar al método [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) para tener acceso a la interfaz de usuario.

> [!NOTE]
> Los programas de Xamarin.Forms deben usar [`HttpClient`](xref:System.Net.Http.HttpClient) en lugar de [`WebRequest`](xref:System.Net.WebRequest) para tener acceso a los archivos a través de Internet. `HttpClient` admite operaciones asincrónicas.

Un enfoque más moderno para el procesamiento asincrónico está disponible en .NET y C#. Esto implica las clases [`Task`](xref:System.Threading.Tasks.Task) y [`Task<TResult>`](xref:System.Threading.Tasks.Task`1) y otros tipos en los espacios de nombres [`System.Threading`](xref:System.Threading) y [`System.Threading.Tasks`](xref:System.Threading.Tasks), así como las palabras clave `async` y `await` de C# 5.0. Esto es en lo que se centra este capítulo.

## <a name="from-callbacks-to-await"></a>De las devoluciones de llamada a await

La propia clase `Page` contiene tres métodos asincrónicos para mostrar cuadros de alerta:

- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) devuelve un objeto `Task`.
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String,System.String)) devuelve un objeto `Task<bool>`.
- [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) devuelve un objeto `Task<string>`.

Los objetos `Task` indican que estos métodos implementan el modelo asincrónico basado en tareas, conocido como TAP. Estos objetos `Task` se devuelven rápidamente desde el método. Los valores devueltos de `Task<T>` constituyen una "promesa" de que un valor de tipo `TResult` estará disponible cuando se complete la tarea. El valor devuelto `Task` indica una acción asincrónica que se completará pero sin ningún valor devuelto.

En todos estos casos, `Task` se completa cuando el usuario descarta el cuadro de alerta.  

### <a name="an-alert-with-callbacks"></a>Una alerta con devoluciones de llamada

En el ejemplo [**AlertCallbacks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks) se muestra cómo controlar los objetos devueltos `Task<bool>` y las llamadas `Device.BeginInvokeOnMainThread` mediante métodos de devolución de llamada.

### <a name="an-alert-with-lambdas"></a>Una alerta con lambdas

En el ejemplo [**AlertLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas) se muestra cómo usar las funciones lambda anónimas para administrar llamadas `Task` y `Device.BeginInvokeOnMainThread`.  

### <a name="an-alert-with-await"></a>Una alerta con await

Un enfoque más sencillo implica las palabras clave `async` y `await` presentadas en C# 5. En el ejemplo [**AlertAwait**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait) se muestra su uso.

### <a name="an-alert-with-nothing"></a>Una alerta con nothing

Si el método asincrónico devuelve `Task` en lugar de `Task<TResult>`, el programa no necesita usar ninguna de estas técnicas si no necesita saber cuándo se completa la tarea asincrónica. En el ejemplo [**NothingAlert**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert) se muestra esto.

### <a name="saving-program-settings-asynchronously"></a>Guardar la configuración del programa de forma asincrónica

En el ejemplo [**SaveProgramChanges**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings) se muestra el uso del método [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) de `Application` para guardar la configuración del programa a medida que cambian sin reemplazar el método `OnSleep`.

### <a name="a-platform-independent-timer"></a>Un temporizador independiente de la plataforma

Se puede usar [`Task.Delay`](xref:System.Threading.Tasks.Task.Delay(System.Int32)) para crear un temporizador independiente de la plataforma. En el ejemplo [**TaskDelayClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock) se muestra esto.

## <a name="file-inputoutput"></a>Entrada y salida de archivos

Tradicionalmente, el espacio de nombres [`System.IO`](xref:System.IO) de .NET ha sido el origen de la compatibilidad de E/S de archivos. Aunque algunos métodos de este espacio de nombres admiten operaciones asincrónicas, la mayoría no. El espacio de nombres también admite varias llamadas a métodos simples que realizan sofisticadas funciones de E/S de archivos.

### <a name="good-news-and-bad-news"></a>Buenas y malas noticias

Todas las plataformas compatibles con Xamarin.Forms admiten el almacenamiento local de la aplicación &mdash; que es privado para la aplicación.

Las bibliotecas de Xamarin.iOS y Xamarin.Android incluyen una versión de .NET que Xamarin ha adaptado expresamente para estas dos plataformas. Entre ellas se incluyen las clases de `System.IO` que puede usar para realizar la E/S de archivos con el almacenamiento local de la aplicación en estas dos plataformas.

Sin embargo, si busca estas clases `System.IO` en un PCL de Xamarin.Forms, no las encontrará. El problema es que Microsoft ha renovado por completo la E/S de archivos para la API de Windows Runtime. Los programas que tienen como destino Windows 8.1, Windows Phone 8.1 y la Plataforma universal de Windows no usan `System.IO` para la E/S de archivos.

Esto significa que necesitará usar [`DependencyService`](xref:Xamarin.Forms.DependencyService) (que se describe en primer lugar en [**Capítulo 9. Llamadas API específicas de la plataforma**](chapter09.md) para implementar E/S de archivos.

> [!NOTE]
> Las bibliotecas de clases portátiles se han reemplazado con las bibliotecas de .NET Standard 2.0, y .NET Standard 2.0 admite tipos [`System.IO`](xref:System.IO) para todas las plataformas Xamarin.Forms. Ya no es necesario usar `DependencyService` para la mayoría de las tareas de E/S de archivos. Consulte [Control de archivos en Xamarin.Forms](~/xamarin-forms/data-cloud/data/files.md) para obtener un enfoque más moderno para la E/S de archivos.

### <a name="a-first-shot-at-cross-platform-file-io"></a>Primera captura de E/S de archivos entre plataformas

En el ejemplo [**TextFileTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout) se define una interfaz [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs) para la E/S de archivos y las implementaciones de esta interfaz en todas las plataformas. Sin embargo, las implementaciones de Windows Runtime no funcionan con los métodos de esta interfaz porque los métodos de E/S de archivos de Windows Runtime son asincrónicos.

### <a name="accommodating-windows-runtime-file-io"></a>Adaptación de la E/S de archivos de Windows Runtime

Los programas que se ejecutan en Windows Runtime usan clases en los espacios de nombres [`Windows.Storage`](/uwp/api/Windows.Storage) y [`Windows.Storage.Streams`](/uwp/api/Windows.Storage.Streams) para la E/S de archivos, incluido el almacenamiento local de la aplicación. Dado que Microsoft ha determinado que cualquier operación que requiera más de 50 milisegundos debe ser asincrónica para evitar el bloqueo del subproceso de interfaz de usuario, estos métodos de E/S de archivos son principalmente asíncronicos.

El código que muestra este nuevo enfoque estará en una biblioteca para que lo puedan usar otras aplicaciones.

## <a name="platform-specific-libraries"></a>Bibliotecas específicas de plataforma

Es ventajoso almacenar código reutilizable en bibliotecas. Esto es obviamente más difícil cuando diferentes partes del código reutilizable son para sistemas operativos completamente diferentes.

En la solución [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) se muestra un enfoque. Esta solución contiene siete proyectos diferentes:

- [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform), un PCL normal de Xamarin.Forms
- [**Xamarin.FormsBook.Platform.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS), una biblioteca de clases de iOS
- [**Xamarin.FormsBook.Platform.Android**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android), una biblioteca de clases de Android
- [**Xamarin.FormsBook.Platform.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP), una biblioteca de clases universales de Windows
- [**Xamarin.FormsBook.Platform.WinRT**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT), un proyecto compartido para el código que es común a todas la plataformas de Windows

Todos los proyectos de plataforma individuales (a excepción de **Xamarin.FormsBook.Platform.WinRT**) tienen referencias a **Xamarin.FormsBook.Platform**. Los tres proyectos de Windows tienen una referencia a **Xamarin.FormsBook.Platform.WinRT**.

Todos los proyectos contienen un método estático `Toolkit.Init` para asegurarse de que la biblioteca se carga si un proyecto no hace referencia directa a ella en una solución de aplicación Xamarin.Forms.

El proyecto **Xamarin.FormsBook.Platform** contiene la nueva interfaz [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs). Todos los métodos ahora tienen nombres con sufijos `Async` y devuelven objetos `Task`.

El proyecto **Xamarin.FormsBook.Platform.WinRT** contiene la clase [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) para Windows Runtime.

El proyecto **Xamarin.FormsBook.Platform.iOS** contiene la clase [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs) para iOS. Estos métodos deben ser ahora asincrónicos. Algunos de los métodos usan las versiones asincrónicas de los métodos definidos en `StreamWriter` y `StreamReader`: [`WriteAsync`](xref:System.IO.StreamWriter.WriteAsync(System.String)) y [`ReadToEndAsync`](xref:System.IO.StreamReader.ReadToEndAsync). Otros convierten un resultado en un objeto `Task` mediante el método [`FromResult`](xref:System.Threading.Tasks.Task.FromResult*).

El proyecto **Xamarin.FormsBook.Platform.Android** contiene una clase [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs) similar para Android.

El proyecto **Xamarin.FormsBook.Platform** también contiene una clase [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs) que facilita el uso del objeto `DependencyService`.

Para usar estas bibliotecas, una solución de aplicación debe incluir todos los proyectos de la solución **Xamarin.FormsBook.Platform**, y cada uno de los proyectos de aplicación debe tener una referencia a la biblioteca correspondiente en **Xamarin.FormsBook.Platform**.

La solución [**TextFileAsync**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync) muestra cómo usar las bibliotecas **Xamarin.FormsBook.Platform**. Cada uno de los proyectos tiene una llamada a `Toolkit.Init`. La aplicación hace uso de las funciones de E/S de archivos asincrónicas.

### <a name="keeping-it-in-the-background"></a>Mantener en segundo plano

Los métodos de las bibliotecas que realizan llamadas a varios métodos asincrónicos, como los métodos `WriteFileAsync` y `ReadFileASync` en la clase [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) de Windows Runtime, pueden resultar algo más eficaces mediante el método [`ConfigureAwait`](xref:System.Threading.Tasks.Task`1.ConfigureAwait(System.Boolean)) para evitar volver al subproceso de interfaz de usuario.

### <a name="dont-block-the-ui-thread"></a>No bloquee el subproceso de interfaz de usuario.

A veces, es tentador evitar el uso de `ContinueWith` o `await` mediante la propiedad [`Result`](xref:System.Threading.Tasks.Task`1.Result) en los métodos. Esto se debe evitar para que pueda bloquear el subproceso de interfaz de usuario o incluso bloquear la aplicación.

## <a name="your-own-awaitable-methods"></a>Métodos propios que admiten await

Puede ejecutar código de forma asincrónica pasándolo a uno de los métodos [`Task.Run`](xref:System.Threading.Tasks.Task.Run(System.Action)). Puede llamar a `Task.Run` dentro de un método asincrónico que controla parte de la sobrecarga.

A continuación se describen los distintos patrones de `Task.Run`.

### <a name="the-basic-mandelbrot-set"></a>Conjunto de Mandelbrot básico

Para dibujar el conjunto de Mandelbrot en tiempo real, al biblioteca [**Xamarin.Forms.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) tiene una estructura [`Complex`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs) similar a la del espacio de nombres `System.Numerics`.

El ejemplo [**MandelbrotSet**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet) tiene un método `CalculateMandeblotAsync` en su archivo de código subyacente que calcula el conjunto de Mandelbrot básico en blanco y negro y utiliza [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) para colocarlo en un mapa de bits.

### <a name="marking-progress"></a>Marcar el progreso

Para notificar el progreso de un método asincrónico, puede crear una instancia de una clase [`Progress<T>`](xref:System.Progress`1) y definir el método asincrónico para tener un argumento de tipo [`IProgress<T>`](xref:System.IProgress`1). Esto se muestra en el ejemplo [**MandelbrotProgress**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress).

### <a name="cancelling-the-job"></a>Cancelación del trabajo

También puede escribir un método asincrónico para que sea cancelable. Comienza con una clase denominada [`CancellationTokenSource`](xref:System.Threading.CancellationTokenSource). La propiedad [`Token`](xref:System.Threading.CancellationTokenSource.Token) es un valor de tipo [`CancellationToken`](xref:System.Threading.CancellationToken). Esto se pasa a la función asincrónica. Un programa llama al método [`Cancel`](xref:System.Threading.CancellationTokenSource.Cancel) de `CancellationTokenSource` (generalmente en respuesta a una acción del usuario) para cancelar la función asincrónica.

El método asincrónico puede comprobar periódicamente la propiedad [`IsCancellationRequested`](xref:System.Threading.CancellationToken.IsCancellationRequested) de `CancellationToken` y salir si la propiedad es `true`, o simplemente llamar al método [`ThrowIfCancellationRequested`](xref:System.Threading.CancellationToken.ThrowIfCancellationRequested), en cuyo caso el método finaliza con [`OperationCancelledException`](xref:System.OperationCanceledException).

En el ejemplo [**MandelbrotCancellation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation) se muestra el uso de una función que se puede cancelar.

### <a name="an-mvvm-mandelbrot"></a>Un conjunto Mandelbrot con MVVM

El ejemplo [**MandelbrotXF**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF) tiene una interfaz de usuario más extensa y se basa principal en las clases [`MandelbrotModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs) y [`MandelbrotViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs):

[![Captura de pantalla triple de Mandelbrot X F](images/ch20fg13-small.png "Conjunto Mandelbrot con MVVM")](images/ch20fg13-large.png#lightbox "Conjunto Mandelbrot con MVVM")

## <a name="back-to-the-web"></a>Volver a la Web

La clase [`WebRequest`](xref:System.Net.WebRequest) usada en algunos ejemplos utiliza un protocolo asincrónico antiguo denominado modelo de programación asincrónica o APM. Puede convertir una clase de este tipo en el protocolo TAP moderno mediante uno de los métodos `FromAsync` de la clase [`TaskFactory`](xref:System.Threading.Tasks.TaskFactory`1). El ejemplo [**ApmToTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap) muestra este comportamiento.

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 20 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [Ejemplos del capítulo 20](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [Working with Files (Trabajo con archivos)](~/xamarin-forms/data-cloud/data/files.md)
