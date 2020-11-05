---
title: Líneas y extremos de trazo
description: En este artículo se explica cómo usar SkiaSharp para dibujar líneas con distintos límites de trazo en Xamarin.Forms aplicaciones y cómo hacerlo con código de ejemplo.
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7e27db7cd05c1997d3ac889b36aca5e3716d2d08
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93367600"
---
# <a name="lines-and-stroke-caps"></a>Líneas y extremos de trazo

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Aprenda a usar SkiaSharp para dibujar líneas con distintos extremos de trazo_

En SkiaSharp, la representación de una sola línea es muy diferente de la representación de una serie de líneas rectas conectadas. Sin embargo, incluso cuando se dibujan líneas individuales, a menudo es necesario asignar a las líneas un ancho de trazo determinado. A medida que estas líneas se vuelven más anchas, la apariencia de los extremos de las líneas también es importante. La apariencia del final de la línea se denomina extremo del *trazo* :

![Las tres opciones de extremos de trazo](lines-images/strokecapsexample.png)

Para dibujar líneas individuales, `SKCanvas` define un [`DrawLine`](xref:SkiaSharp.SKCanvas.DrawLine(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) método simple cuyos argumentos indican las coordenadas inicial y final de la línea con un `SKPaint` objeto:

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

De forma predeterminada, la [`StrokeWidth`](xref:SkiaSharp.SKPaint.StrokeWidth) propiedad de un objeto recién creado `SKPaint` es 0, que tiene el mismo efecto que el valor 1 en la representación de una línea de un píxel de grosor. Esto parece muy fino en dispositivos de alta resolución como, por lo que es probable que desee establecer `StrokeWidth` en un valor mayor. Pero una vez que empiece a dibujar líneas de un grosor ajustable, lo que provoca otro problema: ¿cómo deben representarse los inicios y finales de estas líneas gruesas?

La apariencia de los inicios y finales de las líneas se denomina *extremo de línea* o, en Skia, un extremo de *trazo*. La palabra "Cap" en este contexto hace referencia a un tipo de sombrero &mdash; que se encuentra al final de la línea. La [`StrokeCap`](xref:SkiaSharp.SKPaint.StrokeCap) propiedad del objeto se establece `SKPaint` en uno de los siguientes miembros de la [`SKStrokeCap`](xref:SkiaSharp.SKStrokeCap) enumeración:

- `Butt` (valor predeterminado)
- `Square`
- `Round`

Se ilustran mejor con un programa de ejemplo. La sección **líneas y rutas** de acceso de SkiaSharp del programa [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) comienza con una página titulada **extremos de trazos** basados en la [`StrokeCapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeCapsPage.cs) clase. En esta página se define un `PaintSurface` controlador de eventos que recorre en bucle los tres miembros de la `SKStrokeCap` enumeración, mostrando el nombre del miembro de la enumeración y dibujando una línea con el extremo del trazo:

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

Para cada miembro de la `SKStrokeCap` enumeración, el controlador dibuja dos líneas, una con un grosor de trazo de 50 píxeles y otra línea situada en la parte superior con un grosor de trazo de dos píxeles. Esta segunda línea pretende ilustrar el inicio y el final geométrico de la línea, independientemente del grosor de la línea y un extremo del trazo:

[![Captura de pantalla triple de la página de extremos de trazo](lines-images/strokecaps-small.png)](lines-images/strokecaps-large.png#lightbox "Captura de pantalla triple de la página de extremos de trazo")

Como puede ver, los `Square` `Round` topes de trazo y extienden eficazmente la longitud de la línea a la mitad del ancho del trazo al principio de la línea y de nuevo al final. Esta extensión es importante cuando es necesario determinar las dimensiones de un objeto gráfico representado.

La `SKCanvas` clase también incluye otro método para dibujar varias líneas que es algo peculiar:

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

El `points` parámetro es una matriz de `SKPoint` valores y `mode` es un miembro de la [`SKPointMode`](xref:SkiaSharp.SKPointMode) enumeración, que tiene tres miembros:

- `Points` para representar los puntos individuales
- `Lines` para conectar cada par de puntos
- `Polygon` para conectar todos los puntos consecutivos

En la página de **varias líneas** se muestra este método. El archivo [**MultipleLinesPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/MultipleLinesPage.xaml) crea una instancia de dos `Picker` vistas que permiten seleccionar un miembro de la `SKPointMode` enumeración y un miembro de la `SKStrokeCap` enumeración:

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

Observe que las declaraciones de espacio de nombres SkiaSharp son ligeramente diferentes porque el `SkiaSharp` espacio de nombres es necesario para hacer referencia a los miembros de las `SKPointMode` `SKStrokeCap` enumeraciones y. El `SelectedIndexChanged` controlador de ambas `Picker` vistas simplemente invalida el `SKCanvasView` objeto:

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

Este controlador debe comprobar la existencia del `SKCanvasView` objeto porque primero se llama al controlador de eventos cuando la `SelectedIndex` propiedad de `Picker` se establece en 0 en el archivo XAML y eso se produce antes de que se haya `SKCanvasView` creado una instancia de.

El `PaintSurface` controlador obtiene los dos valores de enumeración de las `Picker` vistas:

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

Las capturas de pantallas muestran una variedad de `Picker` selecciones:

[![Captura de pantalla triple de la página de varias líneas](lines-images/multiplelines-small.png)](lines-images/multiplelines-large.png#lightbox "Captura de pantalla triple de la página de varias líneas")

El iPhone de la izquierda muestra cómo el `SKPointMode.Points` miembro de enumeración hace que `DrawPoints` represente cada uno de los puntos de la `SKPoint` matriz como un cuadrado si el extremo de línea es `Butt` o `Square` . Los círculos se representan si el extremo de línea es `Round` .

La captura de pantalla de Android muestra el resultado de `SKPointMode.Lines` . El `DrawPoints` método dibuja una línea entre cada par de `SKPoint` valores, utilizando el extremo de línea especificado, en este caso `Round` .

Cuando se usa en su lugar `SKPointMode.Polygon` , se dibuja una línea entre los puntos sucesivos de la matriz, pero si se observa muy atentamente, verá que estas líneas no están conectadas. Cada una de estas líneas independientes comienza y termina con el límite de línea especificado. Si selecciona los `Round` Cap, puede parecer que las líneas están conectadas, pero realmente no están conectadas.

El hecho de que las líneas estén conectadas o no conectadas es un aspecto fundamental del trabajo con las rutas de acceso de los gráficos.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)