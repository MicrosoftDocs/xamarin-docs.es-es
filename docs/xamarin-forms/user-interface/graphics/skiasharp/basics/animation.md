---
title: Animación básica en SkiaSharp
description: En este artículo se explica cómo animar los gráficos de SkiaSharp en Xamarin.Forms aplicaciones y cómo hacerlo con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 31C96FD6-07E4-4473-A551-24753A5118C3
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3052220b914b09f18490846bbd2558bbf07e4d3a
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562267"
---
# <a name="basic-animation-in-skiasharp"></a>Animación básica en SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Descubra cómo animar los gráficos de SkiaSharp_

Puede animar los gráficos de SkiaSharp en Xamarin.Forms haciendo `PaintSurface` que el método se llame periódicamente, cada vez que dibuje los gráficos de un modo ligeramente distinto. Esta es una animación que se muestra más adelante en este artículo con círculos concéntricos que aparentemente se expanden desde el centro:

![Algunos círculos concéntricos se expandan aparentemente desde el centro](animation-images/animationexample.png)

La página de la **elipse pulsating** del programa [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) anima los dos ejes de una elipse para que parezca ser pulsating e incluso puede controlar la velocidad de esta Pulsation. El archivo [**PulsatingEllipsePage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml) crea una instancia Xamarin.Forms `Slider` de y un `Label` para mostrar el valor actual del control deslizante. Se trata de una manera común de integrar un `SKCanvasView` con otras Xamarin.Forms vistas:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.PulsatingEllipsePage"
             Title="Pulsating Ellipse">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Slider x:Name="slider"
                Grid.Row="0"
                Maximum="10"
                Minimum="0.1"
                Value="5"
                Margin="20, 0" />

        <Label Grid.Row="1"
               Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='Cycle time = {0:F1} seconds'}"
               HorizontalTextAlignment="Center" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="2"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

El archivo de código subyacente crea una instancia de un `Stopwatch` objeto para que sirva como un reloj de precisión alta. La `OnAppearing` invalidación establece el `pageIsActive` campo en `true` y llama a un método denominado `AnimationLoop` . La `OnDisappearing` invalidación establece este `pageIsActive` campo en `false` :

```csharp
Stopwatch stopwatch = new Stopwatch();
bool pageIsActive;
float scale;            // ranges from 0 to 1 to 0

public PulsatingEllipsePage()
{
    InitializeComponent();
}

protected override void OnAppearing()
{
    base.OnAppearing();
    pageIsActive = true;
    AnimationLoop();
}

protected override void OnDisappearing()
{
    base.OnDisappearing();
    pageIsActive = false;
}
```

El `AnimationLoop` método inicia el `Stopwatch` y, a continuación, el bucle while `pageIsActive` es `true` . Es esencialmente un "bucle infinito" mientras la página está activa, pero no hace que el programa se bloquee porque el bucle concluye con una llamada a `Task.Delay` con el `await` operador, que permite que otras partes de la función del programa. El argumento para que `Task.Delay` se complete después de 1/30 segundos. Esto define la velocidad de fotogramas de la animación.

```csharp
async Task AnimationLoop()
{
    stopwatch.Start();

    while (pageIsActive)
    {
        double cycleTime = slider.Value;
        double t = stopwatch.Elapsed.TotalSeconds % cycleTime / cycleTime;
        scale = (1 + (float)Math.Sin(2 * Math.PI * t)) / 2;
        canvasView.InvalidateSurface();
        await Task.Delay(TimeSpan.FromSeconds(1.0 / 30));
    }

    stopwatch.Stop();
}

```

El `while` bucle comienza obteniendo una hora de ciclo de `Slider` . Este es un tiempo en segundos, por ejemplo, 5. La segunda instrucción calcula un valor de `t` para el *tiempo*. Para un `cycleTime` valor de 5, `t` aumenta de 0 a 1 cada 5 segundos. El argumento de la `Math.Sin` función en la segunda instrucción va de 0 a 2π cada 5 segundos. La `Math.Sin` función devuelve un valor que va de 0 a 1 a 0 y, a continuación, a &ndash; 1 y 0 cada 5 segundos, pero con valores que cambian más lentamente cuando el valor está cerca de 1 o – 1. Se agrega el valor 1 para que los valores sean siempre positivos y, a continuación, se divide por 2, por lo que los valores van de 1/2 a 1 a 1/2 a 0 a 1/2, pero más lentos cuando el valor es aproximadamente 1 y 0. Esto se almacena en el `scale` campo y `SKCanvasView` se invalida.

