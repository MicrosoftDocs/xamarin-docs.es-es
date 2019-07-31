---
title: Manipulaciones táctiles
description: En este artículo se explica cómo usar las transformaciones de matriz para implementar arrastrar táctil, gestos y rotación y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B8DD2D-7392-4EC5-BFB0-6209407AD650
author: davidbritch
ms.author: dabritch
ms.date: 09/14/2018
ms.openlocfilehash: 407fe78618c5e5fcd8732d9ff3cea50561ca78f3
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655549"
---
# <a name="touch-manipulations"></a>Manipulaciones táctiles

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Transforma la matriz de uso para implementar arrastrar táctil, gestos y rotación_

En entornos de multitoque, como los de dispositivos móviles, los usuarios normalmente usan los dedos para manipular objetos en la pantalla. Gestos comunes, como un arrastre con un solo dedo y casos de emergencia con dos dedos pueden mover y escalar los objetos o incluso girarlas. Estos gestos generalmente se implementan mediante matrices de transformación y, en este artículo se muestra cómo hacerlo.

![](touch-images/touchmanipulationsexample.png "Un mapa de bits sujeta a la traslación, escala y giro")

Todos los ejemplos se muestra a continuación usan el efecto de seguimiento de toque Xamarin.Forms presentado en el artículo [ **invocar eventos de efectos**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md).

## <a name="dragging-and-translation"></a>Arrastrar y traducción

Una de las aplicaciones más importantes de transformaciones de matriz es el procesamiento de toque. Una sola [ `SKMatrix` ](xref:SkiaSharp.SKMatrix) valor puede consolidar una serie de operaciones de toque. 

Para arrastrar un solo dedo, el `SKMatrix` valor realiza la traducción. Esto se muestra en el **arrastrando el mapa de bits** página. El archivo XAML crea una instancia de un `SKCanvasView` en un objeto Xamarin.Forms `Grid`. Un `TouchEffect` objeto se ha agregado a la `Effects` colección eso `Grid`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Transforms.BitmapDraggingPage"
             Title="Bitmap Dragging">
    
    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</ContentPage>
```

En teoría, el `TouchEffect` objeto podría agregarse directamente a la `Effects` colección de los `SKCanvasView`, pero que no funciona en todas las plataformas. Dado que el `SKCanvasView` es el mismo tamaño que el `Grid` en esta configuración, adjuntarlo a la `Grid` funciona igual de bien.

El archivo de código subyacente se carga en un recurso de mapa de bits en su constructor y lo muestra en el `PaintSurface` controlador:

```csharp
public partial class BitmapDraggingPage : ContentPage
{
    // Bitmap and matrix for display
    SKBitmap bitmap;
    SKMatrix matrix = SKMatrix.MakeIdentity();
    ···

    public BitmapDraggingPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap
        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, new SKPoint());
    }
}
```

Sin ningún código adicional, la `SKMatrix` valor siempre es la matriz de identidad, y no tendría ningún efecto en la presentación del mapa de bits. El objetivo de la `OnTouchEffectAction` controlador establecido en el archivo XAML consiste en modificar el valor de matriz para reflejar las manipulaciones táctiles.

El `OnTouchEffectAction` controlador comienza al convertir el Xamarin.Forms `Point` valor en un SkiaSharp `SKPoint` valor. Esto es solo cuestión de escalado automático basado en el `Width` y `Height` propiedades de `SKCanvasView` (que son unidades independientes del dispositivo) y el `CanvasSize` propiedad, que es en unidades de píxeles:

```csharp
public partial class BitmapDraggingPage : ContentPage
{
    ···
    // Touch information
    long touchId = -1;
    SKPoint previousPoint;
    ···
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point = 
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                // Find transformed bitmap rectangle
                SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
                rect = matrix.MapRect(rect);

                // Determine if the touch was within that rectangle
                if (rect.Contains(point))
                {
                    touchId = args.Id;
                    previousPoint = point;
                }
                break;

            case TouchActionType.Moved:
                if (touchId == args.Id)
                {
                    // Adjust the matrix for the new position
                    matrix.TransX += point.X - previousPoint.X;
                    matrix.TransY += point.Y - previousPoint.Y;
                    previousPoint = point;
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                touchId = -1;
                break;
        }
    }
    ···
}
```

Cuando un dedo toca la pantalla, un evento de tipo por primera vez `TouchActionType.Pressed` se activa. La primera tarea consiste en determinar si el dedo toca el mapa de bits. Esta tarea se suele denominar _la prueba de posicionamiento_. En este caso, la prueba de posicionamiento puede realizarse mediante la creación de un `SKRect` correspondiente al mapa de bits, al aplicar la transformación de matriz a él con el valor `MapRect`y, a continuación, determinar si el punto de toque se encuentra dentro del rectángulo transformado.

Si es así, el `touchId` campo se establece en el Id. de toque, y se guarda la posición del dedo.

Para el `TouchActionType.Moved` eventos, los factores de traslación de los `SKMatrix` se ajustan en función de valor en la posición actual del dedo y la nueva posición del dedo. Que se guarda la nueva posición para la próxima vez y el `SKCanvasView` se invalida.

Mientras experimenta con este programa, tenga en cuenta que solo puede arrastrar el mapa de bits cuando el dedo toca un área donde se muestra el mapa de bits. Aunque esta restricción no es muy importante para este programa, es muy importante al manipular varios mapas de bits.

## <a name="pinching-and-scaling"></a>Reducir y escalar

¿Qué desea que suceda cuando dos dedos tocar el mapa de bits? Si mueve los dos dedos en paralelo, probablemente desee el mapa de bits para mover junto con los dedos. Si los dos dedos realizan casos de emergencia o ajustar la operación, que es posible que desee el mapa de bits girado (para que se describen en la sección siguiente) o escalar. Al escalar un mapa de bits, resulta más conveniente para los dos dedos y permanecen en la misma posición en relación con el mapa de bits y para el mapa de bits que se escalan en consecuencia.

Control a la vez dos dedos parece complicado, pero tenga en cuenta que el `TouchAction` controlador sólo recibe información acerca de un solo dedo a la vez. Si el mapa de bits manipulan dos dedos, para cada evento, un solo dedo ha cambiado la posición, pero el otro no ha cambiado. En el **escalar mapas de bits** código de página siguiente, se llama el dedo que no se ha cambiado la posición del _pivot_ punto porque la transformación es relativo a ese punto.

Una diferencia entre este programa y el programa anterior es ese toque varios que identificadores deben guardarse. Se usa un diccionario para este propósito, donde el Id. de toque es la clave del diccionario y el valor del diccionario es la posición actual de ese dedo:

```csharp
public partial class BitmapScalingPage : ContentPage
{
    ···
    // Touch information
    Dictionary<long, SKPoint> touchDictionary = new Dictionary<long, SKPoint>();
    ···
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                // Find transformed bitmap rectangle
                SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
                rect = matrix.MapRect(rect);

