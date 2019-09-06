---
title: Operaciones de punto flotante en Xamarin. iOS
description: En este documento se describe cómo Xamarin. iOS controla las operaciones de punto flotante de precisión de 32 bits y 64 bits y describe los impactos asociados en el rendimiento.
ms.prod: xamarin
ms.assetid: 003F25C1-B430-4339-9C95-7DF527EBC699
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 11/25/2015
ms.openlocfilehash: 1ecb00fecaf14afb8c6d5c59297eb26821ed791a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291928"
---
# <a name="floating-point-operations-in-xamarinios"></a>Operaciones de punto flotante en Xamarin. iOS

Xamarin. iOS realizará de forma predeterminada operaciones de punto flotante de 32 y 64 bits con una precisión de 64 bits en ARM.  

Aunque esta mayor precisión está más próxima a lo que los desarrolladores esperan de las C# operaciones de punto flotante en en el escritorio, en Mobile, el impacto en el rendimiento puede ser significativo.

Es posible compilar el código de punto flotante de 32 bits para utilizar las operaciones de punto flotante de 32 bits.  Para ello, debe usar al menos Xamarin. iOS 8,10 y establecer en el panel de las opciones de compilación de iOS en la línea de entrada de "Mtouch extra arguments" el siguiente valor:

```
--aot-options=-O=float32
```

Esto informará a los compiladores estáticos (el compilador estático integrado de mono o a LLVM) para realizar operaciones de punto flotante con valores Float de 32 bits.
