---
title: Transformaciones no afines
description: En este artículo se explica cómo crear efectos de inclinación y perspectiva con la tercera columna de la matriz de transformación y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 785F4D13-7430-492E-B24E-3B45C560E9F1
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
ms.openlocfilehash: f526be2156c4d74cf695b6b11706e3bbb7f355bb
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642991"
---
# <a name="non-affine-transforms"></a>Transformaciones no afines

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Crear efectos de inclinación y perspectiva con la tercera columna de la matriz de transformación_

Traslación, escala, rotación y sesgado se clasifican como *afín* transforma. Transformaciones afines conservan líneas paralelas. Si dos líneas paralelas antes de la transformación, permanecen paralelo después de la transformación. Rectángulos siempre se transforman en paralelogramos.

Sin embargo, también es capaz de transformaciones no afines, que tienen la capacidad de transformar un rectángulo en cualquier cuadrilátero convexo SkiaSharp:

![](non-affine-images/nonaffinetransformexample.png "Un mapa de bits que se transforma en un cuadrilátero convexa")

Un cuadrilátero convexo es una cifra de cuatro lados con ángulos interior siempre menor que lados que no se cruzan y 180 grados.

No afines transforma los resultados cuando la tercera fila de la matriz de transformación se establece en valores distintos de 0, 0 y 1. El completo `SKMatrix` multiplicación es:

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z' |
              │ TransX  TransY  Persp2 │
</pre>

Las fórmulas de la transformación resultantes son:

x' = ScaleX·x SkewX·y + TransX

y' = SkewY·x ScaleY·y + TransY

z' = Persp0·x Persp1·y + Persp2

La regla de uso de una matriz de 3 por 3 para las transformaciones bidimensionales fundamental es que todo está en el plano donde Z es igual a 1. A menos que `Persp0` y `Persp1` son 0, y `Persp2` es igual a 1, la transformación ha movido las coordenadas Z desactivar ese plano.

Para restaurar una transformación bidimensional, las coordenadas deben devolverse a ese plano. Otro paso es necesario. La x', y', y z 'valores deben dividirse z":

x"= x' / z'

y"= s ' / z'

z"= z' / z' = 1

Estos se conocen como *coordenadas homogéneas* y se han desarrollado por matemático agosto Ferdinand Möbius, conocidos mucho mejor para su rareza topológica, la banda Möbius.

Si z' es 0, los resultados de la división en coordenadas de infinitos. De hecho, una de las motivaciones del Möbius para el desarrollo de coordenadas homogéneas era la capacidad para representar valores infinitos con un número finito.

Sin embargo, cuando se muestran los gráficos, desea evitar la representación algo con coordenadas que transforman los valores infinitos. No se representarán esas coordenadas. Todo el contenido en las proximidades esas coordenadas estará muy grandes y probablemente no visualmente coherente.

En esta ecuación, no desea que el valor de z' como cero:

z' = Persp0·x Persp1·y + Persp2

Por lo tanto, estos valores tienen algunas restricciones prácticas: 

El `Persp2` celda puede ser cero o no es cero. Si `Persp2` es cero, z' es cero para el punto (0, 0), y que normalmente no es deseable porque ese punto es muy común en gráficos bidimensionales. Si `Persp2` no es igual a cero, entonces no hay ninguna pérdida de generalidad si `Persp2` se fija en 1. Por ejemplo, si determina que `Persp2` debe ser 5, a continuación, puede simplemente divide todas las celdas de la matriz por 5, lo que hace que `Persp2` igual a 1, y el resultado será el mismo.

Por estos motivos, `Persp2` a menudo se fija en 1, que es el mismo valor en la matriz de identidad.

Por lo general, `Persp0` y `Persp1` son números pequeños. Por ejemplo, suponga que comienzan con una matriz de identidad pero conjunto `Persp0` a 0,01:

<pre>
| 1  0   0.01 |
| 0  1    0   |
| 0  0    1   |
</pre>

Las fórmulas de transformación son:

x' = x / (0.01·x + 1)

y' = s / (0.01·x + 1)

Ahora puede usar esta transformación para representar un cuadrado de 100 píxeles situado en el origen. Le mostramos cómo se transforman las cuatro esquinas:

(0, 0) → (0, 0)

(0, 100) → (0, 100)

(100, 0) → (50, 0)

(100, 100) → (50, 50)

Cuando x es 100 y, a continuación, el punto z' denominador es 2, por lo que las coordenadas x e y se reduce a la mitad. Se convierte en el lado derecho del cuadro de menor que el lado izquierdo:

![](non-affine-images/nonaffinetransform.png "Un cuadro sometido a una transformación no afín")

El `Persp` parte de estos nombres de la celda hace referencia a "perspectiva" porque el escorzo sugiere que la casilla se inclina ahora con el lado derecho más lejos del Visor.

El **perspectiva de pruebas** página le permite experimentar con valores de `Persp0` y `Pers1` para hacerse una idea de cómo funcionan. Valores razonables de estas celdas de la matriz son tan pequeños que el `Slider` en la plataforma Universal de Windows no puede correctamente controlarlos. Para alojar el problema UWP, los dos `Slider` elementos en el [ **TestPerspective.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml) deben inicializarse en el intervalo de – 1 a 1:

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

