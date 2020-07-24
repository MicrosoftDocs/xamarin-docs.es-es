---
title: Transformación de rotación
description: En este artículo se exploran los efectos y las animaciones posibles con la transformación girar SkiaSharp y se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: CBB3CD72-4377-4EA3-A768-0C4228229FC2
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 533cb607cc2c47bf108d5e03733f31ebfc2fe475
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86934139"
---
# <a name="the-rotate-transform"></a>Transformación de rotación

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explore los efectos y las animaciones posibles con la transformación girar SkiaSharp_

Con la transformación girar, los objetos de gráficos SkiaSharp se rompen libremente de la restricción de alineación con los ejes horizontal y vertical:

![Texto girado en torno a un centro](rotate-images/rotateexample.png)

Para girar un objeto gráfico alrededor del punto (0,0), SkiaSharp admite un [`RotateDegrees`](xref:SkiaSharp.SKCanvas.RotateDegrees(System.Single)) método y un [`RotateRadians`](xref:SkiaSharp.SKCanvas.RotateRadians(System.Single)) método:

```csharp
public void RotateDegrees (Single degrees)

public Void RotateRadians (Single radians)
```

Un círculo de 360 grados es igual que twoπ radianes, por lo que es fácil convertir entre las dos unidades. Use lo que sea conveniente. Todas las funciones trigonométricas de la [`Math`](xref:System.Math) clase .net usan unidades de radianes.

La rotación es en el sentido de las agujas del reloj para ángulos crecientes (Aunque la rotación en el sistema de coordenadas cartesiano está en el sentido contrario a las agujas del reloj por Convención, la rotación en el sentido de las agujas del reloj es coherente con las coordenadas Y aumentando en el SkiaSharp). Se permiten ángulos negativos y ángulos superiores a 360 grados.

Las fórmulas de transformación para la rotación son más complejas que las de translate y Scale. Para un ángulo de α, las fórmulas de transformación son:

x ' = x • cos (α) – y • sin (α)   

y ' = x • sin (α) + y • cos (α)

La página **giro básico** muestra el `RotateDegrees` método. El archivo [**BasicRotate.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicRotatePage.xaml.cs) muestra texto con su línea base centrada en la página y lo gira basándose en un `Slider` intervalo de – 360 a 360. Esta es la parte relevante del `PaintSurface` controlador:

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

Dado que la rotación se centra alrededor de la esquina superior izquierda del lienzo, para la mayoría de los ángulos establecidos en este programa, el texto se gira fuera de la pantalla:

[![Captura de pantalla triple de la página girar básica](rotate-images/basicrotate-small.png)](rotate-images/basicrotate-large.png#lightbox "Captura de pantalla triple de la página girar básica")

Con mucha frecuencia, querrá girar algo centrado alrededor de un punto de pivote especificado mediante estas versiones de los [`RotateDegrees`](xref:SkiaSharp.SKCanvas.RotateDegrees(System.Single,System.Single,System.Single)) [`RotateRadians`](xref:SkiaSharp.SKCanvas.RotateRadians(System.Single,System.Single,System.Single)) métodos y:

```csharp
public void RotateDegrees (Single degrees, Single px, Single py)

public void RotateRadians (Single radians, Single px, Single py)
```

La página de **girar centrada** es igual que la **rotación básica** , salvo que la versión expandida de `RotateDegrees` se usa para establecer el centro de rotación en el mismo punto que se usa para colocar el texto:

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

[![Captura de pantalla triple de la página giro centrado](rotate-images/centeredrotate-small.png)](rotate-images/centeredrotate-large.png#lightbox "Captura de pantalla triple de la página giro centrado")

Al igual que con la versión centrada del `Scale` método, la versión centrada de la `RotateDegrees` llamada es un acceso directo. Este es el método:

```csharp
RotateDegrees (degrees, px, py);
```

Esa llamada es equivalente a la siguiente:

```csharp
canvas.Translate(px, py);
canvas.RotateDegrees(degrees);
canvas.Translate(-px, -py);
```

Descubrirá que a veces puede combinar `Translate` llamadas con `Rotate` llamadas. Por ejemplo, a continuación se enumeran las `RotateDegrees` `DrawText` llamadas y en la página **girar centrada** ;

```csharp
canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

La `RotateDegrees` llamada es equivalente a dos `Translate` llamadas y un no centrado `RotateDegrees` :

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

La `DrawText` llamada para mostrar texto en una ubicación determinada es equivalente a una `Translate` llamada para esa ubicación seguida de `DrawText` en el punto (0,0):

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.DrawText(Title, 0, 0, textPaint);
```

Las dos llamadas consecutivas se `Translate` cancelan entre sí:

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.DrawText(Title, 0, 0, textPaint);
```

Conceptualmente, las dos transformaciones se aplican en el orden opuesto al modo en que aparecen en el código. La `DrawText` llamada muestra el texto en la esquina superior izquierda del lienzo. La `RotateDegrees` llamada gira ese texto en relación con la esquina superior izquierda. A continuación, la `Translate` llamada mueve el texto al centro del lienzo.

Normalmente hay varias maneras de combinar la rotación y la traslación. La página **texto girado** crea la siguiente pantalla:

[![Captura de pantalla triple de la página de texto girado](rotate-images/rotatedtext-small.png)](rotate-images/rotatedtext-large.png#lightbox "Captura de pantalla triple de la página de texto girado")

Este es el `PaintSurface` controlador de la [`RotatedTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/RotatedTextPage.cs) clase:

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

Los `xCenter` `yCenter` valores y indican el centro del lienzo. El `yText` valor es un poco desplazamiento de eso. Este valor es la coordenada Y necesaria para colocar el texto de forma que esté realmente centrado en la página. `for`A continuación, el bucle establece una rotación basada en el centro del lienzo. La rotación es en incrementos de 30 grados. El texto se dibuja utilizando el `yText` valor. El número de espacios en blanco antes de la palabra "ROTAte" en el `text` valor se determinó empíricamente para hacer que la conexión entre estas 12 cadenas de texto parezca ser un dodecagon.

Una manera de simplificar este código es incrementar el ángulo de rotación en 30 grados cada vez a través del bucle después de la `DrawText` llamada. Esto elimina la necesidad de llamar a `Save` y `Restore` . Tenga en cuenta que la `degrees` variable ya no se usa en el cuerpo del `for` bloque:

```csharp
for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, xCenter, yText, textPaint);
    canvas.RotateDegrees(30, xCenter, yCenter);
}

```

También es posible usar la forma simple de si se `RotateDegrees` preorienta el bucle con una llamada a `Translate` para moverse todo al centro del lienzo:

```csharp
float yText = -textBounds.Height / 2 - textBounds.Top;

canvas.Translate(xCenter, yCenter);

for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, 0, yText, textPaint);
    canvas.RotateDegrees(30);
}
```

El `yText` cálculo modificado ya no se incorpora `yCenter` . Ahora la `DrawText` llamada centra el texto verticalmente en la parte superior del lienzo.

Dado que las transformaciones se aplican de forma conceptual en cuanto a cómo aparecen en el código, a menudo es posible comenzar con más transformaciones globales, seguidas de más transformaciones locales. Esta suele ser la manera más sencilla de combinar la rotación y la traslación.

Por ejemplo, supongamos que desea dibujar un objeto gráfico que gira alrededor de su centro de forma muy parecida a una rotación de planeta en su eje. Pero también desea que este objeto gire en torno al centro de la pantalla de forma muy similar a un planeta girando alrededor del sol.

Puede hacer esto colocando el objeto en la esquina superior izquierda del lienzo y, a continuación, utilizando una animación para girarlo alrededor de esa esquina. A continuación, traduzca el objeto horizontalmente como un radio orbital. Ahora aplique una segunda rotación animada, también en torno al origen. Esto hace que el objeto gire alrededor de la esquina. Ahora se convierte en el centro del lienzo.

Este es el `PaintSurface` controlador que contiene estas llamadas de transformación en orden inverso:

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

Los `revolveDegrees` `rotateDegrees` campos y están animados. Este programa utiliza una técnica de animación diferente basada en la Xamarin.Forms [`Animation`](xref:Xamarin.Forms.Animation) clase. (Esta clase se describe en el [capítulo 22 sobre *Cómo crear Xamarin.Forms Mobile Apps con * ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)) la `OnAppearing` invalidación crea dos `Animation` objetos con métodos de devolución de llamada y, a continuación, llama a `Commit` en ellos para una duración de animación:

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

El primer `Animation` objeto anima `revolveDegrees` desde 0 grados hasta 360 grados en 10 segundos. El segundo anima `rotateDegrees` de 0 a 360 grados cada 1 segundo y también invalida la superficie para generar otra llamada al `PaintSurface` controlador. La `OnDisappearing` invalidación cancela estas dos animaciones:

```csharp
protected override void OnDisappearing()
{
    base.OnDisappearing();
    this.AbortAnimation("revolveAnimation");
    this.AbortAnimation("rotateAnimation");
}
```

El programa de **reloj analógico desagradable** (que se llama porque un reloj analógico más atractivo se describe en un artículo posterior) usa la rotación para dibujar las marcas de minuto y hora del reloj y para girar las manecillas. El programa dibuja el reloj mediante un sistema de coordenadas arbitrario basado en un círculo que se centra en el punto (0,0) con un radio de 100. Usa la traducción y el escalado para expandir y centrar ese círculo en la página.

Las `Translate` `Scale` llamadas y se aplican globalmente al reloj, por lo que son las primeras a las que se llamará después de la inicialización de los `SKPaint` objetos:

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

Hay 60 marcas de dos tamaños diferentes que deben dibujarse en un círculo alrededor del reloj. La `DrawCircle` llamada dibuja ese círculo en el punto (0, – 90), que en relación con el centro del reloj corresponde a 12:00. La `RotateDegrees` llamada incrementa el ángulo de rotación en 6 grados después de cada marca de graduación. La `angle` variable se utiliza únicamente para determinar si se dibuja un círculo grande o un círculo pequeño:

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

Por último, el `PaintSurface` controlador obtiene la hora actual y calcula los grados de rotación de las manecillas de hora, minuto y segundo. Cada mano se dibuja en la posición 12:00 para que el ángulo de rotación sea relativo a lo siguiente:

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

El reloj es ciertamente funcional, aunque las manos son bastante brutas:

[![Captura de pantalla triple de la página de texto del reloj análogo desagradable](rotate-images/uglyanalogclock-small.png)](rotate-images/uglyanalogclock-large.png#lightbox "Captura de pantalla triple de la página analógica desagradable")

Para obtener un reloj más atractivo, consulte el artículo [**datos de la ruta de acceso SVG en SkiaSharp**](../curves/path-data.md).

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
