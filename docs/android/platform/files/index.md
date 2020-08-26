---
title: Acceso a archivos con Xamarin.Android
description: En esta guía se explica cómo acceder a archivos en Xamarin.Android.
ms.prod: xamarin
ms.assetid: FC1CFC58-B799-4DD6-8ED1-DE36B0E56856
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/23/2018
ms.openlocfilehash: 746433293c52d7071a3289962ea021f716fd0cfe
ms.sourcegitcommit: f7fe46c0236a7130b63a33d9d1670d5111582dd2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2020
ms.locfileid: "88186179"
---
# <a name="file-storage-and-access-with-xamarinandroid"></a>Almacenamiento y acceso a archivos con Xamarin.Android

Un requisito común para las aplicaciones Android es manipular archivos, es decir, guardar imágenes, descargar documentos o exportar datos para compartirlos con otros programas. Android (que se basa en Linux) admite esto proporcionando espacio para el almacenamiento de archivos. Android agrupa el sistema de archivos en dos tipos diferentes de almacenamiento:

* **Almacenamiento interno**: se trata de una parte del sistema de archivos a la que solo puede acceder la aplicación o el sistema operativo.
* **Almacenamiento externo**: se trata de una partición del almacenamiento de archivos a la que pueden acceder todas las aplicaciones, el usuario y, posiblemente, otros dispositivos. En algunos dispositivos, el almacenamiento externo puede ser extraíble (como una tarjeta SD).

Estas agrupaciones son solo conceptuales y no tienen por qué hacer referencia a una única partición o directorio del dispositivo. Un dispositivo Android siempre proporcionará una partición para el almacenamiento interno y otra para el externo. Es posible que determinados dispositivos tengan varias particiones que se consideran almacenamiento externo. Independientemente de las particiones, las API para leer, escribir o crear archivos son las mismas. Hay dos conjuntos de API que puede usar una aplicación de Xamarin.Android para acceder a los archivos:

1. **API de .NET (proporcionadas por Mono y encapsuladas mediante Xamarin.Android)** : incluyen los [asistentes del sistema de archivos](~/essentials/file-system-helpers.md?context=xamarin/android) que proporciona [Xamarin.Essentials](~/essentials/index.md?context=xamarin/android). Las API de .NET proporcionan la mejor compatibilidad entre plataformas y, por tanto, esta guía se centrará en estas API.
1. **API nativas de acceso a archivos de Java (proporcionadas por Java y encapsuladas mediante Xamarin.Android)** : Java proporciona sus propias API para leer y escribir archivos. Se trata de una alternativa a las API de .NET totalmente aceptable, pero son específicas de Android y no son adecuadas para las aplicaciones que están pensadas para ser multiplataforma.

Leer archivos y escribir en ellos se hace casi de la misma manera en Xamarin.Android que en cualquier otra aplicación de .NET. La aplicación Xamarin.Android determina la ruta de acceso al archivo que se manipulará y, a continuación, usa expresiones estándar de .NET para acceder a él. Dado que las rutas de acceso reales al almacenamiento interno y externo pueden variar de un dispositivo a otro, o de una versión de Android a otra, no se recomienda codificar de forma rígida la ruta de acceso a los archivos. En su lugar, use las API de Xamarin.Android para determinar la ruta de acceso a los archivos. De este modo, las API de .NET para leer y escribir archivos exponen las API nativas de Android que le ayudarán a determinar la ruta de acceso en el almacenamiento interno y externo.

Antes de analizar las API relacionadas con el acceso a los archivos, es importante comprender algunos datos relacionados con el almacenamiento interno y externo. Esto es lo que abordará la sección siguiente.

## <a name="internal-vs-external-storage"></a>Almacenamiento interno y externo

Conceptualmente, el almacenamiento interno y el almacenamiento externo son muy similares; son lugares en los que una aplicación de Xamarin.Android puede guardar archivos. Esta similitud puede resultar confusa para los desarrolladores que no están familiarizados con Android, ya que no está claro cuando una aplicación debe usar el almacenamiento interno o el externo.

