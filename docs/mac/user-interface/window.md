---
title: Windows en Xamarin. Mac
description: En este artículo se explica cómo trabajar con ventanas y paneles en una aplicación de Xamarin. Mac. Describe cómo crear ventanas y paneles en Xcode y Interface Builder, cargarlos desde guiones gráficos y archivos. Xib, y trabajar con ellos mediante programación.
ms.prod: xamarin
ms.assetid: 4F6C67E9-BBFF-44F7-B29E-AB47D7F44287
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: df623efcc1da617ac6b700b42d3ac058dea817ca
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772650"
---
# <a name="windows-in-xamarinmac"></a>Windows en Xamarin. Mac

_En este artículo se explica cómo trabajar con ventanas y paneles en una aplicación de Xamarin. Mac. Describe cómo crear ventanas y paneles en Xcode y Interface Builder, cargarlos desde guiones gráficos y archivos. Xib, y trabajar con ellos mediante programación._

Al trabajar con C# y .net en una aplicación de Xamarin. Mac, tiene acceso a las mismas ventanas y paneles que un desarrollador que trabaja en *Objective-C* y *Xcode* . Dado que Xamarin. Mac se integra directamente con Xcode, puede usar los _Interface Builder_ de Xcode para crear y mantener sus ventanas y paneles (o, opcionalmente, C# crearlos directamente en el código).

En función de su finalidad, una aplicación de Xamarin. Mac puede presentar una o más ventanas en pantalla para administrar y coordinar la información que muestra y con la que trabaja. Las funciones principales de una ventana son:

1. Para proporcionar un área en la que se puedan colocar y administrar las vistas y los controles.
2. Aceptar y responder a eventos en respuesta a la interacción del usuario con el teclado y el mouse.

Windows se puede usar en un estado no modal (por ejemplo, un editor de texto que puede tener varios documentos abiertos a la vez) o modal (como un cuadro de diálogo de exportación que se debe descartar para que la aplicación pueda continuar).

Los paneles son un tipo especial de ventana (una subclase de la `NSWindow` clase base), que normalmente atiende a una función auxiliar en una aplicación, como las ventanas de la utilidad, como los inspectores de formato de texto y el selector de color del sistema.

