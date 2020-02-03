---
title: Transformación de traslación
description: En este artículo examina cómo usar la transformación de traslación para desplazar los gráficos de SkiaSharp en Xamarin.Forms y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: BD28ADA1-49F9-44E2-A548-46024A29882F
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: f1efd7610b32e6a3903d34fc2f8b5a6e20c9da8a
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76723608"
---
# <a name="the-translate-transform"></a>Transformación de traslación

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Obtenga información sobre cómo usar la transformación traducir para desplazar los gráficos de SkiaSharp_

El tipo más sencillo de transformación en SkiaSharp es la *transformación* de *traslación o traducción* . Esta transformación desplaza objetos gráficos en dirección horizontal y vertical. En cierto sentido, la traducción es la transformación más innecesaria porque normalmente puede lograr el mismo efecto cambiando simplemente las coordenadas que se usa en la función de dibujo. Al representar una ruta de acceso, sin embargo, todas las coordenadas se encapsulan en la ruta de acceso, por lo que es mucho más fácil aplicar una transformación de traslación para desplazar la ruta de acceso completa.

También es útil para la animación y efectos de texto simple traducción:

![](translate-images/translateexample.png "Text shadow, engraving, and embossing with translation")

El método [`Translate`](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single)) de `SKCanvas` tiene dos parámetros que hacen que los objetos gráficos dibujados posteriormente se desplacen horizontal y verticalmente:

```csharp
public void Translate (Single dx, Single dy)
```

Estos argumentos pueden ser negativos. Un segundo método [`Translate`](xref:SkiaSharp.SKCanvas.Translate(SkiaSharp.SKPoint)) combina los dos valores de traslación en un único valor de `SKPoint`:

```csharp
public void Translate (SKPoint point)
```

