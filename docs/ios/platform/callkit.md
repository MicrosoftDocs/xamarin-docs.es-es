---
title: CallKit en Xamarin. iOS
description: En este artículo se trata la nueva API de CallKit que Apple lanzó en iOS 10 y cómo implementarla en aplicaciones de VOIP. iOS VOIP.
ms.prod: xamarin
ms.assetid: 738A142D-FFD2-4738-B3ED-57C273179848
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/15/2017
ms.openlocfilehash: 0dd7736d37f6db17ad794258d9dff852b2745053
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431855"
---
# <a name="callkit-in-xamarinios"></a>CallKit en Xamarin. iOS

La nueva API de CallKit en iOS 10 proporciona una manera para que las aplicaciones de VOIP se integren con la interfaz de usuario de iPhone y proporcionen una interfaz y una experiencia familiares al usuario final. Con esta API, los usuarios pueden ver las llamadas VOIP e interactuar con ellas desde la pantalla de bloqueo del dispositivo iOS y administrar contactos con las vistas **Favoritos** y **recientes** de la aplicación de teléfono.

## <a name="about-callkit"></a>Acerca de CallKit

Según Apple, CallKit es un nuevo marco que elevará las aplicaciones de voz sobre IP (VOIP) de terceros a una experiencia de primera entidad en iOS 10. La API de CallKit permite que las aplicaciones VOIP se integren con la interfaz de usuario de iPhone y proporcionen una interfaz y una experiencia familiares al usuario final. Al igual que la aplicación de teléfono integrada, un usuario puede ver e interactuar con llamadas VOIP desde la pantalla de bloqueo del dispositivo iOS y administrar contactos mediante las vistas de **Favoritos** y **recientes** de la aplicación de teléfono.

Además, la API de CallKit ofrece la posibilidad de crear extensiones de aplicación que pueden asociar un número de teléfono con un nombre (identificador de llamada) o indicar al sistema Cuándo debe bloquearse un número (bloqueo de llamadas).

### <a name="the-existing-voip-app-experience"></a>La experiencia existente de la aplicación de VOIP

Antes de hablar sobre la nueva API de CallKit y sus capacidades, eche un vistazo a la experiencia del usuario actual con una aplicación de VOIP de terceros en iOS 9 (y menos) mediante una aplicación de VOIP ficticia denominada MonkeyCall. MonkeyCall es una aplicación sencilla que permite al usuario enviar y recibir llamadas VOIP mediante las API de iOS existentes.

Actualmente, si el usuario recibe una llamada entrante en MonkeyCall y su iPhone está bloqueado, la notificación recibida en la pantalla de bloqueo es indistinguible de cualquier otro tipo de notificación (por ejemplo, de los mensajes o las aplicaciones de correo electrónico).

Si el usuario deseara responder a la llamada, tendría que deslizar la notificación de MonkeyCall para abrir la aplicación y escribir su código de acceso (o User Touch ID) para desbloquear el teléfono antes de que pudiera aceptar la llamada e iniciar la conversación.

La experiencia es igualmente complicada si el teléfono está desbloqueado. Una vez más, la llamada entrante de MonkeyCall se muestra como un banner de notificación estándar que se desliza en la parte superior de la pantalla. Dado que la notificación es temporal, el usuario puede omitirla fácilmente. para ello, abra el centro de notificaciones y busque la notificación específica para responder y, a continuación, llame a o busque e inicie la aplicación MonkeyCall manualmente.

### <a name="the-callkit-voip-app-experience"></a>La experiencia de la aplicación CallKit VOIP

Mediante la implementación de las nuevas API de CallKit en la aplicación MonkeyCall, la experiencia del usuario con una llamada VOIP entrante se puede mejorar enormemente en iOS 10. Tome el ejemplo del usuario que recibe una llamada VOIP cuando su teléfono está bloqueado. Al implementar CallKit, la llamada aparecerá en la pantalla de bloqueo del iPhone, del mismo modo que si la llamada se estuviera recibiendo desde la aplicación de teléfono integrada, con la interfaz de usuario nativa y la funcionalidad de deslizamiento a respuesta estándar.

Una vez más, si el iPhone se desbloquea cuando se recibe una llamada de VOIP de MonkeyCall, se presenta la misma pantalla completa, la misma interfaz de usuario nativa y la funcionalidad de deslizar y rechazar estándar de la aplicación de teléfono integrada, y MonkeyCall tiene la opción de reproducir un tono personalizado.

CallKit proporciona funcionalidad adicional a MonkeyCall, lo que permite que las llamadas VOIP interactúen con otros tipos de llamadas, que aparecen en las listas recientes y favoritas integradas, para usar las características integradas no molestar y bloquear, iniciar llamadas MonkeyCall desde Siri y ofrece la capacidad de los usuarios de asignar MonkeyCall llamadas a personas en la aplicación contactos.

En las secciones siguientes se tratará la arquitectura de CallKit, los flujos de llamadas entrantes y salientes y la API de CallKit en detalle.

## <a name="the-callkit-architecture"></a>Arquitectura de CallKit

En iOS 10, Apple ha adoptado CallKit en todos los servicios del sistema, de modo que las llamadas realizadas en CarPlay, por ejemplo, son conocidas para la interfaz de usuario del sistema a través de CallKit. En el ejemplo que se muestra a continuación, dado que MonkeyCall adopta CallKit, el sistema lo conoce de la misma manera que estos servicios de sistema integrados y obtiene todas las mismas características:

