---
title: Introducción a las sugerencias proactivas en Xamarin. iOS
description: En este artículo se muestra cómo usar las sugerencias proactivas en la aplicación de Xamarin. iOS para impulsar la interacción, lo que permite que el sistema presente de forma proactiva información útil al usuario.
ms.prod: xamarin
ms.assetid: 8DDD084A-0D1E-4DF7-B686-6309DCEFF5D3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 85879026639dbf92e1c85881e57124e802803bb3
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436986"
---
# <a name="introduction-to-proactive-suggestions-in-xamarinios"></a>Introducción a las sugerencias proactivas en Xamarin. iOS

_En este artículo se muestra cómo usar las sugerencias proactivas en la aplicación de Xamarin. iOS para impulsar la interacción, lo que permite que el sistema presente de forma proactiva información útil al usuario._

Novedades de iOS 10, las sugerencias proactivas presentan a los usuarios las formas de interactuar con una aplicación de Xamarin. iOS de forma proactiva que presenten de forma proactiva información útil al usuario en los momentos adecuados.

iOS 10 presenta nuevas formas de impulsar la interacción con la aplicación, ya que permite que el sistema presente de forma proactiva información útil al usuario en los momentos adecuados. Del mismo modo que iOS 9 proporcionó la capacidad de agregar una búsqueda profunda a la aplicación mediante las sugerencias de Spotlight, de entrega y Siri (consulte [nuevas API de búsqueda](~/ios/platform/search/index.md)), con iOS 10 una aplicación puede exponer la funcionalidad que el sistema puede presentar al usuario desde las siguientes ubicaciones:

- El conmutador de aplicación
- La pantalla de bloqueo
- CarPlay
- Maps
- Interacciones de Siri
- Sugerencias de QuickType

La aplicación expone esta funcionalidad al sistema mediante una colección de tecnologías como, por ejemplo `NSUserActivity` , marcado Web, Spotlight principal, MapKit, Media Player y UIKit. Además, al proporcionar compatibilidad proactiva de sugerencias para la aplicación, la integración de Siri es más profunda de forma gratuita.

## <a name="location-based-suggestions"></a>Sugerencias basadas en la ubicación

Como novedad de iOS 10, la `NSUserActivity` clase incluye una `MapItem` propiedad que permite al desarrollador proporcionar información de ubicación que se puede usar en otros contextos. Por ejemplo, si la aplicación muestra revisiones de restaurante, el desarrollador puede establecer la `MapItem` propiedad en la ubicación del restaurante que el usuario está viendo en la aplicación. Si el usuario cambia a la aplicación Maps, la ubicación del restaurante está disponible automáticamente.

Si la aplicación admite la búsqueda de aplicaciones, puede usar los nuevos componentes de dirección de la `CSSearchableItemAttributesSet` clase para especificar las ubicaciones que el usuario puede querer visitar. Al establecer la `MapItem` propiedad, las demás propiedades se rellenan automáticamente.

Además de establecer `Latitude` y de las `Longitude` propiedades del componente de dirección, se recomienda que la aplicación proporcione también las `NamedLocation` `PhoneNumbers` propiedades y, por lo que Siri puede iniciar una llamada a la ubicación.

## <a name="web-markup-based-suggestions"></a>Sugerencias basadas en el marcado Web

