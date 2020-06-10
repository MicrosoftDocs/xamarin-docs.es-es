---
title: "transformaciones de matriz en SkiaSharp" Description: "este artículo profundiza en las transformaciones de SkiaSharp con una matriz de transformación versátil y demuestra esto con código de ejemplo".
MS. Prod: Xamarin ms. Technology: Xamarin-skiasharp ms. AssetID: 9EDED6A0-F0BF-4471-A9EF-E0D6C5954AE4 Author: davidbritch ms. Author: dabritch ms. Date: 04/12/2017 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="matrix-transforms-in-skiasharp"></a>Transformaciones de matriz en SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Profundice en las transformaciones de SkiaSharp con la matriz de transformación versátil_

Todas las transformaciones aplicadas al `SKCanvas` objeto se consolidan en una sola instancia de la [`SKMatrix`](xref:SkiaSharp.SKMatrix) estructura. Se trata de una matriz de transformación estándar de 3 por 3 similar a la de todos los sistemas de gráficos 2D modernos.

Como ha visto, puede usar transformaciones en SkiaSharp sin conocer la matriz de transformación, pero la matriz de transformación es importante desde una perspectiva teórica, y es fundamental al usar transformaciones para modificar las rutas de acceso o para administrar la entrada táctil compleja, que se muestran en este artículo y en la siguiente.

![](matrix-images/matrixtransformexample.png "A bitmap subjected to an affine transform")

La matriz de transformación actual aplicada a `SKCanvas` está disponible en cualquier momento mediante el acceso a la propiedad de solo lectura [`TotalMatrix`](xref:SkiaSharp.SKCanvas.TotalMatrix) . Puede establecer una nueva matriz de transformación mediante el [`SetMatrix`](xref:SkiaSharp.SKCanvas.SetMatrix(SkiaSharp.SKMatrix)) método, y puede restaurar la matriz de transformación a los valores predeterminados llamando a [`ResetMatrix`](xref:SkiaSharp.SKCanvas.ResetMatrix) .

El único otro `SKCanvas` miembro que trabaja directamente con la transformación de matriz del lienzo es [`Concat`](xref:SkiaSharp.SKCanvas.Concat(SkiaSharp.SKMatrix@)) que concatena dos matrices multiplicándolo entre sí.

La matriz de transformación predeterminada es la matriz de identidad y consta de 1 en las celdas diagonales y de 0 en cualquier otro lugar:

<pre>
| 1  0  0 |
| 0  1  0 |
| 0  0  1 |
</pre>

Puede crear una matriz de identidad mediante el [`SKMatrix.MakeIdentity`](xref:SkiaSharp.SKMatrix.MakeIdentity) método estático:

```csharp
SKMatrix matrix = SKMatrix.MakeIdentity();
```

El `SKMatrix` constructor predeterminado no *not* devuelve una matriz de identidad. Devuelve una matriz con todas las celdas establecidas en cero. No use el `SKMatrix` constructor a menos que planee establecer esas celdas manualmente.

Cuando SkiaSharp representa un objeto gráfico, cada punto (x, y) se convierte efectivamente en una matriz de uno por 3 con un 1 en la tercera columna:

<pre>
| x  y  1 |
</pre>

Esta matriz de uno a tres representa un punto tridimensional con la coordenada Z establecida en 1. Hay razones matemáticas (descritas más adelante) por qué una transformación de matriz bidimensional requiere trabajar en tres dimensiones. Puede considerar que esta matriz 1 por 3 representa un punto en un sistema de coordenadas 3D, pero siempre en el plano 2D donde Z es igual a 1.

A continuación, la matriz de transformación multiplica esta matriz 1 por 3 y el resultado es el punto representado en el lienzo:

<pre>
              | 1  0  0 |
| x  y  1 | × | 0  1  0 | = | x'  y'  z' |
              | 0  0  1 |
</pre>

