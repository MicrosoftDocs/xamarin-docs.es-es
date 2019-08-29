---
title: Implementación inalámbrica para aplicaciones Xamarin.iOS y tvOS
description: En este documento se describe cómo implementar de forma inalámbrica una aplicación Xamarin.iOS en un dispositivo iOS desde Visual Studio para Mac o Visual Studio 2019.
ms.prod: xamarin
ms.assetid: 5AB4C5A9-4FBB-4DCB-BD72-0022D5439E65
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.custom: video
ms.date: 01/22/2019
ms.openlocfilehash: ac4ae1d8b247a709307841be1ffa1fee2afbf68f
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70121475"
---
# <a name="wireless-deployment-for-xamarinios-and-tvos-apps"></a>Implementación inalámbrica para aplicaciones Xamarin.iOS y tvOS

Una parte importante del flujo de trabajo del desarrollador es la implementación en un dispositivo. Xcode 9 introdujo la opción de implementación en un dispositivo iOS o Apple TV a través de una red, en lugar de tener que conectar directamente los dispositivos cada vez que quiera implementar y depurar la aplicación. Esta característica se ha introducido en Visual Studio para Mac 7.4 y Visual Studio 15.6.

En esta guía se detalla cómo emparejar e implementar en un dispositivo a través de la red.

## <a name="requirements"></a>Requisitos

La implementación inalámbrica está disponible como característica tanto en Visual Studio para Mac como en Visual Studio.

Para usar la implementación inalámbrica, debe tener lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

- macOS 10.12.4
- La versión más reciente de Visual Studio para Mac
- Xcode 9.0 o posterior
- Un dispositivo con iOS 11.0 o tvOS 11.0 y versiones posteriores

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

- La versión más reciente de Visual Studio
- Un dispositivo con iOS 11.0 o tvOS 11.0 y versiones posteriores

En el host de compilación del equipo Mac, deben estar instalados los componentes siguientes:

- macOS 10.12.4
- Visual Studio para Mac
- Xcode 9.0 o posterior

-----

## <a name="connecting-a-device"></a>Conexión de un dispositivo

Para implementar y depurar de manera inalámbrica en su dispositivo, debe emparejar el dispositivo iOS o Apple TV con Xcode en el equipo Mac. Una vez emparejado, puede seleccionarlo en la lista de destinos de dispositivo de Visual Studio. 

El siguiente proceso de emparejamiento solo tiene que producirse una vez por cada dispositivo. Xcode conservará la configuración de conexión.

<a name="pair" />

### <a name="pairing-an-ios-device-with-xcode"></a>Emparejamiento de un dispositivo iOS con Xcode

1. Abra Xcode y vaya a **Window > Devices and Simulators** (Ventana > Dispositivos y simuladores).
2. Conecte el dispositivo iOS a su equipo Mac con un cable relámpago. Puede que tenga que seleccionar la opción **Trust This Computer** (Confiar en este equipo) en el dispositivo.
3. Seleccione el dispositivo y, a continuación, active la casilla **Connect via network** (Conectarse mediante una red) para emparejar su dispositivo:  ![Ventana del simulador y dispositivo que muestra la opción de conectarse mediante una red](wireless-deployment-images/image2.png)

### <a name="pairing-an-apple-tv-with-xcode"></a>Emparejamiento de un dispositivo Apple TV con Xcode

1. Asegúrese de que el equipo Mac y Apple TV están conectados a la misma red.

2. Abra Xcode y vaya a **Window > Devices and Simulators** (Ventana > Dispositivos y simuladores).

3. En Apple TV, vaya a **Settings > Remotes and Devices > Remote App and Devices** (Configuración > Remotos y dispositivos > Aplicación remota y dispositivos).

4. Seleccione Apple TV en el área **Discovered** (Detectado) de Xcode y escriba el código de verificación que aparece en el dispositivo Apple TV.

5. Haga clic en el botón **Connect** (Conectar). Cuando se empareja correctamente, aparece un icono de conexión de red al lado de Apple TV.

## <a name="deploy-to-a-device"></a>Implementar en un dispositivo

Cuando un dispositivo se conecta de forma inalámbrica y está listo para usarse para la implementación, aparece en la lista de destinos de dispositivo, como si el dispositivo se hubiera conectado a través de USB.

Para realizar pruebas en un dispositivo físico, el dispositivo debe estar [aprovisionado](~/ios/get-started/installation/device-provisioning/index.md). Asegúrese de hacerlo antes de intentar implementar en un dispositivo. 

Para implementar en un dispositivo iOS o tvOS, siga estos pasos:

1. Asegúrese de que la máquina de implementación y el dispositivo de destino se encuentran en la misma red inalámbrica. 

2. Seleccione el dispositivo de la lista de dispositivos de destino y ejecute la aplicación.

3. Si el dispositivo está bloqueado, se le pedirá que lo desbloquee. Cuando el dispositivo esté desbloqueado, la aplicación se implementa en él.

La depuración inalámbrica se habilita automáticamente tras la implementación inalámbrica, por lo que puede usar puntos de interrupción establecidos previamente y continuar el flujo de trabajo de depuración, tal y como lo ha hecho siempre.

## <a name="troubleshooting"></a>Solución de problemas

1. Asegúrese siempre de que el dispositivo iOS o Apple TV están conectados a la misma red que el equipo Mac.

2. Si el dispositivo no aparece en Visual Studio, compruebe la ventana **Devices and Simulators** (Dispositivos y simuladores) de Xcode. 

    - Si Xcode **no** muestra el dispositivo como conectado, pruebe a [emparejar](#pair) de nuevo el dispositivo.

    - Si Xcode sí muestra el dispositivo como conectado, pruebe a reiniciar Visual Studio y el dispositivo.

3. Si aún no lo ha hecho, tendrá que [aprovisionar](~/ios/get-started/installation/device-provisioning/index.md) el dispositivo.

4. Si tiene problemas con esta característica que los pasos anteriores no pueden corregir, informe de la existencia de un problema en [Developer Community](https://developercommunity.visualstudio.com/spaces/41/index.html).

## <a name="related-links"></a>Vínculos relacionados

- [Emparejamiento de un dispositivo inalámbrico con Xcode](https://help.apple.com/xcode/mac/9.0/index.html?localePath=en.lproj#/devbc48d1bad)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Debug-to-iOS-Devices-Over-Wi-Fi/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
