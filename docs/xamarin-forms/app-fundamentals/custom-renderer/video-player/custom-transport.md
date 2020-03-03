---
title: Controles de transporte de vídeo personalizados
description: En este artículo, se explica cómo implementar controles de transporte personalizados en una aplicación de reproductor de vídeo con Xamarin.Forms.
ms.prod: xamarin
ms.assetid: CE9E955D-A9AC-4019-A5D7-6390D80DECA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: efe41fa5f25f6257587fd97a2711e9037b94dc6e
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/26/2020
ms.locfileid: "77636021"
---
# <a name="custom-video-transport-controls"></a>Controles de transporte de vídeo personalizados

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

Los controles de transporte de un reproductor de vídeo son los botones que realizan las funciones **Reproducir**, **Pausa** y **Detener**. Estos botones suelen identificarse con iconos conocidos en lugar de texto, y las funciones **Reproducir** y **Pausa** suelen combinarse en un mismo botón.

De forma predeterminada, el elemento `VideoPlayer` muestra controles de transporte compatibles con cada plataforma. Al establecer la propiedad `AreTransportControlsEnabled` en `false`, se eliminan estos controles. Después, puede controlar el elemento `VideoPlayer` mediante programación, o bien puede eliminar sus propios controles de transporte.

## <a name="the-play-pause-and-stop-methods"></a>Métodos Reproducir, Pausa y Detener

La clase `VideoPlayer` define tres métodos (llamados `Play`, `Pause` y `Stop`) que se implementan mediante la activación de eventos:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        public event EventHandler PlayRequested;

        public void Play()
        {
            PlayRequested?.Invoke(this, EventArgs.Empty);
        }

        public event EventHandler PauseRequested;

        public void Pause()
        {
            PauseRequested?.Invoke(this, EventArgs.Empty);
        }

        public event EventHandler StopRequested;

        public void Stop()
        {
            StopRequested?.Invoke(this, EventArgs.Empty);
        }
    }
}
```

Los controladores de eventos para estos eventos se establecen mediante la clase `VideoPlayerRenderer` en cada plataforma, como se muestra a continuación:

### <a name="ios-transport-implementations"></a>Implementaciones de transporte de iOS

La versión de iOS de `VideoPlayerRenderer` usa el método `OnElementChanged` para establecer controladores para estos tres eventos cuando la propiedad `NewElement` no es `null` y elimina la asociación de los controladores de eventos cuando `OldElement` no es `null`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        // Event handlers to implement methods
        void OnPlayRequested(object sender, EventArgs args)
        {
            player.Play();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            player.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            player.Pause();
            player.Seek(new CMTime(0, 1));
        }
    }
}
```

Para implementar los controladores de eventos, se llama a métodos en el objeto `AVPlayer`. Como no hay ningún método `Stop` para `AVPlayer`, para simularlo, se pausa el vídeo y se mueve la posición al principio.

### <a name="android-transport-implementations"></a>Implementaciones de transporte de Android

La implementación de Android es similar a la implementación de iOS. Los controladores para las tres funciones se establecen cuando `NewElement` no es `null` y, cuando `OldElement` no es `null`, se elimina la asociación:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        void OnPlayRequested(object sender, EventArgs args)
        {
            videoView.Start();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            videoView.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            videoView.StopPlayback();
        }
    }
}
```

Estas tres funciones llaman a métodos definidos por `VideoView`.

### <a name="uwp-transport-implementations"></a>Implementaciones de transporte de UWP

La implementación de UWP de las tres funciones de transporte es muy similar a las implementaciones de iOS y Android:

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
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        // Event handlers to implement methods
        void OnPlayRequested(object sender, EventArgs args)
        {
            Control.Play();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            Control.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            Control.Stop();
        }
    }
}
```

## <a name="the-video-player-status"></a>Estado del reproductor de vídeo

Implementar las funciones **Reproducir**, **Pausa** y **Detener** no es suficiente para admitir los controles de transporte. Con frecuencia, los comandos **Reproducir** y **Pausa** se implementan con el mismo botón, que cambia su apariencia para indicar si el vídeo está reproduciéndose o en pausa en ese momento. Además, el botón no tiene que habilitarse si el vídeo aún no se ha cargado.

Estos requisitos implican que el reproductor de vídeo necesita mostrar un estado actual que indique si está reproduciéndose o en pausa, o bien si aún no está preparado para reproducir un vídeo. (Cada plataforma también admite propiedades que indican si el vídeo se puede pausar o se puede mover a una nueva posición, pero estas propiedades pueden aplicarse para la transmisión por streaming de vídeo, en lugar de aplicarlas en archivos de vídeo, por lo que no se admiten en el elemento `VideoPlayer` que se describe aquí).

