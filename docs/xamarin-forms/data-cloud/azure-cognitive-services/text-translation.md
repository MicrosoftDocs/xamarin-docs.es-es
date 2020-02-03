---
title: Traducción de texto mediante la API del traductor
description: Microsoft Translator API puede usarse para traducir el texto de voz y a través de una API de REST. En este artículo se explica cómo usar Microsoft Translator Text API para traducir texto de un idioma a otro en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 68330242-92C5-46F1-B1E3-2395D8823B0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 841b1d4abab5e4c09249174b221da20794771a86
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725573"
---
# <a name="text-translation-using-the-translator-api"></a>Traducción de texto mediante la API del traductor

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_La API de traductor de Microsoft se puede usar para traducir la voz y el texto a través de una API de REST. En este artículo se explica cómo usar Microsoft Translator Text API para traducir texto de un idioma a otro en una aplicación de Xamarin. Forms._

## <a name="overview"></a>Información general

La API del traductor tiene dos componentes:

- Una API de REST para traducir texto de un idioma en el texto de otro idioma de traducción de texto. La API detecta automáticamente el idioma del texto que se ha enviado antes de trasladarlo.
- Una API de REST para transcriba la voz de un idioma en el texto de otro idioma de traducción de voz. La API también integra las funcionalidades de texto a voz para volver a hablar el texto traducido.

En este artículo se centra en traducir el texto de un idioma a otro utilizando Translator Text API.

> [!NOTE]
> Si no tiene una [suscripción a Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), cree una [cuenta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de empezar.

Para usar Translator Text API, se debe obtener una clave de API. Esto puede obtenerse en [Cómo suscribirse a Microsoft Translator Text API](/azure/cognitive-services/translator/translator-text-how-to-signup/).

Para obtener más información acerca de Microsoft Translator Text API, consulte la [documentación de Translator Text API](/azure/cognitive-services/translator/).

## <a name="authentication"></a>Authentication

Cada solicitud realizada al Translator Text API requiere un token de acceso JSON Web Token (JWT), que se puede obtener del servicio de token de servicios cognitivos en `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. Un token se puede obtener realizando una solicitud POST al servicio de token, especificando un encabezado `Ocp-Apim-Subscription-Key` que contiene la clave de API como su valor.

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

El token de acceso debe especificarse en cada llamada Translator Text API como un encabezado de `Authorization` con el prefijo `Bearer`de la cadena, como se muestra en el ejemplo de código siguiente:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

Para obtener más información sobre el servicio de token de servicios cognitivos, vea [autenticación](/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="performing-text-translation"></a>Realizar la traducción de texto

La traducción de texto se puede lograr mediante la realización de una solicitud GET a la API de `translate` en `https://api.microsofttranslator.com/v2/http.svc/translate`. En la aplicación de ejemplo, el método `TranslateTextAsync` invoca el proceso de traducción de texto:

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

El método `TranslateTextAsync` genera un URI de solicitud y recupera un token de acceso del servicio de token. La solicitud de traducción de texto se envía a la API de `translate`, que devuelve una respuesta XML que contiene el resultado. Se analiza la respuesta XML y se devuelve el resultado de la traducción al método de llamada para su presentación.

Para obtener más información sobre las API de REST de traducción de texto, consulte [Translator Text API](/azure/cognitive-services/translator/reference/v3-0-reference).

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

Este método establece el texto que se deben traducir y el idioma para traducir el texto para. Para obtener una lista de los idiomas admitidos por Microsoft Translator, consulte [idiomas admitidos en microsoft Translator Text API](/azure/cognitive-services/translator/languages/).

> [!NOTE]
> Si una aplicación necesita saber en qué idioma se encuentra el texto, se puede llamar a la API de `Detect` para detectar el idioma de la cadena de texto.

### <a name="sending-the-request"></a>Enviar la solicitud

El método `SendRequestAsync` realiza la solicitud GET a la API de REST de traducción de texto y devuelve la respuesta:

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

Este método compila la solicitud GET agregando el token de acceso al encabezado de `Authorization`, con el prefijo de la cadena `Bearer`. La solicitud GET se envía a la API de `translate`, con la dirección URL de la solicitud que especifica el texto que se va a traducir y el idioma al que se va a traducir el texto. La respuesta, a continuación, lee y devuelve al método de llamada.

La API de `translate` enviará el código de Estado HTTP 200 (correcto) en la respuesta, siempre que la solicitud sea válida, lo que indica que la solicitud se realizó correctamente y que la información solicitada se encuentra en la respuesta. Para obtener una lista de las posibles respuestas de error, consulte mensajes de respuesta en [obtener traducción](/azure/cognitive-services/translator/reference/v3-0-translate).

### <a name="processing-the-response"></a>Procesamiento de la respuesta

La respuesta de API se devuelve en formato XML. Los siguientes datos XML muestran un mensaje de respuesta correcta típico:

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

En la aplicación de ejemplo, la respuesta XML se analiza en una instancia de `XDocument`, con el valor raíz de XML que se devuelve al método de llamada para su presentación, tal y como se muestra en las siguientes capturas de pantalla:

![](text-translation-images/text-translation.png "Text Translation to German")

## <a name="summary"></a>Resumen

En este artículo se explica cómo usar Microsoft Translator Text API traducir texto de un idioma al texto de otro idioma en una aplicación de Xamarin.Forms. Además de traducir el texto, Microsoft Translator API también pueden transcribir voz de un idioma en el texto de otro idioma.

## <a name="related-links"></a>Vínculos relacionados

- [Translator Text API documentación](/azure/cognitive-services/translator/)
- [Consumo de un servicio web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Cognitive Services todo (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [Translator Text API](/azure/cognitive-services/translator/reference/v3-0-reference)
