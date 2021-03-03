---
title: Manipulaciones táctiles
description: En este artículo se explica cómo usar transformaciones de matriz para implementar la función táctil de arrastrar, reducir y girar, y se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B8DD2D-7392-4EC5-BFB0-6209407AD650
author: davidbritch
ms.author: dabritch
ms.date: 09/14/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ee69ca1e95f7dcffa60387579e89c3a2d3e985da
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374295"
---
# <a name="touch-manipulations"></a>Manipulaciones táctiles

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Usar transformaciones de matriz para implementar la función táctil de arrastrar, reducir y girar_

En entornos multitáctiles, como los de los dispositivos móviles, los usuarios suelen usar los dedos para manipular los objetos en la pantalla. Los gestos comunes, como un arrastre de un dedo y un toque de dos dedos, pueden mover y escalar objetos, o incluso girarlos. Estos gestos se implementan generalmente mediante matrices de transformación, y en este artículo se muestra cómo hacerlo.

![Mapa de bits que se somete a traslación, escala y giro](touch-images/touchmanipulationsexample.png)

Todos los ejemplos que se muestran aquí usan el Xamarin.Forms efecto de seguimiento de toque que se muestra en el artículo [**invocación de eventos a partir de efectos**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md).

## <a name="dragging-and-translation"></a>Arrastrar y traslación

Una de las aplicaciones más importantes de las transformaciones de matriz es el procesamiento táctil. Un [`SKMatrix`](xref:SkiaSharp.SKMatrix) valor único puede consolidar una serie de operaciones táctiles. 

En el caso del arrastre con un solo dedo, el `SKMatrix` valor realiza la traducción. Esto se muestra en la página de **arrastre de mapas de bits** . El archivo XAML crea una instancia `SKCanvasView` de en un Xamarin.Forms `Grid` . Se ha `TouchEffect` agregado un objeto a la `Effects` colección de `Grid` :

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

En teoría, el `TouchEffect` objeto podría agregarse directamente a la `Effects` colección de `SKCanvasView` , pero eso no funciona en todas las plataformas. Dado que `SKCanvasView` tiene el mismo tamaño que el `Grid` de esta configuración, la asociación a la `Grid` también funciona.

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

Sin código adicional, el `SKMatrix` valor siempre es la matriz de identidades y no tendrá ningún efecto en la presentación del mapa de bits. El objetivo del `OnTouchEffectAction` controlador establecido en el archivo XAML es modificar el valor de la matriz para reflejar las manipulaciones táctiles.

El `OnTouchEffectAction` controlador comienza convirtiendo el Xamarin.Forms `Point` valor en un valor de SkiaSharp `SKPoint` . Se trata de una cuestión sencilla de escalado basada en las `Width` `Height` propiedades y de `SKCanvasView` (que son unidades independientes del dispositivo) y la `CanvasSize` propiedad, que se encuentra en unidades de píxeles:

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

Cuando un dedo toca primero la pantalla, se desencadena un evento de tipo `TouchActionType.Pressed` . La primera tarea consiste en determinar si el dedo toca el mapa de bits. Esta tarea se suele denominar _prueba de posicionamiento_. En este caso, la prueba de posicionamiento se puede lograr creando un `SKRect` valor que se corresponda con el mapa de bits, aplicando la transformación de matriz a él `MapRect` y, a continuación, determinando si el punto táctil está dentro del rectángulo transformado.

En ese caso, el `touchId` campo se establece en el ID. de toque y se guarda la posición del dedo.

En el `TouchActionType.Moved` caso del evento, los factores de traducción del `SKMatrix` valor se ajustan en función de la posición actual del dedo y la nueva posición del dedo. La nueva posición se guarda para la próxima vez a través de y `SKCanvasView` se invalida.

Mientras experimenta con este programa, tenga en cuenta que solo puede arrastrar el mapa de bits cuando el dedo toca un área en la que se muestra el mapa de bits. Aunque esa restricción no es muy importante para este programa, resulta fundamental al manipular varios mapas de bits.

## <a name="pinching-and-scaling"></a>Reducir y escalar

