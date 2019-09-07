---
title: Efectos de la ruta de acceso de SkiaSharp
description: En este artículo se explica los distintos efectos de ruta de acceso de SkiaSharp que permiten a las rutas de acceso que se usará para trazado y rellenar y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 95167D1F-A718-405A-AFCC-90E596D422F3
author: davidbritch
ms.author: dabritch
ms.date: 07/29/2017
ms.openlocfilehash: 82755a6a87ec0a47c10aac7078beeab6e14c218d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759383"
---
# <a name="path-effects-in-skiasharp"></a>Efectos de la ruta de acceso de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Descubra los distintos efectos de la ruta de acceso que permiten a las rutas de acceso que se usará para trazado y rellenar_

Un *efecto de la ruta de acceso* es una instancia de la [ `SKPathEffect` ](xref:SkiaSharp.SKPathEffect) clase que se crea con uno de los ocho métodos de creación estático definidos por la clase. El `SKPathEffect` objeto, a continuación, se establece en el [ `PathEffect` ](xref:SkiaSharp.SKPaint.PathEffect) propiedad de un [ `SKPaint` ](xref:SkiaSharp.SKPaint) objeto para una variedad de efectos interesantes, por ejemplo, trazado de una línea con una ruta de acceso replicada pequeño :

![El ejemplo de cadena vinculada](effects-images/patheffectsample.png)

Efectos de la ruta de acceso le permiten:

- Trazar una línea con puntos y guiones
- Trazar una línea con cualquier ruta de acceso relleno
- Rellenar un área con líneas de trama.
- Rellenar un área con una ruta de acceso en mosaico
- Asegúrese de que las esquinas redondeadas vivas
- Agregar aleatorio "vibración" a las líneas y curvas

Además, puede combinar dos o más efectos del trazado.

En este artículo también muestra cómo usar el [ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath*) método `SKPaint` para convertir una ruta de acceso en otra ruta de acceso mediante la aplicación de las propiedades de `SKPaint`, incluidos `StrokeWidth` y `PathEffect`. Esto da como resultado algunas técnicas interesantes, como la obtención de una ruta de acceso que es un esquema de otra ruta de acceso. `GetFillPath` También es útil en relación con los efectos de la ruta de acceso.

## <a name="dots-and-dashes"></a>Puntos y guiones

El uso de la [ `PathEffect.CreateDash` ](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single)) método descrito en el artículo [ **puntos y guiones**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md). El primer argumento del método es una matriz que contiene un número par de dos o más valores, alternando entre las longitudes de guiones y las longitudes de los espacios entre los guiones:

```csharp
public static SKPathEffect CreateDash (Single[] intervals, Single phase)
```

Estos valores son *no* en relación con el ancho del trazo. Por ejemplo, si el ancho del trazo es 10, y desea que una línea formada por cuadrados guiones y espacios de square, establezca el `intervals` matriz {10, 10}. El `phase` argumento indica dónde dentro del modelo de guiones empieza la línea. En este ejemplo, si desea que la línea para comenzar con la distancia cuadrada, establezca `phase` a 10.

Los extremos de los guiones se ven afectados por la `StrokeCap` propiedad de `SKPaint`. Para anchos de ancho de trazo, es muy común para establecer esta propiedad en `SKStrokeCap.Round` para redondear los extremos de los guiones. En este caso, los valores de la `intervals` matriz realice *no* incluye la longitud adicional procedente del redondeo. Este hecho implica que un punto circular, es necesario especificar un ancho de cero. Para un ancho de trazo de 10, para crear una línea con puntos circulares y espacios entre los puntos del diámetro del mismo, use un `intervals` matriz de {0, 20}.

El **texto animado de números separados por puntos** página es similar a la **texto con contorno** página se describe en el artículo [ **la integración de texto y gráficos** ](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md) en que muestra que describe los caracteres de texto estableciendo el `Style` propiedad de la `SKPaint` objeto `SKPaintStyle.Stroke`. Además, **texto animado de números separados por puntos** usa `SKPathEffect.CreateDash` para proporcionar a este esquema un aspecto con puntos y el programa también anima la `phase` argumento de la `SKPathEffect.CreateDash` método para hacer que los puntos parecen viaja alrededor del texto caracteres. Esta es la página en modo horizontal:

[![Captura de pantalla triple de la página de texto con puntos animados](effects-images/animateddottedtext-small.png)](effects-images/animateddottedtext-large.png#lightbox)

El [ `AnimatedDottedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) clase comienza definiendo algunas constantes y también reemplaza la `OnAppearing` y `OnDisappearing` métodos para la animación:

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

El `PaintSurface` controlador comienza creando una `SKPaint` objeto para mostrar el texto. El `TextSize` propiedad se ajusta según el ancho de la pantalla:

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

Hacia el final del método, el `SKPathEffect.CreateDash` es llamar al método mediante el `dashArray` que se define como un campo y el texto animado `phase` valor. El `SKPathEffect` instancia se establece en el `PathEffect` propiedad de la `SKPaint` objeto para mostrar el texto.

Como alternativa, puede establecer el `SKPathEffect` de objeto para el `SKPaint` objeto antes de medir el texto y se centraban en la página. Sin embargo, en ese caso, los puntos animados y guiones provocar alguna variación en el tamaño del texto representado y el texto se tiende a Vibrar un poco. (¡Pruébelo!)

También podrá observar que como círculo puntos animados en torno a los caracteres de texto, hay un punto determinado en cada curva cerrada donde los puntos parecen ENTRAR y salir de existencia. Esto es donde la ruta de acceso que define el contorno de carácter empieza y termina. Si la longitud de ruta de acceso no es un múltiplo integral de la longitud del patrón de dash (en este caso 20 píxeles), puede ajustar única parte de ese patrón al final de la ruta de acceso.

Es posible ajustar la longitud de la del patrón de guiones para ajustarse a la longitud de la ruta de acceso, pero que necesita determinar la longitud de la ruta de acceso, una técnica que se trata en el artículo [ **información de ruta de acceso y de enumeración** ](information.md).

El **Dot / Dash Morph** programa anima el mismo patrón de guiones, por lo que parecen divida en puntos, que combinan guiones formulario nuevo guiones:

[![Captura de pantalla triple de la página de transformación de guiones de puntos](effects-images/dotdashmorph-small.png)](effects-images/dotdashmorph-large.png#lightbox)

El [ `DotDashMorphPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) clase invalidaciones el `OnAppearing` y `OnDisappearing` métodos exactamente igual que hizo el programa anterior, pero la clase define la `SKPaint` objeto como un campo:

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

El `PaintSurface` controlador crea un trazado elíptico en función del tamaño de la página y ejecuta una sección larga de código que establece la `dashArray` y `phase` variables. Como la variable animada `t` varía entre 0 y 1, el `if` bloques dividen ese tiempo en trimestres de cuatro y cada una de esas trimestres, `tsub` también comprendido entre 0 y 1. Al final, el programa crea la `SKPathEffect` y lo establece en el `SKPaint` objeto para el dibujo.

## <a name="from-path-to-path"></a>Desde la ruta de acceso a la ruta de acceso

El [ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,System.Single)) método `SKPaint` convierte una ruta de acceso en el otro en función de configuración el `SKPaint` objeto. Para ver cómo funciona esto, reemplace el `canvas.DrawPath` llamar en el programa anterior con el código siguiente:

```csharp
SKPath newPath = new SKPath();
bool fill = ellipsePaint.GetFillPath(ellipsePath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);

```

En este código nuevo, el `GetFillPath` llamar convierte la `ellipsePath` (que es simplemente una elipse) en `newPath`, que, a continuación, se muestra con `newPaint`. El `newPaint` objeto se crea con todos los valores de propiedad predeterminados, salvo que el `Style` se establece en función de la propiedad con el valor booleano devolver el valor de `GetFillPath`.

Los objetos visuales son idénticos salvo por el color, que se establece en `ellipsePaint` pero no `newPaint`. En lugar de la elipse simple definida en `ellipsePath`, `newPath` contiene numerosos contornos de ruta de acceso que definen la serie de puntos y guiones. Éste es el resultado de aplicar las distintas propiedades de `ellipsePaint` (en concreto, `StrokeWidth`, `StrokeCap`, y `PathEffect`) a `ellipsePath` y colocando la ruta de acceso resultante en `newPath`. El `GetFillPath` método devuelve un valor booleano que indica si la ruta de acceso de destino se va a rellenar o no; en este ejemplo, el valor devuelto es `true` para rellenar la ruta de acceso.

Intente cambiar el `Style` en `newPaint` a `SKPaintStyle.Stroke` y verá los contornos de ruta de acceso individuales descritos con una línea de un ancho en píxeles.

## <a name="stroking-with-a-path"></a>Trazado con una ruta de acceso

El [ `SKPathEffect.Create1DPath` ](xref:SkiaSharp.SKPathEffect.Create1DPath(SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPath1DPathEffectStyle)) es conceptualmente similar a método `SKPathEffect.CreateDash` salvo que especifique una ruta de acceso en lugar de un patrón de guiones y espacios. Esta ruta de acceso se replica varias veces en el trazo de la línea o curva.

La sintaxis es la siguiente:

```csharp
public static SKPathEffect Create1DPath (SKPath path, Single advance,
                                         Single phase, SKPath1DPathEffectStyle style)
```

En general, la ruta de acceso que se pasa a `Create1DPath` será pequeño y centrado alrededor del punto (0, 0). El `advance` parámetro indica la distancia entre los centros de la ruta de acceso como la ruta de acceso se replica en la línea. Normalmente, se establece este argumento para el ancho aproximado de la ruta de acceso. El `phase` desempeña argumento el mismo rol aquí que lo hace en el `CreateDash` método.

