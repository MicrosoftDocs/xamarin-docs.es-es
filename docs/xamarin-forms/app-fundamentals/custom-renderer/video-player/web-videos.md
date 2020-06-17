---
title: "Reproducción de un vídeo web" description: "En este artículo se explica cómo reproducir vídeos web en una aplicación de reproductor de vídeo mediante Xamarin.Forms."
ms.prod: xamarin ms.assetid: 75781A10-865D-4BA8-8D6B-E3DA012922BC ms.technology: xamarin-forms author: davidbritch ms.author: dabritch ms.date: 02/12/2018 no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="playing-a-web-video"></a>Reproducción de un vídeo web

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

La clase `VideoPlayer` define una propiedad `Source` que se usa para especificar el origen del archivo de vídeo, así como una propiedad `AutoPlay`. El valor predeterminado de `AutoPlay` es `true`, lo que significa que el vídeo se debería comenzar a reproducir de forma automática después de establecer `Source`:

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Source property
        public static readonly BindableProperty SourceProperty =
            BindableProperty.Create(nameof(Source), typeof(VideoSource), typeof(VideoPlayer), null);

        [TypeConverter(typeof(VideoSourceConverter))]
        public VideoSource Source
        {
            set { SetValue(SourceProperty, value); }
            get { return (VideoSource)GetValue(SourceProperty); }
        }

        // AutoPlay property
        public static readonly BindableProperty AutoPlayProperty =
            BindableProperty.Create(nameof(AutoPlay), typeof(bool), typeof(VideoPlayer), true);

        public bool AutoPlay
        {
            set { SetValue(AutoPlayProperty, value); }
            get { return (bool)GetValue(AutoPlayProperty); }
        }
        ···
    }
}
```

La propiedad `Source` es de tipo `VideoSource`, que se modela a partir de la clase abstracta [`ImageSource`](xref:Xamarin.Forms.ImageSource) de Xamarin.Forms y sus tres derivadas: [`UriImageSource`](xref:Xamarin.Forms.UriImageSource), [`FileImageSource`](xref:Xamarin.Forms.FileImageSource) y [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource). Pero no hay ninguna opción de hacer streaming disponible para `VideoPlayer`, porque iOS y Android no admiten la reproducción de un vídeo desde una secuencia.

## <a name="video-sources"></a>Orígenes de vídeo

La clase abstracta `VideoSource` consta únicamente de tres métodos estáticos que crean las instancias de las tres clases que se derivan de `VideoSource`:

```csharp
namespace FormsVideoLibrary
{
    [TypeConverter(typeof(VideoSourceConverter))]
    public abstract class VideoSource : Element
    {
        public static VideoSource FromUri(string uri)
        {
            return new UriVideoSource() { Uri = uri };
        }

        public static VideoSource FromFile(string file)
        {
            return new FileVideoSource() { File = file };
        }

        public static VideoSource FromResource(string path)
        {
            return new ResourceVideoSource() { Path = path };
        }
    }
}
```

La clase `UriVideoSource` se usa para especificar un archivo de vídeo descargable con un URI. Define una única propiedad de tipo `string`:

```csharp
namespace FormsVideoLibrary
{
    public class UriVideoSource : VideoSource
    {
        public static readonly BindableProperty UriProperty =
            BindableProperty.Create(nameof(Uri), typeof(string), typeof(UriVideoSource));

        public string Uri
        {
            set { SetValue(UriProperty, value); }
            get { return (string)GetValue(UriProperty); }
        }
    }
}
```

El control de los objetos de tipo `UriVideoSource` se describe a continuación.

La clase `ResourceVideoSource` se usa para acceder a los archivos de vídeo que se almacenan como recursos insertados en la aplicación de plataforma, que también se especifica con una propiedad `string`:

```csharp
namespace FormsVideoLibrary
{
    public class ResourceVideoSource : VideoSource
    {
        public static readonly BindableProperty PathProperty =
            BindableProperty.Create(nameof(Path), typeof(string), typeof(ResourceVideoSource));

        public string Path
        {
            set { SetValue(PathProperty, value); }
            get { return (string)GetValue(PathProperty); }
        }
    }
}
```

El control de los objetos de tipo `ResourceVideoSource` se describe en el artículo [Carga de vídeos de recursos de aplicación](loading-resources.md). La clase `VideoPlayer` no tiene ninguna función para cargar un archivo de vídeo almacenado como un recurso en la biblioteca de .NET Standard.

La clase `FileVideoSource` se usa para acceder a los archivos de vídeo desde la biblioteca de vídeos del dispositivo. La única propiedad también es de tipo `string`:

```csharp
namespace FormsVideoLibrary
{
    public class FileVideoSource : VideoSource
    {
        public static readonly BindableProperty FileProperty =
                  BindableProperty.Create(nameof(File), typeof(string), typeof(FileVideoSource));

