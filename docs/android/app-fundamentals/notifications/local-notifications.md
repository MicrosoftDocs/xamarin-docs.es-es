---
title: Notificaciones locales en Android
description: En esta sección se muestra cómo implementar notificaciones locales en Xamarin. Android. Se explican los distintos elementos de la interfaz de usuario de una notificación de Android y se describen las API implicadas en la creación y visualización de una notificación.
ms.prod: xamarin
ms.assetid: 03E19D14-7C81-4D5C-88FC-C3A3A927DB46
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/16/2018
ms.openlocfilehash: 0d5cde38c9bb9ef4771ec17ef34ebf7e1b8cf74c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755511"
---
# <a name="local-notifications-on-android"></a>Notificaciones locales en Android

_En esta sección se muestra cómo implementar notificaciones locales en Xamarin. Android. Se explican los distintos elementos de la interfaz de usuario de una notificación de Android y se describen las API implicadas en la creación y visualización de una notificación._

## <a name="local-notifications-overview"></a>Información general sobre las notificaciones locales

Android proporciona dos áreas controladas por el sistema para mostrar los iconos de notificación y la información de notificación al usuario. Cuando se publica una notificación por primera vez, su icono se muestra en el *área de notificación*, tal como se muestra en la siguiente captura de pantalla:

![Ejemplo de área de notificación en un dispositivo](local-notifications-images/01-notification-shade.png)

Para obtener detalles sobre la notificación, el usuario puede abrir el cajón de notificaciones (que expande cada icono de notificación para mostrar el contenido de la notificación) y realizar las acciones asociadas a las notificaciones. En la captura de pantalla siguiente se muestra un *cajón de notificaciones* que se corresponde con el área de notificación mostrada anteriormente:

[![Ejemplo de cajón de notificaciones que muestra tres notificaciones](local-notifications-images/02-notification-drawer-sml.png)](local-notifications-images/02-notification-drawer.png#lightbox)

Las notificaciones de Android usan dos tipos de diseños:

- ***Diseño base*** &ndash; formato de presentación compacto y fijo.

- ***Diseño expandido*** &ndash; un formato de presentación que se puede expandir hasta un tamaño mayor para mostrar más información.

En las secciones siguientes se explica cada uno de estos tipos de diseño (y cómo crearlos).

> [!NOTE]
> Esta guía se centra en las [API de NotificationCompat](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html) de la biblioteca de compatibilidad de [Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Estas API garantizarán la máxima compatibilidad con versiones anteriores de Android 4,0 (nivel de API 14).

### <a name="base-layout"></a>Diseño base

Todas las notificaciones de Android se basan en el formato de diseño base, que, como mínimo, incluye los siguientes elementos:

1. Un *icono de notificación*, que representa la aplicación de origen, o el tipo de notificación si la aplicación admite distintos tipos de notificaciones.

2. El *título*de la notificación o el nombre del remitente si la notificación es un mensaje personal.

3. Mensaje de notificación.

4. *Marca*de tiempo.

Estos elementos se muestran como se muestra en el diagrama siguiente:

[![Ubicación de los elementos de notificación](local-notifications-images/03-notification-callouts-sml.png)](local-notifications-images/03-notification-callouts.png#lightbox)

Los diseños base se limitan a 64 píxeles independientes de densidad (DP) en altura. Android crea este estilo de notificación básico de forma predeterminada.

Opcionalmente, las notificaciones pueden mostrar un icono grande que representa la aplicación o la foto del remitente. Cuando se usa un icono grande en una notificación en Android 5,0 y versiones posteriores, el icono de notificación pequeña se muestra como un distintivo en el icono grande:

![Foto de notificación simple](local-notifications-images/04-simple-notification-photo.png)

A partir de Android 5,0, las notificaciones también pueden aparecer en la pantalla de bloqueo:

[![Ejemplo de notificación de pantalla de bloqueo](local-notifications-images/05-lockscreen-notification-sml.png)](local-notifications-images/05-lockscreen-notification.png#lightbox)

El usuario puede hacer doble punteo en la notificación de la pantalla de bloqueo para desbloquear el dispositivo y saltar a la aplicación que originó esa notificación, o bien deslizar para descartar la notificación. Las aplicaciones pueden establecer el nivel de visibilidad de una notificación para controlar lo que se muestra en la pantalla de bloqueo y los usuarios pueden elegir si desea que se muestre contenido confidencial en las notificaciones de la pantalla de bloqueo.

Android 5,0 presentó un formato de presentación de notificaciones de alta prioridad denominado " *Head-up*". Las notificaciones de los cabezales se desplazan hacia abajo desde la parte superior de la pantalla durante unos segundos y, a continuación, se revierten al área de notificación:

[![Ejemplo de notificación de suscripción](local-notifications-images/06-heads-up-notification-sml.png)](local-notifications-images/06-heads-up-notification.png#lightbox)

Las notificaciones de cabezales permiten que la interfaz de usuario del sistema Coloque información importante delante del usuario sin interrumpir el estado de la actividad que se está ejecutando actualmente.

Android incluye compatibilidad con los metadatos de notificación para que las notificaciones se puedan ordenar y mostrar de manera inteligente. Los metadatos de notificación también controlan el modo en que se presentan las notificaciones en la pantalla de bloqueo y en formato de cabeza. Las aplicaciones pueden establecer los siguientes tipos de metadatos de notificación:

- **Prioridad** de &ndash; El nivel de prioridad determina cómo y cuándo se presentan las notificaciones. Por ejemplo, en Android 5,0, las notificaciones de alta prioridad se muestran como notificaciones de cabeza.

- **Visibilidad** de &ndash; Especifica la cantidad de contenido de notificación que se va a mostrar cuando aparezca la notificación en la pantalla de bloqueo.

- **Categoría** de Informa al sistema de cómo controlar la notificación en varias circunstancias, como cuando el dispositivo está en modo *no molesta.* &ndash;

> [!NOTE]
> La **visibilidad** y la **categoría** se introdujeron en Android 5,0 y no están disponibles en versiones anteriores de Android. A partir de Android 8,0, los [canales de notificación](#notif-chan) se utilizan para controlar cómo se presentan las notificaciones al usuario.

### <a name="expanded-layouts"></a>Diseños expandidos

A partir de Android 4,1, las notificaciones se pueden configurar con estilos de diseño expandidos que permiten al usuario expandir el alto de la notificación para ver más contenido. Por ejemplo, en el ejemplo siguiente se muestra una notificación de diseño expandida en modo de contrato:

![Notificación contratada](local-notifications-images/07-contracted-notification.png)

Cuando se expande esta notificación, se revela todo el mensaje:

![Notificación expandida](local-notifications-images/08-expanded-notification.png)

Android admite tres estilos de diseño expandidos para las notificaciones de evento único:

- ***Big Text*** &ndash; En el modo de contrato, muestra un extracto de la primera línea del mensaje seguido de dos puntos. En el modo expandido, muestra el mensaje completo (como se muestra en el ejemplo anterior).

- ***Bandeja de entrada*** &ndash; En el modo de contrato, muestra el número de mensajes nuevos. En el modo expandido, muestra el primer mensaje de correo electrónico o una lista de los mensajes en la bandeja de entrada.

- ***Imagen*** de &ndash; En el modo de contrato, solo muestra el texto del mensaje. En el modo expandido, muestra el texto y una imagen.

[Más allá de la notificación básica](#beyond-the-basic-notification) (más adelante en este artículo) se explica cómo crear notificaciones de *texto*, de *bandeja de entrada*y de *imagen* .

<a name="notif-chan"></a>
<a name="notification-channels"></a>
## <a name="notification-channels"></a>Canales de notificación

A partir de Android 8,0 (Oreo), puede usar la característica de *canales de notificación* para crear un canal personalizable por el usuario para cada tipo de notificación que desea mostrar. Los canales de notificación permiten agrupar las notificaciones para que todas las notificaciones enviadas a un canal presenten el mismo comportamiento. Por ejemplo, podría tener un canal de notificación destinado a notificaciones que requieran atención inmediata y un canal "más silencioso" independiente que se use para mensajes informativos.

La aplicación de **YouTube** que se instala con Android Oreo enumera dos categorías de notificación: **Descargar notificaciones** y **notificaciones generales**:

[![Pantallas de notificación para YouTube en Android Oreo](local-notifications-images/27-youtube-sml.png)](local-notifications-images/27-youtube.png#lightbox)

Cada una de estas categorías corresponde a un canal de notificación. La aplicación de YouTube implementa un canal de **notificaciones de descarga** y un canal de **notificaciones generales** . El usuario puede pulsar **Descargar notificaciones**, que muestra la pantalla de configuración del canal de notificaciones de descarga de la aplicación:

[![Descargar la pantalla de notificaciones de la aplicación de YouTube](local-notifications-images/28-yt-download-sml.png)](local-notifications-images/28-yt-download.png#lightbox)

En esta pantalla, el usuario puede modificar el comportamiento del canal de las notificaciones de **descarga** haciendo lo siguiente:

- Establezca el nivel de importancia en **urgente**, **alto**, **medio**o **bajo**, lo que configura el nivel de interrupción de sonido y visual.

- Activar o desactivar el punto de notificación.

- Activar o desactivar la luz intermitente.

- Mostrar u ocultar notificaciones en la pantalla de bloqueo.

- Invalide la configuración de no **molestar** .

El canal de **notificaciones generales** tiene una configuración similar:

[![Pantalla de notificaciones generales para la aplicación de YouTube](local-notifications-images/29-yt-general-sml.png)](local-notifications-images/29-yt-general.png#lightbox)

Tenga en cuenta que no tiene control absoluto sobre el modo en que los canales de notificación &ndash; interactúan con el usuario. el usuario puede modificar la configuración de cualquier canal de notificación en el dispositivo, tal como se ve en las capturas de pantallas anteriores. Sin embargo, puede configurar los valores predeterminados (como se describe a continuación). Como se muestra en estos ejemplos, la nueva característica de canales de notificación permite proporcionar a los usuarios un mayor control sobre los distintos tipos de notificaciones.

## <a name="notification-creation"></a>Creación de notificaciones

Para crear una notificación en Android, use la clase [NotificationCompat. Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder) del paquete NuGet [Xamarin. Android. support. V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) . Esta clase permite crear y publicar notificaciones en versiones anteriores de Android.
`NotificationCompat.Builder`también se describe.

`NotificationCompat.Builder`proporciona métodos para establecer las diversas opciones en una notificación, como:

- El contenido, incluido el título, el texto del mensaje y el icono de notificación.

- Estilo de la notificación, como el estilo de *texto grande*, *bandeja de entrada*o *imagen* .

- La prioridad de la notificación: mínima, baja, predeterminada, alta o máxima. En Android 8,0 y versiones posteriores, la prioridad se establece a través de un [_canal de notificación_](#notification-channels).

- La visibilidad de la notificación en la pantalla de bloqueo: pública, privada o secreta.

- Metadatos de categoría que ayudan a Android a clasificar y filtrar la notificación.

- Una intención opcional que indica que una actividad se inicia cuando se puntea la notificación.

- IDENTIFICADOR del canal de notificación en el que se publicará la notificación (Android 8,0 y versiones posteriores).

Después de establecer estas opciones en el generador, se genera un objeto de notificación que contiene la configuración. Para publicar la notificación, se pasa este objeto de notificación al *Administrador de notificaciones*. Android proporciona la clase [NotificationManager](xref:Android.App.NotificationManager) , que es responsable de publicar notificaciones y mostrarla al usuario. Se puede obtener una referencia a esta clase desde cualquier contexto, como una actividad o un servicio.

### <a name="creating-a-notification-channel"></a>Creación de un canal de notificación

Las aplicaciones que se ejecutan en Android 8,0 deben crear un canal de notificación para las notificaciones. Un canal de notificación requiere los tres datos siguientes:

- Una cadena de identificador que es única para el paquete que identificará el canal.
- Nombre del canal que se va a mostrar al usuario.  El nombre debe tener entre 1 y 40 caracteres.
- Importancia del canal.

Las aplicaciones deberán comprobar la versión de Android que se están ejecutando.
Los dispositivos que ejecutan versiones anteriores a Android 8,0 no deben crear un canal de notificación. El método siguiente es un ejemplo de cómo crear un canal de notificación en una actividad:

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

    var channelName = Resources.GetString(Resource.String.channel_name);
    var channelDescription = GetString(Resource.String.channel_description);
    var channel = new NotificationChannel(CHANNEL_ID, channelName, NotificationImportance.Default)
                  {
                      Description = channelDescription
                  };

    var notificationManager = (NotificationManager) GetSystemService(NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

Se debe crear el canal de notificación cada vez que se cree la actividad. En el `CreateNotificationChannel` caso del método, debe llamarse en `OnCreate` el método de una actividad.

### <a name="creating-and-publishing-a-notification"></a>Crear y publicar una notificación

Para generar una notificación en Android, siga estos pasos:

1. Cree una instancia `NotificationCompat.Builder` de un objeto.

2. Llame a varios métodos en `NotificationCompat.Builder` el objeto para establecer las opciones de notificación.

3. Llame al método [Build](xref:Android.App.Notification.Builder.Build) del `NotificationCompat.Builder` objeto para crear una instancia de un objeto de notificación.

4. Llame al método [Notify](xref:Android.App.NotificationManager.Notify*) del administrador de notificaciones para publicar la notificación.

Debe proporcionar al menos la siguiente información para cada notificación:

- Un icono pequeño (24x24 DP de tamaño)

- Un título corto

- El texto de la notificación

En el ejemplo de código siguiente se muestra cómo `NotificationCompat.Builder` utilizar para generar una notificación básica. Observe que los métodos `NotificationCompat.Builder` admiten el [encadenamiento de métodos](https://en.wikipedia.org/wiki/Method_chaining); es decir, cada método devuelve el objeto de generador para que pueda usar el resultado de la última llamada al método para invocar la siguiente llamada al método:

```csharp
// Instantiate the builder and set notification elements:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

En este ejemplo, se crea `NotificationCompat.Builder` una instancia `builder` de un nuevo objeto denominado, junto con el identificador del canal de notificación que se va a usar. Se establecen el título y el texto de la notificación, y el icono de notificación se carga desde **Resources/drawable/ic_notification. png**. La llamada al método del generador de `Build` notificaciones crea un objeto de notificación con esta configuración. El siguiente paso consiste en llamar `Notify` al método del administrador de notificaciones. Para buscar el administrador de notificaciones, `GetSystemService`llame a, como se mostró anteriormente.

El `Notify` método acepta dos parámetros: el identificador de notificación y el objeto de notificación. El identificador de notificación es un entero único que identifica la notificación en la aplicación. En este ejemplo, el identificador de notificación se establece en cero (0); sin embargo, en una aplicación de producción, querrá asignar a cada notificación un identificador único. Reutilizar el valor del identificador anterior en una llamada a `Notify` hace que se sobrescriba la última notificación.

Cuando este código se ejecuta en un dispositivo Android 5,0, genera una notificación similar a la del ejemplo siguiente:

![Resultado de la notificación para el código de ejemplo](local-notifications-images/09-hello-world.png)

El icono de notificación se muestra en el lado izquierdo de la notificación &ndash; esta imagen de un &ldquo;círculo tengo&rdquo; un canal alfa para que Android pueda dibujar un fondo circular gris detrás. También puede proporcionar un icono sin un canal alfa. Para mostrar una imagen fotográfica como un icono, consulte [formato de iconos grandes](#large-icon-format) más adelante en este tema.

La marca de tiempo se establece automáticamente, pero puede invalidar esta configuración mediante una llamada al método [SetWhen](xref:Android.App.Notification.Builder.SetWhen*) del generador de notificaciones. Por ejemplo, en el ejemplo de código siguiente se establece la marca de tiempo en la hora actual:

```csharp
builder.SetWhen (Java.Lang.JavaSystem.CurrentTimeMillis());
```

### <a name="enabling-sound-and-vibration"></a>Habilitar sonido y vibración

Si desea que la notificación también emita un sonido, puede llamar al método [SetDefaults](xref:Android.App.Notification.Builder.SetDefaults*) del generador de notificaciones y pasar la `NotificationDefaults.Sound` marca:

```csharp
// Instantiate the notification builder and enable sound:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetDefaults (NotificationDefaults.Sound)
    .SetSmallIcon (Resource.Drawable.ic_notification);
```

Esta llamada a `SetDefaults` hará que el dispositivo reproduzca un sonido cuando se publique la notificación. Si desea que el dispositivo vibra en lugar de reproducir un sonido, puede pasar `NotificationDefaults.Vibrate` a `SetDefaults.` si desea que el dispositivo reproduzca un sonido y vibrar el dispositivo, puede pasar ambas marcas a `SetDefaults`:

```csharp
builder.SetDefaults (NotificationDefaults.Sound | NotificationDefaults.Vibrate);
```

Si habilita el sonido sin especificar un sonido para reproducir, Android usa el sonido predeterminado de la notificación del sistema. Sin embargo, puede cambiar el sonido que se reproducirá llamando al método [SetSound](xref:Android.App.Notification.Builder.SetSound*) del generador de notificaciones. Por ejemplo, para reproducir el sonido de alarma con la notificación (en lugar del sonido de notificación predeterminado), puede obtener el URI del sonido de alarma desde el [RingtoneManager](xref:Android.Media.RingtoneManager) y pasarlo a `SetSound`:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Alarm));
```

Como alternativa, puede usar el sonido de tono predeterminado del sistema para su notificación:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Ringtone));
```

Después de crear un objeto de notificación, es posible establecer las propiedades de notificación en el objeto de notificación (en lugar de configurarlos de antemano a través `NotificationCompat.Builder` de los métodos). Por ejemplo, en lugar de llamar al `SetDefaults` método para habilitar la vibración en una notificación, puede modificar directamente la marca de bits de la propiedad de [valores predeterminados](xref:Android.App.Notification.Defaults) de la notificación:

```csharp
// Build the notification:
Notification notification = builder.Build();

// Turn on vibrate:
notification.Defaults |= NotificationDefaults.Vibrate;
```

Este ejemplo hace que el dispositivo vibra cuando se publica la notificación.

### <a name="updating-a-notification"></a>Actualización de una notificación

Si desea actualizar el contenido de una notificación una vez publicada, puede volver a usar el objeto existente `NotificationCompat.Builder` para crear un nuevo objeto de notificación y publicar esta notificación con el identificador de la última notificación. Por ejemplo:

```csharp
// Update the existing notification builder content:
builder.SetContentTitle ("Updated Notification");
builder.SetContentText ("Changed to this message.");

// Build a notification object with updated content:
notification = builder.Build();

// Publish the new notification with the existing ID:
notificationManager.Notify (notificationId, notification);
```

En este ejemplo, el objeto `NotificationCompat.Builder` existente se usa para crear un nuevo objeto de notificación con un título y un mensaje distintos.
El nuevo objeto de notificación se publica con el identificador de la notificación anterior y esto actualiza el contenido de la notificación publicada anteriormente:

![Notificación actualizada](local-notifications-images/12-updated-notification.png)

El cuerpo de la notificación anterior se reutiliza &ndash; solo el título y el texto de la notificación cambia mientras la notificación se muestra en el cajón de notificaciones. El texto del título cambia de "notificación de ejemplo" a "notificación actualizada" y el texto del mensaje cambia de "Hola mundo. Esta es mi primera notificación. en "cambio a este mensaje".

Una notificación permanece visible hasta que se produce una de estas tres acciones:

- El usuario descarta la notificación (o pulsa *Borrar todo*).

- La aplicación realiza una llamada a `NotificationManager.Cancel`, pasando el identificador de notificación único que se asignó cuando se publicó la notificación.

- La aplicación llama `NotificationManager.CancelAll`a.

Para obtener más información sobre cómo actualizar las notificaciones de Android, consulte [modificar una notificación](https://developer.android.com/training/notify-user/managing.html#Updating).

### <a name="starting-an-activity-from-a-notification"></a>Inicio de una actividad a partir de una notificación

En Android, es habitual asociar una notificación a una *acción* &ndash; que se inicia cuando el usuario pulsa la notificación. Esta actividad puede residir en otra aplicación o incluso en otra tarea. Para agregar una acción a una notificación, se crea un objeto [PendingIntent](xref:Android.App.PendingIntent) y se asocia `PendingIntent` con la notificación. Un `PendingIntent` es un tipo especial de intención que permite a la aplicación de destinatario ejecutar un fragmento de código predefinido con los permisos de la aplicación de envío. Cuando el usuario pulsa la notificación, Android inicia la actividad especificada por `PendingIntent`.

El siguiente fragmento de código muestra cómo crear una notificación con un `PendingIntent` que iniciará la actividad de la `MainActivity`aplicación de origen:

```csharp
// Set up an intent so that tapping the notifications returns to this app:
Intent intent = new Intent (this, typeof(MainActivity));

// Create a PendingIntent; we're only using one PendingIntent (ID = 0):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    PendingIntent.GetActivity (this, pendingIntentId, intent, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including pending intent:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentIntent (pendingIntent)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first action notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

Este código es muy similar al código de notificación de la sección anterior, excepto en que `PendingIntent` se agrega un al objeto de notificación. En este ejemplo, el `PendingIntent` está asociado a la actividad de la aplicación de origen antes de pasarse al método [SetContentIntent](xref:Android.App.Notification.Builder.SetContentIntent*) del generador de notificaciones. La `PendingIntentFlags.OneShot` marca se pasa `PendingIntent.GetActivity` al método para que `PendingIntent` se use solo una vez. Cuando se ejecuta este código, se muestra la siguiente notificación:

![Primera notificación de acción](local-notifications-images/10-first-action-notification.png)

Al pulsar esta notificación, el usuario vuelve a la actividad de origen.

En una aplicación de producción, la aplicación debe controlar la *pila de retroceso* cuando el usuario presiona el botón **atrás** dentro de la actividad de notificación (si no está familiarizado con las tareas de Android y la pila de retroceso, consulte [tareas y pila de retroceso](https://developer.android.com/guide/components/tasks-and-back-stack.html)).
En la mayoría de los casos, la navegación hacia atrás de la actividad de notificación debe devolver al usuario de la aplicación y volver a la pantalla principal. Para administrar la pila de retroceso, la aplicación usa la clase [TaskStackBuilder](xref:Android.App.TaskStackBuilder) para crear `PendingIntent` un con una pila de retroceso.

Otra consideración real es que la actividad de origen puede necesitar enviar datos a la actividad de notificación. Por ejemplo, la notificación puede indicar que ha llegado un mensaje de texto, y la actividad de notificación (una pantalla de visualización de mensajes), requiere el identificador del mensaje para mostrar el mensaje al usuario. La actividad que crea el `PendingIntent` puede usar el método [Intent. PutExtra](xref:Android.Content.Intent.PutExtra*) para agregar datos (por ejemplo, una cadena) al intento de modo que estos datos se pasen a la actividad de notificación.

En el ejemplo de código siguiente se muestra cómo `TaskStackBuilder` usar para administrar la pila de retroceso y se incluye un ejemplo de cómo enviar una cadena de un solo mensaje a una actividad `SecondActivity`de notificación denominada:

```csharp
// Setup an intent for SecondActivity:
Intent secondIntent = new Intent (this, typeof(SecondActivity));

// Pass some information to SecondActivity:
secondIntent.PutExtra ("message", "Greetings from MainActivity!");

// Create a task stack builder to manage the back stack:
TaskStackBuilder stackBuilder = TaskStackBuilder.Create(this);

// Add all parents of SecondActivity to the stack:
stackBuilder.AddParentStack (Java.Lang.Class.FromType (typeof (SecondActivity)));

// Push the intent that starts SecondActivity onto the stack:
stackBuilder.AddNextIntent (secondIntent);

// Obtain the PendingIntent for launching the task constructed by
// stackbuilder. The pending intent can be used only once (one shot):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    stackBuilder.GetPendingIntent (pendingIntentId, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including
// the pending intent:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentIntent (pendingIntent)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my second action notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

En este ejemplo de código, la aplicación consta de dos actividades `MainActivity` : (que contiene el código de notificación anterior) `SecondActivity`, y la pantalla que el usuario ve después de puntear en la notificación. Cuando se ejecuta este código, se presenta una notificación simple (similar al ejemplo anterior). Al pulsar la notificación, el usuario `SecondActivity` recibe la pantalla:

![Captura de pantalla de segunda actividad](local-notifications-images/11-second-activity.png)

El mensaje de cadena (que se pasa al `PutExtra` método de la intención) `SecondActivity` se recupera en a través de esta línea de código:

```csharp
// Get the message from the intent:
string message = Intent.Extras.GetString ("message", "");
```

Este mensaje recuperado, "Greetings from MainActivity!," se muestra `SecondActivity` en la pantalla, tal como se muestra en la captura de pantalla anterior. Cuando el usuario presiona el botón **atrás** mientras está `SecondActivity`en, la navegación conduce a la aplicación y vuelve a la pantalla anterior al inicio de la aplicación.

Para obtener más información sobre la creación de intenciones pendientes, vea [PendingIntent](xref:Android.App.PendingIntent).

<a name="beyond-the-basic-notification" />

## <a name="beyond-the-basic-notification"></a>Más allá de la notificación básica

Las notificaciones tienen como valor predeterminado un formato de diseño base simple en Android, pero puede mejorar este formato básico `NotificationCompat.Builder` realizando llamadas de método adicionales. En esta sección, aprenderá a agregar un icono de foto grande a la notificación y verá ejemplos de cómo crear notificaciones de diseño expandidas.

<a name="large-icon-format" />

### <a name="large-icon-format"></a>Formato de iconos grandes

Las notificaciones de Android suelen mostrar el icono de la aplicación de origen (en el lado izquierdo de la notificación). Sin embargo, las notificaciones pueden mostrar una imagen o una foto (un *icono grande*) en lugar del icono pequeño estándar. Por ejemplo, una aplicación de mensajería podría mostrar una foto del remitente en lugar del icono de la aplicación.

Este es un ejemplo de una notificación &ndash; básica de Android 5,0 que muestra solo el icono de aplicación pequeña:

![Notificación normal de ejemplo](local-notifications-images/13-sample-notification.png)

Y a continuación se muestra una captura de pantalla de la notificación después de modificarla para &ndash; mostrar un icono grande, se usa un icono creado a partir de una imagen de un Monkey de código Xamarin:

![Ejemplo de notificación de iconos grandes](local-notifications-images/14-large-icon-sample.png)

Tenga en cuenta que cuando se presenta una notificación en formato de iconos grandes, el icono de aplicación pequeña se muestra como un distintivo en la esquina inferior derecha del icono grande.

Para usar una imagen como un icono grande en una notificación, se llama al método [SetLargeIcon](xref:Android.App.Notification.Builder.SetLargeIcon*) del generador de notificaciones y se pasa un mapa de bits de la imagen. A diferencia `SetSmallIcon`de `SetLargeIcon` , solo acepta un mapa de bits. Para convertir un archivo de imagen en un mapa de bits, se usa la clase [BitmapFactory](xref:Android.Graphics.BitmapFactory) . Por ejemplo:

```csharp
builder.SetLargeIcon (BitmapFactory.DecodeResource (Resources, Resource.Drawable.monkey_icon));
```

Este código de ejemplo abre el archivo de imagen en **Resources/drawable/monkey_icon. png**, lo convierte en un mapa de bits y pasa `NotificationCompat.Builder`el mapa de bits resultante a. Normalmente, la resolución de la imagen de origen es mayor que &ndash; el icono pequeño pero no es mucho mayor. Una imagen demasiado grande podría producir operaciones de cambio de tamaño innecesarias que podrían retrasar el envío de la notificación.

### <a name="big-text-style"></a>Estilo de texto grande

El estilo de *texto grande* es una plantilla de diseño expandida que se usa para mostrar mensajes largos en las notificaciones. Al igual que todas las notificaciones de diseño expandidas, la notificación de Big Text se muestra inicialmente en un formato de presentación compacto:

![Ejemplo de notificación de texto grande](local-notifications-images/15-big-text-notification.png)

En este formato, solo se muestra un extracto del mensaje, finalizado en dos puntos. Cuando el usuario arrastra hacia abajo en la notificación, se expande para mostrar el mensaje de notificación completo:

![Notificación de Big Text expandida](local-notifications-images/16-big-text-expanded.png)

Este formato de diseño expandido también incluye texto de resumen en la parte inferior de la notificación. El alto máximo de la notificación de *Big Text* es 256 DP.

Para crear una notificación de *texto grande* , cree una instancia `NotificationCompat.Builder` de un objeto, como antes, y, a continuación, cree una instancia y `NotificationCompat.Builder` agregue un objeto [BigTextStyle](xref:Android.App.Notification.BigTextStyle) al objeto. Este es un ejemplo:

```csharp
// Instantiate the Big Text style:
Notification.BigTextStyle textStyle = new Notification.BigTextStyle();

// Fill it with text:
string longTextMessage = "I went up one pair of stairs.";
longTextMessage += " / Just like me. ";
//...
textStyle.BigText (longTextMessage);

// Set the summary text:
textStyle.SetSummaryText ("The summary text goes here.");

// Plug this style into the builder:
builder.SetStyle (textStyle);

// Create the notification and publish it ...
```

En este ejemplo, el texto del mensaje y el texto de resumen se `BigTextStyle` almacenan`textStyle`en el objeto () antes de pasarse a`NotificationCompat.Builder.`

### <a name="image-style"></a>Estilo de imagen

El estilo de *imagen* (también denominado estilo *Big Picture* ) es un formato de notificación expandido que se puede usar para mostrar una imagen en el cuerpo de una notificación. Por ejemplo, una aplicación de captura de pantalla o una aplicación fotográfica puede usar el estilo de notificación de *imagen* para proporcionar al usuario una notificación de la última imagen capturada. Tenga en cuenta que el alto máximo de la notificación de imagen &ndash; es 256 DP Android cambiará el tamaño de la imagen para ajustarse a esta restricción de alto máximo, dentro de los límites de memoria disponible.

Al igual que todas las notificaciones de diseño expandidas, las notificaciones de *imagen* se muestran en primer lugar en un formato compacto que muestra un extracto del texto del mensaje adjunto:

![La notificación de imagen compacta no muestra ninguna imagen](local-notifications-images/17-image-compact.png)

Cuando el usuario se arrastra hacia abajo en la notificación de *imagen* , se expande para mostrar una imagen. Por ejemplo, esta es la versión expandida de la notificación anterior:

![Imagen expandida la notificación de imagen](local-notifications-images/18-image-expanded.png)

Tenga en cuenta que cuando la notificación se muestra en formato compacto, muestra el texto de la notificación (el texto que se pasa al `SetContentText` método del generador de notificaciones, como se mostró anteriormente). Sin embargo, cuando la notificación se expande para mostrar la imagen, muestra el texto de resumen sobre la imagen.

Para crear una notificación de *imagen* , cree una instancia `NotificationCompat.Builder` de un objeto como antes y, a continuación, cree e inserte un `NotificationCompat.Builder` objeto [BigPictureStyle](xref:Android.App.Notification.BigPictureStyle) en el objeto. Por ejemplo:

```csharp
// Instantiate the Image (Big Picture) style:
Notification.BigPictureStyle picStyle = new Notification.BigPictureStyle();

// Convert the image to a bitmap before passing it into the style:
picStyle.BigPicture (BitmapFactory.DecodeResource (Resources, Resource.Drawable.x_bldg));

// Set the summary text that will appear with the image:
picStyle.SetSummaryText ("The summary text goes here.");

// Plug this style into the builder:
builder.SetStyle (picStyle);

// Create the notification and publish it ...
```

Al igual que el método `SetLargeIcon` de `NotificationCompat.Builder`, el método [BigPicture](xref:Android.App.Notification.BigPictureStyle.BigPicture*) de `BigPictureStyle` requiere un mapa de bits de la imagen que desea mostrar en el cuerpo de la notificación. En este ejemplo, el método [DecodeResource](xref:Android.Graphics.BitmapFactory.DecodeResource*) de `BitmapFactory` lee el archivo de imagen ubicado en **Resources/drawable/x_bldg. png** y lo convierte en un mapa de bits.

También puede mostrar imágenes que no están empaquetadas como un recurso. Por ejemplo, el siguiente código de ejemplo carga una imagen desde la tarjeta SD local y la muestra en una notificación de *imagen* :

```csharp
// Using the Image (Big Picture) style:
Notification.BigPictureStyle picStyle = new Notification.BigPictureStyle();

// Read an image from the SD card, subsample to half size:
BitmapFactory.Options options = new BitmapFactory.Options();
options.InSampleSize = 2;
string imagePath = "/sdcard/Pictures/my-tshirt.jpg";
picStyle.BigPicture (BitmapFactory.DecodeFile (imagePath, options));

// Set the summary text that will appear with the image:
picStyle.SetSummaryText ("Check out my new T-Shirt!");

// Plug this style into the builder:
builder.SetStyle (picStyle);

// Create notification and publish it ...
```

En este ejemplo, se carga el archivo de imagen que se encuentra en **/sdcard/Pictures/My-Tshirt.jpg** , se cambia el tamaño a la mitad de su tamaño original y, a continuación, se convierte en un mapa de bits para su uso en la notificación:

![Imagen de camiseta de ejemplo en la notificación](local-notifications-images/19-tshirt-notification.png)

Si no conoce el tamaño del archivo de imagen de antemano, es una buena idea encapsular la llamada a [BitmapFactory. DecodeFile](xref:Android.Graphics.BitmapFactory.DecodeFile*) en un controlador &ndash; de excepciones. se `OutOfMemoryError` puede producir una excepción si la imagen es demasiado grande para que Android cambie de tamaño.

Para obtener más información sobre cómo cargar y descodificar imágenes de mapa de bits de gran tamaño, consulte [carga eficaz de mapas de bits grandes](https://github.com/xamarin/recipes/tree/master/Recipes/android/resources/general/load_large_bitmaps_efficiently).

### <a name="inbox-style"></a>Estilo de bandeja de entrada

El formato de *bandeja de entrada* es una plantilla de diseño expandida diseñada para mostrar líneas de texto independientes (por ejemplo, un resumen de bandeja de entrada de correo electrónico) en el cuerpo de la notificación. La notificación de formato de *bandeja de entrada* se muestra primero en un formato compacto:

![Ejemplo de notificación de bandeja de entrada compacta](local-notifications-images/20-inbox-compact.png)

Cuando el usuario arrastra hacia abajo en la notificación, se expande para mostrar un resumen de correo electrónico como se muestra en la siguiente captura de pantalla:

![Notificación de bandeja de entrada de ejemplo expandida](local-notifications-images/21-inbox-expanded.png)

Para crear una notificación de *bandeja de entrada* , cree `NotificationCompat.Builder` una instancia de un objeto, como antes, y agregue un `NotificationCompat.Builder`objeto [InboxStyle](xref:Android.App.Notification.InboxStyle) a. Este es un ejemplo:

```csharp
// Instantiate the Inbox style:
Notification.InboxStyle inboxStyle = new Notification.InboxStyle();

// Set the title and text of the notification:
builder.SetContentTitle ("5 new messages");
builder.SetContentText ("chimchim@xamarin.com");

// Generate a message summary for the body of the notification:
inboxStyle.AddLine ("Cheeta: Bananas on sale");
inboxStyle.AddLine ("George: Curious about your blog post");
inboxStyle.AddLine ("Nikko: Need a ride to Evolve?");
inboxStyle.SetSummaryText ("+2 more");

// Plug this style into the builder:
builder.SetStyle (inboxStyle);
```

Para agregar nuevas líneas de texto al cuerpo de la notificación, llame al método [AddLine](xref:Android.App.Notification.InboxStyle.AddLine*) del `InboxStyle` objeto (el alto máximo de la notificación de *bandeja de entrada* es 256 DP). Tenga en cuenta que, a diferencia del estilo *Big Text* , el estilo *Inbox* es compatible con líneas de texto individuales en el cuerpo de la notificación.

También puede usar el estilo de *bandeja de entrada* para cualquier notificación que necesite Mostrar líneas individuales de texto en un formato expandido. Por ejemplo, el estilo de notificación de *bandeja de entrada* se puede usar para combinar varias notificaciones pendientes &ndash; en una notificación de resumen; puede actualizar una única notificación de estilo de *bandeja de entrada* con nuevas líneas de contenido de notificación (consulte [ Actualizar una notificación](#updating-a-notification) anterior), en lugar de generar una secuencia continua de notificaciones nuevas, principalmente similares.

## <a name="configuring-metadata"></a>Configuración de metadatos

`NotificationCompat.Builder`incluye métodos a los que se puede llamar para establecer metadatos sobre la notificación, como la prioridad, la visibilidad y la categoría. Android usa esta información &mdash; junto con la configuración &mdash; de preferencias del usuario para determinar cómo y cuándo mostrar las notificaciones.

### <a name="priority-settings"></a>Configuración de prioridad

Las aplicaciones que se ejecutan en Android 7,1 y menos necesitan establecer la prioridad directamente en la propia notificación. La configuración de prioridad de una notificación determina dos resultados cuando se publica la notificación:

- Donde aparece la notificación en relación con otras notificaciones.
    Por ejemplo, las notificaciones de prioridad alta se presentan por encima de las notificaciones de prioridad inferior en el cajón de notificaciones, independientemente de Cuándo se publicó cada notificación.

- Si la notificación se muestra en el formato de notificación de cara al día (Android 5,0 y versiones posteriores). Solo las notificaciones de prioridad *alta* y *máxima* se muestran como notificaciones de suscripción.

Xamarin. Android define las siguientes enumeraciones para establecer la prioridad de la notificación:

- `NotificationPriority.Max`&ndash; Alerta al usuario de una condición urgente o crítica (por ejemplo, una llamada entrante, instrucciones para activar o una alerta de emergencia). En dispositivos Android 5,0 y versiones posteriores, las notificaciones de prioridad máxima se muestran en el formato de cara arriba.

- `NotificationPriority.High`&ndash; Informa al usuario de eventos importantes (como correos electrónicos importantes o la llegada de mensajes de chat en tiempo real). En los dispositivos Android 5,0 y versiones posteriores, las notificaciones de prioridad alta se muestran en formato de cara a arriba.

- `NotificationPriority.Default`&ndash; Notifica al usuario las condiciones que tienen un nivel de importancia medio.

- `NotificationPriority.Low`&ndash; Para información no urgente a la que se debe informar al usuario (por ejemplo, recordatorios de actualizaciones de software o actualizaciones de red social).

- `NotificationPriority.Min`&ndash; Para obtener información general que el usuario solo advierte al ver las notificaciones (por ejemplo, la información de ubicación o información meteorológica).

Para establecer la prioridad de una notificación, llame al método [SetPriority](xref:Android.App.Notification.Builder.SetPriority*) del `NotificationCompat.Builder` objeto, pasando el nivel de prioridad. Por ejemplo:

```csharp
builder.SetPriority (NotificationPriority.High);
```

En el ejemplo siguiente, la notificación de alta prioridad, "un mensaje importante" aparece en la parte superior del cajón de notificaciones:

![Ejemplo de notificación de alta prioridad](local-notifications-images/22-hi-priority-drawer.png)

Dado que se trata de una notificación de prioridad alta, también se muestra como una notificación de cara arriba de la pantalla de actividad actual del usuario en Android 5,0:

![Ejemplo de notificación de suscripción](local-notifications-images/23-heads-up-example.png)

En el ejemplo siguiente, se muestra la notificación de prioridad baja "pensamiento para el día" bajo una notificación de nivel de batería de prioridad superior:

![Ejemplo de notificación de prioridad baja](local-notifications-images/24-lo-priority-drawer.png)

Dado que la notificación "pensamiento durante el día" es una notificación de prioridad baja, Android no la mostrará en formato de cabeza.

> [!NOTE]
> En Android 8,0 y versiones posteriores, la prioridad de la configuración del canal de notificación y del usuario determinará la prioridad de la notificación.

### <a name="visibility-settings"></a>Configuración de visibilidad

A partir de Android 5,0, la configuración de *visibilidad* está disponible para controlar la cantidad de contenido de notificación que aparece en la pantalla de bloqueo seguro.
Xamarin. Android define las siguientes enumeraciones para establecer la visibilidad de la notificación:

- `NotificationVisibility.Public`&ndash; El contenido completo de la notificación se muestra en la pantalla de bloqueo seguro.

- `NotificationVisibility.Private`&ndash; En la pantalla de bloqueo seguro solo se muestra información esencial (por ejemplo, el icono de notificación y el nombre de la aplicación que la registró), pero el resto de los detalles de la notificación están ocultos. Todas las notificaciones tienen `NotificationVisibility.Private`como valor predeterminado.

- `NotificationVisibility.Secret`&ndash; No se muestra nada en la pantalla de bloqueo seguro, ni siquiera en el icono de notificación. El contenido de la notificación solo está disponible después de que el usuario desbloquea el dispositivo.

Para establecer la visibilidad de una notificación, las aplicaciones llaman `SetVisibility` al método `NotificationCompat.Builder` del objeto y pasan la configuración de visibilidad. Por ejemplo, esta llamada a `SetVisibility` realiza la notificación `Private`:

```csharp
builder.SetVisibility (NotificationVisibility.Private);
```

Cuando se `Private` envía una notificación, solo se muestra el nombre y el icono de la aplicación en la pantalla de bloqueo seguro. En lugar del mensaje de notificación, el usuario ve "desbloquear el dispositivo para ver esta notificación":

![Desbloquear el mensaje de notificación del dispositivo](local-notifications-images/25-lockscreen-private.png)

En este ejemplo, **NotificationsLab** es el nombre de la aplicación de origen. Esta versión censurada de la notificación solo aparece cuando la pantalla de bloqueo es segura (es decir, protegida mediante PIN, patrón o contraseña) &ndash; si la pantalla de bloqueo no es segura, el contenido completo de la notificación está disponible en la pantalla de bloqueo.

### <a name="category-settings"></a>Configuración de categorías

A partir de Android 5,0, las categorías predefinidas están disponibles para clasificar y filtrar las notificaciones. Xamarin. Android proporciona las siguientes enumeraciones para estas categorías:

- `Notification.CategoryCall`&ndash; Llamada telefónica entrante.

- `Notification.CategoryMessage`&ndash; Mensaje de texto entrante.

- `Notification.CategoryAlarm`&ndash; Una condición de alarma o una expiración del temporizador.

- `Notification.CategoryEmail`&ndash; Mensaje de correo electrónico entrante.

- `Notification.CategoryEvent`&ndash; Un evento de calendario.

- `Notification.CategoryPromo`&ndash; Un mensaje o anuncio promocional.

- `Notification.CategoryProgress`&ndash; Progreso de una operación en segundo plano.

- `Notification.CategorySocial`&ndash; Actualización de redes sociales.

- `Notification.CategoryError`&ndash; Error de una operación en segundo plano o proceso de autenticación.

- `Notification.CategoryTransport`&ndash; Actualización de reproducción de medios.

- `Notification.CategorySystem`&ndash; Reservado para uso del sistema (estado del sistema o del dispositivo).

- `Notification.CategoryService`&ndash; Indica que se está ejecutando un servicio en segundo plano.

- `Notification.CategoryRecommendation`&ndash; Un mensaje de recomendación relacionado con la aplicación que se está ejecutando actualmente.

- `Notification.CategoryStatus`&ndash; Información sobre el dispositivo.

Cuando se ordenan las notificaciones, la prioridad de la notificación tiene prioridad sobre la configuración de la categoría. Por ejemplo, una notificación de alta prioridad se mostrará como un aviso, incluso si pertenece a la `Promo` categoría. Para establecer la categoría de una notificación, se llama al `SetCategory` método `NotificationCompat.Builder` del objeto, pasando el valor de la categoría. Por ejemplo:

```csharp
builder.SetCategory (Notification.CategoryCall);
```

La característica no *molestar* (novedad en Android 5,0) filtra las notificaciones en función de las categorías. Por ejemplo, la pantalla *no molestar* en **configuración** permite al usuario excluir las notificaciones de llamadas telefónicas y mensajes:

![No molestar modificadores de pantalla](local-notifications-images/26-do-not-disturb.png)

Cuando el usuario configura *no molesta* para bloquear todas las interrupciones excepto las llamadas telefónicas (como se muestra en la captura de pantalla anterior), Android permite que se presenten notificaciones con un valor de categoría de `Notification.CategoryCall` cuando el dispositivo está en no  *modo de molestar* . Tenga en `Notification.CategoryAlarm` cuenta que las notificaciones no se bloquean nunca en el modo no *molestar* .

En el ejemplo [LocalNotifications](https://docs.microsoft.com/samples/xamarin/monodroid-samples/localnotifications) se muestra cómo `NotificationCompat.Builder` usar para iniciar una segunda actividad desde una notificación. Este código de ejemplo se explica en el tutorial [uso de notificaciones locales en Xamarin. Android](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md) .

### <a name="notification-styles"></a>Estilos de notificación

Para crear notificaciones de estilo *Big Text*, *Image*o Inbox en `NotificationCompat.Builder`la *bandeja de entrada* con, la aplicación debe usar las versiones de compatibilidad de estos estilos. Por ejemplo, para usar el estilo *Big Text* , cree una `NotificationCompat.BigTextstyle`instancia de:

```csharp
NotificationCompat.BigTextStyle textStyle = new NotificationCompat.BigTextStyle();

// Plug this style into the builder:
builder.SetStyle (textStyle);
```

Del mismo modo, la aplicación `NotificationCompat.InboxStyle` puede `NotificationCompat.BigPictureStyle` usar y para los estilos de *imagen* y *bandeja de entrada* , respectivamente.

### <a name="notification-priority-and-category"></a>Categoría y prioridad de la notificación

`NotificationCompat.Builder`admite el `SetPriority` método (disponible a partir de Android 4,1). Sin embargo, `SetCategory` el método *no* es compatible `NotificationCompat.Builder` con porque las categorías forman parte del nuevo sistema de metadatos de notificación que se presentó en Android 5,0.

Para admitir versiones anteriores de Android, donde `SetCategory` no está disponible, el código puede comprobar el nivel de API en tiempo de ejecución para `SetCategory` llamar condicionalmente cuando el nivel de API es igual o mayor que Android 5,0 (nivel de API 21):

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetCategory (Notification.CategoryEmail);
}
```

En este ejemplo, la plataforma de **destino** de la aplicación está establecida en Android 5,0 y la **versión mínima de Android** está establecida en **Android 4,1 (nivel de API 16)** . Dado `SetCategory` que está disponible en el nivel de API 21 y versiones posteriores, este `SetCategory` código de ejemplo solo llamará cuando esté disponible `SetCategory` &ndash; ; no se llamará cuando el nivel de API sea inferior a 21.

### <a name="lock-screen-visibility"></a>Visibilidad de la pantalla de bloqueo

Dado que Android no admitía las notificaciones de la pantalla de bloqueo antes de Android 5,0 `NotificationCompat.Builder` (nivel de API `SetVisibility` 21), no admite el método. Como se explicó anteriormente `SetCategory`para, el código puede comprobar el nivel de API en tiempo `SetVisiblity` de ejecución y llamar solo cuando está disponible:

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetVisibility (Notification.Public);
}
```

## <a name="summary"></a>Resumen

En este artículo se explica cómo crear notificaciones locales en Android. Se ha descrito la anatomía de una notificación, se ha explicado `NotificationCompat.Builder` cómo usar para crear notificaciones, cómo aplicar estilo a las notificaciones en los formatos de iconos grandes, de *texto grande*, de *imágenes* y de *bandeja de entrada* , cómo establecer la configuración de los metadatos de notificación, como prioridad, visibilidad y categoría, y cómo iniciar una actividad desde una notificación. En este artículo también se describe cómo funciona esta configuración de notificaciones con las nuevas características de cabeza de seguridad, pantalla de bloqueo y *no molestar* que se introdujeron en Android 5,0. Por último, aprendió a usar `NotificationCompat.Builder` para mantener la compatibilidad con las notificaciones con versiones anteriores de Android.

Para obtener instrucciones sobre el diseño de notificaciones para Android, vea [Notificaciones](https://developer.android.com/guide/topics/ui/notifiers/notifications.html).

## <a name="related-links"></a>Vínculos relacionados

- [NotificationsLab (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-notificationslab)
- [LocalNotifications (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/localnotifications)
- [Tutorial de notificaciones locales en Android](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)
- [Notificar al usuario](https://developer.android.com/training/notify-user/index.html)
- [Aviso](xref:Android.App.Notification)
- [NotificationManager](xref:Android.App.NotificationManager)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](xref:Android.App.PendingIntent)
