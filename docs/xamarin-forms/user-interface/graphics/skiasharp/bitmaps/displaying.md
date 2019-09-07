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
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759509"
---
# <a name="displaying-skiasharp-bitmaps"></a>Mostrar mapas de bits de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

El asunto de mapas de bits de SkiaSharp se introdujo en el artículo  **[conceptos básicos de mapa de bits de SkiaSharp](../basics/bitmaps.md)** . Ese artículo mostró tres formas de mapas de bits de carga y tres formas de mostrar los mapas de bits. En este artículo Revisa las técnicas necesarias para cargar mapas de bits y profundiza en el uso de la `DrawBitmap` métodos de `SKCanvas`.

![Mostrar ejemplo](displaying-images/DisplayingSample.png "Mostrar ejemplo")

El `DrawBitmapLattice` y `DrawBitmapNinePatch` métodos se describen en el artículo  **[visualización de mapas de bits de SkiaSharp segmentada](segmented.md)** .

Son ejemplos de esta página desde la **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** aplicación. En la página principal de la aplicación, elija **SkiaSharp Bitmaps**y, a continuación, vaya a la **mostrar mapas de bits** sección.

## <a name="loading-a-bitmap"></a>Carga un mapa de bits

Un mapa de bits utilizado por una aplicación de SkiaSharp normalmente procede de uno de estos tres orígenes diferentes:

- Más de Internet
- Desde un recurso incrustado en el archivo ejecutable
- Desde la biblioteca de fotos del usuario

También es posible que una aplicación de SkiaSharp crear un nuevo mapa de bits y, a continuación, dibuje en él o establecer los bits de mapa de bits de forma algorítmica. Esas técnicas se explican en los artículos **[creación y dibujo de SkiaSharp Bitmaps](drawing.md)** y **[acceso a píxeles de mapa de bits de SkiaSharp](pixel-bits.md)** .

En los siguientes tres ejemplos de código de la carga de un mapa de bits, se asume la clase para contener un campo de tipo `SKBitmap`:

```csharp
SKBitmap bitmap;
```

Como el artículo **[conceptos básicos de mapa de bits de SkiaSharp](../basics/bitmaps.md)** indicadas, es la mejor manera de cargar un mapa de bits a través de Internet con el [ `HttpClient` ](xref:System.Net.Http.HttpClient) clase. Una única instancia de la clase se puede definir como un campo:

```csharp
HttpClient httpClient = new HttpClient();
```

Cuando se usa `HttpClient` con aplicaciones iOS y Android, desea establecer las propiedades del proyecto, como se describe en los documentos en  **[seguridad de capa de transporte (TLS) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md)** .

El código que usa `HttpClient` suele implicar la `await` operador, por lo que debe residir en un `async` método:

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

Tenga en cuenta que el `Stream` objeto obtenida `GetStreamAsync` se copia en un `MemoryStream`. Android no permite la `Stream` desde `HttpClient` para ser procesados por el subproceso principal, excepto en los métodos asincrónicos. 

[`SKBitmap.Decode`](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream)) Realiza mucho trabajo: El `Stream` objeto que se le pasa hace referencia a un bloque de memoria que contiene un mapa de bits completo en uno de los formatos de archivo de mapa de bits comunes, normalmente JPEG, PNG o GIF. El `Decode` método debe determinar el formato y, a continuación, descodificar el archivo de mapa de bits en formato de mapa de bits interno de SkiaSharp.

Después de su código llama a `SKBitmap.Decode`, probablemente invalidará la `CanvasView` para que el `PaintSurface` controlador puede mostrar el mapa de bits recién cargado.

La segunda manera de cargar un mapa de bits es mediante la inclusión del mapa de bits como un recurso incrustado en la biblioteca estándar de .NET hace referencia a los proyectos de plataforma individuales. Un recurso de identificador se pasa a la [ `GetManifestResourceStream` ](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) método. Este identificador de recurso está formado por el nombre del ensamblado, el nombre de la carpeta y el nombre de archivo del recurso separado por puntos:

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

