---
title: Reconocimiento de voz con la API de Microsoft Speech
description: Microsoft Speech API es una API basada en la nube que proporciona algoritmos para procesar el lenguaje oral. En este artículo se explica cómo usar la API de REST de reconocimiento de voz de Microsoft para convertir audio en texto en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: B435FF6B-8785-48D9-B2D9-1893F5A87EA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: eca079972f4e46c0cf60c4749658ff9a7fe1791b
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489809"
---
# <a name="speech-recognition-using-the-microsoft-speech-api"></a>Reconocimiento de voz con la API de Microsoft Speech

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Microsoft Speech API es una API basada en la nube que proporciona algoritmos para procesar el lenguaje hablado. En este artículo se explica cómo usar la API de REST de reconocimiento de voz de Microsoft para convertir audio en texto en una aplicación de Xamarin. Forms._

## <a name="overview"></a>Información general del

Microsoft Speech API tiene dos componentes:

- Un reconocimiento de voz API para convertir las palabras habladas en texto. El reconocimiento de voz puede realizarse a través de una API de REST, la biblioteca de cliente o la biblioteca de servicio.
- Un texto a voz API para convertir texto en palabras habladas. Conversión de texto a voz se realiza a través de una API de REST.

En este artículo se centra en realizar el reconocimiento de voz a través de la API de REST. Mientras que las bibliotecas de cliente y el servicio admiten la devolución de resultados parciales, la API de REST solo puede devolver un resultado de reconocimiento único, sin los resultados parciales.