Mediante la multiplicación de matrices estándar, los puntos convertidos son los siguientes:

`x' = x`

`y' = y`

`z' = 1`

Esta es la transformación predeterminada.

Cuando `Translate` se llama al método en el `SKCanvas` objeto, los `tx` argumentos y del `ty` método se `Translate` convierten en las dos primeras celdas de la tercera fila de la matriz de transformación:

<pre>
|  1   0   0 |
|  0   1   0 |
| tx  ty   1 |
</pre>

La multiplicación es ahora como sigue:

<pre>
              |  1   0   0 |
| x  y  1 | × |  0   1   0 | = | x'  y'  z' |
              | tx  ty   1 |
</pre>

Estas son las fórmulas de transformación:

`x' = x + tx`

`y' = y + ty`

Los factores de escala tienen un valor predeterminado de 1. Cuando se llama al `Scale` método en un nuevo `SKCanvas` objeto, la matriz de transformación resultante contiene los `sx` `sy` argumentos y en las celdas diagonales:

<pre>
              | sx   0   0 |
| x  y  1 | × |  0  sy   0 | = | x'  y'  z' |
              |  0   0   1 |
</pre>

Las fórmulas de transformación son las siguientes:

`x' = sx · x`

`y' = sy · y`

La matriz de transformación después de llamar `Skew` a contiene los dos argumentos en las celdas de la matriz adyacentes a los factores de escala:

<pre>
              │   1   ySkew   0 │
| x  y  1 | × │ xSkew   1     0 │ = | x'  y'  z' |
              │   0     0     1 │
</pre>

Las fórmulas de transformación son:

`x' = x + xSkew · y`

`y' = ySkew · x + y`

En una llamada a `RotateDegrees` o `RotateRadians` para un ángulo de α, la matriz de transformación es la siguiente:

<pre>
              │  cos(α)  sin(α)  0 │
| x  y  1 | × │ –sin(α)  cos(α)  0 │ = | x'  y'  z' |
              │    0       0     1 │
</pre>

Estas son las fórmulas de transformación:

`x' = cos(α) · x - sin(α) · y`

`y' = sin(α) · x - cos(α) · y`

Cuando α es 0 grados, es la matriz de identidades. Cuando α es 180 grados, la matriz de transformación es la siguiente:

<pre>
| –1   0   0 |
|  0  –1   0 |
|  0   0   1 |
</pre>

Una rotación de 180 grados equivale a voltear un objeto horizontalmente y verticalmente, lo que también se consigue mediante el establecimiento de factores de escala de – 1.

Todos estos tipos de transformaciones se clasifican como transformaciones *afines* . Las transformaciones afines nunca implican la tercera columna de la matriz, que permanece con los valores predeterminados de 0, 0 y 1. El artículo [**transformaciones no afines**](non-affine.md) describe las transformaciones no afines.

## <a name="matrix-multiplication"></a>Multiplicación de matrices

Una ventaja importante con el uso de la matriz de transformación es que las transformaciones compuestas se pueden obtener mediante la multiplicación de matrices, a la que a menudo se hace referencia en la documentación de SkiaSharp como *concatenación*. Muchos de los métodos relacionados con la transformación en `SKCanvas` hacen referencia a "preconcatenación" o "anterior a concat". Esto hace referencia al orden de multiplicación, que es importante porque la multiplicación de matrices no es conmutativa.

Por ejemplo, la documentación del [`Translate`](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single)) método indica que "preasigna la matriz actual con la traducción especificada", mientras que la documentación para el [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single)) método indica que "preasigna la matriz actual con la escala especificada".

Esto significa que la transformación especificada por la llamada al método es el multiplicador (el operando izquierdo) y la matriz de transformación actual es el multiplicando (el operando derecho).

Supongamos que `Translate` se llama seguido de `Scale` :

```csharp
canvas.Translate(tx, ty);
canvas.Scale(sx, sy);
```

