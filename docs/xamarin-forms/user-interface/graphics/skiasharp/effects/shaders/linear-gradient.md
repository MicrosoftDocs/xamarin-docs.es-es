---
title: Degradado lineal de SkiaSharp
description: Descubra cómo trazar líneas o áreas de relleno con degradados se componen de una mezcla gradual de dos colores.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 20A2A8C4-FEB7-478D-BF57-C92E26117B6A
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 290e533e54b2ee150b94d9fb6b0f5119324f9cf0
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/07/2020
ms.locfileid: "78916380"
---
# <a name="the-skiasharp-linear-gradient"></a>Degradado lineal de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

La clase [`SKPaint`](xref:SkiaSharp.SKPaint) define una propiedad [`Color`](xref:SkiaSharp.SKPaint.Color) que se usa para trazar líneas o rellenar áreas con un color sólido. También puede trazar líneas o rellenar áreas con _degradados_, que son mezclas graduales de colores:

![Ejemplo de degradado lineal](linear-gradient-images/LinearGradientSample.png "Ejemplo de degradado lineal")

El tipo más básico de degradado es un degradado _lineal_ . La combinación de colores se produce en una línea (denominada _línea de degradado_) de un punto a otro. Las líneas que son perpendiculares a la línea de degradado tienen el mismo color. Puede crear un degradado lineal mediante uno de los dos métodos de [`SKShader.CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient*) estáticos. La diferencia entre las dos sobrecargas es que uno incluye una transformación de matriz y el otro no. 

Estos métodos devuelven un objeto de tipo [`SKShader`](xref:SkiaSharp.SKShader) que se establece en la propiedad [`Shader`](xref:SkiaSharp.SKPaint.Shader) de `SKPaint`. Si la propiedad `Shader` no es null, invalida la propiedad `Color`. Cualquier línea que esté contorneada o cualquier área que se rellene con este `SKPaint` objeto se basa en el degradado, en lugar del color sólido.

> [!NOTE]
> La propiedad `Shader` se omite cuando se incluye un objeto `SKPaint` en una llamada a `DrawBitmap`. Puede usar la propiedad `Color` de `SKPaint` para establecer un nivel de transparencia para mostrar un mapa de bits (como se describe en el artículo [Mostrar mapas de bits de SkiaSharp](../../bitmaps/displaying.md#displaying-in-pixel-dimensions)), pero no puede usar la propiedad `Shader` para mostrar un mapa de bits con una transparencia de degradado. Existen otras técnicas para mostrar mapas de bits con transparencias de degradado: estas se describen en los artículos [SkiaSharp gradientes circulares](circular-gradients.md#radial-gradients-for-masking) y [SkiaSharp composiciones and Blend modes](../blend-modes/porter-duff.md#gradient-transparency-and-transitions).

## <a name="corner-to-corner-gradients"></a>Esquina a esquina degradados

A menudo un degradado lineal se extiende desde una de las esquinas de un rectángulo a otro. Si el punto de inicio es la esquina superior izquierda del rectángulo, puede ampliar el degradado:

- verticalmente en la esquina inferior izquierda
- horizontalmente a la esquina superior derecha
- diagonalmente en la esquina inferior derecha

El degradado lineal diagonal se muestra en la primera página de la sección **sombreadores SkiaSharp y otros efectos** del ejemplo [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . La página de **degradado de esquina a esquina** crea un `SKCanvasView` en su constructor. El controlador de `PaintSurface` crea un objeto `SKPaint` en una instrucción `using` y, a continuación, define un rectángulo cuadrado de 300 píxeles centrado en el lienzo:

```csharp
public class CornerToCornerGradientPage : ContentPage
{
    ···
    public CornerToCornerGradientPage ()
    {
        Title = "Corner-to-Corner Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
        ···
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Create 300-pixel square centered rectangle
            float x = (info.Width - 300) / 2;
            float y = (info.Height - 300) / 2;
            SKRect rect = new SKRect(x, y, x + 300, y + 300);

            // Create linear gradient from upper-left to lower-right
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Left, rect.Top),
                                new SKPoint(rect.Right, rect.Bottom),
                                new SKColor[] { SKColors.Red, SKColors.Blue },
                                new float[] { 0, 1 },
                                SKShaderTileMode.Repeat);

            // Draw the gradient on the rectangle
            canvas.DrawRect(rect, paint);
            ···
        }
    }
}
```

A la propiedad `Shader` de `SKPaint` se le asigna el valor devuelto `SKShader` del método `SKShader.CreateLinearGradient` estático. Los cinco argumentos son los siguientes:

- El punto inicial del degradado, se establece aquí en la esquina superior izquierda del rectángulo
- El punto final del degradado, se establece aquí en la esquina inferior derecha del rectángulo
- Una matriz de dos o más colores que contribuyen al degradado
- Matriz de valores de `float` que indican la posición relativa de los colores dentro de la línea de degradado.
- Miembro de la enumeración [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) que indica cómo se comporta el degradado más allá de los extremos de la línea de degradado

Una vez creado el objeto de degradado, el método `DrawRect` dibuja el rectángulo cuadrado de 300 píxeles usando el objeto `SKPaint` que incluye el sombreador. Aquí se ejecuta en iOS, Android y la plataforma Universal de Windows (UWP):

[![Degradado de esquina a esquina](linear-gradient-images/CornerToCornerGradient.png "Degradado de esquina a esquina")](linear-gradient-images/CornerToCornerGradient-Large.png#lightbox)

La línea de degradado se define mediante los dos puntos especificados como los dos primeros argumentos. Tenga en cuenta que estos puntos son relativos al _lienzo_ y _no_ al objeto gráfico que se muestra con el degradado. A lo largo de la línea de degradado, el color gradualmente, pasa de color rojo en la esquina superior izquierda en azul en la esquina inferior derecha. Cualquier línea perpendicular a la línea de degradado tiene un color constante.

La matriz de `float` valores especificados como cuarto argumento tiene una correspondencia uno a uno con la matriz de colores. Los valores indican la posición relativa de la línea de degradado que se producen esos colores. En este caso, el valor de 0 significa que `Red` se produce al principio de la línea de degradado y 1 significa que `Blue` se produce al final de la línea. Los números deben ser ascendente y deben estar en el intervalo de 0 a 1. Si no están en ese intervalo, se ajustará para que esté en ese intervalo.

Los dos valores de la matriz se pueden establecer en algo distinto de 0 y 1. Pruebe esto:

```csharp
new float[] { 0.25f, 0.75f }
```

Ahora el completamente primer trimestre de la línea de degradado es rojo puro, y el último trimestre es azul puro. La combinación de rojo y azul está restringida a la mitad de la línea de degradado central.

Por lo general, deseará espacio estos valores de posición igualmente de 0 a 1. En ese caso, puede simplemente proporcionar `null` como el cuarto argumento a `CreateLinearGradient`.

Aunque este degradado se define entre dos vértices del rectángulo de 300 píxeles cuadrado, no se restringen a rellenar el rectángulo. La página de **degradado de esquina a esquina** incluye algún código adicional que responda a los grifos o a los clics del mouse en la página. El campo `drawBackground` se alterna entre `true` y `false` cada vez que se pulsa. Si el valor es `true`, el controlador de `PaintSurface` utiliza el mismo objeto `SKPaint` para rellenar todo el lienzo y, a continuación, dibuja un rectángulo negro que indica el rectángulo más pequeño: 

```csharp
public class CornerToCornerGradientPage : ContentPage
{
    bool drawBackground;

