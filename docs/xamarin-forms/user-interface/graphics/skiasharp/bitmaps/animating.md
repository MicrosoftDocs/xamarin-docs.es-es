---
title: "animar mapas de bits de SkiaSharp" Descripción: "Obtenga información sobre cómo realizar la animación de mapas de bits mediante una visualización secuencial de una serie de mapas de bits y la representación de archivos GIF animados".
MS. Prod: Xamarin ms. Technology: Xamarin-skiasharp ms. AssetID: 97142ADC-E2FD-418C-8A09-9C561AEE5BFD Author: davidbritch ms. Author: dabritch ms. Date: 07/12/2018 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="animating-skiasharp-bitmaps"></a>Animar mapas de bits SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Las aplicaciones que animan gráficos SkiaSharp generalmente llaman a `InvalidateSurface` en `SKCanvasView` una velocidad fija, a menudo cada 16 milisegundos. Al invalidar la superficie, se desencadena una llamada al `PaintSurface` controlador para volver a dibujar la pantalla. A medida que los objetos visuales se vuelven a dibujar 60 veces por segundo, parecen estar animados sin problemas.

Sin embargo, si los gráficos son demasiado complejos para representarlos en 16 milisegundos, la animación puede llegar a ser vibrante. El programador puede optar por reducir la frecuencia de actualización a 30 veces o 15 veces por segundo, pero a veces esto no es suficiente. A veces, los gráficos son tan complejos que simplemente no se pueden representar en tiempo real.

Una solución es preparar la animación de antemano representando los marcos individuales de la animación en una serie de mapas de bits. Para mostrar la animación, solo es necesario mostrar estos mapas de bits secuencialmente 60 veces por segundo.

Por supuesto, eso es potencialmente una gran cantidad de mapas de bits, pero es la forma en que se realizan las películas animadas 3D de gran presupuesto. Los gráficos 3D son mucho demasiado complejos para representarlos en tiempo real. Se requiere mucho tiempo de procesamiento para representar cada fotograma. Lo que se ve al ver la película es esencialmente una serie de mapas de bits.

Puede hacer algo similar en SkiaSharp. En este artículo se muestran dos tipos de animación de mapa de bits. El primer ejemplo es una animación del conjunto de Mandelbrot:

![Animar ejemplo](animating-images/AnimatingSample.png "Animar ejemplo")

En el segundo ejemplo se muestra cómo usar SkiaSharp para representar un archivo GIF animado.

## <a name="bitmap-animation"></a>Animación de mapa de bits

El conjunto Mandelbrot es visualmente fascinante pero computionally largo. (Para obtener una explicación del conjunto de Mandelbrot y las matemáticas que se usan aquí, consulte el [capítulo 20 de _creación de Mobile Apps con Xamarin. Forms_ ](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf) a partir de la página 666. En la descripción siguiente se da por supuesto que el conocimiento en segundo plano).

En el ejemplo de [**animación de Mandelbrot**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-mandelanima) se usa la animación de mapas de bits para simular un zoom continuo de un punto fijo en el conjunto de Mandelbrot. El zoom se hace seguido del zoom y, a continuación, el ciclo se repite siempre o hasta que el programa finaliza.

El programa se prepara para esta animación creando hasta 50 mapas de bits que almacena en el almacenamiento local de la aplicación. Cada mapa de bits abarca la mitad del ancho y el alto del plano complejo como mapa de bits anterior. (En el programa, se dice que estos mapas de bits representan _niveles de zoom_enteros). Los mapas de bits se muestran en la secuencia. El escalado de cada mapa de bits se anima para proporcionar una progresión fluida de un mapa de bits a otro.

Al igual que el último programa descrito en el capítulo 20 de _creación de Mobile Apps con Xamarin. Forms_, el cálculo del conjunto de Mandelbrot en la **animación de Mandelbrot** es un método asincrónico con ocho parámetros. Los parámetros incluyen un punto central complejo y un ancho y alto del plano complejo que rodea ese punto central. Los tres parámetros siguientes son el ancho y el alto de píxel del mapa de bits que se va a crear y un número máximo de iteraciones para el cálculo recursivo. El `progress` parámetro se usa para mostrar el progreso de este cálculo. El `cancelToken` parámetro no se usa en este programa:

```csharp
static class Mandelbrot
{
    public static Task<BitmapInfo> CalculateAsync(Complex center,
                                                  double width, double height,
                                                  int pixelWidth, int pixelHeight,
                                                  int iterations,
                                                  IProgress<double> progress,
                                                  CancellationToken cancelToken)
    {
        return Task.Run(() =>
        {
            int[] iterationCounts = new int[pixelWidth * pixelHeight];
            int index = 0;

            for (int row = 0; row < pixelHeight; row++)
            {
                progress.Report((double)row / pixelHeight);
                cancelToken.ThrowIfCancellationRequested();

                double y = center.Imaginary + height / 2 - row * height / pixelHeight;

                for (int col = 0; col < pixelWidth; col++)
                {
                    double x = center.Real - width / 2 + col * width / pixelWidth;
                    Complex c = new Complex(x, y);

                    if ((c - new Complex(-1, 0)).Magnitude < 1.0 / 4)
                    {
                        iterationCounts[index++] = -1;
                    }
                    // http://www.reenigne.org/blog/algorithm-for-mandelbrot-cardioid/
                    else if (c.Magnitude * c.Magnitude * (8 * c.Magnitude * c.Magnitude - 3) < 3.0 / 32 - c.Real)
                    {
                        iterationCounts[index++] = -1;
                    }
                    else
                    {
                        Complex z = 0;
                        int iteration = 0;

                        do
                        {
                            z = z * z + c;
                            iteration++;
                        }
                        while (iteration < iterations && z.Magnitude < 2);

                        if (iteration == iterations)
                        {
                            iterationCounts[index++] = -1;
                        }
                        else
                        {
                            iterationCounts[index++] = iteration;
                        }
                    }
                }
            }
            return new BitmapInfo(pixelWidth, pixelHeight, iterationCounts);
        }, cancelToken);
    }
}
```

El método devuelve un objeto de tipo `BitmapInfo` que proporciona información para crear un mapa de bits:

```csharp
class BitmapInfo
{
    public BitmapInfo(int pixelWidth, int pixelHeight, int[] iterationCounts)
    {
        PixelWidth = pixelWidth;
        PixelHeight = pixelHeight;
        IterationCounts = iterationCounts;
    }

    public int PixelWidth { private set; get; }

    public int PixelHeight { private set; get; }

    public int[] IterationCounts { private set; get; }
}
```

El archivo XAML de **animación de Mandelbrot** incluye dos `Label` vistas, a `ProgressBar` y a, así `Button` como a `SKCanvasView` :

```csharp
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="MandelAnima.MainPage"
             Title="Mandelbrot Animation">

    <StackLayout>
        <Label x:Name="statusLabel"
               HorizontalTextAlignment="Center" />
        <ProgressBar x:Name="progressBar" />

        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <StackLayout Orientation="Horizontal"
                     Padding="5">
            <Label x:Name="storageLabel"
                   VerticalOptions="Center" />

            <Button x:Name="deleteButton"
                    Text="Delete All"
                    HorizontalOptions="EndAndExpand"
                    Clicked="OnDeleteButtonClicked" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

El archivo de código subyacente comienza definiendo tres constantes cruciales y una matriz de mapas de bits:

```csharp
public partial class MainPage : ContentPage
{
    const int COUNT = 10;           // The number of bitmaps in the animation.
                                    // This can go up to 50!

    const int BITMAP_SIZE = 1000;   // Program uses square bitmaps exclusively

    // Uncomment just one of these, or define your own
    static readonly Complex center = new Complex(-1.17651152924355, 0.298520986549558);
    //   static readonly Complex center = new Complex(-0.774693089457127, 0.124226621261617);
    //   static readonly Complex center = new Complex(-0.556624880053304, 0.634696788141351);

