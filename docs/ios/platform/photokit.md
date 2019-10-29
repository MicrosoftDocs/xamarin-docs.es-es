---
title: PhotoKit en Xamarin. iOS
description: En este documento se describe PhotoKit, se analizan los objetos de modelo, se consultan los datos del modelo y se guardan los cambios en la biblioteca de fotos.
ms.prod: xamarin
ms.assetid: 7FDEE394-3787-40FA-8372-76A05BF184B3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: 82cff753e7569c2642c467db692c2d2d84347df0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031618"
---
# <a name="photokit-in-xamarinios"></a>PhotoKit en Xamarin. iOS

PhotoKit es un nuevo marco que permite que las aplicaciones consulten la biblioteca de imágenes del sistema y creen interfaces de usuario personalizadas para ver y modificar su contenido. Incluye varias clases que representan recursos de imagen y vídeo, así como colecciones de recursos como álbumes y carpetas.

## <a name="model-objects"></a>Objetos de modelo

PhotoKit representa estos recursos en lo que llama a los objetos de modelo. Los objetos de modelo que representan las fotos y los vídeos son del tipo `PHAsset`. Un `PHAsset` contiene metadatos, como el tipo de medio del recurso y su fecha de creación.
Del mismo modo, las clases `PHAssetCollection` y `PHCollectionList` contienen metadatos sobre colecciones de recursos y listas de colecciones, respectivamente. Las colecciones de recursos son grupos de activos, como todas las fotografías y vídeos de un año determinado. Del mismo modo, las listas de colecciones son grupos de recopilaciones de recursos, como fotos y vídeos agrupados por año.

## <a name="querying-model-data"></a>Consultar datos del modelo

PhotoKit facilita la consulta de datos del modelo mediante una variedad de métodos de captura. Por ejemplo, para recuperar todas las imágenes, llamaría a `PHAsset.Fetch`, pasando el tipo de medio `PHAssetMediaType.Image`.

```csharp
PHFetchResult fetchResults = PHAsset.FetchAssets (PHAssetMediaType.Image, null);
```

A continuación, la instancia de `PHFetchResult` contendría todas las instancias de `PHAsset` que representan imágenes. Para obtener las imágenes, use el `PHImageManager` (o la versión de almacenamiento en caché, `PHCachingImageManager`) para realizar una solicitud de la imagen mediante una llamada a `RequestImageForAsset`. Por ejemplo, el código siguiente recupera una imagen para cada recurso de un `PHFetchResult` que se va a mostrar en una celda de vista de colección:

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

![](photokit-images/image4.png "The running app displaying a grid of images")

## <a name="saving-changes-to-the-photo-library"></a>Guardar cambios en la biblioteca de fotos

Aquí se explica cómo controlar la consulta y la lectura de datos. También puede volver a escribir los cambios en la biblioteca. Dado que varias aplicaciones interesadas pueden interactuar con la biblioteca de fotos del sistema, puede registrar un observador para recibir notificaciones de los cambios mediante un PhotoLibraryObserver. A continuación, cuando se entren los cambios, la aplicación puede actualizarse en consecuencia. Por ejemplo, esta es una implementación sencilla para volver a cargar la vista de Colección anterior:

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

Para volver a escribir los cambios de la aplicación, debe crear una solicitud de cambio. Cada una de las clases de modelo tiene una clase de solicitud de cambio asociada. Por ejemplo, para cambiar un PHAsset, se crea una PHAssetChangeRequest. Los pasos para realizar cambios que se escriben de nuevo en la biblioteca de fotos y se envían a los observadores como el anterior son:

- Realice la operación de edición.
- Guarde los datos de la imagen filtrada en una instancia de PHContentEditingOutput.
- Realice una solicitud de cambio para publicar los cambios de la salida de edición.

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

![](photokit-images/image5.png "An example of the filter being applied")

Y gracias a PHPhotoLibraryChangeObserver, el cambio se refleja en la vista de colección cuando el usuario se desplaza hacia atrás:

![](photokit-images/image6.png "The change is reflected in the collection view when the user navigates back")
