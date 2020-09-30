---
title: Porter-Duff modos de Blend
description: Use los modos de mezcla Porter-Duff para crear escenas basadas en imágenes de origen y de destino.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 57F172F8-BA03-43EC-A215-ED6B78696BB5
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 12e3e95b0f87d0e93d157bebe057874430866c2b
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560785"
---
# <a name="porter-duff-blend-modes"></a>Porter-Duff modos de Blend

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Los modos de mezcla Porter-Duff se denominan después de Thomas Porter y Tom Duff, que desarrollaron un álgebra de la composición mientras trabajaba con Lucasfilm. Las [_imágenes digitales de composición_](https://graphics.pixar.com/library/Compositing/paper.pdf) de papel se publicaron en el problema 1984 de julio de los _gráficos de equipos_, páginas 253 a 259. Estos modos de mezcla son esenciales para la composición, que consiste en ensamblar varias imágenes en una escena compuesta:

![Ejemplo de Porter-Duff](porter-duff-images/PorterDuffSample.png "Ejemplo de Porter-Duff")

## <a name="porter-duff-concepts"></a>Conceptos de Porter-Duff

Suponga que un rectángulo de brownish ocupa los dos tercios izquierdo y superior de la superficie de pantalla:

![Destino de Porter-Duff](porter-duff-images/PorterDuffDst.png "Destino de Porter-Duff")

Esta área se denomina _destino_ _o,_ a veces, el fondo o el _fondo_ .

Desea dibujar el rectángulo siguiente, que es el mismo tamaño del destino. El rectángulo es transparente excepto para un área azulada que ocupa los dos tercios derecho e inferior:

![Origen de Porter-Duff](porter-duff-images/PorterDuffSrc.png "Origen de Porter-Duff")

Esto se denomina _origen_ o, en ocasiones, _primer plano_.

Cuando se muestra el origen en el destino, aquí es lo que se espera:

![Porter-Duff origen sobre](porter-duff-images/PorterDuffSrcOver.png "Porter-Duff origen sobre")

Los píxeles transparentes del origen permiten mostrar el fondo, mientras que los píxeles de origen azuladas ocultan el fondo. Este es el caso normal, y se hace referencia a él en SkiaSharp como `SKBlendMode.SrcOver` . Ese valor es el valor predeterminado de la `BlendMode` propiedad cuando `SKPaint` se crea una instancia de un objeto por primera vez.

Sin embargo, es posible especificar un modo de mezcla diferente para un efecto diferente. Si especifica `SKBlendMode.DstOver` , en el área donde se entrecruzan el origen y el destino, el destino aparece en lugar del origen:

![Destino de Porter-Duff](porter-duff-images/PorterDuffDstOver.png "Destino de Porter-Duff")

El `SKBlendMode.DstIn` modo de mezcla muestra solo el área donde el destino y el origen intersecan con el color de destino:

![Destino de Porter-Duff en](porter-duff-images/PorterDuffDstIn.png "Destino de Porter-Duff en")

El modo de mezcla de `SKBlendMode.Xor` (Exclusive o) hace que no aparezca nada donde se superpongan las dos áreas:

![Porter-Duff or exclusivo](porter-duff-images/PorterDuffXor.png "Porter-Duff or exclusivo")

Los rectángulos de origen y destino de color dividen de forma eficaz la superficie de presentación en cuatro áreas únicas que se pueden colorear de varias maneras correspondientes a la presencia de los rectángulos de origen y de destino:

![Porter-Duff](porter-duff-images/PorterDuff.png "Porter-Duff")

Los rectángulos superior derecho e inferior izquierdo están siempre en blanco porque el destino y el origen son transparentes en esas áreas. El color de destino ocupa el área superior izquierda, de modo que el área puede colorearse con el color de destino o no. Del mismo modo, el color de origen ocupa el área inferior derecha, de modo que el área puede colorearse con el color de origen o no. La intersección del destino y el origen en el centro pueden estar coloreados con el color de destino, el color de origen o no estar en absoluto.

El número total de combinaciones es 2 (para la parte superior izquierda) las horas 2 (para la esquina inferior derecha) las horas 3 (para el centro) o 12. Estos son los 12 modos de composición de Porter-Duff básicos.

Hacia el final de la _composición de imágenes digitales_ (página 256), Porter y Duff agregan un modo decimotercer denominado _Plus_ (que corresponde al `SKBlendMode.Plus` miembro SkiaSharp y al modo _más ligero_ de W3C (que no se debe confundir con el modo _ligero_ de W3C). Este `Plus` modo agrega los colores de origen y de destino, un proceso que se describe con más detalle en breve.

Skia agrega un modo decimocuarto denominado `Modulate` que es muy similar a `Plus` , salvo que se multiplican los colores de origen y de destino. Se puede tratar como un modo de mezcla Porter-Duff adicional.

Estos son los 14 modos Porter-Duff, tal y como se define en SkiaSharp. En la tabla se muestra cómo se colorea cada una de las tres áreas que no están en blanco en el diagrama anterior:

| Mode       | Destination | Intersección | Source |
| ---------- |:-----------:|:------------:|:------:|
| `Clear`    |             |              |        |
| `Src`      |             | Source       | X      |
| `Dst`      | X           | Destination  |        |
| `SrcOver`  | X           | Source       | X      |
| `DstOver`  | X           | Destination  | X      |
| `SrcIn`    |             | Source       |        |
| `DstIn`    |             | Destination  |        |
| `SrcOut`   |             |              | X      |
| `DstOut`   | X           |              |        |
| `SrcATop`  | X           | Source       |        |
| `DstATop`  |             | Destination  | X      |
| `Xor`      | X           |              | X      |
| `Plus`     | X           | Sum          | X      |
| `Modulate` |             | Producto      |        | 

Estos modos de mezcla son simétricos. El origen y el destino se pueden intercambiar y todos los modos siguen estando disponibles.

La Convención de nomenclatura de los modos sigue algunas reglas sencillas:

- **Src** o **DST** por sí solo significa que solo están visibles los píxeles de origen o de destino.
- El sufijo **over** indica qué es visible en la intersección. El origen o el destino se dibujan "sobre" el otro.
- El sufijo **in** significa que solo se colorea la intersección. El resultado se restringe únicamente a la parte del origen o destino que está "en" el otro.
- El sufijo **out** significa que la intersección no se colorea. La salida es solo la parte del origen o el destino que está "fuera" de la intersección.
- El sufijo de **encima** es la Unión de **in** y **out**. Incluye el área en la que el origen o el destino están "encima" del otro.

Observe la diferencia con los `Plus` `Modulate` modos y. Estos modos están realizando un tipo diferente de cálculo en los píxeles de origen y de destino. Se describen con más detalle en breve.

La página de **cuadrícula Porter-Duff** muestra los 14 modos en una pantalla en forma de cuadrícula. Cada modo es una instancia independiente de `SKCanvasView` . Por ese motivo, una clase se deriva de `SKCanvasView` denominada `PorterDuffCanvasView` . El constructor estático crea dos mapas de bits del mismo tamaño, uno con un rectángulo brownish en su área superior izquierda y otro con un rectángulo azulada:

```csharp
class PorterDuffCanvasView : SKCanvasView
{
    static SKBitmap srcBitmap, dstBitmap;

    static PorterDuffCanvasView()
    {
        dstBitmap = new SKBitmap(300, 300);
        srcBitmap = new SKBitmap(300, 300);

        using (SKPaint paint = new SKPaint())
        {
            using (SKCanvas canvas = new SKCanvas(dstBitmap))
            {
                canvas.Clear();
                paint.Color = new SKColor(0xC0, 0x80, 0x00);
                canvas.DrawRect(new SKRect(0, 0, 200, 200), paint);
            }
            using (SKCanvas canvas = new SKCanvas(srcBitmap))
            {
                canvas.Clear();
                paint.Color = new SKColor(0x00, 0x80, 0xC0);
                canvas.DrawRect(new SKRect(100, 100, 300, 300), paint);
            }
        }
    }
    ···
}
```

El constructor de instancia tiene un parámetro de tipo `SKBlendMode` . Guarda este parámetro en un campo. 

```csharp
class PorterDuffCanvasView : SKCanvasView
{
    ···
    SKBlendMode blendMode;

    public PorterDuffCanvasView(SKBlendMode blendMode)
    {
        this.blendMode = blendMode;
    }

    protected override void OnPaintSurface(SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find largest square that fits
        float rectSize = Math.Min(info.Width, info.Height);
        float x = (info.Width - rectSize) / 2;
        float y = (info.Height - rectSize) / 2;
        SKRect rect = new SKRect(x, y, x + rectSize, y + rectSize);

        // Draw destination bitmap
        canvas.DrawBitmap(dstBitmap, rect);

        // Draw source bitmap
        using (SKPaint paint = new SKPaint())
        {
            paint.BlendMode = blendMode;
            canvas.DrawBitmap(srcBitmap, rect, paint);
        }

        // Draw outline
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 2;
            rect.Inflate(-1, -1);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

La `OnPaintSurface` invalidación dibuja los dos mapas de bits. El primero se dibuja normalmente:

```csharp
canvas.DrawBitmap(dstBitmap, rect);
```

La segunda se dibuja con un `SKPaint` objeto en el que la propiedad se ha `BlendMode` establecido en el argumento del constructor:

```csharp
using (SKPaint paint = new SKPaint())
{
    paint.BlendMode = blendMode;
    canvas.DrawBitmap(srcBitmap, rect, paint);
}
```

El resto de la `OnPaintSurface` invalidación dibuja un rectángulo alrededor del mapa de bits para indicar su tamaño.

La `PorterDuffGridPage` clase crea catorce instancias de `PorterDurffCanvasView` , una por cada miembro de la `blendModes` matriz. El orden de los `SKBlendModes` miembros de la matriz es un poco diferente de la tabla para colocar modos similares adyacentes entre sí. Las 14 instancias de `PorterDuffCanvasView` se organizan junto con las etiquetas en un `Grid` :

```csharp
public class PorterDuffGridPage : ContentPage
{
    public PorterDuffGridPage()
    {
        Title = "Porter-Duff Grid";

        SKBlendMode[] blendModes =
        {
            SKBlendMode.Src, SKBlendMode.Dst, SKBlendMode.SrcOver, SKBlendMode.DstOver,
            SKBlendMode.SrcIn, SKBlendMode.DstIn, SKBlendMode.SrcOut, SKBlendMode.DstOut,
            SKBlendMode.SrcATop, SKBlendMode.DstATop, SKBlendMode.Xor, SKBlendMode.Plus,
            SKBlendMode.Modulate, SKBlendMode.Clear
        };

        Grid grid = new Grid
        {
            Margin = new Thickness(5)
        };

        for (int row = 0; row < 4; row++)
        {
            grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
            grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Star });
        }

        for (int col = 0; col < 3; col++)
        {
            grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Star });
        }

        for (int i = 0; i < blendModes.Length; i++)
        {
            SKBlendMode blendMode = blendModes[i];
            int row = 2 * (i / 4);
            int col = i % 4;

            Label label = new Label
            {
                Text = blendMode.ToString(),
                HorizontalTextAlignment = TextAlignment.Center
            };
            Grid.SetRow(label, row);
            Grid.SetColumn(label, col);
            grid.Children.Add(label);

            PorterDuffCanvasView canvasView = new PorterDuffCanvasView(blendMode);

            Grid.SetRow(canvasView, row + 1);
            Grid.SetColumn(canvasView, col);
            grid.Children.Add(canvasView);
        }

        Content = grid;
    }
}
```

Este es el resultado:

[![Porter-Duff Grid](porter-duff-images/PorterDuffGrid.png "Porter-Duff Grid")](porter-duff-images/PorterDuffGrid-Large.png#lightbox)

Querrá convencerse de que la transparencia es fundamental para el funcionamiento correcto de los modos de mezcla Porter-Duff. La `PorterDuffCanvasView` clase contiene un total de tres llamadas al `Canvas.Clear` método. Todos ellos usan el método sin parámetros, que establece todos los píxeles en transparentes:

```csharp
canvas.Clear();
```

Intente cambiar cualquiera de esas llamadas para que los píxeles se establezcan en blanco opaco:

```csharp
canvas.Clear(SKColors.White);
```

Después de ese cambio, algunos de los modos de fusión parecerá que funcionan, pero otros no. Si establece el fondo del mapa de bits de origen en blanco, el `SrcOver` modo no funciona porque no hay píxeles transparentes en el mapa de bits de origen para permitir que el destino se muestre. Si establece el fondo del mapa de bits de destino o el lienzo en blanco, `DstOver` no funcionará porque el destino no tiene píxeles transparentes.

Podría haber una tentación de reemplazar los mapas de bits en la página de **cuadrícula Porter-Duff** con `DrawRect` llamadas más sencillas. Que funcionará para el rectángulo de destino, pero no para el rectángulo de origen. El rectángulo de origen debe englobar algo más que el área de color azulada. El rectángulo de origen debe incluir un área transparente que se corresponda con el área de color del destino. Solo se aplicarán estos modos de mezcla.

## <a name="using-mattes-with-porter-duff"></a>Uso de Matters con Porter-Duff

La página de composición de la **pared del brick** muestra un ejemplo de una tarea de composición clásica: una imagen se debe ensamblar desde varias partes, incluido un mapa de bits con un fondo que se debe eliminar. Este es el **SeatedMonkey.jpg** mapa de bits con el fondo problemático:

![Monkey sentado](porter-duff-images/SeatedMonkey.jpg "Monkey sentado")

Como preparación para la composición, se creó un _mate_ correspondiente, que es otro mapa de bits que es negro donde desea que aparezca la imagen y sea transparente en caso contrario. Este archivo se denomina **SeatedMonkeyMatte.png** y se encuentra entre los recursos de la carpeta **multimedia** en el ejemplo [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) :

![Mate Monkey colocado](porter-duff-images/SeatedMonkeyMatte.png "Mate Monkey colocado")

_No_ se trata de un mate creado por el experto. De forma óptima, el mate debe incluir píxeles parcialmente transparentes alrededor del borde de los píxeles negros y este mate no.

El archivo XAML de la página de **composición de la pared del brick** crea instancias de un `SKCanvasView` y un `Button` que guía al usuario a través del proceso de creación de la imagen final:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BrickWallCompositingPage"
             Title="Brick-Wall Compositing">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Button Text="Show sitting monkey"
                HorizontalOptions="Center"
                Margin="0, 10"
                Clicked="OnButtonClicked" />

    </StackLayout>
</ContentPage>
```

