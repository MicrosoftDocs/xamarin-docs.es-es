---
title: Vistas Web en Xamarin. iOS
description: En este documento se describen las distintas formas en que una aplicación de Xamarin. iOS puede mostrar contenido Web. Describe la seguridad de transporte de aplicaciones, WKWebView, SFSafariViewController, Safari y.
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: bf8f6a9014192d680b0032e034e34b594ecf193c
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430542"
---
# <a name="web-views-in-xamarinios"></a>Vistas Web en Xamarin. iOS

A lo largo de la vigencia de iOS Apple, se han publicado varias maneras de que los desarrolladores de aplicaciones incorporen la funcionalidad de vista Web en sus aplicaciones. La mayoría de los usuarios usan el explorador Web Safari integrado en sus dispositivos iOS y, por lo tanto, se espera que la funcionalidad de vista Web de otras aplicaciones sea coherente con esta experiencia. Esperan que los mismos gestos funcionen, que el rendimiento sea el par y que la funcionalidad sea la misma.

iOS 11 presentó nuevos cambios en `WKWebView` y `SFSafariViewController` . Para obtener más información, consulte la guía de la [Guía de cambios Web en iOS 11](~/ios/platform/introduction-to-ios11/web.md) .

## <a name="wkwebview"></a>WKWebView

`WKWebView` se presentó en iOS 8, lo que permite a los desarrolladores de aplicaciones implementar una interfaz de exploración Web similar a la de Safari para móviles. Esto se debe, en parte, al hecho de que `WKWebView` usa el motor de JavaScript de Nitro, el mismo motor que usa Mobile Safari. `WKWebView` siempre debe usarse a través de UIWebView cuando sea posible debido al aumento del rendimiento, los gestos descriptivos integrados y la facilidad de interacción entre la página web y la aplicación.

`WKWebView` se puede Agregar a la aplicación de una manera prácticamente idéntica a UIWebView, aunque el desarrollador tiene mucho más control sobre la funcionalidad y la interfaz de usuario. Al crear y mostrar el objeto de vista Web, se mostrará la página solicitada; sin embargo, puede controlar cómo se presenta la vista, cómo puede desplazarse el usuario y cómo el usuario sale de la vista.  

El código siguiente puede usarse para iniciar un `WKWebView` en la aplicación de Xamarin. iOS:

```csharp
WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
View.AddSubview(webView);

var url = new NSUrl("https://docs.microsoft.com");
var request = new NSUrlRequest(url);
webView.LoadRequest(request);
```

Es importante tener en cuenta que `WKWebView` se encuentra en el `WebKit` espacio de nombres, por lo que tendrá que agregar esta directiva using a la parte superior de la clase.

`WKWebView` también se puede usar en aplicaciones de Xamarin. Mac y debe usarlo si va a crear una aplicación Mac/iOS multiplataforma.

La receta [controlar alertas de JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/handle_javascript_alerts) también proporciona información sobre el uso de WKWebView con JavaScript.

## <a name="sfsafariviewcontroller"></a>SFSafariViewController

 `SFSafariViewController` es la forma más reciente de proporcionar contenido Web de la aplicación y está disponible en iOS 9 y versiones posteriores. A diferencia de `UIWebView` o `WKWebView` , `SFSafariViewController` es un controlador de vista, por lo que no se puede usar con otras vistas. Debe presentar `SFSafariViewController` como un nuevo controlador de vista, de la misma manera que presentaría cualquier controlador de vista.

 `SFSafariViewController` es esencialmente un ' mini Safari ' que se puede incrustar en la aplicación. Al igual que WKWebView usa el mismo motor de JavaScript de nivel de acceso, pero también proporciona una gama de características adicionales de Safari, como Autorrellenar, lector, y la capacidad de compartir cookies y datos con Safari móviles. La interacción entre el usuario y `SFSafariViewController` no es accesible para la aplicación. La aplicación no tendrá acceso a ninguna de las características de Safari predeterminadas.

También implementa de forma predeterminada un botón **listo** , lo que permite al usuario volver fácilmente a la aplicación, así como botones de navegación hacia delante y hacia atrás, lo que permite al usuario navegar por una pila de páginas Web. Además, también proporciona al usuario una barra de direcciones, lo que les da la tranquilidad de que están en la página web esperada. La barra de direcciones no permite que el usuario cambie la dirección URL.

Estas implementaciones no se pueden cambiar, por lo que `SFSafariViewController` es ideal usar como el explorador predeterminado si la aplicación desea presentar una página web sin ninguna personalización.

