---
title: Mosaico de mapa de bits de SkiaSharp
description: Icono de un área con mapas de bits repetido horizontal y verticalmente.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 9ED14E07-4DC8-4B03-8A33-772838BF51EA
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 7d323aa6616f7547ab91dfe2b394c339e273d61c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68650022"
---
# <a name="skiasharp-bitmap-tiling"></a>Mosaico de mapa de bits de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/catclock)

Como ha visto en los dos artículos anteriores, el [ `SKShader` ](xref:SkiaSharp.SKShader) clase puede crear degradados lineales o circulares. En este artículo se centra en la `SKShader` objeto que usa un mapa de bits en un área de mosaico. El mapa de bits se puede repetir horizontalmente y verticalmente, ya sea en su orientación original o como alternativa volteado horizontalmente y verticalmente. El volteo evita discontinuidades entre los iconos:

![Ejemplo de disposición en mosaico de mapa de bits](bitmap-tiling-images/BitmapTilingSample.png "ejemplo de disposición en mosaico de mapa de bits")

Estático [ `SKShader.CreateBitmap` ](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode)) el método que crea este sombreador tiene un `SKBitmap` parámetro y dos miembros de la [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode) enumeración:

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy)
```

Los dos parámetros indican los modos utilizados para el mosaico horizontal y vertical disposición en mosaico. Esto es lo mismo `SKShaderTileMode` enumeración que también se utiliza con los métodos de degradado.

Un [ `CreateBitmap` ](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) sobrecarga incluye un `SKMatrix` argumento para realizar una transformación en los mapas de bits en mosaico:

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy, SKMatrix localMatrix)
```

En este artículo contiene varios ejemplos del uso de esta transformación de matriz con los mapas de bits en mosaico.

## <a name="exploring-the-tile-modes"></a>Exploración de los modos de mosaico

El primer programa en el **mosaico de mapa de bits** sección de la **sombreadores y otros efectos** página de la [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) ejemplo Muestra los efectos de los dos `SKShaderTileMode` argumentos. El **modos de voltear mosaico de mapa de bits** archivo XAML crea instancias de un `SKCanvasView` y dos `Picker` vistas que le permiten seleccionar un `SKShaderTilerMode` valor de disposición en mosaico horizontal y vertical. Tenga en cuenta que una matriz de los `SKShaderTileMode` miembros se define en el `Resources` sección:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BitmapTileFlipModesPage"
             Title="Bitmap Tile Flip Modes">

    <ContentPage.Resources>
        <x:Array x:Key="tileModes"
                 Type="{x:Type skia:SKShaderTileMode}">
            <x:Static Member="skia:SKShaderTileMode.Clamp" />
            <x:Static Member="skia:SKShaderTileMode.Repeat" />
            <x:Static Member="skia:SKShaderTileMode.Mirror" />
        </x:Array>
    </ContentPage.Resources>

    <StackLayout>
        <skiaforms:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="xModePicker"
                Title="Tile X Mode"
                Margin="10, 0"
                ItemsSource="{StaticResource tileModes}"
                SelectedIndex="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />

        <Picker x:Name="yModePicker"
                Title="Tile Y Mode"
                Margin="10, 10"
                ItemsSource="{StaticResource tileModes}"
                SelectedIndex="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />

    </StackLayout>
</ContentPage>
```

El constructor del archivo de código subyacente se carga en el recurso de mapa de bits que se muestra un objeto monkey sentado. En primer lugar, recorta la imagen mediante el [ `ExtractSubset` ](xref:SkiaSharp.SKBitmap.ExtractSubset(SkiaSharp.SKBitmap,SkiaSharp.SKRectI)) método `SKBitmap` para que la cabeza y los pies tocan los bordes del mapa de bits. El constructor, a continuación, usa el [ `Resize` ](xref:SkiaSharp.SKBitmap.Resize(SkiaSharp.SKImageInfo,SkiaSharp.SKBitmapResizeMethod)) método para crear otro mapa de bits de la mitad del tamaño. Estos cambios hacen que el mapa de bits que un poco más adecuado para la colocación en mosaico:

```csharp
public partial class BitmapTileFlipModesPage : ContentPage
{
    SKBitmap bitmap;

