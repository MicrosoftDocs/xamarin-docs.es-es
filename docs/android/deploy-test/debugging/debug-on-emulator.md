---
title: Depuración en Android Emulator
description: En esta guía se explica cómo iniciar y depurar aplicaciones en Visual Studio mediante Android Emulator.
ms.prod: xamarin
ms.assetid: AEA165A4-D81A-411B-91DF-2DED2EED27B5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 8ca13b4f9c961b8bb206d065ce3cf641a8662160
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028083"
---
# <a name="debugging-on-the-android-emulator"></a>Depuración en Android Emulator

_En esta guía se explica cómo iniciar un dispositivo virtual en Android Emulator para depurar y probar la aplicación._

## <a name="overview"></a>Información general

Android Emulator (incluido como parte de la carga de trabajo **Desarrollo para dispositivos móviles con .NET**) se puede ejecutar en varias configuraciones para simular otros dispositivos Android. Cada una de estas configuraciones se crea como un _dispositivo virtual_. En esta guía, obtendrá información sobre cómo iniciar el emulador desde Visual Studio y ejecutar la aplicación en un dispositivo virtual. Para obtener información sobre cómo configurar Android Emulator y crear dispositivos virtuales, vea [Configuración de Android Emulator](~/android/get-started/installation/android-emulator/index.md).

## <a name="using-a-pre-configured-virtual-device"></a>Uso de un dispositivo virtual preconfigurado

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Visual Studio incluye dispositivos virtuales preconfigurados que aparecen en el menú desplegable del dispositivo. Por ejemplo, en la siguiente captura de pantalla de Visual Studio 2017, están disponibles varios dispositivos virtuales preconfigurados:

- **VisualStudio\_android-23\_arm\_phone**

- **VisualStudio\_android-23\_arm\_tablet**

- **VisualStudio\_android-23\_x86\_phone** 

- **VisualStudio\_android-23\_x86\_tablet** 

