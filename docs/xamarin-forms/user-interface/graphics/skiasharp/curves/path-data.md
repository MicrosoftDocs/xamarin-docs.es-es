---
title: Datos de la ruta de acceso SVG en SkiaSharp
description: En este artículo se explica cómo definir rutas de acceso de SkiaSharp mediante cadenas de texto en el formato de gráficos vectoriales escalables y se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 1D53067B-3502-4D74-B89D-7EC496901AE2
author: davidbritch
ms.author: dabritch
ms.date: 05/24/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 12af5d9be025fb2113d70a93a364619aff75598d
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370070"
---
# <a name="svg-path-data-in-skiasharp"></a>Datos de la ruta de acceso SVG en SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Definir rutas de acceso mediante cadenas de texto en el formato de gráficos vectoriales escalables_

La [`SKPath`](xref:SkiaSharp.SKPath) clase admite la definición de objetos path completos a partir de cadenas de texto en un formato establecido por la especificación de gráficos de vectores escalables (SVG). Más adelante en este artículo veremos cómo puede representar una ruta de acceso completa, como esta, en una cadena de texto:

![Una ruta de acceso de ejemplo definida con datos de ruta de acceso de SVG](path-data-images/pathdatasample.png)

SVG es un lenguaje de programación de gráficos basado en XML para páginas Web. Dado que SVG debe permitir que las rutas de acceso se definan en el marcado en lugar de una serie de llamadas de función, el estándar SVG incluye una manera muy concisa de especificar una ruta de acceso de gráficos completa como una cadena de texto.

En SkiaSharp, este formato se conoce como "SVG path-Data". También se admite el formato en entornos de programación basados en XAML de Windows, incluidos los Windows Presentation Foundation y el Plataforma universal de Windows, donde se conoce como la [Sintaxis de marcado](/dotnet/framework/wpf/graphics-multimedia/path-markup-syntax) de la ruta de acceso o la sintaxis de los comandos de [movimiento y dibujo](/windows/uwp/xaml-platform/move-draw-commands-syntax/). También puede servir como formato de intercambio para imágenes de gráficos vectoriales, especialmente en archivos basados en texto como XML.

La [`SKPath`](xref:SkiaSharp.SKPath) clase define dos métodos con las palabras `SvgPathData` en sus nombres:

```csharp
public static SKPath ParseSvgPathData(string svgPath)

public string ToSvgPathData()
```

El [`ParseSvgPathData`](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String)) método estático convierte una cadena en un `SKPath` objeto, mientras [`ToSvgPathData`](xref:SkiaSharp.SKPath.ToSvgPathData) que convierte un `SKPath` objeto en una cadena.

Esta es una cadena SVG para una estrella de cinco puntas centrada en el punto (0,0) con un radio de 100:

```
"M 0 -100 L 58.8 90.9, -95.1 -30.9, 95.1 -30.9, -58.8 80.9 Z"
```

Las letras son comandos que compilan un `SKPath` objeto: `M` indica una `MoveTo` llamada, `L` es `LineTo` y va `Z` `Close` a cerrar un contorno. Cada par de números proporciona una coordenada X e y de un punto. Observe que el `L` comando va seguido de varios puntos separados por comas. En una serie de coordenadas y puntos, las comas y los espacios en blanco se tratan de forma idéntica. Algunos programadores prefieren colocar comas entre las coordenadas X e y, en lugar de entre los puntos, pero solo se necesitan comas o espacios para evitar la ambigüedad. Esto es absolutamente válido:

```
"M0-100L58.8 90.9-95.1-30.9 95.1-30.9-58.8 80.9Z"
```

