---
title: Creación de reproductores de vídeo de plataforma
description: En este artículo se explica cómo implementar un representador personalizado de reproductor de vídeo en cada plataforma mediante Xamarin.Forms.
ms.prod: xamarin
ms.assetid: EEE2FB9B-EB73-4A3F-A859-7A1D4808E149
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 007c027772701e424aad5995c0ec025c3589171c
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "76725096"
---
# <a name="creating-the-platform-video-players"></a>Creación de reproductores de vídeo de plataforma

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

La solución [**VideoPlayerDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos) contiene todo el código necesario para implementar un reproductor de vídeo para Xamarin.Forms. También incluye una serie de páginas en las que se muestra cómo usar el reproductor de vídeo dentro de una aplicación. Todo el código `VideoPlayer` y sus representadores de plataforma residen en carpetas de proyecto denominadas `FormsVideoLibrary` que además usan el espacio de nombres `FormsVideoLibrary`. Esto debería facilitar la copia de los archivos en la propia aplicación y la referencia a las clases.

## <a name="the-video-player"></a>Reproductor de vídeo

La clase [`VideoPlayer`](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos/FormsVideoLibrary/VideoPlayer.cs) forma parte de la biblioteca de .NET Standard **VideoPlayerDemos** compartida entre las plataformas. Deriva de `View`:

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
    }
}
```

Los miembros de esta clase (y la interfaz `IVideoPlayerController`) se describen en los artículos siguientes.

Cada una de las plataformas contiene una clase denominada `VideoPlayerRenderer` que contiene el código específico de la plataforma en la que se va a implementar un reproductor de vídeo. La tarea principal de este representador es crear un reproductor de vídeo para esa plataforma.

### <a name="the-ios-player-view-controller"></a>Controlador de vistas del reproductor de iOS

Hay varias clases involucradas en la implementación de un reproductor de vídeo en iOS. La aplicación primero crea un elemento [`AVPlayerViewController`](xref:AVKit.AVPlayerViewController) y luego establece la propiedad [`Player`](xref:AVKit.AVPlayerViewController.Player*) en un objeto de tipo [`AVPlayer`](xref:AVFoundation.AVPlayer). Se necesitan otras clases cuando se asigna un origen de vídeo al reproductor.

Como todos los representadores, el elemento [`VideoPlayerRenderer`](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos.iOS/FormsVideoLibrary/VideoPlayerRenderer.csVideoPlayerRenderer.cs) de iOS contiene un atributo `ExportRenderer` que identifica la vista `VideoPlayer` con el representador:

```csharp
using System;
using System.ComponentModel;
using System.IO;

using AVFoundation;
using AVKit;
using CoreMedia;
using Foundation;
using UIKit;

using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.iOS.VideoPlayerRenderer))]

namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
    }
}
```

Por lo general, un representador que establece un control de plataforma deriva de la clase [`ViewRenderer<View, NativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs), donde `View` es el derivado `View` de Xamarin.Forms (en este caso, `VideoPlayer`) y `NativeView` es un derivado `UIView` de iOS para la clase de representador. Para este representador, ese argumento genérico simplemente se establece en `UIView`, por motivos que va a entender en breve.

Cuando un representador se basa en un derivado `UIViewController` (como este), la clase debe reemplazar la propiedad `ViewController` y devolver el controlador de vistas, en este caso `AVPlayerViewController`. Ese es el propósito del campo `_playerViewController`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        AVPlayerItem playerItem;
        AVPlayerViewController _playerViewController;       // solely for ViewController property

        public override UIViewController ViewController => _playerViewController;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    // Create AVPlayerViewController
                    _playerViewController = new AVPlayerViewController();

                    // Set Player property to AVPlayer
                    player = new AVPlayer();
                    _playerViewController.Player = player;

                    // Use the View from the controller as the native control
                    SetNativeControl(_playerViewController.View);
                }
                ···
            }
        }
        ···
    }
}
```

La responsabilidad principal de la invalidación `OnElementChanged` es comprobar si la propiedad `Control` es `null` y, si es así, crear un control de plataforma y pasarlo al método `SetNativeControl`. En este caso, ese objeto solo está disponible desde la propiedad `View` de `AVPlayerViewController`. Ese derivado `UIView` es una clase privada denominada `AVPlayerView`, pero dado que es privada, no puede especificarse explícitamente como segundo argumento genérico de `ViewRenderer`.

Por lo general, la propiedad `Control` de la clase de representador hace referencia al elemento `UIView` usado para implementar el representador, pero en este caso, la propiedad `Control` no se usa en ningún otro lugar.

### <a name="the-android-video-view"></a>Vista de vídeo de Android

El representador de Android para `VideoPlayer` se basa en la clase [`VideoView`](xref:Android.Widget.VideoView) de Android. Pero, si se usa `VideoView` por sí solo para reproducir un vídeo en una aplicación de Xamarin.Forms, el vídeo llena el área asignada para `VideoPlayer` sin mantener la relación de aspecto correcta. Por este motivo (como verá en breve), `VideoView` se convierte en elemento secundario de un elemento `RelativeLayout` de Android. Una directiva `using` define `ARelativeLayout` para distinguirlo del elemento `RelativeLayout` de Xamarin.Forms, y ese es el segundo argumento genérico de `ViewRenderer`:

```csharp
using System;
using System.ComponentModel;
using System.IO;

