---
title: Autenticar usuarios con una base de datos de documentos de Azure Cosmos DB y Xamarin.Forms
description: En este artículo se explica cómo combinar el control de acceso con colecciones con particiones de Azure Cosmos DB, para que un usuario solo pueda tener acceso a sus propios documentos en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 11ED4A4C-0F05-40B2-AB06-5A0F2188EF3D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 6e79e647d64103b6d257de7233f488899bcaff40
ms.sourcegitcommit: 2a8eb8bce427e72d4e7edd06ce432e19f17dcdd7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388608"
---
# <a name="authenticate-users-with-an-azure-cosmos-db-document-database-and-xamarinforms"></a>Autenticar usuarios con una base de datos de documentos de Azure Cosmos DB y Xamarin.Forms

[![Descargar](~/media/shared/download.png) ejemplo Descargue el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)

_Las bases de datos de documentos de Azure Cosmos DB admiten colecciones con particiones, que pueden abarcar varios servidores y particiones, al tiempo que admiten almacenamiento y rendimiento ilimitados. En este artículo se explica cómo combinar el control de acceso con colecciones con particiones, para que un usuario solo pueda tener acceso a sus propios documentos en una aplicación de Xamarin.Forms._

## <a name="overview"></a>Información general

Se debe especificar una clave de partición al crear una colección con particiones y los documentos con la misma clave de partición se almacenarán en la misma partición. Por lo tanto, si se especifica la identidad del usuario como clave de partición, se producirá una colección con particiones que solo almacenará documentos para ese usuario. Esto también garantiza que la base de datos de documentos de Azure Cosmos DB se escalará a medida que aumente el número de usuarios y elementos.

Se debe conceder acceso a cualquier colección y el modelo de control de acceso de la API de SQL define dos tipos de construcciones de acceso:

- **Las claves maestras** permiten el acceso administrativo completo a todos los recursos de una cuenta de Cosmos DB y se crean cuando se crea una cuenta de Cosmos DB.
- **Los tokens** de recursos capturan la relación entre el usuario de una base de datos y el permiso que el usuario tiene para un recurso de Cosmos DB específico, como una colección o un documento.

Al exponer una clave maestra, se abre una cuenta de Cosmos DB la posibilidad de uso malintencionado o negligente. Sin embargo, los tokens de recursos de Azure Cosmos DB proporcionan un mecanismo seguro para permitir que los clientes lean, escriban y eliminen recursos específicos en una cuenta de Azure Cosmos DB según los permisos concedidos.

Un enfoque típico para solicitar, generar y entregar tokens de recursos a una aplicación móvil es utilizar un agente de tokens de recursos. En el diagrama siguiente se muestra una visión general de alto nivel de cómo la aplicación de ejemplo utiliza un agente de tokens de recursos para administrar el acceso a los datos de la base de datos de documentos:

![](azure-cosmosdb-auth-images/documentdb-authentication.png "Document Database Authentication Process")

El agente de tokens de recursos es un servicio de API web de nivel intermedio, hospedado en Azure App Service, que posee la clave maestra de la cuenta de Cosmos DB. La aplicación de ejemplo utiliza el agente de tokens de recursos para administrar el acceso a los datos de la base de datos de documentos de la siguiente manera:

1. Al iniciar sesión, la aplicación Xamarin.Forms se pone en contacto con Azure App Service para iniciar un flujo de autenticación.
1. Azure App Service realiza un flujo de autenticación de OAuth con Facebook. Una vez completado el flujo de autenticación, la aplicación Xamarin.Forms recibe un token de acceso.
1. La aplicación Xamarin.Forms usa el token de acceso para solicitar un token de recurso del agente de token de recursos.
1. El agente de tokens de recursos utiliza el token de acceso para solicitar la identidad del usuario a Facebook. A continuación, la identidad del usuario se usa para solicitar un token de recurso de Cosmos DB, que se usa para conceder acceso de lectura y escritura a la colección con particiones del usuario autenticado.
1. La aplicación Xamarin.Forms usa el token de recurso para tener acceso directamente a los recursos de Cosmos DB con los permisos definidos por el token de recurso.

> [!NOTE]
> Cuando expire el token de recurso, las solicitudes de base de datos de documentos posteriores recibirán una excepción no autorizada 401. En este momento, las aplicaciones de Xamarin.Forms deben restablecer la identidad y solicitar un nuevo token de recurso.

Para obtener más información acerca de las particiones de Cosmos DB, consulte [Cómo particionar y escalar en Azure Cosmos DB](/azure/cosmos-db/partition-data/). Para obtener más información sobre el control de acceso de Cosmos DB, consulte [Protección](/azure/cosmos-db/secure-access-to-data/) del acceso a los datos de Cosmos DB y control de [acceso en la API de SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).

