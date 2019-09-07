---
title: Uso de datos en una aplicación Android
ms.prod: xamarin
ms.assetid: D5932AEB-0B6E-4F37-8B32-9BE4775AEE85
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/08/2018
ms.openlocfilehash: 922b1fa411a176df580050384e7555120fd68137
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754451"
---
# <a name="using-data-in-an-app"></a>Uso de datos en una aplicación

En el ejemplo **DataAccess_Adv** se muestra una aplicación de trabajo que permite la entrada del usuario y la funcionalidad de la base de datos CRUD (crear, leer, actualizar y eliminar). La aplicación consta de dos pantallas: una lista y un formulario de entrada de datos. Todo el código de acceso a datos se puede volver a usar en iOS y Android sin modificarlo.

Después de agregar algunos datos, las pantallas de la aplicación tienen el siguiente aspecto en Android:

![Lista de ejemplos de Android](using-data-in-an-app-images/image11.png "Lista de ejemplos de Android")

![Detalle de ejemplo de Android](using-data-in-an-app-images/image12.png "Detalle de ejemplo de Android")

El proyecto de Android se muestra &ndash; debajo del código que se muestra en esta sección se encuentra en el directorio **ORM** :

![Árbol del proyecto de Android](using-data-in-an-app-images/image14.png "Árbol del proyecto de Android")

El código de interfaz de usuario nativo para las actividades en Android está fuera del ámbito de este documento. Consulte la guía de los [adaptadores y ListView de Android](~/android/user-interface/layouts/list-view/index.md) para obtener más información sobre los controles de interfaz de usuario.

## <a name="read"></a>Lectura

Hay un par de operaciones de lectura en el ejemplo:

- Lectura de la lista
- Leer registros individuales

Los dos métodos de la `StockDatabase` clase son:

```csharp
public IEnumerable<Stock> GetStocks ()
{
    lock (locker) {
        return (from i in Table<Stock> () select i).ToList ();
    }
}
public Stock GetStock (int id)
{
    lock (locker) {
        return Table<Stock>().FirstOrDefault(x => x.Id == id);
    }
}
```

Android representa los datos como un `ListView`.

## <a name="create-and-update"></a>Crear y actualizar

Para simplificar el código de la aplicación, se proporciona un único método Save que realiza una inserción o una actualización dependiendo de si se ha establecido PrimaryKey. Dado que `Id` la propiedad está marcada con `[PrimaryKey]` un atributo, no debe establecerla en el código. Este método detectará si el valor se ha guardado previamente (comprobando la propiedad de clave principal) e inserta o actualiza el objeto en consecuencia:

```csharp
public int SaveStock (Stock item)
{
    lock (locker) {
        if (item.Id != 0) {
            Update (item);
            return item.Id;
    } else {
            return Insert (item);
        }
    }
}
```

Normalmente, las aplicaciones reales requerirán alguna validación (por ejemplo, campos obligatorios, longitud mínima u otras reglas de negocios). Una buena aplicación multiplataforma implementa tanto como sea posible la validación lógica en el código compartido, pasando los errores de validación a la interfaz de usuario para su presentación de acuerdo con las capacidades de la plataforma.

## <a name="delete"></a>Eliminar

A diferencia de `Insert` los `Update` métodos y, `Delete<T>` el método solo puede aceptar el valor de clave principal en lugar `Stock` de un objeto completo. En este ejemplo, `Stock` se pasa un objeto al método, pero solo la propiedad ID se pasa `Delete<T>` al método.

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>Usar un archivo de base de datos SQLite rellenado previamente

Algunas aplicaciones se incluyen con una base de datos que ya se ha rellenado con datos. Puede hacerlo fácilmente en la aplicación móvil si envía un archivo de base de datos de SQLite existente a la aplicación y lo copia en un directorio de escritura antes de tener acceso a él. Dado que SQLite es un formato de archivo estándar que se usa en muchas plataformas, hay una serie de herramientas disponibles para crear un archivo de base de datos de SQLite:

- **Extensión de administrador de SQLite de SQLite** &ndash; Funciona en Mac y Windows y genera archivos que son compatibles con iOS y Android.

- **Línea de comandos** Vea [www.SQLite.org/SQLite.html](http://www.sqlite.org/sqlite.html) . &ndash;

Al crear un archivo de base de datos para su distribución con la aplicación, tenga cuidado con el nombre de las tablas y columnas para asegurarse de que coinciden con lo que espera el código, especialmente si usa SQLite.NET, lo C# que esperará que los nombres coincidan con sus clases y propiedades ( o los atributos personalizados asociados).

Para asegurarse de que algún código se ejecuta antes que cualquier otro elemento de la aplicación Android, puede colocarlo en la primera actividad que se va a `Application` cargar o puede crear una subclase que se cargue antes de cualquier actividad. En el código siguiente se `Application` muestra una subclase que copia un archivo de base de **datos existente. SQLite** fuera del directorio **/Resources/RAW/** .

```csharp
[Application]
public class YourAndroidApp : Application {
    public override void OnCreate ()
    {
        base.OnCreate ();
        var docFolder = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
        Console.WriteLine ("Data path:" + Database.DatabaseFilePath);
        var dbFile = Path.Combine(docFolder, "data.sqlite"); // FILE NAME TO USE WHEN COPIED
        if (!System.IO.File.Exists(dbFile)) {
            var s = Resources.OpenRawResource(Resource.Raw.data);  // DATA FILE RESOURCE ID
            FileStream writeStream = new FileStream(dbFile, FileMode.OpenOrCreate, FileAccess.Write);
            ReadWriteStream(s, writeStream);
        }
    }
    // readStream is the stream you need to read
    // writeStream is the stream you want to write to
    private void ReadWriteStream(Stream readStream, Stream writeStream)
    {
        int Length = 256;
        Byte[] buffer = new Byte[Length];
        int bytesRead = readStream.Read(buffer, 0, Length);
        // write the required bytes
        while (bytesRead > 0)
        {
            writeStream.Write(buffer, 0, bytesRead);
            bytesRead = readStream.Read(buffer, 0, Length);
        }
        readStream.Close();
        writeStream.Close();
    }
}
```

## <a name="related-links"></a>Vínculos relacionados

- [Acceso a datos básico (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [Acceso a la configuración avanzada (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Recetas de datos de Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Acceso a datos de Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
