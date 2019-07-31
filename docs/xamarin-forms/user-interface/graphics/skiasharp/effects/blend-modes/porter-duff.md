---
title: Modos de fusión Porter Duff
description: Use los modos de mezcla Porter Duff para redactar escenas basadas en imágenes de origen y destino.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 57F172F8-BA03-43EC-A215-ED6B78696BB5
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 60de4b8abc200ac7892838765210167f8a79dcfe
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645842"
---
# <a name="porter-duff-blend-modes"></a>Modos de fusión Porter Duff

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Los modos de mezcla Porter Duff nombrados de Thomas Porter y Tom Duff, que desarrolló un álgebra de la composición mientras trabajaba para Lucasfilm. Su papel [ _la composición de imágenes digitales_ ](https://graphics.pixar.com/library/Compositing/paper.pdf) se publicó en el número de julio de 1984 de _gráficos para PC_, páginas de 253 a 259. Estos modos de fusión son esenciales para la composición, que está ensamblando varias imágenes en una escena de composición:

![Ejemplo de Porter Duff](porter-duff-images/PorterDuffSample.png "Porter Duff ejemplo")

## <a name="porter-duff-concepts"></a>Conceptos de Porter Duff

Supongamos que un rectángulo de color entre ocupa el superior e izquierdo dos tercios de la superficie de pantalla:

![Destino de Porter Duff](porter-duff-images/PorterDuffDst.png "Porter Duff destino")

Esta área se denomina el _destino_ o a veces la _en segundo plano_ o _telón de fondo_.

Desea dibujar el rectángulo siguiente, que es el mismo tamaño de destino. El rectángulo es transparente, excepto un área azulado que ocupa el derecho e inferior dos tercios:

![Origen de Porter Duff](porter-duff-images/PorterDuffSrc.png "Porter Duff origen")

Esto se denomina la _origen_ o a veces la _primer plano_.

Cuando el origen se muestra en el destino, aquí es lo que espera:

![Origen Porter Duff sobre](porter-duff-images/PorterDuffSrcOver.png "Porter Duff código de origen")

Los píxeles transparentes del origen de permitir que el fondo mostrar a través, mientras que los píxeles de origen azulado oscurecen el fondo. Ese es el caso normal, y se conoce en SkiaSharp como `SKBlendMode.SrcOver`. Que el valor es el valor predeterminado de la `BlendMode` propiedad cuando un `SKPaint` primero se crea una instancia del objeto.

Sin embargo, es posible especificar un modo blend diferente para un efecto diferente. Si especifica `SKBlendMode.DstOver`, a continuación, en el área de intersección entre el origen y destino, el destino aparece en lugar del origen:

![Destino Porter Duff sobre](porter-duff-images/PorterDuffDstOver.png "Porter Duff destino a través de")

El `SKBlendMode.DstIn` modo blend muestra solo el área donde el origen y destino forman una intersección con el color de destino:

![Destino de Porter Duff en](porter-duff-images/PorterDuffDstIn.png "destino Porter Duff en")

El modo de mezcla de `SKBlendMode.Xor` (OR exclusivo) hace que no hay nada que aparecen que se superponen a las dos áreas:

![Porter Duff exclusivo o](porter-duff-images/PorterDuffXor.png "Porter Duff exclusivo o")

Los rectángulos coloreados de origen y destino dividen eficazmente la superficie de visualización en cuatro áreas únicas que se pueden colorear de correspondiente a la presencia de los rectángulos de origen y destino de varias maneras:

![Porter Duff](porter-duff-images/PorterDuff.png "Porter Duff")

Los rectángulos superior derecha e inferior izquierda siempre están en blanco porque el origen y destino son transparentes en esas áreas. El color de destino ocupa el área superior izquierda, por lo que o bien se puede colorear área con el color de destino o no en absoluto. De forma similar, el color de origen ocupa el área inferior derecha, para que se puede colorear área con el color de origen o no en absoluto. La intersección del destino y origen en la parte central puede ser coloreada con el color de destino, el color de origen, o no en absoluto.

El número total de combinaciones es 2 (para la esquina superior izquierda) veces 2 (para la esquina inferior derecha) veces 3 (para el centro) o 12. Estos son los modos de composición Porter Duff básicos 12.

Hacia el final de _la composición de imágenes digitales_ (página 256), Porter y Duff agregan un modo 13 denominado _plus_ (correspondiente a la SkiaSharp `SKBlendMode.Plus` miembro y el W3C _más claro_  modo (que consiste en que no se deben confundir con el W3C _aclarar_ modo.) Esto `Plus` modo agrega los colores de origen y destino, un proceso que se describirán más detalladamente en breve.

Skia agrega un modo 14 denominado `Modulate` que es muy similar a `Plus` , salvo que se multiplican los colores de origen y destino. Se puede tratar como un modo de blend Porter Duff adicional.

Estos son los modos de Porter Duff 14, tal como se define en SkiaSharp. La tabla muestra cómo color cada una de las tres áreas en blanco en el diagrama anterior:

| Modo       | Destino | Intersección | Origen |
| ---------- |:-----------:|:------------:|:------:|
| `Clear`    |             |              |        |
| `Src`      |             | Origen       | X      |
| `Dst`      | X           | Destino  |        |
| `SrcOver`  | X           | Origen       | X      |
| `DstOver`  | X           | Destino  | X      |
| `SrcIn`    |             | Origen       |        |
| `DstIn`    |             | Destino  |        |
| `SrcOut`   |             |              | X      |
| `DstOut`   | X           |              |        |
| `SrcATop`  | X           | Origen       |        |
| `DstATop`  |             | Destino  | X      |
| `Xor`      | X           |              | X      |
| `Plus`     | X           | Sum          | X      |
| `Modulate` |             | Producto      |        | 

Estos modos de fusión son simétricos. Se pueden intercambiar el origen y destino y todos los modos de siguen estando disponibles.

La convención de nomenclatura de los modos sigue unas reglas sencillas:

- **Src** o **Dst** por sí solo, significa que sólo los píxeles de origen o destino son visibles.
- El **sobre** sufijo indica lo que está visible en la intersección. El origen o el destino se dibuja "u" otro.
- El **en** sufijo significa que sólo la intersección está coloreada. El resultado se restringe a solo la parte del origen o destino que sea "in" de la otra.
- El **Out** sufijo significa que no se colorea la intersección. El resultado es sólo la parte del origen o destino que sea "out" de la intersección.
- El **encima** sufijo es la unión de **en** y **Out**. Incluye el área donde el origen o destino es "en" de la otra.

Tenga en cuenta la diferencia con el `Plus` y `Modulate` modos. Estos modos están realizando un tipo diferente de cálculo en los píxeles de origen y destino. Se describen más detalladamente en breve.

El **Porter Duff cuadrícula** página muestra todos los modos de 14 en una pantalla en forma de una cuadrícula. Cada modo es una instancia independiente de `SKCanvasView`. Por ese motivo, se deriva una clase `SKCanvasView` denominado `PorterDuffCanvasView`. El constructor estático crea dos mapas de bits del mismo tamaño, uno con un rectángulo de color entre en su área superior izquierda y otro con un rectángulo azulado:

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

El constructor de instancia tiene un parámetro de tipo `SKBlendMode`. Este parámetro guarda en un campo. 

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

El `OnPaintSurface` invalidación dibuja los dos mapas de bits. El primero se dibuja con normalidad:

```csharp
canvas.DrawBitmap(dstBitmap, rect);
```

El segundo se dibuja con un `SKPaint` objeto donde el `BlendMode` propiedad se estableció en el argumento del constructor:

```csharp
using (SKPaint paint = new SKPaint())
{
    paint.BlendMode = blendMode;
    canvas.DrawBitmap(srcBitmap, rect, paint);
}
```

El resto de la `OnPaintSurface` invalidación dibuja un rectángulo alrededor de mapa de bits para indicar sus tamaños.

El `PorterDuffGridPage` clase crea catorce instancias de `PorterDurffCanvasView`, uno para cada miembro de la `blendModes` matriz. El orden de los `SKBlendModes` miembros de la matriz es un poco diferente de la tabla con el fin de colocar los modos similar adyacentes entre sí. Las instancias de 14 `PorterDuffCanvasView` se organizan junto con etiquetas en un `Grid`:

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

[![Cuadrícula de Porter Duff](porter-duff-images/PorterDuffGrid.png "Porter Duff cuadrícula")](porter-duff-images/PorterDuffGrid-Large.png#lightbox)

Desea le quepa duda de que es fundamental para el correcto funcionamiento de los modos de blend Porter Duff transparencia. El `PorterDuffCanvasView` clase contiene un total de tres llamadas a la `Canvas.Clear` método. Todos ellos use el método sin parámetros, que todos los píxeles se establece en transparente:

```csharp
canvas.Clear();
```

Intente cambiar cualquiera de esas llamadas, por lo que se establecen los píxeles en negro opaco:

```csharp
canvas.Clear(SKColors.White);
```

Este cambio, algunos de los modos de mezcla se parecen funcionar, pero otros no lo harán. Si establece el fondo del mapa de bits de origen a blanco, el `SrcOver` modo no funciona porque no hay ningún píxeles transparentes en el mapa de bits de origen para permitir que el destino se muestre a través. Si establece el fondo de mapa de bits de destino o en el lienzo en blanco, a continuación, `DstOver` no funciona porque el destino no tiene los píxeles transparentes.

Es posible que haya un tentación para reemplazar los mapas de bits en el **cuadrícula Porter Duff** página con más sencillo `DrawRect` llamadas. Que funcionará para el rectángulo de destino pero no para el rectángulo de origen. El rectángulo de origen debe abarcar más que el área de color azulado. El rectángulo de origen debe incluir un área transparente que se corresponde con el área coloreada del destino. Solo entonces estas éstas combinarán trabajo modos.

## <a name="using-mattes-with-porter-duff"></a>Uso de mates con Porter Duff

La página de composición de la **pared del brick** muestra un ejemplo de una tarea de composición clásica: Una imagen se debe ensamblar desde varias partes, incluido un mapa de bits con un fondo que se debe eliminar. Este es el **SeatedMonkey.jpg** mapa de bits con el fondo problemático:

![Asentado Monkey](porter-duff-images/SeatedMonkey.jpg "asentada Monkey")

Como preparación para la composición, correspondiente _mate_ se creó, que es otro mapa de bits de color negro en la que desea que aparezca la imagen y transparente en caso contrario. Este archivo se denomina **SeatedMonkeyMatte.png** y se encuentra entre los recursos en el **Media** carpeta en el [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) ejemplo :

![Asentado Monkey mate](porter-duff-images/SeatedMonkeyMatte.png "asentada Monkey mate")

Se trata de _no_ un mate creado por expertos. De forma óptima, mate debe incluir píxeles parcialmente transparentes alrededor del borde de los píxeles negros, y este mate no lo hace.

El archivo XAML para el **paredes de ladrillo composición** crea una instancia de la página una `SKCanvasView` y un `Button` que guía al usuario a través del proceso necesario para componer la imagen final:

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

El archivo de código subyacente carga los dos mapas de bits necesarios y que controla la `Clicked` eventos de la `Button`. Para cada `Button` haga clic en, el `step` Pole je incrementado a un nuevo `Text` propiedad está establecida para el `Button`. Cuando `step` llega a 5, vuelve a establecerse en 0:

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

Cuando el programa se ejecuta en primer lugar, nada está visible, excepto el `Button`:

[![Paso de la composición de pared de ladrillos 0](porter-duff-images/BrickWallCompositing0.png "paredes de ladrillo composición paso 0")](porter-duff-images/BrickWallCompositing0-Large.png#lightbox)

Al presionar el `Button` hace que una vez `step` se incrementa en 1 y el `PaintSurface` controlador muestra ahora **SeatedMonkey.jpg**:

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

No hay ningún `SKPaint` objeto y, por tanto, ningún modo de mezcla. El mapa de bits aparece en la parte inferior de la pantalla:

[![Paso de la composición de pared de ladrillos 1](porter-duff-images/BrickWallCompositing1.png "paredes de ladrillo composición paso 1")](porter-duff-images/BrickWallCompositing1-Large.png#lightbox)

Presione el `Button` nuevo y `step` incrementos a 2. Este es el paso fundamental de mostrar el **SeatedMonkeyMatte.png** archivo:

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

Es el modo de mezcla `SKBlendMode.DstIn`, lo que significa que el destino se conservarán en áreas que se corresponden a las áreas que no sean transparentes del origen. El resto del rectángulo de destino correspondiente en el mapa de bits original se vuelve transparente:

[![Paso de la composición de pared de ladrillos 2](porter-duff-images/BrickWallCompositing2.png "paredes de ladrillo composición paso 2")](porter-duff-images/BrickWallCompositing2-Large.png#lightbox)

Se ha quitado el fondo. 

El paso siguiente es dibujar un rectángulo que es similar a un acera que reside en el objeto monkey. El aspecto de este acera se basa en una composición de dos sombreadores: un sombreador de color sólido y un sombreador de ruido de Perlin:

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

Dado que este acera debe ir detrás de objeto monkey, es el modo de mezcla `DstOver`. El destino aparece únicamente cuando el fondo sea transparente:

[![Paso de la composición de pared de ladrillos 3](porter-duff-images/BrickWallCompositing3.png "paredes de ladrillo composición paso 3")](porter-duff-images/BrickWallCompositing3-Large.png#lightbox)

El último paso consiste en Agregar una pared. El sistema utiliza el icono de mapa de bits de pared de ladrillos disponible como la propiedad estática `BrickWallTile` en el `AlgorithmicBrickWallPage` clase. Una transformación de traslación se agrega a la `SKShader.CreateBitmap` llamada para desplazar los iconos para que la fila inferior es un icono completo:

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

Para mayor comodidad, el `DrawRect` llamada muestra este sombreador a lo largo de todo el lienzo, pero el `DstOver` modo limita la salida a solo el área del lienzo sigue transparente:

[![Paso de la composición de pared de ladrillos 4](porter-duff-images/BrickWallCompositing4.png "paredes de ladrillo composición paso 4")](porter-duff-images/BrickWallCompositing4-Large.png#lightbox)

Obviamente, existen otras formas para crear esta escena. Se podría generar comenzando en segundo plano y progresa en primer plano. Pero usando los modos de blend proporciona una mayor flexibilidad. En concreto, el uso del mate permite el fondo de un mapa de bits que se excluirán de la escena compuesta.

Como ha aprendido en el artículo [recorte con trazados y regiones](../../curves/clipping.md), `SKCanvas` clase define tres tipos de recorte, correspondiente a la [ `ClipRect` ](xref:SkiaSharp.SKCanvas.ClipRect*), [ `ClipPath` ](xref:SkiaSharp.SKCanvas.ClipPath*), y [ `ClipRegion` ](xref:SkiaSharp.SKCanvas.ClipRegion*) métodos. Los modos de mezcla Porter Duff agrega otro tipo de recorte, lo que permite restringir una imagen a todo lo que puede dibujar, incluidos los mapas de bits. Mate utilizado en **paredes de ladrillo composición** básicamente define un área de recorte.

## <a name="gradient-transparency-and-transitions"></a>Las transiciones y transparencia de degradado

Los ejemplos de la Duff Porter blend modos mostrados anteriormente en este artículo han todos implicado imágenes que consistía en píxeles opacos y píxeles transparentes, pero no parcialmente transparentes píxeles. Las funciones de modo de mezcla se definen para los píxeles también. En la tabla siguiente es una definición más formal de los modos de blend Porter Duff que utiliza la notación que se encuentra en la Skia [ **SkBlendMode referencia**](https://skia.org/user/api/SkBlendMode_Reference). (Porque **SkBlendMode referencia** es una referencia Skia, se utilizan la sintaxis de C++.)

Conceptualmente, los componentes rojos, verdes, azules y alfabéticos de cada píxel se convierten de bytes a números de punto flotante en el intervalo de 0 a 1. El canal alfa, 0 es completamente transparente y 1 es completamente opaco

La notación en la tabla siguiente utiliza las siguientes abreviaturas:

- **Acelerador de desarrollo** es el canal alfa de destino
- **Controlador de dominio** constituye el destino de color RGB
- **SA** es el canal alfa de origen
- **SC** es el color RGB de origen

Los colores RGB se multiplican previamente por el valor alfa. Por ejemplo, si **Sc** representa rojo puro pero **Sa** es 0 x 80, es el color RGB **(0 x 80, 0, 0)** . Si **Sa** es 0, entonces todos los componentes RGB también son cero.

El resultado se muestra entre corchetes con el canal alfa y el color RGB separados por comas: **[color alfa,]** . Para el color, el cálculo se realiza por separado para los componentes rojos, verde y azules:

| Modo       | Operación |
| ---------- | --------- |
| `Clear`    | [0, 0]    |
| `Src`      | [Sa, Sc]  |
| `Dst`      | [DAS, controlador de dominio]  |
| `SrcOver`  | [Sa + Da· (1 – Sa), Sc + Dc· (1 – Sa) | 
| `DstOver`  | [Acelerador de desarrollo + Sa· (1 – Da), Dc + Sc· (1 – Da) |
| `SrcIn`    | [Sa· Acelerador de desarrollo, Sc· Acelerador de desarrollo] |
| `DstIn`    | [Da· SA, Dc· SA] |
| `SrcOut`   | [Sa· (1 – Da), Sc· (1 – Da)] |
| `DstOut`   | [Da· (1 – Sa), Dc· (1 – Sa)] |
| `SrcATop`  | [DAS, Sc· Acelerador de desarrollo + Dc· (1 – Sa)] |
| `DstATop`  | [Sa, Dc· SA + Sc· (1 – Da)] |
| `Xor`      | [Sa + Da – 2· SA· Acelerador de desarrollo, Sc· (1 – Da) + Dc· (1 – Sa)] |
| `Plus`     | [Sa + Acelerador de desarrollo, Sc + Dc] |
| `Modulate` | [Sa· Acelerador de desarrollo, Sc· Controlador de dominio] | 

Estas operaciones son más fáciles de analizar cuando **DAS** y **Sa** son 0 o 1. Por ejemplo, para el valor predeterminado `SrcOver` modo, si **Sa** es 0, a continuación, **Sc** es también 0 y el resultado es **[Acelerador de desarrollo, controlador de dominio]** , el color y alfa de destino. Si **Sa** es 1, entonces el resultado es **[Sa, Sc]** , el alfa de origen y el color, o **[1, Sc]** .

El `Plus` y `Modulate` son un poco diferentes de los demás modos en que pueden dar lugar a nuevos colores de la combinación de origen y el destino. El `Plus` se puede interpretar el modo con los componentes de bytes o componentes de punto flotante. En el **cuadrícula Porter Duff** página mostrado anteriormente, el color de destino es **(0xC0, 0 x 80, 0 x 00)** y el color de origen es **(0 x 00, 0 x 80, 0xC0)** . Se agrega cada par de componentes, pero la suma se fija en 0xFF. El resultado es el color **(0xC0, 0xFF, 0xC0)** . Que es el color que se muestra en la intersección.

Para el `Modulate` modo, los valores RGB se deben convertir a punto flotante. El color de destino es **(0,75, 0,5, 0)** y el origen es **(0; 0,5; 0,75)** . Los componentes RGB son cada multiplicados entre sí y el resultado es **(0, 0.25, 0)** . Que es el color que se muestra en la intersección de la **Porter Duff cuadrícula** página de este modo.

El **Porter Duff transparencia** página le permite examinar el funcionamiento de los modos de mezcla Porter Duff en objetos gráficos que sean parcialmente transparentes. El archivo XAML incluye una `Picker` con los modos de Porter Duff:

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

El archivo de código subyacente pasa a ocupar dos rectángulos del mismo tamaño con un degradado lineal. El degradado de destino es desde la esquina superior derecha a la parte inferior izquierda. Es marrón en la esquina superior derecha, pero, a continuación, hacia el centro comienza con transparencia progresiva y es transparente en la esquina inferior izquierda. 

El rectángulo de origen tiene un degradado desde la parte superior izquierda a la esquina inferior derecha. La esquina superior izquierda es azulada pero nuevo se desvanece transparente y también es transparente en la esquina inferior derecha. 

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

Este programa muestra que se pueden utilizar los modos de mezcla Porter Duff con objetos gráficos que no sea de mapas de bits. Sin embargo, el origen debe incluir un área transparente. Esto sucede aquí porque el degradado rellena el rectángulo, pero la parte del degradado es transparente.

Presentamos tres ejemplos:

[![Transparencia de Porter Duff](porter-duff-images/PorterDuffTransparency.png "Porter Duff transparencia")](porter-duff-images/PorterDuffTransparency-Large.png#lightbox)

La configuración de origen y de destino es muy similar a los diagramas se muestra en la página 255 de la original Porter-Duff [ _la composición de imágenes digitales_ ](https://graphics.pixar.com/library/Compositing/paper.pdf) papel, pero esta página se muestra que el modos de mezcla son con buen comportamiento para las áreas de transparencia parcial.

Puede usar degradados transparentes para algunos efectos diferentes. Una posibilidad es enmascaramiento, que es similar a la técnica que se muestra en el [ **degradados radiales para el enmascaramiento** ](../shaders/circular-gradients.md#radial-gradients-for-masking) sección de la **página degradados circular de SkiaSharp**. Gran parte de la **composición máscara** página es similar a dicho programa anterior. Carga un recurso de mapa de bits y determina un rectángulo en el que se va a mostrar. Un degradado radial se crea basándose en un centro de determinada previamente y radius:

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

La diferencia con este programa es que el degradado comienza con negro en el centro y termina con la transparencia. Se muestra en el mapa de bits con un modo de mezcla de `DstIn`, que muestra el destino solo en las áreas de origen que no son transparentes.

Después de la `DrawRect` llamada, toda la superficie del lienzo es transparente, excepto el círculo definido por el degradado radial. Se realiza una llamada final:

```csharp
canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
```

Todas las áreas transparentes del lienzo son de color rosas:

[![Máscara de composición](porter-duff-images/CompositingMask.png "máscara de composición")](porter-duff-images/CompositingMask-Large.png#lightbox)

También puede utilizar los modos de Porter Duff y degradados parcialmente transparentes para las transiciones de una imagen a otro. El **transiciones degradado** página incluye un `Slider` para indicar un nivel de progreso en la transición de 0 a 1 y un `Picker` para elegir el tipo de transición que desee:

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

El archivo de código subyacente carga dos recursos de mapa de bits para demostrar la transición. Estas son las mismas dos imágenes usadas en el **mapa de bits disolver** página anteriormente en este artículo. El código también define una enumeración con tres miembros que corresponden a tres tipos de degradados &mdash; lineal, radial y limpieza. Estos valores se cargan en el `Picker`:

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

El archivo de código subyacente crea tres `SKPaint` objetos. La `paint0` objeto no usa un modo de mezcla. Este objeto de pintura se usa para dibujar un rectángulo con un degradado que se pasa del negro al transparente como se indica en la `colors` matriz. El `positions` matriz se basa en la posición de la `Slider`, pero el ajusta ligeramente. Si el `Slider` está en su valor mínimo o máximo, el `progress` los valores son 0 o 1, y uno de los dos mapas de bits debe estar totalmente visible. El `positions` matriz debe establecerse como corresponda para esos valores.

Si el `progress` valor es 0, el `positions` matriz contiene los valores -0.1 y 0. SkiaSharp ajustará en caso contrario, ese primer valor para que sea igual a 0, lo que significa que el degradado de color negro sólo en 0 y transparente. Cuando `progress` es 0,5 y, después, la matriz contiene los valores 0,45 y 0,55. El degradado es negro de 0 a 0,45, a continuación, realiza la transición a transparente y es completamente transparente desde 0,55 en 1. Cuando `progress` es 1, el `positions` matriz es 1 y 1.1, lo que significa que el degradado de color negro de 0 a 1.

El `colors` y `position` las matrices se utilizan en los tres métodos de `SKShader` que crear un degradado. Solo uno de estos sombreadores se crea basándose en la `Picker` selección: 

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

Ese gradiente se muestra en el rectángulo sin un modo de mezcla. Después de eso `DrawRect` llamada, el lienzo simplemente contiene un degradado del negro transparente. Aumenta la cantidad de negro con mayor `Slider` valores.

En las cuatro instrucciones finales de la `PaintSurface` controlador, se muestran los dos mapas de bits. El `SrcOut` modo blend significa que el primer mapa de bits solo se muestra en las áreas transparentes del fondo. El `DstOver` modo para el segundo mapa de bits significa que el segundo mapa de bits se muestra únicamente en las áreas donde no se muestra el primer mapa de bits.

Las capturas de pantalla siguientes muestran los tres tipos de transiciones diferentes, cada uno en la marca del 50%:

[![Transiciones de degradado](porter-duff-images/GradientTransitions.png "transiciones de degradado")](porter-duff-images/GradientTransitions-Large.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
