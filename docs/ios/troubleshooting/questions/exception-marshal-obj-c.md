---
title: '¿Por qué se produce un error en la aplicación de iOS 9: System. Exception: error al serializar el objeto de Objective-C?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8805ABEC-48D4-4CCB-A226-3A5B2ECE4BF0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 1bb67eaa884e523e96ef1015daaa6b959ea1512d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031102"
---
# <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>¿Por qué se produce un error en la aplicación de iOS 9: System. Exception: error al serializar el objeto de Objective-C?

Es posible que aparezca un error con este formato:

> System. Exception: no se pudo serializar el objeto de Objective-C... No se pudo encontrar una instancia administrada existente para este objeto...

Los cambios de la API en iOS 9 requieren que se use un constructor de devolución de llamada al llamar a código no administrado, ya que la API subyacente espera ahora. Utilice la siguiente línea para agregar el constructor de devolución de llamada a la clase: 

`public foo (IntPtr handle) : base (handle)` 

### <a name="next-steps"></a>Pasos siguientes

Para obtener más ayuda, para ponerse en contacto con nosotros o si este problema permanece incluso después de usar la información anterior, consulte [¿Qué opciones de soporte técnico están disponibles para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obtener información sobre las opciones de contacto, sugerencias y cómo archivar un nuevo error si es necesario . 
