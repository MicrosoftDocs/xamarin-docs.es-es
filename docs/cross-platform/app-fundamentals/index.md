---
title: Compartir código en varias plataformas
description: Este documento contiene vínculos a varias guías que describen técnicas para compartir código, incluidas bibliotecas de clases portables, proyectos compartidos, .NET Standard y NuGet.
ms.prod: xamarin
ms.assetid: 7D179ACF-09A6-46EE-B49D-E27AB5F09CD4
author: davidortinau
ms.author: daortin
ms.date: 07/18/2018
ms.openlocfilehash: a91fba3cd1fba3bcf2317e8f9cb25631c62491cb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016829"
---
# <a name="sharing-code-on-multiple-platforms"></a>Compartir código en varias plataformas

En estos artículos se explican las distintas opciones disponibles para compartir código entre plataformas, como Windows, Android, iOS, etc.

## <a name="code-sharing-overviewcode-sharingmd"></a>[Información general sobre el uso compartido de código](code-sharing.md)

Obtenga información sobre las distintas opciones de uso compartido de código disponibles para los proyectos de Xamarin, incluidas las bibliotecas de .NET Standard y los proyectos compartidos. También se admiten bibliotecas de clases portables, pero se consideran en desuso en favor de .NET Standard.

## <a name="net-standardcross-platformapp-fundamentalsnet-standardmd"></a>[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)

.NET Standard es la opción preferida para compartir código entre plataformas. El código se compila con una versión específica (2,0 proporciona la mejor compatibilidad con la API con el código de .NET Framework existente) y puede ser consumido por otros proyectos que admitan ese nivel o superior. .NET Standard proyectos se admiten en Visual Studio 2019 y Visual Studio 2019 para Mac.

## <a name="shared-projectscross-platformapp-fundamentalsshared-projectsmd"></a>[Proyectos compartidos](~/cross-platform/app-fundamentals/shared-projects.md)

Los proyectos compartidos permiten escribir código común al que hacen referencia varios proyectos de aplicación diferentes. El código se compila como parte de cada proyecto de referencia y puede incluir directivas de compilador para ayudar a incorporar la funcionalidad específica de la plataforma en la base de código compartido. En este artículo se describe cómo funcionan los proyectos compartidos y cómo crearlos y usarlos con los proyectos de Xamarin.

## <a name="portable-class-librariescross-platformapp-fundamentalspclmd"></a>[Bibliotecas de clases portables](~/cross-platform/app-fundamentals/pcl.md)

Los proyectos de biblioteca de clases portable permiten compilar y distribuir ensamblados que contienen código compartido para ejecutarse en varias plataformas. Para crear una biblioteca de clases portable (o "PCL"), primero seleccione las plataformas de destino y, a continuación, escriba el código en un subconjunto de la .NET Framework que esté disponible en el perfil definido para esas plataformas. PCL se considera que están en desuso en las versiones más recientes de Visual Studio; se recomienda a los desarrolladores que utilicen .NET Standard 2,0 en su lugar.

## <a name="nuget-projects-multiplatform-libraries-for-code-sharingcross-platformapp-fundamentalsnuget-multiplatform-librariesindexmd"></a>[Proyectos de NuGet: bibliotecas multiplataforma para el uso compartido de código](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)

Los paquetes NuGet se pueden generar automáticamente a partir de proyectos PCL o de .NET Standard. y los proyectos compartidos se pueden empaquetar en los paquetes NuGet "cebo and switch" con el tipo de proyecto de NuGet independiente. En esta sección se explica cómo crear paquetes NuGet para cada escenario de uso compartido de código.

## <a name="manually-creating-nuget-packages-for-xamarincross-platformapp-fundamentalsnuget-manualmd"></a>[Creación manual de paquetes NuGet para Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md)

Sugerencias para crear paquetes NuGet que funcionan con la plataforma Xamarin.

## <a name="use-cc-libraries-in-cross-platform-xamarin-projectscross-platformcppindexmd"></a>[Usar bibliotecas deC++ C/en proyectos de Xamarin multiplataforma](~/cross-platform/cpp/index.md)

Esta técnica le permite desacoplar la evolución de las bibliotecas deC++ C/, C# un enlace en un NuGet y las aplicaciones de Xamarin. La biblioteca C/C++ Library nativa proporciona la funcionalidad, pero todo el código específico de la plataforma está aislado de las aplicaciones finales de Xamarin, lo que permite el mayor rendimiento posible sin duplicación de código. 
