---
ms.openlocfilehash: b11a5972c2aabace8a6991a82f5719f34450297d
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2020
ms.locfileid: "67841516"
---
En este ejercicio, agregará clases de acceso a datos al proyecto **LocalDatabaseTutorial**, que se usará para conservar datos sobre personas en la base de datos.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. En el **Explorador de soluciones**, agregue una nueva clase denominada `Person` al proyecto **LocalDatabaseTutorial**. Después, en **Person.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Person
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Name { get; set; }
            public int Age { get; set; }
        }
    }
    ```

    Este código define una clase `Person` que almacenará los datos sobre cada persona en la aplicación. La propiedad `ID` está marcada con los atributos `PrimaryKey` y `AutoIncrement` para garantizar que cada instancia de `Person` en la base de datos tenga un identificador único proporcionado por SQLite.NET.

1. En el **Explorador de soluciones**, agregue una nueva clase denominada `Database` al proyecto **LocalDatabaseTutorial**. Después, en **Database.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Database
        {
            readonly SQLiteAsyncConnection _database;

            public Database(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Person>().Wait();
            }

            public Task<List<Person>> GetPeopleAsync()
            {
                return _database.Table<Person>().ToListAsync();
            }

            public Task<int> SavePersonAsync(Person person)
            {
                return _database.InsertAsync(person);
            }
        }
    }
    ```

    Esta clase contiene código para crear la base de datos, leer datos de ella y escribir datos en ella. El código usa API asincrónicas de SQLite.NET que mueven las operaciones de base de datos a subprocesos en segundo plano. Además, el constructor `Database` toma la ruta de acceso del archivo de base de datos como un argumento. La clase `App` proporcionará esta ruta de acceso en el siguiente ejercicio.

1. En el **Explorador de soluciones**, en el proyecto **LocalDatabaseTutorial**, expanda **App.xaml** y haga doble clic en **App.xaml.cs** para abrirlo. Después, en **App.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace LocalDatabaseTutorial
    {
        public partial class App : Application
        {
            static Database database;

            public static Database Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new Database(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "people.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();

                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                // Handle when your app starts
            }

            protected override void OnSleep()
            {
                // Handle when your app sleeps
            }

            protected override void OnResume()
            {
                // Handle when your app resumes
            }
        }
    }
    ```

    Este código define una propiedad `Database` que crea una instancia de `Database` como un singleton. Se pasan una ruta de acceso de archivo local y el nombre de archivo, que representa dónde se debe almacenar la base de datos, como argumento al constructor de clase `Database`.

    > [!IMPORTANT]
    > La ventaja de exponer la base de datos como un singleton es que se crea una conexión de base de datos única que se mantiene abierta mientras la aplicación se ejecuta, lo que evita el gasto de abrir y cerrar el archivo de base de datos cada vez que se realiza una operación de base de datos.

1. Compile la solución para asegurarse de que no haya errores.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En el **Panel de solución**, agregue una nueva clase denominada `Person` al proyecto **LocalDatabaseTutorial**. Después, en **Person.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Person
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Name { get; set; }
            public int Age { get; set; }
        }
    }
    ```

    Este código define una clase `Person` que almacenará los datos sobre cada persona en la aplicación. La propiedad `ID` está marcada con los atributos `PrimaryKey` y `AutoIncrement` para garantizar que cada instancia de `Person` en la base de datos tenga un identificador único proporcionado por SQLite.NET.

1. En el **Panel de solución**, agregue una nueva clase denominada `Database` al proyecto **LocalDatabaseTutorial**. Después, en **Database.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Database
        {
            readonly SQLiteAsyncConnection _database;

            public Database(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Person>().Wait();
            }

            public Task<List<Person>> GetPeopleAsync()
            {
                return _database.Table<Person>().ToListAsync();
            }

            public Task<int> SavePersonAsync(Person person)
            {
                return _database.InsertAsync(person);
            }
        }
    }
    ```

    Esta clase contiene código para crear la base de datos, leer datos de ella y escribir datos en ella. El código usa API asincrónicas de SQLite.NET que mueven las operaciones de base de datos a subprocesos en segundo plano. Además, el constructor `Database` toma la ruta de acceso del archivo de base de datos como un argumento. La clase `App` proporcionará esta ruta de acceso en el siguiente ejercicio.

1. En el **Panel de solución**, en el proyecto **LocalDatabaseTutorial**, expanda **App.xaml** y haga doble clic en **App.xaml.cs** para abrirlo. Después, en **App.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace LocalDatabaseTutorial
    {
        public partial class App : Application
        {
            static Database database;

            public static Database Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new Database(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "people.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();

                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                // Handle when your app starts
            }

            protected override void OnSleep()
            {
                // Handle when your app sleeps
            }

            protected override void OnResume()
            {
                // Handle when your app resumes
            }
        }
    }
    ```

    Este código define una propiedad `Database` que crea una instancia de `Database` como un singleton. Se pasan una ruta de acceso de archivo local y el nombre de archivo, que representa dónde se debe almacenar la base de datos, como argumento al constructor de clase `Database`.

    > [!IMPORTANT]
    > La ventaja de exponer la base de datos como un singleton es que se crea una conexión de base de datos única que se mantiene abierta mientras la aplicación se ejecuta, lo que evita el gasto de abrir y cerrar el archivo de base de datos cada vez que se realiza una operación de base de datos.

1. Compile la solución para asegurarse de que no haya errores.