                // Determine if the touch was within that rectangle
                if (rect.Contains(point) && !touchDictionary.ContainsKey(args.Id))
                {
                    touchDictionary.Add(args.Id, point);
                }
                break;

            case TouchActionType.Moved:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    // Single-finger drag
                    if (touchDictionary.Count == 1)
                    {
                        SKPoint prevPoint = touchDictionary[args.Id];

                        // Adjust the matrix for the new position
                        matrix.TransX += point.X - prevPoint.X;
                        matrix.TransY += point.Y - prevPoint.Y;
                        canvasView.InvalidateSurface();
                    }
                    // Double-finger scale and drag
                    else if (touchDictionary.Count >= 2)
                    {
                        // Copy two dictionary keys into array
                        long[] keys = new long[touchDictionary.Count];
                        touchDictionary.Keys.CopyTo(keys, 0);

                        // Find index of non-moving (pivot) finger
                        int pivotIndex = (keys[0] == args.Id) ? 1 : 0;

                        // Get the three points involved in the transform
                        SKPoint pivotPoint = touchDictionary[keys[pivotIndex]];
                        SKPoint prevPoint = touchDictionary[args.Id];
                        SKPoint newPoint = point;

                        // Calculate two vectors
                        SKPoint oldVector = prevPoint - pivotPoint;
                        SKPoint newVector = newPoint - pivotPoint;

                        // Scaling factors are ratios of those
                        float scaleX = newVector.X / oldVector.X;
                        float scaleY = newVector.Y / oldVector.Y;

                        if (!float.IsNaN(scaleX) && !float.IsInfinity(scaleX) &&
                            !float.IsNaN(scaleY) && !float.IsInfinity(scaleY))
                        {
                            // If something bad hasn't happened, calculate a scale and translation matrix
                            SKMatrix scaleMatrix = 
                                SKMatrix.MakeScale(scaleX, scaleY, pivotPoint.X, pivotPoint.Y);

                            SKMatrix.PostConcat(ref matrix, scaleMatrix);
                            canvasView.InvalidateSurface();
                        }
                    }

