---
title: Envío y recepción de notificaciones de envío con Azure Notification Hubs y Xamarin. Forms
description: En este artículo se explica cómo usar Azure Notification Hubs para enviar notificaciones de envío entre plataformas a aplicaciones de Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 07D13195-3A0D-4C95-ACF0-143A9084973C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 05/23/2019
ms.openlocfilehash: c4237e9315ccc095abc72fdec24d58ffe1faebdf
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739226"
---
# <a name="send-and-receive-push-notifications-with-azure-notification-hubs-and-xamarinforms"></a>Envío y recepción de notificaciones de envío con Azure Notification Hubs y Xamarin. Forms

[![Descargar el](~/media/shared/download.png)ejemplo descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azurenotificationhub/)

Las notificaciones de entrega envían información desde un sistema back-end a una aplicación móvil. Apple, Google y otras plataformas tienen su propio servicio de notificaciones de envío (PNS). Azure Notification Hubs le permite centralizar las notificaciones entre plataformas para que la aplicación back-end pueda comunicarse con un solo concentrador, lo que se encarga de distribuir las notificaciones a cada PNS específico de la plataforma.

Integre Azure Notification Hubs en Mobile Apps siguiendo estos pasos:

1. [Configure la Notification Services de extracción y el centro de notificaciones de Azure](#set-up-push-notification-services-and-azure-notification-hub).
1. [Aprenda a usar plantillas y etiquetas](#register-templates-and-tags-with-the-azure-notification-hub).
1. [Cree una aplicación de Xamarin. Forms multiplataforma](#xamarinforms-application-functionality).
1. [Configure el proyecto de Android nativo para las notificaciones de envío](#configure-the-android-application-for-notifications).
1. [Configure el proyecto de iOS nativo para las notificaciones de envío](#configure-ios-for-notifications).
1. [Notificaciones de prueba mediante el centro de notificaciones de Azure](#test-notifications-in-the-azure-portal).
1. [Cree una aplicación de back-end para enviar notificaciones](#create-a-notification-dispatcher).

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Configuración de Notification Services de extracción y el centro de notificaciones de Azure

La integración de Azure Notification Hubs con una aplicación móvil de Xamarin. Forms es similar a la integración de Azure Notification Hubs con una aplicación nativa de Xamarin. Configure una **aplicación FCM** siguiendo los pasos de la consola de Firebase en las notificaciones de [envío a Xamarin. Android con Azure Notification hubs](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm#create-a-firebase-project-and-enable-firebase-cloud-messaging). Complete los pasos siguientes con el tutorial de Xamarin. Android:

1. Defina un nombre de paquete Android como `com.xamarin.notifysample`, que se usa en el ejemplo.
1. Descargue **Google-Services. JSON** desde la consola de Firebase. En un paso posterior, agregará este archivo a la aplicación Android.
1. Cree una instancia de Azure Notification Hub y asígnele un nombre. En este artículo y ejemplo `xdocsnotificationhub` se usa como nombre del centro.
1. Copie la **clave de servidor** de FCM y guárdela como la **clave de API** en **Google (GCM/FCM)** en el centro de notificaciones de Azure.

En la captura de pantalla siguiente se muestra la configuración de la plataforma Google en el centro de notificaciones de Azure:

![Captura de pantalla de la configuración de Google del centro de notificaciones de Azure](azure-notification-hub-images/fcm-notification-hub-config.png "Configuración de Google del centro") de notificaciones de Azure

Necesitará una máquina macOS para completar la instalación de dispositivos iOS. Configure APNS siguiendo los pasos iniciales de las [notificaciones Push en Xamarin. iOS con Azure Notification hubs](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started#generate-the-certificate-signing-request-file). Complete los pasos siguientes con el tutorial de Xamarin. iOS:

1. Defina un identificador de lote de iOS. En este artículo y ejemplo `com.xamarin.notifysample` se usa como identificador de paquete.
1. Cree un archivo de solicitud de firma de certificado (CSR) y úselo para generar un certificado de notificación de extracción.
1. Cargue el certificado de notificación push en **Apple (APNS)** en el centro de notificaciones de Azure.

En la captura de pantalla siguiente se muestra la configuración de la plataforma de Apple en el centro de notificaciones de Azure:

![Captura de pantalla de la configuración de Apple del centro de notificaciones de Azure](azure-notification-hub-images/apns-notification-hub-config.png "Configuración de Apple del centro") de notificaciones de Azure

## <a name="register-templates-and-tags-with-the-azure-notification-hub"></a>Registro de plantillas y etiquetas en el centro de notificaciones de Azure

Azure Notification Hub requiere que las aplicaciones móviles se registren en el concentrador, definir plantillas y suscribirse a etiquetas. El registro vincula un identificador de PNS específico de la plataforma a un identificador en el centro de notificaciones de Azure. Para más información sobre los registros, consulte [Administración de registros](/azure/notification-hubs/notification-hubs-push-notification-registration-management).

Las plantillas permiten a los dispositivos especificar plantillas de mensajes con parámetros. Los mensajes entrantes se pueden personalizar por dispositivo, por etiqueta. Para obtener más información acerca de las plantillas, consulte [plantillas](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).

Las etiquetas se pueden usar para suscribirse a categorías de mensajes como noticias, deportes y tiempo. Para simplificar, la aplicación de ejemplo define una plantilla predeterminada con un parámetro `messageParam` único denominado y una sola `default`etiqueta denominada. En sistemas más complejos, las etiquetas específicas del usuario se pueden usar para enviar mensajes a un usuario entre dispositivos para recibir notificaciones personalizadas. Para obtener más información sobre las etiquetas, consulte [expresiones de enrutamiento y etiquetas](/azure/notification-hubs/notification-hubs-tags-segment-push-message).

Para recibir mensajes correctamente, cada aplicación nativa debe realizar estos pasos:

1. Obtenga un identificador de PNS o un token de la plataforma PNS.
1. Registre el identificador de PNS con el centro de notificaciones de Azure.
1. Especifique una plantilla que contenga los mismos parámetros que los mensajes salientes.
1. Suscríbase a la etiqueta de destino de los mensajes salientes.

Estos pasos se describen con más detalle para cada plataforma en las secciones [configurar la aplicación Android para notificaciones](#configure-the-android-application-for-notifications) y [configurar iOS para notificaciones](#configure-ios-for-notifications) .

## <a name="xamarinforms-application-functionality"></a>Funcionalidad de la aplicación de Xamarin. Forms

La aplicación de ejemplo de Xamarin. Forms muestra una lista de mensajes de notificación de envío. Esto se logra con el `AddMessage` método, que agrega el mensaje de notificación de envío especificado a la interfaz de usuario. Este método también evita que se agreguen mensajes duplicados a la interfaz de usuario y se ejecuta en el subproceso principal para que se pueda llamar desde cualquier subproceso. En el código siguiente se muestra el método `AddMessage`:

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

La aplicación de ejemplo contiene un archivo **AppConstants.CS** , que define las propiedades utilizadas por los proyectos de la plataforma. Este archivo debe personalizarse con los valores de su centro de notificaciones de Azure. En el código siguiente se muestra el archivo **AppConstants.CS** :

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

Personalice los valores siguientes en `AppConstants` para conectar la aplicación de ejemplo a su centro de notificaciones de Azure:

* `NotificationHubName`: Use el nombre del centro de notificaciones de Azure que creó en el Azure Portal.
* `ListenConnectionString`: Este valor se encuentra en el centro de notificaciones de Azure en **directivas de acceso**.

La captura de pantalla siguiente muestra dónde se encuentran estos valores en el Azure Portal:

![Captura de pantalla de la Directiva de acceso del centro de notificaciones de Azure](azure-notification-hub-images/notification-hub-access-policy.png "Directiva de acceso al centro") de notificaciones de Azure

## <a name="configure-the-android-application-for-notifications"></a>Configuración de la aplicación de Android para las notificaciones

Complete los pasos siguientes para configurar la aplicación de Android para recibir y procesar notificaciones:

1. Configure el **nombre del paquete** de Android para que coincida con el nombre del paquete en la consola de Firebase.
1. Instale los siguientes paquetes de NuGet para interactuar con Google Play, Firebase y Azure Notification Hubs:
    1. Xamarin.GooglePlayServices.Base.
    1. Xamarin. Firebase. Messaging.
    1. Xamarin.Azure.NotificationHubs.Android.
1. Copie el `google-services.json` archivo que ha descargado durante la instalación de FCM en el proyecto y establezca `GoogleServicesJson`la acción de compilación en.
1. [Configure archivo AndroidManifest. XML para comunicarse con Firebase](#configure-android-manifest).
1. [Registre la aplicación con Firebase y el centro de notificaciones `FirebaseInstanceIdService`de Azure mediante ](#register-using-a-custom-firebaseinstanceidservice).
1. [Procesar mensajes con un `FirebaseMessagingService` ](#process-messages-with-a-firebasemessagingservice).
1. [Agregue notificaciones entrantes a la interfaz de usuario de Xamarin. Forms](#add-incoming-notifications-to-the-xamarinforms-ui).

> [!NOTE]
> La acción de compilación **GoogleServicesJson** forma parte del paquete NuGet **Xamarin. GooglePlayServices. base** . Visual Studio 2019 establece las acciones de compilación disponibles durante el inicio. Si no ve **GoogleServicesJson** como acción de compilación, reinicie Visual Studio 2019 después de instalar los paquetes de NuGet.

### <a name="configure-android-manifest"></a>Configuración del manifiesto de Android

Los `receiver` elementos dentro del `application` elemento permiten que la aplicación se comunique con Firebase. Los `uses-permission` elementos permiten que la aplicación controle los mensajes y se registren en el centro de notificaciones de Azure. El **archivo AndroidManifest. XML** completo debe ser similar al ejemplo siguiente:

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

### <a name="register-using-a-custom-firebaseinstanceidservice"></a>Registro con un FirebaseInstanceIdService personalizado

Firebase emite tokens que identifican de forma única un dispositivo en el PNS. Los tokens tienen una duración larga, pero se actualizan ocasionalmente. Cuando se emite o actualiza un token, la aplicación debe registrar su nuevo token en el centro de notificaciones de Azure. El registro es controlado por una instancia de una clase que deriva de `FirebaseInstanceIdService`.

En la aplicación de ejemplo `FirebaseRegistrationService` , la clase hereda `FirebaseInstanceIdService`de. Esta clase tiene un `IntentFilter` que incluye `com.google.firebase.INSTANCE_ID_EVENT`, lo que permite que el sistema operativo `OnTokenRefresh` Android llame automáticamente cuando Firebase emite un token.

En el código siguiente se muestra `FirebaseInstanceIdService` el personalizado de la aplicación de ejemplo:

```csharp
[Service]
[IntentFilter(new [] { "com.google.firebase.INSTANCE_ID_EVENT"})]
public class FirebaseRegistrationService : FirebaseInstanceIdService
{
    public override void OnTokenRefresh()
    {
        string token = FirebaseInstanceId.Instance.Token;

        // NOTE: logging the token is not recommended in production but during
        // development it is useful to test messages directly from Firebase
        Log.Info(AppConstants.DebugTag, $"Token received: {token}");

        SendRegistrationToServer(token);
    }

    void SendRegistrationToServer(string token)
    {
        try
        {
            NotificationHub hub = new NotificationHub(AppConstants.NotificationHubName, AppConstants.ListenConnectionString, this);

            // register device with Azure Notification Hub using the token from FCM
            Registration reg = hub.Register(token, AppConstants.SubscriptionTags);

            // subscribe to the SubscriptionTags list with a simple template.
            string pnsHandle = reg.PNSHandle;
            var cats = string.Join(", ", reg.Tags);
            var temp = hub.RegisterTemplate(pnsHandle, "defaultTemplate", AppConstants.FCMTemplateBody, AppConstants.SubscriptionTags);
        }
        catch (Exception e)
        {
            Log.Error(AppConstants.DebugTag, $"Error registering device: {e.Message}");
        }
    }
}
```

El `SendRegistrationToServer` método`FirebaseRegistrationClass` de registra el dispositivo en el centro de notificaciones de Azure y se suscribe a etiquetas con una plantilla. La aplicación de ejemplo define una etiqueta única `default` denominada y una plantilla con un solo parámetro `messageParam` llamado en el archivo **AppConstants.CS** . Para más información sobre el registro, las etiquetas y las plantillas, consulte [registro de plantillas y etiquetas en el centro](#register-templates-and-tags-with-the-azure-notification-hub) de notificaciones de Azure.

### <a name="process-messages-with-a-firebasemessagingservice"></a>Procesar mensajes con un FirebaseMessagingService

Los mensajes entrantes se enrutan a una `FirebaseMessagingService` instancia de, donde se pueden convertir en una notificación local. El proyecto de Android de la aplicación de ejemplo contiene una `FirebaseService` clase denominada que hereda `FirebaseMessagingService`de. Esta clase tiene un `IntentFilter` que incluye `com.google.firebase.MESSAGING_EVENT`, lo que permite al sistema operativo Android `OnMessageReceived` llamar automáticamente cuando se recibe un mensaje de notificación de envío.

En el ejemplo siguiente se `FirebaseService` muestra el de la aplicación de ejemplo:

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
public class FirebaseService : FirebaseMessagingService
{
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
        var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

        var notificationBuilder = new NotificationCompat.Builder(this)
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
}
```

Los mensajes entrantes se convierten en una notificación `SendLocalNotification` local con el método. Este método crea una nueva `Intent` y coloca el contenido del mensaje `Intent` en como `string` `Extra`. Cuando el usuario pulsa la notificación local, si la aplicación está en primer plano o en segundo plano, `MainActivity` se inicia y tiene acceso al contenido del mensaje a través `Intent` del objeto.

La notificación y `Intent` el ejemplo locales requieren que el usuario realice la acción de puntear en la notificación. Esto es conveniente cuando el usuario debe tomar medidas antes de que cambie el estado de la aplicación. Sin embargo, puede que desee tener acceso a los datos del mensaje sin necesidad de una acción del usuario en algunos casos. En el ejemplo anterior también se envía el mensaje directamente a `MainPage` la instancia actual `SendMessageToMainPage` con el método. En producción, si implementa ambos métodos para un tipo de mensaje único, el `MainPage` objeto obtendrá mensajes duplicados si el usuario pulsa la notificación.

> [!NOTE]
> La aplicación de Android solo recibirá notificaciones de envío si se ejecuta en segundo plano o en primer plano. Para recibir notificaciones de envío cuando el `Activity` principal no se está ejecutando, debe implementar un servicio que esté fuera del ámbito de este ejemplo. Para obtener más información, vea [creación de servicios de Android](/xamarin/android/app-fundamentals/services/)

### <a name="add-incoming-notifications-to-the-xamarinforms-ui"></a>Agregar notificaciones entrantes a la interfaz de usuario de Xamarin. Forms

La `MainActivity` clase debe obtener permiso para controlar las notificaciones y administrar los datos de los mensajes entrantes. En el código siguiente se muestra `MainActivity` la implementación completa:

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

        if (IsPlayServiceAvailable() == false)
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

El `Activity` atributo establece la aplicación `LaunchMode` en `SingleTop`. Este modo de inicio indica al sistema operativo Android que solo permita una única instancia de esta actividad. Con este modo de inicio, `Intent` los `OnNewIntent` datos entrantes se enrutan al método, que extrae los datos del `MainPage` mensaje y los `AddMessage` envía a la instancia a través del método. Si la aplicación usa un modo de inicio diferente, debe controlar `Intent` los datos de manera diferente.

El `OnCreate` método utiliza un método auxiliar llamado `IsPlayServiceAvailable` para asegurarse de que el dispositivo admite Google Play servicio. Los emuladores o dispositivos que no admiten Google Play servicio no pueden recibir notificaciones de envío de Firebase.

## <a name="configure-ios-for-notifications"></a>Configuración de iOS para notificaciones

El proceso de configuración de la aplicación de iOS para recibir notificaciones es el siguiente:

1. Configure el **identificador de paquete** en el archivo **info. plist** para que coincida con el valor usado en el perfil de aprovisionamiento.
1. Agregue la opción **habilitar notificaciones de envío** al archivo contitles **. plist** .
1. Agregue el paquete de NuGet **Xamarin. Azure. NotificationHubs. iOS** al proyecto.
1. [Regístrese para recibir notificaciones con APNS](#register-for-notifications-with-apns).
1. [Registre la aplicación con el centro de notificaciones de Azure y suscríbase a las etiquetas](#register-with-azure-notification-hub-and-subscribe-to-tags).
1. [Agregue notificaciones de APNS a la interfaz de usuario de Xamarin. Forms](#add-apns-notifications-to-xamarinforms-ui).

En la captura de pantalla siguiente se muestra la opción **habilitar notificaciones de envío** seleccionada en el archivo contitles **. plist** dentro de Visual Studio:

![Captura de pantalla del derecho a las notificaciones de envío](azure-notification-hub-images/push-notification-entitlement.png "Derechos") de notificaciones de envío

### <a name="register-for-notifications-with-apns"></a>Registrarse para recibir notificaciones con APNS

El `FinishedLaunching` método del archivo **AppDelegate.CS** se debe invalidar para registrarse para las notificaciones remotas. El registro difiere en función de la versión de iOS que se usa en el dispositivo. El proyecto de iOS de la aplicación de ejemplo invalida el `FinishedLaunching` método que se `RegisterForRemoteNotifications` va a llamar como se muestra en el ejemplo siguiente:

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

### <a name="register-with-azure-notification-hub-and-subscribe-to-tags"></a>Registrarse en el centro de notificaciones de Azure y suscribirse a etiquetas

Cuando el dispositivo se ha registrado correctamente para las notificaciones remotas durante el `FinishedLaunching` método, iOS llamará al `RegisteredForRemoteNotifications` método. Este método debe invalidarse para realizar las siguientes acciones:

1. Cree una instancia `SBNotificationHub`del.
1. Anular el registro de los registros existentes.
1. Registre el dispositivo en el centro de notificaciones.
1. Suscríbase a etiquetas específicas con una plantilla.

Para obtener más información sobre el registro del dispositivo, las plantillas y las etiquetas, consulte [registrar plantillas y etiquetas en el centro](#register-templates-and-tags-with-the-azure-notification-hub)de notificaciones de Azure. En el código siguiente se muestra el registro del dispositivo y las plantillas:

```csharp
public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
{
    Hub = new SBNotificationHub(AppConstants.ListenConnectionString, AppConstants.NotificationHubName);

    // update registration with Azure Notification Hub
    Hub.UnregisterAllAsync(deviceToken, (error) =>
    {
        if (error != null)
        {
            Debug.WriteLine($"Unable to call unregister {error}");
            return;
        }

        var tags = new NSSet(AppConstants.SubscriptionTags.ToArray());
        Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) =>
        {
            if (errorCallback != null)
            {
                Debug.WriteLine($"RegisterNativeAsync error: {errorCallback}");
            }
        });

        var templateExpiration = DateTime.Now.AddDays(120).ToString(System.Globalization.CultureInfo.CreateSpecificCulture("en-US"));
        Hub.RegisterTemplateAsync(deviceToken, "defaultTemplate", AppConstants.APNTemplateBody, templateExpiration, tags, (errorCallback) =>
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
> Se puede producir un error en el registro de notificaciones remotas en situaciones como la ausencia de conexión de red. Puede optar por invalidar `FailedToRegisterForRemoveNotifications` el método para controlar el error de registro.

### <a name="add-apns-notifications-to-xamarinforms-ui"></a>Agregar notificaciones de APNS a la interfaz de usuario de Xamarin. Forms

Cuando un dispositivo recibe una notificación remota, iOS llama al `ReceivedRemoteNotification` método. El JSON del mensaje entrante se convierte `NSDictionary` en un objeto y `ProcessNotification` el método extrae los valores del diccionario y los envía a la instancia `MainPage` de Xamarin. Forms. El `ReceivedRemoteNotifications` método se invalida para llamar `ProcessNotification` a como se muestra en el código siguiente:

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

## <a name="test-notifications-in-the-azure-portal"></a>Notificaciones de prueba en el Azure Portal

Azure Notification Hubs permite comprobar que la aplicación puede recibir mensajes de prueba. La sección **envío de prueba** del centro de notificaciones le permite elegir la plataforma de destino y enviar un mensaje. Establecer `default` la **expresión de etiqueta Send to** enviará mensajes a las aplicaciones que han registrado una plantilla para `default` la etiqueta. Al hacer clic en el botón **Enviar** , se genera un informe que incluye el número de dispositivos alcanzado con el mensaje. En la captura de pantalla siguiente se muestra una prueba de notificación de Android en el Azure Portal:

![Captura de pantalla de un mensaje de prueba de Azure Notification Hub](azure-notification-hub-images/azure-notification-hub-test-send.png "Mensaje de prueba de Azure Notification Hub")

### <a name="testing-tips"></a>Sugerencias de prueba

1. Al probar que una aplicación puede recibir notificaciones de envío, debe usar un dispositivo físico. Es posible que los dispositivos virtuales iOS y Android no estén configurados correctamente para recibir notificaciones de envío.
1. La aplicación de ejemplo de Android registra el token y las plantillas una vez cuando se emite el token Firebase. Durante las pruebas, es posible que tenga que solicitar un nuevo token y volver a registrarlo en el centro de notificaciones de Azure. La mejor manera de forzar esto es limpiar el proyecto, eliminar las `bin` carpetas `obj` y y desinstalar la aplicación del dispositivo antes de volver a generar e implementar.
1. Muchas partes del flujo de notificaciones de entrada se ejecutan de forma asincrónica. Esto puede dar lugar a que los puntos de interrupción no se alcancen o se alcancen en un orden inesperado. Use el registro de depuración o dispositivo para realizar un seguimiento de la ejecución sin interrumpir el flujo de la aplicación. Filtre el registro del dispositivo Android con `DebugTag` el especificado `Constants`en.

## <a name="create-a-notification-dispatcher"></a>Crear un distribuidor de notificaciones

Azure Notification Hubs habilitar la aplicación back-end para enviar notificaciones a dispositivos entre plataformas. En el ejemplo se muestra el envío de notificaciones con la aplicación de consola **NotificationDispatcher** . La aplicación incluye el archivo **DispatcherConstants.CS** , que define las siguientes propiedades:

```csharp
public static class DispatcherConstants
{
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string FullAccessConnectionString { get; set; } = "< Insert your DefaultFullSharedAccessSignature >";
}
```

Debe configurar **DispatcherConstants.CS** para que coincida con la configuración del centro de notificaciones de Azure. El valor de la `SubscriptionTags` propiedad debe coincidir con los valores que se usan en las aplicaciones cliente. La `NotificationHubName` propiedad es el nombre de la instancia del centro de notificaciones de Azure. La `FullAccessConnectionString` propiedad es la clave de acceso que se encuentra en las **directivas de acceso**de centro de notificaciones. En la captura de pantalla siguiente se muestra `NotificationHubName` la `FullAccessConnectionString` ubicación de las propiedades y en el Azure Portal:

![Captura de pantalla del nombre y FullAccessConnectionString del centro de notificaciones de Azure](azure-notification-hub-images/notification-hub-full-access-policy.png "Nombre y FullAccessConnectionString del centro de notificaciones de Azure")

La aplicación de consola recorre en `SubscriptionTags` bucle cada valor y envía notificaciones a los suscriptores mediante una `NotificationHubClient` instancia de la clase. En el código siguiente se muestra la `Program` clase de aplicación de consola:

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

Cuando se ejecuta la aplicación de consola de ejemplo, se puede presionar la barra espaciadora para enviar mensajes. Los dispositivos que ejecutan las aplicaciones cliente deben recibir notificaciones numeradas, siempre y cuando estén configurados correctamente.

## <a name="related-links"></a>Vínculos relacionados

* [Plantillas de notificación de extracción](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).
* [Administración del registro de dispositivos](/azure/notification-hubs/notification-hubs-push-notification-registration-management).
* [Enrutamiento y expresiones de etiqueta](/azure/notification-hubs/notification-hubs-tags-segment-push-message).
* [Tutorial de Azure Notification hubs de Xamarin. Android](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm).
* [Tutorial de Azure Notification hubs de Xamarin. iOS](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started).
