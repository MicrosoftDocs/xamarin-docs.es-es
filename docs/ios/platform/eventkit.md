---
title: EventKit en Xamarin. iOS
description: En este documento se describe EventKit y cómo usarlo en Xamarin. iOS. Se describen los calendarios, los eventos de calendario y los recordatorios, se examinan las clases que se usan normalmente al programar con EventKit, etc.
ms.prod: xamarin
ms.assetid: 00E88629-357D-1FCD-4FCE-1330D5D9D32C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 1be6da2bbaf4aeffe00d90945bd06867f929c334
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032546"
---
# <a name="eventkit-in-xamarinios"></a>EventKit en Xamarin. iOS

iOS tiene dos aplicaciones relacionadas con el calendario integradas: la aplicación de calendario y la aplicación de recordatorios. Es lo suficientemente sencillo para comprender cómo la aplicación de calendario administra los datos de calendario, pero la aplicación de recordatorios es menos obvia. En realidad, los recordatorios pueden tener fechas asociadas en cuanto a su vencimiento, cuando se completan, etc. Como tal, iOS almacena todos los datos de calendario, ya sean eventos de calendario o recordatorios, en una ubicación, denominada *base de datos de calendario*.

El marco de EventKit proporciona una manera de tener acceso a los *calendarios*, *eventos de calendario*y datos de *recordatorios* que almacena la base de datos de calendario. El acceso a los calendarios y los eventos de calendario está disponible desde iOS 4, pero el acceso a los recordatorios es nuevo en iOS 6.

En esta guía vamos a tratar:

- **Aspectos básicos de EventKit** : se presentan los elementos fundamentales de EventKit a través de las clases principales y se proporciona una descripción de su uso. En esta sección se requiere la lectura antes de abordar la siguiente parte del documento. 
- **Tareas comunes** : la sección tareas comunes pretende ser una referencia rápida sobre cómo hacer cosas comunes como; enumerar calendarios, crear, guardar y recuperar eventos de calendario y recordatorios, así como usar los Controladores integrados para crear y modificar eventos de calendario. En esta sección no es necesario leer de antemano, ya que se trata de una referencia para tareas concretas. 