                    // Store the new point in the dictionary
                    touchDictionary[args.Id] = point;
                }

                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    touchDictionary.Remove(args.Id);
                }
                break;
        }
    }
    ···
}
```

El control de la `Pressed` acción es prácticamente el mismo que el Id. de programa, excepto que el anterior y puntos de contacto se agregan al diccionario. El `Released` y `Cancelled` acciones quitan la entrada del diccionario.

El control para el `Moved` acción es más compleja, sin embargo. Si hay un solo dedo, el procesamiento es casi el mismo que el programa anterior. Para dos o más dedos, el programa también debe obtener la información del diccionario de que impliquen el dedo que no se mueve. Esto lo consigue copiar las claves de diccionario en una matriz y, a continuación, comparar la primera clave con el Id. del dedo que se va a mover. Que permite que el programa obtener el punto de pivote correspondiente al dedo que no se mueve.

A continuación, el sistema calcula dos vectores de la nueva posición del dedo en relación con el punto de pivote y la posición anterior del dedo en relación con el punto de pivote. Las proporciones de los vectores son factores de escala. Dado que la división por cero es una posibilidad, estos deben comprobarse para valores infinitos o NaN (no un número). Si todo es correcto, una transformación de escala se concatena con el `SKMatrix` guarda como un campo de valor.

Mientras experimenta con esta página, observará que puede arrastrar el mapa de bits con uno o dos dedos, o ampliarlo con dos dedos. El escalado es _anisotrópico_, lo que significa que el escalado puede ser diferente en las direcciones horizontales y vertical. Esto distorsiona la relación de aspecto, pero también le permite girar el mapa de bits para crear una imagen reflejada. También es posible que descubra que puede reducir el mapa de bits a una dimensión de cero y desaparece. En el código de producción, desea protegerse contra esto.

## <a name="two-finger-rotation"></a>Rotación con dos dedos

El **girar el mapa de bits** página permite utilizar dos dedos para isótropo escala o rotación. El mapa de bits siempre conserva su relación de aspecto correcta. Utilizando dos dedos para la rotación y escala anisotrópico no funciona muy bien porque el movimiento de los dedos es muy similar para ambas tareas.

La primera gran diferencia en este programa es la lógica de la prueba de posicionamiento. Los programas anteriores usan el `Contains` método `SKRect` para determinar si el punto de toque está dentro del rectángulo transformado que se corresponde con el mapa de bits. Pero si el usuario manipula el mapa de bits, podría ser el mapa de bits girado y `SKRect` no puede representar correctamente un rectángulo girado. Es posible que temen que debe implementar bastante compleja geometría analítica en ese caso la lógica de la prueba de posicionamiento.

Sin embargo, hay un acceso directo disponible: Determinar si un punto está dentro de los límites de un rectángulo transformado es el mismo que determinar si un punto de transformación inversa se encuentra dentro de los límites del rectángulo no transformado. Eso es mucho más fácil de cálculo y la lógica puede seguir usando la cómoda `Contains` método:

```csharp
public partial class BitmapRotationPage : ContentPage
{
    ···
    // Touch information
    Dictionary<long, SKPoint> touchDictionary = new Dictionary<long, SKPoint>();
    ···
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!touchDictionary.ContainsKey(args.Id))
                {
                    // Invert the matrix
                    if (matrix.TryInvert(out SKMatrix inverseMatrix))
                    {
                        // Transform the point using the inverted matrix
                        SKPoint transformedPoint = inverseMatrix.MapPoint(point);

                        // Check if it's in the untransformed bitmap rectangle
                        SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);

                        if (rect.Contains(transformedPoint))
                        {
                            touchDictionary.Add(args.Id, point);
                        }
                    }
                }
                break;

            case TouchActionType.Moved:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    // Single-finger drag
                    if (touchDictionary.Count == 1)
                    {
                        SKPoint prevPoint = touchDictionary[args.Id];

                        // Adjust the matrix for the new position
                        matrix.TransX += point.X - prevPoint.X;
                        matrix.TransY += point.Y - prevPoint.Y;
                        canvasView.InvalidateSurface();
                    }
                    // Double-finger rotate, scale, and drag
                    else if (touchDictionary.Count >= 2)
                    {
                        // Copy two dictionary keys into array
                        long[] keys = new long[touchDictionary.Count];
                        touchDictionary.Keys.CopyTo(keys, 0);

                        // Find index non-moving (pivot) finger
                        int pivotIndex = (keys[0] == args.Id) ? 1 : 0;

                        // Get the three points in the transform
                        SKPoint pivotPoint = touchDictionary[keys[pivotIndex]];
                        SKPoint prevPoint = touchDictionary[args.Id];
                        SKPoint newPoint = point;

                        // Calculate two vectors
                        SKPoint oldVector = prevPoint - pivotPoint;
                        SKPoint newVector = newPoint - pivotPoint;

                        // Find angles from pivot point to touch points
                        float oldAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
                        float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

                        // Calculate rotation matrix
                        float angle = newAngle - oldAngle;
                        SKMatrix touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

                        // Effectively rotate the old vector
                        float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
                        oldVector.X = magnitudeRatio * newVector.X;
                        oldVector.Y = magnitudeRatio * newVector.Y;

                        // Isotropic scaling!
                        float scale = Magnitude(newVector) / Magnitude(oldVector);

                        if (!float.IsNaN(scale) && !float.IsInfinity(scale))
                        {
                            SKMatrix.PostConcat(ref touchMatrix,
                                SKMatrix.MakeScale(scale, scale, pivotPoint.X, pivotPoint.Y));

                            SKMatrix.PostConcat(ref matrix, touchMatrix);
                            canvasView.InvalidateSurface();
                        }
                    }

                    // Store the new point in the dictionary
                    touchDictionary[args.Id] = point;
                }

                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    touchDictionary.Remove(args.Id);
                }
                break;
        }
    }

    float Magnitude(SKPoint point)
    {
        return (float)Math.Sqrt(Math.Pow(point.X, 2) + Math.Pow(point.Y, 2));
    }
    ···
}
```

La lógica para el `Moved` evento empieza igual que el programa anterior. Dos vectores denominados `oldVector` y `newVector` se calcula basándose en el anterior y el punto actual del dedo móvil y el punto de pivote del dedo unmoving. Pero, a continuación, se determinan los ángulos de estos vectores, y la diferencia es el ángulo de giro.

El escalado también podría estar implicado, por lo que el vector antiguo se gira basándose en el ángulo de giro. El tamaño relativo de los dos vectores ahora es el factor de escala. Tenga en cuenta que el mismo `scale` valor se utiliza para horizontal y escalado vertical para que el escalado es isótropo. El `matrix` campo se ajusta con la matriz de rotación y una matriz de escala.

Si la aplicación necesita para implementar táctil de procesamiento para un mapa de bits única (u otro objeto), puede adaptar el código de estos tres muestras para su propia aplicación. Pero si necesita implementar táctil de procesamiento de varios mapas de bits, probablemente quiera encapsular estos touch operaciones en otras clases.

## <a name="encapsulating-the-touch-operations"></a>Encapsula las operaciones de interacción

El **manipulación táctil** página muestra la manipulación táctil de un mapa de bits única, pero usando otros archivos que encapsulan gran parte de la lógica mostrada anteriormente. El primero de estos archivos es el [ `TouchManipulationMode` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationMode.cs) enumeración, que indica los distintos tipos de manipulación táctil implementado por el código seguirá viendo:

```csharp
enum TouchManipulationMode
{
    None,
    PanOnly,
    IsotropicScale,     // includes panning
    AnisotropicScale,   // includes panning
    ScaleRotate,        // implies isotropic scaling
    ScaleDualRotate     // adds one-finger rotation
}
```

`PanOnly` es un arrastre de un solo dedo que se implementa con la traducción. Todas las demás opciones también incluyen movimiento panorámico pero implican dos dedos: `IsotropicScale` es una operación de acercar los dedos que da como resultado el objeto que escala equitativamente en dirección horizontal y vertical. `AnisotropicScale` permite la escala desiguales.

El `ScaleRotate` opción está destinada a escala con dos dedos y rotación. El escalado es isótropo. Como se mencionó anteriormente, implementar con dos dedos rotación con un escalado anisotrópico es problemático porque los movimientos del dedo están prácticamente el mismo.

El `ScaleDualRotate` opción agrega la rotación con un solo dedo. Cuando un solo dedo arrastra el objeto, el objeto arrastrado en primer lugar se gira en torno a su centro para que el centro del objeto se alinee con el vector de arrastrar.

El [ **TouchManipulationPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml) archivo incluye una `Picker` con los miembros de la `TouchManipulationMode` enumeración:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             xmlns:local="clr-namespace:SkiaSharpFormsDemos.Transforms"
             x:Class="SkiaSharpFormsDemos.Transforms.TouchManipulationPage"
             Title="Touch Manipulation">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker Title="Touch Mode"
                Grid.Row="0"
                SelectedIndexChanged="OnTouchModePickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:TouchManipulationMode}">
                    <x:Static Member="local:TouchManipulationMode.None" />
                    <x:Static Member="local:TouchManipulationMode.PanOnly" />
                    <x:Static Member="local:TouchManipulationMode.IsotropicScale" />
                    <x:Static Member="local:TouchManipulationMode.AnisotropicScale" />
                    <x:Static Member="local:TouchManipulationMode.ScaleRotate" />
                    <x:Static Member="local:TouchManipulationMode.ScaleDualRotate" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                4
            </Picker.SelectedIndex>
        </Picker>
        
        <Grid BackgroundColor="White"
              Grid.Row="1">
            
            <skia:SKCanvasView x:Name="canvasView"
                               PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>
    </Grid>
</ContentPage>
```

