---
title: Introducción a los guiones gráficos en Xamarin. iOS
description: En este documento se proporciona una introducción a los guiones gráficos en Xamarin. iOS. Describe cómo se usa un guion gráfico para definir una interfaz de usuario, objetos segue, y cómo usar el diseñador de iOS para editar archivos de guion gráfico.
ms.prod: xamarin
ms.assetid: A3339BD2-9F56-7965-25F5-4B7C991EB775
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 13f5c594543934e14295615517e3de01a98a69a5
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2020
ms.locfileid: "76940999"
---
# <a name="introduction-to-storyboards-in-xamarinios"></a>Introducción a los guiones gráficos en Xamarin. iOS

En esta guía se explicará qué es un guion gráfico y se examinan algunos de los componentes clave, como objetos segue. Veremos cómo se pueden crear y usar guiones gráficos y qué ventajas tienen para un desarrollador.

Antes de que Apple introdujera el formato de archivo de guion gráfico como una representación visual de la interfaz de usuario de una aplicación de iOS, los desarrolladores creaban archivos XIB para cada controlador de vista y programaban la navegación entre cada vista manualmente.  El uso de un guion gráfico permite al desarrollador definir tanto controladores de vista como la navegación entre ellos en una superficie de diseño y ofrece la edición WYSIWYG de la interfaz de usuario de la aplicación.

Un guion gráfico se puede crear, abrir y editar con Xamarin iOS Designer. En esta guía también se explica cómo usar el diseñador para crear los guiones gráficos mientras C# se usa para programar la navegación.

## <a name="requirements"></a>Requisitos de

Los guiones gráficos se pueden usar con Xcode, el diseñador de iOS en Visual Studio para Mac y Visual Studio 2019 con las cargas de trabajo de Xamarin instaladas.

## <a name="what-is-a-storyboard"></a>¿Qué es un guion gráfico?

