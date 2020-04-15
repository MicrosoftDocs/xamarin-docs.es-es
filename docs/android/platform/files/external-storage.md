---
title: Acceso a archivos en el almacenamiento externo con Xamarin.Android
description: En esta guía se tratará el acceso a archivos en el almacenamiento externo en Xamarin.Android.
ms.prod: xamarin
ms.assetid: 40da10b2-a207-4f9c-a2dd-165d9b662f33
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/23/2018
ms.openlocfilehash: 96b0d6a00c7825939b1f89ed63e3e5559ca4ef59
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020481"
---
# <a name="external-storage"></a>Almacenamiento externo

El almacenamiento externo se refiere al almacenamiento de archivos que no se encuentra en el almacenamiento interno y al que tampoco tiene acceso exclusivo la aplicación responsable del archivo. El objetivo principal del almacenamiento externo es proporcionar un lugar donde almacenar los archivos que se pueden compartir entre las aplicaciones o que son demasiado grandes para el almacenamiento interno.

Históricamente, el almacenamiento externo hacía referencia a una partición de disco en medios extraíbles, como una tarjeta SD (también conocido como _almacenamiento portátil_). Esta distinción ya no es tan relevante, ya que los dispositivos Android han evolucionado y muchos de ellos ya no admiten el almacenamiento extraíble. En su lugar, algunos dispositivos asignarán parte de su memoria no volátil interna con Android, para que desempeñe la misma función que los medios extraíbles. Esto se conoce como almacenamiento _emulado_ y se sigue considerando como almacenamiento externo. Como alternativa, algunos dispositivos Android pueden tener varias particiones de almacenamiento externo. Por ejemplo, una tableta Android (además de su almacenamiento interno) podría tener almacenamiento emulado y una o varias ranuras para una tarjeta SD. Todas estas particiones se tratan en Android como almacenamiento externo.

En los dispositivos que tienen varios usuarios, cada usuario tendrá un directorio dedicado en la partición de almacenamiento externo principal para su almacenamiento externo. Las aplicaciones que se ejecutan como un usuario no tendrán acceso a los archivos de otro usuario en el dispositivo. Los archivos de todos los usuarios se podrán leer y escribir en todo el mundo. Sin embargo, Android creará un espacio aislado de cada perfil de usuario para separarlo de los demás.

Leer archivos y escribir en ellos se hace casi de la misma manera en Xamarin.Android que en cualquier otra aplicación de .NET. La aplicación Xamarin.Android determina la ruta de acceso al archivo que se manipulará y, a continuación, usa expresiones estándar de .NET para acceder a él. Dado que las rutas de acceso reales al almacenamiento interno y externo pueden variar de un dispositivo a otro, o de una versión de Android a otra, no se recomienda codificar de forma rígida la ruta de acceso a los archivos. En su lugar, Xamarin.Android expone las API nativas de Android que le ayudarán a determinar la ruta de acceso a los archivos en el almacenamiento interno y externo.

En esta guía se describen los conceptos y las API de Android que son específicos del almacenamiento externo.

## <a name="public-and-private-files-on-external-storage"></a>Archivos públicos y privados en el almacenamiento externo

Hay dos tipos diferentes de archivos que una aplicación puede mantener en el almacenamiento externo:

* Archivos **privados**: son archivos específicos de la aplicación (pero se podrán leer y escribir en todo el mundo). Android espera que los archivos privados se almacenen en un directorio específico del almacenamiento externo. Aunque los archivos se denominan "privados", siguen siendo visibles y accesibles para otras aplicaciones del dispositivo; Android no les aplica ninguna protección especial.

* Archivos **públicos**: son archivos que no se consideran específicos de la aplicación y están diseñados para ser compartidos libremente.

