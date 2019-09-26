---
title: Controles de imagen de watchos en Xamarin
description: En este documento se describe cómo usar los controles de imagen en una aplicación de watchos compilada con Xamarin. Describe el control WKInterfaceImage, el método SetImage, la adición de imágenes a una extensión de inspección, animaciones, etc.
ms.prod: xamarin
ms.assetid: B741C207-3427-46F3-9C90-A52BF8933FA4
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: f9367eda7651ca61a8a3cb0928ad11cb320faab6
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "70769963"
---
# <a name="watchos-image-controls-in-xamarin"></a>Controles de imagen de watchos en Xamarin

watchOS proporciona un [`WKInterfaceImage`](xref:WatchKit.WKInterfaceImage) control para mostrar imágenes y animaciones simples. Algunos controles también pueden tener una imagen de fondo (como botones, grupos y controladores de interfaz).

![](image-images/image-walkway.png "Imagen que muestra de Apple Watch") ![](image-images/image-animation.png "de Apple Watch con una animación simple")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

Use imágenes del catálogo de recursos para agregar imágenes a las aplicaciones del kit de inspección.
Solo **@2x** versiones son necesarias, ya que todos ver dispositivos tienen pantallas de Retina.

![](image-images/asset-universal-sml.png "Solo se necesitan 2x versiones, ya que todos los dispositivos de inspección tienen pantallas retinas")

Es recomendable asegurarse de que las imágenes son el tamaño correcto de la pantalla de inspección. *Evite* el uso de imágenes de tamaño incorrecto (especialmente grandes) y el escalado para mostrarlas en el reloj.

Puede usar los tamaños del kit de inspección (38mm y 42mm) en una imagen del catálogo de recursos para especificar distintas imágenes para cada tamaño de presentación.

![](image-images/asset-watch-sml.png "Puede usar los tamaños del kit de inspección 38mm y 42mm en una imagen del catálogo de recursos para especificar diferentes imágenes para cada tamaño de presentación.")

## <a name="images-on-the-watch"></a>Imágenes en el reloj

La forma más eficaz de mostrar imágenes es *incluirlas en el proyecto de la aplicación de inspección* y mostrarlas `SetImage(string imageName)` mediante el método.

Por ejemplo, el ejemplo [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog/) tiene varias imágenes agregadas a un catálogo de recursos en el proyecto de la aplicación Watch:

![](image-images/asset-whale-sml.png "El ejemplo WatchKitCatalog tiene varias imágenes agregadas a un catálogo de recursos en el proyecto de la aplicación Watch.")

Se pueden cargar y mostrar eficazmente en el reloj `SetImage` con el parámetro de nombre de cadena:

```csharp
myImageControl.SetImage("Whale");
myOtherImageControl.SetImage("Worry");
```

### <a name="background-images"></a>Imágenes de fondo

La misma lógica se aplica a `SetBackgroundImage (string imageName)` en las `Button`clases `Group`, y `InterfaceController` . El mejor rendimiento se consigue mediante el almacenamiento de las imágenes en la propia aplicación de inspección.

## <a name="images-in-the-watch-extension"></a>Imágenes en la extensión de inspección

Además de cargar imágenes que se almacenan en la propia aplicación de inspección, puede enviar imágenes desde el paquete de extensión a la aplicación de inspección para su presentación (o puede descargar imágenes de una ubicación remota y mostrarlas).

Para cargar imágenes desde la extensión de inspección, `UIImage` Cree instancias y, `SetImage` a continuación `UIImage` , llame a con el objeto.

Por ejemplo, el ejemplo [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) tiene una imagen denominada **Bumblebee** en el proyecto de extensión Watch:

![](image-images/asset-bumblebee-sml.png "El ejemplo WatchKitCatalog tiene una imagen denominada Bumblebee en el proyecto de extensión Watch")

El siguiente código dará como resultado:

- la imagen que se carga en la memoria y
- se muestra en el reloj.

```csharp
using (var image = UIImage.FromBundle ("Bumblebee")) {
    myImageControl.SetImage (image);
}
```

## <a name="animations"></a>Animaciones

Para animar un conjunto de imágenes, todas deben comenzar con el mismo prefijo y tener un sufijo numérico.

El ejemplo [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) tiene una serie de imágenes numeradas en el proyecto de la aplicación Watch con el prefijo de **bus** :

![](image-images/asset-bus-animation-sml.png "El ejemplo WatchKitCatalog tiene una serie de imágenes numeradas en el proyecto de la aplicación Watch con el prefijo de bus")

Para mostrar estas imágenes como una animación, primero cargue la imagen `SetImage` con con el nombre del prefijo y, a continuación, llame `StartAnimating`a:

```csharp
animatedImage.SetImage ("Bus");
animatedImage.StartAnimating ();
```

Llame `StopAnimating` a en el control de imagen para detener el bucle de animación:

```csharp
animatedImage.StopAnimating ();
```

<a name="cache" />

## <a name="appendix-caching-images-watchos-1"></a>Apéndice: Almacenamiento en caché de imágenes (watchos 1)

> [!IMPORTANT]
> las aplicaciones de watchos 3 se ejecutan completamente en el dispositivo. La siguiente información es solo para las aplicaciones de watchos 1.

Si la aplicación usa repetidamente una imagen almacenada en la extensión (o se ha descargado), es posible almacenar en caché la imagen en el almacenamiento del reloj para aumentar el rendimiento de las pantallas siguientes.

Use el `WKInterfaceDevice`método `AddCachedImage` s para transferir la imagen al reloj y, a continuación, `SetImage` use con el parámetro de nombre de imagen como una cadena para mostrarla:

```csharp
var device = WKInterfaceDevice.CurrentDevice;
using (var image = UIImage.FromBundle ("Bumblebee")) {
    if (!device.AddCachedImage (image, "Bumblebee")) {
            Console.WriteLine ("Image cache full.");
        } else {
            cachedImage.SetImage ("Bumblebee");
        }
    }
}
```

Puede consultar el contenido de la memoria caché de imágenes en el `WKInterfaceDevice.CurrentDevice.WeakCachedImages`código mediante.

### <a name="managing-the-cache"></a>Administrar la memoria caché

La memoria caché de unos 20 MB de tamaño. Se mantiene a lo largo de los reinicios de la aplicación y, cuando se llena, es responsabilidad suya borrar los `RemoveCachedImage` archivos `RemoveAllCachedImages` mediante métodos o `WKInterfaceDevice.CurrentDevice` en el objeto.

## <a name="related-links"></a>Vínculos relacionados

- [WatchKitCatalog (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Documento de imagen de Apple](https://developer.apple.com/documentation/watchkit/wkinterfaceimage)
