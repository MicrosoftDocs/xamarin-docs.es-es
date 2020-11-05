---
title: Integración de texto y gráficos
description: En este artículo se explica cómo determinar el tamaño de la cadena de texto representada para integrar texto con gráficos de SkiaSharp en Xamarin.Forms aplicaciones y cómo hacerlo con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B5AC82-7736-4AD8-AA16-FE43E18D203C
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fdc529e6b5f1678bd26b40a17b5e0c5257a6dc0e
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368599"
---
# <a name="integrating-text-and-graphics"></a>Integración de texto y gráficos

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Vea cómo determinar el tamaño de la cadena de texto representada para integrar texto con gráficos de SkiaSharp_

En este artículo se muestra cómo medir texto, escalar el texto a un tamaño determinado e integrar texto con otros gráficos:

![Texto rodeado por rectángulos](text-images/textandgraphicsexample.png)

Esa imagen también incluye un rectángulo redondeado. La `Canvas` clase SkiaSharp incluye [`DrawRect`](xref:SkiaSharp.SKCanvas.DrawRect*) métodos para dibujar un rectángulo y [`DrawRoundRect`](xref:SkiaSharp.SKCanvas.DrawRoundRect*) métodos para dibujar un rectángulo con esquinas redondeadas. Estos métodos permiten definir el rectángulo como un `SKRect` valor o de otras maneras.

La página de **texto con Marcos** centra una cadena de texto corta en la página y la rodea con un marco compuesto por un par de rectángulos redondeados. La [`FramedTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/FramedTextPage.cs) clase muestra cómo se hace.

En SkiaSharp, se usa la `SKPaint` clase para establecer los atributos de texto y de fuente, pero también se puede utilizar para obtener el tamaño representado del texto. El inicio del siguiente `PaintSurface` controlador de eventos llama a dos `MeasureText` métodos diferentes. La primera [`MeasureText`](xref:SkiaSharp.SKPaint.MeasureText(System.String)) llamada tiene un `string` argumento simple y devuelve el ancho en píxeles del texto basándose en los atributos de fuente actuales. Después, el programa calcula una nueva `TextSize` propiedad del `SKPaint` objeto basándose en el ancho representado, la propiedad actual `TextSize` y el ancho del área de presentación. Este cálculo está pensado para establecerse de `TextSize` modo que la cadena de texto se represente al 90% del ancho de la pantalla:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string str = "Hello SkiaSharp!";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Chocolate
    };

    // Adjust TextSize property so text is 90% of screen width
    float textWidth = textPaint.MeasureText(str);
    textPaint.TextSize = 0.9f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds = new SKRect();
    textPaint.MeasureText(str, ref textBounds);
    ...
}
```

La segunda [`MeasureText`](xref:SkiaSharp.SKPaint.MeasureText(System.String,SkiaSharp.SKRect@)) llamada tiene un `SKRect` argumento, por lo que obtiene el ancho y el alto del texto representado. La `Height` propiedad de este `SKRect` valor depende de la presencia de mayúsculas, ascendentes y descendentes en la cadena de texto. `Height`Por ejemplo, se muestran valores diferentes para las cadenas de texto "Mom", "cat" y "Dog".

Las `Left` `Top` propiedades y de la `SKRect` estructura indican las coordenadas de la esquina superior izquierda del texto representado si el texto se muestra mediante una `DrawText` llamada con las posiciones X e y de 0. Por ejemplo, cuando se ejecuta este programa en un simulador de iPhone 7, `TextSize` se le asigna el valor 90,6254 como resultado del cálculo que sigue a la primera llamada a `MeasureText` . El `SKRect` valor obtenido de la segunda llamada a `MeasureText` tiene los siguientes valores de propiedad:

- `Left` = 6
- `Top` = &ndash;68
- `Width` = 664,8214
- `Height` = 88;

Tenga en cuenta que las coordenadas X e Y que se pasan al `DrawText` método especifican el lado izquierdo del texto en la línea base. El `Top` valor indica que el texto extiende 68 píxeles por encima de esa línea de base y (restando 68 de 88) 20 píxeles por debajo de la línea de base. El `Left` valor de 6 indica que el texto comienza en seis píxeles a la derecha del valor X de la `DrawText` llamada. Esto permite un espaciado entre caracteres normal. Si desea mostrar el texto de forma correcta en la esquina superior izquierda de la pantalla, pase los negativos de estos `Left` `Top` valores y como las coordenadas X e y de `DrawText` , en este ejemplo, &ndash; 6 y 68.

La `SKRect` estructura define varias propiedades y métodos útiles, algunos de los cuales se usan en el resto del `PaintSurface` controlador. Los `MidX` `MidY` valores y indican las coordenadas del centro del rectángulo. (En el ejemplo de iPhone 7, esos valores son 338,4107 y &ndash; 24). El código siguiente usa estos valores para el cálculo más sencillo de las coordenadas para centrar el texto en la pantalla:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(str, xText, yText, textPaint);
    ...
}
```

La `SKImageInfo` estructura info también define una [`Rect`](xref:SkiaSharp.SKImageInfo.Rect) propiedad de tipo `SKRect` , por lo que también puede calcular `xText` y `yText` como se indica a continuación:

```csharp
float xText = info.Rect.MidX - textBounds.MidX;
float yText = info.Rect.MidY - textBounds.MidY;
```

El `PaintSurface` controlador concluye con dos llamadas a `DrawRoundRect` , que requieren argumentos de `SKRect` . Este `SKRect` valor se basa en el `SKRect` valor obtenido del `MeasureText` método, pero no puede ser el mismo. En primer lugar, debe ser un poco mayor para que el rectángulo redondeado no dibuje sobre los bordes del texto. En segundo lugar, debe desplazarse en el espacio para que los `Left` valores y se `Top` correspondan con la esquina superior izquierda donde se va a colocar el rectángulo. Estos dos trabajos se logran mediante [`Offset`](xref:SkiaSharp.SKRect.Offset*) los [`Inflate`](xref:SkiaSharp.SKRect.Inflate*) métodos y definidos por `SKRect` :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Create a new SKRect object for the frame around the text
    SKRect frameRect = textBounds;
    frameRect.Offset(xText, yText);
    frameRect.Inflate(10, 10);

    // Create an SKPaint object to display the frame
    SKPaint framePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 5,
        Color = SKColors.Blue
    };

    // Draw one frame
    canvas.DrawRoundRect(frameRect, 20, 20, framePaint);

    // Inflate the frameRect and draw another
    frameRect.Inflate(10, 10);
    framePaint.Color = SKColors.DarkBlue;
    canvas.DrawRoundRect(frameRect, 30, 30, framePaint);
}
```