El archivo de código subyacente carga los dos mapas de bits que necesita y controla el `Clicked` evento de `Button` . Para cada `Button` clic, el `step` campo se incrementa y `Text` se establece una nueva propiedad para `Button` . Cuando `step` llega a 5, se vuelve a establecer en 0:

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    SKBitmap monkeyBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BrickWallCompositingPage), 
        "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    SKBitmap matteBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BrickWallCompositingPage), 
        "SkiaSharpFormsDemos.Media.SeatedMonkeyMatte.png");

    int step = 0;

    public BrickWallCompositingPage ()
    {
        InitializeComponent ();
    }

    void OnButtonClicked(object sender, EventArgs args)
    {
        Button btn = (Button)sender;
        step = (step + 1) % 5;

        switch (step)
        {
            case 0: btn.Text = "Show sitting monkey"; break;
            case 1: btn.Text = "Draw matte with DstIn"; break;
            case 2: btn.Text = "Draw sidewalk with DstOver"; break;
            case 3: btn.Text = "Draw brick wall with DstOver"; break;
            case 4: btn.Text = "Reset"; break;
        }

        canvasView.InvalidateSurface();
    }
    
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        ···
    }
}
```

Cuando el programa se ejecuta por primera vez, no hay nada visible excepto `Button` :

[![Paso 0 de composición de la pared de ladrillo](porter-duff-images/BrickWallCompositing0.png "Paso 0 de composición de la pared de ladrillo")](porter-duff-images/BrickWallCompositing0-Large.png#lightbox)

Si presiona la `Button` vez, `step` se incrementará a 1 y el `PaintSurface` controlador muestra ahora **SeatedMonkey.jpg**:

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        float x = (info.Width - monkeyBitmap.Width) / 2;
        float y = info.Height - monkeyBitmap.Height;

        // Draw monkey bitmap
        if (step >= 1)
        {
            canvas.DrawBitmap(monkeyBitmap, x, y);
        }
        ···
    }
}
```