En el proyecto **VideoPlayerDemos**, se incluye una enumeración `VideoStatus` con tres miembros:

```csharp
namespace FormsVideoLibrary
{
    public enum VideoStatus
    {
        NotReady,
        Playing,
        Paused
    }
}
```

La clase `VideoPlayer` define una propiedad enlazable de solo lectura denominada `Status` del tipo `VideoStatus`. Esta propiedad se define como de solo lectura porque únicamente tiene que establecerse desde el representador de la plataforma:

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Status read-only property
        private static readonly BindablePropertyKey StatusPropertyKey =
            BindableProperty.CreateReadOnly(nameof(Status), typeof(VideoStatus), typeof(VideoPlayer), VideoStatus.NotReady);

        public static readonly BindableProperty StatusProperty = StatusPropertyKey.BindableProperty;

        public VideoStatus Status
        {
            get { return (VideoStatus)GetValue(StatusProperty); }
        }

        VideoStatus IVideoPlayerController.Status
        {
            set { SetValue(StatusPropertyKey, value); }
            get { return Status; }
        }
        ···
    }
}
```

Normalmente, una propiedad enlazable de solo lectura tendría un descriptor de acceso `set` privado en la propiedad `Status` para permitirle establecerlo en la clase. Pero, para un elemento `View` derivado admitido por representadores, la propiedad tiene que establecerse desde fuera de la clase, pero solo por el representador de la plataforma.

Por este motivo, se define otra propiedad con el nombre `IVideoPlayerController.Status`. Esta es una implementación de interfaz explícita y es posible mediante la interfaz `IVideoPlayerController` que implementa la clase `VideoPlayer`:

```csharp
namespace FormsVideoLibrary
{
    public interface IVideoPlayerController
    {
        VideoStatus Status { set; get; }

        TimeSpan Duration { set; get; }
    }
}
```

Esto es similar a la forma en que el control [`WebView`](xref:Xamarin.Forms.WebView) usa la interfaz [`IWebViewController`](xref:Xamarin.Forms.IWebViewController) para implementar las propiedades `CanGoBack` y `CanGoForward`. (Para obtener más información, vea el código fuente de [`WebView`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/WebView.cs) y sus representadores).

Esto permite que una clase externa a `VideoPlayer` pueda establecer la propiedad `Status` al hacer referencia a la interfaz `IVideoPlayerController`. (Verá el código en breve). La propiedad también se puede establecer desde otras clases, pero es poco probable que se establezca por error. Aún más importante, la propiedad `Status` no se puede establecer mediante un enlace de datos.

Para ayudar a los representadores a mantener actualizada la propiedad `Status`, la clase `VideoPlayer` define un evento `UpdateStatus` que se desencadena cada décima de segundo:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        public event EventHandler UpdateStatus;

        public VideoPlayer()
        {
            Device.StartTimer(TimeSpan.FromMilliseconds(100), () =>
            {
                UpdateStatus?.Invoke(this, EventArgs.Empty);
                return true;
            });
        }
        ···
    }
}
```

### <a name="the-ios-status-setting"></a>Ajuste de estado de iOS

El elemento `VideoPlayerRenderer` de iOS establece un controlador para el evento `UpdateStatus` (y anula la asociación de ese controlador cuando el elemento `VideoPlayer` subyacente está ausente) y usa el controlador para establecer la propiedad `Status`:

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
                args.NewElement.UpdateStatus += OnUpdateStatus;
                ···
            }

            if (args.OldElement != null)
            {
                args.OldElement.UpdateStatus -= OnUpdateStatus;
                ···
            }
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            VideoStatus videoStatus = VideoStatus.NotReady;

            switch (player.Status)
            {
                case AVPlayerStatus.ReadyToPlay:
                    switch (player.TimeControlStatus)
                    {
                        case AVPlayerTimeControlStatus.Playing:
                            videoStatus = VideoStatus.Playing;
                            break;

                        case AVPlayerTimeControlStatus.Paused:
                            videoStatus = VideoStatus.Paused;
                            break;
                    }
                    break;
                }
            }

            ((IVideoPlayerController)Element).Status = videoStatus;
            ···
        }
        ···
    }
}
```

Se debe acceder a dos propiedades de `AVPlayer`: la propiedad [`Status`](xref:AVFoundation.AVPlayer.Status*) de tipo `AVPlayerStatus` y la propiedad [`TimeControlStatus`](xref:AVFoundation.AVPlayer.TimeControlStatus*) de tipo `AVPlayerTimeControlStatus`. Tenga en cuenta que la propiedad `Element` (que es el objeto `VideoPlayer`) tiene que transmitir a `IVideoPlayerController` para establecer la propiedad `Status`.

### <a name="the-android-status-setting"></a>Ajuste de estado de Android

La propiedad [`IsPlaying`](xref:Android.Widget.VideoView.IsPlaying) del elemento `VideoView` de Android es un operador booleano que solo indica si el vídeo está reproduciéndose o en pausa. Para determinar si el elemento `VideoView` aún no puede reproducir ni pausar el vídeo, es necesario controlar el evento `Prepared` de `VideoView`. Estos dos controladores se establecen en el método `OnElementChanged` y se anula la asociación durante el reemplazo `Dispose`:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        bool isPrepared;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    ···
                    videoView.Prepared += OnVideoViewPrepared;
                    ···
                }
                ···
                args.NewElement.UpdateStatus += OnUpdateStatus;
                ···
            }

            if (args.OldElement != null)
            {
                args.OldElement.UpdateStatus -= OnUpdateStatus;
                ···
            }

        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null && videoView != null)
            {
                videoView.Prepared -= OnVideoViewPrepared;
            }
            if (Element != null)
            {
                Element.UpdateStatus -= OnUpdateStatus;
            }

            base.Dispose(disposing);
        }
        ···
    }
}
```

El controlador `UpdateStatus` usa el campo `isPrepared` (establecido en el controlador `Prepared`) y la propiedad `IsPlaying` para establecer la propiedad `Status`:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        bool isPrepared;
        ···
        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            isPrepared = true;
            ···
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            VideoStatus status = VideoStatus.NotReady;

            if (isPrepared)
            {
                status = videoView.IsPlaying ? VideoStatus.Playing : VideoStatus.Paused;
            }
            ···
        }
        ···
    }
}
```

### <a name="the-uwp-status-setting"></a>Ajuste de estado de UWP

El elemento `VideoPlayerRenderer` de UWP usa el evento `UpdateStatus`, pero no lo necesita para establecer la propiedad `Status`. El elemento `MediaElement` define un evento [`CurrentStateChanged`](xref:Windows.UI.Xaml.Controls.MediaElement.CurrentStateChanged) que permite notificar al representador si se cambia la propiedad [`CurrentState`](xref:Windows.UI.Xaml.Controls.MediaElement.CurrentState*). En el reemplazo `Dispose`, se anula la asociación de la propiedad:

```csharp
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
                    ···
                    mediaElement.CurrentStateChanged += OnMediaElementCurrentStateChanged;
                };
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null)
            {
                ···
                Control.CurrentStateChanged -= OnMediaElementCurrentStateChanged;
            }

            base.Dispose(disposing);
        }
        ···
    }
}
```

La propiedad `CurrentState` es el tipo [`MediaElementState`](/uwp/api/windows.ui.xaml.media.mediaelementstate) y se asigna fácilmente a `VideoStatus`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        void OnMediaElementCurrentStateChanged(object sender, RoutedEventArgs args)
        {
            VideoStatus videoStatus = VideoStatus.NotReady;

            switch (Control.CurrentState)
            {
                case MediaElementState.Playing:
                    videoStatus = VideoStatus.Playing;
                    break;

                case MediaElementState.Paused:
                case MediaElementState.Stopped:
                    videoStatus = VideoStatus.Paused;
                    break;
            }

            ((IVideoPlayerController)Element).Status = videoStatus;
        }
        ···
    }
}
```

## <a name="play-pause-and-stop-buttons"></a>Botones Reproducir, Pausa y Detener

Usar caracteres Unicode para imágenes simbólicas de **Reproducir**, **Pausar** y **Detener** es problemático. En la sección [Aspectos técnicos varios](https://unicode-table.com/en/blocks/miscellaneous-technical/) del estándar Unicode, se definen tres caracteres de símbolos que parecen apropiados para esta finalidad. Estos son:

- 0x23F5 (triángulo que apunta hacia la derecha de tamaño medio y color negro) o &#x23F5; para **Reproducir**
- 0x23F8 (barra doble vertical) o &#x23F8; para **Pausa**
- 0x23F9 (cuadrado negro) o &#x23F9; para **Detener**

Independientemente de cómo se muestren estos símbolos en el explorador (ya que en cada explorador se controlan de formas distintas), no se muestran de forma coherente en las plataformas admitidas por Xamarin.Forms. En dispositivos con iOS y UWP, los caracteres **Pausa** y **Detener** tienen una apariencia gráfica, pero con un fondo 3D azul y un primer plano blanco. Esto no ocurre en Android, donde el símbolo es simplemente azul. Pero el punto de código 0x23F5 de **Reproducir** no tiene la misma apariencia en UWP y, además, ni siquiera se admite en iOS y Android.

Por ese motivo, el punto de código 0x23F5 no se puede usar para **Reproducir**. Un sustituto adecuado es:

- 0x25B6 (triángulo que apunta hacia la derecha negro) o &#x25B6; para **Reproducir**

Esto se admite en todas las plataformas, pero se trata de un triángulo negro sin formato que no tiene la apariencia 3D de **Pausa** y **Detener**. Una posibilidad es seguir el punto de código 0x25B6 con un código de variante:

- 0x25B6 seguido de 0xFE0F (variante 16) o &#x25B6;&#xFE0F; para **Reproducir**

Esto es lo que se usa en el marcado que se muestra a continuación. En iOS, proporciona al símbolo de **Reproducir** la misma apariencia 3D que los botones **Pausa** y **Detener**, pero la variante no funciona en Android ni en UWP.

La página **Transporte personalizado** establece la propiedad **AreTransportControlsEnabled** en **false** e incluye un elemento `ActivityIndicator` mostrado cuando el vídeo está cargándose, además de dos botones. Los objetos `DataTrigger` se usan para habilitar y deshabilitar el elemento `ActivityIndicator` y los botones, así como para cambiar el primer botón entre **Reproducir** y **Pausa**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.CustomTransportPage"
             Title="Custom Transport">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           AutoPlay="False"
                           AreTransportControlsEnabled="False"
                           Source="{StaticResource BigBuckBunny}" />

        <ActivityIndicator Grid.Row="0"
                           Color="Gray"
                           IsVisible="False">
            <ActivityIndicator.Triggers>
                <DataTrigger TargetType="ActivityIndicator"
                             Binding="{Binding Source={x:Reference videoPlayer},
                                               Path=Status}"
                             Value="{x:Static video:VideoStatus.NotReady}">
                    <Setter Property="IsVisible" Value="True" />
                    <Setter Property="IsRunning" Value="True" />
                </DataTrigger>
            </ActivityIndicator.Triggers>
        </ActivityIndicator>

        <StackLayout Grid.Row="1"
                     Orientation="Horizontal"
                     Margin="0, 10"
                     BindingContext="{x:Reference videoPlayer}">

            <Button Text="&#x25B6;&#xFE0F; Play"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnPlayPauseButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.Playing}">
                        <Setter Property="Text" Value="&#x23F8; Pause" />
                    </DataTrigger>

                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.NotReady}">
                        <Setter Property="IsEnabled" Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>

            <Button Text="&#x23F9; Stop"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnStopButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.NotReady}">
                        <Setter Property="IsEnabled" Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>
        </StackLayout>
    </Grid>
</ContentPage>
```

Los desencadenadores de datos se describen con detalle en el artículo [Desencadenadores de datos](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers).

El archivo de código subyacente contiene los controladores para los eventos `Clicked` del botón:

```csharp
namespace VideoPlayerDemos
{
    public partial class CustomTransportPage : ContentPage
    {
        public CustomTransportPage()
        {
            InitializeComponent();
        }

        void OnPlayPauseButtonClicked(object sender, EventArgs args)
        {
            if (videoPlayer.Status == VideoStatus.Playing)
            {
                videoPlayer.Pause();
            }
            else if (videoPlayer.Status == VideoStatus.Paused)
            {
                videoPlayer.Play();
            }
        }

        void OnStopButtonClicked(object sender, EventArgs args)
        {
            videoPlayer.Stop();
        }
    }
}
```

Como `AutoPlay` se establece en `false` en el archivo **CustomTransport.xaml**, necesita pulsar el botón **Reproducir** cuando este se habilite para iniciar el vídeo. Los botones se definen de forma que los caracteres Unicode indicados anteriormente se muestren con sus equivalentes de texto. Los botones tienen una apariencia coherente en todas las plataformas cuando el vídeo está reproduciéndose:

[![Reproducción del transporte personalizado](custom-transport-images/customtransportplaying-small.png "Reproducción del transporte personalizado")](custom-transport-images/customtransportplaying-large.png#lightbox "Reproducción del transporte personalizado")

Pero, en Android y UWP, el botón **Reproducir** tiene una apariencia muy distinta cuando el vídeo está pausado:

[![Transporte personalizado detenido](custom-transport-images/customtransportpaused-small.png "Transporte personalizado detenido")](custom-transport-images/customtransportpaused-large.png#lightbox "Transporte personalizado detenido")

En una aplicación de producción, es probable que quiera usar sus propias imágenes de mapa de bits para los botones con el fin de conseguir una uniformidad visual.

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de reproductor de vídeo (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
