---
title: Compilación para varios dispositivos en Xamarin.iOS
description: En este documento se describen distintas opciones de configuración de compilación que pueden usarse para personalizar una compilación de Xamarin.iOS para diferentes dispositivos.
ms.prod: xamarin
ms.assetid: 3B259248-887E-3E4F-E09C-7AD28C2A8CEE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: cac0d9bc6d4ac2c07bcfd09275ff3f78e0e18b91
ms.sourcegitcommit: e27e29c14b783263e063baaa65d4eecb8dd31f57
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98628844"
---
# <a name="compiling-for-different-devices-in-xamarinios"></a>Compilación para varios dispositivos en Xamarin.iOS

Las propiedades de compilación del ejecutable se pueden configurar en la página de propiedades de **Compilación de iOS** del proyecto; para acceder a ella, haga clic con el botón derecho en el nombre del proyecto y vaya a **Opciones > Compilación de iOS** en Visual Studio para Mac, y a **Propiedades** en Visual Studio:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Página de propiedades de compilación de iOS de los proyectos para Visual Studio para Mac.](compiling-for-different-devices-images/image1.png)](compiling-for-different-devices-images/image1.png#lightbox) 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Página de propiedades de compilación de iOS de los proyectos para Visual Studio.](compiling-for-different-devices-images/image1a.png)](compiling-for-different-devices-images/image1a.png#lightbox)

-----

Además de las opciones de configuración disponibles en la interfaz de usuario, también puede pasar su propia configuración de las opciones de la línea de comandos a la [herramienta de compilación de Xamarin.iOS (mtouch)](~/ios/deploy-test/mtouch.md).

## <a name="sdk-options"></a>Opciones de SDK

Visual Studio para Mac permite configurar dos propiedades importantes relacionadas con el SDK: la versión del SDK de iOS utilizada para compilar el software y el destino de la implementación, o bien la versión de iOS mínima requerida.

La opción **Versión del SDK** de iOS permite usar distintas versiones de un SDK publicado de Apple, que remite a Xamarin.iOS a los compiladores, los enlazadores y las bibliotecas a las que debe hacer referencia durante la compilación. Haga clic con el botón derecho en el proyecto y elija **Opciones**; a continuación, elija **Compilación de iOS** en la ventana de opciones:

[![Elegir la versión del SDK en la ventana de opciones](compiling-for-different-devices-images/sdk-version-sml.png)](compiling-for-different-devices-images/sdk-version.png#lightbox)

La opción **Destino de implementación** se usa para seleccionar la versión mínima requerida del sistema operativo en que se ejecutará la aplicación. Se configura en el archivo **Info.plist** del proyecto. Debe elegir la versión mínima que tiene todas las API que necesita para ejecutar la aplicación.

[![Establecer el destino de implementación en el archivo Info.plist](compiling-for-different-devices-images/deployment-target-sml.png)](compiling-for-different-devices-images/deployment-target.png#lightbox)

En general, la API de Xamarin.iOS API expone todos los métodos disponibles en la última versión del SDK y, si resulta necesario, se ofrecen propiedades prácticas que le permiten detectar si la funcionalidad está disponible en tiempo de ejecución (por ejemplo, `UIDevice.UserInterfaceIdiom` y `UIDevice.IsMultitaskingSupported` siempre funcionan en Xamarin.iOS, y nosotros realizamos todo el trabajo en segundo plano).

## <a name="linking"></a>Vinculación

Consulte nuestra página dedicada al [enlazador](~/ios/deploy-test/linker.md) para obtener más información sobre cómo el enlazador ayuda a reducir el tamaño de los ejecutables y cómo utilizarlo de manera eficaz.

## <a name="code-generation-engine"></a>Motor de generación de código

A partir de Xamarin.iOS 4.0, hay dos backend de generación de código para Xamarin.iOS. El motor de generación de código Mono regular y uno basado en el compilador de optimización LLVM. Cada motor tiene su ventajas y desventajas.

Por lo general, durante el proceso de desarrollo, es probable que use el motor de generación de código Mono, ya que le ayudará a realizar la iteración con rapidez. Para las compilaciones de versiones y la implementación de App Store, puede que desee cambiar al motor de generación de código LLVM.

El motor backend de optimización LLVM produce código más rápido y preciso que el motor Mono, pero en detrimento de tiempos de compilación largos.

Puede habilitarlos en las opciones de Compilación de iOS en Visual Studio para Mac o Visual Studio.

[![Habilitación de LLVM en Visual Studio para Mac.](compiling-for-different-devices-images/image2.png)](compiling-for-different-devices-images/image2.png#lightbox)

[![Habilitación de LLVM en Visual Studio.](compiling-for-different-devices-images/image2a.png)](compiling-for-different-devices-images/image2a.png#lightbox)

## <a name="architecture-support"></a>Compatibilidad de la arquitectura

### <a name="armv6-xamarinios-discontinued-support-for-armv6-with-v810"></a>ARMv6 (compatibilidad descontinuada de Xamarin.iOS para ARMv6 con v8.10)

- iPhone (original), 3G
- iPod de primera y segunda generación

### <a name="armv7"></a>ARMv7

- iPhone 3GS, 4, 4S
- iPad 1, 2, 3, Mini
- iPod de tercera, cuarta y quinta generación

### <a name="armv7s"></a>ARMv7s

- iPhone 5
- iPhone 5c
- iPad 4

Si el destino solo es un procesador de ARMv7s, el código generado será un poco más rápido, pero ya no se ejecutará en los sistemas ARMv7 o ARMv6 a menos que se compile un archivo binario FAT que contenga varios archivos ejecutables en el paquete.

### <a name="arm64-xamarinios-started-supporting-arm64-in-v86"></a>ARM64 (Xamarin.iOS empezó a ser compatible con ARM64 en v8.6)

- iPhone 5s
- iPhone SE
- iPhone 6, 6 Plus
- iPhone 6s, 6s Plus
- iPhone 7, 7 Plus
- iPhone 8, 8 Plus
- iPhone X
- iPad Air
- iPad Air 2
- iPad Mini 2, 3, 4
- iPad Pro (todos)

Tenga en cuenta que las compilaciones enviadas a App Store deben incluir compatibilidad de 64 bits; se trata de un requisito establecido por [Apple](https://developer.apple.com/news/?id=12172014b). Además, iOS 11 solo admite las aplicaciones de 64 bits.

### <a name="arm-thumb-2-support"></a>Compatibilidad con ARM Thumb-2

Thumb es un conjunto de instrucciones más compacto que usan los procesadores ARM. Al habilitar la compatibilidad con Thumb, puede reducir el tamaño del archivo ejecutable, pero en detrimento de tiempos de ejecución más lentos. Thumb es compatible con ARMv7 y ARMv7s.

## <a name="conditional-framework-usage"></a>Uso condicional de marcos de trabajo

Si el proyecto desea usar algunas de las características de las últimas versiones de iOS, debe basarse de manera condicional en determinados marcos de trabajo nuevos. Un buen ejemplo de ello es usar iAd al ejecutar iOS 4.0 o versiones posteriores, pero todavía se admite la compatibilidad con dispositivos 3.x. Para ello, debe informar a Xamarin.iOS que necesita establecer un vínculo "flexible" con el marco de trabajo de iAd. Los enlaces flexibles garantizan que el marco de trabajo solo se cargue a petición la primera vez que se requiera una clase del marco de trabajo.

Para ello, debe realizar los pasos siguientes:

- Abra **Opciones del proyecto** y vaya al panel **Compilación de iOS**.
- Agregue `'-gcc_flags "-weak_framework iAd"'` a **Opciones adicionales** para cada configuración en que desea establecer un enlace flexible:

[![Opciones adicionales](compiling-for-different-devices-images/image3.png)](compiling-for-different-devices-images/image3.png#lightbox)

Además de esto, necesita proteger el uso de los tipos de la ejecución en versiones anteriores de iOS donde pueden no existir. Hay varios métodos para hacer esto, pero uno de ellos analiza `UIDevice.CurrentDevice.SystemVersion`.

## <a name="related-links"></a>Vínculos relacionados

- [Enlazador](~/ios/deploy-test/linker.md)
