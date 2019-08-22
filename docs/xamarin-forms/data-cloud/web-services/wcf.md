---
title: Consumir un servicio Web de Windows Communication Foundation (WCF)
description: En este artículo se muestra cómo consumir un servicio de Protocolo Simple de acceso de objetos (SOAP) de WCF desde una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 5696FF04-EF21-4B7A-8C8B-26DE28B5C0AD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/28/2019
ms.openlocfilehash: 28cb1573262b63cc2b0ccad9f468fe36c682718d
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69888846"
---
# <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>Consumir un servicio Web de Windows Communication Foundation (WCF)

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)

_WCF es el marco unificado de Microsoft para crear aplicaciones orientadas a servicios. Permite a los desarrolladores crear aplicaciones distribuidas seguras, confiables, transacciones e interoperables. En este artículo se muestra cómo consumir un servicio de Protocolo Simple de acceso de objetos (SOAP) de WCF desde una aplicación de Xamarin.Forms._

WCF describe un servicio con varios contratos diferentes, entre los que se incluyen:

- **Los contratos de datos** : definir las estructuras de datos que forman la base para el contenido dentro de un mensaje.
- **Contratos de mensaje** – redactar mensajes de los contratos de datos existente.
- **Contratos de error** : permitir que los errores de SOAP personalizados que se especifique.
- **Contratos de servicio** : especificar las operaciones que admiten servicios y los mensajes necesarios para interactuar con cada operación. También especifique ningún comportamiento de error personalizado que se puede asociar con las operaciones en cada servicio.

Hay diferencias entre los servicios Web de ASP.NET (ASMX) y WCF, pero WCF admite las mismas funcionalidades que ASMX proporciona: mensajes SOAP a través de HTTP. Para obtener más información sobre cómo consumir un servicio de ASMX, consulte [consumo de servicios Web de ASP.net (asmx)](~/xamarin-forms/data-cloud/web-services/asmx.md).

> [!IMPORTANT]
> La compatibilidad con la plataforma de Xamarin para WCF se limita a los mensajes SOAP con codificación de texto a través de `BasicHttpBinding` http/https mediante la clase.
>
> La compatibilidad con WCF requiere el uso de herramientas que solo están disponibles en un entorno de Windows para generar el proxy y hospedar el TodoWCFService. Para compilar y probar la aplicación de iOS, será necesario implementar el TodoWCFService en un equipo Windows o como un servicio Web de Azure.
>
> Normalmente, las aplicaciones nativas de Xamarin Forms comparten código con una biblioteca de clases .NET Standard. Sin embargo, .NET Core no es compatible actualmente con WCF, por lo que el proyecto compartido debe ser una biblioteca de clases portable heredada. Para obtener información sobre la compatibilidad de WCF en .NET Core, vea [elegir entre .net Core y .NET Framework para aplicaciones de servidor](/dotnet/standard/choosing-core-framework-server).

La solución de aplicación de ejemplo incluye un servicio WCF que se puede ejecutar localmente y se muestra en la siguiente captura de pantalla:

![](wcf-images/portal.png "Aplicación de ejemplo")

