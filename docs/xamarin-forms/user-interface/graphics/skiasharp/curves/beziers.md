---
title: "tres tipos de curvas de Bézier" Descripción: "en este artículo se explica cómo usar SkiaSharp para representar curvas Bézier cúbicas, cuadráticas y cónicas en Xamarin.Forms aplicaciones, y se muestra con código de ejemplo".
MS. Prod: Xamarin ms. Technology: Xamarin-skiasharp ms. AssetID: 8FE0F6DC-16BC-435F-9626-DD1790C0145A Author: davidbritch ms. Author: dabritch ms. Date: 05/25/2017 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="three-types-of-bzier-curves"></a>Tres tipos de curvas de Bézier

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explorar cómo usar SkiaSharp para representar curvas Bézier cúbicas, cuadráticas y cónicas_

La curva de Bézier se denomina después de Pierre Bézier (1910 – 1999), un ingeniero francés en la compañía de automóviles Renault, que usó la curva para el diseño asistido por PC de los cuerpos de automóvil.

Se sabe que las curvas de Bézier son adecuadas para el diseño interactivo: se comportan correctamente &mdash; en otras palabras, no hay singularidad que provoquen que la curva se convierta en infinita o no manejable &mdash; y, por lo general, estéticamente agradable:

![Una curva de Bézier de ejemplo](beziers-images/beziersample.png)

Los contornos de caracteres de las fuentes basadas en equipo normalmente se definen con curvas de Bézier.

El artículo de Wikipedia sobre [**curva de Bézier**](https://en.wikipedia.org/wiki/B%C3%A9zier_curve) contiene información general útil. El término *curva de Bézier* realmente hace referencia a una familia de curvas similares. SkiaSharp admite tres tipos de curvas de Bézier, denominados el *cúbico*, el *cuadrático*y el *cónico*. El cónico también se conoce como el *cuadrático racional*.

## <a name="the-cubic-bzier-curve"></a>Curva cúbica de Bézier

El cúbico es el tipo de curva de Bézier que la mayoría de los desarrolladores piensan cuando aparece el asunto de las curvas de Bézier.

Puede Agregar una curva Bézier cúbica a un `SKPath` objeto mediante el [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKPoint)) método con tres `SKPoint` parámetros, o la [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo(System.Single,System.Single,System.Single,System.Single,System.Single,System.Single)) sobrecarga con `x` parámetros y independientes `y` :

```csharp
public void CubicTo (SKPoint point1, SKPoint point2, SKPoint point3)

public void CubicTo (Single x1, Single y1, Single x2, Single y2, Single x3, Single y3)
```

La curva comienza en el punto actual del contorno. La curva de Bézier cúbica completa se define mediante cuatro puntos:

- punto inicial: el punto actual del contorno o (0,0) si no se `MoveTo` ha llamado a.
- primer punto de control: `point1` en la `CubicTo` llamada
- segundo punto de control: `point2` en la `CubicTo` llamada
- punto final: `point3` en la `CubicTo` llamada

La curva resultante comienza en el punto inicial y termina en el punto final. Por lo general, la curva no pasa a través de los dos puntos de control; en su lugar, los puntos de control funcionan de forma muy similar a los imanes para extraer la curva hacia ellos.

La mejor manera de hacerse una idea de la curva Bézier cúbica es por experimentación. Este es el propósito de la página de **curva de Bézier** , que deriva de `InteractivePage` . El archivo [**BezierCurvePage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml) crea instancias de `SKCanvasView` y `TouchEffect` . El archivo de código subyacente [**BezierCurvePage.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml.cs) crea cuatro `TouchPoint` objetos en su constructor. El `PaintSurface` controlador de eventos crea un objeto `SKPath` para representar una curva de Bézier basada en los cuatro `TouchPoint` objetos y también dibuja líneas tangentes de puntos desde los puntos de control a los extremos:

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

Aquí se ejecuta:

[![Captura de pantalla triple de la página curva de Bézier](beziers-images/beziercurve-small.png)](beziers-images/beziercurve-large.png#lightbox)

Matemáticamente, la curva es una polinómica cúbica. La curva corta una línea recta en tres puntos como máximo. En el punto inicial, la curva siempre es tangente a y en la misma dirección que una línea recta desde el punto inicial hasta el primer punto de control. En el extremo, la curva siempre es tangente a y en la misma dirección que una línea recta desde el segundo punto de control hasta el punto final.

La curva Bézier cúbica siempre está limitada por una cuadrangular convexa que conecta los cuatro puntos. Esto se denomina *casco convexo*. Si los puntos de control se encuentran en la línea recta entre el punto inicial y el final, la curva de Bézier se representa como una línea recta. Pero la curva también puede cruzarse, como se muestra en la tercera captura de pantalla.

Un contorno de ruta de acceso puede contener varias curvas de Bézier cúbicas conectadas, pero la conexión entre dos curvas Bézier cúbicas solo será suave si los tres puntos siguientes son colineales (es decir, se encuentran en una línea recta):

- segundo punto de control de la primera curva.
- punto final de la primera curva, que también es el punto inicial de la segunda curva.
- primer punto de control de la segunda curva.

En el siguiente artículo sobre los datos de la [**ruta de acceso de SVG**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md), detectará una utilidad para facilitar la definición de curvas de Bézier con conexión fluida.

A veces resulta útil conocer las ecuaciones paramétricas subyacentes que representan una curva Bézier cúbica. Para *t* de 0 a 1, las ecuaciones paramétricas son las siguientes:

x (t) = (1 – t) ³ x ₀ + 3T (1 – t) ² x ₁ + 3T ² (1 – t) x ₂ + t ³ x ₃

y (t) = (1 – t) ³ y ₀ + 3T (1 – t) ² y ₁ + 3T ² (1 – t) y ₂ + t ³ y ₃

El exponente más alto de 3 confirma que son polinómicos cúbicas. Es fácil comprobar que cuando es `t` igual a 0, el punto es (x ₀, y ₀), que es el punto inicial, y cuando es `t` igual a 1, el punto es (x ₃, y ₃), que es el punto final. Cerca del punto inicial (para los valores bajos de `t` ), el primer punto de control (x ₁, y ₁) tiene un efecto fuerte, y cerca del punto final (valores altos de ' t ') el segundo punto de control (x ₂, y ₂) tiene un efecto fuerte.

## <a name="bezier-curve-approximation-to-circular-arcs"></a>Aproximación de curva de Bézier a arcos circulares

A veces es conveniente usar una curva de Bézier para representar un arco circular. Una curva Bézier cúbica puede aproximar un arco circular hasta un círculo de un cuarto, por lo que cuatro curvas Bezier conectadas pueden definir un círculo completo. Esta aproximación se trata en dos artículos publicados hace más de 25 años:

> Tor Dokken, et al, "buena aproximación de los círculos por curvas de Bézier continuas con curvatura", " *diseño geométrico asistido por PC 7* (1990), 33-41.

> Michael Goldapp, "aproximación de arcos circulares por polinómicos cúbicos," *diseño geométrico asistido por equipo 8* (1991), 227-238.

En el diagrama siguiente se muestran cuatro puntos etiquetados `pto` ,, `pt1` `pt2` y `pt3` que definen una curva de Bézier (que se muestra en rojo) que se aproxima a un arco circular:

![Aproximación de un arco circular con una curva de Bézier](beziers-images/bezierarc45.png)

Las líneas de los puntos inicial y final a los puntos de control son tangentes al círculo y a la curva Bézier, y tienen una longitud de *L*. El primer artículo mencionado anteriormente indica que la curva de Bézier se aproxima mejor a un arco circular cuando esa longitud *L* se calcula de la siguiente manera:

L = 4 × tan (α/4)/3

En la ilustración se muestra un ángulo de 45 grados, de modo que L es igual a 0,265. En el código, ese valor se multiplicará por el radio deseado del círculo.

La página de **arco circular Bézier** permite experimentar con la definición de una curva de Bézier para aproximar un arco circular para ángulos de hasta 180 grados. El archivo [**BezierCircularArcPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml) crea una instancia de `SKCanvasView` y un `Slider` para seleccionar el ángulo. El `PaintSurface` controlador de eventos en el archivo de código subyacente [**BezierCircularArgPage.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml.cs) usa una transformación para establecer el punto (0,0) en el centro del lienzo. Dibuja un círculo centrado en ese punto para la comparación y, a continuación, calcula los dos puntos de control para la curva de Bézier:

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

Los puntos de inicio y finalización ( `point0` y `point3` ) se calculan en función de las ecuaciones paramétricas normales del círculo. Dado que el círculo está centrado en (0,0), estos puntos también se pueden tratar como vectores radiales desde el centro del círculo hasta la circunferencia. Los puntos de control se encuentran en líneas que son tangentes al círculo, por lo que están en los ángulos rectos de estos vectores radiales. Un vector con un ángulo recto en otro es simplemente el vector original con las coordenadas X e y intercambiados y uno de ellos se hizo negativo.

Este es el programa que se ejecuta con distintos ángulos:

[![Captura de pantalla triple de la página de arco circular de Bézier](beziers-images/beziercirculararc-small.png)](beziers-images/beziercirculararc-large.png#lightbox)

Mire atentamente la tercera captura de pantalla y verá que la curva de Bézier se desvía especialmente de un semicírculo cuando el ángulo es de 180 grados, pero la pantalla de iOS muestra que parece que se ajusta a un cuarto de círculo justo cuando el ángulo es de 90 grados.

Calcular las coordenadas de los dos puntos de control es bastante sencillo cuando el círculo del cuarto está orientado de la manera siguiente:

![Aproximación de un círculo de cuarto con una curva de Bézier](beziers-images/bezierarc90.png)

Si el radio del círculo es 100, entonces *L* es 55 y es un número fácil de recordar.

La página **elevar el círculo** anima una figura entre un círculo y un cuadrado. El círculo se aproxima por cuatro curvas de Bézier cuyas coordenadas se muestran en la primera columna de esta definición de matriz en la [`SquaringTheCirclePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/SquaringTheCirclePage.cs) clase:

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

La segunda columna contiene las coordenadas de cuatro curvas de Bézier que definen un cuadrado cuya área es aproximadamente la misma que el área del círculo. (Dibujar un cuadrado con el área *exacta* como un círculo determinado es el problema geométrico clásico sin resolver de [elevar el círculo](https://en.wikipedia.org/wiki/Squaring_the_circle)). Para representar un cuadrado con curvas Bezier, los dos puntos de control de cada curva son iguales y se colocan con los puntos inicial y final, por lo que la curva de Bézier se representa como una línea recta.

La tercera columna de la matriz es para los valores interpolados de una animación. La página establece un temporizador de 16 milisegundos y `PaintSurface` se llama al controlador en esa frecuencia:

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

Los puntos se interpolan en función de un valor oscilante sinusoidalmente de `t` . A continuación, los puntos interpolados se usan para construir una serie de cuatro curvas de Bézier conectadas. Esta es la animación en ejecución:

[![Captura de pantalla triple de la página elevar del círculo](beziers-images/squaringthecircle-small.png)](beziers-images/squaringthecircle-large.png#lightbox)

Esta animación sería imposible sin curvas que sean lo suficientemente flexibles como para representarse como arcos circulares y líneas rectas.

La página de **infinito de Bézier** también saca partido de la capacidad de una curva de Bézier para aproximar un arco circular. Este es el `PaintSurface` controlador de la [`BezierInfinityPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierInfinityPage.cs) clase:

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

Podría ser un buen ejercicio trazar estas coordenadas en el papel del gráfico para ver cómo se relacionan. El signo de infinito se centra alrededor del punto (0,0) y los dos bucles tienen centros de (– 150, 0) y (150, 0) y radios de 100. En la serie de `CubicTo` comandos, puede ver coordenadas X de los puntos de control que toman valores de – 95 y – 205 (esos valores son – 150 Plus y menos 55), 205 y 95 (150 Plus y menos 55), así como 250 y – 250 para los lados derecho e izquierdo. La única excepción es cuando el signo de infinito se cruza en el centro. En ese caso, los puntos de control tienen coordenadas con una combinación de 50 y – 50 para enderezar la curva cerca del centro.

Este es el signo de infinito:

[![Captura de pantalla triple de la página de infinidad de Bézier](beziers-images/bezierinfinity-small.png)](beziers-images/bezierinfinity-large.png#lightbox)

Es algo más suave hacia el centro que el signo de infinito representado por la página de **infinidad de arco** de las [**tres formas de dibujar un arco**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) .

## <a name="the-quadratic-bzier-curve"></a>Curva cuadrática de Bézier

La curva cuadrática de Bézier tiene solo un punto de control y la curva se define solo en tres puntos: el punto inicial, el punto de control y el punto final. Las ecuaciones paramétricas son muy similares a las curvas Bézier cúbicas, salvo que el exponente superior es 2, por lo que la curva es una polinómica cuadrática:

x (t) = (1 – t) ² x ₀ + 2T (1 – t) x ₁ + t ² x ₂

y (t) = (1 – t) ² y ₀ + 2T (1 – t) y ₁ + t ² y ₂

Para agregar una curva cuadrática de Bézier a un trazado, use el [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint)) método o la [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo(System.Single,System.Single,System.Single,System.Single)) sobrecarga con `x` coordenadas y independientes `y` :

```csharp
public void QuadTo (SKPoint point1, SKPoint point2)

public void QuadTo (Single x1, Single y1, Single x2, Single y2)
```

Los métodos agregan una curva desde la posición actual a `point2` con `point1` como el punto de control.

Puede experimentar con curvas de Bézier cuadráticas con la página de **curva cuadrática** , que es muy similar a la página de **curva de Bézier** , excepto en que solo tiene tres puntos táctiles. Este es el `PaintSurface` controlador del archivo de código subyacente [**QuadraticCurve.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/QuadraticCurvePage.xaml.cs) :

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

[![Captura de pantalla triple de la página curva cuadrática](beziers-images/quadraticcurve-small.png)](beziers-images/quadraticcurve-large.png#lightbox)

Las líneas de puntos son tangentes a la curva en el punto inicial y final, y se encuentran en el punto de control.

La trayectoria Bézier cuadrática es buena si necesita una curva de una forma general, pero prefiere la comodidad de un solo punto de control en lugar de dos. La trayectoria Bézier cuadrática se representa de forma más eficaz que cualquier otra curva, razón por la que se usa internamente en Skia para representar los arcos elípticos.

Sin embargo, la forma de una curva cuadrática de Bézier no es elíptica, por lo que es necesario que se requieran varias Bézier cuadráticas para aproximar un arco elíptico. La trayectoria Bézier cuadrática es un segmento de una parabola.

## <a name="the-conic-bzier-curve"></a>Curva cónica de Bézier

La curva cónica &mdash; de Bézier también conocida como la curva de Bézier cuadrática racional &mdash; es una adición relativamente reciente a la familia de curvas de Bézier. Al igual que la curva cuadrática de Bézier, la curva de Bézier cuadrática racional incluye un punto inicial, un punto final y un punto de control. Pero la curva de Bézier cuadrática cuadrático también requiere un valor de *peso* . Se denomina una cuadrático *racional* porque las fórmulas paramétricas implican proporciones.

Las ecuaciones paramétricas para X e y son proporciones que comparten el mismo denominador. Esta es la ecuación del denominador para *t* que va de 0 a 1 y un valor de peso de *w*:

d (t) = (1 – t) ² + 2wt (1 – t) + t ²

En teoría, un cuadrático racional puede incluir tres valores de peso independientes, uno para cada uno de los tres términos, pero estos se pueden simplificar a un solo valor de peso en el plazo medio.

Las ecuaciones paramétricas de las coordenadas X e y son similares a las ecuaciones paramétricas de la Bézier cuadrática, salvo que el término intermedio también incluye el valor de la ponderación y la expresión se divide por el denominador:

x (t) = ((1 – t) ² x ₀ + 2wt (1 – t) x ₁ + t ² x ₂)) ÷ d (t)

y (t) = ((1 – t) ² y ₀ + 2wt (1 – t) y ₁ + t ² y ₂)) ÷ d (t)

Las curvas Bézier cuadráticas racional también se denominan *cónicas* , ya que pueden representar exactamente los segmentos de cualquier sección cónica &mdash; hyperbolas, PARABOLAS, elipses y círculos.

Para agregar una curva racional cuadrática de Bézier a un trazado, use el [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) método o la [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo(System.Single,System.Single,System.Single,System.Single,System.Single)) sobrecarga con `x` coordenadas y independientes `y` :

```csharp
public void ConicTo (SKPoint point1, SKPoint point2, Single weight)

public void ConicTo (Single x1, Single y1, Single x2, Single y2, Single weight)
```

Observe el `weight` parámetro final.

La página **curva cónica** le permite experimentar con estas curvas. La clase `ConicCurvePage` deriva de `InteractivePage`. El archivo [**ConicCurvePage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml) crea una instancia de `Slider` para seleccionar un valor de peso entre – 2 y 2. El archivo de código subyacente [**ConicCurvePage.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml.cs) crea tres `TouchPoint` objetos y el `PaintSurface` controlador simplemente representa la curva resultante con las líneas tangentes en los puntos de control:

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

Aquí se ejecuta:

[![Captura de pantalla triple de la página curva cónica](beziers-images/coniccurve-small.png)](beziers-images/coniccurve-large.png#lightbox)

Como puede ver, el punto de control parece extraer la curva más cuando el peso es mayor. Cuando el peso es cero, la curva se convierte en una línea recta desde el punto inicial hasta el punto final.

En teoría, se permiten pesos negativos y hacen que la curva se pliegue *fuera* del punto de control. Sin embargo, los pesos de – 1 o inferior hacen que el denominador de las ecuaciones paramétricas sea negativo para valores concretos de *t*. Probablemente por este motivo, los pesos negativos se omiten en los `ConicTo` métodos. El programa de **curva cónica** le permite establecer pesos negativos, pero como puede ver mediante experimentos, los pesos negativos tienen el mismo efecto que una ponderación de cero y hacen que se represente una línea recta.

Es muy fácil derivar el punto de control y el peso para usar el `ConicTo` método para dibujar un arco circular hasta (pero sin incluir) un semicírculo. En el diagrama siguiente, las líneas tangentes de los puntos inicial y final se encuentran en el punto de control.

![Representación de arco cónico de un arco circular](beziers-images/conicarc.png)

Puede usar trigonometría para determinar la distancia del punto de control del centro del círculo: es el radio del círculo dividido entre el coseno de la mitad del ángulo α. Para dibujar un arco circular entre los puntos inicial y final, establezca el peso en el mismo coseno de la mitad del ángulo. Observe que si el ángulo es de 180 grados, las líneas tangentes nunca se cumplen y el peso es cero. Pero para los ángulos inferiores a 180 grados, la matemática funciona correctamente.

En la página **arco circular cónico** se muestra esto. El archivo [**ConicCircularArc. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml) crea una instancia `Slider` de para seleccionar el ángulo. El `PaintSurface` controlador del archivo de código subyacente [**ConicCircularArc.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml.cs) calcula el punto de control y el peso:

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

Como puede ver, no hay ninguna diferencia visual entre la `ConicTo` ruta de acceso que aparece en rojo y el círculo subyacente que se muestra como referencia:

[![Captura de pantalla triple de la página de arco circular cónico](beziers-images/coniccirculararc-small.png)](beziers-images/coniccirculararc-large.png#lightbox)

Pero establezca el ángulo en 180 grados y se producirá un error en las matemáticas.

En este caso, es lamentable que no `ConicTo` admita pesos negativos, porque en teoría (según las ecuaciones paramétricas), el círculo se puede completar con otra llamada a `ConicTo` con los mismos puntos, pero con un valor negativo de la ponderación. Esto permitiría crear un círculo completo con solo dos `ConicTo` curvas basadas en cualquier ángulo entre (pero sin incluir) cero grados y 180 grados.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
