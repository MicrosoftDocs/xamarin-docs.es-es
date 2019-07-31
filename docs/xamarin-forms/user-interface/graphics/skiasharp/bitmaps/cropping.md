---
title: Recortar los mapas de bits de SkiaSharp
description: Obtenga información sobre cómo usar SkiaSharp para diseñar una interfaz de usuario de forma interactiva desribing un rectángulo de recorte.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 0A79AB27-C69F-4376-8FFE-FF46E4783F30
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 50174844100eb852ac7daf5ce3f33b02b490ceb2
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646662"
---
# <a name="cropping-skiasharp-bitmaps"></a>Recortar los mapas de bits de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

El [ **creando y mapas de bits de dibujo de SkiaSharp** ](drawing.md) artículo se describe cómo un `SKBitmap` objeto se puede pasar a un `SKCanvas` constructor. Cualquier método de dibujo se llama en la que hace que los gráficos de lienzo para representarse en el mapa de bits. Estos métodos de dibujo incluyen `DrawBitmap`, lo que significa que esta técnica permite transferir parte o la totalidad de un mapa de bits a otro mapa de bits, quizás con las transformaciones aplicadas.

Puede usar esa técnica para recortar un mapa de bits mediante una llamada a la [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) método con rectángulos de origen y destino:

```csharp
canvas.DrawBitmap(bitmap, sourceRect, destRect);
```

Sin embargo, las aplicaciones que implementan el recorte a menudo proporcionan una interfaz para el usuario seleccionar el rectángulo de recorte de forma interactiva:

![Ejemplo de recorte](cropping-images/CroppingSample.png "recortar de ejemplo")

En este artículo se centra en esa interfaz.

## <a name="encapsulating-the-cropping-rectangle"></a>Que encapsula el rectángulo de recorte

Resulta útil aislar parte de la lógica de recorte en una clase denominada `CroppingRectangle`. Los parámetros del constructor incluyen un rectángulo máximo, que suele ser el tamaño del mapa de bits que se recorta, y una relación de aspecto opcional. En primer lugar, el constructor define un rectángulo de recorte inicial, que hace que sea pública en el `Rect` propiedad de tipo `SKRect`. Este rectángulo de recorte inicial es el 80% del ancho y alto del rectángulo de mapa de bits, pero se ajusta a continuación, si se especifica una relación de aspecto:

```csharp
class CroppingRectangle
{
    ···
    SKRect maxRect;             // generally the size of the bitmap
    float? aspectRatio;

    public CroppingRectangle(SKRect maxRect, float? aspectRatio = null)
    {
        this.maxRect = maxRect;
        this.aspectRatio = aspectRatio;

        // Set initial cropping rectangle
        Rect = new SKRect(0.9f * maxRect.Left + 0.1f * maxRect.Right,
                          0.9f * maxRect.Top + 0.1f * maxRect.Bottom,
                          0.1f * maxRect.Left + 0.9f * maxRect.Right,
                          0.1f * maxRect.Top + 0.9f * maxRect.Bottom);

        // Adjust for aspect ratio
        if (aspectRatio.HasValue)
        {
            SKRect rect = Rect;
            float aspect = aspectRatio.Value;

            if (rect.Width > aspect * rect.Height)
            {
                float width = aspect * rect.Height;
                rect.Left = (maxRect.Width - width) / 2;
                rect.Right = rect.Left + width;
            }
            else
            {
                float height = rect.Width / aspect;
                rect.Top = (maxRect.Height - height) / 2;
                rect.Bottom = rect.Top + height;
            }

            Rect = rect;
        }
    }
    
    public SKRect Rect { set; get; }
    ···
}
```

Un elemento útil de información que `CroppingRectangle` también hace disponible es una matriz de `SKPoint` los valores correspondientes a las cuatro esquinas de en el orden superior izquierda, superior derecha, inferior derecha e inferior izquierda del rectángulo de recorte:

```csharp
class CroppingRectangle
{
    ···
    public SKPoint[] Corners
    {
        get
        {
            return new SKPoint[]
            {
                new SKPoint(Rect.Left, Rect.Top),
                new SKPoint(Rect.Right, Rect.Top),
                new SKPoint(Rect.Right, Rect.Bottom),
                new SKPoint(Rect.Left, Rect.Bottom)
            };
        }
    }
    ···
}
```