Todas las tareas de esta guía están disponibles en la aplicación de ejemplo complementaria:

 [![](eventkit-images/01.png "The companion sample application screens")](eventkit-images/01.png#lightbox)

## <a name="requirements"></a>Requisitos

EventKit se presentó en iOS 4,0, pero el acceso a los datos de recordatorios se presentó en iOS 6,0. Por lo tanto, para realizar el desarrollo de EventKit general, deberá tener como destino como mínimo la versión 4,0 y 6,0 para los recordatorios.

Además, la aplicación de avisos no está disponible en el simulador, lo que significa que los datos de recordatorios tampoco estarán disponibles, a menos que los Agregue primero. Además, solo se muestran las solicitudes de acceso al usuario en el dispositivo real. Como tal, el desarrollo de EventKit se prueba mejor en el dispositivo.

## <a name="event-kit-basics"></a>Conceptos básicos del kit de eventos

Al trabajar con EventKit, es importante tener una idea de las clases comunes y su uso. Todas estas clases se pueden encontrar en el `EventKit` y `EventKitUI` (para el `EKEventEditController`).

### <a name="eventstore"></a>EventStore

La clase *EventStore* es la clase más importante de EventKit porque es necesario para realizar cualquier operación en EventKit. Puede considerarse como el almacenamiento persistente o el motor de base de datos para todos los datos de EventKit. En `EventStore` tiene acceso a los calendarios y los eventos de calendario en la aplicación de calendario, así como a los recordatorios de la aplicación de recordatorios.

Dado que `EventStore` es como un motor de base de datos, debe ser de larga duración, lo que significa que debe crearse y destruirse lo menos posible durante la vigencia de una instancia de la aplicación. De hecho, se recomienda que una vez que cree una instancia de un `EventStore` en una aplicación, mantenga esa referencia en torno a toda la duración de la aplicación, a menos que esté seguro de que no la necesitará de nuevo. Además, todas las llamadas deben ir a una única instancia de `EventStore`. Por esta razón, se recomienda usar el patrón singleton para mantener una única instancia disponible.

#### <a name="creating-an-event-store"></a>Crear un almacén de eventos

En el código siguiente se muestra una manera eficaz de crear una instancia única de la clase `EventStore` y hacer que esté disponible estáticamente desde dentro de una aplicación:

```csharp
public class App
{
    public static App Current {
            get { return current; }
    }
    private static App current;

    public EKEventStore EventStore {
            get { return eventStore; }
    }
    protected EKEventStore eventStore;

    static App ()
    {
            current = new App();
    }
    protected App () 
    {
            eventStore = new EKEventStore ( );
    }
}
```

El código anterior usa el patrón singleton para crear una instancia de la `EventStore` cuando se carga la aplicación. A continuación, se puede tener acceso a la `EventStore` globalmente desde dentro de la aplicación de la siguiente manera:

```csharp
App.Current.EventStore;
```

Tenga en cuenta que todos los ejemplos de aquí usan este patrón, por lo que hacen referencia al `EventStore` a través de `App.Current.EventStore`.

#### <a name="requesting-access-to-calendar-and-reminder-data"></a>Solicitar acceso al calendario y a los datos de recordatorio

Antes de que se permita el acceso a los datos a través de EventStore, una aplicación debe solicitar primero el acceso a los datos de eventos de calendario o a los datos de recordatorios, en función de lo que necesite. Para facilitar esto, el `EventStore` expone un método denominado `RequestAccess` que, cuando se llama, muestra una vista de alerta al usuario que le indica que la aplicación solicita acceso a los datos del calendario o datos de recordatorio, en función de la `EKEntityType` que se pase a. instalar. Dado que genera una vista de alerta, la llamada es asincrónica y llamará a un controlador de finalización que se pasa como un `NSAction` (o lambda) que recibirá dos parámetros. un valor booleano que indica si se ha concedido o no el acceso, y un `NSError`, que, si no es null, contendrá cualquier información de error en la solicitud. Por ejemplo, el código siguiente solicitará acceso a los datos de eventos de calendario y mostrará una vista de alerta si no se ha concedido la solicitud.

```csharp
App.Current.EventStore.RequestAccess (EKEntityType.Event, 
    (bool granted, NSError e) => {
            if (granted)
                    //do something here
            else
                    new UIAlertView ( "Access Denied", 
"User Denied Access to Calendar Data", null,
"ok", null).Show ();
            } );
```

Una vez que se haya concedido la solicitud, se recordará siempre que la aplicación se instale en el dispositivo y no se mostrará una alerta al usuario.
Sin embargo, el acceso solo se proporciona al tipo de recurso, ya sean eventos de calendario o recordatorios. Si una aplicación necesita acceder a ambos, debería solicitar ambos.

Dado que se recuerda el permiso, es relativamente barato realizar la solicitud cada vez, por lo que es conveniente solicitar siempre el acceso antes de realizar una operación.

Además, dado que se llama al controlador de finalización en un subproceso independiente (que no es de interfaz de usuario), se debe llamar a todas las actualizaciones de la interfaz de usuario en el controlador de finalización a través de `InvokeOnMainThread`; de lo contrario, se producirá una excepción y, si no se detecta, se bloqueará la aplicación.

### <a name="ekentitytype"></a>EKEntityType

`EKEntityType` es una enumeración que describe el tipo de `EventKit` elemento o los datos. Tiene dos valores: `Event` y recordatorio. Se utiliza en varios métodos, incluidos `EventStore.RequestAccess` para indicar a `EventKit` qué tipo de datos se va a obtener o recuperar.

### <a name="ekcalendar"></a>EKCalendar

 *EKCalendar* representa un calendario, que contiene un grupo de eventos de calendario. Los calendarios se pueden almacenar en muchos lugares distintos, como localmente, en *iCloud*, en una ubicación de proveedor de terceros, como un *servidor de Exchange* o *Google*, etc. Muchas veces `EKCalendar` se usa para indicar a `EventKit` dónde buscar eventos o dónde guardarlos.

### <a name="ekeventeditcontroller"></a>EKEventEditController

 *EKEventEditController* se puede encontrar en el espacio de nombres `EventKitUI` y es un controlador integrado que se puede usar para editar o crear eventos de calendario. Al igual que los controladores de cámara integrados, `EKEventEditController` realiza el pesado trabajo en la visualización de la interfaz de usuario y el control de guardado.

### <a name="ekevent"></a>EKEvent

 *EKEvent* representa un evento de calendario. Tanto `EKEvent` como `EKReminder` heredan de `EKCalendarItem` y tienen campos como `Title`, `Notes`, etc.

### <a name="ekreminder"></a>EKReminder

 *EKReminder* representa un elemento de recordatorio.

### <a name="ekspan"></a>EKSpan

*EKSpan* es una enumeración que describe el intervalo de eventos al modificar eventos que pueden repetirse y tiene dos valores: *esta* y *FutureEvents*. `ThisEvent` significa que cualquier cambio solo se producirá en el evento determinado de la serie a la que se hace referencia, mientras que `FutureEvents` afectará a ese evento y a todas las periodicidades futuras.

## <a name="tasks"></a>Tareas

Para facilitar su uso, el uso de EventKit se ha dividido en tareas comunes, que se describen en las secciones siguientes.

### <a name="enumerate-calendars"></a>Enumerar calendarios

Para enumerar los calendarios que el usuario ha configurado en el dispositivo, llame a `GetCalendars` en el `EventStore` y pase el tipo de calendarios (recordatorios o eventos) que desea recibir:

```csharp
EKCalendar[] calendars = 
App.Current.EventStore.GetCalendars ( EKEntityType.Event );
```

### <a name="add-or-modify-an-event-using-the-built-in-controller"></a>Agregar o modificar un evento con el controlador integrado

El *EKEventEditViewController* realiza una gran parte del trabajo pesado si desea crear o editar un evento con la misma interfaz de usuario que se presenta al usuario al utilizar la aplicación de calendario:

 [![](eventkit-images/02.png "The UI that is presented to the user when using the Calendar Application")](eventkit-images/02.png#lightbox)

Para usarlo, querrá declararlo como una variable de nivel de clase para que no obtenga la recopilación de elementos no utilizados si se declara dentro de un método:

```csharp
public class HomeController : DialogViewController
{
        protected CreateEventEditViewDelegate eventControllerDelegate;
        ...
}
```

A continuación, para iniciarla: cree una instancia de ella, asígnele una referencia al `EventStore`, conecte a ella un delegado de *EKEventEditViewDelegate* y, a continuación, muestre el uso de `PresentViewController`:

```csharp
EventKitUI.EKEventEditViewController eventController = 
        new EventKitUI.EKEventEditViewController ();

// set the controller's event store - it needs to know where/how to save the event
eventController.EventStore = App.Current.EventStore;

// wire up a delegate to handle events from the controller
eventControllerDelegate = new CreateEventEditViewDelegate ( eventController );
eventController.EditViewDelegate = eventControllerDelegate;

// show the event controller
PresentViewController ( eventController, true, null );
```

Opcionalmente, si desea rellenar previamente el evento, puede crear un evento de marca (como se muestra a continuación), o bien puede recuperar un evento guardado:

```csharp
EKEvent newEvent = EKEvent.FromStore ( App.Current.EventStore );
// set the alarm for 10 minutes from now
newEvent.AddAlarm ( EKAlarm.FromDate ( DateTime.Now.AddMinutes ( 10 ) ) );
// make the event start 20 minutes from now and last 30 minutes
newEvent.StartDate = DateTime.Now.AddMinutes ( 20 );
newEvent.EndDate = DateTime.Now.AddMinutes ( 50 );
newEvent.Title = "Get outside and exercise!";
newEvent.Notes = "This is your reminder to go and exercise for 30 minutes.”;
```

Si desea rellenar previamente la interfaz de usuario, asegúrese de establecer la propiedad de evento en el controlador:

```csharp
eventController.Event = newEvent;
```

Para usar un evento existente, consulte la sección *recuperar un evento por identificador* más adelante.

El delegado debe reemplazar el método `Completed`, al que llama el controlador cuando el usuario finaliza con el cuadro de diálogo:

```csharp
protected class CreateEventEditViewDelegate : EventKitUI.EKEventEditViewDelegate
{
        // we need to keep a reference to the controller so we can dismiss it
        protected EventKitUI.EKEventEditViewController eventController;

        public CreateEventEditViewDelegate (EventKitUI.EKEventEditViewController eventController)
        {
                // save our controller reference
                this.eventController = eventController;
        }

        // completed is called when a user eith
        public override void Completed (EventKitUI.EKEventEditViewController controller, EKEventEditViewAction action)
        {
                eventController.DismissViewController (true, null);
                }
        }
}
```

Opcionalmente, en el delegado, puede comprobar la *acción* en el método `Completed` para modificar el evento y volver a guardar, o hacer otras cosas, si se cancela, etc:

```csharp
public override void Completed (EventKitUI.EKEventEditViewController controller, EKEventEditViewAction action)
{
        eventController.DismissViewController (true, null);

        switch ( action ) {

        case EKEventEditViewAction.Canceled:
                break;
        case EKEventEditViewAction.Deleted:
                break;
        case EKEventEditViewAction.Saved:
                // if you wanted to modify the event you could do so here,
// and then save:
                //App.Current.EventStore.SaveEvent ( controller.Event, )
                break;
        }
}
```

### <a name="creating-an-event-programmatically"></a>Crear un evento mediante programación

Para crear un evento en el código, use el Factory Method *FromStore* en la clase `EKEvent` y establezca los datos en ella:

```csharp
EKEvent newEvent = EKEvent.FromStore ( App.Current.EventStore );
// set the alarm for 10 minutes from now
newEvent.AddAlarm ( EKAlarm.FromDate ( DateTime.Now.AddMinutes ( 10 ) ) );
// make the event start 20 minutes from now and last 30 minutes
newEvent.StartDate = DateTime.Now.AddMinutes ( 20 );
newEvent.EndDate = DateTime.Now.AddMinutes ( 50 );
newEvent.Title = "Get outside and do some exercise!";
newEvent.Notes = "This is your motivational event to go and do 30 minutes of exercise. Super important. Do this.";
```

Debe establecer el calendario en el que desea guardar el evento, pero si no tiene preferencias, puede usar el valor predeterminado:

```csharp
newEvent.Calendar = App.Current.EventStore.DefaultCalendarForNewEvents;
```

Para guardar el evento, llame al método *SaveEvent* en el `EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveEvent ( newEvent, EKSpan.ThisEvent, out e );
```

Una vez guardada, la propiedad *parámetros EventIdentifier* se actualizará con un identificador único que se puede usar más adelante para recuperar el evento:

```csharp
Console.WriteLine ("Event Saved, ID: " + newEvent.CalendarItemIdentifier);
```

 `EventIdentifier` es un GUID con formato de cadena.

### <a name="create-a-reminder-programmatically"></a>Crear un recordatorio mediante programación

Crear un recordatorio en el código es muy similar a crear un evento de calendario:

```csharp
EKReminder reminder = EKReminder.Create ( App.Current.EventStore );
reminder.Title = "Do something awesome!";
reminder.Calendar = App.Current.EventStore.DefaultCalendarForNewReminders;
```

Para guardar, llame al método *SaveReminder* en el `EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveReminder ( reminder, true, out e );
```

### <a name="retrieving-an-event-by-id"></a>Recuperación de un evento por identificador

Para recuperar un evento por su identificador, use el método *EventFromIdentifier* en el `EventStore` y pásele el `EventIdentifier` extraído del evento:

```csharp
EKEvent mySavedEvent = App.Current.EventStore.EventFromIdentifier ( newEvent.EventIdentifier );
```

En el caso de los eventos, hay dos otras propiedades de identificador, pero `EventIdentifier` es la única que funciona para esto.

### <a name="retrieving-a-reminder-by-id"></a>Recuperación de un recordatorio por identificador

Para recuperar un recordatorio, use el método *GetCalendarItem* en el `EventStore` y páselo *CalendarItemIdentifier*:

```csharp
EKCalendarItem myReminder = App.Current.EventStore.GetCalendarItem ( reminder.CalendarItemIdentifier );
```

Dado que `GetCalendarItem` devuelve un `EKCalendarItem`, se debe convertir a `EKReminder` si necesita tener acceso a los datos de recordatorio o utilizar la instancia como `EKReminder` más adelante.

No use `GetCalendarItem` para eventos de calendario, como en el momento de la escritura, no funciona.

### <a name="deleting-an-event"></a>Eliminar un evento

Para eliminar un evento de calendario, llame a *RemoveEvent* en el `EventStore` y pase una referencia al evento y el `EKSpan`adecuado:

```csharp
NSError e;
App.Current.EventStore.RemoveEvent ( mySavedEvent, EKSpan.ThisEvent, true, out e);
```

Sin embargo, tenga en cuenta que, una vez eliminado un evento, la referencia de evento se `null`.

### <a name="deleting-a-reminder"></a>Eliminar un recordatorio

Para eliminar un recordatorio, llame a *RemoveReminder* en el `EventStore` y pase una referencia al recordatorio:

```csharp
NSError e;
App.Current.EventStore.RemoveReminder ( myReminder as EKReminder, true, out e);
```

Tenga en cuenta que en el código anterior hay una conversión a `EKReminder`, porque se usó `GetCalendarItem` para recuperarlo.

### <a name="searching-for-events"></a>Buscar eventos

Para buscar eventos de calendario, debe crear un objeto *NSPredicate* mediante el método *PredicateForEvents* en el `EventStore`. Un `NSPredicate` es un objeto de datos de consulta que usa iOS para buscar coincidencias:

```csharp
DateTime startDate = DateTime.Now.AddDays ( -7 );
DateTime endDate = DateTime.Now;
// the third parameter is calendars we want to look in, to use all calendars, we pass null
NSPredicate query = App.Current.EventStore.PredicateForEvents ( startDate, endDate, null );
```

Una vez que haya creado el `NSPredicate`, use el método *EventsMatching* en el `EventStore`:

```csharp
// execute the query
EKCalendarItem[] events = App.Current.EventStore.EventsMatching ( query );
```

Tenga en cuenta que las consultas son sincrónicas (bloqueos) y pueden tardar mucho tiempo, dependiendo de la consulta, por lo que puede que desee poner en marcha un nuevo subproceso o tarea para hacerlo.

### <a name="searching-for-reminders"></a>Buscar recordatorios

La búsqueda de recordatorios es similar a los eventos; requiere un predicado, pero la llamada ya es asincrónica, por lo que no se preocupe por el bloqueo del subproceso:

```csharp
// create our NSPredicate which we'll use for the query
NSPredicate query = App.Current.EventStore.PredicateForReminders ( null );

// execute the query
App.Current.EventStore.FetchReminders (
        query, ( EKReminder[] items ) => {
                // do someting with the items
        } );
```

## <a name="summary"></a>Resumen

En este documento se proporciona información general sobre las partes importantes del marco de trabajo de EventKit y varias de las tareas más comunes. Sin embargo, el marco de trabajo de EventKit es muy grande y eficaz, e incluye características que no se han introducido aquí, como por ejemplo, actualizaciones por lotes, configuración de alarmas, configuración de periodicidad en eventos, registro y escucha de cambios en la base de datos de calendario. establecimiento de geovallas y mucho más.  Para obtener más información, consulte la [Guía de programación del calendario y los recordatorios](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)de Apple.

## <a name="related-links"></a>Vínculos relacionados

- [Calendarios (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/calendars)
- [Introducción a iOS 6](~/ios/platform/introduction-to-ios6/index.md)
- [Introducción a los calendarios y los recordatorios](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)
