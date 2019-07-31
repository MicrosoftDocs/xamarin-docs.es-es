---
title: Autenticación de usuarios con una base de datos de documentos Azure Cosmos DB y Xamarin. Forms
description: En este artículo se explica cómo combinar el control de acceso con las colecciones de Azure Cosmos DB con particiones, para que un usuario sólo puede tener acceso a sus propios documentos en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 11ED4A4C-0F05-40B2-AB06-5A0F2188EF3D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 0067a9e576e695a308e4326955b540be2ff46f61
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657228"
---
# <a name="authenticate-users-with-an-azure-cosmos-db-document-database-and-xamarinforms"></a>Autenticación de usuarios con una base de datos de documentos Azure Cosmos DB y Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)

_Azure bases de datos de documento de Cosmos DB admiten colecciones con particiones, que pueden abarcar varios servidores y particiones, al tiempo que admite almacenamiento ilimitado y rendimiento. En este artículo se explica cómo combinar el control de acceso con las colecciones con particiones, para que un usuario sólo puede tener acceso a sus propios documentos en una aplicación de Xamarin.Forms._

## <a name="overview"></a>Información general

Una clave de partición debe especificarse al crear una colección con particiones y documentos con la misma clave de partición se almacenarán en la misma partición. Por lo tanto, especificar la identidad del usuario como una clave de partición dará como resultado de una colección con particiones que sólo van a almacenar documentos de ese usuario. Esto también garantiza que la base de datos de documentos de Azure Cosmos DB se escalará a medida el número de usuarios y aumentar los elementos.

Se debe conceder acceso a cualquier colección, y el modelo de control de acceso de API de SQL define dos tipos de construcciones de acceso:

- **Las claves maestras** habilitar el acceso administrativo completo a todos los recursos dentro de una cuenta de Cosmos DB y se crean cuando se crea una cuenta de Cosmos DB.
- **Los tokens de recursos** capturan la relación entre el usuario de una base de datos y el permiso el usuario tiene para un recurso específico de Cosmos DB, como una colección o un documento.

Exponer una clave maestra, abre una cuenta de Cosmos DB a la posibilidad de un uso negligente o malintencionado. Sin embargo, los tokens de recursos de Azure Cosmos DB proporcionan un mecanismo seguro para permitir que los clientes leer, escribir y eliminar recursos específicos en una cuenta de Azure Cosmos DB según los permisos concedidos.

Un enfoque típico para solicitar, generar y entregar tokens de recursos a una aplicación móvil es usar a un agente de token de recurso. El siguiente diagrama muestra una descripción general de cómo la aplicación de ejemplo usa a un agente de tokens de recursos para administrar el acceso a los datos de la base de datos de documento:

![](azure-cosmosdb-auth-images/documentdb-authentication.png "Proceso de autenticación de base de datos de documento")

La resource token broker es un servicio de API Web de nivel intermedio, hospedado en Azure App Service, que tiene la clave maestra de la cuenta de Cosmos DB. La aplicación de ejemplo usa a la resource token broker para administrar el acceso a la base de datos de documento como sigue:

1. En el inicio de sesión, la aplicación de Xamarin.Forms se pone en contacto con Azure App Service para iniciar un flujo de autenticación.
1. Azure App Service realiza un flujo de autenticación de OAuth con Facebook. Una vez que se complete el flujo de autenticación, la aplicación de Xamarin.Forms recibe un token de acceso.
1. La aplicación de Xamarin.Forms utiliza el token de acceso para solicitar un token de recurso de la resource token broker.
1. La resource token broker usa el token de acceso para solicitar la identidad del usuario de Facebook. La identidad del usuario, a continuación, se usa para solicitar un token de recurso de Cosmos DB, que se usa para conceder acceso de lectura/escritura a la colección con particiones del usuario autenticado.
1. La aplicación de Xamarin.Forms utiliza el token de recurso para acceder directamente a los recursos de Cosmos DB con los permisos definidos por el token de recurso.