A continuación, el resto del método es directa. Crea otro `SKPaint` objeto para los bordes y llama `DrawRoundRect` dos veces. La segunda llamada usa un rectángulo inflado por otros 10 píxeles. La primera llamada especifica un radio de esquina de 20 píxeles. El segundo tiene un radio de esquina de 30 píxeles, por lo que parece ser paralelo:

 [![Captura de pantalla triple de la página texto con marco](text-images/framedtext-small.png)](text-images/framedtext-large.png#lightbox "Captura de pantalla triple de la página texto con marco")

Puede girar el teléfono o el simulador lateralmente para ver el tamaño de los fotogramas y el texto.

Si solo necesita centrar texto en la pantalla, puede hacerlo aproximadamente sin medir el texto. En su lugar, establezca la [`TextAlign`](xref:SkiaSharp.SKPaint.TextAlign) propiedad de `SKPaint` en el miembro de la enumeración [`SKTextAlign.Center`](xref:SkiaSharp.SKTextAlign) . La coordenada X que especifique en el `DrawText` método indicará dónde se coloca el centro horizontal del texto. Si pasa el punto medio de la pantalla al `DrawText` método, el texto se centrará horizontalmente y se centrará *casi* verticalmente, ya que la línea base se centrará verticalmente.

El texto se puede tratar de forma muy similar a cualquier otro objeto gráfico. Una opción sencilla es mostrar el contorno de los caracteres de texto:

[![Captura de pantalla triple de la página de texto esquematizada](text-images/outlinedtext-small.png)](text-images/outlinedtext-large.png#lightbox "Captura de pantalla triple de la página de texto esquematizada")

Esto se consigue simplemente cambiando la propiedad normal `Style` del `SKPaint` objeto de su configuración predeterminada de `SKPaintStyle.Fill` a `SKPaintStyle.Stroke` y especificando un ancho de trazo. El `PaintSurface` controlador de la página de **texto esquematizada** muestra cómo se hace:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string text = "OUTLINE";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 1,
        FakeBoldText = true,
        Color = SKColors.Blue
    };

    // Adjust TextSize property so text is 95% of screen width
    float textWidth = textPaint.MeasureText(text);
    textPaint.TextSize = 0.95f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds = new SKRect();
    textPaint.MeasureText(text, ref textBounds);

    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(text, xText, yText, textPaint);
}
```

Otro objeto gráfico común es el mapa de bits. Este es un tema de gran tamaño que se describe en profundidad en la sección [**SkiaSharp mapas de bits**](../bitmaps/index.md), pero en el siguiente artículo, [**aspectos básicos de mapas de bits en SkiaSharp**](bitmaps.md), proporciona una introducción más breve.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)