Hacia la parte inferior es un `SKCanvasView` y un `TouchEffect` adjunto a la celda única `Grid` que lo rodea.

El [ **TouchManipulationPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml.cs) archivo de código subyacente tiene un `bitmap` campo pero no es de tipo `SKBitmap`. El tipo es `TouchManipulationBitmap` (una clase que se verá en breve):

```csharp
public partial class TouchManipulationPage : ContentPage
{
    TouchManipulationBitmap bitmap;
    ...

    public TouchManipulationPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.MountainClimbers.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            SKBitmap bitmap = SKBitmap.Decode(stream);
            this.bitmap = new TouchManipulationBitmap(bitmap);
            this.bitmap.TouchManager.Mode = TouchManipulationMode.ScaleRotate;
        }
    }
    ...
}
```

El constructor crea un `TouchManipulationBitmap` objeto, pasando al constructor un `SKBitmap` obtenido de un recurso incrustado. El constructor concluye estableciendo el `Mode` propiedad de la `TouchManager` propiedad de la `TouchManipulationBitmap` objeto a un miembro de la `TouchManipulationMode` enumeración.

El `SelectedIndexChanged` controlador para el `Picker` también establece esto `Mode` propiedad:

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    void OnTouchModePickerSelectedIndexChanged(object sender, EventArgs args)
    {
        if (bitmap != null)
        {
            Picker picker = (Picker)sender;
            bitmap.TouchManager.Mode = (TouchManipulationMode)picker.SelectedItem;
        }
    }
    ...
}
```

El `TouchAction` controlador de la `TouchEffect` crea una instancia en el archivo XAML llama a dos métodos en `TouchManipulationBitmap` denominado `HitTest` y `ProcessTouchEvent`:

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    List<long> touchIds = new List<long>();
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (bitmap.HitTest(point))
                {
                    touchIds.Add(args.Id);
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    break;
                }
                break;

            case TouchActionType.Moved:
                if (touchIds.Contains(args.Id))
                {
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchIds.Contains(args.Id))
                {
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    touchIds.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
}
```

Si el `HitTest` devuelve del método `true` &mdash; lo que significa que un dedo tocó la pantalla del área ocupada por el mapa de bits &mdash; , a continuación, el Id. de toque se agrega a la `TouchIds` colección. Este identificador representa la secuencia de eventos táctiles para ese dedo hasta que se levanta el dedo desde la pantalla. Si varios dedos toca el mapa de bits, el `touchIds` colección contiene un Id. de toque para cada dedo.

El `TouchAction` controlador también llama a la `ProcessTouchEvent` clase `TouchManipulationBitmap`. Aquí es donde algunos (aunque no todos) de la interacción real se produce el procesamiento.

El [ `TouchManipulationBitmap` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationBitmap.cs) clase es una clase contenedora para `SKBitmap` que contiene código para representar el mapa de bits y procesar eventos de toque. Funciona junto con más generalizado de código en un `TouchManipulationManager` clase (que verá en breve).

El `TouchManipulationBitmap` constructor guarda el `SKBitmap` y crea una instancia de dos propiedades, el `TouchManager` propiedad de tipo `TouchManipulationManager` y `Matrix` propiedad de tipo `SKMatrix`:

```csharp
class TouchManipulationBitmap
{
    SKBitmap bitmap;
    ...

    public TouchManipulationBitmap(SKBitmap bitmap)
    {
        this.bitmap = bitmap;
        Matrix = SKMatrix.MakeIdentity();

        TouchManager = new TouchManipulationManager
        {
            Mode = TouchManipulationMode.ScaleRotate
        };
    }

    public TouchManipulationManager TouchManager { set; get; }

    public SKMatrix Matrix { set; get; }
    ...
}
```

Esto `Matrix` propiedad es la transformación acumulada resultante de toda la actividad de toque. Como verá, cada evento de toque se resuelve en una matriz, que, a continuación, se concatena con el `SKMatrix` valor almacenado por el `Matrix` propiedad.

El `TouchManipulationBitmap` objeto dibuja a sí mismo su `Paint` método. El argumento es un `SKCanvas` objeto. Esto `SKCanvas` podrían tener ya una transformación aplicada a él, por lo que la `Paint` método concatena el `Matrix` propiedad asociada con el mapa de bits a la transformación existente y restaura el lienzo cuando haya terminado:

```csharp
class TouchManipulationBitmap
{
    ...
    public void Paint(SKCanvas canvas)
    {
        canvas.Save();
        SKMatrix matrix = Matrix;
        canvas.Concat(ref matrix);
        canvas.DrawBitmap(bitmap, 0, 0);
        canvas.Restore();
    }
    ...
}
```

El `HitTest` devuelve del método `true` si el usuario toca la pantalla en un punto dentro de los límites del mapa de bits. Esto utiliza la lógica que se mostraron anteriormente en el **rotación del mapa de bits** página:

```csharp
class TouchManipulationBitmap
{
    ...
    public bool HitTest(SKPoint location)
    {
        // Invert the matrix
        SKMatrix inverseMatrix;

        if (Matrix.TryInvert(out inverseMatrix))
        {
            // Transform the point using the inverted matrix
            SKPoint transformedPoint = inverseMatrix.MapPoint(location);

            // Check if it's in the untransformed bitmap rectangle
            SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
            return rect.Contains(transformedPoint);
        }
        return false;
    }
    ...
}
```

El segundo método público en `TouchManipulationBitmap` es `ProcessTouchEvent`. Cuando se llama a este método, ya se determinó que el evento de toque pertenece a este mapa de bits determinado. El método no mantiene un diccionario de [ `TouchManipulationInfo` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationInfo.cs) objetos, que es simplemente el punto anterior y el nuevo punto de cada dedo:

```csharp
class TouchManipulationInfo
{
    public SKPoint PreviousPoint { set; get; }

    public SKPoint NewPoint { set; get; }
}
```

Este es el diccionario y la `ProcessTouchEvent` propio método:

```csharp
class TouchManipulationBitmap
{
    ...
    Dictionary<long, TouchManipulationInfo> touchDictionary =
        new Dictionary<long, TouchManipulationInfo>();
    ...
    public void ProcessTouchEvent(long id, TouchActionType type, SKPoint location)
    {
        switch (type)
        {
            case TouchActionType.Pressed:
                touchDictionary.Add(id, new TouchManipulationInfo
                {
                    PreviousPoint = location,
                    NewPoint = location
                });
                break;

            case TouchActionType.Moved:
                TouchManipulationInfo info = touchDictionary[id];
                info.NewPoint = location;
                Manipulate();
                info.PreviousPoint = info.NewPoint;
                break;

            case TouchActionType.Released:
                touchDictionary[id].NewPoint = location;
                Manipulate();
                touchDictionary.Remove(id);
                break;

            case TouchActionType.Cancelled:
                touchDictionary.Remove(id);
                break;
        }
    }
    ...
}
```

En el `Moved` y `Released` eventos, las llamadas al método `Manipulate`. En estos momentos, el `touchDictionary` contiene uno o varios `TouchManipulationInfo` objetos. Si el `touchDictionary` contiene un elemento, es probable que el `PreviousPoint` y `NewPoint` valores no son iguales y representan el movimiento de un dedo. Si varios dedos tocan el mapa de bits, el diccionario contiene más de un elemento, pero solo uno de estos elementos tiene distintos `PreviousPoint` y `NewPoint` valores. El resto tienen las mismas `PreviousPoint` y `NewPoint` valores.

Esto es importante: El `Manipulate` método puede suponer que está procesando el movimiento de un solo dedo. En el momento de esta llamada Mover ninguno de los otros dedos y si realmente está moviendo (ya que es probable), se procesarán los movimientos en las futuras llamadas a `Manipulate`.

El `Manipulate` método copia primero el diccionario en una matriz para su comodidad. Omite cualquier cosa que no sean las dos primeras entradas. Si más de dos dedos están intentando manipular el mapa de bits, se omiten los demás. `Manipulate` es el último miembro de `TouchManipulationBitmap`:

```csharp
class TouchManipulationBitmap
{
    ...
    void Manipulate()
    {
        TouchManipulationInfo[] infos = new TouchManipulationInfo[touchDictionary.Count];
        touchDictionary.Values.CopyTo(infos, 0);
        SKMatrix touchMatrix = SKMatrix.MakeIdentity();

        if (infos.Length == 1)
        {
            SKPoint prevPoint = infos[0].PreviousPoint;
            SKPoint newPoint = infos[0].NewPoint;
            SKPoint pivotPoint = Matrix.MapPoint(bitmap.Width / 2, bitmap.Height / 2);

            touchMatrix = TouchManager.OneFingerManipulate(prevPoint, newPoint, pivotPoint);
        }
        else if (infos.Length >= 2)
        {
            int pivotIndex = infos[0].NewPoint == infos[0].PreviousPoint ? 0 : 1;
            SKPoint pivotPoint = infos[pivotIndex].NewPoint;
            SKPoint newPoint = infos[1 - pivotIndex].NewPoint;
            SKPoint prevPoint = infos[1 - pivotIndex].PreviousPoint;

            touchMatrix = TouchManager.TwoFingerManipulate(prevPoint, newPoint, pivotPoint);
        }

        SKMatrix matrix = Matrix;
        SKMatrix.PostConcat(ref matrix, touchMatrix);
        Matrix = matrix;
    }
}
```

Si manipula el mapa de bits, un solo dedo `Manipulate` llamadas la `OneFingerManipulate` método de la `TouchManipulationManager` objeto. Para dos dedos, llama a `TwoFingerManipulate`. Los argumentos de estos métodos son los mismos: el `prevPoint` y `newPoint` argumentos representan el dedo que se mueven. Pero la `pivotPoint` argumento es diferente para las dos llamadas:

Para la manipulación con un solo dedo, el `pivotPoint` es el centro del mapa de bits. Esto es para permitir la rotación con un solo dedo. Para la manipulación con dos dedos, el evento indica que el movimiento de un solo dedo, para que el `pivotPoint` es el dedo que no se mueve.

En ambos casos, `TouchManipulationManager` devuelve un `SKMatrix` valor, que el método se concatena con el actual `Matrix` propiedad que `TouchManipulationPage` se usa para representar el mapa de bits.

