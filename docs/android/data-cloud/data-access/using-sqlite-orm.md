---
title: Uso de SQLite.NET con Android
description: La biblioteca SQLite.NET PCL NuGet proporciona un mecanismo de acceso a datos simple para las aplicaciones de Xamarin. Android.
ms.prod: xamarin
ms.assetid: 3447B7EE-A320-489E-AF02-E5721097760A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/18/2018
ms.openlocfilehash: c684efe9bcb292869eb25b36067fcaa42b546a04
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69525923"
---
# <a name="using-sqlitenet-with-android"></a>Uso de SQLite.NET con Android

La biblioteca SQLite.NET que Xamarin recomienda es un ORM muy básico que le permite almacenar y recuperar fácilmente objetos en la base de datos SQLite local en un dispositivo Android. ORM representa la asignación &ndash; relacional de objetos API que permite guardar y recuperar "objetos" de una base de datos sin escribir instrucciones SQL.

Para incluir la biblioteca de SQLite.NET en una aplicación de Xamarin, agregue el siguiente paquete de NuGet al proyecto:

- **Nombre del paquete:** SQLite-net-PCL
- **Autor:** Frank A. Krueger
- **Id.:** sqlite-net-pcl
- **Url:** [nuget.org/packages/sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

[![Paquete NuGet de SQLite.net](using-sqlite-orm-images/image1a-sml.png "Paquete NuGet de SQLite.net")](using-sqlite-orm-images/image1a.png#lightbox)

> [!TIP]
> Hay varios paquetes de SQLite disponibles: Asegúrese de elegir el correcto (puede que no sea el resultado superior en la búsqueda).

Una vez que tenga la biblioteca SQLite.NET disponible, siga estos tres pasos para usarla para tener acceso a una base de datos:

1. **Agregar una instrucción using** Agregue la siguiente instrucción a los C# archivos en los que se requiere acceso a los datos: &ndash;

    ```csharp
    using SQLite;
    ```

2. **Crear una base de datos en blanco** &ndash; Se puede crear una referencia de base de datos pasando la ruta de acceso del archivo al constructor de la clase SQLiteConnection. No es necesario comprobar si el archivo ya existe &ndash; , se creará automáticamente si es necesario; de lo contrario, se abrirá el archivo de base de datos existente. La `dbPath` variable se debe determinar según las reglas descritas anteriormente en este documento:

    ```csharp
    var db = new SQLiteConnection (dbPath);
    ```

3. **Guardar datos** &ndash; Una vez que haya creado un objeto SQLiteConnection, los comandos de base de datos se ejecutan llamando a sus métodos, como createTable e insertan de la siguiente manera:

    ```csharp
    db.CreateTable<Stock> ();
    db.Insert (newStock); // after creating the newStock object
    ```

4. **Recuperación de datos** &ndash; Para recuperar un objeto (o una lista de objetos), use la sintaxis siguiente:

    ```csharp
    var stock = db.Get<Stock>(5); // primary key id of 5
    var stockList = db.Table<Stock>();
    ```

## <a name="basic-data-access-sample"></a>Ejemplo de acceso a datos básico

El código de ejemplo *DataAccess_Basic* de este documento tiene este aspecto cuando se ejecuta en Android. En el código se muestra cómo realizar operaciones simples de SQLite.NET y se muestra el resultado como texto en la ventana principal de la aplicación.


**Android**

![Ejemplo de SQLite.net de Android](using-sqlite-orm-images/image3.png "Ejemplo de SQLite.net de Android")

En el ejemplo de código siguiente se muestra una interacción completa de la base de datos mediante la biblioteca SQLite.NET para encapsular el acceso a la base de datos subyacente.
Muestra lo siguiente:

1. Crear el archivo de base de datos

2. Inserción de datos mediante la creación de objetos y su almacenamiento

3. Consultar los datos

Deberá incluir estos espacios de nombres:

```csharp
using SQLite; // from the github SQLite.cs class
```

La última requiere que haya agregado SQLite al proyecto. Tenga en cuenta que la tabla de base de datos SQLite se define agregando atributos `Stock` a una clase (la clase) en lugar de un comando CREATE TABLE.

```csharp
[Table("Items")]
public class Stock {
    [PrimaryKey, AutoIncrement, Column("_id")]
    public int Id { get; set; }
    [MaxLength(8)]
    public string Symbol { get; set; }
}
public static void DoSomeDataAccess () {
       Console.WriteLine ("Creating database, if it doesn't already exist");
   string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "ormdemo.db3");
   var db = new SQLiteConnection (dbPath);
   db.CreateTable<Stock> ();
   if (db.Table<Stock> ().Count() == 0) {
        // only insert the data if it doesn't already exist
        var newStock = new Stock ();
        newStock.Symbol = "AAPL";
        db.Insert (newStock);
        newStock = new Stock ();
        newStock.Symbol = "GOOG";
        db.Insert (newStock);
        newStock = new Stock ();
        newStock.Symbol = "MSFT";
        db.Insert (newStock);
    }
    Console.WriteLine("Reading data");
    var table = db.Table<Stock> ();
    foreach (var s in table) {
        Console.WriteLine (s.Id + " " + s.Symbol);
    }
}
```

El uso `[Table]` del atributo sin especificar un parámetro de nombre de tabla hará que la tabla de base de datos subyacente tenga el mismo nombre que la clase (en este caso, "stock"). El nombre de tabla real es importante si escribe consultas SQL directamente en la base de datos en lugar de usar los métodos de acceso a datos de ORM. Del mismo `[Column("_id")]` modo, el atributo es opcional y, si no está presente, se agregará una columna a la tabla con el mismo nombre que la propiedad de la clase.

## <a name="sqlite-attributes"></a>Atributos de SQLite

Los atributos comunes que se pueden aplicar a las clases para controlar cómo se almacenan en la base de datos subyacente incluyen:

- **[PrimaryKey]** &ndash; Este atributo se puede aplicar a una propiedad de entero para obligarlo a ser la clave principal de la tabla subyacente. No se admiten las claves principales compuestas.

- **[Incremento automático]** &ndash; Este atributo hará que el valor de una propiedad de entero se incremente automáticamente para cada nuevo objeto insertado en la base de datos.

- **[Column (Name)]** Si se proporciona `name` el parámetro opcional, se invalidará el valor predeterminado del nombre de la columna de base de datos subyacente (que es igual que la propiedad). &ndash;

- **[Tabla (nombre)]** &ndash; Marca la clase como capaz de almacenarse en una tabla subyacente de SQLite. Si se especifica el parámetro de nombre opcional, se invalidará el valor predeterminado del nombre de la tabla de base de datos subyacente (que es igual que el nombre de clase).

- **[MaxLength (valor)]** &ndash; Restrinja la longitud de una propiedad de texto cuando se intente realizar una inserción de base de datos. El consumo de código debe validar esto antes de insertar el objeto, ya que este atributo solo es ' checked ' cuando se intenta realizar una operación de actualización o inserción de base de datos.

- **[Omitir]** &ndash; Hace que SQLite.net omita esta propiedad.
    Esto es especialmente útil para las propiedades que tienen un tipo que no se puede almacenar en la base de datos, o propiedades que modelan recopilaciones que no se pueden resolver automáticamente mediante SQLite.

- **[Único]** &ndash; Garantiza que los valores de la columna de base de datos subyacente son únicos.


La mayoría de estos atributos son opcionales, SQLite usará los valores predeterminados para los nombres de tabla y columna. Siempre debe especificar una clave principal de tipo entero para que las consultas de selección y eliminación se puedan realizar de forma eficaz en los datos.

## <a name="more-complex-queries"></a>Consultas más complejas

Los siguientes métodos de `SQLiteConnection` se pueden usar para realizar otras operaciones de datos:

- **Insertar** &ndash; Agrega un nuevo objeto a la base de datos.

- **Obtener&lt;T&gt;intentarecuperar un objeto** mediantelaclaveprincipal&ndash; .

- **La&lt;tablaT&gt;**  devuelve&ndash; todos los objetos de la tabla.

- **Eliminar** &ndash; Elimina un objeto utilizando su clave principal.

- **Query&lt;T&gt;realizauna consulta SQL** quedevuelveunnúmerodefilas(comoobjetos).&ndash;

- **Ejecutar** Use este método (y no `Query`) cuando no espere filas de SQL (como las instrucciones INSERT, Update y Delete). &ndash;


### <a name="getting-an-object-by-the-primary-key"></a>Obtener un objeto por la clave principal

SQLite.Net proporciona el método get para recuperar un solo objeto basado en su clave principal.

```csharp
var existingItem = db.Get<Stock>(3);
```

### <a name="selecting-an-object-using-linq"></a>Seleccionar un objeto mediante Linq

Los métodos que devuelven la compatibilidad con `IEnumerable<T>` colecciones, por lo que puede usar LINQ para consultar u ordenar el contenido de una tabla. En el código siguiente se muestra un ejemplo de uso de LINQ para filtrar todas las entradas que comienzan por la letra "A":

```csharp
var apple = from s in db.Table<Stock>()
    where s.Symbol.StartsWith ("A")
    select s;
Console.WriteLine ("-> " + apple.FirstOrDefault ().Symbol);
```

### <a name="selecting-an-object-using-sql"></a>Seleccionar un objeto mediante SQL

Aunque SQLite.Net puede proporcionar acceso basado en objetos a los datos, a veces es posible que tenga que realizar una consulta más compleja de lo que permite LINQ (o puede necesitar un rendimiento más rápido). Puede usar comandos SQL con el método de consulta, como se muestra aquí:

```csharp
var stocksStartingWithA = db.Query<Stock>("SELECT * FROM Items WHERE Symbol = ?", "A");
foreach (var s in stocksStartingWithA) {
    Console.WriteLine ("a " + s.Symbol);
}
```

> [!NOTE]
> Al escribir instrucciones SQL directamente, se crea una dependencia de los nombres de las tablas y columnas de la base de datos, que se han generado a partir de las clases y sus atributos. Si cambia estos nombres en el código, debe recordar actualizar las instrucciones SQL escritas manualmente.

### <a name="deleting-an-object"></a>Eliminar un objeto

La clave principal se usa para eliminar la fila, como se muestra aquí:

```csharp
var rowcount = db.Delete<Stock>(someStock.Id); // Id is the primary key
```

Puede comprobar el para `rowcount` confirmar el número de filas afectadas (eliminadas en este caso).

## <a name="using-sqlitenet-with-multiple-threads"></a>Usar SQLite.NET con varios subprocesos

SQLite admite tres modos de subprocesamiento diferentes: *Subproceso único*, *multiproceso*y serializado. Si desea tener acceso a la base de datos desde varios subprocesos sin restricciones, puede configurar SQLite para usar el modo de subproceso serializado. Es importante establecer este modo pronto en la aplicación (por ejemplo, al principio del `OnCreate` método).

Para cambiar el modo de subprocesos `SqliteConnection.SetConfig`, llame a. Por ejemplo, esta línea de código configura SQLite para el modo de serialización:

```csharp
using using Mono.Data.Sqlite;
...
SqliteConnection.SetConfig(SQLiteConfig.Serialized);
```

La versión de Android de SQLite tiene una limitación que requiere algunos pasos más. Si la llamada a `SqliteConnection.SetConfig` genera una excepción de SQLite `library used incorrectly`como, debe usar la siguiente solución alternativa:

1. Vínculo a la biblioteca **libsqlite.so** nativa para que las `sqlite3_shutdown` API `sqlite3_initialize` y estén disponibles para la aplicación:

    ```csharp
    [DllImport("libsqlite.so")]
    internal static extern int sqlite3_shutdown();

    [DllImport("libsqlite.so")]
    internal static extern int sqlite3_initialize();
    ```

2. Al principio del método, agregue `OnCreate` este código al cierre de SQLite, configúrelo para el modo de **serialización** y reinicialice SQLite:

    ```csharp
    using using Mono.Data.Sqlite;
    ...
    sqlite3_shutdown();
    SqliteConnection.SetConfig(SQLiteConfig.Serialized);
    sqlite3_initialize();
    ```

Esta solución alternativa también funciona para `Mono.Data.Sqlite` la biblioteca. Para obtener más información acerca de SQLite y multithreading, consulte [SQLite y varios](https://www.sqlite.org/threadsafe.html)subprocesos.

## <a name="related-links"></a>Vínculos relacionados

- [Acceso a datos básico (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [Acceso a la configuración avanzada (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Acceso a datos de Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
