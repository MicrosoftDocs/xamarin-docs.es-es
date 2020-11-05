---
title: Modos de mezcla no separables
description: Use los modos de mezcla no separables para modificar el matiz, la saturación o la luminosidad.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97FA2730-87C0-4914-8C9F-C64A02CF9EEF
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ea590c0390ab045e5cf8b526aee66c2408d1b784
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374672"
---
# <a name="the-non-separable-blend-modes"></a>Modos de mezcla no separables

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Como vimos en el artículo [**SkiaSharp modos de mezcla**](separable.md)separables, los modos de mezcla separables realizan operaciones en los canales rojo, verde y azul por separado. Los modos de mezcla no separables no. Al trabajar con los niveles de matiz, saturación y luminosidad del color, los modos de mezcla no separables pueden modificar los colores de maneras interesantes:

![Ejemplo no separable](non-separable-images/NonSeparableSample.png "Ejemplo no separable")

## <a name="the-hue-saturation-luminosity-model"></a>Modelo Hue-saturación-luminosidad

Para comprender los modos de mezcla no separables, es necesario tratar los píxeles de origen y de destino como colores en el modelo Hue-saturación-luminosidad. (La luminosidad también se denomina luminosidad).

El modelo de color HSL se analizó en el artículo [**integración con Xamarin.Forms**](../../basics/integration.md) y un programa de ejemplo en ese artículo permite experimentar con los colores HSL. Puede crear un `SKColor` valor mediante valores de matiz, saturación y luminosidad con el método estático [`SKColor.FromHsl`](xref:SkiaSharp.SKColor.FromHsl*) .

El matiz representa la longitud de onda dominante del color. Los valores de matiz oscilan entre 0 y 360 y se recorren las principales sumas y sustractivas: rojo es el valor 0, el amarillo es 60, el verde es 120, el aguamarina es 180, el azul es 240, el magenta es de 300 y el ciclo vuelve a rojo en 360.

Si no hay ningún color dominante &mdash; , por ejemplo, el color es blanco o negro o un sombreado gris &mdash; , el matiz es indefinido y normalmente se establece en 0. 

Los valores de saturación pueden oscilar entre 0 y 100 e indican la pureza del color. Un valor de saturación de 100 es el color más puro, mientras que los valores inferiores a 100 hacen que el color sea más gris. Un valor de saturación de 0 da como resultado un sombreado de gris.

El valor de luminosidad (o luminosidad) indica el brillo del color. Un valor de luminosidad de 0 es negro, independientemente de los demás valores. Del mismo modo, el valor de luminosidad 100 es blanco. 

El valor HSL (0, 100, 50) es el valor RGB (FF, 00, 00), que es rojo puro. El valor HSL (180, 100, 50) es el valor RGB (00, FF, FF), aguamarina puro. A medida que se reduce la saturación, se reduce el componente de color dominante y se incrementan los demás componentes. En un nivel de saturación de 0, todos los componentes son los mismos y el color es un sombreado gris. Disminuya la luminosidad para ir a negro; aumente la luminosidad para ir a blanco.

## <a name="the-blend-modes-in-detail"></a>Modos de Blend en detalle

Al igual que los demás modos de fusión, los cuatro modos de mezcla no separables implican un destino (que suele ser una imagen de mapa de bits) y un origen, que suele ser un solo color o degradado. Los modos de mezcla combinan los valores de matiz, saturación y luminosidad del destino y el origen:

| Modo de mezcla   | Componentes del origen | Componentes del destino |
| ------------ | ---------------------- | --------------------------- |
| `Hue`        | Matiz                    | Saturación y luminosidad   |
| `Saturation` | Saturación             | Matiz y luminosidad          |
| `Color`      | Matiz y saturación     | Luminosidad                  | 
| `Luminosity` | Luminosidad             | Matiz y saturación          | 

