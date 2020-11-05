---
title: El degradado lineal SkiaSharp
description: Descubra cómo trazar líneas o rellenar áreas con degradados compuestos de una mezcla gradual de dos colores.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 20A2A8C4-FEB7-478D-BF57-C92E26117B6A
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9d2ac2eec5422068243ac43e78092e25c10652f5
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366794"
---
# <a name="the-skiasharp-linear-gradient"></a>El degradado lineal SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

La [`SKPaint`](xref:SkiaSharp.SKPaint) clase define una [`Color`](xref:SkiaSharp.SKPaint.Color) propiedad que se usa para trazar líneas o rellenar áreas con un color sólido. También puede trazar líneas o rellenar áreas con _degradados_ , que son mezclas graduales de colores:

![Ejemplo de degradado lineal](linear-gradient-images/LinearGradientSample.png "Ejemplo de degradado lineal")

El tipo más básico de degradado es un degradado _lineal_ . La combinación de colores se produce en una línea (denominada _línea de degradado_ ) de un punto a otro. Las líneas perpendiculares a la línea de degradado tienen el mismo color. Puede crear un degradado lineal mediante uno de los dos [`SKShader.CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient*) métodos estáticos. La diferencia entre las dos sobrecargas es que una incluye una transformación de matriz y la otra no. 

Estos métodos devuelven un objeto de tipo [`SKShader`](xref:SkiaSharp.SKShader) que se establece en la [`Shader`](xref:SkiaSharp.SKPaint.Shader) propiedad de `SKPaint` . Si la `Shader` propiedad no es null, invalida la `Color` propiedad. Cualquier línea que esté contorneada o cualquier área que se rellene con este `SKPaint` objeto se basa en el degradado, en lugar del color sólido.

> [!NOTE]
> La `Shader` propiedad se omite cuando se incluye un `SKPaint` objeto en una `DrawBitmap` llamada. Puede usar la `Color` propiedad de `SKPaint` para establecer un nivel de transparencia para mostrar un mapa de bits (como se describe en el artículo [Mostrar mapas de bits de SkiaSharp](../../bitmaps/displaying.md#displaying-in-pixel-dimensions)), pero no puede usar la `Shader` propiedad para mostrar un mapa de bits con una transparencia de degradado. Existen otras técnicas para mostrar mapas de bits con transparencias de degradado: estas se describen en los artículos [SkiaSharp gradientes circulares](circular-gradients.md#radial-gradients-for-masking) y [SkiaSharp composiciones and Blend modes](../blend-modes/porter-duff.md#gradient-transparency-and-transitions).

## <a name="corner-to-corner-gradients"></a>Degradados de esquina a esquina

A menudo, un degradado lineal se extiende de una esquina de un rectángulo a otro. Si el punto inicial es la esquina superior izquierda del rectángulo, el degradado puede extender:

- verticalmente en la esquina inferior izquierda
- horizontalmente en la esquina superior derecha
- diagonalmente a la esquina inferior derecha

El degradado lineal diagonal se muestra en la primera página de la sección **sombreadores SkiaSharp y otros efectos** del ejemplo [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . La página de **degradado de esquina a esquina** crea un `SKCanvasView` en su constructor. El `PaintSurface` controlador crea un `SKPaint` objeto en una `using` instrucción y, a continuación, define un rectángulo cuadrado de 300 píxeles centrado en el lienzo:

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

`Shader`A la propiedad de `SKPaint` se le asigna el `SKShader` valor devuelto del `SKShader.CreateLinearGradient` método estático. Los cinco argumentos son los siguientes:

- El punto inicial del degradado, establecido aquí en la esquina superior izquierda del rectángulo.
- El punto final del degradado, establecido aquí en la esquina inferior derecha del rectángulo.
- Matriz de dos o más colores que contribuyen al degradado.
- Matriz de `float` valores que indica la posición relativa de los colores dentro de la línea de degradado.
- Miembro de la [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) enumeración que indica cómo se comporta el degradado más allá de los extremos de la línea de degradado

Una vez creado el objeto de degradado, el `DrawRect` método dibuja el rectángulo cuadrado de 300 píxeles usando el `SKPaint` objeto que incluye el sombreador. Aquí se ejecuta en iOS, Android y el Plataforma universal de Windows (UWP):

[![Degradado de esquina a esquina](linear-gradient-images/CornerToCornerGradient.png "Degradado de esquina a esquina")](linear-gradient-images/CornerToCornerGradient-Large.png#lightbox)

La línea de degradado se define mediante los dos puntos especificados como los dos primeros argumentos. Tenga en cuenta que estos puntos son relativos al _lienzo_ y _no_ al objeto gráfico que se muestra con el degradado. A lo largo de la línea de degradado, el color cambia gradualmente de rojo en la esquina superior izquierda a azul en la esquina inferior derecha. Cualquier línea que sea perpendicular a la línea de degradado tiene un color constante.

La matriz de `float` valores especificada como cuarto argumento tiene una correspondencia uno a uno con la matriz de colores. Los valores indican la posición relativa a lo largo de la línea de degradado donde se producen esos colores. En este caso, el valor de 0 significa que `Red` se produce al principio de la línea de degradado y 1 significa que `Blue` se produce al final de la línea. Los números deben ser ascendentes y deben estar en el intervalo de 0 a 1. Si no están en ese intervalo, se ajustarán para que estén en ese intervalo.

Los dos valores de la matriz se pueden establecer en un valor distinto de 0 y 1. Pruebe esto:

```csharp
new float[] { 0.25f, 0.75f }
```

Ahora todo el primer trimestre de la línea degradada es rojo puro y el último trimestre es azul puro. La mezcla de rojo y azul está restringida a la mitad central de la línea de degradado.

Por lo general, querrá espaciar estos valores de posición equitativamente entre 0 y 1. En ese caso, puede proporcionar simplemente `null` como el cuarto argumento a `CreateLinearGradient` .

Aunque este degradado se define entre dos esquinas del rectángulo cuadrado de 300 píxeles, no se limita a llenar ese rectángulo. La página de **degradado de esquina a esquina** incluye algún código adicional que responda a los grifos o a los clics del mouse en la página. El `drawBackground` campo se alterna entre `true` y `false` con cada derivación. Si el valor es `true` , el `PaintSurface` controlador usa el mismo `SKPaint` objeto para rellenar todo el lienzo y, a continuación, dibuja un rectángulo negro que indica el rectángulo más pequeño: 

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

Esto es lo que verá después de puntear en la pantalla:

[![Degradado de esquina a esquina lleno](linear-gradient-images/CornerToCornerGradientFull.png "Degradado de esquina a esquina lleno")](linear-gradient-images/CornerToCornerGradientFull-Large.png#lightbox)

Observe que el degradado se repite en el mismo patrón más allá de los puntos que definen la línea de degradado. Esta repetición se produce porque el último argumento de `CreateLinearGradient` es `SKShaderTileMode.Repeat` . (Verá las demás opciones en breve).

Observe también que los puntos que se usan para especificar la línea de degradado no son únicos. Las líneas perpendiculares a la línea de degradado tienen el mismo color, por lo que hay un número infinito de líneas de degradado que puede especificar para el mismo efecto. Por ejemplo, al rellenar un rectángulo con un degradado horizontal, puede especificar las esquinas superior izquierda y superior derecha, o las esquinas inferior izquierda e inferior derecha, o cualesquiera dos puntos que sean pares con y paralelos a esas líneas.

## <a name="interactively-experiment"></a>Experimentar de forma interactiva

Puede experimentar de forma interactiva con degradados lineales con la página de **degradado lineal interactiva** . En esta página se usa la `InteractivePage` clase presentada en el artículo [**tres formas de dibujar un arco**](../../curves/arcs.md). `InteractivePage` controla [`TouchEffect`](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md) los eventos para mantener una colección de `TouchPoint` objetos que se pueden desplace con los dedos o con el mouse.

El archivo XAML adjunta `TouchEffect` a un elemento primario de `SKCanvasView` y también incluye un `Picker` que permite seleccionar uno de los tres miembros de la [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) enumeración:

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

El constructor del archivo de código subyacente crea dos `TouchPoint` objetos para los puntos inicial y final del degradado lineal. El `PaintSurface` controlador define una matriz de tres colores (para un degradado de rojo a verde y azul) y obtiene el actual `SKShaderTileMode` de `Picker` :

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

El `PaintSurface` controlador crea el `SKShader` objeto a partir de toda la información y lo utiliza para colorear todo el lienzo. La matriz de `float` valores se establece en `null` . De lo contrario, para espaciar uniformemente tres colores, debería establecer ese parámetro en una matriz con los valores 0, 0,5 y 1.

La mayor parte del `PaintSurface` controlador está dedicada a mostrar varios objetos: los puntos táctiles como círculos de contorno, la línea de degradado y las líneas perpendiculares a las líneas de degradado en los puntos táctiles:

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

La línea de degradado que conecta los dos Touchpoints es fácil de dibujar, pero las líneas perpendiculares requieren algo más de trabajo. La línea de degradado se convierte en un vector, se normaliza para tener una longitud de una unidad y, a continuación, se gira en 90 grados. A ese vector se le asigna una longitud de 200 píxeles. Se usa para dibujar cuatro líneas que se extienden desde los puntos táctiles para que sean perpendiculares a la línea de degradado.

Las líneas perpendiculares coinciden con el principio y el final del degradado. Lo que sucede más allá de esas líneas depende de la configuración de la `SKShaderTileMode` enumeración:

[![Degradado lineal interactivo](linear-gradient-images/InteractiveLinearGradient.png "Degradado lineal interactivo")](linear-gradient-images/InteractiveLinearGradient-Large.png#lightbox)

Las tres capturas de pantallas muestran los resultados de los tres valores diferentes de [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) . La captura de pantalla `SKShaderTileMode.Clamp` de iOS muestra, que solo extiende los colores del borde del degradado. La `SKShaderTileMode.Repeat` opción de la captura de pantalla de Android muestra cómo se repite el patrón de degradado. La `SKShaderTileMode.Mirror` opción de la captura de pantalla de UWP también repite el patrón, pero el patrón se invierte cada vez, lo que da lugar a que no haya continuidad de color.

## <a name="gradients-on-gradients"></a>Degradados en degradados

La `SKShader` clase no define propiedades ni métodos públicos, excepto `Dispose` . `SKShader`Por lo tanto, los objetos creados por sus métodos estáticos son inmutables. Incluso si usa el mismo degradado para dos objetos diferentes, es probable que desee modificar ligeramente el degradado. Para ello, deberá crear un nuevo `SKShader` objeto.

La página de **texto de degradado** muestra texto y un segundo plano de colores con degradados similares:

[![Texto degradado](linear-gradient-images/GradientText.png "Texto degradado")](linear-gradient-images/GradientText-Large.png#lightbox)

Las únicas diferencias en los degradados son los puntos inicial y final. El degradado utilizado para mostrar texto se basa en dos puntos en las esquinas del rectángulo delimitador del texto. En segundo plano, los dos puntos se basan en todo el lienzo. Este es el código:

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

La `Shader` propiedad del `SKPaint` objeto se establece primero para mostrar un degradado para cubrir el fondo. Los puntos de degradado se establecen en las esquinas superior izquierda e inferior derecha del lienzo.

El código establece la `TextSize` propiedad del `SKPaint` objeto de modo que el texto se muestre en el 90% del ancho del lienzo. Los límites de texto se utilizan para calcular `xText` los `yText` valores y que se van a pasar al `DrawText` método para centrar el texto.

Sin embargo, los puntos de degradado de la segunda `CreateLinearGradient` llamada deben hacer referencia a la esquina superior izquierda e inferior derecha del texto en relación con el lienzo cuando se muestra. Esto se consigue desplazando el `textBounds` rectángulo por los mismos `xText` `yText` valores y:

```csharp
textBounds.Offset(xText, yText);
```

Ahora se pueden usar las esquinas superior izquierda e inferior derecha del rectángulo para establecer los puntos inicial y final del degradado.

## <a name="animating-a-gradient"></a>Animar un degradado

Hay varias maneras de animar un degradado. Un enfoque consiste en animar los puntos inicial y final. La página de **animación de degradado** mueve los dos puntos en un círculo que está centrado en el lienzo. El radio de este círculo es la mitad del ancho o del alto del lienzo, lo que sea menor. Los puntos inicial y final son opuestos entre sí en este círculo y el degradado va de blanco a negro con un `Mirror` modo de mosaico:

[![Animación de degradado](linear-gradient-images/GradientAnimation.png "Animación de degradado")](linear-gradient-images/GradientAnimation-Large.png#lightbox)

El constructor crea el `SKCanvasView` . Los `OnAppearing` `OnDisappearing` métodos y controlan la lógica de animación:

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

Esta es una manera de calcular los dos puntos de degradado. Un `SKPoint` valor denominado `vector` se calcula para extender desde el centro del lienzo hasta un punto en el radio del círculo. La dirección de este vector se basa en los valores de seno y coseno del ángulo. A continuación, se calculan los dos puntos de degradado opuestos: un punto se calcula restando ese Vector desde el punto central y otro punto se calcula agregando el vector al punto central:

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

Un enfoque algo diferente requiere menos código. Este enfoque usa el método de [`SKShader.CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) sobrecarga con una transformación de matriz como el último argumento. Este enfoque es la versión en el ejemplo [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) :

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

