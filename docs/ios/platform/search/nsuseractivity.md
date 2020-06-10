---
title: Búsqueda con NSUserActivity en Xamarin. iOS
description: En este documento se describe cómo indexar un NSUserActivity, lo que permite que se pueda buscar en Spotlight y Safari. Describe cómo responder a la selección de un NSUserActivity en los resultados de la búsqueda.
ms.prod: xamarin
ms.assetid: 0B28B284-C7C9-4C0D-A782-D471FBBC4CAE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: cbf5f8c6f53b075f587a0e7763a4019d44352f14
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84569003"
---
# <a name="search-with-nsuseractivity-in-xamarinios"></a>Búsqueda con NSUserActivity en Xamarin. iOS

`NSUserActivity`se presentó en iOS 8 y se usa para proporcionar los datos que se van a entregar.
Permite crear actividades en partes específicas de la aplicación que luego se pueden pasar a otra instancia de la aplicación que se ejecuta en un dispositivo iOS diferente. Después, el dispositivo receptor puede continuar con la actividad iniciada en el dispositivo anterior, lo que se seleccionará justo donde el usuario se quedó sin conexión. Para obtener más información sobre el uso de la entrega, consulte nuestra [Introducción a](~/ios/platform/handoff.md) la documentación de la entrega.

Novedad de iOS 9, `NSUserActivity` se puede indexar (tanto de forma pública como privada) y de buscar en la búsqueda de Spotlight y en Safari. Al marcar un `NSUserActivity` como que permite búsquedas y agregar metadatos indexables, la actividad se puede enumerar en los resultados de la búsqueda en el dispositivo iOS.

