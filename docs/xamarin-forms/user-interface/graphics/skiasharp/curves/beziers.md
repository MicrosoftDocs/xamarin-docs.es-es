---
title: Tres tipos de curvas de Bézier
description: En este artículo se explica cómo usar SkiaSharp para representar las curvas de Bézier cúbicas, cuadráticas y cónicos en las aplicaciones de Xamarin.Forms y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8FE0F6DC-16BC-435F-9626-DD1790C0145A
author: davidbritch
ms.author: dabritch
ms.date: 05/25/2017
ms.openlocfilehash: 7802360c95d31f237cf69c69728cffe02ad47e5a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646229"
---
# <a name="three-types-of-bzier-curves"></a>Tres tipos de curvas de Bézier

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explorar el uso de SkiaSharp para representar las curvas de Bézier cúbicas, cuadráticas y cónicos_

La curva de Bézier se denomina después Pierre Bézier (1910: 1999), un ingeniero de francés de la empresa automotriz Renault, que utilizaban la curva para el diseño asistido por ordenador de cuerpos de automóvil.

Las curvas de Bézier son conocidas para ser adecuadas para el diseño interactivo: Se comportan &mdash; correctamente en otras palabras, no hay singularidad que provoquen que la curva se convierta en &mdash; infinita o no manejable y, por lo general, estéticamente agradable:

![](beziers-images/beziersample.png "Una curva de Bézier de ejemplo")

Normalmente se definen los contornos de caracteres de las fuentes basadas en equipo con curvas de Bézier.

El artículo de Wikipedia sobre [ **curva de Bézier** ](https://en.wikipedia.org/wiki/B%C3%A9zier_curve) contiene cierta información útil en segundo plano. El término *curva de Bézier* hace referencia a una familia de curvas similar. SkiaSharp admite tres tipos de curvas de Bézier, llamados el *cúbica*, el *cuadrática*y el *cónicos*. El cónicos es también conocida como la *cuadrática rational*.

## <a name="the-cubic-bzier-curve"></a>La curva de Bézier cúbica

El curva Bézier cúbica es el tipo de curva de Bézier que pensar en la mayoría de los desarrolladores cuando aparezca el asunto de curvas de Bézier.

Puede agregar una curva Bézier cúbica que un `SKPath` objeto utilizando el [ `CubicTo` ](xref:SkiaSharp.SKPath.CubicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKPoint)) método con tres `SKPoint` parámetros, o el [ `CubicTo` ](xref:SkiaSharp.SKPath.CubicTo(System.Single,System.Single,System.Single,System.Single,System.Single,System.Single)) sobrecarga con independiente`x` y `y` parámetros:

```csharp
public void CubicTo (SKPoint point1, SKPoint point2, SKPoint point3)

public void CubicTo (Single x1, Single y1, Single x2, Single y2, Single x3, Single y3)
```

La curva comienza en el punto actual del contorno. La curva de Bézier cúbica completa se define por cuatro puntos:

- punto de inicio: punto actual en el contorno, o (0, 0) si `MoveTo` no se ha llamado.
- en primer lugar el punto de control: `point1` en el `CubicTo` llamar
- segundo punto de control: `point2` en el `CubicTo` llamar
- punto de conexión: `point3` en el `CubicTo` llamar

La curva resultante comienza en el punto inicial y termina en el punto final. Por lo general la curva no pasa por dos puntos de control; en su lugar, el control de puntos de función como imanes para extraer la curva hacia ellos.

