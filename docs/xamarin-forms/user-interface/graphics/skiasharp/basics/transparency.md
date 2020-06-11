---
Título: "transparencia SkiaSharp" Descripción: "usar transparencia para combinar varios objetos en una sola escena".
MS. Prod: Xamarin ms. Technology: Xamarin-skiasharp ms. AssetID: B62F9487-C30E-4C63-BAB1-4C091FF50378 Author: davidbritch ms. Author: dabritch ms. Date: 08/23/2018 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="skiasharp-transparency"></a>Transparencia SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Como ha visto, la [`SKPaint`](xref:SkiaSharp.SKPaint) clase incluye una [`Color`](xref:SkiaSharp.SKPaint.Color) propiedad de tipo [`SKColor`](xref:SkiaSharp.SKColor) . `SKColor`incluye un canal alfa, por lo que todo lo que se colorea con un `SKColor` valor puede ser parcialmente transparente. 

Se ha mostrado cierta transparencia en la [**animación básica en el artículo de SkiaSharp**](animation.md) . En este artículo se profundiza en la transparencia para combinar varios objetos en una sola escena, una técnica que a veces se conoce como _fusión_. En los artículos de la sección [**sombreadores SkiaSharp**](../effects/shaders/index.md) se describen técnicas de combinación más avanzadas.

Puede establecer el nivel de transparencia al crear por primera vez un color con el constructor de cuatro parámetros [`SKColor`](xref:SkiaSharp.SKColor.%23ctor(System.Byte,System.Byte,System.Byte,System.Byte)) :

```csharp
SKColor (byte red, byte green, byte blue, byte alpha);
```

Un valor alfa de 0 es completamente transparente y un valor alfa de 0xFF es totalmente opaco. Los valores entre esos dos extremos crean colores parcialmente transparentes.

Además, `SKColor` define un método útil [`WithAlpha`](xref:SkiaSharp.SKColor.WithAlpha*) que crea un nuevo color a partir de un color existente, pero con el nivel alfa especificado:

```csharp
SKColor halfTransparentBlue = SKColors.Blue.WithAlpha(0x80);
```

El uso de texto parcialmente transparente se muestra en la página código **más** en el ejemplo de [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . En esta página se atenúan dos cadenas de texto y se extraen mediante la incorporación de transparencia en los `SKColor` valores:

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

El `transparency` campo está animado para variar de 0 a 1 y de nuevo en un ritmo sinusoidal. La primera cadena de texto se muestra con un valor alfa calculado restando el `transparency` valor de 1:

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * (1 - transparency)));
```

El [`WithAlpha`](xref:SkiaSharp.SKColor.WithAlpha*) método establece el componente alfa en un color existente, que aquí es `SKColors.Blue` . La segunda cadena de texto usa un valor alfa calculado a partir del `transparency` propio valor:

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * transparency));
```

La animación alterna entre las dos palabras, urging al usuario "Code More" (o quizás solicitando "más código"):

[![Código más código](transparency-images/CodeMoreCode.png "Código más código")](transparency-images/CodeMoreCode-Large.png#lightbox)

En el artículo anterior sobre [**conceptos básicos de mapas de bits en SkiaSharp**](bitmaps.md), vio cómo mostrar mapas de bits con uno de los [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap*) métodos de `SKCanvas` . Todos los `DrawBitmap` métodos incluyen un `SKPaint` objeto como último parámetro. De forma predeterminada, este parámetro se establece en `null` y puede pasarlo por alto. 

Como alternativa, puede establecer la `Color` propiedad de este `SKPaint` objeto para mostrar un mapa de bits con algún nivel de transparencia. Establecer un nivel de transparencia en la `Color` propiedad de `SKPaint` permite atenuar los mapas de bits hacia dentro y hacia fuera, o bien para disolver un mapa de bits en otro. 

La transparencia de mapa de bits se muestra en la página de **resolución de mapa de bits** . El archivo XAML crea una instancia de un `SKCanvasView` y un `Slider` :

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

El archivo de código subyacente carga dos recursos de mapa de bits. Estos mapas de bits no tienen el mismo tamaño, pero tienen la misma relación de aspecto:

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

La `Color` propiedad del `SKPaint` objeto se establece en dos niveles alfa complementarios para los dos mapas de bits. Cuando `SKPaint` se usa con mapas de bits, no importa cuál sea el resto del `Color` valor. Lo único que importa es el canal alfa. Aquí el código simplemente llama al `WithAlpha` método en el valor predeterminado de la `Color` propiedad.

El movimiento de se `Slider` resuelve entre un mapa de bits y el otro:

[![Solución de mapa de bits](transparency-images/BitmapDissolve.png "Solución de mapa de bits")](transparency-images/BitmapDissolve-Large.png#lightbox)

En los últimos artículos, ha aprendido a usar SkiaSharp para dibujar texto, círculos, elipses, rectángulos redondeados y mapas de bits. El siguiente paso es [SkiaSharp líneas y rutas](../paths/index.md) de acceso en las que aprenderá a dibujar líneas conectadas en una ruta de gráficos.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
