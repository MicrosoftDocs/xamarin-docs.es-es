---
title: Filtros de color de SkiaSharp
description: Usar filtros de color para convertir colores con transformaciones o tablas.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 774E7B55-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/28/2018
ms.openlocfilehash: 5aa8b2e85d5a7d547af5333dcaf350025b86cc26
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647694"
---
# <a name="skiasharp-color-filters"></a>Filtros de color de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Filtros de color puede traducir los colores de un mapa de bits (u otra imagen) para otros colores efectos como posterización:

![Ejemplo de filtros de color](color-filters-images/ColorFiltersExample.png "ejemplo de filtros de Color")

Para usar un filtro de color, establezca el [ `ColorFilter` ](xref:SkiaSharp.SKPaint.ColorFilter) propiedad de `SKPaint` a un objeto de tipo [ `SKColorFilter` ](xref:SkiaSharp.SKColorFilter) creados por uno de los métodos estáticos en esa clase. En este artículo se muestra: 

- crear una transformación de color con el [ `CreateColorMatrix` ](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) método.
- crea una tabla de color con el [ `CreateTable` ](xref:SkiaSharp.SKColorFilter.CreateTable*) método.

## <a name="the-color-transform"></a>La transformación de color

La transformación de color implica el uso de una matriz que para modificar los colores. Al igual que la mayoría de los sistemas de gráficos 2D, SkiaSharp usa matrices principalmente para transformar los puntos de coordenada como iscussed en el artículo [ **transformaciones de matriz en SkiaSharp**](../transforms/matrix.md). El [ `SKColorFilter` ](xref:SkiaSharp.SKColorFilter) también admite transformaciones de matriz, pero las transformaciones de matriz de colores RGB. Cierta familiaridad con los conceptos de la matriz es necesario comprender estas transformaciones de color. 

La matriz de transformación de color tiene una dimensión de cuatro filas y cinco columnas:

<pre>
| M11 M12 M13 M14 M15 |
| M21 M22 M23 M24 M25 |
| M31 M32 M33 M34 M35 |
| M41 M42 M43 M44 M45 |
</pre>

Transforma un color de fuente RGB (R, G, B, A) para el color de destino (R', G', B ','). 

En preparación para la multiplicación de matrices, el color de origen se convierte en una matriz de 5 x 1:

<pre>
| R |
| G |
| B |
| A |
| 1 |
</pre>

Estos valores R, G, B y A son los bytes originales comprendida entre 0 y 255. Son _no_ normalizado a valores de punto flotante en el intervalo de 0 a 1.

