---
title: Los modos de mezcla no separables
description: Utilizar los modos de mezcla no separables para alterar el matiz, saturación o luminosidad.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97FA2730-87C0-4914-8C9F-C64A02CF9EEF
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 9054539b08da89c0f7d8a93150866fb1b41e63f1
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642781"
---
# <a name="the-non-separable-blend-modes"></a>Los modos de mezcla no separables

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Como se vio en el artículo [ **modos de fusión de SkiaSharp separable**](separable.md), los modos de mezcla separables realizan operaciones en los canales de rojos, verde y azules por separado. Los modos de mezcla no separables no lo hacen. Al operar en los niveles de matiz, saturación y luminosidad del color, los modos de mezcla no separable pueden modificar los colores de maneras interesantes:

![Ejemplo que no sean separables](non-separable-images/NonSeparableSample.png "ejemplo no separables")

## <a name="the-hue-saturation-luminosity-model"></a>El modelo de matiz-saturación-luminosidad

Para entender los modos de mezcla no separables, es necesario para tratar los píxeles de origen y destino, como los colores en el modelo de matiz-saturación-luminosidad. (Luminosidad también se conoce como la luminosidad.)

El modelo de color HSL explicado en el artículo [ **integración con Xamarin.Forms** ](../../basics/integration.md) y un programa de ejemplo de este artículo permite la experimentación con colores HSL. Puede crear un `SKColor` valor con los valores de matiz, saturación y luminosidad estático [ `SKColor.FromHsl` ](xref:SkiaSharp.SKColor.FromHsl*) método.

El matiz representa la longitud de onda dominante del color. Los valores de matiz oscilan entre 0 y 360 y desplazarse por las principales sumas y sustractivas: Rojo es el valor 0, el amarillo es 60, el verde es 120, el aguamarina es 180, el azul es 240, el magenta es 300 y el ciclo vuelve a rojo en 360.

Si no hay ningún color predominante &mdash; por ejemplo, el color es blanco o negro o una sombra gris &mdash; el matiz es undefined y normalmente se establece en 0. 

Los valores de saturación pueden oscilar entre 0 y 100 e indicar la pureza del color. Un valor de saturación de 100 es el color más puro mientras que los valores inferiores a 100 hacer que el color esté más grayish. Un valor de saturación de 0 da como resultado un tono de gris.

El valor de luminosidad (o luminosidad) indica el color claro cómo es. Un valor de luminosidad 0 es negro, independientemente de las otras opciones. De forma similar, un valor de luminosidad de 100 es blanco. 

El valor HSL (0, 100, 50) es el valor RGB (FF, 00, 00), que es rojo puro. El valor HSL (180, 100, 50) es el valor RGB (00, FF, FF), cian puro. A medida que se reduce la saturación, se reduce el componente de color dominante y se incrementan los demás componentes. En un nivel de saturación de 0, todos los componentes son los mismos y el color es un tono de gris. Disminuir la luminosidad para ir a negro; Aumente la luminosidad para ir a en blanco.

## <a name="the-blend-modes-in-detail"></a>Los modos de blend en detalle

Al igual que los otros modos de mezcla, los cuatro modos de blend no separables implican un destino (que suele ser una imagen de mapa de bits) y un origen, que suele ser un único color o un degradado. Los modos de mezcla combinan valores de matiz, saturación y luminosidad desde el origen y el destino:

| Modo de mezcla   | Componentes de origen | Componentes de destino |
| ------------ | ---------------------- | --------------------------- |
| `Hue`        | Matiz                    | Saturación y luminosidad   |
| `Saturation` | Saturación             | Matiz y luminosidad          |
| `Color`      | Matiz y saturación     | Luminosidad                  | 
| `Luminosity` | Luminosidad             | Matiz y saturación          | 

