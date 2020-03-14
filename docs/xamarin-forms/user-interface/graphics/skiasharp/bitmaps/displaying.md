---
title: Mostrar mapas de bits de SkiaSharp
description: Obtenga información sobre cómo mostrar SkiaSharp mapas de bits en píxeles, tamaños y expanden para rellenar los rectángulos mientras conserva la relación de aspecto.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8E074F8D-4715-4146-8CC0-FD7A8290EDE9
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 9955b68346c74435a3a141c69d02e1bec5856bd3
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305658"
---
# <a name="displaying-skiasharp-bitmaps"></a>Mostrar mapas de bits de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

El asunto de los mapas de bits SkiaSharp se presentó en el artículo **[conceptos básicos de mapas de bits en SkiaSharp](../basics/bitmaps.md)** . Ese artículo mostró tres formas de mapas de bits de carga y tres formas de mostrar los mapas de bits. En este artículo se revisan las técnicas para cargar mapas de bits y se profundiza en el uso de los métodos de `DrawBitmap` de `SKCanvas`.

![Mostrar muestra](displaying-images/DisplayingSample.png "Mostrar muestra")

Los métodos `DrawBitmapLattice` y `DrawBitmapNinePatch` se describen en el artículo **[presentación segmentada de mapas de bits de SkiaSharp](segmented.md)** .

Los ejemplos de esta página proceden de la aplicación **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** . En la Página principal de la aplicación, elija **SkiaSharp mapas de bits**y, a continuación, vaya a la sección **visualización de mapas de bits** .

## <a name="loading-a-bitmap"></a>Carga un mapa de bits

Un mapa de bits utilizado por una aplicación de SkiaSharp normalmente procede de uno de estos tres orígenes diferentes:

- Más de Internet
- Desde un recurso incrustado en el archivo ejecutable
- Desde la biblioteca de fotos del usuario

También es posible que una aplicación de SkiaSharp crear un nuevo mapa de bits y, a continuación, dibuje en él o establecer los bits de mapa de bits de forma algorítmica. Estas técnicas se describen en los artículos **[creación y dibujo de mapas de bits de SkiaSharp](drawing.md)** y **[acceso a los píxeles de mapas de bits de SkiaSharp](pixel-bits.md)** .

En los tres ejemplos de código siguientes de carga de un mapa de bits, se supone que la clase contiene un campo de tipo `SKBitmap`:

```csharp
SKBitmap bitmap;
```

Como se indica **[en el artículo conceptos básicos de mapas de bits en SkiaSharp](../basics/bitmaps.md)** , la mejor manera de cargar un mapa de bits a través de Internet es con la clase [`HttpClient`](xref:System.Net.Http.HttpClient) . Una única instancia de la clase se puede definir como un campo:

```csharp
HttpClient httpClient = new HttpClient();
```

Al usar `HttpClient` con aplicaciones iOS y Android, querrá establecer las propiedades del proyecto tal y como se describe en los documentos sobre la seguridad de la **[capa de transporte (TLS) 1,2](~/cross-platform/app-fundamentals/transport-layer-security.md)** .

El código que usa `HttpClient` a menudo implica el operador `await`, por lo que debe residir en un método `async`:

```csharp
try
{
    using (Stream stream = await httpClient.GetStreamAsync("https:// ··· "))
    using (MemoryStream memStream = new MemoryStream())
    {
        await stream.CopyToAsync(memStream);
        memStream.Seek(0, SeekOrigin.Begin);

        bitmap = SKBitmap.Decode(stream);
        ···
    };
}
catch
{
    ···
}
```

Observe que el objeto `Stream` Obtenido de `GetStreamAsync` se copia en un `MemoryStream`. Android no permite que el subproceso principal procese el `Stream` de `HttpClient`, excepto en los métodos asincrónicos. 

El [`SKBitmap.Decode`](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream)) realiza una gran cantidad de trabajo: el `Stream` objeto que se pasa hace referencia a un bloque de memoria que contiene un mapa de bits completo en uno de los formatos de archivo de mapa de bits comunes, normalmente JPEG, PNG o GIF. El método `Decode` debe determinar el formato y, a continuación, descodificar el archivo de mapa de bits en el propio formato de mapa de bits interno de SkiaSharp.

