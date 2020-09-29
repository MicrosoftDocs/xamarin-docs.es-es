---
title: 'Caso práctico de aplicación multiplataforma: tarea'
description: En este documento se describe cómo se ha diseñado la aplicación de ejemplo portable de tareas y se ha creado como una aplicación móvil multiplataforma. Se describen los requisitos de la aplicación, la interfaz, el modelo de datos, la funcionalidad básica, la implementación, etc.
ms.prod: xamarin
ms.assetid: B581B2D0-9890-C383-C654-0B0E12DAD5A6
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 803b42cfcc27dc86b0d4bc78fc4745af5565e8cb
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457554"
---
# <a name="cross-platform-app-case-study-tasky"></a>Caso práctico de aplicación multiplataforma: tarea

*Tarea* *portátil* es una sencilla aplicación de lista de tareas pendientes. En este documento se explica cómo se diseñó y compiló, siguiendo las instrucciones del documento [Building multiplatform Applications (creación de aplicaciones multiplataforma](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) ). La discusión abarca las siguientes áreas:

<a name="Design_Process"></a>

## <a name="design-process"></a>Proceso de diseño

Es aconsejable crear una de mapa de carreteras para lo que desea lograr antes de comenzar a codificar. Esto es especialmente cierto para el desarrollo multiplataforma, donde se crea una funcionalidad que se expondrá de varias maneras. A partir de una idea clara de lo que está compilando, se ahorra tiempo y esfuerzo más adelante en el ciclo de desarrollo.

 <a name="Requirements"></a>

### <a name="requirements"></a>Requisitos

El primer paso para diseñar una aplicación es identificar las características deseadas. Estos pueden ser objetivos de alto nivel o casos de uso detallados. Tasky tiene requisitos funcionales sencillos:

- Ver una lista de tareas
- Agregar, editar y eliminar tareas
- Establecer el estado de una tarea en ' listo '

Debe considerar el uso de características específicas de la plataforma.  ¿Puede aprovechar las ventajas de las geovallas de iOS o Windows Phone los iconos dinámicos? Incluso si no usa características específicas de la plataforma en la primera versión, debe planear con anterioridad para asegurarse de que su empresa & las capas de datos pueden acomodarlas.

 <a name="User_Interface_Design"></a>

### <a name="user-interface-design"></a>Diseño de la interfaz de usuario

