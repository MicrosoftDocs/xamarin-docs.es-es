---
title: Compresión de archivos en Xamarin. iOS
description: En este documento se describe cómo trabajar con la API de libcompression en Xamarin. iOS. Describe la desinflación, la inflación y los diferentes algoritmos admitidos.
ms.prod: xamarin
ms.assetid: 94D05DAB-01E8-4C62-9CEF-9D6417EEA8EB
ms.technology: xamarin-ios
author: mandel-macaque
ms.author: mandel
ms.date: 03/04/2019
ms.openlocfilehash: bcc63aa4e1926f5502d571bf47c83b0c8ea7e429
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199524"
---
# <a name="file-compression-in-xamarinios"></a>Compresión de archivos en Xamarin. iOS

Las aplicaciones Xamarin destinadas a iOS 9,0 o macOS 10,11 (y versiones más recientes) pueden usar el _marco de compresión_ para comprimir (codificar) y descomprimir (descodificar) los datos. Xamarin. iOS proporciona este marco de trabajo después de Stream API. El marco de compresión permite a los programadores interactuar con los datos de compresión y descomprimidos como si fueran secuencias normales sin necesidad de utilizar delegados o devoluciones de llamada.

El marco de compresión proporciona compatibilidad con los algoritmos siguientes:

* LZ4
* LZ4 sin procesar
* Lzfse
* Lzma
* Zlib

El uso del marco de compresión permite a los desarrolladores realizar operaciones de compresión sin ninguna biblioteca de terceros o paquetes Nuget. Esto reduce las dependencias externas y garantiza que las operaciones de compresión se admitirán en todas las plataformas (siempre y cuando cumplan los requisitos mínimos de sistema operativo).

## <a name="general-file-decompression"></a>Descompresión de archivos general

El marco de compresión usa Stream API en Xamarin. iOS y Xamarin. Mac. Esta API significa que para comprimir los datos, el desarrollador puede usar los patrones normales usados en otras API de e/s en .NET. En el ejemplo siguiente se muestra cómo descomprimir datos con el marco de compresión, que es similar a la API `System.IO.Compression.DeflateStream` que se encuentra en la API:

```csharp
// sample zlib data
static byte [] compressed_data = { 0xf3, 0x48, 0xcd, 0xc9, 0xc9, 0xe7, 0x02, 0x00 };

using (var backing = new MemoryStream (compressed_data)) // compress data to read
using (var decompressing = new CompressionStream (backing, CompressionMode.Decompress, CompressionAlgorithm.Zlib)) // create decompression stream with the correct algorithm
using (var reader = new StreamReader (decompressing))
{
    // perform the required stream operations
    Console.WriteLine (reader.ReadLine ());
}
```

Implementa la `IDisposable` interfaz, como otra `System.IO.Streams`, por lo que los desarrolladores deben asegurarse de que los recursos se liberan una vez que ya no son necesarios. `CompressionStream`

## <a name="general-file-compression"></a>Compresión de archivos general

La API de compresión también permite a los desarrolladores comprimir datos después de la misma API. Los datos se pueden comprimir utilizando uno de los algoritmos proporcionados que `CompressionAlgorithm` se indican en el enumerador.

```csharp
// sample method that copies the data from the source stream to the destination stream
static void CopyStream (Stream src, Stream dest)
{
    byte[] array = new byte[1024];
    int bytes_read;
    bytes_read = src.Read (array, 0, 1024);
    while (bytes_read != 0) {
        dest.Write (array, 0, bytes_read);
        bytes_read = src.Read (array, 0, 1024);
    }
}

static void CompressExample ()
{
    // create some sample data to compress
    byte [] data = new byte[100000];
    for (int i = 0; i < 100000; i++) {
        data[i] = (byte) i;
    }

    using (var dataStream = new MemoryStream (data)) // stream that contains the data to compress
    using (var backing = new MemoryStream ()) // stream in which the compress data will be written
    using (var compressing = new CompressionStream (backing, CompressionMode.Compress, CompressionAlgorithm.Zlib, true))
    {
        // copy the data to the compressing stream
        CopyStream (dataStream, compressing);
        // at this point, the CompressionStream contains all the data from the dataStream but compressed
        // using the zlib algorithm
    }
```

## <a name="async-support"></a>Compatibilidad con Async

Admite todas las operaciones asincrónicas que admite `System.IO.DeflateStream`, lo que significa que los desarrolladores pueden usar la palabra clave Async para realizar las operaciones de compresión y descompresión sin bloquear el subproceso de la interfaz de usuario. `CompressionStream`
