---
title: Filtros de máscara de SkiaSharp
description: Obtenga información sobre cómo usar el filtro de máscara para crear desenfoques y otros efectos alfa.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 940422A1-8BC0-4039-8AD7-26C61320F858
author: davidbritch
ms.author: dabritch
ms.date: 08/27/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 827b5618dce019e2dedb773f270fe1090da5d616
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562514"
---
# <a name="skiasharp-mask-filters"></a>Filtros de máscara de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Los filtros de máscara son efectos que manipulan la geometría y el canal alfa de objetos gráficos. Para usar un filtro de máscara, establezca la [`MaskFilter`](xref:SkiaSharp.SKPaint.MaskFilter) propiedad de `SKPaint` en un objeto de tipo [`SKMaskFilter`](xref:SkiaSharp.SKMaskFilter) que haya creado mediante una llamada a uno de los `SKMaskFilter` métodos estáticos.

La mejor manera de familiarizarse con los filtros de máscara es experimentar con estos métodos estáticos. El filtro de máscara más útil crea un desenfoque:

![Ejemplo de desenfoque](mask-filters-images/MaskFilterExample.png "Ejemplo de desenfoque")

Esta es la única característica de filtro de máscara que se describe en este artículo. En el siguiente artículo de [**filtros de imagen de SkiaSharp**](image-filters.md) también se describe un efecto de desenfoque que podría preferir a este. 

El [`SKMaskFilter.CreateBlur`](xref:SkiaSharp.SKMaskFilter.CreateBlur(SkiaSharp.SKBlurStyle,System.Single)) método estático tiene la siguiente sintaxis:

```csharp
public static SKMaskFilter CreateBlur (SKBlurStyle blurStyle, float sigma);
```

Las sobrecargas permiten especificar marcas para el algoritmo utilizado para crear el desenfoque y un rectángulo para evitar desenfoques en áreas que se tratarán con otros objetos gráficos.

[`SKBlurStyle`](xref:SkiaSharp.SKBlurStyle) es una enumeración con los siguientes miembros:

- `Normal`
- `Solid`
- `Outer`
- `Inner`

Los efectos de estos estilos se muestran en los ejemplos siguientes. El `sigma` parámetro especifica la extensión del desenfoque. En versiones anteriores de Skia, la extensión del desenfoque se indicaba con un valor de radio. Si se prefiere un valor de radio para su aplicación, existe un método estático [`SKMaskFilter.ConvertRadiusToSigma`](xref:SkiaSharp.SKMaskFilter.ConvertRadiusToSigma*) que puede convertir de uno a otro. El método multiplica el radio por 0,57735 y agrega 0,5.

La página **experimento de desenfoque de máscara** en el ejemplo [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) permite experimentar con los estilos de desenfoque y los valores de Sigma. El archivo XAML crea una instancia de `Picker` con los cuatro `SKBlurStyle` miembros de enumeración y un `Slider` para especificar el valor de Sigma:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.MaskBlurExperimentPage"
             Title="Mask Blur Experiment">
    
    <StackLayout>
        <skiaforms:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blurStylePicker" 
                Title="Filter Blur Style" 
                Margin="10, 0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlurStyle}">
                    <x:Static Member="skia:SKBlurStyle.Normal" />
                    <x:Static Member="skia:SKBlurStyle.Solid" />
                    <x:Static Member="skia:SKBlurStyle.Outer" />
                    <x:Static Member="skia:SKBlurStyle.Inner" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="sigmaSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaSlider},
                              Path=Value,
                              StringFormat='Sigma = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

El archivo de código subyacente utiliza esos valores para crear un `SKMaskFilter` objeto y establecerlo en la `MaskFilter` propiedad de un `SKPaint` objeto. Este `SKPaint` objeto se usa para dibujar una cadena de texto y un mapa de bits:

```csharp
public partial class MaskBlurExperimentPage : ContentPage
{
    const string TEXT = "Blur My Text";

    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                            typeof(MaskBlurExperimentPage), 
                            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public MaskBlurExperimentPage ()
    {
        InitializeComponent ();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
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

        // Get values from XAML controls
        SKBlurStyle blurStyle =
            (SKBlurStyle)(blurStylePicker.SelectedIndex == -1 ?
                                        0 : blurStylePicker.SelectedItem);

        float sigma = (float)sigmaSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = (info.Width - 100) / (TEXT.Length / 2);
            paint.MaskFilter = SKMaskFilter.CreateBlur(blurStyle, sigma);

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

Este es el programa que se ejecuta en iOS, Android y el Plataforma universal de Windows (UWP) con el `Normal` estilo de desenfoque y los niveles crecientes `sigma` :

[![Experimento de desenfoque de máscara-normal](mask-filters-images/MaskBlurExperiment-Normal.png "Experimento de desenfoque de máscara-normal")](mask-filters-images/MaskBlurExperiment-Normal-Large.png#lightbox)

Observará que solo los bordes del mapa de bits se ven afectados por el desenfoque. La `SKMaskFilter` clase no es el efecto correcto que se debe usar si se desea desenfocar una imagen de mapa de bits completa. Para ello, querrá usar la [`SKImageFilter`](xref:SkiaSharp.SKImageFilter) clase tal y como se describe en el siguiente artículo sobre los [**filtros de imagen de SkiaSharp**](image-filters.md).

El texto se desenfoca más con los valores de aumento del `sigma` argumento. Al experimentar con este programa, observará que, para un valor determinado `sigma` , el desenfoque es más extremo en el escritorio de Windows 10. Esta diferencia se debe a que la densidad de píxeles es más baja en un monitor de escritorio que en los dispositivos móviles, por lo que el alto del texto en píxeles es inferior. El `sigma` valor es proporcional a una extensión de desenfoque en píxeles, de modo que, para un `sigma` valor determinado, el efecto es más extremo en pantallas de menor resolución. En una aplicación de producción, probablemente querrá calcular un `sigma` valor que es proporcional al tamaño del gráfico. 

Pruebe varios valores antes de la liquidación en un nivel de desenfoque que tenga el mejor aspecto para su aplicación. Por ejemplo, en la página **experimento de desenfoque de máscara** , pruebe a establecer `sigma` este aspecto:

```csharp
sigma = paint.TextSize / 18;
paint.MaskFilter = SKMaskFilter.CreateBlur(blurStyle, sigma);
```

Ahora el `Slider` no tiene ningún efecto, pero el grado de desenfoque es coherente entre las plataformas:

[![Desenfoque de máscara: coherente](mask-filters-images/MaskBlurExperiment-Consistent.png "Desenfoque de máscara: coherente")](mask-filters-images/MaskBlurExperiment-Consistent-Large.png#lightbox)

Todas las capturas de pantallas hasta ahora han mostrado el desenfoque creado con el `SKBlurStyle.Normal` miembro de enumeración. Las capturas de pantallas siguientes muestran los efectos de los `Solid` `Outer` estilos de `Inner` desenfoque, y:

[![Experimento de desenfoque de máscara](mask-filters-images/MaskBlurExperiment.png "Experimento de desenfoque de máscara")](mask-filters-images/MaskBlurExperiment-Large.png#lightbox)

La captura de pantalla de iOS muestra el `Solid` estilo: los caracteres de texto siguen presentes como trazos negros sólidos y el desenfoque se agrega a la parte externa de estos caracteres de texto. 

La captura de pantalla de Android en el centro muestra el `Outer` estilo: los trazos de caracteres se eliminan (como es el mapa de bits) y el desenfoque rodea el espacio vacío en el que aparecían los caracteres de texto. 

La captura de pantalla de UWP de la derecha muestra el `Inner` estilo. El desenfoque está restringido al área ocupada normalmente por los caracteres de texto.

En el artículo sobre el [**degradado lineal SkiaSharp**](shaders/linear-gradient.md#transparency-and-gradients) se describe un programa de **gradiente de reflexión** que usaba un degradado lineal y una transformación para imitar el reflejo de una cadena de texto:

[![Degradado de reflexión](shaders/linear-gradient-images/ReflectionGradient.png "Degradado de reflexión")](shaders/linear-gradient-images/ReflectionGradient-Large.png#lightbox)

La página de **reflexión borrosa** agrega una única instrucción a ese código:

```csharp
public class BlurryReflectionPage : ContentPage
{
    const string TEXT = "Reflection";

    public BlurryReflectionPage()
    {
        Title = "Blurry Reflection";

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
            // Set text color to blue
            paint.Color = SKColors.Blue;

            // Set text size to fill 90% of width
            paint.TextSize = 100;
            float width = paint.MeasureText(TEXT);
            float scale = 0.9f * info.Width / width;
            paint.TextSize *= scale;

            // Get text bounds
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Calculate offsets to position text above center
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2;

            // Draw unreflected text
            canvas.DrawText(TEXT, xText, yText, paint);

            // Shift textBounds to match displayed text
            textBounds.Offset(xText, yText);

            // Use those offsets to create a gradient for the reflected text
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, textBounds.Top),
                                new SKPoint(0, textBounds.Bottom),
                                new SKColor[] { paint.Color.WithAlpha(0),
                                                paint.Color.WithAlpha(0x80) },
                                null,
                                SKShaderTileMode.Clamp);

            // Create a blur mask filter
            paint.MaskFilter = SKMaskFilter.CreateBlur(SKBlurStyle.Normal, paint.TextSize / 36);

            // Scale the canvas to flip upside-down around the vertical center
            canvas.Scale(1, -1, 0, yText);

            // Draw reflected text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

La nueva instrucción agrega un filtro de desenfoque para el texto reflejado que se basa en el tamaño del texto:

```csharp
paint.MaskFilter = SKMaskFilter.CreateBlur(SKBlurStyle.Normal, paint.TextSize / 36);
```

Este filtro de desenfoque hace que la reflexión parezca mucho más realista:

[![Reflexión borrosa](mask-filters-images/BlurryReflection.png "Reflexión borrosa")](mask-filters-images/BlurryReflection-Large.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)