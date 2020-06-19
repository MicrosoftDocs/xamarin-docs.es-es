---
title: Transformación de traslación
description: En este artículo se examina cómo usar la transformación traducir para desplazar los gráficos de SkiaSharp en Xamarin.Forms las aplicaciones y se muestra el código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: BD28ADA1-49F9-44E2-A548-46024A29882F
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0eb3b4a6b37d59363984c9248cc39de91a6819e0
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138260"
---
# <a name="the-translate-transform"></a>Transformación de traslación

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Obtenga información sobre cómo usar la transformación traducir para desplazar los gráficos de SkiaSharp_

El tipo más sencillo de transformación en SkiaSharp es la *transformación* de *traslación o traducción* . Esta transformación desplaza objetos gráficos en las direcciones horizontal y vertical. En cierto sentido, la conversión es la transformación más innecesaria porque normalmente puede lograr el mismo efecto cambiando simplemente las coordenadas que se usan en la función de dibujo. Sin embargo, al representar una ruta de acceso, todas las coordenadas se encapsulan en la ruta de acceso, por lo que es mucho más fácil aplicar una transformación translate para desplazar toda la ruta de acceso.

La traducción también es útil para animaciones y para efectos de texto simples:

![](translate-images/translateexample.png "Text shadow, engraving, and embossing with translation")

El [`Translate`](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single)) método de `SKCanvas` tiene dos parámetros que hacen que los objetos de gráficos dibujados posteriormente se desplacen horizontal y verticalmente:

```csharp
public void Translate (Single dx, Single dy)
```

Estos argumentos pueden ser negativos. Un segundo [`Translate`](xref:SkiaSharp.SKCanvas.Translate(SkiaSharp.SKPoint)) método combina los dos valores de traducción en un solo `SKPoint` valor:

```csharp
public void Translate (SKPoint point)
```

La página de **traducción acumulada** del programa de ejemplo [**SkiaSharpForms**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) muestra que varias llamadas del `Translate` método son acumulativas. La [`AccumulatedTranslatePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AccumulatedTranslatePage.cs) clase muestra 20 versiones del mismo rectángulo, cada una de las cuales tiene un desplazamiento del rectángulo anterior lo suficiente para que se ajusten a lo largo de la diagonal. Este es el `PaintSurface` controlador de eventos:

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

Los rectángulos sucesivos se filtran por la página:

[![](translate-images/accumulatedtranslate-small.png "Triple screenshot of the Accumulated Translate page")](translate-images/accumulatedtranslate-large.png#lightbox "Triple screenshot of the Accumulated Translate page")

Si los factores de conversión acumulados son `dx` y `dy` , y el punto que se especifica en una función de dibujo es ( `x` , `y` ), el objeto gráfico se representa en el punto ( `x'` , `y'` ), donde:

x ' = x + DX

y ' = y + DY

Estas se conocen como *fórmulas de transformación* para la traducción. Los valores predeterminados de `dx` y `dy` de un nuevo `SKCanvas` valor de 0.

Es habitual usar la transformación traducir para efectos de sombra y técnicas similares, como se muestra en la página **traducir efectos de texto** . Esta es la parte relevante del `PaintSurface` controlador en la [`TranslateTextEffectsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TranslateTextEffectsPage.cs) clase:

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

En cada uno de los tres ejemplos, `Translate` se llama a para mostrar el texto que se va a desplazar desde la ubicación dada por las `x` `y` variables y. Después, el texto se muestra de nuevo en otro color sin efecto de conversión:

[![](translate-images/translatetexteffects-small.png "Triple screenshot of the Translate Text Effects page")](translate-images/translatetexteffects-large.png#lightbox "Triple screenshot of the Translate Text Effects page")

En cada uno de los tres ejemplos se muestra una manera diferente de negar la `Translate` llamada:

En el primer ejemplo, simplemente llama `Translate` de nuevo, pero con valores negativos. Dado que las `Translate` llamadas son acumulativas, esta secuencia de llamadas simplemente restaura la traducción total a los valores predeterminados de cero.

En el segundo ejemplo se llama a [`ResetMatrix`](xref:SkiaSharp.SKCanvas.ResetMatrix) . Esto hace que todas las transformaciones vuelvan a su estado predeterminado.

En el tercer ejemplo se guarda el estado del `SKCanvas` objeto con una llamada a [`Save`](xref:SkiaSharp.SKCanvas.Save) y, a continuación, se restaura el estado con una llamada a [`Restore`](xref:SkiaSharp.SKCanvas.Restore) . Esta es la manera más versátil de manipular transformaciones para una serie de operaciones de dibujo. Estas `Save` y llaman a `Restore` una función como una pila: puede llamar `Save` varias veces y, a continuación, llamar a `Restore` en la secuencia inversa para volver a los Estados anteriores. El `Save` método devuelve un entero y puede pasar ese entero a [`RestoreToCount`](xref:SkiaSharp.SKCanvas.RestoreToCount*) para llamar de forma eficaz `Restore` varias veces. La [`SaveCount`](xref:SkiaSharp.SKCanvas.SaveCount) propiedad devuelve el número de Estados que se guardan actualmente en la pila.

También puede usar la [`SKAutoCanvasRestore`](xref:SkiaSharp.SKAutoCanvasRestore) clase para restaurar el estado de canvas. El constructor de esta clase está pensado para ser llamado en una `using` instrucción; el estado del lienzo se restaura automáticamente al final del `using` bloque.

Sin embargo, no tiene que preocuparse de las transformaciones que llevan de una llamada del `PaintSurface` controlador a la siguiente. Cada nueva llamada a `PaintSurface` entrega un `SKCanvas` objeto nuevo con transformaciones predeterminadas.

Otro uso común de la `Translate` transformación es para representar un objeto visual que se ha creado originalmente mediante coordenadas que son prácticas para dibujar. Por ejemplo, puede que desee especificar coordenadas para un reloj analógico con un centro en el punto (0,0). Después, puede usar transformaciones para mostrar el reloj donde desea. Esta técnica se muestra en la página [**Hendecagram array**]. La [`HendecagramArrayPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramArrayPage.cs) clase comienza creando un `SKPath` objeto para una estrella de 11 puntas. El `HendecagramPath` objeto se define como público, estático y de solo lectura para que se pueda obtener acceso a él desde otros programas de demostración. Se crea en un constructor estático:

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

Si el centro de la estrella es el punto (0,0), todos los puntos de la estrella se encuentran en un círculo que rodea ese punto. Cada punto es una combinación de valores de seno y coseno de un ángulo que aumenta en 5/11 de 360 grados. (También es posible crear una estrella de 11 puntas aumentando el ángulo en 2/11, 3/11 o 4/11 del círculo). El radio de ese círculo se establece como 100.

Si esta ruta de acceso se representa sin transformaciones, el centro se colocará en la esquina superior izquierda de `SKCanvas` , y solo se verá un trimestre. `PaintSurface` `HendecagramPage` En su lugar, el controlador usa `Translate` para colocar en mosaico el lienzo con varias copias de la estrella, cada una coloreada aleatoriamente:

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

A menudo, las animaciones implican transformaciones. La página de **animación Hendecagram** mueve la estrella de 11 puntas alrededor de un círculo. La [`HendecagramAnimationPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramAnimationPage.cs) clase comienza con algunos campos e invalidaciones de los `OnAppearing` `OnDisappearing` métodos y para iniciar y detener un Xamarin.Forms temporizador:

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

El `angle` campo se anima desde 0 grados hasta 360 grados cada 5 segundos. El `PaintSurface` controlador usa la `angle` propiedad de dos maneras: para especificar el matiz del color en el `SKColor.FromHsl` método y como argumento de los `Math.Sin` `Math.Cos` métodos y para regir la ubicación de la estrella:

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

El `PaintSurface` controlador llama al `Translate` método dos veces, primero para traducir al centro del lienzo y, a continuación, trasladar a la circunferencia de un círculo centrado en torno a (0,0). El radio del círculo se establece para que sea lo más grande posible mientras se mantiene la estrella dentro de los límites de la página:

[![](translate-images/hendecagramanimation-small.png "Triple screenshot of the Hendecagram Animation page")](translate-images/hendecagramanimation-large.png#lightbox "Triple screenshot of the Hendecagram Animation page")

Tenga en cuenta que la estrella mantiene la misma orientación que gira alrededor del centro de la página. No gira en absoluto. Se trata de un trabajo para una transformación de giro.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
