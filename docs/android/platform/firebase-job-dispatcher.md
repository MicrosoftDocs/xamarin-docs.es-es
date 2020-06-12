---
title: Distribuidor de trabajo de Firebase
description: En esta guía se describe cómo programar el trabajo en segundo plano mediante la biblioteca Distribuidor de trabajo de Firebase de Google.
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/05/2018
ms.openlocfilehash: 0ade609997e391e24d4a6da250172efa81a5d490
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571342"
---
# <a name="firebase-job-dispatcher"></a>Distribuidor de trabajo de Firebase

_En esta guía se describe cómo programar el trabajo en segundo plano mediante la biblioteca Distribuidor de trabajo de Firebase de Google._

## <a name="overview"></a>Información general

Una de las mejores formas de mantener la respuesta de una aplicación Android al usuario es asegurarse de que el trabajo complejo o de larga duración se realice en segundo plano. Sin embargo, es importante que el trabajo en segundo plano no afecte negativamente a la experiencia del usuario con el dispositivo. 

Por ejemplo, un trabajo en segundo plano puede sondear un sitio web cada tres o cuatro minutos para consultar los cambios en un conjunto de datos determinado. Este proceso, aparentemente benigno, tendría un efecto desastroso en la duración de la batería. La aplicación reactivará el dispositivo de forma repetida, elevará la CPU a un estado de energía mayor, encenderá los radios, realizará las solicitudes de red y, luego, procesará los resultados. La situación empeora porque el dispositivo no se apaga inmediatamente y vuelve al estado inactivo de baja energía. Un trabajo en segundo plano mal programado puede mantener involuntariamente el dispositivo en un estado con requisitos de energía innecesarios y excesivos. Esta actividad aparentemente inocente (sondear un sitio web) dejará al dispositivo inutilizable durante un período de tiempo relativamente corto.

Android proporciona las siguientes API para ayudar a realizar trabajos en segundo plano, pero por sí solas no son suficientes para la programación inteligente de trabajos. 

- **[Servicios de intención](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** : los servicios de intención son excelentes para realizar el trabajo, pero no proporcionan ninguna manera de programarlo.
- **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)** : estas API solo permiten programar el trabajo, pero no proporcionan ninguna forma de realizarlo. Además, AlarmManager solo permite restricciones basadas en el tiempo, lo que significa que se producirá una alarma en un momento determinado o transcurrido un período de tiempo determinado. 
- **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)** : JobSchedule es una excelente API que funciona con el sistema operativo para programar trabajos. Sin embargo, solo está disponible para las aplicaciones Android que tienen como destino el nivel de API 21 o una versión posterior. 
- **[Receptores de difusión](~/android/app-fundamentals/broadcast-receivers.md)** : una aplicación Android puede configurar receptores de difusión para que realicen el trabajo en respuesta a eventos o intenciones en todo el sistema. Sin embargo, los receptores de difusión no proporcionan ningún control sobre cuándo se debe ejecutar el trabajo. Además, los cambios en el sistema operativo Android se restringirán cuando funcionen los receptores de difusión o los tipos de trabajo a los que puedan responder. 

Hay dos características clave para realizar eficazmente el trabajo en segundo plano (a veces denominado _tareas en segundo plano_ o _trabajo_):

1. **Programar de forma inteligente el trabajo**: es importante que cuando una aplicación esté realizando trabajos en segundo plano, los haga como un buen ciudadano. Lo ideal es que la aplicación no exija que se ejecute un trabajo. En su lugar, la aplicación debe especificar las condiciones que deben cumplirse para cuando pueda ejecutarse el trabajo y, luego, programar ese trabajo para que se ejecute cuando se cumplan las condiciones. Esto permite que Android realice el trabajo de forma inteligente. Por ejemplo, las solicitudes de red se pueden procesar por lotes para ejecutarse todas al mismo tiempo con el fin de hacer el uso máximo de la sobrecarga implicada en las redes.
2. **Encapsulación del trabajo**: el código para realizar el trabajo en segundo plano debe encapsularse en un componente discreto que se pueda ejecutar independientemente de la interfaz de usuario y sea relativamente fácil de volver a programar si el trabajo no se completa por algún motivo.