¿Qué desea suceder cuando dos dedos tocan el mapa de bits? Si los dos dedos se mueven en paralelo, es probable que desee que el mapa de bits se mueva junto con los dedos. Si los dos dedos realizan una operación de pinch o Stretch, es posible que desee que el mapa de bits se rote (para tratarlo en la sección siguiente) o que se escale. Al escalar un mapa de bits, resulta más conveniente que los dos dedos permanezcan en las mismas posiciones en relación con el mapa de bits, y para que el mapa de bits se escale en consecuencia.

Controlar dos dedos a la vez parece complicado, pero tenga en cuenta que el `TouchAction` controlador solo recibe información sobre un dedo a la vez. Si dos dedos manipulan el mapa de bits, para cada evento, un dedo ha cambiado de posición, pero el otro no ha cambiado. En el código de la página de **escala de mapa de bits** siguiente, el dedo que no ha cambiado de posición se denomina punto de _pivote_ porque la transformación es relativa a ese punto.

Una diferencia entre este programa y el programa anterior es que se deben guardar varios ID. Touch. Para este fin se usa un diccionario, donde Touch ID es la clave del diccionario y el valor del diccionario es la posición actual de dicho dedo:

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

El control de la `Pressed` acción es prácticamente el mismo que el programa anterior, salvo que el identificador y el punto táctil se agregan al diccionario. Las `Released` `Cancelled` acciones y quitan la entrada del diccionario.

Sin embargo, el control de la `Moved` acción es más complejo. Si solo hay un dedo implicado, el procesamiento es muy similar al del programa anterior. En el caso de dos o más dedos, el programa también debe obtener información del diccionario que implique el dedo que no se mueve. Para ello, copia las claves del diccionario en una matriz y, a continuación, compara la primera clave con el identificador del dedo que se está moviendo. Esto permite que el programa obtenga el punto de pivote correspondiente al dedo que no se mueve.

Después, el programa calcula dos vectores de la nueva posición del dedo en relación con el punto de pivote y la posición del dedo anterior en relación con el punto de pivote. Las proporciones de estos vectores son factores de escala. Dado que la división por cero es una posibilidad, se debe comprobar si hay valores infinitos o valores NaN (no un número). Si todo es correcto, una transformación de escala se concatena con el `SKMatrix` valor guardado como un campo.

Al experimentar con esta página, observará que puede arrastrar el mapa de bits con uno o dos dedos, o bien escalarlo con dos dedos. El escalado es _anisotrópico_, lo que significa que el escalado puede ser diferente en las direcciones horizontal y vertical. Esto distorsiona la relación de aspecto, pero también permite voltear el mapa de bits para crear una imagen reflejada. También puede descubrir que puede reducir el mapa de bits a una dimensión cero y desaparece. En el código de producción, querrá protegerse frente a esto.

## <a name="two-finger-rotation"></a>Rotación de dos dedos

La página **girar mapa de bits** le permite usar dos dedos para el escalado horizontal o de giro. El mapa de bits siempre conserva la relación de aspecto correcta. El uso de dos dedos para la rotación y el escalado anisotrópico no funciona muy bien porque el movimiento de los dedos es muy similar para ambas tareas.

La primera diferencia importante en este programa es la lógica de la prueba de posicionamiento. Los programas anteriores usaban el `Contains` método de `SKRect` para determinar si el punto táctil está dentro del rectángulo transformado que corresponde al mapa de bits. Sin embargo, a medida que el usuario manipula el mapa de bits, el mapa de bits podría girarse y `SKRect` no puede representar correctamente un rectángulo girado. Podría temer que la lógica de prueba de posicionamiento necesite implementar geometría analítica bastante compleja en ese caso.

Sin embargo, hay un acceso directo disponible: determinar si un punto está dentro de los límites de un rectángulo transformado es el mismo que determinar si un punto de transformación inverso se encuentra dentro de los límites del rectángulo sin transformar. Este es un cálculo mucho más sencillo y la lógica puede seguir usando el método práctico `Contains` :

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

