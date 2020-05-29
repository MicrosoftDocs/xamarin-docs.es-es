---
title: ''
description: ''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5c3909271580d0568d7c603de0d434ff5b3f3bc4
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138676"
---
# <a name="segmented-display-of-skiasharp-bitmaps"></a>Presentación segmentada de mapas de bits SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

El `SKCanvas` objeto SkiaSharp define un método denominado `DrawBitmapNinePatch` y dos métodos denominados `DrawBitmapLattice` que son muy similares. Ambos métodos representan un mapa de bits al tamaño de un rectángulo de destino, pero en lugar de ajustar el mapa de bits uniformemente, muestran partes del mapa de bits en sus dimensiones de píxeles y ajustan otras partes del mapa de bits para que quepa el rectángulo:

![Ejemplos segmentados](segmented-images/SegmentedSample.png "Ejemplo segmentado")

Estos métodos se suelen usar para representar mapas de bits que forman parte de objetos de la interfaz de usuario, como botones. Al diseñar un botón, normalmente desea que el tamaño de un botón se base en el contenido del botón, pero es probable que desee que el borde del botón tenga el mismo ancho independientemente del contenido del botón. Esta es una aplicación ideal de `DrawBitmapNinePatch` .

`DrawBitmapNinePatch`es un caso especial de `DrawBitmapLattice` pero es el más fácil de usar y comprender los dos métodos.

## <a name="the-nine-patch-display"></a>La pantalla de nueve revisiones 

Conceptualmente, [`DrawBitmapNinePatch`](xref:SkiaSharp.SKCanvas.DrawBitmapNinePatch(SkiaSharp.SKBitmap,SkiaSharp.SKRectI,SkiaSharp.SKRect,SkiaSharp.SKPaint)) divide un mapa de bits en nueve rectángulos:

![Nueve parche](segmented-images/NinePatch.png "Nueve parche")

Los rectángulos de las cuatro esquinas se muestran en sus tamaños de píxeles. A medida que las flechas indican, las demás áreas de los bordes del mapa de bits se ajustan horizontal o verticalmente al área del rectángulo de destino. El rectángulo del centro se expande tanto horizontal como verticalmente. 

Si no hay espacio suficiente en el rectángulo de destino para mostrar incluso las cuatro esquinas en sus dimensiones de píxeles, se escalan verticalmente hasta el tamaño disponible y no se muestran las cuatro esquinas.

Para dividir un mapa de bits en estos nueve rectángulos, solo es necesario especificar el rectángulo en el centro. Esta es la sintaxis del `DrawBitmapNinePatch` método:

```csharp
canvas.DrawBitmapNinePatch(bitmap, centerRectangle, destRectangle, paint);
```

El rectángulo central es relativo al mapa de bits. Es un `SKRectI` valor (la versión entera de `SKRect` ) y todas las coordenadas y tamaños están en unidades de píxeles. El rectángulo de destino es relativo a la superficie de la pantalla. El argumento `paint` es opcional.

La página de **presentación de nueve revisiones** en el ejemplo [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) usa primero un constructor estático para crear una propiedad estática pública de tipo `SKBitmap` :

```csharp
public partial class NinePatchDisplayPage : ContentPage
{
    static NinePatchDisplayPage()
    {
        using (SKCanvas canvas = new SKCanvas(FiveByFiveBitmap))
        using (SKPaint paint = new SKPaint
        {
            Style = SKPaintStyle.Stroke,
            Color = SKColors.Red,
            StrokeWidth = 10
        })
        {
            for (int x = 50; x < 500; x += 100)
                for (int y = 50; y < 500; y += 100)
                {
                    canvas.DrawCircle(x, y, 40, paint);
                }
        }
    }

    public static SKBitmap FiveByFiveBitmap { get; } = new SKBitmap(500, 500);
    ···
}
```