Si el ancho del lienzo es menor que el alto, los dos puntos de degradado se establecen en (0,0) y (,0 `info.Width` ). La transformación de giro pasada como el último argumento para `CreateLinearGradient` rotar eficazmente esos dos puntos alrededor del centro de la pantalla.

Tenga en cuenta que si el ángulo es 0, no hay rotación y los dos puntos de degradado son las esquinas superior izquierda e superior derecha del lienzo. Esos puntos no son los mismos puntos de degradado calculados como se muestra en la `CreateLinearGradient` llamada anterior. Pero estos puntos son _paralelos_ a la línea de degradado horizontal que disecciona el centro del lienzo y dan como resultado un degradado idéntico.

**Degradado de arco iris**

La página **degradado de arco iris** dibuja un arco iris desde la esquina superior izquierda del lienzo hasta la esquina inferior derecha. Pero este degradado de arco iris no es como un arco iris real. Es recto en lugar de curvar, pero se basa en ocho colores HSL (matiz-saturación-luminosidad) que se determinan al recorrer los valores de matiz de 0 a 360:

```csharp
SKColor[] colors = new SKColor[8];

for (int i = 0; i < colors.Length; i++)
{
    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
}
```

Ese código forma parte del `PaintSurface` controlador que se muestra a continuación. El controlador comienza creando una ruta de acceso que define un polígono de seis caras que se extiende desde la esquina superior izquierda del lienzo hasta la esquina inferior derecha:

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

