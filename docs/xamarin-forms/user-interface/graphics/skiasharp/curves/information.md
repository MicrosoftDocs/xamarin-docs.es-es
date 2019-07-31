---
title: Enumeración e información de trazado
description: En este artículo se explica cómo obtener información acerca de las rutas de acceso de SkiaSharp y enumerar el contenido y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.assetid: 8E8C5C6A-F324-4155-8652-7A77D231B3E5
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2017
ms.openlocfilehash: 05d5003d349ae11a1ec6a1b6f3d66b2f68ffad8a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652874"
---
# <a name="path-information-and-enumeration"></a>Enumeración e información de trazado

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Obtenga información acerca de las rutas de acceso y enumerar el contenido_

El [ `SKPath` ](xref:SkiaSharp.SKPath) clase define varias propiedades y métodos que permiten obtener información acerca de la ruta de acceso. El [ `Bounds` ](xref:SkiaSharp.SKPath.Bounds) y [ `TightBounds` ](xref:SkiaSharp.SKPath.TightBounds) propiedades (y los métodos relacionados) obtienen la métrica: dimensiones de una ruta de acceso. El [ `Contains` ](xref:SkiaSharp.SKPath.Contains(System.Single,System.Single)) método le permite determinar si un punto determinado está dentro de una ruta de acceso.

A veces resulta útil para determinar la longitud total de todas las líneas y curvas que conforman una ruta de acceso. Calcular este valor de longitud no es una tarea de forma algorítmica sencilla, por lo que toda una clase denominada [ `PathMeasure` ](xref:SkiaSharp.SKPathMeasure) se dedica a él.

También a veces es útil obtener todas las operaciones y los puntos que componen una ruta de acceso de dibujo. En primer lugar, este recurso podría parecer innecesario: Si el programa ha creado la ruta de acceso, el programa ya conoce el contenido. Sin embargo, hemos visto que las rutas de acceso también pueden crearse mediante [efectos del trazado](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) y convirtiendo [cadenas de texto en trazados](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md). También puede obtener todas las operaciones y los puntos que componen estas rutas de acceso de dibujo. Una posibilidad es para aplicar una transformación algorítmica a todos los puntos, por ejemplo, para ajustar el texto en un hemisferio:

![](information-images/pathenumerationsample.png "Texto ajustado en un hemisferio.")

## <a name="getting-the-path-length"></a>Obtención de la longitud de ruta de acceso

En el artículo [ **trazados y texto** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md) vimos cómo usar el [ `DrawTextOnPath` ](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) método para dibujar una cadena de texto cuyo previsto sigue el curso de una ruta de acceso. Pero ¿qué ocurre si desea cambiar el tamaño del texto para que se ajuste con precisión la ruta de acceso? Dibujar texto en torno a un círculo es fácil porque es fácil de calcular la circunferencia de un círculo. Pero la circunferencia de una elipse o la longitud de una curva de Bézier no es tan sencilla.

El [ `SKPathMeasure` ](xref:SkiaSharp.SKPathMeasure) puede ayudar la clase. El [constructor](xref:SkiaSharp.SKPathMeasure.%23ctor(SkiaSharp.SKPath,System.Boolean,System.Single)) acepta un `SKPath` argumento y el [ `Length` ](xref:SkiaSharp.SKPathMeasure.Length) propiedad revela su longitud.

Esta clase se muestra en el **longitud de ruta de acceso** ejemplo, que se basa en el **curva de Bézier** página. El [ **PathLengthPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml) archivo deriva `InteractivePage` e incluye una interfaz táctil:

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

El [ **PathLengthPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml.cs) archivo de código subyacente le permite mover cuatro puntos táctiles para definir los puntos de conexión y puntos de control de una curva Bézier cúbica. Tres campos de definen una cadena de texto, un `SKPaint` objeto y un ancho calculado del texto:

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

El `baseTextWidth` campo es el ancho del texto según un `TextSize` de 10.

El `PaintSurface` controlador dibuja la curva de Bézier y, a continuación, cambia el tamaño del texto se ajuste a lo largo de su longitud total:

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

El `Length` propiedad recién creada `SKPathMeasure` objeto obtiene la longitud de la ruta de acceso. La longitud de ruta de acceso se divide por el `baseTextWidth` valor (que es el ancho del texto según un tamaño de texto de 10) y, a continuación, multiplicado por el tamaño del texto de base 10. El resultado es un nuevo tamaño de texto para mostrar el texto a lo largo de esa ruta de acceso:

[![](information-images/pathlength-small.png "Captura de pantalla triple de la página de longitud de ruta de acceso")](information-images/pathlength-large.png#lightbox "Triple captura de pantalla de la página de longitud de ruta de acceso")

A medida que la curva de Bézier mayores o menores, puede ver el tamaño del texto cambie.

## <a name="traversing-the-path"></a>Atravesar la ruta de acceso

`SKPathMeasure` puede hacer algo más que la medida en la longitud de la ruta de acceso. Para cualquier valor entre cero y la longitud de la ruta de acceso, un `SKPathMeasure` objeto puede obtener la posición en la ruta de acceso y la tangente a la curva de la ruta de acceso en ese momento. La tangente está disponible como un vector en forma de un `SKPoint` de objeto o como un giro encapsulados en un `SKMatrix` objeto. Estos son los métodos de `SKPathMeasure` que obtener esta información de maneras variadas y flexible:

```csharp
Boolean GetPosition (Single distance, out SKPoint position)

Boolean GetTangent (Single distance, out SKPoint tangent)

Boolean GetPositionAndTangent (Single distance, out SKPoint position, out SKPoint tangent)

Boolean GetMatrix (Single distance, out SKMatrix matrix, SKPathMeasureMatrixFlags flag)
```

Los miembros de la [ `SKPathMeasureMatrixFlags` ](xref:SkiaSharp.SKPathMeasureMatrixFlags) enumeración son:

- `GetPosition`
- `GetTangent`
- `GetPositionAndTangent`

El **monociclo mitad canalización** página anima un dibujo en un monociclo que parece andar y hacia atrás a lo largo de una curva Bézier cúbica:

[![](information-images/unicyclehalfpipe-small.png "Captura de pantalla de la página de la canalización con mitad monociclo triple")](information-images/unicyclehalfpipe-large.png#lightbox "Triple captura de pantalla de la página de la canalización con mitad monociclo")

El `SKPaint` objeto que se usa para contornear la media canalización y UNICYCLE se define como un campo `UnicycleHalfPipePage` en la clase. También define es el `SKPath` objeto para el monociclo:

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

La clase contiene las invalidaciones estándares de la `OnAppearing` y `OnDisappearing` métodos para ver la animación. El `PaintSurface` controlador crea la ruta de acceso de la canalización de la mitad y, a continuación, lo dibuja. Un `SKPathMeasure` objeto, a continuación, se crea en función de esta ruta de acceso:

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

El `PaintSurface` controlador calcula un valor de `t` que va de 0 a 1 cada cinco segundos. A continuación, usa el `Math.Cos` función a convertirlo en un valor de `t` cuyos intervalos van de 0 a 1 y volver a 0, donde 0 corresponde a la monociclo al principio de la esquina superior izquierda, mientras que 1 se corresponde con el monociclo la esquina superior derecha. La función de coseno hace que la velocidad sea más lenta en la parte superior de la canalización y más rápido en la parte inferior.

Tenga en cuenta que este valor de `t` debe ser multiplicado por la longitud de ruta de acceso para el primer argumento `GetMatrix`. La matriz, a continuación, se aplica a la `SKCanvas` objeto para dibujar el trazado monociclo.

## <a name="enumerating-the-path"></a>Enumerar la ruta de acceso

Dos integrados con las clases de `SKPath` le permiten enumerar el contenido de la ruta de acceso. Estas clases son [ `SKPath.Iterator` ](xref:SkiaSharp.SKPath.Iterator) y [ `SKPath.RawIterator` ](xref:SkiaSharp.SKPath.RawIterator). Las dos clases son muy similares, pero `SKPath.Iterator` pueden eliminar elementos en la ruta de acceso con una longitud de cero, o cerca de una longitud cero. El `RawIterator` se utiliza en el ejemplo siguiente.

Puede obtener un objeto de tipo `SKPath.RawIterator` mediante una llamada a la [ `CreateRawIterator` ](xref:SkiaSharp.SKPath.CreateRawIterator) método `SKPath`. Enumerar archivos en la ruta de acceso se logra llamando repetidamente el [ `Next` ](xref:SkiaSharp.SKPath.RawIterator.Next*) método. Pasa una matriz de cuatro `SKPoint` valores:

```csharp
SKPoint[] points = new SKPoint[4];
...
SKPathVerb pathVerb = rawIterator.Next(points);
```

El `Next` método devuelve un miembro de la [ `SKPathVerb` ](xref:SkiaSharp.SKPathVerb) tipo de enumeración. Estos valores indican que el comando de dibujo particular en la ruta de acceso. El número de puntos válidos insertado en la matriz depende de este verbo:

- `Move` con un solo punto
- `Line` con dos puntos
- `Cubic` con cuatro puntos
- `Quad` con tres puntos
- `Conic` con tres puntos (y llamar también a la [ `ConicWeight` ](xref:SkiaSharp.SKPath.RawIterator.ConicWeight*) método para el peso)
- `Close` con un punto
- `Done`

El `Done` verbo indica que la enumeración de la ruta de acceso se ha completado.

Tenga en cuenta que no hay ningún `Arc` verbos. Esto indica que todos los arcos se convierten en curvas de Bézier cuando se agrega a la ruta de acceso.

Parte de la información en el `SKPoint` matriz es redundante. Por ejemplo, si un `Move` verbo es seguido por un `Line` verbo y, a continuación, el primero de los dos puntos que acompañan a la `Line` es el mismo que el `Move` punto. En la práctica, esta redundancia es muy útil. Cuando llegue una `Cubic` verbo, viene acompañado por todos los cuatro puntos que definen la curva de Bézier cúbica. No tiene que conservar la posición actual establecida por el verbo anterior.

Sin embargo, es el verbo problemático, `Close`. Este comando dibuja una línea recta desde la posición actual hasta el principio del contorno establecido anteriormente la `Move` comando. Idealmente, el `Close` verbo debe proporcionar estos dos puntos en lugar de un solo punto. Lo que es peor que es el punto que acompaña la `Close` verbo siempre es (0, 0). Cuando enumere una ruta de acceso, probablemente necesitará conservar los `Move` punto y la posición actual.

## <a name="enumerating-flattening-and-malforming"></a>Enumerar, acoplar y Malforming

A veces es deseable para aplicar un algoritmo transformar una ruta de acceso a malform de alguna manera:

![](information-images/pathenumerationsample.png "Texto ajustado en un hemisferio.")

La mayoría de estas letras se componen de líneas rectas, aunque aparentemente se han twisted estas líneas rectas en curvas. ¿Cómo es esto posible?

La clave es que las líneas rectas originales se dividen en una serie de líneas rectas más pequeñas. A continuación, se pueden manipular estas líneas rectas menor individuales de maneras diferentes para formar una curva.

Para facilitar este proceso, el [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) ejemplo contiene una variable static [ `PathExtensions` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathExtensions.cs) clase con un `Interpolate` método que desglosa un línea recta en numerosas líneas cortas que son solo una unidad de longitud. Además, la clase contiene varios métodos que convierten a los tres tipos de curvas de Bézier en una serie de líneas rectas pequeñas que se aproxima a la curva. (Las fórmulas paramétricas se presentaron en el artículo [ **tipos tres de curvas de Bézier**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md).) Este proceso se denomina _aplanamiento_ la curva:

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
        }

        return points;
    }

    static double Length(SKPoint pt0, SKPoint pt1)
    {
        return Math.Sqrt(Math.Pow(pt1.X - pt0.X, 2) + Math.Pow(pt1.Y - pt0.Y, 2));
    }
}
```

Todos estos métodos se hace referencia desde el método de extensión `CloneWithTransform` también se incluyen en esta clase y se muestra a continuación. Este método clona una ruta de acceso enumerando los comandos de la ruta de acceso y construir una nueva ruta de acceso basado en los datos. Sin embargo, la nueva ruta de acceso consta solo de `MoveTo` y `LineTo` llamadas. Todas las líneas rectas y curvas se reducen a una serie de líneas pequeñas.

Al llamar a `CloneWithTransform`, se pasa al método un `Func<SKPoint, SKPoint>`, que es una función con un `SKPaint` parámetro que devuelve un `SKPoint` valor. Esta función se invoca para que cada punto de aplicar una transformación personalizada algorítmica:

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

Tenga en cuenta que el método conserva el primer punto de cada contorno en la variable llamada `firstPoint` y la posición actual después de cada comando de dibujo en la variable `lastPoint`. Estas variables son necesarias para construir el cierre final de línea cuando un `Close` se encuentra el verbo.

El **GlobularText** ejemplo utiliza este método de extensión aparentemente ajustar texto alrededor de un hemisferio en un efecto 3D:

[![](information-images/globulartext-small.png "Captura de pantalla triple de la página de texto Globular")](information-images/globulartext-large.png#lightbox "Triple captura de pantalla de la página de texto Globular")

El [ `GlobularTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/GlobularTextPage.cs) constructor de clase lleva a cabo esta transformación. Crea un `SKPaint` de objeto para el texto y, a continuación, obtiene un `SKPath` objeto desde el `GetTextPath` método. Se trata de la ruta de acceso pasa a la `CloneWithTransform` método de extensión junto con una función de transformación:

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

La función de transformación calcula primero los dos valores denominados `longitude` y `latitude` que van de – π/2 en la parte superior e izquierda del texto, a π/2 a la derecha e inferior del texto. El rango de estos valores no es satisfactorio visualmente, por lo que se reducen al multiplicar por 0,75. (Pruebe el código sin esos ajustes. El texto se convierte en demasiado oscuros en el norte y sur polos y demasiado delgado en los lados.) Estas coordenadas esféricas tridimensionales se convierten a bidimensional `x` y `y` coordenadas mediante fórmulas estándares.

La nueva ruta de acceso se almacena como un campo. El `PaintSurface` controlador, a continuación, simplemente necesita centrar y escalar la ruta de acceso para que se muestre en la pantalla:

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

Esta es una técnica muy versátil. Si la matriz de los efectos de la ruta de acceso que se describe en el [ **efectos del trazado** ](effects.md) artículo no abarca bastante algo que creemos que debe incluirse, se trata de una manera de rellenar los huecos.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
