---
title: Icono para aplicaciones Xamarin.Mac
description: En este artículo se describe la creación de las imágenes necesarias para el icono de la aplicación de Xamarin.Mac, el empaquetado de las imágenes en un archivo .icns y la inclusión del icono en el proyecto de Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 675b9405-d9a7-49f0-94ad-417f10a71d11
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: d0acd44561b220507aafda7af05cd2862f6c9009
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73021700"
---
# <a name="application-icon-for-xamarinmac-apps"></a>Icono para aplicaciones Xamarin.Mac

_En este artículo se describe la creación de las imágenes necesarias para el icono de la aplicación de Xamarin.Mac, el empaquetado de las imágenes en un archivo .icns y la inclusión del icono en el proyecto de Xamarin.Mac._

## <a name="overview"></a>Información general

Cuando se trabaja con C# y .NET en una aplicación de Xamarin.Mac, el desarrollador tiene acceso a las mismas herramientas de imagen e icono que un desarrollador que trabaje en *Objective-C* y *Xcode*.

Un buen icono debe transmitir el propósito principal de la aplicación de Xamarin.Mac y sugerir la experiencia que cabe esperar al usar la aplicación. En este artículo se incluyen todos los pasos necesarios para crear los activos de imagen necesarios para un icono, empaquetar los activos en un archivo `AppIcon.appiconset` y usar el archivo en una aplicación de Xamarin.Mac.

![El editor de AppIcon.appiconset](app-icon-images/intro01.png "El editor de AppIcon.appiconset")

## <a name="application-icon"></a>Icono de aplicación

Un buen icono debe transmitir el propósito principal de la aplicación de Xamarin.Mac y sugerir la experiencia que cabe esperar al usar la aplicación. Todas las aplicaciones macOS deben incluir varios tamaños del icono para mostrarlo en el Finder, Dock, Launchpad y otras ubicaciones del equipo.

## <a name="designing-the-icon"></a>Diseñar el icono

Apple sugiere lo siguiente para diseñar un icono de aplicación:

- Dele al icono una forma realista y única.
- Si la aplicación macOS tiene un equivalente en iOS, no reutilice el icono de la aplicación de iOS.
- Use imágenes universales que las personas puedan reconocer fácilmente.
- Esfuércese por alcanzar la simplicidad.
- Use con moderación el color y las sombras para conseguir que el icono represente adecuadamente la aplicación.
- Evite sugerir texto mezclando texto real con frases de tipo _lorem ipsum_.
- Cree una versión idealizada del tema del icono en vez de usar una foto real.
- Evite usar elementos de la interfaz de usuario de macOS en los iconos.
- No use en los iconos réplicas de iconos de Apple.

