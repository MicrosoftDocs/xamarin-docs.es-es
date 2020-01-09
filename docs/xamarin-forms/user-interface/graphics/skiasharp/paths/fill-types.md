---
title: Tipos de relleno del trazado
description: En este artículo examina los efectos de diferentes posibles con tipos de relleno de ruta de acceso de SkiaSharp y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.assetid: 57103A7A-49A2-46AE-894C-7C2664682644
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 16d576c599dbf5815b19aec4a2e8390f7ed0e601
ms.sourcegitcommit: 191f1f3b13a14e2afadcb95126c5f653722f126f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/30/2019
ms.locfileid: "75545574"
---
# <a name="the-path-fill-types"></a>Tipos de relleno del trazado

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Descubra los distintos efectos posibles con tipos de relleno de ruta de acceso de SkiaSharp_

Se pueden superponer dos perfiles en una ruta de acceso y las líneas que componen un contorno solo se pueden superponer. Posiblemente se puede rellenar cualquier área delimitada, pero es posible que no desea rellenar todas las áreas cerradas. Por ejemplo:

![](fill-types-images/filltypeexample.png "Five-pointed star partially filles")

Tiene un pequeño control sobre esto. El algoritmo de relleno se rige por el [ `SKFillType` ](xref:SkiaSharp.SKPath.FillType) propiedad de `SKPath`, que establece en un miembro de la [ `SKPathFillType` ](xref:SkiaSharp.SKPathFillType) enumeración:

- `Winding`, el valor predeterminado
- `EvenOdd`
- `InverseWinding`
- `InverseEvenOdd`

Los algoritmos de devanado y par-impar determinan si cualquier área delimitada se rellenan o no ha rellenado en función de una línea hipotética dibujada desde esa área hasta el infinito. Esa línea cruza una o varias líneas de límites que componen la ruta de acceso. Con el modo de generación, si el número de líneas límites en el saldo de una dirección horizontalmente el número de líneas en la otra dirección y, después, en el área no está rellena. En caso contrario, se rellena el área. El algoritmo par-impar rellena un área, si el número de líneas del límite es impar.

Con muchas rutas rutinarias, el algoritmo devanado a menudo rellena todas las áreas cerradas de una ruta de acceso. El algoritmo par-impar generalmente produce resultados más interesantes.

El ejemplo clásico es una estrella de cinco puntas, como se muestra en el **Five-Pointed estrella** página. El [ **FivePointedStarPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/FivePointedStarPage.xaml) archivo crean instancias de dos `Picker` vistas para seleccionar la ruta de acceso de rellenar el tipo y si se trazan o rellena la ruta de acceso o ambos y en qué orden:

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

Normalmente, el tipo de relleno de la ruta de acceso debe afectar a sólo rellenos y no los trazos, pero los dos `Inverse` afectan a los modos de trazos y rellenos. Para los rellenos, las dos `Inverse` tipos rellenar áreas oppositely para que se rellena el área situada fuera de la estrella. Para los dos trazos `Inverse` tipos todo excepto el trazo de color. Uso de estos tipos de relleno inversa puede producir algunos efectos impares, como se muestra en la captura de pantalla de iOS:

[![](fill-types-images/fivepointedstar-small.png "Triple screenshot of the Five-Pointed Star page")](fill-types-images/fivepointedstar-large.png#lightbox "Triple screenshot of the Five-Pointed Star page")

En la captura de pantalla de Android se muestran los efectos típicos pares-impares y de bobinado, pero el orden del trazo y del relleno también afecta a los resultados.

El algoritmo devanado es dependiente de la dirección que se dibujan líneas. Normalmente, al crear una ruta de acceso, puede controlar esa dirección que especifique que se dibujan líneas de un punto a otro. Sin embargo, el `SKPath` clase también define los métodos como `AddRect` y `AddCircle` que dibujar contornos todos. Para controlar cómo se dibujan estos objetos, los métodos incluyen un parámetro de tipo [ `SKPathDirection` ](xref:SkiaSharp.SKPathDirection), que tiene dos miembros:

- `Clockwise`
- `CounterClockwise`

Los métodos de `SKPath` que incluyen un `SKPathDirection` asignarle un valor predeterminado del parámetro `Clockwise`.

El **círculos superpuestos** página crea una ruta de acceso con cuatro círculos superpuestos con un tipo de relleno par-impar ruta de acceso:

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

Es una interesante imagen creada con un mínimo de código:

[![](fill-types-images/overlappingcircles-small.png "Triple screenshot of the Overlapping Circles page")](fill-types-images/overlappingcircles-large.png#lightbox "Triple screenshot of the Overlapping Circles page")

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
