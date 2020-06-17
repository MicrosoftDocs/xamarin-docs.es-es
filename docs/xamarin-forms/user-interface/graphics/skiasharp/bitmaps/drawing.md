---
title: "crear y dibujar en mapas de bits de SkiaSharp" Descripción: "Aprenda a crear mapas de bits de SkiaSharp y, a continuación, dibuje en estos mapas de bits mediante la creación de un lienzo basado en ellos".
MS. Prod: Xamarin ms. Technology: Xamarin-skiasharp ms. AssetID: 79BD3266-D457-4E50-BDDF-33450035FA0F Author: davidbritch ms. Author: dabritch ms. Date: 07/17/2018 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="creating-and-drawing-on-skiasharp-bitmaps"></a>Crear y dibujar en mapas de bits de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Ha visto cómo una aplicación puede cargar mapas de bits desde Internet, desde recursos de aplicaciones y desde la biblioteca de fotos del usuario. También es posible crear mapas de bits nuevos dentro de la aplicación. El enfoque más sencillo implica a uno de los constructores de [`SKBitmap`](xref:SkiaSharp.SKBitmap.%23ctor(System.Int32,System.Int32,System.Boolean)) :

```csharp
SKBitmap bitmap = new SKBitmap(width, height);
```

Los `width` `height` parámetros y son enteros y especifican las dimensiones de píxeles del mapa de bits. Este constructor crea un mapa de bits de color completo con cuatro bytes por píxel: un byte para cada uno de los componentes rojo, verde, azul y alfa (opacidad).

Después de crear un nuevo mapa de bits, debe obtener algo en la superficie del mapa de bits. En general, esto se realiza de una de estas dos maneras:

- Dibuje en el mapa de bits mediante `Canvas` métodos de dibujo estándar.
- Acceso directo a los bits de píxeles.

En este artículo se muestra el primer enfoque:

![Ejemplo de dibujo](drawing-images/DrawingSample.png "Ejemplo de dibujo")

El segundo enfoque se describe en el artículo [**acceso a SkiaSharp Bitmap pixels**](pixel-bits.md).

## <a name="drawing-on-the-bitmap"></a>Dibujar en el mapa de bits

Dibujar en la superficie de un mapa de bits es igual que dibujar en una pantalla de vídeo. Para dibujar en una pantalla de vídeo, se obtiene un `SKCanvas` objeto de los `PaintSurface` argumentos de evento. Para dibujar en un mapa de bits, se crea un `SKCanvas` objeto mediante el [`SKCanvas`](xref:SkiaSharp.SKCanvas.%23ctor(SkiaSharp.SKBitmap)) constructor:

```csharp
SKCanvas canvas = new SKCanvas(bitmap);
```

Cuando haya terminado de dibujar en el mapa de bits, puede desechar el `SKCanvas` objeto. Por esta razón, `SKCanvas` se suele llamar al constructor en una `using` instrucción:

```csharp
using (SKCanvas canvas = new SKCanvas(bitmap))
{
    ··· // call drawing function
}
```

A continuación, se puede mostrar el mapa de bits. En un momento posterior, el programa puede crear un nuevo `SKCanvas` objeto basado en ese mismo mapa de bits y dibujarlo más.

La página de **mapa de bits de Hello** en la aplicación **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** escribe el texto "Hello, bitmap!" en un mapa de bits y, a continuación, muestra el mapa de bits varias veces.

El constructor de `HelloBitmapPage` comienza creando un `SKPaint` objeto para mostrar texto. Determina las dimensiones de una cadena de texto y crea un mapa de bits con esas dimensiones. A continuación, crea un `SKCanvas` objeto basado en ese mapa de bits, llama a `Clear` y, a continuación, llama a `DrawText` . Siempre es una buena idea llamar a `Clear` con un nuevo mapa de bits, ya que un mapa de bits recién creado podría contener datos aleatorios.

