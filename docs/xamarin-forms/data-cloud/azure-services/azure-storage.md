---
title: Almacenar datos y acceder a ellos en Azure Storage de Xamarin. Forms
description: Azure Storage es una solución de almacenamiento en la nube escalable que puede utilizarse para almacenar datos estructurados y no estructurados. En este artículo se explica cómo usar Xamarin. Forms para almacenar datos binarios y de texto en Azure Storage y cómo obtener acceso a los datos.
ms.prod: xamarin
ms.assetid: 5B10D37B-839B-4CD0-9C65-91014A93F3EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/28/2018
ms.openlocfilehash: 8d773abbca348d09d3359f09cbded22f6521fb7f
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487326"
---
# <a name="store-and-access-data-in-azure-storage-from-xamarinforms"></a>Almacenar datos y acceder a ellos en Azure Storage de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azurestorage)

_Azure Storage es una solución de almacenamiento en la nube escalable que se puede usar para almacenar datos estructurados y no estructurados. En este artículo se muestra cómo usar Xamarin. Forms para almacenar datos binarios y de texto en Azure Storage y cómo obtener acceso a los datos._

Azure Storage proporciona cuatro servicios de almacenamiento:

- Almacenamiento de blobs. Un blob puede ser texto o datos binarios, como las copias de seguridad, las máquinas virtuales, archivos multimedia o documentos.
- Table Storage es un almacén de clave-atributo NoSQL.
- Queue Storage es un servicio de mensajería para el procesamiento de flujo de trabajo y comunicación entre servicios en la nube.
- Almacenamiento de archivos ofrece almacenamiento compartido mediante el protocolo SMB.

Hay dos tipos de cuentas de almacenamiento:

- Una cuenta de almacenamiento de uso general proporciona acceso a servicios de Azure Storage desde una sola cuenta.
- Una cuenta de almacenamiento de Blob es una cuenta de almacenamiento especializada para almacenar blobs. Se recomienda este tipo de cuenta cuando necesite almacenar los datos blob.

En este artículo y que acompaña a la aplicación de ejemplo muestra cómo cargar archivos de texto e imágenes a blob storage y descargarlos. Además, también se muestra al recuperar una lista de archivos desde blob storage y eliminar archivos.

