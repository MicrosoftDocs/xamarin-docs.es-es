---
title: Vistas web en Xamarin.iOS
description: Este documento describe las diversas maneras en que una aplicación Xamarin.iOS puede mostrar contenido web. Describe WKWebView, SFSafariViewController, Safari y seguridad de transporte de aplicaciones.
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: cc6c87452bf5312d66e33b7c49d3dc216eceb7d6
ms.sourcegitcommit: b93754b220fca3d6e3d131341e3cfbe233d10f84
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2020
ms.locfileid: "80628299"
---
# <a name="web-views-in-xamarinios"></a>Vistas web en Xamarin.iOS

A lo largo de la vida útil de iOS Apple ha lanzado una serie de maneras para que los desarrolladores de aplicaciones incorporen la funcionalidad de vista web en sus aplicaciones. La mayoría de los usuarios utilizan el navegador web Safari integrado en su dispositivo iOS y, por lo tanto, esperan que la funcionalidad de vista web de otras aplicaciones sea coherente con esta experiencia. Esperan que los mismos gestos funcionen, que el rendimiento esté a la par y que la funcionalidad sea la misma.

iOS 11 introdujo nuevos `WKWebView` `SFSafariViewController`cambios tanto en ambos como en . Para obtener más información al respecto, consulte la [guía Cambios web en iOS 11.](~/ios/platform/introduction-to-ios11/web.md)

## <a name="wkwebview"></a>WKWebView

`WKWebView`se introdujo en iOS 8 permitiendo a los desarrolladores de aplicaciones implementar una interfaz de navegación web similar a la de Safari móvil. Esto se debe, en parte, `WKWebView` al hecho de que utiliza el motor Nitro Javascript, el mismo motor utilizado por Safari móvil. `WKWebView`siempre se debe usar a través de UIWebView siempre que sea posible debido al mayor rendimiento, los gestos integrados en gestos fáciles de usar y la facilidad de interacción entre la página web y la aplicación.

`WKWebView`se puede agregar a la aplicación de una manera casi idéntica a UIWebView, sin embargo, como el desarrollador tiene mucho más control sobre la interfaz de usuario/UX y la funcionalidad. Crear y mostrar el objeto de vista web mostrará la página solicitada, sin embargo, puede controlar cómo se presenta la vista, cómo puede navegar el usuario y cómo el usuario sale de la vista.  

El código siguiente se puede `WKWebView` usar para iniciar un en la aplicación Xamarin.iOS:

```csharp
WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
View.AddSubview(webView);

var url = new NSUrl("https://docs.microsoft.com");
var request = new NSUrlRequest(url);
webView.LoadRequest(request);
```

Es importante tener `WKWebView` en cuenta `WebKit` que está en el espacio de nombres, por lo que tendrá que agregar esta directiva using a la parte superior de la clase.

`WKWebView`También se puede usar en aplicaciones de Xamarin.Mac y debe usarlo si va a crear una aplicación mac/iOS multiplataforma.

La receta [Controlar alertas](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/handle_javascript_alerts) de JavaScript también proporciona información sobre el uso de WKWebView con Javascript.

## <a name="sfsafariviewcontroller"></a>SFSafariViewController

 `SFSafariViewController`es la última forma de proporcionar contenido web desde la aplicación y está disponible en iOS 9 y versiones posteriores. A `UIWebView` `WKWebView`diferencia `SFSafariViewController` de o , es un controlador de vista y, por lo tanto, no se puede utilizar con otras vistas. Debe presentar `SFSafariViewController` como un nuevo controlador de vista, de la misma manera que presentaría cualquier controlador de vista.

 `SFSafariViewController`es esencialmente un 'mini safari' que se puede incrustar en su aplicación. Al igual que WKWebView utiliza el mismo motor Javascript Nitro, pero también proporciona una gama de características adicionales de Safari como Autocompletar, Lector, y la capacidad de compartir cookies y datos con Safari móvil. La interacción entre `SFSafariViewController` el usuario y el no es accesible para la aplicación. La aplicación no tendrá acceso a ninguna de las características predeterminadas de Safari.

También, de forma predeterminada, implementa un botón **Hecho,** que permite al usuario volver fácilmente a la aplicación y botones de navegación hacia delante y hacia atrás, lo que permite al usuario navegar a través de una pila de páginas web. Además, también proporciona al usuario una barra de direcciones dándoles la tranquilidad de que están en la página web esperada. La barra de direcciones no permite al usuario cambiar la url.

