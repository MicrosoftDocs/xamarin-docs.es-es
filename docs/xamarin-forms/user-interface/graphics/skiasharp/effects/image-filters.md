---
title: Filtros de imágenes de SkiaSharp
description: Obtenga información sobre cómo usar el filtro de imágenes para crear desenfoques y sombras paralelas.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 173E7B22-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/27/2018
ms.openlocfilehash: f93f0462d476daaaa551833391b1be1865795476
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770535"
---
# <a name="skiasharp-image-filters"></a>Filtros de imágenes de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Filtros de imágenes son los efectos que funcionan en todos los bits de color de los píxeles que componen una imagen. Son más versátiles que los filtros de máscara, que solo funcionan en el canal alfa como se describe en el artículo [ **filtros de máscara de SkiaSharp**](mask-filters.md). Para usar un filtro de imagen, establezca la [ `ImageFilter` ](xref:SkiaSharp.SKPaint.ImageFilter) propiedad de `SKPaint` a un objeto de tipo [ `SKImageFilter` ](xref:SkiaSharp.SKImageFilter) que ha creado llamando a uno de los métodos estáticos de la clase.

La mejor manera de familiarizarse con los filtros de la máscara está experimentando con estos métodos estáticos. Puede utilizar un filtro de máscara para difuminar un mapa de bits completo:

![Ejemplo de desenfoque](image-filters-images/ImageFilterExample.png "desenfoque de ejemplo")

En este artículo también muestra cómo utilizar un filtro de imágenes para crear una caída instantáneas y de relieve y grabados efectos.

## <a name="blurring-vector-graphics-and-bitmaps"></a>Gráficos vectoriales y mapas de bits de desenfoque

El efecto de desenfoque creado [`SKImageFilter.CreateBlur`](xref:SkiaSharp.SKImageFilter.CreateBlur*) por el método estático tiene una gran ventaja sobre los métodos de [`SKMaskFilter`](xref:SkiaSharp.SKMaskFilter) desenfoque de la clase: El filtro de imagen puede desenfocar un mapa de bits completo. El método tiene la siguiente sintaxis:

```csharp
public static SkiaSharp.SKImageFilter CreateBlur (float sigmaX, float sigmaY,
                                                  SKImageFilter input = null,
                                                  SKImageFilter.CropRect cropRect = null);
```

El método tiene dos valores de sigma &mdash; la primera para la extensión de desenfoque en dirección horizontal y el segundo para la dirección vertical. Puede actuar en cascada de filtros de imágenes mediante la especificación de otro filtro de la imagen como el tercer argumento opcional. También se puede especificar un rectángulo de recorte.

El **desenfoque experimentar imagen** página en el [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) incluye dos `Slider` vistas que le permiten experimentar con distintos niveles de desenfoque de configuración:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.ImageBlurExperimentPage"
             Title="Image Blur Experiment">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="sigmaXSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaXSlider},
                              Path=Value,
                              StringFormat='Sigma X = {0:F1}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="sigmaYSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaYSlider},
                              Path=Value,
                              StringFormat='Sigma Y = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

El archivo de código subyacente usa los dos `Slider` valores para llamar a `SKImageFilter.CreateBlur` para el `SKPaint` objeto utilizado para mostrar texto y un mapa de bits:

```csharp
public partial class ImageBlurExperimentPage : ContentPage
{
    const string TEXT = "Blur My Text";

    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                            typeof(MaskBlurExperimentPage),
                            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public ImageBlurExperimentPage ()
    {
        InitializeComponent ();
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

        canvas.Clear(SKColors.Pink);

        // Get values from sliders
        float sigmaX = (float)sigmaXSlider.Value;
        float sigmaY = (float)sigmaYSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = (info.Width - 100) / (TEXT.Length / 2);
            paint.ImageFilter = SKImageFilter.CreateBlur(sigmaX, sigmaY);

            // Get text bounds and calculate display rectangle
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);
            SKRect textRect = new SKRect(0, 0, info.Width, textBounds.Height + 50);

            // Center the text in the display rectangle
            float xText = textRect.Width / 2 - textBounds.MidX;
            float yText = textRect.Height / 2 - textBounds.MidY;

            canvas.DrawText(TEXT, xText, yText, paint);

            // Calculate rectangle for bitmap
            SKRect bitmapRect = new SKRect(0, textRect.Bottom, info.Width, info.Height);
            bitmapRect.Inflate(-50, -50);

            canvas.DrawBitmap(bitmap, bitmapRect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

Los tres capturas de pantalla muestran diversas configuraciones para el `sigmaX` y `sigmaY` configuración:

[![Experimento de desenfoque de la imagen](image-filters-images/ImageBlurExperiment.png "experimento de desenfoque de la imagen")](image-filters-images/ImageBlurExperiment-Large.png#lightbox)

Para mantener la coherencia entre los diferentes tamaños y resoluciones de desenfoque, establezca `sigmaX` y `sigmaY` a valores que son proporcionales al tamaño de la imagen que se aplica el efecto de desenfoque a píxeles representados.

## <a name="drop-shadow"></a>Sombra paralela

El [ `SKImageFilter.CreateDropShadow` ](xref:SkiaSharp.SKImageFilter.CreateDropShadow*) método estático crea una `SKImageFilter` objeto para un efecto de sombra:

```csharp
public static SKImageFilter CreateDropShadow (float dx, float dy,
                                              float sigmaX, float sigmaY,
                                              SKColor color,
                                              SKDropShadowImageFilterShadowMode shadowMode,
                                              SKImageFilter input = null,
                                              SKImageFilter.CropRect cropRect = null);
```

Establezca este objeto en el `ImageFilter` propiedad de un `SKPaint` objeto y cualquier cosa que se dibuja con el objeto tendrá una sombra detrás de él.

El `dx` y `dy` parámetros indican los desplazamientos horizontales y verticales de la sombra en píxeles desde el objeto gráfico. La convención en los gráficos 2D es suponer una fuente de luz procedentes de la parte superior izquierda, lo que implica que ambos argumentos deben ser positivos para colocar la sombra debajo y a la derecha del objeto gráfico.

El `sigmaX` y `sigmaY` parámetros están desapareciendo factores de la sombra paralela.

El `color` parámetro es el color de la sombra paralela. Esto `SKColor` valor puede incluir la transparencia. Una posibilidad es el valor de color `SKColors.Black.WithAlpha(0x80)` oscurecer cualquier color de fondo.

Los dos parámetros finales son opcionales.

El **Drop Shadow experimentar** programa permite experimentar con los valores de `dx`, `dy`, `sigmaX`, y `sigmaY` para mostrar una cadena de texto con sombra paralela. El archivo XAML crea una instancia de cuatro `Slider` vistas para establecer estos valores:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.DropShadowExperimentPage"
             Title="Drop Shadow Experiment">
    <ContentPage.Resources>
        <Style TargetType="Slider">
            <Setter Property="Margin" Value="10, 0" />
        </Style>

        <Style TargetType="Label">
            <Setter Property="HorizontalTextAlignment" Value="Center" />
        </Style>
    </ContentPage.Resources>

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="dxSlider"
                Minimum="-20"
                Maximum="20"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference dxSlider},
                              Path=Value,
                              StringFormat='Horizontal offset = {0:F1}'}" />

        <Slider x:Name="dySlider"
                Minimum="-20"
                Maximum="20"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference dySlider},
                              Path=Value,
                              StringFormat='Vertical offset = {0:F1}'}" />

        <Slider x:Name="sigmaXSlider"
                Maximum="10"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaXSlider},
                              Path=Value,
                              StringFormat='Sigma X = {0:F1}'}" />

        <Slider x:Name="sigmaYSlider"
                Maximum="10"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaYSlider},
                              Path=Value,
                              StringFormat='Sigma Y = {0:F1}'}" />
    </StackLayout>
</ContentPage>
```