    public BitmapTileFlipModesPage ()
    {
        InitializeComponent ();

        SKBitmap origBitmap = BitmapExtensions.LoadBitmapResource(
            GetType(), "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

        // Define cropping rect
        SKRectI cropRect = new SKRectI(5, 27, 296, 260);

        // Get the cropped bitmap
        SKBitmap croppedBitmap = new SKBitmap(cropRect.Width, cropRect.Height);
        origBitmap.ExtractSubset(croppedBitmap, cropRect);

        // Resize to half the width and height
        SKImageInfo info = new SKImageInfo(cropRect.Width / 2, cropRect.Height / 2);
        bitmap = croppedBitmap.Resize(info, SKBitmapResizeMethod.Box);
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get tile modes from Pickers
        SKShaderTileMode xTileMode =
            (SKShaderTileMode)(xModePicker.SelectedIndex == -1 ?
                                        0 : xModePicker.SelectedItem);
        SKShaderTileMode yTileMode =
            (SKShaderTileMode)(yModePicker.SelectedIndex == -1 ?
                                        0 : yModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(bitmap, xTileMode, yTileMode);
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

El `PaintSurface` controlador obtiene la `SKShaderTileMode` configuración de los dos `Picker` vistas y crea un `SKShader` objeto basado en el mapa de bits y esos dos valores. Este sombreador se usa para rellenar el lienzo:

[![Modos de voltear mosaico de mapa de bits](bitmap-tiling-images/BitmapTileFlipModes.png "modos de voltear mosaico de mapa de bits")](bitmap-tiling-images/BitmapTileFlipModes-Large.png#lightbox)

La pantalla de iOS a la izquierda muestra el efecto de los valores predeterminados de `SKShaderTileMode.Clamp`. El mapa de bits se encuentra en la esquina superior izquierda. A continuación el mapa de bits, se repite la fila inferior de píxeles hacia abajo. A la derecha del mapa de bits, la columna situada más de píxeles se repite completamente a través. El resto del lienzo se colorea por píxel Marrón oscuro en la esquina inferior derecha del mapa de bits. Debería ser obvio que la `Clamp` opción casi nunca se utiliza con el mosaico de mapa de bits.

En el centro de la pantalla de Android muestra el resultado de `SKShaderTileMode.Repeat` para ambos argumentos. Se repite el mosaico horizontal y verticalmente. La plataforma Universal de Windows se muestra en pantalla `SKShaderTileMode.Mirror`. Los iconos se repite pero también puede voltear horizontalmente y verticalmente. La ventaja de esta opción es que no hay ningún discontinuidades entre los iconos.

Tenga en cuenta que puede utilizar diferentes opciones para la repetición horizontal y vertical. Puede especificar `SKShaderTileMode.Mirror` como el segundo argumento `CreateBitmap` pero `SKShaderTileMode.Repeat` como tercer argumento. En cada fila, el monos todavía alternar entre la imagen normal y la imagen reflejada, pero ninguno de los objetos son al revés.

## <a name="patterned-backgrounds"></a>Fondos de entramado

Mosaico de mapa de bits se usa normalmente para crear un fondo con trama de un mapa de bits relativamente pequeño. El ejemplo clásico es una pared.

El **pared algorítmico** página crea un mapa de bits pequeño que es similar a un brick todo y dos mitades de un brick separados por mortero. Dado que se usa este brick en el ejemplo siguiente, se crea mediante un constructor estático y han hecho públicos con una propiedad estática:

```csharp
public class AlgorithmicBrickWallPage : ContentPage
{
    static AlgorithmicBrickWallPage()
    {
        const int brickWidth = 64;
        const int brickHeight = 24;
        const int morterThickness = 6;
        const int bitmapWidth = brickWidth + morterThickness;
        const int bitmapHeight = 2 * (brickHeight + morterThickness);

        SKBitmap bitmap = new SKBitmap(bitmapWidth, bitmapHeight);

        using (SKCanvas canvas = new SKCanvas(bitmap))
        using (SKPaint brickPaint = new SKPaint())
        {
            brickPaint.Color = new SKColor(0xB2, 0x22, 0x22);

            canvas.Clear(new SKColor(0xF0, 0xEA, 0xD6));
            canvas.DrawRect(new SKRect(morterThickness / 2,
                                       morterThickness / 2,
                                       morterThickness / 2 + brickWidth,
                                       morterThickness / 2 + brickHeight),
                                       brickPaint);

            int ySecondBrick = 3 * morterThickness / 2 + brickHeight;

            canvas.DrawRect(new SKRect(0,
                                       ySecondBrick,
                                       bitmapWidth / 2 - morterThickness / 2,
                                       ySecondBrick + brickHeight),
                                       brickPaint);

            canvas.DrawRect(new SKRect(bitmapWidth / 2 + morterThickness / 2,
                                       ySecondBrick,
                                       bitmapWidth,
                                       ySecondBrick + brickHeight),
                                       brickPaint);
        }

        // Save as public property for other programs
        BrickWallTile = bitmap;
    }

    public static SKBitmap BrickWallTile { private set; get; }
    ···
}
```

El mapa de bits resultante es 70 píxeles de ancho y alto de 60 píxeles:

![Brick algorítmico de pared icono](bitmap-tiling-images/AlgorithmicBrickWallTile.png "algorítmico Brick de pared icono")

El resto de la **pared algorítmico** página crea un `SKShader` objeto que se repite esta imagen horizontal y verticalmente:

```csharp
public class AlgorithmicBrickWallPage : ContentPage
{
    ···
    public AlgorithmicBrickWallPage ()
    {
        Title = "Algorithmic Brick Wall";

        // Create SKCanvasView
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
            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(BrickWallTile,
                                                 SKShaderTileMode.Repeat,
                                                 SKShaderTileMode.Repeat);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Este es el resultado:

[![Pared algorítmico](bitmap-tiling-images/AlgorithmicBrickWall.png "algorítmica pared.")](bitmap-tiling-images/AlgorithmicBrickWall-Large.png#lightbox)

Es posible que prefieran algo un poco más realistas. En ese caso, puede tomar una fotografía de una pared real y, a continuación, recortarlo. Este mapa de bits es de 300 píxeles de ancho por 150 píxeles de alto:

![Icono de pared Brick](bitmap-tiling-images/BrickWallTile.jpg "paredes de ladrillo icono")

Este mapa de bits se usa en el **pared fotográficas** página:

```csharp
public class PhotographicBrickWallPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PhotographicBrickWallPage),
                        "SkiaSharpFormsDemos.Media.BrickWallTile.jpg");

    public PhotographicBrickWallPage()
    {
        Title = "Photographic Brick Wall";

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
            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Tenga en cuenta que el `SKShaderTileMode` argumentos `CreateBitmap` son ambos `Mirror`. Esta opción normalmente es necesaria al usar los iconos creados con imágenes reales. Creación de reflejo de los iconos evita discontinuidades:

[![Papel fotográfico pared](bitmap-tiling-images/PhotographicBrickWall.png "pared fotográficas")](bitmap-tiling-images/PhotographicBrickWall-Large.png#lightbox)

Es necesario algún trabajo para obtener un mapa de bits adecuado para el icono. Ésta no funciona muy bien porque destaca el brick más oscuro demasiado. Con frecuencia aparece dentro de las imágenes repetidas, revelar el hecho de que este paredes de ladrillo se construyen a partir de un mapa de bits más pequeño.

El **Media** carpeta de la [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) ejemplo también incluye esta imagen de una pared de piedra:

![Icono del muro de piedra](bitmap-tiling-images/StoneWallTile.jpg "muro de piedra icono")

Sin embargo, el mapa de bits original es un poco demasiado grande para un icono. Podría cambiarse, pero la `SKShader.CreateBitmap` método también puede cambiar el icono al aplicar una transformación a él. Esta opción se muestra en el **pared de piedra** página:

```csharp
public class StoneWallPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(StoneWallPage),
                        "SkiaSharpFormsDemos.Media.StoneWallTile.jpg");

    public StoneWallPage()
    {
        Title = "Stone Wall";

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
            // Create scale transform
            SKMatrix matrix = SKMatrix.MakeScale(0.5f, 0.5f);

            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror,
                                                 matrix);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Un `SKMatrix` valor se crea para escalar la imagen a la mitad de su tamaño original:

[![Tiro pared](bitmap-tiling-images/StoneWall.png "piedra pared")](bitmap-tiling-images/StoneWall-Large.png#lightbox)

¿Funciona la transformación en el mapa de bits original utilizado en el `CreateBitmap` método? ¿O transforman la matriz resultante de iconos? 

Una manera fácil de responder a esta pregunta es incluir una rotación como parte de la transformación:

```csharp
SKMatrix matrix = SKMatrix.MakeScale(0.5f, 0.5f);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeRotationDegrees(15));
```

Si la transformación se aplica al icono individual, a continuación, se debe girar cada imagen repetida del icono y el resultado debería contener muchas interrupciones. Pero resulta obvio a partir de esta captura de pantalla que se transforma la matriz compuesta de iconos:

[![Tiro pared girado](bitmap-tiling-images/StoneWallRotated.png "piedra pared girado")](bitmap-tiling-images/StoneWallRotated-Large.png#lightbox)

En la sección [ **icono alineación**](#tile-alignment), verá un ejemplo de una transformación de traslación que se aplica para el sombreador.

Independiente [ **reloj Cat** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/catclock) ejemplo (no forma parte de **SkiaSharpFormsDemos**) simula un grano de madera en segundo plano mediante el mosaico de mapa de bits en función de este mapa de bits 240 píxeles cuadrado:

![Nivel de detalle de madera](bitmap-tiling-images/WoodGrain.png "grano de madera")

Es una fotografía de un piso de madera. El `SKShaderTileMode.Mirror` opción permite que aparezca como un área mucho mayor de madera:

[![Cat reloj](bitmap-tiling-images/CatClock.png "Cat reloj")](bitmap-tiling-images/CatClock-Large.png#lightbox)

## <a name="tile-alignment"></a>Alineación de icono

Todos los ejemplos mostrados hasta ahora han usado el sombreador creado por `SKShader.CreateBitmap` para cubrir todo el lienzo. En la mayoría de los casos, usará el mosaico de mapa de bits para la tramitación de las áreas más pequeñas o (más rara vez) para rellenar el interior de las líneas gruesas. Este es el icono de pared fotográficas utilizado para un rectángulo más pequeño:

[![Alineación de icono](bitmap-tiling-images/TileAlignment.png "alineación de icono")](bitmap-tiling-images/TileAlignment-Large.png#lightbox)

Esto podría ser correcto para usted, o quizá no. Quizás haya molestado en que el modelo de mosaico no comienza con un brick completo en la esquina superior izquierda del rectángulo. Eso es porque los sombreadores se alinean con el lienzo y no el objeto gráfico adornan.

La solución es sencilla. Crear un `SKMatrix` valor basado en una transformación de traslación. La transformación traslada eficazmente el patrón de mosaico en el punto donde desea que la esquina superior izquierda del icono esté alineado. Este método se muestra en el **alineación de icono** página, que se creó la imagen de los iconos sin alinear mostrado anteriormente:

```csharp
public class TileAlignmentPage : ContentPage
{
    bool isAligned;

    public TileAlignmentPage()
    {
        Title = "Tile Alignment";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;

        // Add tap handler
        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            isAligned ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);

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
            SKRect rect = new SKRect(info.Width / 7,
                                     info.Height / 7,
                                     6 * info.Width / 7,
                                     6 * info.Height / 7);

            // Get bitmap from other program
            SKBitmap bitmap = AlgorithmicBrickWallPage.BrickWallTile;

            // Create bitmap tiling
            if (!isAligned)
            {
                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat);
            }
            else
            {
                SKMatrix matrix = SKMatrix.MakeTranslation(rect.Left, rect.Top);

                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat,
                                                     matrix);
            }

            // Draw rectangle
            canvas.DrawRect(rect, paint);
        }
    }
}
```

El **alineación de icono** página incluye un `TapGestureRecognizer`. Pulse o haga clic en la pantalla y los modificadores del programa para la `SKShader.CreateBitmap` método con un `SKMatrix` argumento. Esta transformación cambia el patrón para que la esquina superior izquierda contiene un brick completo:

[![Icono alineación pulsado](bitmap-tiling-images/TileAlignmentTapped.png "pulsado de alineación de icono")](bitmap-tiling-images/TileAlignmentTapped-Large.png#lightbox)

También puede usar esta técnica para asegurarse de que el patrón de mosaico de mapa de bits se centra dentro del área que pinta. En el **centrado iconos** página, el `PaintSurface` controlador calcula primero coordenadas como si va a mostrar el mapa de bits única en el centro del lienzo. A continuación, usa esas coordenadas para crear una transformación de traslación para `SKShader.CreateBitmap`. Esta transformación cambia el modelo completo para que se centra un icono:

```csharp
public class CenteredTilesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(CenteredTilesPage),
                        "SkiaSharpFormsDemos.Media.monkey.png");

    public CenteredTilesPage ()
    {
        Title = "Centered Tiles";

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

        // Find coordinates to center bitmap in canvas...
        float x = (info.Width - bitmap.Width) / 2f;
        float y = (info.Height - bitmap.Height) / 2f;

        using (SKPaint paint = new SKPaint())
        {
            // ... but use them to create a translate transform
            SKMatrix matrix = SKMatrix.MakeTranslation(x, y);
            paint.Shader = SKShader.CreateBitmap(bitmap, 
                                                 SKShaderTileMode.Repeat, 
                                                 SKShaderTileMode.Repeat, 
                                                 matrix);

            // Use that tiled bitmap pattern to fill a circle
            canvas.DrawCircle(info.Rect.MidX, info.Rect.MidY,
                              Math.Min(info.Width, info.Height) / 2,
                              paint);
        }
    }
}
```

El `PaintSurface` controlador concluye con el dibujo de un círculo en el centro del lienzo. Inevitablemente, uno de los iconos está exactamente en el centro del círculo, y los demás se organizan en un modelo simétrico:

[![Centrado iconos](bitmap-tiling-images/CenteredTiles.png "centrado iconos")](bitmap-tiling-images/CenteredTiles-Large.png#lightbox)

Otro enfoque centrado es realmente un poco más fácil. En lugar de construir una transformación de traslación que coloca un icono en el centro, puede Centrar una esquina del patrón de mosaico. En el `SKMatrix.MakeTranslation` llamada, utilice los argumentos para el centro del lienzo:

```csharp
SKMatrix matrix = SKMatrix.MakeTranslation(info.Rect.MidX, info.Rect.MidY);
```

El patrón es aún centrado y simétricos, pero ningún icono está en el centro:

[![Centrado iconos alternativo](bitmap-tiling-images/CenteredTilesAlternate.png "centrado iconos alternativo")](bitmap-tiling-images/CenteredTilesAlternate-Large.png#lightbox)

## <a name="simplification-through-rotation"></a>Simplificación de rotación

A veces, mediante una transformación de giro en el `SKShader.CreateBitmap` método puede simplificar el icono de mapa de bits. Esto se hace evidente cuando se intenta definir un icono para una barrera de vínculo de la cadena. El **ChainLinkTile.cs** archivo crea el icono se muestra a continuación (con un fondo de color rosado para una mayor claridad):

![Icono de vínculo físico de cadena](bitmap-tiling-images/HardChainLinkTile.png "icono de vínculo físico de cadena")

El icono debe incluir dos vínculos, por lo que el código del mosaico divide en cuatro cuadrantes. Los cuadrantes superior izquierda e inferior derecha son los mismos, pero no están completos. Los cables tienen poca muescas deben controlarse con algunos dibujo adicionales en los cuadrantes superior derecha e inferior izquierda. El archivo que hace este trabajo es 174 líneas de largo.

Resulta para ser mucho más fácil de crear este icono:

![Icono de vínculo de la cadena sea más fácil](bitmap-tiling-images/EasierChainLinkTile.png "más fácil de icono de vínculo de cadena")

Si el sombreador de mosaico de mapa de bits es gira 90 grados, los objetos visuales son prácticamente los mismos.

El código para crear el icono de vínculo de la cadena sea más fácil es parte de la **eslabones icono** página. El constructor determina un tamaño del mosaico según el tipo de dispositivo que se está ejecutando el programa y, a continuación, llama a `CreateChainLinkTile`, que se basa en el mapa de bits mediante líneas, las rutas de acceso y los sombreadores de degradado:

```csharp
public class ChainLinkFencePage : ContentPage
{
    ···
    SKBitmap tileBitmap;

