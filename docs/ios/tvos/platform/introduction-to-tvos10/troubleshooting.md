---
title: Solución de problemas de aplicaciones de tvOS 10 compiladas con Xamarin
description: En este artículo se proporcionan varias sugerencias para la solución de problemas para trabajar con tvOS 10 en aplicaciones de Xamarin. Describe los problemas relacionados con App Store, la compatibilidad binaria, CFNetwork HttpProtocol, CloudKit, Core Image, NSUserActivity y UIKit.
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 65a76c0196b79a17f935f59902c8e6d2f9f25933
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434918"
---
# <a name="troubleshooting-tvos-10-apps-built-with-xamarin"></a>Solución de problemas de aplicaciones de tvOS 10 compiladas con Xamarin

En las secciones siguientes se enumeran algunos problemas conocidos que pueden producirse al usar tvOS 10 con Xamarin y la solución para estos problemas:

- [App Store](#App-Store)
- [Compatibilidad binaria](#Binary-Compatibility)
- [Protocolo HTTP CFNetwork](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Imagen principal](#CoreImage)
- [NSUserActivity](#NSUserActivity)
- [UIKit](#UIKit)

<a name="App-Store"></a>

## <a name="app-store"></a>App Store

Problemas conocidos:

- Al probar las compras desde la aplicación en el entorno de espacio aislado, el cuadro de diálogo de autenticación puede aparecer dos veces.
- Al probar compras desde la aplicación con contenido hospedado en el entorno de espacio aislado, el cuadro de diálogo de contraseña aparecerá cada vez que la aplicación se ponga en primer plano hasta que se complete la descarga de contenido.

<a name="Binary-Compatibility"></a>

## <a name="binary-compatibility"></a>Compatibilidad binaria

Problemas conocidos:

- Si se llama `NSObject.ValueForKey` a, se producirá una `null` excepción en una clave.
- La referencia a una fuente por nombre cuando se llama a `UIFont.WithName` producirá un bloqueo.
- `NSURLSession`Y `NSURLConnection` ya no son conjuntos de cifrado RC4 durante el protocolo de enlace TLS para `http://` direcciones URL.
- Las aplicaciones pueden bloquearse si modifican la geometría de una supervista en los `ViewWillLayoutSubviews` `LayoutSubviews` métodos o.
- Para todas las conexiones SSL/TLS, el cifrado simétrico RC4 ahora está deshabilitado de forma predeterminada. Además, la API de transporte seguro ya no admite SSLv3 y se recomienda que la aplicación deje de usar la criptografía SHA-1 y 3DES lo antes posible.

<a name="CFNetwork-HTTP-Protocol"></a>

## <a name="cfnetwork-http-protocol"></a>Protocolo HTTP CFNetwork

La `HTTPBodyStream` propiedad de la `NSMutableURLRequest` clase se debe establecer en un flujo no abierto ya que `NSURLConnection` y `NSURLSession` ahora exigen estrictamente este requisito.

<a name="CloudKit"></a>

## <a name="cloudkit"></a>CloudKit

Las operaciones de ejecución prolongada devolverán un _"no tiene permiso para guardar el archivo"._ .

<a name="CoreImage"></a>

## <a name="core-image"></a>Imagen principal

La `CIImageProcessor` API ahora es compatible con un recuento de imágenes de entrada arbitrarias. `CIImageProcessor` Se quitará la API que se incluyó en tvOS 10 Beta 1.

<a name="NSUserActivity"></a>

## <a name="nsuseractivity"></a>NSUserActivity

Después de una operación de entrega, la `UserInfo` propiedad de un `NSUserActivity` objeto puede estar vacía. Llame explícitamente al `BecomeCurrent` objeto ' NSUserActivity ' como solución alternativa actual.

<a name="UIKit"></a>

## <a name="uikit"></a>UIKit

Problemas conocidos:

- Los cambios en la apariencia de fondo de `UINavigationBar` `UITabBar` o `UIToolBar` pueden dar lugar a un paso de diseño para resolver el nuevo aspecto. Intentar modificar estos aspectos visuales dentro de un `LayoutSubviews` evento, `UpdateConstraints` `WillLayoutSubviews` o `DidUpdateSubviews` puede dar lugar a un bucle de diseño infinito.
- En tvOS 10, al llamar al `RemoveGestureRecognizer` método de un `UIView` objeto se cancela explícitamente cualquier reconocedor de gestos en curso.
- Los controladores de vista presentados ahora pueden afectar a la apariencia de la barra de estado.
- tvOS 10 requiere que el desarrollador llame a `base.AwakeFromNib` al subclase `UIViewController` e invalide el `AwakeFromNib` método.
- Las aplicaciones con `UIView` subclases personalizadas que invalidan `LayoutSubviews` y han sucio el diseño antes de llamar a `base.LayoutSubviews` pueden desencadenar un bucle de diseño infinito en tvOS 10.
- Los recursos de imágenes que se pueden voltear o especificar de dirección no se voltean cuando se asignan a los `UIButton` objetos.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [Novedades de tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)