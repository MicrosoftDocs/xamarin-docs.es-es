---
title: Resumen del capítulo 20. E/S de archivo y Async
description: 'Crear Mobile Apps con Xamarin. Forms: Resumen del capítulo 20. E/S de archivo y Async'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D595862D-64FD-4C0D-B0AD-C1F440564247
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 283273e6ee28cc5cd1a61169f38bfcd1dd1726d8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771037"
---
# <a name="summary-of-chapter-20-async-and-file-io"></a>Resumen del capítulo 20. E/S de archivo y Async

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)

> [!NOTE] 
> Notas de esta página indican áreas donde se ha dividido Xamarin.Forms desde el material presentado en el libro.

 Una interfaz gráfica de usuario debe responder a eventos de entrada del usuario secuencialmente. Esto implica que todo el procesamiento de eventos de entrada del usuario se debe producir en un único subproceso, se suele denominado el *subproceso principal* o *el subproceso de interfaz de usuario*.

Los usuarios esperan interfaces gráficas de usuario tienen capacidad de respuesta. Esto significa que un programa debe procesar rápidamente los eventos de entrada del usuario. Si no es posible, procesamiento, a continuación, debe relegado a subprocesos secundarios de ejecución.

Varios programas de ejemplo en este libro se han usado el [ `WebRequest` ](xref:System.Net.WebRequest) clase. En esta clase la [ `BeginGetResponse` ](xref:System.Net.WebRequest.BeginGetResponse(System.AsyncCallback,System.Object)) método inicia un subproceso de trabajo, que llama a una función de devolución de llamada cuando se complete. Sin embargo, esa función de devolución de llamada se ejecuta en el subproceso de trabajo, por lo que el programa debe llamar a [ `Device.BeginInvokeOnMainThread` ](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) método para acceder a la interfaz de usuario.

> [!NOTE]
> Deben usar programas de Xamarin.Forms [ `HttpClient` ](xref:System.Net.Http.HttpClient) lugar [ `WebRequest` ](xref:System.Net.WebRequest) para tener acceso a archivos a través de internet. `HttpClient` admite operaciones asincrónicas.

Un enfoque más moderno para el procesamiento asincrónico está disponible en .NET y C#. Esto implica la [ `Task` ](xref:System.Threading.Tasks.Task) y [ `Task<TResult>` ](xref:System.Threading.Tasks.Task`1) clases y otros tipos en el [ `System.Threading` ](xref:System.Threading) y [ `System.Threading.Tasks` ](xref:System.Threading.Tasks) espacios de nombres, así como el C# 5.0 `async` y `await` palabras clave. Eso es lo que en este capítulo se centra en.

## <a name="from-callbacks-to-await"></a>En las devoluciones de llamada a await

El `Page` propia clase contiene tres métodos asincrónicos para mostrar cuadros de alerta:

- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) Devuelve un `Task` objeto
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String,System.String)) Devuelve un `Task<bool>` objeto
- [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) Devuelve un `Task<string>` objeto

El `Task` objetos indican que estos métodos implementan la tarea-based Asynchronous Pattern, conocido como TAP. Estos `Task` se devuelven objetos rápidamente desde el método. El `Task<T>` devolver valores constituyen una "promesa" que un valor de tipo `TResult` estará disponible cuando se complete la tarea. El `Task` valor devuelto indica una acción asincrónica que le completa pero no tiene ningún valor devuelto.

En todos estos casos, el `Task` está completa cuando el usuario descarte el cuadro de alerta.  

### <a name="an-alert-with-callbacks"></a>Una alerta con devoluciones de llamada

El [ **AlertCallbacks** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks) ejemplo muestra cómo controlar `Task<bool>` devuelven objetos y `Device.BeginInvokeOnMainThread` llamadas con métodos de devolución de llamada.

### <a name="an-alert-with-lambdas"></a>Una alerta con expresiones lambda

El [ **AlertLambdas** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas) ejemplo muestra cómo usar las funciones lambda anónima para el control `Task` y `Device.BeginInvokeOnMainThread` llamadas.  

### <a name="an-alert-with-await"></a>Una alerta con await

