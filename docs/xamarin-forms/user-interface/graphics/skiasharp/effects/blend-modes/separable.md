---
title: Los modos de mezcla separables
description: Utilizar los modos de mezcla separables para modificar los colores rojos, verde y azules.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 66D1A537-A247-484E-B5B9-FBCB7838FBE9
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 621bc872a80cf023bb9428e2d3183e8af611211f
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200389"
---
# <a name="the-separable-blend-modes"></a>Los modos de mezcla separables

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Como se vio en el artículo [ **modos de fusión de SkiaSharp Porter-Duff**](porter-duff.md), por lo general, los modos de mezcla Porter Duff realizan operaciones de recorte. Los modos de mezcla separables son diferentes. Los modos separables modificar los componentes de color rojo, verde y azul individuales de una imagen. Modos de fusión separables pueden mezclar el color para demostrar que es realmente en blanco la combinación de rojo, verde y azul:

![Colores primarios](separable-images/SeparableSample.png "colores primarios")

## <a name="lighten-and-darken-two-ways"></a>Aclarar y oscurecer dos formas 

Es habitual tener un mapa de bits que es algo demasiado oscura o demasiado clara. Puede utilizar los modos de mezcla separables para aclarar u oscurecer el imabe.  De hecho, dos de los modos de blend separables en el [ `SKBlendMode` ](xref:SkiaSharp.SKBlendMode) se denominan enumeración `Lighten` y `Darken`. 

Estos dos modos se muestran en el **aclarar y oscurecer** página. El archivo XAML crea una instancia de dos `SKCanvasView` objetos y dos `Slider` vistas:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.LightenAndDarkenPage"
             Title="Lighten and Darken">
    <StackLayout>
        <skia:SKCanvasView x:Name="lightenCanvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="lightenSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />

        <skia:SKCanvasView x:Name="darkenCanvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="darkenSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />
    </StackLayout>
