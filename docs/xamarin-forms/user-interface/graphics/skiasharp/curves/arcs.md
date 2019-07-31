---
title: Tres maneras de dibujar un arco
description: En este artículo se explica cómo usar SkiaSharp para definir los arcos de tres maneras diferentes y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: F1DA55E4-0182-4388-863C-5C340213BF3C
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2017
ms.openlocfilehash: 97c168460b091b9ada954cacd895a670c31805b2
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655184"
---
# <a name="three-ways-to-draw-an-arc"></a>Tres maneras de dibujar un arco

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Aprenda a usar SkiaSharp para definir los arcos de tres maneras diferentes_

Un arco es una curva de la circunferencia de una elipse, por ejemplo, las partes de este inicio de sesión infinito redondeadas:

![](arcs-images/arcsample.png "Inicio de sesión de infinito")

A pesar de la sencillez de esa definición, no hay ninguna manera de definir una función de dibujo del arco que cumple todas las necesidades, y por lo tanto, no hay consenso entre los sistemas de gráficos de la mejor manera de dibujar un arco. Por este motivo, la `SKPath` clase no se restringe a solo un enfoque.

`SKPath` define un [ `AddArc` ](xref:SkiaSharp.SKPath.AddArc*) método, cinco diferentes [ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo*) métodos y relativo de dos [ `RArcTo` ](xref:SkiaSharp.SKPath.RArcTo*) métodos. Estos métodos se dividen en tres categorías, que representa tres enfoques muy diferentes al especificar un arco. Que utilice depende de la información disponible para definir el arco y cómo este arco se ajusta con los otros gráficos que se está dibujando.

## <a name="the-angle-arc"></a>El ángulo de arco

El enfoque de arco de ángulo al dibujo de arcos requiere que especifique un rectángulo que delimita una elipse. El arco de la circunferencia de esta elipse se indica mediante ángulos desde el centro de la elipse que indican el principio del arco y su longitud. Dos métodos diferentes dibujan arcos de ángulo. Estos son los [ `AddArc` ](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single)) método y el [ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKRect,System.Single,System.Single,System.Boolean)) método:

```csharp
public void AddArc (SKRect oval, Single startAngle, Single sweepAngle)

public void ArcTo (SKRect oval, Single startAngle, Single sweepAngle, Boolean forceMoveTo)
```

Estos métodos son idénticos a los [`AddArc`](xref:Android.Graphics.Path.AddArc*) métodos Android`ArcTo`y [] XREF: Android. Graphics. Path. ArcTo *). IOS [ `AddArc` ](xref:CoreGraphics.CGPath.AddArc(System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.Boolean)) método es similar pero está restringido a arcos de la circunferencia de un círculo en lugar de generalizarse para una elipse.

Ambos métodos comienzan por un `SKRect` valor que define la ubicación y el tamaño de una elipse:

![](arcs-images/anglearcoval.png "La elipse que comienza un arco de ángulo")

El arco es una parte de la circunferencia de esta elipse.

El `startAngle` argumento es un ángulo en grados con respecto a una línea horizontal que se dibuja desde el centro de la elipse a la derecha hacia la derecha. El `sweepAngle` argumento es relativa a la `startAngle`. Estos son `startAngle` y `sweepAngle` valores de 60 grados y 100 grados, respectivamente:

![](arcs-images/anglearcangles.png "Los ángulos que definen un arco de ángulo")

El ángulo inicial que comienza el arco. Su longitud se rige por el ángulo de barrido. Aquí se muestra el arco en rojo:

![](arcs-images/anglearchighlight.png "El arco ángulo resaltado")

La curva que se agrega a la ruta de acceso con el `AddArc` o `ArcTo` método es simplemente esa parte de la circunferencia de la elipse:

![](arcs-images/anglearc.png "El arco ángulo por sí mismo")

Los `startAngle` argumentos `sweepAngle` o pueden ser negativos: El arco está en el sentido de las `sweepAngle` agujas del reloj para los valores positivos y en el sentido contrario a las agujas del reloj.