Un enfoque más sencillo implica la `async` y `await` palabras clave introducidas en C# 5. El [ **AlertAwait** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait) muestra su uso.

### <a name="an-alert-with-nothing"></a>Una alerta sin nada

Si el método asincrónico devuelve `Task` lugar `Task<TResult>`, a continuación, el programa no necesita usar cualquiera de estas técnicas, si no es necesario saber cuando se completa la tarea asincrónica. El [ **NothingAlert** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert) muestra esto.

### <a name="saving-program-settings-asynchronously"></a>Guardando la configuración del programa de forma asincrónica

El [ **SaveProgramChanges** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings) ejemplo muestra el uso de la [ `SavePropertiesAsync` ](xref:Xamarin.Forms.Application.SavePropertiesAsync) método `Application` para guardar la configuración del programa cuando cambien sin reemplazar el `OnSleep` método.

### <a name="a-platform-independent-timer"></a>Un temporizador de independiente de la plataforma

Es posible usar [ `Task.Delay` ](xref:System.Threading.Tasks.Task.Delay(System.Int32)) para crear un temporizador independiente de la plataforma. El [ **TaskDelayClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock) muestra esto.

## <a name="file-inputoutput"></a>Archivo de entrada/salida

Tradicionalmente, .NET [ `System.IO` ](xref:System.IO) espacio de nombres ha sido la fuente de la compatibilidad con E/S de archivos. Aunque algunos métodos en este espacio de nombres admiten las operaciones asincrónicas, la mayoría no lo hacen. El espacio de nombres también admite varias llamadas a métodos simples que realizan funciones de E/S de archivo sofisticadas.

### <a name="good-news-and-bad-news"></a>Buenas noticias y malas noticias

Todas las plataformas compatibles con almacenamiento local de aplicación de soporte técnico de Xamarin.Forms &mdash; almacenamiento privado a la aplicación.

Las bibliotecas de Xamarin.iOS y Xamarin.Android incluyen una versión de .NET que Xamarin ha diseñada expresamente para estas dos plataformas. Estos incluyen clases de `System.IO` que puede usar para realizar operaciones de E/S de archivo con el almacenamiento local para la aplicación en estas dos plataformas.

Sin embargo, si busca estos `System.IO` clases en una PCL de Xamarin.Forms, no encontrarlos. El problema es ese archivo Microsoft reformado completamente E/S para la API de Windows en tiempo de ejecución. No usan programas como destino la plataforma Universal de Windows, Windows Phone 8.1 y Windows 8.1 `System.IO` para E/S de archivos.