`TouchManipulationManager` se ha generalizado y no se utiliza ningún otro archivo excepto `TouchManipulationMode`. Puede utilizar esta clase sin cambios en sus propias aplicaciones. Define una propiedad única de tipo `TouchManipulationMode`:

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }
    ...
}
```


Sin embargo, probablemente deseará evitar la `AnisotropicScale` opción. Es muy fácil con esta opción para manipular el mapa de bits para que uno de los factores de escala se convierte en cero. Esto hace que el mapa de bits desaparecen de la visión, nunca a devolver. Si realmente necesita escalado anisotrópico, desea mejorar la lógica para evitar resultados no deseados.

`TouchManipulationManager` hace uso de vectores, pero dado que no hay ningún `SKVector` estructura de SkiaSharp, `SKPoint` se usa en su lugar. `SKPoint` admite que el operador de resta y el resultado se pueden tratar como un vector. La lógica específica solo vector que fue necesario agregar es un `Magnitude` cálculo:

```csharp
class TouchManipulationManager
{
    ...
    float Magnitude(SKPoint point)
    {
        return (float)Math.Sqrt(Math.Pow(point.X, 2) + Math.Pow(point.Y, 2));
    }
}
```

Cada vez que se ha seleccionado la rotación, tanto los métodos de manipulación de un solo dedo y con dos dedos controlen la rotación primero. Si se detecta cualquier rotación, se quita eficazmente el componente de rotación. Lo que falta se interpreta como movimiento panorámico y el escalado.

Este es el `OneFingerManipulate` método. Si no se habilitó la rotación con un solo dedo, entonces la lógica es simple &mdash; simplemente utiliza los puntos anteriores y nuevas para construir un vector denominado `delta` que corresponde exactamente a la traducción. Con una rotación con un solo dedo habilitada, el método usa ángulos desde el punto de pivote (el centro del mapa de bits) para los puntos anteriores y nuevas para construir una matriz de rotación:

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }

    public SKMatrix OneFingerManipulate(SKPoint prevPoint, SKPoint newPoint, SKPoint pivotPoint)
    {
        if (Mode == TouchManipulationMode.None)
        {
            return SKMatrix.MakeIdentity();
        }

        SKMatrix touchMatrix = SKMatrix.MakeIdentity();
        SKPoint delta = newPoint - prevPoint;

        if (Mode == TouchManipulationMode.ScaleDualRotate)  // One-finger rotation
        {
            SKPoint oldVector = prevPoint - pivotPoint;
            SKPoint newVector = newPoint - pivotPoint;

            // Avoid rotation if fingers are too close to center
            if (Magnitude(newVector) > 25 && Magnitude(oldVector) > 25)
            {
                float prevAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
                float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

                // Calculate rotation matrix
                float angle = newAngle - prevAngle;
                touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

                // Effectively rotate the old vector
                float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
                oldVector.X = magnitudeRatio * newVector.X;
                oldVector.Y = magnitudeRatio * newVector.Y;

                // Recalculate delta
                delta = newVector - oldVector;
            }
        }

        // Multiply the rotation matrix by a translation matrix
        SKMatrix.PostConcat(ref touchMatrix, SKMatrix.MakeTranslation(delta.X, delta.Y));

        return touchMatrix;
    }
    ...
}
```

En el `TwoFingerManipulate` método, el punto de pivote es la posición del dedo que no se mueve en este evento táctil en particular. La rotación es muy similar a la rotación con un solo dedo y, a continuación, el vector denominado `oldVector` (según el punto anterior) se ajusta la rotación. El movimiento restante se interpreta como el escalado:

```csharp
class TouchManipulationManager
{
    ...
    public SKMatrix TwoFingerManipulate(SKPoint prevPoint, SKPoint newPoint, SKPoint pivotPoint)
    {
        SKMatrix touchMatrix = SKMatrix.MakeIdentity();
        SKPoint oldVector = prevPoint - pivotPoint;
        SKPoint newVector = newPoint - pivotPoint;

        if (Mode == TouchManipulationMode.ScaleRotate ||
            Mode == TouchManipulationMode.ScaleDualRotate)
        {
            // Find angles from pivot point to touch points
            float oldAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
            float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

            // Calculate rotation matrix
            float angle = newAngle - oldAngle;
            touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

            // Effectively rotate the old vector
            float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
            oldVector.X = magnitudeRatio * newVector.X;
            oldVector.Y = magnitudeRatio * newVector.Y;
        }

        float scaleX = 1;
        float scaleY = 1;

        if (Mode == TouchManipulationMode.AnisotropicScale)
        {
            scaleX = newVector.X / oldVector.X;
            scaleY = newVector.Y / oldVector.Y;

        }
        else if (Mode == TouchManipulationMode.IsotropicScale ||
                 Mode == TouchManipulationMode.ScaleRotate ||
                 Mode == TouchManipulationMode.ScaleDualRotate)
        {
            scaleX = scaleY = Magnitude(newVector) / Magnitude(oldVector);
        }

        if (!float.IsNaN(scaleX) && !float.IsInfinity(scaleX) &&
            !float.IsNaN(scaleY) && !float.IsInfinity(scaleY))
        {
            SKMatrix.PostConcat(ref touchMatrix,
                SKMatrix.MakeScale(scaleX, scaleY, pivotPoint.X, pivotPoint.Y));
        }

        return touchMatrix;
    }
    ...
}
```

Observará que no hay ninguna conversión explícita en este método. Sin embargo, tanto el `MakeRotation` y `MakeScale` métodos se basan en el punto de pivote y que incluye una conversión implícita. Si usa dos dedos en el mapa de bits y arrástrelos en la misma dirección, `TouchManipulation` obtendrán una serie de eventos de toque, alternando entre los dos dedos. Como cada dedo se mueve en relación con los otros, escala o rotación de resultados, pero es negada por el movimiento del otro dedo y el resultado es la traducción.

