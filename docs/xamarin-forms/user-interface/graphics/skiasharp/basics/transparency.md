---
title: Transparencia de SkiaSharp
description: Use la transparencia para combinar varios objetos en una sola escena.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B62F9487-C30E-4C63-BAB1-4C091FF50378
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 4e8c909ce7dfd9c8f6dccbf2685ff2658931b6a5
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656354"
---
# <a name="skiasharp-transparency"></a>Transparencia de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Como ha visto, el [ `SKPaint` ](xref:SkiaSharp.SKPaint) clase incluye un [ `Color` ](xref:SkiaSharp.SKPaint.Color) propiedad de tipo [ `SKColor` ](xref:SkiaSharp.SKColor). `SKColor` incluye un canal alfa, algo que de color con una `SKColor` valor puede ser parcialmente transparente. 

Algunos transparencia se mostró en el [ **animación básica de SkiaSharp** ](animation.md) artículo. En este artículo profundiza un poco en transparencia para combinar varios objetos en una sola escena, una técnica conocida como _fusión_. Más avanzadas de las técnicas de mezcla se tratan en los artículos de la [ **sombreadores de SkiaSharp** ](../effects/shaders/index.md) sección.

Puede establecer el nivel de transparencia al crear un color mediante el parámetro de cuatro [ `SKColor` ](xref:SkiaSharp.SKColor.%23ctor(System.Byte,System.Byte,System.Byte,System.Byte)) constructor:

```csharp
SKColor (byte red, byte green, byte blue, byte alpha);
```

Un valor alfa de 0 es completamente transparente y un valor alfa de 0xFF es completamente opaco. Los valores entre estos dos extremos crean colores que sean parcialmente transparentes.

Además, `SKColor` define una práctica [ `WithAlpha` ](xref:SkiaSharp.SKColor.WithAlpha*) método que crea un nuevo color de un color existente pero con el nivel de alfa especificado:

```csharp
SKColor halfTransparentBlue = SKColors.Blue.WithAlpha(0x80);
```

Se muestra el uso de texto parcialmente transparente en el **código más código** página en el [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) ejemplo. Esta página desvanece dos cadenas de texto de entrada y salida mediante la incorporación de transparencia en la `SKColor` valores:

```csharp
public class CodeMoreCodePage : ContentPage
{
    SKCanvasView canvasView;
    bool isAnimating;
    Stopwatch stopwatch = new Stopwatch();
    double transparency;

    public CodeMoreCodePage ()
    {
        Title = "Code More Code";

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
        const int duration = 5;     // seconds
        double progress = stopwatch.Elapsed.TotalSeconds % duration / duration;
        transparency = 0.5 * (1 + Math.Sin(progress * 2 * Math.PI));
        canvasView.InvalidateSurface();

        return isAnimating;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        const string TEXT1 = "CODE";
        const string TEXT2 = "MORE";

        using (SKPaint paint = new SKPaint())
        {
            // Set text width to fit in width of canvas
            paint.TextSize = 100;
            float textWidth = paint.MeasureText(TEXT1);
            paint.TextSize *= 0.9f * info.Width / textWidth;

            // Center first text string
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT1, ref textBounds);

            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * (1 - transparency)));
            canvas.DrawText(TEXT1, xText, yText, paint);

            // Center second text string
            textBounds = new SKRect();
            paint.MeasureText(TEXT2, ref textBounds);

            xText = info.Width / 2 - textBounds.MidX;
            yText = info.Height / 2 - textBounds.MidY;

            paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * transparency));
            canvas.DrawText(TEXT2, xText, yText, paint);
        }
    }
}
```

El `transparency` campo se anima para variar de 0 a 1 y vuelva a hacer en su trabajo sinusoidal. La primera cadena de texto se muestra con un valor alfa que se calcula restando el `transparency` valor comprendido entre 1:

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * (1 - transparency)));
```

El [ `WithAlpha` ](xref:SkiaSharp.SKColor.WithAlpha*) método establece el componente alfa en color existente, que aquí es `SKColors.Blue`. La segunda cadena de texto utiliza un valor alfa que se calcula a partir de la `transparency` propio valor:

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * transparency));
```