Comience con un diseño de alto nivel que se puede implementar en las plataformas de destino. Tenga cuidado de tener en cuenta las restricciones de interfaz de usuario de Platform-identificación específica. Por ejemplo, `TabBarController` en iOS puede mostrar más de cinco botones, mientras que el Windows Phone equivalente puede mostrar hasta cuatro.
Dibuje el flujo de pantalla con la herramienta de su elección (papel trabajo).

 [![Dibuje el flujo de pantalla con la herramienta de su papel de elección](case-study-tasky-images/taskydesign.png)](case-study-tasky-images/taskydesign.png#lightbox)

 <a name="Data_Model"></a>

### <a name="data-model"></a>Modelo de datos

Saber qué datos deben almacenarse le ayudará a determinar qué mecanismo de persistencia debe usar. Consulte [acceso a datos multiplataforma](~/cross-platform/app-fundamentals/index.md) para obtener información sobre los mecanismos de almacenamiento disponibles y ayudar a decidir entre ellos. Para este proyecto, usaremos SQLite.NET.

Tasky necesita almacenar tres propiedades para cada ' TaskItem ':

- **Name**: cadena
- **Notas** : cadena
- **Done** : booleano

 <a name="Core_Functionality"></a>

### <a name="core-functionality"></a>Funcionalidad principal

Tenga en cuenta la API que la interfaz de usuario tendrá que usar para cumplir los requisitos. Una lista de tareas pendientes requiere las siguientes funciones:

- **Enumerar todas las tareas** : para mostrar la lista de la pantalla principal de todas las tareas disponibles
- **Obtener una tarea** : cuando se toca una fila de tarea
- **Guardar una tarea** : cuando se edita una tarea
- **Eliminar una tarea** : cuando se elimina una tarea
- **Crear tarea vacía** : cuando se crea una nueva tarea

Para lograr la reutilización de código, esta API debe implementarse una vez en la *biblioteca de clases portable*.

 <a name="Implementation"></a>

### <a name="implementation"></a>Implementación

Una vez que se haya acordado el diseño de la aplicación, tenga en cuenta cómo podría implementarse como una aplicación multiplataforma. Esto se convertirá en la arquitectura de la aplicación. Siguiendo las instrucciones que se indican en el documento sobre la [creación de aplicaciones multiplataforma](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) , el código de aplicación debe dividirse en las siguientes partes:

- **Código común** : un proyecto común que contiene código reutilizable para almacenar los datos de la tarea. exponga una clase de modelo y una API para administrar el guardado y la carga de datos.
- **Código específico** de la plataforma: proyectos específicos de la plataforma que implementan una interfaz de usuario nativa para cada sistema operativo, mediante el uso del código común como "back-end".

[![Los proyectos específicos de la plataforma implementan una interfaz de usuario nativa para cada sistema operativo, utilizando el código común como el back-end.](case-study-tasky-images/taskypro-architecture.png)](case-study-tasky-images/taskypro-architecture.png#lightbox)

Estas dos partes se describen en las secciones siguientes.

 <a name="Common_(PCL)_Code"></a>

## <a name="common-pcl-code"></a>Código común (PCL)

Tasky portable usa la estrategia de biblioteca de clases portable para compartir código común. Vea el documento [Opciones de código para compartir](~/cross-platform/app-fundamentals/code-sharing.md) para obtener una descripción de las opciones de uso compartido de código.

Todo el código común, incluida la capa de acceso a datos, el código de base de datos y los contratos, se coloca en el proyecto de biblioteca.

El proyecto PCL completo se ilustra a continuación. Todo el código de la biblioteca portable es compatible con cada plataforma de destino. Cuando se implementa, cada aplicación nativa hará referencia a esa biblioteca.

![Cuando se implementa, cada aplicación nativa hará referencia a esa biblioteca.](case-study-tasky-images/portable-project.png)

En el diagrama de clases siguiente se muestran las clases agrupadas por capa. La `SQLiteConnection` clase es código reutilizable del paquete SQLite-net. El resto de las clases son código personalizado para tareas. Las `TaskItemManager` `TaskItem` clases y representan la API que se expone a las aplicaciones específicas de la plataforma.

 [![Las clases TaskItemManager y TaskItem representan la API que se expone a las aplicaciones específicas de la plataforma.](case-study-tasky-images/classdiagram-core.png)](case-study-tasky-images/classdiagram-core.png#lightbox)

El uso de espacios de nombres para separar las capas ayuda a administrar las referencias entre cada capa. Los proyectos específicos de la plataforma solo deben incluir una `using` instrucción para el nivel de negocio. La capa de acceso a datos y el nivel de datos se deben encapsular mediante la API expuesta por `TaskItemManager` en el nivel de negocio.

 <a name="References"></a>

### <a name="references"></a>Referencias

Las bibliotecas de clases portables deben ser utilizables en varias plataformas, cada una de ellas con distintos niveles de compatibilidad para las características de plataforma y de marco. Debido a ello, existen limitaciones en cuanto a los paquetes y las bibliotecas de marcos que se pueden usar. Por ejemplo, Xamarin. iOS no admite la `dynamic` palabra clave de c#, por lo que una biblioteca de clases portable no puede usar ningún paquete que dependa de código dinámico, aunque ese código funcione en Android. Visual Studio para Mac impedirá agregar paquetes y referencias incompatibles, pero querrá tener en cuenta las limitaciones para evitar sorpresas más adelante.

Nota: verá que los proyectos hacen referencia a las bibliotecas de Framework que no ha usado. Estas referencias se incluyen como parte de las plantillas de proyecto de Xamarin. Cuando se compilan aplicaciones, el proceso de vinculación quitará el código sin referencia, por lo que, aunque se `System.Xml` haya hecho referencia a él, no se incluirá en la aplicación final porque no se está usando ninguna función XML.

 <a name="Data_Layer_(DL)"></a>

### <a name="data-layer-dl"></a>Capa de datos (DL)

La capa de datos contiene el código que realiza el almacenamiento físico de los datos, ya sea en una base de datos, en archivos planos u otros mecanismos. La capa de datos de tareas está compuesta de dos partes: la biblioteca SQLite-NET y el código personalizado agregado para conectarla.

Tasky se basa en el paquete de NuGet SQLite-net (publicado por Frank Krueger) para insertar el código SQLite-NET que proporciona una interfaz de base de datos de asignación relacional de objetos (ORM). La `TaskItemDatabase` clase hereda de `SQLiteConnection` y agrega los métodos de creación, lectura, actualización y eliminación (CRUD) necesarios para leer y escribir datos en SQLite. Es una implementación simple reutilizable de métodos CRUD genéricos que se pueden volver a usar en otros proyectos.

`TaskItemDatabase`Es un singleton, lo que garantiza que todo el acceso se produce en la misma instancia. Se usa un bloqueo para evitar el acceso simultáneo de varios subprocesos.

 <a name="SQLite_on_WIndows_Phone"></a>

#### <a name="sqlite-on-windows-phone"></a>SQLite en Windows Phone

Aunque iOS y Android se distribuyen con SQLite como parte del sistema operativo, Windows Phone no incluye un motor de base de datos compatible. Para compartir código en las tres plataformas, se requiere una versión nativa de Windows Phone de SQLite. Vea [trabajar con una base de datos local](~/xamarin-forms/data-cloud/data/databases.md) para obtener más información sobre cómo configurar el proyecto de Windows Phone para SQLite.

 <a name="Using_an_Interface_to_Generalize_Data_Access"></a>

#### <a name="using-an-interface-to-generalize-data-access"></a>Usar una interfaz para generalizar el acceso a datos

La capa de datos toma una dependencia de `BL.Contracts.IBusinessIdentity` para que pueda implementar métodos de acceso a datos abstractos que requieran una clave principal. Cualquier clase de nivel de negocio que implementa la interfaz se puede conservar en la capa de datos.

La interfaz simplemente especifica una propiedad de entero que actúa como clave principal:

```csharp
public interface IBusinessEntity {
    int ID { get; set; }
}
```

La clase base implementa la interfaz y agrega los atributos SQLite-NET para marcarlos como una clave principal de incremento automático. Cualquier clase del nivel de negocio que implementa esta clase base se puede conservar en el nivel de datos:

```csharp
public abstract class BusinessEntityBase : IBusinessEntity {
    public BusinessEntityBase () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
}
```

Un ejemplo de los métodos genéricos en el nivel de datos que usan la interfaz es este `GetItem<T>` método:

```csharp
public T GetItem<T> (int id) where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return Table<T>().FirstOrDefault(x => x.ID == id);
    }
}
```

 <a name="Locking_to_prevent_Concurrent_Access"></a>

#### <a name="locking-to-prevent-concurrent-access"></a>Bloqueo para evitar el acceso simultáneo

Se implementa un [bloqueo](/previous-versions/visualstudio/visual-studio-2010/c5kehkcz(v=vs.100)) dentro de la `TaskItemDatabase` clase para impedir el acceso simultáneo a la base de datos. Esto se hace para asegurarse de que se serializa el acceso simultáneo de distintos subprocesos (de lo contrario, un componente de la interfaz de usuario podría intentar leer la base de datos al mismo tiempo que un subproceso en segundo plano lo está actualizando). A continuación se muestra un ejemplo de cómo se implementa el bloqueo:

```csharp
static object locker = new object ();
public IEnumerable<T> GetItems<T> () where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return (from i in Table<T> () select i).ToList ();
    }
}
public T GetItem<T> (int id) where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return Table<T>().FirstOrDefault(x => x.ID == id);
    }
}
```

La mayor parte del código de nivel de datos se puede volver a usar en otros proyectos. El único código específico de la aplicación en la capa es la `CreateTable<TaskItem>` llamada en el `TaskItemDatabase` constructor.

 <a name="Data_Access_Layer_(DAL)"></a>

### <a name="data-access-layer-dal"></a>Capa de acceso a datos (DAL)

La `TaskItemRepository` clase encapsula el mecanismo de almacenamiento de datos con una API fuertemente tipada que permite `TaskItem` crear, eliminar, recuperar y actualizar objetos.

 <a name="Using_Conditional_Compilation"></a>

#### <a name="using-conditional-compilation"></a>Usar la compilación condicional

La clase utiliza la compilación condicional para establecer la ubicación del archivo. este es un ejemplo de implementación de la divergencia de la plataforma. La propiedad que devuelve la ruta de acceso se compila en un código diferente en cada plataforma. Aquí se muestran las directivas de compilador específicas de código y plataforma:

```csharp
public static string DatabaseFilePath {
    get {
        var sqliteFilename = "TaskDB.db3";
#if SILVERLIGHT
        // Windows Phone expects a local path, not absolute
        var path = sqliteFilename;
#else
#if __ANDROID__
        // Just use whatever directory SpecialFolder.Personal returns
        string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
        // we need to put in /Library/ on iOS5.1+ to meet Apple's iCloud terms
        // (they don't want non-user-generated data in Documents)
        string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
        string libraryPath = Path.Combine (documentsPath, "..", "Library"); // Library folder
#endif
        var path = Path.Combine (libraryPath, sqliteFilename);
                #endif
                return path;
    }
}
```

Dependiendo de la plataforma, el resultado será " <app
path> /Library/TaskDB.db3" para iOS, " <app
path> /Documents/TaskDB.db3" para Android o simplemente "TaskDB. db3" para Windows Phone.

### <a name="business-layer-bl"></a>Capa de negocio (BL)

El nivel de negocio implementa las clases de modelo y una fachada para administrarlas.
En tareas, el modelo es la `TaskItem` clase e `TaskItemManager` implementa el patrón de fachada para proporcionar una API para la administración `TaskItems` .

 <a name="Façade"></a>

#### <a name="faade"></a>Frontal

 `TaskItemManager` incluye `DAL.TaskItemRepository` para proporcionar los métodos GET, Save y Delete a los que se hará referencia en las capas de la aplicación y la interfaz de usuario.

Las reglas de negocios y la lógica se colocarían aquí si es necesario; por ejemplo, las reglas de validación que se deben cumplir antes de que se guarde un objeto.

 <a name="API_for_Platform-Specific_Code"></a>

### <a name="api-for-platform-specific-code"></a>API para código específico de la plataforma

Una vez que se ha escrito el código común, se debe crear la interfaz de usuario para recopilar y mostrar los datos que expone. La `TaskItemManager` clase implementa el patrón de fachada para proporcionar una API simple para que el código de la aplicación tenga acceso a.

Normalmente, el código escrito en cada proyecto específico de la plataforma se acoplará estrechamente al SDK nativo de ese dispositivo y solo tendrá acceso al código común mediante la API definida por `TaskItemManager` . Esto incluye los métodos y las clases empresariales que expone, como `TaskItem` .

Las imágenes no se comparten entre plataformas, pero se agregan de forma independiente a cada proyecto. Esto es importante porque cada plataforma controla las imágenes de forma diferente, con diferentes nombres de archivo, directorios y resoluciones.

En las secciones restantes se describen los detalles de implementación específicos de la plataforma de la interfaz de usuario de tarea.

 <a name="iOS_App"></a>

## <a name="ios-app"></a>Aplicación iOS

Solo hay unas cuantas clases necesarias para implementar la aplicación de tareas de iOS mediante el proyecto de PCL común para almacenar y recuperar datos. A continuación se muestra el proyecto de Xamarin. iOS completo de iOS:

 ![el proyecto de iOS se muestra aquí](case-study-tasky-images/taskyios-solution.png)

Las clases se muestran en este diagrama, agrupadas en capas.

 [![Las clases se muestran en este diagrama, agrupadas en capas.](case-study-tasky-images/classdiagram-android.png)](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References"></a>

### <a name="references"></a>Referencias

La aplicación de iOS hace referencia a las bibliotecas de SDK específicas de la plataforma, por ejemplo, Xamarin. iOS y MonoTouch. Dialog-1.

También debe hacer referencia al `TaskyPortableLibrary` proyecto PCL.
Aquí se muestra la lista de referencias:

 ![Aquí se muestra la lista de referencias.](case-study-tasky-images/taskyios-references.png)

El nivel de aplicación y la capa de la interfaz de usuario se implementan en este proyecto mediante estas referencias.

 <a name="Application_Layer_(AL)"></a>

### <a name="application-layer-al"></a>Nivel de aplicación (AL)

El nivel de aplicación contiene clases específicas de la plataforma necesarias para "enlazar" los objetos expuestos por la PCL a la interfaz de usuario. La aplicación específica de iOS tiene dos clases para ayudar a mostrar las tareas:

- **EditingSource** : esta clase se usa para enlazar listas de tareas a la interfaz de usuario. Dado `MonoTouch.Dialog` que se usó para la lista de tareas, es necesario implementar esta aplicación auxiliar para habilitar la funcionalidad de deslizar a eliminar en  `UITableView` . La deslizamiento a la eliminación es habitual en iOS, pero no en Android o en Windows Phone, por lo que el proyecto específico de iOS es el único que lo implementa.
- **TaskDialog** : esta clase se usa para enlazar una sola tarea a la interfaz de usuario. Usa la `MonoTouch.Dialog` API de reflexión para "Encapsular" el `TaskItem` objeto con una clase que contiene los atributos correctos para permitir que la pantalla de entrada tenga el formato correcto.

La `TaskDialog` clase utiliza `MonoTouch.Dialog` atributos para crear una pantalla basada en las propiedades de una clase. La clase tiene el siguiente aspecto:

```csharp
public class TaskDialog {
    public TaskDialog (TaskItem task)
    {
        Name = task.Name;
        Notes = task.Notes;
        Done = task.Done;
    }
    [Entry("task name")]
    public string Name { get; set; }
    [Entry("other task info")]
    public string Notes { get; set; }
    [Entry("Done")]
    public bool Done { get; set; }
    [Section ("")]
    [OnTap ("SaveTask")]    // method in HomeScreen
    [Alignment (UITextAlignment.Center)]
    public string Save;
    [Section ("")]
    [OnTap ("DeleteTask")]  // method in HomeScreen
    [Alignment (UITextAlignment.Center)]
    public string Delete;
}
```

Observe que los `OnTap` atributos requieren un nombre de método: estos métodos deben existir en la clase donde `MonoTouch.Dialog.BindingContext` se crea (en este caso, la clase que se `HomeScreen` describe en la sección siguiente).

 <a name="User_Interface_Layer_(UI)"></a>

### <a name="user-interface-layer-ui"></a>Nivel de interfaz de usuario (UI)

La capa de interfaz de usuario consta de las siguientes clases:

1. **AppDelegate** : contiene llamadas a la API de apariencia para aplicar estilo a las fuentes y los colores usados en la aplicación. Tasky es una aplicación simple, por lo que no hay otras tareas de inicialización que se ejecuten en  `FinishedLaunching` .
2. **Pantallas** : subclases de `UIViewController` que definen cada pantalla y su comportamiento. Las pantallas unen la interfaz de usuario con clases de nivel de aplicación y la API común ( `TaskItemManager` ). En este ejemplo, las pantallas se crean en el código, pero podrían haberse diseñado con la Interface Builder de Xcode o el diseñador de guiones gráficos.
3. **Imágenes** : los elementos visuales son una parte importante de cada aplicación. Tasky tiene imágenes de pantalla de presentación e icono, que para iOS se debe proporcionar en la resolución normal y retina.

 <a name="Home_Screen"></a>

#### <a name="home-screen"></a>Pantalla Inicio

La pantalla principal es una `MonoTouch.Dialog` pantalla que muestra una lista de tareas de la base de datos SQLite. Hereda de `DialogViewController` e implementa código para establecer `Root` que contenga una colección de `TaskItem` objetos para su presentación.

 [![Hereda de DialogViewController e implementa código para establecer la raíz para que contenga una colección de objetos TaskItem para su presentación](case-study-tasky-images/ios-taskylist.png)](case-study-tasky-images/ios-taskylist.png#lightbox)

Los dos métodos principales relacionados con la visualización e interacción con la lista de tareas son:

1. **PopulateTable** : usa el método del nivel de negocio  `TaskManager.GetTasks` para recuperar una colección de  `TaskItem` objetos que se van a mostrar.
2. **Seleccionada** : cuando se toca una fila, muestra la tarea en una nueva pantalla.

 <a name="Task_Details_Screen"></a>

#### <a name="task-details-screen"></a>Pantalla de detalles de tarea

Detalles de tarea es una pantalla de entrada que permite editar o eliminar tareas.

Taskly usa `MonoTouch.Dialog` la API de reflexión para mostrar la pantalla, por lo que no hay ninguna `UIViewController` implementación. En su lugar, la `HomeScreen` clase crea una instancia de y muestra `DialogViewController` mediante la `TaskDialog` clase de la capa de aplicación.

En esta captura de pantalla se muestra una pantalla vacía que muestra el `Entry` atributo que establece el texto de marca de agua en los campos **nombre** y **notas** :

 [![En esta captura de pantalla se muestra una pantalla vacía que muestra el atributo de entrada que establece el texto de marca de agua en los campos nombre y notas.](case-study-tasky-images/ios-taskydetail.png)](case-study-tasky-images/ios-taskydetail.png#lightbox)

La funcionalidad de la pantalla de detalles de la **tarea** (como guardar o eliminar una tarea) debe implementarse en la `HomeScreen` clase, porque es donde se `MonoTouch.Dialog.BindingContext` crea. Los `HomeScreen` métodos siguientes admiten la pantalla de detalles de la tarea:

1. **ShowTaskDetails** : crea un  `MonoTouch.Dialog.BindingContext` para representar una pantalla. Crea la pantalla de entrada mediante la reflexión para recuperar los tipos y nombres de propiedad de la  `TaskDialog` clase. La información adicional, como el texto de marca de agua para los cuadros de entrada, se implementa con atributos en las propiedades.
2. **SaveTask** : se hace referencia a este método en la  `TaskDialog` clase a través de un  `OnTap` atributo. Se llama cuando se presiona  **Guardar** y utiliza  `MonoTouch.Dialog.BindingContext` para recuperar los datos especificados por el usuario antes de guardar los cambios mediante  `TaskItemManager` .
3. **DeleteTask** : se hace referencia a este método en la  `TaskDialog` clase a través de un  `OnTap` atributo. Utiliza  `TaskItemManager` para eliminar los datos mediante la clave principal (propiedad ID).

 <a name="Android_App"></a>

## <a name="android-app"></a>Aplicación Android

El proyecto de Xamarin. Android completo se muestra a continuación:

 ![El proyecto de Android se muestra aquí](case-study-tasky-images/taskyandroid-solution.png)

El diagrama de clases, con las clases agrupadas por capa:

 [![El diagrama de clases, con clases agrupadas por nivel](case-study-tasky-images/classdiagram-android.png)](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References"></a>

### <a name="references"></a>Referencias

El proyecto de aplicación de Android debe hacer referencia al ensamblado de Xamarin. Android específico de la plataforma para tener acceso a las clases desde el Android SDK.

También debe hacer referencia al proyecto PCL (por ejemplo, TaskyPortableLibrary) para tener acceso a los datos comunes y al código de nivel de negocio.

 ![TaskyPortableLibrary para tener acceso a los datos comunes y al código de nivel de negocio](case-study-tasky-images/taskyandroid-references.png)

 <a name="Application_Layer_(AL)"></a>

### <a name="application-layer-al"></a>Nivel de aplicación (AL)

De forma similar a la versión de iOS que vimos anteriormente, la capa de aplicación en la versión de Android contiene clases específicas de la plataforma necesarias para "enlazar" los objetos expuestos por el núcleo a la interfaz de usuario.

 **TaskListAdapter** : para mostrar una lista \<T> de objetos, es necesario implementar un adaptador para mostrar los objetos personalizados en un `ListView` . El adaptador controla qué diseño se usa para cada elemento de la lista; en este caso, el código usa un diseño integrado de Android `SimpleListItemChecked` .

 <a name="User_Interface_(UI)"></a>

### <a name="user-interface-ui"></a>Interfaz de usuario (UI)

La capa de interfaz de usuario de la aplicación Android es una combinación de código y marcado XML.

- **Recursos/diseño** : diseños de pantalla y el diseño de celda de fila implementados como archivos AXML. El AXML puede escribirse de forma manual o se puede usar visualmente con el diseñador de la interfaz de usuario de Xamarin para Android.
- **Recursos/Dibujables** : imágenes (iconos) y botón personalizado.
- **Pantallas** : subclases de actividad que definen cada pantalla y su comportamiento. Une la interfaz de usuario con clases de capa de aplicación y la API común ( `TaskItemManager` ).

 <a name="Home_Screen"></a>

#### <a name="home-screen"></a>Pantalla Inicio

La pantalla principal está formada por una subclase de actividad `HomeScreen` y el `HomeScreen.axml` archivo que define el diseño (posición del botón y de la lista de tareas). La pantalla tiene el siguiente aspecto:

 [![La pantalla tiene el siguiente aspecto](case-study-tasky-images/android-taskylist.png)](case-study-tasky-images/android-taskylist.png#lightbox)

El código de la pantalla principal define los controladores para hacer clic en el botón y hacer clic en los elementos de la lista, así como rellenar la lista en el `OnResume` método (de modo que refleje los cambios realizados en la pantalla de detalles de la tarea). Los datos se cargan con la capa de negocio `TaskItemManager` y `TaskListAdapter` desde el nivel de aplicación.

 <a name="Task_Details_Screen"></a>

#### <a name="task-details-screen"></a>Pantalla de detalles de tarea

La pantalla detalles de tarea también consta de una `Activity` subclase y un archivo de diseño AXML. El diseño determina la ubicación de los controles de entrada y la clase de C# define el comportamiento para cargar y guardar `TaskItem` objetos.

 [![La clase define el comportamiento para cargar y guardar objetos TaskItem](case-study-tasky-images/android-taskydetail.png)](case-study-tasky-images/android-taskydetail.png#lightbox)

Todas las referencias a la biblioteca PCL se encuentran a través de la `TaskItemManager` clase.

 <a name="Windows_Phone_App"></a>

## <a name="windows-phone-app"></a>Windows Phone aplicación
El proyecto Windows Phone completo:

 ![Windows Phone aplicación del proyecto de Windows Phone completo](case-study-tasky-images/taskywp7-solution.png)

En el diagrama siguiente se muestran las clases agrupadas en capas:

 [![En este diagrama se presentan las clases agrupadas en capas.](case-study-tasky-images/classdiagram-wp7.png)](case-study-tasky-images/classdiagram-wp7.png#lightbox)

 <a name="References"></a>

### <a name="references"></a>Referencias

El proyecto específico de la plataforma debe hacer referencia a las bibliotecas específicas de la plataforma necesarias (como `Microsoft.Phone` y `System.Windows` ) para crear una aplicación Windows Phone válida.

También debe hacer referencia al proyecto PCL (por ejemplo, `TaskyPortableLibrary`) para que use la `TaskItem` clase y la base de datos.

 ![TaskyPortableLibrary para el uso de la clase TaskItem y la base de datos](case-study-tasky-images/taskywp7-references.png)

 <a name="Application_Layer_(AL)"></a>

### <a name="application-layer-al"></a>Nivel de aplicación (AL)

De nuevo, al igual que con las versiones de iOS y Android, el nivel de aplicación se compone de los elementos no visuales que ayudan a enlazar datos a la interfaz de usuario.

 <a name="ViewModels"></a>

#### <a name="viewmodels"></a>ViewModels

ViewModels encapsula datos de la PCL ( `TaskItemManager` ) y los presenta de forma que el enlace de datos de Silverlight/XAML pueda consumirlos. Este es un ejemplo de un comportamiento específico de la plataforma (como se describe en el documento aplicaciones multiplataforma).

 <a name="User_Interface_(UI)"></a>

### <a name="user-interface-ui"></a>Interfaz de usuario (UI)

XAML tiene una capacidad de enlace de datos única que se puede declarar en el marcado y reducir la cantidad de código necesario para mostrar los objetos:

1. **Páginas** : los archivos XAML y su código subyacente definen la interfaz de usuario y hacen referencia a ViewModels y al proyecto PCL para mostrar y recopilar datos.
2. **Imágenes** : la pantalla de presentación, las imágenes de fondo y de icono son una parte fundamental de la interfaz de usuario.

 <a name="MainPage"></a>

#### <a name="mainpage"></a>MainPage

La clase MainPage utiliza `TaskListViewModel` para Mostrar datos mediante las características de enlace de datos de XAML. La de la página `DataContext` se establece en el modelo de vista, que se rellena de forma asincrónica. La `{Binding}` Sintaxis de XAML determina cómo se muestran los datos.

 <a name="TaskDetailsPage"></a>

#### <a name="taskdetailspage"></a>TaskDetailsPage

Cada tarea se muestra enlazando `TaskViewModel` al XAML definido en TaskDetailsPage. Xaml. Los datos de la tarea se recuperan a través `TaskItemManager` de en el nivel de negocio.

 <a name="Results"></a>

## <a name="results"></a>Results

Las aplicaciones resultantes tienen este aspecto en cada plataforma:

 <a name="iOS"></a>

### <a name="ios"></a>iOS

La aplicación usa el diseño de la interfaz de usuario estándar de iOS, como el botón ' Agregar ' que se coloca en la barra de navegación y el icono de **más (+)** integrado. También usa el comportamiento predeterminado del `UINavigationController` botón ' atrás ' y admite ' deslizar a eliminar ' en la tabla.

 [ ![ También usa el comportamiento predeterminado del botón atrás UINavigationController y admite la función de deslizar para eliminar en la tabla](case-study-tasky-images/ios-taskylist.png)](case-study-tasky-images/ios-taskylist.png#lightbox) [ ![ también usa el comportamiento del botón atrás predeterminado UINavigationController y admite la función de deslizar para eliminar en la tabla](case-study-tasky-images/ios-taskylist.png)](case-study-tasky-images/ios-taskylist.png#lightbox) .

 <a name="Android"></a>

### <a name="android"></a>Android

La aplicación Android usa controles integrados, incluido el diseño integrado para las filas que requieren un "tick" mostrado. El comportamiento de back-out del hardware o del sistema se admite además de un botón atrás en pantalla.

 [ ![ El comportamiento de back-out del hardware o del sistema se admite además de un botón atrás en pantalla](case-study-tasky-images/android-taskylist.png)](case-study-tasky-images/android-taskylist.png#lightbox). el comportamiento de back-out del[ ![ hardware y del sistema se admite además de un botón atrás en pantalla](case-study-tasky-images/android-taskylist.png)](case-study-tasky-images/android-taskylist.png#lightbox) .

 <a name="Windows_Phone"></a>

### <a name="windows-phone"></a>Windows Phone

La aplicación Windows Phone usa el diseño estándar, rellenando la barra de la aplicación en la parte inferior de la pantalla en lugar de una barra de navegación en la parte superior.

 [ ![ La aplicación Windows Phone usa el diseño estándar, rellenando la barra de la aplicación en la parte inferior de la pantalla, en lugar de una barra de navegación en la parte superior](case-study-tasky-images/wp-taskylist.png)](case-study-tasky-images/wp-taskylist.png#lightbox) [ ![ , la aplicación Windows Phone usa el diseño estándar, rellenando la barra de la aplicación en la parte inferior de la pantalla en lugar de una barra de navegación en la parte superior](case-study-tasky-images/wp-taskylist.png)](case-study-tasky-images/wp-taskylist.png#lightbox)

 <a name="Summary"></a>

## <a name="summary"></a>Resumen

En este documento se proporciona una explicación detallada de cómo se han aplicado los principios del diseño de aplicaciones superpuestas a una aplicación sencilla para facilitar la reutilización del código en tres plataformas móviles: iOS, Android y Windows Phone.

Se ha descrito el proceso que se usa para diseñar las capas de aplicación y se ha explicado la funcionalidad de código que se ha &amp; implementado en cada capa.

El código se puede descargar desde [GitHub](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable).

## <a name="related-links"></a>Vínculos relacionados

- [Compilar aplicaciones multiplataforma (documento principal)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Aplicación de ejemplo portable de tareas (GitHub)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)