La única parte restante de la **manipulación táctil** página es la `PaintSurface` controlador en el `TouchManipulationPage` archivo de código subyacente. Esto llama a la `Paint` método de la `TouchManipulationBitmap`, que aplica a la matriz que representa la actividad táctil acumulada:

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    MatrixDisplay matrixDisplay = new MatrixDisplay();
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap
        bitmap.Paint(canvas);

        // Display the matrix in the lower-right corner
        SKSize matrixSize = matrixDisplay.Measure(bitmap.Matrix);

        matrixDisplay.Paint(canvas, bitmap.Matrix,
            new SKPoint(info.Width - matrixSize.Width,
                        info.Height - matrixSize.Height));
    }
}
```

El `PaintSurface` controlador termina mostrando un `MatrixDisplay` objeto muestra la matriz acumulado táctil:

[![](touch-images/touchmanipulation-small.png "Captura de pantalla triple de la página de la manipulación táctil")](touch-images/touchmanipulation-large.png#lightbox "Triple captura de pantalla de la página de la manipulación táctil")

## <a name="manipulating-multiple-bitmaps"></a>Manipular varios mapas de bits

Una de las ventajas de aislar el código de procesamiento de toque en clases como `TouchManipulationBitmap` y `TouchManipulationManager` es la capacidad de volver a usar estas clases en un programa que permite al usuario manipular varios mapas de bits.

El **vista de mapa de bits de dispersión** página muestra cómo hacerlo. En lugar de definir un campo de tipo `TouchManipulationBitmap`, [ `BitmapScatterPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BitmapScatterViewPage.xaml.cs) clase define un `List` de objetos de mapa de bits:

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    List<TouchManipulationBitmap> bitmapCollection =
        new List<TouchManipulationBitmap>();
    ...
    public BitmapScatterViewPage()
    {
        InitializeComponent();

        // Load in all the available bitmaps
        Assembly assembly = GetType().GetTypeInfo().Assembly;
        string[] resourceIDs = assembly.GetManifestResourceNames();
        SKPoint position = new SKPoint();

        foreach (string resourceID in resourceIDs)
        {
            if (resourceID.EndsWith(".png") ||
                resourceID.EndsWith(".jpg"))
            {
                using (Stream stream = assembly.GetManifestResourceStream(resourceID))
                {
                    SKBitmap bitmap = SKBitmap.Decode(stream);
                    bitmapCollection.Add(new TouchManipulationBitmap(bitmap)
                    {
                        Matrix = SKMatrix.MakeTranslation(position.X, position.Y),
                    });
                    position.X += 100;
                    position.Y += 100;
                }
            }
        }
    }
    ...
}
```

El constructor se carga en todos los mapas de bits disponibles como recursos incrustados y los agrega a la `bitmapCollection`. Tenga en cuenta que el `Matrix` propiedad se inicializa en cada `TouchManipulationBitmap` de objeto, por lo que las esquinas superior izquierda de cada mapa de bits se desplazan por 100 píxeles.

El `BitmapScatterView` página también debe controlar los eventos de toque de varios mapas de bits. En lugar de definir un `List` de touch Id. de manipulan actualmente `TouchManipulationBitmap` objetos, este programa requiere un diccionario:

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    ...
    Dictionary<long, TouchManipulationBitmap> bitmapDictionary =
       new Dictionary<long, TouchManipulationBitmap>();
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                for (int i = bitmapCollection.Count - 1; i >= 0; i--)
                {
                    TouchManipulationBitmap bitmap = bitmapCollection[i];

                    if (bitmap.HitTest(point))
                    {
                        // Move bitmap to end of collection
                        bitmapCollection.Remove(bitmap);
                        bitmapCollection.Add(bitmap);

                        // Do the touch processing
                        bitmapDictionary.Add(args.Id, bitmap);
                        bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                        canvasView.InvalidateSurface();
                        break;
                    }
                }
                break;

            case TouchActionType.Moved:
                if (bitmapDictionary.ContainsKey(args.Id))
                {
                    TouchManipulationBitmap bitmap = bitmapDictionary[args.Id];
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (bitmapDictionary.ContainsKey(args.Id))
                {
                    TouchManipulationBitmap bitmap = bitmapDictionary[args.Id];
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    bitmapDictionary.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
}
```

Observe cómo el `Pressed` lógica recorre el `bitmapCollection` en orden inverso. Los mapas de bits a menudo superponen entre sí. Los mapas de bits más adelante en la colección se encuentran visualmente encima de los mapas de bits anteriormente en la colección. Si hay varios mapas de bits en el dedo que presiona en la pantalla, lo más alto debe ser la que se manipula mediante ese dedo.

Tenga en cuenta también que el `Pressed` lógica mueve ese mapa de bits al final de la colección para que se mueva visualmente en la parte superior de la pila de otros mapas de bits.

En el `Moved` y `Released` eventos, el `TouchAction` llamadas del controlador de la `ProcessingTouchEvent` método `TouchManipulationBitmap` al igual que el programa anterior.

Por último, el `PaintSurface` llamadas del controlador de la `Paint` método de cada `TouchManipulationBitmap` objeto:

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKCanvas canvas = args.Surface.Canvas;
        canvas.Clear();

        foreach (TouchManipulationBitmap bitmap in bitmapCollection)
        {
            bitmap.Paint(canvas);
        }
    }
}
```

El código recorre en iteración la colección y muestra la pila de mapas de bits desde el principio de la colección al final:

[![](touch-images/bitmapscatterview-small.png "Captura de pantalla triple de la página de vista de mapa de bits de dispersión")](touch-images/bitmapscatterview-large.png#lightbox "Triple captura de pantalla de la página de vista de mapa de bits de dispersión")

## <a name="single-finger-scaling"></a>Escalado de un solo dedo

Una operación de escalado requiere normalmente un gesto de reducir con dos dedos. Sin embargo, es posible implementar el escalado con un solo dedo haciendo que el dedo mover las esquinas de un mapa de bits.

Esto se muestra en el **solo dedo esquina escalado** página. Dado que este ejemplo utiliza un tipo de escala que algo diferente que se implementa en el `TouchManipulationManager` (clase), no utiliza esa clase o la `TouchManipulationBitmap` clase. En su lugar, toda la lógica de interacción es en el archivo de código subyacente. Esto es un poco más fácil lógica de lo habitual porque realiza el seguimiento de un solo dedo a la vez y simplemente omite cualquier dedos secundaria que podrían estar tocando la pantalla.

El [ **SingleFingerCornerScale.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml) crea una instancia de la página el `SKCanvasView` clase y crea un `TouchEffect` objeto para el seguimiento de eventos de función táctil:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Transforms.SingleFingerCornerScalePage"
             Title="Single Finger Corner Scale">

    <Grid BackgroundColor="White"
          Grid.Row="1">

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction"   />
        </Grid.Effects>
    </Grid>
</ContentPage>
```

