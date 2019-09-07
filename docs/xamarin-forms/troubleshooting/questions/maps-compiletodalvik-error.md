---
title: ¿Por qué se produce un ERROR en el proyecto de Android de Xamarin. Forms. Maps con COMPILETODALVIK de nivel superior inesperado?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C0251EB1-F509-47AD-98D6-846AF46425E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 2872cc7b54e26d07b388f08d650048e8d3861930
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759967"
---
# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>¿Por qué se produce un ERROR en el proyecto de Android de Xamarin. Forms. Maps con COMPILETODALVIK de nivel superior inesperado?

Este error puede aparecer en el panel de errores de Visual Studio para Mac o en la ventana de salida de la compilación de Visual Studio; en los proyectos de Android con Xamarin. Forms. Maps.

Esto se resuelve normalmente aumentando el tamaño del montón de Java para el proyecto de Xamarin. Android. Siga estos pasos para aumentar el tamaño del montón:

## <a name="visual-studio"></a>Visual Studio

1. Haga clic con el botón derecho en el proyecto de Android & abrir las opciones del proyecto.
2. Vaya a **Opciones de Android-> avanzado**
3. En el cuadro de texto tamaño del montón de Java escriba 1G.
4. Recompile el proyecto.

![Captura de pantalla de las opciones de proyecto de Visual Studio](maps-compiletodalvik-error-images/vsjavaheap.png "Opciones de compilación de Android en Visual Studio")

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. Haga clic con el botón derecho en el proyecto de Android & abrir las opciones del proyecto.
2. Vaya a **compilación-> Android Build-> avanzado**
3. En el cuadro de texto tamaño del montón de Java escriba 1G.
4. Recompile el proyecto.  

![Captura de pantalla de las opciones del proyecto Visual Studio para Mac](maps-compiletodalvik-error-images/xsjavaheap.png "Opciones de compilación de Android en Visual Studio para Mac")
