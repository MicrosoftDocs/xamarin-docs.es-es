---
title: 'Error IBTool: no se pudo completar la operación.'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A804EBC4-2BBF-4A98-A4E8-A455DB2E8A17
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: ddeffd2dd01553b5beb8bca7827603a416c3955b
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603027"
---
# <a name="ibtool-error-the-operation-couldnt-be-completed"></a>Error IBTool: no se pudo completar la operación.

## <a name="fixed-in-xcode-611"></a>Corregido en Xcode 6.1.1

Apple [corrigió](https://developer.apple.com/library/content/documentation/Xcode/Conceptual/RN-Xcode-Archive/Chapters/xc6_release_notes.html#//apple_ref/doc/uid/TP40016994-CH4-SW1) este `ibtool` error en Xcode 6.1.1, por lo que la solución más sencilla es actualizar a Xcode 6.1.1 o superior.

* * *

## <a name="description-of-the-problem"></a>Descripción del problema

El `ibtool` comando de Xcode 6,0 tenía un error en OS X 10,10 Yosemite. Xamarin. iOS usa Xcode `ibtool` para compilar guiones gráficos y `XIB` archivos.

Puede encontrar más información sobre el error en Xcode en el siguiente Stack Overflow post: [https://stackoverflow.com/questions/25754763/cant-open-storyboard](https://stackoverflow.com/questions/25754763/cant-open-storyboard)

### <a name="error-message"></a>Mensaje de error

> No se pudo abrir el documento "archivo mainstoryboard. Storyboard". The operation couldn’t be completed. (error com. Apple. InterfaceBuilder-1.)

## <a name="workarounds-for-xcode-60"></a>Soluciones alternativas (para Xcode 6,0)

### <a name="option-1-manage-all-uiimageviewimage-properties-in-code"></a>Opción 1: administrar todas `UIImageView.Image` las propiedades en el código

En lugar de establecer la `Image` propiedad de un `UIImageView` en el guión gráfico o el `.xib` archivo, puede establecer la propiedad en uno de los métodos de invalidación del ciclo de vida de la vista en el controlador de vista (por ejemplo, en `ViewDidLoad()` ). Vea también [trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md) para obtener sugerencias sobre el uso de `UIImage.FromBundle()` vs. `UIImage.FromFile()`

### <a name="option-2-move-all-of-the-image-resources-to-the-top-level-resources-folder"></a>Opción 2: trasladar todos los recursos de imagen a la carpeta de nivel superior `Resources`

Después de mover las imágenes a la carpeta de nivel superior, tendrá que `Resources` actualizar el guión gráfico y `.xib` los archivos para usar las nuevas rutas de acceso de imagen.

### <a name="option-3-set-the-logicalname-for-any-problematic-image-assets-so-they-are-copied-to-the-top-level-of-theapp-bundle"></a>Opción 3: establecer `LogicalName` para cualquier recurso de imagen problemático para que se copien en el nivel superior de la `.app` agrupación

Por ejemplo, suponga que el `.csproj` archivo original contiene la siguiente entrada:

`<BundleResource Include="Resources\Images\image.png" />`

Puede cambiar este elemento y agregar un `LogicalName` para que la imagen se copie en el nivel superior de la `.app` agrupación:

```xml
<BundleResource Include="Resources\Images\image.png">
    <LogicalName>image.png</LogicalName>
</BundleResource>
```

En Visual Studio para Mac, `LogicalName` también se puede establecer mediante el `Resource ID` campo de la imagen en la **vista > paneles > propiedades**. (Vea también: [https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545](https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545) )

Después de este cambio, tendrá que actualizar el guion gráfico y `.xib` los archivos para usar las nuevas rutas de acceso de imagen de nivel superior. En Visual Studio y Visual Studio para Mac, tendrá que editar la ruta de acceso de la `Image` propiedad a mano.

### <a name="next-steps"></a>Pasos siguientes

Para obtener más ayuda, para ponerse en contacto con nosotros o si este problema permanece incluso después de usar la información anterior, consulte [¿Qué opciones de soporte técnico están disponibles para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obtener información sobre las opciones de contacto, sugerencias y cómo archivar un nuevo error si es necesario. 
