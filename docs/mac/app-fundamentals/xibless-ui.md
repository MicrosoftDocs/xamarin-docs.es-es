---
title: '. Storyboard/. Xib: diseño de la interfaz de usuario menos en Xamarin. Mac'
description: En este artículo se explica cómo crear una interfaz de usuario de una aplicación de Xamarin. Mac directamente desde el código de C#, sin archivos. Storyboard, archivos. Xib ni Interface Builder.
ms.prod: xamarin
ms.assetid: 02310F58-DCF1-4589-9F4A-065DF64FC0E1
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 47025f48b1f3455f70a077f2ca55da2cdeeba761
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430889"
---
# <a name="storyboardxib-less-user-interface-design-in-xamarinmac"></a>. Storyboard/. Xib: diseño de la interfaz de usuario menos en Xamarin. Mac

_En este artículo se explica cómo crear una interfaz de usuario de una aplicación de Xamarin. Mac directamente desde el código de C#, sin archivos. Storyboard, archivos. Xib ni Interface Builder._

## <a name="overview"></a>Información general

Cuando se trabaja con C# y .NET en una aplicación de Xamarin. Mac, tiene acceso a los mismos elementos de la interfaz de usuario y herramientas que un desarrollador que trabaja en *Objective-C* y *Xcode* . Normalmente, al crear una aplicación de Xamarin. Mac, usará los Interface Builder de Xcode con archivos. Storyboard o. Xib para crear y mantener la interfaz de usuario de la aplicación.

También tiene la opción de crear parte o toda la interfaz de usuario de la aplicación de Xamarin. Mac directamente en el código de C#. En este artículo, trataremos los aspectos básicos de la creación de interfaces de usuario y elementos de interfaz de usuario en el código de C#.

[![Editor de código Visual Studio para Mac](xibless-ui-images/intro01.png "Editor de código Visual Studio para Mac")](xibless-ui-images/intro01-large.png#lightbox)

<a name="Switching_a_Window_to_use_Code"></a>

## <a name="switching-a-window-to-use-code"></a>Cambiar una ventana para utilizar código

Cuando se crea una nueva aplicación de Cocoa de Xamarin. Mac, se obtiene una ventana en blanco de forma predeterminada. Esta ventana se define en un archivo **Main. Storyboard** (o tradicionalmente un archivo **MainWindow. Xib**) incluido automáticamente en el proyecto. También se incluye un archivo **ViewController.CS** que administra la vista principal de la aplicación (o de nuevo tradicionalmente un archivo **MainWindow.CS** y **MainWindowController.CS** ).

Para cambiar a una ventana de Xibless para una aplicación, haga lo siguiente:

1. Abra la aplicación que desea dejar `.storyboard` de usar o archivos. Xib para definir la interfaz de usuario en Visual Studio para Mac.
2. En el **Panel de solución**, haga clic con el botón derecho en el archivo **Main. Storyboard** o **MainWindow. Xib** y seleccione **quitar**:

    ![Quitar el guión gráfico o la ventana principales](xibless-ui-images/switch01.png "Quitar el guión gráfico o la ventana principales")
3. En el **cuadro de diálogo quitar**, haga clic en el botón **eliminar** para quitar completamente el. Storyboard o. Xib del proyecto:

    ![Confirmar la eliminación](xibless-ui-images/switch02.png "Confirmar la eliminación")

Ahora tendremos que modificar el archivo **MainWindow.CS** para definir el diseño de la ventana y modificar el archivo **ViewController.CS** o **MainWindowController.CS** para crear una instancia de nuestra `MainWindow` clase, ya que ya no se usa el archivo. Storyboard o. Xib.

Las aplicaciones modernas de Xamarin. Mac que usan guiones gráficos para su interfaz de usuario pueden no incluir automáticamente los archivos **MainWindow.CS**, **ViewController.CS** o **MainWindowController.CS** . Según sea necesario, simplemente agregue una nueva clase vacía de C# al proyecto (**Agregar**  >  **nuevo archivo..**  >  . **General**  >  **Clase vacía**) y asígnele el mismo nombre que el archivo que falta.

### <a name="defining-the-window-in-code"></a>Definir la ventana en el código

A continuación, edite el archivo **MainWindow.CS** y haga que tenga el aspecto siguiente:

```csharp
using System;
using Foundation;
using AppKit;
using CoreGraphics;

namespace MacXibless
{
    public partial class MainWindow : NSWindow
    {
        #region Private Variables
        private int NumberOfTimesClicked = 0;
        #endregion

        #region Computed Properties
        public NSButton ClickMeButton { get; set;}
        public NSTextField ClickMeLabel { get ; set;}
        #endregion

        #region Constructors
        public MainWindow (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindow (NSCoder coder) : base (coder)
        {
        }

        public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
            // Define the user interface of the window here
            Title = "Window From Code";

            // Create the content view for the window and make it fill the window
            ContentView = new NSView (Frame);

            // Add UI elements to window
            ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
                AutoresizingMask = NSViewResizingMask.MinYMargin
            };
            ContentView.AddSubview (ClickMeButton);

            ClickMeLabel = new NSTextField (new CGRect (120, Frame.Height - 65, Frame.Width - 130, 20)) {
                BackgroundColor = NSColor.Clear,
                TextColor = NSColor.Black,
                Editable = false,
                Bezeled = false,
                AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin,
                StringValue = "Button has not been clicked yet."
            };
            ContentView.AddSubview (ClickMeLabel);
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Wireup events
            ClickMeButton.Activated += (sender, e) => {
                // Update count
                ClickMeLabel.StringValue = (++NumberOfTimesClicked == 1) ? "Button clicked one time." : string.Format("Button clicked {0} times.",NumberOfTimesClicked);
            };
        }
        #endregion

    }
}
```

Vamos a analizar algunos de los elementos clave.

En primer lugar, hemos agregado algunas _propiedades calculadas_ que actuarán como salidas (como si la ventana se hubiera creado en un archivo. Storyboard o. Xib):

```csharp
public NSButton ClickMeButton { get; set;}
public NSTextField ClickMeLabel { get ; set;}
```

Estos nos proporcionarán acceso a los elementos de la interfaz de usuario que se van a mostrar en la ventana. Dado que la ventana no se está inflando a partir de un archivo. Storyboard o. Xib, necesitamos una manera de crear una instancia de ella (como veremos más adelante en la `MainWindowController` clase). Eso es lo que hace este nuevo método de constructor:

```csharp
public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
    ...
}
```

Aquí es donde se diseñará el diseño de la ventana y se colocarán los elementos de interfaz de usuario necesarios para crear la interfaz de usuario necesaria. Antes de poder agregar elementos de la interfaz de usuario a una ventana, necesita una _vista de contenido_ que contenga los elementos:

```csharp
ContentView = new NSView (Frame);
```

Esto crea una vista de contenido que rellenará la ventana. Ahora se agregará el primer elemento de la interfaz de usuario, `NSButton` a la ventana:

```csharp
ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
ContentView.AddSubview (ClickMeButton);
```

Lo primero que hay que tener en cuenta es que, a diferencia de iOS, macOS usa notación matemática para definir el sistema de coordenadas de la ventana. Por lo tanto, el punto de origen se encuentra en la esquina inferior izquierda de la ventana, con los valores que aumentan a la derecha y hacia la esquina superior derecha de la ventana. Cuando creamos el nuevo `NSButton` , lo vamos a tener en cuenta, ya que definimos su posición y tamaño en la pantalla.

La `AutoresizingMask = NSViewResizingMask.MinYMargin` propiedad indica al botón que desea permanecer en la misma ubicación desde la parte superior de la ventana cuando se cambia el tamaño de la ventana verticalmente. De nuevo, esto es necesario porque (0,0) está en la parte inferior izquierda de la ventana.

Por último, el `ContentView.AddSubview (ClickMeButton)` método agrega `NSButton` a la vista de contenido para que se muestre en la pantalla cuando se ejecute la aplicación y se muestre la ventana.

A continuación, se agregará una etiqueta a la ventana que mostrará el número de veces que se ha `NSButton` realizado el clic en:

```csharp
ClickMeLabel = new NSTextField (new CGRect (120, Frame.Height - 65, Frame.Width - 130, 20)) {
    BackgroundColor = NSColor.Clear,
    TextColor = NSColor.Black,
    Editable = false,
    Bezeled = false,
    AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin,
    StringValue = "Button has not been clicked yet."
};
ContentView.AddSubview (ClickMeLabel);
```

Dado que macOS no tiene un elemento de interfaz de usuario de _etiqueta_ específico, se ha agregado una etiqueta, que no se puede editar, y con un estilo especial `NSTextField` . Al igual que el botón anterior, el tamaño y la ubicación tienen en cuenta que (0,0) está en la parte inferior izquierda de la ventana. La `AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin` propiedad usa el operador **or** para combinar dos `NSViewResizingMask` características. Esto hará que la etiqueta permanezca en la misma ubicación desde la parte superior de la ventana cuando se cambie el tamaño de la ventana verticalmente y se reduzca y crezca en ancho a medida que se cambie el tamaño de la ventana horizontalmente.

De nuevo, el `ContentView.AddSubview (ClickMeLabel)` método agrega `NSTextField` a la vista de contenido para que se muestre en la pantalla cuando se ejecute la aplicación y se abra la ventana.

### <a name="adjusting-the-window-controller"></a>Ajustar el controlador de ventana

Dado que el diseño de ya `MainWindow` no se carga desde un archivo. Storyboard o. Xib, es necesario realizar algunos ajustes en el controlador de ventana. Edite el archivo **MainWindowController.CS** y haga que tenga un aspecto similar al siguiente:

```csharp
using System;

using Foundation;
using AppKit;
using CoreGraphics;

namespace MacXibless
{
    public partial class MainWindowController : NSWindowController
    {
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindowController (NSCoder coder) : base (coder)
        {
        }

        public MainWindowController () : base ("MainWindow")
        {
            // Construct the window from code here
            CGRect contentRect = new CGRect (0, 0, 1000, 500);
            base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);

            // Simulate Awaking from Nib
            Window.AwakeFromNib ();
        }

        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();
        }

        public new MainWindow Window {
            get { return (MainWindow)base.Window; }
        }
    }
}

```

Permita analizar los elementos clave de esta modificación.

En primer lugar, se define una nueva instancia de la `MainWindow` clase y se asigna a la propiedad del controlador de la ventana base `Window` :

```csharp
CGRect contentRect = new CGRect (0, 0, 1000, 500);
base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);
```

Definimos la ubicación de la ventana de la pantalla con un `CGRect` . Al igual que el sistema de coordenadas de la ventana, la pantalla define (0,0) como la esquina inferior izquierda. A continuación, definimos el estilo de la ventana mediante el operador **or** para combinar dos o más `NSWindowStyle` características:

```csharp
... (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable) ...
```

`NSWindowStyle`Están disponibles las siguientes características:

- Sin **borde** : la ventana no tendrá borde.
- **Titulado** : la ventana tendrá una barra de título.
- **Closable** : la ventana tiene un botón Cerrar y se puede cerrar.
- **Miniaturizable** : la ventana tiene un botón miniaturize y se puede minimizar.
- **Ajustable** : la ventana tendrá un botón cambiar tamaño y se podrá cambiar de tamaño.
- **Utilidad** : la ventana es una ventana de estilo de utilidad (panel).
- **DocModal** : Si la ventana es un panel, el documento será modal en lugar de modal del sistema.
- **NonactivatingPanel** : Si la ventana es un panel, no se convertirá en la ventana principal.
- **TexturedBackground** : la ventana tendrá un fondo con textura.
- Sin **escalar** : la ventana no se escalará.
- **UnifiedTitleAndToolbar** : las áreas de título y barra de herramientas de la ventana se combinarán.
- **HUD** : la ventana se mostrará como un panel de información emergente.
- **FullScreenWindow** : la ventana puede entrar en el modo de pantalla completa.
- **FullSizeContentView** : la vista de contenido de la ventana está detrás del título y el área de la barra de herramientas.

Las dos últimas propiedades definen el _tipo de almacenamiento en búfer_ para la ventana y si el dibujo de la ventana se va a diferir. Para obtener más información sobre `NSWindows` , consulte la [Introducción a](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) la documentación de Windows de Apple.

Por último, dado que la ventana no se está inflando a partir de un archivo. Storyboard o. Xib, necesitamos simularla en nuestro **MainWindowController.CS** llamando al método de Windows `AwakeFromNib` :

```csharp
Window.AwakeFromNib ();
```

Esto le permitirá codificar en la ventana como una ventana estándar cargada desde un archivo. Storyboard o. Xib.

### <a name="displaying-the-window"></a>Mostrar la ventana

Con el archivo. Storyboard o. Xib eliminado y modificados los archivos **MainWindow.CS** y **MainWindowController.CS** , usará la ventana de la misma forma que lo haría con cualquier ventana normal que se hubiera creado en la Interface Builder de Xcode con un archivo. Xib.

A continuación se creará una nueva instancia de la ventana y su controlador y se mostrará la ventana en la pantalla:

```csharp
private MainWindowController mainWindowController;
...

mainWindowController = new MainWindowController ();
mainWindowController.Window.MakeKeyAndOrderFront (this);
```

En este punto, si se ejecuta la aplicación y el botón hizo clic un par de veces, se mostrará lo siguiente:

![Ejecución de una aplicación de ejemplo](xibless-ui-images/run01.png "Ejecución de una aplicación de ejemplo")

## <a name="adding-a-code-only-window"></a>Agregar una ventana de solo código

Si deseamos agregar una ventana de xibless solo de código a una aplicación de Xamarin. Mac existente, haga clic con el botón derecho en el proyecto en el **Panel de solución** y seleccione **Agregar**  >  **nuevo archivo.**.. En el cuadro de diálogo **nuevo archivo** , elija la ventana de cacao de **Xamarin. Mac**  >  **con el controlador**, como se muestra a continuación:

![Agregar un nuevo controlador de ventana](xibless-ui-images/add01.png "Agregar un nuevo controlador de ventana")

Al igual que antes, eliminaremos el archivo default. Storyboard o. Xib del proyecto (en este caso, **SecondWindow. Xib**) y seguiremos los pasos descritos en la sección sobre cómo [cambiar una ventana para usar el código](#Switching_a_Window_to_use_Code) anterior para cubrir la definición de la ventana en el código.

## <a name="adding-a-ui-element-to-a-window-in-code"></a>Agregar un elemento de la interfaz de usuario a una ventana en el código

Tanto si una ventana se ha creado en código como si se ha cargado desde un archivo. Storyboard o. Xib, puede haber ocasiones en las que deseemos agregar un elemento de la interfaz de usuario a una ventana desde el código. Por ejemplo:

```csharp
var ClickMeButton = new NSButton (new CGRect (10, 10, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
MyWindow.ContentView.AddSubview (ClickMeButton);
```

En el código anterior se crea un nuevo `NSButton` y se agrega a la `MyWindow` instancia de la ventana para su presentación. Básicamente, cualquier elemento de la interfaz de usuario que se pueda definir en la Interface Builder de Xcode en un archivo. Storyboard o. Xib se puede crear en el código y mostrarse en una ventana.

## <a name="defining-the-menu-bar-in-code"></a>Definir la barra de menús en el código

Debido a las limitaciones actuales de Xamarin. Mac, no se sugiere que cree la barra de menús de la aplicación de Xamarin. Mac: `NSMenuBar` en el código, pero siga usando el archivo **Main. Storyboard** o **MainMenu. Xib** para definirlo. Dicho esto, puede Agregar y quitar menús y elementos de menú en el código de C#.

Por ejemplo, edite el archivo **AppDelegate.CS** y haga que el método tenga el `DidFinishLaunching` aspecto siguiente:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    mainWindowController = new MainWindowController ();
    mainWindowController.Window.MakeKeyAndOrderFront (this);

    // Create a Status Bar Menu
    NSStatusBar statusBar = NSStatusBar.SystemStatusBar;

    var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);
    item.Title = "Phrases";
    item.HighlightMode = true;
    item.Menu = new NSMenu ("Phrases");

    var address = new NSMenuItem ("Address");
    address.Activated += (sender, e) => {
        Console.WriteLine("Address Selected");
    };
    item.Menu.AddItem (address);

    var date = new NSMenuItem ("Date");
    date.Activated += (sender, e) => {
        Console.WriteLine("Date Selected");
    };
    item.Menu.AddItem (date);

    var greeting = new NSMenuItem ("Greeting");
    greeting.Activated += (sender, e) => {
        Console.WriteLine("Greetings Selected");
    };
    item.Menu.AddItem (greeting);

    var signature = new NSMenuItem ("Signature");
    signature.Activated += (sender, e) => {
        Console.WriteLine("Signature Selected");
    };
    item.Menu.AddItem (signature);
}
```

Lo anterior crea un menú de la barra de estado a partir del código y lo muestra cuando se inicia la aplicación. Para obtener más información sobre cómo trabajar con menús, vea la documentación de los [menús](~/mac/user-interface/menu.md) .

## <a name="summary"></a>Resumen

En este artículo se ha tomado una visión detallada de la creación de la interfaz de usuario de una aplicación de Xamarin. Mac en código C# en lugar de usar la Interface Builder de Xcode con archivos. Storyboard o. Xib.

## <a name="related-links"></a>Vínculos relacionados

- [MacXibless (ejemplo)](/samples/xamarin/mac-samples/macxibless)
- [Windows](~/mac/user-interface/window.md)
- [Menús](~/mac/user-interface/menu.md)
- [Directrices de la interfaz humana de macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Introducción a Windows](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/WinPanel/Introduction.html)