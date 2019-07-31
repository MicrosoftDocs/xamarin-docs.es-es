---
title: Buscar datos con Azure Search y Xamarin. Forms
description: En este artículo se muestra cómo usar la biblioteca de Microsoft Azure Search para integrar la búsqueda de Azure en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: A4AEF233-3672-4174-9DBA-15BEE3030C0B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/05/2016
ms.openlocfilehash: d13246ddae3f505d04db2a866f785a9f60ce3979
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652039"
---
# <a name="search-data-with-azure-search-and-xamarinforms"></a>Buscar datos con Azure Search y Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azuresearch)

_Azure Search es un servicio en la nube que proporciona la indización y consulta las capacidades de los datos cargados. Esto quita los requisitos de infraestructura y la complejidad de algoritmo de búsqueda tradicionalmente asociada con la implementación de la funcionalidad de búsqueda en una aplicación. En este artículo se muestra cómo usar la biblioteca de Microsoft Azure Search para integrar la búsqueda de Azure en una aplicación de Xamarin.Forms._

## <a name="overview"></a>Información general

Los datos se almacenan en Azure Search como índices y documentos. Un *índice* es un almacén de datos que se pueden buscar por el servicio Azure Search y es conceptualmente similar a una tabla de base de datos. Un *documento* es una unidad única de datos en un índice de búsqueda y es conceptualmente similar a una fila de la base de datos. Al cargar los documentos y enviar las consultas de búsqueda para Azure Search, las solicitudes se realizan en un índice específico en el servicio de búsqueda.

Cada solicitud realizada a Azure Search debe incluir el nombre del servicio y una clave de API. Hay dos tipos de clave de API:

- *Las claves de administración* conceder derechos completos para todas las operaciones. Esto incluye la administración del servicio, crear y eliminar orígenes de datos y los índices.
- *Claves de consulta* conceder acceso de solo lectura a índices y documentos y debe usarse en aplicaciones que emiten solicitudes de búsqueda.

La solicitud más comunes para Azure Search es ejecutar una consulta. Hay dos tipos de consulta que se pueden enviar:

- Un *búsqueda* consulta busca uno o más elementos en todos los campos de búsqueda en un índice. Las consultas de búsqueda se crean con la sintaxis simplificada, o la sintaxis de consulta de Lucene. Para obtener más información, consulte [sintaxis de consulta Simple en Azure Search](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search/), y [sintaxis de consulta de Lucene en Azure Search](/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search/).
- Un *filtro* consulta evalúa una expresión booleana en todos los campos filtrables de un índice. Filtrar las consultas se crean con un subconjunto del lenguaje de filtro de OData. Para obtener más información, consulte [sintaxis de expresiones de OData para Azure Search](/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search/).

Las consultas de búsqueda y filtrar las consultas pueden utilizarse por separado o conjuntamente. Cuando se usan juntas, la consulta de filtro se aplica primero a todo el índice y, a continuación, se realiza la consulta de búsqueda en los resultados de la consulta de filtro.