    SKBitmap[] bitmaps = new SKBitmap[COUNT];   // array of bitmaps
    ···
}
```

En algún momento, es probable que desee cambiar el `COUNT` valor a 50 para ver el intervalo completo de la animación. Los valores por encima de 50 no son útiles. En torno a un nivel de zoom de 48 o por lo tanto, la resolución de números de punto flotante de precisión doble es insuficiente para el cálculo del conjunto de Mandelbrot. Este problema se describe en la página 684 de _creación de Mobile Apps con Xamarin. Forms_.

El `center` valor es muy importante. Este es el foco del zoom de animación. Los tres valores del archivo son los que se usan en las tres capturas de pantallas finales del capítulo 20 de _creación de Mobile Apps con Xamarin. Forms_ en la página 684, pero puede experimentar con el programa de ese capítulo para obtener uno de sus propios valores.

El ejemplo de **animación de Mandelbrot** almacena estos `COUNT` mapas de bits en el almacenamiento de la aplicación local. 50 los mapas de bits requieren más de 20 megabytes de almacenamiento en el dispositivo, por lo que es posible que desee saber cuánto almacenamiento están ocupando estos mapas de bits y, en algún momento, puede que desee eliminarlos todos. Este es el propósito de estos dos métodos en la parte inferior de la `MainPage` clase:

```csharp
public partial class MainPage : ContentPage
{
    ···
    void TallyBitmapSizes()
    {
        long fileSize = 0;

        foreach (string filename in Directory.EnumerateFiles(FolderPath()))
        {
            fileSize += new FileInfo(filename).Length;
        }

        storageLabel.Text = $"Total storage: {fileSize:N0} bytes";
    }

