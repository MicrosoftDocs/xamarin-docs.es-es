---
title: Traducción de texto mediante la API del traductor
description: Microsoft Translator API puede usarse para traducir el texto de voz y a través de una API de REST. En este artículo se explica cómo usar Microsoft Translator Text API para traducir texto de un idioma a otro en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 68330242-92C5-46F1-B1E3-2395D8823B0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 4f73ea249d29075b0e9e115e86afc971632b7b61
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487508"
---
# <a name="text-translation-using-the-translator-api"></a>Traducción de texto mediante la API del traductor

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_La API de traductor de Microsoft se puede usar para traducir la voz y el texto a través de una API de REST. En este artículo se explica cómo usar Microsoft Translator Text API para traducir texto de un idioma a otro en una aplicación de Xamarin. Forms._

## <a name="overview"></a>Información general del

La API del traductor tiene dos componentes:

- Una API de REST para traducir texto de un idioma en el texto de otro idioma de traducción de texto. La API detecta automáticamente el idioma del texto que se ha enviado antes de trasladarlo.
- Una API de REST para transcriba la voz de un idioma en el texto de otro idioma de traducción de voz. La API también integra capacidades de texto a voz para hablar de vuelta el texto traducido.

En este artículo se centra en traducir el texto de un idioma a otro utilizando Translator Text API.

> [!NOTE]
> Si no tiene una [suscripción a Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), cree una [cuenta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de empezar.

Para usar Translator Text API, se debe obtener una clave de API. Esto puede obtenerse en [cómo suscribirse a Microsoft Translator Text API](/azure/cognitive-services/translator/translator-text-how-to-signup/).

Para obtener más información acerca de Microsoft Translator Text API, consulte [documentación de Translator Text API](/azure/cognitive-services/translator/).

## <a name="authentication"></a>Autenticación

Cada solicitud realizada a Translator Text API requiere un token de acceso JSON Web Token (JWT), que puede obtenerse desde el servicio de token de cognitive services en `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. Se puede obtener un token mediante una solicitud POST al servicio de token, especificando un `Ocp-Apim-Subscription-Key` encabezado que contiene la clave de API como su valor.

En el ejemplo de código siguiente se muestra cómo solicitar un acceso token al servicio de token:

```csharp
public AuthenticationService(string apiKey)
{
    subscriptionKey = apiKey;
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
}
...
async Task<string> FetchTokenAsync(string fetchUri)
{
    UriBuilder uriBuilder = new UriBuilder(fetchUri);
    uriBuilder.Path += "/issueToken";
    var result = await httpClient.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
    return await result.Content.ReadAsStringAsync();
}
```

El token de acceso devuelta, que es texto Base64, tiene una fecha de expiración de 10 minutos. Por lo tanto, la aplicación de ejemplo renueva el token de acceso cada 9 minutos.

Debe especificarse el token de acceso en cada Translator Text API llamar como una `Authorization` el prefijo con la cadena de encabezado `Bearer`, tal y como se muestra en el ejemplo de código siguiente:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

Para obtener más información sobre el servicio de token de cognitive services, consulte [API de Token de autenticación](https://docs.microsofttranslator.com/oauth-token.html).

## <a name="performing-text-translation"></a>Realizar la traducción de texto

Traducción de texto se puede lograr mediante una solicitud GET a la `translate` API en `https://api.microsofttranslator.com/v2/http.svc/translate`. En la aplicación de ejemplo, el `TranslateTextAsync` método invoca el proceso de traducción de texto:

```csharp
public async Task<string> TranslateTextAsync(string text)
{
  ...
  string requestUri = GenerateRequestUri(Constants.TextTranslatorEndpoint, text, "en", "de");
  string accessToken = authenticationService.GetAccessToken();
  var response = await SendRequestAsync(requestUri, accessToken);
  var xml = XDocument.Parse(response);
  return xml.Root.Value;
}
```

El `TranslateTextAsync` método genera un URI de solicitud y recupera un token de acceso al servicio de token. A continuación, se envía la solicitud de traducción de texto a la `translate` API, que devuelve una respuesta XML que contiene el resultado. Se analiza la respuesta XML y se devuelve el resultado de la traducción al método de llamada para su presentación.

Para obtener más información acerca de las API de REST de traducción de texto, consulte [Microsoft Translator Text API](https://docs.microsofttranslator.com/text-translate.html).

### <a name="configuring-text-translation"></a>Configurar la traducción de texto

El proceso de traducción de texto se puede configurar mediante la especificación de parámetros de consulta HTTP:

```csharp
string GenerateRequestUri(string endpoint, string text, string to)
{
  string requestUri = endpoint;
  requestUri += string.Format("?text={0}", Uri.EscapeUriString(text));
  requestUri += string.Format("&to={0}", to);
  return requestUri;
}
```

Este método establece el texto que se deben traducir y el idioma para traducir el texto para. Para obtener una lista de los idiomas compatibles con Microsoft Translator, consulte [idiomas admitidos en Microsoft Translator Text API](/azure/cognitive-services/translator/languages/).

> [!NOTE]
> Si una aplicación necesita saber qué idioma está el texto, el `Detect` API se puede llamar para detectar el idioma de la cadena de texto.

### <a name="sending-the-request"></a>Enviar la solicitud

El `SendRequestAsync` método realiza la solicitud GET a la API de REST de traducción de texto y devuelve la respuesta:

```csharp
async Task<string> SendRequestAsync(string url, string bearerToken)
{
    if (httpClient == null)
    {
        httpClient = new HttpClient();
    }
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);

    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

Este método basa la solicitud GET mediante la adición de token de acceso para el `Authorization` encabezado, el prefijo con la cadena `Bearer`. A continuación, se envía la solicitud GET a la `translate` API con la URL de solicitud especifica el texto que se deben traducir y el idioma para traducir el texto a. La respuesta, a continuación, lee y devuelve al método de llamada.

El `translate` API enviará el código de estado HTTP 200 (OK) en la respuesta, siempre que la solicitud es válida, lo que indica que la solicitud es correcta y que la información solicitada está en la respuesta. Para obtener una lista de posibles respuestas de error, vea los mensajes de respuesta en [obtener traducir](https://docs.microsofttranslator.com/text-translate.html#!/default/get_Translate).

### <a name="processing-the-response"></a>Procesamiento de la respuesta

La respuesta de API se devuelve en formato XML. Los siguientes datos XML muestran un mensaje de respuesta correcta típico:

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

En la aplicación de ejemplo, se analiza la respuesta XML en un `XDocument` instancia, con el valor de la raíz XML que se devuelve al método de llamada para su presentación, como se muestra en las capturas de pantalla siguiente:

![](text-translation-images/text-translation.png "Text Translation to German")

## <a name="summary"></a>Resumen

En este artículo se explica cómo usar Microsoft Translator Text API traducir texto de un idioma al texto de otro idioma en una aplicación de Xamarin.Forms. Además de traducir el texto, Microsoft Translator API también pueden transcribir voz de un idioma en el texto de otro idioma.

## <a name="related-links"></a>Vínculos relacionados

- [Documentación de Translator Text API](/azure/cognitive-services/translator/).
- [Consumo de un servicio web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Todo Cognitive Services (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [Microsoft Translator Text API](https://docs.microsofttranslator.com/text-translate.html).
