---
title: Plataformas admitidas para Xamarin.Forms
description: Requisitos de sistema de desarrollo y plataformas para Xamarin.Forms.
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f93af19587cf962ac0c852599157261a087dadbc
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84197546"
---
# <a name="xamarinforms-supported-platforms"></a>Plataformas admitidas para Xamarin.Forms

Las aplicaciones en Xamarin.Forms se pueden escribir para los siguientes sistemas operativos:

- iOS 9 o versiones posteriores.
- Android 4.4 (API 19) o versiones posteriores ([más detalles](#android-platform-support)). Pero se recomienda Android 5.0 (API 21) como la API mínima. Esto garantiza la compatibilidad total con todas las bibliotecas de compatibilidad de Android, a la vez que se destina a la mayoría de los dispositivos Android.
- Plataforma universal de Windows de Windows 10.

Las aplicaciones en Xamarin.Forms para iOS, Android y la Plataforma universal de Windows (UWP) se pueden compilar en Visual Studio. Pero se necesita un equipo Mac en red para el desarrollo de iOS con la última versión de Xcode y la versión mínima de macOS que especifica Apple. Para más información, vea [Requisitos de Windows](~/cross-platform/get-started/requirements.md#windows-requirements).

Las aplicaciones en Xamarin.Forms para iOS y Android se pueden compilar en Visual Studio para Mac. Para más información, vea [Requisitos de macOS](~/cross-platform/get-started/requirements.md#macos-requirements).

> [!NOTE]
> Para desarrollar aplicaciones con Xamarin.Forms es necesario familiarizarse con [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md).

## <a name="additional-platform-support"></a>Compatibilidad con plataforma adicional

Xamarin.Forms admite otras plataformas además de iOS, Android y Windows:

- Samsung Tizen
- macOS 10.13 o posterior
- GTK#
- WPF

El estado de estas plataformas está disponible en la [wiki de compatibilidad con la plataforma de GitHub para Xamarin.Forms](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support).

## <a name="android-platform-support"></a>Compatibilidad con la plataforma Android

Debe tener instalada la plataforma más reciente de Android SDK Tools y de la API de Android. Puede actualizar a las versiones más recientes con [Android SDK Manager](~/android/get-started/installation/android-sdk.md).

Además, la versión de compilación o de destino de los proyectos de Android **debe** establecerse en *Usar la última plataforma instalada*, aunque la versión mínima se puede establecer en API 19 para que pueda seguir admitiendo dispositivos que usen Android 4.4 y versiones más recientes. Estos valores se establecen en **Opciones del proyecto**:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

**Opciones de proyecto > Aplicación > Propiedades de la aplicación**

![Opciones de compilación de Android en Visual Studio](requirements-images/options-android-vs-sml.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

**Compilar > General**

![Selección de la plataforma de destino más reciente](requirements-images/options-general-sml.png)

**Compilar > Aplicación de Android**

![Selección de las versiones de Android mínima y de destino para la aplicación](requirements-images/options-android-sml.png)

-----

## <a name="deprecated-platforms"></a>Plataformas en desuso

No se admiten estas plataformas cuando se usa Xamarin.Forms 3.0 o una versión más reciente:

- *Windows 8.1 / Windows Phone 8.1 WinRT*
- *Windows Phone 8 Silverlight*
