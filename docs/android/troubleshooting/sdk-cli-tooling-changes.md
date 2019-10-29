---
title: Cambios en las Herramientas de Android SDK
description: Cambios en el modo en que el Android SDK administra los niveles de API instalados y AVD.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/21/2018
ms.openlocfilehash: 4c559a76d7354fd957d065717ef14d91591d1be0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019494"
---
# <a name="changes-to-the-android-sdk-tooling"></a>Cambios en las Herramientas de Android SDK

_Cambios en el modo en que el Android SDK administra los niveles de API instalados y AVD._

## <a name="changes-to-android-sdk-tooling"></a>Cambios en las herramientas de Android SDK

En las versiones recientes del SDK Tools para Android, Google ha quitado los administradores de AVD y SDK existentes en favor de las nuevas herramientas de la CLI (interfaz de línea de comandos). Se ha quitado el programa **Android** y los administradores de la GUI de Google (interfaz gráfica de usuario) de Visual Studio para Mac y las versiones anteriores de Visual Studio Tools para Xamarin ya no funcionarán después de la versión 25.2.5 de Android SDK Tools. Por ejemplo, si se intenta usar el programa **Android** a través de la línea de comandos, se producirá un mensaje de error similar al siguiente:

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

En las secciones siguientes se explica cómo administrar los dispositivos virtuales de Android SDK y Android con Android SDK 25.3.0 y versiones posteriores.

### <a name="ui-tools"></a>Herramientas de interfaz de usuario

Visual Studio y Visual Studio para Mac ahora proporcionan reemplazos de Xamarin para los administradores basados en la GUI de Google que no se han suspendido:

- Para descargar Android SDK herramientas, plataformas y otros componentes necesarios para desarrollar aplicaciones de Xamarin. Android, use el administrador de [Android SDK de Xamarin](~/android/get-started/installation/android-sdk.md) en lugar del administrador de SDK de Google heredado.

- Para crear y configurar dispositivos virtuales Android, use el [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md) en lugar del administrador de emuladores de Google heredado.

Estas herramientas son funcionalmente equivalentes a los administradores basados en la GUI de Google que reemplazan.

### <a name="cli-tools"></a>Herramientas de la CLI

Como alternativa, puede usar las herramientas de la CLI para administrar y actualizar los emuladores y Android SDK. Los siguientes programas componen ahora la interfaz de la línea de comandos para las herramientas de Android SDK:

#### <a name="sdkmanager"></a>sdkmanager

**Agregado en:** Android SDK Tools 25.2.3 (noviembre de 2016) y versiones posteriores.

Hay un nuevo programa denominado **sdkmanager** en la carpeta **Tools/bin** de la Android SDK. Esta herramienta se utiliza para mantener el Android SDK en la línea de comandos. Para obtener más información sobre el uso de esta herramienta, vea [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html).

#### <a name="avdmanager"></a>avdmanager

**Agregado en:** Android SDK Tools 25.3.0 (marzo de 2017) y versiones posteriores.

Hay un nuevo programa denominado **avdmanager** en la carpeta **Tools/bin** de la Android SDK. Esta herramienta se usa para mantener el AVD para el Android Emulator. Para obtener más información sobre el uso de esta herramienta, vea [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html).

### <a name="downgrading"></a>Degradar

Puede cambiar la versión de **Android SDK Tools** instalando una versión anterior del Android SDK desde el [sitio web para desarrolladores de Android](https://developer.android.com/studio/index.html).

### <a name="using-the-old-gui"></a>Uso de la GUI anterior

Todavía puede usar la interfaz gráfica de usuario original ejecutando el programa **Android** dentro de la carpeta de **herramientas** , siempre que esté en **Android SDK Tools** versión **25.2.5** o inferior.

## <a name="related-links"></a>Vínculos relacionados

- [Configuración de Android SDK](~/android/get-started/installation/android-sdk.md)
- [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)
- [Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md)
- [Notas de la versión de SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