> [!NOTE]
> Cuando expira el token de recurso, las solicitudes de base de datos de documento posterior recibirá una excepción 401 no autorizado. En este momento, las aplicaciones de Xamarin.Forms deben volver a establecer la identidad y solicitar un nuevo token de recurso.

Para obtener más información acerca de la partición de Cosmos DB, consulte [partición y escalado en Azure Cosmos DB](/azure/cosmos-db/partition-data/). Para obtener más información acerca del control de acceso de Cosmos DB, consulte [protección del acceso a datos de Cosmos DB](/azure/cosmos-db/secure-access-to-data/) y [control de acceso en la API de SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).

## <a name="setup"></a>Programa de instalación

El proceso para integrar a la resource token broker en una aplicación de Xamarin.Forms es como sigue:

1. Cree una cuenta de Cosmos DB que se va a usar el control de acceso. Para obtener más información, consulte [Cosmos DB configuración](#cosmosdb_configuration).
1. Cree un Azure App Service para hospedar a la resource token broker. Para obtener más información, consulte [configuración de Azure App Service](#app_service_configuration).
1. Creación de una aplicación de Facebook para realizar la autenticación. Para obtener más información, consulte [Facebook App Configuration](#facebook_configuration).
1. Configurar el servicio de aplicación de Azure para llevar a cabo la autenticación con Facebook fácil. Para obtener más información, consulte [configuración de autenticación de Azure App Service](#app_service_authentication_configuration).
1. Configure la aplicación de ejemplo de Xamarin.Forms para comunicarse con Azure App Service y Cosmos DB. Para obtener más información, consulte [configuración de la aplicación de Xamarin.Forms](#forms_application_configuration).

<a name="cosmosdb_configuration" />

### <a name="azure-cosmos-db-configuration"></a>Configuración de Azure Cosmos DB

El proceso de creación de una cuenta de Cosmos DB que se va a usar el control de acceso es como sigue:

1. Cree una cuenta de Cosmos DB. Para obtener más información, consulte [crear una cuenta de Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account).
1. En la cuenta de Cosmos DB, crear una nueva colección denominada `UserItems`, especificar una clave de partición de `/userid`.

<a name="app_service_configuration" />

### <a name="azure-app-service-configuration"></a>Configuración de Azure App Service

El proceso para hospedar a la resource token broker en Azure App Service es como sigue:

1. En el portal de Azure, cree una nueva aplicación web de App Service. Para obtener más información, consulte [crear una aplicación web en un entorno de App Service](/azure/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase/).
1. En el portal de Azure, abra la hoja de configuración de la aplicación de la aplicación web y agregue la siguiente configuración:
    - `accountUrl` : el valor debe ser la dirección URL de cuenta de Cosmos DB en la hoja de claves de la cuenta de Cosmos DB.
    - `accountKey` : el valor debe ser la clave maestra para Cosmos DB (principal o secundaria) en la hoja de claves de la cuenta de Cosmos DB.
    - `databaseId` : el valor debe ser el nombre de la base de datos de Cosmos DB.
    - `collectionId` : el valor debe ser el nombre de la colección de Cosmos DB (en este caso, `UserItems`).
    - `hostUrl` : el valor debe ser la dirección URL de la aplicación web en la hoja de información general de la cuenta de servicio de aplicación.

    Captura de pantalla siguiente muestra esta configuración:

    [![](azure-cosmosdb-auth-images/azure-web-app-settings.png "Configuración de aplicación Web de App Service")](azure-cosmosdb-auth-images/azure-web-app-settings-large.png#lightbox "configuración de aplicación Web de App Service")

1. Publicar la solución de resource token broker en la aplicación web de Azure App Service.

<a name="facebook_configuration" />

### <a name="facebook-app-configuration"></a>Configuración de la aplicación de Facebook

El proceso de creación de una aplicación de Facebook para realizar la autenticación es el siguiente:

1. Creación de una aplicación de Facebook. Para obtener más información, consulte [registrar y configurar una aplicación](https://developers.facebook.com/docs/apps/register) en el Centro para desarrolladores de Facebook.
1. Agregar el producto de inicio de sesión de Facebook a la aplicación. Para obtener más información, consulte [agregar inicio de sesión de Facebook a la aplicación o sitio Web](https://developers.facebook.com/docs/facebook-login) en el Centro para desarrolladores de Facebook.
1. Configurar el inicio de sesión de Facebook como sigue:
   - Habilitar el inicio de sesión de cliente OAuth.
   - Habilitar el inicio de sesión de OAuth de Web.
   - Establece el URI para el URI de la aplicación web de App Service, de redirección de OAuth válido con `/.auth/login/facebook/callback` anexado.

  Captura de pantalla siguiente muestra esta configuración:

  ![](azure-cosmosdb-auth-images/facebook-oauth-settings.png "Configuración de inicio de sesión de Facebook OAuth")

Para obtener más información, consulte [registrar la aplicación con Facebook](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-nameregister-aregister-your-application-with-facebook).

<a name="app_service_authentication_configuration" />

### <a name="azure-app-service-authentication-configuration"></a>Configuración de autenticación de Azure App Service

El proceso de configuración de autenticación fácil de App Service es como sigue:

1. En el Portal de Azure, vaya a la aplicación web de App Service.
1. En el Portal de Azure, abra la autenticación / hoja de autorización y realizar la configuración siguiente:
    - Autenticación de App Service debe estar activada.
    - La acción que se realizará cuando una solicitud no está autenticada debe establecerse en **inicio de sesión con Facebook**.

    Captura de pantalla siguiente muestra esta configuración:

    [![](azure-cosmosdb-auth-images/app-service-authentication-settings.png "Configuración de autenticación de App Service Web App")](azure-cosmosdb-auth-images/app-service-authentication-settings-large.png#lightbox "configuración de autenticación de App Service Web App")

La aplicación web de App Service también debe configurarse para comunicarse con la aplicación de Facebook para habilitar el flujo de autenticación. Esto puede realizarse seleccionando el proveedor de identidades de Facebook, y especificando el **Id. de aplicación** y **secreto de la aplicación** los valores de la configuración de la aplicación de Facebook en el Centro para desarrolladores de Facebook. Para obtener más información, consulte [Facebook agregar información a la aplicación](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-namesecrets-aadd-facebook-information-to-your-application).

<a name="forms_application_configuration" />

### <a name="xamarinforms-application-configuration"></a>Configuración de la aplicación de Xamarin.Forms

El proceso para configurar la aplicación de ejemplo de Xamarin.Forms es como sigue:

1. Abra la solución Xamarin.Forms.
1. Abra `Constants.cs` y actualice los valores de las siguientes constantes:
    - `EndpointUri` : el valor debe ser la dirección URL de cuenta de Cosmos DB en la hoja de claves de la cuenta de Cosmos DB.
    - `DatabaseName` : el valor debe ser el nombre de la base de datos del documento.
    - `CollectionName` : el valor debe ser el nombre de la colección de base de datos de documentos (en este caso, `UserItems`).
    - `ResourceTokenBrokerUrl` : el valor debe ser la dirección URL de la aplicación de web resource token broker en la hoja de información general de la cuenta de servicio de aplicación.

## <a name="initiating-login"></a>Iniciando el inicio de sesión

La aplicación de ejemplo inicia el proceso de inicio de sesión mediante el uso de Xamarin.Auth para redirigir un explorador a una URL de proveedor de identidades, como se muestra en el ejemplo de código siguiente:

```csharp
var auth = new Xamarin.Auth.WebRedirectAuthenticator(
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/facebook"),
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/done"));
```

Esto hace que un flujo de autenticación de OAuth que se inicie entre Azure App Service y Facebook, que muestra la página de inicio de sesión de Facebook:

![](azure-cosmosdb-auth-images/login.png "Inicio de sesión de Facebook")

Se puede cancelar el inicio de sesión presionando el **cancelar** botón en iOS o presionando el **volver** botón en Android, en cuyo caso permanece sin autenticar el usuario y la interfaz de usuario del proveedor de identidad es Quita de la pantalla.

Para obtener más información acerca de Xamarin.Auth, consulte [la autenticación de usuarios con un proveedor de identidades](~/xamarin-forms/data-cloud/authentication/oauth.md).

## <a name="obtaining-a-resource-token"></a>Obtener un Token de recurso

Después de autenticarse correctamente, el `WebRedirectAuthenticator.Completed` desencadena el evento. En el ejemplo de código siguiente se muestra cómo controlar este evento:

```csharp
auth.Completed += async (sender, e) =>
{
  if (e.IsAuthenticated && e.Account.Properties.ContainsKey("token"))
  {
    var easyAuthResponseJson = JsonConvert.DeserializeObject<JObject>(e.Account.Properties["token"]);
    var easyAuthToken = easyAuthResponseJson.GetValue("authenticationToken").ToString();

    // Call the ResourceBroker to get the resource token
    using (var httpClient = new HttpClient())
    {
      httpClient.DefaultRequestHeaders.Add("x-zumo-auth", easyAuthToken);
      var response = await httpClient.GetAsync(Constants.ResourceTokenBrokerUrl + "/api/resourcetoken/");
      var jsonString = await response.Content.ReadAsStringAsync();
      var tokenJson = JsonConvert.DeserializeObject<JObject>(jsonString);
      resourceToken = tokenJson.GetValue("token").ToString();
      UserId = tokenJson.GetValue("userid").ToString();

      if (!string.IsNullOrWhiteSpace(resourceToken))
      {
        client = new DocumentClient(new Uri(Constants.EndpointUri), resourceToken);
        ...
      }
      ...
    }
  }
};
```

El resultado de una autenticación correcta es un token de acceso, que está disponible `AuthenticatorCompletedEventArgs.Account` propiedad. El token de acceso se extrae y se utiliza en una solicitud GET en del resource token broker `resourcetoken` API.

El `resourcetoken` API usa el token de acceso para solicitar la identidad del usuario de Facebook, que sirve para solicitar un token de recurso de Cosmos DB. Si ya existe un documento de permiso válido para el usuario en la base de datos de documento, se recupera y se devuelve un documento JSON que contiene el token de recurso a la aplicación de Xamarin.Forms. Si no existe un documento de permiso válido para el usuario, se crea un usuario y un permiso en la base de datos de documento y el token de recurso se extraen el documento de permiso y se devuelve a la aplicación de Xamarin.Forms en un documento JSON.

> [!NOTE]
> Un usuario de base de datos de documento es un recurso asociado con una base de datos de documento, y cada base de datos puede contener cero o más usuarios. Un permiso de base de datos de documento es un recurso asociado con un usuario de base de datos de documento y cada usuario puede contener cero o más permisos. Un recurso de permiso proporciona acceso a un token de seguridad que requiere que el usuario cuando se intenta tener acceso a un recurso como un documento.

Si el `resourcetoken` API se completa correctamente, enviará el código de estado HTTP 200 (OK) en la respuesta, junto con un documento JSON que contiene el token de recurso. Los siguientes datos JSON muestran un mensaje de respuesta correcta típico:

```csharp
{
  "id": "John Smithpermission",
  "token": "type=resource&ver=1&sig=zx6k2zzxqktzvuzuku4b7y==;a74aukk99qtwk8v5rxfrfz7ay7zzqfkbfkremrwtaapvavw2mrvia4umbi/7iiwkrrq+buqqrzkaq4pp15y6bki1u//zf7p9x/aefbvqvq3tjjqiffurfx+vexa1xarxkkv9rbua9ypfzr47xpp5vmxuvzbekkwq6txme0xxxbjhzaxbkvzaji+iru3xqjp05amvq1r1q2k+qrarurhmjzah/ha0evixazkve2xk1zu9u/jpyf1xrwbkxqpzebvqwma+hyyaazemr6qx9uz9be==;",
  "expires": 4035948,
  "userid": "John Smith"
}
```

El `WebRedirectAuthenticator.Completed` controlador de eventos lee la respuesta desde el `resourcetoken` API y extrae el token de recurso y el identificador de usuario. El token de recurso, a continuación, se pasa como argumento a la `DocumentClient` constructor, que encapsula el punto de conexión, credenciales y directiva de conexión utilizada para acceder a Cosmos DB y se usa para configurar y ejecutar solicitudes en Cosmos DB. El token de recurso se envía con cada solicitud para acceder directamente a un recurso e indica que se concede acceso de lectura/escritura a la colección con particiones de los usuarios autenticados.

## <a name="retrieving-documents"></a>Recuperación de documentos

Recuperar documentos que pertenecen sólo al usuario autenticado puede lograrse mediante la creación de una consulta de documento que incluye el identificador del usuario como clave de partición y se muestra en el ejemplo de código siguiente:

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink,
                        new FeedOptions
                        {
                          MaxItemCount = -1,
                          PartitionKey = new PartitionKey(UserId)
                        })
          .Where(item => !item.Id.Contains("permission"))
          .AsDocumentQuery();
while (query.HasMoreResults)
{
  Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
}
```

La consulta recupera todos los documentos que pertenecen al usuario autenticado, de la colección especificada, asincrónicamente y los coloca en un `List<TodoItem>` colección para su presentación.

El `CreateDocumentQuery<T>` método especifica un `Uri` argumento que representa la colección que se debe consultar documentos, y un `FeedOptions` objeto. La `FeedOptions` objeto especifica que se puede devolver un número ilimitado de elementos por la consulta y el identificador del usuario como clave de partición. Esto garantiza que se devuelven únicamente los documentos en la colección del usuario con particiones en el resultado.

> [!NOTE]
> Tenga en cuenta que los documentos de permiso, que son creados por la resource token broker, se almacenan en la misma colección de documentos que los documentos creados por la aplicación de Xamarin.Forms. Por lo tanto, la consulta de documento contiene un `Where` cláusula que se aplica un predicado de filtrado a la consulta en la colección de documentos. Esta cláusula garantiza que los documentos de permiso no se devuelven de la colección de documentos.

Para obtener más información acerca de cómo recuperar documentos desde una colección de documentos, consulte [recuperar documentos de colección de documentos](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#document_query).

## <a name="inserting-documents"></a>Inserción de documentos

Antes de insertar un documento en una colección de documentos, el `TodoItem.UserId` propiedad debe actualizarse con el valor utilizado como clave de partición, como se muestra en el ejemplo de código siguiente:

```csharp
item.UserId = UserId;
await client.CreateDocumentAsync(collectionLink, item);
```

Esto garantiza que se insertará el documento en la colección del usuario con particiones.

Para obtener más información sobre cómo insertar un documento en una colección de documentos, consulte [insertar documentos en una colección de documentos](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#inserting_document).

## <a name="deleting-documents"></a>Eliminación de documentos

El valor de clave de partición debe especificarse al eliminar un documento de una colección con particiones, como se muestra en el ejemplo de código siguiente:

```csharp
await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id),
                 new RequestOptions
                 {
                   PartitionKey = new PartitionKey(UserId)
                 });
```

Esto garantiza que Cosmos DB sabe que particiones colección para eliminar el documento de.

Para obtener más información sobre cómo eliminar un documento de una colección de documentos, consulte [eliminar un documento de una colección de documentos](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#deleting_document).

## <a name="summary"></a>Resumen

En este artículo se explica cómo combinar el control de acceso con las colecciones con particiones, para que un usuario sólo puede tener acceso a sus propios documentos de base de datos de documento en una aplicación de Xamarin.Forms. Especificar la identidad del usuario como una clave de partición garantiza que una colección con particiones solo puede almacenar documentos de ese usuario.


## <a name="related-links"></a>Vínculos relacionados

- [Todo Azure Cosmos DB Auth (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)
- [Consumo de una base de datos de documentos de Cosmos Azure DB](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md)
- [Protección del acceso a datos de Azure Cosmos DB](/azure/cosmos-db/secure-access-to-data/)
- [Control de acceso en la API de SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).
- [Cómo crear particiones y escalado en Azure Cosmos DB](/azure/cosmos-db/partition-data/)
- [Biblioteca de cliente de Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API de Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn948556.aspx)
