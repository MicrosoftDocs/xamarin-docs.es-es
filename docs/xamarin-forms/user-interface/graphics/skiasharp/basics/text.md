---
title: Integración de texto y gráficos
description: En este artículo se explica cómo determinar el tamaño de la cadena de texto representada para integrar el texto con gráficos de SkiaSharp en las aplicaciones de Xamarin.Forms y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B5AC82-7736-4AD8-AA16-FE43E18D203C
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: d23b4dbb97f4f98ff0361bb056e394bb7cbd941a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645642"
---
# <a name="integrating-text-and-graphics"></a>Integración de texto y gráficos

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Vea cómo determinar el tamaño de la cadena de texto representada para integrar el texto con gráficos de SkiaSharp_

En este artículo se muestra cómo medir el texto, escalar el texto a un tamaño determinado e integrar texto con otros gráficos:

![](text-images/textandgraphicsexample.png "Texto rodeado de rectángulos")

Esta imagen también incluye un rectángulo redondeado. El SkiaSharp `Canvas` clase incluye [ `DrawRect` ](xref:SkiaSharp.SKCanvas.DrawRect*) métodos para dibujar un rectángulo y [ `DrawRoundRect` ](xref:SkiaSharp.SKCanvas.DrawRoundRect*) métodos para dibujar un rectángulo con esquinas redondeadas. Estos métodos permiten el rectángulo que se define como un `SKRect` valor o de otras maneras.

