---
title: Creación de una aplicación Xamarin. iOS mediante la API Elements
description: Este artículo se basa en la información que se presenta en el artículo del cuadro de diálogo Introducción al monotoque. Presenta un tutorial que muestra cómo usar el MonoTouch. Dialog (MT. D) elementos API para empezar a crear rápidamente una aplicación con MT. D.
ms.prod: xamarin
ms.assetid: F1124734-DF44-F1F3-0832-46F52A788CDC
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: 82320b069156828101d17e79ca48a8933b8a8777
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655077"
---
# <a name="creating-a-xamarinios-application-using-the-elements-api"></a>Creación de una aplicación Xamarin. iOS mediante la API Elements

_Este artículo se basa en la información que se presenta en el artículo del cuadro de diálogo Introducción al monotoque. Presenta un tutorial que muestra cómo usar el MonoTouch. Dialog (MT. D) elementos API para empezar a crear rápidamente una aplicación con MT. D._

En este tutorial, usaremos el MT. D elementos API para crear un estilo principal-detalle de la aplicación que muestra una lista de tareas. Cuando el usuario selecciona el <span class="ui">+</span> botón en la barra de navegación, se agregará una nueva fila a la tabla para la tarea. Al seleccionar la fila, se navegará a la pantalla de detalles que nos permite actualizar la descripción de la tarea y la fecha de vencimiento, como se muestra a continuación:

 [![](elements-api-walkthrough-images/01-task-list-app.png "Al seleccionar la fila, se navegará a la pantalla de detalles que nos permite actualizar la descripción de la tarea y la fecha de vencimiento.")](elements-api-walkthrough-images/01-task-list-app.png#lightbox)

 ## <a name="setting-up-mtd"></a>Configuración de MT. D

Módulo. D se distribuye con Xamarin. iOS. Para usarlo, haga clic con el botón derecho en el nodo **referencias** de un proyecto de Xamarin. iOS en Visual Studio 2017 o Visual Studio para Mac y agregue una referencia al ensamblado **MonoTouch. Dialog-1** . A continuación, `using MonoTouch.Dialog` Agregue las instrucciones en el código fuente según sea necesario.

## <a name="elements-api-walkthrough"></a>Tutorial de API de elementos

En el artículo sobre el [cuadro de diálogo Introducción al monotoque](~/ios/user-interface/monotouch.dialog/index.md) , hemos conseguido una comprensión sólida de las distintas partes de Mt. D. Vamos a usar la API Elements para colocarlas todas juntas en una aplicación.

## <a name="setting-up-the-multi-screen-application"></a>Configuración de la aplicación de varias pantallas

Para iniciar el proceso de creación de la pantalla, MonoTouch. `DialogViewController`Dialog crea y, a `RootElement`continuación, agrega un.

Para crear una aplicación de varias pantallas con MonoTouch. Dialog, es necesario:

1.  Cree un control `UINavigationController.`
1.  Cree un control `DialogViewController.`
1.  `DialogViewController` Agregue como la raíz del`UINavigationController.` 
1.  Agregue un `RootElement` al`DialogViewController.`
1.  Agregue `Sections` y `Elements` al`RootElement.` 

### <a name="using-a-uinavigationcontroller"></a>Uso de un UINavigationController

Para crear una aplicación de estilo de navegación, es necesario crear un `UINavigationController`y, después, agregarlo `RootViewController` como `AppDelegate`en el `FinishedLaunching` método de. Para que el `UINavigationController` trabajo se realice con MonoTouch. Dialog, `DialogViewController` agregamos a `UINavigationController` , tal y como se muestra a continuación:

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

El código anterior crea una instancia de `RootElement` y la `DialogViewController`pasa al. Siempre tiene una `RootElement` en la parte superior de su jerarquía. `DialogViewController` En este ejemplo, `RootElement` se crea con la cadena "lista de tareas pendientes", que sirve como título en la barra de navegación del controlador de navegación. En este punto, la ejecución de la aplicación presentaría la pantalla que se muestra a continuación:

 [![](elements-api-walkthrough-images/02-to-do-list-screen-.png "Al ejecutar la aplicación, se mostrará la pantalla que se muestra aquí.")](elements-api-walkthrough-images/02-to-do-list-screen-.png#lightbox)

Veamos cómo usar la estructura jerárquica de MonoTouch. Dialog de `Sections` y `Elements` para agregar más pantallas.

### <a name="creating-the-dialog-screens"></a>Crear las pantallas de cuadro de diálogo

Una `DialogViewController` es una `UITableViewController` subclase que MonoTouch. Dialog usa para agregar pantallas. MonoTouch. Dialog crea pantallas agregando un `RootElement` a un `DialogViewController`, como vimos anteriormente. `RootElement` Puede tener`Section` instancias que representan las secciones de una tabla.
Las secciones se componen de elementos, otras secciones o incluso otros `RootElements`. Mediante el anidamiento `RootElements`, MonoTouch. Dialog crea automáticamente una aplicación de estilo de navegación, como veremos a continuación.

### <a name="using-dialogviewcontroller"></a>Usar DialogViewController

, Que es una `UITableViewController` subclase, tiene `UITableView` como vista. `DialogViewController` En este ejemplo, queremos agregar elementos a la tabla cada vez que se puntee en el <span class="ui">+</span> botón. `NavigationItem` <span class="ui">+</span> `RightBarButton` Dado que `UINavigationController`se ha agregado a, se puede usar la propiedad de para agregar el botón, como se muestra a continuación: `DialogViewController`

```csharp
_addButton = new UIBarButtonItem (UIBarButtonSystemItem.Add);
_rootVC.NavigationItem.RightBarButtonItem = _addButton;
```

Cuando creamos el `RootElement` anterior, pasamos una sola `Section` instancia para que podamos agregar elementos cuando el usuario Puntee <span class="ui">+</span> en el botón. Podemos usar el código siguiente para hacerlo en el controlador de eventos del botón:

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

Este código crea un nuevo `Task` objeto cada vez que se puntea en el botón. A continuación se muestra la implementación simple de `Task` la clase:

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

La propiedad de `Name` la tarea se usa para crear `RootElement`el título de la tarea junto con una `n` variable de contador denominada que se incrementa para cada nueva tarea. MonoTouch. Dialog convierte los elementos en las filas que se agregan a `TableView` cuando se `taskElement` agrega cada uno de ellos.

## <a name="presenting-and-managing-dialog-screens"></a>Presentación y administración de pantallas de cuadro de diálogo

Usamos un `RootElement` objeto para que MonoTouch. Dialog cree automáticamente una nueva pantalla para los detalles de cada tarea y navegue hasta ella cuando se selecciona una fila.

La pantalla de detalles de tarea se compone de dos secciones: cada una de estas secciones contiene un solo elemento. El primer elemento se crea `EntryElement` a partir de para proporcionar una fila modificable para la propiedad de `Description` la tarea. Cuando se selecciona el elemento, se presenta un teclado para la edición de texto, como se muestra a continuación:

 [![](elements-api-walkthrough-images/03-create-task.png "Cuando se selecciona el elemento, se presenta un teclado para la edición de texto como se muestra")](elements-api-walkthrough-images/03-create-task.png#lightbox)

La segunda sección contiene un `DateElement` que nos permite administrar la propiedad de `DueDate` la tarea. Al seleccionar la fecha se carga automáticamente un selector de fecha, como se muestra a continuación:

 [![](elements-api-walkthrough-images/04-date-picker.png "Al seleccionar la fecha, se carga automáticamente un selector de fecha como")](elements-api-walkthrough-images/04-date-picker.png#lightbox)

En los `EntryElement` casos y `DateElement` (o para cualquier elemento de entrada de datos en MonoTouch. Dialog), cualquier cambio en los valores se conserva automáticamente. Podemos mostrar esto editando la fecha y navegando hacia atrás y hacia delante entre la pantalla raíz y varios detalles de la tarea, donde se conservan los valores de las pantallas de detalles.

## <a name="summary"></a>Resumen

En este artículo se ha presentado un tutorial que muestra cómo usar la API de elementos MonoTouch. Dialog. Se han tratado los pasos básicos para crear una aplicación de varias pantallas con MT. D, incluido cómo usar `DialogViewController` y cómo agregar elementos y secciones para crear pantallas. Además, se ha mostrado cómo usar MT. D junto con un `UINavigationController`.

## <a name="related-links"></a>Vínculos relacionados

- [MTDWalkthrough (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/mtdwalkthrough)
- [Introducción a MonoTouch. Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Tutorial de la API de reflexión](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Tutorial de elementos JSON](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Cuadro de diálogo MonoTouch en github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Aplicación TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Referencia de la clase UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referencia de la clase UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