[![](nsuseractivity-images/apphistory01.png "The App History overview")](nsuseractivity-images/apphistory01.png#lightbox)

Si el usuario selecciona un resultado de búsqueda que pertenece a una actividad de la aplicación, se iniciará la aplicación y la actividad que se describe en se `NSUserActivity` reiniciará y se mostrará al usuario.

Las siguientes propiedades de `NSUserActivity` se usan para admitir la búsqueda de aplicaciones:

- `EligibleForHandoff`: Si `true` es, esta actividad se puede usar en una operación de entrega.
- `EligibleForSearch`: Si `true` es, esta actividad se agregará al índice en el dispositivo y se mostrará en los resultados de la búsqueda.
- `EligibleForPublicIndexing`: Si es `true` , esta actividad se agregará al índice basado en la nube de Apple y se presentará a los usuarios (a través de la búsqueda) que no hayan instalado ya la aplicación en su dispositivo iOS. Consulte la sección [indexación de búsqueda pública](#public-search-indexing) a continuación para obtener más detalles.
- `Title`: Proporciona un título para la actividad y se muestra en los resultados de la búsqueda. Los usuarios también pueden buscar el texto del propio título.
- `Keywords`: Es una matriz de cadenas que se usa para describir la actividad que se indexará y hará que el usuario final pueda realizar búsquedas.
- `ContentAttributeSet`: Es un `CSSearchableItemAttributeSet` que se usa para describir más detalladamente la actividad y proporcionar contenido enriquecido en los resultados de la búsqueda.
- `ExpirationDate`: Si desea que una actividad solo se muestre hasta una fecha determinada, puede especificar esa fecha aquí.
- `WebpageURL`: Si la actividad se puede ver en la web o si la aplicación admite vínculos profundos de Safari, puede establecer el vínculo para visitar aquí.

## <a name="nsuseractivity-quickstart"></a>Inicio rápido de NSUserActivity

Siga estas instrucciones para implementar una búsqueda `NSUserActivity` en la aplicación:

- [Crear identificadores de tipo de actividad](#creatingtypeid)
- [Crear una actividad](#createactivity)
- [Responder a una actividad](#respondactivity)
- [Indexación de búsqueda pública](#indexing)

El uso [additional benefits](#benefits) `NSUserActivity` de para hacer búsquedas en el contenido tiene algunas ventajas adicionales.

<a name="creatingtypeid"></a>

## <a name="creating-activity-type-identifiers"></a>Crear identificadores de tipo de actividad

Antes de poder crear una actividad de búsqueda, debe crear un identificador de _tipo de actividad_ para identificarla. El identificador de tipo de actividad es una cadena corta agregada a la `NSUserActivityTypes` matriz del archivo **info. plist** de la aplicación que se usa para identificar de forma única un tipo de actividad de usuario determinado. Habrá una entrada en la matriz para cada actividad que la aplicación admita y exponga a la búsqueda de aplicaciones. 

Apple sugiere el uso de una notación de estilo DNS inversa para el identificador de tipo de actividad con el fin de evitar colisiones. Por ejemplo: `com.company-name.appname.activity` para actividades basadas en aplicaciones específicas o `com.company-name.activity` para actividades que se pueden ejecutar en varias aplicaciones.

El identificador de tipo de actividad se usa al crear una `NSUserActivity` instancia de para identificar el tipo de actividad. Cuando una actividad continúa a medida que el usuario pulsa un resultado de búsqueda, el tipo de actividad (junto con el identificador de equipo de la aplicación) determina qué aplicación se debe iniciar para continuar con la actividad.

Para crear los identificadores de tipo de actividad necesarios para admitir este comportamiento, edite el archivo **info. plist** y cambie a la vista de **código fuente** . Agregue una `NSUserActivityTypes` clave y cree los identificadores en el siguiente formato:

[![](nsuseractivity-images/type01.png "The NSUserActivityTypes key and required identifiers in the plist editor")](nsuseractivity-images/type01.png#lightbox)

En el ejemplo anterior, creamos un nuevo identificador de tipo de actividad para la actividad de búsqueda ( `com.xamarin.platform` ). Al crear sus propias aplicaciones, reemplace el contenido de la `NSUserActivityTypes` matriz por los identificadores de tipo de actividad específicos de las actividades que admite la aplicación.

<a name="createactivity"></a>

## <a name="creating-an-activity"></a>Crear una actividad

El siguiente es un ejemplo de la creación de una actividad para una búsqueda hospedada de índice en el dispositivo:

```csharp
// Create App Search Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Add App Search ability
activity.EligibleForSearch = true;
activity.BecomeCurrent();
```

Podríamos agregar más detalles estableciendo la `ContentAttributeSet` propiedad de la manera `NSUserActivity` siguiente:

[![](nsuseractivity-images/apphistory02.png "Addition Search Details overview")](nsuseractivity-images/apphistory02.png#lightbox)

Mediante el uso de `ContentAttributeSet` , puede crear resultados de búsqueda enriquecidos que adquieran que el usuario final interactúe con ellos.

<a name="respondactivity"></a>

## <a name="responding-to-an-activity"></a>Responder a una actividad

Para responder al toque del usuario en un resultado de búsqueda ( `NSUserActivity` ) de nuestra aplicación, edite el archivo **AppDelegate.CS** e invalide el `ContinueUserActivity` método. Por ejemplo:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    }

    return true;
}
```

Tenga en cuenta que se trata de la misma invalidación de método que se usa para responder a las solicitudes de entrega. Ahora, si el usuario hace clic en un vínculo de nuestra aplicación en los resultados de búsqueda de Spotlight, la aplicación se enviará al primer plano (o se iniciará si no se está ejecutando) y se mostrará el contenido, la navegación o la característica representada por ese vínculo:

[![](nsuseractivity-images/apphistory03.png "Restore Previous State from Search")](nsuseractivity-images/apphistory03.png#lightbox)

<a name="indexing"></a>

## <a name="public-search-indexing"></a>Indexación de búsqueda pública

Como hemos visto anteriormente, iOS 9 facilita el acceso de búsqueda al contenido y las características de la aplicación que el usuario ya ha descubierto y usado en un dispositivo iOS determinado. Con la indexación pública, iOS 9 proporciona una manera para los usuarios que todavía no han descubierto contenido o características (o que no han instalado aún la aplicación) para obtener los resultados también en sus búsquedas.

La indexación pública funciona de la siguiente manera:

1. Al crear una actividad para la aplicación, puede marcarla como pública.
2. Las actividades públicas se envían a Apple y se indexan en la nube.
3. A medida que más usuarios interactúan con la aplicación en un dispositivo y usan la característica o el contenido específico que representa la actividad, aumenta en rango.
4. Los resultados públicos más populares estarán disponibles para otros usuarios, incluso si no tienen la aplicación instalada.
5. Estos resultados públicos se mostrarán en la búsqueda de Spotlight y en Safari (si la actividad incluye una dirección URL).

Podemos tomar la actividad de búsqueda privada que hemos creado anteriormente y expandirla para que sea pública:

```csharp
// Create App Search Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Add App Search ability
activity.EligibleForSearch = true;
activity.EligibleForPublicIndexing = true;
activity.BecomeCurrent();
```

Solo porque se ha establecido una actividad para la indexación pública estableciendo `EligibleForPublicIndexing = true` , no significa que se agregue automáticamente al índice de nube pública de Apple. Primero se deben cumplir las condiciones siguientes:

1. Debe aparecer en los resultados de la búsqueda y ser seleccionado por muchos usuarios. Los resultados permanecen privados hasta que se alcanza un umbral de compromiso de actividad.
2. El aprovisionamiento de aplicaciones evita que cualquier dato específico del usuario se pueda indexar y hacer público.

<a name="benefits"></a>

## <a name="additional-benefits"></a>Ventajas adicionales

Al adoptar la búsqueda de aplicaciones a través `NSUserActivity` de en la aplicación, también obtiene las siguientes características:

- **Promisión:** puesto que la búsqueda de aplicaciones está exponiendo contenido, navegación o características con el mismo mecanismo que la entrega ( `NSUserActivity` ), puede permitir fácilmente que los usuarios de la aplicación inicien una actividad en un dispositivo y continúen en otro.
- **Sugerencias de Siri** : junto con las sugerencias estándar que las sugerencias de Siri suelen realizar, se pueden sugerir automáticamente los activos de la aplicación.
- **Siri avisos inteligentes** : los usuarios podrán preguntar a Siri para recordarles las actividades de la aplicación.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guía de programación de búsqueda de aplicaciones](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
- [Entrada de blog & ejemplo](https://blog.xamarin.com/improve-discoverability-with-search-in-ios-9/)
