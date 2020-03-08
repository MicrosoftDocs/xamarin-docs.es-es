---
title: Acceso a archivos con Xamarin. Android
description: En esta guía se explicará el acceso a archivos en Xamarin. Android
ms.prod: xamarin
ms.assetid: FC1CFC58-B799-4DD6-8ED1-DE36B0E56856
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/23/2018
ms.openlocfilehash: 1bb0fae73a1e3647cdc0e3266c7b44ac04fcc1ee
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/07/2020
ms.locfileid: "78914227"
---
# <a name="file-storage-and-access-with-xamarinandroid"></a>File Storage y acceso con Xamarin. Android

Un requisito común para las aplicaciones de Android es manipular archivos &ndash; guardar imágenes, descargar documentos o exportar datos para compartirlos con otros programas. Android (que se basa en Linux) es compatible con esto proporcionando espacio para el almacenamiento de archivos. Android agrupa el sistema de archivos en dos tipos diferentes de almacenamiento:

* **Almacenamiento interno** &ndash; se trata de una parte del sistema de archivos a la que solo puede tener acceso la aplicación o el sistema operativo.
* **Almacenamiento externo** &ndash; se trata de una partición para el almacenamiento de archivos a los que pueden acceder todas las aplicaciones, el usuario y, posiblemente, otros dispositivos. En algunos dispositivos, el almacenamiento externo puede ser extraíble (como una tarjeta SD).

Estas agrupaciones solo son conceptuales y no hacen referencia necesariamente a una única partición o directorio en el dispositivo. Un dispositivo Android siempre proporcionará una partición para el almacenamiento interno y el almacenamiento externo. Es posible que determinados dispositivos tengan varias particiones que se consideren como almacenamiento externo. Independientemente de la partición, las API para leer, escribir o crear archivos son las mismas. Hay dos conjuntos de API que puede usar una aplicación de Xamarin. Android para el acceso a archivos:

1. **Las API de .net (proporcionadas por mono y que se incluyen en Xamarin. Android)** &ndash; incluyen las [aplicaciones auxiliares del sistema de archivos](~/essentials/file-system-helpers.md?context=xamarin/android) proporcionadas por [Xamarin. Essentials](~/essentials/index.md?context=xamarin/android). Las API de .NET proporcionan la mejor compatibilidad entre plataformas y, por lo tanto, esta guía se centrará en estas API.
1. **Las API nativas de acceso a archivos de Java (proporcionadas por Java y incluidas en Xamarin. Android)** &ndash; Java proporcionan sus propias API para leer y escribir archivos. Se trata de una alternativa totalmente aceptable para las API de .NET, pero son específicas de Android y no son adecuadas para las aplicaciones que están pensadas para ser multiplataforma.

Leer y escribir en archivos es casi idéntico en Xamarin. Android, como en cualquier otra aplicación de .NET. La aplicación Xamarin. Android determina la ruta de acceso al archivo que se manipulará y, a continuación, utilizará expresiones estándar de .NET para el acceso a archivos. Dado que las rutas de acceso reales al almacenamiento interno y externo pueden variar de un dispositivo a un dispositivo o de una versión de Android a una versión de Android, no se recomienda codificar de forma rígida la ruta de acceso a los archivos. En su lugar, use las API de Xamarin. Android para determinar la ruta de acceso a los archivos. De este modo, las API de .NET para leer y escribir archivos exponen las API nativas de Android que le ayudarán a determinar la ruta de acceso a los archivos en el almacenamiento interno y externo.

Antes de analizar las API relacionadas con el acceso a archivos, es importante comprender algunos de los detalles relacionados con el almacenamiento interno y externo. Esto se explicará en la sección siguiente.

## <a name="internal-vs-external-storage"></a>Almacenamiento interno y externo

Conceptualmente, el almacenamiento interno y el almacenamiento externo son muy similares &ndash; son lugares en los que una aplicación de Xamarin. Android puede guardar archivos. Esta similitud puede ser confusa para los desarrolladores que no están familiarizados con Android, ya que no está claro cuando una aplicación debe usar el almacenamiento interno y el almacenamiento externo.

