---
title: Enumeración e información de trazado
description: En este artículo se explica cómo obtener información sobre las rutas de acceso de SkiaSharp y enumerar el contenido y se muestra con código de ejemplo.
ms.prod: xamarin
ms.assetid: 8E8C5C6A-F324-4155-8652-7A77D231B3E5
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cf9ebb819d5b424963170d563575c4900bbed28b
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556365"
---
# <a name="path-information-and-enumeration"></a>Enumeración e información de trazado

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Obtener información sobre las rutas de acceso y enumerar el contenido_

La [`SKPath`](xref:SkiaSharp.SKPath) clase define varias propiedades y métodos que permiten obtener información sobre la ruta de acceso. Las [`Bounds`](xref:SkiaSharp.SKPath.Bounds) [`TightBounds`](xref:SkiaSharp.SKPath.TightBounds) propiedades y (y los métodos relacionados) obtienen las dimensiones métricas de un trazado. El [`Contains`](xref:SkiaSharp.SKPath.Contains(System.Single,System.Single)) método le permite determinar si un punto determinado se encuentra dentro de una ruta de acceso.

A veces resulta útil determinar la longitud total de todas las líneas y curvas que componen un trazado. Calcular esta longitud no es una tarea con algoritmos simples, por lo que se le dedica una clase completa denominada [`PathMeasure`](xref:SkiaSharp.SKPathMeasure) .

En ocasiones, también resulta útil obtener todas las operaciones de dibujo y los puntos que componen una ruta de acceso. En primer lugar, es posible que este recurso parezca innecesario: Si el programa ha creado la ruta de acceso, el programa ya conoce el contenido. Sin embargo, ha visto que las rutas de acceso también pueden crearse mediante efectos de trazado y convirtiendo [cadenas de texto en rutas](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)de [acceso](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) . También puede obtener todas las operaciones de dibujo y los puntos que componen estas rutas de acceso. Una posibilidad es aplicar una transformación algorítmica a todos los puntos, por ejemplo, para ajustar el texto alrededor de un hemisferio:

![Texto ajustado en un hemisferio](information-images/pathenumerationsample.png)

## <a name="getting-the-path-length"></a>Obtención de la longitud de la ruta de acceso

