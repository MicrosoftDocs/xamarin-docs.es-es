---
title: "WebView JavaScript Alerts on Windows" Descripción: "las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir el específico de la plataforma de Windows que permite a un WebView mostrar las alertas de JavaScript en un cuadro de diálogo de mensaje de UWP.
MS. Prod: Xamarin ms. AssetID: 95A153A1-72A0-4C0B-A452-ACE966BB12CB ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/24/2018 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="webview-javascript-alerts-on-windows"></a>Alertas de JavaScript de WebView en Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

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

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
