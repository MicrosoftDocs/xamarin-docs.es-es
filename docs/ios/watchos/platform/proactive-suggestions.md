---
title: Sugerencias proactivas de watchos en Xamarin
description: En este artículo se muestra cómo usar las sugerencias proactivas en una aplicación de watchos 3 para impulsar la interacción, lo que permite que el sistema presente de forma proactiva información útil automáticamente al usuario.
ms.prod: xamarin
ms.assetid: 10CC9F16-963C-44F1-8B98-F09FB2310DFF
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: 68ba09ce74f161c728f0a58276a0b0d98fec7d8c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70281285"
---
# <a name="watchos-proactive-suggestions-in-xamarin"></a>Sugerencias proactivas de watchos en Xamarin

_En este artículo se muestra cómo usar las sugerencias proactivas en una aplicación de watchos 3 para impulsar la interacción, lo que permite que el sistema presente de forma proactiva información útil automáticamente al usuario._


Novedades de watchos 3, las sugerencias proactivas presentan a los usuarios las formas de interactuar con una aplicación de Xamarin. iOS de forma proactiva que presenten de forma proactiva información útil al usuario en los momentos adecuados.


## <a name="about-proactive-suggestions"></a>Acerca de las sugerencias proactivas

Novedad de watchos 3, `NSUserActivity` incluye una `MapItem` propiedad que permite que la aplicación proporcione información de ubicación que se puede usar en otros contextos. Por ejemplo, si la aplicación muestra las revisiones de Hotel y `MapItem` proporciona una ubicación, si el usuario cambió a la aplicación Maps, la ubicación del hotel que estaba viendo está disponible.

La aplicación expone esta funcionalidad al sistema mediante una colección de tecnologías como `NSUserActivity`, por ejemplo, MapKit, Media Player y UIKit. Además, al proporcionar compatibilidad proactiva de sugerencias para la aplicación, la integración de Siri es más profunda de forma gratuita.

## <a name="location-based-suggestions"></a>Sugerencias basadas en la ubicación

Como novedad de watchos 3, `NSUserActivity` la clase incluye `MapItem` una propiedad que permite al desarrollador proporcionar información de ubicación que se puede usar en otros contextos. Por ejemplo, si la aplicación muestra revisiones de restaurante, el desarrollador puede establecer `MapItem` la propiedad en la ubicación del restaurante que el usuario está viendo en la aplicación. Si el usuario cambia a la aplicación Maps, la ubicación del restaurante está disponible automáticamente.

Si la aplicación admite la búsqueda de aplicaciones, puede usar los nuevos componentes de dirección `CSSearchableItemAttributesSet` de la clase para especificar las ubicaciones que el usuario puede querer visitar. Al establecer la `MapItem` propiedad, las demás propiedades se rellenan automáticamente.

Además de establecer `Latitude` y `Longitude` de las propiedades del componente de dirección, se recomienda que la aplicación proporcione también las `NamedLocation` propiedades `PhoneNumbers` y, por lo que Siri puede iniciar una llamada a la ubicación.

## <a name="contextual-siri-reminders"></a>Recordatorios contextuales de Siri

Permite al usuario usar Siri para realizar rápidamente un recordatorio para ver el contenido que está viendo actualmente en la aplicación en otro momento. Por ejemplo, si estuviera viendo una opinión de un restaurante en la aplicación, podría invocar a Siri y decir *"avisarme sobre esto cuando llegue a casa".* Siri generaría el recordatorio con un vínculo a la revisión de la aplicación.

## <a name="implementing-proactive-suggestions"></a>Implementación de sugerencias proactivas

Agregar compatibilidad con las sugerencias proactiva a la aplicación de Xamarin. iOS suele ser tan sencillo como implementar algunas API o expandir algunas API que es posible que la aplicación ya esté implementando.

Las sugerencias proactivas funcionan con las aplicaciones de tres maneras principales:

- **`NSUserActivity`** : Ayuda al sistema a comprender la información con la que el usuario está trabajando actualmente en la pantalla.
- **Sugerencias de ubicación** : Si la aplicación ofrece o usa información basada en la ubicación, esta extensión de API ofrece nuevas maneras de compartir esta información entre aplicaciones.