Esto significa que deberá usar el [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) (primero se describe en [ **capítulo 9. Llamadas a API específicas de la plataforma** ](chapter09.md) para implementar la E/S de archivos.

> [!NOTE]
> Bibliotecas de clases portables se han reemplazado con las bibliotecas .NET Standard 2.0 y .NET Standard 2.0 es compatible con [ `System.IO` ](xref:System.IO) tipos para todas las plataformas de Xamarin.Forms. Ya no es necesario usar un `DependencyService` para la mayoría de las tareas de E/S de archivos. Consulte [manejo de archivos en Xamarin.Forms](~/xamarin-forms/data-cloud/data/files.md) un enfoque más moderno para E/S de archivos.

### <a name="a-first-shot-at-cross-platform-file-io"></a>Un primer intento en E/S de archivos entre plataformas

El [ **TextFileTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout) ejemplo define un [ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs) interfaz para E/S de archivos y las implementaciones de esta interfaz en todas las plataformas. Sin embargo, las implementaciones en tiempo de ejecución de Windows no funcionan con los métodos de esta interfaz porque los métodos de E/S de archivo de Windows en tiempo de ejecución son asincrónicos.

### <a name="accommodating-windows-runtime-file-io"></a>Adaptación de E/S de archivos en tiempo de ejecución de Windows

Programas que se ejecutan en el tiempo de ejecución de Windows usan clases en el [ `Windows.Storage` ](/uwp/api/Windows.Storage) y [ `Windows.Storage.Streams` ](/uwp/api/Windows.Storage.Streams) espacios de nombres para E/S de archivos, incluido el almacenamiento local de aplicación. Dado que Microsoft ha determinado que cualquier operación que requiere que más de 50 milisegundos deben ser asincrónicos para evitar bloquear el subproceso de interfaz de usuario, estos métodos de E/S de archivo principalmente son asincrónicos.

El código que muestra este nuevo enfoque estará en una biblioteca para que se pueden usar otras aplicaciones.

## <a name="platform-specific-libraries"></a>Bibliotecas específicas de plataforma

Resulta ventajoso almacenar código reutilizable en las bibliotecas. Esto es obviamente más difícil cuando son distintas partes del código reutilizable para los sistemas operativos completamente diferentes.

El [ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) solución muestra un enfoque. Esta solución contiene siete proyectos diferentes:

- [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform), una PCL normal de Xamarin.Forms
- [**Xamarin.FormsBook.Platform.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS), una biblioteca de clases de iOS
- [**Xamarin.FormsBook.Platform.Android**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android), una biblioteca de clases de Android
- [**Xamarin.FormsBook.Platform.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP), una biblioteca de clases de Windows Universal
- [**Xamarin.FormsBook.Platform.WinRT**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT), un proyecto compartido para el código que es común a todas las plataformas de Windows

Todos los proyectos de plataforma individuales (con la excepción de **Xamarin.FormsBook.Platform.WinRT**) tienen referencias a **Xamarin.FormsBook.Platform**. Los tres proyectos de Windows tienen una referencia a **Xamarin.FormsBook.Platform.WinRT**.

Todos los proyectos contienen una estática `Toolkit.Init` método para asegurarse de que se carga la biblioteca directamente no hace referencia a un proyecto en una solución de aplicación de Xamarin.Forms.

El **Xamarin.FormsBook.Platform** contiene el nuevo proyecto [ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs) interfaz. Ahora, todos los métodos tienen nombres con `Async` sufijos y retorno `Task` objetos.

El **Xamarin.FormsBook.Platform.WinRT** proyecto contiene el [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) clase para el tiempo de ejecución de Windows.

El **Xamarin.FormsBook.Platform.iOS** proyecto contiene el [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs) clase para iOS. Estos métodos ahora deben ser asincrónicos. Algunos de los métodos utilizan las versiones asincrónicas de los métodos definidos en `StreamWriter` y `StreamReader`: [ `WriteAsync` ](xref:System.IO.StreamWriter.WriteAsync(System.String)) y [ `ReadToEndAsync` ](xref:System.IO.StreamReader.ReadToEndAsync). Que otras convierten un resultado para un `Task` objeto utilizando el [ `FromResult` ](xref:System.Threading.Tasks.Task.FromResult*) método.

El **Xamarin.FormsBook.Platform.Android** proyecto contiene un texto similar [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs) clase para Android.

El **Xamarin.FormsBook.Platform** proyecto también contiene un [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs) clase que facilita el uso de la `DependencyService` objeto.

Para usar estas bibliotecas, una solución de aplicación debe incluir todos los proyectos de la **Xamarin.FormsBook.Platform** solución y cada uno de los proyectos de aplicación deben tener una referencia a la biblioteca correspondiente en  **Xamarin.FormsBook.Platform**.

El [ **TextFileAsync** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync) solución muestra cómo usar el **Xamarin.FormsBook.Platform** bibliotecas. Cada uno de los proyectos tiene una llamada a `Toolkit.Init`. La aplicación hace uso de la E/S de archivos asincrónica funciones.

### <a name="keeping-it-in-the-background"></a>Mantenerla en segundo plano

Los métodos de bibliotecas que realizan llamadas a varios métodos asincrónicos &mdash; , como el `WriteFileAsync` y `ReadFileASync` métodos en el tiempo de ejecución de Windows [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) clase &mdash; puede realizarse un poco más eficaz mediante el uso de la [ `ConfigureAwait` ](xref:System.Threading.Tasks.Task`1.ConfigureAwait(System.Boolean)) método para evitar cambiar al subproceso de la interfaz de usuario.

### <a name="dont-block-the-ui-thread"></a>No bloquea el subproceso de interfaz de usuario.

A veces es tentador para evitar el uso de `ContinueWith` o `await` utilizando el [ `Result` ](xref:System.Threading.Tasks.Task`1.Result) propiedad en los métodos. Esto se debe evitar para que pueda bloquear el subproceso de interfaz de usuario o incluso bloquear la aplicación.

## <a name="your-own-awaitable-methods"></a>Sus propios métodos que admite await

Puede ejecutar algún código de forma asincrónica al pasarlo a uno de los [ `Task.Run` ](xref:System.Threading.Tasks.Task.Run(System.Action)) métodos. Puede llamar a `Task.Run` dentro de un método asincrónico que se encarga de parte de la sobrecarga.

Los distintos `Task.Run` patrones se describen a continuación.

### <a name="the-basic-mandelbrot-set"></a>El conjunto de Mandelbrot básico

Para dibujar el conjunto Mandelbrot establecido en tiempo real, el [ **Xamarin.Forms.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca tiene un [ `Complex` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs) estructura similar a la de la `System.Numerics` espacio de nombres.

El [ **MandelbrotSet** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet) ejemplo tiene un `CalculateMandeblotAsync` método en su archivo de código subyacente que calcula el conjunto de Mandelbrot en blanco y negro básico y lo usa [ `BmpMaker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs)colocarlo en un mapa de bits.

### <a name="marking-progress"></a>Marcar el curso

Para notificar el progreso de un método asincrónico, puede crear instancias de un [ `Progress<T>` ](xref:System.Progress`1) clase y definir el método asincrónico para tener un argumento de tipo [ `IProgress<T>` ](xref:System.IProgress`1). Esto se muestra en el [ **MandelbrotProgress** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress) ejemplo.

### <a name="cancelling-the-job"></a>Cancelando el trabajo

También puede escribir un método asincrónico para que sea cancelable. Comenzar con una clase denominada [ `CancellationTokenSource` ](xref:System.Threading.CancellationTokenSource). El [ `Token` ](xref:System.Threading.CancellationTokenSource.Token) propiedad es un valor de tipo [ `CancellationToken` ](xref:System.Threading.CancellationToken). Esto se pasa a la función asincrónica. Un programa llama a la [ `Cancel` ](xref:System.Threading.CancellationTokenSource.Cancel) método `CancellationTokenSource` (normalmente en respuesta a una acción del usuario) para cancelar la función asincrónica.

El método asincrónico puede comprobar periódicamente el [ `IsCancellationRequested` ](xref:System.Threading.CancellationToken.IsCancellationRequested) propiedad de `CancellationToken` y salir si la propiedad es `true`, o simplemente llamar a la [ `ThrowIfCancellationRequested` ](xref:System.Threading.CancellationToken.ThrowIfCancellationRequested) método, en cuyo caso el método termina con un [ `OperationCancelledException` ](xref:System.OperationCanceledException).

El [ **MandelbrotCancellation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation) ejemplo muestra el uso de una función cancelable.

### <a name="an-mvvm-mandelbrot"></a>Un Mandelbrot MVVM

El [ **MandelbrotXF** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF) ejemplo tiene una interfaz de usuario más amplia y se basa principalmente en un [ `MandelbrotModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs) y [ `MandelbrotViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs)clases:

[![Captura de pantalla triple de Mandelbrot X F](images/ch20fg13-small.png "MVVM Mandelbrot")](images/ch20fg13-large.png#lightbox "Mandelbrot MVVM")

## <a name="back-to-the-web"></a>Volver a la web

El [ `WebRequest` ](xref:System.Net.WebRequest) clase usada en algunos ejemplos usa un protocolo asincrónico anticuado denominado APM o modelo de programación asincrónica. Puede convertir este tipo de clase en el protocolo TAP moderno mediante uno de los `FromAsync` métodos en el [ `TaskFactory` ](xref:System.Threading.Tasks.TaskFactory`1) clase. El [ **ApmToTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap) muestra esto.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 20 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [Capítulo 20 muestras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [Trabajar con archivos](~/xamarin-forms/data-cloud/data/files.md)
