---
title: ¿Qué configuración del proyecto es necesaria para el depurador?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3A024E4E-ACA3-4C7A-ADEF-541665D15779
author: davidortinau
ms.author: daortin
ms.date: 05/08/2018
ms.openlocfilehash: 856c04d129058e8cbac30dcdf619e8b2b5a66cb6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014260"
---
# <a name="what-project-settings-are-required-for-the-debugger"></a>¿Qué configuración del proyecto es necesaria para el depurador?

Para que el depurador funcione según lo esperado (puntos de interrupción, Mostrar registros de depuración, etc.), se debe habilitar la presentación de la información de depuración y la instrumentación del desarrollador.

Siga estos pasos para comprobar la configuración del entorno:

## <a name="visual-studio"></a>Programa para la mejora

1. Abrir las opciones del proyecto
2. Vaya a **Compilar > avanzadas...** Establecer la información de depuración en **completa**
3. Configuración de cada plataforma:
   - Vaya a **Opciones de Android > opciones de depuración**. Marque el cuadro **Habilitar instrumentación de desarrollador** .
   - Vaya a la **depuración de iOS > la instrumentación de &** . Marque el cuadro **Habilitar depuración** .

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. Abrir las opciones del proyecto
2. Vaya a **compilación > Compilador > opciones generales**. Establecer la información de depuración en **completa**
3. Configuración de cada plataforma:
    - Vaya a **compilación > opciones de depuración de compilación > Android**. Marque el cuadro **Habilitar instrumentación de desarrollador** .
    - Vaya a **compilación > depuración de iOS**. Marque el cuadro **Habilitar depuración** .
