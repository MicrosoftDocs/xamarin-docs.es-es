---
title: Animar los mapas de bits de SkiaSharp
description: Obtenga información sobre cómo realizar una animación mapa de bits secuencialmente mostrando una serie de mapas de bits, y procesar archivos GIF animados.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97142ADC-E2FD-418C-8A09-9C561AEE5BFD
author: davidbritch
ms.author: dabritch
ms.date: 07/12/2018
ms.openlocfilehash: 74fb5bdc13facd52c06e8b7c2138d1d5db9d4565
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199509"
---
# <a name="animating-skiasharp-bitmaps"></a>Animar los mapas de bits de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Llamar las aplicaciones que animación gráficos SkiaSharp generalmente `InvalidateSurface` en la `SKCanvasView` con una tarifa fija, que a menudo cada 16 milisegundos. Invalidar la superficie desencadena una llamada a la `PaintSurface` controlador para volver a dibujar la pantalla. Como los objetos visuales se vuelven a dibujar 60 veces por segundo, aparecen animar sin problemas.

Sin embargo, si los gráficos son demasiado complejos para representarse en 16 milisegundos, la animación puede ser impreciso. El programador puede optar por reducir la frecuencia de actualización a 30 veces o 15 veces por segundo, pero a veces incluso no es suficiente. En ocasiones, los gráficos son tan complejos que simplemente no se puede representar en tiempo real.

Una solución consiste en preparar para la animación con antelación con la representación de los marcos individuales de la animación en una serie de mapas de bits. Para mostrar la animación, sólo es necesario mostrar estos mapas de bits secuencialmente 60 veces por segundo.

Por supuesto, que es potencialmente una gran cantidad de mapas de bits, pero que es 3D de gran presupuesto cómo se realizan las películas animadas. Los gráficos 3D son mucho demasiado complejos para representarse en tiempo real. Una gran cantidad de tiempo de procesamiento es necesario para representar cada fotograma. Lo que verá cuando vea la película es básicamente una serie de mapas de bits.

Puede hacer algo similar en SkiaSharp. En este artículo se muestra dos tipos de animación de mapa de bits. El primer ejemplo es una animación del conjunto de Mandelbrot:

![Ejemplo de animación](animating-images/AnimatingSample.png "animación de ejemplo")

El segundo ejemplo muestra cómo usar SkiaSharp para representar un archivo GIF animado.

## <a name="bitmap-animation"></a>Animación de mapa de bits

El conjunto de Mandelbrot es visualmente fascinante pero computionally largas. (Para obtener una explicación del conjunto Mandelbrot y las matemáticas que se usa aquí, consulte [capítulo 20 de _Creating Mobile Apps with Xamarin.Forms_ ](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf) inicial en la página 666. La siguiente descripción supone esa información en segundo plano).

El [ **Mandelbrot animación** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-mandelanima) ejemplo utiliza la animación de mapa de bits para simular un zoom continuo de un punto fijo en el conjunto de Mandelbrot. Acercar va seguido de alejar y, a continuación, el ciclo se repite indefinidamente o hasta que finalice el programa.

El programa se prepara para esta animación mediante la creación de hasta 50 mapas de bits que almacena en almacenamiento local para la aplicación. Cada mapa de bits abarca la mitad del ancho y alto del plano complejo como el mapa de bits anterior. (En el programa, se dice estos mapas de bits que representan integral _niveles de zoom_.) Los mapas de bits, a continuación, se muestran en la secuencia. La escala de cada mapa de bits se anima para proporcionar un avance sin problemas desde un mapa de bits a otro.

Al igual que el final del programa se describe en el capítulo 20 de _Creating Mobile Apps with Xamarin.Forms_, el cálculo del conjunto de Mandelbrot en **Mandelbrot animación** es un método asincrónico con ocho parámetros. Los parámetros incluyen un punto central complejas y un ancho y alto del plano complejo que rodean a ese punto central. Los siguientes tres parámetros son el ancho en píxeles y el alto del mapa de bits que se va a crear y un número máximo de iteraciones para el cálculo recursiva. El `progress` parámetro se usa para mostrar el progreso de este cálculo. El `cancelToken` parámetro no se usa en este programa:

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

El **Mandelbrot animación** archivo XAML incluye dos `Label` vistas, un `ProgressBar`y un `Button` , así como la `SKCanvasView`:

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

El archivo de código subyacente comienza definiendo tres constantes fundamentales y una matriz de mapas de bits:

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

En algún momento, probablemente quiera cambiar el `COUNT` valor con 50 para ver la gama completa de la animación. Los valores superiores a 50 no son útiles. En torno a un nivel de zoom de 48 o algo así, la resolución de números de punto flotante de precisión doble resultará insuficiente para el cálculo del conjunto de Mandelbrot. Este problema se describe en la página 684 de _Creating Mobile Apps with Xamarin.Forms_.

