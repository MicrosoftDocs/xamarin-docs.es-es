---
title: Inserción de .NET
description: La incrustación de .net permite que elC#código F#.net existente (, y otros) lo consuma el código escrito en otros lenguajes de programación.
ms.prod: xamarin
ms.assetid: 617C38CA-B921-4A76-8DFC-B0A3DF90E48A
author: conceptdev
ms.author: crdun
ms.date: 11/14/2017
ms.openlocfilehash: af068e5a09cc11eec33508a4f2eb33186168aae6
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290222"
---
# <a name="net-embedding"></a>Inserción de .NET

![Vista previa](~/media/shared/preview.png)

La inserción de .net permite que el códigoC#.net F#existente (, y otros) se consuma desde otros lenguajes de programación y en distintos entornos.

Esto significa que si tiene una biblioteca de .NET que quiere usar desde la aplicación de iOS existente, puede hacerlo.   O bien, si desea vincularlo a una biblioteca C++ nativa, también puede hacerlo.   O consumir código .NET desde Java.

La inserción de .NET se basa en el proyecto de código abierto [Embeddinator-4000](https://github.com/mono/Embeddinator-4000) .

## <a name="environments-and-languages"></a>Entornos e idiomas

La herramienta es consciente del entorno que va a utilizar, así como del lenguaje que lo consumirá.   Por ejemplo, la plataforma iOS no permite la compilación Just-in-Time (JIT), por lo que la inserción de .NET compilará estáticamente el código de .NET en código nativo que se puede usar en iOS.  Otros entornos permiten la compilación JIT y, en esos entornos, optamos por la compilación JIT.

Admite varios consumidores de lenguajes, por lo que muestra código .NET como código idiomático en el lenguaje de destino.   Esta es la lista de idiomas admitidos en este momento:

- [**Objective-c**](objective-c/index.md) : asignación de .net a las API de Objective-c de idiomático
- [**Java**](android/index.md) : asignación de .net a API de Java de idiomático
- [**C**](get-started/c.md) : asignar .net a objetos orientados a objetos como las API de c

Habrá más idiomas más adelante.

## <a name="getting-started"></a>Introducción

Para empezar, compruebe una de nuestras guías para cada uno de los idiomas admitidos actualmente:

- [**Objective-C**](get-started/objective-c/index.md) : trata de MacOS e iOS
- [**Java**](get-started/java/index.md) : cubre MacOS y Android
- [**C**](get-started/c.md) : trata el lenguaje c en plataformas de escritorio

## <a name="related-links"></a>Vínculos relacionados

- [Embeddinator-4000 en GitHub](https://github.com/mono/Embeddinator-4000)
