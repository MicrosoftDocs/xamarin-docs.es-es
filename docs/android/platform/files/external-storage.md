---
title: Acceso a archivos en el almacenamiento externo con Xamarin. Android
description: En esta guía se tratará el acceso a archivos en el almacenamiento externo en Xamarin. Android
ms.prod: xamarin
ms.assetid: 40da10b2-a207-4f9c-a2dd-165d9b662f33
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/23/2018
ms.openlocfilehash: e30f726cfb783fc47bc09f7590a523eb0e487105
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756526"
---
# <a name="external-storage"></a>Almacenamiento externo

El almacenamiento externo hace referencia al almacenamiento de archivos que no está en el almacenamiento interno y no es exclusivamente accesible para la aplicación responsable del archivo. El objetivo principal del almacenamiento externo es proporcionar un lugar donde colocar los archivos que se van a compartir entre las aplicaciones o que son demasiado grandes para el almacenamiento interno.

Históricamente, el almacenamiento externo hacía referencia a una partición de disco en un medio extraíble, como una tarjeta SD (también conocido como _almacenamiento portátil_). Esta distinción ya no es tan relevante como los dispositivos Android han evolucionado y muchos dispositivos Android ya no admiten el almacenamiento extraíble. En su lugar, algunos dispositivos asignarán parte de su memoria no volátil interna que Android debe realizar en el mismo medio extraíble de función. Esto se conoce como almacenamiento _emulado_ y se sigue considerando como almacenamiento externo. Como alternativa, algunos dispositivos Android pueden tener varias particiones de almacenamiento externo. Por ejemplo, una tableta Android (además de su almacenamiento interno) podría haber emulado el almacenamiento y una o más ranuras para una tarjeta SD. Todas estas particiones se tratan en Android como almacenamiento externo.

En los dispositivos que tienen varios usuarios, cada usuario tendrá un directorio dedicado en la partición de almacenamiento externo principal para su almacenamiento externo. Las aplicaciones que se ejecutan como un usuario no tendrán acceso a los archivos de otro usuario en el dispositivo. Los archivos de todos los usuarios siguen siendo de lectura mundial y se pueden escribir en todo el mundo. sin embargo, Android creará un espacio aislado de cada perfil de usuario de los demás.

Leer y escribir en archivos es casi idéntico en Xamarin. Android, como en cualquier otra aplicación de .NET. La aplicación Xamarin. Android determina la ruta de acceso al archivo que se manipulará y, a continuación, utilizará expresiones estándar de .NET para el acceso a archivos. Dado que las rutas de acceso reales al almacenamiento interno y externo pueden variar de un dispositivo a un dispositivo o de una versión de Android a una versión de Android, no se recomienda codificar de forma rígida la ruta de acceso a los archivos. En su lugar, Xamarin. Android expone las API nativas de Android que le ayudarán a determinar la ruta de acceso a los archivos en el almacenamiento interno y externo.

En esta guía se describen los conceptos y las API de Android que son específicos del almacenamiento externo.

## <a name="public-and-private-files-on-external-storage"></a>Archivos públicos y privados en el almacenamiento externo

Hay dos tipos diferentes de archivos que una aplicación puede mantener en el almacenamiento externo:

* **Archivos privados** los archivos privadossonarchivosespecíficosdelaaplicación(perosiguensiendodelecturamundialydeescriturainternacional).&ndash; Android espera que los archivos privados se almacenen en un directorio específico del almacenamiento externo. Aunque los archivos se denominan "privados", siguen siendo visibles y accesibles para otras aplicaciones en el dispositivo, no se les ofrece ninguna protección especial por parte de Android.

* Archivos&ndash; públicos: son archivos que no se consideran específicos de la aplicación y están diseñados para ser compartidos libremente.

