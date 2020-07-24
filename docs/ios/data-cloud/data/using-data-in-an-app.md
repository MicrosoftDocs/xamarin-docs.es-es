---
title: Uso de datos en una aplicación iOS
description: En este documento se describe el ejemplo de DataAccess_Adv, que muestra cómo recopilar datos proporcionados por el usuario y realizar operaciones de creación, lectura, actualización y eliminación (CRUD) en una aplicación de Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 2CB8150E-CD2C-4E97-8605-1EE8CBACFEEC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/11/2016
ms.openlocfilehash: c888c132748c4212b1e52413647614ca83897d75
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938520"
---
# <a name="using-data-in-an-ios-app"></a>Uso de datos en una aplicación iOS

En el ejemplo **DataAccess_Adv** se muestra una aplicación de trabajo que permite la entrada del usuario y la funcionalidad de la base de datos *CRUD* (crear, leer, actualizar y eliminar). La aplicación consta de dos pantallas: una lista y un formulario de entrada de datos. Todo el código de acceso a datos se puede volver a usar en iOS y Android sin modificarlo.

Después de agregar algunos datos, las pantallas de la aplicación tienen el siguiente aspecto en iOS:

 ![lista de ejemplos de iOS](using-data-in-an-app-images/image9.png)

 ![detalle de ejemplo de iOS](using-data-in-an-app-images/image10.png)

El proyecto de iOS se muestra a continuación: el código que se muestra en esta sección se encuentra en el directorio **ORM** :

 ![árbol del proyecto de iOS](using-data-in-an-app-images/image13.png)

El código de la interfaz de usuario nativa para ViewControllers en iOS está fuera del ámbito de este documento.
Consulte la guía [iOS Working with tables and Cells (trabajar con tablas y celdas](~/ios/user-interface/controls/tables/index.md) ) para obtener más información sobre los controles de interfaz de usuario.

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

iOS representa los datos de manera diferente como `UITableView` .

## <a name="create-and-update"></a>Crear y actualizar

Para simplificar el código de la aplicación, se proporciona un único método Save que realiza una inserción o una actualización dependiendo de si se ha establecido PrimaryKey. Dado `Id` que la propiedad está marcada con un `[PrimaryKey]` atributo, no debe establecerla en el código.
Este método detectará si el valor se ha guardado previamente (comprobando la propiedad de clave principal) e inserta o actualiza el objeto en consecuencia:

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

Normalmente, las aplicaciones reales requerirán alguna validación (por ejemplo, campos obligatorios, longitud mínima u otras reglas de negocios).
Una buena aplicación multiplataforma implementa tanto como sea posible la validación lógica en el código compartido, pasando los errores de validación a la interfaz de usuario para su presentación de acuerdo con las capacidades de la plataforma.

## <a name="delete"></a>Eliminar

A diferencia de `Insert` los `Update` métodos y, el `Delete<T>` método solo puede aceptar el valor de clave principal en lugar de un `Stock` objeto completo.
En este ejemplo, `Stock` se pasa un objeto al método, pero solo la propiedad ID se pasa al `Delete<T>` método.

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>Usar un archivo de base de datos SQLite rellenado previamente

Algunas aplicaciones se incluyen con una base de datos que ya se ha rellenado con datos.
Puede hacerlo fácilmente en la aplicación móvil si envía un archivo de base de datos de SQLite existente a la aplicación y lo copia en un directorio de escritura antes de tener acceso a él. Dado que SQLite es un formato de archivo estándar que se usa en muchas plataformas, hay una serie de herramientas disponibles para crear un archivo de base de datos de SQLite:

- **Extensión** de la administración de SQLite de SQLite: funciona en Mac y Windows y genera archivos que son compatibles con iOS y Android.
- **Línea de comandos** : consulte [www.SQLite.org/sqlite.html](https://www.sqlite.org/sqlite.html) .

Al crear un archivo de base de datos para su distribución con la aplicación, tenga cuidado con la nomenclatura de tablas y columnas para asegurarse de que coinciden con lo que espera el código, especialmente si usa SQLite.NET, lo que esperará que los nombres coincidan con las clases y las propiedades de C# (o los atributos personalizados asociados).

En el caso de iOS, incluya el archivo SQLite en la aplicación y asegúrese de que está marcado con la **acción de compilación: contenido**. Coloque el código en `FinishedLaunching` para copiar el archivo en un directorio de escritura *antes* de llamar a los métodos de datos. En el código siguiente se copiará una base de datos existente denominada **Data. SQLite**, solo si aún no existe.

```csharp
// Copy the database across (if it doesn't exist)
var appdir = NSBundle.MainBundle.ResourcePath;
var seedFile = Path.Combine (appdir, "data.sqlite");
if (!File.Exists (Database.DatabaseFilePath))
{
  File.Copy (seedFile, Database.DatabaseFilePath);
}
```

Cualquier código de acceso a datos (ya sea ADO.NET o Using SQLite.NET) que se ejecute después de que se haya completado, tendrá acceso a los datos rellenados previamente.

## <a name="related-links"></a>Vínculos relacionados

- [Acceso a datos básico (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [Acceso a la configuración avanzada (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Recetas de datos de iOS](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Acceso a datos de Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