Esta matriz se usa en el método siguiente, que se denomina `HitTest`. El `SKPoint` parámetro es un punto que corresponde a un toque del dedo o un clic del mouse. El método devuelve un índice (0, 1, 2 o 3) correspondiente a la esquina que toca el puntero del dedo o el mouse, dentro de una distancia dada por la `radius` parámetro: 

```csharp
class CroppingRectangle
{
    ···
    public int HitTest(SKPoint point, float radius)
    {
        SKPoint[] corners = Corners;

        for (int index = 0; index < corners.Length; index++)
        {
            SKPoint diff = point - corners[index];
                
            if ((float)Math.Sqrt(diff.X * diff.X + diff.Y * diff.Y) < radius)
            {
                return index;
            }
        }

        return -1;
    }
    ···
}
```

Si el punto táctil o mouse no estaba en `radius` unidades de cualquier esquina, el método devuelve &ndash;1.

El método final en `CroppingRectangle` se denomina `MoveCorner`, que se llama en respuesta a tocar ni el mouse sobre el movimiento. Los dos parámetros indican el índice de la esquina se mueve y la nueva ubicación de esa esquina. La primera mitad del método ajusta según la nueva ubicación de la esquina, pero siempre dentro de los límites del rectángulo de recorte `maxRect`, que es el tamaño del mapa de bits. Esta lógica también tiene en cuenta el `MINIMUM` campo para evitar la contracción del rectángulo de recorte en nothing:

```csharp
class CroppingRectangle
{
    const float MINIMUM = 10;   // pixels width or height
    ···
    public void MoveCorner(int index, SKPoint point)
    {
        SKRect rect = Rect;

        switch (index)
        {
            case 0: // upper-left
                rect.Left = Math.Min(Math.Max(point.X, maxRect.Left), rect.Right - MINIMUM);
                rect.Top = Math.Min(Math.Max(point.Y, maxRect.Top), rect.Bottom - MINIMUM);
                break;

            case 1: // upper-right
                rect.Right = Math.Max(Math.Min(point.X, maxRect.Right), rect.Left + MINIMUM);
                rect.Top = Math.Min(Math.Max(point.Y, maxRect.Top), rect.Bottom - MINIMUM);
                break;

            case 2: // lower-right
                rect.Right = Math.Max(Math.Min(point.X, maxRect.Right), rect.Left + MINIMUM);
                rect.Bottom = Math.Max(Math.Min(point.Y, maxRect.Bottom), rect.Top + MINIMUM);
                break;

            case 3: // lower-left
                rect.Left = Math.Min(Math.Max(point.X, maxRect.Left), rect.Right - MINIMUM);
                rect.Bottom = Math.Max(Math.Min(point.Y, maxRect.Bottom), rect.Top + MINIMUM);
                break;
        }

        // Adjust for aspect ratio
        if (aspectRatio.HasValue)
        {
            float aspect = aspectRatio.Value;

            if (rect.Width > aspect * rect.Height)
            {
                float width = aspect * rect.Height;

                switch (index)
                {
                    case 0:
                    case 3: rect.Left = rect.Right - width; break;
                    case 1:
                    case 2: rect.Right = rect.Left + width; break;
                }
            }
            else
            {
                float height = rect.Width / aspect;

                switch (index)
                {
                    case 0:
                    case 1: rect.Top = rect.Bottom - height; break;
                    case 2:
                    case 3: rect.Bottom = rect.Top + height; break;
                }
            }
        }

        Rect = rect;
    }
}
```

La segunda mitad del método se ajusta para la relación de aspecto opcional.

Tenga en cuenta que todo el contenido de esta clase está en unidades de píxeles.

## <a name="a-canvas-view-just-for-cropping"></a>Una vista de lienzo para recortar

