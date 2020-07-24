---
title: Creación de interfaces de usuario de iOS en código en Xamarin. iOS
description: En este documento se describe cómo usar código para compilar una interfaz de usuario para una aplicación Xamarin. iOS. Describe los controladores de vista, la creación de una jerarquía de vistas, el control de una rotación, etc.
ms.prod: xamarin
ms.assetid: 7CB1FEAE-0BB3-4CDC-9076-5BD555003F1D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/03/2018
ms.openlocfilehash: edd49cc891a86d3323bab319ab811e85f9148640
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997103"
---
# <a name="creating-ios-user-interfaces-in-code-in-xamarinios"></a>Creación de interfaces de usuario de iOS en código en Xamarin. iOS

La interfaz de usuario de una aplicación de iOS es como un escaparate; normalmente, la aplicación obtiene una ventana, pero puede rellenar la ventana con tantos objetos como sea necesario, y los objetos y las disposiciones se pueden cambiar en función de lo que la aplicación quiera mostrar. Los objetos de este escenario, lo que el usuario ve, se denominan "vistas". Para compilar una sola pantalla en una aplicación, las vistas se apilan unas sobre otras en una jerarquía de vistas de contenido, que esta, a su vez, se administra a través de un controlador de vistas único. Las aplicaciones con varias pantallas tienen varias jerarquías de vistas de contenido, cada una con su propio controlador de vistas. La aplicación coloca las vistas en la ventana para crear una jerarquía de vistas de contenido diferente basándose en la pantalla en la que se encuentra el usuario.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

En el diagrama siguiente se muestran las relaciones entre la ventana, las vistas, las subvistas y el controlador de vistas que, de forma conjunta, proporcionan la interfaz de usuario a la pantalla del dispositivo:

[![En este diagrama se muestran las relaciones entre la ventana, las vistas, las subvistas y el controlador de vista.](ios-code-only-images/image9.png)](ios-code-only-images/image9.png#lightbox)

Estas jerarquías de vistas se pueden construir mediante el [Xamarin Designer para iOS](~/ios/user-interface/designer/index.md) en Visual Studio, pero es conveniente tener una comprensión fundamental de cómo trabajar completamente en el código. Este artículo le guía a través de algunos puntos básicos para ponerse en marcha con el desarrollo de la interfaz de usuario de solo código.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

En el diagrama siguiente se muestran las relaciones entre la ventana, las vistas, las subvistas y el controlador de vistas que, de forma conjunta, proporcionan la interfaz de usuario a la pantalla del dispositivo:

[![En este diagrama se muestran las relaciones entre la ventana, las vistas, las subvistas y el controlador de vista.](ios-code-only-images/image9.png)](ios-code-only-images/image9.png#lightbox)

Estas jerarquías de vistas se pueden construir mediante el [Xamarin Designer para iOS](~/ios/user-interface/designer/index.md) en Visual Studio para Mac, pero es conveniente tener un conocimiento fundamental de cómo trabajar completamente en el código. Este artículo le guía a través de algunos puntos básicos para ponerse en marcha con el desarrollo de la interfaz de usuario de solo código.

-----

## <a name="creating-a-code-only-project"></a>Crear un proyecto de solo código

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="ios-blank-project-template"></a>plantilla de proyecto en blanco de iOS

En primer lugar, cree un proyecto de iOS en Visual Studio con el **archivo > nuevo proyecto > proyecto de Visual C# > iPhone & iPad > aplicación de iOS (Xamarin)** , que se muestra a continuación:

[![Cuadro de diálogo nuevo proyecto](ios-code-only-images/blankapp.w157-sml.png)](ios-code-only-images/blankapp.w157.png#lightbox)

Después, seleccione la plantilla de proyecto **aplicación vacía** :

[![Cuadro de diálogo Seleccionar plantilla](ios-code-only-images/blankapp-2.w157-sml.png)](ios-code-only-images/blankapp-2.w157.png#lightbox)

La plantilla de proyecto vacía agrega 4 archivos al proyecto:

[![Archivos de proyecto](ios-code-only-images/empty-project.w157-sml.png "Archivos de proyecto")](ios-code-only-images/empty-project.w157.png#lightbox)

1. **AppDelegate.CS** : contiene una `UIApplicationDelegate` subclase, `AppDelegate` , que se usa para controlar los eventos de aplicación de iOS. La ventana de la aplicación se crea en el `AppDelegate` `FinishedLaunching` método de.
1. **Main.CS** : contiene el punto de entrada de la aplicación, que especifica la clase para `AppDelegate` .
1. **Info. plist** : archivo de lista de propiedades que contiene información de configuración de la aplicación.
1. **Contitles. plist** : archivo de lista de propiedades que contiene información sobre las capacidades y los permisos de la aplicación.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

## <a name="ios-templates"></a>plantillas de iOS

Visual Studio para Mac no proporciona una plantilla vacía. Todas las plantillas tienen compatibilidad con guiones gráficos, que recomienda Apple como la principal manera de crear una interfaz de usuario. Sin embargo, es posible crear la interfaz de usuario completamente en el código.

Los pasos siguientes le guían por el proceso de eliminación del guion gráfico de una aplicación:

1. Use la plantilla de aplicación de vista única para crear un nuevo proyecto de iOS:

    [![Usar la plantilla de aplicación de vista única](ios-code-only-images/single-view-app.png)](ios-code-only-images/single-view-app.png#lightbox)

1. Elimine `Main.Storyboard` los `ViewController.cs` archivos y. **No** elimine el `LaunchScreen.Storyboard` . El controlador de vista debe eliminarse, ya que es el código subyacente para el controlador de vista que se crea en el guión gráfico:
1. Asegúrese de seleccionar **eliminar** en el cuadro de diálogo emergente:

    [![Seleccione eliminar en el cuadro de diálogo emergente.](ios-code-only-images/delete.png)](ios-code-only-images/delete.png#lightbox)

1. En info. plist, elimine la información incluida en la **información de implementación > opción de interfaz principal** :

    [![Eliminar la información incluida en la opción de interfaz principal](ios-code-only-images/main-interface.png)](ios-code-only-images/main-interface.png#lightbox)

1. Por último, agregue el código siguiente al `FinishedLaunching` método en la clase AppDelegate:

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

El código que se agregó al `FinishedLaunching` método en el paso 5 anterior, es la cantidad mínima de código necesaria para crear una ventana para la aplicación de iOS.

-----

las aplicaciones de iOS se compilan con el [patrón MVC](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md#model-view-controller-mvc). La primera pantalla que se muestra en una aplicación se crea desde el controlador de vista raíz de la ventana. Para más información sobre el propio patrón MVC, consulte la guía de [pantalla de Hello, iOS](~/ios/get-started/hello-ios-multiscreen/index.md) .

La implementación de `AppDelegate` agregada por la plantilla crea la ventana de la aplicación, de la que solo hay una para cada aplicación de iOS y la hace visible con el código siguiente:

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

Si va a ejecutar esta aplicación ahora, es probable que se produzca una excepción que lo indique `Application windows are expected to have a root view controller at the end of application launch` . Vamos a agregar un controlador y convertirlo en el controlador de vista raíz de la aplicación.

## <a name="adding-a-controller"></a>Agregar un controlador

La aplicación puede contener muchos controladores de vista, pero debe tener un controlador de vista raíz para controlar todos los controladores de vista.  Agregue un controlador a la ventana creando una `UIViewController` instancia y estableciéndolo en la `Window.RootViewController` propiedad:

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

Cada controlador tiene una vista asociada, a la que se puede tener acceso desde la `View` propiedad. En el código anterior se cambia la propiedad de la vista `BackgroundColor` a para `UIColor.LightGray` que sea visible, como se muestra a continuación:

 [![El fondo de la vista es un gris claro visible](ios-code-only-images/image1.png)](ios-code-only-images/image1.png#lightbox)

También podríamos establecer cualquier `UIViewController` subclase como `RootViewController` de esta manera, incluidos los controladores de UIKit, así como los que escribimos nosotros mismos. Por ejemplo, el código siguiente agrega `UINavigationController` como `RootViewController` :

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

 [![Controlador anidado en el controlador de navegación](ios-code-only-images/image2.png)](ios-code-only-images/image2.png#lightbox)

## <a name="creating-a-view-controller"></a>Crear un controlador de vista

Ahora que hemos visto cómo agregar un controlador como el `RootViewController` de la ventana, veremos cómo crear un controlador de vista personalizado en el código.

Agregue una nueva clase denominada `CustomViewController` como se muestra a continuación:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Agregue una nueva clase denominada CustomViewController](ios-code-only-images/customviewcontroller.w157-sml.png)](ios-code-only-images/customviewcontroller.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Agregue una nueva clase denominada CustomViewController](ios-code-only-images/new-file.png)](ios-code-only-images/new-file.png#lightbox)

-----

La clase debe heredar de `UIViewController` , que se encuentra en el `UIKit` espacio de nombres, como se muestra a continuación:

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

`UIViewController`contiene un método denominado `ViewDidLoad` al que se llama cuando el controlador de vistas se carga por primera vez en la memoria. Este es un lugar adecuado para la inicialización de la vista, como establecer sus propiedades.

Por ejemplo, el código siguiente agrega un botón y un controlador de eventos para que inserte un nuevo controlador de vista en la pila de navegación cuando se presiona el botón:

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

Para cargar este controlador en la aplicación y demostrar la navegación simple, cree una nueva instancia de `CustomViewController` . Cree un nuevo controlador de navegación, pase la instancia del controlador de vista y establezca el nuevo controlador de navegación en el de la ventana `RootViewController` de la `AppDelegate` manera anterior:

```csharp
var cvc = new CustomViewController ();

var navController = new UINavigationController (cvc);

Window.RootViewController = navController;
```

Ahora, cuando se carga la aplicación, `CustomViewController` se carga dentro de un controlador de navegación:

 [![El CustomViewController se carga dentro de un controlador de navegación](ios-code-only-images/customvc.png)](ios-code-only-images/customvc.png#lightbox)

Al hacer clic en el botón, se generará un nuevo _controlador de vista_ en la pila de navegación:

[![Un nuevo controlador de vista insertado en la pila de navegación](ios-code-only-images/customvca.png)](ios-code-only-images/customvca.png#lightbox)

## <a name="building-the-view-hierarchy"></a>Generar la jerarquía de vistas

En el ejemplo anterior, empezamos a crear una interfaz de usuario en el código agregando un botón al controlador de vistas.

las interfaces de usuario de iOS se componen de una jerarquía de vistas. Las vistas adicionales, como etiquetas, botones, controles deslizantes, etc., se agregan como subvistas de alguna vista primaria.

Por ejemplo, vamos a editar el `CustomViewController` para crear una pantalla de inicio de sesión donde el usuario pueda escribir un nombre de usuario y una contraseña. La pantalla constará de dos campos de texto y un botón.

### <a name="adding-the-text-fields"></a>Agregar los campos de texto

En primer lugar, quite el botón y el controlador de eventos que se agregó en la sección [inicialización de la vista](#initializing-the-view) .

Agregue un control para el nombre de usuario mediante la creación e inicialización de `UITextField` y, a continuación, la adición a la jerarquía de vistas, como se muestra a continuación:

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

Cuando se crea `UITextField` , se establece la `Frame` propiedad para definir su ubicación y tamaño. En iOS, la coordenada 0, 0 está en la parte superior izquierda con + x a la derecha y + y hacia abajo. Después de establecer el `Frame` junto con un par de otras propiedades, llamamos `View.AddSubview` a para agregar `UITextField` a la jerarquía de vistas. Esto crea `usernameField` una subvista de la `UIView` instancia a la que `View` hace referencia la propiedad. Una subvista se agrega con un orden z superior a su vista primaria, por lo que aparece delante de la vista primaria en la pantalla.

`UITextField`A continuación se muestra la aplicación con el incluido:

 [![La aplicación con el campo incluido](ios-code-only-images/image4.png)](ios-code-only-images/image4.png#lightbox)

Podemos agregar un `UITextField` para la contraseña de forma similar, pero esta vez se establece la `SecureTextEntry` propiedad en true, como se muestra a continuación:

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

La configuración `SecureTextEntry = true` oculta el texto escrito `UITextField` por el usuario, tal como se muestra a continuación:

 [![Establecer SecureTextEntry true oculta el texto escrito por el usuario](ios-code-only-images/image4a.png)](ios-code-only-images/image4a.png#lightbox)

### <a name="adding-the-button"></a>Agregar el botón

A continuación, agregaremos un botón para que el usuario pueda enviar el nombre de usuario y la contraseña. El botón se agrega a la jerarquía de vistas como cualquier otro control, pasándolo como un argumento al método de la vista primaria de `AddSubview` nuevo.

En el código siguiente se agrega el botón y se registra un controlador de eventos para el `TouchUpInside` evento:

```csharp
var submitButton = UIButton.FromType (UIButtonType.RoundedRect);

submitButton.Frame = new CGRect (10, 170, w - 20, 44);
submitButton.SetTitle ("Submit", UIControlState.Normal);

submitButton.TouchUpInside += (sender, e) => {
    Console.WriteLine ("Submit button pressed");
};

View.AddSubview(submitButton);
```

Con esto en contexto, la pantalla de inicio de sesión aparece ahora como se muestra a continuación:

 [![La pantalla de inicio de sesión](ios-code-only-images/image5.png)](ios-code-only-images/image5.png#lightbox)

A diferencia de las versiones anteriores de iOS, el fondo del botón predeterminado es transparente. La modificación de la propiedad del botón `BackgroundColor` cambia:

```csharp
submitButton.BackgroundColor = UIColor.White;
```

Esto producirá un botón cuadrado en lugar del botón típico con bordes redondeados. Para obtener el borde redondeado, use el siguiente fragmento de código:

```csharp
submitButton.Layer.CornerRadius = 5f;
```

Con estos cambios, la vista tendrá el siguiente aspecto:

[![Ejemplo de ejecución de la vista](ios-code-only-images/image6.png)](ios-code-only-images/image6.png#lightbox)

## <a name="adding-multiple-views-to-the-view-hierarchy"></a>Agregar varias vistas a la jerarquía de vistas

iOS proporciona una utilidad para agregar varias vistas a la jerarquía de vistas mediante el uso de `AddSubviews` .

```csharp
View.AddSubviews(new UIView[] { usernameField, passwordField, submitButton });
```

## <a name="adding-button-functionality"></a>Agregar funcionalidad de botón

Cuando se hace clic en un botón, los usuarios esperan que suceda algo. Por ejemplo, se muestra una alerta o la navegación se realiza en otra pantalla.

Vamos a agregar código para que inserte un segundo controlador de vista en la pila de navegación.

En primer lugar, cree el segundo controlador de vista:

```csharp
var loginVC = new UIViewController () { Title = "Login Success!"};
loginVC.View.BackgroundColor = UIColor.Purple;
```

A continuación, agregue la funcionalidad al `TouchUpInside` evento:

```csharp
submitButton.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (loginVC, true);
            };
```

La navegación se muestra a continuación:

[![La navegación se muestra en este gráfico](ios-code-only-images/navigation.png)](ios-code-only-images/navigation.png#lightbox)

Tenga en cuenta que, de forma predeterminada, cuando se usa un controlador de navegación, iOS proporciona a la aplicación una barra de navegación y un botón atrás para permitirle desplazarse a través de la pila.

## <a name="iterating-through-the-view-hierarchy"></a>Recorrer en iteración la jerarquía de vistas

Es posible recorrer en iteración la jerarquía de la subvista y elegir una vista concreta. Por ejemplo, para buscar cada `UIButton` y dar a ese botón un distinto `BackgroundColor` , se puede usar el siguiente fragmento de código

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

Sin embargo, esto no funcionará si la vista que se recorre en iteración es `UIView` como todas las vistas se devolverán como si los `UIView` objetos agregados a la propia vista primaria heredan `UIView` .

## <a name="handling-rotation"></a>Controlar la rotación

Si el usuario gira el dispositivo a horizontal, los controles no se ajustan correctamente, como se muestra en la siguiente captura de pantalla:

[![Si el usuario gira el dispositivo a horizontal, no se cambia el tamaño de los controles de forma adecuada.](ios-code-only-images/image7.png)](ios-code-only-images/image7.png#lightbox)

Una manera de solucionarlo es establecer la `AutoresizingMask` propiedad en cada vista. En este caso, queremos que los controles se ajusten horizontalmente, por lo que estableceremos cada uno de ellos `AutoresizingMask` . El ejemplo siguiente es para `usernameField` , pero es necesario aplicar lo mismo a cada gadget de la jerarquía de vistas.

```csharp
usernameField.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
```

Ahora, cuando giramos el dispositivo o el simulador, todo se expande para rellenar el espacio adicional, como se muestra a continuación:

[![Todos los controles se ajustan para rellenar el espacio adicional](ios-code-only-images/image8.png)](ios-code-only-images/image8.png#lightbox)

## <a name="creating-custom-views"></a>Crear vistas personalizadas

Además de utilizar controles que forman parte de UIKit, también se pueden usar vistas personalizadas. Se puede crear una vista personalizada heredando de `UIView` e invalidando `Draw` . Vamos a crear una vista personalizada y a agregarla a la jerarquía de vistas para mostrar.

### <a name="inheriting-from-uiview"></a>Heredar de UIView

Lo primero que debemos hacer es crear una clase para la vista personalizada. Haremos esto mediante la plantilla de **clase** en Visual Studio para agregar una clase vacía denominada `CircleView` . La clase base debe establecerse en `UIView` , que se recuerda en el `UIKit` espacio de nombres. También necesitaremos el `System.Drawing` espacio de nombres. Los otros `System.*` espacios de nombres no se utilizarán en este ejemplo, así que no dude en quitarlos.

La clase debería tener el siguiente aspecto:

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

Cada `UIView` tiene un `Draw` método al que llama el sistema cuando es necesario dibujar. `Draw`nunca se debe llamar directamente a. Lo llama el sistema durante el procesamiento de bucles de ejecución. La primera vez que se realiza el bucle de ejecución después de agregar una vista a la jerarquía de vistas, `Draw` se llama a su método. Las llamadas subsiguientes se `Draw` producen cuando la vista se marca como que es necesario dibujar llamando a `SetNeedsDisplay` o `SetNeedsDisplayInRect` en la vista.

Podemos agregar código de dibujo a nuestra vista agregando este código dentro del método invalidado `Draw` , como se muestra a continuación:

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

`CircleView` `UIView` Como es, también se pueden establecer `UIView` propiedades. Por ejemplo, podemos establecer `BackgroundColor` en el constructor:

```csharp
public CircleView()
{
    BackgroundColor = UIColor.White;
}
```

Para usar el `CircleView` recién creado, podemos agregarlo como una subvista a la jerarquía de vistas en un controlador existente, como hicimos con `UILabels` y `UIButton` versiones anteriores, o podemos cargarlo como la vista de un nuevo controlador. Vamos a hacer lo último.

### <a name="loading-a-view"></a>Cargar una vista

 `UIViewController`tiene un método denominado al `LoadView` que llama el controlador para crear su vista. Este es un lugar adecuado para crear una vista y asignarla a la propiedad del controlador `View` .

En primer lugar, necesitamos un controlador, por lo que debe crear una nueva clase vacía denominada `CircleController` .

En `CircleController` , agregue el código siguiente para establecer el `View` en un `CircleView` (no debe llamar a la `base` implementación en la invalidación):

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

Por último, es necesario presentar el controlador en tiempo de ejecución. Vamos a hacer esto agregando un controlador de eventos en el botón de envío que hemos agregado anteriormente, como se indica a continuación:

```csharp
submitButton.TouchUpInside += delegate
{
    Console.WriteLine("Submit button clicked");

    //circleController is declared as class variable
    circleController = new CircleController();
    PresentViewController(circleController, true, null);
};
```

Ahora, al ejecutar la aplicación y pulsar el botón Enviar, se muestra la nueva vista con un círculo:

[![Se muestra la nueva vista con un círculo](ios-code-only-images/circles.png)](ios-code-only-images/circles.png#lightbox)

## <a name="creating-a-launch-screen"></a>Crear una pantalla de inicio

Se muestra una [pantalla de inicio](~/ios/app-fundamentals/images-icons/launch-screens.md) cuando la aplicación se inicia como una forma de mostrar a los usuarios de que responde. Dado que se muestra una pantalla de inicio cuando se carga la aplicación, no se puede crear en el código, ya que la aplicación todavía se está cargando en la memoria.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Al crear un proyecto de iOS en Visual Studio, se proporciona una pantalla de inicio en forma de un archivo. Xib, que se puede encontrar en la carpeta de **recursos** dentro del proyecto.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Al crear un proyecto de iOS en Visual Studio para Mac, se proporciona una pantalla de inicio en forma de un archivo de guion gráfico.

-----

Para editarlo, haga doble clic en él y ábralo en el diseñador de iOS.

Apple recomienda que se use un archivo. Xib o Storyboard para las aplicaciones que tienen como destino iOS 8 o posterior, cuando se inicia cualquier archivo en el diseñador de iOS, se usarán las clases de tamaño y el diseño automático para adaptar el diseño de modo que tenga un aspecto correcto y se muestre correctamente en todos los tamaños de dispositivo. Se puede usar una imagen de inicio estática además de un. Xib o un guion gráfico para permitir la compatibilidad con aplicaciones destinadas a versiones anteriores.

Para obtener más información acerca de cómo crear una pantalla de inicio, consulte los documentos siguientes:

- [Crear una pantalla de inicio con un. Xib](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)
- [Administrar pantallas de inicio con guiones gráficos](~/ios/app-fundamentals/images-icons/launch-screens.md)

> [!IMPORTANT]
> A partir de iOS 9, Apple recomienda que se usen guiones gráficos como método principal para crear una pantalla de inicio.

### <a name="creating-a-launch-image-for-pre-ios-8-applications"></a>Creación de una imagen de inicio para aplicaciones anteriores a iOS 8

Se puede usar una imagen estática además de una pantalla de inicio de. Xib o Storyboard si la aplicación tiene como destino versiones anteriores a iOS 8.

Esta imagen estática se puede establecer en el archivo info. plist o como un catálogo de recursos (para iOS 7) en la aplicación. Tendrá que proporcionar imágenes independientes para cada tamaño de dispositivo (320x480, 640x960, 640x1136) en el que se pueda ejecutar la aplicación. Para obtener más información sobre los tamaños de pantalla de inicio, vea la guía de [Inicio de imágenes de pantalla](~/ios/app-fundamentals/images-icons/launch-screens.md) .

> [!IMPORTANT]
> Si la aplicación no tiene ninguna pantalla de inicio, es posible que observe que no se ajusta totalmente a la pantalla. En este caso, debe asegurarse de incluir, como mínimo, una imagen de 640x1136 con `Default-568@2x.png` el nombre info. plist.

## <a name="summary"></a>Resumen

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

En este artículo se ha explicado cómo desarrollar aplicaciones de iOS mediante programación en Visual Studio. Hemos examinado cómo crear un proyecto a partir de una plantilla de proyecto vacía, en la que se explica cómo crear y agregar un controlador de vista raíz a la ventana. Después hemos mostrado cómo usar los controles de UIKit para crear una jerarquía de vistas dentro de un controlador para desarrollar una pantalla de la aplicación. A continuación, hemos examinado cómo hacer que las vistas se diseñan correctamente en distintas orientaciones y vimos cómo crear una vista personalizada mediante subclases, así como `UIView` Cómo cargar la vista dentro de un controlador. Por último, hemos explorado cómo agregar una pantalla de inicio a una aplicación.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

En este artículo se ha explicado cómo desarrollar aplicaciones de iOS mediante programación en Visual Studio para Mac. Hemos examinado cómo crear un proyecto a partir de una plantilla de vista única, en la que se explica cómo crear y agregar un controlador de vista raíz a la ventana. Después hemos mostrado cómo usar los controles de UIKit para crear una jerarquía de vistas dentro de un controlador para desarrollar una pantalla de la aplicación. A continuación, hemos examinado cómo hacer que las vistas se diseñan correctamente en distintas orientaciones y vimos cómo crear una vista personalizada mediante subclases, así como `UIView` Cómo cargar la vista dentro de un controlador. Por último, hemos explorado cómo agregar una pantalla de inicio a una aplicación.

-----

## <a name="related-links"></a>Vínculos relacionados

- [SimpleLogin (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/simplelogin)
