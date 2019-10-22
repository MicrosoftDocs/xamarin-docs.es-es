---
title: Simulador de iOS remoto para Windows
description: El simulador de iOS remoto para Windows le permite probar sus aplicaciones en un simulador de iOS mostrado en Windows junto con Visual Studio 2019.
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: conceptdev
ms.author: crdun
ms.date: 04/26/2019
ms.openlocfilehash: 3067493315c62c46f44fb94aa61a919e1449080b
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "70289709"
---
# <a name="remoted-ios-simulator-for-windows"></a>Simulador de iOS remoto para Windows

El simulador de iOS remoto para Windows le permite probar sus aplicaciones en un simulador de iOS mostrado en Windows junto con Visual Studio 2019 y Visual Studio 2017.

[![simulador de iOS que se ejecuta en Windows](images/hero-sml.png "simulador de iOS que se ejecuta en Windows")](images/hero.png#lightbox)

## <a name="getting-started"></a>Introducción

El simulador de iOS remoto para Windows se instala automáticamente como parte de Xamarin en Visual Studio 2019 y Visual Studio 2017. Para usarlo, siga estos pasos:

1. [Emparejar Visual 2019 con un host de compilación de Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. En Visual Studio, inicie la depuración de un proyecto de iOS o tvOS. El simulador de iOS remoto para Windows aparecerá en el equipo Windows.

Vea [este vídeo](deploy.md) para obtener una guía paso a paso.

## <a name="simulator-window"></a>Ventana del simulador

La barra de herramientas situada en la parte superior de la ventana del simulador contiene varios botones útiles:

- **Inicio** : simula el botón Inicio en un dispositivo iOS.
- **Lock** : bloquea el simulador (Deslice el dedo hasta el desbloqueo).
- **Captura de pantalla** : guarda una captura de pantalla del simulador (almacenado en **Pictures\Xamarin\iOS Simulator \\** ).
- [**Configuración**](#settings) : muestra el teclado, la ubicación y otros valores de configuración.
- [**Otras opciones**](#other-options) : muestra varias opciones del simulador, como la rotación, los gestos de sacudidos y el ID. de toque.

    [![ejemplo de mapas del simulador de iOS](images/maps-app-sml.png "ejemplo de mapas del simulador de iOS")](images/maps-app.png#lightbox)

## <a name="settings"></a>Configuración

Al hacer clic en el icono de engranaje de la barra de herramientas, se abre la ventana **configuración** :

[![configuración del simulador de iOS](images/settings-sml.png "configuración del simulador de iOS")](images/settings.png#lightbox)

Esta configuración permite habilitar el teclado de hardware, elegir una ubicación que el dispositivo debe notificar (se admiten las ubicaciones estáticas y en movimiento), habilitar Touch ID y restablecer el contenido y la configuración del simulador.

## <a name="other-options"></a>Otras opciones

El botón de puntos suspensivos de la barra de herramientas revela otras opciones, como la rotación, los gestos de agitar y el reinicio. Estas mismas opciones se pueden ver como una lista haciendo clic con el botón derecho en cualquier parte de la ventana del simulador:

[![configuración adicional del simulador de iOS](images/more-sml.png "configuración adicional del simulador de iOS")](images/more.png#lightbox)

## <a name="touchscreen-support"></a>Compatibilidad con la pantalla táctil

La mayoría de los equipos Windows modernos tienen pantallas táctiles. Dado que el simulador de iOS remoto para Windows admite interacciones táctiles, puede probar la aplicación con los mismos gestos de toque, deslizar y tocar múltiples dedos que usa con dispositivos iOS físicos.

Del mismo modo, el simulador de iOS remoto para Windows trata la entrada de Windows Stylus como entrada de Apple Pencil.

## <a name="sound-handling"></a>Control de sonido

Los sonidos reproducidos por el simulador procederán de los altavoces del equipo Mac del host.
los sonidos de iOS no se escuchan en el equipo Windows.

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>Deshabilitación del simulador de iOS remoto para Windows

Para deshabilitar el simulador de iOS remoto para Windows, vaya a **herramientas > opciones > Xamarin > configuración de iOS** y desactive el **simulador remoto en Windows**.

[![casilla para usar el simulador](images/options-sml.png "casilla para usar el simulador")](images/options.png#lightbox)

Con esta opción deshabilitada, la depuración abre el simulador de iOS en el host de compilación de Mac conectado.