    public CornerToCornerGradientPage ()
    {
        ···
        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            drawBackground ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPaint paint = new SKPaint())
        {
            ···
            if (drawBackground)
            {
                // Draw the gradient on the whole canvas
                canvas.DrawRect(info.Rect, paint);

                // Outline the smaller rectangle
                paint.Shader = null;
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                canvas.DrawRect(rect, paint);
            }
        }
    }
}
```

Aquí es lo que verá después de puntear en la pantalla:

[![Degradado de esquina a esquina lleno](linear-gradient-images/CornerToCornerGradientFull.png "Degradado de esquina a esquina lleno")](linear-gradient-images/CornerToCornerGradientFull-Large.png#lightbox)

Tenga en cuenta que el degradado se repite en el mismo patrón más allá de los puntos que definen la línea de degradado. Esta repetición se produce porque el último argumento que se va a `CreateLinearGradient` es `SKShaderTileMode.Repeat`. (Verá las demás opciones en breve.)

Tenga en cuenta que los puntos que se usa para especificar la línea de degradado no son únicos. Las líneas que son perpendiculares a la línea de degradado tienen el mismo color, por lo que hay un número infinito de líneas de degradado que se pueden especificar para el mismo efecto. Por ejemplo, al rellenar un rectángulo con un degradado horizontal, puede especificar las esquinas superior izquierda y derecha, o las esquinas inferior izquierda e inferior derecha o son paralelas a esas líneas e incluso con dos puntos.

## <a name="interactively-experiment"></a>Experimente de forma interactiva

Puede experimentar de forma interactiva con degradados lineales con la página de **degradado lineal interactiva** . En esta página se usa la clase `InteractivePage` presentada en el artículo [**tres maneras de dibujar un arco**](../../curves/arcs.md). `InteractivePage` controla los eventos de [`TouchEffect`](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md) para mantener una colección de objetos `TouchPoint` que se pueden desplace con los dedos o con el mouse.

El archivo XAML asocia el `TouchEffect` a un elemento primario del `SKCanvasView` y también incluye un `Picker` que le permite seleccionar uno de los tres miembros de la enumeración [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) :

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
                       xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Effects.InteractiveLinearGradientPage"
                       Title="Interactive Linear Gradient">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid BackgroundColor="White"
              Grid.Row="0">
            <skiaforms:SKCanvasView x:Name="canvasView"
                                    PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>

        <Picker x:Name="tileModePicker" 
                Grid.Row="1"
                Title="Shader Tile Mode" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKShaderTileMode}">
                    <x:Static Member="skia:SKShaderTileMode.Clamp" />
                    <x:Static Member="skia:SKShaderTileMode.Repeat" />
                    <x:Static Member="skia:SKShaderTileMode.Mirror" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</local:InteractivePage>
```