[![Dispositivos virtuales](debug-on-emulator-images/win/01-virtual-devices-sml.png)](debug-on-emulator-images/win/01-virtual-devices.png#lightbox)

Normalmente, se selecciona el dispositivo virtual **VisualStudio\_android-23\_x86\_phone** para probar y depurar una aplicación de teléfono. Si uno de estos dispositivos virtuales preconfigurados cumple los requisitos (es decir, coincide con el nivel de API de destino de la aplicación), vaya a [Inicio del emulador](#launching) para empezar a ejecutar la aplicación en el emulador. (Si todavía no está familiarizado con los niveles de API de Android, vea [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) (Información de los niveles de API de Android)).

Si el proyecto de Xamarin.Android usa un nivel de plataforma de destino que no es compatible con los dispositivos virtuales disponibles, en el menú desplegable se enumerarán los dispositivos virtuales que no se pueden usar, en **Dispositivos no compatibles**. Por ejemplo, el siguiente proyecto tiene una plataforma de destino establecida en **Android 7.1 Nougat (API 25)** , que es no es compatible con los dispositivos virtuales **Android 6.0** indicados en este ejemplo:

[![Dispositivo virtual no compatible](debug-on-emulator-images/win/02-incompatible-level-sml.png)](debug-on-emulator-images/win/02-incompatible-level.png#lightbox)

Puede hacer clic en **Cambiar destino mínimo de Android** para cambiar la versión mínima de Android del proyecto de modo que coincida con el nivel de API de los dispositivos virtuales disponibles. También puede usar [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md) para crear dispositivos virtuales compatibles con el nivel de API de destino.
Para poder configurar dispositivos virtuales para un nuevo nivel de API, debe instalar las imágenes del sistema correspondientes para ese nivel de API (vea [Configuración de Android SDK para Xamarin.Android](~/android/get-started/installation/android-sdk.md)).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Visual Studio para Mac incluye dispositivos virtuales preconfigurados que aparecen en el menú desplegable del dispositivo. Por ejemplo, en la siguiente captura de pantalla, están disponibles dos dispositivos virtuales preconfigurados:

- **Android\_Accelerated\_x86**

- **Android\_ARMv7a**

[![Dispositivos virtuales](debug-on-emulator-images/mac/01-virtual-devices-sml.png)](debug-on-emulator-images/mac/01-virtual-devices.png#lightbox)

Normalmente, se selecciona el dispositivo virtual **Android\_Accelerated\_x86** para probar y depurar una aplicación para teléfonos. Si este dispositivo virtual preconfigurado cumple los requisitos, es decir, coincide con el nivel de API de destino de la aplicación, vaya a [Launching the Emulator (Inicio del emulador)](#launching) para empezar a ejecutar la aplicación en el emulador. (Si todavía no está familiarizado con los niveles de API de Android, vea [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) (Información de los niveles de API de Android)).

-----

## <a name="editing-virtual-devices"></a>Edición de dispositivos virtuales

Para modificar dispositivos virtuales (o crear otros), debe usar [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).

<a name="launching" />

## <a name="launching-the-emulator"></a>Inicio del emulador

Cerca de la parte superior de Visual Studio, hay un menú desplegable que se puede usar para seleccionar el modo de **Depuración** o **Versión**. Al seleccionar **Depuración**, se asocia el depurador al proceso de la aplicación que se ejecuta dentro del emulador después de que se inicie la aplicación. Al seleccionar el modo de **Versión**, se deshabilita el depurador (pero todavía es posible ejecutar la aplicación y usar instrucciones de registro para la depuración). Tras elegir un dispositivo virtual en el menú desplegable de dispositivos, seleccione el modo de **Depuración** o **Versión**. Después, haga clic en el botón Reproducir para ejecutar la aplicación:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Modos de Depuración y Versión, botón Reproducir](debug-on-emulator-images/win/17-debug-release-sml.png)](debug-on-emulator-images/win/17-debug-release.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Modos de Depuración y Versión, botón Reproducir](debug-on-emulator-images/mac/16-debug-release-sml.png)](debug-on-emulator-images/mac/16-debug-release.png#lightbox)

-----

Cuando se inicie el emulador, Xamarin.Android implementará la aplicación en el emulador. El emulador ejecuta la aplicación con la imagen de dispositivo virtual configurada. A continuación, se muestra una captura de pantalla de ejemplo de Android Emulator. En este ejemplo, el emulador está ejecutando una aplicación en blanco denominada **MyApp**:

![El emulador está ejecutando una aplicación en blanco](debug-on-emulator-images/emulator-running.png)

El emulador puede dejarse en ejecución, de modo que no es necesario cerrarlo y esperar a que se reinicie cada vez que se inicie la aplicación. La primera vez que se ejecuta una aplicación de Xamarin.Android en el emulador, se instala el tiempo de ejecución compartido de Xamarin.Android para el nivel de API de destino, seguido de la aplicación. La instalación del tiempo de ejecución puede tardar unos minutos, así que tendrá que esperar. La instalación del tiempo de ejecución tiene lugar solamente cuando se implementa en el emulador la primera aplicación de Xamarin.Android, &ndash;de modo que las posteriores implementaciones serán más rápidas porque solo se copiará la aplicación en el emulador.

<a name="quick-boot" />

## <a name="quick-boot"></a>Arranque rápido

Las versiones más recientes de Android Emulator incluyen una característica denominada _Arranque rápido_ que inicia el emulador en unos pocos segundos. Al cerrar el emulador, toma una instantánea del estado del dispositivo virtual para que se pueda restaurar rápidamente desde ese estado cuando se reinicia.
Para obtener acceso a esta característica, necesita lo siguiente:

- Android Emulator versión 27.0.2 o posterior
- Android SDK Tools versión 26.1.1 o posterior

Cuando se instalan las versiones indicadas anteriormente del emulador y de las herramientas del SDK, la característica de Arranque rápido se habilita de forma predeterminada. 

El primer arranque en frío del dispositivo virtual tiene lugar sin una mejora de velocidad porque todavía no se ha creado una instantánea:

![Captura de pantalla del arranque en frío](debug-on-emulator-images/cold-boot.png)

Cuando salga del emulador, el Arranque rápido guarda el estado del emulador en una instantánea:

![Proceso de guardar el estado durante el apagado](debug-on-emulator-images/saving-state.png)

Los inicios posteriores del dispositivo virtual son mucho más rápidos porque el emulador simplemente restaura el estado con el que se ha cerrado el emulador.

![Proceso de carga del estado durante el reinicio](debug-on-emulator-images/loading-state.png)

## <a name="troubleshooting"></a>Solución de problemas

Para obtener sugerencias y soluciones alternativas a problemas habituales del emulador, vea [Solución de problemas de Android Emulator](~/android/get-started/installation/android-emulator/troubleshooting.md).

## <a name="summary"></a>Resumen

En esta guía se explica el proceso para configurar Android Emulator para ejecutar y probar aplicaciones Xamarin.Android. Se describen los pasos para iniciar el emulador mediante dispositivos virtuales preconfigurados y se proporcionan los pasos para implementar una aplicación en el emulador desde Visual Studio. 

Para obtener más información sobre cómo usar Android Emulator, vea los temas siguientes para desarrolladores de Android:

- [Navegar en la pantalla](https://developer.android.com/studio/run/emulator.html#navigate)

- [Realizar tareas básicas en el emulador](https://developer.android.com/studio/run/emulator.html#tasks)

- [Trabajar con los controles extendidos, la configuración y la ayuda](https://developer.android.com/studio/run/emulator.html#extended)

- [Ejecutar el emulador con el Arranque rápido](https://developer.android.com/studio/run/emulator#quickboot)