La transformación `Scale` se multiplica por la `Translate` transformación de la matriz de transformación compuesta:

<pre>
| sx   0   0 |   |  1   0   0 |   | sx   0   0 |
|  0  sy   0 | × |  0   1   0 | = |  0  sy   0 |
|  0   0   1 |   | tx  ty   1 |   | tx  ty   1 |
</pre>

`Scale`se podría llamar a antes que `Translate` :

```csharp
canvas.Scale(sx, sy);
canvas.Translate(tx, ty);
```

En ese caso, se invierte el orden de la multiplicación y los factores de escala se aplican eficazmente a los factores de traducción:

<pre>
|  1   0   0 |   | sx   0   0 |   |  sx      0    0 |
|  0   1   0 | × |  0  sy   0 | = |   0     sy    0 |
| tx  ty   1 |   |  0   0   1 |   | tx·sx  ty·sy  1 |
</pre>

Este es el `Scale` método con un punto de pivote:

```csharp
canvas.Scale(sx, sy, px, py);
```

Esto es equivalente a las siguientes llamadas de traslación y escala:

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Las tres matrices de transformación se multiplican en orden inverso al modo en que los métodos aparecen en el código:

<pre>
|  1    0   0 |   | sx   0   0 |   |  1   0  0 |   |    sx         0     0 |
|  0    1   0 | × |  0  sy   0 | × |  0   1  0 | = |     0        sy     0 |
| –px  –py  1 |   |  0   0   1 |   | px  py  1 |   | px–px·sx  py–py·sy  1 |
</pre>

## <a name="the-skmatrix-structure"></a>Estructura SKMatrix

La `SKMatrix` estructura define nueve propiedades de lectura y escritura del tipo `float` correspondiente a las nueve celdas de la matriz de transformación:

<pre>
│ ScaleX  SkewY   Persp0 │
│ SkewX   ScaleY  Persp1 │
│ TransX  TransY  Persp2 │
</pre>

`SKMatrix`también define una propiedad denominada [`Values`](xref:SkiaSharp.SKMatrix.Values) de tipo `float[]` . Esta propiedad se puede usar para establecer u obtener los nueve valores en una captura en el orden `ScaleX` , `SkewX` , `TransX` , `SkewY` , `ScaleY` , `TransY` , `Persp0` , `Persp1` y `Persp2` .

Las `Persp0` `Persp1` celdas, y `Persp2` se describen en el artículo [**transformaciones no afines**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md). Si estas celdas tienen sus valores predeterminados de 0, 0 y 1, la transformación se multiplica por un punto de coordenadas como el siguiente:

<pre>
              │ ScaleX  SkewY   0 │
| x  y  1 | × │ SkewX   ScaleY  0 │ = | x'  y'  z' |
              │ TransX  TransY  1 │
</pre>

`x' = ScaleX · x + SkewX · y + TransX`

`y' = SkewX · x + ScaleY · y + TransY`

`z' = 1`

Esta es la transformación afín bidimensional completa. La transformación afín conserva las líneas paralelas, lo que significa que un rectángulo nunca se transforma en algo que no sea un paralelogramo.

La `SKMatrix` estructura define varios métodos estáticos para crear `SKMatrix` valores. Estos son todos los valores devueltos `SKMatrix` :

- [`MakeTranslation`](xref:SkiaSharp.SKMatrix.MakeTranslation(System.Single,System.Single))
- [`MakeScale`](xref:SkiaSharp.SKMatrix.MakeScale(System.Single,System.Single))
- [`MakeScale`](xref:SkiaSharp.SKMatrix.MakeScale(System.Single,System.Single,System.Single,System.Single))con un punto de pivote
- [`MakeRotation`](xref:SkiaSharp.SKMatrix.MakeRotation(System.Single))para un ángulo en radianes
- [`MakeRotation`](xref:SkiaSharp.SKMatrix.MakeRotation(System.Single,System.Single,System.Single))para un ángulo en radianes con un punto de pivote
- [`MakeRotationDegrees`](xref:SkiaSharp.SKMatrix.MakeRotationDegrees(System.Single))
- [`MakeRotationDegrees`](xref:SkiaSharp.SKMatrix.MakeRotationDegrees(System.Single,System.Single,System.Single))con un punto de pivote
- [`MakeSkew`](xref:SkiaSharp.SKMatrix.MakeSkew(System.Single,System.Single))

