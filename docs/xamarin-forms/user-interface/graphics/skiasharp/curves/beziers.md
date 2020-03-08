---
title: Tres tipos de curvas de Bézier
description: En este artículo se explica cómo usar SkiaSharp para representar las curvas de Bézier cúbicas, cuadráticas y cónicos en las aplicaciones de Xamarin.Forms y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8FE0F6DC-16BC-435F-9626-DD1790C0145A
author: davidbritch
ms.author: dabritch
ms.date: 05/25/2017
ms.openlocfilehash: 1cf061f2ff27720ad78567bc26f00d99c5456f04
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/07/2020
ms.locfileid: "78916409"
---
# <a name="three-types-of-bzier-curves"></a>Tres tipos de curvas de Bézier

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explorar cómo usar SkiaSharp para representar curvas Bézier cúbicas, cuadráticas y cónicas_

La curva de Bézier se denomina después Pierre Bézier (1910: 1999), un ingeniero de francés de la empresa automotriz Renault, que utilizaban la curva para el diseño asistido por ordenador de cuerpos de automóvil.

Se sabe que las curvas de Bézier son adecuadas para el diseño interactivo: están bien comportados &mdash; es decir, no hay singularidad que provoquen que la curva se convierta en una &mdash; infinita o poco manejable y, por lo general, estéticamente agradable:

![Una curva de Bézier de ejemplo](beziers-images/beziersample.png)

Normalmente se definen los contornos de caracteres de las fuentes basadas en equipo con curvas de Bézier.

El artículo de Wikipedia sobre [**curva de Bézier**](https://en.wikipedia.org/wiki/B%C3%A9zier_curve) contiene información general útil. El término *curva de Bézier* realmente hace referencia a una familia de curvas similares. SkiaSharp admite tres tipos de curvas de Bézier, denominados el *cúbico*, el *cuadrático*y el *cónico*. El cónico también se conoce como el *cuadrático racional*.

## <a name="the-cubic-bzier-curve"></a>La curva de Bézier cúbica

El curva Bézier cúbica es el tipo de curva de Bézier que pensar en la mayoría de los desarrolladores cuando aparezca el asunto de curvas de Bézier.

Puede Agregar una curva Bézier cúbica a un objeto `SKPath` mediante el método [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKPoint)) con tres parámetros `SKPoint`, o la sobrecarga [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo(System.Single,System.Single,System.Single,System.Single,System.Single,System.Single)) con parámetros `x` y `y` independientes:

```csharp
public void CubicTo (SKPoint point1, SKPoint point2, SKPoint point3)

public void CubicTo (Single x1, Single y1, Single x2, Single y2, Single x3, Single y3)
```

La curva comienza en el punto actual del contorno. La curva de Bézier cúbica completa se define por cuatro puntos:

- punto inicial: el punto actual del contorno o (0,0) si no se ha llamado a `MoveTo`
- primer punto de control: `point1` en la llamada `CubicTo`
- segundo punto de control: `point2` en la llamada `CubicTo`
- punto final: `point3` en la llamada `CubicTo`

La curva resultante comienza en el punto inicial y termina en el punto final. Por lo general la curva no pasa por dos puntos de control; en su lugar, el control de puntos de función como imanes para extraer la curva hacia ellos.

Es la mejor forma de hacerse una idea de la curva de Bézier cúbica mediante experimentación. Este es el propósito de la página de **curva de Bézier** , que deriva de `InteractivePage`. El archivo [**BezierCurvePage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml) crea instancias del `SKCanvasView` y de un `TouchEffect`. El archivo de código subyacente [**BezierCurvePage.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml.cs) crea cuatro objetos `TouchPoint` en su constructor. El controlador de eventos `PaintSurface` crea un `SKPath` para representar una curva de Bézier basada en los cuatro objetos `TouchPoint` y también dibuja líneas tangentes de puntos desde los puntos de control a los extremos:

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

