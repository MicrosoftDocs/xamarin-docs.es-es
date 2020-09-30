---
title: Puntos y guiones en SkiaSharp
description: En este artículo se explica cómo dominar las complejidades del dibujo de líneas de puntos y guiones en SkiaSharp, y se muestra con código de ejemplo.
ms.prod: xamarin
ms.assetid: 8E9BCC13-830C-458C-9FC8-ECB4EAE66078
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5064a53b140c26acdc5149f5495cc002e657a9b0
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91564009"
---
# <a name="dots-and-dashes-in-skiasharp"></a>Puntos y guiones en SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Domine las complejidades del dibujo de líneas de puntos y guiones en SkiaSharp_

SkiaSharp permite dibujar líneas que no son sólidas, sino que se componen de puntos y guiones:

![Línea de puntos](dots-images/dottedlinesample.png)

Esto se hace con un *efecto de ruta de acceso*, que es una instancia de la [`SKPathEffect`](xref:SkiaSharp.SKPathEffect) clase que se establece en la [`PathEffect`](xref:SkiaSharp.SKPaint.PathEffect) propiedad de `SKPaint` . Puede crear un efecto de trazado (o combinar los efectos de la ruta de acceso) mediante uno de los métodos de creación estáticos definidos por `SKPathEffect` . ( `SKPathEffect` es uno de los seis efectos que admite SkiaSharp; los demás se describen en la sección [**SkiaSharp Effect**](../effects/index.md)).

Para dibujar líneas de puntos o guiones, se usa el [`SKPathEffect.CreateDash`](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single)) método estático. Hay dos argumentos: el primero es una matriz de `float` valores que indican las longitudes de los puntos y guiones, y la longitud de los espacios que hay entre ellos. Esta matriz debe tener un número par de elementos y debe haber al menos dos elementos. (Puede haber cero elementos en la matriz, pero el resultado es una línea sólida). Si hay dos elementos, el primero es la longitud de un punto o un guión, y el segundo es la longitud del espacio antes del punto o guión siguiente. Si hay más de dos elementos, se encuentran en este orden: longitud de guión, longitud de hueco, longitud de guión, longitud de hueco, etc.

Por lo general, querrá que las longitudes de los guiones y los espacios sean múltiplos del ancho del trazo. Por ejemplo, si el ancho del trazo es de 10 píxeles, la matriz {10, 10} dibujará una línea de puntos en la que los puntos y huecos tengan la misma longitud que el grosor del trazo.

Sin embargo, el `StrokeCap` valor del `SKPaint` objeto también afecta a estos puntos y guiones. Como verá en breve, esto afectará a los elementos de esta matriz.

Las líneas de puntos y guiones se muestran en la página **puntos y guiones** . El archivo [**DotsAndDashesPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/DotsAndDashesPage.xaml) crea una instancia `Picker` de dos vistas, una para permitirle seleccionar un extremo del trazo y el segundo para seleccionar una matriz de guiones:

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

 Los tres primeros elementos de `dashArrayPicker` suponen que el ancho del trazo es de 10 píxeles. La matriz {10, 10} es para una línea de puntos, {30, 10} es para una línea discontinua, y {10, 10, 30, 10} es para una línea de puntos y guiones. (Los otros tres se tratarán en breve).

El [`DotsAndDashesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/DotsAndDashesPage.xaml.cs) archivo de código subyacente contiene el `PaintSurface` controlador de eventos y un par de rutinas auxiliares para tener acceso a las `Picker` vistas:

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

En las siguientes capturas de pantalla, la pantalla de iOS de la izquierda muestra una línea de puntos:

[![Captura de pantalla triple de la página de puntos y guiones](dots-images/dotsanddashes-small.png)](dots-images/dotsanddashes-large.png#lightbox "Captura de pantalla triple de la página de puntos y guiones")

Sin embargo, también se supone que la pantalla de Android muestra una línea de puntos con la matriz {10, 10} pero, en su lugar, la línea es sólida. ¿Qué ha ocurrido? El problema es que la pantalla Android también tiene un valor de Bloq Mayús `Square` . Esto extiende todos los guiones a la mitad del ancho del trazo, lo que hace que rellenen los huecos.

Para evitar este problema cuando se usa un extremo de trazo de `Square` o `Round` , debe reducir las longitudes de los guiones en la matriz por la longitud del trazo (a veces, lo que da lugar a una longitud de guión de 0) y aumentar las longitudes de los espacios por la longitud del trazo. Así es como se calcularon las últimas tres matrices en el `Picker` en el archivo XAML:

- {10, 10} se convierte en {0, 20} para una línea de puntos
- {30, 10} se convierte en {20, 20} para una línea discontinua
- {10, 10, 30, 10} se convierte en {0, 20, 20, 20} para una línea de puntos y guiones

La pantalla de UWP muestra el punto y la línea discontinua de un extremo de trazo de `Round` . El `Round` extremo del trazo suele proporcionar la mejor apariencia de los puntos y los guiones en líneas gruesas.

Hasta ahora no se ha hecho ninguna mención del segundo parámetro al `SKPathEffect.CreateDash` método. Este parámetro se denomina `phase` y hace referencia a un desplazamiento dentro del patrón de puntos y guiones para el principio de la línea. Por ejemplo, si la matriz de guiones es {10, 10} y `phase` es 10, la línea comienza con un espacio en lugar de un punto.

Una aplicación interesante del `phase` parámetro está en una animación. La página de **espiral animada** es similar a la página de **espiral Archimedean** , excepto en que la [`AnimatedSpiralPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/AnimatedSpiralPage.cs) clase anima el `phase` parámetro mediante el Xamarin.Forms `Device.Timer` método:

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

[![Captura de pantalla triple de la página de espiral animada](dots-images/animatedspiral-small.png)](dots-images/animatedspiral-large.png#lightbox "Captura de pantalla triple de la página de espiral animada")

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)