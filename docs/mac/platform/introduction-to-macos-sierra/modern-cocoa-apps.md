---
title: Creación de aplicaciones modernas de macOS
description: En este artículo se tratan varias sugerencias, características y técnicas que un desarrollador puede usar para compilar una aplicación moderna de macOS en Xamarin. Mac.
ms.prod: xamarin
ms.assetid: F20EE590-246E-40EB-B309-D9D8C090C7F1
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 13d1709f77b312dbdf357c8ce1871727b2073fef
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574436"
---
# <a name="building-modern-macos-apps"></a>Creación de aplicaciones modernas de macOS

_En este artículo se tratan varias sugerencias, características y técnicas que un desarrollador puede usar para compilar una aplicación moderna de macOS en Xamarin. Mac._

<a name="Building-Modern-Looks-with-Modern-Views"></a>

## <a name="building-modern-looks-with-modern-views"></a>Creación de un aspecto moderno con las vistas modernas

Un aspecto moderno incluirá una ventana moderna y un aspecto de la barra de herramientas, como la aplicación de ejemplo que se muestra a continuación:

[![](modern-cocoa-apps-images/content08.png "An example of a modern Mac app UI")](modern-cocoa-apps-images/content08.png#lightbox)

<a name="Enabling-Full-Sized-Content-Views"></a>

### <a name="enabling-full-sized-content-views"></a>Habilitar vistas de contenido de tamaño completo

Para conseguirlo en una aplicación de Xamarin. Mac, el desarrollador querrá usar una vista de _contenido de tamaño completo_, lo que significa que el contenido se extiende en las áreas de la herramienta y la barra de título, y que MacOS lo desenfocará automáticamente.

Para habilitar esta característica en el código, cree una clase personalizada para `NSWindowController` y haga que tenga un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Set window to use Full Size Content View
            Window.StyleMask = NSWindowStyle.FullSizeContentView;
        }
        #endregion
    }
}
```

Esta característica también se puede habilitar en la Interface Builder de Xcode seleccionando la ventana y comprobando la **vista de contenido de tamaño completo**:

[![](modern-cocoa-apps-images/content01.png "Editing the main storyboard in Xcode's Interface Builder")](modern-cocoa-apps-images/content01.png#lightbox)

Cuando se usa una vista de contenido de tamaño completo, el desarrollador puede tener que desplazar el contenido debajo del título y las áreas de la barra de herramientas para que el contenido específico (como etiquetas) no se deslice debajo de ellos.

Para complicar este problema, las áreas de título y de barra de herramientas pueden tener un alto dinámico basado en la acción que el usuario está realizando actualmente, la versión de macOS que el usuario ha instalado o el hardware de Mac en el que se ejecuta la aplicación.

Como resultado, simplemente no funcionará la codificación rígida del desplazamiento al diseñar la interfaz de usuario. El desarrollador deberá adoptar un enfoque dinámico.

Apple ha incluido la propiedad [observable de valor de clave](~/mac/app-fundamentals/databinding.md#Observing_Value_Changes) `ContentLayoutRect` de la `NSWindow` clase para obtener el área de contenido actual en el código. El desarrollador puede usar este valor para colocar manualmente los elementos necesarios cuando cambia el área de contenido.

La mejor solución es usar las clases de diseño automático y tamaño para colocar los elementos de la interfaz de usuario en el código o en Interface Builder.

Código como el ejemplo siguiente se puede usar para colocar los elementos de la interfaz de usuario mediante el diseño automático y las clases de tamaño en el controlador de vista de la aplicación:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacModern
{
    public partial class ViewController : NSViewController
    {
        #region Computed Properties
        public NSLayoutConstraint topConstraint { get; set; }
        #endregion

        ...

        #region Override Methods
        public override void UpdateViewConstraints ()
        {
            // Has the constraint already been set?
            if (topConstraint == null) {
                // Get the top anchor point
                var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
                var topAnchor = contentLayoutGuide.TopAnchor;

                // Found?
                if (topAnchor != null) {
                    // Assemble constraint and activate it
                    topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
                    topConstraint.Active = true;
                }
            }

            base.UpdateViewConstraints ();
        }
        #endregion
    }
}
```

Este código crea almacenamiento para una restricción Top que se aplicará a una etiqueta ( `ItemTitle` ) para asegurarse de que no se pospone en el área de título y barra de herramientas:

```csharp
public NSLayoutConstraint topConstraint { get; set; }
```

Al invalidar el método del controlador de vista `UpdateViewConstraints` , el desarrollador puede probar para ver si ya se ha creado la restricción necesaria y crearla si es necesario.

Si es necesario compilar una nueva restricción, `ContentLayoutGuide` se obtiene acceso a la propiedad de la ventana del control que se debe restringir y convertirla en un `NSLayoutGuide` :

```csharp
var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
```

Se obtiene acceso a la propiedad de nivel de delimitador de `NSLayoutGuide` y, si está disponible, se usa para generar una nueva restricción con la cantidad de desplazamiento deseada y la nueva restricción se activa para aplicarla:

```csharp
// Assemble constraint and activate it
topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
topConstraint.Active = true;
```

<a name="Enabling-Streamlined-Toolbars"></a>

### <a name="enabling-streamlined-toolbars"></a>Habilitar barras de herramientas optimizadas

Una ventana de macOS normal incluye una barra de título estándar en las ejecuciones, junto con el borde superior de la ventana. Si la ventana también incluye una barra de herramientas, se mostrará en esta área de la barra de título:

[![](modern-cocoa-apps-images/content02.png "A standard Mac Toolbar")](modern-cocoa-apps-images/content02.png#lightbox)

Cuando se usa una barra de herramientas optimizada, el área de título desaparece y la barra de herramientas se desplaza hacia arriba en la posición de la barra de título, en línea con los botones cerrar, minimizar y maximizar de la ventana:

[![](modern-cocoa-apps-images/content03.png "A streamlined Mac Toolbar")](modern-cocoa-apps-images/content03.png#lightbox)

La barra de herramientas simplificada se habilita invalidando el `ViewWillAppear` método de `NSViewController` y haciéndolo similar al siguiente:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Enable streamlined Toolbars
    View.Window.TitleVisibility = NSWindowTitleVisibility.Hidden;
}
```

Este efecto se usa normalmente para _las aplicaciones_ de la caja de zapatos (una aplicación de ventana) como mapas, calendario, notas y preferencias del sistema. 

<a name="Using-Accessory-View-Controllers"></a>

### <a name="using-accessory-view-controllers"></a>Uso de controladores de vista de accesorio

En función del diseño de la aplicación, el desarrollador también podría querer complementar el área de la barra de título con un controlador de vista de accesorio que aparece justo debajo del área de la barra de título y herramientas para proporcionar controles contextuales al usuario en función de la actividad en la que están participando actualmente:

[![](modern-cocoa-apps-images/content04.png "An example Accessory View Controller")](modern-cocoa-apps-images/content04.png#lightbox)

El controlador de vista de accesorio se desenfocará automáticamente y el sistema cambiará su tamaño sin intervención del desarrollador.

Para agregar un controlador de vista de accesorio, haga lo siguiente:

1. Haga doble clic en el archivo `Main.storyboard` en el **Explorador de soluciones** para abrirlo para su edición.
2. Arrastre un **controlador de vista personalizado** a la jerarquía de la ventana: 

    [![](modern-cocoa-apps-images/content05.png "Adding a new Custom View Controller")](modern-cocoa-apps-images/content05.png#lightbox)
3. Diseño de la interfaz de usuario de la vista de accesorio: 

    [![](modern-cocoa-apps-images/content06.png "Designing the new view")](modern-cocoa-apps-images/content06.png#lightbox)
4. Exponga la vista de accesorio como una **salida** y cualquier otra **acción** o **salida** para su interfaz de usuario: 

    [![](modern-cocoa-apps-images/content07.png "Adding the required OUtlet")](modern-cocoa-apps-images/content07.png#lightbox)
5. Guarde los cambios.
6. Vuelva a Visual Studio para Mac para sincronizar los cambios.

Edite `NSWindowController` y haga que tenga un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Create a title bar accessory view controller and attach
            // the view created in Interface Builder
            var accessoryView = new NSTitlebarAccessoryViewController ();
            accessoryView.View = AccessoryViewGoBar;

            // Set the location and attach the accessory view to the
            // titlebar to be displayed
            accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
            Window.AddTitlebarAccessoryViewController (accessoryView);

        }
        #endregion
    }
}
```

Los puntos clave de este código son los lugares en los que la vista se establece en la vista personalizada que se definió en Interface Builder y se expone como una **salida**:

```csharp
accessoryView.View = AccessoryViewGoBar;
```

Y `LayoutAttribute` que define _dónde_ se mostrará el accesorio:

```csharp
accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
```

Dado que macOS ahora está totalmente localizado, las `Left` propiedades y han `Right` `NSLayoutAttribute` quedado en desuso y deben reemplazarse por `Leading` y `Trailing` .

<a name="Using-Tabbed-Windows"></a>

### <a name="using-tabbed-windows"></a>Usar ventanas con pestañas

Además, el sistema macOS puede agregar controladores de vista de accesorio a la ventana de la aplicación. Por ejemplo, para crear ventanas con pestañas en las que varias de las ventanas de la aplicación se combinan en una ventana virtual:

[![](modern-cocoa-apps-images/content08.png "An example of a tabbed Mac Window")](modern-cocoa-apps-images/content08.png#lightbox)

Normalmente, el desarrollador deberá realizar una acción limitada usar ventanas con pestañas en sus aplicaciones de Xamarin. Mac, el sistema las administrará automáticamente de la siguiente manera:

- Las ventanas se incluirán en pestañas automáticamente cuando `OrderFront` se invoque el método.
- Windows se desplazará automáticamente cuando `OrderOut` se invoque el método.
- En el código, todas las ventanas con pestañas se siguen considerando "visibles"; sin embargo, las pestañas que no están en el frente están ocultas por el sistema mediante CoreGraphics.
- Utilice la `TabbingIdentifier` propiedad de `NSWindow` para agrupar las ventanas en pestañas.
- Si se trata `NSDocument` de una aplicación basada en, varias de estas características se habilitarán automáticamente (por ejemplo, el botón de signo más que se agrega a la barra de pestañas) sin ninguna acción del desarrollador.
- Las aplicaciones no basadas en no `NSDocument` basados en pueden habilitar el botón "más" del grupo de pestañas para agregar un nuevo documento invalidando el `GetNewWindowForTab` método de `NSWindowsController` .

Al reunir todas las piezas, el `AppDelegate` de una aplicación que quería usar ventanas con pestañas basadas en sistema podría ser similar al siguiente:

```csharp
using AppKit;
using Foundation;

namespace MacModern
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewDocumentNumber { get; set; } = 0;
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

        #region Custom Actions
        [Export ("newDocument:")]
        public void NewDocument (NSObject sender)
        {
            // Get new window
            var storyboard = NSStoryboard.FromName ("Main", null);
            var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

            // Display
            controller.ShowWindow (this);
        } 
        #endregion
    }
}
```

Donde la `NewDocumentNumber` propiedad realiza un seguimiento del número de nuevos documentos creados y el `NewDocument` método crea un nuevo documento y lo muestra.

El `NSWindowController` podría tener el siguiente aspecto:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Application Access
        /// <summary>
        /// A helper shortcut to the app delegate.
        /// </summary>
        /// <value>The app.</value>
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Public Methods
        public void SetDefaultDocumentTitle ()
        {
            // Is this the first document?
            if (App.NewDocumentNumber == 0) {
                // Yes, set title and increment
                Window.Title = "Untitled";
                ++App.NewDocumentNumber;
            } else {
                // No, show title and count
                Window.Title = $"Untitled {App.NewDocumentNumber++}";
            }
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Prefer Tabbed Windows
            Window.TabbingMode = NSWindowTabbingMode.Preferred;
            Window.TabbingIdentifier = "Main";

            // Set default window title
            SetDefaultDocumentTitle ();

            // Set window to use Full Size Content View
            // Window.StyleMask = NSWindowStyle.FullSizeContentView;

            // Create a title bar accessory view controller and attach
            // the view created in Interface Builder
            var accessoryView = new NSTitlebarAccessoryViewController ();
            accessoryView.View = AccessoryViewGoBar;

            // Set the location and attach the accessory view to the
            // titlebar to be displayed
            accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
            Window.AddTitlebarAccessoryViewController (accessoryView);

        }

        public override void GetNewWindowForTab (NSObject sender)
        {
            // Ask app to open a new document window
            App.NewDocument (this);
        }
        #endregion
    }
}
```

Donde la `App` propiedad estática proporciona un acceso directo para llegar a `AppDelegate` . El `SetDefaultDocumentTitle` método establece un nuevo título de documento en función del número de nuevos documentos creados.

El código siguiente indica a macOS que la aplicación prefiere usar pestañas y proporciona una cadena que permite agrupar las ventanas de la aplicación en pestañas:

```csharp
// Prefer Tabbed Windows
Window.TabbingMode = NSWindowTabbingMode.Preferred;
Window.TabbingIdentifier = "Main";
```

Y el método de invalidación siguiente agrega un botón de signo más a la barra de pestañas que creará un nuevo documento cuando el usuario haga clic en él:

```csharp
public override void GetNewWindowForTab (NSObject sender)
{
    // Ask app to open a new document window
    App.NewDocument (this);
}
```

<a name="Using-Core-Animation"></a>

### <a name="using-core-animation"></a>Usar la animación básica

La animación básica es un motor de representación de gráficos de alta potencia que está integrado en macOS. La animación principal se ha optimizado para aprovechar las ventajas de la GPU (unidad de procesamiento de gráficos) disponible en el hardware de macOS moderno en lugar de ejecutar las operaciones de gráficos en la CPU, lo que puede ralentizar el equipo.

`CALayer`, Proporcionado por la animación básica, se puede usar para tareas como el desplazamiento rápido y fluido y animaciones. La interfaz de usuario de una aplicación debe estar formada por varias subvistas y capas para aprovechar al máximo la animación básica.

Un `CALayer` objeto proporciona varias propiedades que permiten al desarrollador controlar lo que se presenta en la pantalla al usuario, por ejemplo:

- `Content`-Puede ser `NSImage` o `CGImage` que proporciona el contenido de la capa.
- `BackgroundColor`: Establece el color de fondo de la capa como un`CGColor`
- `BorderWidth`: Establece el ancho del borde.
- `BorderColor`: Establece el color del borde.

Para usar gráficos principales en la interfaz de usuario de la aplicación, debe usar vistas con _respaldo de capas_ , que Apple sugiere que el desarrollador siempre debe habilitar en la vista de contenido de la ventana. De este modo, todas las vistas secundarias heredarán también automáticamente la copia de seguridad de la capa.

Además, Apple sugiere el uso de vistas respaldadas por capas en lugar de agregar un nuevo `CALayer` como una subcapa, ya que el sistema controlará automáticamente varias de las opciones de configuración necesarias (como las que requiere la presentación de retina).

La copia de seguridad de capas se puede habilitar estableciendo el valor `WantsLayer` de `NSView` en `true` o dentro del Interface Builder de Xcode en el **Inspector de efectos** de la vista comprobando el **nivel de animación principal**:

[![](modern-cocoa-apps-images/content09.png "The View Effects Inspector")](modern-cocoa-apps-images/content09.png#lightbox)

<a name="Redrawing-Views-with-Layers"></a>

#### <a name="redrawing-views-with-layers"></a>Volver a dibujar vistas con capas

Otro paso importante al usar vistas con copia de seguridad de capas en una aplicación de Xamarin. Mac es establecer el `LayerContentsRedrawPolicy` de en `NSView` `OnSetNeedsDisplay` en el `NSViewController` . Por ejemplo:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set the content redraw policy
    View.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

Si el desarrollador no establece esta propiedad, se volverá a dibujar la vista cada vez que cambie el origen del marco, lo que no es deseable por motivos de rendimiento. Sin embargo, con esta propiedad establecida en `OnSetNeedsDisplay` el desarrollador tendrá que establecer manualmente en para `NeedsDisplay` `true` forzar el redibujo del contenido.

Cuando una vista se marca como desfasada, el sistema comprueba la `WantsUpdateLayer` propiedad de la vista. Si devuelve `true` , `UpdateLayer` se llama al método; de lo contrario, `DrawRect` se llama al método de la vista para actualizar el contenido de la vista.

Apple tiene las siguientes sugerencias para actualizar el contenido de una vista cuando sea necesario:

- Apple prefiere usar `UpdateLater` sobre `DrawRect` siempre que sea posible, ya que proporciona un aumento significativo del rendimiento.
- Use el mismo `layer.Contents` para los elementos de la interfaz de usuario que tienen un aspecto similar.
- Apple también prefiere que el desarrollador componga su interfaz de usuario con vistas estándar como `NSTextField` , siempre que sea posible.

Para usar `UpdateLayer` , cree una clase personalizada para `NSView` y haga que el código tenga el aspecto siguiente:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainView : NSView
    {
        #region Computed Properties
        public override bool WantsLayer {
            get { return true; }
        }

        public override bool WantsUpdateLayer {
            get { return true; }
        }
        #endregion

        #region Constructor
        public MainView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void DrawRect (CoreGraphics.CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

        }

        public override void UpdateLayer ()
        {
            base.UpdateLayer ();

            // Draw view 
            Layer.BackgroundColor = NSColor.Red.CGColor;
        }
        #endregion
    }
}
```

