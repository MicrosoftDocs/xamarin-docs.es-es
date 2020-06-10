---
title: "los modos de combinación separables" Description: "Use los modos de fusión separables para modificar los colores rojo, verde y azul".
MS. Prod: Xamarin ms. Technology: Xamarin-skiasharp ms. AssetID: 66D1A537-A247-484E-B5B9-FBCB7838FBE9 Author: davidbritch ms. Author: dabritch ms. Date: 08/23/2018 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="the-separable-blend-modes"></a>Modos de fusión separables

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Como vimos en el artículo [**SkiaSharp Porter-Duff Blend modes**](porter-duff.md), los modos de mezcla Porter-Duff generalmente realizan operaciones de recorte. Los modos de fusión separables son diferentes. Los modos separables modifican los componentes de color rojo, verde y azul de una imagen. Los modos de mezcla separables pueden mezclar colores para demostrar que la combinación de rojo, verde y azul es realmente blanca:

![Colores primarios](separable-images/SeparableSample.png "Colores primarios")

## <a name="lighten-and-darken-two-ways"></a>Aclarar y oscurecer dos maneras 

Es habitual tener un mapa de bits que sea algo demasiado oscuro o demasiado claro. Puede usar los modos de mezcla separables para aclarar u oscurecer el imabe.  En realidad, dos de los modos de mezcla que [`SKBlendMode`](xref:SkiaSharp.SKBlendMode) se pueden separar en la enumeración se denominan `Lighten` y `Darken` . 

Estos dos modos se muestran en la página **aclarar y oscurecer** . El archivo XAML crea una instancia de dos `SKCanvasView` objetos y dos `Slider` vistas:

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

La primera `SKCanvasView` y `Slider` `SKBlendMode.Lighten` la demostración y el segundo par demuestran `SKBlendMode.Darken` . Las dos `Slider` vistas comparten el mismo `ValueChanged` controlador y las dos `SKCanvasView` comparten el mismo `PaintSurface` controlador. Ambos controladores de eventos comprueban qué objeto está desencadenando el evento:

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

El `PaintSurface` controlador calcula un rectángulo adecuado para el mapa de bits. El controlador muestra ese mapa de bits y, a continuación, muestra un rectángulo en el mapa de bits utilizando un `SKPaint` objeto con su `BlendMode` propiedad establecida en `SKBlendMode.Lighten` o `SKBlendMode.Darken` . La `Color` propiedad es un sombreado gris basado en `Slider` . En el `Lighten` modo, el color oscila entre el negro y el blanco, pero para el `Darken` modo va de blanco a negro.

Las capturas de pantallas de izquierda a derecha muestran valores cada vez mayores `Slider` , ya que la imagen superior se ilumina y la imagen inferior se oscurece:

[![Aclarar y oscurecer](separable-images/LightenAndDarken.png "Aclarar y oscurecer")](separable-images/LightenAndDarken-Large.png#lightbox)

Este programa muestra la forma normal en la que se usan los modos de mezcla separables: el destino es una imagen de algún tipo, muy a menudo un mapa de bits. El origen es un rectángulo que se muestra utilizando un `SKPaint` objeto con su `BlendMode` propiedad establecida en un modo de mezcla separable. El rectángulo puede ser un color sólido (tal como está aquí) o un degradado. La transparencia _no_ se utiliza generalmente con los modos de mezcla separables.

Al experimentar con este programa, descubrirá que estos dos modos de fusión no aclaran y oscurecen la imagen uniformemente. En su lugar, `Slider` parece establecer un umbral de algún tipo. Por ejemplo, a medida que aumenta el `Slider` para el `Lighten` modo, las áreas más oscuras de la imagen se iluminan primero, mientras que las áreas más claras siguen siendo las mismas.

En el `Lighten` modo, si el píxel de destino es el valor de color RGB (Dr, DG, dB) y el píxel de origen es el color (Sr, SG, SB), el resultado es (o, OG, OB) calculado de la manera siguiente:

 `Or = max(Dr, Sr)` `Og = max(Dg, Sg)`
 `Ob = max(Db, Sb)`

En rojo, verde y azul por separado, el resultado es el mayor del destino y el origen. Esto produce el efecto de aclarar primero las áreas oscuras del destino.

El `Darken` modo es similar, salvo que el resultado es el menor del destino y el origen:

 `Or = min(Dr, Sr)` `Og = min(Dg, Sg)`
 `Ob = min(Db, Sb)`

Los componentes rojo, verde y azul se administran por separado, por lo que se hace referencia a estos modos _de mezcla como modos de fusión_ separables. Por esta razón, las abreviaturas **DC** y **SC** se pueden usar para los colores de origen y destino, y se entiende que los cálculos se aplican a cada uno de los componentes rojo, verde y azul por separado.

En la tabla siguiente se muestran todos los modos de fusión separables con explicaciones breves de lo que hacen. La segunda columna muestra el color de origen que no produce ningún cambio:

| Modo de mezcla   | Sin cambios | Operación |
| ------------ | --------- | --------- |
| `Plus`       | Negro     | Se aclara mediante la adición de colores: SC + DC |
| `Modulate`   | Blanco     | Se oscurece mediante la multiplicación de colores: SC · CD | 
| `Screen`     | Negro     | Complementa el producto de complementos: SC + DC &ndash; SC · CD |
| `Overlay`    | Gris      | Inversa de`HardLight` |
| `Darken`     | Blanco     | Mínimo de colores: mín. (SC, DC) |
| `Lighten`    | Negro     | Número máximo de colores: máximo (SC, DC) |
| `ColorDodge` | Negro     | Brillante el destino en función del origen |
| `ColorBurn`  | Blanco     | Oscurece el destino basándose en el origen | 
| `HardLight`  | Gris      | Similar al efecto de la luz severa |
| `SoftLight`  | Gris      | Similar al efecto de la luz de software | 
| `Difference` | Negro     | Resta el más oscuro de la más clara: ABS (DC &ndash; SC) | 
| `Exclusion`  | Negro     | Similar a, `Difference` pero menor contraste |
| `Multiply`   | Blanco     | Se oscurece mediante la multiplicación de colores: SC · CD |

Se pueden encontrar algoritmos más detallados en las especificaciones W3C [**Comfunding and blending Level 1**](https://www.w3.org/TR/compositing-1/) y Skia [**SkBlendMode Reference**](https://skia.org/user/api/SkBlendMode_Reference), aunque la notación de estos dos orígenes no es la misma. Tenga en cuenta que `Plus` normalmente se considera un modo de mezcla Porter-Duff y `Modulate` no forma parte de la especificación del W3C.

Si el origen es transparente, para todos los modos de fusión separables excepto `Modulate` , el modo de mezcla no tiene ningún efecto. Como ha visto anteriormente, el `Modulate` modo de mezcla incorpora el canal alfa en la multiplicación. De lo contrario, `Modulate` tiene el mismo efecto que `Multiply` . 

Observe los dos modos denominados `ColorDodge` y `ColorBurn` . Las palabras _sobreexponer_ y _grabar_ se originaron en las prácticas fotográficas de Darkroom. Un ampliador crea una impresión fotográfica por luz luminosa a través de un negativo. Sin luz, la impresión es blanca. La impresión se oscurece a medida que la luz cae en la impresión durante un período de tiempo más largo. Los fabricantes de impresión solían utilizar un objeto de mano o pequeño para impedir que parte de la luz caiga en una determinada parte de la impresión, lo que hace que el área sea más clara. Esto se conoce como _dodging_. Por el contrario, el material opaco con un hueco (o el bloqueo de la mayor parte de la luz) podría usarse para dirigir más luz en un punto determinado para oscurecerlo, denominado _grabación_.

El programa **aclarar y grabar** es muy similar a **aclarar y oscurecer**. El archivo XAML está estructurado de la misma forma pero con diferentes nombres de elemento, y el archivo de código subyacente es similar, pero el efecto de estos dos modos de fusión es bastante diferente:

[![Aclarar y grabar](separable-images/DodgeAndBurn.png "Aclarar y grabar")](separable-images/DodgeAndBurn-Large.png#lightbox)

En el caso de `Slider` los valores pequeños, el `Lighten` modo aclara primero las áreas oscuras, a la vez que se `ColorDodge` aclara de manera uniforme.

Los programas de aplicación de procesamiento de imágenes suelen permitir que dodging y Burning se limiten a áreas específicas, al igual que en un Darkroom. Esto puede realizarse mediante degradados o mediante un mapa de bits con diferentes tonalidades de gris.

## <a name="exploring-the-separable-blend-modes"></a>Explorar los modos de mezcla separables

La página **modos de mezcla** separables permite examinar todos los modos de mezcla que se pueden separar. Muestra un destino de mapa de bits y un origen de rectángulo coloreado mediante uno de los modos de mezcla. 

El archivo XAML define un `Picker` (para seleccionar el modo de mezcla) y cuatro controles deslizantes. Los tres primeros controles deslizantes permiten establecer los componentes rojo, verde y azul del origen. El cuarto control deslizante está diseñado para invalidar esos valores estableciendo un sombreado gris. Los controles deslizantes individuales no se identifican, pero los colores indican su función:

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

El archivo de código subyacente carga uno de los recursos de mapa de bits y lo dibuja dos veces, una vez en la mitad superior del lienzo y otra vez en la mitad inferior del lienzo:

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

Hacia la parte inferior del `PaintSurface` controlador, se dibuja un rectángulo sobre el segundo mapa de bits con el modo de mezcla seleccionado y el color seleccionado. Puede comparar el mapa de bits modificado en la parte inferior con el mapa de bits original en la parte superior:

[![Modos de fusión separables](separable-images/SeparableBlendModes.png "Modos de fusión separables")](separable-images/SeparableBlendModes-Large.png#lightbox)

## <a name="additive-and-subtractive-primary-colors"></a>Colores primarios de suma y resta

La página **colores primarios** dibuja tres círculos superpuestos de rojo, verde y azul:

[![Colores primarios aditivos](separable-images/PrimaryColors-Additive.png "Colores primarios aditivos")](separable-images/PrimaryColors-Additive.png#lightbox)

Estos son los colores primarios aditivos. Las combinaciones de dos dos producen cian, magenta y amarillo, y una combinación de los tres es blanco.

Estos tres círculos se dibujan con el `SKBlendMode.Plus` modo, pero también puede usar `Screen` , `Lighten` o `Difference` para el mismo efecto. Este es el programa:

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

El programa incluye un `TabGestureRecognizer` . Al pulsar o hacer clic en la pantalla, el programa utiliza `SKBlendMode.Multiply` para mostrar las tres principales sustractivas:

[![Colores primarios restados](separable-images/PrimaryColors-Subtractive.png "Colores primarios restados")](separable-images/PrimaryColors-Subtractive-Large.png#lightbox)

El `Darken` modo también funciona para este mismo efecto.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
