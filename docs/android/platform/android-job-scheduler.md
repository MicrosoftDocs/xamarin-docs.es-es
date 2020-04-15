---
title: Programador de trabajos de Android
description: En esta guía se describe cómo programar trabajos en segundo plano mediante la API del programador de trabajos de Android.
ms.prod: xamarin
ms.assetid: 673BB8C3-C5CC-43EC-BA8F-758F15D986C9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/19/2018
ms.openlocfilehash: 10d2ae6ac35f02d75ef6e04a0531ec3f5dafd668
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "76940819"
---
# <a name="android-job-scheduler"></a>Programador de trabajos de Android

_En esta guía se describe cómo programar trabajos en segundo plano mediante la API del programador de trabajos de Android, que está disponible en dispositivos Android que ejecutan Android 5.0 (nivel de API 21) y versiones posteriores._

## <a name="overview"></a>Información general 

Una de las mejores formas de mantener la respuesta de una aplicación Android al usuario es asegurarse de que el trabajo complejo o de larga duración se realice en segundo plano. Sin embargo, es importante que el trabajo en segundo plano no afecte negativamente a la experiencia del usuario con el dispositivo. 

Por ejemplo, un trabajo en segundo plano puede sondear un sitio web cada tres o cuatro minutos para consultar los cambios en un conjunto de datos determinado. Este proceso, aparentemente benigno, tendría un efecto desastroso en la duración de la batería. La aplicación reactivará el dispositivo de forma repetida, elevará la CPU a un estado de energía mayor, encenderá los radios, realizará las solicitudes de red y, luego, procesará los resultados. La situación empeora porque el dispositivo no se apaga inmediatamente y vuelve al estado inactivo de baja energía. Un trabajo en segundo plano mal programado puede mantener involuntariamente el dispositivo en un estado con requisitos de energía innecesarios y excesivos. Esta actividad aparentemente inocente (sondear un sitio web) dejará al dispositivo inutilizable durante un período de tiempo relativamente corto.

Android proporciona las siguientes API para ayudar a realizar trabajos en segundo plano, pero por sí solas no son suficientes para la programación inteligente de trabajos. 

- **[Servicios de intención](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** : los servicios de intención son excelentes para realizar el trabajo, pero no proporcionan ninguna manera de programarlo.
- **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)** : estas API solo permiten programar el trabajo, pero no proporcionan ninguna forma de realizarlo. Además, AlarmManager solo permite restricciones basadas en el tiempo, lo que significa que se producirá una alarma en un momento determinado o transcurrido un período de tiempo concreto. 
- **[Receptores de difusión](~/android/app-fundamentals/broadcast-receivers.md)** : una aplicación Android puede configurar receptores de difusión para que realicen el trabajo en respuesta a eventos o intenciones en todo el sistema. Sin embargo, los receptores de difusión no proporcionan ningún control sobre cuándo se debe ejecutar el trabajo. Además, los cambios en el sistema operativo Android se restringirán cuando funcionen los receptores de difusión o los tipos de trabajo a los que puedan responder. 

Hay dos características clave para realizar eficazmente el trabajo en segundo plano (a veces denominado _tareas en segundo plano_ o _trabajo_):

1. **Programar de forma inteligente el trabajo**: es importante que cuando una aplicación esté realizando trabajos en segundo plano, los haga como un buen ciudadano. Lo ideal es que la aplicación no exija que se ejecute un trabajo. En su lugar, la aplicación debe especificar las condiciones que deben cumplirse para que se ejecute el trabajo y, luego, programar ese trabajo con el sistema operativo que lo realizará cuando se cumplan las condiciones. De esta manera, Android puede ejecutar el trabajo para garantizar la máxima eficacia en el dispositivo. Por ejemplo, las solicitudes de red se pueden procesar por lotes para ejecutarse todas al mismo tiempo con el fin de usar al máximo la sobrecarga que conllevan las redes.
2. **Encapsulación del trabajo**: el código para realizar el trabajo en segundo plano debe encapsularse en un componente discreto que se pueda ejecutar con independencia de la interfaz de usuario y sea relativamente fácil de volver a programar si el trabajo no se completa por algún motivo.

El programador de trabajos de Android es un marco integrado en el sistema operativo Android que proporciona una API fluida para simplificar la programación de trabajos en segundo plano.  El programador de trabajos de Android consta de los siguientes tipos:

- `Android.App.Job.JobScheduler` es un servicio del sistema que se usa para programar, ejecutar y, si es necesario, cancelar los trabajos en nombre de una aplicación de Android.
- `Android.App.Job.JobService` es una clase abstracta que se debe extender con la lógica que ejecutará el trabajo en el subproceso principal de la aplicación. Esto significa que `JobService` es responsable de cómo se realice el trabajo de forma asincrónica.
- Un objeto `Android.App.Job.JobInfo` contiene los criterios para indicar a Android cuándo se debe ejecutar el trabajo.

Para programar el trabajo con el programador de trabajos de Android, una aplicación de Xamarin.Android debe encapsular el código en una clase que extienda la clase `JobService`. `JobService` tiene tres métodos de ciclo de vida a los que se puede llamar durante la vigencia del trabajo:

- **bool OnStartJob(JobParameters parameters)** : `JobScheduler` llama a este método para realizar el trabajo y se ejecuta en el subproceso principal de la aplicación. Es responsabilidad de `JobService` realizar el trabajo de forma asincrónica y devolver `true` si queda trabajo o `false` si se ha completado.
    
    Cuando `JobScheduler` llama a este método, solicitará y conservará un wakelock de Android mientras dure el trabajo. Una vez finalizado el trabajo, es responsabilidad de `JobService` indicar a `JobScheduler` este hecho mediante la llamada al método `JobFinished` (descrito a continuación).

- **JobFinished(JobParameters parameters, bool needsReschedule)** : este método se debe llamar mediante `JobService` para indicar a `JobScheduler` que el trabajo se ha completado. Si no se llama a `JobFinished`, `JobScheduler` no quitará el wakelock, lo que provocará un desgaste innecesario de la batería. 

- **bool OnStopJob(JobParameters parameters)** : se llama cuando Android detiene el trabajo antes de tiempo. Debe devolver `true` si el trabajo se debe volver a programar en función de los criterios de reintento (que se describen a continuación con más detalle).

Es posible especificar _restricciones_ o _desencadenadores_ que controlarán cuándo un trabajo puede o debe ejecutarse. Por ejemplo, es posible restringir un trabajo para que solo se ejecute cuando el dispositivo se esté cargando o iniciar un trabajo cuando se haga una foto.

En esta guía se explica de forma detallada cómo implementar una clase `JobService` y programarla con `JobScheduler`.

## <a name="requirements"></a>Requisitos

El programador de trabajos de Android requiere el nivel de API 21 de Android (Android 5.0) o posterior. 

## <a name="using-the-android-job-scheduler"></a>Uso del programador de trabajos de Android

Para usar la API JobScheduler de Android, siga estos tres pasos:

1. Implemente un tipo JobService para encapsular el trabajo.
2. Use un objeto `JobInfo.Builder` para crear el objeto `JobInfo` que contendrá los criterios de `JobScheduler` para ejecutar el trabajo. 
3. Programe el trabajo mediante `JobScheduler.Schedule`.

### <a name="implement-a-jobservice"></a>Implementación de JobService

Todo el trabajo realizado por la biblioteca del programador de trabajos de Android debe realizarse en un tipo que extienda la clase abstracta `Android.App.Job.JobService`. Crear un objeto `JobService` es muy parecido a crear un objeto `Service` con el marco de Android: 

1. Extienda la clase `JobService`.
2. Decore la subclase con `ServiceAttribute` y establezca el parámetro `Name` en una cadena formada por el nombre del paquete y el nombre de la clase (vea el ejemplo siguiente).
3. Establezca la propiedad `Permission` de `ServiceAttribute` en la cadena `android.permission.BIND_JOB_SERVICE`.
4. Invalide el método `OnStartJob`, y agregue el código para realizar el trabajo. Android invocará este método en el subproceso principal de la aplicación para ejecutar el trabajo. El trabajo que tarde más de unos milisegundos se debería ejecutar en un subproceso para evitar que se bloquee la aplicación.
5. Cuando se complete el trabajo, `JobService` debe llamar al método `JobFinished`. Este método es el modo en que `JobService` indica a `JobScheduler` que se ha completado el trabajo. Si no se llama a `JobFinished`, `JobService` someterá al dispositivo a exigencias innecesarias, lo que acortará la duración de la batería. 
6. También es una buena idea reemplazar el método `OnStopJob`. Android llama a este método cuando se está cerrando el trabajo antes de que finalice y proporciona a `JobService` la oportunidad de eliminar los recursos correctamente. Este método debe devolver `true` si es necesario volver a programar el trabajo o `false` si no es aconsejable volver a ejecutarlo.