    void OnDeleteButtonClicked(object sender, EventArgs args)
    {
        foreach (string filepath in Directory.EnumerateFiles(FolderPath()))
        {
            File.Delete(filepath);
        }

        TallyBitmapSizes();
    }
}
```

Puede eliminar los mapas de bits en el almacenamiento local mientras el programa está animando los mismos mapas de bits porque el programa los conserva en la memoria. Pero la próxima vez que ejecute el programa, tendrá que volver a crear los mapas de bits.

Los mapas de bits almacenados en el almacenamiento de la aplicación local incorporan el `center` valor en sus nombres de archivo, por lo que si cambia la `center` configuración, los mapas de bits existentes no se reemplazarán en el almacenamiento y seguirán ocupando espacio.

Estos son los métodos que `MainPage` usa para construir los nombres de archivo, así como un `MakePixel` método para definir un valor de píxel basado en componentes de color:

```csharp
public partial class MainPage : ContentPage
{
    ···
    // File path for storing each bitmap in local storage
    string FolderPath() =>
        Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData);

    string FilePath(int zoomLevel) =>
        Path.Combine(FolderPath(),
                     String.Format("R{0}I{1}Z{2:D2}.png", center.Real, center.Imaginary, zoomLevel));

    // Form bitmap pixel for Rgba8888 format
    uint MakePixel(byte alpha, byte red, byte green, byte blue) =>
        (uint)((alpha << 24) | (blue << 16) | (green << 8) | red);
    ···
}
```

El `zoomLevel` parámetro va `FilePath` de 0 a la `COUNT` constante menos 1.

El `MainPage` constructor llama al `LoadAndStartAnimation` método:

```csharp
public partial class MainPage : ContentPage
{
    ···
    public MainPage()
    {
        InitializeComponent();

        LoadAndStartAnimation();
    }
    ···
}
```

El `LoadAndStartAnimation` método es responsable de tener acceso al almacenamiento local de la aplicación para cargar los mapas de bits que puedan haberse creado cuando el programa se ejecutó anteriormente. Recorre `zoomLevel` los valores de 0 a `COUNT` . Si el archivo existe, lo carga en la `bitmaps` matriz. De lo contrario, debe crear un mapa de bits para los `center` valores y concretos llamando `zoomLevel` a `Mandelbrot.CalculateAsync` . Ese método obtiene los recuentos de iteraciones para cada píxel, que este método convierte en colores:

```csharp
public partial class MainPage : ContentPage
{
    ···
    async void LoadAndStartAnimation()
    {
        // Show total bitmap storage
        TallyBitmapSizes();

        // Create progressReporter for async operation
        Progress<double> progressReporter =
            new Progress<double>((double progress) => progressBar.Progress = progress);

        // Create (unused) CancellationTokenSource for async operation
        CancellationTokenSource cancelTokenSource = new CancellationTokenSource();

        // Loop through all the zoom levels
        for (int zoomLevel = 0; zoomLevel < COUNT; zoomLevel++)
        {
            // If the file exists, load it
            if (File.Exists(FilePath(zoomLevel)))
            {
                statusLabel.Text = $"Loading bitmap for zoom level {zoomLevel}";

                using (Stream stream = File.OpenRead(FilePath(zoomLevel)))
                {
                    bitmaps[zoomLevel] = SKBitmap.Decode(stream);
                }
            }
            // Otherwise, create a new bitmap
            else
            {
                statusLabel.Text = $"Creating bitmap for zoom level {zoomLevel}";

                CancellationToken cancelToken = cancelTokenSource.Token;

                // Do the (generally lengthy) Mandelbrot calculation
                BitmapInfo bitmapInfo =
                    await Mandelbrot.CalculateAsync(center,
                                                    4 / Math.Pow(2, zoomLevel),
                                                    4 / Math.Pow(2, zoomLevel),
                                                    BITMAP_SIZE, BITMAP_SIZE,
                                                    (int)Math.Pow(2, 10), progressReporter, cancelToken);

                // Create bitmap & get pointer to the pixel bits
                SKBitmap bitmap = new SKBitmap(BITMAP_SIZE, BITMAP_SIZE, SKColorType.Rgba8888, SKAlphaType.Opaque);
                IntPtr basePtr = bitmap.GetPixels();

                // Set pixel bits to color based on iteration count
                for (int row = 0; row < bitmap.Width; row++)
                    for (int col = 0; col < bitmap.Height; col++)
                    {
                        int iterationCount = bitmapInfo.IterationCounts[row * bitmap.Width + col];
                        uint pixel = 0xFF000000;            // black

                        if (iterationCount != -1)
                        {
                            double proportion = (iterationCount / 32.0) % 1;
                            byte red = 0, green = 0, blue = 0;

                            if (proportion < 0.5)
                            {
                                red = (byte)(255 * (1 - 2 * proportion));
                                blue = (byte)(255 * 2 * proportion);
                            }
                            else
                            {
                                proportion = 2 * (proportion - 0.5);
                                green = (byte)(255 * proportion);
                                blue = (byte)(255 * (1 - proportion));
                            }

                            pixel = MakePixel(0xFF, red, green, blue);
                        }

                        // Calculate pointer to pixel
                        IntPtr pixelPtr = basePtr + 4 * (row * bitmap.Width + col);

                        unsafe     // requires compiling with unsafe flag
                        {
                            *(uint*)pixelPtr.ToPointer() = pixel;
                        }
                    }

                // Save as PNG file
                SKData data = SKImage.FromBitmap(bitmap).Encode();

                try
                {
                    File.WriteAllBytes(FilePath(zoomLevel), data.ToArray());
                }
                catch
                {
                    // Probably out of space, but just ignore
                }

                // Store in array
                bitmaps[zoomLevel] = bitmap;

                // Show new bitmap sizes
                TallyBitmapSizes();
            }

            // Display the bitmap
            bitmapIndex = zoomLevel;
            canvasView.InvalidateSurface();
        }

        // Now start the animation
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }
    ···
}
```

Tenga en cuenta que el programa almacena estos mapas de bits en el almacenamiento de aplicaciones local en lugar de en la biblioteca fotográfica del dispositivo. La biblioteca .NET Standard 2,0 permite usar los `File.OpenRead` métodos y conocidos `File.WriteAllBytes` para esta tarea.

Una vez que todos los mapas de bits se han creado o cargado en la memoria, el método inicia un `Stopwatch` objeto y llama a `Device.StartTimer` . `OnTimerTick`Se llama al método cada 16 milisegundos.

`OnTimerTick`calcula un `time` valor en milisegundos comprendido entre 0 y 6000 veces `COUNT` , lo que apportions seis segundos para la presentación de cada mapa de bits. El `progress` valor usa el `Math.Sin` valor para crear una animación sinusoidal que será más lenta al principio del ciclo y más lenta al final a medida que invierte la dirección.

El `progress` valor va de 0 a `COUNT` . Esto significa que la parte entera de `progress` es un índice de la `bitmaps` matriz, mientras que la parte fraccionaria de `progress` indica un nivel de zoom para ese mapa de bits determinado. Estos valores se almacenan en `bitmapIndex` los `bitmapProgress` campos y, y se muestran en `Label` y `Slider` en el archivo XAML. `SKCanvasView`Se invalida para actualizar la presentación del mapa de bits:

```csharp
public partial class MainPage : ContentPage
{
    ···
    Stopwatch stopwatch = new Stopwatch();      // for the animation
    int bitmapIndex;
    double bitmapProgress = 0;
    ···
    bool OnTimerTick()
    {
        int cycle = 6000 * COUNT;       // total cycle length in milliseconds

        // Time in milliseconds from 0 to cycle
        int time = (int)(stopwatch.ElapsedMilliseconds % cycle);

        // Make it sinusoidal, including bitmap index and gradation between bitmaps
        double progress = COUNT * 0.5 * (1 + Math.Sin(2 * Math.PI * time / cycle - Math.PI / 2));

        // These are the field values that the PaintSurface handler uses
        bitmapIndex = (int)progress;
        bitmapProgress = progress - bitmapIndex;

        // It doesn't often happen that we get up to COUNT, but an exception would be raised
        if (bitmapIndex < COUNT)
        {
            // Show progress in UI
            statusLabel.Text = $"Displaying bitmap for zoom level {bitmapIndex}";
            progressBar.Progress = bitmapProgress;

            // Update the canvas
            canvasView.InvalidateSurface();
        }

        return true;
    }
    ···
}
```

Por último, el `PaintSurface` controlador de `SKCanvasView` calcula un rectángulo de destino para mostrar el mapa de bits lo más grande posible mientras se mantiene la relación de aspecto. Un rectángulo de origen se basa en el `bitmapProgress` valor. El `fraction` valor que se calcula aquí oscila entre 0 cuando `bitmapProgress` es 0 para mostrar todo el mapa de bits, hasta 0,25 cuando `bitmapProgress` es 1 para mostrar la mitad del ancho y el alto del mapa de bits, con lo que se amplía de forma eficaz:

```csharp
public partial class MainPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        if (bitmaps[bitmapIndex] != null)
        {
            // Determine destination rect as square in canvas
            int dimension = Math.Min(info.Width, info.Height);
            float x = (info.Width - dimension) / 2;
            float y = (info.Height - dimension) / 2;
            SKRect destRect = new SKRect(x, y, x + dimension, y + dimension);

            // Calculate source rectangle based on fraction:
            //  bitmapProgress == 0: full bitmap
            //  bitmapProgress == 1: half of length and width of bitmap
            float fraction = 0.5f * (1 - (float)Math.Pow(2, -bitmapProgress));
            SKBitmap bitmap = bitmaps[bitmapIndex];
            int width = bitmap.Width;
            int height = bitmap.Height;
            SKRect sourceRect = new SKRect(fraction * width, fraction * height,
                                           (1 - fraction) * width, (1 - fraction) * height);

            // Display the bitmap
            canvas.DrawBitmap(bitmap, sourceRect, destRect);
        }
    }
    ···
}
```

Esta es la ejecución del programa:

[![Animación de Mandelbrot](animating-images/MandelbrotAnimation.png "Animación de Mandelbrot")](animating-images/MandelbrotAnimation-Large.png#lightbox)

## <a name="gif-animation"></a>Animación GIF

La especificación del formato de intercambio de gráficos (GIF) incluye una característica que permite que un solo archivo GIF contenga varios marcos secuenciales de una escena que se pueden mostrar en sucesión, a menudo en un bucle. Estos archivos se conocen como archivos _GIF animados_. Los exploradores Web pueden reproducir archivos GIF animados y SkiaSharp permite a una aplicación extraer los fotogramas de un archivo GIF animado y mostrarlos secuencialmente.

En el ejemplo [SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) se incluye un recurso GIF animado denominado **Newtons_cradle_animation_book_2.gif** creado por DemonDeLuxe y que se descarga de la página de [base de Newton](https://en.wikipedia.org/wiki/Newton%27s_cradle) en Wikipedia. La página **GIF animada** incluye un archivo XAML que proporciona esa información y crea una instancia de `SKCanvasView` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.AnimatedGifPage"
             Title="Animated GIF">

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="0"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Label Text="GIF file by DemonDeLuxe from Wikipedia Newton's Cradle page"
               Grid.Row="1"
               Margin="0, 5"
               HorizontalTextAlignment="Center" />
    </Grid>
</ContentPage>
```

