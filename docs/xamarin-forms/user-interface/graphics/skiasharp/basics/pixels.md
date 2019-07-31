---
title: Píxeles y unidades independientes del dispositivo
description: En este artículo se explora las diferencias entre las coordenadas de SkiaSharp e Xamarin.Forms y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 26C25BB8-FBE8-4B77-B01D-16A163A16890
author: davidbritch
ms.author: dabritch
ms.date: 02/09/2017
ms.openlocfilehash: e2bf493a5d8a4197fbc59044edf126761b41cf8d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649725"
---
# <a name="pixels-and-device-independent-units"></a>Píxeles y unidades independientes del dispositivo

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explore las diferencias entre las coordenadas de SkiaSharp e Xamarin.Forms_

En este artículo se explora las diferencias en el sistema de coordenadas que se usan en SkiaSharp y Xamarin.Forms. Puede obtener información para convertir entre los dos sistemas de coordenadas y también dibujar gráficos que rellenan un área determinada:

![](pixels-images/screenfillexample.png "Una elipse que ocupe toda la pantalla")

Si ha estado programando en Xamarin.Forms durante un tiempo, podría tener una idea acerca de las coordenadas de Xamarin.Forms y tamaños. Los círculos dibujados en los dos artículos anteriores pueden parecer un poco más pequeños para usted.

Esos círculos *son* pequeño en comparación con los tamaños de Xamarin.Forms. De forma predeterminada, SkiaSharp dibuja en unidades de píxeles mientras Xamarin.Forms basará coordenadas y los tamaños en una unidad independiente del dispositivo establecida por la plataforma subyacente. (Encontrará más información sobre el sistema de coordenadas de Xamarin.Forms en [capítulo 5. Tratar con tamaños](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter05.md) del libro *Creating Mobile Apps with Xamarin.Forms*.)

La página en el [ **SkewSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) programa titulada **superficie tamaño** utiliza la salida de texto de SkiaSharp para mostrar el tamaño de la superficie de pantalla de tres orígenes diferentes:

- Xamarin.Forms normal [ `Width` ](xref:Xamarin.Forms.VisualElement.Width) y [ `Height` ](xref:Xamarin.Forms.VisualElement.Height) propiedades de la `SKCanvasView` objeto.
- El [ `CanvasSize` ](xref:SkiaSharp.Views.Forms.SKCanvasView.CanvasSize) propiedad de la `SKCanvasView` objeto.
- El [ `Size` ](xref:SkiaSharp.SKImageInfo.Size) propiedad de la `SKImageInfo` valor, que es coherente con la `Width` y `Height` propiedades utilizadas en las dos páginas anteriores.

