---
title: SkiaSharp ruido y composición
description: Genere sombreadores de ruido de Perl y combínelo con otros sombreadores.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 90C2D00A-2876-43EA-A836-538C3318CF93
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 00b6251f530a4927d069ae92ec919645a06baf15
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555403"
---
# <a name="skiasharp-noise-and-composing"></a>SkiaSharp ruido y composición

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Los gráficos vectoriales simples tienden a parecer poco naturales. Las líneas rectas, las curvas suaves y los colores sólidos no se asemejan a las imperfecciones de los objetos del mundo real. Al trabajar en los gráficos generados por el equipo para el _Tron_de películas 1982, Computer científico Ken Perl comenzó a desarrollar algoritmos que usaban procesos aleatorios para proporcionar a estas imágenes texturas más realistas. En 1997, Ken Perl ha ganado una Premio a la Academia de logros técnicos. Su trabajo ha sido conocido como Perlen el ruido y se admite en SkiaSharp. Este es un ejemplo:

![Ejemplo de ruido de Perlen](noise-images/NoiseSample.png "Ejemplo de ruido de Perlen")

Como puede ver, cada píxel no es un valor de color aleatorio. La continuidad de píxel a píxel produce formas aleatorias.

La compatibilidad de Perlen el ruido en Skia se basa en una especificación del W3C para CSS y SVG. En la sección 8,20 del [**módulo de efectos de filtro 1**](https://www.w3.org/TR/filter-effects-1/#feTurbulenceElement) se incluyen los algoritmos de ruido de Perl en código C.

## <a name="exploring-perlin-noise"></a>Exploración del ruido de Perl

La [`SKShader`](xref:SkiaSharp.SKShader) clase define dos métodos estáticos distintos para generar el ruido de Perl:  [`CreatePerlinNoiseFractalNoise`](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise*) y [`CreatePerlinNoiseTurbulence`](xref:SkiaSharp.SKShader.CreatePerlinNoiseTurbulence*) . Los parámetros son idénticos:

```csharp
public static SkiaSharp CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);

public static SkiaSharp.SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);
```

Ambos métodos también existen en versiones sobrecargadas con un `SKPointI` parámetro adicional. En la sección de [**mosaico Perl**](#tiling-perlin-noise) en el ruido se describen estas sobrecargas.

Los dos `baseFrequency` argumentos son valores positivos definidos en la documentación de SkiaSharp en el intervalo de 0 a 1, pero también se pueden establecer en valores superiores. Cuanto mayor sea el valor, mayor será el cambio en la imagen aleatoria en las direcciones horizontal y vertical.

El `numOctaves` valor es un entero de 1 o superior. Se relaciona con un factor de iteración en los algoritmos. Cada octava adicional aporta un efecto que es la mitad de la octava anterior, por lo que el efecto disminuye con valores de octava más altos.

El `seed` parámetro es el punto inicial para el generador de números aleatorios. Aunque se especifica como un valor de punto flotante, la fracción se trunca antes de usarse y 0 es igual que 1.

La página de **ruido de Perlen** en el ejemplo [ **SkiaSharpFormsDemos**)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) le permite experimentar con varios valores de los `baseFrequency` `numOctaves` argumentos y. Este es el archivo XAML:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.PerlinNoisePage"
             Title="Perlin Noise">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="baseFrequencyXSlider"
                Maximum="4"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="baseFrequencyXText"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="baseFrequencyYSlider"
                Maximum="4"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="baseFrequencyYText"
               HorizontalTextAlignment="Center" />

        <StackLayout Orientation="Horizontal"
                     HorizontalOptions="Center"
                     Margin="10">

            <Label Text="{Binding Source={x:Reference octavesStepper},
                                  Path=Value,
                                  StringFormat='Number of Octaves: {0:F0}'}"
                   VerticalOptions="Center" />

            <Stepper x:Name="octavesStepper"
                     Minimum="1"
                     ValueChanged="OnStepperValueChanged" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

Utiliza dos `Slider` vistas para los dos `baseFrequency` argumentos. Para expandir el intervalo de valores inferiores, los controles deslizantes son logarítmicos. El archivo de código subyacente calcula los argumentos para los `SKShader` métodos de las potencias de los `Slider` valores. Las `Label` vistas muestran los valores calculados:

```csharp
float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);
```

Un `Slider` valor de 1 corresponde a 0,001, un `Slider` valor de sistema operativo 2 corresponde a 0,01, el valor `Slider` 3 corresponde a 0,1 y el `Slider` valor 4 corresponde a 1.

Este es el archivo de código subyacente que incluye ese código:

```csharp
public partial class PerlinNoisePage : ContentPage
{
    public PerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get values from sliders and stepper
        float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
        baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

        float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
        baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);

        int numOctaves = (int)octavesStepper.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader =
                SKShader.CreatePerlinNoiseFractalNoise(baseFreqX,
                                                       baseFreqY,
                                                       numOctaves,
                                                       0);

            SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
            canvas.DrawRect(rect, paint);

            paint.Shader =
                SKShader.CreatePerlinNoiseTurbulence(baseFreqX,
                                                     baseFreqY,
                                                     numOctaves,
                                                     0);

            rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Este es el programa que se ejecuta en dispositivos iOS, Android y Plataforma universal de Windows (UWP). El ruido fractal se muestra en la mitad superior del lienzo. El ruido de turbulencias está en la mitad inferior:

[![Sonido de Perl](noise-images/PerlinNoise.png "Sonido de Perl")](noise-images/PerlinNoise-Large.png#lightbox)

Los mismos argumentos siempre producen el mismo patrón que comienza en la esquina superior izquierda. Esta coherencia es obvia al ajustar el ancho y el alto de la ventana de UWP. Cuando Windows 10 vuelve a dibujar la pantalla, el patrón de la mitad superior del lienzo sigue siendo el mismo.

El patrón de ruido incorpora varios grados de transparencia. La transparencia es obvia si establece un color en la `canvas.Clear()` llamada. Ese color se destaca en el patrón. También verá este efecto en la sección [**combinar varios sombreadores**](#combining-multiple-shaders).

Estos patrones de ruido de Perlan rara vez se usan. A menudo, están sujetos a los modos de mezcla y los filtros de color descritos en los artículos posteriores.

## <a name="tiling-perlin-noise"></a>Colocar en mosaico Perl en el ruido

Los dos métodos estáticos `SKShader` para crear el ruido de Perl también existen en las versiones de sobrecarga. Las [`CreatePerlinNoiseFractalNoise`](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI)) [`CreatePerlinNoiseTurbulence`](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI)) sobrecargas y tienen un `SKPointI` parámetro adicional:

```csharp
public static SKShader CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);

public static SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);
```

La [`SKPointI`](xref:SkiaSharp.SKPointI) estructura es la versión entera de la estructura familiar [`SKPoint`](xref:SkiaSharp.SKPoint) . `SKPointI` define `X` `Y` las propiedades y del tipo `int` en lugar de `float` .

Estos métodos crean un patrón de repetición del tamaño especificado. En cada icono, el borde derecho es el mismo que el borde izquierdo y el borde superior es el mismo que el borde inferior. Esta característica se muestra en la página de **ruido de Perlen en mosaico** . El archivo XAML es similar al ejemplo anterior, pero solo tiene una `Stepper` vista para cambiar el `seed` argumento:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.TiledPerlinNoisePage"
             Title="Tiled Perlin Noise">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <StackLayout Orientation="Horizontal"
                     HorizontalOptions="Center"
                     Margin="10">

            <Label Text="{Binding Source={x:Reference seedStepper},
                                  Path=Value,
                                  StringFormat='Seed: {0:F0}'}"
                   VerticalOptions="Center" />

            <Stepper x:Name="seedStepper"
                     Minimum="1"
                     ValueChanged="OnStepperValueChanged" />

        </StackLayout>
    </StackLayout>
</ContentPage>
```

El archivo de código subyacente define una constante para el tamaño del mosaico. El `PaintSurface` controlador crea un mapa de bits de ese tamaño y un `SKCanvas` para dibujar en dicho mapa de bits. El `SKShader.CreatePerlinNoiseTurbulence` método crea un sombreador con ese tamaño de mosaico. Este sombreador se dibuja en el mapa de bits:

```csharp
public partial class TiledPerlinNoisePage : ContentPage
{
    const int TILE_SIZE = 200;

    public TiledPerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get seed value from stepper
        float seed = (float)seedStepper.Value;

        SKRect tileRect = new SKRect(0, 0, TILE_SIZE, TILE_SIZE);

        using (SKBitmap bitmap = new SKBitmap(TILE_SIZE, TILE_SIZE))
        {
            using (SKCanvas bitmapCanvas = new SKCanvas(bitmap))
            {
                bitmapCanvas.Clear();

                // Draw tiled turbulence noise on bitmap
                using (SKPaint paint = new SKPaint())
                {
                    paint.Shader = SKShader.CreatePerlinNoiseTurbulence(
                                        0.02f, 0.02f, 1, seed,
                                        new SKPointI(TILE_SIZE, TILE_SIZE));

                    bitmapCanvas.DrawRect(tileRect, paint);
                }
            }

            // Draw tiled bitmap shader on canvas
            using (SKPaint paint = new SKPaint())
            {
                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat);
                canvas.DrawRect(info.Rect, paint);
            }

            // Draw rectangle showing tile
            using (SKPaint paint = new SKPaint())
            {
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                paint.StrokeWidth = 2;

                canvas.DrawRect(tileRect, paint);
            }
        }
    }
}
```

