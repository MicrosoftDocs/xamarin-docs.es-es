---
title: Crear controles personalizados en Xamarin. Mac
description: En este documento se describe cómo crear controles personalizados en Xamarin. Mac. Muestra cómo crear el control personalizado, realizar un seguimiento de su estado, dibujar su interfaz, responder a los datos proporcionados por el usuario y usar el control en una aplicación.
ms.prod: xamarin
ms.assetid: 004534B1-5AEE-452C-BBBE-8C2673FD49B7
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 537816213208ed6e71f0986558c9a94a327759e2
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227906"
---
# <a name="creating-custom-controls-in-xamarinmac"></a>Crear controles personalizados en Xamarin. Mac

Al trabajar con C# y .net en una aplicación de Xamarin. Mac, tiene acceso a los mismos controles de usuario que un desarrollador que trabaja en *Objective-C*, *SWIFT* y *Xcode* . Como Xamarin. Mac se integra directamente con Xcode, puede usar los _Interface Builder_ de Xcode para crear y mantener los controles de usuario (o, opcionalmente, C# crearlos directamente en el código).

Aunque macOS proporciona una gran cantidad de controles de usuario integrados, puede haber ocasiones en las que necesite crear un control personalizado para proporcionar funcionalidad no integrada o que coincida con un tema personalizado de la interfaz de usuario (como una interfaz de juego).

