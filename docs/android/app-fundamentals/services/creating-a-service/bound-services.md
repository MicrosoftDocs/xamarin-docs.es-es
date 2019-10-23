---
title: Servicios enlazados en Xamarin. Android
description: Los servicios enlazados son servicios de Android que proporcionan una interfaz cliente-servidor que un cliente (como una actividad de Android) puede interactuar con. En esta guía se describen los componentes clave implicados en la creación de un servicio enlazado y cómo usarlo en una aplicación de Xamarin. Android.
ms.prod: xamarin
ms.assetid: 809ECE88-EF08-4E9A-B389-A2DC08C51A6E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/04/2018
ms.openlocfilehash: 584f523446584192cfa882697c0f76865ce78a10
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "70754924"
---
# <a name="bound-services-in-xamarinandroid"></a>Servicios enlazados en Xamarin. Android

_Los servicios enlazados son servicios de Android que proporcionan una interfaz cliente-servidor que un cliente (como una actividad de Android) puede interactuar con. En esta guía se describen los componentes clave implicados en la creación de un servicio enlazado y cómo usarlo en una aplicación de Xamarin. Android._

## <a name="bound-services-overview"></a>Información general sobre servicios enlazados

Los servicios que proporcionan una interfaz cliente-servidor para que los clientes interactúen directamente con el servicio se conocen como _servicios enlazados_.  Puede haber varios clientes conectados a una única instancia de un servicio al mismo tiempo. El servicio enlazado y el cliente están aislados entre sí. En su lugar, Android proporciona una serie de objetos intermedios que administran el estado de la conexión entre los dos. Este estado es mantenido por un objeto que implementa la interfaz [`Android.Content.IServiceConnection`](xref:Android.Content.IServiceConnection) .  El cliente crea este objeto y se pasa como parámetro al método [`BindService`](xref:Android.Content.Context.BindService*) . El `BindService` está disponible en cualquier objeto de [`Android.Content.Context`](xref:Android.Content.Context) (por ejemplo, una actividad). Se trata de una solicitud al sistema operativo Android para iniciar el servicio y enlazar un cliente a él. Hay tres maneras de enlazar un cliente a un servicio mediante el método `BindService`:

- **Un enlazador de servicios** &ndash; un enlazador de servicios es una clase que implementa la interfaz de [`Android.OS.IBinder`](xref:Android.OS.IBinder) . La mayoría de las aplicaciones no implementarán esta interfaz directamente, sino que extenderán la clase [`Android.OS.Binder`](xref:Android.OS.Binder) . Este es el enfoque más común y es adecuado para cuando el servicio y el cliente existen en el mismo proceso.
- **Con Messenger** &ndash; esta técnica es adecuada para cuando el servicio pueda existir en un proceso independiente. En su lugar, se calcula la referencia de las solicitudes de servicio entre el cliente y el servicio a través de un [`Android.OS.Messenger`](xref:Android.OS.Messenger). Se crea un [`Android.OS.Handler`](xref:Android.OS.Handler) en el servicio que controlará las solicitudes de `Messenger`. Esto se tratará en otra guía.
- El **uso del lenguaje de definición de interfaz Android (AIDL])** &ndash; [AIDL]](https://developer.android.com/guide/components/aidl) es una técnica avanzada que no se tratará en esta guía.

Una vez que un cliente se ha enlazado a un servicio, la comunicación entre los dos se produce a través de `Android.OS.IBinder` objeto.  Este objeto es responsable de la interfaz que permitirá al cliente interactuar con el servicio. No es necesario que cada aplicación de Xamarin. Android implemente esta interfaz desde cero, el Android SDK proporciona la clase [`Android.OS.Binder`](xref:Android.OS.Binder) , que se encarga de la mayor parte del código necesario con la serialización del objeto entre el cliente y el servicio.

Cuando un cliente se realiza con el servicio, debe desenlazarlo llamando al método `UnbindService`. Una vez que el último cliente se ha desenlazado de un servicio, Android detendrá y eliminará el servicio enlazado.

En este diagrama se ilustra el modo en que la actividad, la conexión de servicio, el enlazador y el servicio se relacionan entre sí:

![Diagrama que muestra cómo se relacionan entre sí los componentes del servicio](bound-services-images/bound-services-02.png "Diagrama que muestra cómo se relacionan entre sí los componentes del servicio.")

En esta guía se explica cómo extender la clase `Service` para implementar un servicio enlazado. También se trata la implementación de `IServiceConnection` y la extensión de `Binder` para permitir que un cliente se comunique con el servicio. Una aplicación de ejemplo acompaña a esta guía, que contiene una solución con un solo proyecto de Xamarin. Android denominado **[BoundServiceDemo](https://github.com/xamarin/monodroid-samples/tree/master/ApplicationFundamentals/ServiceSamples/BoundServiceDemo)** . Se trata de una aplicación muy básica que muestra cómo implementar un servicio y cómo enlazar una actividad a él. El servicio enlazado tiene una API muy sencilla con un solo método, `GetFormattedTimestamp`, que devuelve una cadena que indica al usuario Cuándo se ha iniciado el servicio y cuánto tiempo se ha estado ejecutando. La aplicación también permite al usuario Desenlazar manualmente y enlazar con el servicio.

[![Screenshot de la aplicación que se ejecuta en un teléfono Android](bound-services-images/bound-services-03-sml.png)](bound-services-images/bound-services-03.png#lightbox)

## <a name="implementing-and-consuming-a-bound-service"></a>Implementación y consumo de un servicio enlazado

Hay tres componentes que se deben implementar para que una aplicación Android consuma un servicio enlazado:

1. **Extienda la clase `Service` e implemente los métodos de devolución de llamada del ciclo de vida** &ndash; esta clase contendrá el código que realizará el trabajo que se solicitará del servicio. Esto se tratará con más detalle a continuación.
2. **Cree una clase que implemente `IServiceConnection`** &ndash; esta interfaz proporciona métodos de devolución de llamada que Android invocará para notificar al cliente cuando la conexión con el servicio ha cambiado, es decir, el cliente se ha conectado o desconectado al servicio. La conexión de servicio también proporcionará una referencia a un objeto que el cliente puede utilizar para interactuar directamente con el servicio. Esta referencia se conoce como el _enlazador_.
3. **Cree una clase que implemente `IBinder`** &ndash; una implementación de _enlazador_ proporciona la API que un cliente utiliza para comunicarse con el servicio. El enlazador puede proporcionar una referencia al servicio enlazado, lo que permite invocar métodos directamente o el enlazador puede proporcionar una API de cliente que encapsula y oculta el servicio enlazado de la aplicación. Un `IBinder` debe proporcionar el código necesario para las llamadas a procedimientos remotos. No es necesario (o recomendado) implementar la interfaz de `IBinder` directamente. En su lugar, las aplicaciones deben extender el tipo de `Binder` que proporciona la mayor parte de la funcionalidad básica requerida por un `IBinder`.
4. **Iniciar y enlazar a un servicio** &ndash; una vez que se ha creado la conexión del servicio, el enlazador y el servicio, la aplicación de Android es responsable de iniciar el servicio y enlazarlo.

Cada uno de estos pasos se tratará con más detalle en las secciones siguientes.

### <a name="extend-the-service-class"></a>Extender la clase `Service`

Para crear un servicio con Xamarin. Android, es necesario subclase `Service` y para adornar la clase con la [`ServiceAttribute`](xref:Android.App.ServiceAttribute). Las herramientas de compilación de Xamarin. Android usan el atributo para registrar correctamente el servicio en el archivo **archivo AndroidManifest. XML** de la aplicación de forma similar a una actividad, un servicio enlazado tiene su propio ciclo de vida y métodos de devolución de llamada asociados a los eventos importantes en su ciclo de vida. La lista siguiente es un ejemplo de algunos de los métodos de devolución de llamada más comunes que implementará un servicio:

- `OnCreate` &ndash; este método se invoca mediante Android, ya que se crea una instancia del servicio. Se utiliza para inicializar las variables u objetos que requiere el servicio durante su vigencia. Este método es opcional.
- `OnBind` &ndash; este método debe ser implementado por todos los servicios enlazados. Se invoca cuando el primer cliente intenta conectarse al servicio. Devolverá una instancia de `IBinder` para que el cliente pueda interactuar con el servicio. Siempre que el servicio se esté ejecutando, el objeto de `IBinder` se usará para satisfacer las solicitudes de cliente futuras para enlazar con el servicio.
- `OnUnbind` &ndash; se llama a este método cuando todos los clientes enlazados tienen desenlazado. Al devolver `true` desde este método, el servicio llamará más adelante a `OnRebind` con la intención pasada a `OnUnbind` cuando los nuevos clientes se enlacen a él. Lo haría cuando un servicio siga ejecutándose una vez que se haya desenlazado. Esto ocurrirá si el servicio desenlazado recientemente también era un servicio iniciado y `StopService` o `StopSelf` no se hubiesen llamado. En este escenario, `OnRebind` permite recuperar el intento. El valor predeterminado devuelve `false`, que no hace nada. Opcional.
- `OnDestroy` &ndash; se llama a este método cuando Android está destruyendo el servicio. Cualquier limpieza necesaria, como liberar recursos, debe realizarse en este método. Opcional.

Los eventos de ciclo de vida clave de un servicio enlazado se muestran en este diagrama:

![Diagrama que muestra el orden en el que se llama a los métodos de ciclo de vida](bound-services-images/bound-services-01.png "Diagrama que muestra el orden en el que se llama a los métodos de ciclo de vida.")

En el siguiente fragmento de código, desde la aplicación complementaria que acompaña a esta guía, se muestra cómo implementar un servicio enlazado en Xamarin. Android:

```csharp
using Android.App;
using Android.Util;
using Android.Content;
using Android.OS;

namespace BoundServiceDemo
{
    [Service(Name="com.xamarin.ServicesDemo1")]
    public class TimestampService : Service, IGetTimestamp
    {
        static readonly string TAG = typeof(TimestampService).FullName;
        IGetTimestamp timestamper;

        public IBinder Binder { get; private set; }

        public override void OnCreate()
        {
            // This method is optional to implement
            base.OnCreate();
            Log.Debug(TAG, "OnCreate");
            timestamper = new UtcTimestamper();
        }

        public override IBinder OnBind(Intent intent)
        {
            // This method must always be implemented
            Log.Debug(TAG, "OnBind");
            this.Binder = new TimestampBinder(this);
            return this.Binder;
        }

        public override bool OnUnbind(Intent intent)
        {
            // This method is optional to implement
            Log.Debug(TAG, "OnUnbind");
            return base.OnUnbind(intent);
        }

        public override void OnDestroy()
        {
            // This method is optional to implement
            Log.Debug(TAG, "OnDestroy");
            Binder = null;
            timestamper = null;
            base.OnDestroy();
        }

        /// <summary>
        /// This method will return a formatted timestamp to the client.
        /// </summary>
        /// <returns>A string that details what time the service started and how long it has been running.</returns>
        public string GetFormattedTimestamp()
        {
            return timestamper?.GetFormattedTimestamp();
        }
    }
}
```

En el ejemplo, el método `OnCreate` Inicializa un objeto que contiene la lógica para recuperar y dar formato a una marca de tiempo que el cliente solicitará. Cuando el primer cliente intenta enlazar con el servicio, Android invocará el método `OnBind`. Este servicio creará una instancia de un objeto `TimestampBinder` que permitirá a los clientes tener acceso a esta instancia del servicio en ejecución. La clase `TimestampBinder` se describe en la sección siguiente.

### <a name="implementing-ibinder"></a>Implementación de IBinder

Como se mencionó, un objeto `IBinder` proporciona el canal de comunicación entre un cliente y un servicio. Las aplicaciones de Android no deben implementar la interfaz `IBinder`, por lo que se debe extender la [`Android.OS.Binder`](xref:Android.OS.Binder) . La clase `Binder` proporciona gran parte de la infraestructura necesaria para calcular las referencias del objeto de enlazador desde el servicio (que se puede estar ejecutando en un proceso independiente) al cliente. En la mayoría de los casos, la subclase `Binder` es solo unas pocas líneas de código y ajusta una referencia al servicio. En este ejemplo, `TimestampBinder` tiene una propiedad que expone el `TimestampService` al cliente:

```csharp
public class TimestampBinder : Binder
{
    public TimestampBinder(TimestampService service)
    {
        this.Service = service;
    }

    public TimestampService Service { get; private set; }
}
```

Esta `Binder` permite invocar los métodos públicos en el servicio; por ejemplo:

```csharp
string currentTimestamp = serviceConnection.Binder.Service.GetFormattedTimestamp()
```

Una vez que se ha ampliado `Binder`, es necesario implementar `IServiceConnection` para conectar todo.

### <a name="creating-the-service-connection"></a>Crear la conexión de servicio

El `IServiceConnection` presentará | present | exponer | conectar el objeto de `Binder` al cliente. Además de implementar la interfaz de `IServiceConnection`, la clase debe extender `Java.Lang.Object`. La conexión de servicio también debe proporcionar alguna manera en que el cliente puede tener acceso al `Binder` (y, por tanto, comunicarse con el servicio enlazado).

Este código procede del proyecto de ejemplo que lo acompaña es una forma posible de implementar `IServiceConnection`:

```csharp
using Android.Util;
using Android.OS;
using Android.Content;

namespace BoundServiceDemo
{
    public class TimestampServiceConnection : Java.Lang.Object, IServiceConnection, IGetTimestamp
    {
        static readonly string TAG = typeof(TimestampServiceConnection).FullName;

        MainActivity mainActivity;
        public TimestampServiceConnection(MainActivity activity)
        {
            IsConnected = false;
            Binder = null;
            mainActivity = activity;
        }

        public bool IsConnected { get; private set; }
        public TimestampBinder Binder { get; private set; }

        public void OnServiceConnected(ComponentName name, IBinder service)
        {
            Binder = service as TimestampBinder;
            IsConnected = this.Binder != null;

            string message = "onServiceConnected - ";
            Log.Debug(TAG, $"OnServiceConnected {name.ClassName}");

            if (IsConnected)
            {
                message = message + " bound to service " + name.ClassName;
                mainActivity.UpdateUiForBoundService();
            }
            else
            {
                message = message + " not bound to service " + name.ClassName;
                mainActivity.UpdateUiForUnboundService();
            }

            Log.Info(TAG, message);
            mainActivity.timestampMessageTextView.Text = message;

        }

        public void OnServiceDisconnected(ComponentName name)
        {
            Log.Debug(TAG, $"OnServiceDisconnected {name.ClassName}");
            IsConnected = false;
            Binder = null;
            mainActivity.UpdateUiForUnboundService();
        }

        public string GetFormattedTimestamp()
        {
            if (!IsConnected)
            {
                return null;
            }

            return Binder?.GetFormattedTimestamp();
        }
    }
}

```

Como parte del proceso de enlace, Android invocará el método `OnServiceConnected`, proporcionando el `name` del servicio que se está enlazando y el `binder` que contiene una referencia al propio servicio. En este ejemplo, la conexión de servicio tiene dos propiedades, una que contiene una referencia al enlazador y una marca booleana para si el cliente está conectado al servicio o no.

El método `OnServiceDisconnected` solo se invoca cuando la conexión entre un cliente y un servicio se pierde o se interrumpe inesperadamente. Este método permite al cliente tener la oportunidad de responder a la interrupción en el servicio.  

## <a name="starting-and-binding-to-a-service-with-an-explicit-intent"></a>Inicio y enlace a un servicio con un intento explícito

Para usar un servicio enlazado, un cliente (por ejemplo, una actividad) debe crear una instancia de un objeto que implemente `Android.Content.IServiceConnection` e invocar el método `BindService`. `BindService` devolverá `true` si el servicio está enlazado a, `false` si no lo está. El método `BindService` toma tres parámetros:

- **Una `Intent`** &ndash; la intención debe identificar explícitamente a qué servicio se va a conectar.
- **Un objeto `IServiceConnection`** &ndash; este objeto es un intermediario que proporciona métodos de devolución de llamada para notificar al cliente cuando se inicia y se detiene el servicio enlazado.
- **[`Android.Content.Bind`](xref:Android.Content.Bind) enum** &ndash; este parámetro es un conjunto de marcas que el sistema utiliza para enlazar el objeto. El valor que se usa con más frecuencia es [`Bind.AutoCreate`](xref:Android.Content.Bind.AutoCreate), que iniciará automáticamente el servicio si aún no se está ejecutando.

El siguiente fragmento de código es un ejemplo de cómo iniciar un servicio enlazado en una actividad mediante un intento explícito:

```csharp
protected override void OnStart ()
{
    base.OnStart ();

    if (serviceConnection == null)
    {
        this.serviceConnection = new TimestampServiceConnection(this);
    }

    Intent serviceToStart = new Intent(this, typeof(TimestampService));
    BindService(serviceToStart, this.serviceConnection, Bind.AutoCreate);

}
```

> [!IMPORTANT]
> A partir de Android 5,0 (nivel de API 21), solo es posible enlazar a un servicio con una intención explícita.

## <a name="architectural-notes-about-the-service-connection-and-the-binder"></a>Notas arquitectónicas sobre la conexión de servicio y el enlazador.

Algunos puristas de OOP pueden no aprobar la implementación anterior de la clase `TimestampBinder` ya que es una infracción de la [ley de Demeter](https://en.wikipedia.org/wiki/Law_of_Demeter) que, en sus Estados de formulario más sencillos, no se comunica con los extraños; Hable solo con sus amigos. Esta implementación concreta expone la clase de `TimestampService` concreta a todos los clientes.

En realidad, no es necesario que el cliente conozca el `TimestampService` y exponer esa clase concreta a los clientes puede hacer que una aplicación sea más frágil y más difícil de mantener a lo largo de su duración. Un enfoque alternativo es usar una interfaz que exponga el método `GetFormattedTimestamp()` y llamadas proxy al servicio a través del `Binder` (o posible la clase de conexión de servicio):  

```csharp
public class TimestampBinder : Binder, IGetTimestamp
{
    TimestampService service;
    public TimestampBinder(TimestampService service)
    {
        this.service = service;
    }

    public string GetFormattedTimestamp()
    {
        return service?.GetFormattedTimestamp();
    }
}
```

En este ejemplo concreto se permite que una actividad invoque métodos en el propio servicio:

```csharp
// In this example the Activity is only talking to a friend, i.e. the IGetTimestamp interface provided by the Binder.
string currentTimestamp = serviceConnection.Binder.GetFormattedTimestamp()
```

## <a name="related-links"></a>Vínculos relacionados

- [Android. app. Service](xref:Android.App.Service)
- [Android. Content. bind](xref:Android.Content.Bind)
- [Android. Content. Context](xref:Android.Content.Context)
- [Android. Content. IServiceConnection](xref:Android.Content.IServiceConnection)
- [Android. OS. Binder](xref:Android.OS.Binder)
- [Android. OS. IBinder](xref:Android.OS.IBinder)
- [BoundServiceDemo (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-boundservicedemo)
