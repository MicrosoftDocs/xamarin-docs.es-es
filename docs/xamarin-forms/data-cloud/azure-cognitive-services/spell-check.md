---
title: ''
description: Bing Spell Check realiza la revisión ortográfica contextual del texto, proporcionando sugerencias en línea para palabras mal escritas. En este artículo se explica cómo usar la API de REST de Bing Spell Check para corregir errores ortográficos en una Xamarin.Forms aplicación.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1703f0049408381a86da73fb28696ef8708cc790
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139300"
---
# <a name="spell-checking-using-the-bing-spell-check-api"></a>Revisión ortográfica mediante el Bing Spell Check API

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Bing Spell Check realiza la revisión ortográfica contextual del texto, proporcionando sugerencias en línea para palabras mal escritas. En este artículo se explica cómo usar la API de REST de Bing Spell Check para corregir errores ortográficos en una Xamarin.Forms aplicación._

## <a name="overview"></a>Información general

La API de REST de Bing Spell Check tiene dos modos operativos y se debe especificar un modo al realizar una solicitud a la API:

- `Spell`corrige texto corto (hasta 9 palabras) sin cambios de mayúsculas y minúsculas.
- `Proof`corrige texto largo, proporciona correcciones de mayúsculas y minúsculas y puntuación básica y suprime las correcciones agresivas.

