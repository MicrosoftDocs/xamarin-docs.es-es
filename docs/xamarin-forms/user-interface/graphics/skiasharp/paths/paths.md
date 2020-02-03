---
title: Conceptos básicos de la ruta de acceso de SkiaSharp
description: En este artículo explora el objeto de SkiaSharp SKPath para la combinación de líneas y curvas conectadas y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.assetid: A7EDA6C2-3921-4021-89F3-211551E430F1
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: c892adf2f75ec00c4a9ee171ded78f79bb8227e9
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725199"
---
# <a name="path-basics-in-skiasharp"></a>Conceptos básicos de la ruta de acceso de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explore el objeto SKPath de SkiaSharp para combinar líneas y curvas conectadas_

Una de las características más importantes de la ruta de acceso de gráficos es la capacidad para definir cuándo deben estar conectadas varias líneas y al no deben estar conectados. La diferencia puede ser significativa, como se muestra en la parte superior de estos dos triángulos:

![](paths-images/connectedlinesexample.png "Two triangles showing the difference between connected and disconnected lines")

Una ruta de acceso de gráficos está encapsulada por el objeto [`SKPath`](xref:SkiaSharp.SKPath) . Una ruta de acceso es una colección de uno o varios *contornos*. Cada contorno es una colección de líneas rectas y curvas *conectadas* . Contornos no están conectados entre sí, pero pueden superponer visualmente. A veces un único perfil puede superponer.

Generalmente, un perfil comienza con una llamada al siguiente método de `SKPath`:

- [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo*) para iniciar un nuevo contorno

El argumento para ese método es un punto único, que se puede expresar como un valor `SKPoint` o como coordenadas X e y independientes. La llamada a `MoveTo` establece un punto al principio del contorno y un *punto actual*inicial. Puede llamar a los métodos siguientes para continuar con el contorno con una línea o curva desde el punto actual a un punto especificado en el método, que, a continuación, se convierte en el nuevo punto activo:

- [`LineTo`](xref:SkiaSharp.SKPath.LineTo*) para agregar una línea recta a la ruta de acceso
- [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*) para agregar un arco, que es una línea en la circunferencia de un círculo o una elipse
- [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo*) para agregar una curva spline Bézier cúbica
- [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo*) para agregar una curva spline Bézier cuadrática
- [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo*) para agregar una curva spline Bézier cuadrática, que puede representar con precisión las secciones cónicas (elipses, PARABOLAS y hyperbolas)

Ninguno de estos cinco métodos contienen toda la información necesaria para describir la línea o curva. Cada uno de estos cinco métodos funciona junto con el punto actual establecido por la llamada de método que le precede inmediatamente. Por ejemplo, el método `LineTo` agrega una línea recta al contorno basándose en el punto actual, por lo que el parámetro que se va a `LineTo` es solo un único punto.

La clase `SKPath` también define métodos que tienen los mismos nombres que los seis métodos pero con un `R` al principio:

- [`RMoveTo`](xref:SkiaSharp.SKPath.RMoveTo*)
- [`RLineTo`](xref:SkiaSharp.SKPath.RLineTo*)
- [`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*)
- [`RCubicTo`](xref:SkiaSharp.SKPath.RCubicTo*)
- [`RQuadTo`](xref:SkiaSharp.SKPath.RQuadTo*)
- [`RConicTo`](xref:SkiaSharp.SKPath.RConicTo*)

El `R` significa *relativo*. Estos métodos tienen la misma sintaxis que los métodos correspondientes sin el `R` pero son relativos al punto actual. Estos son útiles para dibujar partes similares de una ruta de acceso en un método que se llama varias veces.

Un contorno finaliza con otra llamada a `MoveTo` o `RMoveTo`, que inicia un nuevo perfil, o una llamada a `Close`, que cierra el contorno. El método `Close` anexa automáticamente una línea recta desde el punto actual al primer punto del contorno y marca la ruta de acceso como cerrada, lo que significa que se representará sin ningún extremo de trazo.

La diferencia entre los contornos abiertos y cerrados se ilustra en la página de **contornos de triángulo** , que usa un objeto de `SKPath` con dos contornos para representar dos triángulos. El primer contorno está abierto y se cierra el segundo. Esta es la clase [`TwoTriangleContoursPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/TwoTriangleContoursPage.cs) :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Create the path
    SKPath path = new SKPath();

    // Define the first contour
    path.MoveTo(0.5f * info.Width, 0.1f * info.Height);
    path.LineTo(0.2f * info.Width, 0.4f * info.Height);
    path.LineTo(0.8f * info.Width, 0.4f * info.Height);
    path.LineTo(0.5f * info.Width, 0.1f * info.Height);

    // Define the second contour
    path.MoveTo(0.5f * info.Width, 0.6f * info.Height);
    path.LineTo(0.2f * info.Width, 0.9f * info.Height);
    path.LineTo(0.8f * info.Width, 0.9f * info.Height);
    path.Close();

    // Create two SKPaint objects
    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Magenta,
        StrokeWidth = 50
    };

    SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Cyan
    };

    // Fill and stroke the path
    canvas.DrawPath(path, fillPaint);
    canvas.DrawPath(path, strokePaint);
}
```

El primer contorno se compone de una llamada a [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo(System.Single,System.Single)) mediante las coordenadas X e y en lugar de un valor `SKPoint`, seguido de tres llamadas a [`LineTo`](xref:SkiaSharp.SKPath.LineTo(System.Single,System.Single)) para dibujar los tres lados del triángulo. El segundo contorno solo tiene dos llamadas a `LineTo` pero finaliza el contorno con una llamada a [`Close`](xref:SkiaSharp.SKPath.Close), que cierra el contorno. La diferencia es importante:

[![](paths-images/twotrianglecontours-small.png "Triple screenshot of the Two Triangle Contours page")](paths-images/twotrianglecontours-large.png#lightbox "Triple screenshot of the Two Triangle Contours page")

Como puede ver, el contorno de la primera es obviamente una serie de tres líneas conectadas, pero no se conecta con el principio final. Las dos líneas se superponen en la parte superior. Obviamente, el segundo contorno está cerrado y se ha realizado con una menor cantidad de llamadas `LineTo` porque el método `Close` agrega automáticamente una línea final para cerrar el contorno.

`SKCanvas` define solo un método de [`DrawPath`](xref:SkiaSharp.SKCanvas.DrawPath(SkiaSharp.SKPath,SkiaSharp.SKPaint)) , que en esta demostración se llama dos veces para rellenar y trazar la ruta de acceso. Se rellenan todos los contornos, incluso aquellos que no están cerrados. Para fines de rellenar trazados no cerrados, una línea recta se supone que existe entre los puntos inicial y final de los perfiles. Si quita el último `LineTo` del primer contorno, o quita la llamada `Close` del segundo perfil, cada contorno tendrá solo dos lados, pero se rellenará como si fuera un triángulo.

`SKPath` define muchos otros métodos y propiedades. Los métodos siguientes agregan contornos todas la ruta de acceso, lo que podría cerrarse o no se cierra en función del método:

- [`AddRect`](xref:SkiaSharp.SKPath.AddRect*)
- [`AddRoundedRect`](xref:SkiaSharp.SKPath.AddRoundedRect(SkiaSharp.SKRect,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddCircle`](xref:SkiaSharp.SKPath.AddCircle(System.Single,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddOval`](xref:SkiaSharp.SKPath.AddOval(SkiaSharp.SKRect,SkiaSharp.SKPathDirection))
- [`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single)) para agregar una curva en la circunferencia de una elipse
- [`AddPath`](xref:SkiaSharp.SKPath.AddPath*) agregar otra ruta de acceso a la ruta de acceso actual
- [`AddPathReverse`](xref:SkiaSharp.SKPath.AddPathReverse(SkiaSharp.SKPath)) agregar otra ruta de acceso en orden inverso

