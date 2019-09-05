---
title: Instalación de Xamarin.iOS en Windows
description: En este documento se describe cómo configurar un equipo Windows, como configurar un host de compilación de Mac y cómo emparejar el equipo Windows con el host de compilación de Mac para el desarrollo de Xamarin.iOS.
zone_pivot_groups: platform-win
ms.prod: xamarin
ms.assetid: abf85d3e-a365-44a2-b1a4-6c572c7f76dd
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/16/2018
ms.openlocfilehash: 53f244521e4b916078bdcbaa02f1a79a919be0c4
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291304"
---
# <a name="installing-xamarinios-on-windows"></a>Instalación de Xamarin.iOS en Windows

_En este artículo, se describe cómo configurar una máquina de Windows y un host de compilación de Mac para el desarrollo de Xamarin.iOS._

::: zone pivot="windows"

## <a name="overview"></a>Información general

Para compilar aplicaciones de Xamarin.iOS con Visual Studio 2019 en Windows, necesitará:

- Una máquina de Windows con Visual Studio 2019 instalado. Puede ser una máquina virtual o física.

  - [Requisitos del sistema de equipos Windows](~/cross-platform/get-started/requirements.md#windows-requirements)

- Un equipo Mac accesible desde la red configurado con las herramientas de compilación de Apple y Xamarin.iOS. Visual Studio 2019 obtiene acceso a esta máquina a través de una conexión de red para usar las herramientas de compilación de Apple, que son necesarias para compilar aplicaciones nativas de iOS.

  - [Requisitos del sistema de equipos Mac](~/cross-platform/get-started/requirements.md#macos-requirements)

  > [!TIP]
  > ¿No tiene acceso a un equipo Mac?
  >
  > Si no tiene acceso a un equipo Mac, puede usar [MacinCloud](https://www.macincloud.com/pages/visual-studio-mac.html) o [MacStadium](https://www.macstadium.com/); ambos servicios proporcionan hardware de Mac en la nube, que puede usar para compilar proyectos de Xamarin.iOS.

## <a name="setup"></a>Programa de instalación

Para configurar el equipo para el desarrollo de Xamarin.iOS en Visual Studio 2019, siga estos pasos:

1. Configurar Windows (instalar Visual Studio 2019)

    Xamarin.iOS funciona con las ediciones de Visual Studio 2019 Community, Professional y Enterprise en una máquina virtual o independiente.

    - [Instale Visual Studio 2019](~/get-started/installation/windows.md).

2. Configurar un equipo Mac (instalar Xcode y Visual Studio para Mac)

    Para compilar, depurar y firmar aplicaciones de iOS para distribuirlas, Visual Studio 2017 debe tener acceso de red a un host de compilación de Mac configurado con las herramientas de desarrollo de Apple (Xcode) y Xamarin.iOS.

    - [Descargue e instale Xcode desde el App Store de Mac](https://itunes.apple.com/us/app/xcode/id497799835?mt=12).
    - [Instale Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/installation), que también instala Xamarin.iOS.

    > [!NOTE]
    > Si prefiere no instalar Visual Studio para Mac, Visual Studio 2019 puede [configurar automáticamente](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning) el host de compilación de Mac con el software necesario para compilar aplicaciones de Xamarin.iOS.
    > Para obtener más información, consulte [Aprovisionamiento automático de Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning).

3. Emparejar con Mac (conectar Visual Studio 2019 con el equipo Mac)

    Para que Visual Studio 2019 use las herramientas de compilación de iOS en el equipo Mac, los dos equipos deben estar conectados a través de una red.

    - [Lea la guía Emparejar con Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

::: zone-end
::: zone pivot="win-vs2017"

## <a name="overview"></a>Información general

Para compilar aplicaciones de Xamarin.iOS con Visual Studio 2017 en Windows, necesitará:

- Una máquina de Windows con Visual Studio 2017 instalado. Puede ser una máquina virtual o física.
  - [Requisitos del sistema de equipos Windows](~/cross-platform/get-started/requirements.md#windows-requirements)

- Un equipo Mac accesible desde la red configurado con las herramientas de compilación de Apple y Xamarin.iOS. Visual Studio 2017 accede a esta máquina a través de una conexión de red para usar las herramientas de compilación de Apple, que son necesarias para compilar aplicaciones nativas de iOS.
  - [Requisitos del sistema de equipos Mac](~/cross-platform/get-started/requirements.md#macos-requirements)

## <a name="setup"></a>Programa de instalación

Para configurar el equipo para el desarrollo de Xamarin.iOS en Visual Studio 2017, siga estos pasos:

1. Configurar Windows (instalar Visual Studio 2017)

    Xamarin.iOS funciona con las ediciones de Visual Studio 2017 Community, Professional y Enterprise en una máquina virtual o independiente.

    - [Instale Visual Studio 2017](~/get-started/installation/windows.md).

2. Configurar un equipo Mac (instalar Xcode y Visual Studio para Mac)

    Para compilar, depurar y firmar aplicaciones de iOS para distribuirlas, Visual Studio 2017 debe tener acceso de red a un host de compilación de Mac configurado con las herramientas de desarrollo de Apple (Xcode) y Xamarin.iOS.

    - [Descargue e instale Xcode desde el App Store de Mac](https://itunes.apple.com/us/app/xcode/id497799835?mt=12).
    - [Instale Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/installation), que también instala Xamarin.iOS.

    > [!NOTE]
    > Si prefiere no instalar Visual Studio para Mac, a partir de [Visual Studio 2017 versión 15.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning), Visual Studio 2017 puede configurar automáticamente el host de compilación de Mac con el software necesario para compilar aplicaciones de Xamarin.iOS. Para obtener más información, consulte [Aprovisionamiento automático de Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning).

3. Emparejar con Mac (conectar Visual Studio 2017 con el equipo Mac)

    Para que Visual Studio 2017 use las herramientas de compilación de iOS en el equipo Mac, los dos equipos deben estar conectados a través de una red.

    - [Lea la guía Emparejar con Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

::: zone-end

## <a name="summary"></a>Resumen

En este artículo, se ha descrito cómo configurar una máquina de Windows y su host de compilación de Mac asociado para el desarrollo de Xamarin.iOS.

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a Xamarin.iOS para Visual Studio](introduction-to-xamarin-ios-for-visual-studio.md)
- [Configuración de Visual Studio para el desarrollo de iOS](config-options.md)
- [Aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md)
