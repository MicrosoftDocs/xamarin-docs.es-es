---
title: Notificaciones de usuario avanzadas en Xamarin. iOS
description: En este artículo se profundiza más en el marco de trabajo de notificaciones de usuario, que se presentó en iOS 10. Describe las notificaciones de usuario, la interfaz de notificación del usuario, los datos adjuntos de multimedia, las interfaces de usuario personalizadas y mucho más.
ms.prod: xamarin
ms.assetid: 4E0C60AE-6F54-4098-8FA0-AADF9AC86805
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/03/2018
ms.openlocfilehash: c5a352ce2bdf4df5e19e49c52966c6230af1f4b2
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435318"
---
# <a name="advanced-user-notifications-in-xamarinios"></a>Notificaciones de usuario avanzadas en Xamarin. iOS

Como novedad de iOS 10, el marco de trabajo de notificaciones de usuario permite la entrega y el control de las notificaciones locales y remotas. Con este marco de trabajo, una extensión de aplicación o aplicación puede programar la entrega de notificaciones locales especificando un conjunto de condiciones como la ubicación o la hora del día.

## <a name="about-user-notifications"></a>Acerca de las notificaciones de usuario

El nuevo marco de notificaciones de usuario permite la entrega y el control de las notificaciones locales y remotas. Con este marco de trabajo, una extensión de aplicación o aplicación puede programar la entrega de notificaciones locales especificando un conjunto de condiciones como la ubicación o la hora del día.

Además, la aplicación o la extensión pueden recibir (y potencialmente modificar) las notificaciones locales y remotas, ya que se entregan al dispositivo iOS del usuario.

El marco de trabajo de la interfaz de usuario de notificaciones de usuario nuevo permite personalizar la apariencia de las notificaciones locales y remotas cuando se presentan al usuario.

Este marco de trabajo proporciona las siguientes maneras en que una aplicación puede enviar notificaciones a un usuario:

- **Alertas visuales** : donde la notificación se revierte desde la parte superior de la pantalla como un banner.
- **Sonido y vibraciones** : pueden asociarse con una notificación.
- **Icono de la aplicación distintivos** : donde el icono de la aplicación muestra un distintivo que muestra que el nuevo contenido está disponible. Como el número de mensajes de correo electrónico no leídos.

Además, en función del contexto actual del usuario, hay diferentes maneras en que se presentará una notificación:

- Si el dispositivo está desbloqueado, la notificación se retirará de la parte superior de la pantalla como un banner.
- Si el dispositivo está bloqueado, la notificación se mostrará en la pantalla de bloqueo del usuario.
- Si el usuario ha perdido una notificación, puede abrir el centro de notificaciones y ver cualquier notificación disponible y en espera.

Una aplicación Xamarin. iOS tiene dos tipos de notificaciones de usuario que se pueden enviar:

- **Notificaciones locales** : las envían las aplicaciones instaladas localmente en el dispositivo de los usuarios.
- **Notificaciones remotas** : se envían desde un servidor remoto y se presentan al usuario, o bien se desencadena una actualización en segundo plano del contenido de la aplicación.

Para obtener más información, consulte nuestra documentación de [notificaciones de usuario mejorada](~/ios/platform/user-notifications/enhanced-user-notifications.md) .

## <a name="the-new-user-notification-interface"></a>La nueva interfaz de notificación de usuario

Las notificaciones de usuario en iOS 10 se presentan con un nuevo diseño de IU que proporciona más contenido, como un título, un subtítulo y datos adjuntos de medios opcionales que se pueden presentar en la pantalla de bloqueo, como un banner en la parte superior del dispositivo o en el centro de notificaciones.

Independientemente de la ubicación en la que se muestre una notificación de usuario en iOS 10, se presenta con la misma apariencia y funcionamiento y con las mismas características y funcionalidad.

En iOS 8, Apple presentó notificaciones procesables en las que el desarrollador podría adjuntar acciones personalizadas a una notificación y permitir que el usuario realice una acción en una notificación sin tener que iniciar la aplicación. En iOS 9, las notificaciones accionables mejoradas de Apple con respuesta rápida permiten al usuario responder a una notificación con la entrada de texto.

