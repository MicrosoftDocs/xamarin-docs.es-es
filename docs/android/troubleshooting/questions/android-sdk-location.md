---
title: ¿Dónde puedo configurar mi ubicaciones de Android SDK?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6A9DE6E9-3E27-4DD2-87D2-34E95E5D401C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 11/16/2017
ms.openlocfilehash: 8685be4bb1cc45ff04dc8d9f7d8e64e7b1483b60
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027025"
---
# <a name="where-can-i-set-my-android-sdk-locations"></a>¿Dónde puedo configurar mi ubicaciones de Android SDK?

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

En Visual Studio, navegue hasta **Herramientas > Opciones > Xamarin > Configuración de Android** para ver y establecer la ubicación de Android SDK:

[![Ejemplo de la pestaña Ubicaciones de Preferencias](android-sdk-location-images/win/01-locations-sml.png)](android-sdk-location-images/win/01-locations.png#lightbox)

La ubicación predeterminada de cada ruta de acceso es la siguiente:

- Ubicación del Kit de desarrollo de Java: 

    **C:\\Archivos de programa\\Java\\jdk1.8.0_131**

- Ubicación de Android SDK: 

    **C:\\Archivos de programa (x86)\\Android\\android-sdk**

- Ubicación de Android NDK: 

    **C:\\ProgramData\\Microsoft\\AndroidNDK64\\android-ndk-r13b**

Tenga en cuenta que el número de versión del NDK puede variar. Por ejemplo, en lugar de **android-ndk-r13b**, podría ser una versión anterior, como **android-ndk-r10e**.

Para establecer la ubicación de Android SDK, escriba la ruta de acceso completa del directorio de Android SDK en el cuadro **Ubicación de Android SDK**. Puede navegar hasta la ubicación de Android SDK en el Explorador de archivos, copiar la ruta de acceso de la barra de direcciones y pegarla en el cuadro **Ubicación de Android SDK**.
Por ejemplo, si la ubicación de Android SDK es **C:\\Usuarios\\nombre_usuario\\AppData\\Local\\Android\\Sdk**, desactive la ruta de acceso anterior en el cuadro **Ubicación de Android SDK**, pegue esta ruta de acceso y haga clic en **Aceptar**.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

En Visual Studio para Mac, navegue hasta **Preferencias > Proyectos > Ubicaciones de SDK > Android**. En la página **Android**, haga clic en la pestaña **Ubicaciones** para ver y establecer la ubicación del SDK:

[![Ejemplo de la pestaña Ubicaciones de Preferencias](android-sdk-location-images/mac/01-locations-sml.png)](android-sdk-location-images/mac/01-locations.png#lightbox)

La ubicación predeterminada de cada ruta de acceso es la siguiente:

- Ubicación de Android SDK: 

    **~/Library/Developer/Xamarin/android-sdk-macosx**

- Ubicación de Android NDK: 

    **~/Library/Developer/Xamarin/android-ndk/android-ndk-r14b**

- Ubicación de SDK de Java (JDK): 

    **/usr**

Tenga en cuenta que el número de versión del NDK puede variar. Por ejemplo, en lugar de **android-ndk-r14b**, podría ser una versión anterior, como **android-ndk-r10e**.

Para establecer la ubicación de Android SDK, escriba la ruta de acceso completa del directorio de Android SDK en el cuadro **Ubicación de Android SDK**. Puede seleccionar la carpeta Android SDK en el buscador, presionar **CTRL +&#8984;+I** para ver la información de la carpeta, hacer clic y arrastrar la ruta de acceso a la derecha de **Donde:** , copiarla y, a continuación, pegarla en el cuadro **Ubicación de Android SDK** de la pestaña **Ubicaciones**. Por ejemplo, si la ubicación de Android SDK es **~/Library/Developer/Android/Sdk**, borre la ruta de acceso anterior del cuadro **Ubicación de Android SDK**, pegue esta ruta de acceso y haga clic en **Aceptar**.

-----
