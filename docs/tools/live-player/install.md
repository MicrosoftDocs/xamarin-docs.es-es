---
title: Xamarin Live Player configuración de Visual Studio
description: En este documento se describe cómo usar la Xamarin Live Player para realizar ediciones en directo en una aplicación en ejecución.
ms.prod: xamarin
ms.assetid: 5DDF9203-8826-4B04-93F5-B8D07EDE3873
author: conceptdev
ms.author: crdun
ms.date: 06/13/2019
ms.openlocfilehash: 94f1d36bf97aab7eabb57e6f2712c9850b390ab1
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290490"
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
2. Vaya a **Herramientas > Opciones...**  y seleccione la opción **Xamarin > otros**.
3. Seleccione **Habilitar Xamarin Live Player**:

    ![Seleccione la casilla Habilitar Xamarin Live Player en la ventana de opciones](install-images/vs2017-options.png)

4. Cree o abra un proyecto de Xamarin (o un [ejemplo](~/tools/live-player/samples.md)).
5. Elija **Live Player** en la lista de dispositivos:

    ![La lista de dispositivos incluye una opción de Xamarin Live Player](install-images/devices-empty-windows.png)

    - Si ya ha emparejado un dispositivo, este estará disponible como una opción.
    - De lo contrario, se le pedirá que empareje un dispositivo cuando sea necesario.

6. Presione el botón **Ejecutar** o seleccione una de las siguientes opciones del menú **Ejecutar** o del menú contextual:

    - **Iniciar sin depurar** : puede editar la aplicación y ver los cambios que se producen en el dispositivo (la aplicación se reinicia cuando se realizan cambios y guarda el archivo).
    - **Iniciar depuración** : puede establecer puntos de interrupción e inspeccionar las variables, pero no se puede editar el código.

    Como alternativa, seleccione **Herramientas > Xamarin Live Player > Ejecutar vista actual en vivo**, que permite editar la aplicación y ver los cambios que se producen en el dispositivo. Se muestra la vista actual (en lugar de la pantalla principal de la aplicación).

7. Si un dispositivo ya está emparejado y se ejecuta la aplicación Xamarin Live Player en este, el código se ejecutará inmediatamente.

    Si no hay ningún dispositivo emparejado, aparecerá un código QR con instrucciones sobre cómo asociar uno:

    ![Ventana para emparejar un dispositivo](install-images/manage-empty-windows.png)

    Si no se puede establecer contacto con el dispositivo para el emparejamiento, puede aparecer un error.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="using-xamarin-live-player"></a>Usar Xamarin Live Player

Ya debe tener la aplicación Xamarin Live Player en el dispositivo. Ya no está disponible para su descarga.

1. Abra **Visual Studio para Mac**.
2. Vaya a **Visual Studio > Preferencias...**  y seleccione la pestaña **Proyectos > Xamarin Live Player (versión preliminar)** .
3. Seleccione **Habilitar Xamarin Live Player**:

    [![Seleccione la casilla Habilitar Xamarin Live Player en la ventana de opciones](install-images/vsmac-options-sml.png)](install-images/vsmac-options.png#lightbox)

4. Cree o abra un proyecto de Xamarin (o un [ejemplo](~/tools/live-player/samples.md)).
5. Elija **Live Player** en la lista de dispositivos.

    ![La lista de dispositivos incluye una opción de Xamarin Live Player](install-images/devices.png)

    - Si ya ha emparejado un dispositivo, este estará disponible como una opción.
    - De lo contrario, se le pedirá que empareje un dispositivo cuando sea necesario.
    - Elija **Dispositivos con Xamarin Live Player...**  para administrar los dispositivos que quiera usar con Xamarin Live Player.

6. Presione el botón **Ejecutar** o seleccione una de las siguientes opciones del menú **Ejecutar** o del menú contextual:

    ![Opciones del menú Ejecutar](install-images/run-menu.png)

    - **Iniciar sin depurar** : puede editar la aplicación y ver los cambios que se producen en el dispositivo (la aplicación se reinicia cuando se realizan cambios y guarda el archivo).
    - **Iniciar depuración** : puede establecer puntos de interrupción e inspeccionar las variables, pero no se puede editar el código.
    - **Ejecutar vista actual en vivo** : puede editar la aplicación y ver los cambios que se producen en el dispositivo. Se muestra la vista actual (en lugar de la pantalla principal de la aplicación).

7. Si un dispositivo ya está emparejado y se ejecuta la aplicación Xamarin Live Player en este, el código se ejecutará inmediatamente.

    Si no hay ningún dispositivo emparejado, aparecerá un código QR con instrucciones sobre cómo asociar uno:

    ![Ventana para emparejar un dispositivo](install-images/manage-empty.png)

    Si no se puede conectar el dispositivo para el emparejamiento, aparecerá un error:

    ![No se puede conectar con el mensaje de error del dispositivo](install-images/error-cannot-connect.png)

-----

Si experimenta algún problema o no puede conectarse, consulte [limitaciones y solución de problemas](~/tools/live-player/troubleshooting.md).

## <a name="related-links"></a>Vínculos relacionados

- [Solución de problemas](~/tools/live-player/troubleshooting.md)
