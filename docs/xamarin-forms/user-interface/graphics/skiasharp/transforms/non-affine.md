---
title: Transformaciones no afines
description: En este artículo se explica cómo crear efectos de perspectiva y de inclinación con la tercera columna de la matriz de transformación y se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 785F4D13-7430-492E-B24E-3B45C560E9F1
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6de5e21c509203c5402ed8c7e75908b54808d140
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556898"
---
# <a name="non-affine-transforms"></a>Transformaciones no afines

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Crear efectos de perspectiva y de inclinación con la tercera columna de la matriz de transformación_

La traslación, la escala, la rotación y el sesgo se clasifican como transformaciones *afines* . Las transformaciones afines conservan las líneas paralelas. Si dos líneas son paralelas antes de la transformación, permanecen paralelas después de la transformación. Los rectángulos siempre se transforman a los paralelogramos.

Sin embargo, SkiaSharp también es capaz de realizar transformaciones no afines, que tienen la capacidad de transformar un rectángulo en cualquier cuadrangular convexa:

![Un mapa de bits transformado en una cuadrangular convexa](non-affine-images/nonaffinetransformexample.png)

Una cuadrangular convexa es una figura de cuatro caras con ángulos interiores siempre inferior a 180 grados y lados que no se cruzan.

Las transformaciones no afines se producen cuando la tercera fila de la matriz de transformación se establece en valores distintos de 0, 0 y 1. La `SKMatrix` multiplicación completa es:

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z' |
              │ TransX  TransY  Persp2 │
</pre>

Las fórmulas de transformación resultantes son:

x ' = ScaleX · x + SkewX · y + TransX

y ' = Sesgoy · x + escalay · y + trans

z ' = Persp0 · x + Persp1 · y + Persp2

La regla fundamental de usar una matriz de 3 por 3 para las transformaciones bidimensionales es que todo permanece en el plano donde Z es igual a 1. A `Persp0` menos `Persp1` que y sean 0, y `Persp2` es igual a 1, la transformación ha sacado las coordenadas Z de ese plano.

Para restaurar esto en una transformación bidimensional, las coordenadas se deben devolver a ese plano. Se requiere otro paso. Los valores x ', y ' y z ' deben dividirse entre z ':

x "= x '/z '

y "= y '/z '

z "= z '/z ' = 1

Estos se conocen como *coordenadas homogéneas* y se desarrollaron con mathematician Ferdinand Möbius de agosto, mucho mejor conocido para su Oddity topológico, la franja Möbius.

Si z ' es 0, la división da como resultado coordenadas infinitas. De hecho, una de Möbius's motivaciones para desarrollar coordenadas homogéneas era la capacidad de representar valores infinitos con números finitos.

Sin embargo, al mostrar los gráficos, desea evitar la representación de algo con coordenadas que se transformen en valores infinitos. Esas coordenadas no se representarán. Todo lo que esté cerca de esas coordenadas será muy grande y probablemente no sea coherente visualmente.

En esta ecuación, no desea que el valor de z ' sea cero:

z ' = Persp0 · x + Persp1 · y + Persp2

Por consiguiente, estos valores tienen algunas restricciones prácticas: 

La `Persp2` celda puede ser cero o no cero. Si `Persp2` es cero, z ' es cero para el punto (0,0) y normalmente no es deseable porque ese punto es muy común en los gráficos bidimensionales. Si `Persp2` no es igual a cero, no se produce ninguna pérdida de carácter general si `Persp2` se fija en 1. Por ejemplo, si determina que `Persp2` debe ser 5, simplemente puede dividir todas las celdas de la matriz 5, lo que hace que sea `Persp2` igual a 1 y el resultado será el mismo.

Por estos motivos, `Persp2` a menudo se fija en 1, que es el mismo valor en la matriz de identidad.

Generalmente, `Persp0` y `Persp1` son números pequeños. Por ejemplo, supongamos que comienza con una matriz de identidad pero establece `Persp0` en 0,01:

<pre>
| 1  0   0.01 |
| 0  1    0   |
| 0  0    1   |
</pre>

Las fórmulas de transformación son:

x ' = x/(0,01 · x + 1)

y ' = y/(0,01 · x + 1)

Ahora use esta transformación para representar un cuadrado de 100 píxeles situado en el origen. Aquí se muestra cómo se transforman las cuatro esquinas:

(0,0) → (0,0)

(0, 100) → (0, 100)

(100, 0) → (50, 0)

(100, 100) → (50, 50)

