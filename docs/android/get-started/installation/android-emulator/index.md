---
title: Configuración de Android Emulator
description: Android Emulator se puede ejecutar en varias configuraciones para simular múltiples dispositivos. En esta guía se explica cómo preparar Android Emulator para probar la aplicación.
ms.prod: xamarin
ms.assetid: 889963B7-F4DA-41D9-9B8D-B733BB71A329
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/27/2018
ms.openlocfilehash: 148afe5354d7995f15dc19c6257ed2a1567162ec
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027951"
---
# <a name="android-emulator-setup"></a>Configuración de Android Emulator

_En esta guía se explica cómo preparar Android Emulator para probar la aplicación._

## <a name="overview"></a>Información general

Android Emulator se puede ejecutar en varias configuraciones para simular múltiples dispositivos. Cada configuración se denomina _dispositivo virtual_. Al implementar y probar la aplicación en el emulador, debe seleccionar un dispositivo virtual preconfigurado o personalizado que simule un dispositivo Android físico, como un teléfono Nexus o Pixel.

En las secciones siguientes se describe cómo acelerar Android Emulator para obtener el máximo rendimiento, cómo usar Android Device Manager para crear y personalizar dispositivos virtuales y cómo personalizar las propiedades de perfil de un dispositivo virtual. Además, en la sección de solución de problemas se explican los problemas más habituales del emulador y sus soluciones.

## <a name="sections"></a>Secciones

### <a name="hardware-acceleration-for-emulator-performance"></a>[Aceleración de hardware para el rendimiento del emulador](~/android/get-started/installation/android-emulator/hardware-acceleration.md)

Cómo preparar el equipo para obtener el máximo rendimiento de Android Emulator mediante la tecnología de virtualización Hyper-V o HAXM. Dado que Android Emulator puede ser excesivamente lento sin la aceleración de hardware, se recomienda habilitar la aceleración de hardware en el equipo antes de usarlo.

### <a name="managing-virtual-devices-with-the-android-device-manager"></a>[Administración de dispositivos virtuales con Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)

Describe cómo usar Android Device Manager para crear y personalizar dispositivos virtuales.

### <a name="editing-android-virtual-device-properties"></a>[Editar las propiedades del dispositivo virtual Android](~/android/get-started/installation/android-emulator/device-properties.md)

Describe cómo usar Android Device Manager para editar las propiedades de perfil de un dispositivo virtual.

### <a name="android-emulator-troubleshooting"></a>[Solución de problemas de Android Emulator](~/android/get-started/installation/android-emulator/troubleshooting.md)

En este artículo se describen los mensajes de advertencia y los problemas más habituales que se producen al ejecutar Android Emulator, así como sugerencias y soluciones.

Después de configurar Android Emulator, vea [Depuración en Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md) para obtener información sobre cómo iniciar el emulador y usarlo para probar y depurar la aplicación.

> [!NOTE]
> A partir de la versión **26.0.1** de Android SDK Tools, Google ha quitado la compatibilidad con administradores de AVD/SDK existentes en favor de las nuevas herramientas de la CLI (interfaz de la línea de comandos). Debido a este cambio de degradación, ahora se usan administradores de SDK/dispositivos de Xamarin en lugar de administradores de SDK/dispositivos de Google para las Herramientas de Android 26.0.1 y versiones posteriores. Para obtener más información sobre Xamarin SDK Manager, vea [Configuración del SDK de Android para Xamarin.Android](~/android/get-started/installation/android-sdk.md).
