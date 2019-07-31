---
title: La revisión ortográfica con Bing Spell Check API
description: Bing Spell Check realiza contextual corrector ortográfico para el texto, que proporciona sugerencias de en línea para palabras mal escritas. En este artículo se explica cómo usar Bing Spell Check REST API para corregir errores ortográficos en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: B40EB103-FDC0-45C6-9940-FB4ACDC2F4F9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: ed6992f946512cd88b4b2b8cfcf4c826bdd6b837
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645342"
---
# <a name="spell-checking-using-the-bing-spell-check-api"></a>La revisión ortográfica con Bing Spell Check API

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Bing Spell Check realiza contextual corrector ortográfico para el texto, que proporciona sugerencias de en línea para palabras mal escritas. En este artículo se explica cómo usar Bing Spell Check REST API para corregir errores ortográficos en una aplicación de Xamarin.Forms._

## <a name="overview"></a>Información general

Bing Spell Check REST API tiene dos modos de funcionamiento y un modo debe especificarse al realizar una solicitud a la API:

- `Spell` corrige un texto breve (de hasta 9 palabras) sin realizar ningún cambio de mayúsculas y minúsculas.
- `Proof` corrige texto largo, proporciona correcciones de mayúsculas y minúsculas y puntuación básica y suprime correcciones agresivas.

Para usar Bing Spell Check API, se debe obtener una clave de API. Esto puede obtenerse en [pruebe Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)

Para obtener una lista de los idiomas admitidos por Bing Spell Check API, consulte [idiomas admitidos](/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages/). Para obtener más información acerca de Bing Spell Check API, consulte [documentación de Bing Spell comprobar](/azure/cognitive-services/bing-spell-check/).

## <a name="authentication"></a>Autenticación

Cada solicitud realizada a Bing Spell Check API necesita una clave de API que se debe especificar como el valor de la `Ocp-Apim-Subscription-Key` encabezado. En el ejemplo de código siguiente se muestra cómo agregar la clave de API para el `Ocp-Apim-Subscription-Key` encabezado de una solicitud:

```csharp
public BingSpellCheckService()
{
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", Constants.BingSpellCheckApiKey);
}
```

Error al pasar una clave de API válida a Bing Spell Check API se producirá un error en la 401 respuesta.

## <a name="performing-spell-checking"></a>Realizar la revisión ortográfica

Corrector ortográfico puede lograrse mediante una solicitud GET o POST a la `SpellCheck` API en `https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck`. Al realizar una solicitud GET, el texto que se puede comprobar la ortografía se envía como un parámetro de consulta. Al realizar una solicitud POST, se envía el texto que se puede comprobar la ortografía en el cuerpo de solicitud. Las solicitudes GET se limitan a corrector ortográfico 1500 caracteres debido a la limitación de longitud de cadena de parámetro de consulta. Por lo tanto, normalmente se deben realizar las solicitudes POST a menos que las cadenas cortas son que se va a comprobar la ortografía.

En la aplicación de ejemplo, el `SpellCheckTextAsync` método invoca el proceso de revisión ortográfica:

```csharp
public async Task<SpellCheckResult> SpellCheckTextAsync(string text)
{
    string requestUri = GenerateRequestUri(Constants.BingSpellCheckEndpoint, text, SpellCheckMode.Spell);
    var response = await SendRequestAsync(requestUri);
    var spellCheckResults = JsonConvert.DeserializeObject<SpellCheckResult>(response);
    return spellCheckResults;
}
```

El `SpellCheckTextAsync` método genera un URI de solicitud y, a continuación, envía la solicitud para el `SpellCheck` API, que devuelve una respuesta JSON que contiene el resultado. La respuesta JSON se deserializa, con lo que se devuelve al método de llamada para su presentación.

### <a name="configuring-spell-checking"></a>Configurar el corrector ortográfico

El proceso de revisión ortográfica puede configurarse mediante la especificación de parámetros de consulta HTTP:

```csharp
string GenerateRequestUri(string spellCheckEndpoint, string text, SpellCheckMode mode)
{
  string requestUri = spellCheckEndpoint;
  requestUri += string.Format("?text={0}", text);                         // text to spell check
  requestUri += string.Format("&mode={0}", mode.ToString().ToLower());    // spellcheck mode - proof or spell
  return requestUri;
}
```

Este método establece el texto que se puede comprobar la ortografía y el modo de comprobación de corrección ortográfica.

