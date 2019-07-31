---
title: Aplicaciones de entrenamiento de watchos en Xamarin
description: En este artículo se describen las mejoras que Apple ha realizado en las aplicaciones de entrenamiento en watchos 3 y cómo implementarlas en Xamarin.
ms.prod: xamarin
ms.assetid: F1D19635-A738-43E5-9873-1FC1BA44EEDF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 366f0cac6c21d5e749871a289bdec78f64299c0b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656447"
---
# <a name="watchos-workout-apps-in-xamarin"></a>Aplicaciones de entrenamiento de watchos en Xamarin

_En este artículo se describen las mejoras que Apple ha realizado en las aplicaciones de entrenamiento en watchos 3 y cómo implementarlas en Xamarin._


Novedad de watchos 3, las aplicaciones relacionadas con el entrenamiento tienen la capacidad de ejecutarse en segundo plano en el Apple Watch y obtener acceso a los datos de HealthKit. Su aplicación primaria basada en iOS 10 también tiene la capacidad de iniciar la aplicación basada en watchos 3 sin la intervención del usuario.

Los siguientes temas se tratarán en detalle:

## <a name="about-workout-apps"></a>Acerca de las aplicaciones de entrenamiento

Los usuarios de las aplicaciones de bienestar y entrenamiento pueden ser muy dedicados, destinando varias horas del día hacia sus objetivos de mantenimiento y bienestar. Como resultado, esperan aplicaciones fáciles de usar que recopilan y muestran datos con precisión y se integran sin problemas con el estado de Apple.

Una aplicación bien diseñada o de entrenamiento ayuda a los usuarios a representar sus actividades para alcanzar sus objetivos de idoneidad. El uso de las aplicaciones Apple Watch, idoneidad y entrenamiento tiene acceso inmediato a la tarifa cardíaca, la grabación de calorie y la detección de actividades.

