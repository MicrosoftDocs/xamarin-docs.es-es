---
title: Mostrar una imagen en Xamarin. iOS
description: En este artículo se describe cómo incluir un recurso de imagen en una aplicación de Xamarin. iOS y cómo C# Mostrar esa imagen mediante código o mediante su asignación a un control en el diseñador de iOS.
ms.prod: xamarin
ms.assetid: 60288B12-49E3-4E87-8690-D04A5EC7A664
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/24/2018
ms.openlocfilehash: 8082fc6ab2625e68d64ee9d42a9b630f885d9002
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227753"
---
# <a name="displaying-an-image-in-xamarinios"></a>Mostrar una imagen en Xamarin. iOS

_En este artículo se describe cómo incluir un recurso de imagen en una aplicación de Xamarin. iOS y cómo C# Mostrar esa imagen mediante código o mediante su asignación a un control en el diseñador de iOS._

## <a name="adding-and-organizing-images-in-a-xamarinios-app"></a>Adición y organización de imágenes en una aplicación de Xamarin. iOS

Cuando se agrega una imagen para su uso en una aplicación de Xamarin. iOS, el desarrollador usará un _Catálogo de recursos_ para admitir todos los dispositivos iOS y la resolución requerida por una aplicación.

Agregado en iOS 7, los **conjuntos de imágenes** de catálogos de recursos contienen todas las versiones o representaciones de una imagen que son necesarias para admitir varios dispositivos y factores de escala para una aplicación. En lugar de confiar en el nombre de archivo de recursos de imagen, los **conjuntos de imágenes** usan un archivo JSON para especificar la imagen a la que pertenece cada dispositivo o resolución. Esta es la manera preferida de administrar y admitir imágenes en iOS (desde iOS 9 o superior).

## <a name="adding-images-to-an-asset-catalog-image-set"></a>Agregar imágenes a un conjunto de imágenes del catálogo de recursos

Como se indicó anteriormente, un **conjunto de imágenes** de catálogos de recursos contiene todas las versiones o representaciones de una imagen que son necesarias para admitir varios dispositivos y factores de escala para una aplicación. En lugar de confiar en el nombre de archivo de recursos de imagen, los **conjuntos de imágenes** usan un archivo JSON para especificar la imagen a la que pertenece cada dispositivo o resolución.

Para crear un nuevo conjunto de imágenes y agregarle imágenes, haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. En el **Explorador de soluciones**, haga doble clic en `Assets.xcassets` el archivo para abrirlo para su edición:

    ![](displaying-an-image-images/imageset01.png "Assets. xcassets en el Explorador de soluciones")
2. Haga clic con el botón derecho en la **lista activos** y seleccione **nuevo conjunto de imágenes**:

    ![](displaying-an-image-images/imageset02.png "Agregar un nuevo conjunto de imágenes")
3. Seleccione el nuevo conjunto de imágenes y se mostrará el editor:

    ![](displaying-an-image-images/imageset03.png "El editor de conjunto de imágenes")
4. Desde aquí, arrastre las imágenes para cada uno de los diferentes dispositivos y resoluciones necesarios.
5. Haga doble clic en el **nombre** del nuevo conjunto de imágenes en la **lista de recursos** para editarlo: ![](displaying-an-image-images/imageset04.png "Editar el nombre del nuevo conjunto de imágenes")

Al usar un **conjunto de imágenes** en el diseñador de iOS, simplemente seleccione el nombre del conjunto en la lista desplegable del editor de propiedades:

![](displaying-an-image-images/imageset06.png "Seleccione el nombre de un conjunto de imágenes en la lista desplegable.")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Abra el catálogo de recursos desde el **Explorador de soluciones**y, en la esquina superior izquierda, haga clic en el botón de **signo más** :

    ![](displaying-an-image-images/asset5.png "Haga clic en el botón de signo más")

2. Seleccione **Agregar conjunto de imágenes** y se mostrará el editor de conjunto de imágenes para el nuevo conjunto de imágenes. Desde aquí, arrastre las imágenes para cada uno de los diferentes dispositivos y resoluciones necesarios.

    ![](displaying-an-image-images/asset7.png "El editor de conjunto de imágenes")

### <a name="renaming-an-image-set"></a>Cambiar el nombre de un conjunto de imágenes

Para cambiar el nombre de un conjunto de imágenes, haga lo siguiente:

