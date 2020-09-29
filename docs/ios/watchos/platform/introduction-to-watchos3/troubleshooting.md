---
title: Solución de problemas de watchos 3
description: En este documento se proporcionan varias sugerencias de solución de problemas útiles al trabajar con watchos 3 en Xamarin. Sugerencias relacionadas con actividades, Apple Pay, actualización en segundo plano, NSURLConnection, privacidad y mucho más.
ms.prod: xamarin
ms.assetid: 5911D898-0E23-40CC-9F3C-5F61B4D50ADC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 2a55991dacd6c35a2fe90cf90ad6f0e2208ff71f
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430458"
---
# <a name="watchos-3-troubleshooting"></a>Solución de problemas de watchos 3

_En este artículo se proporcionan varias sugerencias para la solución de problemas para trabajar con watchos 3 en Xamarin Apple Watch apps._

En esta página se enumeran algunos problemas conocidos que pueden producirse al usar watchos 3 con Xamarin y la solución a esos problemas.

## <a name="activities"></a>Actividades

Para que el uso compartido de actividades funcione correctamente, todos los relojes de Apple emparejados deben ejecutar watchos 3.

Problemas conocidos:

- A veces se produce un error al responder a una notificación de uso compartido de actividad.
- Se puede producir un error al responder a una notificación de uso compartido de actividad con un mensaje.
- El texto contextual sobre un mensaje de notificación de uso compartido de actividad será incorrecto.

## <a name="apple-pay"></a>Apple Pay

Problemas conocidos:

- Si se especifica una fecha de expiración incorrecta o un código CW para una nueva atención de pago en Apple Pay, cuando se llega al **siguiente** , se bloqueará el proceso en ejecución.
- Las compras desde la aplicación Apple Pay que requieren un número PIN pueden bloquearse.

## <a name="auto-mac-unlock"></a>Desbloqueo de Mac automática

Con watchos 3 beta 2 (o posterior) y macOS Sierra beta 2 (o superior), si la autenticación en dos fases está habilitada en la cuenta de iCloud del usuario, pueden usar su Apple Watch para desbloquear automáticamente su Mac.

## <a name="background-refresh"></a>Actualización en segundo plano

Si se infringen los recursos del sistema, se producirá un bloqueo de la aplicación watchos 3 con los siguientes códigos de excepción:

- **0xc51bad01** : la aplicación consume demasiado tiempo de CPU.
- **0xc51bad02** : la aplicación consume demasiado tiempo en la pared.
- **0xc51bad03** : la aplicación no tenía tiempo de ejecución suficiente para completar la tarea actual.

## <a name="clock"></a>Clock

Las complicaciones de las aplicaciones Apple Watch recién instaladas pueden aparecer en blanco. Reinicie Apple Watch para corregir este problema.

## <a name="connectivity"></a>Conectividad

Problemas conocidos:

- watchos no pedirá al usuario permiso de acceso para los datos de usuario protegidos en el Apple Watch. Conceda acceso a la aplicación de iPhone antes de usar los datos en la aplicación de inspección.
- El Apple Watch puede especificar un estado en el que se produzca un error en todas las transmisiones WatchConnectivity, reinicie el Apple Watch para corregirlo.

## <a name="notifications"></a>Notificaciones

Si un archivo multimedia es demasiado grande, se presentará en el iPhone del usuario, pero no en el Apple Watch.

## <a name="nsurlconnection"></a>NSURLConnection

`NSURLConnection`Se producirá un error en todas las conexiones que usen protocolos TLS más antiguos. Para todas las conexiones SSL/TLS, el cifrado simétrico RC4 ahora está deshabilitado de forma predeterminada. Además, la API de transporte seguro ya no admite SSLv3 y se recomienda que la aplicación deje de usar la criptografía SHA-1 y 3DES lo antes posible.

A partir de watchos 3, Apple fuerza la seguridad de las conexiones SSL/TLS. Los servicios y las aplicaciones afectados deben actualizar los servidores web para usar las últimas versiones del protocolo TLS.

## <a name="nsurlsession"></a>NSURLSession

A partir de watchos 3, la `HTTPBodyStream` propiedad de la `NSMutableURLRequest` clase se debe establecer en un flujo no abierto ya que `NSURLConnection` y `NSURLSession` ahora exigen estrictamente este requisito.

## <a name="privacy"></a>Privacidad

Problemas conocidos:

Cuando se trabaja con `https://` direcciones URL `NSURLSession` y `NSURLConnection` ya no son compatibles con los conjuntos de cifrado RC4 durante el protocolo de enlace TLS. Puede generarse uno de los siguientes códigos de error:

- **-1200 o-98** - `NSURLErrorSecurityConnectionFailed` errores y SecureTransport.
- **-1200 [3:-9824]** -error de carga http.
- **-1200**  -  `NSURLConnection` finalizó con error.

A partir de watchos 3, Apple fuerza la seguridad de las conexiones SSL/TLS. Los servicios y las aplicaciones afectados deben actualizar los servidores web para usar las últimas versiones del protocolo TLS. Para obtener más información, vea [NSURLConnection](#nsurlconnection) .

## <a name="snapshots"></a>Instantáneas

Las aplicaciones de WatchKit que no hayan adoptado la nueva `HandelBackgroundTask` API ya no recibirán actualizaciones periódicas en watchos 3. 

## <a name="watchkit"></a>WatchKit

Las escenas de SpriteKit y SceneKit se detendrán cuando una aplicación escriba el fondo en la base de watchos.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de watchOS](/samples/browse/?products=xamarin&term=Xamarin.iOS%2bwatchOS)
- [Novedades de watchos 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)