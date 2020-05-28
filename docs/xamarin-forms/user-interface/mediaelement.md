---
title: Xamarin.FormsMediaElement
description: En este artículo se explica cómo usar MediaElement para reproducir vídeo y audio en una Xamarin.Forms aplicación.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1dfa51177bba3ebf1e3e29208cc926c77567a048
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84134243"
---
# <a name="xamarinforms-mediaelement"></a>Xamarin.FormsMediaElement

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos/)

[`MediaElement`](xref:Xamarin.Forms.MediaElement)es una vista para reproducir vídeo y audio. Los medios admitidos por la plataforma subyacente se pueden reproducir desde los orígenes siguientes:

- La web, mediante un URI (HTTP o HTTPS).
- Un recurso incrustado en la aplicación de la plataforma con el `ms-appx:///` esquema del URI.
- Archivos que proceden de las carpetas de datos locales y temporales de la aplicación, mediante el `ms-appdata:///` esquema de URI.
- Biblioteca del dispositivo.

[`MediaElement`](xref:Xamarin.Forms.MediaElement)puede utilizar los controles de reproducción de la plataforma, que se conocen como controles de transporte. Sin embargo, están deshabilitadas de forma predeterminada y se pueden reemplazar por sus propios controles de transporte. Las capturas de `MediaElement` pantalla siguientes muestran cómo reproducir un vídeo con los controles de transporte de plataforma:

[![Captura de pantalla de un MediaElement que reproduce un vídeo, en iOS y Android](mediaelement-images/playback-controls.png "Cómo reproducir un vídeo")](mediaelement-images/playback-controls-large.png#lightbox "Cómo reproducir un vídeo")

[`MediaElement`](xref:Xamarin.Forms.MediaElement)está disponible en Xamarin.Forms 4,5. Sin embargo, actualmente es experimental y solo se puede usar agregando la siguiente línea de código al archivo *app.Xaml.CS* :

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

> [!NOTE]
> [`MediaElement`](xref:Xamarin.Forms.MediaElement)está disponible en iOS, Android, el Plataforma universal de Windows (UWP), macOS, Windows Presentation Foundation y Tizen.

[`MediaElement`](xref:Xamarin.Forms.MediaElement)define las siguientes propiedades:

- [`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect), de tipo [`Aspect`](xref:Xamarin.Forms.Aspect) , determina cómo se escalará el elemento multimedia para ajustarse al área de presentación. El valor predeterminado de esta propiedad es `AspectFit`.
- [`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay), de tipo `bool` , indica si la reproducción multimedia se iniciará automáticamente cuando [`Source`](xref:Xamarin.Forms.MediaElement.Source) se establezca la propiedad. El valor predeterminado de esta propiedad es `true`.
- [`BufferingProgress`](xref:Xamarin.Forms.MediaElement.BufferingProgress), de tipo `double` , indica el progreso del almacenamiento en búfer actual. El valor predeterminado de esta propiedad es 0,0.
- [`CanSeek`](xref:Xamarin.Forms.MediaElement.CanSeek), de tipo `bool` , indica si se puede cambiar la posición de los elementos multimedia estableciendo el valor de la [`Position`](xref:Xamarin.Forms.MediaElement.Position) propiedad. Se trata de una propiedad de solo lectura.
- [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState), de tipo [`MediaElementState`](xref:Xamarin.Forms.MediaElementState) , indica el estado actual del control. Se trata de una propiedad de solo lectura, cuyo valor predeterminado es `MediaElementState.Closed` .
- [`Duration`](xref:Xamarin.Forms.MediaElement.Duration), de tipo `TimeSpan?` , indica la duración del medio abierto actualmente. Se trata de una propiedad de solo lectura cuyo valor predeterminado es `null` .
- [`IsLooping`](xref:Xamarin.Forms.MediaElement.IsLooping), de tipo `bool` , describe si el origen multimedia cargado actualmente debe reanudar la reproducción desde el principio después de llegar al final. El valor predeterminado de esta propiedad es `false`.
- [`KeepScreenOn`](xref:Xamarin.Forms.MediaElement.KeepScreenOn), de tipo `bool` , determina si la pantalla del dispositivo debe permanecer activada durante la reproducción multimedia. El valor predeterminado de esta propiedad es `false`.
- [`Position`](xref:Xamarin.Forms.MediaElement.Position), de tipo `TimeSpan` , describe el progreso actual a través del tiempo de reproducción del elemento multimedia. El valor predeterminado de esta propiedad es `TimeSpan.Zero`.
- [`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls), de tipo `bool` , determina si se muestran los controles de reproducción de plataformas. El valor predeterminado de esta propiedad es `false`.
- [`Source`](xref:Xamarin.Forms.MediaElement.Source), de tipo [`MediaSource`](xref:Xamarin.Forms.MediaSource) , indica el origen del medio cargado en el control.
- [`VideoHeight`](xref:Xamarin.Forms.MediaElement.VideoHeight), de tipo `int` , indica el alto del control. Se trata de una propiedad de solo lectura.
- [`VideoWidth`](xref:Xamarin.Forms.MediaElement.VideoWidth), de tipo `int` , indica el ancho del control. Se trata de una propiedad de solo lectura.
- [`Volume`](xref:Xamarin.Forms.MediaElement.Volume), de tipo `double` , determina el volumen del medio, que se representa en una escala lineal entre 0 y 1. Esta propiedad usa un `TwoWay` enlace y su valor predeterminado es 1.

Estas propiedades, con la excepción de la `CanSeek` propiedad, están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

La [`MediaElement`](xref:Xamarin.Forms.MediaElement) clase también define cuatro eventos:

- [`MediaOpened`](xref:Xamarin.Forms.MediaElement.MediaOpened)se desencadena cuando se ha validado y abierto el flujo multimedia.
- [`MediaEnded`](xref:Xamarin.Forms.MediaElement.MediaEnded)se desencadena cuando `MediaElement` finaliza la reproducción de su multimedia.
- [`MediaFailed`](xref:Xamarin.Forms.MediaElement.MediaFailed)se desencadena cuando hay un error asociado con el origen del medio.
- [`SeekCompleted`](xref:Xamarin.Forms.MediaElement.SeekCompleted)se desencadena cuando el punto de búsqueda de una operación de búsqueda solicitada está listo para la reproducción.

Además, [`MediaElement`](xref:Xamarin.Forms.MediaElement) incluye [`Play`](xref:Xamarin.Forms.MediaElement.Play) [`Pause`](xref:Xamarin.Forms.MediaElement.Pause) los métodos, y [`Stop`](xref:Xamarin.Forms.MediaElement.Stop) .

Para obtener información sobre los formatos multimedia compatibles en Android, consulte [formatos de medios admitidos](https://developer.android.com/guide/topics/media/media-formats) en Developer.Android.com. Para obtener información sobre los formatos multimedia compatibles en el Plataforma universal de Windows (UWP), consulte [Códecs compatibles](/windows/uwp/audio-video-camera/supported-codecs).

## <a name="play-remote-media"></a>Reproducir medios remotos

[`MediaElement`](xref:Xamarin.Forms.MediaElement)Puede reproducir archivos multimedia remotos mediante los esquemas de URI http y https. Esto se logra estableciendo la [`Source`](xref:Xamarin.Forms.MediaElement.Source) propiedad en el URI del archivo multimedia:

```xaml
<MediaElement Source="https://sec.ch9.ms/ch9/5d93/a1eab4bf-3288-4faf-81c4-294402a85d93/XamarinShow_mid.mp4"
              ShowsPlaybackControls="True" />
```

De forma predeterminada, los medios que define la [`Source`](xref:Xamarin.Forms.MediaElement.Source) propiedad se reproducen inmediatamente después de abrir los elementos multimedia. Para suprimir la reproducción de medios automática, establezca la [`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay) propiedad en `false` .

Los controles de reproducción multimedia están deshabilitados de forma predeterminada y se habilitan estableciendo la [`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls) propiedad en `true` . [`MediaElement`](xref:Xamarin.Forms.MediaElement)usará los controles de reproducción de la plataforma.

## <a name="play-local-media"></a>Reproducir medios locales

Los medios locales se pueden reproducir desde los orígenes siguientes:

- Un recurso incrustado en la aplicación de la plataforma con el `ms-appx:///` esquema del URI.
- Archivos que proceden de las carpetas de datos locales y temporales de la aplicación, mediante el `ms-appdata:///` esquema de URI.
- Biblioteca del dispositivo.

Para obtener más información sobre estos esquemas de URI, vea [esquemas de URI](/windows/uwp/app-resources/uri-schemes).

### <a name="play-media-embedded-in-the-app-package"></a>Reproducir medios incrustados en el paquete de la aplicación

[`MediaElement`](xref:Xamarin.Forms.MediaElement)Puede reproducir archivos multimedia que están incrustados en el paquete de la aplicación mediante el `ms-appx:///` esquema de URI. Los archivos multimedia se incrustan en el paquete de la aplicación colocándolos en el proyecto de plataforma.

Almacenar un archivo multimedia en el proyecto de plataforma es diferente para cada plataforma:

- En iOS, los archivos multimedia deben almacenarse en la carpeta **recursos** o en una subcarpeta de la carpeta **recursos** . El archivo multimedia debe tener un `Build Action` de `BundleResource` .
- En Android, los archivos multimedia deben almacenarse en una subcarpeta de **recursos** denominada **sin formato**. La carpeta **raw** no puede contener subcarpetas. El archivo multimedia debe tener un `Build Action` de `AndroidResource` .
- En UWP, los archivos multimedia se pueden almacenar en cualquier carpeta del proyecto. El archivo multimedia debe tener un `BuildAction` de `Content` .

Los archivos multimedia que cumplen estos criterios se pueden reproducir con el `ms-appx:///` esquema URI:

```xaml
<MediaElement Source="ms-appx:///XamarinForms101UsingEmbeddedImages.mp4"
              ShowsPlaybackControls="True" />
```

Al utilizar el enlace de datos, se puede usar un convertidor de valores para aplicar este esquema de URI:

```csharp
public class VideoSourceConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        if (value == null)
            return null;

        if (string.IsNullOrWhiteSpace(value.ToString()))
            return null;

        if (Device.RuntimePlatform == Device.UWP)
            return new Uri($"ms-appx:///Assets/{value}");
        else
            return new Uri($"ms-appx:///{value}");
    }
    // ...
}
```

`VideoSourceConverter`Después, se puede usar una instancia de para aplicar el `ms-appx:///` esquema de URI a un archivo multimedia incrustado:

```xaml
<MediaElement Source="{Binding MediaSource, Converter={StaticResource VideoSourceConverter}}"
              ShowsPlaybackControls="True" />
```

Para obtener más información sobre el esquema de URI de MS-appx, consulte [MS-appx y MS-appx-web](/windows/uwp/app-resources/uri-schemes#ms-appx-and-ms-appx-web).

### <a name="play-media-from-the-apps-local-and-temporary-folders"></a>Reproducir medios desde las carpetas locales y temporales de la aplicación

[`MediaElement`](xref:Xamarin.Forms.MediaElement)Puede reproducir archivos multimedia que se copian en las carpetas de datos locales o temporales de la aplicación, mediante el `ms-appdata:///` esquema de URI.

En el ejemplo siguiente se muestra la [`Source`](xref:Xamarin.Forms.MediaElement.Source) propiedad establecida en un archivo multimedia almacenado en la carpeta de datos locales de la aplicación:

```xaml
<MediaElement Source="ms-appdata:///local/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

En el ejemplo siguiente se muestra la [`Source`](xref:Xamarin.Forms.MediaElement.Source) propiedad de un archivo multimedia almacenado en la carpeta de datos temporales de la aplicación:

```xaml
<MediaElement Source="ms-appdata:///temp/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

> [!IMPORTANT]
> Además de reproducir archivos multimedia que se almacenan en las carpetas de datos locales o temporales de la aplicación, UWP también puede reproducir archivos multimedia que se encuentran en la carpeta móvil de la aplicación. Esto se puede lograr si se prefija el archivo multimedia con `ms-appdata:///roaming/` .

Al utilizar el enlace de datos, se puede usar un convertidor de valores para aplicar este esquema de URI:

```csharp
public class VideoSourceConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        if (value == null)
            return null;

        if (string.IsNullOrWhiteSpace(value.ToString()))
            return null;

        return new Uri($"ms-appdata:///{value}");
    }
    // ...
}
```

`VideoSourceConverter`Después, se puede usar una instancia de para aplicar el `ms-appdata:///` esquema de URI a un archivo multimedia en la carpeta de datos local o temporal de la aplicación:

```xaml
<MediaElement Source="{Binding MediaSource, Converter={StaticResource VideoSourceConverter}}"
              ShowsPlaybackControls="True" />
```

Para obtener más información sobre el esquema de URI MS-AppData, consulte [MS-AppData](/windows/uwp/app-resources/uri-schemes#ms-appdata).

#### <a name="copying-a-media-file-to-the-apps-local-or-temporary-data-folder"></a>Copiar un archivo multimedia en la carpeta de datos local o temporal de la aplicación

La reproducción de un archivo multimedia almacenado en la carpeta de datos local o temporal de la aplicación requiere que la aplicación copie el archivo multimedia. Esto puede realizarse, por ejemplo, mediante la copia de un archivo multimedia desde el paquete de la aplicación:

```csharp
// This method copies the video from the app package to the app data
// directory for your app. To copy the video to the temp directory
// for your app, comment out the first line of code, and uncomment
// the second line of code.
public static async Task CopyVideoIfNotExists(string filename)
{
    string folder = FileSystem.AppDataDirectory;
    //string folder = Path.GetTempPath();
    string videoFile = Path.Combine(folder, "XamarinVideo.mp4");

    if (!File.Exists(videoFile))
    {
        using (Stream inputStream = await FileSystem.OpenAppPackageFileAsync(filename))
        {
            using (FileStream outputStream = File.Create(videoFile))
            {
                await inputStream.CopyToAsync(outputStream);
            }
        }
    }
}
```

> [!NOTE]
> En el ejemplo de código anterior se usa la `FileSystem` clase incluida en Xamarin.Essentials . Para obtener más información, vea [ Xamarin.Essentials : aplicaciones auxiliares del sistema de archivos](~/essentials/file-system-helpers.md?context=xamarin%2Fxamarin-forms&tabs=android).

### <a name="play-media-from-the-device-library"></a>Reproducir medios desde la biblioteca de dispositivos

La mayoría de los dispositivos móviles y equipos de escritorio modernos tienen la capacidad de grabar vídeos y audio con la cámara y el micrófono del dispositivo. El medio que se crea se almacena a continuación como archivos en el dispositivo. Estos archivos se pueden recuperar de la biblioteca y reproducir por el [`MediaElement`](xref:Xamarin.Forms.MediaElement) .

Cada una de las plataformas incluye una instalación que permite al usuario seleccionar archivos multimedia de la biblioteca del dispositivo. En Xamarin.Forms , los proyectos de la plataforma pueden invocar esta funcionalidad y la clase puede llamarla [`DependencyService`](xref:Xamarin.Forms.DependencyService) .

El servicio de dependencia de selección de vídeo usado en la aplicación de ejemplo es muy similar a uno definido en la [selección de una foto de la biblioteca de imágenes](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md), con la excepción de que el selector devuelve un nombre de archivo en lugar de un `Stream` objeto. El proyecto de código compartido define una interfaz denominada `IVideoPicker` , que define un único método denominado `GetVideoFileAsync` . Cada plataforma implementa esta interfaz en una `VideoPicker` clase.

En el ejemplo de código siguiente se muestra cómo recuperar un archivo multimedia de la biblioteca de dispositivos:

```csharp
string filename = await DependencyService.Get<IVideoPicker>().GetVideoFileAsync();
if (!string.IsNullOrWhiteSpace(filename))
{
    mediaElement.Source = new FileMediaSource
    {
        File = filename
    };
}
```

El servicio de dependencia de selección de vídeo se invoca llamando al `DependencyService.Get` método para obtener la implementación de una `IVideoPicker` interfaz en el proyecto de plataforma. `GetVideoFileAsync`A continuación, se llama al método en esa instancia y el nombre de archivo devuelto se usa para crear un [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) objeto y establecerlo en la [`Source`](xref:Xamarin.Forms.MediaElement.Source) propiedad de [`MediaElement`](xref:Xamarin.Forms.MediaElement) .

## <a name="change-video-aspect-ratio"></a>Cambiar la relación de aspecto de vídeo

La [`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect) propiedad determina cómo se escalará el medio de vídeo para ajustarse al área de presentación. De forma predeterminada, esta propiedad se establece en el `AspectFit` miembro de la enumeración, pero se puede establecer en cualquiera de los [`Aspect`](xref:Xamarin.Forms.Aspect) miembros de enumeración:

- `AspectFit`indica que el vídeo se mostrará en la pantalla, si es necesario, para que quepa en el área de visualización, a la vez que se conserva la relación de aspecto.
- `AspectFill`indica que el vídeo se recortará para que rellene el área de visualización, a la vez que conserva la relación de aspecto.
- `Fill`indica que el vídeo se ajustará para rellenar el área de presentación.

## <a name="poll-for-position-data"></a>Sondear datos de posición

La notificación de cambio de propiedad para la [`Position`](xref:Xamarin.Forms.MediaElement.Position) propiedad enlazable solo se desencadena en momentos clave como la reproducción inicial y final, y se produce una pausa. Por lo tanto, el enlace de datos a la `Position` propiedad no producirá datos de posición precisos. En su lugar, debe configurar un temporizador y sondear la propiedad.

Una buena forma de hacerlo es en la `OnAppearing` invalidación de la página que requiere los datos de la posición a medida que se reproduce el medio:

```csharp
bool polling = true;

protected override void OnAppearing()
{
    base.OnAppearing();

    Device.StartTimer(TimeSpan.FromMilliseconds(1000), () =>
    {
        Device.BeginInvokeOnMainThread(() =>
        {
            positionLabel.Text = mediaElement.Position.ToString("hh\\:mm\\:ss");
        });
        return polling;
    });
}

protected override void OnDisappearing()
{
    base.OnDisappearing();
    polling = false;
}
```

En este ejemplo, la `OnAppearing` invalidación inicia un temporizador que se actualiza `positionLabel` con el `Position` valor cada segundo. La devolución de llamada del temporizador se invoca cada segundo, hasta que la devolución de llamada vuelve `false` . Cuando se produce la navegación por la página `OnDisappearing` , se ejecuta la invalidación, lo que detiene la devolución de llamada del temporizador.

## <a name="understand-mediasource-types"></a>Descripción de los tipos MediaSource

Un [`MediaElement`](xref:Xamarin.Forms.MediaElement) puede reproducir elementos multimedia estableciendo su [`Source`](xref:Xamarin.Forms.MediaElement.Source) propiedad en un archivo multimedia local o remoto. La `Source` propiedad es de tipo [`MediaSource`](xref:Xamarin.Forms.MediaSource) y esta clase define dos métodos estáticos:

- [`FromFile`](xref:Xamarin.Forms.MediaSource.FromFile*), devuelve una [`MediaSource`](xref:Xamarin.Forms.MediaSource) instancia de a partir de un `string` argumento.
- [`FromUri`](xref:Xamarin.Forms.MediaSource.FromUri*), devuelve una [`MediaSource`](xref:Xamarin.Forms.MediaSource) instancia de a partir de un `Uri` argumento.

Además, la [`MediaSource`](xref:Xamarin.Forms.MediaSource) clase también tiene operadores implícitos que devuelven `MediaSource` instancias de los `string` `Uri` argumentos y.

> [!NOTE]
> Cuando la [`Source`](xref:Xamarin.Forms.MediaElement.Source) propiedad se establece en XAML, se invoca un convertidor de tipos para devolver una [`MediaSource`](xref:Xamarin.Forms.MediaSource) instancia de un `string` o `Uri` .

La [`MediaSource`](xref:Xamarin.Forms.MediaSource) clase también tiene dos clases derivadas:

- [`UriMediaSource`](xref:Xamarin.Forms.UriMediaSource), que se usa para especificar un archivo multimedia remoto desde un URI. Esta clase tiene una [`Uri`](xref:Xamarin.Forms.UriMediaSource.Uri) propiedad que se puede establecer en `Uri` .
- [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource), que se usa para especificar un archivo multimedia local de un `string` . Esta clase tiene una [`File`](xref:Xamarin.Forms.FileMediaSource.File) propiedad que se puede establecer en `string` . Además, esta clase tiene operadores implícitos para convertir un `string` en un `FileMediaSource` objeto y un `FileMediaSource` objeto en `string` .

> [!NOTE]
> Cuando [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) se crea un objeto en XAML, se invoca un convertidor de tipos para devolver una [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) instancia de un `string` .

## <a name="determine-mediaelement-status"></a>Determinar el estado de MediaElement

La [`MediaElement`](xref:Xamarin.Forms.MediaElement) clase define una propiedad enlazable de solo lectura denominada [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState) , de tipo [`MediaElementState`](xref:Xamarin.Forms.MediaElementState) . Esta propiedad indica el estado actual del control, como si el contenido multimedia se reproduce o está en pausa, o si todavía no está listo para reproducir el medio.

La [`MediaElementState`](xref:Xamarin.Forms.MediaElementState) enumeración define los siguientes miembros:

- `Closed`indica que `MediaElement` no contiene ningún medio.
- `Opening`indica que `MediaElement` está validando e intentando cargar el origen especificado.
- `Buffering`indica que `MediaElement` está cargando los medios para la reproducción. Su [`Position`](xref:Xamarin.Forms.MediaElement.Position) propiedad no avanza durante este estado. Si `MediaElement` se está reproduciendo vídeo, continúa mostrando el último fotograma mostrado.
- `Playing`indica que `MediaElement` está reproduciendo el origen del medio.
- `Paused`indica que no `MediaElement` hace avanzar su [`Position`](xref:Xamarin.Forms.MediaElement.Position) propiedad. Si `MediaElement` se está reproduciendo vídeo, continúa mostrando el fotograma actual.
- `Stopped`indica que el `MediaElement` elemento contiene medios pero no se está reproduciendo o en pausa. Su [`Position`](xref:Xamarin.Forms.MediaElement.Position) propiedad es 0 y no avanza. Si el medio cargado es un vídeo, `MediaElement` muestra el primer fotograma.

Por lo general, no es necesario examinar la [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState) propiedad cuando se usan los [`MediaElement`](xref:Xamarin.Forms.MediaElement) controles de transporte. Sin embargo, esta propiedad se convierte en importante al implementar sus propios controles de transporte.

## <a name="implement-custom-transport-controls"></a>Implementar controles de transporte personalizados

Los controles de transporte de un reproductor multimedia incluyen los botones que realizan las funciones de **reproducción**, **pausa**y **detención**. Estos botones suelen identificarse con iconos conocidos en lugar de texto, y las funciones **Reproducir** y **Pausa** suelen combinarse en un mismo botón.

De forma predeterminada, los [`MediaElement`](xref:Xamarin.Forms.MediaElement) controles de reproducción están deshabilitados. Esto le permite controlar `MediaElement` mediante programación o proporcionando sus propios controles de transporte. Para ello, `MediaElement` incluye [`Play`](xref:Xamarin.Forms.MediaElement.Play) [`Pause`](xref:Xamarin.Forms.MediaElement.Pause) [`Stop`](xref:Xamarin.Forms.MediaElement.Stop) los métodos, y.

En el ejemplo de XAML siguiente se muestra una página que contiene un [`MediaElement`](xref:Xamarin.Forms.MediaElement) control y los controles de transporte personalizados:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MediaElementDemos.CustomTransportPage"
             Title="Custom transport">
    <Grid>
        ...
        <MediaElement x:Name="mediaElement"
                      AutoPlay="False"
                      ... />
        <StackLayout BindingContext="{x:Reference mediaElement}"
                     ...>
            <Button Text="&#x25B6;&#xFE0F; Play"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnPlayPauseButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding CurrentState}"
                                 Value="{x:Static MediaElementState.Playing}">
                        <Setter Property="Text"
                                Value="&#x23F8; Pause" />
                    </DataTrigger>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding CurrentState}"
                                 Value="{x:Static MediaElementState.Buffering}">
                        <Setter Property="IsEnabled"
                                Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>
            <Button Text="&#x23F9; Stop"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnStopButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding CurrentState}"
                                 Value="{x:Static MediaElementState.Stopped}">
                        <Setter Property="IsEnabled"
                                Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>
        </StackLayout>
    </Grid>
</ContentPage>
```

En este ejemplo, los controles de transporte personalizados se definen como [`Button`](xref:Xamarin.Forms.Button) objetos. Sin embargo, solo hay dos `Button` objetos, donde el primero `Button` representa **Play** y **PAUSE**, y el segundo `Button` representa **Stop**. [`DataTrigger`](xref:Xamarin.Forms.DataTrigger)los objetos se usan para habilitar y deshabilitar los botones, y para cambiar el primer botón entre **reproducir** y **pausar**. Para obtener más información acerca de los desencadenadores de datos, vea [ Xamarin.Forms desencadenadores](~/xamarin-forms/app-fundamentals/triggers.md).

El archivo de código subyacente tiene los controladores para los [`Clicked`](xref:Xamarin.Forms.Button.Clicked) eventos:

```csharp
void OnPlayPauseButtonClicked(object sender, EventArgs args)
{
    if (mediaElement.CurrentState == MediaElementState.Stopped ||
        mediaElement.CurrentState == MediaElementState.Paused)
    {
        mediaElement.Play();
    }
    else if (mediaElement.CurrentState == MediaElementState.Playing)
    {
        mediaElement.Pause();
    }
}

void OnStopButtonClicked(object sender, EventArgs args)
{
    mediaElement.Stop();
}
```

El botón **reproducir** se puede presionar una vez que se habilita para iniciar la reproducción:

[![Captura de pantalla de un MediaElement con controles de transporte personalizados, en iOS y Android](mediaelement-images/custom-transport-playback.png "Cómo reproducir un vídeo")](mediaelement-images/custom-transport-playback-large.png#lightbox "Cómo reproducir un vídeo")

Al presionar el botón **pausar** se produce una pausa en la reproducción:

[![Captura de pantalla de un MediaElement con reproducción en pausa, en iOS y Android](mediaelement-images/custom-transport-paused.png "MediaElement con un vídeo en pausa")](mediaelement-images/custom-transport-paused-large.png#lightbox "MediaElement con un vídeo en pausa")

Al presionar el botón **detener** se detiene la reproducción y se devuelve la posición del archivo multimedia al principio.

## <a name="implement-a-custom-position-bar"></a>Implementar una barra de posición personalizada

Los controles de transporte que cada plataforma implementa incluyen una barra de posición. Esta barra es similar a un control deslizante y muestra la ubicación actual del medio dentro de su duración total. Además, puede manipular la barra de posición para desplace hacia delante o hacia atrás hasta una nueva posición en el vídeo.

La implementación de una barra de posición personalizada requiere conocer la duración del medio y la posición de reproducción actual. Estos datos están disponibles en las [`Duration`](xref:Xamarin.Forms.MediaElement.Duration) [`Position`](xref:Xamarin.Forms.MediaElement.Position) propiedades y.

> [!IMPORTANT]
> [`Position`](xref:Xamarin.Forms.MediaElement.Position)Se debe sondear para obtener datos de posición precisos. Para obtener más información, consulte [sondear para datos de posición](#poll-for-position-data).

Una barra de posición personalizada se puede implementar mediante un [`Slider`](xref:Xamarin.Forms.Slider) , como se muestra en el ejemplo siguiente:

```csharp
public class PositionSlider : Slider
{
    public static readonly BindableProperty DurationProperty =
        BindableProperty.Create(nameof(Duration), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(1),
                                propertyChanged: (bindable, oldValue, newValue) =>
                                {
                                    ((PositionSlider)bindable).SetTimeToEnd();
                                    double seconds = ((TimeSpan)newValue).TotalSeconds;
                                    ((Slider)bindable).Maximum = seconds <= 0 ? 1 : seconds;
                                });

    public TimeSpan Duration
    {
        get { return (TimeSpan)GetValue(DurationProperty); }
        set { SetValue(DurationProperty, value); }
    }

    public static readonly BindableProperty PositionProperty =
        BindableProperty.Create(nameof(Position), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(0),
                                propertyChanged: (bindable, oldValue, newValue) => ((PositionSlider)bindable).SetTimeToEnd());

    public TimeSpan Position
    {
        get { return (TimeSpan)GetValue(PositionProperty); }
        set { SetValue(PositionProperty, value); }
    }

    static readonly BindablePropertyKey TimeToEndPropertyKey =
        BindableProperty.CreateReadOnly(nameof(TimeToEnd), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan());

    public static readonly BindableProperty TimeToEndProperty = TimeToEndPropertyKey.BindableProperty;

    public TimeSpan TimeToEnd
    {
        get { return (TimeSpan)GetValue(TimeToEndProperty); }
        private set { SetValue(TimeToEndPropertyKey, value); }
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

    void SetTimeToEnd()
    {
        TimeToEnd = Duration - Position;
    }
}
```

La `PositionSlider` clase define sus propias `Duration` `Position` propiedades y enlazables, y una `TimeToEnd` propiedad enlazable. Las tres propiedades son del tipo `TimeSpan` . El controlador de propiedad modificada para la `Duration` propiedad establece la `Maximum` propiedad de [`Slider`](xref:Xamarin.Forms.Slider) en la `TotalSeconds` propiedad del `TimeSpan` valor. La `TimeToEnd` propiedad se calcula en función de los cambios realizados en las `Duration` `Position` propiedades y, y comienza en la duración del medio y disminuye hasta cero a medida que continúa la reproducción.

`PositionSlider`Se actualiza desde el subyacente [`Slider`](xref:Xamarin.Forms.Slider) cuando `Slider` se mueve para indicar que el medio debe ser avanzado o inverso a una nueva posición. Esto se detecta en el `PropertyChanged` controlador en el `PositionSlider` constructor. El controlador comprueba si hay algún cambio en la propiedad `Value` y, si es diferente de la propiedad `Position`, la propiedad `Position` se establece desde la propiedad `Value`. Para obtener más información acerca del uso de un [`Slider`](xref:Xamarin.Forms.Slider) [ Xamarin.Forms control deslizante](~/xamarin-forms/user-interface/slider.md) , vea

> [!NOTE]
> En Android, el [`Slider`](xref:Xamarin.Forms.Slider) solo tiene 1000 pasos discretos, independientemente de la `Minimum` configuración de y `Maximum` . Si la longitud del medio es superior a 1000 segundos, dos valores diferentes se `Position` corresponden con el mismo `Value` `Slider` . Este es el motivo por el que el código anterior comprueba que la nueva posición y la posición existente son mayores que una centésima de la duración total.

En el ejemplo siguiente se muestra el `PositionSlider` que se está consumiendo en una página:

```xaml
<controls:PositionSlider x:Name="positionSlider"
                         BindingContext="{x:Reference mediaElement}"
                         Duration="{Binding Duration}"
                         ValueChanged="OnPositionSliderValueChanged">
    <controls:PositionSlider.Triggers>
        <DataTrigger TargetType="controls:PositionSlider"
                     Binding="{Binding CurrentState}"
                     Value="{x:Static MediaElementState.Buffering}">
            <Setter Property="IsEnabled" Value="False" />
        </DataTrigger>
    </controls:PositionSlider.Triggers>
</controls:PositionSlider>
```

En este ejemplo, la `Duration` propiedad de `PositionSlider` está enlazada a datos a la [`Duration`](xref:Xamarin.Forms.MediaElement.Duration) propiedad de [`MediaElement`](xref:Xamarin.Forms.MediaElement) . Cuando [`Value`](xref:Xamarin.Forms.Slider.Value) cambia la propiedad de [`Slider`](xref:Xamarin.Forms.Slider) , el `ValueChanged` evento se activa y `OnPositionSliderValueChanged` se ejecuta el controlador. Este controlador establece la [`MediaElement.Position`](xref:Xamarin.Forms.MediaElement.Position) propiedad en el valor de la `PositionSlider.Position` propiedad. Por lo tanto, si se arrastran los `Slider` resultados en el cambio de posición de reproducción multimedia:

[![Captura de pantalla de un MediaElement con una barra de posición personalizada, en iOS y Android](mediaelement-images/custom-position-bar.png "MediaElement con una barra de posición personalizada")](mediaelement-images/custom-position-bar-large.png#lightbox "MediaElement con una barra de posición personalizada")

Además, [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) se utiliza un objeto para deshabilitar `PositionSlider` cuando el medio se almacena en el búfer. Para obtener más información acerca de los desencadenadores de datos, vea [ Xamarin.Forms desencadenadores](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="implement-a-custom-volume-control"></a>Implementar un control de volumen personalizado

Los controles de reproducción multimedia implementados por cada plataforma incluyen una barra de volumen. Esta barra es similar a un control deslizante y muestra el volumen del medio. Además, puede manipular la barra de volumen para aumentar o disminuir el volumen.

Una barra de volumen personalizada se puede implementar mediante un [`Slider`](xref:Xamarin.Forms.Slider) , como se muestra en el ejemplo siguiente:

```xaml
<StackLayout>
    <MediaElement AutoPlay="False"
                  Source="{StaticResource AdvancedAsync}" />
    <Slider Maximum="1.0"
            Minimum="0.0"
            Value="{Binding Volume}"
            Rotation="270"
            WidthRequest="100" />
</StackLayout>
```

En este ejemplo, los [`Slider`](xref:Xamarin.Forms.Slider) datos enlazan su `Value` propiedad a la [`Volume`](xref:Xamarin.Forms.MediaElement.Volume) propiedad de [`MediaElement`](xref:Xamarin.Forms.MediaElement) . Esto es posible porque la `Volume` propiedad usa un `TwoWay` enlace. Por lo tanto, el cambio de la propiedad provocará `Value` el cambio de la `Volume` propiedad.

> [!NOTE]
> La [`Volume`](xref:Xamarin.Forms.MediaElement.Volume) propiedad tiene una devolución de llamada vlidation que garantiza que su valor es mayor o igual que 0,0 y menor o igual que 1,0.

Para obtener más información acerca del uso de un [`Slider`](xref:Xamarin.Forms.Slider) [ Xamarin.Forms control deslizante](~/xamarin-forms/user-interface/slider.md) , vea

## <a name="related-links"></a>Vínculos relacionados

- [MediaElementDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos/)
- [Esquemas de URI](/windows/uwp/app-resources/uri-schemes)
- [Xamarin.FormsDesencadenadores](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.FormsHasta](~/xamarin-forms/user-interface/slider.md)
- [Android: formatos de medios admitidos](https://developer.android.com/guide/topics/media/media-formats)
- [UWP: códecs admitidos](/windows/uwp/audio-video-camera/supported-codecs)
