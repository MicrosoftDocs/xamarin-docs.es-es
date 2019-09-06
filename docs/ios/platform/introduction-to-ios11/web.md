---
title: Cambios de WebKit y Safari en iOS 11
description: En este documento se describen los cambios realizados en WebKit y el marco de servicios de Safari en iOS 11. Describe cómo trabajar con actualizaciones de estilo en SFSafariViewController y nuevas características de WKWebView.
ms.prod: xamarin
ms.assetid: C74B2E94-177C-43D4-8D6C-9B528773C120
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/12/2017
ms.openlocfilehash: b90673559d0b8a3728898b7d8dbc3207bb22520b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70280078"
---
# <a name="webkit-and-safari-changes-in-ios-11"></a>Cambios de WebKit y Safari en iOS 11

iOS 11 presenta una nueva versión del explorador Web Safari: Safari 11,0, que incluye los cambios en WebKit y SafariServices. En esta guía se analizan estos cambios.

## <a name="safariservices"></a>SafariServices

`SFSafariViewController`se presentó en iOS 9 como una opción para mostrar contenido web o para autenticar a los usuarios de la aplicación. Puede encontrar más información sobre sus características en la guía de [vistas web](~/ios/user-interface/controls/uiwebview.md#safariviewcontroller) .

iOS 11 ha introducido actualizaciones de estilo en el controlador de vista de Safari, lo que ofrece a los usuarios una experiencia más fluida entre una aplicación y la Web. Por ejemplo, la eliminación de la barra de direcciones ahora ofrece al controlador de vistas de Safari el funcionamiento de un explorador en la aplicación, en lugar de un pequeño explorador. También puede personalizar la combinación de colores para ajustarla a la combinación de colores de la aplicación mediante el `preferredBarTintColor` establecimiento `PreferredControlTintColor` de las propiedades y:

```csharp
sfViewController.PreferredControlTintColor = UIColor.White;
sfViewController.PreferredBarTintColor = UIColor.Purple;
```

El siguiente fragmento de código representa las barras en color púrpura y blanco, tal y como se muestra en la siguiente imagen:

![Barras SFSafariViewController representadas en color púrpura y blanco](web-images/image1.png)

El botón `Done`descartar que se presenta en el controlador de vista de Safari también `DismissButtonStyle` se puede cambiar estableciendo `Close`la propiedad `Cancel`en, o:

```csharp
sfViewController.DismissButtonStyle = SFSafariViewControllerDismissButtonStyle.Close;
```

![Descartar texto del botón cambiado](web-images/image2.png)

Este valor se puede cambiar mientras `SFSafariViewController` se presenta.


En función del contenido que se muestre en un controlador de vista de Safari, podría ser necesario asegurarse de que las barras de menús no se contraigan cuando el usuario se desplaza. Esto se habilita estableciendo la nueva `BarCollapsedEnabled` propiedad en: `false`

```csharp
var config = new SFSafariViewControllerConfiguration();
config.BarCollapsingEnabled = false;

var sfViewController = new SFSafariViewController(url, config);
```

![Contracción de la barra deshabilitada](web-images/image3.png)

Apple también ha realizado actualizaciones de la privacidad en el controlador de vista de Safari en iOS 11. Ahora, la exploración de datos como cookies y almacenamiento local solo existe en cada aplicación, en lugar de en todas las instancias del controlador de vista de Safari. Esto mantiene la actividad de exploración del usuario privada en la aplicación.

También se han `SFSafariViewController` agregado características adicionales, como la compatibilidad con arrastrar y `window.open()` colocar para las direcciones URL y la compatibilidad con en iOS 11. Puede encontrar más información sobre estas nuevas características en [la documentación de SFSafariViewController de Apple](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?changes=latest_minor).


## <a name="webkit"></a>WebKit

`WKWebView`se presentó como parte de WebKit en iOS 8 como medio para mostrar el contenido web al usuario. Es mucho más personalizable que `SFSafariViewController`, lo que le permite crear su propia navegación y la interfaz de usuario.

Apple ha incorporado tres mejoras principales `WKWebView` para iOS 11: 

- La capacidad de administrar cookies
- Filtrado de contenido
- Carga de recursos personalizada. 

La administración de cookies se realiza [`WKHttpCookieStore`](https://developer.apple.com/documentation/webkit/wkhttpcookiestore) a través de la nueva clase, que permite agregar y eliminar cookies, para obtener todas las cookies almacenadas en un WKWebView y observar el almacén de cookies en busca de cambios.

El filtrado de contenido le permite administrar el tipo de contenido que verá el usuario, lo que le permitirá asegurarse de que es seguro, compatible con la familia y, si es necesario, solo está disponible para un grupo de usuarios seleccionado. Esto se implementa a través de [`WKContentRuleList`](https://developer.apple.com/documentation/webkit/wkcontentrulelist) la nueva clase, proporcionando pares de desencadenadores y acciones en JSON. Puede encontrar más información sobre estos desencadenadores y acciones en la guía de [reglas de bloqueo de contenido](https://developer.apple.com/library/content/documentation/Extensions/Conceptual/ContentBlockingRules/Introduction/Introduction.html) de Apple.

iOS 11 permite ahora personalizar `WKWebView` con la carga de recursos personalizada para el contenido Web. Esto se implementa a través `IWKUrlSchemeHandler` de la interfaz, que le permite controlar los esquemas de dirección URL que no son nativos del kit de Web. Esta interfaz tiene un método Start y stop que debe implementarse:

```csharp
public class MyHandler : NSObject, IWKUrlSchemeHandler {

    [Export("webView:startURLSchemeTask:")]
    public void StartUrlSchemeTask(WKWebView webView, IWKUrlSchemeTask urlSchemeTask){
        
        // Implement a IWKUrlSchemeTask here
        var response = new NSUrlResponse(urlSchemeTask.Request.Url, "text/html", ContentLength, null);
        urlSchemeTask.DidReceiveResponse(response);
        urlSchemeTask.DidReceiveData(someData);
        urlSchemeTask.DidFinish();
    }

    [Export("webView:stopURLSchemeTask:")]
    public void StopUrlSchemeTask(WKWebView webView, IWKUrlSchemeTask urlSchemeTask){
        throw new NotImplementedException();
    }

}
``` 

Una vez implementado el controlador, úselo para establecer la `SetUrlSchemeHandler` propiedad `WKWebViewConfiguration`en. A continuación, cargue la dirección URL de algo que use el esquema personalizado:

```csharp
var config = new WKWebViewConfiguration();
config.SetUrlSchemeHandler(new MyHandler(), "xamarin-asset");

webView = new WKWebView (View.Frame, config);
webView.LoadRequest (new NSUrlRequest("xamarin-asset://xamarin.com"));
```

