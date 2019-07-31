---
title: 'Tutorial: uso de notificaciones locales en Xamarin. Android'
description: En este tutorial se muestra cómo usar las notificaciones locales en las aplicaciones de Xamarin. Android. Muestra los aspectos básicos de la creación y publicación de una notificación local. Cuando el usuario hace clic en la notificación en el área de notificación, inicia una segunda actividad.
ms.prod: xamarin
ms.assetid: D8C6C9E2-3282-49D1-A2F6-78A4F3306E29
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/16/2018
ms.openlocfilehash: 61dedca1b89237bdd4669a63173b1c6e4fde072b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644304"
---
# <a name="walkthrough---using-local-notifications-in-xamarinandroid"></a>Tutorial: uso de notificaciones locales en Xamarin. Android

_En este tutorial se muestra cómo usar las notificaciones locales en las aplicaciones de Xamarin. Android. Muestra los aspectos básicos de la creación y publicación de una notificación local. Cuando el usuario hace clic en la notificación en el área de notificación, inicia una segunda actividad._


## <a name="overview"></a>Información general

En este tutorial, se creará una aplicación de Android que genera una notificación cuando el usuario hace clic en un botón de una actividad. Cuando el usuario hace clic en la notificación, inicia una segunda actividad que muestra el número de veces que el usuario hizo clic en el botón de la primera actividad.

En las siguientes capturas de pantallas se muestran algunos ejemplos de esta aplicación:

[![Capturas de pantallas de ejemplo con notificación](local-notifications-walkthrough-images/1-overview-sml.png)](local-notifications-walkthrough-images/1-overview.png#lightbox)

> [!NOTE]
> Esta guía se centra en las [API de NotificationCompat](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html) de la biblioteca de compatibilidad de [Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Estas API garantizarán la máxima compatibilidad con versiones anteriores de Android 4,0 (nivel de API 14).

## <a name="creating-the-project"></a>Crear el proyecto

Para empezar, vamos a crear un nuevo proyecto de Android con la plantilla de **aplicación de Android** . Vamos a llamar a este proyecto **LocalNotifications**. (Si no está familiarizado con la creación de proyectos de Xamarin. Android, consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md)).

Edite el archivo de recursos **Values/Strings. XML** para que contenga dos recursos de cadena adicionales que se usarán cuando sea el momento de crear el canal de notificación:


```xml
<?xml version="1.0" encoding="utf-8"?>

<resources>
  <string name="Hello">Hello World, Click Me!</string>
  <string name="ApplicationName">Notifications</string>

  <string name="channel_name">Local Notifications</string>
  <string name="channel_description">The count from MainActivity.</string>
</resources>
```

### <a name="add-the-androidsupportv4-nuget-package"></a>Adición del paquete de NuGet Android. support. V4

En este tutorial, estamos usando `NotificationCompat.Builder` para compilar nuestra notificación local. Tal como se explica en las [notificaciones locales](~/android/app-fundamentals/notifications/local-notifications.md), debemos incluir la [biblioteca de compatibilidad de Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) NuGet en `NotificationCompat.Builder`nuestro proyecto para usar.

A continuación, vamos a editar **MainActivity.CS** y agregaremos `using` la siguiente instrucción para que los `Android.Support.V4.App` tipos de estén disponibles para nuestro código:

```csharp
using Android.Support.V4.App;
```

Además, debemos aclarar el compilador que estamos usando la `Android.Support.V4.App` versión de en lugar de `TaskStackBuilder` la `Android.App` versión. Agregue la siguiente `using` instrucción para resolver cualquier ambigüedad:

```csharp
using TaskStackBuilder = Android.Support.V4.App.TaskStackBuilder;
```

### <a name="create-the-notification-channel"></a>Crear el canal de notificación

A continuación, agregue un método `MainActivity` a que creará un canal de notificación (si es necesario):

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

    var name = Resources.GetString(Resource.String.channel_name);
    var description = GetString(Resource.String.channel_description);
    var channel = new NotificationChannel(CHANNEL_ID, name, NotificationImportance.Default)
                  {
                      Description = description
                  };

    var notificationManager = (NotificationManager) GetSystemService(NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

Actualice el `OnCreate` método para llamar a este nuevo método:

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateNotificationChannel();
}
```


### <a name="define-the-notification-id"></a>Definir el identificador de notificación

Se necesitará un identificador único para el canal de notificación y notificación. Vamos a editar **MainActivity.CS** y agregaremos la siguiente variable de instancia estática `MainActivity` a la clase:

```csharp
static readonly int NOTIFICATION_ID = 1000;
static readonly string CHANNEL_ID = "location_notification";
internal static readonly string COUNT_KEY = "count";
```

### <a name="add-code-to-generate-the-notification"></a>Agregar código para generar la notificación

A continuación, es necesario crear un nuevo controlador de eventos para el `Click` evento de botón. Agregue el método siguiente a `MainActivity`:

```csharp
void ButtonOnClick(object sender, EventArgs eventArgs)
{
    // Pass the current button press count value to the next activity:
    var valuesForActivity = new Bundle();
    valuesForActivity.PutInt(COUNT_KEY, count);

    // When the user clicks the notification, SecondActivity will start up.
    var resultIntent = new Intent(this, typeof(SecondActivity));

    // Pass some values to SecondActivity:
    resultIntent.PutExtras(valuesForActivity);

    // Construct a back stack for cross-task navigation:
    var stackBuilder = TaskStackBuilder.Create(this);
    stackBuilder.AddParentStack(Class.FromType(typeof(SecondActivity)));
    stackBuilder.AddNextIntent(resultIntent);

    // Create the PendingIntent with the back stack:
    var resultPendingIntent = stackBuilder.GetPendingIntent(0, (int) PendingIntentFlags.UpdateCurrent);

    // Build the notification:
    var builder = new NotificationCompat.Builder(this, CHANNEL_ID)
                  .SetAutoCancel(true) // Dismiss the notification from the notification area when the user clicks on it
                  .SetContentIntent(resultPendingIntent) // Start up this activity when the user clicks the intent.
                  .SetContentTitle("Button Clicked") // Set the title
                  .SetNumber(count) // Display the count in the Content Info
                  .SetSmallIcon(Resource.Drawable.ic_stat_button_click) // This is the icon to display
                  .SetContentText($"The button has been clicked {count} times."); // the message to display.

    // Finally, publish the notification:
    var notificationManager = NotificationManagerCompat.From(this);
    notificationManager.Notify(NOTIFICATION_ID, builder.Build());

    // Increment the button press count:
    count++;
}
```

El `OnCreate` método de MainActivity debe hacer la llamada para crear el canal de notificación y asignar `ButtonOnClick` el método al `Click` evento del botón (Reemplace el controlador de eventos de delegado que proporciona la plantilla):

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateNotificationChannel();

    // Display the "Hello World, Click Me!" button and register its event handler:
    var button = FindViewById<Button>(Resource.Id.MyButton);
    button.Click += ButtonOnClick;
}
```


### <a name="create-a-second-activity"></a>Crear una segunda actividad

Ahora es necesario crear otra actividad que Android mostrará cuando el usuario haga clic en la notificación. Agregue otra actividad de Android al proyecto denominado **SecondActivity**. Abra **SecondActivity.CS** y reemplace su contenido por este código:

```csharp
using System;
using Android.App;
using Android.OS;
using Android.Widget;

namespace LocalNotifications
{
    [Activity(Label = "Second Activity")]
    public class SecondActivity : Activity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            // Get the count value passed to us from MainActivity:
            var count = Intent.Extras.GetInt(MainActivity.COUNT_KEY, -1);

            // No count was passed? Then just return.
            if (count <= 0)
            {
                return;
            }

            // Display the count sent from the first activity:
            SetContentView(Resource.Layout.Second);
            var txtView = FindViewById<TextView>(Resource.Id.textView1);
            txtView.Text = $"You clicked the button {count} times in the previous activity.";
        }
    }
}
```

También debemos crear un diseño de recursos para **SecondActivity**. Agregue un nuevo archivo de **diseño de Android** a su proyecto denominado **Second. axml**. Edite **Second. axml** y pegue el siguiente código de diseño:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <TextView
        android:text=""
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:id="@+id/textView1" />
</LinearLayout>
```


### <a name="add-a-notification-icon"></a>Icono Agregar un aviso

Por último, agregue un icono pequeño que aparecerá en el área de notificación cuando se inicie la notificación. Puede copiar [este icono](local-notifications-walkthrough-images/ic-stat-button-click.png) en el proyecto o crear su propio icono personalizado. Asigne el nombre archivo **de\_icono IC\_STAT\_Button click. png** y cópielo en la carpeta Resources **/drawable** . No olvide usar **agregar > elemento existente...** para incluir este archivo de icono en el proyecto.


### <a name="run-the-application"></a>Ejecución de la aplicación

Compile y ejecute la aplicación. Debería aparecer la primera actividad, similar a la siguiente captura de pantalla:

[![Captura de pantalla de la primera actividad](local-notifications-walkthrough-images/2-start-screen-sml.png)](local-notifications-walkthrough-images/2-start-screen.png#lightbox)

Al hacer clic en el botón, debe observar que el icono pequeño de la notificación aparece en el área de notificación:

[![Aparece el icono de notificación](local-notifications-walkthrough-images/3-notification-icon-sml.png)](local-notifications-walkthrough-images/3-notification-icon.png#lightbox)

Si desliza el dedo hacia abajo y expone el cajón de notificaciones, debería ver la notificación:

[![Mensaje de notificación](local-notifications-walkthrough-images/4-notifications-sml.png)](local-notifications-walkthrough-images/4-notifications.png#lightbox)

Al hacer clic en la notificación, debe desaparecer y la otra actividad debe iniciarse &ndash; en un aspecto similar al de la siguiente captura de pantalla:

[![Captura de pantalla de segunda actividad](local-notifications-walkthrough-images/5-second-activity-sml.png)](local-notifications-walkthrough-images/5-second-activity.png#lightbox)

Felicidades. En este punto, ha completado el tutorial de notificaciones locales de Android y tiene un ejemplo en funcionamiento al que puede hacer referencia. Hay muchas más notificaciones que se han mostrado aquí, por lo que si desea más información, eche un vistazo a [la documentación de Google sobre](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)las notificaciones.


## <a name="summary"></a>Resumen

Este tutorial se `NotificationCompat.Builder` usa para crear y Mostrar notificaciones. Mostraba un ejemplo básico de cómo iniciar una segunda actividad como una manera de responder a la interacción del usuario con la notificación y demostró la transferencia de datos de la primera actividad a la segunda actividad.


## <a name="related-links"></a>Vínculos relacionados

- [LocalNotifications (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/localnotifications)
- [Canales de notificación de Android Oreo](https://blog.xamarin.com/android-oreo-notification-channels/)
- [Aviso](xref:Android.App.Notification)
- [NotificationManager](xref:Android.App.NotificationManager)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](xref:Android.App.PendingIntent)
