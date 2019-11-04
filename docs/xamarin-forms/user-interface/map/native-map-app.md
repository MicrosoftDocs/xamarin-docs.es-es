---
title: Inicio de la aplicación de asignación nativa desde Xamarin. Forms
description: La aplicación de mapas nativos en cada plataforma se puede iniciar desde una aplicación de Xamarin. Forms mediante la clase del iniciador de Xamarin. Essentials.
ms.prod: xamarin
ms.assetid: 5CF7CD67-3F20-4D80-B99E-D35A5FD1019A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/30/2019
ms.openlocfilehash: 54776d28bb75b152a6402e4d531d1baa4f724cba
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426328"
---
# <a name="launch-the-native-map-app-from-xamarinforms"></a>Inicio de la aplicación de asignación nativa desde Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

La aplicación de asignación nativa en cada plataforma se puede iniciar desde una aplicación de Xamarin. Forms mediante la clase de `Launcher` de Xamarin. Essentials. Esta clase permite a una aplicación abrir otra aplicación a través de su esquema de URI personalizado. La funcionalidad del iniciador se puede invocar con el método `OpenAsync`, pasando un argumento `string` o `Uri` que representa el esquema de la dirección URL personalizada que se va a abrir. Para obtener más información sobre Xamarin. Essentials, consulte [Xamarin. Essentials](~/essentials/index.md?context=xamarin/xamarin-forms).

> [!NOTE]
> Una alternativa al uso de la clase de `Launcher` Xamarin. Essentials es usar su `Map` clase. Para obtener más información, consulte [Xamarin. Essentials: Map](~/essentials/maps.md?context=xamarin/xamarin-forms).

La aplicación Maps en cada plataforma usa un esquema URI personalizado único. Para obtener información sobre el esquema de URI de Maps en iOS, consulte [asignación de vínculos](https://developer.apple.com/library/archive/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html) en Developer.Apple.com. Para obtener información sobre el esquema de URI de Maps en Android, consulte la [Guía para desarrolladores de Maps](https://developer.android.com/guide/components/intents-common.html#Maps) y [Google Maps intents for Android](https://developers.google.com/maps/documentation/urls/android-intents) en developers.Android.com. Para obtener información sobre el esquema de URI de Maps en el Plataforma universal de Windows (UWP), consulte [Inicio de la aplicación Windows Maps](/windows/uwp/launch-resume/launch-maps-app).

## <a name="launch-the-map-app-at-a-specific-location"></a>Iniciar la aplicación de asignación en una ubicación específica

Se puede abrir una ubicación en la aplicación de mapas nativos agregando los parámetros de consulta adecuados al esquema de URI personalizado para cada aplicación de mapa:

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    // https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html
    await Launcher.OpenAsync("http://maps.apple.com/?q=394+Pacific+Ave+San+Francisco+CA");
}
else if (Device.RuntimePlatform == Device.Android)
{
    // open the maps app directly
    await Launcher.OpenAsync("geo:0,0?q=394+Pacific+Ave+San+Francisco+CA");
}
else if (Device.RuntimePlatform == Device.UWP)
{
    await Launcher.OpenAsync("bingmaps:?where=394 Pacific Ave San Francisco CA");
}
```

Este código de ejemplo hace que la aplicación de asignación nativa se inicie en cada plataforma, con el mapa centrado en un PIN que representa la ubicación especificada:

[![Captura de pantalla de la aplicación de asignación nativa, en iOS y Android](native-map-app-images/location.png "Aplicación de asignación nativa")](native-map-app-images/location-large.png#lightbox "Aplicación de asignación nativa")

## <a name="launch-the-map-app-with-directions"></a>Inicio de la aplicación de asignación con directions

La aplicación de mapas nativos se puede iniciar mostrando instrucciones mediante la adición de los parámetros de consulta adecuados al esquema de URI personalizado para cada aplicación de mapa:

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    // https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html
    await Launcher.OpenAsync("http://maps.apple.com/?daddr=San+Francisco,+CA&saddr=cupertino");
}
else if (Device.RuntimePlatform == Device.Android)
{
    // opens the 'task chooser' so the user can pick Maps, Chrome or other mapping app
    await Launcher.OpenAsync("http://maps.google.com/?daddr=San+Francisco,+CA&saddr=Mountain+View");
}
else if (Device.RuntimePlatform == Device.UWP)
{
    await Launcher.OpenAsync("bingmaps:?rtp=adr.394 Pacific Ave San Francisco CA~adr.One Microsoft Way Redmond WA 98052");
}
```

Este código de ejemplo hace que la aplicación de asignación nativa se inicie en cada plataforma, con el mapa centrado en una ruta entre las ubicaciones especificadas:

[![Captura de pantalla de la ruta de aplicación de asignación nativa, en iOS y Android](native-map-app-images/directions.png "Direcciones de asignación de aplicaciones nativas")](native-map-app-images/directions-large.png#lightbox "Direcciones de asignación de aplicaciones nativas")

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Xamarin.Essentials](~/essentials/index.md?context=xamarin/xamarin-forms)
- [Vínculos de mapa](https://developer.apple.com/library/archive/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html)
- [Guía para desarrolladores de Maps](https://developer.android.com/guide/components/intents-common.html#Maps)
- [Google Maps para Android](https://developers.google.com/maps/documentation/)
- [Inicio de la aplicación Windows Maps](/windows/uwp/launch-resume/launch-maps-app)
