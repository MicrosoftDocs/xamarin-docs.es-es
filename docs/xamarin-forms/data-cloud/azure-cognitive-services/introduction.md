---
title: Xamarin.Formsy Azure Cognitive Services introducción
description: En este artículo se proporciona una introducción a una aplicación de ejemplo que muestra cómo invocar algunas de las API de los servicios cognitivos de Microsoft.
ms.prod: xamarin
ms.assetid: 74121ADB-1322-4C1E-A103-F37257BC7CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7c2a63c40d1006c7d83c9dde871e17d4b194bdca
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84129719"
---
# <a name="xamarinforms-and-azure-cognitive-services-introduction"></a>Xamarin.Formsy Azure Cognitive Services introducción

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Microsoft Cognitive Services son un conjunto de API, SDK y servicios disponibles para que los desarrolladores puedan hacer que sus aplicaciones sean más inteligentes mediante la adición de características como el reconocimiento facial, el reconocimiento de voz y la comprensión de lenguajes. En este artículo se proporciona una introducción a la aplicación de ejemplo que muestra cómo invocar algunas de las API de los servicios cognitivos de Microsoft._

## <a name="overview"></a>Información general

El ejemplo adjunto es una aplicación de lista de tareas que proporciona funcionalidad para:

- Ver una lista de tareas.
- Agregue y edite tareas a través del teclado en pantalla o mediante el reconocimiento de voz con Microsoft Speech API.
- Revisar la ortografía de las tareas mediante el Bing Spell Check API. Para obtener más información, vea revisión [ortográfica mediante el Bing spell check API](spell-check.md).
- Traduzca tareas de inglés a alemán mediante la API de traductor. Para obtener más información, consulte [traducción de texto mediante la API de traductor](text-translation.md).
- Eliminar tareas.
- Establezca el estado de una tarea en "listo".
- Califique la aplicación con el reconocimiento de emociones mediante el Face API. Para obtener más información, consulte [el reconocimiento de emociones mediante el Face API](emotion-recognition.md).

> [!WARNING]
> El Bing Speech API ha dejado de usarse en favor del servicio de voz de Azure. Para obtener un ejemplo dedicado a Azure Speech Service, consulte [reconocimiento de voz con la API del servicio de voz](~/xamarin-forms/data-cloud/azure-cognitive-services/speech-recognition.md).

Las tareas se almacenan en una base de datos SQLite local. Para obtener más información sobre cómo usar una base de datos SQLite local, consulte [trabajar con una base de datos local](~/xamarin-forms/data-cloud/data/databases.md).

`TodoListPage`Se muestra cuando se inicia la aplicación. En esta página se muestra una lista de las tareas almacenadas en la base de datos local y se permite al usuario crear una nueva tarea o evaluar la aplicación:

![](introduction-images/sample-application-1.png "TodoListPage")

Se pueden crear nuevos elementos haciendo clic en el *+* botón, que navega a `TodoItemPage` . También se puede navegar por esta página seleccionando una tarea:

![](introduction-images/sample-application-2.png "TodoItemPage")

`TodoItemPage`Permite que las tareas se creen, se editen, se comprueben ortográficamente, se traduzcan, se guarden y se eliminen. El reconocimiento de voz se puede usar para crear o editar una tarea. Para ello, presione el botón micrófono para iniciar la grabación y presione el mismo botón una segunda vez para detener la grabación, que envía la grabación a la API de reconocimiento de Bing Speech.

Al hacer clic en el botón de emoticonos de `TodoListPage` , navega hasta el `RateAppPage` , que se usa para realizar el reconocimiento de emociones en una imagen de una expresión facial:

![](introduction-images/sample-application-3.png "RateAppPage")

`RateAppPage`Permite al usuario tomar una fotografía de su superficie, que se envía al Face API con la emoción devuelta que se muestra.

## <a name="understand-the-application-anatomy"></a>Descripción de la anatomía de la aplicación

El proyecto de código compartido para la aplicación de ejemplo consta de cinco carpetas principales:

|Carpeta|Propósito|
|--- |--- |
|Modelos|Contiene las clases de modelo de datos para la aplicación. Esto incluye la `TodoItem` clase, que modela un único elemento de datos utilizado por la aplicación. La carpeta también incluye las clases que se usan para modelar las respuestas JSON devueltas de diferentes API del servicio cognitivo de Microsoft.|
|Repositorios|Contiene la `ITodoItemRepository` interfaz y la `TodoItemRepository` clase que se utilizan para realizar operaciones de base de datos.|
|Servicios|Contiene las interfaces y clases que se usan para tener acceso a diferentes API del servicio cognitivo de Microsoft, junto con las interfaces que utiliza la `DependencyService` clase para buscar las clases que implementan las interfaces en los proyectos de la plataforma.|
|Utils|Contiene la `Timer` clase, que usa la `AuthenticationService` clase para renovar un token de acceso de JWT cada 9 minutos.|
|Vistas|Contiene las páginas de la aplicación.|