[![captura de pantalla triple de la página curva de Bézier](beziers-images/beziercurve-small.png)](beziers-images/beziercurve-large.png#lightbox)

Matemáticamente, la curva es una polinómica cúbica. La curva forma una intersección con una línea recta en tres puntos como máximo. En el punto inicial, la curva es siempre un punto tangente y, en la misma dirección que, de una línea recta desde el principio hasta el primer punto de control. En el punto final, la curva es siempre una línea recta desde el segundo control tangente y, en la misma dirección que, apunte al punto final.

La curva de Bézier cúbica siempre está limitada por un cuadrilátero convexo conecta los cuatro puntos. Esto se denomina *casco convexo*. Si los puntos de control se encuentran en la línea recta entre el punto inicial y final, la curva de Bézier se representa como una línea recta. Pero la curva puede también entre sí, como se muestra en la captura de pantalla terceros.

Un contorno de la ruta de acceso puede contener varias curvas de Bézier cúbicas conectadas, pero la conexión entre dos curvas de Bézier cúbicas será suave solo si los siguientes tres puntos son colineales (es decir, se encuentran en una línea recta):

- el segundo punto de control de la primera curva
- el punto final de la primera curva, que también es el punto inicial de la segunda curva
- el primer punto de control de la segunda curva

En el siguiente artículo sobre los datos de la [**ruta de acceso de SVG**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md), detectará una utilidad para facilitar la definición de curvas de Bézier con conexión fluida.

A veces resulta útil conocer las ecuaciones paramétricas subyacentes que representan una curva Bézier cúbica. Para *t* de 0 a 1, las ecuaciones paramétricas son las siguientes:

x (t) = (1 – t) ³x₀ + 3t (1 – t) ²x₁ + 3t² (1 – t) x₂ + t³x₃

y(t) = (1 – t) ³y₀ + 3t (1 – t) ²y₁ + 3t² (1 – t) y₂ + t³y₃

El exponente más alto de 3 confirma que son polynomials cúbicas. Es fácil comprobar que cuando `t` es igual a 0, el punto es (x ₀, y ₀), que es el punto inicial, y cuando `t` es igual a 1, el punto es (x ₃, y ₃), que es el punto final. Cerca del punto inicial (para los valores bajos de `t`), el primer punto de control (x ₁, y ₁) tiene un efecto fuerte y cerca del punto final (valores altos de ' t ') el segundo punto de control (x ₂, y ₂) tiene un efecto fuerte.

## <a name="bezier-curve-approximation-to-circular-arcs"></a>Aproximación de la curva de Bézier en arcos circulares

A veces es conveniente usar una curva de Bézier para representar un arco circular. Una curva Bézier cúbica puede aproximar un arco circular hasta un círculo de un cuarto, por lo que cuatro curvas Bezier conectadas pueden definir un círculo completo. Esta aproximación se describe en los dos artículos publicados hace más de 25 años:

> Tor Dokken, et al, "buena aproximación de los círculos por curvas de Bézier continuas con curvatura", " *diseño geométrico asistido por PC 7* (1990), 33-41.

> Michael Goldapp, "aproximación de arcos circulares por polinómicos cúbicos," *diseño geométrico asistido por equipo 8* (1991), 227-238.

En el diagrama siguiente se muestran cuatro puntos con la etiqueta `pto`, `pt1`, `pt2`y `pt3` que definen una curva de Bézier (que se muestra en rojo) que se aproxima a un arco circular:

![Aproximación de un arco circular con una curva de Bézier](beziers-images/bezierarc45.png)

Las líneas de los puntos inicial y final a los puntos de control son tangentes al círculo y a la curva Bézier, y tienen una longitud de *L*. El primer artículo mencionado anteriormente indica que la curva de Bézier se aproxima mejor a un arco circular cuando esa longitud *L* se calcula de la siguiente manera:

L = 4 × tan(α / 4) / 3

La ilustración muestra un ángulo de 45 grados, por lo que L es igual a 0.265. En el código, ese valor se multiplicará por el radio del círculo deseado.

La página de **arco circular Bézier** permite experimentar con la definición de una curva de Bézier para aproximar un arco circular para ángulos de hasta 180 grados. El archivo [**BezierCircularArcPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml) crea una instancia del `SKCanvasView` y un `Slider` para seleccionar el ángulo. El controlador de eventos `PaintSurface` del archivo de código subyacente [**BezierCircularArgPage.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml.cs) usa una transformación para establecer el punto (0,0) en el centro del lienzo. Dibuja un círculo centrado en ese punto para la comparación y, a continuación, calcula los dos puntos de control para la curva de Bézier:

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

Los puntos inicial y final (`point0` y `point3`) se calculan en función de las ecuaciones paramétricas normales del círculo. Dado que el círculo se centra en (0, 0), estos puntos pueden también tratar como vectores radiales desde el centro del círculo a la circunferencia. Los puntos de control están en las líneas tangente al círculo, por lo que son ángulos rectos a estos vectores radiales. Un vector de un ángulo recto a otro es simplemente el vector original con las coordenadas X e Y que se intercambian y uno de ellos realiza negativo.

Este es el programa que se ejecuta con distintos ángulos:

[![captura de pantalla triple de la página de arco circular de Bézier](beziers-images/beziercirculararc-small.png)](beziers-images/beziercirculararc-large.png#lightbox)

Fíjese en la captura de pantalla terceros, y verá que la curva de Bézier en particular se desvía de un semicírculo cuando el ángulo de 180 grados, pero se muestra la pantalla de iOS que parece ser para ajustarse a un círculo trimestre perfectamente cuando el ángulo es 90 grados.

Es muy fácil calcular las coordenadas de dos puntos de control cuando el cuarto de círculo está orientado similar al siguiente:

![Aproximación de un círculo de cuarto con una curva de Bézier](beziers-images/bezierarc90.png)

Si el radio del círculo es 100, entonces *L* es 55 y es un número fácil de recordar.

La página **elevar el círculo** anima una figura entre un círculo y un cuadrado. El círculo se aproxima por cuatro curvas de Bézier cuyas coordenadas se muestran en la primera columna de esta definición de matriz en la clase [`SquaringTheCirclePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/SquaringTheCirclePage.cs) :

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

La segunda columna contiene las coordenadas de cuatro curvas de Bézier que definen un cuadrado cuya área es aproximadamente el mismo que el área del círculo. (Dibujar un cuadrado con el área *exacta* como un círculo determinado es el problema geométrico clásico sin resolver de [elevar el círculo](https://en.wikipedia.org/wiki/Squaring_the_circle)). Para representar un cuadrado con curvas Bezier, los dos puntos de control de cada curva son iguales y se colocan con los puntos inicial y final, por lo que la curva de Bézier se representa como una línea recta.

Es la tercera columna de la matriz de valores interpolados para una animación. La página establece un temporizador de 16 milisegundos y se llama al controlador de `PaintSurface` en esa frecuencia:

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

Los puntos se interpolan en función de un valor oscilante sinusoidalmente de `t`. Los puntos interpolados, a continuación, se usan para construir una serie de cuatro curvas Bézier conectadas. Aquí está la animación en ejecución:

[![captura de pantalla triple de la página elevar del círculo](beziers-images/squaringthecircle-small.png)](beziers-images/squaringthecircle-large.png#lightbox)

Una animación de este tipo sería imposible sin curvas de forma algorítmica lo suficientemente flexibles como para representarse como arcos circulares y de líneas rectas.

La página de **infinito de Bézier** también saca partido de la capacidad de una curva de Bézier para aproximar un arco circular. A continuación se muestra el controlador de `PaintSurface` de la clase [`BezierInfinityPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierInfinityPage.cs) :

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

Podría ser un buen ejercicio para trazar estas coordenadas en papel de gráfico para ver cómo se relacionan entre sí. El inicio de sesión infinito se centra en el punto (0, 0), y los dos bucles tienen centros de (–150, 0) y (150, 0) y los radios de 100. En la serie de comandos de `CubicTo`, puede ver coordenadas X de los puntos de control que toman valores de – 95 y – 205 (esos valores son – 150 Plus y menos 55), 205 y 95 (150 Plus y menos 55), así como 250 y – 250 para los lados derecho e izquierdo. La única excepción es cuando el inicio de sesión infinito se cruza en el centro. En ese caso, los puntos de control tienen coordenadas con una combinación de 50 y – 50 para enderezar la curva cerca del centro.

Este es el inicio de sesión infinito:

[![captura de pantalla triple de la página de infinidad de Bézier](beziers-images/bezierinfinity-small.png)](beziers-images/bezierinfinity-large.png#lightbox)

Es algo más suave hacia el centro que el signo de infinito representado por la página de **infinidad de arco** de las [**tres formas de dibujar un arco**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) .

## <a name="the-quadratic-bzier-curve"></a>La curva de Bézier cuadrática

La curva de Bézier cuadrática tiene solo un punto de control y la curva se define por tan sólo tres puntos: el punto inicial, el punto de control y el punto final. Las ecuaciones paramétricas son muy similares a la curva de Bézier cúbica, salvo que el máximo exponente es 2, por lo que la curva es una cuadrática polinómico:

x (t) = (1 – t) ²x₀ + 2t (1 – t) x₁ + t²x₂

y(t) = (1 – t) ²y₀ + 2t (1 – t) y₁ + t²y₂

Para agregar una curva cuadrática de Bézier a un trazado, use el método [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint)) o la sobrecarga [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo(System.Single,System.Single,System.Single,System.Single)) con coordenadas `x` y `y` independientes:

```csharp
public void QuadTo (SKPoint point1, SKPoint point2)

public void QuadTo (Single x1, Single y1, Single x2, Single y2)
```

Los métodos agregan una curva desde la posición actual a `point2` con `point1` como punto de control.

Puede experimentar con curvas de Bézier cuadráticas con la página de **curva cuadrática** , que es muy similar a la página de **curva de Bézier** , excepto en que solo tiene tres puntos táctiles. Este es el controlador de `PaintSurface` en el archivo de código subyacente [**QuadraticCurve.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/QuadraticCurvePage.xaml.cs) :

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

[![captura de pantalla triple de la página curva cuadrática](beziers-images/quadraticcurve-small.png)](beziers-images/quadraticcurve-large.png#lightbox)

Las líneas de puntos tangentes a la curva en el punto inicial y final y cumple en el punto de control.

El Bézier cuadrática es bueno si necesita una curva de una forma general, pero si prefiere la comodidad de un solo punto de control, en lugar de dos. El Bézier cuadrática representa la forma más eficaz que cualquier otra curva, que es la razón por la se usa internamente en Skia para representar arcos elípticos.

Sin embargo, la forma de una curva cuadrática de Bézier no es elíptica, por lo que es necesario que se requieran varias Bézier cuadráticas para aproximar un arco elíptico. La trayectoria Bézier cuadrática es un segmento de una parabola.

## <a name="the-conic-bzier-curve"></a>La curva de Bézier cónicos

La curva cónica de Bézier &mdash; también conocida como la curva de Bézier cuadrática cuadrático &mdash; es una adición relativamente reciente a la familia de curvas de Bézier. Al igual que la curva de Bézier cuadrática, la curva de Bézier cuadrática rational implica un punto inicial, un punto de conexión y un punto de control. Pero la curva de Bézier cuadrática cuadrático también requiere un valor de *peso* . Se denomina una cuadrático *racional* porque las fórmulas paramétricas implican proporciones.

Las ecuaciones paramétricas para X e Y son relaciones que comparten el mismo denominador. Esta es la ecuación del denominador para *t* que va de 0 a 1 y un valor de peso de *w*:

ät = (1 – t) ² + 2wt(1 – t) + t²

En teoría, un cuadrática rational puede implicar tres valores de ponderación independiente, uno para cada una de las tres condiciones, pero estos se pueden simplificar a un solo valor de peso en el término medio.

Las ecuaciones paramétricas para las coordenadas X e Y son similares a las ecuaciones paramétricas para el Bézier cuadrática, salvo que el término medio también incluye el valor de ponderación y la expresión se divide por el denominador:

x (t) = ((1 – t) ²x₀ + 2wt (1 – t) x₁ + t²x₂)) ÷ ät

y(t) = ((1 – t) ²y₀ + 2wt (1 – t) y₁ + t²y₂)) ÷ ät

Las curvas Bézier cuadráticas racional también se denominan *cónicas* , ya que pueden representar exactamente los segmentos de cualquier sección cónica &mdash; hyperbolas, PARABOLAS, elipses y círculos.

Para agregar una curva racional cuadrática de Bézier a un trazado, use el método [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) o la sobrecarga [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo(System.Single,System.Single,System.Single,System.Single,System.Single)) con coordenadas `x` y `y` independientes:

```csharp
public void ConicTo (SKPoint point1, SKPoint point2, Single weight)

public void ConicTo (Single x1, Single y1, Single x2, Single y2, Single weight)
```

Observe el parámetro `weight` final.

La página **curva cónica** le permite experimentar con estas curvas. La clase `ConicCurvePage` se deriva de `InteractivePage`. El archivo [**ConicCurvePage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml) crea una instancia de un `Slider` para seleccionar un valor de peso entre-2 y 2. El archivo de código subyacente [**ConicCurvePage.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml.cs) crea tres objetos `TouchPoint`, y el controlador `PaintSurface` simplemente representa la curva resultante con las líneas tangentes en los puntos de control:

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

[![captura de pantalla triple de la página curva cónica](beziers-images/coniccurve-small.png)](beziers-images/coniccurve-large.png#lightbox)

Como puede ver, el punto de control parece extraer la curva hacia más cuando el peso sea mayor. Cuando el peso es cero, la curva se convierte en una línea recta desde el punto inicial para el punto final.

En teoría, se permiten pesos negativos y hacen que la curva se pliegue *fuera* del punto de control. Sin embargo, los pesos de – 1 o inferior hacen que el denominador de las ecuaciones paramétricas sea negativo para valores concretos de *t*. Probablemente por este motivo, los pesos negativos se omiten en los métodos `ConicTo`. El programa de **curva cónica** le permite establecer pesos negativos, pero como puede ver mediante experimentos, los pesos negativos tienen el mismo efecto que una ponderación de cero y hacen que se represente una línea recta.

Es muy fácil derivar el punto de control y el peso para usar el método `ConicTo` para dibujar un arco circular hasta (pero sin incluir) un semicírculo. En el diagrama siguiente, líneas de tangente de los puntos inicial y final se encuentran en el punto de control.

![Representación de arco cónico de un arco circular](beziers-images/conicarc.png)

Puede usar para determinar la distancia del punto de control desde el centro del círculo trigonometría: es el radio del círculo dividido por el coseno de la mitad el ángulo α. Para dibujar un arco circular entre los puntos inicial y final, establezca la ponderación en ese mismo coseno la mitad del ángulo. Tenga en cuenta que si el ángulo es 180 grados, a continuación, nunca se encuentran en las líneas de tangente y el peso es cero. Pero para ángulos inferior a 180 grados, los cálculos matemáticos funciona bien.

En la página **arco circular cónico** se muestra esto. El archivo [**ConicCircularArc. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml) crea una instancia de un `Slider` para seleccionar el ángulo. El controlador de `PaintSurface` del archivo de código subyacente [**ConicCircularArc.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml.cs) calcula el punto de control y el peso:

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

Como puede ver, no hay ninguna diferencia visual entre el `ConicTo` la ruta de acceso que aparece en rojo y el círculo subyacente que se muestra como referencia:

[![captura de pantalla triple de la página de arco circular cónico](beziers-images/coniccirculararc-small.png)](beziers-images/coniccirculararc-large.png#lightbox)

Pero el ángulo de 180 grados y el error de matemáticas.

En este caso, es lamentable que `ConicTo` no admita pesos negativos, porque en teoría (según las ecuaciones paramétricas), el círculo se puede completar con otra llamada a `ConicTo` con los mismos puntos pero con un valor negativo de la ponderación. Esto permitiría crear un círculo completo con solo dos curvas `ConicTo` basadas en cualquier ángulo entre (pero sin incluir) cero grados y 180 grados.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
