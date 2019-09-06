---
title: ¿Cómo se recopilan las pilas de llamadas actuales del proceso de Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 64c24b09-2c4a-43ad-b94d-6cd05a1aee44
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: f07bc4437293bdc49e4811c0104fd7245ccf9738
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282953"
---
# <a name="how-do-i-collect-the-current-call-stacks-of-the-visual-studio-process"></a>¿Cómo se recopilan las pilas de llamadas actuales del proceso de Visual Studio?

Cuando la GUI se bloquea (se bloquea, se inmoviliza) en Visual Studio, una parte importante de la información de diagnóstico que se va a recopilar es el conjunto de pilas de llamadas de todos los subprocesos del proceso de Visual Studio. Para guardar esta información para una instancia bloqueada de Visual Studio, puede usar una segunda instancia de Visual Studio:

1. Inicie una segunda instancia (una nueva ventana) de Visual Studio.

2. Cierre todas las soluciones abiertas en la nueva instancia de Visual Studio.

3. Seleccione **Depurar > Asociar al proceso**.

   ![](vs-callstack-images/image1.png "Seleccionar Depurar > asociar al proceso")

4. Seleccione la instancia bloqueada original de `devenv.exe` de la lista de **Procesos disponibles**.

5. Seleccione **Depurar > Interrumpir todo**.

   ![](vs-callstack-images/image2.png "Seleccione Depurar > interrumpir todo")

6. Seleccione **Depurar > guardar volcado como**.

   ![](vs-callstack-images/image3.png "Seleccione Depurar > Guardar volcado como")

7. Cambie **Guardar como tipo** a **minivolcado (\*. DMP)** . Esto producirá un archivo mucho más pequeño que el **minivolcado con montón**y el montón no suele ser relevante para diagnosticar inmovilizaciones.

   ![](vs-callstack-images/image4.png "Esto producirá un archivo mucho más pequeño que el minivolcado con montón y el montón no suele ser relevante para diagnosticar inmovilizaciones")

8. Guarde el archivo de volcado de memoria. Si envía el archivo en línea, puede comprimirlo para reducir el tamaño.
