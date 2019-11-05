---
title: Depuración de varios procesos
description: En este documento se describe cómo usar Visual Studio para Mac para depurar varios procesos que se ejecutan al mismo tiempo. Por ejemplo, esta característica puede usarse para depurar al mismo tiempo una aplicación móvil y un proyecto de servicio web.
ms.prod: xamarin
ms.assetid: 852F8AB1-F9E2-4126-9C8A-12500315C599
author: davidortinau
ms.author: daortin
ms.date: 03/24/2017
ms.openlocfilehash: fb96dab2d9979a365964d4993d9c7fc7fee299f5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016546"
---
# <a name="multi-process-debugging"></a>Depuración de varios procesos

Es muy común que las soluciones modernas desarrolladas en Visual Studio para Mac tengan varios proyectos en distintas plataformas de destino. Por ejemplo, una solución podría contener un proyecto de aplicación para dispositivos móviles basado en los datos proporcionados por un proyecto de servicio web. Al desarrollar esta solución, un desarrollador podría tener que ejecutar ambos proyectos al mismo tiempo con el fin de solucionar errores. A partir de la [versión Cycle 9 de Xamarin](https://releases.xamarin.com/stable-release-cycle-9/), ahora es posible que Visual Studio para Mac depure varios procesos que se ejecutan al mismo tiempo. Esto permite establecer puntos de interrupción, inspeccionar variables y ver los subprocesos en más de un proyecto en ejecución. Esto se conoce como _depuración de varios procesos_. 

En esta guía se tratan algunos de los cambios realizados en Visual Studio para Mac para admitir la depuración de varios procesos, cómo configurar las soluciones para depurar múltiples procesos y cómo adjuntarlos a procesos existentes con Visual Studio para Mac.

## <a name="requirements"></a>Requisitos

La depuración de varios procesos requiere Visual Studio para Mac.

## <a name="ide-changes"></a>Cambios en el IDE

Para ayudar a los desarrolladores a depurar varios procesos, Visual Studio para Mac incluye algunos cambios en la interfaz de usuario. Visual Studio para Mac tiene una **barra de herramientas de depuración** actualizada y una nueva sección **Configuración de soluciones** en la carpeta **Opciones de solución**. Además, el **panel de subprocesos** muestra ahora los procesos en ejecución y los subprocesos para cada proceso. Visual Studio para Mac también muestra varios paneles de depuración, uno para cada proceso, para ciertas cosas como **Resultado de la aplicación**.

### <a name="solution-configurations"></a>Configuraciones de soluciones

De forma predeterminada, Visual Studio para Mac mostrará un proyecto individual en el área **Configuración de soluciones** de la barra de herramientas de depuración. Al iniciar una sesión de depuración, este es el proyecto que Visual Studio para Mac iniciará y al que adjuntará el depurador.

Para iniciar y depurar varios procesos en Visual Studio para Mac, es necesario crear una _configuración de soluciones_. Una configuración de soluciones describe qué proyectos de una solución deben incluirse al iniciar una sesión de depuración con un clic en el botón **Iniciar** o al pulsar &#8984;&#8617; (**Cmd-Entrar**). La captura de pantalla siguiente es un ejemplo de una solución en Visual Studio para Mac que tiene varias configuraciones de soluciones:

![](multi-process-debugging-images/mpd01-xs.png "A solution with multiple solution configurations")

### <a name="parts-of-the-debug-toolbar"></a>Partes de la barra de herramientas de depuración

La barra de herramientas de depuración ha cambiado para permitir que se pueda seleccionar una configuración de soluciones a través de un menú emergente. En esta captura de pantalla se muestran las partes de la barra de herramientas de depuración:

![](multi-process-debugging-images/mpd02-xs.png "The parts of the debug toolbar")

1. **Configuración de soluciones**: para establecer la configuración de soluciones, haga clic en la configuración de soluciones, en la barra de herramientas de depuración, y seleccione la configuración en el menú emergente:

    ![](multi-process-debugging-images/mpd03-xs.png "A sample popup with solution configurations")

2. **Destino de compilación**: identifica el destino de compilación para los proyectos. Esto no ha cambiado con relación a versiones anteriores de Visual Studio para Mac.
3. **Destinos de dispositivo**: esta opción permite seleccionar los dispositivos en los que se va a ejecutar la solución. Es posible identificar un dispositivo o un emulador individual para cada proyecto:

    ![](multi-process-debugging-images/mpd04-xs.png "Popup showing the devices for a project")

### <a name="multiple-debug-pads"></a>Múltiples paneles de depuración

Cuando se inicia la configuración de soluciones múltiples, algunos de los paneles de Visual Studio para Mac aparecerán varias veces, una para cada proceso. Por ejemplo, en la captura de pantalla siguiente se muestran dos paneles de **Resultado de la aplicación** para una solución que está ejecutando dos proyectos:

![](multi-process-debugging-images/mpd05-xs.png "Output Pad for a solution configuration")

### <a name="multiple-processes-and-the-_active-thread_"></a>Varios procesos y el _subproceso activo_

Cuando se encuentra un punto de interrupción en un proceso, dicho proceso detendrá la ejecución, mientras que los demás procesos continúan ejecutándose. En un escenario de un solo proceso, Visual Studio para Mac puede mostrar fácilmente información como subprocesos, variables locales o resultado de la aplicación en un único conjunto de paneles. En cambio, cuando hay varios procesos con múltiples puntos de interrupción, y posiblemente varios subprocesos, puede resultar abrumador para el desarrollador hacer frente a la información de una sesión de depuración que está intentando mostrar toda la información de todos los subprocesos (y procesos) a la vez.

Para solucionar este problema, Visual Studio para Mac solo mostrará la información de un subproceso a la vez, conocido como el _subproceso activo_. El primer subproceso que se detiene en un punto de interrupción se considera el _subproceso activo_. El subproceso activo es el subproceso que es el foco de atención del desarrollador. El subproceso activo dispondrá de comandos de depuración, como **Paso a paso por procedimientos** &#8679;&#8984;O (Mayús-Cmd-O).

El **panel de subproceso** mostrará la información de todos los procesos y subprocesos que se encuentran bajo comprobación en la configuración de soluciones y proporcionará indicaciones visuales sobre cuál es el subproceso activo:

![](multi-process-debugging-images/mpd06-xs.png "Thread pad for a solution configuration")

Los subprocesos se agrupan por el proceso que los hospeda. El nombre del proyecto y el identificador del subproceso activo se muestran en negrita, y aparece una flecha hacia la derecha en el medianil junto al subproceso activo. En la captura de pantalla anterior, el **subproceso n.º 1** en el **proceso con identificador 48703** (**FirstProject**) es el subproceso activo.

Al depurar varios procesos, es posible cambiar el subproceso activo para ver la información de depuración de ese proceso (o subproceso) mediante el uso del **panel de subproceso**. Para cambiar el subproceso activo, seleccione el subproceso que quiera en el **panel de subproceso** y luego haga doble clic en él.

#### <a name="stepping-through-code-when-multiple-projects-are-stopped"></a>Ejecución paso a paso del código cuando se detienen varios proyectos

Cuando dos (o más) proyectos tienen puntos de interrupción, Visual Studio para Mac pausará ambos procesos. Solo es posible recorrer el código **paso a paso por procedimientos** en el subproceso activo. El otro proceso se pausará hasta que un cambio de ámbito haga posible que el depurador cambie el foco del subproceso activo. Por ejemplo, fíjese en la siguiente captura de pantalla en que Visual Studio para Mac depura dos proyectos:

![](multi-process-debugging-images/mpd09-xs.png  "Visual Studio for Mac debugging two projects")

En esta captura de pantalla, cada solución tiene su propio punto de interrupción. Cuando se inicia la depuración, el primer punto de interrupción que se encuentra está en la **línea 10** de `MainClass` en **SecondProject**. Dado que ambos proyectos tienen puntos de interrupción, cada proceso se detiene. Una vez que se encuentra el punto de interrupción, cada invocación del **paso a paso por procedimientos** provocará que Visual Studio para Mac recorra el código paso a paso por procedimientos en el subproceso activo.

La ejecución paso a paso del código está limitada al subproceso activo, por lo que Visual Studio para Mac recorrerá paso a paso, una línea de código a la vez, mientras que el otro proceso aún está en pausa.

Si tomamos la captura de pantalla anterior como ejemplo, cuando el bucle `for` ha finalizado, Visual Studio para Mac permitiría que **FirstProject** se ejecutara hasta que se encontrara el punto de interrupción en la **línea 11** en `MainClass`. Para cada comando de **paso a paso por procedimientos**, el depurador avanzaría línea a línea en **FirstProject**, hasta que los algoritmos heurísticos internos de Visual Studio para Mac vuelvan a cambiar el subproceso activo a **SecondProject**.

Si solo uno de los proyectos tuviera establecido un punto de interrupción, únicamente se pausaría ese proceso. El otro proyecto continuaría ejecutándose hasta que el desarrollador lo pausara o se agregara un punto de interrupción.

### <a name="pausing-and-resuming-a-processes"></a>Pausar y reanudar un proceso

Para pausar o reanudar un proceso, haga clic con el botón derecho en el proceso y seleccione **Pausar** o **Reanudar** en el menú contextual:

![](multi-process-debugging-images/mpd08-xs.png "Pause or resume in the Thread pad")

La apariencia de la barra de herramientas de depuración cambiará según el estado de los proyectos que se están depurando. Cuando se ejecuten varios proyectos, la barra de herramientas de depuración mostrará los botones **Pausar** y **Reanudar** en los casos en que haya al menos un proyecto que se esté ejecutando y un proyecto en pausa:

![](multi-process-debugging-images/mpd07-xs.png  "Debug toolbar")

Al hacer clic en el botón **Pausar** en la **barra de herramientas de depuración**, se detendrán todos los procesos que se están depurando, mientras que al hacer clic en el botón **Reanudar** se reanudarán todos los procesos en pausa.

### <a name="debugging-a-second-project"></a>Depurar un segundo proyecto

También es posible depurar un segundo proyecto una vez que Visual Studio para Mac inicia el primer proyecto. Una vez iniciado el primer proyecto, **haga clic con el botón derecho* en el proyecto, en el **Panel de solución**, y seleccione **Iniciar la depuración de elemento**:

![](multi-process-debugging-images/mpd13-xs.png  "Start Debugging Item")

## <a name="creating-a-solution-configuration"></a>Crear una configuración de soluciones

Una _configuración de soluciones_ indica a Visual Studio para Mac qué proyecto se debe ejecutar cuando se inicia una sesión de depuración con el botón **Iniciar**. Puede haber más de una configuración de soluciones por solución. Esto hace posible especificar qué proyectos se ejecutan cuando se depura el proyecto.

Para crear una nueva configuración de soluciones en Xamaring Studio:

1. Abra el cuadro de diálogo **Opciones de solución** en Visual Studio para Mac y seleccione **Ejecutar > Configuraciones**:

    ![](multi-process-debugging-images/mpd10-xs.png "Solution Configuration in the Solution Options dialog")

2. Haga clic en el botón **Nuevo**, escriba el nombre de la nueva configuración de soluciones y haga clic en **Crear**. La nueva configuración de soluciones aparecerá en la ventana **Configuraciones**:

    ![](multi-process-debugging-images/mpd11-xs.png  "Naming a new solution configuration")

3. Seleccione la nueva configuración de ejecución en la lista de configuraciones. En el cuadro de diálogo **Opciones de solución** se mostrará cada proyecto de la solución. Marque cada proyecto que debe iniciarse al iniciar una sesión de depuración:

    ![](multi-process-debugging-images/mpd12-xs.png "Selecting the project to start")

La configuración de soluciones **MultipleProjects** aparecerá ahora en la **barra de herramientas de depuración**, lo que permite que un desarrollador depure simultáneamente los dos proyectos.

## <a name="summary"></a>Resumen

En esta guía se ha tratado la depuración de varios procesos en Visual Studio para Mac. Se han detallado algunos de los cambios en el IDE para admitir la depuración de varios procesos y se han descrito algunos de los comportamientos asociados.

## <a name="related-links"></a>Vínculos relacionados

- [Notas de la versión de Xamarin Cycle 9](https://releases.xamarin.com/stable-release-cycle-9/)
