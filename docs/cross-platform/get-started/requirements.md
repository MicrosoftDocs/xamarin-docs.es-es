---
title: Requisitos del sistema
description: En este documento se enumeran los requisitos del sistema para compilar aplicaciones con Xamarin en equipos Mac y Windows. Además, contiene vínculos a instrucciones de instalación.
ms.prod: xamarin
ms.assetid: dd344d57-18e2-42a5-8c15-3f5be4123c72
author: conceptdev
ms.author: crdun
ms.date: 04/26/2018
ms.openlocfilehash: aea82b5946d12bc75aef18953d61cd88e5329329
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511384"
---
# <a name="system-requirements"></a>Requisitos del sistema

Los productos de Xamarin dependen de los SDK de la plataforma de Apple y Google para fijar como destino iOS o Android para que nuestros requisitos del sistema coincidan con los suyos. Esta página describe la compatibilidad del sistema para la plataforma Xamarin, y el entorno de desarrollo y versiones del SDK recomendados.

Eche un vistazo a las [instrucciones de instalación](#installation-instructions) para obtener más información sobre cómo obtener el software y los SDK necesarios.

## <a name="development-environments"></a>Entornos de desarrollo

Esta tabla muestra qué plataformas se pueden crear con diferentes combinaciones de sistema operativo y herramientas de desarrollo:

[!include[](~/cross-platform/includes/development-environment.md)]

> [!NOTE]
> Para desarrollar para iOS en equipos Windows debe haber un [equipo Mac accesible en la red](~/ios/get-started/installation/windows/connecting-to-mac/index.md), para la depuración y compilación remota. Esto también funciona si ejecuta Visual Studio dentro de una máquina virtual de Windows en un equipo Mac.

## <a name="macos-requirements"></a>Requisitos de macOS

El uso de un equipo Mac para el desarrollo de Xamarin requiere las siguientes versiones de software o del SDK. Compruebe la versión de sistema operativo y siga las instrucciones para el [instalador de Xamarin](#installation-instructions).

[!include[](~/cross-platform/includes/macos-requirements.md)]

> [!NOTE]
> Xcode se puede instalar (y actualizar) en [developer.apple.com](https://developer.apple.com/xcode/download/) o a través de Mac App Store.

### <a name="testing--debugging-on-macos"></a>Pruebas y depuración en macOS

- Se pueden implementar aplicaciones Xamarin para dispositivos móviles en dispositivos físicos a través de USB para probar y depurar (las aplicaciones de Apple Watch se implementan en primer lugar en el iPhone emparejado).
- Las aplicaciones de Xamarin.Mac pueden probarse directamente en el equipo de desarrollo.

[!include[](~/cross-platform/includes/macos-testing.md)]

> [!WARNING]
> Xamarin.Mac 4.8 solo admite macOS 10.9 (Mavericks) o superior.
> Las versiones anteriores de Xamarin.Mac son compatibles con macOS 10.7 o versiones posteriores, pero estas versiones anteriores de macOS carecen de infraestructura TLS suficiente para admitir TLS 1.2. Para macOS 10.7 o Mac OS 10.8 de destino, use Xamarin.Mac 4.6 o una versión anterior.

## <a name="windows-requirements"></a>Requisitos de Windows

El uso de un equipo Windows para el desarrollo de Xamarin requiere las siguientes versiones de software o del SDK.
Compruebe la versión del sistema operativo (y confirme que no está usando una versión *Express* de Visual Studio; si es así, considere la posibilidad de actualizar a una edición *Community*).
El instalador de Visual Studio 2019 y Visual Studio 2017 incluye una opción para instalar Xamarin automáticamente (la carga de trabajo de **Desarrollo para dispositivos móviles con .NET**).

[!include[](~/cross-platform/includes/windows-requirements.md)]

> [!NOTE]
> - Xamarin para Visual Studio admite cualquier edición de Visual Studio 2019 o Visual Studio 2017 (Community, Professional y Enterprise).
> - Para desarrollar aplicaciones de Xamarin.Forms para la plataforma universal de Windows (UWP) es necesario Windows 10 con Visual Studio 2017. Se recomienda Visual Studio 2019.

### <a name="testing--debugging-on-windows"></a>Pruebas y depuración en Windows

Las aplicaciones móviles Xamarin se pueden implementar en dispositivos físicos a través de USB o inalámbricamente para probar y depurar (los dispositivos iOS deben estar conectados al equipo Mac, no al equipo que ejecuta Visual Studio).

[!include[](~/cross-platform/includes/windows-testing.md)]

## <a name="installation-instructions"></a>Instrucciones de instalación

Puede descargar con [Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/installation) la versión más reciente de Xamarin para macOS. Para Windows, siga las [instrucciones de instalación de Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio).

En la [página de novedades](~/whats-new/index.yml) encontrará una lista completa de las versiones de producto actuales. Esta página también incluye vínculos a las notas de la versión.

Aquí están disponibles instrucciones de [instalación](~/get-started/installation/index.md) específicas para cada plataforma:

- [Xamarin.iOS](~/ios/get-started/installation/index.md)
- [Xamarin.Android](~/android/get-started/installation/index.md)
- [Xamarin.Mac](~/mac/get-started/installation.md)

También hay información adicional sobre [las plataformas admitidas y los requisitos de Xamarin.Forms](~/get-started/requirements.md).

## <a name="related-links"></a>Vínculos relacionados

- [Descargar Xamarin](https://visualstudio.microsoft.com/xamarin/)
- [Notas de la versión de Xamarin.Forms](/xamarin/xamarin-forms/release-notes/)
- [Notas de la versión de Xamarin.Android](/xamarin/android/release-notes/)
- [Notas de la versión de Xamarin.iOS](/xamarin/ios/release-notes/)
