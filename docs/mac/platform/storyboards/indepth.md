---
title: Trabajar con guiones gráficos en Xamarin. Mac
description: En este documento se describe cómo trabajar con guiones gráficos en Xamarin. Mac, examinando cómo cargarlos desde código, el ciclo de vida del controlador de vista, la cadena del respondedor, objetos segue, controladores de ventanas, reconocedores de gestos, etc.
ms.prod: xamarin
ms.assetid: DF4DF7C2-DDD7-4A32-B375-5C5446301EC5
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 53f8dda128facc29a0bfbb67b2dfbd263504b3b4
ms.sourcegitcommit: 513feb0e07558766e3de4a898e53d56b27c20559
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697688"
---
# <a name="working-with-storyboards-in-xamarinmac"></a>Trabajar con guiones gráficos en Xamarin. Mac

Un guión gráfico define toda la interfaz de usuario de una aplicación determinada dividida en una información general funcional de los controladores de vista. En el Interface Builder de Xcode, cada uno de estos controladores reside en su propia escena.

[![Un guión gráfico en la Interface Builder de Xcode](indepth-images/intro01.png)](indepth-images/intro01.png#lightbox)

El guión gráfico es un archivo de recursos (con las extensiones de `.storyboard` ) que se incluye en el lote de la aplicación de Xamarin. Mac cuando se compila y se envía. Para definir el guión gráfico de inicio de la aplicación, edite su `Info.plist` archivo y seleccione la **interfaz principal** en el cuadro desplegable: 

[![Editor de info. plist](indepth-images/sb01.png)](indepth-images/sb01.png#lightbox)

<a name="Loading-from-Code"></a>

## <a name="loading-from-code"></a>Cargar desde código

Puede haber ocasiones en las que necesite cargar un guion gráfico específico desde el código y crear un controlador de vista manualmente. Puede usar el código siguiente para realizar esta acción:

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);
```

`FromName`Carga el archivo de guion gráfico con el nombre especificado que se ha incluido en el paquete de la aplicación. `InstantiateControllerWithIdentifier`Crea una instancia del controlador de vista con la identidad especificada. La identidad se establece en el Interface Builder de Xcode al diseñar la interfaz de usuario:

[![Establecer el identificador de guión gráfico en Interface Builder.](indepth-images/sb02.png)](indepth-images/sb02.png#lightbox)

Opcionalmente, puede usar el `InstantiateInitialController` método para cargar el controlador de vista al que se ha asignado el controlador inicial en Interface Builder:

[![Establecimiento del controlador inicial](indepth-images/sb03.png)](indepth-images/sb03.png#lightbox)

Está marcado por el **punto de entrada del guión gráfico** y por la flecha de apertura finalizada.

<a name="View-Controllers"></a>

## <a name="view-controllers"></a>Controladores de vista

Los controladores de vista definen las relaciones entre una vista determinada de la información dentro de una aplicación Mac y el modelo de datos que proporciona esa información. Cada escena de nivel superior del guión gráfico representa un controlador de vista en el código de la aplicación de Xamarin. Mac.

<a name="The-View-Controller-Lifecycle"></a>

### <a name="the-view-controller-lifecycle"></a>Ciclo de vida del controlador de vista

Se han agregado varios métodos nuevos a la `NSViewController` clase para admitir guiones gráficos en MacOS. Lo más importante es que los métodos siguientes usen para responder al ciclo de vida de la vista que controla el controlador de vista dado:

- `ViewDidLoad` : Se llama a este método cuando la vista se carga desde el archivo de guion gráfico.
- `ViewWillAppear` : Se llama a este método justo antes de que la vista se muestre en la pantalla.
- `ViewDidAppear` : Este método se llama directamente después de que la vista se muestre en la pantalla.
- `ViewWillDisappear` : Se llama a este método justo antes de que se quite la vista de la pantalla.
- `ViewDidDisappear` : Este método se llama directamente después de quitar la vista de la pantalla.
- `UpdateViewConstraints` : Se llama a este método cuando es necesario actualizar las restricciones que definen una posición y tamaño de diseño automático de la vista.
- `ViewWillLayout` : Se llama a este método justo antes de que las subvistas de esta vista se colocan en la pantalla.
- `ViewDidLayout` : Este método se llama directamente después de que las subvistas de la vista se colocan en la pantalla.

<a name="The-Responder-Chain"></a>

### <a name="the-responder-chain"></a>Cadena del respondedor

Además, `NSViewControllers` ahora forman parte de la cadena de _respuesta_ de la ventana:

[![Cadena del respondedor](indepth-images/vc01.png)](indepth-images/vc01.png#lightbox)

Y, por tanto, están conectados para recibir y responder a eventos como cortar, copiar y pegar selecciones de elementos de menú. Esta conexión de controlador de vista automática solo se produce en aplicaciones que se ejecutan en macOS Sierra (10,12) y versiones posteriores.

<a name="Containment"></a>

### <a name="containment"></a>Containment

En guiones gráficos, los controladores de vista (como el controlador de vista en dos paneles y el controlador de vista de pestaña) ahora pueden implementar la _contención_, de modo que pueden "contener" otros controladores de subvista:

[![Ejemplo de contención de controlador de vista](indepth-images/vc02.png)](indepth-images/vc02.png#lightbox)

Los controladores de vista secundarios contienen métodos y propiedades para volver a enlazarlos al controlador de vista primario y trabajar con la visualización y eliminación de vistas de la pantalla.

Todos los controladores de vista de contenedor integrados en macOS tienen un diseño específico que Apple sugiere que siga si crea sus propios controladores de vista de contenedor personalizados:

[![Diseño del controlador de vista](indepth-images/vc03.png)](indepth-images/vc03.png#lightbox)

El controlador de vista de colección contiene una matriz de elementos de la vista de colección, cada uno de los cuales contiene uno o varios controladores de vista que contienen sus propias vistas.

<a name="Segues"></a>

## <a name="segues"></a>Objetos segue

Objetos segue proporcionan las relaciones entre todas las escenas que definen la interfaz de usuario de la aplicación. Si está familiarizado con el trabajo en guiones gráficos en iOS, sabe que objetos segue para iOS normalmente definen las transiciones entre las vistas de pantalla completa. Esto difiere de macOS, cuando objetos segue normalmente define "[contención](#Containment)", donde una escena es el elemento secundario de una escena primaria.

En macOS, la mayoría de las aplicaciones suelen agrupar sus vistas en la misma ventana con elementos de interfaz de usuario como vistas y pestañas de división. A diferencia de iOS, en el que las vistas se deben pasar de una pantalla a otra, debido a un espacio de presentación físico limitado.

<a name="Presentation-Segues"></a>

### <a name="presentation-segues"></a>Objetos segue de presentación

En función de la contención de las tendencias de macOS, hay situaciones en las que se usan la _presentación objetos segue_ , como ventanas modales, vistas de hoja y popovers. macOS proporciona los siguientes tipos de segue integrados:

- **Mostrar** : muestra el destino de segue como una ventana no modal. Por ejemplo, use este tipo de segue para presentar otra instancia de una ventana de documento en la aplicación.
- **Modal** : presenta el destino de segue como una ventana modal. Por ejemplo, use este tipo de segue para presentar la ventana de preferencias de la aplicación.
- **Hoja** : presenta el destino de segue como una hoja adjunta a la ventana primaria. Por ejemplo, use este tipo de segue para presentar una hoja de búsqueda y reemplazo.
- **Elemento flotante** : presenta el destino de segue como en una ventana de elemento flotante. Por ejemplo, use este tipo segue para presentar opciones cuando el usuario hace clic en un elemento de la interfaz de usuario.
- **Personalizado** : presenta el destino de segue con un tipo segue personalizado definido por el desarrollador. Para obtener más información, consulte la sección creación de un [objetos segue personalizado](#Creating-Custom-Segues) .

Al usar la presentación objetos segue, puede invalidar el `PrepareForSegue` método del controlador de vista primario para la presentación a fin de inicializar las variables y y proporcionar los datos al controlador de vistas que se está presentando.

<a name="Triggered-Segues"></a>

### <a name="triggered-segues"></a>Objetos segue desencadenado

Los objetos segue desencadenados permiten especificar objetos segue con nombre (a través de su propiedad de **identificador** en Interface Builder) y desactivarlos mediante eventos como el usuario que hace clic en un botón o llamando al `PerformSegue` método en el código:

```csharp
// Display the Scene defined by the given Segue ID
PerformSegue("MyNamedSegue", this);
``` 

El identificador de segue se define dentro del Interface Builder de Xcode al diseñar la interfaz de usuario de la aplicación:

[![Escribir un nombre de segue](indepth-images/sg02.png)](indepth-images/sg02.png#lightbox)

En el controlador de vista que actúa como el origen de segue, debe invalidar el `PrepareForSegue` método y realizar cualquier inicialización necesaria antes de que se ejecute segue y se muestre el controlador de vista especificado:

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on Segue ID
    switch (segue.Identifier) {
    case "MyNamedSegue":
        // Prepare for the segue to happen
        ...
        break;
    }
}
```

Opcionalmente, puede invalidar el `ShouldPerformSegue` método y controlar si el segue se ejecuta realmente a través del código C#. En el caso de los controladores de vista presentados manualmente, llame `DismissController` a su método para quitarlos de la pantalla cuando ya no se necesiten.

<a name="Creating-Custom-Segues"></a>

### <a name="creating-custom-segues"></a>Crear objetos segue personalizadas

Puede haber ocasiones en las que la aplicación requiera un tipo segue no proporcionado por el objetos segue de compilación definido en macOS. En este caso, puede crear un segue personalizado que se puede asignar a los Interface Builder de Xcode al diseñar la interfaz de usuario de la aplicación.

Por ejemplo, para crear un nuevo tipo segue que reemplace el controlador de vista actual dentro de una ventana (en lugar de abrir la escena de destino en una nueva ventana), podemos usar el código siguiente:

```csharp
using System;
using AppKit;
using Foundation;

namespace OnCardMac
{
    [Register("ReplaceViewSeque")]
    public class ReplaceViewSeque : NSStoryboardSegue
    {
        #region Constructors
        public ReplaceViewSeque() {

        }

        public ReplaceViewSeque (string identifier, NSObject sourceController, NSObject destinationController) : base(identifier,sourceController,destinationController) {

        }

        public ReplaceViewSeque (IntPtr handle) : base(handle) {
        }

        public ReplaceViewSeque (NSObjectFlag x) : base(x) {
        }
        #endregion

        #region Override Methods
        public override void Perform ()
        {
            // Cast the source and destination controllers
            var source = SourceController as NSViewController;
            var destination = DestinationController as NSViewController;

            // Swap the controllers
            source.View.Window.ContentViewController = destination;

            // Release memory
            source.RemoveFromParentViewController ();
        }
        #endregion

    }
        
}
```

Hay un par de cosas que debe tener en cuenta aquí:

- Estamos usando el `Register` atributo para exponer esta clase a Objective-C/MacOS.
- Estamos invalidando el `Perform` método para realizar realmente la acción de nuestro segue personalizado.
- Estamos reemplazando el controlador de la ventana `ContentViewController` por el que se define en el destino (destino) de segue.
- Estamos quitando el controlador de vista original para liberar memoria mediante el `RemoveFromParentViewController` método.

Para usar este nuevo tipo de segue en la Interface Builder de Xcode, debemos compilar primero la aplicación, luego cambiar a Xcode y agregar un nuevo segue entre dos escenas. Establezca el **estilo** en **Custom** y la **clase segue** en `ReplaceViewSegue` (el nombre de nuestra clase segue personalizada):

[![Establecimiento de la clase segue](indepth-images/sg01.png)](indepth-images/sg01.png#lightbox)

<a name="Triggered-Segues"></a>

## <a name="window-controllers"></a>Controladores de ventanas

Los controladores de ventanas contienen y controlan los distintos tipos de ventana que puede crear la aplicación macOS. En el caso de los guiones gráficos, tienen las siguientes características:

1. Deben proporcionar un controlador de vista de contenido. Este será el mismo controlador de vista de contenido que tiene la ventana secundaria.
2. La `Storyboard` propiedad contendrá el guión gráfico desde el que se cargó el controlador de ventana; de lo contrario, `null` si no se carga desde un guion gráfico.
3. Puede llamar al `DismissController` método para cerrar la ventana especificada y quitarla de la vista.

Al igual que los controladores de vista, los controladores de ventanas implementan los `PerformSegue` métodos, y, `PrepareForSegue` `ShouldPerformSegue` y se pueden usar como origen de una operación segue.

El controlador de ventana es responsable de las siguientes características de una aplicación macOS:

- Administran una ventana específica.
- Administran la barra de título y la barra de herramientas de la ventana (si está disponible).
- Administran el controlador de vista de contenido para mostrar el contenido de la ventana.

<a name="Gesture-Recognizers"></a>

## <a name="gesture-recognizers"></a>Reconocedores de gestos

Los reconocedores de gestos para macOS son prácticamente idénticos a sus homólogos en iOS y permiten al desarrollador agregar fácilmente gestos (como hacer clic en un botón del mouse) a elementos de la interfaz de usuario de la aplicación.

Sin embargo, si los gestos de iOS están determinados por el diseño de la aplicación (por ejemplo, al puntear en la pantalla con dos dedos), la mayoría de los gestos en macOS vienen determinados por el hardware.

Mediante el uso de reconocedores de gestos, puede reducir considerablemente la cantidad de código necesario para agregar interacciones personalizadas a un elemento en la interfaz de usuario. Como pueden determinar automáticamente entre dos clics dobles y únicos, hacer clic y arrastrar eventos, etc.

En lugar de reemplazar el `MouseDown` evento en el controlador de vista, debe usar un reconocedor de gestos para controlar el evento de entrada del usuario al trabajar con guiones gráficos.

Los siguientes reconocedores de gestos están disponibles en macOS:

- `NSClickGestureRecognizer` -Registra eventos de presionar y bajar del mouse.
- `NSPanGestureRecognizer` -Registra los eventos de presionar el botón del mouse, arrastrar y soltar.
- `NSPressGestureRecognizer` : Registra la acción de mantener presionado el botón del mouse durante un período de tiempo determinado.
- `NSMagnificationGestureRecognizer` : Registra un evento de ampliación del hardware del dispositivo de registro.
- `NSRotationGestureRecognizer` : Registra un evento de rotación del hardware de paneles de registro.

<a name="Using-Storyboard-References"></a>

## <a name="using-storyboard-references"></a>Usar referencias a guion gráfico

Una referencia de guion gráfico permite tomar un diseño de guion gráfico grande y complejo y dividirlo en guiones gráficos más pequeños a los que se hace referencia desde el original, con lo que se elimina la complejidad y se facilita el diseño y el mantenimiento de los guiones gráficos resultantes.

Además, una referencia de guion gráfico puede proporcionar un _delimitador_ a otra escena en el mismo guión gráfico o en una determinada escena en otro.

<a name="Referencing-an-External-Storyboard"></a>

### <a name="referencing-an-external-storyboard"></a>Referencia a un guión gráfico externo

Para agregar una referencia a un guion gráfico externo, haga lo siguiente:

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el nombre del proyecto y seleccione **Agregar**  >  **nuevo archivo..**  >  . **Mac**  >  **Guion gráfico**. Escriba un **nombre** para el nuevo guion gráfico y haga clic en el botón **nuevo** : 

    [![Agregar un nuevo guion gráfico](indepth-images/ref01.png)](indepth-images/ref01.png#lightbox)
2. En el **Explorador de soluciones**, haga doble clic en el nombre del nuevo guion gráfico para abrirlo para su edición en el Interface Builder de Xcode.
3. Diseñe el diseño de las escenas del nuevo guión gráfico como lo haría normalmente y guarde los cambios: 

    [![Diseñar la interfaz](indepth-images/ref02.png)](indepth-images/ref02.png#lightbox)
4. Cambie al guion gráfico al que va a agregar la referencia en el Interface Builder.
5. Arrastre una **referencia de guion gráfico** desde la **biblioteca de objetos** hasta el superficie de diseño: 

    [![Seleccionar una referencia de guion gráfico en la biblioteca](indepth-images/ref03.png)](indepth-images/ref03.png#lightbox)
6. En el **Inspector de atributos**, seleccione el nombre del **guion gráfico** que creó anteriormente: 

    [![Configuración de la referencia](indepth-images/ref04.png)](indepth-images/ref04.png#lightbox)
7. Control: haga clic en un widget de interfaz de usuario (como un botón) en una escena existente y cree un nuevo segue en la **referencia de guion gráfico** que acaba de crear.  En el menú emergente, seleccione **Mostrar** para completar el segue: 

    [![Establecimiento del tipo segue](indepth-images/ref06.png)](indepth-images/ref06.png#lightbox) 
8. Guarde los cambios en el guion gráfico.
9. Vuelva a Visual Studio para Mac para sincronizar los cambios.

Cuando se ejecuta la aplicación y el usuario hace clic en el elemento de la interfaz de usuario desde el que creó el segue, se mostrará el controlador de ventana inicial del guion gráfico externo especificado en la referencia del guión gráfico.

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard"></a>

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>Hacer referencia a una escena específica en un guión gráfico externo

Para agregar una referencia a una escena específica, un guion gráfico externo (y no el controlador de ventana inicial), haga lo siguiente:

1. En el **Explorador de soluciones**, haga doble clic en el guion gráfico externo para abrirlo para su edición en el Interface Builder de Xcode.
2. Agregue una nueva escena y diseñe su diseño como lo haría normalmente: 

    [![Diseñar el diseño en Xcode](indepth-images/ref07.png)](indepth-images/ref07.png#lightbox)
3. En el **Inspector de identidad**, escriba un **identificador de guión gráfico** para el controlador de ventana de la nueva escena: 

    [![Establecer el guión gráfico D en AltScene bajo Identity.](indepth-images/ref08.png)](indepth-images/ref08.png#lightbox)
4. Abra el guion gráfico al que va a agregar la referencia en Interface Builder.
5. Arrastre una **referencia de guion gráfico** desde la **biblioteca de objetos** hasta el superficie de diseño: 

    [![Seleccionar una referencia de guion gráfico de la biblioteca](indepth-images/ref03.png)](indepth-images/ref03.png#lightbox)
6. En el **Inspector de identidad**, seleccione el nombre del **guión gráfico** y el **ID. de referencia** (identificador de guión gráfico) de la escena que creó anteriormente: 

    [![Establecer la referencia en AltScene en la referencia de guion gráfico.](indepth-images/ref09.png)](indepth-images/ref09.png#lightbox)
7. Control: haga clic en un widget de interfaz de usuario (como un botón) en una escena existente y cree un nuevo segue en la **referencia de guion gráfico** que acaba de crear. En el menú emergente, seleccione **Mostrar** para completar el segue: 

    [![Establecimiento del tipo segue](indepth-images/ref06.png)](indepth-images/ref06.png#lightbox) 
8. Guarde los cambios en el guion gráfico.
9. Vuelva a Visual Studio para Mac para sincronizar los cambios.

Cuando se ejecuta la aplicación y el usuario hace clic en el elemento de la interfaz de usuario en el que ha creado el segue, se mostrará la escena con el **identificador de guión gráfico** especificado desde el guión gráfico externo especificado en la referencia de guion gráfico.

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard"></a>

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>Hacer referencia a una escena específica en el mismo guion gráfico

Para agregar una referencia a una escena específica del mismo guión gráfico, haga lo siguiente:

1. En el **Explorador de soluciones**, haga doble clic en el guion gráfico para abrirlo para su edición.
2. Agregue una nueva escena y diseñe su diseño como lo haría normalmente: 

    [![Editar el guion gráfico en Xcode](indepth-images/ref11.png)](indepth-images/ref11.png#lightbox)
3. En el **Inspector de identidad**, escriba un **identificador de guión gráfico** para el controlador de ventana de la nueva escena: 

    [![Establecer el guión gráfico D en IntScene bajo Identity.](indepth-images/ref12.png)](indepth-images/ref12.png#lightbox)
4. Arrastre una **referencia de guion gráfico** desde el **cuadro de herramientas** hasta el superficie de diseño: 

    [![Seleccionar una referencia de guion gráfico de la biblioteca](indepth-images/ref03.png)](indepth-images/ref03.png#lightbox)
5. En **Inspector de atributos**, seleccione **ID. de referencia** (identificador de guión gráfico) de la escena que creó anteriormente: 

    [![Establecer la referencia en IntScene en la referencia de guion gráfico.](indepth-images/ref13.png)](indepth-images/ref13.png#lightbox)
6. Control: haga clic en un widget de interfaz de usuario (como un botón) en una escena existente y cree un nuevo segue en la **referencia de guion gráfico** que acaba de crear. En el menú emergente, seleccione **Mostrar** para completar el segue: 

    [![Seleccionar el tipo de segue](indepth-images/ref06.png)](indepth-images/ref06.png#lightbox) 
7. Guarde los cambios en el guion gráfico.
8. Vuelva a Visual Studio para Mac para sincronizar los cambios.

Cuando se ejecuta la aplicación y el usuario hace clic en el elemento de la interfaz de usuario en el que ha creado el segue, se mostrará la escena con el **identificador de guión gráfico** especificado en el mismo guion gráfico especificado en la referencia de guion gráfico.

<a name="Complex-Storyboard-Example"></a>

## <a name="complex-storyboard-example"></a>Ejemplo de Storyboard complejo

Para ver un ejemplo complejo de cómo trabajar con guiones gráficos en una aplicación de Xamarin. Mac, consulte la [aplicación de ejemplo SourceWriter](/samples/xamarin/mac-samples/sourcewriter). SourceWriter es un editor de código fuente simple que proporciona compatibilidad con la finalización de código y el resaltado de sintaxis simple.

El código de SourceWriter se ha comentado completamente y, si están disponibles, se han proporcionado vínculos de métodos o tecnologías clave a información relevante en la documentación de las guías de Xamarin.Mac.

## <a name="related-links"></a>Vínculos relacionados

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabajar con Windows](~/mac/user-interface/window.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
- [Introducción a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)