`SKMatrix`también define varios métodos estáticos que concatenan dos matrices, lo que significa multiplicarlas. Estos métodos se denominan [`Concat`](xref:SkiaSharp.SKMatrix.Concat*) , [`PostConcat`](xref:SkiaSharp.SKMatrix.PostConcat*) y [`PreConcat`](xref:SkiaSharp.SKMatrix.PreConcat*) , y hay dos versiones de cada uno. Estos métodos no tienen valores devueltos; en su lugar, hacen referencia `SKMatrix` a los valores existentes a través de los `ref` argumentos. En el ejemplo siguiente, `A` , `B` y `R` (para "resultado") son todos los `SKMatrix` valores.

Los dos `Concat` métodos se denominan de la siguiente manera:

```csharp
SKMatrix.Concat(ref R, A, B);

SKMatrix.Concat(ref R, ref A, ref B);
```

Estos realizan la multiplicación siguiente:

`R = B × A`

Los otros métodos solo tienen dos parámetros. El primer parámetro se modifica y, en la devolución de la llamada al método, contiene el producto de las dos matrices. Los dos `PostConcat` métodos se denominan de la siguiente manera:

```csharp
SKMatrix.PostConcat(ref A, B);

SKMatrix.PostConcat(ref A, ref B);
```

Estas llamadas realizan la operación siguiente:

`A = A × B`

Los dos `PreConcat` métodos son similares:

```csharp
SKMatrix.PreConcat(ref A, B);

SKMatrix.PreConcat(ref A, ref B);
```

Estas llamadas realizan la operación siguiente:

`A = B × A`

Las versiones de estos métodos con todos los `ref` argumentos son ligeramente más eficaces para llamar a las implementaciones subyacentes, pero podría resultar confuso para alguien que lea el código y suponiendo que cualquier elemento con un `ref` argumento se modifique mediante el método. Además, a menudo resulta cómodo pasar un argumento que sea el resultado de uno de los `Make` métodos, por ejemplo:

```csharp
SKMatrix result;
SKMatrix.Concat(result, SKMatrix.MakeTranslation(100, 100),
                        SKMatrix.MakeScale(3, 3));
```

Esto crea la siguiente matriz:

<pre>
│   3    0  0 │
│   0    3  0 │
│ 100  100  1 │
</pre>

Esta es la transformación de escala multiplicada por la transformación traducir. En este caso concreto, la `SKMatrix` estructura proporciona un acceso directo con un método denominado [`SetScaleTranslate`](xref:SkiaSharp.SKMatrix.SetScaleTranslate(System.Single,System.Single,System.Single,System.Single)) :

```csharp
SKMatrix R = new SKMatrix();
R.SetScaleTranslate(3, 3, 100, 100);
```

Esta es una de las veces en las que es seguro utilizar el `SKMatrix` constructor. El `SetScaleTranslate` método establece las nueve celdas de la matriz. También es seguro usar el `SKMatrix` constructor con los `Rotate` métodos y estáticos `RotateDegrees` :

```csharp
SKMatrix R = new SKMatrix();

SKMatrix.Rotate(ref R, radians);

SKMatrix.Rotate(ref R, radians, px, py);

SKMatrix.RotateDegrees(ref R, degrees);

SKMatrix.RotateDegrees(ref R, degrees, px, py);
```

