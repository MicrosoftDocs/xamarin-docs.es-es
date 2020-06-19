---
title: Efectos de la ruta de acceso en SkiaSharp
description: En este artículo se explican los distintos efectos de la ruta de SkiaSharp que permiten usar las rutas de acceso para el trazado y el relleno, y se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 95167D1F-A718-405A-AFCC-90E596D422F3
author: davidbritch
ms.author: dabritch
ms.date: 07/29/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f3a5a581ffb4ca2acf1d4209b8b7a744f0daa5eb
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128061"
---
# <a name="path-effects-in-skiasharp"></a>Efectos de la ruta de acceso en SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Descubrir los distintos efectos de trazado que permiten usar las rutas de acceso para el trazado y el relleno_

Un *efecto de ruta de acceso* es una instancia de la [`SKPathEffect`](xref:SkiaSharp.SKPathEffect) clase que se crea con uno de ocho métodos de creación estáticos definidos por la clase. `SKPathEffect`A continuación, el objeto se establece en la [`PathEffect`](xref:SkiaSharp.SKPaint.PathEffect) propiedad de un [`SKPaint`](xref:SkiaSharp.SKPaint) objeto para diversos efectos interesantes; por ejemplo, contornea una línea con una ruta de acceso replicada pequeña:

![El ejemplo de cadena vinculada](effects-images/patheffectsample.png)

Los efectos de la ruta de acceso le permiten:

- Trazar una línea con puntos y guiones
- Trazar una línea con cualquier trazado relleno
- Rellenar un área con líneas de trama
- Rellenar un área con un trazado en mosaico
- Redondear las esquinas vivas
- Agregar "vibración" aleatoria a las líneas y las curvas

Además, puede combinar dos o más efectos de la ruta de acceso.

En este artículo también se muestra cómo utilizar el [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath*) método de `SKPaint` para convertir un trazado en otra ruta de acceso mediante la aplicación de propiedades de `SKPaint` , incluidos `StrokeWidth` y `PathEffect` . Esto da como resultado técnicas interesantes, como obtener una ruta de acceso que es un contorno de otra ruta de acceso. `GetFillPath`también es útil en relación con los efectos de la ruta de acceso.

## <a name="dots-and-dashes"></a>Puntos y guiones

El uso del [`PathEffect.CreateDash`](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single)) método se describe en los [**puntos y guiones**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)del artículo. El primer argumento del método es una matriz que contiene un número par de dos o más valores, alternando entre las longitudes de los guiones y las longitudes de los espacios entre los guiones:

```csharp
public static SKPathEffect CreateDash (Single[] intervals, Single phase)
```

Estos valores *no* son relativos al ancho del trazo. Por ejemplo, si el ancho del trazo es 10 y desea una línea formada por guiones cuadrados y huecos cuadrados, establezca la `intervals` matriz en {10, 10}. El `phase` argumento indica dónde comienza la línea dentro del patrón de guiones. En este ejemplo, si desea que la línea empiece con la diferencia cuadrada, establezca `phase` en 10.

Los extremos de los guiones se ven afectados por la `StrokeCap` propiedad de `SKPaint` . En el caso de anchos de trazo anchos, es muy común establecer esta propiedad en `SKStrokeCap.Round` para redondear los extremos de los guiones. En este caso, los valores de la `intervals` matriz *no* incluyen la longitud adicional resultante del redondeo. Este hecho significa que un punto circular requiere especificar un ancho de cero. Para un ancho de trazo de 10, para crear una línea con puntos y huecos circulares entre los puntos del mismo diámetro, use una `intervals` matriz de {0, 20}.

La página de **texto con puntos animados** es similar a la página de **texto esquematizada** que se describe en el artículo [**integración de texto y gráficos**](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md) , en el que se muestran caracteres de texto esquematizados estableciendo la `Style` propiedad del `SKPaint` objeto en `SKPaintStyle.Stroke` . Además, el **texto punteado animado** usa `SKPathEffect.CreateDash` para dar a este contorno una apariencia punteada y el programa también anima el `phase` argumento del `SKPathEffect.CreateDash` método para que parezca que los puntos se desplazan por los caracteres de texto. Esta es la página en modo horizontal:

[![Captura de pantalla triple de la página de texto con puntos animados](effects-images/animateddottedtext-small.png)](effects-images/animateddottedtext-large.png#lightbox)

La [`AnimatedDottedTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) clase comienza por definir algunas constantes y también invalida los `OnAppearing` `OnDisappearing` métodos y para la animación:

```csharp
public class AnimatedDottedTextPage : ContentPage
{
    const string text = "DOTTED";
    const float strokeWidth = 10;
    static readonly float[] dashArray = { 0, 2 * strokeWidth };

    SKCanvasView canvasView;
    bool pageIsActive;

    public AnimatedDottedTextPage()
    {
        Title = "Animated Dotted Text";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;

        Device.StartTimer(TimeSpan.FromSeconds(1f / 60), () =>
        {
            canvasView.InvalidateSurface();
            return pageIsActive;
        });
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        pageIsActive = false;
    }
    ...
}
```

El `PaintSurface` controlador comienza creando un `SKPaint` objeto para mostrar el texto. La `TextSize` propiedad se ajusta en función del ancho de la pantalla:

```csharp
public class AnimatedDottedTextPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Create an SKPaint object to display the text
        using (SKPaint textPaint = new SKPaint
            {
                Style = SKPaintStyle.Stroke,
                StrokeWidth = strokeWidth,
                StrokeCap = SKStrokeCap.Round,
                Color = SKColors.Blue,
            })
        {
            // Adjust TextSize property so text is 95% of screen width
            float textWidth = textPaint.MeasureText(text);
            textPaint.TextSize *= 0.95f * info.Width / textWidth;

            // Find the text bounds
            SKRect textBounds = new SKRect();
            textPaint.MeasureText(text, ref textBounds);

            // Calculate offsets to center the text on the screen
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            // Animate the phase; t is 0 to 1 every second
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 1 / 1);
            float phase = -t * 2 * strokeWidth;

            // Create dotted line effect based on dash array and phase
            using (SKPathEffect dashEffect = SKPathEffect.CreateDash(dashArray, phase))
            {
                // Set it to the paint object
                textPaint.PathEffect = dashEffect;

                // And draw the text
                canvas.DrawText(text, xText, yText, textPaint);
            }
        }
    }
}
```

Hacia el final del método, `SKPathEffect.CreateDash` se llama al método mediante el `dashArray` que se define como un campo y el `phase` valor animado. La `SKPathEffect` instancia se establece en la `PathEffect` propiedad del `SKPaint` objeto para mostrar el texto.

Como alternativa, puede establecer el `SKPathEffect` objeto en el `SKPaint` objeto antes de medir el texto y centrarlo en la página. En ese caso, sin embargo, los puntos animados y guiones provocan una variación en el tamaño del texto representado y el texto tiende a vibrar un poco. (Pruébelo!)

También observará que, como los puntos animados rodean los caracteres de texto, hay un punto determinado en cada curva cerrada en la que los puntos parecen aparecer y no existir. Aquí es donde comienza y termina la ruta de acceso que define el contorno de caracteres. Si la longitud de la ruta de acceso no es un múltiplo entero de la longitud del patrón de guiones (en este caso, 20 píxeles), solo parte de ese patrón puede caber al final de la ruta de acceso.

Es posible ajustar la longitud del patrón de guiones para ajustarse a la longitud de la ruta de acceso, pero esto requiere determinar la longitud de la ruta de acceso, una técnica que se trata en el artículo [**información de ruta de acceso y enumeración**](information.md).

El programa de **transformación de puntos/guiones** anima el propio patrón de guiones para que los guiones parezcan dividirse en puntos, que se combinan para formar guiones de nuevo:

[![Captura de pantalla triple de la página de transformación de guiones de puntos](effects-images/dotdashmorph-small.png)](effects-images/dotdashmorph-large.png#lightbox)

La [`DotDashMorphPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) clase invalida los `OnAppearing` métodos y tal `OnDisappearing` como hizo el programa anterior, pero la clase define el `SKPaint` objeto como un campo:

```csharp
public class DotDashMorphPage : ContentPage
{
    const float strokeWidth = 30;
    static readonly float[] dashArray = new float[4];

    SKCanvasView canvasView;
    bool pageIsActive = false;

    SKPaint ellipsePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = strokeWidth,
        StrokeCap = SKStrokeCap.Round,
        Color = SKColors.Blue
    };
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Create elliptical path
        using (SKPath ellipsePath = new SKPath())
        {
            ellipsePath.AddOval(new SKRect(50, 50, info.Width - 50, info.Height - 50));

            // Create animated path effect
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 3 / 3);
            float phase = 0;

            if (t < 0.25f)  // 1, 0, 1, 2 --> 0, 2, 0, 2
            {
                float tsub = 4 * t;
                dashArray[0] = strokeWidth * (1 - tsub);
                dashArray[1] = strokeWidth * 2 * tsub;
                dashArray[2] = strokeWidth * (1 - tsub);
                dashArray[3] = strokeWidth * 2;
            }
            else if (t < 0.5f)  // 0, 2, 0, 2 --> 1, 2, 1, 0
            {
                float tsub = 4 * (t - 0.25f);
                dashArray[0] = strokeWidth * tsub;
                dashArray[1] = strokeWidth * 2;
                dashArray[2] = strokeWidth * tsub;
                dashArray[3] = strokeWidth * 2 * (1 - tsub);
                phase = strokeWidth * tsub;
            }
            else if (t < 0.75f) // 1, 2, 1, 0 --> 0, 2, 0, 2
            {
                float tsub = 4 * (t - 0.5f);
                dashArray[0] = strokeWidth * (1 - tsub);
                dashArray[1] = strokeWidth * 2;
                dashArray[2] = strokeWidth * (1 - tsub);
                dashArray[3] = strokeWidth * 2 * tsub;
                phase = strokeWidth * (1 - tsub);
            }
            else               // 0, 2, 0, 2 --> 1, 0, 1, 2
            {
                float tsub = 4 * (t - 0.75f);
                dashArray[0] = strokeWidth * tsub;
                dashArray[1] = strokeWidth * 2 * (1 - tsub);
                dashArray[2] = strokeWidth * tsub;
                dashArray[3] = strokeWidth * 2;
            }

            using (SKPathEffect pathEffect = SKPathEffect.CreateDash(dashArray, phase))
            {
                ellipsePaint.PathEffect = pathEffect;
                canvas.DrawPath(ellipsePath, ellipsePaint);
            }
        }
    }
}
```

El `PaintSurface` controlador crea una ruta de acceso elíptica según el tamaño de la página y ejecuta una sección larga de código que establece las `dashArray` variables y `phase` . Como la variable animada `t` va de 0 a 1, los `if` bloques se dividen en cuatro trimestres y, en cada uno de esos trimestres, `tsub` también oscilan entre 0 y 1. Al final, el programa crea el `SKPathEffect` y lo establece en el `SKPaint` objeto para el dibujo.

## <a name="from-path-to-path"></a>Desde ruta de acceso a ruta de acceso

El [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,System.Single)) método de `SKPaint` convierte una ruta de acceso en otra basándose en la configuración del `SKPaint` objeto. Para ver cómo funciona esto, reemplace la `canvas.DrawPath` llamada del programa anterior por el código siguiente:

```csharp
SKPath newPath = new SKPath();
bool fill = ellipsePaint.GetFillPath(ellipsePath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);

```

En este nuevo código, la `GetFillPath` llamada convierte `ellipsePath` (que es simplemente un óvalo) en `newPath` , que se muestra con `newPaint` . El `newPaint` objeto se crea con todos los valores de propiedad predeterminados, salvo que la `Style` propiedad se establece basándose en el valor booleano devuelto de `GetFillPath` .

Los objetos visuales son idénticos salvo el color, que se establece en `ellipsePaint` pero no en `newPaint` . En lugar de la elipse simple definida en `ellipsePath` , `newPath` contiene numerosos contornos de ruta de acceso que definen la serie de puntos y guiones. Este es el resultado de aplicar varias propiedades de `ellipsePaint` (específicamente, `StrokeWidth` , `StrokeCap` y `PathEffect` ) a `ellipsePath` y colocar la ruta de acceso resultante en `newPath` . El `GetFillPath` método devuelve un valor booleano que indica si se va a rellenar la ruta de acceso de destino; en este ejemplo, el valor devuelto es `true` para rellenar la ruta de acceso.

Intente cambiar la `Style` configuración de `newPaint` a `SKPaintStyle.Stroke` y verá los contornos de ruta de acceso individuales que se describen con una línea de un solo píxel.

## <a name="stroking-with-a-path"></a>Contornear con una ruta de acceso

El [`SKPathEffect.Create1DPath`](xref:SkiaSharp.SKPathEffect.Create1DPath(SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPath1DPathEffectStyle)) método es conceptualmente similar a `SKPathEffect.CreateDash` , salvo que se especifica una ruta de acceso en lugar de un patrón de guiones y espacios. Esta ruta de acceso se replica varias veces para trazar la línea o curva.

La sintaxis es la siguiente:

```csharp
public static SKPathEffect Create1DPath (SKPath path, Single advance,
                                         Single phase, SKPath1DPathEffectStyle style)
```

En general, la ruta de acceso que se pasa a `Create1DPath` será pequeña y se centrará alrededor del punto (0,0). El `advance` parámetro indica la distancia entre los centros del trazado, ya que la ruta de acceso se replica en la línea. Normalmente, este argumento se establece en el ancho aproximado de la ruta de acceso. El `phase` argumento desempeña el mismo rol aquí que en el `CreateDash` método.

[`SKPath1DPathEffectStyle`](xref:SkiaSharp.SKPath1DPathEffectStyle)Tiene tres miembros:

- `Translate`
- `Rotate`
- `Morph`

El `Translate` miembro hace que la ruta de acceso permanezca en la misma orientación que la que se replica a lo largo de una línea o curva. En `Rotate` , la ruta de acceso se gira en función de una tangente a la curva. La ruta de acceso tiene su orientación normal para las líneas horizontales. `Morph`es similar a, `Rotate` salvo que la propia ruta de acceso también está curvada para coincidir con la curvatura de la línea que se traza.

En la página efecto de la **ruta de acceso 1D** se muestran estas tres opciones. El archivo [**OneDimensionalPathEffectPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml) define un selector que contiene tres elementos correspondientes a los tres miembros de la enumeración:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Curves.OneDimensionalPathEffectPage"
             Title="1D Path Effect">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker x:Name="effectStylePicker"
                Title="Effect Style"
                Grid.Row="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Translate</x:String>
                    <x:String>Rotate</x:String>
                    <x:String>Morph</x:String>
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           Grid.Row="1" />
    </Grid>
