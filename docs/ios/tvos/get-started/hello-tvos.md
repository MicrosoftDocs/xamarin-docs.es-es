---
title: Hola, tvOS Guía de inicio rápido
description: En esta guía se explica cómo crear su primera aplicación de Xamarin. tvOS y su cadena de herramientas de desarrollo. También presenta Xamarin Designer, que expone controles de interfaz de usuario al código y muestra cómo compilar, ejecutar y probar una aplicación de Xamarin. tvOS.
ms.prod: xamarin
ms.assetid: 6E0AFE58-A13B-492F-861E-D5D73EB1C4A3
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 02/02/2018
ms.openlocfilehash: 9ad1c63dae312546315406d40858ce24802c6a58
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769310"
---
# <a name="hello-tvos-quick-start-guide"></a>Hola, tvOS Guía de inicio rápido

_En esta guía se explica cómo crear su primera aplicación de Xamarin. tvOS y su cadena de herramientas de desarrollo. También presenta Xamarin Designer, que expone controles de interfaz de usuario al código y muestra cómo compilar, ejecutar y probar una aplicación de Xamarin. tvOS._

Apple ha lanzado la quinta generación de Apple TV, Apple TV 4K, que ejecuta tvOS 11.

La plataforma Apple TV está abierta a los desarrolladores, lo que les permite crear sofisticadas aplicaciones envolventes y liberarlas a través de la tienda de aplicaciones integrada de Apple TV.

Si está familiarizado con el desarrollo de Xamarin. iOS, debe encontrar la transición a tvOS bastante sencilla. La mayoría de las API y características son las mismas; sin embargo, muchas API comunes no están disponibles (como WebKit). Además, al trabajar con el Siri remoto se plantean algunos desafíos de diseño que no están presentes en los dispositivos iOS basados en la pantalla táctil.