El Distribuidor de trabajo de Firebase es una biblioteca de Google que proporciona una API fluida para simplificar la programación del trabajo en segundo plano. Está pensada para reemplazar a Google Cloud Manager. El Distribuidor de trabajo de Firebase consta de las siguientes API:

- `Firebase.JobDispatcher.JobService` es una clase abstracta que se debe extender con la lógica que se ejecutará en el trabajo en segundo plano.
- `Firebase.JobDispatcher.JobTrigger` declara cuándo se debe iniciar el trabajo. Esto suele expresarse como una ventana de tiempo, por ejemplo, esperar al menos 30 segundos antes de iniciar el trabajo, pero ejecutar el trabajo en 5 minutos.
- `Firebase.JobDispatcher.RetryStrategy` contiene información sobre lo que se debe hacer cuando un trabajo no se ejecuta correctamente. La estrategia de reintento especifica cuánto tiempo se debe esperar antes de intentar volver a ejecutar el trabajo. 
- `Firebase.JobDispatcher.Constraint` es un valor opcional que describe una condición que se debe cumplir antes de que se pueda ejecutar el trabajo, por ejemplo, el dispositivo se encuentra en una red uniforme o en un proceso de carga.
- `Firebase.JobDispatcher.Job` es una API que unifica las API anteriores en una unidad de trabajo que `JobDispatcher` puede programar. La clase `Job.Builder` se usa para crear una instancia de `Job`.
- `Firebase.JobDispatcher.JobDispatcher` utiliza las tres API anteriores para programar el trabajo con el sistema operativo y proporcionar una manera de cancelar los trabajos, si es necesario.

Para programar el trabajo con el Distribuidor de trabajo de Firebase, una aplicación Xamarin.Android debe encapsular el código en un tipo que extienda la clase `JobService`. `JobService` tiene tres métodos de ciclo de vida a los que se puede llamar durante la vigencia del trabajo:

- **`bool OnStartJob(IJobParameters parameters)`** : este método es donde se producirá el trabajo y siempre debe implementarse. Se ejecuta en el subproceso principal. Este método devolverá `true` si hay trabajo restante o `false` si el trabajo ya se ha hecho. 
- **`bool OnStopJob(IJobParameters parameters)`** : se llama a este método cuando el trabajo se detiene por alguna razón. Debe devolver `true` si el trabajo se debe volver a programar para más tarde.
- **`JobFinished(IJobParameters parameters, bool needsReschedule)`** : se llama a este método cuando `JobService` ha finalizado cualquier trabajo asincrónico. 

Para programar un trabajo, la aplicación creará una instancia de un objeto `JobDispatcher`. A continuación, se usa `Job.Builder` para crear un objeto `Job`, que se proporciona a `JobDispatcher`, que intentará programar el trabajo para que se ejecute.

En esta guía, se abordará cómo agregar el Distribuidor de trabajo de Firebase a una aplicación Xamarin.Android y cómo usarlo para programar el trabajo en segundo plano.

## <a name="requirements"></a>Requisitos

El Distribuidor de trabajo de Firebase requiere Android, nivel de API 9, o posterior. La biblioteca Distribuidor de trabajo de Firebase se basa en algunos componentes proporcionados por Google Play Services; el dispositivo debe tener Google Play Services instalado.

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>Uso de la biblioteca Distribuidor de trabajo de Firebase en Xamarin.Android

Para empezar a trabajar con el Distribuidor de trabajo de Firebase, agregue primero el [paquete de NuGet Xamarin.Firebase.JobDispatcher](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher) al proyecto de Xamarin.Android. Busque el administrador de paquetes de NuGet para el paquete **Xamarin.Firebase.JobDispatcher** (que todavía se encuentra en versión preliminar).

Después de agregar la biblioteca Distribuidor de trabajo de Firebase, cree una clase `JobService` y, a continuación, prográmela para que se ejecute con una instancia de `FirebaseJobDispatcher`.

### <a name="creating-a-jobservice"></a>Creación de una clase JobService

Todo el trabajo realizado por la biblioteca Distribuidor de trabajo de Firebase debe realizarse en un tipo que extienda la clase abstracta `Firebase.JobDispatcher.JobService`. Crear un objeto `JobService` es muy parecido a crear un objeto `Service` con el marco de Android: 

