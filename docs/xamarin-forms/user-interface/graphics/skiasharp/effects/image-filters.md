---
title: Filtros de imagen de SkiaSharp
description: Obtenga información sobre cómo usar el filtro de imágenes para crear desenfoques y sombras paralelas.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 173E7B22-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/27/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 16e41b7a15483bddb3232de4bfeba0fd19b3c01f
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93369875"
---
# <a name="skiasharp-image-filters"></a>Filtros de imagen de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Los filtros de imagen son efectos que funcionan en todos los bits de color de los píxeles que componen una imagen. Son más versátiles que los filtros de máscara, que solo operan en el canal alfa, tal como se describe en el artículo [**filtros de máscara SkiaSharp**](mask-filters.md). Para usar un filtro de imagen, establezca la [`ImageFilter`](xref:SkiaSharp.SKPaint.ImageFilter) propiedad de `SKPaint` en un objeto de tipo [`SKImageFilter`](xref:SkiaSharp.SKImageFilter) que haya creado mediante una llamada a uno de los métodos estáticos de la clase.

La mejor manera de familiarizarse con los filtros de máscara es experimentar con estos métodos estáticos. Puede usar un filtro de máscara para desenfocar todo un mapa de bits:

![Ejemplo de desenfoque](image-filters-images/ImageFilterExample.png "Ejemplo de desenfoque")

En este artículo también se muestra cómo usar un filtro de imagen para crear una sombra paralela y para efectos de relieve y de grabado.

## <a name="blurring-vector-graphics-and-bitmaps"></a>Gráficos vectoriales y mapas de bits de desenfoque

El efecto de desenfoque creado por el [`SKImageFilter.CreateBlur`](xref:SkiaSharp.SKImageFilter.CreateBlur*) método estático tiene una gran ventaja sobre los métodos de desenfoque de la [`SKMaskFilter`](xref:SkiaSharp.SKMaskFilter) clase: el filtro de imagen puede desenfocar todo un mapa de bits. El método tiene la siguiente sintaxis:

```csharp
public static SkiaSharp.SKImageFilter CreateBlur (float sigmaX, float sigmaY,
                                                  SKImageFilter input = null,
                                                  SKImageFilter.CropRect cropRect = null);
```

El método tiene dos valores Sigma &mdash; el primero para la extensión de desenfoque en la dirección horizontal y el segundo para la dirección vertical. Puede aplicar filtros de imagen en cascada especificando otro filtro de imagen como el tercer argumento opcional. También se puede especificar un rectángulo de recorte.

La página de **experimento de desenfoque de imagen** en [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) incluye dos `Slider` vistas que permiten experimentar con la configuración de varios niveles de desenfoque:

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

El archivo de código subyacente usa los dos `Slider` valores para llamar al `SKImageFilter.CreateBlur` `SKPaint` objeto que se usa para mostrar el texto y un mapa de bits:

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

Las tres capturas de pantallas muestran varias configuraciones para la `sigmaX` configuración de y `sigmaY` :

