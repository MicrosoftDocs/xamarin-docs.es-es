---
title: Copiar y pegar en Xamarin. Mac
description: En este artículo se explica cómo trabajar con la mesa de trabajo para proporcionar una copia y pegar en una aplicación de Xamarin. Mac. Muestra cómo trabajar con tipos de datos estándar que se pueden compartir entre varias aplicaciones y cómo admitir datos personalizados en una aplicación determinada.
ms.prod: xamarin
ms.assetid: 7E9C99FB-B7B4-4C48-B20F-84CB48543083
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: e85a273c75fd09672c6c75738adcdc576705af09
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433979"
---
# <a name="copy-and-paste-in-xamarinmac"></a>Copiar y pegar en Xamarin. Mac

_En este artículo se explica cómo trabajar con la mesa de trabajo para proporcionar una copia y pegar en una aplicación de Xamarin. Mac. Muestra cómo trabajar con tipos de datos estándar que se pueden compartir entre varias aplicaciones y cómo admitir datos personalizados en una aplicación determinada._

## <a name="overview"></a>Información general

Cuando se trabaja con C# y .NET en una aplicación de Xamarin. Mac, tiene acceso a la misma mesa de trabajo (copiar y pegar) que un desarrollador que trabaja en Objective-C tiene.

En este artículo se tratarán las dos formas principales de usar la mesa de opciones en una aplicación de Xamarin. Mac:

1. **Tipos de datos estándar** : como las operaciones de la mesa de actividades se realizan normalmente entre dos aplicaciones no relacionadas, ninguna de las aplicaciones conoce los tipos de datos que admite el otro. Para maximizar la posibilidad de uso compartido, la mesa de la mesa puede contener varias representaciones de un elemento determinado (mediante un conjunto estándar de tipos de datos comunes), lo que permite a la aplicación de consumo elegir la versión que mejor se adapte a sus necesidades.
2. **Datos personalizados** : para admitir la copia y el pegado de datos complejos dentro de Xamarin. Mac, puede definir un tipo de datos personalizado que se administrará mediante la mesa de pegado. Por ejemplo, una aplicación de dibujo vectorial que permite al usuario copiar y pegar formas complejas que se componen de varios tipos de datos y puntos.

