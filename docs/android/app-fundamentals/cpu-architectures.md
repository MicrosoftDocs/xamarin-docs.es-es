---
title: Arquitecturas de CPU
description: Xamarin. Android admite varias arquitecturas de CPU, incluidos los dispositivos de 32 bits y de 64 bits. En este artículo se explica cómo dirigir una aplicación a una o varias arquitecturas de CPU compatibles con Android.
ms.prod: xamarin
ms.assetid: D4BC889D-9164-49BB-9B7B-F6C4E4E109F1
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2019
ms.openlocfilehash: 16e805488969aadb0d0b8aa5c892248b7fa403c9
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521207"
---
# <a name="cpu-architectures"></a>Arquitecturas de CPU

_Xamarin. Android admite varias arquitecturas de CPU, incluidos los dispositivos de 32 bits y de 64 bits. En este artículo se explica cómo dirigir una aplicación a una o varias arquitecturas de CPU compatibles con Android._

## <a name="cpu-architectures-overview"></a>Información general sobre arquitecturas de CPU

Al preparar la aplicación para su lanzamiento, debe especificar las arquitecturas de CPU de la plataforma que admite la aplicación. Un APK único puede contener código máquina para admitir varias arquitecturas diferentes. Cada colección de código específico de la arquitectura está asociada a una *interfaz binaria de aplicación* (ABI). Cada ABI define cómo se espera que este código máquina interactúe con Android en tiempo de ejecución.
Para obtener más información sobre cómo funciona, consulte [dispositivos &amp; de varios núcleos Xamarin. Android](~/android/deploy-test/multicore-devices.md).


## <a name="how-to-specify-supported-architectures"></a>Cómo especificar las arquitecturas admitidas

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Normalmente, se selecciona explícitamente una arquitectura (o arquitecturas) cuando la aplicación está configurada para su **lanzamiento**. Cuando la aplicación está configurada para depurar, se habilitan las opciones **usar tiempo de ejecución compartido** y **usar implementación rápida** , lo que deshabilita la selección de arquitectura explícita.

En Visual Studio, haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y seleccione **propiedades**. En la página **Opciones de Android** , consulte la sección Propiedades de **empaquetado** y compruebe que la opción usar el **tiempo de ejecución compartido** está deshabilitada (la desactivación permite seleccionar explícitamente qué Abi se admite). Haga clic en el botón **Opciones avanzadas** y, en **arquitecturas admitidas**, compruebe las arquitecturas que desea admitir:

[![Selección de armeabi y armeabi-v7a](cpu-architectures-images/vs/01-abi-selections-sml.png)](cpu-architectures-images/vs/01-abi-selections.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Normalmente, se selecciona explícitamente una arquitectura (o arquitecturas) cuando la aplicación está configurada para su **lanzamiento**. Cuando la aplicación está configurada para depurar, se habilitan las opciones usar el entorno de **ejecución mono compartido** y la **implementación rápida** de ensamblados, lo que impide la selección de arquitectura explícita.

En Visual Studio para Mac, busque el proyecto en el panel de **solución** , haga clic en el icono de engranaje situado junto al proyecto y seleccione **Opciones**. En el cuadro de diálogo **Opciones del proyecto** , haga clic en **compilación de Android**. Haga clic en la pestaña **General** y compruebe que la opción **usar el tiempo de ejecución mono compartido** está deshabilitada (la desactivación permite seleccionar explícitamente qué Abi admite). Haga clic en la pestaña **Opciones avanzadas** y, en **Abi compatibles**, compruebe el Abi de las arquitecturas que desea admitir:

[![Selección de armeabi y armeabi-v7a](cpu-architectures-images/xs/01-abi-selections-sml.png)](cpu-architectures-images/xs/01-abi-selections.png#lightbox)

-----


Xamarin.Android admite las siguientes arquitecturas:

- **armeabi** &ndash; CPU basadas en ARM que admiten al menos el conjunto de instrucciones ARMv5TE. Tenga en `armeabi` cuenta que no es seguro para subprocesos y no debe usarse en dispositivos de varias CPU.

> [!NOTE]
> A partir de [Xamarin. Android 9,2](https://docs.microsoft.com/xamarin/android/release-notes/9/9.2#removal-of-support-for-armeabi-cpu-architecture), `armeabi` ya no se admite.

- **armeabi-v7a** &ndash; CPU basadas en ARM con operaciones de punto flotante de hardware y varios dispositivos de CPU (SMP). Tenga en `armeabi-v7a` cuenta que el código del equipo no se ejecutará en los dispositivos ARMv5.

- **arm64-v8a** &ndash; CPU basadas en la arquitectura ARMv8 de 64 bits.

- **x86** &ndash; CPU que admiten el conjunto de instrucciones x86 (o IA-32). Este conjunto de instrucciones es equivalente al de Pentium Pro, incluidas las instrucciones MMX, SSE, SSE2 e SSE3.

- **x86_64** CPU que admiten el conjunto de instrucciones x86 de 64 bits (también conocido como *x64* y *AMD64*).

`armeabi-v7a` De forma predeterminada, Xamarin. Android es para las compilaciones de **versión** . Este valor proporciona un rendimiento significativamente mejor `armeabi`que. Si tiene como destino una plataforma ARM de 64 bits (como el nexo 9), seleccione `arm64-v8a`. Si va a implementar la aplicación en un dispositivo x86, seleccione `x86`. Si el dispositivo x86 de destino usa una arquitectura de CPU de 64 bits `x86_64`, seleccione.

## <a name="targeting-multiple-platforms"></a>Establecer como destino varias plataformas

Para tener como destino varias arquitecturas de CPU, puede seleccionar más de una ABI (a costa de un tamaño de archivo APK mayor). Puede usar la opción **generar un paquete (. apk) por la ABI seleccionada** (descrita en [establecer propiedades de empaquetado](~/android/deploy-test/release-prep/index.md#Set_Packaging_Properties)) para crear un apk independiente para cada arquitectura admitida.

No tiene que seleccionar **arm64-v8a** o **x86_64** para tener como destino dispositivos de 64 bits; no es necesaria la compatibilidad con 64 bits para ejecutar la aplicación en el hardware de 64 bits. Por ejemplo, los dispositivos ARM de 64 bits (como el [nexo 9](http://www.google.com/nexus/9/)) pueden ejecutar aplicaciones configuradas para `armeabi-v7a`. La principal ventaja de habilitar la compatibilidad con 64 bits es hacer que la aplicación pueda abordar más memoria.

> [!NOTE]
> A partir de agosto de 2018 se necesitarán nuevas aplicaciones para alcanzar el nivel 26 de la API, y a partir de agosto de 2019 [se requerirá que las aplicaciones proporcionen versiones de 64 bits](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html) además de la versión de 32 bits.

## <a name="additional-information"></a>Información adicional

En algunas situaciones, puede que tenga que crear un APK independiente para cada arquitectura (para reducir el tamaño de la APK o porque la aplicación tiene bibliotecas compartidas que son específicas de una arquitectura de CPU determinada).
Para obtener más información sobre este enfoque, vea compilar [apk específicos de ABI](~/android/deploy-test/building-apps/abi-specific-apks.md).
