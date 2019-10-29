---
title: ¿Cómo puedo ver qué bibliotecas se admiten en una PCL?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 14FF03BD-AF41-4DB1-B307-2349C13DE7E4
author: davidortinau
ms.author: daortin
ms.date: 07/27/2018
ms.openlocfilehash: 6d6519fc058c91a0f3519be034f8840469f87f0e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73013324"
---
# <a name="how-can-i-view-what-libraries-are-supported-in-a-pcl"></a>¿Cómo puedo ver qué bibliotecas se admiten en una PCL?

- Puede encontrar información general sobre las distintas características admitidas por las distintas plataformas de destino de PCL en la sección *características admitidas* de esta página: [https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library](https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library)

- Otra opción es usar el [analizador de portabilidad de .net](https://visualstudiogallery.msdn.microsoft.com/1177943e-cfb7-4822-a8a6-e56c7905292b) para evaluar si la biblioteca existente se puede convertir en un perfil de PCL.

- Una tercera posibilidad es examinar el contenido del perfil real que puede usar. Con el perfil 78 por ejemplo, puede ir aquí: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\Profile\Profile78\` y ver todos los ensamblados que contiene.

Sea cual sea el método que elija, tenga en cuenta que se debe descargar alguna funcionalidad a través de NuGet y Microsoft BCL Library.
