---
title: "Posicionamiento de vídeo personalizado" description: "En este artículo se explica cómo implementar una barra de posición personalizada en una aplicación de reproductor de vídeo mediante Xamarin.Forms."
ms.prod: xamarin ms.assetid: 6D792264-30FF-46F7-8C1B-2FEF9D277DF4 ms.technology: xamarin-forms author: davidbritch ms.author: dabritch ms.date: 02/12/2018 no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="custom-video-positioning"></a>Barra de posición de vídeo personalizada

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

Los controles de transporte que cada plataforma implementa incluyen una barra de posición. Esta barra es similar a un control deslizante o una barra de desplazamiento y muestra la ubicación actual del vídeo dentro de su duración total. Además, el usuario puede manipular la barra de posición para avanzar o retroceder a una nueva posición en el vídeo.

En este artículo se muestra cómo puede implementar su propia barra de posición personalizada.

## <a name="the-duration-property"></a>La propiedad Duration

Un elemento de información que `VideoPlayer` necesita para admitir una barra de posición personalizada es la duración del vídeo. `VideoPlayer` define una propiedad `Duration` de solo lectura de tipo `TimeSpan`:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Duration read-only property
        private static readonly BindablePropertyKey DurationPropertyKey =
            BindableProperty.CreateReadOnly(nameof(Duration), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan(),
                propertyChanged: (bindable, oldValue, newValue) => ((VideoPlayer)bindable).SetTimeToEnd());

        public static readonly BindableProperty DurationProperty = DurationPropertyKey.BindableProperty;

        public TimeSpan Duration
        {
            get { return (TimeSpan)GetValue(DurationProperty); }
        }

        TimeSpan IVideoPlayerController.Duration
        {
            set { SetValue(DurationPropertyKey, value); }
            get { return Duration; }
        }
        ···
    }
}
```

Al igual que la propiedad `Status` descrita en el [artículo anterior](custom-transport.md), esta propiedad `Duration` es de solo lectura. Se define con una clave `BindablePropertyKey` privada y solo se puede establecer mediante una referencia a la interfaz de `IVideoPlayerController`, que incluye esta propiedad `Duration`:

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

Tenga en cuenta también el controlador de cambio de propiedad que llama a un método denominado `SetTimeToEnd` que se describe más adelante en este artículo.

La duración de un vídeo *no* está disponible inmediatamente después de que se establezca la propiedad `Source` de `VideoPlayer`. El archivo de vídeo debe descargarse parcialmente antes de que el reproductor de vídeo subyacente pueda determinar su duración.

Aquí le mostramos cómo cada uno de los representadores de la plataforma obtiene la duración del vídeo:

### <a name="video-duration-in-ios"></a>Duración del vídeo en iOS

En iOS, la duración de un vídeo se obtiene a partir de la propiedad `Duration` de `AVPlayerItem`, pero no inmediatamente después de que se haya creado `AVPlayerItem`. Es posible establecer un observador de iOS para la propiedad `Duration`, pero `VideoPlayerRenderer` obtiene la duración en el método `UpdateStatus`, al que se llama 10 veces por segundo:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            if (playerItem != null)
            {
                ((IVideoPlayerController)Element).Duration = ConvertTime(playerItem.Duration);
                ···
            }
        }

        TimeSpan ConvertTime(CMTime cmTime)
        {
            return TimeSpan.FromSeconds(Double.IsNaN(cmTime.Seconds) ? 0 : cmTime.Seconds);

        }
        ···
    }
}
```

El método `ConvertTime` convierte un objeto `CMTime` en un valor `TimeSpan`.

### <a name="video-duration-in-android"></a>Duración del vídeo en Android

La propiedad `Duration` de la `VideoView` de Android notifica una duración válida en milisegundos cuando se activa el evento `Prepared` de `VideoView`. La clase `VideoPlayerRenderer` de Android utiliza ese controlador para obtener la propiedad `Duration`:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            ···
            ((IVideoPlayerController)Element).Duration = TimeSpan.FromMilliseconds(videoView.Duration);
        }
        ···
    }
}
```

### <a name="video-duration-in-uwp"></a>Duración del vídeo en UWP

La propiedad `NaturalDuration` de `MediaElement` es un valor `TimeSpan` y entra en vigor cuando `MediaElement` activa el evento `MediaOpened`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        void OnMediaElementMediaOpened(object sender, RoutedEventArgs args)
        {
            ((IVideoPlayerController)Element).Duration = Control.NaturalDuration.TimeSpan;
        }
        ···
    }
}
```

