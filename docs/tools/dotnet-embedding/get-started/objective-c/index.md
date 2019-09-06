---
title: Introducción a Objective-C
description: En este documento se describe cómo empezar a usar la inserción de .NET con Objective-C. Se describen los requisitos, la instalación de la inserción de .NET desde NuGet y las plataformas admitidas.
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
author: conceptdev
ms.author: crdun
ms.date: 11/14/2017
ms.openlocfilehash: 66b99e0da574c50df32afedb1dd6dc9de315b347
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278465"
---
# <a name="getting-started-with-objective-c"></a>Introducción a Objective-C

Esta es la página de introducción de Objective-C, que cubre los aspectos básicos de todas las plataformas admitidas.

## <a name="requirements"></a>Requisitos

Para usar la inserción de .NET con Objective-C, necesitará un equipo Mac que ejecute:

- macOS 10,12 (Sierra) o posterior
- Xcode 8.3.2 o posterior
- [Mono 5,0](https://www.mono-project.com/download/)

Puede instalar [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) para editar y compilar C# el código.

> [!NOTE]
> - Las versiones anteriores de macOS, Xcode y mono _podrían_ funcionar, pero no se prueban y no se admiten.
> - La generación de código se puede realizar en Windows, pero solo es posible compilarla en un equipo Mac en el que esté instalado Xcode

## <a name="installing-net-embedding-from-nuget"></a>Instalación de la inserción de .NET desde NuGet

Siga estas [instrucciones](~/tools/dotnet-embedding/get-started/install/install.md) para instalar y configurar la inserción de .net para su proyecto.

En las guías de introducción de [MacOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) e [iOS](~/tools/dotnet-embedding/get-started/objective-c/ios.md) se muestra una invocación de comando de ejemplo.

## <a name="platforms"></a>Plataformas

Objective-C es un lenguaje que se usa con más frecuencia para escribir aplicaciones para macOS, iOS, tvOS y watchos; la incrustación de .NET es compatible con todas esas plataformas. Al trabajar con cada plataforma se implican algunas [diferencias clave, que se explican aquí](~/tools/dotnet-embedding/objective-c/platforms.md).

### <a name="macos"></a>macOS

[La creación de una aplicación MacOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) es más fácil, ya que no implica tantos pasos adicionales, como la configuración de identidades, perfiles creando, simuladores y dispositivos. Se recomienda comenzar con el documento de macOS antes que el de iOS.

### <a name="ios--tvos"></a>iOS/tvOS

Asegúrese de que ya está configurado para desarrollar aplicaciones de iOS antes de intentar crear una mediante la inserción de .NET. Las [instrucciones siguientes](~/tools/dotnet-embedding/get-started/objective-c/ios.md) suponen que ya ha creado e implementado correctamente una aplicación de iOS desde el equipo.

La compatibilidad con tvOS es análoga a cómo funciona iOS, simplemente mediante el uso de proyectos de tvOS en los IDE (tanto Visual Studio como Xcode) en lugar de los proyectos de iOS.

> [!NOTE]
> La compatibilidad con watchos estará disponible en una versión futura y será muy similar a iOS/tvOS.

## <a name="further-reading"></a>Información adicional

- [Características de inserción de .NET específicas para Objective-C](~/tools/dotnet-embedding/objective-c/index.md)
- [Prácticas recomendadas para Objective-C](~/tools/dotnet-embedding/objective-c/best-practices.md)
- [Limitaciones de la inserción de .NET](~/tools/dotnet-embedding/limitations.md)
- [Contribuir al proyecto de código abierto](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
- [Códigos de error y descripciones](~/tools/dotnet-embedding/errors.md)
- [Plataformas de destino](~/tools/dotnet-embedding/objective-c/platforms.md)

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo meteorológico (iOS & macOS)](https://github.com/jamesmontemagno/embeddinator-weather)
