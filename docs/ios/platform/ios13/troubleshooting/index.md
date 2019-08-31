---
title: Solución de problemas de Xamarin e iOS 13
description: Esta sección contiene sugerencias para la solución de problemas de la funcionalidad de Xamarin relacionada con iOS 13.
ms.prod: xamarin
ms.assetid: 00DE8C33-1407-45C0-A0C7-32AF1E490034
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/12/2019
ms.openlocfilehash: d2a2146a0b7345475e2eb93d52fb02387c833224
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200039"
---
# <a name="troubleshooting-tips-for-ios-13-and-xamarinios"></a>Sugerencias para la solución de problemas de iOS 13 y Xamarin. iOS

![Características en versión preliminar](~/media/shared/preview.png)

## <a name="updating-to-xcode-11-stops-the-simulator-from-launching"></a>La actualización a Xcode 11 detiene el inicio del simulador

Después de actualizar a **Xcode 11 beta 1** cada vez que se inicia un simulador, se produce la siguiente excepción y el simulador no se inicia. Esto sucede con todos los simuladores.

### <a name="exception"></a>Excepción

`Foundation.ObjCException: NSInvalidArgumentException: -[SimDevice registerNotificationHandler:]: unrecognized selector sent to instance 0x7ffbf5d1e110`

### <a name="workaround"></a>Solución alternativa

Hasta que haya una [corrección](https://github.com/xamarin/xamarin-macios/issues/6216), se pueden seguir los pasos siguientes para volver a instalar el marco de trabajo del simulador antiguo a fin de permitir que los desarrolladores sigan funcionando:

> [!NOTE]
> En estos pasos se supone que tiene dos aplicaciones Xcode:
> - **Xcode11-beta1. app** : versión beta que no funciona con simuladores y Visual Studio para Mac.
> - **Xcode102. app** : una versión estable de Xcode 10. El suyo también podría llamarse **Xcode. app**.
>
> Cambie los ejemplos de línea de comandos siguientes según corresponda para su configuración.

1. Asegúrese de que tiene Xcode 11 seleccionado mediante Xcode: seleccione:

   `sudo xcode-select -s /Applications/Xcode11-beta1.app/Contents/Developer/`

2. Ejecute, si es necesario, las herramientas de instalación por primera vez.

    `/Applications/Xcode11-beta1.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

3. Quite el siguiente marco de trabajo:

    `sudo rm -Rf  /Library/Developer/PrivateFrameworks/CoreSimulator.framework/Versions/*`

4. Volver a la versión anterior de Xcode

   `sudo xcode-select -s /Applications/Xcode102.app/Contents/Developer/`

5. Vuelva a ejecutar la primera herramienta de inicio de la versión _anterior_ de Xcode que acaba de seleccionar.

   `/Applications/Xcode102.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

Después de seguir estos pasos, debería poder trabajar con el simulador de iOS de nuevo.