El almacenamiento interno hace referencia a la memoria no volátil que Android asigna al sistema operativo, los APK y para las aplicaciones individuales. A este espacio solo pueden acceder el sistema operativo o las aplicaciones. Android asigna un directorio para cada aplicación en la partición de almacenamiento interno. Cuando se desinstala una aplicación, también se eliminan todos los archivos conservados en el almacenamiento interno en ese directorio. El almacenamiento interno es más adecuado para los archivos a los que solo se puede acceder desde la aplicación y que no se compartirán con otras aplicaciones o que tendrán muy poco valor una vez desinstalada la aplicación. En Android 6.0 o en versiones posteriores, Google puede realizar automáticamente una copia de seguridad de los archivos del almacenamiento interno mediante la característica [Copia de seguridad automática en Android 6.0](https://developer.android.com/guide/topics/data/autobackup). El almacenamiento interno presenta las siguientes desventajas:

* No se pueden compartir los archivos.
* Los archivos se eliminarán cuando se desinstale la aplicación.
* Es posible que el espacio disponible en el almacenamiento interno sea limitado.

El almacenamiento externo se refiere al almacenamiento de archivos que no es interno y al que tampoco tiene acceso exclusivo una aplicación. El objetivo principal del almacenamiento externo es proporcionar un lugar donde almacenar los archivos que se pueden compartir entre las aplicaciones o que son demasiado grandes para el almacenamiento interno. La ventaja del almacenamiento externo es que normalmente tiene mucho más espacio para los archivos que el interno. Sin embargo, no todos los dispositivos tienen almacenamiento externo y, de tenerlo, puede que el usuario necesite permiso especial para acceder a él.

> [!NOTE]
> En el caso de los dispositivos que admiten varios usuarios, Android proporciona a cada usuario su propio directorio en el almacenamiento interno y externo. No pueden acceder a este directorio otros usuarios del dispositivo. Esta separación no es visible para las aplicaciones, siempre que no codifiquen de forma rígida rutas de acceso a archivos en el almacenamiento interno o externo.

Como regla general, las aplicaciones de Xamarin.Android preferirán guardar los archivos en el almacenamiento interno cuando sea posible y usarán el almacenamiento externo cuando sea necesario compartir los archivos con otras aplicaciones, cuando estos sean demasiado grandes o cuando deban conservarse incluso si se desinstala la aplicación. Por ejemplo, un archivo de configuración es más adecuado para un almacenamiento interno, ya que solo tiene importancia para la aplicación que lo crea.  Por el contrario, las fotos cumplen los requisitos para el almacenamiento externo. Pueden ser muy grandes y, en muchos casos, es posible que el usuario quiera compartirlas o acceder a ellas, incluso si se desinstala la aplicación.

Esta guía se centrará en el almacenamiento interno. Para obtener más información sobre el uso del almacenamiento externo en una aplicación de Xamarin.Android, consulte el artículo [Almacenamiento externo](~/android/platform/files/external-storage.md).

## <a name="working-with-internal-storage"></a>Cómo trabajar con almacenamiento interno

El directorio de almacenamiento interno para una aplicación viene determinado por el sistema operativo, y se expone a las aplicaciones de Android mediante la propiedad `Android.Content.Context.FilesDir`. Esto devolverá un objeto `Java.IO.File` que representa el directorio que Android ha dedicado exclusivamente para la aplicación.  Por ejemplo, para una aplicación con el nombre de paquete **com.companyname**, el directorio de almacenamiento interno podría ser:

```bash
/data/user/0/com.companyname/files
```

En este documento se hará referencia al directorio de almacenamiento interno como _ALMACENAMIENTO\_INTERNO_.

> [!IMPORTANT]
> La ruta de acceso exacta al directorio de almacenamiento interno puede variar de un dispositivo a otro y entre las distintas versiones de Android. Por este motivo, las aplicaciones no deben codificar de forma rígida la ruta de acceso al directorio de almacenamiento interno de archivos y, en su lugar, deben usar las API de Xamarin.Android, como `System.Environment.GetFolderPath()`.

Para maximizar el uso compartido de código, las aplicaciones de Xamarin.Android (o las aplicaciones de Xamarin.Forms que tienen como destino Xamarin.Android) deben usar el método [`System.Environment.GetFolderPath()`](xref:System.Environment.GetFolderPath*). En Xamarin.Android, este método devolverá una cadena para un directorio que se encuentre en la misma ubicación que `Android.Content.Context.FilesDir`. Este método toma una enumeración, `System.Environment.SpecialFolder`, que se usa para identificar un conjunto de constantes enumeradas que representan las rutas de acceso a carpetas especiales que usa el sistema operativo. No todos los valores `System.Environment.SpecialFolder` se asignarán a un directorio válido en Xamarin.Android. En la tabla siguiente se describe qué ruta de acceso se puede esperar para un valor `System.Environment.SpecialFolder` determinado:

| System.Environment.SpecialFolder | Ruta de acceso  |
|----------------------|---|
| `ApplicationData` | **_ALMACENAMIENTO\_INTERNO_/.config** |
| `Desktop` | **_ALMACENAMIENTO\_INTERNO_/Desktop** |
| `LocalApplicationData` | **_ALMACENAMIENTO\_INTERNO_/.local/share** |
| `MyDocuments` | **_ALMACENAMIENTO\_INTERNO_** |
| `MyMusic` | **_ALMACENAMIENTO\_INTERNO_/Music** |
| `MyPictures` | **_ALMACENAMIENTO\_INTERNO_/Pictures** |
| `MyVideos` | **_ALMACENAMIENTO\_INTERNO_/Videos** |
| `Personal` | **_ALMACENAMIENTO\_INTERNO_** |
| `Fonts` | **_INTERNAL\_STORAGE_/.fonts** |
| `Templates` | **_INTERNAL\_STORAGE_/Templates** |
| `CommonApplicationData` | **/usr/share** |
| `CommonApplicationData` | **/usr/share** |

### <a name="reading-or-writing-to-files-on-internal-storage"></a>Lectura y escritura en archivos en el almacenamiento interno

Se puede usar cualquiera de las [API de C# para escribir](https://docs.microsoft.com/dotnet/csharp/programming-guide/file-system/how-to-write-to-a-text-file) en un archivo; solo se necesita obtener la ruta de acceso al archivo que se encuentra en el directorio asignado a la aplicación. Se recomienda encarecidamente que se usen las versiones asincrónicas de las API de .NET para minimizar los problemas que pueden asociarse con el acceso a archivos que bloquean el subproceso principal.

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

El siguiente fragmento de código proporciona una manera de leer un valor entero almacenado en un archivo de texto:

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

## <a name="using--xamarinessentials-ndash-file-system-helpers"></a>Uso de Xamarin.Essentials: asistentes del sistema de archivos

[Xamarin.Essentials](~/essentials/file-system-helpers.md?context=xamarin/android) es un conjunto de API para escribir código compatible con varias plataformas. Los [asistentes del sistema de archivos](~/essentials/file-system-helpers.md?context=xamarin/android) son una clase que contiene una serie de asistentes para ayudar a localizar los directorios de datos y la memoria caché de la aplicación. Este fragmento de código proporciona un ejemplo de cómo buscar el directorio de almacenamiento interno y el directorio de la memoria caché de una aplicación:

```csharp
// Get the path to a file on internal storage
var backingFile = Path.Combine(Xamarin.Essentials.FileSystem.AppDataDirectory, "count.txt");

// Get the path to a file in the cache directory
var cacheFile = Path.Combine(Xamarin.Essentials.FileSystem.CacheDirectory, "count.txt");
```

## <a name="hiding-files-from-the-mediastore"></a>Cómo ocultar archivos a `MediaStore`

`MediaStore` es un componente de Android que recopila metadatos de los archivos multimedia (vídeos, música e imágenes) en un dispositivo Android. Su finalidad es simplificar el uso compartido de estos archivos en todas las aplicaciones Android del dispositivo.

Los archivos privados no se mostrarán como elementos multimedia que se pueden compartir. Por ejemplo, si una aplicación guarda una imagen en su almacenamiento externo privado, ese archivo no lo recopilará el escáner de elementos multimedia (`MediaStore`).

`MediaStore` recopilará los archivos públicos. Sin embargo, los directorios que tienen un nombre de archivo de cero bytes **.NOMEDIA** no serán recopilados por `MediaStore`.

## <a name="related-links"></a>Vínculos relacionados

* [Almacenamiento externo](~/android/platform/files/external-storage.md)
* [Cómo guardar archivos en un dispositivo de almacenamiento](https://developer.android.com/training/data-storage/files)
* [Asistentes del sistema de archivos de Xamarin.Essentials](~/essentials/file-system-helpers.md?context=xamarin/android)
* [Cómo crear una copia de seguridad automática para los datos del usuario](https://developer.android.com/guide/topics/data/autobackup)
* [Almacenamiento adoptable](https://source.android.com/devices/storage/adoptable)
