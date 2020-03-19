---
title: Plataformas compatibles con Xamarin.Forms
description: Requisitos de sistema de desarrollo y plataformas para Xamarin.Forms.
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2020
ms.openlocfilehash: 5751983bf4a818e8ee91af98b0c05658efaca8b2
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79303622"
---
# <a name="xamarinforms-supported-platforms"></a>Plataformas compatibles con Xamarin.Forms

Las aplicaciones de Xamarin.Forms pueden escribirse para los siguientes sistemas operativos:

- iOS 9 o versiones posteriores.
- Android 4.4 (API 19) o versiones posteriores ([más detalles](#android-platform-support)). Pero se recomienda Android 5.0 (API 21) como la API mínima. Esto garantiza la compatibilidad total con todas las bibliotecas de compatibilidad de Android, a la vez que se destina a la mayoría de los dispositivos Android.
- Plataforma universal de Windows de Windows 10.

Las aplicaciones Xamarin.Forms para iOS, Android y la plataforma universal de Windows (UWP) se pueden compilar en Visual Studio. Pero se necesita un equipo Mac en red para el desarrollo de iOS con la última versión de Xcode y la versión mínima de macOS que especifica Apple. Para más información, vea [Requisitos de Windows](~/cross-platform/get-started/requirements.md#windows-requirements).

Las aplicaciones Xamarin.Forms para iOS y Android se pueden compilar en Visual Studio para Mac. Para más información, vea [Requisitos de macOS](~/cross-platform/get-started/requirements.md#macos-requirements).

> [!NOTE]
> Para desarrollar aplicaciones con Xamarin.Forms es necesario estar familiarizado con [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md).

## <a name="additional-platform-support"></a>Compatibilidad con plataforma adicional

Xamarin.Forms admite otras plataformas además de iOS, Android y Windows:

- Samsung Tizen
- macOS
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

Cuando se usa Xamarin.Forms 3.0 o una versión posterior no se admiten estas plataformas:

- *Windows 8.1 / Windows Phone 8.1 WinRT*
- *Windows Phone 8 Silverlight*
