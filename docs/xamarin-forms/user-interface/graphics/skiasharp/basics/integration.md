---
title: Integración con Xamarin.Forms
description: En este artículo se explica cómo crear gráficos de SkiaSharp que respondan a los elementos táctiles y a Xamarin.Forms los elementos, y se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 288224F1-7AEE-4148-A88D-A70C03F83D7A
author: davidbritch
ms.author: dabritch
ms.date: 02/09/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8288ad3238babe1ce6abb6d9517cdae71373d27c
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366625"
---
# <a name="integrating-with-no-locxamarinforms"></a>Integración con Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Crear gráficos de SkiaSharp que respondan a Xamarin.Forms los elementos Touch y_

Los gráficos de SkiaSharp se pueden integrar con el resto de de Xamarin.Forms varias maneras. Puede combinar un lienzo SkiaSharp y Xamarin.Forms los elementos de la misma página e incluso colocar Xamarin.Forms los elementos en la parte superior de un lienzo SkiaSharp:

![Seleccionar un color con controles deslizantes](integration-images/integrationexample.png)

Otro enfoque para crear gráficos interactivos de SkiaSharp en Xamarin.Forms es el toque.
La segunda página del programa [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) tiene derecho a **activar o desactivar relleno**. Dibuja un círculo simple de dos maneras &mdash; sin relleno y con un relleno &mdash; que se alterna mediante una derivación. La [`TapToggleFillPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml.cs) clase muestra cómo puede modificar los gráficos de SkiaSharp en respuesta a los datos proporcionados por el usuario.

En esta página, `SKCanvasView` se crea una instancia de la clase en el archivo [TapToggleFill. Xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml) , que también establece un Xamarin.Forms [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) en la vista:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.TapToggleFillPage"
             Title="Tap Toggle Fill">

    <skia:SKCanvasView PaintSurface="OnCanvasViewPaintSurface">
        <skia:SKCanvasView.GestureRecognizers>
            <TapGestureRecognizer Tapped="OnCanvasViewTapped" />
        </skia:SKCanvasView.GestureRecognizers>
    </skia:SKCanvasView>
</ContentPage>
```

Observe la `skia` declaración del espacio de nombres XML.

El `Tapped` controlador del `TapGestureRecognizer` objeto simplemente alterna el valor de un campo booleano y llama al [`InvalidateSurface`](xref:SkiaSharp.Views.Forms.SKCanvasView.InvalidateSurface) método de `SKCanvasView` :

```csharp
bool showFill = true;
...
void OnCanvasViewTapped(object sender, EventArgs args)
{
    showFill ^= true;
    (sender as SKCanvasView).InvalidateSurface();
}
```

La llamada a `InvalidateSurface` genera de forma eficaz una llamada al `PaintSurface` controlador, que usa el `showFill` campo para rellenar o no rellenar el círculo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = Color.Red.ToSKColor(),
        StrokeWidth = 50
    };
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);

    if (showFill)
    {
        paint.Style = SKPaintStyle.Fill;
        paint.Color = SKColors.Blue;
        canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    }
}
```

La `StrokeWidth` propiedad se ha establecido en 50 para acentuar la diferencia. También puede ver el ancho de línea completo dibujando el interior primero y después el contorno. De forma predeterminada, las cifras de gráficos que se dibujan más adelante en el `PaintSurface` controlador de eventos ocultan las dibujadas anteriormente en el controlador.

La página **exploración en color** muestra cómo también puede integrar gráficos SkiaSharp con otros Xamarin.Forms elementos y también muestra la diferencia entre dos métodos alternativos para definir colores en SkiaSharp. El [`SKColor.FromHsl`](xref:SkiaSharp.SKColor.FromHsl(System.Single,System.Single,System.Single,System.Byte)) método estático crea un `SKColor` valor basado en el modelo de matiz-saturación-luminosidad:

```csharp
public static SKColor FromHsl (Single h, Single s, Single l, Byte a)
```

El [`SKColor.FromHsv`](xref:SkiaSharp.SKColor.FromHsv(System.Single,System.Single,System.Single,System.Byte)) método estático crea un `SKColor` valor basado en el modelo de matiz-saturación-valor similar:

```csharp
public static SKColor FromHsv (Single h, Single s, Single v, Byte a)
```

En ambos casos, el `h` argumento oscila entre 0 y 360. Los `s` `l` argumentos, y `v` van de 0 a 100. El `a` argumento (alfa u opacidad) va de 0 a 255.

El archivo [**ColorExplorePage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml) crea dos `SKCanvasView` objetos en `StackLayout` paralelo con `Slider` `Label` vistas y que permiten al usuario seleccionar los valores de color HSL y HSV:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Basics.ColorExplorePage"
             Title="Color Explore">
    <StackLayout>
        <!-- Hue slider -->
        <Slider x:Name="hueSlider"
                Maximum="360"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference hueSlider},
                              Path=Value,
                              StringFormat='Hue = {0:F0}'}" />

        <!-- Saturation slider -->
        <Slider x:Name="saturationSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference saturationSlider},
                              Path=Value,
                              StringFormat='Saturation = {0:F0}'}" />

        <!-- Lightness slider -->
        <Slider x:Name="lightnessSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference lightnessSlider},
                              Path=Value,
                              StringFormat='Lightness = {0:F0}'}" />

        <!-- HSL canvas view -->
        <Grid VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="hslCanvasView"
                               PaintSurface="OnHslCanvasViewPaintSurface" />

            <Label x:Name="hslLabel"
                   HorizontalOptions="Center"
                   VerticalOptions="Center"
                   BackgroundColor="Black"
                   TextColor="White" />
        </Grid>

        <!-- Value slider -->
        <Slider x:Name="valueSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference valueSlider},
                              Path=Value,
                              StringFormat='Value = {0:F0}'}" />

        <!-- HSV canvas view -->
        <Grid VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="hsvCanvasView"
                               PaintSurface="OnHsvCanvasViewPaintSurface" />

            <Label x:Name="hsvLabel"
                   HorizontalOptions="Center"
                   VerticalOptions="Center"
                   BackgroundColor="Black"
                   TextColor="White" />
        </Grid>
    </StackLayout>
</ContentPage>
```

