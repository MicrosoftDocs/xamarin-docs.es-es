---
title: Cómo resolver un error PathTooLongException?
description: En este artículo se explica cómo resolver un PathTooLongException que puede producirse al compilar una aplicación.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/29/2018
ms.openlocfilehash: ffe88546ff58387865d71268bd64ec05c8aec3c5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026787"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>Cómo resolver un error PathTooLongException?

## <a name="cause"></a>Motivo

Los nombres de ruta de acceso generados en un proyecto de Xamarin. Android pueden ser bastante largos.
Por ejemplo, se podría generar una ruta de acceso como la siguiente durante una compilación:

**C:\\algunos\\de la solución de\\de directorios\\Project\\obj\\Debug\\__library_projects__\\Xamarin. Forms. Platform. Android\\library_project_imports\\Assets**

En Windows (donde la longitud máxima de una ruta de acceso es de [260 caracteres](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)), se podría producir **PathTooLongException** al compilar el proyecto si una ruta de acceso generada supera la longitud máxima. 

## <a name="fix"></a>Solución

La propiedad `UseShortFileNames` MSBuild está establecida en `True` para evitar este error de forma predeterminada. Cuando esta propiedad se establece en `True`, el proceso de compilación usa nombres de ruta de acceso más cortos para reducir la probabilidad de producir **PathTooLongException**.
Por ejemplo, cuando `UseShortFileNames` está establecido en `True`, la ruta de acceso anterior se acorta a la ruta de acceso similar a la siguiente:

**C:\\algunos\\de la solución de\\de directorios\\Project\\obj\\Debug\\LP\\1\\JL\\Assets**

Para establecer esta propiedad manualmente, agregue la siguiente propiedad de MSBuild al archivo **. csproj** del proyecto:

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

Si al establecer esta marca no se corrige el error **PathTooLongException** , otro enfoque consiste en especificar una [raíz de salida intermedia común](https://blogs.msdn.microsoft.com/kirillosenkov/2015/04/04/using-a-common-intermediate-and-output-directory-for-your-solution/) para los proyectos de la solución estableciendo `IntermediateOutputPath` en el archivo **. csproj** del proyecto. Intente usar una ruta de acceso relativamente corta. Por ejemplo:

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

Para obtener más información sobre cómo establecer las propiedades de compilación, vea [proceso de compilación](~/android/deploy-test/building-apps/build-process.md).