Es un tercer enfoque para obtener un mapa de bits de biblioteca de imágenes del usuario. El código siguiente usa un servicio de dependencia que se incluye en el **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** aplicación. El **SkiaSharpFormsDemo** biblioteca estándar de .NET incluye el `IPhotoLibrary` interfaz, mientras que cada uno de los proyectos de plataforma contiene un `PhotoLibrary` clase que implementa esa interfaz.

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

Por lo general, este código también invalida la `CanvasView` para que el `PaintSurface` controlador puede mostrar el nuevo mapa de bits.

El `SKBitmap` clase define varias propiedades útiles, incluidas [ `Width` ](xref:SkiaSharp.SKBitmap.Width) y [ `Height` ](xref:SkiaSharp.SKBitmap.Height), que revelan las dimensiones en píxeles del mapa de bits, así como muchos métodos, incluidos métodos para crear mapas de bits, copiarlos y exponer los bits de píxeles. 

## <a name="displaying-in-pixel-dimensions"></a>Mostrar en las dimensiones de píxeles

El SkiaSharp [ `Canvas` ](xref:SkiaSharp.SKCanvas) clase define cuatro `DrawBitmap` métodos. Estos métodos permiten a los mapas de bits que se mostrará en dos formas muy diferentes: 

- Especificar un `SKPoint` valor (o independiente `x` y `y` valores) muestra el mapa de bits en sus dimensiones en píxeles. Los píxeles del mapa de bits se asignan directamente a los píxeles de la pantalla de vídeo.
- Si se especifica un rectángulo, el mapa de bits para ajustarse al tamaño y forma del rectángulo. 

Mostrar un mapa de bits en sus dimensiones en píxeles con [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKPoint,SkiaSharp.SKPaint)) con un `SKPoint` parámetro o [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) con independiente `x` y `y` parámetros:

```csharp
DrawBitmap(SKBitmap bitmap, SKPoint pt, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, float x, float y, SKPaint paint = null)
```

Estos dos métodos son funcionalmente idénticos. El punto especificado indica la ubicación de la esquina superior izquierda del mapa de bits en relación con el lienzo. Dado que la resolución de píxeles de los dispositivos móviles es tan alta, los mapas de bits más pequeños suelen aparecen muy pequeños en estos dispositivos.

El elemento opcional `SKPaint` parámetro le permite mostrar el mapa de bits con transparencia. Para ello, cree un `SKPaint` de objeto y establecer el `Color` propiedad a cualquier `SKColor` valor con un valor alfa de canal menor que 1. Por ejemplo:

```csharp
paint.Color = new SKColor(0, 0, 0, 0x80);
```

El 0 x 80 pasadas como el último argumento indica la transparencia del 50%. También puede establecer un canal alfa en uno de los colores predefinidos:

```csharp
paint.Color = SKColors.Red.WithAlpha(0x80);
```

Sin embargo, el color en sí es irrelevante. Se examina solo el canal alfa cuando se usa el `SKPaint` objeto en un `DrawBitmap` llamar.

La `SKPaint` objeto también desempeña un papel al mostrar mapas de bits utiliza modos de fusión o efectos de filtro. Estos se muestran en los artículos [los modos de composición y blend de SkiaSharp](../effects/blend-modes/index.md) y [filtros de imágenes de SkiaSharp](../effects/image-filters.md).

El **dimensiones en píxeles** página en el **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** programa de ejemplo muestra un recurso de mapa de bits que es de 320 píxeles de ancho x 240 píxeles de altos:

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

El `PaintSurface` controlador centra el mapa de bits mediante el cálculo `x` y `y` valores según las dimensiones de píxeles de la superficie de visualización y las dimensiones en píxeles del mapa de bits:

[![Dimensión de píxel](displaying-images/PixelDimensions.png "dimensiones en píxeles")](displaying-images/PixelDimensions-Large.png#lightbox)

Si la aplicación desea mostrar el mapa de bits en la esquina superior izquierda, simplemente pasaría coordenadas de (0, 0). 

## <a name="a-method-for-loading-resource-bitmaps"></a>Un método para cargar los mapas de bits de recursos

Muchos de los ejemplos próximamente deberá cargar los recursos de mapa de bits. Estático `BitmapExtensions` clase en el **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** solución contiene un método para ayudar a:

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

Tenga en cuenta el `Type` parámetro. Esto puede ser el `Type` objeto asociado con cualquier tipo en el ensamblado que almacena el recurso de mapa de bits.

Esto `LoadBitmapResource` método se usará en todas las muestras siguientes que requieren recursos de mapa de bits.

## <a name="stretching-to-fill-a-rectangle"></a>Expandir para rellenar un rectángulo

El `SKCanvas` clase define también un [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) método que representa el mapa de bits a un rectángulo y otro [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) método que representa un subconjunto rectangular del mapa de bits a un rectángulo:

```
DrawBitmap(SKBitmap bitmap, SKRect dest, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, SKRect source, SKRect dest, SKPaint paint = null)
```

En ambos casos, el mapa de bits se expande para rellenar el rectángulo denominado `dest`. En el segundo método, el `source` rectángulo permite seleccionar un subconjunto del mapa de bits. El `dest` rectángulo está en relación con el dispositivo de salida; el `source` rectángulo es relativo el mapa de bits.

El **rectángulo relleno** página muestra el primero de estos dos métodos mostrando el mismo mapa de bits utilizado en el ejemplo anterior en un rectángulo del mismo tamaño como el lienzo: 

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

Tenga en cuenta el uso de la nueva `BitmapExtensions.LoadBitmapResource` método para establecer el `SKBitmap` campo. Se obtiene el rectángulo de destino de la [ `Rect` ](xref:SkiaSharp.SKImageInfo.Rect) propiedad de `SKImageInfo`, que describe el tamaño de la superficie de pantalla:

[![Rellenar el rectángulo](displaying-images/FillRectangle.png "rellenar rectángulo")](displaying-images/FillRectangle-Large.png#lightbox)

Esto suele ser _no_ lo que desea. La imagen queda distorsionada al que se ajusta de forma distinta en dirección horizontal y vertical. Cuando se muestra un mapa de bits en un valor distinto de su tamaño en píxeles, normalmente desea conservar la relación de aspecto original del mapa de bits.

## <a name="stretching-while-preserving-the-aspect-ratio"></a>Ajuste conservando la relación de aspecto

Ajuste de un mapa de bits al tiempo que conserva la relación de aspecto es un proceso también conocido como _escala uniforme_. Ese término sugiere un enfoque algorítmico. Se muestra una posible solución en el **escala uniforme** página:

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

El `PaintSurface` controlador calcula un `scale` factor que es la cantidad mínima de la relación entre el ancho de pantalla y el alto al alto y ancho del mapa de bits. El `x` y `y` , a continuación, se pueden calcular valores para centrar el mapa de bits escalado dentro del ancho y alto. El rectángulo de destino tiene una esquina superior izquierda de `x` y `y` y una esquina inferior derecha de esos valores además de la escala de ancho y alto del mapa de bits:

[![Ajuste de escala uniforme](displaying-images/UniformScaling.png "escala uniforme")](displaying-images/UniformScaling-Large.png#lightbox)

Girar el teléfono lateralmente para ver el mapa de bits que se han extendido a esa área:

[![Uniforme de ajuste de escala horizontal](displaying-images/UniformScaling-Landscape.png "uniforme escalado horizontal")](displaying-images/UniformScaling-Landscape-Large.png#lightbox)

La ventaja de usar esto `scale` factor queda patente cuando desea implementar un algoritmo ligeramente diferente. Suponga que desea conservar la relación de aspecto del mapa de bits, pero también llenar el rectángulo de destino. Es la única manera de esto es posible al recortar parte de la imagen, pero puede implementar ese algoritmo simplemente cambiando `Math.Min` a `Math.Max` en el código anterior. Este es el resultado: 

[![Alternativa de ajuste de escala uniforme](displaying-images/UniformScaling-Alternative.png "alternativa escala uniforme")](displaying-images/UniformScaling-Alternative-Large.png#lightbox)

Se conserva la relación de aspecto del mapa de bits, pero se recortan las áreas de la izquierda y derecha del mapa de bits.

## <a name="a-versatile-bitmap-display-function"></a>Una función de exhibición versátil de mapa de bits

Entornos de programación basado en XAML (por ejemplo, UWP y Xamarin.Forms) tienen una instalación para expandir o reducir el tamaño de los mapas de bits manteniendo sus proporciones. Aunque SkiaSharp no incluye esta característica, puede implementarla usted mismo. El `BitmapExtensions` clase incluida en el [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) aplicación muestra cómo. La clase define dos nuevos `DrawBitmap` métodos que realizan el cálculo de la relación de aspecto. Estos nuevos métodos son métodos de extensión de `SKCanvas`.

El nuevo `DrawBitmap` métodos incluyen un parámetro de tipo `BitmapStretch`, una enumeración definida en el **BitmapExtensions.cs** archivo:

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

El `None`, `Fill`, `Uniform`, y `UniformToFill` miembros son los mismos que los de UWP [ `Stretch` ](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.media.stretch.aspx) enumeración. Xamarin.Forms similar [ `Aspect` ](xref:Xamarin.Forms.Aspect) enumeración define los miembros `Fill`, `AspectFit`, y `AspectFill`.

El **escala uniforme** el mapa de bits dentro del rectángulo de los centros de página que se muestra anteriormente, pero puede ser conveniente otras opciones, como la posición del mapa de bits en el lado izquierdo o derecho del rectángulo, o la parte superior o inferior. Que es el propósito de la `BitmapAlignment` enumeración:

```csharp
public enum BitmapAlignment
{
    Start,
    Center,
    End
}
```

Configuración de alineación no tiene ningún efecto cuando se usa con `BitmapStretch.Fill`.

La primera `DrawBitmap` función de extensión contiene un rectángulo de destino, pero ningún rectángulo de origen. Los valores predeterminados se definen para que si desea que el mapa de bits centrado, sólo necesita especificar un `BitmapStretch` miembro:

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

El propósito principal de este método es calcular un factor de escala denominado `scale` que, a continuación, se aplica al alto y ancho del mapa de bits cuando se llama a la `CalculateDisplayRect` método. Este es el método que calcula un rectángulo para mostrar el mapa de bits en función de la alineación horizontal y vertical:

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

El `BitmapExtensions` clase contiene más `DrawBitmap` método con un rectángulo de origen para especificar un subconjunto del mapa de bits. Este método es similar a la primera, salvo que el factor de escala se calcula en función de la `source` rectángulo y, a continuación, se aplican a la `source` rectángulo en la llamada a `CalculateDisplayRect`:

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

El primero de estos dos nuevos `DrawBitmap` métodos se muestra en el **modos de escalado** página. El archivo XAML contiene tres `Picker` elementos que le permiten seleccionar los miembros de la `BitmapStretch` y `BitmapAlignment` enumeraciones:

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

El archivo de código subyacente simplemente invalida la `CanvasView` cuando alguna `Picker` elemento ha cambiado. El `PaintSurface` controlador tiene acceso a los tres `Picker` vistas para llamar a la `DrawBitmap` método de extensión:

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

[![Los modos de escalado](displaying-images/ScalingModes.png "los modos de escalado")](displaying-images/ScalingModes-Large.png#lightbox)

El **rectángulo subconjunto** página tiene prácticamente el mismo archivo XAML que **modos de escalado**, pero el archivo de código define un subconjunto rectangular del mapa de bits especificado por el `SOURCE` campo: 

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

[![Subconjunto de rectángulo](displaying-images/RectangleSubset.png "subconjunto de rectángulo")](displaying-images/RectangleSubset-Large.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
