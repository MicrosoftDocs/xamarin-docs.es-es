---
title: Introducción a Xamarin. Forms y Azure Cognitive Services
description: En este artículo se proporciona una introducción a una aplicación de ejemplo que muestra cómo invocar algunas de las API de Microsoft Cognitive Service.
ms.prod: xamarin
ms.assetid: 74121ADB-1322-4C1E-A103-F37257BC7CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 52774b387644b14e3d4612dffa6d3c3b28a37f25
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "68652314"
---
# <a name="xamarinforms-and-azure-cognitive-services-introduction"></a>Introducción a Xamarin. Forms y Azure Cognitive Services

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Microsoft Cognitive Services son un conjunto de API, SDK y servicios disponibles para los desarrolladores para que sus aplicaciones más inteligentes mediante la adición de características como reconocimiento facial, reconocimiento de voz y comprensión del lenguaje. En este artículo se proporciona una introducción a la aplicación de ejemplo que muestra cómo invocar algunas de las API de Microsoft Cognitive Service._

## <a name="overview"></a>Información general

El ejemplo adjunto es una aplicación de lista de tareas que proporciona funcionalidad para:

- Ver una lista de tareas.
- Agregar y editar tareas mediante el teclado en pantalla, o bien al realizar el reconocimiento de voz con la API de voz de Microsoft. Para obtener más información acerca de cómo realizar el reconocimiento de voz, consulte [reconocimiento de voz con la API de Microsoft Speech](speech-recognition.md).
- Revisión ortográfica con Bing Spell Check API de tareas de comprobación. Para obtener más información, consulte [ortográfica con Bing Spell Check API](spell-check.md).
- Traducir las tareas del inglés al alemán mediante la API del traductor. Para obtener más información, consulte [traducción de texto mediante la API del traductor](text-translation.md).
- Eliminar las tareas.
- Establece el estado de una tarea a 'listo'.
- Valore la aplicación con reconocimiento de emociones, mediante Face API. Para obtener más información, consulte [mediante Face API de reconocimiento de emociones](emotion-recognition.md).

Las tareas se almacenan en una base de datos SQLite local. Para obtener más información sobre el uso de una base de datos SQLite local, consulte [trabajar con una base de datos Local](~/xamarin-forms/data-cloud/data/databases.md).

El `TodoListPage` se muestra cuando se inicia la aplicación. Esta página muestra una lista de las tareas que se almacenan en la base de datos local y permite al usuario para crear una nueva tarea o para clasificar la solicitud:

![](introduction-images/sample-application-1.png "TodoListPage")

Se pueden crear nuevos elementos, haga clic en el *+* botón, que se desplaza a la `TodoItemPage`. Esta página también se puede navegar a seleccionando una tarea:

![](introduction-images/sample-application-2.png "TodoItemPage")

El `TodoItemPage` permite crear, editar, revisar, tareas traducido, se guardan y se eliminan. El reconocimiento de voz se puede usar para crear o editar una tarea. Esto se logra al presionar el botón de micrófono para iniciar la grabación y presionando el botón mismo una segunda vez para detener la grabación, que envía la grabación a la API de reconocimiento de voz de Bing.

Al hacer clic en el botón emoticones en el `TodoListPage` navega a la `RateAppPage`, que se usa para realizar el reconocimiento de emociones en una imagen de una expresión facial:

![](introduction-images/sample-application-3.png "RateAppPage")

El `RateAppPage` permite al usuario tomar una foto de su cara, que se envía a Face API con la emoción devuelta que se muestran.

## <a name="understand-the-application-anatomy"></a>Descripción de la anatomía de la aplicación

El proyecto de código compartido para la aplicación de ejemplo consta de cinco carpetas principales:

|Carpeta|Propósito|
|--- |--- |
|Modelos|Contiene las clases de modelo de datos para la aplicación. Esto incluye la `TodoItem` (clase), que modela un único elemento de datos usados por la aplicación. La carpeta también incluye las clases utilizadas para las respuestas JSON de modelo devueltas desde diferentes Microsoft Cognitive Service APIs.|
|Repositorios|Contiene el `ITodoItemRepository` interfaz y `TodoItemRepository` clase que se usan para realizar operaciones de base de datos.|
|Servicios|Contiene las interfaces y clases que se usan para tener acceso a diferentes Microsoft Cognitive Service APIs, junto con interfaces que se usan por el `DependencyService` clase para buscar las clases que implementan las interfaces en los proyectos de plataforma.|
|Utils|Contiene el `Timer` (clase), que es utilizado por el `AuthenticationService` clase renovar un token de acceso JWT cada 9 minutos.|
|Vistas|Contiene las páginas de la aplicación.|

