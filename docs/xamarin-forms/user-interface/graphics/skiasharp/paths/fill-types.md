---
title: Tipos de relleno del trazado
description: En este artículo se examinan los distintos efectos posibles con los tipos de relleno de ruta de SkiaSharp y se muestra con código de ejemplo.
ms.prod: xamarin
ms.assetid: 57103A7A-49A2-46AE-894C-7C2664682644
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c8c54f3d3815e418d2f71960dc7733711cb40ae2
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139053"
---
# <a name="the-path-fill-types"></a>Tipos de relleno del trazado

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Descubra los distintos efectos posibles con los tipos de relleno de ruta de SkiaSharp_

Dos contornos en un trazado pueden superponerse y las líneas que componen un solo contorno pueden superponerse. Es posible que se rellenen las áreas incluidas, pero es posible que no desee rellenar todas las áreas delimitadas. Este es un ejemplo:

![](fill-types-images/filltypeexample.png "Five-pointed star partially filles")

Tiene un pequeño control sobre esto. El algoritmo de relleno se rige por la [`SKFillType`](xref:SkiaSharp.SKPath.FillType) propiedad de `SKPath` , que se establece en un miembro de la [`SKPathFillType`](xref:SkiaSharp.SKPathFillType) enumeración:

- `Winding`, la opción predeterminada
- `EvenOdd`
- `InverseWinding`
- `InverseEvenOdd`

Los algoritmos de bobinado e par pares determinan si se rellena o no el rellenado de una zona en función de una línea hipotética dibujada desde esa área hasta el infinito. Esa línea cruza una o varias líneas de límite que componen la ruta de acceso. Con el modo de bobinado, si el número de líneas de límite dibujadas en una dirección equilibra el número de líneas dibujadas en la otra dirección, el área no se rellena. En caso contrario, se rellena el área. El algoritmo par impar rellena un área si el número de líneas de límite es impar.

Con muchas rutas de acceso de rutina, el algoritmo de bobinado suele rellenar todas las áreas delimitadas de un trazado. Normalmente, el algoritmo par impar genera resultados más interesantes.

El ejemplo clásico es una estrella de cinco puntas, tal como se muestra en la página de **estrella de cinco puntas** . El archivo [**FivePointedStarPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FivePointedStarPage.xaml) crea una instancia de dos `Picker` vistas para seleccionar el tipo de relleno de trazado y si la ruta de acceso se traza o rellena, o ambos, y en qué orden:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Paths.FivePointedStarPage"
             Title="Five-Pointed Star">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Picker x:Name="fillTypePicker"
                Title="Path Fill Type"
                Grid.Row="0"
                Grid.Column="0"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKPathFillType}">
                    <x:Static Member="skia:SKPathFillType.Winding" />
                    <x:Static Member="skia:SKPathFillType.EvenOdd" />
                    <x:Static Member="skia:SKPathFillType.InverseWinding" />
                    <x:Static Member="skia:SKPathFillType.InverseEvenOdd" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="drawingModePicker"
                Title="Drawing Mode"
                Grid.Row="0"
                Grid.Column="1"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Fill only</x:String>
                    <x:String>Stroke only</x:String>
                    <x:String>Stroke then Fill</x:String>
                    <x:String>Fill then Stroke</x:String>
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                Grid.Row="1"
                                Grid.Column="0"
                                Grid.ColumnSpan="2"
                                PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

El archivo de código subyacente usa ambos `Picker` valores para dibujar una estrella de cinco puntas:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = 0.45f * Math.Min(info.Width, info.Height);

    SKPath path = new SKPath
    {
        FillType = (SKPathFillType)fillTypePicker.SelectedItem
    };
    path.MoveTo(info.Width / 2, info.Height / 2 - radius);

    for (int i = 1; i < 5; i++)
    {
        // angle from vertical
        double angle = i * 4 * Math.PI / 5;
        path.LineTo(center + new SKPoint(radius * (float)Math.Sin(angle),
                                        -radius * (float)Math.Cos(angle)));
    }
    path.Close();

    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 50,
        StrokeJoin = SKStrokeJoin.Round
    };

    SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue
    };

    switch ((string)drawingModePicker.SelectedItem)
    {
        case "Fill only":
            canvas.DrawPath(path, fillPaint);
            break;

        case "Stroke only":
            canvas.DrawPath(path, strokePaint);
            break;

        case "Stroke then Fill":
            canvas.DrawPath(path, strokePaint);
            canvas.DrawPath(path, fillPaint);
            break;

        case "Fill then Stroke":
            canvas.DrawPath(path, fillPaint);
            canvas.DrawPath(path, strokePaint);
            break;
    }
}
```

Normalmente, el tipo de relleno de trazado debe afectar solo a los rellenos y no a los trazos, pero los dos `Inverse` modos afectan tanto a los rellenos como a los trazos. En el caso de los rellenos, los dos `Inverse` tipos rellenan áreas de forma opuesta para que el área fuera de la estrella se rellene. En el caso de los trazos, los dos `Inverse` tipos colorean todo, excepto el trazo. El uso de estos tipos de relleno inversos puede producir algunos efectos impares, como se muestra en la captura de pantalla de iOS:

[![](fill-types-images/fivepointedstar-small.png "Triple screenshot of the Five-Pointed Star page")](fill-types-images/fivepointedstar-large.png#lightbox "Triple screenshot of the Five-Pointed Star page")

En la captura de pantalla de Android se muestran los efectos típicos pares-impares y de bobinado, pero el orden del trazo y del relleno también afecta a los resultados.

El algoritmo de bobinado depende de la dirección en la que se dibujan las líneas. Normalmente, cuando se crea una ruta de acceso, se puede controlar esa dirección a medida que se especifica que las líneas se dibujen de un punto a otro. Sin embargo, la `SKPath` clase también define métodos como `AddRect` y `AddCircle` que dibujan contornos completos. Para controlar cómo se dibujan estos objetos, los métodos incluyen un parámetro de tipo [`SKPathDirection`](xref:SkiaSharp.SKPathDirection) , que tiene dos miembros:

- `Clockwise`
- `CounterClockwise`

Los métodos de `SKPath` que incluyen un `SKPathDirection` parámetro le asignan un valor predeterminado de `Clockwise` .

La página **círculos superpuestos** crea una ruta de acceso con cuatro círculos superpuestos con un tipo de relleno de trazado par-impar:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = Math.Min(info.Width, info.Height) / 4;

    SKPath path = new SKPath
    {
        FillType = SKPathFillType.EvenOdd
    };

    path.AddCircle(center.X - radius / 2, center.Y - radius / 2, radius);
    path.AddCircle(center.X - radius / 2, center.Y + radius / 2, radius);
    path.AddCircle(center.X + radius / 2, center.Y - radius / 2, radius);
    path.AddCircle(center.X + radius / 2, center.Y + radius / 2, radius);

    SKPaint paint = new SKPaint()
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Cyan
    };

    canvas.DrawPath(path, paint);

    paint.Style = SKPaintStyle.Stroke;
    paint.StrokeWidth = 10;
    paint.Color = SKColors.Magenta;

    canvas.DrawPath(path, paint);
}
```

Se trata de una imagen interesante creada con un código mínimo:

[![](fill-types-images/overlappingcircles-small.png "Triple screenshot of the Overlapping Circles page")](fill-types-images/overlappingcircles-large.png#lightbox "Triple screenshot of the Overlapping Circles page")

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
