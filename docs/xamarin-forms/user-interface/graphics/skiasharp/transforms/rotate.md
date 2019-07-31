---
title: Transformación de rotación
description: En este artículo explora los efectos y animaciones posibles con la transformación de giro de SkiaSharp y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: CBB3CD72-4377-4EA3-A768-0C4228229FC2
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2017
ms.openlocfilehash: 1ec5c5fb1a81873d88a59eefba7652a86fc1ba4e
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657213"
---
# <a name="the-rotate-transform"></a>Transformación de rotación

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explore los efectos y animaciones posibles con la transformación de giro de SkiaSharp_

Con la transformación de giro, objetos de gráficos de SkiaSharp liberarse de la restricción de alineación con los ejes horizontales y vertical:

![](rotate-images/rotateexample.png "Texto girado en torno a un centro")

Para girar un objeto gráfico alrededor del punto (0, 0), SkiaSharp admite tanto una [ `RotateDegrees` ](xref:SkiaSharp.SKCanvas.RotateDegrees(System.Single)) método y un [ `RotateRadians` ](xref:SkiaSharp.SKCanvas.RotateRadians(System.Single)) método:

```csharp
public void RotateDegrees (Single degrees)

public Void RotateRadians (Single radians)
```

Un círculo de 360 grados es igual a twoπ radianes, por lo que es fácil realizar la conversión entre las dos unidades. Use lo que sea conveniente. Todas las funciones trigonométricas en .NET [ `Math` ](xref:System.Math) clase utilizar unidades de radianes.

Es hacia la derecha para aumentar los ángulos de rotación. (Aunque la rotación en el sistema de coordenadas cartesiano es hacia la izquierda por convención, giro hacia la derecha es coherente con las coordenadas Y aumentar deja de funcionar como SkiaSharp). Negativo de ángulos y ángulos mayores que se permiten 360 grados.

Las fórmulas de transformación de giro son más complejas que las de traslación y escala. Con un ángulo de α, las fórmulas de transformación son:

x' = x•cos(α) – y•sin(α)   

y' = x•sin(α) + y•cos(α)

El **girar básica** página muestra la `RotateDegrees` método. El [ **BasicRotate.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicRotatePage.xaml.cs) archivo muestra un texto con su línea base que se centra en la página y gira según un `Slider` con un intervalo de -360 y 360. Aquí es la parte relevante de la `PaintSurface` controlador:

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Blue,
    TextAlign = SKTextAlign.Center,
    TextSize = 100
})
{
    canvas.RotateDegrees((float)rotateSlider.Value);
    canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
}
```

Porque la rotación se centra en la esquina superior izquierda del lienzo, para la mayoría de ángulos en este programa, el texto se gira fuera de la pantalla:

[![](rotate-images/basicrotate-small.png "Captura de pantalla de la página básica girar triple")](rotate-images/basicrotate-large.png#lightbox "Triple captura de pantalla de la página girar básica")

Muy a menudo querrá algo se centra en torno a un punto de pivote especificado con estas versiones de girar el [ `RotateDegrees` ](xref:SkiaSharp.SKCanvas.RotateDegrees(System.Single,System.Single,System.Single)) y [ `RotateRadians` ](xref:SkiaSharp.SKCanvas.RotateRadians(System.Single,System.Single,System.Single)) métodos:

```csharp
public void RotateDegrees (Single degrees, Single px, Single py)

public void RotateRadians (Single radians, Single px, Single py)
```

El **centrado girar** página es exactamente igual que el **girar básica** , salvo que la versión expandida de la `RotateDegrees` se usa para establecer el centro de rotación en el mismo punto en que se utiliza para colocar el texto:

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Blue,
    TextAlign = SKTextAlign.Center,
    TextSize = 100
})
{
    canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
    canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
}
```

Ahora el texto gira alrededor del punto que se usa para colocar el texto, que es el centro horizontal de la línea base del texto:

[![](rotate-images/centeredrotate-small.png "Captura de pantalla de la página centrado girar triple")](rotate-images/centeredrotate-large.png#lightbox "Triple captura de pantalla de la página centrado girar")

Igual que con la versión de centrado la `Scale` método, la versión de centrado el `RotateDegrees` llamada es un acceso directo. Este es el método:

```csharp
RotateDegrees (degrees, px, py);
```

Que es equivalente a la siguiente llamada:

```csharp
canvas.Translate(px, py);
canvas.RotateDegrees(degrees);
canvas.Translate(-px, -py);
```

Descubrirá que a veces puede combinar `Translate` llama con `Rotate` llamadas. Por ejemplo, estos son los `RotateDegrees` y `DrawText` llama el **centrado girar** página;

```csharp
canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

El `RotateDegrees` llamada es equivalente a dos `Translate` llamadas y no centrado `RotateDegrees`:

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

El `DrawText` llamada para mostrar texto en una ubicación concreta es equivalente a un `Translate` llamar para esa ubicación seguida `DrawText` en el punto (0, 0):

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.DrawText(Title, 0, 0, textPaint);
```

Los dos consecutivos `Translate` llamadas cancelan entre sí:

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.DrawText(Title, 0, 0, textPaint);
```

Conceptualmente, las dos transformaciones se aplican en el orden opuesto a cómo aparecen estas en el código. El `DrawText` llamada muestra el texto en la esquina superior izquierda del lienzo. El `RotateDegrees` llamada gira ese texto en relación con la esquina superior izquierda. El `Translate` llamada mueve el texto en el centro del lienzo.

Normalmente, hay varias maneras para combinar el giro y traslación. El **texto girado** página crea la vista siguiente:

[![](rotate-images/rotatedtext-small.png "Captura de pantalla de la página de texto girado triple")](rotate-images/rotatedtext-large.png#lightbox "Triple captura de pantalla de la página de texto girado")

Este es el `PaintSurface` controlador de la [ `RotatedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/RotatedTextPage.cs) clase:

```csharp
static readonly string text = "    ROTATE";
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 72
    })
    {
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);
        float yText = yCenter - textBounds.Height / 2 - textBounds.Top;

        for (int degrees = 0; degrees < 360; degrees += 30)
        {
            canvas.Save();
            canvas.RotateDegrees(degrees, xCenter, yCenter);
            canvas.DrawText(text, xCenter, yText, textPaint);
            canvas.Restore();
        }
    }
}

```

El `xCenter` y `yCenter` valores indican que el centro del lienzo. El `yText` valor es un poco de desplazamiento de la. Este valor es la coordenada Y es necesaria colocar el texto, por lo que realmente verticalmente se centre en la página. El `for` bucle, a continuación, Establece una rotación basada en el centro del lienzo. La rotación es en incrementos de 30 grados. El texto se dibuja utilizando el `yText` valor. El número de espacios en blanco delante de la palabra "gira" en el `text` valor determinó empíricamente para realizar la conexión entre estas cadenas de 12 texto parece ser un dodecagon.

Es una manera de simplificar este código se va a incrementar el ángulo de giro 30 grados cada vez a través del bucle después de la `DrawText` llamar. Esto elimina la necesidad de las llamadas a `Save` y `Restore`. Tenga en cuenta que el `degrees` ya no se usa la variable dentro del cuerpo de la `for` bloque:

```csharp
for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, xCenter, yText, textPaint);
    canvas.RotateDegrees(30, xCenter, yCenter);
}

```

También es posible usar la forma sencilla de `RotateDegrees` anteponiendo el bucle con una llamada a `Translate` para mover todo al centro del lienzo:

```csharp
float yText = -textBounds.Height / 2 - textBounds.Top;

canvas.Translate(xCenter, yCenter);

for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, 0, yText, textPaint);
    canvas.RotateDegrees(30);
}
```

Modificado `yText` cálculo ya no incorpora `yCenter`. Ahora el `DrawText` llamada centra el texto verticalmente en la parte superior del lienzo.

Dado que las transformaciones se aplican conceptualmente opuesta a cómo aparecen estas en código, suele ser posible transformaciones para empezar más globales, seguidas por más transformaciones locales. Esto suele ser la manera más fácil de combinar el giro y traslación.

Por ejemplo, suponga que desea dibujar un objeto gráfico que gira en torno a su centro como un planeta girando sobre su eje. Pero también desea que este objeto para giran en torno al centro de la pantalla como un planeta girar alrededor del sol.

Para ello, puede colocar el objeto en la esquina superior izquierda del lienzo y, a continuación, utilizando una animación para hacerla girar en torno a esa esquina. A continuación, convertir el objeto horizontalmente como un radio orbital. Ahora se aplican una segunda rotación animada, también en torno al origen. Esto hace que el objeto giran en torno a la esquina. Ahora se convierten en el centro del lienzo.

Este es el `PaintSurface` controlador que contiene estas transformar las llamadas en orden inverso:

```csharp
float revolveDegrees, rotateDegrees;
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Red
    })
    {
        // Translate to center of canvas
        canvas.Translate(info.Width / 2, info.Height / 2);

        // Rotate around center of canvas
        canvas.RotateDegrees(revolveDegrees);

        // Translate horizontally
        float radius = Math.Min(info.Width, info.Height) / 3;
        canvas.Translate(radius, 0);

        // Rotate around center of object
        canvas.RotateDegrees(rotateDegrees);

        // Draw a square
        canvas.DrawRect(new SKRect(-50, -50, 50, 50), fillPaint);
    }
}
```

El `revolveDegrees` y `rotateDegrees` se animan los campos. Este programa usa una técnica de animación diferente en función de Xamarin.Forms [ `Animation` ](xref:Xamarin.Forms.Animation) clase. (Esta clase se describe en [capítulo 22 de *Creating Mobile Apps with Xamarin.Forms*](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)) la `OnAppearing` reemplazada crea dos `Animation` los objetos que tienen métodos de devolución de llamada y, a continuación, llama a `Commit` en ellos durante un tiempo de animación:

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    new Animation((value) => revolveDegrees = 360 * (float)value).
        Commit(this, "revolveAnimation", length: 10000, repeat: () => true);

    new Animation((value) =>
    {
        rotateDegrees = 360 * (float)value;
        canvasView.InvalidateSurface();
    }).Commit(this, "rotateAnimation", length: 1000, repeat: () => true);
}
```

La primera `Animation` anima el objeto `revolveDegrees` desde 0 a 360 grados de más de 10 segundos. La segunda se anima `rotateDegrees` desde 0 a 360 grados cada 1 segundo y también invalida la superficie para generar otra llamada a la `PaintSurface` controlador. El `OnDisappearing` invalidación cancela estas dos animaciones:

```csharp
protected override void OnDisappearing()
{
    base.OnDisappearing();
    this.AbortAnimation("revolveAnimation");
    this.AbortAnimation("rotateAnimation");
}
```

El **feo reloj analógico** (se denomina así porque un reloj analógico más atractivo se describirá en un artículo posterior) del programa usa rotación para dibujar las marcas de minuto y la hora del reloj y rotar las manos. El programa dibuja el reloj con un sistema de coordenadas arbitrario basado en un círculo que se centra en el punto (0, 0) con un radio de 100. Usa para expandir y ese círculo en la página del centro de traslación y escala.

El `Translate` y `Scale` llamadas se aplican globalmente al reloj, esos son los primeros que llamarse tras la inicialización de la `SKPaint` objetos:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint strokePaint = new SKPaint())
    using (SKPaint fillPaint = new SKPaint())
    {
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.Color = SKColors.Black;
        strokePaint.StrokeCap = SKStrokeCap.Round;

        fillPaint.Style = SKPaintStyle.Fill;
        fillPaint.Color = SKColors.Gray;

        // Transform for 100-radius circle centered at origin
        canvas.Translate(info.Width / 2f, info.Height / 2f);
        canvas.Scale(Math.Min(info.Width / 200f, info.Height / 200f));
        ...
    }
}
```

Hay 60 marcas de dos tamaños diferentes que se deben dibujar en un círculo alrededor del reloj. El `DrawCircle` llamada dibuja que círculo en el punto (0, -90), que, en relación con el centro del reloj, corresponde a las 12:00. El `RotateDegrees` llamada incrementa el ángulo de rotación en grados 6 después de cada marca de graduación. El `angle` variable se usa únicamente para determinar si se dibuja un círculo grande o un pequeño círculo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
        // Hour and minute marks
        for (int angle = 0; angle < 360; angle += 6)
        {
            canvas.DrawCircle(0, -90, angle % 30 == 0 ? 4 : 2, fillPaint);
            canvas.RotateDegrees(6);
        }
    ...
    }
}
```

Por último, el `PaintSurface` controlador obtiene la hora actual y calcula los grados de rotación de la hora, minuto y segundo manos. Cada manecilla se dibuja en la posición de 12:00, por lo que es el ángulo de rotación en relación con la:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
        DateTime dateTime = DateTime.Now;

        // Hour hand
        strokePaint.StrokeWidth = 20;
        canvas.Save();
        canvas.RotateDegrees(30 * dateTime.Hour + dateTime.Minute / 2f);
        canvas.DrawLine(0, 0, 0, -50, strokePaint);
        canvas.Restore();

        // Minute hand
        strokePaint.StrokeWidth = 10;
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Minute + dateTime.Second / 10f);
        canvas.DrawLine(0, 0, 0, -70, strokePaint);
        canvas.Restore();

        // Second hand
        strokePaint.StrokeWidth = 2;
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Second);
        canvas.DrawLine(0, 10, 0, -80, strokePaint);
        canvas.Restore();
    }
}
```

El reloj es ciertamente funcional, aunque son bastante crudas manos:

[![](rotate-images/uglyanalogclock-small.png "Captura de pantalla de la página de texto de reloj analógico feo triple")](rotate-images/uglyanalogclock-large.png#lightbox "Triple screenshot of the Ugly Analog page")

Para un reloj sea más atractivo, consulte el artículo [ **datos de ruta de acceso de SVG en SkiaSharp**](../curves/path-data.md).

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
