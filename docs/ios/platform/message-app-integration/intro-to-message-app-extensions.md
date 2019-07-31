---
title: Conceptos básicos de la extensión de aplicaciones de mensajes en Xamarin. iOS
description: En este artículo se muestra cómo incluir una extensión de aplicación de mensaje en una solución de Xamarin. iOS que se integra con la aplicación de mensajes y presenta una nueva funcionalidad al usuario.
ms.prod: xamarin
ms.assetid: 0CFB494C-376C-449D-B714-9E82644F9DA3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: 7004c4692158be2e51eca93dece349da2a735bab
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68654390"
---
# <a name="message-app-extension-basics-in-xamarinios"></a>Conceptos básicos de la extensión de aplicaciones de mensajes en Xamarin. iOS

_En este artículo se muestra cómo incluir una extensión de aplicación de mensaje en una solución de Xamarin. iOS que se integra con la aplicación de mensajes y presenta una nueva funcionalidad al usuario._

Novedad de iOS 10, una extensión de aplicación de mensaje se integra con la aplicación de **mensajes** y presenta una nueva funcionalidad al usuario. La extensión puede enviar texto, adhesivos, archivos multimedia y mensajes interactivos.

## <a name="about-message-app-extensions"></a>Acerca de las extensiones de aplicación de mensajes

Como se indicó anteriormente, una extensión de aplicación de mensaje se integra con la aplicación de **mensajes** y presenta una nueva funcionalidad al usuario. La extensión puede enviar texto, adhesivos, archivos multimedia y mensajes interactivos. Hay disponibles dos tipos de extensiones de aplicación de mensajes:

- **Paquetes** de adhesivos: contiene una colección de adhesivos que el usuario puede Agregar a un mensaje. Los paquetes de adhesivos se pueden crear sin necesidad de escribir ningún código.
- **aplicación IMessage** : puede presentar una interfaz de usuario personalizada dentro de la aplicación mensajes para seleccionar adhesivos, escribir texto, incluidos archivos multimedia (con conversiones de tipo opcionales) y crear, editar y enviar mensajes de interacción.

Las extensiones de aplicaciones de mensajes proporcionan tres tipos de contenido principales:

- **Mensajes** interactivos: son un tipo de contenido de mensaje personalizado que genera una aplicación, cuando el usuario pulsa el mensaje, la aplicación se iniciará en primer plano.
- **Adhesivos** : son imágenes generadas por la aplicación que se pueden incluir en los mensajes enviados entre usuarios.
- **Otro contenido admitido** : la aplicación puede proporcionar contenido, como fotos, vídeos, texto o vínculos a cualquier otro tipo de contenido que siempre se admita en la aplicación mensajes.

Como novedad de iOS 10, la aplicación de mensaje incluye ahora su propia tienda de aplicaciones integrada y dedicada. Las aplicaciones que incluyen extensiones de aplicaciones de mensajes se mostrarán y promocionarán en este almacén. El nuevo cajón de aplicaciones de mensajes mostrará todas las aplicaciones que se han descargado de la tienda de aplicaciones de mensajes para proporcionar acceso rápido a los usuarios.

También novedad en iOS 10, Apple ha agregado atribución de aplicaciones insertadas que permite al usuario detectar fácilmente una aplicación. Por ejemplo, si un usuario envía contenido a otro desde una aplicación que el segundo usuario no tiene instalado (por ejemplo, un adhesivo, por ejemplo), el nombre de la aplicación de envío aparece en el contenido del historial de mensajes. Si el usuario pulsa el nombre de la aplicación, se abrirá la tienda de aplicaciones de mensajes y se seleccionará la aplicación en la tienda.

Las extensiones de aplicaciones de mensajes son similares a las aplicaciones iOS existentes que el desarrollador está familiarizado con la creación y tendrán acceso a todos los marcos y características estándar de una aplicación estándar de iOS. Por ejemplo:

- Tienen acceso a la compra desde la aplicación.
- Tienen acceso a Apple Pay.
- Tienen acceso al hardware del dispositivo, como la cámara.

Las extensiones de Message apps solo se admiten en iOS 10; sin embargo, el contenido que estas extensiones envían se puede ver en los dispositivos watchos y macOS. La nueva _Página recientes_ agregada a watchos 3 muestra los adhesivos recientes enviados desde el teléfono, incluidos los de las extensiones de aplicaciones de mensajes, y permiten al usuario enviar dichos adhesivos desde el reloj.

## <a name="about-the-messages-framework"></a>Acerca del marco de mensajes

Como novedad de iOS 10, el marco de mensajes proporciona la interfaz entre la extensión de aplicaciones de mensajes y la aplicación de mensajes en el dispositivo iOS del usuario. Cuando el usuario inicia una aplicación desde dentro de la aplicación mensajes, este marco permite detectar la aplicación y proporciona los datos y el contexto necesarios para diseñar su interfaz de usuario.

Una vez que se inicia la aplicación, el usuario interactúa con ella para crear contenido nuevo y compartirlo a través de un mensaje. A continuación, la aplicación usa el marco de mensajes para transferir el contenido recién creado a la aplicación mensajes para su procesamiento.

El marco de mensajes y las extensiones de aplicaciones de mensajes se basan en las tecnologías de extensiones de aplicación de iOS preexistentes. Para obtener más información sobre las extensiones de la aplicación, consulte la guía de programación de la [extensión de aplicaciones](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)de Apple.

A diferencia de otros puntos de extensión que Apple ha proporcionado en todo el sistema, el desarrollador no necesita proporcionar una aplicación host para las extensiones de aplicaciones de mensajes, ya que la propia aplicación de mensaje actúa como contenedor. Sin embargo, el desarrollador tiene la opción de incluir la extensión de aplicaciones de mensajes dentro de una aplicación iOS nueva o existente y enviarla junto con la agrupación.

Si las extensiones de las aplicaciones de mensajes se incluyen en el paquete de una aplicación iOS, el icono de la aplicación se mostrará tanto en la pantalla principal del dispositivo como en el cajón de la aplicación de mensaje desde la aplicación mensajes. Si no se incluye en un lote de aplicaciones, la extensión de aplicaciones de mensaje solo se mostrará en el cajón de la aplicación de mensaje.

Aunque las extensiones de aplicaciones de mensajes no se incluyan en un paquete de aplicaciones de host, el desarrollador deberá proporcionar un icono de aplicación en el paquete de la extensión de aplicaciones de mensajes, ya que este es el icono que se mostrará en otras partes del sistema, como el cajón o la configuración de la aplicación de mensaje. , para la extensión.

## <a name="about-stickers"></a>Acerca de los adhesivos

Apple diseñó adhesivos como una nueva forma para que los usuarios de iMessage se comuniquen permitiendo que los adhesivos se envíen en línea como cualquier otro contenido de mensaje o se adjunten a burbujas de mensajes anteriores dentro de la conversación.

¿Qué son los adhesivos?

- Son imágenes que proporciona la extensión de aplicaciones de mensajes.
- Pueden ser imágenes animadas o estáticas.
- Proporcionan una nueva forma de compartir el contenido de la imagen desde dentro de una aplicación.

Hay dos maneras de crear adhesivos:

1. Se pueden crear extensiones de aplicaciones de mensajes de paquetes de adhesivos desde dentro de Xcode sin incluir ningún código. Lo único que se necesita son los recursos para los iconos de adhesivos y aplicaciones.
2. Mediante la creación de una extensión de aplicaciones de mensajes estándar que proporciona adhesivos a partir del código a través del marco de mensajes.

### <a name="creating-sticker-packs"></a>Crear paquetes de adhesivos

