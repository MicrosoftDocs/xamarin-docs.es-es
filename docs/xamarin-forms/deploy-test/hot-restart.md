---
title: Reinicio rápido de Xamarin
description: En este documento se describe cómo configurar y usar el reinicio rápido de Xamarin para depurar una aplicación iOS.
ms.prod: xamarin
ms.assetid: 6BC62A88-9368-41BB-8494-760F2A4805DB
ms.technology: xamarin-forms
author: jimmgarrido
ms.author: jigarrid
ms.date: 01/14/2020
ms.openlocfilehash: fb607ab605322499e42f500e4f3bf08c1c267c23
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2020
ms.locfileid: "76519661"
---
# <a name="xamarin-hot-restart-preview"></a>Reinicio rápido de Xamarin (versión preliminar)

![Características en versión preliminar](~/media/shared/preview.png)

El reinicio rápido de Xamarin permite probar rápidamente los cambios en la aplicación durante el desarrollo, incluidas las ediciones de código de varios archivos, los recursos y las referencias. Inserta los cambios nuevos en el grupo de aplicaciones existente en el destino de depuración, lo que genera un ciclo de compilación e implementación mucho más rápido.

> [!NOTE]
> En este momento, el reinicio rápido de Xamarin está disponible en Visual Studio 2019 16.5 Preview y es compatible con aplicaciones iOS en las que se usa Xamarin.Forms. La compatibilidad con Visual Studio para Mac y aplicaciones que no son de Xamarin.Forms está en la hoja de ruta.

## <a name="requirements"></a>Requisitos

- Visual Studio 2019 16.5 Preview 2 o posterior
- iTunes (64 bits)
- Cuenta de desarrollador de Apple


## <a name="initial-setup"></a>Instalación inicial

1. Asegúrese de que el proyecto de iOS se establece como el proyecto de inicio y la configuración de compilación en **Depuración | iPhone**.

   1. Si se trata de un proyecto existente, vaya a **Compilar > Configuration Manager...** y asegúrese de que **Implementación** está habilitado para el proyecto de iOS.

2. Seleccione y haga clic en **Dispositivo local** en la barra de herramientas para iniciar el Asistente para la instalación:

    [![](hot-restart-images/toolbar.png "Screenshot of the Visual Studio toolbar with local device set as the debug target.")](hot-restart-images/toolbar.png)

3. Si iTunes no está instalado, haga clic en **Descargar iTunes** para descargar el instalador. Haga clic en **Siguiente** cuando se complete la instalación de iTunes.

4. Conecte un dispositivo iOS al equipo. El nombre del dispositivo aparecerá en el asistente una vez que se haya detectado. Haga clic en **Siguiente**.

5. Escriba las credenciales de la cuenta de desarrollador de Apple y haga clic en **Siguiente**.

6. Seleccione un equipo de desarrollo mediante el menú desplegable para habilitar el [ aprovisionamiento automático](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md) en el proyecto. Haga clic en **Finalizar**.

> [!NOTE]
> Se recomienda el uso del aprovisionamiento automático para que se puedan configurar fácilmente dispositivos iOS adicionales para la implementación. Pero puede deshabilitarlo y seguir usando el aprovisionamiento manual si los perfiles de aprovisionamiento correctos están presentes.

## <a name="use-xamarin-hot-restart"></a>Uso del reinicio rápido de Xamarin
Después de la instalación inicial, el dispositivo conectado aparecerá en el menú desplegable de destino de depuración. Para depurar la aplicación, seleccione el dispositivo en la lista desplegable y haga clic en el botón **Ejecutar**. Es posible que tenga que iniciar manualmente la aplicación en el dispositivo para poder iniciar la sesión de depuración.

Puede realizar modificaciones en los archivos de código durante la depuración y después presionar el botón **Reiniciar** de la barra de herramientas de depuración, o bien **Ctrl+Mayús+F5**, para reiniciar la sesión de depuración con los nuevos cambios aplicados:

[![](hot-restart-images/restart.png "Screenshot of the debug toolbar with the restart button highlighted.")](hot-restart-images/toolbar.png)

## <a name="limitations"></a>Limitaciones
- Actualmente solo se admiten aplicaciones iOS compiladas con Xamarin.Forms y dispositivos iOS.
- Los archivos de Storyboard y XIB no se admiten y es posible que la aplicación se bloquee si intenta cargarlos en tiempo de ejecución. Si los usa en la aplicación, háganoslo saber, ya que nos interesa que este escenario se admita en el futuro.
- No se admiten marcos ni bibliotecas de iOS estáticas. Es posible que vea errores en tiempo de ejecución o bloqueos si la aplicación intenta cargarlos. Pero se admiten las bibliotecas de iOS dinámicas.
- No se puede usar el reinicio rápido de Xamarin para crear paquetes de aplicaciones para la publicación. Seguirá necesitando un equipo Mac a fin de realizar una compilación, firma e implementación completas para la aplicación en producción.

## <a name="troubleshoot"></a>Solucionar problemas
- El asistente para la instalación no detectará iTunes si se ha instalado desde Microsoft Store. Primero tendrá que desinstalar esa versión y, después, descargar el [instalador desde Apple](https://go.microsoft.com/fwlink/?linkid=2101014).
- Si la aplicación ya está en el dispositivo, es posible que se produzca un error `AMDeviceStartHouseArrestService` al intentar realizar la implementación con el reinicio rápido. La solución alternativa consiste en desinstalar la aplicación en el dispositivo y volver a implementarla.

Para notificar problemas adicionales, use la herramienta de comentarios en [Ayuda > Enviar comentarios > Notificar un problema](/visualstudio/ide/feedback-options?view=vs-2019#report-a-problem).
