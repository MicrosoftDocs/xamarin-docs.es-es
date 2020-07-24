---
title: Cambios adicionales de los marcos de iOS 9
description: En este documento se describen los cambios de marco de trabajo adicionales introducidos en iOS 9. Describe AVFoundation, AVKit y CloudKit.
ms.prod: xamarin
ms.assetid: CFDE1FC4-9327-402B-95A0-581D4AA0E9D5
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 30501600e0b86498ae967340e2201a135b22d7ad
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939130"
---
# <a name="additional-ios-9-frameworks-changes"></a>Cambios adicionales de los marcos de iOS 9

_En este artículo se describen cambios más pequeños o mejoras en los marcos de trabajo existentes para iOS 9._

[![Logotipo de iOS 9](additional-framework-changes-images/ios9-sml.png)](additional-framework-changes-images/ios9.png#lightbox)

Además de los principales cambios en iOS, Apple ha realizado modificaciones y mejoras en varios marcos de trabajo existentes en iOS 9.

## <a name="avfoundation-framework-additions"></a>Adiciones de AVFoundation Framework

En el marco de AVFoundation, la clase [AVSpeechSynthesisVoice](xref:AVFoundation.AVSpeechSynthesisVoice) permite ahora especificar una voz por identificador además del idioma.

Por ejemplo, el código siguiente obtiene una lista de todas las voces disponibles:

```csharp
var voices = AVSpeechSynthesisVoice.GetSpeechVoices ();
```

Después, puede usar una de las voces de la lista estableciéndolo como la `Voice` propiedad de una instancia de la clase [AVSpeachUtterance](xref:AVFoundation.AVSpeechUtterance) .

La clase [AVQueuePlayer](xref:AVFoundation.AVQueuePlayer) ahora admite una mezcla de medios basados en archivos y streaming de Internet en la cola. Las versiones anteriores solo podían poner en cola los medios del mismo tipo.

Para obtener más información, consulte la [referencia de AVSpeechSynthesisVoice](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVSpeechSynthesisVoice_Ref/index.html#//apple_ref/occ/cl/AVSpeechSynthesisVoice)de Apple.

## <a name="avkit-framework-additions"></a>Adiciones de AVKit Framework

Para trabajar con la nueva característica de imagen en imagen (PIP), el marco de trabajo de AVKit incluye las `AVPictureInPictureController` clases New y [AVPlayerViewController](xref:AVKit.AVPlayerViewController) :

- **AVPictureInPictureController** : esta clase permite a una aplicación de iOS 9 responder al usuario que inicia la reproducción de un vídeo en una ventana de PIP flotante y de tamaño variable en un iPad.
- **AVPlayerViewController** : administra un `AVPlayer` controlador que se usa para presentar un vídeo en una ventana de PIP flotante y de tamaño variable en un iPad.

Para obtener más información, consulte nuestra documentación sobre la [multitarea para iPad](~/ios/platform/introduction-to-ios9/index.md#multitasking) y referencia de [AVPictureInPictureController](https://developer.apple.com/library/prerelease/ios/documentation/AVKit/Reference/AVPictureInPictureController_Class/index.html#//apple_ref/occ/cl/AVPictureInPictureController) y referencia de [AVPlayerViewController](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVPlayerViewController_Class/index.html#//apple_ref/occ/cl/AVPlayerViewController)de Apple.

## <a name="introducing-cloudkit-web-services"></a>Introducción a los servicios Web de CloudKit

El marco CloudKit agiliza el desarrollo de aplicaciones que tienen acceso a iCloud. Esto incluye la recuperación de los datos de la aplicación y los derechos de los recursos, así como la posibilidad de almacenar información de la aplicación de forma segura. Este kit proporciona a los usuarios una capa de anonimato al permitir el acceso a las aplicaciones con sus identificadores de iCloud sin compartir información personal.

El nuevo marco de trabajo de _servicios Web de CloudKit_ proporciona una biblioteca de JavaScript (CloudKit JS) que se puede incorporar en su sitio web para proporcionar acceso a los mismos datos y contenido basados en CloudKit que la aplicación de Xamarin. iOS.

> [!IMPORTANT]
> Antes de poder acceder, presentar o actualizar el contenido de una base de datos de CloudKit mediante CloudKit JS, debe haber definido previamente el esquema de esa base de datos.

Para obtener más información, consulte los siguientes documentos:

- [Introducción a CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) : Introducción al uso de CloudKit en una aplicación de Xamarin. iOS.
- [CloudKit Inicio rápido](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987) : Introducción a CloudKit.
- [Referencia de CLOUDKIT JS](https://developer.apple.com/library/prerelease/ios/documentation/CloudKitJS/Reference/CloudKitJavaScriptReference/index.html#//apple_ref/doc/uid/TP40015359) : documentación de CloudKit JS de Apple.
- [Catálogo de CloudKit: Introducción a CloudKit (cacao y JavaScript)](https://developer.apple.com/library/prerelease/ios/samplecode/CloudAtlas/Introduction/Intro.html#//apple_ref/doc/uid/TP40014599) : aplicación de ejemplo de Apple con CloudKit y CloudKit JS.

> [!IMPORTANT]
> Apple [proporciona herramientas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ayudar a los desarrolladores a tratar correctamente el Reglamento general de protección de datos (RGPD) de la Unión Europea.

## <a name="foundation-framework-additions"></a>Adiciones de Foundation Framework

Apple incluye los siguientes cambios en Foundation Framework en iOS 9:

### <a name="changes-to-nsbundle"></a>Cambios en NSBundle

Se han realizado los siguientes cambios en la clase [NSBundle](xref:Foundation.NSBundle) para iOS 9:

- `GetPreservationPriorityForTag (NSString tag)`: Obtiene la prioridad de conservación actual para los recursos con la etiqueta especificada. Los valores válidos están en el intervalo `0.0` a `1.0` , los recursos con la prioridad más baja se purgarán primero.
- `SetPreservationPriorityForTag (double priority, NSSet tags)`: Establece la prioridad de conservación actual para los recursos con las etiquetas especificadas. Los valores válidos están en el intervalo `0.0` a `1.0` , los recursos con la prioridad más baja se purgarán primero.

Para obtener más información, consulte la [referencia de NSBundle](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/index.html#//apple_ref/occ/cl/NSBundle)de Apple.

### <a name="changes-to-nsprocessinfo"></a>Cambios en NSProcessInfo

Cada proceso que se ejecuta en un dispositivo iOS tiene un único _agente de información de proceso_ (PIA). Use la clase [NSProcessInfo](xref:Foundation.NSProcessInfo) para proporcionar información sobre el Pia actual y controlar la administración térmica y la potencia de un proceso determinado.

Por ejemplo, para controlar la finalización automática de un proceso, puede usar el código siguiente:

```csharp
// Disable automatic termination
var activity = NSProcessInfo.ProcessInfo.BeginActivity(NSActivityOptions.AutomaticTerminationDisabled, "Define reason for change here...");

// Perform the required task
...

// Return to normal operation
NSProcessInfo.ProcessInfo.EndActivity(activity);
```

Para obtener más información, consulte la [referencia de NSProcessInfo](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSProcessInfo_Class/index.html#//apple_ref/occ/cl/NSProcessInfo)de Apple.

### <a name="reacting-to-low-power-mode"></a>Reacción al modo de baja energía

Use la `LowPowerModeEnabled` propiedad de la clase [NSProcessInfo](xref:Foundation.NSProcessInfo) para determinar si se ha habilitado el modo de baja energía en el dispositivo iOS en el que se ejecuta la aplicación. Por ejemplo:

```csharp
// Is the device in low power mode?
if (NSProcessInfo.ProcessInfo.LowPowerModeEnabled) {
    // Reduce activity to conserve energy...
} else {
    // Return to normal activity...
}
```

## <a name="healthkit-framework-changes"></a>Cambios de HealthKit Framework

Apple incluye los siguientes cambios en el marco de trabajo de [HealthKit](xref:HealthKit) en iOS 9:

- Compatibilidad con la eliminación y el seguimiento de la eliminación en bloque de las entradas en la base de datos HealthKit. Consulte referencia de la clase [HKDeletedObject](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKDeletedObject_ClassReference/index.html#//apple_ref/occ/cl/HKDeletedObject), [HKAnchoredObjectQuery](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKAnchoredObjectQuery_Class/index.html#//apple_ref/occ/cl/HKAnchoredObjectQuery) y [HKHealthStore](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKHealthStore_Class/index.html#//apple_ref/doc/uid/TP40014708) de Apple para obtener más información.
- Se han agregado nuevas categorías y características de seguimiento a la `HKQuantityTypeIdentifier` clase (como `UVExposure` ) y a la `HKCategoryTypeIdentifier` clase (como `OvulationTestResult` ). 

Consulte nuestra [Introducción a](~/ios/platform/healthkit.md) la documentación de HealthKit para más información sobre cómo trabajar con HealthKit en Xamarin. iOS.

## <a name="local-authentication-framework-changes"></a>Cambios en el marco de autenticación local

Apple incluye los siguientes cambios en el marco de [autenticación local](xref:LocalAuthentication) en iOS 9:

- Con los `EvaluateAccessControl` `EvaluatePolicy` métodos y de la clase [LAContext](xref:LocalAuthentication.LAContext) , ahora puede volver a usar las coincidencias de Touch ID de los intentos de desbloqueo correctos anteriores.
- La capacidad de obtener una lista de los dedos registrados actualmente.
- Compatibilidad con el seguimiento cuando se agrega o se quita un dedo de la autenticación.
- La capacidad de usar el _contexto de autenticación_ en las llamadas a llaves y la compatibilidad para evaluar las listas de control de acceso de cadena de claves.
- La capacidad de cancelar un aviso del usuario desde el código.

Para obtener más información, consulte [Touch ID and facial ID con Xamarin. iOS](~/ios/platform/touch-id-face-id.md).

### <a name="lacontext-changes"></a>LAContext cambios

Se han realizado los siguientes cambios en la clase [LAContext](xref:LocalAuthentication.LAContext) para iOS 9:

- **TouchIdAuthenticationMaximumAllowableReuseDuration** : devuelve la cantidad máxima de tiempo que se puede volver a usar una autenticación de Touch ID.
- **EvaluatedPolicyDomainState** : obtiene o establece el estado de una directiva evaluada.
- **MaxBiometryFailures** : se ha depreciado en iOS 9.
- **TouchIdAuthenticationAllowableReuseDuration** Obtiene o establece la cantidad de tiempo que se puede volver a usar una autenticación de Touch ID.
- **EvaluateAccessControl** : evalúa asincrónicamente una directiva de autenticación.
- **Invalidate** : invalida una autenticación de Touch ID dada.
- **IsCredentialSet** : devuelve `true` si las credenciales están establecidas actualmente.
- **SetCredentialType** Establece el tipo de credencial dado.

Para más información, consulte la [referencia de LAContext](https://developer.apple.com/library/prerelease/ios/documentation/LocalAuthentication/Reference/LAContext_Class/index.html#//apple_ref/occ/instm/LAContext/evaluatePolicy:localizedReason:reply:) de Apple.

## <a name="mapkit-framework-changes"></a>Cambios de MapKit Framework

Apple incluye los siguientes cambios en el marco de trabajo de [MapKit](xref:MapKit) en iOS 9:

- MapKit ahora proporciona compatibilidad para iniciar la aplicación de mapa directamente en las direcciones de tránsito y para consultar el tiempo estimado de llegada (ETA) mediante las clases [MKLaunchOptions](xref:MapKit.MKLaunchOptions) y [MKDirections](xref:MapKit.MKLaunchOptions) .
- Los resultados de la búsqueda devueltos por MapKit y la clase [CLGeocoder](xref:CoreLocation.CLGeocoder) también pueden proporcionar la zona horaria del resultado.
- Ahora puede personalizar completamente las anotaciones de mapa que presenta la aplicación iOS mediante la `DetailCalloutAccessoryView` propiedad de la clase [MKAnnotationView](xref:MapKit.MKAnnotationView) .

Para más información sobre cómo trabajar con mapas y anotaciones en Xamarin. iOS y Apple [CLGeocoder Reference](https://developer.apple.com/library/prerelease/ios/documentation/CoreLocation/Reference/CLGeocoder_class/index.html#//apple_ref/occ/cl/CLGeocoder) , consulte nuestra documentación sobre las asignaciones y las [superposiciones](~/ios/user-interface/controls/ios-maps/ios-maps-walkthrough.md) de [iOS](~/ios/user-interface/controls/ios-maps/index.md) en MapKit.

## <a name="passkit-framework-additions"></a>Adiciones de PassKit Framework

Apple incluye los siguientes cambios en el marco de trabajo de [PassKit](xref:PassKit) en iOS 9:

- Apple Pay admite ahora las tarjetas de débito y de crédito de la tienda junto con las tarjetas de detección. Consulte la sección **redes de pago** de referencia de la [clase PKPaymentRequest](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKPaymentRequest_Ref/index.html#//apple_ref/doc/uid/TP40014832) de Apple para obtener más información.
- Desde directamente dentro de una aplicación de Xamarin. iOS, ahora puede Agregar redes de pago y emisores de tarjetas a Apple Pay. Consulte referencia de la [clase PKAddPaymentPassViewController](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKAddPaymentPassViewController_Class/index.html#//apple_ref/doc/uid/TP40016116) de Apple para obtener más detalles.

Consulte nuestra [Introducción a](~/ios/platform/passkit.md) la documentación de PassKit para más información sobre cómo trabajar con PassKit en Xamarin. iOS.

## <a name="safari-services-framework-additions"></a>Adiciones del marco de servicios de Safari

Apple incluye los siguientes cambios en el marco de trabajo de [Safari Services](xref:SafariServices) en iOS 9:

- Ahora puede usar la nueva clase [SFSafariViewController](xref:SafariServices.SFSafariViewController) para mostrar el contenido web en una aplicación de Xamarin. iOS. Proporciona la capacidad de compartir datos y cookies del sitio web con la aplicación Safari e incluye varias características de Safari (como lector y Autorrellenar). [SFSafariViewController](xref:SafariServices.SFSafariViewController) incluye un botón **listo** que devolverá los usuarios a la aplicación cuando hayan terminado de ver el contenido Web.

Dado que la clase [SFSafariViewController](xref:SafariServices.SFSafariViewController) está adaptada para mostrar una única página de contenido Web, debe considerar la posibilidad de usarla para reemplazar los controles [WKWebKit](xref:WebKit.WKWebView) o [UIWebView](xref:UIKit.UIWebView) de las aplicaciones de Xamarin. iOS existentes.

### <a name="displaying-a-website"></a>Mostrar un sitio web

El código siguiente es un ejemplo de llamada a un [SFSafariViewController](xref:SafariServices.SFSafariViewController) desde el interior de otro controlador de vista:

```csharp
// Create an instance of the Safari Services View Controller
var controller = new SFSafariViewController(new NSUrl("http://www.xamarin.com"));

// Display website
PresentViewController(controller, true, null);
```

## <a name="uikit-framework-changes"></a>Cambios de UIKit Framework

Apple ha incluido muchas mejoras en varios elementos del marco [UIKit](xref:UIKit) para iOS 9. En las secciones siguientes se detallarán esos cambios.

### <a name="3d-touch-events"></a>Eventos táctiles 3D

Como novedad de iOS 9 y iPhone 6S y iPhone 6S Plus, 3D Touch agrega gestos sensibles a la presión a las aplicaciones de iOS. Como resultado, si la aplicación se ejecuta en iOS 9 (o posterior) y el dispositivo iOS es capaz de admitir la funcionalidad táctil 3D, los cambios en la presión harán que `TouchesMoved` se genere el evento.

Debido a este cambio de comportamiento, las aplicaciones de iOS deberían estar preparadas para que el `TouchesMoved` evento se invoque con más frecuencia, incluso si las coordenadas X/y no han cambiado.

Para obtener más información, consulte nuestra [Introducción a la guía táctil 3D](~/ios/platform/3d-touch.md) .

### <a name="document-open-in-place-functionality"></a>Funcionalidad de apertura en contexto de documentos

Mediante el uso `FinishedLaunching (application, launchOptions)` de los `WillFinishLaunching (Application, launchOptions)` métodos o de la clase [UIApplicationDelegate](xref:UIKit.UIApplicationDelegate) , ahora puede abrir un documento y modificarlo en su lugar (en lugar de trabajar en una copia).

Para admitir la nueva funcionalidad de apertura en contexto, agregue la `LSSupportsOpeningDocumentsInPlace` clave al archivo **info. plist** de la aplicación Xamarin. iOS con un valor de `YES` .

Para más información, consulte la [referencia de UIApplicationDelegate](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) de Apple.

### <a name="enhanced-touch-events"></a>Eventos táctiles mejorados

Apple ha proporcionado varias mejoras para tocar eventos en iOS 9. Entre ellas se incluye la capacidad de usar la predicción táctil y obtener acceso a los toques intermedios entre las actualizaciones de la pantalla.

Para más información, consulte la guía de control de eventos de Apple [para iOS](https://developer.apple.com/library/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Introduction/Introduction.html) .

### <a name="fetching-tailored-content"></a>Captura de contenido personalizado

La nueva `NSDataAsset` clase permite a una aplicación de Xamarin. iOS capturar contenido adaptado a la memoria y a las capacidades gráficas del dispositivo iOS en el que se está ejecutando actualmente.

### <a name="new-layout-anchors"></a>Nuevos delimitadores de diseño

Las nuevas `NSLayoutAnchor` `NSLayoutDimension` clases de delimitador y diseño funcionan con las nuevas propiedades de delimitador de la clase [UIView](xref:UIKit.UIView) (como `LeadingAnchor` y) para facilitar el `WidthAnchor` diseño en iOS 9.

Consulte la documentación de [Introducción a los guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) para obtener más información sobre cómo trabajar con las clases de diseño automático y tamaño en una aplicación de Xamarin. iOS y referencia de [NSLayoutAnchor](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutAnchor_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutAnchor)de Apple, referencia de [NSLayoutDimension](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutDimension_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutDimension) y referencia de [UIView](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/cl/UIView) para obtener más información.

### <a name="new-readable-content-margins"></a>Nuevos márgenes de contenido legible

La nueva `UILayoutGuide` clase se puede usar para proporcionar márgenes de contenido legibles y definir las regiones de dibujo para el contenido dentro de una vista. Consulte la [referencia de UILayoutGuide](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UILayoutGuide_Class_Reference/index.html#//apple_ref/occ/cl/UILayoutGuide) de Apple para obtener más información.

### <a name="text-input-in-notifications-modifications"></a>Entrada de texto en las modificaciones de notificaciones

La clase [UIUserNotificationAction](xref:UIKit.UIUserNotificationAction) tiene una nueva `Behavior` propiedad que se puede usar para admitir la entrada de texto de las notificaciones.

### <a name="uiapplicationdelegate-changes"></a>UIApplicationDelegate cambios

Aunque no ha quedado desusado formalmente por Apple, sugieren reemplazar todas las llamadas al `FinishedLaunching (UIApplication application)` método de la clase [UIApplicationDelegate](xref:UIKit.UIApplicationDelegate) con `FinishedLaunching (UIApplication application, NSDictionary launchOptions)` los `WillFinishLaunching (UIApplication application, NSDictionary launchOptions)` métodos o.

Para más información, consulte la [referencia de UIApplicationDelegate](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) de Apple.

### <a name="uikit-dynamics-changes"></a>UIKit cambios de Dynamics

Apple incluye los siguientes cambios en UIKit Dynamics en iOS 9:

- Dynamics ahora proporciona compatibilidad para los límites de colisión no rectangulares.
- La nueva clase personalizable `UIFieldBehavior` se usa para admitir varios tipos de campo.
- Se han agregado tipos de datos adjuntos adicionales a la `UIAttachmentBehavior` clase.

Para más información, consulte la [referencia de UIAttachment](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIAttachmentBehavior_Class/index.html#//apple_ref/occ/cl/UIAttachmentBehavior) de Apple.

### <a name="uipickerview-and-uidatepicker-changes"></a>Cambios de UIPickerView y UIDatePicker

Antes de iOS 9, los controles [UIPickerView](xref:UIKit.UIPickerView) y [UIDatePicker](xref:UIKit.UIDatePicker) eran no redimensionables y cambiarían de tamaño automáticamente para rellenar el ancho de su contenedor (normalmente, el ancho del dispositivo iOS en el que se estaba ejecutando la aplicación).

En iOS 9, este cambio de tamaño automático ya no se produce y los controles se representarán con un ancho de 320 puntos en todos los dispositivos iOS, independientemente del tamaño y la orientación de la pantalla.

Para corregir esta situación, use las clases de diseño y tamaño automáticos para anclar el ancho del control a los bordes del contenedor primario (vista) y especifique el alto necesario. Consulte la documentación [Introducción a los guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) para más información sobre cómo trabajar con clases de diseño y tamaño automáticos en una aplicación de Xamarin. iOS.

### <a name="new-uitextinputassistantitem-class"></a>Nueva clase UITextInputAssistantItem

Use la nueva `UITextInputAssistantItem` clase para los grupos de botones de la barra de diseño en una _barra de accesos directos_. La barra de acceso directo es un área nueva que está disponible en el teclado en pantalla para proporcionar accesos directos de escritura.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [Introducción a iOS 9](~/ios/platform/introduction-to-ios9/index.md)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [Novedades de iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