> [!NOTE]
> Si no tiene una [suscripción a Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), cree una [cuenta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de empezar.

Para usar la API de voz de Microsoft, se debe obtener una clave de API. Esto se puede obtener desde Azure [portal](https://portal.azure.com/). Para obtener más información, consulte [crear una cuenta de Cognitive Services en el portal de Azure](/azure/cognitive-services/cognitive-services-apis-create-account).

Para obtener más información acerca de la API de voz de Microsoft, consulte [documentación de Microsoft Speech API](/azure/cognitive-services/speech/home/).

## <a name="authentication"></a>Autenticación

Todas las solicitudes realizadas a la API de REST de voz de Microsoft requieren un token de acceso JSON Web Token (JWT), que puede obtenerse desde el servicio de token de cognitive services en `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. Se puede obtener un token mediante una solicitud POST al servicio de token, especificando un `Ocp-Apim-Subscription-Key` encabezado que contiene la clave de API como su valor.

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

Debe especificarse el token de acceso en cada API de REST de Microsoft Speech llamar como una `Authorization` el prefijo con la cadena de encabezado `Bearer`, tal y como se muestra en el ejemplo de código siguiente:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

Un error pasar un token de acceso válido a la API de REST de Microsoft Speech provocará un error de 403 respuesta.

## <a name="performing-speech-recognition"></a>Realizar el reconocimiento de voz

El reconocimiento de voz se logra mediante una solicitud POST a la `recognition` API en `https://speech.platform.bing.com/speech/recognition/`. Una única solicitud no puede contener más de 10 segundos de audio, y la duración total de solicitudes no puede superar 14 segundos.

Contenido de audio se debe colocar en el cuerpo de POST de la solicitud en formato wav.

En la aplicación de ejemplo, el `RecognizeSpeechAsync` método invoca el proceso de reconocimiento de voz:

```csharp
public async Task<SpeechResult> RecognizeSpeechAsync(string filename)
{
    ...

    // Read audio file to a stream
    var file = await PCLStorage.FileSystem.Current.LocalStorage.GetFileAsync(filename);
    var fileStream = await file.OpenAsync(PCLStorage.FileAccess.Read);

    // Send audio stream to Bing and deserialize the response
    string requestUri = GenerateRequestUri(Constants.SpeechRecognitionEndpoint);
    string accessToken = authenticationService.GetAccessToken();
    var response = await SendRequestAsync(fileStream, requestUri, accessToken, Constants.AudioContentType);
    var speechResult = JsonConvert.DeserializeObject<SpeechResult>(response);

    fileStream.Dispose();
    return speechResult;
}
```

Audio se registra en cada proyecto específico de plataforma como datos wav PCM y el `RecognizeSpeechAsync` método usa el `PCLStorage` paquete NuGet para abrir el archivo de audio como una secuencia. Se recupera la solicitud de reconocimiento de voz se genera el URI y un token de acceso al servicio de token. La solicitud de reconocimiento de voz se registra en el `recognition` API, que devuelve una respuesta JSON que contiene el resultado. La respuesta JSON se deserializa, con lo que se devuelve al método de llamada para su presentación.

### <a name="configuring-speech-recognition"></a>Configurar el reconocimiento de voz

El proceso de reconocimiento de voz se puede configurar mediante la especificación de parámetros de consulta HTTP:

```csharp
string GenerateRequestUri(string speechEndpoint)
{
    // To build a request URL, you should follow:
    // https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest
    string requestUri = speechEndpoint;
    requestUri += @"dictation/cognitiveservices/v1?";
    requestUri += @"language=en-us";
    requestUri += @"&format=simple";
    System.Diagnostics.Debug.WriteLine(requestUri.ToString());
    return requestUri;
}
```

La configuración principal realizada por el `GenerateRequestUri` método consiste en establecer la configuración regional del contenido de audio. Para obtener una lista de las configuraciones regionales admitidas, consulte [idiomas admitidos](/azure/cognitive-services/speech/api-reference-rest/supportedlanguages/).

### <a name="sending-the-request"></a>Enviar la solicitud

El `SendRequestAsync` método realiza la solicitud POST a la API de REST de voz de Microsoft y devuelve la respuesta:

```csharp
async Task<string> SendRequestAsync(Stream fileStream, string url, string bearerToken, string contentType)
{
    if (httpClient == null)
    {
        httpClient = new HttpClient();
    }
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);

    var content = new StreamContent(fileStream);
    content.Headers.TryAddWithoutValidation("Content-Type", contentType);
    var response = await httpClient.PostAsync(url, content);
    return await response.Content.ReadAsStringAsync();
}
```

Este método crea la solicitud de POST:

- Contiene la secuencia de audio en un `StreamContent` instancia, que proporciona contenido HTTP basado en una secuencia.
- Establecer el `Content-Type` encabezado de la solicitud a `audio/wav; codec="audio/pcm"; samplerate=16000`.
- Agregar el token de acceso a la `Authorization` encabezado, el prefijo con la cadena `Bearer`.

A continuación, se envía la solicitud POST a `recognition` API. La respuesta, a continuación, lee y devuelve al método de llamada.

El `recognition` API enviará el código de estado HTTP 200 (OK) en la respuesta, siempre que la solicitud es válida, lo que indica que la solicitud es correcta y que la información solicitada está en la respuesta. Para obtener una lista de posibles respuestas de error, consulte [Troubleshooting](/azure/cognitive-services/speech/troubleshooting).

### <a name="processing-the-response"></a>Procesamiento de la respuesta

La respuesta de API se devuelve en formato JSON, con el texto reconocido que esté incluido en la `name` etiqueta. Los siguientes datos JSON muestran un mensaje de respuesta correcta típico:

```json
{  
   "RecognitionStatus":"Success",
   "DisplayText":"Go shopping tomorrow.",
   "Offset":16000000,
   "Duration":17100000
}
```

En la aplicación de ejemplo, la respuesta JSON se deserializa en un `SpeechResult` instancia, con lo que se devuelve al método de llamada para su presentación, como se muestra en las capturas de pantalla siguiente:

![](speech-recognition-images/speech-recognition.png "Speech Recognition")

## <a name="summary"></a>Resumen

En este artículo se explica cómo usar la API de REST de voz de Microsoft para convertir audio en texto en una aplicación de Xamarin.Forms. Además de realizar el reconocimiento de voz, Speech API de Microsoft también puede convertir texto en palabras habladas.

## <a name="related-links"></a>Vínculos relacionados

- [Documentación de Microsoft Speech API](/azure/cognitive-services/speech/home/).
- [Consumo de un servicio web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Todo Cognitive Services (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