El `CroppingRectangle` utilizan la clase que ya ha visto la `PhotoCropperCanvasView` (clase), que se deriva de `SKCanvasView`. Esta clase es responsable de mostrar el mapa de bits y el rectángulo de recorte, así como controlar los eventos de funciones táctiles o mouse para cambiar el rectángulo de recorte.

El `PhotoCropperCanvasView` constructor requiere un mapa de bits. Una relación de aspecto es opcional. El constructor crea una instancia de un objeto de tipo `CroppingRectangle` según esta relación de aspecto y el mapa de bits y lo guarda como un campo:

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    ···
    public PhotoCropperCanvasView(SKBitmap bitmap, float? aspectRatio = null)
    {
        this.bitmap = bitmap;

        SKRect bitmapRect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
        croppingRect = new CroppingRectangle(bitmapRect, aspectRatio);
        ···
    }
    ···
}
```

Dado que esta clase se deriva de `SKCanvasView`, no es necesario instalar un controlador para el `PaintSurface` eventos. En su lugar, puede invalidar su `OnPaintSurface` método. El método muestra el mapa de bits y utiliza un par de `SKPaint` objetos guardados como campos que se va a dibujar el rectángulo de recorte actual:

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    const int CORNER = 50;      // pixel length of cropper corner
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    SKMatrix inverseBitmapMatrix;
    ···
    // Drawing objects
    SKPaint cornerStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.White,
        StrokeWidth = 10
    };

    SKPaint edgeStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.White,
        StrokeWidth = 2
    };
    ···
    protected override void OnPaintSurface(SKPaintSurfaceEventArgs args)
    {
        base.OnPaintSurface(args);

        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Gray);

        // Calculate rectangle for displaying bitmap 
        float scale = Math.Min((float)info.Width / bitmap.Width, (float)info.Height / bitmap.Height);
        float x = (info.Width - scale * bitmap.Width) / 2;
        float y = (info.Height - scale * bitmap.Height) / 2;
        SKRect bitmapRect = new SKRect(x, y, x + scale * bitmap.Width, y + scale * bitmap.Height);
        canvas.DrawBitmap(bitmap, bitmapRect);

        // Calculate a matrix transform for displaying the cropping rectangle
        SKMatrix bitmapScaleMatrix = SKMatrix.MakeIdentity();
        bitmapScaleMatrix.SetScaleTranslate(scale, scale, x, y);

        // Display rectangle
        SKRect scaledCropRect = bitmapScaleMatrix.MapRect(croppingRect.Rect);
        canvas.DrawRect(scaledCropRect, edgeStroke);

        // Display heavier corners
        using (SKPath path = new SKPath())
        {
            path.MoveTo(scaledCropRect.Left, scaledCropRect.Top + CORNER);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Left + CORNER, scaledCropRect.Top);

            path.MoveTo(scaledCropRect.Right - CORNER, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Top + CORNER);

            path.MoveTo(scaledCropRect.Right, scaledCropRect.Bottom - CORNER);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Right - CORNER, scaledCropRect.Bottom);

            path.MoveTo(scaledCropRect.Left + CORNER, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Bottom - CORNER);

            canvas.DrawPath(path, cornerStroke);
        }

        // Invert the transform for touch tracking
        bitmapScaleMatrix.TryInvert(out inverseBitmapMatrix);
    }
    ···
}
```

El código en el `CroppingRectangle` clase base del rectángulo de recorte en el tamaño de píxel del mapa de bits. Sin embargo, la presentación del mapa de bits mediante el `PhotoCropperCanvasView` clase se escala en función del tamaño del área de visualización. El `bitmapScaleMatrix` calculado en el `OnPaintSurface` reemplazar asignaciones de los píxeles del mapa de bits de tamaño y posición del mapa de bits, tal y como se muestra. Esta matriz, a continuación, se utiliza para transformar el rectángulo de recorte para que se pueden mostrar en relación con el mapa de bits.

La última línea de la `OnPaintSurface` invalidación toma el inverso de la `bitmapScaleMatrix` y lo guarda como el `inverseBitmapMatrix` campo. Esto se usa para el procesamiento de toque.

