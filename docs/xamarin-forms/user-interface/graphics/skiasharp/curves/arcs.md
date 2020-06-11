---
title: "tres formas de dibujar un arco" Descripción: "en este artículo se explica cómo usar SkiaSharp para definir arcos de tres maneras diferentes y se muestra el código de ejemplo".
MS. Prod: Xamarin ms. Technology: Xamarin-skiasharp ms. AssetID: F1DA55E4-0182-4388-863C-5C340213BF3C Author: davidbritch ms. Author: dabritch ms. Date: 05/10/2017 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="three-ways-to-draw-an-arc"></a>Tres maneras de dibujar un arco

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Aprenda a usar SkiaSharp para definir arcos de tres maneras diferentes_

Un arco es una curva en la circunferencia de una elipse, como las partes redondeadas de este signo de infinito:

![Signo de infinito](arcs-images/arcsample.png)

A pesar de la simplicidad de esa definición, no hay forma de definir una función de dibujo de arco que satisfaga cada necesidad y, por lo tanto, no hay ningún consenso entre los sistemas de gráficos de la mejor forma de dibujar un arco. Por esta razón, la `SKPath` clase no se limita a un solo enfoque.

`SKPath`define un [`AddArc`](xref:SkiaSharp.SKPath.AddArc*) método, cinco [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*) métodos diferentes y dos métodos relativos [`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*) . Estos métodos se dividen en tres categorías, que representan tres enfoques muy diferentes para especificar un arco. La que use dependerá de la información disponible para definir el arco y de cómo quepa este arco con los demás gráficos que dibuje.

## <a name="the-angle-arc"></a>Arco angular

El enfoque de arco en ángulo para dibujar arcos requiere que se especifique un rectángulo que delimita una elipse. El arco en la circunferencia de esta elipse se indica mediante ángulos desde el centro de la elipse que indica el principio del arco y su longitud. Dos métodos diferentes dibujan arcos angulares. Estos son el [`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single)) método y el [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKRect,System.Single,System.Single,System.Boolean)) método:

```csharp
public void AddArc (SKRect oval, Single startAngle, Single sweepAngle)

public void ArcTo (SKRect oval, Single startAngle, Single sweepAngle, Boolean forceMoveTo)
```

Estos métodos son idénticos a los [`AddArc`](xref:Android.Graphics.Path.AddArc*) métodos Android y [ `ArcTo` ] XREF: Android. Graphics. Path. ArcTo *). El [`AddArc`](xref:CoreGraphics.CGPath.AddArc(System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.Boolean)) método iOS es similar, pero está restringido a los arcos de la circunferencia de un círculo en lugar de generalizado a una elipse.

Ambos métodos comienzan con un `SKRect` valor que define la ubicación y el tamaño de una elipse:

![Elipse que comienza con un arco angular](arcs-images/anglearcoval.png)

El arco es una parte de la circunferencia de esta elipse.

El `startAngle` argumento es un ángulo en el sentido de las agujas del reloj en grados respecto a una línea horizontal dibujada desde el centro de la elipse hacia la derecha. El `sweepAngle` argumento es relativo a `startAngle` . Estos son `startAngle` `sweepAngle` los valores y de 60 grados y 100 grados, respectivamente:

![Ángulos que definen un arco angular](arcs-images/anglearcangles.png)

El arco comienza en el ángulo inicial. Su longitud se rige por el ángulo de barrido. El arco se muestra aquí en rojo:

![Arco de ángulo resaltado](arcs-images/anglearchighlight.png)

La curva agregada a la ruta de acceso con el `AddArc` `ArcTo` método o es simplemente esa parte de la circunferencia de la elipse:

![Arco angular por sí mismo](arcs-images/anglearc.png)

Los `startAngle` `sweepAngle` argumentos o pueden ser negativos: el arco está en el sentido de las agujas del reloj para los valores positivos `sweepAngle` y en el sentido contrario a las agujas del reloj para los valores negativos.

