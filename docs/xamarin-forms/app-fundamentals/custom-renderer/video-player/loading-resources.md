---
title: "Carga de vídeos de recursos de aplicación" description: "En este artículo se explica cómo cargar vídeos almacenados como recursos de aplicación en una aplicación de reproductor de vídeo mediante Xamarin.Forms."
ms.prod: xamarin ms.assetid: F75BD540-9354-4C17-A119-57F3DEC66D54 ms.technology: xamarin-forms author: davidbritch ms.author: dabritch ms.date: 02/12/2018 no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="loading-application-resource-videos"></a>Carga de vídeos de recursos de aplicación

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

Los representadores personalizados para la vista `VideoPlayer` son capaces de reproducir archivos de vídeo que se han insertado en los proyectos de cada plataforma como recursos de la aplicación. Pero la versión actual de `VideoPlayer` no puede acceder a los recursos insertados en una biblioteca de .NET Standard.

Para cargar estos recursos, cree una instancia de `ResourceVideoSource` estableciendo la propiedad `Path` en el nombre de archivo (o la carpeta y el nombre de archivo) del recurso. Como alternativa, puede llamar al método `VideoSource.FromResource` estático para hacer referencia al recurso. Después, establezca el objeto `ResourceVideoSource` en la propiedad `Source` de `VideoPlayer`.

## <a name="storing-the-video-files"></a>Almacenamiento de los archivos de vídeo

El almacenamiento de un archivo de vídeo en el proyecto de la plataforma es diferente para cada plataforma.

### <a name="ios-video-resources"></a>Recursos de vídeo de iOS

En un proyecto de iOS, puede almacenar un vídeo en la carpeta **Recursos**, o bien en una subcarpeta de la carpeta **Recursos**. El archivo de vídeo debe tener un elemento `Build Action` de tipo `BundleResource`. Establezca la propiedad `Path` de `ResourceVideoSource` en el nombre de archivo, por ejemplo **MiArchivo.mp4** para un archivo en la carpeta **Recursos**, o bien **MiCarpeta/MiArchivo.mp4**, donde **MiCarpeta** es una subcarpeta de **Recursos**.

En la solución **VideoPlayerDemos**, el proyecto **VideoPlayerDemos.iOS** contiene una subcarpeta de **Recursos** denominada **Videos** que contiene un archivo denominado **iOSApiVideo.mp4**. Se trata de un breve vídeo en el que se muestra cómo usar el sitio web de Xamarin para encontrar documentación para la clase `AVPlayerViewController` de iOS.

### <a name="android-video-resources"></a>Recursos de vídeo de Android

En un proyecto de Android, los vídeos se deben almacenar en una subcarpeta de **Recursos** denominada **raw**. La carpeta **raw** no puede contener subcarpetas. Asigne al archivo de vídeo un elemento `Build Action` de tipo `AndroidResource`. Establezca la propiedad `Path` de `ResourceVideoSource` en el nombre de archivo, por ejemplo, **MiArchivo.mp4**.

El proyecto **VideoPlayerDemos.Android** contiene una subcarpeta de **Recursos** denominada **raw**, que contiene un archivo denominado **AndroidApiVideo.mp4**.

### <a name="uwp-video-resources"></a>Recursos de vídeo de UWP

En un proyecto para Plataforma Universal de Windows, puede almacenar vídeos en cualquier carpeta del proyecto. Asigne al archivo un elemento `Build Action` de tipo `Content`. Establezca la propiedad `Path` de `ResourceVideoSource` en la carpeta y el nombre de archivo, por ejemplo, **MiCarpeta/MiVideo.mp4**.

El proyecto **VideoPlayerDemos.UWP** contiene una carpeta denominada **Videos** con el archivo **UWPApiVideo.mp4**.

## <a name="loading-the-video-files"></a>Carga de los archivos de vídeo

Cada una de las clases de representador de plataforma contiene código en su método `SetSource` para cargar los archivos de vídeo almacenados como recursos.

### <a name="ios-resource-loading"></a>Carga de recursos de iOS

