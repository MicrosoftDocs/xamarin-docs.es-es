---
title: Ejecución de servicios de Android en procesos remotos
description: Por lo general, todos los componentes de una aplicación de Android se ejecutarán en el mismo proceso. Los servicios de Android son una excepción importante para esto, ya que se pueden configurar para que se ejecuten en sus propios procesos y se compartan con otras aplicaciones, incluidas las de otros desarrolladores de Android. En esta guía se explica cómo crear y usar un servicio remoto de Android con Xamarin.
ms.prod: xamarin
ms.assetid: 27A2E972-A690-480B-B31D-5EF1F74F673C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 5429f260399602b7ef15e8263bc74cb8ae940f4f
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "70754881"
---
# <a name="running-android-services-in-remote-processes"></a>Ejecución de servicios de Android en procesos remotos

_Por lo general, todos los componentes de una aplicación de Android se ejecutarán en el mismo proceso. Los servicios de Android son una excepción importante para esto, ya que se pueden configurar para que se ejecuten en sus propios procesos y se compartan con otras aplicaciones, incluidas las de otros desarrolladores de Android. En esta guía se explica cómo crear y usar un servicio remoto de Android con Xamarin._

## <a name="out-of-process-services-overview"></a>Información general de servicios fuera de proceso

Cuando se inicia una aplicación, Android crea un proceso en el que se ejecuta la aplicación. Normalmente, todos los componentes de la aplicación se ejecutarán en este proceso. Los servicios de Android son una excepción importante para esto, ya que se pueden configurar para que se ejecuten en sus propios procesos y se compartan con otras aplicaciones, incluidas las de otros desarrolladores de Android. Estos tipos de servicios se conocen como _servicios remotos_ o _servicios fuera de proceso_. El código de estos servicios se incluirá en el mismo APK que la aplicación principal. sin embargo, cuando se inicia el servicio, Android creará un nuevo proceso solo para ese servicio. Por el contrario, un servicio que se ejecuta en el mismo proceso que el resto de la aplicación a veces se denomina _servicio local_.

En general, no es necesario que una aplicación implemente un servicio remoto. Un servicio local es suficiente (y deseable) para los requisitos de una aplicación en muchos casos. Un fuera de proceso tiene su propio espacio de memoria que debe ser administrado por Android. Aunque esto introduce más sobrecarga en la aplicación general, hay algunos escenarios en los que puede ser ventajoso ejecutar un servicio en su propio proceso:

1. La **funcionalidad de uso compartido** &ndash; algunos desarrolladores de aplicaciones pueden tener varias aplicaciones y funcionalidad que se comparten entre todas las aplicaciones. El empaquetado de esa funcionalidad en un servicio Android que se ejecuta en su propio proceso puede simplificar el mantenimiento de la aplicación. También es posible empaquetar el servicio en su propio APK independiente e implementarlo de forma independiente del resto de la aplicación.
2. **Mejorar la experiencia del usuario** &ndash; hay dos formas posibles de que un servicio fuera de proceso pueda mejorar la experiencia del usuario de la aplicación. La primera forma de tratar con la administración de memoria. Cuando se produce un ciclo de recolección de elementos no utilizados (GC), Android pausará toda la actividad del proceso hasta que se complete el GC. El usuario podría percibir esta pausa como "entrecortado" o "Jank". Cuando un servicio se ejecuta en su propio proceso, es el proceso de servicio que está en pausa, no el proceso de la aplicación. Esta pausa será mucho menos perceptible para el usuario, ya que el proceso de aplicación (y, por lo tanto, la interfaz de usuario) no está en pausa.

    En segundo lugar, si los requisitos de memoria de un proceso se vuelven demasiado grandes, Android puede terminar ese proceso para liberar recursos para el dispositivo. Si un servicio tiene una superficie de memoria grande y se está ejecutando en el mismo proceso que la interfaz de usuario, cuando Android recupere forzosamente esos recursos, se apagará la interfaz de usuario, lo que obliga al usuario a iniciar la aplicación. Sin embargo, si Android cierra un servicio que se ejecuta en su propio proceso, el proceso de la interfaz de usuario no se ve afectado. La interfaz de usuario puede enlazar (y reiniciar) el servicio, ser transparente para el usuario y reanudar el funcionamiento normal.

3. **Mejorar el rendimiento** de la aplicación &ndash; el proceso de la interfaz de usuario puede finalizar o cerrarse independientemente del proceso del servicio. Al mover tareas de inicio largas a un servicio fuera de proceso, es posible que el tiempo de inicio de la interfaz de usuario se haya mejorado (suponiendo que el proceso de servicio se mantenga activo entre las veces que se inicia la interfaz de usuario).

En muchos sentidos, el enlace a un servicio que se ejecuta en otro proceso es el mismo que el [enlace a un servicio local](~/android/app-fundamentals/services/creating-a-service/bound-services.md). El cliente invocará `BindService` para enlazar (e iniciar, si es necesario) el servicio. Se creará un objeto `Android.OS.IServiceConnection` para administrar la conexión entre el cliente y el servicio. Si el cliente se enlaza correctamente al servicio, Android devolverá un objeto a través de la `IServiceConnection` que se puede usar para invocar métodos en el servicio. A continuación, el cliente interactúa con el servicio que usa este objeto. Para revisar, estos son los pasos para enlazar a un servicio:

- **Cree un intento** &ndash; se debe usar un intento explícito para enlazar con el servicio.
- **Implementar y crear instancias de un objeto `IServiceConnection`** &ndash; el objeto `IServiceConnection` actúa como intermediario entre el cliente y el servicio.  Es responsable de supervisar la conexión entre el cliente y el servidor.
- **Invoque el método `BindService`** &ndash; la llamada a `BindService` enviará la intención y la conexión de servicio creada en los pasos anteriores a Android, que se encargará de iniciar el servicio y establecer la comunicación entre el cliente y el servicio.

La necesidad de cruzar los límites del proceso conlleva una complejidad adicional: la comunicación es unidireccional (cliente a servidor) y el cliente no puede invocar directamente métodos en la clase de servicio. Recuerde que cuando un servicio ejecuta el mismo proceso que el cliente, Android proporciona un `IBinder` objeto que puede permitir la comunicación bidireccional. Este no es el caso del servicio que se ejecuta en su propio proceso. Un cliente se comunica con un servicio remoto con la ayuda de la clase `Android.OS.Messenger`.

Cuando un cliente solicita enlazar con el servicio remoto, Android invocará el método de ciclo de vida de `Service.OnBind`, que devolverá el objeto de `IBinder` interno encapsulado por el `Messenger`. El `Messenger` es un contenedor fino sobre una implementación de `IBinder` especial que proporciona el Android SDK. El `Messenger` se encarga de la comunicación entre los dos procesos diferentes. El desarrollador no se preocupe por los detalles de la serialización de un mensaje, el cálculo de referencias del mensaje en el límite del proceso y, a continuación, su deserialización en el cliente. El objeto `Messenger` controla este trabajo. En este diagrama se muestran los componentes de Android del lado cliente que intervienen cuando un cliente inicia el enlace a un servicio fuera de proceso:

![Diagrama que muestra los pasos y componentes de un enlace de cliente a un servicio](out-of-process-services-images/ipc-01.png "Diagrama que muestra los pasos y componentes de un enlace de cliente a un servicio.")

La clase `Service` del proceso remoto pasa por las mismas devoluciones de llamada del ciclo de vida que pasa un servicio enlazado en un proceso local, y muchas de las API implicadas son las mismas. `Service.OnCreate` se usa para inicializar un `Handler` e insertarlo en `Messenger` objeto. Del mismo modo, `OnBind` se invalida, pero en lugar de devolver un objeto `IBinder`, el servicio devolverá el `Messenger`.  En este diagrama se ilustra lo que ocurre en el servicio cuando un cliente se enlaza a él:

![Diagrama que muestra los pasos y componentes que pasa un servicio cuando se enlaza a un cliente remoto](out-of-process-services-images/ipc-02.png "Diagrama que muestra los pasos y los componentes que pasa un servicio cuando se enlaza a un cliente remoto.")

Cuando un servicio recibe un `Message`, lo procesa en la instancia de `Android.OS.Handler`. El servicio implementará su propia subclase `Handler` que debe reemplazar el método `HandleMessage`. El `Messenger` invoca este método y recibe el `Message` como parámetro. El `Handler` inspeccionará los metadatos de `Message` y utilizará esa información para invocar métodos en el servicio.

La comunicación unidireccional se produce cuando un cliente crea un `Message` objeto y lo envía al servicio mediante el método `Messenger.Send`. `Messenger.Send` serializará el `Message` y la mano de los datos serializados en Android, lo que enrutará el mensaje en el límite del proceso y en el servicio.  La `Messenger` hospedada por el servicio creará un objeto `Message` a partir de los datos entrantes. Esta `Message` se coloca en una cola, donde los mensajes se envían de una en una a la `Handler`. En el `Handler` se inspeccionarán los metadatos contenidos en el `Message` y se invocarán los métodos adecuados en la `Service`. En el diagrama siguiente se muestran estos diversos conceptos en acción:

![Diagrama que muestra cómo se pasan los mensajes entre procesos](out-of-process-services-images/ipc-03.png "Diagrama que muestra cómo se pasan los mensajes entre los procesos.")

En esta guía se explican los detalles de la implementación de un servicio fuera de proceso. Se explica cómo implementar un servicio diseñado para ejecutarse en su propio proceso y cómo un cliente puede comunicarse con ese servicio mediante el marco de `Messenger`. También se explica brevemente la comunicación bidireccional: el cliente que envía un mensaje a un servicio y el servicio envía un mensaje de vuelta al cliente. Dado que los servicios se pueden compartir entre diferentes aplicaciones, en esta guía también se describe una técnica para limitar el acceso del cliente al servicio mediante los permisos de Android.