Los dos puntos de degradado del `CreateLinearGradient` método se basan en dos de los puntos que definen esta ruta de acceso: ambos puntos están cerca de la esquina superior izquierda. El primero está en el borde superior del lienzo y el segundo en el borde izquierdo del lienzo. Este es el resultado:

[![Error de degradado de arco iris](linear-gradient-images/RainbowGradientFaulty.png "Error de degradado de arco iris")](linear-gradient-images/RainbowGradientFaulty-Large.png#lightbox)

Se trata de una imagen interesante, pero no se trata de la intención. El problema es que al crear un degradado lineal, las líneas de color constante son perpendiculares a la línea de degradado. La línea de degradado se basa en los puntos en los que la figura toca los lados superior e izquierdo, y esa línea no suele ser perpendicular a los bordes de la figura que se extienden a la esquina inferior derecha. Este enfoque solo funcionaría si el lienzo fuera cuadrado.

Para crear un degradado de arco iris adecuado, la línea de degradado debe ser perpendicular al borde del arco iris. Este es un cálculo más complicado. Debe definirse un vector que sea paralelo al lado largo de la figura. El vector gira 90 grados para que sea perpendicular a ese lado. A continuación, se alarga para que sea el ancho de la figura mediante la multiplicación por `rainbowWidth` . Los dos puntos de degradado se calculan en función de un punto del lado de la ilustración y ese punto más el vector. Este es el código que aparece en la página de **degradado de arco iris** en el ejemplo [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) :

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

Ahora, los colores del arco iris están alineados con la ilustración:

[![Degradado de arco iris](linear-gradient-images/RainbowGradient.png "Degradado de arco iris")](linear-gradient-images/RainbowGradient-Large.png#lightbox)

**Colores de infinito**

También se usa un degradado de arco iris en la página de **colores de infinito** . En esta página se dibuja un signo de infinito mediante un objeto de trazado descrito en el artículo [**tres tipos de curvas de Bézier**](../../curves/beziers.md#bezier-curve-approximation-to-circular-arcs). A continuación, la imagen se colorea con un degradado de arco iris animado que se rastrea continuamente en la imagen.

El constructor crea el `SKPath` objeto que describe el signo de infinito. Una vez creada la ruta de acceso, el constructor también puede obtener los límites rectangulares de la ruta de acceso. A continuación, calcula un valor denominado `gradientCycleLength` . Si un degradado se basa en las esquinas superior izquierda e inferior derecha del `pathBounds` rectángulo, este `gradientCycleLength` valor es el ancho horizontal total del patrón de degradado:

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

El constructor también crea la `colors` matriz para el arco iris y el `SKCanvasView` objeto.

Las invalidaciones de `OnAppearing` los `OnDisappearing` métodos y realizan la sobrecarga de la animación. El `OnTimerTick` método anima el `offset` campo de 0 a `gradientCycleLength` cada dos segundos:

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

Por último, el `PaintSurface` controlador representa el signo de infinito. Dado que la ruta de acceso contiene coordenadas negativas y positivas que rodean un punto central de (0,0), `Translate` se usa una transformación en el lienzo para desplazarla al centro. La transformación traducir va seguida de una `Scale` transformación que aplica un factor de escala que hace que el signo de infinito sea lo más grande posible mientras se mantiene dentro del 95% del ancho y del alto del lienzo. 

Observe que la `STROKE_WIDTH` constante se agrega al ancho y alto del rectángulo delimitador de la ruta de acceso. La ruta de acceso se trazará con una línea de este ancho, por lo que el tamaño del tamaño de infinito representado se aumenta en la mitad de ese ancho en los cuatro lados:

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

Fíjese en los puntos pasados como los dos primeros argumentos de `SKShader.CreateLinearGradient` . Esos puntos se basan en el rectángulo delimitador de la ruta de acceso original. El primer punto es ( &ndash; 250, &ndash; 100) y el segundo es (250, 100). Interna a SkiaSharp, esos puntos están sujetos a la transformación del lienzo actual para que se alineen correctamente con el signo de infinito mostrado.

Sin el último argumento en `CreateLinearGradient` , vería un degradado de arco iris que se extiende desde la parte superior izquierda del signo de infinito hasta la parte inferior derecha. (En realidad, el degradado se extiende desde la esquina superior izquierda a la esquina inferior derecha del rectángulo delimitador. El signo de infinito representado es mayor que el rectángulo delimitador en la mitad del `STROKE_WIDTH` valor en todos los lados. Dado que el degradado es rojo al principio y al final, y el degradado se crea con `SKShaderTileMode.Repeat` , la diferencia no es apreciable.)

Con ese último argumento en `CreateLinearGradient` , el patrón de degradado se barrido continuamente por la imagen:

[![Colores de infinito](linear-gradient-images/InfinityColors.png "Colores de infinito")](linear-gradient-images/InfinityColors-Large.png#lightbox)

## <a name="transparency-and-gradients"></a>Transparencia y degradados

Los colores que contribuyen a un degradado pueden incorporar transparencia. En lugar de un degradado que se desvanece de un color a otro, el degradado puede desvanecerse de un color a un transparente. 

Puede usar esta técnica para algunos efectos interesantes. Uno de los ejemplos clásicos muestra un objeto gráfico con su reflexión:

[![Degradado de reflexión](linear-gradient-images/ReflectionGradient.png "Degradado de reflexión")](linear-gradient-images/ReflectionGradient-Large.png#lightbox)

El texto que está al revés está coloreado con un degradado del 50% en la parte superior a la parte inferior. Estos niveles de transparencia están asociados a los valores alfa de 0x80 y 0.

El `PaintSurface` controlador de la página **gradiente de reflexión** escala el tamaño del texto al 90% del ancho del lienzo. A continuación, calcula `xText` los `yText` valores y para colocar el texto de forma que se Centre horizontalmente pero se encuentre en una línea base que se corresponda con el centro vertical de la página:

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

Esos `xText` `yText` valores y son los mismos que se usan para mostrar el texto reflejado en la `DrawText` llamada en la parte inferior del `PaintSurface` controlador. Sin embargo, justo antes de ese código, verá una llamada al `Scale` método de `SKCanvas` . Este `Scale` método escala horizontalmente por 1 (que no hace nada), sino verticalmente por &ndash; 1, lo que de hecho voltea todo hacia abajo. El centro de rotación se establece en el punto (0, `yText` ), donde `yText` es el centro vertical del lienzo, calculado originalmente como `info.Height` dividido entre 2.

Tenga en cuenta que Skia usa el degradado para colorear objetos gráficos antes de las transformaciones del lienzo. Después de dibujar el texto no reflejado, el `textBounds` rectángulo se desplaza para que se corresponda con el texto mostrado:

```csharp
textBounds.Offset(xText, yText);
```

La `CreateLinearGradient` llamada define un degradado desde la parte superior del rectángulo hasta la parte inferior. El degradado se realiza desde un azul completamente transparente ( `paint.Color.WithAlpha(0)` ) hasta un 50% de azul transparente ( `paint.Color.WithAlpha(0x80)` ). La transformación Canvas voltea el texto hacia abajo, por lo que el azul transparente 50% empieza en la línea de base y se convierte en transparente en la parte superior del texto.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)