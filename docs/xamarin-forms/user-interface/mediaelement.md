---
title: MediaElement de Xamarin. Forms
description: En este artículo se explica cómo usar MediaElement para reproducir vídeo y audio en una aplicación de Xamarin. Forms.
ms.prod: xamarin
ms.assetid: e65f1e56-a80d-46c7-9ff4-7ae6650a3165
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/7/2020
ms.openlocfilehash: 67e4e9eec38f9dd23ebc3efe503d4b2a34ea8b39
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145722"
---
# <a name="xamarinforms-mediaelement"></a>MediaElement de Xamarin. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/WorkingWithMediaElement)

`MediaElement` representa un reproductor multimedia para la reproducción de vídeo y audio. Se pueden usar los controles de reproducción del sistema operativo, denominados controles de transporte, o puede ocultarlos y proporcionar sus propios controles para satisfacer sus requisitos de diseño.

Para poder usar este nuevo control de vista previa, debe optar por usarlo estableciendo la marca adecuada en el `App.xaml.xs`:

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

Plataformas admitidas:

- Android
- iOS
- UWP
- WPF
- macOS
- Tizen

## <a name="set-media-source"></a>Establecer origen de medios

La propiedad `MediaElement` `Source` puede tomar un URI o una ruta de acceso de archivo local. La reproducción comenzará inmediatamente después de la apertura del medio.

```xaml
<MediaElement Source="http://sec.ch9.ms/ch9/5d93/a1eab4bf-3288-4faf-81c4-294402a85d93/XamarinShow_mid.mp4" />
```

![](mediaelement-images/mediaelement-android.png "MediaElement on Android")

Para establecer el origen en un recurso local desde el proyecto de plataforma, use el esquema de URI "MS-appx".

```xaml
<MediaElement Source="ms-appx://XamarinShow_mid.mp4" />
```

Al usar el enlace de datos, puede que desee usar un convertidor de valores para aplicar este esquema de URI:

```csharp
public class VideoSourceConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        if (value == null)
            return null;

        if (String.IsNullOrWhiteSpace(value.ToString()))
            return null;

        if (Device.RuntimePlatform == Device.UWP)
            return new Uri($"ms-appx:///Assets/{value}");
        else
            return new Uri($"ms-appx:///{value}");
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        throw new NotImplementedException();
    }
}
```

```xaml
<MediaElement
    Source="{Binding VideoSource, Converter={StaticResource VideoSourceConverter}}" />
```

## <a name="control-playback"></a>Controlar la reproducción

De forma predeterminada, la `MediaElement` usará los controles de reproducción nativa de las plataformas para reproducir/pausar, volumen y silenciar, buscar y mostrar hora.

Para invalidar e implementar sus propios controles, deshabilite primero los controles de plataforma estableciendo `ShowsPlaybackControls="False"`. Ahora puede usar sus propios controles para reflejar el estado de reproducción y controlar la reproducción con las siguientes propiedades, métodos y eventos:

- `Play()`, `Pause()``Stop()` métodos para controlar la reproducción
- `AutoPlay` para establecer el comportamiento inicial
- `CanSeek` para habilitar o deshabilitar la búsqueda
- propiedades `Position` y `Duration` para Time
- `Volume` propiedad para cambiar el volumen y silenciar
- `IsLooping` propiedad para repetir cuando se completa la reproducción
- `StateRequested`, `PositionRequested``VolumeRequested` para controlar las actualizaciones de la interfaz de usuario

### <a name="handle-additional-media-events"></a>Control de eventos multimedia adicionales

Puede responder a eventos multimedia comunes, como los eventos `MediaOpened`, `MediaEnded`, `MediaFailed`y `CurrentStateChanged`. Es recomendable controlar siempre el evento MediaFailed.
CurrentStateChanged proporcione información sobre el estado de reproducción. Usar un valor de la enumeración `MediaElementState`:

- **Closed**: no contiene ningún medio y muestra un fotograma transparente
- **Abriendo**: está validando e intentando cargar el origen especificado
- **Almacenamiento en búfer**: está cargando los medios para la reproducción
  - Su `Position` no avanza durante este estado
  - Si ya se está reproduciendo vídeo, continúa mostrando el último fotograma mostrado
- **Jugando**: está reproduciendo el origen multimedia actual
- En **pausa**: no avanza su `Position`
  - Si se reproduce vídeo, continúa mostrando el fotograma actual
- **Detenido**: contiene medios pero no se está reproduciendo o pausado
  - Su `Position` es 0 y no avanza
  - Si el medio cargado es un vídeo, el `MediaElement` muestra el primer fotograma.

## <a name="control-display"></a>Mostrar control

De forma similar a [`Image`](xref:Xamarin.Forms.Image), el control `MediaElement` tiene `VideoHeight`, `VideoWidth`y `Aspect`. El aspecto toma tres opciones diferentes:

- **AspectFill**: el vídeo llenará todo el ancho y el alto del control, a menudo sangrado fuera de los límites del control y manteniendo el aspecto del origen.
- **AspectFit**: el vídeo se ajustará dentro del ancho y el alto del control manteniendo el aspecto del origen.
- **Fill**: el vídeo rellenará el ancho y el alto del control.

## <a name="additional-properties"></a>Propiedades adicionales

- `VideoWidth`: ancho del control
- `VideoHeight`: alto del control
- `KeepScreenOn`: Si la pantalla del dispositivo debe permanecer activada durante la reproducción

## <a name="related-links"></a>Vínculos relacionados

- [MediaElement (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/WorkingWithMediaElement)
