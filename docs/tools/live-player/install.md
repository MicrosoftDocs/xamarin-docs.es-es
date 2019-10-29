---
title: Xamarin Live Player configuración de Visual Studio
description: En este documento se describe cómo usar la Xamarin Live Player para realizar ediciones en directo en una aplicación en ejecución.
ms.prod: xamarin
ms.assetid: 5DDF9203-8826-4B04-93F5-B8D07EDE3873
author: davidortinau
ms.author: daortin
ms.date: 06/13/2019
ms.openlocfilehash: 3dfe63cf3cf87a99d15879a0d4791248fa06f195
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029684"
---
# <a name="xamarin-live-player-visual-studio-configuration"></a>Xamarin Live Player configuración de Visual Studio

![Características en versión preliminar](~/media/shared/preview.png)

> [!WARNING]
> Finalizó la vista previa de Xamarin Live Player. La aplicación ya no está disponible. Las instrucciones siguientes se proporcionan para que los clientes sigan usando la versión preliminar con Visual Studio 2017.

> [!TIP]
> Puede usar la [vista previa de XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) en Visual Studio 2019 o Visual Studio para Mac para ver los diseños de pantalla a medida que los edita.

# <a name="visual-studio-2017tabwindows"></a>[Visual Studio 2017](#tab/windows)

## <a name="using-xamarin-live-player"></a>Usar Xamarin Live Player

Ya debe tener la aplicación Xamarin Live Player en el dispositivo. Ya no está disponible para su descarga.

1. Abra **Visual Studio 2017**.
2. Vaya a **herramientas > opciones...** y seleccione la pestaña **Xamarin > Other (otros** ).
3. **Activar Xamarin Live Player**:

    ![Active la casilla habilitar Xamarin Live Player en la ventana Opciones.](install-images/vs2017-options.png)

4. Cree o abra un proyecto de Xamarin (o un [ejemplo](~/tools/live-player/samples.md)).
5. Elija **Live Player** en la lista de dispositivos:

    ![La lista de dispositivos incluye una opción de Xamarin Live Player](install-images/devices-empty-windows.png)

    - Si ya ha emparejado un dispositivo, estará disponible como opción.
    - De lo contrario, se le pedirá que empareje un dispositivo cuando sea necesario.

6. Presione el botón **Ejecutar** o seleccione una de las siguientes opciones en el menú **Ejecutar** o haga clic con el botón secundario:

    - **Iniciar sin depurar** : puede editar la aplicación y ver los cambios que se producen en el dispositivo (la aplicación se reinicia a medida que se realizan los cambios y se guarda el archivo).
    - **Iniciar depuración** : puede establecer puntos de interrupción e inspeccionar variables, pero el código no se puede editar.

    Como alternativa, seleccione **herramientas > Xamarin Live Player > vista actual**de la ejecución en directo, que le permite editar la aplicación y ver los cambios que se producen en el dispositivo. Se muestra la vista actual (en lugar de la pantalla principal de la aplicación).

7. Si un dispositivo ya está emparejado y la aplicación Xamarin Live Player se está ejecutando en el dispositivo, el código se ejecutará de inmediato.

    Si no se empareja ningún dispositivo, aparecerá un código QR con instrucciones sobre cómo emparejar un dispositivo:

    ![Ventana emparejar un dispositivo](install-images/manage-empty-windows.png)

    Si no se puede establecer contacto con el dispositivo para el emparejamiento, puede aparecer un error.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="using-xamarin-live-player"></a>Usar Xamarin Live Player

Ya debe tener la aplicación Xamarin Live Player en el dispositivo. Ya no está disponible para su descarga.

1. Abra **Visual Studio para Mac**.
2. Vaya a **Visual Studio > preferencias...** y seleccione la pestaña **proyectos > Xamarin Live Player (vista previa)** .
3. **Activar Xamarin Live Player**:

    [![Active la casilla habilitar Xamarin Live Player en la ventana Opciones](install-images/vsmac-options-sml.png)](install-images/vsmac-options.png#lightbox)

4. Cree o abra un proyecto de Xamarin (o un [ejemplo](~/tools/live-player/samples.md)).
5. Elija **Live Player** en la lista de dispositivos.

    ![La lista de dispositivos incluye una opción de Xamarin Live Player](install-images/devices.png)

    - Si ya ha emparejado un dispositivo, estará disponible como opción.
    - De lo contrario, se le pedirá que empareje un dispositivo cuando sea necesario.
    - Elija **Xamarin Live Player dispositivos...** para administrar los dispositivos que quiere usar con Xamarin Live Player.

6. Presione el botón **Ejecutar** o seleccione una de las siguientes opciones en el menú **Ejecutar** o haga clic con el botón secundario:

    ![Opciones del menú ejecutar](install-images/run-menu.png)

    - **Iniciar sin depurar** : puede editar la aplicación y ver los cambios que se producen en el dispositivo (la aplicación se reinicia a medida que se realizan los cambios y se guarda el archivo).
    - **Iniciar depuración** : puede establecer puntos de interrupción e inspeccionar variables, pero el código no se puede editar.
    - **Vista actual de la ejecución activa** : puede editar la aplicación y ver los cambios que se producen en el dispositivo. Se muestra la vista actual (en lugar de la pantalla principal de la aplicación).

7. Si un dispositivo ya está emparejado y la aplicación Xamarin Live Player se está ejecutando en el dispositivo, el código se ejecutará de inmediato.

    Si no se empareja ningún dispositivo, aparecerá un código QR con instrucciones sobre cómo emparejar un dispositivo:

    ![Ventana emparejar un dispositivo](install-images/manage-empty.png)

    Si no se puede establecer contacto con el dispositivo para el emparejamiento, aparecerá un error:

    ![No se puede conectar con el mensaje de error del dispositivo](install-images/error-cannot-connect.png)

-----

Si experimenta algún problema o no puede conectarse, consulte [limitaciones y solución de problemas](~/tools/live-player/troubleshooting.md).

## <a name="related-links"></a>Vínculos relacionados

- [Solución de problemas](~/tools/live-player/troubleshooting.md)