No hay ningún `SKPaint` objeto y, por lo tanto, ningún modo de mezcla. El mapa de bits aparece en la parte inferior de la pantalla:

[![Paso 1 de composición de la pared de ladrillo](porter-duff-images/BrickWallCompositing1.png "Paso 1 de composición de la pared de ladrillo")](porter-duff-images/BrickWallCompositing1-Large.png#lightbox)

Presione de `Button` nuevo y `step` aumente a 2. Este es el paso fundamental para mostrar el archivo **SeatedMonkeyMatte.png** :

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Draw matte to exclude monkey's surroundings
        if (step >= 2)
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.BlendMode = SKBlendMode.DstIn;
                canvas.DrawBitmap(matteBitmap, x, y, paint);
            }
        }
        ···
    }
}
```

El modo de mezcla es `SKBlendMode.DstIn` , lo que significa que el destino se conservará en las áreas correspondientes a las áreas no transparentes del origen. El resto del rectángulo de destino correspondiente al mapa de bits original se vuelve transparente:

[![Paso 2 de composición de la pared de ladrillo](porter-duff-images/BrickWallCompositing2.png "Paso 2 de composición de la pared de ladrillo")](porter-duff-images/BrickWallCompositing2-Large.png#lightbox)

Se ha quitado el fondo. 

El siguiente paso consiste en dibujar un rectángulo que se parezca a un Sidewalk en el que está sentado el Monkey. La apariencia de este Sidewalk se basa en una composición de dos sombreadores: un sombreador de color sólido y un sombreador de ruido de Perlen:

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        const float sidewalkHeight = 80;
        SKRect rect = new SKRect(info.Rect.Left, info.Rect.Bottom - sidewalkHeight,
                                 info.Rect.Right, info.Rect.Bottom);

        // Draw gravel sidewalk for monkey to sit on
        if (step >= 3)
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.Shader = SKShader.CreateCompose(
                                    SKShader.CreateColor(SKColors.SandyBrown),
                                    SKShader.CreatePerlinNoiseTurbulence(0.1f, 0.3f, 1, 9));

                paint.BlendMode = SKBlendMode.DstOver;
                canvas.DrawRect(rect, paint);
            }
        }
        ···
    }
}
```