La sintaxis de los datos de la ruta de acceso de SVG se documenta formalmente en [la sección 8,3 de la especificación SVG](https://www.w3.org/TR/SVG11/paths.html#PathData). A continuación se muestra un resumen:

## <a name="moveto"></a>**MoveTo**

```
M x y
```

Esto inicia un nuevo perfil en la ruta de acceso estableciendo la posición actual. Los datos de la ruta de acceso siempre deben empezar con un `M` comando.

## <a name="lineto"></a>**LineTo**

```
L x y ...
```

Este comando agrega una línea recta (o líneas) al trazado y establece la nueva posición actual en el final de la última línea. Puede seguir el `L` comando con varios pares de coordenadas *x* e *y* .

## <a name="horizontal-lineto"></a>**Línea horizontal**

```
H x ...
```

Este comando agrega una línea horizontal a la ruta de acceso y establece la nueva posición actual en el final de la línea. Puede seguir el `H` comando con varias coordenadas *x* , pero no tiene mucho sentido.

## <a name="vertical-line"></a>**Línea vertical**

```
V y ...
```

Este comando agrega una línea vertical a la ruta de acceso y establece la nueva posición actual en el final de la línea.

## <a name="close"></a>**Cerrar**

```
Z
```

El `C` comando cierra el contorno agregando una línea recta desde la posición actual hasta el principio del contorno.

## <a name="arcto"></a>**ArcTo**

El comando para agregar un arco elíptico al contorno es, con diferencia, el comando más complejo en toda la especificación de datos de ruta de acceso de SVG. Es el único comando en el que los números pueden representar un valor distinto de los valores de las coordenadas:

```
A rx ry rotation-angle large-arc-flag sweep-flag x y ...
```

Los parámetros *RX* y *Ry* son los radios horizontal y vertical de la elipse. El *ángulo de rotación* es en grados en el sentido de las agujas del reloj.

Establezca la *marca de gran arco* en 1 para el arco grande o en 0 para el arco pequeño.

Establezca la *marca de barrido* en 1 para el sentido de las agujas del reloj y en 0 para el sentido contrario a las agujas del reloj.

El arco se dibuja en el punto ( *x* , *y* ), que se convierte en la nueva posición actual.

## <a name="cubicto"></a>**Cúbico**

```
C x1 y1 x2 y2 x3 y3 ...
```

Este comando agrega una curva Bézier cúbica desde la posición actual a ( *x3* , *Y3* ), que se convierte en la nueva posición actual. Los puntos ( *x1* , *Y1* ) y ( *x2* , *Y2* ) son puntos de control.

Se pueden especificar varias curvas Bezier mediante un solo `C` comando. El número de puntos debe ser un múltiplo de 3.

También hay un comando "Smooth" de curva de Bézier:

```
S x2 y2 x3 y3 ...
```

Este comando debe seguir un comando de Bézier normal (aunque no es estrictamente necesario). El comando Smooth Bézier calcula el primer punto de control para que sea un reflejo del segundo punto de control de la curva Bézier anterior en torno a su punto mutuo. Por tanto, estos tres puntos son colineales y la conexión entre las dos curvas de Bézier es suave.

## <a name="quadto"></a>**Cuádrupleto**

```
Q x1 y1 x2 y2 ...
```

En el caso de curvas Bézier cuadráticas, el número de puntos debe ser un múltiplo de 2. El punto de control es ( *x1* , *Y1* ) y el punto final (y la nueva posición actual) es ( *x2* , *Y2* )

También hay un comando de curva cuadrática suave:

```
T x2 y2 ...
```

El punto de control se calcula en función del punto de control de la curva cuadrática anterior.

Todos estos comandos también están disponibles en las versiones "relativas", donde los puntos de coordenadas son relativos a la posición actual. Estos comandos relativos comienzan con letras minúsculas, por ejemplo, `c` en lugar de `C` para la versión relativa del comando Bézier cúbica.

Esta es la extensión de la definición de datos path SVG. No hay ninguna utilidad para repetir grupos de comandos o para realizar cualquier tipo de cálculo. Los comandos para `ConicTo` u otros tipos de especificaciones de Arc no están disponibles.

El [`SKPath.ParseSvgPathData`](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String)) método estático espera una cadena válida de comandos SVG. Si se detecta algún error de sintaxis, el método devuelve `null` . Es la única indicación de error.

