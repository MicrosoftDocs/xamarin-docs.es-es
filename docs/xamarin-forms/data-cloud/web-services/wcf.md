---
title: Consumir un servicio Web de Windows Communication Foundation (WCF)
description: En este artículo se muestra cómo consumir un servicio de Protocolo simple de acceso a objetos (SOAP) de WCF desde una Xamarin.Forms aplicación de.
ms.prod: xamarin
ms.assetid: 5696FF04-EF21-4B7A-8C8B-26DE28B5C0AD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/28/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cee301178880a62695f4208161965b01bb6f522e
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938598"
---
# <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>Consumir un servicio Web de Windows Communication Foundation (WCF)

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)

_WCF es el marco unificado de Microsoft para la creación de aplicaciones orientadas a servicios. Permite a los desarrolladores compilar aplicaciones distribuidas seguras, con transacciones y de confianza, e interoperables. En este artículo se muestra cómo consumir un servicio de Protocolo simple de acceso a objetos (SOAP) de WCF desde una Xamarin.Forms aplicación de._

WCF describe un servicio con varios contratos diferentes, entre los que se incluyen:

- **Contratos de datos** : defina las estructuras de datos que forman la base del contenido de un mensaje.
- **Contratos de mensajes** : redacte mensajes a partir de contratos de datos existentes.
- **Contratos de error** : permite especificar los errores de SOAP personalizados.
- **Contratos de servicio** : especifique las operaciones que admiten los servicios y los mensajes necesarios para interactuar con cada operación. También especifican cualquier comportamiento de error personalizado que se puede asociar a las operaciones en cada servicio.

Hay diferencias entre los servicios Web de ASP.NET (ASMX) y WCF, pero WCF admite las mismas funcionalidades que ASMX proporciona: mensajes SOAP a través de HTTP. Para obtener más información sobre cómo consumir un servicio de ASMX, consulte [consumo de servicios Web de ASP.net (asmx)](~/xamarin-forms/data-cloud/web-services/asmx.md).

> [!IMPORTANT]
> La compatibilidad con la plataforma de Xamarin para WCF se limita a los mensajes SOAP con codificación de texto a través de HTTP/HTTPS mediante la `BasicHttpBinding` clase.
>
> La compatibilidad con WCF requiere el uso de herramientas que solo están disponibles en un entorno de Windows para generar el proxy y hospedar el TodoWCFService. Para compilar y probar la aplicación de iOS, será necesario implementar el TodoWCFService en un equipo Windows o como un servicio Web de Azure.
>
> Normalmente, las aplicaciones nativas de Xamarin Forms comparten código con una biblioteca de clases .NET Standard. Sin embargo, .NET Core no es compatible actualmente con WCF, por lo que el proyecto compartido debe ser una biblioteca de clases portable heredada. Para obtener información sobre la compatibilidad de WCF en .NET Core, vea [elegir entre .net Core y .NET Framework para aplicaciones de servidor](/dotnet/standard/choosing-core-framework-server).

La solución de aplicación de ejemplo incluye un servicio WCF que se puede ejecutar localmente y se muestra en la siguiente captura de pantalla:

![Aplicación de ejemplo](wcf-images/portal.png)

> [!NOTE]
> En iOS 9 y versiones posteriores, la seguridad de transporte de aplicaciones (ATS) exige conexiones seguras entre recursos de Internet (como el servidor back-end de la aplicación) y la aplicación, lo que evita la divulgación accidental de información confidencial. Dado que ATS está habilitado de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estarán sujetas a los requisitos de seguridad de ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
>
> ATS puede no participar en si no es posible usar el `HTTPS` Protocolo y proteger la comunicación de los recursos de Internet. Esto se puede lograr actualizando el archivo **info. plist** de la aplicación. Para obtener más información, vea [seguridad de transporte de aplicaciones](~/ios/app-fundamentals/ats.md).

## <a name="consume-the-web-service"></a>Consumo del servicio web

El servicio WCF proporciona las siguientes operaciones:

|Operación|Descripción|Parámetros|
|--- |--- |--- |
|GetTodoItems|Obtención de una lista de tareas pendientes|
|CreateTodoItem|Crear un nuevo elemento de tareas pendientes|Un TodoItem serializado en XML|
|EditTodoItem|Actualizar una tarea pendiente|Un TodoItem serializado en XML|
|DeleteTodoItem|Eliminar una tarea pendiente|Un TodoItem serializado en XML|

