---
title: Audio de Android
description: El sistema operativo Android proporciona una amplia compatibilidad para multimedia, que engloba audio y vídeo. Esta guía se centra en el audio en Android y trata la reproducción y grabación de audio mediante el reproductor de audio integrado y las clases de grabadora, así como la API de audio de bajo nivel. También se explica cómo trabajar con eventos de audio difundidos por otras aplicaciones, para que los desarrolladores puedan compilar aplicaciones con el comportamiento correcto.
ms.prod: xamarin
ms.assetid: 646ED563-C34E-256D-4B56-29EE99881C27
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: 960b4eb058209547c65a3b438bed541c3ade257c
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521255"
---
# <a name="android-audio"></a>Audio de Android

_El sistema operativo Android proporciona una amplia compatibilidad para multimedia, que engloba audio y vídeo. Esta guía se centra en el audio en Android y trata la reproducción y grabación de audio mediante el reproductor de audio integrado y las clases de grabadora, así como la API de audio de bajo nivel. También se explica cómo trabajar con eventos de audio difundidos por otras aplicaciones, para que los desarrolladores puedan compilar aplicaciones con el comportamiento correcto._


## <a name="overview"></a>Información general

Los dispositivos móviles modernos han adoptado una funcionalidad que anteriormente habría requerido piezas dedicadas &ndash; de cámaras de equipos, reproductores de música y grabadores de vídeo. Por este motivo, los marcos multimedia se han convertido en una característica de primera clase en las API móviles.

Android proporciona una amplia compatibilidad con multimedia. En este artículo se examina el trabajo con audio en Android y se tratan los temas siguientes:

1. **Reproducir audio con MediaPlayer** Usar la `MediaPlayer` clase integrada para reproducir audio, incluidos los archivos de audio locales y los archivos de audio transmitidos `AudioTrack` con la clase. &ndash;

2. **Grabar audio** Usar la `MediaRecorder` clase integrada para grabar audio. &ndash;

3. **Trabajar con notificaciones de audio** &ndash; El uso de notificaciones de audio para crear aplicaciones con un comportamiento correcto que respondan correctamente a eventos (por ejemplo, llamadas telefónicas entrantes) al suspender o cancelar las salidas de audio.

4. **Trabajar con audio de bajo nivel** Reproducción de audio mediante `AudioTrack` la clase escribiendo directamente en los búferes de memoria. &ndash; Grabar audio mediante la `AudioRecord` clase y leer directamente desde los búferes de memoria.


## <a name="requirements"></a>Requisitos

En esta guía se requiere Android 2,0 (nivel de API 5) o posterior. Tenga en cuenta que la depuración de audio en Android debe realizarse en un dispositivo.

Es necesario solicitar los `RECORD_AUDIO` permisos en **archivo AndroidManifest. XML**:

![Sección permisos necesarios del manifiesto de Android con\_registro de audio habilitado](android-audio-images/image01.png)



## <a name="playing-audio-with-the-mediaplayer-class"></a>Reproducir audio con la clase MediaPlayer

La manera más sencilla de reproducir audio en Android es con la clase integrada [MediaPlayer](xref:Android.Media.MediaPlayer) .
`MediaPlayer`puede reproducir archivos locales o remotos pasando la ruta de acceso del archivo. Sin embargo `MediaPlayer` , es muy sensible al estado y al llamar a uno de sus métodos en un estado incorrecto se producirá una excepción. Es importante interactuar con `MediaPlayer` en el orden que se describe a continuación para evitar errores.



### <a name="initializing-and-playing"></a>Inicializar y reproducir

La reproducción de `MediaPlayer` audio con requiere la siguiente secuencia:

1. Cree una instancia de un nuevo objeto [MediaPlayer](xref:Android.Media.MediaPlayer) .

1. Configure el archivo para que se reproduzca mediante el método [SetDataSource](xref:Android.Media.MediaPlayer.SetDataSource*) .

1. Llame al método [Prepare](xref:Android.Media.MediaPlayer.Prepare) para inicializar el reproductor.

1. Llame al método [Start](xref:Android.Media.MediaPlayer.Start) para iniciar la reproducción de audio.


