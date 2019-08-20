---
title: Tareas en segundo plano de watchos en Xamarin
description: En este documento se describe cómo usar las tareas en segundo plano con watchos en Xamarin, echar un vistazo a los tipos de tareas en segundo plano, usar recursos, implementar tareas en segundo plano, programar, procedimientos recomendados, etc.
ms.prod: xamarin
ms.assetid: 2049C430-7566-45F8-9E3D-1446F484981E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/13/2017
ms.openlocfilehash: 93ad7d6adbecac2b2d5cf32d1dfc84edba718407
ms.sourcegitcommit: 0df727caf941f1fa0aca680ec871bfe7a9089e7c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2019
ms.locfileid: "69620887"
---
# <a name="watchos-background-tasks-in-xamarin"></a>Tareas en segundo plano de watchos en Xamarin

Con watchos 3, hay tres formas principales en que una aplicación de inspección puede mantener la información actualizada: 

- Usar una de las diversas tareas en segundo plano nuevas. 
- Tener una de sus complicaciones en la superficie de inspección (lo que le permite actualizar el tiempo). 
- Tener el PIN del usuario para la aplicación en el nuevo Dock (donde se mantiene en memoria y se actualiza con frecuencia). 

## <a name="keeping-an-app-up-to-date"></a>Mantener actualizada una aplicación

Antes de analizar todas las formas en que un desarrollador puede mantener actualizados y actualizados la interfaz de usuario y los datos de una aplicación de watchos, en esta sección se examina un conjunto típico de patrones de uso y cómo un usuario puede moverse entre su iPhone y su Apple Watch a lo largo del día según  la hora del día y la actividad que están realizando actualmente (por ejemplo, la conducción).

Considere el ejemplo siguiente:

