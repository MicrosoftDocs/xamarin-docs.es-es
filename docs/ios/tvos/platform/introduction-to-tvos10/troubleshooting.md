---
title: Solución de problemas de aplicaciones de tvOS 10 compiladas con Xamarin
description: En este artículo se proporcionan varias sugerencias para la solución de problemas para trabajar con tvOS 10 en aplicaciones de Xamarin. Describe los problemas relacionados con App Store, la compatibilidad binaria, CFNetwork HttpProtocol, CloudKit, Core Image, NSUserActivity y UIKit.
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: a6588dee675aee3e2580b70dfdea2920c6235775
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030603"
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

<a name="App-Store" />

## <a name="app-store"></a>App Store

Problemas conocidos:

- Al probar las compras desde la aplicación en el entorno de espacio aislado, el cuadro de diálogo de autenticación puede aparecer dos veces.
- Al probar compras desde la aplicación con contenido hospedado en el entorno de espacio aislado, el cuadro de diálogo de contraseña aparecerá cada vez que la aplicación se ponga en primer plano hasta que se complete la descarga de contenido.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Compatibilidad binaria

Problemas conocidos:

- Si se llama a `NSObject.ValueForKey`, se producirá una excepción `null` clave.
- Hacer referencia a una fuente por nombre al llamar a `UIFont.WithName` provocará un bloqueo.
- Tanto `NSURLSession` como `NSURLConnection` ya no son los conjuntos de cifrado RC4 durante el protocolo de enlace TLS para las direcciones URL de `http://`.
- Las aplicaciones pueden bloquearse si modifican la geometría de una supervista en los métodos `ViewWillLayoutSubviews` o `LayoutSubviews`.
- Para todas las conexiones SSL/TLS, el cifrado simétrico RC4 ahora está deshabilitado de forma predeterminada. Además, la API de transporte seguro ya no admite SSLv3 y se recomienda que la aplicación deje de usar la criptografía SHA-1 y 3DES lo antes posible.

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>Protocolo HTTP CFNetwork

La propiedad `HTTPBodyStream` de la clase `NSMutableURLRequest` debe establecerse en un flujo no abierto, ya que `NSURLConnection` y `NSURLSession` ahora exigen estrictamente este requisito.

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

Las operaciones de ejecución prolongada devolverán un _"no tiene permiso para guardar el archivo"._ Error.

<a name="CoreImage" />

## <a name="core-image"></a>Imagen principal

La API de `CIImageProcessor` admite ahora un recuento de imágenes de entrada arbitrarias. `CIImageProcessor` API que se incluyó en tvOS 10 Beta 1 se quitará.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Después de una operación de entrega, la propiedad `UserInfo` de un objeto `NSUserActivity` puede estar vacía. Llame explícitamente `BecomeCurrent` objeto ' NSUserActivity ' como solución alternativa actual.

<a name="UIKit" />

## <a name="uikit"></a>UIKit

Problemas conocidos:

- Los cambios en la apariencia de fondo de `UINavigationBar`, `UITabBar` o `UIToolBar` pueden dar lugar a un paso de diseño para resolver el nuevo aspecto. Si se intenta modificar estos aspectos visuales dentro de un `LayoutSubviews`, `UpdateConstraints`, `WillLayoutSubviews` o `DidUpdateSubviews` evento, se puede producir un bucle de diseño infinito.
- En tvOS 10, al llamar al método `RemoveGestureRecognizer` de un objeto `UIView` se cancela explícitamente cualquier reconocedor de gestos en curso.
- Los controladores de vista presentados ahora pueden afectar a la apariencia de la barra de estado.
- tvOS 10 requiere que el desarrollador llame a `base.AwakeFromNib` al subclaser `UIViewController` e invalidar el método `AwakeFromNib`.
- Las aplicaciones con subclases de `UIView` personalizadas que invalidan `LayoutSubviews` y han modificado el diseño antes de llamar a `base.LayoutSubviews` pueden desencadenar un bucle de diseño infinito en tvOS 10.
- Los recursos de imágenes que son específicos de la dirección o volteable no se voltean cuando se asignan a objetos de `UIButton`.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [Novedades de tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
