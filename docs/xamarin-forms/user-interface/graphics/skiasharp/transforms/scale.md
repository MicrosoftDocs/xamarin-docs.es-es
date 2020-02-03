---
title: Transformación de escala
description: En este artículo explora la transformación de escala de SkiaSharp para el escalado de objetos para distintos tamaños y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 54A43F3D-9DA8-44A7-9AE4-7E3025129A0B
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2017
ms.openlocfilehash: 7dc7a2faabef86aa63b52739edda696efcb54aba
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76724251"
---
# <a name="the-scale-transform"></a>Transformación de escala

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Detección de la transformación de escala SkiaSharp para escalar objetos a distintos tamaños_

Como ha visto en [**el artículo traducir transformación**](translate.md) , la transformación traducir puede trasladar un objeto gráfico de una ubicación a otra. En cambio, la transformación de escala cambia el tamaño del objeto gráfico:

![](scale-images/scaleexample.png "A tall word scaled in size")

La transformación de escala también a menudo hace que las coordenadas de gráficos mover la medida que se hacen más grandes.

Antes vio dos fórmulas de transformación que describen los efectos de los factores de traducción de `dx` y `dy`:

x' = x + dx

y' = y + dy

Los factores de escala de `sx` y `sy` son multiplicativos en lugar de aditivos:

x' = sx (en inglés) x

y' = sy (en inglés) y

Los valores predeterminados de los factores de traslación están 0; los valores predeterminados de los factores de escala son 1.

La clase `SKCanvas` define cuatro métodos de `Scale`. El primer método [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single)) es para los casos en los que desea el mismo factor de escala horizontal y vertical:

```csharp
public void Scale (Single s)
```

Esto se conoce como escalado *anisotrópico* &mdash; escalado que es el mismo en ambas direcciones. Escalado isótropo conserva la relación de aspecto del objeto.

El segundo método [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single)) permite especificar valores diferentes para el escalado horizontal y vertical:

```csharp
public void Scale (Single sx, Single sy)
```

Esto da como resultado el escalado *anisotrópico* .
El tercer método [`Scale`](xref:SkiaSharp.SKCanvas.Scale(SkiaSharp.SKPoint)) combina los dos factores de escala en un único valor de `SKPoint`:

```csharp
public void Scale (SKPoint size)
```

El cuarto método `Scale` se describirá en breve.

En la página **escala básico** se muestra el método `Scale`. El archivo [**BasicScalePage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml) contiene dos elementos `Slider` que permiten seleccionar los factores de escala horizontal y vertical entre 0 y 10. El archivo de código subyacente [**BasicScalePage.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs) usa esos valores para llamar `Scale` antes de mostrar un rectángulo redondeado con una línea discontinua y ajustarlo a un texto en la esquina superior izquierda del lienzo:

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

Puede que se pregunte: ¿Cómo afectan los factores de escala al valor devuelto del método `MeasureText` de `SKPaint`? La respuesta es: no en absoluto. `Scale` es un método de `SKCanvas`. No afecta a todo lo que haga con un objeto de `SKPaint` hasta que use ese objeto para representar algo en el lienzo.

Como puede ver, todo lo dibujado después de la llamada a `Scale` aumenta proporcionalmente:

[![](scale-images/basicscale-small.png "Triple screenshot of the Basic Scale page")](scale-images/basicscale-large.png#lightbox "Triple screenshot of the Basic Scale page")

El texto, el ancho de la línea discontinua, la longitud de los guiones en esa línea, el redondeo de las esquinas y en el margen de 10 píxeles entre los bordes superiores e izquierdos del lienzo y el rectángulo redondeado son todos los sujetos a los mismos factores de escala.

> [!IMPORTANT]
> La plataforma Universal de Windows no se representará correctamente texto escalado anisotropicly.

Anisotrópico escalado hace que el ancho del trazo para convertirse en diferentes para las líneas alineado con los ejes horizontales y vertical. (Esto también es evidente en la primera imagen de esta página). Si no desea que el ancho del trazo se vea afectado por los factores de escala, establézcalo en 0 y siempre tendrá un ancho de píxel, independientemente del valor de `Scale`.

El escalado es relativo a la esquina superior izquierda del lienzo. Esto podría ser exactamente lo que desea, pero no es posible. Suponga que desea colocar el texto y el rectángulo en otro lugar en el lienzo y desea ajustar la escala en relación con su centro. En ese caso, puede usar la cuarta versión del método [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) , que incluye dos parámetros adicionales para especificar el centro del escalado:

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

Los parámetros `px` y `py` definen un punto que a veces se denomina *centro de escalado* , pero en la documentación de SkiaSharp se conoce como *punto de pivote*. Este es un punto con respecto a la esquina superior izquierda del lienzo que no se ve afectado por el ajuste de escala. Los ajustes de escala se produce relativa a dicho centro.

La página [**escala centrada**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs) muestra cómo funciona esto. El controlador de `PaintSurface` es similar al programa de **escala básico** , salvo que el valor de `margin` se calcula para centrar el texto horizontalmente, lo que implica que el programa funciona mejor en el modo vertical:

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

La esquina superior izquierda del rectángulo redondeado se coloca `margin` píxeles desde la izquierda del lienzo y `margin` píxeles desde la parte superior. Los dos últimos argumentos del método `Scale` se establecen en esos valores más el ancho y el alto del texto, que también es el ancho y el alto del rectángulo redondeado. Esto significa que los ajustes de escala en relación con el centro del rectángulo:

[![](scale-images/centeredscale-small.png "Triple screenshot of the Centered Scale page")](scale-images/centeredscale-large.png#lightbox "Triple screenshot of the Centered Scale page")

Los elementos `Slider` de este programa tienen un intervalo de &ndash;de 10 a 10. Como puede ver, los valores negativos (por ejemplo en Android, pantalla en el centro) escalado vertical hará que los objetos se voltea alrededor del eje horizontal que pasa a través del centro de escalado. Los valores negativos (tal como se muestra en la pantalla UWP a la derecha) el escalado horizontal hará que los objetos se voltea alrededor del eje vertical que pasa a través del centro de escalado.

La versión del método [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) con puntos dinámicos es un acceso directo para una serie de tres llamadas a `Translate` y `Scale`. Para ver cómo funciona esto, reemplace el método `Scale` en la página **escala centrada** por lo siguiente:

```csharp
canvas.Translate(-px, -py);
```

Estos son los valores negativos de las coordenadas del punto de pivote.

Ejecute el programa otra vez. Verá que el rectángulo y el texto se desplazan para que el centro está en la esquina superior izquierda del lienzo. Apenas puede verlo. Los controles deslizantes no funcionan supuesto porque ahora el programa no aumenta en absoluto.

Ahora agregue la llamada `Scale` básica (sin un centro de escalado) *antes* de que `Translate` llame a:

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Si está familiarizado con este ejercicio en otros que sistemas de programación de gráficos, se podría pensar que es incorrecto, pero no lo es. Skia controla llamadas sucesivas transformación un poco diferente de lo que podría estar familiarizado.

Con las llamadas sucesivas `Scale` y `Translate`, el centro del rectángulo redondeado todavía está en la esquina superior izquierda, pero ahora se puede escalar con relación a la esquina superior izquierda del lienzo, que también es el centro del rectángulo redondeado.

Ahora, antes de que `Scale` llame a, agregue otra llamada `Translate` con los valores de centro:

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

Al combinar `Translate` y `Scale` llamadas, el orden es importante. Si el `Translate` viene después del `Scale`, los factores de conversión se escalan eficazmente mediante los factores de escala. Si el `Translate` viene antes del `Scale`, no se escalan los factores de traducción. Este proceso se vuelve un poco más claro (aunque más matemáticos) cuando se especifica el asunto de matrices de transformación.

La clase `SKPath` define una propiedad de [`Bounds`](xref:SkiaSharp.SKPath.Bounds) de solo lectura que devuelve un `SKRect` que define la extensión de las coordenadas de la ruta de acceso. Por ejemplo, cuando se obtiene la propiedad `Bounds` de la ruta de acceso hendecagram creada anteriormente, las propiedades `Left` y `Top` del rectángulo son aproximadamente – 100, las propiedades `Right` y `Bottom` son aproximadamente 100 y las propiedades `Width` y `Height` son aproximadamente 200. (La mayoría de los valores reales es un poco menor porque los puntos de las estrellas se definen por un círculo con un radio de 100, pero solo el punto superior es paralelo con los ejes horizontales o verticales).

La disponibilidad de esta información implica que debe ser posible derivar la escala y traducir factores adecuados para el escalado de una ruta de acceso para el tamaño del lienzo. La página de [**escalado de anisotrópico**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs) muestra esto con una estrella de 11 puntas. Una escala *anisotrópico* significa que no es igual en las direcciones horizontal y vertical, lo que significa que la estrella no conservará su relación de aspecto original. Este es el código relevante en el controlador de `PaintSurface`:

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

El rectángulo `pathBounds` se obtiene cerca de la parte superior de este código y, después, se usa más adelante con el ancho y el alto del lienzo en la llamada `Scale`. Esa llamada por sí misma escalará las coordenadas de la ruta de acceso cuando se representen mediante la llamada `DrawPath` pero la estrella se centrará en la esquina superior derecha del lienzo. Debe desplazar hacia abajo y a la izquierda. Este es el trabajo de la llamada `Translate`. Esas dos propiedades de `pathBounds` son aproximadamente – 100, por lo que los factores de traducción son aproximadamente 100. Dado que la llamada a `Translate` es posterior a la llamada `Scale`, esos valores se escalan eficazmente mediante los factores de escala, por lo que mueven el centro de la estrella al centro del lienzo:

[![](scale-images/anisotropicscaling-small.png "Triple screenshot of the Anisotropic Scaling page")](scale-images/anisotropicscaling-large.png#lightbox "Triple screenshot of the Anisotropic Scaling page")

Otra forma de pensar en las llamadas `Scale` y `Translate` es determinar el efecto en la secuencia inversa: la llamada a `Translate` desplaza la ruta de acceso para que se vuelva totalmente visible pero esté orientada en la esquina superior izquierda del lienzo. A continuación, el método `Scale` hace que esa estrella sea más grande en relación con la esquina superior izquierda.

En realidad, parece que el asterisco es un poco mayor que el lienzo. El problema es el ancho del trazo. La propiedad `Bounds` de `SKPath` indica las dimensiones de las coordenadas codificadas en la ruta de acceso y eso es lo que el programa utiliza para escalarla. Cuando se representa la ruta de acceso con un ancho de trazo determinado, la ruta de acceso representada es mayor que el lienzo.

Para corregir este problema, que es necesario para compensar que. Un enfoque sencillo en este programa es agregar la siguiente instrucción justo antes de la llamada a `Scale`:

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

Esto aumenta el `pathBounds` rectángulo en unidades 1,5 en los cuatro lados. Se trata de una solución razonable solo cuando se redondea a la combinación de trazo. Una unión angular puede ser más larga y difícil de calcular.

También puede usar una técnica similar con el texto, como se muestra en la página de **texto anisotrópico** . Esta es la parte relevante del controlador de `PaintSurface` de la clase [`AnisotropicTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs) :

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

Es una lógica similar y el texto se expande hasta el tamaño de la página en función del rectángulo de límite de texto devuelto de `MeasureText` (que es un poco mayor que el texto real):

[![](scale-images/anisotropictext-small.png "Triple screenshot of the Anisotropic Test page")](scale-images/anisotropictext-large.png#lightbox "Triple screenshot of the Anisotropic Test page")

Si necesita conservar la relación de aspecto de los objetos gráficos, desea usar el escalado isótropo. La página de **escalado de anisotrópico** muestra esto para la estrella de 11 puntas. Conceptualmente, los pasos para mostrar un objeto gráfico en el centro de la página con un escalado isótropo son:

- Traducir el centro del objeto gráfico en la esquina superior izquierda.
- Escalar el objeto basándose en el valor mínimo de las dimensiones de página horizontales y verticales dividido entre las dimensiones del objeto gráfico.
- Traducir el centro del objeto escalado hasta el centro de la página.

El [`IsotropicScalingPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs) realiza estos pasos en orden inverso antes de mostrar la estrella:

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

[![](scale-images/isotropicscaling-small.png "Triple screenshot of the Isotropic Scaling page")](scale-images/isotropicscaling-large.png#lightbox "Triple screenshot of the Isotropic Scaling page")

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
