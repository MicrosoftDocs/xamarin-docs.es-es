---
title: ¿Cómo se recopilan las pilas de llamadas actuales del proceso de Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 64c24b09-2c4a-43ad-b94d-6cd05a1aee44
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: 9ed79b2273758b8051a96169d4c9b53870de1fb1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73013028"
---
# <a name="how-do-i-collect-the-current-call-stacks-of-the-visual-studio-process"></a>¿Cómo se recopilan las pilas de llamadas actuales del proceso de Visual Studio?

Cuando la GUI se bloquea (se bloquea, se inmoviliza) en Visual Studio, una parte importante de la información de diagnóstico que se va a recopilar es el conjunto de pilas de llamadas de todos los subprocesos del proceso de Visual Studio. Para guardar esta información para una instancia bloqueada de Visual Studio, puede usar una segunda instancia de Visual Studio:

1. Inicie una segunda instancia (una nueva ventana) de Visual Studio.

2. Cierre todas las soluciones abiertas en la nueva instancia de Visual Studio.

3. Seleccione **Depurar > Asociar al proceso**.

   ![](vs-callstack-images/image1.png "Select Debug > Attach to Process")

4. Seleccione la instancia bloqueada original de `devenv.exe` de la lista de **Procesos disponibles**.

5. Seleccione **Depurar > Interrumpir todo**.

   ![](vs-callstack-images/image2.png "Select Debug > Break All")

6. Seleccione **Depurar > guardar volcado como**.

   ![](vs-callstack-images/image3.png "Select Debug > Save Dump As")

7. Cambie **Guardar como tipo** a **minivolcado (\*. DMP)** . Esto producirá un archivo mucho más pequeño que el **minivolcado con montón**y el montón no suele ser relevante para diagnosticar inmovilizaciones.

   ![](vs-callstack-images/image4.png "This will produce a much smaller file than Minidump with Heap, and the heap is usually not relevant for diagnosing freezes")

8. Guarde el archivo de volcado de memoria. Si envía el archivo en línea, puede comprimirlo para reducir el tamaño.
