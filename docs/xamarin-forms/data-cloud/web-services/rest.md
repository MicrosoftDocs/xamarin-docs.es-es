---
title: Consumo de un servicio web RESTful
description: La integración de un servicio Web en una aplicación es un escenario común. En este artículo se muestra cómo consumir un servicio web RESTful desde una aplicación de Xamarin. Forms.
ms.prod: xamarin
ms.assetid: B540910C-9C51-416A-AAB9-057BF76489C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2018
ms.openlocfilehash: 6ccbb24be8c03d634c884853fb0ec339d49cf49d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029549"
---
# <a name="consume-a-restful-web-service"></a>Consumo de un servicio web RESTful

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_La integración de un servicio Web en una aplicación es un escenario común. En este artículo se muestra cómo consumir un servicio web RESTful desde una aplicación de Xamarin. Forms._

La transferencia de estado representacional (REST) es un estilo arquitectónico para la creación de servicios Web. Las solicitudes REST se realizan a través de HTTP usando los mismos verbos HTTP que los exploradores web utilizan para recuperar páginas web y enviar datos a los servidores. Los verbos son:

- **Get** : esta operación se usa para recuperar datos del servicio Web.
- **Post** : esta operación se usa para crear un nuevo elemento de datos en el servicio Web.
- **Put** : esta operación se usa para actualizar un elemento de datos en el servicio Web.
- **Patch** : esta operación se usa para actualizar un elemento de datos en el servicio Web mediante la descripción de un conjunto de instrucciones sobre cómo se debe modificar el elemento. Este verbo no se utiliza en la aplicación de ejemplo.
- **Delete** : esta operación se usa para eliminar un elemento de datos en el servicio Web.

Las API de servicios web que se adhieren a REST se denominan API de RESTful y se definen mediante:

- URI base.
- Métodos HTTP, como GET, POST, PUT, PATCH o DELETE.
- Un tipo de medio para los datos, como notación de objetos JavaScript (JSON).

