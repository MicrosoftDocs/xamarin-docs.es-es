---
title: Distribuidor de trabajo de Firebase
description: En esta guía se describe cómo programar el trabajo en segundo plano mediante la biblioteca de distribuidores de trabajos de Firebase de Google.
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/05/2018
ms.openlocfilehash: 280fe11f935db0a364f3342b22bb9544cdda1e6d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020246"
---
# <a name="firebase-job-dispatcher"></a>Distribuidor de trabajo de Firebase

_En esta guía se describe cómo programar el trabajo en segundo plano mediante la biblioteca de distribuidores de trabajos de Firebase de Google._

## <a name="overview"></a>Información general

Una de las mejores formas de mantener la respuesta de una aplicación Android al usuario es asegurarse de que el trabajo complejo o de larga ejecución se realiza en segundo plano. Sin embargo, es importante que el trabajo en segundo plano no afecte negativamente a la experiencia del usuario con el dispositivo. 

Por ejemplo, un trabajo en segundo plano puede sondear un sitio web cada tres o cuatro minutos para consultar los cambios en un conjunto de datos determinado. Parece benigno, pero tendría un impacto desastroso en la duración de la batería. La aplicación reactivará repetidamente el dispositivo, elevará la CPU a un estado de energía mayor, apagará las radios, realizará las solicitudes de red y, a continuación, procesará los resultados. Es peor porque el dispositivo no se apagará inmediatamente y volverá al estado inactivo de baja energía. Un trabajo en segundo plano mal programado puede mantener involuntariamente el dispositivo en un estado con requisitos de energía innecesarios y excesivos. Esta actividad aparentemente inocente (sondeo de un sitio web) hará que el dispositivo no se pueda usar en un período de tiempo relativamente corto.

Android proporciona las siguientes API para ayudar a realizar el trabajo en segundo plano, pero no basta para la programación inteligente de trabajos. 

