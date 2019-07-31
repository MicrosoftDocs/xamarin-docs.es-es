---
title: Degradado lineal de SkiaSharp
description: Descubra cómo trazar líneas o áreas de relleno con degradados se componen de una mezcla gradual de dos colores.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 20A2A8C4-FEB7-478D-BF57-C92E26117B6A
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 43a427b4eca174dce4af47bb43cb1a048a6cfe7c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647687"
---
# <a name="the-skiasharp-linear-gradient"></a>Degradado lineal de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

El [ `SKPaint` ](xref:SkiaSharp.SKPaint) clase define un [ `Color` ](xref:SkiaSharp.SKPaint.Color) propiedad que se usa para trazar líneas o áreas de relleno con un color sólido. También puede trazar líneas o rellenar las áreas con _degradados_, que son graduales mezclas de colores:

![Ejemplo de degradado lineal](linear-gradient-images/LinearGradientSample.png "ejemplo degradado lineal")

El tipo más básico de degradado es un _lineal_ degradado. La combinación de colores que se produce en una línea (denominado el _línea degradado_) desde un punto a otro. Las líneas que son perpendiculares a la línea de degradado tienen el mismo color. Crear un degradado lineal con uno de los dos métodos estático [ `SKShader.CreateLinearGradient` ](xref:SkiaSharp.SKShader.CreateLinearGradient*) métodos. La diferencia entre las dos sobrecargas es que uno incluye una transformación de matriz y el otro no. 

Estos métodos devuelven un objeto de tipo [ `SKShader` ](xref:SkiaSharp.SKShader) que establecer para el [ `Shader` ](xref:SkiaSharp.SKPaint.Shader) propiedad de `SKPaint`. Si el `Shader` propiedad es no nulo, invalida el `Color` propiedad. Cualquier línea que se traza o cualquier área que se rellena mediante este `SKPaint` objeto se basa en el degradado en lugar de con el color sólido.

