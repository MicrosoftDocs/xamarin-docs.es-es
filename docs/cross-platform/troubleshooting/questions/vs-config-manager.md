---
title: ¿Por qué Visual Studio no incluye en mi compilación el proyecto de biblioteca al que se hace referencia?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: b9009db8-e716-43aa-b40e-6f28a8eb1b82
author: davidortinau
ms.author: daortin
ms.date: 12/02/2016
ms.openlocfilehash: aba2e9dacd930c6302a96a8daf929eadab485922
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73012754"
---
# <a name="why-doesnt-visual-studio-include-my-referenced-library-project-in-my-build"></a>¿Por qué Visual Studio no incluye en mi compilación el proyecto de biblioteca al que se hace referencia?

Visual Studio usa el **Administrador de configuración** para determinar qué proyectos de una solución se incluyen automáticamente en una configuración de implementación o compilación determinada.

Algunas de las plantillas que se generan con un proyecto de biblioteca al que se hace referencia ya incluyen en la configuración la biblioteca en cuestión, pero en caso contrario deberá establecerse manualmente.

## <a name="how-to-use-the-configuration-manager"></a>Cómo usar el Administrador de configuración

1. Abra **Compilar > Administrador de configuración**
2. Seleccionar la configuración que se va a personalizar; por ejemplo, **depurar | iPhone**
3. Active las casillas de verificación de los proyectos que quiere incluir.

> [!NOTE]
> Los cuadros atenuados se controlan automáticamente y no deben cambiar.

Presentación en pantalla de estos pasos: [https://screencast.com/t/zLoQOpEn](https://screencast.com/t/zLoQOpEn)
