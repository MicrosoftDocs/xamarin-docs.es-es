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
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027025"
---
# <a name="where-can-i-set-my-android-sdk-locations"></a>¿Dónde puedo configurar mi ubicaciones de Android SDK?

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

En Visual Studio, vaya a **herramientas > opciones > Xamarin > configuración de Android** para ver y establecer la ubicación de Android SDK:

[![pestaña ubicaciones de ejemplo en preferencias](android-sdk-location-images/win/01-locations-sml.png)](android-sdk-location-images/win/01-locations.png#lightbox)

La ubicación predeterminada de cada ruta de acceso es la siguiente:

- Ubicación del kit de desarrollo de Java: 

    **C:\\archivos de programa\\Java\\JDK 1.8.0 _131**

- Android SDK Ubicación: 

    **C:\\Archivos de programa (x86)\\Android\\android-sdk**

- Ubicación del NDK de Android: 

    **C:\\ProgramData\\Microsoft\\AndroidNDK64\\Android-NDK-r13b**

Tenga en cuenta que el número de versión del NDK puede variar. Por ejemplo, en lugar de **Android-NDK-r13b**, podría ser una versión anterior como **Android-NDK-r10e**.

Para establecer la ubicación de Android SDK, escriba la ruta de acceso completa del directorio de Android SDK en el cuadro **Ubicación de Android SDK** . Puede navegar hasta el Android SDK ubicación en el explorador de archivos, copiar la ruta de acceso de la barra de direcciones y pegar esta ruta de acceso en el cuadro **Ubicación de Android SDK** .
Por ejemplo, si la ubicación de Android SDK está en **C:\\usuarios\\nombre de usuario\\AppData\\el SDK de\\de Android\\local**, desactive la ruta de acceso anterior en el cuadro ubicación de **Android SDK** y péguela en esta ruta de acceso. y haga clic en **Aceptar**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

En Visual Studio para Mac, vaya a **preferencias > proyectos > ubicaciones del SDK > Android**. En la página **Android** , haga clic en la pestaña **ubicaciones** para ver y establecer la ubicación del SDK:

[![pestaña ubicaciones de ejemplo en preferencias](android-sdk-location-images/mac/01-locations-sml.png)](android-sdk-location-images/mac/01-locations.png#lightbox)

La ubicación predeterminada de cada ruta de acceso es la siguiente:

- Android SDK Ubicación: 

    **~/Library/Developer/Xamarin/android-sdk-macosx**

- Ubicación del NDK de Android: 

    **~/Library/Developer/Xamarin/android-ndk/android-ndk-r14b**

- Ubicación del SDK de Java (JDK): 

    **queda**

Tenga en cuenta que el número de versión del NDK puede variar. Por ejemplo, en lugar de **Android-NDK-R14B**, podría ser una versión anterior como **Android-NDK-r10e**.

Para establecer la ubicación de Android SDK, escriba la ruta de acceso completa del directorio de Android SDK en el cuadro **Ubicación de Android SDK** . Puede seleccionar la carpeta Android SDK en el buscador, presionar **&#8984;Ctrl + + I** para ver la información de la carpeta, hacer clic y arrastrar el trazado a la derecha de **donde:** , copiar y pegarlo en el cuadro **Ubicación Android SDK** de la pestaña **ubicaciones** . Por ejemplo, si la ubicación de Android SDK está en **~/Library/Developer/Android/SDK**, desactive la ruta de acceso antigua en el cuadro **Ubicación Android SDK** , péguela en esta ruta de acceso y haga clic en **Aceptar**.

-----