</ContentPage>
```

El archivo de código subyacente [**OneDimensionalPathEffectPage.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml.cs) define tres `SKPathEffect` objetos como campos. Todos ellos se crean mediante `SKPathEffect.Create1DPath` con los `SKPath` objetos creados con `SKPath.ParseSvgPathData` . El primero es un cuadro sencillo, el segundo es una forma de rombo y el tercero es un rectángulo. Se usan para mostrar los tres estilos de efecto:

```csharp
public partial class OneDimensionalPathEffectPage : ContentPage
{
    SKPathEffect translatePathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -10 -10 L 10 -10, 10 10, -10 10 Z"),
                                  24, 0, SKPath1DPathEffectStyle.Translate);

    SKPathEffect rotatePathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -10 0 L 0 -10, 10 0, 0 10 Z"),
                                  20, 0, SKPath1DPathEffectStyle.Rotate);

    SKPathEffect morphPathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -25 -10 L 25 -10, 25 10, -25 10 Z"),
                                  55, 0, SKPath1DPathEffectStyle.Morph);

    SKPaint pathPaint = new SKPaint
    {
        Color = SKColors.Blue
    };

    public OneDimensionalPathEffectPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath path = new SKPath())
        {
            path.MoveTo(new SKPoint(0, 0));
            path.CubicTo(new SKPoint(2 * info.Width, info.Height),
                         new SKPoint(-info.Width, info.Height),
                         new SKPoint(info.Width, 0));

            switch ((string)effectStylePicker.SelectedItem))
            {
                case "Translate":
                    pathPaint.PathEffect = translatePathEffect;
                    break;

                case "Rotate":
                    pathPaint.PathEffect = rotatePathEffect;
                    break;

                case "Morph":
                    pathPaint.PathEffect = morphPathEffect;
                    break;
            }

            canvas.DrawPath(path, pathPaint);
        }
    }
}
```

El `PaintSurface` controlador crea una curva de Bézier que se recorre en bucle y accede al selector para determinar qué `PathEffect` se debe usar para trazarla. Las tres opciones ( `Translate` , `Rotate` y `Morph` ) se muestran de izquierda a derecha:

[![Captura de pantalla triple de la página del efecto de la ruta de acceso 1D](effects-images/1dpatheffect-small.png)](effects-images/1dpatheffect-large.png#lightbox)

La ruta de acceso especificada en el `SKPathEffect.Create1DPath` método siempre se rellena. La ruta de acceso especificada en el `DrawPath` método siempre se traza si el `SKPaint` objeto tiene su `PathEffect` propiedad establecida en un efecto de ruta de acceso 1D. Observe que el `pathPaint` objeto no tiene ningún `Style` valor, que normalmente tiene como valor predeterminado `Fill` , pero la ruta de acceso se traza con independencia de.

El cuadro que se usa en el `Translate` ejemplo es un cuadrado de 20 píxeles y el `advance` argumento se establece en 24. Esta diferencia provoca un intervalo entre los cuadros cuando la línea es aproximadamente horizontal o vertical, pero los cuadros se superponen un poco cuando la línea es diagonal porque la diagonal del cuadro es 28,3 píxeles.

La forma de rombo en el `Rotate` ejemplo es también de 20 píxeles de ancho. El `advance` valor de se establece en 20 para que los puntos sigan tocando mientras se gira el rombo junto con la curvatura de la línea.

La forma de rectángulo en el `Morph` ejemplo es de 50 píxeles de ancho con un `advance` valor de 55 para hacer un pequeño espacio entre los rectángulos, ya que se doblan alrededor de la curva de Bézier.

Si el `advance` argumento es menor que el tamaño de la ruta de acceso, las rutas de acceso replicadas pueden superponerse. Esto puede dar lugar a algunos efectos interesantes. La página **cadena vinculada** muestra una serie de círculos superpuestos que parecen ser similares a una cadena vinculada, que se bloquea en la forma distintiva de una catenaria:

[![Captura de pantalla triple de la página cadena vinculada](effects-images/linkedchain-small.png)](effects-images/linkedchain-large.png#lightbox)

Mire muy bien y verá que no son círculos en realidad. Cada vínculo de la cadena tiene dos arcos, el tamaño y la posición, para que parezcan conectarse con vínculos contiguos.

Una cadena o cable de distribución de peso uniforme se bloquea en forma de una catenaria. Un arco construido en forma de beneficios de una catenaria invertida de una distribución igual de presión desde el peso de un arco. La catenaria tiene una descripción matemática aparentemente simple:

`y = a · cosh(x / a)`

*Cosh* es la función coseno hiperbólica. Para *x* igual a 0, *cosh* es cero e *y* es igual *a*. Este es el centro de la catenaria. Al igual que la función de *coseno* , se dice que *cosh* es *par*, lo que significa que *cosh (– x)* es igual a *cosh (x)* y los valores aumentan para aumentar los argumentos positivos o negativos. Estos valores describen las curvas que forman los lados de la catenaria.

Buscar el valor adecuado de para ajustar la *catenaria a las* dimensiones de la página del teléfono no es un cálculo directo. Si *w* y *h* son el ancho y el alto de un rectángulo, el valor óptimo de *un* satisface la siguiente ecuación:

`cosh(w / 2 / a) = 1 + h / a`

El método siguiente de la [`LinkedChainPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/LinkedChainPage.cs) clase incorpora esa igualdad haciendo referencia a las dos expresiones de la izquierda y derecha del signo igual como `left` y `right` . Para valores *pequeños de,* `left` es mayor que `right` ; para los valores grandes de *a*, `left` es menor que `right` . El `while` bucle se reduce en un valor óptimo de *un*:

```csharp
float FindOptimumA(float width, float height)
{
    Func<float, float> left = (float a) => (float)Math.Cosh(width / 2 / a);
    Func<float, float> right = (float a) => 1 + height / a;

    float gtA = 1;         // starting value for left > right
    float ltA = 10000;     // starting value for left < right

    while (Math.Abs(gtA - ltA) > 0.1f)
    {
        float avgA = (gtA + ltA) / 2;

        if (left(avgA) < right(avgA))
        {
            ltA = avgA;
        }
        else
        {
            gtA = avgA;
        }
    }

    return (gtA + ltA) / 2;
}
```

El `SKPath` objeto para los vínculos se crea en el constructor de la clase y `SKPathEffect` , a continuación, el objeto resultante se establece en la `PathEffect` propiedad del `SKPaint` objeto que se almacena como un campo:

```csharp
public class LinkedChainPage : ContentPage
{
    const float linkRadius = 30;
    const float linkThickness = 5;

    Func<float, float, float> catenary = (float a, float x) => (float)(a * Math.Cosh(x / a));

    SKPaint linksPaint = new SKPaint
    {
        Color = SKColors.Silver
    };

    public LinkedChainPage()
    {
        Title = "Linked Chain";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Create the path for the individual links
        SKRect outer = new SKRect(-linkRadius, -linkRadius, linkRadius, linkRadius);
        SKRect inner = outer;
        inner.Inflate(-linkThickness, -linkThickness);

        using (SKPath linkPath = new SKPath())
        {
            linkPath.AddArc(outer, 55, 160);
            linkPath.ArcTo(inner, 215, -160, false);
            linkPath.Close();

            linkPath.AddArc(outer, 235, 160);
            linkPath.ArcTo(inner, 395, -160, false);
            linkPath.Close();

            // Set that path as the 1D path effect for linksPaint
            linksPaint.PathEffect =
                SKPathEffect.Create1DPath(linkPath, 1.3f * linkRadius, 0,
                                          SKPath1DPathEffectStyle.Rotate);
        }
    }
    ...
}
```

El trabajo principal del `PaintSurface` controlador es crear una ruta de acceso para la propia catenaria. Después de determinar el nivel *óptimo y* almacenarlo en la `optA` variable, también debe calcular un desplazamiento desde la parte superior de la ventana. Después, puede acumular una colección de `SKPoint` valores para la catenaria, convertirlo en una ruta de acceso y dibujar la ruta de acceso con el objeto creado anteriormente `SKPaint` :

