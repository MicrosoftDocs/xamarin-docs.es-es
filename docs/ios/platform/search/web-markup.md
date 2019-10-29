---
title: Buscar con marcado Web en Xamarin. iOS
description: En este documento se describe cómo crear los resultados de la búsqueda basada en Web que se vinculan a una aplicación de Xamarin. iOS. Describe cómo habilitar la indización de contenido Web, lo que permite que el sitio web de la aplicación sea reconocible, mediante banners de aplicaciones inteligentes, vínculos universales, etc.
ms.prod: xamarin
ms.assetid: 876315BA-2EF9-4275-AE33-A3A494BBF7FD
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 3d5db2f060b59fc689bea99141342b0447ac8933
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031527"
---
# <a name="search-with-web-markup-in-xamarinios"></a>Buscar con marcado Web en Xamarin. iOS

En el caso de las aplicaciones que proporcionan acceso a su contenido a través de un sitio web (no solo desde dentro de la aplicación), el contenido web se puede marcar con vínculos especiales que va a rastrear Apple y proporcionar un vínculo profundo a la aplicación en el dispositivo iOS 9 del usuario.

Si la aplicación de iOS ya admite la vinculación profunda móvil y el sitio web presentó vínculos profundos al contenido dentro de la aplicación, _Applebot_ web Crawler de Apple indexará este contenido y lo agregará automáticamente a su índice de nube:

[![](web-markup-images/webmarkup01.png "Cloud Index overview")](web-markup-images/webmarkup01.png#lightbox)

Apple mostrará estos resultados en los resultados de búsqueda de Spotlight y de la búsqueda de Safari.
Si el usuario pulsa uno de estos resultados (y tiene la aplicación instalada), se le dirigirá al contenido de la aplicación:

[![](web-markup-images/webmarkup02.png "Deep linking from a website in search results")](web-markup-images/webmarkup02.png#lightbox)

## <a name="enabling-web-content-indexing"></a>Habilitar la indización de contenido web

Hay cuatro pasos necesarios para que el contenido de la aplicación se pueda buscar mediante el marcado Web:

1. Asegúrese de que Apple pueda detectar e indexar el sitio web de la aplicación mediante su definición como sitio web de **soporte técnico** o de **marketing** en iTunes Connect.
2. Asegúrese de que el sitio web de la aplicación contiene el marcado necesario para implementar la vinculación profunda móvil. Consulte las secciones siguientes para obtener más detalles.
3. Habilite el control de vínculos profundos en la aplicación iOS.
4. Agregue marcado para los datos estructurados que se muestran en el sitio web de la aplicación para proporcionar un resultado enriquecido y atractivo al usuario final. Aunque este paso no es estrictamente necesario, Apple es muy recomendable.

En las secciones siguientes se detallan los pasos.

## <a name="make-your-apps-website-discoverable"></a>Haga que el sitio web de la aplicación sea reconocible

La manera más fácil de hacer que Apple busque el sitio web de la aplicación es usarlo como sitio web de **soporte técnico** o de **marketing** al enviar la aplicación a Apple a través de iTunes Connect.

## <a name="using-smart-app-banners"></a>Uso de banners de aplicaciones inteligentes

Proporcione un banner de Smart app en el sitio web para presentar un vínculo claro a la aplicación. Si la aplicación aún no está instalada, Safari solicitará automáticamente al usuario que instale la aplicación. En caso contrario, el uso puede pulsar el vínculo **Ver** para iniciar la aplicación desde el sitio Web. Por ejemplo, para crear un banner de Smart App, puede usar el código siguiente:

```html
<meta name="AppName" content="app-id=123456, app-argument=http://company.com/AppName">
```

Para más información, consulte la documentación sobre la [promoción de aplicaciones con banners](https://developer.apple.com/library/ios/documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html) de aplicaciones inteligentes de Apple.

## <a name="using-universal-links"></a>Usar vínculos universales

Como novedad de iOS 9, los vínculos universales proporcionan una mejor alternativa a los titulares de aplicaciones inteligentes o a los esquemas de direcciones URL personalizadas existentes al proporcionar lo siguiente:

- **Único** : la misma dirección URL no se puede reclamar por más de un sitio Web.
- **Seguro** : se requiere un certificado firmado para el sitio web que garantiza que el sitio web es propiedad del usuario y que se vincula de forma válida a la aplicación.
- **Flexible** : el usuario final puede controlar si la dirección URL inicia el sitio web o la aplicación.
- **Universal** : se puede usar la misma dirección URL para definir el contenido del sitio web y de la aplicación.

## <a name="using-twitter-cards"></a>Uso de tarjetas de Twitter

Puede proporcionar vínculos profundos al contenido de la aplicación mediante una tarjeta de Twitter. Por ejemplo:

```html
<meta name="twitter:app:name:iphone" content="AppName">
<meta name="twitter:app:id:iphone" content="AppNameID">
<meta name="twitter:app:url:iphone" content="AppNameURL">
```

Para obtener más información, consulte la documentación del Protocolo de la [tarjeta Twitter](https://developer.twitter.com/en/docs/tweets/optimize-with-cards/overview/abouts-cards) de Twitter.

## <a name="using-facebook-app-links"></a>Uso de vínculos de aplicación de Facebook

Puede proporcionar vínculos profundos al contenido de la aplicación mediante un vínculo de aplicación de Facebook. Por ejemplo:

```html
<meta property="al:ios:app_name" content="AppName">
<meta property="al:ios:app_store_id" content="AppNameID">
<meta property="al:ios:url" content="AppNameURL">
```

Para obtener más información, consulte la documentación de los vínculos de la [aplicación](https://developers.facebook.com/docs/applinks) de Facebook.

## <a name="opening-deep-links"></a>Abrir vínculos profundos

Debe agregar compatibilidad para abrir y Mostrar vínculos profundos en la aplicación de Xamarin. iOS. Edite el archivo **AppDelegate.CS** e invalide el método `OpenURL` para controlar el formato de la dirección URL personalizada. Por ejemplo:

```csharp
public override bool OpenUrl (UIApplication application, NSUrl url, string sourceApplication, NSObject annotation)
{

  // Handling a URL in the form http://company.com/appname/?123
  try {
    var components = new NSUrlComponents(url,true);
    var path = components.Path;
    var query = components.Query;

    // Is this a known format?
    if (path == "/appname") {
      // Display the view controller for the content
      // specified in query (123)
      return ContentViewController.LoadContent(query);
    }
  } catch {
    // Ignore issue for now
  }

  return false;
}
```

En el código anterior, buscamos una dirección URL que contenga `/appname` y pasando el valor de `query` (`123` en este ejemplo) a un controlador de vista personalizado en nuestra aplicación para mostrar el contenido solicitado al usuario.

## <a name="providing-rich-results-with-structured-data"></a>Proporcionar resultados enriquecidos con datos estructurados

Al incluir el marcado de datos estructurados, puede proporcionar resultados de búsqueda enriquecidos al usuario final que van más allá de simplemente un título y una descripción. Incluir imágenes, datos específicos de la aplicación (como clasificaciones) y acciones para los resultados mediante el marcado de datos estructurados.

Los resultados enriquecidos son más atractivos y pueden ayudar a mejorar la clasificación en el índice de búsqueda basado en la nube, ya que se favorece a más usuarios para que interactúen con ellos.

Una opción para proporcionar marcado de datos estructurados es mediante Open Graph. Por ejemplo:

```html
<meta property="og:image" content="http://company.com/appname/icon.jpg">
<meta property="og:audio" content="http://company.com/appname/theme.m4a">
<meta property="og:video" content="http://company.com/appname/tutorial.mp4">
```

Para obtener más información, vea el sitio web de [Open Graph](https://ogp.me) .

Otro formato común para el marcado de datos estructurados es esquema. microdatos de la organización. Por ejemplo:

```html
<div itemprop="aggregateRating" itemscope itemtype="http://schema.org/AggregateRating">
  <span itemprop="ratingValue">4** stars -
  <span itemprop="reviewCount">255** reviews
```

La misma información se puede representar en el formato JSON-LD del esquema.

```html
<script type="application/ld+json">
  "@content":"http://schema.org",
  "@type":"AggregateRating",
  "ratingValue":"4",
  "reviewCount":"255"
</script>
```

A continuación se muestra un ejemplo de metadatos del sitio web que proporcionan resultados de búsqueda enriquecidos al usuario final:

[![](web-markup-images/deeplink01.png "Rich search results via Structured Data Markup")](web-markup-images/deeplink01.png#lightbox)

Apple admite actualmente los siguientes tipos de esquema de schema.org:

- AggregateRating
- ImageObject
- InteractionCount
- Aporta
- Organización
- Rangoprecio
- Receta
- SearchAction

Para obtener más información sobre estos tipos de esquema, vea [Schema.org](https://schema.org).

## <a name="providing-actions-with-structured-data"></a>Proporcionar acciones con datos estructurados

Los tipos específicos de datos estructurados permitirán que el usuario final pueda accionar un resultado de búsqueda. Actualmente se admiten las siguientes acciones:

- Marcar un número de teléfono.
- Obtener la dirección de asignación a una dirección determinada.
- Reproducir un archivo de audio o vídeo.

Por ejemplo, la definición de una acción para marcar un número de teléfono podría ser similar a la siguiente:

```html
<div itemscope itemtype="http://schema.org/Organization">
  <span itemprop="telephone">(408) 555-1212**
```

Cuando se presente el resultado de la búsqueda al usuario final, se mostrará un icono de teléfono pequeño en el resultado. Si el usuario pulsa el icono, se llamará al número especificado.

El siguiente código HTML agregaría una acción para reproducir un archivo de audio desde el resultado de la búsqueda:

```html
<div itemscope itemtype="http://schema.org/AudioObject">
  <span itemprop="contentUrl">http://company.com/appname/greeting.m4a**
```

Por último, el siguiente código HTML agregaría una acción para obtener direcciones del resultado de la búsqueda:

```html
<div itemscope itemtype="http://schema.org/PostalAddress">
  <span itemprop="streetAddress">1 Infinite Loop**
  <span itemprop="addressLocality">Cupertino**
  <span itemprop="addressRegion">CA**
  <span itemprop="postalCode">95014**
```

Para obtener más información, consulte el [sitio para desarrolladores de búsqueda de aplicaciones](https://developer.apple.com/ios/search/)de Apple.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guía de programación de búsqueda de aplicaciones](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
