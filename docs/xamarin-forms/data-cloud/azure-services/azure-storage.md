---
title: Almacenar y obtener acceso a los datos de Azure Storage desde Xamarin.Forms
description: Azure Storage es una solución de almacenamiento en la nube escalable que se puede usar para almacenar datos estructurados y no estructurados. En este artículo se explica cómo usar Xamarin.Forms para almacenar datos binarios y de texto en Azure Storage y cómo obtener acceso a los datos.
ms.prod: xamarin
ms.assetid: 5B10D37B-839B-4CD0-9C65-91014A93F3EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/28/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bd726a5e5d6064ecb9aa1c862697e08a9c4733f4
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370122"
---
# <a name="store-and-access-data-in-azure-storage-from-no-locxamarinforms"></a>Almacenar y obtener acceso a los datos de Azure Storage desde Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/webservices-azurestorage)

_Azure Storage es una solución de almacenamiento en la nube escalable que se puede usar para almacenar datos estructurados y no estructurados. En este artículo se muestra cómo usar Xamarin.Forms para almacenar datos binarios y de texto en Azure Storage y cómo obtener acceso a los datos._

Azure Storage proporciona cuatro servicios de almacenamiento:

- Blob Storage. Un BLOB puede ser texto o datos binarios, como copias de seguridad, máquinas virtuales, archivos multimedia o documentos.
- Table Storage es un almacén de atributos de clave NoSQL.
- Queue Storage es un servicio de mensajería para el procesamiento de flujos de trabajo y la comunicación entre servicios en la nube.
- File Storage proporciona almacenamiento compartido mediante el protocolo SMB.

Existen dos tipos de cuentas de almacenamiento:

- Las cuentas de almacenamiento de uso general proporcionan acceso a Azure Storage Services desde una sola cuenta.
- Una cuenta de almacenamiento de blobs es una cuenta de almacenamiento especializada para almacenar BLOBs. Este tipo de cuenta se recomienda cuando solo necesita almacenar datos de blobs.

En este artículo y en la aplicación de ejemplo que lo acompaña, se muestra la carga de archivos de imagen y texto en el almacenamiento de blobs y su descarga. Además, también se muestra cómo recuperar una lista de archivos del almacenamiento de blobs y cómo eliminar archivos.

