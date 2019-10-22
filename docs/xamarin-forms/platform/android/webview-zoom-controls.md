---
title: Zoom de WebView en Android
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir el específico de la plataforma Android que habilita el zoom en un WebView.
ms.prod: xamarin
ms.assetid: DC1A3762-6A42-4298-929C-445F416C3E60
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: 2142882add91d613263d11fa4c1e6d7ad142c7c7
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "68655998"
---
# <a name="webview-zoom-on-android"></a>Zoom de WebView en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este específico de la plataforma Android permite el aumento del zoom y un control de zoom en un [`WebView`](xref:Xamarin.Forms.WebView). Se consume en XAML estableciendo las propiedades `WebView.EnableZoomControls` y `WebView.DisplayZoomControls` enlazables en valores `boolean`:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView Source="https://www.xamarin.com"
             android:WebView.EnableZoomControls="true"
             android:WebView.DisplayZoomControls="true" />
</ContentPage>
```

La propiedad `WebView.EnableZoomControls` enlazable controla si está habilitada la opción de control de zoom en el [`WebView`](xref:Xamarin.Forms.WebView)y la propiedad enlazable `WebView.DisplayZoomControls` controla si los controles de zoom están superpuestos en el `WebView`.

Como alternativa, se puede usar la plataforma específica de C# para usar la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>()
    .EnableZoomControls(true)
    .DisplayZoomControls(true);
```

El método `WebView.On<Android>` especifica que este específico de la plataforma solo se ejecutará en Android. El método `WebView.EnableZoomControls`, en el espacio de nombres [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) , se utiliza para controlar si está habilitada la opción de alejar zoom en el [`WebView`](xref:Xamarin.Forms.WebView). El método `WebView.DisplayZoomControls`, en el mismo espacio de nombres, se utiliza para controlar si los controles de zoom están superpuestos en el `WebView`. Además, los métodos `WebView.ZoomControlsEnabled` y `WebView.ZoomControlsDisplayed` se pueden usar para devolver si los controles de zoom y zoom están habilitados, respectivamente.

El resultado es que se puede habilitar el aumento del zoom en un [`WebView`](xref:Xamarin.Forms.WebView), y los controles de zoom se pueden superponer en el `WebView`:

[![Captura de pantalla de vista previa ampliada en Android](webview-zoom-controls-images/webview-zoom.png "Vista previa ampliada")](webview-zoom-controls-images/webview-zoom-large.png#lightbox "Vista previa ampliada")

> [!IMPORTANT]
> Los controles de zoom deben estar habilitados y mostrados, a través de las propiedades o métodos enlazables correspondientes, para superponerse en un [`WebView`](xref:Xamarin.Forms.WebView).

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
