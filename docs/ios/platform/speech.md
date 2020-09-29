---
title: Reconocimiento de voz en Xamarin. iOS
description: En este artículo se presentan las nuevas Speech API y se muestra cómo implementarlas en una aplicación de Xamarin. iOS para admitir el reconocimiento de voz continuo y la conversión de voz (desde secuencias de audio en directo o grabadas) en texto.
ms.prod: xamarin
ms.assetid: 64FED50A-6A28-4833-BEAE-63CEC9A09010
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 98cbcd333d223d741602786643ef1948915d7dfc
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436461"
---
# <a name="speech-recognition-in-xamarinios"></a>Reconocimiento de voz en Xamarin. iOS

_En este artículo se presentan las nuevas Speech API y se muestra cómo implementarlas en una aplicación de Xamarin. iOS para admitir el reconocimiento de voz continuo y la conversión de voz (desde secuencias de audio en directo o grabadas) en texto._

Novedad de iOS 10, Apple ha emitido la API de reconocimiento de voz que permite que una aplicación de iOS admita el reconocimiento de voz continuo y la conversión de voz (desde secuencias de audio en directo o grabadas) en texto.

Según Apple, la API de reconocimiento de voz tiene las siguientes características y ventajas:

- Muy precisa
- Estado del arte
- Fácil de usar
- Fast (rápido)
- Admite varios idiomas
- Respeta la privacidad del usuario

## <a name="how-speech-recognition-works"></a>Cómo funciona el reconocimiento de voz

El reconocimiento de voz se implementa en una aplicación de iOS adquiriendo audio en directo o grabado previamente (en cualquiera de los idiomas que admite la API) y pasándolo a un reconocedor de voz que devuelve una transcripción de texto sin formato de las palabras pronunciadas.