El constructor del archivo de código subyacente crea dos objetos `TouchPoint` para los puntos inicial y final del degradado lineal. El controlador de `PaintSurface` define una matriz de tres colores (para un degradado de rojo a verde y azul) y obtiene el `SKShaderTileMode` actual de la `Picker`:

```csharp
public partial class InteractiveLinearGradientPage : InteractivePage
{
    public InteractiveLinearGradientPage ()
    {
        InitializeComponent ();

        touchPoints = new TouchPoint[2];

        for (int i = 0; i < 2; i++)
        { 
            touchPoints[i] = new TouchPoint
            {
                Center = new SKPoint(100 + i * 200, 100 + i * 200)
            };
        }

        InitializeComponent();
        baseCanvasView = canvasView;
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKColor[] colors = { SKColors.Red, SKColors.Green, SKColors.Blue };
        SKShaderTileMode tileMode =
            (SKShaderTileMode)(tileModePicker.SelectedIndex == -1 ?
                                        0 : tileModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateLinearGradient(touchPoints[0].Center,
                                                         touchPoints[1].Center,
                                                         colors,
                                                         null,
                                                         tileMode);
            canvas.DrawRect(info.Rect, paint);
        }
        ···
    }
}
```

El controlador de `PaintSurface` crea el objeto de `SKShader` a partir de toda la información y lo utiliza para colorear todo el lienzo. La matriz de valores de `float` se establece en `null`. En caso contrario, para igualmente el espacio de tres colores, establecería ese parámetro en una matriz con los valores 0, 0,5 y 1.

La mayor parte del controlador de `PaintSurface` está dedicada a mostrar varios objetos: los puntos táctiles como círculos de contorno, la línea de degradado y las líneas perpendiculares a las líneas de degradado en los puntos táctiles:

```csharp
public partial class InteractiveLinearGradientPage : InteractivePage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Display the touch points here rather than by TouchPoint
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 3;

            foreach (TouchPoint touchPoint in touchPoints)
            {
                canvas.DrawCircle(touchPoint.Center, touchPoint.Radius, paint);
            }

            // Draw gradient line connecting touchpoints
            canvas.DrawLine(touchPoints[0].Center, touchPoints[1].Center, paint);

            // Draw lines perpendicular to the gradient line
            SKPoint vector = touchPoints[1].Center - touchPoints[0].Center;
            float length = (float)Math.Sqrt(Math.Pow(vector.X, 2) +
                                            Math.Pow(vector.Y, 2));
            vector.X /= length;
            vector.Y /= length;
            SKPoint rotate90 = new SKPoint(-vector.Y, vector.X);
            rotate90.X *= 200;
            rotate90.Y *= 200;

            canvas.DrawLine(touchPoints[0].Center, 
                            touchPoints[0].Center + rotate90, 
                            paint);

            canvas.DrawLine(touchPoints[0].Center,
                            touchPoints[0].Center - rotate90,
                            paint);

            canvas.DrawLine(touchPoints[1].Center,
                            touchPoints[1].Center + rotate90,
                            paint);

            canvas.DrawLine(touchPoints[1].Center,
                            touchPoints[1].Center - rotate90,
                            paint);
        }
    }
}
```

