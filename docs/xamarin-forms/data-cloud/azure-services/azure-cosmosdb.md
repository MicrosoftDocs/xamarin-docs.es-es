---
title: Usar una base de datos de documentos Azure Cosmos DB en Xamarin. Forms
description: En este artículo se explica cómo usar la biblioteca de cliente estándar de .NET de Azure Cosmos DB para integrar una base de datos de documentos de Azure Cosmos DB en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 7C0605D9-9B7F-4002-9B60-2B5DAA3EA30C
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 13abfa9789cee32ed2c7cd2401c1402d9c7c3886
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489783"
---
# <a name="consume-an-azure-cosmos-db-document-database-in-xamarinforms"></a>Usar una base de datos de documentos Azure Cosmos DB en Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdb)

_Una base de datos de documentos Azure Cosmos DB es una base de datos NoSQL que proporciona acceso de baja latencia a los documentos JSON, que ofrece un servicio de base de datos escalable, rápido y de alta disponibilidad para aplicaciones que requieren escalado sin problemas y replicación global. En este artículo se explica cómo usar la biblioteca de cliente de Azure Cosmos DB .NET Standard para integrar una base de datos de documentos de Azure Cosmos DB en una aplicación de Xamarin. Forms._

> [!VIDEO https://youtube.com/embed/BoVH12igmbg]

**Vídeo Microsoft Azure Cosmos DB**

Una cuenta de base de datos de documento de Azure Cosmos DB se puede aprovisionar mediante una suscripción de Azure. Cada cuenta de base de datos puede tener cero o más bases de datos. Una base de datos de documentos en Azure Cosmos DB es un contenedor lógico para las colecciones de documentos y los usuarios.

Una base de datos de documentos de Azure Cosmos DB puede contener cero o más colecciones de documentos. Cada colección de documentos puede tener un nivel de rendimiento diferente, lo que permite más rendimiento que se especifique para las colecciones de acceso frecuente y menos rendimiento para las colecciones que se accede con poca frecuencia.

Cada colección de documentos se compone de cero o más documentos JSON. Documentos en una colección están libres de esquemas y, por lo que no es necesario compartir la misma estructura o campos. Cuando se agregan documentos a una colección de documentos, Cosmos DB indexa automáticamente les y estén disponibles para consulta.

Para fines de desarrollo, también se puede utilizar una base de datos de documento en un emulador. Usando el emulador, las aplicaciones se pueden desarrollar y probar de forma local, sin crear una suscripción de Azure ni incurrir en ningún gasto. Para obtener más información sobre el emulador, consulte [desarrollar localmente con el emulador de Azure Cosmos DB](/azure/cosmos-db/local-emulator/).

En este artículo y que acompaña a la aplicación de ejemplo, se muestra una aplicación de lista de tareas pendientes donde las tareas se almacenan en una base de datos de documentos de Azure Cosmos DB. Para obtener más información acerca de la aplicación de ejemplo, vea [descripción del ejemplo](~/xamarin-forms/data-cloud/web-services/introduction.md).

Para obtener más información sobre Azure Cosmos DB, consulte el [documentación de Azure Cosmos DB](/azure/cosmos-db/).

> [!NOTE]
> Si no tiene una [suscripción a Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), cree una [cuenta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de empezar.

## <a name="setup"></a>Instalación

El proceso para integrar una base de datos de documentos de Azure Cosmos DB en una aplicación de Xamarin.Forms es como sigue:

1. Cree una cuenta de Cosmos DB. Para obtener más información, consulte [crear una cuenta de Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#create-an-azure-cosmos-account).
1. Agregar el [biblioteca de cliente de .NET Standard de Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core) paquete NuGet a los proyectos de plataforma en la solución de Xamarin.Forms.
1. Agregar `using` directivas para el `Microsoft.Azure.Documents`, `Microsoft.Azure.Documents.Client`, y `Microsoft.Azure.Documents.Linq` espacios de nombres a las clases que tendrá acceso a la cuenta de Cosmos DB.

Después de realizar estos pasos, la biblioteca de cliente estándar de .NET de Azure Cosmos DB puede utilizarse para configurar y ejecutar solicitudes en la base de datos del documento.

> [!NOTE]
> La biblioteca estándar de .NET de Azure Cosmos DB de cliente solo puede instalarse en los proyectos de plataforma y no en un proyecto de biblioteca de clases portables (PCL). Por lo tanto, la aplicación de ejemplo es un proyecto de acceso compartido (SAP) para evitar la duplicación de código. Sin embargo, la `DependencyService` clase puede usarse en un proyecto PCL para invocar código de biblioteca de cliente estándar de .NET de Azure Cosmos DB incluido en proyectos específicos de plataforma.

## <a name="consuming-the-azure-cosmos-db-account"></a>Consumo de la cuenta de Azure Cosmos DB

El `DocumentClient` tipo encapsula el punto de conexión, credenciales y directiva de conexión utilizada para tener acceso a la cuenta de Azure Cosmos DB y se usa para configurar y ejecutar solicitudes en la cuenta. En el ejemplo de código siguiente se muestra cómo crear una instancia de esta clase:

```csharp
DocumentClient client = new DocumentClient(new Uri(Constants.EndpointUri), Constants.PrimaryKey);
```

Deben proporcionarse el Uri de Cosmos DB y la clave principal para el `DocumentClient` constructor. Estos pueden obtenerse desde el Portal de Azure. Para obtener más información, consulte [conectar a una cuenta de Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#Connect).

### <a name="creating-a-database"></a>Crear una base de datos

Una base de datos de documento es un contenedor lógico para las colecciones de documentos y los usuarios y puede ser creado en el Portal de Azure, o utilizando mediante programación el `DocumentClient.CreateDatabaseIfNotExistsAsync` método:

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

El `CreateDatabaseIfNotExistsAsync` método especifica un `Database` como un argumento de objeto con el `Database` objeto que especifica el nombre de la base de datos como su `Id` propiedad. El `CreateDatabaseIfNotExistsAsync` método crea la base de datos si no existe, o devuelve la base de datos si ya existe. Sin embargo, la aplicación de ejemplo omite todos los datos devueltos por la `CreateDatabaseIfNotExistsAsync` método.

> [!NOTE]
> El `CreateDatabaseIfNotExistsAsync` método devuelve un `Task<ResourceResponse<Database>>` objeto y el código de estado de la respuesta pueden comprobarse para determinar si una base de datos se creó o se ha devuelto una base de datos existente.

### <a name="creating-a-document-collection"></a>Creación de una colección de documentos

Una colección de documentos es un contenedor para documentos JSON y puede ser creado en el Portal de Azure, o utilizando mediante programación el `DocumentClient.CreateDocumentCollectionIfNotExistsAsync` método:

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

El `CreateDocumentCollectionIfNotExistsAsync` método requiere dos argumentos obligatorios: especifica un nombre de base de datos como un `Uri`y un `DocumentCollection` objeto. El `DocumentCollection` objeto representa una colección de documentos cuyo nombre se especifica con el `Id` propiedad. El `CreateDocumentCollectionIfNotExistsAsync` método crea la colección de documentos si no existe, o devuelve la colección de documentos si ya existe. Sin embargo, la aplicación de ejemplo omite todos los datos devueltos por la `CreateDocumentCollectionIfNotExistsAsync` método.

> [!NOTE]
> El `CreateDocumentCollectionIfNotExistsAsync` método devuelve un `Task<ResourceResponse<DocumentCollection>>` objeto y el código de estado de la respuesta pueden comprobarse para determinar si una colección de documentos se creó o se devuelve una colección de documentos existentes.

Opcionalmente, el `CreateDocumentCollectionIfNotExistsAsync` método también puede especificar un `RequestOptions` objeto, que encapsula las opciones que se pueden especificar para las solicitudes emitidas a la cuenta de Cosmos DB. El `RequestOptions.OfferThroughput` propiedad se utiliza para definir el nivel de rendimiento de la colección de documentos y, en el ejemplo de aplicación, se establece en 400 unidades de solicitud por segundo. Este valor se debe incrementar o disminuir dependiendo de si la colección con frecuencia o con poca frecuencia accederá.

> [!IMPORTANT]
> Tenga en cuenta que el `CreateDocumentCollectionIfNotExistsAsync` creará una nueva colección con un rendimiento reservado, lo cual tiene implicaciones de precios.

<a name="document_query" />

### <a name="retrieving-document-collection-documents"></a>Recuperación de documentos de la colección de documentos

El contenido de una colección de documentos puede obtenerse mediante la creación y ejecución de una consulta de documento. Se crea una consulta de documento con el `DocumentClient.CreateDocumentQuery` método:

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

Esta consulta recupera todos los documentos de la colección especificada asincrónicamente y coloca los documentos en un `List<TodoItem>` colección para su presentación.

El `CreateDocumentQuery<T>` método especifica un `Uri` argumento que representa la colección que se debe consultar para los documentos. En este ejemplo, el `collectionLink` variable es un campo de nivel de clase que especifica el `Uri` que representa la colección de documentos para recuperar documentos desde:

```csharp
Uri collectionLink = UriFactory.CreateDocumentCollectionUri(Constants.DatabaseName, Constants.CollectionName);
```

El `CreateDocumentQuery<T>` método crea una consulta que se ejecuta de forma sincrónica y devuelve un `IQueryable<T>` objeto. Sin embargo, el `AsDocumentQuery` método convierte el `IQueryable<T>` objeto a un `IDocumentQuery<T>` objeto que se puede ejecutar de forma asincrónica. Se ejecuta la consulta asincrónica con el `IDocumentQuery<T>.ExecuteNextAsync` método, que recupera la página siguiente de resultados de la base de datos de documento, con el `IDocumentQuery<T>.HasMoreResults` propiedad que indica si hay resultados adicionales que va a devolver la consulta.

Documentos se pueden filtrar en el servidor mediante la inclusión de un `Where` cláusula de la consulta, que se aplica un predicado de filtrado a la consulta en la colección de documentos:

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
          .Where(f => f.Done != true)
          .AsDocumentQuery();
```

Esta consulta recupera todos los documentos de la colección cuya `Done` es igual a la propiedad `false`.

<a name="inserting_document" />

### <a name="inserting-a-document-into-a-document-collection"></a>Insertar un documento en una colección de documentos

Documentos son contenido JSON de definidos por el usuario y se pueden insertar en una colección de documentos con el `DocumentClient.CreateDocumentAsync` método:

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.CreateDocumentAsync(collectionLink, item);
  ...
}
```

El `CreateDocumentAsync` método especifica un `Uri` argumento que representa la colección debe insertarse el documento, y un `object` argumento que representa el documento que se va a insertar.

### <a name="replacing-a-document-in-a-document-collection"></a>Reemplazar un documento en una colección de documentos

Se pueden reemplazar documentos en una colección de documentos con el `DocumentClient.ReplaceDocumentAsync` método:

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, item.Id), item);
  ...
}
```

El `ReplaceDocumentAsync` método especifica un `Uri` argumento que representa el documento de la colección que se debe reemplazar, y un `object` argumento que representa los datos de documento actualizado.

<a name="deleting_document" />

### <a name="deleting-a-document-from-a-document-collection"></a>Eliminación de un documento de una colección de documentos

Se puede eliminar un documento de una colección de documentos con el `DocumentClient.DeleteDocumentAsync` método:

```csharp
public async Task DeleteTodoItemAsync(string id)
{
  ...
  await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id));
  ...
}
```

El `DeleteDocumentAsync` método especifica un `Uri` argumento que representa el documento en la colección que se debe eliminar.

### <a name="deleting-a-document-collection"></a>Eliminación de una colección de documentos

Se puede eliminar una colección de documentos de una base de datos con el `DocumentClient.DeleteDocumentCollectionAsync` método:

```csharp
await client.DeleteDocumentCollectionAsync(collectionLink);
```

El `DeleteDocumentCollectionAsync` método especifica un `Uri` argumento que representa la colección de documentos que va a eliminar. Tenga en cuenta que al invocar este método, también se eliminarán los documentos almacenados en la colección.

### <a name="deleting-a-database"></a>Eliminar una base de datos

Se puede eliminar una base de datos de una cuenta de base de datos de Cosmos DB con la `DocumentClient.DeleteDatabaesAsync` método:

```csharp
await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(Constants.DatabaseName));
```

El `DeleteDatabaseAsync` método especifica un `Uri` argumento que representa la base de datos que va a eliminar. Tenga en cuenta que al invocar este método también eliminará las colecciones de documentos almacenadas en la base de datos y los documentos almacenados en las colecciones de documentos.

## <a name="summary"></a>Resumen

En este artículo se explica cómo usar la biblioteca de cliente estándar de .NET de Azure Cosmos DB para integrar una base de datos de documentos de Azure Cosmos DB en una aplicación de Xamarin.Forms. Una base de datos de documentos de Azure Cosmos DB es una base de datos NoSQL que proporciona acceso de baja latencia a los documentos JSON, que ofrece un servicio de base de datos rápido, alta disponibilidad y escalabilidad para aplicaciones que requieren replicación global y escala sin problemas.

## <a name="related-links"></a>Vínculos relacionados

- [Lista de tareas de Azure Cosmos DB (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdb)
- [Documentación de Azure Cosmos DB](/azure/cosmos-db/)
- [Biblioteca de cliente de Azure Cosmos DB .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API de Azure Cosmos DB](https://docs.microsoft.com/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)
