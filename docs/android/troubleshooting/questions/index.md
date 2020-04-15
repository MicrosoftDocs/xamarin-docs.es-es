---
title: Preguntas más frecuentes sobre Xamarin.Android
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 28b13951a0d681ffdb8e643667e496e0b3606628
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "76724007"
---
# <a name="android-frequently-asked-questions"></a>Preguntas más frecuentes sobre Android

## <a name="installation--setup"></a>Configuración e instalación

### <a name="which-android-sdk-packages-should-i-install"></a>[¿Qué paquetes de Android SDK debo instalar?](install-android-sdk-packages.md)

La instalación de Android SDK no incluye automáticamente todos los paquetes mínimos necesarios para el desarrollo. Aunque las necesidades individuales de los desarrolladores pueden variar, en esta guía se describen los paquetes que se necesitarán para desarrollar con Xamarin.Android.

### <a name="where-can-i-set-my-android-sdk-locations"></a>[¿Dónde puedo configurar mi ubicaciones de Android SDK?](android-sdk-location.md)

En esta guía se describe la configuración predeterminada de Android SDK, que debería funcionar en la mayoría de las configuraciones, así como la forma de cambiar estos valores predeterminados en Visual Studio para Mac o Visual Studio, si es necesario.

### <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>[¿Cómo se puede actualizar la versión del kit de desarrollo de Java (JDK)?](update-jdk.md)

En este artículo se muestra cómo actualizar la versión del kit de desarrollo de Java (JDK) en Windows y Mac.

### <a name="can-i-use-java-development-kit-jdk-version-9-or-later"></a>[¿Puedo usar la versión 9 del kit de desarrollo de Java (JDK) o una versión posterior?](jdk9-errors.md)

Xamarin.Android requiere JDK 8 o la versión móvil de OpenJDK de Microsoft. En este artículo se muestran algunos mensajes de error comunes que puede ver si está instalado JDK 9 o posterior, junto con las instrucciones para comprobar la versión de JDK.

### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>[¿Cómo instalar manualmente las bibliotecas de compatibilidad de Android que requieren los paquetes de Xamarin.Android.Support?](install-android-support-library.md)

En esta guía se proporcionan pasos de ejemplo para instalar la biblioteca de compatibilidad de `Xamarin.Android.Support.v4` en Windows y Mac.

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>[¿Qué controladores USB son necesario para depurar Android en Windows?](android-drivers-debug-windows.md)

Para depurar en un dispositivo Android al desarrollar en Windows, debe instalar un controlador USB compatible. El administrador de Android SDK incluye el "controlador USB de Google" de forma predeterminada, que agrega compatibilidad con los dispositivos Nexus.
Otros dispositivos requieren controladores USB publicados por el fabricante del dispositivo. En esta guía se proporciona información sobre cómo buscar estos controladores, así como métodos de prueba alternativos.

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>[¿Es posible conectarse a emuladores de Android que se ejecutan en un Mac desde una máquina virtual de Windows?](connect-android-emulator-mac-windows.md)

En esta guía se tratan los métodos aplicables al uso del emulador de Android.

## <a name="general-questions"></a>Preguntas generales

### <a name="how-do-i-automate-an-android-nunit-test-project"></a>[¿Cómo se automatiza un proyecto de prueba de NUnit de Android?](automate-android-nunit-test.md)

En esta guía se explica cómo configurar un proyecto de prueba de NUnit de Android, _no_ un proyecto de Xamarin.UITest. Las guías de Xamarin.UITest están disponibles [aquí](/appcenter/test-cloud/preparing-for-upload).

### <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>[¿Por qué no se puede conectar la compilación de versión de Android a Internet?](android-internet.md)

El motivo más habitual de este problema es que el permiso de **INTERNET** se incluye automáticamente en una compilación de depuración, pero debe establecerse manualmente para una compilación de versión. En esta guía se describe cómo habilitar el permiso en las compilaciones de versiones.

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>[Paquetes NuGet más inteligentes de Xamarin Android Support v4/v13](android-support-v4v13-libraries.md)

`Support-v4` y `Support-v13` no se pueden usar a la vez en la misma aplicación; es decir, se excluyen mutuamente. Esto se debe a que, en realidad, `Support-v13` contiene todos los tipos e implementaciones de `Support-v4`. Si se prueban y se hace referencia a ambas en el mismo proyecto, se producirán errores de tipo duplicado.

### <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>[¿Cómo se puede resolver un error PathTooLongException?](path-too-long-exception.md)

En este artículo se explica cómo resolver un error **PathTooLongException** que puede producirse al compilar un proyecto de Xamarin.Android.

## <a name="deprecated"></a>Obsoleto

> [!NOTE]
> Los artículos siguientes se aplican a problemas que se han resuelto en versiones recientes de Xamarin. Sin embargo, si el problema se produce en la versión más reciente del software, cree un [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con la información de control de versiones completa y la salida del registro de compilación completa.

### <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>[¿Qué versión de Xamarin.Android agrega compatibilidad con Lollipop?](xa-lollipop.md)

Esta guía se escribió originalmente para la versión preliminar de Android L. Xamarin.Android 4.17 incorporó la compatibilidad con la versión preliminar de Android L y Xamarin.Android 4.20 agregó compatibilidad con Android Lollipop.

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawable"></a>[Android.Support.v7.AppCompat: no se encontró ningún recurso que coincida con el nombre especificado: attr 'android: actionModeShareDrawable'](missing-action-mode-share-drawable.md)

Este error puede producirse en versiones anteriores de Xamarin si faltan algunos de los paquetes de Android SDK necesarios.

### <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>[Ajuste de los parámetros de memoria de Java para Android Designer](android-designer-java-memory.md)

Los parámetros de memoria predeterminados que se utilizan al iniciar el proceso `java` para el diseñador de Android pueden ser incompatibles con algunas configuraciones del sistema. A partir de Xamarin Studio 5.7.2.7 y Xamarin para Visual Studio 3.9.344, esta configuración se puede personalizar por proyecto.

### <a name="my-android-resourcedesignercs-file-will-not-update"></a>[Mi archivo Resource.designer.cs de Android no se actualizará](resource-designer-wont-update.md)

Un error en Xamarin.Studio 5.1 dañó anteriormente los archivos .csproj al eliminar parcialmente o completamente el código XML del archivo .csproj. Esto generó errores en partes importantes del sistema de compilación de Android (como la actualización de Resource.designer.cs de Android). A partir de la versión 5.1.4 estable del 15 de julio, se ha corregido este error, pero, en muchos casos, el archivo de proyecto tiene que repararse manualmente, tal y como se describe en esta guía.
