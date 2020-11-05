---
title: Recorte con trazados y regiones
description: En este artículo se explica cómo usar las rutas de acceso de SkiaSharp para recortar gráficos a áreas específicas, y para crear regiones y cómo hacerlo con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8022FBF9-2208-43DB-94D8-0A4E9A5DA07F
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 79c32595053cc80ffae91b5434e18690ca2dce4a
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370720"
---
# <a name="clipping-with-paths-and-regions"></a>Recorte con trazados y regiones

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Usar rutas de acceso para recortar gráficos a áreas específicas y crear regiones_

A veces es necesario restringir la representación de gráficos a un área determinada. Esto se conoce como *recorte*. Puede usar el recorte para efectos especiales, como esta imagen de un Monkey de usuario que se ha detectado a través de un Keyhole:

![Monkey a través de Keyhole](clipping-images/clippingsample.png)

El *área de recorte* es el área de la pantalla en la que se representan los gráficos. Todo lo que se muestra fuera del área de recorte no se representa. El área de recorte se define normalmente mediante un rectángulo o un [`SKPath`](xref:SkiaSharp.SKPath) objeto, pero también puede definir un área de recorte mediante un [`SKRegion`](xref:SkiaSharp.SKRegion) objeto. Estos dos tipos de objetos al principio parecen relacionados porque puede crear una región a partir de una ruta de acceso. Sin embargo, no puede crear una ruta de acceso desde una región y son muy diferentes internamente: una ruta de acceso consta de una serie de líneas y curvas, mientras que una región se define mediante una serie de líneas de recorrido horizontal.

