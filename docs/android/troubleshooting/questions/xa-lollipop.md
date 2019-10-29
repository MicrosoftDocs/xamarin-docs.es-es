---
title: ¿Qué versión de Xamarin.Android agrega compatibilidad con Lollipop?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63B6E10C-098D-4C82-9253-07CA62EA85A5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 9e36189c771ed0c91a6030fd0ab615ab9af4dd52
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026718"
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>¿Qué versión de Xamarin.Android agrega compatibilidad con Lollipop?

> [!NOTE]
> Esta guía se escribió originalmente para la versión preliminar de Android L.

- [Xamarin. Android 4,17](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.17/index.md) agregó compatibilidad con la versión preliminar de Android L.
- [Xamarin. Android 4,20](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.20/index.md) agregó compatibilidad con el círculo Android.

Xamarin solo admite activamente la versión estable actual de las herramientas de Xamarin. La información siguiente se proporciona "tal cual" para las versiones anteriores de las herramientas. Para obtener la información más reciente sobre las versiones de Xamarin, consulte las notas de la [versión](https://docs.microsoft.com/xamarin/whats-new/#product-release-notes).

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>"Missing Android. jar para el nivel de API 21" en Android L Preview

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Puede aparecer el siguiente mensaje de error (o similar):

```cmd
Error 1 Could not find android.jar for API Level 21.
```

Este mensaje significa que la plataforma de Android SDK para el nivel de API 21 no está instalada. Instálelo en el administrador de Android SDK (**herramientas > abra Android SDK Manager...** ) o cambie el proyecto de Xamarin. Android para que tenga como destino una versión de API que esté instalada.

Existen algunas soluciones alternativas para este problema:

1. Cambie el proyecto para que tenga como destino la API 19 o una inferior.

2. Cambie el nombre de la carpeta Android-21 de Android-21 a Android-L. (Lo mejor es que solo se use como una solución temporal y que no funcione muy bien).

   **% LOCALAPPDATA%\\plataformas Android\\Android-SDK\\\\Android-21**

3. Retroceder temporalmente al nivel de API de Android 21 "L" Preview [1]:

    1. Elimine el **% LOCALAPPDATA%\\plataformas android\\Android-sdk\\\\Android-21** 
    2. Extract [1] en **C:\\usuarios\\&lt;username&gt;\\AppData\\Local\\android\\Android-sdk\\plataformas** para crear una carpeta **Android-L** .

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Puede aparecer el siguiente mensaje de error (o similar):

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

Esto significa que la plataforma de Android SDK para el nivel de API 21 no está instalada. Instálelo en el administrador de Android SDK (Herramientas > SDK Manager...) o cambie el proyecto de Xamarin. Android para que tenga como destino una versión de API que esté instalada.

Existen algunas soluciones alternativas para este problema:

1. Cambie el proyecto para que tenga como destino la API 19 o una inferior.

2. Cambie el nombre de la carpeta Android-21 de Android-21 a Android-L. (Lo mejor es que solo se use como una solución temporal y que no funcione muy bien).

   **~/Library/Developer/Xamarin/android-sdk-macosx/android-21**

3. Retroceder temporalmente al nivel de API de Android 21 "L" Preview [1]:

    1. Eliminar **/Users/username/Library/Developer/Xamarin/Android-SDK-MacOSX/Android-21**
    2. Extraiga [1] en **/Users/username/Library/Developer/Xamarin/Android-SDK-MacOSX** para crear una carpeta **de Android-L** .

-----

[1]- [https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)
