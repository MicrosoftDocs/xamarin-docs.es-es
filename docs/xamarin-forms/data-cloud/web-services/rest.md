---
title: Consumo de un servicio web RESTful
description: Integrar un servicio web en una aplicación es un escenario común. En este artículo se muestra cómo consumir un servicio web de RESTful desde una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: B540910C-9C51-416A-AAB9-057BF76489C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2018
ms.openlocfilehash: 9b99421755b1ad979b4beed279bef6e194659ab7
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69888862"
---
# <a name="consume-a-restful-web-service"></a>Consumo de un servicio web RESTful

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_Integrar un servicio web en una aplicación es un escenario común. En este artículo se muestra cómo consumir un servicio web de RESTful desde una aplicación de Xamarin.Forms._

La transferencia de estado representacional (REST) es un estilo de arquitectura para compilar servicios web. Las solicitudes REST se realizan a través de HTTP con los mismos verbos HTTP que los exploradores web usan para recuperar las páginas web y para enviar datos a los servidores. Los verbos son:

- **GET**: esta operación se usa para recuperar datos desde el servicio web.
- **POST**: esta operación se usa para crear un nuevo elemento de datos en el servicio web.
- **PUT**: esta operación se usa para actualizar un elemento de datos en el servicio web.
- **PATCH**: esta operación se usa para actualizar un elemento de datos en el servicio web con la descripción de un conjunto de instrucciones sobre cómo se debe modificar el elemento. Este verbo no se usa en la aplicación de ejemplo.
- **DELETE**: esta operación se usa para eliminar un elemento de datos en el servicio web.

Las API del servicio web que se adhieren a REST se conocen como API RESTful y se definen mediante:

- Un URI base.
- Métodos HTTP, como GET, POST, PUT, PATCH o DELETE.
- Un tipo de medio para los datos, como la notación de objetos JavaScript (JSON).

