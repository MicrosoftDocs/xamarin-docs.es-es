---
Título: "los degradados circulares SkiaSharp" Descripción: "Obtenga información sobre los diferentes tipos de degradados basados en círculos".
MS. Prod: Xamarin ms. Technology: Xamarin-skiasharp ms. AssetID: 400AE23A-6A0B-4FA8-BD6B-DE4146B04732 Author: davidbritch ms. Author: dabritch ms. Date: 08/23/2018 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="the-skiasharp-circular-gradients"></a>Los degradados circulares SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

La [`SKShader`](xref:SkiaSharp.SKShader) clase define métodos estáticos para crear cuatro tipos diferentes de degradados. El artículo sobre el [**degradado lineal SkiaSharp**](linear-gradient.md) describe el [`CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient*) método. En este artículo se tratan los otros tres tipos de degradados, que se basan en círculos.

El [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient*) método crea un degradado que emana del centro de un círculo:

![Ejemplo de degradado radial](circular-gradients-images/RadialGradientSample.png)

El [`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient*) método crea un degradado que se barre alrededor del centro de un círculo:

![Ejemplo de degradado de barrido](circular-gradients-images/SweepGradientSample.png)

El tercer tipo de degradado es bastante inusual. Se denomina degradado cónico de dos puntos y se define mediante el [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient*) método. El degradado se extiende de un círculo a otro:

![Ejemplo de gradiente cónico](circular-gradients-images/ConicalGradientSample.png)

Si los dos círculos tienen tamaños diferentes, el degradado adopta la forma de un cono.

En este artículo se analizan estos degradados con más detalle.

## <a name="the-radial-gradient"></a>Degradado radial

El [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) método tiene la siguiente sintaxis:

```csharp
public static SKShader CreateRadialGradient (SKPoint center, 
                                             Single radius, 
                                             SKColor[] colors, 
                                             Single[] colorPos, 
                                             SKShaderTileMode mode)
```

Una [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) sobrecarga también incluye un parámetro de matriz de transformación.

Los dos primeros argumentos especifican el centro de un círculo y un radio. El degradado comienza en ese centro y se extiende hacia afuera para los `radius` píxeles. Lo que sucede más allá `radius` depende del [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) argumento. El `colors` parámetro es una matriz de dos o más colores (al igual que en los métodos de degradado lineal) y `colorPos` es una matriz de enteros en el intervalo de 0 a 1. Estos enteros indican las posiciones relativas de los colores a lo largo de esa `radius` línea. Puede establecer ese argumento en `null` para espaciar equitativamente los colores.

Si usa `CreateRadialGradient` para rellenar un círculo, puede establecer el centro del degradado en el centro del círculo y el radio del degradado en el radio del círculo. En ese caso, el `SKShaderTileMode` argumento no tiene ningún efecto en la representación del degradado. Pero si el área rellenada por el degradado es mayor que el círculo definido por el degradado, el `SKShaderTileMode` argumento tiene un efecto profundo sobre lo que ocurre fuera del círculo.

