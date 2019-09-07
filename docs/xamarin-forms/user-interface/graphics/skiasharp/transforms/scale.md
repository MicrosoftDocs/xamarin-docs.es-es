---
title: Transformación de escala
description: En este artículo explora la transformación de escala de SkiaSharp para el escalado de objetos para distintos tamaños y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 54A43F3D-9DA8-44A7-9AE4-7E3025129A0B
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2017
ms.openlocfilehash: 1adade4e66a6df504ba7c8ac3ff1f668c014fe93
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770448"
---
# <a name="the-scale-transform"></a>Transformación de escala

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Detectar la transformación de escala para el escalado de objetos para diversos tamaños de SkiaSharp_

Como ha visto en [ **traducir la transformación** ](translate.md) artículo, la transformación de traslación puede mover un objeto gráfico de una ubicación a otra. En cambio, la transformación de escala cambia el tamaño del objeto gráfico:

![](scale-images/scaleexample.png "Una palabra alta escalada de tamaño")

La transformación de escala también a menudo hace que las coordenadas de gráficos mover la medida que se hacen más grandes.

Ya vimos dos fórmulas de transformación que describen los efectos de factores de traslación de `dx` y `dy`:

x' = x + dx

y' = y + dy

Escalar los factores de `sx` y `sy` son multiplicación en lugar de adición:

x' = sx (en inglés) x

y' = sy (en inglés) y

Los valores predeterminados de los factores de traslación están 0; los valores predeterminados de los factores de escala son 1.

El `SKCanvas` clase define cuatro `Scale` métodos. La primera [ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(System.Single)) método es para incluir casos cuando desee que la misma escala horizontal y vertical:

```csharp
public void Scale (Single s)
```

Esto se conoce como *isótropo* escalado &mdash; escala que es el mismo en ambas direcciones. Escalado isótropo conserva la relación de aspecto del objeto.

El segundo [ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single)) método le permite especificar valores diferentes para el escalado horizontal y vertical:

```csharp
public void Scale (Single sx, Single sy)
```

Esto da como resultado *anisotrópico* escalado.
La tercera [ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(SkiaSharp.SKPoint)) método combina los dos factores de escala en una sola `SKPoint` valor:

```csharp
public void Scale (SKPoint size)
```

El cuarto `Scale` método se describirá en breve.

El **escalado básico** página muestra la `Scale` método. El [ **BasicScalePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml) archivo contiene dos `Slider` elementos que le permiten seleccionar los factores de escala horizontales y verticales entre 0 y 10. El [ **BasicScalePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs) archivo de código subyacente usa esos valores para llamar a `Scale` antes de mostrar un rectángulo redondeado trazan con una línea discontinua y tamaño para ajustarse a algún texto en la esquina superior izquierda esquina del lienzo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] {  7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        canvas.Scale((float)xScaleSlider.Value,
                     (float)yScaleSlider.Value);

        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);

        float margin = 10;
        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

Puede que se pregunte: ¿Cómo afectan los factores de escala al valor devuelto `MeasureText` desde el `SKPaint`método de? La respuesta es la siguiente: No, en absoluto. `Scale` es un método de `SKCanvas`. No afecta a todo lo que hacer con un `SKPaint` objeto hasta que ese objeto se usa para representar algo en el lienzo.

Como puede ver, todo lo que se dibuja después el `Scale` llamar aumenta proporcionalmente:

[![](scale-images/basicscale-small.png "Captura de pantalla de la página de escalado básico triple")](scale-images/basicscale-large.png#lightbox "Triple captura de pantalla de la página de escalado básico")

El texto, el ancho de la línea discontinua, la longitud de los guiones en esa línea, el redondeo de las esquinas y en el margen de 10 píxeles entre los bordes superiores e izquierdos del lienzo y el rectángulo redondeado son todos los sujetos a los mismos factores de escala.

> [!IMPORTANT]
> La plataforma Universal de Windows no se representará correctamente texto escalado anisotropicly.

Anisotrópico escalado hace que el ancho del trazo para convertirse en diferentes para las líneas alineado con los ejes horizontales y vertical. (Esto es también puede deducirse a partir de la primera imagen en esta página). Si no desea que el ancho del trazo se vea afectado por los factores de escala, establézcalo en 0 y siempre será un píxel de ancho, con independencia de la `Scale` configuración.

El escalado es relativo a la esquina superior izquierda del lienzo. Esto podría ser exactamente lo que desea, pero no es posible. Suponga que desea colocar el texto y el rectángulo en otro lugar en el lienzo y desea ajustar la escala en relación con su centro. En ese caso puede utilizar la cuarta versión de la [ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) método, que incluye dos parámetros adicionales para especificar el centro de escalado:

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

El `px` y `py` parámetros definen un punto que a veces se denomina el *escalado center* , pero en el SkiaSharp documentación se conoce como un *punto de pivote*. Este es un punto con respecto a la esquina superior izquierda del lienzo que no se ve afectado por el ajuste de escala. Los ajustes de escala se produce relativa a dicho centro.

El [ **centrado escala** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs) página muestra cómo funciona esto. El `PaintSurface` controlador es similar a la **escalado básico** de programa, excepto que el `margin` valor se calcula para centrar el texto horizontalmente, lo cual implica que el programa funciona mejor en modo vertical:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] { 7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);
        float margin = (info.Width - textBounds.Width) / 2;

        float sx = (float)xScaleSlider.Value;
        float sy = (float)yScaleSlider.Value;
        float px = margin + textBounds.Width / 2;
        float py = margin + textBounds.Height / 2;

        canvas.Scale(sx, sy, px, py);

        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

Se coloca la esquina superior izquierda del rectángulo redondeado `margin` píxeles desde la izquierda del lienzo y `margin` píxeles desde la parte superior. Los dos últimos argumentos para el `Scale` método se establecen en los valores más el ancho y alto del texto, que también es el ancho y alto del rectángulo redondeado. Esto significa que los ajustes de escala en relación con el centro del rectángulo:

[![](scale-images/centeredscale-small.png "Captura de pantalla triple de la página escala centrado")](scale-images/centeredscale-large.png#lightbox "Triple captura de pantalla de la página escala centrado")

El `Slider` elementos en este programa tienen un intervalo de &ndash;10 a 10. Como puede ver, los valores negativos (por ejemplo en Android, pantalla en el centro) escalado vertical hará que los objetos se voltea alrededor del eje horizontal que pasa a través del centro de escalado. Los valores negativos (tal como se muestra en la pantalla UWP a la derecha) el escalado horizontal hará que los objetos se voltea alrededor del eje vertical que pasa a través del centro de escalado.

La versión de la [ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) método con puntos de pivote es un acceso directo para una serie de tres `Translate` y `Scale` llamadas. Desea ver cómo funciona esto reemplazando el `Scale` método en el **centrado escala** página con lo siguiente:

```csharp
canvas.Translate(-px, -py);
```

Estos son los valores negativos de las coordenadas del punto de pivote.

Ejecute el programa otra vez. Verá que el rectángulo y el texto se desplazan para que el centro está en la esquina superior izquierda del lienzo. Apenas puede verlo. Los controles deslizantes no funcionan supuesto porque ahora el programa no aumenta en absoluto.

Ahora, agregue las opciones básicas `Scale` llamar (sin un centro de la escala) *antes* que `Translate` llamar:

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Si está familiarizado con este ejercicio en otros que sistemas de programación de gráficos, se podría pensar que es incorrecto, pero no lo es. Skia controla llamadas sucesivas transformación un poco diferente de lo que podría estar familiarizado.

Con las sucesivas `Scale` y `Translate` llamadas, el centro del rectángulo redondeado está en la esquina superior izquierda, pero ahora puede escalar con respecto a la esquina superior izquierda del lienzo, que también es el centro del rectángulo redondeado.

Ahora, antes de que `Scale` llamada, agregue otro `Translate` llamar con los valores centrados:

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Esto desplaza el resultado de la escalado a la posición original. Son equivalentes a esas tres llamadas:

```csharp
canvas.Scale(sx, sy, px, py);
```

Las transformaciones individuales se combinan para que la fórmula de la transformación total es:

 x' = sx (en inglés) (x – px) + px

 y' = sy (en inglés) (y – py) + py

Tenga en cuenta que los valores predeterminados de `sx` y `sy` son 1. Es fácil le quepa duda de que estas fórmulas no transforma el punto de pivote (px, py). Permanece en la misma ubicación en relación con el lienzo.

Si combina `Translate` y `Scale` llamadas, el orden es importante. Si el `Translate` viene después de la `Scale`, los factores de traslación se escalan de forma eficaz mediante los factores de escala. Si el `Translate` precede el `Scale`, no se escalan los factores de traslación. Este proceso se vuelve un poco más claro (aunque más matemáticos) cuando se especifica el asunto de matrices de transformación.

El `SKPath` clase define solo lectura [ `Bounds` ](xref:SkiaSharp.SKPath.Bounds) propiedad que devuelve un `SKRect` definir el alcance de las coordenadas en la ruta de acceso. Por ejemplo, cuando el `Bounds` se obtiene la propiedad de la ruta de acceso de hendecagram que creó anteriormente, el `Left` y `Top` las propiedades del rectángulo son aproximadamente -100, el `Right` y `Bottom` son propiedades aproximadamente 100 y el `Width` y `Height` propiedades son aproximadamente 200. (La mayoría de los valores reales es un poco menor porque los puntos de las estrellas se definen por un círculo con un radio de 100, pero solo el punto superior es paralelo con los ejes horizontales o verticales).

La disponibilidad de esta información implica que debe ser posible derivar la escala y traducir factores adecuados para el escalado de una ruta de acceso para el tamaño del lienzo. El [ **escalado anisotrópico** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs) página muestra con la estrella que señala la 11. Un *anisotrópico* escala significa que es desigual en dirección horizontal y vertical, lo que significa que la estrella no conserva su relación de aspecto original. Este es el código pertinente el `PaintSurface` controlador:

```csharp
SKPath path = HendecagramPage.HendecagramPath;
SKRect pathBounds = path.Bounds;

using (SKPaint fillPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Pink
})
using (SKPaint strokePaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 3,
    StrokeJoin = SKStrokeJoin.Round
})
{
    canvas.Scale(info.Width / pathBounds.Width,
                 info.Height / pathBounds.Height);
    canvas.Translate(-pathBounds.Left, -pathBounds.Top);

    canvas.DrawPath(path, fillPaint);
    canvas.DrawPath(path, strokePaint);
}
```

El `pathBounds` rectángulo es obtenido en la parte superior de este código y, a continuación, se usará más adelante con el ancho y alto del lienzo en el `Scale` llamar. Llamada por sí mismo escalará las coordenadas de la ruta de acceso cuando se representa por la `DrawPath` llamada pero la estrella se centrará en la esquina superior derecha del lienzo. Debe desplazar hacia abajo y a la izquierda. Este es el trabajo de la `Translate` llamar. Esas dos propiedades de `pathBounds` están aproximadamente -100, por lo que los factores de traslación alrededor de 100. Porque el `Translate` llamada es después de la `Scale` llamar, esos valores se escalan eficazmente mediante los factores de escala, por lo que mover el centro de la estrella en el centro del lienzo:

[![](scale-images/anisotropicscaling-small.png "Captura de pantalla de la página escala anisotrópico triple")](scale-images/anisotropicscaling-large.png#lightbox "Triple captura de pantalla de la página escala anisotrópico")

Otra manera de pensar en las `Scale` llamadas a y `Translate` es determinar el efecto en la secuencia inversa: La `Translate` llamada desplaza la ruta de acceso para que se vuelva totalmente visible pero esté orientada en la esquina superior izquierda del lienzo. El `Scale` método, a continuación, realiza ese estrella mayor con respecto a la esquina superior izquierda.

En realidad, parece que el asterisco es un poco mayor que el lienzo. El problema es el ancho del trazo. El `Bounds` propiedad de `SKPath` indica las dimensiones de las coordenadas se codifican en la ruta de acceso, y eso es lo que el programa usa para escalar. Cuando se representa la ruta de acceso con un ancho de trazo determinado, la ruta de acceso representada es mayor que el lienzo.

Para corregir este problema, que es necesario para compensar que. Un enfoque fácil en este programa consiste en agregar el derecho de instrucción siguientes antes el `Scale` llamar:

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

Esto aumenta la `pathBounds` rectángulo por 1,5 unidades en los cuatro lados. Se trata de una solución razonable solo cuando se redondea a la combinación de trazo. Una unión angular puede ser más larga y difícil de calcular.

También puede usar una técnica similar con texto, como el **texto anisotrópico** muestra la página. Aquí es la parte relevante de la `PaintSurface` controlador desde la [ `AnisotropicTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs) clase:

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 0.1f,
    StrokeJoin = SKStrokeJoin.Round
})
{
    SKRect textBounds = new SKRect();
    textPaint.MeasureText("HELLO", ref textBounds);

    // Inflate bounds by the stroke width
    textBounds.Inflate(textPaint.StrokeWidth / 2,
                       textPaint.StrokeWidth / 2);

    canvas.Scale(info.Width / textBounds.Width,
                 info.Height / textBounds.Height);
    canvas.Translate(-textBounds.Left, -textBounds.Top);

    canvas.DrawText("HELLO", 0, 0, textPaint);
}
```

Es una lógica similar, y el texto se expande hasta el tamaño de la página según el rectángulo de límites de texto devuelto desde `MeasureText` (que es un poco mayor que el texto real):

[![](scale-images/anisotropictext-small.png "Captura de pantalla triple de la página de prueba anisotrópico")](scale-images/anisotropictext-large.png#lightbox "Triple captura de pantalla de la página de prueba anisotrópico")

Si necesita conservar la relación de aspecto de los objetos gráficos, desea usar el escalado isótropo. El **escalado isótropo** página muestra para la estrella que señala la 11. Conceptualmente, los pasos para mostrar un objeto gráfico en el centro de la página con un escalado isótropo son:

- Traducir el centro del objeto gráfico en la esquina superior izquierda.
- Escalar el objeto basándose en el valor mínimo de las dimensiones de página horizontales y verticales dividido entre las dimensiones del objeto gráfico.
- Traducir el centro del objeto escalado hasta el centro de la página.

El [ `IsotropicScalingPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/skia-sharp-forms/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs) lleva a cabo estos pasos en orden inverso antes de mostrar la estrella:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPath path = HendecagramArrayPage.HendecagramPath;
    SKRect pathBounds = path.Bounds;

    using (SKPaint fillPaint = new SKPaint())
    {
        fillPaint.Style = SKPaintStyle.Fill;

        float scale = Math.Min(info.Width / pathBounds.Width,
                               info.Height / pathBounds.Height);

        for (int i = 0; i <= 10; i++)
        {
            fillPaint.Color = new SKColor((byte)(255 * (10 - i) / 10),
                                          0,
                                          (byte)(255 * i / 10));
            canvas.Save();
            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(scale);
            canvas.Translate(-pathBounds.MidX, -pathBounds.MidY);
            canvas.DrawPath(path, fillPaint);
            canvas.Restore();

            scale *= 0.9f;
        }
    }
}
```

El código también muestra la estrella 10 veces más, cada vez que reduce el escalado factor 10% y progresivamente cambiando el color de rojo a azul:

[![](scale-images/isotropicscaling-small.png "Captura de pantalla de la página escala isótropo triple")](scale-images/isotropicscaling-large.png#lightbox "Triple captura de pantalla de la página escala isótropo")

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