Es la mejor forma de hacerse una idea de la curva de Bézier cúbica mediante experimentación. Este es el propósito de la **curva de Bézier** página, que se deriva de `InteractivePage`. El [ **BezierCurvePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml) crea una instancia de archivo del `SKCanvasView` y un `TouchEffect`. El [ **BezierCurvePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml.cs) archivo de código subyacente crea cuatro `TouchPoint` objetos en su constructor. El `PaintSurface` controlador de eventos crea un `SKPath` para representar una curva de Bézier en función de los cuatro `TouchPoint` objetos y también dibuja líneas tangentes punteadas desde los puntos de control en los puntos de conexión:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with cubic Bezier curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.CubicTo(touchPoints[1].Center,
                     touchPoints[2].Center,
                     touchPoints[3].Center);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[2].Center.X,
                    touchPoints[2].Center.Y,
                    touchPoints[3].Center.X,
                    touchPoints[3].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
       touchPoint.Paint(canvas);
    }
}
```

Aquí se está ejecutando:

[![](beziers-images/beziercurve-small.png "Captura de pantalla de la página de la curva de Bézier triple")](beziers-images/beziercurve-large.png#lightbox "Triple captura de pantalla de la página de la curva de Bézier")

Matemáticamente, la curva es una polinómica cúbica. La curva forma una intersección con una línea recta en tres puntos como máximo. En el punto inicial, la curva es siempre un punto tangente y, en la misma dirección que, de una línea recta desde el principio hasta el primer punto de control. En el punto final, la curva es siempre una línea recta desde el segundo control tangente y, en la misma dirección que, apunte al punto final.

La curva de Bézier cúbica siempre está limitada por un cuadrilátero convexo conecta los cuatro puntos. Esto se denomina un *forma convexa*. Si los puntos de control se encuentran en la línea recta entre el punto inicial y final, la curva de Bézier se representa como una línea recta. Pero la curva puede también entre sí, como se muestra en la captura de pantalla terceros.

Un contorno de la ruta de acceso puede contener varias curvas de Bézier cúbicas conectadas, pero la conexión entre dos curvas de Bézier cúbicas será suave solo si los siguientes tres puntos son colineales (es decir, se encuentran en una línea recta):

- el segundo punto de control de la primera curva
- el punto final de la primera curva, que también es el punto inicial de la segunda curva
- el primer punto de control de la segunda curva

En el siguiente artículo en [ **datos de ruta de acceso de SVG**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md), descubrirá unas instalaciones para facilitar la definición de smooth curvas Bézier conectadas.

A veces resulta útil conocer las ecuaciones paramétricas subyacentes que representan una curva Bézier cúbica. Para *t* comprendida entre 0 y 1, las ecuaciones paramétricas son los siguientes:

x (t) = (1 – t) ³x₀ + 3t (1 – t) ²x₁ + 3t² (1 – t) x₂ + t³x₃

y(t) = (1 – t) ³y₀ + 3t (1 – t) ²y₁ + 3t² (1 – t) y₂ + t³y₃

El exponente más alto de 3 confirma que son polynomials cúbicas. Es fácil comprobar que, cuando `t` es igual a 0, el punto es (x₀, y₀), que es el punto inicial y cuándo `t` es igual a 1, el punto es (x₃, y₃), que es el punto final. Cerca del punto inicial (para los valores bajos de `t`), el primer punto de control (x₁, y₁) tiene una fuerte afecta y casi el punto final (valores altos de ' t ') del segundo punto de control (x₂, y₂) tiene un gran efecto.

## <a name="bezier-curve-approximation-to-circular-arcs"></a>Aproximación de la curva de Bézier en arcos circulares

A veces es conveniente utilizar una curva de Bézier para representar un arco circular. Una curva Bézier cúbica puede aproximar un arco circular muy bien hasta un cuarto de círculo, por lo que cuatro curvas Bézier conectadas pueden definir un círculo completo. Esta aproximación se describe en los dos artículos publicados hace más de 25 años:

> Tor Dokken, et al., "Buena aproximación de círculos de curvas de Bézier curvatura continua," *7 de diseño geométrico asistido por equipo* (1990), 33: 41.

> Michael Goldapp, "Aproximación de arcos circulares por Polynomials cúbicas," *diseño geométrico 8 asistido por ordenador* (1991), 227 238.

El diagrama siguiente muestra cuatro puntos con la etiqueta `pto`, `pt1`, `pt2`, y `pt3` definir una curva de Bézier (se muestra en rojo) que se asemeja a un arco circular:

![](beziers-images/bezierarc45.png "Aproximación de un arco circular con una curva de Bézier")

Las líneas de los puntos inicial y final para los puntos de control son tangente para el círculo y la curva de Bézier y tienen una longitud de *L*. El primer artículo citado anteriormente indica que la curva de Bézier que mejor se aproxima a un arco circular cuando esa longitud *L* se calcula del siguiente modo:

L = 4 × tan(α / 4) / 3

La ilustración muestra un ángulo de 45 grados, por lo que L es igual a 0.265. En el código, ese valor se multiplicará por el radio del círculo deseado.

El **arco Circular en curva** página le permite experimentar con la definición de una curva de Bézier para aproximarse a un arco circular para los ángulos que van hasta 180 grados. El [ **BezierCircularArcPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml) crea una instancia de archivo del `SKCanvasView` y un `Slider` para seleccionar el ángulo. El `PaintSurface` controlador de eventos en el [ **BezierCircularArgPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml.cs) archivo de código subyacente utiliza una transformación para establecer el punto (0, 0) en el centro del lienzo. Dibuja un círculo centrado en ese punto para la comparación y, a continuación, calcula los dos puntos de control para la curva de Bézier:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    // Draw the circle
    float radius = Math.Min(info.Width, info.Height) / 3;
    canvas.DrawCircle(0, 0, radius, blackStroke);

    // Get the value of the Slider
    float angle = (float)angleSlider.Value;

    // Calculate length of control point line
    float length = radius * 4 * (float)Math.Tan(Math.PI * angle / 180 / 4) / 3;

    // Calculate sin and cosine for half that angle
    float sin = (float)Math.Sin(Math.PI * angle / 180 / 2);
    float cos = (float)Math.Cos(Math.PI * angle / 180 / 2);

    // Find the end points
    SKPoint point0 = new SKPoint(-radius * sin, radius * cos);
    SKPoint point3 = new SKPoint(radius * sin, radius * cos);

    // Find the control points
    SKPoint point0Normalized = Normalize(point0);
    SKPoint point1 = point0 + new SKPoint(length * point0Normalized.Y,
                                          -length * point0Normalized.X);

    SKPoint point3Normalized = Normalize(point3);
    SKPoint point2 = point3 + new SKPoint(-length * point3Normalized.Y,
                                          length * point3Normalized.X);

    // Draw the points
    canvas.DrawCircle(point0.X, point0.Y, 10, blackFill);
    canvas.DrawCircle(point1.X, point1.Y, 10, blackFill);
    canvas.DrawCircle(point2.X, point2.Y, 10, blackFill);
    canvas.DrawCircle(point3.X, point3.Y, 10, blackFill);

    // Draw the tangent lines
    canvas.DrawLine(point0.X, point0.Y, point1.X, point1.Y, dottedStroke);
    canvas.DrawLine(point3.X, point3.Y, point2.X, point2.Y, dottedStroke);

    // Draw the Bezier curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(point0);
        path.CubicTo(point1, point2, point3);
        canvas.DrawPath(path, redStroke);
    }
}

// Vector methods
SKPoint Normalize(SKPoint v)
{
    float magnitude = Magnitude(v);
    return new SKPoint(v.X / magnitude, v.Y / magnitude);
}

float Magnitude(SKPoint v)
{
    return (float)Math.Sqrt(v.X * v.X + v.Y * v.Y);
}

```

