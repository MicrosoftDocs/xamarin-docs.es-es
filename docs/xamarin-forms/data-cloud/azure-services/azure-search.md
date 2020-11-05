---
title: Buscar datos con Azure Search y Xamarin.Forms
description: En este artículo se muestra cómo usar la biblioteca de búsqueda de Microsoft Azure para integrar Azure Search en una Xamarin.Forms aplicación.
ms.prod: xamarin
ms.assetid: A4AEF233-3672-4174-9DBA-15BEE3030C0B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/05/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 487ff8114a36212c1e7d413cc9bd1f4f2c016faa
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374438"
---
# <a name="search-data-with-azure-search-and-no-locxamarinforms"></a>Buscar datos con Azure Search y Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/webservices-azuresearch)

_Azure Search es un servicio en la nube que proporciona capacidades de indexación y consulta para los datos cargados. Esto elimina los requisitos de infraestructura y las complejidades del algoritmo de búsqueda tradicionalmente asociadas a la implementación de la funcionalidad de búsqueda en una aplicación. En este artículo se muestra cómo usar la biblioteca de búsqueda de Microsoft Azure para integrar Azure Search en una Xamarin.Forms aplicación._

## <a name="overview"></a>Información general

Los datos se almacenan en Azure Search como índices y documentos. Un *Índice* es un almacén de datos que el servicio de Azure Search puede buscar y es conceptualmente similar a una tabla de base de datos. Un *documento* es una unidad única de datos en los que se pueden realizar búsquedas en un índice y es conceptualmente similar a una fila de base de datos. Al cargar documentos y enviar consultas de búsqueda a Azure Search, se realizan solicitudes a un índice específico en el servicio de búsqueda.

Cada solicitud realizada a Azure Search debe incluir el nombre del servicio y una clave de API. Hay dos tipos de clave de API:

- *Las claves de administración* conceden derechos completos a todas las operaciones. Esto incluye administrar el servicio, crear y eliminar índices y orígenes de datos.
- *Las claves de consulta* conceden acceso de solo lectura a los índices y documentos y deben ser usadas por las aplicaciones que emiten solicitudes de búsqueda.

La solicitud más común para Azure Search es ejecutar una consulta. Hay dos tipos de consultas que se pueden enviar:

- Una consulta de *búsqueda* busca uno o más elementos en todos los campos de búsqueda de un índice. Las consultas de búsqueda se crean mediante la sintaxis simplificada o la sintaxis de consulta de Lucene. Para obtener más información, vea [Sintaxis de consulta simple en Azure Search](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search/)y [Sintaxis de consulta de Lucene en Azure Search](/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search/).
- Una consulta de *filtro* evalúa una expresión booleana sobre todos los campos filtrables de un índice. Las consultas de filtro se crean mediante un subconjunto del lenguaje de filtro de OData. Para obtener más información, vea [Sintaxis de expresiones de oData para Azure Search](/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search/).

Las consultas de búsqueda y las consultas de filtro se pueden usar por separado o juntas. Cuando se usan conjuntamente, la consulta de filtro se aplica primero a todo el índice y, a continuación, se realiza la consulta de búsqueda en los resultados de la consulta de filtro.