```csharp
public class LinkedChainPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        // Width and height of catenary
        int width = info.Width;
        float height = info.Height - linkRadius;

        // Find the optimum 'a' for this width and height
        float optA = FindOptimumA(width, height);

        // Calculate the vertical offset for that value of 'a'
        float yOffset = catenary(optA, -width / 2);

        // Create a path for the catenary
        SKPoint[] points = new SKPoint[width];

        for (int x = 0; x < width; x++)
        {
            points[x] = new SKPoint(x, yOffset - catenary(optA, x - width / 2));
        }

        using (SKPath path = new SKPath())
        {
            path.AddPoly(points, false);

            // And render that path with the linksPaint object
            canvas.DrawPath(path, linksPaint);
        }
    }
    ...
}
```

Este programa define la ruta de acceso `Create1DPath` que se usa en para tener su punto (0,0) en el centro. Parece razonable porque el punto (0,0) de la ruta de acceso está alineado con la línea o curva que se va a adornar. Sin embargo, puede usar un punto (0,0) no centrado en algunos efectos especiales.

La página de **correa transportadora** crea un trazado similar a una cinta transportadora Oblong con una parte superior e inferior curvada cuyo tamaño se ajusta a las dimensiones de la ventana. Esa ruta de acceso se traza con un `SKPaint` objeto simple de 20 píxeles de ancho y color gris y, a continuación, se vuelve a trazar con otro `SKPaint` objeto con un `SKPathEffect` objeto que hace referencia a una ruta de acceso similar a un pequeño cubo:

[![Captura de pantalla triple de la página de la correa transportadora](effects-images/conveyorbelt-small.png)](effects-images/conveyorbelt-large.png#lightbox)

El punto (0,0) de la ruta de acceso del cubo es el identificador, por lo que cuando `phase` se anima el argumento, los cubos parecen girar en torno a la cinta transportadora, es decir, con agua en la parte inferior y volcarlo en la parte superior.

La [`ConveyorBeltPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConveyorBeltPage.cs) clase implementa la animación con invalidaciones de los `OnAppearing` `OnDisappearing` métodos y. La ruta de acceso del cubo se define en el constructor de la página:

```csharp
public class ConveyorBeltPage : ContentPage
{
    SKCanvasView canvasView;
    bool pageIsActive = false;

    SKPaint conveyerPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 20,
        Color = SKColors.DarkGray
    };

    SKPath bucketPath = new SKPath();

    SKPaint bucketsPaint = new SKPaint
    {
        Color = SKColors.BurlyWood,
    };

    public ConveyorBeltPage()
    {
        Title = "Conveyor Belt";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Create the path for the bucket starting with the handle
        bucketPath.AddRect(new SKRect(-5, -3, 25, 3));

        // Sides
        bucketPath.AddRoundedRect(new SKRect(25, -19, 27, 18), 10, 10,
                                  SKPathDirection.CounterClockwise);
        bucketPath.AddRoundedRect(new SKRect(63, -19, 65, 18), 10, 10,
                                  SKPathDirection.CounterClockwise);

        // Five slats
        for (int i = 0; i < 5; i++)
        {
            bucketPath.MoveTo(25, -19 + 8 * i);
            bucketPath.LineTo(25, -13 + 8 * i);
            bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                             SKPathDirection.CounterClockwise, 65, -13 + 8 * i);
            bucketPath.LineTo(65, -19 + 8 * i);
            bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                             SKPathDirection.Clockwise, 25, -19 + 8 * i);
            bucketPath.Close();
        }

        // Arc to suggest the hidden side
        bucketPath.MoveTo(25, -17);
        bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                         SKPathDirection.Clockwise, 65, -17);
        bucketPath.LineTo(65, -19);
        bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                         SKPathDirection.CounterClockwise, 25, -19);
        bucketPath.Close();

        // Make it a little bigger and correct the orientation
        bucketPath.Transform(SKMatrix.MakeScale(-2, 2));
        bucketPath.Transform(SKMatrix.MakeRotationDegrees(90));
    }
    ...
```

El código de creación del depósito se completa con dos transformaciones que hacen que el depósito sea un poco más grande y lo gire lateralmente. Aplicar estas transformaciones era más fácil que ajustar todas las coordenadas del código anterior.

El `PaintSurface` controlador comienza definiendo una ruta de acceso para la propia cinta transportadora. Esto es simplemente un par de líneas y un par de semicírculos que se dibujan con una línea de gris oscuro de 20 píxeles:

```csharp
public class ConveyorBeltPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        float width = info.Width / 3;
        float verticalMargin = width / 2 + 150;

        using (SKPath conveyerPath = new SKPath())
        {
            // Straight verticals capped by semicircles on top and bottom
            conveyerPath.MoveTo(width, verticalMargin);
            conveyerPath.ArcTo(width / 2, width / 2, 0, SKPathArcSize.Large,
                               SKPathDirection.Clockwise, 2 * width, verticalMargin);
            conveyerPath.LineTo(2 * width, info.Height - verticalMargin);
            conveyerPath.ArcTo(width / 2, width / 2, 0, SKPathArcSize.Large,
                               SKPathDirection.Clockwise, width, info.Height - verticalMargin);
            conveyerPath.Close();

            // Draw the conveyor belt itself
            canvas.DrawPath(conveyerPath, conveyerPaint);

            // Calculate spacing based on length of conveyer path
            float length = 2 * (info.Height - 2 * verticalMargin) +
                           2 * ((float)Math.PI * width / 2);

            // Value will be somewhere around 200
            float spacing = length / (float)Math.Round(length / 200);

            // Now animate the phase; t is 0 to 1 every 2 seconds
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 2 / 2);
            float phase = -t * spacing;

            // Create the buckets PathEffect
            using (SKPathEffect bucketsPathEffect =
                        SKPathEffect.Create1DPath(bucketPath, spacing, phase,
                                                  SKPath1DPathEffectStyle.Rotate))
            {
                // Set it to the Paint object and draw the path again
                bucketsPaint.PathEffect = bucketsPathEffect;
                canvas.DrawPath(conveyerPath, bucketsPaint);
            }
        }
    }
}
```

La lógica para dibujar la cinta transportadora no funciona en modo horizontal.

Los depósitos se deben espaciar aproximadamente 200 píxeles en la cinta transportadora. Sin embargo, es probable que la cinta transportadora no sea un múltiplo de 200 píxeles de longitud, lo que significa que, a medida que el `phase` argumento de `SKPathEffect.Create1DPath` está animado, los depósitos entrarán en la existencia y desaparecerán.

Por esta razón, el programa calcula primero un valor denominado `length` que es la longitud de la cinta transportadora. Dado que la cinta transportadora consta de líneas rectas y semicírculos, se trata de un cálculo sencillo. Después, el número de depósitos se calcula dividiendo `length` por 200. Se redondea al entero más próximo y ese número se divide en `length` . El resultado es un espaciado para un número entero de cubos. El `phase` argumento es simplemente una fracción de eso.

## <a name="from-path-to-path-again"></a>Volver a la ruta de acceso

En la parte inferior del `DrawSurface` controlador de la **cinta transportadora**, comente la `canvas.DrawPath` llamada y reemplácela por el código siguiente:

```csharp
SKPath newPath = new SKPath();
bool fill = bucketsPaint.GetFillPath(conveyerPath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);
```

Como en el ejemplo anterior de `GetFillPath` , verá que los resultados son los mismos, excepto el color. Después de ejecutar `GetFillPath` , el `newPath` objeto contiene varias copias de la ruta de acceso del cubo, cada una de las cuales se coloca en el mismo punto en el que la animación la colocó en el momento de la llamada.

## <a name="hatching-an-area"></a>Sombrear un área

El [`SKPathEffect.Create2DLines`](xref:SkiaSharp.SKPathEffect.Create2DLine(System.Single,SkiaSharp.SKMatrix)) método rellena un área con líneas paralelas, a menudo denominadas *líneas de sombreado*. El método tiene la siguiente sintaxis:

```csharp
public static SKPathEffect Create2DLine (Single width, SKMatrix matrix)
```

El `width` argumento especifica el ancho del trazo de las líneas de sombreado. El `matrix` parámetro es una combinación de escalado y rotación opcional. El factor de escala indica el incremento de píxeles que Skia usa para espaciar las líneas de sombreado. La separación entre las líneas es el factor de escala menos el `width` argumento. Si el factor de escala es menor o igual que el `width` valor, no habrá espacio entre las líneas de trama y el área aparecerá rellenada. Especifique el mismo valor para el escalado horizontal y vertical.

De forma predeterminada, las líneas de trama son horizontales. Si el `matrix` parámetro contiene rotación, las líneas de sombreado se giran en el sentido de las agujas del reloj.

La página **relleno de trama** muestra este efecto de trazado. La [`HatchFillPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/HatchFillPage.cs) clase define tres efectos de la ruta de acceso como campos, el primero para las líneas de sombreado horizontal con un ancho de 3 píxeles con un factor de escala que indica que están separadas por 6 píxeles. La separación entre las líneas es por tanto tres píxeles. El segundo efecto de la ruta de acceso es para las líneas de trama vertical con un ancho de seis píxeles de distancia de 24 píxeles (por lo que la separación es de 18 píxeles), y la tercera es para las líneas de sombreado diagonal de 12 píxeles de espacio ancho 36 píxeles de distancia.