La lógica para el `Moved` evento comienza como el programa anterior. Dos vectores denominados `oldVector` y `newVector` se calculan según el anterior y el punto actual del dedo en movimiento y el punto de pivote del dedo que no se mueve. Pero se determinan los ángulos de estos vectores y la diferencia es el ángulo de giro.

También podría estar implicado el escalado, por lo que el vector anterior se gira según el ángulo de rotación. La magnitud relativa de los dos vectores es ahora el factor de escala. Observe que `scale` se usa el mismo valor para el escalado horizontal y vertical, de modo que el escalado sea anisotrópico. La `matrix` matriz de rotación y una matriz de escala ajustan el campo.

Si su aplicación necesita implementar el procesamiento táctil para un único mapa de bits (u otro objeto), puede adaptar el código de estos tres ejemplos para su propia aplicación. Pero si necesita implementar el procesamiento táctil para varios mapas de bits, probablemente querrá encapsular estas operaciones táctiles en otras clases.

## <a name="encapsulating-the-touch-operations"></a>Encapsular las operaciones táctiles

La página de **manipulación táctil** muestra la manipulación táctil de un único mapa de bits, pero usa otros archivos que encapsulan gran parte de la lógica mostrada anteriormente. El primero de estos archivos es la [`TouchManipulationMode`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationMode.cs) enumeración, que indica los distintos tipos de manipulación táctil que implementa el código que verá:

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

`PanOnly` es un arrastre de un dedo que se implementa con la traducción. Todas las opciones siguientes también incluyen el movimiento panorámico, pero implica dos dedos: `IsotropicScale` es una operación de pinch que hace que el escalado de objetos sea igual en las direcciones horizontal y vertical. `AnisotropicScale` permite el escalado distinto.

La `ScaleRotate` opción es para el ajuste de escala y la rotación de dos dedos. El escalado es anisotrópico. Como se mencionó anteriormente, la implementación de la rotación de dos dedos con el escalado anisotrópico es problemática porque los movimientos de los dedos son esencialmente los mismos.

La `ScaleDualRotate` opción agrega una rotación de un dedo. Cuando un solo dedo arrastra el objeto, el objeto arrastrado se gira primero alrededor de su centro, de modo que el centro del objeto se alinea con el vector de arrastre.

El archivo [**TouchManipulationPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml) incluye un `Picker` con los miembros de la `TouchManipulationMode` enumeración:

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

Hacia la parte inferior hay un `SKCanvasView` y un `TouchEffect` adjunto a la celda única `Grid` que lo incluye.

El archivo de código subyacente [**TouchManipulationPage.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml.cs) tiene un `bitmap` campo, pero no es de tipo `SKBitmap` . El tipo es `TouchManipulationBitmap` (una clase que verá en breve):

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

El constructor crea una instancia `TouchManipulationBitmap` de un objeto, pasando al constructor `SKBitmap` obtenido a partir de un recurso incrustado. El constructor concluye estableciendo la `Mode` propiedad de la `TouchManager` propiedad del `TouchManipulationBitmap` objeto en un miembro de la `TouchManipulationMode` enumeración.

El `SelectedIndexChanged` controlador de `Picker` también establece esta `Mode` propiedad:

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

El `TouchAction` controlador de la instancia de que se ha `TouchEffect` creado en el archivo XAML llama a dos métodos en `TouchManipulationBitmap` denominados `HitTest` y `ProcessTouchEvent` :

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

Si el `HitTest` método devuelve `true` &mdash; , lo que significa que un dedo ha tocado la pantalla dentro del área ocupada por el mapa de bits &mdash; , el Touch ID se agrega a la `TouchIds` colección. Este identificador representa la secuencia de eventos táctiles para ese dedo hasta que el dedo se levanta de la pantalla. Si varios dedos tocan el mapa de bits, la `touchIds` colección contiene un ID. de toque para cada dedo.

El `TouchAction` controlador también llama a la `ProcessTouchEvent` clase en `TouchManipulationBitmap` . Aquí es donde se producen algunos (pero no todos) el procesamiento táctil real.

La [`TouchManipulationBitmap`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationBitmap.cs) clase es una clase contenedora para `SKBitmap` que contiene código para representar los eventos de toque de mapa de bits y proceso. Funciona junto con código más generalizado en una `TouchManipulationManager` clase (que verá en breve).