Un `TouchEffect` se crea una instancia de objeto como un campo y el constructor adjunta un controlador para el `TouchAction` eventos, pero la `TouchEffect` debe agregarse a la `Effects` colección de la _primario_ de la `SKCanvasView`derivados, por lo que terminamos el `OnParentSet` invalidar:

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    const int RADIUS = 100;     // pixel radius of touch hit-test
    ···
    CroppingRectangle croppingRect;
    SKMatrix inverseBitmapMatrix;

    // Touch tracking 
    TouchEffect touchEffect = new TouchEffect();
    struct TouchPoint
    {
        public int CornerIndex { set; get; }
        public SKPoint Offset { set; get; }
    }

    Dictionary<long, TouchPoint> touchPoints = new Dictionary<long, TouchPoint>();
    ···
    public PhotoCropperCanvasView(SKBitmap bitmap, float? aspectRatio = null)
    {
        ···
        touchEffect.TouchAction += OnTouchEffectTouchAction;
    }
    ···
    protected override void OnParentSet()
    {
        base.OnParentSet();

        // Attach TouchEffect to parent view
        Parent.Effects.Add(touchEffect);
    }
    ···
    void OnTouchEffectTouchAction(object sender, TouchActionEventArgs args)
    {
        SKPoint pixelLocation = ConvertToPixel(args.Location);
        SKPoint bitmapLocation = inverseBitmapMatrix.MapPoint(pixelLocation);

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                // Convert radius to bitmap/cropping scale
                float radius = inverseBitmapMatrix.ScaleX * RADIUS;

                // Find corner that the finger is touching
                int cornerIndex = croppingRect.HitTest(bitmapLocation, radius);

                if (cornerIndex != -1 && !touchPoints.ContainsKey(args.Id))
                {
                    TouchPoint touchPoint = new TouchPoint
                    {
                        CornerIndex = cornerIndex,
                        Offset = bitmapLocation - croppingRect.Corners[cornerIndex]
                    };

                    touchPoints.Add(args.Id, touchPoint);
                }
                break;

            case TouchActionType.Moved:
                if (touchPoints.ContainsKey(args.Id))
                {
                    TouchPoint touchPoint = touchPoints[args.Id];
                    croppingRect.MoveCorner(touchPoint.CornerIndex, 
                                            bitmapLocation - touchPoint.Offset);
                    InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchPoints.ContainsKey(args.Id))
                {
                    touchPoints.Remove(args.Id);
                }
                break;
        }
    }

    SKPoint ConvertToPixel(Xamarin.Forms.Point pt)
    {
        return new SKPoint((float)(CanvasSize.Width * pt.X / Width),
                           (float)(CanvasSize.Height * pt.Y / Height));
    }
}
```

Procesan los eventos de toque el `TouchAction` controlador están en unidades independientes del dispositivo. Estos primero deben convertirse a píxeles mediante los `ConvertToPixel` método en la parte inferior de la clase y, a continuación, se convierte en `CroppingRectangle` unidades mediante `inverseBitmapMatrix`.

Para `Pressed` eventos, el `TouchAction` llamadas del controlador de la `HitTest` método `CroppingRectangle`. Si esto devuelve un índice distinto &ndash;1, a continuación, una de las esquinas del rectángulo de recorte se está manipulando. Que el índice y un desplazamiento del punto táctil actual desde la esquina se almacena en un `TouchPoint` de objetos y agregado a la `touchPoints` diccionario.

Para el `Moved` eventos, el `MoveCorner` método `CroppingRectangle` se llama para mover la esquina, con posibles ajustes para la relación de aspecto.

En cualquier momento, un programa mediante `PhotoCropperCanvasView` puede tener acceso a la `CroppedBitmap` propiedad. Esta propiedad usa el `Rect` propiedad de la `CroppingRectangle` para crear un nuevo mapa de bits del tamaño recortado. La versión de `DrawBitmap` con el origen y destino rectángulos, a continuación, extrae un subconjunto del mapa de bits original:

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    ···
    public SKBitmap CroppedBitmap
    {
        get
        {
            SKRect cropRect = croppingRect.Rect;
            SKBitmap croppedBitmap = new SKBitmap((int)cropRect.Width, 
                                                  (int)cropRect.Height);
            SKRect dest = new SKRect(0, 0, cropRect.Width, cropRect.Height);
            SKRect source = new SKRect(cropRect.Left, cropRect.Top, 
                                       cropRect.Right, cropRect.Bottom);

            using (SKCanvas canvas = new SKCanvas(croppedBitmap))
            {
                canvas.DrawBitmap(bitmap, source, dest);
            }

            return croppedBitmap;
        }
    }
    ···
}
```

