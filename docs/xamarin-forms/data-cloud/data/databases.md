---
title: Bases de datos locales de Xamarin.Forms
description: Xamarin.Forms admite aplicaciones de base de datos que usan el motor de base de datos de SQLite, lo que permite cargar y guardar los objetos en código compartido. En este artículo se describe cómo las aplicaciones de Xamarin.Forms pueden leer y escribir datos en una base de datos de SQLite local mediante SQLite.Net.
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 9ea105b27aacef9ca9d63af0c57de880d039ff53
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739178"
---
# <a name="xamarinforms-local-databases"></a>Bases de datos locales de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)

_Xamarin.Forms admite aplicaciones de base de datos que usan el motor de base de datos de SQLite, lo que permite cargar y guardar los objetos en código compartido. En este artículo se describe cómo las aplicaciones de Xamarin.Forms pueden leer y escribir datos en una base de datos de SQLite local mediante SQLite.Net._

## <a name="overview"></a>Información general

Las aplicaciones de Xamarin.Forms pueden usar el paquete de [NuGet SQLite.NET PCL](https://www.nuget.org/packages/sqlite-net-pcl/) para incorporar las operaciones de base de datos en el código compartido haciendo referencia a las clases `SQLite` que se incluyen en el NuGet. Las operaciones de base de datos se pueden definir en el proyecto de biblioteca de .NET Standard de la solución de Xamarin.Forms.

La [aplicación de ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo) acompañante es una sencilla aplicación de lista de tareas. Las siguientes capturas de pantalla muestran cómo el ejemplo aparece en cada plataforma:

[![Capturas de pantalla de ejemplo de base de datos de Xamarin.Forms](databases-images/todo-list-sml.png "Capturas de pantalla de primera página de lista de tareas pendientes")](databases-images/todo-list.png#lightbox "TodoList First Page Screenshots") [![ Capturas de pantalla de ejemplo de base de datos de Xamarin.Forms](databases-images/todo-list-sml.png "Capturas de pantalla de primera página de lista de tareas pendientes")](databases-images/todo-list.png#lightbox "TodoList First Page Screenshots")

<a name="Using_SQLite_with_PCL" />

## <a name="using-sqlite"></a>Uso de SQLite

Para agregar compatibilidad de SQLite a una biblioteca de .NET Standard de Xamarin.Forms, use la función de búsqueda de NuGet para buscar **sqlite-net-pcl** e instalar el paquete más reciente:

![Agregar paquete de NuGet SQLite.NET PCL](databases-images/vs2017-sqlite-pcl-nuget.png "Add NuGet SQLite.NET PCL Package")

Hay una serie de paquetes de NuGet con nombres similares, el paquete correcto tiene estos atributos:

- **Creado por:** Frank A. Krueger
- **Id.:** sqlite-net-pcl
- **Vínculo de NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> Independientemente del nombre del paquete, utilice el paquete de NuGet **sqlite-net-pcl** incluso en los proyectos de .NET Standard.

Una vez agregada la referencia, agregue una propiedad a la clase `App` que devuelve una ruta de acceso local para almacenar la base de datos:

```csharp
static TodoItemDatabase database;

public static TodoItemDatabase Database
{
  get
  {
    if (database == null)
    {
      database = new TodoItemDatabase(
        Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "TodoSQLite.db3"));
    }
    return database;
  }
}
```

El constructor `TodoItemDatabase`, que toma la ruta de acceso del archivo de base de datos como un argumento, se muestra a continuación:

```csharp
public TodoItemDatabase(string dbPath)
{
  database = new SQLiteAsyncConnection(dbPath);
  database.CreateTableAsync<TodoItem>().Wait();
}
```

La ventaja de exponer la base de datos como un singleton es que se crea una conexión de base de datos única que se mantiene abierta mientras la aplicación se ejecuta, lo que evita el gasto de abrir y cerrar el archivo de base de datos cada vez que se realiza una operación de base de datos.

El resto de la clase `TodoItemDatabase` contiene consultas de SQLite que se ejecutan entre plataformas. Debajo se muestra el código de ejemplo de consulta (puede encontrar más información sobre la sintaxis en [Uso de SQLite.NET con Xamarin.iOS](~/ios/data-cloud/data/using-sqlite-orm.md).

```csharp
public Task<List<TodoItem>> GetItemsAsync()
{
  return database.Table<TodoItem>().ToListAsync();
}

public Task<List<TodoItem>> GetItemsNotDoneAsync()
{
  return database.QueryAsync<TodoItem>("SELECT * FROM [TodoItem] WHERE [Done] = 0");
}

public Task<TodoItem> GetItemAsync(int id)
{
  return database.Table<TodoItem>().Where(i => i.ID == id).FirstOrDefaultAsync();
}

public Task<int> SaveItemAsync(TodoItem item)
{
  if (item.ID != 0)
  {
    return database.UpdateAsync(item);
  }
  else {
    return database.InsertAsync(item);
  }
}

public Task<int> DeleteItemAsync(TodoItem item)
{
  return database.DeleteAsync(item);
}
```

> [!NOTE]
> La ventaja de usar la API de SQLite.Net asincrónica es que las operaciones de base de datos se mueven a subprocesos en segundo plano. Además, no es necesario escribir código de control de simultaneidad adicional porque la API se encarga de ello.

## <a name="summary"></a>Resumen

Xamarin.Forms admite aplicaciones de base de datos que usan el motor de base de datos de SQLite, lo que permite cargar y guardar los objetos en código compartido.

Este artículo se centra en el **acceso** a una base de datos SQLite mediante Xamarin.Forms. Para obtener más información sobre cómo trabajar con SQLite.Net, vea la documentación de [SQLite.NET en Android](~/android/data-cloud/data-access/using-sqlite-orm.md) o [SQLite.NET en iOS](~/ios/data-cloud/data/using-sqlite-orm.md).

## <a name="related-links"></a>Vínculos relacionados

- [Todo Sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo) (Ejemplo de tareas)
- [Xamarin.Forms Samples](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms) (Ejemplos de Xamarin.Forms)
