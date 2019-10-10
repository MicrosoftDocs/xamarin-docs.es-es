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

_Integrar un servicio web en una aplicación es un escenario común. En este artículo se muestra cómo consumir un servicio web RESTful desde una aplicación de Xamarin.Forms._

Representational State Transfer (REST) es un estilo de arquitectura para la creación de servicios web. Las solicitudes REST se realizan a través de HTTP utilizando los mismos verbos HTTP que los exploradores web que se usan para recuperar las páginas web y para enviar datos a los servidores. Los verbos son:

- **GET**: Esta operación se usa para recuperar datos desde el servicio web.
- **POST**: Esta operación se usa para crear un nuevo elemento de datos en el servicio web.
- **PUT**: Esta operación se usa para actualizar un elemento de datos en el servicio web.
- **PATCH**: Esta operación se usa para actualizar un elemento de datos en el servicio web con la descripción de un conjunto de instrucciones sobre cómo se debe modificar el elemento. Este verbo no se utiliza en la aplicación de ejemplo.
- **DELETE**: Esta operación se usa para eliminar un elemento de datos en el servicio web.

Las APIs de los servicios web que se adhieren a REST se denominan APIs RESTful y se definen utilizando:

- Un URI base.
- Métodos HTTP, como GET, POST, PUT, PATCH o DELETE.
- Un tipo de medio para los datos, como JavaScript Object Notation (JSON).