[![La pila del servicio CallKit](callkit-images/callkit01.png)](callkit-images/callkit01.png#lightbox)

Eche un vistazo más de cerca a la aplicación MonkeyCall en el diagrama anterior. La aplicación contiene todo su código para comunicarse con su propia red y contiene sus propias interfaces de usuario. Se vincula en CallKit para comunicarse con el sistema:

[![Arquitectura de la aplicación MonkeyCall](callkit-images/callkit02.png)](callkit-images/callkit02.png#lightbox)

Hay dos interfaces principales en CallKit que usa la aplicación:

- `CXProvider` -Esto permite a la aplicación MonkeyCall informar al sistema de las notificaciones fuera de banda que puedan producirse.
- `CXCallController` : Permite a la aplicación MonkeyCall informar al sistema de las acciones del usuario local.

### <a name="the-cxprovider"></a>CXProvider

Como se indicó anteriormente, `CXProvider` permite a una aplicación informar al sistema de las notificaciones fuera de banda que puedan producirse. Se trata de notificaciones que no se producen debido a las acciones del usuario local, pero que se producen debido a eventos externos, como las llamadas entrantes.

Una aplicación debe usar `CXProvider` para lo siguiente:

- Notificar una llamada entrante al sistema.
- Notifica que una llamada saliente se ha conectado al sistema.
- Notificar al usuario remoto que finaliza la llamada al sistema.

Cuando la aplicación desea comunicarse con el sistema, usa la `CXCallUpdate` clase y, cuando el sistema necesita comunicarse con la aplicación, usa la `CXAction` clase:

[![Comunicación con el sistema a través de un CXProvider](callkit-images/callkit03.png)](callkit-images/callkit03.png#lightbox)

### <a name="the-cxcallcontroller"></a>CXCallController

`CXCallController`Permite a una aplicación informar al sistema de las acciones del usuario local, como el usuario que inicia una llamada VoIP. Al implementar una `CXCallController` aplicación, se obtiene la interacción con otros tipos de llamadas del sistema. Por ejemplo, si ya hay una llamada de telefonía activa en curso, `CXCallController` puede permitir que la aplicación VoIP Coloque esa llamada en espera e inicie o responda a una llamada de VoIP.

Una aplicación debe usar `CXCallController` para lo siguiente:

- Informe cuando el usuario haya iniciado una llamada saliente al sistema.
- Informa cuando el usuario responde a una llamada entrante al sistema.
- Informe cuando el usuario finaliza una llamada al sistema.

Cuando la aplicación desea comunicar las acciones del usuario local al sistema, usa la `CXTransaction` clase:

[![Informar al sistema mediante CXCallController](callkit-images/callkit04.png)](callkit-images/callkit04.png#lightbox)

## <a name="implementing-callkit"></a>Implementación de CallKit

En las secciones siguientes se muestra cómo implementar CallKit en una aplicación de VOIP. iOS VOIP. Por ejemplo, este documento utilizará el código de la aplicación ficticia MonkeyCall VOIP. El código que se presenta aquí representa varias clases auxiliares, las partes específicas de CallKit se tratarán en detalle en las secciones siguientes.

### <a name="the-activecall-class"></a>La clase ActiveCall

La `ActiveCall` aplicación MonkeyCall usa la clase para almacenar toda la información sobre una llamada VoIP que está activa actualmente de la manera siguiente:

```csharp
using System;
using CoreFoundation;
using Foundation;

namespace MonkeyCall
{
    public class ActiveCall
    {
        #region Private Variables
        private bool isConnecting;
        private bool isConnected;
        private bool isOnhold;
        #endregion

        #region Computed Properties
        public NSUuid UUID { get; set; }
        public bool isOutgoing { get; set; }
        public string Handle { get; set; }
        public DateTime StartedConnectingOn { get; set;}
        public DateTime ConnectedOn { get; set;}
        public DateTime EndedOn { get; set; }

        public bool IsConnecting {
            get { return isConnecting; }
            set {
                isConnecting = value;
                if (isConnecting) StartedConnectingOn = DateTime.Now;
                RaiseStartingConnectionChanged ();
            }
        }

        public bool IsConnected {
            get { return isConnected; }
            set {
                isConnected = value;
                if (isConnected) {
                    ConnectedOn = DateTime.Now;
                } else {
                    EndedOn = DateTime.Now;
                }
                RaiseConnectedChanged ();
            }
        }

        public bool IsOnHold {
            get { return isOnhold; }
            set {
                isOnhold = value;
            }
        }
        #endregion

        #region Constructors
        public ActiveCall ()
        {
        }

        public ActiveCall (NSUuid uuid, string handle, bool outgoing)
        {
            // Initialize
            this.UUID = uuid;
            this.Handle = handle;
            this.isOutgoing = outgoing;
        }
        #endregion

        #region Public Methods
        public void StartCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call starting successfully
            completionHandler (true);

            // Simulate making a starting and completing a connection
            DispatchQueue.MainQueue.DispatchAfter (new DispatchTime(DispatchTime.Now, 3000), () => {
                // Note that the call is starting
                IsConnecting = true;

                // Simulate pause before connecting
                DispatchQueue.MainQueue.DispatchAfter (new DispatchTime (DispatchTime.Now, 1500), () => {
                    // Note that the call has connected
                    IsConnecting = false;
                    IsConnected = true;
                });
            });
        }

        public void AnswerCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call being answered
            IsConnected = true;
            completionHandler (true);
        }

        public void EndCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call ending
            IsConnected = false;
            completionHandler (true);
        }
        #endregion

        #region Events
        public delegate void ActiveCallbackDelegate (bool successful);
        public delegate void ActiveCallStateChangedDelegate (ActiveCall call);

        public event ActiveCallStateChangedDelegate StartingConnectionChanged;
        internal void RaiseStartingConnectionChanged ()
        {
            if (this.StartingConnectionChanged != null) this.StartingConnectionChanged (this);
        }

        public event ActiveCallStateChangedDelegate ConnectedChanged;
        internal void RaiseConnectedChanged ()
        {
            if (this.ConnectedChanged != null) this.ConnectedChanged (this);
        }
        #endregion
    }
}
```

`ActiveCall` contiene varias propiedades que definen el estado de la llamada y dos eventos que se pueden generar cuando cambia el estado de la llamada. Puesto que esto es solo un ejemplo, hay tres métodos que se usan para simular el inicio, la respuesta y la finalización de una llamada.

### <a name="the-startcallrequest-class"></a>La clase StartCallRequest

La `StartCallRequest` clase estática proporciona algunos métodos auxiliares que se utilizarán al trabajar con llamadas salientes:

```csharp
using System;
using Foundation;
using Intents;

namespace MonkeyCall
{
    public static class StartCallRequest
    {
        public static string URLScheme {
            get { return "monkeycall"; }
        }

        public static string ActivityType {
            get { return INIntentIdentifier.StartAudioCall.GetConstant ().ToString (); }
        }

        public static string CallHandleFromURL (NSUrl url)
        {
            // Is this a MonkeyCall handle?
            if (url.Scheme == URLScheme) {
                // Yes, return host
                return url.Host;
            } else {
                // Not handled
                return null;
            }
        }

        public static string CallHandleFromActivity (NSUserActivity activity)
        {
            // Is this a start call activity?
            if (activity.ActivityType == ActivityType) {
                // Yes, trap any errors
                try {
                    // Get first contact
                    var interaction = activity.GetInteraction ();
                    var startAudioCallIntent = interaction.Intent as INStartAudioCallIntent;
                    var contact = startAudioCallIntent.Contacts [0];

                    // Get the person handle
                    return contact.PersonHandle.Value;
                } catch {
                    // Error, report null
                    return null;
                }
            } else {
                // Not handled
                return null;
            }
        }
    }
}
```

Las `CallHandleFromURL` `CallHandleFromActivity` clases y se usan en AppDelegate para obtener el identificador de contacto de la persona a la que se llama en una llamada saliente. Para obtener más información, consulte la sección [control de llamadas salientes](#handling-outgoing-calls) más adelante.

### <a name="the-activecallmanager-class"></a>La clase ActiveCallManager

La `ActiveCallManager` clase controla todas las llamadas abiertas en la aplicación MonkeyCall.

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using CallKit;

namespace MonkeyCall
{
    public class ActiveCallManager
    {
        #region Private Variables
        private CXCallController CallController = new CXCallController ();
        #endregion

        #region Computed Properties
        public List<ActiveCall> Calls { get; set; }
        #endregion

        #region Constructors
        public ActiveCallManager ()
        {
            // Initialize
            this.Calls = new List<ActiveCall> ();
        }
        #endregion

        #region Private Methods
        private void SendTransactionRequest (CXTransaction transaction)
        {
            // Send request to call controller
            CallController.RequestTransaction (transaction, (error) => {
                // Was there an error?
                if (error == null) {
                    // No, report success
                    Console.WriteLine ("Transaction request sent successfully.");
                } else {
                    // Yes, report error
                    Console.WriteLine ("Error requesting transaction: {0}", error);
                }
            });
        }
        #endregion

        #region Public Methods
        public ActiveCall FindCall (NSUuid uuid)
        {
            // Scan for requested call
            foreach (ActiveCall call in Calls) {
                if (call.UUID.Equals(uuid)) return call;
            }

            // Not found
            return null;
        }

        public void StartCall (string contact)
        {
            // Build call action
            var handle = new CXHandle (CXHandleType.Generic, contact);
            var startCallAction = new CXStartCallAction (new NSUuid (), handle);

            // Create transaction
            var transaction = new CXTransaction (startCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void EndCall (ActiveCall call)
        {
            // Build action
            var endCallAction = new CXEndCallAction (call.UUID);

            // Create transaction
            var transaction = new CXTransaction (endCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void PlaceCallOnHold (ActiveCall call)
        {
            // Build action
            var holdCallAction = new CXSetHeldCallAction (call.UUID, true);

            // Create transaction
            var transaction = new CXTransaction (holdCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void RemoveCallFromOnHold (ActiveCall call)
        {
            // Build action
            var holdCallAction = new CXSetHeldCallAction (call.UUID, false);

            // Create transaction
            var transaction = new CXTransaction (holdCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }
        #endregion
    }
}
```

De nuevo, como esto es solo una simulación, `ActiveCallManager` solo mantiene una colección de `ActiveCall` objetos y tiene una rutina para buscar una llamada determinada por su `UUID` propiedad. También incluye métodos para iniciar, finalizar y cambiar el estado de suspensión de una llamada saliente. Para obtener más información, consulte la sección [control de llamadas salientes](#handling-outgoing-calls) más adelante.

### <a name="the-providerdelegate-class"></a>La clase ProviderDelegate

Como se indicó anteriormente, un `CXProvider` proporciona una comunicación bidireccional entre la aplicación y el sistema para las notificaciones fuera de banda. El desarrollador debe proporcionar un personalizado `CXProviderDelegate` y asociarlo a `CXProvider` para que la aplicación administre los eventos de CallKit fuera de banda. MonkeyCall usa lo siguiente `CXProviderDelegate` :

```csharp
using System;
using Foundation;
using CallKit;
using UIKit;

namespace MonkeyCall
{
    public class ProviderDelegate : CXProviderDelegate
    {
        #region Computed Properties
        public ActiveCallManager CallManager { get; set;}
        public CXProviderConfiguration Configuration { get; set; }
        public CXProvider Provider { get; set; }
        #endregion

        #region Constructors
        public ProviderDelegate (ActiveCallManager callManager)
        {
            // Save connection to call manager
            CallManager = callManager;

            // Define handle types
            var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };

            // Get Image Template
            var templateImage = UIImage.FromFile ("telephone_receiver.png");

            // Setup the initial configurations
            Configuration = new CXProviderConfiguration ("MonkeyCall") {
                MaximumCallsPerCallGroup = 1,
                SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
                IconTemplateImageData = templateImage.AsPNG(),
                RingtoneSound = "musicloop01.wav"
            };

            // Create a new provider
            Provider = new CXProvider (Configuration);

            // Attach this delegate
            Provider.SetDelegate (this, null);

        }
        #endregion

        #region Override Methods
        public override void DidReset (CXProvider provider)
        {
            // Remove all calls
            CallManager.Calls.Clear ();
        }

        public override void PerformStartCallAction (CXProvider provider, CXStartCallAction action)
        {
            // Create new call record
            var activeCall = new ActiveCall (action.CallUuid, action.CallHandle.Value, true);

            // Monitor state changes
            activeCall.StartingConnectionChanged += (call) => {
                if (call.isConnecting) {
                    // Inform system that the call is starting
                    Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNSDate());
                }
            };

            activeCall.ConnectedChanged += (call) => {
                if (call.isConnected) {
                    // Inform system that the call has connected
                    provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNSDate ());
                }
            };

            // Start call
            activeCall.StartCall ((successful) => {
                // Was the call able to be started?
                if (successful) {
                    // Yes, inform the system
                    action.Fulfill ();

                    // Add call to manager
                    CallManager.Calls.Add (activeCall);
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformAnswerCallAction (CXProvider provider, CXAnswerCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Attempt to answer call
            call.AnswerCall ((successful) => {
                // Was the call successfully answered?
                if (successful) {
                    // Yes, inform system
                    action.Fulfill ();
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformEndCallAction (CXProvider provider, CXEndCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Attempt to answer call
            call.EndCall ((successful) => {
                // Was the call successfully answered?
                if (successful) {
                    // Remove call from manager's queue
                    CallManager.Calls.Remove (call);

                    // Yes, inform system
                    action.Fulfill ();
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformSetHeldCallAction (CXProvider provider, CXSetHeldCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Update hold status
            call.isOnHold = action.OnHold;

            // Inform system of success
            action.Fulfill ();
        }

        public override void TimedOutPerformingAction (CXProvider provider, CXAction action)
        {
            // Inform user that the action has timed out
        }

        public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
        {
            // Start the calls audio session here
        }

        public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
        {
            // End the calls audio session and restart any non-call
            // related audio
        }
        #endregion

        #region Public Methods
        public void ReportIncomingCall (NSUuid uuid, string handle)
        {
            // Create update to describe the incoming call and caller
            var update = new CXCallUpdate ();
            update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);

            // Report incoming call to system
            Provider.ReportNewIncomingCall (uuid, update, (error) => {
                // Was the call accepted
                if (error == null) {
                    // Yes, report to call manager
                    CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
                } else {
                    // Report error to user here
                    Console.WriteLine ("Error: {0}", error);
                }
            });
        }
        #endregion
    }
}
```

Cuando se crea una instancia de este delegado, se pasa el `ActiveCallManager` que usará para controlar cualquier actividad de llamada. A continuación, define los tipos de identificador ( `CXHandleType` ) a los que `CXProvider` responderá:

```csharp
// Define handle types
var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };
```

Y obtiene la imagen de plantilla que se aplicará al icono de la aplicación cuando se esté realizando una llamada:

```csharp
// Get Image Template
var templateImage = UIImage.FromFile ("telephone_receiver.png");
```

Estos valores se agrupan en un `CXProviderConfiguration` que se usará para configurar `CXProvider` :

```csharp
// Setup the initial configurations
Configuration = new CXProviderConfiguration ("MonkeyCall") {
    MaximumCallsPerCallGroup = 1,
    SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
    IconTemplateImageData = templateImage.AsPNG(),
    RingtoneSound = "musicloop01.wav"
};
```

A continuación, el delegado crea una nueva `CXProvider` con estas configuraciones y se adjunta a ella:

```csharp
// Create a new provider
Provider = new CXProvider (Configuration);

// Attach this delegate
Provider.SetDelegate (this, null);
```

Cuando se usa CallKit, la aplicación ya no creará y administrará sus propias sesiones de audio, sino que tendrá que configurar y usar una sesión de audio que el sistema creará y administrará para ella.

Si se trata de una aplicación real, `DidActivateAudioSession` se utilizaría el método para iniciar la llamada con un preconfigurado `AVAudioSession` que el sistema proporcionó:

```csharp
public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // Start the call's audio session here...
}
```

También usaría el `DidDeactivateAudioSession` método para finalizar y liberar su conexión a la sesión de audio proporcionada por el sistema:

```csharp
public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // End the calls audio session and restart any non-call
    // releated audio
}
```

El resto del código se tratará en detalle en las secciones siguientes.

### <a name="the-appdelegate-class"></a>La clase AppDelegate

MonkeyCall usa AppDelegate para contener instancias de `ActiveCallManager` y `CXProviderDelegate` que se usarán en toda la aplicación:

```csharp
using Foundation;
using UIKit;
using Intents;
using System;

namespace MonkeyCall
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        #region Constructors
        public override UIWindow Window { get; set; }
        public ActiveCallManager CallManager { get; set; }
        public ProviderDelegate CallProviderDelegate { get; set; }
        #endregion

        #region Override Methods
        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Initialize the call handlers
            CallManager = new ActiveCallManager ();
            CallProviderDelegate = new ProviderDelegate (CallManager);

            return true;
        }

        public override bool OpenUrl (UIApplication app, NSUrl url, NSDictionary options)
        {
            // Get handle from url
            var handle = StartCallRequest.CallHandleFromURL (url);

            // Found?
            if (handle == null) {
                // No, report to system
                Console.WriteLine ("Unable to get call handle from URL: {0}", url);
                return false;
            } else {
                // Yes, start call and inform system
                CallManager.StartCall (handle);
                return true;
            }
        }

        public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
        {
            var handle = StartCallRequest.CallHandleFromActivity (userActivity);

            // Found?
            if (handle == null) {
                // No, report to system
                Console.WriteLine ("Unable to get call handle from User Activity: {0}", userActivity);
                return false;
            } else {
                // Yes, start call and inform system
                CallManager.StartCall (handle);
                return true;
            }
        }

        ...
        #endregion
    }
}
```

Los `OpenUrl` `ContinueUserActivity` métodos de invalidación y se usan cuando la aplicación está procesando una llamada saliente. Para obtener más información, consulte la sección [control de llamadas salientes](#handling-outgoing-calls) más adelante.

## <a name="handling-incoming-calls"></a>Controlar las llamadas entrantes

Hay varios Estados y procesos en los que puede pasar una llamada de VOIP entrante durante un flujo de trabajo de llamada entrante típico, como:

- Informar al usuario (y al sistema) de que existe una llamada entrante.
- Recibir una notificación cuando el usuario desea responder a la llamada e inicializar la llamada con el otro usuario.
- Informe al sistema y a la red de comunicación cuando el usuario desea finalizar la llamada actual.

En las secciones siguientes se describe con detalle cómo una aplicación puede utilizar CallKit para controlar el flujo de trabajo de llamada entrante, de nuevo mediante la aplicación MonkeyCall VOIP como ejemplo.

### <a name="informing-user-of-incoming-call"></a>Informe al usuario de la llamada entrante

Cuando un usuario remoto inicia una conversación de VOIP con el usuario local, ocurre lo siguiente:

[![Un usuario remoto ha iniciado una conversación de VOIP](callkit-images/callkit05.png)](callkit-images/callkit05.png#lightbox)

1. La aplicación recibe una notificación de su red de comunicaciones en la que hay una llamada de VOIP entrante.
2. La aplicación usa `CXProvider` para enviar `CXCallUpdate` al sistema que informa de la llamada.
3. El sistema publica la llamada a la interfaz de usuario del sistema, los servicios del sistema y cualquier otra aplicación de VOIP mediante CallKit.

Por ejemplo, en `CXProviderDelegate` :

```csharp
public void ReportIncomingCall (NSUuid uuid, string handle)
{
    // Create update to describe the incoming call and caller
    var update = new CXCallUpdate ();
    update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);

    // Report incoming call to system
    Provider.ReportNewIncomingCall (uuid, update, (error) => {
        // Was the call accepted
        if (error == null) {
            // Yes, report to call manager
            CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
        } else {
            // Report error to user here
            Console.WriteLine ("Error: {0}", error);
        }
    });
}
```

Este código crea una nueva `CXCallUpdate` instancia y le asocia un identificador que identificará al llamador. A continuación, usa el `ReportNewIncomingCall` método de la `CXProvider` clase para informar al sistema de la llamada. Si se realiza correctamente, se agrega la llamada a la colección de llamadas activas de la aplicación, si no es así, se debe informar del error al usuario.

### <a name="user-answering-incoming-call"></a>Llamada entrante de respuesta del usuario

Si el usuario desea responder a la llamada de VOIP entrante, ocurre lo siguiente:

[![El usuario responde a la llamada de VOIP entrante](callkit-images/callkit06.png)](callkit-images/callkit06.png#lightbox)

1. La interfaz de usuario del sistema informa al sistema de que el usuario desea responder a la llamada de VOIP.
2. El sistema envía un `CXAnswerCallAction` a la aplicación que `CXProvider` le informa del intento de respuesta.
3. La aplicación informa a su red de comunicación de que el usuario está respondiendo a la llamada y la llamada de VOIP continúa como de costumbre.

Por ejemplo, en `CXProviderDelegate` :

```csharp
public override void PerformAnswerCallAction (CXProvider provider, CXAnswerCallAction action)
{
    // Find requested call
    var call = CallManager.FindCall (action.CallUuid);

    // Found?
    if (call == null) {
        // No, inform system and exit
        action.Fail ();
        return;
    }

    // Attempt to answer call
    call.AnswerCall ((successful) => {
        // Was the call successfully answered?
        if (successful) {
            // Yes, inform system
            action.Fulfill ();
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

Este código busca primero la llamada determinada en su lista de llamadas activas. Si no se puede encontrar la llamada, se notifica al sistema y el método se cierra. Si se encuentra, `AnswerCall` se llama al método de la `ActiveCall` clase para iniciar la llamada y el sistema es información si se realiza correctamente o produce un error.

### <a name="user-ending-incoming-call"></a>Llamada entrante de finalización de usuario

Si el usuario desea finalizar la llamada desde la interfaz de usuario de la aplicación, ocurre lo siguiente:

[![El usuario finaliza la llamada desde la interfaz de usuario de la aplicación.](callkit-images/callkit07.png)](callkit-images/callkit07.png#lightbox)

1. La aplicación crea `CXEndCallAction` que se agrupa en un `CXTransaction` que se envía al sistema para informarle de que la llamada está finalizando.
2. El sistema comprueba el intento de finalización de la llamada y envía de `CXEndCallAction` nuevo a la aplicación a través de `CXProvider` .
3. A continuación, la aplicación informa a su red de comunicación de que la llamada está finalizando.

Por ejemplo, en `CXProviderDelegate` :

```csharp
public override void PerformEndCallAction (CXProvider provider, CXEndCallAction action)
{
    // Find requested call
    var call = CallManager.FindCall (action.CallUuid);

    // Found?
    if (call == null) {
        // No, inform system and exit
        action.Fail ();
        return;
    }

    // Attempt to answer call
    call.EndCall ((successful) => {
        // Was the call successfully answered?
        if (successful) {
            // Remove call from manager's queue
            CallManager.Calls.Remove (call);

            // Yes, inform system
            action.Fulfill ();
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

Este código busca primero la llamada determinada en su lista de llamadas activas. Si no se puede encontrar la llamada, se notifica al sistema y el método se cierra. Si se encuentra, `EndCall` se llama al método de la `ActiveCall` clase para finalizar la llamada y el sistema es información si se realiza correctamente o produce un error. Si se realiza correctamente, la llamada se quita de la colección de llamadas activas.

## <a name="managing-multiple-calls"></a>Administrar varias llamadas

La mayoría de las aplicaciones de VOIP pueden controlar varias llamadas a la vez. Por ejemplo, si actualmente hay una llamada de VOIP activa y la aplicación recibe una notificación de que hay una nueva llamada entrante, el usuario puede pausarse o colgarse en la primera llamada para responder a la segunda.

En la situación anterior, el sistema enviará un `CXTransaction` a la aplicación que incluirá una lista de varias acciones (como `CXEndCallAction` y `CXAnswerCallAction` ). Todas estas acciones deberán cumplirse individualmente para que el sistema pueda actualizar la interfaz de usuario de forma adecuada.

## <a name="handling-outgoing-calls"></a>Control de llamadas salientes

Si el usuario pulsa una entrada de la lista de recientes (en la aplicación de teléfono), por ejemplo, que proviene de una llamada que pertenece a la aplicación, se le enviará una _llamada de inicio_ que intentará el sistema:

[![Recibir un intento de llamada de inicio](callkit-images/callkit08.png)](callkit-images/callkit08.png#lightbox)

1. La aplicación creará una _acción de inicio de llamada_ basada en el intento de llamada de inicio que recibió del sistema.
2. La aplicación usará `CXCallController` para solicitar la acción iniciar llamada del sistema.
3. Si el sistema acepta la acción, se devolverá a la aplicación a través del `XCProvider` delegado.
4. La aplicación inicia la llamada saliente con su red de comunicación.

Para obtener más información sobre los intentos, consulte nuestra documentación sobre las extensiones de la interfaz de usuario de intents [y intents](~/ios/platform/sirikit/understanding-sirikit.md) .

### <a name="the-outgoing-call-lifecycle"></a>Ciclo de vida de la llamada saliente

Cuando se trabaja con CallKit y una llamada saliente, la aplicación necesitará informar al sistema de los siguientes eventos de ciclo de vida:

1. **Inicio** : informe al sistema de que una llamada saliente está a punto de iniciarse.
2. **Iniciado** : informa al sistema de que se ha iniciado una llamada saliente.
3. **Conexión** : informar al sistema de que la llamada saliente se está conectando.
4. **Conectado** : informe de que la llamada saliente está conectada y que ambas partes pueden hablar ahora.

Por ejemplo, el código siguiente iniciará una llamada saliente:

```csharp
private CXCallController CallController = new CXCallController ();
...

private void SendTransactionRequest (CXTransaction transaction)
{
    // Send request to call controller
    CallController.RequestTransaction (transaction, (error) => {
        // Was there an error?
        if (error == null) {
            // No, report success
            Console.WriteLine ("Transaction request sent successfully.");
        } else {
            // Yes, report error
            Console.WriteLine ("Error requesting transaction: {0}", error);
        }
    });
}

public void StartCall (string contact)
{
    // Build call action
    var handle = new CXHandle (CXHandleType.Generic, contact);
    var startCallAction = new CXStartCallAction (new NSUuid (), handle);

    // Create transaction
    var transaction = new CXTransaction (startCallAction);

    // Inform system of call request
    SendTransactionRequest (transaction);
}
```

Crea un `CXHandle` y lo usa para configurar un que `CXStartCallAction` se incluye en un `CXTransaction` que se envía al sistema mediante el `RequestTransaction` método de la `CXCallController` clase. Al llamar al `RequestTransaction` método, el sistema puede poner cualquier llamada existente en espera, independientemente del origen (aplicación de teléfono, FaceTime, VoIP, etc.), antes de que se inicie la nueva llamada.

La solicitud para iniciar una llamada de VOIP saliente puede proviene de varios orígenes diferentes, como Siri, una entrada en una tarjeta de contacto (en la aplicación de contactos) o desde la lista de recientes (en la aplicación de teléfono). En estas situaciones, se enviará a la aplicación un intento de llamada de inicio dentro de `NSUserActivity` y el AppDelegate deberá controlarla:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    var handle = StartCallRequest.CallHandleFromActivity (userActivity);

    // Found?
    if (handle == null) {
        // No, report to system
        Console.WriteLine ("Unable to get call handle from User Activity: {0}", userActivity);
        return false;
    } else {
        // Yes, start call and inform system
        CallManager.StartCall (handle);
        return true;
    }
}
```

Aquí `CallHandleFromActivity` se usa el método de la clase auxiliar `StartCallRequest` para obtener el identificador de la persona a la que se llama (vea [la clase StartCallRequest](#the-startcallrequest-class) anterior).

El `PerformStartCallAction` método de la [clase ProviderDelegate](#the-providerdelegate-class) se usa para, por último, iniciar la llamada de salida real e informar al sistema de su ciclo de vida:

```csharp
public override void PerformStartCallAction (CXProvider provider, CXStartCallAction action)
{
    // Create new call record
    var activeCall = new ActiveCall (action.CallUuid, action.CallHandle.Value, true);

    // Monitor state changes
    activeCall.StartingConnectionChanged += (call) => {
        if (call.IsConnecting) {
            // Inform system that the call is starting
            Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNSDate());
        }
    };

    activeCall.ConnectedChanged += (call) => {
        if (call.IsConnected) {
            // Inform system that the call has connected
            Provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNSDate ());
        }
    };

    // Start call
    activeCall.StartCall ((successful) => {
        // Was the call able to be started?
        if (successful) {
            // Yes, inform the system
            action.Fulfill ();

            // Add call to manager
            CallManager.Calls.Add (activeCall);
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

Crea una instancia de la `ActiveCall` clase (para contener información sobre la llamada en curso) y se rellena con la persona a la que se llama. Los `StartingConnectionChanged` `ConnectedChanged` eventos y se usan para supervisar y notificar el ciclo de vida de la llamada saliente. La llamada se inicia y el sistema informó de que se cumplió la acción.

### <a name="ending-an-outgoing-call"></a>Finalización de una llamada saliente

Cuando el usuario ha terminado de realizar una llamada de salida y desea finalizarla, se puede usar el código siguiente:

```csharp
private CXCallController CallController = new CXCallController ();
...

private void SendTransactionRequest (CXTransaction transaction)
{
    // Send request to call controller
    CallController.RequestTransaction (transaction, (error) => {
        // Was there an error?
        if (error == null) {
            // No, report success
            Console.WriteLine ("Transaction request sent successfully.");
        } else {
            // Yes, report error
            Console.WriteLine ("Error requesting transaction: {0}", error);
        }
    });
}

public void EndCall (ActiveCall call)
{
    // Build action
    var endCallAction = new CXEndCallAction (call.UUID);

    // Create transaction
    var transaction = new CXTransaction (endCallAction);

    // Inform system of call request
    SendTransactionRequest (transaction);
}
```

Si crea un `CXEndCallAction` con el UUID de la llamada al final, lo empaqueta en un `CXTransaction` que se envía al sistema mediante el `RequestTransaction` método de la `CXCallController` clase.

## <a name="additional-callkit-details"></a>Detalles adicionales de CallKit

En esta sección se tratan algunos detalles adicionales que el desarrollador tendrá que tener en cuenta al trabajar con CallKit como:

- Configuración de proveedor
- Errores de acción
- Restricciones del sistema
- Audio VOIP

### <a name="provider-configuration"></a>Configuración de proveedor

La configuración del proveedor permite a una aplicación de VOIP de iOS 10 personalizar la experiencia del usuario (dentro de la interfaz de usuario de llamada nativa) al trabajar con CallKit.

Una aplicación puede hacer los siguientes tipos de personalizaciones:

- Mostrar un nombre traducido.
- Habilitar la compatibilidad con llamadas de vídeo.
- Personalice los botones en la interfaz de usuario de llamada mediante la presentación de su propio icono de imagen de plantilla. La interacción del usuario con los botones personalizados se envía directamente a la aplicación que se va a procesar.

### <a name="action-errors"></a>Errores de acción

las aplicaciones de VOIP de iOS 10 que usan CallKit deben controlar las acciones con errores y mantener el usuario informado del estado de la acción en todo momento.

Tenga en cuenta el siguiente ejemplo:

1. La aplicación ha recibido una acción iniciar llamada y ha comenzado el proceso de inicialización de una nueva llamada VOIP con su red de comunicación.
2. Debido a la capacidad de comunicación de red limitada o no, se produce un error en esta conexión.
3. La aplicación *debe* enviar el mensaje de **error** de nuevo a la acción iniciar llamada ( `Action.Fail()` ) para informar al sistema del error.
4. Esto permite al sistema informar al usuario del estado de la llamada. Por ejemplo, para mostrar la interfaz de usuario de error de llamada.

Además, una aplicación de VOIP de iOS 10 tendrá que responder a los _errores de tiempo de espera_ que se pueden producir cuando una acción esperada no se puede procesar en un período de tiempo determinado. Cada tipo de acción proporcionado por CallKit tiene un valor de tiempo de espera máximo asociado a él. Estos valores de tiempo de espera garantizan que las acciones CallKit solicitadas por el usuario se controlan de forma eficaz, manteniendo el fluido del sistema operativo y respondiendo también.

Hay varios métodos en el delegado de proveedor ( `CXProviderDelegate` ) que deben invalidarse para controlar correctamente también estas situaciones de tiempo de espera.

### <a name="system-restrictions"></a>Restricciones del sistema

Según el estado actual del dispositivo iOS que ejecuta la aplicación iOS 10 VOIP, se pueden aplicar ciertas restricciones del sistema.

Por ejemplo, el sistema puede restringir una llamada de VOIP entrante si:

1. La persona que llama se encuentra en la lista de llamadores bloqueados del usuario.
2. El dispositivo iOS del usuario se encuentra en el modo do-not-molestar.

Si una llamada VOIP está restringida por cualquiera de estas situaciones, use el código siguiente para controlarla:

```csharp
public class ProviderDelegate : CXProviderDelegate
{
...

    public void ReportIncomingCall (NSUuid uuid, string handle)
    {
        // Create update to describe the incoming call and caller
        var update = new CXCallUpdate ();
        update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);

        // Report incoming call to system
        Provider.ReportNewIncomingCall (uuid, update, (error) => {
            // Was the call accepted
            if (error == null) {
                // Yes, report to call manager
                CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
            } else {
                // Report error to user here
                if (error.Code == (int)CXErrorCodeIncomingCallError.CallUuidAlreadyExists) {
                    // Handle duplicate call ID
                } else if (error.Code == (int)CXErrorCodeIncomingCallError.FilteredByBlockList) {
                    // Handle call from blocked user
                } else if (error.Code == (int)CXErrorCodeIncomingCallError.FilteredByDoNotDisturb) {
                    // Handle call while in do-not-disturb mode
                } else {
                    // Handle unknown error
                }
            }
        });
    }

}
```

### <a name="voip-audio"></a>Audio VOIP

CallKit proporciona varias ventajas para controlar los recursos de audio que necesitará una aplicación de VOIP de iOS 10 durante una llamada de VOIP en vivo. Una de las mayores ventajas es que la sesión de audio de la aplicación tendrá prioridades elevadas cuando se ejecute en iOS 10. Este es el mismo nivel de prioridad que las aplicaciones de teléfono y FaceTime integradas y este nivel de prioridad mejorada impedirá que otras aplicaciones en ejecución interrumpan la sesión de audio de la aplicación VOIP.

Además, CallKit tiene acceso a otras sugerencias de enrutamiento de audio que pueden mejorar el rendimiento y enrutar de forma inteligente el audio VOIP a dispositivos de salida específicos durante una llamada activa basada en las preferencias del usuario y los Estados de los dispositivos. Por ejemplo, en función de dispositivos conectados como auriculares Bluetooth, conexión CarPlay dinámica o configuración de accesibilidad.

Durante el ciclo de vida de una llamada de VOIP típica mediante CallKit, la aplicación tendrá que configurar la secuencia de audio que CallKit le proporcionará. Eche un vistazo al ejemplo siguiente:

[![La secuencia de acción de inicio de llamada](callkit-images/callkit09.png)](callkit-images/callkit09.png#lightbox)

1. La aplicación recibe una acción de llamada de inicio para responder a una llamada entrante.
2. Antes de que la aplicación cumpla esta acción, proporciona la configuración necesaria para su `AVAudioSession` .
3. La aplicación informa al sistema de que se ha realizado la acción.
4. Antes de que se conecte la llamada, CallKit proporciona una prioridad alta `AVAudioSession` que coincide con la configuración que solicitó la aplicación. Se notificará a la aplicación mediante el `DidActivateAudioSession` método de su `CXProviderDelegate` .

## <a name="working-with-call-directory-extensions"></a>Trabajar con extensiones de directorio de llamadas

Al trabajar con CallKit, _las extensiones de directorio de llamadas_ proporcionan una manera de agregar números de llamadas bloqueadas e identificar números que son específicos de una aplicación de VoIP determinada en contactos en la aplicación de contacto en el dispositivo iOS.

### <a name="implementing-a-call-directory-extension"></a>Implementar una extensión de directorio de llamadas

Para implementar una extensión de directorio de llamadas en una aplicación de Xamarin. iOS, haga lo siguiente:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Abra la solución de la aplicación en Visual Studio para Mac.
2. Haga clic con el botón derecho en el nombre de la solución en el **Explorador de soluciones** y seleccione **Agregar**  >  **Agregar nuevo proyecto**.
3. Seleccione **iOS**  >  **extensiones**  >  de iOS**extensiones de directorio de llamadas** y haga clic en el botón **siguiente** :

    [![Crear una nueva extensión de directorio de llamadas](callkit-images/calldir01.png)](callkit-images/calldir01.png#lightbox)
4. Escriba un **nombre** para la extensión y haga clic en el botón **siguiente** :

    [![Escribir un nombre para la extensión](callkit-images/calldir02.png)](callkit-images/calldir02.png#lightbox)
5. Ajuste el **nombre del proyecto** o el **nombre** de la solución si es necesario y haga clic en el botón **crear** :

    [![Creación del proyecto](callkit-images/calldir03.png)](callkit-images/calldir03.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Abra la solución de la aplicación en Visual Studio.
2. Haga clic con el botón derecho en el nombre de la solución en el **Explorador de soluciones** y seleccione **Agregar**  >  **Agregar nuevo proyecto**.
3. Seleccione **iOS**  >  **extensiones**  >  de iOS**extensiones de directorio de llamadas** y haga clic en el botón **siguiente** :

    [![Crear una nueva extensión de directorio de llamadas](callkit-images/calldir01w.png)](callkit-images/calldir01.png#lightbox)
4. Escriba un **nombre** para la extensión y haga clic en el botón **Aceptar**

-----

Esto agregará una `CallDirectoryHandler.cs` clase al proyecto que tendrá el siguiente aspecto:

```csharp
using System;

using Foundation;
using CallKit;

namespace MonkeyCallDirExtension
{
    [Register ("CallDirectoryHandler")]
    public class CallDirectoryHandler : CXCallDirectoryProvider, ICXCallDirectoryExtensionContextDelegate
    {
        #region Constructors
        protected CallDirectoryHandler (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void BeginRequest (CXCallDirectoryExtensionContext context)
        {
            context.Delegate = this;

            if (!AddBlockingPhoneNumbers (context)) {
                Console.WriteLine ("Unable to add blocking phone numbers");
                var error = new NSError (new NSString ("CallDirectoryHandler"), 1, null);
                context.CancelRequest (error);
                return;
            }

            if (!AddIdentificationPhoneNumbers (context)) {
                Console.WriteLine ("Unable to add identification phone numbers");
                var error = new NSError (new NSString ("CallDirectoryHandler"), 2, null);
                context.CancelRequest (error);
                return;
            }

            context.CompleteRequest (null);
        }
        #endregion

        #region Private Methods
        private bool AddBlockingPhoneNumbers (CXCallDirectoryExtensionContext context)
        {
            // Retrieve phone numbers to block from data store. For optimal performance and memory usage when there are many phone numbers,
            // consider only loading a subset of numbers at a given time and using autorelease pool(s) to release objects allocated during each batch of numbers which are loaded.
            //
            // Numbers must be provided in numerically ascending order.

            long [] phoneNumbers = { 14085555555, 18005555555 };

            foreach (var phoneNumber in phoneNumbers)
                context.AddBlockingEntry (phoneNumber);

            return true;
        }

        private bool AddIdentificationPhoneNumbers (CXCallDirectoryExtensionContext context)
        {
            // Retrieve phone numbers to identify and their identification labels from data store. For optimal performance and memory usage when there are many phone numbers,
            // consider only loading a subset of numbers at a given time and using autorelease pool(s) to release objects allocated during each batch of numbers which are loaded.
            //
            // Numbers must be provided in numerically ascending order.

            long [] phoneNumbers = { 18775555555, 18885555555 };
            string [] labels = { "Telemarketer", "Local business" };

            for (var i = 0; i < phoneNumbers.Length; i++) {
                long phoneNumber = phoneNumbers [i];
                string label = labels [i];
                context.AddIdentificationEntry (phoneNumber, label);
            }

            return true;
        }
        #endregion

        #region Public Methods
        public void RequestFailed (CXCallDirectoryExtensionContext extensionContext, NSError error)
        {
            // An error occurred while adding blocking or identification entries, check the NSError for details.
            // For Call Directory error codes, see the CXErrorCodeCallDirectoryManagerError enum.
            //
            // This may be used to store the error details in a location accessible by the extension's containing app, so that the
            // app may be notified about errors which occurred while loading data even if the request to load data was initiated by
            // the user in Settings instead of via the app itself.
        }
        #endregion
    }
}
```

El `BeginRequest` método del controlador de directorio de llamada deberá modificarse para proporcionar la funcionalidad necesaria. En el caso del ejemplo anterior, intenta establecer la lista de números bloqueados y disponibles en la base de datos de contactos de la aplicación VOIP. Si se produce un error en alguna de las solicitudes por cualquier motivo, cree un `NSError` para describir el error y pásele el `CancelRequest` método de la `CXCallDirectoryExtensionContext` clase.

Para establecer los números bloqueados, use el `AddBlockingEntry` método de la `CXCallDirectoryExtensionContext` clase. Los números que se proporcionan al método _deben_ estar en orden ascendente. Para obtener un rendimiento óptimo y el uso de memoria cuando hay muchos números de teléfono, considere la posibilidad de cargar solo un subconjunto de números en un momento dado y el uso de grupos de liberación parcial para liberar objetos asignados durante cada lote de números que se cargan.

Para informar a la aplicación de los números de contacto conocidos en la aplicación de VOIP, use el `AddIdentificationEntry` método de la `CXCallDirectoryExtensionContext` clase y proporcione tanto el número como una etiqueta de identificación. De nuevo, los números proporcionados al método _deben_ estar en orden ascendente. Para obtener un rendimiento óptimo y el uso de memoria cuando hay muchos números de teléfono, considere la posibilidad de cargar solo un subconjunto de números en un momento dado y el uso de grupos de liberación parcial para liberar objetos asignados durante cada lote de números que se cargan.

## <a name="summary"></a>Resumen

En este artículo se ha explicado la nueva API de CallKit que Apple lanzó en iOS 10 y cómo implementarla en aplicaciones de VOIP de Xamarin. iOS. Ha mostrado cómo CallKit permite que una aplicación se integre en el sistema iOS, cómo proporciona paridad de características con aplicaciones integradas (como teléfono) y cómo aumenta la visibilidad de una aplicación en iOS en ubicaciones como las pantallas de bloqueo y de inicio, a través de interacciones de Siri y a través de las aplicaciones de contactos.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)