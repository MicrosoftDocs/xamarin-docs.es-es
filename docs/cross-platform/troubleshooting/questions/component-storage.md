---
title: ¿Dónde se almacenan los componentes en mi equipo?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
author: davidortinau
ms.author: daortin
ms.date: 05/08/2018
ms.openlocfilehash: 9447cf903c8789078e66082e720eeecfa7bb3e0d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014237"
---
# <a name="where-are-the-components-stored-on-my-machine"></a>¿Dónde se almacenan los componentes en mi equipo?

Siempre que se instala un componente de Xamarin en un proyecto de aplicación, se coloca en dos lugares:

1. En una carpeta componentes en el nivel raíz de la carpeta de la solución. Si quita el componente de todos los proyectos de la solución, también se quitará de esta carpeta.

2. Una copia también se almacena en las siguientes ubicaciones:
    - Windows: `%LocalAppData%\Xamarin\Cache\Components`
    - Mac: `~/Library/Caches/Xamarin/Components`

Para quitar completamente un componente del sistema, elimínelo de sus proyectos o soluciones y de la carpeta de caché anterior.