Sin embargo `AddArc` , *no* define un contorno cerrado. Si llama a `LineTo` después de `AddArc` , se dibuja una línea desde el final del arco hasta el punto en el `LineTo` método, y el mismo es true `ArcTo` .

`AddArc`inicia automáticamente un nuevo perfil y es funcionalmente equivalente a una llamada a `ArcTo` con un argumento final de `true` :

```csharp
path.ArcTo (oval, startAngle, sweepAngle, true);
```

Se llama al último argumento `forceMoveTo` y, de hecho, se produce una `MoveTo` llamada al principio del arco. Que comienza un nuevo perfil. No es el caso con un último argumento de `false` :

```csharp
path.ArcTo (oval, startAngle, sweepAngle, false);
```

Esta versión de `ArcTo` dibuja una línea desde la posición actual hasta el principio del arco. Esto significa que el arco puede estar en algún lugar en medio de un contorno mayor.

La página de **arco de ángulo** permite usar dos controles deslizantes para especificar los ángulos de inicio y de barrido. El archivo XAML crea una instancia de dos `Slider` elementos y un `SKCanvasView` . El `PaintCanvas` controlador del archivo [**AngleArcPage.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AngleArcPage.xaml.cs) dibuja el óvalo y el arco con dos `SKPaint` objetos definidos como campos:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKRect rect = new SKRect(100, 100, info.Width - 100, info.Height - 100);
    float startAngle = (float)startAngleSlider.Value;
    float sweepAngle = (float)sweepAngleSlider.Value;

    canvas.DrawOval(rect, outlinePaint);

    using (SKPath path = new SKPath())
    {
        path.AddArc(rect, startAngle, sweepAngle);
        canvas.DrawPath(path, arcPaint);
    }
}
```

Como puede ver, el ángulo inicial y el ángulo de barrido pueden tomar valores negativos:

[![Captura de pantalla triple de la página de arco de ángulo](arcs-images/anglearc-small.png)](arcs-images/anglearc-large.png#lightbox)

Este enfoque para generar un arco es algorítmicamente el más sencillo y es fácil derivar las ecuaciones paramétricas que describen el arco. Conociendo el tamaño y la ubicación de la elipse, y los ángulos de inicio y de barrido, los puntos inicial y final del arco se pueden calcular mediante trigonometría simples:

`x = oval.MidX + (oval.Width / 2) * cos(angle)`

`y = oval.MidY + (oval.Height / 2) * sin(angle)`

El `angle` valor es `startAngle` o `startAngle + sweepAngle` .

El uso de dos ángulos para definir un arco es el mejor para los casos en los que se conoce la longitud angular del arco que se desea dibujar, por ejemplo, para crear un gráfico circular. En la página **gráfico circular Seccionado** se muestra esto. La [`ExplodedPieChartPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ExplodedPieChartPage.cs) clase utiliza una clase interna para definir algunos datos y colores fabricados:

```csharp
class ChartData
{
    public ChartData(int value, SKColor color)
    {
        Value = value;
        Color = color;
    }

    public int Value { private set; get; }

    public SKColor Color { private set; get; }
}

ChartData[] chartData =
{
    new ChartData(45, SKColors.Red),
    new ChartData(13, SKColors.Green),
    new ChartData(27, SKColors.Blue),
    new ChartData(19, SKColors.Magenta),
    new ChartData(40, SKColors.Cyan),
    new ChartData(22, SKColors.Brown),
    new ChartData(29, SKColors.Gray)
};

```