Dado que este Sidewalk debe ir detrás de Monkey, el modo de mezcla es `DstOver` . El destino solo aparece cuando el fondo es transparente:

[![Paso 3 de composición de la pared de ladrillo](porter-duff-images/BrickWallCompositing3.png "Paso 3 de composición de la pared de ladrillo")](porter-duff-images/BrickWallCompositing3-Large.png#lightbox)

El último paso es agregar un muro de bricks. El programa usa el icono de mapa de bits de la pared del brick disponible como la propiedad estática `BrickWallTile` en la `AlgorithmicBrickWallPage` clase. Se agrega una transformación de traslación a la `SKShader.CreateBitmap` llamada para desplazar los mosaicos de modo que la fila inferior sea un icono completo:

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Draw bitmap tiled brick wall behind monkey
        if (step >= 4)
        {
            using (SKPaint paint = new SKPaint())
            {
                SKBitmap bitmap = AlgorithmicBrickWallPage.BrickWallTile;
                float yAdjust = (info.Height - sidewalkHeight) % bitmap.Height;

                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat,
                                                     SKMatrix.MakeTranslation(0, yAdjust));
                paint.BlendMode = SKBlendMode.DstOver;
                canvas.DrawRect(info.Rect, paint);
            }
        }
    }
}
```

Para mayor comodidad, la `DrawRect` llamada muestra este sombreador en todo el lienzo, pero el `DstOver` modo limita la salida a solo el área del lienzo que sigue siendo transparente:

[![Paso 4 de composición de la pared de ladrillo](porter-duff-images/BrickWallCompositing4.png "Paso 4 de composición de la pared de ladrillo")](porter-duff-images/BrickWallCompositing4-Large.png#lightbox)

Obviamente, hay otras maneras de crear esta escena. Se puede crear a partir de un segundo plano y avanzar hasta el primer plano. Sin embargo, el uso de los modos de mezcla ofrece más flexibilidad. En concreto, el uso del mate permite que el fondo de un mapa de bits se excluya de la escena compuesta.

Como aprendió en el [recorte del artículo con rutas de acceso y regiones](../../curves/clipping.md), la `SKCanvas` clase define tres tipos de recortes, que corresponden a los [`ClipRect`](xref:SkiaSharp.SKCanvas.ClipRect*) [`ClipPath`](xref:SkiaSharp.SKCanvas.ClipPath*) métodos, y [`ClipRegion`](xref:SkiaSharp.SKCanvas.ClipRegion*) . Los modos de mezcla Porter-Duff agregan otro tipo de recorte, lo que permite restringir una imagen a todo lo que se puede dibujar, incluidos los mapas de bits. Esencialmente, el mate usado en la **composición de la pared del brick** define un área de recorte.

## <a name="gradient-transparency-and-transitions"></a>Transparencia y transiciones de degradado

En los ejemplos de los modos de mezcla Porter-Duff mostrados anteriormente en este artículo se incluyen todas las imágenes implicadas de píxeles opacos y píxeles transparentes, pero no píxeles parcialmente transparentes. También se definen las funciones de modo de mezcla para esos píxeles. La tabla siguiente es una definición más formal de los modos de mezcla Porter-Duff que usa la notación que se encuentra en la referencia de Skia [**SkBlendMode**](https://skia.org/user/api/SkBlendMode_Reference). (Dado **que la referencia de SkBlendMode** es una referencia de Skia, se usa la sintaxis de C++).

Conceptualmente, los componentes rojo, verde, azul y alfa de cada píxel se convierten de bytes a números de punto flotante en el intervalo de 0 a 1. En el canal alfa, 0 es completamente transparente y 1 es totalmente opaco.

La notación de la tabla siguiente usa las siguientes abreviaturas:

- **Da** es el canal alfa de destino
- **DC** es el color RGB de destino
- **SA** es el canal alfa de origen
- **SC** es el color RGB de origen

Los colores RGB se multiplican previamente por el valor alfa. Por ejemplo, si **SC** representa rojo puro pero **SA** es 0X80, el color RGB es **(0x80, 0, 0)**. Si **SA** es 0, todos los componentes RGB también son cero.

El resultado se muestra entre corchetes con el canal alfa y el color RGB separado por una coma: **[Alpha, color]**. Para el color, el cálculo se realiza por separado para los componentes rojo, verde y azul:

| Mode       | Operación |
| ---------- | --------- |
| `Clear`    | [0,0]    |
| `Src`      | [SA, SC]  |
| `Dst`      | [Da, DC]  |
| `SrcOver`  | [SA + da · (1 – SA), SC + DC · (1 – SA) | 
| `DstOver`  | [Da + SA · (1 – da), DC + SC · (1 – da) |
| `SrcIn`    | Valida Da, SC · & |
| `DstIn`    | & SA, DC · Valida |
| `SrcOut`   | Valida (1 – da), SC · (1 – da)] |
| `DstOut`   | & (1 – SA), DC · (1 – SA)] |
| `SrcATop`  | [Da, SC · Da + DC · (1 – SA)] |
| `DstATop`  | [SA, DC · SA + SC · (1 – da)] |
| `Xor`      | [SA + da – 2 · Valida Da, SC · (1 – da) + DC · (1 – SA)] |
| `Plus`     | [SA + da, SC + DC] |
| `Modulate` | Valida Da, SC · CD | 

Estas operaciones son más fáciles de analizar cuando **da** y **SA** son 0 o 1. Por ejemplo, para el `SrcOver` modo predeterminado, si **SA** es 0, **SC** también es 0 y el resultado es **[da, DC]**, el alfa y el color de destino. Si **SA** es 1, el resultado es **[SA, SC]**, el alfa de origen y el color, o **[1, SC]**.

Los `Plus` `Modulate` modos y son ligeramente diferentes de los demás en que los nuevos colores pueden ser el resultado de la combinación del origen y del destino. El `Plus` modo se puede interpretar con componentes de byte o con componentes de punto flotante. En la página de **cuadrícula Porter-Duff** mostrada anteriormente, el color de destino es **(0xC0, 0x80, 0x00)** y el color de origen es **(0x00, 0x80, 0xC0)**. Cada par de componentes se agrega pero la suma se fija en 0xFF. El resultado es el color **(0xC0, 0xFF, 0xC0)**. Este es el color que se muestra en la intersección.

En el `Modulate` modo, los valores RGB se deben convertir en punto flotante. El color de destino es **(0,75, 0,5, 0)** y el origen es **(0, 0,5, 0,75)**. Los componentes RGB se multiplican juntos y el resultado es **(0, 0,25, 0)**. Este es el color que se muestra en la intersección de la página de **cuadrícula Porter-Duff** para este modo.

La página **de transparencia Porter-Duff** permite examinar cómo funcionan los modos de mezcla Porter-Duff en objetos gráficos que son parcialmente transparentes. El archivo XAML incluye un `Picker` con los modos Porter-Duff:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.PorterDuffTransparencyPage"
             Title="Porter-Duff Transparency">

    <StackLayout>
        <skiaviews:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blendModePicker"
                Title="Blend Mode"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlendMode}">
                    <x:Static Member="skia:SKBlendMode.Clear" />
                    <x:Static Member="skia:SKBlendMode.Src" />
                    <x:Static Member="skia:SKBlendMode.Dst" />
                    <x:Static Member="skia:SKBlendMode.SrcOver" />
                    <x:Static Member="skia:SKBlendMode.DstOver" />
                    <x:Static Member="skia:SKBlendMode.SrcIn" />
                    <x:Static Member="skia:SKBlendMode.DstIn" />
                    <x:Static Member="skia:SKBlendMode.SrcOut" />
                    <x:Static Member="skia:SKBlendMode.DstOut" />
                    <x:Static Member="skia:SKBlendMode.SrcATop" />
                    <x:Static Member="skia:SKBlendMode.DstATop" />
                    <x:Static Member="skia:SKBlendMode.Xor" />
                    <x:Static Member="skia:SKBlendMode.Plus" />
                    <x:Static Member="skia:SKBlendMode.Modulate" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                3
            </Picker.SelectedIndex>
        </Picker>
    </StackLayout>
</ContentPage>
```

