---
title: Remote Notifications with Firebase Cloud Messaging (Notificaciones remotas con Firebase Cloud Messaging)
description: En este tutorial se proporciona una explicación paso a paso de cómo usar la mensajería en la nube Firebase para implementar notificaciones remotas (también denominadas notificaciones de envío) en una aplicación de Xamarin. Android. Muestra cómo implementar las distintas clases que se necesitan para las comunicaciones con Firebase Cloud Messaging (FCM), proporciona ejemplos de cómo configurar el manifiesto de Android para el acceso a FCM y muestra la mensajería de nivel inferior mediante la consola de Firebase.
ms.prod: xamarin
ms.assetid: 4D7C5F46-C997-49F6-AFDA-6763E68CDC90
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/31/2018
ms.openlocfilehash: 702ca70e220d8e4d28a1a2ddc6be40daae052d58
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456125"
---
# <a name="remote-notifications-with-firebase-cloud-messaging"></a>Remote Notifications with Firebase Cloud Messaging (Notificaciones remotas con Firebase Cloud Messaging)

_En este tutorial se proporciona una explicación paso a paso de cómo usar la mensajería en la nube Firebase para implementar notificaciones remotas (también denominadas notificaciones de envío) en una aplicación de Xamarin. Android. Muestra cómo implementar las distintas clases que se necesitan para las comunicaciones con Firebase Cloud Messaging (FCM), proporciona ejemplos de cómo configurar el manifiesto de Android para el acceso a FCM y muestra la mensajería de nivel inferior mediante la consola de Firebase._

## <a name="fcm-notifications-overview"></a>Información general sobre las notificaciones de FCM

En este tutorial, se creará una aplicación básica denominada **FCMClient** para ilustrar los aspectos básicos de la mensajería de FCM. **FCMClient** comprueba la presencia de Google Play Services, recibe los tokens de registro de FCM, muestra las notificaciones remotas que se envían desde la consola de Firebase y se suscribe a los mensajes de tema:

[![Captura de pantalla de ejemplo de la aplicación](remote-notifications-with-fcm-images/00-app-example-sml.png)](remote-notifications-with-fcm-images/00-app-example.png#lightbox)

Se explorarán las siguientes áreas de temas:

1. Notificaciones en segundo plano

2. Mensajes de tema

3. Notificaciones en primer plano

Durante este tutorial, agregará funcionalidad a **FCMClient** de forma incremental y la ejecutará en un dispositivo o emulador para comprender cómo interactúa con FCM. Usará el registro para notificar las transacciones de aplicaciones activas con servidores de FCM y observará cómo se generan las notificaciones a partir de los mensajes de FCM que especifique en la GUI de notificaciones de la consola de Firebase.

## <a name="requirements"></a>Requisitos

Le resultará útil familiarizarse con los [distintos tipos de mensajes](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages) que se pueden enviar mediante la mensajería en la nube de Firebase. La carga del mensaje determinará el modo en que una aplicación cliente recibirá y procesará el mensaje.

Para poder continuar con este tutorial, debe adquirir las credenciales necesarias para usar los servidores de FCM de Google. Este proceso se explica en [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#setup_fcm).
En concreto, debe descargar el **google-services.jsen** el archivo para usarlo con el código de ejemplo que se muestra en este tutorial. Si aún no ha creado un proyecto en la consola de Firebase (o si aún no ha descargado el **google-services.jsen** el archivo), consulte [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md).

Para ejecutar la aplicación de ejemplo, necesitará un emulador o dispositivo de prueba de Android que se permitía con Firebase. La mensajería en la nube de Firebase admite clientes que se ejecutan en Android 4,0 o posterior, y estos dispositivos también deben tener la aplicación Google Play Store instalada (Google Play Services se requiere 9.2.1 o posterior). Si aún no tiene la aplicación Google Play Store instalada en el dispositivo, visite el sitio web de [Google Play](https://support.google.com/googleplay) para descargarla e instalarla. Como alternativa, puede usar el emulador de Android SDK con Google Play Services instalado en lugar de un dispositivo de prueba (no es necesario instalar el Google Play Store si usa el emulador de Android SDK).

## <a name="start-an-app-project"></a>Iniciar un proyecto de aplicación

Para empezar, cree un nuevo proyecto de Xamarin. Android vacío llamado **FCMClient**. Si no está familiarizado con la creación de proyectos de Xamarin. Android, consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).
Una vez creada la nueva aplicación, el siguiente paso es establecer el nombre del paquete e instalar varios paquetes de NuGet que se usarán para la comunicación con FCM.

### <a name="set-the-package-name"></a>Establecer el nombre del paquete

En [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md), ha especificado un nombre de paquete para la aplicación habilitada para FCM. Este nombre de paquete también sirve como [*identificador*](./firebase-cloud-messaging.md#fcm-in-action-app-id) de la aplicación que está asociado a la [clave de API](firebase-cloud-messaging.md#fcm-in-action-api-key). Configure la aplicación para que use este nombre de paquete:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Abra las propiedades del proyecto **FCMClient** .

2. En la página **manifiesto de Android** , establezca el nombre del paquete.

En el ejemplo siguiente, el nombre del paquete se establece en `com.xamarin.fcmexample` :

[![Establecer el nombre del paquete](remote-notifications-with-fcm-images/01-package-name-vs-sml.png)](remote-notifications-with-fcm-images/01-package-name-vs.png#lightbox)

Mientras actualiza el manifiesto de **Android**, asegúrese también de que `Internet` está habilitado el permiso.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Abra las propiedades del proyecto **FCMClient** .

2. En la página de la **aplicación Android** , establezca el nombre del paquete.

En el ejemplo siguiente, el nombre del paquete se establece en `com.xamarin.fcmexample` :

[![Establecer el nombre del paquete](remote-notifications-with-fcm-images/01-package-name-xs-sml.png)](remote-notifications-with-fcm-images/01-package-name-xs.png#lightbox)

Mientras actualiza el manifiesto de **Android**, asegúrese también de que el `INTERNET` permiso está habilitado (en **permisos necesarios**).

-----

> [!IMPORTANT]
> La aplicación cliente no podrá recibir un token de registro de FCM si el nombre del paquete no coincide *exactamente* con el nombre del paquete que se especificó en la consola de Firebase.

### <a name="add-the-xamarin-google-play-services-base-package"></a>Agregar el paquete base de Xamarin Google Play Services

Dado que la mensajería en la nube de Firebase depende de Google Play Services, se debe agregar el paquete de NuGet [de la base de Google Play Services de Xamarin](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Base/) al proyecto de Xamarin. Android. Necesitará la versión 29.0.0.2 o posterior.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. En Visual Studio, haga clic con el botón derecho en **referencias > administrar paquetes NuGet.**...

2. Haga clic en la pestaña **examinar** y busque **Xamarin. GooglePlayServices. base**.

3. Instale este paquete en el proyecto **FCMClient** :

    [![Instalación de Google Play Services base](remote-notifications-with-fcm-images/02-google-play-services-vs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. En Visual Studio para Mac, haga clic con el botón derecho en **paquetes > agregar paquetes.**...

2. Busque **Xamarin.GooglePlayServices.Base**.

3. Instale este paquete en el proyecto **FCMClient** :

    [![Instalación de Google Play Services base](remote-notifications-with-fcm-images/02-google-play-services-xs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-xs.png#lightbox)

-----

Si recibe un error durante la instalación de NuGet, cierre el proyecto **FCMClient** , ábralo de nuevo y vuelva a intentar la instalación de NuGet.

Al instalar **Xamarin. GooglePlayServices. base**, también se instalan todas las dependencias necesarias. Edite **MainActivity.CS** y agregue la siguiente `using` instrucción:

```csharp
using Android.Gms.Common;
```

Esta instrucción hace `GoogleApiAvailability` que la clase de **Xamarin. GooglePlayServices. base** esté disponible para el código **FCMClient** .
`GoogleApiAvailability` se utiliza para comprobar la presencia de Google Play Services.

### <a name="add-the-xamarin-firebase-messaging-package"></a>Adición del paquete de mensajería de Xamarin Firebase

Para recibir mensajes de FCM, el paquete de NuGet de [Xamarin Firebase-Messaging](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) debe agregarse al proyecto de la aplicación. Sin este paquete, una aplicación de Android no puede recibir mensajes de los servidores de FCM.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. En Visual Studio, haga clic con el botón derecho en **referencias > administrar paquetes NuGet.**...

2. Busque **Xamarin. Firebase. Messaging**.

3. Instale este paquete en el proyecto **FCMClient** :

    [![Instalación de la mensajería de Xamarin Firebase](remote-notifications-with-fcm-images/03-firebase-messaging-vs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. En Visual Studio para Mac, haga clic con el botón derecho en **paquetes > agregar paquetes.**...

2. Busque **Xamarin. Firebase. Messaging**.

3. Instale este paquete en el proyecto **FCMClient** :

    [![Instalación de la mensajería de Xamarin Firebase](remote-notifications-with-fcm-images/03-firebase-messaging-xs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-xs.png#lightbox)

-----

Al instalar **Xamarin. Firebase. Messaging**, también se instalan todas las dependencias necesarias.

A continuación, edite **MainActivity.CS** y agregue las siguientes `using` instrucciones:

```csharp
using Firebase.Messaging;
using Firebase.Iid;
using Android.Util;
```

Las dos primeras instrucciones hacen que los tipos del paquete de NuGet **Xamarin. Firebase. Messaging** estén disponibles para el código **FCMClient** . **Android. util** agrega funcionalidad de registro que se usará para observar las transacciones con FMS.

### <a name="add-the-google-services-json-file"></a><a name="add-googleplayservices-json"></a>Adición del archivo JSON de Google Services

El siguiente paso consiste en agregar el **google-services.jsen** el archivo al directorio raíz del proyecto:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Copie **google-services.jsen** la carpeta del proyecto.

2. Agregue **google-services.js** al proyecto de la aplicación (haga clic en **Mostrar todos los archivos** en el **Explorador de soluciones**, haga clic con el botón derecho en **google-services.jsactivado**y seleccione **incluir en el proyecto**).

3. Seleccione **google services.json** en la ventana del **Explorador de soluciones**.

4. En el panel **propiedades** , establezca la **acción de compilación** en  **GoogleServicesJson**:

    [![Establecer la acción de compilación en GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-vs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-vs.png#lightbox)

    > [!NOTE] 
    > Si no se muestra la acción de compilación **GoogleServicesJson** , guarde y cierre la solución y vuelva a abrirla.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Copie **google-services.jsen** la carpeta del proyecto.

2. Agregue **google-services.js** al proyecto de la aplicación.

3. Haga clic con el botón secundario en **google-services.js**.

4. Establezca la **acción de compilación** en **GoogleServicesJson**:

    [![Establecer la acción de compilación en GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-xs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-xs.png#lightbox)

-----

Cuando se agrega **google-services.jsen** el proyecto (y se establece la acción de compilación **GoogleServicesJson** ), el proceso de compilación extrae el identificador de cliente y la [clave de API](./firebase-cloud-messaging.md#fcm-in-action-api-key) y, a continuación, agrega estas credenciales a la **AndroidManifest.xml** combinada o generada que reside en **obj/Debug/Android/AndroidManifest.xml**. Este proceso de mezcla agrega automáticamente los permisos y otros elementos FCM necesarios para la conexión con los servidores de FCM.

## <a name="check-for-google-play-services-and-create-a-notification-channel"></a>Comprobación de Google Play Services y creación de un canal de notificación

Google recomienda que las aplicaciones Android comprueben la presencia del Google Play Services APK antes de tener acceso a Google Play Services características (para obtener más información, consulte [comprobación de servicios de Google Play](https://firebase.google.com/docs/cloud-messaging/android/client#sample-play)).

Primero se creará un diseño inicial para la interfaz de usuario de la aplicación. Edite **Resources/layout/main. axml** y reemplace su contenido por el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="10dp">
    <TextView
        android:text=" "
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/msgText"
        android:textAppearance="?android:attr/textAppearanceMedium"
        android:padding="10dp" />
</LinearLayout>
```

Se `TextView` usará para mostrar mensajes que indiquen si Google Play Services está instalado. Guarde los cambios en **Main. axml**.

Edite **MainActivity.CS** y agregue las siguientes variables de instancia a la `MainActivity` clase:

```csharp
public class MainActivity : AppCompatActivity
{
    static readonly string TAG = "MainActivity";

    internal static readonly string CHANNEL_ID = "my_notification_channel";
    internal static readonly int NOTIFICATION_ID = 100;

    TextView msgText;
```

Las variables `CHANNEL_ID` y se `NOTIFICATION_ID` usarán en el método [`CreateNotificationChannel`](#create-notification-channel-code) que se agregará `MainActivity` posteriormente en este tutorial.

En el ejemplo siguiente, el `OnCreate` método comprobará que Google Play Services está disponible antes de que la aplicación intente usar los servicios de FCM.
Agregue el siguiente método a la clase `MainActivity`:

```csharp
public bool IsPlayServicesAvailable ()
{
    int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable (this);
    if (resultCode != ConnectionResult.Success)
    {
        if (GoogleApiAvailability.Instance.IsUserResolvableError (resultCode))
            msgText.Text = GoogleApiAvailability.Instance.GetErrorString (resultCode);
        else
        {
            msgText.Text = "This device is not supported";
            Finish ();
        }
        return false;
    }
    else
    {
        msgText.Text = "Google Play Services is available.";
        return true;
    }
}
```

Este código comprueba el dispositivo para ver si está instalado el Google Play Services APK. Si no está instalado, se muestra un mensaje en el `TextBox` que indica al usuario que descargue una apk del Google Play Store (o para habilitarla en la configuración del sistema del dispositivo).

<a name="create-notification-channel-code"></a>Las aplicaciones que se ejecutan en Android 8,0 (nivel de API 26) o superior deben crear un [_canal de notificación_](~/android/app-fundamentals/notifications/local-notifications.md) para publicar sus notificaciones.  Agregue el método siguiente a la `MainActivity` clase, que creará el canal de notificación (si es necesario):

```csharp
void CreateNotificationChannel()
{
    if (Build.VERSION.SdkInt < BuildVersionCodes.O)
    {
        // Notification channels are new in API 26 (and not a part of the
        // support library). There is no need to create a notification
        // channel on older versions of Android.
        return;
    }

    var channel = new NotificationChannel(CHANNEL_ID,
                                          "FCM Notifications",
                                          NotificationImportance.Default)
                  {

                      Description = "Firebase Cloud Messages appear in this channel"
                  };

    var notificationManager = (NotificationManager)GetSystemService(Android.Content.Context.NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

Reemplace el método `OnCreate` por el código siguiente:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);
    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();

    CreateNotificationChannel();
}
```

`IsPlayServicesAvailable` se llama al final de `OnCreate` para que la comprobación de Google Play Services se ejecute cada vez que se inicie la aplicación. `CreateNotificationChannel`Se llama al método para asegurarse de que existe un canal de notificación para dispositivos que ejecutan Android 8 o posterior. Si la aplicación tiene un `OnResume` método, debe llamar `IsPlayServicesAvailable` también a desde `OnResume` . Vuelva a compilar y ejecutar la aplicación completamente. Si todo está configurado correctamente, debería ver una pantalla similar a la siguiente captura de pantalla:

[![La aplicación indica que Google Play Services está disponible](remote-notifications-with-fcm-images/05-gps-available-sml.png)](remote-notifications-with-fcm-images/05-gps-available.png#lightbox)

Si no obtiene este resultado, compruebe que el Google Play Services APK está instalado en el dispositivo (para obtener más información, consulte [configuración de Google Play Services](https://developers.google.com/android/guides/setup)).
Compruebe también que ha agregado el paquete **Xamarin. Google. Play. Services. base** al proyecto **FCMClient** como se explicó anteriormente.

## <a name="add-the-instance-id-receiver"></a>Agregar el receptor de ID. de instancia

El siguiente paso consiste en agregar un servicio que se extienda `FirebaseInstanceIdService` para controlar la creación, la rotación y la actualización de los [tokens de registro de Firebase](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token). El `FirebaseInstanceIdService` servicio es necesario para que FCM pueda enviar mensajes al dispositivo. Cuando el `FirebaseInstanceIdService` servicio se agrega a la aplicación cliente, la aplicación recibirá automáticamente mensajes FCM y los mostrará como notificaciones cada vez que la aplicación esté en el fondo.

### <a name="declare-the-receiver-in-the-android-manifest"></a>Declarar el receptor en el manifiesto de Android

Edite **AndroidManifest.xml** e inserte los siguientes `<receiver>` elementos en la `<application>` sección:

```xml
<receiver
    android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver"
    android:exported="false" />
<receiver
    android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver"
    android:exported="true"
    android:permission="com.google.android.c2dm.permission.SEND">
    <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
    </intent-filter>
</receiver>
```

Este XML hace lo siguiente:

- Declara una `FirebaseInstanceIdReceiver` implementación de que proporciona un [identificador único](https://developers.google.com/instance-id/) para cada instancia de la aplicación. Este receptor también autentica y autoriza las acciones.

- Declara una implementación interna `FirebaseInstanceIdInternalReceiver` que se usa para iniciar los servicios de forma segura.

- El [identificador](./firebase-cloud-messaging.md#fcm-in-action-app-id) de la aplicación se almacena en el **google-services.jsen** el archivo que se [agregó al proyecto](#add-googleplayservices-json). Los enlaces de Firebase de Xamarin. Android reemplazarán el token `${applicationId}` por el identificador de la aplicación; la aplicación cliente no necesita código adicional para proporcionar el identificador de la aplicación.

`FirebaseInstanceIdReceiver`Es un `WakefulBroadcastReceiver` que recibe `FirebaseInstanceId` eventos y `FirebaseMessaging` y los entrega a la clase de la que deriva `FirebaseInstanceIdService` .

### <a name="implement-the-firebase-instance-id-service"></a>Implementar el servicio de ID. de instancia de Firebase

El trabajo de registro de la aplicación con FCM se controla mediante el servicio personalizado proporcionado por el `FirebaseInstanceIdService` usuario.
`FirebaseInstanceIdService` realiza los pasos siguientes:

1. Usa la [API de ID. de instancia](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID) para generar tokens de seguridad que autorizan a la aplicación cliente para acceder a FCM y el servidor de aplicaciones. A cambio, la aplicación devuelve un [token de registro](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token) de FCM.

2. Reenvía el token de registro al servidor de aplicaciones si lo requiere el servidor de aplicaciones.

Agregue un nuevo archivo denominado **MyFirebaseIIDService.CS** y reemplace el código de plantilla por lo siguiente:

```csharp
using System;
using Android.App;
using Firebase.Iid;
using Android.Util;

namespace FCMClient
{
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class MyFirebaseIIDService : FirebaseInstanceIdService
    {
        const string TAG = "MyFirebaseIIDService";
        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "Refreshed token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }
        void SendRegistrationToServer(string token)
        {
            // Add custom implementation, as needed.
        }
    }
}
```

Este servicio implementa un `OnTokenRefresh` método que se invoca cuando el token de registro se crea o cambia inicialmente. Cuando se `OnTokenRefresh` ejecuta, recupera el token más reciente de la `FirebaseInstanceId.Instance.Token` propiedad (que FCM se actualiza de forma asincrónica). En este ejemplo, el token actualizado se registra para que se pueda ver en la ventana de salida:

```csharp
var refreshedToken = FirebaseInstanceId.Instance.Token;
Log.Debug(TAG, "Refreshed token: " + refreshedToken);
```

`OnTokenRefresh` se invoca con poca frecuencia: se usa para actualizar el token en las siguientes circunstancias:

- Cuando la aplicación se instala o se desinstala.

- Cuando el usuario elimina los datos de la aplicación.

- Cuando la aplicación borra el ID. de instancia.

- Cuando se ha puesto en peligro la seguridad del token.

Según la documentación del [ID. de instancia](https://developers.google.com/instance-id/guides/android-implementation) de Google, el servicio de ID. de instancia de FCM solicitará que la aplicación actualice su token periódicamente (normalmente, cada 6 meses).

`OnTokenRefresh` también llama `SendRegistrationToAppServer` a para asociar el token de registro del usuario a la cuenta del servidor (si existe) que mantiene la aplicación:

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

Dado que esta implementación depende del diseño del servidor de aplicaciones, en este ejemplo se proporciona un cuerpo de método vacío. Si el servidor de aplicaciones requiere información de registro de FCM, modifique `SendRegistrationToAppServer` para asociar el token de ID. de instancia de FCM del usuario con cualquier cuenta de servidor que mantenga la aplicación. (Tenga en cuenta que el token es opaco a la aplicación cliente).

Cuando un token se envía al servidor de aplicaciones, `SendRegistrationToAppServer` debe mantener un valor booleano para indicar si el token se ha enviado al servidor. Si este valor booleano es false, `SendRegistrationToAppServer` envía el token al servidor &ndash; de aplicaciones; de lo contrario, el token ya se envió al servidor de aplicaciones en una llamada anterior. En algunos casos (como este `FCMClient` ejemplo), el servidor de aplicaciones no necesita el token; por lo tanto, este método no es necesario para este ejemplo.

## <a name="implement-client-app-code"></a>Implementar código de aplicación cliente

Ahora que los servicios del receptor están en su lugar, se puede escribir el código de la aplicación cliente para aprovechar estos servicios. En las secciones siguientes, se agrega un botón a la interfaz de usuario para registrar el token de registro (también denominado *token de identificador de instancia*) y se agrega más código a `MainActivity` para ver `Intent` información cuando la aplicación se inicia desde una notificación:

[![Botón de token de registro agregado a la pantalla de la aplicación](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

### <a name="log-tokens"></a>Tokens de registro

El código agregado en este paso está pensado únicamente para fines de demostración &ndash; . una aplicación cliente de producción no tendría necesidad de registrar tokens de registro. Edite **Resources/layout/main. axml** y agregue la siguiente `Button` declaración inmediatamente después del `TextView` elemento:

```xml
<Button
  android:id="@+id/logTokenButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:text="Log Token" />
```

Agregue el siguiente código al final del método `MainActivity.OnCreate`:

```csharp
var logTokenButton = FindViewById<Button>(Resource.Id.logTokenButton);
logTokenButton.Click += delegate {
    Log.Debug(TAG, "InstanceID token: " + FirebaseInstanceId.Instance.Token);
};
```

Este código registra el token actual en la ventana de salida cuando se puntea el botón del **token de registro** .

### <a name="handle-notification-intents"></a>Controlar los intentos de notificación

Cuando el usuario pulsa una notificación emitida desde **FCMClient**, los datos que acompañan a ese mensaje de notificación se ponen a disposición de los `Intent` extras. Edite **MainActivity.CS** y agregue el siguiente código al principio del `OnCreate` método (antes de la llamada a `IsPlayServicesAvailable` ):

```csharp
if (Intent.Extras != null)
{
    foreach (var key in Intent.Extras.KeySet())
    {
        var value = Intent.Extras.GetString(key);
        Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
    }
}
```

El iniciador de la aplicación `Intent` se activa cuando el usuario puntea el mensaje de notificación, por lo que este código registrará los datos que lo acompañan en la `Intent` ventana de salida. Si se `Intent` debe desencadenar un diferente, el `click_action` campo del mensaje de notificación se debe establecer en `Intent` (el iniciador `Intent` se utiliza cuando no `click_action` se especifica).

## <a name="background-notifications"></a>Notificaciones en segundo plano

Compile y ejecute la aplicación **FCMClient** . Se muestra el botón **registro del token** :

[![Se muestra el botón de token de registro](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

Puntee en el botón **token de registro** . Se debe mostrar un mensaje similar al siguiente en la ventana de salida del IDE:

[![Token de ID. de instancia mostrado en la ventana de salida](remote-notifications-with-fcm-images/07-token-received-sml.png)](remote-notifications-with-fcm-images/07-token-received.png#lightbox)

La cadena larga etiquetada con **token** es el token de identificador de instancia que se pegará en la consola de Firebase &ndash; Seleccione y copie esta cadena en el portapapeles. Si no ve un token de identificador de instancia, agregue la siguiente línea en la parte superior del `OnCreate` método para comprobar que **google-services.jsen** se analizó correctamente:

```csharp
Log.Debug(TAG, "google app id: " + GetString(Resource.String.google_app_id));
```

El `google_app_id` valor registrado en la ventana de salida debe coincidir con el `mobilesdk_app_id` valor registrado en **google-services.jsen**.

### <a name="send-a-message"></a>Envío de un mensaje

Inicie sesión en la [consola de Firebase](https://console.firebase.google.com), seleccione el proyecto, haga clic en **notificaciones**y haga clic en **enviar su primer mensaje**:

[![Botón Enviar el primer mensaje](remote-notifications-with-fcm-images/08-first-notification-sml.png)](remote-notifications-with-fcm-images/08-first-notification.png#lightbox)

En la página **redactar mensaje** , escriba el texto del mensaje y seleccione **dispositivo único**. Copie el token de identificador de instancia de la ventana de salida del IDE y péguelo en el campo **token de registro de FCM** de la consola de Firebase:

[![Cuadro de diálogo redactar mensaje](remote-notifications-with-fcm-images/09-compose-message-sml.png)](remote-notifications-with-fcm-images/09-compose-message.png#lightbox)

En el dispositivo (o emulador) Android, en segundo plano, puntee en el botón **información general** de Android y toque en la pantalla principal. Cuando el dispositivo esté listo, haga clic en **Enviar mensaje** en la consola de Firebase:

[![Botón Enviar mensaje](remote-notifications-with-fcm-images/10-send-message-sml.png)](remote-notifications-with-fcm-images/10-send-message.png#lightbox)

Cuando aparezca el cuadro de diálogo **Revisar mensaje** , haga clic en **Enviar**.
El icono de notificación debe aparecer en el área de notificación del dispositivo (o emulador):

[![Se muestra el icono de notificación](remote-notifications-with-fcm-images/11-notification-icon-sml.png)](remote-notifications-with-fcm-images/11-notification-icon.png#lightbox)

Abra el icono de notificación para ver el mensaje. El mensaje de notificación debe ser exactamente lo que se escribió en el campo **texto del mensaje** de la consola de Firebase:

[![Se muestra el mensaje de notificación en el dispositivo](remote-notifications-with-fcm-images/12-notification-sml.png)](remote-notifications-with-fcm-images/12-notification.png#lightbox)

Pulse el icono de notificación para iniciar la aplicación **FCMClient** . Los `Intent` extras enviados a **FCMClient** se muestran en la ventana de salida del IDE:

[![Lista de extras para intenciones de la clave, el identificador del mensaje y la clave de contraer](remote-notifications-with-fcm-images/13-intent-extras-sml.png)](remote-notifications-with-fcm-images/13-intent-extras.png#lightbox)

En este ejemplo, la clave **from** se establece en el número de proyecto Firebase de la aplicación (en este ejemplo, `41590732` ) y el **collapse_key** se establece en su nombre de paquete (**com. Xamarin. fcmexample**).
Si no recibe un mensaje, intente eliminar la aplicación **FCMClient** en el dispositivo (o emulador) y repita los pasos anteriores.

> [!NOTE]
> Si fuerza el cierre de la aplicación, FCM dejará de entregar notificaciones. Android impide que se realicen difusiones de servicio en segundo plano desde el inicio accidental o innecesario de componentes de aplicaciones detenidas. (Para obtener más información sobre este comportamiento, vea [iniciar controles en aplicaciones detenidas](https://developer.android.com/about/versions/android-3.1.html#launchcontrols)). Por esta razón, es necesario desinstalar manualmente la aplicación cada vez que la ejecuta y detenerla en una sesión de depuración, lo que &ndash; obliga a FCM a generar un nuevo token para que los mensajes se sigan recibiendo.

### <a name="add-a-custom-default-notification-icon"></a>Agregar un icono de notificación predeterminada personalizado

En el ejemplo anterior, el icono de notificación se establece en el icono de la aplicación. El siguiente código XML configura un icono predeterminado personalizado para las notificaciones. Android muestra este icono predeterminado personalizado para todos los mensajes de notificación en los que no se establece explícitamente el icono de notificación.

Para agregar un icono de notificación predeterminada personalizado, agregue el icono al directorio **Resources/drawable** , edite **AndroidManifest.xml**e inserte el siguiente `<meta-data>` elemento en la `<application>` sección:

```xml
<meta-data
    android:name="com.google.firebase.messaging.default_notification_icon"
    android:resource="@drawable/ic_stat_ic_notification" />
```

En este ejemplo, el icono de notificación que se encuentra en **los recursos/drawable/IC \_ STAT- \_ IC \_notification.png** se usará como el icono de notificación predeterminada personalizada. Si un icono predeterminado personalizado no está configurado en **AndroidManifest.xml** y no se ha establecido ningún icono en la carga de notificación, Android usa el icono de la aplicación como el icono de notificación (como se indica en el icono de notificación de la captura de pantalla anterior).

## <a name="handle-topic-messages"></a>Controlar mensajes de temas

El código escrito de forma que controla los tokens de registro y agrega la funcionalidad de notificación remota a la aplicación. En el ejemplo siguiente se agrega código que realiza escuchas para *los mensajes de tema* y los reenvía al usuario como notificaciones remotas. Los mensajes de tema son mensajes FCM que se envían a uno o varios dispositivos que se suscriben a un tema determinado. Para obtener más información acerca de los mensajes de tema, vea el [tema sobre mensajería](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md).

### <a name="subscribe-to-a-topic"></a>Suscripción a un tema

Edite **Resources/layout/main. axml** y agregue la siguiente `Button` declaración inmediatamente después del `Button` elemento anterior:

```xml
<Button
  android:id="@+id/subscribeButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:layout_marginTop="20dp"
  android:text="Subscribe to Notifications" />
```

Este XML agrega un botón de **suscripción a la notificación** al diseño.
Edite **MainActivity.CS** y agregue el código siguiente al final del `OnCreate` método:

```csharp
var subscribeButton = FindViewById<Button>(Resource.Id.subscribeButton);
subscribeButton.Click += delegate {
    FirebaseMessaging.Instance.SubscribeToTopic("news");
    Log.Debug(TAG, "Subscribed to remote notifications");
};
```

Este código busca el botón **suscribirse a notificación** en el diseño y asigna el controlador de clic al código que llama `FirebaseMessaging.Instance.SubscribeToTopic` , pasando el tema suscrito, _noticias_. Cuando el usuario pulsa el botón **subscribe** , la aplicación se suscribe al tema _News_ . En la siguiente sección, se enviará un mensaje de tema de _noticias_ desde la GUI de notificaciones de la consola de Firebase.

### <a name="send-a-topic-message"></a>Enviar un mensaje de tema

Desinstale la aplicación, vuelva a compilarla y ejecútela de nuevo. Haga clic en el botón **suscribirse a notificaciones** :

[![Botón suscribirse a notificaciones](remote-notifications-with-fcm-images/14-subscribe-sml.png)](remote-notifications-with-fcm-images/14-subscribe.png#lightbox)

Si la aplicación se ha suscrito correctamente, debería ver el **tema la sincronización se realizó correctamente** en la ventana de salida del IDE:

[![La ventana de salida muestra el mensaje sincronización correcta del tema](remote-notifications-with-fcm-images/15-topic-sync-sml.png)](remote-notifications-with-fcm-images/15-topic-sync.png#lightbox)

Siga estos pasos para enviar un mensaje de tema:

1. En la consola de Firebase, haga clic en **nuevo mensaje**.

2. En la página **redactar mensaje** , escriba el texto del mensaje y seleccione  **tema**.

3. En el menú desplegable del **tema** , seleccione el tema integrado  **News**:

    [![Seleccionar el tema de noticias](remote-notifications-with-fcm-images/16-topic-message-sml.png)](remote-notifications-with-fcm-images/16-topic-message.png#lightbox)

4. En el dispositivo (o emulador) Android, en segundo plano, puntee en el botón **información general** de Android y toque en la pantalla principal.

5. Cuando el dispositivo esté listo, haga clic en **Enviar mensaje** en la consola de Firebase.

6. Compruebe la ventana de salida del IDE para ver **/topics/News** en la salida del registro:

    [![Se muestra el mensaje de/topic/News](remote-notifications-with-fcm-images/17-message-arrived-sml.png)](remote-notifications-with-fcm-images/17-message-arrived.png#lightbox)

Cuando este mensaje se muestra en la ventana de salida, el icono de notificación también debe aparecer en el área de notificación del dispositivo Android. Abra el icono de notificación para ver el mensaje del tema:

[![El mensaje del tema aparece como una notificación](remote-notifications-with-fcm-images/18-other-news-sml.png)](remote-notifications-with-fcm-images/18-other-news.png#lightbox)

Si no recibe un mensaje, intente eliminar la aplicación **FCMClient** en el dispositivo (o emulador) y repita los pasos anteriores.

## <a name="foreground-notifications"></a>Notificaciones en primer plano

Para recibir notificaciones en aplicaciones en primer plano, debe implementar `FirebaseMessagingService` . Este servicio también es necesario para recibir cargas de datos y para enviar mensajes de nivel superior. En los siguientes ejemplos se muestra cómo implementar un servicio que extiende `FirebaseMessagingService` &ndash; la aplicación resultante podrá controlar las notificaciones remotas mientras se ejecuta en primer plano.

### <a name="implement-firebasemessagingservice"></a>Implementación de FirebaseMessagingService

El `FirebaseMessagingService` servicio es responsable de recibir y procesar los mensajes de Firebase. Cada aplicación debe subclase de este tipo e invalidar `OnMessageReceived` para procesar un mensaje entrante. Cuando una aplicación está en primer plano, la `OnMessageReceived` devolución de llamada siempre controlará el mensaje.

> [!NOTE]
> Las aplicaciones solo tienen 10 segundos para controlar un mensaje de nube de Firebase entrante. Cualquier trabajo que tarde más tiempo se debe programar para la ejecución en segundo plano mediante una biblioteca como el [programador de trabajos de Android](~/android/platform/android-job-scheduler.md) o el distribuidor de trabajos de [Firebase](~/android/platform/firebase-job-dispatcher.md).

Agregue un nuevo archivo denominado **MyFirebaseMessagingService.CS** y reemplace el código de plantilla por lo siguiente:

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Media;
using Android.Util;
using Firebase.Messaging;

namespace FCMClient
{
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class MyFirebaseMessagingService : FirebaseMessagingService
    {
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
        }
    }
}
```

Tenga en cuenta que el `MESSAGING_EVENT` filtro de intención se debe declarar para que los mensajes de FCM nuevos se dirijan a `MyFirebaseMessagingService` :

```csharp
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
```

Cuando la aplicación cliente recibe un mensaje de FCM, `OnMessageReceived` extrae el contenido del mensaje del objeto pasado llamando `RemoteMessage` a su `GetNotification` método. Después, registra el contenido del mensaje para que se pueda ver en la ventana de salida del IDE:

```csharp
var body = message.GetNotification().Body;
Log.Debug(TAG, "Notification Message Body: " + body);
```

> [!NOTE]
> Si establece puntos de interrupción en `FirebaseMessagingService` , la sesión de depuración puede o no alcanzar estos puntos de interrupción debido al modo en que FCM entrega los mensajes.

### <a name="send-another-message"></a>Enviar otro mensaje

Desinstale la aplicación, vuelva a compilarla, ejecútela de nuevo y siga estos pasos para enviar otro mensaje:

1. En la consola de Firebase, haga clic en **nuevo mensaje**.

2. En la página **redactar mensaje** , escriba el texto del mensaje y seleccione  **dispositivo único**.

3. Copie la cadena de token de la ventana de salida del IDE y péguela en el campo **token de registro de FCM** de la consola de Firebase como antes.

4. Asegúrese de que la aplicación se está ejecutando en primer plano y haga clic en **Enviar mensaje** en la consola de Firebase:

    [![Envío de otro mensaje desde la consola](remote-notifications-with-fcm-images/19-hello-again-sml.png)](remote-notifications-with-fcm-images/19-hello-again.png#lightbox)

5. Cuando aparezca el cuadro de diálogo **Revisar mensaje** , haga clic en **Enviar**.

6. El mensaje entrante se registra en la ventana de salida del IDE:

    [![Cuerpo del mensaje impreso en la ventana de salida](remote-notifications-with-fcm-images/20-logged-message.png)](remote-notifications-with-fcm-images/20-logged-message.png#lightbox)

### <a name="add-a-local-notification-sender"></a>Agregar un remitente de notificación local

En este ejemplo restante, el mensaje FCM entrante se convertirá en una notificación local que se inicia mientras la aplicación se ejecuta en primer plano. Edite **MyFirebaseMessageService.CS** y agregue las siguientes `using` instrucciones:

```csharp
using FCMClient;
using System.Collections.Generic;
```

Agregue el método siguiente a `MyFirebaseMessagingService`:

<a name="sendnotification-method"></a>

```csharp
void SendNotification(string messageBody, IDictionary<string, string> data)
{
    var intent = new Intent(this, typeof(MainActivity));
    intent.AddFlags(ActivityFlags.ClearTop);
    foreach (var key in data.Keys)
    {
        intent.PutExtra(key, data[key]);
    }

    var pendingIntent = PendingIntent.GetActivity(this,
                                                  MainActivity.NOTIFICATION_ID,
                                                  intent,
                                                  PendingIntentFlags.OneShot);

    var notificationBuilder = new  NotificationCompat.Builder(this, MainActivity.CHANNEL_ID)
                              .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                              .SetContentTitle("FCM Message")
                              .SetContentText(messageBody)
                              .SetAutoCancel(true)
                              .SetContentIntent(pendingIntent);

    var notificationManager = NotificationManagerCompat.From(this);
    notificationManager.Notify(MainActivity.NOTIFICATION_ID, notificationBuilder.Build());
}
```

Para distinguir esta notificación de las notificaciones en segundo plano, este código marca las notificaciones con un icono que difiere del icono de la aplicación. Agregue el archivo [IC \_ stat \_ STAT \_notification.png](remote-notifications-with-fcm-images/ic-stat-ic-notification.png) a **Resources/drawable** e inclúyalo en el proyecto **FCMClient** .

El `SendNotification` método utiliza `NotificationCompat.Builder` para crear la notificación y `NotificationManagerCompat` se utiliza para iniciar la notificación. La notificación contiene un `PendingIntent` que permitirá al usuario abrir la aplicación y ver el contenido de la cadena pasada en `messageBody` . Para obtener más información acerca de `NotificationCompat.Builder` , consulte [notificaciones locales](~/android/app-fundamentals/notifications/local-notifications.md).

Llame al `SendNotification` método al final del `OnMessageReceived` método:

```csharp
public override void OnMessageReceived(RemoteMessage message)
{
    Log.Debug(TAG, "From: " + message.From);

    var body = message.GetNotification().Body;
    Log.Debug(TAG, "Notification Message Body: " + body);
    SendNotification(body, message.Data);
}
```

Como resultado de estos cambios, `SendNotification` se ejecutará siempre que se reciba una notificación mientras la aplicación está en primer plano y la notificación aparecerá en el área de notificación.

Cuando una aplicación está en segundo plano, la [carga útil del mensaje](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages) determinará cómo se trata el mensaje:

- **Notificación** &ndash; de los mensajes se enviarán a la **bandeja del sistema**. Aparecerá una notificación local. Cuando el usuario pulsa la notificación, se iniciará la aplicación.
- **Datos** &ndash; de administrará los mensajes `OnMessageReceived` .
- **Ambos** &ndash; los mensajes que tienen una notificación y una carga de datos se entregarán a la bandeja del sistema. Cuando se inicia la aplicación, la carga de datos aparecerá en el `Extras` del `Intent` que se usó para iniciar la aplicación.

En este ejemplo, si la aplicación está en segundo plano, `SendNotification` se ejecutará si el mensaje tiene una carga de datos. De lo contrario, se iniciará una notificación en segundo plano (ilustrada anteriormente en este tutorial).

### <a name="send-the-last-message"></a>Enviar el último mensaje

Desinstale la aplicación, vuelva a compilarla, ejecútela de nuevo y, después, siga los pasos siguientes para enviar el último mensaje:

1. En la consola de Firebase, haga clic en **nuevo mensaje**.

2. En la página **redactar mensaje** , escriba el texto del mensaje y seleccione **dispositivo único**.

3. Copie la cadena de token de la ventana de salida del IDE y péguela en el campo **token de registro de FCM** de la consola de Firebase como antes.

4. Asegúrese de que la aplicación se está ejecutando en primer plano y haga clic en **Enviar mensaje** en la consola de Firebase:

    [![Enviar el mensaje en primer plano](remote-notifications-with-fcm-images/21-console-fg-msg-sml.png)](remote-notifications-with-fcm-images/21-console-fg-msg.png#lightbox)

Esta vez, el mensaje que se registró en la ventana de salida también se empaqueta en una notificación nueva &ndash; el icono de notificación aparece en la bandeja de notificación mientras la aplicación se ejecuta en primer plano:

[![Icono de notificación de mensaje de primer plano](remote-notifications-with-fcm-images/22-foreground-icon-sml.png)](remote-notifications-with-fcm-images/22-foreground-icon.png#lightbox)

Al abrir la notificación, debería ver el último mensaje enviado desde la GUI de notificaciones de la consola de Firebase:

[![Notificación en primer plano que se muestra con el icono de primer plano](remote-notifications-with-fcm-images/23-foreground-msg-sml.png)](remote-notifications-with-fcm-images/23-foreground-msg.png#lightbox)

## <a name="disconnecting-from-fcm"></a>Desconectar de FCM

Para cancelar la suscripción a un tema, llame al método [UnsubscribeFromTopic](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging.html#unsubscribeFromTopic%28java.lang.String%29) en la clase [FirebaseMessaging](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging) . Por ejemplo, para cancelar la suscripción al tema de _noticias_ suscrito anteriormente, se podría agregar un botón de **anulación de suscripción** al diseño con el siguiente código de controlador:

```csharp
var unSubscribeButton = FindViewById<Button>(Resource.Id.unsubscribeButton);
unSubscribeButton.Click += delegate {
    FirebaseMessaging.Instance.UnsubscribeFromTopic("news");
    Log.Debug(TAG, "Unsubscribed from remote notifications");
};
```

Para anular por completo el registro del dispositivo de FCM, elimine el identificador de instancia llamando al método [DeleteInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId.html#deleteInstanceId%28%29) en la clase [FirebaseInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId) . Por ejemplo:

```csharp
FirebaseInstanceId.Instance.DeleteInstanceId();
```

Esta llamada al método elimina el identificador de instancia y los datos asociados a él. Como resultado, se detiene el envío periódico de datos de FCM al dispositivo.

## <a name="troubleshooting"></a>Solución de problemas

A continuación se describen los problemas y las soluciones alternativas que pueden surgir al usar Firebase Cloud Messaging con Xamarin. Android.

### <a name="firebaseapp-is-not-initialized"></a>FirebaseApp no se ha inicializado

En algunos casos, puede ver este mensaje de error:

```shell
Java.Lang.IllegalStateException: Default FirebaseApp is not initialized in this process
Make sure to call FirebaseApp.initializeApp(Context) first.
```

Se trata de un problema conocido que puede solucionar limpiando la solución y recompilando el proyecto (**compilación > limpiar solución**, **compilar > recompilar solución**).

## <a name="summary"></a>Resumen

En este tutorial se detallan los pasos para implementar notificaciones remotas de mensajería en la nube de Firebase en una aplicación de Xamarin. Android. Se describe cómo instalar los paquetes necesarios para las comunicaciones de FCM y se explica cómo configurar el manifiesto de Android para el acceso a los servidores de FCM. Proporciona código de ejemplo que muestra cómo comprobar la presencia de Google Play Services. Se ha mostrado cómo implementar un servicio de escucha de ID. de instancia que negocia con FCM para un token de registro y se explicó cómo este código crea notificaciones en segundo plano mientras la aplicación está en el fondo. Se ha explicado cómo suscribirse a los mensajes de tema y se ha proporcionado un ejemplo de implementación de un servicio de escucha de mensajes que se usa para recibir y Mostrar notificaciones remotas mientras la aplicación se ejecuta en primer plano.

## <a name="related-links"></a>Vínculos relacionados

- [FCMNotifications (ejemplo)](/samples/xamarin/monodroid-samples/firebase-fcmnotifications)
- [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
- [Acerca de los mensajes de FCM](https://firebase.google.com/docs/cloud-messaging/concept-options)