---
title: archivos. Xib en Xamarin. Mac
description: En este artículo se explica cómo trabajar con archivos. Xib creados en la Interface Builder de Xcode para crear y mantener interfaces de usuario para una aplicación de Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 6AF3D216-448D-4B2D-9026-74E4FFF5923A
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: f0889ecc428c595509fb23710bf3110c1bacad4e
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290288"
---
# <a name="xib-files-in-xamarinmac"></a>archivos. Xib en Xamarin. Mac

_En este artículo se explica cómo trabajar con archivos. Xib creados en la Interface Builder de Xcode para crear y mantener interfaces de usuario para una aplicación de Xamarin. Mac._

> [!NOTE]
> La manera preferida de crear una interfaz de usuario para una aplicación de Xamarin. Mac es con guiones gráficos. Esta documentación se ha dejado en su lugar por motivos históricos y para trabajar con proyectos de Xamarin. Mac anteriores. Para obtener más información, consulte la documentación [Introducción a los guiones gráficos](~/mac/platform/storyboards/index.md) .

## <a name="overview"></a>Información general

Al trabajar con C# y .net en una aplicación de Xamarin. Mac, tiene acceso a los mismos elementos de la interfaz de usuario y herramientas que un desarrollador que trabaja en *Objective-C* y *Xcode* . Como Xamarin. Mac se integra directamente con Xcode, puede usar los _Interface Builder_ de Xcode para crear y mantener las interfaces de usuario (o, opcionalmente, C# crearlas directamente en el código).

MacOS usa un archivo. Xib para definir los elementos de la interfaz de usuario de la aplicación (por ejemplo, menús, ventanas, vistas, etiquetas, campos de texto) que se crean y se mantienen gráficamente en la Interface Builder de Xcode.

