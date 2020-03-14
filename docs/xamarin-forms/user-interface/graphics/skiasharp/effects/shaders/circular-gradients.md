---
title: Los degradados circulares de SkiaSharp
description: Obtenga información sobre los diferentes tipos de degradados en función de círculos.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 400AE23A-6A0B-4FA8-BD6B-DE4146B04732
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: d56cc499112a937cd1a22664adeedd54c4397341
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305748"
---
# <a name="the-skiasharp-circular-gradients"></a>Los degradados circulares de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

La clase [`SKShader`](xref:SkiaSharp.SKShader) define métodos estáticos para crear cuatro tipos diferentes de degradados. En el artículo [**SkiaSharp linear gradiente**](linear-gradient.md) se describe el método de [`CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient*) . En este artículo se trata los tres tipos de degradados, todos ellos se basan en círculos.

El método [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient*) crea un degradado que emana del centro de un círculo:

![Ejemplo de degradado radial](circular-gradients-images/RadialGradientSample.png)

El método [`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient*) crea un degradado que se barre alrededor del centro de un círculo:

![Ejemplo de degradado de barrido](circular-gradients-images/SweepGradientSample.png)

El tercer tipo de degradado es bastante raro. Se denomina degradado cónico de dos puntos y se define mediante el método [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient*) . El degradado se extiende desde un círculo a otro:

![Ejemplo de degradado cónico](circular-gradients-images/ConicalGradientSample.png)

Si los dos círculos tienen tamaños diferentes, a continuación, el degradado adopta la forma de cono.

Este artículo exploran estos degradados con más detalle.

## <a name="the-radial-gradient"></a>Degradado radial

El método [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) tiene la siguiente sintaxis:

```csharp
public static SKShader CreateRadialGradient (SKPoint center, 
                                             Single radius, 
                                             SKColor[] colors, 
                                             Single[] colorPos, 
                                             SKShaderTileMode mode)
```

Una sobrecarga de [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) también incluye un parámetro de matriz de transformación.

Los primeros dos argumentos especifican el centro de un círculo y un radio. El degradado comienza en ese centro y se extiende hacia afuera para `radius` píxeles. Lo que ocurre más allá `radius` depende del argumento [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) . El parámetro `colors` es una matriz de dos o más colores (al igual que en los métodos de degradado lineal) y `colorPos` es una matriz de enteros en el intervalo de 0 a 1. Estos enteros indican las posiciones relativas de los colores a lo largo de la línea `radius`. Puede establecer ese argumento en `null` para espaciar equitativamente los colores.

Si usa `CreateRadialGradient` para rellenar un círculo, puede establecer el centro del degradado en el centro del círculo y el radio del degradado en el radio del círculo. En ese caso, el argumento `SKShaderTileMode` no tiene ningún efecto en la representación del degradado. Pero si el área rellenada por el degradado es mayor que el círculo definido por el degradado, el argumento `SKShaderTileMode` tiene un efecto profundo sobre lo que ocurre fuera del círculo.

El efecto de `SKShaderMode` se muestra en la página **gradiente radial** en el ejemplo [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . El archivo XAML de esta página crea una instancia de un `Picker` que le permite seleccionar uno de los tres miembros de la enumeración `SKShaderTileMode`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.RadialGradientPage"
             Title="Radial Gradient">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                Grid.Row="0"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="tileModePicker" 
                Grid.Row="1"
                Title="Shader Tile Mode" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKShaderTileMode}">
                    <x:Static Member="skia:SKShaderTileMode.Clamp" />
                    <x:Static Member="skia:SKShaderTileMode.Repeat" />
                    <x:Static Member="skia:SKShaderTileMode.Mirror" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</ContentPage>
