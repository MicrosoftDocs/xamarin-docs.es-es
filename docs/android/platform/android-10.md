---
title: Características de Android 10
description: Cómo empezar a desarrollar aplicaciones para Android 10 con Xamarin.Android.
ms.assetid: B3342772-FB88-4B7F-BC15-8BC78EED749E
author: JonDouglas
ms.author: jodou
ms.date: 09/17/2019
ms.openlocfilehash: c19c9e5bd279824ea2d3e4e9f88857388f786a2c
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73612276"
---
# <a name="android-10-with-xamarin"></a>Android 10 con Xamarin

_Cómo empezar a desarrollar aplicaciones para Android 10 con Xamarin.Android_

Android 10 ahora está disponible en Google. Una serie de nuevas características y API están disponibles en esta versión, y muchas de ellas son necesarias para aprovechar las nuevas capacidades de hardware de los dispositivos Android más recientes.

![Logotipo de Android 10](~/android/platform/android-10-images/android10_black.png)

Este artículo está estructurado para ayudarle a empezar a desarrollar aplicaciones de Xamarin.Android para Android 10. Explica cómo instalar las actualizaciones necesarias, configurar el SDK y preparar un emulador o un dispositivo para realizar pruebas. También proporciona un esquema de las nuevas características de Android 10 y proporciona código fuente de ejemplo que muestra cómo usar algunas de las características clave de Android 10.