La versión de iOS de `VideoPlayerRenderer` usa el método `GetUrlForResource` de `NSBundle` para cargar el recurso. La ruta de acceso completa se debe dividir en un nombre de archivo, una extensión y un directorio. En el código se usa la clase `Path` del espacio de nombres `System.IO` de .NET para dividir la ruta de acceso en estos componentes:

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
            else if (Element.Source is ResourceVideoSource)
            {
                string path = (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhiteSpace(path))
                {
                    string directory = Path.GetDirectoryName(path);
                    string filename = Path.GetFileNameWithoutExtension(path);
                    string extension = Path.GetExtension(path).Substring(1);
                    NSUrl url = NSBundle.MainBundle.GetUrlForResource(filename, extension, directory);
                    asset = AVAsset.FromUrl(url);
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="android-resource-loading"></a>Carga de recursos de Android

El elemento `VideoPlayerRenderer` de Android usa el nombre de paquete y el nombre de archivo para construir un objeto `Uri`. El nombre del paquete es el nombre de la aplicación, en este caso **VideoPlayerDemos.Android**, que se puede obtener de la propiedad estática `Context.PackageName`. Después, el objeto `Uri` resultante se pasa al método `SetVideoURI` de `VideoView`:

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
            else if (Element.Source is ResourceVideoSource)
            {
                string package = Context.PackageName;
                string path = (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhiteSpace(path))
                {
                    string filename = Path.GetFileNameWithoutExtension(path).ToLowerInvariant();
                    string uri = "android.resource://" + package + "/raw/" + filename;
                    videoView.SetVideoURI(Android.Net.Uri.Parse(uri));
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="uwp-resource-loading"></a>Carga de recursos de UWP

El elemento `VideoPlayerRenderer` de UWP crea un objeto `Uri` para la ruta de acceso y lo establece en la propiedad `Source` de `MediaElement`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        async void SetSource()
        {
            bool hasSetSource = false;
            ···
            else if (Element.Source is ResourceVideoSource)
            {
                string path = "ms-appx:///" + (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhiteSpace(path))
                {
                    Control.Source = new Uri(path);
                    hasSetSource = true;
                }
            }
        }
        ···
    }
}
```

## <a name="playing-the-resource-file"></a>Reproducción del archivo de recursos

En la página **Reproducir recurso de vídeo** de la solución **VideoPlayerDemos** se usa la clase `OnPlatform` para especificar el archivo de vídeo para cada plataforma:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayVideoResourcePage"
             Title="Play Video Resource">
    <video:VideoPlayer>
        <video:VideoPlayer.Source>
            <video:ResourceVideoSource>
                <video:ResourceVideoSource.Path>
                    <OnPlatform x:TypeArguments="x:String">
                        <On Platform="iOS" Value="Videos/iOSApiVideo.mp4" />
                        <On Platform="Android" Value="AndroidApiVideo.mp4" />
                        <On Platform="UWP" Value="Videos/UWPApiVideo.mp4" />
                    </OnPlatform>
                </video:ResourceVideoSource.Path>
            </video:ResourceVideoSource>
        </video:VideoPlayer.Source>
    </video:VideoPlayer>
</ContentPage>
```

Si el recurso de iOS se almacena en la carpeta **Recursos**, y si el recurso de UWP se almacena en la carpeta raíz del proyecto, puede usar el mismo nombre de archivo para cada plataforma. En ese caso, puede establecer ese nombre directamente en la propiedad `Source` de `VideoPlayer`.

Esta es la ejecución de la página:

[![Reproducir recurso de vídeo](loading-resources-images/playvideoresource-small.png "Reproducir recurso de vídeo")](loading-resources-images/playvideoresource-large.png#lightbox "Reproducir recurso de vídeo")

Ya ha visto cómo [cargar vídeos desde un URI web](web-videos.md) y cómo reproducir recursos insertados. Además, puede [cargar vídeos desde la biblioteca de vídeos del dispositivo](accessing-library.md).

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de reproductor de vídeo (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
