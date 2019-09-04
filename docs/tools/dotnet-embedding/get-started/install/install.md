---
title: Instalación de la inserción de .NET
description: En este documento se describe cómo instalar la incrustación de .NET. Explica cómo ejecutar las herramientas de forma manual, cómo generar enlaces automáticamente, cómo usar destinos de MSBuild personalizados y pasos posteriores a la compilación necesarios.
ms.prod: xamarin
ms.assetid: 47106AF3-AC6E-4A0E-B30B-9F73C116DDB3
author: chamons
ms.author: chhamo
ms.date: 04/18/2018
ms.openlocfilehash: 47efbaa12475f627b5963cb6613c3441a1d96aac
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227842"
---
# <a name="installing-net-embedding"></a>Instalación de la inserción de .NET

## <a name="installing-net-embedding-from-nuget"></a>Instalación de la inserción de .NET desde NuGet

Elija **agregar > agregar paquetes Nuget...** e instalar **Embeddinator-4000** desde el administrador de paquetes Nuget:

![Administrador de paquetes de NuGet](images/visualstudionuget.png)

Se instalarán **Embeddinator-4000. exe** y **objcgen** en el directorio **Packages/Embeddinator-4000/Tools** .

En general, se debe seleccionar la versión más reciente de Embeddinator-4000 para su descarga. La compatibilidad con Objective-C requiere 0,4 o posterior.

## <a name="running-manually"></a>Ejecutar manualmente

Ahora que NuGet está instalado, puede ejecutar las herramientas manualmente.

- Abra un terminal (macOS) o un símbolo del sistema (Windows)
- Cambiar el directorio a la raíz de la solución
- Las herramientas se instalan en:
  - **./Packages/Embeddinator-4000. [Versión]/Tools/objcgen** (Objective-C)
  - **./Packages/Embeddinator-4000. [Versión]/tools/Embeddinator-4000.exe** (Java/C)
- En macOS, **objcgen** se puede ejecutar directamente.
- En Windows, **Embeddinator-4000. exe** se puede ejecutar directamente.
- En macOS, **Embeddinator-4000. exe** debe ejecutarse con **mono**:
  - `mono ./packages/Embeddinator-4000.[VERSION]/tools/Embeddinator-4000.exe`

Cada invocación de comando necesitará una serie de parámetros que se enumeran en la documentación específica de la plataforma.

## <a name="automatic-binding-generation"></a>Generación automática de enlaces

Hay dos enfoques para la ejecución automática de la inserción de .NET del proceso de compilación.

- Destinos de MSBuild personalizados
- Pasos posteriores a la compilación

Aunque en este documento se describirán ambos, se prefiere el uso de un destino MSBuild personalizado. Los pasos posteriores a la compilación no se ejecutarán necesariamente al compilar desde la línea de comandos.

### <a name="custom-msbuild-targets"></a>Destinos de MSBuild personalizados

Para personalizar la compilación con destinos de MSbuild, cree primero un archivo **Embeddinator-4000. targets** junto a su csproj similar al siguiente:

```xml
<Project DefaultTargets="Build" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <Target Name="RunEmbeddinator" AfterTargets="AfterBuild" Inputs="$(OutputPath)/$(AssemblyName).dll" Outputs="$(IntermediateOutputPath)/Embeddinator/$(AssemblyName).framework/$(AssemblyName)">
        <Exec Command="" />
    </Target>
</Project>
```

Aquí, el texto del comando se debe rellenar con una de las invocaciones de la inserción de .NET que se enumeran en la documentación específica de la plataforma.

Para usar este destino:

- Cierre el proyecto en Visual Studio 2017 o Visual Studio para Mac
- Abra el archivo csproj de la biblioteca en un editor de texto
- Agregue esta línea en la parte inferior, encima de `</Project>` la última línea:

```xml
 <Import Project="Embeddinator-4000.targets" />
```

- Vuelva a abrir el proyecto

### <a name="post-build-steps"></a>Pasos posteriores a la compilación

Los pasos para agregar un paso posterior a la compilación para ejecutar la incrustación de .NET varían en función del IDE:

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

En Visual Studio para Mac, vaya a **Opciones de proyecto > Compilar > comandos personalizados** y agregue un paso después de la **compilación** .

Configure el comando que aparece en la documentación específica de la plataforma.

> [!NOTE]
> Asegúrese de usar el número de versión que instaló desde NuGet.

Si va a realizar el desarrollo continuo en el C# proyecto, también puede Agregar un comando personalizado para limpiar el directorio de **salida** antes de ejecutar la inserción de .net:

```shell
rm -Rf '${SolutionDir}/output/'
```

![Acción de compilación personalizada](images/visualstudiocustombuild.png)

> [!NOTE]
> El enlace generado se colocará en el directorio indicado por `--outdir` el `--out` parámetro o. El nombre de enlace generado puede variar a medida que algunas plataformas tienen limitaciones en los nombres de paquete.

#### <a name="visual-studio-2017"></a>Visual Studio 2017

Básicamente se configura lo mismo, pero los menús de Visual Studio 2017 son un poco diferentes. Los comandos de Shell también son ligeramente diferentes.

Vaya a **Opciones de proyecto > eventos de compilación** y escriba el comando que aparece en la documentación específica de la plataforma en el cuadro **línea de comandos del evento posterior a la compilación** . Por ejemplo:

![Incrustación de .NET en Windows](images/visualstudiowindows.png)
 