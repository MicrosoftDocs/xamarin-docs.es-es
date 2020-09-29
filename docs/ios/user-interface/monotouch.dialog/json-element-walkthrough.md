---
title: Uso de JSON para crear una interfaz de usuario en Xamarin. iOS
description: MonoTouch. Dialog (MT. D) incluye compatibilidad con la generación dinámica de la interfaz de usuario a través de datos JSON. En este tutorial, se le guiará por el uso de JSONElement para crear una interfaz de usuario a partir de JSON que se incluye con una aplicación o que se carga desde una dirección URL remota.
ms.prod: xamarin
ms.assetid: E353DF14-51D7-98E3-59EA-16683C770C23
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: c19cf373a89798703224574fe13724dfbade33bb
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436690"
---
# <a name="using-json-to-create-a-user-interface-in-xamarinios"></a>Uso de JSON para crear una interfaz de usuario en Xamarin. iOS

_MonoTouch. Dialog (MT. D) incluye compatibilidad con la generación dinámica de la interfaz de usuario a través de datos JSON. En este tutorial, se le guiará por el uso de JSONElement para crear una interfaz de usuario a partir de JSON que se incluye con una aplicación o que se carga desde una dirección URL remota._

Módulo. D admite la creación de interfaces de usuario declaradas en JSON. Cuando los elementos se declaran mediante JSON, MT. D creará automáticamente los elementos asociados. El JSON se puede cargar desde un archivo local, una instancia analizada `JsonObject` o incluso una dirección URL remota.

Módulo. D admite toda la gama de características que están disponibles en la API Elements al usar JSON. Por ejemplo, la aplicación de la siguiente captura de pantalla se declara completamente mediante JSON:

[ ![ Por ejemplo, la aplicación de esta captura de pantalla se declara completamente mediante JSON](json-element-walkthrough-images/01-load-from-file.png)](json-element-walkthrough-images/01-load-from-file.png#lightbox) [ ![ ; por ejemplo, la aplicación de esta captura de pantalla se declara completamente mediante JSON](json-element-walkthrough-images/01-load-from-file.png)](json-element-walkthrough-images/01-load-from-file.png#lightbox)

Volvamos al ejemplo en el tutorial sobre los [elementos](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) de la API, en el que se muestra cómo agregar una pantalla de detalles de tarea mediante JSON.

## <a name="setting-up-mtd"></a>Configuración de MT. D

Módulo. D se distribuye con Xamarin. iOS. Para usarlo, haga clic con el botón derecho en el nodo **referencias** de un proyecto de Xamarin. iOS en Visual Studio 2017 o Visual Studio para Mac y agregue una referencia al ensamblado **MonoTouch. Dialog-1** . A continuación, agregue `using MonoTouch.Dialog` las instrucciones en el código fuente según sea necesario.

## <a name="json-walkthrough"></a>Tutorial de JSON

El ejemplo de este tutorial permite crear tareas. Cuando se selecciona una tarea en la primera pantalla, se presenta una pantalla de detalles como se muestra a continuación:

 [![Cuando se selecciona una tarea en la primera pantalla, se presenta una pantalla de detalles como se muestra](json-element-walkthrough-images/03-task-list.png)](json-element-walkthrough-images/03-task-list.png#lightbox)

## <a name="creating-the-json"></a>Creación de JSON

En este ejemplo, se cargará el JSON desde un archivo en el proyecto denominado `task.json` . Módulo. D espera que el JSON se ajuste a una sintaxis que refleje la API de los elementos. Al igual que con la API Elements del código, cuando se usa JSON, se declaran secciones y, dentro de esas secciones, se agregan elementos. Para declarar secciones y elementos en JSON, usamos las cadenas "Sections" y "Elements", respectivamente, como las claves. Para cada elemento, el tipo de elemento asociado se establece mediante la `type` clave. Cada propiedad de elementos restantes se establece con el nombre de la propiedad como clave.

Por ejemplo, en el siguiente código JSON se describen las secciones y los elementos de los detalles de la tarea:

```json
{
    "title": "Task",
    "sections": [
        {
            "elements" : [
                {
                    "id" : "task-description",
                    "type": "entry",
                    "placeholder": "Enter task description"
                },
                {
                    "id" : "task-duedate",
                    "type": "date",
                    "caption": "Due Date",
                    "value": "00:00"
                }
            ]
        }
    ]
}
```

Observe que el código JSON anterior incluye un identificador para cada elemento. Cualquier elemento puede incluir un identificador, para hacer referencia a él en tiempo de ejecución. Veremos cómo se usa en un momento en el que se muestra cómo cargar el archivo JSON en el código.

## <a name="loading-the-json-in-code"></a>Cargar el archivo JSON en el código

Una vez que se ha definido el JSON, es necesario cargarlo en MT. D usar la `JsonElement` clase. Suponiendo que se ha agregado al proyecto un archivo con el JSON que hemos creado anteriormente, con el nombre sample.jsy dado una acción de compilación de contenido, la carga de `JsonElement` es tan sencilla como llamar a la siguiente línea de código:

```csharp
var taskElement = JsonElement.FromFile ("task.json");
```

Puesto que estamos agregando esto a petición cada vez que se crea una tarea, podemos modificar el botón en el que se hizo clic desde el ejemplo anterior de la API de elementos de la siguiente manera:

```csharp
_addButton.Clicked += (sender, e) => {
    ++n;

    var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

    var taskElement = JsonElement.FromFile ("task.json");

    _rootElement [0].Add (taskElement);
};
```

## <a name="accessing-elements-at-runtime"></a>Obtener acceso a elementos en tiempo de ejecución

Recuerde que hemos agregado un identificador a ambos elementos cuando los declaró en el archivo JSON. Podemos usar la propiedad ID para tener acceso a cada elemento en tiempo de ejecución para modificar sus propiedades en el código. Por ejemplo, el código siguiente hace referencia a los elementos de entrada y fecha para establecer los valores del objeto de tarea:

```csharp
_addButton.Clicked += (sender, e) => {
    ++n;

    var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

    var taskElement = JsonElement.FromFile ("task.json");

    taskElement.Caption = task.Name;

    var description = taskElement ["task-description"] as EntryElement;

    if (description != null) {
        description.Caption = task.Name;
        description.Value = task.Description;       
    }

    var duedate = taskElement ["task-duedate"] as DateElement;

    if (duedate != null) {                
        duedate.DateValue = task.DueDate;
    }
    _rootElement [0].Add (taskElement);
};
```

## <a name="loading-json-from-a-url"></a>Carga de JSON desde una dirección URL

Módulo. D también admite la carga dinámica de JSON desde una dirección URL externa simplemente pasando la dirección URL al constructor de `JsonElement` . Módulo. D expandirá la jerarquía declarada en JSON a petición mientras navega entre pantallas. Por ejemplo, considere un archivo JSON como el que se encuentra debajo de la raíz del servidor Web local:

```json
{
    "type": "root",
    "title": "home",
    "sections": [
        {
            "header": "Nested view!",
            "elements": [
                {
                    "type": "boolean",
                    "caption": "Just a boolean",
                    "id": "first-boolean",
                    "value": false
                },
                {
                    "type": "string",
                    "caption": "Welcome to the nested controller"
                }
            ]
        }
    ]
}
```

Podemos cargarlo con `JsonElement` como en el código siguiente:

```csharp
_rootElement = new RootElement ("Json Example") {
    new Section ("") {
        new JsonElement ("Load from url", "http://localhost/sample.json")
    }
};
```

En tiempo de ejecución, MT recuperará y analizará el archivo. D cuando el usuario navega a la segunda vista, como se muestra en la siguiente captura de pantalla:

 [![MT recuperará y analizará el archivo. D cuando el usuario navega a la segunda vista](json-element-walkthrough-images/04-json-web-example.png)](json-element-walkthrough-images/04-json-web-example.png#lightbox)

## <a name="summary"></a>Resumen

En este artículo se ha mostrado cómo crear una interfaz Using con MT. D desde JSON. Se mostró cómo cargar JSON incluido en un archivo con la aplicación, así como desde una dirección URL remota. También se ha mostrado cómo acceder a los elementos descritos en JSON en tiempo de ejecución.

## <a name="related-links"></a>Vínculos relacionados

- [MTDJsonDemo (ejemplo)](/samples/xamarin/ios-samples/mtdjsondemo)
- [Introducción a MonoTouch. Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Tutorial de API de elementos](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Tutorial de la API de reflexión](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Cuadro de diálogo MonoTouch en github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Aplicación TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Referencia de la clase UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referencia de la clase UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)