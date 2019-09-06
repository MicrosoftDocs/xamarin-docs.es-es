---
title: Extensiones de aplicación de mensajes avanzadas en Xamarin. iOS
description: En este artículo se muestran técnicas avanzadas para trabajar con extensiones de aplicación de mensaje en una solución de Xamarin. iOS que se integra con la aplicación mensajes y presenta una nueva funcionalidad al usuario.
ms.prod: xamarin
ms.assetid: 394A1FDA-AF70-4493-9B2C-4CFE4BE791B6
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 4e488f43f966ca8efd58de4d2bb8f16997d1322e
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290954"
---
# <a name="advanced-message-app-extensions-in-xamarinios"></a>Extensiones de aplicación de mensajes avanzadas en Xamarin. iOS

_En este artículo se muestran técnicas avanzadas para trabajar con extensiones de aplicación de mensaje en una solución de Xamarin. iOS que se integra con la aplicación mensajes y presenta una nueva funcionalidad al usuario._


Novedad de iOS 10, una extensión de aplicación de mensaje se integra con la aplicación de **mensajes** y presenta una nueva funcionalidad al usuario. La extensión puede enviar texto, adhesivos, archivos multimedia y mensajes interactivos.

## <a name="about-message-app-extensions"></a>Acerca de las extensiones de aplicación de mensajes

Como se indicó anteriormente, una extensión de aplicación de mensaje se integra con la aplicación de **mensajes** y presenta una nueva funcionalidad al usuario. La extensión puede enviar texto, adhesivos, archivos multimedia y mensajes interactivos. Hay disponibles dos tipos de extensiones de aplicación de mensajes:

- **Paquetes de adhesivos** : contiene una colección de adhesivos que el usuario puede Agregar a un mensaje. Los paquetes de adhesivos se pueden crear sin necesidad de escribir ningún código.
- **aplicación IMessage** : puede presentar una interfaz de usuario personalizada dentro de la aplicación mensajes para seleccionar adhesivos, escribir texto, incluidos archivos multimedia (con conversiones de tipo opcionales) y crear, editar y enviar mensajes de interacción.

Las extensiones de aplicaciones de mensajes proporcionan tres tipos de contenido principales:

- **Mensajes interactivos** : son un tipo de contenido de mensaje personalizado que genera una aplicación, cuando el usuario pulsa el mensaje, la aplicación se iniciará en primer plano.
- **Adhesivos** : son imágenes generadas por la aplicación que se pueden incluir en los mensajes enviados entre usuarios. Consulte nuestra aplicación de ejemplo del [generador de helados](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-icecreambuilder) para ver un ejemplo de implementación de una aplicación de adhesivo.
- **Otro contenido admitido** : la aplicación puede proporcionar contenido como fotos, vídeos, texto o vínculos del tipo que siempre ha sido compatible con la aplicación mensajes.

Como novedad de iOS 10, la aplicación de mensaje incluye ahora su propia tienda de aplicaciones integrada y dedicada. Las aplicaciones que incluyen extensiones de aplicaciones de mensajes se mostrarán y promocionarán en este almacén. El nuevo cajón de aplicaciones de mensajes mostrará todas las aplicaciones que se han descargado de la tienda de aplicaciones de mensajes para proporcionar acceso rápido a los usuarios.

También novedad en iOS 10, Apple ha agregado atribución de aplicaciones insertadas que permite al usuario detectar fácilmente una aplicación. Por ejemplo, si un usuario envía contenido a otro desde una aplicación que el segundo usuario no tiene instalado (por ejemplo, un adhesivo, por ejemplo), el nombre de la aplicación de envío aparece en el contenido del historial de mensajes. Si el usuario pulsa el nombre de la aplicación, se abrirá la tienda de aplicaciones de mensajes y se seleccionará la aplicación en la tienda.

Las extensiones de aplicaciones de mensajes son similares a las aplicaciones iOS existentes que el desarrollador está familiarizado con la creación y tendrán acceso a todos los marcos y características estándar de una aplicación estándar de iOS. Por ejemplo:

- Tienen acceso a la compra desde la aplicación.
- Tienen acceso a Apple Pay.
- Tienen acceso al hardware del dispositivo, como la cámara.

Las extensiones de Message apps solo se admiten en iOS 10; sin embargo, el contenido que estas extensiones envían se puede ver en los dispositivos watchos y macOS. La nueva _Página recientes_ agregada a watchos 3 muestra los adhesivos recientes enviados desde el teléfono, incluidos los de las extensiones de aplicaciones de mensajes, y permiten al usuario enviar dichos adhesivos desde el reloj.