[![Un ejemplo de la aplicación en ejecución](xib-images/intro01.png "Un ejemplo de la aplicación en ejecución")](xib-images/intro01-large.png#lightbox)

En este artículo, trataremos los aspectos básicos del trabajo con archivos. Xib en una aplicación de Xamarin. Mac. Se recomienda encarecidamente que trabaje primero en el artículo [Hello, Mac](~/mac/get-started/hello-mac.md) , ya que trata conceptos clave y técnicas que usaremos en este artículo.

Es posible que desee echar un vistazo a la [sección exponer C# clases y métodos a Objective-C](~/mac/internals/how-it-works.md) del documento [interno de Xamarin. Mac](~/mac/internals/how-it-works.md) , en él se explican los `Register` atributos y `Export` que se usan para conectar C# las clases a Objetos de Objective-C y elementos de la interfaz de usuario.


## <a name="introduction-to-xcode-and-interface-builder"></a>Introducción a Xcode y a Interface Builder

Como parte de Xcode, Apple ha creado una herramienta llamada Interface Builder, que permite crear visualmente la interfaz de usuario en un diseñador. Xamarin.Mac se integra con fluidez con el generador de interfaz, lo que le permite crear la interfaz de usuario con las mismas herramientas que lo hacen los usuarios de Objective-C de objetivo.


### <a name="components-of-xcode"></a>Componentes de Xcode

Al abrir un archivo. Xib en Xcode desde Visual Studio para Mac, se abre con un **navegador de proyectos** a la izquierda, la **jerarquía** de la interfaz y el editor de la **interfaz** en el centro, y una **propiedad &** la sección utilidades de la derecha:

[![Los componentes de la interfaz de usuario de Xcode](xib-images/xcode03.png "Los componentes de la interfaz de usuario de Xcode")](xib-images/xcode03-large.png#lightbox)

Echemos un vistazo a lo que hace cada una de estas secciones de Xcode y cómo las usará para crear la interfaz para la aplicación de Xamarin. Mac.


#### <a name="project-navigation"></a>Navegación del proyecto

Al abrir un archivo. Xib para editarlo en Xcode, Visual Studio para Mac crea un archivo de proyecto de Xcode en segundo plano para comunicar los cambios entre él y Xcode. Más adelante, cuando vuelva a Visual Studio para Mac de Xcode, los cambios realizados en este proyecto se sincronizarán con el proyecto de Xamarin. Mac Visual Studio para Mac.

La sección de **navegación del proyecto** le permite desplazarse por todos los archivos que componen este proyecto de Xcode de _correcciones de compatibilidad (Shim_ ). Normalmente, solo estará interesado en los archivos. Xib de esta lista, como **MainMenu. Xib** y **MainWindow. Xib**.


#### <a name="interface-hierarchy"></a>Jerarquía de la interfaz

La sección jerarquía de la **interfaz** permite tener acceso fácilmente a varias propiedades clave de la interfaz de usuario, como sus **marcadores de posición** y la **ventana**principal. También puede usar esta sección para obtener acceso a los elementos individuales (vistas) que componen la interfaz de usuario y ajustar la forma en que se anidan arrastrándolas dentro de la jerarquía.


#### <a name="interface-editor"></a>Editor de interfaces

La sección del editor de la **interfaz** proporciona la superficie en la que se va a diseñar gráficamente la interfaz de usuario. Arrastre los elementos desde la sección **biblioteca** de la sección **propiedades & Utilidades** para crear el diseño. A medida que se agregan elementos de la interfaz de usuario (vistas) a la superficie de diseño, se agregarán a la sección jerarquía de la **interfaz** en el orden en que aparecen en el editor de la **interfaz**.


#### <a name="properties--utilities"></a>Propiedades & Utilidades

Las **propiedades & sección utilidades** está dividida en dos secciones principales en las que se va a trabajar, **propiedades** (también denominadas inspectores) y la **biblioteca**:

![Inspector de propiedad](xib-images/xcode04.png "Inspector de propiedad")

Inicialmente, esta sección está casi vacía; sin embargo, si selecciona un elemento en el **Editor de interfaz** o en la jerarquía de la **interfaz**, la sección **propiedades** se rellenará con información sobre el elemento y las propiedades especificadas que puede ajustar.

En la sección **Propiedades** hay 8 *pestañas de inspectores* diferentes, como se muestra en la siguiente ilustración:

[![Información general de todos los inspectores](xib-images/xcode05.png "Información general de todos los inspectores")](xib-images/xcode05-large.png#lightbox)

De izquierda a derecha, estas pestañas son:

- **File Inspector** (Inspector de archivos): en esta pestaña se muestra la información del archivo, como el nombre de archivo y la ubicación del archivo Xib que se está editando.
- **Ayuda rápida**: en la pestaña Ayuda rápida se proporciona ayuda contextual según lo que está seleccionado en Xcode.
- **Identity Inspector** (Inspector de identidad): en esta pestaña se proporciona información sobre el control o la vista seleccionados.
- **Inspector de atributos** : el inspector de atributos permite personalizar varios atributos del control o vista seleccionados.
- **Inspector de tamaño** : el inspector de tamaño le permite controlar el tamaño y el comportamiento de cambio de tamaño del control o vista seleccionados.
- **Conexiones inspector** : el inspector de conexiones muestra las conexiones de salida y de acción de los controles seleccionados. Examinaremos las salidas y las acciones en un momento.
- **Inspector de enlaces** : el inspector de enlaces le permite configurar controles para que sus valores se enlacen automáticamente a los modelos de datos.
- **Inspector de efectos de vista** : el inspector de efectos de la vista permite especificar efectos en los controles, como animaciones.

En la sección **biblioteca** , puede buscar controles y objetos para colocarlos en el diseñador para crear gráficamente la interfaz de usuario:

![Ejemplo del inspector de biblioteca](xib-images/xcode06.png "Ejemplo del inspector de biblioteca")

Ahora que está familiarizado con el IDE de Xcode y Interface Builder, echemos un vistazo a su uso para crear una interfaz de usuario.


## <a name="creating-and-maintaining-windows-in-xcode"></a>Crear y mantener ventanas en Xcode

El método preferido para crear la interfaz de usuario de una aplicación de Xamarin. Mac es con guiones gráficos (consulte la documentación [Introducción a los guiones gráficos](~/mac/platform/storyboards/index.md) para obtener más información) y, como resultado, cualquier proyecto nuevo que se inicie en Xamarin. Mac usará guiones gráficos por predeterminada.

Para cambiar al uso de una interfaz de usuario basada en. Xib, haga lo siguiente:

1. Abra Visual Studio para Mac e inicie un nuevo proyecto de Xamarin. Mac.
2. En el **Panel de solución**, haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **nuevo archivo..** .
3. Seleccione**controlador de Windows** **Mac** > :

    ![Agregar un nuevo controlador de ventana](xib-images/setup00.png "Agregar un nuevo controlador de ventana")
4. Escriba `MainWindow` como nombre y haga clic en el botón **nuevo** :

    ![Agregar una nueva ventana principal](xib-images/setup01.png "Agregar una nueva ventana principal")
5. Vuelva a hacer clic con el botón derecho en el proyecto y seleccione **Agregar** > **nuevo archivo..** .
6. Seleccione el**menú principal**de **Mac** > :

    ![Agregar un nuevo menú principal](xib-images/setup02.png "Agregar un nuevo menú principal")
7. Deje el nombre como `MainMenu` y haga clic en el botón **nuevo** .
8. En el **Panel de solución** Seleccione el archivo **Main. Storyboard** , haga clic con el botón derecho y seleccione **quitar**:

    ![Seleccionar el guión gráfico principal](xib-images/setup03.png "Seleccionar el guión gráfico principal")
9. En el cuadro de diálogo quitar, haga clic en el botón **eliminar** :

    ![Confirmar la eliminación](xib-images/setup04.png "Confirmar la eliminación")
10. En el **Panel de solución**, haga doble clic en el archivo **info. plist** para abrirlo para su edición.
11. Seleccione `MainMenu` en la lista desplegable **interfaz principal** :

    [![Establecer el menú principal](xib-images/setup05.png "Establecer el menú principal")](xib-images/setup05-large.png#lightbox)
12. En el **Panel de solución**, haga doble clic en el archivo **MainMenu. Xib** para abrirlo para su edición en el Interface Builder de Xcode.
13. En el **Inspector de biblioteca**, `object` escriba en el campo de búsqueda y arrastre un nuevo **objeto** a la superficie de diseño:

    [![Edición del menú principal](xib-images/setup06.png "Edición del menú principal")](xib-images/setup06-large.png#lightbox)
14. En el **Inspector de identidad**, `AppDelegate` escriba para la **clase**:

    [![Seleccionar el delegado de la aplicación](xib-images/setup07.png "Seleccionar el delegado de la aplicación")](xib-images/setup07-large.png#lightbox)
15. Seleccione el **propietario del archivo** en la jerarquía de la **interfaz**, cambie al **Inspector de conexión** y arrastre una línea del delegado `AppDelegate` hasta el **objeto** que acaba de agregar al proyecto:

    [![Conectar el delegado de la aplicación](xib-images/setup08.png "Conectar el delegado de la aplicación")](xib-images/setup08-large.png#lightbox)
16. Guarde los cambios y vuelva a Visual Studio para Mac.

Con todos estos cambios en su lugar, edite el archivo **AppDelegate.CS** y haga que tenga un aspecto similar al siguiente:

```csharp
using AppKit;
using Foundation;

namespace MacXib
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        public MainWindowController mainWindowController { get; set; }

        public AppDelegate ()
        {
        }

        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
            mainWindowController = new MainWindowController ();
            mainWindowController.Window.MakeKeyAndOrderFront (this);
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
    }
}
```

Ahora la ventana principal de la aplicación se define en un archivo **. Xib** que se incluye automáticamente en el proyecto al agregar un controlador de ventana. Para editar el diseño de Windows, en el **Panel de solución**, haga doble clic en el archivo **MainWindow. Xib** :

![Seleccionar el archivo MainWindow. Xib](xib-images/edit01.png "Seleccionar el archivo MainWindow. Xib")

Se abrirá el diseño de ventana en la Interface Builder de Xcode:

[![Editar MainWindow. Xib](xib-images/edit02.png "Editar MainWindow. Xib")](xib-images/edit02-large.png#lightbox)


### <a name="standard-window-workflow"></a>Flujo de trabajo de ventana estándar

En el caso de las ventanas creadas y con las que trabaja en la aplicación de Xamarin. Mac, el proceso es básicamente el mismo:

1. En el caso de las nuevas ventanas que no son el valor predeterminado que se agrega automáticamente al proyecto, agregue una nueva definición de ventana al proyecto.
2. Haga doble clic en el archivo. Xib para abrir el diseño de ventana para su edición en el Interface Builder de Xcode.
3. Establezca las propiedades de ventana necesarias en el **Inspector de atributos** y el inspector de **tamaño**.
4. Arrastre los controles necesarios para compilar la interfaz y configurarlos en el **Inspector de atributos**.
5. Use el **Inspector de tamaño** para controlar el cambio de tamaño de los elementos de la interfaz de usuario.
6. Exponga los elementos de la interfaz de C# usuario de la ventana al código mediante salidas y acciones.
7. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.


### <a name="designing-a-window-layout"></a>Diseñar un diseño de ventana

El proceso para diseñar una interfaz de usuario en Interface Builder es básicamente el mismo para cada elemento que se agrega:

1. Busque el control deseado en el **Inspector de biblioteca** y arrástrelo al editor de la **interfaz** y colóquelo.
2. Establezca las propiedades de ventana necesarias en el **Inspector de atributos**.
3. Use el **Inspector de tamaño** para controlar el cambio de tamaño de los elementos de la interfaz de usuario.
4. Si usa una clase personalizada, establézcala en el **Inspector de identidad**.
5. Exponga los elementos de la C# interfaz de usuario al código mediante salidas y acciones.
6. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Por ejemplo:

1. En Xcode, arrastre un **botón de comando** de la **sección Biblioteca**:

    [![Seleccionar un botón de la biblioteca](xib-images/xcode07.png "Seleccionar un botón de la biblioteca")](xib-images/xcode07-large.png#lightbox)
2. Coloque el botón en la **ventana** del editor de la **interfaz**:

    [![Agregar un botón a la ventana](xib-images/xcode08.png "Agregar un botón a la ventana")](xib-images/xcode08-large.png#lightbox)
3. Haga clic en la propiedad **Título** del **Attribute Inspector** (Inspector de atributos) y cambie el título del botón por `Click Me`:

    ![Establecer los atributos del botón](xib-images/xcode09.png "Establecer los atributos del botón")
4. Arrastre una **Etiqueta** de la **sección Biblioteca**:

    [![Selección de una etiqueta en la biblioteca](xib-images/xcode10.png "Selección de una etiqueta en la biblioteca")](xib-images/xcode10-large.png#lightbox)
5. Coloque la etiqueta en la **Ventana** situada junto al botón en el **Interface Editor** (Editor de la interfaz):

    [![Agregar una etiqueta a la ventana](xib-images/xcode11.png "Agregar una etiqueta a la ventana")](xib-images/xcode11-large.png#lightbox)
6. Agarre el controlador derecho de la etiqueta y arrástrelo hasta que esté cerca del borde de la ventana:

    [![Cambiar el tamaño de la etiqueta](xib-images/xcode12.png "Cambiar el tamaño de la etiqueta")](xib-images/xcode12-large.png#lightbox)
7. Con la etiqueta aún seleccionada en el **Editor**de la interfaz, cambie al **Inspector de tamaño**:

    ![Seleccionar el inspector de tamaño](xib-images/xcode13.png "Seleccionar el inspector de tamaño")
8. En el **cuadro ajuste** automático de tamaño, haga clic en el **corchete rojo atenuado** de la derecha y la **flecha atenuar horizontalmente** en el centro:

    ![Editar las propiedades de ajuste automático de tamaño](xib-images/xcode14.png "Editar las propiedades de ajuste automático de tamaño")
9. Esto garantiza que la etiqueta se ajustará para aumentar y reducirse cuando se cambie el tamaño de la ventana en la aplicación en ejecución. Los **corchetes rojos** y los de la parte superior e izquierda del cuadro de **autoajuste de tamaño** indican a la etiqueta que se bloquee en las ubicaciones X e y dadas.
10. Guardar los cambios en la interfaz de usuario

A medida que va a cambiar el tamaño y mover los controles, debe haber observado que Interface Builder proporciona sugerencias de ajuste útiles basadas en las [directrices de la interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). Estas instrucciones le ayudarán a crear aplicaciones de alta calidad que tendrán una apariencia familiar para los usuarios de Mac.

Si observa la sección jerarquía de la **interfaz** , observe cómo se muestra el diseño y la jerarquía de los elementos que componen la interfaz de usuario:

![Seleccionar un elemento en la jerarquía de la interfaz](xib-images/xcode15.png "Seleccionar un elemento en la jerarquía de la interfaz")

Aquí puede seleccionar los elementos que desea editar o arrastrar para reordenar los elementos de la interfaz de usuario si es necesario. Por ejemplo, si un elemento de la interfaz de usuario estaba incluido en otro elemento, puede arrastrarlo a la parte inferior de la lista para convertirlo en el elemento de nivel superior de la ventana.

Para obtener más información sobre cómo trabajar con Windows en una aplicación de Xamarin. Mac, consulte nuestra documentación de [Windows](~/mac/user-interface/window.md) .


## <a name="exposing-ui-elements-to-c-code"></a>Exponer elementos de la interfaz C# de usuario al código

Una vez que haya terminado de diseñar la apariencia de la interfaz de usuario en Interface Builder, deberá exponer los elementos de la interfaz de usuario para que se pueda tener acceso a ellos C# desde el código. Para ello, utilizará acciones y salidas.


### <a name="setting-a-custom-main-window-controller"></a>Establecer un controlador de ventana principal personalizado

Para poder crear salidas y acciones para exponer elementos de la interfaz de usuario C# al código, la aplicación de Xamarin. Mac tendrá que usar un controlador de ventana personalizado.

Haga lo siguiente:

1. Abra el guión gráfico de la aplicación en la Interface Builder de Xcode.
2. `NSWindowController` Seleccione en el superficie de diseño.
3. Cambie a la vista **Inspector de identidad** y `WindowController` escriba como **nombre de clase**:

    [![Editar el nombre de clase](xib-images/windowcontroller01.png "Editar el nombre de clase")](xib-images/windowcontroller01-large.png#lightbox)
4. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos.
5. Se agregará un archivo **WindowController.CS** al proyecto en el **Panel de solución** en Visual Studio para Mac:

    ![Nombre de la nueva clase en Visual Studio para Mac](xib-images/windowcontroller02.png "Nombre de la nueva clase en Visual Studio para Mac")
6. Vuelva a abrir el guion gráfico en la Interface Builder de Xcode.
7. El archivo **WindowController. h** estará disponible para su uso:

    [![El archivo. h coincidente en Xcode](xib-images/windowcontroller03.png "El archivo. h coincidente en Xcode")](xib-images/windowcontroller03-large.png#lightbox)


### <a name="outlets-and-actions"></a>Salidas y acciones

¿Qué son las salidas y las acciones? En la programación tradicional de interfaz de usuario de .NET, un control de la interfaz de usuario se expone de forma automática como una propiedad cuando se agrega. En Mac es algo diferente, al agregar simplemente un control a una vista, el código no puede obtener acceso a él. El desarrollador debe exponer de forma explícita el elemento de interfaz de usuario al código. En este orden, Apple nos ofrece dos opciones:

- **Salidas**: las salidas son análogas a las propiedades. Si conecta un control a una salida, se expone al código a través de una propiedad, por lo que puede hacer cosas como adjuntar controladores de eventos, llamar a métodos en él, etc.
- **Acciones**: las acciones son análogas al patrón de comando en WPF. Por ejemplo, cuando se realiza una acción en un control, por ejemplo, un clic en un botón, el control llamará automáticamente a un método en el código. Las acciones son eficaces y prácticas porque puede conectar muchos controles a la misma acción.

En Xcode, las salidas y las acciones se agregan directamente en el código a través del *arrastre de control*. Más concretamente, esto significa que para crear una salida o acción, elija el elemento de control al que desea agregar una salida o acción, mantenga presionado el botón de **control** del teclado y arrastre el control directamente a su código.

Para los desarrolladores de Xamarin. Mac, esto significa que arrastra a los archivos de código auxiliar de Objective-C C# que se corresponden con el archivo en el que desea crear la salida o acción. Visual Studio para Mac creó un archivo llamado **MainWindow. h** como parte del proyecto de Xcode de correcciones de compatibilidad (shim) que ha generado para usar el Interface Builder:

[![Ejemplo de un archivo. h en Xcode](xib-images/xcode16.png "Ejemplo de un archivo. h en Xcode")](xib-images/xcode16-large.png#lightbox)

Este archivo stub. h refleja el **MainWindow.Designer.CS** que se agrega automáticamente a un proyecto de Xamarin. Mac cuando se `NSWindow` crea un nuevo. Este archivo se utilizará para sincronizar los cambios realizados por Interface Builder y es donde se crearán las salidas y acciones para que los elementos de la interfaz C# de usuario se expongan al código.


#### <a name="adding-an-outlet"></a>Agregar una salida

Con una comprensión básica de qué son las salidas y las acciones, echemos un vistazo a la creación de una salida para exponer un C# elemento de la interfaz de usuario al código.

Haga lo siguiente:

1. En Xcode, en la esquina superior disponible más a la derecha de la pantalla, haga clic en el botón de **doble círculo** para abrir **Assistant Editor** (Editor del asistente):

    [![Seleccionar el editor del asistente](xib-images/outlet01.png "Seleccionar el editor del asistente")](xib-images/outlet01-large.png#lightbox)
2. Xcode cambiará a un modo de vista en dos paneles con el **Interface Editor** (Editor de la interfaz) en un lado y un **Editor de código** en el otro.
3. Tenga en cuenta que Xcode ha seleccionado automáticamente el archivo **MainWindowController. m** en el **Editor de código**, que es incorrecto. Si recuerda que en nuestro debate sobre qué salidas y acciones están anteriores, es necesario seleccionar **MainWindow. h** .
4. En la parte superior del **Editor de código** , haga clic en el **vínculo automático** y seleccione el archivo **MainWindow. h** :

    [![Seleccionar el archivo. h correcto](xib-images/outlet02.png "Seleccionar el archivo. h correcto")](xib-images/outlet02-large.png#lightbox)
5. Ahora, Xcode debería tener el archivo correcto seleccionado:

    [![El archivo correcto seleccionado](xib-images/outlet03.png "El archivo correcto seleccionado")](xib-images/outlet03-large.png#lightbox)
6. **El último paso es muy importante.** Si no tiene seleccionado el archivo correcto, no podrá crear salidas ni acciones, ya que se expondrán a la clase equivocada en C#.
7. En el **Editor**de la interfaz, mantenga presionada la tecla **control** del teclado y haga clic y arrastre la etiqueta que hemos creado anteriormente en el editor `@interface MainWindow : NSWindow { }` de código justo debajo del código:

    [![Arrastrar para crear una nueva salida](xib-images/outlet04.png "Arrastrar para crear una nueva salida")](xib-images/outlet04-large.png#lightbox)
8. Aparecerá un cuadro de diálogo. Deje la **conexión** establecida en salida y escriba `ClickedLabel` para el **nombre**:

    [![Establecimiento de las propiedades de la toma](xib-images/outlet05.png "Establecimiento de las propiedades de la toma")](xib-images/outlet05-large.png#lightbox)
9. Haga clic en el botón **conectar** para crear la salida:

    ![La salida completada](xib-images/outlet06.png "La salida completada")
10. Guarde los cambios en el archivo.


#### <a name="adding-an-action"></a>Agregar una acción

A continuación, echemos un vistazo a la creación de una acción para exponer una interacción del usuario C# con el elemento de la interfaz de usuario en el código.

Haga lo siguiente:

1. Asegúrese de que todavía está en el **Editor de asistentes** y que el archivo **MainWindow. h** está visible en el **Editor de código**.
2. En el **Editor**de la interfaz, mantenga presionada la tecla **control** del teclado y haga clic y arrastre el botón que hemos creado anteriormente al editor de `@property (assign) IBOutlet NSTextField *ClickedLabel;` código justo debajo del código:

    [![Arrastrar para crear una acción](xib-images/action01.png "Arrastrar para crear una acción")](xib-images/action01-large.png#lightbox)
3. Cambie el tipo de **conexión** a acción:

    [![Seleccionar un tipo de acción](xib-images/action02.png "Seleccionar un tipo de acción")](xib-images/action02-large.png#lightbox)
4. Escriba `ClickedButton` como **Nombre**:

    [![Configuración de la acción](xib-images/action03.png "Configuración de la acción")](xib-images/action03-large.png#lightbox)
5. Haga clic en el botón **conectar** para crear una acción:

    ![La acción completada](xib-images/action04.png "La acción completada")
6. Guarde los cambios en el archivo.

Con la interfaz de usuario conectada y expuesta al C# código, vuelva a Visual Studio para Mac y deje que sincronice los cambios de Xcode y Interface Builder.


### <a name="writing-the-code"></a>Escribir el código

Con la interfaz de usuario creada y sus elementos de interfaz de usuario expuestos al código mediante salidas y acciones, está listo para escribir el código para que el programa cobre vida. Por ejemplo, abra el archivo **MainWindow.CS** para editarlo; para ello, haga doble clic en el **Panel de solución**:

[![El archivo MainWindow.CS](xib-images/code01.png "El archivo MainWindow.CS")](xib-images/code01-large.png#lightbox)

Y agregue el código siguiente a la `MainWindow` clase para trabajar con la salida de ejemplo que creó anteriormente:

```csharp
private int numberOfTimesClicked = 0;
...

public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Set the initial value for the label
    ClickedLabel.StringValue = "Button has not been clicked yet.";
}
```

Tenga en cuenta `NSLabel` que se obtiene acceso C# a en mediante el nombre directo que le asignó en Xcode cuando creó su salida en Xcode, en este caso, se llama `ClickedLabel`. Puede tener acceso a cualquier método o propiedad del objeto expuesto del mismo modo que lo haría con C# cualquier clase normal.

> [!IMPORTANT]
> Debe utilizar `AwakeFromNib`, en lugar de otro método `Initialize`como, porque `AwakeFromNib` se llama a _después_ de que el sistema operativo haya cargado y creado una instancia de la interfaz de usuario desde el archivo. Xib. Si ha intentado tener acceso al control de etiqueta antes de que el archivo. Xib se haya cargado completamente y se haya creado una `NullReferenceException` instancia, se producirá un error porque el control etiqueta no se crearía todavía.

A continuación, agregue la siguiente clase parcial a `MainWindow` la clase:

```csharp
partial void ClickedButton (Foundation.NSObject sender) {

    // Update counter and label
    ClickedLabel.StringValue = string.Format("The button has been clicked {0} time{1}.",++numberOfTimesClicked, (numberOfTimesClicked < 2) ? "" : "s");
}
```

Este código se asocia a la acción que creó en Xcode y Interface Builder y se llamará cada vez que el usuario haga clic en el botón.

Algunos elementos de la interfaz de usuario tienen automáticamente acciones integradas, por ejemplo, elementos en la barra de menús predeterminada, como el elemento de`openDocument:`menú **abrir...** (). En el **Panel de solución**, haga doble clic en el archivo **AppDelegate.CS** para abrirlo para su edición y agregue el código siguiente `DidFinishLaunching` debajo del método:

```csharp
[Export ("openDocument:")]
void OpenDialog (NSObject sender)
{
    var dlg = NSOpenPanel.OpenPanel;
    dlg.CanChooseFiles = false;
    dlg.CanChooseDirectories = true;

    if (dlg.RunModal () == 1) {
        var alert = new NSAlert () {
            AlertStyle = NSAlertStyle.Informational,
            InformativeText = "At this point we should do something with the folder that the user just selected in the Open File Dialog box...",
            MessageText = "Folder Selected"
        };
        alert.RunModal ();
    }
}
```

La línea clave aquí es `[Export ("openDocument:")]`, indica `NSMenu` que **AppDelegate** tiene un método `void OpenDialog (NSObject sender)` que responde a la `openDocument:` acción.

Para obtener más información sobre cómo trabajar con menús, vea la documentación de los [menús](~/mac/user-interface/menu.md) .


## <a name="synchronizing-changes-with-xcode"></a>Sincronizar cambios con Xcode

Al volver a Visual Studio para Mac de Xcode, los cambios que haya realizado en Xcode se sincronizarán automáticamente con el proyecto de Xamarin. Mac.

Si selecciona el **MainWindow.Designer.CS** en el **Panel de solución** podrá ver cómo se ha conectado la salida y la acción en nuestro C# código:

[![Sincronizar cambios con Xcode](xib-images/sync01.png "Sincronizar cambios con Xcode")](xib-images/sync01-large.png#lightbox)

Observe cómo las dos definiciones en el archivo **MainWindow.Designer.CS** :

```csharp
[Outlet]
AppKit.NSTextField ClickedLabel { get; set; }

[Action ("ClickedButton:")]
partial void ClickedButton (Foundation.NSObject sender);
```

Alinear con las definiciones del archivo **MainWindow. h** en Xcode:

```objc
@property (assign) IBOutlet NSTextField *ClickedLabel;
- (IBAction)ClickedButton:(id)sender;
```

Como puede ver, Visual Studio para Mac escucha los cambios en el archivo. h y, a continuación, sincroniza automáticamente los cambios en el archivo **. Designer.CS** respectivo para exponerlos a la aplicación. También puede observar que **MainWindow.Designer.CS** es una clase parcial, por lo que Visual Studio para Mac no tiene que modificar **MainWindow.CS** , lo que sobrescribirá los cambios que se hayan realizado en la clase.

Normalmente, nunca tendrá que abrir el **MainWindow.Designer.CS** usted mismo, ya que se presentó aquí solo con fines educativos.

> [!IMPORTANT]
> En la mayoría de los casos, Visual Studio para Mac verá automáticamente los cambios realizados en Xcode y los sincronizará con el proyecto de Xamarin. Mac. En caso de que esa sincronización no se realice de forma automática, vuelva a Xcode y después vuelva a Visual Studio para Mac. Normalmente, esto iniciará un ciclo de sincronización.


## <a name="adding-a-new-window-to-a-project"></a>Agregar una nueva ventana a un proyecto

Además de la ventana principal del documento, es posible que una aplicación de Xamarin. Mac necesite Mostrar otros tipos de ventanas al usuario, como preferencias o paneles del inspector. Al agregar una nueva ventana al proyecto, siempre debe usar la **ventana de Cocoa con** la opción Controller, ya que esto facilita el proceso de carga de la ventana desde el archivo. Xib.

Para agregar una nueva ventana, haga lo siguiente:

1. En el **Panel de solución**, haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **nuevo archivo..** .
2. En el cuadro de diálogo nuevo archivo, seleccione la**ventana de cacao de** **Xamarin. Mac** > con el controlador:

    ![Agregar un nuevo controlador de ventana](xib-images/new01.png "Agregar un nuevo controlador de ventana")
3. Escriba `PreferencesWindow` para el **Nombre** y haga clic en el botón **Nuevo**.
4. Haga doble clic en el archivo **PreferencesWindow. Xib** para abrirlo y editarlo en Interface Builder:

    [![Edición de la ventana en Xcode](xib-images/new02.png "Edición de la ventana en Xcode")](xib-images/new02-large.png#lightbox)
5. Diseñe la interfaz:

    [![Diseñar el diseño de Windows](xib-images/new03.png "Diseñar el diseño de Windows")](xib-images/new03-large.png#lightbox)
6. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Agregue el código siguiente a **AppDelegate.CS** para mostrar la nueva ventana:

```csharp
[Export("applicationPreferences:")]
void ShowPreferences (NSObject sender)
{
    var preferences = new PreferencesWindowController ();
    preferences.Window.MakeKeyAndOrderFront (this);
}
```

La `var preferences = new PreferencesWindowController ();` línea crea una nueva instancia del controlador de ventana que carga la ventana del archivo. Xib y la aumenta. La `preferences.Window.MakeKeyAndOrderFront (this);` línea muestra la nueva ventana al usuario.

Si ejecuta el código y selecciona las **preferencias...** en el menú de la **aplicación**, se mostrará la ventana:

![Ejecutar la aplicación de ejemplo](xib-images/new04.png "Ejecutar la aplicación de ejemplo")

Para obtener más información sobre cómo trabajar con Windows en una aplicación de Xamarin. Mac, consulte nuestra documentación de [Windows](~/mac/user-interface/window.md) .


## <a name="adding-a-new-view-to-a-project"></a>Agregar una nueva vista a un proyecto

Hay ocasiones en las que es más fácil dividir el diseño de la ventana en varios archivos. Xib que se pueden administrar. Por ejemplo, como desactivar el contenido de la ventana principal al seleccionar un elemento de la barra de herramientas en una **ventana de preferencias** o cambiar el contenido en respuesta a una selección de la **lista de origen** .

Al agregar una nueva vista al proyecto, debe usar siempre la opción de **Cocoe view with Controller** , ya que esto facilita el proceso de carga de la vista desde el archivo. Xib.

Para agregar una nueva vista, haga lo siguiente:

1. En el **Panel de solución**, haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **nuevo archivo..** .
2. En el cuadro de diálogo nuevo archivo, seleccione **Xamarin. Mac** > **cacao View con controlador**:

    ![Agregar una nueva vista](xib-images/view01.png "Agregar una nueva vista")
3. Escriba `SubviewTable` para el **Nombre** y haga clic en el botón **Nuevo**.
4. Haga doble clic en el archivo **SubviewTable. Xib** para abrirlo y editarlo en Interface Builder y diseñar la interfaz de usuario:

    [![Diseño de la nueva vista en Xcode](xib-images/view02.png "Diseño de la nueva vista en Xcode")](xib-images/view02-large.png#lightbox)
5. Conecte todas las acciones y salidas necesarias.
6. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Después, edite **SubviewTable.CS** y agregue el código siguiente al archivo **AwakeFromNib** para rellenar la nueva vista cuando se carga:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create the Product Table Data Source and populate it
    var DataSource = new ProductTableDataSource ();
    DataSource.Products.Add (new Product ("Xamarin.iOS", "Allows you to develop native iOS Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Android", "Allows you to develop native Android Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Mac", "Allows you to develop Mac native Applications in C#"));
    DataSource.Sort ("Title", true);

    // Populate the Product Table
    ProductTable.DataSource = DataSource;
    ProductTable.Delegate = new ProductTableDelegate (DataSource);

    // Auto select the first row
    ProductTable.SelectRow (0, false);
}
```

Agregue una enumeración al proyecto para realizar el seguimiento de la vista que se está mostrando actualmente. Por ejemplo, **SubviewType.CS**:

```csharp
public enum SubviewType
{
    None,
    TableView,
    OutlineView,
    ImageView
}
```

Edite el archivo. Xib de la ventana que va a consumir la vista y mostrándola. Agregue una **vista personalizada** que actuará como contenedor de la vista una vez que se carga en la memoria C# mediante código y se expone a una salida `ViewContainer`llamada:

[![Crear la salida necesaria](xib-images/view03.png "Crear la salida necesaria")](xib-images/view03-large.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Después, edite el archivo. CS de la ventana que mostrará la nueva vista (por ejemplo, **MainWindow.CS**) y agregue el código siguiente:

```csharp
private SubviewType ViewType = SubviewType.None;
private NSViewController SubviewController = null;
private NSView Subview = null;
...

private void DisplaySubview(NSViewController controller, SubviewType type) {

    // Is this view already displayed?
    if (ViewType == type) return;

    // Is there a view already being displayed?
    if (Subview != null) {
        // Yes, remove it from the view
        Subview.RemoveFromSuperview ();

        // Release memory
        Subview = null;
        SubviewController = null;
    }

    // Save values
    ViewType = type;
    SubviewController = controller;
    Subview = controller.View;

    // Define frame and display
    Subview.Frame = new CGRect (0, 0, ViewContainer.Frame.Width, ViewContainer.Frame.Height);
    ViewContainer.AddSubview (Subview);
}
```

Cuando es necesario mostrar una nueva vista cargada desde un archivo. Xib en el contenedor de la ventana (la **vista personalizada** agregada anteriormente), este código controla la eliminación de cualquier vista existente y su intercambio para el nuevo. Parece ver que ya tiene una vista mostrada, si es así, la quita de la pantalla. A continuación, se toma la vista que se ha pasado (tal y como se cargó desde un controlador de vista) y se cambia su tamaño para que quepa en el área de contenido y se agrega al contenido que se va a mostrar.

Para mostrar una nueva vista, use el código siguiente:

```csharp
DisplaySubview(new SubviewTableController(), SubviewType.TableView);
```

Esto crea una nueva instancia del controlador de vista para que se muestre la nueva vista, establece su tipo (tal y como se especifica en la enumeración agregada al proyecto `DisplaySubview` ) y usa el método agregado a la clase de la ventana para mostrar realmente la vista. Por ejemplo:

[![Ejecutar la aplicación de ejemplo](xib-images/view04.png "Ejecutar la aplicación de ejemplo")](xib-images/view04-large.png#lightbox)

Para obtener más información sobre cómo trabajar con Windows en una aplicación de Xamarin. Mac, consulte nuestra documentación sobre [cuadros de diálogo](~/mac/user-interface/dialog.md) y [ventanas](~/mac/user-interface/window.md) .


## <a name="summary"></a>Resumen

En este artículo se ha realizado una visión detallada del trabajo con archivos. Xib en una aplicación de Xamarin. Mac. Vimos los distintos tipos y usos de los archivos. Xib para crear la interfaz de usuario de la aplicación, cómo crear y mantener archivos. Xib en la Interface Builder de Xcode y cómo trabajar con archivos. Xib C# en el código.


## <a name="related-links"></a>Vínculos relacionados

- [MacImages (ejemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/macimages)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [Menús](~/mac/user-interface/menu.md)
- [Cuadros de diálogo](~/mac/user-interface/dialog.md)
- [Trabajar con imágenes](~/mac/app-fundamentals/image.md)
- [Directrices de la interfaz humana de macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