Cuando x es 100, el denominador z ' es 2, por lo que las coordenadas x e y se reducen de forma eficaz. El lado derecho del cuadro se vuelve más corto que el lado izquierdo:

![Cuadro sometido a una transformación no afín](non-affine-images/nonaffinetransform.png)

La `Persp` parte de estos nombres de celda hace referencia a "Perspective" porque el escorzo sugiere que el cuadro ahora está inclinado con el lado derecho más allá del visor.

La página **perspectiva de pruebas** le permite experimentar con los valores de `Persp0` y `Pers1` para hacerse una idea de cómo funcionan. Los valores razonables de estas celdas de la matriz son tan pequeños que el `Slider` en el plataforma universal de Windows no puede controlarlas correctamente. Para dar cabida al problema de UWP, los dos `Slider` elementos de [**TestPerspective. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml) deben inicializarse en el intervalo de – 1 a 1:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Transforms.TestPerspectivePage"
             Title="Test Perpsective">
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
                    <Setter Property="Minimum" Value="-1" />
                    <Setter Property="Maximum" Value="1" />
                    <Setter Property="Margin" Value="20, 0" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="persp0Slider"
                Grid.Row="0"
                ValueChanged="OnPersp0SliderValueChanged" />

        <Label x:Name="persp0Label"
               Text="Persp0 = 0.0000"
               Grid.Row="1" />

        <Slider x:Name="persp1Slider"
                Grid.Row="2"
                ValueChanged="OnPersp1SliderValueChanged" />

        <Label x:Name="persp1Label"
               Text="Persp1 = 0.0000"
               Grid.Row="3" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="4"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

Los controladores de eventos de los controles deslizantes del [`TestPerspectivePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml.cs) archivo de código subyacente dividen los valores por 100 para que estén comprendidos entre-0,01 y 0,01. Además, el constructor se carga en un mapa de bits:

```csharp
public partial class TestPerspectivePage : ContentPage
{
    SKBitmap bitmap;

    public TestPerspectivePage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnPersp0SliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        Slider slider = (Slider)sender;
        persp0Label.Text = String.Format("Persp0 = {0:F4}", slider.Value / 100);
        canvasView.InvalidateSurface();
    }

    void OnPersp1SliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        Slider slider = (Slider)sender;
        persp1Label.Text = String.Format("Persp1 = {0:F4}", slider.Value / 100);
        canvasView.InvalidateSurface();
    }
    ...
}
```

El `PaintSurface` controlador calcula un `SKMatrix` valor denominado `perspectiveMatrix` basándose en los valores de estos dos controles deslizantes divididos por 100. Se combina con dos transformaciones de traslación que colocan el centro de esta transformación en el centro del mapa de bits:

```csharp
public partial class TestPerspectivePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Calculate perspective matrix
        SKMatrix perspectiveMatrix = SKMatrix.MakeIdentity();
        perspectiveMatrix.Persp0 = (float)persp0Slider.Value / 100;
        perspectiveMatrix.Persp1 = (float)persp1Slider.Value / 100;

        // Center of screen
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);
        SKMatrix.PostConcat(ref matrix, perspectiveMatrix);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(xCenter, yCenter));

        // Coordinates to center bitmap on canvas
        float x = xCenter - bitmap.Width / 2;
        float y = yCenter - bitmap.Height / 2;

        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, x, y);
    }
}
```

Estas son algunas imágenes de ejemplo:

[![Captura de pantalla triple de la página perspectiva de pruebas](non-affine-images/testperspective-small.png)](non-affine-images/testperspective-large.png#lightbox "Captura de pantalla triple de la página perspectiva de pruebas")

Al experimentar con los controles deslizantes, observará que los valores más allá de 0,0066 o inferiores a 0,0066 hacen que la imagen se corte repentinamente y sea incoherente. El mapa de bits que se transforma tiene un cuadrado de 300 píxeles. Se transforma en relación con su centro, por lo que las coordenadas del intervalo del mapa de bits van de – 150 a 150. Recuerde que el valor de z ' es:

z ' = Persp0 · x + Persp1 · y + 1

Si `Persp0` o `Persp1` es mayor que 0,0066 o inferior a 0,0066, siempre hay alguna coordenada del mapa de bits que tiene como resultado un valor de z ' de cero. Esto produce una división por cero y la representación se convierte en un desorden. Al usar transformaciones no afines, desea evitar representar nada con las coordenadas que causan la división por cero.

Por lo general, no estará configurando `Persp0` y `Persp1` de forma aislada. También suele ser necesario establecer otras celdas de la matriz para lograr determinados tipos de transformaciones no afines.

Una transformación no afín es una *transformación de inclinación*. Este tipo de transformación no afín conserva las dimensiones globales de un rectángulo, pero un lado:

![Un cuadro sometido a una transformación de cónica](non-affine-images/tapertransform.png)

La [`TaperTransform`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransform.cs) clase realiza un cálculo generalizado de una transformación no afín basada en estos parámetros:

- tamaño rectangular de la imagen que se va a transformar.
- una enumeración que indica el lado del rectángulo que almacena en cinta
- otra enumeración que indica el modo en que las cintas de ti y
- la extensión de la inclinación.

Este es el código:

```csharp
enum TaperSide { Left, Top, Right, Bottom }

