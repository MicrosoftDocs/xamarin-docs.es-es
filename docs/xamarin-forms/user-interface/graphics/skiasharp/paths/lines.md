---
title: Líneas y extremos de trazo
description: En este artículo se explica cómo usar SkiaSharp para dibujar líneas con extremos de trazo diferentes en las aplicaciones de Xamarin.Forms y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 724a79e618321f97257718bf56dd1fdd18f73563
ms.sourcegitcommit: 191f1f3b13a14e2afadcb95126c5f653722f126f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/30/2019
ms.locfileid: "75545622"
---
# <a name="lines-and-stroke-caps"></a>Líneas y extremos de trazo

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Obtenga información sobre cómo utilizar SkiaSharp para dibujar líneas con extremos de trazo diferente_

En SkiaSharp, representar una sola línea es muy diferente de la representación de una serie de líneas rectas conectadas. Incluso al dibujar líneas individuales, sin embargo, a menudo resulta necesaria para proporcionar un ancho de trazo determinado a las líneas. A medida que estas líneas se vuelven más amplias, la apariencia de los extremos de las líneas también adquiere importancia. Se llama a la apariencia del final de la línea de la *extremo trazo*:

![](lines-images/strokecapsexample.png "The three stroke caps options")

Para dibujar líneas individuales, `SKCanvas` define una sencilla [ `DrawLine` ](xref:SkiaSharp.SKCanvas.DrawLine(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) método cuyos argumentos indican iniciales y finales de coordenadas de la línea con un `SKPaint` objeto:

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

De forma predeterminada, el [ `StrokeWidth` ](xref:SkiaSharp.SKPaint.StrokeWidth) propiedad de una instancia recién creada `SKPaint` objeto es 0, que tiene el mismo efecto que un valor de 1 en la representación de una línea de un píxel en el grosor. Esto parece muy fino en alta resolución dispositivos como teléfonos, por lo que probablemente desee establecer el `StrokeWidth` en un valor mayor. ¿Pero cuando empiece a dibujar las líneas de un grosor de tamaño ajustable, que provoca otro problema: cómo empieza y finaliza de estas líneas gruesas la se debe representar?

Se llama a la apariencia del final de las líneas y se inicia un *extremo de línea* o, en Skia, un *extremo trazo*. La palabra "cap" en este contexto hace referencia a un tipo de hat &mdash; algo que se encuentra en el extremo de la línea. Establece el [ `StrokeCap` ](xref:SkiaSharp.SKPaint.StrokeCap) propiedad de la `SKPaint` objeto en uno de los siguientes miembros de la [ `SKStrokeCap` ](xref:SkiaSharp.SKStrokeCap) enumeración:

- `Butt` (el valor predeterminado)
- `Square`
- `Round`

Se ilustran mejor con un programa de ejemplo. El **líneas de SkiaSharp y rutas de acceso** sección de la [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) programa comienza con una página titulada **extremos de trazo** basándose en la [ `StrokeCapsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/StrokeCapsPage.cs) clase. Esta página define una `PaintSurface` controlador de eventos que se repite en los tres miembros de la `SKStrokeCap` enumeración, mostrar el nombre del miembro de enumeración y dibujar una línea con ese extremo trazo:

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

Para cada miembro de la `SKStrokeCap` enumeración, el controlador dibuja dos líneas, uno con un grosor de trazo de 50 píxeles y otra línea situado en la parte superior con un grosor de trazo de dos píxeles. Esta segunda línea pretende ilustrar geométrica comienzo y al final de la línea independiente del grosor de la línea y un extremo del trazo:

[![](lines-images/strokecaps-small.png "Triple screenshot of the Stroke Caps page")](lines-images/strokecaps-large.png#lightbox "Triple screenshot of the Stroke Caps page")

Como puede ver, el `Square` y `Round` extremos de trazo amplían la longitud de la línea de forma eficaz por la mitad del ancho del trazo al principio de la línea y de nuevo al final. Esta extensión se vuelve importante cuando es necesario determinar las dimensiones de un objeto graphics representado.

La `SKCanvas` clase incluye también otro método para dibujar varias líneas que resulta bastante peculiar:

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

El `points` parámetro es una matriz de `SKPoint` valores y `mode` es un miembro de la [ `SKPointMode` ](xref:SkiaSharp.SKPointMode) enumeración, que tiene tres miembros:

- `Points` para representar los puntos individuales
- `Lines` para conectarse a cada par de puntos
- `Polygon` para conectar todos los puntos consecutivos

El **varias líneas** página muestra este método. El [ **MultipleLinesPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/MultipleLinesPage.xaml) archivo crean instancias de dos `Picker` vistas que le permiten seleccionar un miembro de la `SKPointMode` enumeración y un miembro de la `SKStrokeCap` enumeración:

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

Tenga en cuenta que las declaraciones de espacio de nombres de SkiaSharp son un poco diferente porque el `SkiaSharp` espacio de nombres es necesario para hacer referencia a los miembros de la `SKPointMode` y `SKStrokeCap` enumeraciones. El `SelectedIndexChanged` controlador para ambos `Picker` vistas simplemente invalida la `SKCanvasView` objeto:

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

Debe comprobar la existencia de este controlador de la `SKCanvasView` objeto porque el controlador de eventos es el primero se le llama cuando el `SelectedIndex` propiedad de la `Picker` se establece en 0 en el archivo XAML, y que se produce antes de la `SKCanvasView` se ha creado una instancia.

El `PaintSurface` controlador obtiene los valores de enumeración de dos desde el `Picker` vistas:

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

Las capturas de pantalla muestran una variedad de `Picker` selecciones:

[![](lines-images/multiplelines-small.png "Triple screenshot of the Multiple Lines page")](lines-images/multiplelines-large.png#lightbox "Triple screenshot of the Multiple Lines page")

El iPhone a la izquierda se muestra cómo el `SKPointMode.Points` hace que el miembro de enumeración `DrawPoints` para representar cada uno de los puntos de la `SKPoint` si el extremo de línea es de matriz como un cuadrado `Butt` o `Square`. Se representan los círculos si el extremo de línea es `Round`.

La captura de pantalla de Android muestra el resultado de la `SKPointMode.Lines`. El método `DrawPoints` dibuja una línea entre cada par de valores de `SKPoint`, utilizando el extremo de línea especificado, en este caso `Round`.

Si en su lugar usa `SKPointMode.Polygon`, se dibuja una línea entre los puntos sucesivos de la matriz, pero si observa muy atentamente, verá que estas líneas no están conectadas. Cada una de estas líneas independientes comienza y termina con el extremo de línea especificado. Si selecciona el `Round` CAP, pueden aparecer las líneas se conecten, pero realmente no estén conectados.

Si las líneas están conectadas o no conectadas son un aspecto fundamental de trabajar con trazados de gráficos.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