El `PaintSurface` controlador primero recorre en iteración los elementos para calcular un `totalValues` número. A partir de eso, puede determinar el tamaño de cada elemento como la fracción del total y convertirlo en un ángulo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int totalValues = 0;

    foreach (ChartData item in chartData)
    {
        totalValues += item.Value;
    }

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float explodeOffset = 50;
    float radius = Math.Min(info.Width / 2, info.Height / 2) - 2 * explodeOffset;
    SKRect rect = new SKRect(center.X - radius, center.Y - radius,
                             center.X + radius, center.Y + radius);

    float startAngle = 0;

    foreach (ChartData item in chartData)
    {
        float sweepAngle = 360f * item.Value / totalValues;

        using (SKPath path = new SKPath())
        using (SKPaint fillPaint = new SKPaint())
        using (SKPaint outlinePaint = new SKPaint())
        {
            path.MoveTo(center);
            path.ArcTo(rect, startAngle, sweepAngle, false);
            path.Close();

            fillPaint.Style = SKPaintStyle.Fill;
            fillPaint.Color = item.Color;

            outlinePaint.Style = SKPaintStyle.Stroke;
            outlinePaint.StrokeWidth = 5;
            outlinePaint.Color = SKColors.Black;

            // Calculate "explode" transform
            float angle = startAngle + 0.5f * sweepAngle;
            float x = explodeOffset * (float)Math.Cos(Math.PI * angle / 180);
            float y = explodeOffset * (float)Math.Sin(Math.PI * angle / 180);

            canvas.Save();
            canvas.Translate(x, y);

            // Fill and stroke the path
            canvas.DrawPath(path, fillPaint);
            canvas.DrawPath(path, outlinePaint);
            canvas.Restore();
        }

        startAngle += sweepAngle;
    }
}
```

`SKPath`Se crea un nuevo objeto para cada sector del gráfico circular. La ruta de acceso consta de una línea desde el centro, a continuación un `ArcTo` para dibujar el arco y otra línea de vuelta a los resultados del centro de la `Close` llamada. Este programa muestra los segmentos del gráfico circular "seccionados" moviéndolos del centro por 50 píxeles. Esa tarea requiere un vector en la dirección del punto medio del ángulo de barrido de cada segmento:

[![Captura de pantalla triple de la página del gráfico circular Seccionado](arcs-images/explodedpiechart-small.png)](arcs-images/explodedpiechart-large.png#lightbox)

Para ver su aspecto sin la "explosión", simplemente comente la `Translate` llamada:

[![Captura de pantalla triple de la página del gráfico circular Seccionado sin la explosión](arcs-images/explodedpiechartunexploded-small.png)](arcs-images/explodedpiechartunexploded-large.png#lightbox)

## <a name="the-tangent-arc"></a>Arco tangente

El segundo tipo de arco compatible con `SKPath` es el *arco tangente*, por lo que se llama a porque el arco es la circunferencia de un círculo que es tangente a dos líneas conectadas.

Se agrega un arco tangente a un trazado con una llamada al [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) método con dos `SKPoint` parámetros, o la [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,System.Single,System.Single)) sobrecarga con parámetros independientes `Single` para los puntos:

```csharp
public void ArcTo (SKPoint point1, SKPoint point2, Single radius)

public void ArcTo (Single x1, Single y1, Single x2, Single y2, Single radius)
```

Este `ArcTo` método es similar a la función PostScript [`arct`](https://www.adobe.com/products/postscript/pdfs/PLRM.pdf) (página 532) y al [`AddArcToPoint`](xref:CoreGraphics.CGPath.AddArcToPoint(System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.nfloat)) método iOS.

El `ArcTo` método implica tres puntos:

- El punto actual del contorno, o el punto (0,0) si no se `MoveTo` ha llamado a.
- El primer argumento de punto para el `ArcTo` método, denominado *punto de esquina*
- El segundo argumento de punto a `ArcTo` , denominado *punto de destino*:

![Tres puntos que comienzan un arco tangente](arcs-images/tangentarcthreepoints.png)

Estos tres puntos definen dos líneas conectadas:

![Líneas que conectan los tres puntos de un arco tangente](arcs-images/tangentarcconnectinglines.png)

Si los tres puntos son colineales &mdash; , es decir, si se encuentran en la misma línea recta, &mdash; no se dibujará ningún arco.

El `ArcTo` método también incluye un `radius` parámetro. Esto define el radio de un círculo:

![Círculo de un arco tangente](arcs-images/tangentarccircle.png)

El arco tangente no se generaliza para una elipse.

Si las dos líneas se unen en cualquier ángulo, se puede insertar ese círculo entre esas líneas para que sea tangente a ambas líneas:

![Círculo de arco tangente entre las dos líneas](arcs-images/tangentarctangentcircle.png)

La curva que se agrega al contorno no toca ninguno de los puntos especificados en el `ArcTo` método. Consta de una línea recta desde el punto actual hasta el primer punto tangente y un arco que termina en el segundo punto tangente, mostrado aquí en rojo:

![Arco de tangente resaltado entre las dos líneas](arcs-images/tangentarchighlight.png)

Esta es la línea recta final y el arco que se agregan al contorno:

![Arco de tangente resaltado entre las dos líneas](arcs-images/tangentarc.png)

El contorno puede continuar desde el segundo punto tangente.

La página de **arco tangente** le permite experimentar con el arco tangente. Esta es la primera de varias páginas que derivan de [`InteractivePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/InteractivePage.cs) , que define algunos `SKPaint` objetos útiles y realiza el `TouchPoint` procesamiento:

```csharp
public class InteractivePage : ContentPage
{
    protected SKCanvasView baseCanvasView;
    protected TouchPoint[] touchPoints;

    protected SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3
    };

    protected SKPaint redStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 15
    };

    protected SKPaint dottedStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] { 7, 7 }, 0)
    };

    protected void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        bool touchPointMoved = false;

        foreach (TouchPoint touchPoint in touchPoints)
        {
            float scale = baseCanvasView.CanvasSize.Width / (float)baseCanvasView.Width;
            SKPoint point = new SKPoint(scale * (float)args.Location.X,
                                        scale * (float)args.Location.Y);
            touchPointMoved |= touchPoint.ProcessTouchEvent(args.Id, args.Type, point);
        }

        if (touchPointMoved)
        {
            baseCanvasView.InvalidateSurface();
        }
    }
}
```

La clase `TangentArcPage` deriva de `InteractivePage`. El constructor del archivo [**TangentArcPage.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml.cs) es responsable de crear instancias e inicializar la `touchPoints` matriz y de establecer `baseCanvasView` (en `InteractivePage` ) en el objeto al que se ha `SKCanvasView` creado una instancia en el archivo [**TangentArcPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml) :

```csharp
public partial class TangentArcPage : InteractivePage
{
    public TangentArcPage()
    {
        touchPoints = new TouchPoint[3];

        for (int i = 0; i < 3; i++)
        {
            TouchPoint touchPoint = new TouchPoint
            {
                Center = new SKPoint(i == 0 ? 100 : 500,
                                     i != 2 ? 100 : 500)
            };
            touchPoints[i] = touchPoint;
        }

        InitializeComponent();

        baseCanvasView = canvasView;
        radiusSlider.Value = 100;
    }

    void sliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

El `PaintSurface` controlador usa el `ArcTo` método para dibujar el arco en función de los puntos táctiles y `Slider` , pero también calcula de forma algorítmica el círculo en el que se basa el ángulo:

```csharp
public partial class TangentArcPage : InteractivePage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Draw the two lines that meet at an angle
        using (SKPath path = new SKPath())
        {
            path.MoveTo(touchPoints[0].Center);
            path.LineTo(touchPoints[1].Center);
            path.LineTo(touchPoints[2].Center);
            canvas.DrawPath(path, dottedStrokePaint);
        }

        // Draw the circle that the arc wraps around
        float radius = (float)radiusSlider.Value;

        SKPoint v1 = Normalize(touchPoints[0].Center - touchPoints[1].Center);
        SKPoint v2 = Normalize(touchPoints[2].Center - touchPoints[1].Center);

        double dotProduct = v1.X * v2.X + v1.Y * v2.Y;
        double angleBetween = Math.Acos(dotProduct);
        float hypotenuse = radius / (float)Math.Sin(angleBetween / 2);
        SKPoint vMid = Normalize(new SKPoint((v1.X + v2.X) / 2, (v1.Y + v2.Y) / 2));
        SKPoint center = new SKPoint(touchPoints[1].Center.X + vMid.X * hypotenuse,
                                     touchPoints[1].Center.Y + vMid.Y * hypotenuse);

        canvas.DrawCircle(center.X, center.Y, radius, this.strokePaint);

        // Draw the tangent arc
        using (SKPath path = new SKPath())
        {
            path.MoveTo(touchPoints[0].Center);
            path.ArcTo(touchPoints[1].Center, touchPoints[2].Center, radius);
            canvas.DrawPath(path, redStrokePaint);
        }

        foreach (TouchPoint touchPoint in touchPoints)
        {
            touchPoint.Paint(canvas);
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
}
```

Esta es la página de **arco tangente** en ejecución:

[![Captura de pantalla triple de la página de arco tangente](arcs-images/tangentarc-small.png)](arcs-images/tangentarc-large.png#lightbox)

El arco tangente es idóneo para crear esquinas redondeadas, como un rectángulo redondeado. Dado `SKPath` que ya incluye un `AddRoundedRect` método, la página **Heptagon redondeada** muestra cómo usar `ArcTo` para redondear las esquinas de un polígono de siete caras. (El código se generaliza para cualquier polígono normal).

El `PaintSurface` controlador de la [`RoundedHeptagonPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RoundedHeptagonPage.cs) clase contiene un `for` bucle para calcular las coordenadas de los siete vértices del heptagon y un segundo para calcular los puntos medio de los siete lados de estos vértices. Estos puntos medio se usan para construir la ruta de acceso:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float cornerRadius = 100;
    int numVertices = 7;
    float radius = 0.45f * Math.Min(info.Width, info.Height);

    SKPoint[] vertices = new SKPoint[numVertices];
    SKPoint[] midPoints = new SKPoint[numVertices];

    double vertexAngle = -0.5f * Math.PI;       // straight up

    // Coordinates of the vertices of the polygon
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        vertices[vertex] = new SKPoint(radius * (float)Math.Cos(vertexAngle),
                                       radius * (float)Math.Sin(vertexAngle));
        vertexAngle += 2 * Math.PI / numVertices;
    }

    // Coordinates of the midpoints of the sides connecting the vertices
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        int prevVertex = (vertex + numVertices - 1) % numVertices;
        midPoints[vertex] = new SKPoint((vertices[prevVertex].X + vertices[vertex].X) / 2,
                                        (vertices[prevVertex].Y + vertices[vertex].Y) / 2);
    }

    // Create the path
    using (SKPath path = new SKPath())
    {
        // Begin at the first midpoint
        path.MoveTo(midPoints[0]);

        for (int vertex = 0; vertex < numVertices; vertex++)
        {
            SKPoint nextMidPoint = midPoints[(vertex + 1) % numVertices];

            // Draws a line from the current point, and then the arc
            path.ArcTo(vertices[vertex], nextMidPoint, cornerRadius);

            // Connect the arc with the next midpoint
            path.LineTo(nextMidPoint);
        }
        path.Close();

        // Render the path in the center of the screen
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 10;

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.DrawPath(path, paint);
        }
    }
}

