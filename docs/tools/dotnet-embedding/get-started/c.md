---
title: introducción a C
description: En este documento se describe cómo usar la inserción de .NET para insertar código de .NET en una aplicación de C. Describe cómo usar la inserción de .NET en Visual Studio 2019 y Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: 2A27BE0F-95FB-4C3A-8A43-72540179AA85
author: davidortinau
ms.author: daortin
ms.date: 04/19/2018
ms.openlocfilehash: 9660eccd3e14a6c4ecad901064650fe1e25458f8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029780"
---
# <a name="getting-started-with-c"></a>introducción a C

## <a name="requirements"></a>Requisitos

Para usar la inserción de .NET con C, necesitará un equipo Mac o Windows que ejecute:

### <a name="macos"></a>macOS

* macOS 10,12 (Sierra) o posterior
* Xcode 8.3.2 o posterior
* [Mono](https://www.mono-project.com/download/)

### <a name="windows"></a>Windows

* Windows 7, 8, 10 o posterior
* Visual Studio 2015 o posterior

## <a name="installing-net-embedding-from-nuget"></a>Instalación de la inserción de .NET desde NuGet

Siga estas [instrucciones](~/tools/dotnet-embedding/get-started/install/install.md) para instalar y configurar la inserción de .net para su proyecto.

La invocación del comando que debe configurar tendrá el siguiente aspecto (posiblemente con distintos números de versión y rutas de acceso):

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

```shell
mono {SolutionDir}/packages/Embeddinator-4000.0.4.0.0/tools/Embeddinator-4000.exe --gen=c --outdir=managed_c --platform=macos --compile managed.dll
```

### <a name="visual-studio-2017"></a>Visual Studio 2017

```shell
$(SolutionDir)\packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe --gen=c --outdir=managed_c --platform=windows --compile managed.dll
```

## <a name="generation"></a>Generación

### <a name="output-files"></a>archivos de salida

Si todo va bien, se mostrará el siguiente resultado:

```shell
Parsing assemblies...
    Parsed 'managed.dll'
Processing assemblies...
Generating binding code...
    Generated: managed.h
    Generated: managed.c
    Generated: mscorlib.h
    Generated: mscorlib.c
    Generated: embeddinator.h
    Generated: glib.c
    Generated: glib.h
    Generated: mono-support.h
    Generated: mono_embeddinator.c
    Generated: mono_embeddinator.h
```

Dado que la marca de `--compile` se ha pasado a la herramienta, la incrustación de .NET también debe haber compilado los archivos de salida en una biblioteca compartida, que puede encontrar junto a los archivos generados, un archivo **libmanaged. dylib** en MacOS y el archivo **. dll administrado** en Windows.

Para consumir la biblioteca compartida, puede incluir el archivo de encabezado **administrado. h** c, que proporciona las declaraciones de c correspondientes a las API de biblioteca administrada respectivas y un vínculo con la biblioteca compartida compilada anteriormente mencionada.

## <a name="further-reading"></a>Información adicional

* [Limitaciones de la inserción de .NET](~/tools/dotnet-embedding/limitations.md)
* [Contribuir al proyecto de código abierto](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Códigos de error y descripciones](~/tools/dotnet-embedding/errors.md)