El archivo de código subyacente usa esos valores para crear una sombra roja en una cadena de texto azul:

```csharp
public partial class DropShadowExperimentPage : ContentPage
{
    const string TEXT = "Drop Shadow";

    public DropShadowExperimentPage ()
    {
        InitializeComponent ();
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

        // Get values from sliders
        float dx = (float)dxSlider.Value;
        float dy = (float)dySlider.Value;
        float sigmaX = (float)sigmaXSlider.Value;
        float sigmaY = (float)sigmaYSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = info.Width / 7;
            paint.Color = SKColors.Blue;
            paint.ImageFilter = SKImageFilter.CreateDropShadow(
                                    dx,
                                    dy,
                                    sigmaX,
                                    sigmaY,
                                    SKColors.Red,
                                    SKDropShadowImageFilterShadowMode.DrawShadowAndForeground);

            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Center the text in the display rectangle
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

Este es el programa que se ejecuta:

[![Quitar instantáneas experimento](image-filters-images/DropShadowExperiment.png "Drop Shadow experimento")](image-filters-images/DropShadowExperiment-Large.png#lightbox)

Los valores de desplazamiento negativo en la captura de pantalla de la plataforma Universal de Windows en el extremo derecho hacen que la sombra aparezcan encima y a la izquierda del texto. Esto sugiere una fuente de luz en la esquina inferior derecha, que no es la convención de gráficos para PC. Pero no parece ser incorrecto en cualquier modo, quizás porque la sombra también queda muy borrosa y parece ornamental más que la mayoría de las sombras paralelas.

## <a name="lighting-effects"></a>Efectos de iluminación

La `SKImageFilter` clase define seis métodos que tienen nombres y parámetros, que se enumeran aquí en orden creciente complejidad similares:

- [`CreateDistantLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateDistantLitDiffuse*)
- [`CreateDistantLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateDistantLitSpecular*)
- [`CreatePointLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreatePointLitDiffuse*)
- [`CreatePointLitSpecular`](xref:SkiaSharp.SKImageFilter.CreatePointLitSpecular*)
- [`CreateSpotLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateSpotLitDiffuse*)
- [`CreateSpotLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateSpotLitSpecular*)

Estos métodos crean filtros de imágenes que imitan el efecto de diferentes tipos de luz en superficies tridimensionales. El filtro de la imagen resultante ilumina los objetos bidimensionales, como si estuvieran en el espacio 3D, lo que puede provocar que aparezcan con privilegios elevados o encajada estos objetos, o con el resaltado especular.

El `Distant` métodos claros suponen que la luz proviene de una distancia lejano. Con el fin de objetos reflectantes, se supone que la luz de punto en una dirección coherente en el espacio 3D, como el sol en un área pequeña de la tierra. El `Point` métodos claros imitan una bombilla situada en un espacio 3D que emite luz en todas las direcciones. El `Spot` luz tiene una posición y una dirección, como una linterna.

Ubicaciones y direcciones en el espacio 3D se especifican con valores de la [ `SKPoint3` ](xref:SkiaSharp.SKPoint3) estructura, que es similar a `SKPoint` pero con tres propiedades denominadas `X`, `Y`, y `Z`.

El número y la complejidad de los parámetros a estos métodos dificultan la experimentación con ellos. Para comenzar, el **experimentar de luz distante** página permite experimentar con los parámetros para el `CreateDistantLightDiffuse` método:

```csharp
public static SKImageFilter CreateDistantLitDiffuse (SKPoint3 direction,
                                                     SKColor lightColor,
                                                     float surfaceScale,
                                                     float kd,
                                                     SKImageFilter input = null,
                                                     SKImageFilter.CropRect cropRect = null);
```

La página no usa los dos últimos parámetros opcionales.