El proyecto de código compartido también contiene algunos archivos importantes:

|Archivo|Propósito|
|--- |--- |
|Constants.cs|La `Constants` clase, que especifica las claves de API y los puntos de conexión de las API de servicio cognitiva de Microsoft que se invocan. Las constantes de clave de API requieren actualización para tener acceso a las diferentes API de servicio cognitiva.|
|App.xaml.cs|La `App` clase es responsable de crear instancias de la primera página que mostrará la aplicación en cada plataforma y de la `TodoManager` clase que se utiliza para invocar las operaciones de base de datos.|

### <a name="nuget-packages"></a>Paquetes NuGet

La aplicación de ejemplo usa los siguientes paquetes NuGet:

- `Newtonsoft.Json`: proporciona un marco JSON para .NET.
- `PCLStorage`: proporciona un conjunto de API de e/s de archivos locales entre plataformas.
- `sqlite-net-pcl`: proporciona almacenamiento de base de datos de SQLite.
- `Xam.Plugin.Media`: proporciona las API de selección y toma de fotografías multiplataforma.

Además, estos paquetes NuGet también instalan sus propias dependencias.

### <a name="model-the-data"></a>Modelado de los datos

La aplicación de ejemplo utiliza la `TodoItem` clase para modelar los datos que se muestran y se almacenan en la base de datos SQLite local. En el ejemplo de código siguiente se muestra la clase `TodoItem`:

```csharp
public class TodoItem
{
  [PrimaryKey, AutoIncrement]
  public int ID { get; set; }
  public string Name { get; set; }
  public bool Done { get; set; }
}
```

La `ID` propiedad se usa para identificar de forma única cada `TodoItem` instancia y está decorada con atributos de SQLite que hacen que la propiedad sea una clave principal de incremento automático en la base de datos.

### <a name="invoke-database-operations"></a>Invocar operaciones de base de datos

La `TodoItemRepository` clase implementa las operaciones de base de datos y se puede tener acceso a una instancia de la clase a través de la `App.TodoManager` propiedad. La `TodoItemRepository` clase proporciona los siguientes métodos para invocar operaciones de base de datos:

- **GetAllItemsAsync** : recupera todos los elementos de la base de datos SQLite local.
- **GetItemAsync** : recupera un elemento especificado de la base de datos SQLite local.
- **SaveItemAsync** : crea o actualiza un elemento en la base de datos SQLite local.
- **DeleteItemAsync** : elimina el elemento especificado de la base de datos SQLite local.

### <a name="platform-project-implementations"></a>Implementaciones del proyecto de plataforma

La `Services` carpeta del proyecto de código compartido contiene las `IFileHelper` `IAudioRecorderService` interfaces y que usa la `DependencyService` clase para buscar las clases que implementan las interfaces en los proyectos de la plataforma.

La `IFileHelper` clase implementa la interfaz `FileHelper` en cada proyecto de plataforma. Esta clase consta de un único método, `GetLocalFilePath` , que devuelve una ruta de acceso de archivo local para almacenar la base de datos de SQLite.

La `IAudioRecorderService` clase implementa la interfaz `AudioRecorderService` en cada proyecto de plataforma. Esta clase consta de `StartRecording` `StopRecording` los métodos de compatibilidad, y, que usan las API de la plataforma para grabar audio desde el micrófono del dispositivo y almacenarlo como un archivo WAV. En iOS, `AudioRecorderService` usa la `AVFoundation` API para grabar audio. En Android, `AudioRecordService` usa la `AudioRecord` API para grabar audio. En el Plataforma universal de Windows (UWP), `AudioRecorderService` usa la `AudioGraph` API para grabar audio.

### <a name="invoke-cognitive-services"></a>Invocar servicios cognitivos

La aplicación de ejemplo invoca el siguiente Microsoft Cognitive Services:

- Microsoft Speech API. Para obtener más información, consulte [reconocimiento de voz mediante Microsoft Speech API](speech-recognition.md).
- Bing Spell Check API. Para obtener más información, vea revisión [ortográfica mediante el Bing spell check API](spell-check.md).
- Traduzca la API. Para obtener más información, consulte [traducción de texto mediante la API de traductor](text-translation.md).
- Face API. Para obtener más información, consulte [el reconocimiento de emociones mediante el Face API](emotion-recognition.md).

## <a name="related-links"></a>Vínculos relacionados

- [Reconocimiento de voz con la API del servicio Speech](~/xamarin-forms/data-cloud/azure-cognitive-services/speech-recognition.md)
- [Microsoft Cognitive Services documentación](https://www.microsoft.com/cognitive-services/documentation)
- [Cognitive Services todo (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
