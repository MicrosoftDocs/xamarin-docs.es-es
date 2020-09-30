---
title: SkiaSharp filtros de color
description: Use filtros de color para convertir colores con transformaciones o tablas.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 774E7B55-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/28/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 809477fe466ee7a8f0985308896c14341f2dd460
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561942"
---
# <a name="skiasharp-color-filters"></a>SkiaSharp filtros de color

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Los filtros de color pueden traducir los colores de un mapa de bits (u otra imagen) a otros colores para efectos como la posterización:

![Ejemplo de filtros de color](color-filters-images/ColorFiltersExample.png "Ejemplo de filtros de color")

Para usar un filtro de color, establezca la [`ColorFilter`](xref:SkiaSharp.SKPaint.ColorFilter) propiedad de `SKPaint` en un objeto de tipo [`SKColorFilter`](xref:SkiaSharp.SKColorFilter) creado por uno de los métodos estáticos de esa clase. En este artículo se muestra: 

- transformación de color creada con el [`CreateColorMatrix`](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) método.
- tabla de colores creada con el [`CreateTable`](xref:SkiaSharp.SKColorFilter.CreateTable*) método.

## <a name="the-color-transform"></a>La transformación de color

La transformación de color implica el uso de una matriz para modificar los colores. Como la mayoría de los sistemas de gráficos 2D, SkiaSharp usa matrices principalmente para transformar los puntos de coordenadas como iscussed en el artículo [**transformaciones de matriz en SkiaSharp**](../transforms/matrix.md). [`SKColorFilter`](xref:SkiaSharp.SKColorFilter)También admite transformaciones de matriz, pero la matriz transforma los colores RGB. Es necesario estar familiarizado con los conceptos de la matriz para comprender estas transformaciones de color. 

La matriz de transformación de color tiene una dimensión de cuatro filas y cinco columnas:

<pre>
| M11 M12 M13 M14 M15 |
| M21 M22 M23 M24 M25 |
| M31 M32 M33 M34 M35 |
| M41 M42 M43 M44 M45 |
</pre>

Transforma un color de origen RGB (R, G, B, a) al color de destino (R ', G ', B ', A '). 

Como preparación para la multiplicación de matrices, el color de origen se convierte en una matriz de 5 × 1:

<pre>
| R |
| G |
| B |
| A |
| 1 |
</pre>

Estos valores R, G, B y A son los bytes originales que van de 0 a 255. _No_ se normalizan en valores de punto flotante en el intervalo de 0 a 1.

