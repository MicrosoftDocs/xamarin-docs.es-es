---
title: Consumo de un servicio web RESTful
description: La integración de un servicio Web en una aplicación es un escenario común. En este artículo se muestra cómo consumir un servicio web RESTful desde una Xamarin.Forms aplicación.
ms.prod: xamarin
ms.assetid: B540910C-9C51-416A-AAB9-057BF76489C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 27bafeb9ac82ba23128eabdd9f4a15cb5c8c1bf1
ms.sourcegitcommit: 322e7bcf9fb8c1ad52ab8e929bea95d45e280834
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/03/2021
ms.locfileid: "101751371"
---
# <a name="consume-a-restful-web-service"></a>Consumo de un servicio web RESTful

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_La integración de un servicio Web en una aplicación es un escenario común. En este artículo se muestra cómo consumir un servicio web RESTful desde una Xamarin.Forms aplicación._

La transferencia de estado representacional (REST) es un estilo arquitectónico para la creación de servicios Web. Las solicitudes REST se realizan a través de HTTP usando los mismos verbos HTTP que los exploradores web utilizan para recuperar páginas web y enviar datos a los servidores. Los verbos son:

- **Get** : esta operación se usa para recuperar datos del servicio Web.
- **Post** : esta operación se usa para crear un nuevo elemento de datos en el servicio Web.
- **Put** : esta operación se usa para actualizar un elemento de datos en el servicio Web.
- **Patch** : esta operación se usa para actualizar un elemento de datos en el servicio Web mediante la descripción de un conjunto de instrucciones sobre cómo se debe modificar el elemento. Este verbo no se utiliza en la aplicación de ejemplo.
- **Delete** : esta operación se usa para eliminar un elemento de datos en el servicio Web.

Las API de servicios web que se adhieren a REST se denominan API de RESTful y se definen mediante:

- Identificador URI base.
- Métodos HTTP, como GET, POST, PUT, PATCH o DELETE.
- Un tipo de medio para los datos, como notación de objetos JavaScript (JSON).

