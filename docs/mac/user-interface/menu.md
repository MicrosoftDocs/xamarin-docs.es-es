---
title: Menús en Xamarin. Mac
description: En este artículo se explica cómo trabajar con menús en una aplicación de Xamarin. Mac. Describe cómo crear y mantener menús y elementos de menú en Xcode y Interface Builder y cómo trabajar con ellos mediante programación.
ms.prod: xamarin
ms.assetid: 5D367F8E-3A76-4995-8A89-488530FAD802
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: c607e4668e4754edaec50a4c8b24776c7bb5844e
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430785"
---
# <a name="menus-in-xamarinmac"></a>Menús en Xamarin. Mac

_En este artículo se explica cómo trabajar con menús en una aplicación de Xamarin. Mac. Describe cómo crear y mantener menús y elementos de menú en Xcode y Interface Builder y cómo trabajar con ellos mediante programación._

Cuando se trabaja con C# y .NET en una aplicación de Xamarin. Mac, tiene acceso a los mismos menús de chocolate que un desarrollador que trabaja en Objective-C y Xcode. Dado que Xamarin. Mac se integra directamente con Xcode, puede usar los Interface Builder de Xcode para crear y mantener sus barras de menús, menús y elementos de menú (o, opcionalmente, crearlos directamente en código de C#).

Los menús son una parte integral de la experiencia del usuario de una aplicación Mac y suelen aparecer en varias partes de la interfaz de usuario:

- **Barra de menús de la aplicación** : este es el menú principal que aparece en la parte superior de la pantalla para cada aplicación Mac.
- **Menús contextuales** : aparecen cuando el usuario hace clic con el botón derecho o el control hace clic en un elemento de una ventana.
- **La barra de estado** es el área situada en el extremo derecho de la barra de menús de la aplicación que aparece en la parte superior de la pantalla (a la izquierda del reloj de la barra de menús) y crece a la izquierda a medida que se agregan elementos.
- **Menú acoplar** : el menú de cada aplicación del Dock que aparece cuando el usuario hace clic con el botón derecho o el control hace clic en el icono de la aplicación, o cuando el usuario hace clic en el icono y mantiene presionado el botón del mouse.
- **Botón emergente y listas desplegables** : un botón emergente muestra un elemento seleccionado y presenta una lista de opciones para seleccionar al hacer clic en el usuario. Una lista desplegable es un tipo de botón emergente que se usa normalmente para seleccionar comandos específicos del contexto de la tarea actual. Ambos pueden aparecer en cualquier parte de una ventana.

[![Un menú de ejemplo](menu-images/intro01.png "Un menú de ejemplo")](menu-images/intro01-large.png#lightbox)

En este artículo, trataremos los aspectos básicos del trabajo con barras de menús, menús y elementos de menú de coco en una aplicación de Xamarin. Mac. Se recomienda encarecidamente que trabaje primero en el artículo [Hello, Mac](~/mac/get-started/hello-mac.md) , específicamente en las secciones [Introducción a Xcode y Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) , ya que trata conceptos clave y técnicas que vamos a usar en este artículo.

Es posible que desee echar un vistazo a la sección [exponer clases y métodos de C# a Objective-C](~/mac/internals/how-it-works.md) del documento [interno de Xamarin. Mac](~/mac/internals/how-it-works.md) , en él se explican `Register` los `Export` atributos y que se usan para conectar las clases de C# a objetos de Objective-C y elementos de la interfaz de usuario.

## <a name="the-applications-menu-bar"></a>Barra de menús de la aplicación 

A diferencia de las aplicaciones que se ejecutan en el sistema operativo Windows, donde cada ventana puede tener su propia barra de menús asociada, cada aplicación que se ejecuta en macOS tiene una sola barra de menús que se ejecuta en la parte superior de la pantalla que se usa para cada ventana de la aplicación:

[![Una barra de menús](menu-images/appmenu01.png "Una barra de menús")](menu-images/appmenu01-large.png#lightbox)

Los elementos de esta barra de menús se activan o desactivan según el contexto actual o el estado de la aplicación y su interfaz de usuario en un momento dado. Por ejemplo: Si el usuario selecciona un campo de texto, los elementos del menú **edición** estarán habilitados como **copiar** y **cortar**.

Según Apple y, de forma predeterminada, todas las aplicaciones de macOS tienen un conjunto estándar de menús y elementos de menú que aparecen en la barra de menús de la aplicación:

- **Menú de Apple** : este menú proporciona acceso a los elementos de todo el sistema que están disponibles para el usuario en todo momento, independientemente de la aplicación que se esté ejecutando. Estos elementos no pueden ser modificados por el desarrollador.
- **Menú** de la aplicación: este menú muestra el nombre de la aplicación en negrita y ayuda al usuario a identificar la aplicación que se está ejecutando actualmente. Contiene elementos que se aplican a la aplicación en su totalidad y no a un documento o proceso determinado, como salir de la aplicación.
- **Menú Archivo** : elementos usados para crear, abrir o guardar documentos con los que funciona la aplicación. Si la aplicación no está basada en documentos, se puede cambiar el nombre o quitar este menú.
- **Menú Edición** : contiene comandos como **cortar**, **copiar**y **pegar** que se usan para editar o modificar elementos en la interfaz de usuario de la aplicación.
- **Menú Formato** : Si la aplicación funciona con texto, este menú contiene comandos para ajustar el formato del texto.
- **Menú Ver** : contiene comandos que afectan al modo en que se muestra (visualiza) el contenido en la interfaz de usuario de la aplicación.
- **Menús específicos** de la aplicación: son los menús específicos de la aplicación (por ejemplo, un menú de marcadores de un explorador Web). Deberían aparecer entre los menús **Ver** y **ventana** de la barra.
- **Menú ventana** : contiene comandos para trabajar con ventanas en la aplicación, así como una lista de ventanas abiertas actuales.
- **Menú Ayuda** : Si la aplicación proporciona ayuda en pantalla, el menú ayuda debe ser el menú situado más a la derecha en la barra. 

Para obtener más información sobre la barra de menús de la aplicación y los menús y elementos de menú estándar, consulte las directrices de la [interfaz humana](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)de Apple.

### <a name="the-default-application-menu-bar"></a>La barra de menús de la aplicación predeterminada

Siempre que se crea un nuevo proyecto de Xamarin. Mac, se obtiene automáticamente una barra de menús de la aplicación estándar y predeterminada que tiene los elementos típicos que una aplicación de macOS tendría normalmente (como se describe en la sección anterior). La barra de menús predeterminada de la aplicación se define en el archivo **Main. Storyboard** (junto con el resto de la interfaz de usuario de la aplicación) en el proyecto en el **Panel de solución**:  

![Seleccionar el guión gráfico principal](menu-images/appmenu02.png "Seleccionar el guión gráfico principal")

Haga doble clic en el archivo **Main. Storyboard** para abrirlo para su edición en el Interface Builder de Xcode y se mostrará la interfaz del editor de menús:

[![Edición de la interfaz de usuario en Xcode](menu-images/defaultbar01.png "Edición de la interfaz de usuario en Xcode")](menu-images/defaultbar01-large.png#lightbox)

Desde aquí, podemos hacer clic en elementos como el elemento de menú **abrir** en el menú **archivo** y editar o ajustar sus propiedades en el **Inspector de atributos**:

[![Editar los atributos de un menú](menu-images/defaultbar02.png "Editar los atributos de un menú")](menu-images/defaultbar02-large.png#lightbox)

Vamos a agregar, editar y eliminar menús y elementos más adelante en este artículo. Por ahora, solo queremos ver qué menús y elementos de menú están disponibles de forma predeterminada y cómo se han expuesto automáticamente al código mediante un conjunto de acciones y salidas predefinidas (para obtener más información, consulte nuestra documentación sobre [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) ).

Por ejemplo, si hacemos clic en el **Inspector de conexión** para el elemento de menú **abrir** , podemos ver que se conecta automáticamente a la `openDocument:` acción: 

[![Ver la acción adjunta](menu-images/defaultbar03.png "Ver la acción adjunta")](menu-images/defaultbar03-large.png#lightbox)

Si selecciona el **primer respondedor** en la **jerarquía** de la interfaz y se desplaza hacia abajo en el **Inspector de conexión**, verá la definición de la `openDocument:` acción a la que está asociado el elemento de menú **abierto** (junto con otras acciones predeterminadas para la aplicación que están y que no se conectan automáticamente a los controles):

[![Ver todas las acciones asociadas](menu-images/defaultbar04.png "Ver todas las acciones asociadas")](menu-images/defaultbar04-large.png#lightbox) 

¿Por qué es importante? En la siguiente sección verá cómo funcionan estas acciones definidas automáticamente con otros elementos de la interfaz de usuario de Cocoa para habilitar y deshabilitar automáticamente los elementos de menú, así como proporcionar funcionalidad integrada para los elementos.

Más adelante vamos a usar estas acciones integradas para habilitar y deshabilitar elementos del código y proporcionar nuestra propia funcionalidad cuando se seleccionan.

<a name="Built-In_Menu_Functionality"></a>

### <a name="built-in-menu-functionality"></a>Funcionalidad de menú integrada

Si fuera la ejecución de una aplicación de Xamarin. Mac recién creada antes de agregar cualquier elemento de interfaz de usuario o código, observará que algunos elementos se conectan automáticamente y se habilitan automáticamente (con funcionalidad totalmente integrada), como el elemento **Quit** del menú de la **aplicación** :

![Un elemento de menú habilitado](menu-images/appmenu03.png "Un elemento de menú habilitado")

Mientras que otros elementos de menú, como **cortar**, **copiar**y **pegar** , no son:

![Elementos de menú deshabilitados](menu-images/appmenu04.png "Elementos de menú deshabilitados")

Detenga la aplicación y haga doble clic en el archivo **Main. Storyboard** en el **Panel de solución** para abrirlo para su edición en el Interface Builder de Xcode. A continuación, arrastre una **vista de texto** desde la **biblioteca** hasta el controlador de vista de la ventana en el editor de la **interfaz**:

[![Seleccionar una vista de texto de la biblioteca](menu-images/appmenu05.png "Seleccionar una vista de texto de la biblioteca")](menu-images/appmenu05-large.png#lightbox)

En el **Editor de restricciones** , vamos a anclar la vista de texto a los bordes de la ventana y establecerla en la que crece y se reduce con la ventana haciendo clic en los cuatro fotopuestos rojos en la parte superior del editor y haciendo clic en el botón **Agregar 4 restricciones** :

[![Editar las restricciones](menu-images/appmenu06.png "Editar las restricciones")](menu-images/appmenu06-large.png#lightbox)

Guarde los cambios en el diseño de la interfaz de usuario y vuelva a cambiar el Visual Studio para Mac para sincronizar los cambios con el proyecto de Xamarin. Mac. Ahora, inicie la aplicación, escriba texto en la vista de texto, selecciónelo y abra el menú **edición** :

![Los elementos de menú se habilitan o deshabilitan automáticamente](menu-images/appmenu07.png "Los elementos de menú se habilitan o deshabilitan automáticamente")

Observe cómo los elementos **cortar**, **copiar**y **pegar** se habilitan automáticamente y son totalmente funcionales, todo ello sin escribir una sola línea de código. 

¿Qué ocurre aquí? Recuerde las acciones predefinidas integradas que se conectan a los elementos de menú predeterminados (como se indicó anteriormente), la mayoría de los elementos de la interfaz de usuario de coco que forman parte de macOS han integrado enlaces a acciones específicas (como `copy:` ). De modo que, cuando se agregan a una ventana, activa y selecciona, se habilitan automáticamente el elemento de menú correspondiente o los elementos adjuntos a esa acción. Si el usuario selecciona ese elemento de menú, se llama a la funcionalidad integrada en el elemento de la interfaz de usuario y se ejecuta sin intervención del desarrollador.

### <a name="enabling-and-disabling-menus-and-items"></a>Habilitar y deshabilitar menús y elementos

De forma predeterminada, cada vez que se produce un evento de usuario, `NSMenu` habilita y deshabilita automáticamente todos los menús y elementos de menú visibles según el contexto de la aplicación. Hay tres maneras de habilitar o deshabilitar un elemento:

- **Habilitación automática de menús** : un elemento de menú está habilitado si `NSMenu` puede encontrar un objeto adecuado que responde a la acción a la que se conecta el elemento. Por ejemplo, la vista de texto anterior que tenía un enlace integrado a la `copy:` acción.
- **Acciones personalizadas y validateMenuItem:** -para cualquier elemento de menú que esté enlazado a una [acción personalizada de ventana o controlador de vista](#Working-with-Custom-Window-Actions), puede Agregar la `validateMenuItem:` acción y habilitar o deshabilitar manualmente los elementos de menú.
- **Menú manual habilitando** : se establece manualmente la `Enabled` propiedad de cada `NSMenuItem` para habilitar o deshabilitar cada elemento en un menú individualmente.

Para elegir un sistema, establezca la `AutoEnablesItems` propiedad de `NSMenu` . `true` es automático (comportamiento predeterminado) y `false` es manual. 

> [!IMPORTANT]
> Si decide usar la habilitación manual de menús, ninguno de los elementos de menú, ni siquiera los controlados por clases AppKit como `NSTextView` , se actualiza automáticamente. Será responsable de habilitar y deshabilitar todos los elementos manualmente en el código.

#### <a name="using-validatemenuitem"></a>Usar validateMenuItem

Como se indicó anteriormente, para cualquier elemento de menú que esté enlazado a una [acción personalizada de ventana o controlador de vista](#Working-with-Custom-Window-Actions), puede Agregar la `validateMenuItem:` acción y habilitar o deshabilitar manualmente los elementos de menú.

En el ejemplo siguiente, la `Tag` propiedad se utilizará para decidir el tipo de elemento de menú que la acción habilitará o deshabilitará `validateMenuItem:` según el estado del texto seleccionado en `NSTextView` . La `Tag` propiedad se ha establecido en Interface Builder para cada elemento de menú:

![Establecer la propiedad Tag](menu-images/validate01.png "Establecer la propiedad Tag")

Y el código siguiente agregado al controlador de vistas:

```csharp
[Action("validateMenuItem:")]
public bool ValidateMenuItem (NSMenuItem item) {

    // Take action based on the menu item type
    // (As specified in its Tag)
    switch (item.Tag) {
    case 1:
        // Wrap menu items should only be available if
        // a range of text is selected
        return (TextEditor.SelectedRange.Length > 0);
    case 2:
        // Quote menu items should only be available if
        // a range is NOT selected.
        return (TextEditor.SelectedRange.Length == 0);
    }

    return true;
}
```

Cuando se ejecuta este código, y no se selecciona ningún texto en la `NSTextView` , los dos elementos de menú de ajuste están deshabilitados (aunque estén conectados a acciones en el controlador de vista):

![Mostrar elementos deshabilitados](menu-images/validate02.png "Mostrar elementos deshabilitados")

Si se selecciona una sección de texto y se vuelve a abrir el menú, los dos elementos de menú de ajuste estarán disponibles:

![Mostrar elementos habilitados](menu-images/validate03.png "Mostrar elementos habilitados")

## <a name="enabling-and-responding-to-menu-items-in-code"></a>Habilitar y responder a los elementos de menú en el código

Como hemos mencionado anteriormente, simplemente agregando determinados elementos de la interfaz de usuario de coco a nuestro diseño de la interfaz de usuario (por ejemplo, un campo de texto), algunos de los elementos de menú predeterminados se habilitarán y funcionarán automáticamente, sin tener que escribir ningún código. A continuación, echemos un vistazo a cómo agregar nuestro propio código C# a nuestro proyecto de Xamarin. Mac para habilitar un elemento de menú y proporcionar la funcionalidad cuando el usuario lo selecciona.

Por ejemplo, supongamos que queremos que el usuario pueda usar el elemento **abrir** en el menú **archivo** para seleccionar una carpeta. Dado que queremos que sea una función de toda la aplicación y no se limite a un elemento de interfaz de usuario o de ventana, vamos a agregar el código para controlar esto a nuestro delegado de aplicación.

En el **Panel de solución**, haga doble clic en el `AppDelegate.CS` archivo para abrirlo para su edición:

![Seleccionar el delegado de la aplicación](menu-images/appmenu08.png "Seleccionar el delegado de la aplicación")

Agregue el código siguiente al método `DidFinishLaunching`:

```csharp
[Export ("openDocument:")]
void OpenDialog (NSObject sender)
{
    var dlg = NSOpenPanel.OpenPanel;
    dlg.CanChooseFiles = false;
    dlg.CanChooseDirectories = true;

    if (dlg.RunModal () == 1) {
        var alert = new NSAlert () {
            AlertStyle = NSAlertStyle.Informational,
            InformativeText = "At this point we should do something with the folder that the user just selected in the Open File Dialog box...",
            MessageText = "Folder Selected"
        };
        alert.RunModal ();
    }
}
```

Vamos a ejecutar la aplicación ahora y abrir el menú **archivo** : 

![Menú Archivo](menu-images/appmenu09.png "Menú Archivo")

Observe que el elemento de menú **abrir** está ahora habilitado. Si se selecciona, se mostrará el cuadro de diálogo Abrir:

![Un cuadro de diálogo abierto](menu-images/appmenu10.png "Un cuadro de diálogo abierto")

Si hacemos clic en el botón **abrir** , se mostrará el mensaje de alerta:

![Un mensaje de cuadro de diálogo de ejemplo](menu-images/appmenu11.png "Un mensaje de cuadro de diálogo de ejemplo")

La línea clave aquí era `[Export ("openDocument:")]` , indica `NSMenu` que **AppDelegate** tiene un método `void OpenDialog (NSObject sender)` que responde a la `openDocument:` acción. Si recuerda lo anterior, el elemento de menú **abrir** se conecta automáticamente a esta acción de forma predeterminada en Interface Builder:

[![Ver las acciones adjuntas](menu-images/defaultbar03.png "Ver las acciones adjuntas")](menu-images/defaultbar03-large.png#lightbox)

A continuación, veremos cómo crear nuestro propio menú, elementos de menú y acciones y responder a ellos en el código.

### <a name="working-with-the-open-recent-menu"></a>Trabajar con el menú Abrir recientes

De forma predeterminada, el menú **archivo** contiene un elemento **abierto reciente** que realiza un seguimiento de los últimos archivos que el usuario ha abierto con la aplicación. Si va a crear una `NSDocument` aplicación de Xamarin. Mac basada en, este menú se administrará automáticamente. Para cualquier otro tipo de aplicación de Xamarin. Mac, será responsable de administrar y responder a este elemento de menú manualmente.

Para controlar manualmente el menú **Abrir recientes** , primero tendrá que informarle de que se ha abierto o guardado un nuevo archivo con lo siguiente:

```csharp
// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

Aunque la aplicación no usa `NSDocuments` , todavía se usa `NSDocumentController` para mantener el menú **Abrir recientes** mediante el envío de un `NSUrl` con la ubicación del archivo al `NoteNewRecentDocumentURL` método de `SharedDocumentController` .

A continuación, debe invalidar el `OpenFile` método del delegado de la aplicación para abrir cualquier archivo que el usuario seleccione en el menú **Abrir recientes** . Por ejemplo:

```csharp
public override bool OpenFile (NSApplication sender, string filename)
{
    // Trap all errors
    try {
        filename = filename.Replace (" ", "%20");
        var url = new NSUrl ("file://"+filename);
        return OpenFile(url);
    } catch {
        return false;
    }
}
```

Devuelve `true` si se puede abrir el archivo; de lo contrario, devuelve `false` y se mostrará una advertencia integrada al usuario que indica que no se pudo abrir el archivo.

Dado que el nombre de archivo y la ruta de acceso que se devuelven desde el menú **Abrir recientes** , pueden incluir un espacio, es necesario que se escape correctamente en este carácter antes de crear un `NSUrl` o se producirá un error. Lo hacemos con el código siguiente:

```csharp
filename = filename.Replace (" ", "%20");
```

Por último, creamos un `NSUrl` que apunta al archivo y usamos un método auxiliar en el delegado de la aplicación para abrir una nueva ventana y cargar el archivo en ella:

```csharp
var url = new NSUrl ("file://"+filename);
return OpenFile(url);
```

Para extraer todo, echemos un vistazo a una implementación de ejemplo en un archivo **AppDelegate.CS** :

```csharp
using AppKit;
using Foundation;
using System.IO;
using System;

namespace MacHyperlink
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewWindowNumber { get; set;} = -1;
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }

        public override bool OpenFile (NSApplication sender, string filename)
        {
            // Trap all errors
            try {
                filename = filename.Replace (" ", "%20");
                var url = new NSUrl ("file://"+filename);
                return OpenFile(url);
            } catch {
                return false;
            }
        }
        #endregion

        #region Private Methods
        private bool OpenFile(NSUrl url) {
            var good = false;

            // Trap all errors
            try {
                var path = url.Path;

                // Is the file already open?
                for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
                    var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
                    if (content != null && path == content.FilePath) {
                        // Bring window to front
                        NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
                        return true;
                    }
                }

                // Get new window
                var storyboard = NSStoryboard.FromName ("Main", null);
                var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

                // Display
                controller.ShowWindow(this);

                // Load the text into the window
                var viewController = controller.Window.ContentViewController as ViewController;
                viewController.Text = File.ReadAllText(path);
                viewController.SetLanguageFromPath(path);
                viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
                viewController.View.Window.RepresentedUrl = url;

                // Add document to the Open Recent menu
                NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);

                // Make as successful
                good = true;
            } catch {
                // Mark as bad file on error
                good = false;
            }

            // Return results
            return good;
        }
        #endregion

        #region actions
        [Export ("openDocument:")]
        void OpenDialog (NSObject sender)
        {
            var dlg = NSOpenPanel.OpenPanel;
            dlg.CanChooseFiles = true;
            dlg.CanChooseDirectories = false;

            if (dlg.RunModal () == 1) {
                // Nab the first file
                var url = dlg.Urls [0];

                if (url != null) {
                    // Open the document in a new window
                    OpenFile (url);
                }
            }
        }
        #endregion
    }
}
```

En función de los requisitos de la aplicación, es posible que no desee que el usuario abra el mismo archivo en varias ventanas al mismo tiempo. En nuestra aplicación de ejemplo, si el usuario elige un archivo que ya está abierto (ya sea desde el abrir **reciente** o **abierto.** elementos de menú), la ventana que contiene el archivo se traslada al principio.

Para ello, usamos el siguiente código en nuestro método auxiliar:

```csharp
var path = url.Path;

// Is the file already open?
for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
    var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
    if (content != null && path == content.FilePath) {
        // Bring window to front
        NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
        return true;
    }
}
```

Hemos diseñado nuestra `ViewController` clase para que contenga la ruta de acceso al archivo en su `Path` propiedad. A continuación, se recorren todas las ventanas abiertas actualmente en la aplicación. Si el archivo ya está abierto en una de las ventanas, se pasa al principio de todas las demás ventanas mediante:

```csharp
NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
```

Si no se encuentra ninguna coincidencia, se abre una nueva ventana con el archivo cargado y el archivo se indica en el menú **Abrir recientes** :

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);

// Load the text into the window
var viewController = controller.Window.ContentViewController as ViewController;
viewController.Text = File.ReadAllText(path);
viewController.SetLanguageFromPath(path);
viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
viewController.View.Window.RepresentedUrl = url;

// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

<a name="Working-with-Custom-Window-Actions"></a>

### <a name="working-with-custom-window-actions"></a>Trabajar con acciones de ventana personalizadas

Al igual que las acciones del **primer respondedor** integradas que se incorporan previamente a los elementos de menú estándar, puede crear nuevas acciones personalizadas y conectarlas a los elementos de menú en Interface Builder.

En primer lugar, defina una acción personalizada en uno de los controladores de ventanas de la aplicación. Por ejemplo:

```csharp
[Action("defineKeyword:")]
public void defineKeyword (NSObject sender) {
    // Preform some action when the menu is selected
    Console.WriteLine ("Request to define keyword");
}
```

A continuación, haga doble clic en el archivo de guion gráfico de la aplicación en el **Panel de solución** para abrirlo para su edición en el Interface Builder de Xcode. Seleccione el **primer respondedor** en la **escena**de la aplicación y, a continuación, cambie al **Inspector de atributos**:

![Inspector de atributos](menu-images/action01.png "Inspector de atributos")

Haga clic en el **+** botón situado en la parte inferior del **Inspector de atributos** para agregar una nueva acción personalizada:

![Agregar una nueva acción](menu-images/action02.png "Agregar una nueva acción")

Asígnele el mismo nombre que la acción personalizada que creó en el controlador de ventana:

![Editar el nombre de la acción](menu-images/action03.png "Editar el nombre de la acción")

Control: haga clic y arrastre de un elemento de menú al **primer respondedor** en la escena de la **aplicación**. En la lista emergente, seleccione la nueva acción que acaba de crear ( `defineKeyword:` en este ejemplo):

![Adjuntar una acción](menu-images/action04.png "Adjuntar una acción")

Guarde los cambios en el guion gráfico y vuelva a Visual Studio para Mac para sincronizar los cambios. Si ejecuta la aplicación, el elemento de menú al que ha conectado la acción personalizada se habilitará o deshabilitará automáticamente (basándose en la ventana con la acción que se va a abrir) y seleccionando el elemento de menú se activará la acción:

[![Probar la nueva acción](menu-images/action05.png "Probar la nueva acción")](menu-images/action05-large.png#lightbox)

<a name="Adding,_Editing_and_Deleting_Menus"></a>

### <a name="adding-editing-and-deleting-menus"></a>Agregar, editar y eliminar menús

Como hemos notado en las secciones anteriores, una aplicación de Xamarin. Mac incluye un número preestablecido de menús y elementos de menú predeterminados en los que los controles de interfaz de usuario específicos se activarán y responderán automáticamente. También hemos aprendido a agregar código a la aplicación que también habilitará y responderá a estos elementos predeterminados.

En esta sección, veremos cómo quitar los elementos de menú que no necesitamos, reorganizar los menús y agregar nuevos menús, elementos de menú y acciones.

Haga doble clic en el archivo **Main. Storyboard** en el **Panel de solución** para abrirlo para su edición:

[![Edición de la interfaz de usuario en Xcode](menu-images/maint01.png "Edición de la interfaz de usuario en Xcode")](menu-images/maint01-large.png#lightbox)

Para nuestra aplicación de Xamarin. Mac específica, no vamos a usar el menú **vista** predeterminada, por lo que vamos a quitarlo. En la **jerarquía** de la interfaz, seleccione el elemento de menú **Ver** que forma parte de la barra de menús principal:

![Seleccionar el elemento de menú Ver](menu-images/maint02.png "Seleccionar el elemento de menú Ver")

Presione SUPR o retroceso para eliminar el menú. A continuación, no vamos a usar todos los elementos del menú **formato** y queremos trasladar los elementos que vamos a usar en los submenús. En la **jerarquía** de la interfaz, seleccione los siguientes elementos de menú:

![Resaltar varios elementos](menu-images/maint03.png "Resaltar varios elementos")

Arrastre los elementos en el **menú** primario desde el submenú en el que se encuentran actualmente:

[![Arrastrar elementos de menú al menú primario](menu-images/maint04.png "Arrastrar elementos de menú al menú primario")](menu-images/maint04-large.png#lightbox)

El menú debería tener ahora el siguiente aspecto:

[![Los elementos de la nueva ubicación](menu-images/maint05.png "Los elementos de la nueva ubicación")](menu-images/maint05-large.png#lightbox)

A continuación, vamos a arrastrar el submenú de **texto** fuera del menú **formato** y colocarlo en la barra de menús principal entre los menús **formato** y **ventana** :

[![Menú de texto](menu-images/maint06.png "Menú de texto")](menu-images/maint06-large.png#lightbox)

Vuelva al menú **formato** y elimine el elemento de submenú **fuente** . A continuación, seleccione el menú **formato** y cambie su nombre por "fuente":

[![Menú fuente](menu-images/maint07.png "Menú fuente")](menu-images/maint07-large.png#lightbox)

A continuación, vamos a crear un menú personalizado de frases predefinidas que se anexarán automáticamente al texto en la vista de texto cuando estén seleccionadas. En el cuadro de búsqueda de la parte inferior del **Inspector de biblioteca** , escriba "menú". Esto hará que sea más fácil buscar y trabajar con todos los elementos de la interfaz de usuario de menú:

![Inspector de biblioteca](menu-images/maint08.png "Inspector de biblioteca")

Ahora vamos a hacer lo siguiente para crear el menú:

1. Arrastre un **elemento de menú** desde el **Inspector de biblioteca** hasta la barra de menús entre los menús **texto** y **ventana** : 

    ![Seleccionar un nuevo elemento de menú en la biblioteca](menu-images/maint10.png "Seleccionar un nuevo elemento de menú en la biblioteca")
2. Cambie el nombre del elemento "frases": 

    [![Establecer el nombre del menú](menu-images/maint09.png "Establecer el nombre del menú")](menu-images/maint09-large.png#lightbox)
3. A continuación, arrastre un **menú** desde el **Inspector de biblioteca**: 

    ![Seleccionar un menú de la biblioteca](menu-images/maint11.png "Seleccionar un menú de la biblioteca")
4. Coloque el **menú** en el nuevo **elemento de menú** que acabamos de crear y cambie su nombre a "frases": 

    [![Edición del nombre del menú](menu-images/maint12.png "Edición del nombre del menú")](menu-images/maint12-large.png#lightbox)
5. Ahora, vamos a cambiar el nombre de los tres **elementos de menú** predeterminados "address", "date" y "GREETING": 

    [![El menú frases](menu-images/maint13.png "El menú frases")](menu-images/maint13-large.png#lightbox)
6. Vamos a agregar un cuarto **elemento de menú** arrastrando un **elemento de menú** desde el **Inspector de biblioteca** y llamándolo "Signature": 

    [![Edición del nombre del elemento de menú](menu-images/maint14.png "Edición del nombre del elemento de menú")](menu-images/maint14-large.png#lightbox)
7. Guarde los cambios en la barra de menús.

Ahora vamos a crear un conjunto de acciones personalizadas para que los nuevos elementos de menú se expongan al código de C#. En Xcode, vamos a cambiar a la vista del **Asistente** :

[![Crear las acciones necesarias](menu-images/maint15.png "Crear las acciones necesarias")](menu-images/maint15-large.png#lightbox)

Vamos a hacer lo siguiente:

1. Control: arrastre desde el elemento de menú **Dirección** hasta el archivo **AppDelegate. h** .
2. Cambie el tipo de **conexión** a **acción**: 

    [![Seleccionar el tipo de acción](menu-images/maint17.png "Seleccionar el tipo de acción")](menu-images/maint17-large.png#lightbox)
3. Escriba el **nombre** "phraseAddress" y presione el botón **conectar** para crear la nueva acción: 

    [![Configurar la acción](menu-images/maint18.png "Configurar la acción")](menu-images/maint18-large.png#lightbox)
4. Repita los pasos anteriores para los elementos de menú **fecha**, **saludo**y **firma** : 

    [![Acciones completadas](menu-images/maint19.png "Acciones completadas")](menu-images/maint19-large.png#lightbox)
5. Guarde los cambios en la barra de menús.

A continuación, es necesario crear una salida para nuestra vista de texto, de modo que podamos ajustar su contenido desde el código. Seleccione el archivo **ViewController. h** en el **Editor del asistente** y cree una nueva salida llamada `documentText` :

[![Creación de una salida](menu-images/maint20.png "Creación de una salida")](menu-images/maint20-large.png#lightbox)

Vuelva a Visual Studio para Mac para sincronizar los cambios desde Xcode. A continuación, edite el archivo **ViewController.CS** y haga que tenga el aspecto siguiente:

```csharp
using System;

using AppKit;
using Foundation;

namespace MacMenus
{
    public partial class ViewController : NSViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public override NSObject RepresentedObject {
            get {
                return base.RepresentedObject;
            }
            set {
                base.RepresentedObject = value;
                // Update the view, if already loaded.
            }
        }

        public string Text {
            get { return documentText.Value; }
            set { documentText.Value = value; }
        } 
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any additional setup after loading the view.
        }

        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            App.textEditor = this;
        }

        public override void ViewWillDisappear ()
        {
            base.ViewDidDisappear ();

            App.textEditor = null;
        }
        #endregion
    }
}
```

Esto expone el texto de nuestra vista de texto fuera de la `ViewController` clase e informa al delegado de la aplicación cuando la ventana gana o pierde el foco. Ahora, edite el archivo **AppDelegate.CS** y haga que tenga un aspecto similar al siguiente:

```csharp
using AppKit;
using Foundation;
using System;

namespace MacMenus
{
    [Register ("AppDelegate")]
    public partial class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public ViewController textEditor { get; set;} = null;
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
        #endregion

        #region Custom actions
        [Export ("openDocument:")]
        void OpenDialog (NSObject sender)
        {
            var dlg = NSOpenPanel.OpenPanel;
            dlg.CanChooseFiles = false;
            dlg.CanChooseDirectories = true;

            if (dlg.RunModal () == 1) {
                var alert = new NSAlert () {
                    AlertStyle = NSAlertStyle.Informational,
                    InformativeText = "At this point we should do something with the folder that the user just selected in the Open File Dialog box...",
                    MessageText = "Folder Selected"
                };
                alert.RunModal ();
            }
        }

        partial void phrasesAddress (Foundation.NSObject sender) {

            textEditor.Text += "Xamarin HQ\n394 Pacific Ave, 4th Floor\nSan Francisco CA 94111\n\n";
        }

        partial void phrasesDate (Foundation.NSObject sender) {

            textEditor.Text += DateTime.Now.ToString("D");
        }

        partial void phrasesGreeting (Foundation.NSObject sender) {

            textEditor.Text += "Dear Sirs,\n\n";
        }

        partial void phrasesSignature (Foundation.NSObject sender) {

            textEditor.Text += "Sincerely,\n\nKevin Mullins\nXamarin,Inc.\n";
        }
        #endregion
    }
}
```

Aquí hemos realizado `AppDelegate` una clase parcial para que podamos usar las acciones y las salidas que definimos en Interface Builder. También exponemos un `textEditor` para realizar un seguimiento de qué ventana está actualmente en el foco.

Los métodos siguientes se usan para administrar los elementos de menú y menú personalizados:

```csharp
partial void phrasesAddress (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Xamarin HQ\n394 Pacific Ave, 4th Floor\nSan Francisco CA 94111\n\n";
}

partial void phrasesDate (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += DateTime.Now.ToString("D");
}

partial void phrasesGreeting (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Dear Sirs,\n\n";
}

partial void phrasesSignature (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Sincerely,\n\nKevin Mullins\nXamarin,Inc.\n";
}
```

Ahora, Si ejecutamos la aplicación, todos los elementos del menú **frase** estarán activos y agregarán la frase dar a la vista de texto cuando se seleccione:

![Un ejemplo de la aplicación en ejecución](menu-images/maint21.png "Un ejemplo de la aplicación en ejecución")

Ahora que tenemos los aspectos básicos de trabajar con la barra de menús de la aplicación, echemos un vistazo a la creación de un menú contextual personalizado.

### <a name="creating-menus-from-code"></a>Crear menús desde código

Además de crear menús y elementos de menú con Interface Builder de Xcode, puede haber ocasiones en las que una aplicación de Xamarin. Mac necesite crear, modificar o quitar un menú, un submenú o un elemento de menú del código.

En el ejemplo siguiente, se crea una clase para que contenga la información sobre los elementos de menú y submenús que se crearán dinámicamente sobre la marcha:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace AppKit.TextKit.Formatter
{
    public class LanguageFormatCommand : NSObject
    {
        #region Computed Properties
        public string Title { get; set; } = "";
        public string Prefix { get; set; } = "";
        public string Postfix { get; set; } = "";
        public List<LanguageFormatCommand> SubCommands { get; set; } = new List<LanguageFormatCommand>();
        #endregion

        #region Constructors
        public LanguageFormatCommand () {

        }

        public LanguageFormatCommand (string title)
        {
            // Initialize
            this.Title = title;
        }

        public LanguageFormatCommand (string title, string prefix)
        {
            // Initialize
            this.Title = title;
            this.Prefix = prefix;
        }

        public LanguageFormatCommand (string title, string prefix, string postfix)
        {
            // Initialize
            this.Title = title;
            this.Prefix = prefix;
            this.Postfix = postfix;
        }
        #endregion
    }
}
```

#### <a name="adding-menus-and-items"></a>Agregar menús y elementos

Con esta clase definida, la rutina siguiente analizará una colección de `LanguageFormatCommand` objetos y compilará de forma recursiva nuevos menús y elementos de menú si los anexa a la parte inferior del menú existente (creado en Interface Builder) que se ha pasado:

```csharp
private void AssembleMenu(NSMenu menu, List<LanguageFormatCommand> commands) {
    NSMenuItem menuItem;

    // Add any formatting commands to the Formatting menu
    foreach (LanguageFormatCommand command in commands) {
        // Add separator or item?
        if (command.Title == "") {
            menuItem = NSMenuItem.SeparatorItem;
        } else {
            menuItem = new NSMenuItem (command.Title);

            // Submenu?
            if (command.SubCommands.Count > 0) {
                // Yes, populate submenu
                menuItem.Submenu = new NSMenu (command.Title);
                AssembleMenu (menuItem.Submenu, command.SubCommands);
            } else {
                // No, add normal menu item
                menuItem.Activated += (sender, e) => {
                    // Apply the command on the selected text
                    TextEditor.PerformFormattingCommand (command);
                };
            }
        }
        menu.AddItem (menuItem);
    }
}
``` 

En el caso de cualquier `LanguageFormatCommand` objeto que tenga una propiedad en blanco `Title` , esta rutina crea un **elemento de menú separador** (una línea fina gris) entre las secciones de menú:

```csharp
menuItem = NSMenuItem.SeparatorItem;
```

Si se proporciona un título, se crea un nuevo elemento de menú con ese título:

```csharp
menuItem = new NSMenuItem (command.Title);
``` 

Si el `LanguageFormatCommand` objeto contiene `LanguageFormatCommand` objetos secundarios, se crea un submenú y el `AssembleMenu` método se llama de forma recursiva para compilar ese menú:

```csharp
menuItem.Submenu = new NSMenu (command.Title);
AssembleMenu (menuItem.Submenu, command.SubCommands);
```

Para cualquier elemento de menú nuevo que no tenga menús secundarios, se agrega código para controlar el elemento de menú seleccionado por el usuario:

```csharp
menuItem.Activated += (sender, e) => {
    // Do something when the menu item is selected
    ...
};
```

#### <a name="testing-the-menu-creation"></a>Probar la creación de menús

Con todo el código anterior en su lugar, si se creó la siguiente colección de `LanguageFormatCommand` objetos:

```csharp
// Define formatting commands
FormattingCommands.Add(new LanguageFormatCommand("Strong","**","**"));
FormattingCommands.Add(new LanguageFormatCommand("Emphasize","_","_"));
FormattingCommands.Add(new LanguageFormatCommand("Inline Code","`","`"));
FormattingCommands.Add(new LanguageFormatCommand("Code Block","```\n","\n```"));
FormattingCommands.Add(new LanguageFormatCommand("Comment","<!--","-->"));
FormattingCommands.Add (new LanguageFormatCommand ());
FormattingCommands.Add(new LanguageFormatCommand("Unordered List","* "));
FormattingCommands.Add(new LanguageFormatCommand("Ordered List","1. "));
FormattingCommands.Add(new LanguageFormatCommand("Block Quote","> "));
FormattingCommands.Add (new LanguageFormatCommand ());

var Headings = new LanguageFormatCommand ("Headings");
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 1","# "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 2","## "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 3","### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 4","#### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 5","##### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 6","###### "));
FormattingCommands.Add (Headings);

FormattingCommands.Add(new LanguageFormatCommand ());
FormattingCommands.Add(new LanguageFormatCommand("Link","[","]()"));
FormattingCommands.Add(new LanguageFormatCommand("Image","![](",")"));
FormattingCommands.Add(new LanguageFormatCommand("Image Link","[![](",")](LinkImageHere)"));
```

Y esa colección pasada a la `AssembleMenu` función (con el menú **formato** establecido como base), se crearán los siguientes menús dinámicos y elementos de menú:

![Los nuevos elementos de menú en la aplicación en ejecución](menu-images/dynamic01.png "Los nuevos elementos de menú en la aplicación en ejecución")

#### <a name="removing-menus-and-items"></a>Quitar menús y elementos

Si necesita quitar cualquier menú o elemento de menú de la interfaz de usuario de la aplicación, puede usar el `RemoveItemAt` método de la `NSMenu` clase simplemente asignándole el índice de base cero del elemento que se va a quitar.

Por ejemplo, para quitar los menús y los elementos de menú creados por la rutina anterior, podría usar el código siguiente:

```csharp
public void UnpopulateFormattingMenu(NSMenu menu) {

    // Remove any additional items
    for (int n = (int)menu.Count - 1; n > 4; --n) {
        menu.RemoveItemAt (n);
    }
}
```

En el caso del código anterior, los cuatro primeros elementos de menú se crean en los Interface Builder de Xcode y están disponibles en la aplicación, por lo que no se quitan dinámicamente.

<a name="Contextual_Menus"></a>

## <a name="contextual-menus"></a>Menús contextuales

Los menús contextuales aparecen cuando el usuario hace clic con el botón derecho o el control hace clic en un elemento de una ventana. De forma predeterminada, algunos de los elementos de la interfaz de usuario integrados en macOS ya tienen menús contextuales asociados (por ejemplo, la vista de texto). Sin embargo, puede haber ocasiones en las que deseemos crear sus propios menús contextuales personalizados para un elemento de la interfaz de usuario que se haya agregado a una ventana.

Vamos a editar el archivo **Main. Storyboard** en Xcode y agregaremos una ventana de **ventana** a nuestro diseño, estableceremos su **clase** en "NSPanel" en el **Inspector de identidad**, agregaremos un nuevo elemento de **Asistente** al menú **ventana** y adjuntarlo a la ventana nueva mediante una **presentación segue**:

[![Establecimiento del tipo segue](menu-images/context01.png "Establecimiento del tipo segue")](menu-images/context01-large.png#lightbox)

Vamos a hacer lo siguiente:

1. Arrastre una **etiqueta** desde el **Inspector de biblioteca** hasta la ventana **Panel** y establezca su texto en "propiedad": 

    [![Editar el valor de la etiqueta](menu-images/context03.png "Editar el valor de la etiqueta")](menu-images/context03-large.png#lightbox)
2. A continuación, arrastre un **menú** desde el **Inspector de biblioteca** hasta el controlador de vista en la jerarquía de vistas y cambie el nombre de los tres elementos de menú predeterminados **documento**, **texto** y **fuente**:

    [![Los elementos de menú requeridos](menu-images/context02.png "Los elementos de menú requeridos")](menu-images/context02-large.png#lightbox)
3. Ahora, arrastre el control desde la **etiqueta de propiedad** hasta el **menú**:

    [![Arrastrar para crear un segue](menu-images/context04.png "Arrastrar para crear un segue")](menu-images/context04-large.png#lightbox)
4. En el cuadro de diálogo emergente, seleccione **menú**: 

    ![Establecimiento del tipo segue](menu-images/context05.png "Establecimiento del tipo segue")
5. En el **Inspector de identidad**, establezca la clase del controlador de vista en "PanelViewController": 

    [![Establecimiento de la clase segue](menu-images/context10.png "Establecimiento de la clase segue")](menu-images/context10-large.png#lightbox)
6. Cambie de nuevo a Visual Studio para Mac para sincronizar y, a continuación, vuelva a Interface Builder.
7. Cambie al **Editor del asistente** y seleccione el archivo **PanelViewController. h** .
8. Cree una acción para el elemento de menú de **documento** llamado `propertyDocument` : 

    [![Configurar la acción](menu-images/context06.png "Configurar la acción")](menu-images/context06-large.png#lightbox)
9. Repita la creación de acciones para los elementos de menú restantes: 

    [![Las acciones necesarias](menu-images/context07.png "Las acciones necesarias")](menu-images/context07-large.png#lightbox)
10. Por último, cree una salida para la **etiqueta de propiedad** denominada `propertyLabel` : 

    [![Configuración de la salida](menu-images/context08.png "Configuración de la salida")](menu-images/context08-large.png#lightbox)
11. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Edite el archivo **PanelViewController.CS** y agregue el código siguiente:

```csharp
partial void propertyDocument (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Document";
}

partial void propertyFont (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Font";
}

partial void propertyText (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Text";
}
```

Ahora, Si ejecutamos la aplicación y hacemos clic con el botón derecho en la etiqueta de propiedad en el panel, veremos el menú contextual personalizado. Si seleccionamos y item en el menú, el valor de la etiqueta cambiará:

![Menú contextual en ejecución](menu-images/context09.png "Menú contextual en ejecución")

A continuación, echemos un vistazo a la creación de menús de barra de estado.

## <a name="status-bar-menus"></a>Menús de la barra de estado

Los menús de la barra de estado muestran una colección de elementos de menú de estado que proporcionan interacción o comentarios al usuario, como un menú o una imagen que refleja el estado de una aplicación. El menú de la barra de estado de una aplicación está habilitado y activo aunque la aplicación se ejecute en segundo plano. La barra de estado de todo el sistema se encuentra en el lado derecho de la barra de menús de la aplicación y es la única barra de estado disponible actualmente en macOS.

Vamos a editar el archivo **AppDelegate.CS** y hacer que el método tenga el `DidFinishLaunching` aspecto siguiente:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Create a status bar menu
    NSStatusBar statusBar = NSStatusBar.SystemStatusBar;

    var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);
    item.Title = "Text";
    item.HighlightMode = true;
    item.Menu = new NSMenu ("Text");

    var address = new NSMenuItem ("Address");
    address.Activated += (sender, e) => {
        PhraseAddress(address);
    };
    item.Menu.AddItem (address);

    var date = new NSMenuItem ("Date");
    date.Activated += (sender, e) => {
        PhraseDate(date);
    };
    item.Menu.AddItem (date);

    var greeting = new NSMenuItem ("Greeting");
    greeting.Activated += (sender, e) => {
        PhraseGreeting(greeting);
    };
    item.Menu.AddItem (greeting);

    var signature = new NSMenuItem ("Signature");
    signature.Activated += (sender, e) => {
        PhraseSignature(signature);
    };
    item.Menu.AddItem (signature);
}
```

`NSStatusBar statusBar = NSStatusBar.SystemStatusBar;` nos permite acceder a la barra de estado de todo el sistema. `var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);` crea un nuevo elemento de la barra de estado. Desde allí, se crea un menú y varios elementos de menú y se adjunta el menú al elemento de la barra de estado que se acaba de crear. 

Si ejecutamos la aplicación, se mostrará el nuevo elemento de la barra de estado. Al seleccionar un elemento en el menú, cambiará el texto en la vista de texto: 

![Menú de la barra de estado en ejecución](menu-images/statusbar01.png "Menú de la barra de estado en ejecución")

A continuación, echemos un vistazo a la creación de elementos de menú de Dock personalizados.

## <a name="custom-dock-menus"></a>Menús de Dock personalizados

Cuando el usuario hace clic con el botón secundario del mouse en el icono de la aplicación en el Dock, aparece el menú acoplar en la aplicación Mac.

![Menú acoplar personalizado](menu-images/dock01.png "Menú acoplar personalizado")

Vamos a crear un menú acoplable personalizado para nuestra aplicación haciendo lo siguiente:

1. En Visual Studio para Mac, haga clic con el botón derecho en el proyecto de la aplicación y seleccione **Agregar**  >  **nuevo archivo..** . En el cuadro de diálogo nuevo archivo, seleccione la definición de interfaz vacía de **Xamarin. Mac**  >  **Empty Interface Definition**, use "DockMenu" como **nombre** y haga clic en el botón **nuevo** para crear el nuevo archivo **DockMenu. Xib** :

    ![Agregar una definición de interfaz vacía](menu-images/dock02.png "Agregar una definición de interfaz vacía")
2. En el **Panel de solución**, haga doble clic en el archivo **DockMenu. Xib** para abrirlo para su edición en Xcode. Cree un nuevo **menú** con los siguientes elementos: **Dirección**, **fecha**, **saludo**y **firma** . 

    [![Diseño de la interfaz de usuario](menu-images/dock03.png "Diseño de la interfaz de usuario")](menu-images/dock03-large.png#lightbox)
3. A continuación, vamos a conectar los nuevos elementos de menú a las acciones existentes que hemos creado para el menú personalizado en la sección anterior [Agregar, editar y eliminar menús](#Adding,_Editing_and_Deleting_Menus) . Cambie al **Inspector de conexión** y seleccione el **primer respondedor** en la **jerarquía**de la interfaz. Desplácese hacia abajo y busque la `phraseAddress:` acción. Arrastre una línea desde el círculo en esa acción hasta el elemento de menú **Dirección** :

    [![Arrastrar para conectar una acción](menu-images/dock04.png "Arrastrar para conectar una acción")](menu-images/dock04-large.png#lightbox)
4. Repita el procedimiento con todos los demás elementos de menú que los asocian a sus acciones correspondientes: 

    [![Las acciones necesarias](menu-images/dock05.png "Las acciones necesarias")](menu-images/dock05-large.png#lightbox)
5. A continuación, seleccione la **aplicación** en la jerarquía de la **interfaz**. En el **Inspector de conexión**, arrastre una línea desde el círculo en la `dockMenu` salida hasta el menú que acabamos de crear:

    [![Arrastrar la conexión hacia arriba en la salida](menu-images/dock06.png "Arrastrar la conexión hacia arriba en la salida")](menu-images/dock06-large.png#lightbox)
6. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.
7. Haga doble clic en el archivo **info. plist** para abrirlo para su edición: 

    [![Edición del archivo info. plist](menu-images/dock07.png "Edición del archivo Info.plist")](menu-images/dock07-large.png#lightbox)
8. Haga clic en la pestaña **origen** en la parte inferior de la pantalla: 

    [![Seleccionar la vista Código fuente](menu-images/dock08.png "Seleccionar la vista Código fuente")](menu-images/dock08-large.png#lightbox)
9. Haga clic en **Agregar nueva entrada**, haga clic en el botón verde más, establezca el nombre de la propiedad en "AppleDockMenu" y el valor en "DockMenu" (el nombre del nuevo archivo. Xib sin la extensión): 

    [![Agregar el elemento DockMenu](menu-images/dock09.png "Agregar el elemento DockMenu")](menu-images/dock09-large.png#lightbox)

Ahora, Si ejecutamos la aplicación y hacemos clic con el botón derecho en su icono en el Dock, se mostrarán los nuevos elementos de menú:

![Un ejemplo del menú acoplar en ejecución](menu-images/dock10.png "Un ejemplo del menú acoplar en ejecución")

Si seleccionamos uno de los elementos personalizados en el menú, se modificará el texto de la vista de texto.

<a name="Pop-up_Menus_and_Pull-Down_Lists"></a>

## <a name="pop-up-button-and-pull-down-lists"></a>Botón emergente y listas desplegables

Un botón emergente muestra un elemento seleccionado y presenta una lista de opciones entre las que elegir cuando el usuario hace clic en ella. Una lista desplegable es un tipo de botón emergente que se usa normalmente para seleccionar comandos específicos del contexto de la tarea actual. Ambos pueden aparecer en cualquier parte de una ventana.

Vamos a crear un botón emergente personalizado para nuestra aplicación haciendo lo siguiente:

1. Edite el archivo **Main. Storyboard** en Xcode y arrastre un **botón emergente** desde el **Inspector de biblioteca** hasta la ventana del **Panel** que hemos creado en la sección de [menús contextuales](#Contextual_Menus) : 

    [![Agregar un botón emergente](menu-images/popup01.png "Agregar un botón emergente")](menu-images/popup01-large.png#lightbox)
2. Agregue un nuevo elemento de menú y establezca los títulos de los elementos de la ventana emergente en: **Dirección**, **fecha**, **saludo**y **firma** . 

    [![Configurar los elementos de menú](menu-images/popup02.png "Configurar los elementos de menú")](menu-images/popup02-large.png#lightbox)
3. A continuación, vamos a conectar los nuevos elementos de menú a las acciones existentes que hemos creado para el menú personalizado en la sección anterior [Agregar, editar y eliminar menús](#Adding,_Editing_and_Deleting_Menus) . Cambie al **Inspector de conexión** y seleccione el **primer respondedor** en la **jerarquía**de la interfaz. Desplácese hacia abajo y busque la `phraseAddress:` acción. Arrastre una línea desde el círculo en esa acción hasta el elemento de menú **Dirección** : 

    [![Arrastrar para conectar una acción](menu-images/popup03.png "Arrastrar para conectar una acción")](menu-images/popup03-large.png#lightbox)
4. Repita el procedimiento con todos los demás elementos de menú que los asocian a sus acciones correspondientes: 

    [![Todas las acciones necesarias](menu-images/popup04.png "Todas las acciones necesarias")](menu-images/popup04-large.png#lightbox)
5. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Ahora, Si ejecutamos la aplicación y seleccionamos un elemento de la ventana emergente, el texto de la vista de texto cambiará:

![Un ejemplo del elemento emergente en ejecución](menu-images/popup05.png "Un ejemplo del elemento emergente en ejecución")

Puede crear listas desplegables y trabajar con ellas de la misma manera que los botones emergentes. En lugar de asociarse a una acción existente, puede crear sus propias acciones personalizadas como hicimos en el menú contextual de la sección [menús contextuales](#Contextual_Menus) .

## <a name="summary"></a>Resumen

En este artículo se ha realizado una visión detallada del trabajo con menús y elementos de menú en una aplicación de Xamarin. Mac. En primer lugar, hemos examinado la barra de menús de la aplicación y, a continuación, hemos examinado la creación de menús contextuales. Por último, se han incluido menús emergentes y listas desplegables.

## <a name="related-links"></a>Vínculos relacionados

- [MacMenus (ejemplo)](/samples/xamarin/mac-samples/macmenus)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Directrices de la interfaz humana: menús](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)
- [Introducción a los menús de la aplicación y las listas emergentes](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MenuList/MenuList.html)