Para obtener más información sobre el almacenamiento de Azure, consulte [Introducción al almacenamiento](https://azure.microsoft.com/documentation/articles/storage-introduction/).

> [!NOTE]
> Si no tiene una [suscripción a Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), cree una [cuenta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de empezar.

## <a name="introduction-to-blob-storage"></a>Introducción a Blob Storage

Almacenamiento de blobs consta de tres componentes, que se muestran en el diagrama siguiente:

![](azure-storage-images/blob-storage.png "Blob Storage Concepts")

Todo el acceso a Azure Storage es a través de una cuenta de almacenamiento. Una cuenta de almacenamiento puede contener un número ilimitado de contenedores, y un contenedor puede almacenar un número ilimitado de blobs, hasta alcanzar el límite de capacidad de la cuenta de almacenamiento.

Un blob es un archivo de cualquier tipo y tamaño. Azure Storage admite tres tipos diferentes de blob:

- Los blobs en bloques están optimizados para la transmisión por secuencias y el almacenamiento de objetos en la nube, y son una buena opción para almacenar copias de seguridad, archivos multimedia, documentos, etc. Los blobs en bloques pueden tener un tamaño máximo de 195 GB.
- Anexar blobs son similares a los blobs en bloques, pero están optimizados para anexar las operaciones, como el registro. Anexar blobs pueden ser hasta 195Gb de tamaño.
- Los blobs en páginas están optimizados para operaciones frecuentes de lectura/escritura y se utilizan normalmente para almacenar las máquinas virtuales y sus discos. Blobs en páginas pueden tener hasta 1Tb de tamaño.

> [!NOTE]
> Tenga en cuenta que las cuentas de almacenamiento de blobs admiten bloques y anexar blobs, pero no los blobs en páginas.

Un blob se carga en Azure Storage y descargado de Azure Storage, como una secuencia de bytes. Por lo tanto, los archivos se deben convertir en una secuencia de bytes antes de la carga y convertido de nuevo a su representación original después de la descarga.

Cada objeto que se almacena en el almacenamiento de Azure tiene una dirección URL única. El nombre de cuenta de almacenamiento forma el subdominio de esa dirección y la combinación de nombre de dominio y subdominio forms un *extremo* para la cuenta de almacenamiento. Por ejemplo, si la cuenta de almacenamiento se denomina *mystorageaccount*, el extremo de blob predeterminado para la cuenta de almacenamiento es `https://mystorageaccount.blob.core.windows.net`.

La dirección URL para tener acceso a un objeto en una cuenta de almacenamiento se crea anexando la ubicación del objeto en la cuenta de almacenamiento para el punto de conexión. Por ejemplo, una dirección de blob tendrá el formato `https://mystorageaccount.blob.core.windows.net/mycontainer/myblob`.

## <a name="setup"></a>Instalación

El proceso para integrar una cuenta de almacenamiento de Azure en una aplicación de Xamarin.Forms es como sigue:

1. Cree una cuenta de almacenamiento. Para obtener más información, consulte [crear una cuenta de almacenamiento](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account).
1. Agregar el [biblioteca de cliente de Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) a la aplicación de Xamarin.Forms.
1. Configurar la cadena de conexión de almacenamiento. Para obtener más información, consulte [conectarse a Azure Storage](#connecting).
1. Agregar `using` directivas para el `Microsoft.WindowsAzure.Storage` y `Microsoft.WindowsAzure.Storage.Blob` espacios de nombres a las clases que tendrá acceso a Azure Storage.

<a name="connecting" />

## <a name="connecting-to-azure-storage"></a>Conectarse a Almacenamiento de Azure

Se debe autenticar cada solicitud realizada en los recursos de la cuenta de almacenamiento. Aunque los blobs se pueden configurar para admitir la autenticación anónima, hay dos enfoques principales de que una aplicación puede usar para autenticarse con una cuenta de almacenamiento:

- Clave compartida. Este enfoque utiliza la clave de cuenta y el nombre de la cuenta de almacenamiento de Azure para servicios de almacenamiento de acceso. Una cuenta de almacenamiento se asigna a dos claves privadas en la creación que puede usarse para la autenticación de clave compartida.
- Firma de acceso compartido. Se trata de un token que se puede anexar a una dirección URL que permite el acceso delegado a un recurso de almacenamiento, con los permisos especifica, durante el período de tiempo que sea válida.

Se pueden especificar las cadenas de conexión que incluya la información de autenticación necesaria para tener acceso a recursos de Azure Storage desde una aplicación. Además, se puede configurar una cadena de conexión para conectarse al emulador de almacenamiento de Azure desde Visual Studio.

> [!NOTE]
> Azure Storage admite HTTP y HTTPS en una cadena de conexión. Sin embargo, se recomienda utilizar HTTPS.

### <a name="connecting-to-the-azure-storage-emulator"></a>Conectar con el emulador de almacenamiento de Azure

El emulador de almacenamiento de Azure proporciona un entorno local que emula el Azure blob, cola y tabla de servicios para fines de desarrollo.

La siguiente cadena de conexión debe utilizarse para conectarse al emulador de almacenamiento de Azure:

```csharp
UseDevelopmentStorage=true
```

Para obtener más información sobre el emulador de almacenamiento de Azure, consulte [usar el emulador de almacenamiento de Azure para desarrollo y pruebas](https://azure.microsoft.com/documentation/articles/storage-use-emulator/).

### <a name="connecting-to-azure-storage-using-a-shared-key"></a>Conectarse a Azure Storage mediante una clave compartida

El siguiente formato de cadena de conexión se debe usar para conectarse a Azure Storage con una clave compartida:

```csharp
DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey
```

`myAccountName` se debe reemplazar por el nombre de la cuenta de almacenamiento y `myAccountKey` debe reemplazarse por una de las dos claves de acceso de cuenta.

> [!NOTE]
> Uso compartido cuando la autenticación de clave, el nombre de cuenta y la clave de cuenta se distribuirá a cada persona que usa la aplicación, que proporcionará acceso de lectura/escritura completo a la cuenta de almacenamiento. Por lo tanto, use la autenticación de clave compartida solo con fines de prueba y nunca distribuir claves a otros usuarios.

### <a name="connecting-to-azure-storage-using-a-shared-access-signature"></a>Conectarse a Azure Storage mediante una firma de acceso compartido

El siguiente formato de cadena de conexión se debe usar para conectarse a Azure Storage con una SAS:

`BlobEndpoint=myBlobEndpoint;SharedAccessSignature=mySharedAccessSignature`

`myBlobEndpoint` debe reemplazarse por la dirección URL del extremo de blob, y `mySharedAccessSignature` debe reemplazarse por la SAS. La SAS proporciona el protocolo, el punto de conexión de servicio y las credenciales para acceder al recurso.

> [!NOTE]
> Se recomienda la autenticación de SAS para aplicaciones de producción. Sin embargo, en una aplicación de producción se debe recuperar la SAS de un servicio back-end a petición, en lugar de que se incluye con la aplicación.

Para obtener más información sobre las firmas de acceso compartido, consulte [utilizando firmas de acceso compartido (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).

## <a name="creating-a-container"></a>Creación de un contenedor

El `GetContainer` método se usa para recuperar una referencia a un contenedor con nombre, que puede usarse para recuperar los blobs del contenedor o a agregar al contenedor de blobs. El siguiente ejemplo de código muestra el método `GetContainer`:

```csharp
static CloudBlobContainer GetContainer(ContainerType containerType)
{
  var account = CloudStorageAccount.Parse(Constants.StorageConnection);
  var client = account.CreateCloudBlobClient();
  return client.GetContainerReference(containerType.ToString().ToLower());
}
```

El `CloudStorageAccount.Parse` método analiza una cadena de conexión y devuelve un `CloudStorageAccount` instancia que representa la cuenta de almacenamiento. Un `CloudBlobClient` , a continuación, se crea la instancia, que se utiliza para recuperar los contenedores y blobs, mediante el `CreateCloudBlobClient` método. El `GetContainerReference` método recupera el contenedor especificado como un `CloudBlobContainer` instancia antes de que se devuelva al método de llamada. En este ejemplo, el nombre del contenedor es el `ContainerType` valor de enumeración, puede convertida en una cadena en minúsculas.

> [!NOTE]
> Los nombres de contenedor deben estar en minúsculas y deben empezar por una letra o un número. Además, solo puede contener letras, números y guiones y debe tener entre 3 y 63 caracteres.

El `GetContainer` método se invoca de la siguiente manera:

```csharp
var container = GetContainer(containerType);
```

El `CloudBlobContainer` instancia, a continuación, se puede usar para crear un contenedor si aún no existe:

```csharp
await container.CreateIfNotExistsAsync();
```

De forma predeterminada, un contenedor recién creado es privado. Esto significa que se debe especificar una clave de acceso de almacenamiento para recuperar los blobs del contenedor. Para obtener información acerca de cómo realizar los blobs dentro de un público de contenedor, consulte [crear un contenedor](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#create-a-container).

## <a name="uploading-data-to-a-container"></a>Cargar datos en un contenedor

El `UploadFileAsync` método se usa para cargar una secuencia de bytes de datos al almacenamiento de blobs y se muestra en el ejemplo de código siguiente:

```csharp
public static async Task<string> UploadFileAsync(ContainerType containerType, Stream stream)
{
  var container = GetContainer(containerType);
  await container.CreateIfNotExistsAsync();

  var name = Guid.NewGuid().ToString();
  var fileBlob = container.GetBlockBlobReference(name);
  await fileBlob.UploadFromStreamAsync(stream);

  return name;
}
```

Después de recuperar una referencia de contenedor, el método crea el contenedor si aún no existe. Un nuevo `Guid` , a continuación, se crea para que actúe como un nombre de blob único y una referencia de blob en bloques se recupera como una `CloudBlockBlob` instancia. El flujo de datos, a continuación, se carga en el blob mediante el `UploadFromStreamAsync` método, que crea el blob si todavía no existe o lo sobrescribe si ya existe.

Antes de poder cargar un archivo con este método de almacenamiento de blobs, primero debe convertirse en una secuencia de bytes. Esto se muestra en el ejemplo de código siguiente:

```csharp
var byteData = Encoding.UTF8.GetBytes(text);
uploadedFilename = await AzureStorage.UploadFileAsync(ContainerType.Text, new MemoryStream(byteData));
```

El `text` datos se convierten en una matriz de bytes que se encapsula como una secuencia que se pasa a la `UploadFileAsync` método.

## <a name="downloading-data-from-a-container"></a>Descarga de datos de un contenedor

El `GetFileAsync` método se utiliza para descargar datos de blob de Azure Storage y se muestra en el ejemplo de código siguiente:

```csharp
public static async Task<byte[]> GetFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);

  var blob = container.GetBlobReference(name);
  if (await blob.ExistsAsync())
  {
    await blob.FetchAttributesAsync();
    byte[] blobBytes = new byte[blob.Properties.Length];

    await blob.DownloadToByteArrayAsync(blobBytes, 0);
    return blobBytes;
  }
  return null;
}
```

Después de recuperar una referencia de contenedor, el método recupera una referencia de blob para los datos almacenados. Si existe el blob, sus propiedades se recuperan mediante el `FetchAttributesAsync` método. Se crea una matriz de bytes del tamaño correcto y se descarga el blob como una matriz de bytes que se devuelve al método de llamada.

Después de descargar los datos de blob de bytes, deben convertirse en su representación original. Esto se muestra en el ejemplo de código siguiente:

```csharp
var byteData = await AzureStorage.GetFileAsync(ContainerType.Text, uploadedFilename);
string text = Encoding.UTF8.GetString(byteData);
```

Se recupera la matriz de bytes de almacenamiento de Azure mediante el `GetFileAsync` método antes de que se convierta a un UTF8 cadena codificada.

## <a name="listing-data-in-a-container"></a>Mostrar datos en un contenedor

El `GetFilesListAsync` método se usa para recuperar una lista de los blobs almacenados en un contenedor y se muestra en el ejemplo de código siguiente:

```csharp
public static async Task<IList<string>> GetFilesListAsync(ContainerType containerType)
{
  var container = GetContainer(containerType);

  var allBlobsList = new List<string>();
  BlobContinuationToken token = null;

  do
  {
    var result = await container.ListBlobsSegmentedAsync(token);
    if (result.Results.Count() > 0)
    {
      var blobs = result.Results.Cast<CloudBlockBlob>().Select(b => b.Name);
      allBlobsList.AddRange(blobs);
    }
    token = result.ContinuationToken;
  } while (token != null);

  return allBlobsList;
}
```

Después de recuperar una referencia de contenedor, el método usa el contenedor `ListBlobsSegmentedAsync` método para recuperar las referencias a los blobs dentro del contenedor. Los resultados devueltos por la `ListBlobsSegmentedAsync` se enumeran método mientras el `BlobContinuationToken` instancia no es `null`. Cada blob se transmite devueltos `IListBlobItem` a un `CloudBlockBlob` en pedidos acceso a la `Name` propiedad del blob, antes de que sea el valor se agrega a la `allBlobsList` colección. Una vez el `BlobContinuationToken` instancia es `null`, ha devuelto el último nombre de blob y ejecución sale del bucle.

## <a name="deleting-data-from-a-container"></a>Eliminar datos de un contenedor

El `DeleteFileAsync` método se usa para eliminar un blob de un contenedor y se muestra en el ejemplo de código siguiente:

```csharp
public static async Task<bool> DeleteFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);
  var blob = container.GetBlobReference(name);
  return await blob.DeleteIfExistsAsync();
}
```

Después de recuperar una referencia de contenedor, el método recupera una referencia de blob para el blob especificado. A continuación, se elimina el blob con el `DeleteIfExistsAsync` método.

## <a name="related-links"></a>Vínculos relacionados

- [Almacenamiento de Azure (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azurestorage)
- [Introducción al almacenamiento](https://azure.microsoft.com/documentation/articles/storage-introduction/)
- [Cómo usar Blob Storage desde Xamarin](https://azure.microsoft.com/documentation/articles/storage-xamarin-blob-storage/)
- [Uso de firmas de acceso compartido (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)
- [Azure Storage de Windows (NuGet)](https://www.nuget.org/packages/WindowsAzure.Storage/)
