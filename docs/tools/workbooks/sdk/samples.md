---
title: Integraciones de ejemplo
description: Este documento contiene vínculos a ejemplos que muestran Xamarin Workbooks integraciones. Los ejemplos vinculados funcionan con representación y SkiaSharp de representación.
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: fa66ee2b2b469900381e2d31dc5dec49eb42c4f6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73018122"
---
# <a name="sample-integrations"></a>Integraciones de ejemplo

Vea el ejemplo de [receptor de cocina][KitchenSink] para ver un ejemplo práctico de una integración. Simplemente cree `KitchenSink.sln` en Visual Studio para Mac o Visual Studio y, a continuación, abra `KitchenSink.workbook`.

[Captura de pantalla de integración de receptor de cocina de![](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

El ejemplo de receptor de cocina muestra ambos conjuntos de conceptos:

* Las piezas de representación demuestran cómo usar `RepresentationManager` para mejorar la representación mediante el uso de las representaciones integradas.
* El objeto `Person` y su representador de JavaScript asociado muestran el uso de `ISerializableObject` sin pasar por un proveedor de representación.

Vea también [SkiaSharp][skiasharp] para ver un ejemplo del mundo real de una integración que usa las [representaciones](~/tools/workbooks/sdk/representations.md) existentes proporcionadas por Xamarin Workbooks para representar sus tipos.

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks
