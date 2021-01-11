---
title: Notificaciones de usuario mejoradas en Xamarin. iOS
description: En este artículo se describe el marco de trabajo de notificaciones de usuario introducido en iOS 10. Describe las notificaciones locales, las notificaciones remotas, la administración de notificaciones, las acciones de notificación, etc.
ms.prod: xamarin
ms.assetid: 4E1FF652-28F0-4566-B383-9D12664401A4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2017
ms.openlocfilehash: 8a18bfe3a72334eab3304492da63e6ce8f889a72
ms.sourcegitcommit: 3edcc63fcf86409b73cd6e5dc77f0093a99b3f87
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2021
ms.locfileid: "98062620"
---
# <a name="enhanced-user-notifications-in-xamarinios"></a>Notificaciones de usuario mejoradas en Xamarin. iOS

Como novedad de iOS 10, el marco de trabajo de notificaciones de usuario permite la entrega y el control de las notificaciones locales y remotas. Con este marco de trabajo, una extensión de aplicación o aplicación puede programar la entrega de notificaciones locales especificando un conjunto de condiciones como la ubicación o la hora del día.

## <a name="about-user-notifications"></a>Acerca de las notificaciones de usuario

Como se indicó anteriormente, el nuevo marco de trabajo de notificaciones de usuario permite la entrega y el control de notificaciones locales y remotas. Con este marco de trabajo, una extensión de aplicación o aplicación puede programar la entrega de notificaciones locales especificando un conjunto de condiciones como la ubicación o la hora del día.

Además, la aplicación o la extensión pueden recibir (y potencialmente modificar) las notificaciones locales y remotas, ya que se entregan al dispositivo iOS del usuario.

El marco de trabajo de la interfaz de usuario de notificaciones de usuario nuevo permite personalizar la apariencia de las notificaciones locales y remotas cuando se presentan al usuario.

Este marco de trabajo proporciona las siguientes maneras en que una aplicación puede enviar notificaciones a un usuario:

- **Alertas visuales** : donde la notificación se revierte desde la parte superior de la pantalla como un banner.
- **Sonido y vibraciones** : pueden asociarse con una notificación.
- **Icono de la aplicación distintivos** : donde el icono de la aplicación muestra un distintivo que muestra que el nuevo contenido está disponible, como el número de mensajes de correo electrónico no leídos.

Además, en función del contexto actual del usuario, hay diferentes maneras en que se presentará una notificación:

- Si el dispositivo está desbloqueado, la notificación se retirará de la parte superior de la pantalla como un banner.
- Si el dispositivo está bloqueado, la notificación se mostrará en la pantalla de bloqueo del usuario.
- Si el usuario ha perdido una notificación, puede abrir el centro de notificaciones y ver cualquier notificación disponible y en espera.

Una aplicación Xamarin. iOS tiene dos tipos de notificaciones de usuario que se pueden enviar:

- **Notificaciones locales** : las envían las aplicaciones instaladas localmente en el dispositivo de los usuarios.
- **Notificaciones remotas** : se envían desde un servidor remoto y se presentan al usuario, o bien desencadenan una actualización en segundo plano del contenido de la aplicación.

### <a name="about-local-notifications"></a>Acerca de las notificaciones locales

Las notificaciones locales que una aplicación de iOS puede enviar tienen las siguientes características y atributos:

- Los envían las aplicaciones que son locales en el dispositivo del usuario. 
- Se pueden configurar para usar desencadenadores de hora o de ubicación. 
- La aplicación programa la notificación con el dispositivo del usuario y se muestra cuando se cumple la condición del desencadenador.
- Cuando el usuario interactúa con una notificación, la aplicación recibirá una devolución de llamada.

Algunos ejemplos de notificaciones locales incluyen:

- Alertas de calendario
- Alertas de recordatorio
- Desencadenadores con reconocimiento de ubicación