Azure Search también admite recuperar sugerencias basadas en la entrada de búsqueda. Para obtener más información, consulte [sugerencia consultas](#suggestions).

## <a name="setup"></a>Programa de instalación

El proceso de integración de Azure Search en una aplicación de Xamarin.Forms es como sigue:

1. Crear un servicio Azure Search. Para obtener más información, consulte [crear un servicio de Azure Search mediante el Portal de Azure](/azure/search/search-create-service-portal/).
1. Quitar Silverlight como una plataforma de destino de la biblioteca de clases portables (PCL) de la solución de Xamarin.Forms. Esto puede realizarse al cambiar el perfil PCL a cualquier perfil que admite el desarrollo multiplataforma, pero no es compatible con Silverlight, como el perfil de 151 o 92.
1. Agregar el [biblioteca de Microsoft Azure Search](https://www.nuget.org/packages/Microsoft.Azure.Search) paquete NuGet al proyecto PCL en la solución de Xamarin.Forms.

Después de realizar estos pasos, la API de biblioteca de Microsoft Search puede usarse para administrar los orígenes de datos y los índices de búsqueda, cargar y administrar documentos y ejecutar consultas.

## <a name="creating-the-azure-search-index"></a>Crear el índice de búsqueda de Azure

Debe definir un esquema de índice que se asigna a la estructura de los datos que se va a buscar. Esto puede ser complicado en el Portal de Azure, o utilizando mediante programación la `SearchServiceClient` clase. Esta clase administra las conexiones con Azure Search y puede usarse para crear un índice. En el ejemplo de código siguiente se muestra cómo crear una instancia de esta clase:

```csharp
var searchClient =
  new SearchServiceClient(Constants.SearchServiceName, new SearchCredentials(Constants.AdminApiKey));
```

El `SearchServiceClient` sobrecarga de constructor toma un nombre de servicio de búsqueda y un `SearchCredentials` objeto como argumentos, con el `SearchCredentials` ajuste objeto el *clave de administración* para el servicio Azure Search. El *clave de administración* es necesario para crear un índice.

> [!NOTE]
>  Una sola `SearchServiceClient` instancia debe utilizarse en una aplicación para evitar que se abra demasiadas conexiones a Azure Search.

Un índice está definido por el `Index` de objeto, como se muestra en el ejemplo de código siguiente:

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

El `Index.Name` propiedad debe establecerse en el nombre del índice y el `Index.Fields` propiedad debe establecerse en una matriz de `Field` objetos. Cada `Field` instancia especifica un nombre, un tipo y las propiedades, que especifican cómo se usa el campo. Estas propiedades incluyen:

- `IsKey` : indica si el campo es la clave del índice. Solo un campo en el índice de tipo `DataType.String`, debe designarse como campo de clave.
- `IsFacetable` : indica si es posible llevar a cabo la navegación por facetas en este campo. El valor predeterminado es `false`.
- `IsFilterable` : indica si el campo se puede usar en las consultas de filtro. El valor predeterminado es `false`.
- `IsRetrievable` : indica si se puede recuperar el campo en los resultados de búsqueda. El valor predeterminado es `true`.
- `IsSearchable` : indica si el campo está incluido en las búsquedas de texto completo. El valor predeterminado es `false`.
- `IsSortable` : indica si el campo se puede utilizar en `OrderBy` expresiones. El valor predeterminado es `false`.

> [!NOTE]
> Cambio de un índice después de su implementación implica volver a generar y volver a cargar los datos.

Un `Index` objeto opcionalmente, puede especificar un `Suggesters` propiedad, que define los campos en el índice que se usará para admitir Autocompletar o sugerencia consultas de búsqueda. El `Suggesters` propiedad debe establecerse en una matriz de `Suggester` objetos que definen los campos que se utilizan para generar resultados de la sugerencia de la búsqueda.

Después de crear el `Index` objeto, el índice se crea mediante una llamada a `Indexes.Create` en el `SearchServiceClient` instancia.

> [!NOTE]
> Al crear un índice de una aplicación que debe mantenerse con capacidad de respuesta, use el `Indexes.CreateAsync` método.

Para obtener más información, consulte [crear un índice de Azure Search mediante el SDK de .NET](/azure/search/search-create-index-dotnet/).

## <a name="deleting-the-azure-search-index"></a>Al eliminar el índice de búsqueda de Azure

Se puede eliminar un índice mediante una llamada a `Indexes.Delete` en el `SearchServiceClient` instancia:

```csharp
searchClient.Indexes.Delete(Constants.Index);
```

## <a name="uploading-data-to-the-azure-search-index"></a>Cargar datos en el índice de búsqueda de Azure

Después de definir el índice, se pueden cargar datos a ella mediante uno de los dos modelos:

- **Modelo de extracción** : periódicamente los datos se ingieren en Azure Cosmos DB, Azure SQL Database, Azure Blob Storage o SQL Server hospedado en una máquina Virtual de Azure.
- **Modelo de inserción** – datos se envían mediante programación al índice. Este es el modelo adoptado en este artículo.

Un `SearchIndexClient` para importar datos en el índice, se debe crear la instancia. Esto puede realizarse mediante una llamada a la `SearchServiceClient.Indexes.GetClient` método, como se muestra en el ejemplo de código siguiente:

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

Datos que se importarán en el índice se empaquetan como un `IndexBatch` objeto, que encapsula una colección de `IndexAction` objetos. Cada `IndexAction` instancia contiene un documento y una propiedad que indica qué acción va a realizar en el documento de Azure Search. En el ejemplo de código anterior, el `IndexAction.Upload` acción que se especifica, los resultados en el documento que se inserta en el índice si es nuevo, o reemplaza si ya existe. El `IndexBatch` objeto, a continuación, se envía al índice mediante una llamada a la `Documents.Index` método en el `SearchIndexClient` objeto. Para obtener información acerca de otras acciones de indexación, consulte [decidir qué acción de indexación para usar](/azure/search/search-import-data-dotnet#decide-which-indexing-action-to-use).

> [!NOTE]
> Solo 1000 documentos pueden incluirse en una única solicitud de indexación.

Tenga en cuenta que en el ejemplo de código anterior, el `monkeyList` colección se crea como un objeto anónimo de una colección de `Monkey` objetos. Esto crea los datos para el `id` campo y se resuelve como la asignación de mayúsculas y minúsculas Pascal `Monkey` los nombres de campo de índice de búsqueda de nombres de propiedad a mayúsculas y minúsculas. Como alternativa, esta asignación también puede realizarse agregando el `[SerializePropertyNamesAsCamelCase]` atributo a la `Monkey` clase.

Para obtener más información, consulte [cargar datos en Azure Search mediante el SDK de .NET](/azure/search/search-import-data-dotnet/).

## <a name="querying-the-azure-search-index"></a>Consultar el índice de búsqueda de Azure

Un `SearchIndexClient` para consultar un índice, se debe crear la instancia. Cuando una aplicación ejecuta consultas, es conveniente seguir el principio de privilegios mínimos y crear un `SearchIndexClient` directamente, pasando el *clave de consulta* como argumento. Esto garantiza que los usuarios tengan acceso de solo lectura a índices y documentos. Este método se muestra en el ejemplo de código siguiente:

```csharp
SearchIndexClient indexClient =
  new SearchIndexClient(Constants.SearchServiceName, Constants.Index, new SearchCredentials(Constants.QueryApiKey));
```

El `SearchIndexClient` sobrecarga de constructor toma un nombre de servicio de búsqueda, el nombre del índice y un `SearchCredentials` objeto como argumentos, con el `SearchCredentials` ajuste objeto el *clave de consulta* para el servicio Azure Search.

### <a name="search-queries"></a>Consultas de búsqueda

El índice se puede consultar mediante una llamada a la `Documents.SearchAsync` método en el `SearchIndexClient` de instancia, tal como se muestra en el ejemplo de código siguiente:

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

El `SearchAsync` método toma un argumento de búsqueda de texto y un elemento opcional `SearchParameters` objeto que se puede usar para refinar la consulta. Una consulta de búsqueda se especifica como el argumento de texto de búsqueda, mientras que una consulta de filtro se puede especificar estableciendo el `Filter` propiedad de la `SearchParameters` argumento. En el ejemplo de código siguiente se muestra que ambos tipos de consulta:

```csharp
var parameters = new SearchParameters
{
  Filter = "location ne 'China' and location ne 'Vietnam'"
};
var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text, parameters);
```

Esta consulta de filtro se aplica a todo el índice y quita los documentos de los resultados donde el `location` campo no es igual a China y no es igual a Vietnam. Después de filtrado, la consulta de búsqueda se realiza en los resultados de la consulta de filtro.

> [!NOTE]
> Para filtrar sin buscar, pasar `*` como el argumento de texto de búsqueda.

El `SearchAsync` método devuelve un `DocumentSearchResult` objeto que contiene los resultados de consulta. Este objeto es de tipo enumerado con cada `Document` objeto se crea como un `Monkey` de objetos y agregado a la `Monkeys` `ObservableCollection` para su presentación. Los siguientes capturas de pantalla show consulta resultados de búsqueda de Azure Search:

![](azure-search-images/search.png "Resultados de búsqueda")

Para obtener más información acerca de la búsqueda y filtrado, consulte [consultas del índice de Azure Search con el SDK de .NET](/azure/search/search-query-dotnet/).

<a name="suggestions" />

### <a name="suggestion-queries"></a>Consultas de sugerencia

Azure Search permite a las sugerencias que se soliciten basándose en una consulta de búsqueda, mediante una llamada a la `Documents.SuggestAsync` método en el `SearchIndexClient` instancia. Esto se muestra en el ejemplo de código siguiente:

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

El `SuggestAsync` método toma un argumento de texto de búsqueda, el nombre del proveedor de sugerencias para usar (que se define en el índice), y un elemento opcional `SuggestParameters` objeto que se puede usar para refinar la consulta. El `SuggestParameters` instancia establece las propiedades siguientes:

- `UseFuzzyMatching` : cuando se establece en `true`, Azure Search encontrará sugerencias incluso si hay un carácter sustituido o no encontrado en el texto de búsqueda.
- `HighlightPreTag` : la etiqueta a la que se antepone a resultados de la sugerencia.
- `HighlightPostTag` : la etiqueta a la que se anexa a la sugerencia de visitas.
- `MinimumCoverage` : representa el porcentaje del índice que debe estar cubierto por una consulta de sugerencia para la consulta notifica un estado correcto. El valor predeterminado es 80.
- `Top` : el número de sugerencias para recuperar. Debe ser un entero entre 1 y 100, con un valor predeterminado de 5.

El efecto general es que se devolverán los primeros 10 resultados del índice con resaltado de referencias y los resultados incluirán documentos que contengan los términos de búsqueda de la ortografía del mismo modo.

El `SuggestAsync` método devuelve un `DocumentSuggestResult` objeto que contiene los resultados de consulta. Este objeto es de tipo enumerado con cada `Document` objeto se crea como un `Monkey` de objetos y agregado a la `Monkeys` `ObservableCollection` para su presentación. Las capturas de pantalla siguientes muestran los resultados de sugerencia obtenidos de Azure Search:

![](azure-search-images/suggest.png "Resultados de la sugerencia")

Tenga en cuenta que en la aplicación de ejemplo, el `SuggestAsync` método solo se invoca cuando el usuario termina de escribir un término de búsqueda. Sin embargo, también puede usarse para admitir las consultas de búsqueda de autocompletado ejecutando en cada keypress.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo usar la biblioteca de Microsoft Azure Search para integrar la búsqueda de Azure en una aplicación de Xamarin.Forms. Azure Search es un servicio en la nube que proporciona la indización y consulta las capacidades de los datos cargados. Esto quita los requisitos de infraestructura y la complejidad de algoritmo de búsqueda tradicionalmente asociada con la implementación de la funcionalidad de búsqueda en una aplicación.


## <a name="related-links"></a>Vínculos relacionados

- [Búsqueda de Azure (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azuresearch)
- [Documentación de Azure Search](/azure/search/)
- [Biblioteca de Microsoft Azure Search](https://www.nuget.org/packages/Microsoft.Azure.Search/)