En el ejemplo de código siguiente se muestra este uso:

```csharp
protected MediaPlayer player;
public void StartPlayer(String  filePath)
{
  if (player == null) {
    player = new MediaPlayer();
  } else {
    player.Reset();
    player.SetDataSource(filePath);
    player.Prepare();
    player.Start();
  }
}
```


### <a name="suspending-and-resuming-playback"></a>Suspender y reanudar la reproducción

La reproducción se puede suspender llamando al método [PAUSE](xref:Android.Media.MediaPlayer.Pause) :

```csharp
player.Pause();
```

Para reanudar la reproducción en pausa, llame al método [Start](xref:Android.Media.MediaPlayer.Start) .
Esto se reanudará desde la ubicación en pausa en la reproducción:

```csharp
player.Start();
```

La llamada al método [Stop](xref:Android.Media.MediaPlayer.Stop) en el reproductor finaliza una reproducción en curso:

```csharp
player.Stop();
```

Cuando el reproductor ya no se necesita, se deben liberar los recursos llamando al método [Release](xref:Android.Media.MediaPlayer.Release) :

```csharp
player.Release();
```



## <a name="using-the-mediarecorder-class-to-record-audio"></a>Uso de la clase MediaRecorder para grabar audio

El consecuencia `MediaPlayer` de la grabación de audio en Android es la clase [MediaRecorder](xref:Android.Media.MediaRecorder) . Al igual que, es sensible al estado y realiza transiciones a través de varios Estados para llegar al punto en el que puede comenzar a grabar. `MediaPlayer` Para grabar audio, se debe establecer `RECORD_AUDIO` el permiso. Para obtener instrucciones sobre cómo establecer permisos de aplicación, vea [Working with archivo AndroidManifest. XML](~/android/platform/android-manifest.md).


### <a name="initializing-and-recording"></a>Inicializar y grabar

La grabación de audio `MediaRecorder` con requiere los pasos siguientes:

1. Cree una instancia de un nuevo objeto [MediaRecorder](xref:Android.Media.MediaRecorder) .

2. Especifique el dispositivo de hardware que se va a usar para capturar la entrada de audio a través del método [SetAudioSource](xref:Android.Media.MediaRecorder.SetAudioSource*) .

