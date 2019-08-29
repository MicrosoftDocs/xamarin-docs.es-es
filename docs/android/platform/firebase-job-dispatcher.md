---
title: Distribuidor de trabajo de Firebase
description: En esta guía se describe cómo programar el trabajo en segundo plano mediante la biblioteca de distribuidores de trabajos de Firebase de Google.
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/05/2018
ms.openlocfilehash: c0a35414cce6ff9981ad7825c8158a2f6f707585
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119492"
---
# <a name="firebase-job-dispatcher"></a>Distribuidor de trabajo de Firebase

_En esta guía se describe cómo programar el trabajo en segundo plano mediante la biblioteca de distribuidores de trabajos de Firebase de Google._


## <a name="overview"></a>Información general

Una de las mejores formas de mantener la respuesta de una aplicación Android al usuario es asegurarse de que el trabajo complejo o de larga ejecución se realiza en segundo plano. Sin embargo, es importante que el trabajo en segundo plano no afecte negativamente a la experiencia del usuario con el dispositivo. 

Por ejemplo, un trabajo en segundo plano puede sondear un sitio web cada tres o cuatro minutos para consultar los cambios en un conjunto de datos determinado. Parece benigno, pero tendría un impacto desastroso en la duración de la batería. La aplicación reactivará repetidamente el dispositivo, elevará la CPU a un estado de energía mayor, apagará las radios, realizará las solicitudes de red y, a continuación, procesará los resultados. Es peor porque el dispositivo no se apagará inmediatamente y volverá al estado inactivo de baja energía. Un trabajo en segundo plano mal programado puede mantener involuntariamente el dispositivo en un estado con requisitos de energía innecesarios y excesivos. Esta actividad aparentemente inocente (sondeo de un sitio web) hará que el dispositivo no se pueda usar en un período de tiempo relativamente corto.

Android proporciona las siguientes API para ayudar a realizar el trabajo en segundo plano, pero no basta para la programación inteligente de trabajos. 