El proyecto de código compartido también contiene algunos archivos importantes:

|Archivo|Propósito|
|--- |--- |
|Constants.cs|El `Constants` (clase), que especifica las claves de API y los puntos de conexión de Microsoft Cognitive Service APIs que se invocan. Las constantes de clave de API requieren actualización para tener acceso a las distintas API de Cognitive Service.|
|App.xaml.cs|El `App` clase es responsable de crear instancias tanto la primera página que se mostrará la aplicación en cada plataforma, y el `TodoManager` clase que se usa para invocar operaciones de base de datos.|

### <a name="nuget-packages"></a>Paquetes NuGet

La aplicación de ejemplo usa los siguientes paquetes NuGet:

- `Newtonsoft.Json` : proporciona un marco JSON para. NET.
- `PCLStorage` : proporciona un conjunto de archivos local las API de E/S de multiplataforma.
- `sqlite-net-pcl` : proporciona almacenamiento de base de datos de SQLite.
- `Xam.Plugin.Media` : proporciona las API de selección y toma de fotografía de multiplataforma.

Además, estos paquetes de NuGet también instalan sus propias dependencias.

### <a name="model-the-data"></a>Modelar los datos

La aplicación de ejemplo usa el `TodoItem` clase para modelar los datos que se muestra y se almacenan en la base de datos SQLite local. En el ejemplo de código siguiente se muestra la clase `TodoItem`:

```csharp
public class TodoItem
{
  [PrimaryKey, AutoIncrement]
  public int ID { get; set; }
  public string Name { get; set; }
  public bool Done { get; set; }
}
```

El `ID` propiedad se utiliza para identificar de forma única cada `TodoItem` de instancia y está decorado con atributos de SQLite que hacen que la propiedad de una clave principal de incremento automático de la base de datos.

### <a name="invoke-database-operations"></a>Invocar operaciones de base de datos

El `TodoItemRepository` clase implementa las operaciones de base de datos y una instancia de la clase se puede acceder mediante el `App.TodoManager` propiedad. La `TodoItemRepository` clase proporciona los métodos siguientes para invocar operaciones de base de datos:

- **GetAllItemsAsync** : recupera todos los elementos de la base de datos SQLite local.
- **GetItemAsync** : recupera un elemento especificado de la base de datos SQLite local.
- **SaveItemAsync** : crea o actualiza un elemento de la base de datos SQLite local.
- **DeleteItemAsync** : elimina el elemento especificado de la base de datos SQLite local.

### <a name="platform-project-implementations"></a>Implementaciones del proyecto de plataforma

La `Services` carpeta del proyecto de código compartido contiene las `IFileHelper` interfaces `IAudioRecorderService` y que usa la `DependencyService` clase para buscar las clases que implementan las interfaces en los proyectos de la plataforma.

El `IFileHelper` interfaz se implementa mediante el `FileHelper` clase en cada proyecto de la plataforma. Esta clase consta de un único método, `GetLocalFilePath`, que devuelve una ruta de acceso local para almacenar la base de datos de SQLite.

El `IAudioRecorderService` interfaz se implementa mediante el `AudioRecorderService` clase en cada proyecto de la plataforma. Esta clase consta de `StartRecording`, `StopRecording`y que admiten métodos, que use las API de plataforma para grabar audio desde el micrófono del dispositivo y almacenan como un archivo wav. En iOS, el `AudioRecorderService` usa el `AVFoundation` API para grabar audio. En Android, el `AudioRecordService` usa el `AudioRecord` API para grabar audio. En la plataforma Universal de Windows (UWP), el `AudioRecorderService` usa el `AudioGraph` API para grabar audio.

### <a name="invoke-cognitive-services"></a>Invocar servicios cognitivos

La aplicación de ejemplo invoca los servicios cognitivos de Microsoft siguiente:

- Microsoft Speech API. Para obtener más información, consulte [reconocimiento de voz con la API de Microsoft Speech](speech-recognition.md).
- Bing Spell Check API. Para obtener más información, consulte [ortográfica con Bing Spell Check API](spell-check.md).
- Traducir API. Para obtener más información, consulte [traducción de texto mediante la API del traductor](text-translation.md).
- Face API. Para obtener más información, consulte [mediante Face API de reconocimiento de emociones](emotion-recognition.md).

## <a name="related-links"></a>Vínculos relacionados

- [Documentación de Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services/documentation)
- [Todo Cognitive Services (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
