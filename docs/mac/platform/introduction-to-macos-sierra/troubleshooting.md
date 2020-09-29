---
title: 'Xamarin. Mac: solución de problemas de macOS Sierra'
description: En este documento se proporcionan varias sugerencias para la solución de problemas para trabajar con macOS Sierra en aplicaciones de Xamarin. Mac. Las sugerencias se relacionan con Mac App Store, Apple Pay, compatibilidad binaria, CFNetwork, CloudKit, etc.
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 09/22/2016
ms.openlocfilehash: d3ec300cbda5d22a285c3f63d3281bde29b94024
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430055"
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

<a name="App-Store"></a>

## <a name="app-store"></a>App Store

Problemas conocidos:

- Al probar las compras desde la aplicación en el entorno de espacio aislado, el cuadro de diálogo de autenticación puede aparecer dos veces.
- Al probar compras desde la aplicación con contenido hospedado en el entorno de espacio aislado, el cuadro de diálogo de contraseña aparecerá cada vez que la aplicación se ponga en primer plano hasta que se complete la descarga de contenido.

<a name="Apple-Pay"></a>

## <a name="apple-pay"></a>Apple Pay

Si se especifica una fecha de expiración o un código de seguridad (CW) incorrectos al agregar una nueva tarjeta de pago a Apple Pay, se terminará el proceso de aprovisionamiento de tarjeta.

<a name="Binary-Compatibility"></a>

## <a name="binary-compatibility"></a>Compatibilidad binaria

Problemas conocidos:

- Si se llama `NSObject.ValueForKey` a, se producirá una `null` excepción en una clave.
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

La `CIImageProcessor` API ahora es compatible con un recuento de imágenes de entrada arbitrarias. `CIImageProcessor` Se quitará la API que se incluyó en macOS Sierra beta 1.

<a name="Notifications"></a>

## <a name="notifications"></a>Notificaciones

Cuando se trabaja con extensiones de contenido de notificación, los controladores de vista no se liberan correctamente y pueden provocar un bloqueo cuando se alcanzan los límites de memoria de la extensión.

<a name="NSUserActivity"></a>

## <a name="nsuseractivity"></a>NSUserActivity

Después de una operación de entrega, la `UserInfo` propiedad de un `NSUserActivity` objeto puede estar vacía. Llame explícitamente al `BecomeCurrent` `NSUserActivity` objeto como solución alternativa actual.

<a name="Safari"></a>

## <a name="safari"></a>Safari

WebGeolocation requiere una `https://` dirección URL segura () para trabajar tanto en iOS 10 como en MacOS Sierra para evitar el uso malintencionado de datos de ubicación.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de Mac](/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [Novedades de macOS 10,12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)