## <a name="the-position-property"></a>La propiedad Position

`VideoPlayer` también necesita una propiedad `Position` que aumente de cero a `Duration` mientras se reproduce el vídeo. `VideoPlayer` implementa esta propiedad como la propiedad `Position` del `MediaElement` de UWP, que es una propiedad enlazable normal con descriptores de acceso `set` y `get` públicos:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Position property
        public static readonly BindableProperty PositionProperty =
            BindableProperty.Create(nameof(Position), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan(),
                propertyChanged: (bindable, oldValue, newValue) => ((VideoPlayer)bindable).SetTimeToEnd());

        public TimeSpan Position
        {
            set { SetValue(PositionProperty, value); }
            get { return (TimeSpan)GetValue(PositionProperty); }
        }
        ···
    }
}
```

El descriptor de acceso `get` devuelve la posición actual del vídeo mientras se reproduce, pero el descriptor de acceso `set` está pensado para responder a la manipulación del usuario de la barra de posición para mover la posición de vídeo hacia adelante o hacia atrás.

En iOS y Android, la propiedad que obtiene la posición actual solo tiene un descriptor de acceso `get`, y hay disponible un método `Seek` para realizar esta segunda tarea. Si se piensa bien, un método `Seek` por separado parece ser un enfoque más sensato que una sola propiedad `Position`. Una sola propiedad `Position` tiene un problema inherente: mientras se reproduce el vídeo, la propiedad `Position` debe actualizarse continuamente para reflejar la nueva posición. Pero no es recomendable que la mayoría de los cambios en la propiedad `Position` hagan que el reproductor de vídeo se mueva a una nueva posición en el vídeo. Si eso ocurriera, el reproductor de vídeo, como respuesta, buscaría el último valor de la propiedad `Position` y el vídeo no avanzaría.

A pesar de las dificultades de la implementación de una propiedad `Position` con los descriptores de acceso `set` y `get`, se ha elegido este enfoque porque es coherente con UWP `MediaElement`, y tiene una gran ventaja con el enlace de datos: La propiedad `Position` de `VideoPlayer` pueden estar enlazada al control deslizante que se usa para mostrar la posición y para buscar una nueva posición. Sin embargo, se deben tomar varias precauciones al implementar esta propiedad `Position` para evitar bucles de retroalimentación.

### <a name="setting-and-getting-ios-position"></a>Establecer y obtener la posición en iOS

En iOS, la propiedad `CurrentTime` del objeto `AVPlayerItem` indica la posición actual del vídeo en reproducción. `VideoPlayerRenderer` de iOS establece la propiedad `Position` en el controlador `UpdateStatus` al mismo tiempo que establece la propiedad `Duration`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            if (playerItem != null)
            {
                ···
                ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, ConvertTime(playerItem.CurrentTime));
            }
        }
        ···
    }
}
```

El representador detecta cuándo el conjunto de propiedades `Position` de `VideoPlayer` ha cambiado en la invalidación de `OnElementPropertyChanged` y utiliza ese valor nuevo para llamar a un método `Seek` en el objeto `AVPlayer`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                TimeSpan controlPosition = ConvertTime(player.CurrentTime);

                if (Math.Abs((controlPosition - Element.Position).TotalSeconds) > 1)
                {
                    player.Seek(CMTime.FromSeconds(Element.Position.TotalSeconds, 1));
                }
            }
        }
        ···
    }
}
```

Tenga en cuenta que cada vez que la propiedad `Position` en `VideoPlayer` se establece desde el controlador `OnUpdateStatus`, la propiedad `Position` activa un evento `PropertyChanged`, que se detecta en la invalidación de `OnElementPropertyChanged`. En la mayoría de estos cambios, el método `OnElementPropertyChanged` no debe hacer nada. En caso contrario, ante cada cambio en la posición del vídeo, se movería a la misma posición a la que ha llegado.

Para evitar este bucle de comentarios, el método `OnElementPropertyChanged` solo llama a `Seek` cuando la diferencia entre la propiedad `Position` y la posición actual del `AVPlayer` es mayor que un segundo.

### <a name="setting-and-getting-android-position"></a>Establecer y obtener la posición en Android

Al igual que en el representador de iOS, el `VideoPlayerRenderer` de Android establece un valor nuevo para la propiedad `Position` en el controlador `OnUpdateStatus`. La propiedad `CurrentPosition` de `VideoView` contiene la posición nueva en unidades de milisegundos:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            TimeSpan timeSpan = TimeSpan.FromMilliseconds(videoView.CurrentPosition);
            ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, timeSpan);
        }
        ···
    }
}
```

