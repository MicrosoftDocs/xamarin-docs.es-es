---
title: MediaElement de Xamarin. Forms
description: En este artículo se explica cómo usar MediaElement para reproducir vídeo y audio en una aplicación de Xamarin. Forms.
ms.prod: xamarin
ms.assetid: e65f1e56-a80d-46c7-9ff4-7ae6650a3165
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/18/2020
ms.openlocfilehash: 601a2884cb9ca90ab6681e48afda4c9f1f467932
ms.sourcegitcommit: 5d22f37dfc358678df52a4d17c57261056a72cb7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77674551"
---
# <a name="xamarinforms-mediaelement"></a>MediaElement de Xamarin. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/MediaElementDemos)

[`MediaElement`](xref:Xamarin.Forms.MediaElement) es una vista para reproducir vídeo y audio. Los medios admitidos por la plataforma subyacente se pueden reproducir desde los orígenes siguientes:

- La web, mediante un URI (HTTP o HTTPS).
- Un recurso incrustado en la aplicación de la plataforma mediante el esquema de URI `ms-appx:///`.
- Archivos que proceden de las carpetas de datos locales y temporales de la aplicación, mediante el esquema de URI `ms-appdata:///`.
- Biblioteca del dispositivo.

[`MediaElement`](xref:Xamarin.Forms.MediaElement) puede utilizar los controles de reproducción de la plataforma, que se conocen como controles de transporte. Sin embargo, están deshabilitadas de forma predeterminada y se pueden reemplazar por sus propios controles de transporte. Las capturas de pantalla siguientes muestran `MediaElement` reproducir un vídeo con los controles de transporte de plataforma:

[![Captura de pantalla de un MediaElement que reproduce un vídeo, en iOS y Android](mediaelement-images/playback-controls.png "Cómo reproducir un vídeo")](mediaelement-images/playback-controls-large.png#lightbox "Cómo reproducir un vídeo")

[`MediaElement`](xref:Xamarin.Forms.MediaElement) está disponible en Xamarin. forms 4,5. Sin embargo, actualmente es experimental y solo se puede usar agregando la siguiente línea de código al archivo *app.Xaml.CS* :

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

> [!NOTE]
> [`MediaElement`](xref:Xamarin.Forms.MediaElement) está disponible en iOS, Android, el plataforma universal de Windows (UWP) y plataformas adicionales.

[`MediaElement`](xref:Xamarin.Forms.MediaElement) define las siguientes propiedades:

- [`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect), de tipo [`Aspect`](xref:Xamarin.Forms.Aspect), determina cómo se escalará el elemento multimedia para ajustarse al área de presentación. El valor predeterminado de esta propiedad es `AspectFit`.
- [`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay), de tipo `bool`, indica si la reproducción multimedia se iniciará automáticamente cuando se establezca la propiedad [`Source`](xref:Xamarin.Forms.MediaElement.Source) . El valor predeterminado de esta propiedad es `true`.
- [`BufferingProgress`](xref:Xamarin.Forms.MediaElement.BufferingProgress), de tipo `double`, indica el progreso del almacenamiento en búfer actual. El valor predeterminado de esta propiedad es 0,0.
- [`CanSeek`](xref:Xamarin.Forms.MediaElement.CanSeek), de tipo `bool`, indica si se puede cambiar la posición de los elementos multimedia estableciendo el valor de la propiedad [`Position`](xref:Xamarin.Forms.MediaElement.Position) . Se trata de una propiedad de solo lectura.
- [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState), de tipo [`MediaElementState`](xref:Xamarin.Forms.MediaElementState), indica el estado actual del control. Se trata de una propiedad de solo lectura, cuyo valor predeterminado es `MediaElementState.Closed`.
- [`Duration`](xref:Xamarin.Forms.MediaElement.Duration), de tipo `TimeSpan?`, indica la duración del medio abierto actualmente. Se trata de una propiedad de solo lectura cuyo valor predeterminado es `null`.
- [`IsLooping`](xref:Xamarin.Forms.MediaElement.IsLooping), de tipo `bool`, describe si el origen multimedia cargado actualmente debe reanudar la reproducción desde el principio después de llegar al final. El valor predeterminado de esta propiedad es `false`.
- [`KeepScreenOn`](xref:Xamarin.Forms.MediaElement.KeepScreenOn), de tipo `bool`, determina si la pantalla del dispositivo debe permanecer activada durante la reproducción multimedia. El valor predeterminado de esta propiedad es `false`.
- [`Position`](xref:Xamarin.Forms.MediaElement.Position), de tipo `TimeSpan`, describe el progreso actual a través del tiempo de reproducción del elemento multimedia. El valor predeterminado de esta propiedad es `TimeSpan.Zero`.
- [`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls), de tipo `bool`, determina si se muestran los controles de reproducción de plataformas. El valor predeterminado de esta propiedad es `false`.
- [`Source`](xref:Xamarin.Forms.MediaElement.Source), de tipo [`MediaSource`](xref:Xamarin.Forms.MediaSource), indica el origen del medio cargado en el control.
- [`VideoHeight`](xref:Xamarin.Forms.MediaElement.VideoHeight), de tipo `int`, indica el alto del control. Se trata de una propiedad de solo lectura.
- [`VideoWidth`](xref:Xamarin.Forms.MediaElement.VideoWidth), de tipo `int`, indica el ancho del control. Se trata de una propiedad de solo lectura.
- [`Volume`](xref:Xamarin.Forms.MediaElement.Volume), de tipo `double`, determina el volumen del medio, que se representa en una escala lineal entre 0 y 1. Esta propiedad usa un enlace `TwoWay` y su valor predeterminado es 1.

Estas propiedades, con la excepción de la propiedad `CanSeek`, están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que pueden ser destinos de enlaces de datos y con estilo.

La clase [`MediaElement`](xref:Xamarin.Forms.MediaElement) también define cuatro eventos:

- [`MediaOpened`](xref:Xamarin.Forms.MediaElement.MediaOpened) se desencadena cuando se ha validado y abierto el flujo multimedia.
- [`MediaEnded`](xref:Xamarin.Forms.MediaElement.MediaEnded) se activa cuando la `MediaElement` termina de reproducir su medio.
- [`MediaFailed`](xref:Xamarin.Forms.MediaElement.MediaFailed) se desencadena cuando hay un error asociado con el origen del medio.
- [`SeekCompleted`](xref:Xamarin.Forms.MediaElement.SeekCompleted) se desencadena cuando el punto de búsqueda de una operación de búsqueda solicitada está listo para la reproducción.

Además, [`MediaElement`](xref:Xamarin.Forms.MediaElement) incluye [`Play`](xref:Xamarin.Forms.MediaElement.Play), [`Pause`](xref:Xamarin.Forms.MediaElement.Pause)y [`Stop`](xref:Xamarin.Forms.MediaElement.Stop) métodos.

Para obtener información sobre los formatos multimedia compatibles en Android, consulte [formatos de medios admitidos](https://developer.android.com/guide/topics/media/media-formats) en Developer.Android.com. Para obtener información sobre los formatos multimedia compatibles en el Plataforma universal de Windows (UWP), consulte [Códecs compatibles](/windows/uwp/audio-video-camera/supported-codecs).

## <a name="play-remote-media"></a>Reproducir medios remotos

Un [`MediaElement`](xref:Xamarin.Forms.MediaElement) puede reproducir archivos multimedia remotos mediante los esquemas de URI http y https. Esto se logra estableciendo la propiedad [`Source`](xref:Xamarin.Forms.MediaElement.Source) en el URI del archivo multimedia:

```xaml
<MediaElement Source="https://sec.ch9.ms/ch9/5d93/a1eab4bf-3288-4faf-81c4-294402a85d93/XamarinShow_mid.mp4"
              ShowsPlaybackControls="True" />
```

De forma predeterminada, los medios definidos por la propiedad [`Source`](xref:Xamarin.Forms.MediaElement.Source) se reproducen inmediatamente después de que se abra el elemento multimedia. Para suprimir la reproducción de medios automática, establezca la propiedad [`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay) en `false`.

Los controles de reproducción multimedia están deshabilitados de forma predeterminada y se habilitan estableciendo la propiedad [`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls) en `true`. a continuación, [`MediaElement`](xref:Xamarin.Forms.MediaElement) usará los controles de reproducción de la plataforma.

## <a name="play-local-media"></a>Reproducir medios locales

Los medios locales se pueden reproducir desde los orígenes siguientes:

- Un recurso incrustado en la aplicación de la plataforma mediante el esquema de URI `ms-appx:///`.
- Archivos que proceden de las carpetas de datos locales y temporales de la aplicación, mediante el esquema de URI `ms-appdata:///`.
- Biblioteca del dispositivo.

Para obtener más información sobre estos esquemas de URI, vea [esquemas de URI](/windows/uwp/app-resources/uri-schemes).

### <a name="play-media-embedded-in-the-app-package"></a>Reproducir medios incrustados en el paquete de la aplicación

Un [`MediaElement`](xref:Xamarin.Forms.MediaElement) puede reproducir archivos multimedia que están incrustados en el paquete de la aplicación mediante el esquema de URI de `ms-appx:///`. Los archivos multimedia se incrustan en el paquete de la aplicación colocándolos en el proyecto de plataforma.

Almacenar un archivo multimedia en el proyecto de plataforma es diferente para cada plataforma:

- En iOS, los archivos multimedia deben almacenarse en la carpeta **recursos** o en una subcarpeta de la carpeta **recursos** . El archivo multimedia debe tener un `Build Action` de `BundleResource`.
- En Android, los archivos multimedia deben almacenarse en una subcarpeta de **recursos** denominada **sin formato**. La carpeta **raw** no puede contener subcarpetas. El archivo multimedia debe tener un `Build Action` de `AndroidResource`.
- En UWP, los archivos multimedia se pueden almacenar en cualquier carpeta del proyecto. El archivo multimedia debe tener un `BuildAction` de `Content`.

Los archivos multimedia que cumplen estos criterios se pueden reproducir con el esquema de URI `ms-appx:///`:

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

A continuación, se puede usar una instancia de la `VideoSourceConverter` para aplicar el esquema de URI de `ms-appx:///` a un archivo multimedia incrustado:

```xaml
<MediaElement Source="{Binding MediaSource, Converter={StaticResource VideoSourceConverter}}"
              ShowsPlaybackControls="True" />
```

Para obtener más información sobre el esquema de URI de MS-appx, consulte [MS-appx y MS-appx-web](/windows/uwp/app-resources/uri-schemes#ms-appx-and-ms-appx-web).

### <a name="play-media-from-the-apps-local-and-temporary-folders"></a>Reproducir medios desde las carpetas locales y temporales de la aplicación

Un [`MediaElement`](xref:Xamarin.Forms.MediaElement) puede reproducir archivos multimedia que se copian en las carpetas de datos locales o temporales de la aplicación, mediante el esquema de URI de `ms-appdata:///`.

En el ejemplo siguiente se muestra la propiedad [`Source`](xref:Xamarin.Forms.MediaElement.Source) establecida en un archivo multimedia almacenado en la carpeta de datos locales de la aplicación:

```xaml
<MediaElement Source="ms-appdata:///local/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

En el ejemplo siguiente se muestra la propiedad [`Source`](xref:Xamarin.Forms.MediaElement.Source) a un archivo multimedia almacenado en la carpeta de datos temporales de la aplicación:

```xaml
<MediaElement Source="ms-appdata:///temp/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

> [!IMPORTANT]
> Además de reproducir archivos multimedia que se almacenan en las carpetas de datos locales o temporales de la aplicación, UWP también puede reproducir archivos multimedia que se encuentran en la carpeta móvil de la aplicación. Esto puede lograrse al prefijar el archivo multimedia con `ms-appdata:///roaming/`.

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

A continuación, se puede usar una instancia de la `VideoSourceConverter` para aplicar el esquema de URI de `ms-appdata:///` a un archivo multimedia en la carpeta de datos local o temporal de la aplicación:

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
> En el ejemplo de código anterior se usa la clase `FileSystem` incluida en Xamarin. Essentials. Para obtener más información, consulte [Xamarin. Essentials: aplicaciones auxiliares del sistema de archivos](~/essentials/file-system-helpers.md?context=xamarin%2Fxamarin-forms&tabs=android).

### <a name="play-media-from-the-device-library"></a>Reproducir medios desde la biblioteca de dispositivos

La mayoría de los dispositivos móviles y equipos de escritorio modernos tienen la capacidad de grabar vídeos y audio con la cámara y el micrófono del dispositivo. El medio que se crea se almacena a continuación como archivos en el dispositivo. Estos archivos se pueden recuperar de la biblioteca y reproducirlos en el [`MediaElement`](xref:Xamarin.Forms.MediaElement).

Cada una de las plataformas incluye una instalación que permite al usuario seleccionar archivos multimedia de la biblioteca del dispositivo. En Xamarin. Forms, los proyectos de plataforma pueden invocar esta funcionalidad y la clase [`DependencyService`](xref:Xamarin.Forms.DependencyService) puede llamarla.

El servicio de dependencia de selección de vídeo usado en la aplicación de ejemplo es muy similar a uno definido en la [selección de una foto de la biblioteca de imágenes](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md), con la excepción de que el selector devuelve un nombre de archivo en lugar de un objeto `Stream`. El proyecto de código compartido define una interfaz denominada `IVideoPicker`, que define un método único denominado `GetVideoFileAsync`. Cada plataforma implementa esta interfaz en una `VideoPicker` clase.

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

El servicio de dependencia de selección de vídeo se invoca llamando al método `DependencyService.Get` para obtener la implementación de una interfaz de `IVideoPicker` en el proyecto de plataforma. A continuación, se llama al método `GetVideoFileAsync` en esa instancia, y el nombre de archivo devuelto se usa para crear un objeto [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) y establecerlo en la propiedad [`Source`](xref:Xamarin.Forms.MediaElement.Source) del [`MediaElement`](xref:Xamarin.Forms.MediaElement).

## <a name="change-video-aspect-ratio"></a>Cambiar la relación de aspecto de vídeo

La propiedad [`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect) determina cómo se escalará el medio de vídeo para ajustarse al área de presentación. De forma predeterminada, esta propiedad se establece en el miembro de enumeración `AspectFit`, pero se puede establecer en cualquiera de los miembros de enumeración [`Aspect`](xref:Xamarin.Forms.Aspect) :

- `AspectFit` indica que el vídeo se mostrará en la pantalla, si es necesario, para que quepa en el área de visualización, a la vez que se conserva la relación de aspecto.
- `AspectFill` indica que el vídeo se recortará para que rellene el área de visualización, a la vez que conserva la relación de aspecto.
- `Fill` indica que el vídeo se ajustará para rellenar el área de visualización.

## <a name="poll-for-position-data"></a>Sondear datos de posición

La notificación de cambio de propiedad para la propiedad [`Position`](xref:Xamarin.Forms.MediaElement.Position) enlazable solo se desencadena en momentos clave como la reproducción inicial y final, y se produce una pausa. Por lo tanto, el enlace de datos a la propiedad `Position` no producirá datos de posición precisos. En su lugar, debe configurar un temporizador y sondear la propiedad.

Un buen lugar para hacerlo es en el `OnAppearing` invalidación de la página que requiere los datos de la posición a medida que se reproduce el medio:

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

En este ejemplo, la invalidación de `OnAppearing` inicia un temporizador que actualiza `positionLabel` con el valor de `Position` cada segundo. La devolución de llamada del temporizador se invoca cada segundo, hasta que la devolución de llamada devuelve `false`. Cuando se produce la navegación por la página, se ejecuta la invalidación de `OnDisappearing`, que detiene la devolución de llamada del temporizador que se invoca.

## <a name="understand-mediasource-types"></a>Descripción de los tipos MediaSource

Un [`MediaElement`](xref:Xamarin.Forms.MediaElement) puede reproducir archivos multimedia estableciendo su propiedad [`Source`](xref:Xamarin.Forms.MediaElement.Source) en un archivo multimedia local o remoto. La propiedad `Source` es de tipo [`MediaSource`](xref:Xamarin.Forms.MediaSource)y esta clase define dos métodos estáticos:

- [`FromFile`](xref:Xamarin.Forms.MediaSource.FromFile*), devuelve una instancia de [`MediaSource`](xref:Xamarin.Forms.MediaSource) de un argumento de `string`.
- [`FromUri`](xref:Xamarin.Forms.MediaSource.FromUri*), devuelve una instancia de [`MediaSource`](xref:Xamarin.Forms.MediaSource) de un argumento de `Uri`.

Además, la clase [`MediaSource`](xref:Xamarin.Forms.MediaSource) también tiene operadores implícitos que devuelven `MediaSource` instancias de `string` y `Uri` argumentos.

> [!NOTE]
> Cuando se establece la propiedad [`Source`](xref:Xamarin.Forms.MediaElement.Source) en XAML, se invoca un convertidor de tipos para devolver una instancia de [`MediaSource`](xref:Xamarin.Forms.MediaSource) de un `string` o `Uri`.

La clase [`MediaSource`](xref:Xamarin.Forms.MediaSource) también tiene dos clases derivadas:

- [`UriMediaSource`](xref:Xamarin.Forms.UriMediaSource), que se usa para especificar un archivo multimedia remoto desde un URI. Esta clase tiene una propiedad [`Uri`](xref:Xamarin.Forms.UriMediaSource.Uri) que se puede establecer en un `Uri`.
- [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource), que se usa para especificar un archivo multimedia local desde un `string`. Esta clase tiene una propiedad [`File`](xref:Xamarin.Forms.FileMediaSource.File) que se puede establecer en un `string`. Además, esta clase tiene operadores implícitos para convertir un `string` en un objeto `FileMediaSource` y un objeto `FileMediaSource` en un `string`.

> [!NOTE]
> Cuando se crea un objeto de [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) en XAML, se invoca un convertidor de tipos para devolver una instancia de [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) de un `string`.

## <a name="determine-mediaelement-status"></a>Determinar el estado de MediaElement

La clase [`MediaElement`](xref:Xamarin.Forms.MediaElement) define una propiedad enlazable de solo lectura denominada [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState), de tipo [`MediaElementState`](xref:Xamarin.Forms.MediaElementState). Esta propiedad indica el estado actual del control, como si el contenido multimedia se reproduce o está en pausa, o si todavía no está listo para reproducir el medio.

La enumeración [`MediaElementState`](xref:Xamarin.Forms.MediaElementState) define los siguientes miembros:

- `Closed` indica que el `MediaElement` no contiene ningún medio.
- `Opening` indica que el `MediaElement` está validando e intentando cargar el origen especificado.
- `Buffering` indica que el `MediaElement` está cargando los medios para la reproducción. Su propiedad [`Position`](xref:Xamarin.Forms.MediaElement.Position) no avanza durante este estado. Si el `MediaElement` estaba reproduciendo vídeo, continúa mostrando el último fotograma mostrado.
- `Playing` indica que el `MediaElement` está reproduciendo el origen del medio.
- `Paused` indica que el `MediaElement` no hace avanzar su propiedad [`Position`](xref:Xamarin.Forms.MediaElement.Position) . Si el `MediaElement` estaba reproduciendo vídeo, continúa mostrando el fotograma actual.
- `Stopped` indica que el `MediaElement` contiene medios pero no se está reproduciendo o en pausa. Su propiedad [`Position`](xref:Xamarin.Forms.MediaElement.Position) es 0 y no avanza. Si el medio cargado es un vídeo, el `MediaElement` muestra el primer fotograma.

Por lo general, no es necesario examinar la propiedad [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState) cuando se usan los controles de transporte de [`MediaElement`](xref:Xamarin.Forms.MediaElement) . Sin embargo, esta propiedad se convierte en importante al implementar sus propios controles de transporte.

## <a name="implement-custom-transport-controls"></a>Implementar controles de transporte personalizados

Los controles de transporte de un reproductor multimedia incluyen los botones que realizan las funciones de **reproducción**, **pausa**y **detención**. Estos botones suelen identificarse con iconos conocidos en lugar de texto, y las funciones **Reproducir** y **Pausa** suelen combinarse en un mismo botón.

De forma predeterminada, los controles de reproducción [`MediaElement`](xref:Xamarin.Forms.MediaElement) están deshabilitados. Esto le permite controlar el `MediaElement` mediante programación o proporcionando sus propios controles de transporte. Para ello, `MediaElement` incluye [`Play`](xref:Xamarin.Forms.MediaElement.Play), [`Pause`](xref:Xamarin.Forms.MediaElement.Pause)y métodos de [`Stop`](xref:Xamarin.Forms.MediaElement.Stop) .

En el ejemplo de XAML siguiente se muestra una página que contiene un [`MediaElement`](xref:Xamarin.Forms.MediaElement) y controles de transporte personalizados:

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

En este ejemplo, los controles de transporte personalizados se definen como objetos [`Button`](xref:Xamarin.Forms.Button) . Sin embargo, hay solo dos objetos `Button`, con el primer `Button` que representa la **reproducción** y la **pausa**, y el segundo `Button` que representa la **detención**. [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) objetos se utilizan para habilitar y deshabilitar los botones y para cambiar el primer botón entre **reproducir** y **pausar**. Para obtener más información sobre los desencadenadores de datos, vea [desencadenadores de Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

El archivo de código subyacente tiene los controladores para los eventos de [`Clicked`](xref:Xamarin.Forms.Button.Clicked) :

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

La implementación de una barra de posición personalizada requiere conocer la duración del medio y la posición de reproducción actual. Estos datos están disponibles en las propiedades [`Duration`](xref:Xamarin.Forms.MediaElement.Duration) y [`Position`](xref:Xamarin.Forms.MediaElement.Position) .

> [!IMPORTANT]
> Se debe sondear el [`Position`](xref:Xamarin.Forms.MediaElement.Position) para obtener datos de posición precisos. Para obtener más información, consulte [sondear para datos de posición](#poll-for-position-data).

Una barra de posición personalizada se puede implementar mediante un [`Slider`](xref:Xamarin.Forms.Slider), como se muestra en el ejemplo siguiente:

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

La clase `PositionSlider` define sus propias propiedades `Duration` y `Position` enlazables y una propiedad enlazable `TimeToEnd`. Las tres propiedades son del tipo `TimeSpan`. El controlador de cambio de propiedad de la propiedad `Duration` establece la propiedad `Maximum` de la [`Slider`](xref:Xamarin.Forms.Slider) en la propiedad `TotalSeconds` del valor `TimeSpan`. La propiedad `TimeToEnd` se calcula en función de los cambios en las propiedades `Duration` y `Position`, y comienza en la duración del medio y disminuye hasta cero a medida que continúa la reproducción.

El `PositionSlider` se actualiza a partir del [`Slider`](xref:Xamarin.Forms.Slider) subyacente cuando se mueve el `Slider` para indicar que el medio debe ser avanzado o inverso a una nueva posición. Esto se detecta en el controlador de `PropertyChanged` en el constructor `PositionSlider`. El controlador comprueba si hay algún cambio en la propiedad `Value` y, si es diferente de la propiedad `Position`, la propiedad `Position` se establece desde la propiedad `Value`. Para obtener más información sobre el uso de un [`Slider`](xref:Xamarin.Forms.Slider) vea, [control deslizante de Xamarin. Forms](~/xamarin-forms/user-interface/slider.md)

> [!NOTE]
> En Android, la [`Slider`](xref:Xamarin.Forms.Slider) solo tiene 1000 pasos discretos, independientemente de la configuración de `Minimum` y `Maximum`. Si la longitud del medio es superior a 1000 segundos, dos valores de `Position` diferentes se corresponden con la misma `Value` de la `Slider`. Este es el motivo por el que el código anterior comprueba que la nueva posición y la posición existente son mayores que una centésima de la duración total.

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

En este ejemplo, la propiedad `Duration` del `PositionSlider` está enlazada a datos a la propiedad [`Duration`](xref:Xamarin.Forms.MediaElement.Duration) del [`MediaElement`](xref:Xamarin.Forms.MediaElement). Cuando cambia la propiedad [`Value`](xref:Xamarin.Forms.Slider.Value) del [`Slider`](xref:Xamarin.Forms.Slider) , se desencadena el evento `ValueChanged` y se ejecuta el controlador de `OnPositionSliderValueChanged`. Este controlador establece la propiedad [`MediaElement.Position`](xref:Xamarin.Forms.MediaElement.Position) en el valor de la propiedad `PositionSlider.Position`. Por lo tanto, si se arrastra el `Slider` se produce un cambio en la posición de reproducción multimedia:

[![Captura de pantalla de un MediaElement con una barra de posición personalizada, en iOS y Android](mediaelement-images/custom-position-bar.png "MediaElement con una barra de posición personalizada")](mediaelement-images/custom-position-bar-large.png#lightbox "MediaElement con una barra de posición personalizada")

Además, se utiliza un objeto [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) para deshabilitar el `PositionSlider` cuando el contenido multimedia se almacena en búfer. Para obtener más información sobre los desencadenadores de datos, vea [desencadenadores de Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="implement-a-custom-volume-control"></a>Implementar un control de volumen personalizado

Los controles de reproducción multimedia implementados por cada plataforma incluyen una barra de volumen. Esta barra es similar a un control deslizante y muestra el volumen del medio. Además, puede manipular la barra de volumen para aumentar o disminuir el volumen.

Una barra de volumen personalizada se puede implementar mediante un [`Slider`](xref:Xamarin.Forms.Slider), como se muestra en el ejemplo siguiente:

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

En este ejemplo, los datos de la [`Slider`](xref:Xamarin.Forms.Slider) enlazan su propiedad `Value` a la propiedad [`Volume`](xref:Xamarin.Forms.MediaElement.Volume) de la [`MediaElement`](xref:Xamarin.Forms.MediaElement). Esto es posible porque la propiedad `Volume` usa un enlace de `TwoWay`. Por lo tanto, el cambio de la propiedad `Value` provocará el cambio de la propiedad `Volume`.

> [!NOTE]
> La propiedad [`Volume`](xref:Xamarin.Forms.MediaElement.Volume) tiene una devolución de llamada vlidation que garantiza que su valor es mayor o igual que 0,0 y menor o igual que 1,0.

Para obtener más información sobre el uso de un [`Slider`](xref:Xamarin.Forms.Slider) vea, [control deslizante de Xamarin. Forms](~/xamarin-forms/user-interface/slider.md)

## <a name="related-links"></a>Vínculos relacionados

- [MediaElementDemos (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/MediaElementDemos)
- [Esquemas de URI](/windows/uwp/app-resources/uri-schemes)
- [Desencadenadores de Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Control deslizante de Xamarin. Forms](~/xamarin-forms/user-interface/slider.md)
- [Android: formatos de medios admitidos](https://developer.android.com/guide/topics/media/media-formats)
- [UWP: códecs admitidos](/windows/uwp/audio-video-camera/supported-codecs)