El `TouchManipulationBitmap` constructor guarda el `SKBitmap` y crea una instancia de dos propiedades, la `TouchManager` propiedad de tipo `TouchManipulationManager` y la `Matrix` propiedad de tipo `SKMatrix` :

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

Esta `Matrix` propiedad es la transformación acumulada resultante de toda la actividad Touch. Como verá, cada evento Touch se resuelve en una matriz, que luego se concatena con el `SKMatrix` valor almacenado por la `Matrix` propiedad.

El `TouchManipulationBitmap` objeto se dibuja en su `Paint` método. El argumento es un `SKCanvas` objeto. `SKCanvas`Es posible que ya se le haya aplicado una transformación, por lo que el `Paint` método concatena la `Matrix` propiedad asociada con el mapa de bits a la transformación existente y restaura el lienzo cuando ha finalizado:

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

El `HitTest` método devuelve `true` si el usuario toca la pantalla en un punto dentro de los límites del mapa de bits. Se usa la lógica mostrada anteriormente en la página **rotación de mapa de bits** :

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

El segundo método público en `TouchManipulationBitmap` es `ProcessTouchEvent` . Cuando se llama a este método, ya se ha establecido que el evento Touch pertenece a este mapa de bits determinado. El método mantiene un diccionario de [`TouchManipulationInfo`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationInfo.cs) objetos, que es simplemente el punto anterior y el nuevo punto de cada dedo:

```csharp
class TouchManipulationInfo
{
    public SKPoint PreviousPoint { set; get; }

    public SKPoint NewPoint { set; get; }
}
```

Este es el Diccionario y el `ProcessTouchEvent` propio método:

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

En los `Moved` `Released` eventos y, el método llama a `Manipulate` . En estos momentos, `touchDictionary` contiene uno o más `TouchManipulationInfo` objetos. Si `touchDictionary` contiene un elemento, es probable que los `PreviousPoint` `NewPoint` valores y sean distintos y representen el movimiento de un dedo. Si varios dedos tocan el mapa de bits, el diccionario contiene más de un elemento, pero solo uno de estos elementos `PreviousPoint` tiene `NewPoint` valores y distintos. Todos los Rest tienen `PreviousPoint` los mismos `NewPoint` valores y.

Esto es importante: el `Manipulate` método puede suponer que está procesando el movimiento de un solo dedo. En el momento de esta llamada no se está moviendo ninguno de los otros dedos y, si realmente se están moviendo (como es probable,), esos movimientos se procesarán en futuras llamadas a `Manipulate` .

El `Manipulate` método copia primero el Diccionario en una matriz para mayor comodidad. Omite todo lo que no sea las dos primeras entradas. Si hay más de dos dedos intentando manipular el mapa de bits, se omiten los demás. `Manipulate` es el miembro final de `TouchManipulationBitmap` :

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

Si un dedo está manipulando el mapa de bits, `Manipulate` llama al `OneFingerManipulate` método del `TouchManipulationManager` objeto. Para dos dedos, llama a `TwoFingerManipulate` . Los argumentos de estos métodos son los mismos: los `prevPoint` `newPoint` argumentos y representan el dedo que se mueve. Pero el `pivotPoint` argumento es diferente para las dos llamadas:

En el caso de la manipulación con un solo dedo, `pivotPoint` es el centro del mapa de bits. Esto permite la rotación de un dedo. En el caso de la manipulación con dos dedos, el evento indica el movimiento de un solo dedo, de modo que `pivotPoint` es el dedo que no se mueve.

En ambos casos, `TouchManipulationManager` devuelve un `SKMatrix` valor, que el método concatena con la propiedad actual `Matrix` que `TouchManipulationPage` usa para representar el mapa de bits.

