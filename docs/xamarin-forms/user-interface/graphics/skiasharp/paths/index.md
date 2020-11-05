---
title: Trazados y líneas de SkiaSharp
description: En este artículo se explica cómo usar SkiaSharp para dibujar líneas y trazados de gráficos en Xamarin.Forms aplicaciones, y se muestra con código de ejemplo.
ms.prod: xamarin
ms.assetid: 316A15FE-383D-4D06-8641-BAC7EE7474CA
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1c5a83a626b9a0fab55b84c7d8cc8e4fbaea3683
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375257"
---
# <a name="skiasharp-lines-and-paths"></a>Trazados y líneas de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Usar SkiaSharp para dibujar líneas y trazados de gráficos_

En la [sección anterior](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md) se mostró que la `SKCanvas` clase SkiaSharp incluye varios métodos para dibujar círculos, óvalos, rectángulos, rectángulos redondeados, texto y mapas de bits. En esta sección y en secciones posteriores se tratan las distintas clases conectadas con la creación y representación de *trazados de gráficos*.

La ruta de acceso de los gráficos es el enfoque más generalizado para dibujar líneas y curvas en SkiaSharp. En esta sección se describe el uso [`SKPath`](xref:SkiaSharp.SKPath) de un objeto para dibujar líneas rectas y el uso de una colección de pequeñas líneas rectas (denominada *polilínea* ) para dibujar curvas que se pueden definir de manera algorítmica. En una sección posterior de [**curvas y trazados de SkiaSharp**](../curves/index.md) se describen los distintos tipos de curvas admitidas por `SKPath` .

Todos los programas de ejemplo de esta sección aparecen en las **líneas** de encabezado y las rutas de acceso en la Página principal del programa [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) y en la carpeta [**rutas**](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths) de acceso de la solución.

## <a name="lines-and-stroke-caps"></a>[Líneas y extremos de trazo](lines.md)

Aprenda a usar SkiaSharp para dibujar líneas con distintos extremos de trazo.

## <a name="path-basics"></a>[Conceptos básicos del trazado](paths.md)

Explore el `SKPath` objeto SkiaSharp para combinar líneas y curvas.

## <a name="the-path-fill-types"></a>[Tipos de relleno del trazado](fill-types.md)

Descubra los distintos efectos posibles con los tipos de relleno de ruta de acceso de SkiaSharp.

## <a name="polylines-and-parametric-equations"></a>[Polilíneas y ecuaciones paramétricas](polylines.md)

Use SkiaSharp para representar cualquier línea que pueda definir con ecuaciones paramétricas.

## <a name="dots-and-dashes"></a>[Puntos y guiones](dots.md)

Domine las complejidades del dibujo de líneas de puntos y guiones en SkiaSharp.

## <a name="finger-painting"></a>[Pintura con los dedos](finger-paint.md)

Use los dedos para pintar en el lienzo.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)