Los dos `SKCanvasView` elementos se encuentran en una sola celda `Grid` con una `Label` posición en la parte superior para mostrar el valor del color RGB resultante.

El archivo de código subyacente [**ColorExplorePage.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml.cs) es relativamente sencillo. El `ValueChanged` controlador compartido para los tres `Slider` elementos simplemente invalida ambos `SKCanvasView` elementos. Los `PaintSurface` Controladores borran el lienzo con el color indicado por los `Slider` elementos y también establecen el `Label` elemento sentado encima de los `SKCanvasView` elementos:

```csharp
public partial class ColorExplorePage : ContentPage
{
    public ColorExplorePage()
    {
        InitializeComponent();

        hueSlider.Value = 0;
        saturationSlider.Value = 100;
        lightnessSlider.Value = 50;
        valueSlider.Value = 100;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        hslCanvasView.InvalidateSurface();
        hsvCanvasView.InvalidateSurface();
    }

    void OnHslCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKColor color = SKColor.FromHsl((float)hueSlider.Value,
                                        (float)saturationSlider.Value,
                                        (float)lightnessSlider.Value);
        args.Surface.Canvas.Clear(color);

        hslLabel.Text = String.Format(" RGB = {0:X2}-{1:X2}-{2:X2} ",
                                      color.Red, color.Green, color.Blue);
    }

    void OnHsvCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKColor color = SKColor.FromHsv((float)hueSlider.Value,
                                        (float)saturationSlider.Value,
                                        (float)valueSlider.Value);
        args.Surface.Canvas.Clear(color);

        hsvLabel.Text = String.Format(" RGB = {0:X2}-{1:X2}-{2:X2} ",
                                      color.Red, color.Green, color.Blue);
    }
}
```

En los modelos de color HSL y HSV, el valor de matiz va de 0 a 360 e indica el matiz dominante del color. Estos son los colores tradicionales del arco iris: rojo, naranja, amarillo, verde, azul, Indigo, violeta y de vuelta en un círculo a rojo.

En el modelo HSL, un valor 0 para luminosidad es siempre negro y un valor 100 siempre es blanco. Cuando el valor de saturación es 0, los valores de luminosidad entre 0 y 100 son tonos de gris. Aumentar la saturación agrega más color. Los colores puros (que son valores RGB con un componente igual a 255, otro igual a 0 y el tercero, entre 0 y 255), se producen cuando la saturación es 100 y la luminosidad es 50.

En el modelo HSV, los colores puros se producen cuando la saturación y el valor son 100. Cuando el valor es 0, independientemente de cualquier otra configuración, el color es negro. Se producen sombras grises cuando la saturación es 0 y los intervalos de valores de 0 a 100.

Pero la mejor manera de hacerse una idea de los dos modelos es experimentar con ellos mismo:

[![Captura de pantalla triple de la página explorar color](integration-images/colorexplore-large.png)](integration-images/colorexplore-small.png#lightbox "Captura de pantalla triple de la página explorar color")

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)