La imagen anterior se creó mediante la página **Monkey a Keyhole** . La  [`MonkeyThroughKeyholePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/MonkeyThroughKeyholePage.cs) clase define una ruta de acceso que usa datos SVG y usa el constructor para cargar un mapa de bits de los recursos del programa:

```csharp
public class MonkeyThroughKeyholePage : ContentPage
{
    SKBitmap bitmap;
    SKPath keyholePath = SKPath.ParseSvgPathData(
        "M 300 130 L 250 350 L 450 350 L 400 130 A 70 70 0 1 0 300 130 Z");

    public MonkeyThroughKeyholePage()
    {
        Title = "Monkey through Keyhole";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ...
}
```

Aunque el `keyholePath` objeto describe el contorno de un Keyhole, las coordenadas son completamente arbitrarias y reflejan lo que resultaba práctico cuando se diseñaban los datos de la ruta de acceso. Por esta razón, el `PaintSurface` controlador obtiene los límites de este trazado y llama a `Translate` y `Scale` para desplace la ruta de acceso al centro de la pantalla y para que sea casi tan alta como la pantalla:

```csharp
public class MonkeyThroughKeyholePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set transform to center and enlarge clip path to window height
        SKRect bounds;
        keyholePath.GetTightBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.98f * info.Height / bounds.Height);
        canvas.Translate(-bounds.MidX, -bounds.MidY);

        // Set the clip path
        canvas.ClipPath(keyholePath);

        // Reset transforms
        canvas.ResetMatrix();

        // Display monkey to fill height of window but maintain aspect ratio
        canvas.DrawBitmap(bitmap,
            new SKRect((info.Width - info.Height) / 2, 0,
                       (info.Width + info.Height) / 2, info.Height));
    }
}
```

Pero no se representa la ruta de acceso. En su lugar, siguiendo las transformaciones, la ruta de acceso se utiliza para establecer un área de recorte con esta instrucción:

```csharp
canvas.ClipPath(keyholePath);
```

`PaintSurface`Después, el controlador restablece las transformaciones con una llamada a `ResetMatrix` y dibuja el mapa de bits que se va a extender al alto completo de la pantalla. Este código supone que el mapa de bits es cuadrado, que es el mapa de bits determinado. El mapa de bits solo se representa dentro del área definida por el trazado de recorte:

[![Captura de pantalla triple de la página de Monkey a Keyhole](clipping-images/monkeythroughkeyhole-small.png)](clipping-images/monkeythroughkeyhole-large.png#lightbox)

El trazado de recorte está sujeto a las transformaciones en vigor cuando `ClipPath` se llama al método, y no a las transformaciones en vigor cuando se muestra un objeto gráfico (como un mapa de bits). La ruta de acceso de recorte forma parte del estado del lienzo que se guarda con el `Save` método y se restaura con el `Restore` método.

## <a name="combining-clipping-paths"></a>Combinar trazados de recorte

En realidad, el área de recorte no es "SET" por el `ClipPath` método. En su lugar, se combina con el trazado de recorte existente, que comienza como un rectángulo de igual tamaño al lienzo. Puede obtener los límites rectangulares del área de recorte mediante la [`ClipBounds`](xref:SkiaSharp.SKCanvas.ClipBounds) propiedad o la [`ClipDeviceBounds`](xref:SkiaSharp.SKCanvas.ClipDeviceBounds) propiedad. La `ClipBounds` propiedad devuelve un `SKRect` valor que refleja las transformaciones que pueden estar en vigor. La `ClipDeviceBounds` propiedad devuelve un `RectI` valor. Se trata de un rectángulo con dimensiones de entero y describe el área de recorte en dimensiones de píxeles reales.

Cualquier llamada a `ClipPath` reduce el área de recorte combinando el área de recorte con una nueva área. La sintaxis completa del [`ClipPath`](xref:SkiaSharp.SKCanvas.ClipPath(SkiaSharp.SKPath,SkiaSharp.SKClipOperation,System.Boolean)) método es la siguiente:

```csharp
public void ClipPath(SKPath path, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

También hay un [`ClipRect`](xref:SkiaSharp.SKCanvas.ClipRect(SkiaSharp.SKRect,SkiaSharp.SKClipOperation,System.Boolean)) método que combina el área de recorte con un rectángulo:

```csharp
public Void ClipRect(SKRect rect, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

De forma predeterminada, el área de recorte resultante es una intersección del área de recorte existente y el `SKPath` o `SKRect` que se especifica en el `ClipPath` `ClipRect` método o. Esto se muestra en la página de **clip de intersección cuatro círculos** . El `PaintSurface` controlador de la  [`FourCircleInteresectClipPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourCircleIntersectClipPage.cs) clase reutiliza el mismo `SKPath` objeto para crear cuatro círculos superpuestos, cada uno de los cuales reduce el área de recorte mediante llamadas sucesivas a `ClipPath` :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float size = Math.Min(info.Width, info.Height);
    float radius = 0.4f * size;
    float offset = size / 2 - radius;

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    using (SKPath path = new SKPath())
    {
        path.AddCircle(-offset, -offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(-offset, offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(offset, -offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(offset, offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Fill;
            paint.Color = SKColors.Blue;
            canvas.DrawPaint(paint);
        }
    }
}
```

Lo que queda es la intersección de estos cuatro círculos:

[![Captura de pantalla triple de la página de recorte de intersección de cuatro círculos](clipping-images//fourcircleintersectclip-small.png)](clipping-images/fourcircleintersectclip-large.png#lightbox)

La [`SKClipOperation`](xref:SkiaSharp.SKClipOperation) enumeración solo tiene dos miembros:

- `Difference` quita la ruta de acceso o el rectángulo especificados del área de recorte existente.

- `Intersect` forma una intersección con la ruta de acceso o el rectángulo especificados con el área de recorte existente.

Si reemplaza los cuatro `SKClipOperation.Intersect` argumentos de la `FourCircleIntersectClipPage` clase con `SKClipOperation.Difference` , verá lo siguiente:

[![Captura de pantalla triple de la página de recorte de intersección de cuatro círculos con la operación de diferencia](clipping-images//fourcircledifferenceclip-small.png)](clipping-images/fourcircledifferenceclip-large.png#lightbox)

Se han quitado cuatro círculos superpuestos del área de recorte.

La página de **operaciones de recorte** ilustra la diferencia entre estas dos operaciones con solo un par de círculos. El primer círculo de la izquierda se agrega al área de recorte con la operación de clip predeterminada de `Intersect` , mientras que el segundo círculo de la derecha se agrega al área de recorte con la operación de recorte indicada por la etiqueta de texto:

[![Captura de pantalla triple de la página de operaciones de recorte](clipping-images//clipoperations-small.png)](clipping-images/clipoperations-large.png#lightbox)

La [`ClipOperationsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClipOperationsPage.cs) clase define dos `SKPaint` objetos como campos y, a continuación, divide la pantalla en dos áreas rectangulares. Estas áreas son diferentes en función de si el teléfono está en modo vertical u horizontal. `DisplayClipOp`A continuación, la clase muestra el texto y llama a `ClipPath` con las dos rutas de acceso de círculo para ilustrar cada operación de clip:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float x = 0;
    float y = 0;

    foreach (SKClipOperation clipOp in Enum.GetValues(typeof(SKClipOperation)))
    {
        // Portrait mode
        if (info.Height > info.Width)
        {
            DisplayClipOp(canvas, new SKRect(x, y, x + info.Width, y + info.Height / 2), clipOp);
            y += info.Height / 2;
        }
        // Landscape mode
        else
        {
            DisplayClipOp(canvas, new SKRect(x, y, x + info.Width / 2, y + info.Height), clipOp);
            x += info.Width / 2;
        }
    }
}