Es fácil dibujar la línea de degradado que se conecta la época de dos, pero las líneas perpendiculares requieren algo más de trabajo. La línea de degradado es puede convertir en un vector, normalizan para tener una longitud de una unidad y, a continuación, girar 90 grados. A continuación, ese vector tiene una longitud de 200 píxeles. Sirve para dibujar las cuatro líneas que se extienden desde los puntos de toque a ser perpendicular a la línea de degradado.

Las líneas perpendiculares coinciden con el principio y al final del degradado. Lo que sucede más allá de esas líneas depende de la configuración de la enumeración `SKShaderTileMode`:

[![Degradado lineal interactivo](linear-gradient-images/InteractiveLinearGradient.png "Degradado lineal interactivo")](linear-gradient-images/InteractiveLinearGradient-Large.png#lightbox)

Las tres capturas de pantallas muestran los resultados de los tres valores diferentes de [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode). La captura de pantalla de iOS muestra `SKShaderTileMode.Clamp`, que solo extiende los colores del borde del degradado. La opción `SKShaderTileMode.Repeat` de la captura de pantalla de Android muestra cómo se repite el patrón de degradado. La opción `SKShaderTileMode.Mirror` de la captura de pantalla de UWP también repite el patrón, pero el patrón se invierte cada vez, lo que da lugar a que no haya continuidad de color.

## <a name="gradients-on-gradients"></a>Degradados en degradados

La clase `SKShader` no define propiedades ni métodos públicos, salvo `Dispose`. Por lo tanto, los objetos `SKShader` creados por sus métodos estáticos son inmutables. Incluso si usa el mismo degradado para dos objetos diferentes, es probable que desee variar ligeramente el degradado. Para ello, deberá crear un nuevo objeto de `SKShader`.

La página de **texto de degradado** muestra texto y un segundo plano de colores con degradados similares:

[![Texto degradado](linear-gradient-images/GradientText.png "Texto degradado")](linear-gradient-images/GradientText-Large.png#lightbox)

Las únicas diferencias en los degradados son los puntos inicial y final. Degradado que se utiliza para mostrar el texto se basa en dos puntos en las esquinas del rectángulo delimitador para el texto. Para el fondo, los dos puntos se basan en todo el lienzo. Este es el código:

```csharp
public class GradientTextPage : ContentPage
{
    const string TEXT = "GRADIENT";

    public GradientTextPage ()
    {
        Title = "Gradient Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Create gradient for background
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, 0),
                                new SKPoint(info.Width, info.Height),
                                new SKColor[] { new SKColor(0x40, 0x40, 0x40),
                                                new SKColor(0xC0, 0xC0, 0xC0) },
                                null,
                                SKShaderTileMode.Clamp);

            // Draw background
            canvas.DrawRect(info.Rect, paint);

            // Set TextSize to fill 90% of width
            paint.TextSize = 100;
            float width = paint.MeasureText(TEXT);
            float scale = 0.9f * info.Width / width;
            paint.TextSize *= scale;

            // Get text bounds
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Calculate offsets to center the text on the screen
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            // Shift textBounds by that amount
            textBounds.Offset(xText, yText);

            // Create gradient for text
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(textBounds.Left, textBounds.Top),
                                new SKPoint(textBounds.Right, textBounds.Bottom),
                                new SKColor[] { new SKColor(0x40, 0x40, 0x40),
                                                new SKColor(0xC0, 0xC0, 0xC0) },
                                null,
                                SKShaderTileMode.Clamp);

            // Draw text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

La propiedad `Shader` del objeto `SKPaint` se establece primero para mostrar un degradado para cubrir el fondo. Los puntos de degradado se establecen en las esquinas superior izquierda e inferior derecha del lienzo.

El código establece la propiedad `TextSize` del objeto `SKPaint` de modo que el texto se muestre en el 90% del ancho del lienzo. Los límites de texto se utilizan para calcular `xText` y `yText` valores que se van a pasar al método `DrawText` para centrar el texto.

Sin embargo, los puntos de degradado para la segunda llamada `CreateLinearGradient` deben hacer referencia a la esquina superior izquierda e inferior derecha del texto en relación con el lienzo cuando se muestra. Esto se consigue desplazando el rectángulo `textBounds` por los mismos valores de `xText` y `yText`:

```csharp
textBounds.Offset(xText, yText);
```

Ahora las esquinas superior izquierda e inferior derecha del rectángulo se pueden usar para establecer los puntos inicial y final del degradado.

## <a name="animating-a-gradient"></a>Animar un degradado

Hay varias maneras de animar un degradado. Un enfoque consiste en animar los puntos inicial y final. La página de **animación de degradado** mueve los dos puntos en un círculo que está centrado en el lienzo. El radio de este círculo es la mitad del ancho o alto del lienzo, lo que sea menor. Los puntos inicial y final son opuestos entre sí en este círculo y el degradado va de blanco a negro con un modo de mosaico `Mirror`:

[![Animación de degradado](linear-gradient-images/GradientAnimation.png "Animación de degradado")](linear-gradient-images/GradientAnimation-Large.png#lightbox)

El constructor crea el `SKCanvasView`. Los métodos `OnAppearing` y `OnDisappearing` controlan la lógica de animación:

```csharp
public class GradientAnimationPage : ContentPage
{
    SKCanvasView canvasView;
    bool isAnimating;
    double angle;
    Stopwatch stopwatch = new Stopwatch();

    public GradientAnimationPage()
    {
        Title = "Gradient Animation";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 3000;
        angle = 2 * Math.PI * (stopwatch.ElapsedMilliseconds % duration) / duration;
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

El método `OnTimerTick` calcula un valor `angle` que se anima desde 0 a 2π cada 3 segundos. 

Aquí es una manera de calcular los dos puntos de degradado. Un valor `SKPoint` denominado `vector` se calcula para extender desde el centro del lienzo hasta un punto en el radio del círculo. La dirección de este vector se basa en los valores de seno y coseno del ángulo. A continuación, se calculan los dos puntos de degradado opuestos: un punto se calcula restando dicho vector desde el punto central, y otro punto se calcula sumando el vector en el punto central:

```csharp
public class GradientAnimationPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
            int radius = Math.Min(info.Width, info.Height) / 2;
            SKPoint vector = new SKPoint((float)(radius * Math.Cos(angle)),
                                         (float)(radius * Math.Sin(angle)));

            paint.Shader = SKShader.CreateLinearGradient(
                                center - vector,
                                center + vector,
                                new SKColor[] { SKColors.White, SKColors.Black },
                                null,
                                SKShaderTileMode.Mirror);

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Un enfoque un poco diferente requiere menos código. Este enfoque usa el método de sobrecarga [`SKShader.CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) con una transformación de matriz como último argumento. Este enfoque es la versión en el ejemplo [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) :

```csharp
public class GradientAnimationPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, 0),
                                info.Width < info.Height ? new SKPoint(info.Width, 0) : 
                                                           new SKPoint(0, info.Height),
                                new SKColor[] { SKColors.White, SKColors.Black },
                                new float[] { 0, 1 },
                                SKShaderTileMode.Mirror,
                                SKMatrix.MakeRotation((float)angle, info.Rect.MidX, info.Rect.MidY));

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Si el ancho del lienzo es menor que el alto, los dos puntos de degradado se establecen en (0,0) y (`info.Width`, 0). La transformación de giro pasada como el último argumento de `CreateLinearGradient` gira realmente los dos puntos alrededor del centro de la pantalla.

Tenga en cuenta que si el ángulo es 0, no hay ningún giro y los dos puntos de degradado son las esquinas superior izquierda y derecha del lienzo. Esos puntos no son los mismos puntos de degradado calculados como se muestra en la llamada a `CreateLinearGradient` anterior. Pero estos puntos son _paralelos_ a la línea de degradado horizontal que disecciona el centro del lienzo y dan como resultado un degradado idéntico.

**Degradado de arco iris**

La página **degradado de arco iris** dibuja un arco iris desde la esquina superior izquierda del lienzo hasta la esquina inferior derecha. Pero este degradado arco iris no es como un arco iris real. Es directamente en lugar de curvados, pero se basa en ocho colores HSL (matiz-saturación-luminosidad) que vienen determinados por recorrer los valores de matiz de 0 a 360:

```csharp
SKColor[] colors = new SKColor[8];

for (int i = 0; i < colors.Length; i++)
{
    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
}
```

Ese código forma parte del controlador de `PaintSurface` que se muestra a continuación. El controlador comienza creando una ruta de acceso que define un polígono de seis lados que se extiende desde la esquina superior izquierda del lienzo a la esquina inferior derecha:

```csharp
public class RainbowGradientPage : ContentPage
{
    public RainbowGradientPage ()
    {
        Title = "Rainbow Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath path = new SKPath())
        {
            float rainbowWidth = Math.Min(info.Width, info.Height) / 2f;

            // Create path from upper-left to lower-right corner
            path.MoveTo(0, 0);
            path.LineTo(rainbowWidth / 2, 0);
            path.LineTo(info.Width, info.Height - rainbowWidth / 2);
            path.LineTo(info.Width, info.Height);
            path.LineTo(info.Width - rainbowWidth / 2, info.Height);
            path.LineTo(0, rainbowWidth / 2);
            path.Close();

            using (SKPaint paint = new SKPaint())
            {
                SKColor[] colors = new SKColor[8];

                for (int i = 0; i < colors.Length; i++)
                {
                    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
                }

                paint.Shader = SKShader.CreateLinearGradient(
                                    new SKPoint(0, rainbowWidth / 2), 
                                    new SKPoint(rainbowWidth / 2, 0),
                                    colors,
                                    null,
                                    SKShaderTileMode.Repeat);

                canvas.DrawPath(path, paint);
            }
        }
    }
}
```

Los dos puntos de degradado del método `CreateLinearGradient` se basan en dos de los puntos que definen esta ruta de acceso: ambos puntos están cerca de la esquina superior izquierda. La primera consiste en el borde superior del lienzo y el segundo es en el borde izquierdo del lienzo. Este es el resultado:

[![Error de degradado de arco iris](linear-gradient-images/RainbowGradientFaulty.png "Error de degradado de arco iris")](linear-gradient-images/RainbowGradientFaulty-Large.png#lightbox)

Se trata de una imagen interesante, pero no resulta bastante la intención. El problema es que, cuando se crea un degradado lineal, las líneas de color constante son perpendiculares a la línea de degradado. La línea de degradado se basa en los puntos donde la figura toca los lados superiores e izquierdos, y esa línea generalmente no es perpendicular a los bordes de la ilustración que se extienden a la esquina inferior derecha. Este enfoque podría funcionar sólo si el lienzo fuera cuadrado.

Para crear un degradado arco iris adecuado, la línea de degradado debe ser perpendicular al borde del arco iris. Es un cálculo más complejo. Debe definirse un vector que sea paralelo en el lado largo de la figura. El vector es gira 90 grados por lo que es perpendicular a ese lado. A continuación, se alarga para que sea el ancho de la figura multiplicando por `rainbowWidth`. Se calculan según los dos puntos de degradado en un punto en el lateral de la ilustración, y que además del vector de punto. Este es el código que aparece en la página de **degradado de arco iris** en el ejemplo [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) :

```csharp
public class RainbowGradientPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPath path = new SKPath())
        {
            ···
            using (SKPaint paint = new SKPaint())
            {
                ···
                // Vector on lower-left edge, from top to bottom 
                SKPoint edgeVector = new SKPoint(info.Width - rainbowWidth / 2, info.Height) - 
                                     new SKPoint(0, rainbowWidth / 2);

                // Rotate 90 degrees counter-clockwise:
                SKPoint gradientVector = new SKPoint(edgeVector.Y, -edgeVector.X);

                // Normalize
                float length = (float)Math.Sqrt(Math.Pow(gradientVector.X, 2) +
                                                Math.Pow(gradientVector.Y, 2));
                gradientVector.X /= length;
                gradientVector.Y /= length;

                // Make it the width of the rainbow
                gradientVector.X *= rainbowWidth;
                gradientVector.Y *= rainbowWidth;

                // Calculate the two points
                SKPoint point1 = new SKPoint(0, rainbowWidth / 2);
                SKPoint point2 = point1 + gradientVector;

                paint.Shader = SKShader.CreateLinearGradient(point1,
                                                             point2,
                                                             colors,
                                                             null,
                                                             SKShaderTileMode.Repeat);

                canvas.DrawPath(path, paint);
            }
        }
    }
}
```

Ahora los colores del arco iris están alineados con la cifra:

[![Degradado de arco iris](linear-gradient-images/RainbowGradient.png "Degradado de arco iris")](linear-gradient-images/RainbowGradient-Large.png#lightbox)

**Colores de infinito**

También se usa un degradado de arco iris en la página de **colores de infinito** . En esta página se dibuja un signo de infinito mediante un objeto de trazado descrito en el artículo [**tres tipos de curvas de Bézier**](../../curves/beziers.md#bezier-curve-approximation-to-circular-arcs). A continuación, se colorea la imagen con un degradado de arco iris animado barre continuamente a través de la imagen.

El constructor crea el objeto `SKPath` que describe el signo de infinito. Después de crea la ruta de acceso, el constructor también puede obtener los límites rectangulares de la ruta de acceso. A continuación, calcula un valor denominado `gradientCycleLength`. Si un degradado se basa en las esquinas superior izquierda e inferior derecha del rectángulo `pathBounds`, este valor `gradientCycleLength` es el ancho horizontal total del patrón de degradado:

```csharp
public class InfinityColorsPage : ContentPage
{
    ···
    SKCanvasView canvasView;

