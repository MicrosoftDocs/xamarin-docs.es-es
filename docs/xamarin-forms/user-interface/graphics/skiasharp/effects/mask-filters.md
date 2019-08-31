---
title: Filtros de máscara de SkiaSharp
description: Obtenga información sobre cómo usar el filtro de máscara para crear desenfoques y otros efectos alfabéticos.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 940422A1-8BC0-4039-8AD7-26C61320F858
author: davidbritch
ms.author: dabritch
ms.date: 08/27/2018
ms.openlocfilehash: 36a8b5c32261d4f508c82feea1e6127574db6a20
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198247"
---
# <a name="skiasharp-mask-filters"></a>Filtros de máscara de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Los filtros de máscara son los efectos que manipulan la geometría y el canal alfa de objetos gráficos. Para usar un filtro de máscara, establezca el [ `MaskFilter` ](xref:SkiaSharp.SKPaint.MaskFilter) propiedad de `SKPaint` a un objeto de tipo [ `SKMaskFilter` ](xref:SkiaSharp.SKMaskFilter) que ha creado llamando a uno de los `SKMaskFilter` métodos estáticos.

La mejor manera de familiarizarse con los filtros de la máscara está experimentando con estos métodos estáticos. El filtro de máscara más útil, crea un desenfoque:

![Ejemplo de desenfoque](mask-filters-images/MaskFilterExample.png "desenfoque de ejemplo")

Que es la única característica de filtro de máscara que se describe en este artículo. El siguiente artículo en [ **filtros de imágenes de SkiaSharp** ](image-filters.md) también se describe un efecto de desenfoque es preferible a ésta. 

Estático [ `SKMaskFilter.CreateBlur` ](xref:SkiaSharp.SKMaskFilter.CreateBlur(SkiaSharp.SKBlurStyle,System.Single)) método tiene la siguiente sintaxis:

```csharp
public static SKMaskFilter CreateBlur (SKBlurStyle blurStyle, float sigma);
```

Las sobrecargas permiten especificar marcas para el algoritmo utilizado para crear el efecto de desenfoque y un rectángulo para evitar el desenfoque en áreas que se tratarán con otros objetos gráficos.

[`SKBlurStyle`](xref:SkiaSharp.SKBlurStyle) es una enumeración con los miembros siguientes:

- `Normal`
- `Solid`
- `Outer`
- `Inner`

En los ejemplos siguientes se muestran los efectos de estos estilos. El `sigma` parámetro especifica la extensión de desenfoque. En versiones anteriores de Skia, la extensión del desenfoque se ha indicado con un valor de radio. Si un valor de radio es preferible para la aplicación, hay una estática [ `SKMaskFilter.ConvertRadiusToSigma` ](xref:SkiaSharp.SKMaskFilter.ConvertRadiusToSigma*) método que se puede convertir de uno a otro. El método multiplica el radio por 0.57735 y agrega 0,5.

El **desenfoque experimentar máscara** página en el [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) ejemplo le permite experimentar con los valores de sigma y estilos de desenfoque. El archivo XAML crea una instancia de un `Picker` con los cuatro `SKBlurStyle` miembros de enumeración y un `Slider` para especificar el valor de sigma:

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

El archivo de código subyacente usa esos valores para crear un `SKMaskFilter` objeto y establézcalo como el `MaskFilter` propiedad de un `SKPaint` objeto. Esto `SKPaint` objeto se usa para dibujar una cadena de texto y un mapa de bits:

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

Este es el programa que se ejecutan en iOS, Android y la plataforma Universal de Windows (UWP) con el `Normal` estilo y el aumento de desenfoque `sigma` niveles:

[![Enmascarar desenfoque experimento - Normal](mask-filters-images/MaskBlurExperiment-Normal.png "máscara desenfoque experimento - Normal")](mask-filters-images/MaskBlurExperiment-Normal-Large.png#lightbox)

Observará que solo los bordes del mapa de bits se ven afectados por el desenfoque. La `SKMaskFilter` clase no es el efecto correcto para usar si desea una imagen de mapa de bits completo de desenfoque. Para el que desea usar el [ `SKImageFilter` ](xref:SkiaSharp.SKImageFilter) tal como se describe en el siguiente artículo en la clase [ **filtros de imágenes de SkiaSharp**](image-filters.md).

El texto se difuminan mucho más con el aumento de los valores de la `sigma` argumento. Experimentar con este programa, observará que para un determinado `sigma` valor, el desenfoque es más extremo en el escritorio de Windows 10. Esta diferencia se produce porque la densidad de píxeles es inferior a un monitor de escritorio que en dispositivos móviles y, por lo tanto, el alto en píxeles del texto es menor. El `sigma` valor es proporcional a una extensión de desenfoque, en píxeles, por lo que para un determinado `sigma` valor, el efecto es más extremo en pantallas de resolución inferior. En una aplicación de producción, probablemente quiera calcular un `sigma` valor que es proporcional al tamaño del gráfico. 

Probar varios valores antes de decidirse por un nivel de desenfoque que busca la mejor para su aplicación. Por ejemplo, en el **máscara desenfoque experimento** página, pruebe a establecer `sigma` similar al siguiente:

```csharp
sigma = paint.TextSize / 18;
paint.MaskFilter = SKMaskFilter.CreateBlur(blurStyle, sigma);
```

Ahora el `Slider` no tiene ningún efecto, pero el grado de desenfoque es coherente entre las plataformas:

[![Enmascarar desenfoque experimento - coherente](mask-filters-images/MaskBlurExperiment-Consistent.png "enmascarar desenfoque experimento - coherente")](mask-filters-images/MaskBlurExperiment-Consistent-Large.png#lightbox)

Todas las capturas de pantalla hasta ahora han demostrado desenfoque creado con el `SKBlurStyle.Normal` miembro de enumeración. Las capturas de pantalla siguientes muestran los efectos de la `Solid`, `Outer`, y `Inner` desenfoque estilos:

[![Enmascarar desenfoque experimento](mask-filters-images/MaskBlurExperiment.png "enmascarar el experimento de desenfoque")](mask-filters-images/MaskBlurExperiment-Large.png#lightbox)

La captura de pantalla de `Solid` iOS muestra el estilo: Los caracteres de texto siguen presentes como trazos negros sólidos y el desenfoque se agrega a la parte externa de estos caracteres de texto. 

La captura de pantalla de Android en el `Outer` centro muestra el estilo: Los trazos de caracteres se eliminan (como es el mapa de bits) y el desenfoque rodea el espacio vacío en el que aparecían los caracteres de texto. 

La captura de pantalla UWP de la derecha se muestra el `Inner` estilo. El efecto de desenfoque está restringido para el área ocupada normalmente por los caracteres de texto.

El [ **degradado lineal de SkiaSharp** ](shaders/linear-gradient.md#transparency-and-gradients) artículo se describe un **reflexión degradado** programa que usa un degradado lineal y una transformación para imitar un reflejo de una cadena de texto:

[![Degradado de reflexión](shaders/linear-gradient-images/ReflectionGradient.png "degradado de la reflexión")](shaders/linear-gradient-images/ReflectionGradient-Large.png#lightbox)

El **reflexión borrosa** página agrega una instrucción única a ese código:

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

La nueva instrucción agrega un filtro de desenfoque para el texto reflejado en la que se basa en el tamaño del texto:

```csharp
paint.MaskFilter = SKMaskFilter.CreateBlur(SKBlurStyle.Normal, paint.TextSize / 36);
```

Este filtro de desenfoque hace que la reflexión a resultar mucho más realistas:

[![Reflexión borrosa](mask-filters-images/BlurryReflection.png "borrosa reflexión")](mask-filters-images/BlurryReflection-Large.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