El archivo de código subyacente rellena dos rectángulos del mismo tamaño con un degradado lineal. El degradado de destino es desde la esquina superior derecha hacia la parte inferior izquierda. Se brownish en la esquina superior derecha, pero, a continuación, hacia el centro comienza la difuminación a transparente y es transparente en la esquina inferior izquierda. 

El rectángulo de origen tiene un degradado de la parte superior izquierda a la esquina inferior derecha. La esquina superior izquierda es azulada, pero de nuevo se atenúa a transparente y es transparente en la esquina inferior derecha. 

```csharp
public partial class PorterDuffTransparencyPage : ContentPage
{
    public PorterDuffTransparencyPage()
    {
        InitializeComponent();
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

        // Make square display rectangle smaller than canvas
        float size = 0.9f * Math.Min(info.Width, info.Height);
        float x = (info.Width - size) / 2;
        float y = (info.Height - size) / 2;
        SKRect rect = new SKRect(x, y, x + size, y + size);

        using (SKPaint paint = new SKPaint())
        {
            // Draw destination
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Right, rect.Top),
                                new SKPoint(rect.Left, rect.Bottom),
                                new SKColor[] { new SKColor(0xC0, 0x80, 0x00),
                                                new SKColor(0xC0, 0x80, 0x00, 0) },
                                new float[] { 0.4f, 0.6f },
                                SKShaderTileMode.Clamp);

            canvas.DrawRect(rect, paint);

            // Draw source
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Left, rect.Top),
                                new SKPoint(rect.Right, rect.Bottom),
                                new SKColor[] { new SKColor(0x00, 0x80, 0xC0), 
                                                new SKColor(0x00, 0x80, 0xC0, 0) },
                                new float[] { 0.4f, 0.6f },
                                SKShaderTileMode.Clamp);

            // Get the blend mode from the picker
            paint.BlendMode = blendModePicker.SelectedIndex == -1 ? 0 :
                                    (SKBlendMode)blendModePicker.SelectedItem;

            canvas.DrawRect(rect, paint);

            // Stroke surrounding rectangle
            paint.Shader = null;
            paint.BlendMode = SKBlendMode.SrcOver;
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 3;
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Este programa demuestra que los modos de mezcla Porter-Duff se pueden usar con objetos gráficos distintos de los mapas de bits. Sin embargo, el origen debe incluir un área transparente. Este es el caso aquí porque el degradado rellena el rectángulo, pero parte del degradado es transparente.

A continuación se muestran tres ejemplos:

[![Transparencia Porter-Duff](porter-duff-images/PorterDuffTransparency.png "Transparencia Porter-Duff")](porter-duff-images/PorterDuffTransparency-Large.png#lightbox)

La configuración del destino y el origen son muy similares a los diagramas que se muestran en la página 255 de las [_imágenes digitales de composición_](https://graphics.pixar.com/library/Compositing/paper.pdf) de Porter-Duff originales, pero en esta página se muestra cómo se comportan correctamente los modos de mezcla para las áreas de transparencia parcial.

Puede usar degradados transparentes para algunos efectos diferentes. Una posibilidad es enmascaramiento, que es similar a la técnica que se muestra en la sección [**degradados radiales para enmascaramiento**](../shaders/circular-gradients.md#radial-gradients-for-masking) de la **Página de degradados circulares SkiaSharp**. Gran parte de la página de la **máscara de composición** es similar a la del programa anterior. Carga un recurso de mapa de bits y determina un rectángulo en el que se va a mostrar. Un degradado radial se crea en función de un centro y un radio predeterminados:

```csharp
public class CompositingMaskPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(CompositingMaskPage),
        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    static readonly SKPoint CENTER = new SKPoint(180, 300);
    static readonly float RADIUS = 120;

    public CompositingMaskPage ()
    {
        Title = "Compositing Mask";

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

        // Find rectangle to display bitmap
        float scale = Math.Min((float)info.Width / bitmap.Width,
                               (float)info.Height / bitmap.Height);

        SKRect rect = SKRect.Create(scale * bitmap.Width, scale * bitmap.Height);

        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Display bitmap in rectangle
        canvas.DrawBitmap(bitmap, rect);

        // Adjust center and radius for scaled and offset bitmap
        SKPoint center = new SKPoint(scale * CENTER.X + x,
                                        scale * CENTER.Y + y);
        float radius = scale * RADIUS;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                center,
                                radius,
                                new SKColor[] { SKColors.Black,
                                                SKColors.Transparent },
                                new float[] { 0.6f, 1 },
                                SKShaderTileMode.Clamp);

            paint.BlendMode = SKBlendMode.DstIn;

            // Display rectangle using that gradient and blend mode
            canvas.DrawRect(rect, paint);
        }

        canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
    }
}
```

La diferencia con este programa es que el degradado comienza con negro en el centro y termina con transparencia. Se muestra en el mapa de bits con un modo de mezcla de `DstIn` , que muestra el destino solo en las áreas del origen que no son transparentes.

Después de la `DrawRect` llamada, toda la superficie del lienzo es transparente, salvo el círculo definido por el degradado radial. Se realiza una llamada final:

```csharp
canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
```

Todas las áreas transparentes del lienzo son de color rosa:

[![Máscara de composición](porter-duff-images/CompositingMask.png "Máscara de composición")](porter-duff-images/CompositingMask-Large.png#lightbox)

También puede usar los modos Porter-Duff y degradados parcialmente transparentes para las transiciones de una imagen a otra. La página **transiciones de degradado** incluye un `Slider` para indicar un nivel de progreso en la transición de 0 a 1, y `Picker` para elegir el tipo de transición que desee:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.GradientTransitionsPage"
             Title="Gradient Transitions">
    
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="progressSlider"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference progressSlider},
                              Path=Value,
                              StringFormat='Progress = {0:F2}'}"
               HorizontalTextAlignment="Center" />

        <Picker x:Name="transitionPicker" 
                Title="Transition" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />
        
    </StackLayout>
</ContentPage>
```