> [!IMPORTANT]
> [Bugzilla 51940/GitHub 1950-Services con procesos aislados y clase de aplicación personalizada no se pueden resolver sobrecargas correctamente](https://github.com/xamarin/xamarin-android/issues/1950) informa de que un servicio Xamarin. Android no se iniciará correctamente cuando la `IsolatedProcess` esté establecida en `true`. Esta guía se proporciona para una referencia. Una aplicación de Xamarin. Android todavía debe ser capaz de comunicarse con un servicio fuera de proceso escrito en Java.

## <a name="requirements"></a>Requisitos

En esta guía se da por supuesto que está familiarizado con la creación de servicios.

Aunque es posible usar las intenciones implícitas con las aplicaciones destinadas a las API de Android anteriores, esta guía se centrará exclusivamente en el uso de las intenciones explícitas. Las aplicaciones que tienen como destino Android 5,0 (nivel de API 21) o superior deben usar una intención explícita para enlazar con un servicio; Esta es la técnica que se mostrará en esta guía.

## <a name="create-a-service-that-runs-in-a-separate-process"></a>Crear un servicio que se ejecute en un proceso independiente

Tal y como se ha descrito anteriormente, el hecho de que un servicio se ejecute en su propio proceso significa que se implican algunas API diferentes. Como introducción rápida, estos son los pasos para enlazar y consumir un servicio remoto:  

- **Cree la subclase `Service`** &ndash; subclase del `Service` tipo e implemente los métodos de ciclo de vida para un servicio enlazado. También es necesario establecer los metadatos que informarán a Android de que el servicio debe ejecutarse en su propio proceso.
- **Implemente un `Handler`** &ndash; el `Handler` sea responsable de analizar las solicitudes del cliente, extraer los parámetros que se pasaron del cliente e invocar los métodos adecuados en el servicio.
- **Cree** una instancia de `Messenger` &ndash; tal y como se describió anteriormente, cada `Service` debe mantener una instancia de la clase `Messenger` que enrutará las solicitudes de cliente al `Handler` creado en el paso anterior.

Un servicio diseñado para ejecutarse en su propio proceso es, fundamentalmente, un servicio enlazado. La clase de servicio extenderá la clase de `Service` base y se decorará con el `ServiceAttribute` que contiene los metadatos que Android necesita agrupar en el manifiesto de Android. Para empezar, las siguientes propiedades de los `ServiceAttribute` que son importantes para un servicio fuera de proceso:

1. `Exported` &ndash; esta propiedad debe establecerse en `true` para permitir que otras aplicaciones interactúen con el servicio. El valor predeterminado de esta propiedad es `false`.
2. `Process` &ndash; se debe establecer esta propiedad. Se utiliza para especificar el nombre del proceso en el que se ejecutará el servicio.
3. `IsolatedProcess` &ndash; esta propiedad permitirá una mayor seguridad, lo que indica a Android que ejecute el servicio en un espacio aislado aislado con el permiso mínimo para interactuar con el resto del sistema. Consulte [Bugzilla 51940-Services con procesos aislados y clase de aplicación personalizada no se pueden resolver sobrecargas correctamente](https://bugzilla.xamarin.com/show_bug.cgi?id=51940).
4. `Permission` &ndash; es posible controlar el acceso del cliente al servicio mediante la especificación de un permiso que los clientes deben solicitar (y se les debe conceder).

Para ejecutar un servicio de su propio proceso, la propiedad `Process` del `ServiceAttribute` debe establecerse en el nombre del servicio. Para interactuar con las aplicaciones externas, la propiedad `Exported` debe establecerse en `true`. Si `Exported` es `false`, solo los clientes del mismo APK (es decir, la misma aplicación) y que se ejecutan en el mismo proceso podrán interactuar con el servicio.

El tipo de proceso en el que se ejecutará el servicio dependerá del valor de la propiedad `Process`. Android identifica tres tipos diferentes de procesos:

- El **proceso privado** &ndash; un proceso privado es uno que solo está disponible para la aplicación que lo inició. Para identificar un proceso como privado, su nombre debe comenzar con un **:** (punto y coma). El servicio que se muestra en el fragmento de código anterior y captura de pantalla es un proceso privado. El siguiente fragmento de código es un ejemplo del `ServiceAttribute`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process=":timestampservice_process",
             Exported=true)]
    ```

- **Proceso global** &ndash; un servicio que se ejecuta en un proceso global es accesible para todas las aplicaciones que se ejecutan en el dispositivo. Un proceso global debe ser un nombre de clase completo que comienza con un carácter en minúscula.
    (A menos que se realicen los pasos para proteger el servicio, otras aplicaciones pueden enlazarse e interactuar con él. La protección del servicio contra el uso no autorizado se tratará más adelante en esta guía).

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

- **Proceso aislado** &ndash; un proceso aislado es un proceso que se ejecuta en su propio espacio aislado, aislado del resto del sistema y sin permisos especiales propios. Para ejecutar un servicio en un proceso aislado, la propiedad `IsolatedProcess` del `ServiceAttribute` se establece en `true`, tal y como se muestra en este fragmento de código:
    
    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             IsolatedProcess= true,
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

> [!IMPORTANT]
> Consulte [Bugzilla 51940-Services con procesos aislados y clase de aplicación personalizada no se pueden resolver sobrecargas correctamente](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)

Un servicio aislado es una forma sencilla de proteger una aplicación y el dispositivo frente a código que no es de confianza. Por ejemplo, una aplicación puede descargar y ejecutar un script desde un sitio Web. En este caso, realizar esta operación en un proceso aislado proporciona una capa adicional de seguridad frente a código que no es de confianza y que pone en peligro el dispositivo Android.

> [!IMPORTANT]
> Una vez que se ha exportado un servicio, el nombre del servicio no debe cambiar. Si se cambia el nombre del servicio, es posible que se interrumpan otras aplicaciones que usan el servicio.

Para ver el efecto que tiene la propiedad `Process`, en la captura de pantalla siguiente se muestra un servicio que se ejecuta en su propio proceso privado:

![Captura de pantalla que muestra un servicio que se ejecuta en un proceso privado](out-of-process-services-images/ipc-04.png "Captura de pantalla que muestra un servicio que se ejecuta en un proceso privado.")

En la siguiente captura de pantalla se muestra `Process="com.xamarin.xample.messengerservice.timestampservice_process"` y el servicio que se ejecuta en un proceso global:

![Captura de pantalla de un servicio que se ejecuta en un proceso global](out-of-process-services-images/ipc-05.png "Captura de pantalla de un servicio que se ejecuta en un proceso global.")

Una vez establecido el `ServiceAttribute`, el servicio debe implementar un `Handler`.

### <a name="implementing-a-handler"></a>Implementar un controlador

Para procesar las solicitudes de cliente, el servicio debe implementar un `Handler` e invalidar el `HandleMessage` methodThis es el método toma una instancia de `Message` que encapsula la llamada al método desde el cliente y traduce esa llamada en alguna acción o tarea que el servicio va a Form. El objeto `Message` expone una propiedad denominada `What` que es un valor entero, cuyo significado se comparte entre el cliente y el servicio y se relaciona con alguna tarea que el servicio debe realizar para el cliente.

En el siguiente fragmento de código de la aplicación de ejemplo se muestra un ejemplo de `HandleMessage`. En este ejemplo, hay dos acciones que un cliente puede solicitar al servicio:

- La primera acción es un mensaje _Hello, World_ , el cliente ha enviado un mensaje simple al servicio.
- La segunda acción invocará un método en el servicio y recuperará una cadena; en este caso, la cadena es un mensaje que devuelve la hora a la que se inició el servicio y cuánto tiempo se ha estado ejecutando:

```csharp
public class TimestampRequestHandler : Android.OS.Handler
{
    // other code omitted for clarity