> [!NOTE]
> En iOS 9 y versiones posteriores, App Transport Security (ATS) exige que las conexiones seguras entre los recursos de internet (por ejemplo, el servidor back-end de la aplicación) y la aplicación, lo que impide la divulgación accidental de información confidencial. Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estará sujeto a los requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
>
> Se puede optar por en ATS de si no es posible usar la `HTTPS` del protocolo y proteger la comunicación de los recursos de internet. Esto puede lograrse mediante la actualización de la aplicación **Info.plist** archivo. Para obtener más información, consulte [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="consume-the-web-service"></a>Consumo del servicio Web

El servicio WCF proporciona las siguientes operaciones:

|Operación|Descripción|Parámetros|
|--- |--- |--- |
|GetTodoItems|Obtener una lista de tareas pendientes|
|CreateTodoItem|Crear una nueva tarea pendiente|Un documento XML serializado TodoItem|
|EditTodoItem|Actualizar una tarea pendiente|Un documento XML serializado TodoItem|
|DeleteTodoItem|Eliminar una tarea pendiente|Un documento XML serializado TodoItem|

Para obtener más información sobre el modelo de datos utilizado en la aplicación, consulte [los datos de modelado](~/xamarin-forms/data-cloud/web-services/introduction.md).

Un *proxy* debe generarse para consumir un servicio WCF, que permite que la aplicación para conectarse al servicio. El proxy se construye por consumir los metadatos del servicio que definen los métodos y la configuración del servicio asociado. Estos metadatos se muestran en forma de un documento de lenguaje de descripción de servicios Web (WSDL) que se genera el servicio web. El proxy puede compilarse mediante el Microsoft WCF Web Service Reference Provider en Visual Studio 2017 para agregar una referencia de servicio para el servicio web en una biblioteca de .NET Standard. Una alternativa para crear al proxy con el Microsoft WCF Web Service Reference Provider en Visual Studio 2017 consiste en utilizar ServiceModel Metadata Utility Tool (svcutil.exe). Para obtener más información, consulte [ServiceModel Metadata Utility Tool (Svcutil.exe)](/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe/).

Las clases de proxy generadas proporcionan métodos para consumir los servicios web que usan el patrón de diseño del modelo de programación asincrónica (APM). En este patrón, una operación asincrónica se implementa como dos métodos denominados *BeginOperationName* y *EndOperationName*, que comienzan y terminan la operación asincrónica.

El *BeginOperationName* método comienza la operación asincrónica y devuelve un objeto que implementa el `IAsyncResult` interfaz. Después de llamar a *BeginOperationName*, una aplicación puede seguir ejecutando instrucciones en el subproceso de llamada mientras lleva a la operación asincrónica en un subproceso ThreadPool.

Para cada llamada a *BeginOperationName*, también debe llamar la aplicación *EndOperationName* para obtener los resultados de la operación. El valor devuelto de *EndOperationName* es el mismo tipo devuelto por el método de servicio web sincrónico. Por ejemplo, el `EndGetTodoItems` método devuelve una colección de `TodoItem` instancias. El *EndOperationName* método también incluye un `IAsyncResult` parámetro que se debe establecer en la instancia devuelta por la llamada correspondiente a la *BeginOperationName* método.

Task Parallel Library (TPL) puede simplificar el proceso de consumo de un par de métodos begin/end APM al encapsular las operaciones asincrónicas en la misma `Task` objeto. Esta encapsulación proporciona varias sobrecargas de los `TaskFactory.FromAsync` método.

Para obtener más información acerca de APM vea [modelo de programación asincrónica](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) y [TPL y la programación tradicional de .NET Framework asincrónicas](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) en MSDN.

### <a name="create-the-todoserviceclient-object"></a>Crear el objeto TodoServiceClient

La clase de proxy generado proporciona el `TodoServiceClient` (clase), que se usa para comunicarse con el servicio WCF a través de HTTP. Proporciona funcionalidad para invocar métodos de servicio web como operaciones asincrónicas de un URI identifican la instancia de servicio. Para obtener más información acerca de las operaciones asincrónicas, vea [información general de soporte técnico de Async](~/cross-platform/platform/async.md).

El `TodoServiceClient` instancia se declara en el nivel de clase para que se encuentra el objeto para siempre y cuando la aplicación necesita consumir el servicio WCF, tal como se muestra en el ejemplo de código siguiente:

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

El `TodoServiceClient` instancia está configurada con una dirección de extremo y la información de enlace. Un enlace se utiliza para especificar el transporte, codificación y detalles protocolares requeridos para que las aplicaciones y servicios para comunicarse entre sí. El `BasicHttpBinding` especifica que se enviarán los mensajes codificados en texto SOAP a través del protocolo de transporte HTTP. Especificación de una dirección de punto de conexión permite a la aplicación para conectarse a distintas instancias del servicio WCF, siempre que hay varias instancias publicadas.

Para obtener más información sobre la configuración de la referencia de servicio, consulte [configuración de la referencia de servicio](~/cross-platform/data-cloud/web-services/index.md#wcf).

### <a name="create-data-transfer-objects"></a>Crear objetos de transferencia de datos

La aplicación de ejemplo usa el `TodoItem` clase para modelar los datos. Para almacenar un `TodoItem` elemento en el servicio web debe convertirse primero en el proxy generado `TodoItem` tipo. Esto se consigue mediante la `ToWCFServiceTodoItem` método, como se muestra en el ejemplo de código siguiente:

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

Este método simplemente crea un nuevo `TodoWCFService.TodoItem` de instancia y establece cada propiedad a la propiedad idéntica desde el `TodoItem` instancia.

De forma similar, cuando se recuperan datos desde el servicio web, se debe convertir desde el proxy generado `TodoItem` tipo a un `TodoItem` instancia. Esto se consigue con la `FromWCFServiceTodoItem` método, como se muestra en el ejemplo de código siguiente:

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

Este método simplemente recupera los datos desde el proxy generado `TodoItem` escriba y lo establece en recién creado `TodoItem` instancia.

### <a name="retrieve-data"></a>Recuperación de datos

El `TodoServiceClient.BeginGetTodoItems` y `TodoServiceClient.EndGetTodoItems` métodos se usan para llamar a la `GetTodoItems` operación proporcionada por el servicio web. Estos métodos asincrónicos se encapsulan en un `Task` de objeto, como se muestra en el ejemplo de código siguiente:

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

El `Task.Factory.FromAsync` método crea un `Task` que se ejecuta el `TodoServiceClient.EndGetTodoItems` método una vez el `TodoServiceClient.BeginGetTodoItems` método se completa, con el `null` parámetro que indica que no hay datos se pasan a la `BeginGetTodoItems` delegar. Por último, el valor de la `TaskCreationOptions` enumeración especifica que debe usarse el comportamiento predeterminado para la creación y ejecución de tareas.

El `TodoServiceClient.EndGetTodoItems` método devuelve un `ObservableCollection` de `TodoWCFService.TodoItem` instancias, que, a continuación, se convierte en un `List` de `TodoItem` instancias para su presentación.

### <a name="create-data"></a>Crear datos

El `TodoServiceClient.BeginCreateTodoItem` y `TodoServiceClient.EndCreateTodoItem` métodos se usan para llamar a la `CreateTodoItem` operación proporcionada por el servicio web. Estos métodos asincrónicos se encapsulan en un `Task` de objeto, como se muestra en el ejemplo de código siguiente:

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

El `Task.Factory.FromAsync` método crea un `Task` que se ejecuta el `TodoServiceClient.EndCreateTodoItem` método una vez el `TodoServiceClient.BeginCreateTodoItem` método se completa, con el `todoItem` parámetro que se va a los datos que se pasan a la `BeginCreateTodoItem` delegado para especificar el `TodoItem` que va a crear el servicio web. Por último, el valor de la `TaskCreationOptions` enumeración especifica que debe usarse el comportamiento predeterminado para la creación y ejecución de tareas.

El servicio web inicia una `FaultException` si se produce un error al crear el `TodoItem`, que se controla mediante la aplicación.

### <a name="update-data"></a>Actualizar datos

El `TodoServiceClient.BeginEditTodoItem` y `TodoServiceClient.EndEditTodoItem` métodos se usan para llamar a la `EditTodoItem` operación proporcionada por el servicio web. Estos métodos asincrónicos se encapsulan en un `Task` de objeto, como se muestra en el ejemplo de código siguiente:

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

El `Task.Factory.FromAsync` método crea un `Task` que se ejecuta el `TodoServiceClient.EndEditTodoItem` método una vez el `TodoServiceClient.BeginCreateTodoItem` método se completa, con el `todoItem` parámetro que se va a los datos que se pasan a la `BeginEditTodoItem` delegado para especificar el `TodoItem` actualizarse por el servicio web. Por último, el valor de la `TaskCreationOptions` enumeración especifica que debe usarse el comportamiento predeterminado para la creación y ejecución de tareas.

El servicio web inicia una `FaultException` si se produce un error al buscar o actualizar el `TodoItem`, que se controla mediante la aplicación.

### <a name="delete-data"></a>Eliminar datos

El `TodoServiceClient.BeginDeleteTodoItem` y `TodoServiceClient.EndDeleteTodoItem` métodos se usan para llamar a la `DeleteTodoItem` operación proporcionada por el servicio web. Estos métodos asincrónicos se encapsulan en un `Task` de objeto, como se muestra en el ejemplo de código siguiente:

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

El `Task.Factory.FromAsync` método crea un `Task` que se ejecuta el `TodoServiceClient.EndDeleteTodoItem` método una vez el `TodoServiceClient.BeginDeleteTodoItem` método se completa, con el `id` parámetro que se va a los datos que se pasan a la `BeginDeleteTodoItem` delegado para especificar el `TodoItem` va a eliminar el servicio web. Por último, el valor de la `TaskCreationOptions` enumeración especifica que debe usarse el comportamiento predeterminado para la creación y ejecución de tareas.

El servicio web inicia una `FaultException` si se produce un error al buscar o eliminar el `TodoItem`, que se controla mediante la aplicación.

## <a name="configure-remote-access-to-iis-express"></a>Configurar el acceso remoto a IIS Express
En Visual Studio 2017 o Visual Studio 2019, debería poder probar la aplicación de UWP en un equipo sin ninguna configuración adicional. La prueba de clientes iOS y Android puede requerir los pasos adicionales de esta sección. Consulte [conexión a servicios web locales desde simuladores de iOS y emuladores de Android](~/cross-platform/deploy-test/connect-to-local-web-services.md) para obtener más información.

De forma predeterminada, IIS Express solo responderá a las `localhost`solicitudes a. Los dispositivos remotos (como un dispositivo Android, un iPhone o incluso un simulador) no tendrán acceso a su servicio WCF local. Debe conocer la dirección IP de la estación de trabajo de Windows 10 en la red local. En este ejemplo, supongamos que la estación de trabajo tiene la dirección `192.168.1.143`IP. En los pasos siguientes se explica cómo configurar Windows 10 y IIS Express para aceptar conexiones remotas y conectarse al servicio desde un dispositivo físico o virtual:

1. **Agregue una excepción al firewall de Windows**. Debe abrir un puerto a través del firewall de Windows que las aplicaciones de la subred pueden utilizar para comunicarse con el servicio WCF. Cree una regla de entrada que abra el puerto 49393 en el firewall. En un símbolo del sistema administrativo, ejecute este comando:

    ```
    netsh advfirewall firewall add rule name="TodoWCFService" dir=in protocol=tcp localport=49393 profile=private remoteip=localsubnet action=allow
    ```

1. **Configure IIS Express para aceptar conexiones remotas**. Puede configurar IIS Express editando el archivo de configuración de IIS Express en **[directorio de la\.solución] vs\config\applicationhost.config**. Busque el `site` elemento con el nombre `TodoWCFService`. Debe ser similar al siguiente XML:

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

    Tendrá que agregar dos `binding` elementos para abrir el puerto 49393 para el tráfico externo y el emulador de Android. El enlace usa un `[IP address]:[port]:[hostname]` formato que especifica el modo en que IIS Express responderá a las solicitudes. Las solicitudes externas tendrán nombres de host que se deben especificar como `binding`. Agregue el siguiente código XML al `bindings` elemento, reemplazando la dirección IP por su propia dirección IP:

    ```xml
    <binding protocol="http" bindingInformation="*:49393:192.168.1.143" />
    <binding protocol="http" bindingInformation="*:49393:127.0.0.1" />
    ```

    Después de los cambios `bindings` , el elemento debe tener un aspecto similar al siguiente:

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

    Una vez completados estos pasos, debería poder ejecutar el TodoWCFService y conectarse desde otros dispositivos de la subred. Para probar esto, ejecute la aplicación y visite `http://localhost:49393/TodoService.svc`. Si recibe un error de **Solicitud incorrecta** al visitar la dirección URL, `bindings` puede que la configuración de IIS Express sea incorrecta (la solicitud está llegando IIS Express pero se está rechazando). Si obtiene un error diferente, puede que la aplicación no se esté ejecutando o que el Firewall esté configurado incorrectamente.

    Para permitir que IIS Express siga ejecutando y atendiendo el servicio, desactive la opción **Editar y continuar** en **las propiedades del proyecto > depuradores de > Web**.

1. **Personalice los dispositivos de extremo que se usan para tener acceso al servicio**. Este paso implica la configuración de la aplicación cliente, que se ejecuta en un dispositivo físico o emulado, para tener acceso al servicio WCF.

    El emulador de Android emplea un proxy interno que impide que el emulador acceda directamente a la dirección `localhost` del equipo host. En su lugar, la `10.0.2.2` dirección en el emulador se enruta `localhost` a en el equipo host a través de un proxy interno. Estas solicitudes `127.0.0.1` de proxy tendrán como nombre de host en el encabezado de solicitud, por lo que ha creado el enlace de IIS Express para este nombre de host en los pasos anteriores.

    El simulador de iOS se ejecuta en un host de compilación de Mac, incluso si usa el [simulador de iOS remoto para Windows](~/tools/ios-simulator/index.md). Las solicitudes de red del simulador tendrán la dirección IP de la estación de trabajo en la red local como nombre de `192.168.1.143`host (en este ejemplo, es, pero es probable que la dirección IP real sea diferente). Este es el motivo por el que creó el enlace de IIS Express para este nombre de host en los pasos anteriores.

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
- [Procedimientos: Creación de un cliente de Windows Communication Foundation](https://docs.microsoft.com/dotnet/framework/wcf/how-to-create-a-wcf-client)
- [Herramienta de utilidad de metadatos de ServiceModel (SvcUtil. exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