Y se admite en la aplicación mediante la implementación de lo siguiente:

- Los **recordatorios de Siri contextuales** (en `NSUserActivity` iOS 10) se han ampliado para permitir que Siri realice rápidamente un recordatorio para ver el contenido que actualmente está viendo en la aplicación en otro momento.
- **Sugerencias de ubicación** : iOS 10 mejora `NSUserActivity` en las ubicaciones de captura que se ven dentro de la aplicación y las promueve en muchos lugares en todo el sistema.
- **Las**  -  solicitudes`NSUserActivity` contextuales de Siri proporcionan el contexto de la información que se presenta dentro de la aplicación a Siri para que el usuario pueda obtener instrucciones o realizar una llamada invocando Siri desde dentro de la aplicación.

Todas estas características tienen una cosa en común, todas usan `NSUserActivity` en un formulario o en otra para proporcionar su funcionalidad. 

## <a name="nsuseractivity"></a>NSUserActivity

Como se indicó anteriormente `NSUserActivity` , ayuda al sistema a comprender la información con la que el usuario está trabajando actualmente en la pantalla. `NSUserActivity`es un mecanismo de almacenamiento en caché de estado ligero para capturar la actividad del usuario mientras navega por la aplicación. Por ejemplo, examinando la aplicación de restaurante:

[![](proactive-suggestions-images/activity02.png "La aplicación restaurante")](proactive-suggestions-images/activity02.png#lightbox)

Con las siguientes interacciones:

1. A medida que el usuario trabaja con la aplicación `NSUserActivity` , se crea un para volver a crear el estado de la aplicación más adelante.
2. Si el usuario busca un restaurante, se sigue el mismo patrón de creación de actividades.
3. Y de nuevo, cuando el usuario ve un resultado. En este último caso, el usuario está viendo una ubicación y en iOS 10, el sistema tiene más conocimiento de ciertos conceptos (como las interacciones de la comunicación o la ubicación).

Eche un vistazo más de cerca a la última pantalla:

[![](proactive-suggestions-images/activity03.png "La carga NSUserActivity")](proactive-suggestions-images/activity03.png#lightbox)

Aquí la aplicación está creando un `NSUserActivity` y se ha rellenado con información para volver a crear el estado más adelante. La aplicación también incluye algunos metadatos, como el nombre y la dirección de la ubicación. Con esta actividad creada, la aplicación permite a iOS saber que representa el estado actual del usuario.

A continuación, la aplicación decide si la actividad se anunciará por encima del aire para su entrega, se guardará como un valor temporal para las sugerencias de ubicación o se agregará al índice de Spotlight en el dispositivo para mostrar en los resultados de la búsqueda.

Para obtener más información sobre la entrega y la búsqueda de Spotlight, consulte la [Introducción a](~/ios/platform/handoff.md) las guías de entrega e [iOS 9 nuevas API de búsqueda](~/ios/platform/search/index.md) .

### <a name="creating-an-activity"></a>Crear una actividad

Antes de crear una actividad, será necesario crear un identificador de tipo de actividad para identificarla. El identificador de tipo de actividad es una cadena corta agregada `NSUserActivityTypes` a la matriz del `Info.plist` archivo de la aplicación que se usa para identificar de forma única un tipo de actividad de usuario determinado. Habrá una entrada en la matriz para cada actividad que la aplicación admita y exponga a la búsqueda de aplicaciones. Consulte nuestra [referencia sobre la creación de identificadores de tipo de actividad](~/ios/platform/search/nsuseractivity.md) para obtener más detalles.

Vea un ejemplo de una actividad:

```csharp
// Create App Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Enable capabilities
activity.EligibleForSearch = true;
activity.EligibleForHandoff = true;
activity.EligibleForPublicIndexing = true;

// Inform system of Activity
activity.BecomeCurrent();
```

Se crea una nueva actividad mediante un identificador de tipo de actividad. A continuación, se crean algunos metadatos que definen la actividad, por lo que este estado se puede restaurar en una fecha posterior. A continuación, la actividad recibe un título significativo y se adjunta a la información del usuario. Por último, algunas capacidades están habilitadas y la actividad se envía al sistema.

El código anterior se podría mejorar aún más para incluir metadatos que proporcionen contexto a la actividad realizando los siguientes cambios:

```csharp
...

// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
attributes.ThumbnailUrl = myThumbnailURL;
attributes.Keywords = new string [] { "software", "mobile", "language" }; 
activity.ContentAttributeSet = attributes;

// Inform system of Activity
activity.BecomeCurrent();
```

Si el desarrollador tiene un sitio web que es capaz de mostrar la misma información que la aplicación, la aplicación puede incluir la dirección URL y el contenido se puede mostrar en otros dispositivos que no tienen la aplicación instalada (a través de la entrega):

```csharp
// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");
```

### <a name="restoring-an-activity"></a>Restaurar una actividad

Para responder al punteo del usuario en un resultado de`NSUserActivity`búsqueda () de la aplicación, edite el archivo **AppDelegate.CS** e invalide el `ContinueUserActivity` método. Por ejemplo:

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

Asegúrese de que es el mismo identificador de tipo de`com.xamarin.platform`actividad () que la actividad creada anteriormente. La aplicación usa la información almacenada en `NSUserActivity` el para restaurar el estado a donde se quedó el usuario.

### <a name="benefits-of-creating-an-activity"></a>Ventajas de la creación de una actividad

Con la cantidad mínima de código presentada anteriormente, la aplicación ahora puede aprovechar las tres nuevas características de iOS 10:

- **Handoff**
- **Búsqueda de Spotlight**
- **Recordatorios contextuales de Siri**

En la sección siguiente se examina cómo habilitar otras dos nuevas características de iOS 10:

- **Sugerencias de ubicación**
- **Solicitudes contextuales de Siri**

### <a name="location-based-suggestions"></a>Sugerencias basadas en la ubicación 

Siga el ejemplo de la aplicación de búsqueda de restaurante anterior. Si ha implementado `NSUserActivity` y rellenado correctamente todos los metadatos y atributos, el usuario podrá hacer lo siguiente:

1. Busque un restaurante en la aplicación en la que quiera que se le guste un amigo.
2. Si el usuario cambia a la aplicación Maps, la dirección del restaurante se sugiere automáticamente como un destino.
3. Esto también funciona para aplicaciones de terceros (que admiten `NSUserActivity`), por lo que el usuario puede cambiar a una aplicación de uso compartido de la ubicación y la dirección del restaurante se sugiere automáticamente como un destino.
4. También proporciona contexto a Siri, por lo que el usuario puede invocar Siri dentro de la aplicación de restaurante y preguntar *"Get directions..."* y Siri proporcionará instrucciones al restaurante que el usuario está viendo.

Toda la funcionalidad anterior tiene algo en común, todos ellos indican de dónde procede originalmente la sugerencia. En el caso del ejemplo anterior, es la aplicación ficticia de revisión de restaurante.

watchos 3 se ha mejorado para habilitar esta funcionalidad para una aplicación a través de varias pequeñas modificaciones y adiciones a los marcos de trabajo existentes:

- `NSUserActivity`tiene campos adicionales para capturar información de ubicación que se ve dentro de la aplicación.
- Se han realizado varias adiciones a MapKit y CoreSpotlight para capturar la ubicación.
- La funcionalidad con reconocimiento de ubicación se ha agregado a Siri, Maps, multitarea y otras aplicaciones del sistema.

Para implementar sugerencias basadas en la ubicación, empiece con el mismo código de actividad presentado anteriormente:

```csharp
// Create App Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Enable capabilities
activity.EligibleForSearch = true;
activity.EligibleForHandoff = true;
activity.EligibleForPublicIndexing = true;

// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
attributes.ThumbnailUrl = myThumbnailURL;
attributes.Keywords = new string [] { "software", "mobile", "language" }; 
activity.ContentAttributeSet = attributes;

// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");

// Inform system of Activity
activity.BecomeCurrent();
```

Si la aplicación usa MapKit, es tan sencillo como agregar la asignación `MKMapItem` actual a la actividad:

```csharp
// Save MKMapItem location
activity.MapItem = myMapItem;
```

Si la aplicación no usa MapKit, puede adoptar la búsqueda de la aplicación y especificar los siguientes atributos nuevos para la ubicación:

```csharp
// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
...

attributes.NamedLocation = "Apple Inc.";
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

Eche un vistazo al código anterior en detalle. En primer lugar, el nombre de la ubicación es obligatorio en cada instancia:

```csharp
attributes.NamedLocation = "Apple Inc.";
```

A continuación, la descripción basada en texto en necesaria para las instancias basadas en texto (como el teclado QuickType):

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

La latitud y la longitud son opcionales, pero asegúrese de que el usuario se enruta a la ubicación exacta a la que la aplicación desea enviarlos:

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

Al establecer los números de teléfono, la aplicación puede obtener acceso a Siri para que el usuario pueda invocar Siri desde la aplicación diciendo algo como, * "llamar a este lugar":

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

Por último, la aplicación puede indicar si la instancia es adecuada para la navegación y llamadas telefónicas:

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

## <a name="activities-best-practices"></a>Prácticas recomendadas de actividades

Apple sugiere las siguientes prácticas recomendadas al trabajar con actividades:

- Se `NeedsSave` usa para las actualizaciones de carga diferida.
- Asegúrese de mantener una referencia segura a la actividad actual.
- Transfiera solo cargas pequeñas que incluyan suficiente información para restaurar el estado.
- Asegúrese de que los identificadores de tipo de actividad son únicos y descriptivos mediante el uso de la notación DNS inversa para especificarlos. 

## <a name="consuming-location-suggestions"></a>Consumir sugerencias de ubicación

En la siguiente sección se tratará la sugerencia de ubicación de consumo que proviene de otras partes del sistema (por ejemplo, la aplicación Maps) o de otras aplicaciones de terceros.

## <a name="routing-apps-and-locations-suggestions"></a>Sugerencias de ubicaciones y aplicaciones de enrutamiento

En esta sección se examinan las sugerencias de ubicación de consumo directamente desde una aplicación de enrutamiento. Para que la aplicación de enrutamiento agregue esta funcionalidad, el desarrollador aprovechará el `MKDirectionsRequest` marco existente como se indica a continuación:

- Para promover la aplicación en multitarea.
- Para registrar la aplicación como una aplicación de enrutamiento.
- Para controlar el inicio de la aplicación con `MKDirectionsRequest` un objeto MapKit.
- Ofrezca a watchos la capacidad de aprender a sugerir la aplicación en función de la interacción del usuario.

Cuando la aplicación se inicia con un objeto `MKDirectionsRequest` MapKit, debe iniciar automáticamente la asignación del usuario a la ubicación solicitada o presentar una interfaz de usuario que facilita al usuario el inicio de la obtención de direcciones. Por ejemplo:


```csharp
using System;
using Foundation;
using UIKit;
using MapKit;
using CoreLocation;

namespace MonkeyChat
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate, IUISplitViewControllerDelegate
    {
        ...

        public override bool OpenUrl (UIApplication app, NSUrl url, NSDictionary options)
        {
            if (MKDirectionsRequest.IsDirectionsRequestUrl (url)) {
                var request = new MKDirectionsRequest (url);
                var coordinate = request.Destination?.Placemark.Location?.Coordinate;
                var address = request.Destination.Placemark.AddressDictionary;
                if (coordinate.IsValid()) {
                    var geocoder = new CLGeocoder ();
                    geocoder.GeocodeAddress (address, (place, err) => {
                        // Handle the display of the address

                    });
                }
            }

            return true;
        }
    }
}
```

Eche un vistazo a este código en detalle. Prueba para ver si se trata de una solicitud de destino válida:

```csharp
if (MKDirectionsRequest.IsDirectionsRequestUrl(url)) {
```

Si es así, crea un `MKDirectionsRequest` a partir de la dirección URL:

```csharp
var request = new MKDirectionsRequest(url);
```

Novedad en watchos 3, se puede enviar a la aplicación una dirección que no tiene coordenadas geográficas, ya que esto hace que el desarrollador tenga que codificar la dirección:

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address

});

```

## <a name="summary"></a>Resumen

En este artículo se han tratado sugerencias proactivas y se ha mostrado cómo el desarrollador puede usarlas para dirigir el tráfico a una aplicación Xamarin. iOS para watchos. Se ha tratado el paso para implementar las sugerencias proactivas y las directrices de uso presentadas.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de watchOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+watchOS)
- [Guía de programación de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
