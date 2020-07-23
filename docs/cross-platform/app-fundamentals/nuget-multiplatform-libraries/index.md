---
title: Proyectos de biblioteca de múltiples plataformas NuGet (también conocido como Nugetizer 3000)
description: En este documento se describe cómo usar la herramienta Nugetizer 3000 para crear automáticamente paquetes NuGet para compartir código entre plataformas.
ms.prod: xamarin
ms.assetid: F0A5A9BB-86CD-44C9-8EE8-74D1E5E74A30
author: davidortinau
ms.author: daortin
ms.date: 07/25/2018
ms.openlocfilehash: ac8e4b2eef0905ba50cb24b9eb035d50a05dd84a
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86932253"
---
# <a name="nuget-multiplatform-library-projects-nugetizer-3000"></a>Proyectos de biblioteca de múltiples plataformas NuGet (Nugetizer 3000)

_Cree paquetes NuGet automáticamente para compartir código entre plataformas mediante el uso de "Nugetizer 3000"._

Es posible crear automáticamente paquetes NuGet para compartir código entre plataformas mediante _Nugetizer 3000_. Esto hace posible la creación de paquetes NuGet a partir de proyectos de biblioteca existentes o mediante la creación de un nuevo **proyecto de biblioteca multiplataforma**.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Nugetizer 3000 está incluido en Visual Studio para Mac &ndash; Buscar el tipo de proyecto biblioteca **> biblioteca Mulitplatform** en el **archivo > nueva** ventana:

[![Crear nueva ventana biblioteca multiplataforma](images/mulitplatform-library-sml.png)](images/mulitplatform-library.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para usar Nugetizer 3000 en Visual Studio, [Descargue y ejecute el instalador de VSIX](https://bit.ly/nugetizer-2017).

-----

## <a name="building-nuget-packages"></a>Compilar paquetes NuGet

Una vez configurada, cada compilación del proyecto genera un paquete NuGet completo, que se puede usar para compartir código internamente con otras aplicaciones o cargarse en [NuGet.org](https://www.nuget.org).

Existen tres escenarios para usar esta característica:

- [Proyectos de biblioteca existentes](existing-library.md)

  Cree un paquete NuGet a partir de proyectos PCL (o .NET Standard) existentes.

- [Crear un nuevo proyecto de biblioteca multiplataforma](single-codebase.md)

  Cree una nueva biblioteca para compartir código común a través de NuGet mediante una PCL o .NET Standard.

- [Crear nuevos proyectos de biblioteca específicos de la plataforma](platform-specific.md)

  Cree una nueva biblioteca y NuGet que incluya código específico de la plataforma para iOS y Android, y use un proyecto compartido para contener el código común y los proyectos específicos de la plataforma para admitir la funcionalidad específica de iOS o Android.

Consulte la [Guía de metadatos](metadata.md) para obtener más información sobre los metadatos necesarios y opcionales que se deben agregar a cualquier paquete de NuGet.

## <a name="further-nuget-information"></a>Más información de NuGet

Obtenga más información sobre la [creación manual de paquetes Nuget para Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md) y cómo [incluir un paquete NuGet en una aplicación](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

La [documentación de Nuget](https://docs.microsoft.com/nuget/) de Microsoft contiene información más detallada sobre el formato **. nupkg** y el uso de paquetes NuGet en Visual Studio.

La explicación del diseño de los proyectos de paquetes NuGet (también conocido como NuGetizer 3000) está disponible en el [repositorio de github de NuGet](https://github.com/NuGet/Home/wiki/NuGetizer-3000).

## <a name="related-links"></a>Vínculos relacionados

- [Casos de uso de NuGetizer-3000](https://github.com/NuGet/Home/wiki/NuGetizer-Core-Scenarios)
- [Creación manual de paquetes NuGet para Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md)
- [Documentación de NuGet](https://docs.microsoft.com/nuget/)