Los servicios web RESTful suelen usar mensajes JSON para devolver datos al cliente. JSON es un formato de intercambio de datos basado en texto que genera cargas compactas, lo que reduce los requisitos de ancho de banda al enviar datos. La aplicación de ejemplo usa la [biblioteca de código abierto JSON.NET de NewtonSoft](http://www.newtonsoft.com/json) para serializar y deserializar los mensajes.

La simplicidad de REST ha ayudado a convertirlo en el método principal para acceder a servicios web en aplicaciones móviles.

Cuando se ejecute la aplicación de ejemplo, se conectará a un servicio REST hospedado localmente, como se muestra en la siguiente captura de pantalla:

![](rest-images/portal.png "Aplicación de ejemplo")

> [!NOTE]
> En iOS 9 y versiones posteriores, App Transport Security (ATS) exige conexiones seguras entre los recursos de Internet (por ejemplo, el servidor back-end de la aplicación) y la aplicación, lo que impide la divulgación accidental de información confidencial. Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estarán sujetas a los requisitos de seguridad de ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
>
>Se puede optar por no usar ATS si no se puede utilizar el protocolo **HTTPS** ni proteger la comunicación de los recursos de Internet. Esto puede lograrse al actualizar el archivo **Info.plist** de la aplicación. Para obtener más información, consulte [Seguridad de transporte de aplicaciones](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Consumo del servicio Web

El servicio REST está escrito con ASP.NET Core y proporciona las siguientes operaciones:

|Operación|Método HTTP|URI relativo|Parámetros|
|--- |--- |--- |--- |
|Obtener una lista de tareas pendientes|GET|/api/todoitems/|
|Crear una nueva tarea pendiente|EXPONER|/api/todoitems/|TodoItem en formato JSON|
|Actualizar una tarea pendiente|PUT|/api/todoitems/|TodoItem en formato JSON|
|Eliminar una tarea pendiente|DELETE|/api/todoitems/{id}|

La mayoría de los URI incluyen el identificador `TodoItem` en la ruta de acceso. Por ejemplo, para eliminar un `TodoItem` cuyo identificador es `6bb8a868-dba1-4f1a-93b7-24ebce87e243`, el cliente envía una solicitud DELETE a `http://hostname/api/todoitems/6bb8a868-dba1-4f1a-93b7-24ebce87e243`. Para obtener más información sobre el modelo de datos usado en la aplicación de ejemplo, vea [Modelado de los datos](~/xamarin-forms/data-cloud/web-services/introduction.md).

Cuando el marco de la API web recibe una solicitud, enruta dicha solicitud a una acción. Estas acciones son simplemente métodos públicos de la clase `TodoItemsController`. El marco usa una tabla de enrutamiento para determinar qué acción va a invocar como respuesta a una solicitud, que se muestra en el ejemplo de código siguiente:

```csharp
config.Routes.MapHttpRoute(
    name: "TodoItemsApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { controller="todoitems", id = RouteParameter.Optional }
);
```

La tabla de enrutamiento contiene una plantilla de ruta y, cuando el marco de la API web recibe una solicitud HTTP, intenta hacer coincidir el URI con la plantilla de ruta de la tabla de enrutamiento. Si no se encuentra una ruta coincidente, el cliente recibe un error 404 (no encontrado). Si se encuentra una ruta coincidente, la API web selecciona el controlador y la acción de la siguiente forma:

- Para buscar el controlador, la API web agrega "controller" en el valor de la variable *{controller}* .
- Para buscar la acción, la API web observa el método HTTP y examina las acciones del controlador que tengan el mismo método HTTP como un atributo.
- La variable de marcador de posición *{id}* se asigna a un parámetro de acción.

El servicio REST usa la autenticación básica. Para obtener más información, consulte [Autenticación de un servicio web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md). Para obtener más información sobre el enrutamiento de ASP.NET Web API, consulte [Enrutamiento de ASP.NET Web API](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) en el sitio web de ASP.NET. Para obtener más información sobre cómo compilar el servicio REST mediante ASP.NET Core, consulte [Crear servicios back-end para aplicaciones móviles nativas](/aspnet/core/mobile/native-mobile-backend/).

La clase `HttpClient` se usa para enviar y recibir solicitudes a través de HTTP. Proporciona funcionalidad para enviar solicitudes HTTP y recibir respuestas HTTP de un recurso identificado con un URI. Cada solicitud se envía como una operación asincrónica. Para obtener más información sobre las operaciones asincrónicas, vea [Información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md).

La clase `HttpResponseMessage` representa un mensaje de respuesta HTTP recibido del servicio web después de realizar una solicitud HTTP. Contiene información sobre la respuesta, como el código de estado, los encabezados y cualquier cuerpo. La clase `HttpContent` representa el cuerpo HTTP y los encabezados de contenido, como `Content-Type` y `Content-Encoding`. El contenido se puede leer con cualquiera de los métodos `ReadAs`, como `ReadAsStringAsync` y `ReadAsByteArrayAsync`, en función del formato de los datos.

### <a name="creating-the-httpclient-object"></a>Crear el objeto HTTPClient

La instancia `HttpClient` se declara en el nivel de clase para que el objeto permanezca durante el tiempo que la aplicación necesite realizar solicitudes HTTP, como se muestra en el ejemplo de código siguiente:

```csharp
public class RestService : IRestService
{
  HttpClient _client;
  ...

  public RestService ()
  {
    _client = new HttpClient ();
  }
  ...
}
```

### <a name="retrieving-data"></a>Recuperar datos

El método `HttpClient.GetAsync` se usa para enviar la solicitud GET al servicio web especificado por el URI y, después, recibir la respuesta del servicio web, como se muestra en el ejemplo de código siguiente:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var uri = new Uri (string.Format (Constants.TodoItemsUrl, string.Empty));
  ...
  var response = await _client.GetAsync (uri);
  if (response.IsSuccessStatusCode)
  {
      var content = await response.Content.ReadAsStringAsync ();
      Items = JsonConvert.DeserializeObject <List<TodoItem>> (content);
  }
  ...
}
```

El servicio REST envía un código de estado HTTP en la propiedad `HttpResponseMessage.IsSuccessStatusCode` para indicar si la solicitud HTTP se ha realizado correctamente o no. Para esta operación, el servicio REST envía el código de estado HTTP 200 (correcto) en la respuesta, lo que indica que la solicitud se ha realizado correctamente y que la información solicitada está en la respuesta.

Si la operación HTTP se ha realizado correctamente, se lee el contenido de la respuesta para su visualización. La propiedad `HttpResponseMessage.Content` representa el contenido de la respuesta HTTP y el método `HttpContent.ReadAsStringAsync` escribe asincrónicamente el contenido HTTP en una cadena. Después, este contenido se convierte de JSON a un objeto `List` de instancias de `TodoItem`.

### <a name="creating-data"></a>Creación de datos

El método `HttpClient.PostAsync` se usa para enviar la solicitud POST al servicio web especificado por el URI y, después, para recibir la respuesta del servicio web, como se muestra en el ejemplo de código siguiente:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  var uri = new Uri (string.Format (Constants.TodoItemsUrl, string.Empty));

  ...
  var json = JsonConvert.SerializeObject (item);
  var content = new StringContent (json, Encoding.UTF8, "application/json");

  HttpResponseMessage response = null;
  if (isNewItem)
  {
    response = await _client.PostAsync (uri, content);
  }
  ...

  if (response.IsSuccessStatusCode)
  {
    Debug.WriteLine (@"\tTodoItem successfully saved.");

  }
  ...
}
```

La instancia `TodoItem` se convierte en una carga JSON para enviar al servicio web. Esta carga se inserta después en el cuerpo del contenido HTTP que se enviará al servicio web antes de realizar la solicitud con el método `PostAsync`.

El servicio REST envía un código de estado HTTP en la propiedad `HttpResponseMessage.IsSuccessStatusCode` para indicar si la solicitud HTTP se ha realizado correctamente o no. Las respuestas comunes para esta operación son:

- **201 (CREADO)** : la solicitud ha dado como resultado la creación de un nuevo recurso antes de que se enviara la respuesta.
- **400 (SOLICITUD INCORRECTA)** : el servidor no entiende la solicitud.
- **409 (CONFLICTO)** : la solicitud no se pudo llevar a cabo debido a un conflicto en el servidor.

### <a name="updating-data"></a>Actualizar datos

El método `HttpClient.PutAsync` se usa para enviar la solicitud PUT al servicio web especificado por el URI y, después, recibir la respuesta del servicio web, como se muestra en el ejemplo de código siguiente:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  response = await _client.PutAsync (uri, content);
  ...
}
```

El funcionamiento del método `PutAsync` es idéntico al del método `PostAsync` que se usa para crear datos en el servicio web, pero varían las posibles respuestas enviadas desde el servicio web. Sin embargo, se diferencian las posibles respuestas enviadas desde el servicio web.

El servicio REST envía un código de estado HTTP en la propiedad `HttpResponseMessage.IsSuccessStatusCode` para indicar si la solicitud HTTP se ha realizado correctamente o no. Las respuestas comunes para esta operación son:

- **204 (SIN CONTENIDO)** : la solicitud se ha procesado correctamente y la respuesta está en blanco intencionadamente.
- **400 (SOLICITUD INCORRECTA)** : el servidor no entiende la solicitud.
- **404 (NO ENCONTRADO)** : el recurso solicitado no existe en el servidor.

### <a name="deleting-data"></a>Eliminar datos

El método `HttpClient.DeleteAsync` se usa para enviar la solicitud DELETE al servicio web especificado por el URI y, después, recibir la respuesta del servicio web, como se muestra en el ejemplo de código siguiente:

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  var uri = new Uri (string.Format (Constants.TodoItemsUrl, id));
  ...
  var response = await _client.DeleteAsync (uri);
  if (response.IsSuccessStatusCode)
  {
    Debug.WriteLine (@"\tTodoItem successfully deleted.");
  }
  ...
}
```

El servicio REST envía un código de estado HTTP en la propiedad `HttpResponseMessage.IsSuccessStatusCode` para indicar si la solicitud HTTP se ha realizado correctamente o no. Las respuestas comunes para esta operación son:

- **204 (SIN CONTENIDO)** : la solicitud se ha procesado correctamente y la respuesta está en blanco intencionadamente.
- **400 (SOLICITUD INCORRECTA)** : el servidor no entiende la solicitud.
- **404 (NO ENCONTRADO)** : el recurso solicitado no existe en el servidor.

## <a name="related-links"></a>Vínculos relacionados

- [Creación de servicios back-end para aplicaciones móviles nativas](/aspnet/core/mobile/native-mobile-backend/)
- [TodoREST (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
