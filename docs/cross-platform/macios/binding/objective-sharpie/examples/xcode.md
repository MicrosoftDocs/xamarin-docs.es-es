---
title: Ejemplo del mundo real con un proyecto de Xcode
description: En este documento se describe cómo usar un proyecto de Xcode como entrada directa para el objetivo Sharpie, lo que simplifica el C# proceso de creación de enlaces a código de Objective-C.
ms.prod: xamarin
ms.assetid: 168AA64C-E181-4937-A1F2-AD095B9A36F2
author: davidortinau
ms.author: daortin
ms.date: 01/15/2016
ms.openlocfilehash: 16191b7ee5bae353a438340d6099d39a296bffd0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016230"
---
# <a name="real-world-example-using-an-xcode-project"></a>Ejemplo del mundo real con un proyecto de Xcode

**En este ejemplo se usa la [biblioteca pop de Facebook](https://github.com/facebook/pop).**

Novedad de la versión 3,0, Objective Sharpie admite proyectos de Xcode como entrada. Estos proyectos especifican los archivos de encabezado y las marcas de compilador correctos necesarios para compilar la biblioteca nativa y, por tanto, es necesario enlazarla también. El objetivo Sharpie seleccionará el primer _destino_ y su configuración predeterminada de un proyecto si no se indica que lo haga.

Antes de que Objective Sharpie intente analizar los archivos de proyecto y de encabezado, debe compilarlos. Los proyectos suelen tener fases de compilación que estructurarán correctamente los archivos de encabezado para el consumo y la integración externos, por lo que es mejor compilar siempre el proyecto completo antes de intentar enlazarlo.

```
$ git clone https://github.com/facebook/pop.git
Cloning into 'pop'...
   (more git clone output)

$ cd pop
$ sharpie bind pop.xcodeproj -sdk iphoneos9.0
```
