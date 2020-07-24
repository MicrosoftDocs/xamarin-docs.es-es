---
title: Polilíneas y ecuaciones paramétricas
description: En este artículo se explica cómo usar SkiaSharp para representar cualquier línea que se puede definir con ecuaciones paramétricas, y se muestra con código de ejemplo.
ms.prod: xamarin
ms.assetid: 85AEBB33-E954-4364-A6E1-808FAB197BEE
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8ffa7ab7c9d2cebb9854ed155c3a00fe65e497c9
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936141"
---
# <a name="polylines-and-parametric-equations"></a>Polilíneas y ecuaciones paramétricas

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Usar SkiaSharp para representar cualquier línea que pueda definir con ecuaciones paramétricas_

En la sección [**curvas y trazados de SkiaSharp**](../curves/index.md) de esta guía, verá los distintos métodos que [`SKPath`](xref:SkiaSharp.SKPath) define para representar determinados tipos de curvas. Sin embargo, a veces es necesario dibujar un tipo de curva que no es compatible directamente con `SKPath` . En tal caso, puede usar una polilínea (una colección de líneas conectadas) para dibujar cualquier curva que pueda definir matemáticamente. Si hace que las líneas sean lo suficientemente pequeñas y suficientemente numerosas, el resultado será similar a una curva. Este espiral es en realidad 3.600 pequeñas líneas:

![Espiral](polylines-images/spiralexample.png)

Por lo general, es mejor definir una curva en términos de un par de ecuaciones paramétricas. Se trata de ecuaciones de coordenadas X e y que dependen de una tercera variable, a la que a veces se le llama `t` para el tiempo. Por ejemplo, las ecuaciones paramétricas siguientes definen un círculo con un radio de 1 centrado en el punto (0,0) para *t* de 0 a 1:

`x = cos(2πt)`

`y = sin(2πt)`

 Si desea un radio mayor que 1, puede multiplicar los valores de seno y coseno por ese radio y, si necesita trasladar el centro a otra ubicación, agregue los valores siguientes:

`x = xCenter + radius·cos(2πt)`

`y = yCenter + radius·sin(2πt)`

En el caso de una elipse con los ejes en paralelo a la horizontal y a la vertical, se implican dos radios:

`x = xCenter + xRadius·cos(2πt)`

`y = yCenter + yRadius·sin(2πt)`

Después, puede colocar el código SkiaSharp equivalente en un bucle que calcula los distintos puntos y los agrega a un trazado. El siguiente código de SkiaSharp crea un `SKPath` objeto para una elipse que rellena la superficie de la pantalla. El bucle recorre los 360 grados directamente. El centro es la mitad del ancho y el alto de la superficie de presentación y, por tanto, son los dos radios:

```csharp
SKPath path = new SKPath();

for (float angle = 0; angle < 360; angle += 1)
{
    double radians = Math.PI * angle / 180;
    float x = info.Width / 2 + (info.Width / 2) * (float)Math.Cos(radians);
    float y = info.Height / 2 + (info.Height / 2) * (float)Math.Sin(radians);

    if (angle == 0)
    {
        path.MoveTo(x, y);
    }
    else
    {
        path.LineTo(x, y);
    }
}
path.Close();
```

Esto da como resultado una elipse definida por 360 pocas líneas. Cuando se representa, parece suave.

Por supuesto, no es necesario crear una elipse con una polilínea porque `SKPath` incluye un `AddOval` método que lo hace automáticamente. Pero es posible que desee dibujar un objeto visual que no proporciona `SKPath` .

La página de **espiral Archimedean** tiene código similar al código de elipse, pero con una diferencia fundamental. Recorre en bucle los 360 grados del círculo 10 veces, ajustando el radio continuamente:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = Math.Min(center.X, center.Y);

    using (SKPath path = new SKPath())
    {
        for (float angle = 0; angle < 3600; angle += 1)
        {
            float scaledRadius = radius * angle / 3600;
            double radians = Math.PI * angle / 180;
            float x = center.X + scaledRadius * (float)Math.Cos(radians);
            float y = center.Y + scaledRadius * (float)Math.Sin(radians);
            SKPoint point = new SKPoint(x, y);

            if (angle == 0)
            {
                path.MoveTo(point);
            }
            else
            {
                path.LineTo(point);
            }
        }

        SKPaint paint = new SKPaint
        {
            Style = SKPaintStyle.Stroke,
            Color = SKColors.Red,
            StrokeWidth = 5
        };

        canvas.DrawPath(path, paint);
    }
}
```

El resultado también se denomina *espiral aritmética* porque el desplazamiento entre cada bucle es constante:

[![Captura de pantalla triple de la página de espiral de Archimedean](polylines-images/archimedeanspiral-small.png)](polylines-images/archimedeanspiral-large.png#lightbox "Captura de pantalla triple de la página de espiral de Archimedean")

Observe que `SKPath` se crea en un `using` bloque. Esto `SKPath` consume más memoria que los `SKPath` objetos de los programas anteriores, lo que sugiere que un `using` bloque es más adecuado para eliminar los recursos no administrados.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
