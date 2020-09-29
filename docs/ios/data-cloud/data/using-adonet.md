---
title: Uso de ADO.NET con Xamarin. iOS
description: En este documento se describe cómo usar ADO.NET como método para tener acceso a SQLite en una aplicación de Xamarin. iOS. Se describen las referencias de ensamblado, mono. Data. SQLite y el ejemplo BasicDataAccess.
ms.prod: xamarin
ms.assetid: 79078A4D-2D24-44F3-9543-B50418A7A000
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: d72a9722a9d48ea52932e4fd6516c0712dbd693c
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436253"
---
# <a name="using-adonet-with-xamarinios"></a>Uso de ADO.NET con Xamarin. iOS

Xamarin tiene compatibilidad integrada para la base de datos SQLite que está disponible en iOS y se expone mediante la conocida sintaxis de ADO.NET. El uso de estas API requiere la escritura de instrucciones SQL procesadas por SQLite, como `CREATE TABLE` , `INSERT` y las `SELECT` instrucciones.

## <a name="assembly-references"></a>Referencias de ensamblado

Para usar Access SQLite a través de ADO.NET, debe agregar `System.Data` `Mono.Data.Sqlite` referencias y a su proyecto de iOS, como se muestra aquí (para obtener ejemplos en Visual Studio para Mac y Visual Studio):

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

 ![Referencias de ensamblado en Visual Studio para Mac](using-adonet-images/image4.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

  ![Referencias de ensamblados en Visual Studio](using-adonet-images/image6.png)

-----

Haga clic con el botón secundario en **referencias > Editar referencias...** y, a continuación, haga clic para seleccionar los ensamblados necesarios.

## <a name="about-monodatasqlite"></a>Acerca de mono. Data. SQLite

Usaremos la `Mono.Data.Sqlite.SqliteConnection` clase para crear un archivo de base de datos en blanco y, a continuación, crear una instancia de `SqliteCommand` los objetos que se pueden usar para ejecutar instrucciones SQL en la base de datos.

1. **Crear una base de datos en blanco** : llame al `CreateFile` método con una ruta de acceso de archivo válida (es decir, grabable). Debe comprobar si el archivo ya existe antes de llamar a este método; de lo contrario, se creará una nueva base de datos (en blanco) en la parte superior de la antigua y se perderán los datos del archivo anterior:

    `Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);`

    > [!NOTE]
    > La `dbPath` variable se debe determinar según las reglas descritas anteriormente en este documento.

2. **Crear una conexión de base** de datos: una vez creado el archivo de base de datos de SQLite, puede crear un objeto de conexión para obtener acceso a los datos. La conexión se crea con una cadena de conexión que toma la forma de `Data Source=file_path` , como se muestra a continuación:

    ```csharp
    var connection = new SqliteConnection ("Data Source=" + dbPath);
    connection.Open();
    // do stuff
    connection.Close();
    ```

    Como se mencionó anteriormente, nunca se debe volver a usar una conexión entre subprocesos diferentes. En caso de duda, cree la conexión según sea necesario y ciérrela cuando haya terminado. pero sea consciente de hacerlo con más frecuencia de lo requerido.

3. **Crear y ejecutar un comando de base de datos** : una vez que tenemos una conexión, podemos ejecutar comandos SQL arbitrarios en ella. En el código siguiente se muestra una CREATE TABLE instrucción que se está ejecutando.

    ```csharp
    using (var command = connection.CreateCommand ()) {
        command.CommandText = "CREATE TABLE [Items] ([_id] int, [Symbol] ntext, [Name] ntext);";
        var rowcount = command.ExecuteNonQuery ();
    }
    ```

Al ejecutar SQL directamente en la base de datos, debe tomar las precauciones normales para no realizar solicitudes no válidas, como intentar crear una tabla que ya existe. Realice un seguimiento de la estructura de la base de datos para que no se produzca un SqliteException como "la tabla de errores de SQLite [items] ya existe".

## <a name="basic-data-access"></a>Acceso a datos básico

El *DataAccess_Basic* código de ejemplo de este documento tiene el siguiente aspecto cuando se ejecuta en iOS:

 ![ejemplo de ADO.NET de iOS](using-adonet-images/image9.png)

En el código siguiente se muestra cómo realizar operaciones de SQLite simples y se muestra el resultado como texto en la ventana principal de la aplicación.

Deberá incluir estos espacios de nombres:

```csharp
using System;
using System.IO;
using Mono.Data.Sqlite;
```

En el ejemplo de código siguiente se muestra una interacción completa de la base de datos:

1. Crear el archivo de base de datos
2. Insertar algunos datos
3. Consultas de datos

Normalmente, estas operaciones aparecen en varios lugares en todo el código; por ejemplo, puede crear el archivo y las tablas de base de datos cuando la aplicación se inicia por primera vez y realizar lecturas y escrituras de datos en pantallas individuales de la aplicación. En el ejemplo siguiente, se ha agrupado en un único método para este ejemplo:

```csharp
public static SqliteConnection connection;
public static string DoSomeDataAccess ()
{
    // determine the path for the database file
    string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "adodemo.db3");

    bool exists = File.Exists (dbPath);

    if (!exists) {
        Console.WriteLine("Creating database");
        // Need to create the database before seeding it with some data
        Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);
        connection = new SqliteConnection ("Data Source=" + dbPath);

        var commands = new[] {
            "CREATE TABLE [Items] (_id ntext, Symbol ntext);",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'AAPL')",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('2', 'GOOG')",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('3', 'MSFT')"
        };
        // Open the database connection and create table with data
        connection.Open ();
        foreach (var command in commands) {
            using (var c = connection.CreateCommand ()) {
                c.CommandText = command;
                var rowcount = c.ExecuteNonQuery ();
                Console.WriteLine("\tExecuted " + command);
            }
        }
    } else {
        Console.WriteLine("Database already exists");
        // Open connection to existing database file
        connection = new SqliteConnection ("Data Source=" + dbPath);
        connection.Open ();
    }

    // query the database to prove data was inserted!
    using (var contents = connection.CreateCommand ()) {
        contents.CommandText = "SELECT [_id], [Symbol] from [Items]";
        var r = contents.ExecuteReader ();
        Console.WriteLine("Reading data");
        while (r.Read ())
            Console.WriteLine("\tKey={0}; Value={1}",
                              r ["_id"].ToString (),
                              r ["Symbol"].ToString ());
    }
    connection.Close ();
}
```

## <a name="more-complex-queries"></a>Consultas más complejas

Dado que SQLite permite que se ejecuten comandos SQL arbitrarios en los datos, puede realizar cualquier instrucción de creación, inserción, actualización, eliminación o selección que desee. Puede leer acerca de los comandos SQL compatibles con SQLite en el sitio web de SQLite. Las instrucciones SQL se ejecutan con uno de los tres métodos en un objeto SqliteCommand:

- **ExecuteNonQuery** : se usa normalmente para la creación de tablas o la inserción de datos. El valor devuelto para algunas operaciones es el número de filas afectadas; de lo contrario, es-1.
- **ExecuteReader** : se utiliza cuando se debe devolver una colección de filas como  `SqlDataReader` .
- **ExecuteScalar** : recupera un valor único (por ejemplo, un agregado).

### <a name="executenonquery"></a>EXECUTENONQUERY

Las instrucciones INSERT, UPDATE y DELETE devolverán el número de filas afectadas. Todas las demás instrucciones SQL devolverán-1.

```csharp
using (var c = connection.CreateCommand ()) {
    c.CommandText = "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'APPL')";
    var rowcount = c.ExecuteNonQuery (); // rowcount will be 1
}
```

### <a name="executereader"></a>EXECUTEREADER

El método siguiente muestra una cláusula WHERE en la instrucción SELECT. Dado que el código está creando una instrucción SQL completa, debe tener cuidado de escapar caracteres reservados como la comilla (') en torno a las cadenas.

```csharp
public static string MoreComplexQuery ()
{
    var output = "";
    output += "\nComplex query example: ";
    string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal), "ormdemo.db3");

    connection = new SqliteConnection ("Data Source=" + dbPath);
    connection.Open ();
    using (var contents = connection.CreateCommand ()) {
        contents.CommandText = "SELECT * FROM [Items] WHERE Symbol = 'MSFT'";
        var r = contents.ExecuteReader ();
        output += "\nReading data";
        while (r.Read ())
            output += String.Format ("\n\tKey={0}; Value={1}",
                    r ["_id"].ToString (),
                    r ["Symbol"].ToString ());
    }
    connection.Close ();

    return output;
}
```

El método ExecuteReader devuelve un objeto SqliteDataReader. Además del método Read mostrado en el ejemplo, otras propiedades útiles incluyen:

- **RowsAffected** : recuento de las filas afectadas por la consulta.
- **HasRows** : indica si se devolvieron filas.

### <a name="executescalar"></a>EXECUTESCALAR

Utilice esto para las instrucciones SELECT que devuelven un valor único (como un agregado).

```csharp
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT COUNT(*) FROM [Items] WHERE Symbol <> 'MSFT'";
    var i = contents.ExecuteScalar ();
}
```

El `ExecuteScalar` tipo de valor devuelto del método es `object` : debe convertir el resultado en función de la consulta de la base de datos. El resultado puede ser un entero de una consulta de recuento o una cadena de una consulta SELECT de una sola columna. Tenga en cuenta que esto es diferente a otros métodos Execute que devuelven un objeto lector o un recuento del número de filas afectadas.

## <a name="microsoftdatasqlite"></a>Microsoft.Data.Sqlite

Hay otra biblioteca `Microsoft.Data.Sqlite` , que se puede [instalar desde NuGet](https://www.nuget.org/packages/Microsoft.Data.Sqlite), que es funcionalmente equivalente a `Mono.Data.Sqlite` y permite los mismos tipos de consultas.

Hay una [comparación entre las dos bibliotecas](/dotnet/standard/data/sqlite/compare) y algunos [detalles específicos de Xamarin](/dotnet/standard/data/sqlite/xamarin). Lo más importante para las aplicaciones de Xamarin. iOS, debe incluir una llamada de inicialización:

```csharp
// required for Xamarin.iOS
SQLitePCL.Batteries_V2.Init();
```

## <a name="related-links"></a>Vínculos relacionados

- [Acceso a datos básico (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [Acceso a la configuración avanzada (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Recetas de datos de iOS](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Acceso a datos de Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)