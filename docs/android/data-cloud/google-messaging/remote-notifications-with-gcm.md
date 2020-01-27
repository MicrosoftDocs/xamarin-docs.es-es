---
title: Notificaciones remotas con Google Cloud Messaging
description: En este tutorial se proporciona una explicación paso a paso de cómo usar Google Cloud Messaging para implementar notificaciones remotas (también denominadas notificaciones de envío) en una aplicación de Xamarin. Android. Se describen las distintas clases que debe implementar para comunicarse con Google Cloud Messaging (GCM), se explica cómo establecer permisos en el manifiesto de Android para el acceso a GCM y se muestra la mensajería de un extremo a otro con un programa de prueba de ejemplo.
ms.prod: xamarin
ms.assetid: 4FC3C774-EF93-41B2-A81E-C6A08F32C09B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/02/2019
ms.openlocfilehash: b621d61584cc39f669c662db3d1df264d9a92eb9
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76723777"
---
# <a name="remote-notifications-with-google-cloud-messaging"></a>Notificaciones remotas con Google Cloud Messaging

> [!WARNING]
> Google dejó de usar GCM a partir del 10 de abril de 2018. Es posible que los siguientes documentos y proyectos de ejemplo ya no se mantengan. El servidor y las API de cliente de GCM de Google se quitarán en cuanto 29 de mayo de 2019. Google recomienda migrar aplicaciones de GCM a Firebase Cloud Messaging (FCM). Para obtener más información sobre el desuso y la migración de GCM, vea [Google Cloud Messaging-deprecated](https://developers.google.com/cloud-messaging/).
>
> Para empezar a trabajar con las notificaciones remotas con la mensajería en la nube Firebase con Xamarin, consulte [notificaciones remotas con FCM](remote-notifications-with-fcm.md).

_En este tutorial se proporciona una explicación paso a paso de cómo usar Google Cloud Messaging para implementar notificaciones remotas (también denominadas notificaciones de envío) en una aplicación de Xamarin. Android. Se describen las distintas clases que debe implementar para comunicarse con Google Cloud Messaging (GCM), se explica cómo establecer permisos en el manifiesto de Android para el acceso a GCM y se muestra la mensajería de un extremo a otro con un programa de prueba de ejemplo._

## <a name="gcm-notifications-overview"></a>Información general sobre las notificaciones de GCM

En este tutorial, vamos a crear una aplicación de Xamarin. Android que usa Google Cloud Messaging (GCM) para implementar notificaciones remotas (también conocidas como *notificaciones de envío*). Implementaremos los diversos servicios de intención y escucha que usan GCM para la mensajería remota y probaremos nuestra implementación con un programa de línea de comandos que simula un servidor de aplicaciones.

Para poder continuar con este tutorial, debe adquirir las credenciales necesarias para usar los servidores GCM de Google. Este proceso se explica en [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md).
En concreto, necesitará una *clave de API* y un *identificador de remitente* para insertarlos en el código de ejemplo que se muestra en este tutorial.

Usaremos los pasos siguientes para crear una aplicación cliente de Xamarin. Android habilitada para GCM:

1. Instale paquetes adicionales necesarios para las comunicaciones con servidores GCM.
2. Configure los permisos de la aplicación para el acceso a los servidores GCM.
3. Implemente el código para comprobar la presencia de Google Play Services.
4. Implemente un servicio de intención de registro que negocie con GCM para un token de registro.
5. Implemente un servicio de escucha de ID. de instancia que escuche las actualizaciones de token de registro de GCM.
6. Implemente un servicio de escucha de GCM que reciba mensajes remotos del servidor de aplicaciones a través de GCM.

Esta aplicación usará una nueva característica de GCM conocida como *mensajería de temas*. En mensajería de tema, el servidor de aplicaciones envía un mensaje a un tema, en lugar de a una lista de dispositivos individuales. Los dispositivos que se suscriben a ese tema pueden recibir mensajes de tema como notificaciones de envío.

Cuando la aplicación cliente esté lista, implementaremos una aplicación de línea C# de comandos que envíe una notificación de inserción a nuestra aplicación cliente a través de GCM.

## <a name="walkthrough"></a>Tutorial

Para empezar, vamos a crear una nueva solución vacía denominada **RemoteNotifications**. A continuación, vamos a agregar un nuevo proyecto de Android a esta solución que se basa en la plantilla de **aplicación de Android** . Vamos a llamar a este proyecto **ClientApp**. (Si no está familiarizado con la creación de proyectos de Xamarin. Android, consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md)). El proyecto **ClientApp** contendrá el código para la aplicación cliente de Xamarin. Android que recibe notificaciones remotas a través de GCM.

