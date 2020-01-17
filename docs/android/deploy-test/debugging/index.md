---
title: Depuración de aplicaciones de Xamarin.Android en dispositivos y emuladores
description: Cómo probar y depurar la aplicación de Xamarin.Android
ms.prod: xamarin
ms.assetid: A355A471-8195-4391-93FE-0000BCB17923
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 3b3fa14ec81bd4f06322197b7140654f9086ce73
ms.sourcegitcommit: 5821c9709bf5e06e6126233932f94f9cf3524577
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/31/2019
ms.locfileid: "75556488"
---
# <a name="debug-xamarinandroid-apps"></a>Depuración de aplicaciones de Xamarin.Android

En esta sección se describe cómo depurar una aplicación de Xamarin.Android en dispositivos o emuladores.

## <a name="debugging-overview"></a>Información general sobre depuración

Para desarrollar una aplicación Android es necesario ejecutarla, ya sea en un hardware físico o mediante un emulador. El uso de hardware es el mejor enfoque, pero no siempre es el más práctico. En muchos casos, puede resultar más sencillo y rentable simular o emular el hardware de Android mediante uno de los emuladores que se describen a continuación.

### <a name="debugging-on-the-android-emulatorandroiddeploy-testdebuggingdebug-on-emulatormd"></a>[Depuración en Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md)

En este artículo se describe cómo iniciar Android Emulator desde Visual Studio y ejecutar la aplicación en un dispositivo virtual.

### <a name="debugging-on-a-deviceandroiddeploy-testdebuggingdebug-on-devicemd"></a>[Depuración en un dispositivo](~/android/deploy-test/debugging/debug-on-device.md)

En este artículo se muestra cómo configurar un dispositivo Android físico para que la aplicación de Xamarin.Android se pueda implementar directamente desde Visual Studio o Visual Studio para Mac.

### <a name="android-debug-logandroiddeploy-testdebuggingandroid-debug-logmd"></a>[Registro de depuración de Android](~/android/deploy-test/debugging/android-debug-log.md)

Un truco muy común que emplean los desarrolladores para depurar sus aplicaciones es usar `Console.WriteLine`. Pero en una plataforma móvil como Android no hay consola. Los dispositivos Android proporcionan un registro que es probable que necesite usar al escribir aplicaciones. Este registro se conoce a veces como **logcat** por el comando escrito para recuperarlo. En este artículo se describe cómo usar **logcat**.
