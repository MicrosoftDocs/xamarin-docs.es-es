---
title: Recortar mapas de bits de SkiaSharp
description: Obtenga información sobre cómo usar SkiaSharp para diseñar una interfaz de usuario para enumerador un rectángulo de recorte interactivamente.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 0A79AB27-C69F-4376-8FFE-FF46E4783F30
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 639b9db51d4a9f0bb0ddd55a3d35bcbda7e31962
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366469"
---
# <a name="cropping-skiasharp-bitmaps"></a>Recortar mapas de bits de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

En el artículo [**creación y dibujo de mapas de bits de SkiaSharp**](drawing.md) se describe cómo se `SKBitmap` puede pasar un objeto a un `SKCanvas` constructor. Cualquier método de dibujo llamado en ese lienzo hace que los gráficos se representen en el mapa de bits. Estos métodos de dibujo incluyen `DrawBitmap` , lo que significa que esta técnica permite transferir parte o todo un mapa de bits a otro mapa de bits, quizás con las transformaciones aplicadas.

Puede utilizar esa técnica para recortar un mapa de bits llamando al [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) método con los rectángulos de origen y de destino:

```csharp
canvas.DrawBitmap(bitmap, sourceRect, destRect);
```

Sin embargo, las aplicaciones que implementan el recorte a menudo proporcionan una interfaz para que el usuario seleccione de forma interactiva el rectángulo de recorte:

![Ejemplo de recorte](cropping-images/CroppingSample.png "Ejemplo de recorte")

Este artículo se centra en la interfaz.

## <a name="encapsulating-the-cropping-rectangle"></a>Encapsular el rectángulo de recorte

Resulta útil aislar parte de la lógica de recorte en una clase denominada `CroppingRectangle` . Los parámetros de constructor incluyen un rectángulo máximo, que suele ser el tamaño del mapa de bits que se va a recortar y una relación de aspecto opcional. El constructor define primero un rectángulo de recorte inicial, que hace público en la `Rect` propiedad de tipo `SKRect` . Este rectángulo de recorte inicial es 80% del ancho y alto del rectángulo de mapa de bits, pero se ajusta después si se especifica una relación de aspecto:

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

Una parte útil de la información que `CroppingRectangle` también está disponible es una matriz de `SKPoint` valores que corresponden a las cuatro esquinas del rectángulo de recorte en el orden superior izquierda, superior derecha, inferior derecha e inferior izquierda:

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

Esta matriz se utiliza en el método siguiente, que se denomina `HitTest` . El `SKPoint` parámetro es un punto que corresponde a un toque de dedo o un clic del mouse. El método devuelve un índice (0, 1, 2 o 3) que corresponde a la esquina que el dedo o el puntero del mouse tocan, dentro de una distancia dada por el `radius` parámetro:

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

Si el toque o el punto del mouse no estaban dentro `radius` de las unidades de cualquier esquina, el método devuelve &ndash; 1.

Se llama al método final de `CroppingRectangle` `MoveCorner` , al que se llama en respuesta a la entrada táctil o al movimiento del mouse. Los dos parámetros indican el índice de la esquina que se está moviendo y la nueva ubicación de dicha esquina. La primera mitad del método ajusta el rectángulo de recorte basándose en la nueva ubicación de la esquina, pero siempre dentro de los límites de `maxRect` , que es el tamaño del mapa de bits. Esta lógica también tiene en cuenta el `MINIMUM` campo para evitar la contracción del rectángulo de recorte en nada:

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

Tenga en cuenta que todo lo que hay en esta clase se encuentra en unidades de píxeles.

## <a name="a-canvas-view-just-for-cropping"></a>Una vista de lienzo solo para recortar

La clase `CroppingRectangle` que acaba de visualizar se usa en la `PhotoCropperCanvasView` clase, que se deriva de `SKCanvasView` . Esta clase es responsable de mostrar el mapa de bits y el rectángulo de recorte, así como controlar los eventos táctiles o de mouse para cambiar el rectángulo de recorte.

El `PhotoCropperCanvasView` constructor requiere un mapa de bits. Una relación de aspecto es opcional. El constructor crea una instancia de un objeto de tipo `CroppingRectangle` basado en este mapa de bits y relación de aspecto y lo guarda como un campo:

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

Dado que esta clase deriva de `SKCanvasView` , no es necesario instalar un controlador para el `PaintSurface` evento. En su lugar, puede invalidar su `OnPaintSurface` método. El método muestra el mapa de bits y utiliza un par de `SKPaint` objetos guardados como campos para dibujar el rectángulo de recorte actual:

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