El **texto enmarcado** página centros de una cadena de texto corta en la página y los bordes con un marco consta de un par de rectángulos redondeados. El [ `FramedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/FramedTextPage.cs) clase muestra cómo hacerlo.

En SkiaSharp, use el `SKPaint` clase para establecer los atributos de fuente y texto, pero también puede usar para obtener el tamaño del texto representado. El principio del siguiente `PaintSurface` controlador de eventos llama a dos diferentes `MeasureText` métodos. La primera [ `MeasureText` ](xref:SkiaSharp.SKPaint.MeasureText(System.String)) llamada tiene un sencillo `string` argumento y devuelve el ancho en píxeles del texto según los atributos de fuente actual. A continuación, el sistema calcula un nuevo `TextSize` propiedad de la `SKPaint` objeto basado en ese ancho representado, actual `TextSize` propiedad y el ancho del área de visualización. Este cálculo se pretende establecer `TextSize` para que el texto de cadena que se representará en el 90% del ancho de la pantalla:

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

El segundo [ `MeasureText` ](xref:SkiaSharp.SKPaint.MeasureText(System.String,SkiaSharp.SKRect@)) llamada tiene un `SKRect` argumento, por lo que obtienen un ancho y alto del texto representado. El `Height` propiedad de este `SKRect` valor depende de la presencia de letras mayúsculas, los ascendentes y descendentes de la cadena de texto. Diferentes `Height` valores se notifican para las cadenas de texto "mom", "cat" y "dog", por ejemplo.

El `Left` y `Top` propiedades de la `SKRect` estructura indican las coordenadas de la esquina superior izquierda del texto representado si el texto se muestra un `DrawText` llamar con las posiciones X e Y de 0. Por ejemplo, cuando se ejecuta este programa en un simulador de iPhone 7 `TextSize` se asigna el valor 90.6254 como resultado del cálculo siguiente la primera llamada a `MeasureText`. El `SKRect` valor obtenido de la segunda llamada a `MeasureText` tiene los siguientes valores de propiedad:

- `Left` = 6
- `Top` = &ndash;68
- `Width` = 664.8214
- `Height` = 88;

Tenga en cuenta que las coordenadas X e Y se pasa a la `DrawText` método especifica el lado izquierdo del texto en la línea base. El `Top` valor indica que el texto extiende 68 píxeles por encima de esa línea de base y (si se resta 68 TI desde 88) 20 píxeles por debajo de la línea base. El `Left` valor 6 indica que el texto comienza seis píxeles a la derecha del valor X de la `DrawText` llamar. Esto permite que el espaciado entre caracteres normales. Si desea mostrar el texto perfectamente en la esquina superior izquierda de la pantalla, pasar los valores negativos de estos `Left` y `Top` los valores de coordenadas X e Y de `DrawText`, en este ejemplo, &ndash;6 y 68.

El `SKRect` estructura define varias propiedades útiles y métodos, algunos de los cuales se usan en el resto de la `PaintSurface` controlador. El `MidX` y `MidY` valores indican las coordenadas del centro del rectángulo. (En el ejemplo de iPhone 7, estos valores son 338.4107 y &ndash;24.) El código siguiente usa estos valores para el cálculo más sencillo de coordenadas para centrar el texto de la pantalla:

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

El `SKImageInfo` también define la estructura de información de un [ `Rect` ](xref:SkiaSharp.SKImageInfo.Rect) propiedad de tipo `SKRect`, por lo que también se puede calcular `xText` y `yText` similar al siguiente:

```csharp
float xText = info.Rect.MidX - textBounds.MidX;
float yText = info.Rect.MidY - textBounds.MidY;
```

El `PaintSurface` controlador concluye con dos llamadas a `DrawRoundRect`, ambos de los cuales requieren argumentos de `SKRect`. Esto `SKRect` valor se basa en el `SKRect` valor obtenido el `MeasureText` método, pero no puede ser el mismo. En primer lugar, debe ser un poco más grande para que no se dibuja el rectángulo redondeado a través de los bordes del texto. En segundo lugar, necesita se indicará en el espacio para que la `Left` y `Top` valores corresponden a la esquina superior izquierda, donde es el rectángulo de colocarse. Estos dos trabajos se llevan a cabo por el [ `Offset` ](xref:SkiaSharp.SKRect.Offset*) y [ `Inflate` ](xref:SkiaSharp.SKRect.Inflate*) métodos definidos por `SKRect`:

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

A continuación, el resto del método es sencillo. Crea otro `SKPaint` objeto para los bordes y las llamadas `DrawRoundRect` dos veces. La segunda llamada usa un rectángulo inflado otro 10 píxeles. La primera llamada especifica un radio de redondeo de 20 píxeles. El segundo tiene un radio de 30 píxeles, por lo que parecen ser paralelo:

 [![](text-images/framedtext-small.png "Captura de pantalla de la página de texto enmarcado triple")](text-images/framedtext-large.png#lightbox "Triple captura de pantalla de la página de texto enmarcado")

Puede activar su teléfono o el simulador lateralmente para ver el texto y el marco aumentan de tamaño.

Si solo necesita algún texto en la pantalla del centro, puede hacerlo aproximadamente sin midiendo el texto. En su lugar, establezca el [ `TextAlign` ](xref:SkiaSharp.SKPaint.TextAlign) propiedad de `SKPaint` para el miembro de enumeración [ `SKTextAlign.Center` ](xref:SkiaSharp.SKTextAlign). La coordenada X que especifique en el `DrawText` método indica a continuación, donde se coloca el centro horizontal del texto. Si se pasa el punto medio de la pantalla para el `DrawText` método, el texto estará centrado horizontalmente y *casi* centrado verticalmente porque la línea de base se centrarán verticalmente.

Texto se puede tratar mucho como cualquier otro objeto gráfico. Es una opción sencilla mostrar el esquema de los caracteres de texto:

[![](text-images/outlinedtext-small.png "Captura de pantalla triple de la página de texto con contorno")](text-images/outlinedtext-large.png#lightbox "Triple screenshot of the Outlined Text page")

Esto se consigue cambiando el valor normal a `Style` propiedad de la `SKPaint` objeto de su valor predeterminado de `SKPaintStyle.Fill` a `SKPaintStyle.Stroke`y especificando un ancho de trazo. El `PaintSurface` controlador de la **texto con contorno** página muestra cómo hacerlo:

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

Otro objeto gráfica común es el mapa de bits. Que es un tema amplio que se tratan en profundidad en la sección [ **SkiaSharp Bitmaps**](../bitmaps/index.md), pero el siguiente artículo, [ **conceptos básicos de mapa de bits de SkiaSharp**](bitmaps.md), Proporciona una introducción más sencillo.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