enum TaperCorner { LeftOrTop, RightOrBottom, Both }

static class TaperTransform
{
    public static SKMatrix Make(SKSize size, TaperSide taperSide, TaperCorner taperCorner, float taperFraction)
    {
        SKMatrix matrix = SKMatrix.MakeIdentity();

        switch (taperSide)
        {
            case TaperSide.Left:
                matrix.ScaleX = taperFraction;
                matrix.ScaleY = taperFraction;
                matrix.Persp0 = (taperFraction - 1) / size.Width;

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewY = size.Height * matrix.Persp0;
                        matrix.TransY = size.Height * (1 - taperFraction);
                        break;

                    case TaperCorner.Both:
                        matrix.SkewY = (size.Height / 2) * matrix.Persp0;
                        matrix.TransY = size.Height * (1 - taperFraction) / 2;
                        break;
                }
                break;

            case TaperSide.Top:
                matrix.ScaleX = taperFraction;
                matrix.ScaleY = taperFraction;
                matrix.Persp1 = (taperFraction - 1) / size.Height;

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewX = size.Width * matrix.Persp1;
                        matrix.TransX = size.Width * (1 - taperFraction);
                        break;

                    case TaperCorner.Both:
                        matrix.SkewX = (size.Width / 2) * matrix.Persp1;
                        matrix.TransX = size.Width * (1 - taperFraction) / 2;
                        break;
                }
                break;

            case TaperSide.Right:
                matrix.ScaleX = 1 / taperFraction;
                matrix.Persp0 = (1 - taperFraction) / (size.Width * taperFraction);

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewY = size.Height * matrix.Persp0;
                        break;

                    case TaperCorner.Both:
                        matrix.SkewY = (size.Height / 2) * matrix.Persp0;
                        break;
                }
                break;

            case TaperSide.Bottom:
                matrix.ScaleY = 1 / taperFraction;
                matrix.Persp1 = (1 - taperFraction) / (size.Height * taperFraction);

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewX = size.Width * matrix.Persp1;
                        break;

                    case TaperCorner.Both:
                        matrix.SkewX = (size.Width / 2) * matrix.Persp1;
                        break;
                }
                break;
        }
        return matrix;
    }
}
```

Esta clase se usa en la página de **transformación de inclinación** . El archivo XAML crea una instancia `Picker` de dos elementos para seleccionar los valores de enumeración y un `Slider` para elegir la fracción de inclinación. El [`PaintSurface`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransformPage.xaml.cs#L55) controlador combina la transformación de inclinación con dos transformaciones de traslación para hacer la transformación en relación con la esquina superior izquierda del mapa de bits:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    TaperSide taperSide = (TaperSide)taperSidePicker.SelectedItem;
    TaperCorner taperCorner = (TaperCorner)taperCornerPicker.SelectedItem;
    float taperFraction = (float)taperFractionSlider.Value;

    SKMatrix taperMatrix =
        TaperTransform.Make(new SKSize(bitmap.Width, bitmap.Height),
                            taperSide, taperCorner, taperFraction);

    // Display the matrix in the lower-right corner
    SKSize matrixSize = matrixDisplay.Measure(taperMatrix);

    matrixDisplay.Paint(canvas, taperMatrix,
        new SKPoint(info.Width - matrixSize.Width,
                    info.Height - matrixSize.Height));

    // Center bitmap on canvas
    float x = (info.Width - bitmap.Width) / 2;
    float y = (info.Height - bitmap.Height) / 2;

    SKMatrix matrix = SKMatrix.MakeTranslation(-x, -y);
    SKMatrix.PostConcat(ref matrix, taperMatrix);
    SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(x, y));

    canvas.SetMatrix(matrix);
    canvas.DrawBitmap(bitmap, x, y);
}
```

