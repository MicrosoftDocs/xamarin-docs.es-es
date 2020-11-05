---
title: Usar una base de datos de documentos de Azure Cosmos DB en Xamarin.Forms
description: En este artículo se explica cómo usar la biblioteca de cliente de .NET Standard de Azure Cosmos DB para integrar una base de datos de documentos Azure Cosmos DB en una Xamarin.Forms aplicación.
ms.prod: xamarin
ms.assetid: 7C0605D9-9B7F-4002-9B60-2B5DAA3EA30C
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3755f85b01821238e790bd2fb09f567c4e98d4d8
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368367"
---
# <a name="consume-an-azure-cosmos-db-document-database-in-no-locxamarinforms"></a>Usar una base de datos de documentos de Azure Cosmos DB en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdb)

_Una base de datos de documentos Azure Cosmos DB es una base de datos NoSQL que proporciona acceso de baja latencia a los documentos JSON, que ofrece un servicio de base de datos escalable, rápido y de alta disponibilidad para aplicaciones que requieren escalado sin problemas y replicación global. En este artículo se explica cómo usar la biblioteca de cliente de .NET Standard de Azure Cosmos DB para integrar una base de datos de documentos Azure Cosmos DB en una Xamarin.Forms aplicación._

> [!VIDEO https://youtube.com/embed/BoVH12igmbg]

**Vídeo Microsoft Azure Cosmos DB**

Se puede aprovisionar una cuenta de base de datos de documentos Azure Cosmos DB mediante una suscripción de Azure. Cada cuenta de base de datos puede tener cero o más bases de datos. Una base de datos de documentos en Azure Cosmos DB es un contenedor lógico para las colecciones de documentos y los usuarios.

Una base de datos de documentos Azure Cosmos DB puede contener cero o más colecciones de documentos. Cada colección de documentos puede tener un nivel de rendimiento diferente, lo que permite especificar más rendimiento para colecciones de acceso frecuente y menos capacidad de proceso para las colecciones a las que se accede con poca frecuencia.

Cada colección de documentos consta de cero o más documentos JSON. Los documentos de una colección no están disponibles para el esquema y, por tanto, no es necesario que compartan la misma estructura o campos. A medida que se agregan documentos a una colección de documentos, Cosmos DB los indexa automáticamente y están disponibles para ser consultados.

Para fines de desarrollo, una base de datos de documentos también se puede consumir a través de un emulador. Con el emulador, las aplicaciones se pueden desarrollar y probar de forma local, sin necesidad de crear una suscripción de Azure ni incurrir en ningún costo. Para obtener más información sobre el emulador, consulte [desarrollo local con el emulador de Azure Cosmos dB](/azure/cosmos-db/local-emulator/).

En este artículo y en la aplicación de ejemplo que lo acompaña, se muestra una aplicación de lista de tareas pendientes en la que las tareas se almacenan en una base de datos de documentos Azure Cosmos DB. Para obtener más información acerca de la aplicación de ejemplo, vea [Descripción del ejemplo](~/xamarin-forms/data-cloud/web-services/introduction.md).

Para obtener más información acerca de Azure Cosmos DB, consulte la [documentación de Azure Cosmos dB](/azure/cosmos-db/).

> [!NOTE]
> Si no tiene una [suscripción a Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), cree una [cuenta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de empezar.

## <a name="setup"></a>Configuración

El proceso de integración de una base de datos de documentos de Azure Cosmos DB en una Xamarin.Forms aplicación de es el siguiente:

1. Cree una cuenta de Cosmos DB. Para obtener más información, consulte [crear una cuenta de Azure Cosmos dB](/azure/cosmos-db/sql-api-dotnetcore-get-started#create-an-azure-cosmos-account).
1. Agregue el paquete NuGet de la [biblioteca de cliente de Azure Cosmos DB .net Standard](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core) a los proyectos de la plataforma de la Xamarin.Forms solución.
1. Agregue `using` directivas para los `Microsoft.Azure.Documents` `Microsoft.Azure.Documents.Client` espacios de nombres, y `Microsoft.Azure.Documents.Linq` a las clases que tendrán acceso a la cuenta de Cosmos dB.

Después de realizar estos pasos, la biblioteca de cliente de Azure Cosmos DB .NET Standard se puede usar para configurar y ejecutar solicitudes en la base de datos de documentos.

> [!NOTE]
> La biblioteca de cliente de Azure Cosmos DB .NET Standard solo se puede instalar en proyectos de plataforma y no en un proyecto de biblioteca de clases portable (PCL). Por lo tanto, la aplicación de ejemplo es un proyecto de acceso compartido (SAP) para evitar la duplicación de código. Sin embargo, `DependencyService` se puede utilizar la clase en un proyecto de PCL para invocar Azure Cosmos DB .net Standard código de biblioteca de cliente contenido en proyectos específicos de la plataforma.

## <a name="consuming-the-azure-cosmos-db-account"></a>Consumo de la cuenta de Azure Cosmos DB

El `DocumentClient` tipo encapsula el extremo, las credenciales y la Directiva de conexión utilizada para obtener acceso a la cuenta de Azure Cosmos dB, y se usa para configurar y ejecutar solicitudes en la cuenta. En el ejemplo de código siguiente se muestra cómo crear una instancia de esta clase:

```csharp
DocumentClient client = new DocumentClient(new Uri(Constants.EndpointUri), Constants.PrimaryKey);
```

El URI de Cosmos DB y la clave principal deben proporcionarse al `DocumentClient` constructor. Estos se pueden obtener desde Azure portal. Para obtener más información, consulte [conexión a una cuenta de Azure Cosmos dB](/azure/cosmos-db/sql-api-dotnetcore-get-started#Connect).

### <a name="creating-a-database"></a>Crear una base de datos

Una base de datos de documentos es un contenedor lógico para las colecciones de documentos y los usuarios, y se puede crear en el portal de Azure o mediante programación con el `DocumentClient.CreateDatabaseIfNotExistsAsync` método:

```csharp
public async Task CreateDatabase(string databaseName)
{
  ...
  await client.CreateDatabaseIfNotExistsAsync(new Database
  {
    Id = databaseName
  });
  ...
}
```

El `CreateDatabaseIfNotExistsAsync` método especifica un `Database` objeto como argumento, con el `Database` objeto que especifica el nombre de la base de datos como su `Id` propiedad. El `CreateDatabaseIfNotExistsAsync` método crea la base de datos si no existe o devuelve la base de datos si ya existe. Sin embargo, la aplicación de ejemplo omite los datos devueltos por el `CreateDatabaseIfNotExistsAsync` método.

> [!NOTE]
> El `CreateDatabaseIfNotExistsAsync` método devuelve un `Task<ResourceResponse<Database>>` objeto y se puede comprobar el código de estado de la respuesta para determinar si se creó una base de datos o si se devolvió una base de datos existente.

### <a name="creating-a-document-collection"></a>Crear una colección de documentos

Una colección de documentos es un contenedor de documentos JSON y se puede crear en el portal de Azure o mediante programación con el `DocumentClient.CreateDocumentCollectionIfNotExistsAsync` método:

```csharp
public async Task CreateDocumentCollection(string databaseName, string collectionName)
{
  ...
  // Create collection with 400 RU/s
  await client.CreateDocumentCollectionIfNotExistsAsync(
    UriFactory.CreateDatabaseUri(databaseName),
    new DocumentCollection
    {
      Id = collectionName
    },
    new RequestOptions
    {
      OfferThroughput = 400
    });
  ...
}
```

El `CreateDocumentCollectionIfNotExistsAsync` método requiere dos argumentos obligatorios: un nombre de base de datos especificado como `Uri` y un `DocumentCollection` objeto. El `DocumentCollection` objeto representa una colección de documentos cuyo nombre se especifica con la `Id` propiedad. El `CreateDocumentCollectionIfNotExistsAsync` método crea la colección de documentos si no existe o devuelve la colección de documentos si ya existe. Sin embargo, la aplicación de ejemplo omite los datos devueltos por el `CreateDocumentCollectionIfNotExistsAsync` método.

> [!NOTE]
> El `CreateDocumentCollectionIfNotExistsAsync` método devuelve un `Task<ResourceResponse<DocumentCollection>>` objeto y se puede comprobar el código de estado de la respuesta para determinar si se ha creado una colección de documentos o si se ha devuelto una colección de documentos existente.

Opcionalmente, el `CreateDocumentCollectionIfNotExistsAsync` método también puede especificar un `RequestOptions` objeto, que encapsula las opciones que se pueden especificar para las solicitudes emitidas a la cuenta de Cosmos dB. La `RequestOptions.OfferThroughput` propiedad se usa para definir el nivel de rendimiento de la colección de documentos y, en la aplicación de ejemplo, se establece en 400 unidades de solicitud por segundo. Este valor debe aumentar o disminuir en función de si se accede a la colección con frecuencia o con poca frecuencia.

> [!IMPORTANT]
> Tenga en cuenta que el `CreateDocumentCollectionIfNotExistsAsync` método creará una nueva colección con un rendimiento reservado, que tiene implicaciones de precios.

### <a name="retrieving-document-collection-documents"></a>Recuperación de documentos de colección de documentos

El contenido de una colección de documentos se puede recuperar creando y ejecutando una consulta de documento. Una consulta de documento se crea con el `DocumentClient.CreateDocumentQuery` método:

```csharp
public async Task<List<TodoItem>> GetTodoItemsAsync()
{
  ...
  var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
            .AsDocumentQuery();
  while (query.HasMoreResults)
  {
    Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
  }
  ...
}
```

Esta consulta recupera de forma asincrónica todos los documentos de la colección especificada y coloca los documentos en una `List<TodoItem>` colección para su presentación.

El `CreateDocumentQuery<T>` método especifica un `Uri` argumento que representa la colección que se debe consultar para los documentos. En este ejemplo, la `collectionLink` variable es un campo de nivel de clase que especifica el `Uri` que representa la colección de documentos de la que se van a recuperar documentos:

```csharp
Uri collectionLink = UriFactory.CreateDocumentCollectionUri(Constants.DatabaseName, Constants.CollectionName);
```

El `CreateDocumentQuery<T>` método crea una consulta que se ejecuta sincrónicamente y devuelve un `IQueryable<T>` objeto. Sin embargo, el `AsDocumentQuery` método convierte el `IQueryable<T>` objeto en un `IDocumentQuery<T>` objeto que se puede ejecutar de forma asincrónica. La consulta asincrónica se ejecuta con el `IDocumentQuery<T>.ExecuteNextAsync` método, que recupera la siguiente página de resultados de la base de datos de documentos, con la `IDocumentQuery<T>.HasMoreResults` propiedad que indica si se van a devolver resultados adicionales de la consulta.

Los documentos se pueden filtrar en el lado servidor incluyendo una `Where` cláusula en la consulta, que aplica un predicado de filtrado a la consulta en la colección de documentos:

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
          .Where(f => f.Done != true)
          .AsDocumentQuery();
```

Esta consulta recupera todos los documentos de la colección cuya `Done` propiedad es igual a `false` .

### <a name="inserting-a-document-into-a-document-collection"></a>Insertar un documento en una colección de documentos

Los documentos son contenido JSON definido por el usuario y se pueden insertar en una colección de documentos con el `DocumentClient.CreateDocumentAsync` método:

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.CreateDocumentAsync(collectionLink, item);
  ...
}
```

El `CreateDocumentAsync` método especifica un `Uri` argumento que representa la colección en la que se debe insertar el documento y un `object` argumento que representa el documento que se va a insertar.

### <a name="replacing-a-document-in-a-document-collection"></a>Reemplazar un documento en una colección de documentos

Los documentos se pueden reemplazar en una colección de documentos con el `DocumentClient.ReplaceDocumentAsync` método:

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, item.Id), item);
  ...
}
```

El `ReplaceDocumentAsync` método especifica un `Uri` argumento que representa el documento de la colección que se debe reemplazar y un `object` argumento que representa los datos de documento actualizados.

### <a name="deleting-a-document-from-a-document-collection"></a>Eliminar un documento de una colección de documentos

Un documento se puede eliminar de una colección de documentos con el `DocumentClient.DeleteDocumentAsync` método:

```csharp
public async Task DeleteTodoItemAsync(string id)
{
  ...
  await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id));
  ...
}
```

El `DeleteDocumentAsync` método especifica un `Uri` argumento que representa el documento de la colección que se debe eliminar.

### <a name="deleting-a-document-collection"></a>Eliminar una colección de documentos

Una colección de documentos se puede eliminar de una base de datos con el `DocumentClient.DeleteDocumentCollectionAsync` método:

```csharp
await client.DeleteDocumentCollectionAsync(collectionLink);
```

El `DeleteDocumentCollectionAsync` método especifica un `Uri` argumento que representa la colección de documentos que se va a eliminar. Tenga en cuenta que al invocar este método también se eliminarán los documentos almacenados en la colección.

### <a name="deleting-a-database"></a>Eliminar una base de datos

Una base de datos se puede eliminar de una cuenta de base de datos de Cosmos DB con el `DocumentClient.DeleteDatabaesAsync` método:

```csharp
await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(Constants.DatabaseName));
```

El `DeleteDatabaseAsync` método especifica un `Uri` argumento que representa la base de datos que se va a eliminar. Tenga en cuenta que al invocar este método también se eliminarán las colecciones de documentos almacenadas en la base de datos y los documentos almacenados en las colecciones de documentos.

## <a name="summary"></a>Resumen

En este artículo se explica cómo usar la biblioteca de cliente de .NET Standard de Azure Cosmos DB para integrar una base de datos de documentos Azure Cosmos DB en una Xamarin.Forms aplicación. Una base de datos de documentos Azure Cosmos DB es una base de datos NoSQL que proporciona acceso de baja latencia a los documentos JSON, que ofrece un servicio de base de datos escalable, rápido y de alta disponibilidad para aplicaciones que requieren escalado sin problemas y replicación global.

## <a name="related-links"></a>Vínculos relacionados

- [Azure Cosmos DB todo (ejemplo)](/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdb)
- [Documentación sobre Azure Cosmos DB](/azure/cosmos-db/)
- [Biblioteca de cliente de Azure Cosmos DB .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API de Azure Cosmos DB](/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)