Los servicios web RESTful normalmente usan mensajes JSON para devolver datos al cliente. JSON es un formato de intercambio de datos basado en texto que genera cargas compactos, lo que reduce los requisitos de ancho de banda cuando se envían datos. La aplicación de ejemplo usa la [biblioteca de NewtonSoft JSON.net](https://www.newtonsoft.com/json) de código abierto para serializar y deserializar mensajes.

La simplicidad de REST ha ayudado a convertirlo en el método principal para tener acceso a los servicios web en aplicaciones móviles.

Cuando se ejecute la aplicación de ejemplo, se conectará a un servicio REST hospedado localmente, como se muestra en la siguiente captura de pantalla:

![Aplicación de ejemplo](rest-images/portal.png)

> [!NOTE]
> En iOS 9 y versiones posteriores, la seguridad de transporte de aplicaciones (ATS) exige conexiones seguras entre recursos de Internet (como el servidor back-end de la aplicación) y la aplicación, lo que evita la divulgación accidental de información confidencial. Dado que ATS está habilitado de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estarán sujetas a los requisitos de seguridad de ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
>
>ATS puede no participar en si no es posible usar el protocolo **https** y proteger la comunicación para los recursos de Internet. Esto se puede lograr actualizando el archivo **info. plist** de la aplicación. Para obtener más información, vea [seguridad de transporte de aplicaciones](~/ios/app-fundamentals/ats.md).

## <a name="consume-the-web-service"></a>Consumo del servicio web

El servicio REST se escribe utilizando ASP.NET Core y proporciona las operaciones siguientes:

|Operación|HTTP method|URI relativo|Parámetros|
|--- |--- |--- |--- |
|Obtención de una lista de tareas pendientes|GET|/api/todoitems/|
|Crear un nuevo elemento de tareas pendientes|POST|/api/todoitems/|Un TodoItem con formato JSON|
|Actualizar una tarea pendiente|PUT|/api/todoitems/|Un TodoItem con formato JSON|
|Eliminar una tarea pendiente|Delete|/api/todoitems/{id}|

La mayoría de los URI incluyen el `TodoItem` identificador de la ruta de acceso. Por ejemplo, para eliminar el `TodoItem` cuyo identificador es `6bb8a868-dba1-4f1a-93b7-24ebce87e243` , el cliente envía una solicitud DELETE a `http://hostname/api/todoitems/6bb8a868-dba1-4f1a-93b7-24ebce87e243` . Para obtener más información sobre el modelo de datos utilizado en la aplicación de ejemplo, vea [modelar los datos](~/xamarin-forms/data-cloud/web-services/introduction.md).

Cuando el marco de la API Web recibe una solicitud, enruta la solicitud a una acción. Estas acciones son simplemente métodos públicos en la `TodoItemsController` clase. El marco de trabajo usa middleware de enrutamiento para hacer coincidir las direcciones URL de las solicitudes entrantes y asignarlas a las acciones. Las API de REST deben usar el enrutamiento de atributo para modelar la funcionalidad de la aplicación como un conjunto de recursos cuyas operaciones se representan mediante verbos HTTP. El enrutamiento mediante atributos utiliza un conjunto de atributos para asignar acciones directamente a las plantillas de ruta. Para obtener más información sobre el enrutamiento de atributos, consulte [enrutamiento de atributos para las API de REST](/aspnet/core/mvc/controllers/routing?view=aspnetcore-5.0#ar). Para obtener más información sobre cómo compilar el servicio REST mediante ASP.NET Core, vea [crear servicios back-end para aplicaciones móviles nativas](/aspnet/core/mobile/native-mobile-backend/).

La `HttpClient` clase se utiliza para enviar y recibir solicitudes a través de http. Proporciona funcionalidad para enviar solicitudes HTTP y recibir respuestas HTTP de un recurso identificado por un URI. Cada solicitud se envía como una operación asincrónica. Para obtener más información acerca de las operaciones asincrónicas, vea [información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md).

La `HttpResponseMessage` clase representa un mensaje de respuesta http recibido del servicio Web después de que se haya realizado una solicitud HTTP. Contiene información sobre la respuesta, incluido el código de estado, los encabezados y cualquier cuerpo. La `HttpContent` clase representa el cuerpo http y los encabezados de contenido, como `Content-Type` y `Content-Encoding` . El contenido se puede leer mediante cualquiera de los `ReadAs` métodos, como `ReadAsStringAsync` y `ReadAsByteArrayAsync` , dependiendo del formato de los datos.

### <a name="create-the-httpclient-object"></a>Crear el objeto HTTPClient

La `HttpClient` instancia de se declara en el nivel de clase para que el objeto se encuentre siempre que la aplicación necesite hacer solicitudes HTTP, como se muestra en el ejemplo de código siguiente:

```csharp
public class RestService : IRestService
{
  HttpClient client;
  ...

  public RestService ()
  {
    client = new HttpClient ();
    ...
  }
  ...
}
```

### <a name="retrieve-data"></a>Recuperación de datos

El `HttpClient.GetAsync` método se usa para enviar la solicitud GET al servicio Web especificado por el URI y, a continuación, recibir la respuesta del servicio Web, como se muestra en el ejemplo de código siguiente:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  Uri uri = new Uri (string.Format (Constants.TodoItemsUrl, string.Empty));
  ...
  HttpResponseMessage response = await client.GetAsync (uri);
  if (response.IsSuccessStatusCode)
  {
      string content = await response.Content.ReadAsStringAsync ();
      Items = JsonSerializer.Deserialize<List<TodoItem>>(content, serializerOptions);
  }
  ...
}
```

El servicio REST envía un código de Estado HTTP en la `HttpResponseMessage.IsSuccessStatusCode` propiedad para indicar si la solicitud HTTP se ha realizado correctamente o no. Para esta operación, el servicio REST envía el código de Estado HTTP 200 (OK) en la respuesta, que indica que la solicitud se realizó correctamente y que la información solicitada se encuentra en la respuesta.

Si la operación HTTP se realizó correctamente, se lee el contenido de la respuesta para su presentación. La `HttpResponseMessage.Content` propiedad representa el contenido de la respuesta HTTP y el `HttpContent.ReadAsStringAsync` método escribe de forma asincrónica el contenido http en una cadena. Este contenido se deserializa a partir de JSON a un `List` de `TodoItem` instancias.

> [!WARNING]
> El uso del `ReadAsStringAsync` método para recuperar una respuesta grande puede tener un impacto negativo en el rendimiento. En tales circunstancias, la respuesta se debe deserializar directamente para evitar tener que almacenarla completamente en búfer.

### <a name="create-data"></a>Crear datos

El `HttpClient.PostAsync` método se usa para enviar la solicitud post al servicio Web especificado por el URI y, a continuación, para recibir la respuesta del servicio Web, como se muestra en el ejemplo de código siguiente:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  Uri uri = new Uri (string.Format (Constants.TodoItemsUrl, string.Empty));

  ...
  string json = JsonSerializer.Serialize<TodoItem>(item, serializerOptions);
  StringContent content = new StringContent (json, Encoding.UTF8, "application/json");

  HttpResponseMessage response = null;
  if (isNewItem)
  {
    response = await client.PostAsync (uri, content);
  }
  ...

  if (response.IsSuccessStatusCode)
  {
    Debug.WriteLine (@"\tTodoItem successfully saved.");
  }
  ...
}
```

La `TodoItem` instancia se serializa a una carga JSON para enviarla al servicio Web. Esta carga se inserta entonces en el cuerpo del contenido HTTP que se enviará al servicio Web antes de que se realice la solicitud con el `PostAsync` método.

El servicio REST envía un código de Estado HTTP en la `HttpResponseMessage.IsSuccessStatusCode` propiedad para indicar si la solicitud HTTP se ha realizado correctamente o no. Las respuestas comunes para esta operación son:

- **201 (creado)** : la solicitud dio lugar a la creación de un nuevo recurso antes de que se enviara la respuesta.
- **400 (solicitud incorrecta)** : el servidor no entiende la solicitud.
- **409 (conflicto)** : no se pudo realizar la solicitud debido a un conflicto en el servidor.

### <a name="update-data"></a>Actualización de datos

El `HttpClient.PutAsync` método se usa para enviar la solicitud Put al servicio Web especificado por el URI y, a continuación, recibir la respuesta del servicio Web, como se muestra en el ejemplo de código siguiente:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  response = await client.PutAsync (uri, content);
  ...
}
```

La operación del `PutAsync` método es idéntica al `PostAsync` método que se usa para crear los datos en el servicio Web. Sin embargo, las posibles respuestas enviadas desde el servicio Web difieren.

El servicio REST envía un código de Estado HTTP en la `HttpResponseMessage.IsSuccessStatusCode` propiedad para indicar si la solicitud HTTP se ha realizado correctamente o no. Las respuestas comunes para esta operación son:

- **204 (sin contenido)** : la solicitud se ha procesado correctamente y la respuesta está en blanco intencionadamente.
- **400 (solicitud incorrecta)** : el servidor no entiende la solicitud.
- **404 (no encontrado)** : el recurso solicitado no existe en el servidor.

### <a name="delete-data"></a>Eliminación de datos

El `HttpClient.DeleteAsync` método se usa para enviar la solicitud de eliminación al servicio Web especificado por el URI y, a continuación, recibir la respuesta del servicio Web, como se muestra en el ejemplo de código siguiente:

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  Uri uri = new Uri (string.Format (Constants.TodoItemsUrl, id));
  ...
  HttpResponseMessage response = await client.DeleteAsync (uri);
  if (response.IsSuccessStatusCode)
  {
    Debug.WriteLine (@"\tTodoItem successfully deleted.");
  }
  ...
}
```