El archivo de código subyacente carga dos recursos de mapa de bits para mostrar la transición. Se trata de las mismas dos imágenes que se usan en la página de **resolución de mapas de bits** anteriormente en este artículo. El código también define una enumeración con tres miembros que corresponden a tres tipos de degradados &mdash; lineales, radiales y de barrido. Estos valores se cargan en `Picker` :

```csharp
public partial class GradientTransitionsPage : ContentPage
{
    SKBitmap bitmap1 = BitmapExtensions.LoadBitmapResource(
        typeof(GradientTransitionsPage),
        "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    SKBitmap bitmap2 = BitmapExtensions.LoadBitmapResource(
        typeof(GradientTransitionsPage),
        "SkiaSharpFormsDemos.Media.FacePalm.jpg");

    enum TransitionMode
    {
        Linear,
        Radial,
        Sweep
    };

    public GradientTransitionsPage ()
    {
        InitializeComponent ();

        foreach (TransitionMode mode in Enum.GetValues(typeof(TransitionMode)))
        {
            transitionPicker.Items.Add(mode.ToString());
        }

        transitionPicker.SelectedIndex = 0;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }
    ···
}
```

El archivo de código subyacente crea tres `SKPaint` objetos. El `paint0` objeto no usa un modo de mezcla. Este objeto Paint se usa para dibujar un rectángulo con un degradado que va de negro a transparente, tal y como se indica en la `colors` matriz. La `positions` matriz se basa en la posición de `Slider` , pero se ajusta en cierto modo. Si el valor de `Slider` es como mínimo o máximo, los `progress` valores son 0 o 1, y uno de los dos mapas de bits debe estar totalmente visible. La `positions` matriz se debe establecer en consecuencia para esos valores.