El código siguiente puede usarse para iniciar un `SFSafariViewController` en la aplicación de Xamarin. iOS:

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

Esto produce la siguiente vista Web:

[![Vista Web de ejemplo con SFSafariViewController](webview-images/sfsafariviewcontroller.png)](webview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

También es posible abrir la aplicación de Safari móviles desde dentro de la aplicación mediante el código siguiente:

```csharp
var url = new NSUrl("https://docs.microsoft.com");

UIApplication.SharedApplication.OpenUrl(url);
```

Esto produce la siguiente vista Web:

[![Página Web presentada en Safari](webview-images/safari.png)](webview-images/safari.png#lightbox)

La navegación por los usuarios fuera de la aplicación a Safari normalmente siempre se debe evitar. La mayoría de los usuarios no esperan la navegación fuera de la aplicación, por lo que si sale de la aplicación, es posible que los usuarios nunca la devuelvan.

las mejoras de iOS 9 permiten al usuario volver fácilmente a la aplicación a través de un botón atrás que se proporciona en la esquina superior izquierda de la página de Safari.

## <a name="app-transport-security"></a>Seguridad de transporte de aplicación

Apple ha introducido la seguridad de transporte de aplicaciones o *ATS* en iOS 9 para asegurarse de que todas las comunicaciones de Internet se ajusten a los procedimientos recomendados de conexión segura.

Para obtener más información sobre ATS, incluido cómo implementarlo en la aplicación, consulte la guía de [seguridad de transporte de aplicaciones](~/ios/app-fundamentals/ats.md) .

## <a name="uiwebview-deprecation"></a>Desuso de UIWebView

`UIWebView` es la forma heredada de Apple de proporcionar contenido web en la aplicación. Se lanzó en iOS 2,0 y quedó en desuso a partir de 8,0.

> [!IMPORTANT]
> `UIWebView` está desusada. Las nuevas aplicaciones que usan este control [no se admitirán en la tienda de aplicaciones a partir del 2020 de abril y las actualizaciones de aplicaciones que usan este control no serán aceptadas por el 2020 de diciembre](https://developer.apple.com/news/?id=12232019b).
>
> [La `UIWebView` documentación de Apple](https://developer.apple.com/documentation/uikit/uiwebview) sugiere que las aplicaciones deben usar [`WKWebView`](#wkwebview) en su lugar.
>
> Si busca recursos con respecto a la advertencia de desuso de `UIWebView` (ITMS-90809) mientras usa Xamarin.Forms, consulte la documentación de [Xamarin.Forms WebView](~/xamarin-forms/user-interface/webview.md#uiwebview-deprecation-and-app-store-rejection-itms-90809).

Los desarrolladores que enviaron aplicaciones de iOS en los últimos seis meses (o por lo tanto) podrían haber recibido una advertencia del App Store, por `UIWebView` estar en desuso.

Los desuso de las API son habituales. Xamarin. iOS usa atributos personalizados para indicar esas API (y sugerir reemplazos cuando estén disponibles) de nuevo a los desarrolladores. Lo que es diferente en este momento y mucho menos frecuente es que el almacén de aplicaciones de Apple **aplicará** el desuso en el momento del envío.

Desafortunadamente, quitar el `UIWebView` tipo de `Xamarin.iOS.dll` es un [cambio de interrupción binaria](/dotnet/core/compatibility/categories#binary-compatibility). Este cambio interrumpirá las bibliotecas de terceros existentes, incluidas algunas que podrían no admitirse o incluso volver a compilarse de nuevo (por ejemplo, el origen cerrado). Esto solo creará problemas adicionales para los desarrolladores. Por lo tanto, *todavía*no estamos quitando el tipo.

A partir de [Xamarin. iOS 13,16](/xamarin/ios/release-notes/13/13.16) , hay disponibles nuevas herramientas y detección para ayudarle a migrar desde `UIWebView` .

### <a name="detection"></a>Detección

Si have'nt recientemente ha enviado una aplicación de iOS a la tienda de aplicaciones de Apple, es posible que se pregunte si esta situación se aplica a sus aplicaciones.

Para averiguarlo, puede agregar `--warn-on-type-ref=UIKit.UIWebView` los **argumentos Mtouch adicionales** del proyecto. Esto advertirá de **cualquier** referencia al que se haya quedado en desuso `UIWebView` dentro de la aplicación (y todas sus dependencias). Se usan advertencias diferentes para informar de los tipos **antes** y **después** de que se haya ejecutado el enlazador administrado.

Las advertencias, como otras, se pueden convertir en errores mediante `-warnaserror:` . Esto puede ser útil si desea asegurarse de que no se agrega una nueva dependencia a `UIWebView` después de las comprobaciones. Por ejemplo:

* `-warnaserror:1502` notificará errores si se encuentran referencias en ensamblados previamente vinculados.
* `-warnaserror:1503` notificará errores si se encuentra alguna referencia en ensamblados posteriores.

También puede silenciar las advertencias si los resultados de la vinculación anterior o posterior no son útiles. Por ejemplo:

* `-nowarn:1502`**no** notificará advertencias si se encuentran referencias en ensamblados previamente vinculados.
* `-nowarn:1503`**no** notificará advertencias si se encuentran referencias en ensamblados posteriores vinculados.

### <a name="removal"></a>Eliminación

Cada aplicación es única. `UIWebView`La eliminación de la aplicación puede requerir pasos diferentes en función de cómo y dónde se use. Los escenarios más comunes son los siguientes:

- No se usa dentro de `UIWebView` la aplicación. Todo está bien. **No** debe tener advertencias cuando se envía a AppStore. No es necesaria ninguna otra cosa.
- Uso directo de `UIWebView` por parte de la aplicación. Para empezar, quite el uso de `UIWebView` , por ejemplo, reemplácelo por los tipos más recientes `WKWebView` (iOS 8) o `SFSafariViewController` (iOS 9). Una vez completado, el vinculador administrado no debe ver ninguna referencia a `UIWebView` y el archivo binario de la aplicación final no tendrá ningún seguimiento.
- Uso indirecto. `UIWebView` puede estar presente en algunas bibliotecas de terceros, tanto administradas como nativas, que usa la aplicación. Empiece por actualizar las dependencias externas a sus versiones más recientes, ya que es posible que esta situación ya se haya resuelto en una versión más reciente. Si no es así, póngase en contacto con los mantenedores de las bibliotecas y pregúntese sobre sus planes de actualización.

También puede probar los siguientes enfoques:

1. Si usa **Xamarin. Forms**, lea esta entrada de [blog](https://devblogs.microsoft.com/xamarin/uiwebview-deprecation-xamarin-forms/).
1. Habilite el vinculador administrado (en todo el proyecto o, al menos, en la dependencia mediante `UIWebView` ) para que *might* se pueda quitar, si no se hace referencia a él. Esto resolverá el problema, pero podría requerir trabajo adicional para que el código sea seguro para el vinculador.
1. Si no puede cambiar la configuración del vinculador administrado, vea los casos especiales que se indican a continuación.

#### <a name="applications-cannot-use-the-linker-or-change-its-settings"></a>Las aplicaciones no pueden utilizar el enlazador (o cambiar su configuración)

Si, por alguna razón, **no** usa el vinculador administrado (por ejemplo, **no vincular**), el `UIWebView` símbolo permanecerá en la aplicación binaria que lo envía a Apple y podría rechazarse.

Una solución *forzada* es agregar `--optimize=force-rejected-types-removal` los **argumentos Mtouch adicionales**del proyecto. Esto quitará los seguimientos de `UIWebView` de la aplicación. Sin embargo, cualquier código que haga referencia al tipo **no** funcionará correctamente (se esperan excepciones o bloqueos). Este enfoque solo debe usarse si se está seguro de que no se puede tener acceso al código en tiempo de ejecución (aunque se pueda obtener acceso a él a través del análisis estático).

#### <a name="support-for-ios-7x-or-earlier"></a>Compatibilidad con iOS 7. x (o versiones anteriores)

`UIWebView` ha sido parte de iOS desde la versión 2.0. Los reemplazos más comunes son `WKWebView` (iOS 8) y `SFSafariViewController` (iOS 9). Si la aplicación sigue siendo compatible con versiones anteriores de iOS, debe tener en cuenta las siguientes opciones:

* Haga que iOS 8 sea la versión de destino mínima (una decisión de tiempo de compilación).
* Use solo `WKWebView` si la aplicación se ejecuta en iOS 8 + (una decisión en tiempo de ejecución).

#### <a name="applications-not-submitted-to-apple"></a>Aplicaciones no enviadas a Apple

Si la aplicación no se envía a Apple, debe pensar en salir de la API en desuso, ya que se puede quitar en futuras versiones de iOS. Sin embargo, puede realizar esta transición con su propio calendario.

## <a name="related-links"></a>Vínculos relacionados

- [Webviews (ejemplo)](/samples/xamarin/ios-samples/webview)