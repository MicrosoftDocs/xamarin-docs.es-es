---
title: Vistas Web en Xamarin. iOS
description: En este documento se describen las distintas formas en que una aplicación de Xamarin. iOS puede mostrar contenido Web. Describe la seguridad de transporte de aplicaciones, WKWebView, SFSafariViewController, Safari y.
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 1ae3a2af436a4ad8860ab27df550a1d74d5084a6
ms.sourcegitcommit: 0ffef1721f28717d46c8168ec96a45b6fe96b623
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/07/2020
ms.locfileid: "75718771"
---
# <a name="web-views-in-xamarinios"></a>Vistas Web en Xamarin. iOS

A lo largo de la vigencia de iOS Apple, se han publicado varias maneras de que los desarrolladores de aplicaciones incorporen la funcionalidad de vista Web en sus aplicaciones. La mayoría de los usuarios usan el explorador Web Safari integrado en sus dispositivos iOS y, por lo tanto, se espera que la funcionalidad de vista Web de otras aplicaciones sea coherente con esta experiencia. Esperan que los mismos gestos funcionen, que el rendimiento sea el par y que la funcionalidad sea la misma.

iOS 11 presentó nuevos cambios en `WKWebView` y `SFSafariViewController`. Para obtener más información, consulte la guía de la [Guía de cambios Web en iOS 11](~/ios/platform/introduction-to-ios11/web.md) .

## <a name="wkwebview"></a>WKWebView

`WKWebView` se presentó en iOS 8, lo que permite a los desarrolladores de aplicaciones implementar una interfaz de exploración Web similar a la de Safari para móviles. En parte, esto se debe al hecho de que `WKWebView` usa el motor de JavaScript de la nitro, el mismo motor que usa Mobile Safari. `WKWebView` debe usarse siempre a través de UIWebView cuando sea posible debido al [aumento del rendimiento](http://blog.initlabs.com/post/100113463211/wkwebview-vs-uiwebview), los gestos descriptivos integrados y la facilidad de interacción entre la página web y la aplicación.
  
`WKWebView` puede agregarse a la aplicación de una manera prácticamente idéntica a UIWebView, sin embargo, como desarrollador, tiene mucho más control sobre la funcionalidad y la interfaz de usuario. Al crear y mostrar el objeto de vista Web, se mostrará la página solicitada; sin embargo, puede controlar cómo se presenta la vista, cómo puede desplazarse el usuario y cómo el usuario sale de la vista.  

El código siguiente puede usarse para iniciar un `WKWebView` en la aplicación de Xamarin. iOS:

```csharp
WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
View.AddSubview(webView);

var url = new NSUrl("https://docs.microsoft.com");
var request = new NSUrlRequest(url);
webView.LoadRequest(request);
```

Es importante tener en cuenta que `WKWebView` está en el espacio de nombres `WebKit`, por lo que tendrá que agregar esta directiva using a la parte superior de la clase.

`WKWebView` también se pueden usar en aplicaciones de Xamarin. Mac y debe usarlo si va a crear una aplicación Mac/iOS multiplataforma.

La receta [controlar alertas de JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/handle_javascript_alerts) también proporciona información sobre el uso de WKWebView con JavaScript.

## <a name="sfsafariviewcontroller"></a>SFSafariViewController

 `SFSafariViewController` es la forma más reciente de proporcionar contenido Web de su aplicación y está disponible en iOS 9 y versiones posteriores. A diferencia de `UIWebView` o `WKWebView`, `SFSafariViewController` es un controlador de vista, por lo que no se puede usar con otras vistas. Debe presentar `SFSafariViewController` como un nuevo controlador de vista, de la misma manera que presentaría cualquier controlador de vista.

 `SFSafariViewController` es esencialmente un ' mini Safari ' que se puede incrustar en la aplicación. Al igual que WKWebView usa el mismo motor de JavaScript de nivel de acceso, pero también proporciona una gama de características adicionales de Safari, como Autorrellenar, lector, y la capacidad de compartir cookies y datos con Safari móviles. La aplicación no puede acceder a la interacción entre el usuario y el `SFSafariViewController`. La aplicación no tendrá acceso a ninguna de las características de Safari predeterminadas.

También implementa de forma predeterminada un botón **listo** , lo que permite al usuario volver fácilmente a la aplicación, así como botones de navegación hacia delante y hacia atrás, lo que permite al usuario navegar por una pila de páginas Web. Además, también proporciona al usuario una barra de direcciones, lo que les da la tranquilidad de que están en la página web esperada. La barra de direcciones no permite que el usuario cambie la dirección URL. 

Estas implementaciones no se pueden cambiar, por lo que `SFSafariViewController` es ideal usar como el explorador predeterminado si la aplicación desea presentar una página web sin ninguna personalización.

El código siguiente puede usarse para iniciar un `SFSafariViewController` en la aplicación de Xamarin. iOS:

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

Esto produce la siguiente vista Web:

[![una vista Web de ejemplo con SFSafariViewController](webview-images/sfsafariviewcontroller.png)](webview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

También es posible abrir la aplicación de Safari móviles desde dentro de la aplicación mediante el código siguiente:

```csharp
var url = new NSUrl("https://docs.microsoft.com");

UIApplication.SharedApplication.OpenUrl(url);
```

Esto produce la siguiente vista Web:

[![una página web presentada en Safari](webview-images/safari.png)](webview-images/safari.png#lightbox)

La navegación por los usuarios fuera de la aplicación a Safari normalmente siempre se debe evitar. La mayoría de los usuarios no esperan la navegación fuera de la aplicación, por lo que si sale de la aplicación, es posible que los usuarios nunca la devuelvan.

las mejoras de iOS 9 permiten al usuario volver fácilmente a la aplicación a través de un botón atrás que se proporciona en la esquina superior izquierda de la página de Safari.

## <a name="app-transport-security"></a>Seguridad de transporte de aplicación

Apple ha introducido la seguridad de transporte de aplicaciones o *ATS* en iOS 9 para asegurarse de que todas las comunicaciones de Internet se ajusten a los procedimientos recomendados de conexión segura.

Para obtener más información sobre ATS, incluido cómo implementarlo en la aplicación, consulte la guía de [seguridad de transporte de aplicaciones](~/ios/app-fundamentals/ats.md) .

## <a name="uiwebview-deprecated"></a>UIWebView (desusado)

> [!IMPORTANT]
> `UIWebView` está desusada. Las aplicaciones que usan este control [no se admitirán en la tienda de aplicaciones a partir del 2020 de abril y las aplicaciones existentes deberán quitarla de diciembre de 2020](https://developer.apple.com/news/?id=12232019b).
> 
> [La documentación `UIWebView` de Apple](https://developer.apple.com/documentation/uikit/uiwebview) sugiere que las aplicaciones deben usar [`WKWebView`](#wkwebview) en su lugar.

`UIWebView` es la forma heredada de Apple de proporcionar contenido web en la aplicación. Se lanzó en iOS 2,0 y quedó en desuso a partir de 8,0.

Para agregar un UIWebView a la aplicación de Xamarin. iOS, use el código siguiente:

```csharp
webView = new UIWebView (View.Bounds);
View.AddSubview(webView);

var url = "https://docs.microsoft.com"; // NOTE: https secure request
webView.LoadRequest(new NSUrlRequest(new NSUrl(url)));
```

Esto produce la siguiente vista Web:

[![el efecto de ScalesPagesToFit](webview-images/webview.png)](webview-images/webview.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [Webviews (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/webview)
