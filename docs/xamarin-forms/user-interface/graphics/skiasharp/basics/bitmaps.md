---
title: Conceptos básicos de mapas de bits en SkiaSharp
description: En este artículo se explica cómo cargar mapas de bits en SkiaSharp desde varios orígenes y mostrarlos en Xamarin.Forms aplicaciones, y cómo hacerlo con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 32C95DFF-9065-42D7-966C-D3DBD16906B3
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 440a078857a940f57f5700a18f91fc8b25bdfae8
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937207"
---
# <a name="bitmap-basics-in-skiasharp"></a>Conceptos básicos de mapas de bits en SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Cargar mapas de bits de varios orígenes y mostrarlos._

La compatibilidad de los mapas de bits en SkiaSharp es bastante extensa. En este artículo solo se tratan los aspectos básicos de &mdash; Cómo cargar mapas de bits y cómo mostrarlos:

![La presentación de dos mapas de bits](bitmaps-images/basicbitmaps-small.png)

En la sección [mapas de bits de SkiaSharp](../bitmaps/index.md)se puede encontrar una exploración mucho más profunda de los mapas de bits.

Un mapa de bits SkiaSharp es un objeto de tipo [`SKBitmap`](xref:SkiaSharp.SKBitmap) . Hay muchas maneras de crear un mapa de bits, pero este artículo se restringe a sí mismo al [`SKBitmap.Decode`](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream)) método, que carga el mapa de bits desde un `Stream` objeto .net.

La página de **mapas de bits básicos** del programa **SkiaSharpFormsDemos** muestra cómo cargar mapas de bits de tres orígenes diferentes:

- Desde Internet
- Desde un recurso incrustado en el archivo ejecutable
- Desde la biblioteca de fotos del usuario