```

Esta es la ejecución del programa:

[![Captura de pantalla triple de la página Heptagon redondeada](arcs-images/roundedheptagon-small.png)](arcs-images/roundedheptagon-large.png#lightbox)

## <a name="the-elliptical-arc"></a>Arco elíptico

El arco elíptico se agrega a un trazado con una llamada al [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,SkiaSharp.SKPoint)) método que tiene dos `SKPoint` parámetros, o la [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,System.Single,System.Single)) sobrecarga con coordenadas X e y independientes:

```csharp
public void ArcTo (SKPoint r, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, SKPoint xy)

public void ArcTo (Single rx, Single ry, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, Single x, Single y)
```

El arco elíptico es coherente con el [arco elíptico](https://www.w3.org/TR/SVG11/paths.html#PathDataEllipticalArcCommands) incluido en Scalable Vector Graphics (SVG) y la [`ArcSegment`](/uwp/api/Windows.UI.Xaml.Media.ArcSegment/) clase plataforma universal de Windows.

Estos `ArcTo` métodos dibujan un arco entre dos puntos, que son el punto actual del contorno, y el último parámetro al `ArcTo` método (el `xy` parámetro o los `x` parámetros y independientes `y` ):

![Dos puntos que definieron un arco elíptico](arcs-images/ellipticalarcpoints.png)

El primer parámetro de punto para el `ArcTo` método ( `r` , o `rx` y `ry` ) no es un punto en absoluto, sino que especifica los radios horizontal y vertical de una elipse;

![Elipse que definió un arco elíptico.](arcs-images/ellipticalarcellipse.png)

El `xAxisRotate` parámetro es el número de grados en el sentido de las agujas del reloj para girar esta elipse:

![La elipse inclinada que definió un arco elíptico](arcs-images/ellipticalarctiltedellipse.png)

Si la elipse inclinada se coloca a continuación para que toque los dos puntos, los puntos están conectados mediante dos arcos diferentes:

![El primer conjunto de arcos elípticos](arcs-images/ellipticalarcellipse1.png)

Estos dos arcos se pueden distinguir de dos maneras: el arco superior es mayor que el arco inferior y, como el arco se dibuja de izquierda a derecha, el arco superior se dibuja en sentido de las agujas del reloj mientras el arco inferior se dibuja en una dirección en sentido contrario a las agujas del reloj.

También es posible ajustar la elipse entre los dos puntos de otra manera:

![Segundo conjunto de arcos elípticos](arcs-images/ellipticalarcellipse2.png)

Ahora hay un arco más pequeño en la parte superior que se dibuja en el sentido de las agujas del reloj y un arco mayor en la parte inferior dibujada en el sentido contrario a las agujas del reloj.

Por lo tanto, estos dos puntos se pueden conectar mediante un arco definido por la elipse inclinada en un total de cuatro maneras:

![Los cuatro arcos elípticos](arcs-images/ellipticalarccolors.png)

Estos cuatro arcos se distinguen por las cuatro combinaciones de [`SKPathArcSize`](xref:SkiaSharp.SKPathArcSize) los [`SKPathDirection`](xref:SkiaSharp.SKPathDirection) argumentos de tipo de enumeración y del `ArcTo` método:

- rojo: SKPathArcSize. Large y SKPathDirection. en el sentido de las agujas del reloj
- verde: SKPathArcSize. Small y SKPathDirection. en el sentido de las agujas del reloj
- Blue: SKPathArcSize. Small y SKPathDirection. en sentido contrario a las agujas del reloj
- magenta: SKPathArcSize. Large y SKPathDirection. en sentido contrario a las agujas del reloj

Si la elipse inclinada no es lo suficientemente grande como para caber entre los dos puntos, se escala uniformemente hasta que sea lo suficientemente grande. Solo dos arcos únicos conectan los dos puntos en ese caso. Pueden distinguirse con el `SKPathDirection` parámetro.

Aunque este enfoque a la hora de definir un arco es complejo al encontrarse por primera vez, es el único enfoque que permite definir un arco con una elipse girada y a menudo es el enfoque más sencillo cuando se necesita integrar los arcos con otras partes del contorno.

La página de **arco elíptico** permite establecer interactivamente los dos puntos y el tamaño y la rotación de la elipse. La `EllipticalArcPage` clase se deriva de `InteractivePage` y el `PaintSurface` controlador del archivo de código subyacente [**EllipticalArcPage.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/EllipticalArcPage.xaml.cs) dibuja los cuatro arcos:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        int colorIndex = 0;
        SKPoint ellipseSize = new SKPoint((float)xRadiusSlider.Value,
                                          (float)yRadiusSlider.Value);
        float rotation = (float)rotationSlider.Value;

        foreach (SKPathArcSize arcSize in Enum.GetValues(typeof(SKPathArcSize)))
            foreach (SKPathDirection direction in Enum.GetValues(typeof(SKPathDirection)))
            {
                path.MoveTo(touchPoints[0].Center);
                path.ArcTo(ellipseSize, rotation,
                           arcSize, direction,
                           touchPoints[1].Center);

                strokePaint.Color = colors[colorIndex++];
                canvas.DrawPath(path, strokePaint);
                path.Reset();
            }
    }

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}

```

