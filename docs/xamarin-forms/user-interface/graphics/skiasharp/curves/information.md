---
title: Enumeración e información de trazado
description: En este artículo se explica cómo obtener información acerca de las rutas de acceso de SkiaSharp y enumerar el contenido y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.assetid: 8E8C5C6A-F324-4155-8652-7A77D231B3E5
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2017
ms.openlocfilehash: 6f4f4e6253c14d86e2057f13d6232a07a83b4d26
ms.sourcegitcommit: ae5557c5024d4b7bd52b2f33cb96114ce2b8e086
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/06/2020
ms.locfileid: "77045080"
---
# <a name="path-information-and-enumeration"></a>Enumeración e información de trazado

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Obtener información sobre las rutas de acceso y enumerar el contenido_

La clase [`SKPath`](xref:SkiaSharp.SKPath) define varias propiedades y métodos que permiten obtener información sobre la ruta de acceso. Las propiedades [`Bounds`](xref:SkiaSharp.SKPath.Bounds) y [`TightBounds`](xref:SkiaSharp.SKPath.TightBounds) (y los métodos relacionados) obtienen las dimensiones métricas de un trazado. El método [`Contains`](xref:SkiaSharp.SKPath.Contains(System.Single,System.Single)) permite determinar si un punto determinado se encuentra dentro de una ruta de acceso.

A veces resulta útil para determinar la longitud total de todas las líneas y curvas que conforman una ruta de acceso. Calcular esta longitud no es una tarea muy sencilla, por lo que una clase completa denominada [`PathMeasure`](xref:SkiaSharp.SKPathMeasure) está dedicada a ella.

También a veces es útil obtener todas las operaciones y los puntos que componen una ruta de acceso de dibujo. En primer lugar, esta función puede parecer innecesaria: si su programa ha creado la ruta de acceso, el programa ya conoce el contenido. Sin embargo, ha visto que las rutas de acceso también pueden crearse mediante efectos de trazado y convirtiendo [cadenas de texto en rutas](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)de [acceso](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) . También puede obtener todas las operaciones y los puntos que componen estas rutas de acceso de dibujo. Una posibilidad es para aplicar una transformación algorítmica a todos los puntos, por ejemplo, para ajustar el texto en un hemisferio:

![](information-images/pathenumerationsample.png "Text wrapped on a hemisphere")

## <a name="getting-the-path-length"></a>Obtención de la longitud de ruta de acceso

En el artículo [**rutas de acceso y texto**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md) vio cómo usar el método [`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) para dibujar una cadena de texto cuya línea base sigue el curso de un trazado. Pero ¿qué ocurre si desea cambiar el tamaño del texto para que se ajuste con precisión la ruta de acceso? Dibujar texto en torno a un círculo es fácil porque es fácil de calcular la circunferencia de un círculo. Pero la circunferencia de una elipse o la longitud de una curva de Bézier no es tan sencilla.

La clase [`SKPathMeasure`](xref:SkiaSharp.SKPathMeasure) puede ayudar. El [constructor](xref:SkiaSharp.SKPathMeasure.%23ctor(SkiaSharp.SKPath,System.Boolean,System.Single)) acepta un argumento de `SKPath` y la propiedad [`Length`](xref:SkiaSharp.SKPathMeasure.Length) revela su longitud.

Esta clase se muestra en el ejemplo de longitud de la **ruta de acceso** , que se basa en la página **curva de Bézier** . El archivo [**PathLengthPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml) se deriva de `InteractivePage` e incluye una interfaz táctil:

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Curves.PathLengthPage"
                       Title="Path Length">
    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</local:InteractivePage>
```

El archivo de código subyacente [**PathLengthPage.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml.cs) permite desplace cuatro puntos táctiles para definir los puntos de control y los puntos de control de una curva Bézier cúbica. Tres campos definen una cadena de texto, un objeto de `SKPaint` y un ancho calculado del texto:

```csharp
public partial class PathLengthPage : InteractivePage
{
    const string text = "Compute length of path";

    static SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Black,
        TextSize = 10,
    };

    static readonly float baseTextWidth = textPaint.MeasureText(text);
    ...
}
```

El campo `baseTextWidth` es el ancho del texto basado en un valor de `TextSize` de 10.

El controlador de `PaintSurface` dibuja la curva de Bézier y, a continuación, ajusta el tamaño del texto para que quepa a lo largo de su longitud completa:

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

        // Get path length
        SKPathMeasure pathMeasure = new SKPathMeasure(path, false, 1);

        // Find new text size
        textPaint.TextSize = pathMeasure.Length / baseTextWidth * 10;

        // Draw text on path
        canvas.DrawTextOnPath(text, path, 0, 0, textPaint);
    }
    ...
}
```

La propiedad `Length` del objeto `SKPathMeasure` recién creado obtiene la longitud de la ruta de acceso. La longitud de la ruta de acceso se divide entre el valor `baseTextWidth` (que es el ancho del texto basado en un tamaño de texto de 10) y, a continuación, se multiplica por el tamaño de texto base de 10. El resultado es un nuevo tamaño de texto para mostrar el texto a lo largo de esa ruta de acceso:

[![](information-images/pathlength-small.png "Triple screenshot of the Path Length page")](information-images/pathlength-large.png#lightbox "Triple screenshot of the Path Length page")

A medida que la curva de Bézier mayores o menores, puede ver el tamaño del texto cambie.

## <a name="traversing-the-path"></a>Atravesar la ruta de acceso

`SKPathMeasure` puede hacer más que simplemente medir la longitud de la ruta de acceso. Para cualquier valor entre cero y la longitud de la ruta de acceso, un objeto `SKPathMeasure` puede obtener la posición en la ruta de acceso y la tangente a la curva de trazado en ese punto. La tangente está disponible como vector en forma de un objeto `SKPoint`, o como un giro encapsulado en un objeto `SKMatrix`. Estos son los métodos de `SKPathMeasure` que obtienen esta información de maneras diferentes y flexibles:

```csharp
Boolean GetPosition (Single distance, out SKPoint position)