Xamarin.Android 10.0 proporciona compatibilidad con Android 10. Para más información sobre la compatibilidad de Xamarin.Android para Android 10, consulte las [notas de la versión de Xamarin.Android 10.0](https://docs.microsoft.com/xamarin/android/release-notes/10/10.0).

## <a name="requirements"></a>Requisitos

Para usar las características de Android 10 en aplicaciones basadas en Xamarin, se requiere lo siguiente:

- **Visual Studio**: se recomienda Visual Studio 2019. En Windows, actualice a Visual Studio 2019 versión 16.3 o posterior. En macOS, actualice a Visual Studio 2019 para Mac versión 8.3 o posterior.
- **Xamarin.Android**: Xamarin.Android 10.0 o posterior debe instalarse con Visual Studio (Xamarin.Android se instala automáticamente como parte de la carga de trabajo de **desarrollo móvil con .NET** en Windows y se instala como parte del **instalador de Visual Studio para Mac**).
- **Kit para desarrolladores de Java**: el desarrollo de Xamarin.Android 10.0 requiere JDK 8. La distribución de Microsoft de OpenJDK se instala automáticamente como parte de Visual Studio.
- **Android SDK**: Android SDK (API 29) o posterior debe estar instalado mediante el Administrador de Android SDK.

## <a name="get-started"></a>Primeros pasos

Para empezar a desarrollar aplicaciones de Android 10 con Xamarin.Android, debe descargar e instalar las herramientas y los paquetes de SDK más recientes antes de poder crear su primer proyecto de Android 10:

1. **Se recomienda Visual Studio 2019**. Actualice a Visual Studio 2019 versión 16.3 o posterior. Si usa Visual Studio para Mac 2019, actualice a Visual Studio 2019 para Mac versión 8.3 o posterior.
2. Instale los paquetes y herramientas de **Android 10 (API 29)** con el Administrador de SDK.
    - Plataforma del SDK de Android 10 (API 29)
    - Imagen del sistema Android 10 (API 29)
    - Herramientas de compilación de Android SDK 29.0.0+
    - Herramientas de plataforma de Android SDK 29.0.0+
    - Android Emulator 29.0.0+
3. Cree un nuevo proyecto de Xamarin.Android que se dirija a Android 10.0.
4. Configure un emulador o dispositivo para probar las aplicaciones de Android 10.

Cada uno de estos pasos se explica con detalle a continuación:

### <a name="update-visual-studio"></a>Actualizar Visual Studio

Se recomienda Visual Studio 2019 para crear aplicaciones de Android 10 con Xamarin.

Si usa Visual Studio 2019, actualice a Visual Studio 2019 versión 16.3 o posterior (para obtener instrucciones, consulte [Actualización de Visual Studio a la versión más reciente](https://docs.microsoft.com/visualstudio/install/update-visual-studio)). En macOS, actualice a Visual Studio 2019 para Mac 8.3 o posterior (para obtener instrucciones, consulte [Actualizar Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/update)).

### <a name="install-the-android-sdk"></a>Instalación de Android SDK

Para crear un proyecto con Xamarin.Android 10.0, primero debe usar el administrador de Android SDK para instalar la plataforma SDK para **Android 10 (nivel de API 29)** .

1. Inicie el Administrador de SDK. En Visual Studio, haga clic en **Herramientas > Android > Administrador de Android SDK**. En Visual Studio para Mac, haga clic en **Herramientas > Administrador de SDK**.
2. En la esquina inferior derecha, haga clic en el icono de engranaje y seleccione **Repositorio > Google (no compatible)** :

    ![Selección del repositorio de Administrador de Android SDK](~/android/platform/android-10-images/sdkrepository.png)

3. Instale los paquetes de la **plataforma del SDK de Android 10**, que se muestran como **Android SDK Platform 29** en la pestaña **Plataformas** (para más información acerca del uso del Administrador de SDK, consulte [Configuración del SDK de Android para Xamarin.Android](https://docs.microsoft.com/xamarin/android/get-started/installation/android-sdk)):

    ![Pestaña Plataforma del Administrador de Android SDK](~/android/platform/android-10-images/sdkplatforms.png)

### <a name="create-a-xamarinandroid-project"></a>Creación de un proyecto de Xamarin.Android

Cree un nuevo proyecto de Xamarin.Android. Si no está familiarizado con el desarrollo de Android con Xamarin, consulte [Hello, Android](https://docs.microsoft.com/xamarin/android/get-started/hello-android/index) para aprender sobre la creación de proyectos de Xamarin.Android.

Cuando cree un proyecto de Android, asegúrese de configurar las opciones de versión para Android 10.0. Por ejemplo, para que el proyecto tenga como destino Android 10, debe configurar el nivel de API de Android de destino del proyecto como **Android 10.0 (API 29)** . Esto incluye la **versión de la plataforma de destino** y la **versión de Android SDK de destino** a la API 29 o posterior. (Para más información sobre los niveles de API de Android, consulte [Descripción de los niveles de API de Android](https://docs.microsoft.com/xamarin/android/app-fundamentals/android-api-levels)).

![Plataforma de destino de Xamarin.Android](~/android/platform/android-10-images/targetframework.png)

### <a name="configure-a-device-or-emulator"></a>Configuración de un dispositivo o emulador

Si usa un dispositivo físico, como un píxel, puede descargar la actualización de Android 10. Para ello, vaya a `System` > `System update` > `Check for update` en la configuración del teléfono. Si prefiere que el dispositivo parpadee, consulte las instrucciones para hacer parpadear una [imagen de fábrica](https://developers.google.com/android/images) o [imagen de OTA](https://developers.google.com/android/ota) en el dispositivo.

Si usa un emulador, cree un dispositivo virtual para el nivel de API 29 y seleccione una imagen basada en x86. Para más información sobre el uso del Administrador de dispositivos Android para crear y administrar dispositivos virtuales, consulte [Administración de dispositivos virtuales con Android Device Manager](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/device-manager). Para más información sobre cómo iniciar y depurar con Android Emulator, consulte [Depuración en Android Emulator](https://docs.microsoft.com/xamarin/android/deploy-test/debugging/debug-on-emulator).

## <a name="new-features"></a>Características nuevas

Android 10 presenta una variedad de características nuevas. Algunas de estas nuevas características están pensadas para aprovechar las nuevas funcionalidades de hardware que ofrecen los dispositivos Android más recientes, mientras que otras están diseñadas para mejorar aún más la experiencia del usuario de Android:

## <a name="enhance-your-app-with-android-10-features-and-apis"></a>Mejora de la aplicación con características y API de Android 10

Después, cuando esté listo, profundice en Android 10 y obtenga información sobre las [nuevas características y API](https://developer.android.com/preview/api-overview.html) que puede usar. Estas son algunas de las características principales con las que empezar a trabajar.

Estas características se recomiendan para todas las aplicaciones:

- **Compatibilidad con un tema oscuro** para garantizar una experiencia unificada para aquellos usuarios que habiliten el [tema oscuro](https://developer.android.com/preview/features/darktheme)en todo el sistema o habilitar  [Force Dark](https://developer.android.com/preview/features/darktheme#force_dark).

![Tema oscuro](~/android/platform/android-10-images/darktheme.png)

- **Compatibilidad [con la navegación mediante gestos](https://developer.android.com/preview/features/gesturalnav)**  en la aplicación al ir de extremo a extremo y asegurarse de que los gestos personalizados se complementen con los gestos de navegación del sistema.

![Navegación mediante gestos](~/android/platform/android-10-images/gesturenavigation.png)

- **Optimización para dispositivos plegables:**  para ofrecer experiencias de vanguardia en los dispositivos innovadores actuales mediante la  [optimización para dispositivos plegables](https://developer.android.com/preview/features/foldables).

![Dispositivo plegable](~/android/platform/android-10-images/foldable.png)

Se recomiendan estas características si es pertinente para su aplicación:

- **Notificaciones más interactivas:**  si las notificaciones incluyen mensajes, habilite [respuestas y acciones sugeridas en las notificaciones](https://developer.android.com/preview/features#smart-suggestions) para atraer a los usuarios y permitirles actuar al instante.
- **Mejor biometría:**  si usa la autenticación biométrica, vaya a [BiometricPrompt](https://developer.android.com/reference/androidx/biometric/BiometricPrompt), la manera preferida de admitir la autenticación mediante huellas digitales en dispositivos modernos.
- **Grabación enriquecida:**  para admitir la grabación de subtítulos o juegos y habilitar [la captura de reproducción de audio](https://developer.android.com/preview/features/playback-capture). Es una excelente manera de llegar a más usuarios y hacer que la aplicación sea más accesible.
- **Mejores códecs:**  para aplicaciones multimedia, pruebe [AV1](https://en.wikipedia.org/wiki/AV1) para el streaming de vídeo y [HDR10 +](https://en.wikipedia.org/wiki/High-dynamic-range_video#HDR10+) para un vídeo de alto rango dinámico. Para el streaming de voz y música, puede usar la codificación [Opus](http://opus-codec.org/) y, para los músicos, está disponible una [API de MIDI nativa](https://developer.android.com/preview/features/midi) .
- **Mejores API de red:**  si la aplicación administra dispositivos IoT a través de Wi-Fi, pruebe las nuevas  [API de conexión de red](https://developer.android.com/preview/features#peer2peer) para funciones como la configuración, descarga o impresión.

Estas son solo algunas de las muchas características y API nuevas de Android 10. Para verlas todas, visite el sitio de [Android 10 para desarrolladores](https://developer.android.com/about/versions/10/highlights).

## <a name="behavior-changes"></a>Cambios de comportamiento

Cuando la versión de Android de destino se establece en el nivel de API 29, hay varios cambios de plataforma que se verán afectados por el comportamiento de la aplicación, incluso si no está implementando las nuevas características descritas anteriormente. La lista siguiente es un breve resumen de estos cambios:

- [Para garantizar la estabilidad y la compatibilidad de la aplicación, la plataforma Android ahora restringe las interfaces que no son de SDK que puede usar la aplicación en Android 10](https://developer.android.com/about/versions/10/behavior-changes-10#non-sdk-restrictions).
- [La memoria compartida ha cambiado](https://developer.android.com/about/versions/10/behavior-changes-10#shared-memory).
- [Tiempo de ejecución de Android y corrección de AOT](https://developer.android.com/about/versions/10/behavior-changes-10#system-only-oat).
- [Los permisos para las intenciones de pantalla completa deben solicitar `USE_FULL_SCREEN_INTENT`](https://developer.android.com/about/versions/10/behavior-changes-10#full-screen-intents).
- [Compatibilidad con dispositivos plegables](https://developer.android.com/about/versions/10/behavior-changes-10#foldables).

## <a name="summary"></a>Resumen

En este artículo se presentó Android 10 y se explicó cómo instalar y configurar las herramientas y los paquetes más recientes para el desarrollo de Xamarin.Android en Android 10. Proporciona información general sobre las principales características disponibles en Android 10. Incluye vínculos a la documentación de la API y temas para desarrolladores de Android que le ayudarán a empezar a crear aplicaciones para Android 10. También se resaltan los cambios de comportamiento más importantes de Android 10 que pueden afectar a las aplicaciones existentes.

## <a name="related-links"></a>Vínculos relacionados

- [Android 10](https://developer.android.com/about/versions/10)