        public string File
        {
            set { SetValue(FileProperty, value); }
            get { return (string)GetValue(FileProperty); }
        }
    }
}
```

El control de los objetos de tipo `FileVideoSource` se describe en el artículo [Acceso a la biblioteca de vídeos del dispositivo](accessing-library.md).

La clase `VideoSource` incluye un atributo `TypeConverter` que hace referencia a `VideoSourceConverter`:

```csharp
namespace FormsVideoLibrary
{
    [TypeConverter(typeof(VideoSourceConverter))]
    public abstract class VideoSource : Element
    {
        ···
    }
}
```

Este convertidor de tipos se invoca cuando la propiedad `Source` se establece en una cadena en XAML. Esta es la clase `VideoSourceConverter`:

```csharp
namespace FormsVideoLibrary
{
    public class VideoSourceConverter : TypeConverter
    {
        public override object ConvertFromInvariantString(string value)
        {
            if (!String.IsNullOrWhiteSpace(value))
            {
                Uri uri;
                return Uri.TryCreate(value, UriKind.Absolute, out uri) && uri.Scheme != "file" ?
                                VideoSource.FromUri(value) : VideoSource.FromResource(value);
            }

            throw new InvalidOperationException("Cannot convert null or whitespace to ImageSource");
        }
    }
}
```

El método `ConvertFromInvariantString` intenta convertir la cadena en un objeto `Uri`. Si lo consigue, y el esquema no es `file:`, el método devuelve un elemento `UriVideoSource`. De lo contrario, devuelve un elemento `ResourceVideoSource`.

## <a name="setting-the-video-source"></a>Establecimiento del origen de vídeo

El resto de la lógica que implica orígenes de vídeo se implementa en los representadores de cada plataforma. En las secciones siguientes se muestra cómo los representadores de plataforma reproducen vídeos cuando la propiedad `Source` se establece en un objeto `UriVideoSource`.

### <a name="the-ios-video-source"></a>Origen de vídeo de iOS

Dos secciones de `VideoPlayerRenderer` están implicadas en la configuración del origen de vídeo del reproductor de vídeo. Cuando Xamarin.Forms crea por primera vez un objeto de tipo `VideoPlayer`, se llama al método `OnElementChanged` con la propiedad `NewElement` del objeto de argumentos establecida en ese objeto `VideoPlayer`. El método `OnElementChanged` llama a `SetSource`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetSource();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            ···
        }
        ···
    }
}
```

Más adelante, cuando se cambia la propiedad `Source`, se llama al método `OnElementPropertyChanged` con una propiedad `PropertyName` de "Source" (Origen), y se vuelve a llamar a `SetSource`.

Para reproducir un archivo de vídeo en iOS, primero se crea un objeto de tipo [`AVAsset`](xref:AVFoundation.AVAsset) para encapsular el archivo de vídeo, que se usa para crear un elemento [`AVPlayerItem`](xref:AVFoundation.AVPlayerItem), que después se pasa al objeto `AVPlayer`. Esta es la forma en la que el método `SetSource` controla la propiedad `Source` cuando es de tipo `UriVideoSource`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        AVPlayerItem playerItem;
        ···
        void SetSource()
        {
            AVAsset asset = null;

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    asset = AVAsset.FromUrl(new NSUrl(uri));
                }
            }
            ···
            if (asset != null)
            {
                playerItem = new AVPlayerItem(asset);
            }
            else
            {
                playerItem = null;
            }

            player.ReplaceCurrentItemWithPlayerItem(playerItem);

            if (playerItem != null && Element.AutoPlay)
            {
                player.Play();
            }
        }
        ···
    }
}
```

La propiedad `AutoPlay` no cuenta con ninguna análoga en las clases de vídeo de iOS, por lo que se examina al final del método `SetSource` para llamar al método `Play` en el objeto `AVPlayer`.

En algunos casos, los vídeos se siguen reproduciendo después de que la página con el elemento `VideoPlayer` haya vuelto a la página principal. Para detener el vídeo, `ReplaceCurrentItemWithPlayerItem` también se establece en la invalidación de `Dispose`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void Dispose(bool disposing)
        {
            base.Dispose(disposing);

            if (player != null)
            {
                player.ReplaceCurrentItemWithPlayerItem(null);
            }
        }
        ···
    }
}
```

### <a name="the-android-video-source"></a>Origen de vídeo de Android

El objeto `VideoPlayerRenderer` de Android debe establecer el origen del reproductor de vídeo al crear `VideoPlayer` por primera vez y posteriormente cuando cambia la propiedad `Source`:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetSource();
                ···
            }
        }
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            ···
        }
        ···
    }
}
```

El método `SetSource` controla los objetos de tipo `UriVideoSource` mediante una llamada a `SetVideoUri` en `VideoView` con un objeto `Uri` de Android creado a partir de la cadena de URI. Aquí la clase `Uri` es completa para distinguirla de la clase `Uri` de .NET:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void SetSource()
        {
            isPrepared = false;
            bool hasSetSource = false;

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    videoView.SetVideoURI(Android.Net.Uri.Parse(uri));
                    hasSetSource = true;
                }
            }
            ···

            if (hasSetSource && Element.AutoPlay)
            {
                videoView.Start();
            }
        }
        ···
    }
}
```

