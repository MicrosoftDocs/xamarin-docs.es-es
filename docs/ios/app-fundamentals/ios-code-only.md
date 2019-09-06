---
title: Creación de Interfaces de usuario de iOS en código de Xamarin.iOS
description: Este documento describe cómo usar código para crear una interfaz de usuario para una aplicación de Xamarin.iOS. Describe los controladores de vista, creación de una jerarquía de vistas, control de un giro y mucho más.
ms.prod: xamarin
ms.assetid: 7CB1FEAE-0BB3-4CDC-9076-5BD555003F1D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 05/03/2018
ms.openlocfilehash: d1464ca3accf53a9362d00e279ff9e158d7e34d4
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70281631"
---
# <a name="creating-ios-user-interfaces-in-code-in-xamarinios"></a>Crear interfaces de usuario de iOS en código de Xamarin.iOS

La interfaz de usuario de una aplicación de iOS es como un escaparate: la aplicación normalmente obtiene una ventana, pero puede llenar la ventana con como muchos objetos como lo necesita, y los objetos y las organizaciones pueden cambiarse según la aplicación desea mostrar. Los objetos de este escenario, lo que el usuario ve, se denominan "vistas". Para crear una sola pantalla en una aplicación, las vistas se apilan unas sobre otras en una jerarquía de vistas de contenido y la jerarquía está administrada por un único controlador de vista. Las aplicaciones con varias pantallas tienen varias jerarquías de vistas de contenido, cada una con su propio controlador de vistas. La aplicación coloca las vistas en la ventana para crear una jerarquía de vistas de contenido diferente basándose en la pantalla en la que se encuentra el usuario.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

En el diagrama siguiente se muestran las relaciones entre la ventana, las vistas, las subvistas y el controlador de vistas que, de forma conjunta, proporcionan la interfaz de usuario a la pantalla del dispositivo:

[![](ios-code-only-images/image9.png "Este diagrama ilustra las relaciones entre la ventana, vistas, las subvistas y controlador de vista")](ios-code-only-images/image9.png#lightbox)

Estas jerarquías de vista pueden crearse mediante el [diseñador Xamarin para iOS](~/ios/user-interface/designer/index.md) pero en Visual Studio, es conveniente tener un entendimiento básico de cómo funcionan completamente en código. En este artículo le guía a través de algunos puntos básicos para poner en marcha y ejecución con el desarrollo de la interfaz de usuario de solo código.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

En el diagrama siguiente se muestran las relaciones entre la ventana, las vistas, las subvistas y el controlador de vistas que, de forma conjunta, proporcionan la interfaz de usuario a la pantalla del dispositivo:

[![](ios-code-only-images/image9.png "Este diagrama ilustra las relaciones entre la ventana, vistas, las subvistas y controlador de vista")](ios-code-only-images/image9.png#lightbox)

Estas jerarquías de vista pueden crearse mediante el [diseñador Xamarin para iOS](~/ios/user-interface/designer/index.md) pero en Visual Studio para Mac, es conveniente tener un entendimiento básico de cómo funcionan completamente en código. En este artículo le guía a través de algunos puntos básicos para poner en marcha y ejecución con el desarrollo de la interfaz de usuario de solo código.

-----

## <a name="creating-a-code-only-project"></a>Crear un proyecto de código

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="ios-blank-project-template"></a>plantilla de proyecto en blanco de iOS

En primer lugar, cree un proyecto de iOS en Visual Studio mediante el **archivo > Nuevo proyecto > Visual C# > iPhone & iPad > iOS (Xamarin) de la aplicación** proyecto, se muestra a continuación:

[![Cuadro de diálogo nuevo proyecto](ios-code-only-images/blankapp.w157-sml.png)](ios-code-only-images/blankapp.w157.png#lightbox)

A continuación, seleccione el **aplicación vacía** plantilla de proyecto:

[![Seleccione un cuadro de diálogo de plantilla](ios-code-only-images/blankapp-2.w157-sml.png)](ios-code-only-images/blankapp-2.w157.png#lightbox)

La plantilla proyecto vacío agrega 4 archivos al proyecto:

[![Archivos de proyecto](ios-code-only-images/empty-project.w157-sml.png "archivos de proyecto")](ios-code-only-images/empty-project.w157.png#lightbox)

1. **AppDelegate.cs** -contiene un `UIApplicationDelegate` subclase, `AppDelegate` , que se usa para controlar eventos de aplicación de iOS. La ventana de la aplicación se crea en el `AppDelegate`del `FinishedLaunching` método.
1. **Main.cs** -contiene el punto de entrada para la aplicación, que especifica la clase para el `AppDelegate` .
1. **Info.plist** -archivo de lista de propiedades que contiene información de configuración de aplicación.
1. **Entitlements.plist** : archivo de lista de propiedades que contiene información sobre las capacidades y los permisos de la aplicación.


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="ios-templates"></a>plantillas de iOS


Visual Studio para Mac no proporciona una plantilla vacía. Todas las plantillas incluyen compatibilidad con guiones gráficos, que Apple recomienda como medio principal para crear una interfaz de usuario. Sin embargo, es posible crear la interfaz de usuario completamente en código.

Los pasos siguientes le guiarán en el proceso de quitar el guión gráfico de una aplicación:


1. Use la plantilla de aplicación de vista única para crear un nuevo proyecto de iOS:

    [![](ios-code-only-images/single-view-app.png "Utilice la plantilla de aplicación de vista única")](ios-code-only-images/single-view-app.png#lightbox)

1. Eliminar el `Main.Storyboard` y `ViewController.cs` archivos. Hacer **no** eliminar el `LaunchScreen.Storyboard`. El controlador de vista deben eliminarse, ya que es el código subyacente para el controlador de vista que se crea en el guión gráfico:
1. Asegúrese de seleccionar **eliminar** desde el cuadro de diálogo emergente:

    [![](ios-code-only-images/delete.png "Seleccione Eliminar en el cuadro de diálogo emergente")](ios-code-only-images/delete.png#lightbox)

1. En Info.plist, elimine la información dentro de la **información de implementación > interfaz principal** opción:

    [![](ios-code-only-images/main-interface.png "Eliminar la información dentro de la opción de la interfaz principal")](ios-code-only-images/main-interface.png#lightbox)

1. Por último, agregue el código siguiente a su `FinishedLaunching` método en la clase AppDelegate:

    ```csharp
    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
    ```

El código que se agregó a la `FinishedLaunching` método en el paso 5 anterior, es la cantidad mínima de código necesario para crear una ventana de la aplicación de iOS.

-----

las aplicaciones de iOS se crean mediante el [patrón MVC](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md#model-view-controller-mvc). Se crea la primera pantalla que muestra una aplicación de controlador de vista de la ventana raíz. Consulte la [Hello, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/index.md) guía para obtener más información sobre MVC propio patrón.

La implementación para el `AppDelegate` agregados por la plantilla crea la ventana de la aplicación, de que hay solo uno para cada aplicación de iOS y lo hace visible con el código siguiente:

```csharp
public class AppDelegate : UIApplicationDelegate
{
    public override UIWindow Window
            {
                get;
                set;
            }

    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

Si fuese a ejecutar esta aplicación ahora, probablemente obtendría una excepción que indica que `Application windows are expected to have a root view controller at the end of application launch`. Vamos a agregar un controlador y convertirlo en controlador de vista raíz de la aplicación.

## <a name="adding-a-controller"></a>Agregar un controlador

La aplicación puede contener muchos controladores de vista, pero debe tener un controlador de vista raíz para controlar todos los controladores de vista.  Agregar un controlador a la ventana mediante la creación de un `UIViewController` instancia y estableciéndolo en el `Window.RootViewController` propiedad:

```csharp
public class AppDelegate : UIApplicationDelegate
{
    // class-level declarations

    public override UIWindow Window
    {
        get;
        set;
    }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        var controller = new UIViewController();
        controller.View.BackgroundColor = UIColor.LightGray;

        Window.RootViewController = controller;

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

Cada controlador tiene una vista asociada, que es accesible desde el `View` propiedad. El código anterior cambia la vista `BackgroundColor` propiedad `UIColor.LightGray` para que sea visible, como se muestra a continuación:

 [![](ios-code-only-images/image1.png "Fondo de la vista es un gris claro visible")](ios-code-only-images/image1.png#lightbox)

Se puede definir cualquier `UIViewController` subclase como el `RootViewController` de esta manera, incluidos los controladores de UIKit, así como las escribimos nosotros mismos. Por ejemplo, el código siguiente agrega un `UINavigationController` como el `RootViewController`:

```csharp
public class AppDelegate : UIApplicationDelegate
{
    // class-level declarations

    public override UIWindow Window
    {
        get;
        set;
    }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        var controller = new UIViewController();
        controller.View.BackgroundColor = UIColor.LightGray;
        controller.Title = "My Controller";

        var navController = new UINavigationController(controller);

        Window.RootViewController = navController;

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

Esto genera el controlador anidado en el controlador de navegación, como se muestra a continuación:

 [![](ios-code-only-images/image2.png "El controlador anidado en el controlador de navegación")](ios-code-only-images/image2.png#lightbox)

## <a name="creating-a-view-controller"></a>Crear un controlador de vista

Ahora que hemos visto cómo agregar un controlador como el `RootViewController` de la ventana, veamos cómo crear un controlador de vista personalizada en código.

Agregar una nueva clase denominada `CustomViewController` tal como se muestra a continuación:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](ios-code-only-images/customviewcontroller.w157-sml.png "Agregar una nueva clase denominada CustomViewController")](ios-code-only-images/customviewcontroller.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](ios-code-only-images/new-file.png "Agregar una nueva clase denominada CustomViewController")](ios-code-only-images/new-file.png#lightbox)

-----

La clase debe heredar de `UIViewController`, que se encuentra en la `UIKit` espacio de nombres, tal como se muestra:

```csharp
using System;
using UIKit;

namespace CodeOnlyDemo
{
    class CustomViewController : UIViewController
    {
    }
}
```

## <a name="initializing-the-view"></a>Inicializar la vista

`UIViewController` contiene un método llamado `ViewDidLoad` que se llama cuando se carga el controlador de vista por primera vez en la memoria. Se trata de un lugar adecuado para realizar la inicialización de la vista, por ejemplo, configurar sus propiedades.

Por ejemplo, el código siguiente agrega un botón y un controlador de eventos para insertar un nuevo controlador de vista en la pila de navegación cuando se presiona el botón:

```csharp
using System;
using CoreGraphics;
using UIKit;

namespace CodyOnlyDemo
{
    public class CustomViewController : UIViewController
    {
        public CustomViewController ()
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            View.BackgroundColor = UIColor.White;
            Title = "My Custom View Controller";

            var btn = UIButton.FromType (UIButtonType.System);
            btn.Frame = new CGRect (20, 200, 280, 44);
            btn.SetTitle ("Click Me", UIControlState.Normal);

            var user = new UIViewController ();
            user.View.BackgroundColor = UIColor.Magenta;

            btn.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (user, true);
            };

            View.AddSubview (btn);

        }
    }
}
```

Para cargar este controlador en la aplicación y mostrar el panel de navegación sencillo, crear una nueva instancia de `CustomViewController`. Crear un nuevo controlador de navegación, pase la instancia de controlador de vista y establezca el nuevo controlador de navegación a la ventana `RootViewController` en el `AppDelegate` como antes:

```csharp
var cvc = new CustomViewController ();

var navController = new UINavigationController (cvc);

Window.RootViewController = navController;
```

Ahora cuando cargue la aplicación, el `CustomViewController` se carga dentro de un controlador de navegación:

 [![](ios-code-only-images/customvc.png "Se carga el CustomViewController dentro de un controlador de navegación")](ios-code-only-images/customvc.png#lightbox)

Al hacer clic en el botón, le _inserción_ un nuevo controlador de vista en la pila de navegación:

[![](ios-code-only-images/customvca.png "Inserta un nuevo controlador de vista en la pila de navegación")](ios-code-only-images/customvca.png#lightbox)

## <a name="building-the-view-hierarchy"></a>Creación de la jerarquía de vistas

En el ejemplo anterior, comenzamos a crear una interfaz de usuario en el código mediante la adición de un botón en el controlador de vista.

interfaces de usuario de iOS se componen de una jerarquía de vistas. Vistas adicionales, como etiquetas, botones, controles deslizantes, etc. se agregan como subvistas de algunos vista primaria.

Por ejemplo, vamos a editar el `CustomViewController` para crear una pantalla de inicio de sesión donde el usuario puede escribir un nombre de usuario y una contraseña. La pantalla constará de dos campos de texto y un botón.

### <a name="adding-the-text-fields"></a>Agregar los campos de texto

En primer lugar, quite el controlador de botón y eventos que se agregó en el [inicializar la vista](#initializing-the-view) sección. 

Agregar un control para el nombre de usuario, crear e inicializar un `UITextField` y, a continuación, agregarlo a la jerarquía de vistas, tal como se muestra a continuación:

```csharp
class CustomViewController : UIViewController
{
    UITextField usernameField;

    public override void ViewDidLoad()
    {
        base.ViewDidLoad();

        View.BackgroundColor = UIColor.Gray;

        nfloat h = 31.0f;
        nfloat w = View.Bounds.Width;

        usernameField = new UITextField
        {
            Placeholder = "Enter your username",
            BorderStyle = UITextBorderStyle.RoundedRect,
            Frame = new CGRect(10, 82, w - 20, h)
        };

        View.AddSubview(usernameField);
    }
}
```

Cuando se crea el `UITextField`, establecemos el `Frame` propiedad para definir su ubicación y tamaño. En iOS la coordenada 0,0 es la esquina superior izquierda con + x a la derecha y + y hacia abajo. Después de establecer el `Frame` junto con un par de otro propiedades, llamamos a `View.AddSubview` para agregar la `UITextField` a la jerarquía de vistas. Esto hace que el `usernameField` una vista secundaria de la `UIView` de instancia que el `View` referencias de propiedad. Se agrega una vista secundaria con un orden z es mayor que esta vista primaria, por lo que aparece delante de la vista primaria en la pantalla.

La aplicación con el `UITextField` incluido se muestra a continuación:

 [![](ios-code-only-images/image4.png "La aplicación con la interfaz de usuario incluido")](ios-code-only-images/image4.png#lightbox)

Podemos agregar un `UITextField` la contraseña de forma similar, pero esta vez hemos establecido el `SecureTextEntry` la propiedad en true, tal como se muestra a continuación:

```csharp
public class CustomViewController : UIViewController
{
    UITextField usernameField, passwordField;
    public override void ViewDidLoad()
    {
       // keep the code the username UITextField
        passwordField = new UITextField
        {
            Placeholder = "Enter your password",
            BorderStyle = UITextBorderStyle.RoundedRect,
            Frame = new CGRect(10, 114, w - 20, h),
            SecureTextEntry = true
        };

      View.AddSubview(usernameField);
      View.AddSubview(passwordField);
   }
}

```

Establecer `SecureTextEntry = true` oculta el texto escrito en el `UITextField` por el usuario, como se muestra a continuación:

 [![](ios-code-only-images/image4a.png "Establecer SecureTextEntry es true, oculta el texto escrito por el usuario")](ios-code-only-images/image4a.png#lightbox)

### <a name="adding-the-button"></a>Agregar el botón

A continuación, vamos a agregar un botón, por lo que el usuario puede enviar el nombre de usuario y la contraseña. El botón se agrega a la jerarquía de vistas como cualquier otro control, al pasarlo como argumento a la vista primaria `AddSubview` nuevo al método.

El código siguiente agrega el botón y se registra un controlador de eventos para el `TouchUpInside` eventos:

```csharp
var submitButton = UIButton.FromType (UIButtonType.RoundedRect);

submitButton.Frame = new CGRect (10, 170, w - 20, 44);
submitButton.SetTitle ("Submit", UIControlState.Normal);

submitButton.TouchUpInside += (sender, e) => {
    Console.WriteLine ("Submit button pressed");
};

View.AddSubview(submitButton);
```

Teniendo esto en su lugar, la pantalla de inicio de sesión aparece ahora como se muestra a continuación:

 [![](ios-code-only-images/image5.png "La pantalla de inicio de sesión")](ios-code-only-images/image5.png#lightbox)

A diferencia de las versiones anteriores de iOS, el fondo del botón predeterminado es transparente. Modificación del botón `BackgroundColor` Esto cambia la propiedad:

```csharp
submitButton.BackgroundColor = UIColor.White;
```

Esto dará como resultado en un botón cuadrado en lugar de redondea el típico con filo de botón. Para obtener el borde redondeado, use el siguiente fragmento de código:

```csharp
submitButton.Layer.CornerRadius = 5f;
```

Con estos cambios, la vista tendrá este aspecto:

[![](ios-code-only-images/image6.png "Ejecución de un ejemplo de la vista")](ios-code-only-images/image6.png#lightbox)

## <a name="adding-multiple-views-to-the-view-hierarchy"></a>Agregar varias vistas a la jerarquía de vistas

iOS proporciona una utilidad para agregar varias vistas a la jerarquía de vistas con `AddSubviews`.

```csharp
View.AddSubviews(new UIView[] { usernameField, passwordField, submitButton });
```

## <a name="adding-button-functionality"></a>Agregar la funcionalidad de botón

Cuando se hace clic en un botón, los usuarios esperarán algún evento. Por ejemplo, se muestra una alerta o se realiza la navegación a otra pantalla.

Vamos a agregar algo de código para insertar un segundo controlador de vista en la pila de navegación.

En primer lugar, cree el segundo controlador de vista:

```csharp
var loginVC = new UIViewController () { Title = "Login Success!"};
loginVC.View.BackgroundColor = UIColor.Purple;
```

A continuación, agregue la funcionalidad para el `TouchUpInside` eventos:

```csharp
submitButton.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (loginVC, true);
            };
```

El panel de navegación se muestra a continuación:

[![](ios-code-only-images/navigation.png "El panel de navegación se muestra en este gráfico")](ios-code-only-images/navigation.png#lightbox)

Tenga en cuenta que de forma predeterminada, cuando se usa un controlador de navegación, iOS proporciona a la aplicación de una barra de navegación y un botón Atrás para que pueda retroceder a través de la pila.

## <a name="iterating-through-the-view-hierarchy"></a>Recorrer en iteración la jerarquía de vistas

Es posible recorrer la jerarquía de la vista secundaria y seleccionar una vista en particular. Por ejemplo, para buscar cada `UIButton` y asigne a ese botón otro `BackgroundColor`, se puede usar el siguiente fragmento de código

```csharp
foreach(var subview in View.Subviews)
{
    if (subview is UIButton)
    {
         var btn = subview as UIButton;
         btn.BackgroundColor = UIColor.Green;
    }
}
```

Esto, sin embargo no funcionará si la vista que se recorre para es un `UIView` como todas las vistas se devolverá como un `UIView` como los objetos agregados a la vista primaria a sí mismos heredan `UIView`.

## <a name="handling-rotation"></a>Control de giro

Si el usuario gira el dispositivo a la horizontal, los controles no cambian de tamaño como corresponda, como se muestra en la captura de pantalla siguiente:

[![](ios-code-only-images/image7.png "Si el usuario gira el dispositivo a la horizontal, los controles no cambian de tamaño correctamente")](ios-code-only-images/image7.png#lightbox)

Es una manera de solucionar este problema estableciendo la `AutoresizingMask` propiedad en cada vista. En este caso queremos que los controles que se va a expandir horizontalmente, por lo que debemos configurar cada `AutoresizingMask`. El ejemplo siguiente es para `usernameField`, pero tendría el mismo que se aplicará a cada gadget en la jerarquía de vistas.

```csharp
usernameField.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
```

Ahora cuando se gira el dispositivo o el simulador, todo lo que se expande para rellenar el espacio adicional, como se muestra a continuación:

[![](ios-code-only-images/image8.png "Todos los controles se ajustan para rellenar el espacio adicional")](ios-code-only-images/image8.png#lightbox)

## <a name="creating-custom-views"></a>Crear vistas personalizadas

Además de utilizar los controles que forman parte de UIKit, también se pueden usar vistas personalizadas. Se puede crear una vista personalizada heredando de `UIView` e invalidar `Draw`. Vamos a crear una vista personalizada y agregarla a la jerarquía de vistas para mostrar.

### <a name="inheriting-from-uiview"></a>Heredar de UIView

Lo primero que debemos hacer es crear una clase para la vista personalizada. Haremos esto mediante la **clase** plantilla en Visual Studio para agregar una clase vacía denominada `CircleView`. La clase base se debe establecer en `UIView`, que se recuerde está en el `UIKit` espacio de nombres. También necesitaremos el `System.Drawing` también espacio de nombres. Los otros diversos `System.*` no será espacios de nombres utilizado en este ejemplo, así que no dude quitarlos.

La clase debe tener este aspecto:

```csharp
using System;

namespace CodeOnlyDemo
{
    class CircleView : UIView
    {
    }
}
```

### <a name="drawing-in-a-uiview"></a>Dibujar en un UIView

Cada `UIView` tiene un `Draw` método llamado por el sistema cuando sea necesario dibujar. `Draw` nunca se debería llamar directamente. Es llamado por el sistema durante el procesamiento de bucle de ejecución. La primera vez a través del bucle de ejecución después de agrega una vista a la jerarquía de vistas, su `Draw` se llama al método. Las llamadas subsiguientes a `Draw` se producen cuando la vista está marcada como que necesitan dibujar llamando `SetNeedsDisplay` o `SetNeedsDisplayInRect` en la vista.

Podemos agregar código de dibujo para nuestra vista agregando este código dentro de invalidado `Draw` método tal y como se muestra a continuación:

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    //get graphics context
    using (var g = UIGraphics.GetCurrentContext())
    {
        // set up drawing attributes
        g.SetLineWidth(10.0f);
        UIColor.Green.SetFill();
        UIColor.Blue.SetStroke();

        // create geometry
        var path = new CGPath();
        path.AddArc(Bounds.GetMidX(), Bounds.GetMidY(), 50f, 0, 2.0f * (float)Math.PI, true);

        // add geometry to graphics context and draw
        g.AddPath(path);
        g.DrawPath(CGPathDrawingMode.FillStroke);
    }
}
```

Puesto que `CircleView` es un `UIView`, también podemos establecer `UIView` también propiedades. Por ejemplo, podemos establecer el `BackgroundColor` en el constructor:

```csharp
public CircleView()
{
    BackgroundColor = UIColor.White;
}
```

Para usar el `CircleView` que acabamos de crear, podemos o agregarlo a una vista secundaria a la jerarquía de vistas en un controlador existente, como hicimos con el `UILabels` y `UIButton` anteriormente, o se puede cargar como la vista de un nuevo controlador. Vamos a hacer esto último.

### <a name="loading-a-view"></a>Carga una vista

 `UIViewController` tiene un método denominado `LoadView` que se llama por el controlador para crear su vista. Se trata de un lugar adecuado para crear una vista y asígnela a del controlador `View` propiedad.

En primer lugar, necesitamos un controlador, por lo tanto, crear una nueva clase vacía denominada `CircleController`.

En `CircleController` agregue el código siguiente para establecer el `View` a un `CircleView` (no debe llamar a la `base` implementación en la invalidación):

```csharp
using UIKit;

namespace CodeOnlyDemo
{
    class CircleController : UIViewController
    {
        CircleView view;

        public override void LoadView()
        {
            view = new CircleView();
            View = view;
        }
    }
}
```

Por último, necesitamos presentar el controlador en tiempo de ejecución. Vamos a ello, agregue un controlador de eventos en el botón Enviar que agregamos anteriormente, como se indica a continuación:

```csharp
submitButton.TouchUpInside += delegate
{
    Console.WriteLine("Submit button clicked");

    //circleController is declared as class variable
    circleController = new CircleController();
    PresentViewController(circleController, true, null);
};
```

Ahora, cuando se ejecute la aplicación y pulse el botón Enviar, se muestra la nueva vista con un círculo:

[![](ios-code-only-images/circles.png "Se muestra la nueva vista con un círculo")](ios-code-only-images/circles.png#lightbox)

## <a name="creating-a-launch-screen"></a>Crear una pantalla de inicio

Un [pantalla de inicio](~/ios/app-fundamentals/images-icons/launch-screens.md) se muestra cuando se inicia la aplicación como una forma de mostrar a los usuarios que está respondiendo. Como se muestra una pantalla de inicio cuando se carga la aplicación, no se puede crear en código como la aplicación todavía se está cargando en memoria.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Cuando se crea un proyecto de iOS en Visual Studio, una pantalla de inicio se proporciona en forma de un archivo .xib, que puede encontrarse en el **recursos** carpeta dentro de su proyecto.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Cuando se crea un proyecto de iOS en Visual Studio para Mac, se proporciona una pantalla de inicio en forma de un archivo de guión gráfico.

-----

Esto se puede editar mediante doble clic en él y abrirlo en el Diseñador de iOS.

Apple recomienda que un archivo de guión gráfico o .xib se usa para las aplicaciones destinadas a iOS 8 o posterior, al iniciar cualquier archivo en el Diseñador de iOS, usará las clases de tamaño y el diseño automático para adaptar el diseño para que se ve bien y se muestra correctamente, para todo el dispositivo tamaños. Una imagen de inicio estático que se puede usar además de un guión gráfico o un .xib para permitir la compatibilidad para las aplicaciones destinadas a versiones anteriores.

Para obtener más información sobre cómo crear una pantalla de inicio, consulte los siguientes documentos:

- [Crear una pantalla de inicio mediante un .xib](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)
- [Administración de las pantallas de inicio con guiones gráficos](~/ios/app-fundamentals/images-icons/launch-screens.md)

> [!IMPORTANT]
> A partir de iOS 9, Apple recomienda que se deben usar guiones gráficos como método principal de la creación de una pantalla de inicio.

### <a name="creating-a-launch-image-for-pre-ios-8-applications"></a>Creación de una imagen de inicio para previa iOS 8 aplicaciones

Si su aplicación tiene como destino las versiones anteriores a iOS 8, una imagen estática puede usarse además de un .xib o la pantalla de inicio del guión gráfico.

Esta imagen estática se puede establecer en el archivo Info.plist, o como un catálogo de recursos (para iOS 7) en la aplicación. Deberá proporcionar imágenes independientes para cada tamaño de dispositivo (320 x 480, 960 x 640, 640 x 1136) que puede ejecutar su aplicación. Para obtener más información sobre los tamaños de pantalla de inicio, ver el [imágenes de pantalla de inicio](~/ios/app-fundamentals/images-icons/launch-screens.md) guía.

> [!IMPORTANT]
> Si la aplicación no tiene que ninguna pantalla de inicio, es posible que tenga en cuenta que no caben completamente la pantalla. Si este es el caso, debe asegurarse de que deben incluir, al menos, una imagen de 640 x 1136 denominada `Default-568@2x.png` al archivo Info.plist.

## <a name="summary"></a>Resumen

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

En este artículo se describe cómo desarrollar aplicaciones de iOS mediante programación en Visual Studio. Hemos visto cómo crear un proyecto desde una plantilla de proyecto vacío, traba sobre cómo crear y agregar un controlador de vista raíz a la ventana. A continuación, mostramos cómo usar controles de UIKit para crear una jerarquía de vistas dentro de un controlador para desarrollar una pantalla de la aplicación. A continuación, examinamos cómo hacer que las vistas de diseñar adecuadamente en diferentes orientaciones y vimos cómo crear una vista personalizada mediante la creación de subclases `UIView`, así como para cargar la vista dentro de un controlador. Por último, exploramos cómo agregar una pantalla de inicio a una aplicación.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

En este artículo se describe cómo desarrollar aplicaciones de iOS mediante programación en Visual Studio para Mac. Hemos visto cómo crear un proyecto de una plantilla de vista única traba sobre cómo crear y agregar un controlador de vista raíz a la ventana. A continuación, mostramos cómo usar controles de UIKit para crear una jerarquía de vistas dentro de un controlador para desarrollar una pantalla de la aplicación. A continuación, examinamos cómo hacer que las vistas de diseñar adecuadamente en diferentes orientaciones y vimos cómo crear una vista personalizada mediante la creación de subclases `UIView`, así como para cargar la vista dentro de un controlador. Por último, exploramos cómo agregar una pantalla de inicio a una aplicación.

-----

## <a name="related-links"></a>Vínculos relacionados

- [SimpleLogin (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/simplelogin)
