---
title: Bases de datos locales de Xamarin.Forms
description: Xamarin.Forms admite aplicaciones de base de datos que usan el motor de base de datos de SQLite, lo que permite cargar y guardar los objetos en código compartido. En este artículo se describe cómo las aplicaciones de Xamarin.Forms pueden leer y escribir datos en una base de datos de SQLite local mediante SQLite.Net.
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 12/05/2019
ms.openlocfilehash: 2369afc693940d83971a43877da363e2c66b31b2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992394"
---
# <a name="xamarinforms-local-databases"></a>Bases de datos locales de Xamarin.Forms

[![Descargar](~/media/shared/download.png) ejemplo Descargue el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)

El motor de base de datos SQLite permite a las aplicaciones de Xamarin.Forms cargar y guardar objetos de datos en código compartido. La aplicación de ejemplo utiliza una tabla de base de datos SQLite para almacenar elementos de todo. En este artículo se describe cómo usar SQLite.Net en código compartido para almacenar y recuperar información en una base de datos local.

[![Capturas de pantalla de la aplicación Todolist en iOS y Android](databases-images/todo-list-sml.png)](databases-images/todo-list.png#lightbox "Aplicación Todolist en iOS y Android")

Integre SQLite.NET en aplicaciones móviles siguiendo estos pasos:

1. [Instale el paquete NuGet](#install-the-sqlite-nuget-package).
1. [Configurar constantes](#configure-app-constants).
1. [Cree una clase de acceso a la base](#create-a-database-access-class)de datos .
1. [Obtener acceso a los datos en Xamarin.Forms](#access-data-in-xamarinforms).
1. [Configuración avanzada](#advanced-configuration).

## <a name="install-the-sqlite-nuget-package"></a>Instale el paquete NuGet de SQLite

Use el Administrador de paquetes NuGet para buscar **sqlite-net-pcl** y agregar la versión más reciente al proyecto de código compartido.

Hay varios paquetes NuGet con nombres similares. El paquete correcto tiene estos atributos:

- **Creado por:** Frank A. Krueger
- **ID:** sqlite-net-pcl
- **Vínculo de NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> Independientemente del nombre del paquete, utilice el paquete de NuGet **sqlite-net-pcl** incluso en los proyectos de .NET Standard.

## <a name="configure-app-constants"></a>Configurar constantes de aplicación

El proyecto de ejemplo incluye un archivo **de Constants.cs** que proporciona datos de configuración comunes:

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

El archivo de constantes especifica los valores de enumeración predeterminados `SQLiteOpenFlag` que se utilizan para inicializar la conexión de base de datos. La `SQLiteOpenFlag` enumeración admite estos valores:

- `Create`: La conexión creará automáticamente el archivo de base de datos si no existe.
- `FullMutex`: la conexión se abre en modo de subprocesamiento serializado.
- `NoMutex`: La conexión se abre en modo multiproceso.
- `PrivateCache`: La conexión no participará en la memoria caché compartida, incluso si está habilitada.
- `ReadWrite`: La conexión puede leer y escribir datos.
- `SharedCache`: La conexión participará en la memoria caché compartida, si está habilitada.
- `ProtectionComplete`: El archivo está cifrado e inaccesible mientras el dispositivo está bloqueado.
- `ProtectionCompleteUnlessOpen`: El archivo se cifra hasta que se abre, pero es accesible incluso si el usuario bloquea el dispositivo.
- `ProtectionCompleteUntilFirstUserAuthentication`: El archivo se cifra hasta después de que el usuario ha arrancado y desbloqueado el dispositivo.
- `ProtectionNone`: el archivo de base de datos no está cifrado.

Es posible que deba especificar diferentes indicadores en función de cómo se usará la base de datos. Para obtener `SQLiteOpenFlags`más información acerca de , vea [Abrir una nueva conexión](https://www.sqlite.org/c3ref/open.html) de base de datos en sqlite.org.

## <a name="create-a-database-access-class"></a>Crear una clase de acceso a la base de datos

Una clase contenedora de base de datos abstrae la capa de acceso a datos del resto de la aplicación. Esta clase centraliza la lógica de consulta y simplifica la administración de la inicialización de la base de datos, lo que facilita la refactorización o ampliación de las operaciones de datos a medida que crece la aplicación. La aplicación Todo `TodoItemDatabase` define una clase para este propósito.

### <a name="lazy-initialization"></a>Inicialización diferida

Usa `TodoItemDatabase` la clase `Lazy` .NET para retrasar la inicialización de la base de datos hasta que se tenga acceso por primera vez. El uso de la inicialización diferida impide que el proceso de carga de la base de datos retrase el inicio de la aplicación. Para obtener más información, consulte [Lazy&lt;T&gt; Class](xref:System.Lazy`1).

```csharp
public class TodoItemDatabase
{
    static readonly Lazy<SQLiteAsyncConnection> lazyInitializer = new Lazy<SQLiteAsyncConnection>(() =>
    {
        return new SQLiteAsyncConnection(Constants.DatabasePath, Constants.Flags);
    });

    static SQLiteAsyncConnection Database => lazyInitializer.Value;
    static bool initialized = false;

    public TodoItemDatabase()
    {
        InitializeAsync().SafeFireAndForget(false);
    }

    async Task InitializeAsync()
    {
        if (!initialized)
        {
            if (!Database.TableMappings.Any(m => m.MappedType.Name == typeof(TodoItem).Name))
            {
                await Database.CreateTablesAsync(CreateFlags.None, typeof(TodoItem)).ConfigureAwait(false);
                initialized = true;
            }
        }
    }

    //...
}
```

La conexión de base de datos es un campo estático que garantiza que se utiliza una única conexión de base de datos durante la vida útil de la aplicación. El uso de una conexión estática persistente ofrece un mejor rendimiento que abrir y cerrar conexiones varias veces durante una sola sesión de aplicación.

El `InitializeAsync` método es responsable de comprobar si ya existe una tabla para almacenar `TodoItem` objetos. Este método crea automáticamente la tabla si no existe.

### <a name="the-safefireandforget-extension-method"></a>El método de extensión SafeFireAndForget

Cuando `TodoItemDatabase` se crea una instancia de la clase, debe inicializar la conexión de base de datos, que es un proceso asincrónico. Pero:

- Los constructores de clases no pueden ser asincrónicos.
- Un método asincrónico que no se espera no producirá excepciones.
- El `Wait` uso del método bloquea el subproceso _y_ se traga excepciones.

Para iniciar la inicialización asincrónica, evitar el bloqueo de la ejecución y tener la `SafeFireAndForget`oportunidad de detectar excepciones, la aplicación de ejemplo utiliza un método de extensión denominado . El `SafeFireAndForget` método de extensión proporciona `Task` funcionalidad adicional a la clase:

```csharp
public static class TaskExtensions
{
    // NOTE: Async void is intentional here. This provides a way
    // to call an async method from the constructor while
    // communicating intent to fire and forget, and allow
    // handling of exceptions
    public static async void SafeFireAndForget(this Task task,
        bool returnToCallingContext,
        Action<Exception> onException = null)
    {
        try
        {
            await task.ConfigureAwait(returnToCallingContext);
        }

        // if the provided action is not null, catch and
        // pass the thrown exception
        catch (Exception ex) when (onException != null)
        {
            onException(ex);
        }
    }
}
```

El `SafeFireAndForget` método espera la ejecución `Task` asincrónica del objeto proporcionado `Action` y permite adjuntar un que se llama si se produce una excepción.

Para obtener más información, vea [Patrón asincrónico basado en tareas (TAP)](https://docs.microsoft.com/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap).

### <a name="data-manipulation-methods"></a>Métodos de manipulación de datos

La `TodoItemDatabase` clase incluye métodos para los cuatro tipos de manipulación de datos: crear, leer, editar y eliminar. La biblioteca SQLite.NET proporciona un mapa relacional de objetos (ORM) simple que le permite almacenar y recuperar objetos sin escribir instrucciones SQL.

```csharp
public class TodoItemDatabase {

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

## <a name="access-data-in-xamarinforms"></a>Acceso a datos en Xamarin.Forms

La clase `App` Xamarin.Forms expone una `TodoItemDatabase` instancia de la clase:

```csharp
static TodoItemDatabase database;
public static TodoItemDatabase Database
{
    get
    {
        if (database == null)
        {
            database = new TodoItemDatabase();
        }
        return database;
    }
}
```

Esta propiedad permite a los componentes de Xamarin.Forms llamar a métodos de recuperación y manipulación de datos en la `Database` instancia en respuesta a la interacción del usuario. Por ejemplo:

```csharp
var saveButton = new Button { Text = "Save" };
saveButton.Clicked += async (sender, e) =>
{
    var todoItem = (TodoItem)BindingContext;
    await App.Database.SaveItemAsync(todoItem);
    await Navigation.PopAsync();
};
```

## <a name="advanced-configuration"></a>Configuración avanzada

SQLite proporciona una API sólida con más características de las que se tratan en este artículo y la aplicación de ejemplo. En las secciones siguientes se tratan las características que son importantes para la escalabilidad.

Para obtener más información, consulte Documentación de [SQLite](https://www.sqlite.org/docs.html) sobre sqlite.org.

### <a name="write-ahead-logging"></a>Registro de escritura anticipada

De forma predeterminada, SQLite utiliza un diario de reversión tradicional. Una copia del contenido de la base de datos sin cambios se escribe en un archivo de reversión independiente y, a continuación, los cambios se escriben directamente en el archivo de base de datos. El COMMIT se produce cuando se elimina el diario de reversión.

Write-Ahead Logging (WAL) escribe primero los cambios en un archivo WAL independiente. En el modo WAL, un COMMIT es un registro especial, anexado al archivo WAL, que permite que se produzcan varias transacciones en un único archivo WAL. Un archivo WAL se vuelve a combinar en el archivo de base de datos en una operación especial denominada _punto_de comprobación.

WAL puede ser más rápido para las bases de datos locales porque los lectores y escritores no se bloquean entre sí, lo que permite que las operaciones de lectura y escritura sean simultáneas. Sin embargo, el modo WAL no permite cambios en el tamaño de _página,_ agrega asociaciones de archivos adicionales a la base de datos y agrega la operación de punto de _comprobación_ adicional.

Para habilitar WAL en `EnableWriteAheadLoggingAsync` SQLite.NET, `SQLiteAsyncConnection` llame al método en la instancia:

```csharp
await Database.EnableWriteAheadLoggingAsync();
```

Para obtener más información, consulte [SQLite Write-Ahead Logging](https://www.sqlite.org/wal.html) on sqlite.org.

### <a name="copying-a-database"></a>Copiar una base de datos

Hay varios casos en los que puede ser necesario copiar una base de datos SQLite:

- Una base de datos se ha enviado con la aplicación, pero debe copiarse o moverse al almacenamiento grabable en el dispositivo móvil.
- Debe realizar una copia de seguridad o una copia de la base de datos.
- Debe versionar, mover o cambiar el nombre del archivo de base de datos.

En general, mover, cambiar el nombre o copiar un archivo de base de datos es el mismo proceso que cualquier otro tipo de archivo con algunas consideraciones adicionales:

- Todas las conexiones de base de datos deben cerrarse antes de intentar mover el archivo de base de datos.
- Si utiliza [Write-Ahead Logging](#write-ahead-logging), SQLite creará un archivo de acceso a memoria compartida (.shm) y un archivo (Write Ahead Log) (.wal). Asegúrese de aplicar cualquier cambio a estos archivos también.

Para obtener más información, vea Control de archivos [en Xamarin.Forms](~/xamarin-forms/data-cloud/data/files.md).

## <a name="related-links"></a>Vínculos relacionados

- [Todo aplicación de muestra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)
- [SQLite.NET paquete NuGet](https://www.nuget.org/packages/sqlite-net-pcl/)
- [Documentación de SQLite](https://www.sqlite.org/docs.html)
- [Uso de SQLite con Android](~/android/data-cloud/data-access/using-sqlite-orm.md)
- [Uso de SQLite con iOS](~/ios/data-cloud/data/using-sqlite-orm.md)
- [Patrón asincrónico basado en tareas (TAP)](https://docs.microsoft.com/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)
- [Lazy&lt;&gt; T Clase](xref:System.Lazy`1)