- Los servicios de **[intención](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash; servicios de intención son excelentes para realizar el trabajo, pero no proporcionan ninguna manera de programar el trabajo.
- **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)** &ndash; estas API solo permiten programar el trabajo, pero no proporcionan ninguna forma de realizar realmente el trabajo. Además, el AlarmManager solo permite restricciones basadas en el tiempo, lo que significa que se producirá una alarma en un momento determinado o transcurrido un período de tiempo determinado. 
- **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)** &ndash; JobSchedule es una excelente API que funciona con el sistema operativo para programar trabajos. Sin embargo, solo está disponible para las aplicaciones Android que tienen como destino el nivel de API 21 o posterior. 
- Los **[receptores de difusión](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; una aplicación Android pueden configurar receptores de difusión para que realicen el trabajo en respuesta a eventos de todo el sistema o a intenciones. Sin embargo, los receptores de difusión no proporcionan ningún control sobre cuándo se debe ejecutar el trabajo. Además, los cambios en el sistema operativo Android se restringirán cuando los receptores de difusión funcionen o los tipos de trabajo a los que puedan responder. 

Hay dos características clave para realizar eficazmente el trabajo en segundo plano (a veces denominado _trabajo en segundo plano_ o _trabajo_):

1. **Programar de forma inteligente el trabajo** &ndash; es importante que cuando una aplicación esté realizando trabajo en segundo plano, lo haga como un buen ciudadano. Idealmente, la aplicación no debería exigir que se ejecute un trabajo. En su lugar, la aplicación debe especificar las condiciones que se deben cumplir para cuando se pueda ejecutar el trabajo y, a continuación, programar ese trabajo para que se ejecute cuando se cumplan las condiciones. Esto permite que Android realice el trabajo de forma inteligente. Por ejemplo, las solicitudes de red se pueden procesar por lotes para ejecutarse todas al mismo tiempo con el fin de hacer el uso máximo de la sobrecarga implicada en las redes.
2. La **encapsulación del trabajo** &ndash; el código para realizar el trabajo en segundo plano debe encapsularse en un componente discreto que se pueda ejecutar independientemente de la interfaz de usuario y será relativamente fácil de volver a programar si el trabajo no se completa para algunos debido.

El distribuidor de trabajos de Firebase es una biblioteca de Google que proporciona una API fluida para simplificar la programación del trabajo en segundo plano. Está pensado para ser el sustituto de Google Cloud Manager. El distribuidor de trabajos de Firebase consta de las siguientes API:

- Una `Firebase.JobDispatcher.JobService` es una clase abstracta que se debe extender con la lógica que se ejecutará en el trabajo en segundo plano.
- Una `Firebase.JobDispatcher.JobTrigger` declara cuando se debe iniciar el trabajo. Esto suele expresarse como una ventana de tiempo, por ejemplo, esperar al menos 30 segundos antes de iniciar el trabajo, pero ejecutar el trabajo en 5 minutos.
- Un `Firebase.JobDispatcher.RetryStrategy` contiene información sobre lo que se debe hacer cuando un trabajo no se ejecuta correctamente. La estrategia de reintento especifica cuánto tiempo se debe esperar antes de intentar volver a ejecutar el trabajo. 
- Un `Firebase.JobDispatcher.Constraint` es un valor opcional que describe una condición que se debe cumplir antes de que se pueda ejecutar el trabajo, por ejemplo, el dispositivo se encuentra en una red desmedida o en un proceso de carga.
- El `Firebase.JobDispatcher.Job` es una API que unifica las API anteriores de en una unidad de trabajo que pueden ser programadas por el `JobDispatcher`. La clase `Job.Builder` se usa para crear una instancia de un `Job`.
- Una `Firebase.JobDispatcher.JobDispatcher` utiliza las tres API anteriores para programar el trabajo con el sistema operativo y proporcionar una manera de cancelar los trabajos, si es necesario.

Para programar el trabajo con el distribuidor de trabajos de Firebase, una aplicación de Xamarin. Android debe encapsular el código en un tipo que extienda la clase `JobService`. `JobService` tiene tres métodos de ciclo de vida a los que se puede llamar durante la vigencia del trabajo:

- **`bool OnStartJob(IJobParameters parameters)`** &ndash; este método es donde se producirá el trabajo y siempre se debe implementar. Se ejecuta en el subproceso principal. Este método devolverá `true` si hay trabajo restante o `false` si se realiza el trabajo. 
- **`bool OnStopJob(IJobParameters parameters)`** &ndash; se llama a este método cuando el trabajo se detiene por alguna razón. Debe devolver `true` si el trabajo se debe volver a programar para más tarde.
- **`JobFinished(IJobParameters parameters, bool needsReschedule)`** &ndash; se llama a este método cuando el `JobService` ha finalizado cualquier trabajo asincrónico. 

Para programar un trabajo, la aplicación creará una instancia de un objeto `JobDispatcher`. A continuación, se usa una `Job.Builder` para crear un objeto `Job`, que se proporciona al `JobDispatcher` que intentará programar el trabajo para que se ejecute.

En esta guía se explica cómo agregar el distribuidor de trabajos de Firebase a una aplicación de Xamarin. Android y cómo usarlo para programar el trabajo en segundo plano.

## <a name="requirements"></a>Requisitos

El distribuidor del trabajo de Firebase requiere el nivel de API de Android 9 o superior. La biblioteca de distribuidores de trabajos de Firebase se basa en algunos componentes proporcionados por Google Play Services; el dispositivo debe tener Google Play Services instalado.

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>Uso de la biblioteca de distribuidores de trabajos de Firebase en Xamarin. Android

Para empezar a trabajar con el distribuidor de trabajos de Firebase, agregue primero el [paquete de NuGet Xamarin. Firebase. JobDispatcher](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher) al proyecto de Xamarin. Android. Busque el paquete **Xamarin. Firebase. JobDispatcher** en el administrador de paquetes NuGet (que todavía está en versión preliminar).

Después de agregar la biblioteca de distribuidores de trabajos de Firebase, cree una `JobService` clase y, a continuación, programe para que se ejecute con una instancia del `FirebaseJobDispatcher`.

### <a name="creating-a-jobservice"></a>Creación de un JobService

Todo el trabajo realizado por la biblioteca de distribución de trabajos de Firebase debe realizarse en un tipo que extienda la `Firebase.JobDispatcher.JobService` clase abstracta. Crear un `JobService` es muy similar a crear un `Service` con el marco de trabajo de Android: 

1. Extender la clase `JobService`
2. Decora la subclase con el `ServiceAttribute`. Aunque no es estrictamente necesario, se recomienda establecer explícitamente el parámetro `Name` para ayudar con la depuración de la `JobService`. 
3. Agregue un `IntentFilter` para declarar el `JobService` en **archivo AndroidManifest. XML**. Esto también ayudará a la biblioteca de distribuidores de trabajos de Firebase a ubicar e invocar el `JobService`.

El código siguiente es un ejemplo de la `JobService` más sencilla para una aplicación, mediante el uso de la TPL para realizar de forma asincrónica algún trabajo:

```csharp
[Service(Name = "com.xamarin.fjdtestapp.DemoJob")]
[IntentFilter(new[] {FirebaseJobServiceIntent.Action})]
public class DemoJob : JobService
{
    static readonly string TAG = "X:DemoService";

    public override bool OnStartJob(IJobParameters jobParameters)
    {
        Task.Run(() =>
        {
            // Work is happening asynchronously (code omitted)
                       
        });

        // Return true because of the asynchronous work
        return true;  
    }

    public override bool OnStopJob(IJobParameters jobParameters)
    {
        Log.Debug(TAG, "DemoJob::OnStartJob");
        // nothing to do.
        return false;
    }
}
```

### <a name="creating-a-firebasejobdispatcher"></a>Creación de un FirebaseJobDispatcher

Antes de que se pueda programar cualquier trabajo, es necesario crear un objeto de `Firebase.JobDispatcher.FirebaseJobDispatcher`. La `FirebaseJobDispatcher` es responsable de programar un `JobService`. El siguiente fragmento de código es una manera de crear una instancia de la `FirebaseJobDispatcher`: 

 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

En el fragmento de código anterior, el `GooglePlayDriver` es una clase que ayuda a la `FirebaseJobDispatcher` a interactuar con algunas de las API de programación en Google Play Services en el dispositivo. El parámetro `context` es cualquier `Context`de Android, como una actividad. Actualmente, el `GooglePlayDriver` es la única implementación de `IDriver` en la biblioteca de distribuidores de trabajos de Firebase. 

El enlace de Xamarin. Android para el distribuidor del trabajo de Firebase proporciona un método de extensión para crear una `FirebaseJobDispatcher` a partir de la `Context`: 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

Una vez creada la instancia de `FirebaseJobDispatcher`, es posible crear una `Job` y ejecutar el código en la clase `JobService`. El `Job` se crea mediante un objeto `Job.Builder` y se explicará en la sección siguiente.

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>Creación de un trabajo de Firebase. JobDispatcher. Job con Job. Builder

La clase `Firebase.JobDispatcher.Job` es responsable de encapsular los metadatos necesarios para ejecutar un `JobService`. @ No__t_0_ contiene información como cualquier restricción que se debe cumplir antes de que se pueda ejecutar el trabajo, si el `Job` es recurrente o cualquier desencadenador que provoque que se ejecute el trabajo.  Como mínimo, un `Job` debe tener una _etiqueta_ (una cadena única que identifique el trabajo en el `FirebaseJobDispatcher`) y el tipo de la `JobService` que se debe ejecutar. El distribuidor del trabajo de Firebase creará una instancia del `JobService` cuando sea el momento de ejecutar el trabajo.  Se crea un `Job` mediante una instancia de la clase `Firebase.JobDispatcher.Job.JobBuilder`. 

El siguiente fragmento de código es el ejemplo más sencillo de cómo crear un `Job` mediante el enlace de Xamarin. Android:

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

El `Job.Builder` realizará algunas comprobaciones de validación básicas en los valores de entrada del trabajo. Se producirá una excepción si no es posible que el `Job.Builder` cree un `Job`.  El `Job.Builder` creará una `Job` con los valores predeterminados siguientes:

- La _duración_ de un `Job`(cuánto tiempo se programará para ejecutarse) es solo hasta que el dispositivo se reinicie &ndash; una vez que el dispositivo se reinicie, se perderá la `Job`.
- Una `Job` no se repite &ndash; solo se ejecutará una vez.
- Un `Job` se programará para que se ejecute lo antes posible.
- La estrategia de reintentos predeterminada para una `Job` es usar un _retroceso exponencial_ (se describe con más detalle a continuación en la sección [configuración de un RetryStrategy](#Setting_a_RetryStrategy)).

### <a name="scheduling-a-job"></a>Programar un trabajo

Después de crear el `Job`, debe programarse con el `FirebaseJobDispatcher` antes de ejecutarse. Hay dos métodos para programar un `Job`:

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

El valor devuelto por `FirebaseJobDispatcher.Schedule` será uno de los siguientes valores enteros:

- `FirebaseJobDispatcher.ScheduleResultSuccess` &ndash; la `Job` se programó correctamente.
- `FirebaseJobDispatcher.ScheduleResultUnknownError` &ndash; problema desconocido que impidió la programación de la `Job`.
- `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable` &ndash; se usó un `IDriver` no válido o el `IDriver` de algún modo no estaba disponible. 
- `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger` &ndash; no se admitía la `Trigger`.
- `FirebaseJobDispatcher.ScheduleResultBadService` &ndash; el servicio no está configurado correctamente o no está disponible.

### <a name="configuring-a-job"></a>Configuración de un trabajo

Es posible personalizar un trabajo. Entre los ejemplos de cómo se puede personalizar un trabajo se incluyen los siguientes:

- [Pasar parámetros a un trabajo](#Passing_Parameters_to_a_Job) &ndash; un `Job` puede requerir valores adicionales para realizar su trabajo, por ejemplo, la descarga de un archivo.
- [Establezca restricciones](#Setting_Constraints) &ndash; puede que sea necesario ejecutar solo un trabajo cuando se cumplan ciertas condiciones. Por ejemplo, ejecute solo una `Job` cuando el dispositivo se esté cargando. 
- [Especifique cuándo debe ejecutarse un `Job`](#Setting_Job_Triggers) &ndash; el distribuidor de trabajos de Firebase permite a las aplicaciones especificar una hora en la que se debe ejecutar el trabajo.  
- [Declare una estrategia de reintento para trabajos con error](#Setting_a_RetryStrategy) &ndash; una _estrategia de reintento_ proporciona instrucciones para la `FirebaseJobDispatcher` sobre qué hacer con `Jobs` que no se completan correctamente. 

Cada uno de estos temas se explicará más en las secciones siguientes.

<a name="Passing_Parameters_to_a_Job" />

#### <a name="passing-parameters-to-a-job"></a>Pasar parámetros a un trabajo

Los parámetros se pasan a un trabajo mediante la creación de una `Bundle` que se pasa junto con el método `Job.Builder.SetExtras`:

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

Se obtiene acceso a la `Bundle` desde la propiedad `IJobParameters.Extras` en el método `OnStartJob`:

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints" />

#### <a name="setting-constraints"></a>Establecer restricciones

Las restricciones pueden ayudar a reducir los costos o la carga de la batería en el dispositivo. La clase `Firebase.JobDispatcher.Constraint` define estas restricciones como valores enteros:

- `Constraint.OnUnmeteredNetwork` &ndash; ejecutar solo el trabajo cuando el dispositivo está conectado a una red desmedida. Esto resulta útil para evitar que el usuario incurra en cargos de datos.
- `Constraint.OnAnyNetwork` &ndash; ejecutar el trabajo en cualquier red a la que esté conectado el dispositivo. Si se especifica junto con `Constraint.OnUnmeteredNetwork`, este valor tendrá prioridad.
- `Constraint.DeviceCharging` &ndash; ejecutar el trabajo solo cuando el dispositivo se está cargando.

Las restricciones se establecen con el método `Job.Builder.SetConstraint`: 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers" />

El `JobTrigger` proporciona instrucciones al sistema operativo sobre cuándo debe iniciarse el trabajo. Una `JobTrigger` tiene una _ventana en ejecución_ que define una hora programada para el momento en que se debe ejecutar el `Job`. La ventana de ejecución tiene un valor de _ventana de inicio_ y un valor de _ventana de finalización_ . La ventana de inicio es el número de segundos que el dispositivo debe esperar antes de ejecutar el trabajo y el valor de la ventana final es el número máximo de segundos que se debe esperar antes de ejecutar el `Job`. 

Se puede crear un `JobTrigger` con el método `Firebase.Jobdispatcher.Trigger.ExecutionWindow`.  Por ejemplo `Trigger.ExecutionWindow(15,60)` significa que el trabajo debe ejecutarse entre 15 y 60 segundos desde la programación. El método `Job.Builder.SetTrigger` se utiliza para 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

La `JobTrigger` predeterminada para un trabajo se representa mediante el valor `Trigger.Now`, que especifica que un trabajo se ejecutará lo antes posible después de la programación.

<a name="Setting_a_RetryStrategy" />

#### <a name="setting-a-retrystrategy"></a>Establecer un RetryStrategy

El `Firebase.JobDispatcher.RetryStrategy` se usa para especificar la cantidad de un retraso que debe usar un dispositivo antes de intentar volver a ejecutar un trabajo con error. Una `RetryStrategy` tiene una _Directiva_, que define el algoritmo de base de tiempo que se utilizará para volver a programar el trabajo con errores y una ventana de ejecución que especifica una ventana en la que se debe programar el trabajo. Esta _ventana de reprogramación_ se define con dos valores. El primer valor es el número de segundos que hay que esperar antes de volver a programar el trabajo (el valor de _retroceso inicial_ ) y el segundo número es el número máximo de segundos antes de que se deba ejecutar el trabajo (el valor de _retroceso máximo_ ). 

Los dos tipos de directivas de reintentos se identifican mediante estos valores int:

- `RetryStrategy.RetryPolicyExponential` &ndash; una directiva de _retroceso exponencial_ aumentará exponencialmente el valor de retroceso inicial después de cada error. La primera vez que se produce un error en un trabajo, la biblioteca esperará el intervalo de _initial especificado antes de volver a programar el trabajo &ndash; ejemplo 30 segundos. La segunda vez que se produce un error en el trabajo, la biblioteca esperará al menos 60 segundos antes de intentar ejecutar el trabajo. Después del tercer intento fallido, la biblioteca esperará 120 segundos, etc. Los `RetryStrategy` predeterminados para la biblioteca de distribuidores de trabajos de Firebase se representan mediante el objeto `RetryStrategy.DefaultExponential`. Tiene un retroceso inicial de 30 segundos y un retroceso máximo de 3600 segundos.
- `RetryStrategy.RetryPolicyLinear` &ndash; esta estrategia es una _interrupción lineal_ de que el trabajo se debe volver a programar para que se ejecute a intervalos establecidos (hasta que se realice correctamente). El retroceso lineal es más adecuado para el trabajo que debe realizarse lo antes posible o para los problemas que se resolverán rápidamente. La biblioteca de distribuidores de trabajos de Firebase define una `RetryStrategy.DefaultLinear` que tiene una ventana de reprogramación de al menos 30 segundos y hasta 3600 segundos.

Es posible definir un `RetryStrategy` personalizado con el método `FirebaseJobDispatcher.NewRetryStrategy`. Toma tres parámetros:

1. `int policy` &ndash; la _Directiva_ es uno de los valores de `RetryStrategy` anteriores, `RetryStrategy.RetryPolicyLinear`o `RetryStrategy.RetryPolicyExponential`.
2. `int initialBackoffSeconds` &ndash; el _retroceso inicial_ es un retraso, en segundos, que es necesario antes de intentar volver a ejecutar el trabajo. El valor predeterminado es de 30 segundos. 
3. `int maximumBackoffSeconds` &ndash; el valor de _retroceso máximo_ declara el número máximo de segundos de retraso antes de intentar ejecutar el trabajo de nuevo. El valor predeterminado es 3600 segundos. 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>Cancelar un trabajo

Es posible cancelar todos los trabajos programados o simplemente un único trabajo mediante el método `FirebaseJobDispatcher.CancelAll()` o el método `FirebaseJobDispatcher.Cancel(string)`:

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

Cualquier método devolverá un valor entero:

- `FirebaseJobDispatcher.CancelResultSuccess` &ndash; el trabajo se canceló correctamente.
- `FirebaseJobDispatcher.CancelResultUnknownError` &ndash; un error impidió que se cancelara el trabajo.
- `FirebaseJobDispatcher.CancelResult.NoDriverAvailable` &ndash; la `FirebaseJobDispatcher` no puede cancelar el trabajo porque no hay ningún `IDriver` válido disponible.

## <a name="summary"></a>Resumen

En esta guía se describe cómo usar el distribuidor de trabajos de Firebase para realizar el trabajo en segundo plano de forma inteligente. Se ha explicado cómo encapsular el trabajo que se va a realizar como un `JobService` y cómo usar el `FirebaseJobDispatcher` para programar ese trabajo, especificando los criterios con un `JobTrigger` y cómo se deben controlar los errores con un `RetryStrategy`.

## <a name="related-links"></a>Vínculos relacionados

- [Xamarin. Firebase. JobDispatcher en NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [firebase-job-Dispatcher en GitHub](https://github.com/firebase/firebase-jobdispatcher-android)
- [Enlace Xamarin. Firebase. JobDispatcher](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [Programación de trabajos inteligentes](https://developer.android.com/topic/performance/scheduling.html)
- [Optimizaciones de memoria y batería de Android: e/s de Google 2016 (vídeo)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