El código de la `CroppingRectangle` clase basa el rectángulo de recorte en el tamaño de píxel del mapa de bits. Sin embargo, la visualización del mapa de bits mediante la `PhotoCropperCanvasView` clase se escala en función del tamaño del área de presentación. El `bitmapScaleMatrix` calculado en la `OnPaintSurface` invalidación se asigna desde píxeles del mapa de bits hasta el tamaño y la posición del mapa de bits tal y como se muestra. Esta matriz se utiliza para transformar el rectángulo de recorte de modo que se pueda mostrar en relación con el mapa de bits.

La última línea de la `OnPaintSurface` invalidación toma el inverso de `bitmapScaleMatrix` y lo guarda como el `inverseBitmapMatrix` campo. Se utiliza para el procesamiento táctil.

`TouchEffect`Se crea una instancia de un objeto como un campo y el constructor adjunta un controlador al `TouchAction` evento, pero `TouchEffect` debe agregarse a la `Effects` colección del _elemento primario_ del `SKCanvasView` derivado, por lo que se realiza en la `OnParentSet` invalidación:

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

Los eventos Touch procesados por el `TouchAction` controlador se encuentran en unidades independientes del dispositivo. Estos primeros deben convertirse en píxeles mediante el `ConvertToPixel` método situado en la parte inferior de la clase y, a continuación, convertirse en `CroppingRectangle` unidades mediante `inverseBitmapMatrix` .

En el caso de los `Pressed` eventos, el `TouchAction` controlador llama al `HitTest` método de `CroppingRectangle` . Si esto devuelve un índice distinto de &ndash; 1, se está manipulando una de las esquinas del rectángulo de recorte. Ese índice y un desplazamiento del punto de toque real de la esquina se almacenan en un `TouchPoint` objeto y se agregan al `touchPoints` Diccionario.

En el `Moved` caso del evento, `MoveCorner` se llama al método de `CroppingRectangle` para trasladar la esquina, con posibles ajustes para la relación de aspecto.

En cualquier momento, un programa `PhotoCropperCanvasView` que use puede tener acceso a la `CroppedBitmap` propiedad. Esta propiedad usa la `Rect` propiedad de `CroppingRectangle` para crear un nuevo mapa de bits del tamaño recortado. La versión de `DrawBitmap` con los rectángulos de origen y de destino extrae un subconjunto del mapa de bits original:

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

## <a name="hosting-the-photo-cropper-canvas-view"></a>Hospedaje de la vista de lienzo Photo Cropper