Además, al igual que en el representador de iOS, el representador de Android llama al método `SeekTo` de `VideoView` cuando la propiedad `Position` ha cambiado, pero solo cuando el cambio y el valor `CurrentPosition` de `VideoView` difieren en más de un segundo:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                if (Math.Abs(videoView.CurrentPosition - Element.Position.TotalMilliseconds) > 1000)
                {
                    videoView.SeekTo((int)Element.Position.TotalMilliseconds);
                }
            }
        }
        ···
    }
}
```

### <a name="setting-and-getting-uwp-position"></a>Establecer y obtener la posición en UWP

`VideoPlayerRenderer` de UWP controla el valor `Position` de la misma manera que los representadores de iOS y Android; sin embargo, dado que la propiedad `Position` del `MediaElement` de UWP también es un valor `TimeSpan`, no es necesario realizar ninguna conversión:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                if (Math.Abs((Control.Position - Element.Position).TotalSeconds) > 1)
                {
                    Control.Position = Element.Position;
                }
            }
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, Control.Position);
        }
        ···
    }
}
```

## <a name="calculating-a-timetoend-property"></a>Calcular una propiedad TimeToEnd

A veces, los reproductores de vídeo muestran el tiempo restante en el vídeo. Este valor comienza en la duración del vídeo cuando el vídeo empieza y disminuye a cero cuando el vídeo finaliza.

`VideoPlayer` incluye una propiedad `TimeToEnd` de solo lectura que se controla por completo dentro de la clase según los cambios en las propiedades `Duration` y `Position`:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        private static readonly BindablePropertyKey TimeToEndPropertyKey =
            BindableProperty.CreateReadOnly(nameof(TimeToEnd), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan());

        public static readonly BindableProperty TimeToEndProperty = TimeToEndPropertyKey.BindableProperty;

        public TimeSpan TimeToEnd
        {
            private set { SetValue(TimeToEndPropertyKey, value); }
            get { return (TimeSpan)GetValue(TimeToEndProperty); }
        }

        void SetTimeToEnd()
        {
            TimeToEnd = Duration - Position;
        }
        ···
    }
}
```

El método `SetTimeToEnd` se llama desde los controladores de cambio de propiedad de `Duration` y `Position`.

## <a name="a-custom-slider-for-video"></a>Un control deslizante personalizado para vídeo

Es posible escribir un control personalizado para una barra de posición o para usar el Xamarin.Forms de `Slider` o una clase que derive de `Slider`, como la siguiente clase `PositionSlider`. La clase define dos propiedades nuevas denominadas `Duration` y `Position` de tipo `TimeSpan` que están diseñadas para enlazarse a las dos propiedades del mismo nombre en `VideoPlayer`. Tenga en cuenta que el modo de enlace predeterminado de la propiedad `Position` es bidireccional:

```csharp
namespace FormsVideoLibrary
{
    public class PositionSlider : Slider
    {
        public static readonly BindableProperty DurationProperty =
            BindableProperty.Create(nameof(Duration), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(1),
                                    propertyChanged: (bindable, oldValue, newValue) =>
                                    {
                                        double seconds = ((TimeSpan)newValue).TotalSeconds;
                                        ((PositionSlider)bindable).Maximum = seconds <= 0 ? 1 : seconds;
                                    });

        public TimeSpan Duration
        {
            set { SetValue(DurationProperty, value); }
            get { return (TimeSpan)GetValue(DurationProperty); }
        }

        public static readonly BindableProperty PositionProperty =
            BindableProperty.Create(nameof(Position), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(0),
                                    defaultBindingMode: BindingMode.TwoWay,
                                    propertyChanged: (bindable, oldValue, newValue) =>
                                    {
                                        double seconds = ((TimeSpan)newValue).TotalSeconds;
                                        ((PositionSlider)bindable).Value = seconds;
                                    });

        public TimeSpan Position
        {
            set { SetValue(PositionProperty, value); }
            get { return (TimeSpan)GetValue(PositionProperty); }
        }