iOS 9 se ha agregado a la capacidad de incluir marcado de datos estructurados en el sitio web que enriquece el contenido que los usuarios ven en los resultados de búsqueda de Spotlight y Safari (consulte [búsqueda con marcado web](~/ios/platform/search/web-markup.md)). iOS 10 agrega la capacidad de incluir marcado basado en ubicación (como [PostalAddress](https://schema.org/PostalAddress) , tal como se define en [Schema.org](https://schema.org/)) para mejorar aún más la experiencia del usuario. Por ejemplo, si un usuario ve una página de ubicación marcada en el sitio web, el sistema puede sugerir la misma ubicación cuando abra Maps.

## <a name="text-based-suggestions"></a>Sugerencias basadas en texto

UIKit se ha expandido en iOS 10 para incluir la propiedad [TextContentType](https://developer.apple.com/reference/uikit/uitextinputtraits/1649656-textcontenttype) de la clase [UITextInputTraits](https://developer.apple.com/reference/uikit/uitextinputtraits) para especificar el significado semántico del contenido en un área de texto. Con esta información, el sistema normalmente puede seleccionar automáticamente el tipo de teclado adecuado, mejorar las sugerencias de corrección automática y integrar de forma proactiva la información de otras aplicaciones y sitios Web.

Por ejemplo, si el usuario escribe texto en un campo de texto marcado `UITextContentType.FullStreetAddress` , el sistema puede sugerir que el campo se rellene automáticamente con la ubicación que el usuario ha visualizado recientemente.

## <a name="media-based-suggestions"></a>Sugerencias basadas en medios

Si la aplicación reproduce medios mediante la API de [MPPlayableContentManager](xref:MediaPlayer.MPPlayableContentManager) , iOS 10 permite a los usuarios ver las carátulas de álbum y reproducir archivos multimedia a través de la aplicación en la pantalla de bloqueo.

## <a name="contextual-siri-reminders"></a>Recordatorios contextuales de Siri

Permite al usuario usar Siri para realizar rápidamente un recordatorio para ver el contenido que está viendo actualmente en la aplicación en otro momento. Por ejemplo, si estuviera viendo una opinión de un restaurante en la aplicación, podría invocar a Siri y decir *"avisarme sobre esto cuando llegue a casa".* Siri generaría el recordatorio con un vínculo a la revisión de la aplicación.

## <a name="contact-based-suggestions"></a>Sugerencias basadas en contacto

Permite que los contactos de la aplicación (y la información relacionada con el contacto) aparezcan en la aplicación de **contacto** en el dispositivo iOS junto con todos los contactos de usuarios almacenados en el sistema.

## <a name="ride-sharing-based-suggestions"></a>Sugerencias basadas en el uso compartido de las

Si una aplicación de uso compartido de archivos usa la API de [MKDirectionsRequest](xref:MapKit.MKDirectionsRequest) , iOS 10 la presentará como una opción en el conmutador de la aplicación en ocasiones en las que es probable que el usuario quiera una conducción. La aplicación también se debe registrar como una aplicación de uso compartido de archivos especificando `MKDirectionsModeRideShare` para la clave [MKDirectionsApplicationSupportedModes](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html) en su `Info.plist` archivo.

Si la aplicación solo admite el uso compartido de la conducción, la sugerencia del sistema comenzaría por *"obtener un acceso a..."* si se admiten otros tipos de dirección de enrutamiento (como caminar o bicicleta), el sistema usará *"obtener instrucciones a..."*

> [!IMPORTANT]
> El objeto [MKMapItem](xref:MapKit.MKMapItem) que recibe la aplicación puede no incluir información de longitud y latitud y requerirá geocodificación.

## <a name="implementing-proactive-suggestions"></a>Implementación de sugerencias proactivas

Agregar la compatibilidad con las sugerencias proactiva a una aplicación de Xamarin. iOS suele ser tan fácil como implementar algunas API o expandir algunas API que es posible que la aplicación ya esté implementando.

Las sugerencias proactivas funcionan con las aplicaciones de tres maneras principales:

- ** `NSUserActivity` y Schema.org**  -  `NSUserActivity` ayuda al sistema a comprender la información con la que el usuario está trabajando actualmente en la pantalla. Schema.org agrega capacidades similares a las páginas Web.
- **Sugerencias de ubicación** : Si la aplicación ofrece o usa información basada en la ubicación, esta extensión de API ofrece nuevas maneras de compartir esta información entre aplicaciones.
- **Sugerencias de aplicación multimedia** : el sistema puede promover la aplicación y su contenido multimedia según el contexto de la interacción del usuario con el dispositivo iOS.

Y se admite en la aplicación mediante la implementación de lo siguiente:

- **Handoff**  -  Entrega `NSUserActivity` se agregó en iOS 8 para admitir la entrega, que permite al desarrollador iniciar una actividad en un dispositivo y, a continuación, continuar en otro (consulte la [Introducción a la entrega](~/ios/platform/handoff.md)).
- **Búsqueda de Spotlight** : iOS 9 agregó la capacidad de promover el contenido de la aplicación desde dentro de los resultados de búsqueda de Spotlight mediante `NSUserActivity` (consulte [búsqueda con Spotlight básico](~/ios/platform/search/corespotlight.md)).
- Los **recordatorios de Siri contextuales** (en iOS 10) se `NSUserActivity` han ampliado para permitir que Siri realice rápidamente un recordatorio para ver el contenido que el usuario está viendo actualmente en la aplicación en otro momento.
- **Sugerencias de ubicación** : iOS 10 mejora `NSUserActivity` en las ubicaciones de captura que se ven dentro de la aplicación y las promueve en muchos lugares en todo el sistema.
- Solicitudes contextuales de **Siri**  -  `NSUserActivity` proporciona contexto a la información presentada dentro de la aplicación en Siri para que el usuario pueda obtener direcciones o realizar una llamada invocando Siri desde dentro de la aplicación.
- **Interacciones de contacto** : novedad de iOS 10, `NSUserActivity` permite promocionar las aplicaciones de comunicaciones desde una tarjeta de contacto (en la aplicación de contactos) como método de comunicación alternativo.

Todas estas características tienen una cosa en común, todas usan `NSUserActivity` en un formulario o en otra para proporcionar su funcionalidad. 

## <a name="nsuseractivity"></a>NSUserActivity

Como se indicó anteriormente, `NSUserActivity` ayuda al sistema a comprender la información con la que el usuario está trabajando actualmente en la pantalla. `NSUserActivity` es un mecanismo de almacenamiento en caché de estado ligero para capturar la actividad del usuario mientras navega por la aplicación. Por ejemplo, al examinar una aplicación de restaurante:

[![Mecanismo de almacenamiento en caché de estado de NSUserActivity Light-Weight](proactive-suggestions-images/activity02.png)](proactive-suggestions-images/activity02.png#lightbox)

Con las siguientes interacciones:

1. A medida que el usuario trabaja con la aplicación, `NSUserActivity` se crea un para volver a crear el estado de la aplicación más adelante.
2. Si el usuario busca un restaurante, se sigue el mismo patrón de creación de actividades.
3. Y de nuevo, cuando el usuario ve un resultado. En este último caso, el usuario está viendo una ubicación y en iOS 10, el sistema tiene más conocimiento de ciertos conceptos (como las interacciones de la comunicación o la ubicación).

Eche un vistazo más de cerca a la última pantalla:

[![Detalles de NSUserActivity](proactive-suggestions-images/activity03.png)](proactive-suggestions-images/activity03.png#lightbox)

Aquí la aplicación está creando un `NSUserActivity` y se ha rellenado con información para volver a crear el estado más adelante. La aplicación también incluye algunos metadatos, como el nombre y la dirección de la ubicación. Con esta actividad creada, la aplicación permite a iOS saber que representa el estado actual del usuario.

A continuación, la aplicación decide si la actividad se anunciará por encima del aire para su entrega, se guardará como un valor temporal para las sugerencias de ubicación o se agregará al índice de Spotlight en el dispositivo para mostrar en los resultados de la búsqueda.

Para obtener más información sobre la entrega y la búsqueda de Spotlight, consulte la [Introducción a](~/ios/platform/handoff.md) las guías de entrega e [iOS 9 nuevas API de búsqueda](~/ios/platform/search/index.md) .

### <a name="creating-an-activity"></a>Crear una actividad

Antes de crear una actividad, será necesario crear un identificador de tipo de actividad para identificarla. El identificador de tipo de actividad es una cadena corta agregada a la `NSUserActivityTypes` matriz del archivo de la aplicación `Info.plist` que se usa para identificar de forma única un tipo de actividad de usuario determinado. Habrá una entrada en la matriz para cada actividad que la aplicación admita y exponga a la búsqueda de aplicaciones. Consulte nuestra [referencia sobre la creación de identificadores de tipo de actividad](~/ios/platform/search/nsuseractivity.md) para obtener más detalles.

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

Para responder al punteo del usuario en un resultado de búsqueda ( `NSUserActivity` ) de la aplicación, edite el archivo **AppDelegate.CS** e invalide el `ContinueUserActivity` método. Por ejemplo:

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

El desarrollador deberá asegurarse de que es el mismo identificador de tipo de actividad ( `com.xamarin.platform` ) que la actividad creada anteriormente. La aplicación usa la información almacenada en el `NSUserActivity` para restaurar el estado a donde se quedó el usuario.

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
2. A medida que el usuario se desplaza fuera de la aplicación mediante el conmutador de aplicaciones de multitarea, el sistema mostrará automáticamente una sugerencia (en la parte inferior de la pantalla) para obtener instrucciones al restaurante mediante su aplicación de navegación favorita.
3. Si el usuario cambia a la aplicación de mensajes y comienza a escribir *"vamos a reunirse en"*, el teclado QuickType sugerirá automáticamente pegarlo en la dirección del restaurante.
4. Si el usuario cambia a la aplicación Maps, la dirección del restaurante se sugiere automáticamente como un destino.
5. Esto también funciona para aplicaciones de terceros (que admiten `NSUserActivity` ), por lo que el usuario puede cambiar a una aplicación de uso compartido de la ubicación y la dirección del restaurante se sugiere automáticamente como un destino.
6. También proporciona contexto a Siri, por lo que el usuario puede invocar Siri dentro de la aplicación de restaurante y preguntar *"Get directions..."* y Siri proporcionará instrucciones al restaurante que el usuario está viendo.

Toda la funcionalidad anterior tiene algo en común, todos ellos indican de dónde procede originalmente la sugerencia. En el caso del ejemplo anterior, es la aplicación ficticia de revisión de restaurante.

iOS 10 se ha mejorado para habilitar esta funcionalidad para una aplicación a través de varias pequeñas modificaciones y adiciones a los marcos de trabajo existentes:

- `NSUserActivity` tiene campos adicionales para capturar información de ubicación que se ve dentro de la aplicación.
- Se han realizado varias adiciones a MapKit y CoreSpotlight para capturar la ubicación.
- La funcionalidad con reconocimiento de ubicación se ha agregado a Siri, mapas, teclados, multitarea y otras aplicaciones del sistema.

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

Si la aplicación usa MapKit, es tan sencillo como agregar la asignación actual `MKMapItem` a la actividad:

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

A continuación, se requiere la descripción basada en texto de la ubicación para las instancias basadas en texto (como el teclado QuickType):

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

La latitud y la longitud son opcionales, pero asegúrese de que el usuario se enruta a la ubicación exacta a la que la aplicación desea enviarlos, por lo que debe incluirse:

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

Al establecer los números de teléfono, la aplicación puede obtener acceso a Siri para que el usuario pueda invocar Siri desde la aplicación diciendo algo como, *"llamar a este lugar"*:

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

Por último, la aplicación puede indicar si la instancia es adecuada para la navegación y llamadas telefónicas:

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

### <a name="implementing-contact-interactions"></a>Implementación de interacciones de contacto

Como novedad en iOS 10, las aplicaciones de comunicación están totalmente integradas en la aplicación Contacts desde la tarjeta contact. En el caso de las aplicaciones que han implementado interacciones de contacto, el usuario puede Agregar la información de la aplicación determinada a usuarios específicos de sus contactos. Y si, por ejemplo, alcanzan el botón acción rápida en la parte superior de una tarjeta para enviar un mensaje, la aplicación conectada se mostrará como una opción para enviar el mensaje desde.

Si se selecciona una aplicación de terceros, se puede recordar y presentar como la manera predeterminada de enviar un mensaje a la persona determinada la próxima vez que el usuario desee ponerse en contacto con ellas.

Las interacciones de contacto se implementan en la aplicación mediante `NSUserActivity` y el nuevo marco de trabajo que se incorporó en iOS 10. Para obtener más información sobre cómo trabajar con intenciones, vea nuestros [conceptos de SiriKit](~/ios/platform/sirikit/understanding-sirikit.md) e implementación de guías de [SiriKit](~/ios/platform/sirikit/implementing-sirikit.md) .

#### <a name="donating-interactions"></a>Donar interacciones

Eche un vistazo a cómo la aplicación puede donar interacciones:

[![Información general sobre la donación de interacciones](proactive-suggestions-images/activity04.png)](proactive-suggestions-images/activity04.png#lightbox)

La aplicación crea un `INInteraction` objeto que contiene una  **intención** ( `INIntent` ), los **participantes** y los **metadatos**. La **intención** representa una acción del usuario, como la realización de una llamada de vídeo o el envío de un mensaje de texto. Los **participantes** incluyen las personas que reciben la comunicación. Los **metadatos** definen información adicional, como el envío correcto del mensaje, etc.

El desarrollador nunca crea directamente una instancia de `INIntent` o `INIntentResponse` , utilizará una de las clases secundarias específicas (en función de la tarea que la aplicación está realizando en nombre del usuario) que heredan de estas clases primarias. Por ejemplo, `INSendMessageIntent` y `INSendMessageIntentResponse` para enviar un mensaje de texto. 

Una vez completada la interacción, llame al `DonateInteraction` método para informar al sistema de que la interacción está disponible para su uso.

Cuando el usuario interactúa con la aplicación desde la tarjeta de contacto, la interacción se agrupa con un `NSUserActivity` , que se usa para iniciar la aplicación:

[![La interacción se agrupa con un NSUserActivity que se usa para iniciar la aplicación.](proactive-suggestions-images/activity05.png)](proactive-suggestions-images/activity05.png#lightbox)

Eche un vistazo al ejemplo siguiente de un intento de envío de mensaje:

```csharp
using System;
using Foundation;
using UIKit;
using Intents;

namespace MonkeyNotification
{
  public class DonateInteraction
  {
    #region Constructors
    public DonateInteraction ()
    {
    }
    #endregion

    #region Public Methods
    public void SendMessageIntent (string text, INPerson from, INPerson[] to)
    {

      // Create App Activity
      var activity = new NSUserActivity ("com.xamarin.message");

      // Define details
      var info = new NSMutableDictionary ();
      info.Add (new NSString ("message"), new NSString (text));

      // Populate Activity
      activity.Title = "Sent MonkeyChat Message";
      activity.UserInfo = info;

      // Enable capabilities
      activity.EligibleForSearch = true;
      activity.EligibleForHandoff = true;
      activity.EligibleForPublicIndexing = true;

      // Inform system of Activity
      activity.BecomeCurrent ();

      // Create message Intent
      var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);

      // Create Intent Reaction
      var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);

      // Create interaction
      var interaction = new INInteraction (intent, response);

      // Donate interaction to the system
      interaction.DonateInteraction ((err) => {
        // Handle donation error
        ...
      });
    }
    #endregion
  }
}
```

En este código, se crea y rellena una instancia de `NSUserActivity` (como se muestra en la sección creación de [una actividad](#creating-an-activity) anterior). A continuación, crea una instancia de `INSendMessageIntent` (que hereda de `INIntent` ) y la rellena con los detalles del mensaje que se está enviando:

```csharp
var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);
```

`INSendMessageIntentResponse`Se crea y se pasa el `NSUserActivity` creado anteriormente:

```csharp
var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);
```

`INInteraction`Se crea a partir de la intención enviar mensaje ( `INSendMessageIntent` ) y la respuesta () que se `INSendMessageIntentResponse` acaba de crear:

```csharp
var interaction = new INInteraction (intent, response);
```

Por último, el sistema es una notificación de la interacción:

```csharp
// Donate interaction to the system
interaction.DonateInteraction ((err) => {
  // Handle donation error
  ...
});
```

Se pasa un controlador de finalización en el que la aplicación puede responder al donativo correctamente o con errores.

### <a name="activities-best-practices"></a>Prácticas recomendadas de actividades

Apple sugiere las siguientes prácticas recomendadas al trabajar con actividades:

- Se usa `NeedsSave` para las actualizaciones de carga diferida.
- Asegúrese de mantener una referencia segura a la actividad actual.
- Transfiera solo cargas pequeñas que incluyan suficiente información para restaurar el estado.
- Asegúrese de que los identificadores de tipo de actividad son únicos y descriptivos mediante el uso de la notación DNS inversa para especificarlos. 

## <a name="schemaorg"></a>Schema.org

Como se mostró anteriormente, `NSUserActivity` ayuda al sistema a comprender la información con la que el usuario está trabajando actualmente en la pantalla. Schema.org agrega capacidades similares a las páginas Web.

Schema.org puede proporcionar los mismos tipos de interacciones basadas en la ubicación al sitio Web. Apple diseñó las nuevas sugerencias de ubicación para trabajar tan bien cuando se ve en Safari como lo hacen en una aplicación nativa.

Segundo plano de Schema.org:

- Proporciona un estándar abierto de vocabulario de marcado Web.
- Funciona mediante la inclusión de metadatos estructurados en páginas Web.
- Hay más de 500 esquemas que representan varios conceptos disponibles.
- Al implementarlo en el sitio web, el desarrollador puede adquirir algunas de las ventajas de usar `NSUserActivity` en una aplicación nativa.

Los esquemas están organizados en un árbol como la estructura, donde determinados tipos, como *restaurante*, se heredan de tipos más genéricos, como la *empresa local*. Para obtener más información, consulte [Schema.org](https://schema.org).

Por ejemplo, si la página web incluye los datos siguientes:

```html
<script type="application/ld+json">
{
  "@context":"http://schema.org",
  "@type":"Restaurant",
  "telephone":"(415) 781-1111",
  "url":"https://www.yanksing.com",
  "address":{
    "@type":"PostalAddress",
    "streetAddress":"101 Spear St",
    "addressLocality":"San Francisco",
    "postalCode":"94105",
    "addressRegion":"CA"
  },
  "aggregateRating":{
    "@type":"AggregateRating",
    "ratingValue":"3.5",
    "reviewCount":"2022"
  }
}
</script>
```

Si el usuario visita esta página en Safari y después cambia a otra aplicación, la información de ubicación de la página se capturará y se ofrecerá como una sugerencia de ubicación en otras partes del sistema (como se ha visto en las actividades anteriores).

Safari extraerá todo lo que haya en una página web que se adhiere a cualquiera de las siguientes propiedades del esquema:

- **PostalAddress**
- **GeoCoordinates**
- Propiedad de teléfono.

Para obtener más información, consulte la guía de [búsqueda con marcado web](~/ios/platform/search/web-markup.md) .

## <a name="consuming-location-suggestions"></a>Consumir sugerencias de ubicación

En la siguiente sección se tratará la sugerencia de ubicación de consumo que proviene de otras partes del sistema (por ejemplo, la aplicación Maps) o de otras aplicaciones de terceros.

Hay dos formas principales en que la aplicación puede usar las sugerencias de Ubicación:

- Mediante el teclado QuickType.
- Directamente mediante el consumo de la información de ubicación en las aplicaciones de enrutamiento.

### <a name="location-suggestions-and-the-quicktype-keyboard"></a>Sugerencias de ubicación y el teclado QuickType

Si la aplicación trabaja con direcciones en formatos basados en texto, la aplicación puede aprovechar las sugerencias de ubicación a través de la interfaz de usuario de QuickType. iOS 10 expande la interfaz de usuario de QuickType con las siguientes características:

- La aplicación puede Agregar sugerencias sobre la intención semántica de los campos de texto en la interfaz de usuario.
- La aplicación puede obtener sugerencias proactivas en la aplicación.
- La aplicación puede beneficiarse de la corrección de Autocorrección mejorada.

La nueva `TextContentType` propiedad de los controles de campo de texto de iOS 10 permite al desarrollador definir el objetivo semántico del valor que el usuario va a especificar en un campo determinado. Por ejemplo:

```csharp
var textField = new UITextField();
textField.TextContentType = UITextContentType.FullStreetAddress;
```

Indicaría al sistema que la aplicación espera que el usuario escriba una dirección completa en el campo especificado. Esto permitirá que el teclado QuickType proporcione automáticamente sugerencias de ubicación en el teclado cuando el usuario escriba un valor en este campo.

A continuación se muestran algunos de los tipos más comunes disponibles para el desarrollador en la `UITextContentType` clase estática:

- `Name`
- `GivenName`
- `FamilyName`
- `Location`
- `FullStreetAddress`
- `AddressCityAndState`
- `TelephoneNumber`
- `EmailAddress`

### <a name="routing-apps-and-locations-suggestions"></a>Sugerencias de ubicaciones y aplicaciones de enrutamiento

En esta sección se examinan las sugerencias de ubicación de consumo directamente desde una aplicación de enrutamiento. Para que la aplicación de enrutamiento agregue esta funcionalidad, el desarrollador aprovechará el `MKDirectionsRequest` marco existente como se indica a continuación:

- Para promover la aplicación en multitarea.
- Para registrar la aplicación como una aplicación de enrutamiento.
- Para controlar el inicio de la aplicación con un `MKDirectionsRequest` objeto MapKit.
- Para proporcionar a iOS la capacidad de aprender a sugerir la aplicación al usuario en el momento adecuado, en función de la interacción del usuario.

Cuando la aplicación se inicia con un `MKDirectionsRequest` objeto MapKit, debe iniciar automáticamente la asignación del usuario a la ubicación solicitada o presentar una interfaz de usuario que facilita al usuario el inicio de la obtención de direcciones. Por ejemplo:

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

Si es así, crea un a `MKDirectionsRequest` partir de la dirección URL:

```csharp
var request = new MKDirectionsRequest(url);
```

Como novedad en iOS 10, se puede enviar a la aplicación una dirección que no tiene coordenadas geográficas; en ese sentido, el desarrollador debe codificar la dirección:

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
  // Handle the display of the address
  
});

```

## <a name="media-app-suggestions"></a>Sugerencias de aplicación multimedia

Si la aplicación controla cualquier tipo de medio, como una aplicación de podcast o un contenido multimedia de streaming como audio o vídeo, con iOS 10, puede aprovechar las sugerencias de medios del sistema.

En el caso de las aplicaciones que controlan los medios, iOS admite los siguientes comportamientos:

- iOS promueve las aplicaciones que es probable que el usuario use en función de su comportamiento anterior.
- Las sugerencias relacionadas con la aplicación se presentarán en Spotlight y en la vista de hoy en día.
- Si la aplicación cumple uno de los siguientes desencadenadores, se podría elevar a una sugerencia de pantalla de bloqueo:
  - Después de conectar auriculares o un dispositivo Bluetooth establece una conexión.
  - Después de obtener un automóvil.
  - Después de llegar a casa o al trabajo. 

Al incluir una llamada de API simple en iOS 10, el desarrollador puede crear una experiencia de pantalla de bloqueo más atractiva para los usuarios de la aplicación multimedia. Mediante el uso de la `MPPlayableContentManager` clase para administrar la reproducción multimedia, los controles multimedia completos (como los presentados por la aplicación de música) se presentarán en la pantalla de bloqueo de la aplicación.

```csharp
using System;
using MediaPlayer;
using UIKit;

namespace MonkeyPlayer
{
  public class PlayableContentDelegate : MPPlayableContentDelegate
  {
    #region Constructors
    public PlayableContentDelegate ()
    {
    }
    #endregion

    #region Override methods
    public override void InitiatePlaybackOfContentItem (MPPlayableContentManager contentManager, Foundation.NSIndexPath indexPath, Action<Foundation.NSError> completionHandler)
    {
      // Access the media item to play
      var item = LoadMediaItem (indexPath);

      // Populate the lock screen
      PopulateNowPlayingItem (item);

      // Prep item to be played
      var status = PreparePlayback (item);

      // Call completion handler
      completionHandler (null);
    }
    #endregion

    #region Public Methods
    public MPMediaItem LoadMediaItem (Foundation.NSIndexPath indexPath)
    {
      var item = new MPMediaItem ();

      // Load item from media store
      ...

      return item;
    }

    public void PopulateNowPlayingItem (MPMediaItem item)
    {
      // Get Info Center and album art
      var infoCenter = MPNowPlayingInfoCenter.DefaultCenter;
      var albumArt = (item.Artwork == null) ? new MPMediaItemArtwork (UIImage.FromFile ("MissingAlbumArt.png")) : item.Artwork;

      // Populate Info Center
      infoCenter.NowPlaying.Title = item.Title;
      infoCenter.NowPlaying.Artist = item.Artist;
      infoCenter.NowPlaying.AlbumTitle = item.AlbumTitle;
      infoCenter.NowPlaying.PlaybackDuration = item.PlaybackDuration;
      infoCenter.NowPlaying.Artwork = albumArt;
    }

    public bool PreparePlayback (MPMediaItem item)
    {
      // Prepare media item for playback
      ...

      // Return results
      return true;
    }
    #endregion
  }
}
```

## <a name="summary"></a>Resumen

En este artículo se han tratado sugerencias proactivas y se ha mostrado cómo el desarrollador puede usarlas para dirigir el tráfico a la aplicación Xamarin. iOS. Se ha tratado el paso para implementar las sugerencias proactivas y las directrices de uso presentadas.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)
- [Guía de programación de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)