Una vez que el código llama a `SKBitmap.Decode`, probablemente invalidará el `CanvasView` para que el controlador de `PaintSurface` pueda mostrar el mapa de bits recién cargado.

La segunda manera de cargar un mapa de bits es mediante la inclusión del mapa de bits como un recurso incrustado en la biblioteca estándar de .NET hace referencia a los proyectos de plataforma individuales. Se pasa un identificador de recurso al método [`GetManifestResourceStream`](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) . Este identificador de recurso está formado por el nombre del ensamblado, el nombre de la carpeta y el nombre de archivo del recurso separado por puntos:

```csharp
string resourceID = "assemblyName.folderName.fileName";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    bitmap = SKBitmap.Decode(stream);
    ···
}
```

También se pueden almacenar archivos de mapa de bits como recursos en el proyecto individual de la plataforma para iOS, Android y la plataforma Universal de Windows (UWP). Sin embargo, al cargar los mapas de bits, requiere código que se encuentra en el proyecto de la plataforma.

Es un tercer enfoque para obtener un mapa de bits de biblioteca de imágenes del usuario. En el código siguiente se usa un servicio de dependencia que se incluye en la aplicación **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** . La biblioteca .NET Standard **SkiaSharpFormsDemo** incluye la interfaz `IPhotoLibrary`, mientras que cada uno de los proyectos de plataforma contiene una clase `PhotoLibrary` que implementa esa interfaz.

```csharp
IPhotoicturePicker picturePicker = DependencyService.Get<IPhotoLibrary>();

using (Stream stream = await picturePicker.GetImageStreamAsync())
{
    if (stream != null)
    {
        bitmap = SKBitmap.Decode(stream);
        ···
    }
}
```

Normalmente, este código también invalida el `CanvasView` para que el controlador de `PaintSurface` pueda mostrar el nuevo mapa de bits.

La clase `SKBitmap` define varias propiedades útiles, como [`Width`](xref:SkiaSharp.SKBitmap.Width) y [`Height`](xref:SkiaSharp.SKBitmap.Height), que revelan las dimensiones de píxeles del mapa de bits, así como muchos métodos, incluidos los métodos para crear mapas de bits, copiarlos y para exponer los bits de píxeles. 

## <a name="displaying-in-pixel-dimensions"></a>Mostrar en las dimensiones de píxeles

La clase SkiaSharp [`Canvas`](xref:SkiaSharp.SKCanvas) define cuatro métodos de `DrawBitmap`. Estos métodos permiten a los mapas de bits que se mostrará en dos formas muy diferentes: 

- Al especificar un valor de `SKPoint` (o valores de `x` y `y` independientes), se muestra el mapa de bits en sus dimensiones de píxeles. Los píxeles del mapa de bits se asignan directamente a los píxeles de la pantalla de vídeo.
- Si se especifica un rectángulo, el mapa de bits para ajustarse al tamaño y forma del rectángulo. 

Puede mostrar un mapa de bits en sus dimensiones de píxeles mediante [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKPoint,SkiaSharp.SKPaint)) con un `SKPoint` parámetro o [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) con parámetros de `x` y `y` independientes:

```csharp
DrawBitmap(SKBitmap bitmap, SKPoint pt, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, float x, float y, SKPaint paint = null)
```

Estos dos métodos son funcionalmente idénticos. El punto especificado indica la ubicación de la esquina superior izquierda del mapa de bits en relación con el lienzo. Dado que la resolución de píxeles de los dispositivos móviles es tan alta, los mapas de bits más pequeños suelen aparecen muy pequeños en estos dispositivos.

El parámetro `SKPaint` opcional permite mostrar el mapa de bits con transparencia. Para ello, cree un objeto de `SKPaint` y establezca la propiedad `Color` en cualquier valor de `SKColor` con un canal alfa inferior a 1. Por ejemplo:

```csharp
paint.Color = new SKColor(0, 0, 0, 0x80);
```

El 0 x 80 pasadas como el último argumento indica la transparencia del 50%. También puede establecer un canal alfa en uno de los colores predefinidos:

```csharp
paint.Color = SKColors.Red.WithAlpha(0x80);
```

