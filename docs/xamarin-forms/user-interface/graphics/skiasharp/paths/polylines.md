---
title: Polilíneas y ecuaciones paramétricas
description: En este artículo se explica cómo al uso de SkiaSharp para representar cualquier línea que puede definir con ecuaciones paramétricas y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.assetid: 85AEBB33-E954-4364-A6E1-808FAB197BEE
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: f326a2ef449b7c807be150a002a4afc600d9908d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652701"
---
# <a name="polylines-and-parametric-equations"></a>Polilíneas y ecuaciones paramétricas

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Uso SkiaSharp para representar cualquier línea que se puede definir con ecuaciones paramétricas_

En el [ **trazados y curvas de SkiaSharp** ](../curves/index.md) sección de esta guía, podrá ver los distintos métodos que [ `SKPath` ](xref:SkiaSharp.SKPath) define para presentar determinados tipos de curvas. Sin embargo, a veces es necesario dibujar un tipo de curva que no es directamente compatible con `SKPath`. En tal caso, puede usar una polilínea (una colección de líneas conectadas) para dibujar una curva que se puede definir matemáticamente. Si hace lo suficientemente pequeño como las líneas y numerosos suficiente, el resultado tendrá un aspecto similar a una curva. Este espiral es realmente 3.600 líneas poco:

![](polylines-images/spiralexample.png "Una espiral")

Generalmente es mejor definir una curva en términos de un par de ecuaciones paramétricas. Estos son las ecuaciones para coordenadas X e Y que dependen de una tercera variable, a veces denominada `t` por vez. Por ejemplo, las ecuaciones paramétricas siguientes definen un círculo con un radio de 1 que se centra en el punto (0, 0) para *t* de 0 a 1:

`x = cos(2πt)`

`y = sin(2πt)`

 Si desea que un radio mayor que 1, simplemente multiplique los valores de seno y coseno por ese radius y si necesita mover el centro a otra ubicación, agregue esos valores:

`x = xCenter + radius·cos(2πt)`

`y = yCenter + radius·sin(2πt)`

Para una elipse con el paralelo de los ejes horizontal y vertical, intervienen dos radios:

`x = xCenter + xRadius·cos(2πt)`

`y = yCenter + yRadius·sin(2πt)`

A continuación, puede colocar el código equivalente de SkiaSharp en un bucle que calcula los diversos puntos y agrega los de una ruta de acceso. El siguiente código de SkiaSharp crea un `SKPath` objeto para una elipse que rellena la superficie de pantalla. El bucle recorre los 360 grados directamente. El centro es mitad del ancho y alto de la superficie de pantalla y, por lo tanto, son los dos radios:

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

Esto da como resultado una elipse definida por 360 líneas poco. Cuando se procesa, aparece sin problemas.

Por supuesto, no es necesario crear una elipse con una polilínea porque `SKPath` incluye un `AddOval` método que lo hace por usted. Pero puede ser conveniente dibujar un objeto visual que no se proporciona por `SKPath`.

El **Archimedean espiral** página tiene código que es similar al código de la elipse, pero con una diferencia fundamental. Recorre los 360 grados del círculo alrededor de 10 veces, ajuste continuamente el radio:

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

El resultado también se denomina un *espiral aritmético* porque el desplazamiento entre cada bucle es constante:

[![](polylines-images/archimedeanspiral-small.png "Captura de pantalla triple de la página de espiral Archimedean")](polylines-images/archimedeanspiral-large.png#lightbox "Triple captura de pantalla de la página Archimedean espiral")

Tenga en cuenta que el `SKPath` se crea en un `using` bloque. Esto `SKPath` consume más memoria que el `SKPath` objetos en los programas anteriores, lo que sugiere que un `using` bloque es más adecuado para desechar los recursos no administrados.


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
