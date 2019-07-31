---
title: Visualización segmentada de mapas de bits de SkiaSharp
description: Mostrar un mapa de bits de SkiaSharp para que se ajusta algunas áreas y algunas áreas no lo son.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 79AE2033-C41C-4447-95A6-76D22E913D19
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: ca6c8fafe4352bac83e5ae60b43627d4c7fdc10f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648666"
---
# <a name="segmented-display-of-skiasharp-bitmaps"></a>Visualización segmentada de mapas de bits de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

El SkiaSharp `SKCanvas` objeto define un método denominado `DrawBitmapNinePatch` y dos métodos denominados `DrawBitmapLattice` que son muy similares. Tanto estos métodos representan un mapa de bits para el tamaño de un rectángulo de destino, pero en lugar de estirar el mapa de bits de manera uniforme, que se muestran las partes del mapa de bits en sus dimensiones en píxeles y stretch otras partes del mapa de bits para que quepa el rectángulo:

![Segmentada ejemplos](segmented-images/SegmentedSample.png "segmentado de ejemplo")

Por lo general, estos métodos se usan para representar mapas de bits que forman parte de los objetos de interfaz de usuario, como botones. Al diseñar un botón, por lo general desea que el tamaño de un botón se basara en el contenido del botón, pero es probable que quiera del borde del botón en el mismo ancho sin tener en cuenta el contenido del botón. Que es una aplicación ideal de `DrawBitmapNinePatch`.

`DrawBitmapNinePatch` es un caso especial de `DrawBitmapLattice` pero resulta más fácil de los dos métodos para usar y entender.

## <a name="the-nine-patch-display"></a>La pantalla de revisión de nueve 

Conceptualmente, [ `DrawBitmapNinePatch` ](xref:SkiaSharp.SKCanvas.DrawBitmapNinePatch(SkiaSharp.SKBitmap,SkiaSharp.SKRectI,SkiaSharp.SKRect,SkiaSharp.SKPaint)) un mapa de bits se divide en nueve rectángulos:

![Revisión de nueve](segmented-images/NinePatch.png "nueve revisión")

Se muestran los rectángulos en las cuatro esquinas en sus tamaños de píxeles. Como indican las flechas, las demás áreas en las aristas del mapa de bits se estiran horizontal o verticalmente hasta el área del rectángulo de destino. Se expande el rectángulo en el centro horizontal y verticalmente. 

Si no hay suficiente espacio en el rectángulo de destino para mostrar incluso las cuatro esquinas de sus dimensiones en píxeles, a continuación, se reducen el tamaño disponible y nada, pero se muestran las cuatro esquinas.

Para dividir un mapa de bits en estos nueve rectángulos, sólo es necesario especificar el rectángulo en el centro. Ésta es la sintaxis de la `DrawBitmapNinePatch` método:

```csharp
canvas.DrawBitmapNinePatch(bitmap, centerRectangle, destRectangle, paint);
```

Es el rectángulo central en relación con el mapa de bits. Es un `SKRectI` valor (la versión de enteros de `SKRect`) y todos los tamaños y las coordenadas están en unidades de píxeles. El rectángulo de destino es relativa a la superficie de pantalla. El argumento `paint` es opcional.

El **nueve revisión mostrar** página en el [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) ejemplo primero utiliza un constructor estático para crear una propiedad estática pública del tipo `SKBitmap`:

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

Otras dos páginas en este artículo usan ese mismo mapa de bits. El mapa de bits es 500 píxeles cuadrados y consta de una matriz de 25 círculos, tamaño, cada una zona cuadrada 100 píxeles ocupan las mismas:

![Círculo cuadrícula](segmented-images/CircleGrid.png "círculo cuadrícula")

El constructor de instancia del programa crea un `SKCanvasView` con un `PaintSurface` controlador que usa `DrawBitmapNinePatch` para mostrar el mapa de bits que se ajusta a la superficie de pantalla completa:

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

El `centerRect` rectángulo abarca la matriz de 16 círculos central. Se muestran los círculos que aparecen en las esquinas en sus dimensiones en píxeles, y todo lo demás se ajusta según corresponda:

[![Pantalla de revisión de nueve](segmented-images/NinePatchDisplay.png "Mostrar nueve-Patch")](segmented-images/NinePatchDisplay-Large.png#lightbox)

La página UWP es 500 píxeles de ancho y, por lo tanto, muestra las filas superior e inferior como una serie de círculos del mismo tamaño. De lo contrario, todos los círculos que no están en las esquinas se ajusta a los puntos suspensivos del formulario.

Para una presentación extraña de los objetos que se compone de una combinación de los círculos y las elipses, intente definir el rectángulo central para que se superpone a las filas y columnas de círculos:

```csharp
SKRectI centerRect = new SKRectI(150, 150, 350, 350);
```

## <a name="the-lattice-display"></a>La presentación de celosía

Los dos `DrawBitmapLattice` son similares a los métodos `DrawBitmapNinePatch`, pero se ha generalizado para cualquier número de divisiones horizontales o verticales. Estas divisiones se definen mediante las matrices de enteros, que corresponden a los píxeles. 

El [ `DrawBitmapLattice` ](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,System.Int32[],System.Int32[],SkiaSharp.SKRect,SkiaSharp.SKPaint)) método con parámetros para estas matrices de enteros no parece funcionar. El [ `DrawBitmapLattice` ](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,SkiaSharp.SKLattice,SkiaSharp.SKRect,SkiaSharp.SKPaint)) método con un parámetro de tipo `SKLattice` funciona, y eso es lo que se utilizan en los ejemplos se muestra a continuación.

