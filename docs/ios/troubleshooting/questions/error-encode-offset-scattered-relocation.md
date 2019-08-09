---
title: 'Error de compilación: No se puede codificar el desplazamiento X en la reubicación distribuida resultante'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 84158C42-FE79-415A-A44A-D48C9E5E6760
ms.technology: xamarin-ios
author: chamons
ms.author: chhamo
ms.date: 08/07/2019
ms.openlocfilehash: df974649c8c9f1d1fb4c13d6d801eb0f6a3d1e77
ms.sourcegitcommit: 2e5a6b8bcd1a073b54604f51538fd108e1c2a8e5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/09/2019
ms.locfileid: "68876234"
---
# <a name="compile-error-can-not-encode-offset-x-in-resulting-scattered-relocation"></a>Error de compilación: No se puede codificar el desplazamiento X en la reubicación distribuida resultante

```
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Xamarin\iOS\Xamarin.iOS.Common.targets(804,3): error GDC116A36: can not encode offset ‘0x1155504’ in resulting scattered relocation.
1> .long mono_aot_Xamarin_iOS_got - . + 12 (TaskId:141)
1> ^ (TaskId:141)
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Xamarin\iOS\Xamarin.iOS.Common.targets(804,3): error GDC116A36: can not encode offset ‘0x11555B8’ in resulting scattered relocation.
1> .long mono_aot_Xamarin_iOS_got - . + 16 (TaskId:141)
```

Este problema se produce al compilar para arquitecturas de 32 bits, como ARMv7, cuando el archivo binario final es demasiado grande para la cadena de herramientas nativa.

Para resolver este:

- Quite la compilación de esa arquitectura en el panel compilación de iOS de opciones del proyecto.
- Habilitar la vinculación o quitar manualmente el código para reducir el tamaño final del archivo ejecutable
