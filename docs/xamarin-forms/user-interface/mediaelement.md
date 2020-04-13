---
title: Xamarin.Forms MediaElement
description: En este artículo se explica cómo usar MediaElement para reproducir vídeo y audio en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: e65f1e56-a80d-46c7-9ff4-7ae6650a3165
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/18/2020
ms.openlocfilehash: 6f6c51c428de569ceb09ed6a26cfc36881c86dc5
ms.sourcegitcommit: b93754b220fca3d6e3d131341e3cfbe233d10f84
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2020
ms.locfileid: "80628339"
---
# <a name="xamarinforms-mediaelement"></a>Xamarin.Forms MediaElement

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar](~/media/shared/download.png) ejemplo Descargue el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos/)

[`MediaElement`](xref:Xamarin.Forms.MediaElement)es una vista para reproducir vídeo y audio. Los medios compatibles con la plataforma subyacente se pueden reproducir desde las siguientes fuentes:

- La web, mediante un URI (HTTP o HTTPS).
- Un recurso incrustado en la aplicación `ms-appx:///` de plataforma, mediante el esquema URI.
- Archivos que proceden de las carpetas de `ms-appdata:///` datos locales y temporales de la aplicación, mediante el esquema URI.
- La biblioteca del dispositivo.

[`MediaElement`](xref:Xamarin.Forms.MediaElement)pueden utilizar los controles de reproducción de la plataforma, que se denominan controles de transporte. Sin embargo, están deshabilitados de forma predeterminada y se pueden reemplazar con sus propios controles de transporte. Las siguientes capturas `MediaElement` de pantalla muestran la reproducción de un vídeo con los controles de transporte de la plataforma:

[![Captura de pantalla de un MediaElement que reproduce un vídeo, en iOS y Android](mediaelement-images/playback-controls.png "MediaElement reproduciendo un vídeo")](mediaelement-images/playback-controls-large.png#lightbox "MediaElement reproduciendo un vídeo")

[`MediaElement`](xref:Xamarin.Forms.MediaElement)está disponible en Xamarin.Forms 4.5. Sin embargo, actualmente es experimental y solo se puede utilizar agregando la siguiente línea de código a su archivo *App.xaml.cs:*

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

> [!NOTE]
> [`MediaElement`](xref:Xamarin.Forms.MediaElement)está disponible en iOS, Android, la Plataforma universal de Windows (UWP), macOS, Windows Presentation Foundation y Tizen.

[`MediaElement`](xref:Xamarin.Forms.MediaElement)define las siguientes propiedades:

- [`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect), de [`Aspect`](xref:Xamarin.Forms.Aspect)tipo , determina cómo se escalará el soporte para ajustarse al área de visualización. El valor predeterminado de esta propiedad es `AspectFit`.
- [`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay), de `bool`tipo , indica si la [`Source`](xref:Xamarin.Forms.MediaElement.Source) reproducción multimedia comenzará automáticamente cuando se establezca la propiedad. El valor predeterminado de esta propiedad es `true`.
- [`BufferingProgress`](xref:Xamarin.Forms.MediaElement.BufferingProgress), de `double`tipo , indica el progreso actual del almacenamiento en búfer. El valor predeterminado de esta propiedad es 0.0.
- [`CanSeek`](xref:Xamarin.Forms.MediaElement.CanSeek), de `bool`tipo , indica si se pueden cambiar la [`Position`](xref:Xamarin.Forms.MediaElement.Position) posición de los medios estableciendo el valor de la propiedad. Se trata de una propiedad de solo lectura.
- [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState), de [`MediaElementState`](xref:Xamarin.Forms.MediaElementState)tipo , indica el estado actual del control. Se trata de una propiedad de `MediaElementState.Closed`solo lectura, cuyo valor predeterminado es .
- [`Duration`](xref:Xamarin.Forms.MediaElement.Duration), de `TimeSpan?`tipo , indica la duración de los medios abiertos actualmente. Se trata de una propiedad de `null`solo lectura cuyo valor predeterminado es .
- [`IsLooping`](xref:Xamarin.Forms.MediaElement.IsLooping), de `bool`tipo , describe si la fuente de medios cargada actualmente debe reanudar la reproducción desde el principio después de llegar a su final. El valor predeterminado de esta propiedad es `false`.
- [`KeepScreenOn`](xref:Xamarin.Forms.MediaElement.KeepScreenOn), de `bool`tipo , determina si la pantalla del dispositivo debe permanecer encendida durante la reproducción multimedia. El valor predeterminado de esta propiedad es `false`.
- [`Position`](xref:Xamarin.Forms.MediaElement.Position), de `TimeSpan`tipo , describe el progreso actual a través del tiempo de reproducción del medio. El valor predeterminado de esta propiedad es `TimeSpan.Zero`.
- [`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls), de `bool`tipo , determina si se muestran los controles de reproducción de las plataformas. El valor predeterminado de esta propiedad es `false`.
- [`Source`](xref:Xamarin.Forms.MediaElement.Source), de [`MediaSource`](xref:Xamarin.Forms.MediaSource)tipo , indica el origen del medio cargado en el control.
- [`VideoHeight`](xref:Xamarin.Forms.MediaElement.VideoHeight), de `int`tipo , indica la altura del control. Se trata de una propiedad de solo lectura.
- [`VideoWidth`](xref:Xamarin.Forms.MediaElement.VideoWidth), de `int`tipo , indica el ancho del control. Se trata de una propiedad de solo lectura.
- [`Volume`](xref:Xamarin.Forms.MediaElement.Volume), de `double`tipo , determina el volumen del medio, que se representa en una escala lineal entre 0 y 1. Esta propiedad `TwoWay` utiliza un enlace y su valor predeterminado es 1.

Estas propiedades, con la `CanSeek` excepción de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) la propiedad, están respaldadas por objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

La [`MediaElement`](xref:Xamarin.Forms.MediaElement) clase también define cuatro eventos:

- [`MediaOpened`](xref:Xamarin.Forms.MediaElement.MediaOpened)se activa cuando la secuencia de medios se ha validado y abierto.
- [`MediaEnded`](xref:Xamarin.Forms.MediaElement.MediaEnded)se dispara `MediaElement` cuando termina de reproducir sus medios.
- [`MediaFailed`](xref:Xamarin.Forms.MediaElement.MediaFailed)se desencadena cuando hay un error asociado con el origen de medios.
- [`SeekCompleted`](xref:Xamarin.Forms.MediaElement.SeekCompleted)se activa cuando el punto de búsqueda de una operación de búsqueda solicitada está listo para su reproducción.

[`MediaElement`](xref:Xamarin.Forms.MediaElement) Además, [`Play`](xref:Xamarin.Forms.MediaElement.Play) [`Pause`](xref:Xamarin.Forms.MediaElement.Pause)incluye [`Stop`](xref:Xamarin.Forms.MediaElement.Stop) , , y métodos.

Para obtener información sobre los formatos multimedia compatibles en Android, consulte [Formatos de medios compatibles](https://developer.android.com/guide/topics/media/media-formats) en developer.android.com. Para obtener información acerca de los formatos multimedia admitidos en la Plataforma universal de Windows (UWP), vea [Códecs admitidos](/windows/uwp/audio-video-camera/supported-codecs).

## <a name="play-remote-media"></a>Reproducir medios remotos

A [`MediaElement`](xref:Xamarin.Forms.MediaElement) puede reproducir archivos multimedia remotos mediante los esquemas URI HTTP y HTTPS. Esto se logra [`Source`](xref:Xamarin.Forms.MediaElement.Source) estableciendo la propiedad en el URI del archivo multimedia:

```xaml
<MediaElement Source="https://sec.ch9.ms/ch9/5d93/a1eab4bf-3288-4faf-81c4-294402a85d93/XamarinShow_mid.mp4"
              ShowsPlaybackControls="True" />
```

De forma predeterminada, el medio [`Source`](xref:Xamarin.Forms.MediaElement.Source) definido por la propiedad se reproduce inmediatamente después de abrir el medio. Para suprimir la reproducción [`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay) automática `false`de medios, establezca la propiedad en .

Los controles de reproducción multimedia están deshabilitados [`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls) de `true`forma predeterminada y se habilitan estableciendo la propiedad en . [`MediaElement`](xref:Xamarin.Forms.MediaElement)utilizará los controles de reproducción de la plataforma.

## <a name="play-local-media"></a>Reproducir medios locales

Los medios locales se pueden reproducir desde las siguientes fuentes:

- Un recurso incrustado en la aplicación `ms-appx:///` de plataforma, mediante el esquema URI.
- Archivos que proceden de las carpetas de `ms-appdata:///` datos locales y temporales de la aplicación, mediante el esquema URI.
- La biblioteca del dispositivo.

Para obtener más información acerca de estos esquemas de URI, vea [esquemas de URI](/windows/uwp/app-resources/uri-schemes).

### <a name="play-media-embedded-in-the-app-package"></a>Reproducir medios incrustados en el paquete de la aplicación

A [`MediaElement`](xref:Xamarin.Forms.MediaElement) puede reproducir archivos multimedia incrustados en el `ms-appx:///` paquete de la aplicación mediante el esquema uri. Los archivos multimedia se incrustan en el paquete de la aplicación colocándolos en el proyecto de plataforma.

Almacenar un archivo multimedia en el proyecto de plataforma es diferente para cada plataforma:

- En iOS, los archivos multimedia deben almacenarse en la carpeta **Recursos** o en una subcarpeta de la carpeta **Recursos.** El archivo multimedia `Build Action` debe `BundleResource`tener un de .
- En Android, los archivos multimedia deben almacenarse en una subcarpeta de **Recursos** denominada **Raw**. La carpeta **raw** no puede contener subcarpetas. El archivo multimedia `Build Action` debe `AndroidResource`tener un de .
- En UWP, los archivos multimedia se pueden almacenar en cualquier carpeta del proyecto. El archivo multimedia `BuildAction` debe `Content`tener un de .

Los archivos multimedia que cumplen estos `ms-appx:///` criterios se pueden reproducir mediante el esquema URI:

```xaml
<MediaElement Source="ms-appx:///XamarinForms101UsingEmbeddedImages.mp4"
              ShowsPlaybackControls="True" />
```

Cuando se utiliza el enlace de datos, se puede utilizar un convertidor de valores para aplicar este esquema URI:

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

A continuación, `VideoSourceConverter` se puede utilizar `ms-appx:///` una instancia de la se puede utilizar para aplicar el esquema URI a un archivo multimedia incrustado:

```xaml
<MediaElement Source="{Binding MediaSource, Converter={StaticResource VideoSourceConverter}}"
              ShowsPlaybackControls="True" />
```

Para obtener más información sobre el esquema URI ms-appx, vea [ms-appx y ms-appx-web](/windows/uwp/app-resources/uri-schemes#ms-appx-and-ms-appx-web).

### <a name="play-media-from-the-apps-local-and-temporary-folders"></a>Reproducir contenido multimedia desde las carpetas locales y temporales de la aplicación

A [`MediaElement`](xref:Xamarin.Forms.MediaElement) puede reproducir archivos multimedia que se copian en las carpetas de datos locales o temporales de la aplicación, mediante el `ms-appdata:///` esquema de URI.

En el ejemplo [`Source`](xref:Xamarin.Forms.MediaElement.Source) siguiente se muestra la propiedad establecida en un archivo multimedia que se almacena en la carpeta de datos local de la aplicación:

```xaml
<MediaElement Source="ms-appdata:///local/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

En el ejemplo [`Source`](xref:Xamarin.Forms.MediaElement.Source) siguiente se muestra la propiedad de un archivo multimedia que se almacena en la carpeta de datos temporales de la aplicación:

```xaml
<MediaElement Source="ms-appdata:///temp/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

> [!IMPORTANT]
> Además de reproducir archivos multimedia almacenados en las carpetas de datos locales o temporales de la aplicación, UWP también puede reproducir archivos multimedia que se encuentran en la carpeta móvil de la aplicación. Esto se puede lograr prefijando `ms-appdata:///roaming/`el archivo multimedia con .

Cuando se utiliza el enlace de datos, se puede utilizar un convertidor de valores para aplicar este esquema URI:

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

A continuación, `VideoSourceConverter` se puede usar `ms-appdata:///` una instancia de la que se puede usar para aplicar el esquema URI a un archivo multimedia en la carpeta de datos local o temporal de la aplicación:

```xaml
<MediaElement Source="{Binding MediaSource, Converter={StaticResource VideoSourceConverter}}"
              ShowsPlaybackControls="True" />
```

Para obtener más información sobre el esquema URI ms-appdata, vea [ms-appdata](/windows/uwp/app-resources/uri-schemes#ms-appdata).

#### <a name="copying-a-media-file-to-the-apps-local-or-temporary-data-folder"></a>Copiar un archivo multimedia en la carpeta de datos local o temporal de la aplicación

La reproducción de un archivo multimedia almacenado en la carpeta de datos local o temporal de la aplicación requiere que la aplicación copie el archivo multimedia allí. Esto se puede lograr, por ejemplo, copiando un archivo multimedia del paquete de la aplicación:

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
> En el ejemplo `FileSystem` de código anterior se usa la clase incluida en Xamarin.Essentials. Para obtener más información, vea [Xamarin.Essentials: aplicaciones auxiliares](~/essentials/file-system-helpers.md?context=xamarin%2Fxamarin-forms&tabs=android)del sistema de archivos .

### <a name="play-media-from-the-device-library"></a>Reproducir contenido multimedia desde la biblioteca de dispositivos

La mayoría de los dispositivos móviles modernos y computadoras de escritorio tienen la capacidad de grabar videos y audio usando la cámara y el micrófono del dispositivo. A continuación, los medios creados se almacenan como archivos en el dispositivo. Estos archivos se pueden recuperar de la [`MediaElement`](xref:Xamarin.Forms.MediaElement)biblioteca y reproducir por el archivo .

Cada una de las plataformas incluye una función que permite al usuario seleccionar medios de la biblioteca del dispositivo. En Xamarin.Forms, los proyectos de plataforma pueden invocar esta [`DependencyService`](xref:Xamarin.Forms.DependencyService) funcionalidad y la clase puede llamarla.

El servicio de dependencia de selección de vídeo utilizado en la aplicación de ejemplo es muy similar al definido en [Picking a Photo from the Picture Library](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md), excepto que el selector devuelve un nombre de archivo en lugar de un `Stream` objeto. El proyecto de código `IVideoPicker`compartido define una interfaz denominada , que define un único método denominado `GetVideoFileAsync`. A continuación, cada plataforma `VideoPicker` implementa esta interfaz en una clase.

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

El servicio de dependencia de `DependencyService.Get` selección de vídeo se `IVideoPicker` invoca llamando al método para obtener la implementación de una interfaz en el proyecto de plataforma. A `GetVideoFileAsync` continuación, se llama al método en esa [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) instancia y se utiliza [`Source`](xref:Xamarin.Forms.MediaElement.Source) el [`MediaElement`](xref:Xamarin.Forms.MediaElement)nombre de archivo devuelto para crear un objeto y establecerlo en la propiedad de la extensión .

## <a name="change-video-aspect-ratio"></a>Cambiar la relación de aspecto del vídeo

La [`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect) propiedad determina cómo se escalarán los medios de vídeo para ajustarse al área de visualización. De forma predeterminada, esta `AspectFit` propiedad se establece en el miembro [`Aspect`](xref:Xamarin.Forms.Aspect) de enumeración, pero se puede establecer en cualquiera de los miembros de enumeración:

- `AspectFit`indica que el vídeo será en caja de letras, si es necesario, para encajar en el área de visualización, conservando al mismo tiempo la relación de aspecto.
- `AspectFill`indica que el vídeo se recortará para que llene el área de visualización, conservando al mismo tiempo la relación de aspecto.
- `Fill`indica que el vídeo se estirará para llenar el área de visualización.

## <a name="poll-for-position-data"></a>Sondeo de datos de posición

La notificación de [`Position`](xref:Xamarin.Forms.MediaElement.Position) cambio de propiedad para la propiedad enlazable solo se activa en momentos clave como el inicio y el final de la reproducción, y la pausa que se produce. Por lo tanto, `Position` el enlace de datos a la propiedad no producirá datos de posición precisos. En su lugar, debe configurar un temporizador y sondear la propiedad.

Un buen lugar para hacer `OnAppearing` esto es en la anulación de la página que requiere los datos de posición a medida que se reproducen los medios:

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

En este ejemplo, la `OnAppearing` invalidación inicia un temporizador que se actualiza `positionLabel` con el `Position` valor cada segundo. La devolución de llamada del temporizador `false`se invoca cada segundo, hasta que la devolución de llamada devuelve . Cuando se produce `OnDisappearing` la navegación de página, se ejecuta la invalidación, lo que detiene la devolución de llamada del temporizador que se invoca.

## <a name="understand-mediasource-types"></a>Comprender los tipos de MediaSource

A [`MediaElement`](xref:Xamarin.Forms.MediaElement) puede reproducir medios [`Source`](xref:Xamarin.Forms.MediaElement.Source) estableciendo su propiedad en un archivo multimedia remoto o local. La `Source` propiedad es [`MediaSource`](xref:Xamarin.Forms.MediaSource)de tipo , y esta clase define dos métodos estáticos:

- [`FromFile`](xref:Xamarin.Forms.MediaSource.FromFile*), devuelve [`MediaSource`](xref:Xamarin.Forms.MediaSource) una `string` instancia de un argumento.
- [`FromUri`](xref:Xamarin.Forms.MediaSource.FromUri*), devuelve [`MediaSource`](xref:Xamarin.Forms.MediaSource) una `Uri` instancia de un argumento.

Además, [`MediaSource`](xref:Xamarin.Forms.MediaSource) la clase también `MediaSource` tiene operadores implícitos que devuelven instancias de `string` y `Uri` argumentos.

> [!NOTE]
> Cuando [`Source`](xref:Xamarin.Forms.MediaElement.Source) la propiedad se establece en XAML, se [`MediaSource`](xref:Xamarin.Forms.MediaSource) invoca `string` un `Uri`convertidor de tipos para devolver una instancia de un archivo o .

La [`MediaSource`](xref:Xamarin.Forms.MediaSource) clase también tiene dos clases derivadas:

- [`UriMediaSource`](xref:Xamarin.Forms.UriMediaSource), que se utiliza para especificar un archivo multimedia remoto desde un URI. Esta clase [`Uri`](xref:Xamarin.Forms.UriMediaSource.Uri) tiene una propiedad que `Uri`se puede establecer en un archivo .
- [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource), que se utiliza para especificar `string`un archivo multimedia local a partir de un archivo . Esta clase [`File`](xref:Xamarin.Forms.FileMediaSource.File) tiene una propiedad que `string`se puede establecer en un archivo . Además, esta clase tiene `string` operadores `FileMediaSource` implícitos `FileMediaSource` para convertir `string`a en un objeto y un objeto en un archivo .

> [!NOTE]
> Cuando [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) se crea un objeto en XAML, se [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) invoca `string`un convertidor de tipos para devolver una instancia de un archivo .

## <a name="determine-mediaelement-status"></a>Determinar el estado de MediaElement

La [`MediaElement`](xref:Xamarin.Forms.MediaElement) clase define una propiedad enlazable de solo lectura denominada [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState), de tipo [`MediaElementState`](xref:Xamarin.Forms.MediaElementState). Esta propiedad indica el estado actual del control, como si el medio se está reproduciendo o en pausa, o si aún no está listo para reproducir el medio.

La [`MediaElementState`](xref:Xamarin.Forms.MediaElementState) enumeración define los siguientes miembros:

- `Closed`indica que `MediaElement` no contiene ningún medio.
- `Opening`indica que `MediaElement` el está validando e intentando cargar el origen especificado.
- `Buffering`indica que `MediaElement` está cargando el medio para su reproducción. Su [`Position`](xref:Xamarin.Forms.MediaElement.Position) propiedad no avanza durante este estado. Si `MediaElement` el vídeo estaba reproduciendo, continúa mostrando el último fotograma mostrado.
- `Playing`indica que `MediaElement` el está reproduciendo la fuente de medios.
- `Paused`indica que `MediaElement` el no [`Position`](xref:Xamarin.Forms.MediaElement.Position) adelante su propiedad. Si `MediaElement` el vídeo estaba reproduciendo, continúa mostrando el fotograma actual.
- `Stopped`indica que `MediaElement` el contenido de medios, pero no se está reproduciendo ni pausado. Su [`Position`](xref:Xamarin.Forms.MediaElement.Position) propiedad es 0 y no tiene antelación. Si el medio cargado `MediaElement` es vídeo, el muestra el primer fotograma.

Por lo general, no es [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState) necesario examinar [`MediaElement`](xref:Xamarin.Forms.MediaElement) la propiedad cuando se utilizan los controles de transporte. Sin embargo, esta propiedad se vuelve importante al implementar sus propios controles de transporte.

## <a name="implement-custom-transport-controls"></a>Implementar controles de transporte personalizados

Los controles de transporte de un reproductor multimedia incluyen los botones que realizan las funciones **Reproducir**, **Pausar**y **Detener**. Estos botones suelen identificarse con iconos conocidos en lugar de texto, y las funciones **Reproducir** y **Pausa** suelen combinarse en un mismo botón.

De forma [`MediaElement`](xref:Xamarin.Forms.MediaElement) predeterminada, los controles de reproducción están deshabilitados. Esto le permite `MediaElement` controlar la programación o proporcionando sus propios controles de transporte. En apoyo de `MediaElement` [`Play`](xref:Xamarin.Forms.MediaElement.Play)esto, incluye , [`Pause`](xref:Xamarin.Forms.MediaElement.Pause), y [`Stop`](xref:Xamarin.Forms.MediaElement.Stop) métodos.

En el ejemplo XAML siguiente [`MediaElement`](xref:Xamarin.Forms.MediaElement) se muestra una página que contiene un y controles de transporte personalizados:

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

En este ejemplo, los controles [`Button`](xref:Xamarin.Forms.Button) de transporte personalizados se definen como objetos. Sin embargo, `Button` solo hay dos `Button` objetos, el primero `Button` que representa **Reproducir** y **Pausa**, y el segundo representan **dootros Detener**. [`DataTrigger`](xref:Xamarin.Forms.DataTrigger)los objetos se utilizan para activar y desactivar los botones y para cambiar el primer botón entre **Reproducir** y **Pausar**. Para obtener más información acerca de los desencadenadores de datos, vea desencadenadores de [Xamarin.Forms](~/xamarin-forms/app-fundamentals/triggers.md).

El archivo de código subyacente tiene [`Clicked`](xref:Xamarin.Forms.Button.Clicked) los controladores para los eventos:

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

El botón **Reproducir** se puede pulsar, una vez que se activa, para iniciar la reproducción:

[![Captura de pantalla de un MediaElement con controles de transporte personalizados, en iOS y Android](mediaelement-images/custom-transport-playback.png "MediaElement reproduciendo un vídeo")](mediaelement-images/custom-transport-playback-large.png#lightbox "MediaElement reproduciendo un vídeo")

Al pulsar el botón **Pausa,** se produce una pausa de reproducción:

[![Captura de pantalla de un MediaElement con reproducción en pausa, en iOS y Android](mediaelement-images/custom-transport-paused.png "MediaElement con un vídeo en pausa")](mediaelement-images/custom-transport-paused-large.png#lightbox "MediaElement con un vídeo en pausa")

Al pulsar el botón **Detener** se detiene la reproducción y se devuelve la posición del archivo multimedia al principio.

## <a name="implement-a-custom-position-bar"></a>Implementar una barra de posición personalizada

Los controles de transporte que cada plataforma implementa incluyen una barra de posición. Esta barra se asemeja a un control deslizante y muestra la ubicación actual del medio dentro de su duración total. Además, puede manipular la barra de posición para avanzar o retroceder a una nueva posición en el vídeo.

La implementación de una barra de posición personalizada requiere conocer la duración del medio y la posición de reproducción actual. Estos datos están [`Duration`](xref:Xamarin.Forms.MediaElement.Duration) disponibles en las propiedades y. [`Position`](xref:Xamarin.Forms.MediaElement.Position)

> [!IMPORTANT]
> El [`Position`](xref:Xamarin.Forms.MediaElement.Position) debe ser sondeado para obtener datos de posición precisos. Para obtener más información, consulte [Sondeo de datos](#poll-for-position-data)de posición .

Una barra de posición personalizada [`Slider`](xref:Xamarin.Forms.Slider)se puede implementar mediante un , como se muestra en el ejemplo siguiente:

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

La `PositionSlider` clase define `Duration` `Position` sus propias propiedades `TimeToEnd` y enlazables, y una propiedad enlazable. Las tres propiedades `TimeSpan`son de tipo . El controlador de cambio `Duration` de `Maximum` propiedad para [`Slider`](xref:Xamarin.Forms.Slider) la `TotalSeconds` propiedad `TimeSpan` establece la propiedad de la a la propiedad del valor. La `TimeToEnd` propiedad se calcula en `Duration` función de los cambios en las `Position` propiedades y comienza a la duración del medio y disminuye a cero a medida que avanza la reproducción.

Se `PositionSlider` actualiza desde el [`Slider`](xref:Xamarin.Forms.Slider) subyacente `Slider` cuando se mueve para indicar que el medio debe ser avanzado o invertido a una nueva posición. Esto se detecta `PropertyChanged` en `PositionSlider` el controlador en el constructor. El controlador comprueba si hay algún cambio en la propiedad `Value` y, si es diferente de la propiedad `Position`, la propiedad `Position` se establece desde la propiedad `Value`. Para obtener más [`Slider`](xref:Xamarin.Forms.Slider) información acerca del uso de un ver, [Xamarin.Forms Slider](~/xamarin-forms/user-interface/slider.md)

> [!NOTE]
> En Android, [`Slider`](xref:Xamarin.Forms.Slider) el único tiene 1000 pasos discretos, independientemente de la `Minimum` configuración y. `Maximum` Si la longitud de los medios es mayor `Position` que 1000 `Value` segundos, dos valores diferentes corresponderían a los `Slider`mismos del archivo . Esta es la razón por la que el código anterior comprueba que la nueva posición y la posición existente son mayores de una centésima parte de la duración total.

En el ejemplo `PositionSlider` siguiente se muestra el consumo en una página:

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

En este ejemplo, `Duration` la `PositionSlider` propiedad de is [`Duration`](xref:Xamarin.Forms.MediaElement.Duration) data-bound a la propiedad de la [`MediaElement`](xref:Xamarin.Forms.MediaElement). Cuando [`Value`](xref:Xamarin.Forms.Slider.Value) la propiedad [`Slider`](xref:Xamarin.Forms.Slider) de `ValueChanged` los cambios, `OnPositionSliderValueChanged` el evento se desencadena y se ejecuta el controlador. Este controlador [`MediaElement.Position`](xref:Xamarin.Forms.MediaElement.Position) establece la propiedad `PositionSlider.Position` en el valor de la propiedad. Por lo tanto, arrastrando los `Slider` resultados en el cambio de posición de reproducción de medios:

[![Captura de pantalla de un MediaElement con una barra de posición personalizada, en iOS y Android](mediaelement-images/custom-position-bar.png "MediaElement con una barra de posición personalizada")](mediaelement-images/custom-position-bar-large.png#lightbox "MediaElement con una barra de posición personalizada")

Además, [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) se utiliza un `PositionSlider` objeto para deshabilitar el momento en que el medio está almacenando en búfer. Para obtener más información acerca de los desencadenadores de datos, vea desencadenadores de [Xamarin.Forms](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="implement-a-custom-volume-control"></a>Implementar un control de volumen personalizado

Los controles de reproducción multimedia implementados por cada plataforma incluyen una barra de volumen. Esta barra se asemeja a un control deslizante y muestra el volumen del medio. Además, puede manipular la barra de volumen para aumentar o disminuir el volumen.

Una barra de volumen personalizada [`Slider`](xref:Xamarin.Forms.Slider)se puede implementar mediante un , como se muestra en el ejemplo siguiente:

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

En este ejemplo, [`Slider`](xref:Xamarin.Forms.Slider) los `Value` datos enlazan su propiedad a la [`Volume`](xref:Xamarin.Forms.MediaElement.Volume) propiedad del [`MediaElement`](xref:Xamarin.Forms.MediaElement)archivo . Esto es posible `Volume` porque `TwoWay` la propiedad utiliza un enlace. Por lo `Value` tanto, cambiar `Volume` la propiedad dará lugar a que la propiedad cambie.

> [!NOTE]
> La [`Volume`](xref:Xamarin.Forms.MediaElement.Volume) propiedad tiene una devolución de llamada de vlidation que garantiza que su valor es mayor o igual que 0.0 y menor o igual que 1.0.

Para obtener más [`Slider`](xref:Xamarin.Forms.Slider) información acerca del uso de un ver, [Xamarin.Forms Slider](~/xamarin-forms/user-interface/slider.md)

## <a name="related-links"></a>Vínculos relacionados

- [MediaElementDemos (muestra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos/)
- [Esquemas de URI](/windows/uwp/app-resources/uri-schemes)
- [Desencadenadores de Xamarin.Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms Slider](~/xamarin-forms/user-interface/slider.md)
- [Android: Formatos multimedia compatibles](https://developer.android.com/guide/topics/media/media-formats)
- [UWP: códecs compatibles](/windows/uwp/audio-video-camera/supported-codecs)