El [ `SKPath1DPathEffectStyle` ](xref:SkiaSharp.SKPath1DPathEffectStyle) tiene tres miembros:

- `Translate`
- `Rotate`
- `Morph`

El `Translate` miembro hace que la ruta de acceso a permanecer en la misma orientación se replica a lo largo de una línea o curva. Para `Rotate`, la ruta de acceso se gira en función de una tangente a la curva. La ruta de acceso tiene su orientación normal para las líneas horizontales. `Morph` es similar a `Rotate` , salvo que la ruta de acceso también se curva para que coincida con la curvatura de la línea que se trazan.

El **efecto de ruta de acceso de D 1** página muestra estas tres opciones. El [ **OneDimensionalPathEffectPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml) archivo define un selector que contiene tres elementos que corresponden a los tres miembros de la enumeración:

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

El [ **OneDimensionalPathEffectPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml.cs) archivo de código subyacente define tres `SKPathEffect` objetos como campos. Estos se crearon con `SKPathEffect.Create1DPath` con `SKPath` objetos creados con `SKPath.ParseSvgPathData`. El primero es un cuadro simple, el segundo es una forma de rombo y el tercero es un rectángulo. Estos se usan para mostrar los estilos de tres efecto:

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

El `PaintSurface` controlador crea una curva de Bézier que pasa a su alrededor y tiene acceso el selector para determinar qué `PathEffect` debe usarse para trazar en él. Las tres opciones: `Translate`, `Rotate`, y `Morph` , se muestran de izquierda a derecha:

[![Captura de pantalla triple de la página del efecto de la ruta de acceso 1D](effects-images/1dpatheffect-small.png)](effects-images/1dpatheffect-large.png#lightbox)

La ruta de acceso especificada en el `SKPathEffect.Create1DPath` siempre se rellena el método. La ruta de acceso especificada en el `DrawPath` siempre se traza el método si el `SKPaint` objeto tiene su `PathEffect` propiedad establecida en un efecto de la ruta de acceso de 1 día. Tenga en cuenta que el `pathPaint` objeto no tiene ningún `Style` configuración, que normalmente el valor predeterminado es `Fill`, pero la ruta de acceso se traza independientemente.

El cuadro que se utiliza en el `Translate` ejemplo es 20 píxeles cuadrados y el `advance` argumento está establecido en 24. Esta diferencia causa un espacio entre los cuadros cuando la línea es casi horizontal o vertical, pero las casillas se superponen un poco cuando se activa la línea diagonal porque es de la diagonal del cuadro 28,3 píxeles.

La forma de rombo en el `Rotate` ejemplo también es 20 píxeles de ancho. El `advance` se establece en 20 para que sigan los puntos de toque como el rombo se gira junto con la curvatura de la línea.

La forma de rectángulo en el `Morph` ejemplo es 50 píxeles de anchos, con un `advance` configuración de 55 para realizar un pequeño espacio entre los rectángulos que están dobladas en torno a la curva de Bézier.

Si el `advance` argumento es menor que el tamaño de la ruta de acceso, a continuación, se pueden superponer las rutas de acceso duplicadas. Esto puede dar lugar a que algunos efectos interesantes. El **cadena vinculada** página muestra una serie de círculos que parecen ser similar a una cadena vinculada, que se bloquea en la forma de un catenaria distintivo superpuestos:

[![Captura de pantalla triple de la página cadena vinculada](effects-images/linkedchain-small.png)](effects-images/linkedchain-large.png#lightbox)

Buscar muy similares y verá que estos no son realmente círculos. Cada vínculo de la cadena es dos arcos, tamaño y se colocan por lo que parecen para conectarse con vínculos adyacentes.

Una cadena o un cable de distribución de peso uniforme se bloquea en forma de un catenaria. Un arco creado en forma de un catenaria invertido se beneficia de una distribución equitativa de presión de la ponderación de un arco. La catenaria tiene una descripción matemática aparentemente simple:

`y = a · cosh(x / a)`

El *cosh* es la función de coseno hiperbólico. Para *x* igual a 0, *cosh* es cero y *y* es igual a *un*. Es el centro de la catenaria. Al igual que el *coseno* función, *cosh* se dice que *incluso*, lo que significa que *cosh(–x)* es igual a *cosh(x)* , y aumentan los valores para aumentar los argumentos positivos o negativos. Estos valores describen las curvas que conforman los lados de la catenaria.

Buscar el valor adecuado de *un* para ajustarse a la catenaria las dimensiones de la página del teléfono no es un cálculo directo. Si *w* y *h* es el ancho y alto de un rectángulo, el valor óptimo de *un* satisface la siguiente ecuación:

`cosh(w / 2 / a) = 1 + h / a`

El método siguiente en el [ `LinkedChainPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/LinkedChainPage.cs) clase incorpora igualdad haciendo referencia a las dos expresiones de la izquierda y derecha del signo igual como `left` y `right`. Para valores pequeños de *un*, `left` es mayor que `right`; para valores grandes de *un*, `left` es menor que `right`. El `while` bucle se reduce en un valor óptimo de *un*:

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

El `SKPath` para los vínculos se crean en el constructor de clase y el conjunto resultante de objetos `SKPathEffect` objeto, a continuación, se establece en el `PathEffect` propiedad de la `SKPaint` objeto almacenado como un campo:

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

El trabajo principal de la `PaintSurface` controlador consiste en crear una ruta de acceso para la catenaria propio. Después de determinar la óptima *un* y almacenarla en la `optA` variable, también debe calcular un desplazamiento desde la parte superior de la ventana. A continuación, puede acumular una colección de `SKPoint` valores para la catenaria, que convierta en una ruta de acceso y dibujar la ruta de acceso con el que se han creado previamente `SKPaint` objeto:

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

Este programa define la ruta de acceso en `Create1DPath` tener su (0, 0) de punto en el centro. Esto parece razonable porque el (0, 0) punto de la ruta de acceso se alinea con la línea o curva que está adornar. Sin embargo, puede usar no centrado (0, 0) el punto para algunos efectos especiales.

El **cinta transportadora** página crea una ruta de acceso similar a una banda transportadora rectangulares con una curva superior e inferior cuyo tamaño se ajusta a las dimensiones de la ventana. Esa ruta de acceso se traza con un sencillo `SKPaint` 20 píxeles de ancho y color gris de objetos y, a continuación, se trazan a intentarlo con otro `SKPaint` objeto con un `SKPathEffect` objeto hace referencia a una ruta de acceso que parezca un poco depósito:

[![Captura de pantalla triple de la página de la correa transportadora](effects-images/conveyorbelt-small.png)](effects-images/conveyorbelt-large.png#lightbox)

La (0, 0) de la ruta de acceso del cubo es el identificador, por lo que cuando el `phase` argumento está animado, parecen que los cubos giran en torno a la cinta transportadora, quizás scooping agua en la parte inferior y el volcado horizontal en la parte superior.

El [ `ConveyorBeltPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConveyorBeltPage.cs) clase implementa la animación con reemplazos de la `OnAppearing` y `OnDisappearing` métodos. La ruta de acceso para el cubo se define en el constructor de la página:

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

El código de creación de depósitos se completa con dos transformaciones que hacen que el depósito un poco más grande y activarlo lateralmente. Aplicar estas transformaciones era más fácil que ajustar todas las coordenadas en el código anterior.

El `PaintSurface` controlador empieza definiendo una ruta de acceso de la cinta transportadora propio. Esto es simplemente un par de líneas y un par de punto y coma de los círculos que se dibujan con la línea gris oscuro 20 píxeles de ancho:

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

Los depósitos deben se espacian acerca de 200 píxeles en la cinta transportadora. Sin embargo, la cinta transportadora probablemente no es un múltiplo de 200 píxeles largos, lo que significa que, como el `phase` argumento de `SKPathEffect.Create1DPath` está animada, depósitos aparecerá dentro y fuera de existencia.

Por este motivo, el programa en primer lugar calcula un valor denominado `length` que tiene la longitud de la cinta transportadora. Dado que la cinta transportadora consta de líneas rectas y círculos de punto y coma, esto es un cálculo simple. A continuación, el número de depósitos se calcula dividiendo `length` por 200. Esto se redondea al entero más cercano y que número es, a continuación, dividen `length`. El resultado es un espacio para un número entero de depósitos. El `phase` argumento es simplemente una fracción de eso.

## <a name="from-path-to-path-again"></a>Desde la ruta de acceso a la ruta de acceso nuevo

En la parte inferior de la `DrawSurface` controlador en **cinta transportadora**, marque como comentario el `canvas.DrawPath` llamar a y reemplácelo por el código siguiente:

```csharp
SKPath newPath = new SKPath();
bool fill = bucketsPaint.GetFillPath(conveyerPath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);
```

Igual que con el ejemplo anterior de `GetFillPath`, verá que los resultados son iguales, salvo el color. Después de ejecutar `GetFillPath`, el `newPath` objeto contiene varias copias de la ruta de acceso de depósito, cada uno está situado en el mismo lugar que la animación los coloca en el momento de la llamada.

## <a name="hatching-an-area"></a>Un área de sombreado

El [ `SKPathEffect.Create2DLines` ](xref:SkiaSharp.SKPathEffect.Create2DLine(System.Single,SkiaSharp.SKMatrix)) método rellena un área con líneas paralelas, a menudo denominada *líneas de sombreado*. El método tiene la siguiente sintaxis:

```csharp
public static SKPathEffect Create2DLine (Single width, SKMatrix matrix)
```

El `width` argumento especifica el ancho del trazo de las líneas de trama. El `matrix` parámetro es una combinación de rotación de escalado y opcional. El factor de escala indica el incremento de píxel que Skia se usa para asignar espacio a las líneas de trama. La separación entre las líneas es el factor de escala menos el `width` argumento. Si el factor de escala es menor o igual que el `width` valor, no habrá ningún espacio entre las líneas de trama y aparecerá el área que se debe rellenar. Especifique el mismo valor para el escalado horizontal y vertical.

De forma predeterminada, las líneas de sombreado son horizontales. Si el `matrix` parámetro contiene la rotación, las líneas de trama se giran hacia la derecha.

El **trama de relleno** página muestra el efecto de esta ruta de acceso. El [ `HatchFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/HatchFillPage.cs) clase define tres efectos de la ruta de acceso como campos, la primera para las líneas de sombreado horizontal con un ancho de 3 píxeles con un factor de escala que indica que están espaciados 6 píxeles. La separación entre las líneas, por tanto, es tres píxeles. El segundo efecto de la ruta de acceso es para las líneas de sombreado vertical con un ancho de seis píxeles espacian 24 píxeles (por lo que la separación es de 18 píxeles), y el tercero es 12 píxeles espaciados anchos 36 píxeles de líneas de sombreado diagonal.

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

Tenga en cuenta la matriz `Multiply` método. Dado que los factores de escala horizontales y verticales son los mismos, no importa el orden en el que se multiplican las matrices de ajuste de escala y giro.

El `PaintSurface` controlador utiliza estos efectos de la ruta de tres acceso con tres colores diferentes en combinación con `fillPaint` para rellenar un rectángulo redondeado de tamaño para ajustarse a la página. El `Style` propiedad establecida en `fillPaint` se omite; cuando el `SKPaint` objeto incluye un efecto de la ruta de acceso creado a partir de `SKPathEffect.Create2DLine`, en cualquier caso, se rellena el área:

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

Si observa detenidamente los resultados, verá que no se limitan las líneas de sombreado de color rojo y azul exactamente al rectángulo redondeado. (Aparentemente es una característica del código subyacente de Skia.) Si no es satisfactorio, se muestra un enfoque alternativo para las líneas de sombreado diagonales en verde: El rectángulo redondeado se utiliza como trazado de recorte y las líneas de sombreado se dibujan en toda la página.

El `PaintSurface` controlador concluye con una llamada a simplemente trazar el rectángulo redondeado, para que pueda ver la discrepancia con las líneas de sombreado de color rojo y azul:

[![Captura de pantalla triple de la página relleno de trama](effects-images/hatchfill-small.png)](effects-images/hatchfill-large.png#lightbox)

En realidad, la pantalla Android no tiene el aspecto siguiente: El escalado de la captura de pantalla ha provocado que las líneas rojas finas y los espacios finos se consoliden en líneas rojas aparentemente más anchas y espacios más amplios.

## <a name="filling-with-a-path"></a>Rellenar con una ruta de acceso

El [ `SKPathEffect.Create2DPath` ](xref:SkiaSharp.SKPathEffect.Create2DPath(SkiaSharp.SKMatrix,SkiaSharp.SKPath)) permite llenar un área con una ruta de acceso que se replica horizontal y verticalmente, en vigor el área de mosaico:

```csharp
public static SKPathEffect Create2DPath (SKMatrix matrix, SKPath path)
```

El `SKMatrix` factores de escala indican el espaciado horizontal y vertical de la ruta de acceso duplicada. Pero no se puede girar la ruta de acceso mediante este `matrix` argumento; si desea que la ruta de acceso que se gira, girar la ruta de acceso mediante la `Transform` definido por el método `SKPath`.

La ruta de acceso replicada normalmente se alinea con los bordes superiores e izquierdos de la pantalla en lugar de con el área que se va a rellenar. Puede invalidar este comportamiento, ya que proporciona factores de traslación entre 0 y los factores de escala para especificar desplazamientos horizontal y vertical de los lados superiores e izquierdo.

El **ruta Mosaico rellena** página muestra el efecto de esta ruta de acceso. La ruta de acceso que se usa para colocar en mosaico del área se define como un campo en el [ `PathTileFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathTileFillPage.cs) clase. La coordenadas horizontales y verticales comprendidos entre -40 a 40, lo que significa que esta ruta de acceso es de 80 píxeles cuadrados:

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

En el `PaintSurface` controlador, el `SKPathEffect.Create2DPath` llamadas establece el espaciado horizontal y vertical en 64 para hacer que los iconos cuadrados 80 píxeles se superpongan. Afortunadamente, la ruta de acceso es similar a una pieza de puzzle, creación de mallas perfectamente con contigua iconos:

[![Captura de pantalla triple de la página de relleno del mosaico de trazado](effects-images/pathtilefill-small.png)](effects-images/pathtilefill-large.png#lightbox)

La capacidad de escalar de la captura de pantalla original hace algunos distorsión, especialmente en la pantalla de Android.

Tenga en cuenta que estos iconos siempre aparecen todos y nunca se truncan. En las primeras dos capturas de pantalla, no es incluso evidente que el área que se va a rellenar es un rectángulo redondeado. Si desea truncar estos iconos a un área determinada, utilice un trazado de recorte.

Pruebe a establecer el `Style` propiedad de la `SKPaint` objeto `Stroke`, y verá los iconos individuales que se describen en lugar de rellenar.

También es posible llenar un área con un mapa de bits en mosaico, tal como se muestra en el artículo [ **mosaico de mapa de bits de SkiaSharp**](../effects/shaders/bitmap-tiling.md).

## <a name="rounding-sharp-corners"></a>Redondear esquinas en ángulo

El **redondea Heptágono** programa se presenta en el [ **tres maneras de dibujar un arco** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) artículo utiliza un arco tangente para los puntos de una figura siete caras de la curva. El **Heptágono redondea otro** página muestra un enfoque mucho más sencillo que usa un efecto de la ruta de acceso creado a partir de la [ `SKPathEffect.CreateCorner` ](xref:SkiaSharp.SKPathEffect.CreateCorner(System.Single)) método:

```csharp
public static SKPathEffect CreateCorner (Single radius)
```

Aunque el único argumento se denomina `radius`, debe establecer a la mitad el radio de redondeo deseado. (Esto es una característica del código subyacente de Skia).

Este es el `PaintSurface` controlador en el [ `AnotherRoundedHeptagonPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AnotherRoundedHeptagonPage.cs) clase:

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

Puede utilizar este efecto con el trazado o relleno según el `Style` propiedad de la `SKPaint` objeto. Aquí se está ejecutando:

[![Captura de pantalla triple de la otra página Heptagon redondeada](effects-images/anotherroundedheptagon-small.png)](effects-images/anotherroundedheptagon-large.png#lightbox)

Verá que este Heptágono redondeado es idéntico al programa anterior. Si necesita más convincente que el radio de redondeo es realmente 100 en lugar de los 50 especificado en el `SKPathEffect.CreateCorner` llamada, puede quitar el comentario la instrucción final en el programa y ver un círculo de radio de 100 superpuesto a la esquina.

## <a name="random-jitter"></a>Vibración aleatoria

A veces, las líneas rectas perfecta de gráficos para PC no son bastante lo que desea, y se desea un poco aleatoriedad. En ese caso, conviene probar la [ `SKPathEffect.CreateDiscrete` ](xref:SkiaSharp.SKPathEffect.CreateDiscrete(System.Single,System.Single,System.UInt32)) método:

```csharp
public static SKPathEffect CreateDiscrete (Single segLength, Single deviation, UInt32 seedAssist)
```

Puede usar este efecto de la ruta de acceso para rellenar o trazado. Las líneas se dividen en segmentos conectados, especificada por la longitud de los cuales aproximada `segLength` — y ampliar en diferentes direcciones. Especifica la extensión de la desviación desde la línea original `deviation`.

El argumento final es un valor de inicialización usado para generar la secuencia pseudoaleatoria usa para el efecto. El efecto de vibración tendrá un aspecto un poco diferente para distintos valores de inicialización. El argumento tiene un valor predeterminado de cero, lo que significa que el efecto es el mismo cada vez que ejecute el programa. Si desea vibración diferente cada vez que se actualiza la pantalla, puede establecer el valor de inicialización la `Millisecond` propiedad de un `DataTime.Now` valor (por ejemplo).

El **vibración experimentar** página le permite experimentar con diferentes valores en un rectángulo de trayectoria:

[![Captura de pantalla triple de la página JitterExperiment](effects-images/jitterexperiment-small.png)](effects-images/jitterexperiment-large.png#lightbox)

El programa es sencillo. El [ **JitterExperimentPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml) archivo crean instancias de dos `Slider` elementos y un `SKCanvasView`:

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

El `PaintSurface` controlador en el [ **JitterExperimentPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml.cs) se denomina archivo de código subyacente siempre que sea un `Slider` cambia el valor. Llama a `SKPathEffect.CreateDiscrete` mediante los dos `Slider` valores y lo usa para trazar un rectángulo:

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

Puede usar este efecto para rellenar, en cuyo caso el contorno del área de relleno está sujeta a estas desviaciones aleatorias. El **texto vibración** página muestra cómo utilizar este efecto de la ruta de acceso para mostrar texto. La mayoría del código en el `PaintSurface` controlador de la [ `JitterTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterTextPage.cs) clase se dedica al calcular el tamaño y centrar el texto:

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

Aquí se está ejecutando en modo horizontal:

[![Captura de pantalla triple de la página JitterText](effects-images/jittertext-small.png)](effects-images/jittertext-large.png#lightbox)

## <a name="path-outlining"></a>Ruta de acceso de esquematización

Ya ha visto dos ejemplos pequeño de la [ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath*) método `SKPaint`, que existe en dos versiones:

```csharp
public Boolean GetFillPath (SKPath src, SKPath dst, Single resScale = 1)

public Boolean GetFillPath (SKPath src, SKPath dst, SKRect cullRect, Single resScale = 1)
```

Solo se necesitan los dos primeros argumentos. El método tiene acceso a la ruta de acceso al que hace referencia el `src` argumento, modifica los datos de ruta de acceso basados en las propiedades de trazo en el `SKPaint` objeto (incluido el `PathEffect` propiedad) y, a continuación, escribe los resultados en el `dst` ruta de acceso. El `resScale` parámetro permite reducir la precisión para crear una ruta de acceso de destino más pequeño y el `cullRect` argumento puede eliminar perfiles fuera de un rectángulo.

Un uso básico de este método no implica efectos de ruta de acceso: Si el `SKPaint` objeto tiene su `Style` propiedad establecida en `SKPaintStyle.Stroke` `PathEffect` ,`GetFillPath` y no *tiene su* conjunto, crea una ruta de acceso que representa un *contorno* de la ruta de acceso de origen como si hubiera sido trazada por el propiedades de Paint.

Por ejemplo, si la `src` ruta de acceso es un círculo simple del radio de 500 y el `SKPaint` objeto especifica un ancho de trazo de 100, el `dst` ruta de acceso se convierte en dos círculos concéntricos, uno con un radio de 450 y otro con un radio de 550. Se llama al método `GetFillPath` porque rellena esta información `dst` ruta de acceso es el mismo que el trazado el `src` ruta de acceso. Pero también puede trazar la `dst` ruta de acceso para ver los contornos de ruta de acceso.

El **pulse al contorno de la ruta de acceso** se muestra cómo hacerlo. El `SKCanvasView` y `TapGestureRecognizer` instancias se crean en el [ **TapToOutlineThePathPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml) archivo. El [ **TapToOutlineThePathPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml.cs) archivo de código subyacente define tres `SKPaint` objetos como campos, dos para el trazado con anchos de 100 y 20 y el tercero para el llenado de trazos:

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

Si no se ha punteado con la pantalla, el `PaintSurface` controlador utiliza la `blueFill` y `redThickStroke` pintar objetos para representar una trayectoria circular:

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

El círculo se llena y se traza como cabría esperar:

[![Captura de pantalla triple del punteo normal para describir la página de trazado](effects-images/taptooutlinethepathnormal-small.png)](effects-images/taptooutlinethepathnormal-large.png#lightbox)

Al puntear en la pantalla, `outlineThePath` está establecido en `true`y el `PaintSurface` controlador crea una nueva `SKPath` objeto y lo usa como la ruta de acceso de destino en una llamada a `GetFillPath` en el `redThickStroke` objeto paint. A continuación, se llena y se traza con esa ruta de acceso de destino `redThinStroke`, lo que en la siguiente:

[![Captura de pantalla triple de la pulsación esquematizada para describir la página de trazado](effects-images/taptooutlinethepathoutlined-small.png)](effects-images/taptooutlinethepathoutlined-large.png#lightbox)

Los dos círculos rojos indican claramente que la ruta de acceso circular original se ha convertido en dos perfiles circulares.

Este método puede ser muy útil en el desarrollo de las rutas de acceso que se utilizará para el `SKPathEffect.Create1DPath` método. Las rutas de acceso que especifique en estos métodos siempre se rellenan cuando se replican las rutas de acceso. Si no desea que la ruta de acceso completa que se debe rellenar, debe definir cuidadosamente los contornos.

Por ejemplo, en el **cadena vinculada** ejemplo, los vínculos se definieron con una serie de cuatro arcos, cada par de los cuales se basaban en dos radios describir el área de la ruta de acceso que se debe rellenar. Es posible reemplazar el código en el `LinkedChainPage` clase hacerlo un poco diferente.

En primer lugar, querrá volver a definir el `linkRadius` constante:

```csharp
const float linkRadius = 27.5f;
const float linkThickness = 5;
```

El `linkPath` es ahora tan sólo dos arcos basándose en ese único radius, con el inicio ángulos y ángulos de barrido:

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

El `outlinePath` objeto es, a continuación, el destinatario del contorno de `linkPath` cuando se traza con las propiedades especificadas en `strokePaint`.

Otro ejemplo de cómo utilizar esta técnica viene a continuación para la ruta de acceso utilizado en un método.

## <a name="combining-path-effects"></a>Efectos de la ruta de acceso de combinación

Los dos métodos de creación estático final de `SKPathEffect` son [ `SKPathEffect.CreateSum` ](xref:SkiaSharp.SKPathEffect.CreateSum(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)) y [ `SKPathEffect.CreateCompose` ](xref:SkiaSharp.SKPathEffect.CreateCompose(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)):

```csharp
public static SKPathEffect CreateSum (SKPathEffect first, SKPathEffect second)

public static SKPathEffect CreateCompose (SKPathEffect outer, SKPathEffect inner)
```

Ambos métodos de combinan dos efectos de la ruta de acceso para crear un efecto de la ruta de acceso compuesta. El `CreateSum` método crea un efecto de la ruta de acceso que es similar a los efectos de dos de la ruta de acceso que se aplica por separado, aunque `CreateCompose` se aplica un efecto de la ruta de acceso (el `inner`) y, a continuación, se aplica el `outer` a la.

Ya hemos visto cómo la `GetFillPath` método de `SKPaint` puede convertir una ruta de acceso a otra ruta de acceso basado en `SKPaint` propiedades (incluyendo `PathEffect`) por lo que no debería ser *demasiado* misterioso cómo un `SKPaint`objeto puede realizar la operación dos veces con los efectos de dos de la ruta de acceso especificados en el `CreateSum` o `CreateCompose` métodos.

Un uso obvio de `CreateSum` consiste en definir un `SKPaint` objeto que rellena una ruta de acceso con efecto de una ruta de acceso y la ruta de acceso con otro efecto de la ruta de acceso de trazos. Esto se muestra en el **gatos en marco** ejemplo, que muestra una matriz de gatos dentro de un marco con bordes festoneados:

[![Captura de pantalla triple de la página de gatos en el marco](effects-images/catsinframe-small.png)](effects-images/catsinframe-large.png#lightbox)

El [ `CatsInFramePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CatsInFramePage.cs) clase comienza definiendo varios campos. Es posible que reconozca el primer campo desde el [ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) clase desde el [ **datos de ruta de acceso de SVG** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md) artículo. La segunda ruta de acceso se basa en una línea y un arco para el patrón Festonear del marco:

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

El `catPath` podría usarse en el `SKPathEffect.Create2DPath` método si la `SKPaint` objeto `Style` propiedad está establecida en `Stroke`. Sin embargo, si la `catPath` sirve directamente en este programa, a continuación, toda la cabeza del gato se completará y las patillas de un incluso no serán visibles. (¡Pruébelo!) Es necesario obtener el contorno de esa ruta de acceso y utilizar ese esquema en el `SKPathEffect.Create2DPath` método.

El constructor realiza este trabajo. Aplican primero dos transformaciones a `catPath` para mover el (0, 0) apunte al centro y reducir verticalmente de tamaño. `GetFillPath` Obtiene todos los contornos de los contornos de `outlinedCatPath`, y ese objeto se usa en el `SKPathEffect.Create2DPath` llamar. El ajuste de escala tenga en cuenta la `SKMatrix` valor son ligeramente mayor que la horizontal y el tamaño vertical de cat para proporcionar un búfer pequeño entre los iconos, mientras que eran los factores de traslación derivadas ligeramente empíricamente para que un gato completo está visible en el esquina superior izquierda del marco:

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

A continuación, llama al constructor `SKPathEffect.Create1DPath` para el marco festoneado. Tenga en cuenta que el ancho de la ruta de acceso es 100 píxeles, pero el avance es 75 píxeles para que la ruta de acceso duplicada se superpone en torno al marco. La instrucción final de las llamadas de constructor `SKPathEffect.CreateSum` para combinar los efectos de la ruta de acceso de dos y establecer el resultado en la `SKPaint` objeto.

Todo este trabajo permite la `PaintSurface` controlador sea bastante sencillo. Solo es necesario definir un rectángulo y dibújelo mediante `framePaint`:

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

Los algoritmos detrás de los efectos de la ruta de acceso siempre causan la ruta de acceso completa utilizada para que rellenar o trazado para mostrarse, lo que puede provocar que algunos objetos visuales que aparezca fuera del rectángulo. El `ClipRect` llamada anterior a la `DrawRect` llamada permite que los objetos visuales a resultar considerablemente más limpio. (Pruebe sin recorte).

Es habitual usar `SKPathEffect.CreateCompose` para agregar cierta vibración a otro efecto de la ruta de acceso. Sin duda puede experimentar en su propio, pero este es un ejemplo de un poco diferente:

El **sombreado discontinuas** rellena una elipse con líneas de trama que son discontinuas. La mayoría del trabajo de la [ `DashedHatchLinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DashedHatchLinesPage.cs) clase se realiza directamente en las definiciones de campo. Estos campos definen un efecto de dash y un efecto de sombreado. Se definen como `static` porque, a continuación, se hace referencia en un `SKPathEffect.CreateCompose` llamar el `SKPaint` definición:

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

El `PaintSurface` controlador debe contener únicamente la sobrecarga estándar además de una llamada a `DrawOval`:

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

Como ya han descubierto, no son precisamente restringidas al interior del área de las líneas de trama y en este ejemplo, siempre empiezan a la izquierda con un guión en todo:

[![Captura de pantalla triple de la página líneas de trama discontinua](effects-images/dashedhatchlines-small.png)](effects-images/dashedhatchlines-large.png#lightbox)

Ahora que ha visto los efectos de la ruta de acceso que van desde simples puntos y guiones para combinaciones extrañas, use su imaginación y vea lo que puede crear.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
