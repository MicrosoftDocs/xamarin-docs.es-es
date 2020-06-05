---
title: "Acceso a la biblioteca de vídeos del dispositivo" description: "En este artículo se explica cómo usar Xamarin.Forms para acceder a la biblioteca de vídeos del dispositivo en una aplicación de reproductor de vídeo."
ms.prod: xamarin ms.assetid: 364C1D43-EAAE-45B9-BE24-0DA5AE74C4D9 ms.technology: xamarin-forms author: davidbritch ms.author: dabritch ms.date: 02/12/2018 no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="accessing-the-devices-video-library"></a>Acceder a la biblioteca de vídeos del dispositivo

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

Los dispositivos móviles y los equipos de escritorio más modernos tienen la capacidad de grabar vídeos mediante la cámara que llevan incorporada. Los vídeos que un usuario crea se almacenan como archivos en el dispositivo. Estos archivos se pueden recuperar de la biblioteca de imágenes, y la clase `VideoPlayer` puede reproducirlos igual que cualquier otro vídeo.

## <a name="the-photo-picker-dependency-service"></a>El servicio de dependencia del selector de fotos

Cada una de las plataformas incluye una utilidad que permite al usuario seleccionar una foto o un vídeo de la biblioteca de imágenes del dispositivo. El primer paso para reproducir un vídeo de la biblioteca de imágenes del dispositivo consiste en crear un servicio de dependencia que invoque al selector de imagen en cada plataforma. El servicio de dependencia descrito anteriormente es muy similar al que se define en el artículo [**Seleccionar una foto de la biblioteca de imágenes**](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md), salvo que el selector de vídeo devuelve un nombre de archivo en lugar de un objeto `Stream`.

El proyecto de la biblioteca de .NET Standard define una interfaz denominada `IVideoPicker` para el servicio de dependencia:

```csharp
namespace FormsVideoLibrary
{
    public interface IVideoPicker
    {
        Task<string> GetVideoFileAsync();
    }
}
```

Cada una de las plataformas contiene una clase denominada `VideoPicker` que implementa esta interfaz.

### <a name="the-ios-video-picker"></a>El selector de vídeo de iOS

`VideoPicker` de iOS utiliza [`UIImagePickerController`](xref:UIKit.UIImagePickerController) para acceder a la biblioteca de imágenes y especifica que dicho acceso debe restringirse a los vídeos (denominados "películas") de la propiedad `MediaType` de iOS. Tenga en cuenta que `VideoPicker` implementa explícitamente la interfaz de `IVideoPicker`. Tenga en cuenta también el atributo `Dependency` que identifica esta clase como un servicio de dependencia. Estos son los dos requisitos que permiten a Xamarin.Forms encontrar el servicio de dependencia en el proyecto de la plataforma:

```csharp
using System;
using System.Threading.Tasks;
using UIKit;
using Xamarin.Forms;

[assembly: Dependency(typeof(FormsVideoLibrary.iOS.VideoPicker))]

namespace FormsVideoLibrary.iOS
{
    public class VideoPicker : IVideoPicker
    {
        TaskCompletionSource<string> taskCompletionSource;
        UIImagePickerController imagePicker;

        public Task<string> GetVideoFileAsync()
        {
            // Create and define UIImagePickerController
            imagePicker = new UIImagePickerController
            {
                SourceType = UIImagePickerControllerSourceType.SavedPhotosAlbum,
                MediaTypes = new string[] { "public.movie" }
            };

            // Set event handlers
            imagePicker.FinishedPickingMedia += OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled += OnImagePickerCancelled;

            // Present UIImagePickerController;
            UIWindow window = UIApplication.SharedApplication.KeyWindow;
            var viewController = window.RootViewController;
            viewController.PresentViewController(imagePicker, true, null);

            // Return Task object
            taskCompletionSource = new TaskCompletionSource<string>();
            return taskCompletionSource.Task;
        }

        void OnImagePickerFinishedPickingMedia(object sender, UIImagePickerMediaPickedEventArgs args)
        {
            if (args.MediaType == "public.movie")
            {
                taskCompletionSource.SetResult(args.MediaUrl.AbsoluteString);
            }
            else
            {
                taskCompletionSource.SetResult(null);
            }
            imagePicker.DismissModalViewController(true);
        }

        void OnImagePickerCancelled(object sender, EventArgs args)
        {
            taskCompletionSource.SetResult(null);
            imagePicker.DismissModalViewController(true);
        }
    }
}
```

