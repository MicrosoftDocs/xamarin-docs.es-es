---
title: Los degradados circulares de SkiaSharp
description: Obtenga información sobre los diferentes tipos de degradados en función de círculos.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 400AE23A-6A0B-4FA8-BD6B-DE4146B04732
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: d56cc499112a937cd1a22664adeedd54c4397341
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199002"
---
# <a name="the-skiasharp-circular-gradients"></a>Los degradados circulares de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

El [ `SKShader` ](xref:SkiaSharp.SKShader) clase define los métodos estáticos para crear cuatro tipos diferentes de los degradados. El [ **degradado lineal de SkiaSharp** ](linear-gradient.md) artículo se describe la [ `CreateLinearGradient` ](xref:SkiaSharp.SKShader.CreateLinearGradient*) método. En este artículo se trata los tres tipos de degradados, todos ellos se basan en círculos.

El [ `CreateRadialGradient` ](xref:SkiaSharp.SKShader.CreateRadialGradient*) método crea un degradado que emana desde el centro de un círculo:

![Ejemplo de degradado radial](circular-gradients-images/RadialGradientSample.png)

El [ `CreateSweepGradient` ](xref:SkiaSharp.SKShader.CreateSweepGradient*) método crea un degradado que se realiza un barrido en torno al centro de un círculo:

![Ejemplo de degradado de barrido](circular-gradients-images/SweepGradientSample.png)

El tercer tipo de degradado es bastante raro. Se llama dos puntas cónico degradado y se define mediante el [ `CreateTwoPointConicalGradient` ](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient*) método. El degradado se extiende desde un círculo a otro:

![Ejemplo de degradado cónico](circular-gradients-images/ConicalGradientSample.png)

Si los dos círculos tienen tamaños diferentes, a continuación, el degradado adopta la forma de cono.

Este artículo exploran estos degradados con más detalle.

## <a name="the-radial-gradient"></a>Degradado radial

El [ `CreateRadialGradient` ](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) método tiene la siguiente sintaxis:

```csharp
public static SKShader CreateRadialGradient (SKPoint center, 
                                             Single radius, 
                                             SKColor[] colors, 
                                             Single[] colorPos, 
                                             SKShaderTileMode mode)
```

Un [ `CreateRadialGradient` ](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) sobrecarga también incluye un parámetro de matriz de transformación.

Los primeros dos argumentos especifican el centro de un círculo y un radio. El degradado comienza en el que el centro y se extiende hacia fuera para `radius` píxeles. ¿Qué ocurre más allá de `radius` depende de la [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode) argumento. El `colors` parámetro es una matriz de dos o más colores (al igual que en los métodos de degradado lineales) y `colorPos` es una matriz de enteros en el intervalo de 0 a 1. Estos números enteros indican las posiciones relativas de los colores a lo largo del que `radius` línea. Puede establecer dicho argumento en `null` para asignar espacio igualmente a los colores.

Si usa `CreateRadialGradient` para rellenar un círculo, puede establecer el centro del degradado en el centro del círculo y el radio de la radio del círculo del degradado. En ese caso, el `SKShaderTileMode` argumento no tiene ningún efecto en la representación del degradado. Sin embargo, si el área que rellena el degradado es mayor que el círculo definido por el degradado, la `SKShaderTileMode` argumento tiene un impacto profundo en lo que ocurre fuera del círculo.