<a name="Using-Modern-Drag-and-Drop"></a>

## <a name="using-modern-drag-and-drop"></a>Usar la función de arrastrar y colocar moderna

Para presentar una experiencia moderna de arrastrar y colocar para el usuario, el desarrollador debe adoptar la _rebaño de arrastre_ en las operaciones de arrastrar y colocar de la aplicación. Arrastrar el recuento es donde cada archivo o elemento individual que se está arrastrando aparece inicialmente como un elemento individual que reproduce (agrupar por debajo del cursor con un recuento del número de elementos) cuando el usuario continúa con la operación de arrastre.

Si el usuario finaliza la operación de arrastre, los elementos individuales se anularán y volverán a sus ubicaciones originales.

En el código de ejemplo siguiente se habilita la función de arrastrar en una vista personalizada:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainView : NSView, INSDraggingSource, INSDraggingDestination
    {
        #region Constructor
        public MainView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void MouseDragged (NSEvent theEvent)
        {
            // Create group of string to be dragged
            var string1 = new NSDraggingItem ((NSString)"Item 1");
            var string2 = new NSDraggingItem ((NSString)"Item 2");
            var string3 = new NSDraggingItem ((NSString)"Item 3");

            // Drag a cluster of items
            BeginDraggingSession (new [] { string1, string2, string3 }, theEvent, this);
        }
        #endregion
    }
}
```

El efecto de recorte se logra mediante el envío de cada elemento que se arrastra al `BeginDraggingSession` método de `NSView` como un elemento independiente en una matriz.

Cuando trabaje con `NSTableView` o `NSOutlineView` , use el `PastboardWriterForRow` método de la `NSTableViewDataSource` clase para iniciar la operación de arrastre:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDataSource: NSTableViewDataSource
    {
        #region Constructors
        public ContentsTableDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override INSPasteboardWriting GetPasteboardWriterForRow (NSTableView tableView, nint row)
        {
            // Return required pasteboard writer
            ...
            
            // Pasteboard writer failed
            return null;
        }
        #endregion
    }
}
```

Esto permite al desarrollador proporcionar un individuo `NSDraggingItem` para cada elemento de la tabla que se está arrastrando en contraposición al método anterior `WriteRowsWith` que escribe todas las filas como un solo grupo en la mesa de la mesa.

Al trabajar con `NSCollectionViews` , use de nuevo el `PasteboardWriterForItemAt` método en lugar del `WriteItemsAt` método al arrastrar.

El desarrollador siempre debe evitar la colocación de archivos de gran tamaño en la mesa de área. Nuevo en macOS Sierra, las _promesas de archivo_ permiten al desarrollador colocar las referencias a los archivos especificados en la mesa de pegado que se cumplirán más adelante cuando el usuario finalice la operación de colocar con las nuevas `NSFilePromiseProvider` clases y `NSFilePromiseReceiver` .

<a name="Using-Modern-Event-Tracking"></a>

## <a name="using-modern-event-tracking"></a>Usar el seguimiento de eventos moderno

Para un elemento de la interfaz de usuario (como un `NSButton` ) que se ha agregado a un área de título o barra de herramientas, el usuario debe poder hacer clic en el elemento y hacer que active un evento como normal (por ejemplo, mostrar una ventana emergente). Sin embargo, puesto que el elemento también está en el área de título o barra de herramientas, el usuario debe poder hacer clic y arrastrar el elemento para moverla también.

Para lograr esto en el código, cree una clase personalizada para el elemento (como `NSButton` ) e invalide el `MouseDown` evento como se indica a continuación:

```csharp
public override void MouseDown (NSEvent theEvent)
{
    var shouldCallSuper = false;

    Window.TrackEventsMatching (NSEventMask.LeftMouseUp, 2000, NSRunLoop.NSRunLoopEventTracking, (NSEvent evt, ref bool stop) => {
        // Handle event as normal
        stop = true;
        shouldCallSuper = true;
    });

    Window.TrackEventsMatching(NSEventMask.LeftMouseDragged, 2000, NSRunLoop.NSRunLoopEventTracking, (NSEvent evt, ref bool stop) => {
        // Pass drag event to window
        stop = true;
        Window.PerformWindowDrag (evt);
    });

    // Call super to handle mousedown
    if (shouldCallSuper) {
        base.MouseDown (theEvent);
    }
}
```

Este código usa el `TrackEventsMatching` método de `NSWindow` que el elemento de la interfaz de usuario está asociado para interceptar los `LeftMouseUp` `LeftMouseDragged` eventos y. Para un `LeftMouseUp` evento, el elemento de la interfaz de usuario responde como normal. Para el `LeftMouseDragged` evento, el evento se pasa al `NSWindow` método de `PerformWindowDrag` para que la ventana se mueva en la pantalla.

La llamada al `PerformWindowDrag` método de la `NSWindow` clase proporciona las siguientes ventajas:

- Permite que la ventana se mueva, incluso si la aplicación está bloqueada (por ejemplo, al procesar un bucle profundo).
- El cambio de espacio funcionará según lo previsto.
- La barra de espacios se mostrará como normal.
- El ajuste y la alineación de las ventanas funcionan como de costumbre.

<a name="Using-Modern-Container-View-Controls"></a>

## <a name="using-modern-container-view-controls"></a>Usar los controles de vista de contenedor modernos

macOS Sierra proporciona muchas mejoras modernas en los controles de vista de contenedor existentes disponibles en la versión anterior del sistema operativo.

<a name="Table View Enhancements"></a>

## <a name="table-view-enhancements"></a>Mejoras de la vista de tabla

El desarrollador siempre debe usar la nueva `NSView` versión basada en controles de vista de contenedor como `NSTableView` . Por ejemplo:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDelegate : NSTableViewDelegate
    {
        #region Constructors
        public ContentsTableDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // Build new view
            var view = new NSView ();
            ...

            // Return the view representing the item to display
            return view;
        }
        #endregion
    }
}
```

Esto permite que las acciones de fila de tabla personalizadas se adjunten a las filas especificadas de la tabla (como deslizar el dedo hacia la derecha para eliminar la fila). Para habilitar este comportamiento, invalide el `RowActions` método de `NSTableViewDelegate` :

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDelegate : NSTableViewDelegate
    {
        #region Constructors
        public ContentsTableDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // Build new view
            var view = new NSView ();
            ...

            // Return the view representing the item to display
            return view;
        }

        public override NSTableViewRowAction [] RowActions (NSTableView tableView, nint row, NSTableRowActionEdge edge)
        {
            // Take action based on the edge
            if (edge == NSTableRowActionEdge.Trailing) {
                // Create row actions
                var editAction = NSTableViewRowAction.FromStyle (NSTableViewRowActionStyle.Regular, "Edit", (action, rowNum) => {
                    // Handle row being edited
                    ...
                });

                var deleteAction = NSTableViewRowAction.FromStyle (NSTableViewRowActionStyle.Destructive, "Delete", (action, rowNum) => {
                    // Handle row being deleted
                    ...
                });

                // Return actions
                return new [] { editAction, deleteAction };
            } else {
                // No matching actions
                return null;
            }
        }
        #endregion
    }
}
```

