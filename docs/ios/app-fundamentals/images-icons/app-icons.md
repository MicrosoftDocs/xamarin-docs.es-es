---
title: Iconos de aplicación en Xamarin. iOS
description: 'En este documento se describe cómo trabajar con varios iconos de aplicación en Xamarin. iOS: el icono de la aplicación, los iconos de Spotlight, los iconos de configuración y la ilustración de iTunes.'
ms.prod: xamarin
ms.assetid: B7791574-4A0F-4CB6-8C18-36D40B5C91EB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/22/2017
ms.openlocfilehash: 0f20e7dafaa855e3610595886537ee4910b09d2e
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643050"
---
# <a name="application-icons-in-xamarinios"></a>Iconos de aplicación en Xamarin. iOS

Los siguientes temas se tratarán en detalle:

* [Iconos de aplicación, información destacada y configuración](#icon-types) : los distintos tipos de iconos necesarios para una aplicación de iOS.
* [Administración de iconos con catálogos de recursos](#managing) : administración de iconos de aplicación mediante catálogos de recursos.
* [material gráfico de iTunes](#itunes) : proporcionar la ilustración de iTunes necesaria para el método ad hoc de entrega de la aplicación.

<a name="icon-types" />

## <a name="application-spotlight-and-settings-icons"></a>Iconos de aplicación, Spotlight y configuración

Del mismo modo que una aplicación de Xamarin. iOS puede usar recursos de imagen para los controles de interfaz de usuario y como iconos de documento, los recursos de imagen se pueden usar para proporcionar iconos de aplicación. Las siguientes capturas de pantallas de un iPad ilustran los tres usos de los iconos en iOS:

- **Icono de aplicación** : cada aplicación de iOS debe definir un icono de aplicación. Este es el icono que el usuario va a pulsar en la pantalla principal de iOS para iniciar la aplicación. Además, Game Center usa este icono, si procede. Ejemplo: 

    [![](app-icons-images/000.png "Icono de aplicación")](app-icons-images/000-full.png#lightbox)
- **Icono de Spotlight** : cada vez que el usuario escribe el nombre de una aplicación en una búsqueda de Spotlight, se muestra este icono. Ejemplo: 

    [![](app-icons-images/000a.png "Icono de Spotlight")](app-icons-images/000a-full.png#lightbox)
- **Icono de configuración** : Si el usuario escribe la aplicación de **configuración** en su dispositivo iOS, este icono se mostrará al final de la lista de **configuración** de la aplicación. Ejemplo: 

    [![](app-icons-images/000b.png "Icono de configuración")](app-icons-images/000b-full.png#lightbox)

Los siguientes tamaños de recurso de imagen y resoluciones serán necesarios para admitir todos los tipos de iconos requeridos por una aplicación de Xamarin. iOS dirigida a iOS 5 a iOS 9 (o superior):

### <a name="iphone-icon-sizes"></a>Tamaños de iconos de iPhone

- **iPhone: iOS 9 & 10 (iPhone 6 & 7 Plus)**

    ||3x|
    |---|---|
    |Icono de aplicación|180x180|
    |Principales|120x120|
    |Configuración|87x87|

- **iPhone: iOS 7 & 8**

    ||1x|2x|
    |---|---|---|
    |Icono de aplicación|60x60<sup>1</sup>|120x120|
    |Principales|40x40<sup>2</sup>|80x80|
    |Configuración|-|-|

- **iPhone: iOS 5 & 6**

    ||1x|2x|
    |---|---|---|
    |Icono de aplicación|57x57|114x114|
    |Principales|29x29|58x58|
    |Configuración|29x29<sup>3, 4</sup>|58x58<sup>3, 4</sup>|

### <a name="ipad-icon-sizes"></a>Tamaños de iconos de iPad

- **iPad: iOS 9 & 10**

    ||2x (iPad Pro)|
    |---|---|
    |Icono de aplicación|167x167<sup>6</sup>|
    |Principales|120x120<sup>6</sup>|
    |Configuración|58x58<sup>5</sup>|

- **iPad: iOS 7 & 8**

    ||1x|2x|
    |---|---|---|
    |Icono de aplicación|76x76|152x152|
    |Principales|40x40|80x80|
    |Configuración|-|-|

- **iPad: iOS 5 & 6**

    ||1x|2x|
    |---|---|---|
    |Icono de aplicación|72x72|144x144|
    |Principales|50x50|100x100|
    |Configuración|29x29<sup>3, 5</sup>|58x58<sup>3, 5</sup>|

 1. Tanto Visual Studio para Mac como Xcode ya no admiten la configuración de la imagen 1x para iOS 7.
 2. No se admite la configuración de una imagen 1x para iOS 7 al usar catálogos de recursos.
 3. iOS 7 & 8 usan los mismos tamaños de imagen que iOS 5 & 6.
 4. Utiliza las mismas imágenes y tamaños que el icono de Spotlight.
 5. Usa los mismos iconos de tamaño que el iPhone.
 6. Solo se admite con conjuntos de imágenes del catálogo de activos.
 
 Para obtener más información sobre los iconos, consulte la documentación de los [tamaños de imagen y icono](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconMatrix.html#//apple_ref/doc/uid/TP40006556-CH27-SW1) de Apple.

<a name="managing" />

## <a name="managing-icons-with-asset-catalogs"></a>Administrar iconos con catálogos de recursos

En el caso de los `AppIcon` iconos, se puede Agregar un conjunto `Assets.xcassets` de imágenes especial al archivo en el proyecto de la aplicación. Toda la versión de la imagen necesaria para admitir todas las resoluciones se incluye en _xcasset_ y se agrupa. Un editor especial en Visual Studio para Mac permite al desarrollador incluir y configurar estas imágenes de forma gráfica.

Para utilizar un catálogo de recursos, haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Haga doble clic en `Info.plist` el archivo en el **Explorador de soluciones** para abrirlo para su edición.
2. Desplácese hacia abajo hasta la sección iconos de la **aplicación** .
3. En la lista desplegable **origen** , asegúrese de que está seleccionado appicos: 

    ![](app-icons-images/migrate01.png "Asegurarse de que se ha seleccionado appicor")
4. En el **Explorador de soluciones**, haga doble clic en `Assets.xcassets` el archivo para abrirlo para su edición: 

    ![](app-icons-images/asset01.png "El archivo assets. xcassets en el Explorador de soluciones")
5. Seleccione `AppIcon` en la lista de recursos para `Icon Editor`Mostrar:

    ![](app-icons-images/asset02.png "El editor de appicos")
6. Haga clic en el tipo de icono dado y seleccione un archivo de imagen para el tipo o tamaño requerido, o bien arrastre en una imagen de una carpeta y suéltelo en el tamaño deseado.
7. Haga clic en el botón **abrir** para incluir la imagen en el proyecto y establecerla en xcasset.
8. Repita el procedimiento con todas las imágenes necesarias.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Haga doble clic en el archivo **info. plist** en el **Explorador de soluciones**:

    ![](app-icons-images/icon01w.png "Seleccionar info. plist")
2. Haga clic en la pestaña **activos visuales** y haga clic en el botón **usar catálogo de recursos** en iconos de la **aplicación**: 

    ![](app-icons-images/icon02w.png "Seleccione la pestaña activos visuales.")
3. En el **Explorador de soluciones**, expanda la carpeta del **Catálogo de recursos** : 

    ![](app-icons-images/image009.png "Expandir la carpeta del catálogo de activos")
4. Haga doble clic en el archivo **multimedia** para abrirlo en el editor: 

    ![](app-icons-images/image010.png "Abrir el archivo multimedia en el editor")
5. En el **Explorador de propiedades** , el desarrollador puede seleccionar los distintos tipos y tamaños de los iconos necesarios.
6. Haga clic en el tipo de icono dado y seleccione un archivo de imagen para el tipo o tamaño requerido.
7. Haga clic en el botón **abrir** para incluir la imagen en el proyecto y establecerla en xcasset.
8. Repita el procedimiento con todas las imágenes necesarias.

-----

Este es el método preferido de incluir y administrar los recursos de imagen que se usarán para proporcionar iconos de aplicación, de información destacada y de configuración para una aplicación.

### <a name="migrating-from-infoplist-to-asset-catalogs"></a>Migración desde info. plist a catálogos de recursos

En el caso de una aplicación de Xamarin. `Info.plist` iOS existente que use el archivo para administrar sus iconos, se recomienda encarecidamente que el desarrollador la cambie `AppIcons` para usar el recurso `Assets.xcassets`de imagen dentro de.

Haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Haga doble clic en `Info.plist` el archivo en el **Explorador de soluciones** para abrirlo para su edición.
2. Desplácese hacia abajo hasta la sección iconos de la **aplicación** .
3. En la lista desplegable **origen** , seleccione **migrar a catálogos de recursos**: 

    ![](app-icons-images/migrate02.png "Selección de migrar a catálogos de recursos")
4. Los iconos existentes definidos en el `Info.plist` archivo se migrarán a un `AppIcons` conjunto de imágenes agregado a `Assets.xcassets`: 

     ![](app-icons-images/migrate03.png "La imagen AppIcons establecida en assets. xcassets")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Haga doble clic en `Info.plist` el archivo en el **Explorador de soluciones** para abrirlo para su edición.
2. Haga clic en la sección iconos de iPhone: 

    ![](app-icons-images/image007.png "Editor de iconos de iPhone de oscuro")
3. Desplácese hacia abajo hasta la sección **iconos** .
4. En la lista desplegable **Catálogo de activos** , seleccione **usar catálogos de recursos**.
5. Los iconos existentes definidos en el `Info.plist` archivo se migrarán a un `Images` conjunto agregado a `Assets.xcassets`.
6. Guarde los cambios en el archivo `Info.plist`.

-----

<a name="itunes" />

## <a name="itunes-artwork"></a>Ilustraciones de iTunes

Si usa el método ad hoc de entrega de la aplicación (ya sea para usuarios corporativos o para pruebas beta en dispositivos reales), el desarrollador también debe incluir un 512 x 512 y una imagen de 1024x1024 que se usará para representar la aplicación en iTunes.

Para especificar las ilustraciones de iTunes, haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Haga doble clic en `Info.plist` el archivo en el **Explorador de soluciones** para abrirlo para su edición.
2. Desplácese a la sección de **material gráfico de iTunes** del editor: 

    ![](app-icons-images/itunes01.png "Desplácese a la sección de material gráfico de iTunes del editor")
3. En el caso de las imágenes que faltan, haga clic en la miniatura en el editor, seleccione el archivo de imagen para la ilustración de iTunes deseada en el cuadro de diálogo Abrir archivo y haga clic en el botón **Aceptar** .
4. Repita este paso hasta que se hayan especificado todas las imágenes necesarias para la aplicación.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Haga doble clic en `Info.plist` el archivo en el **Explorador de soluciones** para abrirlo para su edición.

2. Haga clic en la pestaña **activos visuales** y expanda la **ilustración iTunes**: 

    ![](app-icons-images/itunes01w.png "Edición de ilustraciones de iTunes en Visual Studio")
3. En el caso de las imágenes que faltan, haga clic en la miniatura en el editor, seleccione el archivo de imagen para la ilustración de iTunes deseada en el cuadro de diálogo Abrir archivo y haga clic en el botón **abrir** .
4. Repita este paso hasta que se hayan especificado todas las imágenes necesarias para la aplicación.

-----

## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con imágenes (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Instrucciones personalizadas para la creación de iconos e imágenes](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html))