Otras dos páginas de este artículo usan ese mismo mapa de bits. El mapa de bits tiene 500 píxeles cuadrados y consta de una matriz de 25 círculos, todo el mismo tamaño, cada uno de los cuales ocupa una zona cuadrada de 100 píxeles:

![Cuadrícula de círculo](segmented-images/CircleGrid.png "Cuadrícula de círculo")

El constructor de instancia del programa crea un `SKCanvasView` con un `PaintSurface` controlador que utiliza `DrawBitmapNinePatch` para mostrar el mapa de bits ensanchado a toda la superficie de la pantalla:

```csharp
public class NinePatchDisplayPage : ContentPage
{
    ···
    public NinePatchDisplayPage()
    {
        Title = "Nine-Patch Display";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRectI centerRect = new SKRectI(100, 100, 400, 400);
        canvas.DrawBitmapNinePatch(FiveByFiveBitmap, centerRect, info.Rect);
    }
}
```

El `centerRect` rectángulo abarca la matriz central de 16 círculos. Los círculos de las esquinas se muestran en sus dimensiones de píxeles y todo lo demás se ajusta en consecuencia:

[![Presentación de nueve revisiones](segmented-images/NinePatchDisplay.png "Presentación de nueve revisiones")](segmented-images/NinePatchDisplay-Large.png#lightbox)

La página UWP tiene un tamaño de 500 píxeles de ancho y, por tanto, muestra las filas superior e inferior como una serie de círculos del mismo tamaño. De lo contrario, todos los círculos que no están en las esquinas se ajustan para formar puntos suspensivos.

En el caso de una presentación extraña de los objetos que se componen de una combinación de círculos y puntos suspensivos, intente definir el rectángulo central de modo que se solape con las filas y columnas de círculos:

```csharp
SKRectI centerRect = new SKRectI(150, 150, 350, 350);
```

## <a name="the-lattice-display"></a>Presentación de Lattice

Los dos `DrawBitmapLattice` métodos son similares a `DrawBitmapNinePatch` , pero se generalizan para cualquier número de divisiones horizontales o verticales. Estas divisiones están definidas por matrices de enteros que corresponden a píxeles. 

[`DrawBitmapLattice`](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,System.Int32[],System.Int32[],SkiaSharp.SKRect,SkiaSharp.SKPaint))Parece que el método con parámetros para estas matrices de enteros no funciona. El [`DrawBitmapLattice`](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,SkiaSharp.SKLattice,SkiaSharp.SKRect,SkiaSharp.SKPaint)) método con un parámetro de tipo `SKLattice` funciona y es el que se usa en los ejemplos que se muestran a continuación.

La [`SKLattice`](xref:SkiaSharp.SKLattice) estructura define cuatro propiedades:

- [`XDivs`](xref:SkiaSharp.SKLattice.XDivs), una matriz de enteros.
- [`YDivs`](xref:SkiaSharp.SKLattice.YDivs), una matriz de enteros.
- [`Flags`](xref:SkiaSharp.SKLattice.Flags), una matriz de `SKLatticeFlags` , un tipo de enumeración
- [`Bounds`](xref:SkiaSharp.SKLattice.Bounds)de tipo `Nullable<SKRectI>` para especificar un rectángulo de origen opcional en el mapa de bits

La `XDivs` matriz divide el ancho del mapa de bits en bandas verticales. La primera franja se extiende desde el píxel 0 a la izquierda hasta `XDivs[0]` . Esta franja se representa en el ancho de píxel. La segunda franja se extiende de `XDivs[0]` a `XDivs[1]` , y se ajusta. La tercera franja se extiende de `XDivs[1]` a `XDivs[2]` y se representa en el ancho de píxel. La última franja se extiende desde el último elemento de la matriz hasta el borde derecho del mapa de bits. Si la matriz tiene un número par de elementos, se muestra en su ancho de píxel. De lo contrario, se ajusta. El número total de bandas verticales es uno más que el número de elementos de la matriz.

La `YDivs` matriz es similar. Divide el alto de la matriz en franjas horizontales.