Los paquetes de adhesivos se crean a partir de una plantilla especial dentro de Xcode y simplemente proporcionan un conjunto estático de recursos de imagen que se pueden usar como adhesivos. Tal y como se indicó anteriormente, no requieren ningún código, el desarrollador simplemente arrastra los archivos de imagen a la carpeta del paquete de adhesivos dentro del catálogo de activos de adhesivos.

Para que una imagen se incluya en un paquete de adhesivos, debe cumplir los siguientes requisitos:

- Las imágenes deben estar en formato PNG, APNG, GIF o JPEG. Apple sugiere el uso de los formatos PNG y APNG al proporcionar recursos adhesivos.
- Los adhesivos animados solo admiten los formatos APNG y GIF.
- Las imágenes de adhesivos deben proporcionar un fondo transparente, ya que pueden colocarse sobre burbujas de mensajes en la conversación por parte del usuario.
- Los archivos de imagen individuales deben ser menores que máximo.
- Las imágenes no pueden ser menores que los 100x100 puntos o mayores que 206 x 206 puntos.

> [!IMPORTANT]
> Siempre se deben proporcionar `@3x` las imágenes de adhesivos en el intervalo de 300 x 300 a 618 x 618 píxeles. El sistema generará automáticamente las `@2x` versiones `@1x` y en tiempo de ejecución según sea necesario.

Apple sugiere la prueba de los recursos de la imagen con adhesivos en diferentes fondos de color (como blanco, negro, rojo, amarillo y multicolor) y en fotos, para asegurarse de que tienen el mejor aspecto en todas las situaciones posibles.

Los paquetes de adhesivos pueden proporcionar adhesivos en uno de los tres tamaños disponibles:

- **Pequeño** : 100 x 100 puntos.
- 136 x 136 puntos. Este es el tamaño predeterminado.
- Puntos 206 x 206 de **gran tamaño** .

Usar el inspector de atributos de Xcode para establecer el tamaño de todo el paquete de adhesivos y proporcionar solo recursos de imagen que coincidan con el tamaño solicitado, para obtener los mejores resultados en el explorador de adhesivos dentro de la aplicación mensajes.