La animación se alterna entre las dos palabras, para animar al usuario a "de código más" (o quizás solicitando "más código"):

[![Código más código](transparency-images/CodeMoreCode.png "más código de código")](transparency-images/CodeMoreCode-Large.png#lightbox)


En el artículo anterior sobre [ **conceptos básicos de mapa de bits de SkiaSharp**](bitmaps.md), vimos cómo mostrar mapas de bits mediante uno de los [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap*) métodos de `SKCanvas`. Todos los `DrawBitmap` métodos incluyen una `SKPaint` objeto como el último parámetro. De forma predeterminada, este parámetro se establece en `null` y puede ignorarlo. 

Como alternativa, puede establecer el `Color` propiedad de este `SKPaint` objeto para mostrar un mapa de bits con cierto grado de transparencia. Establecer un nivel de transparencia en la `Color` propiedad de `SKPaint` permite a los mapas de bits fundido de entrada y salida o disolver un mapa de bits a otro. 

Transparencia de mapa de bits se muestra en el **mapa de bits disolver** página. El archivo XAML crea una instancia de un `SKCanvasView` y un `Slider`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BitmapDissolvePage"
             Title="Bitmap Dissolve">
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="progressSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />
    </StackLayout>
</ContentPage>
```

El archivo de código subyacente carga dos recursos de mapa de bits. Estos mapas de bits no son del mismo tamaño, pero son la misma relación de aspecto:

```csharp
public partial class BitmapDissolvePage : ContentPage
{
    SKBitmap bitmap1;
    SKBitmap bitmap2;

    public BitmapDissolvePage()
    {
        InitializeComponent();

        // Load two bitmaps
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(
                                "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg"))
        {
            bitmap1 = SKBitmap.Decode(stream);
        }
        using (Stream stream = assembly.GetManifestResourceStream(
                                "SkiaSharpFormsDemos.Media.FacePalm.jpg"))
        {
            bitmap2 = SKBitmap.Decode(stream);
        }
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find rectangle to fit bitmap
        float scale = Math.Min((float)info.Width / bitmap1.Width,
                                (float)info.Height / bitmap1.Height);
        SKRect rect = SKRect.Create(scale * bitmap1.Width,
                                    scale * bitmap1.Height);
        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Get progress value from Slider
        float progress = (float)progressSlider.Value;

        // Display two bitmaps with transparency
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = paint.Color.WithAlpha((byte)(0xFF * (1 - progress)));
            canvas.DrawBitmap(bitmap1, rect, paint);

            paint.Color = paint.Color.WithAlpha((byte)(0xFF * progress));
            canvas.DrawBitmap(bitmap2, rect, paint);
        }
    }
}
```

El `Color` propiedad de la `SKPaint` objeto se establece en dos niveles de alfa complementarios para los dos mapas de bits. Cuando se usa `SKPaint` con mapas de bits, no importa lo que el resto de la `Color` es el valor. Lo único que importa es el canal alfa. Este código simplemente llama a la `WithAlpha` método en el valor predeterminado de la `Color` propiedad.

Mover el `Slider` disoluciones entre un mapa de bits y otra:

[![Mapa de bits disolver](transparency-images/BitmapDissolve.png "disolución de mapa de bits")](transparency-images/BitmapDissolve-Large.png#lightbox)

En los últimos artículos de varias, ha visto cómo usar SkiaSharp para dibujar texto, círculos, elipses, rectángulos redondeados y mapas de bits. El siguiente paso es [líneas de SkiaSharp y rutas de acceso](../paths/index.md) en que se obtendrá información sobre cómo dibujar líneas conectadas en una ruta de acceso de gráficos.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