El constructor finaliza creando un `SKCanvasView` objeto para mostrar el mapa de bits:

```csharp
public partial class HelloBitmapPage : ContentPage
{
    const string TEXT = "Hello, Bitmap!";
    SKBitmap helloBitmap;

    public HelloBitmapPage()
    {
        Title = TEXT;

        // Create bitmap and draw on it
        using (SKPaint textPaint = new SKPaint { TextSize = 48 })
        {
            SKRect bounds = new SKRect();
            textPaint.MeasureText(TEXT, ref bounds);

            helloBitmap = new SKBitmap((int)bounds.Right,
                                       (int)bounds.Height);

            using (SKCanvas bitmapCanvas = new SKCanvas(helloBitmap))
            {
                bitmapCanvas.Clear();
                bitmapCanvas.DrawText(TEXT, 0, -bounds.Top, textPaint);
            }
        }

        // Create SKCanvasView to view result
        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Aqua);

        for (float y = 0; y < info.Height; y += helloBitmap.Height)
            for (float x = 0; x < info.Width; x += helloBitmap.Width)
            {
                canvas.DrawBitmap(helloBitmap, x, y);
            }
    }
}
```

El `PaintSurface` controlador representa el mapa de bits varias veces en filas y columnas de la pantalla. Observe que el `Clear` método del `PaintSurface` controlador tiene un argumento de `SKColors.Aqua` , que colorea el fondo de la superficie de la pantalla:

[![¡ Hola, mapa de bits!](drawing-images/HelloBitmap.png "¡ Hola, mapa de bits!")](drawing-images/HelloBitmap-Large.png#lightbox)

La apariencia del fondo aguamarina revela que el mapa de bits es transparente excepto para el texto.

## <a name="clearing-and-transparency"></a>Borrar y transparencia

La presentación de la página de **mapa de bits de Hola** muestra que el mapa de bits que creó el programa es transparente excepto para el texto negro. Este es el motivo por el que se muestra el color aguamarina de la superficie de la pantalla.

La documentación de los `Clear` métodos de los `SKCanvas` describe con la instrucción: "reemplaza todos los píxeles del lienzo ' clip actual '. El uso de la palabra "reemplaza" revela una característica importante de estos métodos: todos los métodos de dibujo de `SKCanvas` agregan algo a la superficie de pantalla existente. Los `Clear` métodos _reemplazan_ a lo que ya existe.

`Clear`existe en dos versiones diferentes:

- El [`Clear`](xref:SkiaSharp.SKCanvas.Clear(SkiaSharp.SKColor)) método con un `SKColor` parámetro reemplaza los píxeles de la superficie de presentación con píxeles de ese color.

- El [`Clear`](xref:SkiaSharp.SKCanvas.Clear) método sin parámetros reemplaza los píxeles por el [`SKColors.Empty`](xref:SkiaSharp.SKColors.Empty) color, que es un color en el que todos los componentes (rojo, verde, azul y alfa) se establecen en cero. Este color se conoce a veces como "negro transparente".

Llamar a `Clear` sin argumentos en un nuevo mapa de bits inicializa todo el mapa de bits para que sea completamente transparente. Todo lo que se dibuje posteriormente en el mapa de bits será opaco o parcialmente opaco.

Este es algo que probar: en la página de **mapa de bits de Hello** , reemplace el `Clear` método aplicado a `bitmapCanvas` por este:

```csharp
bitmapCanvas.Clear(new SKColor(255, 0, 0, 128));
```

El orden de los `SKColor` parámetros del constructor es rojo, verde, azul y alfa, donde cada valor puede oscilar entre 0 y 255. Tenga en cuenta que un valor alfa de 0 es transparente, mientras que un valor alfa de 255 es opaco.

El valor (255, 128 0,0) borra los píxeles del mapa de bits a píxeles rojos con una opacidad del 50%. Esto significa que el fondo del mapa de bits es semitransparente. El fondo rojo semitransparente del mapa de bits se combina con el fondo aguamarina de la superficie de la pantalla para crear un fondo gris.

Pruebe a establecer el color del texto en negro transparente colocando la siguiente asignación en el `SKPaint` inicializador:

```csharp
Color = new SKColor(0, 0, 0, 0)
```

Podría pensar que este texto transparente crearía áreas completamente transparentes del mapa de bits a través del cual vería el fondo aguamarina de la superficie de la pantalla. Pero esto no es así. El texto se dibuja encima de lo que ya está en el mapa de bits. El texto transparente no será visible en absoluto.

Ningún `Draw` método hace que un mapa de bits sea más transparente. `Clear`Esto solo puede hacerlo.

## <a name="bitmap-color-types"></a>Tipos de colores de mapa de bits

El constructor más sencillo `SKBitmap` le permite especificar un ancho y un alto de píxel de entero para el mapa de bits. Otros `SKBitmap` constructores son más complejos. Estos constructores requieren argumentos de dos tipos de enumeración: [`SKColorType`](xref:SkiaSharp.SKColorType) y [`SKAlphaType`](xref:SkiaSharp.SKAlphaType) . Otros constructores usan la [`SKImageInfo`](xref:SkiaSharp.SKImageInfo) estructura, que consolida esta información.

La `SKColorType` enumeración tiene 9 miembros. Cada uno de estos miembros describe una manera determinada de almacenar los píxeles del mapa de bits:

- `Unknown`
- `Alpha8`&mdash;cada píxel es de 8 bits, que representa un valor alfa de totalmente transparente a totalmente opaco.
- `Rgb565`&mdash;cada píxel es de 16 bits, 5 bits para rojo y azul, y 6 para verde
- `Argb4444`&mdash;cada píxel es de 16 bits, 4 para alfa, rojo, verde y azul
- `Rgba8888`&mdash;cada píxel es 32 bits, 8 cada uno para rojo, verde, azul y alfa.
- `Bgra8888`&mdash;cada píxel es 32 bits, 8 cada uno para azul, verde, rojo y alfa.
- `Index8`&mdash;cada píxel es de 8 bits y representa un índice en un[`SKColorTable`](xref:SkiaSharp.SKColorTable)
- `Gray8`&mdash;cada píxel es de 8 bits que representa un sombreado gris del negro al blanco
- `RgbaF16`&mdash;cada píxel es 64 bits, con rojo, verde, azul y alfa en un formato de punto flotante de 16 bits.

Los dos formatos en los que cada píxel es 32 píxeles (4 bytes) se denominan a menudo formatos _de color completo_ . Muchos de los otros formatos de fecha a partir de una hora en que el vídeo se muestra en sí mismos no eran capaces de color completo. Los mapas de bits de color limitado eran adecuados para estas pantallas y los mapas de bits permitidos ocupan menos espacio en la memoria.

Estos días, los programadores casi siempre usan mapas de bits de color completo y no se molestan en otros formatos. La excepción es el `RgbaF16` formato, que permite una mayor resolución de color que incluso los formatos de colores completos. Sin embargo, este formato se usa para fines especializados, como la creación de imágenes médicas, y no tiene mucho sentido cuando se usa con pantallas de color completo estándar.

Esta serie de artículos se restringirá a los `SKBitmap` formatos de color que se usan de forma predeterminada cuando no `SKColorType` se especifica ningún miembro. Este formato predeterminado se basa en la plataforma subyacente. En el caso de las plataformas admitidas por Xamarin.Forms , el tipo de color predeterminado es:

- `Rgba8888`para iOS y Android
- `Bgra8888`para UWP

La única diferencia es el orden de los 4 bytes en memoria y esto solo se convierte en un problema cuando se accede directamente a los bits de píxeles. Esto no será importante hasta que llegue al artículo [**acceso a los píxeles del mapa de bits de SkiaSharp**](pixel-bits.md).

La `SKAlphaType` enumeración tiene cuatro miembros:

- `Unknown`
- `Opaque`&mdash;el mapa de bits no tiene transparencia
- `Premul`&mdash;los componentes de color se multiplican previamente por el componente alfa
- `Unpremul`&mdash;los componentes de color no se multiplican previamente por el componente alfa

Este es un píxel de mapa de bits rojo de 4 bytes con una transparencia del 50%, con los bytes mostrados en el orden rojo, verde, azul, alfa:

0xFF 0x00 0x00 0x80

Cuando un mapa de bits que contiene píxeles semitransparentes se representa en una superficie de presentación, los componentes de color de cada píxel de mapa de bits se deben multiplicar por el valor alfa de ese píxel y los componentes de color del píxel correspondiente de la superficie de presentación se deben multiplicar por 255 menos el valor alfa. Los dos píxeles se pueden combinar a continuación. El mapa de bits se puede representar más rápido si el valor alfa ya mulitplied los componentes de color de los píxeles del mapa de bits. Ese mismo píxel rojo se almacenaría como este en un formato premultiplicado:

0x80 0x00 0x00 en 0x80

Esta mejora de rendimiento es el motivo por el `SkiaSharp` que los mapas de bits se crean de forma predeterminada con un `Premul` formato. Pero de nuevo, es necesario conocer esto solo cuando se tiene acceso a bits de píxeles y se manipulan.

## <a name="drawing-on-existing-bitmaps"></a>Dibujar en mapas de bits existentes

No es necesario crear un nuevo mapa de bits para dibujar en él. También puede dibujar en un mapa de bits existente.

La página **Moustache de Monkey** utiliza su constructor para cargar la imagen de **MonkeyFace.png** . A continuación, crea un `SKCanvas` objeto basado en dicho mapa de bits y usa `SKPaint` `SKPath` objetos y para dibujar un Moustache en él:

```csharp
public partial class MonkeyMoustachePage : ContentPage
{
    SKBitmap monkeyBitmap;

    public MonkeyMoustachePage()
    {
        Title = "Monkey Moustache";

        monkeyBitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MonkeyFace.png");

        // Create canvas based on bitmap
        using (SKCanvas canvas = new SKCanvas(monkeyBitmap))
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                paint.StrokeWidth = 24;
                paint.StrokeCap = SKStrokeCap.Round;

                using (SKPath path = new SKPath())
                {
                    path.MoveTo(380, 390);
                    path.CubicTo(560, 390, 560, 280, 500, 280);

                    path.MoveTo(320, 390);
                    path.CubicTo(140, 390, 140, 280, 200, 280);

                    canvas.DrawPath(path, paint);
                }
            }
        }

        // Create SKCanvasView to view result
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
        canvas.DrawBitmap(monkeyBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

El constructor finaliza creando un `SKCanvasView` cuyo `PaintSurface` controlador simplemente muestra el resultado:

[![Monkey Moustache](drawing-images/MonkeyMoustache.png "Monkey Moustache")](drawing-images/MonkeyMoustache-Large.png#lightbox)

## <a name="copying-and-modifying-bitmaps"></a>Copiar y modificar mapas de bits

Los métodos de `SKCanvas` que puede usar para dibujar en un mapa de bits incluyen `DrawBitmap` . Esto significa que puede dibujar un mapa de bits en otro, lo que normalmente lo modifica de alguna manera.

La forma más versátil de modificar un mapa de bits es obtener acceso a los bits de píxeles reales, un asunto que se trata en el artículo sobre el **[acceso a los píxeles del mapa](pixel-bits.md)** de bits SkiaSharp. Pero hay muchas otras técnicas para modificar los mapas de bits que no requieren el acceso a los bits de píxeles.

El siguiente mapa de bits incluido con la aplicación **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** tiene 360 píxeles de ancho y 480 píxeles de alto:

![Montañas rocosas](drawing-images/MountainClimbers.jpg "Montañas rocosas")

Supongamos que no ha recibido el permiso del Monkey de la izquierda para publicar esta fotografía. Una solución consiste en ocultar la superficie del Monkey con una técnica llamada _pixelización_. Los píxeles de la superficie se reemplazan por bloques de color para que no pueda crear las características. Los bloques de color normalmente se derivan de la imagen original al calcular el promedio de los colores de los píxeles correspondientes a estos bloques. Pero no es necesario realizar esta promediación por sí mismo. Se produce automáticamente cuando se copia un mapa de bits en una dimensión de píxeles más pequeña.

La superficie del Monkey izquierdo ocupa aproximadamente un área cuadrada de 72 píxeles con una esquina superior izquierda en el punto (112, 238). Vamos a reemplazar el área cuadrada de 72 píxeles con una matriz de bloques de color 9 por 9, cada una de las cuales tiene un cuadrado de 8 por 8 píxeles.

La página de **imagen de píxel** se carga en ese mapa de bits y crea primero un mapa de bits de 9 píxeles reducido denominado `faceBitmap` . Se trata de un destino para copiar solo la superficie del Monkey. El rectángulo de destino tiene solo 9 píxeles cuadrados, pero el rectángulo de origen tiene 72 píxeles cuadrados. Cada bloque de píxeles de origen de 8 por 8 se consolida en un solo píxel al calcular el promedio de los colores.

El siguiente paso consiste en copiar el mapa de bits original en un nuevo mapa de bits del mismo tamaño denominado `pixelizedBitmap` . `faceBitmap`Después, el diminuto se copia en la parte superior con un rectángulo de destino de 72 píxeles cuadrados para que cada píxel de `faceBitmap` se expanda a 8 veces su tamaño:

```csharp
public class PixelizedImagePage : ContentPage
{
    SKBitmap pixelizedBitmap;

    public PixelizedImagePage ()
    {
        Title = "Pixelize Image";

        SKBitmap originalBitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

        // Create tiny bitmap for pixelized face
        SKBitmap faceBitmap = new SKBitmap(9, 9);

        // Copy subset of original bitmap to that
        using (SKCanvas canvas = new SKCanvas(faceBitmap))
        {
            canvas.Clear();
            canvas.DrawBitmap(originalBitmap,
                              new SKRect(112, 238, 184, 310),   // source
                              new SKRect(0, 0, 9, 9));          // destination

        }

        // Create full-sized bitmap for copy
        pixelizedBitmap = new SKBitmap(originalBitmap.Width, originalBitmap.Height);

        using (SKCanvas canvas = new SKCanvas(pixelizedBitmap))
        {
            canvas.Clear();

            // Draw original in full size
            canvas.DrawBitmap(originalBitmap, new SKPoint());

            // Draw tiny bitmap to cover face
            canvas.DrawBitmap(faceBitmap,
                              new SKRect(112, 238, 184, 310));  // destination
        }

        // Create SKCanvasView to view result
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
        canvas.DrawBitmap(pixelizedBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

El constructor finaliza creando un `SKCanvasView` para mostrar el resultado:

[![Imagen de píxel](drawing-images/PixelizeImage.png "Imagen de píxel")](drawing-images/PixelizeImage-Large.png#lightbox)

## <a name="rotating-bitmaps"></a>Rotar mapas de bits

Otra tarea común es rotar mapas de bits. Esto es especialmente útil cuando se recuperan mapas de bits de una biblioteca de fotos de iPhone o iPad. A menos que el dispositivo se haya mantenido en una orientación determinada cuando se tomó la foto, es probable que la imagen esté al revés o a un lado.

Al activar un mapa de bits hacia abajo, es necesario crear otro mapa de bits del mismo tamaño que el primero y, a continuación, establecer una transformación para que rote en 180 grados mientras se copia el primero en el segundo. En todos los ejemplos de esta sección, `bitmap` es el `SKBitmap` objeto que necesita girar:

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(180, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

Al rotar en 90 grados, debe crear un mapa de bits que tenga un tamaño diferente del original intercambiando el alto y el ancho. Por ejemplo, si el mapa de bits original tiene 1200 píxeles de ancho y 800 píxeles de alto, el mapa de bits girado tiene un ancho de 800 píxeles y 1200 píxeles de ancho. Establezca la traslación y la rotación para que el mapa de bits se rote alrededor de la esquina superior izquierda y, a continuación, se desplace hacia la vista. (Tenga en cuenta que `Translate` `RotateDegrees` se llama a los métodos y en el orden opuesto a la manera en que se aplican). Este es el código para rotar 90 grados en el sentido de las agujas del reloj:

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.Translate(bitmap.Height, 0);
    canvas.RotateDegrees(90);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

Y esta es una función similar para rotar 90 grados en el sentido contrario a las agujas del reloj:

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.Translate(0, bitmap.Width);
    canvas.RotateDegrees(-90);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

Estos dos métodos se usan en las páginas de **rompecabezas fotográfico** descritas en el artículo [**recortar mapas de bits de SkiaSharp**](cropping.md#cropping-skiasharp-bitmaps).

Un programa que permite al usuario girar un mapa de bits en incrementos de 90 grados solo necesita implementar una función para la rotación de 90 grados. Después, el usuario puede girar en cualquier incremento de 90 grados repitiendo la ejecución de esta función.

Un programa también puede girar un mapa de bits en cualquier cantidad. Un enfoque sencillo es modificar la función que gira en 180 grados reemplazando 180 por una variable generalizada `angle` :

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(angle, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

Sin embargo, en el caso general, esta lógica recortará las esquinas del mapa de bits girado. Un enfoque más adecuado consiste en calcular el tamaño del mapa de bits girado mediante trigonometría para incluir esas esquinas.

Este trigonometría se muestra en la página de **rotación de mapas de bits** . El archivo XAML crea una instancia `SKCanvasView` de y un `Slider` que puede oscilar entre 0 y 360 grados con un que `Label` muestra el valor actual:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.BitmapRotatorPage"
             Title="Bitmap Rotator">
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="slider"
                Maximum="360"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='Rotate by {0:F0}&#x00B0;'}"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

El archivo de código subyacente carga un recurso de mapa de bits y lo guarda como un campo estático de solo lectura denominado `originalBitmap` . El mapa de bits mostrado en el `PaintSurface` controlador es `rotatedBitmap` , que se establece inicialmente en `originalBitmap` :

```csharp
public partial class BitmapRotatorPage : ContentPage
{
    static readonly SKBitmap originalBitmap =
        BitmapExtensions.LoadBitmapResource(typeof(BitmapRotatorPage),
            "SkiaSharpFormsDemos.Media.Banana.jpg");

    SKBitmap rotatedBitmap = originalBitmap;

    public BitmapRotatorPage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(rotatedBitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        double angle = args.NewValue;
        double radians = Math.PI * angle / 180;
        float sine = (float)Math.Abs(Math.Sin(radians));
        float cosine = (float)Math.Abs(Math.Cos(radians));
        int originalWidth = originalBitmap.Width;
        int originalHeight = originalBitmap.Height;
        int rotatedWidth = (int)(cosine * originalWidth + sine * originalHeight);
        int rotatedHeight = (int)(cosine * originalHeight + sine * originalWidth);

        rotatedBitmap = new SKBitmap(rotatedWidth, rotatedHeight);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear(SKColors.LightPink);
            canvas.Translate(rotatedWidth / 2, rotatedHeight / 2);
            canvas.RotateDegrees((float)angle);
            canvas.Translate(-originalWidth / 2, -originalHeight / 2);
            canvas.DrawBitmap(originalBitmap, new SKPoint());
        }

        canvasView.InvalidateSurface();
    }
}
```

El `ValueChanged` controlador de `Slider` realiza las operaciones que crean un nuevo `rotatedBitmap` basándose en el ángulo de rotación. El nuevo ancho y alto se basan en los valores absolutos de senos y los pares de ancho y alto originales. Las transformaciones que se usan para dibujar el mapa de bits original en el mapa de bits girado mueven el centro de mapas de bits original al origen, después lo giran en el número de grados especificado y, a continuación, traducen ese centro al centro del mapa de bits girado. (Los `Translate` `RotateDegrees` métodos y se llaman en el orden contrario al de cómo se aplican).

Observe el uso del `Clear` método para hacer que el fondo de `rotatedBitmap` una rosa clara. Esto es únicamente para ilustrar el tamaño de `rotatedBitmap` en la pantalla:

[![Rotador de mapa de bits](drawing-images/BitmapRotator.png "Rotador de mapa de bits")](drawing-images/BitmapRotator-Large.png#lightbox)

El mapa de bits girado es lo suficientemente grande como para incluir el mapa de bits original completo, pero no mayor.

## <a name="flipping-bitmaps"></a>Voltear mapas de bits

Otra operación que se realiza normalmente en mapas de bits se denomina _volteo_. Conceptualmente, el mapa de bits gira en tres dimensiones en torno a un eje vertical o eje horizontal a través del centro del mapa de bits. El volteo vertical crea una imagen reflejada.

La página de **impulsor de mapa de bits** en la aplicación **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** muestra estos procesos. El archivo XAML contiene `SKCanvasView` y dos botones para voltearlos vertical y horizontalmente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.BitmapFlipperPage"
             Title="Bitmap Flipper">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Button Text="Flip Vertical"
                Grid.Row="1" Grid.Column="0"
                Margin="0, 10"
                Clicked="OnFlipVerticalClicked" />

        <Button Text="Flip Horizontal"
                Grid.Row="1" Grid.Column="1"
                Margin="0, 10"
                Clicked="OnFlipHorizontalClicked" />
    </Grid>
</ContentPage>
```

El archivo de código subyacente implementa estas dos operaciones en los `Clicked` controladores de los botones:

```csharp
public partial class BitmapFlipperPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(BitmapRotatorPage),
            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public BitmapFlipperPage()
    {
        InitializeComponent();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnFlipVerticalClicked(object sender, ValueChangedEventArgs args)
    {
        SKBitmap flippedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

        using (SKCanvas canvas = new SKCanvas(flippedBitmap))
        {
            canvas.Clear();
            canvas.Scale(-1, 1, bitmap.Width / 2, 0);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = flippedBitmap;
        canvasView.InvalidateSurface();
    }

    void OnFlipHorizontalClicked(object sender, ValueChangedEventArgs args)
    {
        SKBitmap flippedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

        using (SKCanvas canvas = new SKCanvas(flippedBitmap))
        {
            canvas.Clear();
            canvas.Scale(1, -1, 0, bitmap.Height / 2);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = flippedBitmap;
        canvasView.InvalidateSurface();
    }
}
```

El volteo vertical se logra mediante una transformación de escalado con un factor de escala horizontal de &ndash; 1. El centro de escala es el centro vertical del mapa de bits. El volteo horizontal es una transformación de escala con un factor de escala vertical de &ndash; 1.

Como puede ver a partir de la letra invertida en la camisa de la Monkey, el volteo no es lo mismo que la rotación. Pero como se muestra en la captura de pantalla de UWP de la derecha, el volteo horizontal y vertical es igual que la rotación de 180 grados:

[![Impulsor de mapa de bits](drawing-images/BitmapFlipper.png "Impulsor de mapa de bits")](drawing-images/BitmapFlipper-Large.png#lightbox)

Otra tarea común que se puede controlar mediante técnicas similares es recortando un mapa de bits a un subconjunto rectangular. Esto se describe en el siguiente artículo [**recortar mapas de bits de SkiaSharp**](cropping.md).

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
