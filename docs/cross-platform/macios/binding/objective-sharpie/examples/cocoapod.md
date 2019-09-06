---
title: Ejemplo del mundo real con CocoaPods
description: En este documento se muestra cómo usar Objective Sharpie para generar automáticamente C# las definiciones de enlace a partir de un CocoaPod.
ms.prod: xamarin
ms.assetid: 233B781D-5841-4250-9F63-0585231D2112
author: conceptdev
ms.author: crdun
ms.date: 03/28/2018
ms.openlocfilehash: 0f730b1c0a0deacdb84c198cfe4af47308a268cc
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290030"
---
# <a name="real-world-example-using-cocoapods"></a>Ejemplo del mundo real con CocoaPods

> [!NOTE]
> En este ejemplo se usa [AFNetworking CocoaPod](https://cocoapods.org/pods/AFNetworking).

Como novedad en la versión 3,0, Objective Sharpie admite el enlace cocoapods e incluso incluye un`sharpie pod`comando () para facilitar la descarga, la configuración y la compilación de cocoapods. Debe [familiarizarse con CocoaPods](https://cocoapods.org) en general antes de usar esta característica.

## <a name="creating-a-binding-for-a-cocoapod"></a>Crear un enlace para un CocoaPod

El `sharpie pod` comando tiene una opción global y dos subcomandos:

```bash
$ sharpie pod -help
usage: sharpie pod [OPTIONS] COMMAND [COMMAND_OPTIONS]

Pod Options:
  -d, -dir DIR     Use DIR as the CocoaPods binding directory,
                   defaulting to the current directory

Available Commands:
  init         Initialize a new Xamarin C# CocoaPods binding project
  bind         Bind an existing Xamarin C# CocoaPods project
```

El `init` subcomando también tiene una ayuda útil:

```bash
$ sharpie pod init -help
usage: sharpie pod init [INIT_OPTIONS] TARGET_SDK POD_SPEC_NAMES

Init Options:
  -f, -force       Initialize a new Podfile and run actions against
                   it even if one already exists
```

Se pueden proporcionar varios nombres de CocoaPod y subespecificacións `init`a.

```bash
$ sharpie pod init ios AFNetworking
** Setting up CocoaPods master repo ...
   (this may take a while the first time)
** Searching for requested CocoaPods ...
** Working directory:
**   - Writing Podfile ...
**   - Installing CocoaPods ...
**     (running `pod install --no-integrate --no-repo-update`)
Analyzing dependencies
Downloading dependencies
Installing AFNetworking (2.6.0)
Generating Pods project
Sending stats
** 🍻 Success! You can now use other `sharpie podn`  commands.
```

Una vez configurada la CocoaPod, ahora puede crear el enlace:

```bash
$ sharpie pod bind
```

Esto hará que el proyecto CocoaPod Xcode se compile y, a continuación, se evalúe y analice por Objective Sharpie. Se generará una gran cantidad de resultados de la consola, pero debe generar la definición de enlace al final:

```bash
(... lots of build output ...)

Parsing 19 header files...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Done.
```

## <a name="next-steps"></a>Pasos siguientes

Después de generar los archivos **ApiDefinitions.CS** y **StructsAndEnums.CS** , eche un vistazo a la documentación siguiente para generar un ensamblado que se usará en las aplicaciones:

- [Información general sobre el enlace de Objective-C](~/cross-platform/macios/binding/overview.md)
- [Enlace de bibliotecas de Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Tutorial: Enlace de una biblioteca de Objective-C de iOS](~/ios/platform/binding-objective-c/walkthrough.md)
