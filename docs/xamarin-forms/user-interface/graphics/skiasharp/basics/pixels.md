---
title: Píxeles y unidades independientes del dispositivo
description: En este artículo se exploran las diferencias entre las coordenadas de SkiaSharp y las Xamarin.Forms coordenadas, y se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 26C25BB8-FBE8-4B77-B01D-16A163A16890
author: davidbritch
ms.author: dabritch
ms.date: 02/09/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0c1ae9e05b6671d45d8df485a89cfc0dea86632d
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937312"
---
# <a name="pixels-and-device-independent-units"></a>Píxeles y unidades independientes del dispositivo

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explorar las diferencias entre las coordenadas de SkiaSharp y las Xamarin.Forms coordenadas_

En este artículo se exploran las diferencias en el sistema de coordenadas usado en SkiaSharp y Xamarin.Forms . Puede obtener información para convertir entre los dos sistemas de coordenadas y también dibujar gráficos que rellenan un área determinada:

![Un óvalo que rellena la pantalla](pixels-images/screenfillexample.png)

Si ha estado programando durante Xamarin.Forms un tiempo, es posible que tenga una idea para las Xamarin.Forms coordenadas y los tamaños. Es posible que los círculos dibujados en los dos artículos anteriores le parezcan algo pequeños.

Esos círculos *son* pequeños en comparación con los Xamarin.Forms tamaños. De forma predeterminada, SkiaSharp dibuja en unidades de píxeles mientras Xamarin.Forms que basa las coordenadas y los tamaños en una unidad independiente del dispositivo establecida por la plataforma subyacente. (Puede encontrar más información sobre el Xamarin.Forms sistema de coordenadas en el [capítulo 5. Trabajar con tamaños](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter05.md) del libro *crear Mobile Apps con Xamarin.Forms *).

La página del programa [**SkewSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) titulado **tamaño de superficie** usa la salida de texto SkiaSharp para mostrar el tamaño de la superficie de presentación de tres orígenes diferentes:

- Xamarin.Forms [`Width`](xref:Xamarin.Forms.VisualElement.Width) Propiedades y normales [`Height`](xref:Xamarin.Forms.VisualElement.Height) del `SKCanvasView` objeto.
- [`CanvasSize`](xref:SkiaSharp.Views.Forms.SKCanvasView.CanvasSize)Propiedad del `SKCanvasView` objeto.
- [`Size`](xref:SkiaSharp.SKImageInfo.Size)Propiedad del `SKImageInfo` valor, que es coherente con las `Width` `Height` propiedades y utilizadas en las dos páginas anteriores.