1. En el **Explorador de soluciones**, haga doble clic en el archivo de **Catálogo de recursos** para abrirlo para su edición:

    ![](displaying-an-image-images/rename01.png "El catálogo de recursos en el Explorador de soluciones")
2. Seleccione el **conjunto de imágenes** para cambiar el nombre:

    ![](displaying-an-image-images/rename02.png "Seleccione el conjunto de imágenes para cambiar el nombre")
3. En el **Explorador de propiedades**, desplácese hasta la parte inferior y seleccione **nombre**(en la sección **varios** ):

    ![](displaying-an-image-images/rename03.png "Seleccione nombre en la sección varios.")
4. Escriba un nuevo **nombre** para el **conjunto de imágenes** y guarde los cambios.

-----

Al usar un **conjunto de imágenes** en el código, haga referencia a él por `FromBundle` su nombre llamando `UIImage` al método de la clase. Por ejemplo:

```csharp
MonkeyImage.Image = UIImage.FromBundle ("PurpleMonkey");
```

> [!IMPORTANT]
> Si las imágenes asignadas a un conjunto de imágenes no se muestran correctamente, asegúrese de que se está usando el nombre de `FromBundle` archivo correcto con el método (el **conjunto de imágenes** y no el nombre del **Catálogo de activos** primario). En el caso de las `.png` imágenes PNG, se puede omitir la extensión. Para otros formatos de imagen, se requiere la extensión (por ejemplo, `PurpleMonkey.jpg`).

### <a name="using-vector-images-in-asset-catalogs"></a>Usar imágenes vectoriales en catálogos de recursos

A partir de iOS 8, se ha agregado la clase de **Vector** especial a los **conjuntos de imágenes** que permiten al desarrollador incluir una imagen de vector con formato **PDF** en la casete en lugar de incluir archivos de mapa de bits individuales en las diferentes resoluciones. Con este método, proporcione un archivo de vector único para `@1x` la resolución (con formato de archivo PDF vectorial) y `@2x` las `@3x` versiones y del archivo se generarán en tiempo de compilación y se incluirán en el paquete de la aplicación.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![](displaying-an-image-images/imageset05.png "Imágenes vectoriales en el editor de catálogos de recursos")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](displaying-an-image-images/asset8.png "Imágenes vectoriales en el editor de catálogos de recursos")

-----

Por ejemplo, si el desarrollador incluye un `MonkeyIcon.pdf` archivo como vector de un catálogo de recursos con una resolución de 150 PX x 150 PX, los siguientes recursos de mapa de bits se incluirán en el lote de aplicaciones final cuando se compiló:

- `MonkeyIcon@1x.png`-150 PX x 150 PX resolución.
- `MonkeyIcon@2x.png`-300 PX x 300 PX resolución.
- `MonkeyIcon@3x.png`-450px x 450px resolución.

Se deben tener en cuenta las siguientes consideraciones al usar imágenes vectoriales de PDF en catálogos de recursos:

- Esto no es totalmente compatible con vectores, ya que el PDF se rasterizará en un mapa de bits en tiempo de compilación y los mapas de bits incluidos en la aplicación final.
- No se puede ajustar el tamaño de la imagen una vez que se ha establecido en el catálogo de recursos. Si el desarrollador intenta cambiar el tamaño de la imagen (ya sea en el código o mediante las clases de diseño automático y tamaño), la imagen se distorsionará como cualquier otro mapa de bits.
- Los catálogos de recursos solo son compatibles con iOS 7 y versiones posteriores, si una aplicación necesita admitir iOS 6 o inferior, no puede usar los catálogos de recursos.

## <a name="working-with-template-images"></a>Trabajar con imágenes de plantilla

En función del diseño de una aplicación de iOS, puede haber ocasiones en las que el desarrollador necesite personalizar un icono o una imagen dentro de la interfaz de usuario para que coincida con un cambio de la combinación de colores (por ejemplo, según las preferencias del usuario).

