---
title: 'Instalación y configuración del desgaste del SO onXamarin. Android '
description: Este artículo le guía a través de los pasos de instalación y los detalles de configuración necesarios para preparar el equipo y los dispositivos para el desarrollo del desgaste de Android. Al final de este artículo, tendrá una instalación operativa de Xamarin. Android desgaste integrada en Visual Studio para Mac o Microsoft Visual Studio y estará listo para empezar a crear su primera aplicación de trabajo de Xamarin. Android.
ms.prod: xamarin
ms.assetid: 3BB395FA-0545-4024-A18F-98CF5E9CA55F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 1f6fa57bbe47bbc2fe97bbd0df7018fc1cd3897a
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522171"
---
# <a name="setup-and-installation"></a>Configuración e instalación

_Este artículo le guía a través de los pasos de instalación y los detalles de configuración necesarios para preparar el equipo y los dispositivos para el desarrollo del desgaste de Android. Al final de este artículo, tendrá una instalación operativa de Xamarin. Android desgaste integrada en Visual Studio para Mac o Microsoft Visual Studio y estará listo para empezar a crear su primera aplicación de trabajo de Xamarin. Android._

## <a name="requirements"></a>Requisitos

Lo siguiente es necesario para crear aplicaciones de desgaste de Android basadas en Xamarin:

- **Visual Studio o Visual Studio para Mac** &ndash; Se requiere Visual Studio 2017 Community o posterior.

- **Xamarin. Android** &ndash; Xamarin. Android 4,17 o posterior debe estar instalado y configurado con Visual Studio o Visual Studio para Mac.

- **Android SDK** -Android SDK 5.0.1 (API 21) o posterior debe instalarse a través del administrador de Android SDK.

- **Kit para desarrolladores de Java** El desarrollo de Xamarin Android requiere [JDK 1,8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) si está desarrollando para el nivel de API 24 o superior (JDK 1,8 también es compatible con los niveles de API anteriores a 24). &ndash;

Puede seguir usando [JDK 1,7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) si está desarrollando específicamente para el nivel de API 23 o una versión anterior.

> [!IMPORTANT]
> Xamarin.Android no admite JDK 9.

## <a name="installation"></a>Instalación

Después de haber instalado Xamarin. Android, realice los pasos siguientes para que esté listo para compilar y probar aplicaciones de desgaste de Android: 

1. Instale las herramientas y el Android SDK necesarios.
2. Configurar un dispositivo de prueba.
3. Cree su primera aplicación de desgaste de Android.

Estos pasos se describen en las secciones siguientes.


### <a name="install-android-sdk-and-tools"></a>Instalación de Android SDK y herramientas 

Inicie el **Administrador de Android SDK**: 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Cómo iniciar el administrador de Android SDK en Visual Studio](installation-images/vs/sdk-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Cómo iniciar el administrador de Android SDK en Visual Studio para Mac](installation-images/xs/sdk-menu.png)

-----


Asegúrese de que tiene instalados los siguientes Android SDK y herramientas:

* Android SDK Tools v 24.0.0 o superior, y
* Android 4.4 W (API20), o
* Android 5.0.1 (API21) o superior.

Si no tiene instaladas las herramientas y el SDK más recientes, descargue las herramientas de SDK *y* los bits de API necesarios (puede que tenga que desplazarse &ndash; un poco para encontrarlos; la selección de la API se muestra a continuación): 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Captura de pantalla del administrador de SDK de ejemplo de la habilitación de componentes de Android 5.0.1](installation-images/vs/sdk-select.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Captura de pantalla del administrador de SDK de ejemplo para habilitar los componentes de Android 4,4 y 5.0.1](installation-images/xs/sdk-select.png)

-----


## <a name="configuration"></a>Configuración

Antes de poder usar probar la aplicación, debe configurar un emulador de desgaste de Android o un dispositivo de desgaste de Android real. 


### <a name="android-wear-emulator"></a>Emulador de uso de Android

Antes de poder usar un emulador de desgaste de Android, debe configurar un dispositivo virtual Android de desgaste de Android (AVD) con el administrador del emulador de **Google**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Cómo iniciar el administrador de Android Emulator desde Visual Studio](installation-images/vs/emulator-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Cómo iniciar el administrador de Android Emulator desde Visual Studio para Mac](installation-images/xs/emulator-menu.png)

-----

Para más información sobre cómo configurar un emulador de desgaste de Android, consulte depurar el [desgaste de Android en un emulador](~/android/wear/deploy-test/debug-on-emulator.md).


### <a name="android-wear-device"></a>Dispositivo de desgaste de Android

Si tiene un dispositivo de desgaste de Android, como un desgaste de SmartWatch de Android, puede depurar la aplicación en este dispositivo en lugar de usar un emulador. Para obtener información sobre el desarrollo con un dispositivo de desgaste, consulte depurar [en un dispositivo de desgaste](~/android/wear/deploy-test/debug-on-device.md).


## <a name="create-your-first-android-wear-app"></a>Cree su primera aplicación de desgaste de Android

Siga las instrucciones [Hello, desgaste](~/android/wear/get-started/hello-wear.md) para compilar su primera aplicación de inspección.


## <a name="packaging-your-app"></a>Empaquetar la aplicación

Las aplicaciones de desgaste de Android siempre se distribuyen con una aplicación de teléfono Android complementaria. 

Al agregar la aplicación de desgaste de Android como referencia a la aplicación principal de Android, se supone que es un proyecto de desgaste de Android y generará automáticamente el XML y los metadatos necesarios. Además, comprobará que los números de versión y paquete coinciden para que pueda enviar fácilmente sus aplicaciones a Google Play. 

Para más información sobre las aplicaciones de desgaste de empaquetado, consulte [trabajar con empaquetado](~/android/wear/deploy-test/packaging.md).


## <a name="related-links"></a>Vínculos relacionados

- [SkeletonWear (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-skeletonwear)
