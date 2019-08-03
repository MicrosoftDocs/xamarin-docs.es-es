---
title: Azure Signalr Service con Xamarin. Forms
description: Introducción a Azure Signalr Service y Azure Functions con Xamarin. Forms
ms.prod: xamarin
ms.assetid: 1B9A69EF-C200-41BF-B098-D978D7F9CD8F
author: profexorgeek
ms.author: jusjohns
ms.date: 06/07/2019
ms.openlocfilehash: a4d0f5c5ceefcfe9a36a5fcf10c6fb4937c1db90
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739212"
---
# <a name="azure-signalr-service-with-xamarinforms"></a>Azure Signalr Service con Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azuresignalr/)

ASP.NET Core Signalr es un modelo de aplicación que simplifica el proceso de agregar comunicación en tiempo real a las aplicaciones. Azure Signalr Service permite el desarrollo y la implementación rápidos de aplicaciones de Signalr escalables. Azure Functions son métodos de código sin servidor de corta duración que se pueden combinar para formar aplicaciones escalables orientadas a eventos.

En este artículo y en el ejemplo se muestra cómo combinar Azure Signalr Service y Azure Functions con Xamarin. Forms para ofrecer mensajes en tiempo real a los clientes conectados.

## <a name="create-an-azure-signalr-service-and-azure-functions-app"></a>Creación de un servicio de Azure Signalr y Azure Functions aplicación

La aplicación de ejemplo consta de tres componentes clave: un concentrador de servicio de Azure Signalr, una instancia de Azure Functions con dos funciones y una aplicación móvil que puede enviar y recibir mensajes. Estos componentes interactúan de la siguiente manera:

1. La aplicación móvil invoca una función **Negotiate** de Azure para obtener información sobre el concentrador signalr.
1. La aplicación móvil usa la información de negociación para registrarse en el concentrador de Signalr y forma una conexión.
1. Después del registro, la aplicación móvil envía mensajes a la función **Talk** de Azure.
1. La función **Talk** pasa el mensaje entrante al concentrador signalr.
1. Signalr Hub difunde el mensaje a todas las instancias de aplicaciones móviles conectadas, incluido el remitente original.