## <a name="setup"></a>Configurar

El proceso para integrar el agente de token de recursos en una aplicación de Xamarin.Forms es el siguiente:

1. Cree una cuenta de Cosmos DB que use el control de acceso. Para obtener más información, consulte Configuración de [Cosmos DB](#cosmosdb_configuration).
1. Cree un servicio de aplicaciones de Azure para hospedar el agente de tokens de recursos. Para obtener más información, consulte Configuración de [Azure App Service](#app_service_configuration).
1. Crea una aplicación de Facebook para realizar la autenticación. Para obtener más información, consulte [Configuración de](#facebook_configuration)la aplicación de Facebook .
1. Configure Azure App Service para realizar una autenticación sencilla con Facebook. Para obtener más información, consulte Configuración de autenticación de [Azure App Service](#app_service_authentication_configuration).
1. Configure la aplicación de ejemplo Xamarin.Forms para comunicarse con Azure App Service y Cosmos DB. Para obtener más información, vea Configuración de la [aplicación Xamarin.Forms](#forms_application_configuration).

> [!NOTE]
> Si no tiene una suscripción a [Azure,](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)cree una [cuenta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de empezar.

<a name="cosmosdb_configuration" />

### <a name="azure-cosmos-db-configuration"></a>Configuración de Azure Cosmos DB

El proceso para crear una cuenta de Cosmos DB que usará el control de acceso es el siguiente:

1. Cree una cuenta de Cosmos DB. Para obtener más información, consulte Crear una cuenta de [Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account).
1. En la cuenta de Cosmos DB, cree una nueva colección denominada `UserItems`, especificando una clave de partición de `/userid`.

<a name="app_service_configuration" />

### <a name="azure-app-service-configuration"></a>Configuración de Azure App Service

El proceso para hospedar el agente de tokens de recursos en Azure App Service es el siguiente:

1. En Azure Portal, cree una nueva aplicación web de App Service. Para obtener más información, vea [Crear una aplicación web en un](/azure/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase/)entorno de App Service .
1. En Azure Portal, abra la hoja Configuración de la aplicación para la aplicación web y agregue la siguiente configuración:
    - `accountUrl`: el valor debe ser la dirección URL de la cuenta de Cosmos DB de la hoja Claves de la cuenta de Cosmos DB.
    - `accountKey`: el valor debe ser la clave maestra de Cosmos DB (primaria o secundaria) de la hoja Claves de la cuenta de Cosmos DB.
    - `databaseId`– el valor debe ser el nombre de la base de datos de Cosmos DB.
    - `collectionId`– el valor debe ser el nombre de la `UserItems`colección de Cosmos DB (en este caso, ).
    - `hostUrl`– el valor debe ser la dirección URL de la aplicación web de la hoja Información general de la cuenta de App Service.

    La siguiente captura de pantalla muestra esta configuración:

    [![](azure-cosmosdb-auth-images/azure-web-app-settings.png "App Service Web App Settings")](azure-cosmosdb-auth-images/azure-web-app-settings-large.png#lightbox "App Service Web App Settings")

1. Publique la solución de agente de tokens de recursos en la aplicación web de Azure App Service.

<a name="facebook_configuration" />

### <a name="facebook-app-configuration"></a>Configuración de la aplicación de Facebook

El proceso para crear una aplicación de Facebook para realizar la autenticación es el siguiente:

1. Crea una aplicación de Facebook. Para obtener más información, consulta [Registrar y configurar una aplicación](https://developers.facebook.com/docs/apps/register) en el Centro para desarrolladores de Facebook.
1. Agrega el producto de inicio de sesión de Facebook a la aplicación. Para obtener más información, consulta Añadir inicio de [sesión](https://developers.facebook.com/docs/facebook-login) de Facebook a tu aplicación o sitio web en el Centro para desarrolladores de Facebook.
1. Configura el inicio de sesión de Facebook de la siguiente manera:
   - Habilite el inicio de sesión de OAuth de cliente.
   - Habilite el inicio de sesión de Web OAuth.
   - Establezca el URI de redirección de OAuth válido `/.auth/login/facebook/callback` en el URI de la aplicación web de App Service, anexado.

  La siguiente captura de pantalla muestra esta configuración:

  ![](azure-cosmosdb-auth-images/facebook-oauth-settings.png "Facebook Login OAuth Settings")

Para obtener más información, consulta [Registrar la aplicación con Facebook](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-nameregister-aregister-your-application-with-facebook).

<a name="app_service_authentication_configuration" />

### <a name="azure-app-service-authentication-configuration"></a>Configuración de la autenticación de Azure App Service

El proceso para configurar la autenticación sencilla de App Service es el siguiente:

1. En Azure Portal, vaya a la aplicación web de App Service.
1. En Azure Portal, abra la hoja Autenticación/Autorización y realice la siguiente configuración:
    - La autenticación de App Service debe estar activada.
    - La acción que se debe realizar cuando una solicitud no está autenticada debe establecerse **en Iniciar sesión con Facebook**.

    La siguiente captura de pantalla muestra esta configuración:

    [![](azure-cosmosdb-auth-images/app-service-authentication-settings.png "App Service Web App Authentication Settings")](azure-cosmosdb-auth-images/app-service-authentication-settings-large.png#lightbox "App Service Web App Authentication Settings")

La aplicación web de App Service también debe configurarse para comunicarse con la aplicación de Facebook para habilitar el flujo de autenticación. Esto se puede lograr seleccionando el proveedor de identidades de Facebook e introduciendo los valores **de APP ID** y App **Secret** en la configuración de la aplicación de Facebook en el Centro para desarrolladores de Facebook. Para obtener más información, consulta Agregar información de [Facebook a la aplicación.](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-namesecrets-aadd-facebook-information-to-your-application)

<a name="forms_application_configuration" />

### <a name="xamarinforms-application-configuration"></a>Configuración de la aplicación de Xamarin.Forms

El proceso para configurar la aplicación de ejemplo xamarin.Forms es el siguiente:

1. Abra la solución Xamarin.Forms.
1. Abra `Constants.cs` y actualice los valores de las siguientes constantes:
    - `EndpointUri`: el valor debe ser la dirección URL de la cuenta de Cosmos DB de la hoja Claves de la cuenta de Cosmos DB.
    - `DatabaseName`– el valor debe ser el nombre de la base de datos de documentos.
    - `CollectionName`– el valor debe ser el nombre de la `UserItems`colección de bases de datos de documentos (en este caso, ).
    - `ResourceTokenBrokerUrl`: el valor debe ser la dirección URL de la aplicación web del agente de tokens de recursos de la hoja Información general de la cuenta de App Service.

## <a name="initiating-login"></a>Iniciar el inicio de sesión

La aplicación de ejemplo inicia el proceso de inicio de sesión redireccionando un explorador a una dirección URL del proveedor de identidades, como se muestra en el siguiente código de ejemplo:

```csharp
var auth = new Xamarin.Auth.WebRedirectAuthenticator(
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/facebook"),
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/done"));
```

Esto hace que se inicie un flujo de autenticación de OAuth entre Azure App Service y Facebook, que muestra la página de inicio de sesión de Facebook:

![](azure-cosmosdb-auth-images/login.png "Facebook Login")

El inicio de sesión se puede cancelar pulsando el botón **Cancelar** en iOS o pulsando el botón **Atrás** en Android, en cuyo caso el usuario permanece sin autenticar y la interfaz de usuario del proveedor de identidades se elimina de la pantalla.

## <a name="obtaining-a-resource-token"></a>Obtención de un token de recurso

Tras la autenticación `WebRedirectAuthenticator.Completed` correcta, se desencadena el evento. En el ejemplo de código siguiente se muestra cómo controlar este evento:

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

El resultado de una autenticación correcta es `AuthenticatorCompletedEventArgs.Account` un token de acceso, que es propiedad disponible. El token de acceso se extrae y se utiliza `resourcetoken` en una solicitud GET a la API del agente de tokens de recursos.

La `resourcetoken` API usa el token de acceso para solicitar la identidad del usuario a Facebook, que a su vez se usa para solicitar un token de recurso de Cosmos DB. Si ya existe un documento de permiso válido para el usuario en la base de datos de documentos, se recupera y se devuelve un documento JSON que contiene el token de recurso a la aplicación Xamarin.Forms. Si no existe un documento de permiso válido para el usuario, se crea un usuario y un permiso en la base de datos de documentos y el token de recurso se extrae del documento de permiso y se devuelve a la aplicación Xamarin.Forms en un documento JSON.

> [!NOTE]
> Un usuario de base de datos de documentos es un recurso asociado a una base de datos de documentos y cada base de datos puede contener cero o más usuarios. Un permiso de base de datos de documentos es un recurso asociado a un usuario de base de datos de documentos y cada usuario puede contener cero o más permisos. Un recurso de permiso proporciona acceso a un token de seguridad que el usuario requiere al intentar tener acceso a un recurso como un documento.

Si `resourcetoken` la API se completa correctamente, enviará el código de estado HTTP 200 (OK) en la respuesta, junto con un documento JSON que contenga el token de recurso. Los siguientes datos JSON muestran un mensaje de respuesta correcto típico:

```csharp
{
  "id": "John Smithpermission",
  "token": "type=resource&ver=1&sig=zx6k2zzxqktzvuzuku4b7y==;a74aukk99qtwk8v5rxfrfz7ay7zzqfkbfkremrwtaapvavw2mrvia4umbi/7iiwkrrq+buqqrzkaq4pp15y6bki1u//zf7p9x/aefbvqvq3tjjqiffurfx+vexa1xarxkkv9rbua9ypfzr47xpp5vmxuvzbekkwq6txme0xxxbjhzaxbkvzaji+iru3xqjp05amvq1r1q2k+qrarurhmjzah/ha0evixazkve2xk1zu9u/jpyf1xrwbkxqpzebvqwma+hyyaazemr6qx9uz9be==;",
  "expires": 4035948,
  "userid": "John Smith"
}
```

El `WebRedirectAuthenticator.Completed` controlador de eventos lee `resourcetoken` la respuesta de la API y extrae el token de recurso y el identificador de usuario. A continuación, el `DocumentClient` token de recurso se pasa como argumento al constructor, que encapsula el punto de conexión, las credenciales y la directiva de conexión que se usan para tener acceso a Cosmos DB, y se usa para configurar y ejecutar solicitudes en Cosmos DB. El token de recurso se envía con cada solicitud para tener acceso directamente a un recurso e indica que se concede acceso de lectura y escritura a la colección con particiones de los usuarios autenticados.

## <a name="retrieving-documents"></a>Recuperación de documentos

La recuperación de documentos que solo pertenecen al usuario autenticado se puede lograr mediante la creación de una consulta de documento que incluya el identificador del usuario como clave de partición y se muestre en el ejemplo de código siguiente:

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

La consulta recupera de forma asincrónica todos los documentos que pertenecen al usuario `List<TodoItem>` autenticado, de la colección especificada y los coloca en una colección para su presentación.

El `CreateDocumentQuery<T>` método especifica `Uri` un argumento que representa la colección que `FeedOptions` se debe consultar para los documentos y un objeto. El `FeedOptions` objeto especifica que la consulta puede devolver un número ilimitado de elementos y el identificador del usuario como clave de partición. Esto garantiza que solo se devuelven los documentos de la colección con particiones del usuario en el resultado.

> [!NOTE]
> Tenga en cuenta que los documentos de permisos, que son creados por el agente de token de recursos, se almacenan en la misma colección de documentos que los documentos creados por la aplicación Xamarin.Forms. Por lo tanto, `Where` la consulta de documento contiene una cláusula que aplica un predicado de filtrado a la consulta en la colección de documentos. Esta cláusula garantiza que los documentos de permisos no se devuelven de la colección de documentos.

Para obtener más información sobre cómo recuperar documentos de una colección de [documentos,](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#document_query)vea Recuperar documentos de recopilación de documentos .

## <a name="inserting-documents"></a>Inserción de documentos

Antes de insertar un documento en `TodoItem.UserId` una colección de documentos, la propiedad debe actualizarse con el valor que se utiliza como clave de partición, como se muestra en el ejemplo de código siguiente:

```csharp
item.UserId = UserId;
await client.CreateDocumentAsync(collectionLink, item);
```

Esto garantiza que el documento se insertará en la colección con particiones del usuario.

Para obtener más información sobre cómo insertar un documento en una colección de documentos, vea [Insertar un documento en una colección](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#inserting_document)de documentos .

## <a name="deleting-documents"></a>Eliminación de documentos

El valor de clave de partición debe especificarse al eliminar un documento de una colección con particiones, como se muestra en el ejemplo de código siguiente:

```csharp
await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id),
                 new RequestOptions
                 {
                   PartitionKey = new PartitionKey(UserId)
                 });
```

Esto garantiza que Cosmos DB sepa de qué colección con particiones se va a eliminar el documento.

Para obtener más información sobre cómo eliminar un documento de una colección de documentos, vea [Eliminar un documento de una colección](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#deleting_document)de documentos .

## <a name="summary"></a>Resumen

En este artículo se explica cómo combinar el control de acceso con colecciones con particiones, para que un usuario solo pueda tener acceso a sus propios documentos de base de datos de documentos en una aplicación de Xamarin.Forms. Especificar la identidad del usuario como clave de partición garantiza que una colección con particiones solo puede almacenar documentos para ese usuario.

## <a name="related-links"></a>Vínculos relacionados

- [Todo Azure Cosmos DB Auth (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)
- [Consumo de una base de datos de documentos de Cosmos Azure DB](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md)
- [Protección del acceso a los datos de Azure Cosmos DB](/azure/cosmos-db/secure-access-to-data/)
- [Control de acceso en la API de SQL.](/rest/api/documentdb/access-control-on-documentdb-resources/)
- [Cómo particionar y escalar en Azure Cosmos DB](/azure/cosmos-db/partition-data/)
- [Biblioteca de cliente de Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API de Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn948556.aspx)
