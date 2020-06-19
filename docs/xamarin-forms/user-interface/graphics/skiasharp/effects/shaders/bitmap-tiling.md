---
title: Mosaico de mapa de bits SkiaSharp
description: Colocar en mosaico un área con mapas de bits repetidos horizontal y verticalmente.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 9ED14E07-4DC8-4B03-8A33-772838BF51EA
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6a28dd20eb8978334365ac217df1241e5288fd28
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137434"
---
# <a name="skiasharp-bitmap-tiling"></a>Mosaico de mapa de bits SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/catclock)

Como ha visto en los dos artículos anteriores, la [`SKShader`](xref:SkiaSharp.SKShader) clase puede crear degradados lineales o circulares. Este artículo se centra en el `SKShader` objeto que utiliza un mapa de bits para colocar en mosaico un área. El mapa de bits se puede repetir horizontal y verticalmente, en su orientación original, o bien voltearse horizontal y verticalmente de forma alternativa. El volteo evita discontinuidades entre los mosaicos:

![Ejemplo de mosaico de mapas de bits](bitmap-tiling-images/BitmapTilingSample.png "Ejemplo de mosaico de mapas de bits")

El [`SKShader.CreateBitmap`](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode)) método estático que crea este sombreador tiene un `SKBitmap` parámetro y dos miembros de la [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) enumeración:

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy)
```

Los dos parámetros indican los modos que se usan para el mosaico horizontal y el mosaico vertical. Esta es la misma `SKShaderTileMode` enumeración que también se utiliza con los métodos de degradado.

Una [`CreateBitmap`](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) sobrecarga incluye un `SKMatrix` argumento para realizar una transformación en los mapas de bits en mosaico:

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy, SKMatrix localMatrix)
```

Este artículo contiene varios ejemplos de cómo usar esta transformación de matriz con mapas de bits en mosaico.

## <a name="exploring-the-tile-modes"></a>Exploración de los modos de mosaico

El primer programa de la sección **segmentación de mapas de bits** de la página **sombreadores y otros efectos** del ejemplo [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) muestra los efectos de los dos `SKShaderTileMode` argumentos. El archivo XAML de los **modos de volteo del mosaico de mapa de bits** crea una instancia de `SKCanvasView` y dos `Picker` vistas que permiten seleccionar un `SKShaderTilerMode` valor para el mosaico horizontal y vertical. Observe que se define una matriz de los `SKShaderTileMode` miembros en la `Resources` sección:

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

El constructor del archivo de código subyacente se carga en el recurso de mapa de bits que muestra un Monkey sentado. En primer lugar, recorta la imagen con el [`ExtractSubset`](xref:SkiaSharp.SKBitmap.ExtractSubset(SkiaSharp.SKBitmap,SkiaSharp.SKRectI)) método de `SKBitmap` para que el encabezado y los pies toquen los bordes del mapa de bits. A continuación, el constructor usa el [`Resize`](xref:SkiaSharp.SKBitmap.Resize(SkiaSharp.SKImageInfo,SkiaSharp.SKBitmapResizeMethod)) método para crear otro mapa de bits de la mitad del tamaño. Estos cambios hacen que el mapa de bits sea un poco más adecuado para el mosaico:

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

El `PaintSurface` controlador obtiene los `SKShaderTileMode` valores de las dos `Picker` vistas y crea un `SKShader` objeto basado en el mapa de bits y esos dos valores. Este sombreador se usa para rellenar el lienzo:

[![Modos de volteo de iconos de mapa de bits](bitmap-tiling-images/BitmapTileFlipModes.png "Modos de volteo de iconos de mapa de bits")](bitmap-tiling-images/BitmapTileFlipModes-Large.png#lightbox)

La pantalla de iOS de la izquierda muestra el efecto de los valores predeterminados de `SKShaderTileMode.Clamp` . El mapa de bits se encuentra en la esquina superior izquierda. Debajo del mapa de bits, la fila inferior de píxeles se repite hasta el final. A la derecha del mapa de bits, la columna de píxeles situada más a la derecha se repite en todo el recorrido. El resto del lienzo está coloreado por el píxel marrón oscuro de la esquina inferior derecha del mapa de bits. Debería ser obvio que la `Clamp` opción casi nunca se usa con el mosaico de mapas de bits.

En la pantalla de Android del centro se muestra el resultado de `SKShaderTileMode.Repeat` ambos argumentos. El mosaico se repite horizontalmente y verticalmente. Se muestra la pantalla Plataforma universal de Windows `SKShaderTileMode.Mirror` . Los mosaicos se repiten pero se voltean horizontal y verticalmente de forma alternativa. La ventaja de esta opción es que no hay ninguna discontinuidad entre los mosaicos.

Tenga en cuenta que puede usar diferentes opciones para la repetición horizontal y vertical. Puede especificar `SKShaderTileMode.Mirror` como segundo argumento, `CreateBitmap` pero `SKShaderTileMode.Repeat` como tercer argumento. En cada fila, el Monkeys sigue siendo alterno entre la imagen normal y la imagen reflejada, pero ninguna de las Monkeys están al revés.

## <a name="patterned-backgrounds"></a>Fondo con patrones

El mosaico de mapas de bits se usa normalmente para crear un fondo con patrones a partir de un mapa de bits relativamente pequeño. El ejemplo clásico es un muro de bricks.

La página de la **pared del brick algorítmico** crea un mapa de bits pequeño que se asemeja a un Brick entero y dos mitades de un Brick separado por un mortero. Dado que este Brick se usa también en el ejemplo siguiente, se crea mediante un constructor estático y se convierte en público con una propiedad estática:

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

El mapa de bits resultante tiene 70 píxeles de ancho y 60 píxeles de alto:

![Icono de pared de Brick algorítmico](bitmap-tiling-images/AlgorithmicBrickWallTile.png "Icono de pared de Brick algorítmico")

El resto de la página de la **pared del brick algorítmico** crea un `SKShader` objeto que repite esta imagen horizontal y verticalmente:

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

[![Muro de bricks algorítmico](bitmap-tiling-images/AlgorithmicBrickWall.png "Muro de bricks algorítmico")](bitmap-tiling-images/AlgorithmicBrickWall-Large.png#lightbox)

Podría preferir algo más realista. En ese caso, puede tomar una fotografía de una pared de Brick real y recortarla. Este mapa de bits es de 300 píxeles de ancho y 150 píxeles de alto:

![Icono de pared de Brick](bitmap-tiling-images/BrickWallTile.jpg "Icono de pared de Brick")

Este mapa de bits se usa en la página de la **pared del brick fotográfico** :

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

Observe que los `SKShaderTileMode` argumentos de `CreateBitmap` son ambos `Mirror` . Normalmente, esta opción es necesaria cuando se usan mosaicos creados a partir de imágenes del mundo real. La creación de reflejo de los mosaicos evita discontinuidades:

[![Pared de Brick fotográfico](bitmap-tiling-images/PhotographicBrickWall.png "Pared de Brick fotográfico")](bitmap-tiling-images/PhotographicBrickWall-Large.png#lightbox)

Se requiere algún trabajo para obtener un mapa de bits adecuado para el icono. Este no funciona bien porque el brick más oscuro destaca demasiado. Aparece periódicamente en las imágenes repetidas, lo que revela el hecho de que esta pared de Brick se construyó a partir de un mapa de bits más pequeño.

La carpeta de **medios** del ejemplo [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) también incluye esta imagen de una pared de piedra:

![Icono de pared de piedra](bitmap-tiling-images/StoneWallTile.jpg "Icono de pared de piedra")

Sin embargo, el mapa de bits original es un poco demasiado grande para un icono. Podría cambiarse de tamaño, pero el `SKShader.CreateBitmap` método también puede cambiar el tamaño del icono aplicándole una transformación. Esta opción se muestra en la página de la **pared de piedra** :

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

`SKMatrix`Se crea un valor para escalar la imagen a la mitad de su tamaño original:

[![Plano de piedra](bitmap-tiling-images/StoneWall.png "Plano de piedra")](bitmap-tiling-images/StoneWall-Large.png#lightbox)

¿La transformación funciona en el mapa de bits original utilizado en el `CreateBitmap` método? ¿O transforma la matriz resultante de mosaicos? 

Una manera fácil de responder a esta pregunta es incluir una rotación como parte de la transformación:

```csharp
SKMatrix matrix = SKMatrix.MakeScale(0.5f, 0.5f);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeRotationDegrees(15));
```

Si la transformación se aplica al mosaico individual, se debe girar cada imagen repetida del mosaico y el resultado contendrá muchas discontinuidades. Pero es obvio que en esta captura de pantalla se transforma la matriz compuesta de mosaicos:

[![Plano de piedra girado](bitmap-tiling-images/StoneWallRotated.png "Plano de piedra girado")](bitmap-tiling-images/StoneWallRotated-Large.png#lightbox)

En la sección [**alineación de mosaicos**](#tile-alignment), verá un ejemplo de una transformación translate aplicada al sombreador.

El ejemplo de [**reloj de gato**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/catclock) independiente (que no forma parte de **SkiaSharpFormsDemos**) simula un fondo de grano de madera mediante mosaicos de mapas de bits basados en este mapa de bits cuadrado de 240 píxeles:

![Grano de madera](bitmap-tiling-images/WoodGrain.png "Grano de madera")

Es una fotografía de un piso de madera. La `SKShaderTileMode.Mirror` opción permite que aparezca como un área mucho mayor de madera:

[![Reloj CAT](bitmap-tiling-images/CatClock.png "Reloj CAT")](bitmap-tiling-images/CatClock-Large.png#lightbox)

## <a name="tile-alignment"></a>Alineación de mosaicos

Todos los ejemplos mostrados hasta ahora han usado el sombreador creado por `SKShader.CreateBitmap` para cubrir todo el lienzo. En la mayoría de los casos, usará el mosaico de mapas de bits para el archivado de áreas más pequeñas o, en raras ocasiones, para rellenar el interior de las líneas gruesas. Este es el icono de la pared de ladrillos fotográfica que se usa para un rectángulo más pequeño:

[![Alineación de mosaicos](bitmap-tiling-images/TileAlignment.png "Alineación de mosaicos")](bitmap-tiling-images/TileAlignment-Large.png#lightbox)

Esto podría tener un aspecto correcto o quizás no. Es posible que se le moleste que el patrón de mosaico no comienza con un Brick completo en la esquina superior izquierda del rectángulo. Esto se debe a que los sombreadores se alinean con el lienzo y no con el objeto gráfico que son adornos.

La corrección es sencilla. Cree un `SKMatrix` valor basado en una transformación de traslación. La transformación desplaza eficazmente el patrón en mosaico hasta el punto en el que desea que se alinee la esquina superior izquierda del icono. Este enfoque se muestra en la página **alineación del mosaico** , que creó la imagen de los mosaicos no alineados mostrados anteriormente:

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

La página de **alineación del mosaico** incluye un `TapGestureRecognizer` . Pulse o haga clic en la pantalla y el programa cambiará al `SKShader.CreateBitmap` método con un `SKMatrix` argumento. Esta transformación desplaza el patrón de modo que la esquina superior izquierda contenga un Brick completo:

[![Puntear alineación de mosaico](bitmap-tiling-images/TileAlignmentTapped.png "Puntear alineación de mosaico")](bitmap-tiling-images/TileAlignmentTapped-Large.png#lightbox)

También puede usar esta técnica para asegurarse de que el patrón de mapa de bits en mosaico está centrado dentro del área que pinta. En la página **mosaicos centrados** , el `PaintSurface` controlador calcula primero las coordenadas como si va a mostrar el único mapa de bits en el centro del lienzo. A continuación, utiliza esas coordenadas para crear una transformación de traducción para `SKShader.CreateBitmap` . Esta transformación desplaza todo el patrón para que un mosaico esté centrado:

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

El `PaintSurface` controlador termina dibujando un círculo en el centro del lienzo. Asegúrese de que uno de los mosaicos esté exactamente en el centro del círculo y que los demás estén organizados en un patrón simétrico:

[![Mosaicos centrados](bitmap-tiling-images/CenteredTiles.png "Mosaicos centrados")](bitmap-tiling-images/CenteredTiles-Large.png#lightbox)

Otro enfoque de centrado es realmente más fácil. En lugar de construir una transformación translate que coloca un icono en el centro, puede centrar una esquina del patrón en mosaico. En la `SKMatrix.MakeTranslation` llamada, use los argumentos del centro del lienzo:

```csharp
SKMatrix matrix = SKMatrix.MakeTranslation(info.Rect.MidX, info.Rect.MidY);
```

El patrón sigue centrado y simétrico, pero no hay ningún mosaico en el centro:

[![Mosaicos centrados alternativos](bitmap-tiling-images/CenteredTilesAlternate.png "Mosaicos centrados alternativos")](bitmap-tiling-images/CenteredTilesAlternate-Large.png#lightbox)

## <a name="simplification-through-rotation"></a>Simplificación a través de la rotación

A veces, el uso de una transformación de giro en el `SKShader.CreateBitmap` método puede simplificar el icono de mapa de bits. Esto resulta evidente cuando se intenta definir un icono para una barrera de vínculo de cadena. El archivo **ChainLinkTile.CS** crea el icono que se muestra aquí (con un fondo rosa para fines de claridad):

![Cadena rígida: icono de vínculo](bitmap-tiling-images/HardChainLinkTile.png "Cadena rígida: icono de vínculo")

El icono debe incluir dos vínculos, de modo que el código divida el mosaico en cuatro cuadrantes. Los cuadrantes superior izquierdo e inferior derecho son los mismos, pero no están completos. Los cables tienen poca muescas que se deben controlar con algún dibujo adicional en los cuadrantes superior derecho e inferior izquierdo. El archivo que realiza todo este trabajo tiene 174 líneas de longitud.

Resulta mucho más fácil crear este icono:

![Icono cadena: vínculo más sencillo](bitmap-tiling-images/EasierChainLinkTile.png "Icono cadena: vínculo más sencillo")

Si el sombreador de mosaicos de mapa de bits se gira 90 grados, los objetos visuales son prácticamente los mismos.

El código para crear el icono de vínculo de cadena más sencillo forma parte de la página de **mosaicos de vínculo de cadena** . El constructor determina un tamaño de mosaico basado en el tipo de dispositivo en el que se está ejecutando el programa y, a continuación, llama a `CreateChainLinkTile` , que se dibuja en el mapa de bits mediante líneas, rutas de acceso y sombreadores de degradado:

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

A excepción de los cables, el icono es transparente, lo que significa que puede mostrarlo encima de otra cosa. El programa se carga en uno de los recursos de mapa de bits, lo muestra para rellenar el lienzo y, a continuación, dibuja el sombreador en la parte superior:

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

Tenga en cuenta que el sombreador gira 45 grados, por lo que está orientado como una barrera de vínculo de cadena real:

[![Barrera de vínculo de cadena](bitmap-tiling-images/ChainLinkFence.png "Barrera de vínculo de cadena")](bitmap-tiling-images/ChainLinkFence-Large.png#lightbox)

## <a name="animating-bitmap-tiles"></a>Animar iconos de mapa de bits

Puede animar un patrón de mosaico de mapa de bits completo animando la transformación de la matriz. Quizás desee que el patrón se mueva horizontal o verticalmente, o ambos. Puede hacerlo mediante la creación de una transformación de traslación basada en las coordenadas de desplazamiento.

También es posible dibujar en un mapa de bits pequeño o manipular los bits de píxel del mapa de bits a la velocidad de 60 veces a un segundo. Dicho mapa de bits se puede usar para la disposición en mosaico y el patrón en mosaico completo puede parecer animado. 

En la página del **icono de mapa de bits animado** se muestra este enfoque. Se crea una instancia de un mapa de bits como un campo de 64 píxeles cuadrados. El constructor llama `DrawBitmap` a para darle un aspecto inicial. Si el `angle` campo es cero (como cuando se llama al método por primera vez), el mapa de bits contiene dos líneas que se cruzan como una X. Las líneas son lo suficientemente largas como para llegar siempre al borde del mapa de bits, independientemente del `angle` valor: 

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

La sobrecarga de animación se produce en `OnAppearing` e `OnDisappearing` invalida. El `OnTimerTick` método anima el `angle` valor de 0 a 360 grados cada 10 segundos para girar la figura X en el mapa de bits:

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

Debido a la simetría de la figura X, es lo mismo que girar el `angle` valor de 0 a 90 grados cada 2,5 segundos.

El `PaintSurface` controlador crea un sombreador a partir del mapa de bits y usa el objeto Paint para colorear todo el lienzo:

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

Las `SKShaderTileMode.Mirror` Opciones aseguran que los brazos de la x de cada combinación de mapas de bits con la x en los mapas de bits adyacentes para crear un patrón animado general que parezca mucho más complejo que la simple animación sugerida:

[![Icono de mapa de bits animado](bitmap-tiling-images/AnimatedBitmapTile.png "Icono de mapa de bits animado")](bitmap-tiling-images/AnimatedBitmapTile-Large.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [CatClock (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/catclock)
