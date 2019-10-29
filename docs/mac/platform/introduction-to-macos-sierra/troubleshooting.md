---
title: 'Xamarin. Mac: solución de problemas de macOS Sierra'
description: En este documento se proporcionan varias sugerencias para la solución de problemas para trabajar con macOS Sierra en aplicaciones de Xamarin. Mac. Las sugerencias se relacionan con Mac App Store, Apple Pay, compatibilidad binaria, CFNetwork, CloudKit, etc.
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 09/22/2016
ms.openlocfilehash: a4e7f7169e4c7ec0ec2947e17b1434179f47488f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73017036"
---
# <a name="xamarinmac---macos-sierra-troubleshooting"></a>Xamarin. Mac: solución de problemas de macOS Sierra

_En este artículo se proporcionan varias sugerencias para la solución de problemas para trabajar con macOS Sierra en aplicaciones de Xamarin. Mac._

En las secciones siguientes se enumeran algunos problemas conocidos que pueden producirse al usar macOS Sierra con Xamarin. Mac y la solución para estos problemas:

- [App Store](#App-Store)
- [Apple Pay](#Apple-Pay)
- [Compatibilidad binaria](#Binary-Compatibility)
- [Protocolo HTTP CFNetwork](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Imagen principal](#CoreImage)
- [Notificaciones](#Notifications)
- [NSUserActivity](#NSUserActivity)
- [Safari](#Safari)

<a name="App-Store" />

## <a name="app-store"></a>App Store

Problemas conocidos:

- Al probar las compras desde la aplicación en el entorno de espacio aislado, el cuadro de diálogo de autenticación puede aparecer dos veces.
- Al probar compras desde la aplicación con contenido hospedado en el entorno de espacio aislado, el cuadro de diálogo de contraseña aparecerá cada vez que la aplicación se ponga en primer plano hasta que se complete la descarga de contenido.

<a name="Apple-Pay" />

## <a name="apple-pay"></a>Apple Pay

Si se especifica una fecha de expiración o un código de seguridad (CW) incorrectos al agregar una nueva tarjeta de pago a Apple Pay, se terminará el proceso de aprovisionamiento de tarjeta.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Compatibilidad binaria

Problemas conocidos:

- Si se llama a `NSObject.ValueForKey`, se producirá una excepción `null` clave.
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

La API de `CIImageProcessor` admite ahora un recuento de imágenes de entrada arbitrarias. `CIImageProcessor` API que se incluyó en macOS Sierra beta 1 se quitará.

<a name="Notifications" />

## <a name="notifications"></a>Notificaciones

Cuando se trabaja con extensiones de contenido de notificación, los controladores de vista no se liberan correctamente y pueden provocar un bloqueo cuando se alcanzan los límites de memoria de la extensión.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Después de una operación de entrega, la propiedad `UserInfo` de un objeto `NSUserActivity` puede estar vacía. Llame explícitamente `BecomeCurrent` objeto `NSUserActivity` como solución alternativa actual.

<a name="Safari" />

## <a name="safari"></a>Safari

WebGeolocation requiere una dirección URL segura (`https://`) para trabajar tanto en iOS 10 como en macOS Sierra para evitar el uso malintencionado de datos de ubicación.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de Mac](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [Novedades de macOS 10,12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