void DisplayClipOp(SKCanvas canvas, SKRect rect, SKClipOperation clipOp)
{
    float textSize = textPaint.TextSize;
    canvas.DrawText(clipOp.ToString(), rect.MidX, rect.Top + textSize, textPaint);
    rect.Top += textSize;

    float radius = 0.9f * Math.Min(rect.Width / 3, rect.Height / 2);
    float xCenter = rect.MidX;
    float yCenter = rect.MidY;

    canvas.Save();

    using (SKPath path1 = new SKPath())
    {
        path1.AddCircle(xCenter - radius / 2, yCenter, radius);
        canvas.ClipPath(path1);

        using (SKPath path2 = new SKPath())
        {
            path2.AddCircle(xCenter + radius / 2, yCenter, radius);
            canvas.ClipPath(path2, clipOp);

            canvas.DrawPaint(fillPaint);
        }
    }

    canvas.Restore();
}
```

Llamar a `DrawPaint` normalmente hace que todo el lienzo se rellene con ese `SKPaint` objeto, pero en este caso, el método simplemente pinta dentro del área de recorte.

## <a name="exploring-regions"></a>Explorar regiones

También puede definir un área de recorte en cuanto a un [`SKRegion`](xref:SkiaSharp.SKRegion) objeto.

Un objeto recién creado `SKRegion` describe un área vacía. Normalmente, la primera llamada en el objeto es [`SetRect`](xref:SkiaSharp.SKRegion.SetRect(SkiaSharp.SKRectI)) para que la región describa un área rectangular. El parámetro `SetRect` es un valor que es `SKRectI` &mdash; un rectángulo con coordenadas enteras porque especifica el rectángulo en términos de píxeles. Después, puede llamar a [`SetPath`](xref:SkiaSharp.SKRegion.SetPath(SkiaSharp.SKPath,SkiaSharp.SKRegion)) con un `SKPath` objeto. Esto crea una región que es igual que el interior de la ruta de acceso, pero que se recorta en la región rectangular inicial.

La región también se puede modificar mediante una llamada a una de las [`Op`](xref:SkiaSharp.SKRegion.Op*) sobrecargas del método, como esta:

```csharp
public Boolean Op(SKRegion region, SKRegionOperation op)
```

La [`SKRegionOperation`](xref:SkiaSharp.SKRegionOperation) enumeración es similar a `SKClipOperation` pero tiene más miembros:

- `Difference`

- `Intersect`

- `Union`

- `XOR`

- `ReverseDifference`

- `Replace`

La región en la que realiza la `Op` llamada se combina con la región especificada como un parámetro basado en el `SKRegionOperation` miembro. Cuando finalmente obtiene una región adecuada para el recorte, puede establecerla como el área de recorte del lienzo mediante el [`ClipRegion`](xref:SkiaSharp.SKCanvas.ClipRegion(SkiaSharp.SKRegion,SkiaSharp.SKClipOperation)) método de `SKCanvas` :

```csharp
public void ClipRegion(SKRegion region, SKClipOperation operation = SKClipOperation.Intersect)
```

En la captura de pantalla siguiente se muestran las áreas de recorte basadas en las seis operaciones de regiones. El círculo izquierdo es la región en la que `Op` se llama al método y el círculo derecho es la región que se pasa al `Op` método:

[![Captura de pantalla triple de la página de operaciones de región](clipping-images//regionoperations-small.png)](clipping-images/regionoperations-large.png#lightbox)

¿Son todas las posibilidades de combinar estos dos círculos? Considere la imagen resultante como una combinación de tres componentes que, por sí mismos, se ven en `Difference` las `Intersect` operaciones, y `ReverseDifference` . El número total de combinaciones es dos en la tercera potencia u ocho. Los dos que faltan son la región original (que es el resultado de no llamar `Op` a en absoluto) y una región completamente vacía.

Es más difícil usar regiones para el recorte porque primero es necesario crear una ruta de acceso y, a continuación, una región de esa ruta de acceso y, a continuación, combinar varias regiones. La estructura general de la página de operaciones de la **región** es muy similar a **las operaciones de recorte** , pero la [`RegionOperationsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionOperationsPage.cs) clase divide la pantalla en seis áreas y muestra el trabajo adicional necesario para usar las regiones para este trabajo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float x = 0;
    float y = 0;
    float width = info.Height > info.Width ? info.Width / 2 : info.Width / 3;
    float height = info.Height > info.Width ? info.Height / 3 : info.Height / 2;

    foreach (SKRegionOperation regionOp in Enum.GetValues(typeof(SKRegionOperation)))
    {
        DisplayClipOp(canvas, new SKRect(x, y, x + width, y + height), regionOp);

        if ((x += width) >= info.Width)
        {
            x = 0;
            y += height;
        }
    }
}

