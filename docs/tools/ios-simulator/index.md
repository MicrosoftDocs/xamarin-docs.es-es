---
title: Simulador de iOS remoto para Windows
description: El simulador de iOS remoto para Windows permite probar sus aplicaciones en un simulador de iOS mostrado en Windows junto con Visual Studio 2019.
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: davidortinau
ms.author: daortin
ms.date: 04/26/2019
ms.openlocfilehash: 826fb2bbfb202507bf4ffe062004fb0137e19176
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456670"
---
# <a name="remoted-ios-simulator-for-windows"></a>Simulador de iOS remoto para Windows

El simulador de iOS remoto para Windows permite probar sus aplicaciones en un simulador de iOS mostrado en Windows junto con Visual Studio 2019 y Visual Studio 2017.

[![Simulador de iOS en ejecución en Mac](images/hero-sml.png "Simulador de iOS en ejecución en Mac")](images/hero.png#lightbox)

## <a name="getting-started"></a>Introducción

El simulador de iOS remoto para Windows se instala automáticamente como parte de Xamarin en Visual Studio 2019 y Visual Studio 2017. En él, siga estos pasos:

1. [Empareje Visual Studio 2019 con un host de compilación de Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. En Visual Studio, inicie la depuración de un proyecto de iOS o tvOS. El simulador de iOS remoto para Windows aparecerá en la máquina con Windows.

Vea [este vídeo](deploy.md) para ver una guía paso a paso.

## <a name="simulator-window"></a>Ventana del simulador

La barra de herramientas situada en la parte superior de la ventana del simulador contiene varios botones útiles:

- **Inicio**: simula el botón de inicio de un dispositivo iOS.
- **Bloquear**: bloquea el simulador (Deslice el dedo para desbloquear).
- **Captura de pantalla**: guarda una captura de pantalla del simulador (almacenado en **Pictures\Xamarin\iOS Simulator\\** ).
- [**Configuración**](#settings): muestra el teclado, la ubicación y otros valores de configuración.
- [**Otras opciones**](#other-options): muestra varias opciones del simulador, como la rotación, los gestos de agitación y Touch ID.

    [![Ejemplo de mapas del simulador de iOS](images/maps-app-sml.png "Ejemplo de mapas del simulador de iOS")](images/maps-app.png#lightbox)

## <a name="settings"></a>Configuración

Al hacer clic en el icono de engranaje de la barra de herramientas, se abre la ventana **Configuración**:

[![Configuración del simulador de iOS](images/settings-sml.png "Configuración del simulador de iOS")](images/settings.png#lightbox)

Esta configuración permite habilitar el teclado de hardware, elegir una ubicación que el dispositivo debe notificar (se admiten las ubicaciones estáticas y en movimiento), habilitar Touch ID y restablecer el contenido y la configuración del simulador.

## <a name="other-options"></a>Otras opciones

El botón de puntos suspensivos de la barra de herramientas muestra otras opciones, como la rotación, los gestos de agitar y el reinicio. Estas mismas opciones se pueden ver como una lista haciendo clic con el botón derecho en cualquier parte de la ventana del simulador:

[![Configuración adicional del simulador de iOS](images/more-sml.png "Configuración adicional del simulador de iOS")](images/more.png#lightbox)

## <a name="touchscreen-support"></a>Compatibilidad con la pantalla táctil

La mayoría de los equipos con Windows modernos tienen pantallas táctiles. Dado que el simulador de iOS remoto para Windows admite interacciones táctiles, puede probar la aplicación con los mismos gestos de alejar, deslizar y varios dedos que usa con los dispositivos iOS físicos.

Del mismo modo, el simulador de iOS remoto para Windows trata la entrada de lápiz de Windows como la entrada del Apple Pencil.

## <a name="sound-handling"></a>Control de sonido

Los sonidos reproducidos por el simulador procederán de los altavoces del equipo Mac host.
Los sonidos de iOS no se escuchan en el equipo con Windows.

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>Deshabilitación del simulador de iOS remoto para Windows

Para deshabilitar el simulador de iOS remoto para Windows, vaya a **Herramientas > Opciones > Xamarin > Configuración de iOS** y desactive **Simulador remoto en Windows**.

[![Casilla para usar el simulador](images/options-sml.png "Casilla para usar el simulador")](images/options.png#lightbox)

Con esta opción deshabilitada, la depuración abre el simulador de iOS en el host de compilación de Mac conectado.

## <a name="troubleshooting"></a>Solución de problemas

Si tiene problemas con el simulador de iOS remoto, puede ver los registros en estas ubicaciones:

- **Mac**:`~/Library/Logs/Xamarin/Simulator.Server`
- **Windows**:`%LOCALAPPDATA%\Xamarin\Logs\Xamarin.Simulator`

Si [notifica un problema en Visual Studio](/visualstudio/ide/how-to-report-a-problem-with-visual-studio), puede ser útil adjuntar estos registros (hay opciones para que las cargas sean privadas).