[![Cómo funciona el reconocimiento de voz](speech-images/speech01.png)](speech-images/speech01.png#lightbox)

### <a name="keyboard-dictation"></a>Dictado de teclado

Cuando la mayoría de los usuarios piensan en el reconocimiento de voz en un dispositivo iOS, piensan en el Asistente de Siri Voice integrado, que se lanzó junto con el dictado de teclado en iOS 5 con la 4S de iPhone.

El dictado de teclado es compatible con cualquier elemento de interfaz que admita TextKit (como `UITextField` o `UITextArea` ) y lo activa el usuario al hacer clic en el botón de dictado (directamente a la izquierda de la barra espaciadora) en el teclado virtual de iOS.

Apple ha publicado las siguientes estadísticas de dictado de teclado (recopiladas desde 2011):

- El dictado de teclado se ha usado ampliamente desde que se lanzó en iOS 5.
- Aproximadamente 65.000 aplicaciones la usan al día.
- Una tercera parte de todo el dictado de iOS se realiza en una aplicación de terceros.

El dictado de teclado es muy fácil de usar, ya que no requiere ningún esfuerzo en la parte del desarrollador, excepto en el uso de un elemento de la interfaz TextKit en el diseño de la interfaz de usuario de la aplicación. El dictado de teclado también tiene la ventaja de no requerir ninguna solicitud de privilegio especial de la aplicación antes de que se pueda usar.

Las aplicaciones que usan las nuevas API de reconocimiento de voz requerirán permisos especiales para que las conceda el usuario, ya que el reconocimiento de voz requiere la transmisión y el almacenamiento temporal de los datos en los servidores de Apple. Para obtener más información, consulte nuestra documentación sobre [mejoras de seguridad y privacidad](~/ios/app-fundamentals/security-privacy.md) .

Aunque el dictado de teclado es fácil de implementar, incluye varias limitaciones y desventajas:

- Requiere el uso de un campo de entrada de texto y la presentación de un teclado.
- Funciona solo con entrada de audio en vivo y la aplicación no tiene control sobre el proceso de grabación de audio.
- No proporciona ningún control sobre el idioma que se usa para interpretar la voz del usuario.
- No hay ninguna manera de que la aplicación sepa si el botón dictado está incluso disponible para el usuario.
- La aplicación no puede personalizar el proceso de grabación de audio.
- Proporciona un conjunto muy superficial de resultados que carecen de información como el tiempo y la confianza.

### <a name="speech-recognition-api"></a>API de reconocimiento de voz

Como novedad de iOS 10, Apple ha lanzado la API de reconocimiento de voz, que proporciona una manera más eficaz para que una aplicación de iOS implemente el reconocimiento de voz. Esta API es la misma que usa Apple para alimentar el Siri y el dictado de teclado, y es capaz de proporcionar una transcripción rápida con el estado de la precisión del arte.

Los resultados proporcionados por la API de reconocimiento de voz están personalizados de forma transparente para los usuarios individuales, sin que la aplicación tenga que recopilar o tener acceso a los datos de usuario privados.

La API de reconocimiento de voz proporciona los resultados a la aplicación que realiza la llamada casi en tiempo real a medida que el usuario está hablando y proporciona más información sobre los resultados de la traducción que solo texto. Estas incluyen:

- Varias interpretaciones de lo que el usuario dijo.
- Niveles de confianza para las traducciones individuales.
- Información de temporización.

Como se indicó anteriormente, el audio para la traducción se puede proporcionar mediante una fuente activa, o bien desde un origen grabado previamente y en cualquiera de los idiomas y dialectos más 50 compatibles con iOS 10.

La API de reconocimiento de voz se puede usar en cualquier dispositivo iOS que ejecute iOS 10 y, en la mayoría de los casos, requiere una conexión a Internet activa, ya que la mayor parte de las traducciones tiene lugar en los servidores de Apple. Dicho esto, algunos dispositivos iOS más recientes admiten la traducción en el dispositivo de idiomas específicos de AlwaysOn.

Apple ha incluido una API de disponibilidad para determinar si un idioma determinado está disponible para la traducción en el momento actual. La aplicación debe usar esta API en lugar de probar directamente la conectividad a Internet.

Como se indicó anteriormente en la sección de dictado del teclado, el reconocimiento de voz requiere la transmisión y el almacenamiento temporal de los datos en los servidores de Apple a través de Internet y, por tanto, la aplicación _debe_ solicitar el permiso del usuario para realizar el reconocimiento incluyendo la `NSSpeechRecognitionUsageDescription` clave en su `Info.plist` archivo y llamando al `SFSpeechRecognizer.RequestAuthorization` método. 

En función del origen del audio que se usa para el reconocimiento de voz, `Info.plist` es posible que se requieran otros cambios en el archivo de la aplicación. Para obtener más información, consulte nuestra documentación sobre [mejoras de seguridad y privacidad](~/ios/app-fundamentals/security-privacy.md) .

## <a name="adopting-speech-recognition-in-an-app"></a>Adopción del reconocimiento de voz en una aplicación

Hay cuatro pasos principales que el desarrollador debe llevar a cabo para adoptar el reconocimiento de voz en una aplicación de iOS:

- Proporcione una descripción del uso en el archivo de la aplicación `Info.plist` mediante la `NSSpeechRecognitionUsageDescription` clave. Por ejemplo, una aplicación de cámara podría incluir la descripción siguiente, _"Esto le permite realizar una foto simplemente diciendo la palabra" queso "._
- Solicite autorización llamando al `SFSpeechRecognizer.RequestAuthorization` método para presentar una explicación (proporcionada en la `NSSpeechRecognitionUsageDescription` clave anterior) de por qué la aplicación desea que el reconocimiento de voz acceda al usuario en un cuadro de diálogo y permita que acepte o rechace.
- Cree una solicitud de reconocimiento de voz:
  - Para el audio grabado previamente en el disco, use la `SFSpeechURLRecognitionRequest` clase.
  - Para audio en vivo (o audio de la memoria), use la `SFSPeechAudioBufferRecognitionRequest` clase.
- Pase la solicitud de reconocimiento de voz a un reconocedor de voz ( `SFSpeechRecognizer` ) para comenzar el reconocimiento. Opcionalmente, la aplicación puede mantener en el devuelto `SFSpeechRecognitionTask` para supervisar y realizar un seguimiento de los resultados del reconocimiento.

Estos pasos se tratarán con más detalle a continuación.

### <a name="providing-a-usage-description"></a>Proporcionar una descripción de uso

Para proporcionar la `NSSpeechRecognitionUsageDescription` clave necesaria en el `Info.plist` archivo, haga lo siguiente:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Haga doble clic en el `Info.plist` archivo para abrirlo y editarlo.
2. Cambie a la vista de **código fuente** : 

    [![Vista de código fuente](speech-images/speech02.png)](speech-images/speech02.png#lightbox)
3. Haga clic en **Agregar nueva entrada**, escriba en `NSSpeechRecognitionUsageDescription` la **propiedad**para `String` el **tipo** y una **Descripción de uso** como **valor**. Por ejemplo: 

    [![Agregar NSSpeechRecognitionUsageDescription](speech-images/speech03.png)](speech-images/speech03.png#lightbox)
4. Si la aplicación va a controlar la transcripción de audio en directo, también necesitará una descripción de uso del micrófono. Haga clic en **Agregar nueva entrada**, escriba en `NSMicrophoneUsageDescription` la **propiedad**para `String` el **tipo** y una **Descripción de uso** como **valor**. Por ejemplo: 

    [![Agregar NSMicrophoneUsageDescription](speech-images/speech04.png)](speech-images/speech04.png#lightbox)
5. Guarde los cambios en el archivo.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Haga doble clic en el `Info.plist` archivo para abrirlo y editarlo.
2. Haga clic en **Agregar nueva entrada**, escriba en `NSSpeechRecognitionUsageDescription` la **propiedad**para `String` el **tipo** y una **Descripción de uso** como **valor**. Por ejemplo: 

    [![Agregar NSSpeechRecognitionUsageDescription](speech-images/speech03w.png)](speech-images/speech03w.png#lightbox)
3. Si la aplicación va a controlar la transcripción de audio en directo, también necesitará una descripción de uso del micrófono. Haga clic en **Agregar nueva entrada**, escriba en `NSMicrophoneUsageDescription` la **propiedad**para `String` el **tipo** y una **Descripción de uso** como **valor**. Por ejemplo: 

    [![Agregar NSMicrophoneUsageDescription](speech-images/speech04w.png)](speech-images/speech04w.png#lightbox)
4. Guarde los cambios en el archivo.

-----

> [!IMPORTANT]
> Si no se proporciona ninguna de las `Info.plist` claves anteriores ( `NSSpeechRecognitionUsageDescription` o), se puede producir `NSMicrophoneUsageDescription` un error en la aplicación sin que se produzca una advertencia al intentar tener acceso al reconocimiento de voz o al micrófono para audio en vivo.

### <a name="requesting-authorization"></a>Solicitud de autorización

Para solicitar la autorización de usuario necesaria que permite a la aplicación tener acceso al reconocimiento de voz, edite la clase de controlador de vista principal y agregue el código siguiente:

```csharp
using System;
using UIKit;
using Speech;

namespace MonkeyTalk
{
    public partial class ViewController : UIViewController
    {
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Request user authorization
            SFSpeechRecognizer.RequestAuthorization ((SFSpeechRecognizerAuthorizationStatus status) => {
                // Take action based on status
                switch (status) {
                case SFSpeechRecognizerAuthorizationStatus.Authorized:
                    // User has approved speech recognition
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.Denied:
                    // User has declined speech recognition
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.NotDetermined:
                    // Waiting on approval
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.Restricted:
                    // The device is not permitted
                    ...
                    break;
                }
            });
        }
    }
}
```

El `RequestAuthorization` método de la `SFSpeechRecognizer` clase solicitará permiso al usuario para tener acceso al reconocimiento de voz mediante el motivo por el que el desarrollador proporcionó la `NSSpeechRecognitionUsageDescription` clave del `Info.plist` archivo.

`SFSpeechRecognizerAuthorizationStatus`Se devuelve un resultado a la `RequestAuthorization` rutina de devolución de llamada del método, que se puede usar para realizar una acción basada en el permiso del usuario. 

> [!IMPORTANT]
> Apple sugiere esperar hasta que el usuario haya iniciado una acción en la aplicación que requiera reconocimiento de voz antes de solicitar este permiso.

### <a name="recognizing-pre-recorded-speech"></a>Reconocimiento de la voz grabada previamente

Si la aplicación desea reconocer la voz de un archivo WAV o MP3 grabado previamente, puede usar el código siguiente:

```csharp
using System;
using UIKit;
using Speech;
using Foundation;
...

public void RecognizeFile (NSUrl url)
{
    // Access new recognizer
    var recognizer = new SFSpeechRecognizer ();

    // Is the default language supported?
    if (recognizer == null) {
        // No, return to caller
        return;
    }

    // Is recognition available?
    if (!recognizer.Available) {
        // No, return to caller
        return;
    }

    // Create recognition task and start recognition
    var request = new SFSpeechUrlRecognitionRequest (url);
    recognizer.GetRecognitionTask (request, (SFSpeechRecognitionResult result, NSError err) => {
        // Was there an error?
        if (err != null) {
            // Handle error
            ...
        } else {
            // Is this the final translation?
            if (result.Final) {
                Console.WriteLine ("You said, \"{0}\".", result.BestTranscription.FormattedString);
            }
        }
    });
}
```

En primer lugar, al examinar este código, se intenta crear un reconocedor de voz ( `SFSpeechRecognizer` ). Si el idioma predeterminado no es compatible con el reconocimiento de voz, `null` se devuelve y las funciones salen.

Si el reconocedor de voz está disponible para el idioma predeterminado, la aplicación comprueba si está disponible actualmente para el reconocimiento mediante la `Available` propiedad. Por ejemplo, es posible que el reconocimiento no esté disponible si el dispositivo no tiene una conexión a Internet activa.

`SFSpeechUrlRecognitionRequest`Se crea un a partir de la `NSUrl` Ubicación del archivo grabado previamente en el dispositivo iOS y se entrega al reconocedor de voz para que procese con una rutina de devolución de llamada.

Cuando se llama a la devolución de llamada, si no se ha `NSError` `null` producido un error que se debe controlar. Dado que el reconocimiento de voz se realiza de forma incremental, la rutina de devolución de llamada se puede llamar más de una vez para que `SFSpeechRecognitionResult.Final` se pruebe la propiedad para ver si la traducción está completa y se escribe la mejor versión de la traducción ( `BestTranscription` ).

### <a name="recognizing-live-speech"></a>Reconocimiento de voz en vivo

Si la aplicación desea reconocer voz en directo, el proceso es muy similar al reconocimiento de la voz grabada previamente. Por ejemplo:

```csharp
using System;
using UIKit;
using Speech;
using Foundation;
using AVFoundation;
...

#region Private Variables
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
#endregion
...

public void StartRecording ()
{
    // Setup audio session
    var node = AudioEngine.InputNode;
    var recordingFormat = node.GetBusOutputFormat (0);
    node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
        // Append buffer to recognition request
        LiveSpeechRequest.Append (buffer);
    });

    // Start recording
    AudioEngine.Prepare ();
    NSError error;
    AudioEngine.StartAndReturnError (out error);

    // Did recording start?
    if (error != null) {
        // Handle error and return
        ...
        return;
    }

    // Start recognition
    RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
        // Was there an error?
        if (err != null) {
            // Handle error
            ...
        } else {
            // Is this the final translation?
            if (result.Final) {
                Console.WriteLine ("You said \"{0}\".", result.BestTranscription.FormattedString);
            }
        }
    });
}

public void StopRecording ()
{
    AudioEngine.Stop ();
    LiveSpeechRequest.EndAudio ();
}

public void CancelRecording ()
{
    AudioEngine.Stop ();
    RecognitionTask.Cancel ();
}
```

En este código, se crean varias variables privadas para controlar el proceso de reconocimiento:

```csharp
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
```

Utiliza AV Foundation para grabar audio que se pasará a un `SFSpeechAudioBufferRecognitionRequest` para controlar la solicitud de reconocimiento:

```csharp
var node = AudioEngine.InputNode;
var recordingFormat = node.GetBusOutputFormat (0);
node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
    // Append buffer to recognition request
    LiveSpeechRequest.Append (buffer);
});
```

La aplicación intenta iniciar la grabación y los errores se administran si no se puede iniciar la grabación:

```csharp
AudioEngine.Prepare ();
NSError error;
AudioEngine.StartAndReturnError (out error);

// Did recording start?
if (error != null) {
    // Handle error and return
    ...
    return;
}
```

La tarea de reconocimiento se inicia y se mantiene un identificador en la tarea de reconocimiento ( `SFSpeechRecognitionTask` ):

```csharp
RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
    ...
});
```

La devolución de llamada se utiliza de manera similar a la usada anteriormente en la voz grabada previamente.

Si el usuario detiene la grabación, se informa al motor de audio y a la solicitud de reconocimiento de voz:

```csharp
AudioEngine.Stop ();
LiveSpeechRequest.EndAudio ();
```

Si el usuario cancela el reconocimiento, se informa al motor de audio y a la tarea de reconocimiento:

```csharp
AudioEngine.Stop ();
RecognitionTask.Cancel ();
```

Es importante llamar a `RecognitionTask.Cancel` si el usuario cancela la traducción para liberar memoria y el procesador del dispositivo.

> [!IMPORTANT]
> Si no se proporcionan las `NSSpeechRecognitionUsageDescription` `NSMicrophoneUsageDescription` `Info.plist` claves o, se puede producir un error en la aplicación sin avisar al intentar tener acceso al reconocimiento de voz o al micrófono para audio en vivo ( `var node = AudioEngine.InputNode;` ). Para obtener más información, consulte la sección **proporcionar una descripción de uso** anterior.

## <a name="speech-recognition-limits"></a>Límites del reconocimiento de voz

Apple impone las siguientes limitaciones cuando se trabaja con el reconocimiento de voz en una aplicación iOS:

- El reconocimiento de voz es gratuito para todas las aplicaciones, pero su uso no es ilimitado:
  - Los dispositivos iOS individuales tienen un número limitado de reconocimientos que se pueden realizar al día.
  - Las aplicaciones se limitarán globalmente según una solicitud por día.
- La aplicación debe estar preparada para controlar los errores de límite de velocidad de uso y conexión de red del reconocimiento de voz.
- El reconocimiento de voz puede tener un alto costo en el agotamiento de la batería y en el tráfico de red elevado en el dispositivo iOS del usuario, por lo que Apple impone un límite de duración de audio estricto de aproximadamente un minuto de longitud máxima.

Si una aplicación alcanza los límites de limitación de velocidad, Apple le pide que se ponga en contacto con ellos.

## <a name="privacy-and-usability-considerations"></a>Consideraciones sobre privacidad y facilidad de uso

Apple tiene la siguiente sugerencia para ser transparente y respetar la privacidad del usuario al incluir el reconocimiento de voz en una aplicación iOS:

- Al grabar la voz del usuario, asegúrese de indicar claramente que la grabación se está llevando a cabo en la interfaz de usuario de la aplicación. Por ejemplo, la aplicación puede reproducir un sonido "grabando" y mostrar un indicador de grabación.
- No utilice el reconocimiento de voz para información confidencial del usuario, como contraseñas, datos de estado o información financiera.
- Mostrar los resultados del reconocimiento _antes_ de actuar en ellos. Esto no solo proporciona comentarios sobre lo que hace la aplicación, sino que permite al usuario controlar los errores de reconocimiento a medida que se realizan.

## <a name="summary"></a>Resumen

En este artículo se han presentado los nuevos Speech API y se ha mostrado cómo implementarlo en una aplicación de Xamarin. iOS para admitir el reconocimiento de voz continuo y la conversión de voz (desde secuencias de audio en directo o grabadas) en texto. 

## <a name="related-links"></a>Vínculos relacionados

- [SpeakToMe (ejemplo)](/samples/xamarin/ios-samples/ios10-speaktome)