Los servicios web RESTful normalmente utilizan mensajes JSON para devolver datos al cliente. JSON es un formato de intercambio de datos basado en texto que genera cargas compactas, lo que reduce los requisitos de ancho de banda al enviar datos. La aplicación de ejemplo usa la biblioteca de código abierto [NewtonSoft JSON.NET](http://www.newtonsoft.com/json) para serializar y deserializar los mensajes.

La simplicidad de REST ha ayudado a convertirlo en el método principal para acceder a servicios web en aplicaciones móviles.

Cuando se ejecute la aplicación de ejemplo, se conectará a un servicio REST alojado localmente, como se muestra en la siguiente captura de pantalla:

![](rest-images/portal.png "Aplicación de ejemplo")

> [!NOTE]
> En iOS 9 y versiones posteriores, App Transport Security (ATS) exige conexiones seguras entre los recursos de Internet (por ejemplo, el servidor back-end de la aplicación) y la aplicación, evitando así la divulgación accidental de información confidencial. Puesto que ATS está habilitada de forma predeterminada en las aplicaciones creadas para iOS 9, todas las conexiones estará sujeto a los requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, fallarán con una excepción.
>
>Se puede optar por desactivar ATS si no es posible usar el protocolo **HTTPS** y la comunicación segura para los recursos de Internet. Esto puede lograrse mediante la actualización del archivo **Info.plist** en la aplicación. Para obtener más información, consulte [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Consumo del servicio Web

El servicio REST está escrito con ASP.NET Core y proporciona las siguientes operaciones:

|Operación|Método HTTP|URI relativo|Parámetros|
|--- |--- |--- |--- |
|Obtener una lista de tareas pendientes|GET|/api/todoitems/|
|Crear una nueva tarea pendiente|POST|/api/todoitems/|TodoItem en formato JSON|
|Actualizar una tarea pendiente|PUT|/api/todoitems/|TodoItem en formato JSON|
|Eliminar una tarea pendiente|DELETE|/api/todoitems/{id}|

La mayoría de los URI incluyen el ID de `TodoItem` en la ruta. Por ejemplo, para eliminar un `TodoItem` cuyo identificador es `6bb8a868-dba1-4f1a-93b7-24ebce87e243`, el cliente envía una solicitud DELETE `http://hostname/api/todoitems/6bb8a868-dba1-4f1a-93b7-24ebce87e243`. Para obtener más información acerca del modelo de datos usado en la aplicación de ejemplo, vea [el modelado de datos](~/xamarin-forms/data-cloud/web-services/introduction.md).

Cuando el marco de la WEB API recibe una solicitud, enruta la solicitud a una acción. Estas acciones son simplemente métodos públicos en la clase `TodoItemsController`. El marco de trabajo usa una tabla de enrutamiento para determinar qué acción va a invocar en respuesta a una solicitud, que se muestra en el ejemplo de código siguiente:

```csharp
config.Routes.MapHttpRoute(
    name: "TodoItemsApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { controller="todoitems", id = RouteParameter.Optional }
);
```

La tabla de enrutamiento contiene una plantilla de ruta, y cuando el marco de la Web API recibe una solicitud HTTP, intenta coincidir el URI con la plantilla de ruta en la tabla de enrutamiento. Si no se puede encontrar una ruta coincidente, el cliente recibe un error 404 (no encontrado). Si se encuentra una ruta coincidente, la Web API selecciona el controlador y la acción de la siguiente siguiente:

- Para buscar el controlador, la Web API agrega "controller" en el valor de la variable *{controller}*.
- Para buscar la acción, la Web API examina el método HTTP y examina las acciones del controlador que coincidan con el mismo método HTTP como un atributo.
- La variable de marcador de posición *{id}* se asigna a un parámetro de acción.

El servicio REST usa la autenticación básica. Para obtener más información, consulte [autenticar un servicio web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md). Para obtener más información sobre el enrutamiento de ASP.NET Web API, consulte [Enrutamiento de ASP.NET Web API](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) en el sitio web de ASP.NET. Para obtener más información acerca de cómo crear el servicio REST usando ASP.NET Core, consulte [crear servicios back-end para aplicaciones móviles nativas](/aspnet/core/mobile/native-mobile-backend/).

La clase `HttpClient` se utiliza para enviar y recibir solicitudes a través de HTTP. Proporciona funcionalidad para enviar solicitudes HTTP y recibir respuestas HTTP de un recurso identificado por URI. Cada solicitud se envía como una operación asincrónica. Para obtener más información acerca de las operaciones asincrónicas, consulte [Información general del soporte asíncrono](~/cross-platform/platform/async.md).

La clase `HttpResponseMessage` representa un mensaje de respuesta HTTP recibido del servicio web después de realizar una solicitud HTTP. Contiene información acerca de la respuesta, incluido el código de estado, los encabezados y cualquier cuerpo. El clase `HttpContent` representa el cuerpo HTTP y los encabezados de contenido, como el `Content-Type` y `Content-Encoding`. El contenido se puede leer utilizando cualquiera de los métodos `ReadAs`, como `ReadAsStringAsync` y `ReadAsByteArrayAsync`, dependiendo del formato de los datos.

### <a name="creating-the-httpclient-object"></a>Crear el objeto HTTPClient

La instancia `HttpClient` se declara a nivel de clase para que el objeto esté durante el tiempo que la aplicación necesite realizar solicitudes HTTP, como se muestra en el siguiente código de ejemplo:

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

### <a name="retrieving-data"></a>Obtener datos

El método `HttpClient.GetAsync` se utiliza para enviar la solicitud de tipo GET al servicio web especificado por el identificador URI y luego recibe la respuesta del servicio web, como se muestra en el siguiente código de ejemplo:

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

El servicio REST envía un código de estado HTTP en la propiedad `HttpResponseMessage.IsSuccessStatusCode` para indicar si la solicitud HTTP se realizó correctamente o no. Para esta operación, el servicio REST envía el código de estado HTTP 200 (OK) en la respuesta, lo indica que la solicitud se realizó correctamente y que la información solicitada está en la respuesta.

Si la operación HTTP se realizó correctamente, se lee el contenido de la respuesta para su visualización. La propiedad `HttpResponseMessage.Content` representa el contenido de la respuesta HTTP y el método `HttpContent.ReadAsStringAsync` escribe asincrónicamente el contenido HTTP en una variable de tipo `string`. Este contenido, a continuación, se convierte de JSON a una instancia `List` de `TodoItem`.

### <a name="creating-data"></a>Creación de datos

El método `HttpClient.PostAsync` se utiliza para enviar la solicitud de tipo POST al servicio web especificado por el identificador URI y luego recibe la respuesta del servicio web, como se muestra en el siguiente código de ejemplo:

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

La instancia `TodoItem` se convierte en un objeto serializado JSON para enviar al servicio web. Esta objeto serializado se inserta en el cuerpo del contenido HTTP que se enviará al servicio web antes de realizar la solicitud con el método `PostAsync`.

El servicio REST envía un código de estado HTTP en la propiedad `HttpResponseMessage.IsSuccessStatusCode` para indicar si la solicitud HTTP se realizó correctamente o no. Las respuestas comunes para esta operación son:

- **201 (CREATED)**: La solicitud dio como resultado la creación de un nuevo recurso antes de enviar la respuesta.
- **400 (BAD REQUEST)**: La solicitud no es entendida por el servidor.
- **409 (CONFLICT)**: La solicitud no se pudo llevar a cabo debido a un conflicto en el servidor.

### <a name="updating-data"></a>Actualizar datos

El método `HttpClient.PutAsync` se utiliza para enviar la solicitud de tipo PUT al servicio web especificado por el identificador URI y luego recibe la respuesta del servicio web, como se muestra en el siguiente código de ejemplo:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  response = await _client.PutAsync (uri, content);
  ...
}
```

El funcionamiento del método `PutAsync` es idéntico al método `PostAsync` que se usa para crear datos en el servicio web. Sin embargo, se diferencian las posibles respuestas enviadas desde el servicio web.

El servicio REST envía un código de estado HTTP en la propiedad `HttpResponseMessage.IsSuccessStatusCode` para indicar si la solicitud HTTP se realizó correctamente o no. Las respuestas comunes para esta operación son:

- **204 (NO CONTENT)**: La solicitud se ha procesado correctamente y la respuesta está en blanco intencionalmente.
- **400 (BAD REQUEST)**: El servidor no entiende la solicitud.
- **404 (NOT FOUND)**: El recurso solicitado no existe en el servidor.

### <a name="deleting-data"></a>Eliminar datos

El método `HttpClient.DeleteAsync` se utiliza para enviar la solicitud de tipo DELETE al servicio web especificado por el identificador URI y luego recibe la respuesta del servicio web, como se muestra en el siguiente código de ejemplo:

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

El servicio REST envía un código de estado HTTP en la propiedad `HttpResponseMessage.IsSuccessStatusCode` para indicar si la solicitud HTTP se realizó correctamente o no. Las respuestas comunes para esta operación son:

- **204 (NO CONTENT)**: La solicitud se ha procesado correctamente y la respuesta está en blanco intencionalmente.
- **400 (BAD REQUEST)**: El servidor no entiende la solicitud.
- **404 (NOT FOUND)**: El recurso solicitado no existe en el servidor.

## <a name="related-links"></a>Vínculos relacionados

- [Creación de servicios back-end para aplicaciones móviles nativas](/aspnet/core/mobile/native-mobile-backend/)
- [TodoREST (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
