---
title: ¿Por qué mi Xamarin.Forms . Se produce un ERROR en el proyecto de Android de Maps con COMPILETODALVIK de nivel superior inesperado?
ms.topic: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e29535e71cb77b05da41c043c6fd932ae4f5ce95
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135855"
---
# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>¿Por qué mi Xamarin.Forms . Se produce un ERROR en el proyecto de Android de Maps con COMPILETODALVIK de nivel superior inesperado?

Este error puede aparecer en el panel de errores de Visual Studio para Mac o en la ventana de salida de la compilación de Visual Studio; en los proyectos de Android con Xamarin.Forms . Maps.

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