Sin embargo, el color en sí es irrelevante. Solo se examina el canal alfa cuando se usa el objeto `SKPaint` en una llamada `DrawBitmap`.

El objeto `SKPaint` también desempeña un rol cuando se muestran mapas de bits mediante modos de mezcla o efectos de filtro. Se muestran en los artículos [modos de composición y mezcla de SkiaSharp](../effects/blend-modes/index.md) y [filtros de imagen de SkiaSharp](../effects/image-filters.md).

La página **dimensiones de píxeles** del programa de ejemplo **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** muestra un recurso de mapa de bits de 320 píxeles de ancho por 240 píxeles de alto:

```csharp
public class PixelDimensionsPage : ContentPage
{
    SKBitmap bitmap;

    public PixelDimensionsPage()
    {
        Title = "Pixel Dimensions";

        // Load the bitmap from a resource
        string resourceID = "SkiaSharpFormsDemos.Media.Banana.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }

        // Create the SKCanvasView and set the PaintSurface handler
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

        float x = (info.Width - bitmap.Width) / 2;
        float y = (info.Height - bitmap.Height) / 2;

        canvas.DrawBitmap(bitmap, x, y);
    }
}
```

El controlador de `PaintSurface` centra el mapa de bits calculando los valores de `x` y `y` basados en las dimensiones de píxeles de la superficie de presentación y las dimensiones de píxeles del mapa de bits:

