---
title: Introducción a los servicios Web de Xamarin. Forms
description: En esta guía se proporciona un tutorial de la aplicación de ejemplo de Xamarin. Forms que muestra cómo comunicarse con distintos servicios Web. Aunque cada servicio Web utiliza una aplicación de ejemplo independiente, son funcionalmente similares y comparten clases comunes.
ms.prod: xamarin
ms.assetid: A3FEB262-0D79-42E6-8F8B-A565618C490B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: bbeab6a6ab0d4a9d0e3a962240317fc0d54f9e25
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "68656636"
---
# <a name="xamarinforms-web-services-introduction"></a>Introducción a los servicios Web de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_En este tema se proporciona un tutorial de la aplicación de ejemplo de Xamarin. Forms que muestra cómo comunicarse con distintos servicios Web. Aunque cada servicio Web utiliza una aplicación de ejemplo independiente, son funcionalmente similares y comparten clases comunes._

La aplicación de lista de tareas pendientes de ejemplo que se describe a continuación se usa para mostrar cómo acceder a los distintos tipos de back-ends de servicio Web con Xamarin. Forms. Proporciona funcionalidad para:

- Ver una lista de tareas.
- Agregar, editar y eliminar tareas.
- Establezca el estado de una tarea en "listo".
- Diga el nombre de la tarea y los campos de notas.

En todos los casos, las tareas se almacenan en un back-end al que se tiene acceso a través de un servicio Web.

Cuando se inicia la aplicación, se muestra una página que muestra las tareas recuperadas del servicio Web y permite al usuario crear una nueva tarea. Al hacer clic en una tarea, la aplicación se desplaza a una segunda página en la que se puede editar, guardar, eliminar e decir la tarea. A continuación se muestra la aplicación final:

![](introduction-images/app-example-1.png "Todo application - first page")
![](introduction-images/app-example-2.png "Todo application - second page")

En cada tema de esta guía se proporciona un vínculo de descarga a una versión *diferente* de la aplicación que muestra un tipo específico de back-end de servicios Web. Descargue el código de ejemplo correspondiente en la página relacionada con cada estilo de servicio Web.

## <a name="understand-the-application-anatomy"></a>Descripción de la anatomía de la aplicación

El proyecto de código compartido para cada aplicación de ejemplo consta de tres carpetas principales:

|Carpeta|Finalidad|
|--- |--- |
|Datos|Contiene las clases e interfaces utilizadas para administrar los elementos de datos y comunicarse con el servicio Web. Como mínimo, incluye la clase `TodoItemManager`, que se expone a través de una propiedad en la clase `App` para invocar las operaciones del servicio Web.|
|Modelos|Contiene las clases de modelo de datos para la aplicación. Como mínimo, incluye la clase `TodoItem`, que modela un único elemento de datos utilizado por la aplicación. La carpeta también puede incluir cualquier clase adicional que se use para modelar los datos de usuario.|
|Vistas|Contiene las páginas de la aplicación. Normalmente, se compone de las clases `TodoListPage` y `TodoItemPage` y de cualquier clase adicional que se use para la autenticación.|

El proyecto de código compartido de cada aplicación también consta de varios archivos importantes:

|Archivo|Finalidad|
|--- |--- |
|Constants.cs|La clase `Constants`, que especifica las constantes utilizadas por la aplicación para comunicarse con el servicio Web. Estas constantes requieren actualización para tener acceso al servicio de back-end personal creado en un proveedor.|
|ITextToSpeech.cs|La interfaz de `ITextToSpeech`, que especifica que el método de `Speak` debe ser proporcionado por cualquier clase de implementación.|
|Todo.cs|La clase `App` que es responsable de crear instancias de la primera página que mostrará la aplicación en cada plataforma y de la clase `TodoItemManager` que se usa para invocar las operaciones del servicio Web.|

### <a name="view-pages"></a>Ver páginas

La mayoría de las aplicaciones de ejemplo contienen al menos dos páginas:

- **TodoListPage** : esta página muestra una lista de instancias de `TodoItem` y un icono de marca si la propiedad `TodoItem.Done` está `true`. Al hacer clic en un elemento, navega hasta el `TodoItemPage`. Además, se pueden crear nuevos elementos haciendo clic en el símbolo de *+* .
- **TodoItemPage** : en esta página se muestran los detalles de la `TodoItem` seleccionada y se permite editar, guardar, eliminar e decir.

Además, algunas aplicaciones de ejemplo contienen páginas adicionales que se usan para administrar el proceso de autenticación del usuario.

### <a name="model-the-data"></a>Modelado de los datos

Cada aplicación de ejemplo utiliza la clase `TodoItem` para modelar los datos que se muestran y se envían al servicio web para su almacenamiento. En el ejemplo de código siguiente se muestra la clase `TodoItem`:

```csharp
public class TodoItem
{
    public string ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

La propiedad `ID` se utiliza para identificar de forma única cada instancia de `TodoItem` y la usa cada servicio web para identificar los datos que se van a actualizar o eliminar.

### <a name="invoke-web-service-operations"></a>Invocar operaciones de servicio Web

El acceso a las operaciones del servicio Web se realiza a través de la clase `TodoItemManager` y se puede tener acceso a una instancia de la clase a través de la propiedad `App.TodoManager`. La clase `TodoItemManager` proporciona los siguientes métodos para invocar las operaciones del servicio Web:

- **GetTasksAsync** : este método se usa para rellenar el control `ListView` en el `TodoListPage` con las instancias de `TodoItem` recuperadas del servicio Web.
- **SaveTaskAsync** : este método se usa para crear o actualizar una instancia de `TodoItem` en el servicio Web.
- **DeleteTaskAsync** : este método se usa para eliminar una instancia de `TodoItem` en el servicio Web.

Además, algunas aplicaciones de ejemplo contienen métodos adicionales en la clase `TodoItemManager`, que se usan para administrar el proceso de autenticación del usuario.

En lugar de invocar directamente las operaciones de servicio Web, los métodos `TodoItemManager` invocan métodos en una clase dependiente que se inserta en el constructor `TodoItemManager`. Por ejemplo, una aplicación de ejemplo inserta la clase `RestService` en el constructor `TodoItemManager` para proporcionar la implementación que usa las API de REST para tener acceso a los datos.

## <a name="related-links"></a>Vínculos relacionados

- [ASMX (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [WCF (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)
- [REST (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)
