---
title: Programador de trabajos de Android
description: En esta guía se describe cómo programar el trabajo en segundo plano mediante la API del programador de trabajos de Android.
ms.prod: xamarin
ms.assetid: 673BB8C3-C5CC-43EC-BA8F-758F15D986C9
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/19/2018
ms.openlocfilehash: e2bfc64626d658cbcb22ba5f2ebd1f1ff069ec19
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757763"
---
# <a name="android-job-scheduler"></a>Programador de trabajos de Android

_En esta guía se describe cómo programar el trabajo en segundo plano mediante la API del programador de trabajos de Android, que está disponible en dispositivos Android que ejecutan Android 5,0 (nivel de API 21) y versiones posteriores._

## <a name="overview"></a>Información general 

Una de las mejores formas de mantener la respuesta de una aplicación Android al usuario es asegurarse de que el trabajo complejo o de larga ejecución se realiza en segundo plano. Sin embargo, es importante que el trabajo en segundo plano no afecte negativamente a la experiencia del usuario con el dispositivo. 

Por ejemplo, un trabajo en segundo plano puede sondear un sitio web cada tres o cuatro minutos para consultar los cambios en un conjunto de datos determinado. Parece benigno, pero tendría un impacto desastroso en la duración de la batería. La aplicación reactivará repetidamente el dispositivo, elevará la CPU a un estado de energía mayor, apagará las radios, realizará las solicitudes de red y, a continuación, procesará los resultados. Es peor porque el dispositivo no se apagará inmediatamente y volverá al estado inactivo de baja energía. Un trabajo en segundo plano mal programado puede mantener involuntariamente el dispositivo en un estado con requisitos de energía innecesarios y excesivos. Esta actividad aparentemente inocente (sondeo de un sitio web) hará que el dispositivo no se pueda usar en un período de tiempo relativamente corto.

Android proporciona las siguientes API para ayudar a realizar el trabajo en segundo plano, pero no basta para la programación inteligente de trabajos. 

