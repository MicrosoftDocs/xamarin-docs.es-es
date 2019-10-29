---
title: 'Error MT1009: no se pudo copiar el ensamblado'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F9FEDFF5-C84C-42B4-8F25-E34846E7315A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 1ccf85c03ab1f2cff6428a0120a8ceb4de250761
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031148"
---
# <a name="error-mt1009-could-not-copy-the-assembly"></a>Error MT1009: no se pudo copiar el ensamblado

> [!IMPORTANT]
> Este problema se ha resuelto en las versiones recientes de Xamarin. iOS. Sin embargo, si el problema se produce en la versión más reciente del software, registre un [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con la información de control de versiones completa y el resultado del registro de compilación completo.

Como se describe en las notas de la [versión](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_7/xamarin.ios_7.2/index.md), se trata de un problema conocido en Xamarin. iOS 7.2.6. Este problema se debe a los permisos de archivo que necesitan más privilegios cuando Xamarin. iOS se instala con una cuenta de usuario diferente a la cuenta principal del desarrollador.

Para solucionar el problema, abra la aplicación terminal. app en la estación de trabajo Mac y ejecute el siguiente comando:

`sudo chmod 0644 /Developer/MonoTouch/usr/lib/mono/2.1/monotouch.dll.mdb`
