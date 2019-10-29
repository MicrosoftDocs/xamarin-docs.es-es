---
title: Creación de una aplicación Xamarin. iOS mediante la API Elements
description: Este artículo se basa en la información que se presenta en el artículo del cuadro de diálogo Introducción al monotoque. Presenta un tutorial que muestra cómo usar el MonoTouch. Dialog (MT. D) elementos API para empezar a crear rápidamente una aplicación con MT. D.
ms.prod: xamarin
ms.assetid: F1124734-DF44-F1F3-0832-46F52A788CDC
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: 2258b2e8451f896aff59c89478833976ef7086e3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73002367"
---
# <a name="creating-a-xamarinios-application-using-the-elements-api"></a>Creación de una aplicación Xamarin. iOS mediante la API Elements

_Este artículo se basa en la información que se presenta en el artículo del cuadro de diálogo Introducción al monotoque. Presenta un tutorial que muestra cómo usar el MonoTouch. Dialog (MT. D) elementos API para empezar a crear rápidamente una aplicación con MT. D._

En este tutorial, usaremos el MT. D elementos API para crear un estilo principal-detalle de la aplicación que muestra una lista de tareas. Cuando el usuario selecciona el botón **+** en la barra de navegación, se agregará una nueva fila a la tabla para la tarea. Al seleccionar la fila, se navegará a la pantalla de detalles que nos permite actualizar la descripción de la tarea y la fecha de vencimiento, como se muestra a continuación:

[![](elements-api-walkthrough-images/01-task-list-app.png "Selecting the row will navigate to the detail screen that allows us to update the task description and the due date")](elements-api-walkthrough-images/01-task-list-app.png#lightbox)

## <a name="setting-up-mtd"></a>Configuración de MT. D

Módulo. D se distribuye con Xamarin. iOS. Para usarlo, haga clic con el botón derecho en el nodo **referencias** de un proyecto de Xamarin. iOS en Visual Studio 2017 o Visual Studio para Mac y agregue una referencia al ensamblado **MonoTouch. Dialog-1** . A continuación, agregue `using MonoTouch.Dialog` instrucciones en el código fuente según sea necesario.

## <a name="elements-api-walkthrough"></a>Tutorial de API de elementos

En el artículo sobre el [cuadro de diálogo Introducción al monotoque](~/ios/user-interface/monotouch.dialog/index.md) , hemos conseguido una comprensión sólida de las distintas partes de Mt. D. Vamos a usar la API Elements para colocarlas todas juntas en una aplicación.

## <a name="setting-up-the-multi-screen-application"></a>Configuración de la aplicación de varias pantallas

Para iniciar el proceso de creación de pantalla, MonoTouch. Dialog crea un `DialogViewController`y, a continuación, agrega un `RootElement`.

Para crear una aplicación de varias pantallas con MonoTouch. Dialog, es necesario:

1. Cree un control `UINavigationController.`
1. Cree un control `DialogViewController.`
1. Agregue el `DialogViewController` como la raíz del `UINavigationController.` 
1. Agregar un `RootElement` al `DialogViewController.`
1. Agregue `Sections` y `Elements` al `RootElement.` 

### <a name="using-a-uinavigationcontroller"></a>Uso de un UINavigationController

Para crear una aplicación de estilo de navegación, es necesario crear una `UINavigationController`y, a continuación, agregarla como `RootViewController` en el método `FinishedLaunching` de la `AppDelegate`. Para que el `UINavigationController` funcione con MonoTouch. Dialog, agregamos un `DialogViewController` al `UINavigationController` como se muestra a continuación:

```csharp
public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
    _window = new UIWindow (UIScreen.MainScreen.Bounds);
            
    _rootElement = new RootElement ("To Do List"){new Section ()};

    // code to create screens with MT.D will go here …

    _rootVC = new DialogViewController (_rootElement);
    _nav = new UINavigationController (_rootVC);
    _window.RootViewController = _nav;
    _window.MakeKeyAndVisible ();
            
    return true;
}
```

El código anterior crea una instancia de un `RootElement` y lo pasa al `DialogViewController`. El `DialogViewController` siempre tiene una `RootElement` en la parte superior de su jerarquía. En este ejemplo, el `RootElement` se crea con la cadena "lista de tareas pendientes", que sirve como título en la barra de navegación del controlador de navegación. En este punto, la ejecución de la aplicación presentaría la pantalla que se muestra a continuación:

 [![](elements-api-walkthrough-images/02-to-do-list-screen-.png "Running the application will present the screen shown here")](elements-api-walkthrough-images/02-to-do-list-screen-.png#lightbox)

Veamos cómo usar la estructura jerárquica del cuadro de diálogo MonoTouch. de `Sections` y `Elements` para agregar más pantallas.

### <a name="creating-the-dialog-screens"></a>Crear las pantallas de cuadro de diálogo

Una `DialogViewController` es una subclase `UITableViewController` que MonoTouch. Dialog usa para agregar pantallas. MonoTouch. Dialog crea pantallas agregando una `RootElement` a una `DialogViewController`, como vimos anteriormente. El `RootElement` puede tener `Section` instancias de que representan las secciones de una tabla.
Las secciones se componen de elementos, otras secciones o incluso otros `RootElements`. Al anidar `RootElements`, MonoTouch. Dialog crea automáticamente una aplicación de estilo de navegación, como veremos a continuación.

### <a name="using-dialogviewcontroller"></a>Usar DialogViewController

El `DialogViewController`, que es una subclase `UITableViewController`, tiene una `UITableView` como vista. En este ejemplo, queremos agregar elementos a la tabla cada vez que se puntee en el botón **+** . Como el `DialogViewController` se agregó a un `UINavigationController`, podemos usar la propiedad `RightBarButton` del `NavigationItem`para agregar el botón **+** , como se muestra a continuación:

```csharp
_addButton = new UIBarButtonItem (UIBarButtonSystemItem.Add);
_rootVC.NavigationItem.RightBarButtonItem = _addButton;
```

Cuando creamos el `RootElement` anterior, le pasamos una sola instancia de `Section` para que se pudieran agregar elementos cuando el usuario puntee en el botón **+** . Podemos usar el código siguiente para hacerlo en el controlador de eventos del botón:

```csharp
_addButton.Clicked += (sender, e) => {                
    ++n;
                
    var task = new Task{Name = "task " + n, DueDate = DateTime.Now};
                
    var taskElement = new RootElement (task.Name) {
        new Section () {
            new EntryElement (task.Name, "Enter task description", task.Description)
        },
        new Section () {
            new DateElement ("Due Date", task.DueDate)
        }
    };
    _rootElement [0].Add (taskElement);
};
```

Este código crea un nuevo objeto `Task` cada vez que se puntea el botón. A continuación se muestra la implementación simple de la clase `Task`:

```csharp
public class Task
{   
    public Task ()
    {
    }
      
    public string Name { get; set; }
        
    public string Description { get; set; }

    public DateTime DueDate { get; set; }
}
```

La propiedad `Name` de la tarea se usa para crear el título del `RootElement`junto con una variable de contador denominada `n` que se incrementa para cada nueva tarea. MonoTouch. Dialog convierte los elementos en las filas que se agregan a la `TableView` cuando se agrega cada `taskElement`.

## <a name="presenting-and-managing-dialog-screens"></a>Presentación y administración de pantallas de cuadro de diálogo

Usamos una `RootElement` para que MonoTouch. Dialog cree automáticamente una nueva pantalla para los detalles de cada tarea y navegue hasta ella cuando se selecciona una fila.

La pantalla de detalles de tarea se compone de dos secciones: cada una de estas secciones contiene un solo elemento. El primer elemento se crea a partir de una `EntryElement` para proporcionar una fila modificable para la propiedad `Description` de la tarea. Cuando se selecciona el elemento, se presenta un teclado para la edición de texto, como se muestra a continuación:

 [![](elements-api-walkthrough-images/03-create-task.png "When the element is selected, a keyboard for text editing is presented as shown")](elements-api-walkthrough-images/03-create-task.png#lightbox)

La segunda sección contiene una `DateElement` que nos permite administrar la propiedad `DueDate` de la tarea. Al seleccionar la fecha se carga automáticamente un selector de fecha, como se muestra a continuación:

 [![](elements-api-walkthrough-images/04-date-picker.png "Selecting the date automatically loads a date picker as")](elements-api-walkthrough-images/04-date-picker.png#lightbox)

En los casos `EntryElement` y `DateElement` (o para cualquier elemento de entrada de datos en MonoTouch. Dialog), cualquier cambio en los valores se conserva automáticamente. Podemos mostrar esto editando la fecha y navegando hacia atrás y hacia delante entre la pantalla raíz y varios detalles de la tarea, donde se conservan los valores de las pantallas de detalles.

## <a name="summary"></a>Resumen

En este artículo se ha presentado un tutorial que muestra cómo usar la API de elementos MonoTouch. Dialog. Se han tratado los pasos básicos para crear una aplicación de varias pantallas con MT. D, incluido cómo usar una `DialogViewController` y cómo agregar elementos y secciones para crear pantallas. Además, se ha mostrado cómo usar MT. D junto con un `UINavigationController`.

## <a name="related-links"></a>Vínculos relacionados

- [MTDWalkthrough (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/mtdwalkthrough)
- [Introducción a MonoTouch. Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Tutorial de la API de reflexión](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Tutorial de elementos JSON](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Cuadro de diálogo MonoTouch en github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Aplicación TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Referencia de la clase UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referencia de la clase UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