> [!NOTE]
> Las funciones **Negotiate** y **Talk** de la aplicación de ejemplo se pueden ejecutar localmente mediante Visual Studio 2019 y las herramientas en tiempo de ejecución de Azure. Sin embargo, el servicio Azure Signalr no se puede emular localmente y es difícil exponer Azure Functions hospedadas localmente a dispositivos físicos o virtuales para realizar pruebas. Se recomienda implementar el Azure Functions en una instancia de la aplicación Azure Functions, ya que esto permite realizar pruebas entre plataformas. Para obtener información detallada sobre la implementación, vea [implementar Azure Functions con Visual Studio 2019](#deploy-azure-functions-with-visual-studio-2019).

### <a name="create-an-azure-signalr-service"></a>Creación de un servicio de Azure Signalr

Para crear un servicio de Azure Signalr, elija **crear un recurso** en la esquina superior izquierda del Azure portal y busque **signalr**. Azure Signalr Service se puede crear en el nivel gratis. Azure Signalr Service debe estar en modo de servicio sin **servidor** . Si elige accidentalmente el modo de servicio predeterminado o clásico, puede cambiarlo más adelante en las propiedades del servicio Azure Signalr.

En la captura de pantalla siguiente se muestra la creación de un nuevo servicio de Azure Signalr:

![Captura de pantalla de la creación del servicio Azure signalr en el Azure portal](azure-signalr-images/azure-signalr-create.png "Creación de un servicio de Azure signalr")

Una vez creada, la sección de **claves** de un servicio de Azure signalr contiene una **cadena de conexión**, que se usa para conectar la aplicación Azure Functions a la central signalr. En la captura de pantalla siguiente se muestra dónde encontrar la cadena de conexión en el servicio Azure Signalr:

![Captura de pantalla de la cadena de conexión de Azure signalr en el Azure portal](azure-signalr-images/azure-signalr-connection-string.png "Cadena de conexión de Azure signalr")

Esta cadena de conexión se usa para [implementar Azure Functions con Visual Studio 2019](#deploy-azure-functions-with-visual-studio-2019).

### <a name="create-an-azure-functions-app"></a>Creación de una aplicación Azure Functions

Para probar la aplicación de ejemplo, debe crear una nueva Azure Functions aplicación en el Azure Portal. Anote el nombre de la **aplicación** , ya que esta dirección URL se usa en el archivo **constants.CS** de la aplicación de ejemplo. En la captura de pantalla siguiente se muestra la creación de una nueva Azure Functions aplicación denominada "xdocsfunctions":

[![Captura de pantalla de la creación de Azure Functions aplicación](azure-signalr-images/azure-functions-app-cropped.png)](azure-signalr-images/azure-functions-app-full.png#lightbox)

Azure Functions se puede implementar en una instancia de la aplicación Azure Functions desde Visual Studio 2019. En las secciones siguientes se describe la implementación de dos funciones en la aplicación de ejemplo en una instancia de la aplicación Azure Functions.

### <a name="build-azure-functions-in-visual-studio-2019"></a>Compilar Azure Functions en Visual Studio 2019

La aplicación de ejemplo contiene una biblioteca de clases denominada **ChatServer**, que incluye dos Azure Functions sin servidor en archivos denominados **Negotiate.CS** y **Talk.CS**.

La `Negotiate` función responde a las solicitudes web con un `SignalRConnectionInfo` objeto que contiene `AccessToken` una propiedad y una `Url` propiedad. La aplicación móvil usa estos valores para registrarse en el concentrador Signalr. En el código siguiente se `Negotiate` muestra la función:

```csharp
[FunctionName("Negotiate")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous,"get",Route = "negotiate")]
    HttpRequest req,
    [SignalRConnectionInfo(HubName = "simplechat")]
    SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

La `Talk` función responde a las solicitudes HTTP post que proporcionan un objeto de mensaje en el cuerpo de post. El cuerpo de la publicación se transforma `SignalRMessage` en un y se reenvía a la central de signalr. En el código siguiente se `Talk` muestra la función:

```csharp
[FunctionName("Talk")]
public static async Task<IActionResult> Run(
    [HttpTrigger(
        AuthorizationLevel.Anonymous,
        "post",
        Route = "talk")]
    HttpRequest req,
    [SignalR(HubName = "simplechat")]
    IAsyncCollector<SignalRMessage> questionR,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    try
    {
        string json = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic obj = JsonConvert.DeserializeObject(json);

        var name = obj.name.ToString();
        var text = obj.text.ToString();

        var jObject = new JObject(obj);

        await questionR.AddAsync(
            new SignalRMessage
            {
                Target = "newMessage",
                Arguments = new[] { jObject }
            });

        return new OkObjectResult($"Hello {name}, your message was '{text}'");
    }
    catch (Exception ex)
    {
        return new BadRequestObjectResult("There was an error: " + ex.Message);
    }
}
```

Para más información sobre Azure functions y Azure Functions aplicaciones, consulte la [documentación de Azure Functions](/azure/azure-functions/).

### <a name="deploy-azure-functions-with-visual-studio-2019"></a>Implementación de Azure Functions con Visual Studio 2019

Visual Studio 2019 le permite implementar funciones en una aplicación Azure Functions. Las funciones hospedadas en Azure facilitan las pruebas entre plataformas al proporcionar un punto de conexión de prueba accesible para todos los dispositivos.

Al hacer clic con el botón derecho en la aplicación de funciones de ejemplo y elegir **publicar** , se inicia el cuadro de diálogo para publicar funciones en la aplicación Azure functions. Si siguió los pasos anteriores para configurar una Function App de Azure, puede elegir **seleccionar existente** para publicar las aplicaciones de ejemplo en la aplicación Azure functions. En la captura de pantalla siguiente se muestran las opciones del cuadro de diálogo publicar en Visual Studio 2019:

![Cuadro de diálogo Opciones de publicación en Visual Studio 2019](azure-signalr-images/vs-publish-target.png "Opciones de publicación en Visual Studio 2019")

Una vez que haya iniciado sesión en el cuenta de Microsoft, puede buscar y elegir la aplicación Azure Functions como el destino de publicación. En la captura de pantalla siguiente se muestra un ejemplo de aplicación Azure Functions en el cuadro de diálogo de publicación de Visual Studio 2019:

![Una aplicación Azure Functions en el cuadro de diálogo de publicación de Visual Studio 2019](azure-signalr-images/vs-app-selection.png "Cuadro de diálogo de publicación de Azure Functions aplicación en Visual Studio 2019")

Después de seleccionar una instancia de la aplicación de Azure Functions, se muestran la dirección URL del sitio, la configuración y otra información sobre la aplicación de Azure Functions de destino. Elija **Editar configuración de Azure App Service** y escriba la cadena de conexión en el campo **remoto** . Las funciones **Negotiate** y **Talk** usan la cadena de conexión para conectarse al servicio Azure signalr y está disponible en la sección **claves** del servicio Azure signalr en el Azure portal. Para más información sobre la cadena de conexión, consulte [creación de un servicio de Azure signalr](#create-an-azure-signalr-service).

Una vez que haya escrito la cadena de conexión, puede hacer clic en **publicar** para implementar las funciones en la aplicación Azure functions. Una vez que haya finalizado, las funciones se enumerarán en la aplicación Azure Functions en el Azure Portal. En la captura de pantalla siguiente se muestran las funciones publicadas en el Azure Portal:

![Funciones publicadas en la aplicación Azure Functions](azure-signalr-images/azure-functions-deployed.png "Funciones publicadas en la aplicación Azure Functions")

## <a name="integrate-azure-signalr-service-with-xamarinforms"></a>Integración del servicio Azure Signalr con Xamarin. Forms

La integración entre Azure signalr Service y la aplicación de Xamarin. Forms es una clase de servicio de signalr de la `MainPage` que se crean instancias en la clase con controladores de eventos asignados a tres eventos. Para obtener más información sobre estos controladores de eventos, vea [uso de la clase signalr Service en Xamarin. Forms](#use-the-signalr-service-class-in-xamarinforms).

La aplicación de ejemplo incluye una clase **constants.CS** que se debe personalizar con el punto de conexión de la dirección URL de la aplicación Azure functions. Establezca el valor de la `HostName` propiedad en la dirección de la aplicación Azure functions. En el código siguiente se muestran las propiedades de constants.CS `HostName` con un valor de ejemplo:

```csharp
public static class Constants
{
    public static string HostName { get; set; } = "https://example-functions-app.azurewebsites.net/";

    // Used to differentiate message types sent via SignalR. This
    // sample only uses a single message type.
    public static string MessageName { get; set; } = "newMessage";

    public static string Username
    {
        get
        {
            return $"{Device.RuntimePlatform} User";
        }
    }
}
```

> [!NOTE]
> La `Username` propiedad del archivo **constants.CS** de la aplicación de ejemplo usa el `RuntimePlatform` valor del dispositivo como nombre de usuario. Esto facilita la prueba de dispositivos entre plataformas e identifica el dispositivo que envía el mensaje. En una aplicación real, este valor podría ser un nombre de usuario único, que se recopila durante un proceso de registro o inicio de sesión.

### <a name="the-signalr-service-class"></a>La clase de servicio Signalr

La `SignalRService` clase del proyecto **ChatClient** en la aplicación de ejemplo muestra una implementación que invoca funciones en una aplicación Azure Functions para conectarse a un servicio de Azure signalr.

El `SendMessageAsync` método de la `SignalRService` clase se usa para enviar mensajes a los clientes conectados al servicio Azure signalr. Este método realiza una solicitud HTTP POST a la función **Talk** hospedada en la aplicación de Azure Functions, incluido un `Message` objeto serializado en JSON como carga posterior. La función **Talk** pasa el mensaje al servicio Azure signalr para difundirlo a todos los clientes conectados. En el código siguiente se muestra el método `SendMessageAsync`:

```csharp
public async Task SendMessageAsync(string username, string message)
{
    IsBusy = true;

    var newMessage = new Message
    {
        Name = username,
        Text = message
    };

    var json = JsonConvert.SerializeObject(newMessage);
    var content = new StringContent(json, Encoding.UTF8, "application/json");
    var result = await client.PostAsync($"{Constants.HostName}/api/talk", content);

    IsBusy = false;
}
```

El `ConnectAsync` método de la `SignalRService` clase realiza una solicitud HTTP GET a la función **Negotiate** hospedada en la aplicación Azure functions. La función **Negotiate** devuelve JSON que se deserializa en una instancia de la `NegotiateInfo` clase. Una vez `NegotiateInfo` que se recupera el objeto, se usa para registrarse directamente con el servicio Azure signalr mediante una instancia de `HubConnection` la clase.

ASP.NET Core Signalr traduce los datos entrantes de la conexión abierta a los mensajes y permite a los desarrolladores definir tipos de mensajes y enlazar los controladores de eventos a los mensajes entrantes por tipo. El `ConnectAsync` método registra un controlador de eventos para el nombre del mensaje definido en el archivo **constants.CS** de la aplicación de ejemplo, que es "newMessage" de forma predeterminada.

En el código siguiente se muestra el método `ConnectAsync`:

```csharp
public async Task ConnectAsync()
{
    try
    {
        IsBusy = true;

        string negotiateJson = await client.GetStringAsync($"{Constants.HostName}/api/negotiate");
        NegotiateInfo negotiate = JsonConvert.DeserializeObject<NegotiateInfo>(negotiateJson);
        HubConnection connection = new HubConnectionBuilder()
            .WithUrl(negotiate.Url, options =>
            {
                options.AccessTokenProvider = async () => negotiate.AccessToken;
            })
            .Build();

        connection.On<JObject>(Constants.MessageName, AddNewMessage);
        await connection.StartAsync();

        IsConnected = true;
        IsBusy = false;

        Connected?.Invoke(this, true, "Connection successful.");
    }
    catch (Exception ex)
    {
        ConnectionFailed?.Invoke(this, false, ex.Message);
    }
}
```

El `AddNewMessage` método se enlaza como el controlador de eventos en `ConnectAsync` el mensaje, tal como se muestra en el código anterior. Cuando se recibe un mensaje, se `AddNewMessage` llama al método con los datos del mensaje proporcionados `JObject`como. El `AddNewMessage` método convierte `JObject` en una instancia de la `Message` clase y, a continuación, invoca el controlador para `NewMessageReceived` si se ha enlazado uno. En el código siguiente se muestra el método `AddNewMessage`:

```csharp
public void AddNewMessage(JObject message)
{
    Message messageModel = new Message
    {
        Name = message.GetValue("name").ToString(),
        Text = message.GetValue("text").ToString(),
        TimeReceived = DateTime.Now
    };

    NewMessageReceived?.Invoke(this, messageModel);
}
```

### <a name="use-the-signalr-service-class-in-xamarinforms"></a>Uso de la clase Signalr Service en Xamarin. Forms

El uso de la clase signalr Service en Xamarin. Forms se logra enlazando los `SignalRService` eventos de clase en la `MainPage` clase de código subyacente.

El `Connected` evento de la `SignalRService` clase se activa cuando se completa correctamente una conexión de signalr. El `ConnectionFailed` evento de la `SignalRService` clase se activa cuando se produce un error en una conexión de signalr. El `SignalR_ConnectionChanged` método de control de eventos se enlaza a ambos eventos `MainPage` en el constructor. Este controlador de eventos actualiza los Estados de los botones Connect y Send según `success` el argumento Connection y agrega el mensaje proporcionado por el evento a la cola de chat `AddMessage` mediante el método. En el código siguiente se `SignalR_ConnectionChanged` muestra el método de control de eventos:

```csharp
void SignalR_ConnectionChanged(object sender, bool success, string message)
{
    connectButton.Text = "Connect";
    connectButton.IsEnabled = !success;
    sendButton.IsEnabled = success;

    AddMessage($"Server connection changed: {message}");
}
```

El `NewMessageReceived` evento de la `SignalRService` clase se activa cuando se recibe un nuevo mensaje del servicio Azure signalr. El `SignalR_NewMessageReceived` método de control de eventos se enlaza `NewMessageReceived` al evento en `MainPage` el constructor. Este controlador de eventos convierte el objeto `Message` entrante en una cadena y lo agrega a la cola de chat mediante `AddMessage` el método. En el código siguiente se `SignalR_NewMessageReceived` muestra el método de control de eventos:

```csharp
void SignalR_NewMessageReceived(object sender, Model.Message message)
{
    string msg = $"{message.Name} ({message.TimeReceived}) - {message.Text}";
    AddMessage(msg);
}
```

El `AddMessage` método agrega un nuevo mensaje como un `Label` objeto a la cola de chat. A `AddMessage` menudo, los controladores de eventos llaman al método desde fuera del subproceso principal de la interfaz de usuario, por lo que obliga a que las actualizaciones de la interfaz de usuario se realicen en el subproceso principal para evitar excepciones. En el código siguiente se muestra el método `AddMessage`:

```csharp
void AddMessage(string message)
{
    Device.BeginInvokeOnMainThread(() =>
    {
        Label label = new Label
        {
            Text = message,
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.Start
        };

        messageList.Children.Add(label);
    });
}
```

## <a name="test-the-application"></a>Probar la aplicación

La aplicación Signalr chat se puede probar en iOS, Android y UWP siempre que tenga:

1. Creó un servicio de Azure Signalr.
1. Se creó una aplicación Azure Functions.
1. Personalizó el archivo **constants.CS** con el punto de conexión de la aplicación Azure functions.

Una vez que se completen estos pasos y se ejecute la aplicación, al hacer clic en el botón **conectar** , se creará una conexión con el servicio Azure signalr. Al escribir un mensaje y hacer clic en el botón **Enviar** , aparecen mensajes en la cola de chat de cualquier aplicación móvil conectada.

## <a name="related-links"></a>Vínculos relacionados

* [Creación de aplicaciones móviles en tiempo real con Xamarin y Signalr](https://mybuild.techcommunity.microsoft.com/sessions/77333/)
* [Introducción a SignalR](/aspnet/signalr/overview/getting-started/introduction-to-signalr)
* [Introducción a Azure Functions](/azure/azure-functions/functions-overview)
* [Azure Functions documentación](/azure/azure-functions/)
* [Ejemplo de chat de Signalr de MVVM](https://github.com/lbugnion/sample-xamarin-signalr)