## <a name="about-interactive-messages"></a>Acerca de los mensajes interactivos

Los mensajes interactivos presentan una burbuja de mensaje personalizada y se proporcionan mediante una extensión de aplicación de mensaje. Permiten al usuario crear contenido interactivo de mensajes, insertarlo en el campo de entrada del mensaje y enviarlo.

[![](advanced-message-app-extensions-images/interactive01.png "Crear contenido interactivo de mensajes")](advanced-message-app-extensions-images/interactive01.png#lightbox)

El usuario receptor puede responder a un mensaje interactivo punteando su burbuja de mensajes en el historial de mensajes para cargar la extensión de la aplicación de mensajes que la creó. La extensión se iniciará en pantalla completa y permitirá al usuario redactar una respuesta y enviarla de vuelta al usuario de origen.

[![](advanced-message-app-extensions-images/interactive02.png "La extensión se inicia en pantalla completa")](advanced-message-app-extensions-images/interactive02.png#lightbox)


Los temas siguientes se tratarán en detalle a continuación:

- Información general sobre la API messages
- Ciclo de vida de la extensión
- Redactar un mensaje
- Envío de un mensaje

## <a name="messages-api-overview"></a>Información general sobre la API messages

Cuando lo invoca el usuario, se mostrará una extensión de aplicación de mensaje en la parte inferior del historial de mensajes en el modo de vista compacta:

[![](advanced-message-app-extensions-images/interactive03.png "Información general sobre la API messages")](advanced-message-app-extensions-images/interactive03.png#lightbox)

1. El `MSMessageAppViewController` objeto de la extensión de aplicación de mensajes es la clase principal a la que se llama cuando se muestra al usuario la vista de la extensión.
2. La conversación se presenta al usuario como una `MSConversation` instancia de objeto.
3. La `MSMessage` clase representa una burbuja de mensaje determinada en la conversación.
4. `MSSession`controla cómo se envía un mensaje.
5. `MSMessageTemplateLayout`controla cómo se muestra el mensaje

## <a name="the-extension-lifecycle"></a>Ciclo de vida de la extensión

Eche un vistazo al proceso de activar una extensión de aplicación de mensajes:

[![](advanced-message-app-extensions-images/interactive04.png "Proceso de activación de una extensión de aplicación de mensajes")](advanced-message-app-extensions-images/interactive04.png#lightbox)

1. Cuando se inicia una extensión (por ejemplo, desde el cajón de la aplicación), la aplicación de mensaje iniciará un proceso.
2. Se `DidBecomeActive` llama al método y se pasa `MSConversation` un que representa la conversación en la que se ejecuta la extensión de aplicación de mensaje.
3. Dado que la extensión se basa en `UIViewController` `ViewWillAppear` y `ViewDidAppear` se llama a.

A continuación, eche un vistazo al proceso de desactivación de una extensión de aplicación de mensajes:

[![](advanced-message-app-extensions-images/interactive05.png "El proceso de desactivación de una extensión de aplicación de mensajes")](advanced-message-app-extensions-images/interactive05.png#lightbox)

1. Cuando se desactiva la extensión de la aplicación de mensajes, `ViewWillDisappear` se llamará primero al método.
2. A continuación `ViewDidDisappear` , se llamará al método.
3. Se `WillResignActive` llama al método y se pasa `MSConversation` un que representa la conversación en la que se ejecuta la extensión de aplicación de mensaje. En este momento, la conexión entre la aplicación de mensajes y la extensión está a punto de liberarse.
4. En algún momento posterior, la aplicación mensajes finaliza el proceso.

Puesto que una extensión es un proceso de corta duración, el sistema finaliza de forma agresiva para conservar el procesamiento y la energía de la batería. El desarrollador debe tenerlo en cuenta al diseñar e implementar una extensión de aplicación de mensaje.

## <a name="composing-a-message"></a>Redactar un mensaje

Una vez que la extensión de la aplicación de mensajes se está ejecutando en un proceso y muestra su interfaz de usuario, se puede usar el código siguiente para crear un nuevo mensaje:

```csharp
MSMessage ComposeMessage (IceCream iceCream, string caption, MSSession session = null)
{
    var components = new NSUrlComponents {
        QueryItems = iceCream.QueryItems
    };

    var layout = new MSMessageTemplateLayout {
        Image = iceCream.RenderSticker (true),
        Caption = caption
    };

    var message = new MSMessage (session ?? new MSSession()) {
        Url = components.Url,
        Layout = layout
    };

    return message;
}
```

Este código crea un nuevo `MSMessage` y establece varias propiedades ( `Url`como). Aunque el mensaje solo se puede crear en iOS, se puede enviar a iOS y macOS para que se muestren.

Si el usuario hace clic en la burbuja de mensajes de la conversación en macOS, el equipo Mac intentará abrir la dirección especificada en la dirección URL en el explorador Web. Como resultado, el sitio web del desarrollador debe ser capaz de mostrar alguna representación del mensaje en el explorador Web en los equipos basados en macOS.

Los `AccessibilityLabel` lectores de pantalla utilizan la propiedad para leer la transcripción de la conversación al usuario. La `Layout` propiedad especifica cómo se mostrará el mensaje; actualmente `MSMessageTemplateLayout` solo se admite y tiene el aspecto siguiente:

[![](advanced-message-app-extensions-images/interactive06.png "La plantilla MSMessageTemplateLayout")](advanced-message-app-extensions-images/interactive06.png#lightbox)

La `Image` propiedad`MSMessageTemplateLayout` de proporciona contenido para el cuerpo principal de MessageBubble en la pantalla. La `MediaFileUrl` propiedad también proporciona contenido para el cuerpo de la burbuja del mensaje, pero permite el contenido que no es compatible `UIImage` con (por ejemplo, un archivo de vídeo que se recorrerá en segundo plano). Si se proporcionan las `MediaFileUrl` propiedades `Image` y, la propiedad tendrá prioridad. `Image` `MediaFileUrl` Admite PNG, JPEG, GIF y vídeo (en cualquier formato que puedan reproducir los formatos de medios de Media Player Framework).

El tamaño de medio recomendado es 300 x 300 píxeles en la resolución de tres. También se aceptan activos ligeramente más grandes y más pequeños, y Apple sugiere pruebas con unos pocos tamaños para obtener los mejores resultados. La aplicación de mensaje no funcionará: muestre y escale los medios según sea necesario.

Cuando los recursos se envían al receptor, los archivos adjuntos se transcodificarán automáticamente en la aplicación mensajes para optimizar su transferencia a través de las redes. Por este motivo, Apple no recomienda incluir texto en los medios que se adjunten al mensaje, ya que los medios se reducirán y se comprimirán para la transmisión, lo que podría representar el texto ilegible.

Las `ImageTitle` propiedades `ImageSubtitle` y proporcionan una descripción para los medios que se presentan en la burbuja del mensaje. Estas propiedades se enviarán como texto al dispositivo receptor, donde se representarán de forma nítida en la esquina inferior izquierda de la imagen.

Las `Caption`propiedades `SubCaption` ,y`TrailingSubcaption`describen aún más la imagen y se representarán en una sección debajo de la imagen. `TrailingCaption` Al establecer todas estas propiedades en `null` , se creará una burbuja de mensaje sin el área de título:

[![](advanced-message-app-extensions-images/interactive07.png "Una burbuja de mensaje sin el área de título")](advanced-message-app-extensions-images/interactive07.png#lightbox)

Lo último que hay que tener en cuenta es que la aplicación mensajes dibujará el icono de la extensión de la aplicación de mensaje en la esquina superior izquierda de la burbuja del mensaje.

## <a name="sending-a-message"></a>Envío de un mensaje

Una vez `MSMessage` que se ha compuesto un, se puede usar el código siguiente para enviarlo:

```csharp
public void SendMessage (MSMessage message)
{
    // Insert the message into the conversation
    ActiveConversation.InsertMessage (message, (error) => {
        // Did the message send successfully?
        if (error == null) {
            // Handle successful send
        } else {
            // Report Error
            Console.WriteLine ("Error: {0}", error);
        }
    };

}
```

La `ActiveConversation` propiedad`MSMessagesAppViewController` de contendrá la conversación actual en la que se inició la extensión de la aplicación de mensaje.

Llame al `InsertMessage` `MSConversation` de para incluir el mensaje en la conversación y controlar los errores que puedan surgir. Si el mensaje se incluye correctamente, la burbuja del mensaje se mostrará en el campo de entrada.

Además, la extensión puede enviar distintos tipos de datos a la conversación, como:

- **Negrita** - `ActiveConversation.InsertText ("Message", (error) => {...});`
- **Datos adjuntos** - `ActiveConversation.InsertAttachment (new NSUrl ("path"), "filename", (error) => {...});`
- **Adhesivos** donde esun`MSSticker`.`ActiveConversation.InsertSticker (sticker, (obj) => {...});`  -  `sticker`

Una vez que el nuevo contenido está en el campo de entrada, el usuario puede enviar el mensaje punteando en el botón de **envío** azul (del mismo modo que lo haría con cualquier mensaje típico). No hay ninguna manera de que la extensión de aplicación de mensajes envíe contenido automáticamente, ya que este proceso está totalmente bajo el control del usuario.

## <a name="handling-the-compact-and-expanded-modes"></a>Controlar los modos compacto y expandido

Una extensión de aplicación de mensajes se puede mostrar en uno de dos modos de vista diferentes:

[![](advanced-message-app-extensions-images/interactive08.png "Una extensión de aplicación de mensaje mostrada en dos modos de vista diferentes: & Compacto expandido")](advanced-message-app-extensions-images/interactive08.png#lightbox)

- **Compact** : este es el modo predeterminado en el que la extensión de aplicación de mensaje ocupa el 25% inferior de la vista de mensajes. En el modo compacto, la aplicación no tiene acceso al teclado, al desplazamiento horizontal ni a los reconocedores de gestos de deslizamiento. La aplicación tiene acceso al campo de entrada y las llamadas a `InsertMessage` se mostrarán al usuario al instante.
- **Expandido** : la extensión de la aplicación de mensajes llena toda la vista de mensajes. No tiene acceso al campo de entrada, pero tiene acceso al teclado, al desplazamiento horizontal y a los reconocedores de gestos de deslizamiento.

Una extensión de aplicación de mensajes se puede cambiar entre estos modos mediante programación o manualmente por el usuario en cualquier momento y debe responder al instante a cualquier cambio en el modo de vista.

Eche un vistazo al siguiente ejemplo de cómo controlar el cambio entre los dos modos de vista diferentes. Se necesitarán dos controladores de vista diferentes para cada Estado. Controla la vista **compacta** y `AddStickerViewController` controlará la vista **expandida:** `StickerBrowserViewController`

```csharp
using System;

using Messages;
using Foundation;
using UIKit;

namespace MessagesExtension {
    public partial class MessagesViewController : MSMessagesAppViewController, IIceCreamsViewControllerDelegate, IBuildIceCreamViewControllerDelegate {
        public MessagesViewController (IntPtr handle) : base (handle)
        {
        }

        public override void WillBecomeActive (MSConversation conversation)
        {
            base.WillBecomeActive (conversation);

            // Present the view controller appropriate for the conversation and presentation style.
            PresentViewController (conversation, PresentationStyle);
        }

        public override void WillTransition (MSMessagesAppPresentationStyle presentationStyle)
        {
            var conversation = ActiveConversation;
            if (conversation == null)
                throw new Exception ("Expected an active converstation");

            // Present the view controller appropriate for the conversation and presentation style.
            PresentViewController (conversation, presentationStyle);
        }

        void PresentViewController (MSConversation conversation, MSMessagesAppPresentationStyle presentationStyle)
        {
            // Determine the controller to present.
            UIViewController controller;

            if (presentationStyle == MSMessagesAppPresentationStyle.Compact) {
                // Show a list of previously created ice creams.
                controller = InstantiateIceCreamsController ();
            } else {
                var iceCream = new IceCream (conversation.SelectedMessage);
                controller = iceCream.IsComplete ? InstantiateCompletedIceCreamController (iceCream) : InstantiateBuildIceCreamController (iceCream);
            }

            foreach (var child in ChildViewControllers) {
                child.WillMoveToParentViewController (null);
                child.View.RemoveFromSuperview ();
                child.RemoveFromParentViewController ();
            }

            AddChildViewController (controller);
            controller.View.Frame = View.Bounds;
            controller.View.TranslatesAutoresizingMaskIntoConstraints = false;
            View.AddSubview (controller.View);

            controller.View.LeftAnchor.ConstraintEqualTo (View.LeftAnchor).Active = true;
            controller.View.RightAnchor.ConstraintEqualTo (View.RightAnchor).Active = true;
            controller.View.TopAnchor.ConstraintEqualTo (View.TopAnchor).Active = true;
            controller.View.BottomAnchor.ConstraintEqualTo (View.BottomAnchor).Active = true;

            controller.DidMoveToParentViewController (this);
        }

        UIViewController InstantiateIceCreamsController ()
        {
            // Instantiate a `IceCreamsViewController` and present it.
            var controller = Storyboard.InstantiateViewController (IceCreamsViewController.StoryboardIdentifier) as IceCreamsViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an IceCreamsViewController from the storyboard");

            controller.Builder = this;
            return controller;
        }

        UIViewController InstantiateBuildIceCreamController (IceCream iceCream)
        {
            // Instantiate a `BuildIceCreamViewController` and present it.
            var controller = Storyboard.InstantiateViewController (BuildIceCreamViewController.StoryboardIdentifier) as BuildIceCreamViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an BuildIceCreamViewController from the storyboard");

            controller.IceCream = iceCream;
            controller.Builder = this;

            return controller;
        }

        public UIViewController InstantiateCompletedIceCreamController (IceCream iceCream)
        {
            // Instantiate a `BuildIceCreamViewController` and present it.
            var controller = Storyboard.InstantiateViewController (CompletedIceCreamViewController.StoryboardIdentifier) as CompletedIceCreamViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an CompletedIceCreamViewController from the storyboard");

            controller.IceCream = iceCream;
            return controller;
        }

        public void DidSelectAdd (IceCreamsViewController controller)
        {
            Request (MSMessagesAppPresentationStyle.Expanded);
        }

        public void Build (BuildIceCreamViewController controller, IceCreamPart iceCreamPart)
        {
            var conversation = ActiveConversation;
            if (conversation == null)
                throw new Exception ("Expected a conversation");

            var iceCream = controller.IceCream;
            if (iceCream == null)
                throw new Exception ("Expected the controller to be displaying an ice cream");

            string messageCaption = string.Empty;
            var b = iceCreamPart as Base;
            var s = iceCreamPart as Scoops;
            var t = iceCreamPart as Topping;

            if (b != null) {
                iceCream.Base = b;
                messageCaption = "Let's build an ice cream";
            } else if (s != null) {
                iceCream.Scoops = s;
                messageCaption = "I added some scoops";
            } else if (t != null) {
                iceCream.Topping = t;
                messageCaption = "Our finished ice cream";
            } else {
                throw new Exception ("Unexpected type of ice cream part selected.");
            }

            // Create a new message with the same session as any currently selected message.
            var message = ComposeMessage (iceCream, messageCaption, conversation.SelectedMessage?.Session);

            // Add the message to the conversation.
            conversation.InsertMessage (message, null);

            // If the ice cream is complete, save it in the history.
            if (iceCream.IsComplete) {
                var history = IceCreamHistory.Load ();
                history.Append (iceCream);
                history.Save ();
            }

            Dismiss ();
        }

        MSMessage ComposeMessage (IceCream iceCream, string caption, MSSession session = null)
        {
            var components = new NSUrlComponents {
                QueryItems = iceCream.QueryItems
            };

            var layout = new MSMessageTemplateLayout {
                Image = iceCream.RenderSticker (true),
                Caption = caption
            };

            var message = new MSMessage (session ?? new MSSession()) {
                Url = components.Url,
                Layout = layout
            };

            return message;
        }
    }
}
```

El `DidTransition` método se invalida para controlar el cambio entre los dos modos:

```csharp
public override void DidTransition (MSMessagesAppPresentationStyle presentationStyle)
{
    base.DidTransition (presentationStyle);

    // Take action based on style
    switch (presentationStyle) {
    case MSMessagesAppPresentationStyle.Compact:
        PresentStickerBrowser ();
        break;
    case MSMessagesAppPresentationStyle.Expanded:
        PresentAddSticker ();
        break;
    }
}
```

Opcionalmente, la aplicación podría haber usado el `WillTransition` método para controlar el cambio del modo de vista antes de que se presente al usuario (como se hace en el ejemplo anterior de icecream Builder). Para obtener más información, consulte nuestra documentación de [Personalización de adhesivo adicional](~/ios/platform/message-app-integration/intro-to-message-app-extensions.md) .

## <a name="replying-to-a-message"></a>Responder a un mensaje

Hay dos casos en los que una extensión de aplicación de mensaje tendrá que controlar al responder a un mensaje:

[![](advanced-message-app-extensions-images/interactive09.png "La extensión de la aplicación de mensajes en los modos inactivo y activo")](advanced-message-app-extensions-images/interactive09.png#lightbox)

- La **extensión es inactiva** : hay una de las burbujas de mensajes de la extensión de la aplicación de mensaje en la transcripción del mensaje que el usuario puede pulsar para activar las extensiones y continuar la conversación interactiva.
- La **extensión está activa** : el usuario puede pulsar la burbuja de mensajes de la extensión de la aplicación de mensajes en la transcripción del mensaje para entrar en el modo de vista expandida y continuar el proceso interactivo desde donde lo dejó.

### <a name="the-extension-is-inactive"></a>La extensión está inactiva

Cuando el usuario puntea una burbuja de mensaje en la transcripción del mensaje y la extensión de la aplicación de mensaje está inactiva, se produce el siguiente proceso:

[![](advanced-message-app-extensions-images/interactive10.png "Controlar una burbuja de mensajes inactivos")](advanced-message-app-extensions-images/interactive10.png#lightbox)

1. El usuario pulsa la burbuja de mensajes de la extensión.
2. Cuando se inicia una extensión, la aplicación de mensaje iniciará un proceso.
3. Se `DidBecomeActive` llama al método y se pasa `MSConversation` un que representa la conversación en la que se ejecuta la extensión de aplicación de mensaje.
4. Dado que la extensión se basa en `UIViewController` `ViewWillAppear` y `ViewDidAppear` se llama a.

Una vez completado el proceso, la extensión de la aplicación de mensajes se presentará en el modo de vista expandida.

### <a name="the-extension-is-active"></a>La extensión está activa

Cuando el usuario puntea una burbuja de mensaje en la transcripción del mensaje y la extensión de la aplicación de mensaje está activa, se produce el siguiente proceso:

[![](advanced-message-app-extensions-images/interactive11.png "Controlar una burbuja de mensaje activa")](advanced-message-app-extensions-images/interactive11.png#lightbox)

1. El usuario pulsa la burbuja de mensajes de la extensión.
2. Dado que la extensión `WillTransition` `MSMessagesAppViewController` de aplicación de mensaje ya está activa, se llama al método de para controlar el cambio desde el modelo de compactación al modo de vista expandida.
3. Se llama al `DidSelectMessage` método `MSMessage` `MSConversation` de y se pasa el y al que pertenece la burbuja del mensaje. `MSMessagesAppViewController`
4. `MSMessagesAppViewController` `DidTransition` Se llama al método de para controlar el cambio desde el modelo compacto al modo de vista expandido.

De nuevo, cuando se complete el proceso, la extensión de la aplicación de mensaje se presentará en el modo de vista expandida.

### <a name="accessing-the-selected-message"></a>Acceso al mensaje seleccionado

En cualquier caso, cuando el usuario puntea una burbuja de mensaje que pertenece a la extensión de la aplicación de mensaje, deberá obtener acceso `MSMessage` a la que se punteó mediante la `MSConversation` `SelectedMessage` propiedad de.

Por ejemplo:

```csharp
using System;
using Foundation;
using Messages;
using UIKit;


namespace MessageExtension
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        ...

        #region Override Methods
        public override void DidSelectMessage (MSMessage message, MSConversation conversation)
        {
            base.DidSelectMessage (message, conversation);

            // Get selected message
            var selected = conversation.SelectedMessage;

            // Present the user interface to continue editing
            // the conversation
            ...
        }
        #endregion
    }
}
```

El mensaje seleccionado se debe mostrar en la interfaz de usuario de la extensión de la aplicación de mensaje y el usuario debe tener permiso para crear una respuesta.

## <a name="removing-partially-completed-messages"></a>Quitar mensajes parcialmente completados

En el proceso de envío de los distintos pasos de una conversación interactiva entre los dos usuarios de la conversación, las burbujas de mensajes parcialmente completadas pueden empezar a saturar la transcripción del mensaje:

[![](advanced-message-app-extensions-images/interactive12.png "Las burbujas de mensaje completadas parcialmente pueden saturar la transcripción del mensaje")](advanced-message-app-extensions-images/interactive12.png#lightbox)

En su lugar, la extensión de la aplicación de mensaje debe contraer las burbujas de mensajes anteriores en un comentario conciso en la transcripción del mensaje:

[![](advanced-message-app-extensions-images/interactive13.png "Contraer las burbujas de mensajes anteriores en la transcripción del mensaje")](advanced-message-app-extensions-images/interactive13.png#lightbox)

Esto se controla mediante `MSSession` para contraer todos los pasos existentes. Por lo `DidSelectMessage` tanto, el `MSMessagesAppViewController` método de la clase podría modificarse para que tenga un aspecto similar al siguiente:

```csharp
public override void DidSelectMessage (MSMessage message, MSConversation conversation)
{
    base.DidSelectMessage (message, conversation);

    MSSession session;
    var summary = "";

    // Get selected message
    var selected = conversation.SelectedMessage;

    // Does the selected message already have a session?
    if (selected.Session == null) {
        // No, create a new session
        session = new MSSession ();
        summary = "Let's build an ice cream";
    } else {
        // Yes, use the existing session
        session = selected.Session;
        summary = "I added some scoops";
    }

    // Create an instance of the message being composed
    var existingMessage = new MSMessage (session);
    message.SummaryText = summary;
    ...

    // Present the user interface to continue editing
    // the conversation
    ...
}
```

Si el mensaje seleccionado ya tiene una salida `MSSession`, se utiliza otra vez que se crea un nuevo. `MSSession` La `SummaryText` propiedad`MSMessage` de se utiliza para agregar un título a los pasos anteriores contraídos. Si la `SummaryText` propiedad está establecida en `null`, los pasos anteriores de la conversación se quitarán por completo de la transcripción de la conversación.

## <a name="advanced-message-api-features"></a>Características avanzadas de la API de mensajes

Con las características básicas de la nueva API de mensajes que se describen en detalle, a continuación, examine algunas de las características más avanzadas que Apple ha integrado en el marco.

En primer lugar, hay otros métodos de invalidación `MSMessagesAppViewController` en la clase que proporcionan un acceso más profundo a la conversación:

- `DidStartSendingMessage`: Se llama cuando el usuario pulsa el botón Enviar. Esto no significa que el mensaje se haya entregado realmente al destinatario, solo que se ha iniciado el proceso de envío.
- `DidCancelSendingMessage`: Esto ocurre cuando el usuario pulsa el botón *X* de la esquina superior derecha de la burbuja del mensaje en la transcripción de la conversación.
- `DidReceiveMessage`: Se llama a este método cuando la extensión de la aplicación de mensaje está activa, se recibió un nuevo mensaje de uno de los participantes en la conversación.

### <a name="group-conversations"></a>Agrupar conversaciones

Se puede usar una extensión de aplicación de mensajes mientras los usuarios están implicados en conversaciones de grupo (con 3 o más usuarios) y debe tenerse en cuenta al diseñar e implementar una extensión de aplicación de mensaje.

Eche un vistazo a la siguiente interacción en una conversación de grupo con tres usuarios:

[![](advanced-message-app-extensions-images/interactive14.png "Interacción en una conversación de grupo con tres usuarios")](advanced-message-app-extensions-images/interactive14.png#lightbox)

1. El usuario 1 envía un mensaje interactivo de grupo en el que se pide al usuario 2 y al usuario 3 que elija una hamburguesa.
2. El usuario 2 elige el tomates.
3. El usuario 3 elige selectores.
4. Las opciones del usuario 2 y el usuario 3 llegan al usuario 1 casi al mismo tiempo. Como resultado, la elección del usuario 2 se contrae en una línea de Resumen y no está disponible. Este caso también podría haberse volteado, con la opción de usuario 2 visible y contraído el usuario 3.

En cualquier caso, este comportamiento no se desea porque el usuario 1 debe tener acceso a las opciones del usuario 2 y del usuario 3. Para controlar esta situación, Apple está sugiriendo que la extensión de la aplicación de mensajes almacena el estado del mensaje en la nube y usa `MSMessage` la propiedad URL de (que se envía entre los usuarios) para tener acceso a este estado.

Cuando el usuario envía un mensaje, se genera un token de sesión y se inserta en la nube con el estado actual del mensaje. Cuando un usuario pulsa en una burbuja de mensaje en la transcripción de la conversación, el token de sesión se usa para recuperar el estado de sesión actual de la nube.

### <a name="sender-identifiers"></a>Identificadores de remitente

Para analizar el acceso al identificador del remitente de un mensaje, tome el ejemplo de una conversación de grupo indicada anteriormente:

[![](advanced-message-app-extensions-images/interactive15.png "Identificadores de envío de conversación de grupo")](advanced-message-app-extensions-images/interactive15.png#lightbox)

1. De nuevo, el usuario 1 envía un mensaje interactivo de grupo en el que se pide al usuario 2 y al usuario 3 que elija una hamburguesa.
2. El usuario 3 elige selectores.
3. La elección del usuario 3 llega al usuario 1 y el usuario 2 todavía no ha respondido.
4. Dado que Apple está muy preocupado por la privacidad del usuario, la extensión de la aplicación de mensaje solo conoce un identificador `NSUUID`único (como un) que se asigna a cada participante en la conversación. En el dispositivo local, solo se conoce el identificador del usuario actual.
5. `MSMessage` Tiene una`SenderIdentifier` propiedad que coincide con uno de los del usuario de la lista de participantes conocida por la extensión.
6. Cada dispositivo de usuario tiene su propia copia de la lista de participantes, en la que solo se conoce el identificador del usuario local.
7. Cuando se envía un mensaje, también `SenderIdentifier` se sabe que su propiedad es la del usuario local.

Los identificadores de remitente se pueden usar de las siguientes maneras:

- Al examinar la lista de participantes, la extensión puede obtener el número de usuarios de la conversación.
- Cuando la extensión recibe un mensaje de un usuario, puede realizar un seguimiento del identificador del remitente. Si recibe otro mensaje con el mismo identificador de remitente, la extensión sabe que es del mismo usuario.
- Se pueden utilizar para ayudar a identificar un usuario específico en la conversación.

El identificador del remitente se puede usar en cualquiera de los campos de texto de `MSMessageTemplateLayout` mediante el prefijo de un signo de dólar (`$`). Por ejemplo:

```csharp
// Pass along the sender identifier
var layout = new MSMessageTemplateLayout()
layout.Caption = string.format("${0} wants pickles.",Conversation.LocalParticipantIdentifier.UuidString);
```

Cuando la aplicación mensajes muestra una burbuja de mensaje con este tipo de formato, reemplazará el `$uuid...` por el nombre de contacto del participante en la conversación que envió el mensaje.

El identificador de remitente es único en cada dispositivo, por lo que debe examinar el diagrama anterior de nuevo. tenga en cuenta que el dispositivo del usuario 1 y el del usuario 3 tienen un identificador de remitente único para cada participante de la conversación.

El ámbito de los identificadores de remitente es la instalación de la extensión de aplicación de mensaje. Por lo tanto, si un usuario desinstala y vuelve a instalar la extensión de aplicación de mensaje, se generarán nuevos identificadores de remitente para la nueva instalación.

Para tener acceso a los identificadores de remitente, la extensión puede usar el código siguiente:

```csharp
public override void DidStartSendingMessage (MSMessage message, MSConversation conversation)
{
    base.DidStartSendingMessage (message, conversation);

    // Get the sender's participant ID
    var senderID = message.SenderParticipantIdentifier;

    // Get the local participant ID
    var localID = conversation.LocalParticipantIdentifier;

    // Get the remote participant IDs
    var remoteIDs = conversation.RemoteParticipantIdentifiers;
}
```

## <a name="supported-platforms"></a>Plataformas compatibles

Los mensajes interactivos generados por una extensión de aplicación de mensajes se entregarán en las siguientes plataformas de Apple:

- watchos 3
- macOS Sierra
- iOS 10

De las tres plataformas, solo iOS 10 permitirá al usuario generar un mensaje interactivo. En MacOS Sierra, si el usuario hace clic en una burbuja de mensajes interactivos, la dirección `MSMessage` URL asociada a se abrirá en Safari y se mostrará una representación del mensaje.

En watchos, la aplicación mensajes puede entregar un mensaje interactivo a un dispositivo iOS conectado en el que el usuario puede crear una respuesta.

La nueva API de mensajes es compatible con la reserva si se recibe el mensaje interactivo en plataformas de Apple anteriores:

- watchos 2 +
- OS X 10,11 +
- iOS 9 +

Se entregarán en un formato de reserva como dos mensajes independientes:

- Una será la imagen proporcionada por el diseño de la plantilla.
- El otro será la dirección URL que se proporciona en `MSMessage`.

## <a name="summary"></a>Resumen

En este artículo se han presentado técnicas avanzadas para trabajar con extensiones de aplicación de mensaje en una solución de Xamarin. iOS que se integra con la aplicación de **mensajes** y presenta una nueva funcionalidad al usuario.


## <a name="related-links"></a>Vínculos relacionados

- [Creador de helados (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-icecreambuilder)
- [Referencia de mensajes](https://developer.apple.com/reference/messages)
- [Guía de programación de extensiones de aplicaciones](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
