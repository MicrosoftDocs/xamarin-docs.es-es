---
title: Conceptos básicos de mapa de bits de SkiaSharp
description: En este artículo se explica cómo cargar mapas de bits de SkiaSharp procedentes de diversos orígenes y mostrarlos en las aplicaciones de Xamarin.Forms y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 32C95DFF-9065-42D7-966C-D3DBD16906B3
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 80f2b686e9802a93b0cf32420ccaef3e8877727c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759553"
---
# <a name="bitmap-basics-in-skiasharp"></a>Conceptos básicos de mapa de bits de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Cargar mapas de bits de varios orígenes y mostrarlos._

La compatibilidad de mapas de bits de SkiaSharp es bastante extensa. Este artículo trata solo de los conceptos básicos &mdash; cargar mapas de bits y cómo mostrarlos:

![](bitmaps-images/basicbitmaps-small.png "La presentación de dos mapas de bits")

Encontrará una explicación mucho más profunda de mapas de bits en la sección [SkiaSharp Bitmaps](../bitmaps/index.md).

Un mapa de bits de SkiaSharp es un objeto de tipo [ `SKBitmap` ](xref:SkiaSharp.SKBitmap). Hay muchas maneras de crear un mapa de bits, pero en este artículo se restringe a sí mismo a la [ `SKBitmap.Decode` ](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream)) método, que carga el mapa de bits de .NET `Stream` objeto.

El **básica de mapas de bits** página en el **SkiaSharpFormsDemos** programa muestra cómo cargar mapas de bits de tres orígenes diferentes:

- Más de Internet
- Desde un recurso incrustado en el archivo ejecutable
- Desde la biblioteca de fotos del usuario

