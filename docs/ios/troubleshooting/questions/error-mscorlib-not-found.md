---
title: 'Error en tiempo de ejecución: The assembly mscorlib.dll was not found or could not be loaded'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1027E16C-2C14-4BB5-AAAB-342F3E28E22E
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: a1c6c5357c62d0421376930ea04c4755fae59d04
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769350"
---
# <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loaded"></a>Error en tiempo de ejecución: The assembly mscorlib.dll was not found or could not be loaded

```
<Warning>: The assembly mscorlib.dll was not found or could not be loaded.
<Warning>: It should have been installed in the `/Developer/MonoTouch/Source/monotouch/builds/install/target64/lib/mono/2.0/mscorlib.dll' directory.
<Warning>: Service exited with abnormal code: 1
```

Este problema se produce cuando faltan `.monotouch-64` las carpetas *Hidden* `.monotouch-32` y en `.xcarchive` para la creación de firmas/IPA, lo que desencadena el error de tiempo de ejecución.