[![Ejemplo de la aplicación en ejecución](copy-paste-images/intro01.png "Ejemplo de la aplicación en ejecución")](copy-paste-images/intro01-large.png#lightbox)

En este artículo, trataremos los conceptos básicos sobre cómo trabajar con la mesa de trabajo en una aplicación de Xamarin. Mac para admitir operaciones de copia y pegado. Se recomienda encarecidamente que trabaje primero en el artículo [Hello, Mac](~/mac/get-started/hello-mac.md) , específicamente en las secciones [Introducción a Xcode y Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) , ya que trata conceptos clave y técnicas que vamos a usar en este artículo.

Es posible que desee echar un vistazo a la sección [exponer clases y métodos de C# a Objective-C](~/mac/internals/how-it-works.md) del documento [interno de Xamarin. Mac](~/mac/internals/how-it-works.md) , en él se explican los `Register` atributos y que se `Export` usan para conectar las clases de C# a objetos de Objective-C y elementos de la interfaz de usuario.

## <a name="getting-started-with-the-pasteboard"></a>Introducción a la mesa de pegado

El área de la mesa presenta un mecanismo normalizado para intercambiar datos dentro de una aplicación determinada o entre aplicaciones. El uso típico de una mesa de área en una aplicación de Xamarin. Mac es controlar las operaciones de copiar y pegar. sin embargo, también se admiten otras operaciones (como arrastrar & colocar y Servicios de aplicación).

Para empezar a trabajar rápidamente, vamos a empezar con una introducción sencilla y práctica sobre el uso de las mesas de trabajos en una aplicación de Xamarin. Mac. Más adelante, se proporcionará una explicación detallada de cómo funciona la mesa de trabajo y los métodos usados.

En este ejemplo, vamos a crear una aplicación simple basada en documentos que administra una ventana que contiene una vista de imagen. El usuario podrá copiar y pegar imágenes entre documentos de la aplicación y a o desde otras aplicaciones o varias ventanas dentro de la misma aplicación.

### <a name="creating-the-xamarin-project"></a>Crear el proyecto de Xamarin

En primer lugar, vamos a crear una nueva aplicación de Xamarin. Mac basada en documentos para la que se va a agregar compatibilidad para copiar y pegar.

Siga estos pasos:

1. Inicie Visual Studio para Mac y haga clic en el vínculo **nuevo proyecto...** .
2. Seleccione **Mac**aplicación  >  **App**  >  de**Cocoa**de aplicación Mac y haga clic en el botón **siguiente** : 

    [![Creación de un nuevo proyecto de aplicación de cacao](copy-paste-images/sample01.png "Creación de un nuevo proyecto de aplicación de cacao")](copy-paste-images/sample01-large.png#lightbox)
3. Escriba `MacCopyPaste` para el **nombre del proyecto** y mantenga todo lo demás como predeterminado. Haga clic en Siguiente: 

    [![Establecer el nombre del proyecto](copy-paste-images/sample01a.png "Establecer el nombre del proyecto")](copy-paste-images/sample01a-large.png#lightbox)

4. Haga clic en el botón **crear** : 

    [![Confirmar la nueva configuración del proyecto](copy-paste-images/sample02.png "Confirmar la nueva configuración del proyecto")](copy-paste-images/sample02-large.png#lightbox)

### <a name="add-an-nsdocument"></a>Agregar un NSDocument

A continuación, se agregará `NSDocument` la clase personalizada que actuará como almacenamiento en segundo plano para la interfaz de usuario de la aplicación. Contendrá una sola vista de imagen y sabrá cómo copiar una imagen de la vista en el área de pegado predeterminada y cómo tomar una imagen de la mesa de pegada predeterminada y mostrarla en la vista de imagen.

Haga clic con el botón derecho en el proyecto de Xamarin. Mac en el **Panel de solución** y seleccione **Agregar**  >  **nuevo archivo...**:

![Agregar un NSDocument al proyecto](copy-paste-images/sample03.png "Agregar un NSDocument al proyecto")

Escriba `ImageDocument` para el **Nombre** y haga clic en el botón **Nuevo**. Edite la clase **ImageDocument.CS** y haga que tenga un aspecto similar al siguiente:

```csharp
using System;
using AppKit;
using Foundation;
using ObjCRuntime;

namespace MacCopyPaste
{
    [Register("ImageDocument")]
    public class ImageDocument : NSDocument
    {
        #region Computed Properties
        public NSImageView ImageView {get; set;}

        public ImageInfo Info { get; set; } = new ImageInfo();

        public bool ImageAvailableOnPasteboard {
            get {
                // Initialize the pasteboard
                NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
                Class [] classArray  = { new Class ("NSImage") };

                // Check to see if an image is on the pasteboard
                return pasteboard.CanReadObjectForClasses (classArray, null);
            }
        }
        #endregion

        #region Constructor
        public ImageDocument ()
        {
        }
        #endregion

        #region Public Methods
        [Export("CopyImage:")]
        public void CopyImage(NSObject sender) {

            // Grab the current image
            var image = ImageView.Image;

            // Anything to process?
            if (image != null) {
                // Get the standard pasteboard
                var pasteboard = NSPasteboard.GeneralPasteboard;

                // Empty the current contents
                pasteboard.ClearContents();

                // Add the current image to the pasteboard
                pasteboard.WriteObjects (new NSImage[] {image});

                // Save the custom data class to the pastebaord
                pasteboard.WriteObjects (new ImageInfo[] { Info });

                // Using a Pasteboard Item
                NSPasteboardItem item = new NSPasteboardItem();
                string[] writableTypes = {"public.text"};

                // Add a data provier to the item
                ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
                var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

                // Save to pasteboard
                if (ok) {
                    pasteboard.WriteObjects (new NSPasteboardItem[] { item });
                }
            }

        }

        [Export("PasteImage:")]
        public void PasteImage(NSObject sender) {

            // Initialize the pasteboard
            NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
            Class [] classArray  = { new Class ("NSImage") };

            bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
            if (ok) {
                // Read the image off of the pasteboard
                NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
                NSImage image = (NSImage)objectsToPaste[0];

                // Display the new image
                ImageView.Image = image;
            }

            Class [] classArray2 = { new Class ("ImageInfo") };
            ok = pasteboard.CanReadObjectForClasses (classArray2, null);
            if (ok) {
                // Read the image off of the pasteboard
                NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
                ImageInfo info = (ImageInfo)objectsToPaste[0];

            }

        }
        #endregion
    }
}
```

Echemos un vistazo a parte del código en detalle a continuación.

En el código siguiente se proporciona una propiedad para comprobar la existencia de datos de imagen en la mesa de área predeterminada, si una imagen está disponible, `true` se devuelve else `false` :

```csharp
public bool ImageAvailableOnPasteboard {
    get {
        // Initialize the pasteboard
        NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
        Class [] classArray  = { new Class ("NSImage") };

        // Check to see if an image is on the pasteboard
        return pasteboard.CanReadObjectForClasses (classArray, null);
    }
}
```

En el código siguiente se copia una imagen de la vista de imagen adjunta en la mesa de opciones predeterminada:

```csharp
[Export("CopyImage:")]
public void CopyImage(NSObject sender) {

    // Grab the current image
    var image = ImageView.Image;

    // Anything to process?
    if (image != null) {
        // Get the standard pasteboard
        var pasteboard = NSPasteboard.GeneralPasteboard;

        // Empty the current contents
        pasteboard.ClearContents();

        // Add the current image to the pasteboard
        pasteboard.WriteObjects (new NSImage[] {image});

        // Save the custom data class to the pastebaord
        pasteboard.WriteObjects (new ImageInfo[] { Info });

        // Using a Pasteboard Item
        NSPasteboardItem item = new NSPasteboardItem();
        string[] writableTypes = {"public.text"};

        // Add a data provider to the item
        ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
        var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

        // Save to pasteboard
        if (ok) {
            pasteboard.WriteObjects (new NSPasteboardItem[] { item });
        }
    }

}
```

Y el código siguiente pega una imagen del área de imágenes predeterminada y la muestra en la vista de imagen adjunta (si la mesa de opciones contiene una imagen válida):

```csharp
[Export("PasteImage:")]
public void PasteImage(NSObject sender) {

    // Initialize the pasteboard
    NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
    Class [] classArray  = { new Class ("NSImage") };

    bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
        NSImage image = (NSImage)objectsToPaste[0];

        // Display the new image
        ImageView.Image = image;
    }

    Class [] classArray2 = { new Class ("ImageInfo") };
    ok = pasteboard.CanReadObjectForClasses (classArray2, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
        ImageInfo info = (ImageInfo)objectsToPaste[0]
    }
}
```

Con este documento en su lugar, crearemos la interfaz de usuario para la aplicación de Xamarin. Mac.

### <a name="building-the-user-interface"></a>Compilar la interfaz de usuario

Haga doble clic en el archivo **Main. Storyboard** para abrirlo en Xcode. A continuación, agregue una barra de herramientas y una imagen correctamente y configúrela de la siguiente manera:

[![Editar la barra de herramientas](copy-paste-images/sample04.png "Editar la barra de herramientas")](copy-paste-images/sample04-large.png#lightbox)

Agregue un elemento de la **barra de herramientas** copiar y pegar imagen en el lado izquierdo de la barra de herramientas. Vamos a usarlos como métodos abreviados para copiar y pegar desde el menú edición. A continuación, agregue cuatro elementos de la **barra de herramientas de imagen** al lado derecho de la barra de herramientas. Se usarán para rellenar correctamente la imagen con algunas imágenes predeterminadas.

Para obtener más información sobre cómo trabajar con barras de herramientas, consulte la documentación de las [barras de herramientas](~/mac/user-interface/toolbar.md) .

A continuación, se exponen las salidas y acciones siguientes para los elementos de la barra de herramientas y la imagen:

[![Creación de salidas y acciones](copy-paste-images/sample05.png "Creación de salidas y acciones")](copy-paste-images/sample05-large.png#lightbox)

Para obtener más información sobre cómo trabajar con salidas y acciones, consulte la sección [tomas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) de nuestra documentación de [Hello, Mac](~/mac/get-started/hello-mac.md) .

### <a name="enabling-the-user-interface"></a>Habilitación de la interfaz de usuario

Con nuestra interfaz de usuario creada en Xcode y nuestro elemento de la interfaz de usuario expuesto a través de salidas y acciones, es necesario agregar el código para habilitar la interfaz de usuario. Haga doble clic en el archivo **ImageWindow.CS** en el **Panel de solución** y haga que tenga un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacCopyPaste
{
    public partial class ImageWindow : NSWindow
    {
        #region Private Variables
        ImageDocument document;
        #endregion

        #region Computed Properties
        [Export ("Document")]
        public ImageDocument Document {
            get {
                return document;
            }
            set {
                WillChangeValue ("Document");
                document = value;
                DidChangeValue ("Document");
            }
        }

        public ViewController ImageViewController {
            get { return ContentViewController as ViewController; }
        }

        public NSImage Image {
            get {
                return ImageViewController.Image;
            }
            set {
                ImageViewController.Image = value;
            }
        }
        #endregion

        #region Constructor
        public ImageWindow (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Create a new document instance
            Document = new ImageDocument ();

            // Attach to image view
            Document.ImageView = ImageViewController.ContentView;
        }
        #endregion

        #region Public Methods
        public void CopyImage (NSObject sender)
        {
            Document.CopyImage (sender);
        }

        public void PasteImage (NSObject sender)
        {
            Document.PasteImage (sender);
        }

        public void ImageOne (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image01.jpg");

            // Set image info
            Document.Info.Name = "city";
            Document.Info.ImageType = "jpg";
        }

        public void ImageTwo (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image02.jpg");

            // Set image info
            Document.Info.Name = "theater";
            Document.Info.ImageType = "jpg";
        }

        public void ImageThree (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image03.jpg");

            // Set image info
            Document.Info.Name = "keyboard";
            Document.Info.ImageType = "jpg";
        }

        public void ImageFour (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image04.jpg");

            // Set image info
            Document.Info.Name = "trees";
            Document.Info.ImageType = "jpg";
        }
        #endregion
    }
}
```

Echemos un vistazo a este código en detalle a continuación.

En primer lugar, se expone una instancia de la `ImageDocument` clase que se ha creado anteriormente:

```csharp
private ImageDocument _document;
...

[Export ("Document")]
public ImageDocument Document {
    get { return _document; }
    set {
        WillChangeValue ("Document");
        _document = value;
        DidChangeValue ("Document");
    }
}
```

Mediante `Export` , `WillChangeValue` y `DidChangeValue` , se configura la `Document` propiedad para permitir el enlace de datos y el código de valor de clave en Xcode.

También exponemos la imagen de la imagen correctamente que hemos agregado a nuestra interfaz de usuario en Xcode con la siguiente propiedad:

```csharp
public ViewController ImageViewController {
    get { return ContentViewController as ViewController; }
}

public NSImage Image {
    get {
        return ImageViewController.Image;
    }
    set {
        ImageViewController.Image = value;
    }
}
```

Cuando se carga y se muestra la ventana principal, se crea una instancia de nuestra `ImageDocument` clase y se adjunta la imagen de la interfaz de usuario con el código siguiente:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create a new document instance
    Document = new ImageDocument ();

    // Attach to image view
    Document.ImageView = ImageViewController.ContentView;
}
```

Por último, en respuesta al usuario que hace clic en los elementos de la barra de herramientas copiar y pegar, llamamos a la instancia de la `ImageDocument` clase para realizar el trabajo real:

```csharp
partial void CopyImage (NSObject sender) {
    Document.CopyImage(sender);
}

partial void PasteImage (Foundation.NSObject sender) {
    Document.PasteImage(sender);
}
```

### <a name="enabling-the-file-and-edit-menus"></a>Habilitar los menús archivo y editar

Lo último que debemos hacer es habilitar el **nuevo** elemento de menú en el menú **archivo** (para crear nuevas instancias de la ventana principal) y habilitar los elementos de menú **cortar**, **copiar** y **pegar** en el menú **edición** .

Para habilitar el **nuevo** elemento de menú, edite el archivo **AppDelegate.CS** y agregue el código siguiente:

```csharp
public int UntitledWindowCount { get; set;} =1;
...

[Export ("newDocument:")]
void NewDocument (NSObject sender) {
    // Get new window
    var storyboard = NSStoryboard.FromName ("Main", null);
    var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

    // Display
    controller.ShowWindow(this);

    // Set the title
    controller.Window.Title = (++UntitledWindowCount == 1) ? "untitled" : string.Format ("untitled {0}", UntitledWindowCount);
}
```

Para obtener más información, consulte la sección [trabajar con varias ventanas](~/mac/user-interface/window.md) de la documentación de [Windows](~/mac/user-interface/window.md) .

Para habilitar los elementos de menú **cortar**, **copiar** y **pegar** , edite el archivo **AppDelegate.CS** y agregue el código siguiente:

```csharp
[Export("copy:")]
void CopyImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Copy the image to the clipboard
    window.Document.CopyImage (sender);
}

[Export("cut:")]
void CutImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Copy the image to the clipboard
    window.Document.CopyImage (sender);

    // Clear the existing image
    window.Image = null;
}

[Export("paste:")]
void PasteImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Paste the image from the clipboard
    window.Document.PasteImage (sender);
}
```

Para cada elemento de menú, obtenemos la ventana de clave actual, más alto y la convertimos en nuestra `ImageWindow` clase:

```csharp
var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
```

Desde allí se llama `ImageDocument` a la instancia de clase de esa ventana para controlar las acciones de copiar y pegar. Por ejemplo: 

```csharp
window.Document.CopyImage (sender);
```

Solo deseamos tener acceso a los elementos de menú **cortar**, **copiar** y **pegar** si hay datos de imagen en la mesa de impresión predeterminada o en la imagen de la ventana activa actual.

Vamos a agregar un archivo **EditMenuDelegate.CS** al proyecto de Xamarin. Mac y que tenga un aspecto similar al siguiente:

```csharp
using System;
using AppKit;

namespace MacCopyPaste
{
    public class EditMenuDelegate : NSMenuDelegate
    {
        #region Override Methods
        public override void MenuWillHighlightItem (NSMenu menu, NSMenuItem item)
        {
        }

        public override void NeedsUpdate (NSMenu menu)
        {
            // Get list of menu items
            NSMenuItem[] Items = menu.ItemArray ();

            // Get the key window and determine if the required images are available
            var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
            var hasImage = (window != null) && (window.Image != null);
            var hasImageOnPasteboard = (window != null) && window.Document.ImageAvailableOnPasteboard;

            // Process every item in the menu
            foreach(NSMenuItem item in Items) {
                // Take action based on the menu title
                switch (item.Title) {
                case "Cut":
                case "Copy":
                case "Delete":
                    // Only enable if there is an image in the view
                    item.Enabled = hasImage;
                    break;
                case "Paste":
                    // Only enable if there is an image on the pasteboard
                    item.Enabled = hasImageOnPasteboard;
                    break;
                default:
                    // Only enable the item if it has a sub menu
                    item.Enabled = item.HasSubmenu;
                    break;
                }
            }
        }
        #endregion
    }
}
```

Una vez más, obtenemos la ventana actual, el nivel superior y usamos su `ImageDocument` instancia de clase para ver si existen los datos de imagen necesarios. A continuación, usamos el `MenuWillHighlightItem` método para habilitar o deshabilitar cada elemento en función de este estado.

Edite el archivo **AppDelegate.CS** y haga que el método tenga el `DidFinishLaunching` aspecto siguiente:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Disable automatic item enabling on the Edit menu
    EditMenu.AutoEnablesItems = false;
    EditMenu.Delegate = new EditMenuDelegate ();
}
```

En primer lugar, deshabilitaremos la habilitación y deshabilitación automática de los elementos de menú en el menú edición. A continuación, se adjunta una instancia de la `EditMenuDelegate` clase que se ha creado anteriormente.

Para obtener más información, consulte la documentación de los [menús](~/mac/user-interface/menu.md) .

### <a name="testing-the-app"></a>Probar la aplicación

Con todo en su lugar, estamos preparados para probar la aplicación. Compilar y ejecutar la aplicación y se muestra la interfaz principal:

![Ejecutar la aplicación](copy-paste-images/run01.png "Ejecución de la aplicación")

Si abre el menú Edición, tenga en cuenta que las opciones **cortar**, **copiar** y **pegar** están deshabilitadas porque no hay ninguna imagen en el área imagen o en la mesa de opciones predeterminada:

![Abrir el menú Edición](copy-paste-images/run02.png "Abrir el menú Edición")

Si agrega una imagen al área imagen y vuelve a abrir el menú Edición, los elementos se habilitarán ahora:

![Mostrar los elementos del menú Edición están habilitados](copy-paste-images/run03.png "Mostrar los elementos del menú Edición están habilitados")

Si copia la imagen y selecciona **nuevo** en el menú Archivo, puede pegar la imagen en la nueva ventana:

![Pegar una imagen en una nueva ventana](copy-paste-images/run04.png "Pegar una imagen en una nueva ventana")

En las secciones siguientes, veremos una visión detallada de cómo trabajar con la mesa de trabajo en una aplicación de Xamarin. Mac.

## <a name="about-the-pasteboard"></a>Acerca de la mesa de la mesa

En macOS (antes conocido como OS X), la mesa de pegado ( `NSPasteboard` ) proporciona compatibilidad con varios procesos de servidor, como copiar & pegar, arrastrar & colocar y servicios de aplicación. En las secciones siguientes, veremos con más detalle varios conceptos clave de la mesa de pegado.

### <a name="what-is-a-pasteboard"></a>¿Qué es una mesa de área?

La `NSPasteboard` clase proporciona un mecanismo normalizado para intercambiar información entre aplicaciones o dentro de una aplicación determinada. La función principal de una mesa de pegado es para controlar las operaciones de copiar y pegar:

1. Cuando el usuario selecciona un elemento en una aplicación y usa el elemento de menú **cortar** o **copiar** , se colocan una o más representaciones del elemento seleccionado en la mesa de área.
2. Cuando el usuario usa el elemento de menú **pegar** (dentro de la misma aplicación o en otro diferente), la versión de los datos que puede controlar se copia desde la mesa de pegado y se agrega a la aplicación.

Los usos menos obvios de la mesa de actividades incluyen las operaciones de búsqueda, arrastre, arrastrar y colocar y servicios de aplicación:

- Cuando el usuario inicia una operación de arrastre, los datos de arrastre se copian en la mesa de la mesa. Si la operación de arrastrar finaliza con una colocación en otra aplicación, esa aplicación copia los datos de la mesa de la mesa.
- En el caso de los servicios de traducción, la aplicación solicitante copia los datos que se van a convertir en la mesa de pegado. El servicio de aplicación recupera los datos de la mesa de pegado, realiza la traducción y, después, los pega de nuevo en la mesa de pegado.

En su forma más sencilla, las mesas de área se usan para trasladar los datos dentro de una aplicación determinada o entre las aplicaciones y existen en un área de memoria global especial fuera del proceso de la aplicación. Aunque los conceptos de las Portapapeles se extienen fácilmente, se deben tener en cuenta varios detalles más complejos. Estos se tratarán con más detalle a continuación.

### <a name="named-pasteboards"></a>Portapapeles con nombre

Una mesa de tareas puede ser pública o privada y se puede usar para una variedad de propósitos dentro de una aplicación o entre varias aplicaciones. macOS proporciona varias mesas de pegas estándar, cada una con un uso específico y definido:

- `NSGeneralPboard` : La mesa de opciones predeterminada para las operaciones de **cortar**, **copiar** y **pegar** .
- `NSRulerPboard` -Admite operaciones de **cortar**, **copiar** y **pegar** en **reglas**.
- `NSFontPboard` -Admite operaciones de **cortar**, **copiar** y **pegar** en `NSFont` objetos.
- `NSFindPboard` : Admite los paneles de búsqueda específicos de la aplicación que pueden compartir texto de búsqueda.
- `NSDragPboard` : Admite las operaciones de **arrastrar y colocar &** .

En la mayoría de los casos, usará una de las portapapeles definidas por el sistema. Pero puede haber situaciones que requieran la creación de sus propias mesa de pegado. En estas situaciones, puede usar el `FromName (string name)` método de la `NSPasteboard` clase para crear una mesa de pegado personalizada con el nombre especificado.

Opcionalmente, puede llamar al `CreateWithUniqueName` método de la `NSPasteboard` clase para crear una mesa de área con un nombre único.

### <a name="pasteboard-items"></a>Elementos de la mesa de área

Cada fragmento de datos que una aplicación escribe en una mesa de trabajo se considera un _elemento de mesa_ de trabajo y una mesa de trabajo puede contener varios elementos al mismo tiempo. De esta manera, una aplicación puede escribir varias versiones de los datos que se van a copiar en una mesa de pegado (por ejemplo, texto sin formato y texto con formato) y la aplicación de recuperación solo puede leer los datos que puede procesar (como solo texto sin formato).

### <a name="data-representations-and-uniform-type-identifiers"></a>Representaciones de datos e identificadores de tipo uniformes

Las operaciones de la mesa de tareas suelen tener lugar entre dos (o más) aplicaciones que no tienen ningún conocimiento o los tipos de datos que cada uno puede controlar. Como se indicó en la sección anterior, para maximizar la posibilidad de compartir información, una mesa de pegado puede contener varias representaciones de los datos que se están copiando y pegando.

Cada representación se identifica mediante un identificador de tipo uniforme (UTI), que no es más que una cadena simple que identifica de forma única el tipo de fecha que se presenta (para obtener más información, consulte la documentación de [Introducción a los identificadores de tipo uniformes](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319) de Apple). 

Si va a crear un tipo de datos personalizado (por ejemplo, un objeto de dibujo en una aplicación de dibujo vectorial), puede crear su propio UTI para identificarlo de forma exclusiva en las operaciones de copiar y pegar.

Cuando una aplicación se prepara para pegar datos copiados de una mesa de pegado, debe encontrar la representación que mejor se adapte a sus capacidades (si existe). Normalmente, será el tipo más rico disponible (por ejemplo, texto con formato para una aplicación de procesamiento de texto), revirtiendo a las formas más sencillas disponibles según sea necesario (texto sin formato para un editor de texto simple).

<a name="Promised_Data"></a>

### <a name="promised-data"></a>Datos prometidos

En general, debe proporcionar tantas representaciones de los datos que se copian como sea posible para maximizar el uso compartido entre las aplicaciones. Sin embargo, debido a restricciones de tiempo o de memoria, es posible que no resulte práctico escribir realmente cada tipo de datos en la mesa de la mesa.

En esta situación, puede colocar la primera representación de datos en la mesa de la mesa y la aplicación receptora puede solicitar una representación diferente, que se puede generar sobre la marcha justo antes de la operación de pegado.

Al colocar el elemento inicial en la mesa de pegado, especificará que una o varias de las demás representaciones disponibles se proporcionan mediante un objeto que se ajusta a la `NSPasteboardItemDataProvider` interfaz. Estos objetos proporcionarán representaciones adicionales a petición, como solicita la aplicación receptora.

### <a name="change-count"></a>Recuento de cambios

Cada área de la mesa mantiene un _recuento de cambios_ que se incrementa cada vez que se declara un nuevo propietario. Una aplicación puede determinar si el contenido de la mesa de pegado ha cambiado desde la última vez que lo examinó comprobando el valor del recuento de cambios.

Use los `ChangeCount` `ClearContents` métodos y de la `NSPasteboard` clase para modificar el número de cambios de una mesa de pegada determinada.

## <a name="copying-data-to-a-pasteboard"></a>Copiar datos en una mesa de pegado

Para realizar una operación de copia, primero tiene que obtener acceso a una mesa de pegado, borrar cualquier contenido existente y escribir tantas representaciones de los datos como sean necesarias para la mesa de pegado.

Por ejemplo:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add the current image to the pasteboard
pasteboard.WriteObjects (new NSImage[] {image});
```

Normalmente, solo escribirá en la mesa de opciones general, como hemos hecho en el ejemplo anterior. Cualquier objeto que se envíe al `WriteObjects` método *debe* ajustarse a la `INSPasteboardWriting` interfaz. Varias clases integradas (como `NSString` , `NSImage` , `NSURL` ,, `NSColor` `NSAttributedString` y `NSPasteboardItem` ) se ajustan automáticamente a esta interfaz.

Si está escribiendo una clase de datos personalizada en la mesa de área, debe ajustarse a la `INSPasteboardWriting` interfaz o incluirse en una instancia de la `NSPasteboardItem` clase (consulte la sección [tipos de datos personalizados](#Custom_Data_Types) más adelante).

## <a name="reading-data-from-a-pasteboard"></a>Leer datos de una mesa de pegado

Como se indicó anteriormente, para maximizar la posibilidad de compartir datos entre aplicaciones, es posible que se escriban varias representaciones de los datos copiados en la mesa de ventas. Depende de la aplicación receptora seleccionar la versión más enriquecida posible para sus capacidades (si existe alguna).

### <a name="simple-paste-operation"></a>Operación de pegado simple

Puede leer los datos de la mesa de pegado mediante el `ReadObjectsForClasses` método. Necesitará dos parámetros:

1. Matriz de `NSObject` tipos de clase basados en que se desea leer de la mesa de pegado. Debería ordenar esto con el tipo de datos más deseado en primer lugar, con cualquier tipo restante en preferencia decreciente.
2. Diccionario que contiene restricciones adicionales (como la limitación a tipos de contenido de dirección URL específicos) o un diccionario vacío si no se requieren más restricciones.

El método devuelve una matriz de elementos que cumplen los criterios que pasamos y, por tanto, contiene como máximo el mismo número de tipos de datos que se solicitan. también es posible que ninguno de los tipos solicitados esté presente y se devolverá una matriz vacía.

Por ejemplo, el código siguiente comprueba si `NSImage` existe un en la mesa de opciones general y lo muestra en una imagen bien, si lo hace:

```csharp
[Export("PasteImage:")]
public void PasteImage(NSObject sender) {

    // Initialize the pasteboard
    NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
    Class [] classArray  = { new Class ("NSImage") };

    bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
        NSImage image = (NSImage)objectsToPaste[0];

        // Display the new image
        ImageView.Image = image;
    }

    Class [] classArray2 = { new Class ("ImageInfo") };
    ok = pasteboard.CanReadObjectForClasses (classArray2, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
        ImageInfo info = (ImageInfo)objectsToPaste[0];
            }
}
```

### <a name="requesting-multiple-data-types"></a>Solicitar varios tipos de datos

En función del tipo de aplicación de Xamarin. Mac que se va a crear, es posible que pueda controlar varias representaciones de los datos que se van a pegar. En esta situación, existen dos escenarios para recuperar datos de la mesa de pegado:

1. Realice una llamada única al `ReadObjectsForClasses` método y proporcione una matriz de todas las representaciones que desee (en el orden preferido).
2. Realice varias llamadas al `ReadObjectsForClasses` método solicitando una matriz diferente de tipos cada vez.

Vea la sección anterior **operación de pegado** más arriba para obtener más información sobre cómo recuperar datos de una mesa de pegado.

### <a name="checking-for-existing-data-types"></a>Comprobar los tipos de datos existentes

Hay ocasiones en las que es posible que desee comprobar si una mesa de pegado contiene una representación de datos determinada sin leer realmente los datos de la mesa de pegado (por ejemplo, habilitar el elemento de menú **pegar** solo cuando existan datos válidos).

Llame al `CanReadObjectForClasses` método de la mesa de la mesa para ver si contiene un tipo determinado.

Por ejemplo, el código siguiente determina si la mesa de opciones general contiene una `NSImage` instancia de:

```csharp
public bool ImageAvailableOnPasteboard {
    get {
        // Initialize the pasteboard
        NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
        Class [] classArray  = { new Class ("NSImage") };

        // Check to see if an image is on the pasteboard
        return pasteboard.CanReadObjectForClasses (classArray, null);
    }
}
```

### <a name="reading-urls-from-the-pasteboard"></a>Lectura de las direcciones URL de la mesa de la mesa

En función de la función de una aplicación de Xamarin. Mac determinada, puede que sea necesario leer las direcciones URL de una mesa de pegado, pero solo si cumplen un determinado conjunto de criterios (por ejemplo, apuntar a archivos o direcciones URL de un tipo de datos específico). En esta situación, puede especificar criterios de búsqueda adicionales mediante el segundo parámetro de los `CanReadObjectForClasses` `ReadObjectsForClasses` métodos o.

<a name="Custom_Data_Types"></a>

## <a name="custom-data-types"></a>Tipos de datos personalizados

Hay ocasiones en las que tendrá que guardar sus propios tipos personalizados en la mesa de pegado desde una aplicación de Xamarin. Mac. Por ejemplo, una aplicación de dibujo vectorial que permite al usuario copiar y pegar objetos de dibujo.

En esta situación, deberá diseñar la clase personalizada de datos para que herede de `NSObject` y se ajuste a unas cuantas interfaces ( `INSCoding` , `INSPasteboardWriting` y `INSPasteboardReading` ). Opcionalmente, puede usar un `NSPasteboardItem` para encapsular los datos que se van a copiar o pegar.

Ambas opciones se tratarán en detalle a continuación.

### <a name="using-a-custom-class"></a>Usar una clase personalizada

En esta sección se va a expandir la aplicación de ejemplo simple que creamos al principio de este documento y se agrega una clase personalizada para realizar un seguimiento de la información sobre la imagen que se va a copiar y pegar entre ventanas.

Agregue una nueva clase al proyecto y llámela **ImageInfo.CS**. Edite el archivo y haga que tenga el aspecto siguiente:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacCopyPaste
{
    [Register("ImageInfo")]
    public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
    {
        #region Computed Properties
        [Export("name")]
        public string Name { get; set; }

        [Export("imageType")]
        public string ImageType { get; set; }
        #endregion

        #region Constructors
        [Export ("init")]
        public ImageInfo ()
        {
        }
        
        public ImageInfo (IntPtr p) : base (p)
        {
        }

        [Export ("initWithCoder:")]
        public ImageInfo(NSCoder decoder) {

            // Decode data
            NSString name = decoder.DecodeObject("name") as NSString;
            NSString type = decoder.DecodeObject("imageType") as NSString;

            // Save data
            Name = name.ToString();
            ImageType = type.ToString ();
        }
        #endregion

        #region Public Methods
        [Export ("encodeWithCoder:")]
        public void EncodeTo (NSCoder encoder) {

            // Encode data
            encoder.Encode(new NSString(Name),"name");
            encoder.Encode(new NSString(ImageType),"imageType");
        }

        [Export ("writableTypesForPasteboard:")]
        public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
            string[] writableTypes = {"com.xamarin.image-info", "public.text"};
            return writableTypes;
        }

        [Export ("pasteboardPropertyListForType:")]
        public virtual NSObject GetPasteboardPropertyListForType (string type) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return NSKeyedArchiver.ArchivedDataWithRootObject(this);
            case "public.text":
                return new NSString(string.Format("{0}.{1}", Name, ImageType));
            }

            // Failure, return null
            return null;
        }

        [Export ("readableTypesForPasteboard:")]
        public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
            string[] readableTypes = {"com.xamarin.image-info", "public.text"};
            return readableTypes;
        }

        [Export ("readingOptionsForType:pasteboard:")]
        public static NSPasteboardReadingOptions GetReadingOptionsForType (string type, NSPasteboard pasteboard) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return NSPasteboardReadingOptions.AsKeyedArchive;
            case "public.text":
                return NSPasteboardReadingOptions.AsString;
            }

            // Default to property list
            return NSPasteboardReadingOptions.AsPropertyList;
        }

        [Export ("initWithPasteboardPropertyList:ofType:")]
        public NSObject InitWithPasteboardPropertyList (NSObject propertyList, string type) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return new ImageInfo();
            case "public.text":
                return new ImageInfo();
            }

            // Failure, return null
            return null;
        }
        #endregion
    }
}
    
