---
title: ¿Qué versión de Xamarin.Android agrega compatibilidad con Lollipop?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63B6E10C-098D-4C82-9253-07CA62EA85A5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 27631d19ea157c1af08666b55a067e729b806434
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453641"
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>¿Qué versión de Xamarin.Android agrega compatibilidad con Lollipop?

> [!NOTE]
> Esta guía se escribió originalmente para la versión preliminar de Android L.

- [Xamarin.Android 4.17](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.17/index.md) agrega compatibilidad con la versión preliminar de Android L.
- [Xamarin.Android 4.20](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.20/index.md) agrega compatibilidad con la versión preliminar de Android L.

Xamarin solo admite activamente la versión estable actual de las herramientas de Xamarin. La siguiente información se proporciona "tal cual" para las versiones anteriores de las herramientas. Para obtener la información más reciente sobre las versiones de Xamarin, consulte las [notas de la versión](/xamarin/android/release-notes/).

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>"Falta android.jar para el nivel de API 21" en la versión preliminar de Android L

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Puede aparecer el siguiente mensaje de error (o similar):

```cmd
Error 1 Could not find android.jar for API Level 21.
```

Este mensaje significa que la plataforma de Android SDK para el nivel API 21 no está instalada. Instálela en Android SDK Manager (**Herramientas > Abrir Android SDK Manager...** ) o cambie el proyecto de Xamarin.Android para que tenga como destino una versión de API que esté instalada.

Hay unas cuantas soluciones alternativas para este problema:

1. Cambie el proyecto para que tenga como destino la API 19 o una inferior.

2. Cambie el nombre de la carpeta android-21 de android-21 a android-L. (Lo mejor es que solo se use como una corrección temporal y puede que no funcione muy bien).

   **%LOCALAPPDATA%\\Android\\android-sdk\\platforms\\android-21**

3. Cambie temporalmente a la versión preliminar anterior de Android "L" del nivel de API 21 [1]:

    1. Elimine **%LOCALAPPDATA%\\Android\\android-sdk\\platforms\\android-21** 
    2. Extraiga [1] en **C:\\Usuarios\\&lt;nombre_usuario&gt;\\AppData\\Local\\Android\\android-sdk\\platforms** para crear una carpeta **android-L**.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Puede aparecer el siguiente mensaje de error (o similar):

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

Esto significa que la plataforma de Android SDK para el nivel de API 21 no está instalada. Instálela en Android SDK Manager (Herramientas > SDK Manager...) o cambie el proyecto Xamarin.Android para que tenga como destino una versión de API que esté instalada.

Hay unas cuantas soluciones alternativas para este problema:

1. Cambie el proyecto para que tenga como destino la API 19 o una inferior.

2. Cambie el nombre de la carpeta android-21 de android-21 a android-L. (Lo mejor es que solo se use como una corrección temporal y puede que no funcione muy bien).

   **~/Library/Developer/Xamarin/android-sdk-macosx/android-21**

3. Cambie temporalmente a la versión preliminar anterior de Android "L" del nivel de API 21 [1]:

    1. Elimine **/Ususarios/nombre_usuario/Library/Developer/Xamarin/android-sdk-macosx/android-21**
    2. Extraiga [1] en **/Ususarios/nombre_usuario/Library/Developer/Xamarin/android-sdk-macosx** para crear una carpeta **android-L**.

-----

[1]: [https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)