Tres `SKBitmap` objetos para estos tres orígenes se definen como campos en el [ `BasicBitmapsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/BasicBitmapsPage.cs) clase:

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

## <a name="loading-a-bitmap-from-the-web"></a>Carga un mapa de bits de la Web

Para cargar un mapa de bits en función de una dirección URL, puede usar el [ `HttpClient` ](/dotnet/api/system.net.http.httpclient?view=netstandard-2.0) clase. Debe crear una instancia de una única instancia de `HttpClient` y volver a usarla, así que la almacene como un campo:

```csharp
HttpClient httpClient = new HttpClient();
```

Cuando se usa `HttpClient` con aplicaciones iOS y Android, desea establecer las propiedades del proyecto, como se describe en los documentos en  **[seguridad de capa de transporte (TLS) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md)** .

Porque es más conveniente utilizar el `await` operador con `HttpClient`, no se puede ejecutar el código en el `BasicBitmapsPage` constructor. En su lugar, forma parte de la `OnAppearing` invalidar. Aquí la dirección URL apunta a un área en el sitio web de Xamarin con algunos mapas de bits de ejemplo. Un paquete en el sitio web permite anexar una especificación para cambiar el tamaño de mapa de bits a un ancho determinado:

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

            webBitmap = SKBitmap.Decode(stream);
            canvasView.InvalidateSurface();
        };
    }
    catch
    {
    }
}
```

El sistema operativo Android produce una excepción cuando se usa el `Stream` devuelto desde `GetStreamAsync` en el `SKBitmap.Decode` método porque está realizando una operación larga en un subproceso principal. Por este motivo, se copia el contenido del archivo de mapa de bits en un `MemoryStream` objeto `CopyToAsync`.

Estático `SKBitmap.Decode` método es responsable de descodificación de archivos de mapa de bits. Funciona con los formatos de mapa de bits GIF, PNG y JPEG y almacena los resultados en un formato interno de SkiaSharp. En este momento, el `SKCanvasView` tiene que invalidar para permitir el `PaintSurface` controlador para actualizar la pantalla.

## <a name="loading-a-bitmap-resource"></a>Cargar un recurso de mapa de bits

En términos de código, el enfoque más sencillo a la carga de los mapas de bits está incluido un recurso de mapa de bits directamente en la aplicación. El **SkiaSharpFormsDemos** programa incluye una carpeta denominada **Media** que contiene varios archivos, incluido uno denominado de mapa de bits **monkey.png**. Para los mapas de bits almacenada como recursos de programa, debe usar el **propiedades** cuadro de diálogo para asignar al archivo un **acción de compilación** de **recurso incrustado**!

Cada recurso incrustado tiene un *identificador de recurso* que está formado por el nombre del proyecto, la carpeta y el nombre de archivo, todo ello conectado por puntos: **SkiaSharpFormsDemos. Media. Monkey. png**. Puede obtener acceso a este recurso mediante la especificación de ese recurso identificador como argumento a la [ `GetManifestResourceStream` ](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) método de la [ `Assembly` ](xref:System.Reflection.Assembly) clase:

```csharp
string resourceID = "SkiaSharpFormsDemos.Media.monkey.png";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    resourceBitmap = SKBitmap.Decode(stream);
}
```

Esto `Stream` objeto se puede pasar directamente a la `SKBitmap.Decode` método.

## <a name="loading-a-bitmap-from-the-photo-library"></a>Cargar un mapa de bits de la biblioteca de fotografías

También es posible que el usuario cargar una foto de la biblioteca de imágenes del dispositivo. Esta función no se proporciona mediante Xamarin.Forms propio. El trabajo requiere un servicio de dependencia, como se describe en el artículo [seleccionar una foto de la biblioteca de imágenes](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

El **IPhotoLibrary.cs** de archivos en el **SkiaSharpFormsDemos** proyecto y los tres **PhotoLibrary.cs** archivos en los proyectos de plataforma se han adaptado de dicho artículo. Además, el Android **MainActivity.cs** se ha modificado el archivo como se describe en el artículo y el proyecto de iOS se haya concedido permiso para acceder a la biblioteca de fotos con dos líneas hacia la parte inferior de la **info.plist**  archivo.

El `BasicBitmapsPage` constructor agrega un `TapGestureRecognizer` a la `SKCanvasView` para recibir una notificación de pesos. Tras la recepción de un toque, el `Tapped` controlador obtiene acceso al servicio de dependencia de selector de imagen y las llamadas `PickPhotoAsync`. Si un `Stream` se devuelve el objeto y, después, se pasa a la `SKBitmap.Decode` método:

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

Tenga en cuenta que el `Tapped` controlador también llama a la `InvalidateSurface` método de la `SKCanvasView` objeto. Esto genera una nueva llamada a la `PaintSurface` controlador.

## <a name="displaying-the-bitmaps"></a>Mostrar los mapas de bits

El `PaintSurface` controlador necesita para mostrar tres mapas de bits. El controlador se da por supuesto que el teléfono está en modo vertical y el lienzo divide verticalmente en tres partes iguales.

Se muestra el mapa de bits primero con el más sencillo [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) método. Todo lo que necesita especificar son las coordenadas X e Y donde es se coloca la esquina superior izquierda del mapa de bits:

```csharp
public void DrawBitmap (SKBitmap bitmap, Single x, Single y, SKPaint paint = null)
```

Aunque un `SKPaint` parámetro está definido, tiene un valor predeterminado de `null` y puede ignorarlo. Los píxeles del mapa de bits simplemente se transfieren a los píxeles de la superficie de pantalla con una asignación uno a uno. Verá una aplicación para este `SKPaint` argumento en la sección siguiente sobre [ **SkiaSharp transparencia**](transparency.md).

Un programa puede obtener las dimensiones en píxeles del mapa de bits con el [ `Width` ](xref:SkiaSharp.SKBitmap.Width) y [ `Height` ](xref:SkiaSharp.SKBitmap.Height) propiedades. Estas propiedades permiten que el programa calcular las coordenadas para colocar el mapa de bits en el centro de la tercera parte superior del lienzo:

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

Los otros dos mapas de bits se muestran con una versión de [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) con un `SKRect` parámetro:

```csharp
public void DrawBitmap (SKBitmap bitmap, SKRect dest, SKPaint paint = null)
```

Una tercera versión de [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) tiene dos `SKRect` argumentos para especificar un subconjunto rectangular del mapa de bits para mostrar, pero esa versión no se usa en este artículo.

Este es el código para mostrar el mapa de bits que se cargan desde un mapa de bits del recurso incrustado:

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

El mapa de bits se ajusta a las dimensiones del rectángulo, que es el motivo por el objeto monkey se expande horizontalmente en estas capturas de pantalla:

[![](bitmaps-images/basicbitmaps-small.png "Captura de pantalla triple de la página de mapas de bits básica")](bitmaps-images/basicbitmaps-large.png#lightbox "triple captura de pantalla de la página de mapas de bits básica")

La tercera imagen &mdash; que solo puede ver si ejecuta el programa y cargar una foto de su propia biblioteca de imágenes &mdash; también se muestra dentro de un rectángulo, pero el rectángulo se ajustan el tamaño y posición para mantener la relación de aspecto del mapa de bits. Este cálculo es un poco más complicado porque requiere calcular un factor en función del tamaño del mapa de bits y el rectángulo de destino de escala y centrar el rectángulo en esa área:

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

Si ningún mapa de bits se ha cargado desde la biblioteca de imágenes, el `else` bloque muestra algún texto para pedir al usuario que pulse en la pantalla.

Puede mostrar mapas de bits con diversos grados de transparencia y el artículo siguiente sobre [ **SkiaSharp transparencia** ](transparency.md) describe cómo.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Seleccionar una foto de la biblioteca de imágenes](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