> [!NOTE]
> Si no tiene una [suscripción a Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), cree una [cuenta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de empezar.

Se debe obtener una clave de API para usar el Bing Spell Check API. Esto puede obtenerse en el [intento Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)

Para obtener una lista de los idiomas admitidos por el Bing Spell Check API, consulte [idiomas admitidos](/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages/). Para obtener más información sobre el Bing Spell Check API, consulte la [documentación de Bing spell check](/azure/cognitive-services/bing-spell-check/).

## <a name="authentication"></a>Authentication

Cada solicitud realizada al Bing Spell Check API requiere una clave de API que debe especificarse como el valor del `Ocp-Apim-Subscription-Key` encabezado. En el ejemplo de código siguiente se muestra cómo agregar la clave de API al `Ocp-Apim-Subscription-Key` encabezado de una solicitud:

```csharp
public BingSpellCheckService()
{
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", Constants.BingSpellCheckApiKey);
}
```

Si no se pasa una clave de API válida a la Bing Spell Check API, se producirá un error de respuesta 401.

## <a name="performing-spell-checking"></a>Realización de revisión ortográfica

La revisión ortográfica se puede lograr realizando una solicitud GET o POST a la `SpellCheck` API en `https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck` . Al realizar una solicitud GET, el texto que se va a revisar se envía como un parámetro de consulta. Al realizar una solicitud POST, el texto que se va a revisar se envía en el cuerpo de la solicitud. Las solicitudes GET se limitan a la revisión ortográfica de 1500 caracteres debidos a la limitación de longitud de cadena de parámetro de consulta. Por lo tanto, las solicitudes POST deben realizarse normalmente a menos que se Compruebe la ortografía de las cadenas cortas.

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

El `SpellCheckTextAsync` método genera un URI de solicitud y, a continuación, envía la solicitud a la `SpellCheck` API, que devuelve una respuesta JSON que contiene el resultado. La respuesta JSON se deserializa, con el resultado devuelto al método de llamada para su presentación.

### <a name="configuring-spell-checking"></a>Configurar la revisión ortográfica

El proceso de revisión ortográfica se puede configurar especificando parámetros de consulta HTTP:

```csharp
string GenerateRequestUri(string spellCheckEndpoint, string text, SpellCheckMode mode)
{
  string requestUri = spellCheckEndpoint;
  requestUri += string.Format("?text={0}", text);                         // text to spell check
  requestUri += string.Format("&mode={0}", mode.ToString().ToLower());    // spellcheck mode - proof or spell
  return requestUri;
}
```

Este método establece el texto que se va a comprobar ortográfica y el modo de revisión ortográfica.

Para obtener más información acerca de la API de REST de Bing Spell Check, consulte [referencia de spell check API V7](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/).

### <a name="sending-the-request"></a>Envío de la solicitud

El `SendRequestAsync` método realiza la solicitud get a la API de REST de Bing spell check y devuelve la respuesta:

```csharp
async Task<string> SendRequestAsync(string url)
{
    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

Este método envía la solicitud GET a la `SpellCheck` API, con la dirección URL de la solicitud que especifica el texto que se va a traducir y el modo de revisión ortográfica. A continuación, la respuesta se lee y se devuelve al método de llamada.

La `SpellCheck` API enviará el código de Estado HTTP 200 (OK) en la respuesta, siempre que la solicitud sea válida, lo que indica que la solicitud se realizó correctamente y que la información solicitada se encuentra en la respuesta. Para obtener una lista de objetos de respuesta, vea [objetos de respuesta](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#response-objects).

### <a name="processing-the-response"></a>Procesar la respuesta

La respuesta de la API se devuelve en formato JSON. Los siguientes datos JSON muestran el mensaje de respuesta para el texto mal escrito `Go shappin tommorow` :

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

La `flaggedTokens` matriz contiene una matriz de palabras en el texto que se han marcado como no escritas correctamente o que son gramaticalmente incorrectas. La matriz estará vacía si no se encuentran errores ortográficos o gramaticales. Las etiquetas dentro de la matriz son:

- `offset`: un desplazamiento de base cero desde el principio de la cadena de texto hasta la palabra que se marcó.
- `token`: la palabra de la cadena de texto que no está escrita correctamente o es gramaticalmente incorrecta.
- `type`: el tipo de error que provocó la marca de la palabra. Hay dos valores posibles: `RepeatedToken` y `UnknownToken` .
- `suggestions`: una matriz de palabras en las que se corregirá el error ortográfico o gramatical. La matriz se compone de un `suggestion` y un `score` , que indica el nivel de confianza que la corrección sugerida es correcta.

En la aplicación de ejemplo, la respuesta JSON se deserializa en una `SpellCheckResult` instancia de, con el resultado devuelto al método de llamada para su presentación. En el ejemplo de código siguiente se muestra cómo `SpellCheckResult` se procesa la instancia para su presentación:

```csharp
var spellCheckResult = await bingSpellCheckService.SpellCheckTextAsync(TodoItem.Name);
foreach (var flaggedToken in spellCheckResult.FlaggedTokens)
{
  TodoItem.Name = TodoItem.Name.Replace(flaggedToken.Token, flaggedToken.Suggestions.FirstOrDefault().Suggestion);
}
```

Este código recorre en iteración la `FlaggedTokens` colección y reemplaza las palabras mal escritas o gramaticalmente incorrectas en el texto de origen con la primera sugerencia. Las capturas de pantallas siguientes se muestran antes y después de la revisión ortográfica:

![](spell-check-images/before-spell-check.png "Before Spell Check")

![](spell-check-images/after-spell-check.png "After Spell Check")

> [!NOTE]
> En el ejemplo anterior `Replace` se usa para simplificar, pero en una gran cantidad de texto podría reemplazar el token equivocado. La API proporciona el `offset` valor que debe usarse en las aplicaciones de producción para identificar la ubicación correcta en el texto de origen para realizar una actualización.

## <a name="summary"></a>Resumen

En este artículo se explica cómo usar la API de REST de Bing Spell Check para corregir errores ortográficos en una Xamarin.Forms aplicación. Bing Spell Check realiza la revisión ortográfica contextual del texto, proporcionando sugerencias en línea para palabras mal escritas.

## <a name="related-links"></a>Vínculos relacionados

- [Bing Spell Check documentación](/azure/cognitive-services/bing-spell-check/)
- [Consumo de un servicio web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Cognitive Services todo (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [Referencia de Bing Spell Check API V7](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)
