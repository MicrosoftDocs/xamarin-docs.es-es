---
title: Contenido mixto de WebView en Android
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo usar el específico de la plataforma Android que muestra contenido mixto en una vista de WebView en aplicaciones que tienen como destino la API 21 o posterior.
ms.prod: xamarin
ms.assetid: 68F908F3-04C5-4B91-B6E5-B7E8357B4154
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: f9b4a12d3049cea37235cc04805a7411a9bdb236
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696489"
---
# <a name="webview-mixed-content-on-android"></a>Contenido mixto de WebView en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este control específico de la plataforma Android determina si un [`WebView`](xref:Xamarin.Forms.WebView) puede mostrar contenido mixto en aplicaciones destinadas a la API 21 o posterior. El contenido mixto es contenido que se carga inicialmente a través de una conexión HTTPS, pero que carga recursos (como imágenes, audio, vídeo, hojas de estilos y scripts) a través de una conexión HTTP. Se consume en XAML estableciendo el [`WebView.MixedContentMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty) propiedad adjunta en un valor de la enumeración [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) :

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

Como alternativa, se puede utilizar para C# usar la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

El método `WebView.On<Android>` especifica que este específico de la plataforma solo se ejecutará en Android. El método [`WebView.SetMixedContentMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.SetMixedContentMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.WebView},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)) , en el espacio de nombres [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) , se utiliza para controlar si se puede mostrar contenido mixto, con la enumeración [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) que proporciona tres valores posibles:

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) : indica que el [`WebView`](xref:Xamarin.Forms.WebView) permitirá a un origen https cargar contenido de un origen http.
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) : indica que el [`WebView`](xref:Xamarin.Forms.WebView) no permitirá que un origen https cargue contenido de un origen http.
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) : indica que el [`WebView`](xref:Xamarin.Forms.WebView) intentará ser compatible con el enfoque del explorador Web del dispositivo más reciente. Es posible que se pueda cargar algún contenido HTTP mediante un origen HTTPS y que se bloqueen otros tipos de contenido. Los tipos de contenido bloqueados o permitidos pueden cambiar con cada versión del sistema operativo.

El resultado es que un valor de [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) especificado se aplica a la [`WebView`](xref:Xamarin.Forms.WebView), que controla si se puede mostrar contenido mixto:

[![Características específicas de la plataforma de control de contenido mixto de WebView](webview-mixed-content-images/webview-mixedcontent.png "Características específicas de la plataforma de control de contenido mixto de WebView")](webview-mixed-content-images/webview-mixedcontent-large.png#lightbox "Características específicas de la plataforma de control de contenido mixto de WebView")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