[![](workout-apps-images/workout01.png "Ejemplo de aplicación de bienestar y entrenamiento")](workout-apps-images/workout01.png#lightbox)

Novedad de watchos 3, la _ejecución en segundo plano_ proporciona a las aplicaciones relacionadas con el entrenamiento la capacidad de ejecutarse en segundo plano en el Apple Watch y obtener acceso a los datos de HealthKit.

Este documento presenta la característica de ejecución en segundo plano, cubre el ciclo de vida de la aplicación de entrenamiento y muestra cómo una aplicación de entrenamiento puede contribuir a los _anillos de actividad_ del usuario en el Apple Watch.

## <a name="about-workout-sessions"></a>Acerca de las sesiones de entrenamiento

El corazón de cada aplicación de entrenamiento es una sesión de`HKWorkoutSession` _entrenamiento_ () que el usuario puede iniciar y detener. La API de la sesión de entrenamiento es fácil de implementar y proporciona varias ventajas a una aplicación de entrenamiento, como:

- Detección de movimiento y calorie en función del tipo de actividad.
- Contribución automática a los anillos de actividad del usuario.
- En una sesión, la aplicación se mostrará automáticamente siempre que el usuario reactive el dispositivo (ya sea elevando su muñeca o interactuando con el Apple Watch).

## <a name="about-background-running"></a>Acerca de la ejecución en segundo plano

Como se indicó anteriormente, con watchos 3, una aplicación de entrenamiento se puede establecer para que se ejecute en segundo plano. El uso de una aplicación de entrenamiento puede procesar los datos de los sensores del Apple Watch mientras se ejecuta en segundo plano. Por ejemplo, una aplicación puede seguir supervisando la tarifa cardíaca del usuario, aunque ya no se muestre en la pantalla.

La ejecución en segundo plano también proporciona la capacidad de presentar comentarios en directo al usuario en cualquier momento durante una sesión de entrenamiento activa, como enviar una alerta de hápticos para informar al usuario de su progreso actual.

Además, la ejecución en segundo plano permite que la aplicación actualice rápidamente su interfaz de usuario para que el usuario tenga los datos más recientes cuando observe rápidamente su Apple Watch.

Para mantener un alto rendimiento en Apple Watch, una aplicación de inspección que use la ejecución en segundo plano debe limitar la cantidad de trabajo en segundo plano para ahorrar batería. Si una aplicación usa una CPU excesiva mientras está en segundo plano, se puede suspender en watchos.

### <a name="enabling-background-running"></a>Habilitar la ejecución en segundo plano

Para habilitar la ejecución en segundo plano, haga lo siguiente:

1. En el **Explorador de soluciones**, haga doble clic en el archivo de `Info.plist` la aplicación de iPhone complementaria de la extensión de inspección para abrirlo y editarlo.
2. Cambie a la vista de **código fuente** : 

    [![](workout-apps-images/plist01.png "Vista de código fuente")](workout-apps-images/plist01.png#lightbox)
3. Agregue una nueva clave denominada `WKBackgroundModes` y establezca el **tipo** en `Array`: 

    [![](workout-apps-images/plist02.png "Agregue una nueva clave denominada WKBackgroundModes")](workout-apps-images/plist02.png#lightbox)
4. Agregue un nuevo elemento a la matriz con el **tipo** de `String` y un valor de `workout-processing`: 

    [![](workout-apps-images/plist03.png "Agregar un nuevo elemento a la matriz con el tipo de cadena y un valor de entrenamiento-procesamiento")](workout-apps-images/plist03.png#lightbox)
5. Guarde los cambios en el archivo.

## <a name="starting-a-workout-session"></a>Inicio de una sesión de entrenamiento

Hay tres pasos principales para iniciar una sesión de entrenamiento:

[![](workout-apps-images/workout02.png "Los tres pasos principales para iniciar una sesión de entrenamiento")](workout-apps-images/workout02.png#lightbox)

1. La aplicación debe solicitar autorización para acceder a los datos en HealthKit.
2. Cree un objeto de configuración de entrenamiento para el tipo de entrenamiento que se va a iniciar.
3. Cree e inicie una sesión de entrenamiento mediante la configuración de entrenamiento recién creada.

### <a name="requesting-authorization"></a>Solicitud de autorización

Antes de que una aplicación pueda acceder a los datos de HealthKit del usuario, debe solicitar y recibir la autorización del usuario. Dependiendo de la naturaleza de la aplicación de entrenamiento, podría crear los siguientes tipos de solicitudes:

- Autorización para escribir datos:
    - Entrenamientos
- Autorización para leer datos:
    - Energía quemada
    - Posición
    - Frecuencia cardíaca  

Para que una aplicación pueda solicitar autorización, debe configurarse para tener acceso a HealthKit.

Haga lo siguiente:

1. Haga doble clic en el archivo `Entitlements.plist` en el **Explorador de soluciones** para abrirlo para su edición.
2. Desplácese hasta la parte inferior y active la casilla **Habilitar HealthKit**: 

    [![](workout-apps-images/auth01.png "Active la casilla habilitar HealthKit")](workout-apps-images/auth01.png#lightbox)
3. Guarde los cambios en el archivo.
4. Siga las instrucciones del perfil de aprovisionamiento y el [ID. de aplicación explícitos](~/ios/platform/healthkit.md) , y [asocie el identificador de la aplicación y el perfil de aprovisionamiento con las secciones de la aplicación de Xamarin. iOS](~/ios/platform/healthkit.md) del artículo de [Introducción a HealthKit](~/ios/platform/healthkit.md) para aprovisionar correctamente la aplicación.
5. Por último, siga las instrucciones del artículo sobre [programación de mantenimiento](~/ios/platform/healthkit.md) y [solicitud de permiso de las](~/ios/platform/healthkit.md) secciones de usuario del artículo [Introducción a HealthKit](~/ios/platform/healthkit.md) para solicitar autorización para acceder al almacén de información de HealthKit del usuario.

### <a name="setting-the-workout-configuration"></a>Establecimiento de la configuración del entrenamiento

Las sesiones de entrenamiento se crean mediante un objeto de`HKWorkoutConfiguration`configuración de entrenamiento () que especifica el tipo `HKWorkoutActivityType.Running`de entrenamiento (como) y la ubicación `HKWorkoutSessionLocationType.Outdoor`de entrenamiento (como):

```csharp
using HealthKit;
...

// Create a workout configuration
var configuration = new HKWorkoutConfiguration () {
    ActivityType = HKWorkoutActivityType.Running,
    LocationType = HKWorkoutSessionLocationType.Outdoor
};
```

### <a name="creating-a-workout-session-delegate"></a>Crear un delegado de sesión de entrenamiento 

Para controlar los eventos que se pueden producir durante una sesión de entrenamiento, la aplicación necesitará crear una instancia de delegado de sesión de entrenamiento. Agregue una nueva clase al proyecto y base fuera de la `HKWorkoutSessionDelegate` clase. En el ejemplo de una ejecución exterior, podría ser similar al siguiente:

```csharp
using System;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
    public class OutdoorRunDelegate : HKWorkoutSessionDelegate
    {
        #region Computed Properties
        public HKHealthStore HealthStore { get; private set; }
        public HKWorkoutSession WorkoutSession { get; private set;}
        #endregion

        #region Constructors
        public OutdoorRunDelegate (HKHealthStore healthStore, HKWorkoutSession workoutSession)
        {
            // Initialize
            this.HealthStore = healthStore;
            this.WorkoutSession = workoutSession;

            // Attach this delegate to the session
            workoutSession.Delegate = this;
        }
        #endregion

        #region Override Methods
        public override void DidFail (HKWorkoutSession workoutSession, NSError error)
        {
            // Handle workout session failing
            RaiseFailed ();
        }

        public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
        {
            // Take action based on the change in state
            switch (toState) {
            case HKWorkoutSessionState.NotStarted:
                break;
            case HKWorkoutSessionState.Paused:
                RaisePaused ();
                break;
            case HKWorkoutSessionState.Running:
                RaiseRunning ();
                break;
            case HKWorkoutSessionState.Ended:
                RaiseEnded ();
                break;
            }

        }

        public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
        {
            base.DidGenerateEvent (workoutSession, @event);
        }
        #endregion

        #region Events
        public delegate void OutdoorRunEventDelegate ();

        public event OutdoorRunEventDelegate Failed;
        internal void RaiseFailed ()
        {
            if (this.Failed != null) this.Failed ();
        }


        public event OutdoorRunEventDelegate Paused;
        internal void RaisePaused ()
        {
            if (this.Paused != null) this.Paused ();
        }

        public event OutdoorRunEventDelegate Running;
        internal void RaiseRunning ()
        {
            if (this.Running != null) this.Running ();
        }

        public event OutdoorRunEventDelegate Ended;
        internal void RaiseEnded ()
        {
            if (this.Ended != null) this.Ended ();
        }
        #endregion
    }
}
```

Esta clase crea varios eventos que se generarán cuando el estado de la sesión de entrenamiento cambie`DidChangeToState`() y si se produce un error`DidFail`en la sesión de entrenamiento (). 

### <a name="creating-a-workout-session"></a>Crear una sesión de entrenamiento

Con el delegado de la sesión de entrenamiento y la configuración de entrenamiento creados anteriormente para crear una nueva sesión de entrenamiento e iniciarla en el almacén de HealthKit predeterminado del usuario:

```csharp
using HealthKit;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public OutdoorRunDelegate RunDelegate { get; set; }
#endregion
...

private void StartOutdoorRun ()
{
    // Create a workout configuration
    var configuration = new HKWorkoutConfiguration () {
        ActivityType = HKWorkoutActivityType.Running,
        LocationType = HKWorkoutSessionLocationType.Outdoor
    };

    // Create workout session
    // Start workout session
    NSError error = null;
    var workoutSession = new HKWorkoutSession (configuration, out error);

    // Successful?
    if (error != null) {
        // Report error to user and return
        return;
    }

    // Create workout session delegate and wire-up events
    RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

    RunDelegate.Failed += () => {
        // Handle the session failing
    };

    RunDelegate.Paused += () => {
        // Handle the session being paused
    };

    RunDelegate.Running += () => {
        // Handle the session running
    };

    RunDelegate.Ended += () => {
        // Handle the session ending
    };

    // Start session
    HealthStore.StartWorkoutSession (workoutSession);
}
```

Si la aplicación inicia esta sesión de entrenamiento y el usuario vuelve a la pantalla de inspección, se mostrará un icono de "hombre en ejecución" de color verde sobre la esfera:

[![](workout-apps-images/workout03.png "Un pequeño icono de hombre en color verde que se muestra sobre la superficie")](workout-apps-images/workout03.png#lightbox)

Si el usuario pulsa este icono, se le devolverá a la aplicación.

## <a name="data-collection-and-control"></a>Recopilación y control de datos

Una vez que se ha configurado e iniciado una sesión de entrenamiento, la aplicación necesitará recopilar datos sobre la sesión (por ejemplo, la tasa de corazón del usuario) y controlar el estado de la sesión:

[![](workout-apps-images/workout04.png "Recopilación de datos y diagrama de control")](workout-apps-images/workout04.png#lightbox)

1. **Observación de ejemplos** : la aplicación tendrá que recuperar información de HealthKit que actuará y se mostrará al usuario.
2. **Observar eventos** : la aplicación tendrá que responder a los eventos generados por HealthKit o desde la interfaz de usuario de la aplicación (por ejemplo, el usuario pausa el entrenamiento).
3. **Entrar en estado de ejecución** : la sesión se ha iniciado y se está ejecutando actualmente.
4. **Entrar en pausa estado** : el usuario ha pausado la sesión de entrenamiento actual y puede reiniciarla posteriormente. El usuario puede cambiar entre los Estados en ejecución y en pausa varias veces en una sola sesión de entrenamiento.
5. **Finalizar sesión de entrenamiento** : en cualquier momento el usuario puede finalizar la sesión de entrenamiento o puede expirar y finalizar por sí solo si se trata de un entrenamiento de uso medido (por ejemplo, una ejecución de dos kilómetros).

El paso final consiste en guardar los resultados de la sesión de entrenamiento en el almacén de HealthKit del usuario.

### <a name="observing-healthkit-samples"></a>Observar ejemplos de HealthKit

La aplicación tendrá que abrir una _consulta de objeto_ delimitador para cada uno de los puntos de datos de HealthKit que le interesen, como la tasa de corazón o la energía activa grabada. Para cada punto de datos que se está observando, será necesario crear un controlador de actualización para capturar los datos nuevos a medida que se envían a la aplicación.

Desde estos puntos de datos, la aplicación puede acumular totales (como la distancia total de ejecución) y actualizar su interfaz de usuario según sea necesario. Además, la aplicación puede notificar a los usuarios cuando han alcanzado un objetivo o logro específico, como completar el siguiente kilómetro de una ejecución.

Eche un vistazo al siguiente código de ejemplo:

```csharp
private void ObserveHealthKitSamples ()
{
    // Get the starting date of the required samples
    var datePredicate = HKQuery.GetPredicateForSamples (WorkoutSession.StartDate, null, HKQueryOptions.StrictStartDate);

    // Get data from the local device
    var devices = new NSSet<HKDevice> (new HKDevice [] { HKDevice.LocalDevice });
    var devicePredicate = HKQuery.GetPredicateForObjectsFromDevices (devices);

    // Assemble compound predicate
    var queryPredicate = NSCompoundPredicate.CreateAndPredicate (new NSPredicate [] { datePredicate, devicePredicate });

    // Get ActiveEnergyBurned
    var queryActiveEnergyBurned = new HKAnchoredObjectQuery (HKQuantityType.Create (HKQuantityTypeIdentifier.ActiveEnergyBurned), queryPredicate, null, HKSampleQuery.NoLimit, (query, addedObjects, deletedObjects, newAnchor, error) => {
        // Valid?
        if (error == null) {
            // Yes, process all returned samples
            foreach (HKSample sample in addedObjects) {
                var quantitySample = sample as HKQuantitySample;
                ActiveEnergyBurned += quantitySample.Quantity.GetDoubleValue (HKUnit.Joule);
            }
            
            // Update User Interface
            ...
        }
    });

    // Start Query
    HealthStore.ExecuteQuery (queryActiveEnergyBurned);
                                          
}
```

Crea un predicado para establecer la fecha de inicio para la que desea obtener datos mediante el `GetPredicateForSamples` método. Crea un conjunto de dispositivos del que se va a extraer información de `GetPredicateForObjectsFromDevices` HealthKit mediante el método, en este caso solo el`HKDevice.LocalDevice`Apple Watch local (). Los dos predicados se combinan en un predicado compuesto`NSCompoundPredicate`() mediante `CreateAndPredicate` el método.

Se crea `HKAnchoredObjectQuery` un nuevo para el punto de datos deseado (en este `HKQuantityTypeIdentifier.ActiveEnergyBurned` caso, para el punto de datos activo de energía grabado), no se impone ningún límite en la`HKSampleQuery.NoLimit`cantidad de datos devueltos () y se define un controlador de actualización para controlar los datos que se devuelven a la aplicación. desde HealthKit. 

Se llamará al controlador de actualización cada vez que se entreguen nuevos datos a la aplicación para el punto de datos determinado. Si no se devuelve ningún error, la aplicación puede leer los datos de forma segura, realizar los cálculos necesarios y actualizar su interfaz de usuario según sea necesario.

El código recorre en bucle todos los ejemplos (`HKSample`) devueltos `addedObjects` en la matriz y los convierte en una muestra de`HKQuantitySample`cantidad (). A continuación, obtiene el valor Double del ejemplo como Joule ((`HKUnit.Joule`) y lo acumula en el total acumulado de energía activa grabado para el entrenamiento y actualiza la interfaz de usuario.

### <a name="achieved-goal-notification"></a>Notificación de objetivo logrado

Como se mencionó anteriormente, cuando el usuario logra un objetivo en la aplicación de entrenamiento (como completar el primer kilómetro de una ejecución), puede enviar comentarios hápticos al usuario a través del Taptic Engine. La aplicación también debe actualizar su interfaz de usuario en este momento, ya que es más probable que el usuario eleve su muñeca para ver el evento que le ha solicitado los comentarios.

Para reproducir los comentarios hápticos, use el código siguiente:

```csharp
// Play haptic feedback
WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);
```

### <a name="observing-events"></a>Observar eventos

Los eventos son marcas de tiempo que la aplicación puede usar para resaltar determinados puntos durante el entrenamiento del usuario. Algunos eventos se crearán directamente por la aplicación y se guardarán en el entrenamiento y los HealthKit crearán automáticamente algunos eventos.

Para observar los eventos creados por HealthKit, la aplicación invalidará el `DidGenerateEvent` método `HKWorkoutSessionDelegate`del:

```csharp
using System.Collections.Generic;
...

public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
    base.DidGenerateEvent (workoutSession, @event);
    
    // Save HealthKit generated event
    WorkoutEvents.Add (@event);
    
    // Take action based on the type of event
    switch (@event.Type) {
    case HKWorkoutEventType.Lap:
        break;
    case HKWorkoutEventType.Marker:
        break;
    case HKWorkoutEventType.MotionPaused:
        break;
    case HKWorkoutEventType.MotionResumed:
        break;
    case HKWorkoutEventType.Pause:
        break;
    case HKWorkoutEventType.Resume:
        break;
    }
}
```

Apple ha agregado los siguientes tipos de eventos nuevos en watchos 3:

- `HKWorkoutEventType.Lap`-Son para eventos que interrumpen el entrenamiento en partes de la misma distancia. Por ejemplo, para marcar una vuelta alrededor de una pista mientras se ejecuta.
- `HKWorkoutEventType.Marker`-Se trata de puntos arbitrarios de interés dentro del entrenamiento. Por ejemplo, alcanzar un punto concreto en la ruta de una ejecución exterior.

Estos nuevos tipos pueden ser creados por la aplicación y almacenados en el entrenamiento para su uso posterior en la creación de gráficos y estadísticas.

Para crear un evento de marcador, haga lo siguiente:

```csharp
using System.Collections.Generic;
...

public float MilesRun { get; set; }
public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

public void ReachedNextMile ()
{
    // Create and save marker event
    var markerEvent = HKWorkoutEvent.Create (HKWorkoutEventType.Marker, NSDate.Now);
    WorkoutEvents.Add (markerEvent);

    // Notify user
    NotifyUserOfReachedMileGoal (++MilesRun);
}
```

Este código crea una nueva instancia de un evento de marcador`HKWorkoutEvent`() y la guarda en una colección privada de eventos (que se escribirá posteriormente en la sesión de entrenamiento) y notifica al usuario del evento a través de hápticos.

### <a name="pausing-and-resuming-workouts"></a>Pausar y reanudar los ejercicios

En cualquier momento de una sesión de entrenamiento, el usuario puede pausar temporalmente el entrenamiento y reanudarlo más adelante. Por ejemplo, podrían pausar una ejecución interior para realizar una llamada importante y reanudar la ejecución después de que la llamada se haya completado.

La interfaz de usuario de la aplicación debe proporcionar una manera de pausar y reanudar el entrenamiento (llamando a HealthKit) para que el Apple Watch pueda conservar el espacio de datos y la energía mientras el usuario ha suspendido su actividad. Además, la aplicación debe omitir todos los puntos de datos nuevos que se puedan recibir cuando la sesión de entrenamiento esté en un estado de pausa.

HealthKit responderá a las llamadas pausar y reanudar mediante la generación de eventos de pausa y reanudación. Mientras se pausa la sesión de entrenamiento, los nuevos eventos o datos se enviarán a la aplicación HealthKit hasta que se reanude la sesión.

Use el código siguiente para pausar y reanudar una sesión de entrenamiento:

```csharp
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public HKWorkoutSession WorkoutSession { get; set;}
...

public void PauseWorkout ()
{
    // Pause the current workout
    HealthStore.PauseWorkoutSession (WorkoutSession);
}

public void ResumeWorkout ()
{
    // Pause the current workout
    HealthStore.ResumeWorkoutSession (WorkoutSession);
}
```

Los eventos pausar y reanudar que se generarán a partir de HealthKit se pueden controlar `DidGenerateEvent` invalidando `HKWorkoutSessionDelegate`el método de:

```csharp
public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
    base.DidGenerateEvent (workoutSession, @event);

    // Take action based on the type of event
    switch (@event.Type) {
    case HKWorkoutEventType.Pause:
        break;
    case HKWorkoutEventType.Resume:
        break;
    }
}
```

### <a name="motion-events"></a>Eventos de movimiento

También novedad de watchos 3, son los eventos de movimiento en`HKWorkoutEventType.MotionPaused`pausa () y de reanudación (`HKWorkoutEventType.MotionResumed`). HealthKit genera estos eventos automáticamente durante un entrenamiento en ejecución cuando el usuario inicia y deja de moverse.

Cuando la aplicación recibe un evento de movimiento en pausa, debe dejar de recopilar datos hasta que el usuario reanude el movimiento y se reciba el evento de reanudación de movimiento. La aplicación no debe pausar la sesión de entrenamiento en respuesta a un evento de movimiento en pausa.

> [!IMPORTANT]
> Los eventos movimiento en pausa y reanudación de movimiento solo se admiten para el tipo`HKWorkoutActivityType.Running`de actividad RunningWorkout ().

De nuevo, estos eventos se pueden controlar invalidando `DidGenerateEvent` el método de: `HKWorkoutSessionDelegate`

```csharp
public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
    base.DidGenerateEvent (workoutSession, @event);
    
    // Take action based on the type of event
    switch (@event.Type) {
    case HKWorkoutEventType.MotionPaused:
        break;
    case HKWorkoutEventType.MotionResumed:
        break;
    }
}

```

## <a name="ending-and-saving-the-workout-session"></a>Finalizar y guardar la sesión de entrenamiento

Cuando el usuario haya completado el entrenamiento, la aplicación deberá finalizar la sesión actual de entrenamiento y guardarla en la base de datos HealthKit. Los entrenamientos guardados en HealthKit se mostrarán automáticamente en la lista actividad de entrenamiento.

Como novedad de iOS 10, esto incluye también la lista de la lista de actividades de entrenamiento en el iPhone del usuario. Por tanto, incluso si el Apple Watch no está cerca, el entrenamiento se presentará en el teléfono.

Los entrenamientos que incluyen ejemplos energéticos actualizarán el anillo de movimiento del usuario en la aplicación de actividades para que las aplicaciones de terceros puedan contribuir ahora a los objetivos de movimiento diarios del usuario.

Los pasos siguientes son necesarios para finalizar y guardar una sesión de entrenamiento:

[![](workout-apps-images/workout05.png "Finalización y guardado del diagrama de sesiones de entrenamiento")](workout-apps-images/workout05.png#lightbox)

1. En primer lugar, la aplicación deberá finalizar la sesión de entrenamiento.
2. La sesión de entrenamiento se guarda en HealthKit.
3. Agregue cualquier ejemplo (por ejemplo, una distancia o un quemado de energía) a la sesión de entrenamiento guardada.

### <a name="ending-the-session"></a>Finalización de la sesión

Para finalizar la sesión de entrenamiento, llame `EndWorkoutSession` al método del `HKHealthStore` que pasa `HKWorkoutSession`:

```csharp
public HKHealthStore HealthStore { get; private set; }
public HKWorkoutSession WorkoutSession { get; private set;}
...

public void EndOutdoorRun ()
{
    // End the current workout session
    HealthStore.EndWorkoutSession (WorkoutSession);
}
```

Se restablecerán los sensores de dispositivos a su modo normal. Cuando HealthKit termine de finalizar el entrenamiento, recibirá una devolución `DidChangeToState` `HKWorkoutSessionDelegate`de llamada al método del:

```csharp
public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
{
    // Take action based on the change in state
    switch (toState) {
    ...
    case HKWorkoutSessionState.Ended:
        StopObservingHealthKitSamples ();
        RaiseEnded ();
        break;
    }

}
```

### <a name="saving-the-session"></a>Guardar la sesión

Una vez que la aplicación haya finalizado la sesión de entrenamiento, deberá crear un entrenamiento`HKWorkout`() y guardarla (junto con un evento) en el almacén de datos`HKHealthStore`de HealthKit ():

```csharp
public HKHealthStore HealthStore { get; private set; }
public HKWorkoutSession WorkoutSession { get; private set;}
public float MilesRun { get; set; }
public double ActiveEnergyBurned { get; set;}
public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

private void SaveWorkoutSession ()
{
    // Build required workout quantities 
    var energyBurned = HKQuantity.FromQuantity (HKUnit.Joule, ActiveEnergyBurned);
    var distance = HKQuantity.FromQuantity (HKUnit.Mile, MilesRun);

    // Create any required metadata
    var metadata = new NSMutableDictionary ();
    metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));

    // Create workout
    var workout = HKWorkout.Create (HKWorkoutActivityType.Running, 
                                    WorkoutSession.StartDate, 
                                    NSDate.Now, 
                                    WorkoutEvents.ToArray (), 
                                    energyBurned, 
                                    distance, 
                                    metadata);

    // Save to HealthKit
    HealthStore.SaveObject (workout, (successful, error) => {
        // Handle any errors
        if (error == null) {
            // Was the save successful
            if (successful) {

            }
        } else {
            // Report error
        }
    });

}
```

Este código crea la cantidad total de energía que se ha grabado y la distancia del `HKQuantity` entrenamiento como objetos. Se crea un diccionario de metadatos que define el entrenamiento y se especifica la ubicación del entrenamiento:

```csharp
metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));
```

Se crea `HKWorkout` un nuevo objeto con el mismo `HKWorkoutActivityType` que el `HKWorkoutSession`, las fechas de inicio y finalización, la lista de eventos (que se acumulan de las secciones anteriores), la energía quemada, la distancia total y el Diccionario de metadatos. Este objeto se guarda en el almacén de estado y se controlan los errores.  

### <a name="adding-samples"></a>Agregar ejemplos

Cuando la aplicación guarda un conjunto de ejemplos en un entrenamiento, HealthKit genera una conexión entre los ejemplos y el propio entrenamiento para que la aplicación pueda consultar HealthKit en una fecha posterior para todas las muestras asociadas a un entrenamiento determinado. Con esta información, la aplicación puede generar gráficos a partir de los datos de entrenamiento y trazarlos en una escala de tiempo de entrenamiento.

Para que una aplicación contribuya al anillo de movimiento de la aplicación de actividad, debe incluir ejemplos energéticos con el entrenamiento guardado. Además, los totales de distancia y energía deben coincidir con la suma de los ejemplos que la aplicación asocia a un entrenamiento guardado.

Para agregar ejemplos a un entrenamiento guardado, haga lo siguiente:

```csharp
using System.Collections.Generic;
using WatchKit;
using HealthKit;
...

public HKHealthStore HealthStore { get; private set; }
public List<HKSample> WorkoutSamples { get; set; } = new List<HKSample> ();
...


private void SaveWorkoutSamples (HKWorkout workout)
{
    // Add samples to saved workout
    HealthStore.AddSamples (WorkoutSamples.ToArray (), workout, (success, error) => {
        // Handle any errors
        if (error == null) {
            // Was the save successful
            if (success) {

            }
        } else {
            // Report error
        }
    });
}
```

Opcionalmente, la aplicación puede calcular y crear un subconjunto más pequeño de muestras o un ejemplo de megapíxeles (que abarca todo el intervalo del entrenamiento) que, a continuación, se asocia con el entrenamiento guardado.

## <a name="workouts-and-ios-10"></a>Entrenamientos e iOS 10

Todas las aplicaciones de entrenamiento watchOS 3 tiene una aplicación de entrenamiento en función de iOS 10 primario y, lo que es nuevo en iOS 10, esta aplicación de iOS se puede usar para iniciar un entrenamiento que se coloque el Apple Watch en el modo de entrenamiento (sin intervención del usuario) y ejecutar la aplicación watchOS en el modo de ejecución de fondo (consulte [sobre fondo ejecutando](#about-background-running) anteriormente para obtener más detalles).

Mientras se ejecuta la aplicación watchos, puede usar WatchConnectivity para la mensajería y la comunicación con la aplicación de iOS primaria.

Eche un vistazo a cómo funciona este proceso:

[![](workout-apps-images/workout06.png "diagrama de comunicación de iPhone y Apple Watch")](workout-apps-images/workout06.png#lightbox)

1. La aplicación de iPhone crea `HKWorkoutConfiguration` un objeto y establece el tipo y la ubicación de entrenamiento.
2. El `HKWorkoutConfiguration` objeto se envía a la versión Apple Watch de la aplicación y, si aún no se está ejecutando, lo inicia el sistema.
3. Con la configuración de entrenamiento pasada, la aplicación watchos 3 inicia una nueva sesión de entrenamiento`HKWorkoutSession`().

> [!IMPORTANT]
> Para que la aplicación de iPhone primaria inicie un entrenamiento en el Apple Watch, la aplicación watchos 3 debe tener habilitada la ejecución en segundo plano. Para más información, consulte habilitación del [fondo que se ejecuta](#enabling-background-running) anteriormente.

Este proceso es muy similar al proceso de inicio de una sesión de entrenamiento en la aplicación watchos 3 directamente. En el iPhone, use el siguiente código:

```csharp
using System;
using HealthKit;
using WatchConnectivity;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set; } = new HKHealthStore ();
public WCSession ConnectivitySession { get; set; } = WCSession.DefaultSession;
#endregion
...

private void StartOutdoorRun ()
{
    // Can the app communicate with the watchOS version of the app?
    if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
        // Create a workout configuration
        var configuration = new HKWorkoutConfiguration () {
            ActivityType = HKWorkoutActivityType.Running,
            LocationType = HKWorkoutSessionLocationType.Outdoor
        };

        // Start watch app
        HealthStore.StartWatchApp (configuration, (success, error) => {
            // Handle any errors
            if (error == null) {
                // Was the save successful
                if (success) {
                    ...
                }
            } else {
                // Report error
                ...
            }
        });
    }
}
```

Este código garantiza que la versión de watchos de la aplicación está instalada y que la versión de iPhone puede conectarse primero a ella:

```csharp
if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
    ...
}
```

A continuación, crea `HKWorkoutConfiguration` un como de costumbre y `StartWatchApp` usa el método `HKHealthStore` de para enviarlo al Apple Watch e iniciar la aplicación y la sesión de entrenamiento.

Y en la aplicación de inspección de SO, use el siguiente código `WKExtensionDelegate`en:

```csharp
using WatchKit;
using HealthKit;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public OutdoorRunDelegate RunDelegate { get; set; }
#endregion
...


public override void HandleWorkoutConfiguration (HKWorkoutConfiguration workoutConfiguration)
{
    // Create workout session
    // Start workout session
    NSError error = null;
    var workoutSession = new HKWorkoutSession (workoutConfiguration, out error);

    // Successful?
    if (error != null) {
        // Report error to user and return
        return;
    }

    // Create workout session delegate and wire-up events
    RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

    RunDelegate.Failed += () => {
        // Handle the session failing
    };

    RunDelegate.Paused += () => {
        // Handle the session being paused
    };

    RunDelegate.Running += () => {
        // Handle the session running
    };

    RunDelegate.Ended += () => {
        // Handle the session ending
    };

    // Start session
    HealthStore.StartWorkoutSession (workoutSession);
}
```

Toma `HKWorkoutConfiguration` y crea un nuevo `HKWorkoutSession` y adjunta una instancia del personalizado `HKWorkoutSessionDelegate`. La sesión de entrenamiento se inicia en el almacén de estado de HealthKit del usuario.

## <a name="bringing-all-the-pieces-together"></a>Reunir todas las piezas

Tomando toda la información que se presenta en este documento, una aplicación de entrenamiento basada en watchos 3 y su aplicación de entrenamiento principal basada en iOS 10 podría incluir las siguientes partes:

1. **iOS 10 `ViewController.cs`**  : controla el inicio de una sesión de conectividad de inspección y un entrenamiento en el Apple Watch.
2. **watchos 3 `ExtensionDelegate.cs`**  : controla la versión de watchos 3 de la aplicación de entrenamiento.
3. **watchos 3 `OutdoorRunDelegate.cs`**  : un personalizado `HKWorkoutSessionDelegate` para controlar los eventos del entrenamiento.

> [!IMPORTANT]
> El código que se muestra en las siguientes secciones solo incluye los elementos necesarios para implementar las nuevas características mejoradas que se proporcionan para las aplicaciones de entrenamiento en watchos 3. No se incluye todo el código auxiliar y el código para presentar y actualizar la interfaz de usuario, pero se puede crear fácilmente siguiendo nuestra documentación de watchos.<p/>



### <a name="viewcontrollercs"></a>ViewController.cs

El `ViewController.cs` archivo de la versión primaria de iOS 10 de la aplicación de entrenamiento incluiría el código siguiente:

```csharp
using System;
using HealthKit;
using UIKit;
using WatchConnectivity;

namespace MonkeyWorkout
{
    public partial class ViewController : UIViewController
    {
        #region Computed Properties
        public HKHealthStore HealthStore { get; set; } = new HKHealthStore ();
        public WCSession ConnectivitySession { get; set; } = WCSession.DefaultSession;
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Private Methods
        private void InitializeWatchConnectivity ()
        {
            // Is Watch Connectivity supported?
            if (!WCSession.IsSupported) {
                // No, abort
                return;
            }

            // Is the session already active?
            if (ConnectivitySession.ActivationState != WCSessionActivationState.Activated) {
                // No, start session
                ConnectivitySession.ActivateSession ();
            }
        }

        private void StartOutdoorRun ()
        {
            // Can the app communicate with the watchOS version of the app?
            if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
                // Create a workout configuration
                var configuration = new HKWorkoutConfiguration () {
                    ActivityType = HKWorkoutActivityType.Running,
                    LocationType = HKWorkoutSessionLocationType.Outdoor
                };

                // Start watch app
                HealthStore.StartWatchApp (configuration, (success, error) => {
                    // Handle any errors
                    if (error == null) {
                        // Was the save successful
                        if (success) {
                            ...
                        }
                    } else {
                        // Report error
                        ...
                    }
                });
            }
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Start Watch Connectivity
            InitializeWatchConnectivity ();
        }
        #endregion
    }
}
```

### <a name="extensiondelegatecs"></a>ExtensionDelegate.cs

El `ExtensionDelegate.cs` archivo en la versión de watchos 3 de la aplicación de entrenamiento incluiría el código siguiente:

```csharp
using System;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
    public class ExtensionDelegate : WKExtensionDelegate
    {
        #region Computed Properties
        public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
        public OutdoorRunDelegate RunDelegate { get; set; }
        #endregion

        #region Constructors
        public ExtensionDelegate ()
        {
            
        }
        #endregion

        #region Private Methods
        private void StartWorkoutSession (HKWorkoutConfiguration workoutConfiguration)
        {
            // Create workout session
            // Start workout session
            NSError error = null;
            var workoutSession = new HKWorkoutSession (workoutConfiguration, out error);

            // Successful?
            if (error != null) {
                // Report error to user and return
                return;
            }

            // Create workout session delegate and wire-up events
            RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

            RunDelegate.Failed += () => {
                // Handle the session failing
                ...
            };

            RunDelegate.Paused += () => {
                // Handle the session being paused
                ...
            };

            RunDelegate.Running += () => {
                // Handle the session running
                ...
            };

            RunDelegate.Ended += () => {
                // Handle the session ending
                ...
            };
            
            RunDelegate.ReachedMileGoal += (miles) => {
                // Handle the reaching a session goal
                ...
            };

            RunDelegate.HealthKitSamplesUpdated += () => {
                // Update UI as required
                ...
            };

            // Start session
            HealthStore.StartWorkoutSession (workoutSession);
        }

        private void StartOutdoorRun ()
        {
            // Create a workout configuration
            var workoutConfiguration = new HKWorkoutConfiguration () {
                ActivityType = HKWorkoutActivityType.Running,
                LocationType = HKWorkoutSessionLocationType.Outdoor
            };

            // Start the session
            StartWorkoutSession (workoutConfiguration);
        }
        #endregion

        #region Override Methods
        public override void HandleWorkoutConfiguration (HKWorkoutConfiguration workoutConfiguration)
        {
            // Start the session
            StartWorkoutSession (workoutConfiguration);
        }
        #endregion
    }
}
```

### <a name="outdoorrundelegatecs"></a>OutdoorRunDelegate.cs

El `OutdoorRunDelegate.cs` archivo en la versión de watchos 3 de la aplicación de entrenamiento incluiría el código siguiente:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
    public class OutdoorRunDelegate : HKWorkoutSessionDelegate
    {
        #region Private Variables
        private HKAnchoredObjectQuery QueryActiveEnergyBurned;
        #endregion

        #region Computed Properties
        public HKHealthStore HealthStore { get; private set; }
        public HKWorkoutSession WorkoutSession { get; private set;}
        public float MilesRun { get; set; }
        public double ActiveEnergyBurned { get; set;}
        public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
        public List<HKSample> WorkoutSamples { get; set; } = new List<HKSample> ();
        #endregion

        #region Constructors
        public OutdoorRunDelegate (HKHealthStore healthStore, HKWorkoutSession workoutSession)
        {
            // Initialize
            this.HealthStore = healthStore;
            this.WorkoutSession = workoutSession;

            // Attach this delegate to the session
            workoutSession.Delegate = this;

        }
        #endregion

        #region Private Methods
        private void ObserveHealthKitSamples ()
        {
            // Get the starting date of the required samples
            var datePredicate = HKQuery.GetPredicateForSamples (WorkoutSession.StartDate, null, HKQueryOptions.StrictStartDate);

            // Get data from the local device
            var devices = new NSSet<HKDevice> (new HKDevice [] { HKDevice.LocalDevice });
            var devicePredicate = HKQuery.GetPredicateForObjectsFromDevices (devices);

            // Assemble compound predicate
            var queryPredicate = NSCompoundPredicate.CreateAndPredicate (new NSPredicate [] { datePredicate, devicePredicate });

            // Get ActiveEnergyBurned
            QueryActiveEnergyBurned = new HKAnchoredObjectQuery (HKQuantityType.Create (HKQuantityTypeIdentifier.ActiveEnergyBurned), queryPredicate, null, HKSampleQuery.NoLimit, (query, addedObjects, deletedObjects, newAnchor, error) => {
                // Valid?
                if (error == null) {
                    // Yes, process all returned samples
                    foreach (HKSample sample in addedObjects) {
                        // Accumulate totals
                        var quantitySample = sample as HKQuantitySample;
                        ActiveEnergyBurned += quantitySample.Quantity.GetDoubleValue (HKUnit.Joule);

                        // Save samples
                        WorkoutSamples.Add (sample);
                    }

                    // Inform caller
                    RaiseHealthKitSamplesUpdated ();
                }
            });

            // Start Query
            HealthStore.ExecuteQuery (QueryActiveEnergyBurned);
                                                  
        }

        private void StopObservingHealthKitSamples ()
        {
            // Stop query
            HealthStore.StopQuery (QueryActiveEnergyBurned);
        }

        private void ResumeObservingHealthkitSamples ()
        {
            // Resume current queries 
            HealthStore.ExecuteQuery (QueryActiveEnergyBurned);
        }

        private void NotifyUserOfReachedMileGoal (float miles)
        {
            // Play haptic feedback
            WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);

            // Raise event
            RaiseReachedMileGoal (miles);
        }

        private void SaveWorkoutSession ()
        {
            // Build required workout quantities
            var energyBurned = HKQuantity.FromQuantity (HKUnit.Joule, ActiveEnergyBurned);
            var distance = HKQuantity.FromQuantity (HKUnit.Mile, MilesRun);

            // Create any required metadata
            var metadata = new NSMutableDictionary ();
            metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));

            // Create workout
            var workout = HKWorkout.Create (HKWorkoutActivityType.Running, 
                                            WorkoutSession.StartDate, 
                                            NSDate.Now, 
                                            WorkoutEvents.ToArray (), 
                                            energyBurned, 
                                            distance, 
                                            metadata);

            // Save to HealthKit
            HealthStore.SaveObject (workout, (successful, error) => {
                // Handle any errors
                if (error == null) {
                    // Was the save successful
                    if (successful) {
                        // Add samples to workout
                        SaveWorkoutSamples (workout);
                    }
                } else {
                    // Report error
                    ...
                }
            });

        }

        private void SaveWorkoutSamples (HKWorkout workout)
        {
            // Add samples to saved workout
            HealthStore.AddSamples (WorkoutSamples.ToArray (), workout, (success, error) => {
                // Handle any errors
                if (error == null) {
                    // Was the save successful
                    if (success) {
                        ...
                    }
                } else {
                    // Report error
                    ...
                }
            });
        }
        #endregion

        #region Public Methods
        public void PauseWorkout ()
        {
            // Pause the current workout
            HealthStore.PauseWorkoutSession (WorkoutSession);
        }

        public void ResumeWorkout ()
        {
            // Pause the current workout
            HealthStore.ResumeWorkoutSession (WorkoutSession);
        }

        public void ReachedNextMile ()
        {
            // Create and save marker event
            var markerEvent = HKWorkoutEvent.Create (HKWorkoutEventType.Marker, NSDate.Now);
            WorkoutEvents.Add (markerEvent);

            // Notify user
            NotifyUserOfReachedMileGoal (++MilesRun);
        }

        public void EndOutdoorRun ()
        {
            // End the current workout session
            HealthStore.EndWorkoutSession (WorkoutSession);
        }
        #endregion

        #region Override Methods
        public override void DidFail (HKWorkoutSession workoutSession, NSError error)
        {
            // Handle workout session failing
            RaiseFailed ();
        }

        public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
        {
            // Take action based on the change in state
            switch (toState) {
            case HKWorkoutSessionState.NotStarted:
                break;
            case HKWorkoutSessionState.Paused:
                StopObservingHealthKitSamples ();
                RaisePaused ();
                break;
            case HKWorkoutSessionState.Running:
                if (fromState == HKWorkoutSessionState.Paused) {
                    ResumeObservingHealthkitSamples ();
                } else {
                    ObserveHealthKitSamples ();
                }
                RaiseRunning ();
                break;
            case HKWorkoutSessionState.Ended:
                StopObservingHealthKitSamples ();
                SaveWorkoutSession ();
                RaiseEnded ();
                break;
            }

        }

        public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
        {
            base.DidGenerateEvent (workoutSession, @event);

            // Save HealthKit generated event
            WorkoutEvents.Add (@event);

            // Take action based on the type of event
            switch (@event.Type) {
            case HKWorkoutEventType.Lap:
                ...
                break;
            case HKWorkoutEventType.Marker:
                ...
                break;
            case HKWorkoutEventType.MotionPaused:
                ...
                break;
            case HKWorkoutEventType.MotionResumed:
                ...
                break;
            case HKWorkoutEventType.Pause:
                ...
                break;
            case HKWorkoutEventType.Resume:
                ...
                break;
            }
        }
        #endregion

        #region Events
        public delegate void OutdoorRunEventDelegate ();
        public delegate void OutdoorRunMileGoalDelegate (float miles);

        public event OutdoorRunEventDelegate Failed;
        internal void RaiseFailed ()
        {
            if (this.Failed != null) this.Failed ();
        }


        public event OutdoorRunEventDelegate Paused;
        internal void RaisePaused ()
        {
            if (this.Paused != null) this.Paused ();
        }

        public event OutdoorRunEventDelegate Running;
        internal void RaiseRunning ()
        {
            if (this.Running != null) this.Running ();
        }

        public event OutdoorRunEventDelegate Ended;
        internal void RaiseEnded ()
        {
            if (this.Ended != null) this.Ended ();
        }

        public event OutdoorRunMileGoalDelegate ReachedMileGoal;
        internal void RaiseReachedMileGoal (float miles)
        {
            if (this.ReachedMileGoal != null) this.ReachedMileGoal (miles);
        }

        public event OutdoorRunEventDelegate HealthKitSamplesUpdated;
        internal void RaiseHealthKitSamplesUpdated ()
        {
            if (this.HealthKitSamplesUpdated != null) this.HealthKitSamplesUpdated ();
        }
        #endregion
    }
}
```

## <a name="best-practices"></a>Procedimientos recomendados

Apple sugiere el uso de las siguientes prácticas recomendadas al diseñar e implementar aplicaciones de entrenamiento en watchos 3 y iOS 10:

- Asegúrese de que la aplicación de entrenamiento de watchos 3 sigue siendo funcional incluso cuando no se puede conectar al iPhone y a la versión de iOS 10 de la aplicación.
- Use la distancia HealthKit cuando el GPS no está disponible, ya que es capaz de generar ejemplos de distancia sin GPS.
- Permite al usuario iniciar el entrenamiento desde el Apple Watch o el iPhone.
- Permite que la aplicación muestre entrenamientos de otros orígenes (como otras aplicaciones de terceros) en sus vistas de datos históricas.
- Asegúrese de que la aplicación no muestra los entrenamientos eliminados en los datos históricos.

## <a name="summary"></a>Resumen

En este artículo se han explicado las mejoras que Apple ha realizado en las aplicaciones de entrenamiento en watchos 3 y cómo implementarlas en Xamarin.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [Introducción a HealthKit](~/ios/platform/healthkit.md)
