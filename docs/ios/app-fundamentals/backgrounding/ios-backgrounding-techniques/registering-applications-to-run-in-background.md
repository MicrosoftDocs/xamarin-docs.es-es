---
title: Registro de aplicaciones de Xamarin. iOS para que se ejecuten en segundo plano
description: En este documento se describe cómo registrar una aplicación de Xamarin. iOS para que se ejecute en segundo plano. Se describen las aplicaciones de audio, las aplicaciones VoIP, los accesorios externos y Bluetooth, entre otros.
ms.prod: xamarin
ms.assetid: 8F89BE63-DDB5-4740-A69D-F60AEB21150D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 67cc2028276b6e1415c88cb8957e2cd567fa5bae
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521269"
---
# <a name="registering-xamarinios-apps-to-run-in-the-background"></a>Registro de aplicaciones de Xamarin. iOS para que se ejecuten en segundo plano

El registro de tareas individuales para los privilegios en segundo plano funciona con algunas aplicaciones, pero lo que sucede si se llama a una aplicación constantemente para realizar tareas importantes de larga ejecución, como la obtención de instrucciones para el usuario a través de GPS. En su lugar, las aplicaciones como estas deben registrarse como aplicaciones necesarias para el segundo plano.

Al registrar una aplicación, se indica a iOS que la aplicación debe tener privilegios especiales necesarios para realizar las tareas en segundo plano.

## <a name="application-registration-categories"></a>Categorías de registro de aplicaciones

Las aplicaciones registradas pueden dividirse en varias categorías:

- Los reproductores de música de **audio** y otras aplicaciones que funcionan con contenido de audio se pueden registrar para seguir reproduciendo audio incluso cuando la aplicación ya no está en primer plano. Si una aplicación de esta categoría intenta hacer algo que no sea reproducir audio o descargar mientras está en segundo plano, iOS la terminará.
- Las aplicaciones VoIP-Voice over Internet Protocol (VoIP) obtienen los mismos privilegios concedidos a las aplicaciones de audio para seguir procesando audio en segundo plano. También se les permite responder según sea necesario a los servicios de VoIP que los encienden, para mantener sus conexiones activas.
- **Accesorios externos y Bluetooth** : reservados para aplicaciones que necesitan comunicarse con dispositivos Bluetooth y otros accesorios de hardware externo, el registro en estas categorías permite que la aplicación permanezca conectada al hardware.
- **NewsStand** : una aplicación de NewsStand puede seguir sincronizando el contenido en segundo plano.
- **Ubicación** : las aplicaciones que hacen uso de datos GPS o de ubicación de red pueden enviar y recibir actualizaciones de ubicación en segundo plano.
- **Fetch (iOS 7 +)** : una aplicación registrada para los privilegios de captura en segundo plano puede comprobar si un proveedor tiene contenido nuevo a intervalos regulares y presentar el usuario con el contenido actualizado cuando vuelva a la aplicación.
- **Notificaciones remotas (iOS 7 +)** : las aplicaciones pueden registrarse para recibir notificaciones de un proveedor y usar la notificación para iniciar una actualización antes de que el usuario abra la aplicación. Las notificaciones pueden presentarse en forma de notificaciones de envío, o bien optar por reactivar la aplicación en modo silencioso.


Las aplicaciones se pueden registrar estableciendo la propiedad de **modos en segundo plano requeridos** en *info. plist*de la aplicación. Una aplicación puede registrarse en tantas categorías como requiera:

 [![](registering-applications-to-run-in-background-images/bgmodes.png "Establecer los modos en segundo plano")](registering-applications-to-run-in-background-images/bgmodes.png#lightbox)

Para obtener una guía paso a paso sobre cómo registrar una aplicación para las actualizaciones de ubicación en segundo plano, vea el [tutorial de ubicación en segundo plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/location-walkthrough.md).

## <a name="application-does-not-run-in-background-property"></a>La aplicación no se ejecuta en la propiedad Background

Otra propiedad que se puede establecer en *info. plist* es que la *aplicación no se ejecuta en segundo plano*o `UIApplicationExitsOnSuspend` la propiedad:

 [![](registering-applications-to-run-in-background-images/plist.png "Deshabilitar la ejecución en segundo plano")](registering-applications-to-run-in-background-images/plist.png#lightbox)

Esto tiene el mismo efecto que establecer la configuración de actualización de la aplicación en segundo plano en OFF en iOS 7 +, salvo que solo se puede cambiar en el lado del desarrollador y está disponible para iOS 4 y versiones posteriores. La aplicación se suspenderá inmediatamente después de escribir el fondo y no podrá realizar ningún procesamiento.

Utilice esta propiedad si la aplicación no está diseñada para controlar el procesamiento en segundo plano, ya que ayuda a evitar un comportamiento inesperado.

## <a name="background-fetch-and-remote-notifications"></a>Captura en segundo plano y notificaciones remotas

La captura en segundo plano y las notificaciones remotas son categorías de registro especiales introducidas en iOS 7. Estas categorías permiten a las aplicaciones recibir contenido nuevo de un proveedor y actualizar en segundo plano. En la siguiente sección se explora la recopilación y las notificaciones remotas con mayor detalle, y también se presenta el reconocimiento de la ubicación como medio para actualizar una aplicación en segundo plano en iOS 6.
