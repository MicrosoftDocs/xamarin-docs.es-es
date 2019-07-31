---
title: Alertas de JavaScript de WebView en Windows
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el específico de la plataforma de Windows que permite a un WebView mostrar las alertas de JavaScript en un cuadro de diálogo de mensaje de UWP.
ms.prod: xamarin
ms.assetid: 95A153A1-72A0-4C0B-A452-ACE966BB12CB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 2bddf8ad314c49d8865863f6e67fc90a2bf80eb7
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656821"
---
# <a name="webview-javascript-alerts-on-windows"></a>Alertas de JavaScript de WebView en Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este específicos de la plataforma permite una [ `WebView` ](xref:Xamarin.Forms.WebView) para mostrar las alertas de JavaScript en un cuadro de diálogo de mensaje UWP. Se consume en XAML estableciendo el [ `WebView.IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabledProperty) propiedad adjunta un `boolean` valor:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <WebView ... windows:WebView.IsJavaScriptAlertEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

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

El `WebView.On<Windows>` método especifica que solo se ejecutan este específicos de la plataforma en la plataforma Universal de Windows. El [ `WebView.SetIsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.SetIsJavaScriptAlertEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.WebView},System.Boolean)) método, en el [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se usa para controlar si las alertas de JavaScript están habilitadas. Además, el `WebView.SetIsJavaScriptAlertEnabled` método puede utilizarse para activar o desactivar las alertas de JavaScript mediante una llamada a la [ `IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabled*) método para devolver si están habilitadas:

```csharp
_webView.On<Windows>().SetIsJavaScriptAlertEnabled(!_webView.On<Windows>().IsJavaScriptAlertEnabled());
```

El resultado es que se pueden mostrar las alertas de JavaScript en un cuadro de diálogo de mensaje UWP:

![Alerta de JavaScript en WebView específicos de la plataforma](webview-javascript-alert-images/webview-javascript-alert.png "alerta específica de la plataforma de JavaScript en WebView")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
