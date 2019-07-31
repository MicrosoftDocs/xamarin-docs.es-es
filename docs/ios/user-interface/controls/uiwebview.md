---
title: Vistas Web en Xamarin. iOS
description: En este documento se describen las distintas formas en que una aplicación de Xamarin. iOS puede mostrar contenido Web. Describe la seguridad de transporte de aplicaciones, UIWebView, WKWebView, SFSafariViewController, Safari y.
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 8848f2bacac4b1ddab5c74ec07246e077bb4f158
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642696"
---
# <a name="web-views-in-xamarinios"></a>Vistas Web en Xamarin. iOS

A lo largo de la vigencia de iOS Apple, se han publicado varias maneras de que los desarrolladores de aplicaciones incorporen la funcionalidad de vista Web en sus aplicaciones. La mayoría de los usuarios usan el explorador Web Safari integrado en sus dispositivos iOS y, por lo tanto, se espera que la funcionalidad de vista Web de otras aplicaciones sea coherente con esta experiencia. Esperan que los mismos gestos funcionen, que el rendimiento sea el par y que la funcionalidad sea la misma.

En este artículo, exploraremos cada una de las tres vistas web que proporciona Apple: `UIWebView`, `WKWebview`y `SFSafariViewController`, sus similitudes y diferencias y cómo se pueden usar. 

iOS 11 presentó nuevos cambios en `WKWebView` y. `SFSafariViewController` Para obtener más información, consulte la guía de la [Guía de cambios Web en iOS 11](~/ios/platform/introduction-to-ios11/web.md) .

## <a name="uiwebview"></a>UIWebView

`UIWebView`es la forma heredada de Apple de proporcionar contenido web en la aplicación. Se lanzó en iOS 2,0 y quedó en desuso a partir de 8,0.

Si tiene previsto admitir versiones de iOS anteriores a 8,0, tendrá que usar `UIWebView`. Debido al hecho de que `UIWebView` está menos optimizado para el rendimiento que las alternativas, se recomienda que Compruebe la versión de iOS del usuario. Si 8,0 o superior, el uso de cualquiera de las opciones que se explica a continuación creará una mejor experiencia de usuario.
 
Para agregar un UIWebView a la aplicación de Xamarin. iOS, use el código siguiente:
 
```
webView = new UIWebView (View.Bounds);
View.AddSubview(webView);

var url = "https://xamarin.com"; // NOTE: https secure request
webView.LoadRequest(new NSUrlRequest(new NSUrl(url)));
```

Esto produce la siguiente vista Web:

[![](uiwebview-images/webview.png "Efecto de ScalesPagesToFit")](uiwebview-images/webview.png#lightbox)

Para obtener más información sobre `UIWebView`el uso de, consulte las siguientes recetas:


- [Cargar una página web](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_a_web_page)
- [Cargar contenido local](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_local_content)
- [Cargar documentos que no son de Web](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_non-web_documents)

## <a name="wkwebview"></a>WKWebView

`WKWebView`se presentó en iOS 8, lo que permite a los desarrolladores de aplicaciones implementar una interfaz de exploración Web similar a la de Safari para móviles. Esto se debe, en parte, al hecho de que `WKWebView` usa el motor de JavaScript de Nitro, el mismo motor que usa Mobile Safari. `WKWebView`siempre se debería usar en UIWebView debido al [aumento del rendimiento](http://blog.initlabs.com/post/100113463211/wkwebview-vs-uiwebview), los gestos de uso descriptivos integrados y la facilidad de interacción entre la página web y la aplicación.
  
`WKWebView`se puede Agregar a la aplicación de una manera prácticamente idéntica a UIWebView, aunque el desarrollador tiene mucho más control sobre la funcionalidad y la interfaz de usuario. Al crear y mostrar el objeto de vista Web, se mostrará la página solicitada; sin embargo, puede controlar cómo se presenta la vista, cómo puede desplazarse el usuario y cómo el usuario sale de la vista.  

El código siguiente puede usarse para iniciar un `WKWebView` en la aplicación de Xamarin. iOS:

```csharp
    WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
    View.AddSubview(webView);

    var url = new NSUrl("https://xamarin.com");
    var request = new NSUrlRequest(url);
    webView.LoadRequest(request);
```

Esto produce la siguiente vista Web:

[![](uiwebview-images/wkwebview.png "Una vista Web de ejemplo sin ScalesPagesToFit")](uiwebview-images/wkwebview.png#lightbox)

Es importante tener en cuenta que `WKWebView` se encuentra en el espacio de nombres WebKit, por lo que tendrá que agregar esta directiva using a la parte superior de la clase.

`WKWebView`también se puede usar en aplicaciones de Xamarin. Mac y, por lo tanto, es posible que quiera considerar la posibilidad de usarlo si va a crear una aplicación Mac/iOS multiplataforma.

La receta [controlar alertas de JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/handle_javascript_alerts) también proporciona información sobre el uso de WKWebView con JavaScript.

<a name="safariviewcontroller" />

## <a name="sfsafariviewcontroller"></a>SFSafariViewController
 
 `SFSafariViewController`es la forma más reciente de proporcionar contenido Web de la aplicación y está disponible en iOS 9 y versiones posteriores. A diferencia `UIWebView` de `WKWebView`o `SFSafariViewController` , es un controlador de vista, por lo que no se puede usar con otras vistas. Debe presentar `SFSafariViewController` como un nuevo controlador de vista, de la misma manera que presentaría cualquier controlador de vista.
 
 `SFSafariViewController`es esencialmente un ' mini Safari ' que se puede incrustar en la aplicación. Al igual que WKWebView usa el mismo motor de JavaScript de nivel de acceso, pero también proporciona una gama de características adicionales de Safari, como Autorrellenar, lector, y la capacidad de compartir cookies y datos con Safari móviles. La interacción entre el usuario y `SFSafariViewController` no es accesible para la aplicación. La aplicación no tendrá acceso a ninguna de las características de Safari predeterminadas.
 
También implementa de forma predeterminada un botón **listo** , lo que permite al usuario volver fácilmente a la aplicación, así como botones de navegación hacia delante y hacia atrás, lo que permite al usuario navegar por una pila de páginas Web. Además, también proporciona al usuario una barra de direcciones, lo que les da la tranquilidad de que están en la página web esperada. La barra de direcciones no permite que el usuario cambie la dirección URL. 

Estas implementaciones no se pueden cambiar, `SFSafariViewController` por lo que es ideal usar como el explorador predeterminado si la aplicación desea presentar una página web sin ninguna personalización.

El código siguiente puede usarse para iniciar un `SFSafariViewController` en la aplicación de Xamarin. iOS:

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

Esto produce la siguiente vista Web:

[![](uiwebview-images/sfsafariviewcontroller.png "Vista Web de ejemplo con SFSafariViewController")](uiwebview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

También es posible abrir la aplicación de Safari móviles desde dentro de la aplicación mediante el código siguiente:

```csharp
var url = new NSUrl("https://xamarin.com");

UIApplication.SharedApplication.OpenUrl(url);

```

Esto produce la siguiente vista Web:

[![](uiwebview-images/safari.png "Página Web presentada en Safari")](uiwebview-images/safari.png#lightbox)

La navegación por los usuarios fuera de la aplicación a Safari normalmente siempre se debe evitar. La mayoría de los usuarios no esperan la navegación fuera de la aplicación, por lo que si sale de la aplicación, es posible que los usuarios nunca la devuelvan.

las mejoras de iOS 9 permiten al usuario volver fácilmente a la aplicación a través de un botón atrás que se proporciona en la esquina superior izquierda de la página de Safari.

## <a name="app-transport-security"></a>Seguridad de transporte de aplicación

Apple ha introducido la seguridad de transporte de aplicaciones o *ATS* en iOS 9 para asegurarse de que todas las comunicaciones de Internet se ajusten a los procedimientos recomendados de conexión segura.

Para obtener más información sobre ATS, incluido cómo implementarlo en la aplicación, consulte la guía de [seguridad de transporte de aplicaciones](~/ios/app-fundamentals/ats.md) .

## <a name="related-links"></a>Vínculos relacionados

- [Webviews (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/webview)