En esta guía se ofrece una introducción al uso de tvOS en una aplicación de Xamarin. Para obtener más información sobre tvOS, consulte la documentación sobre el [Prepárese de Apple para Apple TV 4k](https://developer.apple.com/tvos/) .

## <a name="overview"></a>Información general

Xamarin. tvOS permite desarrollar aplicaciones de Apple TV totalmente nativas en C# y .net con las mismas bibliotecas de OS X y controles de interfaz que se usan al desarrollar en *SWIFT* (o *Objective-C*) y *Xcode*.

Además, dado que las aplicaciones de Xamarin. tvOS C# se escriben en y .net, el código de back-end común puede compartirse con las aplicaciones de Xamarin. iOS, Xamarin. Android y Xamarin. Mac. todo ello al tiempo que ofrece una experiencia nativa en cada plataforma.

En este artículo se presentan los conceptos básicos necesarios para crear una aplicación de Apple TV con Xamarin. tvOS y Visual Studio recorriendo el proceso de creación de una aplicación **Hello, tvOS** básica que cuente el número de veces que se ha haga clic en un botón:

[![](hello-tvos-images/run05.png "Ejemplo de ejecución de la aplicación")](hello-tvos-images/run05.png#lightbox)

Trataremos los siguientes conceptos:

- **Visual Studio para Mac** : Introducción a la Visual Studio para Mac y cómo crear aplicaciones de Xamarin. tvOS con ella.
- **Anatomía de una aplicación Xamarin. tvOS** : de qué consta una aplicación Xamarin. tvOS.
- **Creación de una interfaz de usuario** : Cómo usar para Xamarin Designer para iOS para crear una interfaz de usuario.
- **Implementación y pruebas** : Cómo ejecutar y probar la aplicación en el simulador de tvOS y en el hardware de tvOS real.

## <a name="starting-a-new-xamarintvos-app-in-visual-studio-for-mac"></a>Inicio de una nueva aplicación Xamarin. tvOS en Visual Studio para Mac

Como se indicó anteriormente, crearemos una aplicación Apple TV denominada `Hello-tvOS` que agrega un solo botón y una etiqueta a la pantalla principal. Cuando se hace clic en el botón, la etiqueta mostrará el número de veces que se ha hecho clic en él.

Para empezar, vamos a hacer lo siguiente:

1. Inicie Visual Studio para Mac:

    [![](hello-tvos-images/setup01.png "Visual Studio para Mac")](hello-tvos-images/setup01.png#lightbox)
2. Haga clic en el vínculo **nueva solución...** en la esquina superior izquierda de la pantalla para abrir el cuadro de diálogo **nuevo proyecto** .
3. Seleccione**aplicación de** > **vista única** de **tvOS** > y haga clic en el botón **siguiente** :

    [![](hello-tvos-images/setup02.png "Seleccionar una aplicación de vista única")](hello-tvos-images/setup02.png#lightbox)
4. Escriba `Hello, tvOS` como nombre de la **aplicación**, escriba el identificador de la **organización** y haga clic en el botón **siguiente** :

    [![](hello-tvos-images/setup04.png "Escriba Hola, tvOS")](hello-tvos-images/setup04.png#lightbox)
5. Escriba `Hello_tvOS` como **nombre del proyecto** y haga clic en el botón **crear** :

    [![](hello-tvos-images/setup03.png "Escriba HellotvOS")](hello-tvos-images/setup03.png#lightbox)

Visual Studio para Mac creará la nueva aplicación Xamarin. tvOS y mostrará los archivos predeterminados que se agregan a la solución de la aplicación:

 [![](hello-tvos-images/project01.png "Vista de archivos predeterminados")](hello-tvos-images/project01.png#lightbox)

Visual Studio para Mac usa **soluciones** y **proyectos**de la misma forma que Visual Studio. Una solución es un contenedor que puede contener uno o varios proyectos; los proyectos pueden incluir aplicaciones, bibliotecas auxiliares, aplicaciones de prueba, etc. En este caso, Visual Studio para Mac ha creado una solución y un proyecto de aplicación para usted.

Si lo desea, puede crear uno o varios proyectos de biblioteca de código que contengan código común compartido. El proyecto de aplicación puede consumir estos proyectos de biblioteca o compartirlos con otros proyectos de aplicaciones de Xamarin. tvOS (o Xamarin. iOS, Xamarin. Android y Xamarin. Mac según el tipo de código), tal como lo haría si estuviera creando una aplicación .NET estándar.

## <a name="anatomy-of-a-xamarintvos-app"></a>Anatomía de una aplicación Xamarin. tvOS

Si está familiarizado con la programación de iOS, observará muchas similitudes aquí. De hecho, tvOS 9 es un subconjunto de iOS 9, por lo que muchos conceptos se cruzarán aquí.

Echemos un vistazo a los archivos del proyecto:

- `Main.cs`: contiene el punto de entrada principal de la aplicación. Cuando se inicia la aplicación, contiene la primera clase y método que se ejecutan.
- `AppDelegate.cs`: Este archivo contiene la clase de aplicación principal responsable de escuchar eventos del sistema operativo.
- `Info.plist`: Este archivo contiene propiedades de la aplicación, como el nombre de la aplicación, los iconos, etc.
- `ViewController.cs`: Esta es la clase que representa la ventana principal y controla el ciclo de vida de la misma.
- `ViewController.designer.cs`: Este archivo contiene código de fontanería que le ayuda a integrarse con la interfaz de usuario de la pantalla principal.
- `Main.storyboard`: La interfaz de usuario de la ventana principal. El Xamarin Designer para iOS puede crear y mantener este archivo.

En las secciones siguientes, echaremos un vistazo rápido a algunos de estos archivos. Los exploraremos con más detalle más adelante, pero es una buena idea comprender sus aspectos básicos ahora.

### <a name="maincs"></a>Main.cs

El `Main.cs` archivo contiene un método `Main` estático que crea una nueva instancia de la aplicación Xamarin. tvOS y pasa el nombre de la clase que controlará los eventos del sistema operativo, que `AppDelegate` en nuestro caso es la clase:

```csharp
using UIKit;

namespace Hello_tvOS
{
    public class Application
    {
        // This is the main entry point of the application.
        static void Main (string[] args)
        {
            // if you want to use a different Application Delegate class from "AppDelegate"
            // you can specify it here.
            UIApplication.Main (args, null, "AppDelegate");
        }
    }
}
```

### <a name="appdelegatecs"></a>AppDelegate.cs

El `AppDelegate.cs` archivo contiene nuestra `AppDelegate` clase, que es responsable de crear la ventana y escuchar los eventos del sistema operativo:

```csharp
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    // The UIApplicationDelegate for the application. This class is responsible for launching the
    // User Interface of the application, as well as listening (and optionally responding) to application events from iOS.
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        // class-level declarations

        public override UIWindow Window {
            get;
            set;
        }

        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Override point for customization after application launch.
            // If not required for your application you can safely delete this method

            return true;
        }

        public override void OnResignActivation (UIApplication application)
        {
            // Invoked when the application is about to move from active to inactive state.
            // This can occur for certain types of temporary interruptions (such as an incoming phone call or SMS message)
            // or when the user quits the application and it begins the transition to the background state.
            // Games should use this method to pause the game.
        }

        public override void DidEnterBackground (UIApplication application)
        {
            // Use this method to release shared resources, save user data, invalidate timers and store the application state.
            // If your application supports background execution this method is called instead of WillTerminate when the user quits.
        }

        public override void WillEnterForeground (UIApplication application)
        {
            // Called as part of the transition from background to active state.
            // Here you can undo many of the changes made on entering the background.
        }

        public override void OnActivated (UIApplication application)
        {
            // Restart any tasks that were paused (or not yet started) while the application was inactive.
            // If the application was previously in the background, optionally refresh the user interface.
        }

        public override void WillTerminate (UIApplication application)
        {
            // Called when the application is about to terminate. Save data, if needed. See also DidEnterBackground.
        }
    }
}
```

Probablemente este código no resulte familiar a menos que haya creado una aplicación de iOS antes, pero es bastante sencillo. Vamos a examinar las líneas importantes.

En primer lugar, echemos un vistazo a la declaración de variables de nivel de clase:

```csharp
public override UIWindow Window {
            get;
            set;
        }

```

La `Window` propiedad proporciona acceso a la ventana principal. tvOS usa lo que se conoce como el patrón de *controlador de vista de modelo* (MVC). Por lo general, para cada ventana creada (y para muchas otras cosas de Windows), hay un controlador, que es responsable del ciclo de vida de la ventana, como mostrarla, agregar nuevas vistas (controles) a ella, etc.

A continuación, tenemos el `FinishedLaunching` método. Este método se ejecuta después de haber creado una instancia de la aplicación y es responsable de crear realmente la ventana de la aplicación y de comenzar el proceso de mostrar la vista en ella. Dado que nuestra aplicación usa un guion gráfico para definir su interfaz de usuario, aquí no se requiere ningún código adicional.

Hay muchos otros métodos que se proporcionan en la plantilla, `DidEnterBackground` como y. `WillEnterForeground` Se pueden quitar de forma segura si los eventos de la aplicación no se usan en la aplicación.

### <a name="viewcontrollercs"></a>ViewController.cs

La `ViewController` clase es el controlador de la ventana principal. Esto significa que es responsable del ciclo de vida de la ventana principal. Vamos a examinar esto en detalle más adelante. ahora vamos a echar un vistazo rápido:

```csharp
using System;
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    public partial class ViewController : UIViewController
    {
        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
    }
}
```

#### <a name="viewcontrollerdesignercs"></a>ViewController.Designer.cs

El archivo de diseñador de la clase de ventana principal está vacío ahora mismo, pero Visual Studio para Mac se rellenará automáticamente a medida que se crea la interfaz de usuario con el diseñador de iOS:

```csharp
using Foundation;

namespace HellotvOS
{
    [Register ("ViewController")]
    partial class ViewController
    {
        void ReleaseDesignerOutlets ()
        {
        }
    }
}
```

Normalmente no se preocupa por los archivos de diseñador, ya que se administran de forma automática por Visual Studio para Mac y simplemente proporcionan el código de fontanería necesario que permite el acceso a los controles que se agregan a cualquier ventana o vista de la aplicación.

Ahora que hemos creado nuestra aplicación Xamarin. tvOS y tenemos un conocimiento básico de sus componentes, echemos un vistazo a la creación de la interfaz de usuario.

<a name="Creating-the-User-Interface" />

## <a name="creating-the-user-interface"></a>Crear la interfaz de usuario

No tiene que usar Xamarin Designer para iOS para crear la interfaz de usuario para la aplicación Xamarin. tvOS, la interfaz de usuario se puede crear C# directamente desde el código, pero esto está fuera del ámbito de este artículo. Por simplicidad, usaremos iOS Designer para crear la interfaz de usuario en el resto de este tutorial.

Para empezar a crear la interfaz de usuario, vamos a hacer `Main.storyboard` doble clic en el archivo en el **Explorador de soluciones** para abrirlo para su edición en el diseñador de iOS:

[![](hello-tvos-images/designer01.png "Archivo Main.storyboard en el Explorador de soluciones")](hello-tvos-images/designer01.png#lightbox)

Esto debería iniciar el diseñador y tener un aspecto similar al siguiente:

[![](hello-tvos-images/designer02.png "Diseñador")](hello-tvos-images/designer02.png#lightbox)

Para obtener más información sobre el diseñador de iOS y su funcionamiento, consulte la [Introducción a la](~/ios/user-interface/designer/introduction.md) guía de Xamarin Designer para iOS.

Ahora podemos empezar a agregar controles a la superficie de diseño de nuestra aplicación Xamarin. tvOS.

Haga lo siguiente:

1. Busque el **cuadro de herramientas**, que debe estar a la derecha de la superficie de diseño:

    [![](hello-tvos-images/designer03.png "Cuadro de herramientas")](hello-tvos-images/designer03.png#lightbox)

    Si no puede encontrarlo aquí, vaya a **ver > paneles > cuadro de herramientas** para verlo.
2. Arrastre una **etiqueta** desde el **cuadro de herramientas** a la superficie de diseño:

    [![](hello-tvos-images/designer04.png "Arrastrar una etiqueta desde el cuadro de herramientas")](hello-tvos-images/designer04.png#lightbox)
3. Haga clic en la propiedad **title** en el **Panel de propiedades** y cambie el título del `Hello, tvOS` botón a y establezca el **tamaño de fuente** en 128:

    [![](hello-tvos-images/designer05.png "Establezca el título en Hello, tvOS y establezca el tamaño de fuente en 128")](hello-tvos-images/designer05.png#lightbox)
4. Cambie el tamaño de la etiqueta para que todas las palabras estén visibles y colóquelas cerca de la parte superior de la ventana:

    [![](hello-tvos-images/designer06.png "Cambiar el tamaño y centrar la etiqueta")](hello-tvos-images/designer06.png#lightbox)
5. Ahora la etiqueta tendrá que estar restringida a su posición, de modo que aparezca como se pretende. independientemente del tamaño de la pantalla. Para ello, haga clic en la etiqueta hasta que aparezca el *identificador con forma de T* :

    [![](hello-tvos-images/designer07.png "El identificador en forma de T")](hello-tvos-images/designer07.png#lightbox)
6. Para restringir la etiqueta horizontalmente, seleccione el cuadrado central y arrástrelo hasta la línea discontinua vertical:

    [![](hello-tvos-images/designer08.png "Seleccionar el cuadrado central")](hello-tvos-images/designer08zoom.png#lightbox)

     La etiqueta debe ser naranja.
7. Seleccione el identificador T en la parte superior de la etiqueta y arrástrelo hasta el borde superior de la ventana:

    [![](hello-tvos-images/designer09.png "Arrastrar el controlador hasta el borde superior de la ventana")](hello-tvos-images/designer09.png#lightbox)
8. Después, haga clic en el ancho y, a continuación, en el *controlador del hueso* de alto, como se muestra a continuación:

    [![](hello-tvos-images/designer10.png "Los manipuladores de ancho y alto del hueso")](hello-tvos-images/designer10.png#lightbox)

     Cuando se haga clic en cada *controlador de hueso* , seleccione ancho y alto respectivamente para establecer las dimensiones fijas.
9. Cuando haya finalizado, las restricciones deben ser similares a las de la pestaña diseño del panel de propiedades:

    [![](hello-tvos-images/designer11.png "Restricciones de ejemplo")](hello-tvos-images/designer11.png#lightbox)
10. Arrastre un **botón** desde el **cuadro de herramientas** y colóquelo debajo de la etiqueta.
11. Haga clic en la propiedad **title** en el **Panel de propiedades** y cambie el título del `Click Me`botón a:

    [![](hello-tvos-images/designer12.png "Cambiar el título de los botones a click me")](hello-tvos-images/designer12.png#lightbox)
12. Repita los pasos 5 a 8 anteriores para restringir el botón en la ventana de tvOS. Sin embargo, en lugar de arrastrar el controlador T hasta la parte superior de la ventana (como en el paso #7), arrástrelo hasta la parte inferior de la etiqueta:

    [![](hello-tvos-images/designer14.png "Restringir el botón")](hello-tvos-images/designer14.png#lightbox)
13. Arrastre otra etiqueta debajo del botón, asígnele el mismo ancho que la primera etiqueta y establezca su **alineación** en **Center**:

    [![](hello-tvos-images/designer15.png "Arrastre otra etiqueta debajo del botón, cambie su tamaño para que tenga el mismo ancho que la primera etiqueta y establezca su alineación en Center.")](hello-tvos-images/designer15.png#lightbox)
14. Al igual que la primera etiqueta y botón, establezca esta etiqueta en Center y ancle en ubicación y tamaño:

    [![](hello-tvos-images/designer16.png "Anclar la etiqueta en la ubicación y el tamaño")](hello-tvos-images/designer16.png#lightbox)
15. Guarde los cambios en la interfaz de usuario.

A medida que va a cambiar el tamaño y mover los controles, debe haber observado que el diseñador proporciona sugerencias de ajuste que se basan en las directrices de la [interfaz de usuario de Apple TV](https://developer.apple.com/tvos/human-interface-guidelines/). Estas instrucciones le ayudarán a crear aplicaciones de alta calidad que tendrán una apariencia familiar para los usuarios de Apple TV.

Si observa la sección **esquema del documento** , observe cómo se muestra el diseño y la jerarquía de los elementos que componen la interfaz de usuario:

[![](hello-tvos-images/designer17.png "La sección esquema del documento")](hello-tvos-images/designer17.png#lightbox)

Aquí puede seleccionar los elementos que desea editar o arrastrar para reordenar los elementos de la interfaz de usuario si es necesario. Por ejemplo, si un elemento de la interfaz de usuario estaba incluido en otro elemento, puede arrastrarlo a la parte inferior de la lista para convertirlo en el elemento de nivel superior de la ventana.

Ahora que hemos creado nuestra interfaz de usuario, es necesario exponer los elementos de la interfaz de usuario para que Xamarin. tvOS pueda tener acceso e C# interactuar con ellos en el código.

### <a name="accessing-the-controls-in-the-code-behind"></a>Obtener acceso a los controles del código subyacente

Hay dos formas principales de acceder a los controles que ha agregado en el diseñador de iOS desde el código:

- Crear un controlador de eventos en un control.
- Asignar un nombre al control, de modo que podamos hacer referencia a él más adelante.

Cuando se agrega cualquiera de estos, la clase parcial dentro `ViewController.designer.cs` de se actualizará para reflejar los cambios. Esto le permitirá acceder a los controles en el controlador de vista.

### <a name="creating-an-event-handler"></a>Crear un controlador de eventos

En esta aplicación de ejemplo, cuando se hace clic en el botón, queremos que suceda _algo_ , por lo que es necesario agregar un controlador de eventos a un evento específico en el botón. Para configurarlo, haga lo siguiente:

1. En Xamarin iOS Designer, seleccione el botón en el controlador de vista.
2. En el panel de propiedades, seleccione la pestaña **eventos** :

    [![](hello-tvos-images/event1.png "Pestaña eventos")](hello-tvos-images/event1.png#lightbox)
3. Busque el evento TouchUpInside y asígnele un controlador de eventos denominado `Clicked`:

    [![](hello-tvos-images/event2.png "El evento TouchUpInside")](hello-tvos-images/event2.png#lightbox)
4. Al presionar **entrar**, se abrirá el archivo **ViewController**. CS y se sugerirán las ubicaciones del controlador de eventos en el código. Use las teclas de flecha del teclado para establecer la ubicación:

    [![](hello-tvos-images/event3.png "Establecimiento de la ubicación")](hello-tvos-images/event3.png#lightbox)
5. Se creará un método parcial, tal como se muestra a continuación:

    [![](hello-tvos-images/event4.png "El método parcial")](hello-tvos-images/event4.png#lightbox)

Ahora estamos listos para empezar a agregar código para que el botón funcione.

### <a name="naming-a-control"></a>Asignar un nombre a un control

Al hacer clic en el botón, la etiqueta debe actualizarse en función del número de clics. Para ello, necesitaremos tener acceso a la etiqueta en el código. Esto se realiza asignándole un nombre. Haga lo siguiente:

1. Abra el guion gráfico y seleccione la etiqueta en la parte inferior del controlador de vista.
2. En el panel de propiedades, seleccione la pestaña **Widget** :

    [![](hello-tvos-images/name1.png "Selección de la pestaña widget")](hello-tvos-images/name1.png#lightbox)
3. En **identidad > nombre**, agregue `ClickedLabel`:

    [![](hello-tvos-images/name2.png "Establecer ClickedLabel")](hello-tvos-images/name2.png#lightbox)

Ahora estamos listos para empezar a actualizar la etiqueta.

### <a name="how-controls-are-accessed"></a>Cómo se obtiene acceso a los controles

Si selecciona `ViewController.designer.cs` en el **Explorador de soluciones** podrá ver cómo se han asignado la `ClickedLabel` etiqueta y el `Clicked` controlador de eventos a una **salida** y una **acción** en C#:

[![](hello-tvos-images/accesscontrol.png "Salidas y acciones")](hello-tvos-images/accesscontrol.png#lightbox)

También puede observar que `ViewController.designer.cs` es una clase parcial, por lo que Visual Studio para Mac no tiene que modificar `ViewController.cs` , lo que sobrescribiría cualquier cambio que se haya realizado en la clase.

La exposición de los elementos de la interfaz de usuario de esta manera le permite tener acceso a ellos en el controlador de vista.

Normalmente, nunca tendrá que abrir el `ViewController.designer.cs` propio, ya que se presentó aquí solo con fines educativos.

<a name="Writing-the-Code" />

## <a name="writing-the-code"></a>Escritura del código

Con la interfaz de usuario creada y sus elementos de interfaz de usuario expuestos al código mediante **salidas** y **acciones**, ya estamos preparados para escribir el código para proporcionar la funcionalidad del programa.

En nuestra aplicación, cada vez que se hace clic en el primer botón, vamos a actualizar la etiqueta para mostrar el número de veces que se hizo clic en el botón. Para ello, es necesario abrir el `ViewController.cs` archivo para editarlo; para ello, haga doble clic en el **Panel de solución**:

[![](hello-tvos-images/code01.png "El panel de solución")](hello-tvos-images/code01.png#lightbox)

En primer lugar, es necesario crear una variable de nivel de clase `ViewController` en nuestra clase para realizar el seguimiento del número de clics que se han producido. Edite la definición de clase y dele un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    public partial class ViewController : UIViewController
    {
        private int numberOfTimesClicked = 0;
        ...
```

A continuación, en la misma clase`ViewController`(), es necesario invalidar el método **ViewDidLoad** y agregar código para establecer el mensaje inicial de la etiqueta:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Set the initial value for the label
    ClickedLabel.Text = "Button has not been clicked yet.";
}
```

Necesitamos `ViewDidLoad`usar, en lugar de otro método `Initialize`como, porque `ViewDidLoad` se llama a *después* de que el sistema operativo se haya cargado y se haya creado una instancia `.storyboard` de la interfaz de usuario desde el archivo. Si intentamos tener acceso al control de etiqueta antes `.storyboard` de que el archivo se haya cargado completamente y se haya creado una `NullReferenceException` instancia, se producirá un error porque el control de etiqueta no se crearía todavía.

A continuación, es necesario agregar el código para responder al usuario haciendo clic en el botón. Agregue lo siguiente a la clase parcial a que hemos creado:

```csharp
partial void Clicked (UIButton sender)
{
    ClickedLabel.Text = string.Format("The button has been clicked {0} time{1}.", ++numberOfTimesClicked, (numberOfTimesClicked
```

Se llamará a este código cada vez que el usuario haga clic en nuestro botón.

Con todo en su lugar, ahora estamos preparados para compilar y probar nuestra aplicación Xamarin. tvOS.

## <a name="testing-the-application"></a>Probar la aplicación

Es el momento de compilar y ejecutar la aplicación para asegurarse de que se ejecuta según lo previsto. Podemos compilar y ejecutar todo en un solo paso, o podemos compilarlo sin ejecutarlo.

Siempre que se crea una aplicación, podemos elegir el tipo de compilación que queremos:

- **Debug** : una compilación de depuración se compila en un archivo "" (aplicación) con metadatos adicionales que nos permiten depurar lo que sucede mientras la aplicación se está ejecutando.
- **Versión** : una compilación de versión también crea un archivo ' ', pero no incluye información de depuración, por lo que es más pequeño y se ejecuta más rápido.  

Puede seleccionar el tipo de compilación en el **selector de configuración** en la esquina superior izquierda de la pantalla Visual Studio para Mac:

[![](hello-tvos-images/run01.png "Seleccionar el tipo de compilación")](hello-tvos-images/run01.png#lightbox)

### <a name="building-the-application"></a>Compilar la aplicación

En nuestro caso, solo queremos una compilación de depuración, así que vamos a asegurarnos de que está seleccionado **depurar** . Vamos a compilar la aplicación en primer lugar presionando **⌘ B**, o bien, en el menú **compilar** , elija **compilar todo**.

Si no se ha producido ningún error, verá un mensaje **compilación correcta** en la barra de estado de Visual Studio para Mac. Si se produjeron errores, revise el proyecto y asegúrese de que ha seguido los pasos correctamente. Empiece por confirmar que el código (tanto en Xcode como en Visual Studio para Mac) coincide con el código del tutorial.

### <a name="running-the-application"></a>Ejecutar la aplicación

Para ejecutar la aplicación, tenemos tres opciones:

- Presione **⌘+Entrar**.
- En el menú **Ejecutar**, elija **Depurar**.
- Haga clic en el botón **Reproducir** de la barra de herramientas de Visual Studio para Mac (justo encima del **Explorador de soluciones**).

La aplicación se compilará (si aún no se ha compilado), se iniciará en modo de depuración, el simulador tvOS se iniciará y la aplicación se iniciará y mostrará la ventana de la interfaz principal:

[![La pantalla principal de la aplicación de ejemplo](hello-tvos-images/run03.png)](hello-tvos-images/run03.png#lightbox)

En el menú **hardware** , seleccione **Mostrar Apple TV Remote** para que pueda controlar el simulador.

[![](hello-tvos-images/run04.png "Seleccione Mostrar Apple TV remoto")](hello-tvos-images/run04.png#lightbox)

Mediante el uso remoto del simulador, si hace clic en el botón algunas veces, la etiqueta debe actualizarse con el recuento:

[![](hello-tvos-images/run05.png "Etiqueta con el recuento actualizado")](hello-tvos-images/run05.png#lightbox)

¡Enhorabuena! Aquí nos centramos mucho en el terreno, pero si siguió este tutorial de principio a fin, ahora debe tener una comprensión sólida de los componentes de una aplicación Xamarin. tvOS, así como las herramientas que se usan para crearlos.

## <a name="where-to-next"></a>¿Dónde se debe a continuación?

El desarrollo de aplicaciones de Apple TV presenta algunos desafíos debido a la desconexión entre el usuario y la interfaz (se encuentra en la habitación no en la mano del usuario) y las limitaciones tvOSn en el tamaño y el almacenamiento de la aplicación.

Como resultado, se recomienda que lea los siguientes documentos antes de pasar al diseño de una aplicación de Xamarin. tvOS:

- [Introducción a tvOS 9](~/ios/tvos/platform/tvos9.md) : en este artículo se presentan todas las API y características nuevas y modificadas disponibles en tvOS 9 para los desarrolladores de Xamarin. tvOS.
- [Trabajar con la navegación y el foco](~/ios/tvos/app-fundamentals/navigation-focus.md) : los usuarios de la aplicación de Xamarin. tvOS no interactuarán con su interfaz directamente como con iOS, donde tocan imágenes en la pantalla del dispositivo, pero indirectamente desde el salón mediante el uso remoto de Siri. En este artículo se describe el concepto de enfoque y cómo se usa para controlar la navegación en una interfaz de usuario de la aplicación Xamarin. tvOS.
- [Siri controladores remotos y Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) : la forma principal en que los usuarios interactuarán con Apple TV y la aplicación Xamarin. tvOS es a través del siri remoto incluido. Si la aplicación es un juego, opcionalmente puede compilar en soporte técnico para terceros, creados para dispositivos de juegos Bluetooth (accesorios MFI) de iOS también en la aplicación. En este artículo se describe la compatibilidad con los nuevos dispositivos de juego de Siri remoto y Bluetooth en las aplicaciones de Xamarin. tvOS.
- [Recursos y almacenamiento de datos](~/ios/tvos/app-fundamentals/resources-data-storage.md) : a diferencia de los dispositivos iOS, el nuevo Apple TV no proporciona almacenamiento local y permanente para aplicaciones tvOS. Como resultado, si la aplicación de Xamarin. tvOS necesita conservar información (como las preferencias del usuario), debe almacenar y recuperar los datos de iCloud. En este artículo se explica cómo trabajar con recursos y el almacenamiento de datos persistentes en una aplicación Xamarin. tvOS.
- [Trabajar con iconos e imágenes](~/ios/tvos/app-fundamentals/icons-images.md) : crear iconos e imágenes atractivas es una parte fundamental del desarrollo de una experiencia de usuario envolvente para las aplicaciones de Apple TV. En esta guía se explican los pasos necesarios para crear e incluir los recursos gráficos necesarios para las aplicaciones de Xamarin. tvOS.
- [Interfaz de usuario](~/ios/tvos/user-interface/index.md) : cobertura de experiencia de usuario general (UX), incluidos los controles de interfaz de usuario (IU), para usar los principios de diseño de la experiencia del usuario y el Interface Builder de Xcode al trabajar con Xamarin. tvOS.
- [Implementación y pruebas](~/ios/tvos/deploy-test/index.md) : en esta sección se tratan los temas que se usan para probar una aplicación, así como la forma de distribuirla. Los temas aquí incluyen aspectos como las herramientas que se usan para la depuración, la implementación en evaluadores y la publicación de una aplicación en la App Store de Apple TV.

Si tiene problemas para trabajar con Xamarin. tvOS, consulte nuestra documentación de [solución de problemas](~/ios/tvos/troubleshooting.md) para obtener una lista de problemas conocidos y soluciones.

## <a name="summary"></a>Resumen

En este artículo se ha proporcionado un tutorial introductorio para desarrollar aplicaciones para tvOS con Visual Studio para Mac mediante la creación de una sencilla aplicación Hello, tvOS. Se han tratado los aspectos básicos del aprovisionamiento de dispositivos tvOS, la creación de interfaces, la codificación de tvOS y las pruebas en el simulador tvOS.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de la interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [Compilación de aplicaciones para tvOS con Xamarin (vídeo)](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
