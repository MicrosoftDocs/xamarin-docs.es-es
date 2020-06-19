---
title: Reconocimiento de emociones percibido mediante el Face API
description: El Face API toma una expresión facial en una imagen como entrada y devuelve datos que incluyen niveles de confianza en un conjunto de emociones para cada una de las caras de la imagen. En este artículo se explica cómo usar el Face API para reconocer emociones, para clasificar una Xamarin.Forms aplicación.
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ff384605b35f6406b628da99de500b550da811c9
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136063"
---
# <a name="perceived-emotion-recognition-using-the-face-api"></a>Reconocimiento de emociones percibido mediante el Face API

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

El Face API puede realizar la detección de emociones para detectar enojo, desprecio, asco, miedo, felicidad, neutralidad, tristeza y sorpresa, en una expresión facial en función de las anotaciones percibidas por los codificadores humanos. Sin embargo, es importante tener en cuenta que las expresiones faciales solas no representan necesariamente los Estados internos de las personas.

Además de devolver un resultado de emoción para una expresión facial, el Face API también puede devolver un cuadro de límite para los rostros detectados.

El reconocimiento de emociones puede realizarse a través de una biblioteca de cliente y a través de una API de REST. Este artículo se centra en realizar el reconocimiento de emociones a través de la API de REST. Para más información sobre la API de REST, consulte la [API de REST de caras](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

El Face API también puede usarse para reconocer las expresiones faciales de las personas en vídeo y puede devolver un resumen de sus emociones. Para obtener más información, consulte [análisis de vídeos en tiempo real](/azure/cognitive-services/face/face-api-how-to-topics/howtoanalyzevideo_face/).

> [!NOTE]
> Si no tiene una [suscripción a Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), cree una [cuenta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de empezar.

Se debe obtener una clave de API para usar el Face API. Esto puede obtenerse en el [intento Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

Para obtener más información sobre el Face API, vea [face API](/azure/cognitive-services/face/overview/).

## <a name="authentication"></a>Authentication

Cada solicitud realizada al Face API requiere una clave de API que debe especificarse como el valor del `Ocp-Apim-Subscription-Key` encabezado. En el ejemplo de código siguiente se muestra cómo agregar la clave de API al `Ocp-Apim-Subscription-Key` encabezado de una solicitud:

```csharp
public FaceRecognitionService()
{
  _client = new HttpClient();
  _client.DefaultRequestHeaders.Add("ocp-apim-subscription-key", Constants.FaceApiKey);
}
```

Si no se pasa una clave de API válida a la Face API, se producirá un error de respuesta 401.

## <a name="perform-emotion-recognition"></a>Realizar el reconocimiento de emociones

El reconocimiento de emociones se realiza mediante una solicitud POST que contiene una imagen a la `detect` API en `https://[location].api.cognitive.microsoft.com/face/v1.0` , donde `[location]]` es la región que se usó para obtener la clave de API. Los parámetros de solicitud opcionales son:

- `returnFaceId`: indica si se van a devolver faceIds de los rostros detectados. El valor predeterminado es `true`.
- `returnFaceLandmarks`: indica si se van a devolver puntos de referencia faciales de los rostros detectados. El valor predeterminado es `false`.
- `returnFaceAttributes`: si se analizan y devuelven uno o varios atributos de la esfera especificados. Los atributos de caras admitidos son `age` , `gender` , `headPose` , `smile` , `facialHair` , `glasses` , `emotion` , `hair` , `makeup` , `occlusion` , `accessories` ,, `blur` `exposure` y `noise` . Tenga en cuenta que el análisis de atributos faciales tiene un costo adicional de cálculo y tiempo.

El contenido de la imagen debe colocarse en el cuerpo de la solicitud POST como una dirección URL o datos binarios.

> [!NOTE]
> Los formatos de archivo de imagen admitidos son JPEG, PNG, GIF y BMP, y el tamaño de archivo permitido es de 1 KB a 4 MB.

En la aplicación de ejemplo, el proceso de reconocimiento de emociones se invoca llamando al `DetectAsync` método:

```csharp
Face[] faces = await _faceRecognitionService.DetectAsync(photoStream, true, false, new FaceAttributeType[] { FaceAttributeType.Emotion });
```

Esta llamada al método especifica la secuencia que contiene los datos de la imagen, que se debe devolver faceIds, que no se deben devolver puntos de referencia de caras y que se debe analizar la emoción de la imagen. También especifica que los resultados se devolverán como una matriz de `Face` objetos. A su vez, el `DetectAsync` método invoca la `detect` API de REST que realiza el reconocimiento de emociones:

```csharp
public async Task<Face[]> DetectAsync(Stream imageStream, bool returnFaceId, bool returnFaceLandmarks, IEnumerable<FaceAttributeType> returnFaceAttributes)
{
  var requestUrl =
    $"{Constants.FaceEndpoint}/detect?returnFaceId={returnFaceId}" +
    "&returnFaceLandmarks={returnFaceLandmarks}" +
    "&returnFaceAttributes={GetAttributeString(returnFaceAttributes)}";
  return await SendRequestAsync<Stream, Face[]>(HttpMethod.Post, requestUrl, imageStream);
}
```

Este método genera un URI de solicitud y, a continuación, envía la solicitud a la `detect` API a través del `SendRequestAsync` método.

> [!NOTE]
> Debe usar la misma región en las llamadas a Face API que usó para obtener las claves de suscripción. Por ejemplo, si ha obtenido las claves de suscripción de la `westus` región, el punto de conexión de detección de caras será `https://westus.api.cognitive.microsoft.com/face/v1.0/detect` .

### <a name="send-the-request"></a>Envío de la solicitud

El `SendRequestAsync` método realiza la solicitud post al Face API y devuelve el resultado como una `Face` matriz:

```csharp
async Task<TResponse> SendRequestAsync<TRequest, TResponse>(HttpMethod httpMethod, string requestUrl, TRequest requestBody)
{
  var request = new HttpRequestMessage(httpMethod, Constants.FaceEndpoint);
  request.RequestUri = new Uri(requestUrl);
  if (requestBody != null)
  {
    if (requestBody is Stream)
    {
      request.Content = new StreamContent(requestBody as Stream);
      request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
    }
    else
    {
      // If the image is supplied via a URL
      request.Content = new StringContent(JsonConvert.SerializeObject(requestBody, s_settings), Encoding.UTF8, "application/json");
    }
  }

  HttpResponseMessage responseMessage = await _client.SendAsync(request);
  if (responseMessage.IsSuccessStatusCode)
  {
    string responseContent = null;
    if (responseMessage.Content != null)
    {
      responseContent = await responseMessage.Content.ReadAsStringAsync();
    }
    if (!string.IsNullOrWhiteSpace(responseContent))
    {
      return JsonConvert.DeserializeObject<TResponse>(responseContent, s_settings);
    }
    return default(TResponse);
  }
  else
  {
    ...
  }
  return default(TResponse);
}
```

Si la imagen se proporciona a través de una secuencia, el método compila la solicitud POST encapsulando el flujo de imagen en una `StreamContent` instancia, que proporciona contenido http basado en una secuencia. Como alternativa, si la imagen se proporciona a través de una dirección URL, el método genera la solicitud POST encapsulando la dirección URL en una `StringContent` instancia de, que proporciona contenido http basado en una cadena.

La solicitud POST se envía a la `detect` API. La respuesta se lee, se deserializa y se devuelve al método de llamada.

La `detect` API enviará el código de Estado HTTP 200 (OK) en la respuesta, siempre que la solicitud sea válida, lo que indica que la solicitud se realizó correctamente y que la información solicitada se encuentra en la respuesta. Para obtener una lista de las posibles respuestas de error, consulte la [API de REST de caras](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

### <a name="process-the-response"></a>Procesamiento de la respuesta

La respuesta de la API se devuelve en formato JSON. Los siguientes datos JSON muestran un mensaje de respuesta correcta típico que proporciona los datos solicitados por la aplicación de ejemplo:

```json
[  
   {  
      "faceId":"8a1a80fe-1027-48cf-a7f0-e61c0f005051",
      "faceRectangle":{  
         "top":192,
         "left":164,
         "width":339,
         "height":339
      },
      "faceAttributes":{  
         "emotion":{  
            "anger":0.0,
            "contempt":0.0,
            "disgust":0.0,
            "fear":0.0,
            "happiness":1.0,
            "neutral":0.0,
            "sadness":0.0,
            "surprise":0.0
         }
      }
   }
]
```

Un mensaje de respuesta correcta consta de una matriz de entradas de caras clasificada por tamaño de rectángulo facial en orden descendente, mientras que una respuesta vacía indica que no se han detectado rostros. Cada una de las caras reconocidas incluye una serie de atributos de caras opcionales, que se especifican mediante el `returnFaceAttributes` argumento para el `DetectAsync` método.

En la aplicación de ejemplo, la respuesta JSON se deserializa en una matriz de `Face` objetos. Al interpretar los resultados de la Face API, la emoción detectada debe interpretarse como la emoción con la puntuación más alta, ya que las puntuaciones se normalizan para sumar a una. Por lo tanto, la aplicación de ejemplo muestra la emoción reconocida con la puntuación más alta para la mayor superficie detectada en la imagen. Esto se consigue con el código siguiente:

```csharp
emotionResultLabel.Text = faces.FirstOrDefault().FaceAttributes.Emotion.ToRankedList().FirstOrDefault().Key;
```

En la captura de pantalla siguiente se muestra el resultado del proceso de reconocimiento de emociones en la aplicación de ejemplo:

![](emotion-recognition-images/emotion-recognition.png "Emotion Recognition")

## <a name="related-links"></a>Vínculos relacionados

- [Face API](/azure/cognitive-services/face/overview/).
- [Cognitive Services todo (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [API de REST de caras](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