    public override void HandleMessage(Message msg)
    {
        int messageType = msg.What;
        Log.Debug(TAG, $"Message type: {messageType}.");

        switch (messageType)
        {
            case Constants.SAY_HELLO_TO_TIMESTAMP_SERVICE:
                // The client as sent a simple Hello, say in the Android Log.
                break;

            case Constants.GET_UTC_TIMESTAMP:
                // Call methods on the service to retrieve a timestamp message.
                break;
            default:
                Log.Warn(TAG, $"Unknown messageType, ignoring the value {messageType}.");
                base.HandleMessage(msg);
                break;
        }
    }
}
```

También es posible empaquetar los parámetros para el servicio en el `Message`. Esto se tratará más adelante en esta guía. El siguiente tema a tener en cuenta es crear el objeto de `Messenger` para procesar el `Message`s entrante.

### <a name="instantiating-the-messenger"></a>Creación de instancias de Messenger

Como se explicó anteriormente, la deserialización del objeto `Message` y la invocación de `Handler.HandleMessage` es responsabilidad del objeto `Messenger`. La clase `Messenger` también proporciona un objeto `IBinder` que el cliente utilizará para enviar mensajes al servicio.  

Cuando se inicia el servicio, crea una instancia del `Messenger` e inserta el `Handler`. Un buen lugar para realizar esta inicialización es en el método `OnCreate` del servicio. Este fragmento de código es un ejemplo de un servicio que inicializa sus propios `Handler` y `Messenger`:

```csharp
private Messenger messenger; // Instance variable for the Messenger