</ContentPage>
```

La primera `SKCanvasView` y `Slider` demostrar `SKBlendMode.Lighten` y muestra el segundo par `SKBlendMode.Darken`. Los dos `Slider` vistas comparten el mismo `ValueChanged` controlador y los dos `SKCanvasView` comparten el mismo `PaintSurface` controlador. Ambos eventos controladores, consulte el objeto que está desencadenando el evento:

```csharp
public partial class LightenAndDarkenPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                typeof(SeparableBlendModesPage),
                "SkiaSharpFormsDemos.Media.Banana.jpg");

    public LightenAndDarkenPage ()
    {
        InitializeComponent ();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if ((Slider)sender == lightenSlider)
        {
            lightenCanvasView.InvalidateSurface();
        }
        else
        {
            darkenCanvasView.InvalidateSurface();
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find largest size rectangle in canvas
        float scale = Math.Min((float)info.Width / bitmap.Width,
                               (float)info.Height / bitmap.Height);
        SKRect rect = SKRect.Create(scale * bitmap.Width, scale * bitmap.Height);
        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Display bitmap
        canvas.DrawBitmap(bitmap, rect);

        // Display gray rectangle with blend mode
        using (SKPaint paint = new SKPaint())
        {
            if ((SKCanvasView)sender == lightenCanvasView)
            {
                byte value = (byte)(255 * lightenSlider.Value);
                paint.Color = new SKColor(value, value, value);
                paint.BlendMode = SKBlendMode.Lighten;
            }
            else
            {
                byte value = (byte)(255 * (1 - darkenSlider.Value));
                paint.Color = new SKColor(value, value, value);
                paint.BlendMode = SKBlendMode.Darken;
            }

            canvas.DrawRect(rect, paint);
        }
    }
}
```

El `PaintSurface` controlador calcula un rectángulo adecuado para el mapa de bits. El controlador muestra ese mapa de bits y, a continuación, se muestra un rectángulo a través de mapa de bits mediante un `SKPaint` objeto con su `BlendMode` propiedad establecida en `SKBlendMode.Lighten` o `SKBlendMode.Darken`. El `Color` propiedad es una sombra gris según el `Slider`. Para el `Lighten` modo, los rangos de colores del negro a blanco, pero la `Darken` modo lo comprendido entre el blanco y negro.

Las capturas de pantalla de izquierda a derecha se muestran cada vez mayores `Slider` los valores de la imagen superior obtiene más clara y más oscura la imagen inferior:

[![Aclarar y oscurecer](separable-images/LightenAndDarken.png "aclarar y oscurecer")](separable-images/LightenAndDarken-Large.png#lightbox)

Este programa muestra la forma normal en la que se usan los modos de mezcla separables: El destino es una imagen de algún tipo, muy a menudo un mapa de bits. El origen es un rectángulo que se muestran mediante un `SKPaint` objeto con su `BlendMode` propiedad establecida en un modo de mezcla separables. El rectángulo puede ser un color sólido (como aquí) o un degradado. La transparencia es _no_ utilizados normalmente con los modos de mezcla separables.

Mientras experimenta con este programa, descubrirá que estos modos de fusión de dos no aclarar y oscurecer la imagen de manera uniforme. En su lugar, el `Slider` establece un umbral de algún tipo. Por ejemplo, al aumentar el `Slider` para el `Lighten` modo, las áreas más oscuras de la imagen produce luz primero mientras las áreas más claras siguen siendo los mismos.

Para el `Lighten` modo, si el píxel del destino es el valor de color RGB (base de datos de recuperación ante desastres, Dg,) y el píxel del origen es el color (Sr, Sg, Sb), entonces el resultado es (o Og, Ob) calcula como sigue:

 `Or = max(Dr, Sr)``Og = max(Dg, Sg)`
 `Ob = max(Db, Sb)`

Para rojo, verde y azul por separado, el resultado es el mayor número de origen y de destino. Esto produce el efecto de las áreas oscuras del destino de rayo en primer lugar.

El `Darken` modo es similar, salvo que el resultado es el menor de origen y de destino:

 `Or = min(Dr, Sr)``Og = min(Dg, Sg)`
 `Ob = min(Db, Sb)`

Los componentes rojos, verde y azules son cada controlan por separado, motivo por el cual estos modos de fusión se conocen como el _separables_ modos de fusión. Por este motivo, las abreviaturas **Dc** y **Sc** puede usarse para el destino y los colores de origen y se entiende que los cálculos se aplican a cada uno de los componentes rojos, verde y azules por separado.

En la tabla siguiente se muestra todos los modos de blend separables con explicaciones de lo que hacen. La segunda columna muestra el color de origen que se produce ningún cambio:

| Modo de mezcla   | Sin cambios | Operación |
| ------------ | --------- | --------- |
| `Plus`       | Negro     | Aclara mediante la adición de colores: SC + DC |
| `Modulate`   | Blanco     | Se oscurece multiplicando los colores: Seguro CD | 
| `Screen`     | Negro     | Complementa el producto de complementos: SC + DC &ndash; SC · CD |
| `Overlay`    | Gris      | Inverso de `HardLight` |
| `Darken`     | Blanco     | Mínimo de colores: min (Sc, el controlador de dominio) |
| `Lighten`    | Negro     | Máximo de colores: max (Sc, el controlador de dominio) |
| `ColorDodge` | Negro     | Ilumina el destino en función de origen |
| `ColorBurn`  | Blanco     | Se oscurece el destino en función de origen | 
| `HardLight`  | Gris      | Similar al efecto de foco intenso |
| `SoftLight`  | Gris      | Similar al efecto de foco suave | 
| `Difference` | Negro     | Resta el más oscuro del más claro: ABS (DC &ndash; SC) | 
| `Exclusion`  | Negro     | Similar a `Difference` contraste pero más bajo |
| `Multiply`   | Blanco     | Se oscurece multiplicando los colores: Seguro CD |

Los algoritmos más detallados que pueden encontrarse en el W3C [ **la composición y mezcla de nivel 1** ](https://www.w3.org/TR/compositing-1/) especificación y el Skia [ **SkBlendMode referencia** ](https://skia.org/user/api/SkBlendMode_Reference), aunque la notación de estos dos orígenes no es el mismo. Tenga en cuenta que `Plus` normalmente se considera como un modo de blend Porter Duff, y `Modulate` no forma parte de la especificación de W3C.

Si el origen es transparente, a continuación, para todos los separables modos de fusión excepto `Modulate`, el modo de mezcla no tiene ningún efecto. Como ha visto anteriormente, el `Modulate` modo blend incorpora el canal alfa en la multiplicación. En caso contrario, `Modulate` tiene el mismo efecto que `Multiply`. 

Tenga en cuenta los dos modos denominados `ColorDodge` y `ColorBurn`. Las palabras _dodge_ y _grabar_ se originó en prácticas de papel fotográfico oscura. Un ampliador de hace una impresión fotográfica brillando luz a través de un negativo. Sin luz, la impresión es blanca. La impresión obtiene oscura a medida que se encuentra más luz en la impresión de un período de tiempo más largo. Creadores de impresión usan a menudo una mano u objetos pequeños para bloquear algunos de la luz no caiga en una determinada parte de la impresión, que se convierte más claro. Esto se conoce como _. evitar_. Por el contrario, se podría usar opaco material con un hueco en él (o manos bloquea la mayor parte de la luz) para dirigir más luz en una zona determinada para oscurecer denominado _grabación_.

El **aclarar y Burn** programa es muy similar a **aclarar y oscurecer**. El archivo XAML está estructurado el mismo pero con nombres de elementos diferentes y el archivo de código subyacente del mismo modo es bastante similar, pero el efecto de estos modos dos blend es bastante diferente:

[![Aclarar y Burn](separable-images/DodgeAndBurn.png "aclarar y Burn")](separable-images/DodgeAndBurn-Large.png#lightbox)

Para pequeñas `Slider` valores, el `Lighten` modo aclara áreas oscuras en primer lugar, mientras `ColorDodge` aclara más uniformemente.

Programas de aplicación de procesamiento de imágenes suelen permiten. evitar y grabación se restrinja a áreas específicas, al igual que en un cuarto oscuro. Esto puede realizarse mediante los degradados o mediante un mapa de bits con distintas tonalidades de gris.

## <a name="exploring-the-separable-blend-modes"></a>Exploración de los modos de mezcla separables

El **separables modos Blend** página le permite examinar todos los modos de blend separables. Muestra un destino de mapa de bits y un origen de rectángulo de color mediante uno de los modos de mezcla. 

El archivo XAML define una `Picker` (para seleccionar el modo de mezcla) y cuatro de los controles deslizantes. Los primeros tres controles deslizantes permiten definir los componentes rojos, verde y azules del origen. El control deslizante cuarto está pensado para reemplazar estos valores estableciendo una sombra gris. No se identifican los controles deslizantes individuales, pero los colores indican su función:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.SeparableBlendModesPage"
             Title="Separable Blend Modes">

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
                    <x:Static Member="skia:SKBlendMode.Plus" />
                    <x:Static Member="skia:SKBlendMode.Modulate" />
                    <x:Static Member="skia:SKBlendMode.Screen" />
                    <x:Static Member="skia:SKBlendMode.Overlay" />
                    <x:Static Member="skia:SKBlendMode.Darken" />
                    <x:Static Member="skia:SKBlendMode.Lighten" />
                    <x:Static Member="skia:SKBlendMode.ColorDodge" />
                    <x:Static Member="skia:SKBlendMode.ColorBurn" />
                    <x:Static Member="skia:SKBlendMode.HardLight" />
                    <x:Static Member="skia:SKBlendMode.SoftLight" />
                    <x:Static Member="skia:SKBlendMode.Difference" />
                    <x:Static Member="skia:SKBlendMode.Exclusion" />
                    <x:Static Member="skia:SKBlendMode.Multiply" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="redSlider"
                MinimumTrackColor="Red"
                MaximumTrackColor="Red"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="greenSlider"
                MinimumTrackColor="Green"
                MaximumTrackColor="Green"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="blueSlider"
                MinimumTrackColor="Blue"
                MaximumTrackColor="Blue"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="graySlider"
                MinimumTrackColor="Gray"
                MaximumTrackColor="Gray"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="colorLabel"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

Uno de los recursos de mapa de bits se carga el archivo de código subyacente y lo dibuja dos veces, una vez en la mitad superior del lienzo y de nuevo en la parte inferior del lienzo de la mitad:

```csharp
public partial class SeparableBlendModesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(SeparableBlendModesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg"); 

    public SeparableBlendModesPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs e)
    {
        if (sender == graySlider)
        {
            redSlider.Value = greenSlider.Value = blueSlider.Value = graySlider.Value;
        }

        colorLabel.Text = String.Format("Color = {0:X2} {1:X2} {2:X2}",
                                        (byte)(255 * redSlider.Value),
                                        (byte)(255 * greenSlider.Value),
                                        (byte)(255 * blueSlider.Value));

        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Draw bitmap in top half
        SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
        canvas.DrawBitmap(bitmap, rect, BitmapStretch.Uniform);

        // Draw bitmap in bottom halr
        rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
        canvas.DrawBitmap(bitmap, rect, BitmapStretch.Uniform);

        // Get values from XAML controls
        SKBlendMode blendMode =
            (SKBlendMode)(blendModePicker.SelectedIndex == -1 ?
                                        0 : blendModePicker.SelectedItem);

        SKColor color = new SKColor((byte)(255 * redSlider.Value),
                                    (byte)(255 * greenSlider.Value),
                                    (byte)(255 * blueSlider.Value));

        // Draw rectangle with blend mode in bottom half
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = color;
            paint.BlendMode = blendMode;
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Hacia la parte inferior de la `PaintSurface` controlador, un rectángulo se dibuja en el segundo mapa de bits con el modo de mezcla seleccionado y el color seleccionado. Puede comparar el mapa de bits modificado en la parte inferior con el mapa de bits original en la parte superior:

[![Modos de fusión separables](separable-images/SeparableBlendModes.png "modos separables Blend")](separable-images/SeparableBlendModes-Large.png#lightbox)

## <a name="additive-and-subtractive-primary-colors"></a>Adición y sustracción colores primarios

El **colores primarios** página dibuja tres círculos superpuestos de rojo, verde y azul:

[![Colores primarios aditivos](separable-images/PrimaryColors-Additive.png "aditivos colores primarios")](separable-images/PrimaryColors-Additive.png#lightbox)

Estos son los colores primarios aditivos. Combinaciones de dos producen aguamarina, fucsia y amarillo y una combinación de las tres es blanca.

Estos tres círculos se dibujan con el `SKBlendMode.Plus` modo, pero también puede usar `Screen`, `Lighten`, o `Difference` para el mismo efecto. Este es el programa:

```csharp
public class PrimaryColorsPage : ContentPage
{
    bool isSubtractive;

    public PrimaryColorsPage ()
    {
        Title = "Primary Colors";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;

        // Switch between additive and subtractive primaries at tap
        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            isSubtractive ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);

        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
        float radius = Math.Min(info.Width, info.Height) / 4;
        float distance = 0.8f * radius;     // from canvas center to circle center
        SKPoint center1 = center + 
            new SKPoint(distance * (float)Math.Cos(9 * Math.PI / 6),
                        distance * (float)Math.Sin(9 * Math.PI / 6));
        SKPoint center2 = center +
            new SKPoint(distance * (float)Math.Cos(1 * Math.PI / 6),
                        distance * (float)Math.Sin(1 * Math.PI / 6));
        SKPoint center3 = center +
            new SKPoint(distance * (float)Math.Cos(5 * Math.PI / 6),
                        distance * (float)Math.Sin(5 * Math.PI / 6));

        using (SKPaint paint = new SKPaint())
        {
            if (!isSubtractive)
            {
                paint.BlendMode = SKBlendMode.Plus; 
                System.Diagnostics.Debug.WriteLine(paint.BlendMode);

                paint.Color = SKColors.Red;
                canvas.DrawCircle(center1, radius, paint);

                paint.Color = SKColors.Lime;    // == (00, FF, 00)
                canvas.DrawCircle(center2, radius, paint);

                paint.Color = SKColors.Blue;
                canvas.DrawCircle(center3, radius, paint);
            }
            else
            {
                paint.BlendMode = SKBlendMode.Multiply
                System.Diagnostics.Debug.WriteLine(paint.BlendMode);

                paint.Color = SKColors.Cyan;
                canvas.DrawCircle(center1, radius, paint);

                paint.Color = SKColors.Magenta;
                canvas.DrawCircle(center2, radius, paint);

                paint.Color = SKColors.Yellow;
                canvas.DrawCircle(center3, radius, paint);
            }
        }
    }
}
```

El programa incluye un `TabGestureRecognizer`. Cuando pulse o haga clic en la pantalla, el programa usa `SKBlendMode.Multiply` para mostrar las tres principales sustracción:

[![Colores primarios sustracción](separable-images/PrimaryColors-Subtractive.png "sustracción colores primarios")](separable-images/PrimaryColors-Subtractive-Large.png#lightbox)

El `Darken` modo también funciona para este mismo efecto.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
