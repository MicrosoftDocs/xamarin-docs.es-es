---
title: Características de marshmallow
description: Este artículo le ayudará a empezar a usar Xamarin. Android para desarrollar aplicaciones para Android 6,0 Marshmallow.
ms.prod: xamarin
ms.assetid: E4D6F183-98D2-460A-9D65-937639A899E0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 7d5f8d2e80a6583cf49af883db8f2f33e6496e09
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757473"
---
# <a name="marshmallow-features"></a>Características de marshmallow

_Este artículo le ayudará a empezar a usar Xamarin. Android para desarrollar aplicaciones para Android 6,0 Marshmallow._

En este artículo se proporciona un esquema de las nuevas características de Android 6,0 Marshmallow, se explica cómo preparar Xamarin. Android para el desarrollo de Android Marshmallow y se proporcionan vínculos a aplicaciones de ejemplo que muestran cómo hacer uso de las nuevas Marshmallow de Android características de las aplicaciones de Xamarin. Android. 

## <a name="overview"></a>Información general

[Android 6,0 Marshmallow](https://developer.android.com/about/versions/marshmallow/index.html), es la próxima versión principal de Android después de un círculo de Android.
Xamarin. Android es compatible con Android Marshmallow e incluye:

- **Enlaces de API 23/Android 6,0** &ndash; Android 6,0 agrega muchas API nuevas para las nuevas características que se describen a continuación; estas API están disponibles para las aplicaciones de Xamarin. Android cuando el destino es el nivel de API 23. Para obtener más información sobre las API de Android 6,0, consulte [API de android 6,0](https://developer.android.com/preview/api-overview.html). 

[![Imágenes prominentes de tabletas y teléfonos que ejecutan Marshmallow](marshmallow-images/android-m-hero-sml.png)](marshmallow-images/android-m-hero.png#lightbox)

Aunque la versión de marshmallow se centra principalmente en "polaco y calidad", también proporciona muchas características nuevas de interés para los desarrolladores de Xamarin. Android. Entre ellas se incluyen: 

- **Permisos de tiempo de ejecución** &ndash; Esta mejora permite a los usuarios aprobar los permisos de seguridad en el tiempo de ejecución. 

- **Mejoras de autenticación** A partir de Android Marshmallow, las aplicaciones pueden usar ahora sensores de huellas digitales para autenticar a los usuarios, y una nueva característica *confirmar credenciales* minimiza la necesidad de escribir contraseñas. &ndash; 

- **Vinculación de aplicaciones** Esta característica ayuda a eliminar la necesidad de hacer que el **selector de aplicaciones** se abra mediante la Asociación automática de aplicaciones con dominios Web. &ndash; 

- **Recurso compartido directo** Puede definir *destinos directos de recursos* compartidos que hacen que el uso compartido sea rápido e intuitivo para los usuarios; esta característica permite a UERs compartir contenido con otras aplicaciones. &ndash; 

- **Interacciones de voz** &ndash; Esta nueva API le permite crear características de voz de conversación en la aplicación. 

- **modo de presentación de 4k** &ndash; En Android Marshmallow, la aplicación puede solicitar una resolución de pantalla de 4k en el hardware que lo admita. 

- **Nuevas características de audio** &ndash; A partir de marshmallow, Android ahora es compatible con el protocolo MIDI. También proporciona nuevas clases para crear objetos de captura y reproducción de audio digital, y ofrece nuevos enlaces de API para asociar dispositivos de audio y de entrada. 

- **Nuevas características de vídeo** &ndash; Marshmallow proporciona una nueva clase que ayuda a las aplicaciones a representar secuencias de audio y vídeo en sincronización; esta clase también proporciona compatibilidad con la velocidad de reproducción dinámica. 

- **Android for work** &ndash; Marshmallow incluye controles mejorados para dispositivos de usuario único corporativos. Admite la instalación silenciosa y desinstalación de aplicaciones por parte del propietario del dispositivo, la aceptación automática de las actualizaciones del sistema, la administración mejorada de certificados, el seguimiento del uso de datos, la administración de permisos y las notificaciones de estado del trabajo. 

- **Biblioteca de compatibilidad de diseño de materiales** La nueva *biblioteca de soporte técnico de diseño* proporciona componentes y patrones de diseño que facilitan la compilación de la apariencia y el funcionamiento del diseño de material en la aplicación. &ndash; 

Además, muchas de las actualizaciones principales de la biblioteca de Android se lanzaron con Android M, y estas actualizaciones proporcionan nuevas características para Android M y versiones anteriores de Android.

Además, muchas de las actualizaciones principales de la biblioteca de Android se lanzaron con Android Marshmallow y estas actualizaciones proporcionan nuevas características para Android Marshmallow y versiones anteriores de Android. En este artículo se explica cómo empezar a compilar aplicaciones con Android Marshmallow y se proporciona información general sobre las nuevas características destacadas en Android 6,0. 

## <a name="requirements"></a>Requisitos

Para usar las nuevas características de Android Marshmallow en aplicaciones basadas en Xamarin, se requiere lo siguiente: 

- **Xamarin. Android** &ndash; Xamarin. Android 5.1.7.12 o posterior debe estar instalado y configurado con Visual Studio o Xamarin Studio.

- **Visual Studio para Mac** o **Visual Studio** &ndash; si usa Visual Studio para Mac, se requiere la versión 5.9.7.22 o posterior. Si usa Visual Studio, se requiere la versión 3.11.1537 o posterior de las herramientas de Xamarin para Visual Studio. 

- **Android SDK** &ndash; Android SDK 6,0 (API 23) o posterior debe instalarse a través del administrador de Android SDK.

- **Kit para desarrolladores de Java** Xamarin. Android requiere [JDK 1,8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o posterior si está desarrollando para el nivel de API 24 o superior (JDK 1,8 también admite los niveles de API anteriores a 24, incluido Marshmallow). &ndash; Se requiere la versión de 64 bits de JDK 1,8 Si usa controles personalizados o la vista previa de formularios.

Puede seguir usando [JDK 1,7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) si está desarrollando específicamente para el nivel de API 23 o una versión anterior. 

## <a name="getting-started"></a>Introducción

Para empezar a usar Android Marshmallow con Xamarin. Android, debe descargar e instalar las herramientas y los paquetes de SDK más recientes antes de poder crear un proyecto de marshmallow de Android: 

1. Instale las últimas actualizaciones de Xamarin desde el canal **estable** . 

2. Instale los paquetes y las herramientas del SDK de Android 6,0 Marshmallow.

3. Cree un nuevo proyecto de Xamarin. Android que tenga como destino Android 6,0 Marshmallow (nivel de API 23). 

4. Configurar un emulador o un dispositivo para Android Marshmallow.

En las secciones siguientes se explica cada uno de estos pasos:

### <a name="install-xamarin-updates"></a>Instalación de actualizaciones de Xamarin

Para actualizar Xamarin para que incluya compatibilidad con Android 6,0 Marshmallow, cambie el canal de actualización a **estable** e instale todas las actualizaciones. Para obtener más información sobre la instalación de actualizaciones desde el canal de actualizaciones, consulte [cambiar el canal de actualizaciones](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel). 

### <a name="install-the-android-60-sdk"></a>Instalación del SDK de Android 6,0

Para crear un proyecto de Xamarin. Android para Android Marshmallow, primero debe usar el administrador de Android SDK para instalar el SDK de Android 6,0:

- Inicie el administrador de Android SDK (en Visual Studio para Mac, use **herramientas > SDK Manager**; en Visual Studio, use **herramientas > Android > Android SDK Manager**) e instale el Android SDK Tools más reciente:

    [![Selección de Android SDK herramientas en el administrador de Android SDK](marshmallow-images/mnc-preview-tools.png)](marshmallow-images/mnc-preview-tools.png#lightbox)

- Además, instale los paquetes de SDK de **Android 6,0** más recientes:

    [![Selección de paquetes de SDK de Android 6,0 en el administrador de Android SDK](marshmallow-images/mnc-preview-packages.png)](marshmallow-images/mnc-preview-packages.png#lightbox)

Debe instalar Android SDK Tools revisión 24.3.4 o posterior.
Para obtener más información sobre cómo usar el administrador de Android SDK para instalar el SDK de Android 6,0, consulte [SDK Manager](https://developer.android.com/tools/help/sdk-manager.html).

### <a name="start-a-xamarinandroid-project"></a>Iniciar un proyecto de Xamarin. Android

Cree un nuevo proyecto de Xamarin. Android. Si no está familiarizado con el desarrollo de Android con Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para obtener información sobre la creación de proyectos de Android. 

Al crear un proyecto de Android, debe configurar las opciones de versión para que tengan como destino Android 6,0 MarshMallow. Para dirigirse al proyecto para Marshmallow, debe configurar el proyecto para el **nivel de API 23 (compatibilidad con Xamarin. Android v 6.0)** . Para más información sobre la configuración de niveles de nivel de API de Android, consulte [Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md).

### <a name="configure-an-emulator-or-device"></a>Configurar un emulador o un dispositivo

Si usa un emulador, inicie Android AVD Manager y cree un nuevo dispositivo con la siguiente configuración:

- Dispositivo: Nexus 5, 6 o 9.
- Destino: Android 6,0-nivel de API 23
- ABI: x86

Por ejemplo, este dispositivo virtual está configurado para emular un nexo 5:

[![Configuración de AVD con un dispositivo de Nexus 5, destino de Android 6,0 y Intel Atom (x86)](marshmallow-images/android-m-avd.png)](marshmallow-images/android-m-avd.png#lightbox)

Si usa un dispositivo físico como un nexo 5, 6 o 9, puede instalar una imagen de vista previa de Android Marshmallow. Para obtener más información sobre cómo actualizar el dispositivo a Android Marshmallow, consulte [imágenes del sistema de hardware](https://developer.android.com/preview/download.html#images).

## <a name="new-features"></a>Características nuevas

Muchos de los cambios introducidos en Android Marshmallow se centran en mejorar la experiencia del usuario de Android, el aumento del rendimiento y la corrección de errores. Sin embargo, Marshmallow también presentó algunos cambios importantes en los aspectos básicos de la plataforma Android. En las secciones siguientes se resaltan estas mejoras y se proporcionan vínculos para ayudarle a empezar a usar las nuevas características de Android Marshmallow en su aplicación. 

### <a name="runtime-permissions"></a>Permisos de tiempo de ejecución

El sistema de permisos de Android se ha optimizado y simplificado significativamente desde el círculo de Android. En Android Marshmallow, los usuarios conceden permisos en cada caso en tiempo de ejecución en lugar de en el momento de la instalación. Para admitir esta característica en Android Marshmallow y versiones posteriores, diseñe la aplicación para que solicite permisos al usuario en tiempo de ejecución (en el contexto en el que se necesitan los permisos). Este cambio facilita que los usuarios empiecen a usar la aplicación inmediatamente, ya que simplifica el proceso de instalación y actualización de la aplicación. 

Consulte [solicitar permisos en tiempo de ejecución en Android Marshmallow](https://blog.xamarin.com/requesting-runtime-permissions-in-android-marshmallow/) para más información (incluidos ejemplos de código) sobre la implementación de permisos en tiempo de ejecución en aplicaciones de Xamarin. Android.
Xamarin también proporciona una aplicación de ejemplo que muestra cómo funcionan los permisos en tiempo de ejecución en Android Marshmallow (y versiones posteriores): [RuntimePermissions](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-runtimepermissions).

En esta aplicación de ejemplo se muestra lo siguiente:

- Cómo comprobar y solicitar permisos en tiempo de ejecución.
- Cómo declarar permisos para dispositivos Android M.

Para usar esta aplicación de ejemplo:

1. Puntee en los botones **cámara** o **contactos** para mostrar un cuadro de diálogo de solicitud de permisos.
2. Conceder permiso para ver fragmentos de cámara o contactos.

Para obtener más información sobre las nuevas características de permisos en tiempo de ejecución en Android Marshmallow, consulte [trabajar con permisos del sistema](https://developer.android.com/preview/features/runtime-permissions.html).

### <a name="authentication-enhancements"></a>Mejoras de autenticación

Android Marshmallow incluye dos mejoras de autenticación que ayudan a eliminar la necesidad de contraseñas:

- **Autenticación mediante huella digital** &ndash; Usa un examen de huellas digitales para autenticar a los usuarios.

- **Confirmar credencial** &ndash; Autentica a los usuarios en función del tiempo que se ha desbloqueado el dispositivo.

Los vínculos y las aplicaciones de ejemplo que se describen a continuación pueden ayudarle a familiarizarse con estas nuevas características.

#### <a name="fingerprint-authentication"></a>Autenticación de huella digital

En los dispositivos que admiten hardware de análisis de huellas digitales, `FingerPrintManager` puede usar la nueva clase para autenticar a un usuario.
Para obtener más información sobre la característica de autenticación mediante huellas digitales en Android Marshmallow, consulte [autenticación mediante huellas digitales](https://developer.android.com/preview/api-overview.html#fingerprint-authentication).

Xamarin proporciona una aplicación de ejemplo que muestra cómo usar huellas digitales registradas para autenticar a un usuario en la aplicación: [FingerprintDialog](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog).

Para usar esta aplicación de ejemplo:

1. Pulse el botón **comprar** para abrir un cuadro de diálogo de autenticación mediante huellas digitales.
2. Digitalice la huella digital registrada para autenticarse.

Tenga en cuenta que esta aplicación de ejemplo requiere un dispositivo con un lector de huellas digitales.
Esta aplicación no almacena la huella digital (o la contraseña).

#### <a name="voice-interactions"></a>Interacciones de voz

La nueva característica de interacciones de voz introducida en Android Marshmallow permite a los usuarios de la aplicación usar su voz para confirmar acciones y seleccionar en una lista de opciones. Para obtener más información sobre las interacciones de voz, consulte [información general de la API de interacción de voz](https://developers.google.com/voice-actions/interaction/). 

Consulte [incorporación de una conversación a la aplicación Android con interacciones de voz](https://blog.xamarin.com/add-a-conversation-to-your-android-app-with-voice-interactions/) para más información (incluidos ejemplos de código) sobre cómo implementar interacciones de voz en aplicaciones de Xamarin. Android.
Hay disponible una aplicación de ejemplo que muestra cómo usar la API de interacción de voz en una aplicación de Xamarin. Android: [Interacciones de voz](https://github.com/jamesmontemagno/MarshmallowSamples/tree/master/VoiceInteractions).

#### <a name="confirm-credential"></a>Confirmar credencial

Con la nueva característica de *confirmación de credenciales* de Android Marshmallow, puede dejar que los usuarios tengan que recordar y especificar contraseñas específicas de la aplicación mediante su autenticación en función del tiempo que se ha desbloqueado su dispositivo.
Para ello, use el nuevo `SetUserAuthenticationValidityDurationSeconds` método `KeyGenerator`de. Use el `KeyGuardManager`método `CreateConfirmDeviceCredentialIntent` de para volver a autenticar al usuario desde dentro de la aplicación. Para obtener más información sobre esta nueva característica en Android Marshmallow, vea [CONFIRM Credential](https://developer.android.com/preview/api-overview.html#confirm-credential).

Xamarin proporciona una aplicación de ejemplo que muestra cómo usar las credenciales del dispositivo (como PIN, patrón o contraseña) en la aplicación: [ConfirmCredential](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-confirmcredential)

Para usar esta aplicación de ejemplo:

1. Configurar una pantalla de bloqueo segura en el dispositivo (**protección > seguridad > Screenlock**).
2. Puntee en el botón **comprar** y confirme las credenciales de la pantalla de bloqueo seguro.

### <a name="chrome-custom-tabs"></a>Pestañas personalizadas de Chrome

Los desarrolladores de aplicaciones se encuentran en una opción cuando un usuario pulsa una dirección URL: la aplicación puede iniciar un explorador o usar un explorador en la `WebView`aplicación basado en un. Ambas opciones presentan desafíos &ndash; que inician el explorador es un cambio de contexto intensivo que no se `WebView`pueden personalizar, mientras que s no comparten el estado con el explorador. Además, el uso `WebView`de s puede Agregar una sobrecarga de mantenimiento adicional. 

Las *pestañas personalizadas de Chrome* permiten mostrar fácilmente sitios web con el potencial de Chrome sin que los usuarios salgan de la aplicación. Esta característica ofrece a la aplicación más control sobre la experiencia web del usuario. hace que las transiciones entre el contenido nativo y web sean más fluidas sin tener `WebView`que recurrir a un. La aplicación también puede afectar a la apariencia y el aspecto de Chrome personalizando lo siguiente: 

- Color de la barra de herramientas

- Entrar y salir de animaciones

- Acciones personalizadas en la barra de herramientas de Chrome y en el menú de desbordamiento

- Preinicio y captura previa de Chrome (para una carga más rápida)

Para aprovechar esta característica en la aplicación de Xamarin. Android, descargue e instale la [biblioteca de pestañas personalizadas de compatibilidad con Android](https://www.nuget.org/packages/Xamarin.Android.Support.CustomTabs/).
Para obtener más información sobre esta característica, consulte [pestañas personalizadas de Chrome](https://developer.chrome.com/multidevice/android/customtabs).

### <a name="material-design-support-library"></a>Biblioteca de compatibilidad de diseño de materiales

El círculo de Android presentó el [diseño de material](http://www.google.com/design/spec/material-design/introduction.html) como un nuevo lenguaje de diseño para actualizar la experiencia de Android (consulte el [tema material](~/android/user-interface/material-theme.md) para obtener información sobre cómo usar el diseño de material en las aplicaciones de Xamarin. Android). Con Android Marshmallow, Google presentó la *biblioteca de compatibilidad de diseño de Android* para facilitar a los desarrolladores de aplicaciones la adopción de la apariencia y el funcionamiento del diseño del material. Esta biblioteca incluye los componentes siguientes:

- **CoordinatorLayout** El nuevo `CoordinatorLayout` widget es similar a, `FrameLayout`pero más eficaz que. &ndash; Puede usar `CoordinatorLayout` como contenedor para vistas secundarias o como un diseño de nivel superior, y proporciona un `layout_anchor` atributo que se puede usar para delimitar vistas en relación con otras vistas.

- **Contraer barras de herramientas** La nueva `CollapsingToolbarLayout` es una barra de la aplicación que se contrae que es un contenedor para `Toolbar`. &ndash; (Tenga en cuenta que la barra de la *aplicación* es lo que antes se conocía como la *barra de acciones*).

- **Botón de acción flotante** &ndash; Un botón redondo que denota la acción principal en la interfaz de la aplicación.

- **Etiquetas flotantes para editar texto** Usa un `TextInputLayout` nuevo`EditText`widget (que se ajusta) para mostrar una etiqueta flotante cuando se oculta una sugerencia cuando un usuario escribe texto. &ndash;

- **Vista de navegación** &ndash; El nuevo`NavigationView` widget le ayuda a usar el cajón de navegación de una manera más fácil de navegar por los usuarios.

- **Snackbar** &ndash; El nuevo`SnackBar` widget es un mecanismo de comentarios ligeros (similar a una notificación del sistema) que muestra un mensaje breve en la parte inferior de la pantalla, que aparece encima de los demás elementos de la pantalla.

- **Pestañas de material** &ndash; El nuevo`TabLayout` widget proporciona un diseño horizontal para mostrar las pestañas como forma de implementar la navegación de nivel superior en la aplicación.

Para beneficiarse de la [biblioteca de soporte técnico de diseño](https://developer.android.com/tools/support-library/features.html#design) de la aplicación de Xamarin. Android, descargue e instale el paquete NuGet de diseño de la [biblioteca de compatibilidad](https://www.nuget.org/packages/Xamarin.Android.Support.Design/) de Xamarin Xamarin.

Vea el [bonito diseño de material con la biblioteca de diseño de compatibilidad con Android](https://blog.xamarin.com/add-beautiful-material-design-with-the-android-support-design-library/) para más información (incluidos ejemplos de código) sobre el uso de la biblioteca de compatibilidad de diseño de materiales en aplicaciones de Xamarin. Android.
Xamarin proporciona una aplicación de ejemplo que muestra una demostración de la nueva biblioteca de diseño de &ndash; Android en Xamarin. Android [Cheesesquare](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-cheesesquare).
Este ejemplo muestra las siguientes características de la biblioteca de diseño:

- Barra de herramientas contraer
- Botón de acción flotante
- Ver delimitación
- NavigationView
- Snackbar

Para obtener más información sobre la biblioteca de diseño, vea [biblioteca de compatibilidad de diseño de Android](http://android-developers.blogspot.co.at/2015/05/android-design-support-library.html) en el blog del desarrollador de Android.

### <a name="additional-library-updates"></a>Actualizaciones adicionales de la biblioteca

Además de Android Marshmallow, Google ha anunciado actualizaciones relacionadas con varias bibliotecas básicas de Android. Xamarin proporciona compatibilidad con Xamarin. Android para estas actualizaciones a través de varios paquetes de NuGet de versión preliminar: 

- [Google Play Services](https://www.nuget.org/packages?q=Xamarin+Google+Play+Services) La versión más reciente de Google Play Services incluye la nueva característica de *invitaciones de aplicación* , que permite a los usuarios compartir su aplicación con amigos. &ndash; Para obtener más información sobre esta característica, vea el apartado sobre cómo [ampliar el alcance de su aplicación con los invitados de la aplicación de Google](https://blog.xamarin.com/expand-your-apps-reach-with-googles-app-invites/). 

- [Bibliotecas de compatibilidad con Android](https://www.nuget.org/packages?q=xamarin+support+library) &ndash; Estos paquetes Nuget ofrecen características que solo están disponibles para las API de biblioteca al mismo tiempo que proporcionan versiones compatibles con versiones anteriores de las API de Android Framework. 

- [Biblioteca portátil de Android](https://www.nuget.org/packages/Xamarin.Android.Wear) &ndash; este NuGet incluye enlaces de Google Play Services. La versión más reciente de la biblioteca portátil aporta nuevas características (incluida una navegación más sencilla para aplicaciones personalizadas) a la plataforma de desgaste de Android. 

## <a name="summary"></a>Resumen

En este artículo se presentaron Android Marshmallow y se explicó cómo instalar y configurar las herramientas y los paquetes más recientes para el desarrollo de Xamarin. Android en Marshmallow. También se proporciona información general sobre las nuevas características de Android Marshmallow para el desarrollo de Xamarin. Android.

## <a name="related-links"></a>Vínculos relacionados

- [Android 6,0 Marshmallow](https://developer.android.com/about/versions/marshmallow/index.html)
- [Obtener la Android SDK](https://developer.android.com/sdk/index.html#Other)
- [Información general sobre características](https://developer.android.com/preview/api-overview.html)
- [Notas de la versión](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1.99/index.md)
- [RuntimePermissions (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-runtimepermissions)
- [ConfirmCredential (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-confirmcredential)
- [FingerprintDialog (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