## <a name="hosting-the-photo-cropper-canvas-view"></a>Hospedaje de la vista de lienzo de puntero Recortar foto

Con esas dos clases de control de la lógica de recorte, la **Recortar foto** página en el **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** aplicación tiene muy poco trabajo. El archivo XAML crea una instancia de un `Grid` al host la `PhotoCropperCanvasView` y un **realiza** botón:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoCroppingPage"
             Title="Photo Cropping">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid x:Name="canvasViewHost"
              Grid.Row="0"
              BackgroundColor="Gray"
              Padding="5" />

        <Button Text="Done"
                Grid.Row="1"
                HorizontalOptions="Center"
                Margin="5"
                Clicked="OnDoneButtonClicked" />
    </Grid>
</ContentPage>
```

El `PhotoCropperCanvasView` no pueden crearse instancias en el archivo XAML porque requiere un parámetro de tipo `SKBitmap`.

En su lugar, el `PhotoCropperCanvasView` se crea una instancia en el constructor del archivo de código subyacente mediante uno de los mapas de bits de recursos:

```csharp
public partial class PhotoCroppingPage : ContentPage
{
    PhotoCropperCanvasView photoCropper;
    SKBitmap croppedBitmap;

    public PhotoCroppingPage ()
    {
        InitializeComponent ();

        SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

        photoCropper = new PhotoCropperCanvasView(bitmap);
        canvasViewHost.Children.Add(photoCropper);
    }