Para obtener más información, consulte la aplicación del [generador](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-icecreambuilder) de helados y la [referencia de mensajes](https://developer.apple.com/reference/messages)de Apple.

## <a name="creating-a-custom-sticker-experience"></a>Creación de una experiencia de adhesivo personalizada

Si la aplicación requiere más control o flexibilidad que el que proporciona un paquete de adhesivos, puede incluir una extensión de aplicación de mensajes y proporcionar los adhesivos a través del marco de mensajes para obtener una experiencia de adhesivo personalizada.

¿Cuáles son las ventajas de crear una experiencia de adhesivo personalizada?

1. Permite a la aplicación personalizar el modo en que se muestran los adhesivos a los usuarios de la aplicación. Por ejemplo, para presentar adhesivos en un formato distinto del diseño de cuadrícula estándar o en un fondo de color diferente.
2. Permite que los adhesivos se creen dinámicamente desde el código en lugar de incluirse como recursos de imagen estáticos.
3. Permite descargar dinámicamente los recursos de las imágenes de adhesivos del servidor Web del desarrollador sin tener que liberar una nueva versión en la tienda de aplicaciones.
4. Permite el acceso de la cámara del dispositivo para crear adhesivos sobre la marcha.
5. Permite compras desde la aplicación para que el usuario pueda comprar más adhesivos desde dentro de la aplicación.

Para crear una experiencia de adhesivo personalizada, haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Inicie Visual Studio para Mac.
2. Abra la solución para agregar una extensión de aplicación de mensajes a. 
3. Seleccione la**extensión IMessage** **extensions** > de **iOS** > y haga clic en el botón **siguiente** : 

    [![](intro-to-message-app-extensions-images/message01.png "Selección de la extensión iMessage")](intro-to-message-app-extensions-images/message01.png#lightbox)
4. Escriba un **nombre de extensión** y haga clic en el botón **siguiente** : 

    [![](intro-to-message-app-extensions-images/message02.png "Escriba un nombre de extensión")](intro-to-message-app-extensions-images/message02.png#lightbox)
5. Haga clic en el botón **crear** para compilar la extensión: 

    [![](intro-to-message-app-extensions-images/message03.png "Haga clic en el botón crear")](intro-to-message-app-extensions-images/message03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Inicie Visual Studio.
2. Abra la solución para agregar una extensión de aplicación de mensaje.
3. Seleccione **extensiones de ios > extensión IMessage (iOS)** y haga clic en el botón **siguiente** :

    [![Selección de la extensión iMessage (iOS)](intro-to-message-app-extensions-images/message01.w157-sml.png)](intro-to-message-app-extensions-images/message01.w157.png#lightbox)

4. Escriba un **nombre** y haga clic en el botón **Aceptar**

-----

De forma predeterminada, `MessagesViewController.cs` el archivo se agregará a la solución. Este es el punto de entrada principal de la extensión y hereda de la `MSMessageAppViewController` clase.

El marco de mensajes proporciona clases para presentar los adhesivos disponibles al usuario:

- `MSStickerBrowserViewController`: Controla la vista en la que se presentarán los adhesivos. También se ajusta a la `IMSStickerBrowserViewDataSource` interfaz para devolver el recuento de adhesivos y la etiqueta de un índice de explorador determinado.
- `MSStickerBrowserView`: Esta es la vista en la que se mostrarán los adhesivos disponibles.
- `MSStickerSize`: Decide los tamaños de celda individuales de la cuadrícula de adhesivos presentados en la vista de explorador.

### <a name="creating-a-custom-sticker-browser"></a>Crear un explorador de adhesivos personalizado

El desarrollador puede personalizar aún más la experiencia de adhesivo para el usuario proporcionando un explorador de adhesivo`MSMessageAppBrowserViewController`personalizado () en la extensión de la aplicación de mensajes. El explorador de adhesivos personalizado cambia el modo en que se presentan los adhesivos al usuario cuando seleccionan un adhesivo para incluirlos en el flujo de mensajes.

Haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. En el **Panel de solución**, haga clic con el botón derecho en el nombre del proyecto de la extensión y seleccione **Agregar** > **nuevo archivo..** .  >  **iOS |**  > **Controlador de interfaz**Apple Watch.
2. Escriba `StickerBrowserViewController` como **nombre** y haga clic en el botón **nuevo** : 

    [![](intro-to-message-app-extensions-images/browser01.png "Escriba StickerBrowserViewController para el nombre")](intro-to-message-app-extensions-images/browser01.png#lightbox)
3. Abra el `StickerBrowserViewController.cs` archivo para editarlo.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el nombre del proyecto de la extensión y seleccione **Agregar** > **nuevo archivo..** .  >  **iOS |**  > **Controlador de interfaz**Apple Watch.
2. Escriba `StickerBrowserViewController` como **nombre** y haga clic en el botón **nuevo** : 

    [![](intro-to-message-app-extensions-images/browser01.w157-sml.png "Escriba StickerBrowserViewController para el nombre")](intro-to-message-app-extensions-images/browser01.w157.png#lightbox)
3. Abra el `StickerBrowserViewController.cs` archivo para editarlo.

-----

Haga que `StickerBrowserViewController.cs` tenga el aspecto siguiente:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Messages;
using Foundation;

namespace MonkeyStickers
{
    public partial class StickerBrowserViewController : MSStickerBrowserViewController
    {
        #region Computed Properties
        public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
        #endregion

        #region Constructors
        public StickerBrowserViewController (MSStickerSize stickerSize) : base (stickerSize)
        {
        }
        #endregion

        #region Private Methods
        private void CreateSticker (string assetName, string localizedDescription)
        {

            // Get path to asset
            var path = NSBundle.MainBundle.PathForResource (assetName, "png");
            if (path == null) {
                Console.WriteLine ("Couldn't create sticker {0}.", assetName);
                return;
            }

            // Build new sticker
            var stickerURL = new NSUrl (path);
            NSError error = null;
            var sticker = new MSSticker (stickerURL, localizedDescription, out error);
            if (error == null) {
                // Add to collection
                Stickers.Add (sticker);
            } else {
                // Report error
                Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
            }
        }

        private void LoadStickers ()
        {

            // Load sticker assets from disk
            CreateSticker ("canada", "Canada Sticker");
            CreateSticker ("clouds", "Clouds Sticker");
            ...
            CreateSticker ("tree", "Tree Sticker");
        }
        #endregion

        #region Public Methods
        public void ChangeBackgroundColor (UIColor color)
        {
            StickerBrowserView.BackgroundColor = color;

        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            LoadStickers ();
        }

        public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
        {
            return Stickers.Count;
        }

        public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
        {
            return Stickers[(int)index];
        }
        #endregion
    }
}
```

Eche un vistazo al código anterior en detalle. Crea almacenamiento para los adhesivos que proporciona la extensión:

```csharp
public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
```

Y reemplaza dos métodos de la `MSStickerBrowserViewController` clase para proporcionar datos para el explorador desde este almacén de datos:

```csharp
public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
{
    return Stickers.Count;
}

public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
{
    return Stickers[(int)index];
}
```

El `CreateSticker` método obtiene la ruta de acceso de un recurso de imagen del paquete de la extensión y lo usa para crear una nueva `MSSticker` instancia de a partir de este recurso, que agrega a la colección:

```csharp
private void CreateSticker (string assetName, string localizedDescription)
{

    // Get path to asset
    var path = NSBundle.MainBundle.PathForResource (assetName, "png");
    if (path == null) {
        Console.WriteLine ("Couldn't create sticker {0}.", assetName);
        return;
    }

    // Build new sticker
    var stickerURL = new NSUrl (path);
    NSError error = null;
    var sticker = new MSSticker (stickerURL, localizedDescription, out error);
    if (error == null) {
        // Add to collection
        Stickers.Add (sticker);
    } else {
        // Report error
        Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
    }
}
```

Se `LoadSticker` llama al método desde `ViewDidLoad` para crear un adhesivo a partir del recurso de imagen con nombre (incluido en el paquete de la aplicación) y agregarlo a la colección de adhesivos.

Para implementar el explorador de adhesivos personalizado, `MessagesViewController.cs` edite el archivo y haga que tenga un aspecto similar al siguiente:

```csharp
using System;
using UIKit;
using Messages;

namespace MonkeyStickers
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        #region Computed Properties
        public StickerBrowserViewController BrowserViewController { get; set;}
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();


            // Create new browser and configure it
            BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
            BrowserViewController.View.Frame = View.Frame;
            BrowserViewController.ChangeBackgroundColor (UIColor.Gray);

            // Add to view
            AddChildViewController (BrowserViewController);
            BrowserViewController.DidMoveToParentViewController (this);
            View.AddSubview (BrowserViewController.View);
        }
        #endregion
    }
}
```

Al echar un vistazo a este código en detalle, se crea almacenamiento para el explorador personalizado:

```csharp
public StickerBrowserViewController BrowserViewController { get; set;}
```

Y en el `ViewDidLoad` método, crea una instancia de y configura un nuevo explorador:

```csharp
// Create new browser and configure it
BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
BrowserViewController.View.Frame = View.Frame;
BrowserViewController.ChangeBackgroundColor (UIColor.Gray);
```

A continuación, agrega el explorador a la vista para mostrarlo:

```csharp
// Add to view
AddChildViewController (BrowserViewController);
BrowserViewController.DidMoveToParentViewController (this);
View.AddSubview (BrowserViewController.View);
```

### <a name="further-sticker-customization"></a>Personalización de adhesivo adicional

Es posible la personalización de adhesivos adicional al incluir solo dos clases en la extensión de la aplicación de mensajes:

- `MSStickerView`
- `MSSticker`

Con los métodos anteriores, la extensión puede admitir la selección de adhesivo que no se basa en el método de explorador adhesivo estándar. Además, se puede cambiar la presentación de adhesivos entre dos modos de vista diferentes:

- **Compact** : este es el modo predeterminado en el que la vista de adhesivo ocupa el 25% inferior de la vista de mensajes.
- **Expandido** : la vista de adhesivo llena toda la vista de mensajes.

Esta vista de adhesivo se puede cambiar entre estos modos mediante programación o manualmente por el usuario.

Eche un vistazo al siguiente ejemplo de cómo controlar el cambio entre los dos modos de vista diferentes. Se necesitarán dos controladores de vista diferentes para cada Estado. Controla la vista compacta y tiene un aspecto similar al siguiente: `StickerBrowserViewController`

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Messages;
using Foundation;

namespace MessageExtension
{
    public partial class StickerBrowserViewController : MSStickerBrowserViewController
    {
        #region Computed Properties
        public MessagesViewController MessagesAppViewController { get; set; }
        public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
        #endregion

        #region Constructors
        public StickerBrowserViewController (MessagesViewController messagesAppViewController, MSStickerSize stickerSize) : base (stickerSize)
        {
            // Initialize
            this.MessagesAppViewController = messagesAppViewController;
        }
        #endregion

        #region Private Methods
        private void CreateSticker (string assetName, string localizedDescription)
        {

            // Get path to asset
            var path = NSBundle.MainBundle.PathForResource (assetName, "png");
            if (path == null) {
                Console.WriteLine ("Couldn't create sticker {0}.", assetName);
                return;
            }

            // Build new sticker
            var stickerURL = new NSUrl (path);
            NSError error = null;
            var sticker = new MSSticker (stickerURL, localizedDescription, out error);
            if (error == null) {
                // Add to collection
                Stickers.Add (sticker);
            } else {
                // Report error
                Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
            }
        }

        private void LoadStickers ()
        {

            // Load sticker assets from disk
            CreateSticker ("add", "Add New Sticker");
            CreateSticker ("canada", "Canada Sticker");
            CreateSticker ("clouds", "Clouds Sticker");
            CreateSticker ("tree", "Tree Sticker");
        }
        #endregion

        #region Public Methods
        public void ChangeBackgroundColor (UIColor color)
        {
            StickerBrowserView.BackgroundColor = color;

        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            LoadStickers ();

        }

        public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
        {
            return Stickers.Count;
        }

        public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
        {
            // Wanting to add a new sticker?
            if (index == 0) {
                // Yes, ask controller to present add sticker interface
                MessagesAppViewController.AddNewSticker ();
                return null;
            } else {
                // No, return existing sticker
                return Stickers [(int)index];
            }
        }
        #endregion
    }
}
```

Controlará la vista de adhesivo expandida y tendrá un aspecto similar al siguiente: `AddStickerViewController`

```csharp
using System;
using Foundation;
using UIKit;
using Messages;

namespace MessageExtension
{
    public class AddStickerViewController : UIViewController
    {
        #region Computed Properties
        public MessagesViewController MessagesAppViewController { get; set;}
        public MSSticker NewSticker { get; set;}
        #endregion

        #region Constructors
        public AddStickerViewController (MessagesViewController messagesAppViewController)
        {
            // Initialize
            this.MessagesAppViewController = messagesAppViewController;
        }
        #endregion

        #region Override Method
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Build interface to create new sticker
            var cancelButton = new UIButton (UIButtonType.RoundedRect);
            cancelButton.TouchDown += (sender, e) => {
                // Cancel add new sticker
                MessagesAppViewController.CancelAddNewSticker ();
            };
            View.AddSubview (cancelButton);

            var doneButton = new UIButton (UIButtonType.RoundedRect);
            doneButton.TouchDown += (sender, e) => {
                // Add new sticker to collection
                MessagesAppViewController.AddStickerToCollection (NewSticker);
            };
            View.AddSubview (doneButton);
            
            ...
        }
        #endregion
    }
}
```

`MessageViewController` Implementa estos controladores de vista para impulsar el estado solicitado:

```csharp
using System;
using UIKit;
using Messages;

namespace MessageExtension
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        #region Computed Properties
        public bool IsAddingSticker { get; set;}
        public StickerBrowserViewController BrowserViewController { get; set; }
        public AddStickerViewController AddStickerController { get; set;}
        #endregion

        #region Constructors
        protected MessagesViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Public Methods
        public void PresentStickerBrowser ()
        {
            // Is the Add sticker view being displayed?
            if (IsAddingSticker) {
                // Yes, remove it from view
                AddStickerController.RemoveFromParentViewController ();
                AddStickerController.View.RemoveFromSuperview ();
            }

            // Add to view
            AddChildViewController (BrowserViewController);
            BrowserViewController.DidMoveToParentViewController (this);
            View.AddSubview (BrowserViewController.View);

            // Save mode
            IsAddingSticker = false;
        }

        public void PresentAddSticker ()
        {
            // Is the sticker browser being displayed?
            if (!IsAddingSticker) {
                // Yes, remove it from view
                BrowserViewController.RemoveFromParentViewController ();
                BrowserViewController.View.RemoveFromSuperview ();
            }

            // Add to view
            AddChildViewController (AddStickerController);
            AddStickerController.DidMoveToParentViewController (this);
            View.AddSubview (AddStickerController.View);

            // Save mode
            IsAddingSticker = true;
        }

        public void AddNewSticker ()
        {
            // Switch to expanded view mode
            Request (MSMessagesAppPresentationStyle.Expanded);
        }

        public void CancelAddNewSticker ()
        {
            // Switch to compact view mode
            Request (MSMessagesAppPresentationStyle.Compact);
        }

        public void AddStickerToCollection (MSSticker sticker)
        {
            // Add sticker to collection
            BrowserViewController.Stickers.Add (sticker);

            // Switch to compact view mode
            Request (MSMessagesAppPresentationStyle.Compact);
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Create new browser and configure it
            BrowserViewController = new StickerBrowserViewController (this, MSStickerSize.Regular);
            BrowserViewController.View.Frame = View.Frame;
            BrowserViewController.ChangeBackgroundColor (UIColor.Gray);

            // Create new Add controller and configure it as well
            AddStickerController = new AddStickerViewController (this);
            AddStickerController.View.Frame = View.Frame;

            // Initially present the sticker browser
            PresentStickerBrowser ();
        }

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
        #endregion
    }
}
```

Cuando el usuario solicita agregar un nuevo adhesivo a su colección disponible, se crea un `AddStickerViewController` nuevo controlador visible y la vista de adhesivo entra en la vista expandida:

```csharp
// Switch to expanded view mode
Request (MSMessagesAppPresentationStyle.Expanded);
```

Cuando el usuario elige una etiqueta para agregarla, se agrega a su colección disponible y se solicita la vista **compacta** :

```csharp
public void AddStickerToCollection (MSSticker sticker)
{
    // Add sticker to collection
    BrowserViewController.Stickers.Add (sticker);

    // Switch to compact view mode
    Request (MSMessagesAppPresentationStyle.Compact);
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

## <a name="summary"></a>Resumen

En este artículo se incluye una extensión de aplicación de mensajes en una solución de Xamarin. iOS que se integra con la aplicación de **mensajes** y presenta una nueva funcionalidad al usuario. Se describe el uso de la extensión para enviar texto, adhesivos, archivos multimedia y mensajes interactivos.



## <a name="related-links"></a>Vínculos relacionados

- [Creador de helados (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-icecreambuilder)
- [Referencia de mensajes](https://developer.apple.com/reference/messages)
- [Guía de programación de extensiones de aplicaciones](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
