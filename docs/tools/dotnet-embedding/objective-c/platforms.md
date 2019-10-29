---
title: Plataformas de Objective-C
description: En este documento se describen las distintas plataformas a las que la incrustación .NET puede dirigirse al trabajar con código de Objective-C. Se describen macOS, iOS, tvOS y watchos.
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: dbc2c40e06f14ec636b4aa4cc11fc4f2d230ee6d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73006423"
---
# <a name="objective-c-platforms"></a>Plataformas de Objective-C

La inserción de .NET puede tener como destino varias plataformas al generar código de Objective-C:

* macOS
* iOS
* tvOS
* watchos [aún no implementado]

La plataforma se selecciona pasando el argumento de línea de comandos `--platform=<platform>` a la inserción de .NET.

Al compilar para las plataformas iOS, tvOS y watchos, la incrustación de .NET siempre creará un marco que inserta Xamarin. iOS, ya que Xamarin. iOS contiene una gran cantidad de código de compatibilidad en tiempo de ejecución que es necesario en estas plataformas.

Sin embargo, al compilar para la plataforma macOS, es posible elegir si el marco de trabajo generado debe incrustar Xamarin. Mac o no. Es posible no insertar Xamarin. Mac si el ensamblado enlazado no hace referencia a Xamarin. Mac. dll (ya sea directa o indirectamente) y se selecciona pasando `--platform=macOS` a la herramienta de inserción de .NET.

Si el ensamblado enlazado contiene una referencia a Xamarin. Mac. dll, es necesario insertar Xamarin. Mac y, además, el embeddinator debe saber qué plataforma de destino se va a usar.

Hay tres plataformas de destino de Xamarin. Mac posibles: `modern` (anteriormente denominado `mobile`), `full` y `system` (la diferencia entre cada se describe en la documentación de la [plataforma de destino][1] de Xamarin. Mac) y cada una se selecciona pasando `--platform=macOS-modern`, `--platform=macOS-full` o `--platform=macOS-system` a la herramienta de incrustación de .NET.

[1]: ~/mac/platform/target-framework.md