Las diferencias entre estos archivos son principalmente conceptuales. Los archivos privados son privados en el sentido de que se consideran una parte de la aplicación, mientras que los archivos públicos son otros archivos que existen en el almacenamiento externo. Android proporciona dos API diferentes para resolver las rutas de acceso a archivos públicos y privados, pero, de lo contrario, se usan las mismas API de .NET para leer y escribir en estos archivos. Se trata de las mismas API que se describen en la sección sobre [lectura y escritura](~/android/platform/files/index.md#reading-or-writing-to-files-on-internal-storage).

### <a name="private-external-files"></a>Archivos externos privados

Los archivos externos privados se consideran específicos de una aplicación (similar a los archivos internos), pero se mantienen en el almacenamiento externo por una serie de motivos (por ejemplo, demasiado grandes para el almacenamiento interno). Al igual que los archivos internos, estos archivos se eliminarán cuando el usuario desinstale la aplicación.

La ubicación principal para los archivos externos privados se encuentra llamando al método `Android.Content.Context.GetExternalFilesDir(string type)`. Este método devolverá `Java.IO.File` un objeto que representa el directorio de almacenamiento externo privado para la aplicación. Al `null` pasar a este método, se devolverá la ruta de acceso al directorio de almacenamiento del usuario para la aplicación. Por ejemplo, para una aplicación con el nombre `com.companyname.app`del paquete, el directorio "raíz" de los archivos externos privados sería:

```bash
/storage/emulated/0/Android/data/com.companyname.app/files/
```

Este documento hará referencia al directorio de almacenamiento para los archivos privados en el almacenamiento _externo\_como\_almacenamiento externo privado_.

El parámetro de `GetExternalFilesDir()` es una cadena que especifica un _directorio_de la aplicación. Se trata de un directorio diseñado para proporcionar una ubicación estándar para una organización lógica de archivos. Los valores de cadena están disponibles a través de constantes `Android.OS.Environment` en la clase:

| `Android.OS.Environment` | Directorio |
|-|-|
| DirectoryAlarms | **_PRIVATE\_EXTERNAL\_STORAGE_/Alarms** |
| DirectoryDcim | **_PRIVATE\_EXTERNAL\_STORAGE_/DCIM** |
| DirectoryDownloads | **_PRIVATE\_EXTERNAL\_STORAGE_/Download** |
| DirectoryDocuments | **_Almacenamiento\_externo\_privado_/Documents** |
| DirectoryMovies | **_PRIVATE\_EXTERNAL\_STORAGE_/Movies** |
| DirectoryMusic | **_PRIVATE\_EXTERNAL\_STORAGE_/Music** |
| DirectoryNotifications | **_Almacenamiento\_externo\_privado_/notifications** |
| DirectoryPodcasts | **_PRIVATE\_EXTERNAL\_STORAGE_/Podcasts** |
| DirectoryRingtones | **_PRIVATE\_EXTERNAL\_STORAGE_/Ringtones** |
| DirectoryPictures | **_PRIVATE\_EXTERNAL\_STORAGE_/Pictures** |

En el caso de los dispositivos que tienen varias particiones de almacenamiento externo, cada partición tendrá un directorio destinado a los archivos privados. El método `Android.Content.Context.GetExternalFilesDirs(string type)` devolverá una matriz `Java.IO.Files`de. Cada objeto representa un directorio privado específico de la aplicación en todos los dispositivos de almacenamiento compartido/externo donde la aplicación puede colocar los archivos que posee.

> [!IMPORTANT]
> La ruta de acceso exacta al directorio de almacenamiento privado privado puede variar de un dispositivo a otro y entre versiones de Android. Por este motivo, las aplicaciones no deben codificar de forma rígida la ruta de acceso a este directorio y, en su lugar, usar `Android.Content.Context.GetExternalFilesDir()`las API de Xamarin. Android, como.

### <a name="public-external-files"></a>Archivos externos públicos

Los archivos públicos son archivos que existen en el almacenamiento externo y que no están almacenados en el directorio que Android asigna para los archivos privados. Los archivos públicos no se eliminarán cuando se desinstale la aplicación. Se debe conceder permiso a las aplicaciones Android para poder leer o escribir archivos públicos. Es posible que los archivos públicos existan en cualquier lugar del almacenamiento externo, pero por Convención Android espera que existan archivos públicos en el directorio identificado por `Android.OS.Environment.ExternalStorageDirectory`la propiedad. Esta propiedad devolverá `Java.IO.File` un objeto que representa el directorio de almacenamiento externo principal. Por ejemplo, `Android.OS.Environment.ExternalStorageDirectory` puede hacer referencia al siguiente directorio:

```bash
/storage/emulated/0/
```

Este documento hará referencia al directorio de almacenamiento de archivos públicos en el almacenamiento externo _como\_almacenamiento\_externo público_.

Android también admite el concepto de directorios de aplicación _en\_el\_almacenamiento externo público_. Estos directorios son exactamente iguales que los directorios de la aplicación `_PRIVATE\_EXTERNAL\_STORAGE_` para y se describen en la tabla de la sección anterior. El método `Android.OS.Environment.GetExternalStoragePublicDirectory(string directoryType)` devolverá `Java.IO.File` un objeto que corresponde a un directorio de aplicación público. El `directoryType` parámetro es un parámetro obligatorio y no puede `null`ser.

Por ejemplo, al `Environment.GetExternalStoragePublicDirectory(Environment.DirectoryDocuments).AbsolutePath` llamar a, se devolverá una cadena que será similar a:

```bash
/storage/emulated/0/Documents
```

> [!IMPORTANT]
> La ruta de acceso exacta al directorio de almacenamiento externo público puede variar entre el dispositivo y el dispositivo y entre las versiones de Android. Por este motivo, las aplicaciones no deben codificar de forma rígida la ruta de acceso a este directorio y, en su lugar, usar `Android.OS.Environment.ExternalStorageDirectory`las API de Xamarin. Android, como.

## <a name="working-with-external-storage"></a>Trabajar con almacenamiento externo

Una vez que una aplicación de Xamarin. Android ha obtenido la ruta de acceso completa a un archivo, debe utilizar cualquiera de las API estándar de .NET para crear, leer, escribir o eliminar archivos. Esto maximiza la cantidad de código compatible entre plataformas de una aplicación. Sin embargo, antes de intentar acceder a un archivo, una aplicación de Xamarin. Android debe asegurarse de que es posible acceder a ese archivo.

1. **Comprobar el almacenamiento externo** &ndash; En función de la naturaleza del almacenamiento externo, es posible que la aplicación no pueda montarla y utilizarla. Todas las aplicaciones deben comprobar el estado del almacenamiento externo antes de intentar usarlo.
2. **Realizar una comprobación de permisos en tiempo de ejecución** &ndash; Una aplicación Android debe solicitar permiso del usuario para tener acceso al almacenamiento externo. Esto significa que se debe realizar una solicitud de permiso en tiempo de ejecución antes de cualquier acceso a archivos. Los [permisos de la guía de Xamarin. Android](~/android/app-fundamentals/permissions.md) contienen más detalles sobre los permisos de Android.

A continuación se explica cada una de estas dos tareas.

### <a name="verifying-that-external-storage-is-available"></a>Comprobando que el almacenamiento externo está disponible

El primer paso antes de escribir en el almacenamiento externo es comprobar que es legible o grabable. La `Android.OS.Environment.ExternalStorageState` propiedad contiene una cadena que identifica el estado del almacenamiento externo. Esta propiedad devolverá una cadena que representa el estado. Esta tabla es una lista de los `ExternalStorageState` valores que puede `Environment.ExternalStorageState`devolver:

| ExternalStorageState | DESCRIPCIÓN  |
|----------------------|---|
| MediaBadRemoval      | El medio se quitó repentinamente sin que se desmonte correctamente. |
| MediaChecking        | El medio está presente pero se está llevando a cabo una comprobación del disco.  |
| MediaEjecting        | Los medios se están desmontando y expulsando.  |
| MediaMounted         | Los medios se montan y se pueden leer o escribir en ellos.  |
| MediaMountedReadOnly | Los medios se montan, pero solo se pueden leer de. |
| MediaNofs            | El medio está presente pero no contiene un sistema de archivos adecuado para Android. |
| MediaRemoved         | No hay ningún medio presente. |
| MediaShared          | Los medios están presentes, pero no están montados. Se comparte a través de USB con otro dispositivo.|
| MediaUnknown         | Android no reconoce el estado de los medios. |
| MediaUnmountable     | El medio está presente pero Android no lo puede montar. |
| MediaUnmounted       | Los medios están presentes pero no están montados. |

La mayoría de las aplicaciones de Android solo tendrán que comprobar si se ha montado el almacenamiento externo. En el fragmento de código siguiente se muestra cómo comprobar que el almacenamiento externo está montado para acceso de solo lectura o acceso de lectura y escritura:

```csharp
bool isReadonly = Environment.MediaMountedReadOnly.Equals(Environment.ExternalStorageState);
bool isWriteable = Environment.MediaMounted.Equals(Environment.ExternalStorageState);
```

## <a name="external-storage-permissions"></a>Permisos de almacenamiento externo

Android considera que el acceso al almacenamiento externo es un _permiso peligroso_, lo que normalmente requiere que el usuario conceda su permiso para acceder al recurso. El usuario puede revocar este permiso en cualquier momento.  Esto significa que se debe realizar una solicitud de permiso en tiempo de ejecución antes de cualquier acceso a archivos. A las aplicaciones se les conceden automáticamente permisos para leer y escribir sus propios archivos privados. Es posible que las aplicaciones lean y escriban los archivos privados que pertenecen a otras aplicaciones después de que el usuario [les conceda permisos](~/android/app-fundamentals/permissions.md) .

Todas las aplicaciones de Android deben declarar uno de los dos permisos para el almacenamiento externo en **archivo AndroidManifest. XML** . Para identificar los permisos, se debe agregar uno de `uses-permission` los dos elementos siguientes a **archivo AndroidManifest. XML**:

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

> [!NOTE]
> Si el usuario concede `WRITE_EXTERNAL_STORAGE` `READ_EXTERNAL_STORAGE` , también se concede implícitamente. No es necesario solicitar ambos permisos en **archivo AndroidManifest. XML**.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Los permisos también se pueden agregar mediante la pestaña **manifiesto de Android** de las propiedades de la **solución**:

![Explorador de soluciones: permisos necesarios para Visual Studio](./images/required-permissions.w157.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Los permisos también se pueden agregar mediante la pestaña **manifiesto de Android** del **Panel de propiedades**de la solución:

[![Panel de solución: permisos necesarios para Visual Studio para Mac](./images/required-permissions.m752-sml.png)](./images/required-permissions.m752.png#lightbox)

-----

En general, todos los permisos peligrosos deben ser aprobados por el usuario. Los permisos para el almacenamiento externo son una anomalía en que hay excepciones a esta regla, en función de la versión de Android que se esté ejecutando en la aplicación:

![Diagrama de flujo de comprobaciones de permisos de almacenamiento externo](./images/external-permission-check-flowchart.png)

Para obtener más información sobre cómo realizar solicitudes de permiso en tiempo de ejecución, consulte los [permisos de la guía en Xamarin. Android](~/android/app-fundamentals/permissions.md). **En monodroid-Sample** [LocalFiles](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles) también se muestra una forma de realizar comprobaciones de permisos en tiempo de ejecución.

#### <a name="granting-and-revoking-permissions-with-adb"></a>Conceder y revocar permisos con ADB

En el transcurso del desarrollo de una aplicación Android, puede que sea necesario conceder y revocar los permisos para probar los distintos flujos de trabajo implicados con las comprobaciones de permisos en tiempo de ejecución. Es posible hacerlo en el símbolo del sistema mediante ADB. Los siguientes fragmentos de código de línea de comandos demuestran cómo conceder o revocar permisos mediante ADB para una aplicación Android cuyo nombre de paquete es **com. CompanyName. app**:

```bash
$ adb shell pm grant com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE

$ adb shell pm revoke com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE
```

## <a name="deleting-files"></a>Eliminar archivos

Cualquiera de las API C# estándar se puede usar para eliminar un archivo de almacenamiento externo, [`System.IO.File.Delete`](xref:System.IO.File.Delete*)como. También es posible usar las API de Java a costa de portabilidad del código. Por ejemplo:

```csharp
System.IO.File.Delete("/storage/emulated/0/Android/data/com.companyname.app/files/count.txt");
```

## <a name="related-links"></a>Vínculos relacionados

* [Ejemplo de archivos locales de Xamarin. Android en **monodroid-samples**](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles)
* [Permisos en Xamarin. Android](~/android/app-fundamentals/permissions.md)
