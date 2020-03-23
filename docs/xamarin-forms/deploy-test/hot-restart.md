---
title: Reinicio rápido de Xamarin
description: En este documento se describe cómo configurar y usar el reinicio rápido de Xamarin para depurar una aplicación iOS.
ms.prod: xamarin
ms.assetid: 6BC62A88-9368-41BB-8494-760F2A4805DB
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/16/2020
ms.openlocfilehash: cc5efffd4c3646fbff9cdb1ad1a30ec614cb4921
ms.sourcegitcommit: 8df67f0d76ff762b517d27b8d4c217d3a3379a18
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2020
ms.locfileid: "79429572"
---
# <a name="xamarin-hot-restart-preview"></a>Reinicio rápido de Xamarin (versión preliminar)

![Características en versión preliminar](~/media/shared/preview.png)

El reinicio rápido de Xamarin permite probar rápidamente los cambios en la aplicación durante el desarrollo, incluidas las ediciones de código de varios archivos, los recursos y las referencias. Inserta los cambios nuevos en el grupo de aplicaciones existente en el destino de depuración, lo que genera un ciclo de compilación e implementación mucho más rápido.

> [!IMPORTANT]
> En este momento, el reinicio rápido de Xamarin está disponible en la versión estable de Visual Studio 2019 16.5 y es compatible con aplicaciones iOS en las que se usa Xamarin.Forms. La compatibilidad con Visual Studio para Mac y aplicaciones que no son de Xamarin.Forms está en la hoja de ruta.

## <a name="requirements"></a>Requisitos

- Visual Studio 2019, versión 16.5
- iTunes (64 bits)
- Cuenta de desarrollador de Apple e inscripción pagada al [Programa para Desarrolladores de Apple](https://developer.apple.com/programs)


## <a name="initial-setup"></a>Instalación inicial

> [!NOTE]
> El reinicio rápido de Xamarin está deshabilitado de forma predeterminada en la versión preliminar. Puede habilitarlo en **Herramientas > Opciones > Entorno > Características de versión preliminar > Habilitar el reinicio rápido de Xamarin**.

1. Asegúrese de que el proyecto de iOS está establecido como proyecto de inicio y la configuración de compilación en **Depuración|iPhone**.

   1. Si se trata de un proyecto existente, vaya a **Compilar > Configuration Manager...** y asegúrese de que **Implementación** está habilitado para el proyecto de iOS.

2. Seleccione y haga clic en **Dispositivo local** en la barra de herramientas para iniciar el Asistente para la instalación:

    [![](hot-restart-images/toolbar.png "Screenshot of the Visual Studio toolbar with local device set as the debug target.")](hot-restart-images/toolbar.png)

3. Si iTunes no está instalado, haga clic en **Descargar iTunes** para descargar el instalador. Haga clic en **Siguiente** cuando se complete la instalación de iTunes.

4. Conecte un dispositivo iOS al equipo. Si un dispositivo ya estaba conectado, desconéctelo y vuelva a conectarlo. El nombre del dispositivo aparecerá en el asistente una vez que se haya detectado. Haga clic en **Siguiente**.

5. Escriba las credenciales de la cuenta de desarrollador de Apple y haga clic en **Siguiente**.

6. Seleccione un equipo de desarrollo mediante el menú desplegable para habilitar el [ aprovisionamiento automático](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md) en el proyecto. Haga clic en **Finalizar**.

> [!NOTE]
> Se recomienda el uso del aprovisionamiento automático para que se puedan configurar fácilmente dispositivos iOS adicionales para la implementación. Pero puede deshabilitarlo y seguir usando el aprovisionamiento manual si los perfiles de aprovisionamiento correctos están presentes.

## <a name="use-xamarin-hot-restart"></a>Uso del reinicio rápido de Xamarin
Después de la instalación inicial, el dispositivo conectado aparecerá en el menú desplegable de destino de depuración. Para depurar la aplicación, seleccione el dispositivo en la lista desplegable y haga clic en el botón **Ejecutar**. Es posible que vea un mensaje de Visual Studio en el que se le pide que inicie manualmente la aplicación en el dispositivo para poder iniciar la sesión de depuración.

Puede realizar modificaciones en los archivos de código durante la depuración y después presionar el botón **Reiniciar** de la barra de herramientas de depuración, o bien **Ctrl+Mayús+F5**, para reiniciar la sesión de depuración con los nuevos cambios aplicados:

[![](hot-restart-images/restart.png "Screenshot of the debug toolbar with the restart button highlighted.")](hot-restart-images/toolbar.png)

También puede usar el símbolo de preprocesador `HOTRESTART` para impedir que se ejecute cierto código al depurar con el reinicio rápido de Xamarin.

## <a name="limitations"></a>Limitaciones

- Actualmente solo se admiten aplicaciones iOS compiladas con Xamarin.Forms y dispositivos iOS.
- Solo se admiten dispositivos iOS de 64 bits. A partir de iOS 11, Apple ya no permite ejecutar aplicaciones iOS en la arquitectura de 32 bits (dispositivos anteriores a iPhone 5s).
- Los archivos de Storyboard y XIB no se admiten y es posible que la aplicación se bloquee si intenta cargarlos en tiempo de ejecución. Use el símbolo de preprocesador `HOTRESTART` para evitar que se ejecute el código.
- No se admiten marcos ni bibliotecas de iOS estáticos y es posible que vea errores en tiempo de ejecución o bloqueos si la aplicación intenta cargarlos. Use el símbolo de preprocesador `HOTRESTART` para evitar que se ejecute el código. Las bibliotecas de iOS dinámicas se admiten.
- No se puede usar el reinicio rápido de Xamarin para crear paquetes de aplicaciones para la publicación. Seguirá necesitando un equipo Mac a fin de realizar una compilación, firma e implementación completas para la aplicación en producción.

## <a name="troubleshoot"></a>Solucionar problemas

- El asistente para la instalación no detectará iTunes si se ha instalado desde Microsoft Store. Primero tendrá que desinstalar esa versión y, después, descargar el [instalador desde Apple](https://go.microsoft.com/fwlink/?linkid=2101014).
- Hay un problema conocido en el que tener habilitadas compilaciones específicas del dispositivo impide que la aplicación entre en el modo de depuración. La solución consiste en deshabilitarlo en **Propiedades > Compilación de iOS** y reintentar la depuración. Este problema se corregirá en futuras versiones.
- Si la aplicación ya está en el dispositivo, es posible que se produzca un error `AMDeviceStartHouseArrestService` al intentar realizar la implementación con el reinicio rápido. La solución alternativa consiste en desinstalar la aplicación en el dispositivo y volver a implementarla.
- Si escribe un identificador de Apple que no forme parte del Programa para Desarrolladores de Apple, podría mostrarse el siguiente error: `Authentication Error. Xcode 7.3 or later is required to continue developing with your Apple ID`. Debe tener una cuenta de desarrollador de Apple válida para usar el reinicio rápido de Xamarin en dispositivos iOS. 

Para notificar problemas adicionales, use la herramienta de comentarios en [Ayuda > Enviar comentarios > Notificar un problema](/visualstudio/ide/feedback-options?view=vs-2019#report-a-problem).
