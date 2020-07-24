---
title: Xamarin.FormsIntroducción a servicios Web
description: En esta guía se proporciona un tutorial de la Xamarin.Forms aplicación de ejemplo que muestra cómo comunicarse con distintos servicios Web. Aunque cada servicio Web utiliza una aplicación de ejemplo independiente, son funcionalmente similares y comparten clases comunes.
ms.prod: xamarin
ms.assetid: A3FEB262-0D79-42E6-8F8B-A565618C490B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cd9815c6448efae0bb0af982c8807c47263ae13e
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938218"
---
# <a name="xamarinforms-web-services-introduction"></a>Xamarin.FormsIntroducción a servicios Web

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_En este tema se proporciona un tutorial de la Xamarin.Forms aplicación de ejemplo que muestra cómo comunicarse con distintos servicios Web. Aunque cada servicio Web utiliza una aplicación de ejemplo independiente, son funcionalmente similares y comparten clases comunes._

La aplicación de lista de tareas pendientes de ejemplo que se describe a continuación se usa para mostrar cómo acceder a los distintos tipos de back-ends de servicio Web con Xamarin.Forms . Proporciona funcionalidad para:

- Ver una lista de tareas.
- Agregar, editar y eliminar tareas.
- Establezca el estado de una tarea en "listo".
- Diga el nombre de la tarea y los campos de notas.

En todos los casos, las tareas se almacenan en un back-end al que se tiene acceso a través de un servicio Web.

Cuando se inicia la aplicación, se muestra una página que muestra las tareas recuperadas del servicio Web y permite al usuario crear una nueva tarea. Al hacer clic en una tarea, la aplicación se desplaza a una segunda página en la que se puede editar, guardar, eliminar e decir la tarea. A continuación se muestra la aplicación final:

![Aplicación todo: primera página tarea ](introduction-images/app-example-1.png)
 ![ pendiente-segunda página](introduction-images/app-example-2.png)

En cada tema de esta guía se proporciona un vínculo de descarga a una versión *diferente* de la aplicación que muestra un tipo específico de back-end de servicios Web. Descargue el código de ejemplo correspondiente en la página relacionada con cada estilo de servicio Web.

## <a name="understand-the-application-anatomy"></a>Descripción de la anatomía de la aplicación

El proyecto de código compartido para cada aplicación de ejemplo consta de tres carpetas principales:

|Carpeta|Propósito|
|--- |--- |
|data|Contiene las clases e interfaces utilizadas para administrar los elementos de datos y comunicarse con el servicio Web. Como mínimo, incluye la `TodoItemManager` clase, que se expone a través de una propiedad en la `App` clase para invocar las operaciones del servicio Web.|
|Modelos|Contiene las clases de modelo de datos para la aplicación. Como mínimo, incluye la `TodoItem` clase, que modela un único elemento de datos utilizado por la aplicación. La carpeta también puede incluir cualquier clase adicional que se use para modelar los datos de usuario.|
|Vistas|Contiene las páginas de la aplicación. Normalmente, se compone de `TodoListPage` las `TodoItemPage` clases y y cualquier clase adicional que se use para la autenticación.|

El proyecto de código compartido de cada aplicación también consta de varios archivos importantes:

|Archivo|Propósito|
|--- |--- |
|Constants.cs|La `Constants` clase, que especifica las constantes utilizadas por la aplicación para comunicarse con el servicio Web. Estas constantes requieren actualización para tener acceso al servicio de back-end personal creado en un proveedor.|
|ITextToSpeech.cs|La `ITextToSpeech` interfaz, que especifica que el `Speak` método debe proporcionarse mediante cualquier clase de implementación.|
|Todo.cs|La `App` clase que es responsable de crear instancias de la primera página que mostrará la aplicación en cada plataforma y de la `TodoItemManager` clase que se utiliza para invocar las operaciones del servicio Web.|

### <a name="view-pages"></a>Ver páginas

La mayoría de las aplicaciones de ejemplo contienen al menos dos páginas:

- **TodoListPage** : esta página muestra una lista de `TodoItem` instancias y un icono de marca si la `TodoItem.Done` propiedad es `true` . Al hacer clic en un elemento, navega hasta el `TodoItemPage` . Además, se pueden crear nuevos elementos haciendo clic en el *+* símbolo.
- **TodoItemPage** : en esta página se muestran los detalles de la seleccionada `TodoItem` y se permite editar, guardar, eliminar y hablar.

Además, algunas aplicaciones de ejemplo contienen páginas adicionales que se usan para administrar el proceso de autenticación del usuario.

### <a name="model-the-data"></a>Modelado de los datos

Cada aplicación de ejemplo utiliza la `TodoItem` clase para modelar los datos que se muestran y se envían al servicio web para su almacenamiento. En el ejemplo de código siguiente se muestra la clase `TodoItem`:

```csharp
public class TodoItem
{
    public string ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

La `ID` propiedad se usa para identificar de forma única cada `TodoItem` instancia y la usa cada servicio web para identificar los datos que se van a actualizar o eliminar.

### <a name="invoke-web-service-operations"></a>Invocar operaciones de servicio Web

Se tiene acceso a las operaciones del servicio Web a través de la `TodoItemManager` clase y se puede tener acceso a una instancia de la clase a través de la `App.TodoManager` propiedad. La `TodoItemManager` clase proporciona los siguientes métodos para invocar las operaciones del servicio Web:

- **GetTasksAsync** : este método se usa para rellenar el `ListView` control en `TodoListPage` con las `TodoItem` instancias recuperadas del servicio Web.
- **SaveTaskAsync** : este método se usa para crear o actualizar una `TodoItem` instancia en el servicio Web.
- **DeleteTaskAsync** : este método se usa para eliminar una `TodoItem` instancia en el servicio Web.

Además, algunas aplicaciones de ejemplo contienen métodos adicionales en la `TodoItemManager` clase, que se usan para administrar el proceso de autenticación del usuario.

En lugar de invocar directamente las operaciones de servicio Web, los `TodoItemManager` métodos invocan métodos en una clase dependiente que se inserta en el `TodoItemManager` constructor. Por ejemplo, una aplicación de ejemplo inyecta la `RestService` clase en el `TodoItemManager` constructor para proporcionar la implementación que usa las API de REST para tener acceso a los datos.

## <a name="related-links"></a>Vínculos relacionados

- [ASMX (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [WCF (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)
- [REST (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)