La página **traducción acumulada** del programa de ejemplo [**SkiaSharpForms**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) muestra que varias llamadas del método `Translate` son acumulativas. La clase [`AccumulatedTranslatePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AccumulatedTranslatePage.cs) muestra 20 versiones del mismo rectángulo, cada una de las cuales tiene un desplazamiento del rectángulo anterior lo suficiente para que se ajusten a lo largo de la diagonal. A continuación se muestra el controlador de eventos `PaintSurface`:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint strokePaint = new SKPaint())
    {
        strokePaint.Color = SKColors.Black;
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.StrokeWidth = 3;

        int rectangleCount = 20;
        SKRect rect = new SKRect(0, 0, 250, 250);
        float xTranslate = (info.Width - rect.Width) / (rectangleCount - 1);
        float yTranslate = (info.Height - rect.Height) / (rectangleCount - 1);

        for (int i = 0; i < rectangleCount; i++)
        {
            canvas.DrawRect(rect, strokePaint);
            canvas.Translate(xTranslate, yTranslate);
        }
    }
}
```

Los rectángulos sucesivas introduciéndose en la página:

[![](translate-images/accumulatedtranslate-small.png "Triple screenshot of the Accumulated Translate page")](translate-images/accumulatedtranslate-large.png#lightbox "Triple screenshot of the Accumulated Translate page")

Si los factores de conversión acumulados son `dx` y `dy`, y el punto que se especifica en una función de dibujo es (`x`, `y`), el objeto gráfico se representa en el punto (`x'`, `y'`), donde:

x' = x + dx

y' = y + dy

Estas se conocen como *fórmulas de transformación* para la traducción. Los valores predeterminados de `dx` y `dy` para una nueva `SKCanvas` son 0.

Es habitual usar la transformación traducir para efectos de sombra y técnicas similares, como se muestra en la página **traducir efectos de texto** . Esta es la parte relevante del controlador de `PaintSurface` en la clase [`TranslateTextEffectsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TranslateTextEffectsPage.cs) :

```csharp
float textSize = 150;

using (SKPaint textPaint = new SKPaint())
{
    textPaint.Style = SKPaintStyle.Fill;
    textPaint.TextSize = textSize;
    textPaint.FakeBoldText = true;

    float x = 10;
    float y = textSize;

    // Shadow
    canvas.Translate(10, 10);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("SHADOW", x, y, textPaint);
    canvas.Translate(-10, -10);
    textPaint.Color = SKColors.Pink;
    canvas.DrawText("SHADOW", x, y, textPaint);

    y += 2 * textSize;

    // Engrave
    canvas.Translate(-5, -5);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("ENGRAVE", x, y, textPaint);
    canvas.ResetMatrix();
    textPaint.Color = SKColors.White;
    canvas.DrawText("ENGRAVE", x, y, textPaint);

    y += 2 * textSize;

    // Emboss
    canvas.Save();
    canvas.Translate(5, 5);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("EMBOSS", x, y, textPaint);
    canvas.Restore();
    textPaint.Color = SKColors.White;
    canvas.DrawText("EMBOSS", x, y, textPaint);
}
```

En cada uno de los tres ejemplos, se llama a `Translate` para mostrar el texto que se va a desplazar desde la ubicación proporcionada por las variables `x` y `y`. A continuación, el texto se muestra de nuevo en otro color no tiene ningún efecto de traducción:

[![](translate-images/translatetexteffects-small.png "Triple screenshot of the Translate Text Effects page")](translate-images/translatetexteffects-large.png#lightbox "Triple screenshot of the Translate Text Effects page")

En cada uno de los tres ejemplos se muestra una manera diferente de negar la llamada `Translate`:

En el primer ejemplo simplemente se llama a `Translate`, pero con valores negativos. Dado que las llamadas `Translate` son acumulativas, esta secuencia de llamadas simplemente restaura la traducción total a los valores predeterminados de cero.

En el segundo ejemplo se llama a [`ResetMatrix`](xref:SkiaSharp.SKCanvas.ResetMatrix). Esto hace que todas las transformaciones volver a su estado predeterminado.

En el tercer ejemplo se guarda el estado del objeto `SKCanvas` con una llamada a [`Save`](xref:SkiaSharp.SKCanvas.Save) y, a continuación, se restaura el estado con una llamada a [`Restore`](xref:SkiaSharp.SKCanvas.Restore). Se trata de la forma más versátil para manipular las transformaciones para una serie de operaciones de dibujo. Estas llamadas `Save` y `Restore` funcionan como una pila: puede llamar a `Save` varias veces y, a continuación, llamar a `Restore` en la secuencia inversa para volver a los Estados anteriores. El método `Save` devuelve un entero y puede pasar ese entero a [`RestoreToCount`](xref:SkiaSharp.SKCanvas.RestoreToCount*) para llamar de forma eficaz `Restore` varias veces. La propiedad [`SaveCount`](xref:SkiaSharp.SKCanvas.SaveCount) devuelve el número de Estados que se guardan actualmente en la pila.

También puede usar la clase [`SKAutoCanvasRestore`](xref:SkiaSharp.SKAutoCanvasRestore) para restaurar el estado de canvas. El constructor de esta clase está pensado para ser llamado en una instrucción `using`; el estado del lienzo se restaura automáticamente al final del bloque de `using`.

Sin embargo, no tiene que preocuparse de las transformaciones que llevan de una llamada del controlador de `PaintSurface` a la siguiente. Cada nueva llamada a `PaintSurface` ofrece un nuevo objeto `SKCanvas` con transformaciones predeterminadas.

Otro uso común de la transformación `Translate` es para representar un objeto visual que se ha creado originalmente mediante coordenadas que son convenientes para dibujar. Por ejemplo, puede especificar las coordenadas de un reloj analógico con un centro en el punto (0, 0). A continuación, puede utilizar transformaciones para mostrar el reloj donde desee. Esta técnica se muestra en la página [**Hendecagram array**]. La clase [`HendecagramArrayPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramArrayPage.cs) comienza creando un objeto `SKPath` para una estrella de 11 puntas. El objeto de `HendecagramPath` se define como público, estático y de solo lectura para que se pueda obtener acceso a él desde otros programas de demostración. Se crea en un constructor estático:

```csharp
public class HendecagramArrayPage : ContentPage
{
    ...
    public static readonly SKPath HendecagramPath;

    static HendecagramArrayPage()
    {
        // Create 11-pointed star
        HendecagramPath = new SKPath();
        for (int i = 0; i < 11; i++)
        {
            double angle = 5 * i * 2 * Math.PI / 11;
            SKPoint pt = new SKPoint(100 * (float)Math.Sin(angle),
                                    -100 * (float)Math.Cos(angle));
            if (i == 0)
            {
                HendecagramPath.MoveTo(pt);
            }
            else
            {
                HendecagramPath.LineTo(pt);
            }
        }
        HendecagramPath.Close();
    }
}
```

Si el centro de la estrella es el punto (0, 0), todos los puntos de la estrella están en un círculo en torno a ese punto. Cada punto es una combinación de valores de seno y coseno de un ángulo que se incremente en 5/11ths de 360 grados. (También es posible crear una estrella de 11 puntas aumentando el ángulo en 2/11, 3/11 o 4/11 del círculo). El radio de ese círculo se establece como 100.

Si esta ruta de acceso se representa sin transformaciones, el centro se colocará en la esquina superior izquierda del `SKCanvas`y solo se verá un cuarto de él. En su lugar, el controlador de `PaintSurface` de `HendecagramPage` usa `Translate` para colocar en mosaico el lienzo con varias copias de la estrella, cada una de ellas coloreada aleatoriamente:

```csharp
public class HendecagramArrayPage : ContentPage
{
    Random random = new Random();
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            for (int x = 100; x < info.Width + 100; x += 200)
                for (int y = 100; y < info.Height + 100; y += 200)
                {
                    // Set random color
                    byte[] bytes = new byte[3];
                    random.NextBytes(bytes);
                    paint.Color = new SKColor(bytes[0], bytes[1], bytes[2]);

                    // Display the hendecagram
                    canvas.Save();
                    canvas.Translate(x, y);
                    canvas.DrawPath(HendecagramPath, paint);
                    canvas.Restore();
                }
        }
    }
}

```

Este es el resultado:

[![](translate-images/hendecagramarray-small.png "Triple screenshot of the Hendecagram Array page")](translate-images/hendecagramarray-large.png#lightbox "Triple screenshot of the Hendecagram Array page")

Las animaciones suelen implican las transformaciones. La página de **animación Hendecagram** mueve la estrella de 11 puntas alrededor de un círculo. La clase [`HendecagramAnimationPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramAnimationPage.cs) comienza con algunos campos e invalidaciones de los métodos `OnAppearing` y `OnDisappearing` para iniciar y detener un temporizador de Xamarin. Forms:

```csharp
public class HendecagramAnimationPage : ContentPage
{
    const double cycleTime = 5000;      // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float angle;

    public HendecagramAnimationPage()
    {
        Title = "Hedecagram Animation";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;
        stopwatch.Start();

        Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
        {
            double t = stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime;
            angle = (float)(360 * t);
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

El campo `angle` se anima desde 0 grados hasta 360 grados cada 5 segundos. El controlador de `PaintSurface` usa la propiedad `angle` de dos maneras: para especificar el matiz del color en el método `SKColor.FromHsl` y como argumento para los métodos `Math.Sin` y `Math.Cos` para regir la ubicación de la estrella:

```csharp
public class HendecagramAnimationPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.Translate(info.Width / 2, info.Height / 2);
        float radius = (float)Math.Min(info.Width, info.Height) / 2 - 100;

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Fill;
            paint.Color = SKColor.FromHsl(angle, 100, 50);

            float x = radius * (float)Math.Sin(Math.PI * angle / 180);
            float y = -radius * (float)Math.Cos(Math.PI * angle / 180);
            canvas.Translate(x, y);
            canvas.DrawPath(HendecagramPage.HendecagramPath, paint);
        }
    }
}
```

El controlador de `PaintSurface` llama al método `Translate` dos veces, primero para traducir al centro del lienzo y, a continuación, trasladar a la circunferencia de un círculo centrado en torno (0,0). Se establece el radio del círculo que ser tan grande como sea posible mientras se mantiene la estrella dentro de los confines de la página:

[![](translate-images/hendecagramanimation-small.png "Triple screenshot of the Hendecagram Animation page")](translate-images/hendecagramanimation-large.png#lightbox "Triple screenshot of the Hendecagram Animation page")

Tenga en cuenta que la estrella mantiene la misma orientación a medida que gira en torno al centro de la página. No gira en absoluto. Es un trabajo para una transformación de giro.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