El `PaintSurface` método usa este `scale` valor para calcular los dos ejes de la elipse:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float maxRadius = 0.75f * Math.Min(info.Width, info.Height) / 2;
    float minRadius = 0.25f * maxRadius;

    float xRadius = minRadius * scale + maxRadius * (1 - scale);
    float yRadius = maxRadius * scale + minRadius * (1 - scale);

    using (SKPaint paint = new SKPaint())
    {
        paint.Style = SKPaintStyle.Stroke;
        paint.Color = SKColors.Blue;
        paint.StrokeWidth = 50;
        canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);

        paint.Style = SKPaintStyle.Fill;
        paint.Color = SKColors.SkyBlue;
        canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);
    }
}
```

El método calcula un radio máximo basado en el tamaño del área de presentación y un radio mínimo basado en el radio máximo. El `scale` valor se anima entre 0 y 1 y de nuevo a 0, por lo que el método utiliza ese para calcular una `xRadius` y `yRadius` que va entre `minRadius` y `maxRadius` . Estos valores se usan para dibujar y rellenar una elipse:

[![Captura de pantalla triple de la página de la elipse pulsating](animation-images/pulsatingellipse-small.png)](animation-images/pulsatingellipse-large.png#lightbox "Captura de pantalla triple de la página de la elipse pulsating")

Observe que el `SKPaint` objeto se crea en un `using` bloque. Al igual que muchas clases SkiaSharp `SKPaint` derivan de `SKObject` , que deriva de `SKNativeObject` , que implementa la [`IDisposable`](xref:System.IDisposable) interfaz. `SKPaint` invalida el `Dispose` método para liberar recursos no administrados.

 `SKPaint`La colocación de un `using` bloque garantiza que `Dispose` se llama al final del bloque para liberar estos recursos no administrados. Esto sucede cuando el recolector de elementos no utilizados de .NET libera la memoria usada por el `SKPaint` objeto, pero en el código de animación, es mejor ser proactiva para liberar memoria de forma más ordenada.

 Una solución mejor en este caso concreto sería crear dos `SKPaint` objetos una vez y guardarlos como campos.

Eso es lo que hace la animación de los **círculos expansibles** . La [`ExpandingCirclesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ExpandingCirclesPage.cs) clase comienza definiendo varios campos, incluido un `SKPaint` objeto:

```csharp
public class ExpandingCirclesPage : ContentPage
{
    const double cycleTime = 1000;       // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float t;
    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke
    };

    public ExpandingCirclesPage()
    {
        Title = "Expanding Circles";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ...
}
```

Este programa utiliza un enfoque diferente para la animación basada en el Xamarin.Forms `Device.StartTimer` método. El `t` campo se anima desde 0 hasta 1 cada `cycleTime` milisegundo:

```csharp
public class ExpandingCirclesPage : ContentPage
{
    ...
    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;
        stopwatch.Start();

        Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
        {
            t = (float)(stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime);
            canvasView.InvalidateSurface();

            if (!pageIsActive)
            {
                stopwatch.Stop();
            }
            return pageIsActive;
        });
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        pageIsActive = false;
    }
    ...
}
```

El `PaintSurface` controlador dibuja cinco círculos concéntricos con radios animados. Si la `baseRadius` variable se calcula como 100, como `t` se anima de 0 a 1, los radios de los cinco círculos aumentan de 0 a 100, de 100 a 200, de 200 a 300, de 300 a 400 y de 400 a 500. Para la mayoría de los círculos `strokeWidth` , es 50, pero para el primer círculo, el `strokeWidth` anima desde 0 hasta 50. Para la mayoría de los círculos, el color es azul, pero para el último círculo, el color se anima de azul a transparente. Observe el cuarto argumento del `SKColor` constructor que especifica la opacidad:

```csharp
public class ExpandingCirclesPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
        float baseRadius = Math.Min(info.Width, info.Height) / 12;

        for (int circle = 0; circle < 5; circle++)
        {
            float radius = baseRadius * (circle + t);

            paint.StrokeWidth = baseRadius / 2 * (circle == 0 ? t : 1);
            paint.Color = new SKColor(0, 0, 255,
                (byte)(255 * (circle == 4 ? (1 - t) : 1)));

            canvas.DrawCircle(center.X, center.Y, radius, paint);
        }
    }
}
```

El resultado es que la imagen tiene el mismo aspecto cuando `t` es igual a 0 que cuando `t` es igual a 1 y los círculos parecen continuar expandiendo siempre:

[![Captura de pantalla triple de la página de círculos en expansión](animation-images/expandingcircles-small.png)](animation-images/expandingcircles-large.png#lightbox "Captura de pantalla triple de la página de círculos en expansión")

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)