public override void OnCreate()
{
    base.OnCreate();
    messenger = new Messenger(new TimestampRequestHandler(this));
    Log.Info(TAG, $"TimestampService is running in process id {Android.OS.Process.MyPid()}.");
}
```

En este momento, el último paso es que el `Service` invalide `OnBind`.

### <a name="implementing-serviceonbind"></a>Implementación de Service. DataBind

Todos los servicios enlazados, tanto si se ejecutan en su propio proceso como si no, deben implementar el método de `OnBind`. El valor devuelto de este método es algún objeto que el cliente puede utilizar para interactuar con el servicio. Exactamente el objeto depende de si el servicio es un servicio local o remoto. Aunque un servicio local devolverá una implementación de `IBinder` personalizada, un servicio remoto devolverá la `IBinder` encapsulada pero la `Messenger` que se creó en la sección anterior:

```csharp
public override IBinder OnBind(Intent intent)
{
    Log.Debug(TAG, "OnBind");
    return messenger.Binder;
}
```

Una vez que se hayan realizado estos tres pasos, el servicio remoto se puede considerar completo.

## <a name="consuming-the-service"></a>Consumir el servicio

Todos los clientes deben implementar código para poder enlazar y consumir el servicio remoto. Conceptualmente, desde el punto de vista del cliente, hay muy pocas diferencias entre el enlace con el servicio local o un servicio remoto. El cliente invoca el método `BindService`, pasando un intento explícito para identificar el servicio y un `IServiceConnection` que ayuda a administrar la conexión entre el cliente y el servicio.

Este fragmento de código es un ejemplo de cómo crear un **intento explícito** de enlazar a un servicio remoto. La intención debe identificar el paquete que contiene el servicio y el nombre del servicio. Una manera de establecer esta información es usar un objeto `Android.Content.ComponentName` y proporcionarlo a la intención. Este fragmento de código es un ejemplo:  

```csharp
// This is the package name of the APK, set in the Android manifest
const string REMOTE_SERVICE_COMPONENT_NAME = "com.xamarin.TimestampService";
// This is the name of the service, according the value of ServiceAttribute.Name
const string REMOTE_SERVICE_PACKAGE_NAME   = "com.xamarin.xample.messengerservice";

// Provide the package name and the name of the service with a ComponentName object.
ComponentName cn = new ComponentName(REMOTE_SERVICE_PACKAGE_NAME, REMOTE_SERVICE_COMPONENT_NAME);
Intent serviceToStart = new Intent();
serviceToStart.SetComponent(cn);
```

Cuando se enlaza el servicio, se invoca al método `IServiceConnection.OnServiceConnected` y proporciona un `IBinder` a un cliente. Sin embargo, el cliente no usará directamente el `IBinder`. En su lugar, creará una instancia de un objeto `Messenger` a partir de ese `IBinder`. Este es el `Messenger` que el cliente usará para interactuar con el servicio remoto.

A continuación se muestra un ejemplo de una implementación de `IServiceConnection` muy básica que demuestra cómo un cliente administraría la conexión y la desconexión de un servicio. Observe que el método `OnServiceConnected` recibe y `IBinder`, y el cliente crea un `Messenger` a partir de ese `IBinder`:

```csharp
public class TimestampServiceConnection : Java.Lang.Object, IServiceConnection
{
    static readonly string TAG = typeof(TimestampServiceConnection).FullName;

    MainActivity mainActivity;
    Messenger messenger;

    public TimestampServiceConnection(MainActivity activity)
    {
        IsConnected = false;
        mainActivity = activity;
    }

    public bool IsConnected { get; private set; }
    public Messenger Messenger { get; private set; }

    public void OnServiceConnected(ComponentName name, IBinder service)
    {
        Log.Debug(TAG, $"OnServiceConnected {name.ClassName}");

        IsConnected = service != null
        Messenger = new Messenger(service);

        if (IsConnected)
        {
            // things to do when the connection is successful. perhaps notify the client? enable UI features?
        }
        else
        {
            // things to do when the connection isn't successful.
        }
    }

