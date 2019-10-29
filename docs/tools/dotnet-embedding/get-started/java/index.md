---
title: Introducción a Java
description: En este documento se describe cómo empezar a usar la inserción de .NET con Java. Se describen los requisitos del sistema, la instalación y las plataformas admitidas.
ms.prod: xamarin
ms.assetid: B9A25E9B-3EC2-489A-8AD3-F78287609747
author: davidortinau
ms.author: daortin
ms.date: 03/28/2018
ms.openlocfilehash: 09ea33724c2b1184654ce7768ea1cb2525b62c28
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73007378"
---
# <a name="getting-started-with-java"></a>Introducción a Java

Esta es la página de introducción para Java, en la que se describen los aspectos básicos de todas las plataformas compatibles.

## <a name="requirements"></a>Requisitos

Para usar la inserción de .NET con Java, necesitará lo siguiente:

* Java 1,8 o posterior
* [Mono 5,0](https://www.mono-project.com/download/)

Para Mac:

* Xcode 8.3.2 o posterior

Para Windows:

* Visual Studio 2017 con C++ compatibilidad
* SDK de Windows 10

Para Android:

* [Xamarin. Android 7,5](https://visualstudio.microsoft.com/xamarin/) o posterior
* [Android Studio 3. x](https://developer.android.com/studio/index.html) con Java 1,8

Puede usar [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) para editar y compilar C# el código.

> [!NOTE]
> Las versiones anteriores de Xcode, Visual Studio, Xamarin. Android, Android Studio y mono _podrían_ funcionar, pero no se prueban y no se admiten.

## <a name="installation"></a>Instalación

La inserción de .NET está disponible actualmente en [NuGet](https://www.nuget.org/packages/Embeddinator-4000/):

```shell
nuget install Embeddinator-4000
```

Esto colocará **Embeddinator-4000. exe** en el directorio **Packages/Embeddinator-4000/Tools** .

Además, puede crear la incrustación de .NET desde el origen, ver nuestro [repositorio git](https://github.com/mono/Embeddinator-4000/) y el documento [contribuyente](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md) para obtener instrucciones.

## <a name="platforms"></a>Plataformas

Java está actualmente en estado de vista previa para macOS, Windows y Android.

La plataforma se selecciona pasando el argumento de línea de comandos `--platform=<platform>` a la herramienta de inserción de .NET. Actualmente se admiten `macOS`, `Windows`y `Android`.

### <a name="macos-and-windows"></a>macOS y Windows

Para el desarrollo, debería poder usar cualquier IDE de Java que admita Java 1,8. Puede incluso usar Android Studio para esto si lo desea, [Consulte aquí](https://stackoverflow.com/questions/16626810/can-android-studio-be-used-to-run-standard-java-projects). Puede usar la salida del archivo JAR como lo haría con cualquier archivo jar estándar de Java.

### <a name="android"></a>Android

Asegúrese de que ya está configurado para desarrollar aplicaciones de Android antes de intentar crear una mediante la inserción de .NET. Las [instrucciones siguientes](~/tools/dotnet-embedding/get-started/java/android.md) suponen que ya ha creado e implementado correctamente una aplicación de Android desde el equipo.

Android Studio se recomienda para el desarrollo, pero otros IDE deberían funcionar siempre y cuando exista compatibilidad con el [formato de archivo AAR](https://developer.android.com/studio/projects/android-library.html).

## <a name="further-reading"></a>Información adicional

* [Introducción en Android](~/tools/dotnet-embedding/get-started/java/android.md)
* [Devoluciones de llamada en Android](~/tools/dotnet-embedding/android/callbacks.md)
* [Investigación preliminar de Android](~/tools/dotnet-embedding/android/index.md)
* [Limitaciones de la inserción de .NET](~/tools/dotnet-embedding/limitations.md)
* [Contribuir al proyecto de código abierto](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Códigos de error y descripciones](~/tools/dotnet-embedding/errors.md)
