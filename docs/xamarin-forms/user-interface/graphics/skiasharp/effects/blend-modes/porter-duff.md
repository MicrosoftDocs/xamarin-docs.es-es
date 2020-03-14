---
title: Modos de fusión Porter Duff
description: Use los modos de mezcla Porter Duff para redactar escenas basadas en imágenes de origen y destino.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 57F172F8-BA03-43EC-A215-ED6B78696BB5
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: c15dd4606a75cc3cdffbad71f15299568157213a
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79306516"
---
# <a name="porter-duff-blend-modes"></a>Modos de fusión Porter Duff

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Los modos de mezcla Porter Duff nombrados de Thomas Porter y Tom Duff, que desarrolló un álgebra de la composición mientras trabajaba para Lucasfilm. Las [_imágenes digitales de composición_](https://graphics.pixar.com/library/Compositing/paper.pdf) de papel se publicaron en el problema 1984 de julio de los _gráficos de equipos_, páginas 253 a 259. Estos modos de fusión son esenciales para la composición, que está ensamblando varias imágenes en una escena de composición:

![Ejemplo de Porter-Duff](porter-duff-images/PorterDuffSample.png "Ejemplo de Porter-Duff")

## <a name="porter-duff-concepts"></a>Conceptos de Porter Duff

Supongamos que un rectángulo de color entre ocupa el superior e izquierdo dos tercios de la superficie de pantalla:

![Destino de Porter-Duff](porter-duff-images/PorterDuffDst.png "Destino de Porter-Duff")

Esta área se denomina _destino_ _o,_ a veces, el fondo o el _fondo_ .

Desea dibujar el rectángulo siguiente, que es el mismo tamaño de destino. El rectángulo es transparente, excepto un área azulado que ocupa el derecho e inferior dos tercios:

![Origen de Porter-Duff](porter-duff-images/PorterDuffSrc.png "Origen de Porter-Duff")

Esto se denomina _origen_ o, en ocasiones, _primer plano_.

Cuando el origen se muestra en el destino, aquí es lo que espera:

![Porter-Duff origen sobre](porter-duff-images/PorterDuffSrcOver.png "Porter-Duff origen sobre")

Los píxeles transparentes del origen de permitir que el fondo mostrar a través, mientras que los píxeles de origen azulado oscurecen el fondo. Este es el caso normal, y se hace referencia a él en SkiaSharp como `SKBlendMode.SrcOver`. Ese valor es el valor predeterminado de la propiedad `BlendMode` cuando se crea una instancia de un objeto `SKPaint`.

Sin embargo, es posible especificar un modo blend diferente para un efecto diferente. Si especifica `SKBlendMode.DstOver`, en el área donde se entrecruzan el origen y el destino, el destino aparece en lugar del origen:

![Destino de Porter-Duff](porter-duff-images/PorterDuffDstOver.png "Destino de Porter-Duff")

En el modo de `SKBlendMode.DstIn` Blend solo se muestra el área donde el destino y el origen intersecan con el color de destino:

![Destino de Porter-Duff en](porter-duff-images/PorterDuffDstIn.png "Destino de Porter-Duff en")

El modo de mezcla de `SKBlendMode.Xor` (Exclusive OR) hace que no aparezca nada donde se superpongan las dos áreas:

![Porter-Duff or exclusivo](porter-duff-images/PorterDuffXor.png "Porter-Duff or exclusivo")

Los rectángulos coloreados de origen y destino dividen eficazmente la superficie de visualización en cuatro áreas únicas que se pueden colorear de correspondiente a la presencia de los rectángulos de origen y destino de varias maneras:

![Porter-Duff](porter-duff-images/PorterDuff.png "Porter Duff")

Los rectángulos superior derecha e inferior izquierda siempre están en blanco porque el origen y destino son transparentes en esas áreas. El color de destino ocupa el área superior izquierda, por lo que o bien se puede colorear área con el color de destino o no en absoluto. De forma similar, el color de origen ocupa el área inferior derecha, para que se puede colorear área con el color de origen o no en absoluto. La intersección del destino y origen en la parte central puede ser coloreada con el color de destino, el color de origen, o no en absoluto.

El número total de combinaciones es 2 (para la esquina superior izquierda) veces 2 (para la esquina inferior derecha) veces 3 (para el centro) o 12. Estos son los modos de composición Porter Duff básicos 12.

Hacia el final de _la composición de imágenes digitales_ (página 256), Porter y Duff agregan un modo decimotercer denominado _Plus_ (que corresponde al miembro SkiaSharp `SKBlendMode.Plus` y al modo _más ligero_ de W3C (que no se debe confundir con el modo _ligero_ de W3C). Este modo de `Plus` agrega los colores de origen y de destino, un proceso que se describe con más detalle en breve.

Skia agrega un modo decimocuarto denominado `Modulate` que es muy similar a `Plus`, salvo que se multiplican los colores de origen y de destino. Se puede tratar como un modo de blend Porter Duff adicional.

Estos son los modos de Porter Duff 14, tal como se define en SkiaSharp. La tabla muestra cómo color cada una de las tres áreas en blanco en el diagrama anterior:

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

Estos modos de fusión son simétricos. Se pueden intercambiar el origen y destino y todos los modos de siguen estando disponibles.

La convención de nomenclatura de los modos sigue unas reglas sencillas:

- **Src** o **DST** por sí solo significa que solo están visibles los píxeles de origen o de destino.
- El sufijo **over** indica qué es visible en la intersección. El origen o el destino se dibuja "u" otro.
- El sufijo **in** significa que solo se colorea la intersección. El resultado se restringe a solo la parte del origen o destino que sea "in" de la otra.
- El sufijo **out** significa que la intersección no se colorea. El resultado es sólo la parte del origen o destino que sea "out" de la intersección.
- El sufijo de **encima** es la Unión de **in** y **out**. Incluye el área en la que el origen o el destino están "encima" del otro.

Observe la diferencia con los modos `Plus` y `Modulate`. Estos modos están realizando un tipo diferente de cálculo en los píxeles de origen y destino. Se describen más detalladamente en breve.

La página de **cuadrícula Porter-Duff** muestra los 14 modos en una pantalla en forma de cuadrícula. Cada modo es una instancia independiente de `SKCanvasView`. Por ese motivo, una clase se deriva de `SKCanvasView` denominada `PorterDuffCanvasView`. El constructor estático crea dos mapas de bits del mismo tamaño, uno con un rectángulo de color entre en su área superior izquierda y otro con un rectángulo azulado:

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

La invalidación de `OnPaintSurface` dibuja los dos mapas de bits. El primero se dibuja con normalidad:

```csharp
canvas.DrawBitmap(dstBitmap, rect);
```

La segunda se dibuja con un objeto `SKPaint` en el que la propiedad `BlendMode` se ha establecido en el argumento de constructor:

```csharp
using (SKPaint paint = new SKPaint())
{
    paint.BlendMode = blendMode;
    canvas.DrawBitmap(srcBitmap, rect, paint);
}
```

El resto de la invalidación de `OnPaintSurface` dibuja un rectángulo alrededor del mapa de bits para indicar su tamaño.

La clase `PorterDuffGridPage` crea catorce instancias de `PorterDurffCanvasView`, una para cada miembro de la matriz `blendModes`. El orden de los miembros de `SKBlendModes` de la matriz es un poco diferente de la tabla para colocar modos similares adyacentes entre sí. Las 14 instancias de `PorterDuffCanvasView` están organizadas junto con etiquetas en un `Grid`:

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

Desea le quepa duda de que es fundamental para el correcto funcionamiento de los modos de blend Porter Duff transparencia. La clase `PorterDuffCanvasView` contiene un total de tres llamadas al método `Canvas.Clear`. Todos ellos use el método sin parámetros, que todos los píxeles se establece en transparente:

```csharp
canvas.Clear();
```

Intente cambiar cualquiera de esas llamadas, por lo que se establecen los píxeles en negro opaco:

```csharp
canvas.Clear(SKColors.White);
```

Este cambio, algunos de los modos de mezcla se parecen funcionar, pero otros no lo harán. Si establece el fondo del mapa de bits de origen en blanco, el modo `SrcOver` no funciona porque no hay píxeles transparentes en el mapa de bits de origen para permitir que el destino se muestre. Si establece el fondo del mapa de bits de destino o el lienzo en blanco, `DstOver` no funciona porque el destino no tiene píxeles transparentes.

Podría haber una tentación de reemplazar los mapas de bits en la página de **cuadrícula Porter-Duff** con llamadas más sencillas `DrawRect`. Que funcionará para el rectángulo de destino pero no para el rectángulo de origen. El rectángulo de origen debe abarcar más que el área de color azulado. El rectángulo de origen debe incluir un área transparente que se corresponde con el área coloreada del destino. Solo entonces estas éstas combinarán trabajo modos.

## <a name="using-mattes-with-porter-duff"></a>Uso de mates con Porter Duff

La página de composición de la **pared del brick** muestra un ejemplo de una tarea de composición clásica: una imagen se debe ensamblar desde varias partes, incluido un mapa de bits con un fondo que se debe eliminar. Este es el mapa de bits **SeatedMonkey. jpg** con el fondo problemático:

![Monkey sentado](porter-duff-images/SeatedMonkey.jpg "Monkey sentado")

Como preparación para la composición, se creó un _mate_ correspondiente, que es otro mapa de bits que es negro donde desea que aparezca la imagen y sea transparente en caso contrario. Este archivo se denomina **SeatedMonkeyMatte. png** y se encuentra entre los recursos de la carpeta **multimedia** en el ejemplo [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) :

![Mate Monkey colocado](porter-duff-images/SeatedMonkeyMatte.png "Mate Monkey colocado")

_No_ se trata de un mate creado por el experto. De forma óptima, mate debe incluir píxeles parcialmente transparentes alrededor del borde de los píxeles negros, y este mate no lo hace.

El archivo XAML de la página de **composición de la pared del brick** crea una instancia de un `SKCanvasView` y un `Button` que guía al usuario a través del proceso de creación de la imagen final:

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

El archivo de código subyacente carga los dos mapas de bits que necesita y controla el evento de `Clicked` de la `Button`. Para cada `Button` haga clic en, se incrementa el campo `step` y se establece una nueva propiedad `Text` para la `Button`. Cuando `step` llega a 5, se vuelve a establecer en 0:

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

Cuando el programa se ejecuta por primera vez, no hay nada visible excepto el `Button`:

[![Paso 0 de composición de la pared de ladrillo](porter-duff-images/BrickWallCompositing0.png "Paso 0 de composición de la pared de ladrillo")](porter-duff-images/BrickWallCompositing0-Large.png#lightbox)

Al presionar el `Button` una vez, `step` se incrementa a 1 y el controlador de `PaintSurface` ahora muestra **SeatedMonkey. jpg**:

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

No hay ningún objeto de `SKPaint` y, por tanto, ningún modo de mezcla. El mapa de bits aparece en la parte inferior de la pantalla:

[![Paso 1 de composición de la pared de ladrillo](porter-duff-images/BrickWallCompositing1.png "Paso 1 de composición de la pared de ladrillo")](porter-duff-images/BrickWallCompositing1-Large.png#lightbox)

Vuelva a presionar el `Button` y `step` incrementos en 2. Este es el paso fundamental de mostrar el archivo **SeatedMonkeyMatte. png** :

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

El modo de mezcla es `SKBlendMode.DstIn`, lo que significa que el destino se conservará en las áreas correspondientes a las áreas no transparentes del origen. El resto del rectángulo de destino correspondiente en el mapa de bits original se vuelve transparente:

[![Paso 2 de composición de la pared de ladrillo](porter-duff-images/BrickWallCompositing2.png "Paso 2 de composición de la pared de ladrillo")](porter-duff-images/BrickWallCompositing2-Large.png#lightbox)

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

Dado que este Sidewalk debe estar detrás del Monkey, el modo de mezcla es `DstOver`. El destino aparece únicamente cuando el fondo sea transparente:

[![Paso 3 de composición de la pared de ladrillo](porter-duff-images/BrickWallCompositing3.png "Paso 3 de composición de la pared de ladrillo")](porter-duff-images/BrickWallCompositing3-Large.png#lightbox)

El último paso consiste en Agregar una pared. El programa usa el icono de mapa de bits de la pared del brick disponible como la propiedad estática `BrickWallTile` en la clase `AlgorithmicBrickWallPage`. Se agrega una transformación de traslación a la `SKShader.CreateBitmap` llamada para desplazar los mosaicos de modo que la fila inferior sea un icono completo:

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

Para mayor comodidad, la llamada de `DrawRect` muestra este sombreador en todo el lienzo, pero el modo de `DstOver` limita la salida a solo el área del lienzo que sigue siendo transparente:

[![Paso 4 de composición de la pared de ladrillo](porter-duff-images/BrickWallCompositing4.png "Paso 4 de composición de la pared de ladrillo")](porter-duff-images/BrickWallCompositing4-Large.png#lightbox)

Obviamente, existen otras formas para crear esta escena. Se podría generar comenzando en segundo plano y progresa en primer plano. Pero usando los modos de blend proporciona una mayor flexibilidad. En concreto, el uso del mate permite el fondo de un mapa de bits que se excluirán de la escena compuesta.

Como aprendió en el [recorte del artículo con rutas de acceso y regiones](../../curves/clipping.md), la clase `SKCanvas` define tres tipos de recortes, que corresponden a los métodos [`ClipRect`](xref:SkiaSharp.SKCanvas.ClipRect*), [`ClipPath`](xref:SkiaSharp.SKCanvas.ClipPath*)y [`ClipRegion`](xref:SkiaSharp.SKCanvas.ClipRegion*) . Los modos de mezcla Porter Duff agrega otro tipo de recorte, lo que permite restringir una imagen a todo lo que puede dibujar, incluidos los mapas de bits. Esencialmente, el mate usado en la **composición de la pared del brick** define un área de recorte.

## <a name="gradient-transparency-and-transitions"></a>Las transiciones y transparencia de degradado

Los ejemplos de la Duff Porter blend modos mostrados anteriormente en este artículo han todos implicado imágenes que consistía en píxeles opacos y píxeles transparentes, pero no parcialmente transparentes píxeles. Las funciones de modo de mezcla se definen para los píxeles también. La tabla siguiente es una definición más formal de los modos de mezcla Porter-Duff que usa la notación que se encuentra en la referencia de Skia [**SkBlendMode**](https://skia.org/user/api/SkBlendMode_Reference). (Dado **que la referencia de SkBlendMode** es una C++ referencia de Skia, se usa la sintaxis).

Conceptualmente, los componentes rojos, verdes, azules y alfabéticos de cada píxel se convierten de bytes a números de punto flotante en el intervalo de 0 a 1. El canal alfa, 0 es completamente transparente y 1 es completamente opaco

La notación en la tabla siguiente utiliza las siguientes abreviaturas:

- **Da** es el canal alfa de destino
- **DC** es el color RGB de destino
- **SA** es el canal alfa de origen
- **SC** es el color RGB de origen

Los colores RGB se multiplican previamente por el valor alfa. Por ejemplo, si **SC** representa rojo puro pero **SA** es 0X80, el color RGB es **(0x80, 0, 0)** . Si **SA** es 0, todos los componentes RGB también son cero.

El resultado se muestra entre corchetes con el canal alfa y el color RGB separado por una coma: **[Alpha, color]** . Para el color, el cálculo se realiza por separado para los componentes rojos, verde y azules:

| Mode       | Operación |
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

Estas operaciones son más fáciles de analizar cuando **da** y **SA** son 0 o 1. Por ejemplo, para el modo de `SrcOver` predeterminado, si **SA** es 0, **SC** también es 0 y el resultado es **[da, DC]** , el alfa y el color de destino. Si **SA** es 1, el resultado es **[SA, SC]** , el alfa de origen y el color, o **[1, SC]** .

Los modos `Plus` y `Modulate` son ligeramente diferentes de los demás en que los nuevos colores pueden ser el resultado de la combinación del origen y del destino. El modo de `Plus` se puede interpretar con componentes de byte o componentes de punto flotante. En la página de **cuadrícula Porter-Duff** mostrada anteriormente, el color de destino es **(0xC0, 0x80, 0x00)** y el color de origen es **(0x00, 0x80, 0xC0)** . Se agrega cada par de componentes, pero la suma se fija en 0xFF. El resultado es el color **(0xC0, 0xFF, 0xC0)** . Que es el color que se muestra en la intersección.

En el modo de `Modulate`, los valores RGB se deben convertir en punto flotante. El color de destino es **(0,75, 0,5, 0)** y el origen es **(0, 0,5, 0,75)** . Los componentes RGB se multiplican juntos y el resultado es **(0, 0,25, 0)** . Este es el color que se muestra en la intersección de la página de **cuadrícula Porter-Duff** para este modo.

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

[![Transparencia Porter-Duff](porter-duff-images/PorterDuffTransparency.png "Transparencia Porter-Duff")](porter-duff-images/PorterDuffTransparency-Large.png#lightbox)

La configuración del destino y el origen son muy similares a los diagramas que se muestran en la página 255 de las [_imágenes digitales de composición_](https://graphics.pixar.com/library/Compositing/paper.pdf) de Porter-Duff originales, pero en esta página se muestra cómo se comportan correctamente los modos de mezcla para las áreas de transparencia parcial.

Puede usar degradados transparentes para algunos efectos diferentes. Una posibilidad es enmascaramiento, que es similar a la técnica que se muestra en la sección [**degradados radiales para enmascaramiento**](../shaders/circular-gradients.md#radial-gradients-for-masking) de la **Página de degradados circulares SkiaSharp**. Gran parte de la página de la **máscara de composición** es similar a la del programa anterior. Carga un recurso de mapa de bits y determina un rectángulo en el que se va a mostrar. Un degradado radial se crea basándose en un centro de determinada previamente y radius:

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

La diferencia con este programa es que el degradado comienza con negro en el centro y termina con la transparencia. Se muestra en el mapa de bits con un modo de mezcla de `DstIn`, que muestra el destino solo en las áreas del origen que no son transparentes.

Después de la llamada a `DrawRect`, toda la superficie del lienzo es transparente excepto para el círculo definido por el degradado radial. Se realiza una llamada final:

```csharp
canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
```

Todas las áreas transparentes del lienzo son de color rosas:

[![Máscara de composición](porter-duff-images/CompositingMask.png "Máscara de composición")](porter-duff-images/CompositingMask-Large.png#lightbox)

También puede utilizar los modos de Porter Duff y degradados parcialmente transparentes para las transiciones de una imagen a otro. La página **transiciones de degradado** incluye un `Slider` para indicar un nivel de progreso en la transición de 0 a 1 y un `Picker` para elegir el tipo de transición que desee:

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

El archivo de código subyacente carga dos recursos de mapa de bits para demostrar la transición. Se trata de las mismas dos imágenes que se usan en la página de **resolución de mapas de bits** anteriormente en este artículo. El código también define una enumeración con tres miembros que corresponden a tres tipos de degradados &mdash; lineal, radial y de barrido. Estos valores se cargan en el `Picker`:

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

El archivo de código subyacente crea tres objetos `SKPaint`. El objeto `paint0` no usa un modo de mezcla. Este objeto Paint se usa para dibujar un rectángulo con un degradado que va de negro a transparente, como se indica en la matriz `colors`. La matriz de `positions` se basa en la posición de la `Slider`, pero se ajusta en cierto modo. Si el `Slider` está en su valor mínimo o máximo, los valores de `progress` son 0 o 1, y uno de los dos mapas de bits debe estar totalmente visible. La matriz de `positions` se debe establecer en consecuencia para esos valores.

Si el valor de la `progress` es 0, la matriz de `positions` contiene los valores-0,1 y 0. SkiaSharp ajustará en caso contrario, ese primer valor para que sea igual a 0, lo que significa que el degradado de color negro sólo en 0 y transparente. Cuando `progress` es 0,5, la matriz contiene los valores 0,45 y 0,55. El degradado es negro de 0 a 0,45, a continuación, realiza la transición a transparente y es completamente transparente desde 0,55 en 1. Cuando `progress` es 1, la matriz de `positions` es 1 y 1,1, lo que significa que el degradado es negro de 0 a 1.

Las matrices `colors` y `position` se usan en los tres métodos de `SKShader` que crean un degradado. Solo uno de estos sombreadores se crea basándose en la `Picker` selección: 

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

Ese gradiente se muestra en el rectángulo sin un modo de mezcla. Después de que `DrawRect` llama a, el lienzo contiene simplemente un degradado de negro a transparente. La cantidad de negro aumenta con valores de `Slider` más altos.

En las cuatro instrucciones finales del controlador de `PaintSurface`, se muestran los dos mapas de bits. El modo de mezcla `SrcOut` significa que el primer mapa de bits solo se muestra en las áreas transparentes del fondo. El modo de `DstOver` para el segundo mapa de bits significa que el segundo mapa de bits solo se muestra en las áreas en las que no se muestra el primer mapa de bits.

Las capturas de pantalla siguientes muestran los tres tipos de transiciones diferentes, cada uno en la marca del 50%:

[![Transiciones de degradado](porter-duff-images/GradientTransitions.png "Transiciones de degradado")](porter-duff-images/GradientTransitions-Large.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