> [!NOTE]
> El `Shader` propiedad se omite cuando se incluye un `SKPaint` objeto en un `DrawBitmap` llamar. Puede usar el `Color` propiedad de `SKPaint` para establecer un nivel de transparencia para mostrar un mapa de bits (como se describe en el artículo [SkiaSharp mostrar mapas de bits](../../bitmaps/displaying.md#displaying-in-pixel-dimensions)), pero no se puede usar el `Shader` propiedad para mostrar un mapa de bits con una transparencia degradada. Existen otras técnicas para mostrar mapas de bits con transparencias de degradado: Se describen en los artículos [SkiaSharp degradados circulares](circular-gradients.md#radial-gradients-for-masking) y los [modos de composición y mezcla SkiaSharp](../blend-modes/porter-duff.md#gradient-transparency-and-transitions).

## <a name="corner-to-corner-gradients"></a>Esquina a esquina degradados

A menudo un degradado lineal se extiende desde una de las esquinas de un rectángulo a otro. Si el punto de inicio es la esquina superior izquierda del rectángulo, puede ampliar el degradado:

- verticalmente en la esquina inferior izquierda
- horizontalmente a la esquina superior derecha
- diagonalmente en la esquina inferior derecha

Degradado lineal diagonal se muestra en la primera página en el **SkiaSharp sombreadores y otros efectos a** sección de la [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) ejemplo. El **esquina a esquina degradado** página crea un `SKCanvasView` en su constructor. El `PaintSurface` controlador crea una `SKPaint` objeto en un `using` instrucción y, a continuación, define un rectángulo de 300 píxeles cuadrado centrado en el lienzo:

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

El `Shader` propiedad de `SKPaint` se asigna el `SKShader` devolver valor de estático `SKShader.CreateLinearGradient` método. Los cinco argumentos son los siguientes:

- El punto inicial del degradado, se establece aquí en la esquina superior izquierda del rectángulo
- El punto final del degradado, se establece aquí en la esquina inferior derecha del rectángulo
- Una matriz de dos o más colores que contribuyen al degradado
- Una matriz de `float` valores que indican la posición relativa de los colores de la línea de degradado
- Un miembro de la [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode) enumeración que indica cómo se comporta el degradado más allá de los extremos de la línea de degradado

Una vez creado el objeto de gradiente, la `DrawRect` método dibuja el rectángulo de 300 píxeles cuadrado utilizando el `SKPaint` objeto que incluye el sombreador. Aquí se ejecuta en iOS, Android y la plataforma Universal de Windows (UWP):

[![Degradado de esquina a esquina](linear-gradient-images/CornerToCornerGradient.png "esquina a esquina degradado")](linear-gradient-images/CornerToCornerGradient-Large.png#lightbox)

La línea de degradado se define mediante los dos puntos especificados como los dos primeros argumentos. Tenga en cuenta que estos puntos son relativas a la _lienzo_ y _no_ para el objeto gráfico que se muestran con el degradado. A lo largo de la línea de degradado, el color gradualmente, pasa de color rojo en la esquina superior izquierda en azul en la esquina inferior derecha. Cualquier línea perpendicular a la línea de degradado tiene un color constante.

La matriz de `float` valores especificados como el cuarto argumento tienen una correspondencia uno a uno con la matriz de colores. Los valores indican la posición relativa de la línea de degradado que se producen esos colores. En este caso, el 0 significa que `Red` se produce al principio de la línea de degradado y 1 significa que `Blue` se produce al final de la línea. Los números deben ser ascendente y deben estar en el intervalo de 0 a 1. Si no están en ese intervalo, se ajustará para que esté en ese intervalo.

Los dos valores de la matriz se pueden establecer en algo distinto de 0 y 1. Pruebe esto:

```csharp
new float[] { 0.25f, 0.75f }
```

Ahora el completamente primer trimestre de la línea de degradado es rojo puro, y el último trimestre es azul puro. La combinación de rojo y azul está restringida a la mitad de la línea de degradado central.

Por lo general, deseará espacio estos valores de posición igualmente de 0 a 1. Si es así, puede proporcionar simplemente `null` como el cuarto argumento `CreateLinearGradient`.

Aunque este degradado se define entre dos vértices del rectángulo de 300 píxeles cuadrado, no se restringen a rellenar el rectángulo. El **esquina a esquina degradado** página incluye algún código adicional que responde a las pulsaciones o los clics del mouse en la página. El `drawBackground` campo se alterna entre `true` y `false` con cada vez que toque. Si el valor es `true`, el `PaintSurface` controlador usa el mismo `SKPaint` objeto para llenar todo el lienzo y, a continuación, se dibuja un rectángulo negro que indica el rectángulo más pequeño: 

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

[![Esquina a esquina degradado completo](linear-gradient-images/CornerToCornerGradientFull.png "esquina a esquina degradado completo")](linear-gradient-images/CornerToCornerGradientFull-Large.png#lightbox)

Tenga en cuenta que el degradado se repite en el mismo patrón más allá de los puntos que definen la línea de degradado. Esta repetición se produce porque el último argumento `CreateLinearGradient` es `SKShaderTileMode.Repeat`. (Verá las demás opciones en breve.)

Tenga en cuenta que los puntos que se usa para especificar la línea de degradado no son únicos. Las líneas que son perpendiculares a la línea de degradado tienen el mismo color, por lo que hay un número infinito de líneas de degradado que se pueden especificar para el mismo efecto. Por ejemplo, al rellenar un rectángulo con un degradado horizontal, puede especificar las esquinas superior izquierda y derecha, o las esquinas inferior izquierda e inferior derecha o son paralelas a esas líneas e incluso con dos puntos.

## <a name="interactively-experiment"></a>Experimente de forma interactiva

Puede experimentar con degradados lineales con interactivamente el **interactivo de degradado lineal** página. Esta página usa el `InteractivePage` clase introducida en el artículo [ **tres maneras de dibujar un arco**](../../curves/arcs.md). `InteractivePage` identificadores [ `TouchEffect` ](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md) eventos para mantener una colección de `TouchPoint` objetos que se pueden mover con el mouse o con los dedos.

Adjunta el archivo XAML el `TouchEffect` con un elemento primario de la `SKCanvasView` e incluye también un `Picker` que le permite seleccionar uno de los tres miembros de la [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode) enumeración:

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

El constructor en el archivo de código subyacente, crea dos `TouchPoint` objetos para los puntos inicial y final del degradado lineal. El `PaintSurface` controlador define una matriz de tres colores (para un degradado de color rojo a verde a azul) y obtiene la actual `SKShaderTileMode` desde el `Picker`:

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

El `PaintSurface` controlador crea el `SKShader` objeto a partir de toda esa información y lo usa para todo el lienzo de color. La matriz de `float` valores se establecen en `null`. En caso contrario, para igualmente el espacio de tres colores, establecería ese parámetro en una matriz con los valores 0, 0,5 y 1.

La mayor parte de la `PaintSurface` controlador está dedicado a la visualización de varios objetos: los puntos táctiles como círculos de esquema, la línea de degradado y las líneas perpendiculares a las líneas de degradado en los puntos de toque:

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

Las líneas perpendiculares coinciden con el principio y al final del degradado. ¿Qué ocurre más allá de esas líneas depende del valor de la `SKShaderTileMode` enumeración:

[![Degradado lineal interactivo](linear-gradient-images/InteractiveLinearGradient.png "interactivo degradado lineal")](linear-gradient-images/InteractiveLinearGradient-Large.png#lightbox)

Las tres capturas de pantalla muestran los resultados de los tres valores distintos de [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode). La captura de pantalla de iOS muestra `SKShaderTileMode.Clamp`, que simplemente extiende los colores en el borde del degradado. El `SKShaderTileMode.Repeat` opción en la captura de pantalla de Android muestra cómo se repite el patrón de degradado. El `SKShaderTileMode.Mirror` opción en la captura de pantalla UWP también repite el patrón, pero el patrón se invierte cada vez, lo que no hay interrupciones de color.

## <a name="gradients-on-gradients"></a>Degradados en degradados

El `SKShader` clase no define ningún propiedades o métodos públicos, excepto para `Dispose`. El `SKShader` objetos creados por sus métodos estáticos, por tanto, son inmutables. Incluso si usa el mismo degradado para dos objetos diferentes, es probable que desee variar ligeramente el degradado. Para ello, deberá crear un nuevo `SKShader` objeto.

El **texto de degradado** página muestra el texto y un segundo plano que están coloreados con degradados similar:

[![Texto de degradado](linear-gradient-images/GradientText.png "texto de degradado")](linear-gradient-images/GradientText-Large.png#lightbox)

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

El `Shader` propiedad de la `SKPaint` objeto se establece en primer lugar para mostrar un degradado para cubrir el fondo. Los puntos de degradado se establecen en las esquinas superior izquierda e inferior derecha del lienzo.

El código establece la `TextSize` propiedad de la `SKPaint` objeto para que el texto se muestra en el 90% del ancho del lienzo. Los límites del texto se utilizan para calcular `xText` y `yText` valores para pasar a la `DrawText` método para centrar el texto.

Sin embargo, puntos de degradado para el segundo `CreateLinearGradient` llamada debe hacer referencia a la esquina superior izquierda e inferior derecha del texto en relación con el lienzo cuando se muestre. Esto se consigue cambiando el `textBounds` rectángulo por el mismo `xText` y `yText` valores:

```csharp
textBounds.Offset(xText, yText);
```

Ahora las esquinas superior izquierda e inferior derecha del rectángulo se pueden usar para establecer los puntos inicial y final del degradado.

## <a name="animating-a-gradient"></a>Animar un degradado

Hay varias maneras de animar un degradado. Un enfoque consiste en animar los puntos inicial y final. El **animación degradado** página mueve los dos puntos alrededor de un círculo que se centra en el lienzo. El radio de este círculo es la mitad del ancho o alto del lienzo, lo que sea menor. Los puntos inicial y final son opuesta entre sí en este círculo, y va de degradado en blanco a negro con un `Mirror` icono modo:

[![Animación degradado](linear-gradient-images/GradientAnimation.png "animación degradado")](linear-gradient-images/GradientAnimation-Large.png#lightbox)

El constructor crea la `SKCanvasView`. El `OnAppearing` y `OnDisappearing` métodos controlan la lógica de animación:

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

El `OnTimerTick` método calcula un `angle` valor que se anima desde 0 hasta 2π cada 3 segundos. 

Aquí es una manera de calcular los dos puntos de degradado. Un `SKPoint` valor denominado `vector` se calcula para ampliar desde el centro del lienzo a un punto en el radio del círculo. La dirección de este vector se basa en los valores de seno y coseno del ángulo. A continuación, se calculan los dos puntos de degradado opuestos: Un punto se calcula restando ese Vector desde el punto central y otro punto se calcula agregando el vector al punto central:

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

Un enfoque un poco diferente requiere menos código. Este enfoque hace uso de la [ `SKShader.CreateLinearGradient` ](xref:SkiaSharp.SKShader.CreateLinearGradient(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) sobrecargar el método con una transformación de matriz como el último argumento. Este enfoque es la versión en el [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) ejemplo:

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

Si el ancho del lienzo es menor que el alto, los dos puntos de degradado se establecen en (0, 0) y (`info.Width`, 0). La transformación de rotación que se pasa como el último argumento `CreateLinearGradient` eficazmente gira esos dos puntos en torno al centro de la pantalla.

Tenga en cuenta que si el ángulo es 0, no hay ningún giro y los dos puntos de degradado son las esquinas superior izquierda y derecha del lienzo. Los puntos no son los mismos puntos de degradado calculados como se muestra en la anterior `CreateLinearGradient` llamar. Pero estos puntos son _paralelo_ a la línea de degradado horizontal que corte el centro del lienzo y dan lugar a un degradado idénticos.

**Degradado arco iris**

El **arco iris degradado** página dibuja un arco iris desde la esquina superior izquierda del lienzo en la esquina inferior derecha. Pero este degradado arco iris no es como un arco iris real. Es directamente en lugar de curvados, pero se basa en ocho colores HSL (matiz-saturación-luminosidad) que vienen determinados por recorrer los valores de matiz de 0 a 360:

```csharp
SKColor[] colors = new SKColor[8];

for (int i = 0; i < colors.Length; i++)
{
    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
}
```

Que el código es parte de la `PaintSurface` controlador se muestra a continuación. El controlador comienza creando una ruta de acceso que define un polígono de seis lados que se extiende desde la esquina superior izquierda del lienzo a la esquina inferior derecha:

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

Los dos puntos de degradado `CreateLinearGradient` del método se basan en dos de los puntos que definen esta ruta de acceso: Ambos puntos están cerca de la esquina superior izquierda. La primera consiste en el borde superior del lienzo y el segundo es en el borde izquierdo del lienzo. Este es el resultado:

[![Degradado arco iris defectuoso](linear-gradient-images/RainbowGradientFaulty.png "degradado arco iris defectuoso")](linear-gradient-images/RainbowGradientFaulty-Large.png#lightbox)

Se trata de una imagen interesante, pero no resulta bastante la intención. El problema es que, cuando se crea un degradado lineal, las líneas de color constante son perpendiculares a la línea de degradado. La línea de degradado se basa en los puntos donde la figura toca los lados superiores e izquierdos, y esa línea generalmente no es perpendicular a los bordes de la ilustración que se extienden a la esquina inferior derecha. Este enfoque podría funcionar sólo si el lienzo fuera cuadrado.

Para crear un degradado arco iris adecuado, la línea de degradado debe ser perpendicular al borde del arco iris. Es un cálculo más complejo. Debe definirse un vector que sea paralelo en el lado largo de la figura. El vector es gira 90 grados por lo que es perpendicular a ese lado. A continuación, que se alargue para que sea el ancho de la figura al multiplicar por `rainbowWidth`. Se calculan según los dos puntos de degradado en un punto en el lateral de la ilustración, y que además del vector de punto. Este es el código que aparece en el **arco iris degradado** página en el [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) ejemplo:

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

[![Degradado arco iris](linear-gradient-images/RainbowGradient.png "degradado arco iris")](linear-gradient-images/RainbowGradient-Large.png#lightbox)

**Colores de infinito**

También se usa un degradado arco iris en el **infinito colores** página. Esta página dibuja un signo de infinito utilizando un objeto de ruta de acceso que se describe en el artículo [ **tipos tres de curvas de Bézier**](../../curves/beziers.md#bezier-curve-approximation-to-circular-arcs). A continuación, se colorea la imagen con un degradado de arco iris animado barre continuamente a través de la imagen.

El constructor crea la `SKPath` objeto que describe el inicio de sesión de infinito. Después de crea la ruta de acceso, el constructor también puede obtener los límites rectangulares de la ruta de acceso. A continuación, calcula un valor denominado `gradientCycleLength`. Si un degradado se basa en las esquinas superior izquierda e inferior derecha de la `pathBounds` rectángulo, esto `gradientCycleLength` valor es el ancho horizontal total del patrón de degradado:

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

El constructor también crea el `colors` matriz para el arco iris y el `SKCanvasView` objeto.

Invalidaciones de la `OnAppearing` y `OnDisappearing` métodos realizan la sobrecarga de la animación. El `OnTimerTick` método anima la `offset` campo de 0 a `gradientCycleLength` cada dos segundos:

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

Por último, el `PaintSurface` controlador representa el inicio de sesión de infinito. Dado que la ruta de acceso contiene coordenadas positivas y negativas que rodea un punto central de (0, 0), un `Translate` transformación en el lienzo se usa para desplazar al centro. La transformación de traslación es seguida por un `Scale` transformación que se aplica el factor de escala que hace que el inicio de sesión infinito tan grande como sea posible mientras se desvía el 95% del ancho y altura del lienzo. 

Tenga en cuenta que el `STROKE_WIDTH` constante se agrega al ancho y alto de la ruta de acceso del rectángulo delimitador. La ruta de acceso se va a trazar una línea de este ancho, por lo que aumenta el tamaño del tamaño infinito representado por ese formato medio en los cuatro lados:

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

Examine los puntos que se pasan como los dos primeros argumentos de `SKShader.CreateLinearGradient`. Estos puntos se basan en la ruta de acceso original del rectángulo delimitador. El primer punto es (&ndash;250, &ndash;100) y el segundo es (250, 100). Interno SkiaSharp, esos puntos se someten a la transformación de lienzo actual para que se alineen correctamente con el inicio de sesión muestra infinito.

Sin el último argumento `CreateLinearGradient`, verá un degradado arco iris que se extiende desde la esquina superior izquierda del signo infinito hasta la esquina inferior derecha. (En realidad, el degradado se extiende desde la esquina superior izquierda a la esquina inferior derecha del rectángulo delimitador. El signo representado infinito es mayor que el rectángulo delimitador en la mitad el `STROKE_WIDTH` valor en todos los lados. Dado que el gradiente es rojo al principio y al final, y el degradado se crea con `SKShaderTileMode.Repeat`, la diferencia no es apreciable.)

Con ese último argumento `CreateLinearGradient`, el patrón de degradado barre continuamente a través de la imagen:

[![Colores de infinito](linear-gradient-images/InfinityColors.png "colores infinito")](linear-gradient-images/InfinityColors-Large.png#lightbox)

## <a name="transparency-and-gradients"></a>Transparencia y degradados

Los colores que contribuyen a un degradado pueden incorporar la transparencia. En lugar de un degradado que se desvanece de un color a otro, puede atenuar el degradado de un color transparente. 

Puede usar esta técnica para algunos efectos interesantes. Uno de los ejemplos clásicos muestra un objeto gráfico con su reflexión:

[![Degradado de reflexión](linear-gradient-images/ReflectionGradient.png "degradado de la reflexión")](linear-gradient-images/ReflectionGradient-Large.png#lightbox)

Se colorea el texto que está al revés con un degradado que es transparente en la parte superior a totalmente transparente en la parte inferior al 50%. Estos niveles de transparencia se asocian con los valores alfa de 0 x 80 y 0.

El `PaintSurface` controlador en el **reflexión degradado** página amplía el tamaño del texto en el 90% del ancho del lienzo. A continuación, calcula `xText` y `yText` valores para colocar el texto que se centra horizontalmente pero sentado en una línea base correspondiente en el centro vertical de la página:

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

Los `xText` y `yText` los valores son los mismos valores que se usa para mostrar el texto reflejado en la `DrawText` llamar a la parte inferior de la `PaintSurface` controlador. Justo antes de ese código, sin embargo, verá una llamada a la `Scale` método `SKCanvas`. Esto `Scale` método escala horizontalmente en 1 (que no hace nada), pero verticalmente por &ndash;1, que se voltea eficazmente todo al revés. El centro de rotación se establece en el punto (0, `yText`), donde `yText` es el centro vertical del lienzo, originalmente se calcula como `info.Height` dividido por 2.

Tenga en cuenta que Skia usa el degradado para objetos gráficos antes de las transformaciones de lienzo de color. Después de dibuja el texto unreflected, el `textBounds` rectángulo se desplaza por lo que se corresponde con el texto mostrado:

```csharp
textBounds.Offset(xText, yText);
```

El `CreateLinearGradient` llamada define un degradado desde la parte superior del rectángulo a la parte inferior. El gradiente es de un azul completamente transparente (`paint.Color.WithAlpha(0)`) a un 50% de selección azul transparente (`paint.Color.WithAlpha(0x80)`). La transformación del lienzo de gira el texto boca abajo, por lo que el azul transparente del 50% comienza en la línea de base y se vuelve transparente en la parte superior del texto.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