[![Experimento de desenfoque de imagen](image-filters-images/ImageBlurExperiment.png "Experimento de desenfoque de imagen")](image-filters-images/ImageBlurExperiment-Large.png#lightbox)

Para que el desenfoque sea coherente entre los distintos tamaños y resoluciones de pantalla, establezca `sigmaX` y `sigmaY` en los valores que son proporcionales al tamaño de píxel representado de la imagen a la que se aplica el desenfoque.

## <a name="drop-shadow"></a>Sombra paralela

El [`SKImageFilter.CreateDropShadow`](xref:SkiaSharp.SKImageFilter.CreateDropShadow*) método estático crea un `SKImageFilter` objeto para una sombra paralela:

```csharp
public static SKImageFilter CreateDropShadow (float dx, float dy,
                                              float sigmaX, float sigmaY,
                                              SKColor color,
                                              SKDropShadowImageFilterShadowMode shadowMode,
                                              SKImageFilter input = null,
                                              SKImageFilter.CropRect cropRect = null);
```

Establezca este objeto en la `ImageFilter` propiedad de un `SKPaint` objeto y todo lo que dibuje con ese objeto tendrá una sombra paralela.

Los `dx` `dy` parámetros y indican los desplazamientos horizontal y vertical de la sombra en píxeles desde el objeto gráfico. La Convención en los gráficos 2D es suponer que una fuente luminosa proviene de la parte superior izquierda, lo que implica que ambos argumentos deberían ser positivos para colocar la sombra debajo y a la derecha del objeto gráfico.

Los `sigmaX` `sigmaY` parámetros y son factores de desenfoque para la sombra paralela.

El `color` parámetro es el color de la sombra paralela. Este `SKColor` valor puede incluir transparencia. Una posibilidad es el valor `SKColors.Black.WithAlpha(0x80)` de color para oscurecer cualquier fondo de color.

Los dos últimos parámetros son opcionales.

El programa de **experimento de sombra paralela** le permite experimentar con valores de `dx` , `dy` , `sigmaX` y `sigmaY` para mostrar una cadena de texto con una sombra paralela. El archivo XAML crea una instancia `Slider` de cuatro vistas para establecer estos valores:

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

El archivo de código subyacente usa esos valores para crear una sombra paralela roja en una cadena de texto azul:

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

Esta es la ejecución del programa:

[![Experimento de sombra paralela](image-filters-images/DropShadowExperiment.png "Experimento de sombra paralela")](image-filters-images/DropShadowExperiment-Large.png#lightbox)

Los valores de desplazamiento negativos en la captura de pantalla del Plataforma universal de Windows en el extremo derecho hacen que la sombra aparezca encima y a la izquierda del texto. Esto sugiere una fuente de luz en la parte inferior derecha, que no es la Convención para los gráficos del equipo. Pero no parece un problema, quizás porque la sombra también se hace muy borrosa y parece más ornamental que la mayoría de las sombras paralelas.

## <a name="lighting-effects"></a>Efectos de iluminación

La `SKImageFilter` clase define seis métodos que tienen nombres y parámetros similares, que se enumeran aquí en orden de complejidad creciente:

- [`CreateDistantLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateDistantLitDiffuse*)
- [`CreateDistantLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateDistantLitSpecular*)
- [`CreatePointLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreatePointLitDiffuse*)
- [`CreatePointLitSpecular`](xref:SkiaSharp.SKImageFilter.CreatePointLitSpecular*)
- [`CreateSpotLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateSpotLitDiffuse*)
- [`CreateSpotLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateSpotLitSpecular*)

Estos métodos crean filtros de imagen que imitan el efecto de distintos tipos de luz en superficies tridimensionales. El filtro de imágenes resultante ilumina los objetos bidimensionales como si existieran en el espacio 3D, lo que puede hacer que estos objetos parezcan elevados o empotrados, o con un resaltado especular.

Los `Distant` métodos claros suponen que la luz proviene de una distancia lejana. Con el fin de iluminar objetos, se supone que la luz apunta en una dirección coherente en el espacio 3D, de forma muy parecida al sol en un área pequeña de la tierra. Los `Point` métodos de luz imitan una bombilla colocada en el espacio 3D que emite luz en todas las direcciones. La `Spot` luz tiene una posición y una dirección, de forma muy parecida a una linterna.

Las ubicaciones y direcciones en el espacio 3D se especifican con valores de la [`SKPoint3`](xref:SkiaSharp.SKPoint3) estructura, que es similar a `SKPoint` pero con tres propiedades denominadas `X` , `Y` y `Z` .

El número y la complejidad de los parámetros de estos métodos dificultan la experimentación. Para empezar, la página del **experimento ligero distante** le permite experimentar con los parámetros del `CreateDistantLightDiffuse` método:

```csharp
public static SKImageFilter CreateDistantLitDiffuse (SKPoint3 direction,
                                                     SKColor lightColor,
                                                     float surfaceScale,
                                                     float kd,
                                                     SKImageFilter input = null,
                                                     SKImageFilter.CropRect cropRect = null);
```

La página no utiliza los dos últimos parámetros opcionales.

Tres `Slider` vistas en el archivo XAML permiten seleccionar la `Z` coordenada del `SKPoint3` valor, el `surfaceScale` parámetro y el `kd` parámetro, que se define en la documentación de la API como "constante de iluminación difusa":

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

El archivo de código subyacente obtiene esos tres valores y los usa para crear un filtro de imagen para mostrar una cadena de texto:

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

El primer argumento de `SKImageFilter.CreateDistantLitDiffuse` es la dirección de la luz. Las coordenadas X e y positivas indican que la luz apunta hacia la derecha y hacia abajo. Las coordenadas Z positivas apuntan a la pantalla. El archivo XAML permite seleccionar valores Z negativos, pero eso solo es para que pueda ver lo que sucede: conceptualmente, las coordenadas Z negativas hacen que la luz señale fuera de la pantalla. En el caso de los valores negativos pequeños, el efecto de iluminación deja de funcionar.

El `surfaceScale` argumento puede oscilar entre – 1 y 1. (Los valores superiores o inferiores no tienen ningún efecto más). Son valores relativos en el eje Z que indican el desplazamiento del objeto gráfico (en este caso, la cadena de texto) de la superficie del lienzo. Use valores negativos para aumentar la cadena de texto encima de la superficie del lienzo y valores positivos para detenerla en el lienzo.

El `lightConstant` valor debe ser positivo. (El programa permite valores negativos para que pueda ver que hacen que el efecto deje de funcionar). Los valores más altos causan una luz más intensa.

Estos factores se pueden equilibrar para obtener un efecto en relieve cuando `surfaceScale` es negativo (como con las capturas de pantalla de iOS y Android) y un efecto de grabado cuando `surfaceScale` es positivo, al igual que con la captura de pantalla de UWP de la derecha:

[![Experimento ligero distante](image-filters-images/DistantLightExperiment.png "Experimento ligero distante")](image-filters-images/DistantLightExperiment-Large.png#lightbox)

La captura de pantalla de Android tiene un valor Z de 0, lo que significa que la luz solo apunta hacia abajo y hacia la derecha. No se ilumina el fondo y no se ilumina la superficie de la cadena de texto. La luz solo afecta al borde del texto para un efecto muy sutil.

En el artículo [traducción](../transforms/translate.md)de transformaciones se ha mostrado un enfoque alternativo para el texto en relieve y grabado: la cadena de texto se muestra dos veces con distintos colores que se desplazan ligeramente entre sí.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)