---
title: Puntos y guiones de SkiaSharp
description: Este artículo explora cómo supere las complejidades de dibujo de líneas de puntos y guiones de SkiaSharp y esto muestra con código de ejemplo.
ms.prod: xamarin
ms.assetid: 8E9BCC13-830C-458C-9FC8-ECB4EAE66078
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 2d02e79ff51468572250d1a7ce7c6d3da103c03a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770528"
---
# <a name="dots-and-dashes-in-skiasharp"></a>Puntos y guiones de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Supere las complejidades de dibujo de líneas de puntos y guiones de SkiaSharp_

SkiaSharp le permite dibujar líneas que no son sólidos, pero en su lugar están formados por puntos y guiones:

![](dots-images/dottedlinesample.png "Línea de puntos")

Hacer esto con un *efecto de la ruta de acceso*, que es una instancia de la [ `SKPathEffect` ](xref:SkiaSharp.SKPathEffect) clase que se establece en el [ `PathEffect` ](xref:SkiaSharp.SKPaint.PathEffect) propiedad de `SKPaint`. Puede crear una ruta de acceso mediante uno de los métodos de creación estático definidos por efecto (o combinar efectos de ruta de acceso) `SKPathEffect`. (`SKPathEffect` es uno de los efectos de seis admite SkiaSharp; los demás se describen en la sección [ **SkiaSharp efecto**](../effects/index.md).)

Para dibujar líneas de puntos o guiones, use el [ `SKPathEffect.CreateDash` ](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single)) método estático. Hay dos argumentos: Esta primera es una matriz de `float` valores que indican las longitudes de los puntos y guiones y la longitud de los espacios que hay entre ellos. Esta matriz debe tener un número par de elementos, y debe haber al menos dos elementos. (Puede haber cero elementos en la matriz pero que da lugar a una línea sólida.) Si hay dos elementos, la primera es la longitud de un punto o guión y el segundo es la longitud del espacio antes del siguiente punto o guión. Si hay más de dos elementos, a continuación, se encuentran en este orden: dash longitud, longitud del espacio, longitud del guión, duración de la brecha y así sucesivamente.

Por lo general, deseará hacer las longitudes de dash y gap un múltiplo del ancho del trazo. Si el ancho del trazo es de 10 píxeles, por ejemplo, a continuación, la matriz {10, 10} dibujará una línea de puntos donde las brechas y los puntos son la misma longitud que el grosor del trazo.

Sin embargo, el `StrokeCap` configuración de la `SKPaint` objeto también afecta a estos puntos y guiones. Como verá en breve, tiene un impacto en los elementos de esta matriz.

Números separados por puntos y se muestran las líneas discontinuas en la **puntos y guiones** página. El [ **DotsAndDashesPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml) archivo crean instancias de dos `Picker` vistas, uno para que le permite seleccionar un extremo del trazo y el segundo para seleccionar una matriz de dash:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Paths.DotsAndDashesPage"
             Title="Dots and Dashes">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Picker x:Name="strokeCapPicker"
                Title="Stroke Cap"
                Grid.Row="0"
                Grid.Column="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKStrokeCap}">
                    <x:Static Member="skia:SKStrokeCap.Butt" />
                    <x:Static Member="skia:SKStrokeCap.Round" />
                    <x:Static Member="skia:SKStrokeCap.Square" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="dashArrayPicker"
                Title="Dash Array"
                Grid.Row="0"
                Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>10, 10</x:String>
                    <x:String>30, 10</x:String>
                    <x:String>10, 10, 30, 10</x:String>
                    <x:String>0, 20</x:String>
                    <x:String>20, 20</x:String>
                    <x:String>0, 20, 20, 20</x:String>
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                PaintSurface="OnCanvasViewPaintSurface"
                                Grid.Row="1"
                                Grid.Column="0"
                                Grid.ColumnSpan="2" />
    </Grid>