El efecto de `SKShaderMode` se muestra en la página **gradiente radial** en el ejemplo [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . El archivo XAML de esta página crea una instancia de `Picker` que permite seleccionar uno de los tres miembros de la `SKShaderTileMode` enumeración:

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

El archivo de código subyacente colorea todo el lienzo con un degradado radial. El centro del degradado se establece en el centro del lienzo y el radio se establece en 100 píxeles. El degradado consta de solo dos colores, blanco y negro:

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

Este código crea un degradado con negro en el centro, que se desvanece gradualmente a blanco 100 píxeles desde el centro. Lo que sucede más allá de ese RADIUS depende del `SKShaderTileMode` argumento:

[![Degradado radial](circular-gradients-images/RadialGradient.png "Degradado radial")](circular-gradients-images/RadialGradient-Large.png#lightbox)

En los tres casos, el degradado rellena el lienzo. En la pantalla de iOS de la izquierda, el degradado más allá del radio continúa con el último color, que es blanco. Este es el resultado de `SKShaderTileMode.Clamp` . La pantalla de Android muestra el efecto de `SKShaderTileMode.Repeat` : en 100 píxeles desde el centro, el degradado comienza de nuevo con el primer color, que es negro. El degradado se repite cada 100 píxeles de radio. 

En la pantalla de Plataforma universal de Windows de la derecha `SKShaderTileMode.Mirror` se muestra cómo hace que los degradados alternen las direcciones. El primer degradado está desde el negro en el centro hasta el blanco en un radio de 100 píxeles. El siguiente es blanco desde el radio de 100 píxeles hasta el negro en un radio de 200 píxeles y se vuelve a invertir el siguiente degradado.

Puede usar más de dos colores en un degradado radial. El ejemplo de **degradado de arco de arco iris** crea una matriz de ocho colores que corresponden a los colores del arco iris y termina con rojo, además de una matriz de ocho valores de posición:

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

Suponga que el mínimo del ancho y el alto del lienzo es 1000, lo que significa que el `rainbowWidth` valor es 250. Los `outerRadius` `innerRadius` valores y se establecen en 1000 y 750, respectivamente. Estos valores se utilizan para calcular la `positions` matriz; los ocho valores van de 0,75 f a 1. El `radius` valor se usa para retornear el círculo. El valor 875 significa que el ancho del trazo de 250 píxeles se extiende entre el radio de 750 píxeles y el radio de 1000 píxeles:

[![Degradado de arco de arco iris](circular-gradients-images/RainbowArcGradient.png "Degradado de arco de arco iris")](circular-gradients-images/RainbowArcGradient-Large.png#lightbox)

Si ha rellenado todo el lienzo con este degradado, verá que es rojo dentro del radio interno. Esto se debe a que la `positions` matriz no empieza por 0. El primer color se usa para los desplazamientos de 0 a través del primer valor de matriz. El degradado también está en rojo más allá del radio exterior. Este es el resultado del `Clamp` modo de mosaico. Dado que el degradado se utiliza para contornear una línea gruesa, estas áreas rojas no están visibles.

## <a name="radial-gradients-for-masking"></a>Degradados radiales para enmascaramiento

Al igual que los degradados lineales, los degradados radiales pueden incorporar colores transparentes o parcialmente transparentes. Esta característica es útil para un proceso denominado _enmascaramiento_, que oculta parte de una imagen para acentuar otra parte de la imagen.

La página de la **máscara de degradado radial** muestra un ejemplo. El programa carga uno de los mapas de bits de recursos. Los `CENTER` `RADIUS` campos y se determinan a partir de un examen del mapa de bits y hacen referencia a un área que se debe resaltar. El `PaintSurface` controlador comienza por calcular un rectángulo para mostrar el mapa de bits y, a continuación, lo muestra en ese rectángulo:

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

Después de dibujar el mapa de bits, algunos códigos simples convierten `CENTER` y `RADIUS` en `center` y `radius` , que hacen referencia al área resaltada en el mapa de bits que se ha escalado y desplazado para su presentación. Estos valores se usan para crear un degradado radial con ese centro y RADIUS. Los dos colores empiezan en transparente en el centro y para el primer 60% del radio. A continuación, el degradado se atenúa hasta el blanco:

[![Máscara de degradado radial](circular-gradients-images/RadialGradientMask.png "Máscara de degradado radial")](circular-gradients-images/RadialGradientMask-Large.png#lightbox)

Este enfoque no es la mejor manera de enmascarar un mapa de bits. El problema es que la máscara tiene principalmente un color de blanco, que se eligió para coincidir con el fondo del lienzo. Si el fondo es algún otro color &mdash; o quizás un degradado en sí &mdash; , no coincidirá. En el artículo [SkiaSharp Porter-Duff Blend modes,](../blend-modes/porter-duff.md)se muestra un enfoque mejor para el enmascaramiento.

## <a name="radial-gradients-for-specular-highlights"></a>Degradados radiales para reflejos especulares

Cuando una luz tiene una superficie redondeada, refleja luz en muchas direcciones, pero parte de la luz se rebota directamente en el ojo del espectador. Esto a menudo crea la apariencia de una zona blanca aproximada en la superficie denominada _resaltado especular_.

En los gráficos tridimensionales, los reflejos especulares suelen ser el resultado de los algoritmos que se usan para determinar las rutas de acceso de luz y el sombreado. En los gráficos bidimensionales, a veces se agregan resaltes especulares para sugerir la apariencia de una superficie 3D. Un resaltado especular puede transformar un círculo rojo plano en una esfera circular roja.

La página **resaltado especular radial** usa un degradado radial para hacerlo exactamente. El `PaintSurface` controlador es el que calcula un radio para el círculo y dos `SKPoint` valores &mdash; a `center` y un `offCenter` que se encuentra a la mitad del centro y el borde superior izquierdo del círculo:

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

La `CreateRadialGradient` llamada crea un degradado que comienza en ese `offCenter` punto con blanco y termina con rojo a una distancia de la mitad del radio. Este es su aspecto:

[![Resaltado especular radial](circular-gradients-images/RadialSpecularHighlight.png "Resaltado especular radial")](circular-gradients-images/RadialSpecularHighlight-Large.png#lightbox)

Si observa detenidamente este degradado, puede decidir que se ha dado un error. El degradado se centra en torno a un punto determinado y es posible que desee que se tratara de un poco menos simétrico para reflejar la superficie redondeada. En ese caso, podría preferir el resaltado especular que se muestra a continuación en la sección [**gradientes cónicos para reflejos especulares**](#conical-gradients-for-specular-highlights).

## <a name="the-sweep-gradient"></a>El degradado de barrido

El [`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[])) método tiene la sintaxis más sencilla de todos los métodos de creación de degradado:

```csharp
public static SKShader CreateSweepGradient (SKPoint center, 
                                            SKColor[] colors, 
                                            Single[] colorPos)
```

Es simplemente un centro, una matriz de colores y las posiciones de color. El degradado comienza a la derecha del punto central y barre 360 grados en el sentido de las agujas del reloj alrededor del centro. Tenga en cuenta que no hay ningún `SKShaderTileMode` parámetro.

[`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKMatrix))También está disponible una sobrecarga con un parámetro de transformación de matriz. Puede aplicar una transformación de giro al degradado para cambiar el punto de partida. También puede aplicar una transformación de escala para cambiar la dirección de la derecha a la derecha.

La página de **degradado de barrido** usa un degradado de barrido para colorear un círculo con un ancho de trazo de 50 píxeles:

[![Degradado de barrido](circular-gradients-images/SweepGradient.png "Degradado de barrido")](circular-gradients-images/SweepGradient-Large.png#lightbox)

La `SweepGradientPage` clase define una matriz de ocho colores con distintos valores de matiz. Observe que la matriz comienza y termina con rojo (un valor de matiz de 0 o 360), que aparece en el extremo derecho de las capturas de pantallas:

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

El programa también implementa un `TapGestureRecognizer` que habilita código al final del `PaintSurface` controlador. Este código usa el mismo degradado para rellenar el lienzo:

[![Degradado de barrido completo](circular-gradients-images/SweepGradientFull.png "Degradado de barrido completo")](circular-gradients-images/SweepGradientFull-Large.png#lightbox)

Estas capturas de pantallas demuestran que el degradado rellena cualquier área en la que esté coloreada. Si el degradado no comienza y termina con el mismo color, habrá una discontinuidad a la derecha del punto central.

## <a name="the-two-point-conical-gradient"></a>Degradado cónico de dos puntos

El [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) método tiene la siguiente sintaxis:

```csharp
public static SKShader CreateTwoPointConicalGradient (SKPoint startCenter, 
                                                      Single startRadius, 
                                                      SKPoint endCenter, 
                                                      Single endRadius, 
                                                      SKColor[] colors, 
                                                      Single[] colorPos, 
                                                      SKShaderTileMode mode)
```

Los parámetros comienzan con puntos centrales y radios para dos círculos, denominados círculo de _Inicio_ y _fin_ de círculo. Los tres parámetros restantes son los mismos que para `CreateLinearGradient` y `CreateRadialGradient` . Una [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) sobrecarga incluye una transformación de matriz.

El degradado comienza en el círculo inicial y termina en el círculo final. El `SKShaderTileMode` parámetro rige lo que ocurre más allá de los dos círculos. El degradado cónico de dos puntos es el único degradado que no rellena completamente un área. Si los dos círculos tienen el mismo radio, el degradado se restringe a un rectángulo con un ancho que es igual que el diámetro de los círculos. Si los dos círculos tienen radios diferentes, el degradado forma un cono.

Es probable que desee experimentar con el degradado cónico de dos puntos, de modo que la página de **gradiente cónico** se derive de `InteractivePage` para permitir que se muevan dos puntos táctiles para los dos radios del círculo:

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

El archivo de código subyacente define los dos `TouchPoint` objetos con radios fijos de 50 y 100:

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

La `colors` matriz es rojo, verde y azul. El código hacia la parte inferior del `PaintSurface` controlador dibuja los dos puntos táctiles como círculos negros para que no obstruyan el degradado.

Observe que `DrawRect` la llamada usa el degradado para colorear todo el lienzo. No obstante, en el caso general, gran parte del lienzo permanece sin color por el degradado. Este es el programa que muestra tres configuraciones posibles:

[![Degradado cónico](circular-gradients-images/ConicalGradient.png "Degradado cónico")](circular-gradients-images/ConicalGradient-Large.png#lightbox)

En la pantalla de iOS de la izquierda se muestra el efecto de la `SKShaderTileMode` configuración de `Clamp` . El degradado comienza con rojo dentro del borde del círculo más pequeño que es opuesto al lado más cercano al segundo círculo. El `Clamp` valor también hace que el color rojo continúe hasta el punto del cono. El degradado termina con azul en el borde exterior del círculo más grande que está más cerca del primer círculo, pero continúa con el azul dentro de ese círculo y más allá.

La pantalla de Android es similar, pero con un `SKShaderTileMode` de `Repeat` . Ahora es más claro que el degradado comienza dentro del primer círculo y termina fuera del segundo círculo. La `Repeat` configuración hace que el degradado se repita de nuevo con rojo dentro del círculo más grande.

La pantalla de UWP muestra lo que sucede cuando el círculo más pequeño se mueve completamente dentro del círculo más grande. El degradado se detiene en un cono y, en su lugar, rellena todo el área. El efecto es similar al degradado radial, pero es asimétrico si el círculo más pequeño no se centra exactamente en el círculo más grande.

Podría dudar la utilidad práctica del degradado cuando un círculo está anidado en otro, pero es ideal para un resaltado especular.

## <a name="conical-gradients-for-specular-highlights"></a>Degradados cónicos para reflejos especulares

Anteriormente en este artículo vio cómo usar un degradado radial para crear un resaltado especular. También puede usar el degradado cónico de dos puntos para este propósito y podría preferir su aspecto:

[![Resaltado especular cónico](circular-gradients-images/ConicalSpecularHighlight.png "Resaltado especular cónico")](circular-gradients-images/ConicalSpecularHighlight-Large.png#lightbox)

El aspecto asimétrico sugiere mejor la superficie redondeada del objeto. 

El código de dibujo de la página **resaltada especulación cónica** es el mismo que el de la página de **resaltado especular radial** , excepto el sombreador:

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

Los dos círculos tienen centros de `offCenter` y `center` . El círculo centrado en `center` está asociado a un radio que abarca toda la bola, pero el círculo centrado en `offCenter` tiene un radio de un solo píxel. El degradado comienza efectivamente en ese punto y termina en el borde de la bola.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