Para obtener más información sobre el modelo de datos que se usa en la aplicación, vea [modelar los datos](~/xamarin-forms/data-cloud/web-services/introduction.md).

Se debe generar un *proxy* para consumir un servicio WCF, lo que permite que la aplicación se conecte al servicio. El proxy se construye mediante el consumo de metadatos de servicio que definen los métodos y la configuración de servicio asociada. Estos metadatos se exponen en forma de un documento de lenguaje de descripción de servicios web (WSDL) generado por el servicio Web. El proxy se puede compilar mediante el Microsoft WCF Web Service Reference Provider en Visual Studio 2017 para agregar una referencia de servicio para el servicio Web a una biblioteca de .NET Standard. Una alternativa a la creación del proxy mediante el Microsoft WCF Web Service Reference Provider en Visual Studio 2017 es usar la herramienta de utilidad de metadatos de ServiceModel (svcutil.exe). Para obtener más información, vea [herramienta de utilidad de metadatos de ServiceModel (Svcutil.exe)](/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe/).

Las clases de proxy generadas proporcionan métodos para consumir los servicios web que usan el modelo de diseño del modelo de programación asincrónica (APM). En este patrón, una operación asincrónica se implementa como dos métodos denominados *BeginOperationName* y *EndOperationName*, que comienzan y finalizan la operación asincrónica.

El método *BeginOperationName* comienza la operación asincrónica y devuelve un objeto que implementa la `IAsyncResult` interfaz. Después de llamar a *BeginOperationName*, una aplicación puede seguir ejecutando instrucciones en el subproceso que realiza la llamada, mientras que la operación asincrónica tiene lugar en un subproceso del grupo de subprocesos.

Para cada llamada a *BeginOperationName*, la aplicación también debe llamar a *EndOperationName* para obtener los resultados de la operación. El valor devuelto de *EndOperationName* es el mismo tipo devuelto por el método de servicio Web sincrónico. Por ejemplo, el `EndGetTodoItems` método devuelve una colección de `TodoItem` instancias de. El método *EndOperationName* también incluye un `IAsyncResult` parámetro que debe establecerse en la instancia devuelta por la llamada correspondiente al método *BeginOperationName* .

La biblioteca TPL (Task Parallel Library) puede simplificar el proceso de consumo de un par de métodos Begin/end de APM encapsulando las operaciones asincrónicas en el mismo `Task` objeto. Esta encapsulación la proporcionan varias sobrecargas del `TaskFactory.FromAsync` método.

Para obtener más información acerca de APM, vea [modelo de programación asincrónica](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) y [TPL y la programación asincrónica .NET Framework tradicional](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) en MSDN.

### <a name="create-the-todoserviceclient-object"></a>Crear el objeto TodoServiceClient

La clase de proxy generada proporciona la `TodoServiceClient` clase, que se usa para comunicarse con el servicio WCF a través de http. Proporciona funcionalidad para invocar métodos de servicio web como operaciones asincrónicas de una instancia de servicio identificada por un identificador URI. Para obtener más información acerca de las operaciones asincrónicas, vea [información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md).

La `TodoServiceClient` instancia de se declara en el nivel de clase para que el objeto se encuentre siempre y cuando la aplicación deba consumir el servicio WCF, como se muestra en el ejemplo de código siguiente:

```csharp
public class SoapService : ISoapService
{
  ITodoService todoService;
  ...

  public SoapService ()
  {
    todoService = new TodoServiceClient (
      new BasicHttpBinding (),
      new EndpointAddress (Constants.SoapUrl));
  }
  ...
}
```

La `TodoServiceClient` instancia se configura con información de enlace y una dirección de extremo. Un enlace se utiliza para especificar los detalles de transporte, codificación y protocolo necesarios para que las aplicaciones y los servicios se comuniquen entre sí. `BasicHttpBinding`Especifica que los mensajes SOAP codificados por texto se enviarán a través del Protocolo de transporte http. La especificación de una dirección de extremo permite que la aplicación se conecte a diferentes instancias del servicio WCF, siempre que haya varias instancias publicadas.