Dado que las notificaciones de usuario son una parte más integral de la experiencia del usuario en iOS 10, Apple tiene más notificaciones procesables para admitir el toque 3D, donde el usuario presiona una notificación y se muestra una interfaz de usuario personalizada para proporcionar una interacción enriquecida con la notificación.

Cuando se muestra la interfaz de usuario de notificación de usuario personalizada, si el usuario interactúa con las acciones asociadas a la notificación, la interfaz de usuario personalizada se puede actualizar de forma instantánea para proporcionar comentarios sobre lo que se ha cambiado.

Como novedad de iOS 10, la API de IU de notificaciones de usuario permite a una aplicación de Xamarin. iOS aprovechar fácilmente estas nuevas características de la interfaz de usuario de notificaciones de usuario.

## <a name="adding-media-attachments"></a>Agregar datos adjuntos de multimedia

Uno de los elementos más comunes que se comparten entre los usuarios son las fotos, por lo que iOS 10 agregó la capacidad de adjuntar un elemento multimedia (como una fotografía) directamente a una notificación, donde se presentará y estará disponible para el usuario junto con el resto del contenido de la notificación.

Sin embargo, debido a los tamaños implicados en el envío de incluso una imagen pequeña, el hecho de adjuntarlo a una carga de notificación remota resulta poco práctico. Para controlar esta situación, el desarrollador puede usar la nueva extensión de servicio en iOS 10 para descargar la imagen de otro origen (por ejemplo, un almacén de datos de CloudKit) y adjuntarla al contenido de la notificación antes de que se muestre al usuario.

Para que una extensión de servicio pueda modificar una notificación remota, su carga debe estar marcada como mutable. Por ejemplo:

```csharp
{
    aps : {
        alert : "New Photo Available",
        mutable-content: 1
    },
    my-attachment : "https://example.com/photo.jpg"
}
```

Eche un vistazo a la siguiente información general del proceso:

[![Proceso de agregar datos adjuntos de multimedia](advanced-user-notifications-images/extension02.png)](advanced-user-notifications-images/extension02.png#lightbox)

Una vez que la notificación remota se entrega al dispositivo (a través de APNs), la extensión de servicio puede descargar la imagen necesaria a través de los medios deseados (como un `NSURLSession` ) y, después de recibir la imagen, puede modificar el contenido de la notificación y mostrarla al usuario.

A continuación se ofrece un ejemplo de cómo se puede controlar este proceso en el código:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;

namespace MonkeyNotification
{
    public class NotificationService : UNNotificationServiceExtension
    {
        #region Constructors
        public NotificationService (IntPtr handle) : base(handle)
        {
        }
        #endregion

        #region Override Methods
        public override void DidReceiveNotificationRequest (UNNotificationRequest request, Action<UNNotificationContent> contentHandler)
        {
            // Get file URL
            var attachementPath = request.Content.UserInfo.ObjectForKey (new NSString ("my-attachment"));
            var url = new NSUrl (attachementPath.ToString ());

            // Download the file
            var localURL = new NSUrl ("PathToLocalCopy");

            // Create attachment
            var attachmentID = "image";
            var options = new UNNotificationAttachmentOptions ();
            NSError err;
            var attachment = UNNotificationAttachment.FromIdentifier (attachmentID, localURL, options , out err);

            // Modify contents
            var content = request.Content.MutableCopy() as UNMutableNotificationContent;
            content.Attachments = new UNNotificationAttachment [] { attachment };

            // Display notification
            contentHandler (content);
        }

        public override void TimeWillExpire ()
        {
            // Handle service timing out
        }
        #endregion
    }
}
```

Cuando se recibe la notificación de APNs, se lee la dirección personalizada de la imagen desde el contenido y el archivo se descarga del servidor. A continuación, `UNNotificationAttachement` se crea un con un identificador único y la ubicación local de la imagen (como un `NSUrl` ). Se crea una copia modificable del contenido de la notificación y se agregan los datos adjuntos del elemento multimedia. Por último, la notificación se muestra al usuario mediante una llamada a `contentHandler` .

Una vez que se han agregado datos adjuntos a una notificación, el sistema asume el movimiento y la administración del archivo.

Además de las notificaciones remotas presentadas anteriormente, también se admiten datos adjuntos de medios de las notificaciones locales, donde `UNNotificationAttachement` se crea y adjunta a la notificación junto con su contenido.

Las notificaciones en iOS 10 admiten archivos adjuntos de imágenes (estáticas y GIF), audio o vídeo y el sistema mostrará automáticamente la interfaz de usuario personalizada correcta para cada uno de estos tipos de datos adjuntos cuando se presente la notificación al usuario.

> [!NOTE]
> Se debe tener cuidado para optimizar tanto el tamaño del medio como el tiempo necesario para descargar el medio desde el servidor remoto (o para ensamblar los medios para las notificaciones locales), ya que el sistema impone límites estrictos al ejecutar la extensión de servicio de la aplicación. Por ejemplo, considere la posibilidad de enviar una versión reducida de la imagen o un pequeño clip de un vídeo para que se presente en la notificación.

## <a name="creating-custom-user-interfaces"></a>Creación de interfaces de usuario personalizadas

Para crear una interfaz de usuario personalizada para las notificaciones de usuario, el desarrollador debe agregar una extensión de contenido de notificación (novedad en iOS 10) a la solución de la aplicación.

La extensión de contenido de notificación permite al desarrollador agregar sus propias vistas a la interfaz de usuario de notificaciones y extraer el contenido que deseen. A partir de iOS 12, las extensiones de contenido de notificación admiten controles de interfaz de usuario interactiva como botones y controles deslizantes. Para obtener más información, consulte la documentación sobre las [notificaciones interactivas en iOS 12](~/ios/platform/introduction-to-ios12/notifications/interactive.md) .

Para admitir la interacción del usuario con una notificación de usuario, se deben crear acciones personalizadas, registradas en el sistema y adjuntas a la notificación antes de que se programen con el sistema. Se llamará a la extensión de contenido de notificación para controlar el procesamiento de estas acciones. Consulte la sección [trabajar con acciones de notificación](~/ios/platform/user-notifications/enhanced-user-notifications.md) del documento de [notificaciones de usuario mejoradas](~/ios/platform/user-notifications/enhanced-user-notifications.md) para obtener más detalles sobre las acciones personalizadas.

Cuando se presenta al usuario una notificación de usuario con una interfaz de usuario personalizada, tendrá los siguientes elementos:

[![Una notificación de usuario con elementos de interfaz de usuario personalizados](advanced-user-notifications-images/customui01.png)](advanced-user-notifications-images/customui01.png#lightbox)

Si el usuario interactúa con las acciones personalizadas (presentadas debajo de la notificación), la interfaz de usuario se puede actualizar para proporcionar los comentarios del usuario como lo que sucedió cuando invocó una acción determinada.

### <a name="adding-a-notification-content-extension"></a>Agregar una extensión de contenido de notificación

Para implementar una interfaz de usuario de notificación de usuario personalizada en una aplicación de Xamarin. iOS, haga lo siguiente:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Abra la solución de la aplicación en Visual Studio para Mac.
2. Haga clic con el botón derecho en el nombre de la solución en el **Panel de solución** y seleccione **Agregar**  >  **Agregar nuevo proyecto**.
3. Seleccione **iOS**  >  **extensiones**  >  de iOS**extensiones de contenido de notificación** y haga clic en el botón **siguiente** : 

    [![Seleccionar extensiones de contenido de notificación](advanced-user-notifications-images/notify01.png)](advanced-user-notifications-images/notify01.png#lightbox)
4. Escriba un **nombre** para la extensión y haga clic en el botón **siguiente** : 

    [![Escriba un nombre para la extensión](advanced-user-notifications-images/notify02.png)](advanced-user-notifications-images/notify02.png#lightbox)
5. Ajuste el **nombre del proyecto** o el **nombre** de la solución si es necesario y haga clic en el botón **crear** : 

    [![Ajustar el nombre del proyecto o el nombre de la solución](advanced-user-notifications-images/notify03.png)](advanced-user-notifications-images/notify03.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Abra la solución de la aplicación en Visual Studio para Mac.
2. Haga clic con el botón derecho en el nombre de la solución en el **Explorador de soluciones** y seleccione **Agregar > nuevo proyecto..**..
3. Seleccione **Visual C# > extensiones de iOS > extensión de contenido de notificación**:

    [![Seleccionar extensiones de contenido de notificación](advanced-user-notifications-images/notify01.w157-sml.png)](advanced-user-notifications-images/notify01.w157.png#lightbox)
4. Escriba un **nombre** para la extensión y haga clic en el botón **Aceptar** .

-----

Cuando se agrega la extensión de contenido de notificación a la solución, se crearán tres archivos en el proyecto de la extensión:

1. `NotificationViewController.cs` : Este es el controlador de vista principal de la extensión de contenido de notificación.
2. `MainInterface.storyboard` -Donde el desarrollador diseña la interfaz de usuario visible para la extensión de contenido de notificación en el diseñador de iOS.
3. `Info.plist` : Controla la configuración de la extensión de contenido de notificación.

El `NotificationViewController.cs` archivo predeterminado tiene el aspecto siguiente:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;

namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

        }
        #endregion
    }
}
```

`DidReceiveNotification`Se llama al método cuando el usuario expande la notificación para que la extensión de contenido de notificación pueda rellenar la interfaz de usuario personalizada con el contenido de `UNNotification` . En el ejemplo anterior, se ha agregado una etiqueta a la vista, expuesto al código con el nombre `label` y se usa para mostrar el cuerpo de la notificación.

### <a name="setting-the-notification-content-extensions-categories"></a>Establecimiento de las categorías de la extensión de contenido de notificación

Se debe informar al sistema sobre cómo buscar la extensión de contenido de notificación de la aplicación en función de las categorías específicas a las que responde. Siga estos pasos:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Haga doble clic en el archivo de la extensión `Info.plist` en el **Panel de solución** para abrirlo para su edición.
2. Cambie a la vista **Código fuente**.
3. Expanda la `NSExtension` clave.
4. Agregue la `UNNotificationExtensionCategory` clave como **cadena** de tipo con el valor de la categoría a la que pertenece la extensión (en este ejemplo, ' invitación de evento]): 

    [![Agregar la clave UNNotificationExtensionCategory](advanced-user-notifications-images/customui02.png)](advanced-user-notifications-images/customui02.png#lightbox)
5. Guarde los cambios.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Haga doble clic en el archivo de la extensión `Info.plist` en el **Explorador de soluciones** para abrirlo para su edición.
2. Expanda la `NSExtension` clave.
3. Agregue la `UNNotificationExtensionCategory` clave como **cadena** de tipo con el valor de la categoría a la que pertenece la extensión (en este ejemplo, ' invitación de evento]): 

    [![Agregar la clave UNNotificationExtensionCategory](advanced-user-notifications-images/customui02w.png)](advanced-user-notifications-images/customui02w.png#lightbox)
4. Guarde los cambios.

-----

Las categorías de extensión de contenido de notificación ( `UNNotificationExtensionCategory` ) utilizan los mismos valores de categoría que se usan para registrar acciones de notificación. En la situación en la que la aplicación usará la misma interfaz de usuario para varias categorías, cambie `UNNotificationExtensionCategory` a la **matriz** de tipos y proporcione todas las categorías necesarias. Por ejemplo:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Categorías de extensión de contenido de notificación](advanced-user-notifications-images/customui03.png)](advanced-user-notifications-images/customui03.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Categorías de extensión de contenido de notificación](advanced-user-notifications-images/customui03w.png)](advanced-user-notifications-images/customui03w.png#lightbox)

-----

### <a name="hiding-the-default-notification-content"></a>Ocultar el contenido de notificación predeterminado

En caso de que la interfaz de usuario de notificaciones personalizadas muestre el mismo contenido que la notificación predeterminada (título, subtítulo y cuerpo mostrados automáticamente en la parte inferior de la interfaz de usuario de notificaciones), esta información predeterminada se puede ocultar agregando la `UNNotificationExtensionDefaultContentHidden` clave a la `NSExtensionAttributes` clave como tipo **booleano** con un valor de `YES` en el archivo de la extensión `Info.plist` :

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Buscar información predeterminada](advanced-user-notifications-images/customui04.png)](advanced-user-notifications-images/customui04.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Buscar información predeterminada](advanced-user-notifications-images/customui04w.png)](advanced-user-notifications-images/customui04w.png#lightbox)

-----

### <a name="designing-the-custom-ui"></a>Diseñar la interfaz de usuario personalizada

Para diseñar la interfaz de usuario personalizada de la extensión de contenido de notificación, haga doble clic en el `MainInterface.storyboard` archivo para abrirlo y editarlo en el diseñador de iOS, y arrastre los elementos que necesite para compilar la interfaz deseada (como `UILabels` y `UIImageViews` ).

> [!NOTE]
> A partir de iOS 12, una extensión de contenido de notificación puede incluir controles interactivos como botones y campos de texto. Para obtener más información, consulte la documentación sobre las [notificaciones interactivas en iOS 12](~/ios/platform/introduction-to-ios12/notifications/interactive.md) .

Una vez que se ha diseñado la interfaz de usuario y los controles necesarios expuestos al código de C#, Abra `NotificationViewController.cs` para editar y modifique el `DidReceiveNotification` método para rellenar la interfaz de usuario cuando el usuario expande la notificación. Por ejemplo:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;

namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

        }
        #endregion
    }
}
```

### <a name="setting-the-content-area-size"></a>Establecer el tamaño del área de contenido

Para ajustar el tamaño del área de contenido que se muestra al usuario, el código siguiente establece la `PreferredContentSize` propiedad del `ViewDidLoad` método en el tamaño deseado. También se puede ajustar este tamaño aplicando restricciones a la vista en el diseñador de iOS, se deja al desarrollador elegir el método que mejor se adapte a ellos.

Dado que el sistema de notificación ya se está ejecutando antes de que se invoque la extensión de contenido de notificación, el área de contenido se iniciará con un tamaño completo y se animará hasta el tamaño solicitado cuando se presente al usuario.

Para eliminar este efecto, edite el `Info.plist` archivo de la extensión y establezca la `UNNotificationExtensionInitialContentSizeRatio` clave de la `NSExtensionAttributes` clave en el tipo **número** con un valor que represente la relación deseada. Por ejemplo:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![La clave UNNotificationExtensionInitialContentSizeRatio](advanced-user-notifications-images/customui05.png)](advanced-user-notifications-images/customui05.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![La clave UNNotificationExtensionInitialContentSizeRatio](advanced-user-notifications-images/customui05w.png)](advanced-user-notifications-images/customui05w.png#lightbox)

-----

### <a name="using-media-attachments-in-custom-ui"></a>Uso de datos adjuntos multimedia en la interfaz de usuario personalizada

Dado que los datos adjuntos multimedia (como se muestra en la sección anterior [Agregar datos adjuntos de multimedia](#adding-media-attachments) ) forman parte de la carga de notificación, se puede acceder a ellos y mostrarlos en la extensión de contenido de notificación del mismo modo que lo harían en la interfaz de usuario de notificación predeterminada.

Por ejemplo, si la interfaz de usuario personalizada anterior incluía un `UIImageView` elemento que se expuso al código de C#, se podría usar el código siguiente para rellenarlo desde con los datos adjuntos multimedia:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;

namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments [0];
                if (attachment.Url.StartAccessingSecurityScopedResource ()) {
                    EventImage.Image = UIImage.FromFile (attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource ();
                }
            }
        }
        #endregion
    }
}
```

