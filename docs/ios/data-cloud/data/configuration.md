---
title: Configuración de SQLite en Xamarin. iOS
description: En este documento se describe cómo determinar la ubicación de un archivo de base de datos SQLite en una aplicación Xamarin. iOS. Estos conceptos son relevantes independientemente del mecanismo de acceso a datos seleccionado.
ms.prod: xamarin
ms.assetid: E5582F4B-AD74-420F-9E6D-B07CFB420B3A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/11/2016
ms.openlocfilehash: 9140b98511aae5e24ee8aaf069668a793221c1a7
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73009056"
---
# <a name="configuring-sqlite-in-xamarinios"></a>Configuración de SQLite en Xamarin. iOS

Para usar SQLite en la aplicación de Xamarin. iOS, deberá determinar la ubicación de archivo correcta para el archivo de base de datos.

## <a name="database-file-path"></a>Ruta del archivo de base de datos

Independientemente del método de acceso a datos que use, debe crear un archivo de base de datos antes de que los datos se puedan almacenar con SQLite. En función de la plataforma de destino, la ubicación del archivo será diferente. En el caso de iOS, puede usar la clase de entorno para construir una ruta de acceso válida, tal como se muestra en el siguiente fragmento de código:

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "database.db3");
// dbPath contains a valid file path for the database file to be stored
```

Hay otras cosas que se deben tener en cuenta a la hora de decidir dónde almacenar el archivo de base de datos. En iOS, puede que desee que se realice una copia de seguridad automática de la base de datos (o no).

Si desea usar una ubicación diferente en cada plataforma de la aplicación multiplataforma, puede usar una directiva de compilador como se muestra para generar una ruta de acceso diferente para cada plataforma:

```csharp
var sqliteFilename = "MyDatabase.db3";
#if __ANDROID__
// Just use whatever directory SpecialFolder.Personal returns
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
// we need to put in /Library/ on iOS5.1+ to meet Apple's iCloud terms
// (they don't want non-user-generated data in Documents)
string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine (documentsPath, "..", "Library"); // Library folder instead
#endif
var path = Path.Combine (libraryPath, sqliteFilename);
```

Consulte el artículo sobre cómo [trabajar con el sistema de archivos](~/ios/app-fundamentals/file-system.md) para más información sobre las ubicaciones de archivos que se van a usar en iOS. Vea el documento compilar [aplicaciones multiplataforma](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) para obtener más información sobre el uso de directivas de compilador para escribir código específico para cada plataforma.

## <a name="threading"></a>Subprocesos

No debe usar la misma conexión de base de datos de SQLite entre varios subprocesos. Tenga cuidado de abrir, usar y cerrar las conexiones que cree en el mismo subproceso.

Para asegurarse de que el código no intenta tener acceso a la base de datos de SQLite desde varios subprocesos al mismo tiempo, tome manualmente un bloqueo cada vez que vaya a tener acceso a la base de datos, de la siguiente manera:

```csharp
object locker = new object(); // class level private field
// rest of class code
lock (locker){
    // Do your query or insert here
}
```

Todo el acceso a la base de datos (lecturas, escrituras, actualizaciones, etc.) debe ajustarse con el mismo bloqueo. Se debe tener cuidado para evitar una situación de interbloqueo asegurándose de que el trabajo dentro de la cláusula Lock se mantiene sencillo y no llama a otros métodos que también pueden tomar un bloqueo.

## <a name="related-links"></a>Vínculos relacionados

- [Acceso a datos básico (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [Acceso a la configuración avanzada (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Recetas de datos de iOS](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Acceso a datos de Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