</ContentPage>
```

 Los tres primeros elementos en el `dashArrayPicker` se supone que el ancho del trazo es de 10 píxeles. El {10, 10} matriz es para una línea de puntos, {30, 10} es para una línea discontinua y {10, 10, 30, 10} es para una línea de punto y guión. (Los otros tres se tratarán en breve.)

El [ `DotsAndDashesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml.cs) contiene el archivo de código subyacente del `PaintSurface` controlador de eventos y un par de rutinas auxiliares para tener acceso a la `Picker` vistas:

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
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = (SKStrokeCap)strokeCapPicker.SelectedItem,
        PathEffect = SKPathEffect.CreateDash(GetPickerArray(dashArrayPicker), 20)
    };

    SKPath path = new SKPath();
    path.MoveTo(0.2f * info.Width, 0.2f * info.Height);
    path.LineTo(0.8f * info.Width, 0.8f * info.Height);
    path.LineTo(0.2f * info.Width, 0.8f * info.Height);
    path.LineTo(0.8f * info.Width, 0.2f * info.Height);

    canvas.DrawPath(path, paint); 
}

float[] GetPickerArray(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return new float[0];
    }

    string str = (string)picker.SelectedItem;
    string[] strs = str.Split(new char[] { ' ', ',' }, StringSplitOptions.RemoveEmptyEntries);
    float[] array = new float[strs.Length];

    for (int i = 0; i < strs.Length; i++)
    {
        array[i] = Convert.ToSingle(strs[i]);
    }
    return array;
}
```

En las siguientes capturas de pantalla, la pantalla de iOS en el extremo izquierdo muestra una línea de puntos:

[![](dots-images/dotsanddashes-small.png "Captura de pantalla de la página de puntos y guiones triple")](dots-images/dotsanddashes-large.png#lightbox "Triple captura de pantalla de la página de puntos y guiones")

Sin embargo, la pantalla de Android también se supone que para mostrar una línea de puntos utilizando la matriz {10, 10}, pero en su lugar, la línea es sólida. ¿Qué ocurre? El problema es que la pantalla de Android también tiene una configuración de extremos de trazo de `Square`. Este comando extiende todos los guiones por la mitad del ancho de trazo, provocando que llenar los espacios.

Para evitar este problema cuando se usa un límite de trazo de `Square` o `Round`, debe reducir las longitudes de guión en la matriz por la longitud del trazo (en ocasiones resultan en una longitud de guión de 0) y aumentar las longitudes de separación por la longitud del trazo. Se trata de cómo las tres últimas dash matrices en la `Picker` se calcularon en el archivo XAML:

- {10, 10} se convierte en {0, 20} para una línea de puntos
- {30, 10} se convierte en {20, 20} para una línea discontinua
- {10, 10, 30, 10} se convierte en {0, 20, 20, 20} para una línea de puntos y guiones

Límite de la pantalla aparece UWP que números separados por puntos y guiones en línea para un trazo de `Round`. El `Round` extremo trazo a menudo proporciona la mejor apariencia de puntos y guiones en líneas gruesas.

Hasta ahora no se ha realizado ninguna mención del segundo parámetro para el `SKPathEffect.CreateDash` método. Este parámetro se denomina `phase` y hace referencia a un desplazamiento dentro del modelo de punto y guión al principio de la línea. Por ejemplo, si la matriz de dash es {10, 10} y el `phase` es 10, a continuación, la línea comienza con un hueco en lugar de un punto.

Una aplicación interesante de la `phase` parámetro está en una animación. El **animado espiral** página es similar a la **Archimedean espiral** página, excepto en que el [ `AnimatedSpiralPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/AnimatedSpiralPage.cs) clase anima la `phase` parámetro mediante el Xamarin.Forms `Device.Timer` método:

```csharp
public class AnimatedSpiralPage : ContentPage
{
    const double cycleTime = 250;       // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float dashPhase;

    public AnimatedSpiralPage()
    {
        Title = "Animated Spiral";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;
        stopwatch.Start();

        Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
        {
            double t = stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime;
            dashPhase = (float)(10 * t);
            canvasView.InvalidateSurface();

            if (!pageIsActive)
            {
                stopwatch.Stop();
            }

            return pageIsActive;
        });
    }
    ···  
}
```

Por supuesto, tendrá que ejecutar el programa para ver la animación:

[![](dots-images/animatedspiral-small.png "Triple captura de pantalla de la página de espiral animado")](dots-images/animatedspiral-large.png#lightbox "Triple captura de pantalla de la página de espiral animado")

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
