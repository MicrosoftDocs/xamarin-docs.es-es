---
title: System. Data en Xamarin. iOS
description: En este documento se describe cómo usar System. Data y mono. Data. SQLite. dll para tener acceso a los datos de SQLite en una aplicación de Xamarin. iOS.
ms.prod: xamarin
ms.assetid: F10C0C57-7BDE-A3F3-B011-9839949D15C8
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 11/25/2015
ms.openlocfilehash: 36eeb517fee69742f900a0cf18e1d19d093dfc52
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290114"
---
# <a name="systemdata-in-xamarinios"></a>System. Data en Xamarin. iOS

Xamarin. iOS 8,10 agrega compatibilidad con [System. Data](xref:System.Data), incluido el `Mono.Data.Sqlite.dll` proveedor ADO.net. La compatibilidad incluye la adición de los siguientes [ensamblados](~/cross-platform/internals/available-assemblies.md):

- `System.Data.dll`
- `System.Data.Service.Client.dll`
- `System.Transactions.dll`
- `Mono.Data.Tds.dll`
- `Mono.Data.Sqlite.dll`

<a name="Example" />

## <a name="example"></a>Ejemplo

El programa siguiente crea una base de `Documents/mydb.db3`datos de en y, si la base de datos no existe previamente, se rellena con datos de ejemplo. A continuación, se consulta la base de datos, con `stderr`la salida escrita en.

### <a name="add-references"></a>Agregar referencias

En primer lugar, haga clic con el botón derecho en el nodo **referencias** y elija **Editar referencias...** a continuación, seleccione `System.Data` y: `Mono.Data.Sqlite`

