---
title: Rotaciones 3D en SkiaSharp
description: En este artículo se explica cómo usar transformaciones no afines para girar objetos 2D en el espacio 3D y se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B5894EA0-C415-41F9-93A4-BBF6EC72AFB9
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9bb2720d60f62c0bac6ab30007e226c362d1c5cd
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563879"
---
# <a name="3d-rotations-in-skiasharp"></a>Rotaciones 3D en SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Use transformaciones no afines para girar objetos 2D en el espacio 3D._

Una aplicación común de las transformaciones no afines es simular el giro de un objeto 2D en el espacio 3D:

![Una cadena de texto girada en el espacio 3D](3d-rotation-images/3drotationsexample.png)

Este trabajo implica trabajar con giros tridimensionales y, a continuación, derivar una transformación no afín `SKMatrix` que realiza estos giros 3D.

Es difícil desarrollar esta `SKMatrix` transformación trabajando únicamente en dos dimensiones. El trabajo es mucho más sencillo cuando esta matriz de 3 por 3 se deriva de una matriz de 4 por 4 utilizada en gráficos 3D. SkiaSharp incluye la [`SKMatrix44`](xref:SkiaSharp.SKMatrix44) clase para este propósito, pero se necesita algún fondo en gráficos 3D para comprender los giros 3D y la matriz de transformación 4 por 4.

Un sistema de coordenadas tridimensional agrega un tercer eje denominado Z. conceptualmente, el eje Z está en los ángulos rectos de la pantalla. Los puntos de coordenadas en el espacio 3D se indican con tres números: (x, y, z). En el sistema de coordenadas 3D que se usa en este artículo, el aumento de los valores de X es el derecho y el aumento de los valores de Y se desplazan hacia abajo, al igual que en dos dimensiones. Aumentar los valores Z positivos salen de la pantalla. El origen es la esquina superior izquierda, al igual que en los gráficos 2D. Puede pensar en la pantalla como un plano XY con el eje Z en los ángulos rectos de este plano.

Esto se denomina sistema de coordenadas de la izquierda. Si apunta el índice para su mano izquierda en la dirección de las coordenadas X positivas (a la derecha) y el dedo central en la dirección de incrementar las coordenadas Y (hacia abajo), los puntos de control en la dirección de las coordenadas Z que aumentan se extienden desde la pantalla.

En los gráficos 3D, las transformaciones se basan en una matriz de 4 por 4. Esta es la matriz de identidad 4 por 4:

<pre>
|  1  0  0  0  |
|  0  1  0  0  |
|  0  0  1  0  |
|  0  0  0  1  |
</pre>

Al trabajar con una matriz de 4 por 4, es conveniente identificar las celdas con los números de fila y de columna:

<pre>
|  M11  M12  M13  M14  |
|  M21  M22  M23  M24  |
|  M31  M32  M33  M34  |
|  M41  M42  M43  M44  |
</pre>

Sin embargo, la `Matrix44` clase SkiaSharp es un poco diferente. La única manera de establecer u obtener valores de celda individuales en `SKMatrix44` es mediante el [`Item`](xref:SkiaSharp.SKMatrix44.Item(System.Int32,System.Int32)) indizador. Los índices de fila y columna están basados en cero en lugar de basados en uno, y las filas y columnas se intercambian. Se tiene acceso a la celda M14 del diagrama anterior mediante el indexador `[3, 0]` de un `SKMatrix44` objeto.

En un sistema de gráficos 3D, un punto 3D (x, y, z) se convierte en una matriz de 1 por 4 para multiplicar por la matriz de transformación de 4 por 4:

<pre>
                 |  M11  M12  M13  M14  |
| x  y  z  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

De forma análoga a las transformaciones 2D que tienen lugar en tres dimensiones, se supone que las transformaciones 3D tienen lugar en cuatro dimensiones. La cuarta dimensión se conoce como W y se supone que el espacio 3D existe en el espacio 4D, donde las coordenadas W son iguales a 1. Las fórmulas de transformación son las siguientes:

`x' = M11·x + M21·y + M31·z + M41`

`y' = M12·x + M22·y + M32·z + M42`

`z' = M13·x + M23·y + M33·z + M43`

`w' = M14·x + M24·y + M34·z + M44`

Es obvio que las fórmulas de transformación que las celdas `M11` , `M22` , `M33` son factores de escalado en las direcciones x, y y z, y `M41` , y `M42` `M43` son factores de traducción en las direcciones x, y y z.

Para volver a convertir estas coordenadas en el espacio 3D en el que W es igual a 1, las coordenadas x ', y ' y z se dividen por w ':

`x" = x' / w'`

`y" = y' / w'`

`z" = z' / w'`

`w" = w' / w' = 1`

Esa división de w ' proporciona perspectiva en el espacio 3D. Si w ' es igual a 1, no se produce ninguna perspectiva.

Los giros en el espacio 3D pueden ser bastante complejos, pero los giros más simples son los que están alrededor de los ejes X, Y y Z. Una rotación del ángulo α alrededor del eje X es esta matriz:

<pre>
|  1     0       0     0  |
|  0   cos(α)  sin(α)  0  |
|  0  –sin(α)  cos(α)  0  |
|  0     0       0     1  |
</pre>

Los valores de X siguen siendo los mismos cuando se someten a esta transformación. La rotación alrededor del eje Y deja valores de Y sin modificar:

<pre>
|  cos(α)  0  –sin(α)  0  |
|    0     1     0     0  |
|  sin(α)  0   cos(α)  0  |
|    0     0     0     1  |
</pre>

El giro alrededor del eje Z es el mismo que en los gráficos 2D:

<pre>
|  cos(α)  sin(α)  0  0  |
| –sin(α)  cos(α)  0  0  |
|    0       0     1  0  |
|    0       0     0  1  |
</pre>

La dirección de la rotación viene implícita por la mano del sistema de coordenadas. Se trata de un sistema que se entrega a la izquierda, por lo que si se apunta el pulgar de la mano izquierda hacia los valores en aumento para un eje determinado (a la derecha para la rotación alrededor del eje X), hacia abajo para la rotación alrededor del eje Y, y hacia el usuario para la rotación alrededor del eje Z

`SKMatrix44` tiene métodos y estáticos generalizados [`CreateRotation`](xref:SkiaSharp.SKMatrix44.CreateRotation(System.Single,System.Single,System.Single,System.Single)) [`CreateRotationDegrees`](xref:SkiaSharp.SKMatrix44.CreateRotationDegrees(System.Single,System.Single,System.Single,System.Single)) que le permiten especificar el eje alrededor del cual se produce la rotación:

```csharp
public static SKMatrix44 CreateRotationDegrees (Single x, Single y, Single z, Single degrees)
```

En el caso de la rotación alrededor del eje X, establezca los tres primeros argumentos en 1, 0, 0. En el caso de la rotación alrededor del eje Y, establézcalo en 0, 1, 0 y para el giro alrededor del eje Z, establézcalo en 0, 0, 1.

La cuarta columna de 4 por 4 es para la perspectiva. `SKMatrix44`No tiene métodos para crear transformaciones de perspectiva, pero puede crear una con el código siguiente:

```csharp
SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
perspectiveMatrix[3, 2] = -1 / depth;
```

El motivo del nombre del argumento `depth` será evidente en breve. Ese código crea la matriz:

<pre>
|  1  0  0      0     |
|  0  1  0      0     |
|  0  0  1  -1/depth  |
|  0  0  0      1     |
</pre>

Las fórmulas de transformación dan como resultado el siguiente cálculo de w ':

`w' = –z / depth + 1`

Esto sirve para reducir las coordenadas X e y cuando los valores de Z son menores que cero (conceptualmente detrás del plano XY) y para aumentar las coordenadas X e y para los valores positivos de Z. Cuando la coordenada Z es igual `depth` a, w ' es cero y las coordenadas se convierten en infinitas. Los sistemas de gráficos tridimensionales se crean a partir de una metáfora de cámara y el `depth` valor aquí representa la distancia de la cámara desde el origen del sistema de coordenadas. Si un objeto gráfico tiene una coordenada Z que es una `depth` unidad de origen, se toca conceptualmente la lente de la cámara y se vuelve infinitamente grande.

Tenga en cuenta que probablemente usará este `perspectiveMatrix` valor en combinación con matrices de rotación. Si un objeto Graphics que se está girando tiene coordenadas X o Y mayores que `depth` , es probable que el giro de este objeto en el espacio 3D implique coordenadas Z mayores que `depth` . Esto se debe evitar. Al crear `perspectiveMatrix` , desea establecer `depth` en un valor lo suficientemente grande para todas las coordenadas del objeto de gráficos, independientemente de cómo se gire. Esto garantiza que nunca haya ninguna División por cero.

La combinación de rotaciones y perspectivas 3D requiere la multiplicación de matrices de 4 por 4. Para este propósito, `SKMatrix44` define métodos de concatenación. Si `A` y `B` son `SKMatrix44` objetos, el código siguiente establece un valor igual a a × B:

```csharp
A.PostConcat(B);
```

Cuando se usa una matriz de transformación de 4 por 4 en un sistema de gráficos 2D, se aplica a los objetos 2D. Estos objetos son planos y se supone que tienen coordenadas Z de cero. La multiplicación de transformación es un poco más sencilla que la transformación mostrada anteriormente:

<pre>
                 |  M11  M12  M13  M14  |
| x  y  0  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

Ese valor 0 para z produce fórmulas de transformación que no implican ninguna celda de la tercera fila de la matriz:

x ' = M11 · x + M21 · y + M41

y ' = M12 · x + M22 · y + M42

z ' = M13 · x + M23 · y + M43

w ' = M14 · x + M24 · y + M44

Además, la coordenada z también es irrelevante. Cuando se muestra un objeto 3D en un sistema de gráficos 2D, se contrae en un objeto bidimensional omitiendo los valores de la coordenada Z. En realidad, las fórmulas de transformación son solo las dos:

`x" = x' / w'`

`y" = y' / w'`

Esto significa que se puede omitir la tercera fila *y* la tercera columna de la matriz de 4 por 4.

Pero, si es así, ¿por qué es necesario en primer lugar la matriz de 4 por 4?

Aunque la tercera fila y la *tercera columna de* 4 por 4 son irrelevantes para las transformaciones bidimensionales, la tercera fila y columna desempeñan un rol antes de eso cuando `SKMatrix44` se multiplican varios valores juntos. Por ejemplo, supongamos que multiplica el giro alrededor del eje Y con la transformación de perspectiva:

<pre>
|  cos(α)  0  –sin(α)  0  |   |  1  0  0      0     |   |  cos(α)  0  –sin(α)   sin(α)/depth  |
|    0     1     0     0  | × |  0  1  0      0     | = |    0     1     0           0        |
|  sin(α)  0   cos(α)  0  |   |  0  0  1  -1/depth  |   |  sin(α)  0   cos(α)  -cos(α)/depth  |  
|    0     0     0     1  |   |  0  0  0      1     |   |    0     0     0           1        |
</pre>

En el producto, la celda `M14` contiene ahora un valor de perspectiva. Si desea aplicar esa matriz a objetos 2D, se elimina la tercera fila y columna para convertirla en una matriz de 3 por 3:

<pre>
|  cos(α)  0  sin(α)/depth  |
|    0     1       0        |
|    0     0       1        |
</pre>

Ahora se puede usar para transformar un punto 2D:

<pre>
                |  cos(α)  0  sin(α)/depth  |
|  x  y  1  | × |    0     1       0        | = |  x'  y'  z'  |
                |    0     0       1        |
</pre>

Las fórmulas de transformación son:

`x' = cos(α)·x`

`y' = y`

`z' = (sin(α)/depth)·x + 1`

Ahora, divida todo por z ':

`x" = cos(α)·x / ((sin(α)/depth)·x + 1)`

`y" = y / ((sin(α)/depth)·x + 1)`

Cuando los objetos 2D se giran con un ángulo positivo alrededor del eje Y, los valores X positivos se receden al fondo mientras que los valores X negativos llegan al primer plano. Los valores X parecen ir más cerca del eje Y (que se rige por el valor del coseno), ya que las coordenadas más alejadas del eje Y se vuelven más pequeñas o más grandes a medida que se mueven más lejos del visor o más cerca del visor.

Al utilizar `SKMatrix44` , realice todas las operaciones de rotación y perspectiva 3D multiplicando varios `SKMatrix44` valores. Después, puede extraer una matriz bidimensional de 3 por 3 de la matriz de 4 por 4 mediante la [`Matrix`](xref:SkiaSharp.SKMatrix44.Matrix) propiedad de la `SKMatrix44` clase. Esta propiedad devuelve un `SKMatrix` valor conocido.

La página **rotación 3D** permite experimentar con la rotación 3D. El archivo [**Rotation3DPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml) crea una instancia de cuatro controles deslizantes para establecer la rotación alrededor de los ejes X, y y Z, y para establecer un valor de profundidad:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Transforms.Rotation3DPage"
             Title="Rotation 3D">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
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
                    <Setter Property="Maximum" Value="360" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="xRotateSlider"
                Grid.Row="0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference xRotateSlider},
                              Path=Value,
                              StringFormat='X-Axis Rotation = {0:F0}'}"
               Grid.Row="1" />

        <Slider x:Name="yRotateSlider"
                Grid.Row="2"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference yRotateSlider},
                              Path=Value,
                              StringFormat='Y-Axis Rotation = {0:F0}'}"
               Grid.Row="3" />

        <Slider x:Name="zRotateSlider"
                Grid.Row="4"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference zRotateSlider},
                              Path=Value,
                              StringFormat='Z-Axis Rotation = {0:F0}'}"
               Grid.Row="5" />

        <Slider x:Name="depthSlider"
                Grid.Row="6"
                Maximum="2500"
                Minimum="250"
                ValueChanged="OnSliderValueChanged" />

        <Label Grid.Row="7"
               Text="{Binding Source={x:Reference depthSlider},
                              Path=Value,
                              StringFormat='Depth = {0:F0}'}" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="8"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

Observe que `depthSlider` se inicializa con un `Minimum` valor de 250. Esto implica que el objeto 2D que se gira aquí tiene las coordenadas X e y restringidas a un círculo definido por un radio de 250 píxeles alrededor del origen. Cualquier rotación de este objeto en el espacio 3D siempre dará como resultado valores de coordenadas inferiores a 250.

El archivo de código subyacente [**Rotation3DPage.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml.cs) se carga en un mapa de bits de 300 píxeles cuadrados:

```csharp
public partial class Rotation3DPage : ContentPage
{
    SKBitmap bitmap;

    public Rotation3DPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

Si la transformación 3D está centrada en este mapa de bits, las coordenadas X e y oscilan entre – 150 y 150, mientras que las esquinas son de 212 píxeles desde el centro, por lo que todo está en el radio de 250 píxeles.

El `PaintSurface` controlador crea `SKMatrix44` objetos basados en los controles deslizantes y los multiplica con `PostConcat` . El `SKMatrix` valor extraído del `SKMatrix44` objeto final está rodeado por transformaciones de traslación para centrar la rotación en el centro de la pantalla:

```csharp
public partial class Rotation3DPage : ContentPage
{
    SKBitmap bitmap;

    public Rotation3DPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
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

        // Find center of canvas
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        // Translate center to origin
        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);

        // Use 3D matrix for 3D rotations and perspective
        SKMatrix44 matrix44 = SKMatrix44.CreateIdentity();
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(1, 0, 0, (float)xRotateSlider.Value));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 1, 0, (float)yRotateSlider.Value));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 0, 1, (float)zRotateSlider.Value));

        SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
        perspectiveMatrix[3, 2] = -1 / (float)depthSlider.Value;
        matrix44.PostConcat(perspectiveMatrix);

        // Concatenate with 2D matrix
        SKMatrix.PostConcat(ref matrix, matrix44.Matrix);

        // Translate back to center
        SKMatrix.PostConcat(ref matrix,
            SKMatrix.MakeTranslation(xCenter, yCenter));

        // Set the matrix and display the bitmap
        canvas.SetMatrix(matrix);
        float xBitmap = xCenter - bitmap.Width / 2;
        float yBitmap = yCenter - bitmap.Height / 2;
        canvas.DrawBitmap(bitmap, xBitmap, yBitmap);
    }
}
```

Al experimentar con el cuarto control deslizante, observará que la configuración de profundidad diferente no mueve el objeto más allá del visor, sino que modifica la extensión del efecto de perspectiva:

[![Captura de pantalla triple de la página 3D de rotación](3d-rotation-images/rotation3d-small.png)](3d-rotation-images/rotation3d-large.png#lightbox "Captura de pantalla triple de la página 3D de rotación")

La **rotación animada 3D** también utiliza `SKMatrix44` para animar una cadena de texto en un espacio 3D. El `textPaint` conjunto de objetos como un campo se utiliza en el constructor para determinar los límites del texto:

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    SKCanvasView canvasView;
    float xRotationDegrees, yRotationDegrees, zRotationDegrees;
    string text = "SkiaSharp";
    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        TextSize = 100,
        StrokeWidth = 3,
    };
    SKRect textBounds;

    public AnimatedRotation3DPage()
    {
        Title = "Animated Rotation 3D";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Measure the text
        textPaint.MeasureText(text, ref textBounds);
    }
    ...
}
```

La `OnAppearing` invalidación define tres Xamarin.Forms `Animation` objetos para animar los `xRotationDegrees` `yRotationDegrees` campos, y `zRotationDegrees` a velocidades diferentes. Observe que los períodos de estas animaciones se establecen en números primos (5 segundos, 7 segundos y 11 segundos), por lo que la combinación global solo se repite cada 385 segundos o más de 10 minutos:

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    ...
    protected override void OnAppearing()
    {
        base.OnAppearing();

        new Animation((value) => xRotationDegrees = 360 * (float)value).
            Commit(this, "xRotationAnimation", length: 5000, repeat: () => true);

        new Animation((value) => yRotationDegrees = 360 * (float)value).
            Commit(this, "yRotationAnimation", length: 7000, repeat: () => true);

        new Animation((value) =>
        {
            zRotationDegrees = 360 * (float)value;
            canvasView.InvalidateSurface();
        }).Commit(this, "zRotationAnimation", length: 11000, repeat: () => true);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        this.AbortAnimation("xRotationAnimation");
        this.AbortAnimation("yRotationAnimation");
        this.AbortAnimation("zRotationAnimation");
    }
    ...
}
```

Como en el programa anterior, el `PaintCanvas` controlador crea `SKMatrix44` valores para la rotación y la perspectiva, y los multiplica juntos:

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find center of canvas
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        // Translate center to origin
        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);

        // Scale so text fits
        float scale = Math.Min(info.Width / textBounds.Width,
                               info.Height / textBounds.Height);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(scale, scale));

        // Calculate composite 3D transforms
        float depth = 0.75f * scale * textBounds.Width;

        SKMatrix44 matrix44 = SKMatrix44.CreateIdentity();
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(1, 0, 0, xRotationDegrees));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 1, 0, yRotationDegrees));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 0, 1, zRotationDegrees));

        SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
        perspectiveMatrix[3, 2] = -1 / depth;
        matrix44.PostConcat(perspectiveMatrix);

        // Concatenate with 2D matrix
        SKMatrix.PostConcat(ref matrix, matrix44.Matrix);

        // Translate back to center
        SKMatrix.PostConcat(ref matrix,
            SKMatrix.MakeTranslation(xCenter, yCenter));

        // Set the matrix and display the text
        canvas.SetMatrix(matrix);
        float xText = xCenter - textBounds.MidX;
        float yText = yCenter - textBounds.MidY;
        canvas.DrawText(text, xText, yText, textPaint);
    }
}
```

Este giro 3D está rodeado de varias transformaciones 2D para descartar el centro de giro al centro de la pantalla, y para escalar el tamaño de la cadena de texto de modo que tenga el mismo ancho que la pantalla:

[![Captura de pantalla triple de la página 3D de rotación animada](3d-rotation-images/animatedrotation3d-small.png)](3d-rotation-images/animatedrotation3d-large.png#lightbox "Captura de pantalla triple de la página 3D de rotación animada")

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)