El archivo de código subyacente no se generaliza para reproducir ningún archivo GIF animado. Omite parte de la información que está disponible, en particular un recuento de repetición y simplemente reproduce el GIF animado en un bucle.

El uso de SkisSharp para extraer los marcos de un archivo GIF animado no parece estar documentado en ningún lugar, por lo que la descripción del código siguiente es más detallada que la habitual:

La descodificación del archivo GIF animado se produce en el constructor de la página y requiere que `Stream` se utilice el objeto que hace referencia al mapa de bits para crear un `SKManagedStream` objeto y, a continuación, un [`SKCodec`](xref:SkiaSharp.SKCodec) objeto. La [`FrameCount`](xref:SkiaSharp.SKCodec.FrameCount) propiedad indica el número de fotogramas que componen la animación.

Estos marcos se guardan finalmente como mapas de bits individuales, por lo que el constructor utiliza `FrameCount` para asignar una matriz de tipo, así `SKBitmap` como dos `int` matrices para la duración de cada fotograma y (para facilitar la lógica de animación) de las duraciones acumuladas.

La [`FrameInfo`](xref:SkiaSharp.SKCodec.FrameInfo) propiedad de la `SKCodec` clase es una matriz de [`SKCodecFrameInfo`](xref:SkiaSharp.SKCodecFrameInfo) valores, uno para cada fotograma, pero lo único que este programa toma de esa estructura es el [`Duration`](xref:SkiaSharp.SKCodecFrameInfo.Duration) del marco en milisegundos.

