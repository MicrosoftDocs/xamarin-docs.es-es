---
title: Transformación de sesgo
description: En este artículo se explica cómo la transformación de sesgo puede crear objetos gráficos inclinados en SkiaSharp y se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: FDD16186-E3B7-4FF6-9BC2-8A2974BFF616
author: davidbritch
ms.author: dabritch
ms.date: 03/20/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ed1bcbed86874362e291fb23ce86dea8992d3408
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563736"
---
# <a name="the-skew-transform"></a>Transformación de sesgo

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Vea cómo la transformación de sesgo puede crear objetos gráficos inclinados en SkiaSharp_

En SkiaSharp, la transformación sesgado inclina los objetos gráficos, como la sombra de esta imagen:

![Ejemplo de sesgo desde el programa de texto de sombra de sesgo](skew-images/skewexample.png)

El sesgo convierte un rectángulo en un paralelogramo, pero una elipse sesgada sigue siendo una elipse.

Aunque Xamarin.Forms define propiedades para la traducción, el escalado y los giros, no hay ninguna propiedad correspondiente en Xamarin.Forms para sesgar.

El [`Skew`](xref:SkiaSharp.SKCanvas.Skew(System.Single,System.Single)) método de `SKCanvas` acepta dos argumentos para el sesgo horizontal y el sesgo vertical:

```csharp
public void Skew (Single xSkew, Single ySkew)
```

Un segundo [`Skew`](xref:SkiaSharp.SKCanvas.Skew(SkiaSharp.SKPoint)) método combina esos argumentos en un `SKPoint` valor único:

```csharp
public void Skew (SKPoint skew)
```

Sin embargo, es poco probable que use cualquiera de estos dos métodos de forma aislada.

La página del **experimento de sesgo** le permite experimentar con valores de sesgo comprendidos entre – 10 y 10. Una cadena de texto se coloca en la esquina superior izquierda de la página, con los valores de sesgo obtenidos de dos `Slider` elementos. Este es el `PaintSurface` controlador de la [`SkewExperimentPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewExperimentPage.xaml.cs) clase:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 200
    })
    {
        string text = "SKEW";
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);

        canvas.Skew((float)xSkewSlider.Value, (float)ySkewSlider.Value);
        canvas.DrawText(text, 0, -textBounds.Top, textPaint);
    }
}
```

Los valores del `xSkew` argumento desplazan la parte inferior del texto a la derecha para los valores positivos o a la izquierda para los valores negativos. Valores de `ySkew` desplazarse a la derecha del texto hacia abajo para los valores positivos o hacia arriba para los valores negativos:

[![Captura de pantalla triple de la página del experimento de sesgo](skew-images/skewexperiment-small.png)](skew-images/skewexperiment-large.png#lightbox "Captura de pantalla triple de la página del experimento de sesgo")

Si el `xSkew` valor es el negativo del `ySkew` valor, el resultado es rotación, pero también escala ligeramente.

Las fórmulas de transformación son las siguientes:

x ' = x + xSkew · sí

y ' = ySkew · x + y

Por ejemplo, para un `xSkew` valor positivo, el valor transformado `x'` aumenta en `y` incrementos. Esto es lo que provoca la inclinación.

Si un triángulo 200 píxeles de ancho y 100 píxeles de alto se colocan con la esquina superior izquierda en el punto (0,0) y se representan con un `xSkew` valor de 1,5, los siguientes resultados del paralelogramo:

![Efecto de la transformación de sesgo en un rectángulo](skew-images/skeweffect.png)

Las coordenadas del borde inferior tienen `y` valores de 100, por lo que se desplazan 150 píxeles a la derecha.

En el caso de los valores distintos de cero de `xSkew` o `ySkew` , solo el punto (0,0) sigue siendo el mismo. Ese punto se puede considerar el centro del sesgo. Si necesita que el centro del sesgo sea otro (que suele ser el caso), no hay ningún `Skew` método que lo proporcione. Deberá combinar explícitamente `Translate` las llamadas con la `Skew` llamada. Para centrar el sesgo en `px` y `py` , realice las siguientes llamadas:

```csharp
canvas.Translate(px, py);
canvas.Skew(xSkew, ySkew);
canvas.Translate(-px, -py);
```

Las fórmulas de transformación compuesta son:

x ' = x + xSkew · (y – py)

y ' = ySkew · (x – PX) + y

Si `ySkew` es cero, `px` no se utiliza el valor. El valor es irrelevante y, de forma similar, para `ySkew` y `py` .

Podría sentirse más cómodo especificando el sesgo como un ángulo de inclinación, como el ángulo α de este diagrama:

![Efecto de la transformación de sesgo en un rectángulo con un ángulo de sesgo indicado](skew-images/skewangleeffect.png)

La relación entre el desplazamiento de 150 píxeles y el vertical de 100 píxeles es la tangente del ángulo, en este ejemplo 56,3 grados.

El archivo XAML de la página **experimento de ángulo de sesgo** es similar a la página ángulo de **sesgo** , salvo que los `Slider` elementos van de – 90 grados a 90 grados. El [`SkewAngleExperiment`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewAngleExperimentPage.xaml.cs) archivo de código subyacente centra el texto en la página y utiliza `Translate` para establecer un centro de sesgo en el centro de la página. Un `SkewDegrees` método Short en la parte inferior del código convierte los ángulos en valores de sesgo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 200
    })
    {
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        string text = "SKEW";
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);
        float xText = xCenter - textBounds.MidX;
        float yText = yCenter - textBounds.MidY;

        canvas.Translate(xCenter, yCenter);
        SkewDegrees(canvas, xSkewSlider.Value, ySkewSlider.Value);
        canvas.Translate(-xCenter, -yCenter);
        canvas.DrawText(text, xText, yText, textPaint);
    }
}