using Android.Content;
using Android.Media;
using Android.Widget;
using ARelativeLayout = Android.Widget.RelativeLayout;

using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.Droid.VideoPlayerRenderer))]

namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        public VideoPlayerRenderer(Context context) : base(context)
        {
        }
        ···
    }
}
```

A partir de Xamarin.Forms 2.5, los representadores de Android deben incluir un constructor con un argumento `Context`.

La invalidación `OnElementChanged` crea `VideoView` y `RelativeLayout` y establece los parámetros de diseño de `VideoView` para centrar dentro de `RelativeLayout`.

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        MediaController mediaController;    // Used to display transport controls
        bool isPrepared;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    // Save the VideoView for future reference
                    videoView = new VideoView(Context);

                    // Put the VideoView in a RelativeLayout
                    ARelativeLayout relativeLayout = new ARelativeLayout(Context);
                    relativeLayout.AddView(videoView);

                    // Center the VideoView in the RelativeLayout
                    ARelativeLayout.LayoutParams layoutParams =
                        new ARelativeLayout.LayoutParams(LayoutParams.MatchParent, LayoutParams.MatchParent);
                    layoutParams.AddRule(LayoutRules.CenterInParent);
                    videoView.LayoutParameters = layoutParams;

                    // Handle a VideoView event
                    videoView.Prepared += OnVideoViewPrepared;

                    // Use the RelativeLayout as the native control
                    SetNativeControl(relativeLayout);
                }
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null && videoView != null)
            {
                videoView.Prepared -= OnVideoViewPrepared;
            }
            base.Dispose(disposing);
        }

        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            isPrepared = true;
            ···
        }
        ···
    }
}
```

Un controlador del evento `Prepared` se asocia en este método y se desasocia en el método `Dispose`. Este evento se desencadena cuando `VideoView` tiene suficiente información para empezar a reproducir un archivo de vídeo.

### <a name="the-uwp-media-element"></a>Elemento multimedia de UWP

En Plataforma universal de Windows (UWP), el reproductor de vídeo más común es [`MediaElement`](xref:Windows.UI.Xaml.Controls.MediaElement). La documentación de `MediaElement` indica que se debe usar [`MediaPlayerElement`](xref:Windows.UI.Xaml.Controls.MediaPlayerElement) en su lugar cuando solo sea necesario admitir versiones de Windows 10 a partir de la compilación 1607.

La invalidación `OnElementChanged` debe crear un elemento `MediaElement`, establecer un par de controladores de eventos y pasar el objeto `MediaElement` a `SetNativeControl`:

```csharp
using System;
using System.ComponentModel;

using Windows.Storage;
using Windows.Storage.Streams;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Media;

using Xamarin.Forms;
using Xamarin.Forms.Platform.UWP;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.UWP.VideoPlayerRenderer))]

namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    MediaElement mediaElement = new MediaElement();
                    SetNativeControl(mediaElement);

                    mediaElement.MediaOpened += OnMediaElementMediaOpened;
                    mediaElement.CurrentStateChanged += OnMediaElementCurrentStateChanged;
                }
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null)
            {
                Control.MediaOpened -= OnMediaElementMediaOpened;
                Control.CurrentStateChanged -= OnMediaElementCurrentStateChanged;
            }

            base.Dispose(disposing);
        }        
        ···
    }
}
```