Tenga en cuenta que un objeto `SKPath` define solo una geometría &mdash; una serie de puntos y conexiones. Solo cuando un `SKPath` se combina con un objeto `SKPaint` es la ruta de acceso representada con un color determinado, el ancho del trazo, etc. Además, tenga en cuenta que el `SKPaint` objeto pasado al método `DrawPath` define características de la ruta de acceso completa. Si desea que se va a dibujar algo que requiere varios colores, debe usar una ruta de acceso independiente para cada color.

Del mismo modo que la apariencia del inicio y el final de una línea se define mediante un extremo del trazo, la apariencia de la conexión entre dos líneas se define mediante una *combinación de trazo*. Para especificar esto, establezca la propiedad [`StrokeJoin`](xref:SkiaSharp.SKPaint.StrokeJoin) de `SKPaint` en un miembro de la enumeración [`SKStrokeJoin`](xref:SkiaSharp.SKStrokeJoin) :

- `Miter` para una combinación puntual
- `Round` para una combinación redondeada
- `Bevel` para una combinación cortada

En la página **combinaciones de trazo** se muestran estas tres combinaciones de trazos con código similar a la página de **extremos** . Este es el controlador de eventos `PaintSurface` en la clase [`StrokeJoinsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeJoinsPage.cs) :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 75,
        TextAlign = SKTextAlign.Right
    };

    SKPaint thickLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 50
    };

    SKPaint thinLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2
    };

    float xText = info.Width - 100;
    float xLine1 = 100;
    float xLine2 = info.Width - xLine1;
    float y = 2 * textPaint.FontSpacing;
    string[] strStrokeJoins = { "Miter", "Round", "Bevel" };

    foreach (string strStrokeJoin in strStrokeJoins)
    {
        // Display text
        canvas.DrawText(strStrokeJoin, xText, y, textPaint);

        // Get stroke-join value
        SKStrokeJoin strokeJoin;
        Enum.TryParse(strStrokeJoin, out strokeJoin);

        // Create path
        SKPath path = new SKPath();
        path.MoveTo(xLine1, y - 80);
        path.LineTo(xLine1, y + 80);
        path.LineTo(xLine2, y + 80);

        // Display thick line
        thickLinePaint.StrokeJoin = strokeJoin;
        canvas.DrawPath(path, thickLinePaint);

        // Display thin line
        canvas.DrawPath(path, thinLinePaint);
        y += 3 * textPaint.FontSpacing;
    }
}
```

Este es el programa que se ejecuta:

[![](paths-images/strokejoins-small.png "Triple screenshot of the Stroke Joins page")](paths-images/strokejoins-large.png#lightbox "Triple screenshot of the Stroke Joins page")

La unión angular consta de un vértice agudo donde las líneas de conexión. Cuando se unen dos líneas en un ángulo pequeño, la unión angular puede ser bastante larga. Para evitar combinaciones en ángulo excesivamente largas, la longitud de la unión angular está limitada por el valor de la propiedad [`StrokeMiter`](xref:SkiaSharp.SKPaint.StrokeMiter) de `SKPaint`. Una unión angular que supera esta longitud se cortan para convertirse en una unión biselada.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