La celda adicional es necesaria para un factor de traducción. Esto es análogo al uso de una matriz 3 × 3 para transformar los puntos de coordenadas bidimensionales, como se describe en la sección [**el motivo de la matriz de 3 por 3**](../transforms/matrix.md#the-reason-for-the-3-by-3-matrix) en el artículo sobre el uso de matrices para transformar puntos de coordenadas.

La matriz 4 × 5 se multiplica por la matriz 5 × 1 y el producto es una matriz de 4 × 1 con el color transformado:

<pre>
| M11 M12 M13 M14 M15 |    | R |   | R' |
| M21 M22 M23 M24 M25 |    | G |   | G' |
| M31 M32 M33 M34 M35 |  × | B | = | B' |
| M41 M42 M43 M44 M45 |    | A |   | A' |
                           | 1 |
</pre>

Estas son las fórmulas independientes para R ', G ', B ' y ':

`R' = M11·R + M12·G + M13·B + M14·A + M15` 

`G' = M21·R + M22·G + M23·B + M24·A + M25` 

`B' = M31·R + M32·G + M33·B + M34·A + M35` 

`A' = M41·R + M42·G + M43·B + M44·A + M45` 

La mayoría de la matriz está formada por factores de multiplicación que generalmente están en el intervalo de 0 a 2. Sin embargo, la última columna (de M15 a M45) contiene valores que se agregan en las fórmulas. Estos valores suelen oscilar entre 0 y 255. Los resultados se fijan entre los valores 0 y 255.

La matriz de identidad es:

<pre>
| 1 0 0 0 0 |
| 0 1 0 0 0 |
| 0 0 1 0 0 |
| 0 0 0 1 0 |
</pre>

Esto no provoca ningún cambio en los colores. Las fórmulas de transformación son:

`R' = R` 

`G' = G`

`B' = B`

`A' = A`

La celda M44 es muy importante porque conserva la opacidad. Por lo general, los valores de M41, M42 y M43 son cero, ya que probablemente no desee que la opacidad se base en los valores rojo, verde y azul. Pero si M44 es cero, un ' será cero y no habrá nada visible.

Uno de los usos más comunes de la matriz de colores es convertir un mapa de bits de color a un mapa de bits de escala de grises. Esto implica una fórmula para una media ponderada de los valores rojo, verde y azul. En el caso de las pantallas de vídeo con el espacio de colores sRGB ("estándar rojo verde azul"), esta fórmula es:

gris-sombreado = 0,2126 · R + 0,7152 · G + 0,0722 · B

Para convertir un mapa de bits de color a un mapa de bits de escala de grises, los resultados de R ', G ' y B ' deben ser iguales que el mismo valor. La matriz es:

<pre>
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0    0    0    1 0 |
</pre>

No hay ningún tipo de datos SkiaSharp que corresponda a esta matriz. En su lugar, debe representar la matriz como una matriz de 20 `float` valores en el orden de las filas: primera fila, segunda fila, etc.

El [`SKColorFilter.CreateColorMatrix`](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) método estático tiene la siguiente sintaxis:

```csharp
public static SKColorFilter CreateColorMatrix (float[] matrix);
```

donde `matrix` es una matriz de los 20 `float` valores. Al crear la matriz en C#, es fácil dar formato a los números para que se parezcan a la matriz de 4 × 5. Esto se muestra en la página **matriz de escala de grises** del ejemplo [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) :

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

El `DrawBitmap` método usado en este código procede del archivo **BitmapExtension.CS** incluido con el ejemplo [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . 

Este es el resultado que se ejecuta en iOS, Android y Plataforma universal de Windows:

[![Matriz de escala de grises](color-filters-images/GrayScaleMatrix.png "Matriz de escala de grises")](color-filters-images/GrayScaleMatrix-Large.png#lightbox)

Observe el valor de la cuarta fila y la cuarta columna. Este es el factor crucial que se multiplica por el valor del color original para el valor de un ' del color transformado. Si esa celda es cero, no se mostrará nada y el problema podría ser difícil de encontrar.

Al experimentar con matrices de colores, puede tratar la transformación desde la perspectiva del origen o desde la perspectiva del destino. ¿Cómo debe contribuir el píxel rojo del origen con los píxeles rojo, verde y azul del destino? Viene determinado por los valores de la primera _columna_ de la matriz. Como alternativa, ¿cómo debe afectar el píxel rojo de destino de los píxeles rojo, verde y azul del origen? Viene determinado por la primera _fila_ de la matriz.

Para ver algunas ideas sobre cómo usar las transformaciones de color, consulte las páginas [**cambiar el color**](/dotnet/framework/winforms/advanced/recoloring-images) de las imágenes. La explicación se refiere Windows Forms y la matriz tiene un formato diferente, pero los conceptos son los mismos.

La **matriz pastel** calcula el píxel rojo de destino al atenuar el píxel rojo de origen y enfatizar ligeramente los píxeles rojo y verde. Este proceso se produce de forma similar para los píxeles verde y azul:

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

El resultado es silenciar la intensidad de los colores como se puede ver aquí:

[![Matriz pastel](color-filters-images/PastelMatrix.png "Matriz pastel")](color-filters-images/PastelMatrix-Large.png#lightbox)

## <a name="color-tables"></a>Tablas de colores

El método estático se [`SKColorFilter.CreateTable`](xref:SkiaSharp.SKColorFilter.CreateTable*) incluye en dos versiones:

```csharp
public static SKColorFilter CreateTable (byte[] table);

public static SKColorFilter CreateTable (byte[] tableA, byte[] tableR, byte[] tableG, byte[] tableB);
```

Las matrices siempre contienen 256 entradas. En el `CreateTable` método con una tabla, se usa la misma tabla para los componentes rojo, verde y azul. Se trata de una tabla de búsqueda simple: Si el color de origen es (R, G, B) y el color de destino es (R ', B ', G '), los componentes de destino se obtienen mediante la indexación `table` con los componentes de origen:

`R' = table[R]`

`G' = table[G]`

`B' = table[B]`

En el segundo método, cada uno de los cuatro componentes de color puede tener una tabla de colores independiente, o bien se pueden compartir las mismas tablas de colores entre dos o más componentes.

Si desea establecer uno de los argumentos en el segundo método en `CreateTable` una tabla de colores que contenga los valores de 0 a 255 en secuencia, puede usar en `null` su lugar. A menudo, la `CreateTable` llamada tiene un `null` primer argumento para el canal alfa.

En la sección sobre la **posterización** en el artículo sobre el [acceso a los bits de píxeles de mapa de bits de SkiaSharp](../bitmaps/pixel-bits.md#posterization), vio cómo modificar los bits de píxeles individuales de un mapa de bits para reducir su resolución de color. Se trata de una técnica denominada _posterización_. 

También puede Posterizar un mapa de bits con una tabla de colores. El constructor de la página de la **tabla de posterización** crea una tabla de colores que asigna su índice a un byte con los 6 bits inferiores establecidos en cero:

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

El programa elige usar esta tabla de colores solo para los canales verde y azul. El canal rojo sigue teniendo una resolución completa:

[![Tabla de posterización](color-filters-images/PosterizeTable.png "Tabla de posterización")](color-filters-images/PosterizeTable-Large.png#lightbox)

Puede usar varias tablas de color para los distintos canales de color para distintos efectos. 

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)