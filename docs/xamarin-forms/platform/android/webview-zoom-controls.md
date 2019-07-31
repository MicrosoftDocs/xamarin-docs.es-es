---
title: Zoom de WebView en Android
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el específico de la plataforma Android que habilita el zoom en un WebView.
ms.prod: xamarin
ms.assetid: DC1A3762-6A42-4298-929C-445F416C3E60
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: 2142882add91d613263d11fa4c1e6d7ad142c7c7
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655998"
---
# <a name="webview-zoom-on-android"></a>Zoom de WebView en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este específico de la plataforma Android permite el aumento del zoom y un control de zoom en [`WebView`](xref:Xamarin.Forms.WebView)un. Se consume en XAML estableciendo las `WebView.EnableZoomControls` propiedades enlazables y `WebView.DisplayZoomControls` en `boolean` valores:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView Source="https://www.xamarin.com"
             android:WebView.EnableZoomControls="true"
             android:WebView.DisplayZoomControls="true" />
</ContentPage>
```

La `WebView.EnableZoomControls` propiedad enlazable controla si está habilitada la opción de reducción del zoom [`WebView`](xref:Xamarin.Forms.WebView)en la, `WebView.DisplayZoomControls` y la propiedad enlazable controla `WebView`si los controles de zoom están superpuestos en.

Como alternativa, se puede usar la plataforma específica de C# para usar la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>()
    .EnableZoomControls(true)
    .DisplayZoomControls(true);
```

El `WebView.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El `WebView.EnableZoomControls` método, en el [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se utiliza para controlar si está habilitada la opción de [`WebView`](xref:Xamarin.Forms.WebView)alejar zoom en. El `WebView.DisplayZoomControls` método, en el mismo espacio de nombres, se utiliza para controlar si los controles `WebView`de zoom están superpuestos en. Además, los `WebView.ZoomControlsEnabled` métodos y `WebView.ZoomControlsDisplayed` se pueden usar para devolver si los controles de zoom y zoom están habilitados, respectivamente.

El resultado es que se puede habilitar el aumento del zoom en un [`WebView`](xref:Xamarin.Forms.WebView)control y los controles de zoom se pueden superponer en el: `WebView`

[ ![Captura de pantalla de vista previa ampliada en]la(webview-zoom-controls-images/webview-zoom.png "vista") previa de Android ampliada] (webview-zoom-controls-images/webview-zoom-large.png#lightbox "Vista previa ampliada")

> [!IMPORTANT]
> Los controles de zoom deben estar habilitados y mostrados, a través de las propiedades o métodos enlazables correspondientes, para [`WebView`](xref:Xamarin.Forms.WebView)superponerse en un.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