Estos métodos *no* concatenan una transformación de giro en una transformación existente. Los métodos establecen todas las celdas de la matriz. Son funcionalmente idénticos a los `MakeRotation` métodos y, `MakeRotationDegrees` salvo que no crean instancias del `SKMatrix` valor.

Supongamos que tiene un `SKPath` objeto que desea mostrar, pero prefiere que tenga una orientación algo diferente o un punto central diferente. Puede modificar todas las coordenadas de esa ruta de acceso llamando al [`Transform`](xref:SkiaSharp.SKPath.Transform(SkiaSharp.SKMatrix)) método de `SKPath` con un `SKMatrix` argumento. En la página transformación de la **ruta de acceso** se muestra cómo hacerlo. La [`PathTransform`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/PathTransformPage.cs) clase hace referencia al `HendecagramPath` objeto en un campo, pero utiliza su constructor para aplicar una transformación a esa ruta de acceso:

```csharp
public class PathTransformPage : ContentPage
{
    SKPath transformedPath = HendecagramArrayPage.HendecagramPath;

    public PathTransformPage()
    {
        Title = "Path Transform";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        SKMatrix matrix = SKMatrix.MakeScale(3, 3);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeRotationDegrees(360f / 22));
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(300, 300));

        transformedPath.Transform(matrix);
    }
    ...
}
```

El `HendecagramPath` objeto tiene un centro en (0,0) y los 11 puntos de la estrella se extienden hacia afuera desde ese centro en 100 unidades en todas las direcciones. Esto significa que la ruta de acceso tiene coordenadas positivas y negativas. La página de transformación de la **ruta de acceso** prefiere trabajar con una estrella tres veces como grande y con todas las coordenadas positivas. Además, no desea que un punto de la estrella apunte directamente hacia arriba. En su lugar, desea que un punto de la estrella señale hacia abajo. (Dado que la estrella tiene 11 puntos, no puede tener ambos). Esto requiere rotar la estrella en 360 grados entre 22.

El constructor crea un `SKMatrix` objeto a partir de tres transformaciones independientes mediante el `PostConcat` método con el siguiente patrón, donde a, B y C son instancias de `SKMatrix` :

```csharp
SKMatrix matrix = A;
SKMatrix.PostConcat(ref A, B);
SKMatrix.PostConcat(ref A, C);
```

Se trata de una serie de multiplicaciones sucesivas, por lo que el resultado es el siguiente:

`A × B × C`

Las multiplicaciones consecutivas ayudan a comprender lo que hace cada transformación. La transformación de escala aumenta el tamaño de las coordenadas de la ruta de acceso en un factor de 3, por lo que las coordenadas van de – 300 a 300. La transformación girar gira la estrella alrededor de su origen. A continuación, la transformación traducir la desplaza por 300 píxeles a la derecha y hacia abajo, por lo que todas las coordenadas se vuelven positivas.

Hay otras secuencias que producen la misma matriz. He aquí otro:

```csharp
SKMatrix matrix = SKMatrix.MakeRotationDegrees(360f / 22);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(100, 100));
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(3, 3));
```

Esto gira el trazado en torno a su centro primero y, a continuación, lo traduce 100 píxeles a la derecha y hacia abajo para que todas las coordenadas sean positivas. A continuación, la estrella aumenta de tamaño en relación con su nueva esquina superior izquierda, que es el punto (0,0).

El `PaintSurface` controlador puede simplemente representar esta ruta de acceso:

```csharp
public class PathTransformPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Magenta;
            paint.StrokeWidth = 5;

            canvas.DrawPath(transformedPath, paint);
        }
    }
}

```

Aparece en la esquina superior izquierda del lienzo:

[![](matrix-images/pathtransform-small.png "Triple screenshot of the Path Transform page")](matrix-images/pathtransform-large.png#lightbox "Triple screenshot of the Path Transform page")

El constructor de este programa aplica la matriz a la ruta de acceso con la siguiente llamada:

```csharp
transformedPath.Transform(matrix);
```

La ruta de acceso *no* conserva esta matriz como una propiedad. En su lugar, aplica la transformación a todas las coordenadas de la ruta de acceso. Si `Transform` se vuelve a llamar a, se vuelve a aplicar la transformación y la única manera de volver es aplicar otra matriz que deshaga la transformación. Afortunadamente, la `SKMatrix` estructura define un [`TryInvert`](xref:SkiaSharp.SKMatrix.TryInvert*) método que obtiene la matriz que invierte una matriz determinada:

```csharp
SKMatrix inverse;
bool success = matrix.TryInverse(out inverse);
```

Se llama al método `TryInverse` porque no todas las matrices se pueden invertir, pero es probable que no se use una matriz no invertida para una transformación de gráficos.

También puede aplicar una transformación de matriz a un `SKPoint` valor, una matriz de puntos, un `SKRect` , o incluso un solo número dentro del programa. La `SKMatrix` estructura admite estas operaciones con una colección de métodos que comienzan con la palabra, como las siguientes `Map` :

```csharp
SKPoint transformedPoint = matrix.MapPoint(point);

SKPoint transformedPoint = matrix.MapPoint(x, y);

SKPoint[] transformedPoints = matrix.MapPoints(pointArray);

float transformedValue = matrix.MapRadius(floatValue);

SKRect transformedRect = matrix.MapRect(rect);
```

Si usa ese último método, tenga en cuenta que la `SKRect` estructura no es capaz de representar un rectángulo girado. El método solo tiene sentido para un `SKMatrix` valor que representa la traslación y el escalado.

## <a name="interactive-experimentation"></a>Experimentación interactiva

Una manera de hacerse una idea de la transformación afín es mover interactivamente tres esquinas de un mapa de bits en la pantalla y ver los resultados de la transformación. Esta es la idea subyacente de la página **Mostrar matriz afín** . Esta página requiere otras dos clases que también se usan en otras demostraciones:

La [`TouchPoint`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchPoint.cs) clase muestra un círculo translúcido que se puede arrastrar alrededor de la pantalla. `TouchPoint`requiere que un `SKCanvasView` elemento o que sea un elemento primario de `SKCanvasView` tenga el objeto [`TouchEffect`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchEffect.cs) adjunto. Establezca la propiedad `Capture` en `true`. En el `TouchAction` controlador de eventos, el programa debe llamar al `ProcessTouchEvent` método en `TouchPoint` para `TouchPoint` cada instancia. El método devuelve `true` si el evento Touch dio como resultado el movimiento del punto de toque. Además, el `PaintSurface` controlador debe llamar al `Paint` método en cada `TouchPoint` instancia, pasándole el `SKCanvas` objeto.

`TouchPoint`muestra una manera común de encapsular un visual SkiaSharp en una clase independiente. La clase puede definir propiedades para especificar las características del visual y un método denominado `Paint` con un `SKCanvas` argumento puede representarla.

La `Center` propiedad de `TouchPoint` indica la ubicación del objeto. Esta propiedad se puede establecer para inicializar la ubicación; la propiedad cambia cuando el usuario arrastra el círculo alrededor del lienzo.

La **Página Mostrar matriz afín** también requiere la [`MatrixDisplay`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/MatrixDisplay.cs) clase. Esta clase muestra las celdas de un `SKMatrix` objeto. Tiene dos métodos públicos: `Measure` para obtener las dimensiones de la matriz representada y `Paint` para mostrarla. La clase contiene una `MatrixPaint` propiedad de tipo `SKPaint` que se puede reemplazar por un color o tamaño de fuente diferente.

El archivo [**ShowAffineMatrixPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml) crea una instancia de `SKCanvasView` y adjunta un `TouchEffect` . El archivo de código subyacente [**ShowAffineMatrixPage.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs) crea tres `TouchPoint` objetos y los establece en posiciones correspondientes a tres esquinas de un mapa de bits que se cargan desde un recurso incrustado:

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    SKMatrix matrix;
    SKBitmap bitmap;
    SKSize bitmapSize;

    TouchPoint[] touchPoints = new TouchPoint[3];

    MatrixDisplay matrixDisplay = new MatrixDisplay();

    public ShowAffineMatrixPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }

        touchPoints[0] = new TouchPoint(100, 100);                  // upper-left corner
        touchPoints[1] = new TouchPoint(bitmap.Width + 100, 100);   // upper-right corner
        touchPoints[2] = new TouchPoint(100, bitmap.Height + 100);  // lower-left corner

        bitmapSize = new SKSize(bitmap.Width, bitmap.Height);
        matrix = ComputeMatrix(bitmapSize, touchPoints[0].Center,
                                           touchPoints[1].Center,
                                           touchPoints[2].Center);
    }
    ...
}
```

Una matriz afín se define de forma única en tres puntos. Los tres `TouchPoint` objetos corresponden a las esquinas superior izquierda, superior derecha e inferior izquierda del mapa de bits. Dado que una matriz afín solo es capaz de transformar un rectángulo en un paralelogramo, el cuarto punto está implícito entre los otros tres. El constructor finaliza con una llamada a `ComputeMatrix` , que calcula las celdas de un `SKMatrix` objeto a partir de estos tres puntos.

El `TouchAction` controlador llama al `ProcessTouchEvent` método de cada `TouchPoint` . El `scale` valor convierte de Xamarin.Forms coordenadas a píxeles:

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        bool touchPointMoved = false;

        foreach (TouchPoint touchPoint in touchPoints)
        {
            float scale = canvasView.CanvasSize.Width / (float)canvasView.Width;
            SKPoint point = new SKPoint(scale * (float)args.Location.X,
                                        scale * (float)args.Location.Y);
            touchPointMoved |= touchPoint.ProcessTouchEvent(args.Id, args.Type, point);
        }

        if (touchPointMoved)
        {
            matrix = ComputeMatrix(bitmapSize, touchPoints[0].Center,
                                               touchPoints[1].Center,
                                               touchPoints[2].Center);
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

Si `TouchPoint` se ha despasado alguno, el método llama de `ComputeMatrix` nuevo y invalida la superficie.

El `ComputeMatrix` método determina la matriz implícita de esos tres puntos. La matriz denominada transforma `A` un rectángulo cuadrado de un píxel en un paralelogramo en función de los tres puntos, mientras que la transformación de escalado llama `S` escala el mapa de bits a un rectángulo cuadrado de un píxel. La matriz compuesta es `S` × `A` :

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    static SKMatrix ComputeMatrix(SKSize size, SKPoint ptUL, SKPoint ptUR, SKPoint ptLL)
    {
        // Scale transform
        SKMatrix S = SKMatrix.MakeScale(1 / size.Width, 1 / size.Height);

        // Affine transform
        SKMatrix A = new SKMatrix
        {
            ScaleX = ptUR.X - ptUL.X,
            SkewY = ptUR.Y - ptUL.Y,
            SkewX = ptLL.X - ptUL.X,
            ScaleY = ptLL.Y - ptUL.Y,
            TransX = ptUL.X,
            TransY = ptUL.Y,
            Persp2 = 1
        };

        SKMatrix result = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref result, A, S);
        return result;
    }
    ...
}
```

