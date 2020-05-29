---
title: ''
description: ''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d9fa710f5dfc61c2892b8fc409a39b37cf449018
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136310"
---
# <a name="skiasharp-effects"></a>Efectos SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

La [`SKPaint`](xref:SkiaSharp.SKPaint) clase SkiaSharp define seis propiedades que se pueden clasificar en términos generales de _efectos_. Se trata de propiedades que modifican la presentación normal de gráficos de alguna manera. Los efectos de SkiaSharp se dividen en seis categorías:

## <a name="path-effects"></a>[Efectos del trazado](../curves/effects.md)

Establezca la [`PathEffect`](xref:SkiaSharp.SKPaint.PathEffect) propiedad de `SKPaint` en un objeto de tipo [`SKPathEffect`](xref:SkiaSharp.SKPathEffect) para mostrar líneas discontinuas, o para trazar o rellenar un área con un patrón creado a partir de trazados. El efecto de la ruta de acceso se explicó anteriormente en esta serie en el artículo [**efectos de la ruta de acceso en SkiaSharp**](../curves/effects.md).

## <a name="shaders"></a>[Sombreadores](shaders/index.md)

Establezca la [`Shader`](xref:SkiaSharp.SKPaint.Shader) propiedad de `SKPaint` en un objeto de tipo [`SKShader`](xref:SkiaSharp.SKShader) para mostrar degradados lineales o circulares, mapas de bits en mosaico y patrones de ruido de Perlen.

## <a name="blend-modes"></a>[Modos de fusión](blend-modes/index.md)

Establezca la [`BlendMode`](xref:SkiaSharp.SKPaint.BlendMode) propiedad de `SKPaint` en un miembro de la [`SKBlendMode`](xref:SkiaSharp.SKBlendMode) enumeración para controlar lo que ocurre cuando se muestra un gráfico de origen en un destino. SkiaSharp admite todos los modos de composición y combinación de CSS, incluidos los modos Porter-Duff, los modos de mezcla separables y los modos de mezcla no separables.

## <a name="mask-filters"></a>[Filtros de máscara](mask-filters.md)

Establezca la [`MaskFilter`](xref:SkiaSharp.SKPaint.MaskFilter) propiedad de `SKPaint` en un objeto de tipo [`SKMaskFilter`](xref:SkiaSharp.SKMaskFilter) para desenfoques y otros efectos alfa.

## <a name="image-filters"></a>[Filtros de imagen](image-filters.md)

Establezca la [`ImageFilter`](xref:SkiaSharp.SKPaint.ImageFilter) propiedad de `SKPaint` en un objeto de tipo [`SKImageFilter`](xref:SkiaSharp.SKImageFilter) para el desenfoque de mapas de bits y la creación de sombras paralelas, relieves o efectos grabados.

## <a name="color-filters"></a>[Filtros de color](color-filters.md)

Establezca la [`ColorFilter`](xref:SkiaSharp.SKPaint.ColorFilter) propiedad de `SKPaint` en un objeto de tipo [`SKColorFilter`](xref:SkiaSharp.SKColorFilter) para modificar los colores usando tablas o transformaciones de matriz.

Todo el código de ejemplo de estos artículos está en [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos). En la Página principal, seleccione **efectos de SkiaSharp**.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