Si el `progress` valor es 0, la `positions` matriz contiene los valores-0,1 y 0. SkiaSharp ajustará ese primer valor para que sea igual a 0, lo que significa que el degradado es negro solo en 0 y transparente en caso contrario. Cuando `progress` es 0,5, la matriz contiene los valores 0,45 y 0,55. El degradado es negro de 0 a 0,45, después realiza la transición a transparente y es completamente transparente de 0,55 a 1. Cuando `progress` es 1, la `positions` matriz es 1 y 1,1, lo que significa que el degradado es negro de 0 a 1.

Las `colors` `position` matrices y se usan en los tres métodos de `SKShader` que crean un degradado. Solo uno de estos sombreadores se crea en función de la `Picker` selección: 

```csharp
public partial class GradientTransitionsPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Assume both bitmaps are square for display rectangle
        float size = Math.Min(info.Width, info.Height);
        SKRect rect = SKRect.Create(size, size);
        float x = (info.Width - size) / 2;
        float y = (info.Height - size) / 2;
        rect.Offset(x, y);

        using (SKPaint paint0 = new SKPaint())
        using (SKPaint paint1 = new SKPaint())
        using (SKPaint paint2 = new SKPaint())
        {
            SKColor[] colors = new SKColor[] { SKColors.Black,
                                               SKColors.Transparent };

            float progress = (float)progressSlider.Value;

            float[] positions = new float[]{ 1.1f * progress - 0.1f,
                                             1.1f * progress };

            switch ((TransitionMode)transitionPicker.SelectedIndex)
            {
                case TransitionMode.Linear:
                    paint0.Shader = SKShader.CreateLinearGradient(
                                        new SKPoint(rect.Left, 0),
                                        new SKPoint(rect.Right, 0),
                                        colors,
                                        positions,
                                        SKShaderTileMode.Clamp);
                    break;

                case TransitionMode.Radial:
                    paint0.Shader = SKShader.CreateRadialGradient(
                                        new SKPoint(rect.MidX, rect.MidY),
                                        (float)Math.Sqrt(Math.Pow(rect.Width / 2, 2) +
                                                         Math.Pow(rect.Height / 2, 2)),
                                        colors,
                                        positions,
                                        SKShaderTileMode.Clamp);
                    break;

                case TransitionMode.Sweep:
                    paint0.Shader = SKShader.CreateSweepGradient(
                                        new SKPoint(rect.MidX, rect.MidY),
                                        colors,
                                        positions);
                    break;
            }

            canvas.DrawRect(rect, paint0);

            paint1.BlendMode = SKBlendMode.SrcOut;
            canvas.DrawBitmap(bitmap1, rect, paint1);

            paint2.BlendMode = SKBlendMode.DstOver;
            canvas.DrawBitmap(bitmap2, rect, paint2);
        }
    }
}
```

Ese degradado se muestra en el rectángulo sin un modo de mezcla. Después de esa `DrawRect` llamada, el lienzo simplemente contiene un degradado de negro a transparente. La cantidad de negro aumenta con `Slider` valores superiores.

En las cuatro instrucciones finales del `PaintSurface` controlador, se muestran los dos mapas de bits. El `SrcOut` modo de mezcla significa que el primer mapa de bits solo se muestra en las áreas transparentes del fondo. El `DstOver` modo para el segundo mapa de bits significa que el segundo mapa de bits solo se muestra en las áreas en las que no se muestra el primer mapa de bits.

En las siguientes capturas de pantallas se muestran tres tipos diferentes de transiciones, cada uno en la marca 50%:

[![Transiciones de degradado](porter-duff-images/GradientTransitions.png "Transiciones de degradado")](porter-duff-images/GradientTransitions-Large.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)