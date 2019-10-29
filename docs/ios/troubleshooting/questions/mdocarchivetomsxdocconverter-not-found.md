---
title: MDocArchiveToMsxDocConverter.exe no encontró rver.BaseCommand.OnRequest
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5AC6AC4-0E7C-4746-A7CF-872F0E75AFF4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 3d21dfdbf6c9be00fe6851bb288268faccd74308
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030967"
---
# <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequest"></a>MDocArchiveToMsxDocConverter.exe no encontró rver.BaseCommand.OnRequest

> [!IMPORTANT]
> Este problema se ha resuelto en las versiones recientes de Xamarin. Sin embargo, si el problema se produce en la versión más reciente del software, registre un [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con la información de control de versiones completa y el resultado del registro de compilación completo.

## <a name="error-message"></a>Mensaje de error

Este error puede aparecer en el *registro del servidor Mac* en Visual Studio:

```
Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found
 rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context, System.Object commandRequestState) [0x00000] in <filename unknown>:0
  at Mtb.Server.Listener.OnRequest (System.Object state) [0x00000] in <filename unknown>:0
```

Hay dos problemas independientes en este mensaje:

1. `Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found`

    Este error es inofensivo, pero también es engañoso. Se [quitará](https://bugzilla.xamarin.com/show_bug.cgi?id=21667) en una versión futura.

2. `rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context …`

    Este error es el problema real. Desafortunadamente, debido a una [limitación](https://bugzilla.xamarin.com/show_bug.cgi?id=22080) , este seguimiento de la pila de excepciones está *incompleto*. Si observa un seguimiento de la pila incompleto similar al siguiente en el registro del servidor Mac, puede comprobar el archivo de `~/Library/Logs/Xamarin/MonoTouchVS/mtbserver.log` en el host de compilación de Mac para buscar el seguimiento de la pila completo.
