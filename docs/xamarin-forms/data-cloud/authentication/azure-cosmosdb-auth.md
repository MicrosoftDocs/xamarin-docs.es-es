---
title: Autentique a los usuarios con una base de datos de documentos Azure Cosmos DB yXamarin.Forms
description: En este artículo se explica cómo combinar el control de acceso con Azure Cosmos DB colecciones con particiones, de modo que un usuario solo pueda tener acceso a sus propios documentos en una Xamarin.Forms aplicación.
ms.prod: xamarin
ms.assetid: 11ED4A4C-0F05-40B2-AB06-5A0F2188EF3D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 05547e960ba1ea141a830396f803dfc265283627
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936466"
---
# <a name="authenticate-users-with-an-azure-cosmos-db-document-database-and-xamarinforms"></a>Autentique a los usuarios con una base de datos de documentos Azure Cosmos DB yXamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)

_Azure Cosmos DB bases de datos de documentos admiten colecciones con particiones, que pueden abarcar varios servidores y particiones, a la vez que admiten un almacenamiento y un rendimiento ilimitados. En este artículo se explica cómo combinar el control de acceso con colecciones con particiones, de modo que un usuario solo pueda tener acceso a sus propios documentos en una Xamarin.Forms aplicación._

## <a name="overview"></a>Información general

Se debe especificar una clave de partición al crear una colección con particiones, y los documentos con la misma clave de partición se almacenarán en la misma partición. Por lo tanto, si se especifica la identidad del usuario como una clave de partición, se generará una colección con particiones que solo almacenará documentos para ese usuario. Esto también garantiza que el Azure Cosmos DB base de datos de documentos se escalará a medida que aumente el número de usuarios y elementos.

Se debe conceder acceso a cualquier recopilación y el modelo de control de acceso de la API de SQL define dos tipos de construcciones de acceso:

- **Las claves maestras** habilitan el acceso administrativo completo a todos los recursos de una cuenta de Cosmos dB y se crean cuando se crea una cuenta de Cosmos dB.
- Los **tokens de recursos** capturan la relación entre el usuario de una base de datos y el permiso que tiene el usuario para un recurso de Cosmos dB específico, como una colección o un documento.

Al exponer una clave maestra, se abre una Cosmos DB cuenta a la posibilidad de que se use el uso malintencionado o por negligencia. Sin embargo, Azure Cosmos DB los tokens de recursos proporcionan un mecanismo seguro para permitir que los clientes Lean, escriban y eliminen recursos específicos en una cuenta de Azure Cosmos DB de acuerdo con los permisos concedidos.

Un enfoque típico para solicitar, generar y entregar tokens de recursos a una aplicación móvil es usar un agente de token de recurso. En el diagrama siguiente se muestra información general de alto nivel sobre cómo la aplicación de ejemplo usa un agente de token de recursos para administrar el acceso a los datos de la base de datos de documentos:

![Proceso de autenticación de base de datos de documentos](azure-cosmosdb-auth-images/documentdb-authentication.png)

El agente de token de recursos es un servicio de API Web de nivel intermedio, hospedado en Azure App Service, que posee la clave maestra de la cuenta de Cosmos DB. La aplicación de ejemplo usa el agente de tokens de recursos para administrar el acceso a los datos de la base de datos de documentos como se indica a continuación:

1. En el inicio de sesión, la Xamarin.Forms aplicación se pone en contacto Azure App Service para iniciar un flujo de autenticación.
1. Azure App Service realiza un flujo de autenticación de OAuth con Facebook. Una vez completado el flujo de autenticación, la Xamarin.Forms aplicación recibe un token de acceso.
1. La Xamarin.Forms aplicación usa el token de acceso para solicitar un token de recurso del agente de token de recursos.
1. El agente de token de recursos usa el token de acceso para solicitar la identidad del usuario de Facebook. La identidad del usuario se usa para solicitar un token de recurso de Cosmos DB, que se usa para conceder acceso de lectura y escritura a la colección con particiones del usuario autenticado.
1. La Xamarin.Forms aplicación utiliza el token de recurso para acceder directamente a Cosmos dB recursos con los permisos definidos por el token de recurso.

> [!NOTE]
> Cuando el token de recurso expira, las solicitudes de base de datos de documentos posteriores recibirán una excepción de 401 no autorizado. En este momento, Xamarin.Forms las aplicaciones deben volver a establecer la identidad y solicitar un nuevo token de recurso.

Para obtener más información acerca de la creación de particiones Cosmos DB, consulte [partición y escalado en Azure Cosmos dB](/azure/cosmos-db/partition-data/). Para obtener más información sobre el control de acceso de Cosmos DB, consulte [proteger el acceso a los datos de Cosmos dB](/azure/cosmos-db/secure-access-to-data/) y [el control de acceso en la API de SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).

