---
title: Dibujar un círculo simple en SkiaSharp
description: En este artículo se explican los aspectos básicos del dibujo de SkiaSharp, incluidos los lienzos y los objetos de Paint, en Xamarin.Forms las aplicaciones y se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: E3A4E373-F65D-45C8-8E77-577A804AC3F8
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 785532d1a8fedfaef367c8fb8ae437220c3de9c4
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938182"
---
# <a name="drawing-a-simple-circle-in-skiasharp"></a>Dibujar un círculo simple en SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Conozca los aspectos básicos del dibujo de SkiaSharp, incluidos los lienzos y los objetos Paint_

En este artículo se presentan los conceptos de dibujo de gráficos en el Xamarin.Forms uso de SkiaSharp, incluida la creación de un `SKCanvasView` objeto para hospedar los gráficos, el control del `PaintSurface` evento y el uso de un `SKPaint` objeto para especificar el color y otros atributos de dibujo.

El programa [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) contiene todo el código de ejemplo de esta serie de artículos de SkiaSharp. La primera página tiene derecho a **un círculo simple** e invoca la clase Page [`SimpleCirclePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs) . Este código muestra cómo dibujar un círculo en el centro de la página con un radio de 100 píxeles. El contorno del círculo es rojo y el interior del círculo es azul.

![Círculo azul que se describe en rojo](circle-images/circleexample.png)

La [`SimpleCircle`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs) clase Page se deriva de `ContentPage` y contiene dos `using` directivas para los espacios de nombres SkiaSharp:

```csharp
using SkiaSharp;
using SkiaSharp.Views.Forms;
```

El siguiente constructor de la clase crea un [`SKCanvasView`](xref:SkiaSharp.Views.Forms.SKCanvasView) objeto, adjunta un controlador para el [`PaintSurface`](xref:SkiaSharp.Views.Forms.SKCanvasView.PaintSurface) evento y establece el `SKCanvasView` objeto como el contenido de la página:

```csharp
public SimpleCirclePage()
{
    Title = "Simple Circle";

    SKCanvasView canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

El `SKCanvasView` espacio ocupa todo el área de contenido de la página. También puede combinar `SKCanvasView` con otros Xamarin.Forms `View` derivados, como verá en otros ejemplos.

El `PaintSurface` controlador de eventos es donde se realiza todo el dibujo. Se puede llamar a este método varias veces mientras el programa se está ejecutando, por lo que debe mantener toda la información necesaria para volver a crear la pantalla de gráficos:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
}

```

El [`SKPaintSurfaceEventArgs`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs) objeto que acompaña al evento tiene dos propiedades:

- [`Info`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Info) de tipo [`SKImageInfo`](xref:SkiaSharp.SKImageInfo)
- [`Surface`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Surface) de tipo [`SKSurface`](xref:SkiaSharp.SKSurface)

La `SKImageInfo` estructura contiene información sobre la superficie de dibujo, lo que es más importante, el ancho y el alto en píxeles. El `SKSurface` objeto representa la propia superficie de dibujo. En este programa, la superficie de dibujo es una presentación de vídeo, pero en otros programas un `SKSurface` objeto también puede representar un mapa de bits en el que se usa SkiaSharp.

La propiedad más importante de `SKSurface` es [`Canvas`](xref:SkiaSharp.SKSurface.Canvas) de tipo [`SKCanvas`](xref:SkiaSharp.SKCanvas) . Esta clase es un contexto de dibujo de gráficos que se usa para realizar el dibujo real. El `SKCanvas` objeto encapsula un estado de gráficos, que incluye transformaciones y recorte de gráficos.

A continuación se muestra un inicio típico de un `PaintSurface` controlador de eventos:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();
    ...
}

```

El [`Clear`](xref:SkiaSharp.SKCanvas.Clear) método borra el lienzo con un color transparente. Una sobrecarga permite especificar un color de fondo para el lienzo.

El objetivo es dibujar un círculo rojo relleno con azul. Dado que esta imagen gráfica concreta contiene dos colores diferentes, el trabajo debe realizarse en dos pasos. El primer paso es dibujar el contorno del círculo. Para especificar el color y otras características de la línea, debe crear e inicializar un [`SKPaint`](xref:SkiaSharp.SKPaint) objeto:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = Color.Red.ToSKColor(),
        StrokeWidth = 25
    };
    ...
}
```

La [`Style`](xref:SkiaSharp.SKPaint.Style) propiedad indica que desea *trazar* una línea (en este caso, el contorno del círculo) en lugar de *rellenar* el interior. Los tres miembros de la [`SKPaintStyle`](xref:SkiaSharp.SKPaintStyle) enumeración son los siguientes:

- [`Fill`](xref:SkiaSharp.SKPaintStyle.Fill)
- [`Stroke`](xref:SkiaSharp.SKPaintStyle.Stroke)
- [`StrokeAndFill`](xref:SkiaSharp.SKPaintStyle.StrokeAndFill)

El valor predeterminado es `Fill`. Utilice la tercera opción para trazar la línea y rellenar el interior con el mismo color.

Establezca la [`Color`](xref:SkiaSharp.SKPaint.Color) propiedad en un valor de tipo [`SKColor`](xref:SkiaSharp.SKColor) . Una manera de obtener un `SKColor` valor es convertir un Xamarin.Forms `Color` valor en un `SKColor` valor mediante el método de extensión [`ToSKColor`](xref:SkiaSharp.Views.Forms.Extensions.ToSKColor*) . La [`Extensions`](xref:SkiaSharp.Views.Forms.Extensions) clase del `SkiaSharp.Views.Forms` espacio de nombres incluye otros métodos que convierten entre Xamarin.Forms valores y valores de SkiaSharp.

La [`StrokeWidth`](xref:SkiaSharp.SKPaint.StrokeWidth) propiedad indica el grosor de la línea. Aquí se establece en 25 píxeles.

Use ese `SKPaint` objeto para dibujar el círculo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    ...
}
```

Las coordenadas se especifican en relación con la esquina superior izquierda de la superficie de presentación. Las coordenadas X aumentan a la derecha y las coordenadas Y aumentan de baja. En la explicación de los gráficos, a menudo se usa la notación matemática (x, y) para denotar un punto. El punto (0,0) es la esquina superior izquierda de la superficie de presentación y a menudo se denomina *origen*.

Los dos primeros argumentos de `DrawCircle` indican las coordenadas X e y del centro del círculo. Se asignan a la mitad del ancho y el alto de la superficie de presentación para colocar el centro del círculo en el centro de la superficie de la pantalla. El tercer argumento especifica el radio del círculo y el último argumento es el `SKPaint` objeto.

Para rellenar el interior del círculo, puede modificar dos propiedades del `SKPaint` objeto y volver a llamar a `DrawCircle` . Este código también muestra una manera alternativa de obtener un `SKColor` valor de uno de los muchos campos de la [`SKColors`](xref:SkiaSharp.SKColors) estructura:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    paint.Style = SKPaintStyle.Fill;
    paint.Color = SKColors.Blue;
    canvas.DrawCircle(args.Info.Width / 2, args.Info.Height / 2, 100, paint);
}
```

Esta vez, la `DrawCircle` llamada rellena el círculo con las nuevas propiedades del `SKPaint` objeto.

Este es el programa que se ejecuta en iOS y Android:

[![Captura de pantalla triple de la página círculo simple](circle-images/simplecircle-small.png)](circle-images/simplecircle-large.png#lightbox "Captura de pantalla triple de la página círculo simple")

Al ejecutar el programa usted mismo, puede desactivar el teléfono o el simulador para ver cómo se vuelve a dibujar el gráfico. Cada vez que es necesario volver a dibujar el gráfico, `PaintSurface` se llama de nuevo al controlador de eventos.

También es posible colorear objetos gráficos con degradados o mosaicos de mapas de bits. Estas opciones se describen en la sección sobre los [**sombreadores de SkiaSharp**](../effects/shaders/index.md).

Un `SKPaint` objeto es poco más que una colección de propiedades de dibujo de gráficos. Estos objetos son ligeros. Puede reutilizar `SKPaint` objetos como hace este programa o puede crear varios `SKPaint` objetos para diversas combinaciones de propiedades de dibujo. Puede crear e inicializar estos objetos fuera del `PaintSurface` controlador de eventos, y puede guardarlos como campos en la clase de página.

> [!NOTE]
> La `SKPaint` clase define un [`IsAntialias`](xref:SkiaSharp.SKPaint.IsAntialias) para habilitar el suavizado de contorno en la representación de los gráficos. En general, el suavizado de contorno produce bordes más suaves, por lo que probablemente querrá establecer esta propiedad en `true` en la mayoría de los `SKPaint` objetos. Por motivos de simplicidad, esta propiedad _no_ se establece en la mayoría de las páginas de ejemplo.

Aunque el ancho del contorno del círculo se especifica como 25 píxeles &mdash; o un cuarto del radio del círculo &mdash; parece más delgado, y hay una buena razón para ello: la mitad del ancho de la línea queda oscurecida por el círculo azul. Los argumentos para el `DrawCircle` método definen las coordenadas geométricas abstractas de un círculo. El interior azul se dimensiona a esa dimensión en el píxel más cercano, pero el contorno de 25 píxeles de ancho abarca la mitad del círculo geométrico del &mdash; interior y la mitad del exterior.

En el ejemplo siguiente del artículo [integración Xamarin.Forms con](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md) se demuestra esto visualmente.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
