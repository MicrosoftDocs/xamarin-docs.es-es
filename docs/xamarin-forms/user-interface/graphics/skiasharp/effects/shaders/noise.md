---
title: Ruido de SkiaSharp y composición
description: Generar a los sombreadores de ruido de Perlin y se combina con otros sombreadores.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 90C2D00A-2876-43EA-A836-538C3318CF93
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: dea7f5e51a864922d56f7b65d19b21a889cbc650
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656163"
---
# <a name="skiasharp-noise-and-composing"></a>Ruido de SkiaSharp y composición

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Gráficos vectoriales simple tienden a parecer natural. Las líneas rectas y curvas suaves colores sólidos no son similares a las imperfecciones de objetos del mundo real. Mientras trabaja en los gráficos generados por el equipo para la película 1982 _Tron_, Ken Perlin de científico informático empezó a desarrollar algoritmos que utilizan procesos aleatorios para ofrecer estas imágenes de texturas más realistas. En 1997, Ken Perlin ganó un premio Academy por logros técnicos. Su trabajo ha llegado a ser conocido como ruido de Perlin y se admite en SkiaSharp. Por ejemplo:

![Ejemplo de ruido de Perlin](noise-images/NoiseSample.png "muestra de ruido de Perlin")

Como puede ver, cada píxel no es un valor de color aleatorio. La continuidad de píxel a píxel da como resultado formas aleatorias.

