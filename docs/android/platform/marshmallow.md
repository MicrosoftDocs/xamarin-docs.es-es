---
title: Características de Marshmallow
description: Este artículo le ayudará a empezar a usar Xamarin.Android para desarrollar aplicaciones para Android 6.0 Marshmallow.
ms.prod: xamarin
ms.assetid: E4D6F183-98D2-460A-9D65-937639A899E0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 52c44efa335d81004ce2e3dbf0d9160640118bea
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453550"
---
# <a name="marshmallow-features"></a>Características de Marshmallow

_Este artículo le ayudará a empezar a usar Xamarin.Android para desarrollar aplicaciones para Android 6.0 Marshmallow._

En este artículo se proporciona un esquema de las nuevas características de Android 6.0 Marshmallow, se explica cómo preparar Xamarin.Android para el desarrollo de Android Marshmallow y se proporcionan vínculos a aplicaciones de ejemplo que muestran cómo usar las nuevas características de Android Marshmallow en aplicaciones de Xamarin.Android. 

## <a name="overview"></a>Información general

[Android 6.0 Marshmallow](https://developer.android.com/about/versions/marshmallow/index.html) es la próxima versión principal de Android después de Android Lollipop.
Xamarin.Android es compatible con Android Marshmallow e incluye:

- **API 23/Android 6.0 Bindings**: Android 6.0 agrega muchas API nuevas para las nuevas características que se describen a continuación; estas API están disponibles para las aplicaciones de Xamarin.Android cuando se apunta al nivel de API 23. Para más información sobre las API de Android 6.0, consulte [API de Android 6.0](https://developer.android.com/preview/api-overview.html). 

[![Imágenes prominentes de tabletas y teléfonos que ejecutan Marshmallow](marshmallow-images/android-m-hero-sml.png)](marshmallow-images/android-m-hero.png#lightbox)

Aunque la versión de Marshmallow se centra principalmente en "mejora y calidad", también proporciona muchas características nuevas de interés para los desarrolladores de Xamarin.Android. Entre ellas se incluyen: 

- **Permisos en tiempo de ejecución**: esta mejora permite a los usuarios aprobar los permisos de seguridad según el caso en tiempo de ejecución. 

- **Mejoras en la autenticación**: a partir de Android Marshmallow, ahora las aplicaciones pueden usar sensores de huellas digitales para autenticar a los usuarios, y una nueva característica de *confirmación de las credenciales* minimiza la necesidad de escribir contraseñas. 

- **Vinculación de aplicaciones**: esta característica ayuda a eliminar la necesidad de tener el **selector de aplicaciones** emergente mediante la asociación automática de aplicaciones con dominios web. 

- **Uso compartido directo**: puede definir *destinos de recursos compartidos directos* que hacen que el uso compartido sea rápido e intuitivo para los usuarios. Esta característica permite a los usuarios compartir contenido con otras aplicaciones. 

- **Interacciones de voz**: esta nueva API le permite crear características de voz de conversación en la aplicación. 

- **Modo de visualización 4K**: en Android Marshmallow, la aplicación puede solicitar una resolución de pantalla 4K en el hardware que la admita. 

- **Nuevas características de audio**: a partir de Marshmallow, Android ahora es compatible con el protocolo MIDI. También proporciona nuevas clases para crear objetos de captura y reproducción de audio digital, y ofrece nuevos enlaces de API para asociar dispositivos de audio y de entrada. 

- **Nuevas características de vídeo**: Marshmallow proporciona una nueva clase que ayuda a las aplicaciones a representar secuencias de audio y vídeo sincronizadas. Esta clase también proporciona compatibilidad con la velocidad de reproducción dinámica. 

- **Android for Work**: Marshmallow incluye controles mejorados para dispositivos corporativos de un solo usuario. Admite la instalación y desinstalación silenciosa de aplicaciones por parte del propietario del dispositivo, la aceptación automática de las actualizaciones del sistema, la administración mejorada de certificados, el seguimiento del uso de datos, la administración de permisos y las notificaciones de estado del trabajo. 

- **Biblioteca de compatibilidad de diseño de materiales**: la nueva *biblioteca de compatibilidad de diseño* proporciona componentes y patrones de diseño que facilitan la compilación de la apariencia y el funcionamiento del diseño de materiales en la aplicación. 

Además, muchas de las actualizaciones principales de la biblioteca de Android se lanzaron con Android M, y estas actualizaciones proporcionan nuevas características para Android M y versiones anteriores de Android.

Además, muchas de las actualizaciones principales de la biblioteca de Android se lanzaron con Android Marshmallow, y estas actualizaciones proporcionan nuevas características para Android Marshmallow y versiones anteriores de Android. En este artículo se explica cómo empezar a compilar aplicaciones con Android Marshmallow y se proporciona información general sobre las nuevas características destacadas en Android 6.0. 

## <a name="requirements"></a>Requisitos

Para usar las nuevas características de Android Marshmallow en aplicaciones basadas en Xamarin, se requiere lo siguiente: 

- **Xamarin.Android**: Xamarin.Android 5.1.7.12 o posterior debe estar instalado y configurado con Visual Studio o Xamarin Studio.

- **Visual Studio para Mac** o **Visual Studio**: si usa Visual Studio para Mac, se requiere la versión 5.9.7.22 o una versión posterior. Si usa Visual Studio, se requiere la versión 3.11.1537 o una versión posterior de las herramientas de Xamarin para Visual Studio. 

- **Android SDK**: Android SDK 6.0 (API 23) o posterior debe estar instalado con el Administrador de Android SDK.

- **Kit para desarrolladores de Java**: Xamarin.Android requiere [JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o posterior si va a desarrollar para el nivel de API 24 o superior (JDK 1.8 también admite niveles de API anteriores a 24, incluido Marshmallow). Se requiere la versión de 64 bits del JDK 1.8 si usa controles personalizados o el controlador de vista previa de formularios.

Si está desarrollando contenido específicamente para el nivel de API 23 o uno anterior, puede continuar usando [JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html). 

## <a name="getting-started"></a>Introducción

Para empezar a usar Android Marshmallow con Xamarin.Android, debe descargar e instalar las herramientas y los paquetes de SDK más recientes antes de poder crear un proyecto de Android Marshmallow: 

1. Instale las últimas actualizaciones de Xamarin desde el canal **Stable**. 

2. Instale los paquetes y las herramientas del SDK de Android 6.0 Marshmallow.

3. Cree un proyecto de Xamarin.Android destinado a Android 6.0 Marshmallow (nivel de API 23). 

4. Configure un emulador o un dispositivo para Android Marshmallow.

En las siguientes secciones se explican cada uno de estos pasos:

### <a name="install-xamarin-updates"></a>Instalación de las actualizaciones de Xamarin

Para actualizar Xamarin para que incluya compatibilidad con Android 6.0 Marshmallow, cambie el canal de actualización a **Stable** e instale todas las actualizaciones. Para obtener más información sobre la instalación de actualizaciones desde el canal de actualizaciones, vea [Cambio del canal de actualizaciones](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel). 

### <a name="install-the-android-60-sdk"></a>Instalación de Android 6.0 SDK

Para crear un proyecto de Xamarin.Android para Android Marshmallow, primero debe usar el Administrador de Android SDK para instalar Android 6.0 SDK:

- Inicie el Administrador de Android SDK (en Visual Studio para Mac, use **Herramientas > Administrador de SDK**; en Visual Studio, use **Herramientas > Android > Administrador de Android SDK**) e instale la última versión de Android SDK Tools:

    [![Selección de herramientas de Android SDK en el Administrador de Android SDK](marshmallow-images/mnc-preview-tools.png)](marshmallow-images/mnc-preview-tools.png#lightbox)

- Además, instale los paquetes del SDK de **Android 6.0** más recientes:

    [![Selección de paquetes del SDK de Android 6.0 en el Administrador de Android SDK](marshmallow-images/mnc-preview-packages.png)](marshmallow-images/mnc-preview-packages.png#lightbox)

Debe instalar Android SDK Tools 24.3.4 o posterior.
Para más información sobre el uso del Administrador de Android SDK para instalar Android 6.0 SDK, vea [Administrador de SDK](https://developer.android.com/tools/help/sdk-manager.html).

### <a name="start-a-xamarinandroid-project"></a>Inicio de un proyecto de Xamarin.Android

Cree un nuevo proyecto de Xamarin.Android. Si no está familiarizado con el desarrollo de Android con Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para aprender sobre la creación de proyectos de Android. 

Cuando cree un proyecto de Android, asegúrese de configurar las opciones de versión para Android 6.0 MarshMallow. Para dirigirse al proyecto de Marshmallow, debe configurar el proyecto para **nivel de API 23 (compatibilidad con Xamarin.Android v6.0)** . Para obtener más información sobre la configuración de los niveles de API de Android, consulte [Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md).

### <a name="configure-an-emulator-or-device"></a>Configuración de un emulador o un dispositivo

Si usa un emulador, inicie Android AVD Manager y cree un nuevo dispositivo con la siguiente configuración:

- Dispositivo: Nexus 5, 6 o 9.
- Destino: Android 6.0 - Nivel de API 23
- ABI: x86

Por ejemplo, este dispositivo virtual está configurado para emular un dispositivo Nexus 5:

[![Configuración de AVD con un dispositivo Nexus 5, Android 6.0 como destino y Atom de Intel (x86)](marshmallow-images/android-m-avd.png)](marshmallow-images/android-m-avd.png#lightbox)

Si usa un dispositivo físico como Nexus 5, 6, o 9, puede instalar una imagen de vista previa de Android Marshmallow. Para obtener más información sobre cómo actualizar el dispositivo a Android Marshmallow, vea [Imágenes del sistema de hardware](https://developer.android.com/preview/download.html#images).

## <a name="new-features"></a>Características nuevas

Muchos de los cambios introducidos en Android Marshmallow se centran en mejorar la experiencia del usuario de Android, el aumento del rendimiento y la corrección de errores. Sin embargo, Marshmallow también presentó algunos cambios importantes en los aspectos básicos de la plataforma Android. En las secciones siguientes se resaltan estas mejoras y se proporcionan vínculos para ayudarle a empezar a usar las nuevas características de Android Marshmallow en la aplicación. 

### <a name="runtime-permissions"></a>Permisos en tiempo de ejecución

El sistema de permisos de Android se ha optimizado y simplificado significativamente a partir de Android Lollipop. En Android Marshmallow, los usuarios conceden permisos según el caso en tiempo de ejecución en lugar de en el momento de la instalación. Para admitir esta característica en Android Marshmallow y versiones posteriores, diseñe la aplicación para que solicite permisos al usuario en tiempo de ejecución (en el contexto en el que se necesitan los permisos). Este cambio facilita que los usuarios empiecen a usar la aplicación inmediatamente, ya que simplifica el proceso de instalación y actualización de la aplicación. 

Consulte [Solicitud de permisos en tiempo de ejecución en Android Marshmallow](https://blog.xamarin.com/requesting-runtime-permissions-in-android-marshmallow/) para obtener más información (incluidos ejemplos de código) sobre la implementación de permisos en tiempo de ejecución en aplicaciones de Xamarin.Android.
Xamarin también proporciona una aplicación de ejemplo que muestra cómo funcionan los permisos en tiempo de ejecución en Android Marshmallow y versiones posteriores: [RuntimePermissions](/samples/xamarin/monodroid-samples/android-m-runtimepermissions).

En esta aplicación de ejemplo se muestra lo siguiente:

- Cómo comprobar y solicitar permisos en tiempo de ejecución.
- Cómo declarar permisos para dispositivos Android M.

Para utilizar esta aplicación de ejemplo:

1. Pulse los botones **Cámara** o **Contactos** para mostrar un cuadro de diálogo de solicitud de permisos.
2. Conceda el permiso para ver fragmentos de cámara o contactos.

Para obtener más información sobre las nuevas características de los permisos en tiempo de ejecución en Android Marshmallow, vea [Trabajar con permisos del sistema](https://developer.android.com/preview/features/runtime-permissions.html).

### <a name="authentication-enhancements"></a>Mejoras en la autenticación

Android Marshmallow incluye dos mejoras de autenticación que ayudan a eliminar la necesidad de contraseñas:

- **Autenticación con huella digital**: usa un examen de la huella digital para autenticar a los usuarios.

- **Confirmar credenciales**: autentica a los usuarios en función del tiempo durante el cual el dispositivo ha estado desbloqueado.

Los vínculos y las aplicaciones de ejemplo que se describen a continuación pueden ayudarle a familiarizarse con estas nuevas características.

#### <a name="fingerprint-authentication"></a>Autenticación de huella digital

En los dispositivos que admiten hardware de análisis de huellas digitales, puede usar la nueva clase `FingerPrintManager` para autenticar a un usuario.
Para obtener más información sobre la característica de autenticación con huellas digitales de Android Marshmallow, vea [Autenticación con huella digital](https://developer.android.com/preview/api-overview.html#fingerprint-authentication).

Xamarin proporciona una aplicación de ejemplo que muestra cómo usar huellas digitales registradas para autenticar a un usuario en la aplicación: [FingerprintDialog](/samples/xamarin/monodroid-samples/android-m-fingerprintdialog).

Para utilizar esta aplicación de ejemplo:

1. Pulse el botón **Comprar** para abrir un cuadro de diálogo de autenticación con huella digital.
2. Digitalice la huella digital registrada para autenticarse.

Tenga en cuenta que esta aplicación de ejemplo requiere un dispositivo con un lector de huellas digitales.
Esta aplicación no almacena la huella digital ni la contraseña.

#### <a name="voice-interactions"></a>Interacciones de voz

La nueva característica de interacciones de voz introducida en Android Marshmallow permite a los usuarios de la aplicación usar su voz para confirmar acciones y seleccionar en una lista de opciones. Para más información sobre las interacciones de voz, vea [Información general sobre Voice Interaction API](https://developers.google.com/voice-actions/interaction/). 

Vea [Agregar una conversación a la aplicación Android con interacciones de voz](https://blog.xamarin.com/add-a-conversation-to-your-android-app-with-voice-interactions/) para obtener más información (incluidos ejemplos de código) sobre cómo implementar interacciones de voz en aplicaciones Xamarin.Android.
Hay disponible una aplicación de ejemplo que muestra cómo usar Voice Interaction API en una aplicación Xamarin.Android: [Interacciones de voz](https://github.com/jamesmontemagno/MarshmallowSamples/tree/master/VoiceInteractions).

#### <a name="confirm-credential"></a>Confirmar credenciales

Con la nueva característica *Confirmar credenciales* de Android Marshmallow, los usuarios ya no tendrán que recordar y escribir las contraseñas específicas de la aplicación mediante su autenticación en función del tiempo durante el cual su dispositivo ha estado desbloqueado.
Para ello, use el nuevo método `SetUserAuthenticationValidityDurationSeconds` de `KeyGenerator`. Use el método `CreateConfirmDeviceCredentialIntent` de `KeyGuardManager` para volver a autenticar al usuario desde dentro de la aplicación. Para más información sobre esta nueva característica en Android Marshmallow, vea [Confirmar credenciales](https://developer.android.com/preview/api-overview.html#confirm-credential).

Xamarin proporciona una aplicación de ejemplo que muestra cómo usar las credenciales del dispositivo (como PIN, patrón o contraseña) en la aplicación: [ConfirmCredential](/samples/xamarin/monodroid-samples/android-m-confirmcredential)

Para utilizar esta aplicación de ejemplo:

1. Configure una pantalla de bloqueo segura en el dispositivo (**Seguro > Seguridad > Bloqueo de pantalla**).
2. Pulse el botón **Comprar** y confirme las credenciales seguras de la pantalla de bloqueo.

### <a name="chrome-custom-tabs"></a>Pestañas personalizadas de Chrome

Los desarrolladores de aplicaciones deben elegir cuando un usuario pulsa una dirección URL: la aplicación puede iniciar un explorador o usar un explorador en la aplicación basado en un elemento `WebView`. Ambas opciones presentan desafíos; iniciar el explorador es un cambio de contexto intensivo que no se puede personalizar, mientras que los elementos `WebView` no comparten el estado con el explorador. Además, el uso de los elementos `WebView` puede agregar una sobrecarga de mantenimiento adicional. 

Las *pestañas personalizadas de Chrome* permiten mostrar sitios web de forma fácil y elegante con el potencial de Chrome sin que los usuarios salgan de la aplicación. Esta característica ofrece a la aplicación más control sobre la experiencia web del usuario; hace que las transiciones entre el contenido nativo y web sean más fluidas sin tener que recurrir a un elemento `WebView`. La aplicación también puede afectar a la apariencia y el aspecto de Chrome personalizando lo siguiente: 

- Color de la barra de herramientas

- Animaciones de entrada y salida

- Acciones personalizadas en la barra de herramientas de Chrome y en el menú de desbordamiento

- Inicio previo y captura previa de contenido de Chrome (para una carga más rápida)

Para beneficiarse de esta característica en la aplicación Xamarin.Android, descargue e instale la [biblioteca de pestañas personalizadas compatibles con Android](https://www.nuget.org/packages/Xamarin.Android.Support.CustomTabs/).
Para más información sobre esta característica, vea [Pestañas personalizadas de Chrome](https://developer.chrome.com/multidevice/android/customtabs).

### <a name="material-design-support-library"></a>Biblioteca de compatibilidad de diseño de materiales

Android Lollipop presentó el [diseño de materiales](https://www.google.com/design/spec/material-design/introduction.html) como un nuevo lenguaje de diseño para actualizar la experiencia de Android (consulte [Tema de materiales](~/android/user-interface/material-theme.md) para obtener información sobre cómo usar el diseño de materiales en las aplicaciones Xamarin.Android). Con Android Marshmallow, Google presentó la *biblioteca de compatibilidad de diseños de Android* para facilitar a los desarrolladores de aplicaciones la adopción de la apariencia y el funcionamiento del diseño del material. Esta biblioteca incluye los siguientes componentes:

- **CoordinatorLayout**: el nuevo widget `CoordinatorLayout` es similar, pero más eficaz que `FrameLayout`. Puede usar `CoordinatorLayout` como contenedor para vistas secundarias o como un diseño de nivel superior, y proporciona un atributo `layout_anchor` que se puede usar para delimitar vistas relacionadas con otras vistas.

- **Barras de herramientas de contracción**: el nuevo elemento `CollapsingToolbarLayout` es una barra de la aplicación que se contrae, que es un contenedor de `Toolbar`. (Tenga en cuenta que la *barra de aplicaciones* es lo que antes se conocía como la *barra de acciones*).

- **Botón de acción flotante**: un botón redondo que denota la acción principal en la interfaz de la aplicación.

- **Etiquetas flotantes para editar texto**: usa un nuevo widget `TextInputLayout` (que contiene `EditText`) para mostrar una etiqueta flotante cuando se oculta una sugerencia cuando un usuario escribe texto.

- **Vista de navegación**: el nuevo widget `NavigationView` le ayuda a usar el cajón de navegación, a fin de facilitar la navegación a los usuarios.

- **Snackbar**: el nuevo widget `SnackBar` es un mecanismo de comentarios ligero (similar a una notificación del sistema) que muestra un mensaje breve en la parte inferior de la pantalla, que aparece encima de los demás elementos de la pantalla.

- **Pestañas de materiales**: el nuevo widget `TabLayout` ofrece un diseño horizontal para mostrar las pestañas como forma de implementar la navegación de nivel superior en la aplicación.

Para aprovechar las ventajas de la [biblioteca de compatibilidad de diseño](https://developer.android.com/tools/support-library/features.html#design) en la aplicación Xamarin.Android, descargue e instale el paquete NuGet de [Diseño de la biblioteca de compatibilidad de Xamarin](https://www.nuget.org/packages/Xamarin.Android.Support.Design/).

Consulte [Diseño bonito de materiales con la biblioteca de diseño de compatibilidad de Android](https://blog.xamarin.com/add-beautiful-material-design-with-the-android-support-design-library/) para más información (incluidos ejemplos de código) sobre el uso de la biblioteca de compatibilidad de diseño de materiales en aplicaciones Xamarin.Android.
Xamarin proporciona una aplicación de ejemplo que presenta una demostración de la nueva biblioteca de diseño de Android en Xamarin.Android &ndash; [Cheesesquare](/samples/xamarin/monodroid-samples/android50-cheesesquare).
Este ejemplo muestra las siguientes características de la biblioteca de diseño:

- Barra de herramientas de contracción
- Botón de acción flotante
- Delimitación de vista
- Vista de navegación
- Snackbar

Para obtener más información sobre la biblioteca de diseño, vea [Biblioteca de compatibilidad de diseño de Android](https://android-developers.googleblog.com/2015/05/android-design-support-library.html) en el blog para desarrolladores de Android.

### <a name="additional-library-updates"></a>Actualizaciones adicionales de la biblioteca

Además de Android Marshmallow, Google ha anunciado actualizaciones relacionadas con varias bibliotecas básicas de Android. Xamarin proporciona compatibilidad con Xamarin.Android para estas actualizaciones mediante varios paquetes NuGet en versión preliminar: 

- [Google Play Services](https://www.nuget.org/packages?q=Xamarin+Google+Play+Services): la última versión de Google Play Services incluye la nueva característica *App Invites*, que permite a los usuarios compartir su aplicación con los amigos. Para obtener más información sobre esta característica, vea [Expanda el alcance de su aplicación con App Invites de Google](https://blog.xamarin.com/expand-your-apps-reach-with-googles-app-invites/). 

- [Bibliotecas de compatibilidad con Android](https://www.nuget.org/packages?q=xamarin+support+library): estos paquetes Nuget ofrecen características que solo están disponibles para las API de biblioteca, al mismo tiempo que proporcionan versiones compatibles con versiones anteriores de las API de Android Framework. 

- [Biblioteca portátil para Android](https://www.nuget.org/packages/Xamarin.Android.Wear): este paquete NuGet incluye enlaces a Google Play Services. La versión más reciente de la biblioteca portátil aporta nuevas características (incluida una navegación más sencilla para aplicaciones personalizadas) a la plataforma de Android Wear. 

## <a name="summary"></a>Resumen

En este artículo se presentó Android Marshmallow y se explicó cómo instalar y configurar las herramientas y los paquetes más recientes para el desarrollo de Xamarin.Android en Android Marshmallow. También se proporciona información general sobre las nuevas características de Android Marshmallow para el desarrollo de Xamarin.Android.

## <a name="related-links"></a>Vínculos relacionados

- [Android 6.0 Marshmallow](https://developer.android.com/about/versions/marshmallow/index.html)
- [Obtener Android SDK](https://developer.android.com/sdk/index.html#Other)
- [Información general de la característica](https://developer.android.com/preview/api-overview.html)
- [Notas de la versión](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1.99/index.md)
- [RuntimePermissions (ejemplo)](/samples/xamarin/monodroid-samples/android-m-runtimepermissions)
- [ConfirmCredential (ejemplo)](/samples/xamarin/monodroid-samples/android-m-confirmcredential)
- [FingerprintDialog (ejemplo)](/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)