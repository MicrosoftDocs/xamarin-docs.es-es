---
title: Reconocimiento de voz mediante la API del servicio Speech
description: En este artículo se explica cómo usar la API del servicio de voz de Azure para transcribir la voz en el texto de una Xamarin.Forms aplicación.
ms.prod: xamarin
ms.assetid: B435FF6B-8785-48D9-B2D9-1893F5A87EA1
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 01/14/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e5888e77948bef0fbb60a4e0068cbedf2d2a50e7
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555390"
---
# <a name="speech-recognition-using-azure-speech-service"></a>Reconocimiento de voz con el servicio de voz de Azure

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-cognitivespeechservice)

El servicio de voz de Azure es una API basada en la nube que ofrece la funcionalidad siguiente:

- **La conversión de voz en texto** transcripción archivos de audio o secuencias en texto.
- **La conversión de texto a voz convierte el** texto de entrada en voz sintetizada de tipo humano.
- La **traducción de voz** permite la traducción en tiempo real en varios idiomas tanto en voz a texto como en voz a voz.
- Los **asistentes de voz** pueden crear interfaces de conversación de tipo humano para aplicaciones.

En este artículo se explica cómo se implementa Speech-to-Text en la aplicación de ejemplo Xamarin.Forms con el servicio de voz de Azure. En las siguientes capturas de pantallas se muestra la aplicación de ejemplo en iOS y Android:

[![Capturas de pantallas de la aplicación de ejemplo en iOS y Android](speech-recognition-images/speech-recognition-cropped.png)](speech-recognition-images/speech-recognition.png#lightbox "Capturas de pantallas de la aplicación de ejemplo en iOS y Android")

## <a name="create-an-azure-speech-service-resource"></a>Creación de un recurso de Azure Speech Services

El servicio de voz de Azure forma parte de Azure Cognitive Services, que proporciona API basadas en la nube para tareas como el reconocimiento de imágenes, el reconocimiento de voz y la traducción, y la búsqueda de Bing. Para obtener más información, consulte [¿Qué son Azure Cognitive Services?](/azure/cognitive-services/welcome).

El proyecto de ejemplo requiere la creación de un recurso de Cognitive Services de Azure en el Azure Portal. Se puede crear un recurso de Cognitive Services para un servicio único, como Speech Service, o como un recurso de varios servicios. Los pasos para crear un recurso de Speech Services son los siguientes:

1. Inicie sesión en el [Azure portal](https://portal.azure.com).
1. Cree un recurso de varios servicios o de un solo servicio.
1. Obtenga la clave de API y la información de la región para el recurso.
1. Actualice el archivo **constants.CS** de ejemplo.

Para obtener una guía paso a paso sobre la creación de un recurso, vea [creación de un recurso de Cognitive Services](/azure/cognitive-services/cognitive-services-apis-create-account).

> [!NOTE]
> Si no tiene una [suscripción a Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), cree una [cuenta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de empezar. Una vez que tenga una cuenta, se puede crear un recurso de servicio único en el nivel gratis para probar el servicio.

## <a name="configure-your-app-with-the-speech-service"></a>Configuración de la aplicación con el servicio de voz

Después de crear un recurso de Cognitive Services, el archivo **constants.CS** se puede actualizar con la región y la clave de API del recurso de Azure:

```csharp
public static class Constants
{
    public static string CognitiveServicesApiKey = "YOUR_KEY_GOES_HERE";
    public static string CognitiveServicesRegion = "westus";
}
```

## <a name="install-nuget-speech-service-package"></a>Instalar el paquete NuGet Speech Service

La aplicación de ejemplo usa el paquete NuGet **Microsoft. cognitiveservices account. Speech** para conectarse al servicio de voz de Azure. Instale este paquete de NuGet en el proyecto compartido y en cada proyecto de plataforma.

## <a name="create-an-imicrophoneservice-interface"></a>Creación de una interfaz IMicrophoneService

Cada plataforma requiere permiso para acceder al micrófono. El proyecto de ejemplo proporciona una `IMicrophoneService` interfaz en el proyecto compartido y utiliza Xamarin.Forms `DependencyService` para obtener implementaciones de la plataforma de la interfaz.

```csharp
public interface IMicrophoneService
{
    Task<bool> GetPermissionAsync();
    void OnRequestPermissionResult(bool isGranted);
}
```

## <a name="create-the-page-layout"></a>Crear el diseño de página

El proyecto de ejemplo define un diseño de página básico en el archivo **mainpage. Xaml** . Los elementos de diseño de clave son un `Button` que inicia el proceso de transcripción, un que `Label` contiene el texto transformado y un `ActivityIndicator` para mostrar cuándo está en curso la transcripción:

```xaml
<ContentPage ...>
    <StackLayout>
        <Frame ...>
            <ScrollView x:Name="scroll"
                        ...>
                <Label x:Name="transcribedText"
                       ... />
            </ScrollView>
        </Frame>

        <ActivityIndicator x:Name="transcribingIndicator"
                           IsRunning="False" />
        <Button x:Name="transcribeButton"
                ...
                Clicked="TranscribeClicked"/>
    </StackLayout>
</ContentPage>
```

## <a name="implement-the-speech-service"></a>Implementar el servicio de voz

El archivo de código subyacente **mainpage.Xaml.CS** contiene toda la lógica para enviar audio y recibir texto transasignado desde el servicio de voz de Azure.

El `MainPage` constructor obtiene una instancia de la `IMicrophoneService` interfaz de `DependencyService` :

```csharp
public partial class MainPage : ContentPage
{
    SpeechRecognizer recognizer;
    IMicrophoneService micService;
    bool isTranscribing = false;

    public MainPage()
    {
        InitializeComponent();

        micService = DependencyService.Resolve<IMicrophoneService>();
    }

    // ...
}
```

`TranscribeClicked`Se llama al método cuando `transcribeButton` se puntea la instancia:

```csharp
async void TranscribeClicked(object sender, EventArgs e)
{
    bool isMicEnabled = await micService.GetPermissionAsync();

    // EARLY OUT: make sure mic is accessible
    if (!isMicEnabled)
    {
        UpdateTranscription("Please grant access to the microphone!");
        return;
    }

    // initialize speech recognizer 
    if (recognizer == null)
    {
        var config = SpeechConfig.FromSubscription(Constants.CognitiveServicesApiKey, Constants.CognitiveServicesRegion);
        recognizer = new SpeechRecognizer(config);
        recognizer.Recognized += (obj, args) =>
        {
            UpdateTranscription(args.Result.Text);
        };
    }

    // if already transcribing, stop speech recognizer
    if (isTranscribing)
    {
        try
        {
            await recognizer.StopContinuousRecognitionAsync();
        }
        catch(Exception ex)
        {
            UpdateTranscription(ex.Message);
        }
        isTranscribing = false;
    }

    // if not transcribing, start speech recognizer
    else
    {
        Device.BeginInvokeOnMainThread(() =>
        {
            InsertDateTimeRecord();
        });
        try
        {
            await recognizer.StartContinuousRecognitionAsync();
        }
        catch(Exception ex)
        {
            UpdateTranscription(ex.Message);
        }
        isTranscribing = true;
    }
    UpdateDisplayState();
}
```

El método `TranscribeClicked` hace lo siguiente:

1. Comprueba si la aplicación tiene acceso al micrófono y sale pronto si no lo hace.
1. Crea una instancia de la `SpeechRecognizer` clase si aún no existe.
1. Detiene la transcripción continua si está en curso.
1. Inserta una marca de tiempo e inicia la transcripción continua si no está en curso.
1. Notifica a la aplicación que actualice su apariencia en función del nuevo estado de la aplicación.

El resto de los `MainPage` métodos de clase son aplicaciones auxiliares para mostrar el estado de la aplicación:

```csharp
void UpdateTranscription(string newText)
{
    Device.BeginInvokeOnMainThread(() =>
    {
        if (!string.IsNullOrWhiteSpace(newText))
        {
            transcribedText.Text += $"{newText}\n";
        }
    });
}

void InsertDateTimeRecord()
{
    var msg = $"=================\n{DateTime.Now.ToString()}\n=================";
    UpdateTranscription(msg);
}

void UpdateDisplayState()
{
    Device.BeginInvokeOnMainThread(() =>
    {
        if (isTranscribing)
        {
            transcribeButton.Text = "Stop";
            transcribeButton.BackgroundColor = Color.Red;
            transcribingIndicator.IsRunning = true;
        }
        else
        {
            transcribeButton.Text = "Transcribe";
            transcribeButton.BackgroundColor = Color.Green;
            transcribingIndicator.IsRunning = false;
        }
    });
}
```

El `UpdateTranscription` método escribe el proporcionado `newText` `string` en el `Label` elemento denominado `transcribedText` . Obliga a que esta actualización se produzca en el subproceso de la interfaz de usuario, por lo que se puede llamar desde cualquier contexto sin producir excepciones. `InsertDateTimeRecord`Escribe la fecha y hora actuales en la `transcribedText` instancia de para marcar el inicio de una nueva transcripción. Por último, el `UpdateDisplayState` método actualiza `Button` los `ActivityIndicator` elementos y para reflejar si la transcripción está en curso o no.

## <a name="create-platform-microphone-services"></a>Creación de servicios de micrófono de plataforma

La aplicación debe tener acceso al micrófono para recopilar datos de voz. La `IMicrophoneService` interfaz debe implementarse y registrarse con `DependencyService` en cada plataforma para que la aplicación funcione.

### <a name="android"></a>Android

El proyecto de ejemplo define una `IMicrophoneService` implementación de para Android denominada `AndroidMicrophoneService` :

```csharp
[assembly: Dependency(typeof(AndroidMicrophoneService))]
namespace CognitiveSpeechService.Droid.Services
{
    public class AndroidMicrophoneService : IMicrophoneService
    {
        public const int RecordAudioPermissionCode = 1;
        private TaskCompletionSource<bool> tcsPermissions;
        string[] permissions = new string[] { Manifest.Permission.RecordAudio };

        public Task<bool> GetPermissionAsync()
        {
            tcsPermissions = new TaskCompletionSource<bool>();

            if ((int)Build.VERSION.SdkInt < 23)
            {
                tcsPermissions.TrySetResult(true);
            }
            else
            {
                var currentActivity = MainActivity.Instance;
                if (ActivityCompat.CheckSelfPermission(currentActivity, Manifest.Permission.RecordAudio) != (int)Permission.Granted)
                {
                    RequestMicPermissions();
                }
                else
                {
                    tcsPermissions.TrySetResult(true);
                }

            }

            return tcsPermissions.Task;
        }

        public void OnRequestPermissionResult(bool isGranted)
        {
            tcsPermissions.TrySetResult(isGranted);
        }

        void RequestMicPermissions()
        {
            if (ActivityCompat.ShouldShowRequestPermissionRationale(MainActivity.Instance, Manifest.Permission.RecordAudio))
            {
                Snackbar.Make(MainActivity.Instance.FindViewById(Android.Resource.Id.Content),
                        "Microphone permissions are required for speech transcription!",
                        Snackbar.LengthIndefinite)
                        .SetAction("Ok", v =>
                        {
                            ((Activity)MainActivity.Instance).RequestPermissions(permissions, RecordAudioPermissionCode);
                        })
                        .Show();
            }
            else
            {
                ActivityCompat.RequestPermissions((Activity)MainActivity.Instance, permissions, RecordAudioPermissionCode);
            }
        }
    }
}
```

`AndroidMicrophoneService`Tiene las siguientes características:

1. El `Dependency` atributo registra la clase con `DependencyService` .
1. El `GetPermissionAsync` método comprueba si se requieren permisos en función de la versión de Android SDK y llama a `RequestMicPermissions` si aún no se ha concedido el permiso.
1. El `RequestMicPermissions` método utiliza la `Snackbar` clase para solicitar permisos al usuario si se requiere un razonamiento; de lo contrario, solicita directamente los permisos de grabación de audio.
1. `OnRequestPermissionResult`Se llama al método con un `bool` resultado una vez que el usuario ha respondido a la solicitud de permisos.

La `MainActivity` clase se personaliza para actualizar la `AndroidMicrophoneService` instancia cuando se completan las solicitudes de permisos:

```csharp
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    IMicrophoneService micService;
    internal static MainActivity Instance { get; private set; }
    
    protected override void OnCreate(Bundle savedInstanceState)
    {
        Instance = this;
        // ...
        micService = DependencyService.Resolve<IMicrophoneService>();
    }
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Android.Content.PM.Permission[] grantResults)
    {
        // ...
        switch(requestCode)
        {
            case AndroidMicrophoneService.RecordAudioPermissionCode:
                if (grantResults[0] == Permission.Granted)
                {
                    micService.OnRequestPermissionResult(true);
                }
                else
                {
                    micService.OnRequestPermissionResult(false);
                }
                break;
        }
    }
}
```

La `MainActivity` clase define una referencia estática denominada `Instance` , que es necesaria para el `AndroidMicrophoneService` objeto al solicitar permisos. Invalida el `OnRequestPermissionsResult` método para actualizar el `AndroidMicrophoneService` objeto cuando el usuario aprueba o deniega la solicitud de permisos.

Por último, la aplicación de Android debe incluir el permiso para grabar audio en el archivo de **AndroidManifest.xml** :

```xml
<manifest ...>
    ...
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
</manifest>
```

### <a name="ios"></a>iOS

El proyecto de ejemplo define una `IMicrophoneService` implementación de para iOS llamada `iOSMicrophoneService` :

```csharp
[assembly: Dependency(typeof(iOSMicrophoneService))]
namespace CognitiveSpeechService.iOS.Services
{
    public class iOSMicrophoneService : IMicrophoneService
    {
        TaskCompletionSource<bool> tcsPermissions;

        public Task<bool> GetPermissionAsync()
        {
            tcsPermissions = new TaskCompletionSource<bool>();
            RequestMicPermission();
            return tcsPermissions.Task;
        }

        public void OnRequestPermissionResult(bool isGranted)
        {
            tcsPermissions.TrySetResult(isGranted);
        }

        void RequestMicPermission()
        {
            var session = AVAudioSession.SharedInstance();
            session.RequestRecordPermission((granted) =>
            {
                tcsPermissions.TrySetResult(granted);
            });
        }
    }
}
```

`iOSMicrophoneService`Tiene las siguientes características:

1. El `Dependency` atributo registra la clase con `DependencyService` .
1. El `GetPermissionAsync` método llama `RequestMicPermissions` a para solicitar permisos al usuario del dispositivo.
1. El `RequestMicPermissions` método utiliza la `AVAudioSession` instancia compartida para solicitar permisos de grabación.
1. El `OnRequestPermissionResult` método actualiza la `TaskCompletionSource` instancia con el `bool` valor proporcionado.

Por último, el archivo **info. plist** de la aplicación iOS debe incluir un mensaje que indique al usuario el motivo por el que la aplicación solicita el acceso al micrófono. Edite el archivo info. plist para incluir las siguientes etiquetas en el `<dict>` elemento:

```xml
<plist>
    <dict>
        ...
        <key>NSMicrophoneUsageDescription</key>
        <string>Voice transcription requires microphone access</string>
    </dict>
</plist>
```

### <a name="uwp"></a>UWP

El proyecto de ejemplo define una `IMicrophoneService` implementación de para UWP denominada `UWPMicrophoneService` :

```csharp
[assembly: Dependency(typeof(UWPMicrophoneService))]
namespace CognitiveSpeechService.UWP.Services
{
    public class UWPMicrophoneService : IMicrophoneService
    {
        public async Task<bool> GetPermissionAsync()
        {
            bool isMicAvailable = true;
            try
            {
                var mediaCapture = new MediaCapture();
                var settings = new MediaCaptureInitializationSettings();
                settings.StreamingCaptureMode = StreamingCaptureMode.Audio;
                await mediaCapture.InitializeAsync(settings);
            }
            catch(Exception ex)
            {
                isMicAvailable = false;
            }

            if(!isMicAvailable)
            {
                await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-microphone"));
            }

            return isMicAvailable;
        }

        public void OnRequestPermissionResult(bool isGranted)
        {
            // intentionally does nothing
        }
    }
}
```

`UWPMicrophoneService`Tiene las siguientes características:

1. El `Dependency` atributo registra la clase con `DependencyService` .
1. El `GetPermissionAsync` método intenta inicializar una `MediaCapture` instancia de. Si se produce un error, se inicia una solicitud de usuario para habilitar el micrófono.
1. El `OnRequestPermissionResult` método existe para satisfacer la interfaz, pero no es necesario para la implementación de UWP.

Por último, el **paquete de UWP. appxmanifest** debe especificar que la aplicación usa el micrófono. Haga doble clic en el archivo package. appxmanifest y seleccione la opción **micrófono** en la pestaña **capacidades** de Visual Studio 2019:

[![Captura de pantalla del manifiesto en Visual Studio 2019](speech-recognition-images/package-manifest-cropped.png)](speech-recognition-images/package-manifest.png#lightbox "Captura de pantalla del manifiesto en Visual Studio 2019")

## <a name="test-the-application"></a>Prueba de la aplicación

Ejecute la aplicación y haga clic en el botón **transcribir** . La aplicación debe solicitar acceso al micrófono e iniciar el proceso de transcripción. `ActivityIndicator`Se animará, mostrando que la transcripción está activa. A medida que hable, la aplicación transmitirá los datos de audio al recurso de Azure Speech Services, que responderá con el texto transformado. El texto `Label` transformado aparecerá en el elemento a medida que se reciba.

> [!NOTE]
> Los emuladores de Android no pueden cargar e inicializar las bibliotecas del servicio de voz. Se recomienda realizar pruebas en un dispositivo físico para la plataforma Android.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de servicio de voz de Azure](/samples/xamarin/xamarin-forms-samples/webservices-cognitivespeechservice)
- [Información general de Azure Speech Service](/azure/cognitive-services/speech-service/overview)
- [Cree un recurso de Cognitive Services](/azure/cognitive-services/cognitive-services-apis-create-account)
- [Inicio rápido: Reconocimiento de voz a través de un micrófono](/azure/cognitive-services/speech-service/quickstarts/speech-to-text-from-microphone)