Dado que el sistema administra los datos adjuntos multimedia, está fuera del espacio aislado de la aplicación. La extensión necesita informar al sistema de que desea tener acceso al archivo mediante una llamada al `StartAccessingSecurityScopedResource` método. Cuando la extensión se realiza con el archivo, debe llamar a `StopAccessingSecurityScopedResource` para liberar su conexión.

### <a name="adding-custom-actions-to-a-custom-ui"></a>Agregar acciones personalizadas a una interfaz de usuario personalizada

Los botones de acción personalizada se pueden usar para agregar interactividad a una interfaz de usuario de notificación personalizada. Consulte la sección [trabajar con acciones de notificación](~/ios/platform/user-notifications/enhanced-user-notifications.md) del documento de [notificaciones de usuario mejoradas](~/ios/platform/user-notifications/enhanced-user-notifications.md) para obtener más detalles sobre las acciones personalizadas.

Además de las acciones personalizadas, la extensión de contenido de notificación puede responder también a las siguientes acciones integradas:

- **Acción predeterminada** : es cuando el usuario puntea una notificación para abrir la aplicación y mostrar los detalles de la notificación dada.
- **Acción de descartar** : esta acción se envía a la aplicación cuando el usuario descarta una notificación determinada.

Las extensiones de contenido de notificación también tienen la capacidad de actualizar su interfaz de usuario cuando el usuario invoca una de las acciones personalizadas, como mostrar una fecha como aceptada cuando el usuario pulsa el botón **Aceptar** acción personalizada. Además, las extensiones de contenido de notificación pueden indicar al sistema que retrase el descartado de la interfaz de usuario de notificación para que el usuario pueda ver el efecto de la acción antes de que se cierre la notificación.

