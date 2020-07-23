---
title: PhotoKit en Xamarin. iOS
description: En este documento se describe PhotoKit, se analizan los objetos de modelo, se consultan los datos del modelo y se guardan los cambios en la biblioteca de fotos.
ms.prod: xamarin
ms.assetid: 7FDEE394-3787-40FA-8372-76A05BF184B3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: 4fe58dbc02da47d5791afbbfe0132fd21e774b63
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939794"
---
# <a name="photokit-in-xamarinios"></a>PhotoKit en Xamarin. iOS

[![Descargar ejemplo ](~/media/shared/download.png) descargar un ejemplo de código](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-samplephotoapp/)

PhotoKit es un marco que permite que las aplicaciones consulten la biblioteca de imágenes del sistema y creen interfaces de usuario personalizadas para ver y modificar su contenido. Incluye varias clases que representan recursos de imagen y vídeo, así como colecciones de recursos como álbumes y carpetas.

## <a name="permissions"></a>Permisos

Para que la aplicación pueda acceder a la biblioteca de fotos, se mostrará un cuadro de diálogo de permisos al usuario. Debe proporcionar texto explicativo en el archivo **info. plist** para explicar el modo en que la aplicación usa la biblioteca de fotos, por ejemplo:

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>Applies filters to photos and updates the original image</string>
```

## <a name="model-objects"></a>Objetos de modelo

PhotoKit representa estos recursos en lo que llama a los objetos de modelo. Los objetos de modelo que representan las fotos y los vídeos son del tipo `PHAsset` . Un `PHAsset` contiene metadatos como el tipo de medio del recurso y su fecha de creación.
Del mismo modo, las `PHAssetCollection` `PHCollectionList` clases y contienen metadatos sobre colecciones de recursos y listas de colecciones, respectivamente. Las colecciones de recursos son grupos de activos, como todas las fotografías y vídeos de un año determinado. Del mismo modo, las listas de colecciones son grupos de recopilaciones de recursos, como fotos y vídeos agrupados por año.

## <a name="querying-model-data"></a>Consultar datos del modelo

PhotoKit facilita la consulta de datos del modelo mediante una variedad de métodos de captura. Por ejemplo, para recuperar todas las imágenes, llamaría a `PHAsset.Fetch` , pasando el `PHAssetMediaType.Image` tipo de medio.

```csharp
PHFetchResult fetchResults = PHAsset.FetchAssets (PHAssetMediaType.Image, null);
```

La `PHFetchResult` instancia contendría entonces todas las `PHAsset` instancias que representan imágenes. Para obtener las imágenes, se usa `PHImageManager` (o la versión de almacenamiento en caché `PHCachingImageManager` ) para realizar una solicitud de la imagen mediante una llamada a `RequestImageForAsset` . Por ejemplo, el código siguiente recupera una imagen para cada recurso de `PHFetchResult` que se va a mostrar en una celda de vista de colección:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    var imageCell = (ImageCell)collectionView.DequeueReusableCell (cellId, indexPath);
    imageMgr.RequestImageForAsset (
        (PHAsset)fetchResults [(uint)indexPath.Item],
        thumbnailSize,
        PHImageContentMode.AspectFill, new PHImageRequestOptions (),
        (img, info) => {
            imageCell.ImageView.Image = img;
        }
    );
    return imageCell;
}
```

Esto da como resultado una cuadrícula de imágenes como se muestra a continuación:

![La aplicación en ejecución que muestra una cuadrícula de imágenes](photokit-images/image4.png)

## <a name="saving-changes-to-the-photo-library"></a>Guardar cambios en la biblioteca de fotos

Aquí se explica cómo controlar la consulta y la lectura de datos. También puede volver a escribir los cambios en la biblioteca. Dado que varias aplicaciones interesadas pueden interactuar con la biblioteca de fotos del sistema, puede registrar un observador para recibir notificaciones de los cambios mediante `PhotoLibraryObserver` . A continuación, cuando se entren los cambios, la aplicación puede actualizarse en consecuencia. Por ejemplo, esta es una implementación sencilla para volver a cargar la vista de Colección anterior:

```csharp
class PhotoLibraryObserver : PHPhotoLibraryChangeObserver
{
    readonly PhotosViewController controller;
    public PhotoLibraryObserver (PhotosViewController controller)

    {
        this.controller = controller;
    }

    public override void PhotoLibraryDidChange (PHChange changeInstance)
    {
        DispatchQueue.MainQueue.DispatchAsync (() => {
            var changes = changeInstance.GetFetchResultChangeDetails (controller.fetchResults);
            controller.fetchResults = changes.FetchResultAfterChanges;
            controller.CollectionView.ReloadData ();
        });
    }
}
```

Para volver a escribir los cambios de la aplicación, debe crear una solicitud de cambio. Cada una de las clases de modelo tiene una clase de solicitud de cambio asociada. Por ejemplo, para cambiar `PHAsset` , se crea un `PHAssetChangeRequest` . Los pasos para realizar cambios que se escriben de nuevo en la biblioteca de fotos y se envían a los observadores como el anterior son:

1. Realice la operación de edición.
2. Guarde los datos de la imagen filtrada en una `PHContentEditingOutput` instancia de.
3. Realice una solicitud de cambio para publicar los cambios de la salida de edición.

Este es un ejemplo en el que se escribe un cambio en una imagen que aplica un filtro Noir de imagen principal:

```csharp
void ApplyNoirFilter (object sender, EventArgs e)
{
    Asset.RequestContentEditingInput (new PHContentEditingInputRequestOptions (), (input, options) => {

        // perform the editing operation, which applies a noir filter in this case
        var image = CIImage.FromUrl (input.FullSizeImageUrl);
        image = image.CreateWithOrientation((CIImageOrientation)input.FullSizeImageOrientation);
        var noir = new CIPhotoEffectNoir {
            Image = image
        };
        var ciContext = CIContext.FromOptions (null);
        var output = noir.OutputImage;
        var uiImage = UIImage.FromImage (ciContext.CreateCGImage (output, output.Extent));
        imageView.Image = uiImage;
        //
        // save the filtered image data to a PHContentEditingOutput instance
        var editingOutput = new PHContentEditingOutput(input);
        var adjustmentData = new PHAdjustmentData();
        var data = uiImage.AsJPEG();
        NSError error;
        data.Save(editingOutput.RenderedContentUrl, false, out error);
        editingOutput.AdjustmentData = adjustmentData;
        //
        // make a change request to publish the changes form the editing output
        PHPhotoLibrary.GetSharedPhotoLibrary.PerformChanges (() => {
            PHAssetChangeRequest request = PHAssetChangeRequest.ChangeRequest(Asset);
            request.ContentEditingOutput = editingOutput;
        },
        (ok, err) => Console.WriteLine ("photo updated successfully: {0}", ok));
    });
}
```

Cuando el usuario selecciona el botón, se aplica el filtro:

![Dos ejemplos en los que se muestra la foto antes y después de aplicar el filtro](photokit-images/image5.png)

Y gracias a `PHPhotoLibraryChangeObserver` , el cambio se refleja en la vista de colección cuando el usuario se desplaza hacia atrás:

![Vista de colección fotográfica que muestra la foto modificada](photokit-images/image6.png)
