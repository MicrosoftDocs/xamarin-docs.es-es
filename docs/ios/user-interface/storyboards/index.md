---
title: Introducción a los guiones gráficos en Xamarin. iOS
description: En este documento se proporciona una introducción a los guiones gráficos en Xamarin. iOS. Describe cómo se usa un guion gráfico para definir una interfaz de usuario, objetos segue, y cómo usar el diseñador de iOS para editar archivos de guion gráfico.
ms.prod: xamarin
ms.assetid: A3339BD2-9F56-7965-25F5-4B7C991EB775
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 9b4f71599ecf85e51899c41c37aecc63e44e7188
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646429"
---
# <a name="introduction-to-storyboards-in-xamarinios"></a>Introducción a los guiones gráficos en Xamarin. iOS

En esta guía se explicará qué es un guion gráfico y se examinan algunos de los componentes clave, como objetos segue. Veremos cómo se pueden crear y usar guiones gráficos y qué ventajas tienen para un desarrollador.

Antes de que Apple introdujera el formato de archivo de guion gráfico como una representación visual de la interfaz de usuario de una aplicación de iOS, los desarrolladores creaban archivos XIB para cada controlador de vista y programaban la navegación entre cada vista manualmente.  El uso de un guion gráfico permite al desarrollador definir tanto controladores de vista como la navegación entre ellos en una superficie de diseño y ofrece la edición WYSIWYG de la interfaz de usuario de la aplicación.

Un guion gráfico se puede crear, abrir y editar con Xamarin iOS Designer. En esta guía también se explica cómo usar el diseñador para crear los guiones gráficos mientras C# se usa para programar la navegación.


## <a name="requirements"></a>Requisitos

Los guiones gráficos se pueden usar con iOS Designer en Visual Studio para Mac o con Visual Studio 2017 con las cargas de trabajo de Xamarin instaladas.

## <a name="what-is-a-storyboard"></a>¿Qué es un guion gráfico?

Un guion gráfico es la representación visual de todas las pantallas de una aplicación. Contiene una secuencia de escenas, donde cada escena representa un *controlador de vista* y sus *vistas*. Estas vistas pueden contener objetos y [controles](~/ios/user-interface/controls/index.md) que permitirán a los usuarios interactuar con la aplicación. Esta colección de vistas y controles (osubvistas) se conoce como una *jerarquía*de vistas de contenido. Las escenas están conectadas mediante objetos segue, que representan una transición entre controladores de vista. Normalmente, esto se consigue creando un segue entre un objeto de la vista inicial y la vista de conexión. Las relaciones en la superficie de diseño se ilustran en la imagen siguiente:

 [![](images/storyboardsview.png "En esta imagen se ilustran las relaciones en la superficie de diseño")](images/storyboardsview.png#lightbox)

Como se muestra, el guión gráfico diseñará cada una de las escenas con contenido ya representado y mostrará las conexiones entre ellas.  Cabe destacar en este punto, que cuando hablamos de escenas en un iPhone, es seguro suponer que una *escena* del guión gráfico es igual a una *pantalla* de contenido del dispositivo. Sin embargo, con un iPad es posible tener varias escenas a la vez (por ejemplo, con un controlador de vista elemento flotante).

Hay muchas ventajas en el uso de guiones gráficos para crear la interfaz de usuario de la aplicación, sobre todo cuando se usa Xamarin. En primer lugar, es una representación visual de la interfaz de usuario, ya que todos los objetos, incluidos [los controles personalizados](~/ios/user-interface/designer/ios-designable-controls-overview.md) , se representan en tiempo de diseño. Esto significa que antes de compilar o implementar la aplicación puede visualizar su apariencia y flujo. Tome la imagen anterior, por ejemplo. Podemos saber de una mirada rápida a la superficie de diseño cuántas escenas hay, el diseño de cada vista y cómo se relaciona todo. Esto es lo que hace que los guiones gráficos sean tan eficaces.

Los eventos son más fáciles de administrar con los guiones gráficos, especialmente cuando se usa el diseñador de iOS. La mayoría de los controles de interfaz de usuario tendrán una lista de posibles eventos en el Panel de propiedades. El controlador de eventos se puede Agregar aquí y completarse en un método parcial en la clase de controladores de vista.

El contenido de un guión gráfico se almacena como un archivo XML. En tiempo de compilación, `.storyboard` los archivos se compilan en archivos binarios conocidos como NIBS. En tiempo de ejecución, estos NIBS se inicializan y se crean instancias para crear nuevas vistas.

## <a name="segues"></a>Objetos segue

Un objeto *segue*, o *segue*, se usa en el desarrollo de iOS para representar una transición entre escenas. Para crear un segue, mantenga presionada la tecla **Ctrl** y haga clic y arrastre de una escena a otra. A medida que se arrastra el mouse, aparece un conector azul que indica que el segue conducirá como se muestra en la imagen siguiente:

 [![](images/createsegue.png "Aparece un conector azul, que indica dónde se mostrará el segue como se muestra en esta imagen.")](images/createsegue.png#lightbox)

Al pasar el mouse, aparecerá un menú que nos permite elegir la acción para nuestro segue. Puede ser similar a las imágenes siguientes: 

**Clases de tamaño y 8 anteriores a iOS**:

[![](images/segue1.png "La lista desplegable de acciones segue sin clases de tamaño")](images/segue1.png#lightbox)

**Al usar clases de tamaño y objetos segue adaptables**:

[![](images/16new.png "La lista desplegable Action segue con clases de tamaño")](images/16new.png#lightbox)

> [!IMPORTANT]
> Si usa VMWare para su máquina virtual de Windows, Ctrl + clic se asigna como el botón _secundario_ del mouse de forma predeterminada. Para crear un segue, edite las preferencias del teclado **a través** > de los**métodos abreviados** de teclado de mouse **& mouse** > y reasigne el **botón secundario** , como se muestra a continuación:
> 
> [![](images/image22.png "Configuración de preferencias del teclado y del mouse")](images/image22.png#lightbox)
> 
> Ahora debería poder agregar un segue entre los controladores de vista de la forma habitual.

Hay diferentes tipos de transiciones, cada una de las cuales proporciona control sobre cómo se presenta al usuario un nuevo controlador de vista y cómo interactúa con otros controladores de vistas en el guión gráfico. Estos se explican a continuación. También es posible crear una subclase de un objeto segue para implementar una transición personalizada:

-  **Mostrar/** enviar: un segue de extracción agrega el controlador de vista a la pila de navegación. Se supone que el controlador de vista que origina la inserciones forma parte del mismo controlador de navegación que el controlador de vista que se va a agregar a la pila. Esto hace lo mismo que `pushViewController` y normalmente se usa cuando hay alguna relación entre los datos de las pantallas. El uso de segue de extracción le permite tener una barra de navegación con un botón atrás y un título agregado a cada vista de la pila, lo que permite explorar en profundidad la navegación por la jerarquía de vistas.
-  **Modal** : un segue modal crea una relación entre dos controladores de vista cualesquiera del proyecto, con la opción de mostrar una transición animada. El controlador de vista secundario ocultará completamente el controlador de vista primario cuando se ponga en vista. A diferencia de un segue de extracción, que agrega un botón atrás para nosotros; Cuando se usa un segue `DismissViewController` modal, se debe usar para volver al controlador de vista anterior.
-  **Personalizado** : cualquier segue personalizado se puede crear como una subclase de `UIStoryboardSegue`.
-  Desenredado: se puede usar un segue de desenredado para navegar hacia atrás a través de un segue de extracción o modal, por ejemplo, descartando el controlador de vista presentado de forma modal. Además, puede desenredar a través de no solo uno, sino también una serie de objetos segues de extracción y de moda y volver a varios pasos de la jerarquía de navegación con una sola acción de desenredado. Para saber cómo usar un segue de desenredado en iOS, lea la receta de creación de desenredado de [objetos segue](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/unwind_segue) .
-  Sin **origen** : una segue sin código fuente indica la escena que contiene el controlador de vista inicial y, por lo tanto, la vista que el usuario verá en primer lugar. Se representa mediante el segue que se muestra a continuación:  

    [![](images/sourcelesssegue.png "Un segue de código fuente")](images/sourcelesssegue.png#lightbox)

### <a name="adaptive-segue-types"></a>Tipos de segue adaptables

 iOS 8 presentó [las clases de tamaño](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) para permitir que un archivo de guión gráfico de iOS funcione con todos los tamaños de pantalla disponibles, lo que permite a los desarrolladores crear una interfaz de usuario para todos los dispositivos iOS. De forma predeterminada, todas las aplicaciones de Xamarin. iOS nuevas usarán clases de tamaño. Para usar clases de tamaño de un proyecto anterior, consulte la guía de [Introducción a guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) . 
 
Cualquier aplicación que use clases de tamaño también usará el nuevo [*objetos segue adaptable*](~/ios/user-interface/storyboards/unified-storyboards.md). Al usar clases de tamaño, recuerde que no vamos a especificar directamente si se usa un iPhone o iPad. En otras palabras, vamos a crear una interfaz de usuario que siempre tendrá el mismo aspecto, independientemente de la cantidad de espacio real con el que tenga que trabajar. El trabajo de objetos segue adaptable es juzgar el entorno y determinar el mejor modo de presentar el contenido. A continuación se muestran los objetos segue adaptables: 

[![](images/adaptivesegue.png "La lista desplegable Adaptive objetos segue")](images/adaptivesegue.png#lightbox)

|Segue|DESCRIPCIÓN|
|--- |--- |
|Mostrar|Esto es muy similar a un segue de extracción, pero tiene en cuenta el contenido de la pantalla.|
|Mostrar detalles|Si la aplicación muestra una vista maestra y de detalles (por ejemplo, en un controlador de vista de división en un iPad), el contenido reemplazará la vista de detalles. Si la aplicación muestra solo el maestro o detalle, el contenido reemplazará la parte superior de la pila del controlador de vista.|
|Presentación|Es similar al segue modal y permite seleccionar los estilos de presentación y de transición.|
|Presentación de elemento flotante|Esto presenta el contenido como un elemento flotante|

### <a name="transferring-data-with-segues"></a>Transferir datos con objetos segue

Las ventajas de una segue no terminan con las transiciones. También se pueden usar para administrar la transferencia de datos entre controladores de vista. Esto se logra invalidando el `PrepareForSegue` método en el controlador de vista inicial y controlando los datos por nosotros mismos. Cuando se desencadena segue (por ejemplo, con un botón), la aplicación llamará a este método, lo que proporciona una oportunidad para preparar el nuevo controlador de vista *antes* de que se produzca la navegación. En el código siguiente, en el ejemplo [Phoneword](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios) , se muestra: 


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

En este ejemplo, `PrepareForSegue` se llamará al método cuando el usuario desencadene el segue. Primero tenemos que crear una instancia del controlador de vista ' recibiendo ' y establecerla como el controlador de vista de destino de segue. Esto se realiza mediante la línea de código siguiente:

```csharp
var callHistoryController = segue.DestinationViewController as CallHistoryController;
```

El método ahora tiene la capacidad de establecer las propiedades en `DestinationViewController`. En este ejemplo, hemos aprovechado esto pasando una lista denominada `PhoneNumbers` `CallHistoryController` a y asignándole un objeto con el mismo nombre:.

```csharp
if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
```

Una vez finalizada la transición, el usuario verá la `CallHistoryController` con la lista rellenada.

## <a name="adding-a-storyboard-to-a-non-storyboard-project"></a>Agregar un guion gráfico a un proyecto que no es un guion gráfico

En ocasiones, puede que tenga que agregar un guion gráfico a un archivo que no es un guion gráfico. Una vez hecho esto en Visual Studio para Mac se puede simplificar mediante los pasos siguientes:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Cree un nuevo archivo de guion gráfico; para ello, vaya a **archivo > nuevo archivo > iOS > guion gráfico**, como se muestra a continuación: 
    
    [![](images/new-storyboard-xs.png "Cuadro de diálogo nuevo archivo")](images/new-storyboard-xs.png#lightbox)

2. Agregue el nombre del guion gráfico a la sección de la **interfaz principal** de **info. plist**, como se muestra a continuación:
    
    [![](images/infoplist.png "Editor de info. plist")](images/infoplist.png#lightbox)
    
    Esto hace el equivalente de crear una instancia del controlador de vista inicial `FinishedLaunching` en el método dentro del delegado de la aplicación. Con esta opción establecida, la aplicación crea una instancia de una ventana (vea más abajo), carga el guión gráfico principal y asigna una instancia del controlador de vista inicial del guión gráfico (el que está junto a la segue sin `RootViewController` código fuente) como la propiedad de la ventana y, a continuación, realiza una ventana visible en la pantalla.

3. En, invalide el `Window` método predeterminado, con el código siguiente para implementar la propiedad de la ventana: `AppDelegate`
        
        public override UIWindow Window {
            get;
            set;
            }
            
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Cree un nuevo archivo de guion gráfico haciendo clic con el botón derecho en el proyecto para **agregar > nuevo archivo > iOS > guion gráfico vacío**, como se muestra a continuación: 
    
    [![](images/new-storyboard-vs.png "Cuadro de diálogo nuevo elemento")](images/new-storyboard-vs.png#lightbox)

2. Agregue el nombre del guion gráfico a la sección **interfaz principal** de la aplicación de iOS, como se muestra a continuación:
    
    [![](images/ios-app.png "Editor de info. plist")](images/ios-app.png#lightbox)
    
    Esto hace el equivalente de crear una instancia del controlador de vista inicial `FinishedLaunching` en el método dentro del delegado de la aplicación. Con esta opción establecida, la aplicación crea una instancia de una ventana (vea más abajo), carga el guión gráfico principal y asigna una instancia del controlador de vista inicial del guión gráfico (el que está junto a la segue sin `RootViewController` código fuente) como la propiedad de la ventana y, a continuación, realiza una ventana visible en la pantalla.

3. En, invalide el `Window` método predeterminado, con el código siguiente para implementar la propiedad de la ventana: `AppDelegate`

        public override UIWindow Window {
            get;
            set;
            }
            
-----

## <a name="creating-a-storyboard-with-the-ios-designer"></a>Crear un guion gráfico con el diseñador de iOS

Se puede crear un guión gráfico mediante el Xamarin Designer para iOS, que se ha integrado sin problemas con Visual Studio para Mac y Visual Studio.

Para empezar a usar iOS Designer para crear guiones gráficos, siga la guía [Hello, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/index.md) . En este tutorial, explorará la navegación entre los controladores de vista mediante objetos segue y la forma de controlar los eventos en los controles.

## <a name="instantiate-storyboards-manually"></a>Crear instancias de guiones gráficos manualmente

Los guiones gráficos reemplazan por completo los archivos XIB individuales del proyecto, pero todavía se puede crear una instancia de los `Storyboard.InstantiateViewController`controladores de vista individuales de un guión gráfico mediante.

A veces, las aplicaciones tienen requisitos especiales que no se pueden controlar con las transiciones de guion gráfico integradas proporcionadas por el diseñador. Por ejemplo, si vamos a crear una aplicación que inicia diferentes pantallas del mismo botón, según el estado actual de una aplicación, es posible que desee crear una instancia de los controladores de vista manualmente y programar la transición.

En la captura de pantalla siguiente se muestran dos controladores de vista en nuestra superficie de diseño sin segue entre ellos. La siguiente sección le guiará por el modo en que se puede configurar la transición en el código.

 [![](images/viewcontrollerspink.png "En esta captura de pantalla se muestran dos controladores de vista en la superficie de diseño sin segue entre ellos.")](images/viewcontrollerspink.png#lightbox)

1. Agregue un _guion gráfico de iPhone vacío_ a un proyecto de proyecto existente:
    
    [![](images/add-storyboard1.png "Agregar guion gráfico")](images/add-storyboard1.png#lightbox)

2. Haga doble clic en el guion gráfico recién creado para abrirlo y agregar un nuevo **controlador de navegación** a la superficie de diseño. Como el controlador de navegación es sin interfaz de usuario, de forma predeterminada, se incluirá con un controlador de vista raíz, como se muestra a continuación:

    [![](images/uinavigationcontroller.png "Ver controladores con objetos segue")](images/uinavigationcontroller.png#lightbox)

3. Seleccione el _controlador de vista_ haciendo clic en la barra negra situada en la parte inferior. En el panel de **propiedades**del diseñador, en **identidad** , podemos especificar una clase personalizada, así como un identificador único para el controlador de vista. Establezca el **nombre de clase** y el identificador `MainViewController`de **guión gráfico** en.

    [![](images/identitypanelnew.png "Especificar clase personalizada")](images/identitypanelnew.png#lightbox)

4. Más adelante, tendrá que crear una instancia de los controladores de vista desde el guión gráfico y usará el identificador de guión gráfico para hacer referencia a ellos en el código. El establecimiento del identificador de restauración para que coincida con el identificador de guión gráfico garantiza que el controlador de vista se vuelva a crear correctamente si es necesario restaurar el estado.

5. Actualmente solo tenemos un controlador de vista. Arrastre otro controlador de vista a la superficie de diseño. En el **Panel de propiedades**, en identidad, establezca el identificador de clase y `PinkViewController`de guión gráfico en, como se muestra a continuación:

    [![](images/pinkvcnew.png "Panel de propiedades")](images/pinkvcnew.png#lightbox)
    
    El IDE creará estas clases personalizadas para los controladores de vista. Se pueden ver en el **Panel de solución**, como se muestra en la siguiente captura de pantalla:
    
    [![](images/solution-pad.png "Panel de solución")](images/solution-pad.png#lightbox)

6. En el `PinkViewController`, seleccione la vista haciendo clic en el centro del marco del controlador. En el Panel de propiedades, en ver, cambie el **fondo** a magenta:
    
    [![](images/pinkcontroller.png "Establecer el color de fondo")](images/pinkcontroller.png#lightbox)

7. Por último, arrastre un botón desde el cuadro de `MainViewController` **herramientas** hasta el. En el panel de propiedades, asígnele el nombre `PinkButton` y el título GoToPink, como se muestra a continuación:

    [![](images/pinkbutton.png "Establecer nombre de botón")](images/pinkbutton.png#lightbox)

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
            window.MakeKeyAndVisible ();
            return true;
        }

    }
```

Esta es una gran cantidad de código, pero solo algunas líneas son desconocidas. En primer lugar, registramos nuestro guion gráfico con el **AppDelegate** pasando el nombre del guión gráfico, **archivo mainstoryboard**. A continuación, se indica a la aplicación que cree una instancia de un controlador de vista inicial `InstantiateInitialViewController` desde el guión gráfico llamando a en nuestro guion gráfico y establecemos ese controlador de vistas como el controlador de vista raíz de la aplicación. Este método determina la primera pantalla que el usuario ve y crea una nueva instancia de ese controlador de vista.

Observe que, en el panel de la solución, el `MainViewcontroller.cs` IDE ha creado una `corresponding designer.cs` clase y su al agregar el nombre de la clase al panel de propiedades en el paso 4. Podemos ver que esta clase creó un constructor especial que incluye una clase base:

```csharp
public MainViewController (IntPtr handle) : base (handle) 
{
}
```


Al crear un guión gráfico mediante el diseñador, el IDE agregará automáticamente el atributo [[Register]](xref:Foundation.RegisterAttribute) en la parte superior `designer.cs` de la clase y pasará un identificador de cadena, que es idéntico al identificador de guión gráfico especificado en el paso anterior. Esto vinculará C# a la escena correspondiente en el guión gráfico.

En algún momento podría querer agregar una clase existente que **no** se creó en el diseñador. En este caso, registraría esta clase como normal:

```csharp
[Register ("MainViewController")]
public partial class MainViewController : UIViewController
{
public MainViewController (IntPtr handle) : base (handle) 
{
}

...
}
```

Para obtener más información sobre el registro de clases y métodos, consulte la documentación del registrador de [tipos](http://docs.xamarin.com/guides/ios/advanced_topics/registrar/) .

El último paso de esta clase es conectar el botón y la transición al controlador de vista de color rosa. Se creará una instancia `PinkViewController` del desde el guión gráfico; a continuación, se programará un `PushViewController`segue de extracción con, como se muestra en el código de ejemplo siguiente:

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

    public void Initialize(){

    //Instantiating View Controller with Storyboard ID 'PinkViewController'
    pinkViewController = Storyboard.InstantiateViewController ("PinkViewController") as PinkViewController;
    }

    public override void ViewDidLoad ()
    {
    base.ViewDidLoad ();

    //When we push the button, we will push the pinkViewController onto our current Navigation Stack
    PinkButton.TouchUpInside += (o, e) =&gt; {
        this.NavigationController.PushViewController (pinkViewController, true);
    };
    }

}
```

La ejecución de la aplicación produce una aplicación de dos pantallas:

![](images/finishedstoryboard.png "Pantallas de ejecución de la aplicación de ejemplo")

## <a name="conditional-segues"></a>Objetos segue condicional

A menudo, pasar de un controlador de vista a otro depende de una condición determinada. Por ejemplo, si hubiéramos realizado una pantalla de inicio de sesión simple, solo deseamos pasar a la siguiente pantalla *si* se ha comprobado el nombre de usuario y la contraseña.

En el ejemplo siguiente, vamos a agregar un campo de contraseña al ejemplo anterior. El usuario solo podrá tener acceso a *PinkViewController* si escribe la contraseña correcta; de lo contrario, se mostrará un error.

Antes de comenzar, siga los pasos 1 a 8 anteriores. En estos pasos se crea nuestro guion gráfico, se comienza a crear la interfaz de usuario y se indica a la aplicación que el controlador de vista que se va a usar como RootViewController.

1. Ahora, vamos a crear la interfaz de usuario y agregar las vistas adicionales que aparecen `MainViewController` en la lista para que parezca que en la siguiente captura de pantalla:

    - UITextField
        - Nombre: PasswordTextField
        - Falso ' Escriba la contraseña secreta '
    - UILabel
        - Negrita 'Error: Contraseña incorrecta. No debe pasar! '
        - Color: Rojo
        - Ecuación Centrar
        - Cuadrícula 2
        - Casilla ' Hidden ' activada    
        
    [![](images/passwordvc.png "Líneas centrales")](images/passwordvc.png#lightbox)
    
2. Cree un segue entre el botón ir a rosa y el controlador de vista; para ello, presione Ctrl y arrastre desde el *PinkButton* hasta el *PinkViewController*, **y seleccione Activar** al pasar el mouse. 

3. Haga clic en segue y asígnele el *identificador* `SegueToPink`:

    [![](images/namesegue.png "Haga clic en segue y asígnele el identificador SegueToPink")](images/namesegue.png#lightbox)  
    

4. Por último, agregue el siguiente método ShouldPerformSegue a `MainViewController` la clase:

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

En este código hemos coincidente con el segueIdentifier en nuestro `SegueToPink` segue, por lo que podemos probar una condición; una contraseña válida en este caso. Si se devuelve `true`la condición, segue realizará y `PinkViewController`presentará. Si `false`es, no se presentará el nuevo controlador de vista.

Podemos aplicar este enfoque a cualquier segue de este controlador de vista comprobando el argumento segueIdentifier en el método ShouldPerformSegue. En este caso, solo tenemos un identificador segue: `SegueToPink`.

Consulte la solución storyboards. Conditional en el [ejemplo de guiones gráficos manuales](https://docs.microsoft.com/samples/xamarin/ios-samples/manualstoryboard) para obtener un ejemplo práctico.

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>Usar referencias a guion gráfico

Una referencia de guion gráfico permite tomar un diseño de guion gráfico grande y complejo y dividirlo en guiones gráficos más pequeños a los que se hace referencia desde el original, con lo que se elimina la complejidad y se facilita el diseño y el mantenimiento de los guiones gráficos resultantes.

Además, una referencia de guion gráfico puede proporcionar un delimitador a otra escena en el mismo guión gráfico o en una determinada escena en otro.

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>Referencia a un guión gráfico externo

Para agregar una referencia a un guion gráfico externo, haga lo siguiente:

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el nombre del proyecto y seleccione **Agregar** > **nuevo archivo..** . Guion gráfico de **iOS** > .  >  Escriba un **nombre** para el nuevo guion gráfico y haga clic en el botón **nuevo** :
    
    [![](images/ref01.png "Cuadro de diálogo nuevo archivo")](images/ref01.png#lightbox)
    
2. Diseñe el diseño de las escenas del nuevo guión gráfico como lo haría normalmente y guarde los cambios: 
    
    [![](images/ref02.png "El diseño de la nueva escena")](images/ref02.png#lightbox)
    
3. Abra el guion gráfico al que va a agregar la referencia en el diseñador de iOS.

4. Arrastre una **referencia de guion gráfico** desde el **cuadro de herramientas** hasta el superficie de diseño: 
    
    [![](images/ref03.png "Referencia A un guión gráfico")](images/ref03.png#lightbox)
    
5. En la pestaña **Widget** del **Explorador de propiedades**, seleccione el nombre del **guion gráfico** que creó anteriormente: 

    [![](images/ref04.png "La pestaña widget")](images/ref04.png#lightbox)
    
6. Control: haga clic en un widget de interfaz de usuario (como un botón) en una escena existente y cree un nuevo segue en la **referencia de guion gráfico** que acaba de crear: 

    [![](images/ref05.png "Creación de un segue")](images/ref05.png#lightbox) 
    
7. En el menú emergente, seleccione **Mostrar** para completar el segue: 

    [![](images/ref06.png "Seleccionar Mostrar para completar el segue")](images/ref06.png#lightbox) 
    
8. Guarde los cambios en el guion gráfico.

Cuando se ejecuta la aplicación y el usuario hace clic en el elemento de la interfaz de usuario desde el que se creó el segue, se mostrará el controlador de vista inicial del guión gráfico externo especificado en la referencia de guion gráfico.

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>Hacer referencia a una escena específica en un guión gráfico externo

Para agregar una referencia a una escena específica, un guion gráfico externo (y no el controlador de vista inicial), haga lo siguiente:

1. En el **Explorador de soluciones**, haga doble clic en el guion gráfico externo para abrirlo para su edición.

2. Agregue una nueva escena y diseñe su diseño como lo haría normalmente: 

    [![](images/ref07.png "El nuevo diseño de la escena")](images/ref07.png#lightbox)
    
3. En la pestaña **Widget** del **Explorador de propiedades**, escriba un **identificador de guión gráfico** para el controlador de vista de la nueva escena: 

    [![](images/ref08.png "Escriba un identificador de guión gráfico para el nuevo controlador de vista de escenas")](images/ref08.png#lightbox)
    
4. Abra el guion gráfico al que va a agregar la referencia en el diseñador de iOS.

5. Arrastre una **referencia de guion gráfico** desde el **cuadro de herramientas** hasta el superficie de diseño: 

    [![](images/ref03.png "Referencia A un guión gráfico")](images/ref03.png#lightbox)
    
6. En la pestaña **Widget** del **Explorador de propiedades**, seleccione el nombre del **guión gráfico** y el **ID. de referencia** (identificador de guión gráfico) de la escena que creó anteriormente: 

    [![](images/ref09.png "La pestaña widget")](images/ref09.png#lightbox)
    
7. Control: haga clic en un widget de interfaz de usuario (como un botón) en una escena existente y cree un nuevo segue en la **referencia de guion gráfico** que acaba de crear: 

    [![](images/ref10.png "Creación de un segue")](images/ref10.png#lightbox) 
    
8. En el menú emergente, seleccione **Mostrar** para completar el segue: 

    [![](images/ref06.png "Seleccionar Mostrar para completar el segue")](images/ref06.png#lightbox) 
    
9. Guarde los cambios en el guion gráfico.

Cuando se ejecuta la aplicación y el usuario hace clic en el elemento de la interfaz de usuario en el que ha creado el segue, se mostrará la escena con el **identificador de guión gráfico** especificado desde el guión gráfico externo especificado en la referencia de guion gráfico.

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>Hacer referencia a una escena específica en el mismo guion gráfico

Para agregar una referencia a una escena específica del mismo guión gráfico, haga lo siguiente:

1. En el **Explorador de soluciones**, haga doble clic en el guion gráfico para abrirlo para su edición.

2. Agregue una nueva escena y diseñe su diseño como lo haría normalmente: 

    [![](images/ref11.png "El nuevo diseño de la escena")](images/ref11.png#lightbox)

3. En la pestaña **Widget** del **Explorador de propiedades**, escriba un **identificador de guión gráfico** para el controlador de vista de la nueva escena: 

    [![](images/ref12.png "La pestaña widget")](images/ref12.png#lightbox)
    
4. Arrastre una **referencia de guion gráfico** desde el **cuadro de herramientas** hasta el superficie de diseño: 

   [![](images/ref03.png "Referencia A un guión gráfico")](images/ref03.png#lightbox)
    
5. En la pestaña **Widget** del **Explorador de propiedades**, seleccione **ID. de referencia** (identificador de guión gráfico) de la escena que creó anteriormente: 

    [![](images/ref13.png "La pestaña widget")](images/ref13.png#lightbox)
    
6. Control: haga clic en un widget de interfaz de usuario (como un botón) en una escena existente y cree un nuevo segue en la **referencia de guion gráfico** que acaba de crear: 

    [![](images/ref14.png "Creación de un segue")](images/ref14.png#lightbox) 
    
7. En el menú emergente, seleccione **Mostrar** para completar el segue: 

    [![](images/ref06.png "Seleccionar Mostrar para completar el segue")](images/ref06.png#lightbox) 
    
8. Guarde los cambios en el guion gráfico.

Cuando se ejecuta la aplicación y el usuario hace clic en el elemento de la interfaz de usuario en el que ha creado el segue, se mostrará la escena con el **identificador de guión gráfico** especificado en el mismo guion gráfico especificado en la referencia de guion gráfico.

## <a name="summary"></a>Resumen

En este artículo se presenta el concepto de guiones gráficos y cómo pueden ser beneficiosos en el desarrollo de aplicaciones de iOS. Se describen las escenas, los controladores de vista, las vistas y las jerarquías de vistas y cómo se vinculan las escenas con distintos tipos de objetos segue.  También explora la creación de instancias de los controladores de vista manualmente desde un guion gráfico y la creación de objetos segue condicionales.



## <a name="related-links"></a>Vínculos relacionados

- [Guion gráfico manual (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/manualstoryboard/)
- [Introducción al diseñador de iOS](~/ios/user-interface/designer/introduction.md)
- [Convertir en guiones gráficos](https://developer.apple.com/library/ios/#releasenotes/Miscellaneous/RN-AdoptingStoryboards/)
- [Referencia de la clase UIStoryboard](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIStoryboard_Class/Reference/Reference.html)
