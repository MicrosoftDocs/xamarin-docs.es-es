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

Representational State Transfer (REST) es un estilo de arquitectura para la creación de servicios web. Solicitudes REST se realizan a través de HTTP utilizando los mismos verbos HTTP que los exploradores web que se usan para recuperar las páginas web y para enviar datos a los servidores. Los verbos son:

- **OBTENER** : esta operación se usa para recuperar datos desde el servicio web.
- **POST** : esta operación se usa para crear un nuevo elemento de datos en el servicio web.
- **COLOCAR** : esta operación se usa para actualizar un elemento de datos en el servicio web.
- **REVISIÓN** : esta operación se usa para actualizar un elemento de datos en el servicio web con la descripción de un conjunto de instrucciones sobre cómo se debe modificar el elemento. No se utiliza este verbo en la aplicación de ejemplo.
- **ELIMINAR** : esta operación se usa para eliminar un elemento de datos en el servicio web.

Se llama a las API de RESTful API que se adhieren a REST del servicio Web y se definen mediante:

- Un URI base.
- Métodos HTTP, como GET, POST, PUT, PATCH o DELETE.
- Tipo de medio de los datos, como JavaScript Object Notation (JSON).

Servicios web rESTful normalmente utilizan mensajes JSON para devolver datos al cliente. JSON es un formato de intercambio de datos basado en texto que genera cargas compactas, lo que reducción los requisitos de ancho de banda al enviar datos. La aplicación de ejemplo usa el código abierto [biblioteca de NewtonSoft JSON.NET](http://www.newtonsoft.com/json) para serializar y deserializar los mensajes.

La simplicidad de REST ha ayudado a hacer el método principal para tener acceso a servicios web en aplicaciones móviles.

Cuando se ejecute la aplicación de ejemplo, se conectará a un servicio REST hospedado localmente, como se muestra en la siguiente captura de pantalla:

![](rest-images/portal.png "Aplicación de ejemplo")

> [!NOTE]
> En iOS 9 y versiones posteriores, App Transport Security (ATS) exige que las conexiones seguras entre los recursos de internet (por ejemplo, el servidor back-end de la aplicación) y la aplicación, lo que impide la divulgación accidental de información confidencial. Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estará sujeto a los requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
>
>Se puede optar por en ATS de si no es posible usar el **HTTPS** protocolo y proteger la comunicación de los recursos de internet. Esto puede lograrse mediante la actualización de la aplicación **Info.plist** archivo. Para obtener más información, consulte [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Consumo del servicio Web

El servicio REST está escrito con ASP.NET Core y proporciona las siguientes operaciones:

|Operación|Método HTTP|URI relativo|Parámetros|
|--- |--- |--- |--- |
|Obtener una lista de tareas pendientes|GET|/ API/todoitems /|
|Crear una nueva tarea pendiente|EXPONER|/ API/todoitems /|TodoItem en formato JSON|
|Actualizar una tarea pendiente|PUT|/ API/todoitems /|TodoItem en formato JSON|
|Eliminar una tarea pendiente|SUPRIMIR|/api/todoitems/{id}|

La mayoría de los URI incluyen el `TodoItem` ID en la ruta de acceso. Por ejemplo, para eliminar la `TodoItem` cuyo identificador es `6bb8a868-dba1-4f1a-93b7-24ebce87e243`, el cliente envía una solicitud DELETE `http://hostname/api/todoitems/6bb8a868-dba1-4f1a-93b7-24ebce87e243`. Para obtener más información acerca del modelo de datos usado en la aplicación de ejemplo, vea [los datos de modelado](~/xamarin-forms/data-cloud/web-services/introduction.md).

Cuando el marco API Web recibe una solicitud enruta la solicitud a una acción. Estas acciones son métodos públicos simplemente en el `TodoItemsController` clase. El marco de trabajo usa una tabla de enrutamiento para determinar qué acción va a invocar en respuesta a una solicitud, que se muestra en el ejemplo de código siguiente:

```csharp
config.Routes.MapHttpRoute(
    name: "TodoItemsApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { controller="todoitems", id = RouteParameter.Optional }
);
```

La tabla de enrutamiento contiene una plantilla de ruta, y cuando el marco API Web recibe una solicitud HTTP, intenta coincidir con el URI en la plantilla de ruta en la tabla de enrutamiento. Si una coincidencia de ruta no se encuentra que el cliente recibe un error 404 (no encontrado). Si se encuentra una ruta coincidente, Web API selecciona el controlador y la acción siguiente:

- Para buscar el controlador, Web API agrega "controller" en el valor de la *{controller}* variable.
- Para buscar la acción, API Web examina el método HTTP y examina las acciones de controlador que se decoran con el mismo método HTTP como un atributo.
- El *{id}* variable de marcador de posición se asigna a un parámetro de acción.

El servicio REST usa la autenticación básica. Para obtener más información, consulte [autenticar un servicio web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md). Para obtener más información sobre el enrutamiento de ASP.NET Web API, consulte [Routing in ASP.NET Web API](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) en el sitio Web ASP.NET. Para obtener más información acerca de cómo crear el servicio REST mediante ASP.NET Core, consulte [crear servicios back-end para aplicaciones móviles nativas](/aspnet/core/mobile/native-mobile-backend/).

La `HttpClient` clase se utiliza para enviar y recibir solicitudes a través de HTTP. Proporciona funcionalidad para enviar solicitudes HTTP y recibir respuestas HTTP de un URI identifica el recurso. Cada solicitud se envía como una operación asincrónica. Para obtener más información acerca de las operaciones asincrónicas, vea [información general de soporte técnico de Async](~/cross-platform/platform/async.md).

La `HttpResponseMessage` clase representa un mensaje de respuesta HTTP recibido del servicio web después de realizar una solicitud HTTP. Contiene información acerca de la respuesta, incluido el código de estado, los encabezados y cualquier cuerpo. El `HttpContent` clase representa el cuerpo HTTP y encabezados de contenido, como `Content-Type` y `Content-Encoding`. El contenido se puede leer utilizando cualquiera de los `ReadAs` métodos, como `ReadAsStringAsync` y `ReadAsByteArrayAsync`, dependiendo del formato de los datos.

### <a name="creating-the-httpclient-object"></a>Crear el objeto HTTPClient

El `HttpClient` instancia se declara en el nivel de clase para que se encuentra el objeto para siempre y cuando la aplicación necesita para realizar solicitudes HTTP, como se muestra en el ejemplo de código siguiente:

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

El `HttpClient.GetAsync` método se utiliza para enviar la solicitud GET al servicio web especificado por el identificador URI y, a continuación, recibir la respuesta del servicio web, como se muestra en el ejemplo de código siguiente:

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

El servicio REST envía un código de estado HTTP en el `HttpResponseMessage.IsSuccessStatusCode` propiedad para indicar si la solicitud HTTP se realizó correctamente o no. Para esta operación el resto servicio envía el código de estado HTTP 200 (OK) en la respuesta, lo que indica que la solicitud es correcta y que la información solicitada en la respuesta.

Si la operación HTTP se realizó correctamente, se lee el contenido de la respuesta, para su presentación. El `HttpResponseMessage.Content` propiedad representa el contenido de la respuesta HTTP y el `HttpContent.ReadAsStringAsync` método escribe asincrónicamente el contenido HTTP en una cadena. Este contenido, a continuación, se convierte de JSON a un `List` de `TodoItem` instancias.

### <a name="creating-data"></a>Creación de datos

El `HttpClient.PostAsync` método se usa para enviar la solicitud POST al servicio web especificado por el identificador URI y, a continuación, para recibir la respuesta del servicio web, como se muestra en el ejemplo de código siguiente:

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

El `TodoItem` instancia se convierte en una carga JSON para enviar al servicio web. Esta carga, a continuación, se inserta en el cuerpo de contenido HTTP que se enviará al servicio web antes de realizar la solicitud con el `PostAsync` método.

El servicio REST envía un código de estado HTTP en el `HttpResponseMessage.IsSuccessStatusCode` propiedad para indicar si la solicitud HTTP se realizó correctamente o no. Las respuestas comunes para esta operación son:

- **201 (creado)** : la solicitud dio como resultado un nuevo recurso que se creó antes de que se envió la respuesta.
- **400 (solicitud incorrecta)** : el servidor no entiende la solicitud.
- **409 (conflicto)** : la solicitud no puede llevarse a cabo debido a un conflicto en el servidor.

### <a name="updating-data"></a>Actualizar datos

El `HttpClient.PutAsync` método se utiliza para enviar la solicitud PUT al servicio web especificado por el identificador URI y, a continuación, recibir la respuesta del servicio web, como se muestra en el ejemplo de código siguiente:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  response = await _client.PutAsync (uri, content);
  ...
}
```

La operación de la `PutAsync` es idéntico al método el `PostAsync` método que se usa para crear datos en el servicio web. Sin embargo, se diferencian las posibles respuestas enviadas desde el servicio web.

El servicio REST envía un código de estado HTTP en el `HttpResponseMessage.IsSuccessStatusCode` propiedad para indicar si la solicitud HTTP se realizó correctamente o no. Las respuestas comunes para esta operación son:

- **204 (sin contenido)** : se ha procesado correctamente la solicitud y la respuesta está intencionadamente en blanco.
- **400 (solicitud incorrecta)** : el servidor no entiende la solicitud.
- **404 (no encontrado)** : el recurso solicitado no existe en el servidor.

### <a name="deleting-data"></a>Eliminar datos

El `HttpClient.DeleteAsync` método se utiliza para enviar la solicitud DELETE al servicio web especificado por el identificador URI y, a continuación, recibir la respuesta del servicio web, como se muestra en el ejemplo de código siguiente:

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

El servicio REST envía un código de estado HTTP en el `HttpResponseMessage.IsSuccessStatusCode` propiedad para indicar si la solicitud HTTP se realizó correctamente o no. Las respuestas comunes para esta operación son:

- **204 (sin contenido)** : se ha procesado correctamente la solicitud y la respuesta está intencionadamente en blanco.
- **400 (solicitud incorrecta)** : el servidor no entiende la solicitud.
- **404 (no encontrado)** : el recurso solicitado no existe en el servidor.

## <a name="related-links"></a>Vínculos relacionados

- [Creación de servicios back-end para aplicaciones móviles nativas](/aspnet/core/mobile/native-mobile-backend/)
- [TodoREST (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