void DisplayClipOp(SKCanvas canvas, SKRect rect, SKRegionOperation regionOp)
{
    float textSize = textPaint.TextSize;
    canvas.DrawText(regionOp.ToString(), rect.MidX, rect.Top + textSize, textPaint);
    rect.Top += textSize;

    float radius = 0.9f * Math.Min(rect.Width / 3, rect.Height / 2);
    float xCenter = rect.MidX;
    float yCenter = rect.MidY;

    SKRectI recti = new SKRectI((int)rect.Left, (int)rect.Top,
                                (int)rect.Right, (int)rect.Bottom);

    using (SKRegion wholeRectRegion = new SKRegion())
    {
        wholeRectRegion.SetRect(recti);

        using (SKRegion region1 = new SKRegion(wholeRectRegion))
        using (SKRegion region2 = new SKRegion(wholeRectRegion))
        {
            using (SKPath path1 = new SKPath())
            {
                path1.AddCircle(xCenter - radius / 2, yCenter, radius);
                region1.SetPath(path1);
            }

            using (SKPath path2 = new SKPath())
            {
                path2.AddCircle(xCenter + radius / 2, yCenter, radius);
                region2.SetPath(path2);
            }

            region1.Op(region2, regionOp);

            canvas.Save();
            canvas.ClipRegion(region1);
            canvas.DrawPaint(fillPaint);
            canvas.Restore();
        }
    }
}
```

Esta es una gran diferencia entre el `ClipPath` método y el `ClipRegion` método:

> [!IMPORTANT]
> A diferencia del `ClipPath` método, el `ClipRegion` método no se ve afectado por las transformaciones.

Para comprender la lógica de esta diferencia, es útil comprender lo que es una región. Si ha pensado en cómo se pueden implementar internamente las operaciones de recorte o de región, probablemente parezca muy complicado. Se combinan varias rutas de acceso potencialmente complejas y el contorno de la ruta resultante es probablemente una pesadilla algorítmica.

Este trabajo se simplifica considerablemente si cada ruta de acceso se reduce a una serie de líneas de recorrido horizontal, como las de los televisores de tubos de Vacuumer antiguos. Cada línea de recorrido es simplemente una línea horizontal con un punto inicial y un punto final. Por ejemplo, un círculo con un radio de 10 píxeles se puede descomponer en 20 líneas de recorrido horizontal, cada una de las cuales comienza en la parte izquierda del círculo y termina en la parte derecha. Combinar dos círculos con cualquier operación de región resulta muy sencillo, ya que simplemente se trata de examinar las coordenadas inicial y final de cada par de líneas de análisis correspondientes.

Esto es lo que es una región: una serie de líneas de recorrido horizontal que definen un área.

Sin embargo, cuando un área se reduce a una serie de líneas de recorrido, estas líneas de recorrido se basan en una dimensión de píxeles determinada. En realidad, la región no es un objeto de gráficos vectoriales. Está más cerca de la naturaleza de un mapa de bits monocromo comprimido que en una ruta de acceso. Por consiguiente, las regiones no se pueden escalar ni girar sin perder fidelidad y, por este motivo, no se transforman cuando se usan para áreas de recorte.

Sin embargo, puede aplicar transformaciones a regiones con fines de dibujo. El programa de Paint de la **región** muestra de un solo tipo la naturaleza interna de las regiones. La [`RegionPaintPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionPaintPage.cs) clase crea un `SKRegion` objeto basado en un `SKPath` círculo de radio de 10 unidades. Después, una transformación expande ese círculo para rellenar la página:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int radius = 10;

    // Create circular path
    using (SKPath circlePath = new SKPath())
    {
        circlePath.AddCircle(0, 0, radius);

        // Create circular region
        using (SKRegion circleRegion = new SKRegion())
        {
            circleRegion.SetRect(new SKRectI(-radius, -radius, radius, radius));
            circleRegion.SetPath(circlePath);

            // Set transform to move it to center and scale up
            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(Math.Min(info.Width / 2, info.Height / 2) / radius);

            // Fill region
            using (SKPaint fillPaint = new SKPaint())
            {
                fillPaint.Style = SKPaintStyle.Fill;
                fillPaint.Color = SKColors.Orange;

                canvas.DrawRegion(circleRegion, fillPaint);
            }

            // Stroke path for comparison
            using (SKPaint strokePaint = new SKPaint())
            {
                strokePaint.Style = SKPaintStyle.Stroke;
                strokePaint.Color = SKColors.Blue;
                strokePaint.StrokeWidth = 0.1f;

                canvas.DrawPath(circlePath, strokePaint);
            }
        }
    }
}
```

La `DrawRegion` llamada rellena la región en naranja, mientras que la `DrawPath` llamada traza el trazado original en azul para la comparación:

[![Captura de pantalla triple de la página de Paint de la región](clipping-images//regionpaint-small.png)](clipping-images/regionpaint-large.png#lightbox)

La región es claramente una serie de coordenadas discretas.

Si no necesita usar transformaciones en relación con las áreas de recorte, puede usar regiones para el recorte, como se muestra en la página de **trébol de cuatro hojas** . La [`FourLeafCloverPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourLeafCloverPage.cs) clase crea una región compuesta a partir de cuatro regiones circulares, establece esa región compuesta como el área de recorte y, a continuación, dibuja una serie de 360 líneas rectas que emanan del centro de la página:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float xCenter = info.Width / 2;
    float yCenter = info.Height / 2;
    float radius = 0.24f * Math.Min(info.Width, info.Height);

    using (SKRegion wholeScreenRegion = new SKRegion())
    {
        wholeScreenRegion.SetRect(new SKRectI(0, 0, info.Width, info.Height));

        using (SKRegion leftRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion rightRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion topRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion bottomRegion = new SKRegion(wholeScreenRegion))
        {
            using (SKPath circlePath = new SKPath())
            {
                // Make basic circle path
                circlePath.AddCircle(xCenter, yCenter, radius);

                // Left leaf
                circlePath.Transform(SKMatrix.MakeTranslation(-radius, 0));
                leftRegion.SetPath(circlePath);

                // Right leaf
                circlePath.Transform(SKMatrix.MakeTranslation(2 * radius, 0));
                rightRegion.SetPath(circlePath);

                // Make union of right with left
                leftRegion.Op(rightRegion, SKRegionOperation.Union);

                // Top leaf
                circlePath.Transform(SKMatrix.MakeTranslation(-radius, -radius));
                topRegion.SetPath(circlePath);

                // Combine with bottom leaf
                circlePath.Transform(SKMatrix.MakeTranslation(0, 2 * radius));
                bottomRegion.SetPath(circlePath);

                // Make union of top with bottom
                bottomRegion.Op(topRegion, SKRegionOperation.Union);

                // Exclusive-OR left and right with top and bottom
                leftRegion.Op(bottomRegion, SKRegionOperation.XOR);

                // Set that as clip region
                canvas.ClipRegion(leftRegion);

                // Set transform for drawing lines from center
                canvas.Translate(xCenter, yCenter);

                // Draw 360 lines
                for (double angle = 0; angle < 360; angle++)
                {
                    float x = 2 * radius * (float)Math.Cos(Math.PI * angle / 180);
                    float y = 2 * radius * (float)Math.Sin(Math.PI * angle / 180);

                    using (SKPaint strokePaint = new SKPaint())
                    {
                        strokePaint.Color = SKColors.Green;
                        strokePaint.StrokeWidth = 2;

                        canvas.DrawLine(0, 0, x, y, strokePaint);
                    }
                }
            }
        }
    }
}
```

En realidad, no parece un trébol de cuatro hojas, pero es una imagen que de otro modo podría ser difícil de representar sin recorte:

[![Captura de pantalla triple de la página de Four-Leaf trébol](clipping-images//fourleafclover-small.png)](clipping-images/fourleafclover-large.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)