    public ChainLinkFencePage ()
    {
        Title = "Chain-Link Fence";

        // Create bitmap for chain-link tiling
        int tileSize = Device.Idiom == TargetIdiom.Desktop ? 64 : 128;
        tileBitmap = CreateChainLinkTile(tileSize);

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    SKBitmap CreateChainLinkTile(int tileSize)
    {
        tileBitmap = new SKBitmap(tileSize, tileSize);
        float wireThickness = tileSize / 12f;

        using (SKCanvas canvas = new SKCanvas(tileBitmap))
        using (SKPaint paint = new SKPaint())
        {
            canvas.Clear();
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = wireThickness;
            paint.IsAntialias = true;

            // Draw straight wires first
            paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                         new SKPoint(0, tileSize),
                                                         new SKColor[] { SKColors.Silver, SKColors.Black },
                                                         new float[] { 0.4f, 0.6f },
                                                         SKShaderTileMode.Clamp);

            canvas.DrawLine(0, tileSize / 2,
                            tileSize / 2, tileSize / 2 - wireThickness / 2, paint);

            canvas.DrawLine(tileSize, tileSize / 2,
                            tileSize / 2, tileSize / 2 + wireThickness / 2, paint);

            // Draw curved wires
            using (SKPath path = new SKPath())
            {
                path.MoveTo(tileSize / 2, 0);
                path.LineTo(tileSize / 2 - wireThickness / 2, tileSize / 2);
                path.ArcTo(wireThickness / 2, wireThickness / 2,
                           0,
                           SKPathArcSize.Small,
                           SKPathDirection.CounterClockwise,
                           tileSize / 2, tileSize / 2 + wireThickness / 2);

                paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                             new SKPoint(0, tileSize),
                                                             new SKColor[] { SKColors.Silver, SKColors.Black },
                                                             null,
                                                             SKShaderTileMode.Clamp);
                canvas.DrawPath(path, paint);

                path.Reset();
                path.MoveTo(tileSize / 2, tileSize);
                path.LineTo(tileSize / 2 + wireThickness / 2, tileSize / 2);
                path.ArcTo(wireThickness / 2, wireThickness / 2,
                           0,
                           SKPathArcSize.Small,
                           SKPathDirection.CounterClockwise,
                           tileSize / 2, tileSize / 2 - wireThickness / 2);

                paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                             new SKPoint(0, tileSize),
                                                             new SKColor[] { SKColors.White, SKColors.Silver },
                                                             null,
                                                             SKShaderTileMode.Clamp);
                canvas.DrawPath(path, paint);
            }
            return tileBitmap;
        }
    }
    ···
}
```

Excepto los cables, el icono es transparente, lo que significa que puede mostrar en la parte superior algo más. El programa se carga en uno de los recursos de mapa de bits, lo muestra para rellenar el lienzo y, a continuación, dibuja al sombreador en la parte superior:

```csharp
public class ChainLinkFencePage : ContentPage
{
    SKBitmap monkeyBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(ChainLinkFencePage), "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");
    ···

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.DrawBitmap(monkeyBitmap, info.Rect, BitmapStretch.UniformToFill, 
                            BitmapAlignment.Center, BitmapAlignment.Start);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(tileBitmap, 
                                                 SKShaderTileMode.Repeat,
                                                 SKShaderTileMode.Repeat,
                                                 SKMatrix.MakeRotationDegrees(45));
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Tenga en cuenta que el sombreador es girados 45 grados, por lo que es la orientación como una barrera de vínculo de cadena real:

[![Barrera eslabones](bitmap-tiling-images/ChainLinkFence.png "eslabones barrera")](bitmap-tiling-images/ChainLinkFence-Large.png#lightbox)

## <a name="animating-bitmap-tiles"></a>Animación de los iconos de mapa de bits

Puede animar un patrón de mosaico de mapa de bits completo al animar la transformación de matriz. Quizás desee que el patrón para mover horizontal o verticalmente, o ambos. Puede hacerlo mediante la creación de una transformación de traslación en función de las coordenadas cambiantes.

También es posible dibujar en un mapa de bits pequeño, o para manipular los bits de píxeles del mapa de bits a una velocidad de 60 veces por segundo. Ese mapa de bits, a continuación, se puede usar para la colocación en mosaico y el patrón de mosaico completo puede parecer que se va a animar. 

El **mosaico de mapa de bits animado** página muestra este enfoque. Se crea una instancia de un mapa de bits como un campo sea 64 píxeles al cuadrado. El constructor llama a `DrawBitmap` para darle una apariencia inicial. Si el `angle` campo es cero (como cuando se llama al método por primera vez), a continuación, el mapa de bits contiene dos líneas cruzadas como una X. Las líneas están lo suficientemente largo para siempre en contacto con el borde del mapa de bits, independientemente de la `angle` valor: 

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    const int SIZE = 64;

    SKCanvasView canvasView;
    SKBitmap bitmap = new SKBitmap(SIZE, SIZE);
    float angle;
    ···

    public AnimatedBitmapTilePage ()
    {
        Title = "Animated Bitmap Tile";

        // Initialize bitmap prior to animation
        DrawBitmap();

        // Create SKCanvasView 
        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
    void DrawBitmap()
    {
        using (SKCanvas canvas = new SKCanvas(bitmap))
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = SIZE / 8;

            canvas.Clear();
            canvas.Translate(SIZE / 2, SIZE / 2);
            canvas.RotateDegrees(angle);
            canvas.DrawLine(-SIZE, -SIZE, SIZE, SIZE, paint);
            canvas.DrawLine(-SIZE, SIZE, SIZE, -SIZE, paint);
        }
    }
    ···
}
```

Se produce la sobrecarga de animación en el `OnAppearing` y `OnDisappearing` invalida. El `OnTimerTick` método anima la `angle` valor de 0 grados a 360 grados cada 10 segundos para girar la ilustración X en el mapa de bits:

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    ···
    // For animation
    bool isAnimating;
    Stopwatch stopwatch = new Stopwatch();
    ···

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 10;     // seconds
        angle = (float)(360f * (stopwatch.Elapsed.TotalSeconds % duration) / duration);
        DrawBitmap();
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

Debido a la simetría de la ilustración X, esto equivale a girar el `angle` valor entre 0 y 90 grados cada 2,5 segundos.

El `PaintSurface` controlador crea un sombreado del mapa de bits y utiliza el objeto de pintura para todo el lienzo de color:

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror);
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

El `SKShaderTileMode.Mirror` opciones garantizan que las ramas de la X en cada mapa de bits join con la X en los mapas de bits adyacentes para crear un patrón animado global que se parece mucho más complejo que sugeriría la animación sencilla:

[![Anima el mosaico de mapa de bits](bitmap-tiling-images/AnimatedBitmapTile.png "animado mosaico de mapa de bits")](bitmap-tiling-images/AnimatedBitmapTile-Large.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [CatClock (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/catclock)