Para obtener más información sobre la configuración de la referencia de servicio, consulte [configuración de la referencia de servicio](~/cross-platform/data-cloud/web-services/index.md#wcf).

### <a name="create-data-transfer-objects"></a>Crear objetos de transferencia de datos

La aplicación de ejemplo utiliza la `TodoItem` clase para modelar los datos. Para almacenar un `TodoItem` elemento en el servicio Web, primero debe convertirse en el tipo generado por el proxy `TodoItem` . Esto se logra mediante el `ToWCFServiceTodoItem` método, tal y como se muestra en el ejemplo de código siguiente:

```csharp
TodoWCFService.TodoItem ToWCFServiceTodoItem (TodoItem item)
{
  return new TodoWCFService.TodoItem
  {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}
```

Este método simplemente crea una nueva `TodoWCFService.TodoItem` instancia de y establece cada propiedad en la propiedad idéntica de la `TodoItem` instancia de.

Del mismo modo, cuando se recuperan datos del servicio Web, se deben convertir del tipo generado por el proxy `TodoItem` a una `TodoItem` instancia de. Esto se logra con el `FromWCFServiceTodoItem` método, como se muestra en el ejemplo de código siguiente:

```csharp
static TodoItem FromWCFServiceTodoItem (TodoWCFService.TodoItem item)
{
  return new TodoItem
  {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}

```

Este método simplemente recupera los datos del tipo generado por el proxy `TodoItem` y los establece en la instancia recién creada `TodoItem` .

### <a name="retrieve-data"></a>Recuperación de datos

Los `TodoServiceClient.BeginGetTodoItems` `TodoServiceClient.EndGetTodoItems` métodos y se usan para llamar a la `GetTodoItems` operación proporcionada por el servicio Web. Estos métodos asincrónicos se encapsulan en un `Task` objeto, tal y como se muestra en el ejemplo de código siguiente:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync <ObservableCollection<TodoWCFService.TodoItem>> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);

  foreach (var item in todoItems)
  {
    Items.Add (FromWCFServiceTodoItem (item));
  }
  ...
}
```

El `Task.Factory.FromAsync` método crea un `Task` que ejecuta el `TodoServiceClient.EndGetTodoItems` método una vez que `TodoServiceClient.BeginGetTodoItems` se completa el método, con el `null` parámetro que indica que no se pasa ningún dato al `BeginGetTodoItems` delegado. Por último, el valor de la `TaskCreationOptions` enumeración especifica que se debe usar el comportamiento predeterminado para la creación y ejecución de tareas.

El `TodoServiceClient.EndGetTodoItems` método devuelve una `ObservableCollection` `TodoWCFService.TodoItem` instancia de, que se convierte a continuación en `List` una `TodoItem` instancia de para su presentación.

### <a name="create-data"></a>Crear datos

Los `TodoServiceClient.BeginCreateTodoItem` `TodoServiceClient.EndCreateTodoItem` métodos y se usan para llamar a la `CreateTodoItem` operación proporcionada por el servicio Web. Estos métodos asincrónicos se encapsulan en un `Task` objeto, tal y como se muestra en el ejemplo de código siguiente:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToWCFServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginCreateTodoItem,
    todoService.EndCreateTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

El `Task.Factory.FromAsync` método crea un `Task` que ejecuta el `TodoServiceClient.EndCreateTodoItem` método una vez que se `TodoServiceClient.BeginCreateTodoItem` completa el método, con el `todoItem` parámetro en los datos que se pasan al `BeginCreateTodoItem` delegado para especificar el `TodoItem` que va a crear el servicio Web. Por último, el valor de la `TaskCreationOptions` enumeración especifica que se debe usar el comportamiento predeterminado para la creación y ejecución de tareas.

El servicio Web inicia una excepción `FaultException` si no puede crear el `TodoItem` control, que es controlado por la aplicación.

### <a name="update-data"></a>Actualización de datos

Los `TodoServiceClient.BeginEditTodoItem` `TodoServiceClient.EndEditTodoItem` métodos y se usan para llamar a la `EditTodoItem` operación proporcionada por el servicio Web. Estos métodos asincrónicos se encapsulan en un `Task` objeto, tal y como se muestra en el ejemplo de código siguiente:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToWCFServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginEditTodoItem,
    todoService.EndEditTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

El `Task.Factory.FromAsync` método crea un `Task` que ejecuta el `TodoServiceClient.EndEditTodoItem` método una vez que `TodoServiceClient.BeginCreateTodoItem` se completa el método, con el `todoItem` parámetro en los datos que se pasan al `BeginEditTodoItem` delegado para especificar el `TodoItem` que va a ser actualizado por el servicio Web. Por último, el valor de la `TaskCreationOptions` enumeración especifica que se debe usar el comportamiento predeterminado para la creación y ejecución de tareas.

El servicio Web inicia una excepción `FaultException` si no puede localizar o actualizar el `TodoItem` control, que es controlado por la aplicación.

### <a name="delete-data"></a>Eliminación de datos

Los `TodoServiceClient.BeginDeleteTodoItem` `TodoServiceClient.EndDeleteTodoItem` métodos y se usan para llamar a la `DeleteTodoItem` operación proporcionada por el servicio Web. Estos métodos asincrónicos se encapsulan en un `Task` objeto, tal y como se muestra en el ejemplo de código siguiente:

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  ...
  await Task.Factory.FromAsync (
    todoService.BeginDeleteTodoItem,
    todoService.EndDeleteTodoItem,
    id,
    TaskCreationOptions.None);
  ...
}
```

