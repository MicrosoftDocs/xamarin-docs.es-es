---
title: Cambios en las Herramientas de Android SDK
description: Cambios en el modo en que Android SDK administra los niveles de API instalados y los AVD.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/21/2018
ms.openlocfilehash: 4c559a76d7354fd957d065717ef14d91591d1be0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019494"
---
# <a name="changes-to-the-android-sdk-tooling"></a>Cambios en las Herramientas de Android SDK

_Cambios en el modo en que Android SDK administra los niveles de API instalados y los AVD._

## <a name="changes-to-android-sdk-tooling"></a>Cambios en las herramientas de Android SDK

En las versiones recientes de SDK Tools para Android, Google ha quitado los administradores de AVD y SDK existentes en favor de las nuevas herramientas de la CLI (interfaz de línea de comandos). Se ha quitado el programa **android** y los administradores de la GUI (interfaz gráfica de usuario) de Google de Visual Studio para Mac y las versiones anteriores de Visual Studio Tools para Xamarin dejarán de funcionar después de la versión 25.2.5 de Android SDK Tools. Por ejemplo, si intenta usar el programa **android** a través de la línea de comandos, se producirá un mensaje de error similar al siguiente:

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

En las secciones siguientes se explica cómo administrar Android SDK y los dispositivos virtuales Android con Android SDK 25.3.0 y versiones posteriores.

### <a name="ui-tools"></a>Herramientas de interfaz de usuario

Visual Studio y Visual Studio para Mac ahora proporcionan reemplazos de Xamarin para los administradores basados en la GUI de Google que se han suspendido:

- Para descargar herramientas de Android SDK, plataformas y otros componentes necesarios para desarrollar aplicaciones de Xamarin.Android, use el [Administrador de Xamarin Android SDK](~/android/get-started/installation/android-sdk.md) en lugar del administrador de SDK de Google heredado.

- Para crear y configurar dispositivos virtuales Android, use el [Administrador de dispositivos Android](~/android/get-started/installation/android-emulator/device-manager.md) en lugar del administrador del emulador de Google heredado.

Estas herramientas son funcionalmente equivalentes a los administradores basados en la GUI de Google a los que reemplazan.

### <a name="cli-tools"></a>Herramientas de la CLI

Como alternativa, puede usar las herramientas de la CLI para administrar y actualizar los emuladores y Android SDK. Los siguientes programas componen ahora la interfaz de la línea de comandos para las herramientas de Android SDK:

#### <a name="sdkmanager"></a>sdkmanager

**Agregado en:** Android SDK Tools 25.2.3 (noviembre de 2016) y versiones posteriores.

Hay un nuevo programa llamado **sdkmanager** en la carpeta **tools/bin** de Android SDK. Esta herramienta se utiliza para mantener Android SDK en la línea de comandos. Para obtener más información sobre el uso de esta herramienta, consulte [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html).

#### <a name="avdmanager"></a>avdmanager

**Agregado en:** Android SDK Tools 25.3.0 (marzo de 2017) y versiones posteriores.

Hay un nuevo programa llamado **avdmanager** en la carpeta **tools/bin** de Android SDK. Esta herramienta se usa para mantener los AVD para Android Emulator. Para obtener más información sobre el uso de esta herramienta, consulte [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html).

### <a name="downgrading"></a>Degradación

Puede degradar la versión de **Android SDK Tools** instalando una versión anterior de Android SDK desde el [sitio web para desarrolladores de Android](https://developer.android.com/studio/index.html).

### <a name="using-the-old-gui"></a>Uso de la GUI anterior

Todavía puede usar la interfaz gráfica de usuario original ejecutando el programa **android** dentro de la carpeta **tools**, siempre y cuando se encuentre en **Android SDK Tools**, versión **25.2.5** o inferior.

## <a name="related-links"></a>Vínculos relacionados

- [Configuración de Android SDK](~/android/get-started/installation/android-sdk.md)
- [Administrador de dispositivos Android](~/android/get-started/installation/android-emulator/device-manager.md)
- [Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md)
- [Notas de la versión de SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