Para lograr este efecto fácilmente, cambie el _modo de representación_ del recurso de imagen a **imagen de plantilla**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](displaying-an-image-images/templateimage01.png "Modo de representación establecido en imagen de plantilla")](displaying-an-image-images/templateimage01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](displaying-an-image-images/templateimage01vs.png "Modo de representación establecido en plantilla")](displaying-an-image-images/templateimage01vs.png#lightbox)

-----

En el diseñador de iOS, asigne el recurso de imagen a un control de IU y, a continuación, establezca el **matiz** para colorear la imagen:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](displaying-an-image-images/templateimage03.png "Establecer el matiz para colorear la imagen")](displaying-an-image-images/templateimage03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](displaying-an-image-images/templateimage03vs.png "Establecer el matiz para colorear la imagen")](displaying-an-image-images/templateimage03vs.png#lightbox)

-----

Opcionalmente, el recurso de imagen y el matiz se pueden establecer directamente en el código:

```csharp
MyIcon.Image = UIImage.FromBundle ("MessageIcon");
MyIcon.TintColor = UIColor.Red;
```

Para usar una imagen de plantilla completamente desde el código, haga lo siguiente:

```csharp
if (MyIcon.Image != null) {
    var mutableImage = MyIcon.Image.ImageWithRenderingMode (UIImageRenderingMode.AlwaysTemplate);
    MyIcon.Image = mutableImage;
    MyIcon.TintColor = UIColor.Red;
}
```

Dado que `RenderMode` la propiedad `UIImage` de es de solo lectura, utilice `ImageWithRenderingMode` el método para crear una nueva instancia de la imagen con la configuración de modo de representación deseada.

Hay tres valores posibles para `UIImage.RenderMode` a través de la `UIImageRenderingMode` enumeración:

- `AlwaysOriginal`: Fuerza la representación de la imagen como el archivo de imagen de origen original sin cambios.
- `AlwaysTemplate`: Fuerza la representación de la imagen como una imagen de plantilla al colorear los píxeles con el color `Tint` especificado.
- `Automatic`: Representa la imagen como una plantilla o original según el entorno en el que se usa. Por ejemplo, si la imagen se `UIToolBar`usa en `UITabBar` , `UINavigationBar`o `UISegmentControl` se tratará como una plantilla.

## <a name="adding-new-assets-collections"></a>Agregar nuevas colecciones de activos

Al trabajar con imágenes en catálogos de activos, puede haber ocasiones en las que se necesitará una nueva colección, en lugar de agregar todas las imágenes de la `Assets.xcassets` aplicación a la colección. Por ejemplo, al diseñar recursos a petición.

Para agregar un nuevo catálogo de activos al proyecto:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Haga clic con el botón derecho en el **nombre del proyecto** en el **Explorador de soluciones** y seleccione **Agregar** > **nuevo archivo..** .
2. Seleccione**Catálogo de recursos**de **iOS** > , escriba un **nombre** para la colección y haga clic en el botón **nuevo** :

    ![](displaying-an-image-images/asset01.png "Creación de un nuevo catálogo de activos")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. En el Explorador de soluciones, haga clic con el botón derecho en la carpeta de catálogos de **recursos** y seleccione **Agregar > nuevo catálogo de recursos**.
2. Asígnele un nombre y haga clic en **Agregar**:

    ![](displaying-an-image-images/asset1.png "Creación de un nuevo catálogo de activos")

-----

Desde aquí, la colección puede trabajar de la misma manera que la colección predeterminada `Assets.xcassets` incluida automáticamente en el proyecto.

## <a name="using-images-with-controls"></a>Usar imágenes con controles

Además de usar imágenes para admitir una aplicación, iOS también usa imágenes con tipos de control de aplicaciones como barras de tabulación, barras de herramientas, barras de navegación, tablas y botones. Una manera sencilla de hacer que una imagen aparezca en un control es asignar una `UIImage` instancia a la propiedad del `Image` control.

### <a name="frombundle"></a>FromBundle

La `FromBundle` llamada al método es una llamada sincrónica (bloqueo) que tiene una serie de características de carga y administración de imágenes integradas, como la compatibilidad con el almacenamiento en caché y el control automático de archivos de imagen para distintas resoluciones.

`UITabBarItem` En el ejemplo siguiente se muestra cómo establecer la imagen de en un `UITabBar`:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

Suponiendo que `MyImage` es el nombre de un recurso de imagen agregado a un catálogo de recursos anterior. Al trabajar con imágenes del catálogo de recursos, solo tiene que especificar el nombre del `FromBundle` conjunto de imágenes en el método para las imágenes con formato **PNG** :

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

Para cualquier otro formato de imagen, incluya la extensión con el nombre. Por ejemplo:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage.jpg");
```

Para obtener más información sobre los iconos y las imágenes, consulte la documentación de Apple sobre el [icono personalizado y las directrices de creación de imágenes](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html).

## <a name="displaying-an-image-in-a-storyboards"></a>Mostrar una imagen en guiones gráficos

Una vez que se ha agregado una imagen a un proyecto de Xamarin. iOS mediante un catálogo de recursos, se puede mostrar fácilmente en un guion `UIImageView` gráfico mediante un en el diseñador de iOS. Por ejemplo, si se ha agregado el siguiente recurso de imagen:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![](displaying-an-image-images/display01.png "Se ha agregado un recurso de imagen de ejemplo")

Haga lo siguiente para mostrarlo en un guion gráfico:

1. Haga doble clic en `Main.storyboard` el archivo en el **Explorador de soluciones** para abrirlo y editarlo en el diseñador de iOS.
2. Seleccione una **vista de imagen** en el **cuadro de herramientas**:

     ![](displaying-an-image-images/display02.png "Seleccionar una vista de imagen en el cuadro de herramientas")
3. Arrastre la vista imagen a la superficie de diseño y colóquela y cambie su tamaño según sea necesario:

    ![](displaying-an-image-images/display03.png "Nueva vista de imagen en el Superficie de diseño")
4. En la sección **Widget** del **Explorador de propiedades** , seleccione el recurso de **imagen** que desee mostrar:

    ![](displaying-an-image-images/display04.png "Seleccione el recurso de imagen que desee mostrar")
5. En la sección **vista** , use el **modo** para controlar cómo se cambiará el tamaño de la imagen cuando se cambie el tamaño de la **vista de imagen** .
6. Con la **vista imagen** seleccionada, haga clic de nuevo para agregar **restricciones**:

    ![](displaying-an-image-images/display05.png "Agregar restricciones")
7. Arrastre el controlador en forma de "T" de cada borde de la **vista de imagen** al lado correspondiente de la pantalla para "anclar" la imagen a los lados. De esta manera, la **vista de imagen** se reducirá y crecerá cuando se cambie el tamaño de la pantalla.
8. Guarde los cambios en el guion gráfico.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](displaying-an-image-images/display01vs.png "Se ha agregado un recurso de imagen de ejemplo")

Haga lo siguiente para mostrarlo en un guion gráfico:

1. Haga doble clic en `Main.storyboard` el archivo en el **Explorador de soluciones** para abrirlo y editarlo en el diseñador de iOS.
2. Seleccione una **vista de imagen** en el **cuadro de herramientas**:

     ![](displaying-an-image-images/display02vs.png "Seleccionar una vista de imagen en el cuadro de herramientas")
3. Arrastre la vista imagen a la superficie de diseño y colóquela y cambie su tamaño según sea necesario:

    ![](displaying-an-image-images/display03vs.png "Nueva vista de imagen en el Superficie de diseño")
4. En la sección **Widget** del **Explorador de propiedades** , seleccione el recurso de **imagen** que desee mostrar:

    ![](displaying-an-image-images/display04vs.png "Seleccione el recurso de imagen que desee mostrar")
5. En la sección **vista** , use el **modo** para controlar cómo se cambiará el tamaño de la imagen cuando se cambie el tamaño de la **vista de imagen** .
6. Con la **vista imagen** seleccionada, haga clic de nuevo para agregar **restricciones**:

    ![](displaying-an-image-images/display05vs.png "Agregar restricciones")
7. Arrastre el controlador en forma de "T" de cada borde de la **vista de imagen** al lado correspondiente de la pantalla para "anclar" la imagen a los lados. De esta manera, la **vista de imagen** se reducirá y crecerá cuando se cambie el tamaño de la pantalla.
8. Guarde los cambios en el guion gráfico.

-----

## <a name="displaying-an-image-in-code"></a>Mostrar una imagen en el código

Al igual que cuando se muestra una imagen en un guión gráfico, una vez que se ha agregado una imagen a un proyecto de Xamarin. iOS mediante un catálogo de recursos C# , se puede mostrar fácilmente mediante el código.

Considere el ejemplo siguiente:

```csharp
// Create an image view that will fill the
// parent image view and set the image from
// an Image Asset

var imageView = new UIImageView (View.Frame);
imageView.Image = UIImage.FromBundle ("Kemah");

// Add the Image View to the parent view
View.AddSubview (imageView);
```

Este código crea un nuevo `UIImageView` y le asigna un tamaño y una posición iniciales. A continuación, carga la imagen desde un recurso de imagen agregado al proyecto y agrega `UIImageView` el al elemento `UIView` primario para mostrarlo.

## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con imágenes (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Tamaño y resolución de la imagen (Apple)](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/image-size-and-resolution/)