Para obtener más información sobre Azure Storage, consulte [Introducción al almacenamiento](https://azure.microsoft.com/documentation/articles/storage-introduction/).

> [!NOTE]
> Si no tiene una [suscripción a Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), cree una [cuenta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de empezar.

## <a name="introduction-to-blob-storage"></a>Introducción a Blob Storage

El almacenamiento de blobs consta de tres componentes, que se muestran en el diagrama siguiente:

![Conceptos de Blob Storage](azure-storage-images/blob-storage.png)

Todo acceso a Azure Storage se realiza a través de una cuenta de almacenamiento. Una cuenta de almacenamiento puede contener un número ilimitado de contenedores y un contenedor puede almacenar un número ilimitado de blobs, hasta el límite de capacidad de la cuenta de almacenamiento.

Un BLOB es un archivo de cualquier tipo y tamaño. Azure Storage admite tres tipos de BLOB diferentes:

- Los blobs en bloques están optimizados para la transmisión por secuencias y el almacenamiento de objetos en la nube, y son una buena opción para almacenar copias de seguridad, archivos multimedia, documentos, etc. Los blobs en bloques pueden tener un tamaño máximo de 195 GB.
- Los blobs en anexos son similares a los blobs en bloques, pero están optimizados para operaciones de anexión, como el registro. Los blobs en anexos pueden tener un tamaño máximo de 195 GB.
- Los blobs en páginas están optimizados para operaciones frecuentes de lectura y escritura y se suelen usar para almacenar máquinas virtuales y sus discos. Los blobs en páginas pueden tener un tamaño máximo de 1 TB.

> [!NOTE]
> Tenga en cuenta que las cuentas de almacenamiento de blobs admiten blobs en bloques y anexos, pero no blobs en páginas.

Un BLOB se carga en Azure Storage y se descarga de Azure Storage, como un flujo de bytes. Por lo tanto, los archivos se deben convertir en una secuencia de bytes antes de la carga y volver a convertirlos a su representación original después de la descarga.

Cada objeto almacenado en Azure Storage tiene una dirección URL única. El nombre de la cuenta de almacenamiento forma el subdominio de esa dirección y la combinación de subdominio y nombre de dominio forma un *punto de conexión* para la cuenta de almacenamiento. Por ejemplo, si la cuenta de almacenamiento se denomina *mystorageaccount* , el punto de conexión de BLOB predeterminado de la cuenta de almacenamiento es `https://mystorageaccount.blob.core.windows.net` .

La dirección URL para el acceso a un objeto en una cuenta de almacenamiento se crea anexando la ubicación del objeto en la cuenta de almacenamiento al extremo. Por ejemplo, una dirección de BLOB tendrá el formato `https://mystorageaccount.blob.core.windows.net/mycontainer/myblob` .

## <a name="setup"></a>Configuración

El proceso para integrar una cuenta de Azure Storage en una Xamarin.Forms aplicación de es el siguiente:

1. Cree una cuenta de almacenamiento. Para obtener más información, consulte [Creación de una cuenta de almacenamiento](/azure/storage/common/storage-account-create#create-a-storage-account).
1. Agregue la [biblioteca de cliente de Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) a la Xamarin.Forms aplicación.
1. Configure la cadena de conexión de almacenamiento. Para obtener más información, consulte [conexión a Azure Storage](#connecting-to-azure-storage).
1. Agregue `using` directivas para los `Microsoft.WindowsAzure.Storage` `Microsoft.WindowsAzure.Storage.Blob` espacios de nombres y a las clases que van a tener acceso a Azure Storage.

## <a name="connecting-to-azure-storage"></a>Conexión a Azure Storage

Se deben autenticar todas las solicitudes realizadas en los recursos de la cuenta de almacenamiento. Aunque los blobs se pueden configurar para admitir la autenticación anónima, hay dos enfoques principales que una aplicación puede usar para autenticarse con una cuenta de almacenamiento:

- Clave compartida. Este enfoque usa el nombre de cuenta de Azure Storage y la clave de cuenta para tener acceso a los servicios de almacenamiento. A una cuenta de almacenamiento se le asignan dos claves privadas en creación que se pueden usar para la autenticación de clave compartida.
- Firma de acceso compartido. Se trata de un token que se puede anexar a una dirección URL que permite el acceso delegado a un recurso de almacenamiento, con los permisos que especifica, durante el período de tiempo que sea válido.

Se pueden especificar cadenas de conexión que incluyan la información de autenticación necesaria para obtener acceso a Azure Storage recursos de una aplicación. Además, se puede configurar una cadena de conexión para conectarse al emulador de almacenamiento de Azure desde Visual Studio.

> [!NOTE]
> Azure Storage admite HTTP y HTTPS en una cadena de conexión. Sin embargo, se recomienda el uso de HTTPS.

### <a name="connecting-to-the-azure-storage-emulator"></a>Conexión al emulador de Azure Storage

El emulador de Azure Storage proporciona un entorno local que emula los servicios de blobs, colas y tablas de Azure para fines de desarrollo.

La siguiente cadena de conexión debe usarse para conectarse al emulador de almacenamiento de Azure:

```csharp
UseDevelopmentStorage=true
```

Para obtener más información sobre el emulador de Azure Storage, consulte [uso del emulador de Azure Storage para desarrollo y pruebas](/azure/storage/common/storage-use-emulator).

### <a name="connecting-to-azure-storage-using-a-shared-key"></a>Conexión a Azure Storage mediante una clave compartida

El siguiente formato de cadena de conexión se debe usar para conectarse a Azure Storage con una clave compartida:

```csharp
DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey
```

`myAccountName` debe reemplazarse por el nombre de la cuenta de almacenamiento y `myAccountKey` debe reemplazarse por una de las dos claves de acceso de cuenta.

> [!NOTE]
> Al usar la autenticación de clave compartida, el nombre de cuenta y la clave de cuenta se distribuirán a cada persona que use la aplicación, lo que proporcionará acceso completo de lectura y escritura a la cuenta de almacenamiento. Por lo tanto, use la autenticación de clave compartida solo con fines de prueba y no distribuya nunca claves a otros usuarios.

### <a name="connecting-to-azure-storage-using-a-shared-access-signature"></a>Conexión a Azure Storage mediante una firma de acceso compartido

El siguiente formato de cadena de conexión se debe usar para conectarse a Azure Storage con una SAS:

`BlobEndpoint=myBlobEndpoint;SharedAccessSignature=mySharedAccessSignature`

`myBlobEndpoint` debe reemplazarse por la dirección URL del punto de conexión del BLOB y `mySharedAccessSignature` debe reemplazarse por la SAS. La SAS proporciona el protocolo, el punto de conexión de servicio y las credenciales para tener acceso al recurso.

> [!NOTE]
> La autenticación de SAS se recomienda para las aplicaciones de producción. Sin embargo, en una aplicación de producción, la SAS se debe recuperar de un servicio back-end a petición, en lugar de agruparse con la aplicación.

Para obtener más información sobre las firmas de acceso compartido, consulte [uso de firmas de acceso compartido (SAS)](/azure/storage/common/storage-sas-overview).

## <a name="creating-a-container"></a>Creación de un contenedor

El `GetContainer` método se usa para recuperar una referencia a un contenedor con nombre, que se puede usar para recuperar los blobs del contenedor o para agregar blobs al contenedor. El siguiente ejemplo de código muestra el método `GetContainer`:

```csharp
static CloudBlobContainer GetContainer(ContainerType containerType)
{
  var account = CloudStorageAccount.Parse(Constants.StorageConnection);
  var client = account.CreateCloudBlobClient();
  return client.GetContainerReference(containerType.ToString().ToLower());
}
```

El `CloudStorageAccount.Parse` método analiza una cadena de conexión y devuelve una `CloudStorageAccount` instancia de que representa la cuenta de almacenamiento. `CloudBlobClient`Después, el método crea una instancia de, que se usa para recuperar contenedores y blobs `CreateCloudBlobClient` . El `GetContainerReference` método recupera el contenedor especificado como una `CloudBlobContainer` instancia, antes de que se devuelva al método de llamada. En este ejemplo, el nombre del contenedor es el `ContainerType` valor de enumeración, convertido en una cadena en minúsculas.

> [!NOTE]
> Los nombres de contenedor deben estar en minúsculas y deben empezar por una letra o un número. Además, solo pueden contener letras, números y el carácter de guión, y deben tener entre 3 y 63 caracteres de longitud.

El `GetContainer` método se invoca de la siguiente manera:

```csharp
var container = GetContainer(containerType);
```

La `CloudBlobContainer` instancia se puede usar para crear un contenedor si aún no existe:

```csharp
await container.CreateIfNotExistsAsync();
```

De forma predeterminada, un contenedor recién creado es privado. Esto significa que se debe especificar una clave de acceso de almacenamiento para recuperar los blobs del contenedor. Para obtener información acerca de cómo crear blobs dentro de un contenedor, consulte [crear un contenedor](/azure/storage/blobs/storage-quickstart-blobs-dotnet#create-a-container).

## <a name="uploading-data-to-a-container"></a>Cargar datos en un contenedor

El `UploadFileAsync` método se usa para cargar un flujo de datos de bytes en el almacenamiento de blobs y se muestra en el ejemplo de código siguiente:

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

Después de recuperar una referencia de contenedor, el método crea el contenedor si aún no existe. `Guid`A continuación, se crea un nuevo para que actúe como un nombre de BLOB único y se recupera una referencia de bloque de BLOB como una `CloudBlockBlob` instancia de. A continuación, la secuencia de datos se carga en el BLOB mediante el `UploadFromStreamAsync` método, que crea el BLOB si todavía no existe o lo sobrescribe si existe.

Antes de que un archivo se pueda cargar en el almacenamiento de blobs mediante este método, primero se debe convertir en una secuencia de bytes. Esto se muestra en el ejemplo de código siguiente:

```csharp
var byteData = Encoding.UTF8.GetBytes(text);
uploadedFilename = await AzureStorage.UploadFileAsync(ContainerType.Text, new MemoryStream(byteData));
```

Los `text` datos se convierten en una matriz de bytes, que se ajusta a continuación como una secuencia que se pasa al `UploadFileAsync` método.

## <a name="downloading-data-from-a-container"></a>Descargar datos de un contenedor

El `GetFileAsync` método se usa para descargar datos de BLOB de Azure Storage y se muestra en el ejemplo de código siguiente:

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

Después de recuperar una referencia de contenedor, el método recupera una referencia de BLOB para los datos almacenados. Si existe el BLOB, el método recupera sus propiedades `FetchAttributesAsync` . Se crea una matriz de bytes con el tamaño correcto y el BLOB se descarga como una matriz de bytes que se devuelve al método de llamada.

Después de descargar los datos de bytes BLOB, se debe convertir a su representación original. Esto se muestra en el ejemplo de código siguiente:

```csharp
var byteData = await AzureStorage.GetFileAsync(ContainerType.Text, uploadedFilename);
string text = Encoding.UTF8.GetString(byteData);
```

La matriz de bytes se recupera de Azure Storage por el `GetFileAsync` método, antes de que se vuelva a convertir en una cadena codificada UTF8.

## <a name="listing-data-in-a-container"></a>Enumerar los datos de un contenedor

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

Después de recuperar una referencia de contenedor, el método usa el método del contenedor `ListBlobsSegmentedAsync` para recuperar las referencias a los blobs dentro del contenedor. Los resultados devueltos por el `ListBlobsSegmentedAsync` método se enumeran mientras la `BlobContinuationToken` instancia no es `null` . Cada BLOB se convierte desde el objeto devuelto `IListBlobItem` a un objeto para `CloudBlockBlob` tener acceso a la `Name` propiedad del BLOB, antes de que se agregue el valor a la `allBlobsList` colección. Una vez que la `BlobContinuationToken` instancia es `null` , se devuelve el último nombre del BLOB y la ejecución sale del bucle.

## <a name="deleting-data-from-a-container"></a>Eliminar datos de un contenedor

El `DeleteFileAsync` método se usa para eliminar un BLOB de un contenedor y se muestra en el ejemplo de código siguiente:

```csharp
public static async Task<bool> DeleteFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);
  var blob = container.GetBlobReference(name);
  return await blob.DeleteIfExistsAsync();
}
```

Después de recuperar una referencia de contenedor, el método recupera una referencia de BLOB para el BLOB especificado. Después, el BLOB se elimina con el `DeleteIfExistsAsync` método.

## <a name="related-links"></a>Vínculos relacionados

- [Azure Storage (ejemplo)](/samples/xamarin/xamarin-forms-samples/webservices-azurestorage)
- [Introducción al almacenamiento](https://azure.microsoft.com/documentation/articles/storage-introduction/)
- [Uso de Blob Storage desde Xamarin](/azure/storage/blobs/storage-quickstart-blobs-xamarin)
- [Uso de firmas de acceso compartido (SAS)](/azure/storage/common/storage-sas-overview)
- [Azure Storage de Windows (NuGet)](https://www.nuget.org/packages/WindowsAzure.Storage/)