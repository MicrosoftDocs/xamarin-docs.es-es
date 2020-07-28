---
title: Generar perfiles de aplicaciones de Xamarin.iOS con Instruments
description: En este documento se describe cómo usar la aplicación Instruments de Apple para generar perfiles de una aplicación Xamarin.iOS instalada en un dispositivo o un simulador.
ms.prod: xamarin
ms.assetid: 70A8CAC8-20C2-655B-37C3-ACF9EA7874D8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: c7cc3c74f5a26ec7e07636ebab865e9016409f89
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939183"
---
# <a name="profiling-xamarinios-applications-with-instruments"></a>Generar perfiles de aplicaciones de Xamarin.iOS con Instruments

Xcode **Instruments** es una herramienta que se puede usar para generar perfiles de aplicaciones de Xamarin.iOS en un dispositivo o en el simulador. Mono usa su modelo Just-In-Time para compilar el código e Instruments no interpreta bien este tipo de datos, por lo que puede ser difícil trabajar con los resultados de aplicaciones basadas en el simulador que usan Instruments.
Debido a este problema, esta guía se centrará en cómo usar la aplicación de desarrollador para interpretar resultados de Instruments de este documento.

## <a name="requirements"></a>Requisitos

Xcode Instruments solo se ejecuta en equipos Mac.

## <a name="opening-the-instruments-app"></a>Abrir la aplicación Instruments

Seleccione el dispositivo y ejecute la aplicación Instruments:

1. Abra el proyecto de Xamarin.iOS en Visual Studio para Mac.
2. Seleccione la configuración **Depurar|iPhone**.
3. Conecte un dispositivo iOS al equipo.
4. En el menú **Ejecutar**, seleccione **Upload to Device** (Cargar en el dispositivo). Se ha compilado la aplicación y se ha cargado en el dispositivo.
5. En el menú **Herramientas**, seleccione **Launch Instruments** (Iniciar Instruments).

Instruments se abrirá y mostrará el cuadro de diálogo siguiente:

 [![Elección de una plantilla de generación de perfiles](using-instruments-to-detect-native-leaks-using-markheap-images/instruments1.png)](using-instruments-to-detect-native-leaks-using-markheap-images/instruments1.png#lightbox)

Haga clic para seleccionar la plantilla **Asignaciones**. Las otras plantillas son válidas,pero en este artículo solo se trata la plantilla de perfil **Asignaciones**.

Después, seleccione el dispositivo y la aplicación mediante el menú de la parte superior de la ventana:

[![Selección del dispositivo y la aplicación](using-instruments-to-detect-native-leaks-using-markheap-images/instruments2.png)](using-instruments-to-detect-native-leaks-using-markheap-images/instruments2.png#lightbox)

El dispositivo iOS debe estar seleccionado en el menú situado en la parte superior de la ventana y la aplicación de la que se va a generar el perfil debe estar seleccionada junto a él (**MemoryDemo** en la captura de pantalla anterior).

Si el dispositivo no aparece en el menú, compruebe la **Consola** en Visual Studio para Mac por si aparece algún mensaje de error cuando se implementa la aplicación en el dispositivo. Además, asegúrese de que el dispositivo se haya aprovisionado para el desarrollo a través de Xcode Organizer.

Haga clic en el botón **Elegir** y debería aparecer la pantalla siguiente:

[Interfaz de generación de perfiles![](using-instruments-to-detect-native-leaks-using-markheap-images/instruments3.png)](using-instruments-to-detect-native-leaks-using-markheap-images/instruments3.png#lightbox)

Haga clic en el botón Grabar (círculo rojo en la parte superior izquierda) para iniciar la generación del perfil.

En la siguiente captura de pantalla se muestra un ejemplo de generación de perfil con **Instruments**:

[![Ejemplo de generación de perfiles con Instruments](using-instruments-to-detect-native-leaks-using-markheap-images/instruments4.png)](using-instruments-to-detect-native-leaks-using-markheap-images/instruments4.png#lightbox)

## <a name="summary"></a>Resumen

En esta guía, se le ha explicado cómo iniciar Xcode Instruments para supervisar una aplicación de iOS de Visual Studio para Mac. Continúe con el [Tutorial de Instruments](~/ios/deploy-test/walkthrough-apples-instrument.md) para obtener un ejemplo de cómo diagnosticar un problema de memoria con Instruments.

## <a name="related-links"></a>Vínculos relacionados

- [Tutorial de Instruments](~/ios/deploy-test/walkthrough-apples-instrument.md)
- [Recolección de elementos no utilizados de Xamarin.iOS (entrada de blog)](https://c-sharx.net/2015-04-27-xamarin-ios-the-garbage-collector-and-me/)
