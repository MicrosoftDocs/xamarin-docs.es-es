---
title: Conceptos básicos de la ruta de acceso en SkiaSharp
description: En este artículo se explora el objeto SkiaSharp SKPath para combinar líneas y curvas conectadas, y se muestra con código de ejemplo.
ms.prod: xamarin
ms.assetid: A7EDA6C2-3921-4021-89F3-211551E430F1
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a7a4e8c4467438d1f732508a15bee7045310109b
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86931227"
---
# <a name="path-basics-in-skiasharp"></a>Conceptos básicos de la ruta de acceso en SkiaSharp

[![Descargar el ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explore el objeto SKPath de SkiaSharp para combinar líneas y curvas conectadas_

Una de las características más importantes de la ruta de acceso de gráficos es la capacidad de definir cuándo deben conectarse varias líneas y cuándo no deben estar conectadas. La diferencia puede ser significativa, ya que la parte superior de estos dos triángulos muestra:

![Dos triángulos que muestran la diferencia entre las líneas conectadas y desconectadas](paths-images/connectedlinesexample.png)

El objeto encapsula una ruta de acceso de gráficos [`SKPath`](xref:SkiaSharp.SKPath) . Una ruta de acceso es una colección de uno o varios *contornos*. Cada contorno es una colección de líneas rectas y curvas *conectadas* . Los contornos no están conectados entre sí, pero pueden superponerse visualmente. A veces, un solo contorno puede superponerse.

Un perfil generalmente comienza con una llamada al método siguiente de `SKPath` :

- [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo*)para iniciar un nuevo perfil

El argumento para ese método es un punto único, que se puede expresar como un `SKPoint` valor o como coordenadas X e y independientes. La `MoveTo` llamada establece un punto al principio del contorno y un *punto actual*inicial. Puede llamar a los métodos siguientes para continuar el contorno con una línea o una curva desde el punto actual hasta un punto especificado en el método, que luego se convierte en el nuevo punto actual:

- [`LineTo`](xref:SkiaSharp.SKPath.LineTo*)para agregar una línea recta a la ruta de acceso
- [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*)para agregar un arco, que es una línea en la circunferencia de un círculo o una elipse
- [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo*)para agregar una curva spline Bézier cúbica
- [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo*)para agregar una curva spline Bézier cuadrática
- [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo*)para agregar una curva spline Bézier cuadrática racional, que puede representar con precisión las secciones cónicas (elipses, PARABOLAS y hyperbolas)

Ninguno de estos cinco métodos contiene toda la información necesaria para describir la línea o la curva. Cada uno de estos cinco métodos funciona junto con el punto actual establecido por la llamada al método inmediatamente anterior. Por ejemplo, el `LineTo` método agrega una línea recta al contorno basándose en el punto actual, por lo que el parámetro en `LineTo` es solo un punto único.

La `SKPath` clase también define métodos que tienen los mismos nombres que estos seis métodos pero con un `R` al principio:

- [`RMoveTo`](xref:SkiaSharp.SKPath.RMoveTo*)
- [`RLineTo`](xref:SkiaSharp.SKPath.RLineTo*)
- [`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*)
- [`RCubicTo`](xref:SkiaSharp.SKPath.RCubicTo*)
- [`RQuadTo`](xref:SkiaSharp.SKPath.RQuadTo*)
- [`RConicTo`](xref:SkiaSharp.SKPath.RConicTo*)

`R`Significa *relativo*. Estos métodos tienen la misma sintaxis que los métodos correspondientes sin, `R` pero son relativos al punto actual. Son útiles para dibujar partes similares de una ruta de acceso en un método al que se llama varias veces.

Un contorno finaliza con otra llamada a `MoveTo` o `RMoveTo` , que inicia un nuevo perfil, o una llamada a `Close` , que cierra el contorno. El `Close` método anexa automáticamente una línea recta desde el punto actual al primer punto del contorno y marca la ruta de acceso como cerrada, lo que significa que se representará sin ningún extremo de trazo.

La diferencia entre los contornos abiertos y cerrados se ilustra en la página de **contornos de triángulo** , que utiliza un `SKPath` objeto con dos contornos para representar dos triángulos. El primer contorno está abierto y el segundo está cerrado. Esta es la [`TwoTriangleContoursPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/TwoTriangleContoursPage.cs) clase:

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

El primer contorno consta de una llamada al uso de las [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo(System.Single,System.Single)) coordenadas X e y en lugar de un `SKPoint` valor, seguido de tres llamadas a [`LineTo`](xref:SkiaSharp.SKPath.LineTo(System.Single,System.Single)) para dibujar los tres lados del triángulo. El segundo contorno solo tiene dos llamadas a `LineTo` , pero finaliza el contorno con una llamada a [`Close`](xref:SkiaSharp.SKPath.Close) , que cierra el contorno. La diferencia es importante:

[![Captura de pantalla triple de la página de contornos de dos triángulos](paths-images/twotrianglecontours-small.png)](paths-images/twotrianglecontours-large.png#lightbox "Captura de pantalla triple de la página de contornos de dos triángulos")

Como puede ver, el primer contorno es obviamente una serie de tres líneas conectadas, pero el extremo no se conecta con el principio. Las dos líneas se superponen en la parte superior. Obviamente, el segundo contorno está cerrado y se ha realizado con una menor cantidad de `LineTo` llamadas porque el `Close` método agrega automáticamente una línea final para cerrar el contorno.

`SKCanvas`define solo un [`DrawPath`](xref:SkiaSharp.SKCanvas.DrawPath(SkiaSharp.SKPath,SkiaSharp.SKPaint)) método, al que en esta demostración se llama dos veces para rellenar y trazar el trazado. Se rellenan todos los contornos, incluso los que no están cerrados. Con el fin de rellenar las rutas de acceso sin cerrar, se supone que existe una línea recta entre los puntos inicial y final de los contornos. Si quita el último `LineTo` del primer contorno, o quita la `Close` llamada del segundo perfil, cada contorno tendrá solo dos lados, pero se rellenará como si fuera un triángulo.

`SKPath`define muchos otros métodos y propiedades. Los métodos siguientes agregan todos los contornos a la ruta de acceso, que podrían estar cerrados o no cerrados según el método:

- [`AddRect`](xref:SkiaSharp.SKPath.AddRect*)
- [`AddRoundedRect`](xref:SkiaSharp.SKPath.AddRoundedRect(SkiaSharp.SKRect,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddCircle`](xref:SkiaSharp.SKPath.AddCircle(System.Single,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddOval`](xref:SkiaSharp.SKPath.AddOval(SkiaSharp.SKRect,SkiaSharp.SKPathDirection))
- [`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single))para agregar una curva en la circunferencia de una elipse
- [`AddPath`](xref:SkiaSharp.SKPath.AddPath*)para agregar otra ruta de acceso a la ruta de acceso actual
- [`AddPathReverse`](xref:SkiaSharp.SKPath.AddPathReverse(SkiaSharp.SKPath))para agregar otra ruta de acceso en orden inverso

Tenga en cuenta que un `SKPath` objeto solo define una &mdash; serie de puntos y conexiones de geometría. Solo cuando un `SKPath` se combina con un `SKPaint` objeto es la ruta de acceso representada con un color determinado, el ancho del trazo, etc. Además, tenga en cuenta que el `SKPaint` objeto pasado al `DrawPath` método define características de la ruta de acceso completa. Si desea dibujar algo que requiera varios colores, debe usar una ruta de acceso independiente para cada color.

Del mismo modo que la apariencia del inicio y el final de una línea se define mediante un extremo del trazo, la apariencia de la conexión entre dos líneas se define mediante una *combinación de trazo*. Para especificar esto, establezca la [`StrokeJoin`](xref:SkiaSharp.SKPaint.StrokeJoin) propiedad de `SKPaint` en un miembro de la [`SKStrokeJoin`](xref:SkiaSharp.SKStrokeJoin) enumeración:

- `Miter`para una combinación puntual
- `Round`para una combinación redondeada
- `Bevel`para una combinación cortada

En la página **combinaciones de trazo** se muestran estas tres combinaciones de trazos con código similar a la página de **extremos** . Este es el `PaintSurface` controlador de eventos de la [`StrokeJoinsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeJoinsPage.cs) clase:

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

Esta es la ejecución del programa:

[![Captura de pantalla triple de la página de combinaciones de trazo](paths-images/strokejoins-small.png)](paths-images/strokejoins-large.png#lightbox "Captura de pantalla triple de la página de combinaciones de trazo")

La unión angular se compone de un punto nítido en el que se conectan las líneas. Cuando dos líneas se unen en un ángulo pequeño, la unión angular puede llegar a ser bastante larga. Para evitar combinaciones en ángulo excesivamente largas, la longitud de la unión angular está limitada por el valor de la [`StrokeMiter`](xref:SkiaSharp.SKPaint.StrokeMiter) propiedad de `SKPaint` . Una unión angular que supera esta longitud se corta y se convierte en una unión biselada.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