[![](background-tasks-images/update00.png "Cómo un usuario puede moverse entre su iPhone y sus Apple Watch a lo largo del día")](background-tasks-images/update00.png#lightbox)

1. En la mañana, mientras esperaba una cafetería, el usuario examina las noticias actuales en su iPhone durante varios minutos.
2. Antes de irse de la cafetería, comprueba rápidamente el tiempo con una complicación en su superficie de inspección.
3. Antes del almuerzo, usan la aplicación Maps en el iPhone para buscar un restaurante cercano y reservar una reserva para cumplir con un cliente.
4. Mientras viaja al restaurante, reciben una notificación en su Apple Watch y con una vista rápida, saben que su cita del almuerzo se está ejecutando tarde.
5. En la tarde, usan la aplicación Maps en el iPhone para comprobar el tráfico antes de conducir a casa.
6. En lo que se refiere a la Página principal, reciben una notificación iMessage en su Apple Watch pidiéndoles que recojan cierta leche y utilicen la característica de respuesta rápida para enviar la respuesta "OK".

Debido a la naturaleza de "vista rápida" (menos de tres segundos) de cómo un usuario desea usar una aplicación Apple Watch, normalmente no hay suficiente tiempo para que la aplicación Capture la información deseada y actualice su interfaz de usuario antes de mostrarla al usuario.

Mediante el uso de las nuevas API que Apple ha incluido en watchos 3, la aplicación puede programar una _actualización en segundo plano_ y tener la información deseada lista antes de que el usuario la solicite. Tome el ejemplo de las complicaciones meteorológicas descritas anteriormente:

[![](background-tasks-images/update01.png "Un ejemplo de la complicación meteorológica")](background-tasks-images/update01.png#lightbox)

1. La aplicación programa para que el sistema reactivarán en un momento determinado. 
2. La aplicación captura la información que necesitará para generar la actualización.
3. La aplicación regenera su interfaz de usuario para reflejar los nuevos datos.
4. Cuando el usuario se acerca a la complicación de la aplicación, tiene información actualizada sin que el usuario tenga que esperar a la actualización.

Como se ha indicado anteriormente, el sistema watchos reactiva la aplicación con una o más tareas, de las cuales tiene un grupo muy limitado disponible:

[![](background-tasks-images/update02.png "El sistema watchos activa la aplicación con una o más tareas")](background-tasks-images/update02.png#lightbox)

Apple recomienda hacer la mayor parte de esta tarea (ya que es un recurso tan limitado a la aplicación) manteniéndola hasta que la aplicación haya finalizado el proceso de actualización.

El sistema entrega estas tareas llamando al nuevo `HandleBackgroundTasks` método `WKExtensionDelegate` del delegado. Por ejemplo:

```csharp
using System;
using Foundation;
using WatchKit;

namespace MonkeyWatch.MonkeySeeExtension
{
  public class ExtensionDelegate : WKExtensionDelegate
  {
    #region Constructors
    public ExtensionDelegate ()
    {
    }
    #endregion

    #region Override Methods
    public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
    {
      // Handle background request here
      ...
    }
    #endregion
  }
}
```

Cuando la aplicación ha finalizado la tarea determinada, la devuelve al sistema mediante su marca completada:

[![](background-tasks-images/update03.png "La tarea vuelve al sistema marcando como completado")](background-tasks-images/update03.png#lightbox)

<a name="New-Background-Tasks" />

## <a name="new-background-tasks"></a>Nuevas tareas en segundo plano

watchos 3 presenta varias tareas en segundo plano que una aplicación puede usar para actualizar su información, lo que garantiza que tiene el contenido que necesita el usuario antes de abrir la aplicación, por ejemplo:

- **Actualización de la aplicación en segundo plano** : la tarea [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) permite que la aplicación actualice su estado en segundo plano. Normalmente esto incluirá otra tarea, como la descarga de contenido nuevo desde Internet mediante un [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession).
- **Actualización de instantánea de fondo** : la tarea [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) permite que la aplicación actualice su contenido y la interfaz de usuario antes de que el sistema tome una instantánea que se usará para rellenar el Dock.
- **Conectividad de inspección en segundo plano** : la tarea [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) se inicia para la aplicación cuando recibe datos de fondo del iPhone emparejado.
- **Sesión de URL en segundo plano** : la tarea [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) se inicia para la aplicación cuando una transferencia en segundo plano requiere autorización o se completa (correctamente o con errores).

Estas tareas se tratarán en detalle en las secciones siguientes.

<a name="WKApplicationRefreshBackgroundTask" />

### <a name="wkapplicationrefreshbackgroundtask"></a>WKApplicationRefreshBackgroundTask

`WKApplicationRefreshBackgroundTask` Es una tarea genérica que se puede programar para que la aplicación se reactivarán en una fecha futura:

[![](background-tasks-images/update04.png "Un reactivarán de WKApplicationRefreshBackgroundTask en una fecha futura")](background-tasks-images/update04.png#lightbox)

En el tiempo de ejecución de la tarea, la aplicación puede realizar cualquier tipo de procesamiento local, como actualizar una escala de tiempo complicada o capturar `NSUrlSession`algunos datos necesarios con.


<a name="WKURLSessionRefreshBackgroundTask" />

### <a name="wkurlsessionrefreshbackgroundtask"></a>WKURLSessionRefreshBackgroundTask

El sistema enviará un `WKURLSessionRefreshBackgroundTask` cuando los datos terminen la descarga y estén listos para ser procesados por la aplicación:

[![](background-tasks-images/update05.png "WKURLSessionRefreshBackgroundTask cuando los datos han finalizado la descarga")](background-tasks-images/update05.png#lightbox)

Una aplicación no deja de ejecutarse mientras los datos se están descargando en segundo plano. En su lugar, la aplicación programa la solicitud de datos, se suspende y el sistema controla la descarga de los datos, reactivando la aplicación solo cuando se complete la descarga.

<a name="WKSnapshotRefreshBackgroundTask" />

### <a name="wksnapshotrefreshbackgroundtask"></a>WKSnapshotRefreshBackgroundTask

En watchos 3, Apple ha agregado el Dock en el que los usuarios pueden anclar sus aplicaciones favoritas y acceder a ellas rápidamente. Cuando el usuario presiona el botón lateral en el Apple Watch, se mostrará una galería de instantáneas de aplicación ancladas. El usuario puede deslizar el dedo hacia la izquierda o la derecha para encontrar la aplicación deseada y, a continuación, puntear en la aplicación para iniciarla y reemplazar la instantánea por la interfaz de la aplicación en ejecución.

[![](background-tasks-images/update06.png "Reemplazar la instantánea con la interfaz de aplicaciones en ejecución")](background-tasks-images/update06.png#lightbox)

Periódicamente, el sistema toma instantáneas de la interfaz de usuario de la aplicación `WKSnapshotRefreshBackgroundTask`(mediante el envío de un) y las usa para rellenar el Dock. watchos ofrece a la aplicación la oportunidad de actualizar su contenido y la interfaz de usuario antes de tomar esta instantánea.

Las instantáneas son muy importantes en watchos 3, ya que funcionan como imágenes de inicio y vista previa de la aplicación. Si el usuario se liquida en una aplicación en el Dock, se expandirá a pantalla completa, entrará en primer plano y comenzará a ejecutarse, por lo que es imperativo que la instantánea esté actualizada:

[![](background-tasks-images/update07.png "Si el usuario se aplica a una aplicación en el Dock, se expandirá a pantalla completa.")](background-tasks-images/update07.png#lightbox)

De nuevo, el sistema emitirá una `WKSnapshotRefreshBackgroundTask` para que la aplicación se pueda preparar (mediante la actualización de los datos y la interfaz de usuario) antes de que se tome la instantánea:

[![](background-tasks-images/update08.png "La aplicación se puede preparar mediante la actualización de los datos y la interfaz de usuario antes de que se tome la instantánea.")](background-tasks-images/update08.png#lightbox)

Cuando la aplicación marca el `WKSnapshotRefreshBackgroundTask` completado, el sistema tomará automáticamente una instantánea de la interfaz de usuario de la aplicación.

> [!IMPORTANT]
> Es importante programar siempre una `WKSnapshotRefreshBackgroundTask` después de que la aplicación haya recibido nuevos datos y actualizado su interfaz de usuario, o el usuario no verá la información modificada.




Además, cuando el usuario recibe una notificación de la aplicación y la pulsa para poner la aplicación en primer plano, la instantánea debe estar actualizada, ya que actúa como la pantalla de inicio también:

[![](background-tasks-images/update09.png "El usuario recibe una notificación de la aplicación y la pulsa para poner la aplicación en primer plano.")](background-tasks-images/update09.png#lightbox)

Si ha transcurrido más de una hora desde que el usuario ha interactuado con una aplicación de watchos, podrá volver a su estado predeterminado. El estado predeterminado puede significar diferentes cosas para diferentes aplicaciones y, en función del diseño de una aplicación, es posible que no tenga un estado predeterminado.

<!--TODO - Possibly link to Apple's Designing Great Apple Watch Experiences video or add our own version here...-->

<a name="WKWatchConnectivityRefreshBackgroundTask" />

### <a name="wkwatchconnectivityrefreshbackgroundtask"></a>WKWatchConnectivityRefreshBackgroundTask

En watchos 3, Apple ha integrado la conectividad de inspección con la API de actualización en `WKWatchConnectivityRefreshBackgroundTask`segundo plano a través de la nueva. Con esta nueva característica, una aplicación de iPhone puede proporcionar datos nuevos a su homólogo de aplicación de inspección, mientras que la aplicación watchos se ejecuta en segundo plano:

[![](background-tasks-images/update10.png "Una aplicación de iPhone puede proporcionar datos nuevos a su homólogo de aplicación de inspección, mientras que la aplicación watchos se ejecuta en segundo plano.")](background-tasks-images/update10.png#lightbox)

El inicio de una incomplicaciones, el contexto de la aplicación, el envío de un archivo o la actualización de la información del usuario desde la aplicación de iPhone reactivará la aplicación Apple Watch en segundo plano.

Cuando la aplicación de inspección se reactivarán a `WKWatchConnectivityRefreshBackgroundTask` través de un, tendrá que usar los métodos de API estándar para recibir los datos de la aplicación de iPhone.

[![](background-tasks-images/update11.png "El flujo de datos WKWatchConnectivityRefreshBackgroundTask")](background-tasks-images/update11.png#lightbox)

1. Asegúrese de que la sesión está activada.
2. Supervise la nueva `HasContentPending` propiedad siempre que el valor sea `true`, la aplicación todavía tenga datos para procesar. Como antes, la aplicación debe mantenerse en la tarea hasta que haya finalizado el procesamiento de todos los datos.
3. Cuando no haya más datos que procesar (`HasContentPending = false`), marque la tarea completada para devolverla al sistema. Si no lo hace, se agotará el tiempo de ejecución asignado en segundo plano de la aplicación, lo que resultará en un informe de bloqueo.

<a name="The-Background-API-Lifecycle" />

## <a name="the-background-api-lifecycle"></a>Ciclo de vida de API en segundo plano

Una vez que se colocan todos los elementos de la nueva API de tareas en segundo plano, un conjunto típico de interacciones sería similar al siguiente:

[![](background-tasks-images/update12.png "Ciclo de vida de API en segundo plano")](background-tasks-images/update12.png#lightbox)

1. En primer lugar, la aplicación watchos programa una tarea en segundo plano para que se activo como algún punto en el futuro.
2. El sistema reactivarán la aplicación y envía una tarea.
3. La aplicación procesa la tarea para completar todo el trabajo necesario.
4. Como resultado del procesamiento de la tarea, es posible que la aplicación necesite programar más tareas en segundo plano para completar más trabajo en el futuro, como la descarga de `NSUrlSession`más contenido mediante.
5. La aplicación marca la tarea como completada y la devuelve al sistema.

<a name="Using-Resources-Responsibly" />

## <a name="using-resources-responsibly"></a>Uso de los recursos de manera responsable

Es fundamental que una aplicación de watchos se comporta de forma responsable dentro de este ecosistema limitando su consumo en los recursos compartidos del sistema.

Eche un vistazo al siguiente escenario:

[![](background-tasks-images/update13.png "Una aplicación de watchos limita su consumo en los recursos compartidos del sistema")](background-tasks-images/update13.png#lightbox)

1. El usuario inicia una aplicación de watchos en 1:00 PM.
2. La aplicación programa una tarea para reactivar y descargar contenido nuevo en una hora a las 2:00 P.M.
3. A las 1:50 p. m., el usuario vuelve a abrir la aplicación, lo que le permite actualizar los datos y la interfaz de usuario en este momento.
4. En lugar de permitir que la tarea reactive la aplicación en 10 minutos, la aplicación debe volver a programar la tarea para que se ejecute una hora más tarde a las 2:50 P.M.

Aunque cada aplicación es diferente, Apple sugiere buscar patrones de uso, como los mostrados anteriormente, para ayudar a conservar los recursos del sistema.

<a name="Implementing-Background-Tasks" />

## <a name="implementing-background-tasks"></a>Implementar tareas en segundo plano

Por ejemplo, este documento usará la aplicación MonkeySoccer Sports falsa que notifica las puntuaciones de fútbol al usuario. 

Eche un vistazo al siguiente escenario de uso típico:

[![](background-tasks-images/update14.png "Escenario de uso típico")](background-tasks-images/update14.png#lightbox)

El equipo de fútbol favorito del usuario está reproduciendo una gran coincidencia de 7:00 PM a 9:00 p. m., por lo que la aplicación debe esperar que el usuario Compruebe la puntuación con regularidad y decida un intervalo de actualización de 30 minutos.

1. El usuario abre la aplicación y programa una tarea para la actualización en segundo plano 30 minutos más tarde. La API en segundo plano solo permite que un tipo de tarea en segundo plano se ejecute en un momento dado.
2. La aplicación recibe la tarea y actualiza sus datos y la interfaz de usuario, y luego programa para otra tarea en segundo plano 30 minutos más tarde. Es importante que el desarrollador tenga que programar otra tarea en segundo plano o que la aplicación no se vuelva a awoken para obtener más actualizaciones.
3. De nuevo, la aplicación recibe la tarea y actualiza sus datos, actualiza su interfaz de usuario y programa otra tarea en segundo plano 30 minutos después.
4. El mismo proceso se repite de nuevo.
5. Se recibe la última tarea en segundo plano y la aplicación actualiza los datos y la interfaz de usuario de nuevo. Puesto que esta es la puntuación final, no programa para una nueva actualización en segundo plano. 

<a name="Scheduling-for-Background-Update" />

## <a name="scheduling-for-background-update"></a>Programación para la actualización en segundo plano

Dado el escenario anterior, la aplicación MonkeySoccer puede usar el código siguiente para programar una actualización en segundo plano:

```csharp
private void ScheduleNextBackgroundUpdate ()
{
  // Create a fire date 30 minutes into the future
  var fireDate = NSDate.FromTimeIntervalSinceNow (30 * 60);

  // Create 
  var userInfo = new NSMutableDictionary ();
  userInfo.Add (new NSString ("LastActiveDate"), NSDate.FromTimeIntervalSinceNow(0));
  userInfo.Add (new NSString ("Reason"), new NSString ("UpdateScore"));

  // Schedule for update
  WKExtension.SharedExtension.ScheduleBackgroundRefresh (fireDate, userInfo, (error) => {
    // Was the Task successfully scheduled?
    if (error == null) {
      // Yes, handle if needed
    } else {
      // No, report error
    }
  });
}
```

Crea un nuevo `NSDate` 30 minutos en el futuro cuando la aplicación quiere ser awoken y crea un `NSMutableDictionary` para contener los detalles de la tarea solicitada. El `ScheduleBackgroundRefresh` método`SharedExtension` de se usa para solicitar que se programe la tarea.

El sistema devolverá `NSError` si no pudo programar la tarea solicitada.

<a name="Processing-the-Update" />

## <a name="processing-the-update"></a>Procesar la actualización

A continuación, eche un vistazo más de cerca a la ventana de 5 minutos que muestra los pasos necesarios para actualizar la puntuación:

[![](background-tasks-images/update15.png "La ventana de 5 minutos que muestra los pasos necesarios para actualizar la puntuación")](background-tasks-images/update15.png#lightbox)

1. En 7:30:02 PM, el sistema activa la aplicación y se le proporciona la tarea actualizar en segundo plano. Su primera prioridad es obtener las puntuaciones más recientes del servidor. Consulte [programación de un NSUrlSession](#Scheduling-a-NSUrlSession) a continuación.
2. En 7:30:05, la aplicación completa la tarea original, el sistema pone la aplicación en suspensión y continúa descargando los datos solicitados en segundo plano.
3. Cuando el sistema completa la descarga, crea una nueva tarea para reactivar la aplicación para que pueda procesar la información descargada. Consulte [Administración de tareas en segundo plano](#Handling-Background-Tasks) y [control de la descarga completada](#Handling-the-Download-Completing) a continuación. 
4. La aplicación guarda la información actualizada y marca la tarea como completada. El desarrollador puede verse tentado de actualizar la interfaz de usuario de la aplicación en este momento. sin embargo, Apple sugiere programar una tarea de instantánea para controlar ese proceso. Vea [la programación de una actualización de instantánea](#Scheduling-a-Snapshot-Update) a continuación.
5. La aplicación recibe la tarea de instantáneas, actualiza su interfaz de usuario y marca la tarea como completada. Consulte [Administración de una actualización de instantánea](#Handling-a-Snapshot-Update) a continuación.

<a name="Scheduling-a-NSUrlSession" />

## <a name="scheduling-a-nsurlsession"></a>Programación de un NSUrlSession

El siguiente código se puede utilizar para programar la descarga de las puntuaciones más recientes:

```csharp
private void ScheduleURLUpdateSession ()
{
  // Create new configuration
  var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.example.urlsession");

  // Create new session
  var backgroundSession = NSUrlSession.FromConfiguration (configuration);

  // Create and start download task
  var downloadTask = backgroundSession.CreateDownloadTask (new NSUrl ("https://example.com/gamexxx/currentScores.json"));
  downloadTask.Resume ();
}
```

Configura y crea una nueva `NSUrlSession`y, a continuación, usa esa sesión para crear una nueva tarea de descarga mediante el `CreateDownloadTask` método. Llama `Resume` al método de la tarea de descarga para iniciar la sesión.

<a name="Handling-Background-Tasks" />

## <a name="handling-background-tasks"></a>Controlar tareas en segundo plano

Al invalidar el `HandleBackgroundTasks` método `WKExtensionDelegate`de, la aplicación puede controlar las tareas en segundo plano de entrada:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using WatchKit;

namespace MonkeySoccer.MonkeySoccerExtension
{
  public class ExtensionDelegate : WKExtensionDelegate
  {
    #region Computed Properties
    public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
    #endregion

    ...
    
    #region Public Methods
    public void CompleteTask (WKRefreshBackgroundTask task)
    {
      // Mark the task completed and remove from the collection
      task.SetTaskCompleted ();
      PendingTasks.Remove (task);
    }
    #endregion 

    #region Override Methods
    public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
    {
      // Handle background request
      foreach (WKRefreshBackgroundTask task in backgroundTasks) {
        // Is this a background session task?
        var urlTask = task as WKUrlSessionRefreshBackgroundTask;
        if (urlTask != null) {
          // Create new configuration
          var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration (urlTask.SessionIdentifier);

          // Create new session
          var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);

          // Keep track of all pending tasks
          PendingTasks.Add (task);
        } else {
          // Ensure that all tasks are completed
          task.SetTaskCompleted ();
        }
      }
    }
    #endregion
    
    ...
  }
}
```

El `HandleBackgroundTasks` método recorre todas las tareas que el sistema ha enviado a la aplicación (en `backgroundTasks`) buscando `WKUrlSessionRefreshBackgroundTask`. Si se encuentra una, se vuelve a unir a la sesión y se adjunta `NSUrlSessionDownloadDelegate` un para controlar la finalización de la descarga (vea el apartado sobre [control de la descarga completada](#Handling-the-Download-Completing) a continuación):

```csharp
// Create new session
var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);
```

Mantiene un identificador en la tarea hasta que se ha completado agregándolo a una colección:

```csharp
public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
...

// Keep track of all pending tasks
PendingTasks.Add (task);
```

Todas las tareas que se envían a la aplicación se deben completar para cualquier tarea que no se esté administrando actualmente, márquela como completada:

```csharp
if (urlTask != null) {
  ...
} else {
  // Ensure that all tasks are completed
  task.SetTaskCompleted ();
}
```

<a name="Handling-the-Download-Completing" />

## <a name="handling-the-download-completing"></a>Control de la descarga completada

La aplicación MonkeySoccer usa el siguiente `NSUrlSessionDownloadDelegate` delegado para controlar la finalización de la descarga y procesar los datos solicitados:

```csharp
using System;
using Foundation;
using WatchKit;

namespace MonkeySoccer.MonkeySoccerExtension
{
  public class BackgroundSessionDelegate : NSUrlSessionDownloadDelegate
  {
    #region Computed Properties
    public ExtensionDelegate WatchExtensionDelegate { get; set; }

    public WKRefreshBackgroundTask Task { get; set; }
    #endregion

    #region Constructors
    public BackgroundSessionDelegate (ExtensionDelegate extensionDelegate, WKRefreshBackgroundTask task)
    {
      // Initialize
      this.WatchExtensionDelegate = extensionDelegate;
      this.Task = task;
    }
    #endregion

    #region Override Methods
    public override void DidFinishDownloading (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, NSUrl location)
    {
      // Handle the downloaded data
      ...

      // Mark the task completed
      WatchExtensionDelegate.CompleteTask (Task);

    }
    #endregion
  }
}
```

Cuando se inicializa, mantiene un identificador para `ExtensionDelegate` y el `WKRefreshBackgroundTask` que lo generó. Invalida el `DidFinishDownloading` método para controlar la finalización de la descarga. A continuación, `CompleteTask` usa el método `ExtensionDelegate` de para informar a la tarea de que se ha completado y quitarla de la colección de tareas pendientes. Vea [administrar tareas en segundo plano](#Handling-Background-Tasks) anteriormente.

<a name="Scheduling-a-Snapshot-Update" />

## <a name="scheduling-a-snapshot-update"></a>Programar una actualización de instantánea

El código siguiente puede usarse para programar una tarea de instantánea para actualizar la interfaz de usuario con las puntuaciones más recientes:

```csharp
private void ScheduleSnapshotUpdate ()
{
  // Create a fire date of now
  var fireDate = NSDate.FromTimeIntervalSinceNow (0);

  // Create user info dictionary
  var userInfo = new NSMutableDictionary ();
  userInfo.Add (new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0));
  userInfo.Add (new NSString ("reason"), new NSString ("UpdateScore"));

  // Schedule for update
  WKExtension.SharedExtension.ScheduleSnapshotRefresh (fireDate, userInfo, (error) => {
    // Was the Task successfully scheduled?
    if (error == null) {
      // Yes, handle if needed
    } else {
      // No, report error
    }
  });
}
```

Al igual que el `NSDate` `NSMutableDictionary` método anterior, crea un nuevo para cuando la aplicación quiere ser awoken y crea un para contener los detalles de la tarea solicitada. `ScheduleURLUpdateSession` El `ScheduleSnapshotRefresh` método`SharedExtension` de se usa para solicitar que se programe la tarea.

El sistema devolverá `NSError` si no pudo programar la tarea solicitada.

<a name="Handling-a-Snapshot-Update" />

## <a name="handling-a-snapshot-update"></a>Controlar una actualización de instantánea

Para controlar la tarea de instantáneas `HandleBackgroundTasks` , el método (vea [controlar las tareas en segundo plano](#Handling-Background-Tasks) anteriores) se modifica para que tenga un aspecto similar al siguiente:

```csharp
public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
{
  // Handle background request
  foreach (WKRefreshBackgroundTask task in backgroundTasks) {
    // Take action based on task type
    if (task is WKUrlSessionRefreshBackgroundTask) {
      var urlTask = task as WKUrlSessionRefreshBackgroundTask;

      // Create new configuration
      var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration (urlTask.SessionIdentifier);

      // Create new session
      var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);

      // Keep track of all pending tasks
      PendingTasks.Add (task);
    } else if (task is WKSnapshotRefreshBackgroundTask) {
      var snapshotTask = task as WKSnapshotRefreshBackgroundTask;

      // Update UI
      ...

      // Create a expiration date 30 minutes into the future
      var expirationDate = NSDate.FromTimeIntervalSinceNow (30 * 60);

      // Create user info dictionary
      var userInfo = new NSMutableDictionary ();
      userInfo.Add (new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0));
      userInfo.Add (new NSString ("reason"), new NSString ("UpdateScore"));

      // Mark task complete
      snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
    } else {
      // Ensure that all tasks are completed
      task.SetTaskCompleted ();
    }
  }
}
```

El método comprueba el tipo de tarea que se está procesando. Si es `WKSnapshotRefreshBackgroundTask` , obtiene acceso a la tarea:

```csharp
var snapshotTask = task as WKSnapshotRefreshBackgroundTask;
```

El método actualiza la interfaz de usuario y después `NSDate` crea un para indicar al sistema Cuándo estará obsoleta la instantánea. Crea un `NSMutableDictionary` con información de usuario para describir la nueva instantánea y marca la tarea de instantáneas como completada con esta información:

```csharp
// Mark task complete
snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
```

Además, también indica a la tarea de instantáneas que la aplicación no devuelve el estado predeterminado (en el primer parámetro). Las aplicaciones que no tienen un concepto de estado predeterminado siempre deben establecer esta propiedad `true`en.

<a name="Working-Efficiently" />

## <a name="working-efficiently"></a>Trabajar de forma eficaz

Como se ve en el ejemplo anterior de la ventana de cinco minutos que tardó la aplicación MonkeySoccer en actualizar sus puntuaciones, trabajando de forma eficaz y usando las nuevas tareas en segundo plano de watchos 3, la aplicación solo estaba activa durante un total de 15 segundos: 

[![](background-tasks-images/update16.png "La aplicación solo estaba activa durante un total de 15 segundos")](background-tasks-images/update16.png#lightbox)

Esto reduce el impacto que tendrá la aplicación en los recursos de Apple Watch disponibles y en la duración de la batería, y también permite que la aplicación funcione mejor con otras aplicaciones que se ejecutan en el reloj.

<a name="How-Scheduling-Works" />

## <a name="how-scheduling-works"></a>Cómo funciona la programación

Aunque una aplicación watchos 3 está en primer plano, siempre está programada para ejecutarse y puede realizar cualquier tipo de procesamiento necesario, como actualizar datos o volver a dibujar su interfaz de usuario. Cuando la aplicación se mueve a fondo, normalmente la suspende el sistema y se detienen todas las operaciones en tiempo de ejecución. 

Mientras la aplicación está en segundo plano, el sistema puede destinarla para ejecutar rápidamente una tarea específica. Por lo tanto, en watchos 2, es posible que el sistema reactive temporalmente una aplicación en segundo plano para hacer cosas como controlar una notificación de gran tamaño o actualizar la complicación de la aplicación. En watchos 3, hay varias maneras nuevas en que una aplicación se puede ejecutar en segundo plano.

Mientras una aplicación está en segundo plano, el sistema impone varios límites en ella:

- Solo se proporcionan unos segundos para completar una tarea determinada. El sistema tiene en cuenta no solo la cantidad de tiempo pasada sino también la potencia de la CPU que consume la aplicación para obtener este límite.
- Cualquier aplicación que supere sus límites se eliminará con los siguientes códigos de error:
  - **CPU** - 0xc51bad01
  - **Hora** : 0xc51bad02
- El sistema impondrá distintos límites en función del tipo de tarea en segundo plano que ha solicitado que la aplicación realice. Por ejemplo, `WKApplicationRefreshBackgroundTask` las `WKURLSessionRefreshBackgroundTask` tareas y se proporcionan tiempos de ejecución ligeramente mayores en otros tipos de tareas en segundo plano.

<a name="Complications-and-App-Updates" />

### <a name="complications-and-app-updates"></a>Complicaciones y actualizaciones de aplicaciones

Además de las nuevas tareas en segundo plano que Apple ha agregado a watchos 3, las complicaciones de la aplicación watchos pueden afectar a la forma y el momento en que la aplicación recibe actualizaciones en segundo plano.

Las complicaciones son pequeños elementos visuales que proporcionan información útil de un vistazo. En función de la superficie de inspección seleccionada, el usuario tiene la capacidad de personalizar una esfera de inspección con una o más complicaciones que puede proporcionar una aplicación de inspección en watchos 3.

Si el usuario incluye una de las complicaciones de la aplicación en su superficie de inspección, proporciona a la aplicación las siguientes ventajas actualizadas:

- Hace que el sistema mantenga la aplicación en un estado listo para iniciar, donde intenta iniciar la aplicación en segundo plano, la mantiene en la memoria y le da tiempo adicional para actualizar.
- Se garantizan complicaciones al menos 50 actualizaciones de las inserciones al día.

El desarrollador siempre debe esforzarse por crear atractivas complicaciones para que sus aplicaciones adquieran que el usuario las agregue a su superficie de inspección por las razones mencionadas anteriormente.

En watchos 2, las complicaciones fueron la manera principal en que una aplicación recibió tiempo de ejecución mientras estaba en segundo plano. En watchos 3, todavía se garantizará que una aplicación de complejidad reciba varias actualizaciones por hora, pero puede usar `WKExtensions` para solicitar más tiempo de ejecución para actualizar sus complicaciones.

Eche un vistazo al código siguiente que se usa para actualizar la complicación desde la aplicación de iPhone conectada:

```csharp
using System;
using WatchConnectivity;
using UIKit;
using Foundation;
using System.Collections.Generic;
using System.Linq;
...

private void UpdateComplication ()
{

  // Get session and the number of remaining transfers
  var session = WCSession.DefaultSession;
  var transfers = session.RemainingComplicationUserInfoTransfers;

  // Create user info dictionary
  var iconattrs = new Dictionary<NSString, NSObject>
    {
      {new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0)},
      {new NSString ("reason"), new NSString ("UpdateScore")}
    };

  var userInfo = NSDictionary<NSString, NSObject>.FromObjectsAndKeys (iconattrs.Values.ToArray (), iconattrs.Keys.ToArray ());

  // Take action based on the number of transfers left
  if (transfers < 1) {
    // No transfers left, either attempt to send or inform
    // user of situation.
    ...
  } else if (transfers < 11) {
    // Running low on transfers, only send on important updates
    // else conserve for a significant change.
    ...
  } else {
    // Send data
    session.TransferCurrentComplicationUserInfo (userInfo);
  }
}
```

Usa la `RemainingComplicationUserInfoTransfers` propiedad `WCSession` de para ver el número de transferencias de prioridad alta que la aplicación ha dejado durante el día y, a continuación, toma medidas en función de ese número. Si la aplicación empieza a ejecutarse bajo transferencias, puede mantener el envío de actualizaciones secundarias y enviar solo información cuando se produce un cambio significativo.

<a name="Scheduling-and-Dock" />

### <a name="scheduling-and-the-dock"></a>Programación y Dock

En watchos 3, Apple ha agregado el Dock en el que los usuarios pueden anclar sus aplicaciones favoritas y acceder a ellas rápidamente. Cuando el usuario presiona el botón lateral en el Apple Watch, se mostrará una galería de instantáneas de aplicación ancladas. El usuario puede deslizar el dedo hacia la izquierda o la derecha para encontrar la aplicación deseada y, a continuación, puntear en la aplicación para iniciarla y reemplazar la instantánea por la interfaz de la aplicación en ejecución.

[![](background-tasks-images/dock01.png "El Dock")](background-tasks-images/dock01.png#lightbox)

El sistema toma instantáneas de la interfaz de usuario de la aplicación de forma periódica y las usa para rellenar los documentos. watchos ofrece a la aplicación la oportunidad de actualizar su contenido y la interfaz de usuario antes de tomar esta instantánea.

Las aplicaciones que se han anclado a la base de acoplamiento pueden esperar lo siguiente:

- Recibirán un mínimo de una actualización por hora. Esto incluye una tarea de actualización de la aplicación y una tarea de instantánea.
- El presupuesto de actualización se distribuye entre todas las aplicaciones del Dock. Por lo tanto, cuantas menos aplicaciones haya anclado el usuario, mayor será la cantidad de actualizaciones posibles que recibirá cada aplicación.
- La aplicación se conservará en la memoria para que la aplicación se reanude rápidamente cuando se selecciona desde el Dock.

La última aplicación que ejecutó el usuario se considerará la aplicación _usada más recientemente_ y ocupará la última ranura en el Dock. Desde allí, el usuario puede elegir anclarlo permanentemente a la base de acoplamiento. El uso más reciente se tratará como cualquier otra aplicación favorita que el usuario ya haya anclado a la base de acoplamiento.

> [!IMPORTANT]
> Las aplicaciones que solo se han agregado a la pantalla principal no tendrán ninguna programación normal. Para recibir actualizaciones periódicas de programación y en segundo plano, se _debe_ agregar una aplicación a la base de acoplamiento.

Como se indicó anteriormente en este documento, las instantáneas son muy importantes en watchos 3, ya que funcionan como imágenes de inicio y vista previa de la aplicación. Si el usuario se liquida en una aplicación en el Dock, se expandirá a pantalla completa, entrará en primer plano y comenzará a ejecutarse, por lo que es imperativo que la instantánea esté actualizada.

Puede haber ocasiones en las que el sistema decida que necesita una instantánea nueva de la interfaz de usuario de la aplicación. En este caso, la solicitud de instantáneas no contará con el presupuesto de tiempo de ejecución de la aplicación. Lo siguiente desencadenará una solicitud de instantánea del sistema:

- Actualización de la escala de tiempo de complicación.
- Interacción del usuario con la notificación de una aplicación.
- Cambiar de primer plano al estado en segundo plano.
- Después de una hora de estar en el estado en segundo plano, por lo que la aplicación puede volver al estado predeterminado.
- Cuando se inicia watchos primero.

<a name="Best-Practices" />

## <a name="best-practices"></a>Procedimientos recomendados 

Apple sugiere las siguientes prácticas recomendadas al trabajar con tareas en segundo plano:

- Programe tantas veces como la aplicación deba actualizarse. Cada vez que se ejecuta la aplicación, debe volver a evaluar sus necesidades futuras y ajustar esta programación según sea necesario.
- Si el sistema envía una tarea de actualización en segundo plano y la aplicación no requiere una actualización, postergue el trabajo hasta que se requiera una actualización.
- Tenga en cuenta todas las oportunidades de tiempo de ejecución disponibles para una aplicación:
  - Activación de acoplamiento y en primer plano.
  - Notificaciones.
  - Actualizaciones de complicaciones.
  - Actualizaciones en segundo plano.
- Se `ScheduleBackgroundRefresh` usa para el tiempo de ejecución en segundo plano de uso general, como:
  - Sondeo del sistema para obtener información.
  - Programe `NSURLSessions` el futuro para solicitar datos de fondo. 
  - Transiciones de hora conocidas.
  - Desencadenar actualizaciones de complejidad.

<a name="Snapshot-Best-Practices" />

## <a name="snapshot-best-practices"></a>Procedimientos recomendados de instantánea

Al trabajar con actualizaciones de instantáneas, Apple realiza las siguientes sugerencias:

- Invalide las instantáneas solo cuando sea necesario, por ejemplo, cuando se produzca un cambio significativo en el contenido.
- Evite la invalidación de instantáneas de alta frecuencia. Por ejemplo, una aplicación de temporizador no debe actualizar la instantánea cada segundo, solo debería realizarse cuando el temporizador haya finalizado.

<a name="App-Data-Flow" />

## <a name="app-data-flow"></a>Flujo de datos de la aplicación

Apple sugiere lo siguiente para trabajar con el flujo de datos:

[![](background-tasks-images/update17.png "Diagrama de flujo de datos de aplicaciones")](background-tasks-images/update17.png#lightbox)

Un evento externo (como la conectividad de inspección) activa la aplicación. Esto obliga a la aplicación a actualizar su modelo de datos (que representa el estado actual de las aplicaciones). Como resultado de la modificación del modelo de datos, la aplicación tendrá que actualizar sus complicaciones, solicitar una nueva instantánea, posiblemente iniciar un `NSURLSession` segundo plano para extraer más datos y programar nuevas actualizaciones en segundo plano.

<a name="The-App-Lifecycle" />

## <a name="the-app-lifecycle"></a>Ciclo de vida de la aplicación

Debido al acoplamiento y a la capacidad de anclar aplicaciones favoritas a él, Apple considera que los usuarios se van a mover entre muchas más aplicaciones, con mucha más frecuencia, y luego lo hicieron con watchos 2. Como resultado, la aplicación debe estar lista para controlar este cambio y moverse rápidamente entre los Estados de primer y segundo plano.

Apple tiene las siguientes sugerencias:

- Asegúrese de que la aplicación finaliza cualquier tarea en segundo plano tan pronto como sea posible al entrar en el modo de activación en primer plano.
- Asegúrese de finalizar todo el trabajo en primer plano antes de entrar `NSProcessInfo.PerformExpiringActivity`en el fondo llamando a.
- Al probar una aplicación en el simulador de watchos, no se aplicará ninguno de los presupuestos de tareas para que una aplicación pueda actualizarse tanto como sea necesario para probar correctamente una característica.
- Pruebe siempre el hardware real Apple Watch para asegurarse de que la aplicación no se está ejecutando más allá de sus presupuestos antes de publicar en iTunes Connect.
- Apple sugiere mantener el Apple Watch en el cargador durante las pruebas y la depuración.
- Asegúrese de que tanto el inicio en frío como la reanudación de una aplicación se prueban exhaustivamente.
- Compruebe que se han completado todas las tareas de la aplicación.
- Varíe el número de aplicaciones ancladas en el Dock para probar los escenarios de los mejores y peores casos.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se han tratado las mejoras que Apple ha realizado en watchos y cómo se pueden usar para mantener actualizada una aplicación de inspección. En primer lugar, se ha incluido toda la nueva tarea en segundo plano que Apple ha agregado en watchos 3. A continuación, se ha tratado el ciclo de vida de la API en segundo plano y cómo implementar tareas en segundo plano en una aplicación Xamarin watchos. Por último, se ha explicado cómo funciona la programación y se han ofrecido algunos procedimientos recomendados.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