En el artículo [**rutas de acceso y texto**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md) vio cómo usar el [`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) método para dibujar una cadena de texto cuya línea base sigue el curso de un trazado. Pero, ¿qué ocurre si desea cambiar el tamaño del texto para que se ajuste a la ruta de acceso con precisión? Dibujar texto alrededor de un círculo es fácil porque la circunferencia de un círculo es fácil de calcular. Pero la circunferencia de una elipse o la longitud de una curva de Bézier no es tan sencilla.

La [`SKPathMeasure`](xref:SkiaSharp.SKPathMeasure) clase puede ayudar. El [constructor](xref:SkiaSharp.SKPathMeasure.%23ctor(SkiaSharp.SKPath,System.Boolean,System.Single)) acepta un `SKPath` argumento y la [`Length`](xref:SkiaSharp.SKPathMeasure.Length) propiedad revela su longitud.

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

El archivo de código subyacente [**PathLengthPage.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml.cs) permite desplace cuatro puntos táctiles para definir los puntos de control y los puntos de control de una curva Bézier cúbica. Tres campos definen una cadena de texto, un `SKPaint` objeto y un ancho calculado del texto:

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

El `baseTextWidth` campo es el ancho del texto basado en un `TextSize` valor de 10.

El `PaintSurface` controlador dibuja la curva de Bézier y, a continuación, ajusta el tamaño del texto para que quepa a lo largo de su longitud completa:

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

La `Length` propiedad del objeto recién creado `SKPathMeasure` obtiene la longitud de la ruta de acceso. La longitud de la ruta de acceso se divide por el `baseTextWidth` valor (que es el ancho del texto basándose en un tamaño de texto de 10) y, a continuación, se multiplica por el tamaño de texto base de 10. El resultado es un nuevo tamaño de texto para mostrar el texto a lo largo de la ruta de acceso:

[![Captura de pantalla triple de la página longitud de la ruta de acceso](information-images/pathlength-small.png)](information-images/pathlength-large.png#lightbox "Captura de pantalla triple de la página longitud de la ruta de acceso")

Como la curva de Bézier es más larga o más corta, puede ver el cambio de tamaño del texto.

## <a name="traversing-the-path"></a>Atravesar la ruta de acceso

`SKPathMeasure` puede hacer algo más que simplemente medir la longitud de la ruta de acceso. Para cualquier valor entre cero y la longitud de la ruta de acceso, un `SKPathMeasure` objeto puede obtener la posición en la ruta de acceso y la tangente a la curva de trazado en ese punto. La tangente está disponible como vector en forma de un `SKPoint` objeto, o como un giro encapsulado en un `SKMatrix` objeto. Estos son los métodos de `SKPathMeasure` que obtienen esta información de maneras diferentes y flexibles:

```csharp
Boolean GetPosition (Single distance, out SKPoint position)

Boolean GetTangent (Single distance, out SKPoint tangent)

Boolean GetPositionAndTangent (Single distance, out SKPoint position, out SKPoint tangent)

Boolean GetMatrix (Single distance, out SKMatrix matrix, SKPathMeasureMatrixFlags flag)
```

Los miembros de la [`SKPathMeasureMatrixFlags`](xref:SkiaSharp.SKPathMeasureMatrixFlags) enumeración son:

- `GetPosition`
- `GetTangent`
- `GetPositionAndTangent`

La página de la **media de UNICYCLE** anima una figura de palo en un UNICYCLE que parece moverse hacia delante y hacia atrás a lo largo de una curva Bézier cúbica:

[![Captura de pantalla triple de la página de la media de UNICYCLE](information-images/unicyclehalfpipe-small.png)](information-images/unicyclehalfpipe-large.png#lightbox "Captura de pantalla triple de la página de la media de UNICYCLE")

El `SKPaint` objeto que se usa para contornear la media canalización y UNICYCLE se define como un campo en la `UnicycleHalfPipePage` clase. También se define el `SKPath` objeto de UNICYCLE:

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

La clase contiene las invalidaciones estándar de los `OnAppearing` `OnDisappearing` métodos y para la animación. El `PaintSurface` controlador crea la ruta de acceso para la mitad de la canalización y, a continuación, la dibuja. `SKPathMeasure`A continuación, se crea un objeto basándose en esta ruta de acceso:

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

El `PaintSurface` controlador calcula un valor de `t` que va de 0 a 1 cada cinco segundos. A continuación, utiliza la `Math.Cos` función para convertirla en un valor de `t` que va de 0 a 1 y de nuevo a 0, donde 0 corresponde a UNICYCLE al principio de la parte superior izquierda, y 1 corresponde a UNICYCLE en la parte superior derecha. La función coseno hace que la velocidad sea más lenta en la parte superior de la canalización y más rápida en la parte inferior.

Tenga en cuenta que este valor de `t` se debe multiplicar por la longitud de la ruta de acceso del primer argumento a `GetMatrix` . A continuación, la matriz se aplica al `SKCanvas` objeto para dibujar la ruta de acceso UNICYCLE.

## <a name="enumerating-the-path"></a>Enumerar la ruta de acceso

Dos clases incrustadas de `SKPath` permiten enumerar el contenido de la ruta de acceso. Estas clases son [`SKPath.Iterator`](xref:SkiaSharp.SKPath.Iterator) y [`SKPath.RawIterator`](xref:SkiaSharp.SKPath.RawIterator) . Las dos clases son muy similares, pero `SKPath.Iterator` pueden eliminar elementos de la ruta de acceso con una longitud cero o cercanos a una longitud cero. `RawIterator`Se utiliza en el ejemplo siguiente.

Puede obtener un objeto de tipo llamando al `SKPath.RawIterator` [`CreateRawIterator`](xref:SkiaSharp.SKPath.CreateRawIterator) método de `SKPath` . La enumeración a través de la ruta de acceso se realiza mediante una llamada repetida al [`Next`](xref:SkiaSharp.SKPath.RawIterator.Next*) método. Pásele una matriz de cuatro `SKPoint` valores:

```csharp
SKPoint[] points = new SKPoint[4];
...
SKPathVerb pathVerb = rawIterator.Next(points);
```

El `Next` método devuelve un miembro del [`SKPathVerb`](xref:SkiaSharp.SKPathVerb) tipo de enumeración. Estos valores indican el comando de dibujo determinado en la ruta de acceso. El número de puntos válidos insertados en la matriz depende de este verbo:

- `Move` con un solo punto
- `Line` con dos puntos
- `Cubic` con cuatro puntos
- `Quad` con tres puntos
- `Conic` con tres puntos (y también llama al [`ConicWeight`](xref:SkiaSharp.SKPath.RawIterator.ConicWeight*) método para el peso)
- `Close` con un punto
- `Done`

El `Done` verbo indica que la enumeración de la ruta de acceso está completa.

Observe que no hay `Arc` verbos. Esto indica que todos los arcos se convierten en curvas Bezier cuando se agregan a la ruta de acceso.

Parte de la información de la `SKPoint` matriz es redundante. Por ejemplo, si un `Move` verbo va seguido de un `Line` verbo, el primero de los dos puntos que acompañan `Line` es igual que el `Move` punto. En la práctica, esta redundancia es muy útil. Al obtener un `Cubic` verbo, va acompañado de los cuatro puntos que definen la curva Bézier cúbica. No es necesario conservar la posición actual establecida por el verbo anterior.

Sin embargo, el verbo problemático es `Close` . Este comando dibuja una línea recta desde la posición actual hasta el principio del contorno establecido anteriormente por el `Move` comando. Idealmente, el `Close` verbo debe proporcionar estos dos puntos en lugar de un solo punto. Lo peor es que el punto que acompaña al `Close` verbo siempre es (0,0). Al enumerar a través de una ruta de acceso, probablemente tendrá que conservar el `Move` punto y la posición actual.

## <a name="enumerating-flattening-and-malforming"></a>Enumerar, aplanar y crear un formato incorrecto

A veces es conveniente aplicar una transformación algorítmica a una ruta de acceso para crear un formato incorrecto de alguna manera:

![Texto ajustado en un hemisferio](information-images/pathenumerationsample.png)

La mayoría de estas letras se componen de líneas rectas, pero estas líneas rectas parecen torcidas en curvas. ¿Cómo es posible?

La clave es que las líneas rectas originales se dividen en una serie de líneas rectas más pequeñas. A continuación, estas líneas rectas más pequeñas pueden manipularse de maneras diferentes para formar una curva.

Para ayudar con este proceso, el ejemplo [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) contiene una [`PathExtensions`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathExtensions.cs) clase estática con un `Interpolate` método que divide una línea recta en numerosas líneas cortas que solo tienen una unidad de longitud. Además, la clase contiene varios métodos que convierten los tres tipos de curvas de Bézier en una serie de pequeñas líneas rectas que se aproximan a la curva. (Las fórmulas paramétricas se presentaron en el artículo [**tres tipos de curvas de Bézier**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md)). Este proceso se denomina _aplanar_ la curva:

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

Se hace referencia a todos estos métodos desde el método de extensión `CloneWithTransform` incluido también en esta clase y se muestra a continuación. Este método clona una ruta de acceso mediante la enumeración de los comandos path y la construcción de una nueva ruta de acceso basada en los datos. Sin embargo, la nueva ruta de acceso consta únicamente de `MoveTo` `LineTo` llamadas y. Todas las curvas y líneas rectas se reducen a una serie de líneas pequeñas.

Al llamar `CloneWithTransform` a, se pasa al método a `Func<SKPoint, SKPoint>` , que es una función con un `SKPaint` parámetro que devuelve un `SKPoint` valor. Se llama a esta función para cada punto para aplicar una transformación algorítmica personalizada:

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

Dado que la ruta de acceso clonada se reduce a pequeñas líneas rectas, la función de transformación tiene la capacidad de convertir líneas rectas en curvas.

Observe que el método conserva el primer punto de cada contorno en la variable llamada `firstPoint` y la posición actual después de cada comando de dibujo de la variable `lastPoint` . Estas variables son necesarias para construir la última línea de cierre cuando `Close` se encuentra un verbo.

En el ejemplo **GlobularText** se usa este método de extensión para ajustar aparentemente el texto alrededor de un hemisferio en un efecto 3D:

[![Captura de pantalla triple de la página de texto globular](information-images/globulartext-small.png)](information-images/globulartext-large.png#lightbox "Captura de pantalla triple de la página de texto globular")

El [`GlobularTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/GlobularTextPage.cs) constructor de clase realiza esta transformación. Crea un `SKPaint` objeto para el texto y, a continuación, obtiene un `SKPath` objeto a partir del `GetTextPath` método. Esta es la ruta de acceso que se pasa al `CloneWithTransform` método de extensión junto con una función de transformación:

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

La función de transformación calcula primero dos valores denominados `longitude` y el `latitude` intervalo de – π/2 en la parte superior e izquierda del texto, a π/2 a la derecha y la parte inferior del texto. El intervalo de estos valores no es visualmente satisfactorio, por lo que se reducen multiplicando por 0,75. (Pruebe el código sin esos ajustes. El texto está demasiado oculto en los polos norte y sur, y demasiado fino en los lados. Estas coordenadas esféricas tridimensionales se convierten en dos dimensiones `x` y en `y` coordenadas por fórmulas estándar.

La nueva ruta de acceso se almacena como un campo. `PaintSurface`A continuación, el controlador solo necesita centrar y escalar la ruta de acceso para mostrarla en la pantalla:

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

Se trata de una técnica muy versátil. Si la matriz de los efectos de la ruta de acceso que se describe en el artículo de efectos de la [**ruta**](effects.md) de acceso no abarca bastante cosa que se debe incluir, se trata de una manera de rellenar los huecos.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)