```csharp
public class HatchFillPage : ContentPage
{
    SKPaint fillPaint = new SKPaint();

    SKPathEffect horzLinesPath = SKPathEffect.Create2DLine(3, SKMatrix.MakeScale(6, 6));

    SKPathEffect vertLinesPath = SKPathEffect.Create2DLine(6,
        Multiply(SKMatrix.MakeRotationDegrees(90), SKMatrix.MakeScale(24, 24)));

    SKPathEffect diagLinesPath = SKPathEffect.Create2DLine(12,
        Multiply(SKMatrix.MakeScale(36, 36), SKMatrix.MakeRotationDegrees(45)));

    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 3,
        Color = SKColors.Black
    };
    ...
    static SKMatrix Multiply(SKMatrix first, SKMatrix second)
    {
        SKMatrix target = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

Observe el método de la matriz `Multiply` . Dado que los factores de escala horizontal y vertical son los mismos, no importa el orden en el que se multiplican las matrices de escala y giro.

El `PaintSurface` controlador usa estos tres efectos de la ruta de acceso con tres colores diferentes en combinación con `fillPaint` para rellenar un rectángulo redondeado de tamaño para ajustarse a la página. `Style`Se omite la propiedad establecida en `fillPaint` ; cuando el `SKPaint` objeto incluye un efecto de ruta de acceso creado a partir de `SKPathEffect.Create2DLine` , el área se rellena sin tener en cuenta lo siguiente:

```csharp
public class HatchFillPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath roundRectPath = new SKPath())
        {
            // Create a path
            roundRectPath.AddRoundedRect(
                new SKRect(50, 50, info.Width - 50, info.Height - 50), 100, 100);

            // Horizontal hatch marks
            fillPaint.PathEffect = horzLinesPath;
            fillPaint.Color = SKColors.Red;
            canvas.DrawPath(roundRectPath, fillPaint);

            // Vertical hatch marks
            fillPaint.PathEffect = vertLinesPath;
            fillPaint.Color = SKColors.Blue;
            canvas.DrawPath(roundRectPath, fillPaint);

            // Diagonal hatch marks -- use clipping
            fillPaint.PathEffect = diagLinesPath;
            fillPaint.Color = SKColors.Green;

            canvas.Save();
            canvas.ClipPath(roundRectPath);
            canvas.DrawRect(new SKRect(0, 0, info.Width, info.Height), fillPaint);
            canvas.Restore();

            // Outline the path
            canvas.DrawPath(roundRectPath, strokePaint);
        }
    }
    ...
}
```

Si observa detenidamente los resultados, verá que las líneas de sombreado rojo y azul no se limitan exactamente al rectángulo redondeado. (Esto es aparentemente una característica del código subyacente de Skia). Si no es satisfactorio, se muestra un enfoque alternativo para las líneas de sombreado diagonales en verde: el rectángulo redondeado se utiliza como trazado de recorte y las líneas de trama se dibujan en toda la página.

El `PaintSurface` controlador concluye con una llamada para trazar simplemente el rectángulo redondeado, por lo que puede ver la discrepancia con las líneas de sombreado rojo y azul:

[![Captura de pantalla triple de la página relleno de trama](effects-images/hatchfill-small.png)](effects-images/hatchfill-large.png#lightbox)

La pantalla de Android no parece realmente que: el escalado de la captura de pantalla ha provocado que las líneas rojas finas y los espacios finos se consoliden en líneas rojas aparentemente más anchas y espacios más amplios.

## <a name="filling-with-a-path"></a>Relleno con un trazado

[`SKPathEffect.Create2DPath`](xref:SkiaSharp.SKPathEffect.Create2DPath(SkiaSharp.SKMatrix,SkiaSharp.SKPath))Permite rellenar un área con una ruta de acceso que se replica horizontal y verticalmente, en efecto en el área en mosaico:

```csharp
public static SKPathEffect Create2DPath (SKMatrix matrix, SKPath path)
```

Los `SKMatrix` factores de escala indican el espaciado horizontal y vertical de la ruta de acceso replicada. Pero no se puede girar la ruta de acceso con este `matrix` argumento; si desea que la ruta de acceso se rote, gire la propia ruta de acceso con el `Transform` método definido por `SKPath` .

Normalmente, la ruta de acceso replicada se alinea con los bordes izquierdo y superior de la pantalla, en lugar de hacerlo con el área que se va a rellenar. Puede invalidar este comportamiento proporcionando factores de traducción entre 0 y los factores de escala para especificar desplazamientos horizontales y verticales desde los lados izquierdo y superior.

La página de **relleno del icono de ruta** muestra este efecto de la ruta de acceso. La ruta de acceso utilizada para colocar en mosaico el área se define como un campo en la [`PathTileFillPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathTileFillPage.cs) clase. Las coordenadas horizontal y vertical van de – 40 a 40, lo que significa que esta ruta de acceso es de 80 píxeles cuadrados:

```csharp
public class PathTileFillPage : ContentPage
{
    SKPath tilePath = SKPath.ParseSvgPathData(
        "M -20 -20 L 2 -20, 2 -40, 18 -40, 18 -20, 40 -20, " +
        "40 -12, 20 -12, 20 12, 40 12, 40 40, 22 40, 22 20, " +
        "-2 20, -2 40, -20 40, -20 8, -40 8, -40 -8, -20 -8 Z");
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Color = SKColors.Red;

            using (SKPathEffect pathEffect =
                   SKPathEffect.Create2DPath(SKMatrix.MakeScale(64, 64), tilePath))
            {
                paint.PathEffect = pathEffect;

                canvas.DrawRoundRect(
                    new SKRect(50, 50, info.Width - 50, info.Height - 50),
                    100, 100, paint);
            }
        }
    }
}
```

En el `PaintSurface` controlador, las `SKPathEffect.Create2DPath` llamadas establecen el espaciado horizontal y vertical en 64 para que los mosaicos cuadrados de 80 píxeles se superpongan. Afortunadamente, la ruta de acceso se parece a una pieza de Puzzle, con lo que la malla se asemeja a los mosaicos adyacentes:

[![Captura de pantalla triple de la página de relleno del mosaico de trazado](effects-images/pathtilefill-small.png)](effects-images/pathtilefill-large.png#lightbox)

El escalado desde la captura de pantalla original provoca cierta distorsión, especialmente en la pantalla de Android.

Tenga en cuenta que estos mosaicos siempre aparecen completos y nunca se truncan. En las dos primeras capturas de pantallas, no es evidente que el área que se rellena es un rectángulo redondeado. Si desea truncar estos mosaicos en un área determinada, use un trazado de recorte.

Pruebe a establecer la `Style` propiedad del `SKPaint` objeto en `Stroke` y verá los mosaicos individuales que se indican en lugar de rellenarse.

También es posible rellenar un área con un mapa de bits en mosaico, tal como se muestra en el artículo [**SkiaSharp de mosaico de mapas de bits**](../effects/shaders/bitmap-tiling.md).

## <a name="rounding-sharp-corners"></a>Esquinas de redondeo

El programa **Heptagon redondeado** presentado en las [**tres formas de dibujar un arco**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) usa un arco tangente para curvar los puntos de una figura de siete caras. La **otra página Heptagon redondeada** muestra un enfoque mucho más sencillo que usa un efecto de trazado creado a partir del [`SKPathEffect.CreateCorner`](xref:SkiaSharp.SKPathEffect.CreateCorner(System.Single)) método:

```csharp
public static SKPathEffect CreateCorner (Single radius)
```

Aunque el único argumento se denomina `radius` , debe establecerlo en la mitad del radio de redondeo deseado. (Esta es una característica del código subyacente de Skia).

Este es el `PaintSurface` controlador de la [`AnotherRoundedHeptagonPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AnotherRoundedHeptagonPage.cs) clase:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int numVertices = 7;
    float radius = 0.45f * Math.Min(info.Width, info.Height);
    SKPoint[] vertices = new SKPoint[numVertices];
    double vertexAngle = -0.5f * Math.PI;       // straight up

    // Coordinates of the vertices of the polygon
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        vertices[vertex] = new SKPoint(radius * (float)Math.Cos(vertexAngle),
                                       radius * (float)Math.Sin(vertexAngle));
        vertexAngle += 2 * Math.PI / numVertices;
    }

    float cornerRadius = 100;

    // Create the path
    using (SKPath path = new SKPath())
    {
        path.AddPoly(vertices, true);

        // Render the path in the center of the screen
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 10;

            // Set argument to half the desired corner radius!
            paint.PathEffect = SKPathEffect.CreateCorner(cornerRadius / 2);

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.DrawPath(path, paint);

            // Uncomment DrawCircle call to verify corner radius
            float offset = cornerRadius / (float)Math.Sin(Math.PI * (numVertices - 2) / numVertices / 2);
            paint.Color = SKColors.Green;
            // canvas.DrawCircle(vertices[0].X, vertices[0].Y + offset, cornerRadius, paint);
        }
    }
}
```

Puede usar este efecto con el trazado o el relleno en función de la `Style` propiedad del `SKPaint` objeto. Aquí se ejecuta:

[![Captura de pantalla triple de la otra página Heptagon redondeada](effects-images/anotherroundedheptagon-small.png)](effects-images/anotherroundedheptagon-large.png#lightbox)

Verá que este heptagon redondeado es idéntico al programa anterior. Si necesita más convencida de que el radio de la esquina es realmente 100 en lugar de los 50 especificados en la `SKPathEffect.CreateCorner` llamada, puede quitar la marca de comentario de la última instrucción del programa y ver un círculo de radio 100 superpuesto en la esquina.

## <a name="random-jitter"></a>Vibración aleatoria

A veces, las líneas rectas perfectas de los gráficos del equipo no son exactamente lo que desea y se desea un poco de aleatoriedad. En ese caso, querrá probar el [`SKPathEffect.CreateDiscrete`](xref:SkiaSharp.SKPathEffect.CreateDiscrete(System.Single,System.Single,System.UInt32)) método:

```csharp
public static SKPathEffect CreateDiscrete (Single segLength, Single deviation, UInt32 seedAssist)
```

Puede usar este efecto de trazado para contornear o rellenar. Las líneas se separan en segmentos conectados (la longitud aproximada se especifica mediante `segLength` ) y se extienden en distintas direcciones. La extensión de la desviación de la línea original se especifica mediante `deviation` .

El último argumento es un parámetro de inicialización que se usa para generar la secuencia pseudoaleatorios utilizada para el efecto. El efecto de vibración será ligeramente diferente para las distintas semillas. El argumento tiene un valor predeterminado de cero, lo que significa que el efecto es el mismo cada vez que se ejecuta el programa. Si desea una vibración diferente cada vez que se vuelva a dibujar la pantalla, puede establecer la inicialización en la `Millisecond` propiedad de un `DataTime.Now` valor (por ejemplo,).

La página **experimento de vibración** permite experimentar con distintos valores en el trazado de un rectángulo:

[![Captura de pantalla triple de la página JitterExperiment](effects-images/jitterexperiment-small.png)](effects-images/jitterexperiment-large.png#lightbox)

El programa es sencillo. El archivo [**JitterExperimentPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml) crea una instancia de dos `Slider` elementos y una `SKCanvasView` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Curves.JitterExperimentPage"
             Title="Jitter Experiment">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.Resources>
            <ResourceDictionary>
                <Style TargetType="Label">
                    <Setter Property="HorizontalTextAlignment" Value="Center" />
                </Style>

                <Style TargetType="Slider">
                    <Setter Property="Margin" Value="20, 0" />
                    <Setter Property="Minimum" Value="0" />
                    <Setter Property="Maximum" Value="100" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="segLengthSlider"
                Grid.Row="0"
                ValueChanged="sliderValueChanged" />

        <Label Text="{Binding Source={x:Reference segLengthSlider},
                              Path=Value,
                              StringFormat='Segment Length = {0:F0}'}"
               Grid.Row="1" />

        <Slider x:Name="deviationSlider"
                Grid.Row="2"
                ValueChanged="sliderValueChanged" />

        <Label Text="{Binding Source={x:Reference deviationSlider},
                              Path=Value,
                              StringFormat='Deviation = {0:F0}'}"
               Grid.Row="3" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="4"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

`PaintSurface`Se llama al controlador en el archivo de código subyacente [**JitterExperimentPage.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml.cs) cada vez que `Slider` cambia un valor. Llama a `SKPathEffect.CreateDiscrete` con los dos `Slider` valores y lo usa para trazar un rectángulo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float segLength = (float)segLengthSlider.Value;
    float deviation = (float)deviationSlider.Value;

    using (SKPaint paint = new SKPaint())
    {
        paint.Style = SKPaintStyle.Stroke;
        paint.StrokeWidth = 5;
        paint.Color = SKColors.Blue;

        using (SKPathEffect pathEffect = SKPathEffect.CreateDiscrete(segLength, deviation))
        {
            paint.PathEffect = pathEffect;

            SKRect rect = new SKRect(100, 100, info.Width - 100, info.Height - 100);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

También puede usar este efecto para el relleno, en cuyo caso el contorno del área rellena está sujeto a estas desviaciones aleatorias. La página de **texto vibración** muestra cómo usar este efecto de trazado para mostrar texto. La mayor parte del código del `PaintSurface` controlador de la [`JitterTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterTextPage.cs) clase está dedicada al ajuste de tamaño y al centro del texto:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string text = "FUZZY";

    using (SKPaint textPaint = new SKPaint())
    {
        textPaint.Color = SKColors.Purple;
        textPaint.PathEffect = SKPathEffect.CreateDiscrete(3f, 10f);

        // Adjust TextSize property so text is 95% of screen width
        float textWidth = textPaint.MeasureText(text);
        textPaint.TextSize *= 0.95f * info.Width / textWidth;

        // Find the text bounds
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);

        // Calculate offsets to center the text on the screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        canvas.DrawText(text, xText, yText, textPaint);
    }
}
```

Aquí se ejecuta en modo horizontal:

[![Captura de pantalla triple de la página JitterText](effects-images/jittertext-small.png)](effects-images/jittertext-large.png#lightbox)

## <a name="path-outlining"></a>Esquematización de trazados

Ya ha visto dos pocos ejemplos del [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath*) método de `SKPaint` , que existen dos versiones:

```csharp
public Boolean GetFillPath (SKPath src, SKPath dst, Single resScale = 1)

public Boolean GetFillPath (SKPath src, SKPath dst, SKRect cullRect, Single resScale = 1)
```

Solo se necesitan los dos primeros argumentos. El método accede a la ruta de acceso a la que hace referencia el `src` argumento, modifica los datos de la ruta de acceso en función de las propiedades de trazo del `SKPaint` objeto (incluida la `PathEffect` propiedad) y, a continuación, escribe los resultados en la ruta de acceso `dst` . El `resScale` parámetro permite reducir la precisión para crear una ruta de acceso de destino más pequeña y el `cullRect` argumento puede eliminar los contornos fuera de un rectángulo.

Un uso básico de este método no implica efectos de ruta de acceso: Si el `SKPaint` objeto tiene su `Style` propiedad establecida en `SKPaintStyle.Stroke` , y no *not* tiene su `PathEffect` conjunto, `GetFillPath` crea una ruta de acceso que representa un *contorno* de la ruta de acceso de origen como si hubiera sido trazada por las propiedades de Paint.

Por ejemplo, si la `src` ruta de acceso es un círculo simple de radius 500 y el `SKPaint` objeto especifica un ancho de trazo de 100, la `dst` ruta de acceso se convierte en dos círculos concéntricos, uno con un radio de 450 y otro con un radio de 550. Se llama al método `GetFillPath` porque el rellenado de esta `dst` ruta de acceso es el mismo que el de la `src` ruta de acceso. Pero también puede trazar el `dst` trazado para ver la ruta de acceso.

La **pulsación para describir la ruta de acceso** muestra esto. `SKCanvasView` `TapGestureRecognizer` Se crea una instancia de y en el archivo [**TapToOutlineThePathPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml) . El archivo de código subyacente [**TapToOutlineThePathPage.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml.cs) define tres `SKPaint` objetos como campos, dos para trazar con anchos de trazo de 100 y 20, y el tercero para el rellenado:

```csharp
public partial class TapToOutlineThePathPage : ContentPage
{
    bool outlineThePath = false;

    SKPaint redThickStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 100
    };

    SKPaint redThinStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 20
    };

    SKPaint blueFill = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue
    };

    public TapToOutlineThePathPage()
    {
        InitializeComponent();
    }

    void OnCanvasViewTapped(object sender, EventArgs args)
    {
        outlineThePath ^= true;
        (sender as SKCanvasView).InvalidateSurface();
    }
    ...
}
```

Si no se ha punteado la pantalla, el `PaintSurface` controlador utiliza `blueFill` los `redThickStroke` objetos Paint y para representar una ruta circular:

```csharp
public partial class TapToOutlineThePathPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath circlePath = new SKPath())
        {
            circlePath.AddCircle(info.Width / 2, info.Height / 2,
                                 Math.Min(info.Width / 2, info.Height / 2) -
                                 redThickStroke.StrokeWidth);

            if (!outlineThePath)
            {
                canvas.DrawPath(circlePath, blueFill);
                canvas.DrawPath(circlePath, redThickStroke);
            }
            else
            {
                using (SKPath outlinePath = new SKPath())
                {
                    redThickStroke.GetFillPath(circlePath, outlinePath);

                    canvas.DrawPath(outlinePath, blueFill);
                    canvas.DrawPath(outlinePath, redThinStroke);
                }
            }
        }
    }
}
```

El círculo se rellena y se traza como cabría esperar:

[![Captura de pantalla triple del punteo normal para describir la página de trazado](effects-images/taptooutlinethepathnormal-small.png)](effects-images/taptooutlinethepathnormal-large.png#lightbox)

Al puntear en la pantalla, `outlineThePath` se establece en `true` y el `PaintSurface` controlador crea un `SKPath` objeto nuevo y lo utiliza como ruta de acceso de destino en una llamada a `GetFillPath` en el `redThickStroke` objeto Paint. A continuación, la ruta de acceso de destino se rellena y se traza con `redThinStroke` , lo que da como resultado lo siguiente:

[![Captura de pantalla triple de la pulsación esquematizada para describir la página de trazado](effects-images/taptooutlinethepathoutlined-small.png)](effects-images/taptooutlinethepathoutlined-large.png#lightbox)

Los dos círculos rojos indican claramente que la ruta circular original se ha convertido en dos contornos circulares.

Este método puede ser muy útil para desarrollar rutas de acceso que se usarán para el `SKPathEffect.Create1DPath` método. Las rutas de acceso que especifique en estos métodos siempre se rellenan cuando se replican las rutas de acceso. Si no desea que se rellene la ruta de acceso completa, debe definir cuidadosamente los esquemas.

Por ejemplo, en el ejemplo de **cadena vinculada** , los vínculos se definieron con una serie de cuatro arcos; cada par se basaba en dos radios para describir el área de la ruta de acceso que se va a rellenar. Es posible reemplazar el código de la `LinkedChainPage` clase para que sea un poco diferente.

En primer lugar, querrá volver a definir la `linkRadius` constante:

```csharp
const float linkRadius = 27.5f;
const float linkThickness = 5;
```

`linkPath`Ahora solo hay dos arcos basados en ese radio único, con los ángulos de inicio y los ángulos de barrido deseados:

```csharp
using (SKPath linkPath = new SKPath())
{
    SKRect rect = new SKRect(-linkRadius, -linkRadius, linkRadius, linkRadius);
    linkPath.AddArc(rect, 55, 160);
    linkPath.AddArc(rect, 235, 160);

    using (SKPaint strokePaint = new SKPaint())
    {
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.StrokeWidth = linkThickness;

        using (SKPath outlinePath = new SKPath())
        {
            strokePaint.GetFillPath(linkPath, outlinePath);

            // Set that path as the 1D path effect for linksPaint
            linksPaint.PathEffect =
                SKPathEffect.Create1DPath(outlinePath, 1.3f * linkRadius, 0,
                                          SKPath1DPathEffectStyle.Rotate);

        }

    }
}
```

`outlinePath`Después, el objeto es el destinatario del contorno de `linkPath` cuando se traza con las propiedades especificadas en `strokePaint` .

Otro ejemplo del uso de esta técnica es la próxima para la ruta de acceso que se usa en un método.

## <a name="combining-path-effects"></a>Combinación de efectos de trazado

Los dos métodos de creación estáticos finales de `SKPathEffect` son [`SKPathEffect.CreateSum`](xref:SkiaSharp.SKPathEffect.CreateSum(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)) y [`SKPathEffect.CreateCompose`](xref:SkiaSharp.SKPathEffect.CreateCompose(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)) :

```csharp
public static SKPathEffect CreateSum (SKPathEffect first, SKPathEffect second)

public static SKPathEffect CreateCompose (SKPathEffect outer, SKPathEffect inner)
```

Ambos métodos combinan dos efectos de trazado para crear un efecto de trazado compuesto. El `CreateSum` método crea un efecto de trazado que es similar a los dos efectos de trazado aplicados por separado, mientras `CreateCompose` que aplica un efecto de trazado (el `inner` ) y, a continuación, aplica `outer` a ese.

Ya ha visto cómo el `GetFillPath` método de `SKPaint` puede convertir una ruta de acceso a otra en función de `SKPaint` las propiedades (incluido `PathEffect` ), por lo que no debe ser *tan* misterioso cómo un `SKPaint` objeto puede realizar esa operación dos veces con los dos efectos de la ruta de acceso especificados en los `CreateSum` `CreateCompose` métodos o.

Un uso obvio de `CreateSum` es definir un `SKPaint` objeto que rellena una ruta de acceso con un efecto de trazado y traza el trazado con otro efecto de trazado. Esto se muestra en el ejemplo de **gatos en el fotograma** , que muestra una matriz de gatos dentro de un fotograma con aristas con cresta:

[![Captura de pantalla triple de la página de gatos en el marco](effects-images/catsinframe-small.png)](effects-images/catsinframe-large.png#lightbox)

La [`CatsInFramePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CatsInFramePage.cs) clase comienza definiendo varios campos. Puede que reconozca el primer campo de la [`PathDataCatPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) clase en el artículo de datos de la [**ruta de acceso de SVG**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md) . La segunda ruta de acceso se basa en una línea y un arco para el patrón de festón del marco:

```csharp
public class CatsInFramePage : ContentPage
{
    // From PathDataCatPage.cs
    SKPath catPath = SKPath.ParseSvgPathData(
        "M 160 140 L 150 50 220 103" +              // Left ear
        "M 320 140 L 330 50 260 103" +              // Right ear
        "M 215 230 L 40 200" +                      // Left whiskers
        "M 215 240 L 40 240" +
        "M 215 250 L 40 280" +
        "M 265 230 L 440 200" +                     // Right whiskers
        "M 265 240 L 440 240" +
        "M 265 250 L 440 280" +
        "M 240 100" +                               // Head
        "A 100 100 0 0 1 240 300" +
        "A 100 100 0 0 1 240 100 Z" +
        "M 180 170" +                               // Left eye
        "A 40 40 0 0 1 220 170" +
        "A 40 40 0 0 1 180 170 Z" +
        "M 300 170" +                               // Right eye
        "A 40 40 0 0 1 260 170" +
        "A 40 40 0 0 1 300 170 Z");

    SKPaint catStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 5
    };

    SKPath scallopPath =
        SKPath.ParseSvgPathData("M 0 0 L 50 0 A 60 60 0 0 1 -50 0 Z");

    SKPaint framePaint = new SKPaint
    {
        Color = SKColors.Black
    };
    ...
}
```

`catPath`Se podría usar en el `SKPathEffect.Create2DPath` método si la `SKPaint` propiedad del objeto `Style` está establecida en `Stroke` . Sin embargo, si `catPath` se usa directamente en este programa, se rellenará todo el encabezado del gato y los bigotes no estarán visibles. (Pruébelo!) Es necesario obtener el contorno de la ruta de acceso y usar ese contorno en el `SKPathEffect.Create2DPath` método.

El constructor realiza este trabajo. En primer lugar, se aplican dos transformaciones a `catPath` para pasar el punto (0,0) al centro y reducirlo verticalmente. `GetFillPath`Obtiene todos los contornos de los contornos de `outlinedCatPath` , y ese objeto se utiliza en la `SKPathEffect.Create2DPath` llamada a. Los factores de escala en el `SKMatrix` valor son ligeramente mayores que el tamaño horizontal y vertical del gato para proporcionar un pequeño búfer entre los mosaicos, mientras que los factores de conversión se derivaron ligeramente de forma empírica para que un gato completo esté visible en la esquina superior izquierda del marco:

```csharp
public class CatsInFramePage : ContentPage
{
    ...
    public CatsInFramePage()
    {
        Title = "Cats in Frame";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Move (0, 0) point to center of cat path
        catPath.Transform(SKMatrix.MakeTranslation(-240, -175));

        // Now catPath is 400 by 250
        // Scale it down to 160 by 100
        catPath.Transform(SKMatrix.MakeScale(0.40f, 0.40f));

        // Get the outlines of the contours of the cat path
        SKPath outlinedCatPath = new SKPath();
        catStroke.GetFillPath(catPath, outlinedCatPath);

        // Create a 2D path effect from those outlines
        SKPathEffect fillEffect = SKPathEffect.Create2DPath(
            new SKMatrix { ScaleX = 170, ScaleY = 110,
                           TransX = 75, TransY = 80,
                           Persp2 = 1 },
            outlinedCatPath);

        // Create a 1D path effect from the scallop path
        SKPathEffect strokeEffect =
            SKPathEffect.Create1DPath(scallopPath, 75, 0, SKPath1DPathEffectStyle.Rotate);

        // Set the sum the effects to frame paint
        framePaint.PathEffect = SKPathEffect.CreateSum(fillEffect, strokeEffect);
    }
    ...
}
```

A continuación, el constructor llama a `SKPathEffect.Create1DPath` para el fotograma con refestón. Observe que el ancho de la ruta de acceso es de 100 píxeles, pero el avance es de 75 píxeles para que la ruta de acceso replicada se superponga alrededor del marco. La última instrucción del constructor llama `SKPathEffect.CreateSum` a para combinar los dos efectos de la ruta de acceso y establecer el resultado en el `SKPaint` objeto.

Todo este trabajo permite `PaintSurface` que el controlador sea bastante sencillo. Solo necesita definir un rectángulo y dibujarlo mediante `framePaint` :

```csharp
public class CatsInFramePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect rect = new SKRect(50, 50, info.Width - 50, info.Height - 50);
        canvas.ClipRect(rect);
        canvas.DrawRect(rect, framePaint);
    }
}
```

Los algoritmos que se encuentran detrás de los efectos de trazado siempre provocan que se muestre la ruta de acceso completa que se usa para el trazado o el relleno, lo que puede hacer que algunos objetos visuales aparezcan fuera del rectángulo. La `ClipRect` llamada anterior a la `DrawRect` llamada permite que los objetos visuales sean considerablemente más limpios. (Pruébelo sin recorte).

Es habitual usar `SKPathEffect.CreateCompose` para agregar alguna vibración a otro efecto de ruta de acceso. Ciertamente puede experimentar por su cuenta, pero este es un ejemplo algo diferente:

Las **líneas de trama discontinua** rellenan una elipse con líneas de sombreado que se rayan. La mayor parte del trabajo de la [`DashedHatchLinesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DashedHatchLinesPage.cs) clase se realiza directamente en las definiciones de campo. Estos campos definen un efecto de guión y un efecto de sombreado. Se definen como `static` porque se hace referencia a ellos en una `SKPathEffect.CreateCompose` llamada en la `SKPaint` definición:

```csharp
public class DashedHatchLinesPage : ContentPage
{
    static SKPathEffect dashEffect =
        SKPathEffect.CreateDash(new float[] { 30, 30 }, 0);

    static SKPathEffect hatchEffect = SKPathEffect.Create2DLine(20,
        Multiply(SKMatrix.MakeScale(60, 60),
                 SKMatrix.MakeRotationDegrees(45)));

    SKPaint paint = new SKPaint()
    {
        PathEffect = SKPathEffect.CreateCompose(dashEffect, hatchEffect),
        StrokeCap = SKStrokeCap.Round,
        Color = SKColors.Blue
    };
    ...
    static SKMatrix Multiply(SKMatrix first, SKMatrix second)
    {
        SKMatrix target = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

El `PaintSurface` controlador debe contener solo la sobrecarga estándar más una llamada a `DrawOval` :

```csharp
public class DashedHatchLinesPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.DrawOval(info.Width / 2, info.Height / 2,
                        0.45f * info.Width, 0.45f * info.Height,
                        paint);
    }
    ...
}
```

Como ya ha descubierto, las líneas de trama no se restringen exactamente al interior del área y, en este ejemplo, siempre comienzan a la izquierda con un guión entero:

[![Captura de pantalla triple de la página líneas de trama discontinua](effects-images/dashedhatchlines-small.png)](effects-images/dashedhatchlines-large.png#lightbox)

Ahora que ha visto los efectos de la ruta de acceso que van desde puntos simples y guiones a combinaciones extrañas, use su imaginación y vea lo que puede crear.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
