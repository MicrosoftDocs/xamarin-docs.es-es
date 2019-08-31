---
title: Crear y dibujar en mapas de bits de SkiaSharp
description: Aprenda a crear mapas de bits de SkiaSharp y, a continuación, dibuje en estos mapas de bits mediante la creación de un lienzo basado en ellas.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 79BD3266-D457-4E50-BDDF-33450035FA0F
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 68d6cb1df8557b6055feb81b21ed5513592c71c4
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198143"
---
# <a name="creating-and-drawing-on-skiasharp-bitmaps"></a>Crear y dibujar en mapas de bits de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Ha visto cómo una aplicación puede cargar los mapas de bits de la Web, de recursos de aplicación y de biblioteca de fotos del usuario. También es posible crear mapas de bits nuevo dentro de la aplicación. El enfoque más sencillo implica uno de los constructores de [ `SKBitmap` ](xref:SkiaSharp.SKBitmap.%23ctor(System.Int32,System.Int32,System.Boolean)):

```csharp
SKBitmap bitmap = new SKBitmap(width, height);
```

El `width` y `height` parámetros son números enteros y especificar las dimensiones en píxeles del mapa de bits. Este constructor crea un mapa de bits de color completo con cuatro bytes por píxel: un byte para el rojo, verde, azul y los componentes alfa (opacidad). 

Después de crear un nuevo mapa de bits, deberá obtener algo en la superficie del mapa de bits. Por lo general hacer esto de dos maneras:

- Dibujar en el mapa de bits mediante el estándar `Canvas` métodos de dibujo.
- Obtener acceso a los bits de píxeles directamente.

En este artículo se muestra el primer enfoque:

![Ejemplo de dibujo](drawing-images/DrawingSample.png "ejemplo de dibujo")

El segundo enfoque se describe en el artículo [ **acceso a píxeles de mapa de bits de SkiaSharp**](pixel-bits.md).

## <a name="drawing-on-the-bitmap"></a>Dibujar en el mapa de bits

Dibujar en la superficie de un mapa de bits es igual a dibujar en una pantalla de vídeo. Para dibujar en una pantalla de vídeo, obtendrá un `SKCanvas` objeto desde el `PaintSurface` argumentos de evento. Para dibujar en un mapa de bits, se crea un `SKCanvas` objeto utilizando el [ `SKCanvas` ](xref:SkiaSharp.SKCanvas.%23ctor(SkiaSharp.SKBitmap)) constructor:

```csharp
SKCanvas canvas = new SKCanvas(bitmap);
```

Cuando haya terminado de dibujar en el mapa de bits, puede desechar el `SKCanvas` objeto. Por este motivo, el `SKCanvas` generalmente se denomina constructor en un `using` instrucción:

```csharp
using (SKCanvas canvas = new SKCanvas(bitmap))
{
    ··· // call drawing function
}
```

A continuación, se puede mostrar el mapa de bits. En un momento posterior, puede crear un nuevo programa `SKCanvas` objeto basado en mismo mapa de bits y dibuje en él algo más.

El **mapa de bits Hello** página en el **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** aplicación escribe el texto "Hola, mapa de bits" en un mapa de bits y luego se muestra que varias veces de mapa de bits.  

El constructor de la `HelloBitmapPage` comienza creando una `SKPaint` objeto para mostrar texto. Determina las dimensiones de una cadena de texto y crea un mapa de bits con dichas dimensiones. A continuación, se crea un `SKCanvas` objeto basado en ese mapa de bits, las llamadas `Clear`y, a continuación, llama a `DrawText`. Siempre es una buena idea para llamar a `Clear` con un nuevo mapa de bits porque un mapa de bits recién creada podría contener datos aleatorios.

El constructor concluye mediante la creación de un `SKCanvasView` objeto para mostrar el mapa de bits:

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

El `PaintSurface` controlador procesa el mapa de bits varias veces en filas y columnas de la pantalla. Tenga en cuenta que el `Clear` método en el `PaintSurface` controlador tiene un argumento de `SKColors.Aqua`, que los colores del fondo de la superficie de presentación:

[![Hola, mapa de bits ](drawing-images/HelloBitmap.png "Hello, mapa de bits.")](drawing-images/HelloBitmap-Large.png#lightbox)

La apariencia del fondo aguamarina revela que el mapa de bits es transparente, excepto el texto.

## <a name="clearing-and-transparency"></a>Borrar y transparencia

La presentación de la **mapa de bits Hello** página se muestra que el mapa de bits el programa creado es transparente, excepto el texto de color negro. Por eso el color aguamarina de la superficie de pantalla se muestra a través.

La documentación de los `Clear` métodos de `SKCanvas` los describe con la instrucción: "Reemplaza todos los píxeles del lienzo ' clip actual '. El uso de la palabra "reemplaza" revela una característica importante de estos métodos: Todos los métodos de dibujo `SKCanvas` de agregan algo a la superficie de pantalla existente. El `Clear` métodos _reemplazar_ lo que está allí.

`Clear` existe en dos versiones diferentes: 

- El [ `Clear` ](xref:SkiaSharp.SKCanvas.Clear(SkiaSharp.SKColor)) método con un `SKColor` parámetro reemplaza los píxeles de la superficie de pantalla con píxeles de ese color.

- El [ `Clear` ](xref:SkiaSharp.SKCanvas.Clear) método sin parámetros reemplaza los píxeles con el [ `SKColors.Empty` ](xref:SkiaSharp.SKColors.Empty) color, que es un color en el que todos los componentes (rojo, verde, azul y alfa) se establecen en cero. Este color se conoce a veces como "negro transparente".

Una llamada a `Clear` sin argumentos en un mapa de bits nuevo inicializa el mapa de bits completo para que sea totalmente transparente. Normalmente, nada dibujado posteriormente en el mapa de bits será opaco o parcialmente opaco.

Este es algo que probar: En la página de **mapa de bits** de `Clear` Hello, reemplace el `bitmapCanvas` método aplicado a por este:

```csharp
bitmapCanvas.Clear(new SKColor(255, 0, 0, 128));
```

El orden de los `SKColor` los parámetros del constructor es rojo, verde, azul y alfa, donde cada valor puede oscilar entre 0 y 255. Tenga en cuenta que un valor alfa de 0 es transparente, mientras que un valor alfa de 255 es opaco.

El valor (255, 0, 0, 128) borra los píxeles del mapa de bits en píxeles rojo con un 50% de opacidad. Esto significa que el fondo de mapa de bits es semitransparente. El fondo rojo semitransparente del mapa de bits se combina con el fondo de la superficie de pantalla para crear un fondo gris aguamarina.

Intente establecer el color del texto en negro transparente, colocando la siguiente asignación el `SKPaint` inicializador:

```csharp
Color = new SKColor(0, 0, 0, 0)
```

Podría pensar que este texto transparente crearía totalmente transparentes áreas del mapa de bits a través del cual se vería el fondo de la superficie de visualización aguamarina. Es decir, pero no así. El texto se dibuja encima de lo que ya está en el mapa de bits. No estará visible en todo el texto transparente.

No `Draw` método alguna vez realiza un mapa de bits más transparente. Solo `Clear` puede hacerlo.

## <a name="bitmap-color-types"></a>Tipos de colores de mapa de bits

El más sencillo `SKBitmap` constructor le permite especificar un ancho de píxel de entero y el alto del mapa de bits. Otros `SKBitmap` constructores son más complejos. Estos constructores requieren argumentos de los dos tipos de enumeración: [ `SKColorType` ](xref:SkiaSharp.SKColorType) y [ `SKAlphaType` ](xref:SkiaSharp.SKAlphaType). Otros constructores utilizan el [ `SKImageInfo` ](xref:SkiaSharp.SKImageInfo) estructura, que consolida toda esta información.

El `SKColorType` enumeración tiene 9 miembros. Cada uno de estos miembros describe una manera determinada de almacenamiento de los píxeles del mapa de bits:

- `Unknown`
- `Alpha8` &mdash; cada píxel es de 8 bits, que representa un valor alfa de totalmente transparente a totalmente opaco
- `Rgb565` &mdash; cada píxel es de 16 bits, 5 bits para el rojo y azul y 6 para el verde
- `Argb4444` &mdash; cada píxel tiene 4 alfa, rojo, verde y azul de 16 bits
- `Rgba8888` &mdash; cada píxel es de 32 bits, 8 para rojo, verde, azul y alfa
- `Bgra8888` &mdash; cada píxel es de 32 bits, 8 para alfa, rojo, verde y azul
- `Index8` &mdash; cada píxel es de 8 bits y representa un índice en una [`SKColorTable`](xref:SkiaSharp.SKColorTable)
- `Gray8` &mdash; cada píxel es de 8 bits que representa una sombra gris, del negro a blanco
- `RgbaF16` &mdash; cada píxel es 64 bits, con rojo, verde, azul y alfa en un formato de punto flotante de 16 bits

Los dos formatos donde cada píxel es 32 píxeles (4 bytes) a menudo se denominan _todo color_ formatos. Muchos de la fecha de otros formatos de un momento cuando se muestra el vídeo a sí mismos no eran capaces de a todo color. Mapas de bits de color limitado eran las adecuadas para estas pantallas y permiten a los mapas de bits ocupar menos espacio en la memoria. 

En la actualidad, los programadores casi siempre usar mapas de bits de color completo y no se moleste con otros formatos. La excepción es el `RgbaF16` formato, lo que permite una mayor resolución de color que incluso los formatos a todo color. Sin embargo, este formato se usa para propósitos especializados, como imágenes médicas y no tiene mucho sentido cuando se usa con las pantallas todo color estándar.

Esta serie de artículos se restringirá a sí misma para la `SKBitmap` color formatos usados de forma predeterminada cuando no hay ninguna `SKColorType` se especifica el miembro. Este formato de forma predeterminada se basa en la plataforma subyacente. Para las plataformas compatibles con Xamarin.Forms, el tipo de color predeterminado es:

- `Rgba8888` para iOS y Android
- `Bgra8888` para UWP

La única diferencia es el orden de los 4 bytes en la memoria, y esto solo resulta un problema cuando se accede directamente a los bits de píxeles. Esto no se vuelven importante hasta llegar al artículo [ **acceso a píxeles de mapa de bits de SkiaSharp**](pixel-bits.md).

El `SKAlphaType` enumeración tiene cuatro miembros:

- `Unknown`
- `Opaque` &mdash; el mapa de bits no tiene transparencia
- `Premul` &mdash; los componentes de color se multiplican previamente por el componente alfa
- `Unpremul` &mdash; los componentes de color no se multiplican previamente por el componente alfa

Este es un píxel del mapa de bits de color rojo de 4 bytes con una transparencia del 50%, con los bytes que se muestra en el rojo de orden, verde, azul, alfa:

0xFF 0x00 0x00 0x80

Cuando se representa un mapa de bits que contiene píxeles parcialmente transparentes en una superficie de pantalla, los componentes de color de cada píxel del mapa de bits deben ser multiplicados por el valor alfa de ese píxel y se deben multiplicar los componentes de color del píxel correspondiente de la superficie de pantalla 255 menos el valor alfa. A continuación, se pueden combinar los dos píxeles. El mapa de bits se puede representar con mayor rapidez si los componentes de color en los píxeles del mapa de bits ya han sido pre-mulitplied por el valor alfa. Ese mismo píxel rojo se almacenaría similar al siguiente en un formato premultiplicado:

0x80 0x00 0x00 0x80

Esta mejora del rendimiento es por eso `SkiaSharp` mapas de bits de forma predeterminada se crean con un `Premul` formato. Pero, nuevamente, es necesario saber esto únicamente al obtener acceso y manipular los bits de píxeles.

## <a name="drawing-on-existing-bitmaps"></a>Dibujar en mapas de bits existente

No es necesario crear un nuevo mapa de bits para dibujar en él. También puede dibujar en un mapa de bits existente. 

El **Monkey Moustache** página usa el constructor para cargar el **MonkeyFace.png** imagen. A continuación, se crea un `SKCanvas` objeto basándose en ese mapa de bits y utiliza `SKPaint` y `SKPath` objetos que se va a dibujar un moustache en él:

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

El constructor concluye mediante la creación de un `SKCanvasView` cuyo `PaintSurface` controlador simplemente muestra el resultado:

[![Juguetear Moustache](drawing-images/MonkeyMoustache.png "juguetear Moustache")](drawing-images/MonkeyMoustache-Large.png#lightbox)

## <a name="copying-and-modifying-bitmaps"></a>Copiar y modificar los mapas de bits

Los métodos de `SKCanvas` que puede usar para dibujar en un mapa de bits incluyen `DrawBitmap`. Esto significa que puede dibujar un mapa de bits en el otro, normalmente se modifica de alguna manera.

La forma más versátil para modificar un mapa de bits es a través de acceso a los bits de píxeles reales, un asunto tratado en el artículo  **[píxeles del mapa de bits de SkiaSharp acceso a](pixel-bits.md)** . Pero hay muchas otras técnicas para modificar los mapas de bits que no necesitan acceso a los bits de píxeles.

El siguiente mapa de bits incluido con el **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** aplicación es 360 píxeles de ancho y 480 píxeles de alto:

![Alpinistas](drawing-images/MountainClimbers.jpg "alpinistas")

Supongamos que no ha recibido el permiso de objeto monkey a la izquierda para publicar esta fotografía. Una solución consiste en Ocultar la cara del objeto monkey mediante una técnica denominada _pixelization_. Los píxeles de la cara se reemplazan con bloques de color, por lo que no se puede realizar las características. Los bloques de color se derivan normalmente de la imagen original con el promedio de los colores de los píxeles correspondientes a estos bloques. Pero no es necesario realizar este promedio por sí mismo. Sucede automáticamente cuando se copia un mapa de bits en una dimensión de píxel más pequeña. 

Cara del objeto monkey izquierdo ocupa aproximadamente una zona cuadrada de 72 píxeles con una esquina superior izquierda en el punto (112, 238). Vamos a reemplazar esa zona cuadrada 72 píxeles con una matriz de 9 x 9 de bloques de color, cada uno de los cuales es 8 por 8 píxeles cuadrados.

El **Pixelize imagen** página se carga en ese mapa de bits y crea primero un mapa de 9 píxeles cuadrado bits pequeño denominado `faceBitmap`. Se trata de un destino para copiar solo los cara del objeto monkey. El rectángulo de destino es simplemente 9-píxeles al cuadrado pero el rectángulo de origen es 72 píxeles al cuadrado. Cada bloque de 8 por 8 de píxeles de origen se consolida hasta un solo píxel calculando los colores.

El siguiente paso es copiar el mapa de bits original en un nuevo mapa de bits del mismo tamaño llamado `pixelizedBitmap`. El pequeño `faceBitmap` , a continuación, se copia en él con un rectángulo de destino de 72 píxeles cuadrados para que cada píxel de `faceBitmap` se expande a su tamaño 8 veces:

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

El constructor concluye mediante la creación de un `SKCanvasView` para mostrar el resultado:

[![Imagen de pixelize](drawing-images/PixelizeImage.png "Pixelize imagen")](drawing-images/PixelizeImage-Large.png#lightbox)

<a name="rotating-bitmaps" />

## <a name="rotating-bitmaps"></a>Rotación de mapas de bits

Otra tarea común es la rotación los mapas de bits. Esto es especialmente útil cuando se recuperan los mapas de bits de una biblioteca de fotos iPhone o iPad. A menos que el dispositivo se mantuvo en una orientación determinada cuando se tomó la fotografía, la imagen es probable que sea la boca abajo o hacia un lado.

Si activa un mapa de bits al revés es necesario crear otro mapa de bits del mismo tamaño que la primera y, a continuación, configurar una transformación para girar 180 grados al copiar la primera a la segunda. En todos los ejemplos en esta sección, `bitmap` es la `SKBitmap` objeto que debe girar:

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(180, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

Al girar 90 grados, deberá crear un mapa de bits que es un tamaño distinto al original, intercambiando el alto y ancho. Por ejemplo, si el mapa de bits original es 1200 píxeles de ancho y alto de 800 píxeles, el mapa de bits girado es 800 píxeles de ancho y 1200 píxeles de ancho. Establezca la traslación y rotación para que el mapa de bits se gira en torno a su esquina superior izquierda y, a continuación, se desplaza en la vista. (Tenga en cuenta que el `Translate` y `RotateDegrees` métodos se llaman en orden inverso de la forma en que se apliquen.) Este es el código para girar 90 grados a la derecha:

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

Y aquí es una función similar para girar 90 grados hacia la izquierda:

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

Estos dos métodos se usan en el **foto rompecabezas** páginas que se describe en el artículo [ **recortar los mapas de bits de SkiaSharp**](cropping.md#tile-division).

Un programa que permite al usuario que se va a girar un mapa de bits en incrementos de 90 grados sólo necesita implementar una función de rotación de 90 grados. El usuario puede girar, a continuación, en cualquier incremento de 90 grados mediante la ejecución repetida de esta uno función.

Un programa también puede girar un mapa de bits en cualquier cantidad. Un enfoque sencillo es modificar la función que gira 180 grados reemplazando 180 por generalizada `angle` variable:

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(angle, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

Sin embargo, por lo general, esta lógica se recorta las esquinas del mapa de bits girado. Un mejor enfoque consiste en calcular el tamaño del mapa de bits girado usar funciones trigonométricas para incluir esas esquinas. 

Se muestra este trigonometría en la **Rotator de mapa de bits** página. El archivo XAML crea una instancia de un `SKCanvasView` y un `Slider` que puede estar comprendido entre 0 y 360 grados con un `Label` que muestra el valor actual:

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

El archivo de código subyacente carga un recurso de mapa de bits y lo guarda como un campo estático de solo lectura denominado `originalBitmap`. El mapa de bits que se muestra en el `PaintSurface` controlador es `rotatedBitmap`, que se establece inicialmente en `originalBitmap`:

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

El `ValueChanged` controlador de la `Slider` realiza las operaciones que se creación un nuevo `rotatedBitmap` según el ángulo de giro. El nuevo ancho y alto se basan en absolute values de senos y cosenos de los altos y anchos originales. Las transformaciones que se usa para dibujar el mapa de bits original en el mapa de bits girado mover el centro del mapa de bits original al origen, a continuación, rotación, el número especificado de grados y, a continuación, convertir ese centro en el centro del mapa de bits girado. (El `Translate` y `RotateDegrees` métodos se llaman en orden inverso de cómo se aplican.)

Tenga en cuenta el uso de la `Clear` método para hacer que el fondo de `rotatedBitmap` un rosa claro. Esto es únicamente para ilustrar el tamaño de `rotatedBitmap` en la pantalla:

[![Mapa de bits Rotator](drawing-images/BitmapRotator.png "Rotator de mapa de bits")](drawing-images/BitmapRotator-Large.png#lightbox)

El mapa de bits girado es lo suficientemente grande como para incluir todo el mapa de bits original, pero no más.

## <a name="flipping-bitmaps"></a>Voltear los mapas de bits

Se llama a otra operación que se realizan normalmente en los mapas de bits _voltear_. Conceptualmente, el mapa de bits se gira en tres dimensiones en torno a un eje vertical o eje horizontal a través del centro del mapa de bits. Volteo vertical, crea una imagen reflejada.

El **impulsor de mapa de bits** página en el **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** aplicación muestra estos procesos. El archivo XAML contiene una `SKCanvasView` y dos botones para voltear verticalmente y horizontalmente:

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

El archivo de código subyacente implementa estas dos operaciones en el `Clicked` controladores para los botones: 

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

El volteo vertical se logra mediante una transformación de escala con un factor de escala horizontal de &ndash;1. El centro de la escala es el centro vertical del mapa de bits. El volteo horizontal es una transformación de escala con un factor de escala vertical de &ndash;1. 

Como puede ver en el rótulo invertido en camisa de monkey, voltear no es igual que la rotación. Pero como se muestra en la captura de pantalla UWP de la derecha, ambos volteo horizontal y verticalmente es el mismo que girar 180 grados:

[![Mapa de bits impulsor](drawing-images/BitmapFlipper.png "impulsor de mapa de bits")](drawing-images/BitmapFlipper-Large.png#lightbox)

Es otra tarea común que se puede controlar utilizando técnicas similares al recortar un mapa de bits a un subconjunto rectangular. Esto se describe en el siguiente artículo [ **recortar los mapas de bits de SkiaSharp**](cropping.md).

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
