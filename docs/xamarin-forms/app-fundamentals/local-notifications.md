---
title: 'title: "Notificaciones locales de Xamarin.Forms" description: "En este artículo se explica cómo enviar y recibir notificaciones locales en Xamarin.Forms."'
description: 'ms.prod: xamarin ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7 ms.technology: xamarin-forms author: profexorgeek ms.author: jusjohns ms.date: 10/10/2019 no-loc: [Xamarin.Forms, Xamarin.Essentials]'
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/10/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 40e040f216ddda40931273f4e7f5614964862fe8
ms.sourcegitcommit: ea9269b5d9e3d68b61bb428560a10034117ee457
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2020
ms.locfileid: "84137603"
---
# <a name="local-notifications-in-xamarinforms"></a>Notificaciones locales de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/local-notifications)

Las notificaciones locales son alertas que envían las aplicaciones instaladas en un dispositivo móvil. Se suelen usar a menudo en características como las siguientes:

- Eventos de calendario
- Recordatorios
- Desencadenadores basados en la ubicación

Cada plataforma controla la creación, la visualización y el consumo de notificaciones locales de manera diferente. En este artículo se explica cómo crear una abstracción multiplataforma para enviar y recibir notificaciones locales con Xamarin.Forms.

[![Aplicación de notificaciones locales en iOS y Android](local-notifications-images/local-notifications-msg-cropped.png)](local-notifications-images/local-notifications-msg.png#lightbox)

## <a name="create-a-cross-platform-interface"></a>Creación de una interfaz multiplataforma

La aplicación de Xamarin.Forms debe crear y consumir notificaciones sin preocuparse por las implementaciones subyacentes de la plataforma. La siguiente interfaz de `INotificationManager` se implementa en la biblioteca de código compartido y define una API multiplataforma que la aplicación puede usar para interactuar con las notificaciones:

```csharp
public interface INotificationManager
{
    event EventHandler NotificationReceived;

    void Initialize();

    int ScheduleNotification(string title, string message);

    void ReceiveNotification(string title, string message);
}
```

Esta interfaz se implementará en cada proyecto de plataforma. El evento `NotificationReceived` permite que la aplicación controle las notificaciones entrantes. El método `Initialize` debe ejecutar alguna lógica de plataforma nativa necesaria para preparar el sistema de notificación. El método `ScheduleNotification` debe enviar una notificación. La plataforma subyacente debe llamar al método `ReceiveNotification` cuando se reciba un mensaje.

## <a name="consume-the-interface-in-xamarinforms"></a>Uso de la interfaz en Xamarin.Forms

Una vez creada una interfaz, se puede usar en el proyecto compartido de Xamarin.Forms aunque aún no se hayan creado las implementaciones de la plataforma. La aplicación de ejemplo contiene un elemento `ContentPage` denominado **MainPage.xaml** con el siguiente contenido:

```xaml
<StackLayout Margin="0,35,0,0"
             x:Name="stackLayout">
    <Label Text="Click the button to create a local notification."
           TextColor="Red"
           HorizontalOptions="Center"
           VerticalOptions="Start" />
    <Button Text="Create Notification"
            HorizontalOptions="Center"
            VerticalOptions="Start"
            Clicked="OnScheduleClick"/>
</StackLayout>
```

El diseño contiene un elemento `Label` con instrucciones para el usuario y un elemento `Button` que debe programar una notificación cuando se pulse.

El código subyacente de la clase `MainPage` controla el envío y la recepción de notificaciones:

```csharp
public partial class MainPage : ContentPage
{
    INotificationManager notificationManager;
    int notificationNumber = 0;

    public MainPage()
    {
        InitializeComponent();

        notificationManager = DependencyService.Get<INotificationManager>();
        notificationManager.NotificationReceived += (sender, eventArgs) =>
        {
            var evtData = (NotificationEventArgs)eventArgs;
            ShowNotification(evtData.Title, evtData.Message);
        };
    }

    void OnScheduleClick(object sender, EventArgs e)
    {
        notificationNumber++;
        string title = $"Local Notification #{notificationNumber}";
        string message = $"You have now received {notificationNumber} notifications!";
        notificationManager.ScheduleNotification(title, message);
    }

    void ShowNotification(string title, string message)
    {
        Device.BeginInvokeOnMainThread(() =>
        {
            var msg = new Label()
            {
                Text = $"Notification Received:\nTitle: {title}\nMessage: {message}"
            };
            stackLayout.Children.Add(msg);
        });
    }
}
```

El constructor de clase `MainPage` usa el elemento `DependencyService` de Xamarin.Forms para recuperar una instancia específica de la plataforma de `INotificationManager`. El método `OnScheduleClicked` usa la instancia de `INotificationManager` para programar una nueva notificación. Se llama al método `ShowNotification` desde el controlador de eventos asociado al evento `NotificationReceived` y se inserta un nuevo elemento `Label` en la página cuando se invoca el evento.

Para obtener más información sobre `DependencyService` de Xamarin.Forms, vea [DependencyService de Xamarin.Forms](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md).

## <a name="create-the-android-interface-implementation"></a>Creación de la implementación de la interfaz de Android

Para que la aplicación de Xamarin.Forms envíe y reciba notificaciones en Android, esta debe proporcionar una implementación de la interfaz `INotificationManager`.

### <a name="create-the-androidnotificationmanager-class"></a>Creación de la clase AndroidNotificationManager

La clase `AndroidNotificationManager` implementa la interfaz `INotificationManager`:

```csharp
using Android.Support.V4.App;
using Xamarin.Forms;
using AndroidApp = Android.App.Application;

[assembly: Dependency(typeof(LocalNotifications.Droid.AndroidNotificationManager))]
namespace LocalNotifications.Droid
{
    public class AndroidNotificationManager : INotificationManager
    {
        const string channelId = "default";
        const string channelName = "Default";
        const string channelDescription = "The default channel for notifications.";
        const int pendingIntentId = 0;

        public const string TitleKey = "title";
        public const string MessageKey = "message";

        bool channelInitialized = false;
        int messageId = -1;
        NotificationManager manager;

        public event EventHandler NotificationReceived;

        public void Initialize()
        {
            CreateNotificationChannel();
        }

        public int ScheduleNotification(string title, string message)
        {
            if (!channelInitialized)
            {
                CreateNotificationChannel();
            }

            messageId++;

            Intent intent = new Intent(AndroidApp.Context, typeof(MainActivity));
            intent.PutExtra(TitleKey, title);
            intent.PutExtra(MessageKey, message);

            PendingIntent pendingIntent = PendingIntent.GetActivity(AndroidApp.Context, pendingIntentId, intent, PendingIntentFlags.OneShot);

            NotificationCompat.Builder builder = new NotificationCompat.Builder(AndroidApp.Context, channelId)
                .SetContentIntent(pendingIntent)
                .SetContentTitle(title)
                .SetContentText(message)
                .SetLargeIcon(BitmapFactory.DecodeResource(AndroidApp.Context.Resources, Resource.Drawable.xamagonBlue))
                .SetSmallIcon(Resource.Drawable.xamagonBlue)
                .SetDefaults((int)NotificationDefaults.Sound | (int)NotificationDefaults.Vibrate);

            var notification = builder.Build();
            manager.Notify(messageId, notification);

            return messageId;
        }

        public void ReceiveNotification(string title, string message)
        {
            var args = new NotificationEventArgs()
            {
                Title = title,
                Message = message,
            };
            NotificationReceived?.Invoke(null, args);
        }

        void CreateNotificationChannel()
        {
            manager = (NotificationManager)AndroidApp.Context.GetSystemService(AndroidApp.NotificationService);

            if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
            {
                var channelNameJava = new Java.Lang.String(channelName);
                var channel = new NotificationChannel(channelId, channelNameJava, NotificationImportance.Default)
                {
                    Description = channelDescription
                };
                manager.CreateNotificationChannel(channel);
            }

            channelInitialized = true;
        }
    }
}
```

El atributo `assembly` situado encima del espacio de nombres registra la implementación de la interfaz de `INotificationManager` con `DependencyService`.

Android permite que las aplicaciones definan varios canales para las notificaciones. El método `Initialize` crea un canal básico que usa la aplicación de ejemplo para enviar notificaciones. El método `ScheduleNotification` define la lógica específica de la plataforma necesaria para crear y enviar una notificación. Por último, el sistema operativo Android llama al método `ReceiveNotification` cuando se recibe un mensaje e invoca el controlador de eventos.

> [!NOTE]
> La clase `Application` se define en los espacios de nombres `Xamarin.Forms` y `Android.App`, por lo que el alias `AndroidApp` se define en las instrucciones `using` para diferenciar los dos.

### <a name="handle-incoming-notifications-on-android"></a>Administración de las notificaciones entrantes en Android

La clase `MainActivity` debe detectar las notificaciones entrantes y notificar la instancia de `AndroidNotificationManager`. El atributo `Activity` de la clase `MainActivity` debe especificar un valor `LaunchMode` de `LaunchMode.SingleTop`:

```csharp
[Activity(
        //...
        LaunchMode = LaunchMode.SingleTop]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        // ...
    }
```

El modo `SingleTop` impide que se inicien varias instancias de un elemento `Activity` mientras la aplicación está en primer plano. Es posible que este valor de `LaunchMode` no sea adecuado para las aplicaciones que inician varias actividades en escenarios de notificación más complejos. Para más información sobre los valores de enumeración de `LaunchMode`, consulte la [actividad LaunchMode de Android](https://developer.android.com/guide/topics/manifest/activity-element#lmode).

En la clase `MainActivity` se modifica para recibir notificaciones entrantes:

```csharp
protected override void OnCreate(Bundle savedInstanceState)
{
    // ...

    global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
    LoadApplication(new App());
    CreateNotificationFromIntent(Intent);
}

protected override void OnNewIntent(Intent intent)
{
    CreateNotificationFromIntent(intent);
}

void CreateNotificationFromIntent(Intent intent)
{
    if (intent?.Extras != null)
    {
        string title = intent.Extras.GetString(AndroidNotificationManager.TitleKey);
        string message = intent.Extras.GetString(AndroidNotificationManager.MessageKey);
        DependencyService.Get<INotificationManager>().ReceiveNotification(title, message);
    }
}
```

El método `CreateNotificationFromIntent` extrae los datos de notificación del argumento `intent` y los proporciona a `AndroidNotificationManager` mediante el método `ReceiveNotification`. Se llama al método `CreateNotificationFromIntent` desde el método `OnCreate` y el método `OnNewIntent`:

- Cuando la aplicación se inicia mediante los datos de notificación, los datos de `Intent` se pasan al método `OnCreate`.
- Si la aplicación ya está en primer plano, los datos de `Intent` se pasan al método `OnNewIntent`.

Android ofrece muchas opciones avanzadas para las notificaciones. Para más información, consulte [Notificaciones en Xamarin.Android ](~/android/app-fundamentals/notifications/index.md).

## <a name="create-the-ios-interface-implementation"></a>Creación de la implementación de la interfaz de iOS

Para que la aplicación de Xamarin.Forms envíe y reciba notificaciones en iOS, esta debe proporcionar una implementación de `INotificationManager`.

### <a name="create-the-iosnotificationmanager-class"></a>Creación de la clase iOSNotificationManager

La clase `iOSNotificationManager` implementa la interfaz `INotificationManager`:

```csharp
[assembly: Dependency(typeof(LocalNotifications.iOS.iOSNotificationManager))]
namespace LocalNotifications.iOS
{
    public class iOSNotificationManager : INotificationManager
    {
        int messageId = -1;

        bool hasNotificationsPermission;

        public event EventHandler NotificationReceived;

        public void Initialize()
        {
            // request the permission to use local notifications
            UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert, (approved, err) =>
            {
                hasNotificationsPermission = approved;
            });
        }

        public int ScheduleNotification(string title, string message)
        {
            // EARLY OUT: app doesn't have permissions
            if(!hasNotificationsPermission)
            {
                return -1;
            }

            messageId++;

            var content = new UNMutableNotificationContent()
            {
                Title = title,
                Subtitle = "",
                Body = message,
                Badge = 1
            };

            // Local notifications can be time or location based
            // Create a time-based trigger, interval is in seconds and must be greater than 0
            var trigger = UNTimeIntervalNotificationTrigger.CreateTrigger(0.25, false);

            var request = UNNotificationRequest.FromIdentifier(messageId.ToString(), content, trigger);
            UNUserNotificationCenter.Current.AddNotificationRequest(request, (err) =>
            {
                if (err != null)
                {
                    throw new Exception($"Failed to schedule notification: {err}");
                }
            });

            return messageId;
        }

        public void ReceiveNotification(string title, string message)
        {
            var args = new NotificationEventArgs()
            {
                Title = title,
                Message = message
            };
            NotificationReceived?.Invoke(null, args);
        }
    }
}
```

El atributo `assembly` situado encima del espacio de nombres registra la implementación de la interfaz de `INotificationManager` con `DependencyService`.

En iOS, debe solicitar permiso para usar notificaciones antes de intentar programar una notificación. El método `Initialize` solicita autorización para usar notificaciones locales. El método `ScheduleNotification` define la lógica necesaria para crear y enviar una notificación. Por último, iOS llamará al método `ReceiveNotification` cuando se reciba un mensaje e invocará el controlador de eventos.

### <a name="handle-incoming-notifications-on-ios"></a>Control de las notificaciones entrantes en iOS

En iOS, debe crear un delegado que cree subclases `UNUserNotificationCenterDelegate` para administrar los mensajes entrantes. La aplicación de ejemplo define una clase `iOSNotificationReceiver`:

```csharp
public class iOSNotificationReceiver : UNUserNotificationCenterDelegate
{
    public override void WillPresentNotification(UNUserNotificationCenter center, UNNotification notification, Action<UNNotificationPresentationOptions> completionHandler)
    {
        DependencyService.Get<INotificationManager>().ReceiveNotification(notification.Request.Content.Title, notification.Request.Content.Body);

        // alerts are always shown for demonstration but this can be set to "None"
        // to avoid showing alerts if the app is in the foreground
        completionHandler(UNNotificationPresentationOptions.Alert);
    }
}
```

Esta clase usa `DependencyService` para obtener una instancia de la clase `iOSNotificationManager` y proporciona datos de notificación entrantes al método `ReceiveNotification`.

La clase `AppDelegate` debe especificar el delegado personalizado durante el inicio de la aplicación. La clase `AppDelegate` debe especificar un objeto `iOSNotificationReceiver` como delegado `UNUserNotificationCenter` durante el inicio de la aplicación. Esto sucede en el método `FinishedLaunching`:

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    global::Xamarin.Forms.Forms.Init();

    UNUserNotificationCenter.Current.Delegate = new iOSNotificationReceiver();

    LoadApplication(new App());
    return base.FinishedLaunching(app, options);
}
```

iOS ofrece muchas opciones avanzadas para las notificaciones. Para más información, consulte [Notificaciones en Xamarin.iOS](~/ios/platform/user-notifications/index.md).

## <a name="test-the-application"></a>Probar la aplicación

Una vez que los proyectos de plataforma contienen una implementación registrada de la interfaz `INotificationManager`, la aplicación se puede probar en ambas plataformas. Ejecute la aplicación y haga clic en el botón **Schedule Notification** (Programar notificación) para crear una notificación.

En Android, la notificación aparecerá en el área de notificación. Cuando se pulsa en la notificación, la aplicación la recibe y muestra un mensaje debajo del botón **Schedule Notification** (Programar notificación):

![Notificaciones locales en Android](local-notifications-images/local-notifications-android.png)

En iOS, la aplicación recibe automáticamente las notificaciones entrantes sin necesidad de la intervención del usuario. La aplicación recibe la notificación y muestra un mensaje debajo del botón **Schedule Notification** (Programar notificación):

![Notificaciones locales en iOS](local-notifications-images/local-notifications-ios.png)

## <a name="related-links"></a>Vínculos relacionados

- [Proyecto de ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/local-notifications)
- [Notificaciones en Xamarin.Android](~/android/app-fundamentals/notifications/index.md)
- [Notificaciones en Xamarin.iOS](~/ios/platform/user-notifications/index.md)
- [Dependency.Service de Xamarin.Forms](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md)