Juntas, la `XDivs` `YDivs` matriz y divide el mapa de bits en rectángulos. El número de rectángulos es igual al producto del número de bandas horizontales y el número de bandas verticales.

Según la documentación de Skia, la `Flags` matriz contiene un elemento para cada rectángulo, primero la fila superior de rectángulos, después la segunda fila, etc. La `Flags` matriz es de tipo [`SKLatticeFlags`](xref:SkiaSharp.SKLatticeFlags) , una enumeración con los siguientes miembros:

- `Default`con valor 0
- `Transparent`con valor 1

Sin embargo, estas marcas no parecen funcionar como se supone y es mejor omitirlas. Sin embargo, no establezca la `Flags` propiedad en `null` . Establézcalo en una matriz de `SKLatticeFlags` valores lo suficientemente grande como para abarcar el número total de rectángulos.

La página de **revisión de Lattice Nine** usa `DrawBitmapLattice` para imitar `DrawBitmapNinePatch` . Usa el mismo mapa de bits creado en `NinePatchDisplayPage` :

```csharp
public class LatticeNinePatchPage : ContentPage
{
    SKBitmap bitmap = NinePatchDisplayPage.FiveByFiveBitmap;

    public LatticeNinePatchPage ()
    {
        Title = "Lattice Nine-Patch";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    `
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        SKLattice lattice = new SKLattice();
        lattice.XDivs = new int[] { 100, 400 };
        lattice.YDivs = new int[] { 100, 400 };
        lattice.Flags = new SKLatticeFlags[9]; 

        canvas.DrawBitmapLattice(bitmap, lattice, info.Rect);
    }
}
```

Las `XDivs` propiedades y `YDivs` se establecen en matrices de solo dos enteros, dividiendo el mapa de bits en tres bandas horizontal y verticalmente: de píxel 0 a píxel 100 (representado en el tamaño de píxel), de píxel 100 a píxel 400 (ajustado) y de píxel 400 a píxel 500 (tamaño de píxel). Juntos, `XDivs` y `YDivs` definen un total de 9 rectángulos, que es el tamaño de la `Flags` matriz. Basta con crear la matriz para crear una matriz de `SKLatticeFlags.Default` valores.

La pantalla es idéntica a la del programa anterior:

[![Revisión de Lattice Nine](segmented-images/LatticeNinePatch.png "Revisión de Lattice Nine")](segmented-images/LatticeNinePatch-Large.png#lightbox)

La página de **presentación de Lattice** divide el mapa de bits en 16 rectángulos:

```csharp
public class LatticeDisplayPage : ContentPage
{
    SKBitmap bitmap = NinePatchDisplayPage.FiveByFiveBitmap;

    public LatticeDisplayPage()
    {
        Title = "Lattice Display";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKLattice lattice = new SKLattice();
        lattice.XDivs = new int[] { 100, 200, 400 };
        lattice.YDivs = new int[] { 100, 300, 400 };

        int count = (lattice.XDivs.Length + 1) * (lattice.YDivs.Length + 1);
        lattice.Flags = new SKLatticeFlags[count];

        canvas.DrawBitmapLattice(bitmap, lattice, info.Rect);
    }
}
```

Las `XDivs` `YDivs` matrices y son ligeramente diferentes, lo que hace que la presentación no sea tan simétrica como los ejemplos anteriores:

[![Lattice Mostrar](segmented-images/LatticeDisplay.png "Lattice Mostrar")](segmented-images/LatticeDisplay-Large.png#lightbox)

En las imágenes de iOS y Android de la izquierda, solo los círculos más pequeños se representan en sus tamaños de píxeles. Todo lo demás se ajusta.

La página de **presentación de Lattice** generaliza la creación de la `Flags` matriz, lo que permite experimentar con `XDivs` y `YDivs` más fácilmente. En concreto, querrá ver lo que sucede cuando se establece el primer elemento de la `XDivs` `YDivs` matriz o en 0. 

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