Estos son algunos ejemplos:

[![Captura de pantalla triple de la página transformación de cónica](non-affine-images/tapertransform-small.png)](non-affine-images/tapertransform-large.png#lightbox "Captura de pantalla triple de la página transformación de cónica")

Otro tipo de transformaciones no afines generalizadas es la rotación 3D, que se muestra en el siguiente artículo, [**rotaciones 3D**](3d-rotation.md).

La transformación no afín puede transformar un rectángulo en cualquier cuadrangular convexa. Esto se muestra en la página **Mostrar matriz no afín** . Es muy similar a la página **Mostrar matriz afín** del artículo [**transformaciones de matriz**](matrix.md) , excepto que tiene un cuarto `TouchPoint` objeto para manipular la cuarta esquina del mapa de bits:

[![Captura de pantalla triple de la página Mostrar matriz no afín](non-affine-images/shownonaffinematrix-small.png)](non-affine-images/shownonaffinematrix-large.png#lightbox "Captura de pantalla triple de la página Mostrar matriz no afín")

Siempre que no intente crear un ángulo interior de una de las esquinas del mapa de bits superior a 180 grados, o hacer que dos lados se crucen entre sí, el programa calcula correctamente la transformación mediante este método de la [`ShowNonAffineMatrixPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowNonAffineMatrixPage.xaml.cs) clase:

```csharp
static SKMatrix ComputeMatrix(SKSize size, SKPoint ptUL, SKPoint ptUR, SKPoint ptLL, SKPoint ptLR)
{
    // Scale transform
    SKMatrix S = SKMatrix.MakeScale(1 / size.Width, 1 / size.Height);

    // Affine transform
    SKMatrix A = new SKMatrix
    {
        ScaleX = ptUR.X - ptUL.X,
        SkewY = ptUR.Y - ptUL.Y,
        SkewX = ptLL.X - ptUL.X,
        ScaleY = ptLL.Y - ptUL.Y,
        TransX = ptUL.X,
        TransY = ptUL.Y,
        Persp2 = 1
    };

    // Non-Affine transform
    SKMatrix inverseA;
    A.TryInvert(out inverseA);
    SKPoint abPoint = inverseA.MapPoint(ptLR);
    float a = abPoint.X;
    float b = abPoint.Y;

    float scaleX = a / (a + b - 1);
    float scaleY = b / (a + b - 1);

    SKMatrix N = new SKMatrix
    {
        ScaleX = scaleX,
        ScaleY = scaleY,
        Persp0 = scaleX - 1,
        Persp1 = scaleY - 1,
        Persp2 = 1
    };

    // Multiply S * N * A
    SKMatrix result = SKMatrix.MakeIdentity();
    SKMatrix.PostConcat(ref result, S);
    SKMatrix.PostConcat(ref result, N);
    SKMatrix.PostConcat(ref result, A);

    return result;
}
```

Para facilitar el cálculo, este método obtiene la transformación total como un producto de tres transformaciones independientes, que se indican aquí con flechas que muestran cómo estas transformaciones modifican las cuatro esquinas del mapa de bits:

(0,0) → (0, 0) → (0,0) → (x0, Y0) (superior izquierda)

(0, H) → (0, 1) → (0, 1) → (x1, Y1) (inferior izquierda)

(W,0) → (1, 0) → (1,0) → (x2, Y2) (superior derecha)

(W, H) → (1,1, 1) → (a, b) → (x3, Y3) (inferior derecha)

Las coordenadas finales a la derecha son los cuatro puntos asociados a los cuatro puntos táctiles. Estas son las coordenadas finales de las esquinas del mapa de bits.

W y H representan el ancho y el alto del mapa de bits. La primera transformación `S` simplemente escala el mapa de bits a un cuadrado de 1 píxel. La segunda transformación es la transformación no afín `N` y la tercera es la transformación afín `A` . Esa transformación afín se basa en tres puntos, por lo que es igual que el método afín anterior [`ComputeMatrix`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs#L68) y no implica la cuarta fila con el punto (a, b).

Los `a` `b` valores y se calculan para que la tercera transformación sea afín. El código obtiene el inverso de la transformación afín y, a continuación, lo usa para asignar la esquina inferior derecha. Es el punto (a, b).

Otro uso de las transformaciones no afines es imitar los gráficos tridimensionales. En el siguiente artículo, [**rotaciones 3D**](3d-rotation.md) verá cómo girar un gráfico bidimensional en un espacio 3D.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)