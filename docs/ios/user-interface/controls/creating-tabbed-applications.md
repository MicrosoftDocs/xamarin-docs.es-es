---
title: Barras de pestañas y controladores de barra de pestañas en Xamarin. iOS
description: En este documento se describen los controladores de barra de pestañas de iOS y cómo usarlos con Xamarin. iOS. Muestra cómo configurar un UITabBarController, trabajar con imágenes, establecer valores de distintivo, trabajar con eventos y mucho más.
ms.prod: xamarin
ms.assetid: 7C772899-2900-F139-D642-F3C4F3F14DDC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 9f8a5e568946e1aea8541211ec3adc45a25f1897
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022133"
---
# <a name="tab-bars-and-tab-bar-controllers-in-xamarinios"></a>Barras de pestañas y controladores de barra de pestañas en Xamarin. iOS

Las aplicaciones con pestañas se usan en iOS para admitir interfaces de usuario en las que se puede tener acceso a varias pantallas sin ningún orden determinado. A través de la clase `UITabBarController`, las aplicaciones pueden incluir fácilmente compatibilidad con estos escenarios de varias pantallas. `UITabBarController` se encarga de la administración de varias pantallas, lo que permite al desarrollador de la aplicación centrarse en los detalles de cada pantalla.

Normalmente, las aplicaciones con pestañas se compilan con la `UITabBarController` es el `RootViewController` de la ventana principal. Sin embargo, con un poco de código adicional, las aplicaciones con pestañas también se pueden usar de forma sucesiva en otra pantalla inicial, como el escenario en el que una aplicación presenta primero una pantalla de inicio de sesión, seguida de la interfaz con pestañas.

Examinaremos el uso de pestañas aquí al trabajar a través de un tutorial de una aplicación sencilla. A continuación, veremos cómo trabajar con pestañas en el escenario que no es de `RootViewController`.

## <a name="introducing-uitabbarcontroller"></a>Introducción a UITabBarController

El `UITabBarController` admite el desarrollo de aplicaciones con pestañas de la siguiente manera:

- Permite agregar varios controladores a él.
- Proporcionar una interfaz de usuario con pestañas, a través de la clase `UITabBar`, para permitir que un usuario cambie entre controladores y sus vistas. 

Los controladores se agregan a la `UITabBarController` a través de su propiedad `ViewControllers`, que es una matriz de `UIViewController`. El propio `UITabBarController` controla la carga del controlador adecuado y la presentación de la vista en función de la pestaña seleccionada.

Las pestañas son instancias de la clase `UITabBarItem`, que están contenidas en una instancia de `UITabBar`. Cada instancia de `UITabBar` es accesible a través de la propiedad `TabBarItem` del controlador en cada pestaña.

Para obtener una descripción de cómo trabajar con el `UITabBarController`, veamos cómo crear una aplicación sencilla que use una.

## <a name="tabbed-application-walkthrough"></a>Tutorial de la aplicación con pestañas

En este tutorial vamos a crear la siguiente aplicación:

[![](creating-tabbed-applications-images/00-app.png "Sample tabbed app")](creating-tabbed-applications-images/00-app.png#lightbox)

Aunque ya existe una plantilla de aplicación con pestañas disponible en Visual Studio para Mac, en este ejemplo, vamos a trabajar desde un proyecto vacío para obtener una mejor comprensión de cómo se construye la aplicación.

 <a name="Creating_the_Application" />

### <a name="creating-the-application"></a>Crear la aplicación

Comencemos por crear una nueva aplicación.

Seleccione el elemento de menú **archivo > nueva > solución** en Visual Studio para Mac y seleccione una **aplicación de > iOS > plantilla de proyecto vacía** , asigne al proyecto el nombre `TabbedApplication`, como se muestra a continuación:

[![](creating-tabbed-applications-images/newsolution1.png "Select the Empty Project template")](creating-tabbed-applications-images/newsolution1.png#lightbox)

[![](creating-tabbed-applications-images/newsolution2.png "Name the project TabbedApplication")](creating-tabbed-applications-images/newsolution2.png#lightbox)

### <a name="adding-the-uitabbarcontroller"></a>Agregar UITabBarController

A continuación, agregue una clase vacía seleccionando **archivo > nuevo archivo** y eligiendo la plantilla de **clase general: Empty** . Asigne un nombre al archivo `TabController` como se muestra a continuación:

[![](creating-tabbed-applications-images/02-newclass.png "Add the TabController class")](creating-tabbed-applications-images/02-newclass.png#lightbox)

La clase `TabController` contendrá la implementación de la `UITabBarController` que administrará una matriz de `UIViewControllers`. Cuando el usuario selecciona una pestaña, el `UITabBarController` se encargará de presentar la vista del controlador de vistas adecuado.

Para implementar el `UITabBarController` es necesario hacer lo siguiente:

1. Establezca la clase base de `TabController` en `UITabBarController`. 
1. Cree instancias de `UIViewController` para agregarlas al `TabController`. 
1. Agregue las instancias de `UIViewController` a una matriz asignada a la propiedad `ViewControllers` de la `TabController`. 

Agregue el código siguiente a la clase `TabController` para lograr estos pasos:

```csharp
using System;
using UIKit;

namespace TabbedApplication {
    public class TabController : UITabBarController {

        UIViewController tab1, tab2, tab3;

        public TabController ()
        {
            tab1 = new UIViewController();
            tab1.Title = "Green";
            tab1.View.BackgroundColor = UIColor.Green;

            tab2 = new UIViewController();
            tab2.Title = "Orange";
            tab2.View.BackgroundColor = UIColor.Orange;

            tab3 = new UIViewController();
            tab3.Title = "Red";
            tab3.View.BackgroundColor = UIColor.Red;

            var tabs = new UIViewController[] {
                tab1, tab2, tab3
            };

            ViewControllers = tabs;
        }
    }
}
```

Observe que para cada instancia de `UIViewController`, se establece la propiedad `Title` de la `UIViewController`. Cuando los controladores se agregan al `UITabBarController`, el `UITabBarController` leerá el `Title` de cada controlador y lo mostrará en la etiqueta de la pestaña asociada, como se muestra a continuación:

[![](creating-tabbed-applications-images/00-app.png "The sample app run")](creating-tabbed-applications-images/00-app.png#lightbox)

#### <a name="setting-the-tabcontroller-as-the-rootviewcontroller"></a>Establecer TabController como RootViewController

El orden en que se colocan los controladores en las pestañas corresponde al orden en que se agregan a la matriz de `ViewControllers`.

Para obtener la `UITabController` que se va a cargar como la primera pantalla, es necesario convertirla en la `RootViewController`de la ventana, como se muestra en el código siguiente para el `AppDelegate`:

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate : UIApplicationDelegate
{
    UIWindow window;
    TabController tabController;
    
    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        window = new UIWindow (UIScreen.MainScreen.Bounds);
        
        tabController = new TabController ();
        window.RootViewController = tabController;
        
        window.MakeKeyAndVisible ();
        
        return true;
    }
}
```

Si ejecutamos la aplicación ahora, el `UITabBarController` se cargará con la primera pestaña seleccionada de forma predeterminada. Al seleccionar cualquiera de las otras pestañas, se muestra la vista del controlador asociado en el `UITabBarController,`, como se muestra a continuación, donde el usuario final ha seleccionado la segunda pestaña:

[![](creating-tabbed-applications-images/03-secondtab.png "The second tab shown")](creating-tabbed-applications-images/03-secondtab.png#lightbox)

 <a name="Modifying_TabBarItems" />

### <a name="modifying-tabbaritems"></a>Modificar TabBarItems

Ahora que tenemos una aplicación de pestañas en ejecución, vamos a modificar el `TabBarItem` para cambiar la imagen y el texto que se muestran, así como para agregar un distintivo a una de las pestañas.

 <a name="Setting_a_System_Item" />

#### <a name="setting-a-system-item"></a>Establecer un elemento del sistema

En primer lugar, vamos a establecer la primera pestaña para usar un elemento del sistema. En el constructor del `TabController`, quite la línea que establece el `Title` del controlador para la instancia de `tab1` y reemplácelo por el código siguiente para establecer la propiedad `TabBarItem` del controlador:

```csharp
tab1.TabBarItem = new UITabBarItem (UITabBarSystemItem.Favorites, 0);
```

Al crear el `UITabBarItem` mediante un `UITabBarSystemItem`, iOS proporciona automáticamente el título y la imagen, tal como se muestra en la siguiente captura de pantalla que muestra el icono de **Favoritos** y el título en la primera pestaña:

 ![](creating-tabbed-applications-images/04a-tabimage.png "The first tab with a star icon")

 <a name="Setting_the_Title_and_Image" />

#### <a name="setting-the-title-and-image"></a>Establecer el título y la imagen

Además de usar un elemento del sistema, el título y la imagen de un `UITabBarItem` pueden establecerse en valores personalizados. Por ejemplo, cambie el código que establece la propiedad `TabBarItem` del controlador denominado `tab2` de la siguiente manera:

```csharp
tab2 = new UIViewController ();
tab2.TabBarItem = new UITabBarItem ();
tab2.TabBarItem.Image = UIImage.FromFile ("second.png");
tab2.TabBarItem.Title = "Second";
tab2.View.BackgroundColor = UIColor.Orange;
```

En el código anterior se supone que se ha agregado una imagen denominada `second.png` a la raíz del proyecto en Visual Studio para Mac. En realidad, hemos agregado tres imágenes a nuestro proyecto para cubrir todas las resoluciones de los dispositivos, como se muestra a continuación:

 [![](creating-tabbed-applications-images/tabbedimages7new.png "The images added to the project")](creating-tabbed-applications-images/tabbedimages7new.png#lightbox)

La imagen de pestaña debe ser un PNG 30 x 30 con transparencia para la resolución normal, 60 x 60 para alta resolución y 90 x 90 para la resolución de iPhone 6 Plus. En nuestro código, solo es necesario cargar el archivo denominado `second.png` e iOS cargará automáticamente la alta resolución en los dispositivos con una pantalla retina. Puede leer más sobre esto en las guías [trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md) . De forma predeterminada, los elementos de la barra de pestañas están atenuados y tienen un matiz azul cuando se seleccionan.

**Nota:**

También se pueden agregar las imágenes anteriores al directorio de **recursos** , que es un directorio especial cuyo contenido se copiará automáticamente en la raíz del paquete de aplicaciones:

[![](creating-tabbed-applications-images/tabbedapplication8.png "The images as Resources")](creating-tabbed-applications-images/tabbedapplication8.png#lightbox)

Además, cuando establecemos la propiedad `Title` directamente en el `TabBarItem`, invalidaría cualquier valor establecido para `Title` en el propio controlador.

Cuando se ejecuta la aplicación ahora, la segunda pestaña muestra el título y la imagen personalizados, tal y como se muestra a continuación:

[![](creating-tabbed-applications-images/05-customtab.png "The second tab with a square icon")](creating-tabbed-applications-images/05-customtab.png#lightbox)

 <a name="Setting_the_Badge_Value" />

#### <a name="setting-the-badge-value"></a>Establecer el valor del distintivo

Una pestaña también puede mostrar un distintivo. Por ejemplo, agregue la siguiente línea de código para establecer un distintivo en la tercera pestaña:

```csharp
tab3.TabBarItem.BadgeValue = "Hi";
```

Al ejecutarlo, se obtiene una etiqueta roja con la cadena "HI" en la esquina superior izquierda de la pestaña, como se muestra a continuación:

[![](creating-tabbed-applications-images/06-badge.png "The second tab with a Hi badge")](creating-tabbed-applications-images/06-badge.png#lightbox)

El distintivo se usa a menudo para mostrar una indicación de número sin leer, elementos nuevos. Para quitar el distintivo, establezca el `BadgeValue` en null, como se muestra a continuación:

```csharp
tab3.TabBarItem.BadgeValue = null;
```

 <a name="Tabs_in_Non-RootViewController_Scenarios" />

## <a name="tabs-in-non-rootviewcontroller-scenarios"></a>Pestañas en escenarios que no son de RootViewController

En el ejemplo anterior, hemos mostrado cómo trabajar con un `UITabBarController` cuando es el `RootViewController` de la ventana. En este ejemplo, examinaremos cómo usar un `UITabBarController` cuando no sea el `RootViewController` y mostraremos cómo se crea el uso de guiones gráficos.

 <a name="Initial_Screen_Example" />

### <a name="initial-screen-example"></a>Ejemplo de pantalla inicial

En este escenario, la pantalla inicial se carga desde un controlador que no es un `UITabBarController`. Cuando el usuario interactúa con la pantalla pulsando un botón, el mismo controlador de vista se cargará en una `UITabBarController`, que se presentará al usuario. En la captura de pantalla siguiente se muestra el flujo de la aplicación:

[![](creating-tabbed-applications-images/inital-screen-application.png "This screenshot shows the application flow")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)

Vamos a iniciar una nueva aplicación para este ejemplo. Una vez más, usaremos la plantilla de **iPhone > App >C#Project () vacía** , con lo que se asignará un nombre a la`InitialScreenDemo`del proyecto.

En este ejemplo, se necesitará un guion gráfico para contener nuestros controladores de vista. Para agregar un guion gráfico:

- Haga clic con el botón derecho en el nombre del proyecto y seleccione **agregar > nuevo archivo**.

- Cuando aparezca el cuadro de diálogo nuevo archivo, vaya a **iOS > guion gráfico de iPhone vacío**.

Vamos a llamar a este nuevo guion gráfico **archivo mainstoryboard** , como se muestra a continuación: 

[![](creating-tabbed-applications-images/new-file-dialog.png "Add a MainStoryboard file to the project")](creating-tabbed-applications-images/new-file-dialog.png#lightbox)

Hay algunos pasos importantes que se deben tener en cuenta al agregar un guion gráfico a un archivo que no es un guion gráfico anterior, que se explican en la guía [Introducción a los guiones gráficos](~/ios/user-interface/storyboards/index.md) . Estos son:

1. Agregue el nombre del guion gráfico a la sección **interfaz principal** del `Info.plist`:

    [![](creating-tabbed-applications-images/project-options.png "Set the Main Interface to MainStoryboard")](creating-tabbed-applications-images/project-options.png#lightbox)
1. En el `App Delegate`, invalide el método de ventana, con el código siguiente:

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

Vamos a necesitar tres controladores de vista para este ejemplo. Una, denominada `ViewController1`, se usará como controlador de vista inicial y en la primera pestaña. Los otros dos, denominados `ViewController2` y `ViewController3`, que se usarán en la segunda y tercera pestaña, respectivamente.

Abra el diseñador haciendo doble clic en el archivo archivo mainstoryboard. Storyboard y arrastre tres controladores de vista a la superficie de diseño. Queremos que cada uno de estos controladores de vista tenga su propia clase correspondiente al nombre anterior, de modo que, en **identidad > clase**, escriba su nombre, tal como se muestra en la siguiente captura de pantalla:

[![](creating-tabbed-applications-images/class-name.png "Set the Class to ViewController1")](creating-tabbed-applications-images/class-name.png#lightbox)

Visual Studio para Mac generará automáticamente las clases y los archivos de diseñador necesarios, puede verse en el Panel de solución, como se muestra a continuación:

[![](creating-tabbed-applications-images/solution-pad2.png "Auto-generated files in the project")](creating-tabbed-applications-images/solution-pad2.png#lightbox)

 <a name="Creating_the_UI" />

#### <a name="creating-the-ui"></a>Crear la interfaz de usuario

A continuación, vamos a crear una interfaz de usuario simple para cada una de las vistas de ViewController con Xamarin iOS Designer.

Queremos arrastrar un `Label` y un `Button` en ViewController1 desde el cuadro de **herramientas** en el lado derecho. A continuación, usaremos el Panel de propiedades para editar el nombre y el texto de los controles para lo siguiente:

- **Etiqueta** : `Text` = **uno**
- **Button** : `Title` = **usuario realiza algunas acciones iniciales**

Vamos a controlar la visibilidad de nuestro botón en un evento de `TouchUpInside` y necesitamos hacer referencia a él en el código subyacente. Vamos a identificarlo con el **nombre** `aButton` en el panel de propiedades, tal y como se muestra en la siguiente captura de pantalla:

[![](creating-tabbed-applications-images/abutton-properties.png "Set the Name to aButton in the Properties Pad")](creating-tabbed-applications-images/abutton-properties.png#lightbox)

El Superficie de diseño debe tener ahora un aspecto similar al de la siguiente captura de pantalla:

[![](creating-tabbed-applications-images/design-surface1.png "Your Design Surface should now look similar to this screenshot")](creating-tabbed-applications-images/design-surface1.png#lightbox)

Vamos a agregar un poco más detalle a `ViewController2` y `ViewController3`, agregando una etiqueta a cada uno y cambiando el texto a "dos" y "tres", respectivamente. Esto destaca al usuario de la pestaña o vista que estamos examinando.

#### <a name="wiring-up-the-button"></a>Conexión del botón

Vamos a cargar `ViewController1` cuando se inicie la aplicación por primera vez. Cuando el usuario pulsa el botón, se oculta el botón y se carga una `UITabBarController` con la instancia de `ViewController1` en la primera pestaña.

Cuando el usuario suelta el `aButton`, queremos que se desencadene un evento TouchUpInside. Vamos a seleccionar el botón y, en la **pestaña eventos** del panel de propiedades, declare el controlador de eventos, `InitialActionCompleted`, por lo que se puede hacer referencia a él en el código. Esto se muestra en la siguiente captura de pantalla:

[![](creating-tabbed-applications-images/event-handler.png "When the user releases the aButton, trigger a TouchUpInside event")](creating-tabbed-applications-images/event-handler.png#lightbox)

Ahora es necesario indicar al controlador de vistas que oculte el botón cuando el evento se active `InitialActionCompleted`. En `ViewController1`, agregue el siguiente método parcial:

```csharp
partial void InitialActionCompleted (UIButton sender)
{
    aButton.Hidden = true;  
}
```

Guarde el archivo y ejecute la aplicación. Deberíamos ver que aparece una pantalla y el botón desaparece al tocar.

#### <a name="adding-the-tab-bar-controller"></a>Agregar el controlador de barra de pestañas

Ahora tenemos nuestra vista inicial funcionando según lo previsto. A continuación, queremos agregarlo a un `UITabBarController`, junto con las vistas 2 y 3. Vamos a abrir el guion gráfico en el diseñador.

En el **cuadro de herramientas**, busque el **controlador de barra de pestañas** en controladores & objetos y arrástrelo hasta el superficie de diseño. Como puede ver en la siguiente captura de pantalla, el controlador de barra de pestañas es sin interfaz de usuario y, por tanto, incorpora dos controladores de vista de forma predeterminada:

[![](creating-tabbed-applications-images/tabbarcontroller.png "Adding a Tab Bar Controller to the layout")](creating-tabbed-applications-images/tabbarcontroller.png#lightbox)

Elimine estos nuevos controladores de vista seleccionando la barra negra situada en la parte inferior y presionando Supr.

En nuestro guion gráfico, podemos usar objetos segue para controlar las transiciones entre TabBarController y nuestros controladores de vista. Después de interactuar con la vista inicial, queremos cargarla en el TabBarController que se presenta al usuario. Vamos a configurar esto en el diseñador.

Pulse **Ctrl** y **arrastre** desde el botón hasta el TabBarController. Al activar el mouse, aparecerá un menú contextual. Queremos usar un segue modal. 

Para configurar cada una de nuestras pestañas, **Presione la tecla Ctrl y haga clic** en TabBarController para cada uno de los controladores de vista en orden de uno a tres y seleccione la **pestaña** relación en el menú contextual, como se muestra a continuación:

[![](creating-tabbed-applications-images/context-menu.png "Select the Tab Relationship")](creating-tabbed-applications-images/context-menu.png#lightbox)

El guión gráfico debe parecerse a la siguiente captura de pantalla:

[![](creating-tabbed-applications-images/segue-layout.png "The Storyboard should resemble this screenshot")](creating-tabbed-applications-images/segue-layout.png#lightbox)

Si hacemos clic en uno de los elementos de la barra de pestañas y exploramos el panel Propiedades, puede ver varias opciones, como se muestra a continuación:

[![](creating-tabbed-applications-images/properties-panel.png "Setting the tab options in the Properties Explorer")](creating-tabbed-applications-images/properties-panel.png#lightbox)

Podemos usar esto para editar ciertos atributos, como el distintivo, el título y el [identificador](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/UIKitUICatalog/TabBarItem.html)de iOS, entre otros.

Si guardamos y ejecutamos la aplicación ahora, veremos que el botón vuelve a aparecer cuando se carga la instancia de ViewController1 en TabBarController. Vamos a corregirlo comprobando si la vista actual tiene un controlador de vista primario. Si lo hace, sabemos que se encuentra dentro del TabBarController y, por tanto, se debe ocultar el botón. Vamos a agregar el código siguiente a la clase ViewController1:

```csharp
public override void ViewDidLoad ()
{
    if (ParentViewController != null){
        aButton.Hidden = true;
    }
}
```

Cuando se ejecuta la aplicación y el usuario pulsa el botón en la primera pantalla, se carga UITabBarController, con la vista de la primera pantalla que se encuentra en la primera pestaña, como se muestra a continuación:

[![](creating-tabbed-applications-images/first-view.png "The sample app output")](creating-tabbed-applications-images/first-view.png#lightbox)

<!--Save the files and run the application:

[![](creating-tabbed-applications-images/inital-screen-application.png "Save the files and run the application")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)-->

## <a name="summary"></a>Resumen

En este artículo se explica cómo usar un `UITabBarController` en una aplicación. Hemos explicado cómo cargar controladores en cada pestaña y cómo establecer propiedades en pestañas como el título, la imagen y el distintivo. A continuación, examinamos, utilizando guiones gráficos, cómo cargar un `UITabBarController` en tiempo de ejecución cuando no es el `RootViewController` de la ventana.

## <a name="related-links"></a>Vínculos relacionados

- [Creación de aplicaciones con pestañas (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/creatingtabbedapplications)
- [Images. zip](https://github.com/xamarin/ios-samples/blob/master/CreatingTabbedApplications/Resources/images.zip?raw=true)
- [Referencia de la clase UITabBarController](https://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBarController_Class/Reference/Reference.html)