Consulte el W3C [ **la composición y mezcla de nivel 1** ](https://www.w3.org/TR/compositing-1/) especificación de los algoritmos.

El **modos Blend no separables** página contiene un `Picker` para seleccionar uno de estos modos y tres blend `Slider` vistas para seleccionar un color HSL:

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

El archivo de código subyacente carga uno de los recursos de mapa de bits, que muestra tan grande como sea posible en el lienzo y, a continuación, se trata de un rectángulo en el lienzo. El color del rectángulo se basa en los tres `Slider` vistas y el modo de mezcla es el seleccionado en el `Picker`:

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

Tenga en cuenta que el programa no muestra el valor de color HSL seleccionados por los tres controles deslizantes. En su lugar, crea un valor de color de los controles deslizantes y, a continuación, usa el [ `ToHsl` ](xref:SkiaSharp.SKColor.ToHsl*) método para obtener los valores de matiz, saturación y luminosidad. Esto es porque el `FromHsl` método convierte un color HSL en un color RGB, que se almacena internamente en el `SKColor` estructura. El `ToHsl` método convierte de RGB a HSL, pero el resultado no será siempre el valor original. 

Por ejemplo, `FromHsl` convierte el valor HSL (180, 50, 0) en el color RGB (0, 0, 0), porque el `Luminosity` es cero. El `ToHsl` método convierte el color RGB (0, 0, 0) al color HSL (0, 0, 0), porque los valores de matiz y saturación son irrelevantes. Cuando se usa este programa, es mejor que ver con la representación del color HSL que el programa está usando en lugar del que se ha especificado con los controles deslizantes.

El `SKBlendModes.Hue` modo de mezcla utiliza el nivel de matiz del origen de conservando los niveles de saturación y luminosidad del destino. Al probar este modo de mezcla, los controles deslizantes de saturación y luminosidad deben establecerse en algo distinto de 0 o 100 porque en esos casos, el matiz no es define de forma única.

[![Modos de mezcla no separables - Hue](non-separable-images/NonSeparableBlendModes-Hue.png "modos de mezcla no separables - Hue")](non-separable-images/NonSeparableBlendModes-Hue-Large.png#lightbox)

Cuando se use establece el control deslizante en 0 (como ocurre con la captura de pantalla de iOS a la izquierda), todo lo que vuelve rojiza. Pero esto no significa que la imagen esté completamente ausente de verde y azul. Obviamente hay tonalidades de gris todavía presentes en el resultado. Por ejemplo, el color RGB (40, 40, C0) es equivalente al color HSL (240, 50, 50). El matiz es azul, pero el valor de saturación de 50 indica que hay también los componentes rojos y verdes. Si el matiz se establece en 0 con `SKBlendModes.Hue`, el color HSL es (0, 50, 50), que es el color RGB (C0, 40, 40). Existen componentes todavía azules y verde, pero ahora el componente dominante es rojo.

El `SKBlendModes.Saturation` modo blend combina el nivel de saturación de la fuente con el tono y la luminosidad del destino. Al igual que el matiz, saturación no está bien definida si la luminosidad es 0 o 100. En teoría, debería funcionar cualquier valor de luminosidad entre estos dos extremos. Sin embargo, parece que el valor de luminosidad afecta al resultado de más de lo que debería. Establezca la luminosidad en 50 y puede ver cómo se puede establecer el nivel de saturación de la imagen:

[![Modos de mezcla no separables - saturación](non-separable-images/NonSeparableBlendModes-Saturation.png "modos de mezcla no separables - saturación")](non-separable-images/NonSeparableBlendModes-Saturation-Large.png#lightbox)

Puede usar este modo blend para aumentar el color de la saturación de una imagen aburrida, o puede reducir la saturación a cero (como se muestra en la captura de pantalla de iOS a la izquierda) para obtener una imagen resultante formada sólo los tonos de gris.

El `SKBlendModes.Color` modo blend conserva la luminosidad del destino pero usa el tono y la saturación del origen. Nuevamente, esto implica que debería funcionar cualquier configuración del control deslizante de luminosidad en algún lugar entre los extremos. 

[![Color de los modos de mezcla no separables -](non-separable-images/NonSeparableBlendModes-Color.png "modos de mezcla no separables - Color")](non-separable-images/NonSeparableBlendModes-Color-Large.png#lightbox)

Verá una aplicación de este modo blend en breve.

Por último, el `SKBlendModes.Luminosity` modo blend es el opuesto de `SKBlendModes.Color`. Conserva el tono y la saturación del destino pero usa la luminosidad del origen. El `Luminosity` modo de mezcla es el más misterioso del lote: Los controles deslizantes de matiz y saturación afectan a la imagen, pero incluso a la luminosidad media, la imagen no es distinta:

[![Modos de mezcla no separables - luminosidad](non-separable-images/NonSeparableBlendModes-Luminosity.png "modos de mezcla no separables - luminosidad")](non-separable-images/NonSeparableBlendModes-Luminosity-Large.png#lightbox)

En teoría, aumentando o reduciendo la luminosidad de una imagen debe hacerla más clara u oscura. Curiosamente, el [ejemplo luminosidad en el Skia **SkBlendMode referencia** ](https://skia.org/user/api/SkBlendMode_Reference#Luminosity) es bastante similar.

Por lo general no es el caso de que desea usar uno de los modos de mezcla no separables con un origen que consta de un único color que se aplica a la imagen de destino completa. El efecto es simplemente demasiado grande. Desea restringir el efecto en una parte de la imagen. En ese caso, el origen probablemente incorporará transparencia, o quizás el origen estará limitado a un gráfico más pequeño.

## <a name="a-matte-for-a-separable-mode"></a>Para un modo separable mate

Este es uno de los mapas de bits que se incluye como recurso en el [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) ejemplo. Es el nombre de archivo **Banana.jpg**:

![Plátano Monkey](non-separable-images/Banana.jpg "Monkey plátano")

Es posible crear un mate que abarca solo el plátano. Esto también es un recurso en el [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) ejemplo. Es el nombre de archivo **BananaMatte.png**:

![Plátano mate](non-separable-images/BananaMatte.png "mate plátano")

Aparte de la forma plátano negro, el resto del mapa de bits es transparente.

El **plátano azul** página utiliza ese mate para modificar el tono y la saturación de la banana que contiene el objeto monkey, pero no cambiar nada más en la imagen. 

En la siguiente `BlueBananaPage` (clase), el **Banana.jpg** mapa de bits se carga como un campo. Las cargas de constructor la **BananaMatte.png** de mapa de bits como el `matteBitmap` objeto, pero no conserva ese objeto más allá del constructor. En su lugar, un mapa de bits terceros denominado `blueBananaBitmap` se crea. El `matteBitmap` se dibuja en `blueBananaBitmap` seguido por un `SKPaint` con su `Color` establecido en azul y su `BlendMode` establecido en `SKBlendMode.SrcIn`. El `blueBananaBitmap` permanece prácticamente transparente, pero con una imagen azul pura sólida de lo banana:

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

El `PaintSurface` controlador dibuja el mapa de bits con el objeto monkey manteniendo el plátano. Este código va seguido de la presentación de `blueBananaBitmap` con `SKBlendMode.Color`. A través de la superficie de la plátano, tono y la saturación de cada píxel se sustituye por el azul sólido, que corresponde a un valor de matiz de 240 y un valor de saturación de 100. La luminosidad, sin embargo, sigue siendo el mismo, lo que significa que el plátano sigue teniendo una textura realista a pesar de su color nuevo:

[![Azul plátano](non-separable-images/BlueBanana.png "azul plátano")](non-separable-images/BlueBanana-Large.png#lightbox)

Intente cambiar el modo de mezcla para `SKBlendMode.Saturation`. El plátano permanece en amarillo, pero es un amarillo más intenso. En una aplicación de la vida real, esto podría ser un efecto más deseable que activar el plátano azul.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
