---
title: Pruebas, optimización e implementación de aplicaciones de Xamarin.Android
description: En esta sección se incluyen guías en las que se explica cómo probar una aplicación, optimizar su rendimiento, prepararla para el lanzamiento, firmarla con un certificado y publicarla en una tienda de aplicaciones.
ms.prod: xamarin
ms.assetid: 568C0B85-EFF3-AF6F-5605-95055193D367
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 4b7d3d19ce8766ccdbfc41163fcad44074e832b8
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "80159772"
---
# <a name="deployment-and-testing"></a>Implementación y prueba

En esta sección se incluyen guías en las que se explica cómo probar una aplicación, optimizar su rendimiento, prepararla para el lanzamiento, firmarla con un certificado y publicarla en una tienda de aplicaciones.

## <a name="application-package-sizes"></a>[Tamaños de paquete de aplicación](app-package-size.md)

En este artículo se examinan los elementos que componen un paquete de aplicación Xamarin.Android y las estrategias asociadas que se pueden utilizar para la implementación eficaz del paquete durante las etapas de depuración y lanzamiento de la versión.

## <a name="apply-changes"></a>[Aplicar cambios](apply-changes.md)

En esta guía se trata la característica Aplicar cambios, que permite enviar cambios de recursos a la aplicación en ejecución sin necesidad de reiniciarla.

## <a name="building-apps"></a>[Creación de aplicaciones](building-apps/index.md)

En esta sección se describe cómo funciona el proceso de compilación y se explica cómo compilar paquetes de aplicaciones Android (APK) específicos de ABI.

## <a name="command-line-emulator"></a>[Emulador de la línea de comandos](command-line-emulator.md)

En este artículo se trata brevemente el inicio del emulador mediante la línea de comandos.

## <a name="debugging"></a>[Depuración](~/android/deploy-test/debugging/index.md)

Las guías de la sección ayudan a depurar la aplicación mediante emuladores de Android, dispositivos Android reales y el registro de depuración.

## <a name="setting-the-debuggable-attribute"></a>[Establecer el atributo Debuggable](~/android/deploy-test/debuggable-attribute.md)

En este artículo se explica cómo establecer el atributo debuggable para que herramientas como `adb` puedan comunicarse con la JVM.

## <a name="environment"></a>[Entorno](environment.md)

En este artículo se describe el entorno de ejecución de Xamarin.Android y las propiedades del sistema Android que influyen en la ejecución del programa.

## <a name="gdb"></a>[GDB](gdb.md)

En este artículo se explica cómo usar `gdb` para depurar una aplicación Xamarin.Android.

## <a name="installing-a-system-app"></a>[Instalar una aplicación del sistema](install-system-app.md)

En esta guía se explica cómo instalar una aplicación Xamarin.Android como una aplicación del sistema en un dispositivo Android o como parte de una ROM personalizada.

## <a name="linking-on-android"></a>[Vincular en Android](linker.md)

En este artículo se describe el proceso de vinculación que se usa en Xamarin.Android para reducir el tamaño final de una aplicación. Se describen los diferentes niveles de vinculación que se pueden ejecutar y se proporcionan algunas orientaciones y consejos de solución de problemas para mitigar los errores que podrían deberse al uso del vinculador.

## <a name="xamarinandroid-performance"></a>[Rendimiento de Xamarin.Android](~/android/deploy-test/performance.md)

Existen muchas técnicas para aumentar el rendimiento de las aplicaciones compiladas con Xamarin.Android. En conjunto, estas técnicas pueden reducir considerablemente la cantidad de trabajo que realiza una CPU y la cantidad de memoria consumida por una aplicación.

## <a name="profiling-android-apps"></a>[Generar perfiles de aplicaciones Android](~/android/deploy-test/profiling.md)

En esta guía se explica cómo usar las herramientas del generador de perfiles para examinar el rendimiento y el uso de memoria de una aplicación Android.

## <a name="preparing-an-application-for-release"></a>[Preparar una aplicación para su lanzamiento](~/android/deploy-test/release-prep/index.md)

Después de haber codificado y probado una aplicación, es necesario preparar un paquete para la distribución. La primera tarea en la preparación de este paquete es compilar la aplicación para el lanzamiento, lo que implica principalmente establecer algunos atributos de la aplicación.

## <a name="signing-the-android-application-package"></a>[Firmar el paquete de aplicación de Android](~/android/deploy-test/signing/index.md)

Obtenga información sobre cómo crear una identidad de firma de Android, crear un certificado de firma para aplicaciones de Android y firmar la aplicación con el certificado de firma. Además, en este tema se explica cómo exportar la aplicación a un disco para la distribución *ad hoc*. Se puede realizar una instalación de prueba del APK resultante en dispositivos Android sin pasar por una tienda de aplicaciones.

## <a name="publishing-an-application"></a>[Publicar una aplicación](~/android/deploy-test/publishing/index.md)

En esta serie de artículos se explican los pasos para la distribución pública de una aplicación creada con Xamarin.Android. La distribución puede realizarse a través de diversos canales, como el correo electrónico, un servidor web privado, Google Play o Amazon Appstore para Android.