El efecto de `SKShaderMode` se muestra en el **degradado Radial** página en el [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) ejemplo. Crea una instancia en el archivo XAML de esta página una `Picker` que le permite seleccionar uno de los tres miembros de la `SKShaderTileMode` enumeración:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.RadialGradientPage"
             Title="Radial Gradient">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                Grid.Row="0"
                                PaintSurface="OnCanvasViewPaintSurface" />

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
</ContentPage>
```

El archivo de código subyacente colorea todo el lienzo con un degradado radial. El centro del degradado está establecido en el centro del lienzo y el radio está establecido en 100 píxeles. El degradado está formado sólo dos colores, blanco y negro:

```csharp
public partial class RadialGradientPage : ContentPage
{
    public RadialGradientPage ()
    {
        InitializeComponent ();
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

        SKShaderTileMode tileMode =
            (SKShaderTileMode)(tileModePicker.SelectedIndex == -1 ?
                                        0 : tileModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                new SKPoint(info.Rect.MidX, info.Rect.MidY),
                                100,
                                new SKColor[] { SKColors.Black, SKColors.White },
                                null,
                                tileMode);

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Este código crea un degradado con negro en el centro de difuminación gradualmente en blanco 100 píxeles desde el centro. ¿Qué ocurre más allá de ese radius depende el `SKShaderTileMode` argumento:

[![Degradado radial](circular-gradients-images/RadialGradient.png "degradado Radial")](circular-gradients-images/RadialGradient-Large.png#lightbox)

En los tres casos, el degradado rellena el lienzo. En la pantalla de iOS a la izquierda, el degradado fuera del radio continúa con el último color, que es el blanco. Que es el resultado de `SKShaderTileMode.Clamp`. La pantalla de Android muestra el efecto `SKShaderTileMode.Repeat`de: En 100 píxeles desde el centro, el degradado comienza de nuevo con el primer color, que es negro. El degradado repite cada 100 píxeles de radius. 

La pantalla de la plataforma Universal de Windows en el derecho se muestra cómo `SKShaderTileMode.Mirror` hace que los gradientes para las direcciones alternativas. El primer degradado es del negro en el centro en blanco en un radio de 100 píxeles. La siguiente es blanca desde el radio de 100 píxeles en negro en un radio de 200 píxeles y se invierte el degradado siguiente de nuevo.

Puede usar más de dos colores en un degradado radial. El **arco iris arco degradado** en el ejemplo crea una matriz de ocho colores correspondientes a los colores del arco iris y terminando con rojo y también una matriz de valores de posición ocho:

```csharp
public class RainbowArcGradientPage : ContentPage
{
    public RainbowArcGradientPage ()
    {
        Title = "Rainbow Arc Gradient";

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
            float rainbowWidth = Math.Min(info.Width, info.Height) / 4f;

            // Center of arc and gradient is lower-right corner
            SKPoint center = new SKPoint(info.Width, info.Height);

            // Find outer, inner, and middle radius
            float outerRadius = Math.Min(info.Width, info.Height);
            float innerRadius = outerRadius - rainbowWidth;
            float radius = outerRadius - rainbowWidth / 2;

            // Calculate the colors and positions
            SKColor[] colors = new SKColor[8];
            float[] positions = new float[8];

            for (int i = 0; i < colors.Length; i++)
            {
                colors[i] = SKColor.FromHsl(i * 360f / 7, 100, 50);
                positions[i] = (i + (7f - i) * innerRadius / outerRadius) / 7f;
            }

            // Create sweep gradient based on center and outer radius
            paint.Shader = SKShader.CreateRadialGradient(center, 
                                                         outerRadius, 
                                                         colors, 
                                                         positions, 
                                                         SKShaderTileMode.Clamp);
            // Draw a circle with a wide line
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = rainbowWidth;

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

Suponga que el mínimo de ancho y altura del lienzo es 1000, lo que significa que el `rainbowWidth` valor es 250. El `outerRadius` y `innerRadius` valores se establecen en 1000 y 750, respectivamente. Estos valores se utilizan para calcular el `positions` matriz; los valores de ocho oscilan entre 0.75f en 1. El `radius` valor se usa para el trazado del círculo. El valor de 875 significa que el ancho del trazo de 250 píxeles se extiende entre el radio de 750 píxeles y el radio de 1000 píxeles:

[![Degradado de arco iris](circular-gradients-images/RainbowArcGradient.png "degradado del arco iris")](circular-gradients-images/RainbowArcGradient-Large.png#lightbox)

Si rellena el lienzo completo con este degradado, vería que es rojo dentro del radio interno. Esto es porque el `positions` matriz no empieza por 0. El primer color se utiliza para los desplazamientos de 0 y el primer valor de matriz. También es rojo más allá de la radio exterior del degradado. Que es el resultado de la `Clamp` icono modo. Dado que el degradado se utiliza para trazado una línea gruesa, estas áreas de color rojo no son visibles.

## <a name="radial-gradients-for-masking"></a>Degradados radiales para el enmascaramiento

Como degradados lineales, degradados radiales pueden incorporar los colores o parcialmente transparentes. Esta característica es útil para un proceso denominado _enmascaramiento_, lo que oculta parte de una imagen para enfatizar la otra parte de la imagen.

El **máscara de degradado Radial** página muestra un ejemplo. El programa carga uno de los mapas de bits del recurso. El `CENTER` y `RADIUS` campos se determina a partir de un examen del mapa de bits y hacer referencia a un área que debe resaltarse. El `PaintSurface` controlador comienza mediante el cálculo de un rectángulo para mostrar el mapa de bits y, a continuación, se muestra en el rectángulo:

```csharp
public class RadialGradientMaskPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(RadialGradientMaskPage),
        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    static readonly SKPoint CENTER = new SKPoint(180, 300);
    static readonly float RADIUS = 120;

    public RadialGradientMaskPage ()
    {
        Title = "Radial Gradient Mask";

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

        // Find rectangle to display bitmap
        float scale = Math.Min((float)info.Width / bitmap.Width,
                                (float)info.Height / bitmap.Height);

        SKRect rect = SKRect.Create(scale * bitmap.Width, scale * bitmap.Height);

        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Display bitmap in rectangle
        canvas.DrawBitmap(bitmap, rect);

        // Adjust center and radius for scaled and offset bitmap
        SKPoint center = new SKPoint(scale * CENTER.X + x,
                                     scale * CENTER.Y + y);
        float radius = scale * RADIUS;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                center,
                                radius,
                                new SKColor[] { SKColors.Transparent,
                                                SKColors.White },
                                new float[] { 0.6f, 1 },
                                SKShaderTileMode.Clamp);

            // Display rectangle using that gradient
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Después de dibujar el mapa de bits, convierte cierto código simple `CENTER` y `RADIUS` a `center` y `radius`, que se hacen referencia en el área resaltada en el mapa de bits que se ha escalado y desplazado a la pantalla. Estos valores se usan para crear un degradado radial con ese centro y el radio. Los dos colores comienzan en transparente en el centro y el primer 60% del radio. El degradado, a continuación, se difumina a blanco:

[![Máscara de degradado radial](circular-gradients-images/RadialGradientMask.png "máscara de degradado Radial")](circular-gradients-images/RadialGradientMask-Large.png#lightbox)

Este enfoque no es la mejor forma de un mapa de bits de máscara. El problema es que la propiedad mask tiene principalmente un color blanco, que se ha elegido para que coincida con el fondo del lienzo. Si el fondo es algún otro color &mdash; o quizás un degradado propio &mdash; si no coincide. En el artículo se muestra un mejor enfoque para enmascaramiento [modos de fusión de SkiaSharp Porter-Duff](../blend-modes/porter-duff.md).

## <a name="radial-gradients-for-specular-highlights"></a>Degradados radiales para resaltes especulares

Cuando una luz produzca una superficie redondeada, refleja la luz en muchas direcciones, pero algunas de la luz rebota directamente en la vista del Visor. Esto a menudo en la superficie de llamada crea la apariencia de un área blanca aproximada un _resaltado especular_.

En los gráficos tridimensionales, resaltes especulares a menudo el resultado de los algoritmos utilizados para determinar las rutas de acceso claros y sombreado. En los gráficos bidimensionales, resaltes especulares a veces se agregan para sugerir la apariencia de una superficie 3D. Un resaltado especular puede transformar un círculo rojo plana en una bola roja.

El **especular Radial** página usa un degradado radial para hacer exactamente eso. El `PaintSurface` seres controlador mediante el cálculo de un radio para el círculo y dos `SKPoint` valores &mdash; un `center` y un `offCenter` que está comprendido entre el centro y el borde superior izquierda del círculo:

```csharp
public class RadialSpecularHighlightPage : ContentPage
{
    public RadialSpecularHighlightPage()
    {
        Title = "Radial Specular Highlight";

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

        float radius = 0.4f * Math.Min(info.Width, info.Height);
        SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
        SKPoint offCenter = center - new SKPoint(radius / 2, radius / 2);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                offCenter,
                                radius / 2,
                                new SKColor[] { SKColors.White, SKColors.Red },
                                null,
                                SKShaderTileMode.Clamp);

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

El `CreateRadialGradient` llamada crea un degradado que comienza en el que `offCenter` punto con blanco y finaliza con la red a una distancia de la mitad el radio. Este es su aspecto:

[![Resaltado especular radial](circular-gradients-images/RadialSpecularHighlight.png "resaltado especular Radial")](circular-gradients-images/RadialSpecularHighlight-Large.png#lightbox)

Si observa detenidamente esta degradado, podría decidir que lo contiene errores. El degradado se centra en torno a un momento determinado, y es posible que desee fuese un poco menos simétricos para reflejar la superficie redondeada. En ese caso, es posible que prefiera el resaltado especular se muestra a continuación, en la sección [ **cónicos degradados de resaltes especulares**](#conical-gradients-for-specular-highlights).

## <a name="the-sweep-gradient"></a>El degradado de barrido

El [ `CreateSweepGradient` ](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[])) método tiene la sintaxis más sencilla de todos los métodos de creación de degradado:

```csharp
public static SKShader CreateSweepGradient (SKPoint center, 
                                            SKColor[] colors, 
                                            Single[] colorPos)
```

Es simplemente un centro de una matriz de colores y las posiciones de color. El degradado comienza a la derecha del punto central y barre 360 grados a las agujas del reloj en torno al centro. Tenga en cuenta que no hay ningún `SKShaderTileMode` parámetro.

Un [ `CreateSweepGradient` ](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKMatrix)) sobrecarga con un parámetro de transformación de matriz también está disponible. Puede aplicar una transformación de giro para cambiar el punto inicial del degradado. También puede aplicar una transformación de escala para cambiar la dirección de las agujas del reloj a hacia la izquierda.

El **barrido degradado** página usa un degradado de barrido para un círculo con un ancho de trazo de 50 píxeles de colores:

[![Degradado de barrido](circular-gradients-images/SweepGradient.png "barrido de degradado")](circular-gradients-images/SweepGradient-Large.png#lightbox)

La `SweepGradientPage` clase define una matriz de ocho colores con valores de matiz diferente. Tenga en cuenta que la matriz comienza y termina con rojo (un valor de matiz de 0 o 360), que aparece a la derecha en las capturas de pantalla:

```csharp
public class SweepGradientPage : ContentPage
{
    bool drawBackground;

    public SweepGradientPage ()
    {
        Title = "Sweep Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

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
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Define an array of rainbow colors
            SKColor[] colors = new SKColor[8];

            for (int i = 0; i < colors.Length; i++)
            {
                colors[i] = SKColor.FromHsl(i * 360f / 7, 100, 50);
            }

            SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);

            // Create sweep gradient based on center of canvas
            paint.Shader = SKShader.CreateSweepGradient(center, colors, null);

            // Draw a circle with a wide line
            const int strokeWidth = 50;
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = strokeWidth;

            float radius = (Math.Min(info.Width, info.Height) - strokeWidth) / 2;
            canvas.DrawCircle(center, radius, paint);

            if (drawBackground)
            {
                // Draw the gradient on the whole canvas
                paint.Style = SKPaintStyle.Fill;
                canvas.DrawRect(info.Rect, paint);
            }
        }
    }
}
```

El programa también implementa un `TapGestureRecognizer` que permite algo de código al final de la `PaintSurface` controlador. Este código usa el mismo degradado para rellenar el lienzo:

[![Barrido completo degradado](circular-gradients-images/SweepGradientFull.png "barrido completo de degradado")](circular-gradients-images/SweepGradientFull-Large.png#lightbox)

Estas capturas de pantalla muestran que los rellenos de degradado cualquier área se colorea por ella. Si el degradado no comenzar y terminar con el mismo color, habrá una discontinuidad a la derecha del punto central.

## <a name="the-two-point-conical-gradient"></a>El degradado cónico de dos puntas

El [ `CreateTwoPointConicalGradient` ](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) método tiene la siguiente sintaxis:

```csharp
public static SKShader CreateTwoPointConicalGradient (SKPoint startCenter, 
                                                      Single startRadius, 
                                                      SKPoint endCenter, 
                                                      Single endRadius, 
                                                      SKColor[] colors, 
                                                      Single[] colorPos, 
                                                      SKShaderTileMode mode)
```

Los parámetros comienzan con puntos centrales y radios de dos círculos, que se conoce como el _iniciar_ círculo y _final_ círculo. Los tres parámetros restantes son los mismos que para `CreateLinearGradient` y `CreateRadialGradient`. Un [ `CreateTwoPointConicalGradient` ](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) sobrecarga incluye una transformación de matriz.

Degradado que comienza en el círculo de inicio y finaliza en el círculo final. El `SKShaderTileMode` parámetro controla lo que ocurre más allá de los dos círculos. El degradado de dos puntas cónico es degradado único que no rellena completamente un área. Si los dos círculos tienen el mismo radio, el degradado está restringido a un rectángulo con un ancho que es el mismo que el diámetro de los círculos. Si los dos círculos tienen distintos radios, el degradado constituye un cono.

Es probable que desee experimentar con dos puntas cónico degradado, por lo que la **cónico degradado** página deriva `InteractivePage` para permitir que los dos puntos de toque circule para las dos radios de un círculo:

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
                       xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Effects.ConicalGradientPage"
                       Title="Conical Gradient">
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

El archivo de código subyacente define los dos `TouchPoint` objetos con radios fijas de 50 y 100:

```csharp
public partial class ConicalGradientPage : InteractivePage
{
    const int RADIUS1 = 50;
    const int RADIUS2 = 100;

    public ConicalGradientPage ()
    {
        touchPoints = new TouchPoint[2];

        touchPoints[0] = new TouchPoint
        {
            Center = new SKPoint(100, 100),
            Radius = RADIUS1
        };

        touchPoints[1] = new TouchPoint
        {
            Center = new SKPoint(300, 300),
            Radius = RADIUS2
        };

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
            paint.Shader = SKShader.CreateTwoPointConicalGradient(touchPoints[0].Center,
                                                                  RADIUS1,
                                                                  touchPoints[1].Center,
                                                                  RADIUS2,
                                                                  colors,
                                                                  null,
                                                                  tileMode);
            canvas.DrawRect(info.Rect, paint);
        }

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
        }
    }
}
```

El `colors` matriz está en roja, verde y azul. El código hacia la parte inferior de la `PaintSurface` controlador dibuja los puntos de dos toque como círculos en negro para que no obstruirá el degradado.

Tenga en cuenta que `DrawRect` llamada utiliza el degradado para todo el lienzo de color. Por lo general, sin embargo, gran parte del lienzo sigue siendo asignar por el degradado. Este es el programa que muestra tres configuraciones posibles:

[![Cónico degradado](circular-gradients-images/ConicalGradient.png "cónico degradado")](circular-gradients-images/ConicalGradient-Large.png#lightbox)

La pantalla de iOS a la izquierda muestra el efecto de la `SKShaderTileMode` de `Clamp`. El degradado comienza con dentro del borde del círculo más pequeño que es opuesta lado más próximo al segundo círculo de color rojo. El `Clamp` valor también hace que el rojo continuar en el punto del cono. Finaliza el gradiente de azul en el borde exterior del círculo más grande que esté más cerca de la primera círculo, pero continúa con el azul dentro de ese círculo y más allá.

La pantalla Android es similar pero con un `SKShaderTileMode` de `Repeat`. Ahora es más claro que el degradado comienza dentro del círculo primera y termina fuera del círculo de segundo. El `Repeat` configuración hace que el degradado se vuelva a repetir con dentro del círculo más grande de color rojo.

La pantalla UWP muestra lo que sucede cuando se mueve el círculo más pequeño completamente dentro del círculo más grande. El degradado deja de ser un cono y en su lugar, rellena el área completa. El efecto es similar del degradado radial, pero es asimétrico si el círculo más pequeño no exactamente se centra dentro del círculo más grande.

Puede dudar de la utilidad práctica del degradado al uno círculo está anidado en otro, pero resulta ideal para una iluminación especular.

## <a name="conical-gradients-for-specular-highlights"></a>Cónicos degradados de resaltes especulares

Anteriormente en este artículo vimos cómo usar un degradado radial para crear una iluminación especular. También puede usar dos puntas cónico degradado para este propósito, y es preferible su aspecto:

[![Cónico resaltado especular](circular-gradients-images/ConicalSpecularHighlight.png "cónico resaltado especular")](circular-gradients-images/ConicalSpecularHighlight-Large.png#lightbox)

La apariencia asimétrica mejor sugiere la superficie del objeto redondeada. 

El código de dibujo en el **especular cónico** página es la misma que la **especular Radial** página excepto el sombreador:

```csharp
public class ConicalSpecularHighlightPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateTwoPointConicalGradient(
                                offCenter,
                                1,
                                center,
                                radius,
                                new SKColor[] { SKColors.White, SKColors.Red },
                                null,
                                SKShaderTileMode.Clamp);

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

Los dos círculos tienen centros de `offCenter` y `center`. El círculo centrado en `center` está asociado con un radio que abarca la bola toda, pero el círculo centrado en `offCenter` tiene un radio de un solo píxel. El degradado eficazmente en ese momento comienza y termina en el perímetro de la bola.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