El código siguiente es un ejemplo del objeto `JobService` más sencillo para una aplicación, donde se usa TPL para realizar de forma asincrónica el trabajo:

```csharp
[Service(Name = "com.xamarin.samples.downloadscheduler.DownloadJob", 
         Permission = "android.permission.BIND_JOB_SERVICE")]
public class DownloadJob : JobService
{
    public override bool OnStartJob(JobParameters jobParams)
    {            
        Task.Run(() =>
        {
            // Work is happening asynchronously
                      
            // Have to tell the JobScheduler the work is done. 
            JobFinished(jobParams, false);
        });

        // Return true because of the asynchronous work
        return true;  
    }

    public override bool OnStopJob(JobParameters jobParams)
    {
        // we don't want to reschedule the job if it is stopped or cancelled.
        return false; 
    }
}
```

### <a name="creating-a-jobinfo-to-schedule-a-job"></a>Creación de JobInfo para programar un trabajo

Las aplicaciones de Xamarin.Android no crean instancias de `JobService` directamente, sino que pasarán un objeto `JobInfo` a `JobScheduler`. `JobScheduler` creará una instancia del objeto `JobService` solicitado, y programará y ejecutará `JobService` según los metadatos de `JobInfo`. Un objeto `JobInfo` debe contener la siguiente información:

- **JobId**: es un valor de `int` que se utiliza para identificar un trabajo en `JobScheduler`. Al reutilizar este valor se actualizarán los trabajos existentes. El valor debe ser único para la aplicación. 
- **JobService**: este parámetro es un objeto `ComponentName` que identifica explícitamente el tipo que debe usar `JobScheduler` para ejecutar un trabajo. 

Este método de extensión muestra cómo crear un objeto `JobInfo.Builder` con un elemento `Context` de Android, como es el caso de una actividad:

```csharp
public static class JobSchedulerHelpers
{
    public static JobInfo.Builder CreateJobBuilderUsingJobId<T>(this Context context, int jobId) where T:JobService
    {
        var javaClass = Java.Lang.Class.FromType(typeof(T));
        var componentName = new ComponentName(context, javaClass);
        return new JobInfo.Builder(jobId, componentName);
    }
}

// Sample usage - creates a JobBuilder for a DownloadJob and sets the Job ID to 1.
var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1);

var jobInfo = jobBuilder.Build();  // creates a JobInfo object.
```

Una eficaz característica del programador de trabajos de Android es la posibilidad de controlar cuándo se ejecuta un trabajo o en qué condiciones se puede ejecutar. En la tabla siguiente se describen algunos de los métodos de `JobInfo.Builder` que permiten a una aplicación influir en cuándo se puede ejecutar un trabajo:  

|  Método | Descripción   |
|---|---|
| `SetMinimumLatency`  | Especifica que se debe observar un retraso (en milisegundos) antes de que se ejecute un trabajo. |
| `SetOverridingDeadline`  | Declara que el trabajo debe ejecutarse antes de que transcurra este tiempo (en milisegundos). |
| `SetRequiredNetworkType`  | Especifica los requisitos de red de un trabajo. |
| `SetRequiresBatteryNotLow` | El trabajo solo se puede ejecutar cuando el dispositivo no muestra una advertencia de "batería baja" al usuario. |
| `SetRequiresCharging` | El trabajo solo se puede ejecutar cuando la batería se está cargando. |
| `SetDeviceIdle` | El trabajo se ejecutará cuando el dispositivo esté ocupado. |
| `SetPeriodic` | Especifica que el trabajo debe ejecutarse con regularidad. |
| `SetPersisted` | El trabajo debe conservarse entre reinicios del dispositivo. | 

En `SetBackoffCriteria` se proporcionan instrucciones sobre cuánto tiempo debe esperar `JobScheduler` antes de intentar ejecutar de nuevo un trabajo. Los criterios de retroceso constan de dos partes: un retroceso en milisegundos (valor predeterminado de 30 segundos) y el tipo de retroceso que se debe usar (en ocasiones conocido como _directiva de retroceso_ o _directiva de reintentos_). Las dos directivas se encapsulan en la enumeración `Android.App.Job.BackoffPolicy`:

- `BackoffPolicy.Exponential`: una directiva de retroceso exponencial aumentará el valor de retroceso inicial de forma exponencial después de cada error. La primera vez que se produce un error en un trabajo, la biblioteca esperará el intervalo inicial especificado antes de volver a programar el trabajo; por ejemplo, 30 segundos. La segunda vez que se produce un error en el trabajo, la biblioteca esperará al menos 60 segundos antes de intentar ejecutar el trabajo. Después del tercer intento con error, la biblioteca esperará 120 segundos y así sucesivamente. Este es el valor predeterminado.
- `BackoffPolicy.Linear`: esta estrategia es un retroceso lineal de que el trabajo se debe volver a programar para que se ejecute a intervalos establecidos (hasta que se realice correctamente). El retroceso lineal es más adecuado para el trabajo que debe realizarse lo antes posible o para los problemas que se resolverán rápidamente por sí solos. 

Para más información sobre cómo crear un objeto `JobInfo`, lea la [documentación de Google sobre la clase `JobInfo.Builder`](https://developer.android.com/reference/android/app/job/JobInfo.Builder.html).

#### <a name="passing-parameters-to-a-job-via-the-jobinfo"></a>Pasar parámetros a un trabajo a través de JobInfo

Los parámetros se pasan a un trabajo mediante la creación de un objeto `PersistableBundle` que se pasa junto con el método `Job.Builder.SetExtras`:

```csharp
var jobParameters = new PersistableBundle();
jobParameters.PutInt("LoopCount", 11);

var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1)
                     .SetExtras(jobParameters)
                     .Build();
```

El acceso a `PersistableBundle` se realiza desde la propiedad `Android.App.Job.JobParameters.Extras` del método `OnStartJob` de un objeto `JobService`:

```csharp
public override bool OnStartJob(JobParameters jobParameters)
{
    var loopCount = jobParams.Extras.GetInt("LoopCount", 10);
    
    // rest of code omitted
} 
```

### <a name="scheduling-a-job"></a>Programación de un trabajo

Para programar un trabajo, una aplicación de Xamarin.Android obtendrá una referencia al servicio del sistema `JobScheduler` y llamará al método `JobScheduler.Schedule` con el objeto `JobInfo` creado en el paso anterior. `JobScheduler.Schedule` se devolverá inmediatamente con uno de dos valores enteros:

- **JobScheduler.ResultSuccess**: el trabajo se ha programado correctamente. 
- **JobScheduler.ResultFailure**: el trabajo no se pudo programar. Esto se debe normalmente a conflictos de los parámetros `JobInfo`.

Este código es un ejemplo de programación de un trabajo y notificación al usuario de los resultados del intento de programación:

```csharp
var jobScheduler = (JobScheduler)GetSystemService(JobSchedulerService);
var scheduleResult = jobScheduler.Schedule(jobInfo);

if (JobScheduler.ResultSuccess == scheduleResult)
{
    var snackBar = Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_success, Snackbar.LengthShort);
    snackBar.Show();
}
else
{
    var snackBar = Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_failure, Snackbar.LengthShort);
    snackBar.Show();
}
```

### <a name="cancelling-a-job"></a>Cancelación de un trabajo

Es posible cancelar todos los trabajos programados o uno solo mediante el método `JobsScheduler.CancelAll()` o el método `JobScheduler.Cancel(jobId)`:

```csharp
// Cancel all jobs
jobScheduler.CancelAll(); 

// to cancel a job with jobID = 1
jobScheduler.Cancel(1)
```
  
## <a name="summary"></a>Resumen

En esta guía se describe cómo usar el programador de trabajos de Android para realizar un trabajo en segundo plano de forma inteligente. Se explica cómo encapsular el trabajo para que se realice como un objeto `JobService` y cómo usar `JobScheduler` para programar ese trabajo, y especificar los criterios con `JobTrigger`; también se describe cómo se deben administrar los errores con `RetryStrategy`.

## <a name="related-links"></a>Vínculos relacionados

- [Programación de trabajos inteligentes](https://developer.android.com/topic/performance/scheduling.html)
- [Referencia de la API JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)
- [Programación de trabajos como un profesional con JobScheduler](https://medium.com/google-developers/scheduling-jobs-like-a-pro-with-jobscheduler-286ef8510129)
- [Optimizaciones de memoria y batería de Android: E/S de Google 2016 (vídeo)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
- [JobScheduler de Android: René Ruppert](https://www.youtube.com/watch?v=aSjBBPYjelE)