`SKCodec`define una propiedad denominada [`Info`](xref:SkiaSharp.SKCodec.Info) de tipo [`SKImageInfo`](xref:SkiaSharp.SKImageInfo) , pero ese `SKImageInfo` valor indica (al menos para esta imagen) que el tipo de color es `SKColorType.Index8` , lo que significa que cada píxel es un índice en un tipo de color. Para evitar molestarse con las tablas de color, el programa utiliza la [`Width`](xref:SkiaSharp.SKImageInfo.Width) [`Height`](xref:SkiaSharp.SKImageInfo.Height) información y de esa estructura para construir su propio valor de color completo `ImageInfo` . Cada `SKBitmap` se crea a partir de ese.

El `GetPixels` método de `SKBitmap` devuelve un `IntPtr` que hace referencia a los bits de píxel de ese mapa de bits. Estos bits de píxeles aún no se han establecido. Que `IntPtr` se pasa a uno de los [`GetPixels`](xref:SkiaSharp.SKCodec.GetPixels(SkiaSharp.SKImageInfo,System.IntPtr,SkiaSharp.SKCodecOptions)) métodos de `SKCodec` . Ese método copia el marco del archivo GIF en el espacio de memoria al que hace referencia `IntPtr` . El [`SKCodecOptions`](xref:SkiaSharp.SKCodecOptions) constructor indica el número de marco:

```csharp
public partial class AnimatedGifPage : ContentPage
{
    SKBitmap[] bitmaps;
    int[] durations;
    int[] accumulatedDurations;
    int totalDuration;
    ···

    public AnimatedGifPage ()
    {
        InitializeComponent ();

        string resourceID = "SkiaSharpFormsDemos.Media.Newtons_cradle_animation_book_2.gif";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        using (SKManagedStream skStream = new SKManagedStream(stream))
        using (SKCodec codec = SKCodec.Create(skStream))
        {
            // Get frame count and allocate bitmaps
            int frameCount = codec.FrameCount;
            bitmaps = new SKBitmap[frameCount];
            durations = new int[frameCount];
            accumulatedDurations = new int[frameCount];

            // Note: There's also a RepetitionCount property of SKCodec not used here

            // Loop through the frames
            for (int frame = 0; frame < frameCount; frame++)
            {
                // From the FrameInfo collection, get the duration of each frame
                durations[frame] = codec.FrameInfo[frame].Duration;

                // Create a full-color bitmap for each frame
                SKImageInfo imageInfo = code.new SKImageInfo(codec.Info.Width, codec.Info.Height);
                bitmaps[frame] = new SKBitmap(imageInfo);

                // Get the address of the pixels in that bitmap
                IntPtr pointer = bitmaps[frame].GetPixels();

                // Create an SKCodecOptions value to specify the frame
                SKCodecOptions codecOptions = new SKCodecOptions(frame, false);

                // Copy pixels from the frame into the bitmap
                codec.GetPixels(imageInfo, pointer, codecOptions);
            }

            // Sum up the total duration
            for (int frame = 0; frame < durations.Length; frame++)
            {
                totalDuration += durations[frame];
            }

            // Calculate the accumulated durations
            for (int frame = 0; frame < durations.Length; frame++)
            {
                accumulatedDurations[frame] = durations[frame] +
                    (frame == 0 ? 0 : accumulatedDurations[frame - 1]);
            }
        }
    }
    ···
}
```

A pesar del `IntPtr` valor, no `unsafe` se requiere ningún código porque `IntPtr` nunca se convierte en un valor de puntero de C#.

Una vez extraído cada fotograma, el constructor totaliza las duraciones de todos los marcos y, a continuación, inicializa otra matriz con las duraciones acumuladas.

El resto del archivo de código subyacente está dedicado a la animación. El `Device.StartTimer` método se usa para iniciar un temporizador y la devolución de `OnTimerTick` llamada usa un `Stopwatch` objeto para determinar el tiempo transcurrido en milisegundos. Recorrer la matriz duraciones acumuladas es suficiente para encontrar el fotograma actual:

```csharp
public partial class AnimatedGifPage : ContentPage
{
    SKBitmap[] bitmaps;
    int[] durations;
    int[] accumulatedDurations;
    int totalDuration;

    Stopwatch stopwatch = new Stopwatch();
    bool isAnimating;

    int currentFrame;
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
        int msec = (int)(stopwatch.ElapsedMilliseconds % totalDuration);
        int frame = 0;

        // Find the frame based on the elapsed time
        for (frame = 0; frame < accumulatedDurations.Length; frame++)
        {
            if (msec < accumulatedDurations[frame])
            {
                break;
            }
        }

        // Save in a field and invalidate the SKCanvasView.
        if (currentFrame != frame)
        {
            currentFrame = frame;
            canvasView.InvalidateSurface();
        }

        return isAnimating;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        // Get the bitmap and center it
        SKBitmap bitmap = bitmaps[currentFrame];
        canvas.DrawBitmap(bitmap,info.Rect, BitmapStretch.Uniform);
    }
}
```

Cada vez que `currentframe` cambia la variable, `SKCanvasView` se invalida y se muestra el nuevo marco:

[![GIF animado](animating-images/AnimatedGif.png "GIF animado")](animating-images/AnimatedGif-Large.png#lightbox)

Por supuesto, querrá ejecutar el programa usted mismo para ver la animación.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Animación de Mandelbrot (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-mandelanima)
