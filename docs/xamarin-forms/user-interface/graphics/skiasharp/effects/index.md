---
title: Efectos de SkiaSharp
description: Obtenga información sobre cómo cambiar la visualización de gráficos con degradados normal, disposición en mosaico de mapa de bits, modos de fusión, desenfoque y otros efectos.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B3E06572-8E2A-49FA-90D1-444C394CD516
author: davidbritch
ms.author: dabritch
ms.date: 08/22/2018
ms.openlocfilehash: da923a3542a57b6150e536ecb6649140e57c81e1
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655352"
---
# <a name="skiasharp-effects"></a>Efectos de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

El SkiaSharp [ `SKPaint` ](xref:SkiaSharp.SKPaint) clase define seis propiedades que se pueden clasificar en el término general de _efectos_. Estas son propiedades que cambiar la visualización de gráficos de alguna manera normal. Los efectos de SkiaSharp se dividen en seis categorías:

## <a name="path-effectscurveseffectsmd"></a>[Efectos del trazado](../curves/effects.md)

Establecer el [ `PathEffect` ](xref:SkiaSharp.SKPaint.PathEffect) propiedad de `SKPaint` a un objeto de tipo [ `SKPathEffect` ](xref:SkiaSharp.SKPathEffect) para mostrar las líneas discontinuas, o al trazo o relleno de un área con un patrón creado a partir de las rutas de acceso. El efecto de la ruta de acceso se ha explicado anteriormente en esta serie en el artículo [ **efectos de la ruta de acceso de SkiaSharp**](../curves/effects.md).

## <a name="shadersshadersindexmd"></a>[Sombreadores](shaders/index.md)

Establecer el [ `Shader` ](xref:SkiaSharp.SKPaint.Shader) propiedad de `SKPaint` a un objeto de tipo [ `SKShader` ](xref:SkiaSharp.SKShader) para mostrar los patrones de ruido de Perlin, mapas de bits en mosaico y degradados lineales o circulares.

## <a name="blend-modesblend-modesindexmd"></a>[Modos de fusión](blend-modes/index.md)

Establecer el [ `BlendMode` ](xref:SkiaSharp.SKPaint.BlendMode) propiedad de `SKPaint` a un miembro de la [ `SKBlendMode` ](xref:SkiaSharp.SKBlendMode) enumeración para determinar lo que ocurre cuando se muestra un gráfico de origen en un destino. SkiaSharp es compatible con todos los CSS composición y blend modos, incluidos los modos Porter Duff, modos de fusión separables y modos de mezcla no separables.

## <a name="mask-filtersmask-filtersmd"></a>[Filtros de máscara](mask-filters.md)

Establecer el [ `MaskFilter` ](xref:SkiaSharp.SKPaint.MaskFilter) propiedad de `SKPaint` a un objeto de tipo [ `SKMaskFilter` ](xref:SkiaSharp.SKMaskFilter) para desenfoques y otros efectos alfabéticos.

## <a name="image-filtersimage-filtersmd"></a>[Filtros de imágenes](image-filters.md)

Establecer el [ `ImageFilter` ](xref:SkiaSharp.SKPaint.ImageFilter) propiedad de `SKPaint` a un objeto de tipo [ `SKImageFilter` ](xref:SkiaSharp.SKImageFilter) para los mapas de bits de desenfoque y la creación de sombras paralelas, relieve o grabados efectos.

## <a name="color-filterscolor-filtersmd"></a>[Filtros de color](color-filters.md)

Establecer el [ `ColorFilter` ](xref:SkiaSharp.SKPaint.ColorFilter) propiedad de `SKPaint` a un objeto de tipo [ `SKColorFilter` ](xref:SkiaSharp.SKColorFilter) para modificar los colores con tablas o las transformaciones de matriz.

Todo el ejemplo de código para estos artículos se encuentran en el [ **SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos). En la página principal, seleccione **SkiaSharp efectos**.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