    void OnDoneButtonClicked(object sender, EventArgs args)
    {
        croppedBitmap = photoCropper.CroppedBitmap;

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
        canvas.DrawBitmap(croppedBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

El usuario, a continuación, puede manipular el rectángulo de recorte:

[![Foto de puntero Recortar 1](cropping-images/PhotoCropping1.png "foto puntero Recortar 1")](cropping-images/PhotoCropping1-Large.png#lightbox)

Si se ha definido un rectángulo de recorte válido, haga clic en el **realiza** botón. El `Clicked` controlador obtiene el mapa de bits recortado desde el `CroppedBitmap` propiedad de `PhotoCropperCanvasView`y reemplaza todo el contenido de la página con un nuevo `SKCanvasView` objeto que muestra este mapa de bits recortado:

[![Foto de puntero Recortar 2](cropping-images/PhotoCropping2.png "puntero Recortar 2 de fotografías")](cropping-images/PhotoCropping2-Large.png#lightbox)

Pruebe a establecer el segundo argumento de `PhotoCropperCanvasView` a 1.78f (por ejemplo):

```csharp
photoCropper = new PhotoCropperCanvasView(bitmap, 1.78f);
```

Verá el rectángulo de recorte restringido a una relación de aspecto de 16 a 9 característico de televisión de alta definición.

<a name="tile-division" />

## <a name="dividing-a-bitmap-into-tiles"></a>Dividir un mapa de bits en los iconos

Una versión de Xamarin.Forms de la famosa 14 y 15 rompecabezas aparecieron en 22 capítulo del libro [ _Creating Mobile Apps with Xamarin.Forms_ ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) y se pueden descargar como [  **XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle). Sin embargo, el rompecabezas pasa a ser más divertido (y a menudo más difícil) cuando se basa en una imagen de su propia biblioteca de fotos.

Esta versión del 14 y 15 rompecabezas es parte de la **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** aplicación y consta de una serie de páginas denominado **foto rompecabezas**.

El **PhotoPuzzlePage1.xaml** archivo consta de un `Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoPuzzlePage1"
             Title="Photo Puzzle">
    
    <Button Text="Pick a photo from your library"
            VerticalOptions="CenterAndExpand" 
            HorizontalOptions="CenterAndExpand"
            Clicked="OnPickButtonClicked"/>
    
</ContentPage>
```

El archivo de código subyacente implementa una `Clicked` controlador que usa el `IPhotoLibrary` servicio de dependencia para permitir que el usuario tome una foto de la biblioteca de fotos:

```csharp
public partial class PhotoPuzzlePage1 : ContentPage
{
    public PhotoPuzzlePage1 ()
    {
        InitializeComponent ();
    }

    async void OnPickButtonClicked(object sender, EventArgs args)
    {
        IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();
        using (Stream stream = await photoLibrary.PickPhotoAsync())
        {
            if (stream != null)
            {
                SKBitmap bitmap = SKBitmap.Decode(stream);

                await Navigation.PushAsync(new PhotoPuzzlePage2(bitmap));
            }
        }
    }
}
```

El método, a continuación, navega a `PhotoPuzzlePage2`, pasando en el constructor del mapa de bits seleccionado.

Es posible que la foto seleccionada de la biblioteca no está orientada tal y como aparece en la biblioteca de fotos, pero se gira o al revés. (Esto es especialmente un problema con dispositivos iOS). Por ese motivo, `PhotoPuzzlePage2` permite girar la imagen a la orientación deseada. El archivo XAML contiene tres botones etiquetados **90&#x00B0; derecha** (es decir, hacia la derecha), **90&#x00B0; izquierda** (izquierda) y **realiza**.

El archivo de código subyacente implementa la lógica de rotación del mapa de bits que se muestra en el artículo  **[creación y dibujo de SkiaSharp Bitmaps](drawing.md#rotating-bitmaps)** . El usuario puede girar la imagen 90 grados hacia la derecha o hacia la izquierda cualquier número de veces: 

```csharp
public partial class PhotoPuzzlePage2 : ContentPage
{
    SKBitmap bitmap;

    public PhotoPuzzlePage2 (SKBitmap bitmap)
    {
        this.bitmap = bitmap;

        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnRotateRightButtonClicked(object sender, EventArgs args)
    {
        SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear();
            canvas.Translate(bitmap.Height, 0);
            canvas.RotateDegrees(90);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = rotatedBitmap;
        canvasView.InvalidateSurface();
    }

    void OnRotateLeftButtonClicked(object sender, EventArgs args)
    {
        SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear();
            canvas.Translate(0, bitmap.Width);
            canvas.RotateDegrees(-90);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = rotatedBitmap;
        canvasView.InvalidateSurface();
    }

    async void OnDoneButtonClicked(object sender, EventArgs args)
    {
        await Navigation.PushAsync(new PhotoPuzzlePage3(bitmap));
    }
}
```

Cuando el usuario hace clic en el **realiza** botón, el `Clicked` controlador navega a `PhotoPuzzlePage3`, pasando el mapa de bits girado final en el constructor de la página.

`PhotoPuzzlePage3` permite la foto debe recortarse. El programa requiere un mapa de bits cuadrado para dividir en una cuadrícula de mosaicos de 4 por 4.

El **PhotoPuzzlePage3.xaml** archivo contiene un `Label`, un `Grid` al host la `PhotoCropperCanvasView`y otro **realiza** botón:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoPuzzlePage3"
             Title="Photo Puzzle">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Label Text="Crop the photo to a square"
               Grid.Row="0"
               FontSize="Large"
               HorizontalTextAlignment="Center"
               Margin="5" />

        <Grid x:Name="canvasViewHost"
              Grid.Row="1"
              BackgroundColor="Gray"
              Padding="5" />

        <Button Text="Done"
                Grid.Row="2"
                HorizontalOptions="Center"
                Margin="5"
                Clicked="OnDoneButtonClicked" />
    </Grid>
</ContentPage>
```

Crea una instancia en el archivo de código subyacente del `PhotoCropperCanvasView` con el mapa de bits que se pasa al constructor. Tenga en cuenta que 1 se pasa como segundo argumento de `PhotoCropperCanvasView`. Esta relación de aspecto 1 fuerza el rectángulo de recorte como un cuadrado:

```csharp
public partial class PhotoPuzzlePage3 : ContentPage
{
    PhotoCropperCanvasView photoCropper;

    public PhotoPuzzlePage3(SKBitmap bitmap)
    {
        InitializeComponent ();

        photoCropper = new PhotoCropperCanvasView(bitmap, 1f);
        canvasViewHost.Children.Add(photoCropper);
    }

    async void OnDoneButtonClicked(object sender, EventArgs args)
    {
        SKBitmap croppedBitmap = photoCropper.CroppedBitmap;
        int width = croppedBitmap.Width / 4;
        int height = croppedBitmap.Height / 4;

        ImageSource[] imgSources = new ImageSource[15];

        for (int row = 0; row < 4; row++)
        {
            for (int col = 0; col < 4; col++)
            {
                // Skip the last one!
                if (row == 3 && col == 3)
                    break;

                // Create a bitmap 1/4 the width and height of the original
                SKBitmap bitmap = new SKBitmap(width, height);
                SKRect dest = new SKRect(0, 0, width, height);
                SKRect source = new SKRect(col * width, row * height, (col + 1) * width, (row + 1) * height);

                // Copy 1/16 of the original into that bitmap
                using (SKCanvas canvas = new SKCanvas(bitmap))
                {
                    canvas.DrawBitmap(croppedBitmap, source, dest);
                }

                imgSources[4 * row + col] = (SKBitmapImageSource)bitmap;
            }
        }

        await Navigation.PushAsync(new PhotoPuzzlePage4(imgSources));
    }
}
```

El **realiza** controlador de botón Obtiene el ancho y alto del mapa de bits recortado (estos dos valores deben ser la misma) y, a continuación, se divide en 15 mapas de bits independientes, cada uno de los cuales es 1/4 el ancho y alto del original. (No se crea el último de los mapas de bits de 16 posibles). El `DrawBitmap` método con el rectángulo de origen y de destino permite un mapa de bits se crean en función de subconjunto de un mapa de bits mayor.

## <a name="converting-to-xamarinforms-bitmaps"></a>Convertir en mapas de bits de Xamarin.Forms

En el `OnDoneButtonClicked` método, la matriz creada para los mapas de 15 bits es de tipo [ `ImageSource` ](xref:Xamarin.Forms.ImageSource):

```csharp
ImageSource[] imgSources = new ImageSource[15];
```

`ImageSource` es el tipo de base de Xamarin.Forms que encapsula un mapa de bits. Afortunadamente, SkiaSharp permite la conversión de mapas de bits de SkiaSharp en Xamarin.Forms mapas de bits. El **SkiaSharp.Views.Forms** ensamblado define un [ `SKBitmapImageSource` ](xref:SkiaSharp.Views.Forms.SKBitmapImageSource) clase que derive de `ImageSource` pero se pueden crear en función de un SkiaSharp `SKBitmap` objeto. `SKBitmapImageSource` incluso define las conversiones entre `SKBitmapImageSource` y `SKBitmap`y de cómo `SKBitmap` objetos se almacenan en una matriz como mapas de bits de Xamarin.Forms:

```csharp
imgSources[4 * row + col] = (SKBitmapImageSource)bitmap;
```

Esta matriz de mapas de bits se pasa como un constructor para `PhotoPuzzlePage4`. Dicha página está completamente Xamarin.Forms y no usa ningún SkiaSharp. Es muy similar a [ **XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle), por lo que no se describen aquí, pero muestra la foto seleccionada dividida en mosaicos cuadrados 15:

[![Foto de rompecabezas 1](cropping-images/PhotoPuzzle1.png "foto rompecabezas 1")](cropping-images/PhotoPuzzle1-Large.png#lightbox)

Al presionar el **Randomize** botón mezcla copia todos los iconos:

[![Foto 2 rompecabezas](cropping-images/PhotoPuzzle2.png "rompecabezas 2 de fotografías")](cropping-images/PhotoPuzzle2-Large.png#lightbox)

Ahora puede colocarlas en el orden correcto. Los iconos en la misma fila o columna como cuadrado en blanco pueden ser punteados moverlos a cuadrado en blanco. 

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
