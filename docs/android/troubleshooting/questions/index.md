---
title: Preguntas más frecuentes sobre Xamarin. Android
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: c14c03d4f618644382aa80b5e0e7fc5b7a46fa9b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760875"
---
# <a name="android-frequently-asked-questions"></a>Preguntas más frecuentes sobre Android

## <a name="installation--setup"></a>Instalación & instalación

### <a name="which-android-sdk-packages-should-i-installinstall-android-sdk-packagesmd"></a>[¿Qué paquetes de Android SDK debo instalar?](install-android-sdk-packages.md)

La instalación del Android SDK no incluye automáticamente todos los paquetes mínimos necesarios para el desarrollo. Aunque las necesidades individuales de los desarrolladores varían, en esta guía se describen los paquetes que normalmente se necesitarán para el desarrollo con Xamarin. Android.

### <a name="where-can-i-set-my-android-sdk-locationsandroid-sdk-locationmd"></a>[¿Dónde puedo configurar mi ubicaciones de Android SDK?](android-sdk-location.md)

En esta guía se describe la configuración predeterminada del Android SDK, que debe funcionar en la mayoría de las configuraciones. y cómo cambiar estos valores predeterminados en Visual Studio para Mac o Visual Studio si es necesario.

### <a name="how-do-i-update-the-java-development-kit-jdk-versionupdate-jdkmd"></a>[¿Cómo se puede actualizar la versión del kit de desarrollo de Java (JDK)?](update-jdk.md)

En este artículo se muestra cómo actualizar la versión del kit de desarrollo de Java (JDK) en Windows y Mac.

### <a name="can-i-use-java-development-kit-jdk-version-9-or-laterjdk9-errorsmd"></a>[¿Puedo usar el kit de desarrollo de Java (JDK) versión 9 o posterior?](jdk9-errors.md)

Xamarin. Android requiere JDK 8 o Microsoft Mobile OpenJDK. En este artículo se enumeran algunos mensajes de error comunes que puede ver si está instalado JDK 9 o posterior, junto con las instrucciones para comprobar la versión de JDK.

### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packagesinstall-android-support-librarymd"></a>[¿Cómo instalar manualmente las bibliotecas de compatibilidad de Android que requieren los paquetes de Xamarin.Android.Support?](install-android-support-library.md)

En esta guía se proporcionan pasos de ejemplo `Xamarin.Android.Support.v4` para instalar la biblioteca de soporte en Windows & Mac.

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windowsandroid-drivers-debug-windowsmd"></a>[¿Qué controladores USB son necesario para depurar Android en Windows?](android-drivers-debug-windows.md)

Para depurar en un dispositivo Android al desarrollar en Windows; debe instalar un controlador USB compatible. De forma predeterminada, el administrador de Android SDK incluye el "controlador USB de Google", que agrega compatibilidad con dispositivos Nexus.
Otros dispositivos requieren controladores USB publicados por el fabricante del dispositivo. En esta guía se proporciona información sobre cómo buscar estos controladores, así como métodos de prueba alternativos.

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vmconnect-android-emulator-mac-windowsmd"></a>[¿Es posible conectarse a emuladores de Android que se ejecutan en un Mac desde una máquina virtual de Windows?](connect-android-emulator-mac-windows.md)

En esta guía se tratan los métodos al usar el emulador de Android.

## <a name="general-questions"></a>Preguntas generales

### <a name="how-do-i-automate-an-android-nunit-test-projectautomate-android-nunit-testmd"></a>[¿Cómo se automatiza un proyecto de prueba de NUnit de Android?](automate-android-nunit-test.md)

En esta guía se explican los pasos para configurar un proyecto de prueba de Android NUnit, _no_ un proyecto de Xamarin. UITest. Las guías de Xamarin. UITest se pueden encontrar [aquí](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

### <a name="why-cant-my-android-release-build-connect-to-the-internetandroid-internetmd"></a>[¿Por qué no se puede conectar la compilación de versión de Android a Internet?](android-internet.md)

La causa más común de este problema es que el permiso de **Internet** se incluye automáticamente en una compilación de depuración, pero debe establecerse manualmente para una compilación de versión. En esta guía se describe cómo habilitar el permiso en las compilaciones de versión.

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packagesandroid-support-v4v13-librariesmd"></a>[Paquetes NuGet más inteligentes de Xamarin Android Support v4/v13](android-support-v4v13-libraries.md)

`Support-v4`y `Support-v13` no se pueden utilizar juntos en la misma aplicación, es decir, son mutuamente excluyentes. Esto se debe `Support-v13` a que realmente contiene todos los tipos y la `Support-v4`implementación de. Si intenta hacer referencia a ambos en el mismo proyecto, se producirán errores de tipo duplicado.

### <a name="how-do-i-resolve-a-pathtoolongexception-errorpath-too-long-exceptionmd"></a>[Cómo resolver un error PathTooLongException?](path-too-long-exception.md)

En este artículo se explica cómo resolver un error **PathTooLongException** que puede producirse al compilar un proyecto de Xamarin. Android.

## <a name="deprecated"></a>En desuso

> [!NOTE]
> Los artículos siguientes se aplican a los problemas que se han resuelto en las versiones recientes de Xamarin. Sin embargo, si el problema se produce en la versión más reciente del software, registre un [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con la información de control de versiones completa y el resultado del registro de compilación completo.

### <a name="what-version-of-xamarinandroid-added-lollipop-supportxa-lollipopmd"></a>[¿Qué versión de Xamarin.Android agrega compatibilidad con Lollipop?](xa-lollipop.md)

Esta guía se escribió originalmente para la versión preliminar de Android L. Xamarin. Android 4,17 se ha agregado compatibilidad con Android L Preview & Xamarin. Android 4,20 se ha agregado compatibilidad con el círculo Android.

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawablemissing-action-mode-share-drawablemd"></a>[Android.Support.v7.AppCompat: no se encontró ningún recurso que coincida con el nombre especificado: attr 'android: actionModeShareDrawable'](missing-action-mode-share-drawable.md)

Este error puede producirse en versiones anteriores de Xamarin si faltan algunos de los paquetes de Android SDK necesarios.

### <a name="adjusting-java-memory-parameters-for-the-android-designerandroid-designer-java-memorymd"></a>[Ajuste de los parámetros de memoria de Java para Android Designer](android-designer-java-memory.md)

Los parámetros de memoria predeterminados que se usan `java` al iniciar el proceso de Android Designer pueden ser incompatibles con algunas configuraciones del sistema. A partir de Xamarin Studio 5.7.2.7 y Xamarin para Visual Studio 3.9.344 esta configuración se puede personalizar en cada proyecto.

### <a name="my-android-resourcedesignercs-file-will-not-updateresource-designer-wont-updatemd"></a>[Mi archivo Resource.designer.cs de Android no se actualizará](resource-designer-wont-update.md)

Un error en Xamarin. Studio 5,1 tenía archivos. csproj previamente dañados al eliminar de forma parcial o totalmente el código XML en el archivo. csproj. Esto haría que se produjera un error en las partes importantes del sistema de compilación de Android (como la actualización de Resource.designer.cs de Android). A partir del 15 de julio, se ha corregido este error; pero en muchos casos el archivo de proyecto tiene que repararse manualmente, tal y como se describe en esta guía.