Con esas dos clases que controlan la lógica de recorte, la página de **recorte de fotografías** en la aplicación **[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** tiene muy poco trabajo. El archivo XAML crea una instancia de `Grid` para hospedar el `PhotoCropperCanvasView` y un botón **Done** :

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

`PhotoCropperCanvasView`No se puede crear una instancia del archivo XAML porque requiere un parámetro de tipo `SKBitmap` .

En su lugar, `PhotoCropperCanvasView` se crea una instancia del en el constructor del archivo de código subyacente utilizando uno de los mapas de bits de recursos:

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

Después, el usuario puede manipular el rectángulo de recorte:

[![Foto Cropper 1](cropping-images/PhotoCropping1.png "Foto Cropper 1")](cropping-images/PhotoCropping1-Large.png#lightbox)

Cuando se haya definido un buen rectángulo de recorte, haga clic en el botón **listo** . El `Clicked` controlador obtiene el mapa de bits recortado de la `CroppedBitmap` propiedad de `PhotoCropperCanvasView` y reemplaza todo el contenido de la página por un nuevo `SKCanvasView` objeto que muestra este mapa de bits recortado:

[![Foto Cropper 2](cropping-images/PhotoCropping2.png "Foto Cropper 2")](cropping-images/PhotoCropping2-Large.png#lightbox)

Pruebe a establecer el segundo argumento de `PhotoCropperCanvasView` en 1,78 f (por ejemplo):

```csharp
photoCropper = new PhotoCropperCanvasView(bitmap, 1.78f);
```

Verá que el rectángulo de recorte está restringido a una característica de relación de aspecto de 16 a 9 de televisión de alta definición.

## <a name="dividing-a-bitmap-into-tiles"></a>Dividir un mapa de bits en mosaicos

Una Xamarin.Forms versión del rompecabezas 14-15 famoso apareció en el capítulo 22 del libro [_creación de Mobile Apps con Xamarin.Forms_](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) y se puede descargar como [**XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle). Sin embargo, el rompecabezas se vuelve más divertido (y a menudo más desafiante) cuando se basa en una imagen de su propia biblioteca de fotos.

Esta versión del rompecabezas 14-15 forma parte de la aplicación **[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** y consta de una serie de páginas con el título **Photo Puzzle**.

El archivo **PhotoPuzzlePage1. Xaml** se compone de `Button` :

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

El archivo de código subyacente implementa un `Clicked` controlador que utiliza el `IPhotoLibrary` servicio de dependencia para permitir que el usuario elija una foto de la biblioteca de fotos:

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

A continuación, el método navega a `PhotoPuzzlePage2` , pasando al constructor el mapa de bits seleccionado.

Es posible que la foto seleccionada de la biblioteca no esté orientada a medida que aparecía en la biblioteca de fotos, sino que esté girada o en paralelo. (Esto es especialmente un problema con dispositivos iOS). Por ese motivo, `PhotoPuzzlePage2` le permite girar la imagen a una orientación deseada. El archivo XAML contiene tres botones con la etiqueta **90&#x00B0; derecha** (lo que significa el sentido de las agujas del reloj), **90&#x00B0; izquierda** (en sentido contrario a las agujas del reloj) y **listo**.

El archivo de código subyacente implementa la lógica de rotación del mapa de bits que se muestra en el artículo **[crear y dibujar en mapas de bits de SkiaSharp](drawing.md#rotating-bitmaps)**. El usuario puede girar la imagen 90 grados en el sentido de las agujas del reloj o en el sentido contrario a las agujas del reloj:

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

Cuando el usuario hace clic en el botón **Done** , el `Clicked` controlador navega hasta `PhotoPuzzlePage3` y pasa el mapa de bits girado final en el constructor de la página.

`PhotoPuzzlePage3` permite recortar la foto. El programa requiere un mapa de bits cuadrado para dividirlo en una cuadrícula de 4 por 4 de mosaicos.

El archivo **PhotoPuzzlePage3. Xaml** contiene un `Label` , un `Grid` para hospedar `PhotoCropperCanvasView` y otro botón **listo** :

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

El archivo de código subyacente crea una instancia de `PhotoCropperCanvasView` con el mapa de bits que se pasa a su constructor. Observe que se pasa 1 como el segundo argumento a `PhotoCropperCanvasView` . Esta relación de aspecto de 1 fuerza que el rectángulo de recorte sea un cuadrado:

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

El controlador de botón **Done** obtiene el ancho y el alto del mapa de bits recortado (estos dos valores deben ser iguales) y, a continuación, lo divide en 15 mapas de bits independientes, cada uno de los cuales es 1/4 el ancho y el alto del original. (No se crea el último de los 16 mapas de bits posibles). El `DrawBitmap` método con el rectángulo de origen y de destino permite crear un mapa de bits basado en un subconjunto de un mapa de bits mayor.

## <a name="converting-to-no-locxamarinforms-bitmaps"></a>Convertir en Xamarin.Forms mapas de bits

En el `OnDoneButtonClicked` método, la matriz creada para los 15 mapas de bits es de tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource) :

```csharp
ImageSource[] imgSources = new ImageSource[15];
```

`ImageSource` es el Xamarin.Forms tipo base que encapsula un mapa de bits. Afortunadamente, SkiaSharp permite convertir mapas de bits de SkiaSharp en Xamarin.Forms mapas de bits. El ensamblado **SkiaSharp. views. Forms** define una [`SKBitmapImageSource`](xref:SkiaSharp.Views.Forms.SKBitmapImageSource) clase que `ImageSource` se deriva de pero que se puede crear basándose en un `SKBitmap` objeto SkiaSharp. `SKBitmapImageSource` incluso define las conversiones entre `SKBitmapImageSource` y `SKBitmap` , y así es como `SKBitmap` se almacenan los objetos en una matriz como Xamarin.Forms mapas de bits:

```csharp
imgSources[4 * row + col] = (SKBitmapImageSource)bitmap;
```

Esta matriz de mapas de bits se pasa como un constructor a `PhotoPuzzlePage4` . Esa página es completamente Xamarin.Forms y no usa ningún SkiaSharp. Es muy similar a [**XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle), por lo que no se describe aquí, pero muestra la foto seleccionada dividida en 15 mosaicos cuadrados:

[![Foto Puzzle 1](cropping-images/PhotoPuzzle1.png "Foto Puzzle 1")](cropping-images/PhotoPuzzle1-Large.png#lightbox)

Al presionar el botón **aleatorizar** se mezclan todos los mosaicos:

[![Rompecabezas fotográfico 2](cropping-images/PhotoPuzzle2.png "Rompecabezas fotográfico 2")](cropping-images/PhotoPuzzle2-Large.png#lightbox)

Ahora puede volver a colocarlos en el orden correcto. Los mosaicos de la misma fila o columna que el cuadrado en blanco se pueden puntear para moverlos al cuadrado en blanco.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)