Un guion gráfico es la representación visual de todas las pantallas de una aplicación. Contiene una secuencia de escenas, donde cada escena representa un *controlador de vista* y sus *vistas*. Estas vistas pueden contener objetos y [controles](~/ios/user-interface/controls/index.md) que permitirán a los usuarios interactuar con la aplicación. Esta colección de vistas y controles (o *subvistas*) se conoce como una *jerarquía*de vistas de contenido. Las escenas están conectadas mediante objetos segue, que representan una transición entre controladores de vista. Normalmente, esto se consigue creando un segue entre un objeto de la vista inicial y la vista de conexión. Las relaciones en la superficie de diseño se ilustran en la imagen siguiente:

 [![](images/storyboardsview.png "The relationships on the design surface are illustrated in this image")](images/storyboardsview.png#lightbox)

Como se muestra, el guión gráfico diseñará cada una de las escenas con contenido ya representado y mostrará las conexiones entre ellas.  Cabe destacar en este punto, que cuando hablamos de escenas en un iPhone, es seguro suponer que una *escena* del guión gráfico es igual a una *pantalla* de contenido del dispositivo. Sin embargo, con un iPad es posible tener varias escenas a la vez (por ejemplo, con un controlador de vista elemento flotante).

Hay muchas ventajas en el uso de guiones gráficos para crear la interfaz de usuario de la aplicación, sobre todo cuando se usa Xamarin. En primer lugar, es una representación visual de la interfaz de usuario, ya que todos los objetos, incluidos [los controles personalizados](~/ios/user-interface/designer/ios-designable-controls-overview.md) , se representan en tiempo de diseño.
Esto significa que antes de compilar o implementar la aplicación puede visualizar su apariencia y flujo. Tome la imagen anterior, por ejemplo. Podemos saber de una mirada rápida a la superficie de diseño cuántas escenas hay, el diseño de cada vista y cómo se relaciona todo. Esto es lo que hace que los guiones gráficos sean tan eficaces.

Los eventos son más fáciles de administrar con los guiones gráficos, especialmente cuando se usa el diseñador de iOS. La mayoría de los controles de interfaz de usuario tendrán una lista de posibles eventos en el Panel de propiedades. El controlador de eventos se puede Agregar aquí y completarse en un método parcial en la clase de controladores de vista.

El contenido de un guión gráfico se almacena como un archivo XML. En tiempo de compilación, los archivos `.storyboard` se compilan en archivos binarios conocidos como NIBS. En tiempo de ejecución, estos NIBS se inicializan y se crean instancias para crear nuevas vistas.

## <a name="segues"></a>Objetos segue

Un objeto *segue*, o *segue*, se usa en el desarrollo de iOS para representar una transición entre escenas. Para crear un segue, mantenga presionada la tecla **Ctrl** y haga clic y arrastre de una escena a otra. A medida que se arrastra el mouse, aparece un conector azul que indica que el segue conducirá como se muestra en la imagen siguiente:

 [![](images/createsegue.png "A blue connector appears, indicating where the segue will lead as demonstrated in this image")](images/createsegue.png#lightbox)

Al pasar el mouse, aparecerá un menú que nos permite elegir la acción para nuestro segue. Puede ser similar a las imágenes siguientes:

**Clases de tamaño y 8 anteriores a iOS**:

[![](images/segue1.png "The Action Segue dropdown without Size Classes")](images/segue1.png#lightbox)

**Al usar clases de tamaño y objetos segue adaptables**:

[![](images/16new.png "The Action Segue dropdown with Size Classes")](images/16new.png#lightbox)

> [!IMPORTANT]
> Si usa VMWare para su máquina virtual de Windows, Ctrl + clic se asigna como el botón _secundario_ del mouse de forma predeterminada. Para crear un segue, edite las preferencias del teclado a través de las **preferencias** > **teclados de mouse de teclado & Mouse** > **mouse** y reasigne el **botón secundario** , como se muestra a continuación:
>
> [![](images/image22.png "Keyboard and Mouse preference settings")](images/image22.png#lightbox)
>
> Ahora debería poder agregar un segue entre los controladores de vista de la forma habitual.

Hay diferentes tipos de transiciones, cada una de las cuales proporciona control sobre cómo se presenta al usuario un nuevo controlador de vista y cómo interactúa con otros controladores de vistas en el guión gráfico. Estos se explican a continuación. También es posible crear una subclase de un objeto segue para implementar una transición personalizada:

- **Mostrar/** enviar: un segue de extracción agrega el controlador de vista a la pila de navegación. Se supone que el controlador de vista que origina la inserciones forma parte del mismo controlador de navegación que el controlador de vista que se va a agregar a la pila. Esto hace lo mismo que `pushViewController` y se suele usar cuando hay alguna relación entre los datos de las pantallas. El uso de segue de extracción le permite tener una barra de navegación con un botón atrás y un título agregado a cada vista de la pila, lo que permite explorar en profundidad la navegación por la jerarquía de vistas.
- **Modal** : un segue modal crea una relación entre dos controladores de vista cualesquiera del proyecto, con la opción de mostrar una transición animada. El controlador de vista secundario ocultará completamente el controlador de vista primario cuando se ponga en vista. A diferencia de un segue de extracción, que agrega un botón atrás para nosotros; Cuando se usa un segue modal `DismissViewController` debe usarse para volver al controlador de vista anterior.
- **Personalizado** : cualquier segue personalizado se puede crear como una subclase de `UIStoryboardSegue`.
- **Desenredado** : se puede usar un segue de desenredado para navegar hacia atrás a través de un segue de extracción o modal, por ejemplo, descartando el controlador de vista presentado de forma modal. Además, puede desenredar a través de no solo uno, sino también una serie de objetos segues de extracción y de moda y volver a varios pasos de la jerarquía de navegación con una sola acción de desenredado. Para saber cómo usar un segue de desenredado en iOS, lea la receta de [creación de desenredado de objetos segue](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/unwind_segue) .
- Sin **origen** : una segue sin código fuente indica la escena que contiene el controlador de vista inicial y, por lo tanto, la vista que el usuario verá en primer lugar. Se representa mediante el segue que se muestra a continuación:  

    [![](images/sourcelesssegue.png "A sourceless segue")](images/sourcelesssegue.png#lightbox)

### <a name="adaptive-segue-types"></a>Tipos de segue adaptables

 iOS 8 presentó [las clases de tamaño](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) para permitir que un archivo de guión gráfico de iOS funcione con todos los tamaños de pantalla disponibles, lo que permite a los desarrolladores crear una interfaz de usuario para todos los dispositivos iOS. De forma predeterminada, todas las aplicaciones de Xamarin. iOS nuevas usarán clases de tamaño. Para usar clases de tamaño de un proyecto anterior, consulte la guía de [Introducción a guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) .

Cualquier aplicación que use clases de tamaño también usará el nuevo [*objetos segue adaptable*](~/ios/user-interface/storyboards/unified-storyboards.md). Al usar clases de tamaño, recuerde que no vamos a especificar directamente el tiempo que estamos usando un iPhone o iPad. En otras palabras, vamos a crear una interfaz de usuario que siempre tendrá el mismo aspecto, independientemente de la cantidad de espacio real con el que tenga que trabajar. El trabajo de objetos segue adaptable es juzgar el entorno y determinar el mejor modo de presentar el contenido. A continuación se muestran los objetos segue adaptables:

[![](images/adaptivesegue.png "The Adaptive Segues dropdown")](images/adaptivesegue.png#lightbox)

|Segue|Descripción|
|--- |--- |
|Mostrar|Esto es muy similar a un segue de extracción, pero tiene en cuenta el contenido de la pantalla.|
|Mostrar detalles|Si la aplicación muestra una vista maestra y de detalles (por ejemplo, en un controlador de vista de división en un iPad), el contenido reemplazará la vista de detalles. Si la aplicación muestra solo el maestro o detalle, el contenido reemplazará la parte superior de la pila del controlador de vista.|
|Presentación|Es similar al segue modal y permite seleccionar los estilos de presentación y de transición.|
|Presentación de elemento flotante|Esto presenta el contenido como un elemento flotante|

### <a name="transferring-data-with-segues"></a>Transferir datos con objetos segue

Las ventajas de una segue no terminan con las transiciones. También se pueden usar para administrar la transferencia de datos entre controladores de vista. Esto se logra invalidando el método de `PrepareForSegue` en el controlador de vista inicial y controlando los datos por nosotros mismos. Cuando se desencadena segue (por ejemplo, con un botón), la aplicación llamará a este método, lo que proporciona una oportunidad para preparar el nuevo controlador de vista *antes* de que se produzca la navegación. En el código siguiente, en el ejemplo [Phoneword](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios) , se muestra:

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue,
NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    var callHistoryController = segue.DestinationViewController
                                  as CallHistoryController;

    if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
}
```

En este ejemplo, se llamará al método `PrepareForSegue` cuando el usuario desencadene el segue. Primero tenemos que crear una instancia del controlador de vista ' recibiendo ' y establecerla como el controlador de vista de destino de segue. Esto se realiza mediante la línea de código siguiente:

```csharp
var callHistoryController = segue.DestinationViewController as CallHistoryController;
```

El método ahora tiene la capacidad de establecer propiedades en el `DestinationViewController`. En este ejemplo hemos aprovechado esto pasando una lista llamada `PhoneNumbers` a la `CallHistoryController` y asignándole un objeto con el mismo nombre:.

```csharp
if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
```

Una vez finalizada la transición, el usuario verá el `CallHistoryController` con la lista rellenada.

## <a name="adding-a-storyboard-to-a-non-storyboard-project"></a>Agregar un guion gráfico a un proyecto que no es un guion gráfico

En ocasiones, puede que tenga que agregar un guion gráfico a un archivo que no es un guion gráfico. Una vez hecho esto en Visual Studio para Mac se puede simplificar mediante los pasos siguientes:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Cree un nuevo archivo de guion gráfico; para ello, vaya a **archivo > nuevo archivo > iOS > guion gráfico**, como se muestra a continuación:

    [![](images/new-storyboard-xs.png "The new file dialog")](images/new-storyboard-xs.png#lightbox)

2. Agregue el nombre del guion gráfico a la sección de la **interfaz principal** de **info. plist**, como se muestra a continuación:

    [![](images/infoplist.png "The Info.plist editor")](images/infoplist.png#lightbox)

    Esto hace el equivalente de crear una instancia del controlador de vista inicial en el método `FinishedLaunching` dentro del delegado de la aplicación. Con esta opción establecida, la aplicación crea una instancia de una ventana (vea a continuación), carga el guión gráfico principal y asigna una instancia del controlador de vista inicial del guión gráfico (el que está junto a la segue sin código fuente) como `RootViewController` propiedad de la ventana y, a continuación, hace que la ventana esté visible en la pantalla.

3. En el `AppDelegate`, invalide el método de `Window` predeterminado, con el código siguiente para implementar la propiedad de la ventana:

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Cree un nuevo archivo de guion gráfico haciendo clic con el botón derecho en el proyecto para **agregar > nuevo archivo > iOS > guion gráfico vacío**, como se muestra a continuación:

    [![](images/new-storyboard-vs.png "The new item dialog")](images/new-storyboard-vs.png#lightbox)

2. Agregue el nombre del guion gráfico a la sección **interfaz principal** de la aplicación de iOS, como se muestra a continuación:

    [![](images/ios-app.png "The Info.plist editor")](images/ios-app.png#lightbox)

    Esto hace el equivalente de crear una instancia del controlador de vista inicial en el método `FinishedLaunching` dentro del delegado de la aplicación. Con esta opción establecida, la aplicación crea una instancia de una ventana (vea a continuación), carga el guión gráfico principal y asigna una instancia del controlador de vista inicial del guión gráfico (el que está junto a la segue sin código fuente) como `RootViewController` propiedad de la ventana y, a continuación, hace que la ventana esté visible en la pantalla.

3. En el `AppDelegate`, invalide el método de `Window` predeterminado, con el código siguiente para implementar la propiedad de la ventana:

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

-----

## <a name="creating-a-storyboard-with-xcode"></a>Crear un guion gráfico con Xcode

Un guion gráfico se puede crear y modificar mediante Xcode para su uso en las aplicaciones de iOS desarrolladas con Visual Studio para Mac.

Los guiones gráficos reemplazan por completo los archivos XIB individuales del proyecto; no obstante, se puede crear una instancia de los controladores de vista individuales de un guión gráfico mediante `Storyboard.InstantiateViewController`.

A veces, las aplicaciones tienen requisitos especiales que no se pueden controlar con las transiciones de guion gráfico integradas proporcionadas por el diseñador. Por ejemplo, si vamos a crear una aplicación que inicia diferentes pantallas del mismo botón, según el estado actual de una aplicación, es posible que desee crear una instancia de los controladores de vista manualmente y programar la transición.

En la captura de pantalla siguiente se muestran dos controladores de vista en nuestra superficie de diseño sin segue entre ellos. La siguiente sección le guiará por el modo en que se puede configurar la transición en el código.

1. Agregue un _guion gráfico de iPhone vacío_ a un proyecto de proyecto existente:

    [![](images/add-storyboard2.png "Adding storyboard")](images/add-storyboard2.png#lightbox)

2. Haga clic con el botón derecho en el archivo de guion gráfico y seleccione **abrir con > Xcode Interface Builder** para abrirlo en Xcode.

3. En Xcode, abra la biblioteca (a través de la **vista > Mostrar biblioteca** o *Mayús + Comando + L*) para mostrar una lista de objetos que se pueden agregar al guion gráfico. Agregue un `Navigation Controller` al guion gráfico arrastrando el objeto desde la lista hasta el guión gráfico. De forma predeterminada, el `Navigation Controller` proporcionará dos pantallas. la pantalla de la derecha es una `TableViewController` que se va a reemplazar por una vista más simple, por lo que se puede quitar haciendo clic en la vista y presionando la tecla Supr.

    [![](images/add-navigation-controller.png "Adding a NavigationController from the Library")](images/add-navigation-controller.png#lightbox)

4. Este controlador de vista tendrá su propia clase personalizada y también necesita su propio identificador de guión gráfico. Al hacer clic en el cuadro situado encima de esta vista recién agregada, habrá tres iconos, el extremo izquierdo de que representa el controlador de vista de la vista. Al seleccionar este icono, puede establecer los valores de clase e ID. en la pestaña identidad del panel derecho. establezca estos valores en `MainViewController` y asegúrese de comprobar `Use Storyboard ID`.

    [![](images/identity-panel.png "Setting the MainViewController in the identity panel")](images/identity-panel.png#lightbox)

5. Con la biblioteca de nuevo, arrastre un controlador de vista a la pantalla. Se establecerá como el controlador de vista raíz. Manteniendo presionada la tecla control, haga clic y arrastre desde el controlador de navegación de la izquierda hasta el controlador de vista que acaba de agregar a la derecha y haga clic en *controlador de vista raíz* en el menú.

    [![](images/add-view-controller.png "Adding a NavigationController from the Library and setting the MainViewController as a Root View Controller")](images/add-view-controller.png#lightbox)

6. Esta aplicación se desplazará a otra vista, así que agregue una vista más al guion gráfico, como antes. Llamaremos a este `PinkViewController`y esos valores se pueden establecer de la misma manera que con la `MainViewController`.

    [![](images/add-additional-view-controller.png "Adding an additional View Controller")](images/add-additional-view-controller.png#lightbox)

7. Dado que el controlador de vistas tendrá un fondo rosa, esa propiedad se puede establecer en el panel atributos mediante la lista desplegable situada junto a `Background`.

    [![](images/set-pink-background.png "Adding an additional View Controller")](images/set-pink-background.png#lightbox)

8. Dado que deseamos que el `MainViewController` navegue hasta el `PinkViewController`, el primero necesitará un botón para interactuar con él. Mediante la biblioteca, podemos agregar un botón a la `MainViewController`.

    [![](images/add-button.png "Adding a Button to the MainViewController")](images/add-button.png#lightbox)

El guión gráfico está completo, pero si implementamos el proyecto ahora, se producirá una pantalla en blanco. Esto se debe a que aún es necesario indicar al IDE que use el guión gráfico y configurar un controlador de vista raíz para que sirva como la primera vista. Normalmente, esto se puede hacer a través de las opciones del proyecto, como se muestra anteriormente. Sin embargo, en este ejemplo se lograría el mismo resultado en el código, agregando lo siguiente a **AppDelegate**:

```csharp
public partial class AppDelegate : UIApplicationDelegate
{
    UIWindow window;
    public static UIStoryboard Storyboard = UIStoryboard.FromName ("MainStoryboard", null);
    public static UIViewController initialViewController;

    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        window = new UIWindow (UIScreen.MainScreen.Bounds);

        initialViewController = Storyboard.InstantiateInitialViewController () as UIViewController;

        window.RootViewController = initialViewController;
        window.AddSubview(initialViewController.View);
        window.MakeKeyAndVisible ();
        return true;
    }
}
```

Esta es una gran cantidad de código, pero solo algunas líneas son desconocidas. En primer lugar, registramos nuestro guion gráfico con el **AppDelegate** pasando el nombre del guión gráfico, **archivo mainstoryboard**. A continuación, se indica a la aplicación que cree una instancia de un controlador de vista inicial desde el guión gráfico mediante una llamada a `InstantiateInitialViewController` en nuestro guion gráfico y que el controlador de vistas se establece como el controlador de vista raíz de la aplicación. Este método determina la primera pantalla que el usuario ve y crea una nueva instancia de ese controlador de vista.

Observe que, en el panel de la solución, el IDE ha creado una `MainViewcontroller.cs` clase y su `corresponding designer.cs` al agregar el nombre de clase al Panel de propiedades en el paso 4. Podemos ver que esta clase creó un constructor especial que incluye una clase base:

```csharp
public MainViewController (IntPtr handle) : base (handle)
{
}
```

Al crear un guion gráfico con Xcode, el IDE agregará automáticamente el atributo [[Register]](xref:Foundation.RegisterAttribute) en la parte superior de la clase `designer.cs` y pasará un identificador de cadena, que es idéntico al identificador de guión gráfico especificado en el paso anterior. Esto vinculará C# a la escena correspondiente en el guión gráfico.

```csharp
[Register ("MainViewController")]
public partial class MainViewController : UIViewController
{
    public MainViewController (IntPtr handle) : base (handle)
    {
    }
    //...
}
```

Para obtener más información sobre el registro de clases y métodos, consulte la documentación del [registrador de tipos](https://docs.microsoft.com/xamarin/ios/internals/registrar) .

El último paso de esta clase es conectar el botón y la transición al controlador de vista de color rosa. Crearemos una instancia del `PinkViewController` del guión gráfico; a continuación, se programará un segue de extracción con `PushViewController`, como se muestra en el código de ejemplo siguiente:

```csharp
public partial class MainViewController : UIViewController
{
    UIViewController pinkViewController;

    public MainViewController (IntPtr handle) : base (handle)
    {
    }

    public override void AwakeFromNib ()
    {
        // Called when loaded from xib or storyboard.
        this.Initialize ();
    }

    public void Initialize()
    {
        //Instantiating View Controller with Storyboard ID 'PinkViewController'
        pinkViewController = Storyboard.InstantiateViewController ("PinkViewController") as PinkViewController;
    }

    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        //When we push the button, we will push the pinkViewController onto our current Navigation Stack
        PinkButton.TouchUpInside += (o, e) =&gt;
        {
            this.NavigationController.PushViewController (pinkViewController, true);
        };
    }
}
```

La ejecución de la aplicación produce una aplicación de dos pantallas:

![](images/finishedstoryboard.png "Sample app run screens")

## <a name="conditional-segues"></a>Objetos segue condicional

A menudo, pasar de un controlador de vista a otro depende de una condición determinada. Por ejemplo, si hubiéramos realizado una pantalla de inicio de sesión simple, solo deseamos pasar a la siguiente pantalla *si* se ha comprobado el nombre de usuario y la contraseña.

En el ejemplo siguiente, vamos a agregar un campo de contraseña al ejemplo anterior. El usuario solo podrá tener acceso a *PinkViewController* si escribe la contraseña correcta; de lo contrario, se mostrará un error.

Antes de comenzar, siga los pasos 1 a 8 anteriores. En estos pasos se crea nuestro guion gráfico, se comienza a crear la interfaz de usuario y se indica a la aplicación que el controlador de vista que se va a usar como RootViewController.

1. Ahora, vamos a crear la interfaz de usuario y agregar las vistas adicionales que aparecen en la `MainViewController` para que parezca que en la siguiente captura de pantalla:

    - UITextField
        - Nombre: PasswordTextField
        - Marcador de posición: ' escriba la contraseña secreta '
    - UILabel
        - Texto: ' error: contraseña incorrecta. No debe pasar! '
        - Color: rojo
        - Alineación: Centro
        - Líneas: 2
        - Casilla ' Hidden ' activada    

    [![](images/passwordvc.png "Center Lines")](images/passwordvc.png#lightbox)

2. Cree un segue entre el botón ir a rosa y el controlador de vista; para ello, presione Ctrl y arrastre desde el *PinkButton* hasta el *PinkViewController*, **y seleccione Activar al pasar** el mouse.

3. Haga clic en segue y asígnele el *identificador* `SegueToPink`:

    [![](images/namesegue.png "Click on the Segue and give it the Identifier SegueToPink")](images/namesegue.png#lightbox)  

4. Por último, agregue el siguiente método ShouldPerformSegue a la clase `MainViewController`:

    ```csharp
    public override bool ShouldPerformSegue (string segueIdentifier, NSObject sender)
    {

        if(segueIdentifier == "SegueToPink"){
            if (PasswordTextField.Text == "password") {
                PasswordTextField.ResignFirstResponder ();
                return true;
            }
            else{
                ErrorLabel.Hidden = false;
                return false;
            }
        }
        return base.ShouldPerformSegue (segueIdentifier, sender);
    }
    ```

En este código hemos buscado segueIdentifier en nuestro `SegueToPink` segue, por lo que podemos probar una condición; una contraseña válida en este caso. Si la condición devuelve `true`, el segue llevará a cabo y presentará el `PinkViewController`. Si `false`, no se presentará el nuevo controlador de vista.

Podemos aplicar este enfoque a cualquier segue de este controlador de vista comprobando el argumento segueIdentifier en el método ShouldPerformSegue. En este caso, solo tenemos un identificador segue: `SegueToPink`.

Consulte la solución storyboards. Conditional en el [ejemplo de guiones gráficos manuales](https://docs.microsoft.com/samples/xamarin/ios-samples/manualstoryboard) para obtener un ejemplo práctico.

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>Usar referencias a guion gráfico

Una referencia de guion gráfico permite tomar un diseño de guion gráfico grande y complejo y dividirlo en guiones gráficos más pequeños a los que se hace referencia desde el original, con lo que se elimina la complejidad y se facilita el diseño y el mantenimiento de los guiones gráficos resultantes.

Además, una referencia de guion gráfico puede proporcionar un _delimitador_ a otra escena en el mismo guión gráfico o en una determinada escena en otro.

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>Referencia a un guión gráfico externo

Para agregar una referencia a un guion gráfico externo, haga lo siguiente:

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el nombre del proyecto y seleccione **Agregar** > **nuevo archivo...**  > el **guión gráfico**de > de **iOS** . Escriba un **nombre** para el nuevo guion gráfico y haga clic en el botón **nuevo** :

    [![](images/ref01.png "The New File Dialog")](images/ref01.png#lightbox)

2. Diseñe el diseño de las escenas del nuevo guión gráfico como lo haría normalmente y guarde los cambios:

    [![](images/ref02.png "The layout of the new scene")](images/ref02.png#lightbox)

3. Abra el guion gráfico al que va a agregar la referencia en el diseñador de iOS.

4. Arrastre una **referencia de guion gráfico** desde el **cuadro de herramientas** hasta el superficie de diseño:

    [![](images/ref03.png "A Storyboard Reference")](images/ref03.png#lightbox)

5. En la pestaña **Widget** del **Explorador de propiedades**, seleccione el nombre del **guion gráfico** que creó anteriormente:

    [![](images/ref04.png "The Widget tab")](images/ref04.png#lightbox)

6. Control: haga clic en un widget de interfaz de usuario (como un botón) en una escena existente y cree un nuevo segue en la **referencia de guion gráfico** que acaba de crear:

    [![](images/ref05.png "Creating a segue")](images/ref05.png#lightbox)

7. En el menú emergente, seleccione **Mostrar** para completar el segue:

    [![](images/ref06.png "Selecting Show to complete the Segue")](images/ref06.png#lightbox)

8. Guarde los cambios en el guion gráfico.

Cuando se ejecuta la aplicación y el usuario hace clic en el elemento de la interfaz de usuario desde el que se creó el segue, se mostrará el controlador de vista inicial del guión gráfico externo especificado en la referencia de guion gráfico.

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>Hacer referencia a una escena específica en un guión gráfico externo

Para agregar una referencia a una escena específica, un guion gráfico externo (y no el controlador de vista inicial), haga lo siguiente:

1. En el **Explorador de soluciones**, haga doble clic en el guion gráfico externo para abrirlo para su edición.

2. Agregue una nueva escena y diseñe su diseño como lo haría normalmente:

    [![](images/ref07.png "The new scene layout")](images/ref07.png#lightbox)

3. En la pestaña **Widget** del **Explorador de propiedades**, escriba un **identificador de guión gráfico** para el controlador de vista de la nueva escena:

    [![](images/ref08.png "Enter a Storyboard ID for the new Scenes View Controller")](images/ref08.png#lightbox)

4. Abra el guion gráfico al que va a agregar la referencia en el diseñador de iOS.

5. Arrastre una **referencia de guion gráfico** desde el **cuadro de herramientas** hasta el superficie de diseño:

    [![](images/ref03.png "A Storyboard Reference")](images/ref03.png#lightbox)

6. En la pestaña **Widget** del **Explorador de propiedades**, seleccione el nombre del **guión gráfico** y el **ID. de referencia** (identificador de guión gráfico) de la escena que creó anteriormente:

    [![](images/ref09.png "The Widget tab ")](images/ref09.png#lightbox)

7. Control: haga clic en un widget de interfaz de usuario (como un botón) en una escena existente y cree un nuevo segue en la **referencia de guion gráfico** que acaba de crear:

    [![](images/ref10.png "Creating a segue")](images/ref10.png#lightbox)

8. En el menú emergente, seleccione **Mostrar** para completar el segue:

    [![](images/ref06.png "Selecting Show to complete the Segue")](images/ref06.png#lightbox)

9. Guarde los cambios en el guion gráfico.

Cuando se ejecuta la aplicación y el usuario hace clic en el elemento de la interfaz de usuario en el que ha creado el segue, se mostrará la escena con el **identificador de guión gráfico** especificado desde el guión gráfico externo especificado en la referencia de guion gráfico.

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>Hacer referencia a una escena específica en el mismo guion gráfico

Para agregar una referencia a una escena específica del mismo guión gráfico, haga lo siguiente:

1. En el **Explorador de soluciones**, haga doble clic en el guion gráfico para abrirlo para su edición.

2. Agregue una nueva escena y diseñe su diseño como lo haría normalmente:

    [![](images/ref11.png "The new scene layout")](images/ref11.png#lightbox)

3. En la pestaña **Widget** del **Explorador de propiedades**, escriba un **identificador de guión gráfico** para el controlador de vista de la nueva escena:

    [![](images/ref12.png "The Widget tab")](images/ref12.png#lightbox)

4. Arrastre una **referencia de guion gráfico** desde el **cuadro de herramientas** hasta el superficie de diseño:

   [![](images/ref03.png "A Storyboard Reference")](images/ref03.png#lightbox)

5. En la pestaña **Widget** del **Explorador de propiedades**, seleccione **ID. de referencia** (identificador de guión gráfico) de la escena que creó anteriormente:

    [![](images/ref13.png "The Widget tab")](images/ref13.png#lightbox)

6. Control: haga clic en un widget de interfaz de usuario (como un botón) en una escena existente y cree un nuevo segue en la **referencia de guion gráfico** que acaba de crear:

    [![](images/ref14.png "Creating a segue")](images/ref14.png#lightbox)

7. En el menú emergente, seleccione **Mostrar** para completar el segue:

    [![](images/ref06.png "Selecting Show to complete the Segue")](images/ref06.png#lightbox)

8. Guarde los cambios en el guion gráfico.

Cuando se ejecuta la aplicación y el usuario hace clic en el elemento de la interfaz de usuario en el que ha creado el segue, se mostrará la escena con el **identificador de guión gráfico** especificado en el mismo guion gráfico especificado en la referencia de guion gráfico.

## <a name="summary"></a>Resumen

En este artículo se presenta el concepto de guiones gráficos y cómo pueden ser beneficiosos en el desarrollo de aplicaciones de iOS. Se describen las escenas, los controladores de vista, las vistas y las jerarquías de vistas y cómo se vinculan las escenas con distintos tipos de objetos segue.  También explora la creación de instancias de los controladores de vista manualmente desde un guion gráfico y la creación de objetos segue condicionales.

## <a name="related-links"></a>Vínculos relacionados

- [Guion gráfico manual (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/manualstoryboard/)
- [Introducción al diseñador de iOS](~/ios/user-interface/designer/introduction.md)
- [Convertir en guiones gráficos](https://developer.apple.com/library/ios/#releasenotes/Miscellaneous/RN-AdoptingStoryboards/)
- [Referencia de la clase UIStoryboard](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIStoryboard_Class/Reference/Reference.html)