Para obtener más información, consulte la documentación de la [Guía de programación de notificaciones locales y remotas](https://developer.apple.com/documentation/usernotifications) de Apple.

### <a name="about-remote-notifications"></a>Acerca de las notificaciones remotas

Las notificaciones remotas que puede enviar una aplicación iOS tienen las siguientes características y atributos:

- La aplicación tiene un componente del lado servidor con el que se comunica.
- El Apple Push Notification Service (APNs) se usa para transmitir la entrega de mejor esfuerzo de las notificaciones remotas al dispositivo del usuario desde los servidores basados en la nube del desarrollador.
- Cuando la aplicación recibe la notificación remota, se mostrará al usuario.
- Cuando el usuario interactúa con la notificación, la aplicación recibirá una devolución de llamada.

Algunos ejemplos de notificaciones remotas son:

- Alertas de noticias
- Actualizaciones deportivas
- Mensajes de mensajería instantánea

Hay dos tipos de notificaciones remotas disponibles para una aplicación de iOS:

- **Accesible** para el usuario: se muestra al usuario en el dispositivo.
- **Actualizaciones silenciosas** : proporcionan un mecanismo para actualizar el contenido de una aplicación iOS en segundo plano. Cuando se recibe una actualización silenciosa, la aplicación puede ponerse en contacto con los servidores remotos para desplegar el contenido más reciente.

Para obtener más información, consulte la documentación de la [Guía de programación de notificaciones locales y remotas](https://developer.apple.com/documentation/usernotifications) de Apple.

### <a name="about-the-existing-notifications-api"></a>Acerca de la API de notificaciones existente

Antes de iOS 10, una aplicación de iOS usaría `UIApplication` para registrar una notificación en el sistema y programar cómo se debe desencadenar dicha notificación (por hora o por ubicación).

Hay varios problemas que un desarrollador puede encontrar al trabajar con la API de notificación existente:

- Se requieren diferentes devoluciones de llamada para las notificaciones locales o remotas, lo que podría provocar la duplicación del código.
- La aplicación tenía un control limitado de la notificación después de haberla programado con el sistema.
- Había diferentes niveles de compatibilidad en todas las plataformas existentes de Apple.

### <a name="about-the-new-user-notification-framework"></a>Acerca del nuevo marco de notificaciones de usuario

Con iOS 10, Apple ha introducido el nuevo marco de notificaciones de usuario, que reemplaza al `UIApplication` método existente anterior.

El marco de trabajo de notificaciones de usuario proporciona lo siguiente:

- Una API conocida que incluye paridad de características con los métodos anteriores, lo que facilita el traslado de código desde el marco de trabajo existente.
- Incluye un conjunto expandido de opciones de contenido que permite enviar notificaciones más completas al usuario.
- Las notificaciones locales y remotas se pueden controlar mediante el mismo código y las devoluciones de llamada.
- Simplifica el proceso de control de las devoluciones de llamada que se envían a una aplicación cuando el usuario interactúa con una notificación.
- Administración mejorada de las notificaciones pendientes y entregadas, incluida la capacidad de quitar o actualizar las notificaciones.
- Agrega la capacidad de realizar la presentación en aplicación de notificaciones.
- Agrega la capacidad de programar y controlar las notificaciones desde las extensiones de la aplicación.
- Agrega un nuevo punto de extensión para las notificaciones. 

El nuevo marco de trabajo de notificaciones de usuario proporciona una API de notificaciones unificada en el múltiplo de las plataformas que admite Apple, entre las que se incluyen: 

- **iOS** : compatibilidad completa para administrar y programar notificaciones.
- **tvOS** : agrega la capacidad de los iconos de aplicación de distintivo para las notificaciones locales y remotas.
- **watchos** : agrega la capacidad de reenviar notificaciones desde el dispositivo iOS emparejado del usuario a su Apple Watch y proporciona a las aplicaciones de supervisión la capacidad de realizar notificaciones locales directamente en el propio reloj.
- **MacOS** : compatibilidad completa para administrar y programar notificaciones.

Para obtener más información, consulte la documentación de [UserNotifications Framework](https://developer.apple.com/reference/usernotifications) y [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui) de Apple.

## <a name="preparing-for-notification-delivery"></a>Preparación para la entrega de notificaciones

Antes de que una aplicación de iOS pueda enviar notificaciones al usuario, la aplicación debe estar registrada en el sistema y, dado que una notificación es una interrupción para el usuario, una aplicación debe solicitar explícitamente el permiso antes de enviarlos.

Hay tres niveles diferentes de solicitudes de notificación que el usuario puede aprobar para una aplicación:

- Pancarta.
- Alertas de sonido.
- Distintivos el icono de la aplicación.

Además, estos niveles de aprobación deben solicitarse y configurarse para las notificaciones locales y remotas.

El permiso de notificación debe solicitarse en cuanto se inicie la aplicación agregando el código siguiente al `FinishedLaunching` método de `AppDelegate` y estableciendo el tipo de notificación deseado ( `UNAuthorizationOptions` ):

> [!NOTE]
> `UNUserNotificationCenter` solo está disponible en iOS 10 +. Por lo tanto, se recomienda comprobar la versión de macOS antes de enviar la solicitud. 

```csharp
using UserNotifications;
...

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    // Version check
    if (UIDevice.CurrentDevice.CheckSystemVersion (10, 0)) {
        // Request notification permissions from the user
        UNUserNotificationCenter.Current.RequestAuthorization (UNAuthorizationOptions.Alert, (approved, err) => {
            // Handle approval
        });
    }

    return true;
}
```

Como esta API está unificada y también funciona en Mac 10.14 +, si está destino macOS también debe comprobar el permiso de notificación lo antes posible:

```csharp
using UserNotifications;
...

public override void DidFinishLaunching (NSNotification notification)
{
    // Check we're at least v10.14
    if (NSProcessInfo.ProcessInfo.IsOperatingSystemAtLeastVersion (new NSOperatingSystemVersion (10, 14, 0))) {
        // Request notification permissions from the user
        UNUserNotificationCenter.Current.RequestAuthorization (UNAuthorizationOptions.Alert | UNAuthorizationOptions.Badge | UNAuthorizationOptions.Sound, (approved, err) => {
            // Handle approval
        });
    }
}

> [!NOTE]
> With MacOS apps, for the permission dialog to appear, you must sign your macOS app, even if building locally in DEBUG mode. Therefore, **Project->Options->Mac Signing->Sign the application bundle** must be checked.

Additionally, a user can always change the notification privileges for an app at any time using the **Settings** app on the device. The app should check for the user's requested notification privileges before presenting a notification using the following code:

```csharp
// Get current notification settings
UNUserNotificationCenter.Current.GetNotificationSettings ((settings) => {
    var alertsAllowed = (settings.AlertSetting == UNNotificationSetting.Enabled);
});    
``` 

### <a name="configuring-the-remote-notifications-environment"></a>Configuración del entorno de notificaciones remotas

Como novedad de iOS 10, el desarrollador debe informar al sistema operativo de en qué entorno se ejecuta la notificación de inserciones de entorno como desarrollo o producción. Si no se proporciona esta información, puede dar lugar a que se rechace la aplicación cuando se envía a la tienda de aplicaciones de iTunes con una notificación similar a la siguiente:

> Falta el derecho a la notificación de envío: la aplicación incluye una API para el servicio de notificaciones de envío de Apple, pero `aps-environment` falta el derecho en la firma de la aplicación.

Para proporcionar el derecho necesario, haga lo siguiente:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Haga doble clic en el `Entitlements.plist` archivo en el **Panel de solución** para abrirlo para su edición.
2. Cambie a la vista de **código fuente** : 

    [![Vista de código fuente](enhanced-user-notifications-images/setup01.png)](enhanced-user-notifications-images/setup01.png#lightbox)
3. Haga clic en el **+** botón para agregar una nueva clave.
4. Escriba `aps-environment` para la **propiedad**, deje el **tipo** como `String` y escriba `development` o `production` para el **valor**: 

    [![La propiedad APS-Environment](enhanced-user-notifications-images/setup02.png)](enhanced-user-notifications-images/setup02.png#lightbox)
5. Guarde los cambios en el archivo.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Haga doble clic en el `Entitlements.plist` archivo en el **Explorador de soluciones** para abrirlo para su edición.
2. Haga clic en el **+** botón para agregar una nueva clave.
3. Escriba `aps-environment` para la **propiedad**, deje el **tipo** como `String` y escriba `development` o `production` para el **valor**: 

    [![La propiedad APS-Environment](enhanced-user-notifications-images/setup02w.png)](enhanced-user-notifications-images/setup02.png#lightbox)
4. Guarde los cambios en el archivo.

-----

### <a name="registering-for-remote-notifications"></a>Registro de notificaciones remotas

Si la aplicación va a enviar y recibir notificaciones remotas, aún tendrá que realizar el _registro de tokens_ mediante la `UIApplication` API existente. Este registro requiere que el dispositivo tenga acceso de conexión de red activo APNs, lo que generará el token necesario que se enviará a la aplicación. La aplicación debe reenviar este token a la aplicación del lado servidor del desarrollador para registrarse para recibir notificaciones remotas:

[![Introducción al registro de tokens](enhanced-user-notifications-images/token01.png)](enhanced-user-notifications-images/token01.png#lightbox)

Use el código siguiente para inicializar el registro necesario:

```csharp
UIApplication.SharedApplication.RegisterForRemoteNotifications ();
```

El token que se envía a la aplicación del lado servidor del desarrollador deberá incluirse como parte de la carga de notificación que se envía desde el servidor a APNs cuando se envía una notificación remota:

[![El token incluido como parte de la carga de notificación](enhanced-user-notifications-images/token02.png)](enhanced-user-notifications-images/token02.png#lightbox)

El token actúa como la clave que une la notificación y la aplicación que se usa para abrir o responder a la notificación.

Para obtener más información, consulte la documentación de la [Guía de programación de notificaciones locales y remotas](https://developer.apple.com/documentation/usernotifications) de Apple.

## <a name="notification-delivery"></a>Entrega de notificaciones

Una vez que la aplicación esté totalmente registrada y los permisos necesarios solicitados y concedidos por el usuario, la aplicación ya está lista para enviar y recibir notificaciones. 

### <a name="providing-notification-content"></a>Proporcionar contenido de notificación

Como novedad de iOS 10, todas las notificaciones contienen un **título** y un **subtítulo** que siempre se mostrarán con el **cuerpo** del contenido de la notificación. También es nuevo, es la capacidad de agregar **datos adjuntos de multimedia** al contenido de notificación.

Para crear el contenido de una notificación local, use el código siguiente:

```csharp
var content = new UNMutableNotificationContent();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
```

En el caso de las notificaciones remotas, el proceso es similar:

```csharp
{
    "aps":{
        "alert":{
            "title":"Notification Title",
            "subtitle":"Notification Subtitle",
            "body":"This is the message body of the notification."
        },
        "badge":1
    }
}
```

### <a name="scheduling-when-a-notification-is-sent"></a>Programar Cuándo se envía una notificación

Con el contenido de la notificación creada, la aplicación debe programar Cuándo se presentará la notificación al usuario mediante el establecimiento de un *desencadenador*. iOS 10 proporciona cuatro tipos de desencadenadores diferentes:

- **Notificación push** : se usa exclusivamente con notificaciones remotas y se desencadena cuando APNs envía un paquete de notificación a la aplicación que se ejecuta en el dispositivo.
- **Intervalo de tiempo** : permite programar una notificación local desde un intervalo de tiempo de inicio con ahora y finalizar un momento posterior. Por ejemplo, `var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);`.
- **Fecha del calendario** : permite programar las notificaciones locales para una fecha y hora específicas.
- **Basado en la ubicación** : permite programar las notificaciones locales cuando el dispositivo iOS entra o sale de una ubicación geográfica específica o está en una proximidad determinada a cualquier señal de Bluetooth.

Cuando una notificación local está lista, la aplicación debe llamar al `Add` método del `UNUserNotificationCenter` objeto para programar su presentación al usuario. En el caso de las notificaciones remotas, la aplicación del lado servidor envía una carga de notificación a APNs, que luego envía el paquete al dispositivo del usuario.

Al reunir todas las piezas, una notificación local de ejemplo podría ser similar a la siguiente:

```csharp
using UserNotifications;
...

var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;

var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);

var requestID = "sampleRequest";
var request = UNNotificationRequest.FromIdentifier (requestID, content, trigger);

UNUserNotificationCenter.Current.AddNotificationRequest (request, (err) => {
    if (err != null) {
        // Do something with error...
    }
});
```

## <a name="handling-foreground-app-notifications"></a>Control de las notificaciones de aplicación en primer plano

Como novedad de iOS 10, una aplicación puede controlar las notificaciones de forma distinta cuando está en primer plano y se desencadena una notificación. Al proporcionar `UNUserNotificationCenterDelegate` e implementar el `WillPresentNotification` método, la aplicación puede asumir la responsabilidad de mostrar la notificación. Por ejemplo:

```csharp
using System;
using UserNotifications;

namespace MonkeyNotification
{
    public class UserNotificationCenterDelegate : UNUserNotificationCenterDelegate
    {
        #region Constructors
        public UserNotificationCenterDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void WillPresentNotification (UNUserNotificationCenter center, UNNotification notification, Action<UNNotificationPresentationOptions> completionHandler)
        {
            // Do something with the notification
            Console.WriteLine ("Active Notification: {0}", notification);

            // Tell system to display the notification anyway or use
            // `None` to say we have handled the display locally.
            completionHandler (UNNotificationPresentationOptions.Alert);
        }
        #endregion
    }
}
```

Este código simplemente escribe el contenido de `UNNotification` en la salida de la aplicación y pide al sistema que muestre la alerta estándar para la notificación. 

Si la aplicación desea mostrar la notificación en sí misma cuando estaba en primer plano y no usar los valores predeterminados del sistema, pase `None` al controlador de finalización. Ejemplo:

```csharp
completionHandler (UNNotificationPresentationOptions.None);
```

Con este código en su lugar, abra el `AppDelegate.cs` archivo para editarlo y cambie el `FinishedLaunching` método para que tenga un aspecto similar al siguiente:

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    // Request notification permissions from the user
    UNUserNotificationCenter.Current.RequestAuthorization (UNAuthorizationOptions.Alert, (approved, err) => {
        // Handle approval
    });

    // Watch for notifications while the app is active
    UNUserNotificationCenter.Current.Delegate = new UserNotificationCenterDelegate ();

    return true;
}
```

Este código está adjuntando el personalizado `UNUserNotificationCenterDelegate` anterior al actual para `UNUserNotificationCenter` que la aplicación pueda controlar la notificación mientras está activo y en primer plano.

## <a name="notification-management"></a>Administración de notificaciones

Como novedad de iOS 10, Notification Management proporciona acceso a las notificaciones pendientes y entregadas y agrega la capacidad de quitar, actualizar o promover estas notificaciones.

Una parte importante de la administración de notificaciones es el _identificador de solicitud_ que se asignó a la notificación cuando se creó y programó con el sistema. En el caso de las notificaciones remotas, se asigna a través del `apps-collapse-id` campo nuevo en el encabezado de la solicitud HTTP.

El identificador de solicitud se usa para seleccionar la notificación en la que la aplicación desea realizar la administración de notificaciones.

### <a name="removing-notifications"></a>Quitar notificaciones

Para quitar una notificación pendiente del sistema, utilice el siguiente código:

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemovePendingNotificationRequests (requests);
```

Para quitar una notificación ya entregada, use el código siguiente:

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemoveDeliveredNotifications (requests);
```

### <a name="updating-an-existing-notification"></a>Actualizar una notificación existente

Para actualizar una notificación existente, solo tiene que crear una nueva notificación con los parámetros deseados modificados (como una nueva hora del desencadenador) y agregarla al sistema con el mismo identificador de solicitud que la notificación que debe modificarse. Ejemplo:

```csharp
using UserNotifications;
...

// Rebuild notification
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;

// New trigger time
var trigger = UNTimeIntervalNotificationTrigger.CreateTrigger (10, false);

// ID of Notification to be updated
var requestID = "sampleRequest";
var request = UNNotificationRequest.FromIdentifier (requestID, content, trigger);

// Add to system to modify existing Notification
UNUserNotificationCenter.Current.AddNotificationRequest (request, (err) => {
    if (err != null) {
        // Do something with error...
    }
});
```

En el caso de las notificaciones ya entregadas, la notificación existente se actualizará y se promoverá a la parte superior de la lista en las pantallas Inicio y de bloqueo, y en el centro de notificaciones si ya la ha leído el usuario.

## <a name="working-with-notification-actions"></a>Trabajar con acciones de notificación

En iOS 10, las notificaciones que se entregan al usuario no son estáticas y proporcionan varias maneras en las que el usuario puede interactuar con ellas (desde las acciones integradas a las personalizadas).

Hay tres tipos de acciones a las que puede responder una aplicación iOS:

- **Acción predeterminada** : es cuando el usuario puntea una notificación para abrir la aplicación y mostrar los detalles de la notificación dada.
- **Acciones personalizadas** : se agregaron en iOS 8 y proporcionan una manera rápida para que el usuario realice una tarea personalizada directamente desde la notificación sin necesidad de iniciar la aplicación. Se pueden presentar como una lista de botones con títulos personalizables o un campo de entrada de texto que se puede ejecutar en segundo plano (donde la aplicación tiene una pequeña cantidad de tiempo para completar la solicitud) o en el primer plano (donde la aplicación se inicia en primer plano para satisfacer la solicitud). Las acciones personalizadas están disponibles en iOS y watchos.
- **Acción de descartar** : esta acción se envía a la aplicación cuando el usuario descarta una notificación determinada.

### <a name="creating-custom-actions"></a>Crear acciones personalizadas

Para crear y registrar una acción personalizada con el sistema, use el código siguiente:

```csharp
// Create action
var actionID = "reply";
var title = "Reply";
var action = UNNotificationAction.FromIdentifier (actionID, title, UNNotificationActionOptions.None);

// Create category
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.None);
    
// Register category
var categories = new UNNotificationCategory [] { category };
UNUserNotificationCenter.Current.SetNotificationCategories (new NSSet<UNNotificationCategory>(categories)); 
```

Al crear un nuevo `UNNotificationAction` , se le asigna un identificador único y el título que aparecerá en el botón. De forma predeterminada, la acción se creará como una acción en segundo plano; sin embargo, se pueden proporcionar opciones para ajustar el comportamiento de la acción (por ejemplo, establecerla como una acción en primer plano).

Cada una de las acciones creadas debe estar asociada a una categoría. Al crear un nuevo `UNNotificationCategory` , se le asigna un identificador único, una lista de acciones que puede realizar, una lista de identificadores de intención para proporcionar más información sobre la intención de las acciones en la categoría y algunas opciones para controlar el comportamiento de la categoría.

Por último, todas las categorías se registran con el sistema mediante el `SetNotificationCategories` método.

### <a name="presenting-custom-actions"></a>Presentación de acciones personalizadas

Una vez que se ha creado y registrado un conjunto de acciones y categorías personalizadas con el sistema, se pueden presentar desde las notificaciones locales o remotas.

En el caso de las notificaciones remotas, establezca una `category` en la carga de notificación remota que coincida con una de las categorías creadas anteriormente. Por ejemplo:

```csharp
{
    aps:{
        alert:"Hello world!",
        category:"message"
    }
}
```

En el caso de las notificaciones locales, establezca la `CategoryIdentifier` propiedad del `UNMutableNotificationContent` objeto. Por ejemplo:

```csharp
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
content.CategoryIdentifier = "message";
...
```

De nuevo, este identificador debe coincidir con una de las categorías que se crearon anteriormente.

### <a name="handling-dismiss-actions"></a>Controlar acciones de descartar

Como se indicó anteriormente, se puede enviar una acción de descartar a la aplicación cuando el usuario descarta una notificación. Como no se trata de una acción estándar, se debe establecer una opción cuando se crea la categoría. Por ejemplo:

```csharp
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.CustomDismissAction);

```

### <a name="handling-action-responses"></a>Control de las respuestas de acción

Cuando el usuario interactúa con las acciones y las categorías personalizadas que se crearon anteriormente, la aplicación necesita completar la tarea solicitada. Esto se hace proporcionando `UNUserNotificationCenterDelegate` e implementando el `UserNotificationCenter` método. Por ejemplo:

```csharp
using System;
using UserNotifications;

namespace MonkeyNotification
{
    public class UserNotificationCenterDelegate : UNUserNotificationCenterDelegate
    {
        ...

        #region Override Methods
        public override void DidReceiveNotificationResponse (UNUserNotificationCenter center, UNNotificationResponse response, Action completionHandler)
        {
            // Take action based on Action ID
            switch (response.ActionIdentifier) {
            case "reply":
                // Do something
                break;
            default:
                // Take action based on identifier
                if (response.IsDefaultAction) {
                    // Handle default action...
                } else if (response.IsDismissAction) {
                    // Handle dismiss action
                }
                break;
            }

            // Inform caller it has been handled
            completionHandler();
        }
        #endregion
    }
}
```

La clase pasada `UNNotificationResponse` tiene una `ActionIdentifier` propiedad que puede ser la acción predeterminada o la acción de descartar. Use `response.Notification.Request.Identifier` para probar cualquier acción personalizada.

La `UserText` propiedad contiene el valor de cualquier entrada de texto de usuario. La `Notification` propiedad contiene la notificación de origen que incluye la solicitud con el contenido del desencadenador y la notificación. La aplicación puede decidir si se trata de una notificación local o remota basada en el tipo de desencadenador.

> [!NOTE]
> iOS 12 permite que una interfaz de usuario de notificación personalizada modifique sus botones de acción en tiempo de ejecución. Para obtener más información, eche un vistazo a la documentación de los [botones de acción de notificación dinámica](~/ios/platform/introduction-to-ios12/notifications/dynamic-actions.md) .

## <a name="working-with-service-extensions"></a>Trabajar con extensiones de servicio

Al trabajar con notificaciones remotas, _las extensiones de servicio_ proporcionan una manera de habilitar el cifrado de un extremo a otro dentro de la carga de notificación. Las extensiones de servicio son una extensión de interfaz que no es de usuario (disponible en iOS 10) que se ejecuta en segundo plano con el propósito principal de aumentar o reemplazar el contenido visible de una notificación antes de que se presente al usuario. 

[![Información general de la extensión de servicio](enhanced-user-notifications-images/extension01.png)](enhanced-user-notifications-images/extension01.png#lightbox)

Las extensiones de servicio están diseñadas para ejecutarse rápidamente y solo se les proporciona una breve cantidad de tiempo para que el sistema las ejecute. En caso de que la extensión de servicio no pueda completar su tarea en la cantidad de tiempo asignada, se llamará a un método de reserva. Si se produce un error en la reserva, se mostrará el contenido de la notificación original al usuario.

Algunos usos posibles de las extensiones de servicio son:

- Proporcionar el cifrado de un extremo a otro del contenido de la notificación remota.
- Agregar datos adjuntos a notificaciones remotas para enriquecerlos.

### <a name="implementing-a-service-extension"></a>Implementar una extensión de servicio

Para implementar una extensión de servicio en una aplicación de Xamarin. iOS, haga lo siguiente:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Abra la solución de la aplicación en Visual Studio para Mac.
2. Haga clic con el botón derecho en el nombre de la solución en el **Panel de solución** y seleccione **Agregar**  >  **Agregar nuevo proyecto**.
3. Seleccione extensiones de **iOS**  >    >  **servicio de notificaciones extensiones** y haga clic en el botón **siguiente** : 

    [![Seleccionar extensiones del servicio de notificaciones](enhanced-user-notifications-images/extension02.png)](enhanced-user-notifications-images/extension02.png#lightbox)
4. Escriba un **nombre** para la extensión y haga clic en el botón **siguiente** : 

    [![Escriba un nombre para la extensión](enhanced-user-notifications-images/extension03.png)](enhanced-user-notifications-images/extension03.png#lightbox)
5. Ajuste el **nombre del proyecto** o el **nombre** de la solución si es necesario y haga clic en el botón **crear** : 

    [![Ajustar el nombre del proyecto o el nombre de la solución](enhanced-user-notifications-images/extension04.png)](enhanced-user-notifications-images/extension04.png#lightbox) 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Abra la solución de la aplicación en Visual Studio.
2. Haga clic con el botón derecho en el nombre de la solución en el **Explorador de soluciones** y seleccione **Agregar > nuevo proyecto..**..
3. Seleccione **Visual C# > extensiones de iOS > extensión del servicio de notificación**:

    [![Seleccionar extensiones del servicio de notificaciones](enhanced-user-notifications-images/extension01.w157-sml.png)](enhanced-user-notifications-images/extension01.w157.png#lightbox)
4. Escriba un **nombre** para la extensión y haga clic en el botón **Aceptar** .

-----

> [!IMPORTANT]
> El identificador de paquete de la extensión de servicio debe coincidir con el identificador de paquete de la aplicación principal con `.appnameserviceextension` anexado al final. Por ejemplo, si la aplicación principal tuviera un identificador de paquete  `com.xamarin.monkeynotify` , la extensión de servicio debe tener un identificador de paquete de `com.xamarin.monkeynotify.monkeynotifyserviceextension` . Se debe establecer automáticamente cuando la extensión se agrega a la solución. 

Hay una clase principal en la extensión del servicio de notificación que tendrá que modificarse para proporcionar la funcionalidad necesaria. Por ejemplo:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;

namespace MonkeyChatServiceExtension
{
    [Register ("NotificationService")]
    public class NotificationService : UNNotificationServiceExtension
    {
        #region Computed Properties
        public Action<UNNotificationContent> ContentHandler { get; set; }
        public UNMutableNotificationContent BestAttemptContent { get; set; }
        #endregion

        #region Constructors
        protected NotificationService (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void DidReceiveNotificationRequest (UNNotificationRequest request, Action<UNNotificationContent> contentHandler)
        {
            ContentHandler = contentHandler;
            BestAttemptContent = (UNMutableNotificationContent)request.Content.MutableCopy ();

            // Modify the notification content here...
            BestAttemptContent.Title = $"{BestAttemptContent.Title}[modified]";

            ContentHandler (BestAttemptContent);
        }

        public override void TimeWillExpire ()
        {
            // Called just before the extension will be terminated by the system.
            // Use this as an opportunity to deliver your "best attempt" at modified content, otherwise the original push payload will be used.

            ContentHandler (BestAttemptContent);
        }
        #endregion
    }
}
```

Al primer método, `DidReceiveNotificationRequest` , se le pasará el identificador de notificación, así como el contenido de la notificación a través del `request` objeto. `contentHandler`Será necesario llamar al método pasado para presentar la notificación al usuario.

Se llamará al segundo método, `TimeWillExpire` , justo antes de que se agote el tiempo de espera para que la extensión de servicio procese la solicitud. Si la extensión de servicio no puede llamar a `contentHandler` en la cantidad de tiempo asignada, se mostrará el contenido original al usuario.

### <a name="triggering-a-service-extension"></a>Desencadenar una extensión de servicio

Con una extensión de servicio creada y entregada con la aplicación, se puede desencadenar mediante la modificación de la carga de notificación remota que se envía al dispositivo. Por ejemplo:

```csharp
{
    aps : {
        alert : "New Message Available",
        mutable-content: 1
    },
    encrypted-content : "#theencryptedcontent"
}
```

La nueva `mutable-content` clave especifica que la extensión de servicio deberá iniciarse para actualizar el contenido de notificación remota. La `encrypted-content` clave contiene los datos cifrados que la extensión de servicio puede descifrar antes de presentarlos al usuario.

Eche un vistazo a la siguiente extensión de servicio de ejemplo:

```csharp
using UserNotification;

namespace myApp {
    public class NotificationService : UNNotificationServiceExtension {
    
        public override void DidReceiveNotificationRequest(UNNotificationRequest request, contentHandler) {
            // Decrypt payload
            var decryptedBody = Decrypt(Request.Content.UserInfo["encrypted-content"]);
            
            // Modify Notification body
            var newContent = new UNMutableNotificationContent();
            newContent.Body = decryptedBody;
            
            // Present to user
            contentHandler(newContent);
        }
        
        public override void TimeWillExpire() {
            // Handle out-of-time fallback event
            ...
        }
        
    }
}
```

Este código descifra el contenido cifrado de la `encrypted-content` clave, crea un nuevo `UNMutableNotificationContent` , establece la `Body` propiedad en el contenido descifrado y usa `contentHandler` para presentar la notificación al usuario.

## <a name="summary"></a>Resumen

En este artículo se han tratado todas las formas en que iOS 10 ha mejorado las notificaciones de los usuarios. Se presentó el nuevo marco de notificaciones de usuario y cómo se usa en una aplicación Xamarin. iOS o una extensión de aplicación.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)
- [Referencia de UserNotifications Framework](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Guía de programación de notificaciones locales y remotas](https://developer.apple.com/documentation/usernotifications)
