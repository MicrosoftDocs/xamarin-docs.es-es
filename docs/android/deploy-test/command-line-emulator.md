---
title: Emulador de la línea de comandos
ms.prod: xamarin
ms.assetid: E592AA32-5E83-B7E5-1753-12416551B23C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 11/05/2018
ms.openlocfilehash: 35e10ffc20e075e0245c7e42f7fd0aff24de4abb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021580"
---
# <a name="command-line-emulator"></a>Emulador de la línea de comandos

## <a name="running-the-android-emulator-from-the-command-line"></a>Ejecución de Android Emulator desde la línea de comandos

Para habilitar la ejecución de Android Emulator desde la línea de comandos, puede usar la herramienta "emulador" que se proporciona en Android SDK. Esta herramienta se puede usar para ejecutar el emulador desde Terminal en OS X o desde el símbolo del sistema en una máquina Windows.

Para iniciar un emulador de Android específico, ejecute el siguiente comando desde el directorio de herramientas en la ubicación de Android SDK (por ejemplo, C:\android-sdk-windows\tools):

En Windows

```cmd
emulator.exe -avd NameOfYourEmulator -partition-size 512
```

En macOS

```bash
./emulator -avd NameOfYourEmulator -partition-size 512
```

El motivo de necesitar el tamaño de partición es permitir que el emulador disponga de mucho espacio para instalarse en él la plataforma de Xamarin.Android dado que, de forma predeterminada, el tamaño del emulador es pequeño.

Puede encontrar más información sobre parámetros adicionales en este sitio de Android: [https://developer.android.com/studio/run/emulator-commandline](https://developer.android.com/studio/run/emulator-commandline)