        public PositionSlider()
        {
            PropertyChanged += (sender, args) =>
            {
                if (args.PropertyName == "Value")
                {
                    TimeSpan newPosition = TimeSpan.FromSeconds(Value);

                    if (Math.Abs(newPosition.TotalSeconds - Position.TotalSeconds) / Duration.TotalSeconds > 0.01)
                    {
                        Position = newPosition;
                    }
                }
            };
        }
    }
}
```

El controlador de cambio de propiedad para la propiedad `Duration` establece la propiedad `Maximum` del `Slider` subyacente en la propiedad `TotalSeconds` del valor `TimeSpan`. De forma similar, el controlador de cambio de propiedad para `Position` establece la propiedad `Value` del `Slider`. De esta manera, el `Slider` subyacente realiza un seguimiento de la posición del `PositionSlider`.

`PositionSlider` se actualiza desde el `Slider` subyacente en una única instancia: cuando el usuario manipula el `Slider` para indicar que el vídeo debe avanzar o retroceder a una posición nueva. Esto se detecta en el controlador `PropertyChanged` en el constructor del `PositionSlider`. El controlador comprueba si hay algún cambio en la propiedad `Value` y, si es diferente de la propiedad `Position`, la propiedad `Position` se establece desde la propiedad `Value`.

En teoría, la instrucción `if` interna podría escribirse así:

```csharp
if (newPosition.Seconds != Position.Seconds)
{
    Position = newPosition;
}
```

Sin embargo, la implementación de `Slider` en Android solo tiene 1000 pasos discretos, independientemente de la configuración de `Minimum` y `Maximum`. Si la longitud de un vídeo fuera mayor que 1000 segundos, dos valores `Position` diferentes se corresponderían a la misma configuración de `Value` del `Slider`, y esta instrucción `if` desencadenaría un falso positivo de una manipulación de usuario del `Slider`. En su lugar, es más seguro comprobar que las posiciones nueva y existente son mayores que una centésima de la duración total.

## <a name="using-the-positionslider"></a>Utilizar el PositionSlider

La documentación del [`MediaElement`](/uwp/api/Windows.UI.Xaml.Controls.MediaElement/) de UWP advierte sobre el enlace a la propiedad `Position` porque la propiedad se actualiza con frecuencia. La documentación recomienda que se utilice un temporizador para consultar la propiedad `Position`.

Esa es una buena recomendación, pero las tres clases `VideoPlayerRenderer` ya utilizan indirectamente un temporizador para actualizar la propiedad `Position`. La propiedad `Position` se cambia en un controlador para el evento `UpdateStatus`, que se activa solo 10 veces por segundo.

Por tanto, la propiedad `Position` del `VideoPlayer` puede enlazarse a la propiedad `Position` del `PositionSlider` sin problemas de rendimiento, como se muestra en la página **Barra de posición personalizada**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.CustomPositionBarPage"
             Title="Custom Position Bar">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           AreTransportControlsEnabled="False"
                           Source="{StaticResource ElephantsDream}" />

        ···

        <StackLayout Grid.Row="1"
                     Orientation="Horizontal"
                     Margin="10, 0"
                     BindingContext="{x:Reference videoPlayer}">

            <Label Text="{Binding Path=Position,
                                  StringFormat='{0:hh\\:mm\\:ss}'}"
                   VerticalOptions="Center"/>

            ···

            <Label Text="{Binding Path=TimeToEnd,
                                  StringFormat='{0:hh\\:mm\\:ss}'}"
                   VerticalOptions="Center" />
        </StackLayout>

        <video:PositionSlider Grid.Row="2"
                              Margin="10, 0, 10, 10"
                              BindingContext="{x:Reference videoPlayer}"
                              Duration="{Binding Duration}"           
                              Position="{Binding Position}">
            <video:PositionSlider.Triggers>
                <DataTrigger TargetType="video:PositionSlider"
                             Binding="{Binding Status}"
                             Value="{x:Static video:VideoStatus.NotReady}">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </video:PositionSlider.Triggers>
        </video:PositionSlider>
    </Grid>
</ContentPage>
```

Los primeros puntos suspensivos (···) ocultan el `ActivityIndicator`; es el mismo que en la página anterior **Transporte personalizado**. Tenga en cuenta los dos elementos `Label` que muestran las propiedades `Position` y `TimeToEnd`. Los puntos suspensivos entre esos dos elementos `Label` ocultan los dos elementos `Button` que aparecen en la página **Transporte personalizado** para reproducir, hacer pausa y detener. La lógica de código subyacente también es la misma que la de la página **Transporte personalizado**.

[![Posicionamiento personalizado](custom-positioning-images/custompositioning-small.png "Posicionamiento personalizado")](custom-positioning-images/custompositioning-large.png#lightbox "Posicionamiento personalizado")

Con esto concluye la explicación de `VideoPlayer`.

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de reproductor de vídeo (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