### <a name="the-android-video-picker"></a>El selector de vídeo de Android

La implementación de Android de `IVideoPicker` requiere un método de devolución de llamada que forma parte de la actividad de la aplicación. Por ese motivo, la clase `MainActivity` define dos propiedades, un campo y un método de devolución de llamada:

```csharp
namespace VideoPlayerDemos.Droid
{
    ···
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            Current = this;
            ···
        }

        // Field, properties, and method for Video Picker
        public static MainActivity Current { private set; get; }

        public static readonly int PickImageId = 1000;

        public TaskCompletionSource<string> PickImageTaskCompletionSource { set; get; }

        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);

            if (requestCode == PickImageId)
            {
                if ((resultCode == Result.Ok) && (data != null))
                {
                    // Set the filename as the completion of the Task
                    PickImageTaskCompletionSource.SetResult(data.DataString);
                }
                else
                {
                    PickImageTaskCompletionSource.SetResult(null);
                }
            }
        }
    }
}
```

El método `OnCreate` en `MainActivity` almacena su propia instancia en la propiedad `Current` estática. Esto permite a la implementación de `IVideoPicker` obtener la instancia de `MainActivity` para iniciar el selector **Seleccione vídeo**:

```csharp
using System;
using System.Threading.Tasks;
using Android.Content;
using Xamarin.Forms;

// Need application's MainActivity
using VideoPlayerDemos.Droid;

[assembly: Dependency(typeof(FormsVideoLibrary.Droid.VideoPicker))]

namespace FormsVideoLibrary.Droid
{
    public class VideoPicker : IVideoPicker
    {
        public Task<string> GetVideoFileAsync()
        {
            // Define the Intent for getting images
            Intent intent = new Intent();
            intent.SetType("video/*");
            intent.SetAction(Intent.ActionGetContent);

            // Get the MainActivity instance
            MainActivity activity = MainActivity.Current;

            // Start the picture-picker activity (resumes in MainActivity.cs)
            activity.StartActivityForResult(
                Intent.CreateChooser(intent, "Select Video"),
                MainActivity.PickImageId);

            // Save the TaskCompletionSource object as a MainActivity property
            activity.PickImageTaskCompletionSource = new TaskCompletionSource<string>();

            // Return Task object
            return activity.PickImageTaskCompletionSource.Task;
        }
    }
}
```

Las adiciones al objeto `MainActivity` son el único código de la solución [**VideoPlayerDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos) en que el código de aplicación normal debe modificarse para admitir las clases `FormsVideoLibrary`.

### <a name="the-uwp-video-picker"></a>El selector de vídeo de UWP

La implementación de UWP de la interfaz de `IVideoPicker` utiliza el [`FileOpenPicker`](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/) de UWP. Inicia la búsqueda de archivos con la biblioteca de imágenes y restringe los tipos de archivo a MP4 y WMV (vídeo de Windows Media):

```csharp
using System;
using System.Threading.Tasks;
using Windows.Storage;
using Windows.Storage.Pickers;
using Xamarin.Forms;

[assembly: Dependency(typeof(FormsVideoLibrary.UWP.VideoPicker))]

namespace FormsVideoLibrary.UWP
{
    public class VideoPicker : IVideoPicker
    {
        public async Task<string> GetVideoFileAsync()
        {
            // Create and initialize the FileOpenPicker
            FileOpenPicker openPicker = new FileOpenPicker
            {
                ViewMode = PickerViewMode.Thumbnail,
                SuggestedStartLocation = PickerLocationId.PicturesLibrary
            };

            openPicker.FileTypeFilter.Add(".wmv");
            openPicker.FileTypeFilter.Add(".mp4");

            // Get a file and return the path
            StorageFile storageFile = await openPicker.PickSingleFileAsync();
            return storageFile?.Path;
        }
    }
}
```

