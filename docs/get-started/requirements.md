---
title: Requisitos de Xamarin.Forms
description: Requisitos de sistema de desarrollo y plataformas para Xamarin.Forms.
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/01/2019
ms.openlocfilehash: e4584867e457661b2dfaa43fcbde7b1adcabeb85
ms.sourcegitcommit: e354aabfb39598e0ce11115db3e6bcebb9f68338
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273108"
---
# <a name="xamarinforms-requirements"></a>Requisitos de Xamarin.Forms

_Requisitos de sistema de desarrollo y plataformas para Xamarin.Forms._

Consulte el artículo [Instalación](installation/index.md) para obtener información general de las prácticas de instalación y configuración que se aplican entre plataformas.

## <a name="target-platforms"></a>Plataformas de destino

Las aplicaciones de Xamarin.Forms pueden escribirse para los siguientes sistemas operativos:

- iOS 9 o superior
- Android 4.4 (API 19) o versiones posteriores ([más detalles](#android))
- Plataforma universal de Windows de Windows 10 ([más detalles](#windows10))

Sin embargo, se recomienda Android 5,0 (API 21) como la API mínima. Esto garantiza la compatibilidad total con todas las bibliotecas de compatibilidad de Android, a la vez que se destina a la mayoría de los dispositivos Android.

Se supone que los desarrolladores están familiarizados con [.net Standard](~/cross-platform/app-fundamentals/net-standard.md).

### <a name="additional-platform-support"></a>Compatibilidad con plataforma adicional

El estado de estas plataformas está disponible en el [GitHub de Xamarin.Forms](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support):

- Samsung Tizen
- macOS
- GTK#
- WPF

### <a name="android"></a>Android

Debe tener instalada la plataforma más reciente de Android SDK Tools y de la API de Android. Puede actualizar a las versiones más recientes con [Android SDK Manager](~/android/get-started/installation/android-sdk.md).

Además, la versión de compilación o de destino de los proyectos de Android **debe** establecerse en *Usar la última plataforma instalada*, aunque la versión mínima se puede establecer en API 19 para que pueda seguir admitiendo dispositivos que usen Android 4.4 y versiones más recientes. Estos valores se establecen en **Opciones del proyecto**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

**Opciones de proyecto > Aplicación > Propiedades de la aplicación**

![Opciones de compilación de Android en Visual Studio](requirements-images/options-android-vs-sml.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

**Compilar > General**

![Seleccionar la última versión de .NET Framework de destino](requirements-images/options-general-sml.png)

**Compilar > Aplicación de Android**

![Seleccione las versiones de Android mínima y de destino para la aplicación](requirements-images/options-android-sml.png)

-----

## <a name="development-system-requirements"></a>Requisitos del sistema de desarrollo

Las aplicaciones de Xamarin.Forms se pueden desarrollar en equipos macOS y Windows. Sin embargo, Windows y Visual Studio son necesarios para producir versiones de Windows de la aplicación.

## <a name="mac-system-requirements"></a>Requisitos del sistema de equipos Mac

Puede usar Visual Studio para Mac para desarrollar aplicaciones de Xamarin. Forms en macOS High Sierra (10,13) o una versión más reciente. Para desarrollar aplicaciones de iOS, se recomienda tener instalado al menos el SDK de iOS 10 y Xcode 9.

> [!NOTE]
> Las aplicaciones de Windows no se pueden desarrollar en macOS.

## <a name="windows-system-requirements"></a>Requisitos del sistema de equipos Windows

Las aplicaciones de Xamarin.Forms para iOS y Android se pueden compilar en cualquier instalación de Windows que admita el desarrollo de Xamarin. Para ello se necesita Visual Studio 2017 o una versión más reciente que se ejecute en Windows 7 o posterior. Se necesita un equipo Mac en red para el desarrollo de iOS.

<a name="windows10" />

### <a name="universal-windows-platform-uwp"></a>Plataforma universal de Windows (UWP)

El desarrollo de aplicaciones de Xamarin.Forms para UWP requiere lo siguiente:

- Windows 10 (se recomienda la última versión, el mínimo de creadores de la actualización)

- Visual Studio 2019 recomendado (Visual Studio 2017 versión 15,8 mínima)

- [SDK de Windows 10](https://dev.windows.com/downloads/windows-10-sdk)

Puede [agregar una aplicación de la Plataforma universal de Windows (UWP)](~/xamarin-forms/platform/windows/installation/index.md) a una solución existente de Xamarin.Forms en cualquier momento.

## <a name="deprecated-platforms"></a>Plataformas desusadas

Estas plataformas no se admiten cuando se usa Xamarin. Forms 3,0 o una versión más reciente:

- *Windows 8.1 / Windows Phone 8.1 WinRT*
- *Windows Phone 8 Silverlight*