Azure Search también admite la recuperación de sugerencias basadas en la entrada de búsqueda. Para obtener más información, vea [consultas de sugerencias](#suggestion-queries).

> [!NOTE]
> Si no tiene una [suscripción a Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), cree una [cuenta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de empezar.

## <a name="setup"></a>Configuración

El proceso para integrar Azure Search en una Xamarin.Forms aplicación es el siguiente:

1. Cree un servicio de Azure Search. Para obtener más información, consulte [creación de un servicio de Azure Search mediante Azure portal](/azure/search/search-create-service-portal/).
1. Quite Silverlight como plataforma de destino de la Xamarin.Forms biblioteca de clases portable (PCL) de la solución. Esto se puede lograr cambiando el perfil de PCL a cualquier perfil que admita el desarrollo multiplataforma, pero no es compatible con Silverlight, como el perfil 151 o el perfil 92.
1. Agregue el paquete NuGet de la [biblioteca de búsqueda Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Search) al proyecto PCL de la Xamarin.Forms solución.

Después de seguir estos pasos, la API de la biblioteca de Microsoft Search se puede usar para administrar índices de búsqueda y orígenes de datos, cargar y administrar documentos y ejecutar consultas.

## <a name="creating-the-azure-search-index"></a>Creación de un índice de Azure Search

Se debe definir un esquema de índice que se asigne a la estructura de los datos que se van a buscar. Esto puede realizarse en Azure portal o mediante programación con la `SearchServiceClient` clase. Esta clase administra las conexiones a Azure Search y se puede usar para crear un índice. En el ejemplo de código siguiente se muestra cómo crear una instancia de esta clase:

```csharp
var searchClient =
  new SearchServiceClient(Constants.SearchServiceName, new SearchCredentials(Constants.AdminApiKey));
```

La `SearchServiceClient` sobrecarga del constructor toma un nombre de servicio de búsqueda y un `SearchCredentials` objeto como argumentos, con el objeto que contiene `SearchCredentials` la *clave de administración* para el servicio Azure Search. La *clave de administración* es necesaria para crear un índice.

> [!NOTE]
> `SearchServiceClient`Se debe usar una sola instancia en una aplicación para evitar que se abran demasiadas conexiones a Azure Search.

El objeto define un índice `Index` , como se muestra en el ejemplo de código siguiente:

```csharp
static void CreateSearchIndex()
{
  var index = new Index()
  {
    Name = Constants.Index,
    Fields = new[]
    {
      new Field("id", DataType.String) { IsKey = true, IsRetrievable = true },
      new Field("name", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSortable = true, IsSearchable = true },
      new Field("location", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSortable = true, IsSearchable = true },
      new Field("details", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSearchable = true },
      new Field("imageUrl", DataType.String) { IsRetrievable = true }
    },
    Suggesters = new[]
    {
      new Suggester("nameSuggester", SuggesterSearchMode.AnalyzingInfixMatching, new[] { "name" })
    }
  };

  searchClient.Indexes.Create(index);
}
```

La `Index.Name` propiedad se debe establecer en el nombre del índice y la `Index.Fields` propiedad debe establecerse en una matriz de `Field` objetos. Cada `Field` instancia especifica un nombre, un tipo y cualquier propiedad, que especifica cómo se usa el campo. Estas propiedades incluyen:

- `IsKey` : indica si el campo es la clave del índice. Solo un campo del índice, de tipo `DataType.String` , debe designarse como el campo de clave.
- `IsFacetable` : indica si es posible realizar una navegación por facetas en este campo. El valor predeterminado es `false`.
- `IsFilterable` : indica si el campo se puede usar en consultas de filtro. El valor predeterminado es `false`.
- `IsRetrievable` : indica si el campo se puede recuperar en los resultados de la búsqueda. El valor predeterminado es `true`.
- `IsSearchable` : indica si el campo está incluido en las búsquedas de texto completo. El valor predeterminado es `false`.
- `IsSortable` : indica si el campo se puede usar en `OrderBy` expresiones. El valor predeterminado es `false`.

> [!NOTE]
> Cambiar un índice después de su implementación implica volver a generar y volver a cargar los datos.

Un `Index` objeto puede especificar opcionalmente una `Suggesters` propiedad, que define los campos en el índice que se van a utilizar para admitir las consultas de autocompletar o buscar sugerencias. La `Suggesters` propiedad se debe establecer en una matriz de `Suggester` objetos que definan los campos que se usan para generar los resultados de la sugerencia de búsqueda.

Después de crear el `Index` objeto, el índice se crea llamando a `Indexes.Create` en la `SearchServiceClient` instancia de.

> [!NOTE]
> Al crear un índice a partir de una aplicación que debe mantener la capacidad de respuesta, use el `Indexes.CreateAsync` método.

Para obtener más información, vea [creación de un índice de Azure Search mediante el SDK de .net](/azure/search/search-create-index-dotnet/).

## <a name="deleting-the-azure-search-index"></a>Eliminar el índice de Azure Search

Un índice se puede eliminar llamando a `Indexes.Delete` en la `SearchServiceClient` instancia de:

```csharp
searchClient.Indexes.Delete(Constants.Index);
```

## <a name="uploading-data-to-the-azure-search-index"></a>Cargar datos en el índice de Azure Search

Después de definir el índice, los datos se pueden cargar en él mediante uno de estos dos modelos:

- **Modelo de extracción** : los datos se ingestan periódicamente de Azure Cosmos DB, Azure SQL Database, Azure Blob Storage o SQL Server hospedan en una máquina virtual de Azure.
- **Modelo de envío** : los datos se envían mediante programación al índice. Este es el modelo adoptado en este artículo.

`SearchIndexClient`Se debe crear una instancia de para importar los datos en el índice. Esto puede realizarse llamando al `SearchServiceClient.Indexes.GetClient` método, como se muestra en el ejemplo de código siguiente:

```csharp
static void UploadDataToSearchIndex()
{
  var indexClient = searchClient.Indexes.GetClient(Constants.Index);

  var monkeyList = MonkeyData.Monkeys.Select(m => new
  {
    id = Guid.NewGuid().ToString(),
    name = m.Name,
    location = m.Location,
    details = m.Details,
    imageUrl = m.ImageUrl
  });

  var batch = IndexBatch.New(monkeyList.Select(IndexAction.Upload));
  try
  {
    indexClient.Documents.Index(batch);
  }
  catch (IndexBatchException ex)
  {
    // Sometimes when the Search service is under load, indexing will fail for some
    // documents in the batch. Compensating actions like delaying and retrying should be taken.
    // Here, the failed document keys are logged.
    Console.WriteLine("Failed to index some documents: {0}",
      string.Join(", ", ex.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
  }
}
```

Los datos que se van a importar en el índice se empaquetan como un `IndexBatch` objeto, que encapsula una colección de `IndexAction` objetos. Cada `IndexAction` instancia contiene un documento y una propiedad que indica Azure Search qué acción se debe realizar en el documento. En el ejemplo de código anterior, `IndexAction.Upload` se especifica la acción, lo que hace que el documento se inserte en el índice si es nuevo o se reemplaza si ya existe. `IndexBatch`A continuación, el objeto se envía al índice llamando al `Documents.Index` método en el `SearchIndexClient` objeto. Para obtener información sobre otras acciones de indexación, consulte [decidir qué acción de indexación se va a usar](/azure/search/search-import-data-dotnet#decide-which-indexing-action-to-use).

> [!NOTE]
> Solo se pueden incluir 1000 documentos en una única solicitud de indexación.

Tenga en cuenta que en el ejemplo de código anterior, la `monkeyList` colección se crea como un objeto anónimo a partir de una colección de `Monkey` objetos. Esto crea datos para el `id` campo y resuelve la asignación de nombres de propiedades de mayúsculas y minúsculas Pascal `Monkey` a nombres de campos de índice de búsqueda de mayúsculas y minúsculas. También se puede realizar esta asignación agregando el `[SerializePropertyNamesAsCamelCase]` atributo a la `Monkey` clase.

Para obtener más información, consulte [carga de datos en Azure Search mediante el SDK de .net](/azure/search/search-import-data-dotnet/).

## <a name="querying-the-azure-search-index"></a>Consultar el índice de Azure Search

`SearchIndexClient`Se debe crear una instancia de para consultar un índice. Cuando una aplicación ejecuta consultas, es aconsejable seguir el principio de privilegios mínimos y crear un `SearchIndexClient` directamente, pasando la clave de *consulta* como argumento. Esto garantiza que los usuarios tengan acceso de solo lectura a los índices y documentos. Este enfoque se muestra en el ejemplo de código siguiente:

```csharp
SearchIndexClient indexClient =
  new SearchIndexClient(Constants.SearchServiceName, Constants.Index, new SearchCredentials(Constants.QueryApiKey));
```

La `SearchIndexClient` sobrecarga del constructor toma como argumentos un nombre de servicio de búsqueda, un nombre de índice y un `SearchCredentials` objeto, con el objeto que contiene la `SearchCredentials` *clave de consulta* para el servicio de Azure Search.

### <a name="search-queries"></a>Consultas de búsqueda

El índice se puede consultar llamando al `Documents.SearchAsync` método en la `SearchIndexClient` instancia, como se muestra en el ejemplo de código siguiente:

```csharp
async Task AzureSearch(string text)
{
  Monkeys.Clear();

  var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text);
  foreach (SearchResult<Monkey> result in searchResults.Results)
  {
    Monkeys.Add(new Monkey
    {
      Name = result.Document.Name,
      Location = result.Document.Location,
      Details = result.Document.Details,
      ImageUrl = result.Document.ImageUrl
    });
  }
}
```

El `SearchAsync` método toma un argumento de texto de búsqueda y un `SearchParameters` objeto opcional que se puede usar para refinar aún más la consulta. Una consulta de búsqueda se especifica como el argumento de texto de búsqueda, mientras que se puede especificar una consulta de filtro estableciendo la `Filter` propiedad del `SearchParameters` argumento. En el ejemplo de código siguiente se muestran ambos tipos de consulta:

```csharp
var parameters = new SearchParameters
{
  Filter = "location ne 'China' and location ne 'Vietnam'"
};
var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text, parameters);
```

Esta consulta de filtro se aplica a todo el índice y quita los documentos de los resultados en los que el `location` campo no es igual a China y no es igual a Vietnam. Después del filtrado, la consulta de búsqueda se realiza en los resultados de la consulta de filtro.

> [!NOTE]
> Para filtrar sin buscar, pase `*` como el argumento de texto de búsqueda.

El `SearchAsync` método devuelve un `DocumentSearchResult` objeto que contiene los resultados de la consulta. Este objeto se enumera, con cada `Document` objeto que se crea como un `Monkey` objeto y se agrega a `Monkeys` `ObservableCollection` para su presentación. Las capturas de pantallas siguientes muestran los resultados de las consultas de búsqueda devueltos desde Azure Search:

![Resultados de la búsqueda](azure-search-images/search.png)

Para obtener más información sobre la búsqueda y el filtrado, consulte [consultar el índice de Azure Search mediante el SDK de .net](/azure/search/search-query-dotnet/).

### <a name="suggestion-queries"></a>Consultas de sugerencias

Azure Search permite solicitar sugerencias en función de una consulta de búsqueda, llamando al `Documents.SuggestAsync` método en la `SearchIndexClient` instancia. Esto se muestra en el ejemplo de código siguiente:

```csharp
async Task AzureSuggestions(string text)
{
  Suggestions.Clear();

  var parameters = new SuggestParameters()
  {
    UseFuzzyMatching = true,
    HighlightPreTag = "[",
    HighlightPostTag = "]",
    MinimumCoverage = 100,
    Top = 10
  };

  var suggestionResults =
    await indexClient.Documents.SuggestAsync<Monkey>(text, "nameSuggester", parameters);

  foreach (var result in suggestionResults.Results)
  {
    Suggestions.Add(new Monkey
    {
      Name = result.Text,
      Location = result.Document.Location,
      Details = result.Document.Details,
      ImageUrl = result.Document.ImageUrl
    });
  }
}
```

El `SuggestAsync` método toma un argumento de texto de búsqueda, el nombre del que se va a usar (que se define en el índice) y un `SuggestParameters` objeto opcional que se puede usar para refinar aún más la consulta. La `SuggestParameters` instancia de establece las siguientes propiedades:

- `UseFuzzyMatching` : cuando se establece en `true` , Azure Search encontrará sugerencias incluso si hay un carácter sustituido o que falta en el texto de búsqueda.
- `HighlightPreTag` : la etiqueta que se antepone a los aciertos de sugerencias.
- `HighlightPostTag` : la etiqueta que se anexa a los aciertos de sugerencias.
- `MinimumCoverage` : representa el porcentaje del índice que debe estar cubierta por una consulta de sugerencia para que se notifique un éxito en la consulta. El valor predeterminado es 80.
- `Top` : número de sugerencias que se van a recuperar. Debe ser un entero comprendido entre 1 y 100, con un valor predeterminado de 5.

El efecto general es que los 10 resultados principales del índice se devolverán con el resaltado de referencias, y los resultados incluirán documentos que incluyan términos de búsqueda escritos de forma similar.

El `SuggestAsync` método devuelve un `DocumentSuggestResult` objeto que contiene los resultados de la consulta. Este objeto se enumera, con cada `Document` objeto que se crea como un `Monkey` objeto y se agrega a `Monkeys` `ObservableCollection` para su presentación. Las capturas de pantallas siguientes muestran los resultados de sugerencias devueltos por Azure Search:

![Resultados de la sugerencia](azure-search-images/suggest.png)

Tenga en cuenta que en la aplicación de ejemplo, el `SuggestAsync` método solo se invoca cuando el usuario finaliza la entrada de un término de búsqueda. Sin embargo, también se puede usar para admitir consultas de búsqueda Autocompletar mediante la ejecución de en cada KeyPress.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo usar la biblioteca de búsqueda de Microsoft Azure para integrar Azure Search en una Xamarin.Forms aplicación. Azure Search es un servicio en la nube que proporciona capacidades de indexación y consulta para los datos cargados. Esto elimina los requisitos de infraestructura y las complejidades del algoritmo de búsqueda tradicionalmente asociadas a la implementación de la funcionalidad de búsqueda en una aplicación.

## <a name="related-links"></a>Vínculos relacionados

- [Azure Search (ejemplo)](/samples/xamarin/xamarin-forms-samples/webservices-azuresearch)
- [Azure Search documentación](/azure/search/)
- [Microsoft Azure biblioteca de búsqueda](https://www.nuget.org/packages/Microsoft.Azure.Search/)