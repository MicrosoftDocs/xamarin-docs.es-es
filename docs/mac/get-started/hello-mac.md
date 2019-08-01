---
title: Tutorial Hello, Mac
description: En este documento se muestra cómo crear una aplicación Xamarin.Mac y se presentan Visual Studio para Mac, Xcode e Interface Builder. También se describe la exposición de controles de la interfaz de usuario al código mediante salidas y acciones y, por último, se explica cómo compilar, ejecutar y probar una aplicación Xamarin.Mac.
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 37D0E9E6-979B-7069-B3BE-C5F0AF99BA72
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 09/02/2018
ms.openlocfilehash: 8f5be75c750a6ee7efc52300382ca2241b87ecd3
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656921"
---
# <a name="hello-mac-walkthrough"></a>Tutorial Hello, Mac

Xamarin.Mac permite desarrollar aplicaciones de Mac completamente nativas en C# y .NET con las mismas API de macOS que se usan al desarrollar en *Objective-C* o *Swift*. Ya que Xamarin.Mac se integra directamente con Xcode, el desarrollador puede usar _Interface Builder_ de Xcode para crear interfaces de usuario de una aplicación (o, si quiere, puede crearlas directamente en código de C#).

Además, dado que las aplicaciones de Xamarin.Mac se escriben en C# y .NET, se puede compartir código con aplicaciones móviles de Xamarin.iOS y Xamarin.Android, a la vez que se que ofrece una experiencia nativa en todas las plataformas.

En este artículo, se presentan los conceptos básicos necesarios para crear una aplicación de Mac con Xamarin.Mac, Visual Studio para Mac e Interface Builder de Xcode, mediante el proceso de compilación de una aplicación **Hello, Mac** simple que cuente el número de veces que se ha hecho clic en un botón:

[![](hello-mac-images/run02-sml.png "Ejemplo de ejecución de la aplicación Hello, Mac")](hello-mac-images/run02.png#lightbox)

Se tratarán los siguientes conceptos:

- **Visual Studio para Mac**: introducción a Visual Studio para Mac y cómo crear aplicaciones de Xamarin.Mac con él.
- **Anatomía de una aplicación de Xamarin.Mac**: de qué consta una aplicación de Xamarin.Mac.
- **Interface Builder de Xcode**: cómo usar Interface Builder de Xcode para definir la interfaz de usuario de una aplicación.
- **Salidas y acciones**: cómo usar las salidas y acciones para conectar los controles de la interfaz de usuario.
- **Implementación y pruebas**: cómo ejecutar y probar una aplicación de Xamarin.Mac.

## <a name="requirements"></a>Requisitos

El desarrollo de aplicaciones de Xamarin.Mac requiere:

- Un equipo Mac con macOS High Sierra (10.13) o posterior.
- [Xcode 9 o posterior](https://itunes.apple.com/us/app/xcode/id497799835?mt=12).
- La última versión de [Xamarin.Mac y Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/installation/).

Para ejecutar una aplicación compilada con Xamarin.Mac, necesitará:

- Un equipo Mac que ejecute macOS 10.7 o una versión posterior.

> [!WARNING]
> La próxima versión de Xamarin.Mac 4.8 solo será compatible con macOS 10.9 o versiones posteriores.
> Las versiones anteriores de Xamarin.Mac son compatibles con macOS 10.7 o versiones posteriores, pero estas versiones anteriores de macOS carecen de infraestructura TLS suficiente para admitir TLS 1.2. Para macOS 10.7 o Mac OS 10.8 de destino, use Xamarin.Mac 4.6 o una versión anterior.

## <a name="starting-a-new-xamarinmac-app-in-visual-studio-for-mac"></a>Inicio de una nueva aplicación de Xamarin.Mac en Visual Studio para Mac

Como se ha indicado anteriormente, esta guía le llevará por los pasos necesarios para crear una aplicación de Mac denominada `Hello_Mac` que agrega un botón y una etiqueta simples en la ventana principal. Cuando se hace clic en el botón, la etiqueta mostrará el número de veces que se ha hecho clic en él.

Para comenzar, siga estos pasos:

1. Inicie Visual Studio para Mac:

    [![](hello-mac-images/setup01-sml.png "Interfaz principal de Visual Studio para Mac")](hello-mac-images/setup01.png#lightbox)

2. Haga clic en el botón **Nuevo proyecto...**  para abrir el cuadro de diálogo **Nuevo proyecto** y, después, seleccione **Mac** > **Aplicación** > **Aplicación de Cocoa** y haga clic en el botón **Siguiente**:

    [![](hello-mac-images/setup02-sml.png "Selección de una aplicación Cocoa")](hello-mac-images/setup02.png#lightbox)

3. Escriba `Hello_Mac` en **Nombre de la aplicación** y deje todo lo demás con el valor predeterminado. Haga clic en **Siguiente**:

    [![](hello-mac-images/setup03-sml.png "Establecimiento del nombre de la aplicación")](hello-mac-images/setup03.png#lightbox)

4. Confirme la ubicación del nuevo proyecto en el equipo:

    [![](hello-mac-images/setup04-sml.png "Comprobación de los detalles de la nueva solución")](hello-mac-images/setup04.png#lightbox)

5. Haga clic en el botón **Crear**.

Visual Studio para Mac creará la nueva aplicación de Xamarin.Mac y mostrará los archivos predeterminados que se agregan a la solución de la aplicación:

[![](hello-mac-images/project01-sml.png "Vista predeterminada de la nueva solución")](hello-mac-images/project01.png#lightbox)

Visual Studio para Mac usa la misma estructura de **Solución** y **Proyecto** que Visual Studio 2019. Una solución es un contenedor que puede contener uno o varios proyectos; los proyectos pueden incluir aplicaciones, bibliotecas auxiliares, aplicaciones de prueba, etc. La plantilla **Archivo > Nuevo proyecto** crea una solución y un proyecto de aplicación automáticamente.

## <a name="anatomy-of-a-xamarinmac-application"></a>Anatomía de una aplicación de Xamarin.Mac

La programación de aplicaciones de Xamarin.Mac es muy similar a trabajar con Xamarin.iOS. iOS usa el marco de CocoaTouch, que es una versión reducida de Cocoa, utilizada por Mac.

Eche un vistazo a los archivos del proyecto:

- **Main.cs** contiene el punto de entrada principal de la aplicación. Cuando se inicia la aplicación, la clase `Main` contiene el primer método que se ejecuta.
- **AppDelegate.cs** contiene la clase `AppDelegate` que es responsable de escuchar los eventos del sistema operativo.
- **Info.plist** contiene propiedades de la aplicación, como el nombre de la aplicación, los iconos, etc.
- **Entitlements.plist** contiene los derechos de la aplicación y permite el acceso, por ejemplo, a compatibilidad con espacio seguro y iCloud.
- **Main.storyboard** define la interfaz de usuario (ventanas y menús) de una aplicación y diseña las interconexiones entre las ventanas mediante objetos Segue. Los guiones gráficos son archivos XML que contienen la definición de las vistas (elementos de la interfaz de usuario). Interface Builder puede crear y mantener este archivo en Xcode.
- **ViewController.cs** es el controlador de la ventana principal. Los controladores se tratarán en detalle en otro artículo, pero por ahora, un controlador se puede considerar como el motor principal de una vista en particular.
- **ViewController.designer.cs** contiene código estructural que ayuda a realizar integraciones con la interfaz de usuario de la pantalla principal.

En las siguientes secciones, se echará un vistazo rápido a algunos de estos archivos. Más adelante, se analizarán con más detalle, pero es recomendable entender sus conceptos básicos ahora.

### <a name="maincs"></a>Main.cs

El archivo **Main.cs** es muy sencillo. Contiene un método `Main` estático que crea una nueva instancia de la aplicación de Xamarin.Mac y pasa el nombre de la clase que controla los eventos del sistema operativo, que en este caso es la clase `AppDelegate`:

```csharp
using System;
using System.Drawing;
using Foundation;
using AppKit;
using ObjCRuntime;

namespace Hello_Mac
{
    class MainClass
    {
        static void Main (string[] args)
        {
            NSApplication.Init ();
            NSApplication.Main (args);
        }
    }
}
```

### <a name="appdelegatecs"></a>AppDelegate.cs

El archivo `AppDelegate.cs` contiene una clase `AppDelegate`, que es responsable de crear ventanas y escuchar a los eventos del sistema operativo:

```csharp
using AppKit;
using Foundation;

namespace Hello_Mac
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        public AppDelegate ()
        {
        }

        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
    }
}
```

Es probable que el desarrollador no conozca este código, a no ser que haya creado una aplicación de iOS antes, pero es bastante sencillo.

El método `DidFinishLaunching` se ejecuta después de que se haya creado una instancia de la aplicación y es el responsable de crear realmente la ventana de la aplicación y comenzar el proceso de mostrar la vista en ella.

Se llamará al método `WillTerminate` cuando el usuario o el sistema hayan creado una instancia de cierre de la aplicación. El desarrollador debería usar este método para finalizar la aplicación antes de salir de ella (por ejemplo, para guardar las preferencias del usuario o el tamaño y la ubicación de la ventana).

### <a name="viewcontrollercs"></a>ViewController.cs

Cocoa (y, por derivación, CocoaTouch) usa lo que se conoce como el patrón *Model View Controller* (MVC). La declaración `ViewController` representa el objeto que controla la ventana real de la aplicación. Por lo general, para cada ventana creada (y para muchas otras cosas dentro de las ventanas) hay un controlador, que es responsable del ciclo de vida de la ventana, como mostrarla, agregarle nuevas vistas (controles), etc.

La clase `ViewController` es el controlador de la ventana principal. El controlador es responsable del ciclo de vida de la ventana principal. Esto se examinará en detalle más adelante, ahora le echaremos un vistazo rápido:

```csharp
using System;

using AppKit;
using Foundation;

namespace Hello_Mac
{
    public partial class ViewController : NSViewController
    {
        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any additional setup after loading the view.
        }

        public override NSObject RepresentedObject {
            get {
                return base.RepresentedObject;
            }
            set {
                base.RepresentedObject = value;
                // Update the view, if already loaded.
            }
        }
    }
}
```

### <a name="viewcontrollerdesignercs"></a>ViewController.Designer.cs

El archivo de diseñador de la clase Ventana principal originalmente está vacío, pero Visual Studio para Mac lo rellenará de forma automática mientras se crea la interfaz de usuario con Interface Builder en Xcode:

```csharp
// WARNING
//
// This file has been generated automatically by Visual Studio for Mac to store outlets and
// actions made in the UI designer. If it is removed, they will be lost.
// Manual changes to this file may not be handled correctly.
//
using Foundation;

namespace Hello_Mac
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

Los archivos de diseñador no deben editarse directamente, ya que los administra Visual Studio para Mac de forma automática para proporcionar el código estructural que permite el acceso a los controles que se han agregado a cualquier ventana o vista de la aplicación.

Con el proyecto de aplicación de Xamarin.Mac creado y un conocimiento básico de sus componentes, cambie a Xcode para crear la interfaz de usuario mediante Interface Builder.

### <a name="infoplist"></a>Info.plist

El archivo `Info.plist` contiene información sobre la aplicación de Xamarin.Mac, como su **Nombre** e **Identificador de agrupación**:

[![](hello-mac-images/infoplist01.png "Editor de plist de Visual Studio para Mac")](hello-mac-images/infoplist01.png#lightbox)

También define qué _guion gráfico_ se usará para mostrar la interfaz de usuario de la aplicación de Xamarin.Mac en la lista desplegable **Interfaz principal**. En el ejemplo anterior, `Main` en la lista desplegable se relaciona con `Main.storyboard` en el árbol de origen del proyecto del **Explorador de soluciones**. También define los iconos de la aplicación al especificar el *catálogo de recursos* que los contiene (**AppIcon** en este caso).

### <a name="entitlementsplist"></a>Entitlements.plist

El archivo `Entitlements.plist` de la aplicación controla los derechos que tiene la aplicación de Xamarin.Mac, como **espacio seguro** e **iCloud**:

[![](hello-mac-images/entitlements01.png "Editor de derechos de Visual Studio para Mac")](hello-mac-images/entitlements01.png#lightbox)

En el ejemplo de Hello World, no se requerirá ningún derecho. En la siguiente sección, se muestra cómo usar Interface Builder en Xcode para editar el archivo **Main.storyboard** y definir la interfaz de usuario de la aplicación de Xamarin.Mac.

## <a name="introduction-to-xcode-and-interface-builder"></a>Introducción a Xcode y a Interface Builder

Como parte de Xcode, Apple ha creado una herramienta denominada Interface Builder, que permite a los desarrolladores crear una interfaz de usuario de forma visual en un diseñador. Xamarin.Mac se integra con fluidez con Interface Builder, lo que permite crear la interfaz de usuario con las mismas herramientas que los usuarios de Objective-C.

Para comenzar, haga doble clic en el archivo `Main.storyboard` del **Explorador de soluciones** para abrirlo y editarlo en Xcode e Interface Builder:

[![](hello-mac-images/xcode01.png "Archivo Main.storyboard en el Explorador de soluciones")](hello-mac-images/xcode01.png#lightbox)

Se debería iniciar Xcode con un aspecto similar al de esta captura de pantalla:

[![](hello-mac-images/xcode02.png "Vista predeterminada de Interface Builder de Xcode")](hello-mac-images/xcode02.png#lightbox)

Antes de comenzar a diseñar la interfaz, vea una introducción rápida de Xcode para orientarse con las principales características que usará.

> [!NOTE]
> El desarrollador no tiene que usar Xcode e Interface Builder para crear la interfaz de usuario de una aplicación de Xamarin.Mac. La interfaz de usuario se puede crear directamente desde el código de C#, pero eso está fuera del ámbito de este artículo. Por simplicidad, usará Interface Builder para crear la interfaz de usuario en el resto de este tutorial.

### <a name="components-of-xcode"></a>Componentes de Xcode

Al abrir un archivo **.storyboard** en Xcode de Visual Studio para Mac, se abrirá con un **navegador de proyectos** a la izquierda, la **jerarquía de la interfaz** y el **editor de la interfaz** en el medio y una sección de **Propiedades y utilidades** a la derecha:

[![](hello-mac-images/xcode03.png "Distintas secciones de Interface Builder en Xcode")](hello-mac-images/xcode03.png#lightbox)

En las siguientes secciones, se echa un vistazo a qué hace cada una de estas características de Xcode y cómo usarlas para crear la interfaz de una aplicación de Xamarin.Mac.

### <a name="project-navigation"></a>Navegación del proyecto

Al abrir un archivo **.storyboard** para editarlo en Xcode, Visual Studio para Mac crea un *archivo de proyecto Xcode* en segundo plano para comunicar los cambios entre él y Xcode. Más adelante, cuando el desarrollador vuelve a Visual Studio para Mac de Xcode, Visual Studio para Mac sincroniza los cambios realizados en este proyecto con el proyecto de Xamarin.Mac.

La sección **Navegación del proyecto** permite al desarrollador desplazarse por todos los archivos que componen este proyecto de Xcode de _correcciones de compatibilidad (shim)_ . Normalmente, solo les interesarán los archivos `.storyboard` de esta lista, como `Main.storyboard`.

### <a name="interface-hierarchy"></a>Jerarquía de la interfaz

En la sección **Jerarquía de la interfaz**, el desarrollador puede tener acceso fácilmente a varias propiedades clave de la interfaz de usuario, como sus **marcadores de posición** y la **ventana** principal. Esta sección se puede usar para tener acceso a los elementos individuales (vistas) que conforman la interfaz de usuario y para ajustar la forma en que se anidan, al arrastrarlos por la jerarquía.

### <a name="interface-editor"></a>Editor de la interfaz

En la sección **Editor de la interfaz** se proporciona la superficie en la que se establece la interfaz de usuario de forma gráfica. Arrastre elementos desde la sección **Biblioteca** de la sección **Properties & Utilities** (Propiedades y utilidades) para crear el diseño. Al agregar elementos de interfaz de usuario (vistas) a la superficie de diseño, se agregarán a la sección **Interface Hierarchy** (Jerarquía de la interfaz) en el orden en que aparecen en el **Interface Editor** (Editor de la interfaz).

### <a name="properties--utilities"></a>Propiedades y utilidades

La sección **Properties & Utilities** (Propiedades y utilidades) se divide en dos secciones principales, **Propiedades** (también denominada Inspectores) y la **Biblioteca**:

[![](hello-mac-images/xcode04.png "Inspector de propiedades")](hello-mac-images/xcode04.png#lightbox)

Inicialmente, esta sección está casi vacía. En cambio, si el desarrollador selecciona un elemento del **Interface Editor** (Editor de la interfaz) o de la **Interface Hierarchy** (Jerarquía de la interfaz), la sección **Propiedades** se rellenará con información sobre ese elemento y las propiedades que se pueden ajustar.

En la sección **Propiedades** hay ocho *pestañas de inspectores* diferentes, como se muestra en la siguiente ilustración:

[![](hello-mac-images/xcode05.png "Vista general de todos los inspectores")](hello-mac-images/xcode05.png#lightbox)

### <a name="properties--utility-types"></a>Tipos de propiedades y utilidades

De izquierda a derecha, estas pestañas son:

- **File Inspector** (Inspector de archivos): en esta pestaña se muestra la información del archivo, como el nombre de archivo y la ubicación del archivo Xib que se está editando.
- **Ayuda rápida**: en la pestaña Ayuda rápida se proporciona ayuda contextual según lo que está seleccionado en Xcode.
- **Identity Inspector** (Inspector de identidad): en esta pestaña se proporciona información sobre el control o la vista seleccionados.
- **Attributes Inspector** (Inspector de atributos): en esta pestaña, el desarrollador puede personalizar varios atributos de la vista o el control seleccionados.
- **Size Inspector** (Inspector de tamaño): en esta pestaña, el desarrollador puede controlar el tamaño y cambiar el tamaño del comportamiento de la vista o del control seleccionados.
- **Connections Inspector** (Inspector de conexiones): en esta pestaña, se muestran las conexiones **Salida** y **Acción** de los controles seleccionados. Salidas y Acciones se describirán en detalle a continuación.
- **Bindings Inspector** (Inspector de enlaces): en esta pestaña, el desarrollador puede configurar controles para que sus valores se enlacen de forma automática con los modelos de datos.
- **View Effects Inspector** (Inspector de efectos de vista): en esta pestaña, el desarrollador puede especificar efectos en los controles, como animaciones.

Use la sección **Biblioteca** para buscar controles y objetos que colocará en el diseñador para compilar de forma gráfica la interfaz de usuario:

[![](hello-mac-images/xcode06.png "Inspector de biblioteca de Xcode")](hello-mac-images/xcode06.png#lightbox)

## <a name="creating-the-interface"></a>Creación de la interfaz

Una vez cubiertos los aspectos básicos del IDE de Xcode y de Interface Builder, el desarrollador puede crear la interfaz de usuario de la vista principal.

Siga estos pasos para usar Interface Builder:

1. En Xcode, arrastre un **botón de comando** de la **sección Biblioteca**:

    [![](hello-mac-images/xcode07.png "Selección de un NSButton desde un inspector de biblioteca")](hello-mac-images/xcode07.png#lightbox)

2. Coloque el botón en la **Vista** (bajo el **controlador de ventana**) en el **Interface Editor** (Editor de la interfaz):

    [![](hello-mac-images/xcode08.png "Agregar un botón al diseño de la interfaz")](hello-mac-images/xcode08.png#lightbox)

3. Haga clic en la propiedad **Título** de **Attribute Inspector** (Inspector de atributos) y cambie el título del botón por **Hacer clic aquí**:

    [![](hello-mac-images/xcode09.png "Establecimiento de las propiedades del botón")](hello-mac-images/xcode09.png#lightbox)

4. Arrastre una **Etiqueta** de la **sección Biblioteca**:

    [![](hello-mac-images/xcode10.png "Selección de una etiqueta desde el inspector de biblioteca")](hello-mac-images/xcode10.png#lightbox)

5. Coloque la etiqueta en la **Ventana** situada junto al botón en el **Interface Editor** (Editor de la interfaz):

    [![](hello-mac-images/xcode11.png "Agregar una etiqueta al diseño de la interfaz")](hello-mac-images/xcode11.png#lightbox)

6. Agarre el controlador derecho de la etiqueta y arrástrelo hasta que esté cerca del borde de la ventana:

    [![](hello-mac-images/xcode12.png "Cambio del tamaño de la etiqueta")](hello-mac-images/xcode12.png#lightbox)

7. Seleccione el botón que acaba de agregar en **Interface Editor** (Editor de la interfaz) y haga clic en el icono del **Editor de restricciones** en la parte inferior de la ventana:

    [![](hello-mac-images/xcode13.png "Agregar restricciones al botón")](hello-mac-images/xcode13.png#lightbox)

8. En la parte superior del editor, haga clic en **Red I-Beams** en la parte superior e izquierda. Al cambiar el tamaño de la ventana, se mantendrá el botón en la misma ubicación en la esquina superior izquierda de la pantalla.

9. Después, compruebe los cuadros **Alto** y **Ancho** y use los tamaños predeterminados. Esto mantiene el botón con el mismo tamaño cuando la ventana cambia de tamaño.

10. Haga clic en el botón **Add 4 Constraints** (Agregar 4 restricciones) para agregar las restricciones y cerrar el editor.

11. Seleccione la etiqueta y vuelva a hacer clic en el icono del **Editor de restricciones**:

    [![](hello-mac-images/xcode14.png "Agregar restricciones a la etiqueta")](hello-mac-images/xcode14.png#lightbox)

12. Al hacer clic en **Red I-Beams** en la parte superior derecha e izquierda del **Editor de restricciones**, se indica a la etiqueta que se bloquee en las ubicaciones X e Y y que se aumente y reduzca al cambiar el tamaño de la ventana de la aplicación en ejecución.

13. De nuevo, compruebe el cuadro **Alto** y use el tamaño predeterminado y, después, haga clic en el botón **Add 4 Constraints** (Agregar 4 restricciones) para agregar las restricciones y cerrar el editor.

14. Guarde los cambios de la interfaz de usuario.

Al cambiar el tamaño y mover controles, tenga en cuenta que Interface Builder ofrece sugerencias de ajuste útiles basadas en las [directrices de interfaz humana de macOS](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/). Estas instrucciones ayudarán al desarrollador a crear aplicaciones de alta calidad que tendrán una apariencia familiar para los usuarios de Mac.

En la sección **Jerarquía de la interfaz**, puede ver cómo se muestran el diseño y la jerarquía de los elementos que componen la interfaz de usuario:

[![](hello-mac-images/xcode15.png "Selección de un elemento en la jerarquía de la interfaz")](hello-mac-images/xcode15.png#lightbox)

Desde aquí, el desarrollador puede seleccionar elementos para editarlos o arrastrarlos para volver a ordenar los elementos de la interfaz de usuario si es necesario. Por ejemplo, si un elemento cubría a otro elemento de interfaz de usuario, podría arrastrarlo a la parte inferior de la lista para convertirlo en el elemento de nivel superior de la ventana.

Una vez que la interfaz de usuario esté creada, el desarrollador tendrá que exponer los elementos de interfaz de usuario para que Xamarin.Mac pueda obtener acceso e interactuar con ellos en código de C#. En la siguiente sección, **Salidas y acciones**, se muestra cómo hacerlo.

### <a name="outlets-and-actions"></a>Salidas y acciones

¿Qué son las **salidas** y **acciones**? En la programación tradicional de interfaz de usuario de .NET, un control de la interfaz de usuario se expone de forma automática como una propiedad cuando se agrega. En Mac es algo diferente, al agregar simplemente un control a una vista, el código no puede obtener acceso a él. El desarrollador debe exponer de forma explícita el elemento de interfaz de usuario al código. Para ello, Apple ofrece dos opciones:

- **Salidas**: las salidas son análogas a las propiedades. Si el desarrollador conecta un control a una salida, se expone al código a través de una propiedad, de modo que puede hacer cosas como agregar controladores de eventos, llamar a métodos en ella, etc.
- **Acciones**: las acciones son análogas al patrón de comando en WPF. Por ejemplo, al realizar una acción en un control (como un clic en un botón), el control llama de forma automática a un método en el código. Las acciones son eficaces y convenientes porque el desarrollador puede conectar muchos controles a la misma acción.

En Xcode, las **salidas** y las **acciones** se agregan directamente al código mediante *el arrastre de controles*. Más concretamente, esto significa que, para poder crear una **salida** o **acción**, el desarrollador elegirá un elemento de control al que agregar una **salida** o **acción**, mantendrá presionada la tecla **Control** del teclado y arrastrará ese control directamente al código.

Para los desarrolladores de Xamarin.Mac, esto significa que el desarrollador lo arrastrará a los archivos de código auxiliar de Objective-C correspondientes al archivo de C# en el que quiere crear la **salida** o **acción**. Visual Studio para Mac ha creado un archivo denominado `ViewController.h` como parte del proyecto de Xcode de correcciones de compatibilidad (shim) que ha generado para usar Interface Builder:

[![](hello-mac-images/xcode16-sml.png "Consulta del código fuente de Xcode")](hello-mac-images/xcode16.png#lightbox)

Este archivo `.h` de código auxiliar refleja el `ViewController.designer.cs` que se agrega de forma automática a un proyecto de Xamarin.Mac cuando se crea un nuevo `NSWindow`. Este archivo se usará para sincronizar los cambios realizados por Interface Builder y es donde se crean las **salidas** y **acciones** para que los elementos de interfaz de usuario se expongan al código de C#.

#### <a name="adding-an-outlet"></a>Agregar una salida

Con una comprensión básica de lo que son las **salidas** y **acciones**, cree una **salida** para exponer la etiqueta creada para nuestro código de C#.

Haga lo siguiente:

1. En Xcode, en la esquina superior disponible más a la derecha de la pantalla, haga clic en el botón de **doble círculo** para abrir **Assistant Editor** (Editor del asistente):

    [![](hello-mac-images/outlet01.png "Mostrar el Editor del asistente")](hello-mac-images/outlet01.png#lightbox)

2. Xcode cambiará a un modo de vista en dos paneles con el **Interface Editor** (Editor de la interfaz) en un lado y un **Editor de código** en el otro.

3. Observe que Xcode ha seleccionado de forma automática el archivo **ViewController.m** en el **Editor de código**, que es incorrecto. En el diálogo anterior sobre qué son las **salidas**  y **acciones**, el desarrollador deberá tener el archivo **ViewController.h** seleccionado.

4. En la parte superior del **Editor de código**, haga clic en el **vínculo automático** y seleccione el archivo `ViewController.h`:

    [![](hello-mac-images/outlet02.png "Selección del archivo correcto")](hello-mac-images/outlet02.png#lightbox)

5. Ahora, Xcode debería tener el archivo correcto seleccionado:

    [![](hello-mac-images/outlet03.png "Consulta del archivo ViewController.h")](hello-mac-images/outlet03.png#lightbox)

6. **El último paso es muy importante**: si no ha seleccionado el archivo correcto, no podrá crear **salidas** y **acciones** o se expondrán a la clase incorrecta en C#.

7. En el **Interface Editor** (Editor de la interfaz), mantenga presionada la tecla **Control** del teclado y haga clic y arrastre la etiqueta que ha creado anteriormente al editor de código justo debajo del código `@interface ViewController : NSViewController {}`:

    [![](hello-mac-images/outlet04.png "Arrastrar para crear una salida")](hello-mac-images/outlet04.png#lightbox)

8. Aparecerá un cuadro de diálogo. Deje la **Conexión** establecida en **Salida** y escriba `ClickedLabel` en el **Nombre**:

    [![](hello-mac-images/outlet05.png "Definición de la salida")](hello-mac-images/outlet05.png#lightbox)

9. Haga clic en el botón **Conectar** para crear la **salida**:

    [![](hello-mac-images/outlet06.png "Consulta de la salida final")](hello-mac-images/outlet06.png#lightbox)

10. Guarde los cambios en el archivo.

#### <a name="adding-an-action"></a>Agregar una acción

Después, exponga el botón al código de C#. Al igual que con la etiqueta anterior, el desarrollador podría conectar el botón con una **salida**. Puesto que solo queremos responder al botón en el que se hace clic, use una **acción** en su lugar.

Haga lo siguiente:

1. Asegúrese de que Xcode siga aún en **Assistant Editor** (Editor del asistente) y el archivo **ViewController.h** esté visible en el **Editor de código**.

2. En el **Interface Editor** (Editor de la interfaz), mantenga presionada la tecla **Control** del teclado y haga clic y arrastre el botón que ha creado anteriormente al editor de código justo debajo del código `@property (assign) IBOutlet NSTextField *ClickedLabel;`:

    [![](hello-mac-images/action01.png "Arrastrar para crear una acción")](hello-mac-images/action01.png#lightbox)

3. Cambie el tipo de **Conexión** por **Acción**:

    [![](hello-mac-images/action02.png "Definición de la acción")](hello-mac-images/action02.png#lightbox)

4. Escriba `ClickedButton` como **Nombre**:

    [![](hello-mac-images/action03.png "Asignación de un nombre a la nueva acción")](hello-mac-images/action03.png#lightbox)

5. Haga clic en el botón **Conectar** para crear la **acción**:

    [![](hello-mac-images/action04.png "Consulta de la acción final")](hello-mac-images/action04.png#lightbox)

6. Guarde los cambios en el archivo.

Con la interfaz de usuario conectada y expuesta al código de C#, vuelva a Visual Studio para Mac y permita que se sincronicen los cambios realizados en Xcode y en Interface Builder.

> [!NOTE]
> Es probable que haya tardado mucho tiempo en crear la interfaz de usuario y las **salidas** y **acciones** de esta primera aplicación, lo cual puede parecer mucho trabajo, pero se han introducido muchos conceptos nuevos y se ha dedicado una gran cantidad de tiempo a cubrir esta nueva base. Después de trabajar un poco con Interface Builder para practicar, esta interfaz y todas sus **salidas** y **acciones** se pueden crear en un par de minutos.

### <a name="synchronizing-changes-with-xcode"></a>Sincronizar los cambios con Xcode

Cuando el desarrollador vuelva a Visual Studio para Mac de Xcode, todos los cambios que se hayan realizado en Xcode se sincronizarán de forma automática con el proyecto de Xamarin.Mac.

Seleccione **ViewController.designer.cs** en el **Explorador de soluciones** para ver cómo se han conectado la **salida** y la **acción** en el código de C#:

[![](hello-mac-images/sync01-sml.png "Sincronización de los cambios con Xcode")](hello-mac-images/sync01.png#lightbox)

Observe cómo las dos definiciones del archivo **ViewController.designer.cs**:

```csharp
[Outlet]
AppKit.NSTextField ClickedLabel { get; set; }

[Action ("ClickedButton:")]
partial void ClickedButton (Foundation.NSObject sender);
```

Se alinean con las definiciones del archivo `ViewController.h` en Xcode:

```objc
@property (assign) IBOutlet NSTextField *ClickedLabel;
- (IBAction)ClickedButton:(id)sender;
```

Visual Studio para Mac escucha los cambios del archivo **.h** y luego sincroniza de forma automática esos cambios en el archivo **.designer.cs** respectivo para exponerlos a la aplicación. Tenga en cuenta que **ViewController.designer.cs** es una clase parcial, por lo que Visual Studio para Mac no tiene que modificar ese **ViewController.cs** que sobrescribiría todos los cambios que haya realizado el desarrollador en la clase.

Normalmente, el desarrollador nunca tendrá que abrir **ViewController.designer.cs**, se ha presentado aquí solo con fines educativos.

> [!NOTE]
> En la mayoría de las situaciones, Visual Studio para Mac verá de forma automática todos los cambios realizados en Xcode y los sincronizará con el proyecto de Xamarin.Mac. En caso de que esa sincronización no se realice de forma automática, vuelva a Xcode y después vuelva a Visual Studio para Mac. Normalmente, esto iniciará un ciclo de sincronización.

## <a name="writing-the-code"></a>Escritura del código

Con la interfaz de usuario creada y sus elementos de interfaz de usuario expuestos al código mediante **salidas** y **acciones**, ya estamos preparados para escribir el código y darle vida al programa.

En el caso de esta aplicación de ejemplo, cada vez que se haga clic en el primer botón, la etiqueta se actualizará para mostrar cuántas veces se ha hecho clic en el botón. Para ello, haga doble clic en el archivo `ViewController.cs` en el **Explorador de soluciones** para editarlo:

[![](hello-mac-images/code01-sml.png "Consulta del archivo ViewController.cs en Visual Studio para Mac")](hello-mac-images/code01.png#lightbox)

En primer lugar, cree una variable de nivel de clase en la clase `ViewController` para realizar un seguimiento del número de clics que se han producido. Edite la definición de clase y dele un aspecto similar al siguiente:

```csharp
namespace Hello_Mac
{
    public partial class ViewController : NSViewController
    {
        private int numberOfTimesClicked = 0;
        ...
```

Después, en la misma clase (`ViewController`), invalide el método `ViewDidLoad` y agregue código para establecer el mensaje inicial de la etiqueta:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Set the initial value for the label
    ClickedLabel.StringValue = "Button has not been clicked yet.";
}
```

Use `ViewDidLoad`, en lugar de otro método como `Initialize`, porque se llama a `ViewDidLoad` *después* de que haya cargado el sistema operativo y se haya creado una instancia de la interfaz de usuario del archivo **.storyboard**. Si el desarrollador ha intentado tener acceso al control de etiqueta antes de que el archivo **.storyboard** haya cargado por completo y haya creado una instancia, obtendría un error `NullReferenceException` porque aún no existiría el control de etiqueta.

Después, agregue el código para responder al usuario que haga clic en el botón. Agregue el siguiente método parcial a la clase `ViewController`:

```csharp
partial void ClickedButton (Foundation.NSObject sender) {
    // Update counter and label
    ClickedLabel.StringValue = string.Format("The button has been clicked {0} time{1}.",++numberOfTimesClicked, (numberOfTimesClicked < 2) ? "" : "s");
}
```

Este código asocia la **acción** creada en Xcode e Interface Builder y se llamará siempre que el usuario haga clic en el botón.

## <a name="testing-the-application"></a>Probar la aplicación

Es el momento de compilar y ejecutar la aplicación para asegurarse de que se ejecuta según lo esperado. El desarrollador puede compilar y ejecutar todo en un paso, o puede compilarla sin ejecutarla.

Cada vez que se compila una aplicación, el desarrollador puede decidir qué tipo de compilación quiere:

- **Depuración**: una compilación de depuración se compila en un archivo **.app** (aplicación) con muchos metadatos adicionales que permiten al desarrollador depurar lo que sucede mientras se ejecuta la aplicación.
- **Versión**: una compilación de versión crea también un archivo **.app**, pero no incluye información de depuración, de modo que ocupa menos y se ejecuta más rápido.

El programador puede seleccionar el tipo de compilación desde el **Selector de configuración** en la esquina superior izquierda de la pantalla de Visual Studio para Mac:

[![](hello-mac-images/run01-sml.png "Selección de una compilación de depuración")](hello-mac-images/run01.png#lightbox)

## <a name="building-the-application"></a>Compilar la aplicación

En el caso de este ejemplo, queremos una compilación de depuración, así que asegúrese de que está seleccionada **Depuración**. Para compilar la aplicación, pulse **⌘B** o, en el menú **Compilación**, haga clic en **Compilar todo**.

Si no ha habido ningún error, se mostrará el mensaje **Compilación correcta** en la barra de estado de Visual Studio para Mac. Si hay algún error, revise el proyecto y asegúrese de que se han seguido correctamente los pasos anteriores. Empiece por confirmar que el código (tanto en Xcode como en Visual Studio para Mac) coincide con el código del tutorial.

## <a name="running-the-application"></a>Ejecutar la aplicación

Existen tres formas de ejecutar la aplicación:

- Presione **⌘+Entrar**.
- En el menú **Ejecutar**, elija **Depurar**.
- Haga clic en el botón **Reproducir** de la barra de herramientas de Visual Studio para Mac (justo encima del **Explorador de soluciones**).

La aplicación se compilará (si aún no se ha compilado), se iniciará en modo de depuración y mostrará la ventana de la interfaz principal:

[![](hello-mac-images/run02-sml.png "Ejecución de la aplicación")](hello-mac-images/run02.png#lightbox)

Si se hace clic varias veces en el botón, la etiqueta debería actualizarse con el recuento:

[![](hello-mac-images/run03-sml.png "Mostrar los resultados de hacer clic en el botón")](hello-mac-images/run03.png#lightbox)

## <a name="where-to-next"></a>Qué hacer después

Una vez aprendidos los conceptos básicos del trabajo con una aplicación de Xamarin.Mac, eche un vistazo a los siguientes documentos para obtener un mejor entendimiento:

- [Introduction to Storyboards](~/mac/platform/storyboards/index.md) (Introducción a los guiones gráficos): en este artículo, se proporciona una introducción al trabajo con guiones gráficos en una aplicación de Xamarin.Mac. En él, se describe cómo crear y mantener la interfaz de usuario de la aplicación mediante guiones gráficos e Interface Builder de Xcode.
- [Windows](~/mac/user-interface/window.md) (Ventanas): en este artículo, se explica cómo trabajar con ventanas y paneles en una aplicación de Xamarin.Mac. En él, se describe cómo crear y mantener ventanas y paneles en Xcode e Interface Builder, cargar ventanas y paneles de archivos .xib, usar ventanas y responder a ventanas en código de C#.
- [Dialogs](~/mac/user-interface/dialog.md) (Diálogos): en este artículo, se explica cómo trabajar con diálogos y ventanas modales en una aplicación de Xamarin.Mac. En él, se describe cómo crear y mantener ventanas modales en Xcode e Interface Builder, trabajar con cuadros de diálogo estándar, mostrar y responder a ventanas en código de C#.
- [Alerts](~/mac/user-interface/alert.md) (Alertas): en este artículo, se explica cómo trabajar con alertas en una aplicación de Xamarin.Mac. En él, se describe cómo crear y visualizar alertas desde el código de C# y cómo responder a las alertas.
- [Menús](~/mac/user-interface/menu.md): los menús se usan en varias partes de la interfaz de usuario de la aplicación de Mac; desde el menú principal de la aplicación en la parte superior de la pantalla hasta menús emergentes y contextuales que pueden aparecer en cualquier parte de una ventana. Los menús son una parte integral de la experiencia del usuario de la aplicación de Mac. En este artículo, se explica cómo trabajar con menús de Cocoa en una aplicación de Xamarin.Mac.
- [Toolbars](~/mac/user-interface/toolbar.md) (Barras de herramientas): en este artículo, se explica cómo trabajar con barras de herramientas en una aplicación de Xamarin.Mac. Se describe cómo crear y realizar el mantenimiento de las barras de herramientas en Interface Builder y Xcode, cómo exponer los elementos de la barra de herramientas al código mediante salidas y acciones, habilitar y deshabilitar los elementos de la barra de herramientas y finalmente responder a los elementos de la barra de herramientas en código de C#.
- [Table Views](~/mac/user-interface/table-view.md) (Vistas de tabla): en este artículo, se explica cómo trabajar con vistas de tabla en una aplicación de Xamarin.Mac. En él, se describe cómo crear y mantener vistas de tabla en Interface Builder y Xcode, cómo exponer los elementos de la vista de tabla al código mediante salidas y acciones, rellenar elementos de tabla y finalmente responder a los elementos de la vista de tabla en código de C#.
- [Outline Views](~/mac/user-interface/outline-view.md) (Vistas de esquema): en este artículo, se explica cómo trabajar con vistas de esquema en una aplicación de Xamarin.Mac. En él, se describe cómo crear y mantener vistas de esquema en Interface Builder y Xcode, cómo exponer los elementos de la vista de esquema al código mediante salidas y acciones, rellenar elementos de esquema y finalmente responder a los elementos de la vista de esquema en código de C#.
- [Source Lists](~/mac/user-interface/source-list.md) (Listas de origen): en este artículo, se explica cómo trabajar con listas de origen en una aplicación de Xamarin.Mac. En él, se describe cómo crear y mantener listas de origen en Interface Builder y Xcode, cómo exponer los elementos de las listas de origen al código mediante salidas y acciones, rellenar elementos de listas de origen y finalmente responder a los elementos de la lista de origen en código de C#.
- [Collection Views](~/mac/user-interface/collection-view.md) (Vistas de colección): en este artículo, se explica cómo trabajar con vistas de colección en una aplicación de Xamarin.Mac. En él, se describe cómo crear y mantener vistas de colección en Interface Builder y Xcode, cómo exponer los elementos de la vista de colección al código mediante salidas y acciones, rellenar vistas de colección y finalmente responder a las vistas de colección en código de C#.
- [Working with Images](~/mac/app-fundamentals/image.md) (Trabajar con imágenes): en este artículo, se explica cómo trabajar con imágenes e iconos en una aplicación de Xamarin.Mac. En él, se describe cómo crear y mantener las imágenes necesarias para crear el icono de una aplicación y usar imágenes tanto en código de C# como en Interface Builder de Xcode.

La [galería de ejemplos de Mac](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac) contiene ejemplos de código listos para usar para ayudar a obtener información de Xamarin.Mac.

Una aplicación de Xamarin.Mac completa que incluye muchas de las características que un usuario esperaría encontrar en una aplicación típica de Mac es la [aplicación de ejemplo de SourceWriter](https://docs.microsoft.com/samples/xamarin/mac-samples/sourcewriter). SourceWriter es un editor de código fuente simple que proporciona compatibilidad con la finalización de código y el resaltado de sintaxis simple.

El código de SourceWriter se ha comentado completamente y, si están disponibles, se han proporcionado vínculos de métodos o tecnologías clave a información relevante en la documentación de Xamarin.Mac.

## <a name="summary"></a>Resumen

En este artículo, se han tratado los conceptos básicos de una aplicación estándar de Xamarin.Mac. Se ha descrito cómo crear una aplicación en Visual Studio para Mac, diseñar la interfaz de usuario en Xcode e Interface Builder, exponer elementos de interfaz de usuario al código de C# mediante **salidas** y **acciones**, agregar código para que funcione con los elementos de interfaz de usuario y, por último, compilar y probar una aplicación de Xamarin.Mac.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Hello, Mac (ejemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/hello-mac)
- [Directrices de la interfaz humana de macOS](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)
