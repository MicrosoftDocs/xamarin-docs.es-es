---
title: Rutas de acceso y texto en SkiaSharp
description: En este artículo se explora la intersección de las rutas de acceso de SkiaSharp y el texto, y se muestra con código de ejemplo.
ms.prod: xamarin
ms.assetid: C14C07F6-4A84-4A8C-BDB4-CD61FBF0F79B
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 08/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b0cbb7d26a2aea02a3255fc75947c20a3d803b86
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84131903"
---
# <a name="paths-and-text-in-skiasharp"></a>Rutas de acceso y texto en SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explorar la intersección de rutas de acceso y texto_

En los sistemas de gráficos modernos, las fuentes de texto son colecciones de contornos de caracteres, normalmente definidas por curvas de Bézier cuadráticas. Por lo tanto, muchos sistemas de gráficos modernos incluyen una utilidad para convertir los caracteres de texto en una ruta de acceso de gráficos.

Ya ha visto que puede trazar los contornos de los caracteres de texto, así como rellenarlos. Esto le permite mostrar estos contornos de carácter con un ancho de trazo determinado e incluso un efecto de trazado tal y como se describe en el artículo de efectos de la [**ruta de acceso**](effects.md) . Pero también es posible convertir una cadena de caracteres en un `SKPath` objeto. Esto significa que los esquemas de texto se pueden usar para el recorte con las técnicas descritas en el artículo [**recorte con rutas y regiones**](clipping.md) .

Además de usar un efecto de trazado para trazar un contorno de carácter, también puede crear efectos de trazado basados en una ruta de acceso que se deriva de una cadena de caracteres, e incluso puede combinar los dos efectos:

![](text-paths-images/pathsandtextsample.png "Text Path Effect")

En el artículo anterior sobre los efectos de la [**ruta de acceso**](effects.md), vio cómo el [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single)) método de `SKPaint` puede obtener un contorno de una ruta de trazo. También puede usar este método con las rutas de acceso que se derivan de los contornos de caracteres.

Por último, en este artículo se muestra otra intersección de rutas de acceso y texto: el [`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) método de `SKCanvas` permite mostrar una cadena de texto para que la línea base del texto siga a un trazado curvo.

## <a name="text-to-path-conversion"></a>Conversión de texto a trazado

El [`GetTextPath`](xref:SkiaSharp.SKPaint.GetTextPath(System.String,System.Single,System.Single)) método de `SKPaint` convierte una cadena de caracteres en un `SKPath` objeto:

```csharp
public SKPath GetTextPath (String text, Single x, Single y)
```

Los `x` `y` argumentos y indican el punto inicial de la línea base del lado izquierdo del texto. Desempeñan el mismo rol aquí que en el `DrawText` método de `SKCanvas` . Dentro de la ruta de acceso, la línea de base del lado izquierdo del texto tendrá las coordenadas (x, y).

El `GetTextPath` método es outkill si simplemente desea rellenar o trazar la ruta de acceso resultante. El `DrawText` método normal le permite hacerlo. El `GetTextPath` método es más útil para otras tareas que implican rutas de acceso.

Una de estas tareas es el recorte. La página **texto de recorte** crea un trazado de recorte basado en el carácter que se describe en la palabra "Code". Esta ruta de acceso se ajusta al tamaño de la página para recortar un mapa de bits que contiene una imagen del código fuente del **texto de recorte** :

[![](text-paths-images/clippingtext-small.png "Triple screenshot of the Clipping Text page")](text-paths-images/clippingtext-large.png#lightbox "Triple screenshot of the Clipping Text page")

El [`ClippingTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClippingTextPage.cs) constructor de clase carga el mapa de bits que se almacena como un recurso incrustado en la carpeta **multimedia** de la solución:

```csharp
public class ClippingTextPage : ContentPage
{
    SKBitmap bitmap;

    public ClippingTextPage()
    {
        Title = "Clipping Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        string resourceID = "SkiaSharpFormsDemos.Media.PageOfCode.png";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ...
}
```