[![](custom-controls-images/intro01.png "Ejemplo de un control de interfaz de usuario personalizado")](custom-controls-images/intro01.png#lightbox)

En este artículo, trataremos los aspectos básicos de la creación de un control de interfaz de usuario personalizado reutilizable en una aplicación de Xamarin. Mac. Se recomienda encarecidamente que trabaje primero en el artículo [Hello, Mac](~/mac/get-started/hello-mac.md) , específicamente en las secciones [Introducción a Xcode y Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) , ya que trata conceptos clave y técnicas que usaremos en Este artículo.

Es posible que desee echar un vistazo a la sección [exposición C# de clases y métodos a Objective-C](~/mac/internals/how-it-works.md) del documento [interno de Xamarin. Mac](~/mac/internals/how-it-works.md) también, en él se explican `Export` los `Register` comandos y que se usan para C# conectar las clases a Objetos de Objective-C y elementos de la interfaz de usuario.

<a name="Introduction-to-Outline-Views" />

## <a name="introduction-to-custom-controls"></a>Introducción a los controles personalizados

Como se indicó anteriormente, puede haber ocasiones en las que necesite crear un control de interfaz de usuario personalizado y reutilizable para proporcionar una funcionalidad única para la interfaz de usuario de la aplicación de Xamarin. Mac o para crear un tema de interfaz de usuario personalizado (como una interfaz de juego).

En estas situaciones, puede heredar fácilmente de `NSControl` y crear una herramienta personalizada que se puede Agregar a la interfaz de usuario de la C# aplicación a través del código o a través del Interface Builder de Xcode. La herencia del `NSControl` control personalizado tendrá automáticamente todas las características estándar que tiene un control de interfaz de usuario integrado ( `NSButton`por ejemplo,).

Si el control de la interfaz de usuario personalizada simplemente muestra información (como una herramienta gráfica y gráficos personalizados), es posible que desee `NSView` heredar de `NSControl`en lugar de.

Con independencia de la clase base que se use, los pasos básicos para crear un control personalizado son los mismos.

En este artículo, creará un componente personalizado del modificador Flip que proporciona un tema de interfaz de usuario único y un ejemplo de creación de un control de interfaz de usuario personalizado totalmente funcional.

<a name="Building-the-Custom-Control" />

## <a name="building-the-custom-control"></a>Compilar el control personalizado

Dado que el control personalizado que estamos creando responderá a la entrada del usuario (clics del botón primario del mouse), vamos a `NSControl`heredar de. De esta manera, nuestro control personalizado tendrá automáticamente todas las características estándar que tiene un control de interfaz de usuario integrado y responde como un control estándar de macOS.

En Visual Studio para Mac, abra el proyecto de Xamarin. Mac para el que desea crear un control de interfaz de usuario personalizado (o cree uno nuevo). Agregue una nueva clase y llámela `NSFlipSwitch`:

[![](custom-controls-images/custom01.png "Agregar una nueva clase")](custom-controls-images/custom01.png#lightbox)

A continuación, edite la `NSFlipSwitch.cs` clase y haga que tenga el aspecto siguiente:

```csharp
using Foundation;
using System;
using System.CodeDom.Compiler;
using AppKit;
using CoreGraphics;

namespace MacCustomControl
{
    [Register("NSFlipSwitch")]
    public class NSFlipSwitch : NSControl
    {
        #region Private Variables
        private bool _value = false;
        #endregion

        #region Computed Properties
        public bool Value {
            get { return _value; }
            set {
                // Save value and force a redraw
                _value = value;
                NeedsDisplay = true;
            }
        }
        #endregion

        #region Constructors
        public NSFlipSwitch ()
        {
            // Init
            Initialize();
        }

        public NSFlipSwitch (IntPtr handle) : base (handle)
        {
            // Init
            Initialize();
        }

        [Export ("initWithFrame:")]
        public NSFlipSwitch (CGRect frameRect) : base(frameRect) {
            // Init
            Initialize();
        }

        private void Initialize() {
            this.WantsLayer = true;
            this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
        }
        #endregion

        #region Draw Methods
        public override void DrawRect (CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

            // Use Core Graphic routines to draw our UI
            ...

        }
        #endregion

        #region Private Methods
        private void FlipSwitchState() {
            // Update state
            Value = !Value;
        }
        #endregion

    }
}
```

Lo primero que hay que tener en cuenta con respecto a nuestra clase personalizada en la `NSControl` que estamos heredando y usando el comando **Register** para exponer esta clase en Objective-C y en la Interface Builder de Xcode:

```csharp
[Register("NSFlipSwitch")]
public class NSFlipSwitch : NSControl
```

En las secciones siguientes, echaremos un vistazo al resto del código anterior en detalle.

<a name="Tracking-the-Controls-State" />

### <a name="tracking-the-controls-state"></a>Seguimiento del estado del control

Puesto que nuestro control personalizado es un conmutador, necesitamos una forma de realizar un seguimiento del estado de activación o desactivación del conmutador. Lo tratamos con el código siguiente en `NSFlipSwitch`:

```csharp
private bool _value = false;
...

public bool Value {
    get { return _value; }
    set {
        // Save value and force a redraw
        _value = value;
        NeedsDisplay = true;
    }
}
```

Cuando cambia el estado del cambio, necesitamos una manera de actualizar la interfaz de usuario. Esto se hace obligando al control a volver a dibujar su interfaz `NeedsDisplay = true`de usuario con.

Si nuestro control requería más que un único estado de activación o desactivación (por ejemplo, un conmutador de varios Estados con 3 posiciones), podríamos haber usado una enumeración para realizar el seguimiento del estado. En nuestro ejemplo, se realizará una simple **bool** .

También hemos agregado un método auxiliar para intercambiar el estado del conmutador entre on y OFF:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
}
```

Más adelante, expandiremos esta clase auxiliar para informar al autor de la llamada cuando haya cambiado el estado de los modificadores.

<a name="Drawing-the-Controls-Interface" />

### <a name="drawing-the-controls-interface"></a>Dibujar la interfaz del control

Vamos a usar rutinas de dibujo de gráficos principales para dibujar la interfaz de usuario del control personalizado en tiempo de ejecución. Antes de que podamos hacerlo, es necesario activar las capas para el control. Lo hacemos con el siguiente método privado:

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

Se llama a este método desde cada constructor del control para asegurarse de que el control está configurado correctamente. Por ejemplo:

```csharp
public NSFlipSwitch (IntPtr handle) : base (handle)
{
    // Init
    Initialize();
}
```

A continuación, es necesario invalidar `DrawRect` el método y agregar las rutinas gráficas básicas para dibujar el control:

```csharp
public override void DrawRect (CGRect dirtyRect)
{
    base.DrawRect (dirtyRect);

    // Use Core Graphic routines to draw our UI
    ...

}
```

Vamos a ajustar la representación visual del control cuando cambie su estado (por ejemplo, pasando de **on** a **OFF**). Cada vez que cambia el estado, podemos usar el `NeedsDisplay = true` comando para forzar que el control vuelva a dibujarse para el nuevo estado.

<a name="Responding-to-User-Input" />

### <a name="responding-to-user-input"></a>Responder a los datos proporcionados por el usuario

Hay dos formas básicas de agregar datos proporcionados por el usuario a nuestro control personalizado: **Invalida** las rutinas de control de mouse o los reconocedores de **gestos**. El método que usamos, se basará en la funcionalidad que requiere nuestro control.

> [!IMPORTANT]
> Para cualquier control personalizado que cree, debe usar métodos de **invalidación** _o_reconocedores de gestos, pero no ambos al mismo tiempo, ya que pueden entrar en conflicto entre sí.

<a name="Summary" />

#### <a name="handling-user-input-with-override-methods"></a>Controlar los datos proporcionados por el usuario mediante métodos de invalidación

Los objetos que heredan `NSControl` de `NSView`(o) tienen varios métodos de invalidación para controlar la entrada del mouse o del teclado. En nuestro control de ejemplo, queremos voltear el estado del conmutador entre **on** y **OFF** cuando el usuario hace clic en el control con el botón primario del mouse. Podemos agregar los siguientes métodos de invalidación a `NSFlipSwitch` la clase para controlar esto:

```csharp
#region Mouse Handling Methods
// --------------------------------------------------------------------------------
// Handle mouse with Override Methods.
// NOTE: Use either this method or Gesture Recognizers, NOT both!
// --------------------------------------------------------------------------------
public override void MouseDown (NSEvent theEvent)
{
    base.MouseDown (theEvent);

    FlipSwitchState ();
}

public override void MouseDragged (NSEvent theEvent)
{
    base.MouseDragged (theEvent);
}

public override void MouseUp (NSEvent theEvent)
{
    base.MouseUp (theEvent);
}

public override void MouseMoved (NSEvent theEvent)
{
    base.MouseMoved (theEvent);
}
## endregion
```

En el código anterior, `FlipSwitchState` llamamos al método (definido anteriormente) para voltear el estado ON/OFF del modificador en el `MouseDown` método. Esto también hará que se vuelva a dibujar el control para reflejar el estado actual.

<a name="Handling-User-Input-with-Gesture-Recognizers" />

#### <a name="handling-user-input-with-gesture-recognizers"></a>Controlar los datos proporcionados por el usuario con reconocedores de gestos

Opcionalmente, puede usar los reconocedores de gestos para controlar el usuario que interactúa con el control. Quite las invalidaciones agregadas anteriormente, edite el `Initialize` método y haga que tenga un aspecto similar al siguiente:

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;

    // --------------------------------------------------------------------------------
    // Handle mouse with Gesture Recognizers.
    // NOTE: Use either this method or the Override Methods, NOT both!
    // --------------------------------------------------------------------------------
    var click = new NSClickGestureRecognizer (() => {
        FlipSwitchState();
    });
    AddGestureRecognizer (click);
}
```

Aquí vamos a crear un nuevo `NSClickGestureRecognizer` y llamar a nuestro `FlipSwitchState` método para cambiar el estado del modificador cuando el usuario haga clic en él con el botón primario del mouse. El `AddGestureRecognizer (click)` método agrega el reconocedor de gestos al control.

De nuevo, el método que usamos depende de lo que estamos tratando de lograr con nuestro control personalizado. Si necesitamos tener acceso de bajo nivel a la interacción del usuario, use los métodos de invalidación. Si necesitamos una funcionalidad predefinida, como clics del mouse, use reconocedores de gestos.

<a name="Responding-to-State-Change-Events" />

### <a name="responding-to-state-change-events"></a>Responder a los eventos de cambio de estado

Cuando el usuario cambia el estado de nuestro control personalizado, necesitamos una manera de responder al cambio de estado en el código (por ejemplo, hacer algo cuando se hace clic en un botón personalizado).

Para proporcionar esta funcionalidad, edite `NSFlipSwitch` la clase y agregue el código siguiente:

```csharp
#region Events
public event EventHandler ValueChanged;

internal void RaiseValueChanged() {
    if (this.ValueChanged != null)
        this.ValueChanged (this, EventArgs.Empty);

    // Perform any action bound to the control from Interface Builder
    // via an Action.
    if (this.Action !=null)
        NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
}
## endregion
```

A continuación, edite el `FlipSwitchState` método y haga que tenga el aspecto siguiente:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
    RaiseValueChanged ();
}
```

En primer lugar, se `ValueChanged` proporciona un evento al que se puede Agregar un C# controlador en el código para que se pueda realizar una acción cuando el usuario cambie el estado del conmutador.

En segundo lugar, dado que el control personalizado `NSControl`hereda de, tiene automáticamente una **acción** que se puede asignar en la Interface Builder de Xcode. Para llamar a esta **acción** cuando cambia el estado, usamos el código siguiente:

```csharp
if (this.Action !=null)
    NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
```

En primer lugar, se comprueba si se ha asignado una **acción** al control. A continuación, se llama a la **acción** si se ha definido.

<a name="Using-the-Custom-Control" />

## <a name="using-the-custom-control"></a>Usar el control personalizado

Con nuestro control personalizado totalmente definido, podemos agregarlo a la interfaz de usuario de la aplicación de Xamarin. C# Mac mediante código o en la Interface Builder de Xcode.

Para agregar el control mediante Interface Builder, primero realice una compilación limpia del proyecto de Xamarin. Mac y, a continuación, haga `Main.storyboard` doble clic en el archivo para abrirlo en Interface Builder para editarlo:

[![](custom-controls-images/custom02.png "Editar el guion gráfico en Xcode")](custom-controls-images/custom02.png#lightbox)

A continuación, arrastre `Custom View` un al diseño de la interfaz de usuario:

[![](custom-controls-images/custom03.png "Selección de una vista personalizada de la biblioteca")](custom-controls-images/custom03.png#lightbox)

Con la vista personalizada aún seleccionada, cambie al **Inspector de identidad** y cambie la **clase** de la vista `NSFlipSwitch`a:

[![](custom-controls-images/custom04.png "Establecer la clase de la vista")](custom-controls-images/custom04.png#lightbox)

Cambie al **Editor del asistente** y cree una **salida** para el control personalizado (asegurándose de enlazarlo en el `ViewController.h` archivo y no en `.m` el archivo):

[![](custom-controls-images/custom05.png "Configuración de una nueva salida")](custom-controls-images/custom05.png#lightbox)

Guarde los cambios, vuelva a Visual Studio para Mac y permita que se sincronicen los cambios. Edite `ViewController.cs` el archivo y haga `ViewDidLoad` que el método tenga el aspecto siguiente:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Do any additional setup after loading the view.
    OptionTwo.ValueChanged += (sender, e) => {
        // Display the state of the option switch
        Console.WriteLine("Option Two: {0}", OptionTwo.Value);
    };
}
```

En este caso, se responde `ValueChanged` al evento que se definió `NSFlipSwitch` anteriormente en la clase y se escribe el **valor** actual cuando el usuario hace clic en el control.

Opcionalmente, podríamos volver a Interface Builder y definir una **acción** en el control:

[![](custom-controls-images/custom06.png "Configuración de una nueva acción")](custom-controls-images/custom06.png#lightbox)

De nuevo, edite el `ViewController.cs` archivo y agregue el siguiente método:

```csharp
partial void OptionTwoFlipped (Foundation.NSObject sender) {
    // Display the state of the option switch
    Console.WriteLine("Option Two: {0}", OptionTwo.Value);
}
```

> [!IMPORTANT]
> Debe utilizar el **evento** o definir una **acción** en Interface Builder, pero no debe usar ambos métodos al mismo tiempo o pueden entrar en conflicto entre sí.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se ha tomado una visión detallada de la creación de un control de interfaz de usuario personalizado reutilizable en una aplicación de Xamarin. Mac. Vimos cómo dibujar la interfaz de usuario de controles personalizados, las dos maneras principales de responder a la entrada del mouse y del usuario, y cómo exponer el nuevo control a las acciones en la Interface Builder de Xcode.

## <a name="related-links"></a>Vínculos relacionados

- [MacCustomControl (ejemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/maccustomcontrol)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Enlace de datos y codificación de clave-valor](~/mac/app-fundamentals/databinding.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
- [Controlar eventos del mouse](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/HandlingMouseEvents/HandlingMouseEvents.html)