El servicio REST envía un código de Estado HTTP en la `HttpResponseMessage.IsSuccessStatusCode` propiedad para indicar si la solicitud HTTP se ha realizado correctamente o no. Las respuestas comunes para esta operación son:

- **204 (sin contenido)** : la solicitud se ha procesado correctamente y la respuesta está en blanco intencionadamente.
- **400 (solicitud incorrecta)** : el servidor no entiende la solicitud.
- **404 (no encontrado)** : el recurso solicitado no existe en el servidor.

### <a name="local-development"></a>Desarrollo local

Si está desarrollando el servicio Web REST localmente con un marco de trabajo como ASP.NET Core API Web, puede depurar el servicio Web y la aplicación móvil al mismo tiempo. En este escenario, debe habilitar el tráfico HTTP de texto no cifrado para iOS Simualtor y el emulador de Android. Para obtener información sobre la configuración del proyecto para permitir la comunicación, consulte [conexión a servicios web locales](~/cross-platform/deploy-test/connect-to-local-web-services.md).

## <a name="related-links"></a>Vínculos relacionados

- [Microsoft Learn: consumir servicios web REST en aplicaciones Xamarin](/learn/modules/consume-rest-services/)
- [Microsoft Learn: Creación de una API web con ASP.NET Core](/learn/modules/build-web-api-aspnet-core/)
- [Creación de servicios back-end para aplicaciones móviles nativas](/aspnet/core/mobile/native-mobile-backend/)
- [Enrutamiento de atributos para las API de REST](/aspnet/core/mvc/controllers/routing?view=aspnetcore-5.0#ar)
- [TodoREST (ejemplo)](/samples/xamarin/xamarin-forms-samples/webservices-todorest)
- [API de HttpClient](xref:System.Net.Http.HttpClient)
- [Configuración de seguridad de red de Android](https://devblogs.microsoft.com/xamarin/cleartext-http-android-network-security/)
- [Seguridad de transporte de aplicación de iOS](~/ios/app-fundamentals/ats.md)
- [Conexión a servicios web locales](~/cross-platform/deploy-test/connect-to-local-web-services.md)
