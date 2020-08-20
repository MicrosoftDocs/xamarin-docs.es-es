---
title: Envío y recepción de notificaciones push con Azure Notification Hubs y Xamarin.Forms
description: En este artículo se explica cómo usar Azure Notification Hubs para enviar notificaciones push multiplataforma a aplicaciones de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 07D13195-3A0D-4C95-ACF0-143A9084973C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/27/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
- Firebase
ms.openlocfilehash: 5a069595aa12852a4a861de9bac52eebd7247e48
ms.sourcegitcommit: 14d67a2db82e67471584b1749e0d5b9ec0c0c09b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2020
ms.locfileid: "88228630"
---
# <a name="send-and-receive-push-notifications-with-azure-notification-hubs-and-no-locxamarinforms"></a>Envío y recepción de notificaciones push con Azure Notification Hubs y Xamarin.Forms

[![Descarga de ejemplo](~/media/shared/download.png)Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azurenotificationhub/)

Las notificaciones push entregan información de un sistema back-end a una aplicación móvil. Apple, Google y otras plataformas tienen su propio servicio de notificaciones push (PNS). Azure Notification Hubs permite centralizar las notificaciones multiplataforma para que la aplicación de back-end pueda comunicarse con un solo centro de conectividad, que se encarga de distribuir las notificaciones al PNS específico de cada plataforma.

Integre Azure Notification Hubs en las aplicaciones móviles con estos pasos:

1. [Configure los servicios de notificaciones push y el centro de notificaciones de Azure](#set-up-push-notification-services-and-azure-notification-hub).
1. [Entienda como se usan las plantillas y las etiquetas](#register-templates-and-tags-with-the-azure-notification-hub).
1. [Cree una aplicación de Xamarin.Forms multiplataforma](#xamarinforms-application-functionality).
1. [Configure el proyecto nativo de Android para notificaciones push](#configure-the-android-application-for-notifications).
1. [Configure el proyecto nativo de iOS para notificaciones push](#configure-ios-for-notifications).
1. [Pruebe notificaciones mediante el centro de notificaciones de Azure](#test-notifications-in-the-azure-portal).
1. [Cree una aplicación de back-end para enviar notificaciones](#create-a-notification-dispatcher).

> [!NOTE]
> Si no tiene una [suscripción a Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), cree una [cuenta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de empezar.

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Configuración de los servicios de notificaciones push y el centro de notificaciones de Azure

La integración de Azure Notification Hubs con una aplicación móvil de Xamarin.Forms es similar a la integración de Azure Notification Hubs con una aplicación nativa de Xamarin. Configure una aplicación de Firebase Cloud Messaging (FCM) siguiendo los pasos de la consola de Firebase que se detallan en [Envío de notificaciones push a Xamarin.Android mediante Azure Notification Hubs](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm#create-a-firebase-project-and-enable-firebase-cloud-messaging). Realice los pasos siguientes con el tutorial de Xamarin.Android:

1. Defina un nombre de paquete de Android como `com.xamarin.notifysample`, que se usa en el ejemplo.
1. Descargue `google-services.json` de la consola de Firebase. En un paso posterior se agrega este archivo a la aplicación Android.
1. Cree una instancia del centro de notificaciones de Azure y asígnele un nombre. En este artículo y este ejemplo se usa `xdocsnotificationhub` como nombre del centro de conectividad.
1. Copie la **Clave de servidor** de FCM y guárdela como **Clave de API** en **Google (GCM/FCM)** en el centro de notificaciones de Azure.

En la captura de pantalla siguiente se muestra la configuración de la plataforma Google en el centro de notificaciones de Azure:

![Captura de pantalla de la configuración de Google del centro de notificaciones de Azure](azure-notification-hub-images/fcm-notification-hub-config.png "Configuración de Google del centro de notificaciones de Azure")

Necesita un equipo macOS para finalizar la configuración de dispositivos iOS. Configure Apple Push Notification Service (APN) siguiendo los pasos iniciales que se detallan en [Envío de notificaciones push a Xamarin.iOS mediante Azure Notification Hubs](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started#generate-the-certificate-signing-request-file). Realice los pasos siguientes con el tutorial de Xamarin.iOS:

1. Defina un identificador de agrupación de iOS. En este artículo y este ejemplo se usa `com.xamarin.notifysample` como identificador de agrupación.
1. Cree un archivo de solicitud de firma de certificado (CSR) y úselo para generar un certificado de notificación push.
1. Cargue el certificado de notificación push en **Apple (APNS)** en el centro de notificaciones de Azure.

En la captura de pantalla siguiente se muestra la configuración de la plataforma Apple en el centro de notificaciones de Azure:

![Captura de pantalla de la configuración de Apple del centro de notificaciones de Azure](azure-notification-hub-images/apns-notification-hub-config.png "Configuración de Apple del centro de notificaciones de Azure")

## <a name="register-templates-and-tags-with-the-azure-notification-hub"></a>Registro de plantillas y etiquetas en el centro de notificaciones de Azure

El centro de notificaciones de Azure necesita que las aplicaciones móviles se registren en el centro de conectividad, definan plantillas y se suscriban a etiquetas. El registro vincula un identificador de PNS específico de la plataforma a un identificador del centro de notificaciones de Azure. Para obtener más información sobre los registros, vea [Administración de registros](/azure/notification-hubs/notification-hubs-push-notification-registration-management).

Las plantillas permiten a los dispositivos especificar plantillas de mensajes con parámetros. Los mensajes entrantes se pueden personalizar por dispositivo y por etiqueta. Para obtener más información sobre las plantillas, vea [Plantillas](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).

Las etiquetas se pueden usar para suscribirse a categorías de mensajes, como noticias, deportes y el tiempo. Para simplificar, la aplicación de ejemplo define una plantilla predeterminada con un solo parámetro denominado `messageParam` y una sola etiqueta denominada `default`. En sistemas más complejos, se pueden usar etiquetas específicas del usuario para enviar mensajes a un usuario entre dispositivos con notificaciones personalizadas. Para obtener más información sobre las etiquetas, vea [Expresiones de etiqueta y enrutamiento](/azure/notification-hubs/notification-hubs-tags-segment-push-message).

Para recibir mensajes correctamente, cada aplicación nativa debe realizar estos pasos:

1. Obtener un identificador de PNS o un token del PNS de la plataforma.
1. Registrar el identificador de PNS en el centro de notificaciones de Azure.
1. Especificar una plantilla que contenga los mismos parámetros que los mensajes salientes.
1. Suscribirse a la etiqueta de destino de los mensajes salientes.

Estos pasos se explican más detalladamente para cada plataforma en las secciones [Configuración de la aplicación Android para recibir notificaciones](#configure-the-android-application-for-notifications) y [Configuración de iOS para recibir notificaciones](#configure-ios-for-notifications).

## <a name="no-locxamarinforms-application-functionality"></a>Funcionalidad de la aplicación de Xamarin.Forms

La aplicación de ejemplo de Xamarin.Forms muestra una lista de mensajes de notificación push. Esto se logra con el método `AddMessage`, que agrega el mensaje de notificación push especificado a la interfaz de usuario. Además, este método evita que se agreguen mensajes duplicados a la interfaz de usuario y se ejecuta en el subproceso principal para que se pueda llamar desde cualquier subproceso. En el código siguiente se muestra el método `AddMessage`:

```csharp
public void AddMessage(string message)
{
    Device.BeginInvokeOnMainThread(() =>
    {
        if (messageDisplay.Children.OfType<Label>().Where(c => c.Text == message).Any())
        {
            // Do nothing, an identical message already exists
        }
        else
        {
            Label label = new Label()
            {
                Text = message,
                HorizontalOptions = LayoutOptions.CenterAndExpand,
                VerticalOptions = LayoutOptions.Start
            };
            messageDisplay.Children.Add(label);
        }
    });
}
```

La aplicación de ejemplo contiene un archivo `AppConstants.cs`, que define las propiedades que usan los proyectos de la plataforma. Este archivo debe personalizarse con los valores del centro de notificaciones de Azure. En el código siguiente, se muestra el archivo `AppConstants.cs`:

```csharp
public static class AppConstants
{
    public static string NotificationChannelName { get; set; } = "XamarinNotifyChannel";
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string ListenConnectionString { get; set; } = "< Insert your DefaultListenSharedAccessSignature >";
    public static string DebugTag { get; set; } = "XamarinNotify";
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string FCMTemplateBody { get; set; } = "{\"data\":{\"message\":\"$(messageParam)\"}}";
    public static string APNTemplateBody { get; set; } = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";
}
```

Personalice los valores siguientes de `AppConstants` para conectar la aplicación de ejemplo al centro de notificaciones de Azure:

* `NotificationHubName`: use el nombre del centro de notificaciones de Azure que ha creado en Azure Portal.
* `ListenConnectionString`: este valor se encuentra en el centro de notificaciones de Azure, en **Directivas de acceso**.

La captura de pantalla siguiente muestra dónde se encuentran estos valores en Azure Portal:

![Captura de pantalla de la directiva de acceso del centro de notificaciones de Azure](azure-notification-hub-images/notification-hub-access-policy.png "Directiva de acceso del centro de notificaciones de Azure")

## <a name="configure-the-android-application-for-notifications"></a>Configuración de la aplicación Android para recibir notificaciones

Realice los pasos siguientes para configurar la aplicación Android a fin de recibir y procesar notificaciones:

1. Configure el **Nombre del paquete** de Android para que coincida con el nombre del paquete de la consola de Firebase.
1. Instale los siguientes paquetes NuGet para interactuar con Google Play, Firebase y Azure Notification Hubs:
    1. `Xamarin.GooglePlayServices.Base`
    1. `Xamarin.Firebase.Messaging`
    1. `Xamarin.Azure.NotificationHubs.Android`
1. Copie el archivo `google-services.json` que ha descargado durante la instalación de FCM en el proyecto y establezca la acción de compilación en `GoogleServicesJson`.
1. [Configure](#configure-android-manifest) `AndroidManifest.xml` para comunicarse con Firebase.
1. [Invalide](#override-firebasemessagingservice-to-handle-messages) `FirebaseMessagingService` para controlar los mensajes.
1. [Agregue](#add-incoming-notifications-to-the-xamarinforms-ui) notificaciones entrantes a la interfaz de usuario de Xamarin.Forms.

> [!NOTE]
> La acción de compilación `GoogleServicesJson` forma parte del paquete NuGet `Xamarin.GooglePlayServices.Base`. Visual Studio 2019 establece las acciones de compilación disponibles durante el inicio. Si no ve `GoogleServicesJson` como acción de compilación, reinicie Visual Studio 2019 después de instalar los paquetes NuGet.

### <a name="configure-android-manifest"></a>Configuración del manifiesto de Android

Los elementos `receiver` del elemento `application` permiten a la aplicación comunicarse con Firebase. Los elementos `uses-permission` permiten a la aplicación controlar los mensajes y registrarse en el centro de notificaciones de Azure. El archivo completo `AndroidManifest.xml` debe tener un aspecto similar al del ejemplo siguiente:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="YOUR_PACKAGE_NAME" android:installLocation="auto">
  <uses-sdk android:minSdkVersion="21" />
  <uses-permission android:name="android.permission.INTERNET" />
  <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
  <uses-permission android:name="android.permission.WAKE_LOCK" />
  <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
  <application android:label="Notification Hub Sample">
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
      <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
      </intent-filter>
    </receiver>
  </application>
</manifest>
```

### <a name="override-no-locfirebasemessagingservice-to-handle-messages"></a>Invalidación de `FirebaseMessagingService` para controlar los mensajes

Para registrarse en Firebase y controlar los mensajes, cree una subclase a partir de la clase `FirebaseMessagingService`. La aplicación de ejemplo define una clase `FirebaseService` que crea una subclase a partir de `FirebaseMessagingService`. Esta clase se etiqueta con un atributo `IntentFilter`, que incluye el filtro `com.google.firebase.MESSAGING_EVENT`. Este filtro permite que Android pase los mensajes entrantes a esta clase para controlar:

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
public class FirebaseService : FirebaseMessagingService
{
    // ...
}

```

Cuando se inicia la aplicación, el SDK de Firebase solicita automáticamente un identificador de token único del servidor de Firebase. Tras una solicitud correcta, se llama al método `OnNewToken` en la clase `FirebaseService`. El proyecto de ejemplo invalida este método y registra el token en Azure Notification Hubs:

```csharp
public override void OnNewToken(string token)
{
    // NOTE: save token instance locally, or log if desired

    SendRegistrationToServer(token);
}

void SendRegistrationToServer(string token)
{
    try
    {
        NotificationHub hub = new NotificationHub(AppConstants.NotificationHubName, AppConstants.ListenConnectionString, this);

        // register device with Azure Notification Hub using the token from FCM
        Registration registration = hub.Register(token, AppConstants.SubscriptionTags);

        // subscribe to the SubscriptionTags list with a simple template.
        string pnsHandle = registration.PNSHandle;
        TemplateRegistration templateReg = hub.RegisterTemplate(pnsHandle, "defaultTemplate", AppConstants.FCMTemplateBody, AppConstants.SubscriptionTags);
    }
    catch (Exception e)
    {
        Log.Error(AppConstants.DebugTag, $"Error registering device: {e.Message}");
    }
}
```

El método `SendRegistrationToServer` registra el dispositivo en el centro de notificaciones de Azure y se suscribe a etiquetas con una plantilla. La aplicación de ejemplo define una sola etiqueta denominada `default` y una plantilla con un solo parámetro denominado `messageParam` en el archivo `AppConstants.cs`. Para obtener más información sobre el registro, las etiquetas y las plantillas, vea [Registro de plantillas y etiquetas en el centro de notificaciones de Azure](#register-templates-and-tags-with-the-azure-notification-hub).

Cuando se recibe un mensaje, se llama al método `OnMessageReceived` en la clase `FirebaseService`:

```csharp
public override void OnMessageReceived(RemoteMessage message)
{
    base.OnMessageReceived(message);
    string messageBody = string.Empty;

    if (message.GetNotification() != null)
    {
        messageBody = message.GetNotification().Body;
    }

    // NOTE: test messages sent via the Azure portal will be received here
    else
    {
        messageBody = message.Data.Values.First();
    }

    // convert the incoming message to a local notification
    SendLocalNotification(messageBody);

    // send the incoming message directly to the MainPage
    SendMessageToMainPage(messageBody);
}

void SendLocalNotification(string body)
{
    var intent = new Intent(this, typeof(MainActivity));
    intent.AddFlags(ActivityFlags.ClearTop);
    intent.PutExtra("message", body);

    //Unique request code to avoid PendingIntent collision.
    var requestCode = new Random().Next();
    var pendingIntent = PendingIntent.GetActivity(this, requestCode, intent, PendingIntentFlags.OneShot);

    var notificationBuilder = new NotificationCompat.Builder(this, AppConstants.NotificationChannelName)
        .SetContentTitle("XamarinNotify Message")
        .SetSmallIcon(Resource.Drawable.ic_launcher)
        .SetContentText(body)
        .SetAutoCancel(true)
        .SetShowWhen(false)
        .SetContentIntent(pendingIntent);

    if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
    {
        notificationBuilder.SetChannelId(AppConstants.NotificationChannelName);
    }

    var notificationManager = NotificationManager.FromContext(this);
    notificationManager.Notify(0, notificationBuilder.Build());
}

void SendMessageToMainPage(string body)
{
    (App.Current.MainPage as MainPage)?.AddMessage(body);
}
```

Los mensajes entrantes se convierten en una notificación local con el método `SendLocalNotification`. Este método crea un nuevo elemento `Intent` y coloca el contenido del mensaje en el `Intent` como una `string` `Extra`. Cuando el usuario pulsa la notificación local, tanto si la aplicación está en primer como en segundo plano, se inicia `MainActivity` y tiene acceso al contenido del mensaje a través del objeto `Intent`.

La notificación local y el ejemplo `Intent` requieren que el usuario realice la acción de pulsar en la notificación. Esto es deseable si el usuario debe realizar una acción antes de que cambie el estado de la aplicación. Aunque en algunos casos puede que quiera acceder a los datos del mensaje sin necesidad de una acción del usuario. En el ejemplo anterior también se envía el mensaje directamente a la instancia actual de `MainPage` con el método `SendMessageToMainPage`. En producción, si implementa ambos métodos para un solo tipo de mensaje, el objeto `MainPage` obtiene mensajes duplicados si el usuario pulsa la notificación.

> [!NOTE]
> La aplicación Android solo recibe notificaciones push si se ejecuta en primer o en segundo plano. Para recibir notificaciones push cuando el elemento `Activity` principal no se esté ejecutando, debe implementar un servicio que esté fuera del ámbito de este ejemplo. Para obtener más información, vea [Creación de servicios de Android](/xamarin/android/app-fundamentals/services/).

### <a name="add-incoming-notifications-to-the-no-locxamarinforms-ui"></a>Adición de notificaciones entrantes a la interfaz de usuario de Xamarin.Forms

La clase `MainActivity` necesita obtener permiso para controlar las notificaciones y administrar los datos de los mensajes entrantes. En el siguiente código se muestra la implementación completa de `MainActivity`:

```csharp
[Activity(Label = "NotificationHubSample", Icon = "@mipmap/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation, LaunchMode = LaunchMode.SingleTop)]
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        TabLayoutResource = Resource.Layout.Tabbar;
        ToolbarResource = Resource.Layout.Toolbar;

        base.OnCreate(savedInstanceState);

        global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
        LoadApplication(new App());

        if (!IsPlayServiceAvailable())
        {
            throw new Exception("This device does not have Google Play Services and cannot receive push notifications.");
        }

        CreateNotificationChannel();
    }

    protected override void OnNewIntent(Intent intent)
    {
        if (intent.Extras != null)
        {
            var message = intent.GetStringExtra("message");
            (App.Current.MainPage as MainPage)?.AddMessage(message);
        }

        base.OnNewIntent(intent);
    }

    bool IsPlayServiceAvailable()
    {
        int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.Success)
        {
            if (GoogleApiAvailability.Instance.IsUserResolvableError(resultCode))
                Log.Debug(AppConstants.DebugTag, GoogleApiAvailability.Instance.GetErrorString(resultCode));
            else
            {
                Log.Debug(AppConstants.DebugTag, "This device is not supported");
            }
            return false;
        }
        return true;
    }

    void CreateNotificationChannel()
    {
        // Notification channels are new as of "Oreo".
        // There is no need to create a notification channel on older versions of Android.
        if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
        {
            var channelName = AppConstants.NotificationChannelName;
            var channelDescription = String.Empty;
            var channel = new NotificationChannel(channelName, channelName, NotificationImportance.Default)
            {
                Description = channelDescription
            };

            var notificationManager = (NotificationManager)GetSystemService(NotificationService);
            notificationManager.CreateNotificationChannel(channel);
        }
    }
}
```

El atributo `Activity` establece el `LaunchMode` de la aplicación en `SingleTop`. Este modo de inicio indica al sistema operativo Android que solo permita una instancia de esta actividad. Con este modo de inicio, los datos entrantes de `Intent` se enrutan al método `OnNewIntent`, que extrae los datos del mensaje y los envía a la instancia de `MainPage` a través del método `AddMessage`. Si la aplicación usa otro modo de inicio, debe controlar los datos de `Intent` de forma diferente.

El método `OnCreate` usa un método auxiliar denominado `IsPlayServiceAvailable` para asegurarse de que el dispositivo admite el servicio Google Play. Los emuladores o dispositivos que no admiten el servicio Google Play no pueden recibir notificaciones push de Firebase.

## <a name="configure-ios-for-notifications"></a>Configuración de iOS para recibir notificaciones

El proceso de configuración de la aplicación iOS para recibir notificaciones es el siguiente:

1. Configure el **identificador de agrupación** del archivo `Info.plist` para que coincida con el valor usado en el perfil de aprovisionamiento.
1. Agregue la opción **Habilitar notificaciones push** al archivo `Entitlements.plist`.
1. Agregue el paquete NuGet `Xamarin.Azure.NotificationHubs.iOS` al proyecto.
1. [Regístrese](#register-for-notifications-with-apns) para recibir notificaciones en APNS.
1. [Registre](#register-with-azure-notification-hub-and-subscribe-to-tags) la aplicación en el centro de notificaciones de Azure y suscríbase a las etiquetas.
1. [Agregue](#add-apns-notifications-to-xamarinforms-ui) notificaciones de APNS a la interfaz de usuario de Xamarin.Forms.

En la siguiente captura de pantalla, se muestra la opción **Habilitar notificaciones push** seleccionada en el archivo `Entitlements.plist` en Visual Studio:

![Captura de pantalla del derecho Notificaciones push](azure-notification-hub-images/push-notification-entitlement.png "Derecho Notificaciones push")

### <a name="register-for-notifications-with-apns"></a>Registro para recibir notificaciones en APNS

El método `FinishedLaunching` del archivo `AppDelegate.cs` se debe invalidar para registrarse para recibir notificaciones remotas. El registro difiere en función de la versión de iOS que se use en el dispositivo. El proyecto de iOS de la aplicación de ejemplo invalida el método `FinishedLaunching` para llamar a `RegisterForRemoteNotifications`, como se muestra en el ejemplo siguiente:

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    global::Xamarin.Forms.Forms.Init();
    LoadApplication(new App());

    base.FinishedLaunching(app, options);

    RegisterForRemoteNotifications();

    return true;
}

void RegisterForRemoteNotifications()
{
    // register for remote notifications based on system version
    if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
    {
        UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert |
            UNAuthorizationOptions.Sound |
            UNAuthorizationOptions.Sound,
            (granted, error) =>
            {
                if (granted)
                    InvokeOnMainThread(UIApplication.SharedApplication.RegisterForRemoteNotifications);
            });
    }
    else if (UIDevice.CurrentDevice.CheckSystemVersion(8, 0))
    {
        var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
        new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();
    }
    else
    {
        UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
        UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes);
    }
}
```

### <a name="register-with-azure-notification-hub-and-subscribe-to-tags"></a>Registro en el centro de notificaciones de Azure y suscripción a etiquetas

Una vez que el dispositivo se ha registrado correctamente para recibir notificaciones remotas durante el método `FinishedLaunching`, iOS llama al método `RegisteredForRemoteNotifications`. Este método debe invalidarse para realizar las siguientes acciones:

1. Crear una instancia de `SBNotificationHub`.
1. Anular el registro de los registros existentes.
1. Registrar el dispositivo en el centro de notificaciones.
1. Suscribirse a etiquetas específicas con una plantilla.

Para obtener más información sobre el registro del dispositivo, las plantillas y las etiquetas, vea [Registro de plantillas y etiquetas en el centro de notificaciones de Azure](#register-templates-and-tags-with-the-azure-notification-hub). En el código siguiente se muestra el registro del dispositivo y las plantillas:

```csharp
public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
{
    Hub = new SBNotificationHub(AppConstants.ListenConnectionString, AppConstants.NotificationHubName);

    // update registration with Azure Notification Hub
    Hub.UnregisterAll(deviceToken, (error) =>
    {
        if (error != null)
        {
            Debug.WriteLine($"Unable to call unregister {error}");
            return;
        }

        var tags = new NSSet(AppConstants.SubscriptionTags.ToArray());
        Hub.RegisterNative(deviceToken, tags, (errorCallback) =>
        {
            if (errorCallback != null)
            {
                Debug.WriteLine($"RegisterNativeAsync error: {errorCallback}");
            }
        });

        var templateExpiration = DateTime.Now.AddDays(120).ToString(System.Globalization.CultureInfo.CreateSpecificCulture("en-US"));
        Hub.RegisterTemplate(deviceToken, "defaultTemplate", AppConstants.APNTemplateBody, templateExpiration, tags, (errorCallback) =>
        {
            if (errorCallback != null)
            {
                if (errorCallback != null)
                {
                    Debug.WriteLine($"RegisterTemplateAsync error: {errorCallback}");
                }
            }
        });
    });
}
```

> [!NOTE]
> Se puede producir un error al registrarse para recibir notificaciones remotas en situaciones como la ausencia de conexión de red. Puede optar por invalidar el método `FailedToRegisterForRemoteNotifications` para controlar el error de registro.

### <a name="add-apns-notifications-to-no-locxamarinforms-ui"></a>Adición de notificaciones de APNS a la interfaz de usuario de Xamarin.Forms

Cuando un dispositivo recibe una notificación remota, iOS llama al método `ReceivedRemoteNotification`. El JSON del mensaje entrante se convierte en un objeto `NSDictionary` y el método `ProcessNotification` extrae valores del diccionario y los envía a la instancia de `MainPage` de Xamarin.Forms. El método `ReceivedRemoteNotifications` se invalida para llamar a `ProcessNotification`, como se muestra en el siguiente código:

```csharp
public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
{
    ProcessNotification(userInfo, false);
}

void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
{
    // make sure we have a payload
    if (options != null && options.ContainsKey(new NSString("aps")))
    {
        // get the APS dictionary and extract message payload. Message JSON will be converted
        // into a NSDictionary so more complex payloads may require more processing
        NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
        string payload = string.Empty;
        NSString payloadKey = new NSString("alert");
        if (aps.ContainsKey(payloadKey))
        {
            payload = aps[payloadKey].ToString();
        }

        if (!string.IsNullOrWhiteSpace(payload))
        {
            (App.Current.MainPage as MainPage)?.AddMessage(payload);
        }

    }
    else
    {
        Debug.WriteLine($"Received request to process notification but there was no payload.");
    }
}
```

## <a name="test-notifications-in-the-azure-portal"></a>Prueba de notificaciones en Azure Portal

Azure Notification Hubs permite comprobar que la aplicación puede recibir mensajes de prueba. La sección **Envío de prueba** del centro de notificaciones permite seleccionar la plataforma de destino y enviar un mensaje. Al establecer **Envío a la expresión de etiqueta** en `default`, se envían mensajes a las aplicaciones que han registrado una plantilla para la etiqueta `default`. Al hacer clic en el botón **Enviar**, se genera un informe que incluye el número de dispositivos a los que llega el mensaje. En la captura de pantalla siguiente se muestra una prueba de notificación de Android en Azure Portal:

![Captura de pantalla de un mensaje de prueba del centro de notificaciones de Azure](azure-notification-hub-images/azure-notification-hub-test-send.png "Mensaje de prueba del centro de notificaciones de Azure")

### <a name="testing-tips"></a>Sugerencias para las pruebas

1. Al probar que una aplicación puede recibir notificaciones push, debe usar un dispositivo físico. Los dispositivos virtuales iOS y Android no se podrían configurar correctamente para recibir notificaciones push.
1. La aplicación de ejemplo de Android registra el token y las plantillas una vez que se emite el token de Firebase. Durante las pruebas, es posible que tenga que solicitar un nuevo token y volver a registrarlo en el centro de notificaciones de Azure. La mejor manera de forzar esto es limpiar el proyecto, eliminar las carpetas `bin` y `obj`, y desinstalar la aplicación del dispositivo antes de volver a compilar e implementar.
1. Muchas partes del flujo de notificaciones push se ejecutan de forma asincrónica. Esto puede dar lugar a que no se alcancen los puntos de interrupción o a que se alcancen en un orden inesperado. Use el registro del dispositivo o de depuración para realizar un seguimiento de la ejecución sin interrumpir el flujo de la aplicación. Filtre el registro del dispositivo Android mediante el elemento `DebugTag` especificado en `Constants`.
1. Cuando se detiene la depuración en Visual Studio, se fuerza a la aplicación a cerrarse. Los receptores de mensajes u otros servicios iniciados como parte del proceso de depuración se cierran y no responden a los eventos de mensaje.

## <a name="create-a-notification-dispatcher"></a>Creación de un distribuidor de notificaciones

Azure Notification Hubs permite que la aplicación de back-end envíe notificaciones a dispositivos entre plataformas. En el ejemplo, se muestra el envío de notificaciones con una aplicación de consola. La aplicación incluye el archivo `DispatcherConstants.cs`, que define las siguientes propiedades:

```csharp
public static class DispatcherConstants
{
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string FullAccessConnectionString { get; set; } = "< Insert your DefaultFullSharedAccessSignature >";
}
```

Debe configurar el archivo `DispatcherConstants.cs` para que coincida con la configuración del centro de notificaciones de Azure. El valor de la propiedad `SubscriptionTags` debe coincidir con los valores usados en las aplicaciones cliente. La propiedad `NotificationHubName` es el nombre de la instancia del centro de notificaciones de Azure. La propiedad `FullAccessConnectionString` es la clave de acceso que se encuentra en las **Directivas de acceso** del centro de notificaciones. En la captura de pantalla siguiente se muestra la ubicación de las propiedades `NotificationHubName` y `FullAccessConnectionString` en Azure Portal:

![Captura de pantalla del nombre del centro de notificaciones de Azure y FullAccessConnectionString](azure-notification-hub-images/notification-hub-full-access-policy.png "Nombre del centro de notificaciones de Azure y FullAccessConnectionString")

La aplicación de consola recorre en bucle cada valor `SubscriptionTags` y envía notificaciones a los suscriptores mediante una instancia de la clase `NotificationHubClient`. En el código siguiente se muestra la clase `Program` de la aplicación de consola:

``` csharp
class Program
{
    static int messageCount = 0;

    static void Main(string[] args)
    {
        Console.WriteLine($"Press the spacebar to send a message to each tag in {string.Join(", ", DispatcherConstants.SubscriptionTags)}");
        WriteSeparator();
        while (Console.ReadKey().Key == ConsoleKey.Spacebar)
        {
            SendTemplateNotificationsAsync().GetAwaiter().GetResult();
        }
    }

    private static async Task SendTemplateNotificationsAsync()
    {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(DispatcherConstants.FullAccessConnectionString, DispatcherConstants.NotificationHubName);
        Dictionary<string, string> templateParameters = new Dictionary<string, string>();

        messageCount++;

        // Send a template notification to each tag. This will go to any devices that
        // have subscribed to this tag with a template that includes "messageParam"
        // as a parameter
        foreach (var tag in DispatcherConstants.SubscriptionTags)
        {
            templateParameters["messageParam"] = $"Notification #{messageCount} to {tag} category subscribers!";
            try
            {
                await hub.SendTemplateNotificationAsync(templateParameters, tag);
                Console.WriteLine($"Sent message to {tag} subscribers.");
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Failed to send template notification: {ex.Message}");
            }
        }

        Console.WriteLine($"Sent messages to {DispatcherConstants.SubscriptionTags.Length} tags.");
        WriteSeparator();
    }

    private static void WriteSeparator()
    {
        Console.WriteLine("==========================================================================");
    }
}
```

Al ejecutar la aplicación de consola de ejemplo, se puede presionar la barra espaciadora para enviar mensajes. Los dispositivos que ejecutan las aplicaciones cliente deben recibir notificaciones numeradas, siempre que estén configuradas correctamente.

## <a name="related-links"></a>Vínculos relacionados

* [Plantillas de notificaciones push](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).
* [Administración de registros de dispositivos](/azure/notification-hubs/notification-hubs-push-notification-registration-management).
* [Expresiones de etiqueta y enrutamiento](/azure/notification-hubs/notification-hubs-tags-segment-push-message).
* [Tutorial de Azure Notification Hubs de Xamarin.Android](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm).
* [Tutorial de Azure Notification Hubs de Xamarin.iOS](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started).