El objeto `VideoView` de Android no tiene una propiedad `AutoPlay` correspondiente, por lo que se llama al método `Start` si se ha establecido un vídeo nuevo.

Hay una diferencia entre el comportamiento de los representadores de iOS y Android si la propiedad `Source` de `VideoPlayer` se establece en `null`, o bien si la propiedad `Uri` de `UriVideoSource` se establece en `null` o en una cadena vacía. Si en el reproductor de vídeo de iOS se está reproduciendo un vídeo, y `Source` está establecido en `null` (o la cadena es `null` o está en blanco), se llama a `ReplaceCurrentItemWithPlayerItem` con el valor `null`. Se reemplaza el vídeo actual y se detiene la reproducción.

Android no admite una función similar. Si la propiedad `Source` se establece en `null`, el método `SetSource` simplemente la ignora y se sigue reproduciendo el vídeo actual.

### <a name="the-uwp-video-source"></a>Origen de vídeo de UWP

El objeto `MediaElement` de UWP define una propiedad `AutoPlay`, que se controla en el representador como cualquier otra propiedad:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetSource();
                SetAutoPlay();
                ···    
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            else if (args.PropertyName == VideoPlayer.AutoPlayProperty.PropertyName)
            {
                SetAutoPlay();
            }
            ···
        }
        ···
    }
}
```

La propiedad `SetSource` controla un objeto `UriVideoSource` mediante el establecimiento de la propiedad `Source` de `MediaElement` en un valor `Uri` de .NET, o bien en `null` si la propiedad `Source` de `VideoPlayer` se establece en `null`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        async void SetSource()
        {
            bool hasSetSource = false;

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    Control.Source = new Uri(uri);
                    hasSetSource = true;
                }
            }
            ···
            if (!hasSetSource)
            {
                Control.Source = null;
            }
        }

        void SetAutoPlay()
        {
            Control.AutoPlay = Element.AutoPlay;
        }
        ···
    }
}
```

## <a name="setting-a-url-source"></a>Establecimiento de un origen de dirección URL

Con la implementación de estas propiedades en los tres representadores, es posible reproducir un vídeo desde un origen de dirección URL. La página **Play Web Video** (Reproducir vídeo web) del programa [**VideoPlayDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos) se define con el siguiente archivo XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayWebVideoPage"
             Title="Play Web Video">

    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />

</ContentPage>
```

La clase `VideoSourceConverter` convierte la cadena en un elemento `UriVideoSource`. Cuando se desplaza a la página **Play Web Video**, se empieza a cargar el vídeo y se inicia la reproducción cuando se ha descargado y almacenado en búfer una cantidad de datos suficiente. El vídeo tiene aproximadamente 10 minutos de duración:

[![Reproducir vídeo web](web-videos-images/playwebvideo-small.png "Reproducir vídeo web")](web-videos-images/playwebvideo-large.png#lightbox "Reproducir vídeo web")

En cada una de las plataformas, los controles de transporte se atenúan si no se usan, pero puede pulsar en el vídeo para restaurarlos y verlos.

Para impedir que el vídeo se inicie de forma automática, establezca la propiedad `AutoPlay` en `false`:

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AutoPlay="false" />
```

Tendrá que presionar el botón **Reproducir** para iniciar el vídeo.

De forma similar, puede suprimir la presentación de los controles de transporte si establece la propiedad `AreTransportControlsEnabled` en `false`:

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AreTransportControlsEnabled="False" />
```

Si establece las dos propiedades en `false`, el vídeo no empezará a reproducirse y no habrá ninguna manera de iniciarlo. Tendría que llamar a `Play` desde el archivo de código subyacente, o bien crear controles de transporte propios como se describe en el artículo [Implementación de controles de transporte de vídeo personalizados](custom-transport.md).

En el archivo **App.xaml** se incluyen recursos para dos vídeos adicionales:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.App">
    <Application.Resources>
        <ResourceDictionary>

            <video:UriVideoSource x:Key="ElephantsDream"
                                  Uri="https://archive.org/download/ElephantsDream/ed_hd_512kb.mp4" />

            <video:UriVideoSource x:Key="BigBuckBunny"
                                  Uri="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />

            <video:UriVideoSource x:Key="Sintel"
                                  Uri="https://archive.org/download/Sintel/sintel-2048-stereo_512kb.mp4" />

        </ResourceDictionary>
    </Application.Resources>
</Application>
```

Para hacer referencia a una de estas películas, puede reemplazar la dirección URL explícita en el archivo **PlayWebVideo.xaml** con una extensión de marcado `StaticResource`, en cuyo caso no se necesitará `VideoSourceConverter` para crear el objeto `UriVideoSource`:

```xaml
<video:VideoPlayer Source="{StaticResource ElephantsDream}" />
```

Como alternativa, puede establecer la propiedad `Source` de un archivo de vídeo en un elemento `ListView`, como se describe en el artículo siguiente, [Enlace de orígenes de vídeo al reproductor](source-bindings.md).

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de reproductor de vídeo (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
