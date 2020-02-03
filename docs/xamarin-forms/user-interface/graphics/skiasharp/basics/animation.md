---
title: Animación básica de SkiaSharp
description: En este artículo se explica cómo animar los gráficos de SkiaSharp en Xamarin.Forms y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 31C96FD6-07E4-4473-A551-24753A5118C3
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 80de16a0cf9b601ac3795085b638b9d62812f4d9
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725555"
---
# <a name="basic-animation-in-skiasharp"></a>Animación básica de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Descubra cómo animar los gráficos de SkiaSharp_

Puede animar los gráficos de SkiaSharp en Xamarin. Forms haciendo que el método de `PaintSurface` se llame periódicamente, cada vez que dibuje los gráficos ligeramente de manera diferente. Aquí es una animación que se muestra más adelante en este artículo con círculos concéntricos que aparentemente se expanden desde el centro:

![](animation-images/animationexample.png "Several concentric circles seemingly expanding from the center")

La página de la **elipse pulsating** del programa [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) anima los dos ejes de una elipse para que parezca ser pulsating e incluso puede controlar la velocidad de esta Pulsation. El archivo [**PulsatingEllipsePage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml) crea una instancia de un `Slider` de Xamarin. Forms y un `Label` para mostrar el valor actual del control deslizante. Se trata de una manera común de integrar un `SKCanvasView` con otras vistas de Xamarin. Forms:

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

El archivo de código subyacente crea una instancia de un objeto `Stopwatch` para servir como un reloj de precisión alta. La invalidación `OnAppearing` establece el campo `pageIsActive` en `true` y llama a un método denominado `AnimationLoop`. La invalidación `OnDisappearing` establece que `pageIsActive` campo en `false`:

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

El método `AnimationLoop` inicia el `Stopwatch` y, a continuación, recorre el bucle mientras `pageIsActive` está `true`. Es esencialmente un "bucle infinito" mientras la página está activa, pero no hace que el programa se bloquee porque el bucle concluye con una llamada a `Task.Delay` con el operador `await`, que permite que otras partes de la función del programa. El argumento para `Task.Delay` provoca que se complete después de 1/30 segundos. Esto define la velocidad de fotogramas de la animación.

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

El bucle `while` comienza obteniendo una hora de ciclo del `Slider`. Se trata de un tiempo en segundos, por ejemplo, 5. La segunda instrucción calcula un valor de `t` para el *tiempo*. En el caso de una `cycleTime` de 5, `t` aumenta de 0 a 1 cada 5 segundos. El argumento de la función `Math.Sin` de la segunda instrucción va de 0 a 2π cada 5 segundos. La función `Math.Sin` devuelve un valor comprendido entre 0 y 1 hasta 0 y, a continuación, &ndash;1 y 0 cada 5 segundos, pero con valores que cambian más lentamente cuando el valor está cerca de 1 o – 1. Por lo que los valores siempre son positivos y, a continuación, se divide por 2, por lo que los valores van de ½ a 1 por ½ en 0 por ½, pero más lento cuando el valor es de aproximadamente 1 y 0, se agrega el valor 1. Esto se almacena en el campo `scale` y se invalida el `SKCanvasView`.

El método `PaintSurface` usa este valor `scale` para calcular los dos ejes de la elipse:

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

El método calcula un radio máximo en función del tamaño del área de visualización y un radio mínimo según el radio máximo. El valor `scale` se anima entre 0 y 1 y de nuevo a 0, por lo que el método utiliza ese para calcular una `xRadius` y `yRadius` que va entre `minRadius` y `maxRadius`. Estos valores se utilizan para dibujar y rellenar una elipse:

[![](animation-images/pulsatingellipse-small.png "Triple screenshot of the Pulsating Ellipse page")](animation-images/pulsatingellipse-large.png#lightbox "Triple screenshot of the Pulsating Ellipse page")

Observe que el objeto de `SKPaint` se crea en un bloque de `using`. Al igual que muchas clases SkiaSharp `SKPaint` deriva de `SKObject`, que deriva de `SKNativeObject`, que implementa la interfaz de [`IDisposable`](xref:System.IDisposable) . `SKPaint` invalida el método `Dispose` para liberar recursos no administrados.

 La colocación de `SKPaint` en un bloque de `using` garantiza que se llame a `Dispose` al final del bloque para liberar estos recursos no administrados. Esto sucede cuando el recolector de elementos no utilizados de .NET libera la memoria usada por el objeto `SKPaint`, pero, en el código de animación, es mejor ser proactiva para liberar memoria de forma más ordenada.

 Una solución mejor en este caso concreto sería crear dos objetos `SKPaint` una vez y guardarlos como campos.

Eso es lo que hace la animación de los **círculos expansibles** . La clase [`ExpandingCirclesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ExpandingCirclesPage.cs) comienza definiendo varios campos, incluido un objeto `SKPaint`:

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

Este programa usa un enfoque diferente a la animación basado en el método de `Device.StartTimer` de Xamarin. Forms. El campo `t` se anima de 0 a 1 cada `cycleTime` milisegundos:

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

El controlador de `PaintSurface` dibuja cinco círculos concéntricos con radios animados. Si la variable de `baseRadius` se calcula como 100, como `t` se anima de 0 a 1, los radios de los cinco círculos aumentan de 0 a 100, 100 a 200, 200 a 300, 300 a 400 y 400 a 500. Para la mayoría de los círculos, el `strokeWidth` es 50, pero para el primer círculo, el `strokeWidth` anima desde 0 hasta 50. Para la mayoría de los círculos, el color es azul, pero para el círculo de la última, se anima el color de azul en transparente. Observe el cuarto argumento del constructor `SKColor` que especifica la opacidad:

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

El resultado es que la imagen tiene el mismo aspecto cuando `t` es igual a 0 como cuando `t` es igual a 1 y los círculos parecen continuar expandiendo siempre:

[![](animation-images/expandingcircles-small.png "Triple screenshot of the Expanding Circles page")](animation-images/expandingcircles-large.png#lightbox "Triple screenshot of the Expanding Circles page")

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
