---
title: Alertas de JavaScript de WebView en Windows
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir el específico de la plataforma de Windows que permite a un WebView mostrar las alertas de JavaScript en un cuadro de diálogo de mensaje de UWP.
ms.prod: xamarin
ms.assetid: 95A153A1-72A0-4C0B-A452-ACE966BB12CB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c0a52bc7d0f8b5c8dc07975653c819aa7f3ec2be
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93367405"
---
# <a name="webview-javascript-alerts-on-windows"></a>Alertas de JavaScript de WebView en Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este específico de la plataforma permite [`WebView`](xref:Xamarin.Forms.WebView) a un para mostrar las alertas de JavaScript en un cuadro de diálogo de mensaje de UWP. Se consume en XAML estableciendo la [`WebView.IsJavaScriptAlertEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabledProperty) propiedad adjunta en un `boolean` valor:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <WebView ... windows:WebView.IsJavaScriptAlertEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

var webView = new Xamarin.Forms.WebView
{
  Source = new HtmlWebViewSource
  {
    Html = @"<html><body><button onclick=""window.alert('Hello World from JavaScript');"">Click Me</button></body></html>"
  }
};
webView.On<Windows>().SetIsJavaScriptAlertEnabled(true);
```

El `WebView.On<Windows>` método especifica que este específico de la plataforma solo se ejecutará en el plataforma universal de Windows. [ `WebView.SetIsJavaScriptAlertEnabled` ] (XREF: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. Webview. SetIsJavaScriptAlertEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . WebView}, System. Boolean)), en el [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se utiliza para controlar si se habilitan las alertas de JavaScript. Además, `WebView.SetIsJavaScriptAlertEnabled` se puede usar el método para alternar las alertas de JavaScript llamando al [`IsJavaScriptAlertEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabled*) método para devolver si están habilitadas:

```csharp
_webView.On<Windows>().SetIsJavaScriptAlertEnabled(!_webView.On<Windows>().IsJavaScriptAlertEnabled());
```

El resultado es que las alertas de JavaScript se pueden mostrar en un cuadro de diálogo de mensaje de UWP:

![Vista previa de la alerta de JavaScript específica de la plataforma](webview-javascript-alert-images/webview-javascript-alert.png "Vista previa de la alerta de JavaScript específica de la plataforma")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)