```

En las secciones siguientes se detallará esta clase.

#### <a name="inheritance-and-interfaces"></a>Herencia e interfaces

Antes de que se pueda escribir o leer una clase de datos personalizada en una mesa de pegado, debe ajustarse a las `INSPastebaordWriting` `INSPasteboardReading` interfaces y. Además, debe heredar de `NSObject` y también ajustarse a la `INSCoding` interfaz:

```csharp
[Register("ImageInfo")]
public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
...
```

La clase también se debe exponer a Objective-C mediante la `Register` Directiva y debe exponer las propiedades o los métodos necesarios mediante `Export` . Por ejemplo:

```csharp
[Export("name")]
public string Name { get; set; }

[Export("imageType")]
public string ImageType { get; set; }
```

Estamos exponiendo los dos campos de datos que esta clase contendrá: el nombre de la imagen y su tipo (jpg, PNG, etc.). 

Para obtener más información, vea la sección [exposición de clases o métodos de C# a Objective-C](~/mac/internals/how-it-works.md) de la documentación [interna de Xamarin. Mac](~/mac/internals/how-it-works.md) , en la que se explican los `Register` atributos y que se `Export` usan para conectar las clases de C# a objetos de Objective-C y elementos de la interfaz de usuario.

#### <a name="constructors"></a>Constructores

Se requerirán dos constructores (expuestos correctamente a Objective-C) para la clase de datos personalizada para que se pueda leer desde una mesa de pegado:

```csharp
[Export ("init")]
public ImageInfo ()
{
}

[Export ("initWithCoder:")]
public ImageInfo(NSCoder decoder) {

    // Decode data
    NSString name = decoder.DecodeObject("name") as NSString;
    NSString type = decoder.DecodeObject("imageType") as NSString;

    // Save data
    Name = name.ToString();
    ImageType = type.ToString ();
}
```

En primer lugar, se expone el constructor _vacío_ en el método de Objective-C predeterminado de `init` .

A continuación, exponemos un `NSCoding` constructor compatible que se usará para crear una nueva instancia del objeto a partir de la mesa de pegado al pegar con el nombre exportado de `initWithCoder` .

Este constructor toma `NSCoder` (tal y como lo crea `NSKeyedArchiver` cuando se escribe en la mesa de la mesa), extrae los datos emparejados de clave y valor y los guarda en los campos de propiedades de la clase de datos.

#### <a name="writing-to-the-pasteboard"></a>Escribir en el área de la mesa

Si se ajusta a la `INSPasteboardWriting` interfaz, es necesario exponer dos métodos y, opcionalmente, un tercer método para que la clase se pueda escribir en la mesa de la mesa.

En primer lugar, es necesario indicar a la mesa de las representaciones de tipos de datos en las que se puede escribir la clase personalizada:

```csharp
[Export ("writableTypesForPasteboard:")]
public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
    string[] writableTypes = {"com.xamarin.image-info", "public.text"};
    return writableTypes;
}
```

Cada representación se identifica mediante un identificador de tipo uniforme (UTI), que no es más que una cadena simple que identifica de forma única el tipo de datos que se presentan (para obtener más información, consulte la documentación de [Introducción a los identificadores de tipo uniformes](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319) de Apple).

En nuestro formato personalizado, vamos a crear nuestra propia UTI: "com. Xamarin. Image-info" (tenga en cuenta que, en notación inversa, como un identificador de la aplicación). Nuestra clase también es capaz de escribir una cadena estándar en la mesa de área ( `public.text` ). 

A continuación, es necesario crear el objeto en el formato solicitado que realmente se escribe en la mesa de la mesa:

```csharp
[Export ("pasteboardPropertyListForType:")]
public virtual NSObject GetPasteboardPropertyListForType (string type) {

    // Take action based on the requested type
    switch (type) {
    case "com.xamarin.image-info":
        return NSKeyedArchiver.ArchivedDataWithRootObject(this);
    case "public.text":
        return new NSString(string.Format("{0}.{1}", Name, ImageType));
    }

    // Failure, return null
    return null;
}
```

Para el `public.text` tipo, se devuelve un objeto simple con formato `NSString` . Para el `com.xamarin.image-info` tipo personalizado, usamos `NSKeyedArchiver` y la `NSCoder` interfaz para codificar la clase de datos personalizada en un archivo emparejado de clave-valor. Tendremos que implementar el método siguiente para controlar realmente la codificación:

```csharp
[Export ("encodeWithCoder:")]
public void EncodeTo (NSCoder encoder) {

    // Encode data
    encoder.Encode(new NSString(Name),"name");
    encoder.Encode(new NSString(ImageType),"imageType");
}
```

Los pares clave-valor individuales se escriben en el codificador y se descodificarán con el segundo constructor que se agregó anteriormente.

Opcionalmente, se puede incluir el método siguiente para definir las opciones al escribir datos en la mesa de pegado:

```csharp
[Export ("writingOptionsForType:pasteboard:"), CompilerGenerated]
public virtual NSPasteboardWritingOptions GetWritingOptionsForType (string type, NSPasteboard pasteboard) {
    return NSPasteboardWritingOptions.WritingPromised;
}
```

Actualmente, solo `WritingPromised` está disponible la opción y se debe usar cuando un tipo determinado solo se promete y no se escriba realmente en la mesa de la mesa. Para obtener más información, consulte la sección de [datos prometidos](#Promised_Data) anterior.

Con estos métodos en su lugar, se puede usar el siguiente código para escribir la clase personalizada en la mesa de la mesa:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add info to the pasteboard
pasteboard.WriteObjects (new ImageInfo[] { Info });
```

#### <a name="reading-from-the-pasteboard"></a>Leer de la mesa de pegado

Al aplicarse a la `INSPasteboardReading` interfaz, es necesario exponer tres métodos para que se pueda leer la clase de datos personalizada de la mesa de pegado.

En primer lugar, es necesario indicar a la mesa de las representaciones de tipos de datos que la clase personalizada puede leer desde el portapapeles:

```csharp
[Export ("readableTypesForPasteboard:")]
public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
    string[] readableTypes = {"com.xamarin.image-info", "public.text"};
    return readableTypes;
}
```

De nuevo, estos se definen como UTI simples y son los mismos tipos que hemos definido en la sección sobre **escritura en la mesa** de la mesa anterior.

A continuación, es necesario indicar a la mesa de opciones _Cómo_ se leerá cada uno de los tipos de UTI con el siguiente método:

```csharp
[Export ("readingOptionsForType:pasteboard:")]
public static NSPasteboardReadingOptions GetReadingOptionsForType (string type, NSPasteboard pasteboard) {

    // Take action based on the requested type
    switch (type) {
    case "com.xamarin.image-info":
        return NSPasteboardReadingOptions.AsKeyedArchive;
    case "public.text":
        return NSPasteboardReadingOptions.AsString;
    }

    // Default to property list
    return NSPasteboardReadingOptions.AsPropertyList;
}
```

En el caso del `com.xamarin.image-info` tipo, estamos indicando a la mesa de pegado que descodifique el par clave-valor que creamos con `NSKeyedArchiver` al escribir la clase en la mesa de la mesa llamando al `initWithCoder:` constructor que agregamos a la clase.

Por último, es necesario agregar el siguiente método para leer las demás representaciones de datos de UTI de la mesa de opciones:

```csharp
[Export ("initWithPasteboardPropertyList:ofType:")]
public NSObject InitWithPasteboardPropertyList (NSObject propertyList, string type) {

    // Take action based on the requested type
    switch (type) {
    case "public.text":
        return new ImageInfo();
    }

    // Failure, return null
    return null;
}
```

Con todos estos métodos en su lugar, se puede leer la clase de datos personalizada de la mesa de pegado mediante el código siguiente:

```csharp
// Initialize the pasteboard
NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
var classArrayPtrs = new [] { Class.GetHandle (typeof(ImageInfo)) };
NSArray classArray = NSArray.FromIntPtrs (classArrayPtrs);

// NOTE: Sending messages directly to the base Objective-C API because of this defect:
// https://bugzilla.xamarin.com/show_bug.cgi?id=31760
// Check to see if image info is on the pasteboard
ok = bool_objc_msgSend_IntPtr_IntPtr (pasteboard.Handle, Selector.GetHandle ("canReadObjectForClasses:options:"), classArray.Handle, IntPtr.Zero);

if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = NSArray.ArrayFromHandle<Foundation.NSObject>(IntPtr_objc_msgSend_IntPtr_IntPtr (pasteboard.Handle, Selector.GetHandle ("readObjectsForClasses:options:"), classArray.Handle, IntPtr.Zero));
    ImageInfo info = (ImageInfo)objectsToPaste[0];
}
```

### <a name="using-a-nspasteboarditem"></a>Uso de un NSPasteboardItem

Puede haber ocasiones en las que tenga que escribir elementos personalizados en la mesa de tareas que no garantizan la creación de una clase personalizada o que desea proporcionar datos en un formato común, solo según sea necesario. En estas situaciones, puede usar `NSPasteboardItem` .

Un `NSPasteboardItem` proporciona un control específico de los datos que se escriben en la mesa de la mesa y está diseñado para el acceso temporal; debe desecharse después de que se haya escrito en la mesa de pegado.

#### <a name="writing-data"></a>Escritura de datos

Para escribir los datos personalizados en un `NSPasteboardItem` , debe proporcionar un personalizado `NSPasteboardItemDataProvider` . Agregue una nueva clase al proyecto y llámela **ImageInfoDataProvider.CS**. Edite el archivo y haga que tenga el aspecto siguiente:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacCopyPaste
{
    [Register("ImageInfoDataProvider")]
    public class ImageInfoDataProvider : NSPasteboardItemDataProvider
    {
        #region Computed Properties
        public string Name { get; set;}
        public string ImageType { get; set;}
        #endregion

        #region Constructors
        [Export ("init")]
        public ImageInfoDataProvider ()
        {
        }

        public ImageInfoDataProvider (string name, string imageType)
        {
            // Initialize
            this.Name = name;
            this.ImageType = imageType;
        }

        protected ImageInfoDataProvider (NSObjectFlag t){
        }

        protected internal ImageInfoDataProvider (IntPtr handle){

        }
        #endregion

        #region Override Methods
        [Export ("pasteboardFinishedWithDataProvider:")]
        public override void FinishedWithDataProvider (NSPasteboard pasteboard)
        {
            
        }

        [Export ("pasteboard:item:provideDataForType:")]
        public override void ProvideDataForType (NSPasteboard pasteboard, NSPasteboardItem item, string type)
        {

            // Take action based on the type
            switch (type) {
            case "public.text":
                // Encode the data to string 
                item.SetStringForType(string.Format("{0}.{1}", Name, ImageType),type);
                break;
            }

        }
        #endregion
    }
}
```

Como hicimos con la clase de datos personalizada, es necesario usar las `Register` directivas y `Export` para exponerla a Objective-C. La clase debe heredar de `NSPasteboardItemDataProvider` y debe implementar `FinishedWithDataProvider` los `ProvideDataForType` métodos y.

Utilice el `ProvideDataForType` método para proporcionar los datos que se van a ajustar en el de la `NSPasteboardItem` siguiente manera:

```csharp
[Export ("pasteboard:item:provideDataForType:")]
public override void ProvideDataForType (NSPasteboard pasteboard, NSPasteboardItem item, string type)
{

    // Take action based on the type
    switch (type) {
    case "public.text":
        // Encode the data to string 
        item.SetStringForType(string.Format("{0}.{1}", Name, ImageType),type);
        break;
    }

}
```

En este caso, se almacenan dos fragmentos de información sobre la imagen (nombre y ImageType) y se escriben en una cadena simple ( `public.text` ).

Escriba escribir los datos en la mesa de opciones, use el código siguiente:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Using a Pasteboard Item
NSPasteboardItem item = new NSPasteboardItem();
string[] writableTypes = {"public.text"};

// Add a data provider to the item
ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

// Save to pasteboard
if (ok) {
    pasteboard.WriteObjects (new NSPasteboardItem[] { item });
}
```

