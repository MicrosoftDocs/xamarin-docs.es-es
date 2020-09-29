---
title: Iconos de aplicación en Xamarin. iOS
description: 'En este documento se describe cómo trabajar con varios iconos de aplicación en Xamarin. iOS: el icono de la aplicación, los iconos de Spotlight, los iconos de configuración y la ilustración de iTunes.'
ms.prod: xamarin
ms.assetid: B7791574-4A0F-4CB6-8C18-36D40B5C91EB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/22/2017
ms.openlocfilehash: 8bf0df03419d3705d72b679cfbfca427b0ac1a93
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435643"
---
# <a name="application-icons-in-xamarinios"></a>Iconos de aplicación en Xamarin. iOS

Los siguientes temas se tratarán en detalle:

- [Iconos de aplicación, información destacada y configuración](#icon-types) : los distintos tipos de iconos necesarios para una aplicación de iOS.
- [Administración de iconos con catálogos de recursos](#managing) : administración de iconos de aplicación mediante catálogos de recursos.
- [material gráfico de iTunes](#itunes) : proporcionar la ilustración de iTunes necesaria para el método ad hoc de entrega de la aplicación.

<a name="icon-types"></a>

## <a name="application-spotlight-and-settings-icons"></a>Iconos de aplicación, Spotlight y configuración

Del mismo modo que una aplicación de Xamarin. iOS puede usar recursos de imagen para los controles de interfaz de usuario y como iconos de documento, los recursos de imagen se pueden usar para proporcionar iconos de aplicación. Las siguientes capturas de pantallas de un iPad ilustran los tres usos de los iconos en iOS:

- **Icono de aplicación** : cada aplicación de iOS debe definir un icono de aplicación. Este es el icono que el usuario va a pulsar en la pantalla principal de iOS para iniciar la aplicación. Además, Game Center usa este icono, si procede. Ejemplo:

    [![Icono de aplicación](app-icons-images/000.png)](app-icons-images/000-full.png#lightbox)
- **Icono de Spotlight** : cada vez que el usuario escribe el nombre de una aplicación en una búsqueda de Spotlight, se muestra este icono. Ejemplo:

    [![Icono de Spotlight](app-icons-images/000a.png)](app-icons-images/000a-full.png#lightbox)
- **Icono de configuración** : Si el usuario escribe la aplicación de **configuración** en su dispositivo iOS, este icono se mostrará al final de la lista de **configuración** de la aplicación. Ejemplo:

    [![Icono de configuración](app-icons-images/000b.png)](app-icons-images/000b-full.png#lightbox)

Los siguientes tamaños de recurso de imagen y resoluciones serán necesarios para admitir todos los tipos de iconos requeridos por una aplicación de Xamarin. iOS dirigida a iOS 5 a iOS 9 (o superior):

### <a name="iphone-icon-sizes"></a>Tamaños de iconos de iPhone

- **iPhone: iOS 9 & 10 (iPhone 6 & 7 Plus)**

    |Icono|3x|
    |---|---|
    |Icono de aplicación|180x180|
    |Destacados|120x120|
    |Configuración|87x87|

- **iPhone: iOS 7 & 8**

    |Icono|1x|2x|
    |---|---|---|
    |Icono de aplicación|60 x 60<sup>1</sup>|120x120|
    |Destacados|40 x 40<sup>2</sup>|80x80|
    |Configuración|-|-|

- **iPhone: iOS 5 & 6**

    |Icono|1x|2x|
    |---|---|---|
    |Icono de aplicación|57x57|114x114|
    |Destacados|29x29|58x58|
    |Configuración|29x29<sup>3, 4</sup>|58x58<sup>3, 4</sup>|

### <a name="ipad-icon-sizes"></a>Tamaños de iconos de iPad

- **iPad: iOS 9 & 10**

    |Icono|2x (iPad Pro)|
    |---|---|
    |Icono de aplicación|167x167<sup>6</sup>|
    |Destacados|120x120<sup>6</sup>|
    |Configuración|58x58<sup>5</sup>|

- **iPad: iOS 7 & 8**

    |Icono|1x|2x|
    |---|---|---|
    |Icono de aplicación|76x76|152 x 152|
    |Destacados|40x40|80x80|
    |Configuración|-|-|

- **iPad: iOS 5 & 6**

    |Icono|1x|2x|
    |---|---|---|
    |Icono de aplicación|72x72|144x144|
    |Destacados|50x50|100x100|
    |Configuración|29x29<sup>3, 5</sup>|58x58<sup>3, 5</sup>|

 1. Tanto Visual Studio para Mac como Xcode ya no admiten la configuración de la imagen 1x para iOS 7.
 2. No se admite la configuración de una imagen 1x para iOS 7 al usar catálogos de recursos.
 3. iOS 7 & 8 usan los mismos tamaños de imagen que iOS 5 & 6.
 4. Utiliza las mismas imágenes y tamaños que el icono de Spotlight.
 5. Usa los mismos iconos de tamaño que el iPhone.
 6. Solo se admite con conjuntos de imágenes del catálogo de activos.

 Para obtener más información sobre los iconos, consulte la documentación de los [tamaños de imagen y icono](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconMatrix.html#//apple_ref/doc/uid/TP40006556-CH27-SW1) de Apple.

<a name="managing"></a>

## <a name="managing-icons-with-asset-catalogs"></a>Administrar iconos con catálogos de recursos

En el caso de los iconos, `AppIcon` se puede Agregar un conjunto de imágenes especial al `Assets.xcassets` archivo en el proyecto de la aplicación. Toda la versión de la imagen necesaria para admitir todas las resoluciones se incluye en _xcasset_ y se agrupa. Un editor especial en Visual Studio para Mac permite al desarrollador incluir y configurar estas imágenes de forma gráfica.

Para usar un catálogo de recursos, siga estos pasos:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Haga doble clic en el `Info.plist` archivo en el **Explorador de soluciones** para abrirlo para su edición.
2. Desplácese hacia abajo hasta la sección **iconos de iPhone** .
3. Haga clic en el botón **migrar a catálogo de recursos** :

    ![Asegurarse de que se ha seleccionado appicor](app-icons-images/migrate01.png)

4. En el **Explorador de soluciones**, haga doble clic en el `Assets.xcassets` archivo para abrirlo para su edición:

    ![El archivo assets. xcassets en el Explorador de soluciones](app-icons-images/asset01.png)

5. Seleccione `AppIcon` en la lista de recursos para mostrar `Icon Editor` :

    ![El editor de appicos](app-icons-images/asset02.png)

6. Haga clic en el tipo de icono dado y seleccione un archivo de imagen para el tipo o tamaño requerido, o bien arrastre en una imagen de una carpeta y suéltelo en el tamaño deseado.
7. Haga clic en el botón **abrir** para incluir la imagen en el proyecto y establecerla en xcasset.
8. Repita el procedimiento con todas las imágenes necesarias.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Haga doble clic en * * info.  * * archivo en el **Explorador de soluciones**:

    ![Seleccionar info. plist](app-icons-images/icon01w.png)

2. Haga clic en la pestaña **activos visuales** y haga clic en el botón **usar catálogo de recursos** en iconos de la **aplicación**:

    ![Seleccione la pestaña activos visuales.](app-icons-images/icon02w.png)

    Si no hay ningún botón, pero en lugar de una lista desplegable, ya se ha agregado un catálogo de recursos a este proyecto.

3. En el **Explorador de soluciones**, expanda la carpeta del **Catálogo de recursos** :

    ![Expandir la carpeta del catálogo de activos](app-icons-images/image009.png)

4. Haga doble clic en el archivo **multimedia** para abrirlo en el editor:

    ![Abrir el archivo multimedia en el editor](app-icons-images/image010.png)

5. En el **Explorador de propiedades** , el desarrollador puede seleccionar los distintos tipos y tamaños de los iconos necesarios.
6. Haga clic en el tipo de icono dado y seleccione un archivo de imagen para el tipo o tamaño requerido.
7. Haga clic en el botón **abrir** para incluir la imagen en el proyecto y establecerla en xcasset.
8. Repita el procedimiento con todas las imágenes necesarias.

-----

Este es el método preferido de incluir y administrar los recursos de imagen que se usarán para proporcionar iconos de aplicación, de información destacada y de configuración para una aplicación.

<a name="itunes"></a>

## <a name="itunes-artwork"></a>Ilustraciones de iTunes

Si usa el método ad hoc de entrega de la aplicación (ya sea para usuarios corporativos o para pruebas beta en dispositivos reales), el desarrollador también debe incluir un 512 x 512 y una imagen de 1024x1024 que se usará para representar la aplicación en iTunes.

Para especificar las ilustraciones de iTunes, haga lo siguiente:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Haga doble clic en el `Info.plist` archivo en el **Explorador de soluciones** para abrirlo para su edición.
2. Desplácese a la sección de **material gráfico de iTunes** del editor:

    ![Desplácese a la sección de material gráfico de iTunes del editor](app-icons-images/itunes01.png)
3. En el caso de las imágenes que faltan, haga clic en la miniatura en el editor, seleccione el archivo de imagen para la ilustración de iTunes deseada en el cuadro de diálogo Abrir archivo y haga clic en el botón **Aceptar** .
4. Repita este paso hasta que se hayan especificado todas las imágenes necesarias para la aplicación.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Haga doble clic en el `Info.plist` archivo en el **Explorador de soluciones** para abrirlo para su edición.

2. Haga clic en la pestaña **activos visuales** y expanda la **ilustración iTunes**:

    ![Edición de ilustraciones de iTunes en Visual Studio](app-icons-images/itunes01w.png)
3. En el caso de las imágenes que faltan, haga clic en la miniatura en el editor, seleccione el archivo de imagen para la ilustración de iTunes deseada en el cuadro de diálogo Abrir archivo y haga clic en el botón **abrir** .
4. Repita este paso hasta que se hayan especificado todas las imágenes necesarias para la aplicación.

-----

## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con imágenes (ejemplo)](/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Instrucciones personalizadas para la creación de iconos e imágenes](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html))