`TouchManipulationManager` está generalizado y no utiliza ningún otro archivo excepto `TouchManipulationMode` . Es posible que pueda usar esta clase sin cambiar sus propias aplicaciones. Define una única propiedad de tipo `TouchManipulationMode`:

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }
    ...
}
```

Sin embargo, probablemente querrá evitar la `AnisotropicScale` opción. Es muy fácil con esta opción manipular el mapa de bits para que uno de los factores de escala sea cero. Esto hace que el mapa de bits desaparezca de la vista, nunca se devuelva. Si realmente necesita escalado anisotrópico, querrá mejorar la lógica para evitar resultados no deseados.

`TouchManipulationManager` hace uso de vectores, pero dado que no hay ninguna `SKVector` estructura en SkiaSharp, `SKPoint` se usa en su lugar. `SKPoint` admite el operador de resta y el resultado se puede tratar como un vector. La única lógica específica del vector que era necesario agregar es un `Magnitude` cálculo:

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

Cada vez que se selecciona rotación, los métodos de manipulación de un dedo y dos dedos controlan primero el giro. Si se detecta algún giro, se quitará el componente de rotación. Lo que queda se interpreta como movimiento panorámico y escalado.

Este es el `OneFingerManipulate` método. Si no se ha habilitado la rotación de un dedo, la lógica es sencilla &mdash; simplemente usa el punto anterior y el nuevo punto para construir un vector denominado `delta` que se corresponde precisamente con la traducción. Con la rotación de un dedo habilitada, el método usa ángulos desde el punto de pivote (el centro del mapa de bits) hasta el punto anterior y el nuevo punto para construir una matriz de rotación:

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

En el `TwoFingerManipulate` método, el punto de pivote es la posición del dedo que no se mueve en este evento Touch determinado. La rotación es muy similar a la rotación de un dedo y, a continuación, se ajusta el vector denominado `oldVector` (basado en el punto anterior) para la rotación. El movimiento restante se interpreta como escalado:

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

Observará que no hay ninguna traducción explícita en este método. Sin embargo, los `MakeRotation` `MakeScale` métodos y se basan en el punto de pivote y eso incluye la conversión implícita. Si utiliza dos dedos en el mapa de bits y los arrastra en la misma dirección, `TouchManipulation` obtendrá una serie de eventos táctiles que alternan entre los dos dedos. A medida que cada dedo se mueve en relación con el otro, el escalado o los resultados de la rotación, pero lo niega el movimiento del otro dedo y el resultado es la traducción.

La única parte restante de la página de **manipulación táctil** es el `PaintSurface` controlador del `TouchManipulationPage` archivo de código subyacente. Llama al `Paint` método de `TouchManipulationBitmap` , que aplica la matriz que representa la actividad táctil acumulada:

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

El `PaintSurface` controlador finaliza mostrando un `MatrixDisplay` objeto que muestra la matriz táctil acumulada:

[![Captura de pantalla triple de la página de manipulación táctil](touch-images/touchmanipulation-small.png)](touch-images/touchmanipulation-large.png#lightbox "Captura de pantalla triple de la página de manipulación táctil")

## <a name="manipulating-multiple-bitmaps"></a>Manipular varios mapas de bits

Una de las ventajas de aislar el código de procesamiento táctil en clases como `TouchManipulationBitmap` y `TouchManipulationManager` es la capacidad de reutilizar estas clases en un programa que permite al usuario manipular varios mapas de bits.

En la página de la **vista dispersión de mapas de bits** se muestra cómo hacerlo. En lugar de definir un campo de tipo `TouchManipulationBitmap` , la [`BitmapScatterPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BitmapScatterViewPage.xaml.cs) clase define un `List` objeto de objetos de mapa de bits:

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

El constructor se carga en todos los mapas de bits disponibles como recursos incrustados y los agrega a `bitmapCollection` . Observe que la `Matrix` propiedad se inicializa en cada `TouchManipulationBitmap` objeto, por lo que las esquinas superior izquierda de cada mapa de bits se desplazan por 100 píxeles.

La `BitmapScatterView` página también necesita controlar los eventos Touch para varios mapas de bits. En lugar de definir un `List` de los ID. de Touch de los objetos manipulados actualmente `TouchManipulationBitmap` , este programa requiere un diccionario:

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