El [ `SKLattice` ](xref:SkiaSharp.SKLattice) estructura define cuatro propiedades:

- [`XDivs`](xref:SkiaSharp.SKLattice.XDivs), una matriz de enteros
- [`YDivs`](xref:SkiaSharp.SKLattice.YDivs), una matriz de enteros
- [`Flags`](xref:SkiaSharp.SKLattice.Flags), una matriz de `SKLatticeFlags`, un tipo de enumeración
- [`Bounds`](xref:SkiaSharp.SKLattice.Bounds) de tipo `Nullable<SKRectI>` para especificar un rectángulo de origen opcional en el mapa de bits

El `XDivs` matriz divide el ancho del mapa de bits en bandas verticales. La primera franja se extiende desde el píxel 0 a la izquierda para `XDivs[0]`. Esta banda se representa en su ancho en píxeles. La segunda banda se extiende desde `XDivs[0]` a `XDivs[1]`y se ajusta. La tercera banda se extiende desde `XDivs[1]` a `XDivs[2]` y se representa en su ancho en píxeles. La franja de último se extiende desde el último elemento de la matriz para el borde derecho del mapa de bits. Si la matriz tiene un número par de elementos, se muestra en su ancho en píxeles. En caso contrario, se ajusta. El número total de bandas verticales es uno más que el número de elementos de la matriz.

El `YDivs` matriz es similar. Divide el alto de la matriz en bandas horizontales.

Juntos, el `XDivs` y `YDivs` matriz dividir el mapa de bits en rectángulos. El número de rectángulos es igual al producto del número de franjas horizontales y el número de bandas verticales.

Según la documentación de Skia, el `Flags` matriz contiene un elemento para cada rectángulo, en primer lugar en la fila superior de los rectángulos, a continuación, la segunda fila y así sucesivamente. El `Flags` matriz es de tipo [ `SKLatticeFlags` ](xref:SkiaSharp.SKLatticeFlags), una enumeración con los miembros siguientes:

- `Default` con valor 0
- `Transparent` con el valor 1

Sin embargo, estos marcadores no parecen funcionar como que se supone, y es mejor hacer caso omiso a. Pero no establece la `Flags` propiedad `null`. Establézcala en una matriz de `SKLatticeFlags` valores lo suficientemente grandes como para abarcar el número total de rectángulos.

El **Lattice nueve Patch** página usa `DrawBitmapLattice` para imitar `DrawBitmapNinePatch`. Utiliza el mismo mapa de bits creado en `NinePatchDisplayPage`:

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

Tanto el `XDivs` y `YDivs` propiedades se establecen en las matrices de dos enteros, dividir el mapa de bits en tres bandas tanto horizontal como verticalmente: de píxel 0 a 100 (representado en el tamaño de píxel), píxeles desde 100 de píxel a píxel 400 (estirado), y desde el píxel 400 a 500 (tamaño de píxel) píxel. Juntos, `XDivs` y `YDivs` definir un total de 9 rectángulos, que es el tamaño de la `Flags` matriz. Basta con crear la matriz es suficiente para crear una matriz de `SKLatticeFlags.Default` valores.

La visualización es idéntica al programa anterior:

[![Lattice nueve-Patch](segmented-images/LatticeNinePatch.png "Lattice nueve-Patch.")](segmented-images/LatticeNinePatch-Large.png#lightbox)

El **Lattice mostrar** página divide el mapa de bits en 16 rectángulos:

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

El `XDivs` y `YDivs` las matrices son algo diferentes, provocando la presentación que no sea bastante como simétrica como en los ejemplos anteriores:

[![Presentación de celosía](segmented-images/LatticeDisplay.png "Lattice presentación")](segmented-images/LatticeDisplay-Large.png#lightbox)

En iOS y Android imágenes a la izquierda, sólo los círculos más pequeños se representan en sus tamaños de píxeles. Se expande todo lo demás.

El **Lattice mostrar** página generaliza la creación de la `Flags` matriz, lo que le permite experimentar con `XDivs` y `YDivs` más fácilmente. En particular, querrá ver lo que sucede cuando se establece el primer elemento de la `XDivs` o `YDivs` matriz en 0. 

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