1. Extienda la clase `JobService`.
2. Decore la subclase con `ServiceAttribute`. Aunque no es estrictamente necesario, se recomienda establecer explícitamente el parámetro `Name` para ayudar con la depuración de `JobService`. 
3. Agregue `IntentFilter` para declarar `JobService` en el archivo **AndroidManifest.xml**. Esto también ayudará a la biblioteca Distribuidor de trabajo de Firebase a ubicar e invocar `JobService`.

El código siguiente es un ejemplo del objeto `JobService` más sencillo para una aplicación, donde se usa TPL para realizar de forma asincrónica algo de trabajo:

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

### <a name="creating-a-firebasejobdispatcher"></a>Creación de un objeto FirebaseJobDispatcher

Antes de que se pueda programar cualquier trabajo, es necesario crear un objeto `Firebase.JobDispatcher.FirebaseJobDispatcher`. `FirebaseJobDispatcher` es responsable de programar `JobService`. El siguiente fragmento de código es una manera de crear una instancia de la clase `FirebaseJobDispatcher`: 

 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

En el fragmento de código anterior, `GooglePlayDriver` es una clase que ayuda a `FirebaseJobDispatcher` a interactuar con algunas de las API de programación de Google Play Services en el dispositivo. El parámetro `context` es cualquier `Context` de Android, como una actividad. Actualmente, `GooglePlayDriver` es la única implementación de `IDriver` en la biblioteca Distribuidor de trabajo de Firebase. 

El enlace de Xamarin.Android para el Distribuidor de trabajo de Firebase proporciona un método de extensión para crear un objeto `FirebaseJobDispatcher` a partir de `Context`: 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

Una vez creada la instancia de `FirebaseJobDispatcher`, es posible crear un objeto `Job` y ejecutar el código en la clase `JobService`. `Job` se crea mediante un objeto `Job.Builder` y se explicará en la sección siguiente.

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>Creación de una clase Firebase.JobDispatcher.Job con Job.Builder

La clase `Firebase.JobDispatcher.Job` es responsable de encapsular los metadatos necesarios para ejecutar `JobService`. `Job` contiene información como cualquier restricción que se debe cumplir antes de que se pueda ejecutar el trabajo, si `Job` es recurrente, o cualquier desencadenador que provoca que se ejecute el trabajo.  Como mínimo, `Job` debe tener una _etiqueta_ (una cadena única que identifique el trabajo en `FirebaseJobDispatcher`) y el tipo de `JobService` que se debe ejecutar. El Distribuidor de trabajo de Firebase creará una instancia de `JobService` cuando sea el momento de ejecutar el trabajo.  Se crea `Job` con el uso de una instancia de la clase `Firebase.JobDispatcher.Job.JobBuilder`. 

El siguiente fragmento de código es el ejemplo más sencillo de cómo crear un objeto `Job` mediante el enlace de Xamarin.Android:

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

`Job.Builder` realizará algunas comprobaciones de validación básicas sobre los valores de entrada del trabajo. Se producirá una excepción si no es posible que `Job.Builder` cree `Job`.  `Job.Builder` creará `Job` con las siguientes opciones predeterminadas:

- La _duración_ de `Job` (durante cuánto tiempo se programará su ejecución) solo es hasta que el dispositivo se reinicia; una vez que se reinicia el dispositivo, se pierde `Job`.
- `Job` no es recurrente; solo se ejecutará una vez.
- `Job` se programará para que se ejecute lo antes posible.
- La estrategia de reintentos predeterminada para `Job` es usar un _retroceso exponencial_ (se describe con más detalle a continuación en la sección [Establecimiento de una clase RetryStrategy](#Setting_a_RetryStrategy)).

### <a name="scheduling-a-job"></a>Programación de un trabajo

Después de crear `Job`, debe programarse con `FirebaseJobDispatcher` antes de ejecutarse. Existen dos métodos para programar un objeto `Job`:

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

El valor devuelto por `FirebaseJobDispatcher.Schedule` será uno de los siguientes valores enteros:

- `FirebaseJobDispatcher.ScheduleResultSuccess`: `Job` se ha programado correctamente.
- `FirebaseJobDispatcher.ScheduleResultUnknownError`: se ha producido algún problema conocido que impedía la programación de `Job`.
- `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable`: se ha usado un objeto `IDriver` no válido o `IDriver` no estaba disponible de alguna manera. 
- `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger`: `Trigger` no se admitía.
- `FirebaseJobDispatcher.ScheduleResultBadService`: el servicio no está configurado correctamente o no está disponible.

### <a name="configuring-a-job"></a>Configuración de un trabajo

Es posible personalizar un trabajo. Entre los ejemplos de cómo se puede personalizar un trabajo se incluyen los siguientes:

- [Pasar parámetros a un trabajo](#Passing_Parameters_to_a_Job): un elemento `Job` puede requerir valores adicionales para realizar su trabajo, por ejemplo, la descarga de un archivo.
- [Establecer restricciones](#Setting_Constraints): puede que solo sea necesario ejecutar un trabajo cuando se cumplan determinadas condiciones. Por ejemplo, ejecute solo un elemento `Job` cuando el dispositivo se esté cargando. 
- [Especifique cuándo debe ejecutarse `Job`](#Setting_Job_Triggers): el Distribuidor de trabajo de Firebase permite a las aplicaciones especificar una hora en la que se debe ejecutar el trabajo.  
- [Declarar una estrategia de reintento para trabajos con error](#Setting_a_RetryStrategy); una _estrategia de reintento_ proporciona instrucciones para `FirebaseJobDispatcher` sobre qué hacer con los elementos `Jobs` que no se completan correctamente. 

Cada uno de estos temas se aborda de forma detallada en las secciones siguientes.

<a name="Passing_Parameters_to_a_Job"></a>

#### <a name="passing-parameters-to-a-job"></a>Paso de parámetros a un trabajo

Los parámetros se pasan a un trabajo mediante la creación de un objeto `Bundle` que se pasa junto con el método `Job.Builder.SetExtras`:

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

El acceso a `Bundle` se realiza desde la propiedad `IJobParameters.Extras` del método `OnStartJob`:

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints"></a>

#### <a name="setting-constraints"></a>Establecimiento de restricciones

Las restricciones pueden ayudar a reducir los costos o el consumo de la batería del dispositivo. La clase `Firebase.JobDispatcher.Constraint` define estas restricciones como valores enteros:

- `Constraint.OnUnmeteredNetwork`: solo se ejecuta el trabajo cuando el dispositivo está conectado a una red uniforme. Esto resulta útil para evitar que el usuario incurra en cargos de datos.
- `Constraint.OnAnyNetwork`: ejecute el trabajo en cualquier red a la que esté conectado el dispositivo. Si se especifica junto con `Constraint.OnUnmeteredNetwork`, este valor tendrá prioridad.
- `Constraint.DeviceCharging`: ejecute el trabajo solo cuando el dispositivo se esté cargando.

Las restricciones se establecen con el método `Job.Builder.SetConstraint`: 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers"></a>

`JobTrigger` proporciona instrucciones al sistema operativo sobre cuándo debe iniciarse el trabajo. `JobTrigger` tiene una _ventana en ejecución_ que define una hora programada para el momento en que se debe ejecutar `Job`. La ventana de ejecución tiene un valor para la _ventana de inicio_ y un valor para la _ventana de finalización_. La ventana de inicio es el número de segundos que el dispositivo debe esperar antes de ejecutar el trabajo y el valor de la ventana de finalización es el número máximo de segundos que se debe esperar antes de ejecutar `Job`. 

Se puede crear `JobTrigger` con el método `Firebase.Jobdispatcher.Trigger.ExecutionWindow`.  Por ejemplo, `Trigger.ExecutionWindow(15,60)` significa que el trabajo debe ejecutarse en un período de entre 15 y 60 segundos desde cuando se programó. El método `Job.Builder.SetTrigger` se usa para: 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

El elemento `JobTrigger` predeterminado para un trabajo se representa mediante el valor `Trigger.Now`, que especifica que un trabajo se ejecutará lo antes posible después de la programación.

<a name="Setting_a_RetryStrategy"></a>

#### <a name="setting-a-retrystrategy"></a>Establecimiento de la clase RetryStrategy

`Firebase.JobDispatcher.RetryStrategy` se usa para especificar cuánto retraso debe aplicar un dispositivo antes de tratar de volver a ejecutar un trabajo con errores. `RetryStrategy` tiene una _directiva_, que define el algoritmo de base de tiempo que se utilizará para volver a programar el trabajo con errores y una ventana de ejecución que especifica una ventana en la que se debe programar el trabajo. Esta _ventana de reprogramación_ se define con dos valores. El primer valor es el número de segundos que hay que esperar antes de volver a programar el trabajo (el valor de _retroceso inicial_) y el segundo número es el número máximo de segundos antes de que se ejecute el trabajo (el valor de _retroceso máximo_). 

Los dos tipos de directivas de reintentos se identifican mediante estos valores int:

- `RetryStrategy.RetryPolicyExponential`: una directiva de _retroceso exponencial_ aumentará el valor de retroceso inicial de forma exponencial después de cada error. La primera vez que se produce un error en un trabajo, la biblioteca esperará el intervalo inicial especificado antes de volver a programar el trabajo; por ejemplo, 30 segundos. La segunda vez que se produce un error en el trabajo, la biblioteca esperará al menos 60 segundos antes de intentar ejecutar el trabajo. Después del tercer intento con error, la biblioteca esperará 120 segundos y así sucesivamente. Las clases `RetryStrategy` predeterminadas para la biblioteca Distribuidor de trabajo de Firebase se representan mediante el objeto `RetryStrategy.DefaultExponential`. Tiene un retroceso inicial de 30 segundos y un retroceso máximo de 3 600 segundos.
- `RetryStrategy.RetryPolicyLinear`: esta estrategia es un _retroceso lineal_ de que el trabajo se debe volver a programar para que se ejecute a intervalos establecidos (hasta que se realice correctamente). El retroceso lineal es más adecuado para el trabajo que debe realizarse lo antes posible o para los problemas que se resolverán rápidamente por sí solos. La biblioteca Distribuidor de trabajo de Firebase define un elemento `RetryStrategy.DefaultLinear` que tiene una ventana de reprogramación de al menos 30 segundos y hasta 3 600 segundos.

Es posible definir una clase `RetryStrategy` personalizada con el método `FirebaseJobDispatcher.NewRetryStrategy`. Adopta tres parámetros:

1. `int policy`: la _directiva_ es uno de los valores de `RetryStrategy` anteriores, `RetryStrategy.RetryPolicyLinear` o `RetryStrategy.RetryPolicyExponential`.
2. `int initialBackoffSeconds`: el _retroceso inicial_ es un retraso, en segundos, que es necesario antes de intentar volver a ejecutar el trabajo. El valor predeterminado es 30 segundos. 
3. `int maximumBackoffSeconds`: el valor _retroceso máximo_ declara el número máximo de segundos de retraso antes de intentar ejecutar el trabajo de nuevo. El valor predeterminado es de 3 600 segundos. 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>Cancelación de un trabajo

Es posible cancelar todos los trabajos programados o uno solo mediante el método `FirebaseJobDispatcher.CancelAll()` o el método `FirebaseJobDispatcher.Cancel(string)`:

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

Cualquier método devolverá un valor entero:

- `FirebaseJobDispatcher.CancelResultSuccess`: el trabajo se canceló correctamente.
- `FirebaseJobDispatcher.CancelResultUnknownError`: un error evitó la cancelación del trabajo.
- `FirebaseJobDispatcher.CancelResult.NoDriverAvailable`: `FirebaseJobDispatcher` no puede cancelar el trabajo porque no hay ningún elemento `IDriver` válido disponible.

## <a name="summary"></a>Resumen

En esta guía se describe cómo usar el Distribuidor de trabajo de Firebase para realizar un trabajo en segundo plano de forma inteligente. Se explica cómo encapsular el trabajo para que se realice como un objeto `JobService` y cómo usar `FirebaseJobDispatcher` para programar ese trabajo, y especificar los criterios con `JobTrigger` y cómo se deben administrar los errores con `RetryStrategy`.

## <a name="related-links"></a>Vínculos relacionados

- [Xamarin.Firebase.JobDispatcher en NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [firebase-job-dispatcher en GitHub](https://github.com/firebase/firebase-jobdispatcher-android)
- [Enlace de Xamarin.Firebase.JobDispatcher](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [Programación de trabajos inteligentes](https://developer.android.com/topic/performance/scheduling.html)
- [Optimizaciones de memoria y batería de Android: E/S de Google 2016 (vídeo)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