El `PaintSurface` controlador comienza creando un `SKPaint` objeto adecuado para el texto. La `Typeface` propiedad se establece así como `TextSize` , aunque para esta aplicación concreta la `TextSize` propiedad es puramente arbitraria. Observe también que no hay ningún `Style` valor.

Los `TextSize` valores de las `Style` propiedades y no son necesarios porque este `SKPaint` objeto se utiliza únicamente para la `GetTextPath` llamada mediante la cadena de texto "Code". Después, el controlador mide el `SKPath` objeto resultante y aplica tres transformaciones para centrarlo y escalarlo al tamaño de la página. A continuación, la ruta de acceso se puede establecer como el trazado de recorte:

```csharp
public class ClippingTextPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Blue);

        using (SKPaint paint = new SKPaint())
        {
            paint.Typeface = SKTypeface.FromFamilyName(null, SKTypefaceStyle.Bold);
            paint.TextSize = 10;

            using (SKPath textPath = paint.GetTextPath("CODE", 0, 0))
            {
                // Set transform to center and enlarge clip path to window height
                SKRect bounds;
                textPath.GetTightBounds(out bounds);

                canvas.Translate(info.Width / 2, info.Height / 2);
                canvas.Scale(info.Width / bounds.Width, info.Height / bounds.Height);
                canvas.Translate(-bounds.MidX, -bounds.MidY);

                // Set the clip path
                canvas.ClipPath(textPath);
            }
        }

        // Reset transforms
        canvas.ResetMatrix();

        // Display bitmap to fill window but maintain aspect ratio
        SKRect rect = new SKRect(0, 0, info.Width, info.Height);
        canvas.DrawBitmap(bitmap,
            rect.AspectFill(new SKSize(bitmap.Width, bitmap.Height)));
    }
}
```

Una vez que se establece el trazado de recorte, se puede mostrar el mapa de bits y se recortará en los contornos de carácter. Observe el uso del [`AspectFill`](xref:SkiaSharp.SKRect.AspectFill(SkiaSharp.SKSize)) método de `SKRect` que calcula un rectángulo para llenar la página conservando la relación de aspecto.

La página efecto de la **ruta de acceso de texto** convierte un solo carácter de y comercial en un trazado para crear un efecto de trazado 1D. A continuación, se usa un objeto Paint con este efecto de trazado para trazar el contorno de una versión mayor del mismo carácter:

[![](text-paths-images/textpatheffect-small.png "Triple screenshot of the Text Path Effect page")](text-paths-images/textpatheffect-large.png#lightbox "Triple screenshot of the Text Path Effect page")

Gran parte del trabajo en la [`TextPathEffectPath`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TextPathEffectPage.cs) clase se produce en los campos y en el constructor. Los dos `SKPaint` objetos definidos como campos se utilizan para dos propósitos diferentes: el primero (con nombre `textPathPaint` ) se usa para convertir el símbolo de y comercial con un `TextSize` de 50 en una ruta de acceso para el efecto de la ruta de acceso 1D. El segundo ( `textPaint` ) se usa para mostrar la versión más grande de la y comercial con ese efecto de trazado. Por ese motivo, el `Style` de este segundo objeto Paint se establece en `Stroke` , pero la `StrokeWidth` propiedad no se establece porque esa propiedad no es necesaria cuando se usa un efecto de ruta 1D:

```csharp
public class TextPathEffectPage : ContentPage
{
    const string character = "@";
    const float littleSize = 50;

    SKPathEffect pathEffect;

    SKPaint textPathPaint = new SKPaint
    {
        TextSize = littleSize
    };

    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black
    };

    public TextPathEffectPage()
    {
        Title = "Text Path Effect";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Get the bounds of textPathPaint
        SKRect textPathPaintBounds = new SKRect();
        textPathPaint.MeasureText(character, ref textPathPaintBounds);

        // Create textPath centered around (0, 0)
        SKPath textPath = textPathPaint.GetTextPath(character,
                                                    -textPathPaintBounds.MidX,
                                                    -textPathPaintBounds.MidY);
        // Create the path effect
        pathEffect = SKPathEffect.Create1DPath(textPath, littleSize, 0,
                                               SKPath1DPathEffectStyle.Translate);
    }
    ...
}
```

El constructor usa primero el `textPathPaint` objeto para medir el símbolo de y comercial con un `TextSize` de 50. A continuación, se pasan los negativos de las coordenadas centrales de ese rectángulo al `GetTextPath` método para convertir el texto en un trazado. La ruta de acceso resultante tiene el punto (0,0) en el centro del carácter, que es ideal para un efecto de trazado 1D.

Podría pensar que el `SKPathEffect` objeto creado al final del constructor podría establecerse en la `PathEffect` propiedad de en lugar de `textPaint` guardarse como un campo. Pero esto no funciona bien porque ha distorsionado los resultados de la `MeasureText` llamada en el `PaintSurface` controlador:

```csharp
public class TextPathEffectPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set textPaint TextSize based on screen size
        textPaint.TextSize = Math.Min(info.Width, info.Height);

        // Do not measure the text with PathEffect set!
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(character, ref textBounds);

        // Coordinates to center text on screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        // Set the PathEffect property and display text
        textPaint.PathEffect = pathEffect;
        canvas.DrawText(character, xText, yText, textPaint);
    }
}
```

Esta `MeasureText` llamada se usa para centrar el carácter en la página. Para evitar problemas, la `PathEffect` propiedad se establece en el objeto Paint una vez que se ha medido el texto, pero antes de que se muestre.

## <a name="outlines-of-character-outlines"></a>Contornos de los contornos de caracteres

Normalmente [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single)) , el método de `SKPaint` convierte una ruta de acceso a otra aplicando las propiedades de la pintura, principalmente el ancho del trazo y el efecto de la ruta de acceso. Cuando se usa sin efectos de trazado, `GetFillPath` crea eficazmente una ruta de acceso que describe otra ruta de acceso. Esto se mostró en la página de **puntear para describir la ruta de acceso** en el artículo efectos de la [**ruta de acceso**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) .

También puede llamar a `GetFillPath` en la ruta de acceso devuelta desde `GetTextPath` pero, en primer lugar, es posible que no esté totalmente seguro de lo que tendría el aspecto.