Tres `Slider` vistas en el XAML de archivos permiten seleccionar la `Z` coordenadas de la `SKPoint3` valor, el `surfaceScale` parámetro y el `kd` parámetro, que se define en la documentación de API como "constante de iluminación difusa":

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaLightExperiment.MainPage"
             Title="Distant Light Experiment">

    <StackLayout>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           VerticalOptions="FillAndExpand" />

        <Slider x:Name="zSlider"
                Minimum="-10"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference zSlider},
                              Path=Value,
                              StringFormat='Z = {0:F0}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="surfaceScaleSlider"
                Minimum="-1"
                Maximum="1"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference surfaceScaleSlider},
                              Path=Value,
                              StringFormat='Surface Scale = {0:F1}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="lightConstantSlider"
                Minimum="-1"
                Maximum="1"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference lightConstantSlider},
                              Path=Value,
                              StringFormat='Light Constant = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

El archivo de código subyacente obtiene esos tres valores y los utiliza para crear un filtro de imagen para mostrar una cadena de texto:

```csharp
public partial class DistantLightExperimentPage : ContentPage
{
    const string TEXT = "Lighting";

    public DistantLightExperimentPage()
    {
        InitializeComponent();
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

        float z = (float)zSlider.Value;
        float surfaceScale = (float)surfaceScaleSlider.Value;
        float lightConstant = (float)lightConstantSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.IsAntialias = true;

            // Size text to 90% of canvas width
            paint.TextSize = 100;
            float textWidth = paint.MeasureText(TEXT);
            paint.TextSize *= 0.9f * info.Width / textWidth;

            // Find coordinates to center text
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            float xText = info.Rect.MidX - textBounds.MidX;
            float yText = info.Rect.MidY - textBounds.MidY;

            // Create distant light image filter
            paint.ImageFilter = SKImageFilter.CreateDistantLitDiffuse(
                                    new SKPoint3(2, 3, z),
                                    SKColors.White,
                                    surfaceScale,
                                    lightConstant);

            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

El primer argumento de `SKImageFilter.CreateDistantLitDiffuse` es la dirección de la luz. Positivo X e Y coordenadas indican que la luz se apunta a la derecha y hacia abajo. Punto de las coordenadas Z positivo en la pantalla. El archivo XAML permite seleccionar valores Z negativos, pero eso es solo para que pueda ver lo que sucede: Conceptualmente, las coordenadas Z negativas hacen que la luz señale fuera de la pantalla. Para cualquier elemento otros valores negativos, a continuación, pequeños, el efecto de iluminación deja de funcionar.

El `surfaceScale` argumento puede oscilar entre -1 y 1. (Los valores superiores o inferiores no tengan ningún efecto adicional). Estos son los valores relativos en el eje Z que indican el desplazamiento del objeto gráfico (en este caso, la cadena de texto) de la superficie del lienzo. Use los valores negativos para generar la cadena de texto por encima de la superficie del lienzo y que los valores positivos se bajan al lienzo.

El `lightConstant` valor debe ser positivo. (El sistema permite los valores negativos para que pueda ver que hacen que el efecto dejará de funcionar). Los valores más altos hacer que la luz más intensa.

Se pueden equilibrar estos factores para obtener una en relieve efecto cuando `surfaceScale` es negativo (al igual que con iOS y Android capturas de pantalla) y un grabado efecto cuando `surfaceScale` es positivo, como con la captura de pantalla UWP a la derecha:

[![Experimento de luz distante](image-filters-images/DistantLightExperiment.png "experimento luz distante")](image-filters-images/DistantLightExperiment-Large.png#lightbox)

La captura de pantalla de Android tiene un valor de Z de 0, lo que significa que la luz sólo apunta hacia abajo y a la derecha. No está iluminado el fondo y la superficie de la cadena de texto no está iluminada cualquiera. El borde del texto para un efecto muy sutil solamente afecta a la luz.

En el artículo [traducción](../transforms/translate.md)de transformaciones se ha mostrado un enfoque alternativo a texto en relieve y grabado: La cadena de texto se muestra dos veces con distintos colores que se desplazan ligeramente entre sí.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