Esto se hace implementando una segunda versión del `DidReceiveNotification` método que incluye un controlador de finalización. Por ejemplo:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;
using CoreGraphics;

namespace myApp {
    public class NotificationViewController : UIViewController, UNNotificationContentExtension {

        public override void ViewDidLoad() {
            base.ViewDidLoad();

            // Adjust the size of the content area
            var size = View.Bounds.Size
            PreferredContentSize = new CGSize(size.Width, size.Width/2);
        }

        public void DidReceiveNotification(UNNotification notification) {

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = Content.UserInfo["location"] as string;
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments[0];
                if (attachment.Url.StartAccessingSecurityScopedResource()) {
                    EventImage.Image = UIImage.FromFile(attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource();
                }
            }
        }

        [Export ("didReceiveNotificationResponse:completionHandler:")]
        public void DidReceiveNotification (UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
        {

            // Update UI when the user interacts with the
            // Notification
            Server.PostEventResponse += (response) {
                // Take action based on the response
                switch(response.ActionIdentifier){
                case "accept":
                    EventResponse.Text = "Going!";
                    EventResponse.TextColor = UIColor.Green;
                    break;
                case "decline":
                    EventResponse.Text = "Not Going.";
                    EventResponse.TextColor = UIColor.Red;
                    break;
                }

                // Close Notification
                completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
            };
        }
    }
}
```

Al agregar el `Server.PostEventResponse` controlador al `DidReceiveNotification` método de la extensión de contenido de notificación, la extensión *debe* administrar todas las acciones personalizadas. La extensión también puede reenviar las acciones personalizadas a la aplicación contenedora mediante el cambio de `UNNotificationContentExtensionResponseOption` . Por ejemplo:

```csharp
// Close Notification
completionHandler (UNNotificationContentExtensionResponseOption.DismissAndForwardAction);
```

### <a name="working-with-the-text-input-action-in-custom-ui"></a>Trabajar con la acción de entrada de texto en la interfaz de usuario personalizada

En función del diseño de la aplicación y de la notificación, es posible que haya ocasiones en las que el usuario deba escribir texto en la notificación (por ejemplo, responder a un mensaje). Una extensión de contenido de notificación tiene acceso a la acción de entrada de texto integrada de la misma manera que una notificación estándar.

Por ejemplo:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;

namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Computed Properties
        // Allow to take input
        public override bool CanBecomeFirstResponder {
            get { return true; }
        }

        // Return the custom created text input view with the
        // required buttons and return here
        public override UIView InputAccessoryView {
            get { return InputView; }
        }
        #endregion

        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Private Methods
        private UNNotificationCategory MakeExtensionCategory ()
        {

            // Create Accept Action
            ...

            // Create decline Action
            ...

            // Create Text Input Action
            var commentID = "comment";
            var commentTitle = "Comment";
            var textInputButtonTitle = "Send";
            var textInputPlaceholder = "Enter comment here...";
            var commentAction = UNTextInputNotificationAction.FromIdentifier (commentID, commentTitle, UNNotificationActionOptions.None, textInputButtonTitle, textInputPlaceholder);

            // Create category
            var categoryID = "event-invite";
            var actions = new UNNotificationAction [] { acceptAction, declineAction, commentAction };
            var intentIDs = new string [] { };
            var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.None);

            // Return new category
            return category;

        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments [0];
                if (attachment.Url.StartAccessingSecurityScopedResource ()) {
                    EventImage.Image = UIImage.FromFile (attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource ();
                }
            }
        }

        [Export ("didReceiveNotificationResponse:completionHandler:")]
        public void DidReceiveNotification (UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
        {

            // Is text input?
            if (response is UNTextInputNotificationResponse) {
                var textResponse = response as UNTextInputNotificationResponse;
                Server.Send (textResponse.UserText, () => {
                    // Close Notification
                    completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
                });
            }

            // Update UI when the user interacts with the
            // Notification
            Server.PostEventResponse += (response) {
                // Take action based on the response
                switch (response.ActionIdentifier) {
                case "accept":
                    EventResponse.Text = "Going!";
                    EventResponse.TextColor = UIColor.Green;
                    break;
                case "decline":
                    EventResponse.Text = "Not Going.";
                    EventResponse.TextColor = UIColor.Red;
                    break;
                }

                // Close Notification
                completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
            };
        }
        #endregion
    }
}
```

Este código crea una nueva acción de entrada de texto y la agrega a la categoría de la extensión (en el `MakeExtensionCategory` método). En el `DidReceive` método de invalidación, controla el usuario que escribe texto con el código siguiente:

```csharp
// Is text input?
if (response is UNTextInputNotificationResponse) {
    var textResponse = response as UNTextInputNotificationResponse;
    Server.Send (textResponse.UserText, () => {
        // Close Notification
        completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
    });
}
```

Si el diseño llama a para agregar botones personalizados al campo de entrada de texto, agregue el código siguiente para incluirlos:

```csharp
// Allow to take input
public override bool CanBecomeFirstResponder {
    get {return true;}
}

// Return the custom created text input view with the
// required buttons and return here
public override UIView InputAccessoryView {
    get {return InputView;}
}
```

Cuando el usuario desencadena la acción de comentario, es necesario activar tanto el controlador de vista como el campo de entrada de texto personalizado:

```csharp
// Update UI when the user interacts with the
// Notification
Server.PostEventResponse += (response) {
    // Take action based on the response
    switch(response.ActionIdentifier){
    ...
    case "comment":
        BecomeFirstResponder();
        TextField.BecomeFirstResponder();
        break;
    }

    // Close Notification
    completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);

};
```

## <a name="summary"></a>Resumen

En este artículo se ha realizado una visión avanzada del uso del nuevo marco de notificaciones de usuario en una aplicación de Xamarin. iOS. Se ha tratado la adición de datos adjuntos de medios a la notificación local y remota, que se ha tratado mediante la nueva interfaz de usuario de notificaciones de usuario para crear ius de notificación personalizadas.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)
- [Referencia de UserNotifications Framework](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Guía de programación de notificaciones locales y remotas](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/Introduction.html)