Observe cómo la `Pressed` lógica se recorre `bitmapCollection` en bucle en orden inverso. Los mapas de bits suelen superponerse entre sí. Los mapas de bits situados más adelante en la colección se encuentran en la parte superior de los mapas de bits anteriores de la colección. Si hay varios mapas de bits bajo el dedo que se presionan en la pantalla, el nivel superior debe ser el que haya manipulado dicho dedo.

Observe también que la `Pressed` lógica mueve ese mapa de bits al final de la colección para que se mueva visualmente a la parte superior de la pila de otros mapas de bits.

En los `Moved` `Released` eventos y, el `TouchAction` controlador llama al `ProcessingTouchEvent` método `TouchManipulationBitmap` exactamente igual que el programa anterior.

Por último, el `PaintSurface` controlador llama al `Paint` método de cada `TouchManipulationBitmap` objeto:

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

El código recorre en bucle la colección y muestra la pila de mapas de bits desde el principio de la colección hasta el final:

[![Captura de pantalla triple de la página de vista dispersión de mapa de bits](touch-images/bitmapscatterview-small.png)](touch-images/bitmapscatterview-large.png#lightbox "Captura de pantalla triple de la página de vista dispersión de mapa de bits")

## <a name="single-finger-scaling"></a>Escalado de Single-Finger

Una operación de escalado generalmente requiere un gesto de pinch con dos dedos. Sin embargo, es posible implementar el escalado con un solo dedo si el dedo mueve las esquinas de un mapa de bits.

Esto se muestra en la página de **escala de esquina de un solo dedo** . Dado que en este ejemplo se usa un tipo de escala ligeramente diferente al que se implementa en la `TouchManipulationManager` clase, no se usa esa clase o la `TouchManipulationBitmap` clase. En su lugar, toda la lógica táctil está en el archivo de código subyacente. Se trata de una lógica algo más sencilla de lo habitual, ya que realiza un seguimiento de un solo dedo cada vez, y simplemente omite los dedos secundarios que puedan tocar la pantalla.

La página [**SingleFingerCornerScale. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml) crea una instancia de la `SKCanvasView` clase y crea un `TouchEffect` objeto para realizar el seguimiento de los eventos de toque:

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

El archivo [**SingleFingerCornerScalePage.Xaml.CS**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml.cs) carga un recurso de mapa de bits desde el directorio **multimedia** y lo muestra mediante un `SKMatrix` objeto definido como un campo:

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

Este `SKMatrix` objeto se modifica mediante la lógica táctil que se muestra a continuación.

El resto del archivo de código subyacente es el `TouchEffect` controlador de eventos. Comienza convirtiendo la ubicación actual del dedo en un `SKPoint` valor. En el caso del `Pressed` tipo de acción, el controlador comprueba que ningún dedo toque la pantalla y que el dedo está dentro de los límites del mapa de bits.

La parte fundamental del código es una `if` instrucción que implica dos llamadas al `Math.Pow` método. Esta expresión matemática comprueba si la ubicación del dedo está fuera de una elipse que rellena el mapa de bits. Si es así, esa es una operación de escalado. El dedo está cerca de una de las esquinas del mapa de bits y se determina un punto de pivote que es la esquina opuesta. Si el dedo está dentro de esta elipse, se trata de una operación de movimiento panorámico normal:

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

El `Moved` tipo de acción calcula una matriz que corresponde a la actividad Touch desde el momento en que el dedo presionó la pantalla hasta este momento. Concatena esa matriz con la matriz en vigor en el momento en que el dedo presionó el mapa de bits por primera vez. La operación de escalado siempre es relativa a la esquina opuesta a la que toca el dedo.

En el caso de los mapas de bits de tamaño pequeño o Oblong, una elipse interior podría ocupar la mayor parte del mapa de bits y dejar pequeñas áreas en las esquinas para escalar el mapa de bits. Podría preferir un enfoque algo diferente, en cuyo caso puede reemplazar `if` el bloque completo que establece `isScaling` en `true` con este código:

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

Este código divide eficazmente el área del mapa de bits en una forma de rombo interior y cuatro triángulos en las esquinas. Esto permite que las áreas mucho más grandes de las esquinas capten y escalen el mapa de bits.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Invocación de eventos desde efectos](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)