Los dos controladores de eventos se desasocian en el evento `Dispose` del representador.

## <a name="showing-the-transport-controls"></a>Mostrar los controles de transporte

Todos los reproductores de vídeo incluidos en las plataformas admiten un conjunto predeterminado de controles de transporte que incluyen botones para reproducir y detener y una barra para indicar la posición actual dentro del vídeo, así como para ir a otra posición.

La clase `VideoPlayer` define una propiedad denominada `AreTransportControlsEnabled` y establece el valor predeterminado en `true`:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // AreTransportControlsEnabled property
        public static readonly BindableProperty AreTransportControlsEnabledProperty =
            BindableProperty.Create(nameof(AreTransportControlsEnabled), typeof(bool), typeof(VideoPlayer), true);

        public bool AreTransportControlsEnabled
        {
            set { SetValue(AreTransportControlsEnabledProperty, value); }
            get { return (bool)GetValue(AreTransportControlsEnabledProperty); }
        }
        ···
    }
}
```

Aunque esta propiedad tiene descriptores de acceso `set` y `get`, el representador solo tiene que controlar los casos en que la propiedad esté establecida. El descriptor de acceso `get` simplemente devuelve el valor actual de la propiedad.

Propiedades como `AreTransportControlsEnabled` se controlan en los representadores de plataforma de dos maneras:

- La primera es cuando Xamarin.Forms crea un elemento `VideoPlayer`. Esto se indica en la invalidación `OnElementChanged` del representador si la propiedad `NewElement` no es `null`. En este momento, el representador puede establecer su propio reproductor de vídeo de plataforma a partir del valor inicial de la propiedad, como se ha definido en `VideoPlayer`.

- Si la propiedad de `VideoPlayer` cambia más adelante, se llama al método `OnElementPropertyChanged` del representador. Esto permite al representador actualizar el reproductor de vídeo de plataforma en función del nuevo valor de la propiedad.

En las secciones siguientes se explica cómo se controla la propiedad `AreTransportControlsEnabled` en cada plataforma.

### <a name="ios-playback-controls"></a>Controles de reproducción de iOS

La propiedad del elemento `AVPlayerViewController` de iOS que controla la presentación de controles de transporte es [`ShowsPlaybackControls`](xref:AVKit.AVPlayerViewController.ShowsPlaybackControls*). Así es como esa propiedad se establece en el elemento `VideoViewRenderer` de iOS:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        AVPlayerViewController _playerViewController;       // solely for ViewController property

        public override UIViewController ViewController => _playerViewController;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            ((AVPlayerViewController)ViewController).ShowsPlaybackControls = Element.AreTransportControlsEnabled;
        }
        ···
    }
}
```

La propiedad `Element` del representador hace referencia a la clase `VideoPlayer`.

### <a name="the-android-media-controller"></a>Controlador de elementos multimedia de Android

En Android, la presentación de los controles de transporte exige crear un objeto [`MediaController`](xref:Android.Widget.MediaController) y asociarlo al objeto `VideoView`. La mecánica se muestra en el método `SetAreTransportControlsEnabled`:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        MediaController mediaController;    // Used to display transport controls
        bool isPrepared;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            if (Element.AreTransportControlsEnabled)
            {
                mediaController = new MediaController(Context);
                mediaController.SetMediaPlayer(videoView);
                videoView.SetMediaController(mediaController);
            }
            else
            {
                videoView.SetMediaController(null);

                if (mediaController != null)
                {
                    mediaController.SetMediaPlayer(null);
                    mediaController = null;
                }
            }
        }
        ···
    }
}
```

### <a name="the-uwp-transport-controls-property"></a>Propiedad de controles de transporte de UWP

El elemento `MediaElement` de UWP define una propiedad denominada [`AreTransportControlsEnabled`](xref:Windows.UI.Xaml.Controls.MediaElement.AreTransportControlsEnabled*), de modo que esa propiedad se establece desde la propiedad `VideoPlayer` del mismo nombre:

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
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            Control.AreTransportControlsEnabled = Element.AreTransportControlsEnabled;
        }
        ···
    }
}
```

Una propiedad más es necesaria para empezar a reproducir un vídeo: se trata de la propiedad fundamental `Source` que hace referencia a un archivo de vídeo. La implementación de la propiedad `Source` se describe en el siguiente artículo, [Reproducción de un vídeo de web](web-videos.md).

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de reproductor de vídeo (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