Sin embargo, `AddArc` does *no* definir un contorno cerrado. Si se llama a `LineTo` después `AddArc`, se dibuja una línea desde el final del arco hasta el punto en el `LineTo` método y el mismo puede decirse `ArcTo`.

`AddArc` inicia un nuevo contorno automáticamente y es funcionalmente equivalente a una llamada a `ArcTo` con el argumento final de `true`:

```csharp
path.ArcTo (oval, startAngle, sweepAngle, true);
```

Que se llama al último argumento `forceMoveTo`, y esto provoca eficazmente que un `MoveTo` llamar al principio del arco. Inicia un nuevo contorno. Ese no es así con un último argumento de `false`:

```csharp
path.ArcTo (oval, startAngle, sweepAngle, false);
```

Esta versión de `ArcTo` dibuja una línea desde la posición actual hasta el principio del arco. Esto significa que el arco pueden en algún lugar en medio de un perfil de mayor tamaño.

El **ángulo arco** página le permite usar dos controles deslizantes para especificar el inicio y el ángulo de barrido. El archivo XAML crea una instancia de dos `Slider` elementos y un `SKCanvasView`. El `PaintCanvas` controlador en el [ **AngleArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AngleArcPage.xaml.cs) archivo dibuja el óvalo y el arco utilizando dos `SKPaint` objetos definidos como campos:

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

Como puede ver, el ángulo inicial y ángulo de barrido pueden tener los valores negativos:

[![](arcs-images/anglearc-small.png "Captura de pantalla de la página de arco de ángulo triple")](arcs-images/anglearc-large.png#lightbox "Triple captura de pantalla de la página de arco de ángulo")

Este enfoque para generar un arco es forma algorítmica el más sencillo, y es fácil derivar las ecuaciones paramétricas que describen el arco. Conocer el tamaño y ubicación de la elipse y los ángulos de inicio y el barrido, los puntos inicial y final del arco se puede calcular mediante trigonometría simple:

`x = oval.MidX + (oval.Width / 2) * cos(angle)`

`y = oval.MidY + (oval.Height / 2) * sin(angle)`

El `angle` valor sea `startAngle` o `startAngle + sweepAngle`.

El uso de los dos ángulos para definir un arco es mejor para los casos donde se conoce la longitud del arco que desea que se va a dibujar, por ejemplo, para crear un gráfico circular angular. El **un gráfico circular seccionado** página muestra esto. El [ `ExplodedPieChartPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ExplodedPieChartPage.cs) clase utiliza una clase interna para definir algunos datos fabricadas y colores:

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

El `PaintSurface` controlador primero recorre en bucle los elementos que se va a calcular un `totalValues` número. De eso, se puede determinar el tamaño de cada elemento como la fracción del total y convertirlo en un ángulo:

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

Un nuevo `SKPath` objeto se crea para cada sector del gráfico circular. La ruta de acceso consta de una línea desde el centro, una `ArcTo` para dibujar el arco y otra línea volver a los resultados del centro de la `Close` llamar. Este programa muestra gráficos circulares "seccionado" moviéndolos todo desde el centro por 50 píxeles. Esa tarea, requiere un vector en la dirección del punto central del ángulo de barrido para cada segmento:

[![](arcs-images/explodedpiechart-small.png "Captura de pantalla de la página de un gráfico circular seccionado triple")](arcs-images/explodedpiechart-large.png#lightbox "Triple captura de pantalla de la página de un gráfico circular seccionado")

Para ver su aspecto sin la "expansión", simplemente marque como comentario el `Translate` llamar:

[![](arcs-images/explodedpiechartunexploded-small.png "Captura de pantalla de la página de un gráfico circular seccionado sin la explosión de triple")](arcs-images/explodedpiechartunexploded-large.png#lightbox "Triple captura de pantalla de la página de un gráfico circular seccionado sin la explosión")

## <a name="the-tangent-arc"></a>El arco tangente

El segundo tipo de arc admitida `SKPath` es el *arco tangente*, se denomina así porque el arco es la circunferencia de un círculo tangente a dos líneas conectadas.

Se agrega un arco tangente a una ruta de acceso con una llamada a la [ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) método con dos `SKPoint` parámetros, o el [ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,System.Single,System.Single)) sobrecarga con independiente `Single` parámetros para el puntos:

```csharp
public void ArcTo (SKPoint point1, SKPoint point2, Single radius)

public void ArcTo (Single x1, Single y1, Single x2, Single y2, Single radius)
```

Esto `ArcTo` método es similar a la PostScript [ `arct` ](https://www.adobe.com/products/postscript/pdfs/PLRM.pdf) función (página 532) y el iOS [ `AddArcToPoint` ](xref:CoreGraphics.CGPath.AddArcToPoint(System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.nfloat)) método.

El `ArcTo` método implica tres puntos:

- Punto actual de contorno o el punto (0, 0) si `MoveTo` no se ha llamado.
- El primer argumento de punto para la `ArcTo` método, denominado el *esquina punto*
- El segundo argumento de punto `ArcTo`, llamado el *punto de destino*:

![](arcs-images/tangentarcthreepoints.png "Tres puntos que comienzan un arco tangente")

Estos tres puntos definen dos líneas conectadas:

![](arcs-images/tangentarcconnectinglines.png "Líneas que conectan los tres puntos de un arco tangente")

Si los tres puntos son colineales &mdash; es decir, si encuentran en la misma línea recta &mdash; ningún arco se va a dibujar.

El `ArcTo` método también incluye un `radius` parámetro. Esto define el radio de un círculo:

![](arcs-images/tangentarccircle.png "El círculo de un arco tangente")

No se ha generalizado el arco tangente de una elipse.

Si se cumplen las dos líneas en cualquier ángulo, se puede insertar ese círculo entre esas líneas para que sea tangente a ambas líneas:

![](arcs-images/tangentarctangentcircle.png "El círculo de arco tangente entre las dos líneas")

No toca la curva que se agrega al contorno cualquiera de los puntos especificados en el `ArcTo` método. Está formado por una línea recta desde el punto actual hasta el primer punto tangente y un arco que termina en el segundo punto tangente, que se muestra en rojo:

![](arcs-images/tangentarchighlight.png "El arco tangente resaltado entre las dos líneas")

Aquí es la última línea recta y arco que se agrega al contorno:

![](arcs-images/tangentarc.png "El arco tangente resaltado entre las dos líneas")

El contorno se puede continuar desde el segundo punto tangente.

El **arco tangente** página le permite experimentar con el arco tangente. Este es el primero de varias páginas que se derivan de [ `InteractivePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/InteractivePage.cs), que define algunas útiles `SKPaint` objetos y realiza `TouchPoint` procesamiento:

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

La clase `TangentArcPage` se deriva de `InteractivePage`. El constructor en el [ **TangentArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml.cs) archivo es responsable de crear instancias e inicializar el `touchPoints` matriz y configuración `baseCanvasView` (en `InteractivePage`) a la `SKCanvasView` crea una instancia de objeto en el [ **TangentArcPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml) archivo:

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

El `PaintSurface` controlador utiliza la `ArcTo` método para dibujar el arco en función de los puntos de toque y un `Slider`, pero también de forma algorítmica calcula el círculo que se basa el ángulo en:

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

Este es el **arco tangente** página que se ejecuta:

[![](arcs-images/tangentarc-small.png "Captura de pantalla de la página de arco tangente triple")](arcs-images/tangentarc-large.png#lightbox "Triple captura de pantalla de la página de arco tangente")

El arco tangente es ideal para crear esquinas redondeadas, como un rectángulo redondeado. Dado que `SKPath` ya incluye un `AddRoundedRect` método, el **Heptágono redondea** página muestra cómo utilizar `ArcTo` para redondear las esquinas de un polígono a siete caras. (El código está generalizado para cualquier polígono regular).

El `PaintSurface` controlador de la [ `RoundedHeptagonPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RoundedHeptagonPage.cs) clase contiene un `for` bucle para calcular las coordenadas de los vértices de la Heptágono y otro para calcular los puntos intermedios de los lados de estas siete siete vértices. Estos puntos intermedios, a continuación, se usan para construir la ruta de acceso:

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

Este es el programa que se ejecuta:

[![](arcs-images/roundedheptagon-small.png "Captura de pantalla de la página Heptágono redondea triple")](arcs-images/roundedheptagon-large.png#lightbox "Triple captura de pantalla de la página Heptágono redondeado")

## <a name="the-elliptical-arc"></a>Arco elíptico

Arco elíptico se agrega a una ruta de acceso con una llamada a la [ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,SkiaSharp.SKPoint)) método que tiene dos `SKPoint` parámetros, o el [ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,System.Single,System.Single)) sobrecargar con independiente X e Y coordenadas:

```csharp
public void ArcTo (SKPoint r, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, SKPoint xy)

public void ArcTo (Single rx, Single ry, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, Single x, Single y)
```

Arco elíptico es coherente con la [arco elíptico](http://www.w3.org/TR/SVG11/paths.html#PathDataEllipticalArcCommands) incluido en Scalable Vector Graphics (SVG) y la plataforma Universal de Windows [ `ArcSegment` ](/uwp/api/Windows.UI.Xaml.Media.ArcSegment/) clase.

Estos `ArcTo` métodos dibujan un arco entre dos puntos, que es el punto actual del contorno, y el último parámetro a la `ArcTo` método (el `xy` parámetro o el separar `x` y `y` parámetros):

![](arcs-images/ellipticalarcpoints.png "Los dos puntos que definen un arco elíptico")

El primer parámetro de punto para la `ArcTo` método (`r`, o `rx` y `ry`) no es un punto de nada, pero en su lugar, especifica los radios horizontales y verticales de una elipse;

![](arcs-images/ellipticalarcellipse.png "La elipse que define un arco elíptico")

El `xAxisRotate` parámetro es el número de grados hacia la derecha que se va a girar esta elipse:

![](arcs-images/ellipticalarctiltedellipse.png "La elipse superpuesta que definen un arco elíptico")

Si, a continuación, se coloca esta elipse superpuesta para que toca los dos puntos, los puntos se conectan mediante dos arcos diferentes:

![](arcs-images/ellipticalarcellipse1.png "El primer conjunto de arcos elípticos")

Estos dos arcos se pueden distinguir de dos maneras: El arco superior es mayor que el arco inferior y, a medida que el arco se dibuja de izquierda a derecha, el arco superior se dibuja en sentido de las agujas del reloj mientras el arco inferior se dibuja en una dirección en sentido contrario a las agujas del reloj.

También es posible ajustar la elipse entre los dos puntos de otro modo:

![](arcs-images/ellipticalarcellipse2.png "El segundo conjunto de arcos elípticos")

Ahora hay un arco más pequeño en la parte superior, que se dibuja a la derecha y en la parte inferior que se dibuja un arco de mayor a la izquierda.

Estos dos puntos, por tanto, se pueden conectar mediante un arco definido por la elipse superpuesta en un total de cuatro maneras:

![](arcs-images/ellipticalarccolors.png "Todos los cuatro arcos elípticos")

Estos cuatro arcos se distinguen por las cuatro combinaciones de la [ `SKPathArcSize` ](xref:SkiaSharp.SKPathArcSize) y [ `SKPathDirection` ](xref:SkiaSharp.SKPathDirection) argumentos de tipo de enumeración para el `ArcTo` método:

- alerta SKPathArcSize. Large y SKPathDirection. en el sentido de las agujas del reloj
- verde SKPathArcSize. Small y SKPathDirection. en el sentido de las agujas del reloj
- azul SKPathArcSize. Small y SKPathDirection. en sentido contrario a las agujas del reloj
- magenta: SKPathArcSize. Large y SKPathDirection. en sentido contrario a las agujas del reloj

Si la elipse superpuesta no es suficientemente grande como para ajustarla entre los dos puntos, se escala uniformemente hasta que sea lo suficientemente grande. Solo dos arcos únicos conectan los dos puntos en ese caso. Estos se pueden distinguir con el `SKPathDirection` parámetro.

Aunque este enfoque para definir un arco parece complejo en la primera vez, es el único enfoque que permite definir un arco con una elipse gira y, a menudo es el enfoque más sencillo cuando necesite integrar arcos con otras partes del contorno.

El **arco elíptico** página permite establecer de forma interactiva los dos puntos y el tamaño y la rotación de la elipse. El `EllipticalArcPage` clase se deriva de `InteractivePage`y el `PaintSurface` controlador en el [ **EllipticalArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/EllipticalArcPage.xaml.cs) archivo de código subyacente dibuja los cuatro arcos:

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

Aquí se está ejecutando:

[![](arcs-images/ellipticalarc-small.png "Captura de pantalla de la página de arco elíptico triple")](arcs-images/ellipticalarc-large.png#lightbox "Triple captura de pantalla de la página de arco elíptico")

El **arco infinito** página arco elíptico utiliza para dibujar un signo de infinito. El inicio de sesión infinito se basa en dos círculos con radios de separados por 100 unidades de 100 unidades:

![](arcs-images/infinitycircles.png "Dos círculos")

Dos líneas por las fronteras entre sí son tangente a ambos círculos:

![](arcs-images/infinitycircleslines.png "Dos círculos con líneas tangentes")

El inicio de sesión infinito es una combinación de partes de esos círculos y las dos líneas. Para usar el arco elíptico para dibujar el inicio de sesión infinito, deben determinar las coordenadas donde las dos líneas son tangente a los círculos.

Crear un rectángulo en uno de los círculos de la derecha:

![](arcs-images/infinitytriangle.png "Dos círculos con líneas de tangente y círculo incrustado")

El radio del círculo es de 100 unidades y la hipotenusa del triángulo es 150 unidades, por lo que el ángulo α es el arco seno (seno inverso) de 100 dividido por 150 o 41.8 grados. La longitud del otro lado del triángulo es 150 el coseno de grados 41.8 o 112, que también se puede calcular mediante el teorema Pitagórica.

A continuación, se pueden calcular las coordenadas del punto tangente con esta información:

`x = 112·cos(41.8) = 83`

`y = 112·sin(41.8) = 75`

Los cuatro puntos de tangencia son todo lo que es necesario establecer una sesión de infinito centrado en el punto (0, 0) de radios de círculo de 100:

![](arcs-images/infinitycoordinates.png "Dos círculos con líneas de tangente y coordenadas")

El `PaintSurface` controlador en el [ `ArcInfinityPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ArcInfinityPage.cs) clase coloca el signo de infinito para que el (0, 0) punto se coloca en el centro de la página y escala de la ruta de acceso para el tamaño de pantalla:

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

El código usa el `Bounds` propiedad de `SKPath` para determinar las dimensiones del seno para ajustar la escala al tamaño del lienzo infinito:

[![](arcs-images/arcinfinity-small.png "Captura de pantalla de la página de arco infinito triple")](arcs-images/arcinfinity-large.png#lightbox "Triple captura de pantalla de la página de arco infinito")

El resultado parece algo pequeño, lo que sugiere que el `Bounds` propiedad de `SKPath` está informando de un tamaño mayor que la ruta de acceso.

Internamente, Skia aproxima el arco mediante varias curvas Bézier cuadráticas. Estas curvas (como verá en la siguiente sección) contienen puntos de control que rigen cómo se dibuja la curva, pero no forman parte de la curva representada. El `Bounds` propiedad incluye los puntos de control.

Para obtener una mejor elección, use el `TightBounds` propiedad, que excluye los puntos de control. Este es el programa que se ejecuta en modo horizontal y el uso del `TightBounds` propiedad para obtener los límites de ruta de acceso:

[![](arcs-images/arcinfinitytightbounds-small.png "Captura de pantalla de la página de arco infinito con límites estrecha triple")](arcs-images/arcinfinitytightbounds-large.png#lightbox "Triple captura de pantalla de la página de arco infinito con límites estrecha")

Aunque las conexiones entre los arcos y líneas rectas son matemáticamente suaves, el cambio de arco recta puede parecer un poco brusco. Se presenta un mejor signo infinito en el siguiente artículo en [ **tipos tres de curvas de Bézier**](beziers.md).

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