Vea la especificación del [**nivel 1 de composición y combinación**](https://www.w3.org/TR/compositing-1/) de W3C para los algoritmos.

La página **modos de mezcla no separables** contiene un `Picker` para seleccionar uno de estos modos de mezcla y tres `Slider` vistas para seleccionar un color HSL:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.NonSeparableBlendModesPage"
             Title="Non-Separable Blend Modes">

    <StackLayout>
        <skiaviews:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blendModePicker"
                Title="Blend Mode"
                Margin="10, 0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlendMode}">
                    <x:Static Member="skia:SKBlendMode.Hue" />
                    <x:Static Member="skia:SKBlendMode.Saturation" />
                    <x:Static Member="skia:SKBlendMode.Color" />
                    <x:Static Member="skia:SKBlendMode.Luminosity" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="hueSlider"
                Maximum="360"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="satSlider"
                Maximum="100"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="lumSlider"
                Maximum="100"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <StackLayout Orientation="Horizontal">
            <Label x:Name="hslLabel"
                   HorizontalOptions="CenterAndExpand" />

            <Label x:Name="rgbLabel"
                   HorizontalOptions="CenterAndExpand" />

        </StackLayout>
    </StackLayout>
</ContentPage>
```

Para ahorrar espacio, las tres `Slider` vistas no se identifican en la interfaz de usuario del programa. Deberá recordar que el orden es matiz, saturación y luminosidad. Dos `Label` vistas en la parte inferior de la página muestran los valores de color HSL y RGB.

El archivo de código subyacente carga uno de los recursos de mapa de bits, muestra lo más grande posible en el lienzo y, a continuación, trata el lienzo con un rectángulo. El color del rectángulo se basa en las tres `Slider` vistas y el modo de mezcla es el seleccionado en la `Picker` :

```csharp
public partial class NonSeparableBlendModesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(NonSeparableBlendModesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg");
    SKColor color;

    public NonSeparableBlendModesPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs e)
    {
        // Calculate new color based on sliders
        color = SKColor.FromHsl((float)hueSlider.Value,
                                (float)satSlider.Value,
                                (float)lumSlider.Value);

        // Use labels to display HSL and RGB color values
        color.ToHsl(out float hue, out float sat, out float lum);

        hslLabel.Text = String.Format("HSL = {0:F0} {1:F0} {2:F0}",
                                      hue, sat, lum);

        rgbLabel.Text = String.Format("RGB = {0:X2} {1:X2} {2:X2}",
                                      color.Red, color.Green, color.Blue);

        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);

        // Get blend mode from Picker
        SKBlendMode blendMode =
            (SKBlendMode)(blendModePicker.SelectedIndex == -1 ?
                                        0 : blendModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Color = color;
            paint.BlendMode = blendMode;
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Tenga en cuenta que el programa no muestra el valor de color HSL como seleccionado por los tres controles deslizantes. En su lugar, crea un valor de color a partir de los controles deslizantes y, a continuación, usa el [`ToHsl`](xref:SkiaSharp.SKColor.ToHsl*) método para obtener los valores de matiz, saturación y luminosidad. Esto se debe a que el `FromHsl` método convierte un color HSL en un color RGB, que se almacena internamente en la `SKColor` estructura. El `ToHsl` método convierte de RGB a HSL, pero el resultado no siempre será el valor original. 

Por ejemplo, `FromHsl` convierte el valor HSL (180, 50, 0) en el color RGB (0,0) porque `Luminosity` es cero. El `ToHsl` método convierte el color RGB (0,0) en el color HSL (0,0) porque los valores de matiz y saturación son irrelevantes. Cuando se usa este programa, es mejor ver la representación del color HSL que usa el programa en lugar de la que se especificó con los controles deslizantes.

El `SKBlendModes.Hue` modo de mezcla utiliza el nivel de matiz del origen mientras conserva los niveles de saturación y luminosidad del destino. Al probar este modo de mezcla, los controles deslizantes saturación y luminosidad deben establecerse en un valor distinto de 0 o 100 porque, en esos casos, el matiz no se define de forma única.

[![Modos de mezcla no separables: matiz](non-separable-images/NonSeparableBlendModes-Hue.png "Modos de mezcla no separables: matiz")](non-separable-images/NonSeparableBlendModes-Hue-Large.png#lightbox)

Cuando se usa, el control deslizante se establece en 0 (como con la captura de pantalla de iOS a la izquierda), todo se convierte en rojizo. Pero esto no significa que la imagen esté totalmente ausente de verde y azul. Obviamente, hay sombras de grises presentes en el resultado. Por ejemplo, el color RGB (40, 40, C0) es equivalente al color HSL (240, 50, 50). El matiz es azul, pero el valor de saturación de 50 indica que también hay componentes rojo y verde. Si el matiz se establece en 0 con `SKBlendModes.Hue` , el color HSL es (0, 50, 50), que es el color RGB (C0, 40, 40). Todavía hay componentes azules y verdes, pero ahora el componente dominante es rojo.

El `SKBlendModes.Saturation` modo de mezcla combina el nivel de saturación del origen con el matiz y la luminosidad del destino. Al igual que el matiz, la saturación no está bien definida si la luminosidad es 0 o 100. En teoría, los valores de luminosidad entre esos dos extremos deberían funcionar. Sin embargo, el valor de luminosidad parece afectar al resultado más de lo que debería. Establezca la luminosidad en 50 y puede ver cómo puede establecer el nivel de saturación de la imagen:

[![Modos de mezcla no separables: saturación](non-separable-images/NonSeparableBlendModes-Saturation.png "Modos de mezcla no separables: saturación")](non-separable-images/NonSeparableBlendModes-Saturation-Large.png#lightbox)

Puede usar este modo de mezcla para aumentar la saturación de color de una imagen mate, o puede reducir la saturación a cero (como en la captura de pantalla de iOS a la izquierda) para una imagen resultante formada solo por tonos grises.

El `SKBlendModes.Color` modo de mezcla conserva la luminosidad del destino, pero utiliza el matiz y la saturación del origen. De nuevo, esto implica que cualquier configuración del control deslizante de luminosidad en algún lugar entre los extremos debe funcionar. 

[![Modos de mezcla no separables: color](non-separable-images/NonSeparableBlendModes-Color.png "Modos de mezcla no separables: color")](non-separable-images/NonSeparableBlendModes-Color-Large.png#lightbox)

Verá una aplicación de este modo de mezcla en breve.

Por último, el `SKBlendModes.Luminosity` modo de mezcla es el contrario de `SKBlendModes.Color` . Conserva el matiz y la saturación del destino, pero utiliza la luminosidad del origen. El `Luminosity` modo de mezcla es el más misterioso del lote: los controles deslizantes de matiz y saturación afectan a la imagen, pero incluso a la luminosidad media, la imagen no es distinta:

[![Modos de mezcla no separables-luminosidad](non-separable-images/NonSeparableBlendModes-Luminosity.png "Modos de mezcla no separables-luminosidad")](non-separable-images/NonSeparableBlendModes-Luminosity-Large.png#lightbox)

En teoría, el aumento o disminución de la luminosidad de una imagen debe ser más claro o más oscuro. Curiosamente, el [ejemplo de luminosidad en la **referencia** de Skia SkBlendMode](https://skia.org/user/api/SkBlendMode_Reference#Luminosity) es bastante similar.

Por lo general, no es el caso que desee usar uno de los modos de mezcla no separables con un origen que conste de un solo color aplicado a toda la imagen de destino. El efecto es demasiado grande. Deseará restringir el efecto a una parte de la imagen. En ese caso, el origen probablemente incorporará transparancy o quizás el origen se limite a un gráfico más pequeño.

## <a name="a-matte-for-a-separable-mode"></a>Un mate para un modo separable

Este es uno de los mapas de bits incluidos como un recurso en el ejemplo [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . El nombre de archivo es **Banana.jpg** :

![Monkey de banana](non-separable-images/Banana.jpg "Monkey de banana")

Es posible crear un mate que abarque solo el plátano. También es un recurso en el ejemplo [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . El nombre de archivo es **BananaMatte.png** :

![Mate de banana](non-separable-images/BananaMatte.png "Mate de banana")

Aparte de la forma de banana negro, el resto del mapa de bits es transparente.

La página **azul banana** usa ese mate para modificar el matiz y la saturación de la banana que contiene el Monkey, pero para no cambiar nada más en la imagen. 

En la `BlueBananaPage` clase siguiente, el mapa de bits **Banana.jpg** se carga como un campo. El constructor carga el **BananaMatte.png** mapa de bits como el `matteBitmap` objeto, pero no conserva ese objeto más allá del constructor. En su lugar, se crea un tercer mapa de bits denominado `blueBananaBitmap` . `matteBitmap`Se dibuja en `blueBananaBitmap` seguido de un `SKPaint` con su `Color` establecido en Blue y su `BlendMode` establecido en `SKBlendMode.SrcIn` . El `blueBananaBitmap` permanece más transparente pero con una imagen de azul puro sólida de la banana:

```csharp
public class BlueBananaPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BlueBananaPage),
        "SkiaSharpFormsDemos.Media.Banana.jpg");

    SKBitmap blueBananaBitmap;

    public BlueBananaPage()
    {
        Title = "Blue Banana";

        // Load banana matte bitmap (black on transparent)
        SKBitmap matteBitmap = BitmapExtensions.LoadBitmapResource(
            typeof(BlueBananaPage),
            "SkiaSharpFormsDemos.Media.BananaMatte.png");

        // Create a bitmap with a solid blue banana and transparent otherwise
        blueBananaBitmap = new SKBitmap(matteBitmap.Width, matteBitmap.Height);

        using (SKCanvas canvas = new SKCanvas(blueBananaBitmap))
        {
            canvas.Clear();
            canvas.DrawBitmap(matteBitmap, new SKPoint(0, 0));

            using (SKPaint paint = new SKPaint())
            {
                paint.Color = SKColors.Blue;
                paint.BlendMode = SKBlendMode.SrcIn;
                canvas.DrawPaint(paint);
            }
        }

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

        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);

        using (SKPaint paint = new SKPaint())
        {
            paint.BlendMode = SKBlendMode.Color;
            canvas.DrawBitmap(blueBananaBitmap, 
                              info.Rect, 
                              BitmapStretch.Uniform, 
                              paint: paint);
        }
    }
}
```

El `PaintSurface` controlador dibuja el mapa de bits con el Monkey que contiene el plátano. Este código va seguido de la presentación de `blueBananaBitmap` con `SKBlendMode.Color` . En la superficie del plátano, el matiz y la saturación de cada píxel se sustituyen por el azul sólido, que corresponde a un valor de matiz de 240 y un valor de saturación de 100. La luminosidad, sin embargo, sigue siendo la misma, lo que significa que el plátano sigue teniendo una textura realista a pesar de su nuevo color:

[![Plátano azul](non-separable-images/BlueBanana.png "Plátano azul")](non-separable-images/BlueBanana-Large.png#lightbox)

Intente cambiar el modo de mezcla a `SKBlendMode.Saturation` . El plátano sigue siendo amarillo, pero es un amarillo más intenso. En una aplicación real, esto podría ser un efecto más deseable que convertir el plátano en azul.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)