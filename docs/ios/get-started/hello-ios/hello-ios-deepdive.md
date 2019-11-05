---
title: Revisión en profundidad de Hello, iOS
description: Este documento analiza en profundidad la aplicación de ejemplo Hello, iOS, para lo que tiene en cuenta su arquitectura, la interfaz de usuario, la jerarquía de vistas del contenido, las pruebas, la implementación y mucho más.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 61ba3a7e-fe11-4439-8bc8-9809512b8eff
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 0fa6c05b3aa90f1d1875b9169350b197d882d863
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73023264"
---
# <a name="hello-ios--deep-dive"></a>Revisión en profundidad de Hello, iOS

En la guía de inicio rápido se introdujo la compilación y la ejecución de una aplicación básica de Xamarin.iOS. Ahora ha llegado el momento de conocer mejor el funcionamiento de las aplicaciones de iOS para poder crear programas más sofisticados. En esta guía se revisan los pasos de Hello, iOS que permiten comprender los aspectos básicos del desarrollo de aplicaciones de iOS.

Esta guía es una ayuda para desarrollar las habilidades y los conocimientos necesarios para compilar una aplicación de iOS de una sola pantalla. Cuando la haya finalizado, podrá comprender las diferentes partes de una aplicación de Xamarin.iOS y cómo encajan entre sí.

::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Introducción a Visual Studio para Mac

Visual Studio para Mac es un IDE gratuito de código abierto que combina las características de Visual Studio y XCode. Incluye un diseñador visual completamente integrado, un editor de texto completo con herramientas de refactorización, un explorador de ensamblados, integración de código fuente y mucho más. En esta guía se presentan algunas características básicas de Visual Studio para Mac, pero si está familiarizado con Visual Studio para Mac, consulte la documentación [Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/).

Visual Studio para Mac sigue la práctica de Visual Studio consistente en organizar el código en *soluciones* y *proyectos*. Una solución es un contenedor que puede incluir uno o varios proyectos. Un proyecto puede ser una aplicación (por ejemplo, de iOS o Android), una biblioteca auxiliar, una aplicación de prueba, etc. En la aplicación Phoneword se ha agregado un nuevo proyecto de iPhone con la nueva plantilla **Single View Application** (Aplicación de vista única). El aspecto de la solución inicial era este:

![](hello-ios-deepdive-images/image30.png "A screenshot of the initial solution")

::: zone-end
::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Introducción a Visual Studio

Visual Studio es un IDE muy completo de Microsoft. Incluye un diseñador visual completamente integrado, un editor de texto completo con herramientas de refactorización, un explorador de ensamblados, integración de código fuente y mucho más. En esta guía se presentan algunas características básicas de Visual Studio con las herramientas de Xamarin para Visual Studio.

Visual Studio organiza el código en soluciones y proyectos. Una solución es un contenedor que puede incluir uno o varios proyectos. Un proyecto puede ser una aplicación (por ejemplo, de iOS o Android), una biblioteca auxiliar, una aplicación de prueba, etc. En la aplicación Phoneword se ha agregado un nuevo proyecto de iPhone con la nueva plantilla **Single View Application** (Aplicación de vista única). El aspecto de la solución inicial era este:

![](hello-ios-deepdive-images/vs-image30.png "A screenshot of the initial solution")

::: zone-end

## <a name="anatomy-of-a-xamarinios-application"></a>Anatomía de una aplicación de Xamarin.iOS

::: zone pivot="macos"

A la izquierda se encuentra el **Panel de solución**, que contiene la estructura de directorios y todos los archivos asociados a la solución:

![](hello-ios-deepdive-images/image31.png "The solution Pad, which contains the directory structure and all the files associated with the solution")

::: zone-end
::: zone pivot="windows"

A la derecha se encuentra el **Panel de soluciones**, que contiene la estructura de directorios y todos los archivos asociados a la solución:

![](hello-ios-deepdive-images/vs-image31.png "The solution Pane, which contains the directory structure and all the files associated with the solution")

::: zone-end

En la guía de [Hello, iOS](~/ios/get-started/hello-ios/hello-ios-quickstart.md), creó una solución denominada **Phoneword** y colocó un proyecto de iOS, **Phoneword_iOS**, en ella. Entre los elementos del proyecto se incluyen los siguientes:

- **Referencias**: contiene los ensamblados necesarios para compilar y ejecutar la aplicación. Expanda el directorio para ver referencias a ensamblados .NET, como [System](https://docs.microsoft.com/dotnet/api/system), System.Core y [System.Xml](https://docs.microsoft.com/dotnet/api/system.xml), así como una referencia al ensamblado Xamarin.iOS.
- **Paquetes**: contiene los paquetes NuGet listos para su uso.
- **Recursos**: en esta carpeta se almacenan otros elementos multimedia.
- **Main.cs**: contiene el punto de entrada principal de la aplicación. Para iniciar la aplicación, se pasa el nombre de la clase principal de la aplicación, `AppDelegate`.
- **AppDelegate.cs**: este archivo contiene la clase de aplicación principal y es responsable de crear la ventana, compilar la interfaz de usuario y escuchar los eventos del sistema operativo.
- **Main.Storyboard**: el guión gráfico contiene el diseño visual de la interfaz de usuario de la aplicación. Los archivos de guión gráfico se abren en un editor gráfico llamado iOS Designer.
- **ViewController.cs**: el controlador de vistas inicia la pantalla (Vista) que los usuarios pueden ver y tocar. El controlador de vistas es responsable de controlar las interacciones entre el usuario y la vista.
- **ViewController.designer.cs**: el archivo `designer.cs` se genera automáticamente y sirve como nexo entre los controles de la vista y sus representaciones de código en el controlador de vistas. Puesto que es un archivo de mecanismos internos, el IDE sobrescribirá cualquier cambio manual. La mayoría de las veces se puede hacer caso omiso de este archivo. Para obtener más información sobre la relación entre el diseñador visual y el código auxiliar, consulte la guía [Introducción a iOS Designer](~/ios/user-interface/designer/introduction.md).
- **Info.plist**: **Info.plist** es la ubicación en la que se establecen las propiedades de la aplicación, como el nombre, los iconos, las imágenes de inicio y mucho más. Se trata de un archivo muy útil y encontrará una introducción completa al respecto en la guía [Trabajar con listas de propiedades de Xamarin.iOS](~/ios/app-fundamentals/property-lists.md).
- **Entitlements.plist**: la lista de propiedades de los derechos permite especificar las *capacidades*, también denominadas Tecnologías del App Store, como iCloud, PassKit y muchas otras. Puede obtener más información sobre **Entitlements.plist** en la guía [Trabajar con listas de propiedades de Xamarin.iOS](~/ios/app-fundamentals/property-lists.md). Para obtener una introducción general sobre los derechos, consulte la guía [Device Provisioning (Aprovisionamiento de dispositivos)](~/ios/get-started/installation/device-provisioning/index.md).

## <a name="architecture-and-app-fundamentals"></a>Arquitectura y aspectos básicos de la aplicación

Para que una aplicación de iOS pueda cargar una interfaz de usuario, es necesario hacer dos cosas. En primer lugar, la aplicación debe definir un *punto de entrada*: el primer código que se ejecuta cuando el proceso de la aplicación se carga en memoria. En segundo lugar, debe definir una clase para controlar los eventos de toda la aplicación e interactuar con el sistema operativo.

En esta sección se cubren las relaciones que se muestran en el siguiente diagrama:

[![](hello-ios-deepdive-images/image32.png "The Architecture and App Fundamentals relationships are illustrated in this diagram")](hello-ios-deepdive-images/image32.png#lightbox)

### <a name="main-method"></a>Main (método)

El punto de entrada principal de una aplicación de iOS es la clase `Application`. La clase `Application` se define en el archivo **Main.cs** y contiene un método `Main` estático. Crea una instancia de la aplicación de Xamarin.iOS y pasa el nombre de la clase *Application Delegate* que controla los eventos del sistema operativo. El código de plantilla del método `Main` estático aparece a continuación:

```csharp
using System;
using UIKit;

namespace Phoneword_iOS
{
    public class Application
    {
        static void Main (string[] args)
        {
            UIApplication.Main (args, null, "AppDelegate");
        }
    }
}
```

### <a name="application-delegate"></a>Application Delegate

En iOS, la clase *Application Delegate* controla los eventos del sistema y reside en **AppDelegate.cs**. La clase `AppDelegate` administra la *ventana* de la aplicación. La ventana es una instancia única de la clase `UIWindow` que actúa como contenedor para la interfaz de usuario. De forma predeterminada, una aplicación obtiene una sola ventana en la que se carga su contenido, ventana que a su vez va unida a una *pantalla* (instancia `UIScreen` única) que proporciona el rectángulo delimitador que coincide con las dimensiones de la pantalla del dispositivo físico.

*AppDelegate* también es responsable de la suscripción a las actualizaciones del sistema sobre eventos de aplicaciones importantes, como, por ejemplo, cuando la aplicación termina de iniciarse o cuando hay poca memoria.

A continuación se presenta el código de plantilla de AppDelegate:

```csharp
using System;
using Foundation;
using UIKit;

namespace Phoneword_iOS
{

    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        public override UIWindow Window {
            get;
            set;
        }

        ...
    }
}
```

Una vez que la aplicación ha definido su ventana, puede comenzar a cargar la interfaz de usuario. En la siguiente sección se aborda la creación de la interfaz de usuario.

## <a name="user-interface"></a>Interfaz de usuario

La interfaz de usuario de una aplicación de iOS es como un escaparate: la aplicación normalmente obtiene una ventana, pero la puede llenar con los objetos que necesite. Dichos objetos y su disposición pueden cambiarse según lo que quiera mostrarse en la aplicación. Los objetos de este escenario, lo que el usuario ve, se denominan "vistas". Para compilar una sola pantalla en una aplicación, las vistas se apilan unas sobre otras en una *jerarquía de vistas de contenido*, que, a su vez, se administra a través de un controlador de vistas único. Las aplicaciones con varias pantallas tienen varias jerarquías de vistas de contenido, cada una con su propio controlador de vistas. La aplicación coloca las vistas en la ventana para crear otra jerarquía de vistas de contenido basándose en la pantalla en la que se encuentra el usuario.

En esta sección se profundiza en la interfaz de usuario mediante la descripción de vistas, jerarquías de vistas de contenido y el diseñador iOS Designer.

### <a name="ios-designer-and-storyboards"></a>iOS Designer y guiones gráficos

iOS Designer es una herramienta visual para compilar interfaces de usuario de Xamarin. El diseñador se puede iniciar haciendo doble clic en cualquier archivo de guión gráfico (.storyboard), que se abrirá en una vista similar a la de la captura de pantalla siguiente:

::: zone pivot="macos"

![](hello-ios-deepdive-images/image33.png "iOS Designer Interface")

Un *guión gráfico* es un archivo que contiene los diseños visuales de las pantallas de la aplicación, así como las transiciones y las relaciones entre las pantallas. La representación de pantalla de la aplicación en un guión gráfico se denomina _escena_. Cada escena representa un controlador de vistas y la pila de vistas que administra, es decir, la jerarquía de vistas de contenido. Cuando se crea un proyecto de **aplicación de vista única** desde una plantilla, Visual Studio para Mac genera automáticamente un archivo de Storyboard denominado `Main.storyboard` y lo rellena con una sola escena, como se muestra en la siguiente captura de pantalla:

![](hello-ios-deepdive-images/image34.png "Visual Studio for Mac automatically generates a Storyboard file called Main.storyboard and populates it with a single Scene")

La barra negra situada en la parte inferior de la pantalla de Storyboard se puede seleccionar para elegir el controlador de vistas de la escena. El controlador de vistas es una instancia de la clase `UIViewController` que contiene el código de respaldo de la jerarquía de vistas de contenido. Las propiedades de este controlador de vistas se pueden ver y establecer en el **Panel de propiedades**, como se muestra en la siguiente captura de pantalla:

![](hello-ios-deepdive-images/image35.png "The Properties Pane")

::: zone-end
::: zone pivot="windows"

![](hello-ios-deepdive-images/vs-image33.png "iOS Designer Interface")

Un *guión gráfico* es un archivo que contiene los diseños visuales de las pantallas de la aplicación, así como las transiciones y las relaciones entre las pantallas. La representación de pantalla de la aplicación en un guión gráfico se denomina _escena_. Cada escena representa un controlador de vistas y la pila de vistas que administra, es decir, la jerarquía de vistas de contenido. Cuando se crea un proyecto de **aplicación de vista única** desde una plantilla, Visual Studio genera automáticamente un archivo de Storyboard denominado `Main.storyboard` y lo rellena con una sola escena, como se muestra en la siguiente captura de pantalla:

![](hello-ios-deepdive-images/vs-image34.png "Visual Studio automatically generates a Storyboard file called Main.storyboard and populates it with a single Scene")

La barra situada en la parte inferior de la pantalla de Storyboard se puede seleccionar para elegir el controlador de vistas de la escena. El controlador de vistas es una instancia de la clase `UIViewController` que contiene el código de respaldo de la jerarquía de vistas de contenido. Las propiedades de este controlador de vistas se pueden ver y establecer en el **Panel de propiedades**, como se muestra en la siguiente captura de pantalla:

![](hello-ios-deepdive-images/vs-image35.png "The Properties Pane")

::: zone-end

Para seleccionar la _vista_, haga clic en la parte blanca de la escena. La vista es una instancia de la clase `UIView` que define un área de la pantalla y proporciona interfaces para trabajar con el contenido de dicha área. La vista predeterminada es una sola *vista raíz* que ocupa toda la pantalla del dispositivo.

A la izquierda de la escena aparece una flecha gris con un icono de marca, como se muestra en la siguiente captura de pantalla:

 [![](hello-ios-deepdive-images/image37.png "A gray arrow with a flag icon")](hello-ios-deepdive-images/image37.png#lightbox)

La flecha gris representa una transición de Storyboard denominada *Segoe*, del inglés "seg-way". Puesto que este segoe no tiene ningún origen, se denomina *segoe sin origen*. Un segoe sin origen apunta a la primera escena cuyas vistas se cargan en la ventana de la aplicación al iniciarse esta. La escena y las vistas que incluye serán lo primero que verá el usuario al cargar la aplicación.

Cuando se compila una interfaz de usuario, se pueden arrastrar vistas adicionales desde el **cuadro de herramientas** a la vista principal en la superficie de diseño, como se muestra en la siguiente captura de pantalla:

::: zone pivot="macos"

![](hello-ios-deepdive-images/image38.png "Additional Views can be dragged from the Toolbox onto the main View on the design surface")

::: zone-end
::: zone pivot="windows"

![](hello-ios-deepdive-images/vs-image38.png "Additional Views can be dragged from the Toolbox onto the main View on the design surface")

::: zone-end

Estas vistas adicionales se denominan *subvistas*. Juntas, la vista raíz y las subvistas forman parte de una *jerarquía de vistas de contenido* que se administra con `ViewController`. El esquema de todos los elementos de la escena puede verse al examinar el panel **Esquema del documento**:

::: zone pivot="macos"

![](hello-ios-deepdive-images/image39.png "The Document Outline pad")

::: zone-end
::: zone pivot="windows"

![](hello-ios-deepdive-images/vs-image39.png "The Document Outline pad")

::: zone-end

Las subvistas se resaltan en el diagrama siguiente:

::: zone pivot="macos"

![](hello-ios-deepdive-images/image40.png "The Subviews are highlighted in the diagram")

::: zone-end
::: zone pivot="windows"

![](hello-ios-deepdive-images/vs-image40.png "The Subviews are highlighted in the diagram")

::: zone-end

En la siguiente sección se desglosa la jerarquía de las vistas de contenido representada por esta escena.

## <a name="content-view-hierarchy"></a>Jerarquía de vistas del contenido

Una _jerarquía de vistas de contenido_ es una pila de vistas y subvistas administrada por un único controlador de vistas, como se muestra en el diagrama siguiente:

 [![](hello-ios-deepdive-images/image41.png "The Content View Hierarchy")](hello-ios-deepdive-images/image41.png#lightbox)

Podemos hacer que la jerarquía de vistas de contenido de `ViewController` sea más fácil de ver, para lo cual será necesario cambiar el color de fondo de la vista raíz temporalmente por el amarillo en la sección de vista del **Controlador de propiedades**, como se muestra en la siguiente captura de pantalla:

::: zone pivot="macos"

![](hello-ios-deepdive-images/image42.png "Changing the background color of the root View to yellow in the View section of the Properties Pad")

::: zone-end
::: zone pivot="windows"

![](hello-ios-deepdive-images/vs-image42.png "Changing the background color of the root View to yellow in the View section of the Properties Pad")

::: zone-end

En el diagrama siguiente se muestran las relaciones entre la ventana, las vistas, las subvistas y el controlador de vistas que, de forma conjunta, proporcionan la interfaz de usuario a la pantalla del dispositivo:

[![](hello-ios-deepdive-images/image43.png "The relationships between the Window, Views, Subviews, and view controller")](hello-ios-deepdive-images/image43.png#lightbox)

En la siguiente sección se describe cómo trabajar con las vistas en el código y cómo programar para la interacción del usuario con los controladores de vistas y el ciclo de vida de las vistas.

## <a name="view-controllers-and-the-view-lifecycle"></a>Controladores de vistas y ciclo de vida de las vistas

Cada jerarquía de vistas de contenido tiene un controlador de vistas correspondiente para facilitar la interacción del usuario. El rol del controlador de vistas consiste en administrar las vistas en la jerarquía de vistas de contenido. El controlador de vistas no forma parte de la jerarquía de vistas de contenido y no es un elemento de la interfaz. Más bien, ofrece el código que permite las interacciones del usuario con los objetos de la pantalla.

### <a name="view-controllers-and-storyboards"></a>Controladores de vistas y guiones gráficos

::: zone pivot="macos"

El controlador de vistas se representa en un Storyboard como una barra en la parte inferior de la escena. Al seleccionar el controlador de vistas, se muestran sus propiedades en el **Panel de propiedades**:

![](hello-ios-deepdive-images/image44.png "Selecting the view controller brings up its properties in the Properties Pane")

Puede establecerse una clase personalizada de controlador de vistas para la jerarquía de vistas de contenido representada por esta escena mediante la edición de la propiedad **Clase** en la sección **Identidad** del **Panel de propiedades**. Por ejemplo, la aplicación **Phoneword** establece `ViewController` como controlador de vistas de la primera pantalla, como se muestra en la siguiente captura de pantalla:

![](hello-ios-deepdive-images/image45new.png "The Phoneword application sets the ViewController as the view controller")

::: zone-end
::: zone pivot="windows"

El controlador de vistas se representa en un Storyboard como una barra en la parte inferior de la escena. Al seleccionar el controlador de vistas, se muestran sus propiedades en el **Panel de propiedades**:

![](hello-ios-deepdive-images/vs-image44.png "Selecting the view controller brings up its properties in the Properties Pane")

Puede establecerse una clase personalizada de controlador de vistas para la jerarquía de vistas de contenido representada por esta escena mediante la edición de la propiedad **Clase** en la sección **Identidad** del **Panel de propiedades**. Por ejemplo, la aplicación **Phoneword** establece `ViewController` como controlador de vistas de la primera pantalla, como se muestra en la siguiente captura de pantalla:

![](hello-ios-deepdive-images/vs-image45.png "The Phoneword application sets the ViewController as the view controller")

::: zone-end

Así, se vincula la representación de Storyboard del controlador de vistas con la clase `ViewController` de C#. Abra el archivo `ViewController.cs` y observe que el controlador de vistas es una *subclase* de `UIViewController`, como se muestra en el código siguiente:

```csharp
public partial class ViewController : UIViewController
{
    public ViewController (IntPtr handle) : base (handle)
    {

    }
}
```

`ViewController` ahora administra las interacciones de la jerarquía de vistas de contenido asociada a este controlador de vistas en Storyboard. A continuación obtendrá información sobre el rol del controlador de vistas en la administración de las vistas mediante la introducción de un proceso denominado "ciclo de vida de las vistas".

> [!NOTE]
> En el caso de las pantallas únicamente de objetos visuales que no requieren la interacción del usuario, la propiedad **Clase** puede dejarse en blanco en el **Panel de propiedades**. De esta forma, se establece la clase auxiliar del controlador de vistas como la implementación predeterminada de `UIViewController`, lo que es adecuado si no va a agregar código personalizado.

### <a name="view-lifecycle"></a>Ciclo de vida de las vistas

El controlador de vistas es responsable de cargar y descargar las jerarquías de vistas de contenido en la ventana. Cuando ocurre algo importante en una vista en la jerarquía de vistas de contenido, el sistema operativo notifica al controlador de vistas a través de eventos en el ciclo de vida de las vistas. Al reemplazar los métodos del ciclo de vida de las vistas, puede interactuar con los objetos de la pantalla y crear una interfaz de usuario dinámica y con capacidad de respuesta.

Estos son los métodos básicos del ciclo de vida y su función:

- **ViewDidLoad**: se llama por *primera vez* cuando el controlador de vistas carga su jerarquía de vistas de contenido en la memoria. Es un buen momento para realizar la configuración inicial, porque es cuando están disponibles por primera vez las subvistas en el código.
- **ViewWillAppear**: se llama cada vez que la vista de un controlador de vistas está a punto de agregarse a una jerarquía de vistas de contenido y aparece en la pantalla.
- **ViewWillDisappear**: se llama cada vez que la vista de un controlador de vistas está a punto de quitarse de una jerarquía de vistas de contenido y desaparece de la pantalla. Este evento del ciclo de vida se utiliza para la limpieza y para guardar el estado.
- **ViewDidAppear** y **ViewDidDisappear**: se llaman cuando una vista se agrega a la jerarquía de vistas de contenido, respectivamente, o bien cuando se quita de ella.

Cuando se agrega código personalizado en cualquier etapa del ciclo de vida, ese método del ciclo de vida debe *reemplazar* la *implementación base*. Para ello, se debe pulsar el método del ciclo de vida existente, que ya tiene código asociado, y ampliarlo con código adicional. Se llama a la implementación base desde el interior del método para garantizar que el código original se ejecute antes que el nuevo código. En la sección siguiente se muestra un ejemplo de ello.

Para obtener más información sobre cómo trabajar con controladores de vistas, consulte [View Controller Programming Guide for iOS](https://developer.apple.com/library/archive/featuredarticles/ViewControllerPGforiPhoneOS/index.html#//apple_ref/doc/uid/TP40007457-CH2-SW1) (Guía de programación del controlador de vistas para iOS) y la [UIViewController reference](https://developer.apple.com/documentation/uikit/uiviewcontroller?language=objc) (Referencia de UIViewController) de Apple.

### <a name="responding-to-user-interaction"></a>Responder a la interacción del usuario

El rol más importante del controlador de vistas es responder a la interacción del usuario, como son las pulsaciones de botones, la navegación y otros elementos. La forma más sencilla de controlar la interacción del usuario es conectar un control para escuchar la entrada del usuario y unir un controlador de eventos para responder a ella. Por ejemplo, una opción sería conectar un botón para responder a un evento de toque, como se muestra en la aplicación Phoneword.

Veamos cómo funciona esto.
En el proyecto `Phoneword_iOS`, se ha agregado un botón denominado `TranslateButton` a la jerarquía de vistas de contenido:

[![](hello-ios-deepdive-images/image1.png "A button was added called TranslateButton to the Content View Hierarchy")](hello-ios-deepdive-images/image1.png#lightbox)

Cuando se asigna un **nombre** al control del **botón** en el **Panel de propiedades**, iOS Designer lo asigna automáticamente a un control en **ViewController.designer.cs**, de tal modo que `TranslateButton` está disponible en la clase `ViewController`. Los controles están disponibles primero en la fase `ViewDidLoad` del ciclo de vida de vistas, por lo que este método de ciclo de vida se usa para responder a los toques del usuario:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // wire up TranslateButton here
}
```

La aplicación Phoneword utiliza un evento de toques denominado `TouchUpInside` para escuchar el toque del usuario. `TouchUpInside` escucha un evento de toques (levantamiento del dedo hacia fuera de la pantalla) que va acompañado de un toque hacia abajo (dedo que toca la pantalla) dentro de los límites del control. Lo opuesto a `TouchUpInside` es el evento `TouchDown`, que se desencadena cuando el usuario presiona un control. El evento `TouchDown` captura mucho ruido y no permite al usuario cancelar el toque deslizando el dedo hacia fuera del control. `TouchUpInside` es la forma más común de responder al toque de un **botón** y crea la experiencia que el usuario espera al pulsar uno. Encontrará más información disponible al respecto en [Directrices de la interfaz humana de iOS](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/MobileHIG/index.html) de Apple.

La aplicación ha controlado el evento `TouchUpInside` con una expresión lambda, pero también se podría usar un delegado o un controlador de eventos con nombre. El código final del botón se parece al siguiente:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    string translatedNumber = "";

    TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
      translatedNumber = Core.PhonewordTranslator.ToNumber(PhoneNumberText.Text);
      PhoneNumberText.ResignFirstResponder ();

      if (translatedNumber == "") {
        CallButton.SetTitle ("Call", UIControlState.Normal);
        CallButton.Enabled = false;
      } else {
        CallButton.SetTitle ("Call " + translatedNumber, UIControlState.Normal);
        CallButton.Enabled = true;
      }
  };
}
```

## <a name="additional-concepts-introduced-in-phoneword"></a>Otros conceptos presentados en Phoneword

La aplicación Phoneword presentó varios conceptos que no se tratan en esta guía. Entre estos conceptos se incluyen los siguientes:

- **Cambiar el texto del botón**: la aplicación Phoneword muestra cómo cambiar el texto de un **botón** llamando a `SetTitle` en el **botón** y pasando el nuevo texto y el _estado de control_ del **botón**. Por ejemplo, el siguiente código cambia el texto "CallButton" por "Call":

    ```csharp
    CallButton.SetTitle ("Call", UIControlState.Normal);
    ```

- **Habilitar y deshabilitar botones**: los **botones** pueden encontrarse en un estado `Enabled` o `Disabled`. Si un **botón** está deshabilitado, no responderá a la entrada del usuario. Por ejemplo, el código siguiente deshabilita el elemento `CallButton`:

    ```csharp
    CallButton.Enabled = false;
    ```

    Para obtener más información sobre los botones, consulte la guía [Botones de Xamarin.iOS](~/ios/user-interface/controls/buttons.md).

- **Descartar el teclado**: cuando el usuario pulsa Campo de texto, iOS muestra el teclado para permitir que el usuario escriba datos. Lamentablemente, no hay ninguna funcionalidad integrada para descartar el teclado. El código siguiente se agrega a `TranslateButton` para descartar el teclado cuando el usuario pulsa el `TranslateButton`:

    ```csharp
    PhoneNumberText.ResignFirstResponder ();
    ```

    Para obtener otro ejemplo en el que se descarta el teclado, consulte la receta [Dismiss the Keyboard](https://github.com/xamarin/recipes/tree/master/Recipes/ios/input/keyboards/dismiss_the_keyboard) (Descartar el teclado).

- **Realizar una llamada de teléfono con la dirección URL**: en la aplicación Phoneword, se utiliza un esquema de dirección URL de Apple para iniciar la aplicación del teléfono del sistema. El esquema de dirección URL personalizado consta del prefijo "tel:" y del número de teléfono traducido, como se muestra en el código siguiente:

    ```csharp
    var url = new NSUrl ("tel:" + translatedNumber);
    if (!UIApplication.SharedApplication.OpenUrl (url))
    {
        // show alert Controller
    }
    ```

- **Mostrar una alerta**: cuando un usuario intenta realizar una llamada de teléfono en un dispositivo que no admite llamadas (por ejemplo, un simulador o un iPod Touch), se muestra un cuadro de diálogo de alerta para informar al usuario al respecto. El código siguiente crea y rellena un controlador de alerta:

    ```csharp
    if (!UIApplication.SharedApplication.OpenUrl (url)) {
                    var alert = UIAlertController.Create ("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
                    alert.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                    PresentViewController (alert, true, null);
                }
    ```

    Para obtener más información sobre las vistas de alertas de iOS, consulte [Alert Controller recipe (Fórmula del controlador de alertas)](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller).

## <a name="testing-deployment-and-finishing-touches"></a>Pruebas, implementación y toques finales

Tanto Visual Studio para Mac como Visual Studio ofrecen numerosas opciones para probar e implementar una aplicación. En esta sección se incluyen las opciones de depuración, se muestra cómo probar las aplicaciones en el dispositivo y se presentan herramientas para crear iconos de aplicación e imágenes de inicio.

### <a name="debugging-tools"></a>Herramientas de depuración

A veces, los problemas relacionados con el código de la aplicación son difíciles de diagnosticar. Para ayudar a diagnosticar problemas de código complejo, puede [establecer un punto de interrupción](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint), [examinar el código](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code) o [enviar información a la ventana de registro](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window).

### <a name="deploy-to-a-device"></a>Implementar en un dispositivo

El simulador de iOS brinda una forma rápida de probar una aplicación. El simulador tiene varias optimizaciones útiles para pruebas, incluida la ubicación del boceto, la [simulación del movimiento](https://github.com/xamarin/recipes/tree/master/Recipes/ios/multitasking/test_location_changes_in_simulator) y mucho más. Sin embargo, los usuarios no utilizarán la aplicación final en un simulador. Todas las aplicaciones deben probarse en dispositivos reales lo antes posible y con frecuencia.

Aprovisionar un dispositivo lleva tiempo y requiere una cuenta de desarrollador de Apple. En la guía [Device Provisioning (Aprovisionamiento del dispositivo)](~/ios/get-started/installation/device-provisioning/index.md) se proporcionan instrucciones precisas sobre cómo preparar un dispositivo para el desarrollo.

> [!NOTE]
> En este momento, debido a un requisito de Apple, es necesario tener un certificado de desarrollo o una _identidad de firma_ para compilar el código para el dispositivo físico o el simulador. Siga los pasos de la [guía Aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/manual-provisioning.md) para configurar esta opción.

Cuando se aprovisiona el dispositivo, puede realizar implementaciones en él al conectarlo, cambiar el destino en la barra de herramientas de compilación para el dispositivo iOS y pulsar **Start (Iniciar)** (**Play (Reproducir)** ) como se muestra en la siguiente captura de pantalla:

::: zone pivot="macos"

![](hello-ios-deepdive-images/image46new.png "Pressing Start/Play")

::: zone-end
::: zone pivot="windows"

![](hello-ios-deepdive-images/vs-image46.png "Pressing Start/Play")

::: zone-end

La aplicación se implementará en el dispositivo iOS:

[![](hello-ios-deepdive-images/image1.png "The app will deploy to the iOS device and run")](hello-ios-deepdive-images/image1.png#lightbox)

### <a name="generate-custom-icons-and-launch-images"></a>Generación de iconos personalizados e imágenes de inicio

No todos tienen a su disposición un diseñador para crear los iconos e imágenes de inicio personalizados que una aplicación necesita para destacar. A continuación se indican varios enfoques alternativos para generar material gráfico personalizado para la aplicación:

::: zone pivot="macos"

- [**Sketch**](https://www.sketchapp.com"): Sketch es una aplicación de Mac para diseñar interfaces de usuario, iconos y mucho más. Es la aplicación que se usó para diseñar el conjunto de iconos de aplicación e imágenes de inicio de Xamarin. Sketch 3 está disponible en la App Store. También puede probar la versión gratuita [Sketch Tool](https://bohemiancoding.com/sketch/tool/).
- [**Pixelmator**](https://www.pixelmator.com/): aplicación versátil de edición de imágenes para Mac que cuesta aproximadamente 30 USD.
- [**Glyphish**](https://www.glyphish.com/): conjuntos de iconos prediseñados de alta calidad que se pueden comprar o descargar de forma gratuita.
- [**Fiverr**](https://www.fiverr.com/): elija entre diversos diseñadores para que creen para usted un conjunto de iconos a partir de 5 USD. Probablemente elegirá un diseñador al azar, pero se trata de un buen recurso si necesita que le diseñen iconos sobre la marcha.

::: zone-end
::: zone pivot="windows"

- Visual Studio: puede usarlo para crear un conjunto de iconos sencillo para la aplicación directamente en el IDE.
- [**Glyphish**](https://www.glyphish.com/): conjuntos de iconos prediseñados de alta calidad que se pueden comprar o descargar de forma gratuita.
- [**Fiverr**](https://www.fiverr.com/): elija entre diversos diseñadores para que creen para usted un conjunto de iconos a partir de 5 USD. Probablemente elegirá un diseñador al azar, pero se trata de un buen recurso si necesita que le diseñen iconos sobre la marcha.

::: zone-end

Para obtener más información sobre los tamaños de las imágenes de inicio, los iconos y los requisitos, consulte la guía [Working with Images (Trabajar con imágenes)](~/ios/app-fundamentals/images-icons/index.md).

## <a name="summary"></a>Resumen

¡Enhorabuena! Ahora ya debería conocer bien los componentes de una aplicación de Xamarin.iOS, así como las herramientas necesarias para su creación.
En el [siguiente tutorial de la serie de introducción](~/ios/get-started/hello-ios-multiscreen/index.md), extenderá la aplicación para administrar varias pantallas. También tendrá la oportunidad de implementar un controlador de navegación, obtener información sobre los segues de Storyboard e introducir el patrón MVC (Modelo-Vista-Controlador) a medida que amplía la aplicación para controlar varias pantallas.

## <a name="related-links"></a>Vínculos relacionados

- [Hola, iOS (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios)
- [Directrices de la interfaz humana de iOS](https://developer.apple.com/design/human-interface-guidelines/ios/overview/themes/)
- [Portal de aprovisionamiento de iOS](https://developer.apple.com/account/#/overview)