El `Task.Factory.FromAsync` método crea un `Task` que ejecuta el `TodoServiceClient.EndDeleteTodoItem` método una vez que se `TodoServiceClient.BeginDeleteTodoItem` completa el método, con el `id` parámetro en los datos que se pasan al `BeginDeleteTodoItem` delegado para especificar el `TodoItem` que va a eliminar el servicio Web. Por último, el valor de la `TaskCreationOptions` enumeración especifica que se debe usar el comportamiento predeterminado para la creación y ejecución de tareas.

El servicio Web inicia una excepción `FaultException` si no puede localizar o eliminar `TodoItem` , que es controlada por la aplicación.

## <a name="configure-remote-access-to-iis-express"></a>Configurar el acceso remoto a IIS Express
En Visual Studio 2017 o Visual Studio 2019, debería poder probar la aplicación de UWP en un equipo sin ninguna configuración adicional. La prueba de clientes iOS y Android puede requerir los pasos adicionales de esta sección. Consulte [conexión a servicios web locales desde simuladores de iOS y emuladores de Android](~/cross-platform/deploy-test/connect-to-local-web-services.md) para obtener más información.

De forma predeterminada, IIS Express solo responderá a las solicitudes a `localhost` . Los dispositivos remotos (como un dispositivo Android, un iPhone o incluso un simulador) no tendrán acceso a su servicio WCF local. Debe conocer la dirección IP de la estación de trabajo de Windows 10 en la red local. En este ejemplo, supongamos que la estación de trabajo tiene la dirección IP `192.168.1.143` . En los pasos siguientes se explica cómo configurar Windows 10 y IIS Express para aceptar conexiones remotas y conectarse al servicio desde un dispositivo físico o virtual:

1. **Agregue una excepción al firewall de Windows**. Debe abrir un puerto a través del firewall de Windows que las aplicaciones de la subred pueden utilizar para comunicarse con el servicio WCF. Cree una regla de entrada que abra el puerto 49393 en el firewall. En un símbolo del sistema administrativo, ejecute este comando:

    ```
    netsh advfirewall firewall add rule name="TodoWCFService" dir=in protocol=tcp localport=49393 profile=private remoteip=localsubnet action=allow
    ```