3. Establezca el formato de audio del archivo de salida mediante el método [SetOutputFormat](xref:Android.Media.MediaRecorder.SetOutputFormat*) . Para obtener una lista de los tipos de audio compatibles, vea [formatos multimedia compatibles con Android](https://developer.android.com/guide/appendix/media-formats.html).

4. Llame al método [SetAudioEncoder](xref:Android.Media.MediaRecorder.SetAudioEncoder*) para establecer el tipo de codificación de audio.

5. Llame al método [SetOutputFile](xref:Android.Media.MediaRecorder.SetOutputFile*) para especificar el nombre del archivo de salida en el que se escriben los datos de audio.

6. Llame al método [Prepare](xref:Android.Media.MediaRecorder.Prepare) para inicializar la grabadora.

7. Llame al método [Start](xref:Android.Media.MediaRecorder.Start) para iniciar la grabación.


En el ejemplo de código siguiente se muestra esta secuencia:

```csharp
protected MediaRecorder recorder;
void RecordAudio (String filePath)
{
  try {
    if (File.Exists (filePath)) {
      File.Delete (filePath);
    }
    if (recorder == null) {
      recorder = new MediaRecorder (); // Initial state.
    } else {
      recorder.Reset ();
      recorder.SetAudioSource (AudioSource.Mic);
      recorder.SetOutputFormat (OutputFormat.ThreeGpp);
      recorder.SetAudioEncoder (AudioEncoder.AmrNb);
      // Initialized state.
      recorder.SetOutputFile (filePath);
      // DataSourceConfigured state.
      recorder.Prepare (); // Prepared state
      recorder.Start (); // Recording state.
    }
  } catch (Exception ex) {
    Console.Out.WriteLine( ex.StackTrace);
  }
}
```


### <a name="stopping-recording"></a>Detener la grabación

Para detener la grabación, llame `Stop` al método `MediaRecorder`en:

```csharp
recorder.Stop();
```



### <a name="cleaning-up"></a>Limpieza

Una vez que `MediaRecorder` se haya detenido, llame al método [RESET](xref:Android.Media.MediaRecorder.Reset) para volver a ponerlo en su estado de inactividad:

```csharp
recorder.Reset();
```

Cuando `MediaRecorder` ya no se necesita, sus recursos deben liberarse llamando al método [Release](xref:Android.Media.MediaRecorder.Release):

```csharp
recorder.Release();
```


## <a name="managing-audio-notifications"></a>Administración de notificaciones de audio



### <a name="the-audiomanager-class"></a>La clase AudioManager

La clase [AudioManager](xref:Android.Media.AudioManager) proporciona acceso a las notificaciones de audio que permiten a las aplicaciones saber cuándo se producen los eventos de audio. Este servicio también proporciona acceso a otras características de audio, como el control de volumen y de modo de timbre. Permite `AudioManager` que una aplicación controle las notificaciones de audio para controlar la reproducción de audio.



### <a name="managing-audio-focus"></a>Administrar el foco de audio

Los recursos de audio del dispositivo (la grabadora y el reproductor integrados) son compartidos por todas las aplicaciones en ejecución.

Conceptualmente, esto es similar a las aplicaciones de un equipo de escritorio en el que solo una aplicación tiene el foco de teclado: después de seleccionar una de las aplicaciones en ejecución al hacer clic en ella, la entrada del teclado solo se dirige a esa aplicación.

El foco de audio es una idea similar y evita que más de una aplicación reproduzca o grabe audio al mismo tiempo. Es más complicado que el foco de teclado porque es un &ndash; factor voluntario de que la aplicación puede omitir el hecho de que actualmente no tiene el &ndash; foco de audio y se reproduce, ya que hay diferentes tipos de foco de audio que se pueden requiere. Por ejemplo, si solo se espera que el solicitante reproduzca audio durante un tiempo muy breve, puede solicitar el foco transitorio.

Se puede conceder el foco de audio inmediatamente, o bien denegarse y concederse en un momento posterior. Por ejemplo, si una aplicación solicita el foco de audio durante una llamada telefónica, se le denegará, pero es posible que se le conceda el foco una vez finalizada la llamada telefónica. En este caso, se registra un agente de escucha para responder en consecuencia si se quita el foco de audio. La solicitud del foco de audio se usa para determinar si es correcto reproducir o grabar audio.

Para obtener más información sobre el foco de audio, consulte [Administración del foco de audio](https://developer.android.com/training/managing-audio/audio-focus.html).



#### <a name="registering-the-callback-for-audio-focus"></a>Registrar la devolución de llamada para el foco de audio

El registro de `FocusChangeListener` la devolución de `IOnAudioChangeListener` llamada desde es una parte importante de la obtención y liberación del foco de audio. Esto se debe a que la concesión del foco de audio se puede diferir hasta un momento posterior. Por ejemplo, una aplicación puede solicitar reproducir música mientras hay una llamada de teléfono en curso. No se concederá el foco de audio hasta que finalice la llamada telefónica.

Por esta razón, el objeto `GetAudioFocus` `AudioManager`de devolución de llamada se pasa como un parámetro al método de y es esta llamada que registra la devolución de llamada. Si el foco de audio se deniega inicialmente, pero se concede posteriormente, la aplicación `OnAudioFocusChange` se informa invocando en la devolución de llamada. Se usa el mismo método para indicar a la aplicación que se está quitando el foco de audio.

Cuando la aplicación ha terminado de usar los recursos `AbandonFocus` `AudioManager`de audio, llama al método de y pasa de nuevo a la devolución de llamada. Esto anula el registro de la devolución de llamada y libera los recursos de audio para que otras aplicaciones puedan obtener el foco de audio.



#### <a name="requesting-audio-focus"></a>Solicitar el foco de audio

Los pasos necesarios para solicitar los recursos de audio del dispositivo son los siguientes:

1. Obtener un identificador para el `AudioManager` servicio de sistema.

2. Cree una instancia de la clase de devolución de llamada.

3. Solicite los recursos de audio del dispositivo llamando al `RequestAudioFocus` método `AudioManager` en. Los parámetros son el objeto de devolución de llamada, el tipo de secuencia (música, llamada de voz, anillo, etc.) y el tipo de derecho de acceso que se solicita (los recursos de audio se pueden solicitar momentáneamente o durante un período indefinido, por ejemplo).

4. Si se concede la solicitud, el `playMusic` método se invoca inmediatamente y el audio comienza a reproducirse.

5. Si se deniega la solicitud, no se realiza ninguna otra acción. En este caso, el audio solo se reproducirá si la solicitud se concede en otro momento.


En el ejemplo de código siguiente se muestran estos pasos:

```csharp
Boolean RequestAudioResources(INotificationReceiver parent)
{
  AudioManager audioMan = (AudioManager) GetSystemService(Context.AudioService);
  AudioManager.IOnAudioFocusChangeListener listener  = new MyAudioListener(this);
  var ret = audioMan.RequestAudioFocus (listener, Stream.Music, AudioFocus.Gain );
  if (ret == AudioFocusRequest.Granted) {
    playMusic();
    return (true);
  } else if (ret == AudioFocusRequest.Failed) {
    return (false);
  }
  return (false);
}
```


#### <a name="releasing-audio-focus"></a>Liberación del foco de audio

Cuando se completa la reproducción de la pista, se `AbandonFocus` invoca el `AudioManager` método en. Esto permite a otra aplicación obtener los recursos de audio del dispositivo. Otras aplicaciones recibirán una notificación de este cambio de foco de audio si han registrado sus propios agentes de escucha.


## <a name="low-level-audio-api"></a>API de audio de bajo nivel

Las API de audio de bajo nivel proporcionan un mayor control sobre la reproducción y grabación de audio, ya que interactúan directamente con los búferes de memoria en lugar de usar los URI de archivo. Hay algunos escenarios en los que es preferible este enfoque. Estos escenarios incluyen:

1. Al reproducirse desde archivos de audio cifrados.

2. Al reproducir una sucesión de clips cortos.

3. Transmisión por secuencias de audio.


### <a name="audiotrack-class"></a>Clase AudioTrack

La clase [AudioTrack](xref:Android.Media.AudioTrack) utiliza las API de audio de bajo nivel para grabar y es el equivalente de bajo nivel de la `MediaPlayer` clase.


#### <a name="initializing-and-playing"></a>Inicializar y reproducir

Para reproducir audio, se debe crear una `AudioTrack` nueva instancia de. La lista de argumentos que se pasa al [constructor](xref:Android.Media.AudioTrack) especifica cómo reproducir el ejemplo de audio contenido en el búfer. Los argumentos son:

1. Tipo &ndash; de flujo voz, tono, música, sistema o alarma.

2. Frecuencia &ndash; : la frecuencia de muestreo expresada en Hz.

3. Mono o &ndash; estéreo de configuración del canal.

4. Formato &ndash; de audio de 8 bits o codificación de 16 bits.

5. Tamaño &ndash; de búfer en bytes.

6. Streaming &ndash; en modo de búfer o static.


Después de la construcción, se invoca el método [Play](xref:Android.Media.AudioTrack.Play) de `AudioTrack` para configurarlo de modo que comience a reproducirse. La escritura del búfer de audio `AudioTrack` en inicia la reproducción:

```csharp
void PlayAudioTrack(byte[] audioBuffer)
{
  AudioTrack audioTrack = new AudioTrack(
    // Stream type
    Stream.Music,
    // Frequency
    11025,
    // Mono or stereo
    ChannelOut.Mono,
    // Audio encoding
    Android.Media.Encoding.Pcm16bit,
    // Length of the audio clip.
    audioBuffer.Length,
    // Mode. Stream or static.
    AudioTrackMode.Stream);

    audioTrack.Play();
    audioTrack.Write(audioBuffer, 0, audioBuffer.Length);
}
```


#### <a name="pausing-and-stopping-the-playback"></a>Pausar y detener la reproducción

Llame al método [PAUSE](xref:Android.Media.AudioTrack.Pause) para pausar la reproducción:

```csharp
audioTrack.Pause();
```

La llamada al método [Stop](xref:Android.Media.AudioTrack.Stop) terminará la reproducción de forma permanente:

```csharp
audioTrack.Stop();
```


#### <a name="cleanup"></a>Reparación

Cuando `AudioTrack` ya no se necesita, sus recursos deben liberarse llamando a [Release](xref:Android.Media.AudioTrack.Release):

```csharp
audioTrack.Release();
```


### <a name="the-audiorecord-class"></a>La clase AudioRecord

La clase [AudioRecord](xref:Android.Media.AudioRecord) es el equivalente de `AudioTrack` en el lado de grabación. Al `AudioTrack`igual que, usa búferes de memoria directamente, en lugar de archivos y URI. Requiere que el `RECORD_AUDIO` permiso se establezca en el manifiesto.


#### <a name="initializing-and-recording"></a>Inicializar y grabar

El primer paso consiste en construir un nuevo objeto [AudioRecord](xref:Android.Media.AudioRecord) . La lista de argumentos que se pasa al [constructor](xref:Android.Media.AudioRecord) proporciona toda la información necesaria para la grabación. A diferencia de `AudioTrack`en, donde los argumentos son en gran medida enumeraciones, los argumentos `AudioRecord` equivalentes en son enteros. Entre ellas se incluyen las siguientes:

1. Origen de entrada de audio de hardware, como el micrófono.

2. Tipo &ndash; de flujo voz, tono, música, sistema o alarma.

3. Frecuencia &ndash; : la frecuencia de muestreo expresada en Hz.

4. Mono o &ndash; estéreo de configuración del canal.

5. Formato &ndash; de audio de 8 bits o codificación de 16 bits.

6. Tamaño de búfer: en bytes


Una vez construida, se invoca su método [StartRecording.](xref:Android.Media.AudioRecord.StartRecording) `AudioRecord` Ahora está listo para empezar a grabar. Lee `AudioRecord` continuamente el búfer de audio para la entrada y escribe esta entrada en un archivo de audio.

```csharp
void RecordAudio()
{
  byte[] audioBuffer = new byte[100000];
  var audRecorder = new AudioRecord(
    // Hardware source of recording.
    AudioSource.Mic,
    // Frequency
    11025,
    // Mono or stereo
    ChannelIn.Mono,
    // Audio encoding
    Android.Media.Encoding.Pcm16bit,
    // Length of the audio clip.
    audioBuffer.Length
  );
  audRecorder.StartRecording();
  while (true) {
    try
    {
      // Keep reading the buffer while there is audio input.
      audRecorder.Read(audioBuffer, 0, audioBuffer.Length);
      // Write out the audio file.
    } catch (Exception ex) {
      Console.Out.WriteLine(ex.Message);
      break;
    }
  }
}
```


#### <a name="stopping-the-recording"></a>Detener la grabación

La llamada al método [Stop](xref:Android.Media.AudioRecord.Stop) finaliza la grabación:

```csharp
audRecorder.Stop();
```


#### <a name="cleanup"></a>Reparación

Cuando el `AudioRecord` objeto ya no es necesario, al llamar a su método [Release](xref:Android.Media.AudioRecord.Release) se liberan todos los recursos asociados a él:

```csharp
audRecorder.Release();
```


## <a name="summary"></a>Resumen

El sistema operativo Android proporciona un marco de trabajo eficaz para reproducir, grabar y administrar audio. En este artículo se ha explicado cómo reproducir y grabar audio mediante las `MediaPlayer` clases `MediaRecorder` y de alto nivel. A continuación, se ha explorado cómo usar las notificaciones de audio para compartir los recursos de audio del dispositivo entre las distintas aplicaciones. Por último, se trata de cómo reproducir y grabar audio mediante las API de bajo nivel, que se interforman directamente con los búferes de memoria.


## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con audio (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/example-workingwithaudio)
- [Media Player](xref:Android.Media.MediaPlayer)
- [Grabadora de medios](xref:Android.Media.MediaRecorder)
- [Administrador de audio](xref:Android.Media.AudioManager)
- [Pista de audio](xref:Android.Media.AudioTrack)
- [Grabadora de audio](xref:Android.Media.AudioRecord)