## <a name="setup"></a>Programa de instalación

El proceso para integrar el agente de token de recursos en una Xamarin.Forms aplicación es el siguiente:

1. Cree una cuenta de Cosmos DB que vaya a usar el control de acceso. Para obtener más información, consulte [configuración de Azure Cosmos dB](#azure-cosmos-db-configuration).
1. Cree un Azure App Service para hospedar el agente de token de recursos. Para obtener más información, consulte [configuración de Azure App Service](#azure-app-service-configuration).
1. Cree una aplicación de Facebook para realizar la autenticación. Para obtener más información, consulte [configuración de aplicaciones de Facebook](#facebook-app-configuration).
1. Configure el Azure App Service para facilitar la autenticación con Facebook. Para obtener más información, consulte Configuración de la [autenticación de Azure App Service](#azure-app-service-authentication-configuration).
1. Configure la Xamarin.Forms aplicación de ejemplo para que se comunique con Azure App Service y cosmos dB. Para obtener más información, consulte Configuración de la [ Xamarin.Forms aplicación](#xamarinforms-application-configuration).

> [!NOTE]
> Si no tiene una [suscripción a Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), cree una [cuenta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de empezar.

### <a name="azure-cosmos-db-configuration"></a>Configuración de Azure Cosmos DB

El proceso para crear una cuenta de Cosmos DB que usará el control de acceso es el siguiente:

1. Cree una cuenta de Cosmos DB. Para obtener más información, consulte [crear una cuenta de Azure Cosmos dB](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account).
1. En la cuenta de Cosmos DB, cree una nueva colección denominada `UserItems` y especifique una clave de partición de `/userid` .

### <a name="azure-app-service-configuration"></a>Configuración de Azure App Service

El proceso para hospedar el agente de token de recursos en Azure App Service es el siguiente:

1. En el Azure Portal, cree una nueva aplicación Web de App Service. Para obtener más información, consulte [crear una aplicación web en un APP Service Environment](/azure/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase/).
1. En el Azure Portal, abra la hoja configuración de la aplicación de la aplicación web y agregue la siguiente configuración:
    - `accountUrl`: el valor debe ser la dirección URL de la cuenta de Cosmos DB de la hoja claves de la cuenta de Cosmos DB.
    - `accountKey`: el valor debe ser la clave maestra Cosmos DB (principal o secundaria) de la hoja claves de la cuenta de Cosmos DB.
    - `databaseId`: el valor debe ser el nombre de la base de datos Cosmos DB.
    - `collectionId`: el valor debe ser el nombre de la colección de Cosmos DB (en este caso, `UserItems` ).
    - `hostUrl`: el valor debe ser la dirección URL de la aplicación web en la hoja de información general de la cuenta de App Service.

    En la captura de pantalla siguiente se muestra esta configuración:

    [![App Service la configuración de la aplicación Web](azure-cosmosdb-auth-images/azure-web-app-settings.png)](azure-cosmosdb-auth-images/azure-web-app-settings-large.png#lightbox "App Service la configuración de la aplicación Web")

1. Publique la solución de agente de tokens de recursos en la aplicación Web de Azure App Service.

### <a name="facebook-app-configuration"></a>Configuración de la aplicación Facebook

El proceso para crear una aplicación de Facebook para realizar la autenticación es el siguiente:

1. Cree una aplicación de Facebook. Para obtener más información, consulte [registrar y configurar una aplicación](https://developers.facebook.com/docs/apps/register) en el centro para desarrolladores de Facebook.
1. Agregue el producto de inicio de sesión de Facebook a la aplicación. Para obtener más información, consulte [adición de un inicio de sesión de Facebook a su aplicación o sitio web](https://developers.facebook.com/docs/facebook-login) en el centro para desarrolladores de Facebook.
1. Configure el inicio de sesión de Facebook de la siguiente manera:
   - Habilite el inicio de sesión de OAuth de cliente.
   - Habilite el inicio de sesión de OAuth Web.
   - Establezca el URI de redirección de OAuth válido en el URI de la aplicación Web de App Service, con `/.auth/login/facebook/callback` anexado.

  En la captura de pantalla siguiente se muestra esta configuración:

  ![Configuración de OAuth de inicio de sesión de Facebook](azure-cosmosdb-auth-images/facebook-oauth-settings.png)

Para obtener más información, consulte [registrar la aplicación con Facebook](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-nameregister-aregister-your-application-with-facebook).

### <a name="azure-app-service-authentication-configuration"></a>Configuración de la autenticación de Azure App Service

El proceso para configurar la autenticación de App Service Easy es el siguiente:

1. En Azure portal, vaya a la aplicación Web de App Service.
1. En Azure portal, abra la hoja autenticación/autorización y realice la siguiente configuración:
    - App Service la autenticación debe estar activada.
    - La acción que debe llevarse a cabo cuando una solicitud no está autenticada debe establecerse para **iniciar sesión en Facebook**.

    En la captura de pantalla siguiente se muestra esta configuración:

    [![App Service la configuración de autenticación de aplicaciones Web](azure-cosmosdb-auth-images/app-service-authentication-settings.png)](azure-cosmosdb-auth-images/app-service-authentication-settings-large.png#lightbox "App Service la configuración de autenticación de aplicaciones Web")

También se debe configurar la aplicación Web de App Service para comunicarse con la aplicación de Facebook para habilitar el flujo de autenticación. Para ello, seleccione el proveedor de identidades de Facebook y escriba los valores de **ID. de aplicación** y secreto de la **aplicación** en la configuración de la aplicación de Facebook en el centro para desarrolladores de Facebook. Para obtener más información, consulte [Agregar información de Facebook a la aplicación](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-namesecrets-aadd-facebook-information-to-your-application).

### <a name="xamarinforms-application-configuration"></a>Xamarin.FormsConfiguración de la aplicación

El proceso para configurar la Xamarin.Forms aplicación de ejemplo es el siguiente:

1. Abra la Xamarin.Forms solución.
1. Abra `Constants.cs` y actualice los valores de las constantes siguientes:
    - `EndpointUri`: el valor debe ser la dirección URL de la cuenta de Cosmos DB de la hoja claves de la cuenta de Cosmos DB.
    - `DatabaseName`: el valor debe ser el nombre de la base de datos de documentos.
    - `CollectionName`: el valor debe ser el nombre de la colección de bases de datos de documentos (en este caso, `UserItems` ).
    - `ResourceTokenBrokerUrl`: el valor debe ser la dirección URL de la aplicación web del agente de token de recursos en la hoja de información general de la cuenta de App Service.

## <a name="initiating-login"></a>Iniciando inicio de sesión

La aplicación de ejemplo inicia el proceso de inicio de sesión redirigiendo un explorador a una dirección URL del proveedor de identidades, como se muestra en el código de ejemplo siguiente:

```csharp
var auth = new Xamarin.Auth.WebRedirectAuthenticator(
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/facebook"),
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/done"));
```

Esto hace que se inicie un flujo de autenticación de OAuth entre Azure App Service y Facebook, que muestra la página de inicio de sesión de Facebook:

![Inicio de sesión de Facebook](azure-cosmosdb-auth-images/login.png)

Para cancelar el inicio de sesión, presione el botón **Cancelar** en iOS o presione el botón **atrás** en Android, en cuyo caso el usuario permanece sin autenticar y la interfaz de usuario del proveedor de identidades se quita de la pantalla.

## <a name="obtaining-a-resource-token"></a>Obtención de un token de recurso

Después de la autenticación correcta, se `WebRedirectAuthenticator.Completed` activa el evento. En el ejemplo de código siguiente se muestra cómo controlar este evento:

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

El resultado de una autenticación correcta es un token de acceso, que es la `AuthenticatorCompletedEventArgs.Account` propiedad disponible. El token de acceso se extrae y se usa en una solicitud GET a la API del agente de token de recurso `resourcetoken` .

La `resourcetoken` API usa el token de acceso para solicitar la identidad del usuario de Facebook, que a su vez se usa para solicitar un token de recurso de Cosmos dB. Si ya existe un documento de permiso válido para el usuario en la base de datos de documentos, se recupera y se devuelve a la aplicación un documento JSON que contiene el token de recurso Xamarin.Forms . Si no existe un documento de permiso válido para el usuario, se crea un usuario y un permiso en la base de datos de documentos, y el token de recurso se extrae del documento de permisos y se devuelve a la Xamarin.Forms aplicación en un documento JSON.

> [!NOTE]
> Un usuario de la base de datos de documentos es un recurso asociado a una base de datos de documentos y cada base de datos puede contener cero o más usuarios. Un permiso de base de datos de documentos es un recurso asociado a un usuario de base de datos de documentos y cada usuario puede contener cero o más permisos. Un recurso de permiso proporciona acceso a un token de seguridad que el usuario necesita al intentar tener acceso a un recurso, como un documento.

Si la `resourcetoken` API se completa correctamente, enviará el código de Estado HTTP 200 (correcto) en la respuesta, junto con un documento JSON que contiene el token de recurso. Los siguientes datos JSON muestran un mensaje de respuesta correcta típico:

```csharp
{
  "id": "John Smithpermission",
  "token": "type=resource&ver=1&sig=zx6k2zzxqktzvuzuku4b7y==;a74aukk99qtwk8v5rxfrfz7ay7zzqfkbfkremrwtaapvavw2mrvia4umbi/7iiwkrrq+buqqrzkaq4pp15y6bki1u//zf7p9x/aefbvqvq3tjjqiffurfx+vexa1xarxkkv9rbua9ypfzr47xpp5vmxuvzbekkwq6txme0xxxbjhzaxbkvzaji+iru3xqjp05amvq1r1q2k+qrarurhmjzah/ha0evixazkve2xk1zu9u/jpyf1xrwbkxqpzebvqwma+hyyaazemr6qx9uz9be==;",
  "expires": 4035948,
  "userid": "John Smith"
}
```

El `WebRedirectAuthenticator.Completed` controlador de eventos lee la respuesta de la `resourcetoken` API y extrae el token de recurso y el ID. de usuario. A continuación, el token de recurso se pasa como argumento al `DocumentClient` constructor, que encapsula el extremo, las credenciales y la Directiva de conexión utilizada para obtener acceso a Cosmos dB, y se usa para configurar y ejecutar solicitudes en cosmos dB. El token de recurso se envía con cada solicitud para acceder directamente a un recurso e indica que se concede acceso de lectura y escritura a la colección con particiones de los usuarios autenticados.

## <a name="retrieving-documents"></a>Recuperación de documentos

La recuperación de documentos que solo pertenecen al usuario autenticado se puede lograr creando una consulta de documento que incluya el identificador del usuario como clave de partición y se muestra en el ejemplo de código siguiente:

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

La consulta recupera de forma asincrónica todos los documentos que pertenecen al usuario autenticado, de la colección especificada y los coloca en una `List<TodoItem>` colección para su presentación.

El `CreateDocumentQuery<T>` método especifica un `Uri` argumento que representa la colección que se debe consultar para los documentos y un `FeedOptions` objeto. El `FeedOptions` objeto especifica que la consulta puede devolver un número ilimitado de elementos, y el identificador del usuario como una clave de partición. Esto garantiza que solo se devuelvan en el resultado los documentos de la colección con particiones del usuario.

> [!NOTE]
> Tenga en cuenta que los documentos de permisos, creados por el agente de token de recursos, se almacenan en la misma colección de documentos que los documentos creados por la Xamarin.Forms aplicación. Por lo tanto, la consulta de documento contiene una `Where` cláusula que aplica un predicado de filtrado a la consulta en la colección de documentos. Esta cláusula garantiza que los documentos de permiso no se devuelven desde la colección de documentos.

Para obtener más información sobre cómo recuperar documentos de una colección de documentos, vea [recuperar documentos de colección](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#retrieving-document-collection-documents)de documentos.

## <a name="inserting-documents"></a>Insertar documentos

Antes de insertar un documento en una colección de documentos, la `TodoItem.UserId` propiedad debe actualizarse con el valor que se usa como clave de partición, tal y como se muestra en el ejemplo de código siguiente:

```csharp
item.UserId = UserId;
await client.CreateDocumentAsync(collectionLink, item);
```

Esto garantiza que el documento se insertará en la colección con particiones del usuario.

Para obtener más información sobre cómo insertar un documento en una colección de documentos, vea [Insertar un documento en una colección de documentos](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#inserting-a-document-into-a-document-collection).

## <a name="deleting-documents"></a>Eliminar documentos

El valor de la clave de partición se debe especificar al eliminar un documento de una colección con particiones, como se muestra en el ejemplo de código siguiente:

```csharp
await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id),
                 new RequestOptions
                 {
                   PartitionKey = new PartitionKey(UserId)
                 });
```

Esto garantiza que Cosmos DB sepa en qué colección con particiones se va a eliminar el documento.

Para obtener más información sobre cómo eliminar un documento de una colección de documentos, vea [eliminar un documento de una colección de documentos](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#deleting-a-document-from-a-document-collection).

## <a name="summary"></a>Resumen

En este artículo se explica cómo combinar el control de acceso con colecciones con particiones, de modo que un usuario solo pueda tener acceso a sus documentos de base de datos de documentos en una Xamarin.Forms aplicación. La especificación de la identidad del usuario como clave de partición garantiza que una colección con particiones solo puede almacenar documentos para ese usuario.

## <a name="related-links"></a>Vínculos relacionados

- [Azure Cosmos DB autenticación de todo (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)
- [Consumo de una base de datos de documentos de Cosmos Azure DB](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md)
- [Protección del acceso a los datos de Azure Cosmos DB](/azure/cosmos-db/secure-access-to-data/)
- [Control de acceso en la API de SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).
- [Cómo particionar y escalar en Azure Cosmos DB](/azure/cosmos-db/partition-data/)
- [Biblioteca de cliente de Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API de Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn948556.aspx)