La compatibilidad de ruido de Perlin en Skia se basa en una especificación de W3C para CSS y SVG. Sección 8.20 de [ **filtro efectos de módulo de nivel 1** ](http://www.w3.org/TR/filter-effects-1/#feTurbulenceElement) incluye los algoritmos de ruido de Perlin subyacentes en el código de C.

## <a name="exploring-perlin-noise"></a>Exploración de ruido de Perlin

El [ `SKShader` ](xref:SkiaSharp.SKShader) clase define dos métodos estáticos para generar ruido de Perlin: [ `CreatePerlinNoiseFractalNoise` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise*) y [ `CreatePerlinNoiseTurbulence` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseTurbulence*). Los parámetros son idénticos:

```csharp
public static SkiaSharp CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);

public static SkiaSharp.SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);
```

Ambos métodos también están incluidos en las versiones sobrecargadas con adicional `SKPointI` parámetro. La sección [ **ruido de Perlin mosaico** ](#tiling-perlin-noise) describe estas sobrecargas.

Los dos `baseFrequency` argumentos son valores positivos definidos en la documentación de SkiaSharp como comprendida entre 0 y 1, pero se puede establecer en los valores más altos. Cuanto mayor sea el valor, mayor será el cambio en la imagen aleatorio en dirección horizontal y vertical.

El `numOctaves` valor es un entero de 1 o superior. Se relaciona con un factor de iteración en los algoritmos. Cada octava adicional contribuye un efecto que es la mitad de la octava anterior, por lo que reduce el efecto con los valores más altos de octava.

El `seed` parámetro es el punto de partida para el generador de números aleatorios. Aunque se especifica como un valor de punto flotante, la fracción se trunca antes de que se usa y 0 es igual a 1.

El **ruido de Perlin** página en el [ **SkiaSharpFormsDemos**)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) ejemplo permite que experimenta con distintos valores de la `baseFrequency` y `numOctaves` argumentos. Este es el archivo XAML:

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

Se utilizan dos `Slider` vistas para las dos `baseFrequency` argumentos. Para expandir el intervalo de valores inferior, los controles deslizantes son logarítmicos. El archivo de código subyacente calcula los argumentos para el `SKShader`métodos de competencias de la `Slider` valores. El `Label` vistas muestran los valores calculados:

```csharp
float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);
```

Un `Slider` valor de 1 corresponde a 0,001, un `Slider` valor 2 del sistema operativo corresponde a 0,01, un `Slider` valores de 3 corresponde a 0,1 y un `Slider` corresponde el valor de 4 a 1.

Este es el archivo de código subyacente que incluya ese código:

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

Este es el programa que se ejecutan en iOS, Android y plataforma Universal de Windows (UWP) los dispositivos. El ruido fractal se muestra en la mitad superior del lienzo. Es el ruido turbulencias en la parte inferior la mitad:

[![Ruido de Perlin](noise-images/PerlinNoise.png "ruido de Perlin")](noise-images/PerlinNoise-Large.png#lightbox)

Los mismos argumentos siempre producen el mismo patrón que comienza en la esquina superior izquierda. Esta coherencia es obvia al ajustar el ancho y alto de la ventana UWP. Como Windows 10, se vuelve a dibujar la pantalla, el patrón en la mitad superior del lienzo sigue siendo el mismo.

El patrón de ruido incorpora varios grados de transparencia. La transparencia se vuelve evidente si se establece un color el `canvas.Clear()` llamar. Ese color se convierte en destacado en el patrón. También verá este efecto en la sección [ **combinar varios de los sombreadores**](#combining-multiple-shaders).

Estos patrones de ruido de Perlin rara vez se utilizan por sí mismos. A menudo estén sometidos para mezclar los modos y filtros de color que se describe en los artículos posteriores.

## <a name="tiling-perlin-noise"></a>Ruido de Perlin disposición en mosaico

Dos estático `SKShader` métodos para crear ruido de Perlin también están incluidos en las versiones de sobrecarga. El [ `CreatePerlinNoiseFractalNoise` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI)) y [ `CreatePerlinNoiseTurbulence` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI)) sobrecargas tienen más `SKPointI` parámetro:

```csharp
public static SKShader CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);

public static SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);
```

El [ `SKPointI` ](xref:SkiaSharp.SKPointI) estructura es la versión de entero de la conocida [ `SKPoint` ](xref:SkiaSharp.SKPoint) estructura. `SKPointI` define `X` y `Y` las propiedades de tipo `int` lugar `float`.

Estos métodos crean un patrón de repetición del tamaño especificado. En cada icono, el borde derecho es el mismo que el borde izquierdo y el borde superior es el mismo que el borde inferior. Esta característica se muestra en el **en mosaico ruido de Perlin** página. El archivo XAML es similar al ejemplo anterior, pero solo tiene un `Stepper` vista para cambiar el `seed` argumento:

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

El archivo de código subyacente define una constante para el tamaño del mosaico. El `PaintSurface` controlador crea un mapa de bits de ese tamaño y un `SKCanvas` para dibujar en ese mapa de bits. El `SKShader.CreatePerlinNoiseTurbulence` método crea un sombreador con ese tamaño de mosaico. Este sombreador se dibuja en el mapa de bits:

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

Después de crear el mapa de bits, otro `SKPaint` objeto se usa para crear un patrón de mosaico de mapa de bits mediante una llamada a `SKShader.CreateBitmap`. Tenga en cuenta los dos argumentos de `SKShaderTileMode.Repeat`:

```csharp
paint.Shader = SKShader.CreateBitmap(bitmap,
                                     SKShaderTileMode.Repeat,
                                     SKShaderTileMode.Repeat);
```

Este sombreador se usa para abarcar el lienzo. Por último, otra `SKPaint` objeto se usa para trazar un rectángulo que muestra el tamaño del mapa de bits original.

Solo el `seed` parámetro es seleccionable desde la interfaz de usuario. Si el mismo `seed` patrón se usa en cada plataforma, mostraría el mismo patrón. Diferentes `seed` valores como resultado patrones diferentes:

[![En el mosaico de ruido de Perlin](noise-images/TiledPerlinNoise.png "en mosaico ruido de Perlin")](noise-images/TiledPerlinNoise-Large.png#lightbox)

El patrón de 200 píxeles cuadrado en la esquina superior izquierda fluye a la perfección en los otros iconos.

## <a name="combining-multiple-shaders"></a>Combinar varios de los sombreadores

El `SKShader` clase incluye un [ `CreateColor` ](xref:SkiaSharp.SKShader.CreateColor*) método que crea un sombreador con un color sólido especificado. Este sombreador no es muy útil por sí mismo porque simplemente puede establecer ese color en el `Color` propiedad de la `SKPaint` de objeto y establecer el `Shader` propiedad en null.

Esto `CreateColor` método resulta útil en otro método que `SKShader` define. Este método es [ `CreateCompose` ](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader)), que combina dos sombreadores. Esta es la sintaxis:

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader);
```

El `srcShader` (sombreador de origen) eficazmente se dibuja en la parte superior de la `dstShader` (sombreador de destino). Si el sombreador de origen es un color sólido o un degradado sin transparencia, el sombreador de destino quedarán completamente ocultos.

Un sombreador de ruido de Perlin contiene transparencia. Si dicho sombreado es el origen, se mostrará el sombreador de destino a través de las áreas transparentes.

El **compone ruido de Perlin** página tiene un archivo XAML que es prácticamente idéntico al primero **ruido de Perlin** página. El archivo de código subyacente también es similar. Pero el original **ruido de Perlin** página establece el `Shader` propiedad de `SKPaint` con el sombreador devuelto desde estático `CreatePerlinNoiseFractalNoise` y `CreatePerlinNoiseTurbulence` métodos. Esto **compone ruido de Perlin** página llamadas `CreateCompose` para un sombreador de combinación. El destino es un sombreado azul sólido siguieron `CreateColor`. El origen es un sombreador de ruido de Perlin:

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

El sombreador de ruido fractal está en la parte superior; el sombreador turbulencias está en la parte inferior:

[![Compone ruido de Perlin](noise-images/ComposedPerlinNoise.png "compone ruido de Perlin")](noise-images/ComposedPerlinNoise-Large.png#lightbox)

Observe cuánto azul estos sombreadores son a los que se muestra el **ruido de Perlin** página. La diferencia muestra la cantidad de transparencia en los sombreadores de ruido.

También hay una sobrecarga de la [ `CreateCompose` ](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader,SkiaSharp.SKBlendMode)) método:

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader, SKBlendMode blendMode);
```

El último parámetro es un miembro de la `SKBlendMode` enumeración, una enumeración con 29 miembros que se describe en la siguiente serie de artículos en [ **los modos de composición y blend de SkiaSharp**](../blend-modes/index.md).

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
