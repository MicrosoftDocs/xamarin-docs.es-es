---
title: Integración con Xamarin.Forms
description: En este artículo se explica cómo crear gráficos de SkiaSharp que responden a tocar y elementos de Xamarin.Forms y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 288224F1-7AEE-4148-A88D-A70C03F83D7A
author: davidbritch
ms.author: dabritch
ms.date: 02/09/2017
ms.openlocfilehash: 586cf0eaa7b0f38af61c9f7d619b30a39023a19e
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652959"
---
# <a name="integrating-with-xamarinforms"></a>Integración con Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Crear gráficos de SkiaSharp que responden a la función táctil y los elementos de Xamarin.Forms_

Gráficos de SkiaSharp pueden integrarse con el resto de Xamarin.Forms de varias maneras. Puede combinar un lienzo de SkiaSharp y elementos de Xamarin.Forms en la misma página y elementos de Xamarin.Forms incluso posición encima de un lienzo de SkiaSharp:

![](integration-images/integrationexample.png "Seleccionar un color con los controles deslizantes")

Otro enfoque para crear gráficos interactivos de SkiaSharp en Xamarin.Forms es a través del tacto.
La segunda página en el [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) programa se titula **pulse relleno de alternancia**. Dibuja un círculo simple de dos maneras de &mdash; sin relleno y con un relleno &mdash; activar o desactivar para un punteo. El [ `TapToggleFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml.cs) clase muestra cómo puede modificar los gráficos de SkiaSharp en respuesta a la entrada del usuario.

Para esta página, el `SKCanvasView` se crea una instancia de clase en el [TapToggleFill.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml) archivo, lo que también establece un objeto Xamarin.Forms [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) en la vista:

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

Tenga en cuenta el `skia` declaración de espacio de nombres XML.

El `Tapped` controlador para el `TapGestureRecognizer` objeto simplemente alterna el valor de un campo booleano y llama a la [ `InvalidateSurface` ](xref:SkiaSharp.Views.Forms.SKCanvasView.InvalidateSurface) método `SKCanvasView`:

```csharp
bool showFill = true;
...
void OnCanvasViewTapped(object sender, EventArgs args)
{
    showFill ^= true;
    (sender as SKCanvasView).InvalidateSurface();
}
```

La llamada a `InvalidateSurface` eficazmente genera una llamada a la `PaintSurface` controlador, que utiliza el `showFill` campo para rellenar o no de relleno del círculo:

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

El `StrokeWidth` propiedad se estableció en 50 resaltar la diferencia. También puede ver el ancho de línea entera por el interior de dibujo en primer lugar y, a continuación, en el esquema. De forma predeterminada, gráficos de las cifras que se dibujan más adelante en el `PaintSurface` controlador de eventos ocultar aquellas dibujadas anteriormente en el controlador.

El **Color explorar** página se muestra cómo puede integrar también SkiaSharp gráficos con otros elementos de Xamarin.Forms y también se muestra la diferencia entre los dos métodos alternativos para definir los colores de SkiaSharp. Estático [ `SKColor.FromHsl` ](xref:SkiaSharp.SKColor.FromHsl(System.Single,System.Single,System.Single,System.Byte)) método crea un `SKColor` valor según el modelo de matiz-saturación-luminosidad:

```csharp
public static SKColor FromHsl (Single h, Single s, Single l, Byte a)
```

Estático [ `SKColor.FromHsv` ](xref:SkiaSharp.SKColor.FromHsv(System.Single,System.Single,System.Single,System.Byte)) método crea un `SKColor` valor según el modelo de valor de saturación de matiz similar:

```csharp
public static SKColor FromHsv (Single h, Single s, Single v, Byte a)
```

En ambos casos, el `h` intervalos de argumento de 0 a 360. El `s`, `l`, y `v` argumentos oscilar entre 0 y 100. El `a` (alfa o la opacidad) rangos de argumento de 0 a 255.

El [ **ColorExplorePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml) archivo crea dos `SKCanvasView` objetos en un `StackLayout` en paralelo con `Slider` y `Label` vistas que permiten al usuario seleccionar HSL y HSV valores de color:

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

Los dos `SKCanvasView` elementos se encuentran en una sola celda `Grid` con una `Label` por allá encima para mostrar el valor de color RGB resultante.

El [ **ColorExplorePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml.cs) archivo de código subyacente es relativamente sencillo. El recurso compartido `ValueChanged` controlador para los tres `Slider` elementos simplemente invalida tanto `SKCanvasView` elementos. El `PaintSurface` controladores borrar el lienzo con el color indicado por el `Slider` elementos y también establece la `Label` sentado en la parte superior de la `SKCanvasView` elementos:

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

En el HSL y HSV modelos de color, el valor de matiz comprendido entre 0 y 360 e indica el matiz del color dominante. Estos son los colores del arco iris tradicionales: rojo, naranja, amarillo, verde, azul, indigo, violeta y atrás en un círculo en rojo.

En el modelo HSL, un valor 0 para la luminosidad siempre es negro y un valor de 100 siempre es blanco. Cuando el valor de saturación es 0, los valores de luminosidad entre 0 y 100 son tonalidades de gris. Al aumentar la saturación, agrega más de color. Colores puros (que son los valores RGB con un componente es igual que 255, otro igual a 0 y el tercer comprendida entre 0 y 255) que se producen cuando la saturación es 100 y la luminosidad es 50.

En el modelo HSV, colores puros se producirá cuando la saturación y el valor 100. Cuando el valor es 0, independientemente de cualquier otra configuración, el color es negro. Tonos de gris se producen cuando la saturación es 0 y el valor varía entre 0 y 100.

Pero es la mejor forma de hacerse una idea de los dos modelos experimentar con ellos:

[![](integration-images/colorexplore-large.png "Captura de pantalla de la página de exploración de Color triple")](integration-images/colorexplore-small.png#lightbox "Triple captura de pantalla de la página de exploración de Color")


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
