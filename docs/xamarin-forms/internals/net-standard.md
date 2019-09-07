---
title: Compatibilidad con .NET Standard 2,0 en Xamarin. Forms
description: En este artículo se explica cómo convertir una aplicación de Xamarin. Forms para usar .NET Standard 2,0. Estándar de .NET es una especificación de API de .NET que va a estar disponible en todas las implementaciones. NET.
ms.prod: xamarin
ms.assetid: 95805355-63a7-44e7-a3c6-6487a6276ab2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: eaef607e3e6095ccc7dfb1f5831d2384d11cab5f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760054"
---
# <a name="net-standard-20-support-in-xamarinforms"></a>Compatibilidad con .NET Standard 2,0 en Xamarin. Forms

_En este artículo se explica cómo convertir una aplicación de Xamarin. Forms para usar .NET Standard 2,0._

Estándar de .NET es una especificación de API de .NET que va a estar disponible en todas las implementaciones. NET. Resulta más fácil compartir código entre aplicaciones de escritorio, aplicaciones móviles, juegos y servicios de nube aunando las API idénticas a las diferentes plataformas. Para obtener información acerca de las plataformas compatibles con el estándar. NET, vea [soporte de implementación de .NET](/dotnet/standard/net-standard#net-implementation-support).

.NET Standard bibliotecas son el sustituto de las bibliotecas de clases portables (PCL). Sin embargo, una biblioteca que tiene como destino .NET Standard sigue siendo una PCL y se conoce como PCL basada en .NET Standard. Ciertos perfiles de PCL se asignan a las versiones de .NET Standard y, en el caso de los perfiles que tienen una asignación, los dos tipos de biblioteca podrán hacer referencia entre sí. Para obtener más información, consulte [compatibilidad con PCL](/dotnet/standard/net-standard#pcl-compatibility).

Xamarin. Forms 2,4 permite que las aplicaciones de Xamarin. Forms tengan como destino .NET Standard 2,0 sustituyendo la PCL por una biblioteca .NET Standard 2,0. Esto se puede lograr de la siguiente manera:

- Asegúrese de que [.net Core 2,0](https://www.microsoft.com/net/download/core) está instalado.
- Actualice la solución de Xamarin. Forms para usar Xamarin. Forms 2,4 o superior.
- Agregue una biblioteca de .NET Standard a la solución, que tiene como destino .NET Standard 2,0.
- Elimine la clase que se agrega a la biblioteca .NET Standard.
- Agregue el paquete de NuGet Xamarin. Forms 2,4 (o superior) a la biblioteca de .NET Standard.
- En los proyectos de la plataforma, agregue una referencia a la biblioteca de .NET Standard y quite la referencia al proyecto PCL que contiene la lógica de la interfaz de usuario de Xamarin. Forms.
- Copie los archivos del proyecto PCL a la biblioteca .NET Standard.
- Quite el proyecto PCL que contiene la lógica de la interfaz de usuario de Xamarin. Forms.

## <a name="related-links"></a>Vínculos relacionados

- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [Opciones de uso compartido de código](~/cross-platform/app-fundamentals/code-sharing.md)
