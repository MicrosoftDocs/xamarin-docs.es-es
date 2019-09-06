---
title: Integraciones de ejemplo
description: Este documento contiene vínculos a ejemplos que muestran Xamarin Workbooks integraciones. Los ejemplos vinculados funcionan con representación y SkiaSharp de representación.
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: fbe471aa7f08d85a870d68505cf2c983b7e442e9
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292814"
---
# <a name="sample-integrations"></a>Integraciones de ejemplo

Vea el ejemplo de [receptor de cocina][KitchenSink] para ver un ejemplo práctico de una integración. Simplemente compile `KitchenSink.sln` en Visual Studio para Mac o Visual Studio y, a `KitchenSink.workbook`continuación, abra.

[![Captura de pantalla de integración del receptor de cocina](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

El ejemplo de receptor de cocina muestra ambos conjuntos de conceptos:

* Las piezas de representación demuestran cómo usar `RepresentationManager` para mejorar la representación mediante el uso de las representaciones integradas.
* El `Person` objeto y su representador de JavaScript asociado `ISerializableObject` muestran cómo usar sin pasar por un proveedor de representación.

Vea también [SkiaSharp][skiasharp] para ver un ejemplo del mundo real de una integración que usa las [representaciones](~/tools/workbooks/sdk/representations.md) existentes proporcionadas por Xamarin Workbooks para representar sus tipos.

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks
