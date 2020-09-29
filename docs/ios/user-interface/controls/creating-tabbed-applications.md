---
title: Barras de pestañas y controladores de barra de pestañas en Xamarin. iOS
description: En este documento se describen los controladores de barra de pestañas de iOS y cómo usarlos con Xamarin. iOS. Muestra cómo configurar un UITabBarController, trabajar con imágenes, establecer valores de distintivo, trabajar con eventos y mucho más.
ms.prod: xamarin
ms.assetid: 7C772899-2900-F139-D642-F3C4F3F14DDC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 9bd048239c404c0eb3309fdc74b26bcb94db4740
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434131"
---
# <a name="tab-bars-and-tab-bar-controllers-in-xamarinios"></a>Barras de pestañas y controladores de barra de pestañas en Xamarin. iOS

Las aplicaciones con pestañas se usan en iOS para admitir interfaces de usuario en las que se puede tener acceso a varias pantallas sin ningún orden determinado. A través de la `UITabBarController` clase, las aplicaciones pueden incluir fácilmente compatibilidad con estos escenarios de varias pantallas. `UITabBarController` se encarga de la administración de varias pantallas, lo que permite al desarrollador de la aplicación centrarse en los detalles de cada pantalla.

Normalmente, las aplicaciones con pestañas se compilan con la `UITabBarController` `RootViewController` de la ventana principal. Sin embargo, con un poco de código adicional, las aplicaciones con pestañas también se pueden usar de forma sucesiva en otra pantalla inicial, como el escenario en el que una aplicación presenta primero una pantalla de inicio de sesión, seguida de la interfaz con pestañas.

En esta página se describen ambos escenarios: cuando las pestañas están en la raíz de la jerarquía de vistas de la aplicación y también en el `RootViewController` escenario.

## <a name="introducing-uitabbarcontroller"></a>Introducción a UITabBarController

`UITabBarController`Admite el desarrollo de aplicaciones con pestañas de la siguiente manera:

- Permite agregar varios controladores a él.
- Proporcionar una interfaz de usuario con pestañas, a través de la  `UITabBar` clase, para permitir que un usuario cambie entre controladores y sus vistas.

Los controladores se agregan a `UITabBarController` mediante su `ViewControllers` propiedad, que es una `UIViewController` matriz. El `UITabBarController` propio controla la carga del controlador adecuado y la presentación de su vista en función de la pestaña seleccionada.

Las pestañas son instancias de la `UITabBarItem` clase, que están contenidas en una `UITabBar` instancia de. Cada `UITabBar` instancia es accesible a través `TabBarItem` de la propiedad del controlador en cada pestaña.

Para obtener una descripción de cómo trabajar con `UITabBarController` , veamos cómo crear una aplicación sencilla que use una.

## <a name="tabbed-application-walkthrough"></a>Tutorial de la aplicación con pestañas

En este tutorial vamos a crear la siguiente aplicación:

[![Aplicación con pestañas de ejemplo](creating-tabbed-applications-images/00-app.png)](creating-tabbed-applications-images/00-app.png#lightbox)

Aunque ya existe una plantilla de aplicación con pestañas disponible en Visual Studio para Mac, en este ejemplo, estas instrucciones funcionan desde un proyecto vacío para obtener una mejor comprensión de cómo se construye la aplicación.

### <a name="creating-the-application"></a>Crear la aplicación

Empiece por crear una nueva aplicación.

Seleccione el elemento de menú **archivo > nueva > solución** en Visual Studio para Mac y seleccione una **aplicación de > iOS > plantilla de proyecto vacía** , asigne al proyecto el nombre `TabbedApplication` que se muestra a continuación:

[![Seleccione la plantilla de proyecto vacía](creating-tabbed-applications-images/newsolution1.png)](creating-tabbed-applications-images/newsolution1.png#lightbox)

[![Asigne al proyecto el nombre TabbedApplication](creating-tabbed-applications-images/newsolution2.png)](creating-tabbed-applications-images/newsolution2.png#lightbox)

### <a name="adding-the-uitabbarcontroller"></a>Agregar UITabBarController

A continuación, agregue una clase vacía seleccionando **archivo > nuevo archivo** y eligiendo la plantilla de **clase general: Empty** . Asigne al archivo el nombre `TabController` que se muestra a continuación:

[![Agregar la clase TabController](creating-tabbed-applications-images/02-newclass.png)](creating-tabbed-applications-images/02-newclass.png#lightbox)

La `TabController` clase contendrá la implementación de `UITabBarController` que administrará una matriz de `UIViewControllers` . Cuando el usuario selecciona una pestaña, se encargará `UITabBarController` de presentar la vista del controlador de vistas adecuado.

Para implementar `UITabBarController` , es necesario hacer lo siguiente:

1. Establezca la clase base de  `TabController` en  `UITabBarController` .
1. Cree  `UIViewController` instancias para agregarlas al  `TabController` .
1. Agregue las  `UIViewController` instancias de a una matriz asignada a la  `ViewControllers` propiedad de  `TabController` .

Agregue el código siguiente a la `TabController` clase para realizar estos pasos:

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

Observe que para cada `UIViewController` instancia, se establece la `Title` propiedad de `UIViewController` . Cuando los controladores se agregan a `UITabBarController` , el `UITabBarController` leerá el `Title` para cada controlador y lo mostrará en la etiqueta de la pestaña asociada, como se muestra a continuación:

[![Ejecución de la aplicación de ejemplo](creating-tabbed-applications-images/00-app.png)](creating-tabbed-applications-images/00-app.png#lightbox)

#### <a name="setting-the-tabcontroller-as-the-rootviewcontroller"></a>Establecer TabController como RootViewController

El orden en que se colocan los controladores en las pestañas corresponde al orden en que se agregan a la `ViewControllers` matriz.

Para que se `UITabController` cargue como la primera pantalla, es necesario convertirla en la de la ventana `RootViewController` , como se muestra en el código siguiente para `AppDelegate` :

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

Si ejecutamos la aplicación ahora, `UITabBarController` se cargará con la primera pestaña seleccionada de forma predeterminada. Al seleccionar cualquiera de las otras pestañas, se obtiene la vista del controlador asociada que presenta el `UITabBarController,` , tal y como se muestra a continuación, donde el usuario final ha seleccionado la segunda pestaña:

![La segunda pestaña mostrada](creating-tabbed-applications-images/03-secondtab-sml.png)

### <a name="modifying-tabbaritems"></a>Modificar TabBarItems

Ahora que tenemos una aplicación de pestañas en ejecución, vamos a modificar el `TabBarItem` para cambiar la imagen y el texto que se muestran, así como para agregar un distintivo a una de las pestañas.

#### <a name="setting-a-system-item"></a>Establecer un elemento del sistema

En primer lugar, vamos a establecer la primera pestaña para usar un elemento del sistema. En el constructor de `TabController` , quite la línea que establece el del controlador `Title` para la `tab1` instancia y reemplácelo por el código siguiente para establecer la propiedad del controlador `TabBarItem` :

```csharp
tab1.TabBarItem = new UITabBarItem (UITabBarSystemItem.Favorites, 0);
```

Al crear el `UITabBarItem` mediante `UITabBarSystemItem` , iOS proporciona automáticamente el título y la imagen, tal como se muestra en la siguiente captura de pantalla que muestra el icono de **Favoritos** y el título en la primera pestaña:

 ![La primera pestaña con un icono de estrella](creating-tabbed-applications-images/04a-tabimage-sml.png)

#### <a name="setting-the-image"></a>Establecimiento de la imagen

Además de usar un elemento del sistema, el título y la imagen de un `UITabBarItem` se pueden establecer en valores personalizados. Por ejemplo, cambie el código que establece la `TabBarItem` propiedad del controlador denominado de la `tab2` manera siguiente:

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

La `Image` propiedad solo debe establecerse en el **second.png** nombre de archivo, iOS cargará automáticamente los archivos de mayor resolución cuando sea necesario. Puede leer más sobre esto en las guías [trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md) . De forma predeterminada, los elementos de la barra de pestañas están atenuados y tienen un matiz azul cuando se seleccionan.

#### <a name="overriding-the-title"></a>Reemplazar el título

Cuando la `Title` propiedad se establece directamente en `TabBarItem` , invalidará cualquier valor establecido para `Title` en el propio controlador.

En la segunda pestaña (central) de esta captura de pantalla se muestra una imagen y un título personalizados:

![La segunda pestaña con un icono cuadrado](creating-tabbed-applications-images/05-customtab-sml.png)

#### <a name="setting-the-badge-value"></a>Establecer el valor del distintivo

Una pestaña también puede mostrar un distintivo. Por ejemplo, agregue la siguiente línea de código para establecer un distintivo en la tercera pestaña:

```csharp
tab3.TabBarItem.BadgeValue = "Hi";
```

Al ejecutarlo, se obtiene una etiqueta roja con la cadena "HI" en la esquina superior izquierda de la pestaña, como se muestra a continuación:

![La segunda pestaña con un distintivo HI](creating-tabbed-applications-images/06-badge-sml.png)

El distintivo se usa a menudo para mostrar una indicación de número sin leer, elementos nuevos. Para quitar el distintivo, establezca `BadgeValue` en null, como se muestra a continuación:

```csharp
tab3.TabBarItem.BadgeValue = null;
```

## <a name="tabs-in-non-rootviewcontroller-scenarios"></a>Pestañas en escenarios que no son de RootViewController

En el ejemplo anterior, hemos mostrado cómo trabajar con un `UITabBarController` cuando es el `RootViewController` de la ventana. En este ejemplo, examinaremos cómo usar un `UITabBarController` cuando no sea `RootViewController` y mostraremos cómo se crea el uso de guiones gráficos.

### <a name="initial-screen-example"></a>Ejemplo de pantalla inicial

En este escenario, la pantalla inicial se carga desde un controlador que no es un `UITabBarController` . Cuando el usuario interactúa con la pantalla al puntear en un botón, el mismo controlador de vista se cargará en un `UITabBarController` , que se presentará al usuario. La siguiente captura de pantalla muestra el flujo de la aplicación:

[![En esta captura de pantalla se muestra el flujo de la aplicación](creating-tabbed-applications-images/inital-screen-application.png)](creating-tabbed-applications-images/inital-screen-application.png#lightbox)

Vamos a iniciar una nueva aplicación para este ejemplo. De nuevo, usaremos la plantilla de **iPhone > App > proyecto vacío (C#)** , esta vez asignando un nombre al proyecto `InitialScreenDemo` .

En este ejemplo, se usa un guion gráfico para diseñar los controladores de vista. Para agregar un guion gráfico:

- Haga clic con el botón derecho en el nombre del proyecto y seleccione **agregar > nuevo archivo**.

- Cuando aparezca el cuadro de diálogo nuevo archivo, vaya a **iOS > guion gráfico de iPhone vacío**.

Vamos a llamar a este nuevo guion gráfico **archivo mainstoryboard** , como se muestra a continuación:

[![Agregar un archivo archivo mainstoryboard al proyecto](creating-tabbed-applications-images/new-file-dialog.png)](creating-tabbed-applications-images/new-file-dialog.png#lightbox)

Hay algunos pasos importantes que se deben tener en cuenta al agregar un guion gráfico a un archivo que no es un guion gráfico anterior, que se explican en la guía [Introducción a los guiones gráficos](~/ios/user-interface/storyboards/index.md) . Dichos componentes son:

1. Agregue el nombre del guion gráfico a la sección **interfaz principal** del `Info.plist` :

    [![Establezca la interfaz principal en archivo mainstoryboard](creating-tabbed-applications-images/project-options.png)](creating-tabbed-applications-images/project-options.png#lightbox)
1. En el `App Delegate` , invalide el método de ventana, con el código siguiente:

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

Vamos a necesitar tres controladores de vista para este ejemplo. Una, denominada `ViewController1` , se usará como controlador de vista inicial y en la primera pestaña. Los otros dos, denominados `ViewController2` y `ViewController3` , que se usarán en la segunda y tercera pestaña respectivamente.

Abra el diseñador haciendo doble clic en el archivo archivo mainstoryboard. Storyboard y arrastre tres controladores de vista a la superficie de diseño. Queremos que cada uno de estos controladores de vista tenga su propia clase correspondiente al nombre anterior, de modo que, en **identidad > clase**, escriba su nombre, tal como se muestra en la siguiente captura de pantalla:

[![Establezca la clase en ViewController1](creating-tabbed-applications-images/class-name.png)](creating-tabbed-applications-images/class-name.png#lightbox)

Visual Studio para Mac generará automáticamente las clases y los archivos de diseñador necesarios, puede verse en el Panel de solución, como se muestra a continuación:

[![Archivos generados automáticamente en el proyecto](creating-tabbed-applications-images/solution-pad2.png)](creating-tabbed-applications-images/solution-pad2.png#lightbox)

#### <a name="creating-the-ui"></a>Crear la interfaz de usuario

A continuación, vamos a crear una interfaz de usuario simple para cada una de las vistas de ViewController con Xamarin iOS Designer.

Queremos arrastrar `Label` y `Button` hacia ViewController1 desde el **cuadro de herramientas** en el lado derecho. A continuación, usaremos el Panel de propiedades para editar el nombre y el texto de los controles para lo siguiente:

- **Etiqueta** : `Text`  =  **una**
- **Botón** : el `Title`  =  **usuario realiza algunas acciones iniciales**

Vamos a controlar la visibilidad de nuestro botón en un `TouchUpInside` evento y necesitamos hacer referencia a él en el código subyacente. Vamos a identificarlo con el **nombre** `aButton` en la panel de propiedades, como se muestra en la siguiente captura de pantalla:

[![Establezca el nombre en aButton en el Panel de propiedades](creating-tabbed-applications-images/abutton-properties.png)](creating-tabbed-applications-images/abutton-properties.png#lightbox)

El Superficie de diseño debe tener ahora un aspecto similar al de la siguiente captura de pantalla:

[![El Superficie de diseño debe tener ahora un aspecto similar a esta captura de pantalla](creating-tabbed-applications-images/design-surface1.png)](creating-tabbed-applications-images/design-surface1.png#lightbox)

Vamos a agregar un poco más detalle a `ViewController2` y `ViewController3` , agregando una etiqueta a cada uno y cambiando el texto a "dos" y "tres", respectivamente. Esto destaca al usuario de la pestaña o vista que estamos examinando.

#### <a name="wiring-up-the-button"></a>Conexión del botón

Vamos a cargar `ViewController1` cuando se inicie la aplicación por primera vez. Cuando el usuario pulse el botón, se ocultará el botón y se cargará `UITabBarController` con la `ViewController1` instancia en la primera pestaña.

Cuando el usuario suelta el `aButton` , queremos que se desencadene un evento TouchUpInside. Vamos a seleccionar el botón y, en la **pestaña eventos** del panel de propiedades, declare el controlador de eventos, `InitialActionCompleted` por lo que se puede hacer referencia a él en el código. Esto se muestra en la siguiente captura de pantalla:

[![Cuando el usuario libera aButton, desencadena un evento TouchUpInside](creating-tabbed-applications-images/event-handler.png)](creating-tabbed-applications-images/event-handler.png#lightbox)

Ahora es necesario indicar al controlador de vistas que oculte el botón cuando se desencadene el evento `InitialActionCompleted` . En `ViewController1` , agregue el siguiente método parcial:

```csharp
partial void InitialActionCompleted (UIButton sender)
{
    aButton.Hidden = true;  
}
```

Guarde el archivo y ejecute la aplicación. Deberíamos ver que aparece una pantalla y el botón desaparece al tocar.

#### <a name="adding-the-tab-bar-controller"></a>Agregar el controlador de barra de pestañas

Ahora tenemos nuestra vista inicial funcionando según lo previsto. A continuación, queremos agregarlo a un `UITabBarController` , junto con las vistas 2 y 3. Vamos a abrir el guion gráfico en el diseñador.

En el **cuadro de herramientas**, busque el **controlador de barra de pestañas** en controladores & objetos y arrástrelo hasta el superficie de diseño. Como puede ver en la siguiente captura de pantalla, el controlador de barra de pestañas es sin interfaz de usuario y, por tanto, incorpora dos controladores de vista de forma predeterminada:

[![Agregar un controlador de barra de pestañas al diseño](creating-tabbed-applications-images/tabbarcontroller.png)](creating-tabbed-applications-images/tabbarcontroller.png#lightbox)

Elimine estos nuevos controladores de vista seleccionando la barra negra situada en la parte inferior y presionando Supr.

En nuestro guion gráfico, podemos usar objetos segue para controlar las transiciones entre TabBarController y nuestros controladores de vista. Después de interactuar con la vista inicial, queremos cargarla en el TabBarController que se presenta al usuario. Vamos a configurar esto en el diseñador.

Pulse **Ctrl** y **arrastre** desde el botón hasta el TabBarController. Al activar el mouse, aparecerá un menú contextual. Queremos usar un segue modal.

Para configurar cada una de nuestras pestañas, **Presione la tecla Ctrl y haga clic** en TabBarController para cada uno de los controladores de vista en orden de uno a tres y seleccione la **pestaña** relación en el menú contextual, como se muestra a continuación:

[![Seleccionar la relación de tabulación](creating-tabbed-applications-images/context-menu.png)](creating-tabbed-applications-images/context-menu.png#lightbox)

El guión gráfico debe parecerse a la siguiente captura de pantalla:

[![El guión gráfico debe ser similar a esta captura de pantalla](creating-tabbed-applications-images/segue-layout.png)](creating-tabbed-applications-images/segue-layout.png#lightbox)

Si hacemos clic en uno de los elementos de la barra de pestañas y exploramos el panel Propiedades, puede ver varias opciones, como se muestra a continuación:

[![Establecer las opciones de la pestaña en el explorador de propiedades](creating-tabbed-applications-images/properties-panel.png)](creating-tabbed-applications-images/properties-panel.png#lightbox)

Podemos usar esto para editar ciertos atributos, como el distintivo, el título y el identificador de iOS, entre otros.

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

[![La salida de la aplicación de ejemplo](creating-tabbed-applications-images/first-view-sml.png)](creating-tabbed-applications-images/first-view.png#lightbox)

## <a name="summary"></a>Resumen

En este artículo se describe cómo usar un `UITabBarController` en una aplicación de. Hemos explicado cómo cargar controladores en cada pestaña y cómo establecer propiedades en pestañas como el título, la imagen y el distintivo. A continuación, hemos examinado, utilizando guiones gráficos, cómo cargar un `UITabBarController` en tiempo de ejecución cuando no es el `RootViewController` de la ventana.

## <a name="related-links"></a>Vínculos relacionados

- [Creación de aplicaciones con pestañas (ejemplo)](/samples/xamarin/ios-samples/creatingtabbedapplications)
- [Images.zip](https://github.com/xamarin/ios-samples/blob/master/CreatingTabbedApplications/Resources/images.zip?raw=true)
- [Referencia de la clase UITabBarController](https://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBarController_Class/Reference/Reference.html)