    // Path information 
    SKPath infinityPath;
    SKRect pathBounds;
    float gradientCycleLength;

    // Gradient information
    SKColor[] colors = new SKColor[8];
    ···

    public InfinityColorsPage ()
    {
        Title = "Infinity Colors";

        // Create path for infinity sign
        infinityPath = new SKPath();
        infinityPath.MoveTo(0, 0);                                  // Center
        infinityPath.CubicTo(  50,  -50,   95, -100,  150, -100);   // To top of right loop
        infinityPath.CubicTo( 205, -100,  250,  -55,  250,    0);   // To far right of right loop
        infinityPath.CubicTo( 250,   55,  205,  100,  150,  100);   // To bottom of right loop
        infinityPath.CubicTo(  95,  100,   50,   50,    0,    0);   // Back to center  
        infinityPath.CubicTo( -50,  -50,  -95, -100, -150, -100);   // To top of left loop
        infinityPath.CubicTo(-205, -100, -250,  -55, -250,    0);   // To far left of left loop
        infinityPath.CubicTo(-250,   55, -205,  100, -150,  100);   // To bottom of left loop
        infinityPath.CubicTo( -95,  100, - 50,   50,    0,    0);   // Back to center
        infinityPath.Close();

        // Calculate path information 
        pathBounds = infinityPath.Bounds;
        gradientCycleLength = pathBounds.Width +
            pathBounds.Height * pathBounds.Height / pathBounds.Width;

        // Create SKColor array for gradient
        for (int i = 0; i < colors.Length; i++)
        {
            colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
        }

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
}
```

El constructor también crea la matriz `colors` para el arco iris y el objeto `SKCanvasView`.

Las invalidaciones de los métodos `OnAppearing` y `OnDisappearing` realizan la sobrecarga de la animación. El método `OnTimerTick` anima el campo `offset` de 0 a `gradientCycleLength` cada dos segundos:

```csharp
public class InfinityColorsPage : ContentPage
{
    ···
    // For animation
    bool isAnimating;
    float offset;
    Stopwatch stopwatch = new Stopwatch();
    ···

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 2;     // seconds
        double progress = stopwatch.Elapsed.TotalSeconds % duration / duration;
        offset = (float)(gradientCycleLength * progress);
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

Por último, el controlador de `PaintSurface` representa el signo de infinito. Dado que la ruta de acceso contiene coordenadas negativas y positivas que rodean un punto central de (0,0), se usa una transformación de `Translate` en el lienzo para desplazarla al centro. La transformación traducir va seguida de una transformación `Scale` que aplica un factor de escala que hace que el signo de infinito sea lo más grande posible mientras se mantiene dentro del 95% del ancho y el alto del lienzo. 

Observe que la constante `STROKE_WIDTH` se agrega al ancho y al alto del rectángulo delimitador de la ruta de acceso. La ruta de acceso se va a trazar una línea de este ancho, por lo que aumenta el tamaño del tamaño infinito representado por ese formato medio en los cuatro lados:

```csharp
public class InfinityColorsPage : ContentPage
{
    const int STROKE_WIDTH = 50;
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set transforms to shift path to center and scale to canvas size
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.95f * 
            Math.Min(info.Width / (pathBounds.Width + STROKE_WIDTH),
                     info.Height / (pathBounds.Height + STROKE_WIDTH)));

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = STROKE_WIDTH;
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(pathBounds.Left, pathBounds.Top),
                                new SKPoint(pathBounds.Right, pathBounds.Bottom),
                                colors,
                                null,
                                SKShaderTileMode.Repeat,
                                SKMatrix.MakeTranslation(offset, 0));

            canvas.DrawPath(infinityPath, paint);
        }
    }
}
```

Fíjese en los puntos pasados como los dos primeros argumentos de `SKShader.CreateLinearGradient`. Estos puntos se basan en la ruta de acceso original del rectángulo delimitador. El primer punto es (&ndash;250, &ndash;100) y el segundo es (250, 100). Interno SkiaSharp, esos puntos se someten a la transformación de lienzo actual para que se alineen correctamente con el inicio de sesión muestra infinito.

Sin el último argumento para `CreateLinearGradient`, vería un degradado de arco iris que se extiende desde la parte superior izquierda del signo de infinito hasta la parte inferior derecha. (En realidad, el degradado se extiende desde la esquina superior izquierda a la esquina inferior derecha del rectángulo delimitador. El signo de infinito representado es mayor que el rectángulo delimitador a la mitad del valor de `STROKE_WIDTH` en todos los lados. Dado que el degradado es rojo al principio y al final, y el degradado se crea con `SKShaderTileMode.Repeat`, no se percibe la diferencia).

Con ese último argumento para `CreateLinearGradient`, el patrón de degradado se barrido continuamente por la imagen:

[![Colores de infinito](linear-gradient-images/InfinityColors.png "Colores de infinito")](linear-gradient-images/InfinityColors-Large.png#lightbox)

## <a name="transparency-and-gradients"></a>Transparencia y degradados

Los colores que contribuyen a un degradado pueden incorporar la transparencia. En lugar de un degradado que se desvanece de un color a otro, puede atenuar el degradado de un color transparente. 

Puede usar esta técnica para algunos efectos interesantes. Uno de los ejemplos clásicos muestra un objeto gráfico con su reflexión:

[![Degradado de reflexión](linear-gradient-images/ReflectionGradient.png "Degradado de reflexión")](linear-gradient-images/ReflectionGradient-Large.png#lightbox)

Se colorea el texto que está al revés con un degradado que es transparente en la parte superior a totalmente transparente en la parte inferior al 50%. Estos niveles de transparencia se asocian con los valores alfa de 0 x 80 y 0.

El controlador de `PaintSurface` de la página **gradiente de reflexión** escala el tamaño del texto al 90% del ancho del lienzo. A continuación, calcula los valores `xText` y `yText` para colocar el texto de forma que se Centre horizontalmente pero se encuentre en una línea base que se corresponda con el centro vertical de la página:

```csharp
public class ReflectionGradientPage : ContentPage
{
    const string TEXT = "Reflection";

    public ReflectionGradientPage ()
    {
        Title = "Reflection Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Set text color to blue
            paint.Color = SKColors.Blue;

            // Set text size to fill 90% of width
            paint.TextSize = 100;
            float width = paint.MeasureText(TEXT);
            float scale = 0.9f * info.Width / width;
            paint.TextSize *= scale;

            // Get text bounds
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Calculate offsets to position text above center
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2;

            // Draw unreflected text
            canvas.DrawText(TEXT, xText, yText, paint);

            // Shift textBounds to match displayed text
            textBounds.Offset(xText, yText);

            // Use those offsets to create a gradient for the reflected text
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, textBounds.Top),
                                new SKPoint(0, textBounds.Bottom),
                                new SKColor[] { paint.Color.WithAlpha(0),
                                                paint.Color.WithAlpha(0x80) },
                                null,
                                SKShaderTileMode.Clamp);

            // Scale the canvas to flip upside-down around the vertical center
            canvas.Scale(1, -1, 0, yText);

            // Draw reflected text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

Esos valores `xText` y `yText` son los mismos que se usan para mostrar el texto reflejado en la llamada a `DrawText` en la parte inferior del controlador de `PaintSurface`. Sin embargo, justo antes de ese código, verá una llamada al método `Scale` de `SKCanvas`. Este método `Scale` escala horizontalmente por 1 (que no hace nada), sino verticalmente por &ndash;1, que de hecho voltea todo lo que está al revés. El centro de rotación se establece en el punto (0, `yText`), donde `yText` es el centro vertical del lienzo, calculado originalmente como `info.Height` dividido entre 2.

Tenga en cuenta que Skia usa el degradado para objetos gráficos antes de las transformaciones de lienzo de color. Después de dibujar el texto no reflejado, el rectángulo de `textBounds` se desplaza para que se corresponda con el texto mostrado:

```csharp
textBounds.Offset(xText, yText);
```

La llamada `CreateLinearGradient` define un degradado desde la parte superior del rectángulo hasta la parte inferior. El degradado es de un azul completamente transparente (`paint.Color.WithAlpha(0)`) a un azul transparente (`paint.Color.WithAlpha(0x80)`) del 50%. La transformación del lienzo de gira el texto boca abajo, por lo que el azul transparente del 50% comienza en la línea de base y se vuelve transparente en la parte superior del texto.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