El [ `SurfaceSizePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SurfaceSizePage.cs) clase muestra cómo mostrar estos valores. El constructor de guarda el `SKCanvasView` objeto como un campo, para que se pueda acceder en el `PaintSurface` controlador de eventos:

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

`SKCanvas` incluye seis diferentes `DrawText` métodos, pero esto [ `DrawText` ](xref:SkiaSharp.SKCanvas.DrawText(System.String,System.Single,System.Single,SkiaSharp.SKPaint)) método es el más sencillo:

```csharp
public void DrawText (String text, Single x, Single y, SKPaint paint)
```

Especifica la cadena de texto, las coordenadas X e Y donde el texto se va a comenzar y un `SKPaint` objeto. La coordenada X especifica dónde se coloca el lado izquierdo del texto, pero se ve: La coordenada Y especifica la posición de la *línea base* del texto. Si alguna vez ha escrito a mano en papel con líneas, la línea base es la línea en qué sit caracteres, y a continuación descienden de los trazos descendentes (como los de las letras g, p, q e y).

La `SKPaint` objeto le permite especificar el color del texto, la familia de fuentes y el tamaño del texto. De forma predeterminada, el [ `TextSize` ](xref:SkiaSharp.SKPaint.TextSize) propiedad tiene un valor de 12, lo que resulta en minúsculo texto en dispositivos como teléfonos de alta resolución. En ningún sitio salvo las aplicaciones más sencillas, también necesitará cierta información sobre el tamaño del texto que se van a mostrar. El `SKPaint` clase define un [ `FontMetrics` ](xref:SkiaSharp.SKPaint.FontMetrics) propiedad y varios [ `MeasureText` ](xref:SkiaSharp.SKPaint.MeasureText(System.String)) métodos, pero para las necesidades de menos elegantes, el [ `FontSpacing` ](xref:SkiaSharp.SKPaint.FontSpacing) propiedad proporciona un valor recomendado para las líneas sucesivas de espaciado del texto.

La siguiente `PaintSurface` controlador crea una `SKPaint` de objeto para un `TextSize` de 40 píxeles, que es el alto deseado vertical del texto de la parte superior de los ascendentes para la parte inferior de los trazos descendentes. El `FontSpacing` valor que el `SKPaint` objeto devuelve un poco mayor que, acerca de 47 píxeles.

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

El método comienza la primera línea de texto con una coordenada X de 20 (para un pequeño margen a la izquierda) y una coordenada Y de `fontSpacing`, que es un poco más que lo que es necesario para mostrar el alto de la primera línea de texto completo en la parte superior de la superficie de pantalla. Después de cada llamada a `DrawText`, la coordenada Y se incrementa en uno o dos incrementos de `fontSpacing`.

Este es el programa que se ejecuta:

[![](pixels-images/surfacesize-small.png "Captura de pantalla triple de la página de tamaño de la superficie")](pixels-images/surfacesize-large.png#lightbox "Triple captura de pantalla de la página de tamaño de la superficie")

Como puede ver, el `CanvasSize` propiedad de la `SKCanvasView` y `Size` propiedad de la `SKImageInfo` valor son coherentes en el informe de las dimensiones en píxeles. El `Height` y `Width` propiedades de la `SKCanvasView` son propiedades de Xamarin.Forms y el tamaño de la vista en las unidades independientes del dispositivo definidos por la plataforma de informes.

El simulador de iOS siete de la izquierda tiene dos píxeles por unidad independiente del dispositivo y el Android Nexus 5 en el centro de tiene tres píxeles por unidad. Por eso el círculo simple mostrado anteriormente tiene distintos tamaños en diferentes plataformas.

Si prefiere trabajar completamente en unidades independientes del dispositivo, puede hacerlo estableciendo la `IgnorePixelScaling` propiedad de la `SKCanvasView` a `true`. Sin embargo, es posible que no le gustan los resultados. SkiaSharp presenta los gráficos en una superficie de dispositivo más pequeño, con un tamaño de píxel igual al tamaño de la vista en unidades independientes del dispositivo. (Por ejemplo, SkiaSharp usaría una superficie de pantalla de 360 x 512 píxeles en la Nexus 5.) A continuación, ajusta esa imagen en tamaño, lo que genera imágenes de mapa de bits perceptible dentadas.

Para mantener la misma resolución de imagen, una solución mejor es escribir sus propias funciones simple para realizar conversiones entre los dos sistemas de coordenadas.

Además el `DrawCircle` método `SKCanvas` también define dos `DrawOval` métodos que dibujar una elipse. Una elipse se define mediante dos radios en lugar de un radio único. Estos se conocen como el *radius principal* y *radio menor*. El `DrawOval` método dibuja una elipse con los dos radios paralelo a los ejes X e Y. (Si necesita dibujar una elipse con los ejes que no son paralelos a los ejes X e Y, puede utilizar una transformación de rotación como se describe en el artículo [ **girar la transformación** ](../transforms/rotate.md) o una ruta de acceso de gráficos como se describe en el artículo [ **tres maneras de dibujar un arco**](../curves/arcs.md)). Esta sobrecarga de la [ `DrawOval` ](xref:SkiaSharp.SKCanvas.DrawOval(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) los parámetros de dos radios de los nombres de método `rx` y `ry` para indicar que son paralelas a los ejes X e Y:

```csharp
public void DrawOval (Single cx, Single cy, Single rx, Single ry, SKPaint paint)
```

¿Es posible dibujar una elipse que rellena la superficie de pantalla? El **elipse rellenar** página se muestra cómo. El `PaintSurface` controlador de eventos en el [ **EllipseFillPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/EllipseFillPage.xaml.cs) clase resta la mitad del ancho del trazo de la `xRadius` y `yRadius` valores para ajustarse a la elipse completa y su se describen en la superficie de pantalla:

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

Aquí se está ejecutando:

[![](pixels-images/ellipsefill-small.png "Captura de pantalla triple de la página de tamaño de la superficie")](pixels-images/ellipsefill-large.png#lightbox "Triple captura de pantalla de la página de tamaño de la superficie")

El otro [ `DrawOval` ](xref:SkiaSharp.SKCanvas.DrawOval(SkiaSharp.SKRect,SkiaSharp.SKPaint)) método tiene un [ `SKRect` ](xref:SkiaSharp.SKRect) argumento, que es un rectángulo definido en cuanto a las coordenadas X e Y de su esquina superior izquierda y la esquina inferior derecha. La elipse rellena el rectángulo, lo que sugiere que es posible usarlo en el **elipse rellenar** página similar al siguiente:

```csharp
SKRect rect = new SKRect(0, 0, info.Width, info.Height);
canvas.DrawOval(rect, paint);
```

Sin embargo, interrumpe todos los bordes del contorno de la elipse en los cuatro lados. Deberá ajustar todos los `SKRect` según los argumentos de constructor la `strokeWidth` para hacer este trabajo derecho:

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