El `center` valor es muy importante. Este es el enfoque de zoom de la animación. Los tres valores en el archivo son los que se usan en las tres capturas de pantalla finales en el capítulo 20 de _Creating Mobile Apps with Xamarin.Forms_ en página 684, pero puede experimentar con el programa en dicho capítulo para proponer una de sus propios valores.

El **Mandelbrot animación** ejemplo almacena estos `COUNT` mapas de bits en el almacenamiento de la aplicación local. Los mapas de cincuenta bits requieren más de 20 megabytes de almacenamiento en el dispositivo, por lo que es posible que desee saber cuánto almacenamiento estos mapas de bits se ocupan y, en algún momento es posible que desee eliminarlos todos. Que es el propósito de estos dos métodos en la parte inferior de la `MainPage` clase:

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

Puede eliminar los mapas de bits en el almacenamiento local mientras el programa anima a los mapas de bits mismos porque el sistema conserva en memoria. Pero la próxima vez que ejecute el programa, será necesario volver a crear los mapas de bits.

Incorporan los mapas de bits almacenados en almacenamiento de la aplicación local el `center` valor en sus nombres de archivo, por lo que si cambia la `center` , los mapas de bits existente no se reemplazará en el almacenamiento y, además se seguirán ocupando espacio.

Estos son los métodos que `MainPage` utiliza para construir los nombres de archivo, así como un `MakePixel` método para definir un valor de píxel se basa en los componentes de color:

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

El `zoomLevel` parámetro `FilePath` intervalos de 0 a la `COUNT` constante menos 1.

El `MainPage` constructor llama a la `LoadAndStartAnimation` método:

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

El `LoadAndStartAnimation` método es responsable de obtener acceso a almacenamiento local para la aplicación para cargar mapas de bits que es posible que se han creado cuando el programa se ha ejecutado anteriormente. Recorre en bucle `zoomLevel` valores de 0 a `COUNT`. Si el archivo existe, carga en el `bitmaps` matriz. En caso contrario, necesita para crear un mapa de bits para la instancia concreta `center` y `zoomLevel` valores mediante una llamada a `Mandelbrot.CalculateAsync`. Ese método obtiene los recuentos de iteración para cada píxel, que este método se convierte en colores:

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

Tenga en cuenta que el sistema almacena estos mapas de bits en el almacenamiento de la aplicación local en lugar de en la biblioteca de fotografías del dispositivo. La biblioteca de .NET Standard 2.0 permite mediante la conocida `File.OpenRead` y `File.WriteAllBytes` métodos para esta tarea.

Después de que todos los mapas de bits se han creado o cargados en memoria, el método inicia una `Stopwatch` objeto y llama a `Device.StartTimer`. El `OnTimerTick` cada 16 milisegundos se llama al método.

`OnTimerTick` calcula un `time` valor en milisegundos que comprendido entre 0 y tiempos de 6000 `COUNT`, que prorratea seis segundos para la presentación de cada mapa de bits. El `progress` valor utiliza el `Math.Sin` para crear una animación sinusoidal que será más lenta al principio del ciclo de valor y más lento al final, tal como invierte la dirección.

El `progress` valor oscila entre 0 y `COUNT`. Esto significa que la parte entera de `progress` es un índice en el `bitmaps` matriz, mientras que la parte fraccionaria de `progress` indica un nivel de zoom para ese mapa de bits. Estos valores se almacenan en el `bitmapIndex` y `bitmapProgress` campos y se muestran en el `Label` y `Slider` en el archivo XAML. El `SKCanvasView` se invalida para actualizar la presentación del mapa de bits:

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

Por último, el `PaintSurface` controlador de la `SKCanvasView` calcula un rectángulo de destino para mostrar el mapa de bits tan grande como sea posible al tiempo que mantiene la relación de aspecto. Un rectángulo de origen se basa en el `bitmapProgress` valor. El `fraction` valor aquí calcula los intervalos de 0 cuando `bitmapProgress` es 0 para mostrar el mapa de bits completo a 0,25 cuando `bitmapProgress` es 1 para mostrar la mitad del ancho y alto del mapa de bits, acercar eficazmente:

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

Este es el programa que se ejecuta:

[![Animación de Mandelbrot](animating-images/MandelbrotAnimation.png "Mandelbrot animación")](animating-images/MandelbrotAnimation-Large.png#lightbox)

## <a name="gif-animation"></a>Animación de GIF

La especificación de formato de intercambio de gráficos (GIF) incluye una característica que permite un único archivo GIF incluir varios marcos secuenciales de una escena que se pueden mostrar en sucesión, a menudo en un bucle. Estos archivos se denominan _GIF animados_. Los exploradores Web pueden reproducir archivos GIF animados y SkiaSharp permite que una aplicación para extraer los marcos de un archivo GIF animado y para mostrarlos de manera secuencial.

El [SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) ejemplo incluye un recurso de GIF animado denominado **Newtons_cradle_animation_book_2.gif** creado por DemonDeLuxe y descargar desde el [base Newton en la ](https://en.wikipedia.org/wiki/Newton%27s_cradle) página en Wikipedia. El **GIF animado** página incluye un archivo XAML que proporciona esa información y crea una instancia de un `SKCanvasView`:

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

El archivo de código subyacente no se ha generalizado para reproducir cualquier archivo GIF animado. Parte de la información que está disponible, en particular omite un recuento de repetición y simplemente se reproduce el archivo GIF animado en un bucle.

El uso de SkisSharp para extraer los marcos de un archivo GIF animado no parece que se documentan en cualquier lugar, por lo que es más detallada de lo habitual la descripción del código que sigue:

La descodificación del archivo GIF animado se produce en el constructor de la página y requiere que el `Stream` objeto que hace referencia el mapa de bits se usa para crear un `SKManagedStream` objeto y, a continuación, un [ `SKCodec` ](xref:SkiaSharp.SKCodec) objeto. El [ `FrameCount` ](xref:SkiaSharp.SKCodec.FrameCount) propiedad indica el número de fotogramas que componen la animación.

Estos marcos finalmente se guardan como mapas de bits individuales, por lo que usa el constructor `FrameCount` para asignar una matriz de tipo `SKBitmap` , así como dos `int` matrices para la duración de cada fotograma y (para facilitar la lógica de animación) el acumulado duraciones.

El [ `FrameInfo` ](xref:SkiaSharp.SKCodec.FrameInfo) propiedad de `SKCodec` clase es una matriz de [ `SKCodecFrameInfo` ](xref:SkiaSharp.SKCodecFrameInfo) valores, uno para cada fotograma, pero lo único que toma este programa desde esa estructura es la [ `Duration` ](xref:SkiaSharp.SKCodecFrameInfo.Duration) del marco en milisegundos.

`SKCodec` define una propiedad denominada [ `Info` ](xref:SkiaSharp.SKCodec.Info) de tipo [ `SKImageInfo` ](xref:SkiaSharp.SKImageInfo), pero esa `SKImageInfo` indica el valor (mínimo para esta imagen) que es el tipo de color `SKColorType.Index8`, lo que significa que cada píxel es un índice en un tipo de color. Para evitar tener que lidiar con las tablas de color, el programa usa el [ `Width` ](xref:SkiaSharp.SKImageInfo.Width) y [ `Height` ](xref:SkiaSharp.SKImageInfo.Height) información de esa estructura que construirla es posee todo color `ImageInfo` valor. Cada `SKBitmap` se crea a partir del que.

El `GetPixels` método `SKBitmap` devuelve un `IntPtr` que hacen referencia a los bits de píxeles del mapa de bits. Estos bits de píxeles no se ha establecido aún. Que `IntPtr` se pasa a uno de los [ `GetPixels` ](xref:SkiaSharp.SKCodec.GetPixels(SkiaSharp.SKImageInfo,System.IntPtr,SkiaSharp.SKCodecOptions)) métodos de `SKCodec`. Ese método copiará el marco del archivo GIF en el espacio de memoria al que hace referencia el `IntPtr`. El [ `SKCodecOptions` ](xref:SkiaSharp.SKCodecOptions) constructor indica el número de fotograma:

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

A pesar de la `IntPtr` valor n `unsafe` código es necesario porque el `IntPtr` nunca se convierte en un valor de puntero de C#.

Después de haber extraído cada fotograma, el constructor totales de las duraciones de todos los marcos y, a continuación, inicializa otra matriz con las duraciones acumuladas.

El resto del archivo de código subyacente se dedica a la animación. El `Device.StartTimer` método se usa para iniciar un temporizador a y el `OnTimerTick` devolución de llamada usa un `Stopwatch` para determinar el tiempo transcurrido en milisegundos. Recorrer en iteración la matriz de duraciones acumulado es suficiente para encontrar el marco actual:

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

Cada vez que el `currentframe` los cambios de variables, la `SKCanvasView` se invalida y se muestra el nuevo marco:

[![GIF animado](animating-images/AnimatedGif.png "GIF animado")](animating-images/AnimatedGif-Large.png#lightbox)

Por supuesto, deseará ejecutar el programa para ver la animación.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Animación de Mandelbrot (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-mandelanima)
