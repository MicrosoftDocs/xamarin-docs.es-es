---
title: ¿Qué controladores USB son necesario para depurar Android en Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 21fd8eff64d374e52e64194524a8c096cdf4d90e
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027038"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>¿Qué controladores USB son necesario para depurar Android en Windows?

## <a name="finding-usb-drivers"></a>Búsqueda de controladores USB

Para depurar en un dispositivo Android al desarrollar en Windows; debe instalar un controlador USB compatible. El administrador de Android SDK incluye el "controlador USB de Google" de forma predeterminada, que agrega compatibilidad con los dispositivos Nexus como se describe aquí: [https://developer.android.com/sdk/win-usb.html](https://developer.android.com/sdk/win-usb.html)

Otros dispositivos requieren controladores USB publicados de forma específica por el fabricante del dispositivo. En esta guía se incluyen algunos vínculos de los fabricantes más habituales: [https://developer.android.com/tools/extras/oem-usb.html](https://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>Alternativas

Según el fabricante, puede resultar difícil rastrear el controlador USB exacto necesario. Algunas alternativas para probar aplicaciones de Android desarrolladas en Windows, incluido el uso de un emulador de Android o de servicios de prueba externos. Algunas de ellas son:

- [Prueba de App Center](https://docs.microsoft.com/appcenter/test-cloud/): los servicios de pruebas en la nube se ejecutan en cientos de dispositivos Android reales.

- [Emulador de Visual Studio para Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/)

- [Depuración en Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md)
