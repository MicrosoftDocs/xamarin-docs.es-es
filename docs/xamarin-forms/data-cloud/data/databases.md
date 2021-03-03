---
title: Xamarin.Forms Bases de datos locales
description: Xamarin.Forms admite aplicaciones controladas por la base de datos que usan el motor de base de datos SQLite, lo que permite cargar y guardar objetos en código compartido. En este artículo se describe cómo Xamarin.Forms las aplicaciones pueden leer y escribir datos en una base de datos SQLite local mediante SQLite.net.
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 03/01/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a7dd5ea8963fed079c82ac6944d571176002486e
ms.sourcegitcommit: 322e7bcf9fb8c1ad52ab8e929bea95d45e280834
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/03/2021
ms.locfileid: "101751449"
---
# <a name="xamarinforms-local-databases"></a>Xamarin.Forms Bases de datos locales

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/todo)

El motor de base de Xamarin.Forms datos SQLite permite a las aplicaciones cargar y guardar objetos de datos en código compartido. La aplicación de ejemplo usa una tabla de base de datos de SQLite para almacenar los elementos de la lista de tareas. En este artículo se describe cómo usar SQLite.Net en código compartido para almacenar y recuperar información en una base de datos local.

[![Capturas de pantallas de la aplicación ToDoList en iOS y Android](databases-images/todo-list-sml.png)](databases-images/todo-list.png#lightbox "Aplicación ToDoList en iOS y Android")

Integre SQLite.NET en Mobile Apps siguiendo estos pasos:

1. [Instale el paquete NuGet](#install-the-sqlite-nuget-package).
1. [Configure constantes](#configure-app-constants).
1. [Cree una clase de acceso a base de datos](#create-a-database-access-class).
1. [Acceder a los Xamarin.Forms datos en ](#access-data-in-xamarinforms).
1. [Configuración avanzada](#advanced-configuration).

## <a name="install-the-sqlite-nuget-package"></a>Instalar el paquete NuGet de SQLite

Use el administrador de paquetes NuGet para buscar **SQLite-net-PCL** y agregue la versión más reciente al proyecto de código compartido.

Hay varios paquetes NuGet con nombres similares. El paquete correcto tiene estos atributos:

- **Id.:** sqlite-net-pcl
- **Autores:** SQLite-net
- **Propietarios:** praeclarum
- **Vínculo de NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> Independientemente del nombre del paquete, utilice el paquete de NuGet **sqlite-net-pcl** incluso en los proyectos de .NET Standard.

## <a name="configure-app-constants"></a>Configurar constantes de aplicación

El proyecto de ejemplo incluye un archivo **constants.CS** que proporciona datos de configuración comunes:

```csharp
public static class Constants
{
    public const string DatabaseFilename = "TodoSQLite.db3";

    public const SQLite.SQLiteOpenFlags Flags =
        // open the database in read/write mode
        SQLite.SQLiteOpenFlags.ReadWrite |
        // create the database if it doesn't exist
        SQLite.SQLiteOpenFlags.Create |
        // enable multi-threaded database access
        SQLite.SQLiteOpenFlags.SharedCache;

    public static string DatabasePath
    {
        get
        {
            var basePath = Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData);
            return Path.Combine(basePath, DatabaseFilename);
        }
    }
}
```

El archivo de constantes especifica `SQLiteOpenFlag` los valores de enumeración predeterminados que se usan para inicializar la conexión de base de datos. La `SQLiteOpenFlag` enumeración admite estos valores:

- `Create`: La conexión creará automáticamente el archivo de base de datos si no existe.
- `FullMutex`: La conexión se abre en el modo de subproceso serializado.
- `NoMutex`: La conexión se abre en modo de subprocesamiento múltiple.
- `PrivateCache`: La conexión no participará en la memoria caché compartida, aunque esté habilitada.
- `ReadWrite`: La conexión puede leer y escribir datos.
- `SharedCache`: La conexión participará en la memoria caché compartida, si está habilitada.
- `ProtectionComplete`: El archivo está cifrado y no se puede obtener acceso a él mientras el dispositivo está bloqueado.
- `ProtectionCompleteUnlessOpen`: El archivo se cifra hasta que se abre, pero es accesible aunque el usuario bloquee el dispositivo.
- `ProtectionCompleteUntilFirstUserAuthentication`: El archivo se cifra hasta que el usuario haya arrancado y desbloqueado el dispositivo.
- `ProtectionNone`: El archivo de base de datos no está cifrado.

Es posible que tenga que especificar marcas diferentes en función de cómo se utilizará la base de datos. Para obtener más información acerca de `SQLiteOpenFlags` , consulte [abrir una nueva conexión de base de datos](https://www.sqlite.org/c3ref/open.html) en SQLite.org.

## <a name="create-a-database-access-class"></a>Crear una clase de acceso a base de datos

Una clase contenedora de base de datos abstrae la capa de acceso a datos desde el resto de la aplicación. Esta clase centraliza la lógica de consulta y simplifica la administración de la inicialización de la base de datos, lo que facilita refactorizar o expandir las operaciones de datos a medida que crece la aplicación. La aplicación todo define una `TodoItemDatabase` clase para este propósito.

### <a name="lazy-initialization"></a>Inicialización diferida

`TodoItemDatabase`Utiliza la inicialización diferida asincrónica, representada por la `AsyncLazy<T>` clase personalizada, para retrasar la inicialización de la base de datos hasta que se tiene acceso por primera vez:

```csharp
public class TodoItemDatabase
{
    static SQLiteAsyncConnection Database;

    public static readonly AsyncLazy<TodoItemDatabase> Instance = new AsyncLazy<TodoItemDatabase>(async () =>
    {
        var instance = new TodoItemDatabase();
        CreateTableResult result = await Database.CreateTableAsync<TodoItem>();
        return instance;
    });

    public TodoItemDatabase()
    {
        Database = new SQLiteAsyncConnection(Constants.DatabasePath, Constants.Flags);
    }

    //...
}
```

El `Instance` campo se usa para crear la tabla de base de datos para el `TodoItem` objeto, si aún no existe, y devuelve `TodoItemDatabase` como singleton. El `Instance` campo de tipo `AsyncLazy<TodoItemDatabase>` se construye la primera vez que se espera. Si varios subprocesos intentan obtener acceso al campo al mismo tiempo, todos usarán la construcción única. A continuación, cuando se completa la construcción, todas `await` las operaciones se completan. Además, las `await` operaciones posteriores a la construcción se completarán inmediatamente, ya que el valor está disponible.

> [!NOTE]
> La conexión de base de datos es un campo estático que garantiza que se utiliza una conexión de base de datos única para la vida de la aplicación. El uso de una conexión estática persistente ofrece un mejor rendimiento que abrir y cerrar conexiones varias veces durante una sesión de aplicación única.

### <a name="asynchronous-lazy-initialization"></a>Inicialización diferida asincrónica

Con el fin de iniciar la inicialización de la base de datos, evitar el bloqueo de la ejecución y tener la oportunidad de detectar excepciones, la aplicación de ejemplo utiliza la inicialización diferida asincrónica, representada por la `AsyncLazy<T>` clase:

```csharp
public class AsyncLazy<T> : Lazy<Task<T>>
{
    readonly Lazy<Task<T>> instance;

    public AsyncLazy(Func<T> factory)
    {
        instance = new Lazy<Task<T>>(() => Task.Run(factory));
    }

    public AsyncLazy(Func<Task<T>> factory)
    {
        instance = new Lazy<Task<T>>(() => Task.Run(factory));
    }

    public TaskAwaiter<T> GetAwaiter()
    {
        return instance.Value.GetAwaiter();
    }
}
```

La `AsyncLazy` clase combina los `Lazy<T>` `Task<T>` tipos y para crear una tarea de inicialización diferida que representa la inicialización de un recurso. El delegado de generador que se pasa al constructor puede ser sincrónico o asincrónico. Los delegados de fábrica se ejecutarán en un subproceso de grupo de subprocesos y no se ejecutarán más de una vez (incluso cuando varios subprocesos intenten iniciarlos simultáneamente). Cuando se completa un delegado de generador, el valor inicializado de forma diferida está disponible y los métodos que esperan a la `AsyncLazy<T>` instancia reciben el valor. Para más información, vea [AsyncLazy](https://devblogs.microsoft.com/pfxteam/asynclazyt/).

### <a name="data-manipulation-methods"></a>Métodos de manipulación de datos

La `TodoItemDatabase` clase incluye métodos para los cuatro tipos de manipulación de datos: crear, leer, editar y eliminar. La biblioteca SQLite.NET proporciona un mapa relacional de objetos simple (ORM) que permite almacenar y recuperar objetos sin escribir instrucciones SQL.

```csharp
public class TodoItemDatabase
{
    // ...
    public Task<List<TodoItem>> GetItemsAsync()
    {
        return Database.Table<TodoItem>().ToListAsync();
    }

    public Task<List<TodoItem>> GetItemsNotDoneAsync()
    {
        // SQL queries are also possible
        return Database.QueryAsync<TodoItem>("SELECT * FROM [TodoItem] WHERE [Done] = 0");
    }

    public Task<TodoItem> GetItemAsync(int id)
    {
        return Database.Table<TodoItem>().Where(i => i.ID == id).FirstOrDefaultAsync();
    }

    public Task<int> SaveItemAsync(TodoItem item)
    {
        if (item.ID != 0)
        {
            return Database.UpdateAsync(item);
        }
        else
        {
            return Database.InsertAsync(item);
        }
    }

    public Task<int> DeleteItemAsync(TodoItem item)
    {
        return Database.DeleteAsync(item);
    }
}
```

## <a name="access-data-in-xamarinforms"></a>Acceder a los datos en Xamarin.Forms

La `TodoItemDatabase` clase expone el `Instance` campo a través del cual se pueden invocar las operaciones de acceso a datos en la `TodoItemDatabase` clase:

```csharp
async void OnSaveClicked(object sender, EventArgs e)
{
    var todoItem = (TodoItem)BindingContext;
    TodoItemDatabase database = await TodoItemDatabase.Instance;
    await database.SaveItemAsync(todoItem);

    // Navigate backwards
    await Navigation.PopAsync();
}
```

## <a name="advanced-configuration"></a>Configuración avanzada

SQLite proporciona una sólida API con más características de las que se describen en este artículo y en la aplicación de ejemplo. En las secciones siguientes se tratan las características que son importantes para la escalabilidad.

Para obtener más información, consulte la [documentación de SQLite](https://www.sqlite.org/docs.html) en SQLite.org.

### <a name="write-ahead-logging"></a>Registro de escritura previa

De forma predeterminada, SQLite usa un diario de reversión tradicional. Una copia del contenido de la base de datos sin modificar se escribe en un archivo de reversión independiente y, a continuación, los cambios se escriben directamente en el archivo de base de datos. La confirmación se produce cuando se elimina el diario de reversión.

El registro de Write-Ahead (WAL) escribe los cambios en un archivo de WAL independiente en primer lugar. En el modo WAL, una confirmación es un registro especial que se anexa al archivo WAL, lo que permite que se produzcan varias transacciones en un único archivo de WAL. Un archivo WAL se vuelve a combinar en el archivo de base de datos en una operación especial denominada _punto de control_.

WAL puede ser más rápido para las bases de datos locales, ya que los lectores y los escritores no se bloquean entre sí, lo que permite que las operaciones de lectura y escritura estén simultáneas. Sin embargo, el modo WAL no permite cambios en el _tamaño de página_, agrega asociaciones de archivo adicionales a la base de datos y agrega la operación de _punto de comprobación_ adicional.

Para habilitar WAL en SQLite.NET, llame al `EnableWriteAheadLoggingAsync` método en la `SQLiteAsyncConnection` instancia de:

```csharp
await Database.EnableWriteAheadLoggingAsync();
```

Para obtener más información, consulte [SQLite Write-Ahead Logging](https://www.sqlite.org/wal.html) on SQLite.org.

### <a name="copy-a-database"></a>Copia de una base de datos

Hay varios casos en los que puede ser necesario copiar una base de datos de SQLite:

- Una base de datos se distribuye con la aplicación, pero debe copiarse o moverse al almacenamiento grabable en el dispositivo móvil.
- Debe realizar una copia de seguridad o una copia de la base de datos.
- Necesita la versión, el movimiento o el cambio de nombre del archivo de base de datos.

En general, mover, cambiar el nombre o copiar un archivo de base de datos es el mismo proceso que cualquier otro tipo de archivo con algunas consideraciones adicionales:

- Todas las conexiones de base de datos deben cerrarse antes de intentar trasladar el archivo de base de datos.
- Si usa el [registro de escritura previa](#write-ahead-logging), SQLite creará un archivo de acceso a memoria compartida (. SHM) y un archivo (. Wal). Asegúrese de aplicar también los cambios a estos archivos.

Para obtener más información, vea [control de Xamarin.Forms archivos en ](~/xamarin-forms/data-cloud/data/files.md).

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo todo](/samples/xamarin/xamarin-forms-samples/todo)
- [Paquete NuGet de SQLite.NET](https://www.nuget.org/packages/sqlite-net-pcl/)
- [Documentación de SQLite](https://www.sqlite.org/docs.html)
- [Uso de SQLite con Android](~/android/data-cloud/data-access/using-sqlite-orm.md)
- [Uso de SQLite con iOS](~/ios/data-cloud/data/using-sqlite-orm.md)
- [AsyncLazy](https://devblogs.microsoft.com/pfxteam/asynclazyt/)