Aquí se ejecuta:

[![Captura de pantalla triple de la página de arco elíptico](arcs-images/ellipticalarc-small.png)](arcs-images/ellipticalarc-large.png#lightbox)

La página de **infinidad de arco** utiliza el arco elíptico para dibujar un signo de infinito. El signo de infinito se basa en dos círculos con radios de 100 unidades separadas por 100 unidades:

![Dos círculos](arcs-images/infinitycircles.png)

Dos líneas que se crucen entre sí son tangentes a ambos círculos:

![Dos círculos con líneas tangentes](arcs-images/infinitycircleslines.png)

El signo de infinito es una combinación de partes de estos círculos y las dos líneas. Para usar el arco elíptico para dibujar el signo de infinito, deben determinarse las coordenadas en las que las dos líneas son tangentes a los círculos.

Construya un rectángulo derecho en uno de los círculos:

![Dos círculos con líneas tangentes y círculo incrustado](arcs-images/infinitytriangle.png)

El radio del círculo es 100 unidades y la hipotenusa del triángulo es de 150 unidades, por lo que el ángulo α es el arcoseno (seno inverso) de 100 dividido entre 150 o 41,8 grados. La longitud del otro lado del triángulo es de 150 veces el coseno de 41,8 grados o 112, que también se puede calcular mediante el teorema de pitagórica.

Las coordenadas del punto tangente se pueden calcular con esta información:

`x = 112·cos(41.8) = 83`

`y = 112·sin(41.8) = 75`

Los cuatro puntos tangentes son todo lo que se necesita para dibujar un signo de infinito centrado en el punto (0,0) con los radios de círculo de 100:

![Dos círculos con líneas tangentes y coordenadas](arcs-images/infinitycoordinates.png)

El `PaintSurface` controlador de la [`ArcInfinityPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ArcInfinityPage.cs) clase coloca el signo de infinito para que el punto (0,0) se coloque en el centro de la página y escale la ruta de acceso al tamaño de la pantalla:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        path.LineTo(83, 75);
        path.ArcTo(100, 100, 0, SKPathArcSize.Large, SKPathDirection.CounterClockwise, 83, -75);
        path.LineTo(-83, 75);
        path.ArcTo(100, 100, 0, SKPathArcSize.Large, SKPathDirection.Clockwise, -83, -75);
        path.Close();

        // Use path.TightBounds for coordinates without control points
        SKRect pathBounds = path.Bounds;

        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(Math.Min(info.Width / pathBounds.Width,
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

El código usa la `Bounds` propiedad de `SKPath` para determinar las dimensiones del seno del infinito para escalarlo al tamaño del lienzo:

[![Captura de pantalla triple de la página de infinidad de arco](arcs-images/arcinfinity-small.png)](arcs-images/arcinfinity-large.png#lightbox)

El resultado parece un poco pequeño, lo que sugiere que la `Bounds` propiedad de `SKPath` informa de un tamaño mayor que el de la ruta de acceso.

Internamente, Skia aproxima el arco con varias curvas Bézier cuadráticas. Estas curvas (como verá en la sección siguiente) contienen puntos de control que rigen cómo se dibuja la curva pero no forman parte de la curva representada. La `Bounds` propiedad incluye esos puntos de control.

Para obtener un ajuste más estrecho, use la `TightBounds` propiedad, que excluye los puntos de control. Este es el programa que se ejecuta en modo horizontal y el uso de la `TightBounds` propiedad para obtener los límites de la ruta de acceso:

[![Captura de pantalla triple de la página de infinidad de arco con límites estrechos](arcs-images/arcinfinitytightbounds-small.png)](arcs-images/arcinfinitytightbounds-large.png#lightbox)

Aunque las conexiones entre los arcos y las líneas rectas son matemáticamente suaves, el cambio de arco a línea recta podría parecer un poco brusco. En el siguiente artículo se presentan [**tres tipos de curvas de Bézier**](beziers.md).

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