- **[Servicios de intención](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash; Los servicios de intención son excelentes para realizar el trabajo; sin embargo, no proporcionan ninguna manera de programar el trabajo.
- **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)** &ndash; Estas API solo permiten programar el trabajo, pero no proporcionan ninguna forma de realizar realmente el trabajo. Además, el AlarmManager solo permite restricciones basadas en el tiempo, lo que significa que se producirá una alarma en un momento determinado o transcurrido un período de tiempo determinado. 
- **[Receptores de difusión](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; Una aplicación Android puede configurar receptores de difusión para que realicen el trabajo en respuesta a eventos de todo el sistema o a intenciones. Sin embargo, los receptores de difusión no proporcionan ningún control sobre cuándo se debe ejecutar el trabajo. Además, los cambios en el sistema operativo Android se restringirán cuando los receptores de difusión funcionen o los tipos de trabajo a los que puedan responder. 

Hay dos características clave para realizar eficazmente el trabajo en segundo plano (a veces denominado _trabajo en segundo plano_ o _trabajo_):

1. **Programación inteligente del trabajo** &ndash; Es importante que cuando una aplicación esté trabajando en segundo plano, como un buen ciudadano. Idealmente, la aplicación no debería exigir que se ejecute un trabajo. En su lugar, la aplicación debe especificar las condiciones que deben cumplirse para que el trabajo pueda ejecutarse y, a continuación, programar ese trabajo con el sistema operativo que realizará el trabajo cuando se cumplan las condiciones. Esto permite que Android ejecute el trabajo para garantizar la máxima eficacia en el dispositivo. Por ejemplo, las solicitudes de red se pueden procesar por lotes para ejecutarse todas al mismo tiempo con el fin de hacer el uso máximo de la sobrecarga implicada en las redes.
2. **Encapsular el trabajo** &ndash; El código para realizar el trabajo en segundo plano debe encapsularse en un componente discreto que se pueda ejecutar independientemente de la interfaz de usuario y será relativamente fácil de volver a programar si el trabajo no se completa por algún motivo.

El programador de trabajos de Android es un marco de trabajo integrado en el sistema operativo Android que proporciona una API fluida para simplificar la programación del trabajo en segundo plano.  El programador de trabajos de Android consta de los siguientes tipos:

- `Android.App.Job.JobScheduler` Es un servicio del sistema que se usa para programar, ejecutar y, si es necesario, cancelar los trabajos en nombre de una aplicación de Android.
- Una `Android.App.Job.JobService` es una clase abstracta que se debe extender con la lógica que ejecutará el trabajo en el subproceso principal de la aplicación. Esto significa que el `JobService` es responsable de cómo se realizará el trabajo de forma asincrónica.
- Un `Android.App.Job.JobInfo` objeto contiene los criterios para guiar Android cuando se debe ejecutar el trabajo.

Para programar el trabajo con el programador de trabajos de Android, una aplicación de Xamarin. Android debe encapsular el código en una `JobService` clase que extienda la clase. `JobService`tiene tres métodos de ciclo de vida a los que se puede llamar durante la vigencia del trabajo:

- **bool OnStartJob (parámetros JobParameters)** &ndash; Llama`JobScheduler` a este método para realizar el trabajo y se ejecuta en el subproceso principal de la aplicación. Es responsabilidad de realizar de `JobService` forma asincrónica el trabajo y `true` si queda trabajo restante o `false` si se realiza el trabajo.
    
    `JobScheduler` Cuando llama a este método, solicitará y conservará un wakelock de Android mientras dure el trabajo. Una vez finalizado el trabajo, es responsabilidad del `JobService` para `JobScheduler` indicar a de este hecho que llame al `JobFinished` método (que se describe a continuación).

- **JobFinished (parámetros JobParameters, bool needsReschedule)** &ndash; Debe `JobScheduler` llamar `JobService` a este método para indicar a que el trabajo se ha realizado. Si `JobFinished` no se llama a, `JobScheduler` no se quitará el wakelock, lo que provocará una purga de la batería innecesaria. 

- **bool OnStopJob (parámetros JobParameters)** &ndash; Se llama a este método cuando Android detiene prematuramente el trabajo. Debe devolver si `true` el trabajo se debe volver a programar en función de los criterios de reintento (que se describen a continuación con más detalle).

Es posible especificar _restricciones_ o _desencadenadores_ que controlarán Cuándo puede o debe ejecutarse un trabajo. Por ejemplo, es posible restringir un trabajo para que solo se ejecute cuando el dispositivo se esté cargando o para iniciar un trabajo cuando se tome una imagen.

En esta guía se explica en detalle cómo implementar una `JobService` clase y programarla `JobScheduler`con.

## <a name="requirements"></a>Requisitos

El programador de trabajos de Android requiere el nivel de API 21 de Android (Android 5,0) o posterior. 

## <a name="using-the-android-job-scheduler"></a>Usar el programador de trabajos de Android

Hay tres pasos para usar la API JobScheduler de Android:

1. Implemente un tipo JobService para encapsular el trabajo.
2. Use un `JobInfo.Builder` objeto para crear el `JobInfo` objeto que contendrá los criterios para `JobScheduler` que ejecute el trabajo. 
3. Programe el trabajo `JobScheduler.Schedule`mediante.

### <a name="implement-a-jobservice"></a>Implementar un JobService

Todo el trabajo realizado por la biblioteca del programador de trabajos de Android debe realizarse en un `Android.App.Job.JobService` tipo que extienda la clase abstracta. Crear un `JobService` es muy similar a crear un `Service` con el marco de trabajo de Android: 

1. Extienda la `JobService` clase.
2. Decore la subclase con `ServiceAttribute` y establezca el `Name` parámetro en una cadena formada por el nombre del paquete y el nombre de la clase (vea el ejemplo siguiente).
3. Establezca la `Permission` propiedad `ServiceAttribute` de en la cadena `android.permission.BIND_JOB_SERVICE`.
4. Invalide el `OnStartJob` método y agregue el código para realizar el trabajo. Android invocará este método en el subproceso principal de la aplicación para ejecutar el trabajo. Trabajo que tardará más tiempo en ejecutarse en un subproceso para evitar que se bloquee la aplicación.
5. Cuando se realiza el trabajo, `JobService` debe `JobFinished` llamar al método. Este método indica el `JobService` modo en `JobScheduler` que se realiza el trabajo. Si no se `JobFinished` llama a, se `JobService` producirá una demanda innecesaria en el dispositivo, lo que reduce la duración de la batería. 
6. Es conveniente reemplazar también el `OnStopJob` método. Android llama a este método cuando se está cerrando el trabajo antes de que finalice y proporciona la `JobService` oportunidad de eliminar los recursos correctamente. Este método debe devolver `true` si es necesario volver a programar el trabajo o `false` si no es deseable volver a ejecutar el trabajo.

El código siguiente es un ejemplo de la más sencilla `JobService` para una aplicación, mediante la TPL para realizar de forma asincrónica algún trabajo:

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

### <a name="creating-a-jobinfo-to-schedule-a-job"></a>Creación de un JobInfo para programar un trabajo

Las aplicaciones de Xamarin. Android no crean instancias `JobService` de directamente, sino que pasarán un `JobInfo` objeto a `JobScheduler`. Creará una instancia del objeto `JobService` solicitado, `JobService` programando y ejecutando según los metadatos de `JobInfo`. `JobScheduler` Un `JobInfo` objeto debe contener la siguiente información:

- **JobID** se trata de `int` un valor que se utiliza para `JobScheduler`identificar un trabajo en. &ndash; Al reutilizar este valor se actualizarán los trabajos existentes. El valor debe ser único para la aplicación. 
- **JobService** Este parámetro es un `ComponentName` que `JobScheduler` identifica explícitamente el tipo que debe usar para ejecutar un trabajo. &ndash; 

Este método de extensión muestra cómo crear un `JobInfo.Builder` con un Android `Context`, como una actividad:

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

Una eficaz característica del programador de trabajos de Android es la posibilidad de controlar cuándo se ejecuta un trabajo o en qué condiciones se puede ejecutar un trabajo. En la tabla siguiente se describen algunos de los `JobInfo.Builder` métodos de que permiten a una aplicación influir en el momento en que se puede ejecutar un trabajo:  

|  Método | DESCRIPCIÓN   |
|---|---|
| `SetMinimumLatency`  | Especifica que se debe observar un retraso (en milisegundos) antes de que se ejecute un trabajo. |
| `SetOverridingDeadline`  | Declara que el trabajo debe ejecutarse antes de que transcurra este tiempo (en milisegundos). |
| `SetRequiredNetworkType`  | Especifica los requisitos de red para un trabajo. |
| `SetRequiresBatteryNotLow` | El trabajo solo se puede ejecutar cuando el dispositivo no muestra una advertencia de "batería baja" al usuario. |
| `SetRequiresCharging` | El trabajo solo se puede ejecutar cuando la batería se está cargando. |
| `SetDeviceIdle` | El trabajo se ejecutará cuando el dispositivo esté ocupado. |
| `SetPeriodic` | Especifica que el trabajo debe ejecutarse con regularidad. |
| `SetPersisted` | El trabajo debe ser perdedor entre los reinicios del dispositivo. | 

El `SetBackoffCriteria` proporciona instrucciones sobre cuánto `JobScheduler` tiempo debe esperar antes de intentar volver a ejecutar un trabajo. Los criterios de retardo son dos partes: un retraso en milisegundos (valor predeterminado de 30 segundos) y el tipo de devolución de llamada que se debe usar (que a veces se conoce como la _Directiva de retroceso_ o la _Directiva de reintentos_). Las dos directivas se encapsulan en la `Android.App.Job.BackoffPolicy` enumeración:

- `BackoffPolicy.Exponential`&ndash; Una directiva de retroceso exponencial aumentará el valor de retroceso inicial de forma exponencial después de cada error. La primera vez que se produce un error en un trabajo, la biblioteca esperará el intervalo inicial especificado antes de volver a programar el trabajo; por ejemplo, 30 segundos. La segunda vez que se produce un error en el trabajo, la biblioteca esperará al menos 60 segundos antes de intentar ejecutar el trabajo. Después del tercer intento fallido, la biblioteca esperará 120 segundos, etc. Este es el valor predeterminado.
- `BackoffPolicy.Linear`&ndash; Esta estrategia es una interrupción lineal de que el trabajo se debe volver a programar para que se ejecute a intervalos establecidos (hasta que se realice correctamente). El retroceso lineal es más adecuado para el trabajo que debe realizarse lo antes posible o para los problemas que se resolverán rápidamente. 

Para obtener más información sobre cómo `JobInfo` crear un objeto, lea [la documentación de Google `JobInfo.Builder` para la clase](https://developer.android.com/reference/android/app/job/JobInfo.Builder.html).

#### <a name="passing-parameters-to-a-job-via-the-jobinfo"></a>Pasar parámetros a un trabajo a través de JobInfo

Los parámetros se pasan a un trabajo mediante la `PersistableBundle` creación de un que se pasa `Job.Builder.SetExtras` junto con el método:

```csharp
var jobParameters = new PersistableBundle();
jobParameters.PutInt("LoopCount", 11);

var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1)
                     .SetExtras(jobParameters)
                     .Build();
```

Se obtiene acceso a desde la `Android.App.Job.JobParameters.Extras` propiedad en el `OnStartJob` método de un `JobService`: `PersistableBundle`

```csharp
public override bool OnStartJob(JobParameters jobParameters)
{
    var loopCount = jobParams.Extras.GetInt("LoopCount", 10);
    
    // rest of code omitted
} 
```

### <a name="scheduling-a-job"></a>Programar un trabajo

Para programar un trabajo, una aplicación de Xamarin. Android obtendrá una referencia al `JobScheduler` servicio de sistema y llamará al `JobScheduler.Schedule` método con `JobInfo` el objeto que se creó en el paso anterior. `JobScheduler.Schedule`se devolverá inmediatamente con uno de dos valores enteros:

- **JobScheduler. ResultSuccess** &ndash; el trabajo se ha programado correctamente. 
- **JobScheduler. ResultFailure** &ndash; no se pudo programar el trabajo. Esto se debe normalmente a `JobInfo` los parámetros en conflicto.

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

### <a name="cancelling-a-job"></a>Cancelar un trabajo

Es posible cancelar todos los trabajos programados, o simplemente un único trabajo mediante el `JobsScheduler.CancelAll()` método o el `JobScheduler.Cancel(jobId)` método:

```csharp
// Cancel all jobs
jobScheduler.CancelAll(); 

// to cancel a job with jobID = 1
jobScheduler.Cancel(1)
```
  
## <a name="summary"></a>Resumen

En esta guía se describe cómo usar el programador de trabajos de Android para realizar un trabajo en segundo plano de forma inteligente. Se ha explicado cómo encapsular el trabajo que se va a `JobService` realizar como y cómo `JobScheduler` usar para programar ese trabajo, especificando los criterios con `JobTrigger` y cómo se deben controlar los errores con un `RetryStrategy`.

## <a name="related-links"></a>Vínculos relacionados

- [Programación de trabajos inteligentes](https://developer.android.com/topic/performance/scheduling.html)
- [Referencia de la API de JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)
- [Programación de trabajos como Pro con JobScheduler](https://medium.com/google-developers/scheduling-jobs-like-a-pro-with-jobscheduler-286ef8510129)
- [Optimizaciones de memoria y batería de Android: e/s de Google 2016 (vídeo)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
- [JobScheduler de Android-René Ruppert](https://www.youtube.com/watch?v=aSjBBPYjelE)