    public void OnServiceDisconnected(ComponentName name)
    {
        Log.Debug(TAG, $"OnServiceDisconnected {name.ClassName}");
        IsConnected = false;
        Messenger = null;

        // Things to do when the service disconnects. perhaps notify the client? disable UI features?

    }
}
```

Una vez que se crea la conexión de servicio y el intento, es posible que el cliente llame a `BindService` e inicie el proceso de enlace:

```csharp
IServiceConnection serviceConnection = new TimestampServiceConnection(this);
BindActivity(serviceToStart, serviceConnection, Bind.AutoCreate);
```

Una vez que el cliente se ha enlazado correctamente al servicio y el `Messenger` está disponible, es posible que el cliente envíe `Messages` al servicio.

## <a name="sending-messages-to-the-service"></a>Envío de mensajes al servicio

Una vez que el cliente está conectado y tiene un objeto `Messenger`, es posible comunicarse con el servicio mediante el envío de objetos de `Message` a través de la `Messenger`. Esta comunicación es unidireccional, el cliente envía el mensaje, pero no se devuelve ningún mensaje del servicio al cliente. En este sentido, el `Message` es un mecanismo de incendio y olvidar.

La manera preferida de crear un objeto `Message` es usar el Factory Method de [`Message.Obtain`](xref:Android.OS.Message) . Este método extrae un objeto de `Message` de un grupo global que se mantiene en Android. `Message.Obtain` también tiene algunos métodos sobrecargados que permiten inicializar el objeto `Message` con los valores y parámetros requeridos por el servicio.  Una vez creada la instancia de `Message`, se envía al servicio llamando a `Messenger.Send`. Este fragmento de código es un ejemplo de creación y distribución de una `Message` al proceso del servicio:

```csharp
Message msg = Message.Obtain(null, Constants.SAY_HELLO_TO_TIMESTAMP_SERVICE);
try
{
    serviceConnection.Messenger.Send(msg);
}
catch (RemoteException ex)
{
    Log.Error(TAG, ex, "There was a error trying to send the message.");
}
```

Hay distintas formas de `Message.Obtain` método. En el ejemplo anterior se usa el [`Message.Obtain(Handler h, Int32 what)`](xref:Android.OS.Message.Obtain). Dado que se trata de una solicitud asincrónica a un servicio fuera de proceso; no habrá respuesta del servicio, por lo que la `Handler` se establece en `null`. El segundo parámetro, `Int32 what`, se almacenará en la propiedad `.What` del objeto `Message`. El código del proceso de servicio utiliza la propiedad `.What` para invocar métodos en el servicio.

La clase `Message` también expone dos propiedades adicionales que pueden ser de uso para el destinatario: `Arg1` y `Arg2`. Estas dos propiedades son valores enteros que pueden tener algunos valores especiales acordados que tienen un significado entre el cliente y el servicio. Por ejemplo, `Arg1` puede contener un identificador de cliente y `Arg2` puede contener un número de pedido de compra para ese cliente. El [`Method.Obtain(Handler h, Int32 what, Int32 arg1, Int32 arg2)`](xref:Android.OS.Message.Obtain) se puede utilizar para establecer las dos propiedades cuando se crea el `Message`. Otra manera de rellenar estos dos valores es establecer las propiedades `.Arg` y `.Arg2` directamente en el objeto `Message` una vez creado.

### <a name="passing-additional-values-to-the-service"></a>Pasar valores adicionales al servicio

Es posible pasar datos más complejos al servicio mediante el uso de un `Bundle`. En este caso, los valores adicionales se pueden colocar en un `Bundle` y enviar junto con el `Message` estableciendo la propiedad de la [propiedad `.Data`](xref:Android.OS.Message.Data) antes de enviarlo.

```csharp
Bundle serviceParameters = new Bundle();
serviceParameters.

var msg = Message.Obtain(null, Constants.SERVICE_TASK_TO_PERFORM);
msg.Data = serviceParameters;

messenger.Send(msg);
```

> [!NOTE]
> En general, un `Message` no debe tener una carga superior a 1 MB. El límite de tamaño puede variar en función de la versión de Android y de cualquier cambio de propietario que el proveedor pueda haber realizado en su implementación del proyecto de código abierto de Android (AOSP) que se incluye con el dispositivo.

## <a name="returning-values-from-the-service"></a>Devolver valores del servicio

La arquitectura de mensajería que se ha explicado en este punto es unidireccional, el cliente envía un mensaje al servicio. Si es necesario que el servicio devuelva un valor a un cliente, se invierte todo lo que se ha analizado hasta este punto. El servicio debe crear un `Message`, empaquetar todos los valores devueltos y enviar el `Message` a través de un `Messenger` al cliente. Sin embargo, el servicio no crea su propio `Messenger`; en su lugar, se basa en el cliente que crea instancias y empaqueta un `Messenger` como parte de la solicitud inicial. El servicio `Send` el mensaje mediante este `Messenger` proporcionado por el cliente.  

La secuencia de eventos para la comunicación bidireccional es la siguiente:

1. El cliente se enlaza al servicio. Cuando el servicio y el cliente se conectan, el `IServiceConnection` que mantiene el cliente tendrá una referencia a un objeto `Messenger` que se utiliza para transmitir `Message`s al servicio. Para evitar confusiones, se hará referencia a este _servicio como Messenger_.
2. El cliente crea una instancia de un `Handler` (denominado _controlador de cliente_) y lo usa para inicializar su propio `Messenger` (el _cliente Messenger_). Tenga en cuenta que el servicio Messenger y el cliente Messenger son dos objetos diferentes que controlan el tráfico en dos direcciones diferentes. El servicio Messenger controla los mensajes del cliente al servicio, mientras que el cliente Messenger controlará los mensajes desde el servicio al cliente.
3. El cliente crea un `Message` objeto y establece la propiedad `ReplyTo` con el cliente Messenger. A continuación, el mensaje se envía al servicio mediante el servicio Messenger.
4. El servicio recibe el mensaje del cliente y realiza el trabajo solicitado.
5. Cuando haya tiempo para que el servicio envíe la respuesta al cliente, usará `Message.Obtain` para crear un nuevo objeto `Message`.
6. Para enviar este mensaje al cliente, el servicio extraerá el Messenger del cliente desde la propiedad `.ReplyTo` del mensaje de cliente y lo utilizará para volver a `.Send` la `Message` al cliente.
7. Cuando el cliente recibe la respuesta, tiene su propio `Handler` que procesará el `Message` inspeccionando la propiedad `.What` (y, si es necesario, extrayendo los parámetros contenidos en el `Message`).

Este código de ejemplo muestra cómo el cliente creará una instancia del `Message` y empaquetará un `Messenger` que el servicio debe usar para su respuesta:

```csharp
Handler clientHandler = new ActivityHandler();
Messenger clientMessenger = new Messenger(activityHandler);