Para obtener más información acerca de Bing Spell Check REST API, consulte [referencia de Spell Check API v7](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/).

### <a name="sending-the-request"></a>Enviar la solicitud

El `SendRequestAsync` método realiza la solicitud GET a Bing Spell Check REST API y devuelve la respuesta:

```csharp
async Task<string> SendRequestAsync(string url)
{
    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

Este método envía la solicitud GET a la `SpellCheck` API con la URL de solicitud especifica el texto que se deben traducir y el modo de comprobación de corrección ortográfica. La respuesta, a continuación, lee y devuelve al método de llamada.

El `SpellCheck` API enviará el código de estado HTTP 200 (OK) en la respuesta, siempre que la solicitud es válida, lo que indica que la solicitud es correcta y que la información solicitada está en la respuesta. Para obtener una lista de objetos de respuesta, consulte [objetos de respuesta](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#response-objects).

### <a name="processing-the-response"></a>Procesamiento de la respuesta

Se devuelve la respuesta de API en formato JSON. Los siguientes datos JSON muestran el mensaje de respuesta para el texto mal escrito `Go shappin tommorow`:

```json
{  
   "_type":"SpellCheck",
   "flaggedTokens":[  
      {  
         "offset":3,
         "token":"shappin",
         "type":"UnknownToken",
         "suggestions":[  
            {  
               "suggestion":"shopping",
               "score":1
            }
         ]
      },
      {  
         "offset":11,
         "token":"tommorow",
         "type":"UnknownToken",
         "suggestions":[  
            {  
               "suggestion":"tomorrow",
               "score":1
            }
         ]
      }
   ],
   "correctionType":"High"
}
```

El `flaggedTokens` matriz contiene una matriz de palabras del texto que se han marcado como no se ha escrito correctamente o que están gramaticalmente incorrecto. La matriz estará vacía si no hay errores ortográficos o gramaticales se encuentran. Las etiquetas dentro de la matriz son:

- `offset` – un desplazamiento de base cero desde el principio de la cadena de texto a la palabra que se marcó.
- `token` : la palabra en la cadena de texto que no se ha escrito correctamente o no es correcta gramaticalmente.
- `type` : el tipo de error que provocó la palabra que se marcará. Hay dos valores posibles: `RepeatedToken` y `UnknownToken`.
- `suggestions` : una matriz de palabras que se corregirá el error de ortografía o gramática. La matriz está formada por un `suggestion` y un `score`, lo que indica el nivel de confianza de que la corrección sugerida es correcta.

En la aplicación de ejemplo, la respuesta JSON se deserializa en un `SpellCheckResult` instancia, con lo que se devuelve al método de llamada para su presentación. El siguiente ejemplo de código muestra cómo el `SpellCheckResult` instancia se procesa para su presentación:

```csharp
var spellCheckResult = await bingSpellCheckService.SpellCheckTextAsync(TodoItem.Name);
foreach (var flaggedToken in spellCheckResult.FlaggedTokens)
{
  TodoItem.Name = TodoItem.Name.Replace(flaggedToken.Token, flaggedToken.Suggestions.FirstOrDefault().Suggestion);
}
```

Este código recorre el `FlaggedTokens` colección y reemplaza cualquier mal escrita o palabras gramaticalmente incorrectas en el texto de origen con la primera sugerencia. Las capturas de pantalla siguientes se muestran antes y después de la revisión ortográfica:

![](spell-check-images/before-spell-check.png "Antes de comprobar la ortografía")

![](spell-check-images/after-spell-check.png "Después de comprobar la ortografía")

> [!NOTE]
> En el ejemplo anterior `Replace` se usa para simplificar, pero en una gran cantidad de texto podría reemplazar el token equivocado. La API proporciona el `offset` valor que debe usarse en las aplicaciones de producción para identificar la ubicación correcta en el texto de origen para realizar una actualización.

## <a name="summary"></a>Resumen

En este artículo se explica cómo usar Bing Spell Check REST API para corregir errores ortográficos en una aplicación de Xamarin.Forms. Bing Spell Check realiza contextual corrector ortográfico para el texto, que proporciona sugerencias de en línea para palabras mal escritas.

## <a name="related-links"></a>Vínculos relacionados

- [Documentación de Bing Spell Check](/azure/cognitive-services/bing-spell-check/)
- [Consumo de un servicio web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Todo Cognitive Services (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [Referencia de Bing Spell Check API v7](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)