Estas implementaciones no se `SFSafariViewController` pueden cambiar, por lo que es ideal para usar como explorador predeterminado si la aplicación desea presentar una página web sin ninguna personalización.

El código siguiente se puede `SFSafariViewController` usar para iniciar un en la aplicación Xamarin.iOS:

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

Esto produce la siguiente vista web:

[![Una vista web de ejemplo con SFSafariViewController](webview-images/sfsafariviewcontroller.png)](webview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

También es posible abrir la aplicación safari móvil desde dentro de la aplicación, utilizando el siguiente código:

```csharp
var url = new NSUrl("https://docs.microsoft.com");

UIApplication.SharedApplication.OpenUrl(url);
```

Esto produce la siguiente vista web:

[![Una página web presentada en Safari](webview-images/safari.png)](webview-images/safari.png#lightbox)

Por lo general, siempre se debe evitar la navegación de los usuarios que se alejan de la aplicación a Safari. La mayoría de los usuarios no esperarán la navegación fuera de la aplicación, por lo que si se aleja de la aplicación, es posible que los usuarios nunca la devuelvan, lo que esencialmente elimina la interacción.

Las mejoras de iOS 9 permiten al usuario volver fácilmente a la aplicación a través de un botón Atrás que se proporciona en la esquina superior izquierda de la página de Safari.

## <a name="app-transport-security"></a>Seguridad de transporte de aplicación

La seguridad de transporte de aplicaciones o *ATS* fue introducida por Apple en iOS 9 para garantizar que todas las comunicaciones por Internet se ajustan a las prácticas recomendadas de conexión segura.

Para obtener más información sobre ATS, incluido cómo implementarlo en la aplicación, consulte la guía Seguridad de [transporte](~/ios/app-fundamentals/ats.md) de aplicaciones.

## <a name="uiwebview-deprecation"></a>Desuso DE UIWebView

`UIWebView`es la forma heredada de Apple de proporcionar contenido web en tu aplicación. Fue lanzado en iOS 2.0, y ha quedado obsoleto a partir de 8.0.

> [!IMPORTANT]
> `UIWebView` está desusada. Las nuevas aplicaciones que utilicen este control no se aceptarán en la App Store a [partir de abril de 2020, y las actualizaciones de aplicaciones que utilicen este control no se aceptarán antes de diciembre de 2020.](https://developer.apple.com/news/?id=12232019b)
>
> [La documentación `UIWebView` de Apple](https://developer.apple.com/documentation/uikit/uiwebview) sugiere [`WKWebView`](#wkwebview) que las aplicaciones deben usar en su lugar.
>
> Si busca recursos con respecto a la advertencia de desuso de `UIWebView` (ITMS-90809) mientras usa Xamarin.Forms, consulte la documentación de [Xamarin.Forms WebView](~/xamarin-forms/user-interface/webview.md#uiwebview-deprecation-and-app-store-rejection-itms-90809).

Los desarrolladores que enviaron aplicaciones de iOS en los últimos seis `UIWebView` meses (más o menos) podrían haber recibido una advertencia de la App Store, sobre estar en desuso.

Los desusos de las API son comunes. Xamarin.iOS usa atributos personalizados para señalar esas API (y sugerir reemplazos cuando estén disponibles) a los desarrolladores. Lo que es diferente esta vez, y mucho menos común, es que el desuso **será aplicado** por la App Store de Apple en el momento del envío.

Desafortunadamente, `UIWebView` la `Xamarin.iOS.dll` eliminación del tipo de es un [cambio de interrupción binaria](https://docs.microsoft.com/dotnet/core/compatibility/categories#binary-compatibility). Este cambio interrumpirá las bibliotecas de terceros existentes, incluidas algunas que podrían no ser compatibles o incluso volver a compilarse (por ejemplo, origen cerrado). Esto solo creará problemas adicionales para los desarrolladores. Por lo tanto, todavía no estamos eliminando el *tipo*.

A partir de [Xamarin.iOS 13.16](https://docs.microsoft.com/xamarin/ios/release-notes/13/13.16) nueva detección `UIWebView`y herramientas están disponibles para ayudarle a migrar desde .

### <a name="detection"></a>Detección

Si no has enviado recientemente una aplicación de iOS a la App Store de Apple, es posible que te preguntes si esta situación se aplica a tu(s) aplicación(s).

Para averiguarlo, puede `--warn-on-type-ref=UIKit.UIWebView` agregar a los **argumentos mtouch adicionales** de su proyecto. Esto advertirá de **cualquier** referencia `UIWebView` a la obsoleta dentro de la aplicación (y todas sus dependencias). Se utilizan diferentes advertencias para notificar tipos **antes** y **después** de que se haya ejecutado el vinculador administrado.

Las advertencias, como otras, se `-warnaserror:`pueden convertir en errores utilizando . Esto puede ser útil si desea asegurarse de que no `UIWebView` se agrega una nueva dependencia después de las verificaciones. Por ejemplo:

* `-warnaserror:1502`notificará errores si se encuentra alguna referencia en ensamblados vinculados previamente.
* `-warnaserror:1503`notificará errores si se encuentra alguna referencia en ensamblados post-vinculados.

También puede silenciar las advertencias si los resultados de la vinculación previa/posterior no son útiles. Por ejemplo:

* `-nowarn:1502`**no** notificará advertencias si se encuentra alguna referencia en ensamblados vinculados previamente.
* `-nowarn:1503`**no** notificará advertencias si se encuentra alguna referencia en ensamblados post-vinculados.

### <a name="removal"></a>Eliminación

Cada aplicación es única. La `UIWebView` eliminación de la aplicación puede requerir diferentes pasos en función de cómo y dónde se usa. Los escenarios más comunes son los siguientes:

- No hay uso `UIWebView` de dentro de la aplicación. Todo está bien. **No** debe tener advertencias al enviar a la AppStore. No se requiere nada más de ti.
- Uso directo `UIWebView` de la aplicación. Comience por eliminar `UIWebView`el uso de , por `WKWebView` ejemplo, reemplazarlo `SFSafariViewController` con los tipos más recientes (iOS 8) o (iOS 9). Una vez que esto se completa, el `UIWebView` vinculador administrado no debe ver ninguna referencia y el binario de la aplicación final no tendrá ningún rastro de él.
- Uso indirecto. `UIWebView`puede estar presente en algunas bibliotecas de terceros, ya sea administradas o nativas que usa la aplicación. Comience actualizando las dependencias externas a sus versiones más recientes, ya que es posible que esta situación ya se resuelva en una versión más reciente. Si no es así, póngase en contacto con los mantenedores de las bibliotecas y pregunte sobre sus planes de actualización.

Como alternativa, puede probar los siguientes enfoques:

1. Si usa **Xamarin.Forms**, lea esta entrada de [blog](https://devblogs.microsoft.com/xamarin/uiwebview-deprecation-xamarin-forms/).
1. Habilite el vinculador administrado (en todo el proyecto o, al menos, en la dependencia mediante `UIWebView`) para que se quite, si no se hace referencia a él. *might* Eso resolverá el problema, pero podría ser necesario un trabajo adicional para que el vinculador de código sea seguro.
1. Si no puede cambiar la configuración del vinculador administrado, consulte los casos especiales a continuación.

#### <a name="applications-cannot-use-the-linker-or-change-its-settings"></a>Las aplicaciones no pueden utilizar el vinculador (o cambiar su configuración)

Si por alguna razón **no** está utilizando el vinculador administrado (por ejemplo, **No vincular**) entonces el `UIWebView` símbolo permanecerá en la aplicación binaria que envíe a Apple y podría ser rechazado.

Una solución *contundente* `--optimize=force-rejected-types-removal` es agregar a los **argumentos mtouch adicionales**del proyecto. Esto eliminará los `UIWebView` rastros de la aplicación. Sin embargo, cualquier código que haga referencia al tipo **no** funcionará correctamente (espere excepciones o bloqueos). Este enfoque solo se debe usar si está seguro de que el código no es accesible en tiempo de ejecución (incluso si era accesible a través del análisis estático).

#### <a name="support-for-ios-7x-or-earlier"></a>Compatibilidad con iOS 7.x (o anterior)

`UIWebView`ha sido parte de iOS desde v2.0. Los reemplazos más `WKWebView` comunes son (iOS 8) y `SFSafariViewController` (iOS 9). Si la aplicación sigue siendo compatible con versiones anteriores de iOS, debe tener en cuenta las siguientes opciones:

* Convierte iOS 8 en la versión de destino mínima (una decisión de tiempo de compilación).
* Utilícelo `WKWebView` solo si la aplicación se ejecuta en iOS 8+ (una decisión en tiempo de ejecución).

#### <a name="applications-not-submitted-to-apple"></a>Solicitudes no presentadas a Apple

Si tu aplicación no se envía a Apple, debes planear alejarte de la API en desuso, ya que se puede quitar en futuras versiones de iOS. Sin embargo, puede hacer esta transición utilizando su propio horario.

## <a name="related-links"></a>Vínculos relacionados

- [WebViews (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/webview)