El estático `NSTableViewRowAction.FromStyle` se usa para crear una nueva acción de fila de tabla de los siguientes estilos:

- `Regular`: Realiza una acción no destructiva estándar, como editar el contenido de la fila.
- `Destructive`: Realiza una acción destructiva, como eliminar la fila de la tabla. Estas acciones se representarán con un fondo rojo.

<a name="Scroll-View-Enhancements"></a>

## <a name="scroll-view-enhancements"></a>Mejoras de la vista de desplazamiento 

Cuando se usa una vista de desplazamiento ( `NSScrollView` ) directamente o como parte de otro control (como `NSTableView` ), el contenido de la vista de desplazamiento se puede deslizar bajo las áreas de título y de la barra de herramientas en una aplicación de Xamarin. Mac con un aspecto moderno y vistas.

Como resultado, el primer elemento del área de contenido de la vista de desplazamiento puede ocultarse parcialmente en el área de título y de barra de herramientas.

Para corregir este problema, Apple ha agregado dos nuevas propiedades a la `NSScrollView` clase:

- `ContentInsets`: Permite al desarrollador proporcionar un `NSEdgeInsets` objeto que define el desplazamiento que se aplicará a la parte superior de la vista de desplazamiento.
- `AutomaticallyAdjustsContentInsets`: Si `true` la vista de desplazamiento controlará automáticamente el `ContentInsets` para el desarrollador.

Mediante el uso del `ContentInsets` desarrollador puede ajustar el inicio de la vista de desplazamiento para permitir la inclusión de accesorios como:

- Un indicador de ordenación como el que se muestra en la aplicación de correo.
- Campo de búsqueda.
- Botón actualizar o actualizar.

<a name="Auto-Layout-and-Localization-in-Modern-Apps"></a>

## <a name="auto-layout-and-localization-in-modern-apps"></a>Diseño y localización automáticos en aplicaciones modernas

Apple ha incluido varias tecnologías en Xcode que permiten al desarrollador crear fácilmente una aplicación de macOS internacionalizada. Xcode permite ahora al desarrollador separar el texto orientado al usuario del diseño de la interfaz de usuario de la aplicación en sus archivos de guion gráfico y proporciona herramientas para mantener esta separación si cambia la interfaz de usuario.

Para obtener más información, consulte la [Guía de internacionalización y localización](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)de Apple.

<a name="Implementing-Base-Internationalization"></a>

### <a name="implementing-base-internationalization"></a>Implementación de la internacionalización base 

Mediante la implementación de la internacionalización base, el desarrollador puede proporcionar un único archivo de guion gráfico para representar la interfaz de usuario de la aplicación y separar todas las cadenas orientadas al usuario. 

Cuando el desarrollador crea el archivo de guion gráfico (o archivos) inicial que define la interfaz de usuario de la aplicación, se creará en la internacionalización base (el lenguaje que habla el desarrollador).

A continuación, el desarrollador puede exportar localizaciones y las cadenas de internacionalización base (en el diseño de la interfaz de usuario del guión gráfico) que se pueden traducir en varios idiomas.

Más adelante, se pueden importar estas localizaciones y Xcode generará los archivos de cadena específicos del lenguaje para el guión gráfico.

<a name="Implementing-Auto-Layout-to-Support-Localization"></a>

### <a name="implementing-auto-layout-to-support-localization"></a>Implementación del diseño automático para admitir la localización

Dado que las versiones localizadas de los valores de cadena pueden tener tamaños y/o direcciones de lectura muy diferentes, el desarrollador debe usar el diseño automático para colocar y cambiar el tamaño de la interfaz de usuario de la aplicación en un archivo de guion gráfico.

Apple recomienda hacer lo siguiente:

- **Quitar restricciones de ancho fijo** : todas las vistas basadas en texto deben poder cambiar de tamaño en función de su contenido. La vista de ancho fijo puede recortar su contenido en idiomas específicos.
- **Usar tamaños de contenido intrínseco** : de forma predeterminada, las vistas basadas en texto cambiarán de tamaño automáticamente para ajustarse a su contenido. En el caso de las vistas basadas en texto que no tienen el tamaño correcto, selecciónelas en Xcode Interface Builder, a continuación, elija **Editar**  >  **tamaño para ajustar el contenido**.
- **Aplicar atributos iniciales y finales** : dado que la dirección del texto puede cambiar en función del idioma del usuario, use los atributos New `Leading` y `Trailing` Constraint en contraposición a los `Right` atributos y existentes `Left` . `Leading`y `Trailing` se ajustarán automáticamente en función de la dirección de los idiomas.
- **Anclar vistas a vistas adyacentes** : Esto permite que las vistas cambien de posición y cambien de tamaño a medida que las vistas en torno a ellas cambian en respuesta al idioma seleccionado.
- **No establecer un tamaño mínimo o máximo de Windows** : permitir que Windows cambie de tamaño a medida que el idioma seleccionado cambia el tamaño de las áreas de contenido.
- **Los cambios de diseño de prueba constantemente** : durante el desarrollo en la aplicación se deben probar constantemente en distintos idiomas. Consulte la documentación sobre [la prueba de la aplicación internacionalizada](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/TestingYourInternationalApp/TestingYourInternationalApp.html#//apple_ref/doc/uid/10000171i-CH7-SW1) de Apple para obtener más detalles.
- **Usar NSStackViews para anclar vistas juntas**  -  `NSStackViews` permite que su contenido cambie y crezca de maneras predecibles y el tamaño del cambio de contenido según el idioma seleccionado.

<a name="Localizing-in-Xcodes-Interface-Builder"></a>

### <a name="localizing-in-xcodes-interface-builder"></a>Localizar en Interface Builder de Xcode

Apple ha proporcionado varias características en Interface Builder de Xcode que el desarrollador puede usar al diseñar o editar la interfaz de usuario de una aplicación para admitir la localización. La sección **dirección del texto** del **Inspector de atributos** permite al desarrollador proporcionar sugerencias sobre cómo se debe usar y actualizar la dirección en una vista de selección basada en texto (como `NSTextField` ):

[![](modern-cocoa-apps-images/content10.png "The Text Direction options")](modern-cocoa-apps-images/content10.png#lightbox)

Hay tres valores posibles para la **dirección del texto**:

- **Natural** : el diseño se basa en la cadena asignada al control.
- De **izquierda a derecha** : el diseño siempre se fuerza de izquierda a derecha.
- De **derecha a izquierda** : el diseño siempre se fuerza de derecha a izquierda.

Existen dos valores posibles para el **diseño**:

- De **izquierda a derecha** : el diseño siempre es de izquierda a derecha.
- De **derecha a izquierda** : el diseño siempre es de derecha a izquierda.

Normalmente, no se deben cambiar a menos que se requiera una alineación específica.

La propiedad **Mirror** indica al sistema que voltee propiedades de control específicas (como la posición de la imagen de celda). Tiene tres valores posibles:

- **Automáticamente** : la posición cambiará automáticamente en función de la dirección del idioma seleccionado.
- **En la interfaz de derecha a izquierda** : la posición solo se cambiará en los idiomas de derecha a izquierda.
- **Nunca** : la posición nunca cambiará.

Si el desarrollador ha especificado el **Centro**, la **justificación** o la alineación **completa** en el contenido de una vista basada en texto, nunca se volteará en función del idioma seleccionado.

Antes de macOS Sierra, los controles creados en el código no se reflejaban automáticamente. El desarrollador tenía que usar código similar al siguiente para controlar la creación de reflejo:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Setting a button's mirroring based on the layout direction
    var button = new NSButton ();
    if (button.UserInterfaceLayoutDirection == NSUserInterfaceLayoutDirection.LeftToRight) {
        button.Alignment = NSTextAlignment.Right;
        button.ImagePosition = NSCellImagePosition.ImageLeft;
    } else {
        button.Alignment = NSTextAlignment.Left;
        button.ImagePosition = NSCellImagePosition.ImageRight;
    }
}
```

Donde `Alignment` y `ImagePosition` se establecen basándose en el `UserInterfaceLayoutDirection` del control.

macOS Sierra agrega varios nuevos constructores de conveniencia (a través del `CreateButton` método estático) que toman varios parámetros (como título, imagen y acción) y se reflejarán de forma automática correctamente. Por ejemplo:

```csharp
var button2 = NSButton.CreateButton (myTitle, myImage, () => {
    // Take action when the button is pressed
    ...
});
```

<a name="Using-System-Appearances"></a>

## <a name="using-system-appearances"></a>Usar aspectos del sistema

Las aplicaciones modernas de macOS pueden adoptar una nueva apariencia de interfaz oscura que funciona bien para la creación de imágenes, edición o presentación de aplicaciones:

[![](modern-cocoa-apps-images/content11.png "An example of a dark Mac Window UI")](modern-cocoa-apps-images/content11.png#lightbox)

Esto puede hacerse agregando una línea de código antes de que se presente la ventana. Por ejemplo:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacModern
{
    public partial class ViewController : NSViewController
    {
        ...
    
        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Apply the Dark Interface Appearance
            View.Window.Appearance = NSAppearance.GetAppearance (NSAppearance.NameVibrantDark);

            ...
        }
        #endregion
    }
}
```

El `GetAppearance` método estático de la `NSAppearance` clase se utiliza para obtener una apariencia con nombre del sistema (en este caso `NSAppearance.NameVibrantDark` ).

Apple tiene las siguientes sugerencias para usar la apariencia del sistema:

- Prefiere los colores con nombre en valores codificados de forma rígida (como `LabelColor` y `SelectedControlColor` ).
- Use el estilo de control estándar del sistema siempre que sea posible.

Una aplicación macOS que use la apariencia del sistema funcionará automáticamente para los usuarios que tengan habilitadas las características de accesibilidad de la aplicación preferencias del sistema. Como resultado, Apple sugiere que el desarrollador siempre debe usar la apariencia del sistema en sus aplicaciones de macOS.

<a name="Designing-UIs-with-Storyboards"></a>

## <a name="designing-uis-with-storyboards"></a>Diseñar ius con guiones gráficos

Los guiones gráficos permiten al desarrollador no solo diseñar los elementos individuales que componen la interfaz de usuario de una aplicación, sino para visualizar y diseñar el flujo de la interfaz de usuario y la jerarquía de los elementos especificados.

Los controladores permiten al desarrollador recopilar elementos en una unidad de composición y objetos segue abstraer y quitar el "código de adherencia" típico necesario para moverse por la jerarquía de vistas:

[![](modern-cocoa-apps-images/content12.png "Editing the UI in Xcode's Interface Builder")](modern-cocoa-apps-images/content12.png#lightbox)

Para obtener más información, consulte la documentación [Introducción a los guiones gráficos](~/mac/platform/storyboards/index.md) .

Hay muchos casos en los que una escena determinada definida en un guion gráfico necesitará datos de una escena anterior en la jerarquía de vistas. Apple tiene las siguientes sugerencias para pasar información entre escenas:

- Los dependancies de datos siempre deben estar en cascada a través de la jerarquía.
- Evite el dependancies estructural codificar de la interfaz de usuario, ya que limita la flexibilidad de la interfaz de usuario.
- Usar interfaces de C# para proporcionar dependancies de datos genéricos.

El controlador de vista que actúa como origen de segue puede invalidar el `PrepareForSegue` método y realizar cualquier inicialización necesaria (como pasar datos) antes de que se ejecute segue para mostrar el controlador de vista de destino. Por ejemplo:

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on Segue ID
    switch (segue.Identifier) {
    case "MyNamedSegue":
        // Prepare for the segue to happen
        ...
        break;
    }
}
```

Para obtener más información, consulte nuestra documentación de [objetos segue](~/mac/platform/storyboards/indepth.md#Segues) .

<a name="Propagating-Actions"></a>

## <a name="propagating-actions"></a>Propagar acciones

En función del diseño de la aplicación macOS, puede haber ocasiones en las que el mejor controlador para una acción en un control de interfaz de usuario esté en otro lugar de la jerarquía de la interfaz de usuario. Esto se aplica normalmente a los menús y elementos de menú que residen en su propia escena, independientes del resto de la interfaz de usuario de la aplicación.

Para controlar esta situación, el desarrollador puede crear una acción personalizada y pasar la acción hacia arriba en la cadena del respondedor. Para obtener más información, consulte la documentación sobre [Cómo trabajar con acciones de ventana personalizadas](~/mac/user-interface/menu.md) .

<a name="Modern-Mac-Features"></a>

## <a name="modern-mac-features"></a>Características modernas de Mac

Apple ha incluido varias características orientadas al usuario en macOS Sierra que permiten al desarrollador sacar el máximo partido de la plataforma Mac, como:

- **NSUserActivity** : permite a la aplicación describir la actividad en la que el usuario está involucrado actualmente. `NSUserActivity`se creó inicialmente para admitir la entrega, donde una actividad iniciada en uno de los dispositivos del usuario podría recogerse y continuarse en otro dispositivo. `NSUserActivity`funciona igual en macOS que en iOS, por lo que consulte la [Introducción a la entrega](~/ios/platform/handoff.md) de documentación de iOS para obtener más detalles.
- **Siri en Mac** : Siri usa la actividad actual ( `NSUserActivity` ) para proporcionar contexto a los comandos que un usuario puede emitir.
- **Restauración de estado** : cuando el usuario sale de una aplicación en MacOS y después la vuelve a iniciar, la aplicación se devolverá automáticamente a su estado anterior. El desarrollador puede usar la API de restauración de estado para codificar y restaurar los Estados transitorios de la interfaz de usuario antes de que se muestre la interfaz de usuario al usuario. Si la aplicación se `NSDocument` basa, la restauración del estado se controla automáticamente. Para habilitar la restauración de estado para `NSDocument` aplicaciones no basadas en, establezca el `Restorable` de la `NSWindow` clase en `true` .
- **Documentos en la nube** : antes de MacOS Sierra, una aplicación tenía que optar explícitamente a trabajar con documentos en la unidad iCloud del usuario. En macOS Sierra las carpetas de **escritorio** y **documentos** del usuario se pueden sincronizar automáticamente con la unidad de icloud del sistema. Como resultado, es posible que se eliminen las copias locales de los documentos para liberar espacio en el equipo del usuario. `NSDocument`las aplicaciones basadas en se controlan automáticamente este cambio. Todos los demás tipos de aplicaciones deberán usar `NSFileCoordinator` para sincronizar la lectura y la escritura de documentos.

<a name="Summary"></a>

## <a name="summary"></a>Resumen

En este artículo se han tratado varias sugerencias, características y técnicas que un desarrollador puede usar para compilar una aplicación moderna de macOS en Xamarin. Mac.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de macOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac)