Los puntos inicial y final (`point0` y `point3`) se calculan en función de las ecuaciones paramétricas normales para el círculo. Dado que el círculo se centra en (0, 0), estos puntos pueden también tratar como vectores radiales desde el centro del círculo a la circunferencia. Los puntos de control están en las líneas tangente al círculo, por lo que son ángulos rectos a estos vectores radiales. Un vector de un ángulo recto a otro es simplemente el vector original con las coordenadas X e Y que se intercambian y uno de ellos realiza negativo.

Este es el programa que se ejecuta con distintos ángulos:

[![](beziers-images/beziercirculararc-small.png "Captura de pantalla de la página de arco Circular en curva triple")](beziers-images/beziercirculararc-large.png#lightbox "Triple captura de pantalla de la página de arco Circular en curva")

Fíjese en la captura de pantalla terceros, y verá que la curva de Bézier en particular se desvía de un semicírculo cuando el ángulo de 180 grados, pero se muestra la pantalla de iOS que parece ser para ajustarse a un círculo trimestre perfectamente cuando el ángulo es 90 grados.

Es muy fácil calcular las coordenadas de dos puntos de control cuando el cuarto de círculo está orientado similar al siguiente:

![](beziers-images/bezierarc90.png "Aproximación de un cuarto de círculo con una curva de Bézier")

Si el radio del círculo es 100, a continuación, *L* es 55 y que es un número fácil de recordar.

El **elevar el círculo** página anima una figura entre un círculo y un cuadrado. El círculo se aproxima al cuatro curvas de Bézier cuyas coordenadas se muestran en la primera columna de esta definición de la matriz en la [ `SquaringTheCirclePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/SquaringTheCirclePage.cs) clase:

```csharp
public class SquaringTheCirclePage : ContentPage
{
    SKPoint[,] points =
    {
        { new SKPoint(   0,  100), new SKPoint(     0,    125), new SKPoint() },
        { new SKPoint(  55,  100), new SKPoint( 62.5f,  62.5f), new SKPoint() },
        { new SKPoint( 100,   55), new SKPoint( 62.5f,  62.5f), new SKPoint() },
        { new SKPoint( 100,    0), new SKPoint(   125,      0), new SKPoint() },
        { new SKPoint( 100,  -55), new SKPoint( 62.5f, -62.5f), new SKPoint() },
        { new SKPoint(  55, -100), new SKPoint( 62.5f, -62.5f), new SKPoint() },
        { new SKPoint(   0, -100), new SKPoint(     0,   -125), new SKPoint() },
        { new SKPoint( -55, -100), new SKPoint(-62.5f, -62.5f), new SKPoint() },
        { new SKPoint(-100,  -55), new SKPoint(-62.5f, -62.5f), new SKPoint() },
        { new SKPoint(-100,    0), new SKPoint(  -125,      0), new SKPoint() },
        { new SKPoint(-100,   55), new SKPoint(-62.5f,  62.5f), new SKPoint() },
        { new SKPoint( -55,  100), new SKPoint(-62.5f,  62.5f), new SKPoint() },
        { new SKPoint(   0,  100), new SKPoint(     0,    125), new SKPoint() }
    };
    ...
}
```

La segunda columna contiene las coordenadas de cuatro curvas de Bézier que definen un cuadrado cuya área es aproximadamente el mismo que el área del círculo. (Dibujar un cuadrado con el *exacta* área como un círculo especificado es el clásico problema geométrico irresoluble de [elevar el círculo](https://en.wikipedia.org/wiki/Squaring_the_circle).) Para representar un cuadrado con curvas de Bézier, dos puntos de control para cada curva son los mismos y son colineales con los puntos inicial y final, por lo que la curva de Bézier se representa como una línea recta.

Es la tercera columna de la matriz de valores interpolados para una animación. La página establece un temporizador de 16 milisegundos y el `PaintSurface` controlador se llama a este ritmo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    canvas.Translate(info.Width / 2, info.Height / 2);
    canvas.Scale(Math.Min(info.Width / 300, info.Height / 300));

    // Interpolate
    TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
    float t = (float)(timeSpan.TotalSeconds % 3 / 3);   // 0 to 1 every 3 seconds
    t = (1 + (float)Math.Sin(2 * Math.PI * t)) / 2;     // 0 to 1 to 0 sinusoidally

    for (int i = 0; i < 13; i++)
    {
        points[i, 2] = new SKPoint(
            (1 - t) * points[i, 0].X + t * points[i, 1].X,
            (1 - t) * points[i, 0].Y + t * points[i, 1].Y);
    }

    // Create the path and draw it
    using (SKPath path = new SKPath())
    {
        path.MoveTo(points[0, 2]);

        for (int i = 1; i < 13; i += 3)
        {
            path.CubicTo(points[i, 2], points[i + 1, 2], points[i + 2, 2]);
        }
        path.Close();

        canvas.DrawPath(path, cyanFill);
        canvas.DrawPath(path, blueStroke);
    }
}
```

Los puntos se interpolan basándose en un valor de sinusoidally oscilante `t`. Los puntos interpolados, a continuación, se usan para construir una serie de cuatro curvas Bézier conectadas. Aquí está la animación en ejecución:

[![](beziers-images/squaringthecircle-small.png "Captura de pantalla triple de la Squaring la página de círculo")](beziers-images/squaringthecircle-large.png#lightbox "Triple captura de pantalla de la Squaring la página de círculo")

Una animación de este tipo sería imposible sin curvas de forma algorítmica lo suficientemente flexibles como para representarse como arcos circulares y de líneas rectas.

El **Bézier infinito** página también aprovecha la capacidad de una curva de Bézier para aproximarse a un arco circular. Este es el `PaintSurface` controlador desde la [ `BezierInfinityPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierInfinityPage.cs) clase:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        path.MoveTo(0, 0);                                // Center
        path.CubicTo(  50,  -50,   95, -100,  150, -100); // To top of right loop
        path.CubicTo( 205, -100,  250,  -55,  250,    0); // To far right of right loop
        path.CubicTo( 250,   55,  205,  100,  150,  100); // To bottom of right loop
        path.CubicTo(  95,  100,   50,   50,    0,    0); // Back to center  
        path.CubicTo( -50,  -50,  -95, -100, -150, -100); // To top of left loop
        path.CubicTo(-205, -100, -250,  -55, -250,    0); // To far left of left loop
        path.CubicTo(-250,   55, -205,  100, -150,  100); // To bottom of left loop
        path.CubicTo( -95,  100,  -50,   50,    0,    0); // Back to center
        path.Close();

        SKRect pathBounds = path.Bounds;
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.9f * Math.Min(info.Width / pathBounds.Width,
                                     info.Height / pathBounds.Height));

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 5;

            canvas.DrawPath(path, paint);
        }
    }
}
```

Podría ser un buen ejercicio para trazar estas coordenadas en papel de gráfico para ver cómo se relacionan entre sí. El inicio de sesión infinito se centra en el punto (0, 0), y los dos bucles tienen centros de (–150, 0) y (150, 0) y los radios de 100. En la serie de `CubicTo` comandos, puede ver las coordenadas de puntos de control de valores de –95 y –205 X (estos valores son –150 signos más y menos 55), 205 y 95 (150 signos más y menos 55), así como 250 y –250 de los lados izquierdos y derecho. La única excepción es cuando el inicio de sesión infinito se cruza en el centro. En ese caso, los puntos de control tienen coordenadas con una combinación de 50 y – 50 para enderezar la curva cerca del centro.

Este es el inicio de sesión infinito:

[![](beziers-images/bezierinfinity-small.png "Captura de pantalla de la página de Bezier infinito triple")](beziers-images/bezierinfinity-large.png#lightbox "Triple captura de pantalla de la página de Bezier infinito")

Es un poco más suave hacia el centro que el inicio de sesión de infinito representada por el **arco infinito** página desde la [ **tres maneras de dibujar un arco** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) artículo.

## <a name="the-quadratic-bzier-curve"></a>La curva de Bézier cuadrática

La curva de Bézier cuadrática tiene solo un punto de control y la curva se define por tan sólo tres puntos: el punto inicial, el punto de control y el punto final. Las ecuaciones paramétricas son muy similares a la curva de Bézier cúbica, salvo que el máximo exponente es 2, por lo que la curva es una cuadrática polinómico:

x (t) = (1 – t) ²x₀ + 2t (1 – t) x₁ + t²x₂

y(t) = (1 – t) ²y₀ + 2t (1 – t) y₁ + t²y₂

Para agregar una curva Bézier cuadrática a una ruta de acceso, use el [ `QuadTo` ](xref:SkiaSharp.SKPath.QuadTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint)) método o la [ `QuadTo` ](xref:SkiaSharp.SKPath.QuadTo(System.Single,System.Single,System.Single,System.Single)) sobrecarga con independiente `x` y `y` coordenadas:

```csharp
public void QuadTo (SKPoint point1, SKPoint point2)

public void QuadTo (Single x1, Single y1, Single x2, Single y2)
```

Los métodos de agregan una curva de la posición actual para `point2` con `point1` como el punto de control.

Puede experimentar con curvas Bézier cuadráticas con el **curva cuadrática** página, que es muy similar a la **curva de Bézier** página, salvo que tiene solo tres puntos de toque. Este es el `PaintSurface` controlador en el [ **QuadraticCurve.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/QuadraticCurvePage.xaml.cs) archivo de código subyacente:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with quadratic Bezier
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.QuadTo(touchPoints[1].Center,
                    touchPoints[2].Center);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[1].Center.X,
                    touchPoints[1].Center.Y,
                    touchPoints[2].Center.X,
                    touchPoints[2].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}
```

Y aquí se está ejecutando:

[![](beziers-images/quadraticcurve-small.png "Captura de pantalla de la página de la curva cuadrática triple")](beziers-images/quadraticcurve-large.png#lightbox "Triple captura de pantalla de la página de la curva cuadrática")

Las líneas de puntos tangentes a la curva en el punto inicial y final y cumple en el punto de control.

El Bézier cuadrática es bueno si necesita una curva de una forma general, pero si prefiere la comodidad de un solo punto de control, en lugar de dos. El Bézier cuadrática representa la forma más eficaz que cualquier otra curva, que es la razón por la se usa internamente en Skia para representar arcos elípticos.

Sin embargo, la forma de una curva Bézier cuadrática no es elíptica, motivo por el que varias trayectorias Bezier cuadrática deben aproximarse a un arco elíptico. El Bézier cuadrática en su lugar, es un segmento de un parábola.

## <a name="the-conic-bzier-curve"></a>La curva de Bézier cónicos

La curva de Bézier cónica &mdash; también conocido como el rational curva Bézier cuadrática &mdash; es un agregado recientemente a la familia de curvas de Bézier. Al igual que la curva de Bézier cuadrática, la curva de Bézier cuadrática rational implica un punto inicial, un punto de conexión y un punto de control. Pero la curva de Bézier cuadrática racional también requiere un *peso* valor. Se llama una *rational* cuadrática porque las fórmulas paramétricas implican proporciones.

Las ecuaciones paramétricas para X e Y son relaciones que comparten el mismo denominador. Aquí está la ecuación para el denominador de *t* comprendido entre 0 a 1 y un valor de ponderación de *w*:

ät = (1 – t) ² + 2wt(1 – t) + t²

En teoría, un cuadrática rational puede implicar tres valores de ponderación independiente, uno para cada una de las tres condiciones, pero estos se pueden simplificar a un solo valor de peso en el término medio.

Las ecuaciones paramétricas para las coordenadas X e Y son similares a las ecuaciones paramétricas para el Bézier cuadrática, salvo que el término medio también incluye el valor de ponderación y la expresión se divide por el denominador:

x (t) = ((1 – t) ²x₀ + 2wt (1 – t) x₁ + t²x₂)) ÷ ät

y(t) = ((1 – t) ²y₀ + 2wt (1 – t) y₁ + t²y₂)) ÷ ät

También se denominan Rational curvas Bézier cuadráticas *conics* porque pueden representar exactamente los segmentos de cualquier sección cónicos &mdash; hipérbolas, parábolas, elipses y círculos.

Para agregar una curva de Bézier cuadrática rational a una ruta de acceso, use el [ `ConicTo` ](xref:SkiaSharp.SKPath.ConicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) método o la [ `ConicTo` ](xref:SkiaSharp.SKPath.ConicTo(System.Single,System.Single,System.Single,System.Single,System.Single)) sobrecarga con independiente `x` y `y` coordenadas:

```csharp
public void ConicTo (SKPoint point1, SKPoint point2, Single weight)

public void ConicTo (Single x1, Single y1, Single x2, Single y2, Single weight)
```

Tenga en cuenta la última `weight` parámetro.

El **curva cónicos** página le permite experimentar con estas curvas. La clase `ConicCurvePage` se deriva de `InteractivePage`. El [ **ConicCurvePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml) crea una instancia de archivo un `Slider` para seleccionar un valor de peso entre – 2 y 2. El [ **ConicCurvePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml.cs) archivo de código subyacente crea tres `TouchPoint` objetos y el `PaintSurface` controlador simplemente representa la curva resultante con las líneas para el control de tangente puntos:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with conic curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.ConicTo(touchPoints[1].Center,
                     touchPoints[2].Center,
                     (float)weightSlider.Value);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[1].Center.X,
                    touchPoints[1].Center.Y,
                    touchPoints[2].Center.X,
                    touchPoints[2].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}
```

Aquí se está ejecutando:

[![](beziers-images/coniccurve-small.png "Captura de pantalla de la página de la curva cónicos triple")](beziers-images/coniccurve-large.png#lightbox "Triple captura de pantalla de la página de la curva cónicos")

Como puede ver, el punto de control parece extraer la curva hacia más cuando el peso sea mayor. Cuando el peso es cero, la curva se convierte en una línea recta desde el punto inicial para el punto final.

En teoría, se permiten los pesos negativos y hacer que la curva doblar *ausente* desde el punto de control. Sin embargo, pesos de – 1 o por debajo de la causa el denominador en las ecuaciones paramétricas a valores negativos para determinados valores de *t*. Probablemente por este motivo, los pesos negativos se omiten en el `ConicTo` métodos. El **curva cónicos** programa le permite establecer pesos negativos, pero como puede ver al experimentar, pesos negativos tienen el mismo efecto que una ponderación de cero y hacer que una línea recta que va a representar.

Es muy fácil derivar el punto de control y la ponderación que se usará el `ConicTo` método para dibujar un arco circular hasta (pero sin incluir) un semicírculo. En el diagrama siguiente, líneas de tangente de los puntos inicial y final se encuentran en el punto de control.

![](beziers-images/conicarc.png "Una representación de arco cónico de un arco circular")

Puede usar trigonometría para determinar la distancia del punto de control del centro del círculo: Es el radio del círculo dividido entre el coseno de la mitad del ángulo α. Para dibujar un arco circular entre los puntos inicial y final, establezca la ponderación en ese mismo coseno la mitad del ángulo. Tenga en cuenta que si el ángulo es 180 grados, a continuación, nunca se encuentran en las líneas de tangente y el peso es cero. Pero para ángulos inferior a 180 grados, los cálculos matemáticos funciona bien.

El **cónicos arco Circular** página muestra esto. El [ **ConicCircularArc.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml) crea una instancia de archivo un `Slider` para seleccionar el ángulo. El `PaintSurface` controlador en el [ **ConicCircularArc.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml.cs) archivo de código subyacente calcula el punto de control y el peso:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    // Draw the circle
    float radius = Math.Min(info.Width, info.Height) / 4;
    canvas.DrawCircle(0, 0, radius, blackStroke);

    // Get the value of the Slider
    float angle = (float)angleSlider.Value;

    // Calculate sin and cosine for half that angle
    float sin = (float)Math.Sin(Math.PI * angle / 180 / 2);
    float cos = (float)Math.Cos(Math.PI * angle / 180 / 2);

    // Find the points and weight
    SKPoint point0 = new SKPoint(-radius * sin, radius * cos);
    SKPoint point1 = new SKPoint(0, radius / cos);
    SKPoint point2 = new SKPoint(radius * sin, radius * cos);
    float weight = cos;

    // Draw the points
    canvas.DrawCircle(point0.X, point0.Y, 10, blackFill);
    canvas.DrawCircle(point1.X, point1.Y, 10, blackFill);
    canvas.DrawCircle(point2.X, point2.Y, 10, blackFill);

    // Draw the tangent lines
    canvas.DrawLine(point0.X, point0.Y, point1.X, point1.Y, dottedStroke);
    canvas.DrawLine(point2.X, point2.Y, point1.X, point1.Y, dottedStroke);

    // Draw the conic
    using (SKPath path = new SKPath())
    {
        path.MoveTo(point0);
        path.ConicTo(point1, point2, weight);
        canvas.DrawPath(path, redStroke);
    }
}
```

Como puede ver, no hay ninguna diferencia visual entre el `ConicTo` ruta de acceso que se muestran en rojo y el círculo subyacente aparece como referencia:

[![](beziers-images/coniccirculararc-small.png "Captura de pantalla de la página de arco Circular cónicos triple")](beziers-images/coniccirculararc-large.png#lightbox "Triple captura de pantalla de la página cónicos arco Circular")

Pero el ángulo de 180 grados y el error de matemáticas.

En este caso es lamentable que `ConicTo` no admite los pesos negativos, ya que en teoría (según las ecuaciones paramétricas), se puede completar el círculo con otra llamada a `ConicTo` con los mismos puntos pero con un valor negativo del peso. Esto permitiría crear un círculo completo con sólo dos `ConicTo` curvas según cualquier ángulo entre (pero no incluyendo) cero grados y 180 grados.


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