1. **Configure IIS Express para aceptar conexiones remotas**. Puede configurar IIS Express editando el archivo de configuración de IIS Express en **[directorio de la solución] \.vs\config\applicationhost.config**. Busque el `site` elemento con el nombre `TodoWCFService` . Debe ser similar al siguiente XML:

    ```xml
    <site name="TodoWCFService" id="2">
        <application path="/" applicationPool="Clr4IntegratedAppPool">
            <virtualDirectory path="/" physicalPath="C:\Users\tom\TodoWCF\TodoWCFService\TodoWCFService" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:49393:localhost" />
        </bindings>
    </site>
    ```

    Tendrá que agregar dos `binding` elementos para abrir el puerto 49393 para el tráfico externo y el emulador de Android. El enlace usa un `[IP address]:[port]:[hostname]` formato que especifica el modo en que IIS Express responderá a las solicitudes. Las solicitudes externas tendrán nombres de host que se deben especificar como `binding` . Agregue el siguiente código XML al `bindings` elemento, reemplazando la dirección IP por su propia dirección IP:

    ```xml
    <binding protocol="http" bindingInformation="*:49393:192.168.1.143" />
    <binding protocol="http" bindingInformation="*:49393:127.0.0.1" />
    ```

    Después de los cambios, el `bindings` elemento debe tener un aspecto similar al siguiente:

    ```xml
    <site name="TodoWCFService" id="2">
        <application path="/" applicationPool="Clr4IntegratedAppPool">
            <virtualDirectory path="/" physicalPath="C:\Users\tom\TodoWCF\TodoWCFService\TodoWCFService" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:49393:localhost" />
            <binding protocol="http" bindingInformation="*:49393:192.168.1.143" />
            <binding protocol="http" bindingInformation="*:49393:127.0.0.1" />
        </bindings>
    </site>
    ```

    >[!IMPORTANT]
    >De forma predeterminada, IIS Express no aceptará conexiones de orígenes externos por motivos de seguridad. Para habilitar las conexiones desde dispositivos remotos, debe ejecutar IIS Express con permisos administrativos. La forma más fácil de hacerlo es ejecutar Visual Studio 2017 con permisos administrativos. Esto iniciará IIS Express con permisos administrativos al ejecutar TodoWCFService.

    Una vez completados estos pasos, debería poder ejecutar el TodoWCFService y conectarse desde otros dispositivos de la subred. Para probar esto, ejecute la aplicación y visite `http://localhost:49393/TodoService.svc` . Si recibe un error de **Solicitud incorrecta** al visitar la dirección URL, `bindings` puede que la configuración de IIS Express sea incorrecta (la solicitud está llegando IIS Express pero se está rechazando). Si obtiene un error diferente, puede que la aplicación no se esté ejecutando o que el Firewall esté configurado incorrectamente.

    Para permitir que IIS Express siga ejecutando y atendiendo el servicio, desactive la opción **Editar y continuar** en **las propiedades del proyecto > depuradores de > web**.

1. **Personalice los dispositivos de extremo que se usan para tener acceso al servicio**. Este paso implica la configuración de la aplicación cliente, que se ejecuta en un dispositivo físico o emulado, para tener acceso al servicio WCF.

    El emulador de Android emplea un proxy interno que impide que el emulador acceda directamente a la dirección del equipo host `localhost` . En su lugar, la dirección `10.0.2.2` en el emulador se enruta a `localhost` en el equipo host a través de un proxy interno. Estas solicitudes de proxy tendrán `127.0.0.1` como nombre de host en el encabezado de solicitud, por lo que ha creado el enlace de IIS Express para este nombre de host en los pasos anteriores.

    El simulador de iOS se ejecuta en un host de compilación de Mac, incluso si usa el [simulador de iOS remoto para Windows](~/tools/ios-simulator/index.md). Las solicitudes de red del simulador tendrán la dirección IP de la estación de trabajo en la red local como nombre de host (en este ejemplo `192.168.1.143` , es, pero es probable que la dirección IP real sea diferente). Este es el motivo por el que creó el enlace de IIS Express para este nombre de host en los pasos anteriores.

    Asegúrese de `SoapUrl` que la propiedad del archivo **constants.CS** del proyecto TodoWCF (portable) tenga valores que sean correctos para la red:

    ```csharp
    public static string SoapUrl
    {
        get
        {
            var defaultUrl = "http://localhost:49393/TodoService.svc";

            if (Device.RuntimePlatform == Device.Android)
            {
                defaultUrl = "http://10.0.2.2:49393/TodoService.svc";
            }
            else if (Device.RuntimePlatform == Device.iOS)
            {
                defaultUrl = "http://192.168.1.143:49393/TodoService.svc";
            }

            return defaultUrl;
        }
    }
    ```

    Una vez que haya configurado el **constants.CS** con los puntos de conexión adecuados, podrá conectarse a la TodoWCFService que se ejecuta en la estación de trabajo de Windows 10 desde dispositivos físicos o virtuales.

## <a name="related-links"></a>Vínculos relacionados

- [TodoWCF (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)
- [Creación de un cliente de Windows Communication Foundation](https://docs.microsoft.com/dotnet/framework/wcf/how-to-create-a-wcf-client)
- [Herramienta de utilidad de metadatos de ServiceModel (svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