Los controladores de eventos para los controles deslizantes en el [ `TestPerspectivePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml.cs) archivo de código subyacente dividir los valores por 100 para que van entre –0.01 y 0,01. Además, el constructor se carga en un mapa de bits:

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

El `PaintSurface` controlador calcula un `SKMatrix` valor denominado `perspectiveMatrix` según los valores de estos dos controles deslizantes divididos por 100. Esto se combina con dos traducen las transformaciones que coloca el centro de esta transformación en el centro del mapa de bits:

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

[![](non-affine-images/testperspective-small.png "Captura de pantalla triple de la página de la perspectiva de pruebas")](non-affine-images/testperspective-large.png#lightbox "Triple captura de pantalla de la página de la perspectiva de pruebas")

Mientras experimenta con los controles deslizantes, encontrará que los valores más allá de 0.0066 o por debajo de –0.0066 hacen que la imagen esté repentinamente fracturado e incoherentes. El mapa de bits que se va a transformar es 300 píxeles al cuadrado. Se transforma en relación con su centro, por lo que las coordenadas del mapa de bits comprendido entre –150 a 150. Recuerde que el valor de z' es:

z' = Persp0·x Persp1·y + 1

Si `Persp0` o `Persp1` es mayor que 0.0066 o por debajo de –0.0066, a continuación, siempre hay algunas coordenadas del mapa de bits que da como resultado una z' valor de cero. Hace la división por cero y el procesamiento se convierte en un caos. Al utilizar las transformaciones no afines, desea evitar la representación nada con las coordenadas que provocan la división por cero.

Por lo general, no se puede establecer `Persp0` y `Persp1` de forma aislada. También a menudo es necesario establecer las demás celdas de la matriz para lograr determinados tipos de transformaciones no afines.

Es una dicha transformación no afín un *transformación estrechamiento*. Este tipo de transformación no afín conserva las dimensiones globales de un rectángulo pero estrecha un lado:

![](non-affine-images/tapertransform.png "Un cuadro sometido a una transformación de inclinación")

El [ `TaperTransform` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransform.cs) clase realiza un cálculo generalizado de una transformación no afín según estos parámetros:

- el tamaño de la imagen que se va a transformar, rectangular
- una enumeración que indica el lado del rectángulo que estrecha,
- otra enumeración que indica cómo estrecha, y
- la extensión de la estrecha.

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

Esta clase se utiliza en el **transformar estrechamiento** página. El archivo XAML crea una instancia de dos `Picker` elementos para seleccionar los valores de enumeración y un `Slider` para elegir la fracción de inclinación. El [ `PaintSurface` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransformPage.xaml.cs#L55) controlador combina la transformación cónica con dos traduzco las transformaciones para realizar la transformación relativa a la esquina superior izquierda del mapa de bits:

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

A continuación se muestran algunos ejemplos:

[![](non-affine-images/tapertransform-small.png "Captura de pantalla de la página de transformar estrechamiento triple")](non-affine-images/tapertransform-large.png#lightbox "Triple captura de pantalla de la página de transformar inclinación")

Otro tipo de transformaciones no afines generalizadas es un giro 3D, que se muestra en el siguiente artículo, [ **rotaciones 3D**](3d-rotation.md).

La transformación no afín puede transformar un rectángulo en cualquier cuadrilátero convexa. Esto se demuestra el **Mostrar matriz no afín** página. Es muy similar a la **Mostrar matriz afín** página desde la [ **transforma la matriz** ](matrix.md) artículo excepto que tiene una cuarta `TouchPoint` objeto para manipular el cuarto esquina del mapa de bits:

[![](non-affine-images/shownonaffinematrix-small.png "Captura de pantalla de la página se muestra la matriz no afín triple")](non-affine-images/shownonaffinematrix-large.png#lightbox "Triple captura de pantalla de la página se muestra la matriz no afines")

No intente hacer que un ángulo interior de una de las esquinas del mapa de bits mayor de 180 grados o hacer dos lados se cruzan, siempre que el sistema calcula correctamente la transformación usando este método desde el [ `ShowNonAffineMatrixPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowNonAffineMatrixPage.xaml.cs) clase:

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

Para facilitar el cálculo, este método obtiene la transformación total como un producto de tres transformaciones independientes, que son como perteneciente aquí con flechas que muestran cómo estas transformaciones modifican las cuatro esquinas del mapa de bits:

(0, 0) → → (0, 0) → (0, 0) (x 0, y0) (superior izquierda)

(0, H) → (0, 1) → (0, 1) → (x1, y1) (abajo a la izquierda)

(0, W) → → (1, 0) → (1, 0) (x 2, y2) (superior derecha)

(W, H) → → (1, 1) → (a, b) (x 3, y3) (esquina inferior derecha)

Las coordenadas finales a la derecha son los cuatro puntos asociados con los puntos de cuatro toque. Estas son las coordenadas finales de las esquinas del mapa de bits.

Anchura y altura representan el ancho y alto del mapa de bits. La primera transformación `S` simplemente se escala el mapa de bits a un cuadrado de 1 píxel. La segunda transformación es la transformación no afín `N`, y el tercero es la transformación afín `A`. Esa transformación afín se basa en tres puntos, por lo que al igual que la anteriormente afín [ `ComputeMatrix` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs#L68) método y no implica la cuarta fila con el (a, b) punto.

El `a` y `b` se calculan los valores para que la tercera transformación es afín. El código obtiene el inverso de la transformación afín y, a continuación, usa para asignar la esquina inferior derecha. Es el punto (a, b).

Otro uso de transformaciones no afines es imitar gráficos tridimensionales. En el siguiente artículo, [ **rotaciones 3D** ](3d-rotation.md) verá cómo girar un gráfico bidimensional en un espacio 3D.


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
