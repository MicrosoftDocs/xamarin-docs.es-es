---
title: Líneas y extremos de trazo
description: En este artículo se explica cómo usar SkiaSharp para dibujar líneas con extremos de trazo diferentes en las aplicaciones de Xamarin.Forms y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 9aaecb8c63ff28111097dce81954f523b4c7731b
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725217"
---
# <a name="lines-and-stroke-caps"></a>Líneas y extremos de trazo

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Aprenda a usar SkiaSharp para dibujar líneas con distintos extremos de trazo_

En SkiaSharp, representar una sola línea es muy diferente de la representación de una serie de líneas rectas conectadas. Incluso al dibujar líneas individuales, sin embargo, a menudo resulta necesaria para proporcionar un ancho de trazo determinado a las líneas. A medida que estas líneas se vuelven más amplias, la apariencia de los extremos de las líneas también adquiere importancia. La apariencia del final de la línea se denomina extremo del *trazo*:

![](lines-images/strokecapsexample.png "The three stroke caps options")

Para dibujar líneas individuales, `SKCanvas` define un método de [`DrawLine`](xref:SkiaSharp.SKCanvas.DrawLine(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) simple cuyos argumentos indican las coordenadas inicial y final de la línea con un objeto `SKPaint`:

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

De forma predeterminada, la propiedad [`StrokeWidth`](xref:SkiaSharp.SKPaint.StrokeWidth) de un objeto de `SKPaint` recién creado es 0, que tiene el mismo efecto que el valor 1 en la representación de una línea de un píxel de grosor. Esto parece muy fino en dispositivos de alta resolución como teléfonos, por lo que probablemente querrá establecer el `StrokeWidth` en un valor mayor. ¿Pero cuando empiece a dibujar las líneas de un grosor de tamaño ajustable, que provoca otro problema: cómo empieza y finaliza de estas líneas gruesas la se debe representar?

La apariencia de los inicios y finales de las líneas se denomina *extremo de línea* o, en Skia, un extremo de *trazo*. La palabra "Cap" en este contexto hace referencia a un tipo de Hat &mdash; algo que se encuentra al final de la línea. Establezca la propiedad [`StrokeCap`](xref:SkiaSharp.SKPaint.StrokeCap) del objeto `SKPaint` en uno de los siguientes miembros de la enumeración [`SKStrokeCap`](xref:SkiaSharp.SKStrokeCap) :

- `Butt` (valor predeterminado)
- `Square`
- `Round`

Se ilustran mejor con un programa de ejemplo. La sección **líneas y rutas** de acceso de SkiaSharp del programa [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) comienza con una página titulada **extremos de trazos** basados en la clase [`StrokeCapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeCapsPage.cs) . En esta página se define un controlador de eventos `PaintSurface` que recorre en bucle los tres miembros de la enumeración `SKStrokeCap`, mostrando el nombre del miembro de la enumeración y dibujando una línea con el extremo del trazo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 75,
        TextAlign = SKTextAlign.Center
    };

    SKPaint thickLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 50
    };

    SKPaint thinLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2
    };

    float xText = info.Width / 2;
    float xLine1 = 100;
    float xLine2 = info.Width - xLine1;
    float y = textPaint.FontSpacing;

    foreach (SKStrokeCap strokeCap in Enum.GetValues(typeof(SKStrokeCap)))
    {
        // Display text
        canvas.DrawText(strokeCap.ToString(), xText, y, textPaint);
        y += textPaint.FontSpacing;

        // Display thick line
        thickLinePaint.StrokeCap = strokeCap;
        canvas.DrawLine(xLine1, y, xLine2, y, thickLinePaint);

        // Display thin line
        canvas.DrawLine(xLine1, y, xLine2, y, thinLinePaint);
        y += 2 * textPaint.FontSpacing;
    }
}
```

Para cada miembro de la enumeración `SKStrokeCap`, el controlador dibuja dos líneas, una con un grosor de trazo de 50 píxeles y otra línea situada en la parte superior con un grosor de trazo de dos píxeles. Esta segunda línea pretende ilustrar geométrica comienzo y al final de la línea independiente del grosor de la línea y un extremo del trazo:

[![](lines-images/strokecaps-small.png "Triple screenshot of the Stroke Caps page")](lines-images/strokecaps-large.png#lightbox "Triple screenshot of the Stroke Caps page")

Como puede ver, los topes de `Square` y `Round` de forma eficaz amplían la longitud de la línea a la mitad del ancho del trazo al principio de la línea y de nuevo al final. Esta extensión se vuelve importante cuando es necesario determinar las dimensiones de un objeto graphics representado.

La clase `SKCanvas` también incluye otro método para dibujar varias líneas que es algo peculiar:

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

El parámetro `points` es una matriz de valores `SKPoint` y `mode` es un miembro de la enumeración [`SKPointMode`](xref:SkiaSharp.SKPointMode) , que tiene tres miembros:

- `Points` para representar los puntos individuales
- `Lines` para conectar cada par de puntos
- `Polygon` para conectar todos los puntos consecutivos

En la página de **varias líneas** se muestra este método. El archivo [**MultipleLinesPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/MultipleLinesPage.xaml) crea una instancia de dos vistas `Picker` que permiten seleccionar un miembro de la enumeración `SKPointMode` y un miembro de la enumeración `SKStrokeCap`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Paths.MultipleLinesPage"
             Title="Multiple Lines">
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker x:Name="pointModePicker"
                Title="Point Mode"
                Grid.Row="0"
                Grid.Column="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKPointMode}">
                    <x:Static Member="skia:SKPointMode.Points" />
                    <x:Static Member="skia:SKPointMode.Lines" />
                    <x:Static Member="skia:SKPointMode.Polygon" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="strokeCapPicker"
                Title="Stroke Cap"
                Grid.Row="0"
                Grid.Column="1"
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

        <skiaforms:SKCanvasView x:Name="canvasView"
                                PaintSurface="OnCanvasViewPaintSurface"
                                Grid.Row="1"
                                Grid.Column="0"
                                Grid.ColumnSpan="2" />
    </Grid>
</ContentPage>
```

Tenga en cuenta que las declaraciones de espacio de nombres SkiaSharp son un poco diferentes porque el espacio de nombres `SkiaSharp` es necesario para hacer referencia a los miembros de las enumeraciones `SKPointMode` y `SKStrokeCap`. El controlador de `SelectedIndexChanged` para las vistas de `Picker` simplemente invalida el objeto `SKCanvasView`:

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

Este controlador necesita comprobar la existencia del objeto `SKCanvasView` porque primero se llama al controlador de eventos cuando la propiedad `SelectedIndex` de la `Picker` está establecida en 0 en el archivo XAML y eso se produce antes de que se haya creado una instancia del `SKCanvasView`.

El controlador de `PaintSurface` obtiene los dos valores de enumeración de las vistas de `Picker`:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Create an array of points scattered through the page
    SKPoint[] points = new SKPoint[10];

    for (int i = 0; i < 2; i++)
    {
        float x = (0.1f + 0.8f * i) * info.Width;

        for (int j = 0; j < 5; j++)
        {
            float y = (0.1f + 0.2f * j) * info.Height;
            points[2 * j + i] = new SKPoint(x, y);
        }
    }

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.DarkOrchid,
        StrokeWidth = 50,
        StrokeCap = (SKStrokeCap)strokeCapPicker.SelectedItem
    };

    // Render the points by calling DrawPoints
    SKPointMode pointMode = (SKPointMode)pointModePicker.SelectedItem;
    canvas.DrawPoints(pointMode, points, paint);
}
```

Las capturas de pantallas muestran diversas selecciones de `Picker`:

[![](lines-images/multiplelines-small.png "Triple screenshot of the Multiple Lines page")](lines-images/multiplelines-large.png#lightbox "Triple screenshot of the Multiple Lines page")

El iPhone de la izquierda muestra cómo el miembro de enumeración `SKPointMode.Points` hace que `DrawPoints` represente cada uno de los puntos de la matriz de `SKPoint` como un cuadrado si el extremo de línea está `Butt` o `Square`. Los círculos se representan si el extremo de línea está `Round`.

La captura de pantalla de Android muestra el resultado de la `SKPointMode.Lines`. El método `DrawPoints` dibuja una línea entre cada par de valores de `SKPoint`, utilizando el extremo de línea especificado, en este caso `Round`.

Si en su lugar usa `SKPointMode.Polygon`, se dibuja una línea entre los puntos sucesivos de la matriz, pero si observa muy atentamente, verá que estas líneas no están conectadas. Cada una de estas líneas independientes comienza y termina con el extremo de línea especificado. Si selecciona las mayúsculas `Round`, es posible que las líneas parezcan estar conectadas, pero realmente no están conectadas.

Si las líneas están conectadas o no conectadas son un aspecto fundamental de trabajar con trazados de gráficos.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
