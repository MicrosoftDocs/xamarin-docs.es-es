---
title: Mostrar imágenes con Xamarin. iOS
description: En este documento se describe cómo mostrar imágenes en Xamarin. iOS. Trata la adición de imágenes a una aplicación mediante programación o a través del diseñador de iOS.
ms.prod: xamarin
ms.assetid: 67CA8DB6-769D-42BB-A137-3AF933789FE1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/13/2018
ms.openlocfilehash: fb51d0d04c86274d43e23e4912ed16765b7cdc8b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657530"
---
# <a name="displaying-images-with-xamarinios"></a>Mostrar imágenes con Xamarin. iOS

Agregar imágenes a la aplicación requiere dos pasos: en primer lugar, agregue las imágenes al proyecto; a continuación, agregue controles y código para mostrarlos en una pantalla. Consulte el artículo sobre [Cómo trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md) para obtener una cobertura más detallada del control de imágenes en Xamarin. iOS.

## <a name="adding-images-to-your-app"></a>Agregar imágenes a la aplicación

Las imágenes se pueden agregar a cualquier carpeta de la solución de Visual Studio para Mac y, si la **acción de compilación** se establece en **contenido** , el archivo se incluirá en la aplicación y se puede mostrar.

Visual Studio para Mac también admite un directorio especial denominado **recursos** que también puede contener archivos de imagen. Los archivos de la carpeta recursos deben tener la **acción de compilación** establecida en **BundleResource**.

En esta captura de pantalla se muestran las opciones de **acción de compilación** que aparecen cuando se hace clic con el botón derecho en un archivo:

 [![](image-images/image30a.png "Menú de acción de compilación")](image-images/image30a.png#lightbox)

Visual Studio para Mac normalmente elegirá automáticamente la **acción de compilación** correcta, pero debe tener en cuenta esta configuración, especialmente si mueve archivos en el proyecto.

### <a name="adding-an-image-file"></a>Agregar un archivo de imagen

Para agregar un archivo de imagen al proyecto, haga clic con el botón derecho en el proyecto y elija **Agregar archivos..** .

 [![](image-images/image31a.png "Agregar archivos... MENU")](image-images/image31a.png#lightbox)

Seleccione la imagen (o imágenes) que desee incluir en el cuadro de diálogo de archivo estándar. La acción de compilación predeterminada para las imágenes será **BundleResource** : no Invalide este valor a menos que tenga un motivo concreto.

 [![](image-images/image32a.png "Cuadro de diálogo Agregar archivos")](image-images/image32a.png#lightbox)

La imagen se agregará al proyecto y estará disponible para cargarse y mostrarse en el código. En esta captura de pantalla se muestra una imagen agregada a un proyecto de aplicación de iOS:

 [![](image-images/image33a.png "Imagen en el proyecto")](image-images/image33a.png#lightbox)

### <a name="what-is-the-resources-directory"></a>¿Qué es el directorio de recursos?

Los archivos colocados en el directorio de **recursos** se tratan de forma diferente a los archivos normales: el contenido de la carpeta de **recursos** se copia en la raíz de la aplicación y se puede hacer referencia a él desde allí en el código. Esto puede ser útil por muchos motivos:

-  Almacenar las imágenes configuradas en las propiedades de la aplicación, como las imágenes de inicio predeterminadas y los iconos de la aplicación.
-  Almacenar otras imágenes y archivos por separado del código, por lo que son más fáciles de administrar (los subdirectorios se conservan cuando se copia el contenido del directorio de recursos).


El directorio de **recursos** es especialmente útil en un proyecto de biblioteca, ya que el código puede suponer que esas imágenes se copiarán en la raíz de la aplicación de consumo, lo que facilita la escritura de bibliotecas de código compartidas que requieren imagen, sonido, vídeo, XML o Otros archivos.

El directorio de **recursos** debe tener el mismo nombre y todos los archivos deben tener la acción de compilación establecida en **BundleResource**.

## <a name="displaying-the-image"></a>Mostrar la imagen

En el diseñador de iOS, use una **vista de imagen** para mostrar una imagen o una serie animada de imágenes. A continuación se muestra el icono de **vista de imagen** del cuadro de herramientas:

 [![](image-images/image35a.png "ImageView en el cuadro de herramientas")](image-images/image35.png#lightbox)

Arrastre la **vista imagen** desde el **cuadro de herramientas** hasta el controlador de vista. A continuación, en la **vista imagen > imagen** , la lista desplegable proporcionará una lista de todos los archivos de imagen disponibles en el proyecto. Seleccione cualquiera de ellas para agregarla a la vista de imagen.

 [![](image-images/image36a.png "ImageView en el cuadro de herramientas")](image-images/image36.png#lightbox)

### <a name="displaying-the-image-programmatically"></a>Mostrar la imagen mediante programación

Dado que **SF Monkey. jpg** se encuentra en la raíz del directorio de **recursos** , estará disponible en tiempo de ejecución en la raíz del lote de aplicaciones. Para mostrar esta imagen en un control de vista de imagen, use el código siguiente:

```csharp
imageview1.Image = UIImage.FromBundle("SF Monkey.png");
```

Si hubiéramos colocado la imagen en **/Resources/pics/SF Monkey. jpg**, el código incluiría la carpeta **pics** en la ruta de acceso:

```csharp
imageview1.Image = UIImage.FromBundle("Pics/SF Monkey.png");
```

No es necesario que las referencias del archivo de recursos incluyan la carpeta Resources.

## <a name="related-links"></a>Vínculos relacionados

- [Controles (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