## <a name="invoking-the-dependency-service"></a>Invocar el servicio de dependencia

En la página **Reproducir vídeo de la biblioteca** del programa [**VideoPlayerDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos) se muestra cómo utilizar el servicio de dependencia del selector de vídeo. El archivo XAML contiene una instancia de `VideoPlayer` y un `Button` con la etiqueta **Mostrar la biblioteca de vídeos**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayLibraryVideoPage"
             Title="Play Library Video">
    <StackLayout>
        <video:VideoPlayer x:Name="videoPlayer"
                           VerticalOptions="FillAndExpand" />

        <Button Text="Show Video Library"
                Margin="10"
                HorizontalOptions="Center"
                Clicked="OnShowVideoLibraryClicked" />
    </StackLayout>
</ContentPage>
```

El archivo de código subyacente contiene el controlador de `Clicked` para el `Button`. Para invocar el servicio de dependencia es necesario llamar a `DependencyService.Get` para obtener la implementación de una interfaz de `IVideoPicker` en el proyecto de plataforma. A continuación, se llama al método `GetVideoFileAsync` en esa instancia:

```csharp
namespace VideoPlayerDemos
{
    public partial class PlayLibraryVideoPage : ContentPage
    {
        public PlayLibraryVideoPage()
        {
            InitializeComponent();
        }

       async void OnShowVideoLibraryClicked(object sender, EventArgs args)
        {
            Button btn = (Button)sender;
            btn.IsEnabled = false;

            string filename = await DependencyService.Get<IVideoPicker>().GetVideoFileAsync();

            if (!String.IsNullOrWhiteSpace(filename))
            {
                videoPlayer.Source = new FileVideoSource
                {
                    File = filename
                };
            }

            btn.IsEnabled = true;
        }
    }
}
```

Posteriormente, el controlador de `Clicked` utiliza ese nombre de archivo para crear un objeto `FileVideoSource` y establecerlo en la propiedad `Source` del `VideoPlayer`.

Cada una de las clases `VideoPlayerRenderer` contiene código en su método `SetSource` para objetos de tipo `FileVideoSource`. Se muestran a continuación:

### <a name="handling-ios-files"></a>Control de archivos de iOS

La versión de iOS de `VideoPlayerRenderer` procesa los objetos `FileVideoSource` mediante el uso del método `Asset.FromUrl` estático con el nombre de archivo. Esto crea un objeto `AVAsset` que representa el archivo de la biblioteca de imágenes del dispositivo:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void SetSource()
        {
            AVAsset asset = null;
            ···
            else if (Element.Source is FileVideoSource)
            {
                string uri = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    asset = AVAsset.FromUrl(new NSUrl(uri));
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="handling-android-files"></a>Control de archivos de Android

Al procesar los objetos de tipo `FileVideoSource`, la implementación de Android de `VideoPlayerRenderer` utiliza el método `SetVideoPath` de `VideoView` para especificar el archivo de biblioteca de imágenes del dispositivo:

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
            ···
            else if (Element.Source is FileVideoSource)
            {
                string filename = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(filename))
                {
                    videoView.SetVideoPath(filename);
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="handling-uwp-files"></a>Control de archivos de UWP

Al controlar los objetos de tipo `FileVideoSource`, la implementación de UWP del método `SetSource` debe crear un objeto `StorageFile`, abrir ese archivo para leerlo y pasar el objeto de secuencia al método `SetSource` del `MediaElement`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
        async void SetSource()
        {
            bool hasSetSource = false;
            ···
            else if (Element.Source is FileVideoSource)
            {
                // Code requires Pictures Library in Package.appxmanifest Capabilities to be enabled
                string filename = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(filename))
                {
                    StorageFile storageFile = await StorageFile.GetFileFromPathAsync(filename);
                    IRandomAccessStreamWithContentType stream = await storageFile.OpenReadAsync();
                    Control.SetSource(stream, storageFile.ContentType);
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

En todas las plataformas, el vídeo empieza a reproducirse casi inmediatamente después de establecer el origen de vídeo porque el archivo se encuentra en el dispositivo y no debe descargarse.

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de reproductor de vídeo (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
- [Seleccionar una foto de la biblioteca de imágenes](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
