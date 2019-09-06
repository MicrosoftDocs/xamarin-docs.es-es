---
title: Plataformas de Objective-C
description: En este documento se describen las distintas plataformas a las que la incrustación .NET puede dirigirse al trabajar con código de Objective-C. Se describen macOS, iOS, tvOS y watchos.
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
author: conceptdev
ms.author: crdun
ms.date: 11/14/2017
ms.openlocfilehash: f97b595f129cb1ad1ea56e3ae43b0f0a477fef5a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282727"
---
# <a name="objective-c-platforms"></a>Plataformas de Objective-C

La inserción de .NET puede tener como destino varias plataformas al generar código de Objective-C:

* macOS
* iOS
* tvOS
* watchos [aún no implementado]

La plataforma se selecciona pasando el `--platform=<platform>` argumento de línea de comandos a la inserción de .net.

Al compilar para las plataformas iOS, tvOS y watchos, la incrustación de .NET siempre creará un marco que inserta Xamarin. iOS, ya que Xamarin. iOS contiene una gran cantidad de código de compatibilidad en tiempo de ejecución que es necesario en estas plataformas.

Sin embargo, al compilar para la plataforma macOS, es posible elegir si el marco de trabajo generado debe incrustar Xamarin. Mac o no. Es posible no insertar Xamarin. Mac si el ensamblado enlazado no hace referencia a Xamarin. Mac. dll (ya sea directa o indirectamente) y se selecciona pasando `--platform=macOS` a la herramienta de incrustación de .net.

Si el ensamblado enlazado contiene una referencia a Xamarin. Mac. dll, es necesario insertar Xamarin. Mac y, además, el embeddinator debe saber qué plataforma de destino se va a usar.

Hay tres posibles plataformas de destino de Xamarin. Mac: `modern` (llamadas `mobile`anteriormente) `full` y `system` (la diferencia entre cada se describe en la documentación de la plataforma de [destino][1] de Xamarin. Mac) y cada una se selecciona pasando `--platform=macOS-modern` `--platform=macOS-full` o `--platform=macOS-system` a la herramienta de incrustación de .net.

[1]: ~/mac/platform/target-framework.md
