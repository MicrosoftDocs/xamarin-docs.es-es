---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7269b0617be7199c365f350fc26ecd42256e28f3
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128458"
---
# <a name="webview-mixed-content-on-android"></a>Contenido mixto de WebView en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este control específico de la plataforma Android determina si [`WebView`](xref:Xamarin.Forms.WebView) puede mostrar contenido mixto en aplicaciones destinadas a la API 21 o posterior. El contenido mixto es contenido que se carga inicialmente a través de una conexión HTTPS, pero que carga recursos (como imágenes, audio, vídeo, hojas de estilos y scripts) a través de una conexión HTTP. Se consume en XAML estableciendo la [`WebView.MixedContentMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty) propiedad adjunta en un valor de la [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) enumeración:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

El `WebView.On<Android>` método especifica que este específico de la plataforma solo se ejecutará en Android. [ `WebView.SetMixedContentMode` ] (XREF: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. Webview. SetMixedContentMode ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . WebView}, Xamarin.Forms . PlatformConfiguration. AndroidSpecific. MixedContentHandling)), en el [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se utiliza para controlar si se puede mostrar contenido mixto, con la [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) enumeración que proporciona tres valores posibles:

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow): indica que [`WebView`](xref:Xamarin.Forms.WebView) permitirá a un origen https cargar contenido de un origen http.
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow): indica que [`WebView`](xref:Xamarin.Forms.WebView) no permitirá que un origen https cargue contenido de un origen http.
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode): indica que el [`WebView`](xref:Xamarin.Forms.WebView) intentará ser compatible con el enfoque del explorador Web del dispositivo más reciente. Es posible que se pueda cargar algún contenido HTTP mediante un origen HTTPS y que se bloqueen otros tipos de contenido. Los tipos de contenido bloqueados o permitidos pueden cambiar con cada versión del sistema operativo.

El resultado es que un [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) valor especificado se aplica a [`WebView`](xref:Xamarin.Forms.WebView) , que controla si se puede mostrar contenido mixto:

[![Características específicas de la plataforma de control de contenido mixto de WebView](webview-mixed-content-images/webview-mixedcontent.png "Características específicas de la plataforma de control de contenido mixto de WebView")](webview-mixed-content-images/webview-mixedcontent-large.png#lightbox "Características específicas de la plataforma de control de contenido mixto de WebView")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