Por último, el `PaintSurface` método representa el mapa de bits basado en esa matriz, muestra la matriz en la parte inferior de la pantalla y representa los puntos táctiles en las tres esquinas del mapa de bits:

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap using the matrix
        canvas.Save();
        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, 0, 0);
        canvas.Restore();

        // Display the matrix in the lower-right corner
        SKSize matrixSize = matrixDisplay.Measure(matrix);

        matrixDisplay.Paint(canvas, matrix,
            new SKPoint(info.Width - matrixSize.Width,
                        info.Height - matrixSize.Height));

        // Display the touchpoints
        foreach (TouchPoint touchPoint in touchPoints)
        {
            touchPoint.Paint(canvas);
        }
    }
  }
```

En la pantalla de iOS siguiente se muestra el mapa de bits cuando la página se carga por primera vez, mientras que las otras dos pantallas la muestran después de alguna manipulación:

[![](matrix-images/showaffinematrix-small.png "Triple screenshot of the Show Affine Matrix page")](matrix-images/showaffinematrix-large.png#lightbox "Triple screenshot of the Show Affine Matrix page")

Aunque parezca que los puntos táctiles arrastran las esquinas del mapa de bits, eso es solo una ilusión. La matriz calculada a partir de los puntos táctiles transforma el mapa de bits para que las esquinas coincidan con los puntos de toque.

Es más natural para los usuarios mover, cambiar el tamaño y girar los mapas de bits no arrastrando las esquinas, sino que usa uno o dos dedos directamente en el objeto para arrastrar, reducir y girar. Esto se trata en el siguiente artículo de [**manipulación táctil**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md).

## <a name="the-reason-for-the-3-by-3-matrix"></a>La razón de la matriz de 3 por 3

Podría esperar que un sistema de gráficos bidimensional solo requiera una matriz de transformación de 2 por 2:

<pre>
           │ ScaleX  SkewY  │
| x  y | × │                │ = | x'  y' |
           │ SkewX   ScaleY │
</pre>

Esto funciona para la escala, la rotación e incluso el sesgo, pero no es capaz de las transformaciones más básicas, que es la traducción.

El problema es que la matriz de 2 por 2 representa una transformación *lineal* en dos dimensiones. Una transformación lineal conserva algunas operaciones aritméticas básicas, pero una de las implicaciones es que una transformación lineal nunca modifica el punto (0,0). Una transformación lineal hace que la traducción sea imposible.

En tres dimensiones, una matriz de transformación lineal tiene el siguiente aspecto:

<pre>
              │ ScaleX  SkewYX  SkewZX │
| x  y  z | × │ SkewXY  ScaleY  SkewZY │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ  ScaleZ │
</pre>

La celda etiquetada `SkewXY` significa que el valor sesga la coordenada x en función de los valores de y; la celda `SkewXZ` significa que el valor sesga la coordenada x en función de los valores de Z; y los valores sesgan de forma similar para las demás `Skew` celdas.

Es posible restringir esta matriz de transformación 3D a un plano bidimensional estableciendo `SkewZX` y `SkewZY` en 0, y `ScaleZ` en 1:

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  z | × │ SkewXY  ScaleY   0 │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ   1 │
</pre>

Si los gráficos bidimensionales se dibujan completamente en el plano en el espacio 3D donde Z es igual a 1, la multiplicación de transformación tiene el siguiente aspecto:

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  1 | × │ SkewXY  ScaleY   0 │ = | x'  y'  1 |
              │ SkewXZ  SkewYZ   1 │
</pre>

Todo permanece en el plano bidimensional, donde Z es igual a 1, pero `SkewXZ` las `SkewYZ` celdas y se convierten en factores de conversión bidimensionales.

Esta es la forma en que una transformación lineal tridimensional actúa como una transformación no lineal bidimensional. (Por analogía, las transformaciones en gráficos 3D se basan en una matriz de 4 por 4).

La `SKMatrix` estructura de SkiaSharp define las propiedades de esa tercera fila:

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z` |
              │ TransX  TransY  Persp2 │
</pre>

Los valores distintos de cero de `Persp0` y `Persp1` producen transformaciones que mueven objetos fuera del plano bidimensional, donde Z es igual a 1. Lo que sucede cuando los objetos se devuelven a ese plano se trata en el artículo sobre las [**transformaciones no afines**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md).

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