Message msg = Message.Obtain(null, Constants.GET_UTC_TIMESTAMP);
msg.ReplyTo = clientMessenger;

try
{
    serviceConnection.Messenger.Send(msg);
}
catch (RemoteException ex)
{
    Log.Error(TAG, ex, "There was a problem sending the message.");
}
```

El servicio debe realizar algunos cambios en su propio `Handler` para extraer el `Messenger` y usarlo para enviar respuestas al cliente. Este fragmento de código es un ejemplo de cómo el `Handler` del servicio crearía un `Message` y lo devolvería al cliente:  

```csharp
// This is the message that the service will send to the client.
Message responseMessage = Message.Obtain(null, Constants.RESPONSE_TO_SERVICE);
Bundle dataToReturn = new Bundle();
dataToReturn.PutString(Constants.RESPONSE_MESSAGE_KEY, "This is the result from the service.");
responseMessage.Data = dataToReturn;

// The msg object here is the message that was received by the service. The service will not instantiate a client,
// It will use the client that is encapsulated by the message from the client.
Messenger clientMessenger = msg.ReplyTo;
if (clientMessenger!= null)
{
    try
    {
        clientMessenger.Send(responseMessage);
    }
    catch (Exception ex)
    {
        Log.Error(TAG, ex, "There was a problem sending the message.");
    }
}
```

Tenga en cuenta que en los ejemplos de código anteriores, la instancia de `Messenger` que crea el cliente *no* es el mismo objeto que recibe el servicio. Se trata de dos objetos de `Messenger` diferentes que se ejecutan en dos procesos independientes que representan el canal de comunicación.

## <a name="securing-the-service-with-android-permissions"></a>Protección del servicio con permisos de Android

Un servicio que se ejecuta en un proceso global es accesible para todas las aplicaciones que se ejecutan en ese dispositivo Android. En algunas situaciones, esta apertura y disponibilidad no son deseables y es necesario proteger el servicio contra el acceso desde clientes no autorizados. Una manera de limitar el acceso al servicio remoto es usar permisos de Android.

Los permisos se pueden identificar mediante la propiedad `Permission` de la `ServiceAttribute` que decora la subclase `Service`. Esto asignará un nombre a un permiso que se debe conceder al cliente al enlazar con el servicio. Si el cliente no tiene los permisos adecuados, Android producirá una `Java.Lang.SecurityException` cuando el cliente intente enlazarse al servicio.

Hay cuatro niveles de permisos diferentes que Android proporciona:

- **normal** &ndash; este es el nivel de permisos predeterminado. Se usa para identificar los permisos de bajo riesgo que Android puede conceder automáticamente a los clientes que lo soliciten. El usuario no tiene que conceder explícitamente estos permisos, pero los permisos se pueden ver en la configuración de la aplicación.
- **firma** &ndash; es una categoría especial de permiso que Android concederá automáticamente a las aplicaciones firmadas con el mismo certificado. Este permiso se ha diseñado para facilitar a los desarrolladores de aplicaciones el uso compartido de componentes o datos entre sus aplicaciones sin molestar al usuario en las aprobaciones de constantes.
- **signatureOrSystem** &ndash; es muy similar a los permisos de **firma** descritos anteriormente. Además de concederse automáticamente a las aplicaciones firmadas con el mismo certificado, este permiso también se concederá a las aplicaciones que estén firmadas con el mismo certificado que se usó para firmar las aplicaciones instaladas con la imagen del sistema Android. Normalmente, los desarrolladores de Android ROM usan este permiso para permitir que sus aplicaciones funcionen con aplicaciones de terceros. Normalmente, no lo usan las aplicaciones que están diseñadas para la distribución general para el público en gran medida.
- los permisos **peligrosos &ndash; peligrosos** son aquellos que podrían causar problemas al usuario. Por esta razón, los permisos **peligrosos** deben ser aprobados explícitamente por el usuario.

Dado que Android concede permisos de `signature` y `normal` automáticamente en el momento de la instalación, es fundamental que APK que hospedan el servicio se instale **antes** que el APK que contiene el cliente. Si el cliente se instala en primer lugar, Android no concederá los permisos. En este caso, será necesario desinstalar el cliente APK, instalar el servicio APK y, a continuación, volver a instalar el cliente APK.

Hay dos maneras comunes de proteger un servicio con permisos de Android:

1. **Implementar la seguridad de nivel de firma** &ndash; seguridad de nivel de firma significa que el permiso se concede automáticamente a las aplicaciones firmadas con la misma clave que se usó para firmar el APK que contiene el servicio. Esta es una manera sencilla para que los desarrolladores protejan su servicio, pero que los mantienen accesibles desde sus propias aplicaciones. Los permisos de nivel de firma se declaran estableciendo la propiedad `Permission` de la `ServiceAttribute` en `signature`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.TimestampService.timestampservice_process",
             Permission="signature")]
    public class TimestampService : Service
    {
    }
    ```