```

El archivo de código subyacente colorea todo el lienzo con un degradado radial. El centro del degradado está establecido en el centro del lienzo y el radio está establecido en 100 píxeles. El degradado está formado sólo dos colores, blanco y negro:

```csharp
public partial class RadialGradientPage : ContentPage
{
    public RadialGradientPage ()
    {
        InitializeComponent ();
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

        SKShaderTileMode tileMode =
            (SKShaderTileMode)(tileModePicker.SelectedIndex == -1 ?
                                        0 : tileModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                new SKPoint(info.Rect.MidX, info.Rect.MidY),
                                100,
                                new SKColor[] { SKColors.Black, SKColors.White },
                                null,
                                tileMode);

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Este código crea un degradado con negro en el centro de difuminación gradualmente en blanco 100 píxeles desde el centro. Lo que sucede más allá de ese radio depende del argumento `SKShaderTileMode`:

[![Degradado radial](circular-gradients-images/RadialGradient.png "Degradado radial")](circular-gradients-images/RadialGradient-Large.png#lightbox)

En los tres casos, el degradado rellena el lienzo. En la pantalla de iOS a la izquierda, el degradado fuera del radio continúa con el último color, que es el blanco. Este es el resultado de `SKShaderTileMode.Clamp`. La pantalla de Android muestra el efecto de `SKShaderTileMode.Repeat`: en 100 píxeles desde el centro, el degradado comienza de nuevo con el primer color, que es negro. El degradado repite cada 100 píxeles de radius. 

En la pantalla de Plataforma universal de Windows de la derecha se muestra cómo `SKShaderTileMode.Mirror` hace que los degradados alternen las direcciones. El primer degradado es del negro en el centro en blanco en un radio de 100 píxeles. La siguiente es blanca desde el radio de 100 píxeles en negro en un radio de 200 píxeles y se invierte el degradado siguiente de nuevo.

Puede usar más de dos colores en un degradado radial. El ejemplo de **degradado de arco de arco iris** crea una matriz de ocho colores que corresponden a los colores del arco iris y termina con rojo, además de una matriz de ocho valores de posición:

```csharp
public class RainbowArcGradientPage : ContentPage
{
    public RainbowArcGradientPage ()
    {
        Title = "Rainbow Arc Gradient";

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
            float rainbowWidth = Math.Min(info.Width, info.Height) / 4f;

            // Center of arc and gradient is lower-right corner
            SKPoint center = new SKPoint(info.Width, info.Height);

            // Find outer, inner, and middle radius
            float outerRadius = Math.Min(info.Width, info.Height);
            float innerRadius = outerRadius - rainbowWidth;
            float radius = outerRadius - rainbowWidth / 2;

            // Calculate the colors and positions
            SKColor[] colors = new SKColor[8];
            float[] positions = new float[8];

            for (int i = 0; i < colors.Length; i++)
            {
                colors[i] = SKColor.FromHsl(i * 360f / 7, 100, 50);
                positions[i] = (i + (7f - i) * innerRadius / outerRadius) / 7f;
            }

            // Create sweep gradient based on center and outer radius
            paint.Shader = SKShader.CreateRadialGradient(center, 
                                                         outerRadius, 
                                                         colors, 
                                                         positions, 
                                                         SKShaderTileMode.Clamp);
            // Draw a circle with a wide line
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = rainbowWidth;

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

Suponga que el mínimo del ancho y el alto del lienzo es 1000, lo que significa que el valor `rainbowWidth` es 250. Los valores `outerRadius` y `innerRadius` se establecen en 1000 y 750, respectivamente. Estos valores se utilizan para calcular la matriz de `positions`; los ocho valores van de 0,75 f a 1. El valor `radius` se usa para retornear el círculo. El valor de 875 significa que el ancho del trazo de 250 píxeles se extiende entre el radio de 750 píxeles y el radio de 1000 píxeles:

[![Degradado de arco de arco iris](circular-gradients-images/RainbowArcGradient.png "Degradado de arco de arco iris")](circular-gradients-images/RainbowArcGradient-Large.png#lightbox)

Si rellena el lienzo completo con este degradado, vería que es rojo dentro del radio interno. Esto se debe a que la matriz de `positions` no empieza por 0. El primer color se utiliza para los desplazamientos de 0 y el primer valor de matriz. También es rojo más allá de la radio exterior del degradado. Este es el resultado de la `Clamp` modo de mosaico. Dado que el degradado se utiliza para trazado una línea gruesa, estas áreas de color rojo no son visibles.

## <a name="radial-gradients-for-masking"></a>Degradados radiales para el enmascaramiento

Como degradados lineales, degradados radiales pueden incorporar los colores o parcialmente transparentes. Esta característica es útil para un proceso denominado _enmascaramiento_, que oculta parte de una imagen para acentuar otra parte de la imagen.

La página de la **máscara de degradado radial** muestra un ejemplo. El programa carga uno de los mapas de bits del recurso. Los campos `CENTER` y `RADIUS` se han determinado a partir de un examen del mapa de bits y hacen referencia a un área que se debe resaltar. El controlador de `PaintSurface` comienza calculando un rectángulo para mostrar el mapa de bits y, a continuación, lo muestra en ese rectángulo:

```csharp
public class RadialGradientMaskPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(RadialGradientMaskPage),
        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    static readonly SKPoint CENTER = new SKPoint(180, 300);
    static readonly float RADIUS = 120;

    public RadialGradientMaskPage ()
    {
        Title = "Radial Gradient Mask";

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
                                new SKColor[] { SKColors.Transparent,
                                                SKColors.White },
                                new float[] { 0.6f, 1 },
                                SKShaderTileMode.Clamp);

            // Display rectangle using that gradient
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Después de dibujar el mapa de bits, algunos códigos simples convierten `CENTER` y `RADIUS` en `center` y `radius`, que hacen referencia al área resaltada en el mapa de bits que se ha escalado y desplazado para su presentación. Estos valores se usan para crear un degradado radial con ese centro y el radio. Los dos colores comienzan en transparente en el centro y el primer 60% del radio. El degradado, a continuación, se difumina a blanco:

[![Máscara de degradado radial](circular-gradients-images/RadialGradientMask.png "Máscara de degradado radial")](circular-gradients-images/RadialGradientMask-Large.png#lightbox)

Este enfoque no es la mejor forma de un mapa de bits de máscara. El problema es que la propiedad mask tiene principalmente un color blanco, que se ha elegido para que coincida con el fondo del lienzo. Si el fondo es algún otro &mdash; de color o quizás un propio degradado &mdash; no coincidirá. En el artículo [SkiaSharp Porter-Duff Blend modes,](../blend-modes/porter-duff.md)se muestra un enfoque mejor para el enmascaramiento.

## <a name="radial-gradients-for-specular-highlights"></a>Degradados radiales para resaltes especulares

Cuando una luz produzca una superficie redondeada, refleja la luz en muchas direcciones, pero algunas de la luz rebota directamente en la vista del Visor. Esto a menudo crea la apariencia de una zona blanca aproximada en la superficie denominada _resaltado especular_.

En los gráficos tridimensionales, resaltes especulares a menudo el resultado de los algoritmos utilizados para determinar las rutas de acceso claros y sombreado. En los gráficos bidimensionales, resaltes especulares a veces se agregan para sugerir la apariencia de una superficie 3D. Un resaltado especular puede transformar un círculo rojo plana en una bola roja.

La página **resaltado especular radial** usa un degradado radial para hacerlo exactamente. El controlador de `PaintSurface` es calcular un radio para el círculo y dos valores de `SKPoint` &mdash; un `center` y un `offCenter` que se encuentra a la mitad del centro y del borde superior izquierdo del círculo:

```csharp
public class RadialSpecularHighlightPage : ContentPage
{
    public RadialSpecularHighlightPage()
    {
        Title = "Radial Specular Highlight";

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

        float radius = 0.4f * Math.Min(info.Width, info.Height);
        SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
        SKPoint offCenter = center - new SKPoint(radius / 2, radius / 2);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                offCenter,
                                radius / 2,
                                new SKColor[] { SKColors.White, SKColors.Red },
                                null,
                                SKShaderTileMode.Clamp);

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

La llamada a `CreateRadialGradient` crea un degradado que comienza en ese `offCenter` punto con blanco y termina con rojo a una distancia de la mitad del radio. Este es su aspecto:

[![Resaltado especular radial](circular-gradients-images/RadialSpecularHighlight.png "Resaltado especular radial")](circular-gradients-images/RadialSpecularHighlight-Large.png#lightbox)

Si observa detenidamente esta degradado, podría decidir que lo contiene errores. El degradado se centra en torno a un momento determinado, y es posible que desee fuese un poco menos simétricos para reflejar la superficie redondeada. En ese caso, podría preferir el resaltado especular que se muestra a continuación en la sección [**gradientes cónicos para reflejos especulares**](#conical-gradients-for-specular-highlights).

## <a name="the-sweep-gradient"></a>El degradado de barrido

El método [`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[])) tiene la sintaxis más sencilla de todos los métodos de creación de degradado:

```csharp
public static SKShader CreateSweepGradient (SKPoint center, 
                                            SKColor[] colors, 
                                            Single[] colorPos)
```

Es simplemente un centro de una matriz de colores y las posiciones de color. El degradado comienza a la derecha del punto central y barre 360 grados a las agujas del reloj en torno al centro. Tenga en cuenta que no hay ningún parámetro `SKShaderTileMode`.

También está disponible una sobrecarga [`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKMatrix)) con un parámetro de transformación de matriz. Puede aplicar una transformación de giro para cambiar el punto inicial del degradado. También puede aplicar una transformación de escala para cambiar la dirección de las agujas del reloj a hacia la izquierda.

La página de **degradado de barrido** usa un degradado de barrido para colorear un círculo con un ancho de trazo de 50 píxeles:

[![Degradado de barrido](circular-gradients-images/SweepGradient.png "Degradado de barrido")](circular-gradients-images/SweepGradient-Large.png#lightbox)

La clase `SweepGradientPage` define una matriz de ocho colores con distintos valores de matiz. Tenga en cuenta que la matriz comienza y termina con rojo (un valor de matiz de 0 o 360), que aparece a la derecha en las capturas de pantalla:

```csharp
public class SweepGradientPage : ContentPage
{
    bool drawBackground;

    public SweepGradientPage ()
    {
        Title = "Sweep Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            drawBackground ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Define an array of rainbow colors
            SKColor[] colors = new SKColor[8];

            for (int i = 0; i < colors.Length; i++)
            {
                colors[i] = SKColor.FromHsl(i * 360f / 7, 100, 50);
            }

            SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);

            // Create sweep gradient based on center of canvas
            paint.Shader = SKShader.CreateSweepGradient(center, colors, null);

            // Draw a circle with a wide line
            const int strokeWidth = 50;
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = strokeWidth;

            float radius = (Math.Min(info.Width, info.Height) - strokeWidth) / 2;
            canvas.DrawCircle(center, radius, paint);

            if (drawBackground)
            {
                // Draw the gradient on the whole canvas
                paint.Style = SKPaintStyle.Fill;
                canvas.DrawRect(info.Rect, paint);
            }
        }
    }
}
```

El programa también implementa un `TapGestureRecognizer` que habilita código al final del controlador de `PaintSurface`. Este código usa el mismo degradado para rellenar el lienzo:

[![Degradado de barrido completo](circular-gradients-images/SweepGradientFull.png "Degradado de barrido completo")](circular-gradients-images/SweepGradientFull-Large.png#lightbox)

Estas capturas de pantalla muestran que los rellenos de degradado cualquier área se colorea por ella. Si el degradado no comenzar y terminar con el mismo color, habrá una discontinuidad a la derecha del punto central.

## <a name="the-two-point-conical-gradient"></a>El degradado cónico de dos puntas

El método [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) tiene la siguiente sintaxis:

```csharp
public static SKShader CreateTwoPointConicalGradient (SKPoint startCenter, 
                                                      Single startRadius, 
                                                      SKPoint endCenter, 
                                                      Single endRadius, 
                                                      SKColor[] colors, 
                                                      Single[] colorPos, 
                                                      SKShaderTileMode mode)
```

Los parámetros comienzan con puntos centrales y radios para dos círculos, denominados círculo de _Inicio_ y _fin_ de círculo. Los tres parámetros restantes son los mismos que para `CreateLinearGradient` y `CreateRadialGradient`. Una sobrecarga [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) incluye una transformación de matriz.

Degradado que comienza en el círculo de inicio y finaliza en el círculo final. El parámetro `SKShaderTileMode` rige lo que ocurre más allá de los dos círculos. El degradado de dos puntas cónico es degradado único que no rellena completamente un área. Si los dos círculos tienen el mismo radio, el degradado está restringido a un rectángulo con un ancho que es el mismo que el diámetro de los círculos. Si los dos círculos tienen distintos radios, el degradado constituye un cono.

Es probable que desee experimentar con el degradado cónico de dos puntos, por lo que la página de **gradiente cónico** deriva de `InteractivePage` para permitir que se muevan dos puntos táctiles para los radios de los dos círculos:

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
                       xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Effects.ConicalGradientPage"
                       Title="Conical Gradient">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>
        
        <Grid BackgroundColor="White"
              Grid.Row="0">
            <skiaforms:SKCanvasView x:Name="canvasView"
                                    PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>

        <Picker x:Name="tileModePicker" 
                Grid.Row="1"
                Title="Shader Tile Mode" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKShaderTileMode}">
                    <x:Static Member="skia:SKShaderTileMode.Clamp" />
                    <x:Static Member="skia:SKShaderTileMode.Repeat" />
                    <x:Static Member="skia:SKShaderTileMode.Mirror" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</local:InteractivePage>
```

El archivo de código subyacente define los dos objetos `TouchPoint` con radios fijos de 50 y 100:

```csharp
public partial class ConicalGradientPage : InteractivePage
{
    const int RADIUS1 = 50;
    const int RADIUS2 = 100;

    public ConicalGradientPage ()
    {
        touchPoints = new TouchPoint[2];

        touchPoints[0] = new TouchPoint
        {
            Center = new SKPoint(100, 100),
            Radius = RADIUS1
        };

        touchPoints[1] = new TouchPoint
        {
            Center = new SKPoint(300, 300),
            Radius = RADIUS2
        };

        InitializeComponent();
        baseCanvasView = canvasView;
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

        SKColor[] colors = { SKColors.Red, SKColors.Green, SKColors.Blue };
        SKShaderTileMode tileMode = 
            (SKShaderTileMode)(tileModePicker.SelectedIndex == -1 ? 
                                        0 : tileModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateTwoPointConicalGradient(touchPoints[0].Center,
                                                                  RADIUS1,
                                                                  touchPoints[1].Center,
                                                                  RADIUS2,
                                                                  colors,
                                                                  null,
                                                                  tileMode);
            canvas.DrawRect(info.Rect, paint);
        }

        // Display the touch points here rather than by TouchPoint
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 3;

            foreach (TouchPoint touchPoint in touchPoints)
            {
                canvas.DrawCircle(touchPoint.Center, touchPoint.Radius, paint);
            }
        }
    }
}
```

La matriz de `colors` es rojo, verde y azul. El código hacia la parte inferior del controlador de `PaintSurface` dibuja los dos puntos táctiles como círculos negros para que no obstruyan el degradado.

Observe que `DrawRect` llamada usa el degradado para colorear todo el lienzo. Por lo general, sin embargo, gran parte del lienzo sigue siendo asignar por el degradado. Este es el programa que muestra tres configuraciones posibles:

[![Degradado cónico](circular-gradients-images/ConicalGradient.png "Degradado cónico")](circular-gradients-images/ConicalGradient-Large.png#lightbox)

La pantalla de iOS de la izquierda muestra el efecto de la `SKShaderTileMode` configuración de `Clamp`. El degradado comienza con dentro del borde del círculo más pequeño que es opuesta lado más próximo al segundo círculo de color rojo. El valor `Clamp` también hace que el color rojo continúe hasta el punto del cono. Finaliza el gradiente de azul en el borde exterior del círculo más grande que esté más cerca de la primera círculo, pero continúa con el azul dentro de ese círculo y más allá.

La pantalla de Android es similar, pero con una `SKShaderTileMode` de `Repeat`. Ahora es más claro que el degradado comienza dentro del círculo primera y termina fuera del círculo de segundo. El valor de `Repeat` hace que el degradado se repita de nuevo con rojo dentro del círculo más grande.

La pantalla UWP muestra lo que sucede cuando se mueve el círculo más pequeño completamente dentro del círculo más grande. El degradado deja de ser un cono y en su lugar, rellena el área completa. El efecto es similar del degradado radial, pero es asimétrico si el círculo más pequeño no exactamente se centra dentro del círculo más grande.

Puede dudar de la utilidad práctica del degradado al uno círculo está anidado en otro, pero resulta ideal para una iluminación especular.

## <a name="conical-gradients-for-specular-highlights"></a>Cónicos degradados de resaltes especulares

Anteriormente en este artículo vimos cómo usar un degradado radial para crear una iluminación especular. También puede usar dos puntas cónico degradado para este propósito, y es preferible su aspecto:

[![Resaltado especular cónico](circular-gradients-images/ConicalSpecularHighlight.png "Resaltado especular cónico")](circular-gradients-images/ConicalSpecularHighlight-Large.png#lightbox)

La apariencia asimétrica mejor sugiere la superficie del objeto redondeada. 

El código de dibujo de la página **resaltada especulación cónica** es el mismo que el de la página de **resaltado especular radial** , excepto el sombreador:

```csharp
public class ConicalSpecularHighlightPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateTwoPointConicalGradient(
                                offCenter,
                                1,
                                center,
                                radius,
                                new SKColor[] { SKColors.White, SKColors.Red },
                                null,
                                SKShaderTileMode.Clamp);

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

Los dos círculos tienen centros de `offCenter` y `center`. El círculo centrado en `center` está asociado a un radio que abarca toda la bola, pero el círculo centrado en `offCenter` tiene un radio de un solo píxel. El degradado eficazmente en ese momento comienza y termina en el perímetro de la bola.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