El almacenamiento interno hace referencia a la memoria no volátil que Android asigna al sistema operativo, APK y para las aplicaciones individuales. Este espacio no es accesible excepto por el sistema operativo o las aplicaciones. Android asignará un directorio en la partición de almacenamiento interno para cada aplicación. Cuando se desinstala la aplicación, también se eliminarán todos los archivos que se mantengan en el almacenamiento interno de ese directorio. El almacenamiento interno es más adecuado para los archivos a los que solo se puede tener acceso desde la aplicación y que no se compartirán con otras aplicaciones o que tendrán muy poco valor una vez que se desinstale la aplicación. En Android 6,0 o superior, Google puede realizar automáticamente una copia de seguridad de los archivos del almacenamiento interno mediante la [característica de copia de seguridad automática de android 6,0](https://developer.android.com/guide/topics/data/autobackup). El almacenamiento interno tiene las siguientes desventajas:

* Los archivos no se pueden compartir.
* Los archivos se eliminarán cuando se desinstale la aplicación.
* Es posible que el espacio disponible en el almacenamiento interno sea limitado.

El almacenamiento externo hace referencia al almacenamiento de archivos que no es almacenamiento interno y no es exclusivamente accesible para una aplicación. El objetivo principal del almacenamiento externo es proporcionar un lugar donde colocar los archivos que se van a compartir entre las aplicaciones o que son demasiado grandes para el almacenamiento interno. La ventaja del almacenamiento externo es que normalmente tiene mucho más espacio para los archivos que el almacenamiento interno. Sin embargo, el almacenamiento externo no siempre está garantizado que esté presente en un dispositivo y puede requerir permiso especial del usuario para acceder a él.

> [!NOTE]
> En el caso de los dispositivos que admiten varios usuarios, Android proporcionará a cada usuario su propio directorio en el almacenamiento interno y externo. No se puede acceder a este directorio a otros usuarios del dispositivo. Esta separación no es visible para las aplicaciones siempre que no codifiquen rutas de acceso a archivos en el almacenamiento interno o externo.

Como regla general, las aplicaciones de Xamarin. Android deben preferir guardar los archivos en el almacenamiento interno cuando sea razonable y confiar en el almacenamiento externo cuando los archivos deben compartirse con otras aplicaciones, son muy grandes o deben conservarse incluso si se desinstala la aplicación. Por ejemplo, un archivo de configuración es más adecuado para un almacenamiento interno, ya que no tiene importancia, excepto en el caso de la aplicación que lo crea.  Por el contrario, las fotos son un buen candidato para el almacenamiento externo. Pueden ser muy grandes y, en muchos casos, es posible que el usuario desee compartirlos o acceder a ellos, incluso si se desinstala la aplicación.

Esta guía se centrará en el almacenamiento interno. Consulte la guía de [almacenamiento externo](~/android/platform/files/external-storage.md) para más información sobre el uso de almacenamiento externo en una aplicación de Xamarin. Android.

## <a name="working-with-internal-storage"></a>Trabajar con almacenamiento interno

El directorio de almacenamiento interno para una aplicación viene determinado por el sistema operativo y se expone a las aplicaciones de Android mediante la propiedad `Android.Content.Context.FilesDir`. Esto devolverá un objeto `Java.IO.File` que representa el directorio que Android ha dedicado exclusivamente a la aplicación.  Por ejemplo, una aplicación con el nombre de paquete **com. CompanyName** el directorio de almacenamiento interno podría ser:

```bash
/data/user/0/com.companyname/files
```

Este documento hará referencia al directorio de almacenamiento interno como _almacenamiento interno\__ .

> [!IMPORTANT]
> La ruta de acceso exacta al directorio de almacenamiento interno puede variar entre el dispositivo y el dispositivo y entre las versiones de Android. Por este motivo, las aplicaciones no deben codificar de forma rígida la ruta de acceso al directorio de almacenamiento de archivos internos y, en su lugar, usar las API de Xamarin. Android, como `System.Environment.GetFolderPath()`.

Para maximizar el uso compartido de código, las aplicaciones de Xamarin. Android (o las aplicaciones de Xamarin. Forms que tienen como destino Xamarin. Android) deben usar el método [`System.Environment.GetFolderPath()`](xref:System.Environment.GetFolderPath*) . En Xamarin. Android, este método devolverá una cadena para un directorio que se encuentra en la misma ubicación que `Android.Content.Context.FilesDir`. Este método toma una enumeración, `System.Environment.SpecialFolder`, que se usa para identificar un conjunto de constantes enumeradas que representan las rutas de acceso de carpetas especiales utilizadas por el sistema operativo. No todos los valores de `System.Environment.SpecialFolder` se asignarán a un directorio válido en Xamarin. Android. En la tabla siguiente se describe qué ruta de acceso se puede esperar para un valor determinado de `System.Environment.SpecialFolder`:

| System.Environment.SpecialFolder | Path  |
|----------------------|---|
| `ApplicationData` | **/.Config de _almacenamiento interno\__** |
| `Desktop` | **/Desktop de _almacenamiento interno\__** |
| `LocalApplicationData` | **/.Local/share de _almacenamiento interno\__** |
| `MyDocuments` | **_ALMACENAMIENTO de\_interno_** |
| `MyMusic` | **/Music de _almacenamiento interno\__** |
| `MyPictures` | **/Pictures de _almacenamiento interno\__** |
| `MyVideos` | **/Videos de _almacenamiento interno\__** |
| `Personal` | **_ALMACENAMIENTO de\_interno_** |

### <a name="reading-or-writing-to-files-on-internal-storage"></a>Leer o escribir en archivos en el almacenamiento interno

Cualquiera de las [ C# API para escribir](https://docs.microsoft.com/dotnet/csharp/programming-guide/file-system/how-to-write-to-a-text-file) en un archivo es suficiente. lo único que se necesita es obtener la ruta de acceso al archivo que se encuentra en el directorio asignado a la aplicación. Se recomienda encarecidamente que se usen las versiones asincrónicas de las API de .NET para minimizar los problemas que pueden asociarse con el acceso a archivos que bloquean el subproceso principal.

Este fragmento de código es un ejemplo de escritura de un entero en un archivo de texto UTF-8 en el directorio de almacenamiento interno de una aplicación:

```csharp
public async Task SaveCountAsync(int count)
{
    var backingFile = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "count.txt");
    using (var writer = File.CreateText(backingFile))
    {
        await writer.WriteLineAsync(count.ToString());
    }
}
```

El siguiente fragmento de código proporciona una manera de leer un valor entero que se almacenó en un archivo de texto:

```csharp
public async Task<int> ReadCountAsync()
{
    var backingFile = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "count.txt");

    if (backingFile == null || !File.Exists(backingFile))
    {
        return 0;
    }

    var count = 0;
    using (var reader = new StreamReader(backingFile, true))
    {
        string line;
        while ((line = await reader.ReadLineAsync()) != null)
        {
            if (int.TryParse(line, out var newcount))
            {
                count = newcount;
            }
        }
    }

    return count;
}
```

## <a name="using--xamarinessentials-ndash-file-system-helpers"></a>Uso de Xamarin. Essentials &ndash; aplicaciones auxiliares del sistema de archivos

[Xamarin. Essentials](~/essentials/file-system-helpers.md?context=xamarin/android) es un conjunto de API para escribir código compatible con varias plataformas. Las [aplicaciones auxiliares del sistema de archivos](~/essentials/file-system-helpers.md?context=xamarin/android) son una clase que contiene una serie de aplicaciones auxiliares para simplificar la ubicación de los directorios de datos y la memoria caché de la aplicación. Este fragmento de código proporciona un ejemplo de cómo buscar el directorio de almacenamiento interno y el directorio de la memoria caché de una aplicación:

```csharp
// Get the path to a file on internal storage
var backingFile = Path.Combine(Xamarin.Essentials.FileSystem.AppDataDirectory, "count.txt");

// Get the path to a file in the cache directory
var cacheFile = Path.Combine(Xamarin.Essentials.FileSystem.CacheDirectory, "count.txt");
```

## <a name="hiding-files-from-the-mediastore"></a>Ocultar archivos del `MediaStore`

El `MediaStore` es un componente de Android que recopila metadatos acerca de los archivos multimedia (vídeos, música, imágenes) en un dispositivo Android. Su finalidad es simplificar el uso compartido de estos archivos en todas las aplicaciones Android del dispositivo.

Los archivos privados no se mostrarán como medios que se pueden compartir. Por ejemplo, si una aplicación guarda una imagen en su almacenamiento externo privado, ese archivo no lo recogerá el escáner multimedia (`MediaStore`).

`MediaStore`recogerá los archivos públicos. Directorios que tienen un nombre de archivo de cero bytes **.** No se examinará nomedia `MediaStore`.

## <a name="related-links"></a>Vínculos relacionados

* [Almacenamiento externo](~/android/platform/files/external-storage.md)
* [Guardar archivos en el almacenamiento del dispositivo](https://developer.android.com/training/data-storage/files)
* [Aplicaciones auxiliares del sistema de archivos de Xamarin. Essentials](~/essentials/file-system-helpers.md?context=xamarin/android)
* [Copia de seguridad de datos de usuario con copia de seguridad automática](https://developer.android.com/guide/topics/data/autobackup)
* [Almacenamiento que se va a adoptar](https://source.android.com/devices/storage/adoptable)
