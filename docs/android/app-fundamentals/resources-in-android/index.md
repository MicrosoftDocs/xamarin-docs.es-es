---
title: Recursos de Android
description: En este artículo se presenta el concepto de recursos de Android en Xamarin. Android y se documenta cómo usarlos. En él se explica cómo usar los recursos de la aplicación de Android para admitir la localización de aplicaciones y varios dispositivos, incluidos los tamaños y las densidades de pantalla variables.
ms.prod: xamarin
ms.assetid: C0DCC856-FA36-04CD-443F-68D26075649E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/01/2018
ms.openlocfilehash: 1ff4d9d896aaa5f290402a49aa4b4bd1f1e00aaf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025055"
---
# <a name="android-resources"></a>Recursos de Android

_En este artículo se presenta el concepto de recursos de Android en Xamarin. Android y se documenta cómo usarlos. En él se explica cómo usar los recursos de la aplicación de Android para admitir la localización de aplicaciones y varios dispositivos, incluidos los tamaños y las densidades de pantalla variables._

## <a name="overview"></a>Información general

Una aplicación Android rara vez es solo código fuente. A menudo hay muchos otros archivos que componen una aplicación: vídeo, imágenes, fuentes y archivos de audio simplemente por nombrar algunos. Colectivamente, estos archivos de código no fuente se conocen como recursos y se compilan (junto con el código fuente) durante el proceso de compilación y se empaquetan como un APK para su distribución e instalación en los dispositivos:

![Diagrama de empaquetado](images/packaging-diagram.png)

Los recursos ofrecen varias ventajas a una aplicación de Android:

- **La separación de código** &ndash; separa el código fuente de imágenes, cadenas, menús, animaciones, colores, etc. Como tales recursos pueden ayudar considerablemente al localizar.

- El **destino de varios dispositivos** &ndash; proporciona compatibilidad más sencilla con diferentes configuraciones de dispositivo sin cambios de código.

- La **comprobación en tiempo de compilación** &ndash; recursos son estáticos y se compilan en la aplicación. Esto permite comprobar el uso de los recursos en tiempo de compilación, cuando será fácil detectar y corregir los errores, en lugar de en tiempo de ejecución cuando sea más difícil de localizar y de corregir.

Cuando se inicia un nuevo proyecto de Xamarin. Android, se crea un directorio especial denominado recursos, junto con algunos subdirectorios:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Contenido y carpeta de recursos](images/resources-folder-vs.png)

En la imagen anterior, los recursos de la aplicación se organizan según su tipo en estos subdirectorios: las imágenes Irán en el directorio de **Drawing** . las vistas van en el subdirectorio de **diseño** , etc.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Contenido y carpeta de recursos](images/resources-folder-xs.png)

En la imagen anterior, los recursos de la aplicación se organizan según su tipo en estos subdirectorios: las imágenes Irán en el directorio de **mipmap** ; las vistas van en el subdirectorio de **diseño** , etc.

-----

Hay dos maneras de tener acceso a estos recursos en una aplicación de Xamarin. Android: *mediante programación* en código y de forma *declarativa* en XML con una sintaxis XML especial.

Estos recursos se denominan *recursos predeterminados* y se usan en todos los dispositivos a menos que se especifique una coincidencia más específica. Además, cada tipo de recurso puede tener opcionalmente *recursos alternativos* que Android puede usar para dirigirse a dispositivos específicos. Por ejemplo, se pueden proporcionar recursos para el destino de la configuración regional del usuario, el tamaño de la pantalla o si el dispositivo gira 90 grados de vertical a horizontal, etc. En cada uno de estos casos, Android cargará los recursos para su uso por parte de la aplicación sin ningún esfuerzo de codificación adicional por parte del desarrollador.

Los recursos alternativos se especifican agregando una cadena corta, denominada *calificador*, al final del directorio que contiene un tipo de recursos determinado.

Por ejemplo, **Resources/drawable-de** especificará las imágenes para los dispositivos que están configurados en una configuración regional alemana, mientras que **Resources-fr** puede contener imágenes para dispositivos establecidos en una configuración regional en francés. Un ejemplo de cómo proporcionar recursos alternativos puede verse en la imagen siguiente, donde se ejecuta la misma aplicación con solo la configuración regional del dispositivo que cambia:

![Pantallas de ejemplo para diferentes configuraciones regionales](images/localized-screenshots.png)

En este artículo se describe el uso de los recursos y se tratan los temas siguientes:

- Los **aspectos básicos** de los recursos de Android &ndash; usar los recursos predeterminados mediante programación y mediante declaración, agregando tipos de recursos, como imágenes y fuentes, a una aplicación.

- Las **configuraciones específicas de dispositivo** &ndash; admiten las diferentes resoluciones de pantalla y densidades de una aplicación.

- La **localización** &ndash; uso de recursos para admitir las distintas regiones que puede usar una aplicación.

## <a name="related-links"></a>Vínculos relacionados

- [Uso de recursos de Android](~/android/app-fundamentals/resources-in-android/android-assets.md)
- [Principios de la aplicación](https://developer.android.com/guide/topics/fundamentals.html)
- [Recursos de la aplicación](https://developer.android.com/guide/topics/resources/index.html)
- [Compatibilidad con varias pantallas](https://developer.android.com/guide/practices/screens_support.html)