Consulte las secciones [App Icon Gallery](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/Gallery.html#//apple_ref/doc/uid/20000957-CH88-SW1) (Galería de iconos de aplicación) y [Designing App Icons](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/Designing.html#//apple_ref/doc/uid/20000957-CH87-SW1) (Diseñar iconos de aplicación) de [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X) de Apple antes de diseñar un icono de aplicación de Xamarin.Mac.

## <a name="required-image-sizes-and-filenames"></a>Tamaños de imagen y nombres de archivo necesarios

Al igual que sucede con cualquier otro recurso de imagen que el desarrollador vaya a usar en una aplicación de Xamarin.Mac, el icono de aplicación debe proporcionar una versión para la resolución Estándar y Retina. De nuevo, al igual que sucede con cualquier otra imagen, debe usar un formato `@2x` al asignar un nombre a los archivos de icono:

- **Resolución estándar**  - _nombreDeImagen_ **.** _extensión_de_nombre_de_archivo_ (ejemplo: **icon_512x512.png**)
- **Alta resolución**  - _nombreDeImagen_ **@2x.** _extensión_de_nombre_de_archivo_ (ejemplo: **icon_512x512@2x.png** )

Por ejemplo, para proporcionar la versión de 512 x 512 del icono de aplicación, el archivo debería denominarse **icon_512x512.png** y **icon_512x512@2x.png** .

Para asegurarse de que el icono tenga un aspecto inmejorable en todos los lugares en los que lo vean los usuarios, proporcione los recursos en los tamaños que se muestran a continuación:

|Filename|Tamaño en píxeles|
|---|---|
|icon_512x512@2x.png|1024 x 1024|
|icon_512x512.png|512 x 512|
|icon_256x256@2x.png|512 x 512|
|icon_256x256.png|256 x 256|
|icon_128x128@2x.png|256 x 256|
|icon_128x128.png|128 x 128|
|icon_32x32@2x.png|64 x 64|
|icon_32x32.png|32 x 32|
|icon_16x16@2x.png|32 x 32|
|icon_16x16.png|16 x 16|

Para obtener más información, consulte la documentación de Apple titulada [Provide High-Resolution Versions of All App Graphics Resources](https://developer.apple.com/library/mac/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Optimizing/Optimizing.html#//apple_ref/doc/uid/TP40012302-CH7-SW3) (Proporcionar versiones de alta resolución de todos los recursos gráficos de la aplicación).

## <a name="packaging-the-icon-resources"></a>Empaquetar los recursos de icono

Una vez que se ha diseñado el icono y se ha guardado con los nombres y los tamaños de archivo necesarios, Visual Studio para Mac facilita su asignación a los activos de imagen para su uso en Xamarin.Mac.

Haga lo siguiente:

1. En el **Panel de solución**, abra **Assets.xcassets** > **AppIcons.appiconset**: 

    ![Edición de AppIcon.appiconset](app-icon-images/intro01.png "Edición de AppIcon.appiconset")
2. Para cada tamaño de icono necesario, haga clic en el icono y seleccione el archivo de imagen correspondiente que se creó anteriormente: 

    [![Selección de una imagen de icono](app-icon-images/intro02.png "Selección de una imagen de icono")](app-icon-images/intro02-large.png#lightbox)
3. Guarde los cambios.

## <a name="using-the-icon"></a>Usar el icono

Una vez que se haya compilado el archivo `AppIcon.appiconset`, deberá asignarlo al proyecto de Xamarin.Mac en Visual Studio para Mac.

Haga lo siguiente:

1. Haga doble clic en **Info.plist** en el **Panel de solución** para abrir las **Opciones de proyecto**.
2. En la sección **Mac OS X Application Target** (Destino de la aplicación de Mac OS X), haga clic en **App Icons** (Iconos de aplicación) para seleccionar el archivo `AppIcon.appiconset`: 

    [![Establecimiento del conjunto de iconos](app-icon-images/icon01.png "Establecimiento del conjunto de iconos")](app-icon-images/icon01-large.png#lightbox)
3. Guarde los cambios.

Cuando se ejecute la aplicación, el icono nuevo se mostrará en el Dock:

![Ejemplo de un icono de aplicación en el Dock de macOS](app-icon-images/icon04.png "Ejemplo de un icono de aplicación en el Dock de macOS")

## <a name="summary"></a>Resumen

En este artículo se ha descrito en detalle cómo se trabaja con las imágenes necesarias para crear un icono de aplicación macOS, cómo se empaqueta el icono y cómo se incluye en un proyecto de Xamarin.Mac.

## <a name="related-links"></a>Vínculos relacionados

- [MacImages (ejemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/macimages)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Working with Images (Trabajo con imágenes)](~/mac/app-fundamentals/image.md)
- [macOS Human Interface Guidelines - Icons and Images](https://developer.apple.com/macos/human-interface-guidelines/icons-and-images/image-size-and-resolution/) (Directrices de interfaz humana de macOS: iconos e imágenes)
- [Acerca de la alta resolución para OS X](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html)
- [Generador de iconos](https://itunes.apple.com/us/app/icns-builder/id554660130?mt=12)
