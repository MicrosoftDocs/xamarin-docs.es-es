---
title: ¿Qué controladores USB son necesario para depurar Android en Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: 8be3f1b8803aa7e052ebc89af51dad3b659f95f5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757330"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>¿Qué controladores USB son necesario para depurar Android en Windows?

## <a name="finding-usb-drivers"></a>Búsqueda de controladores USB

Para depurar en un dispositivo Android al desarrollar en Windows; debe instalar un controlador USB compatible. De forma predeterminada, el administrador de Android SDK incluye el "controlador USB de Google", que agrega compatibilidad con dispositivos Nexus como se describe aquí:[https://developer.android.com/sdk/win-usb.html](https://developer.android.com/sdk/win-usb.html)

Otros dispositivos requieren controladores USB publicados específicamente por el fabricante del dispositivo. En esta guía se incluyen algunos vínculos para los fabricantes más comunes:[https://developer.android.com/tools/extras/oem-usb.html](https://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>Alternativas

Según el Manfacturer, puede ser difícil realizar un seguimiento del controlador USB exacto necesario. Algunas alternativas para probar aplicaciones Android desarrolladas en Windows, incluido el uso de un emulador de Android o el uso de servicios de prueba externos. Algunos son:

- [App Center test](https://docs.microsoft.com/appcenter/test-cloud/) -Cloud Testing Services se ejecutan en cientos de dispositivos Android reales.

- [Emulador de Visual Studio para Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/)

- [Depuración en Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md)