El [ **SingleFingerCornerScalePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml.cs) archivo carga un recurso de mapa de bits desde el **Media** directorio y lo muestra mediante un `SKMatrix` objeto definido como un campo:

```csharp
public partial class SingleFingerCornerScalePage : ContentPage
{
    SKBitmap bitmap;
    SKMatrix currentMatrix = SKMatrix.MakeIdentity();
    ···

    public SingleFingerCornerScalePage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.SetMatrix(currentMatrix);
        canvas.DrawBitmap(bitmap, 0, 0);
    }
    ···
}
```

Esto `SKMatrix` se modifica el objeto por la lógica de interacción se muestra a continuación.

El resto del archivo de código subyacente es la `TouchEffect` controlador de eventos. Comienza mediante la conversión de la ubicación actual del dedo en una `SKPoint` valor. Para el `Pressed` tipo de acción, el controlador comprueba que ningún otro dedo está tocando la pantalla, y que el dedo se encuentra dentro de los límites del mapa de bits.

La parte fundamental del código es un `if` instrucción que implican dos llamadas a la `Math.Pow` método. Este matemáticas comprueba si la ubicación del dedo está fuera de una elipse que rellena el mapa de bits. Si es así, es una operación de escalado. El dedo se encuentra cerca de una de las esquinas del mapa de bits y un punto de pivote se determina que es la esquina opuesta. Si el dedo se encuentra dentro de esta elipse, es una operación de desplazamiento regular:

```csharp
public partial class SingleFingerCornerScalePage : ContentPage
{
    SKBitmap bitmap;
    SKMatrix currentMatrix = SKMatrix.MakeIdentity();

    // Information for translating and scaling
    long? touchId = null;
    SKPoint pressedLocation;
    SKMatrix pressedMatrix;

    // Information for scaling
    bool isScaling;
    SKPoint pivotPoint;
    ···

    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                // Track only one finger
                if (touchId.HasValue)
                    return;

                // Check if the finger is within the boundaries of the bitmap
                SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
                rect = currentMatrix.MapRect(rect);
                if (!rect.Contains(point))
                    return;

                // First assume there will be no scaling
                isScaling = false;

                // If touch is outside interior ellipse, make this a scaling operation
                if (Math.Pow((point.X - rect.MidX) / (rect.Width / 2), 2) +
                    Math.Pow((point.Y - rect.MidY) / (rect.Height / 2), 2) > 1)
                {
                    isScaling = true;
                    float xPivot = point.X < rect.MidX ? rect.Right : rect.Left;
                    float yPivot = point.Y < rect.MidY ? rect.Bottom : rect.Top;
                    pivotPoint = new SKPoint(xPivot, yPivot);
                }

                // Common for either pan or scale
                touchId = args.Id;
                pressedLocation = point;
                pressedMatrix = currentMatrix;
                break;

            case TouchActionType.Moved:
                if (!touchId.HasValue || args.Id != touchId.Value)
                    return;

                SKMatrix matrix = SKMatrix.MakeIdentity();

                // Translating
                if (!isScaling)
                {
                    SKPoint delta = point - pressedLocation;
                    matrix = SKMatrix.MakeTranslation(delta.X, delta.Y);
                }
                // Scaling
                else
                {
                    float scaleX = (point.X - pivotPoint.X) / (pressedLocation.X - pivotPoint.X);
                    float scaleY = (point.Y - pivotPoint.Y) / (pressedLocation.Y - pivotPoint.Y);
                    matrix = SKMatrix.MakeScale(scaleX, scaleY, pivotPoint.X, pivotPoint.Y);
                }

                // Concatenate the matrices
                SKMatrix.PreConcat(ref matrix, pressedMatrix);
                currentMatrix = matrix;
                canvasView.InvalidateSurface();
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                touchId = null;
                break;
        }
    }
}
```

El `Moved` tipo de acción calcula una matriz correspondiente a la actividad táctil desde el momento en que el dedo presiona la pantalla hasta este momento. Concatena esa matriz con la matriz en vigor en el momento en que el dedo presiona primero el mapa de bits. La operación de escalado siempre es relativa a la esquina opuesta a lo que el dedo toca.

Para los mapas de bits pequeño o rectangulares, una elipse interior podría ocupar más del mapa de bits y dejar diminutas áreas en las esquinas para escalar el mapa de bits. Es preferible un enfoque un poco diferente, en cuyo caso puede reemplazar esa toda `if` bloque que establece `isScaling` a `true` con este código:

```csharp
float halfHeight = rect.Height / 2;
float halfWidth = rect.Width / 2;

// Top half of bitmap
if (point.Y < rect.MidY)
{
    float yRelative = (point.Y - rect.Top) / halfHeight;

    // Upper-left corner
    if (point.X < rect.MidX - yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Right, rect.Bottom);
    }
    // Upper-right corner
    else if (point.X > rect.MidX + yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Left, rect.Bottom);
    }
}
// Bottom half of bitmap
else
{
    float yRelative = (point.Y - rect.MidY) / halfHeight;

    // Lower-left corner
    if (point.X < rect.Left + yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Right, rect.Top);
    }
    // Lower-right corner
    else if (point.X > rect.Right - yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Left, rect.Top);
    }
}
```

Este código de forma eficaz divide el área del mapa de bits en una forma de rombo interiores y cuatro triángulos en las esquinas. Esto permite mucho más grandes áreas en las esquinas para captar y escalar el mapa de bits.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Invocación de eventos de efectos](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