### <a name="add-required-packages"></a>Agregar paquetes necesarios

Antes de poder implementar el código de la aplicación cliente, debemos instalar varios paquetes que usaremos para la comunicación con GCM. Además, debemos agregar la aplicación Google Play Store a nuestro dispositivo si aún no está instalado.

#### <a name="add-the-xamarin-google-play-services-gcm-package"></a>Agregar el paquete de Xamarin Google Play Services GCM

Para recibir mensajes de Google Cloud Messaging, el marco de [Google Play Services](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Gcm/) debe estar presente en el dispositivo. Sin este marco de trabajo, una aplicación Android no puede recibir mensajes de servidores GCM. Google Play Services se ejecuta en segundo plano mientras el dispositivo Android está encendido y escucha los mensajes de GCM de forma silenciosa. Cuando llegan estos mensajes, Google Play Services los convierte en intentos y, a continuación, difunde estos intentos a las aplicaciones que se han registrado para ellos.

En Visual Studio, haga clic con el botón derecho en **referencias > administrar paquetes NuGet...** ; en Visual Studio para Mac, haga clic con el botón derecho en **paquetes > agregar paquetes.** ... Busque **Xamarin Google Play Services-GCM** e instale este paquete en el proyecto **ClientApp** :

[![instalar Google Play Services](remote-notifications-with-gcm-images/1-google-play-services-sml.png)](remote-notifications-with-gcm-images/1-google-play-services.png#lightbox)

Al instalar **xamarin Google Play Services-GCM**, **Xamarin Google Play Services-base** se instala automáticamente. Si recibe un error, cambie la configuración mínima de *Android a destino* del proyecto a un valor distinto de **compilar con la versión del SDK** y vuelva a intentar la instalación de NuGet.

A continuación, edite **MainActivity.CS** y agregue las siguientes instrucciones de `using`:

```csharp
using Android.Gms.Common;
using Android.Util;
```

Esto hace que los tipos del paquete Google Play Services GMS estén disponibles para nuestro código y agrega la funcionalidad de registro que se usará para realizar el seguimiento de las transacciones con GMS.

#### <a name="google-play-store"></a>Google Play Store

Para recibir mensajes de GCM, la aplicación Google Play Store debe estar instalada en el dispositivo. (Siempre que se instala una aplicación Google Play en un dispositivo, Google Play Store también se instala, por lo que es probable que ya esté instalado en el dispositivo de prueba). Sin Google Play, una aplicación Android no puede recibir mensajes de GCM.
Si todavía no tiene la aplicación Google Play Store instalada en el dispositivo, visite el sitio web de [Google Play](https://support.google.com/googleplay) para descargar e instalar Google Play.

Como alternativa, puede usar un emulador de Android que ejecute Android 2,2 o posterior en lugar de un dispositivo de prueba (no es necesario instalar Google Play Store en un emulador de Android). Sin embargo, si usa un emulador, debe usar Wi-Fi para conectarse a GCM y debe abrir varios puertos en el Firewall de Wi-Fi, como se explica más adelante en este tutorial.

### <a name="set-the-package-name"></a>Establecer el nombre del paquete

En [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md), se especificó un nombre de paquete para nuestra aplicación habilitada para GCM (este nombre de paquete también sirve como el identificador de la *aplicación* que está asociado a nuestra clave de API y al identificador del remitente). Vamos a abrir las propiedades del proyecto **ClientApp** y establecer el nombre del paquete en esta cadena. En este ejemplo, se establece el nombre del paquete en `com.xamarin.gcmexample`:

[![establecer el nombre del paquete](remote-notifications-with-gcm-images/2-package-name-sml.png)](remote-notifications-with-gcm-images/2-package-name.png#lightbox)

Tenga en cuenta que la aplicación cliente no podrá recibir un token de registro de GCM si el nombre del paquete no coincide *exactamente* con el nombre del paquete que hemos escrito en la consola para desarrolladores de Google.

### <a name="add-permissions-to-the-android-manifest"></a>Agregar permisos al manifiesto de Android

Una aplicación Android debe tener configurados los permisos siguientes para poder recibir notificaciones de Google Cloud Messaging:

- `com.google.android.c2dm.permission.RECEIVE` &ndash; concede permiso a la aplicación para registrar y recibir mensajes de Google Cloud Messaging. (¿Qué significa `c2dm`? Esto significa la _mensajería de la nube al dispositivo_, que es la predecesora ahora desusada para GCM.
    GCM todavía usa `c2dm` en muchas de sus cadenas de permiso).

- `android.permission.WAKE_LOCK` &ndash; (opcional) impide que la CPU del dispositivo pase a suspensión mientras escucha un mensaje.

- `android.permission.INTERNET` &ndash; concede acceso a Internet para que la aplicación cliente pueda comunicarse con GCM.

- *package_name*`.permission.C2D_MESSAGE` &ndash; registra la aplicación con Android y solicita permiso para recibir exclusivamente todos los mensajes C2D (de la nube al dispositivo). El prefijo *package_name* es el mismo que el identificador de la aplicación.

Estableceremos estos permisos en el manifiesto de Android. Vamos a editar **archivo AndroidManifest. XML** y reemplazar el contenido por el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="YOUR_PACKAGE_NAME"
    android:versionCode="1"
    android:versionName="1.0"
    android:installLocation="auto">
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="YOUR_PACKAGE_NAME.permission.C2D_MESSAGE" />
    <permission android:name="YOUR_PACKAGE_NAME.permission.C2D_MESSAGE"
                android:protectionLevel="signature" />
    <application android:label="ClientApp" android:icon="@drawable/Icon">
    </application>
</manifest>
```

En el XML anterior, cambie *YOUR_PACKAGE_NAME* por el nombre del paquete para el proyecto de aplicación cliente. Por ejemplo: `com.xamarin.gcmexample`.

### <a name="check-for-google-play-services"></a>Comprobar Google Play Services

En este tutorial, vamos a crear una aplicación sin sistema operativo con un solo `TextView` en la interfaz de usuario. Esta aplicación no indica directamente la interacción con GCM. En su lugar, veremos la ventana de salida para ver cómo se realiza el enlace de la aplicación con GCM y buscaremos en la bandeja de notificación las nuevas notificaciones a medida que lleguen.

En primer lugar, vamos a crear un diseño para el área de mensajes. Edite **Resources. layout. Main. axml** y reemplace el contenido por el siguiente código XML:

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

Guarde **Main. axml** y ciérrelo.

Cuando se inicia la aplicación cliente, queremos comprobar que Google Play Services está disponible antes de intentar ponerse en contacto con GCM. Edite **MainActivity.CS** y reemplace la declaración de variable de instancia ``count`` por la siguiente declaración de variable de instancia:

```csharp
TextView msgText;
```

A continuación, agregue el método siguiente a la clase **MainActivity** :

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
            msgText.Text = "Sorry, this device is not supported";
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

Este código comprueba el dispositivo para ver si está instalado el Google Play Services APK. Si no está instalado, se muestra un mensaje en el área de mensajes que indica al usuario que descargue un APK del Google Play Store (o habilítelo en la configuración del sistema del dispositivo). Dado que deseamos ejecutar esta comprobación cuando se inicia la aplicación cliente, agregaremos una llamada a este método al final de `OnCreate`.

A continuación, reemplace el método `OnCreate` por el código siguiente:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();
}
```

Este código comprueba la presencia del Google Play Services APK y escribe el resultado en el área de mensajes.

Vamos a volver a compilar y ejecutar la aplicación por completo. Debería ver una pantalla similar a la siguiente captura de pantalla:

[![Google Play Services está disponible](remote-notifications-with-gcm-images/3-first-screen-sml.png)](remote-notifications-with-gcm-images/3-first-screen.png#lightbox)

Si no obtiene este resultado, compruebe que el Google Play Services APK está instalado en el dispositivo y que el paquete **Xamarin Google Play Services-GCM** se agrega al proyecto **ClientApp** como se explicó anteriormente. Si obtiene un error de compilación, intente limpiar la solución y compilar el proyecto de nuevo.

A continuación, escribiremos código para ponerse en contacto con GCM y obtener un token de registro.

### <a name="register-with-gcm"></a>Registro con GCM

Antes de que la aplicación pueda recibir notificaciones remotas del servidor de aplicaciones, debe registrarse en GCM y obtener un token de registro. El trabajo de registro de la aplicación con GCM se controla mediante una `IntentService` que se crean. En el `IntentService` se realizan los pasos siguientes:

1. Usa la API [InstanceID](https://developers.google.com/instance-id/) para generar tokens de seguridad que autorizan a la aplicación cliente a acceder al servidor de aplicaciones. A cambio, recibimos un token de registro de GCM.

2. Reenvía el token de registro al servidor de aplicaciones (si el servidor de aplicaciones lo requiere).

3. Se suscribe a uno o varios canales de temas de notificación.

Después de implementar este `IntentService`, lo probaremos para ver si obtenemos un token de registro de GCM.

Agregue un nuevo archivo denominado **RegistrationIntentService.CS** y reemplace el código de plantilla por lo siguiente:

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Util;
using Android.Gms.Gcm;
using Android.Gms.Gcm.Iid;

namespace ClientApp
{
    [Service(Exported = false)]
    class RegistrationIntentService : IntentService
    {
        static object locker = new object();

        public RegistrationIntentService() : base("RegistrationIntentService") { }

        protected override void OnHandleIntent (Intent intent)
        {
            try
            {
                Log.Info ("RegistrationIntentService", "Calling InstanceID.GetToken");
                lock (locker)
                {
                    var instanceID = InstanceID.GetInstance (this);
                    var token = instanceID.GetToken (
                        "YOUR_SENDER_ID", GoogleCloudMessaging.InstanceIdScope, null);

                    Log.Info ("RegistrationIntentService", "GCM Registration Token: " + token);
                    SendRegistrationToAppServer (token);
                    Subscribe (token);
                }
            }
            catch (Exception e)
            {
                Log.Debug("RegistrationIntentService", "Failed to get a registration token");
                return;
            }
        }

        void SendRegistrationToAppServer (string token)
        {
            // Add custom implementation here as needed.
        }

        void Subscribe (string token)
        {
            var pubSub = GcmPubSub.GetInstance(this);
            pubSub.Subscribe(token, "/topics/global", null);
        }
    }
}
```

En el código de ejemplo anterior, cambie *YOUR_SENDER_ID* al número de identificación del remitente del proyecto de la aplicación cliente. Para obtener el identificador del remitente del proyecto:

1. Inicie sesión en la [consola de Google Cloud](https://console.cloud.google.com/) y seleccione el nombre del proyecto en el menú desplegable. En el panel de **información del proyecto** que se muestra para el proyecto, haga clic en **ir a configuración del proyecto**:

    [![seleccionar el proyecto XamarinGCM](remote-notifications-with-gcm-images/7-choose-project-sml.png)](remote-notifications-with-gcm-images/7-choose-project.png#lightbox)

2. En la página **configuración** , busque el **número de proyecto** &ndash; este es el identificador del remitente del proyecto:

    [![número de proyecto mostrado](remote-notifications-with-gcm-images/9-project-number-sml.png)](remote-notifications-with-gcm-images/9-project-number.png#lightbox)

Queremos iniciar el `RegistrationIntentService` cuando la aplicación comience a ejecutarse. Edite **MainActivity.CS** y modifique el método de `OnCreate` para que se inicie el `RegistrationIntentService` después de comprobar la presencia de Google Play Services:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView(Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    if (IsPlayServicesAvailable ())
    {
        var intent = new Intent (this, typeof (RegistrationIntentService));
        StartService (intent);
    }
}
```

Ahora, echemos un vistazo a cada sección de `RegistrationIntentService` para entender cómo funciona.

En primer lugar, anotamos el `RegistrationIntentService` con el siguiente atributo para indicar que el sistema no debe crear instancias del servicio:

```csharp
[Service (Exported = false)]
```

El constructor `RegistrationIntentService` nombra el subproceso de trabajo *RegistrationIntentService* para facilitar la depuración.

```csharp
public RegistrationIntentService() : base ("RegistrationIntentService") { }
```

La funcionalidad básica de `RegistrationIntentService` reside en el método `OnHandleIntent`. Vamos a examinar este código para ver cómo registra nuestra aplicación con GCM.

#### <a name="request-a-registration-token"></a>Solicitar un token de registro

`OnHandleIntent` primero llama al método [InstanceID. GetToken](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID.html#getToken&#40;java.lang.String,%20java.lang.String&#41;) de Google para solicitar un token de registro de GCM. Encapsulamos este código en un `lock` para protegerse frente a la posibilidad de que se produzcan varios intentos de registro al mismo tiempo &ndash; la `lock` garantiza que estos intentos se procesan de forma secuencial. Si no se puede obtener un token de registro, se produce una excepción y se registra un error. Si el registro se realiza correctamente, `token` se establece en el token de registro que obtuvimos de GCM:

```csharp
static object locker = new object ();
...
try
{
    lock (locker)
    {
        var instanceID = InstanceID.GetInstance (this);
        var token = instanceID.GetToken (
            "YOUR_SENDER_ID", GoogleCloudMessaging.InstanceIdScope, null);
        ...
    }
}
catch (Exception e)
{
    Log.Debug ...
```

#### <a name="forward-the-registration-token-to-the-app-server"></a>Reenviar el token de registro al servidor de aplicaciones

Si obtenemos un token de registro (es decir, no se produjo ninguna excepción), llamamos `SendRegistrationToAppServer` para asociar el token de registro del usuario con la cuenta del lado servidor (si existe) que mantiene nuestra aplicación. Dado que esta implementación depende del diseño del servidor de aplicaciones, se proporciona un método vacío aquí:

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

En algunos casos, el servidor de aplicaciones no necesita el token de registro del usuario. en ese caso, se puede omitir este método. Cuando se envía un token de registro al servidor de aplicaciones, `SendRegistrationToAppServer` debe mantener un valor booleano para indicar si el token se ha enviado al servidor. Si este valor booleano es false, `SendRegistrationToAppServer` envía el token al servidor de aplicaciones &ndash; de lo contrario, el token ya se envió al servidor de aplicaciones en una llamada anterior.

#### <a name="subscribe-to-the-notification-topic"></a>Suscribirse al tema de notificación

A continuación, llamamos al método `Subscribe` para indicar a GCM que queremos suscribirte a un tema de notificación. En `Subscribe`, llamamos a la API GcmPubSub. subscribe para suscribir la aplicación cliente a todos los mensajes en `/topics/global`:

```csharp
void Subscribe (string token)
{
    var pubSub = GcmPubSub.GetInstance(this);
    pubSub.Subscribe(token, "/topics/global", null);
}
```

El servidor de aplicaciones debe enviar mensajes de notificación a `/topics/global` si se van a recibir. Tenga en cuenta que el nombre del tema en `/topics` puede ser cualquier cosa que desee, siempre que el servidor de aplicaciones y la aplicación cliente acuerden estos nombres. (Aquí, elegimos el nombre `global` para indicar que queremos recibir mensajes en todos los temas admitidos por el servidor de aplicaciones).

#### <a name="implement-an-instance-id-listener-service"></a>Implementar un servicio de escucha de ID. de instancia

Los tokens de registro son únicos y seguros. sin embargo, es posible que la aplicación cliente (o GCM) necesite actualizar el token de registro en caso de que se vuelva a instalar la aplicación o un problema de seguridad. Por esta razón, debemos implementar una `InstanceIdListenerService` que responda a las solicitudes de actualización de tokens de GCM.

Agregue un nuevo archivo denominado **InstanceIdListenerService.CS** y reemplace el código de plantilla por lo siguiente:

```csharp
using Android.App;
using Android.Content;
using Android.Gms.Gcm.Iid;

namespace ClientApp
{
    [Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
    class MyInstanceIDListenerService : InstanceIDListenerService
    {
        public override void OnTokenRefresh()
        {
            var intent = new Intent (this, typeof (RegistrationIntentService));
            StartService (intent);
        }
    }
}
```

Anote `InstanceIdListenerService` con el siguiente atributo para indicar que el sistema no debe crear una instancia del servicio y que puede recibir solicitudes de actualización del token de registro de GCM (también denominado *identificador de instancia*):

```csharp
[Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
```

El método `OnTokenRefresh` en nuestro servicio inicia el `RegistrationIntentService` para que pueda interceptar el nuevo token de registro.

#### <a name="test-registration-with-gcm"></a>Registro de prueba con GCM

Vamos a volver a compilar y ejecutar la aplicación por completo. Si recibe correctamente un token de registro de GCM, el token de registro debe mostrarse en la ventana de salida. Por ejemplo:

```shell
D/Mono    ( 1934): Assembly Ref addref ClientApp[0xb4ac2400] -> Xamarin.GooglePlayServices.Gcm[0xb4ac2640]: 2
I/RegistrationIntentService( 1934): Calling InstanceID.GetToken
I/RegistrationIntentService( 1934): GCM Registration Token: f8LdveCvXig:APA91bFIsjUAbP-V8TPQdLR89qQbEJh1SYG38AcCbBUf34z5gSdUc5OsXrgs93YFiGcRSRafPfzkz23lf3-LvYV1CwrFheMjHgwPeFSh12MywnRIhz

```

### <a name="handle-downstream-messages"></a>Controlar mensajes de nivel inferior

El código que hemos implementado hasta ahora es solo el código "configurado". comprueba si está instalado Google Play Services y negocia con GCM y el servidor de aplicaciones para preparar la aplicación cliente para recibir notificaciones remotas. Sin embargo, todavía hemos implementado código que realmente recibe y procesa los mensajes de notificación de nivel inferior. Para ello, debemos implementar un servicio de *escucha de GCM*. Este servicio recibe mensajes de tema del servidor de aplicaciones y los difunde localmente como notificaciones. Después de implementar este servicio, vamos a crear un programa de prueba para enviar mensajes a GCM, de modo que podamos ver si nuestra implementación funciona correctamente.

#### <a name="add-a-notification-icon"></a>Icono Agregar un aviso

En primer lugar, vamos a agregar un icono pequeño que aparecerá en el área de notificación cuando se inicie la notificación. Puede copiar [este icono](remote-notifications-with-gcm-images/ic-stat-ic-notification.png) en el proyecto o crear su propio icono personalizado. Asignaremos un nombre al archivo de icono **ic_stat_button_click. png** y lo copiaremos en la carpeta **Resources/drawable** . No olvide usar **agregar > elemento existente...** para incluir este archivo de icono en el proyecto.

#### <a name="implement-a-gcm-listener-service"></a>Implementar un servicio de escucha de GCM

Agregue un nuevo archivo denominado **GcmListenerService.CS** y reemplace el código de plantilla por lo siguiente:

```csharp
using Android.App;
using Android.Content;
using Android.OS;
using Android.Gms.Gcm;
using Android.Util;

namespace ClientApp
{
    [Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
    public class MyGcmListenerService : GcmListenerService
    {
        public override void OnMessageReceived (string from, Bundle data)
        {
            var message = data.GetString ("message");
            Log.Debug ("MyGcmListenerService", "From:    " + from);
            Log.Debug ("MyGcmListenerService", "Message: " + message);
            SendNotification (message);
        }

        void SendNotification (string message)
        {
            var intent = new Intent (this, typeof(MainActivity));
            intent.AddFlags (ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity (this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new Notification.Builder(this)
                .SetSmallIcon (Resource.Drawable.ic_stat_ic_notification)
                .SetContentTitle ("GCM Message")
                .SetContentText (message)
                .SetAutoCancel (true)
                .SetContentIntent (pendingIntent);

            var notificationManager = (NotificationManager)GetSystemService(Context.NotificationService);
            notificationManager.Notify (0, notificationBuilder.Build());
        }
    }
}
```

Echemos un vistazo a cada sección de nuestra `GcmListenerService` para entender cómo funciona.

En primer lugar, se anota `GcmListenerService` con un atributo para indicar que el sistema no debe crear instancias de este servicio, y se incluye un filtro de intención para indicar que recibe los mensajes de GCM:

```csharp
[Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
```

Cuando `GcmListenerService` recibe un mensaje de GCM, se invoca el método de `OnMessageReceived`. Este método extrae el contenido del mensaje del `Bundle`pasado, registra el contenido del mensaje (para que podamos verlo en la ventana de salida) y llama a `SendNotification` para iniciar una notificación local con el contenido del mensaje recibido:

```csharp
var message = data.GetString ("message");
Log.Debug ("MyGcmListenerService", "From:    " + from);
Log.Debug ("MyGcmListenerService", "Message: " + message);
SendNotification (message);
```

El método `SendNotification` usa `Notification.Builder` para crear la notificación y, a continuación, usa el `NotificationManager` para iniciar la notificación. De hecho, esto convierte el mensaje de notificación remota en una notificación local que se va a presentar al usuario.
Para obtener más información sobre el uso de `Notification.Builder` y `NotificationManager`, consulte [notificaciones locales](~/android/app-fundamentals/notifications/local-notifications.md).

#### <a name="declare-the-receiver-in-the-manifest"></a>Declarar el receptor en el manifiesto

Antes de poder recibir mensajes de GCM, debemos declarar el agente de escucha de GCM en el manifiesto de Android. Vamos a editar **archivo AndroidManifest. XML** y reemplazar la sección `<application>` por el siguiente código XML:

```xml
<application android:label="RemoteNotifications" android:icon="@drawable/Icon">
    <receiver android:name="com.google.android.gms.gcm.GcmReceiver"
              android:exported="true"
              android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="YOUR_PACKAGE_NAME" />
        </intent-filter>
    </receiver>
</application>
```

En el XML anterior, cambie *YOUR_PACKAGE_NAME* por el nombre del paquete para el proyecto de aplicación cliente. En el ejemplo del tutorial, el nombre del paquete es `com.xamarin.gcmexample`.

Echemos un vistazo a cada una de las opciones de este XML:

|Configuración de|Descripción|
|---|---|
|`com.google.android.gms.gcm.GcmReceiver`|Declara que nuestra aplicación implementa un receptor de GCM que captura y procesa los mensajes entrantes de notificaciones de entrada.|
|`com.google.android.c2dm.permission.SEND`|Declara que solo los servidores GCM pueden enviar mensajes directamente a la aplicación.|
|`com.google.android.c2dm.intent.RECEIVE`|El filtro de intención anuncia que nuestra aplicación controla los mensajes de difusión desde GCM.|
|`com.google.android.c2dm.intent.REGISTRATION`|El filtro de intención anuncia que nuestra aplicación controla nuevos intentos de registro (es decir, que hemos implementado un servicio de escucha de ID. de instancia).|

Como alternativa, puede decorar `GcmListenerService` con estos atributos en lugar de especificarlos en XML; Aquí se especifican en **archivo AndroidManifest. XML** para que los ejemplos de código sean más fáciles de seguir.

### <a name="create-a-message-sender-to-test-the-app"></a>Creación de un remitente de mensaje para probar la aplicación

Vamos a agregar un C# proyecto de aplicación de consola de escritorio a la solución y llamarlo **MessageSender**. Usaremos esta aplicación de consola para simular un servidor de aplicaciones &ndash; enviará mensajes de notificación a **ClientApp** a través de GCM.

#### <a name="add-the-jsonnet-package"></a>Agregar el paquete Json.NET

En esta aplicación de consola, vamos a crear una carga de JSON que contiene el mensaje de notificación que queremos enviar a la aplicación cliente. Usaremos el paquete **JSON.net** en **MessageSender** para que sea más fácil crear el objeto JSON que GCM necesita. En Visual Studio, haga clic con el botón derecho en **referencias > administrar paquetes NuGet...** ; en Visual Studio para Mac, haga clic con el botón derecho en **paquetes > agregar paquetes.** ...

Vamos a buscar el paquete **JSON.net** e instalarlo en el proyecto:

[![instalar el paquete Json.NET](remote-notifications-with-gcm-images/4-add-json.net-sml.png)](remote-notifications-with-gcm-images/4-add-json.net.png#lightbox)

#### <a name="add-a-reference-to-systemnethttp"></a>Agregar una referencia a System .net. http

También tendremos que agregar una referencia a `System.Net.Http` para poder crear instancias de un `HttpClient` para enviar el mensaje de prueba a GCM. En el proyecto **MessageSender** , haga clic con el botón secundario en **referencias > Agregar referencia** y desplácese hacia abajo hasta que vea **System .net. http**. Coloque una marca de verificación junto a **System .net. http** y haga clic en **Aceptar**.

#### <a name="implement-code-that-sends-a-test-message"></a>Implementar código que envía un mensaje de prueba

En **MessageSender**, edite **Program.CS** y reemplace el contenido por el código siguiente:

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json.Linq;

namespace MessageSender
{
    class MessageSender
    {
        public const string API_KEY = "YOUR_API_KEY";
        public const string MESSAGE = "Hello, Xamarin!";

        static void Main (string[] args)
        {
            var jGcmData = new JObject();
            var jData = new JObject();

            jData.Add ("message", MESSAGE);
            jGcmData.Add ("to", "/topics/global");
            jGcmData.Add ("data", jData);

            var url = new Uri ("https://gcm-http.googleapis.com/gcm/send");
            try
            {
                using (var client = new HttpClient())
                {
                    client.DefaultRequestHeaders.Accept.Add(
                        new MediaTypeWithQualityHeaderValue("application/json"));

                    client.DefaultRequestHeaders.TryAddWithoutValidation (
                        "Authorization", "key=" + API_KEY);

                    Task.WaitAll(client.PostAsync (url,
                        new StringContent(jGcmData.ToString(), Encoding.Default, "application/json"))
                            .ContinueWith(response =>
                            {
                                Console.WriteLine(response);
                                Console.WriteLine("Message sent: check the client device notification tray.");
                            }));
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Unable to send GCM message:");
                Console.Error.WriteLine(e.StackTrace);
            }
        }
    }
}
```

En el código anterior, cambie *YOUR_API_KEY* a la clave de API del proyecto de la aplicación cliente.

Este servidor de aplicaciones de prueba envía el siguiente mensaje con formato JSON a GCM:

```csharp
{
  "to": "/topics/global",
  "data": {
    "message": "Hello, Xamarin!"
  }
}
```

GCM, a su vez, reenvía este mensaje a la aplicación cliente. Vamos a compilar **MessageSender** y abrir una ventana de consola en la que se puede ejecutar desde la línea de comandos.

### <a name="try-it"></a>¡Inténtelo!

Ahora estamos listos para probar la aplicación cliente. Si usa un emulador o si el dispositivo se comunica con GCM a través de Wi-Fi, debe abrir los siguientes puertos TCP en el firewall para que los mensajes de GCM lleguen: 5228, 5229 y 5230.

Inicie la aplicación cliente y vea la ventana de salida. Una vez que el `RegistrationIntentService` recibe correctamente un token de registro de GCM, la ventana de salida debe mostrar el token con una salida de registro similar a la siguiente:

```shell
I/RegistrationIntentService(16103): GCM Registration Token: eX9ggabZV1Q:APA91bHjBnQXMUeBOT6JDiLpRt8m2YWtY ...
```

En este momento, la aplicación cliente está lista para recibir un mensaje de notificación remota. Desde la línea de comandos, ejecute el programa **MessageSender. exe** para enviar un mensaje de notificación "Hello, Xamarin" a la aplicación cliente.
Si aún no ha compilado el proyecto **MessageSender** , hágalo ahora.

Para ejecutar **MessageSender. exe** en Visual Studio, abra un símbolo del sistema, cambie al directorio **MessageSender/bin/Debug** y ejecute el comando directamente:

```cmd
MessageSender.exe
```

Para ejecutar **MessageSender. exe** en Visual Studio para Mac, abra una sesión de terminal, cambie a **MessageSender/bin/depure** el directorio y use mono para ejecutar **MessageSender. exe.**

```bash
mono MessageSender.exe
```

El mensaje puede tardar hasta un minuto en propagarse a través de GCM y volver a la aplicación cliente. Si el mensaje se recibe correctamente, debería ver un resultado similar al siguiente en la ventana de salida:

```shell
D/MyGcmListenerService(16103): From:    /topics/global
D/MyGcmListenerService(16103): Message: Hello, Xamarin!
```

Además, debe observar que aparece un nuevo icono de notificación en la bandeja de notificación:

[aparece el icono de notificación de ![en el dispositivo](remote-notifications-with-gcm-images/5-icon-appears-sml.png)](remote-notifications-with-gcm-images/5-icon-appears.png#lightbox)

Al abrir la bandeja de notificación para ver las notificaciones, debería ver nuestra notificación remota:

[![se muestra el mensaje de notificación](remote-notifications-with-gcm-images/6-notification-in-tray-sml.png)](remote-notifications-with-gcm-images/6-notification-in-tray.png#lightbox)

Enhorabuena, la aplicación ha recibido su primera notificación remota.

Tenga en cuenta que ya no se recibirán mensajes de GCM si la aplicación se detiene forzosamente. Para reanudar las notificaciones después de una detención forzada, la aplicación debe reiniciarse manualmente. Para más información sobre esta directiva de Android, consulte [Inicio de controles en aplicaciones detenidas](https://developer.android.com/about/versions/android-3.1.html#launchcontrols) y este [envío de desbordamiento de pila](https://stackoverflow.com/questions/5051687/broadcastreceiver-not-receiving-boot-completed/19856267#19856267).

## <a name="summary"></a>Resumen

En este tutorial se detallan los pasos para implementar notificaciones remotas en una aplicación de Xamarin. Android. Se describe cómo instalar los paquetes adicionales necesarios para las comunicaciones de GCM y se explica cómo configurar los permisos de aplicación para el acceso a los servidores GCM.
Proporciona código de ejemplo que muestra cómo comprobar la presencia de Google Play Services, cómo implementar un servicio de control de la intención de registro y un servicio de escucha de ID. de instancia que negocia con GCM para un token de registro y cómo implementar un agente de escucha de GCM servicio que recibe y procesa los mensajes de notificación remotos. Por último, hemos implementado un programa de prueba de línea de comandos para enviar notificaciones de prueba a nuestra aplicación cliente a través de GCM.

## <a name="related-links"></a>Vínculos relacionados

- [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md)