Las diferencias entre estos archivos son principalmente conceptuales. Los archivos privados son privados en el sentido de que se consideran una parte de la aplicación, mientras que los archivos públicos son todos los demás archivos existentes en el almacenamiento externo. Android proporciona dos API diferentes para resolver las rutas de acceso a archivos públicos y privados, pero, en cambio, se usan las mismas API de .NET para leer y escribir en estos archivos. Se trata de las mismas API que se describen en la sección [Leer o escribir en archivos en el almacenamiento interno](~/android/platform/files/index.md#reading-or-writing-to-files-on-internal-storage).

### <a name="private-external-files"></a>Archivos externos privados

Los archivos externos privados se consideran específicos de una aplicación (similar a los archivos internos), pero se mantienen en el almacenamiento externo por una serie de motivos (por ejemplo, porque son demasiado grandes para el almacenamiento interno). Al igual que los archivos internos, estos archivos se eliminarán cuando el usuario desinstale la aplicación.

La ubicación principal de los archivos externos privados se encuentra con una llamada al método `Android.Content.Context.GetExternalFilesDir(string type)`. Este método devolverá un objeto `Java.IO.File` que representa el directorio de almacenamiento externo privado de la aplicación. Al pasar `null` a este método, se devolverá la ruta de acceso al directorio de almacenamiento del usuario de la aplicación. Por ejemplo, para una aplicación con el nombre de paquete `com.companyname.app`, el directorio "raíz" de los archivos externos privados sería:

```bash
/storage/emulated/0/Android/data/com.companyname.app/files/
```

Este documento hará referencia al directorio de almacenamiento para los archivos privados en el almacenamiento externo como _ALMACENAMIENTO\_EXTERNO\_PRIVADO_.

El parámetro para `GetExternalFilesDir()` es una cadena que especifica un _directorio de aplicación_. Se trata de un directorio diseñado para proporcionar una ubicación estándar para una organización lógica de archivos. Los valores de cadena están disponibles mediante constantes en la clase `Android.OS.Environment`:

| `Android.OS.Environment` | Directorio |
|-|-|
| DirectoryAlarms | **_PRIVATE\_EXTERNAL\_STORAGE_/Alarms** |
| DirectoryDcim | **_PRIVATE\_EXTERNAL\_STORAGE_/DCIM** |
| DirectoryDownloads | **_PRIVATE\_EXTERNAL\_STORAGE_/Download** |
| DirectoryDocuments | **_PRIVATE\_EXTERNAL\_STORAGE_/Documents** |
| DirectoryMovies | **_PRIVATE\_EXTERNAL\_STORAGE_/Movies** |
| DirectoryMusic | **_PRIVATE\_EXTERNAL\_STORAGE_/Music** |
| DirectoryNotifications | **_PRIVATE\_EXTERNAL\_STORAGE_/Notifications** |
| DirectoryPodcasts | **_PRIVATE\_EXTERNAL\_STORAGE_/Podcasts** |
| DirectoryRingtones | **_PRIVATE\_EXTERNAL\_STORAGE_/Ringtones** |
| DirectoryPictures | **_PRIVATE\_EXTERNAL\_STORAGE_/Pictures** |

En el caso de los dispositivos que tienen varias particiones de almacenamiento externo, cada partición tendrá un directorio destinado a los archivos privados. El método `Android.Content.Context.GetExternalFilesDirs(string type)` devolverá una matriz de `Java.IO.Files`. Cada objeto representa un directorio privado específico de la aplicación en todos los dispositivos de almacenamiento compartido/externo donde la aplicación puede colocar los archivos que posee.

> [!IMPORTANT]
> La ruta de acceso exacta al directorio de almacenamiento externo privado puede variar de un dispositivo a otro y entre las distintas versiones de Android. Por este motivo, las aplicaciones no deben codificar de forma rígida la ruta de acceso a este directorio y, en su lugar, deben usar las API de Xamarin.Android, como `Android.Content.Context.GetExternalFilesDir()`.

### <a name="public-external-files"></a>Archivos externos públicos

Los archivos públicos son archivos que existen en el almacenamiento externo y que no están almacenados en el directorio que Android asigna para los archivos privados. Los archivos públicos no se eliminarán cuando se desinstale la aplicación. Se debe conceder permiso a las aplicaciones Android para poder leer o escribir archivos públicos. Es posible que los archivos públicos existan en cualquier lugar del almacenamiento externo, pero, convencionalmente, Android espera que existan archivos públicos en el directorio identificado por la propiedad `Android.OS.Environment.ExternalStorageDirectory`. Esta propiedad devolverá un objeto `Java.IO.File` que representa el directorio de almacenamiento externo principal. Como ejemplo, `Android.OS.Environment.ExternalStorageDirectory` puede hacer referencia al siguiente directorio:

```bash
/storage/emulated/0/
```

Este documento hará referencia al directorio de almacenamiento para los archivos públicos en el almacenamiento externo como _ALMACENAMIENTO\_EXTERNO\_PÚBLICO_.

Android también admite el concepto de directorios de aplicaciones en _ALMACENAMIENTO\_EXTERNO\_PÚBLICO_. Estos directorios son exactamente iguales que los directorios de la aplicación para `PRIVATE_EXTERNAL_STORAGE` y se describen en la tabla de la sección anterior. El método `Android.OS.Environment.GetExternalStoragePublicDirectory(string directoryType)` devolverá un objeto `Java.IO.File` que se corresponde con un directorio de aplicación público. El parámetro `directoryType` es obligatorio y no puede ser `null`.

Por ejemplo, al llamar a `Environment.GetExternalStoragePublicDirectory(Environment.DirectoryDocuments).AbsolutePath`, se devolverá una cadena que será similar a la siguiente:

```bash
/storage/emulated/0/Documents
```

> [!IMPORTANT]
> La ruta de acceso exacta al directorio de almacenamiento externo público puede variar de un dispositivo a otro y entre las distintas versiones de Android. Por este motivo, las aplicaciones no deben codificar de forma rígida la ruta de acceso a este directorio y, en su lugar, deben usar las API de Xamarin.Android, como `Android.OS.Environment.ExternalStorageDirectory`.

## <a name="working-with-external-storage"></a>Cómo trabajar con almacenamiento externo

Una vez que una aplicación Xamarin.Android ha obtenido la ruta de acceso completa a un archivo, debe utilizar cualquiera de las API estándar de .NET para crear, leer, escribir o eliminar archivos. Esto maximiza la cantidad de código compatible entre plataformas para una aplicación. Sin embargo, antes de intentar acceder a un archivo, una aplicación Xamarin.Android debe asegurarse de que es posible acceder a ese archivo.

1. **Verificar el almacenamiento externo**: en función de la naturaleza del almacenamiento externo, es posible que la aplicación no pueda montarlo ni utilizarlo. Todas las aplicaciones deben comprobar el estado del almacenamiento externo antes de intentar usarlo.
2. **Realizar una comprobación de permisos en tiempo de ejecución**: una aplicación Android debe solicitar permiso al usuario para acceder al almacenamiento externo. Esto significa que se debe realizar una solicitud de permiso en tiempo de ejecución antes de acceder a cualquier archivo. La guía [Permisos en Xamarin. Android](~/android/app-fundamentals/permissions.md) contiene más detalles sobre los permisos de Android.

A continuación, se explica cada una de estas dos tareas.

### <a name="verifying-that-external-storage-is-available"></a>Verificación de la disponibilidad del almacenamiento externo

El primer paso antes de escribir en el almacenamiento externo es comprobar que es legible o grabable. La propiedad `Android.OS.Environment.ExternalStorageState` contiene una cadena que identifica el estado del almacenamiento externo. Esta propiedad devolverá una cadena que representa el estado. Esta tabla es una lista de los valores `ExternalStorageState` que `Environment.ExternalStorageState` puede devolver:

| ExternalStorageState | Descripción  |
|----------------------|---|
| MediaBadRemoval      | El soporte físico se quitó repentinamente sin desmontarlo correctamente. |
| MediaChecking        | El soporte físico está presente, pero se está llevando a cabo una comprobación del disco.  |
| MediaEjecting        | El desmontaje y la expulsión del soporte físico están en proceso.  |
| MediaMounted         | El soporte físico está montado y se puede leer o escribir en él.  |
| MediaMountedReadOnly | El soporte físico está montado, pero solo se puede leer. |
| MediaNofs            | El soporte físico está presente, pero no contiene un sistema de archivos adecuado para Android. |
| MediaRemoved         | No hay ningún soporte físico. |
| MediaShared          | Hay un soporte físico, pero no está montado. Se comparte con otro dispositivo mediante USB.|
| MediaUnknown         | Android no reconoce el estado del soporte físico. |
| MediaUnmountable     | El medio está presente, pero Android no lo puede montar. |
| MediaUnmountable       | Hay un soporte físico, pero no está montado. |

La mayoría de las aplicaciones Android solo tienen que comprobar si se ha montado el almacenamiento externo. En el fragmento de código siguiente se muestra cómo comprobar que el almacenamiento externo está montado para el acceso de solo lectura o el acceso de lectura y escritura:

```csharp
bool isReadonly = Environment.MediaMountedReadOnly.Equals(Environment.ExternalStorageState);
bool isWriteable = Environment.MediaMounted.Equals(Environment.ExternalStorageState);
```

## <a name="external-storage-permissions"></a>Permisos del almacenamiento externo

Android considera que el acceso al almacenamiento externo es un _permiso peligroso_, que normalmente requiere que el usuario conceda su permiso para acceder al recurso. El usuario puede revocar este permiso en cualquier momento.  Esto significa que se debe realizar una solicitud de permiso en tiempo de ejecución antes de acceder a cualquier archivo. A las aplicaciones se les conceden permisos automáticamente para leer y escribir sus propios archivos privados. Es posible que las aplicaciones lean y escriban los archivos privados que pertenecen a otras aplicaciones después de que el usuario les haya [concedido permiso](~/android/app-fundamentals/permissions.md).

Todas las aplicaciones Android deben declarar uno de los dos permisos para el almacenamiento externo en el archivo **AndroidManifest.xml**. Para identificar los permisos, se debe agregar uno de los dos siguientes elementos `uses-permission` al archivo **AndroidManifest.xml**:

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

> [!NOTE]
> Si el usuario concede `WRITE_EXTERNAL_STORAGE`, también se concederá `READ_EXTERNAL_STORAGE` de forma implícita. No es necesario solicitar ambos permisos en **AndroidManifest.xml**.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Los permisos también se pueden agregar con la pestaña **Manifiesto de Android** de las **propiedades de la solución**:

![Explorador de soluciones: permisos necesarios en Visual Studio](./images/required-permissions.w157.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Los permisos también se pueden agregar con la pestaña **Manifiesto de Android** del **panel de propiedades de la solución**:

[![Panel de solución: permisos necesarios en Visual Studio para Mac](./images/required-permissions.m752-sml.png)](./images/required-permissions.m752.png#lightbox)

-----

En general, el usuario debe aprobar todos los permisos peligrosos. Los permisos para el almacenamiento externo son una anomalía, en cuanto a que hay excepciones a esta regla, en función de la versión de Android que se ejecute en la aplicación:

![Diagrama de flujo de comprobaciones de permisos de almacenamiento externo](./images/external-permission-check-flowchart.png)

Para más información sobre cómo realizar solicitudes de permiso en tiempo de ejecución, consulte la guía [Permisos en Xamarin.Android](~/android/app-fundamentals/permissions.md). El atributo [LocalFiles](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles) de **monodroid-sample** también muestra una forma de realizar comprobaciones de permisos en tiempo de ejecución.

#### <a name="granting-and-revoking-permissions-with-adb"></a>Conceder y revocar permisos con ADB

En el transcurso del desarrollo de una aplicación Android, puede que sea necesario conceder y revocar los permisos para probar los distintos flujos de trabajo implicados en las comprobaciones de permisos en tiempo de ejecución. Es posible hacerlo en el símbolo del sistema mediante ADB. Los siguientes fragmentos de código de línea de comandos demuestran cómo conceder o revocar permisos mediante ADB para una aplicación Android cuyo nombre de paquete es **com.companyname.app**:

```bash
$ adb shell pm grant com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE

$ adb shell pm revoke com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE
```

## <a name="deleting-files"></a>Eliminar archivos

Cualquiera de las API de C# estándar se pueden usar para eliminar un archivo del almacenamiento externo, como [`System.IO.File.Delete`](xref:System.IO.File.Delete*). También es posible usar las API de Java a costa de la portabilidad del código. Por ejemplo:

```csharp
System.IO.File.Delete("/storage/emulated/0/Android/data/com.companyname.app/files/count.txt");
```

## <a name="related-links"></a>Vínculos relacionados

* [Ejemplo de archivos locales de Xamarin.Android en **monodroid-samples**](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles)
* [Permisos en Xamarin.Android](~/android/app-fundamentals/permissions.md)
