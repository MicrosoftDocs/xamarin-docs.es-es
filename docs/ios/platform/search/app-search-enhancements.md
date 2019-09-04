---
title: Mejoras en la búsqueda de aplicaciones en Xamarin. iOS
description: En este artículo se describen las mejoras que Apple ha realizado en la búsqueda de aplicaciones en iOS 10 y cómo implementarlas en Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 30124DB6-6A02-4F66-A2D9-BBC8008E6B48
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: ec7523ac2adc3a6b4ba18a7b8a0fe21749bd7856
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227411"
---
# <a name="app-search-enhancements-in-xamarinios"></a>Mejoras en la búsqueda de aplicaciones en Xamarin. iOS

_En este artículo se describen las mejoras que Apple ha realizado en la búsqueda de aplicaciones en iOS 10 y cómo implementarlas en Xamarin. iOS._

En iOS 10, Apple ha realizado varias mejoras en la búsqueda de aplicaciones, como la vinculación profunda de micromecenazgo, la búsqueda en la aplicación, la continuación de la búsqueda y la visualización de los resultados de la validación. En este artículo se trata la implementación de estas características en una aplicación Xamarin. iOS.

## <a name="about-app-search-enhancements"></a>Acerca de las mejoras de búsqueda de aplicaciones

Los principales avances en iOS 10 proporcionan varias mejoras en la búsqueda de aplicaciones, como:

- **Micromecenazgo popularidad de vínculos profundos (con privacidad diferencial)** : proporciona una manera de promover el contenido de una aplicación vinculada en profundidad en los resultados de la búsqueda.
- **Búsqueda en la aplicación** : Use la nueva `CSSearchQuery` clase para proporcionar una capacidad de búsqueda de contenido destacado en la aplicación similar a la forma en que funcionan las aplicaciones mail, messages y Notes.
- **Continuación** de la búsqueda: permite que un usuario inicie una búsqueda en Spotlight o Safari, abra una aplicación y continúe con la búsqueda.
- **Visualización de los resultados de la validación** : la herramienta de validación de la [API de búsqueda de aplicaciones](https://search.developer.apple.com/appsearch-validation-tool) de Apple ahora muestra una representación visual del marcado de un sitio web y de la vinculación profunda al realizar pruebas.
- **Uso compartido de imágenes de aplicación de mensajes** : permite que las imágenes de la aplicación populares que se proporcionan para compartir mensajes (a través de una extensión de aplicación de mensajes) aparezcan en búsquedas de Spotlight.

En las secciones siguientes se tratarán estos temas con más detalle.

## <a name="crowdsourced-deep-link-popularity"></a>Popularidad de vínculo profundo de micromecenazgo

iOS 10 proporciona un mecanismo para contar la frecuencia con la que el usuario sigue los vínculos profundos más populares en una aplicación y usa esta información para mejorar la clasificación del contenido de una aplicación en los resultados de la búsqueda, al tiempo que se sigue protegiendo la identidad del usuario mediante la *diferencia Privacidad*.

En el caso de las `NSUserActivity` aplicaciones que usan objetos para proporcionar direcciones URL de vínculo `EligibleForPublicIndexing` profundo y tienen `true`la propiedad establecida en, iOS 10 envía un subconjunto de hashes de *privacidad diferencial* a los servidores de Apple. Esta información se usa para promover el contenido popular en la aplicación en los resultados de la búsqueda.

Para más información sobre la implementación de la vinculación profunda en una aplicación de Xamarin. iOS, consulte la documentación de [búsqueda con NSUserActivity](~/ios/platform/search/nsuseractivity.md) .

## <a name="in-app-searching"></a>Búsqueda en la aplicación

Al implementar la nueva clase [CSSearchQuery](https://developer.apple.com/reference/corespotlight/cssearchquery) , una aplicación puede proporcionar la tecnología de búsqueda y coincidencia de Spotlight para buscar contenido dentro de sí mismo, sin que el usuario tenga que salir de la aplicación (de forma similar a cómo funciona el correo electrónico, los mensajes y la aplicación de notas).

Normalmente, las aplicaciones que `CSSearchQuery` admiten no tendrán que mantener su propio índice de búsqueda independiente.

## <a name="search-continuation"></a>Continuación de búsqueda

En iOS 9, Apple presentó las API de búsqueda (como Spotlight principal `NSUserActivity` y marcado Web) para ofrecer un contenido de gran interés dentro de una aplicación para permitir que los usuarios busquen el contenido mediante las interfaces de búsqueda de Spotlight y Safari. Consulte la documentación de la [nueva API de búsqueda](~/ios/platform/search/index.md) para obtener más detalles.

En iOS 10 Apple se basa en esta característica, ya que permite al usuario iniciar una búsqueda en Spotlight o Safari y luego continuar con la búsqueda cuando abra una aplicación.

Para implementar esta característica, edite el archivo `Info.plist` de la aplicación, `CoreSpotlightContinuation` agregue la clave de tipo **Boolean** y establezca su `YES`valor en:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](app-search-enhancements-images/search01.png "Edición de CoreSpotlightContinuation en el archivo info. plist")](app-search-enhancements-images/search01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](app-search-enhancements-images/searchw01.png "Edición de CoreSpotlightContinuation en el archivo info. plist")](app-search-enhancements-images/search01.png#lightbox)

-----

Para responder al usuario que continúa el resultado de la`NSUserActivity`búsqueda (), `AppDelegate.cs` edite el archivo `ContinueUserActivity` e invalide el método. Por ejemplo:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    default:
        if (userActivity.ActivityType == CSSearchQuery.ContinuationActionType) {
            var search = userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString);
            // Continue user's search here...
        }
        break;
    }

    return true;
}
```

Este código busca el tipo de acción de continuación de`userActivity.ActivityType == CSSearchQuery.ContinuationActionType`consulta () y, a continuación, lee la consulta `NSUserActivity` actual del usuario del Diccionario de`userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString)`información de usuario de la clase (). Desde aquí, la aplicación debe tomar medidas para continuar la búsqueda del usuario.

Para más información sobre cómo trabajar con búsquedas en una aplicación de Xamarin. iOS, consulte nuestra documentación de [búsqueda con Core Spotlight](~/ios/platform/search/corespotlight.md) .

## <a name="visualization-of-validation-results"></a>Visualización de los resultados de la validación

La herramienta de validación de la [API de búsqueda de aplicaciones](https://search.developer.apple.com/appsearch-validation-tool) de Apple ahora muestra una representación visual del marcado de un sitio web y de la vinculación profunda (incluido el marcado, como se define en [Schema.org](http://schema.org/)) al realizar pruebas de forma rápida.

Mediante el uso de la herramienta de validación, un desarrollador puede ver la información que el rastreador web Applebot ha indexado para el sitio, como el título, la descripción, la dirección URL y cualquier otro elemento compatible.

Para obtener más información sobre cómo trabajar con el marcado Web, consulte nuestra documentación de [Buscar con marcado web](~/ios/platform/search/web-markup.md) .

## <a name="message-app-image-sharing"></a>Uso compartido de imágenes de aplicación de mensajes

Si una extensión de aplicación de mensaje proporciona imágenes para compartir en los mensajes, la extensión puede configurarse para permitir que el usuario realice búsquedas destacadas de imágenes populares dentro de los mensajes, sin tener que salir de la aplicación.

Para habilitar esta característica, haga lo siguiente:

1. Cree una extensión de aplicación de mensaje.
2. `com.apple.developer.associated-domains` Agregue a los derechos de la aplicación e incluya una lista de dominios web que hospeden las imágenes que la extensión de aplicación de mensajes está compartiendo. Especifique el `spotlight-image-search` servicio para cada dominio.
3. Agregue un `apple-app-site-association` archivo al sitio web que hospeda las imágenes. Este archivo incluye un diccionario para el `spotlight-image-search` servicio e incluye el identificador de la aplicación, que es el identificador del equipo o el prefijo de identificador de la aplicación seguido del identificador de paquete. El archivo puede contener hasta 500 rutas de acceso y patrones que serán indexados por Spotlight e incluidos en búsquedas de imágenes populares. Para obtener más información, consulte la documentación sobre [la creación y carga de archivos de asociación](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW4) de Apple.
4. Permita que el Applebot Rastree los sitios Web. Consulte la documentación [sobre Applebot](https://support.apple.com/HT204683) de Apple.

Consulte la documentación de [integración de aplicaciones de mensajes](~/ios/platform/message-app-integration/index.md) para obtener más información.

## <a name="summary"></a>Resumen

En este artículo se han explicado las mejoras que Apple ha realizado en la búsqueda de aplicaciones en iOS 10 y cómo implementarlas en Xamarin. iOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