Una vez creado el mapa de bits, `SKPaint` se usa otro objeto para crear un patrón de mapa de bits en mosaico mediante una llamada a `SKShader.CreateBitmap` . Observe los dos argumentos de `SKShaderTileMode.Repeat` :

```csharp
paint.Shader = SKShader.CreateBitmap(bitmap,
                                     SKShaderTileMode.Repeat,
                                     SKShaderTileMode.Repeat);
```

Este sombreador se usa para cubrir el lienzo. Por último, `SKPaint` se usa otro objeto para trazar un rectángulo que muestra el tamaño del mapa de bits original.

Solo el `seed` parámetro es seleccionable desde la interfaz de usuario. Si `seed` se usa el mismo patrón en cada plataforma, mostraría el mismo patrón. `seed`Los distintos valores dan como resultado patrones diferentes:

[![Sonido de Perl en mosaico](noise-images/TiledPerlinNoise.png "Sonido de Perl en mosaico")](noise-images/TiledPerlinNoise-Large.png#lightbox)

El patrón de cuadrado de 200 píxeles de la esquina superior izquierda fluye sin problemas en los demás iconos.

## <a name="combining-multiple-shaders"></a>Combinar varios sombreadores

La `SKShader` clase incluye un [`CreateColor`](xref:SkiaSharp.SKShader.CreateColor*) método que crea un sombreador con un color sólido especificado. Este sombreador no es muy útil porque simplemente se puede establecer ese color en la `Color` propiedad del `SKPaint` objeto y establecer la `Shader` propiedad en NULL.

Este `CreateColor` método resulta útil en otro método que `SKShader` define. Este método es [`CreateCompose`](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader)) , que combina dos sombreadores. Esta es la sintaxis:

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader);
```

El `srcShader` (sombreador de origen) se dibuja realmente encima de `dstShader` (sombreador de destino). Si el sombreador de origen es un color sólido o un degradado sin transparencia, el sombreador de destino estará completamente oculto.

Un sombreador de ruido de Perlen contiene transparencia. Si ese sombreador es el origen, el sombreador de destino se mostrará a través de las áreas transparentes.

La página de **ruido de Perl en composición** tiene un archivo XAML que es prácticamente idéntico a la primera página de ruido de **Perlen** . El archivo de código subyacente también es similar. Sin embargo, la página de **ruido de Perl** en el original establece la `Shader` propiedad de `SKPaint` en el sombreador devuelto de los métodos estáticos `CreatePerlinNoiseFractalNoise` y `CreatePerlinNoiseTurbulence` . Esta página de **ruido de Perl está compuesta** llama a `CreateCompose` un sombreador de combinación. El destino es un sombreador azul sólido creado mediante `CreateColor` . El origen es un sombreador de ruido de Perlen:

```csharp
public partial class ComposedPerlinNoisePage : ContentPage
{
    public ComposedPerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get values from sliders and stepper
        float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
        baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

        float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
        baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);

        int numOctaves = (int)octavesStepper.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateCompose(
                SKShader.CreateColor(SKColors.Blue),
                SKShader.CreatePerlinNoiseFractalNoise(baseFreqX,
                                                       baseFreqY,
                                                       numOctaves,
                                                       0));

            SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
            canvas.DrawRect(rect, paint);

            paint.Shader = SKShader.CreateCompose(
                SKShader.CreateColor(SKColors.Blue),
                SKShader.CreatePerlinNoiseTurbulence(baseFreqX,
                                                     baseFreqY,
                                                     numOctaves,
                                                     0));

            rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

El sombreador de ruido fractal está en la parte superior; el sombreador de turbulencias está en la parte inferior:

[![Compuestos Perl en el ruido](noise-images/ComposedPerlinNoise.png "Compuestos Perl en el ruido")](noise-images/ComposedPerlinNoise-Large.png#lightbox)

Observe cuántos azules son estos sombreadores que los que se muestran en la página de **ruido de Perlen** . La diferencia ilustra la cantidad de transparencia en los sombreadores de ruido.

También hay una sobrecarga del [`CreateCompose`](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader,SkiaSharp.SKBlendMode)) método:

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader, SKBlendMode blendMode);
```

El último parámetro es un miembro de la `SKBlendMode` enumeración, una enumeración con 29 miembros que se describe en la siguiente serie de artículos sobre los [**modos de composición y mezcla de SkiaSharp**](../blend-modes/index.md).

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)