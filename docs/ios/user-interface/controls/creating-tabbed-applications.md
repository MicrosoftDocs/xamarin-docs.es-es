---
title: Barras de pestañas y controladores de la barra de pestañas de Xamarin.iOS
description: Este documento describe los controladores de barra de ficha de iOS y cómo usarlos con Xamarin.iOS. Muestra cómo configurar un UITabBarController, trabajar con imágenes, establezca los valores de notificación, trabajar con eventos y mucho más.
ms.prod: xamarin
ms.assetid: 7C772899-2900-F139-D642-F3C4F3F14DDC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 25d8563288cce614bc2823b0146e5121688c6f02
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725485"
---
# <a name="tab-bars-and-tab-bar-controllers-in-xamarinios"></a>Barras de pestañas y controladores de barra de pestañas en Xamarin. iOS

Se usan las aplicaciones con pestañas en iOS para admitir las interfaces de usuario donde se pueden tener acceso a varias pantallas en ningún orden determinado. A través de la `UITabBarController` (clase), las aplicaciones pueden incluir fácilmente compatibilidad para estos escenarios de varias pantallas. `UITabBarController` se encarga de la administración de varias pantalla, lo que permite al desarrollador de la aplicación para centrarse en los detalles de cada pantalla.

Por lo general, se compilan aplicaciones con pestañas con el `UITabBarController` que se va a la `RootViewController` de la ventana principal. Sin embargo, con un poco de código adicional, con pestañas de las aplicaciones también se usan en sucesión a alguna otra pantalla inicial, como el escenario donde una aplicación primero presenta una pantalla de inicio de sesión, seguida de la interfaz con pestañas.

En esta página se describen ambos escenarios: cuando las pestañas están en la raíz de la jerarquía de vistas de la aplicación y también en el escenario no`RootViewController`.

## <a name="introducing-uitabbarcontroller"></a>Introducción a UITabBarController

El `UITabBarController` admite por fichas de desarrollo de aplicaciones a lo siguiente:

- Permite que varios controladores para agregarse a ella.
- Proporcionar una interfaz de usuario con pestañas, a través de la `UITabBar` (clase), para permitir que un usuario cambiar entre los controladores y sus vistas.

Los controladores se agregan a la `UITabBarController` a través de su `ViewControllers` propiedad, que es un `UIViewController` matriz. El `UITabBarController` propio controla al cargar el controlador adecuado y presenta su vista en función de la pestaña seleccionada.

Las pestañas son instancias de la `UITabBarItem` (clase), que se encuentran en un `UITabBar` instancia. Cada `UITabBar` instancia es accesible a través del `TabBarItem` propiedad del controlador en cada pestaña.

Para obtener una descripción de cómo trabajar con el `UITabBarController`, vamos a compilar una aplicación sencilla que utiliza uno.

## <a name="tabbed-application-walkthrough"></a>Tutorial de la aplicación con pestañas

En este tutorial, vamos a crear la siguiente aplicación:

[![aplicación con pestañas de ejemplo](creating-tabbed-applications-images/00-app.png)](creating-tabbed-applications-images/00-app.png#lightbox)

Aunque ya existe una plantilla de aplicación con pestañas disponible en Visual Studio para Mac, en este ejemplo, estas instrucciones funcionan desde un proyecto vacío para obtener una mejor comprensión de cómo se construye la aplicación.

### <a name="creating-the-application"></a>Crear la aplicación

Empiece por crear una nueva aplicación.

Seleccione el **archivo > Nuevo > solución** elemento de menú en Visual Studio para Mac y seleccione un **iOS > aplicación > proyecto vacío** plantilla, el nombre del proyecto `TabbedApplication`, tal y como se muestra a continuación:

[![](creating-tabbed-applications-images/newsolution1.png "Select the Empty Project template")](creating-tabbed-applications-images/newsolution1.png#lightbox)

[![](creating-tabbed-applications-images/newsolution2.png "Name the project TabbedApplication")](creating-tabbed-applications-images/newsolution2.png#lightbox)

### <a name="adding-the-uitabbarcontroller"></a>Agregar el UITabBarController

A continuación, agregue una clase vacía seleccionando **archivo > nuevo archivo** y eligiendo la **General: clase vacía** plantilla. Nombre del archivo `TabController` tal como se muestra a continuación:

[![](creating-tabbed-applications-images/02-newclass.png "Add the TabController class")](creating-tabbed-applications-images/02-newclass.png#lightbox)

El `TabController` clase contendrá la implementación de la `UITabBarController` que va a administrar una matriz de `UIViewControllers`. Cuando el usuario selecciona una ficha, el `UITabBarController` se encargará de presentar la vista para el controlador de vista adecuado.

Para implementar el `UITabBarController` tenemos que hacer lo siguiente:

1. Establece la clase base del `TabController` a `UITabBarController` .
1. Crear `UIViewController` instancias se deben agregar a la `TabController` .
1. Agregar el `UIViewController` instancias en una matriz asignada a la `ViewControllers` propiedad de la `TabController` .

Agregue el código siguiente a la `TabController` clase para conseguir estos pasos:

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

Tenga en cuenta que para cada `UIViewController` instancia, establecemos la `Title` propiedad de la `UIViewController`. Cuando se agregan los controladores a la `UITabBarController`, el `UITabBarController` leerá el `Title` para cada controlador y mostrarlo en la etiqueta de la pestaña asociado tal como se muestra a continuación:

[![](creating-tabbed-applications-images/00-app.png "The sample app run")](creating-tabbed-applications-images/00-app.png#lightbox)

#### <a name="setting-the-tabcontroller-as-the-rootviewcontroller"></a>Establecer el TabController como RootViewController

El orden en que los controladores se colocan en las pestañas se corresponde con el orden en que se agregan a la `ViewControllers` matriz.

Para obtener el `UITabController` para cargar como la primera pantalla, es necesario para que sea la ventana `RootViewController`, tal y como se muestra en el siguiente código para el `AppDelegate`:

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

Si se ejecuta la aplicación ahora, el `UITabBarController` se cargará con la primera ficha seleccionada de forma predeterminada. La selección de cualquiera de las otras pestañas ver los resultados en el controlador asociado que se presenta el `UITabBarController,` tal como se muestra a continuación donde el usuario final ha seleccionado la segunda pestaña:

![La segunda pestaña mostrada](creating-tabbed-applications-images/03-secondtab-sml.png)

### <a name="modifying-tabbaritems"></a>Modificar TabBarItems

Ahora que tenemos un ejecución pestaña aplicación, vamos a modificar el `TabBarItem` para cambiar la imagen y texto que se muestra, así como para agregar un distintivo en una de las pestañas.

#### <a name="setting-a-system-item"></a>Establecer un elemento del sistema

En primer lugar, vamos a establecer la primera ficha para usar un elemento del sistema. En el constructor de la `TabController`, quite la línea que establece el controlador `Title` para el `tab1` de instancia y reemplácelo por el código siguiente para establecer el controlador `TabBarItem` propiedad:

```csharp
tab1.TabBarItem = new UITabBarItem (UITabBarSystemItem.Favorites, 0);
```

Al crear el `UITabBarItem` mediante un `UITabBarSystemItem`, el título y la imagen se proporcionan automáticamente mediante iOS, tal como se muestra en la captura de pantalla siguiente muestra la **favoritos** icono y el título en la primera pestaña:

 ![La primera pestaña con un icono de estrella](creating-tabbed-applications-images/04a-tabimage-sml.png)

#### <a name="setting-the-image"></a>Establecimiento de la imagen

Además de utilizar un elemento del sistema, el título y la imagen de un `UITabBarItem` se puede establecer en valores personalizados. Por ejemplo, cambiar el código que establece la `TabBarItem` propiedad del controlador denominado `tab2` como sigue:

```csharp
tab2 = new UIViewController ();
tab2.TabBarItem = new UITabBarItem ();
tab2.TabBarItem.Image = UIImage.FromFile ("second.png");
tab2.TabBarItem.Title = "Second";
tab2.View.BackgroundColor = UIColor.Orange;
```

En el código anterior se supone que se ha agregado una imagen denominada `second.png` a la raíz del proyecto (o un directorio de **recursos** ). Para admitir todas las densidades de pantalla, se necesitan tres imágenes, como se muestra a continuación:

![Imágenes agregadas al proyecto](creating-tabbed-applications-images/tabbedimages7new.png)

Vea la sección tamaño del icono de la **barra de pestañas** de la [Página iconos personalizados de Apple](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/custom-icons/) para obtener instrucciones sobre las dimensiones correctas. El tamaño recomendado varía en función del estilo de la imagen (circular, cuadrado, ancho o alto).

La propiedad `Image` solo debe establecerse en el segundo nombre de archivo **. png** , iOS cargará automáticamente los archivos de mayor resolución cuando sea necesario. Puede leer más sobre esto en el [trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md) guías. De forma predeterminada los elementos de la barra de pestaña son grises, con un tono azul cuando se selecciona.

#### <a name="overriding-the-title"></a>Reemplazar el título

Cuando la propiedad `Title` se establece directamente en el `TabBarItem`, invalidará cualquier conjunto de valores para `Title` en el propio controlador.

En la segunda pestaña (central) de esta captura de pantalla se muestra una imagen y un título personalizados:

![La segunda pestaña con un icono cuadrado](creating-tabbed-applications-images/05-customtab-sml.png)

#### <a name="setting-the-badge-value"></a>Establecer el valor del distintivo

Una pestaña también puede mostrar una notificación. Por ejemplo, agregue la siguiente línea de código para establecer un distintivo en la tercera pestaña:

```csharp
tab3.TabBarItem.BadgeValue = "Hi";
```

Ejecutar esto da como resultado una etiqueta roja con la cadena "¡Hi" en la esquina superior izquierda de la pestaña tal como se muestra a continuación:

![La segunda pestaña con un distintivo HI](creating-tabbed-applications-images/06-badge-sml.png)

El distintivo a menudo se usa para mostrar una indicación numérica no leída, nuevos elementos. Para quitar el distintivo, establezca el `BadgeValue` en null como se muestra a continuación:

```csharp
tab3.TabBarItem.BadgeValue = null;
```

## <a name="tabs-in-non-rootviewcontroller-scenarios"></a>Pestañas en escenarios que no son de RootViewController

En el ejemplo anterior, hemos mostrado cómo trabajar con un `UITabBarController` cuando es el `RootViewController` de la ventana. En este ejemplo, examinaremos cómo usar un `UITabBarController` cuando no es el `RootViewController` y muestran cómo se crea a usar guiones gráficos.

### <a name="initial-screen-example"></a>Ejemplo de pantalla inicial

En este escenario, la pantalla inicial se carga desde un controlador que no es un `UITabBarController`. Cuando el usuario interactúa con la pantalla pulsando un botón, se cargará el mismo controlador de vista en un `UITabBarController`, que, a continuación, se presenta al usuario. Captura de pantalla siguiente muestra el flujo de la aplicación:

[![](creating-tabbed-applications-images/inital-screen-application.png "This screenshot shows the application flow")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)

Vamos a iniciar una nueva aplicación en este ejemplo. Una vez más, vamos a usar el **iPhone > aplicación > proyecto vacío (C#)** plantilla, esta vez nombrar el proyecto `InitialScreenDemo`.

En este ejemplo, se usa un guion gráfico para diseñar los controladores de vista. Para agregar un guion gráfico:

- Haga doble clic en el nombre del proyecto y seleccione **Agregar > nuevo archivo**.

- Cuando aparezca el cuadro de diálogo nuevo archivo, vaya a **iOS > iPhone guión gráfico vacía**.

Vamos a llamar a este nuevo guion gráfico **MainStoryboard** , tal y como se muestra a continuación:

[![](creating-tabbed-applications-images/new-file-dialog.png "Add a MainStoryboard file to the project")](creating-tabbed-applications-images/new-file-dialog.png#lightbox)

Hay algunos pasos importantes a tener en cuenta cuando se agrega un guión gráfico en un archivo previamente sin guiones gráficos, que se tratan en el [Introducción a guiones gráficos](~/ios/user-interface/storyboards/index.md) guía. Los enfoques son:

1. Agregue el nombre del guion gráfico a la **interfaz principal** sección de la `Info.plist`:

    [![](creating-tabbed-applications-images/project-options.png "Set the Main Interface to MainStoryboard")](creating-tabbed-applications-images/project-options.png#lightbox)
1. En su `App Delegate`, invalide el método de ventana, con el código siguiente:

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

Vamos a necesitar tres controladores de vista para este ejemplo. Una, denominada `ViewController1`, se usará como controlador de vista inicial y en la primera pestaña. Los otros dos, denominados `ViewController2` y `ViewController3`, que se usarán en la segunda y tercera pestaña, respectivamente.

Abra el diseñador haciendo doble clic en el archivo MainStoryboard.storyboard y arrastre tres controladores de vista hasta la superficie de diseño. Queremos que cada uno de estos controladores de vista para tener su propia clase que corresponde al nombre anterior, es así, en **identidad > clase**, escriba su nombre, como se muestra en la captura de pantalla siguiente:

[![](creating-tabbed-applications-images/class-name.png "Set the Class to ViewController1")](creating-tabbed-applications-images/class-name.png#lightbox)

Visual Studio para Mac generará automáticamente las clases y los archivos de diseñador es necesario, esto se puede ver en el panel de solución, tal como se muestra a continuación:

[![](creating-tabbed-applications-images/solution-pad2.png "Auto-generated files in the project")](creating-tabbed-applications-images/solution-pad2.png#lightbox)

#### <a name="creating-the-ui"></a>Creación de la interfaz de usuario

A continuación, vamos a crear una interfaz de usuario simple para cada una de las vistas de ViewController, mediante el Diseñador de iOS de Xamarin.

Queremos arrastre un `Label` y un `Button` en ViewController1 desde el **cuadro de herramientas** en el lado derecho. A continuación, vamos a usar el panel de propiedades para editar el nombre y el texto de los controles a la siguiente:

- **Etiqueta** : `Text`  =  **uno**
- **Botón** : `Title`  =  **usuario realiza alguna acción inicial**

Se controlará la visibilidad de nuestro botón en un `TouchUpInside` eventos y tenemos que hacer referencia a él en el código subyacente. Vamos a identificarlo con el **nombre** `aButton` en el panel de propiedades, como se muestra en la captura de pantalla siguiente:

[![](creating-tabbed-applications-images/abutton-properties.png "Set the Name to aButton in the Properties Pad")](creating-tabbed-applications-images/abutton-properties.png#lightbox)

La superficie de diseño ahora debe ser similar a la captura de pantalla siguiente:

[![](creating-tabbed-applications-images/design-surface1.png "Your Design Surface should now look similar to this screenshot")](creating-tabbed-applications-images/design-surface1.png#lightbox)

Vamos a agregar un poco más detalle a `ViewController2` y `ViewController3`, agregando una etiqueta a cada uno, y cambiar el texto 'Dos' y 'Tres' respectivamente. Esto resalta al usuario qué estamos examinando de pestaña o vista.

#### <a name="wiring-up-the-button"></a>Conexión del botón

Vamos a cargar `ViewController1` cuando inicia la aplicación por primera vez. Cuando el usuario pulsa el botón, se oculta el botón y cargar un `UITabBarController` con el `ViewController1` instancia en la primera pestaña.

Cuando el usuario suelta el `aButton`, queremos que se desencadena un evento de TouchUpInside. Vamos a seleccionar el botón y en el **pestaña eventos** del panel de propiedades, declare el controlador de eventos: `InitialActionCompleted` : por lo que puede hacer referencia en el código. Esto se muestra en la captura de pantalla siguiente:

[![](creating-tabbed-applications-images/event-handler.png "When the user releases the aButton, trigger a TouchUpInside event")](creating-tabbed-applications-images/event-handler.png#lightbox)

Ahora tenemos que saber el controlador de vista para ocultar el botón cuando se desencadene el evento `InitialActionCompleted`. En `ViewController1`, agregue el siguiente método parcial:

```csharp
partial void InitialActionCompleted (UIButton sender)
{
    aButton.Hidden = true;  
}
```

Guarde el archivo y ejecutar la aplicación. Deberíamos ver pantalla aparecerá uno y el botón desaparecen al retocar.

#### <a name="adding-the-tab-bar-controller"></a>Agregar el controlador de barra de pestañas

Ahora tenemos nuestra vista inicial que funciona según lo esperado. A continuación, queremos que lo agregará a un `UITabBarController`, junto con las vistas de 2 y 3. Abramos el guión gráfico en el diseñador.

En el **cuadro de herramientas**, busque el **controlador de barra de pestañas** en controladores de & objetos y arrástrelo hasta la superficie de diseño. Como puede ver en la captura de pantalla siguiente, el controlador de barra de pestañas es menor de la interfaz de usuario y, por tanto, ofrece dos controladores de vista con él de forma predeterminada:

[![](creating-tabbed-applications-images/tabbarcontroller.png "Adding a Tab Bar Controller to the layout")](creating-tabbed-applications-images/tabbarcontroller.png#lightbox)

Eliminar estos nuevos controladores de vista, seleccione la barra negra situada en la parte inferior y presionando la tecla SUPR.

En el guión gráfico, podemos usar objetos Segue para controlar las transiciones entre el TabBarController y nuestros controladores de vista. Después de interactuar con la vista inicial, queremos cargarlos en el TabBarController presenta al usuario. Vamos a configurar esto en el diseñador.

**CTRL-clic** y **arrastre** desde el botón para el TabBarController. En el mouse hacia arriba, aparecerá un menú contextual. Queremos usar un segue modal.

Para configurar cada una de nuestras pestañas, **Ctrl-clic** desde el TabBarController a cada uno de nuestros controladores de vista en orden de uno a tres y seleccione la relación **ficha** en el menú contextual, como se muestra a continuación:

[![](creating-tabbed-applications-images/context-menu.png "Select the Tab Relationship")](creating-tabbed-applications-images/context-menu.png#lightbox)

El guión gráfico debe ser similar a la captura de pantalla siguiente:

[![](creating-tabbed-applications-images/segue-layout.png "The Storyboard should resemble this screenshot")](creating-tabbed-applications-images/segue-layout.png#lightbox)

Si se haga clic en uno de los elementos de la barra de pestaña y explore el panel Propiedades, puede ver una serie de opciones diferentes, como se muestra a continuación:

[![](creating-tabbed-applications-images/properties-panel.png "Setting the tab options in the Properties Explorer")](creating-tabbed-applications-images/properties-panel.png#lightbox)

Podemos usar esto para editar ciertos atributos, como el distintivo, el título y el identificador de iOS, entre otros.

Si se guarde y ejecute la aplicación ahora, buscaremos que vuelve a aparecer el botón cuando la instancia de ViewController1 se cargan en el TabBarController. Vamos a solucionar este problema mediante la comprobación para ver si la vista actual tiene un controlador de vista de un elemento primario. Si es así, sabemos están dentro de la TabBarController y, por lo tanto, se debe ocultar el botón. Vamos a agregar el código siguiente a la clase ViewController1:

```csharp
public override void ViewDidLoad ()
{
    if (ParentViewController != null){
        aButton.Hidden = true;
    }
}
```

Cuando se ejecuta la aplicación y el usuario pulsa el botón en la primera pantalla, el UITabBarController se carga, con la vista de la primera pantalla que se colocan en la primera pestaña, como se muestra a continuación:

[![la salida de la aplicación de ejemplo](creating-tabbed-applications-images/first-view-sml.png)](creating-tabbed-applications-images/first-view.png#lightbox)

## <a name="summary"></a>Resumen

En este artículo trata cómo usar un `UITabBarController` en una aplicación. Hemos visto cómo cargar los controladores en cada pestaña, así como cómo establecer propiedades en fichas tales el título, imagen y notificación. A continuación, examinamos, con guiones gráficos, cómo cargar un `UITabBarController` en tiempo de ejecución cuando no es el `RootViewController` de la ventana.

## <a name="related-links"></a>Vínculos relacionados

- [Creación de aplicaciones con pestañas (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/creatingtabbedapplications)
- [Images.zip](https://github.com/xamarin/ios-samples/blob/master/CreatingTabbedApplications/Resources/images.zip?raw=true)
- [Referencia de clase UITabBarController](https://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBarController_Class/Reference/Reference.html)