[![](window-images/intro01.png "Edición de una ventana en Xcode")](window-images/intro01.png#lightbox)

En este artículo, trataremos los aspectos básicos del trabajo con ventanas y paneles en una aplicación de Xamarin. Mac. Se recomienda encarecidamente que trabaje primero en el artículo [Hello, Mac](~/mac/get-started/hello-mac.md) , específicamente en las secciones [Introducción a Xcode y Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) , ya que trata conceptos clave y técnicas que usaremos en Este artículo.

Es posible que desee echar un vistazo a la sección [exposición C# de clases y métodos a Objective-C](~/mac/internals/how-it-works.md) del documento [interno de Xamarin. Mac](~/mac/internals/how-it-works.md) también, en él se explican `Export` los `Register` comandos y que se usan para C# conectar las clases a Objetos de Objective-C y elementos de la interfaz de usuario.

<a name="Introduction_to_Windows" />

## <a name="introduction-to-windows"></a>Introducción a Windows

Como se indicó anteriormente, una ventana proporciona un área en la que se pueden colocar y administrar vistas y controles, y responde a eventos en función de la interacción del usuario (ya sea mediante el teclado o el mouse).

Según Apple, hay cinco tipos principales de ventanas en una aplicación macOS:

- **Ventana de documento** : una ventana de documento contiene datos de usuario basados en archivos, como una hoja de cálculo o un documento de texto.
- **Ventana** de la aplicación: una ventana de la aplicación es la ventana principal de una aplicación que no está basada en documentos (como la aplicación de calendario en un equipo Mac).
- **Panel** : un panel flota sobre otras ventanas y proporciona herramientas o controles con los que los usuarios pueden trabajar mientras los documentos están abiertos. En algunos casos, un panel puede ser traslúcido (por ejemplo, al trabajar con gráficos de gran tamaño).
- **Cuadro de diálogo** : aparece un cuadro de diálogo en respuesta a una acción del usuario y, normalmente, proporciona maneras en que los usuarios pueden completar la acción. Un cuadro de diálogo requiere una respuesta del usuario antes de que se pueda cerrar. (Consulte [trabajar con cuadros de diálogo](~/mac/user-interface/dialog.md))
- **Alertas** : una alerta es un tipo especial de cuadro de diálogo que aparece cuando se produce un problema grave (como un error) o como una advertencia (como preparar la eliminación de un archivo). Dado que una alerta es un diálogo, también requiere una respuesta de usuario antes de que se pueda cerrar. (Vea [trabajar con alertas](~/mac/user-interface/alert.md))

Para obtener más información, consulte la sección [About Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) de las directrices de la interfaz de usuario de Apple [OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Main_Key_and_Inactive_Windows" />

### <a name="main-key-and-inactive-windows"></a>Principales, clave y ventanas inactivas

Las ventanas de una aplicación de Xamarin. Mac pueden verse y comportarse de manera diferente en función de cómo el usuario interactúe actualmente con ellas. La ventana de documento o aplicación más importante que se centra actualmente en la atención del usuario se denomina _ventana principal_. En la mayoría de los casos, esta ventana también será la _ventana clave_ (la ventana que actualmente acepta datos proporcionados por el usuario). Pero esto no es siempre el caso, por ejemplo, un selector de colores podría estar abierto y ser la ventana de clave con la que interactúa el usuario para cambiar el estado de un elemento en la ventana de documento (que seguirá siendo la ventana principal).

Las ventanas principal y de clave (si son independientes) siempre están activas, las ventanas _inactivas_ son ventanas abiertas que no están en primer plano. Por ejemplo, una aplicación de editor de texto podría tener más de un documento abierto a la vez, solo la ventana principal estaba activa, todas las demás serían inactivas. 

Para obtener más información, consulte la sección [About Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) de las directrices de la interfaz de usuario de Apple [OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Naming_Windows" />

### <a name="naming-windows"></a>Asignar nombres a ventanas

Una ventana puede mostrar una barra de título y, cuando se muestra el título, suele ser el nombre de la aplicación, el nombre del documento en el que se está trabajando o la función de la ventana (por ejemplo, inspector). Algunas aplicaciones no muestran una barra de título porque son reconocibles por la visión y no funcionan con documentos.

Apple sugiere las siguientes directrices:

- Use el nombre de la aplicación como título de una ventana principal que no sea de documento. 
- Asignar un nombre a una `untitled`nueva ventana de documento. En el primer documento nuevo, no Anexe un número al título (por ejemplo `untitled 1`,). Si el usuario crea otro documento nuevo antes de guardarlo y crear títulos en el primero, `untitled 2`llame `untitled 3`a esa ventana,, etc.

Para obtener más información, consulte la sección [nomenclatura de Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowNaming.html#//apple_ref/doc/uid/20000957-CH35-SW1) de las directrices de interfaz humana de Apple [OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) .

<a name="Full-Screen_Windows" />

### <a name="full-screen-windows"></a>Ventanas de pantalla completa

En macOS, una ventana de la aplicación puede desplazarse por toda la pantalla, lo que incluye la barra de menús de la aplicación (que puede revelarse moviendo el cursor a la parte superior de la pantalla) para proporcionar una interacción gratuita de distracción con su contenido.

Apple sugiere las siguientes directrices:

- Determine si tiene sentido que una ventana se vea en pantalla completa. Las aplicaciones que proporcionan interacciones breves (como una calculadora) no deben proporcionar un modo de pantalla completa.
- Muestra la barra de herramientas si la tarea de pantalla completa lo requiere. Normalmente, la barra de herramientas está oculta en modo de pantalla completa.
- La ventana de pantalla completa debe tener todas las características que los usuarios necesitan para completar la tarea.
- Si es posible, evite la interacción con el buscador mientras el usuario está en una ventana de pantalla completa.
- Aproveche el aumento del espacio de la pantalla sin desplazar el foco fuera de la tarea principal.

Para obtener más información, consulte la sección de [ventanas de pantalla completa](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/FullScreen.html#//apple_ref/doc/uid/20000957-CH61-SW1) de las [directrices de interfaz humana de Apple OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) .

<a name="Panels" />

### <a name="panels"></a>Paneles

Un panel es una ventana auxiliar que contiene controles y opciones que afectan al documento activo o a la selección (como el selector de colores del sistema):

[![](window-images/panel01.png "Un panel de colores")](window-images/panel01.png#lightbox)

Los paneles pueden ser _específicos de la aplicación o de_ _todo_el sistema. Los paneles específicos de la aplicación flotan sobre la parte superior de las ventanas de documento de la aplicación y desaparecen cuando la aplicación está en segundo plano. Los paneles de todo el sistema (como el panel **fuentes** ), flotan encima de todas las ventanas abiertas independientemente de la aplicación. 

Apple sugiere las siguientes directrices:

- En general, use un panel estándar, los paneles transparentes solo deben usarse con moderación y para tareas con un uso intensivo de gráficos.
- Considere la posibilidad de usar un panel para proporcionar a los usuarios un fácil acceso a los controles importantes o a la información que afecta directamente a su tarea.
- Oculte y muestre los paneles según sea necesario.
- Los paneles siempre deben incluir la barra de título.
- Los paneles no deben incluir un botón minimizar activo.

#### <a name="inspectors"></a>Inspectores

La mayoría de las aplicaciones macOS modernas presentan controles y opciones auxiliares que afectan al documento activo o a la selección como _inspectores_ que forman parte de la ventana principal (como la aplicación de **páginas** que se muestra a continuación), en lugar de usar ventanas de panel:

[![](window-images/panel02.png "Un inspector de ejemplo")](window-images/panel02.png#lightbox)

Para obtener más información, consulte la sección [paneles](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowPanels.html#//apple_ref/doc/uid/20000957-CH42-SW1) de las [directrices de la interfaz humana de Apple OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) y nuestra aplicación de ejemplo [MacInspector](https://docs.microsoft.com/samples/xamarin/mac-samples/macinspector) para una implementación completa de una **interfaz de inspector** en una aplicación de Xamarin. Mac.

<a name="Creating_and_Maintaining_Windows_in_Xcode" />

## <a name="creating-and-maintaining-windows-in-xcode"></a>Crear y mantener ventanas en Xcode

Cuando se crea una nueva aplicación de Cocoa de Xamarin. Mac, se obtiene una ventana en blanco de forma predeterminada. Esta ventana se define en un `.storyboard` archivo que se incluye automáticamente en el proyecto. Para editar el diseño de Windows, en el **Explorador de soluciones**, haga doble `Main.storyboard` clic en el archivo:

[![](window-images/edit01.png "Seleccionar el guión gráfico principal")](window-images/edit01.png#lightbox)

Se abrirá el diseño de ventana en la Interface Builder de Xcode:

[![](window-images/edit02.png "Edición de la interfaz de usuario en Xcode")](window-images/edit02.png#lightbox)

En el **Inspector de atributos**, hay varias propiedades que puede usar para definir y controlar la ventana:

- **Título** : este es el texto que se mostrará en el título de la ventana.
- **Autosave** : esta es la _clave_ que se usará para identificar la ventana cuando su posición y la configuración se guardan automáticamente.
- **Barra de título** : hace que la ventana muestre una barra de título.
- **Título y barra de herramientas unificados** : Si la ventana incluye una barra de herramientas, debe formar parte de la barra de título.
- **Vista de contenido con tamaño completo** : permite que el área de contenido de la ventana esté debajo de la barra de título.
- **Sombra** : hace que la ventana tenga una sombra.
- Las ventanas con textura **texturizada** pueden usar efectos (como vibrancy) y se pueden mover arrastrándolos en cualquier lugar del cuerpo.
- **Cerrar** : hace que la ventana tenga un botón Cerrar.
- **Minimizar** : hace que la ventana tenga un botón minimizar.
- **Cambiar tamaño** : hace que la ventana tenga un control de tamaño.
- **Botón** de la barra de herramientas: hace que la ventana tenga un botón Ocultar/Mostrar barra de herramientas.
- **Restaurable** : es la posición de la ventana y la configuración se guarda y restaura automáticamente.
- **Visible en el inicio** : es la ventana que se muestra `.xib` automáticamente cuando se carga el archivo.
- **Ocultar al desactivar** : es la ventana oculta cuando la aplicación entra en el fondo.
- **Liberar al cerrar** : la ventana se purga de la memoria cuando está cerrada.
- **Mostrar información sobre herramientas siempre** : ¿se muestra constantemente la información sobre herramientas.
- **Recalcula el bucle de vista** : es el orden de la vista que se vuelve a calcular antes de que se dibuje la ventana.
- **Espacios**, **Exposé** y **ciclos** : todos definen cómo se comporta la ventana en esos entornos MacOS. 
- **Pantalla completa** : determina si esta ventana puede entrar en el modo de pantalla completa. 
- **Animación** : controla el tipo de animación disponible para la ventana.
- **Apariencia** : controla la apariencia de la ventana. Por ahora solo hay una apariencia, aguamarina.

Para obtener más información, consulte la [Introducción a Windows y a](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) la documentación de [NSWindow](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSWindow_Class/index.html#//apple_ref/occ/cl/NSWindow) .

<a name="Setting_the_Default_Size_and_Location" />

### <a name="setting-the-default-size-and-location"></a>Establecer el tamaño y la ubicación predeterminados

Para establecer la posición inicial de la ventana y controlar su tamaño, cambie al **Inspector de tamaño**:

[![](window-images/edit07.png "El tamaño y la ubicación predeterminados")](window-images/edit07.png#lightbox)

Aquí puede establecer el tamaño inicial de la ventana, darle un tamaño mínimo y máximo, establecer la ubicación inicial en la pantalla y controlar los bordes alrededor de la ventana.

<a name="Setting-a-Custom-Main-Window-Controller" />

### <a name="setting-a-custom-main-window-controller"></a>Establecer un controlador de ventana principal personalizado

Para poder crear salidas y acciones para exponer elementos de la interfaz de usuario C# al código, la aplicación de Xamarin. Mac tendrá que usar un controlador de ventana personalizado.

Haga lo siguiente:

1. Abra el guión gráfico de la aplicación en la Interface Builder de Xcode.
2. `NSWindowController` Seleccione en el superficie de diseño.
3. Cambie a la vista **Inspector de identidad** y `WindowController` escriba como **nombre de clase**: 

    [![](window-images/windowcontroller01.png "Establecer el nombre de clase")](window-images/windowcontroller01.png#lightbox)
4. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos.
5. Se `WindowController.cs` agregará un archivo al proyecto en el **Explorador de soluciones** en Visual Studio para Mac: 

    [![](window-images/windowcontroller02.png "Seleccionar el controlador de Windows")](window-images/windowcontroller02.png#lightbox)
6. Vuelva a abrir el guion gráfico en la Interface Builder de Xcode.
7. El `WindowController.h` archivo estará disponible para su uso: 

    [![](window-images/windowcontroller03.png "Edición del archivo WindowController. h")](window-images/windowcontroller03.png#lightbox)

<a name="Adding_UI_Elements" />

### <a name="adding-ui-elements"></a>Agregar elementos de interfaz de usuario

Para definir el contenido de una ventana, arrastre los controles desde el **Inspector de biblioteca** hasta el editor de la **interfaz**. Consulte la documentación de [Introducción a Xcode y Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) para obtener más información sobre el uso de Interface Builder para crear y habilitar controles.

Por ejemplo, arrastre una barra de herramientas desde el **Inspector de biblioteca** hasta la ventana del editor de la **interfaz**:

[![](window-images/edit03.png "Seleccionar una barra de herramientas de la biblioteca")](window-images/edit03.png#lightbox)

A continuación, arrastre en una **vista de texto** y cambie su tamaño para rellenar el área de la barra de herramientas:

[![](window-images/edit04.png "Agregar una vista de texto")](window-images/edit04.png#lightbox)

Dado que deseamos que la **vista de texto** se reduzca y crezca a medida que cambia el tamaño de la ventana, vamos a cambiar al **Editor de restricciones** y agregar las restricciones siguientes:

[![](window-images/edit05.png "Modificar restricciones")](window-images/edit05.png#lightbox)

Al hacer clic en el para los **haces de I en rojo** en la parte superior del editor y hacer clic en **Agregar 4 restricciones**, estamos indicando a la vista de texto que se adhiere a las coordenadas X, y, y a aumentar o reducir horizontal y verticalmente a medida que se cambia el tamaño de la ventana.

Por último, vamos a exponer la **vista de texto** al código mediante una **salida** (asegurándose de `ViewController.h` seleccionar el archivo):

[![](window-images/edit06.png "Configuración de una salida")](window-images/edit06.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Para obtener más información sobre cómo trabajar con **salidas** y **acciones**, consulte nuestra documentación sobre la [salida y la acción](~/mac/get-started/hello-mac.md#outlets-and-actions) .

<a name="Standard_Window_Workflow" />

### <a name="standard-window-workflow"></a>Flujo de trabajo de ventana estándar

En el caso de las ventanas creadas y con las que trabaja en la aplicación de Xamarin. Mac, el proceso es básicamente el mismo que se acaba de hacer anteriormente:

1. En el caso de las nuevas ventanas que no son el valor predeterminado que se agrega automáticamente al proyecto, agregue una nueva definición de ventana al proyecto. Esto se explicará con más detalle a continuación.
1. Haga doble clic en `Main.storyboard` el archivo para abrir el diseño de ventana para su edición en el Interface Builder de Xcode.
1. Arrastre una ventana nueva al diseño de la interfaz de usuario y enlace la ventana a la ventana principal con _objetos segue_ (para obtener más información, vea la sección [objetos segue](~/mac/platform/storyboards/indepth.md#Segues) de nuestra documentación sobre [Cómo trabajar con guiones gráficos](~/mac/platform/storyboards/indepth.md) ).
1. Establezca las propiedades de ventana necesarias en el **Inspector de atributos** y el inspector de **tamaño**.
1. Arrastre los controles necesarios para compilar la interfaz y configurarlos en el **Inspector de atributos**.
1. Use el **Inspector de tamaño** para controlar el cambio de tamaño de los elementos de la interfaz de usuario.
1. Exponga los elementos de la interfaz de C# usuario de la ventana al código mediante **salidas** y **acciones**.
1. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Ahora que tenemos una ventana básica creada, veremos los procesos típicos que realiza una aplicación de Xamarin. Mac al trabajar con Windows. 

<a name="Displaying_the_Default_Window" />

## <a name="displaying-the-default-window"></a>Mostrar la ventana predeterminada

De forma predeterminada, una nueva aplicación de Xamarin. Mac mostrará automáticamente la ventana definida en `MainWindow.xib` el archivo cuando se inicie:

[![](window-images/display01.png "Ventana de ejemplo en ejecución")](window-images/display01.png#lightbox)

Como hemos modificado el diseño de la ventana anterior, ahora incluye una barra de herramientas y un control de **vista de texto** predeterminados. La siguiente sección `Info.plist` del archivo es responsable de mostrar esta ventana:

[![](window-images/display00.png "Edición de info. plist")](window-images/display00.png#lightbox)

La lista desplegable **interfaz principal** se usa para seleccionar el guion gráfico que se usará como la interfaz de usuario de la `Main.storyboard`aplicación principal (en este caso).

Un controlador de vista se agrega automáticamente al proyecto para controlar las ventanas principales que se muestran (junto con la vista principal). Se define en el `ViewController.cs` archivo y se adjunta al **propietario del archivo** en Interface Builder en el inspector de **identidad**:

[![](window-images/display02.png "Establecer el propietario del archivo")](window-images/display02.png#lightbox)

En nuestra ventana, nos gustaría tener un título de `untitled` cuando se abre por primera vez, así que vamos a invalidar el `ViewWillAppear` método `ViewController.cs` en el para que tenga el aspecto siguiente:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";
}
```    

> [!NOTE]
> Estamos estableciendo el valor de la `Title` propiedad de la ventana en el `ViewWillAppear` `ViewDidLoad` método en lugar del método porque, mientras que la vista se puede cargar en la memoria, todavía no se ha creado una instancia totalmente. Si se intentó tener acceso `Title` a la propiedad `ViewDidLoad` en el método, se `null` obtendría una excepción, ya que la ventana no se ha construido y se ha conectado a la propiedad.

<a name="Programmatically_Closing_a_Window" />

## <a name="programmatically-closing-a-window"></a>Cerrar una ventana mediante programación

Puede haber ocasiones en las que desee cerrar mediante programación una ventana en una aplicación de Xamarin. Mac, a excepción de hacer que el usuario haga clic en el botón **cerrar** de la ventana o usando un elemento de menú. MacOS proporciona dos maneras diferentes de cerrar un `NSWindow` mediante programación: `PerformClose` y `Close`.

<a name="PerformClose" />

### <a name="performclose"></a>PerformClose

Al llamar `PerformClose` al método de `NSWindow` un se simula que el usuario hace clic en el botón **cerrar** de la ventana resaltando momentáneamente el botón y cerrando la ventana.

Si la aplicación implementa el `NSWindow`evento de `WillClose` , se generará antes de que se cierre la ventana. Si el evento devuelve `false`, la ventana no se cerrará. Si la ventana no tiene un botón **cerrar** o no se puede cerrar por alguna razón, el SO emitirá el sonido de la alerta.

Por ejemplo:

```csharp
MyWindow.PerformClose(this);
```

Intentaría cerrar la instancia `MyWindow` de `NSWindow` . Si se realiza correctamente, se cerrará la ventana, de lo contrario se emitirá el sonido de la alerta y permanecerá abierto.

<a name="Close" />

### <a name="close"></a>Cerrar

Al llamar `Close` al método de `NSWindow` un no se simula que el usuario haga clic en el botón **cerrar** de la ventana resaltando momentáneamente el botón, simplemente se cierra la ventana.

Una ventana no tiene que estar visible para cerrarse y se publicará una `NSWindowWillCloseNotification` notificación en el centro de notificaciones predeterminado para que la ventana se cierre.

El `Close` método difiere de dos maneras importantes `PerformClose` del método:

1. No intenta generar el `WillClose` evento.
2. No simula que el usuario haga clic en el botón **cerrar** resaltando momentáneamente el botón.

Por ejemplo:

```csharp
MyWindow.Close();
```

Se debe cerrar la `MyWindow` `NSWindow` instancia de.

<a name="Modified-Windows-Content" />

## <a name="modified-windows-content"></a>Contenido de Windows modificado

En MacOS, Apple ha proporcionado una manera de informar al usuario de que el usuario ha modificado el`NSWindow`contenido de una ventana () y debe guardarse. Si la ventana contiene contenido modificado, se mostrará un pequeño punto negro en su widget de **cierre** :

[![](window-images/close01.png "Una ventana con el marcador modificado")](window-images/close01.png#lightbox)

Si el usuario intenta cerrar la ventana o salir de la aplicación Mac mientras hay cambios sin guardar en el contenido de la ventana, debe presentar un cuadro de [diálogo](~/mac/user-interface/dialog.md) o una [hoja modal](~/mac/user-interface/dialog.md) y permitir que el usuario guarde primero los cambios:

[![](window-images/close02.png "Una hoja de almacenamiento que se muestra cuando se cierra la ventana")](window-images/close02.png#lightbox)

### <a name="marking-a-window-as-modified"></a>Marcar una ventana como modificada

Para marcar una ventana como con contenido modificado, use el código siguiente:

```csharp
// Mark Window content as modified
Window.DocumentEdited = true;
```

Una vez guardado el cambio, borre la marca modificada mediante:

```csharp
// Mark Window content as not modified
Window.DocumentEdited = false;
```

### <a name="saving-changes-before-closing-a-window"></a>Guardar los cambios antes de cerrar una ventana

Para ver si el usuario cierra una ventana y le permite guardar el contenido modificado de antemano, tendrá que crear una subclase de `NSWindowDelegate` e invalidar su `WindowShouldClose` método. Por ejemplo:

```csharp
using System;
using AppKit;
using System.IO;
using Foundation;

namespace SourceWriter
{
    public class EditorWindowDelegate : NSWindowDelegate
    {
        #region Computed Properties
        public NSWindow Window { get; set;}
        #endregion

        #region constructors
        public EditorWindowDelegate (NSWindow window)
        {
            // Initialize
            this.Window = window;

        }
        #endregion

        #region Override Methods
        public override bool WindowShouldClose (Foundation.NSObject sender)
        {
            // is the window dirty?
            if (Window.DocumentEdited) {
                var alert = new NSAlert () {
                    AlertStyle = NSAlertStyle.Critical,
                    InformativeText = "Save changes to document before closing window?",
                    MessageText = "Save Document",
                };
                alert.AddButton ("Save");
                alert.AddButton ("Lose Changes");
                alert.AddButton ("Cancel");
                var result = alert.RunSheetModal (Window);

                // Take action based on result
                switch (result) {
                case 1000:
                    // Grab controller
                    var viewController = Window.ContentViewController as ViewController;

                    // Already saved?
                    if (Window.RepresentedUrl != null) {
                        var path = Window.RepresentedUrl.Path;

                        // Save changes to file
                        File.WriteAllText (path, viewController.Text);
                        return true;
                    } else {
                        var dlg = new NSSavePanel ();
                        dlg.Title = "Save Document";
                        dlg.BeginSheet (Window, (rslt) => {
                            // File selected?
                            if (rslt == 1) {
                                var path = dlg.Url.Path;
                                File.WriteAllText (path, viewController.Text);
                                Window.DocumentEdited = false;
                                viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
                                viewController.View.Window.RepresentedUrl = dlg.Url;
                                Window.Close();
                            }
                        });
                        return true;
                    }
                    return false;
                case 1001:
                    // Lose Changes
                    return true;
                case 1002:
                    // Cancel
                    return false;
                }
            }

            return true;
        }
        #endregion
    }
}
```

Use el código siguiente para adjuntar una instancia de este delegado a la ventana:

```csharp
// Set delegate
Window.Delegate = new EditorWindowDelegate(Window);
```

### <a name="saving-changes-before-closing-the-app"></a>Guardar los cambios antes de cerrar la aplicación

Por último, la aplicación de Xamarin. Mac debe comprobar si alguna de sus ventanas contiene contenido modificado y permitir al usuario guardar los cambios antes de salir. Para ello, edite `AppDelegate.cs` el archivo, invalide el `ApplicationShouldTerminate` método y haga que tenga un aspecto similar al siguiente:

```csharp
public override NSApplicationTerminateReply ApplicationShouldTerminate (NSApplication sender)
{
    // See if any window needs to be saved first
    foreach (NSWindow window in NSApplication.SharedApplication.Windows) {
        if (window.Delegate != null && !window.Delegate.WindowShouldClose (this)) {
            // Did the window terminate the close?
            return NSApplicationTerminateReply.Cancel;
        }
    }

    // Allow normal termination
    return NSApplicationTerminateReply.Now;
}
```

<a name="Working_with_Multiple_Windows" />

## <a name="working-with-multiple-windows"></a>Trabajar con varias ventanas

La mayoría de las aplicaciones Mac basadas en documentos pueden editar varios documentos al mismo tiempo. Por ejemplo, un editor de texto puede tener varios archivos de texto abiertos para editarlos al mismo tiempo. De forma predeterminada, la nueva aplicación de Xamarin. Mac tiene un menú **archivo** con un **nuevo** elemento conectado automáticamente a la `newDocument:` **acción**.

Vamos a activar este nuevo elemento y permitir que el usuario abra varias copias de la ventana principal para editar varios documentos a la vez.

Vamos a editar `AppDelegate.cs` el archivo y agregaremos la siguiente propiedad calculada:

```csharp
public int UntitledWindowCount { get; set;} =1;
```

Lo usaremos para realizar el seguimiento del número de archivos no guardados para que podamos enviarnos sus comentarios al usuario (según las instrucciones de Apple, tal como se ha explicado anteriormente).

A continuación, agregue el método siguiente:

```csharp
[Export ("newDocument:")]
void NewDocument (NSObject sender) {
    // Get new window
    var storyboard = NSStoryboard.FromName ("Main", null);
    var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

    // Display
    controller.ShowWindow(this);

    // Set the title
    controller.Window.Title = (++UntitledWindowCount == 1) ? "untitled" : string.Format ("untitled {0}", UntitledWindowCount);
}
```

Este código crea una nueva versión del controlador de ventana, carga la nueva ventana, la convierte en la ventana principal y clave, y la establece como título. Ahora, Si ejecutamos la aplicación y seleccionamos **nuevo** en el menú **archivo** , se abrirá una nueva ventana del editor y se mostrará:

[![](window-images/display04.png "Se agregó una nueva ventana sin título")](window-images/display04.png#lightbox)

Si se abre el menú de **Windows** , puede ver que la aplicación realiza el seguimiento y el control automático de las ventanas abiertas:

[![](window-images/display05.png "Menú ventanas")](window-images/display05.png#lightbox)

Para obtener más información sobre cómo trabajar con menús en una aplicación de Xamarin. Mac, consulte nuestra documentación sobre [Cómo trabajar con menús](~/mac/user-interface/menu.md) .

<a name="Getting_the_Currently_Active_Window" />

### <a name="getting-the-currently-active-window"></a>Obtener la ventana activa actualmente

En una aplicación de Xamarin. Mac que puede abrir varias ventanas (documentos), hay ocasiones en las que necesitará obtener la ventana actual y en el nivel superior (la ventana de clave). El código siguiente devolverá la ventana de clave:

```csharp
var window = NSApplication.SharedApplication.KeyWindow;
```

Se puede llamar a en cualquier clase o método que necesite tener acceso a la ventana de clave actual. Si no hay ninguna ventana abierta, se devolverá `null`.

<a name="Accessing-All-App-Windows" />

### <a name="accessing-all-app-windows"></a>Acceder a todas las ventanas de la aplicación

Puede haber ocasiones en las que necesite tener acceso a todas las ventanas que la aplicación de Xamarin. Mac tiene abierta actualmente. Por ejemplo, para ver si un archivo que el usuario desea abrir ya está abierto en una ventana de salida.

`NSApplication.SharedApplication` Mantiene una`Windows` propiedad que contiene una matriz de todas las ventanas abiertas en la aplicación. Puede recorrer en iteración esta matriz para tener acceso a todas las ventanas actuales de la aplicación. Por ejemplo:

```csharp
// Is the file already open?
for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
    var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
    if (content != null && path == content.FilePath) {
        // Bring window to front
        NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
        return true;
    }
}
```

En el código de ejemplo, se convierte cada ventana devuelta a `ViewController` la clase personalizada en nuestra aplicación y se prueba el valor de `Path` una propiedad personalizada con respecto a la ruta de acceso de un archivo que el usuario desea abrir. Si el archivo ya está abierto, se trae la ventana al principio.

<a name="Adjusting_the_Window_Size_in_Code" />

## <a name="adjusting-the-window-size-in-code"></a>Ajustar el tamaño de la ventana en el código

Hay ocasiones en las que la aplicación necesita cambiar el tamaño de una ventana en el código. Para cambiar el tamaño y la `Frame` posición de una ventana, debe ajustar su propiedad. Al ajustar el tamaño de una ventana, normalmente también debe ajustar su origen para mantener la ventana en la misma ubicación debido al sistema de coordenadas de macOS.

A diferencia de iOS en el que la esquina superior izquierda representa (0,0), macOS usa un sistema de coordenadas matemáticos donde la esquina inferior izquierda de la pantalla representa (0,0). En iOS, las coordenadas aumentan a medida que se desplaza hacia abajo hacia la derecha. En macOS, las coordenadas aumentan en el valor hacia la derecha. 

En el código de ejemplo siguiente se cambia el tamaño de una ventana:

```csharp
nfloat y = 0;

// Calculate new origin
y = Frame.Y - (768 - Frame.Height);

// Resize and position window
CGRect frame = new CGRect (Frame.X, y, 1024, 768);
SetFrame (frame, true);
```

> [!IMPORTANT]
> Al ajustar el tamaño y la ubicación de Windows en el código, debe asegurarse de que respeta los tamaños mínimo y máximo que ha establecido en Interface Builder. Esto no se respetará automáticamente y podrá hacer que la ventana sea más grande o más pequeña que estos límites.

<a name="Monitoring-Window-Size-Changes" />

## <a name="monitoring-window-size-changes"></a>Supervisión de los cambios de tamaño de la ventana

Puede haber ocasiones en las que necesite supervisar los cambios en el tamaño de una ventana dentro de la aplicación de Xamarin. Mac. Por ejemplo, para volver a dibujar el contenido para ajustarse al nuevo tamaño.

Para supervisar los cambios de tamaño, primero asegúrese de que ha asignado una clase personalizada para el controlador de ventana en la Interface Builder de Xcode. Por ejemplo, `MasterWindowController` en lo siguiente:

[![](window-images/resize01.png "Inspector de identidad")](window-images/resize01.png#lightbox)

Después, edite la clase de controlador de ventana personalizada `DidResize` y supervise el evento en la ventana del controlador para recibir notificaciones de los cambios de tamaño en directo. Por ejemplo:

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

    Window.DidResize += (sender, e) => {
        // Do something as the window is being live resized
    };
}
```

Opcionalmente, puede usar el `DidEndLiveResize` evento para recibir una notificación solo después de que el usuario haya terminado de cambiar el tamaño de la ventana. Por ejemplo:

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

        Window.DidEndLiveResize += (sender, e) => {
        // Do something after the user's finished resizing
        // the window
    };
}
```

<a name="Setting_a_Window’s_Title_and_Represented_File" />

## <a name="setting-a-windows-title-and-represented-file"></a>Establecer el título y el archivo representado de una ventana

Cuando se trabaja con ventanas que representan documentos `NSWindow` , tiene `DocumentEdited` una propiedad que, si `true` se establece en, muestra un punto pequeño en el botón Cerrar para dar al usuario una indicación de que el archivo se ha modificado y debe guardarse antes de cerrarse.

Vamos a editar `ViewController.cs` el archivo y realizar los cambios siguientes:

```csharp
public bool DocumentEdited {
    get { return View.Window.DocumentEdited; }
    set { View.Window.DocumentEdited = value; }
}
...

public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";

    View.Window.WillClose += (sender, e) => {
        // is the window dirty?
        if (DocumentEdited) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to give the user the ability to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        }
    };
}

public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Show when the document is edited
    DocumentEditor.TextDidChange += (sender, e) => {
        // Mark the document as dirty
        DocumentEdited = true;
    };

    // Overriding this delegate is required to monitor the TextDidChange event
    DocumentEditor.ShouldChangeTextInRanges += (NSTextView view, NSValue[] values, string[] replacements) => {
        return true;
    };

}
```

También estamos supervisando `WillClose` el evento en la ventana y comprobando el estado `DocumentEdited` de la propiedad. Si es `true` necesario proporcionar al usuario la capacidad de guardar los cambios en el archivo. Si ejecutamos la aplicación y escribemos texto, se mostrará el punto:

[![](window-images/file01.png "Una ventana modificada")](window-images/file01.png#lightbox)

Si intentamos cerrar la ventana, obtenemos una alerta:

[![](window-images/file02.png "Mostrar un cuadro de diálogo guardar")](window-images/file02.png#lightbox)

Si estamos cargando un documento desde un archivo, podemos establecer el título de la ventana en el nombre del archivo mediante el `window.SetTitleWithRepresentedFilename (Path.GetFileName(path));` método (dado que `path` es una cadena que representa el archivo que se está abriendo). Además, se puede establecer la dirección URL del archivo mediante el `window.RepresentedUrl = url;` método.

Si la dirección URL apunta a un tipo de archivo conocido por el sistema operativo, se mostrará el icono en la barra de título. Si el usuario hace clic con el botón derecho en el icono, se mostrará la ruta de acceso al archivo.

Vamos a editar el `AppDelegate.cs` archivo y agregar el método siguiente:

```csharp
[Export ("openDocument:")]
void OpenDialog (NSObject sender)
{
    var dlg = NSOpenPanel.OpenPanel;
    dlg.CanChooseFiles = true;
    dlg.CanChooseDirectories = false;

    if (dlg.RunModal () == 1) {
        // Nab the first file
        var url = dlg.Urls [0];

        if (url != null) {
            var path = url.Path;

            // Get new window
            var storyboard = NSStoryboard.FromName ("Main", null);
            var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

            // Display
            controller.ShowWindow(this);

            // Load the text into the window
            var viewController = controller.Window.ContentViewController as ViewController;
            viewController.Text = File.ReadAllText(path);
                    viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
            viewController.View.Window.RepresentedUrl = url;

        }
    }
}
```

Ahora, Si ejecutamos la aplicación, seleccione **abrir...** en el menú **archivo** , seleccione un archivo de texto en el cuadro de diálogo **abrir** y ábralo:

[![](window-images/file03.png "Cuadro de diálogo abrir")](window-images/file03.png#lightbox)

Se mostrará el archivo y se establecerá el título con el icono del archivo:

[![](window-images/file04.png "Contenido de un archivo cargado")](window-images/file04.png#lightbox)

<a name="Adding_a_New_Window_to_a_Project" />

## <a name="adding-a-new-window-to-a-project"></a>Agregar una nueva ventana a un proyecto

Además de la ventana principal del documento, es posible que una aplicación de Xamarin. Mac necesite Mostrar otros tipos de ventanas al usuario, como preferencias o paneles del inspector.

Para agregar una nueva ventana, haga lo siguiente:

1. En el **Explorador de soluciones**, haga doble clic en `Main.storyboard` el archivo para abrirlo para su edición en el Interface Builder de Xcode.
2. Arrastre un nuevo **controlador de ventana** desde la **biblioteca** y colóquelo en el **superficie de diseño**:

    [![](window-images/new01.png "Selección de un nuevo controlador de ventana en la biblioteca")](window-images/new01.png#lightbox)
3. En el **Inspector de identidad**, `PreferencesWindow` escriba para el **ID. de guion gráfico**: 

    [![](window-images/new02.png "Establecer el identificador de guión gráfico")](window-images/new02.png#lightbox)
4. Diseñe la interfaz: 

    [![](window-images/new03.png "Diseñar la interfaz de usuario")](window-images/new03.png#lightbox)
5. Abra el menú de la`MacWindows`aplicación (), seleccione **preferencias...** , haga clic en el control y arrástrelo a la nueva ventana: 

    [![](window-images/new05.png "Creación de un segue")](window-images/new05.png#lightbox)
6. Seleccione **Mostrar** en el menú emergente.
7. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Si ejecutamos el código y seleccionamos las **preferencias...** en el menú de la **aplicación**, se mostrará la ventana:

[![](window-images/new04.png "Menú de preferencias de ejemplo")](window-images/new04.png#lightbox)

<a name="Working_with_Panels" />

## <a name="working-with-panels"></a>Trabajar con paneles

Como se indicó al principio de este artículo, un panel flota sobre otras ventanas y proporciona herramientas o controles con los que los usuarios pueden trabajar mientras los documentos están abiertos. 

Al igual que cualquier otro tipo de ventana que se crea y con la que se trabaja en la aplicación de Xamarin. Mac, el proceso es básicamente el mismo:

1. Agregue una nueva definición de ventana al proyecto.
2. Haga doble clic en `.xib` el archivo para abrir el diseño de ventana para su edición en el Interface Builder de Xcode.
3. Establezca las propiedades de ventana necesarias en el **Inspector de atributos** y el inspector de **tamaño**.
4. Arrastre los controles necesarios para compilar la interfaz y configurarlos en el **Inspector de atributos**.
5. Use el **Inspector de tamaño** para controlar el cambio de tamaño de los elementos de la interfaz de usuario.
6. Exponga los elementos de la interfaz de C# usuario de la ventana al código mediante **salidas** y **acciones**.
7. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

En el **Inspector de atributos**, tiene las siguientes opciones específicas de los paneles:

[![](window-images/panel03.png "Inspector de atributo")](window-images/panel03.png#lightbox)

- **Estilo** : permite ajustar el estilo del panel desde: Panel normal (se parece a una ventana estándar), el panel de utilidad (tiene una barra de título más pequeña), el panel HUD (es traslúcido y la barra de título forma parte del fondo).
- **No Activation** : determina que en el panel se convierte en la ventana de clave.
- **Documento modal** : Si el documento es modal, el panel solo flotará sobre las ventanas de la aplicación, de lo contrario se flota por encima de todo.

Para agregar un nuevo panel, haga lo siguiente:

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **nuevo archivo..** .
2. En el cuadro de diálogo nuevo archivo, seleccione la**ventana de cacao de** **Xamarin. Mac** > con el controlador:

    [![](window-images/panels00.png "Agregar un nuevo controlador de ventana")](window-images/panels00.png#lightbox)
3. Escriba `DocumentPanel` para el **Nombre** y haga clic en el botón **Nuevo**.
4. Haga doble clic en `DocumentPanel.xib` el archivo para abrirlo y editarlo en Interface Builder: 

    [![](window-images/new02.png "Editar el panel")](window-images/new02.png#lightbox)
5. Elimine la ventana existente y arrastre un panel desde el **Inspector de biblioteca** en el editor de la **interfaz**: 

    [![](window-images/panels01.png "Eliminando la ventana existente")](window-images/panels01.png#lightbox)
6. Enganche el panel hasta la**salida**de la**ventana** -  -  **propietaria del archivo**: 

    [![](window-images/panels02.png "Arrastrar para conectar el panel")](window-images/panels02.png#lightbox)
7. Cambie al **Inspector de identidades** y establezca la clase del panel `DocumentPanel`en: 

    [![](window-images/panels03.png "Establecer la clase del panel")](window-images/panels03.png#lightbox)
8. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.
9. Edite `DocumentPanel.cs` el archivo y cambie la definición de clase por lo siguiente: 

    `public partial class DocumentPanel : NSPanel`
10. Guarde los cambios en el archivo.

Edite `AppDelegate.cs` el archivo y haga `DidFinishLaunching` que el método tenga el aspecto siguiente:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
        // Display panel
    var panel = new DocumentPanelController ();
    panel.Window.MakeKeyAndOrderFront (this);
}

```

Si ejecutamos nuestra aplicación, se mostrará el panel:

[![](window-images/panels04.png "Panel en una aplicación en ejecución")](window-images/panels04.png#lightbox)

> [!IMPORTANT]
> Las ventanas de panel están desusadas por Apple y deben reemplazarse por **interfaces de inspector**. Para ver un ejemplo completo de cómo crear un **Inspector** en una aplicación de Xamarin. Mac, consulte nuestra aplicación de ejemplo [MacInspector](https://docs.microsoft.com/samples/xamarin/mac-samples/macinspector) .

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se ha realizado una visión detallada del trabajo con ventanas y paneles en una aplicación de Xamarin. Mac. Vimos los distintos tipos y usos de ventanas y paneles, cómo crear y mantener ventanas y paneles en la Interface Builder de Xcode y cómo trabajar con ventanas y paneles en C# el código.

## <a name="related-links"></a>Vínculos relacionados

- [MacWindows (ejemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/macwindows)
- [MacInspector (ejemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/macinspector)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabajar con menús](~/mac/user-interface/menu.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
- [Introducción a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