La celda adicional es necesaria para un factor de traslación. Esto es análogo al uso de una matriz de 3 x 3 para transformar puntos de coordenadas bidimensionales, como se describe en la sección [ **el motivo de la matriz de 3 por 3** ](../transforms/matrix.md#the-reason-for-the-3-by-3-matrix) en el artículo sobre el uso de matrices para transformar puntos de coordenada.

La matriz de 4 x 5 se multiplica por la matriz de 5 x 1, y el producto es una matriz de 4 x 1 con el color transformado:

<pre>
| M11 M12 M13 M14 M15 |    | R |   | R' |
| M21 M22 M23 M24 M25 |    | G |   | G' |
| M31 M32 M33 M34 M35 |  × | B | = | B' |
| M41 M42 M43 M44 M45 |    | A |   | A' |
                           | 1 |
</pre>

Estas son las fórmulas independientes para R', G', 'B y A ":

`R' = M11·R + M12·G + M13·B + M14·A + M15` 

`G' = M21·R + M22·G + M23·B + M24·A + M25` 

`B' = M31·R + M32·G + M33·B + M34·A + M35` 

`A' = M41·R + M42·G + M43·B + M44·A + M45` 

La mayoría de la matriz consta de multiplicación factores que son generalmente en el intervalo de 0 a 2. Sin embargo, la última columna (M15 a través de M45) contiene valores que se agregan en las fórmulas. Por lo general, estos valores comprendido entre 0 y 255. Los resultados se unen entre los valores 0 y 255.

Es la matriz de identidad:

<pre>
| 1 0 0 0 0 |
| 0 1 0 0 0 |
| 0 0 1 0 0 |
| 0 0 0 1 0 |
</pre>

Se produce ningún cambio a los colores. Las fórmulas de transformación son:

`R' = R` 

`G' = G`

`B' = B`

`A' = A`

La celda M44 es muy importante porque conserva la opacidad. Por lo general es el caso de que M41 M42 y M43 son todos cero, ya que probablemente no querrá opacidad debe basarse en los valores de rojos, verde y azules. Sin embargo, si M44 es cero, una ' será cero y no será visible.

Uno de los usos más comunes de la matriz de colores es convertir un mapa de bits de color en un mapa de bits de la escala de grises. Esto implica una fórmula para una media ponderada de los valores de rojos, verde y azules. Para pantallas de vídeo usando el espacio de color sRGB ("standard rojo verde azul"), esta fórmula es:

escala de grises siempre = 0.2126· R + 0.7152· G + 0.0722· B

Para convertir un mapa de bits de color en un mapa de bits de escala de grises, R', G', y todos los resultados de B' deben ser ese mismo valor. La matriz es:

<pre>
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0    0    0    1 0 |
</pre>

No hay ningún tipo de datos de SkiaSharp que corresponde a esta matriz. En su lugar, debe representar la matriz como una matriz de 20 `float` valores en orden de fila: primera fila, a continuación, segunda fila y así sucesivamente.

Estático [ `SKColorFilter.CreateColorMatrix` ](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) método tiene la siguiente sintaxis:

```csharp
public static SKColorFilter CreateColorMatrix (float[] matrix);
```

donde `matrix` es una matriz de los 20 `float` valores. Al crear la matriz en C#, es fácil dar formato a los números por lo que se parecen a la matriz de 4 x 5. Esto se muestra en el **matriz de escala de grises** página en el [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) ejemplo:

```csharp
public class GrayScaleMatrixPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(CenteredTilesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg");

    public GrayScaleMatrixPage()
    {
        Title = "Gray-Scale Matrix";

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

        using (SKPaint paint = new SKPaint())
        {
            paint.ColorFilter =
                SKColorFilter.CreateColorMatrix(new float[]
                {
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0,     0,     0,     1, 0
                });

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

El `DrawBitmap` es el método utilizado en este código desde el **BitmapExtension.cs** archivo incluido con el [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) ejemplo. 

Este es el resultado que se ejecutan en iOS, Android y plataforma Universal de Windows:

[![Matriz de escala de grises](color-filters-images/GrayScaleMatrix.png "matriz de escala de grises")](color-filters-images/GrayScaleMatrix-Large.png#lightbox)

Esté atento al valor de la cuarta fila y la cuarta columna. Que es un factor crucial que se multiplica por el valor del color original en lugar de A' el valor del color transformado. Si esa celda es cero, se mostrará nada y el problema podría ser difícil encontrar.

Cuando experimente con matrices de color, puede tratar la transformación desde la perspectiva del origen o la perspectiva del destino. ¿Cómo debe contribuir el píxel del origen de rojo a los píxeles de rojos, verde y azules del destino? Que viene determinada por los valores de la primera _columna_ de la matriz. ¿Como alternativa, cómo debería el píxel del destino rojo verse afectado por los píxeles de rojos, verde y azules del origen de? Que viene determinada por la primera _fila_ de la matriz.

Para algunas ideas sobre cómo utilizar las transformaciones de color, vea el [ **cambiar el color de imágenes** ](https://docs.microsoft.com/dotnet/framework/winforms/advanced/recoloring-images) páginas. La discusión refiere a Windows Forms y la matriz es un formato distinto, pero los conceptos son los mismos.

El **Pastel matriz** calcula el píxel del destino rojo atenuar el píxel origen rojo y un poco con énfasis en los píxeles rojos y verdes. Este proceso se produce de forma similar a los píxeles de color verdes y azules:

```csharp
public class PastelMatrixPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PastelMatrixPage),
                        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    public PastelMatrixPage()
    {
        Title = "Pastel Matrix";

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

        using (SKPaint paint = new SKPaint())
        {
            paint.ColorFilter =
                SKColorFilter.CreateColorMatrix(new float[]
                {
                    0.75f, 0.25f, 0.25f, 0, 0,
                    0.25f, 0.75f, 0.25f, 0, 0,
                    0.25f, 0.25f, 0.75f, 0, 0,
                    0, 0, 0, 1, 0
                });

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

El resultado es silenciar la intensidad de los colores, como puede observar aquí:

[![Matriz de pastel](color-filters-images/PastelMatrix.png "matriz Pastel")](color-filters-images/PastelMatrix-Large.png#lightbox)

## <a name="color-tables"></a>Tablas de colores

Estático [ `SKColorFilter.CreateTable` ](xref:SkiaSharp.SKColorFilter.CreateTable*) método tiene dos versiones:

```csharp
public static SKColorFilter CreateTable (byte[] table);

public static SKColorFilter CreateTable (byte[] tableA, byte[] tableR, byte[] tableG, byte[] tableB);
```

Las matrices siempre contengan 256 entradas. En el `CreateTable` método con una tabla, la misma tabla se utiliza para los componentes rojos, verde y azules. Se trata de una tabla de búsqueda simple: Si el color de origen es (r, G, B) y el color de destino es (r ', B ', g '), los componentes de destino se obtienen mediante la `table` indexación con los componentes de origen:

`R' = table[R]`

`G' = table[G]`

`B' = table[B]`

En el segundo método, cada uno de los cuatro componentes de color puede tener una tabla de colores independiente o las mismas tablas de color pueden compartirse entre dos o más componentes.

Si desea establecer uno de los argumentos para el segundo `CreateTable` método a una tabla de colores que contiene los valores 0 y 255 en secuencia, puede usar `null` en su lugar. Muy a menudo el `CreateTable` llamada tiene un `null` primer argumento para el canal alfa.

En la sección sobre **posterización** en el artículo sobre [bits de píxeles del mapa de bits de SkiaSharp acceso a](../bitmaps/pixel-bits.md#posterization), vimos cómo modificar los bits de píxeles individuales de un mapa de bits para reducir su resolución de color. Se trata de una técnica denominada _posterización_. 

También puede posterización un mapa de bits con una tabla de colores. El constructor de la **tabla posterización** página crea una tabla de colores que se asigna su índice en un byte con la parte inferior a 6 bits establecidos en cero:

```csharp
public class PosterizeTablePage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PosterizeTablePage),
                        "SkiaSharpFormsDemos.Media.MonkeyFace.png");

    byte[] colorTable = new byte[256];

    public PosterizeTablePage()
    {
        Title = "Posterize Table";

        // Create color table
        for (int i = 0; i < 256; i++)
        {
            colorTable[i] = (byte)(0xC0 & i);
        }

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

        using (SKPaint paint = new SKPaint())
        {
            paint.ColorFilter =
                SKColorFilter.CreateTable(null, null, colorTable, colorTable);

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

El programa imprime esta tabla de colores solo para los canales de color verdes y azules. Canal rojo sigue teniendo resolución completa:

[![Tabla de posterización](color-filters-images/PosterizeTable.png "posterización tabla")](color-filters-images/PosterizeTable-Large.png#lightbox)

Puede utilizar varias tablas de color para los canales de color diferente para distintos efectos. 

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