[![Dimensiones de píxeles](displaying-images/PixelDimensions.png "Dimensiones de píxeles")](displaying-images/PixelDimensions-Large.png#lightbox)

Si la aplicación desea mostrar el mapa de bits en la esquina superior izquierda, simplemente pasaría coordenadas de (0, 0). 

## <a name="a-method-for-loading-resource-bitmaps"></a>Un método para cargar los mapas de bits de recursos

Muchos de los ejemplos próximamente deberá cargar los recursos de mapa de bits. La clase `BitmapExtensions` estática de la solución **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** contiene un método para ayudar:

```csharp
static class BitmapExtensions
{
    public static SKBitmap LoadBitmapResource(Type type, string resourceID)
    {
        Assembly assembly = type.GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            return SKBitmap.Decode(stream);
        }
    }
    ···
}
```

Observe el parámetro `Type`. Este puede ser el objeto de `Type` asociado a cualquier tipo en el ensamblado que almacena el recurso de mapa de bits.

Este método `LoadBitmapResource` se usará en todos los ejemplos posteriores que requieran recursos de mapa de bits.

## <a name="stretching-to-fill-a-rectangle"></a>Expandir para rellenar un rectángulo

La clase `SKCanvas` también define un método [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) que representa el mapa de bits en un rectángulo y otro método [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) que representa un subconjunto rectangular del mapa de bits en un rectángulo:

```
DrawBitmap(SKBitmap bitmap, SKRect dest, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, SKRect source, SKRect dest, SKPaint paint = null)
```

En ambos casos, el mapa de bits se ajusta para rellenar el rectángulo denominado `dest`. En el segundo método, el rectángulo `source` permite seleccionar un subconjunto del mapa de bits. El rectángulo `dest` es relativo al dispositivo de salida; el rectángulo `source` es relativo al mapa de bits.

La página **relleno del rectángulo** muestra el primero de estos dos métodos mostrando el mismo mapa de bits usado en el ejemplo anterior en un rectángulo del mismo tamaño que el lienzo: 

```csharp
public class FillRectanglePage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public FillRectanglePage ()
    {
        Title = "Fill Rectangle";

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

        canvas.DrawBitmap(bitmap, info.Rect);
    }
}
```

Observe el uso del método New `BitmapExtensions.LoadBitmapResource` para establecer el campo `SKBitmap`. El rectángulo de destino se obtiene de la propiedad [`Rect`](xref:SkiaSharp.SKImageInfo.Rect) de `SKImageInfo`, que describe el tamaño de la superficie de presentación:

[![Rellenar rectángulo](displaying-images/FillRectangle.png "Rellenar rectángulo")](displaying-images/FillRectangle-Large.png#lightbox)

Normalmente _no_ es lo que desea. La imagen queda distorsionada al que se ajusta de forma distinta en dirección horizontal y vertical. Cuando se muestra un mapa de bits en un valor distinto de su tamaño en píxeles, normalmente desea conservar la relación de aspecto original del mapa de bits.

## <a name="stretching-while-preserving-the-aspect-ratio"></a>Ajuste conservando la relación de aspecto

La expansión de un mapa de bits a la vez que se conserva la relación de aspecto es un proceso que también se conoce como _escalado uniforme_. Ese término sugiere un enfoque algorítmico. En la página **escala uniforme** se muestra una posible solución:

```csharp
public class UniformScalingPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(UniformScalingPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public UniformScalingPage()
    {
        Title = "Uniform Scaling";

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

        float scale = Math.Min((float)info.Width / bitmap.Width, 
                               (float)info.Height / bitmap.Height);
        float x = (info.Width - scale * bitmap.Width) / 2;
        float y = (info.Height - scale * bitmap.Height) / 2;
        SKRect destRect = new SKRect(x, y, x + scale * bitmap.Width, 
                                           y + scale * bitmap.Height);

        canvas.DrawBitmap(bitmap, destRect);
    }
}
```

El controlador de `PaintSurface` calcula un factor de `scale` que es el mínimo de la relación entre el ancho y el alto de la pantalla y el ancho y el alto del mapa de bits. Los valores `x` y `y` se pueden calcular para centrar el mapa de bits escalado dentro del ancho y el alto de la pantalla. El rectángulo de destino tiene una esquina superior izquierda de `x` y `y` y una esquina inferior derecha de los valores más el ancho y el alto escalados del mapa de bits:

[![Escalado uniforme](displaying-images/UniformScaling.png "Escalado uniforme")](displaying-images/UniformScaling-Large.png#lightbox)

Girar el teléfono lateralmente para ver el mapa de bits que se han extendido a esa área:

[![Horizontal de escalado uniforme](displaying-images/UniformScaling-Landscape.png "Horizontal de escalado uniforme")](displaying-images/UniformScaling-Landscape-Large.png#lightbox)

La ventaja de utilizar este factor `scale` es obvio cuando se desea implementar un algoritmo ligeramente diferente. Suponga que desea conservar la relación de aspecto del mapa de bits, pero también llenar el rectángulo de destino. La única forma de hacerlo es recortar parte de la imagen, pero puede implementar ese algoritmo simplemente cambiando `Math.Min` a `Math.Max` en el código anterior. Este es el resultado: 

[![Alternativa de escalado uniforme](displaying-images/UniformScaling-Alternative.png "Alternativa de escalado uniforme")](displaying-images/UniformScaling-Alternative-Large.png#lightbox)

Se conserva la relación de aspecto del mapa de bits, pero se recortan las áreas de la izquierda y derecha del mapa de bits.

## <a name="a-versatile-bitmap-display-function"></a>Una función de exhibición versátil de mapa de bits

Entornos de programación basado en XAML (por ejemplo, UWP y Xamarin.Forms) tienen una instalación para expandir o reducir el tamaño de los mapas de bits manteniendo sus proporciones. Aunque SkiaSharp no incluye esta característica, puede implementarla usted mismo. La clase `BitmapExtensions` incluida en la aplicación [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) muestra cómo. La clase define dos nuevos métodos de `DrawBitmap` que realizan el cálculo de la relación de aspecto. Estos nuevos métodos son métodos de extensión de `SKCanvas`.

Los nuevos métodos de `DrawBitmap` incluyen un parámetro de tipo `BitmapStretch`, una enumeración definida en el archivo **BitmapExtensions.CS** :

```csharp
public enum BitmapStretch
{
    None,
    Fill,
    Uniform,
    UniformToFill,
    AspectFit = Uniform,
    AspectFill = UniformToFill
}
```

Los miembros `None`, `Fill`, `Uniform`y `UniformToFill` son los mismos que los de la enumeración [`Stretch`](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.media.stretch.aspx) de UWP. La enumeración similar de Xamarin. Forms [`Aspect`](xref:Xamarin.Forms.Aspect) define miembros `Fill`, `AspectFit`y `AspectFill`.

La página **escala uniforme** mostrada arriba centra el mapa de bits dentro del rectángulo, pero podría querer otras opciones, como colocar el mapa de bits en el lado izquierdo o derecho del rectángulo, o en la parte superior o inferior. Este es el propósito de la enumeración `BitmapAlignment`:

```csharp
public enum BitmapAlignment
{
    Start,
    Center,
    End
}
```

La configuración de alineación no tiene ningún efecto cuando se usa con `BitmapStretch.Fill`.

La primera `DrawBitmap` función de extensión contiene un rectángulo de destino, pero ningún rectángulo de origen. Los valores predeterminados se definen de modo que, si desea que el mapa de bits esté centrado, solo necesita especificar un miembro de `BitmapStretch`:

```csharp
static class BitmapExtensions
{
    ···
    public static void DrawBitmap(this SKCanvas canvas, SKBitmap bitmap, SKRect dest, 
                                  BitmapStretch stretch, 
                                  BitmapAlignment horizontal = BitmapAlignment.Center, 
                                  BitmapAlignment vertical = BitmapAlignment.Center, 
                                  SKPaint paint = null)
    {
        if (stretch == BitmapStretch.Fill)
        {
            canvas.DrawBitmap(bitmap, dest, paint);
        }
        else
        {
            float scale = 1;

            switch (stretch)
            {
                case BitmapStretch.None:
                    break;

                case BitmapStretch.Uniform:
                    scale = Math.Min(dest.Width / bitmap.Width, dest.Height / bitmap.Height);
                    break;

                case BitmapStretch.UniformToFill:
                    scale = Math.Max(dest.Width / bitmap.Width, dest.Height / bitmap.Height);
                    break;
            }

            SKRect display = CalculateDisplayRect(dest, scale * bitmap.Width, scale * bitmap.Height, 
                                                  horizontal, vertical);

            canvas.DrawBitmap(bitmap, display, paint);
        }
    }
    ···
}
```

El propósito principal de este método es calcular un factor de escala denominado `scale` que se aplica después al ancho y al alto del mapa de bits al llamar al método `CalculateDisplayRect`. Este es el método que calcula un rectángulo para mostrar el mapa de bits en función de la alineación horizontal y vertical:

```csharp
static class BitmapExtensions
{
    ···
    static SKRect CalculateDisplayRect(SKRect dest, float bmpWidth, float bmpHeight, 
                                       BitmapAlignment horizontal, BitmapAlignment vertical)
    {
        float x = 0;
        float y = 0;

        switch (horizontal)
        {
            case BitmapAlignment.Center:
                x = (dest.Width - bmpWidth) / 2;
                break;

            case BitmapAlignment.Start:
                break;

            case BitmapAlignment.End:
                x = dest.Width - bmpWidth;
                break;
        }

        switch (vertical)
        {
            case BitmapAlignment.Center:
                y = (dest.Height - bmpHeight) / 2;
                break;

            case BitmapAlignment.Start:
                break;

            case BitmapAlignment.End:
                y = dest.Height - bmpHeight;
                break;
        }

        x += dest.Left;
        y += dest.Top;

        return new SKRect(x, y, x + bmpWidth, y + bmpHeight);
    }
}
```

La clase `BitmapExtensions` contiene un método de `DrawBitmap` adicional con un rectángulo de origen para especificar un subconjunto del mapa de bits. Este método es similar al primero, salvo que el factor de escala se calcula en función del rectángulo `source` y, a continuación, se aplica al rectángulo `source` en la llamada a `CalculateDisplayRect`:

```csharp
static class BitmapExtensions
{
    ···
    public static void DrawBitmap(this SKCanvas canvas, SKBitmap bitmap, SKRect source, SKRect dest,
                                  BitmapStretch stretch,
                                  BitmapAlignment horizontal = BitmapAlignment.Center,
                                  BitmapAlignment vertical = BitmapAlignment.Center,
                                  SKPaint paint = null)
    {
        if (stretch == BitmapStretch.Fill)
        {
            canvas.DrawBitmap(bitmap, source, dest, paint);
        }
        else
        {
            float scale = 1;

            switch (stretch)
            {
                case BitmapStretch.None:
                    break;

                case BitmapStretch.Uniform:
                    scale = Math.Min(dest.Width / source.Width, dest.Height / source.Height);
                    break;

                case BitmapStretch.UniformToFill:
                    scale = Math.Max(dest.Width / source.Width, dest.Height / source.Height);
                    break;
            }

            SKRect display = CalculateDisplayRect(dest, scale * source.Width, scale * source.Height, 
                                                  horizontal, vertical);

            canvas.DrawBitmap(bitmap, source, display, paint);
        }
    }
    ···
}
```

El primero de estos dos nuevos métodos de `DrawBitmap` se muestra en la página **modos de escalado** . El archivo XAML contiene tres elementos `Picker` que permiten seleccionar miembros de las enumeraciones `BitmapStretch` y `BitmapAlignment`:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SkiaSharpFormsDemos"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.ScalingModesPage"
             Title="Scaling Modes">

    <Grid Padding="10">
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <skia:SKCanvasView x:Name="canvasView" 
                           Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Label Text="Stretch:"
               Grid.Row="1" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="stretchPicker"
                Grid.Row="1" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapStretch}">
                    <x:Static Member="local:BitmapStretch.None" />
                    <x:Static Member="local:BitmapStretch.Fill" />
                    <x:Static Member="local:BitmapStretch.Uniform" />
                    <x:Static Member="local:BitmapStretch.UniformToFill" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Label Text="Horizontal Alignment:"
               Grid.Row="2" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="horizontalPicker" 
                Grid.Row="2" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapAlignment}">
                    <x:Static Member="local:BitmapAlignment.Start" />
                    <x:Static Member="local:BitmapAlignment.Center" />
                    <x:Static Member="local:BitmapAlignment.End" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Label Text="Vertical Alignment:"
               Grid.Row="3" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="verticalPicker" 
                Grid.Row="3" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapAlignment}">
                    <x:Static Member="local:BitmapAlignment.Start" />
                    <x:Static Member="local:BitmapAlignment.Center" />
                    <x:Static Member="local:BitmapAlignment.End" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</ContentPage>
```

El archivo de código subyacente simplemente invalida el `CanvasView` cuando algún elemento `Picker` ha cambiado. El controlador de `PaintSurface` tiene acceso a las tres vistas de `Picker` para llamar al método de extensión de `DrawBitmap`:

```csharp
public partial class ScalingModesPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(ScalingModesPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public ScalingModesPage()
    {
        InitializeComponent();
    }

    private void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect dest = new SKRect(0, 0, info.Width, info.Height);

        BitmapStretch stretch = (BitmapStretch)stretchPicker.SelectedItem;
        BitmapAlignment horizontal = (BitmapAlignment)horizontalPicker.SelectedItem;
        BitmapAlignment vertical = (BitmapAlignment)verticalPicker.SelectedItem;

        canvas.DrawBitmap(bitmap, dest, stretch, horizontal, vertical);
    }
}
```

Estas son algunas combinaciones de opciones:

[![Modos de escalado](displaying-images/ScalingModes.png "Modos de escalado")](displaying-images/ScalingModes-Large.png#lightbox)

La página de **subconjunto de rectángulos** tiene prácticamente el mismo archivo XAML que los **modos de escalado**, pero el archivo de código subyacente define un subconjunto rectangular del mapa de bits proporcionado por el campo `SOURCE`: 

```csharp
public partial class ScalingModesPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(ScalingModesPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");

    static readonly SKRect SOURCE = new SKRect(94, 12, 212, 118);

    public RectangleSubsetPage()
    {
        InitializeComponent();
    }

    private void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect dest = new SKRect(0, 0, info.Width, info.Height);

        BitmapStretch stretch = (BitmapStretch)stretchPicker.SelectedItem;
        BitmapAlignment horizontal = (BitmapAlignment)horizontalPicker.SelectedItem;
        BitmapAlignment vertical = (BitmapAlignment)verticalPicker.SelectedItem;

        canvas.DrawBitmap(bitmap, SOURCE, dest, stretch, horizontal, vertical);
    }
}
```

Este origen de rectángulo aísla la cabeza del objeto monkey, como se muestra en estas capturas de pantalla:

[![Subconjunto de rectángulos](displaying-images/RectangleSubset.png "Subconjunto de rectángulos")](displaying-images/RectangleSubset-Large.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
