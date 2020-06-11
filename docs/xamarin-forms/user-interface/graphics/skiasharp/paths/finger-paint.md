---
title: "Paint Painting in SkiaSharp" Description: "en este artículo se explica cómo usar los dedos para pintar en el lienzo de SkiaSharp en una Xamarin.Forms aplicación y se muestra el código de ejemplo".
MS. Prod: Xamarin ms. Technology: Xamarin-skiasharp ms. AssetID: 56929D74-8F2C-44C6-90E6-3FBABCDC0A4B Author: davidbritch ms. Author: dabritch ms. Date: 04/05/2017 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="finger-painting-in-skiasharp"></a>Pintado con el dedo en SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Use los dedos para pintar en el lienzo._

Un `SKPath` objeto se puede actualizar y mostrar continuamente. Esta característica permite usar una ruta de acceso para dibujar interactivamente, como en un programa de pintado de dedos.

![](finger-paint-images/fingerpaintsample.png "An exercise in finger painting")

La compatibilidad táctil en no Xamarin.Forms permite el seguimiento de dedos individuales en la pantalla, por lo que se ha Xamarin.Forms desarrollado un efecto de seguimiento táctil para proporcionar soporte técnico táctil adicional. Este efecto se describe en el artículo [**invocación de eventos a partir de efectos**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md). Las [**demostraciones de efectos de seguimiento táctil de**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/) programas de ejemplo incluyen dos páginas que usan SkiaSharp, incluido un programa de pintado de dedos.

La solución [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) incluye este evento de seguimiento de toque. El proyecto de biblioteca de .NET Standard incluye la `TouchEffect` clase, la `TouchActionType` enumeración, el `TouchActionEventHandler` delegado y la `TouchActionEventArgs` clase. Cada uno de los proyectos de la plataforma incluye una `TouchEffect` clase para esa plataforma; el proyecto de iOS también contiene una `TouchRecognizer` clase.

La página de **Paint de Finger** en **SkiaSharpFormsDemos** es una implementación simplificada de la pintura de los dedos. No permite seleccionar el color o el ancho del trazo, no tiene forma de borrar el lienzo y, por supuesto, no se puede guardar la ilustración.

El archivo [**FingerPaintPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FingerPaintPage.xaml) coloca `SKCanvasView` en una sola celda `Grid` y asocia el `TouchEffect` a ese `Grid` :

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

Adjuntar `TouchEffect` directamente a `SKCanvasView` no funciona en todas las plataformas.

El archivo de código subyacente [**FingerPaintPage.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FingerPaintPage.xaml.cs) define dos colecciones para almacenar los `SKPath` objetos, así como un `SKPaint` objeto para representar estas rutas de acceso:

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

Como sugiere el nombre, el `inProgressPaths` Diccionario almacena las rutas de acceso que se están dibujando actualmente con uno o más dedos. La clave del diccionario es el ID. de toque que acompaña a los eventos de toque. El `completedPaths` campo es una colección de rutas de acceso que se finalizaron cuando un dedo estaba dibujando el trazado de la pantalla.

El `TouchAction` controlador administra estas dos colecciones. Cuando un dedo toca primero la pantalla, `SKPath` se agrega un nuevo a `inProgressPaths` . A medida que se mueve el dedo, se agregan puntos adicionales a la ruta de acceso. Cuando se suelta el dedo, la ruta de acceso se transfiere a la `completedPaths` colección. Puede pintar con varios dedos simultáneamente. Después de cada cambio en una de las rutas de acceso o recopilaciones, `SKCanvasView` se invalida:

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

Los puntos que acompañan a los eventos de seguimiento táctil son Xamarin.Forms coordenadas; se deben convertir en coordenadas SkiaSharp, que son píxeles. Este es el propósito del `ConvertToPixel` método.

`PaintSurface`A continuación, el controlador simplemente representa ambas colecciones de rutas de acceso. Las rutas de acceso completadas anteriormente aparecen debajo de las rutas de acceso en curso:

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

Las pinturas de dedos solo están limitadas por su talento:

[![](finger-paint-images/fingerpaint-small.png "Triple screenshot of the Finger Paint page")](finger-paint-images/fingerpaint-large.png#lightbox "Triple screenshot of the Finger Paint page")

Ahora ha visto cómo dibujar líneas y cómo definir curvas mediante ecuaciones paramétricas. Una sección posterior sobre [**curvas y trazados de SkiaSharp**](../curves/index.md) cubre los distintos tipos de curvas que `SKPath` admite. Pero un requisito previo útil es una exploración de [**transformaciones de SkiaSharp**](../transforms/index.md).

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Demostraciones de efectos de seguimiento táctil (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)
- [Invocación de eventos desde efectos](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