[![](system.data-images/edit-references-sml.png "Agregar nuevas referencias")](system.data-images/edit-references.png#lightbox)

### <a name="sample-code"></a>Código de ejemplo

En el código siguiente se muestra un ejemplo sencillo de creación de una tabla e inserción de filas mediante comandos SQL incrustados:

```csharp
using System;
using System.Data;
using System.IO;
using Mono.Data.Sqlite;

class Demo {
    static void Main (string [] args)
    {
        var connection = GetConnection ();
        using (var cmd = connection.CreateCommand ()) {
            connection.Open ();
            cmd.CommandText = "SELECT * FROM People";
            using (var reader = cmd.ExecuteReader ()) {
                while (reader.Read ()) {
                    Console.Error.Write ("(Row ");
                    Write (reader, 0);
                    for (nint i = 1; i < reader.FieldCount; ++i) {
                        Console.Error.Write(" ");
                        Write (reader, i);
                    }
                    Console.Error.WriteLine(")");
                }
            }
            connection.Close ();
        }
    }

    static SqliteConnection GetConnection()
    {
        var documents = Environment.GetFolderPath (
                Environment.SpecialFolder.Personal);
        string db = Path.Combine (documents, "mydb.db3");
        bool exists = File.Exists (db);
        if (!exists)
            SqliteConnection.CreateFile (db);
        var conn = new SqliteConnection("Data Source=" + db);
        if (!exists) {
            var commands = new[] {
            "CREATE TABLE People (PersonID INTEGER NOT NULL, FirstName ntext, LastName ntext)",
            // WARNING: never insert user-entered data with embedded parameter values
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (1, 'First', 'Last')",
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (2, 'Dewey', 'Cheatem')",
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (3, 'And', 'How')",
            };
            conn.Open ();
            foreach (var cmd in commands) {
                using (var c = conn.CreateCommand()) {
                    c.CommandText = cmd;
                    c.CommandType = CommandType.Text;
                    c.ExecuteNonQuery ();
                }
            }
            conn.Close ();
        }
        return conn;
    }

    static void Write(SqliteDataReader reader, int index)
    {
        Console.Error.Write("({0} '{1}')",
                reader.GetName(index),
                reader [index]);
    }
}
```

> [!IMPORTANT]
> Como se mencionó en el ejemplo de código anterior, no se recomienda incrustar cadenas en comandos SQL porque hace que el código sea vulnerable a la inyección de código [SQL](https://en.wikipedia.org/wiki/SQL_injection).


### <a name="using-command-parameters"></a>Uso de parámetros de comando

En el código siguiente se muestra cómo usar los parámetros de comando para insertar de forma segura el texto escrito por el usuario en la base de datos (incluso si el texto contiene caracteres SQL especiales como un apóstrofo):

```csharp
// user input from Textbox control
var fname = fnameTextbox.Text;
var lname = lnameTextbox.Text;
// use command parameters to safely insert into database
using (var addCmd = conn.CreateCommand ()) {
    addCmd.CommandText = "INSERT INTO [People] (PersonID, FirstName, LastName) VALUES (@COL1, @COL2, @COL3)";
    addCmd.CommandType = System.Data.CommandType.Text;
    addCmd.AddParameterWithValue ("@COL1", 1);
    addCmd.AddParameterWithValue ("@COL2", fname);
    addCmd.AddParameterWithValue ("@COL3", lname);
    addCmd.ExecuteNonQuery ();
}
```

<a name="Missing_Functionality" />

## <a name="missing-functionality"></a>Funcionalidad que falta

Falta alguna funcionalidad en **System. Data** y **mono. Data. SQLite** .

<a name="System.Data" />

### <a name="systemdata"></a>System.Data

La funcionalidad que falta en **System. Data. dll** consta de:

- Cualquier cosa que requiera [System. CodeDom](xref:System.CodeDom) (por ejemplo,  [System. Data. TypedDataSetGenerator](xref:System.Data.TypedDataSetGenerator) )
- Compatibilidad con archivos de configuración XML (por ejemplo,  [System. Data. Common. DbProviderConfigurationHandler](xref:System.Data.Common.DbProviderConfigurationHandler) )
- [System. Data. Common. DbProviderFactories](xref:System.Data.Common.DbProviderFactories) (depende de la compatibilidad con el archivo de configuración XML)
- [System.Data.OleDb](xref:System.Data.OleDb)
- [System.Data.Odbc](xref:System.Data.Odbc)
- La `System.EnterpriseServices.dll` dependencia se ha *quitado* de, lo que da lugar a `System.Data.dll` la eliminación del método [SqlConnection. EnlistDistributedTransaction (ITransaction)](xref:System.Data.SqlClient.SqlConnection.EnlistDistributedTransaction*) .


<a name="Mono.Data.Sqlite" />

### <a name="monodatasqlite"></a>Mono.Data.Sqlite

Mientras tanto, **mono. Data. SQLite. dll** no sufrió ningún cambio en el código fuente, sino que puede hospedarse en varios problemas en `Mono.Data.Sqlite.dll` tiempo de *ejecución* desde que enlaza SQLite 3,5. iOS 8, mientras tanto, se incluye con SQLite 3.8.5. Basta con decir que algunas cosas han cambiado entre las dos versiones.

La versión anterior de iOS se incluye con las siguientes versiones de SQLite:

- **iOS 7** : versión 3.7.13.
- **iOS 6** -versión 3.7.13.
- **iOS 5** -versión 3.7.7.
- **iOS 4** : versión 3.6.22.

Los problemas más comunes parecen estar relacionados con las consultas de esquema de la base de datos; por ejemplo, determinar en tiempo de ejecución Qué columnas existen `Mono.Data.Sqlite.SqliteConnection.GetSchema` en una tabla determinada, como ( `Mono.Data.Sqlite.SqliteDataReader.GetSchemaTable` invalidar [DbConnection. GetSchema](xref:System.Data.Common.DbConnection.GetSchema) e (reemplazar [). DbDataReader. GetSchemaTable](xref:System.Data.Common.DbDataReader.GetSchemaTable). En Resumen, parece que no funciona nada que use [DataTable](xref:System.Data.DataTable) .

<a name="Data_Binding" />

## <a name="data-binding"></a>Enlace de datos

El enlace de datos no se admite en este momento.

