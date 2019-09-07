---
title: Rotaciones de 3D de SkiaSharp
description: En este artículo se explica cómo utilizar las transformaciones no afines girar objetos 2D en el espacio 3D y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B5894EA0-C415-41F9-93A4-BBF6EC72AFB9
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
ms.openlocfilehash: 60f09b2e60708df6b1e6b68be7ce0792bc8cd9b0
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759181"
---
# <a name="3d-rotations-in-skiasharp"></a>Rotaciones de 3D de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Utilizar las transformaciones no afines girar objetos 2D en el espacio 3D._

Una aplicación común de las transformaciones no afines está simulando el giro de un objeto 2D en el espacio 3D:

![](3d-rotation-images/3drotationsexample.png "Una cadena de texto girada en el espacio 3D")

Este trabajo implica trabajar con rotaciones tridimensionales y, a continuación, derivar no afines `SKMatrix` transformación que realiza estas rotaciones 3D.

Resulta difícil desarrollar `SKMatrix` transformación funciona únicamente dentro de dos dimensiones. El trabajo pasa a ser mucho más fácil cuando esta matriz 3 por 3 se deriva de una matriz de 4 x 4 utilizada en los gráficos 3D. SkiaSharp incluye la [ `SKMatrix44` ](xref:SkiaSharp.SKMatrix44) clase para este propósito, pero algunos en segundo plano en los gráficos 3D es necesaria para comprender las rotaciones de 3D y la matriz de transformación de 4 por 4.

Un sistema de coordenadas tridimensional agrega un tercer eje Z. conceptualmente llamado, el eje Z es perpendicular a la pantalla. Se indican los puntos de coordenada en el espacio 3D con tres números: (x, y, z). En el modelo 3D del sistema de coordenadas utilizado en este artículo, aumentar los valores de X son a la derecha e incrementar los valores de Y dejan de funcionar, al igual que en dos dimensiones. Los valores de Z positivos crecientes vienen fuera de la pantalla. El origen es la esquina superior izquierda, al igual que en los gráficos 2D. La pantalla se puede considerar como un plano XY con el eje Z en ángulos rectos este plano.

Esto se denomina un sistema de coordenadas izquierdo. Si señala el índice de la mano izquierda en la dirección de positivo X coordenadas (a la derecha) y coordina el dedo intermedio en la dirección del creciente Y (hacia abajo), a continuación, el pulgar puntos en la dirección de aumentar las coordenadas Z, ampliación horizontal de la pantalla.

En los gráficos 3D, las transformaciones se basan en una matriz de 4 por 4. Aquí es la matriz de identidad de 4 por 4:

<pre>
|  1  0  0  0  |
|  0  1  0  0  |
|  0  0  1  0  |
|  0  0  0  1  |
</pre>

Al trabajar con una matriz de 4 por 4, es conveniente identificar las celdas con sus números de fila y columna:

<pre>
|  M11  M12  M13  M14  |
|  M21  M22  M23  M24  |
|  M31  M32  M33  M34  |
|  M41  M42  M43  M44  |
</pre>

Sin embargo, el SkiaSharp `Matrix44` clase es un poco diferente. La única forma de establecer u obtener los valores de celda individual en `SKMatrix44` es mediante la [ `Item` ](xref:SkiaSharp.SKMatrix44.Item(System.Int32,System.Int32)) indizador. Los índices de fila y columna son basado en cero en lugar de basado en uno, y se intercambian las filas y columnas. La celda M14 en el diagrama anterior se obtiene acceso mediante el indizador `[3, 0]` en un `SKMatrix44` objeto.

En un sistema de gráficos 3D, un punto 3D (x, y, z) se convierte en una matriz de 1 al 4 para multiplicar por la matriz de transformación de 4 por 4:

<pre>
                 |  M11  M12  M13  M14  |
| x  y  z  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

Como 2D transformaciones que tienen lugar en tres dimensiones, las transformaciones 3D se supone que tienen lugar en cuatro dimensiones. La cuarta dimensión se conoce como W y el espacio 3D se supone que existe dentro del espacio de 4D donde las coordenadas de W son iguales a 1. Las fórmulas de transformación son los siguientes:

`x' = M11·x + M21·y + M31·z + M41`

`y' = M12·x + M22·y + M32·z + M42`

`z' = M13·x + M23·y + M33·z + M43`

`w' = M14·x + M24·y + M34·z + M44`

En las fórmulas de transformación resulta evidente que las celdas `M11`, `M22`, `M33` son factores de escala en las direcciones X, Y y Z, y `M41`, `M42`, y `M43` son factores de traslación en la X, Y y Z direcciones.

Para volver a convertir estas coordenadas en el espacio 3D donde W es igual a 1, x', y', y z 'coordenadas son todos divididas por w':

`x" = x' / w'`

`y" = y' / w'`

`z" = z' / w'`

`w" = w' / w' = 1`

División por w' proporciona una perspectiva en el espacio 3D. Si w' es igual a 1, a continuación, se produce ninguna perspectiva.

Los giros en el espacio 3D pueden ser bastante complejos, pero las rotaciones más simple son aquellos alrededor del eje X, Y y Z. Un giro del ángulo α alrededor del eje X es esta matriz:

<pre>
|  1     0       0     0  |
|  0   cos(α)  sin(α)  0  |
|  0  –sin(α)  cos(α)  0  |
|  0     0       0     1  |
</pre>

Los valores de X siguen siendo los mismos cuando está sometido a esta transformación. Rotación alrededor del eje Y deja los valores de Y sin cambios:

<pre>
|  cos(α)  0  –sin(α)  0  |
|    0     1     0     0  |
|  sin(α)  0   cos(α)  0  |
|    0     0     0     1  |
</pre>

Rotación alrededor del eje Z es igual que en los gráficos 2D:

<pre>
|  cos(α)  sin(α)  0  0  |
| –sin(α)  cos(α)  0  0  |
|    0       0     1  0  |
|    0       0     0  1  |
</pre>

La dirección de la rotación está implícito en la situación del sistema de coordenadas. Se trata de un sistema zurdo, por lo que si el punto de control de posición de la mano izquierda hacia el aumento de los valores de un eje determinado, a la derecha de la rotación alrededor del eje X, hacia abajo de rotación alrededor del eje Y y hacia TI rotación alrededor del eje Z, a continuación, la curva de yo el otro dedo indica la dirección de rotación de ángulos positivos.

`SKMatrix44` se generalizado estático [ `CreateRotation` ](xref:SkiaSharp.SKMatrix44.CreateRotation(System.Single,System.Single,System.Single,System.Single)) y [ `CreateRotationDegrees` ](xref:SkiaSharp.SKMatrix44.CreateRotationDegrees(System.Single,System.Single,System.Single,System.Single)) métodos que le permiten especificar el eje alrededor de la cual se produce la rotación:

```csharp
public static SKMatrix44 CreateRotationDegrees (Single x, Single y, Single z, Single degrees)
```

Rotación alrededor del eje X, establezca los tres primeros argumentos en 1, 0, 0. Rotación alrededor del eje Y, los establecerá en 0, 1, 0 y rotación alrededor del eje Z, los establecerá en 0, 0, 1.

Es la cuarta columna de 4 por 4 de perspectiva. El `SKMatrix44` no tiene métodos para crear transformaciones de perspectiva, pero puede crear una propia mediante el código siguiente:

```csharp
SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
perspectiveMatrix[3, 2] = -1 / depth;
```

La razón para el nombre del argumento `depth` será evidente en breve. Ese código crea la matriz:

<pre>
|  1  0  0      0     |
|  0  1  0      0     |
|  0  0  1  -1/depth  |
|  0  0  0      1     |
</pre>

Como resultado de las fórmulas de transformación en el siguiente cálculo de w':

`w' = –z / depth + 1`

Esto sirve para reducir las coordenadas X e Y cuando los valores de Z son inferiores a cero (conceptualmente detrás del plano XY) y aumentar las coordenadas X e Y para los valores positivos de Z. Cuando la coordenada Z es igual a `depth`, a continuación, w' es cero, y las coordenadas se convierten en infinitas. Los sistemas de gráficos tridimensionales se basan en una metáfora de la cámara y el `depth` aquí el valor representa la distancia de la cámara desde el origen del sistema de coordenadas. Si un objeto gráfico tiene una Z es coordinar `depth` unidades desde el origen, conceptualmente es tocar la lente de la cámara y pasa a ser más grande.

Tenga en cuenta que probablemente usará este `perspectiveMatrix` valor junto con las matrices de rotación. Si un objeto graphics que se giran tiene las coordenadas X o Y mayor `depth`, entonces es probable que implican las coordenadas Z mayor que la rotación de este objeto en el espacio 3D `depth`. ¡Esto se debe evitar! Al crear `perspectiveMatrix` desea establecer `depth` en un valor lo suficientemente grande para todas las coordenadas del objeto de gráficos con independencia de cómo se gira. Esto garantiza que nunca es una división por cero.

Combinación de perspectiva y rotaciones 3D requiere la multiplicación de matrices de 4 por 4 juntos. Para este propósito, `SKMatrix44` define los métodos de concatenación. Si `A` y `B` son `SKMatrix44` objetos, a continuación, el código siguiente establece un igual a un × B:

```csharp
A.PostConcat(B);
```

Cuando se usa una matriz de transformación de 4 por 4 en un sistema de gráficos 2D, se aplica a objetos 2D. Estos objetos son fijos y se supone que tiene las coordenadas Z de cero. La multiplicación de transformación es un poco más sencilla que la transformación mostrada anteriormente:

<pre>
                 |  M11  M12  M13  M14  |
| x  y  0  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

Que el valor de 0 para los resultados de la z en las fórmulas de transformación que no implican ninguna de las celdas de la tercera fila de la matriz:

x' = M11·x M21·y + M41

y' = M12·x M22·y + M42

z' = M13·x M23·y + M43

w' = M14·x M24·y + M44

Además, el punto z' coordenadas también es irrelevante aquí. Cuando se muestra un objeto 3D en un sistema de gráficos 2D, se contrae a un objeto bidimensional pasando por alto los valores de las coordenadas Z. Las fórmulas de transformación son en realidad, estos dos:

`x" = x' / w'`

`y" = y' / w'`

Esto significa que la tercera fila *y* se puede omitir la tercera columna de la matriz de 4 por 4.

Pero si es así, ¿por qué es la matriz de 4 por 4 incluso necesario en primer lugar?

Aunque la tercera fila y la tercera columna de 4 por 4 no son pertinentes para las transformaciones bidimensionales, la tercera fila y columna *hacer* desempeñan un papel antes de que cuando varios `SKMatrix44` se multiplican los valores. Por ejemplo, supongamos que multiplica rotación alrededor del eje Y con la transformación de perspectiva:

<pre>
|  cos(α)  0  –sin(α)  0  |   |  1  0  0      0     |   |  cos(α)  0  –sin(α)   sin(α)/depth  |
|    0     1     0     0  | × |  0  1  0      0     | = |    0     1     0           0        |
|  sin(α)  0   cos(α)  0  |   |  0  0  1  -1/depth  |   |  sin(α)  0   cos(α)  -cos(α)/depth  |  
|    0     0     0     1  |   |  0  0  0      1     |   |    0     0     0           1        |
</pre>

En el producto, la celda `M14` ahora contiene un valor de la perspectiva. Si desea aplicar esa matriz a objetos 2D, la tercera fila y columna se eliminan para convertirla en una matriz de 3 por 3:

<pre>
|  cos(α)  0  sin(α)/depth  |
|    0     1       0        |
|    0     0       1        |
</pre>

Ahora puede usarse para transformar un punto de 2D:

<pre>
                |  cos(α)  0  sin(α)/depth  |
|  x  y  1  | × |    0     1       0        | = |  x'  y'  z'  |
                |    0     0       1        |
</pre>

Las fórmulas de transformación son:

`x' = cos(α)·x`

`y' = y`

`z' = (sin(α)/depth)·x + 1`

Ahora todo lo dividido z ":

`x" = cos(α)·x / ((sin(α)/depth)·x + 1)`

`y" = y / ((sin(α)/depth)·x + 1)`

Cuando se giran los objetos 2D con un ángulo positivo alrededor del eje Y, a continuación, positivo valores X se desvanecen en segundo plano mientras negativo X valores vienen al primer plano. Los valores X parecen acercarse al eje Y (que se rige por el valor de coseno) como coordenadas más alejado del eje Y se convierte en más de cerca en el Visor de o menor o mayor cuando se mueven más lejos del Visor.

Cuando se usa `SKMatrix44`, realizar todas las operaciones de perspectiva y giro 3D mediante la multiplicación diversos `SKMatrix44` valores. A continuación, puede extraer una matriz bidimensional de 3 por 3 de 4 por 4 matriz utilizando el [ `Matrix` ](xref:SkiaSharp.SKMatrix44.Matrix) propiedad de la `SKMatrix44` clase. Esta propiedad devuelve un familiar `SKMatrix` valor.

El **giro 3D** página permite experimentar con giro 3D. El [ **Rotation3DPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml) cuatro controles deslizantes para establecer el giro alrededor del eje X, Y y Z y para establecer un valor de profundidad crea una instancia de archivo:

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

Tenga en cuenta que el `depthSlider` se inicializa con un `Minimum` valor de 250. Esto implica que el objeto 2D que se giran aquí tiene las coordenadas X e Y restringidas a un círculo definido por un radio de 250 píxeles en torno al origen. Los valores de coordenadas inferior a 250 producirá siempre cualquier rotación de este objeto en el espacio 3D.

El [ **Rotation3DPage.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml.cs) carga el archivo de código subyacente en un mapa de bits que es de 300 píxeles cuadrados:

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

Si la transformación 3D se centra en este mapa de bits, a continuación, coordenadas X e Y el intervalo entre –150 y 150, mientras que las esquinas son 212 píxeles desde el centro, por lo que todo está dentro del radio de 250 píxeles.

El `PaintSurface` controlador crea `SKMatrix44` objetos basados en los controles deslizantes y se multiplican juntos mediante `PostConcat`. El `SKMatrix` valor extraído de la última `SKMatrix44` objeto está rodeado por traduzco las transformaciones para centrar la rotación en el centro de la pantalla:

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

Cuando se experimenta con el control deslizante cuarto, observará que la configuración de profundidad diferentes no mover el objeto aún más lejos del Visor, pero en su lugar alter el alcance de la perspectiva de efecto:

[![](3d-rotation-images/rotation3d-small.png "Captura de pantalla triple de la página de giro 3D")](3d-rotation-images/rotation3d-large.png#lightbox "Triple captura de pantalla de la página de giro 3D")

El **animado giro 3D** también usa `SKMatrix44` para animar una cadena de texto en el espacio 3D. La `textPaint` objeto establecido como un campo se usa en el constructor para determinar los límites del texto:

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

El `OnAppearing` invalidación define tres Xamarin.Forms `Animation` objetos que se va a animar el `xRotationDegrees`, `yRotationDegrees`, y `zRotationDegrees` campos con distintas velocidades. Tenga en cuenta que se establecen los períodos de estas animaciones preparar números (5 segundos, 7 segundos y 11 segundos) para que la combinación general solo repite cada 385 segundos o más de 10 minutos:

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

Como se muestra en el programa anterior, el `PaintCanvas` controlador crea `SKMatrix44` los valores de rotación y perspectiva y multiplica juntos:

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

Este giro 3D está acotado por varias transformaciones 2D para mover el centro de rotación en el centro de la pantalla y escalar el tamaño de la cadena de texto para que sea el mismo ancho que la pantalla:

[![](3d-rotation-images/animatedrotation3d-small.png "Triple captura de pantalla de la página de rotación animados 3D")](3d-rotation-images/animatedrotation3d-large.png#lightbox "Triple captura de pantalla de la página de rotación animados 3D")

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