Tres `SKBitmap` objetos para estos tres orígenes se definen como campos en la [`BasicBitmapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/BasicBitmapsPage.cs) clase:

```csharp
public class BasicBitmapsPage : ContentPage
{
    SKCanvasView canvasView;
    SKBitmap webBitmap;
    SKBitmap resourceBitmap;
    SKBitmap libraryBitmap;

    public BasicBitmapsPage()
    {
        Title = "Basic Bitmaps";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
        ...
    }
    ...
}
```

## <a name="loading-a-bitmap-from-the-web"></a>Cargar un mapa de bits desde la web

Para cargar un mapa de bits basado en una dirección URL, puede usar la [`HttpClient`](/dotnet/api/system.net.http.httpclient?view=netstandard-2.0) clase. Solo debe crear una instancia de `HttpClient` y reutilizarla, por lo que debe almacenarla como un campo:

```csharp
HttpClient httpClient = new HttpClient();
```

Al usar `HttpClient` con aplicaciones iOS y Android, querrá establecer las propiedades del proyecto tal y como se describe en los documentos sobre la seguridad de la **[capa de transporte (TLS) 1,2](~/cross-platform/app-fundamentals/transport-layer-security.md)**.

Dado que es más conveniente usar el `await` operador con `HttpClient` , el código no se puede ejecutar en el `BasicBitmapsPage` constructor. En su lugar, es parte de la `OnAppearing` invalidación. La dirección URL aquí apunta a un área del sitio web de Xamarin con algunos mapas de bits de ejemplo. Un paquete en el sitio web permite anexar una especificación para cambiar el tamaño del mapa de bits a un ancho determinado:

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    // Load web bitmap.
    string url = "https://developer.xamarin.com/demo/IMG_3256.JPG?width=480";

    try
    {
        using (Stream stream = await httpClient.GetStreamAsync(url))
        using (MemoryStream memStream = new MemoryStream())
        {
            await stream.CopyToAsync(memStream);
            memStream.Seek(0, SeekOrigin.Begin);

            webBitmap = SKBitmap.Decode(memStream);
            canvasView.InvalidateSurface();
        };
    }
    catch
    {
    }
}
```

El sistema operativo Android genera una excepción cuando se usa la `Stream` devuelta desde `GetStreamAsync` en el `SKBitmap.Decode` método porque se está realizando una operación larga en un subproceso principal. Por esta razón, el contenido del archivo de mapa de bits se copia en un `MemoryStream` objeto mediante `CopyToAsync` .

El `SKBitmap.Decode` método estático es responsable de descodificar los archivos de mapa de bits. Funciona con formatos de mapa de bits JPEG, PNG y GIF, y almacena los resultados en un formato SkiaSharp interno. En este momento, `SKCanvasView` se debe invalidar para permitir que el `PaintSurface` controlador actualice la pantalla.

## <a name="loading-a-bitmap-resource"></a>Cargar un recurso de mapa de bits

En términos de código, el enfoque más sencillo para cargar mapas de bits consiste en incluir directamente un recurso de mapa de bits en la aplicación. El programa **SkiaSharpFormsDemos** incluye una carpeta denominada **medios** que contiene varios archivos de mapa de bits, incluido uno denominado **monkey.png**. En el caso de los mapas de bits almacenados como recursos de programa, debe usar el cuadro de diálogo **propiedades** para dar al archivo una **acción de compilación** de **recurso incrustado**.

Cada recurso incrustado tiene un *identificador de recurso* que está formado por el nombre del proyecto, la carpeta y el nombre de archivo, todo ello conectado por puntos: **SkiaSharpFormsDemos.Media.monkey.png**. Puede obtener acceso a este recurso especificando ese identificador de recurso como argumento [`GetManifestResourceStream`](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) del método de la [`Assembly`](xref:System.Reflection.Assembly) clase:

```csharp
string resourceID = "SkiaSharpFormsDemos.Media.monkey.png";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    resourceBitmap = SKBitmap.Decode(stream);
}
```

Este `Stream` objeto se puede pasar directamente al `SKBitmap.Decode` método.

## <a name="loading-a-bitmap-from-the-photo-library"></a>Cargar un mapa de bits desde la biblioteca de fotos

También es posible que el usuario cargue una foto de la biblioteca de imágenes del dispositivo. Esta instalación no se proporciona por Xamarin.Forms sí misma. El trabajo requiere un servicio de dependencia, como el que se describe en el artículo [seleccionar una foto de la biblioteca de imágenes](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

El archivo **IPhotoLibrary.CS** del proyecto **SkiaSharpFormsDemos** y los tres archivos **PhotoLibrary.CS** de los proyectos de la plataforma se han adaptado a ese artículo. Además, el archivo **MainActivity.CS** de Android se ha modificado como se describe en el artículo y el proyecto de iOS tiene permiso para tener acceso a la biblioteca de fotos con dos líneas hacia la parte inferior del archivo **info. plist** .

El `BasicBitmapsPage` constructor agrega `TapGestureRecognizer` a para recibir `SKCanvasView` notificaciones de los grifos. Al recibir una derivación, el `Tapped` controlador obtiene acceso al servicio de dependencia del selector de imágenes y llama a `PickPhotoAsync` . Si `Stream` se devuelve un objeto, se pasa al `SKBitmap.Decode` método:

```csharp
// Add tap gesture recognizer
TapGestureRecognizer tapRecognizer = new TapGestureRecognizer();
tapRecognizer.Tapped += async (sender, args) =>
{
    // Load bitmap from photo library
    IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();

    using (Stream stream = await photoLibrary.PickPhotoAsync())
    {
        if (stream != null)
        {
            libraryBitmap = SKBitmap.Decode(stream);
            canvasView.InvalidateSurface();
        }
    }
};
canvasView.GestureRecognizers.Add(tapRecognizer);
```

Observe que el `Tapped` controlador también llama al `InvalidateSurface` método del `SKCanvasView` objeto. Esto genera una nueva llamada al `PaintSurface` controlador.

## <a name="displaying-the-bitmaps"></a>Mostrar los mapas de bits

El `PaintSurface` controlador necesita mostrar tres mapas de bits. El controlador supone que el teléfono está en modo vertical y divide el lienzo verticalmente en tres partes iguales.

El primer mapa de bits se muestra con el [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) método más sencillo. Lo único que debe especificar son las coordenadas X e y donde se va a colocar la esquina superior izquierda del mapa de bits:

```csharp
public void DrawBitmap (SKBitmap bitmap, Single x, Single y, SKPaint paint = null)
```

Aunque `SKPaint` se define un parámetro, tiene un valor predeterminado de `null` y puede pasarlo por alto. Los píxeles del mapa de bits se transfieren simplemente a los píxeles de la superficie de presentación con una asignación uno a uno. Verá una aplicación para este `SKPaint` argumento en la siguiente sección sobre la [**transparencia de SkiaSharp**](transparency.md).

Un programa puede obtener las dimensiones de píxeles de un mapa de bits con las [`Width`](xref:SkiaSharp.SKBitmap.Width) [`Height`](xref:SkiaSharp.SKBitmap.Height) propiedades y. Estas propiedades permiten al programa calcular las coordenadas para colocar el mapa de bits en el centro del tercio superior del lienzo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    if (webBitmap != null)
    {
        float x = (info.Width - webBitmap.Width) / 2;
        float y = (info.Height / 3 - webBitmap.Height) / 2;
        canvas.DrawBitmap(webBitmap, x, y);
    }
    ...
}
```

Los otros dos mapas de bits se muestran con una versión de [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) con un `SKRect` parámetro:

```csharp
public void DrawBitmap (SKBitmap bitmap, SKRect dest, SKPaint paint = null)
```

Una tercera versión de [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) tiene dos `SKRect` argumentos para especificar un subconjunto rectangular del mapa de bits que se va a mostrar, pero esa versión no se usa en este artículo.

Este es el código para mostrar el mapa de bits cargado desde un mapa de bits de recursos incrustados:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    if (resourceBitmap != null)
    {
        canvas.DrawBitmap(resourceBitmap,
            new SKRect(0, info.Height / 3, info.Width, 2 * info.Height / 3));
    }
    ...
}
```

El mapa de bits se ajusta a las dimensiones del rectángulo, por lo que el Monkey se ajusta horizontalmente en estas capturas de pantallas:

[![Captura de pantalla triple de la página de mapas de bits básicos](bitmaps-images/basicbitmaps-small.png)](bitmaps-images/basicbitmaps-large.png#lightbox "Captura de pantalla triple de la página de mapas de bits básicos")

La tercera imagen &mdash; que solo puede ver si ejecuta el programa y carga una foto desde su propia biblioteca de imágenes &mdash; también se muestra dentro de un rectángulo, pero el tamaño y la posición del rectángulo se ajustan para mantener la relación de aspecto del mapa de bits. Este cálculo es un poco más complicado porque requiere calcular un factor de escala basado en el tamaño del mapa de bits y el rectángulo de destino, y centrar el rectángulo en esa área:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    if (libraryBitmap != null)
    {
        float scale = Math.Min((float)info.Width / libraryBitmap.Width,
                               info.Height / 3f / libraryBitmap.Height);

        float left = (info.Width - scale * libraryBitmap.Width) / 2;
        float top = (info.Height / 3 - scale * libraryBitmap.Height) / 2;
        float right = left + scale * libraryBitmap.Width;
        float bottom = top + scale * libraryBitmap.Height;
        SKRect rect = new SKRect(left, top, right, bottom);
        rect.Offset(0, 2 * info.Height / 3);

        canvas.DrawBitmap(libraryBitmap, rect);
    }
    else
    {
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = SKColors.Blue;
            paint.TextAlign = SKTextAlign.Center;
            paint.TextSize = 48;

            canvas.DrawText("Tap to load bitmap",
                info.Width / 2, 5 * info.Height / 6, paint);
        }
    }
}
```

Si todavía no se ha cargado ningún mapa de bits de la biblioteca de imágenes, el `else` bloque muestra texto para pedir al usuario que puntee en la pantalla.

Puede mostrar mapas de bits con varios grados de transparencia y en el siguiente artículo sobre la [**transparencia de SkiaSharp**](transparency.md) se describe cómo hacerlo.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Seleccionar una foto de la biblioteca de imágenes](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