- **[Servicios de intención](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash; Los servicios de intención son excelentes para realizar el trabajo; sin embargo, no proporcionan ninguna manera de programar el trabajo.
- **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)** &ndash; Estas API solo permiten programar el trabajo, pero no proporcionan ninguna forma de realizar realmente el trabajo. Además, el AlarmManager solo permite restricciones basadas en el tiempo, lo que significa que se producirá una alarma en un momento determinado o transcurrido un período de tiempo determinado. 
- **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)** &ndash; JobSchedule es una excelente API que funciona con el sistema operativo para programar trabajos. Sin embargo, solo está disponible para las aplicaciones Android que tienen como destino el nivel de API 21 o posterior. 
- **[Receptores de difusión](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; Una aplicación Android puede configurar receptores de difusión para que realicen el trabajo en respuesta a eventos de todo el sistema o a intenciones. Sin embargo, los receptores de difusión no proporcionan ningún control sobre cuándo se debe ejecutar el trabajo. Además, los cambios en el sistema operativo Android se restringirán cuando los receptores de difusión funcionen o los tipos de trabajo a los que puedan responder. 

Hay dos características clave para realizar eficazmente el trabajo en segundo plano (a veces denominado _trabajo en segundo plano_ o _trabajo_):

1. **Programación inteligente del trabajo** &ndash; Es importante que cuando una aplicación esté trabajando en segundo plano, como un buen ciudadano. Idealmente, la aplicación no debería exigir que se ejecute un trabajo. En su lugar, la aplicación debe especificar las condiciones que se deben cumplir para cuando se pueda ejecutar el trabajo y, a continuación, programar ese trabajo para que se ejecute cuando se cumplan las condiciones. Esto permite que Android realice el trabajo de forma inteligente. Por ejemplo, las solicitudes de red se pueden procesar por lotes para ejecutarse todas al mismo tiempo con el fin de hacer el uso máximo de la sobrecarga implicada en las redes.
2. **Encapsular el trabajo** &ndash; El código para realizar el trabajo en segundo plano debe encapsularse en un componente discreto que se pueda ejecutar independientemente de la interfaz de usuario y será relativamente fácil de volver a programar si el trabajo no se completa por algún motivo.

El distribuidor de trabajos de Firebase es una biblioteca de Google que proporciona una API fluida para simplificar la programación del trabajo en segundo plano. Está pensado para ser el sustituto de Google Cloud Manager. El distribuidor de trabajos de Firebase consta de las siguientes API:

- Una `Firebase.JobDispatcher.JobService` es una clase abstracta que se debe extender con la lógica que se ejecutará en el trabajo en segundo plano.
- `Firebase.JobDispatcher.JobTrigger` Declara cuando se debe iniciar el trabajo. Esto suele expresarse como una ventana de tiempo, por ejemplo, esperar al menos 30 segundos antes de iniciar el trabajo, pero ejecutar el trabajo en 5 minutos.
- `Firebase.JobDispatcher.RetryStrategy` Contiene información acerca de lo que se debe hacer cuando un trabajo no se ejecuta correctamente. La estrategia de reintento especifica cuánto tiempo se debe esperar antes de intentar volver a ejecutar el trabajo. 
- Un `Firebase.JobDispatcher.Constraint` es un valor opcional que describe una condición que se debe cumplir antes de que se pueda ejecutar el trabajo, como que el dispositivo se encuentra en una red desmedida o en un proceso de carga.
- Es una API que unifica las API anteriores de en una unidad de trabajo que puede ser programada `JobDispatcher`por. `Firebase.JobDispatcher.Job` La `Job.Builder` clase se utiliza para crear una instancia `Job`de un.
- `Firebase.JobDispatcher.JobDispatcher` Utiliza las tres API anteriores para programar el trabajo con el sistema operativo y proporcionar una manera de cancelar los trabajos, si es necesario.

Para programar el trabajo con el distribuidor de trabajos de Firebase, una aplicación de Xamarin. Android debe encapsular el código en un tipo que `JobService` extienda la clase. `JobService`tiene tres métodos de ciclo de vida a los que se puede llamar durante la vigencia del trabajo:

- **`bool OnStartJob(IJobParameters parameters)`** &ndash; Este método es donde se producirá el trabajo y siempre debe implementarse. Se ejecuta en el subproceso principal. Este método devolverá `true` si queda trabajo restante o `false` si se realiza el trabajo. 
- **`bool OnStopJob(IJobParameters parameters)`** &ndash; Se llama a este método cuando el trabajo se detiene por alguna razón. Debe devolver si `true` el trabajo se debe volver a programar para más tarde.
- **`JobFinished(IJobParameters parameters, bool needsReschedule)`** Se llama a este método cuando `JobService` el ha finalizado cualquier trabajo asincrónico. &ndash; 

Para programar un trabajo, la aplicación creará una instancia `JobDispatcher` de un objeto. A continuación, `Job.Builder` se usa para crear un `Job` objeto, `JobDispatcher` que se proporciona al que intentará programar el trabajo para que se ejecute.

En esta guía se explica cómo agregar el distribuidor de trabajos de Firebase a una aplicación de Xamarin. Android y cómo usarlo para programar el trabajo en segundo plano.

## <a name="requirements"></a>Requisitos

El distribuidor del trabajo de Firebase requiere el nivel de API de Android 9 o superior. La biblioteca de distribuidores de trabajos de Firebase se basa en algunos componentes proporcionados por Google Play Services; el dispositivo debe tener Google Play Services instalado.

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>Uso de la biblioteca de distribuidores de trabajos de Firebase en Xamarin. Android

Para empezar a trabajar con el distribuidor de trabajos de Firebase, agregue primero el [paquete de NuGet Xamarin. Firebase. JobDispatcher](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher) al proyecto de Xamarin. Android. Busque el paquete **Xamarin. Firebase. JobDispatcher** en el administrador de paquetes NuGet (que todavía está en versión preliminar).

Después de agregar la biblioteca `JobService` `FirebaseJobDispatcher`de distribuidores de trabajos de Firebase, cree una clase y, a continuación, programe para que se ejecute con una instancia del.


### <a name="creating-a-jobservice"></a>Creación de un JobService

Todo el trabajo realizado por la biblioteca de distribución de trabajos de Firebase debe realizarse en un tipo que `Firebase.JobDispatcher.JobService` extienda la clase abstracta. Crear un `JobService` es muy similar a crear un `Service` con el marco de trabajo de Android: 

1. Extender la `JobService` clase
2. Decora la subclase con `ServiceAttribute`. Aunque no es estrictamente necesario, se recomienda establecer explícitamente el `Name` parámetro para ayudar con la depuración de. `JobService` 
3. Agregue un `IntentFilter` para declarar el `JobService` en **archivo AndroidManifest. XML**. Esto también ayudará a la biblioteca de distribuidores de trabajos de Firebase a ubicar `JobService`e invocar.

El código siguiente es un ejemplo de la más sencilla `JobService` para una aplicación, mediante la TPL para realizar de forma asincrónica algún trabajo:

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

Antes de que se pueda programar cualquier trabajo, es necesario crear un `Firebase.JobDispatcher.FirebaseJobDispatcher` objeto. El `FirebaseJobDispatcher` es responsable de programar un `JobService`. El siguiente fragmento de código es una manera de crear una instancia de `FirebaseJobDispatcher`: 
 
 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

En el fragmento de código anterior, `GooglePlayDriver` la clase es que `FirebaseJobDispatcher` ayuda a interactuar con algunas de las API de programación en Google Play Services en el dispositivo. El parámetro `context` es cualquier Android `Context`, como una actividad. `IDriver` Actualmente, `GooglePlayDriver` es la única implementación de la biblioteca de distribuidores de trabajos de Firebase. 

El enlace de Xamarin. Android para el distribuidor del trabajo de Firebase proporciona un método `FirebaseJobDispatcher` `Context`de extensión para crear un a partir de: 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

Una vez que se ha creado una `Job` instancia de, es posible crear y ejecutar el código en la `JobService` clase. `FirebaseJobDispatcher` La `Job` crea un `Job.Builder` objeto y se explicará en la sección siguiente.

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>Creación de un trabajo de Firebase. JobDispatcher. Job con Job. Builder

La `Firebase.JobDispatcher.Job` clase es responsable de encapsular los metadatos necesarios para ejecutar un `JobService`. Contiene información como cualquier restricción que se debe cumplir antes de que `Job` se pueda ejecutar el trabajo, si es recurrente, o cualquier desencadenador que provoque que se ejecute el trabajo.`Job`  `Job` Como mínimo, un debe tener una _etiqueta_ (una cadena única que identifica `FirebaseJobDispatcher`el trabajo para) y el tipo de `JobService` que se debe ejecutar. El distribuidor del trabajo de Firebase creará una instancia `JobService` del cuando sea el momento de ejecutar el trabajo.  Se `Job` crea un con una instancia de la `Firebase.JobDispatcher.Job.JobBuilder` clase. 

El siguiente fragmento de código es el ejemplo más sencillo de cómo crear un `Job` mediante el enlace de Xamarin. Android:

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

`Job.Builder` Realizará algunas comprobaciones de validación básicas en los valores de entrada del trabajo. Se producirá una excepción si no es posible que el `Job.Builder` cree un. `Job`  El `Job.Builder` creará un `Job` con los valores predeterminados siguientes:

- La `Job` _duración_ de un (cuánto tiempo se programará para ejecutarse) es solo hasta que el &ndash; dispositivo se reinicie `Job` una vez que se reinicie el dispositivo.
- Un `Job` no es &ndash; periódico solo se ejecutará una vez.
- `Job` Se programará para que se ejecute lo antes posible.
- La estrategia de reintentos `Job` predeterminada para un es usar un _retroceso exponencial_ (se describe con más detalle a continuación en la sección [configuración de un RetryStrategy](#Setting_a_RetryStrategy)).

### <a name="scheduling-a-job"></a>Programar un trabajo

Después de crear `Job`el, debe programarse con antes de `FirebaseJobDispatcher` ejecutarse. Hay dos métodos para programar un `Job`:

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

El valor devuelto `FirebaseJobDispatcher.Schedule` por será uno de los siguientes valores enteros:

- `FirebaseJobDispatcher.ScheduleResultSuccess`&ndash; Se`Job` programó correctamente.
- `FirebaseJobDispatcher.ScheduleResultUnknownError`Se produjo algún problema desconocido que `Job` impidió que se programara. &ndash;
- `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable`Se usó `IDriver` un no válido o `IDriver` no estaba disponible de algún modo. &ndash; 
- `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger`&ndash; No`Trigger` se admitía.
- `FirebaseJobDispatcher.ScheduleResultBadService`&ndash; El servicio no está configurado correctamente o no está disponible.
 
### <a name="configuring-a-job"></a>Configuración de un trabajo

Es posible personalizar un trabajo. Entre los ejemplos de cómo se puede personalizar un trabajo se incluyen los siguientes:

- [Pasar parámetros a un trabajo](#Passing_Parameters_to_a_Job) &ndash; Un`Job` puede requerir valores adicionales para realizar su trabajo, por ejemplo, para descargar un archivo.
- [Establecer restricciones](#Setting_Constraints) &ndash; Puede que sea necesario ejecutar solo un trabajo cuando se cumplen ciertas condiciones. Por ejemplo, ejecute `Job` solo cuando el dispositivo se esté cargando. 
- [Especifique cuándo `Job` debe ejecutarse](#Setting_Job_Triggers) &ndash; el distribuidor de trabajos de Firebase permite a las aplicaciones especificar una hora en la que se debe ejecutar el trabajo.  
- [Declarar una estrategia de reintento para trabajos con error](#Setting_a_RetryStrategy) Una estrategia `FirebaseJobDispatcher` de reintento proporciona instrucciones sobre qué hacer con `Jobs` que no se completan. &ndash; 

Cada uno de estos temas se explicará más en las secciones siguientes.

<a name="Passing_Parameters_to_a_Job" />

#### <a name="passing-parameters-to-a-job"></a>Pasar parámetros a un trabajo

Los parámetros se pasan a un trabajo mediante la `Bundle` creación de un que se pasa `Job.Builder.SetExtras` junto con el método:

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

Se obtiene acceso a desde la `IJobParameters.Extras` propiedad en el `OnStartJob` método: `Bundle`

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints" />

#### <a name="setting-constraints"></a>Establecer restricciones

Las restricciones pueden ayudar a reducir los costos o la carga de la batería en el dispositivo. La `Firebase.JobDispatcher.Constraint` clase define estas restricciones como valores enteros:

- `Constraint.OnUnmeteredNetwork`&ndash; Solo se ejecuta el trabajo cuando el dispositivo está conectado a una red desmedida. Esto resulta útil para evitar que el usuario incurra en cargos de datos.
- `Constraint.OnAnyNetwork`&ndash; Ejecute el trabajo en cualquier red a la que esté conectado el dispositivo. Si se especifica junto `Constraint.OnUnmeteredNetwork`con, este valor tendrá prioridad.
- `Constraint.DeviceCharging`&ndash; Ejecute el trabajo solo cuando el dispositivo se esté cargando.

Las restricciones se establecen con el `Job.Builder.SetConstraint` método: 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers" />

`JobTrigger` Proporciona instrucciones al sistema operativo sobre cuándo debe iniciarse el trabajo. Un `JobTrigger` tiene una _ventana en ejecución_ que define una hora programada para el `Job` momento en que se debe ejecutar. La ventana de ejecución tiene un valor de _ventana de inicio_ y un valor de _ventana_ de finalización. La ventana de inicio es el número de segundos que debe esperar el dispositivo antes de ejecutar el trabajo y el valor de la ventana final es el número máximo de segundos que `Job`se debe esperar antes de ejecutar. 

Se `JobTrigger` puede crear un con el `Firebase.Jobdispatcher.Trigger.ExecutionWindow` método.  Por ejemplo `Trigger.ExecutionWindow(15,60)` , significa que el trabajo debe ejecutarse entre 15 y 60 segundos desde la programación. El `Job.Builder.SetTrigger` método se usa para 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

El valor `JobTrigger` predeterminado para un trabajo se representa mediante el `Trigger.Now`valor, que especifica que un trabajo se ejecutará lo antes posible después de la programación.

<a name="Setting_a_RetryStrategy" />

#### <a name="setting-a-retrystrategy"></a>Establecer un RetryStrategy

`Firebase.JobDispatcher.RetryStrategy` Se usa para especificar la cantidad de un retraso que debe usar un dispositivo antes de intentar volver a ejecutar un trabajo con error. Un `RetryStrategy` tiene una _Directiva_que define el algoritmo de base de tiempo que se utilizará para volver a programar el trabajo con errores y una ventana de ejecución que especifica una ventana en la que se debe programar el trabajo. Esta _ventana_ de reprogramación se define con dos valores. El primer valor es el número de segundos que hay que esperar antes de volver a programar el trabajo (el valor de _retroceso inicial_ ) y el segundo número es el número máximo de segundos antes de que se deba ejecutar el trabajo (el valor de _retroceso máximo_ ). 
 
Los dos tipos de directivas de reintentos se identifican mediante estos valores int:

- `RetryStrategy.RetryPolicyExponential`Una directiva de _retroceso exponencial_ aumentará el valor de retroceso inicial de forma exponencial después de cada error. &ndash; La primera vez que se produce un error en un trabajo, la biblioteca esperará el intervalo de _initial especificado antes de &ndash; volver a programar el ejemplo de trabajo 30 segundos. La segunda vez que se produce un error en el trabajo, la biblioteca esperará al menos 60 segundos antes de intentar ejecutar el trabajo. Después del tercer intento fallido, la biblioteca esperará 120 segundos, etc. El `RetryStrategy.DefaultExponential` objeto `RetryStrategy` representa el valor predeterminado para la biblioteca de distribuidores de trabajos de Firebase. Tiene un retroceso inicial de 30 segundos y un retroceso máximo de 3600 segundos.
- `RetryStrategy.RetryPolicyLinear`Esta estrategia es una _interrupción lineal_ de que el trabajo se debe volver a programar para que se ejecute a intervalos establecidos (hasta que se realice correctamente). &ndash; El retroceso lineal es más adecuado para el trabajo que debe realizarse lo antes posible o para los problemas que se resolverán rápidamente. La biblioteca de distribuidores de trabajos de Firebase define `RetryStrategy.DefaultLinear` un que tiene una ventana de reprogramación de al menos 30 segundos y hasta 3600 segundos.

Es posible definir un personalizado `RetryStrategy` con el `FirebaseJobDispatcher.NewRetryStrategy` método. Toma tres parámetros:

1. `int policy``RetryStrategy.RetryPolicyExponential` `RetryStrategy` `RetryStrategy.RetryPolicyLinear` La Directiva es uno de los valores anteriores, o. &ndash;
2. `int initialBackoffSeconds`El _retroceso inicial_ es un retraso, en segundos, que es necesario antes de intentar volver a ejecutar el trabajo. &ndash; El valor predeterminado es de 30 segundos. 
3. `int maximumBackoffSeconds`El valor de _retroceso máximo_ declara el número máximo de segundos de retraso antes de intentar ejecutar el trabajo de nuevo. &ndash; El valor predeterminado es 3600 segundos. 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>Cancelar un trabajo

Es posible cancelar todos los trabajos programados, o simplemente un único trabajo mediante el `FirebaseJobDispatcher.CancelAll()` método o el `FirebaseJobDispatcher.Cancel(string)` método:

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

Cualquier método devolverá un valor entero:

- `FirebaseJobDispatcher.CancelResultSuccess`&ndash; El trabajo se canceló correctamente.
- `FirebaseJobDispatcher.CancelResultUnknownError`&ndash; Un error impidió que se cancelara el trabajo.
- `FirebaseJobDispatcher.CancelResult.NoDriverAvailable`No puede cancelar el trabajo porque no hay ningún válido `IDriver` disponible. &ndash; `FirebaseJobDispatcher`

## <a name="summary"></a>Resumen

En esta guía se describe cómo usar el distribuidor de trabajos de Firebase para realizar el trabajo en segundo plano de forma inteligente. Se ha explicado cómo encapsular el trabajo que se va a `JobService` realizar como y cómo `FirebaseJobDispatcher` usar para programar ese trabajo, especificando los criterios con `JobTrigger` y cómo se deben controlar los errores con un `RetryStrategy`.


## <a name="related-links"></a>Vínculos relacionados

- [Xamarin. Firebase. JobDispatcher en NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [firebase-job-Dispatcher en GitHub](https://github.com/firebase/firebase-jobdispatcher-android)
- [Enlace Xamarin. Firebase. JobDispatcher](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [Programación de trabajos inteligentes](https://developer.android.com/topic/performance/scheduling.html)
- [Optimizaciones de memoria y batería de Android: e/s de Google 2016 (vídeo)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
