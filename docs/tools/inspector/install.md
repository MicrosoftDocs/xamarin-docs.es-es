---
title: Instalación y requisitos del inspector
description: En este documento se describe cómo instalar el Xamarin Inspector y se describen las plataformas de sistema operativo, IDE y de aplicación compatibles.
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
author: conceptdev
ms.author: crdun
ms.date: 06/19/2018
ms.openlocfilehash: 1273a51d29d7abcbecb9b19ae42e111db8ccc06c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292302"
---
# <a name="inspector-installation-and-requirements"></a>Instalación y requisitos del inspector

## <a name="download-and-installation"></a>Descarga e instalación

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. Descargue e instale [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/) y seleccione la carga de trabajo **desarrollo para dispositivos móviles con .net** .
1. [Inicie sesión](https://docs.microsoft.com/visualstudio/ide/signing-in-to-visual-studio) para habilitar su suscripción Enterprise.
1. [Inspeccione](~/tools/inspector/inspect.md) su propia aplicación.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. Descargue e instale [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).
1. [Inicie sesión](https://docs.microsoft.com/visualstudio/mac/activation) para habilitar su suscripción Enterprise.
1. [Inspeccione](~/tools/inspector/inspect.md) su propia aplicación.

-----

## <a name="requirements"></a>Requisitos

### <a name="supported-operating-systems"></a>Sistemas operativos admitidos

- **Mac** -OS X 10,11 o superior
- **Windows** -Windows 7 o posterior (con Internet Explorer 11 o posterior y .net 4.6.1 o superior)

### <a name="supported-ides"></a>IDE compatibles

- Visual Studio para Mac
- Visual Studio 2017 con la carga **de trabajo desarrollo para dispositivos móviles con .net**

La inspección de aplicaciones activas está disponible para los clientes empresariales.

<a name="supported-platforms" />

### <a name="supported-app-platforms"></a>Plataformas de aplicaciones admitidas

|Plataforma de aplicaciones|Compatibilidad con IDE|Notas|
|--- |--- |--- |
|Mac|Solo se admite en Visual Studio para Mac|
|iOS|Compatible con Visual Studio 2017 y Visual Studio para Mac| El comportamiento del vinculador debe establecerse en **no vincular** (en opciones de proyecto de **compilación de iOS** ) |
|Android|Compatible con Visual Studio 2017 y Visual Studio para Mac|Debe tener como destino Android > = 4.0.3, con **fastdev** habilitado.<br />Debe usar los emuladores de Google, Visual Studio o Xamarin Android. Es posible que los emuladores de Android 7 no permitan la inspección en este momento.|
|WPF|Solo se admite en Visual Studio 2017|

<a name="reporting-bugs" />

## <a name="reporting-bugs"></a>Informes de errores

Los errores se deben informar directamente a través de Visual Studio:

- **Ayuda > Enviar comentarios > notificar un problema**

Incluya toda la información siguiente:

### <a name="platform-version-information"></a>Información de versión de la plataforma

Esta información es fundamental.

Visual Studio para Mac

- **Visual Studio > Acerca de Visual Studio > Mostrar detalles > copiar información**
- Pegar en informe de errores

Visual Studio

- **Ayuda > Acerca de Visual Studio > copiar información**
- Háganoslo saber la versión del sistema operativo y si está ejecutando Windows de 32 bits o de 64 bits.

### <a name="log-files"></a>Archivos de registro

Adjunte siempre los archivos de registro de cliente del IDE y del inspector.

Cliente de inspector

- Macintosh`~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

1.4. x también ofrece la posibilidad de seleccionar el archivo de registro en Finder (macOS) o en el explorador (Windows) directamente desde el menú principal:

- **Ayuda > Mostrar archivo de registro**

Visual Studio para Mac

- `~/Library/Logs/VisualStudio/7.0/Ide.log`

Visual Studio

- `%LOCALAPPDATA%\Xamarin\Logs\{VS version}\Inspector {date}.log`
- El contenido del panel de **salida** de Visual Studio también puede ser informativo.

### <a name="project-settings"></a>Configuración del proyecto

Si puede adjuntar el **. csproj** para el proyecto que está intentando inspeccionar, sería muy útil. Esto es más fácil que preguntar acerca de la configuración individual.

Asegúrese también de que está en la configuración de depuración.

### <a name="selected-devices"></a>Dispositivos seleccionados

En el caso de Android e iOS, es fundamental saber en qué dispositivo se está depurando cuando se desea inspeccionar. Necesitamos saber:

- Nombre del dispositivo tal como se muestra en el IDE
- Versión del sistema operativo del dispositivo
- Android: Comprobar que se usa un emulador x86
- Android: ¿Qué plataforma de emulador usa? ¿Es el emulador de Google? ¿Android Emulator de Visual Studio? ¿Xamarin Android Player?
- ¿La aplicación que está depurando aparece correctamente y funciona en el dispositivo?
- ¿El dispositivo tiene conectividad de red (consulte a través del explorador Web)?

[client-bugs]: https://github.com/Microsoft/workbooks/issues/new