2. **Cree un permiso personalizado** &ndash; sea posible que el desarrollador del servicio cree un permiso personalizado para el servicio. Esta es la mejor opción cuando un desarrollador desea compartir su servicio con aplicaciones de otros desarrolladores. Un permiso personalizado requiere un poco más de esfuerzo para implementar y se tratará a continuación.

En la sección siguiente se describe un ejemplo simplificado de la creación de un permiso de `normal` personalizado. Para obtener más información sobre los permisos de Android, consulte la documentación de Google para ver los [procedimientos recomendados & seguridad](https://developer.android.com/training/articles/security-tips.html). Para obtener más información sobre los permisos de Android, consulte la [sección permisos](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms) de la documentación de Android del manifiesto de aplicación para obtener más información sobre los permisos de Android.

> [!NOTE]
> En general, [Google desaconseja el uso de permisos personalizados](https://developer.android.com/training/articles/security-tips.html#RequestingPermissions) , ya que pueden resultar confusos para los usuarios.

### <a name="creating-a-custom-permission"></a>Crear un permiso personalizado

Para utilizar un permiso personalizado, el servicio lo declara mientras el cliente solicita explícitamente ese permiso.

Para crear un permiso en el servicio APK, se agrega un elemento `permission` al elemento `manifest` en **archivo AndroidManifest. XML**. Este permiso debe tener establecidos los atributos `name`, `protectionLevel` y `label`. El atributo `name` debe establecerse en una cadena que identifique de forma única el permiso. El nombre se mostrará en la vista **información** de la aplicación de la **configuración de Android** (como se muestra en la sección siguiente).

El atributo `protectionLevel` debe establecerse en uno de los cuatro valores de cadena descritos anteriormente.  Los `label` y `description` deben hacer referencia a los recursos de cadena y se usan para proporcionar al usuario un nombre descriptivo y una descripción para el usuario.

Este fragmento de código es un ejemplo de declaración de un atributo de `permission` personalizado en **archivo AndroidManifest. XML** de APK que contiene el servicio:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="com.xamarin.xample.messengerservice">

    <uses-sdk android:minSdkVersion="21" />

    <permission android:name="com.xamarin.xample.messengerservice.REQUEST_TIMESTAMP"
                android:protectionLevel="signature"
                android:label="@string/permission_label"
                android:description="@string/permission_description"
                />

    <application android:allowBackup="true"
            android:icon="@mipmap/icon"
            android:label="@string/app_name"
            android:theme="@style/AppTheme">

    </application>
</manifest>
```

A continuación, el **archivo AndroidManifest. XML** del cliente apk debe solicitar explícitamente este nuevo permiso. Esto se hace agregando el atributo `users-permission` a **archivo AndroidManifest. XML**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="com.xamarin.xample.messengerclient">

    <uses-sdk android:minSdkVersion="21" />

    <uses-permission android:name="com.xamarin.xample.messengerservice.REQUEST_TIMESTAMP" />

    <application
            android:allowBackup="true"
            android:icon="@mipmap/icon"
            android:label="@string/app_name"
            android:theme="@style/AppTheme">
    </application>
    </manifest>
```

### <a name="view-the-permissions-granted-to-an-app"></a>Ver los permisos concedidos a una aplicación

Para ver los permisos que se han concedido a una aplicación, abra la aplicación de configuración de Android y seleccione **aplicaciones**. Busque y seleccione la aplicación en la lista. En la pantalla de información de la **aplicación** , pulse **permisos** que mostrarán una vista que muestra todos los permisos concedidos a la aplicación:

[![Screenshots desde un dispositivo Android que muestra cómo encontrar los permisos concedidos a una aplicación](out-of-process-services-images/ipc-06-sml.png)](out-of-process-services-images/ipc-06.png#lightbox)

## <a name="summary"></a>Resumen

En esta guía se ha realizado un debate avanzado sobre cómo ejecutar un servicio Android en un proceso remoto. Se han explicado las diferencias entre un servicio local y otro remoto, junto con algunos motivos por los que un servicio remoto puede ser útil para la estabilidad y el rendimiento de una aplicación de Android. Después de explicar cómo implementar un servicio remoto y cómo un cliente puede comunicarse con el servicio, la guía pasó a proporcionar una manera de limitar el acceso al servicio únicamente desde clientes autorizados.

## <a name="related-links"></a>Vínculos relacionados

- [Controlador](xref:Android.OS.Handler)
- [Mensaje](xref:Android.OS.Message)
- [Alerta](xref:Android.OS.Messenger)
- [ServiceAttribute](xref:Android.App.ServiceAttribute)
- [El atributo exportado](https://developer.android.com/guide/topics/manifest/service-element.html#exported)
- [Los servicios con procesos aislados y la clase de aplicación personalizada no pueden resolver sobrecargas correctamente](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)
- [Procesos y subprocesos](https://developer.android.com/guide/components/processes-and-threads.html)
- [Manifiesto de Android: permisos](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)
- [Sugerencias de seguridad](https://developer.android.com/training/articles/security-tips.html)
- [MessengerServiceDemo (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-messengerservicedemo)
