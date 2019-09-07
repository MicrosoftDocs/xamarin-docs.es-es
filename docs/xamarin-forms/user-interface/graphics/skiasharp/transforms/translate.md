---
title: Transformación de traslación
description: En este artículo examina cómo usar la transformación de traslación para desplazar los gráficos de SkiaSharp en Xamarin.Forms y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: BD28ADA1-49F9-44E2-A548-46024A29882F
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 6e108852b2978903fb74686ad60cf419bbe4e3c0
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772965"
---
# <a name="the-translate-transform"></a>Transformación de traslación

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Aprenda a usar la transformación de traslación para desplazar los gráficos de SkiaSharp_

Es el tipo más sencillo de transformación en SkiaSharp el *traducir* o *traducción* transformar. Esta transformación desplaza objetos gráficos en dirección horizontal y vertical. En cierto sentido, la traducción es la transformación más innecesaria porque normalmente puede lograr el mismo efecto cambiando simplemente las coordenadas que se usa en la función de dibujo. Al representar una ruta de acceso, sin embargo, todas las coordenadas se encapsulan en la ruta de acceso, por lo que es mucho más fácil aplicar una transformación de traslación para desplazar la ruta de acceso completa.

También es útil para la animación y efectos de texto simple traducción:

![](translate-images/translateexample.png "Sombra de texto, grabados y relieve con la traducción")

El [ `Translate` ](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single)) método `SKCanvas` tiene dos parámetros que hacen que los objetos gráficos dibujados a continuación se desplace horizontalmente y verticalmente:

```csharp
public void Translate (Single dx, Single dy)
```

Estos argumentos pueden ser negativos. Un segundo [ `Translate` ](xref:SkiaSharp.SKCanvas.Translate(SkiaSharp.SKPoint)) método combina los valores de dos traducción en una sola `SKPoint` valor:

```csharp
public void Translate (SKPoint point)
```

El **acumulado traducir** página de la [ **SkiaSharpForms** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) programa de ejemplo muestra varias llamadas de la `Translate` método son acumulativos. El [ `AccumulatedTranslatePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AccumulatedTranslatePage.cs) clase muestra 20 versiones del mismo rectángulo, cada uno de ellos desplazamiento desde el rectángulo anterior lo suficiente para estirar a lo largo de la diagonal. Este es el `PaintSurface` controlador de eventos:

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

[![](translate-images/accumulatedtranslate-small.png "Captura de pantalla de la página acumulados traducir triple")](translate-images/accumulatedtranslate-large.png#lightbox "Triple captura de pantalla de la página acumulados traducir")

Si son los factores de traslación acumulado `dx` y `dy`, y es el punto especificado en una función de dibujo (`x`, `y`), a continuación, se representa el objeto gráfico en el punto (`x'`, `y'`), donde:

x' = x + dx

y' = y + dy

Estos se conocen como el *transformar fórmulas* para la traducción. Los valores predeterminados de `dx` y `dy` para un nuevo `SKCanvas` son 0.

Es habitual usar la transformación de traslación para efectos de sombra y técnicas similares, como el **traducir texto efectos** muestra la página. Aquí es la parte relevante de la `PaintSurface` controlador en el [ `TranslateTextEffectsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TranslateTextEffectsPage.cs) clase:

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

En cada uno de los tres ejemplos, `Translate` se llama para mostrar el texto que se desplaza desde la ubicación proporcionada por el `x` y `y` variables. A continuación, el texto se muestra de nuevo en otro color no tiene ningún efecto de traducción:

[![](translate-images/translatetexteffects-small.png "Captura de pantalla triple de la página de traducir los efectos de texto")](translate-images/translatetexteffects-large.png#lightbox "Triple captura de pantalla de la página de traducir los efectos de texto")

Cada uno de los tres ejemplos muestra una manera diferente de que se niegue el `Translate` llamar:

El primer ejemplo se llama simplemente `Translate` nuevo pero con valores negativos. Dado que el `Translate` llamadas son acumulativas, esta secuencia de llamadas simplemente restaura la traducción total a los valores predeterminados de cero.

El segundo ejemplo se llama [ `ResetMatrix` ](xref:SkiaSharp.SKCanvas.ResetMatrix). Esto hace que todas las transformaciones volver a su estado predeterminado.

El tercer ejemplo guarda el estado de la `SKCanvas` objeto con una llamada a [ `Save` ](xref:SkiaSharp.SKCanvas.Save) y, a continuación, restaura el estado con una llamada a [ `Restore` ](xref:SkiaSharp.SKCanvas.Restore). Se trata de la forma más versátil para manipular las transformaciones para una serie de operaciones de dibujo. Estas `Save` y`Restore` llaman a una función como una pila: Puede llamar `Save` varias veces a y, a continuación `Restore` , llamar a en la secuencia inversa para volver a los Estados anteriores. El `Save` método devuelve un entero y puede pasar ese entero a [ `RestoreToCount` ](xref:SkiaSharp.SKCanvas.RestoreToCount*) eficaz llamar a `Restore` varias veces. El [ `SaveCount` ](xref:SkiaSharp.SKCanvas.SaveCount) propiedad devuelve el número de Estados que actualmente se guardan en la pila.

También puede usar el [ `SKAutoCanvasRestore` ](xref:SkiaSharp.SKAutoCanvasRestore) clase para restaurar el estado del lienzo. El constructor de esta clase está pensado para ser llamado un `using` instrucción; el lienzo de estado se restaura automáticamente al final de la `using` bloque. 

Sin embargo, no tiene que preocuparse por las transformaciones que lleva a través de una llamada de la `PaintSurface` controlador a la siguiente. Cada nueva llamada a `PaintSurface` ofrece una nueva `SKCanvas` objeto con transformaciones predeterminadas.

Otro uso común de la `Translate` transformaci para representar un objeto visual que originalmente se creó utilizando las coordenadas son convenientes para el dibujo. Por ejemplo, puede especificar las coordenadas de un reloj analógico con un centro en el punto (0, 0). A continuación, puede utilizar transformaciones para mostrar el reloj donde desee. Esta técnica se muestra en la [**Hendecagram matriz**] página. El [ `HendecagramArrayPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramPage.cs) clase comienza creando una `SKPath` objeto para una estrella 11 puntas. La `HendecagramPath` objeto se define como público, estático y de solo lectura para que se puede acceder desde otros programas de demostración. Se crea en un constructor estático:

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

Si el centro de la estrella es el punto (0, 0), todos los puntos de la estrella están en un círculo en torno a ese punto. Cada punto es una combinación de valores de seno y coseno de un ángulo que se incremente en 5/11ths de 360 grados. (También es posible crear una estrella puntas 11 aumentando el ángulo 2/11, 11ths/3 o 4/11 del círculo.) Se establece el radio del círculo como 100.

Si esta ruta de acceso se representa sin las transformaciones, el centro se colocarán en la esquina superior izquierda de la `SKCanvas`y solo un trimestre de la misma estarán visibles. El `PaintSurface` controlador de `HendecagramPage` en su lugar usa `Translate` lienzo con varias copias de la estrella de mosaico, cada una de ellas al azar de color:

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

[![](translate-images/hendecagramarray-small.png "Captura de pantalla triple de la página de la matriz Hendecagram")](translate-images/hendecagramarray-large.png#lightbox "Triple captura de pantalla de la página de la matriz Hendecagram")

Las animaciones suelen implican las transformaciones. El **Hendecagram animación** página mueve la estrella que señala el 11 en torno a un círculo. El [ `HendecagramAnimationPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramAnimationPage.cs) clase comienza con algunos campos e invalidaciones de la `OnAppearing` y `OnDisappearing` métodos para iniciar y detener un temporizador de Xamarin.Forms:

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

El `angle` campo se anima de 0 grados hasta 360 grados cada 5 segundos. El `PaintSurface` controlador utiliza la `angle` propiedad de dos maneras: para especificar el matiz del color en el `SKColor.FromHsl` método y como un argumento para el `Math.Sin` y `Math.Cos` métodos para controlar la ubicación de la estrella:

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

El `PaintSurface` llamadas del controlador de la `Translate` dos veces, primero al método se traducen en el centro del lienzo y, a continuación, para traducir a la circunferencia de un círculo que se centra en torno a (0, 0). Se establece el radio del círculo que ser tan grande como sea posible mientras se mantiene la estrella dentro de los confines de la página:

[![](translate-images/hendecagramanimation-small.png "Captura de pantalla triple de la página de animación Hendecagram")](translate-images/hendecagramanimation-large.png#lightbox "Triple captura de pantalla de la página de animación Hendecagram")

Tenga en cuenta que la estrella mantiene la misma orientación a medida que gira en torno al centro de la página. No gira en absoluto. Es un trabajo para una transformación de giro.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
