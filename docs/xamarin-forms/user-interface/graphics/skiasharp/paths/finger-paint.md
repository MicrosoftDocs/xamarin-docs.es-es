---
title: Pintar con los dedos de SkiaSharp
description: En este artículo se explica cómo utilizar los dedos para pintar en el lienzo de SkiaSharp en una aplicación de Xamarin.Forms y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 56929D74-8F2C-44C6-90E6-3FBABCDC0A4B
author: davidbritch
ms.author: dabritch
ms.date: 04/05/2017
ms.openlocfilehash: d5cf0927c64732d6d0a44204db9509fae77f0d1d
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76724088"
---
# <a name="finger-painting-in-skiasharp"></a>Pintar con los dedos de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Use los dedos para pintar en el lienzo._

Un objeto `SKPath` puede actualizarse y mostrarse continuamente. Esta característica permite que se usará para dibujar interactivo, como en un programa para una ruta de acceso.

![](finger-paint-images/fingerpaintsample.png "An exercise in finger painting")

La compatibilidad táctil en Xamarin.Forms no permite el seguimiento individuales dedos en la pantalla, por lo que se ha desarrollado un efecto de seguimiento de interacción de Xamarin.Forms para proporcionar compatibilidad táctil adicionales. Este efecto se describe en el artículo [**invocación de eventos a partir de efectos**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md). Las [**demostraciones de efectos de seguimiento táctil de**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/) programas de ejemplo incluyen dos páginas que usan SkiaSharp, incluido un programa de pintado de dedos.

La solución [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) incluye este evento de seguimiento de toque. El proyecto de biblioteca de .NET Standard incluye la clase `TouchEffect`, la enumeración `TouchActionType`, el delegado `TouchActionEventHandler` y la clase `TouchActionEventArgs`. Cada uno de los proyectos de la plataforma incluye una clase `TouchEffect` para esa plataforma; el proyecto de iOS también contiene una clase `TouchRecognizer`.

La página de **Paint de Finger** en **SkiaSharpFormsDemos** es una implementación simplificada de la pintura de los dedos. No admiten la selección de color o ancho del trazo, no tiene ninguna manera de borrar el lienzo y, por supuesto no se puede guardar el material gráfico.

El archivo [**FingerPaintPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FingerPaintPage.xaml) coloca el `SKCanvasView` en un `Grid` de una sola celda y asocia el `TouchEffect` a esa `Grid`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Paths.FingerPaintPage"
             Title="Finger Paint">

    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</ContentPage>
```

Adjuntar el `TouchEffect` directamente a la `SKCanvasView` no funciona en todas las plataformas.

El archivo de código subyacente [**FingerPaintPage.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FingerPaintPage.xaml.cs) define dos colecciones para almacenar los objetos `SKPath`, así como un objeto `SKPaint` para representar estas rutas de acceso:

```csharp
public partial class FingerPaintPage : ContentPage
{
    Dictionary<long, SKPath> inProgressPaths = new Dictionary<long, SKPath>();
    List<SKPath> completedPaths = new List<SKPath>();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };

    public FingerPaintPage()
    {
        InitializeComponent();
    }
    ...
}
```

Como sugiere el nombre, el Diccionario de `inProgressPaths` almacena las rutas de acceso que se están dibujando actualmente con uno o más dedos. La clave del diccionario es el identificador de contacto que acompaña a los eventos de toque. El campo `completedPaths` es una colección de rutas de acceso que han terminado cuando un dedo estaba dibujando el trazado de la pantalla.

El controlador de `TouchAction` administra estas dos colecciones. Cuando un dedo toca por primera vez la pantalla, se agrega una nueva `SKPath` a `inProgressPaths`. Cuando se coloca ese dedo, puntos adicionales se agregan a la ruta de acceso. Cuando se suelta el dedo, la ruta de acceso se transfiere a la colección de `completedPaths`. Puede pintar con varios dedos simultáneamente. Después de cada cambio en una de las rutas de acceso o recopilaciones, el `SKCanvasView` se invalida:

```csharp
public partial class FingerPaintPage : ContentPage
{
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = new SKPath();
                    path.MoveTo(ConvertToPixel(args.Location));
                    inProgressPaths.Add(args.Id, path);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Moved:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = inProgressPaths[args.Id];
                    path.LineTo(ConvertToPixel(args.Location));
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    completedPaths.Add(inProgressPaths[args.Id]);
                    inProgressPaths.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Cancelled:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    inProgressPaths.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
    SKPoint ConvertToPixel(Point pt)
    {
        return new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                           (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));
    }
}
```

Los puntos que acompaña a los eventos de seguimiento de interacción son las coordenadas de Xamarin.Forms; estos se deben convertir a coordenadas de SkiaSharp, que son píxeles. Este es el propósito del método `ConvertToPixel`.

A continuación, el controlador de `PaintSurface` simplemente representa ambas colecciones de rutas de acceso. Las rutas de acceso anteriormente completados aparecen debajo de las rutas de acceso en curso:

```csharp
public partial class FingerPaintPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKCanvas canvas = args.Surface.Canvas;
        canvas.Clear();

        foreach (SKPath path in completedPaths)
        {
            canvas.DrawPath(path, paint);
        }

        foreach (SKPath path in inProgressPaths.Values)
        {
            canvas.DrawPath(path, paint);
        }
    }
    ...
}
```

Los dibujos dedo sólo están limitados por su talento:

[![](finger-paint-images/fingerpaint-small.png "Triple screenshot of the Finger Paint page")](finger-paint-images/fingerpaint-large.png#lightbox "Triple screenshot of the Finger Paint page")

Ahora ha visto cómo dibujar líneas y curvas mediante las ecuaciones paramétricas de definir. Una sección posterior sobre [**curvas y trazados de SkiaSharp**](../curves/index.md) cubre los distintos tipos de curvas que `SKPath` admite. Pero un requisito previo útil es una exploración de [**transformaciones de SkiaSharp**](../transforms/index.md).

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Demostraciones de efectos de seguimiento táctil (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)
- [Invocar eventos a partir de efectos](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