Los servicios web RESTful normalmente usan mensajes JSON para devolver datos al cliente. JSON es un formato de intercambio de datos basado en texto que genera cargas compactos, lo que reduce los requisitos de ancho de banda cuando se envían datos. La aplicación de ejemplo usa la [biblioteca de NewtonSoft JSON.net](https://www.newtonsoft.com/json) de código abierto para serializar y deserializar mensajes.

La simplicidad de REST ha ayudado a convertirlo en el método principal para tener acceso a los servicios web en aplicaciones móviles.

Cuando se ejecute la aplicación de ejemplo, se conectará a un servicio REST hospedado localmente, como se muestra en la siguiente captura de pantalla:

![](rest-images/portal.png "Sample Application")

> [!NOTE]
> En iOS 9 y versiones posteriores, la seguridad de transporte de aplicaciones (ATS) exige conexiones seguras entre recursos de Internet (como el servidor back-end de la aplicación) y la aplicación, lo que evita la divulgación accidental de información confidencial. Dado que ATS está habilitado de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estarán sujetas a los requisitos de seguridad de ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
>
>ATS puede no participar en si no es posible usar el protocolo **https** y proteger la comunicación para los recursos de Internet. Esto se puede lograr actualizando el archivo **info. plist** de la aplicación. Para obtener más información, vea [seguridad de transporte de aplicaciones](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Consumo del servicio Web

El servicio REST se escribe utilizando ASP.NET Core y proporciona las operaciones siguientes:

|Operación|Método HTTP|URI relativo|Parámetros|
|--- |--- |--- |--- |
|Obtención de una lista de tareas pendientes|GET|/api/todoitems/|
|Crear un nuevo elemento de tareas pendientes|Exponer|/api/todoitems/|Un TodoItem con formato JSON|
|Actualizar una tarea pendiente|PONDRÁN|/api/todoitems/|Un TodoItem con formato JSON|
|Eliminar una tarea pendiente|SUPRIMIR|/api/todoitems/{id}|

La mayoría de los URI incluyen el identificador de `TodoItem` en la ruta de acceso. Por ejemplo, para eliminar la `TodoItem` cuyo identificador es `6bb8a868-dba1-4f1a-93b7-24ebce87e243`, el cliente envía una solicitud DELETE a `http://hostname/api/todoitems/6bb8a868-dba1-4f1a-93b7-24ebce87e243`. Para obtener más información sobre el modelo de datos utilizado en la aplicación de ejemplo, vea [modelar los datos](~/xamarin-forms/data-cloud/web-services/introduction.md).

Cuando el marco de la API Web recibe una solicitud, enruta la solicitud a una acción. Estas acciones son simplemente métodos públicos en la clase `TodoItemsController`. El marco de trabajo utiliza una tabla de enrutamiento para determinar qué acción se debe invocar en respuesta a una solicitud, que se muestra en el ejemplo de código siguiente:

```csharp
config.Routes.MapHttpRoute(
    name: "TodoItemsApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { controller="todoitems", id = RouteParameter.Optional }
);
```

La tabla de enrutamiento contiene una plantilla de ruta y, cuando el marco de la API Web recibe una solicitud HTTP, intenta hacer coincidir el URI con la plantilla de ruta de la tabla de enrutamiento. Si no se encuentra una ruta coincidente, el cliente recibe un error 404 (no encontrado). Si se encuentra una ruta coincidente, la API Web selecciona el controlador y la acción como se indica a continuación:

- Para buscar el controlador, la API web agrega "controller" en el valor de la variable *{controller}* .
- Para encontrar la acción, la API Web examina el método HTTP y examina las acciones del controlador que se representan con el mismo método HTTP que un atributo.
- La variable de marcador de posición *{ID}* está asignada a un parámetro de acción.

El servicio REST utiliza la autenticación básica. Para obtener más información, consulte [autenticación de un servicio web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md). Para obtener más información sobre el enrutamiento de ASP.NET Web API, consulte [enrutamiento en ASP.net web API](https://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) en el sitio web de ASP.net. Para obtener más información sobre cómo compilar el servicio REST mediante ASP.NET Core, vea [crear servicios back-end para aplicaciones móviles nativas](/aspnet/core/mobile/native-mobile-backend/).

La clase `HttpClient` se utiliza para enviar y recibir solicitudes a través de HTTP. Proporciona funcionalidad para enviar solicitudes HTTP y recibir respuestas HTTP de un recurso identificado por un URI. Cada solicitud se envía como una operación asincrónica. Para obtener más información acerca de las operaciones asincrónicas, vea [información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md).

La clase `HttpResponseMessage` representa un mensaje de respuesta HTTP recibido del servicio Web después de que se haya realizado una solicitud HTTP. Contiene información sobre la respuesta, incluido el código de estado, los encabezados y cualquier cuerpo. La clase `HttpContent` representa el cuerpo HTTP y los encabezados de contenido, como `Content-Type` y `Content-Encoding`. El contenido se puede leer mediante cualquiera de los métodos de `ReadAs`, como `ReadAsStringAsync` y `ReadAsByteArrayAsync`, dependiendo del formato de los datos.

### <a name="creating-the-httpclient-object"></a>Creación del objeto HTTPClient

La instancia de `HttpClient` se declara en el nivel de clase para que el objeto se encuentre siempre y cuando la aplicación tenga que hacer solicitudes HTTP, como se muestra en el ejemplo de código siguiente:

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

El método `HttpClient.GetAsync` se usa para enviar la solicitud GET al servicio Web especificado por el URI y, a continuación, recibir la respuesta del servicio Web, como se muestra en el ejemplo de código siguiente:

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

El servicio REST envía un código de Estado HTTP en la propiedad `HttpResponseMessage.IsSuccessStatusCode` para indicar si la solicitud HTTP se ha realizado correctamente o no. Para esta operación, el servicio REST envía el código de Estado HTTP 200 (OK) en la respuesta, que indica que la solicitud se realizó correctamente y que la información solicitada se encuentra en la respuesta.

Si la operación HTTP se realizó correctamente, se lee el contenido de la respuesta para su presentación. La propiedad `HttpResponseMessage.Content` representa el contenido de la respuesta HTTP y el método `HttpContent.ReadAsStringAsync` escribe asincrónicamente el contenido HTTP en una cadena. A continuación, este contenido se convierte de JSON a un `List` de instancias de `TodoItem`.

### <a name="creating-data"></a>Crear datos

El método `HttpClient.PostAsync` se usa para enviar la solicitud POST al servicio Web especificado por el URI y, a continuación, para recibir la respuesta del servicio Web, como se muestra en el ejemplo de código siguiente:

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

La instancia de `TodoItem` se convierte en una carga JSON para enviarla al servicio Web. Esta carga se inserta entonces en el cuerpo del contenido HTTP que se enviará al servicio Web antes de que se realice la solicitud con el método `PostAsync`.

El servicio REST envía un código de Estado HTTP en la propiedad `HttpResponseMessage.IsSuccessStatusCode` para indicar si la solicitud HTTP se ha realizado correctamente o no. Las respuestas comunes para esta operación son:

- **201 (creado)** : la solicitud dio lugar a la creación de un nuevo recurso antes de que se enviara la respuesta.
- **400 (solicitud incorrecta)** : el servidor no entiende la solicitud.
- **409 (conflicto)** : no se pudo realizar la solicitud debido a un conflicto en el servidor.

### <a name="updating-data"></a>Actualizar datos

El método `HttpClient.PutAsync` se usa para enviar la solicitud PUT al servicio Web especificado por el URI y, a continuación, recibir la respuesta del servicio Web, como se muestra en el ejemplo de código siguiente:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  response = await _client.PutAsync (uri, content);
  ...
}
```

El funcionamiento del método `PutAsync` es idéntico al del método `PostAsync` que se usa para crear datos en el servicio web, pero varían las posibles respuestas enviadas desde el servicio web. Sin embargo, las posibles respuestas enviadas desde el servicio Web difieren.

El servicio REST envía un código de Estado HTTP en la propiedad `HttpResponseMessage.IsSuccessStatusCode` para indicar si la solicitud HTTP se ha realizado correctamente o no. Las respuestas comunes para esta operación son:

- **204 (sin contenido)** : la solicitud se ha procesado correctamente y la respuesta está en blanco intencionadamente.
- **400 (solicitud incorrecta)** : el servidor no entiende la solicitud.
- **404 (no encontrado)** : el recurso solicitado no existe en el servidor.

### <a name="deleting-data"></a>Eliminar datos

El método `HttpClient.DeleteAsync` se usa para enviar la solicitud de eliminación al servicio Web especificado por el URI y, a continuación, recibir la respuesta del servicio Web, como se muestra en el ejemplo de código siguiente:

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

El servicio REST envía un código de Estado HTTP en la propiedad `HttpResponseMessage.IsSuccessStatusCode` para indicar si la solicitud HTTP se ha realizado correctamente o no. Las respuestas comunes para esta operación son:

- **204 (sin contenido)** : la solicitud se ha procesado correctamente y la respuesta está en blanco intencionadamente.
- **400 (solicitud incorrecta)** : el servidor no entiende la solicitud.
- **404 (no encontrado)** : el recurso solicitado no existe en el servidor.

## <a name="related-links"></a>Vínculos relacionados

- [Creación de servicios back-end para aplicaciones móviles nativas](/aspnet/core/mobile/native-mobile-backend/)
- [TodoREST (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
