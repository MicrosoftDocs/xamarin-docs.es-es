---
title: Xamarin. Requisitos de formularios
description: Requisitos del sistema de plataforma y desarrollo para Xamarin. Forme.
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2019
no-loc:
- Xamarin
- Xamarin.Forms
- Xamarin.Android
- Xamarin.Essentials
- Xamarin.iOS
- Xamarin.Mac
ms.openlocfilehash: d12daa358917399fc5fd1febf02d4f96a647f360
ms.sourcegitcommit: 6f09bc2b760e76a61a854f55d6a87c4f421ac6c8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2020
ms.locfileid: "75607859"
---
# <a name="opno-locxamarinforms-requirements"></a>Xamarin. Requisitos de formularios

_Requisitos del sistema de plataforma y desarrollo para Xamarin. Forme._

Consulte el artículo [Instalación](installation/index.md) para obtener información general de las prácticas de instalación y configuración que se aplican entre plataformas.

## <a name="target-platforms"></a>Plataformas de destino

Xamarin. Las aplicaciones de formularios se pueden escribir para los siguientes sistemas operativos:

- iOS 9 o superior
- Android 4.4 (API 19) o versiones posteriores ([más detalles](#android))
- Plataforma universal de Windows de Windows 10 ([más detalles](#windows10))

Sin embargo, se recomienda Android 5,0 (API 21) como la API mínima. Esto garantiza la compatibilidad total con todas las bibliotecas de compatibilidad de Android, a la vez que se destina a la mayoría de los dispositivos Android.

Se supone que los desarrolladores están familiarizados con [.net Standard](~/cross-platform/app-fundamentals/net-standard.md).

### <a name="additional-platform-support"></a>Compatibilidad con plataforma adicional

El estado de estas plataformas está disponible en el [Xamarin. Formularios GitHub](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support):

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

Xamarin. Las aplicaciones de Forms se pueden desarrollar en macOS y Windows. Sin embargo, Windows y Visual Studio son necesarios para producir versiones de Windows de la aplicación.

## <a name="mac-system-requirements"></a>Requisitos del sistema de equipos Mac

Puede usar Visual Studio para Mac para desarrollar Xamarin. Aplicaciones de Forms en macOS High Sierra (10,13) o posterior. Para desarrollar aplicaciones de iOS, se recomienda usar la versión más reciente de Xcode, iOS y macOS. Para conocer los requisitos de versión específicos, consulte las notas de la [versión deXamarin. iOS](/xamarin/ios/release-notes/)más recientes.

> [!NOTE]
> Las aplicaciones de Windows no se pueden desarrollar en macOS.

## <a name="windows-system-requirements"></a>Requisitos del sistema de equipos Windows

Xamarin. Las aplicaciones de Forms para iOS y Android se pueden crear en cualquier instalación de Windows que admita el desarrollo de Xamarin. Para obtener compatibilidad total con las características actuales de la plataforma, use la versión más reciente de Visual Studio. 

Un equipo Mac en red es necesario para el desarrollo de iOS con la versión más reciente de Xcode y la versión mínima de macOS que especifica Apple.

<a name="windows10" />

### <a name="universal-windows-platform-uwp"></a>Plataforma universal de Windows (UWP)

Desarrollo de Xamarin. Aplicaciones de Forms para UWP requiere:

- Windows 10 (se recomienda la última versión, el mínimo de creadores de la actualización)

- Se recomienda Visual Studio 2019

- [SDK de Windows 10](https://dev.windows.com/downloads/windows-10-sdk)

Puede [Agregar una aplicación plataforma universal de Windows (UWP)](~/xamarin-forms/platform/windows/installation/index.md) a un Xamarinexistente. Solución de formularios en cualquier momento.

## <a name="deprecated-platforms"></a>Plataformas desusadas

Estas plataformas no se admiten cuando se usa Xamarin. Formularios 3,0 o versiones más recientes:

- *Windows 8.1 / Windows Phone 8.1 WinRT*
- *Windows Phone 8 Silverlight*
