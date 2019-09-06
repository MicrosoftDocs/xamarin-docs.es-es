---
title: Simulador remoto de iOS para Windows
description: El simulador remoto de iOS para Windows le permite probar las aplicaciones en un simulador de iOS que se ejecuta en Windows junto con Visual Studio 2019.
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: conceptdev
ms.author: crdun
ms.date: 04/26/2019
ms.openlocfilehash: 3067493315c62c46f44fb94aa61a919e1449080b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289709"
---
# <a name="remoted-ios-simulator-for-windows"></a>Simulador remoto de iOS para Windows

El simulador remoto de iOS para Windows le permite probar las aplicaciones en un simulador de iOS que se ejecuta en Windows, junto con Visual Studio 2019 y Visual Studio 2017.

[![simulador remoto de iOS que se ejecuta en Windows](images/hero-sml.png "simulador remoto de iOS que se ejecuta en Windows")](images/hero.png#lightbox)

## <a name="getting-started"></a>Introducción

El simulador remoto de iOS para Windows se instala automáticamente como parte de Xamarin en Visual Studio 2019 y Visual Studio 2017. Para usarlo, siga estos pasos:

1. [Emparejar Visual 2019 a un host de compilación de Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. En Visual Studio, inicie la depuración de un proyecto de iOS o tvOS. El simulador remoto de iOS para Windows aparecerá automáticamente en el equipo Windows.

Inspecione [este vídeo](deploy.md) para obtener una guía paso a paso.

## <a name="simulator-window"></a>Ventana del simulador

La barra de herramientas situada en la parte superior de la ventana del simulador contiene varios botones útiles:

- **Inicio** : simula el botón Inicio de un dispositivo iOS.
- **Bloqueo** : bloquea el simulador (pasar el dedo para desbloquear).
- **Captura de pantalla** : guarda una captura de pantalla del simulador (almacenado en el **\\simulador Pictures\Xamarin\iOS**).
- [**Configuración** ](#settings) : muestra el teclado, ubicación y otras opciones.
- [**Otras opciones** ](#other-options) : abrirá varias opciones del simulador, como rotación, gestos de agitar y Touch Id.

    [![Ejemplo de mapas en el simulador de iOS](images/maps-app-sml.png "Ejemplo de mapas en el simulador de iOS")](images/maps-app.png#lightbox)

## <a name="settings"></a>Configuración

Al hacer clic en el icono de engranaje de la barra de herramientas, se abre la ventana de **configuración**:

[![Configuración del simulador de iOS](images/settings-sml.png "Configuración del simulador de iOS")](images/settings.png#lightbox)

Estas opciones permiten habilitar el teclado de hardware, elegir una ubicación que el dispositivo debe notificar (se admiten ubicaciones estáticas y móviles), habilitar Touch ID y restablecer el contenido y la configuración del simulador.

## <a name="other-options"></a>Otras opciones

El botón de puntos suspensivos de la barra de herramientas muestra otras opciones, como la rotación, gestos de agitar y el reinicio del sistema. Estas mismas opciones pueden verse como una lista haciendo clic con el botón derecho en cualquier lugar de la ventana del simulador:

[![Configuración adicional del simulador de iOS](images/more-sml.png "Configuración adicional del simulador de iOS")](images/more.png#lightbox)

## <a name="touchscreen-support"></a>Compatibilidad con la pantalla táctil

Los equipos Windows más modernos tienen pantallas táctiles. Gracias a que el simulador remoto de iOS para Windows es compatible con las interacciones táctiles, puede probar la aplicación con los mismos gestos táctiles de reducir, deslizar el dedo y con varios dedos que usa con los dispositivos iOS físicos.

De forma similar, el simulador remoto de iOS para Windows procesa la entrada de lápiz de Windows como entrada de lápiz de Apple.

## <a name="sound-handling"></a>Control de sonido

Los sonidos reproducidos por el simulador procederán de los altavoces del equipo Mac del host.
los sonidos de iOS no se escuchan en el equipo Windows.

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>Deshabilitar el simulador remoto de iOS para Windows

Para deshabilitar el simulador remoto de iOS para Windows, vaya a **Herramientas > Opciones > Xamarin > Configuración de iOS** y desactive la opción **Simulador Remoto en Windows**.

[![Casilla de verificación para usar el simulador](images/options-sml.png "Casilla de verificación para usar el simulador")](images/options.png#lightbox)

Con esta opción deshabilitada, el proceso de depuración abre el simulador de iOS en el host de compilación de Mac conectado.
