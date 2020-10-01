---
title: ¿Cómo se puede resolver un error PathTooLongException?
description: En este artículo se explica cómo resolver un error PathTooLongException que puede producirse al compilar una aplicación.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/29/2018
ms.openlocfilehash: d58cb676b347caac00c39a381de94954219d1865
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456865"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>¿Cómo se puede resolver un error PathTooLongException?

## <a name="cause"></a>Motivo

Los nombres de ruta de acceso generados en un proyecto de Xamarin.Android pueden ser bastante largos.
Por ejemplo, se podría generar una ruta de acceso como la siguiente durante una compilación:

**C:\\Some\\Directory\\Solution\\Project\\obj\\Debug\\__library_projects__\\Xamarin.Forms.Platform.Android\\library_project_imports\\assets**

En Windows (donde la longitud máxima de una ruta de acceso es [260 caracteres](/windows/win32/fileio/naming-a-file)), se podría producir un error **PathTooLongException** al compilar el proyecto si una ruta de acceso generada supera la longitud máxima. 

## <a name="fix"></a>Solución

La propiedad `UseShortFileNames` de MSBuild está establecida en `True` para evitar este error de forma predeterminada. Cuando esta propiedad se establece en `True`, el proceso de compilación usa nombres de ruta de acceso más cortos para reducir la probabilidad de producir un error **PathTooLongException**.
Por ejemplo, cuando `UseShortFileNames` se establece en `True`, la ruta de acceso anterior se acorta a una ruta de acceso similar a la siguiente:

**C:\\Some\\Directory\\Solution\\Project\\obj\\Debug\\lp\\1\\jl\\assets**

Para establecer esta propiedad manualmente, agregue la siguiente propiedad de MSBuild al archivo **.csproj** del proyecto:

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

Si no se corrige el error **PathTooLongException**, otro método consiste en especificar una [raíz de salida intermedia común](/archive/blogs/kirillosenkov/using-a-common-intermediate-and-output-directory-for-your-solution) para los proyectos de la solución. Para ello, establezca `IntermediateOutputPath` en el archivo **.csproj** del proyecto. Intente usar una ruta de acceso relativamente corta. Por ejemplo:

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

Para obtener más información sobre cómo establecer las propiedades de compilación, vea [Proceso de compilación](~/android/deploy-test/building-apps/build-process.md).