El [`ToSvgPathData`](xref:SkiaSharp.SKPath.ToSvgPathData) método es útil para obtener los datos de la ruta de acceso de SVG de un `SKPath` objeto existente y transferirlos a otro programa, o para almacenar en un formato de archivo basado en texto como XML. (El `ToSvgPathData` método no se muestra en el código de ejemplo de este artículo). *No* se espera `ToSvgPathData` que devuelva una cadena correspondiente exactamente a las llamadas de método que crearon la ruta de acceso. En concreto, descubrirá que los arcos se convierten en varios `QuadTo` comandos y eso es como aparecen en los datos de ruta de acceso devueltos desde `ToSvgPathData` .

La página de datos de la **ruta de acceso** escribe la palabra "Hello" con los datos de la ruta de acceso SVG. Los `SKPath` objetos y `SKPaint` se definen como campos en la [`PathDataHelloPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataHelloPage.cs) clase:

```csharp
public class PathDataHelloPage : ContentPage
{
    SKPath helloPath = SKPath.ParseSvgPathData(
        "M 0 0 L 0 100 M 0 50 L 50 50 M 50 0 L 50 100" +                // H
        "M 125 0 C 60 -10, 60 60, 125 50, 60 40, 60 110, 125 100" +     // E
        "M 150 0 L 150 100, 200 100" +                                  // L
        "M 225 0 L 225 100, 275 100" +                                  // L
        "M 300 50 A 25 50 0 1 0 300 49.9 Z");                           // O

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };
    ...
}
```

La ruta de acceso que define la cadena de texto comienza en la esquina superior izquierda en el punto (0,0). Cada letra tiene 50 unidades de ancho y 100 de alto, y las letras están separadas por otras 25 unidades, lo que significa que todo el trazado tiene una longitud de 350 unidades.

' H ' de "Hello" se compone de contornos de 3 1 de línea, mientras que ' E ' es dos curvas de Bézier cúbicas conectadas. Observe que el `C` comando va seguido de seis puntos y dos de los puntos de control tienen las coordenadas y de – 10 y 110, que los coloca fuera del intervalo de las coordenadas y de las otras letras. ' L ' es dos líneas conectadas, mientras que ' O ' es una elipse que se representa con un `A` comando.

Observe que el `M` comando que comienza el último contorno establece la posición en el punto (350, 50), que es el centro vertical del lado izquierdo de la "O". Como se indica en los primeros números que siguen al `A` comando, la elipse tiene un radio horizontal de 25 y un radio vertical de 50. El punto final se indica mediante el último par de números del `A` comando, que representa el punto (300, 49,9). Esto es deliberadamente ligeramente diferente del punto inicial. Si el extremo se establece igual que el punto inicial, el arco no se representará. Para dibujar una elipse completa, debe establecer el extremo cerca de (pero no igual a) el punto inicial, o bien debe utilizar dos o más `A` comandos, cada uno para parte de la elipse completa.

Es posible que desee agregar la siguiente instrucción al constructor de la página y, a continuación, establecer un punto de interrupción para examinar la cadena resultante:

```csharp
string str = helloPath.ToSvgPathData();
```

Descubrirá que el arco se ha reemplazado por una larga serie de `Q` comandos para obtener una aproximación por etapas del arco con curvas Bézier cuadráticas.

El `PaintSurface` controlador obtiene los límites estrechos de la ruta de acceso, que no incluye los puntos de control para las curvas ' e ' y ' O '. Las tres transformaciones mueven el centro del trazado hasta el punto (0,0), escalan la ruta de acceso al tamaño del lienzo (pero también toman en cuenta el ancho del trazo) y, a continuación, mueven el centro del trazado al centro del lienzo:

```csharp
public class PathDataHelloPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect bounds;
        helloPath.GetTightBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);

        canvas.Scale(info.Width / (bounds.Width + paint.StrokeWidth),
                     info.Height / (bounds.Height + paint.StrokeWidth));

        canvas.Translate(-bounds.MidX, -bounds.MidY);

        canvas.DrawPath(helloPath, paint);
    }
}
```

La ruta de acceso rellena el lienzo, que es más razonable cuando se ve en modo horizontal:

[![Captura de pantalla triple de la página de datos de la ruta de acceso](path-data-images/pathdatahello-small.png)](path-data-images/pathdatahello-large.png#lightbox "Captura de pantalla triple de la página de datos de la ruta de acceso")

La página **path Data CAT** es similar. Los objetos path y Paint se definen como campos en la [`PathDataCatPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) clase:

```csharp
public class PathDataCatPage : ContentPage
{
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

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 5
    };
    ...
}
```

El encabezado de un gato es un círculo y, aquí, se representa con dos `A` comandos, cada uno de los cuales dibuja un semicírculo. Ambos `A` comandos para el encabezado definen los radios horizontal y vertical de 100. El primer arco comienza en (240, 100) y termina en (240, 300), que se convierte en el punto inicial del segundo arco que termina en (240, 100).

Los dos ojos también se representan con dos `A` comandos y, al igual que con el encabezado del gato, el segundo `A` comando finaliza en el mismo punto que el inicio del primer `A` comando. Sin embargo, estos pares de `A` comandos no definen una elipse. El con de cada arco es de 40 unidades y el radio también es de 40 unidades, lo que significa que estos arcos no son semicírculos completos.

El `PaintSurface` controlador realiza transformaciones similares como el ejemplo anterior, pero establece un solo `Scale` factor para mantener la relación de aspecto y proporcionar un pequeño margen para que los bigotes del gato no toquen los lados de la pantalla:

```csharp
public class PathDataCatPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        SKRect bounds;
        catPath.GetBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);

        canvas.Scale(0.9f * Math.Min(info.Width / bounds.Width,
                                     info.Height / bounds.Height));

        canvas.Translate(-bounds.MidX, -bounds.MidY);

        canvas.DrawPath(catPath, paint);
    }
}
```

Esta es la ejecución del programa:

[![Captura de pantalla triple de la página path Data CAT](path-data-images/pathdatacat-small.png)](path-data-images/pathdatacat-large.png#lightbox "Captura de pantalla triple de la página path Data CAT")

Normalmente, cuando un `SKPath` objeto se define como un campo, los contornos de la ruta de acceso se deben definir en el constructor u otro método. Sin embargo, al usar los datos de la ruta de acceso de SVG, habrá visto que la ruta de acceso se puede especificar completamente en la definición de campo.

En el ejemplo de **reloj analógico anterior feo** del artículo [**girar transformación**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md) se muestran las manos del reloj como líneas simples. El siguiente programa de **reloj analógico** reemplaza las líneas por los `SKPath` objetos definidos como campos en la [`PrettyAnalogClockPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PrettyAnalogClockPage.cs) clase junto con los `SKPaint` objetos:

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    // Clock hands pointing straight up
    SKPath hourHandPath = SKPath.ParseSvgPathData(
        "M 0 -60 C   0 -30 20 -30  5 -20 L  5   0" +
                "C   5 7.5 -5 7.5 -5   0 L -5 -20" +
                "C -20 -30  0 -30  0 -60 Z");

    SKPath minuteHandPath = SKPath.ParseSvgPathData(
        "M 0 -80 C   0 -75  0 -70  2.5 -60 L  2.5   0" +
                "C   2.5 5 -2.5 5 -2.5   0 L -2.5 -60" +
                "C 0 -70  0 -75  0 -80 Z");

    SKPath secondHandPath = SKPath.ParseSvgPathData(
        "M 0 10 L 0 -80");

    // SKPaint objects
    SKPaint handStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2,
        StrokeCap = SKStrokeCap.Round
    };

    SKPaint handFillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Gray
    };
    ...
}
```

Ahora, las manecillas de hora y minuto tienen áreas. Para que esas manos sean distintas entre sí, se dibujan con un contorno negro y un relleno gris usando `handStrokePaint` los `handFillPaint` objetos y.

En el ejemplo de **reloj analógico anterior feo** , los pequeños círculos que marcaron las horas y los minutos se dibujaban en un bucle. En este ejemplo de **reloj análogo** , se usa un enfoque totalmente diferente: las marcas de hora y minuto son líneas de puntos dibujadas con los `minuteMarkPaint` `hourMarkPaint` objetos y:

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    SKPaint minuteMarkPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3,
        StrokeCap = SKStrokeCap.Round,
        PathEffect = SKPathEffect.CreateDash(new float[] { 0, 3 * 3.14159f }, 0)
    };

    SKPaint hourMarkPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 6,
        StrokeCap = SKStrokeCap.Round,
        PathEffect = SKPathEffect.CreateDash(new float[] { 0, 15 * 3.14159f }, 0)
    };
    ...
}
```

En el artículo de [**puntos y guiones**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md) se explica cómo puede usar el [`SKPathEffect.CreateDash`](xref:SkiaSharp.SKPathEffect.CreateDash*) método para crear una línea discontinua. El primer argumento es una `float` matriz que generalmente tiene dos elementos: el primer elemento es la longitud de los guiones y el segundo elemento es el espacio entre los guiones. Cuando la `StrokeCap` propiedad está establecida en `SKStrokeCap.Round` , los extremos redondeados del guión alargan eficazmente la longitud de los guiones en el ancho del trazo de ambos lados del guión. Por lo tanto, si se establece el primer elemento de matriz en 0, se crea una línea de puntos.

La distancia entre estos puntos se rige por el segundo elemento de la matriz. Como verá en breve, estos dos `SKPaint` objetos se usan para dibujar círculos con un radio de 90 unidades. Por lo tanto, la circunferencia de este círculo es 180 π, lo que significa que las marcas de 60 por minuto deben aparecer cada unidades de 3π, que es el segundo valor de la `float` matriz en `minuteMarkPaint` . Las marcas de 12 horas deben aparecer cada unidad de 15π, que es el valor de la segunda `float` matriz.

La `PrettyAnalogClockPage` clase establece un temporizador para invalidar la superficie cada 16 milisegundos y `PaintSurface` se llama al controlador a esa velocidad. Las definiciones anteriores de los `SKPath` `SKPaint` objetos y permiten un código de dibujo muy limpio:

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Transform for 100-radius circle in center
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(Math.Min(info.Width / 200, info.Height / 200));

        // Draw circles for hour and minute marks
        SKRect rect = new SKRect(-90, -90, 90, 90);
        canvas.DrawOval(rect, minuteMarkPaint);
        canvas.DrawOval(rect, hourMarkPaint);

        // Get time
        DateTime dateTime = DateTime.Now;

        // Draw hour hand
        canvas.Save();
        canvas.RotateDegrees(30 * dateTime.Hour + dateTime.Minute / 2f);
        canvas.DrawPath(hourHandPath, handStrokePaint);
        canvas.DrawPath(hourHandPath, handFillPaint);
        canvas.Restore();

        // Draw minute hand
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Minute + dateTime.Second / 10f);
        canvas.DrawPath(minuteHandPath, handStrokePaint);
        canvas.DrawPath(minuteHandPath, handFillPaint);
        canvas.Restore();

        // Draw second hand
        double t = dateTime.Millisecond / 1000.0;

        if (t < 0.5)
        {
            t = 0.5 * Easing.SpringIn.Ease(t / 0.5);
        }
        else
        {
            t = 0.5 * (1 + Easing.SpringOut.Ease((t - 0.5) / 0.5));
        }

        canvas.Save();
        canvas.RotateDegrees(6 * (dateTime.Second + (float)t));
        canvas.DrawPath(secondHandPath, handStrokePaint);
        canvas.Restore();
    }
}
```

Sin embargo, se hace algo especial con la segunda mano. Dado que el reloj se actualiza cada 16 milisegundos, la `Millisecond` propiedad del `DateTime` valor puede usarse potencialmente para animar una segunda mano de barrido en lugar de una que se mueve en saltos discretos de segundo a segundo. Pero este código no permite que el movimiento sea suave. En su lugar, usa las Xamarin.Forms [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) funciones de aceleración de animación y para un tipo diferente de movimiento. Estas funciones de entradas y salidas lentas hacen que la segunda mano se mueva en una jerkier, &mdash; sacando un poco antes de que se mueva y, después, se desplace ligeramente por encima de su destino, un efecto que desafortunadamente no se puede reproducir en estas capturas de pantallas estáticas:

[![Captura de pantalla triple de la página del reloj análogo](path-data-images/prettyanalogclock-small.png)](path-data-images/prettyanalogclock-large.png#lightbox "Captura de pantalla triple de la página del reloj análogo")

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)