La [`SurfaceSizePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SurfaceSizePage.cs) clase muestra cómo mostrar estos valores. El constructor guarda el `SKCanvasView` objeto como un campo, por lo que se puede tener acceso a él en el `PaintSurface` controlador de eventos:

```csharp
SKCanvasView canvasView;

public SurfaceSizePage()
{
    Title = "Surface Size";

    canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

`SKCanvas`incluye seis `DrawText` métodos diferentes, pero este [`DrawText`](xref:SkiaSharp.SKCanvas.DrawText(System.String,System.Single,System.Single,SkiaSharp.SKPaint)) método es el más sencillo:

```csharp
public void DrawText (String text, Single x, Single y, SKPaint paint)
```

Especifique la cadena de texto, las coordenadas X e y donde va a comenzar el texto y un `SKPaint` objeto. La coordenada X especifica dónde se coloca el lado izquierdo del texto, pero ve: la coordenada Y especifica la posición de la *línea base* del texto. Si alguna vez ha escrito a mano en papel con renglones, la línea de base es la línea en la que se encuentran los caracteres y, por debajo, los descendentes (como los de las letras g, p, q e y) descienden.

El `SKPaint` objeto le permite especificar el color del texto, la familia de fuentes y el tamaño del texto. De forma predeterminada, la [`TextSize`](xref:SkiaSharp.SKPaint.TextSize) propiedad tiene un valor de 12, lo que da como resultado un pequeño texto en dispositivos de alta resolución como, por ejemplo, teléfonos. En cualquier cosa, excepto en las aplicaciones más sencillas, también necesitará cierta información sobre el tamaño del texto que se muestra. La `SKPaint` clase define una [`FontMetrics`](xref:SkiaSharp.SKPaint.FontMetrics) propiedad y varios [`MeasureText`](xref:SkiaSharp.SKPaint.MeasureText(System.String)) métodos, pero para las necesidades menos sofisticadas, la [`FontSpacing`](xref:SkiaSharp.SKPaint.FontSpacing) propiedad proporciona un valor recomendado para el espaciado de líneas de texto sucesivas.

El siguiente `PaintSurface` controlador crea un `SKPaint` objeto para un `TextSize` de 40 píxeles, que es el alto vertical deseado del texto desde la parte superior de los ascendentes hasta la parte inferior de los descendentes. El `FontSpacing` valor `SKPaint` devuelto por el objeto es un poco mayor que, aproximadamente 47 píxeles.

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 40
    };

    float fontSpacing = paint.FontSpacing;
    float x = 20;               // left margin
    float y = fontSpacing;      // first baseline
    float indent = 100;

    canvas.DrawText("SKCanvasView Height and Width:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(String.Format("{0:F2} x {1:F2}",
                                  canvasView.Width, canvasView.Height),
                    x + indent, y, paint);
    y += fontSpacing * 2;
    canvas.DrawText("SKCanvasView CanvasSize:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(canvasView.CanvasSize.ToString(), x + indent, y, paint);
    y += fontSpacing * 2;
    canvas.DrawText("SKImageInfo Size:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(info.Size.ToString(), x + indent, y, paint);
}
```

El método comienza la primera línea de texto con una coordenada X de 20 (para un pequeño margen a la izquierda) y una coordenada Y de `fontSpacing` , que es un poco más de lo necesario para mostrar el alto completo de la primera línea de texto en la parte superior de la superficie de la pantalla. Después de cada llamada a `DrawText` , la coordenada Y aumenta en uno o dos incrementos de `fontSpacing` .

Esta es la ejecución del programa:

[![Captura de pantalla triple de la página tamaño de la superficie](pixels-images/surfacesize-small.png)](pixels-images/surfacesize-large.png#lightbox "Captura de pantalla triple de la página tamaño de la superficie")

Como puede ver, la `CanvasSize` propiedad de `SKCanvasView` y la `Size` propiedad del `SKImageInfo` valor son coherentes en la notificación de las dimensiones de píxeles. Las `Height` `Width` propiedades y de `SKCanvasView` son Xamarin.Forms propiedades y notifican el tamaño de la vista en las unidades independientes del dispositivo definidas por la plataforma.

El simulador de siete de iOS de la izquierda tiene dos píxeles por unidad independiente del dispositivo y el nexo de Android 5 en el centro tiene tres píxeles por unidad. Este es el motivo por el que el círculo simple mostrado anteriormente tiene tamaños diferentes en distintas plataformas.

Si prefiere trabajar completamente en unidades independientes del dispositivo, puede hacerlo si establece la `IgnorePixelScaling` propiedad de `SKCanvasView` en `true` . Sin embargo, es posible que no le gusten los resultados. SkiaSharp representa los gráficos en una superficie de dispositivo más pequeña, con un tamaño de píxel igual al tamaño de la vista en unidades independientes del dispositivo. (Por ejemplo, SkiaSharp usaría una superficie de presentación de 360 x 512 píxeles en el nexo 5). A continuación, escala la imagen en tamaño, lo que da lugar a un jaggies de mapas de bits apreciable.

Para mantener la misma resolución de imagen, una solución mejor es escribir sus propias funciones sencillas para convertir entre los dos sistemas de coordenadas.

Además del `DrawCircle` método, `SKCanvas` también define dos `DrawOval` métodos que dibujan una elipse. Una elipse se define mediante dos radios en lugar de un radio único. Estos se conocen como el *radio principal* y el *radio secundario*. El `DrawOval` método dibuja una elipse con los dos radios paralelos a los ejes X e y. (Si necesita dibujar una elipse con ejes que no son paralelos a los ejes X e y, puede usar una transformación de giro como se describe en el artículo [**la transformación girar**](../transforms/rotate.md) o una ruta de acceso de gráficos, como se describe en el artículo [**tres formas de dibujar un arco**](../curves/arcs.md)). Esta sobrecarga del [`DrawOval`](xref:SkiaSharp.SKCanvas.DrawOval(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) método nombra los dos parámetros de radios `rx` y `ry` para indicar que son paralelos a los ejes X e y:

```csharp
public void DrawOval (Single cx, Single cy, Single rx, Single ry, SKPaint paint)
```

¿Es posible dibujar una elipse que rellene la superficie de la pantalla? En la página **relleno de puntos suspensivos** se muestra cómo. El `PaintSurface` controlador de eventos de la clase [**EllipseFillPage.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/EllipseFillPage.xaml.cs) resta la mitad del ancho del trazo de `xRadius` los `yRadius` valores y para ajustarse a la elipse completa y su contorno dentro de la superficie de la pantalla:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float strokeWidth = 50;
    float xRadius = (info.Width - strokeWidth) / 2;
    float yRadius = (info.Height - strokeWidth) / 2;

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = strokeWidth
    };
    canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);
}
```

Aquí se ejecuta:

[![Captura de pantalla triple de la página tamaño de la superficie](pixels-images/ellipsefill-small.png)](pixels-images/ellipsefill-large.png#lightbox "Captura de pantalla triple de la página tamaño de la superficie")

El otro [`DrawOval`](xref:SkiaSharp.SKCanvas.DrawOval(SkiaSharp.SKRect,SkiaSharp.SKPaint)) método tiene un [`SKRect`](xref:SkiaSharp.SKRect) argumento, que es un rectángulo definido en términos de las coordenadas X e y de la esquina superior izquierda y la esquina inferior derecha. La elipse rellena ese rectángulo, lo que sugiere que podría ser posible usarlo en la página de **relleno de la elipse** de la siguiente manera:

```csharp
SKRect rect = new SKRect(0, 0, info.Width, info.Height);
canvas.DrawOval(rect, paint);
```

Sin embargo, se truncan todos los bordes del contorno de la elipse en los cuatro lados. Debe ajustar todos los argumentos de `SKRect` constructor basados en para que `strokeWidth` este trabajo sea correcto:

```csharp
SKRect rect = new SKRect(strokeWidth / 2,
                         strokeWidth / 2,
                         info.Width - strokeWidth / 2,
                         info.Height - strokeWidth / 2);
canvas.DrawOval(rect, paint);
```

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
