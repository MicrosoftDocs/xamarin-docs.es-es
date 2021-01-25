---
title: Aplicación Xamarin Live Player
description: En este documento se describe el Xamarin Live Player aplicación, que se puede usar para obtener una vista previa de los cambios de código en el dispositivo. Describe el programa de instalación, los ejemplos, los registros, la configuración, la administración de dispositivos y mucho más.
ms.prod: xamarin
ms.assetid: A7EB73C1-38D7-46C5-9AF6-4C571C168BE7
author: davidortinau
ms.author: daortin
ms.date: 06/13/2019
ms.openlocfilehash: d79eb9ad1ca57361e063f6ce73910d6bb149cf72
ms.sourcegitcommit: 424eaef56fd2933c98e72f1d3e7ac71730fe4835
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/25/2021
ms.locfileid: "98758076"
---
# <a name="xamarin-live-player-app"></a>Aplicación Xamarin Live Player

![Características en versión preliminar](~/media/shared/preview.png)

> [!WARNING]
> Finalizó la vista previa de Xamarin Live Player. La aplicación ya no está disponible. Las instrucciones siguientes se proporcionan para que los clientes sigan usando la versión preliminar con Visual Studio 2017.

> [!TIP]
> Puede usar la [vista previa de XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) en Visual Studio 2019 o Visual Studio para Mac para ver los diseños de pantalla a medida que los edita.

En el inicio, la aplicación Xamarin Live Player tiene el siguiente aspecto:

![Captura de pantalla de la aplicación Android de Live Player](player-images/app-android-sml.png)

Al presionar **emparejar con Visual Studio**, use la cámara para digitalizar el código de barras que se muestra en el equipo:

![Captura de pantalla del escáner de código de barras Android](player-images/scan-android-sml.png)

Si la conexión es correcta, el código debe ejecutarse en el dispositivo casi inmediatamente (como el [ejemplo Calculator](https://github.com/xamarin/mobile-samples/tree/master/LivePlayer/BasicCalculator):

![Aplicación de calculadora de ejemplo en ejecución en el dispositivo](player-images/basic-calculator-sml.png)

## <a name="options"></a>Opciones

Presione el botón de información **(i)** en la parte inferior de la aplicación para que se muestre el menú de **Opciones** :

[![Captura de pantalla del menú de opciones](player-images/options-sml.png)](player-images/options.png#lightbox)

### <a name="logs"></a>Registros

Vea los registros para diagnosticar problemas.

### <a name="settings"></a>Configuración

- Alternar la presentación de errores de compilación y en tiempo de ejecución.
- Información de versión.
- Enviar comentarios

[![Captura de pantalla de la configuración](player-images/settings-sml.png)](player-images/settings.png#lightbox)

## <a name="managing-devices"></a>Administración de dispositivos

Para conectar un dispositivo por primera vez, siga las instrucciones de [requisitos & configuración](~/tools/live-player/install.md). Puede emparejar varios dispositivos y administrarlos a través del IDE.

# <a name="visual-studio-2017"></a>[Visual Studio 2017](#tab/windows)

En Visual Studio, elija **herramientas > Xamarin Live Player > administrar dispositivos...**

![Ventana administrar dispositivos](player-images/manage-tools-menu-vs.png)

Esta ventana le permite hacer lo siguiente:

- Emparejar un dispositivo nuevo examinando el código
- También puede emparejar un dispositivo escribiendo el código que se muestra en la pantalla.
- Quitar los dispositivos existentes de la lista

También puede tener acceso a esta ventana desde la lista de dispositivos.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

En Visual Studio para Mac, elija **herramientas > (Xamarin Live Player) administrar dispositivos...**

![La captura de pantalla muestra administrar dispositivos seleccionados en la ventana herramientas.](player-images/manage-tools-menu.png)

Esta ventana le permite hacer lo siguiente:

- Emparejar un dispositivo nuevo examinando el código
- También puede emparejar un dispositivo escribiendo el código que se muestra en la pantalla.
- Quitar los dispositivos existentes de la lista

![Captura de pantalla muestra la ventana de Xamarin Live Player con la opción de obtener una vista previa de la aplicación.](player-images/manage.png)

También puede tener acceso a esta ventana desde la lista de dispositivos:

![Elegir Xamarin Live Player dispositivos en la lista de dispositivos](player-images/manage-device-menu.png)

-----

Si tiene algún problema, vea [limitaciones y solución de problemas](~/tools/live-player/troubleshooting.md).

## <a name="related-links"></a>Vínculos relacionados

- [Solución de problemas](~/tools/live-player/troubleshooting.md)