Boolean GetTangent (Single distance, out SKPoint tangent)

Boolean GetPositionAndTangent (Single distance, out SKPoint position, out SKPoint tangent)

Boolean GetMatrix (Single distance, out SKMatrix matrix, SKPathMeasureMatrixFlags flag)
```

Los miembros de la enumeración [`SKPathMeasureMatrixFlags`](xref:SkiaSharp.SKPathMeasureMatrixFlags) son:

- `GetPosition`
- `GetTangent`
- `GetPositionAndTangent`

La página de la **media de UNICYCLE** anima una figura de palo en un UNICYCLE que parece moverse hacia delante y hacia atrás a lo largo de una curva Bézier cúbica:

[![](information-images/unicyclehalfpipe-small.png "Triple screenshot of the Unicycle Half-Pipe page")](information-images/unicyclehalfpipe-large.png#lightbox "Triple screenshot of the Unicycle Half-Pipe page")

El objeto `SKPaint` que se usa para contornear la media canalización y UNICYCLE se define como un campo en la clase `UnicycleHalfPipePage`. También se define el objeto de `SKPath` para UNICYCLE:

```csharp
public class UnicycleHalfPipePage : ContentPage
{
    ...
    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 3,
        Color = SKColors.Black
    };

    SKPath unicyclePath = SKPath.ParseSvgPathData(
        "M 0 0" +
        "A 25 25 0 0 0 0 -50" +
        "A 25 25 0 0 0 0 0 Z" +
        "M 0 -25 L 0 -100" +
        "A 15 15 0 0 0 0 -130" +
        "A 15 15 0 0 0 0 -100 Z" +
        "M -25 -85 L 25 -85");
    ...
}
```

La clase contiene las invalidaciones estándar de los métodos `OnAppearing` y `OnDisappearing` para la animación. El controlador de `PaintSurface` crea la ruta de acceso para la mitad de la canalización y, a continuación, la dibuja. A continuación, se crea un objeto `SKPathMeasure` basado en esta ruta de acceso:

```csharp
public class UnicycleHalfPipePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath pipePath = new SKPath())
        {
            pipePath.MoveTo(50, 50);
            pipePath.CubicTo(0, 1.25f * info.Height,
                             info.Width - 0, 1.25f * info.Height,
                             info.Width - 50, 50);

            canvas.DrawPath(pipePath, strokePaint);

            using (SKPathMeasure pathMeasure = new SKPathMeasure(pipePath))
            {
                float length = pathMeasure.Length;

                // Animate t from 0 to 1 every three seconds
                TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
                float t = (float)(timeSpan.TotalSeconds % 5 / 5);

                // t from 0 to 1 to 0 but slower at beginning and end
                t = (float)((1 - Math.Cos(t * 2 * Math.PI)) / 2);

                SKMatrix matrix;
                pathMeasure.GetMatrix(t * length, out matrix,
                                      SKPathMeasureMatrixFlags.GetPositionAndTangent);

                canvas.SetMatrix(matrix);
                canvas.DrawPath(unicyclePath, strokePaint);
            }
        }
    }
}
```

El controlador de `PaintSurface` calcula un valor de `t` que va de 0 a 1 cada cinco segundos. A continuación, utiliza la función `Math.Cos` para convertirla en un valor de `t` que va de 0 a 1 y de nuevo a 0, donde 0 corresponde a UNICYCLE al principio de la parte superior izquierda, mientras que 1 corresponde a UNICYCLE en la parte superior derecha. La función de coseno hace que la velocidad sea más lenta en la parte superior de la canalización y más rápido en la parte inferior.

Tenga en cuenta que este valor de `t` se debe multiplicar por la longitud de la ruta de acceso del primer argumento que se va a `GetMatrix`. A continuación, la matriz se aplica al `SKCanvas` objeto para dibujar la ruta de acceso UNICYCLE.

## <a name="enumerating-the-path"></a>Enumerar la ruta de acceso

Dos clases incrustadas de `SKPath` permiten enumerar el contenido de la ruta de acceso. Estas clases son [`SKPath.Iterator`](xref:SkiaSharp.SKPath.Iterator) y [`SKPath.RawIterator`](xref:SkiaSharp.SKPath.RawIterator). Las dos clases son muy similares, pero `SKPath.Iterator` pueden eliminar elementos de la ruta de acceso con una longitud cero o cercanos a una longitud cero. En el ejemplo siguiente se usa el `RawIterator`.

Puede obtener un objeto de tipo `SKPath.RawIterator` llamando al método [`CreateRawIterator`](xref:SkiaSharp.SKPath.CreateRawIterator) de `SKPath`. La enumeración a través de la ruta de acceso se realiza mediante una llamada repetida al método [`Next`](xref:SkiaSharp.SKPath.RawIterator.Next*) . Pásele una matriz de cuatro valores de `SKPoint`:

```csharp
SKPoint[] points = new SKPoint[4];
...
SKPathVerb pathVerb = rawIterator.Next(points);
```

El método `Next` devuelve un miembro del tipo de enumeración [`SKPathVerb`](xref:SkiaSharp.SKPathVerb) . Estos valores indican que el comando de dibujo particular en la ruta de acceso. El número de puntos válidos insertado en la matriz depende de este verbo:

- `Move` con un solo punto
- `Line` con dos puntos
- `Cubic` con cuatro puntos
- `Quad` con tres puntos
- `Conic` con tres puntos (y también llamar al método [`ConicWeight`](xref:SkiaSharp.SKPath.RawIterator.ConicWeight*) para el peso)
- `Close` con un punto
- `Done`

El verbo `Done` indica que la enumeración path está completa.

Observe que no hay ningún verbo `Arc`. Esto indica que todos los arcos se convierten en curvas de Bézier cuando se agrega a la ruta de acceso.

Parte de la información de la matriz `SKPoint` es redundante. Por ejemplo, si un verbo `Move` va seguido de un verbo `Line`, el primero de los dos puntos que acompañan a la `Line` es el mismo que el punto de `Move`. En la práctica, esta redundancia es muy útil. Cuando se obtiene un verbo de `Cubic`, está acompañado de los cuatro puntos que definen la curva Bézier cúbica. No tiene que conservar la posición actual establecida por el verbo anterior.

Sin embargo, el verbo problemático es `Close`. Este comando dibuja una línea recta desde la posición actual hasta el principio del contorno establecido anteriormente por el comando `Move`. Idealmente, el verbo `Close` debe proporcionar estos dos puntos en lugar de un solo punto. Lo peor es que el punto que acompaña al verbo `Close` es siempre (0,0). Al enumerar a través de una ruta de acceso, probablemente tendrá que conservar el punto de `Move` y la posición actual.

## <a name="enumerating-flattening-and-malforming"></a>Enumerar, acoplar y Malforming

A veces es deseable para aplicar un algoritmo transformar una ruta de acceso a malform de alguna manera:

![](information-images/pathenumerationsample.png "Text wrapped on a hemisphere")

La mayoría de estas letras se componen de líneas rectas, aunque aparentemente se han twisted estas líneas rectas en curvas. ¿Cómo es esto posible?

La clave es que las líneas rectas originales se dividen en una serie de líneas rectas más pequeñas. A continuación, se pueden manipular estas líneas rectas menor individuales de maneras diferentes para formar una curva.

Para ayudar con este proceso, el ejemplo [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) contiene una clase [`PathExtensions`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathExtensions.cs) estática con un método `Interpolate` que divide una línea recta en numerosas líneas cortas que solo tienen una unidad de longitud. Además, la clase contiene varios métodos que convierten a los tres tipos de curvas de Bézier en una serie de líneas rectas pequeñas que se aproxima a la curva. (Las fórmulas paramétricas se presentaron en el artículo [**tres tipos de curvas de Bézier**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md)). Este proceso se denomina _aplanar_ la curva:

```csharp
static class PathExtensions
{
    ...
    static SKPoint[] Interpolate(SKPoint pt0, SKPoint pt1)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * pt0.X + t * pt1.X;
            float y = (1 - t) * pt0.Y + t * pt1.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenCubic(SKPoint pt0, SKPoint pt1, SKPoint pt2, SKPoint pt3)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2) + Length(pt2, pt3));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * (1 - t) * (1 - t) * pt0.X +
                        3 * t * (1 - t) * (1 - t) * pt1.X +
                        3 * t * t * (1 - t) * pt2.X +
                        t * t * t * pt3.X;
            float y = (1 - t) * (1 - t) * (1 - t) * pt0.Y +
                        3 * t * (1 - t) * (1 - t) * pt1.Y +
                        3 * t * t * (1 - t) * pt2.Y +
                        t * t * t * pt3.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenQuadratic(SKPoint pt0, SKPoint pt1, SKPoint pt2)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * (1 - t) * pt0.X + 2 * t * (1 - t) * pt1.X + t * t * pt2.X;
            float y = (1 - t) * (1 - t) * pt0.Y + 2 * t * (1 - t) * pt1.Y + t * t * pt2.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenConic(SKPoint pt0, SKPoint pt1, SKPoint pt2, float weight)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float denominator = (1 - t) * (1 - t) + 2 * weight * t * (1 - t) + t * t;
            float x = (1 - t) * (1 - t) * pt0.X + 2 * weight * t * (1 - t) * pt1.X + t * t * pt2.X;
            float y = (1 - t) * (1 - t) * pt0.Y + 2 * weight * t * (1 - t) * pt1.Y + t * t * pt2.Y;
            x /= denominator;
            y /= denominator;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static double Length(SKPoint pt0, SKPoint pt1)
    {
        return Math.Sqrt(Math.Pow(pt1.X - pt0.X, 2) + Math.Pow(pt1.Y - pt0.Y, 2));
    }
}
```

Se hace referencia a todos estos métodos desde el método de extensión `CloneWithTransform` también se incluyen en esta clase y se muestran a continuación. Este método clona una ruta de acceso enumerando los comandos de la ruta de acceso y construir una nueva ruta de acceso basado en los datos. Sin embargo, la nueva ruta de acceso consta únicamente de llamadas `MoveTo` y `LineTo`. Todas las líneas rectas y curvas se reducen a una serie de líneas pequeñas.

Al llamar a `CloneWithTransform`, se pasa al método un `Func<SKPoint, SKPoint>`, que es una función con un parámetro `SKPaint` que devuelve un valor de `SKPoint`. Esta función se invoca para que cada punto de aplicar una transformación personalizada algorítmica:

```csharp
static class PathExtensions
{
    public static SKPath CloneWithTransform(this SKPath pathIn, Func<SKPoint, SKPoint> transform)
    {
        SKPath pathOut = new SKPath();

        using (SKPath.RawIterator iterator = pathIn.CreateRawIterator())
        {
            SKPoint[] points = new SKPoint[4];
            SKPathVerb pathVerb = SKPathVerb.Move;
            SKPoint firstPoint = new SKPoint();
            SKPoint lastPoint = new SKPoint();

            while ((pathVerb = iterator.Next(points)) != SKPathVerb.Done)
            {
                switch (pathVerb)
                {
                    case SKPathVerb.Move:
                        pathOut.MoveTo(transform(points[0]));
                        firstPoint = lastPoint = points[0];
                        break;

                    case SKPathVerb.Line:
                        SKPoint[] linePoints = Interpolate(points[0], points[1]);

                        foreach (SKPoint pt in linePoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[1];
                        break;

                    case SKPathVerb.Cubic:
                        SKPoint[] cubicPoints = FlattenCubic(points[0], points[1], points[2], points[3]);

                        foreach (SKPoint pt in cubicPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[3];
                        break;

                    case SKPathVerb.Quad:
                        SKPoint[] quadPoints = FlattenQuadratic(points[0], points[1], points[2]);

                        foreach (SKPoint pt in quadPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[2];
                        break;

                    case SKPathVerb.Conic:
                        SKPoint[] conicPoints = FlattenConic(points[0], points[1], points[2], iterator.ConicWeight());

                        foreach (SKPoint pt in conicPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[2];
                        break;

                    case SKPathVerb.Close:
                        SKPoint[] closePoints = Interpolate(lastPoint, firstPoint);

                        foreach (SKPoint pt in closePoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        firstPoint = lastPoint = new SKPoint(0, 0);
                        pathOut.Close();
                        break;
                }
            }
        }
        return pathOut;
    }
    ...
}
```

Dado que la ruta de acceso clonado se reduce a diminutas líneas rectas, la función de transformación tiene la capacidad de convertir líneas rectas en curvas.

Observe que el método conserva el primer punto de cada contorno en la variable llamada `firstPoint` y la posición actual después de cada comando de dibujo de la variable `lastPoint`. Estas variables son necesarias para construir la última línea de cierre cuando se encuentra un verbo `Close`.

En el ejemplo **GlobularText** se usa este método de extensión para ajustar aparentemente el texto alrededor de un hemisferio en un efecto 3D:

[![](information-images/globulartext-small.png "Triple screenshot of the Globular Text page")](information-images/globulartext-large.png#lightbox "Triple screenshot of the Globular Text page")

El constructor de clase [`GlobularTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/GlobularTextPage.cs) realiza esta transformación. Crea un objeto `SKPaint` para el texto y, a continuación, obtiene un objeto `SKPath` del método `GetTextPath`. Esta es la ruta de acceso que se pasa al método de extensión `CloneWithTransform` junto con una función de transformación:

```csharp
public class GlobularTextPage : ContentPage
{
    SKPath globePath;

    public GlobularTextPage()
    {
        Title = "Globular Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        using (SKPaint textPaint = new SKPaint())
        {
            textPaint.Typeface = SKTypeface.FromFamilyName("Times New Roman");
            textPaint.TextSize = 100;

            using (SKPath textPath = textPaint.GetTextPath("HELLO", 0, 0))
            {
                SKRect textPathBounds;
                textPath.GetBounds(out textPathBounds);

                globePath = textPath.CloneWithTransform((SKPoint pt) =>
                {
                    double longitude = (Math.PI / textPathBounds.Width) *
                                            (pt.X - textPathBounds.Left) - Math.PI / 2;
                    double latitude = (Math.PI / textPathBounds.Height) *
                                            (pt.Y - textPathBounds.Top) - Math.PI / 2;

                    longitude *= 0.75;
                    latitude *= 0.75;

                    float x = (float)(Math.Cos(latitude) * Math.Sin(longitude));
                    float y = (float)Math.Sin(latitude);

                    return new SKPoint(x, y);
                });
            }
        }
    }
    ...
}
```

La función de transformación calcula primero dos valores denominados `longitude` y `latitude` de – π/2 en la parte superior e izquierda del texto hasta π/2 en la parte derecha e inferior del texto. El rango de estos valores no es satisfactorio visualmente, por lo que se reducen al multiplicar por 0,75. (Pruebe el código sin esos ajustes. El texto está demasiado oculto en los polos norte y sur, y demasiado fino en los lados. Estas coordenadas esféricas tridimensionales se convierten en `x` bidimensionales y `y` coordenadas por fórmulas estándar.

La nueva ruta de acceso se almacena como un campo. A continuación, el controlador de `PaintSurface` simplemente necesita centrar y escalar la ruta de acceso para mostrarla en la pantalla:

```csharp
public class GlobularTextPage : ContentPage
{
    SKPath globePath;
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint pathPaint = new SKPaint())
        {
            pathPaint.Style = SKPaintStyle.Fill;
            pathPaint.Color = SKColors.Blue;
            pathPaint.StrokeWidth = 3;
            pathPaint.IsAntialias = true;

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(0.45f * Math.Min(info.Width, info.Height));     // radius
            canvas.DrawPath(globePath, pathPaint);
        }
    }
}
```

Esta es una técnica muy versátil. Si la matriz de los efectos de la ruta de acceso que se describe en el artículo de efectos de la [**ruta**](effects.md) de acceso no abarca bastante cosa que se debe incluir, se trata de una manera de rellenar los huecos.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