void SkewDegrees(SKCanvas canvas, double xDegrees, double yDegrees)
{
    canvas.Skew((float)Math.Tan(Math.PI * xDegrees / 180),
                (float)Math.Tan(Math.PI * yDegrees / 180));
}
```

Como un ángulo se aproxima a 90 grados positivo o negativo, la tangente se aproxima al infinito, pero se pueden usar ángulos de hasta 80 grados, entre otros:

[![Captura de pantalla triple de la página experimento de ángulo de sesgo](skew-images/skewangleexperiment-small.png)](skew-images/skewangleexperiment-large.png#lightbox "Captura de pantalla triple de la página experimento de ángulo de sesgo")

Un pequeño sesgo horizontal negativo puede imitar el texto oblicuo o en cursiva, como se muestra en la página de **texto oblicuo** . La [`ObliqueTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ObliqueTextPage.cs) clase muestra cómo se hace:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint()
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Maroon,
        TextAlign = SKTextAlign.Center,
        TextSize = info.Width / 8       // empirically determined
    })
    {
        canvas.Translate(info.Width / 2, info.Height / 2);
        SkewDegrees(canvas, -20, 0);
        canvas.DrawText(Title, 0, 0, textPaint);
    }
}

void SkewDegrees(SKCanvas canvas, double xDegrees, double yDegrees)
{
    canvas.Skew((float)Math.Tan(Math.PI * xDegrees / 180),
                (float)Math.Tan(Math.PI * yDegrees / 180));
}
```

La `TextAlign` propiedad de `SKPaint` se establece en `Center` . Sin las transformaciones, la `DrawText` llamada con las coordenadas de (0,0) colocaría el texto con el centro horizontal de la línea de base en la esquina superior izquierda. El `SkewDegrees` sesga el texto horizontalmente 20 grados en relación con la línea base. La `Translate` llamada mueve el centro horizontal de la línea de base del texto al centro del lienzo:

[![Captura de pantalla triple de la página de texto oblicuo](skew-images/obliquetext-small.png)](skew-images/obliquetext-large.png#lightbox "Captura de pantalla triple de la página de texto oblicuo")

La página de **texto de sombra de sesgo** muestra cómo usar una combinación de un sesgo de 45 grados y una escala vertical para crear una sombra de texto que se inclina fuera del texto. Esta es la parte pertinente del `PaintSurface` controlador:

```csharp
using (SKPaint textPaint = new SKPaint())
{
    textPaint.Style = SKPaintStyle.Fill;
    textPaint.TextSize = info.Width / 6;   // empirically determined

    // Common to shadow and text
    string text = "Shadow";
    float xText = 20;
    float yText = info.Height / 2;

    // Shadow
    textPaint.Color = SKColors.LightGray;
    canvas.Save();
    canvas.Translate(xText, yText);
    canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
    canvas.Scale(1, 3);
    canvas.Translate(-xText, -yText);
    canvas.DrawText(text, xText, yText, textPaint);
    canvas.Restore();

    // Text
    textPaint.Color = SKColors.Blue;
    canvas.DrawText(text, xText, yText, textPaint);
}
```

La sombra se muestra primero y después el texto:

[![Captura de pantalla triple de la página de texto de sombra de sesgo](skew-images/skewshadowtext1-small.png)](skew-images/skewshadowtext1-large.png#lightbox "Captura de pantalla triple de la página de texto de sombra de sesgo")

La coordenada vertical pasada al `DrawText` método indica la posición del texto con respecto a la línea base. Es la misma coordenada vertical utilizada para el centro de sesgo. Esta técnica no funcionará si la cadena de texto contiene descendientes. Por ejemplo, sustituya la palabra "no estándar" por "Shadow" y este es el resultado:

[![Captura de pantalla triple de la página de texto de sombra de sesgo con una palabra alternativa con descendiente](skew-images/skewshadowtext2-small.png)](skew-images/skewshadowtext2-large.png#lightbox "Captura de pantalla triple de la página de texto de sombra de sesgo con una palabra alternativa con descendiente")

La sombra y el texto se siguen alineando en la línea de base, pero el efecto tiene un aspecto incorrecto. Para corregirlo, debe obtener los límites de texto:

```csharp
SKRect textBounds = new SKRect();
textPaint.MeasureText(text, ref textBounds);
```

Las `Translate` llamadas deben ajustarse por el alto de los descendentes:

```csharp
canvas.Translate(xText, yText + textBounds.Bottom);
canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
canvas.Scale(1, 3);
canvas.Translate(-xText, -yText - textBounds.Bottom);
```

Ahora la sombra se extiende desde la parte inferior de los descendentes:

[![Captura de pantalla triple de la página de texto de sombra de sesgo con ajustes para descendentes](skew-images/skewshadowtext3-small.png)](skew-images/skewshadowtext3-large.png#lightbox "Captura de pantalla triple de la página de texto de sombra de sesgo con ajustes para descendentes")

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)