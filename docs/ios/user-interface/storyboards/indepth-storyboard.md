---
title: Guiones gráficos en Xamarin. iOS
description: En este documento se proporciona una introducción a los guiones gráficos en Xamarin. iOS. Describe cómo se usa un guion gráfico para definir una interfaz de usuario, objetos segue, y cómo usar el diseñador de iOS para editar archivos de guion gráfico.
ms.prod: xamarin
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: e6bbc9d7d404136984e3d1ec93186dc85691c751
ms.sourcegitcommit: d1f0e0a9100548cfe0960ed2225b979cc1d7c28f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2020
ms.locfileid: "96439746"
---
# <a name="storyboards-in-xamarinios"></a>Guiones gráficos en Xamarin. iOS

En esta guía, explicaremos qué es un guion gráfico y examinaremos algunos de los componentes clave, como objetos segue. Veremos cómo se pueden crear y usar guiones gráficos y qué ventajas tienen para un desarrollador.

Antes de que Apple introdujera el formato de archivo de guion gráfico como una representación visual de la interfaz de usuario de una aplicación de iOS, los desarrolladores creaban archivos XIB para cada controlador de vista y programaban la navegación entre cada vista manualmente.  El uso de un guion gráfico permite al desarrollador definir tanto controladores de vista como la navegación entre ellos en una superficie de diseño y ofrece la edición WYSIWYG de la interfaz de usuario de la aplicación.

Un guion gráfico se puede crear y abrir con Visual Studio para Mac. En esta guía también se explica cómo usar Xcode Interface Builder para crear los guiones gráficos mientras se usa C# para programar la navegación.

## <a name="requirements"></a>Requisitos

Los guiones gráficos se pueden usar con Xcode e iniciar desde dentro de un proyecto de Xamarin. iOS en Visual Studio para Mac.

## <a name="what-is-a-storyboard"></a>¿Qué es un guion gráfico?

Un guion gráfico es la representación visual de todas las pantallas de una aplicación. Contiene una secuencia de escenas, donde cada escena representa un *controlador de vista* y sus *vistas*. Estas vistas pueden contener objetos y [controles](~/ios/user-interface/controls/index.md) que permiten al usuario interactuar con la aplicación. Esta colección de vistas y controles (o *subvistas*) se conoce como una *jerarquía* de vistas de contenido. Las escenas están conectadas mediante objetos segue, que representan una transición entre controladores de vista. Normalmente, esto se consigue creando un segue entre un objeto de la vista inicial y la vista de conexión. Las relaciones en la superficie de diseño se muestran en la siguiente imagen:

 [![En esta imagen se ilustran las relaciones en la superficie de diseño](images/storyboardsview.png)](images/storyboardsview.png#lightbox)

Como se muestra, el guión gráfico dispone cada una de las escenas con contenido ya representado y muestra las conexiones entre ellas. Cuando hablamos de escenas en un iPhone, es seguro suponer que una *escena* del guion gráfico es igual a una *pantalla* de contenido del dispositivo. Sin embargo, con un iPad, es posible que aparezcan varias escenas a la vez, por ejemplo, con un controlador de vista de elemento flotante.

Hay muchas ventajas en el uso de guiones gráficos para crear la interfaz de usuario de la aplicación, sobre todo cuando se usa Xamarin. En primer lugar, es una representación visual de la interfaz de usuario, ya que todos los objetos, incluidos [los controles personalizados](../designer/ios-designable-controls-overview.md) , se representan en tiempo de diseño.
Esto significa que antes de compilar o implementar la aplicación, puede visualizar su apariencia y flujo. Tome la imagen anterior, por ejemplo. Podemos saber de una mirada rápida a la superficie de diseño cuántas escenas hay, el diseño de cada vista y cómo se relaciona todo. Esto es lo que hace que los guiones gráficos sean tan eficaces.

También puede administrar eventos con guiones gráficos.  La mayoría de los controles de interfaz de usuario tienen una lista de posibles eventos en el Panel de propiedades. El controlador de eventos se puede Agregar aquí y completarse en un método parcial en la clase View Controllers.

El contenido de un guión gráfico se almacena como un archivo XML. En tiempo de compilación, los `.storyboard` archivos se compilan en archivos binarios conocidos como NIBS. En tiempo de ejecución, estos NIBS se inicializan y se crean instancias para crear nuevas vistas.

## <a name="segues"></a>Objetos segue

Un objeto *segue*, o *segue*, se usa en el desarrollo de iOS para representar una transición entre escenas. Para crear un segue, mantenga presionada la tecla **Ctrl** y haga clic y arrastre de una escena a otra. Al arrastrar el mouse, aparece un conector azul que indica dónde conducirá el segue. Esto se muestra en la siguiente imagen:

 [![Aparece un conector azul, que indica dónde se mostrará el segue como se muestra en esta imagen.](images/createsegue.png)](images/createsegue.png#lightbox)

Al hacer clic con el mouse, aparece un menú que le permite elegir la acción para el segue. Puede ser similar a las siguientes imágenes:

**Clases de tamaño y 8 anteriores a iOS**:

[![La lista desplegable de acciones segue sin clases de tamaño](images/segue1.png)](images/segue1.png#lightbox)

**Al usar clases de tamaño y objetos segue adaptables**:

[![La lista desplegable Action segue con clases de tamaño](images/16new.png)](images/16new.png#lightbox)

> [!IMPORTANT]
> Si usa VMWare para su máquina virtual de Windows, Ctrl + clic se asigna como el botón _secundario_ del mouse de forma predeterminada. Para crear un segue, edite las preferencias del **teclado a través de** los  >  métodos abreviados de teclado de mouse **& Mouse**  >  **Mouse Shortcuts** y reasigne el **botón secundario** , como se muestra a continuación:
>
> [![Configuración de preferencias del teclado y del mouse](images/image22.png)](images/image22.png#lightbox)
>
> Ahora debería poder agregar un segue entre los controladores de vista de la forma habitual.

Hay diferentes tipos de transiciones, cada una de las cuales proporciona control sobre cómo se presenta al usuario un nuevo controlador de vista y cómo interactúa con otros controladores de vistas en el guión gráfico. Estos se explican a continuación. También es posible crear una subclase de un objeto segue para implementar una transición personalizada:

- **Mostrar/** enviar: un segue de extracción agrega el controlador de vista a la pila de navegación. Se supone que el controlador de vista que origina la inserciones forma parte del mismo controlador de navegación que el controlador de vista que se va a agregar a la pila. Esto hace lo mismo que  `pushViewController` y normalmente se usa cuando hay alguna relación entre los datos de las pantallas. El uso de segue de extracción le permite tener una barra de navegación con un botón atrás y un título agregado a cada vista de la pila, lo que permite explorar en profundidad la navegación por la jerarquía de vistas.
- **Modal** : un segue modal crea una relación entre dos controladores de vista cualesquiera del proyecto, con la opción de mostrar una transición animada. El controlador de vista secundario ocultará completamente el controlador de vista primario cuando se ponga en vista. A diferencia de un segue de extracción, que agrega un botón atrás, debe usar `DismissViewController` al usar un segue modal para volver al controlador de vista anterior.
- **Personalizado** : cualquier segue personalizado se puede crear como una subclase de `UIStoryboardSegue` .
- **Desenredado** : se puede usar un segue de desenredado para navegar hacia atrás a través de un segue de extracción o modal, por ejemplo, descartando el controlador de vista presentado de forma modal. Además, puede desenredar a través de no solo uno, sino también una serie de objetos segues de extracción y de moda y volver a varios pasos de la jerarquía de navegación con una sola acción de desenredado. Para saber cómo usar un segue de desenredado en iOS, lea la receta de  [creación de desenredado de objetos segue](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/unwind_segue) .
- Sin **origen** : una segue sin código fuente indica la escena que contiene el controlador de vista inicial y, por lo tanto, la vista que el usuario verá en primer lugar. Se representa mediante el segue que se muestra aquí:

    [![Un segue de código fuente](images/sourcelesssegue.png)](images/sourcelesssegue.png#lightbox)

### <a name="adaptive-segue-types"></a>Tipos de segue adaptables

 iOS 8 presentó [las clases de tamaño](../storyboards/unified-storyboards.md#size-classes) para permitir que un archivo de guión gráfico de iOS funcione con todos los tamaños de pantalla disponibles, lo que permite a los desarrolladores crear una interfaz de usuario para todos los dispositivos iOS. De forma predeterminada, todas las aplicaciones de Xamarin. iOS nuevas usan clases de tamaño. Para usar clases de tamaño de un proyecto anterior, consulte la guía de [Introducción a guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) .

Cualquier aplicación que use clases de tamaño también usará el nuevo [*objetos segue adaptable*](unified-storyboards.md). Cuando use clases de tamaño, recuerde que no está especificando directamente si usa un iPhone o un iPad. En otras palabras, va a crear una interfaz de usuario que siempre tendrá el mismo aspecto, independientemente de la cantidad de espacio real con el que tenga que trabajar. El trabajo de objetos segue adaptable es juzgar el entorno y determinar la mejor presentación del contenido. A continuación se muestran los objetos segue adaptables:

[![La lista desplegable Adaptive objetos segue](images/adaptivesegue.png)](images/adaptivesegue.png#lightbox)

|Segue|Descripción|
|--- |--- |
|Presentación|Esto es muy similar a un segue de extracción, pero tiene en cuenta el contenido de la pantalla.|
|Mostrar detalles|Si la aplicación muestra una vista maestra y de detalles (por ejemplo, en un controlador de vista de división en un iPad), el contenido reemplazará la vista de detalles. Si la aplicación muestra solo el maestro o detalle, el contenido reemplazará la parte superior de la pila del controlador de vista.|
|Presentación|Es similar al segue modal y permite seleccionar los estilos de presentación y de transición.|
|Presentación de elemento flotante|Esto presenta el contenido como un elemento flotante.|

### <a name="transferring-data-with-segues"></a>Transferir datos con objetos segue

Las ventajas de una segue no terminan con las transiciones. También se pueden usar para administrar la transferencia de datos entre controladores de vista. Esto se logra invalidando el `PrepareForSegue` método en el controlador de vista inicial y controlando los datos por sí mismo. Cuando se desencadena segue (por ejemplo, con un botón), la aplicación llamará a este método, lo que proporciona una oportunidad para preparar el nuevo controlador de vista *antes* de que se produzca la navegación. El siguiente código del ejemplo [Phoneword](/samples/xamarin/ios-samples/hello-ios) muestra esto:

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

En este ejemplo, se `PrepareForSegue` llamará al método cuando el usuario desencadene el segue. Primero debe crear una instancia del controlador de vista ' recibiendo ' y establecerla como controlador de vista de destino de segue. Esto se realiza mediante la línea de código siguiente:

```csharp
var callHistoryController = segue.DestinationViewController as CallHistoryController;
```

El método ahora tiene la capacidad de establecer las propiedades en `DestinationViewController` . En este ejemplo se aprovecha esa capacidad si se pasa una lista denominada `PhoneNumbers` a `CallHistoryController` y se asigna a un objeto con el mismo nombre:

```csharp
if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
```

Una vez finalizada la transición, el usuario verá la `CallHistoryController` con la lista rellenada.

## <a name="adding-a-storyboard-to-a-non-storyboard-project"></a>Agregar un guion gráfico a un proyecto que no es un guion gráfico

En alguna ocasión, es posible que necesite agregar un guion gráfico a un archivo que no sea un guion gráfico. Puede simplificar el proceso en Visual Studio para Mac siguiendo estos pasos:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Cree un nuevo archivo de guion gráfico; para ello, vaya a **archivo > nuevo archivo > iOS > guion gráfico**.

    [![Cuadro de diálogo nuevo archivo](images/new-storyboard-xs.png)](images/new-storyboard-xs.png#lightbox)

2. Agregue el nombre del guion gráfico a la sección de la **interfaz principal** de **info. plist**.

    [![Editor de info. plist](images/infoplist.png)](images/infoplist.png#lightbox)

    Esto hace el equivalente de crear una instancia del controlador de vista inicial en el `FinishedLaunching` método dentro del delegado de la aplicación. Con esta opción establecida, la aplicación crea una instancia de una ventana (vea el paso siguiente), carga el guión gráfico principal y asigna una instancia del controlador de vista inicial del guión gráfico (el que está junto a la segue sin código fuente) como la `RootViewController` propiedad de la ventana. A continuación, hace que la ventana esté visible en la pantalla.

3. En `AppDelegate` , invalide el `Window` método predeterminado con el código siguiente para implementar la propiedad de la ventana:

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Cree un nuevo archivo de guion gráfico haciendo clic con el botón derecho en el proyecto para **agregar > nuevo archivo > iOS > guion gráfico vacío**.

    [![Cuadro de diálogo nuevo elemento](images/new-storyboard-vs.png)](images/new-storyboard-vs.png#lightbox)

2. Agregue el nombre del guion gráfico a la sección **interfaz principal** de la aplicación de iOS.

    [![Editor de info. plist](images/ios-app.png)](images/ios-app.png#lightbox)

    Esto hace el equivalente de crear una instancia del controlador de vista inicial en el `FinishedLaunching` método dentro del delegado de la aplicación. Con esta opción establecida, la aplicación crea una instancia de una ventana (vea el paso siguiente), carga el guión gráfico principal y asigna una instancia del controlador de vista inicial del guión gráfico (el que está junto a la segue sin código fuente) como la `RootViewController` propiedad de la ventana. A continuación, hace que la ventana esté visible en la pantalla.

3. En `AppDelegate` , invalide el `Window` método predeterminado con el código siguiente para implementar la propiedad de la ventana:

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

-----

## <a name="creating-a-storyboard-with-xcode"></a>Crear un guion gráfico con Xcode

Un guion gráfico se puede crear y modificar mediante Xcode para su uso en las aplicaciones de iOS desarrolladas con Visual Studio para Mac.

Los guiones gráficos reemplazan por completo los archivos XIB individuales del proyecto, pero todavía se puede crear una instancia de los controladores de vista individuales de un guión gráfico mediante `Storyboard.InstantiateViewController` .

A veces, las aplicaciones tienen requisitos especiales que no se pueden controlar con las transiciones de guion gráfico integradas proporcionadas por el diseñador. Por ejemplo, si fuera a crear una aplicación que inicia pantallas diferentes desde el mismo botón, dependiendo del estado actual de una aplicación, puede que desee crear una instancia de los controladores de vista manualmente y programar la transición usted mismo.

En la captura de pantalla siguiente se muestran dos controladores de vista en la superficie de diseño sin segue entre ellos. En la siguiente sección se describe cómo se puede configurar la transición en el código.

1. Agregue un _guion gráfico de iPhone vacío_ a un proyecto de proyecto existente:

    [![Agregar guion gráfico](images/add-storyboard2.png)](images/add-storyboard2.png#lightbox)

2. Haga clic con el botón derecho en el archivo de guion gráfico y seleccione **abrir con > Xcode Interface Builder** para abrirlo en Xcode.

   > Tip Si prefiere usar el generador de interfaz de Xcode de forma predeterminada, puede elegirlo en el Visual Studio para Mac preferencias en **proyectos > iOS**.  A partir de la versión 16,9 de Visual Studio y Visual Studio para Mac 8,9, Xcode Interface Builder será la única opción. *

   ![Seleccionar la herramienta de diseñador preferida.](images/set-preferred-designer-tool.png)

3. En Xcode, abra la biblioteca (a través de la **vista > Mostrar biblioteca** o **Mayús + Comando + L**) para mostrar una lista de objetos que se pueden agregar al guion gráfico. Agregue un `Navigation Controller` al guion gráfico arrastrando el objeto desde la lista hasta el guión gráfico. De forma predeterminada, el `Navigation Controller` proporcionará dos pantallas. La pantalla de la derecha es la que se va a `TableViewController` reemplazar con una vista más simple, por lo que se puede quitar haciendo clic en la vista y presionando la tecla Supr.

    [![Adición de un NavigationController desde la biblioteca](images/add-navigation-controller.png)](images/add-navigation-controller.png#lightbox)

4. Este controlador de vista tendrá su propia clase personalizada y también necesita su propio identificador de guión gráfico. Al hacer clic en el cuadro situado encima de esta vista recién agregada, hay tres iconos, el situado más a la izquierda, que representa el controlador de vista de la vista. Al seleccionar este icono, puede establecer los valores de clase e identificador en la pestaña identidad del panel derecho. Establezca estos valores en `MainViewController` y asegúrese de comprobar `Use Storyboard ID` .

    [![Establecer MainViewController en el panel identidad](images/identity-panel.png)](images/identity-panel.png#lightbox)

5. Con la biblioteca de nuevo, arrastre un control controlador de vista a la pantalla. Se establecerá como el controlador de vista raíz. Manteniendo presionada la tecla **control** , haga clic y arrastre desde el controlador de navegación de la izquierda hasta el controlador de vista que acaba de agregar a la derecha y seleccione **controlador de vista raíz** en el menú.

    [![Agregar un NavigationController desde la biblioteca y establecer MainViewController como controlador de vista raíz](images/add-view-controller.png)](images/add-view-controller.png#lightbox)

6. Esta aplicación se desplazará a otra vista, así que agregue una vista más al guion gráfico, como antes. Llámelo `PinkViewController` y establezca esos valores de la misma manera que con `MainViewController` .

    [![Agregar un controlador de vista adicional](images/add-additional-view-controller.png)](images/add-additional-view-controller.png#lightbox)

7. Puesto que el controlador de vistas tendrá un fondo rosa, establezca esa propiedad en el panel atributos mediante la lista desplegable junto a `Background` .

    [![Agregar un controlador de vista adicional](images/set-pink-background.png)](images/set-pink-background.png#lightbox)

8. Dado que deseamos `MainViewController` que navegue hasta el `PinkViewController` , el primero necesitará un botón para interactuar con él. Utilice la biblioteca para agregar un botón a `MainViewController` .

    [![Agregar un botón a MainViewController](images/add-button.png)](images/add-button.png#lightbox)

El guión gráfico está completo, pero si implementa el proyecto ahora, obtendrá una pantalla en blanco. Esto se debe a que aún necesita indicar al IDE que use el guión gráfico y configurar un controlador de vista raíz para que sirva como primera vista. Normalmente, esto se puede hacer a través de las opciones del proyecto, tal y como se mostró anteriormente. Sin embargo, en este ejemplo, agregaremos el código siguiente a **AppDelegate** para lograr el mismo resultado:

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

Esta es una gran cantidad de código, pero solo algunas líneas son desconocidas. En primer lugar, registre el guión gráfico con el **AppDelegate** pasando el nombre del guión gráfico, **archivo mainstoryboard**. A continuación, se indica a la aplicación que cree una instancia de un controlador de vista inicial desde el guión gráfico mediante una llamada a `InstantiateInitialViewController` en el guión gráfico y establece ese controlador de vistas como el controlador de vista raíz de la aplicación. Este método determina la primera pantalla que el usuario ve y crea una nueva instancia de ese controlador de vista.

Observe en el panel de la solución que el IDE creó una `MainViewcontroller.cs` clase y su `*.designer.cs` archivo correspondiente cuando agregó el nombre de la clase al panel de propiedades en el paso 4. Esta clase creó un constructor especial que incluye una clase base:

```csharp
public MainViewController (IntPtr handle) : base (handle)
{
}
```

Al crear un guion gráfico con Xcode, el IDE agregará automáticamente el atributo [[Register]](xref:Foundation.RegisterAttribute) en la parte superior de la `*.designer.cs` clase y pasará un identificador de cadena, que es idéntico al identificador de guión gráfico especificado en el paso anterior. Se vinculará el C# a la escena correspondiente en el guión gráfico.

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

Para obtener más información sobre el registro de clases y métodos, vea [tipo de registrador](../../internals/registrar.md).

El último paso de esta clase es conectar el botón y la transición al controlador de vista de color rosa. Creará una instancia del `PinkViewController` desde el guión gráfico; a continuación, programará un segue de extracción con `PushViewController` , como se muestra en el código de ejemplo siguiente:

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

![Pantallas de ejecución de la aplicación de ejemplo](images/finishedstoryboard.png)

## <a name="conditional-segues"></a>Objetos segue condicional

A menudo, pasar de un controlador de vista a otro depende de una condición determinada. Por ejemplo, si vamos a crear una pantalla de inicio de sesión simple, solo deseamos pasar a la siguiente pantalla *si* se ha comprobado el nombre de usuario y la contraseña.

En el ejemplo siguiente, vamos a agregar un campo de contraseña al ejemplo anterior. El usuario solo podrá tener acceso a *PinkViewController* si escribe la contraseña correcta; de lo contrario, se mostrará un error.

Antes de comenzar, siga los pasos 1 a 8 anteriores. En estos pasos se crea nuestro guion gráfico, se comienza a crear la interfaz de usuario y se indica a la aplicación que el controlador de vista que se va a usar como RootViewController.

1. Ahora, vamos a crear la interfaz de usuario y agregar las vistas adicionales que aparecen en la lista `MainViewController` para que parezca que en la captura de pantalla siguiente:

    - UITextField
        - Nombre: PasswordTextField
        - Marcador de posición: ' escriba la contraseña secreta '
    - UILabel
        - Texto: ' error: contraseña incorrecta. No debe pasar! '
        - Color rojo
        - Alineación: Centro
        - Líneas: 2
        - Casilla ' Hidden ' activada    

    [![Líneas centrales](images/passwordvc.png)](images/passwordvc.png#lightbox)

2. Cree un segue entre el botón ir a rosa y el controlador de vista, para lo que debe arrastrar el control desde el *PinkButton* al *PinkViewController* y, a continuación **, seleccione Activar al pasar** el mouse.

3. Haga clic en segue y asígnele el *identificador* `SegueToPink` :

    [![Haga clic en segue y asígnele el identificador SegueToPink](images/namesegue.png)](images/namesegue.png#lightbox)  

4. Por último, agregue el `ShouldPerformSegue` método siguiente a la `MainViewController` clase:

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

En este código hemos coincidentes con segueIdentifier en nuestro `SegueToPink` segue, por lo que podemos probar una condición; en este caso, una contraseña válida. Si se devuelve la condición `true` , segue realizará y presentará `PinkViewController` . Si `false` es, no se presentará el nuevo controlador de vista.

Podemos aplicar este enfoque a cualquier segue de este controlador de vista comprobando el argumento segueIdentifier en el método ShouldPerformSegue. En este caso, solo tenemos un identificador segue: `SegueToPink` .

Consulte la solución storyboards. Conditional en el [ejemplo de guiones gráficos manuales](/samples/xamarin/ios-samples/manualstoryboard) para obtener un ejemplo práctico.

## <a name="summary"></a>Resumen

En este artículo se presenta el concepto de guiones gráficos y cómo pueden ser beneficiosos en el desarrollo de aplicaciones de iOS. Se describen las escenas, los controladores de vista, las vistas y las jerarquías de vistas, y cómo se vinculan las escenas con distintos tipos de objetos segue.  También se explora la creación de instancias de los controladores de vista manualmente desde un guion gráfico y la creación de objetos segue condicional.

## <a name="related-links"></a>Vínculos relacionados

- [Guion gráfico manual (ejemplo)](/samples/xamarin/ios-samples/manualstoryboard/)
- [Introducción al diseñador de iOS](../designer/introduction.md)
- [Convertir en guiones gráficos](https://developer.apple.com/library/ios/#releasenotes/Miscellaneous/RN-AdoptingStoryboards/)
- [Referencia de la clase UIStoryboard](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIStoryboard_Class/Reference/Reference.html)
