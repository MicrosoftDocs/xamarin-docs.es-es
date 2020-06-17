---
title: "Enlazar orígenes de vídeo con el reproductor" description: "En este artículo, se explica cómo enlazar orígenes de vídeo al reproductor de vídeo con Xamarin.Forms."
ms.prod: xamarin ms.assetid: 504E0C7E-051A-4AF2-B654-BAB4D0957928 ms.technology: xamarin-forms author: davidbritch ms.author: dabritch ms.date: 02/12/2018 no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="binding-video-sources-to-the-player"></a>Enlazar orígenes de vídeo con el reproductor

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

Cuando la propiedad `Source` de la vista `VideoPlayer` se establece en un nuevo archivo de vídeo, el vídeo existente deja de reproducirse y se inicia el nuevo vídeo. Esto se demuestra mediante la página **Seleccionar vídeo web** del ejemplo [**VideoPlayerDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos). En esta página, se incluye un elemento `ListView` con los títulos de los tres vídeos a los que se hace referencia en el archivo **App.xaml**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.SelectWebVideoPage"
             Title="Select Web Video">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0" />

        <ListView Grid.Row="1"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Elephant's Dream</x:String>
                    <x:String>Big Buck Bunny</x:String>
                    <x:String>Sintel</x:String>
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </Grid>
</ContentPage>
```

Al seleccionar un vídeo, se ejecuta el controlador de eventos `ItemSelected` del archivo de código subyacente. El controlador elimina los espacios en blanco y los apóstrofos del título y usa el resultado como una clave para obtener uno de los recursos definidos en el archivo **App.xaml**. Después, el objeto `UriVideoSource` se establece en la propiedad `Source` del elemento `VideoPlayer`.

```csharp
namespace VideoPlayerDemos
{
    public partial class SelectWebVideoPage : ContentPage
    {
        public SelectWebVideoPage()
        {
            InitializeComponent();
        }

        void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs args)
        {
            if (args.SelectedItem != null)
            {
                string key = ((string)args.SelectedItem).Replace(" ", "").Replace("'", "");
                videoPlayer.Source = (UriVideoSource)Application.Current.Resources[key];
            }
        }
    }
}
```

Cuando se carga la primera página, no se selecciona ningún elemento en `ListView`, por lo que tendrá que seleccionar uno para que el vídeo empiece a reproducirse:

[![Seleccionar vídeo web](source-bindings-images/selectwebvideo-small.png "Seleccionar vídeo web")](source-bindings-images/selectwebvideo-large.png#lightbox "Seleccionar vídeo web")

La propiedad `Source` de `VideoPlayer` se complementa con una propiedad enlazable, lo que quiere decir que puede ser el objetivo de un enlace de datos. Esto se demuestra mediante la página **Enlazar a VideoPlayer**. El marcado del archivo es compatible con la clase siguiente **BindToVideoPlayer.xaml**, que encapsula un título para un vídeo y un objeto correspondiente `VideoSource`:

```csharp
namespace VideoPlayerDemos
{
    public class VideoInfo
    {
        public string DisplayName { set; get; }

        public VideoSource VideoSource { set; get; }

        public override string ToString()
        {
            return DisplayName;
        }
    }
}
```

El elemento `ListView` del archivo **BindToVideoPlayer.xaml** contiene una matriz de estos objetos `VideoInfo` y cada uno se inicializa con un título del vídeo y el objeto `UriVideoSource` del diccionario de recursos en **App.xaml**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:VideoPlayerDemos"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.BindToVideoPlayerPage"
             Title="Bind to VideoPlayer">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           Source="{Binding Source={x:Reference listView},
                                            Path=SelectedItem.VideoSource}" />

        <ListView x:Name="listView"
                  Grid.Row="1">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:VideoInfo}">
                    <local:VideoInfo DisplayName="Elephant's Dream"
                                     VideoSource="{StaticResource ElephantsDream}" />

                    <local:VideoInfo DisplayName="Big Buck Bunny"
                                     VideoSource="{StaticResource BigBuckBunny}" />

                    <local:VideoInfo DisplayName="Sintel"
                                     VideoSource="{StaticResource Sintel}" />
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </Grid>
</ContentPage>
```

La propiedad `Source` del elemento `VideoPlayer` se enlaza al elemento `ListView`. El elemento `Path` del enlace se especifica como `SelectedItem.VideoSource`, que es un trazado compuesto formado por dos propiedades: `SelectedItem` es una propiedad de `ListView`. El elemento seleccionado es del tipo `VideoInfo`, que tiene una propiedad `VideoSource`.

Como con la primera página **Seleccionar vídeo web**, de manera inicial no se selecciona ningún elemento desde `ListView`, por lo que necesita seleccionar uno de los vídeos para que empiece a reproducirse.

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de reproductor de vídeo (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