#### <a name="reading-data"></a>Lectura de datos

Para volver a leer los datos de la mesa de pegado, use el siguiente código:

```csharp
// Initialize the pasteboard
NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
Class [] classArray  = { new Class ("NSImage") };

bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
    NSImage image = (NSImage)objectsToPaste[0];

    // Do something with data
    ...
}
            
Class [] classArray2 = { new Class ("ImageInfo") };
ok = pasteboard.CanReadObjectForClasses (classArray2, null);
if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
    
    // Do something with data
    ...
}
```

## <a name="summary"></a>Resumen

En este artículo se ha explicado con detalle cómo trabajar con la mesa de trabajo en una aplicación de Xamarin. Mac para admitir operaciones de copia y pegado. En primer lugar, se presentó un ejemplo sencillo para familiarizarse con las operaciones de las tareas de área de trabajar estándar. A continuación, se tomó una visión detallada de la mesa de pegado y cómo leer y escribir datos en ella. Por último, examinaba el uso de un tipo de datos personalizado para admitir la copia y el pegado de tipos de datos complejos dentro de una aplicación.

## <a name="related-links"></a>Vínculos relacionados

- [MacCopyPaste (ejemplo)](/samples/xamarin/mac-samples/maccopypaste)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Guía de programación de la mesa de pega](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PasteboardGuide106/Articles/pbGettingStarted.html)
- [Directrices de la interfaz humana de macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)