En la página **esquemas de contorno de caracteres** se muestra la técnica. Todo el código relevante está en el `PaintSurface` controlador de la [`CharacterOutlineOutlinesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CharacterOutlineOutlinesPage.cs) clase.

El constructor comienza creando un `SKPaint` objeto denominado `textPaint` con una `TextSize` propiedad basada en el tamaño de la página. Esto se convierte en una ruta de acceso mediante el `GetTextPath` método. Argumentos de la coordenada para `GetTextPath` centrar el trazado en la pantalla de forma eficaz:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint())
    {
        // Set Style for the character outlines
        textPaint.Style = SKPaintStyle.Stroke;

        // Set TextSize based on screen size
        textPaint.TextSize = Math.Min(info.Width, info.Height);

        // Measure the text
        SKRect textBounds = new SKRect();
        textPaint.MeasureText("@", ref textBounds);

        // Coordinates to center text on screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        // Get the path for the character outlines
        using (SKPath textPath = textPaint.GetTextPath("@", xText, yText))
        {
            // Create a new path for the outlines of the path
            using (SKPath outlinePath = new SKPath())
            {
                // Convert the path to the outlines of the stroked path
                textPaint.StrokeWidth = 25;
                textPaint.GetFillPath(textPath, outlinePath);

                // Stroke that new path
                using (SKPaint outlinePaint = new SKPaint())
                {
                    outlinePaint.Style = SKPaintStyle.Stroke;
                    outlinePaint.StrokeWidth = 5;
                    outlinePaint.Color = SKColors.Red;

                    canvas.DrawPath(outlinePath, outlinePaint);
                }
            }
        }
    }
}
```

`PaintSurface`A continuación, el controlador crea una nueva ruta de acceso denominada `outlinePath` . Esto se convierte en la ruta de acceso de destino en la llamada a `GetFillPath` . La `StrokeWidth` propiedad de 25 causas es `outlinePath` describir el contorno de una ruta de acceso de 25 píxeles de trazado de los caracteres de texto. Esta ruta de acceso se muestra en rojo con un ancho de trazo de 5:

[![](text-paths-images/characteroutlineoutlines-small.png "Triple screenshot of the Character Outline Outlines page")](text-paths-images/characteroutlineoutlines-large.png#lightbox "Triple screenshot of the Character Outline Outlines page")

Mire atentamente y verá superposiciones donde el contorno de la ruta de acceso crea una esquina aguda. Estos son los artefactos normales de este proceso.

## <a name="text-along-a-path"></a>Texto a lo largo de un trazado

El texto se muestra normalmente en una línea base horizontal. El texto se puede girar para que se ejecute vertical o diagonalmente, pero la línea base sigue siendo una línea recta.

No obstante, hay ocasiones en las que desea que el texto se ejecute a lo largo de una curva. Este es el propósito del [`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) método de `SKCanvas` :

```csharp
public Void DrawTextOnPath (String text, SKPath path, Single hOffset, Single vOffset, SKPaint paint)
```

El texto especificado en el primer argumento se establece para ejecutarse a lo largo de la ruta de acceso especificada como segundo argumento. Puede comenzar el texto en un desplazamiento desde el principio de la ruta de acceso con el `hOffset` argumento. Normalmente, la ruta de acceso forma la línea base del texto: los trazos de texto se encuentran en un lado del trazado y los de texto se encuentran en el otro. Sin embargo, puede desplazar la línea de base de texto desde la ruta de acceso con el `vOffset` argumento.

Este método no tiene ninguna utilidad que proporcione instrucciones sobre cómo establecer la `TextSize` propiedad de `SKPaint` para que el tamaño del texto sea perfecto para ejecutarse desde el principio del trazado hasta el final. A veces, puede averiguar el tamaño del texto por su cuenta. Otras veces necesitará usar funciones de medición de rutas de acceso que se describen en el siguiente artículo sobre la [**información y la enumeración**](information.md)de la ruta de acceso.

El programa de **texto circular** ajusta el texto alrededor de un círculo. Es fácil determinar la circunferencia de un círculo, por lo que es fácil cambiar el tamaño del texto para ajustarse exactamente. El `PaintSurface` controlador de la [`CircularTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CircularTextPage.cs) clase calcula un radio de un círculo basado en el tamaño de la página. Ese círculo se convierte en `circularPath` :

```csharp
public class CircularTextPage : ContentPage
{
    const string text = "xt in a circle that shapes the te";
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath circularPath = new SKPath())
        {
            float radius = 0.35f * Math.Min(info.Width, info.Height);
            circularPath.AddCircle(info.Width / 2, info.Height / 2, radius);

            using (SKPaint textPaint = new SKPaint())
            {
                textPaint.TextSize = 100;
                float textWidth = textPaint.MeasureText(text);
                textPaint.TextSize *= 2 * 3.14f * radius / textWidth;

                canvas.DrawTextOnPath(text, circularPath, 0, 0, textPaint);
            }
        }
    }
}
```

`TextSize` `textPaint` A continuación, se ajusta la propiedad de para que el ancho del texto coincida con la circunferencia del círculo:

[![](text-paths-images/circulartext-small.png "Triple screenshot of the Circular Text page")](text-paths-images/circulartext-large.png#lightbox "Triple screenshot of the Circular Text page")

El texto en sí también se ha elegido como circular: la palabra "círculo" es el asunto de la oración y el objeto de una frase de preposicional.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
