---
title: Características de nougat
description: Cómo empezar a usar Xamarin. Android para desarrollar aplicaciones para Android nougat.
ms.prod: xamarin
ms.assetid: 5C74ABE2-C862-4ED0-8EA5-C7FEE5251D4B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/02/2018
ms.openlocfilehash: 128982abdee7a0fea8df79f7b7b9ecd6a290775a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761257"
---
# <a name="nougat-features"></a>Características de nougat

_Cómo empezar a usar Xamarin. Android para desarrollar aplicaciones para Android nougat._

En este artículo se proporciona un esquema de las características introducidas en Android nougat, se explica cómo preparar Xamarin. Android para el desarrollo de Android nougat y se proporcionan vínculos a aplicaciones de ejemplo que muestran cómo usar las características de Android nougat en Aplicaciones de Xamarin. Android.

## <a name="overview"></a>Información general

[Android nougat](https://developer.android.com/about/versions/nougat/android-7.0.html) es el seguimiento de Google para Android 6,0 Marshmallow. Xamarin. Android proporciona compatibilidad con **enlaces Android 7. x** en Xamarin Android 7,0 y versiones posteriores. Android nougat agrega muchas API nuevas para las características de nougat que se describen a continuación: estas API están disponibles para las aplicaciones de Xamarin. Android cuando se usa Xamarin. Android 7,0.

[![Imágenes prominentes de tabletas y teléfonos Android con Android nougat](nougat-images/android-n-hero-sml.png)](nougat-images/android-n-hero.png#lightbox)

Para obtener más información sobre las API de Android 7. x, consulte [android 7,1 para desarrolladores](https://developer.android.com/preview/api-overview.html).
Para obtener una lista de problemas conocidos de Xamarin. Android 7,0, consulte las notas de la [versión](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md).

Android nougat proporciona muchas características nuevas de interés para los desarrolladores de Xamarin. Android. Entre ellas se incluyen:

- **Compatibilidad con varias ventanas** &ndash; Esta mejora permite a los usuarios abrir dos aplicaciones en la pantalla al mismo tiempo.

- **Mejoras en las notificaciones** El sistema de notificaciones rediseñada en Android nougat incluye una característica de *respuesta directa* que permite a los usuarios responder rápidamente a los mensajes de texto directamente desde la interfaz de usuario de notificaciones. &ndash; Además, si la aplicación crea notificaciones para los mensajes recibidos, la nueva característica de *notificaciones integradas* puede agrupar las notificaciones como un solo grupo cuando se recibe más de un mensaje.

- **Protector de datos** &ndash; Esta característica es un nuevo servicio del sistema que ayuda a reducir el uso de datos móviles por parte de las aplicaciones; ofrece a los usuarios el control sobre cómo usan los datos móviles los datos de las aplicaciones.

Además, Android nougat ofrece muchas otras mejoras de interés para los desarrolladores de aplicaciones, como una nueva característica de configuración de seguridad de red, Doze sobre la marcha, atestación de clave, nuevas API de configuración rápida, compatibilidad con múltiples configuraciones regionales, API de ICU4J, mejoras de WebView, acceso a las características del lenguaje Java 8, acceso a directorios de ámbito, una API de puntero personalizada, compatibilidad con plataforma VR, archivos virtuales y optimizaciones de procesamiento en segundo plano.

En este artículo se explica cómo empezar a compilar aplicaciones con Android nougat para probar las nuevas características y planear la migración o el trabajo de características para dirigirse a la nueva plataforma Android nougat.

## <a name="requirements"></a>Requisitos

Para usar las nuevas características de Android nougat en aplicaciones basadas en Xamarin, se requiere lo siguiente:

- **Visual Studio o Visual Studio para Mac** &ndash; Si usa Visual Studio, se requiere la versión 4.2.0.628 o posterior de Visual Studio Tools para Xamarin. Si usa Visual Studio para Mac, se requiere la versión 6.1.0 o posterior de Visual Studio para Mac.

- **Xamarin. Android** &ndash; Xamarin. Android 7,0 o posterior debe estar instalado y configurado con Visual Studio o Visual Studio para Mac.

- **Android SDK** Android SDK 7,0 (API 24) o posterior debe instalarse a través del administrador de Android SDK.

- **Kit para desarrolladores de Java** El desarrollo de Xamarin Android 7,0 requiere [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o posterior si está desarrollando para el nivel de API 24 o superior (JDK 8 también admite niveles de API anteriores a 24). &ndash; Se requiere la versión de 64 bits de JDK 8 Si usa controles personalizados o la vista previa de formularios.

> [!IMPORTANT]
> Xamarin.Android no admite JDK 9.

Tenga en cuenta que las aplicaciones se deben volver a generar con Xamarin C6SR4 o una versión posterior para que funcionen de forma confiable con Android nougat. Dado que Android nougat solo puede vincularse a las [bibliotecas nativas proporcionadas por NDK](https://developer.android.com/about/versions/nougat/android-7.0-changes.html), es posible que las aplicaciones existentes que usan bibliotecas como **mono. Data. SQLite. dll** se bloqueen al ejecutarse en Android nougat si no se vuelven a generar correctamente.

## <a name="getting-started"></a>Introducción

Para empezar a usar Android nougat con Xamarin. Android, debe descargar e instalar las herramientas y los paquetes de SDK más recientes antes de poder crear un proyecto de nougat de Android:

1. Instale las últimas actualizaciones de Xamarin. Android desde Xamarin.

2. Instale los paquetes y las herramientas de **Android 7,0 (API 24)** o versiones posteriores.

3. Cree un nuevo proyecto de Xamarin. Android que tenga como destino Android nougat.

4. Configurar un emulador o un dispositivo para Android nougat.

En las secciones siguientes se explica cada uno de estos pasos:

### <a name="install-xamarin-updates"></a>Instalación de actualizaciones de Xamarin

Para agregar compatibilidad con Xamarin para Android nougat, cambie el canal de actualizaciones en Visual Studio o Visual Studio para Mac al canal estable y aplique las actualizaciones más recientes. Si también necesita características que solo están disponibles en el canal alfa o beta, puede cambiar al canal alfa o beta (los canales alfa y beta también proporcionan compatibilidad con Android 7. x). Para obtener información sobre cómo cambiar el canal de actualizaciones (versiones), consulte [cambiar el canal de actualizaciones](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel).

### <a name="install-the-android-sdk"></a>Instalar el Android SDK

Para crear un proyecto con Xamarin Android 7,0, primero debe usar el administrador de Android SDK para instalar la **plataforma SDK Android N (API 24)** o posterior. También debe instalar la **Android SDK Tools**más reciente:

1. Inicie el administrador de Android SDK (en Visual Studio para Mac, use **Herramientas > abrir el&hellip;administrador de Android SDK**; en Visual Studio, use **Herramientas > Android > Android SDK Manager**).

2. Instale **Android 7,0 (API 24)** o posterior:

    [![Selección de paquetes de Android 7,0 en el administrador de Android SDK](nougat-images/preview-packages.png)](nougat-images/preview-packages.png#lightbox)

3. Instale las herramientas de Android SDK más recientes:

    [![Selección de las herramientas de Android SDK más recientes en el administrador de Android SDK](nougat-images/preview-tools.png)](nougat-images/preview-tools.png#lightbox)

    Debe instalar Android SDK Tools revisión 25.2.2 o posterior, Android SDK Platform Tools 24.0.3 o posterior y Android SDK Build Tools 24.0.2 o posterior.

4. Compruebe que la **Ubicación del kit de desarrollo de Java** está configurada para JDK 1,8:

    [![Configuración de la ruta de acceso de JDK 8 en herramientas opciones](nougat-images/use-jdk-1.8.png)](nougat-images/use-jdk-1.8.png#lightbox)

    Para ver esta configuración en Visual Studio, haga clic en **herramientas > opciones > Xamarin > configuración de Android**. En Visual Studio para Mac, haga clic en **preferencias > proyectos > ubicaciones del SDK > Android**.

### <a name="start-a-xamarinandroid-project"></a>Iniciar un proyecto de Xamarin. Android

Cree un nuevo proyecto de Xamarin. Android. Si no está familiarizado con el desarrollo de Android con Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para obtener información sobre la creación de proyectos de Xamarin. Android.

Al crear un proyecto de Android, debe configurar las opciones de versión para que tengan como destino Android 7,0 o posterior. Por ejemplo, para tener como destino el proyecto para Android 7,0, debe configurar el nivel de API de Android de destino de su proyecto en **android 7,0 (API 24-nougat)** . Se recomienda establecer el nivel de plataforma de destino en API 24 o posterior. Para más información sobre la configuración de niveles de nivel de API de Android, consulte [Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md).

> [!NOTE]
> Actualmente, debe establecer la **versión mínima de Android** en **Android 7,0 (API 24-nougat)** para implementar la aplicación en dispositivos Android nougat o emuladores.

### <a name="configure-an-emulator-or-device"></a>Configurar un emulador o un dispositivo

Si usa un emulador, inicie Android AVD Manager y cree un nuevo dispositivo con la siguiente configuración:

- Dispositivo: Nexus 5X, Nexus 6, el 6P de Nexus, el jugador de Nexus, el Nexus 9 o el píxel C.
- Destino: Android 7,0-nivel de API 24
- Abi: x86 o x86\_64

Por ejemplo, este dispositivo virtual está configurado para emular un nexo 6:

[![Configuración de AVD con un dispositivo de Nexus 6, destino de Android 7,0 y CPU/ABI x86 de Intel Atom](nougat-images/android-n-avd.png)](nougat-images/android-n-avd.png#lightbox)

Si usa un dispositivo físico, como un nexo de Nexus 5, 6 o 9, puede actualizar el dispositivo a través de las actualizaciones automáticas por aire (OTA) o descargar una imagen del sistema y hacer que el dispositivo parpadee directamente. Para obtener más información sobre cómo actualizar manualmente el dispositivo a Android nougat, consulte [imágenes de OTA para dispositivos Nexus](https://developers.google.com/android/nexus/ota).

Tenga en cuenta que los dispositivos de Nexus 5 no se admiten en Android nougat.

## <a name="new-features"></a>Características nuevas

Android nougat presenta una variedad de características y funcionalidades nuevas, como la compatibilidad con varias ventanas, las mejoras de notificaciones y el protector de datos. En las secciones siguientes se resaltan estas características y se proporcionan vínculos para ayudarle a empezar a usarlas en la aplicación.

### <a name="multi-window-mode"></a>Modo de varias ventanas

El modo de varias ventanas permite a los usuarios abrir dos aplicaciones a la vez con una compatibilidad completa con varias tareas. Estas aplicaciones pueden ejecutarse en paralelo (horizontal) o una por encima de la otra (vertical) en el modo de pantalla dividida.
Los usuarios pueden arrastrar un divisor entre las aplicaciones para cambiar su tamaño y pueden cortar y pegar contenido entre las aplicaciones. Cuando se presentan dos aplicaciones en el modo de varias ventanas, la actividad seleccionada continúa ejecutándose mientras la actividad no seleccionada se pausa pero sigue estando visible. El modo de varias ventanas no modifica el ciclo de vida de la actividad de Android.

[![Aplicaciones de ejemplo que se ejecutan en modo de varias ventanas en vertical y horizontal](nougat-images/multi-window-mode.png)](nougat-images/multi-window-mode.png#lightbox)

Puede configurar cómo las actividades de la aplicación de Xamarin. Android admiten el modo de varias ventanas. Por ejemplo, puede configurar atributos que establezcan el tamaño mínimo y el alto y ancho predeterminados de la aplicación en el modo de varias ventanas. Puede usar la nueva `Activity.IsInMultiWindowMode` propiedad para determinar si la actividad está en modo de varias ventanas. Por ejemplo:

```csharp
if (!IsInMultiWindowMode) {
    multiDisabledMessage.Visibility = ViewStates.Visible;
} else {
    multiDisabledMessage.Visibility = ViewStates.Gone;
}
```

La aplicación de ejemplo [MultiWindowPlayground](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-multiwindowplayground) incluye código C# que muestra cómo sacar partido de varias interfaces de usuario de ventana con la aplicación.

Para obtener más información sobre el modo de varias ventanas, vea [compatibilidad con varias ventanas](https://developer.android.com/guide/topics/ui/multi-window.html).

### <a name="enhanced-notifications"></a>Notificaciones mejoradas

Android nougat introduce un sistema de notificación rediseñado. Incluye una nueva característica de respuesta directa que permite a los usuarios responder rápidamente a las notificaciones de mensajes de texto entrantes directamente en la interfaz de usuario de notificaciones. A partir de Android 7,0, los mensajes de notificación se pueden agrupar como un solo grupo cuando se recibe más de un mensaje. Además, los desarrolladores pueden personalizar las vistas de notificación, aprovechar las decoraciones del sistema en las notificaciones y aprovechar las nuevas plantillas de notificación al generar notificaciones.

#### <a name="direct-reply"></a>Respuesta directa

Cuando un usuario recibe una notificación de mensaje entrante, Android nougat hace posible responder al mensaje dentro de la notificación (en lugar de abrir la aplicación de mensajería para enviar una respuesta).
Esta característica de respuesta en línea permite a los usuarios responder rápidamente a un mensaje SMS o de texto directamente dentro de la interfaz de notificación:

[![Captura de pantalla de una notificación con un campo de respuesta directa en línea](nougat-images/notifications-inline-reply-sml.png)](nougat-images/notifications-inline-reply.png#lightbox)

Para admitir esta característica en la aplicación, debe agregar *acciones de respuesta en línea* a la aplicación a través de un objeto [RemoteInput](xref:Android.App.RemoteInput) para que los usuarios puedan responder a través del texto directamente desde la interfaz de usuario de notificaciones.
Por ejemplo, el código siguiente genera un `RemoteInput` para recibir la entrada de texto, crea un intento pendiente para la acción de respuesta y crea una acción de entrada remota habilitada:

```csharp
// Build a RemoteInput for receiving text input:
var remoteInput = new Android.Support.V4.App.RemoteInput.Builder (EXTRA_REMOTE_REPLY)
    .SetLabel (GetString (Resource.String.reply))
    .Build ();

// Build a Pending Intent for the reply action to trigger:
PendingIntent replyIntent = PendingIntent.GetBroadcast (ApplicationContext,
                                conversation.ConversationId,
                                GetMessageReplyIntent (conversation.ConversationId),
                                PendingIntentFlags.UpdateCurrent);

// Build an Android 7.0 compatible Remote Input enabled action:
NotificationCompat.Action actionReplyByRemoteInput = new NotificationCompat.Action.Builder (
    Resource.Drawable.notification_icon,
    GetString (Resource.String.reply),
    replyIntent).AddRemoteInput (remoteInput).Build ();
```

Esta acción se agrega a la notificación:

```csharp
// Create the notification:
NotificationCompat.Builder builder = new NotificationCompat.Builder (ApplicationContext)
   .SetSmallIcon (Resource.Drawable.notification_icon)
   ...
   .AddAction (actionReplyByRemoteInput);
```

La aplicación de ejemplo del servicio C# de `RemoteInput` [Mensajería](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-messagingservice) incluye código que muestra cómo extender las notificaciones con un objeto. Para obtener más información sobre cómo agregar acciones de respuesta en línea a la aplicación para Android 7,0 o posterior, consulte el tema respuesta de Android [a las notificaciones](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#direct) .

#### <a name="bundled-notifications"></a>Notificaciones agrupadas

Los Nougats de Android pueden agrupar los mensajes de notificación juntos (por ejemplo, por tema de mensaje) y mostrar el grupo en lugar de cada mensaje independiente.
Esta característica de *notificaciones integradas* permite a los usuarios descartar o archivar un grupo de notificaciones en una acción. El usuario puede desplazarse hacia abajo para expandir el paquete de notificaciones para ver cada notificación en detalle:

[![Ejemplo de captura de pantalla de notificaciones integradas](nougat-images/bundled-notifications-sml.png)](nougat-images/bundled-notifications.png#lightbox)

Para admitir notificaciones integradas, la aplicación puede usar el método [Builder. SetGroup](xref:Android.App.Notification.Builder.SetGroup*) para agrupar notificaciones similares. Para obtener más información sobre los grupos de notificación agrupados en Android N, vea el tema sobre las [notificaciones de empaquetado](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#bundle) de Android.

#### <a name="custom-views"></a>Vistas personalizadas

Android nougat permite crear vistas de notificación personalizadas con encabezados de notificación del sistema, acciones y diseños expansibles. Para obtener más información sobre las vistas de notificaciones personalizadas en Android nougat, consulte el tema mejoras en las [notificaciones](https://developer.android.com/about/versions/nougat/android-7.0.html#notification_enhancements) de Android.

### <a name="data-saver"></a>Protector de datos

A partir de Android nougat, los usuarios pueden habilitar una nueva configuración del *protector de datos* que bloquee el uso de datos en segundo plano. Esta configuración también indica a la aplicación que use menos datos en primer plano siempre que sea posible. El [ConnectivityManager](xref:Android.Net.ConnectivityManager) se ha ampliado en Android nougat para que la aplicación pueda comprobar si el usuario ha habilitado el protector de datos para que la aplicación pueda hacer un esfuerzo por limitar su uso de datos cuando se habilita el protector de datos.

Para obtener más información sobre la nueva característica de protector de datos en Android nougat, consulte el tema sobre el [uso de datos de red de optimización](https://developer.android.com/training/basics/network-ops/data-saver.html) de Android.

### <a name="app-shortcuts"></a>Accesos directos a aplicaciones

Android 7,1 presentó una característica de *accesos directos de aplicaciones* que permite a los usuarios iniciar rápidamente tareas comunes o recomendadas con la aplicación.
Para activar el menú de accesos directos, el usuario presiona el icono de aplicación para un segundo o más &ndash; el menú aparece con una vibración rápida.
La liberación de la prensa hace que el menú permanezca:

[![Pantalla de ejemplo de un menú contextual de aplicación para una aplicación de mensajería](nougat-images/app-shortcuts-sml.png)](nougat-images/app-shortcuts.png#lightbox)

Esta característica solo está disponible en el nivel de API 25 o superior.
Para obtener más información sobre la nueva característica de accesos directos de aplicación en Android 7,1, vea el tema sobre [accesos directos a aplicaciones](https://developer.android.com/guide/topics/ui/shortcuts.html) de Android.

### <a name="sample-code"></a>Código de ejemplo

Hay varios ejemplos de Xamarin. Android disponibles para mostrarle cómo sacar provecho de las características de nougat de Android:

- [MultiWindowPlayground](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-multiwindowplayground) muestra el uso de la API de varias ventanas disponible en Android nougat. Puede cambiar la aplicación de ejemplo en modo de varias ventanas para ver cómo afecta al ciclo de vida y el comportamiento de la aplicación.

- El [servicio de mensajería](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-messagingservice) es un servicio sencillo que envía notificaciones `NotificationCompatManager`mediante el. También extiende la notificación con un `RemoteInput` objeto para permitir que los dispositivos nougat de Android respondan a través del texto directamente de la notificación sin tener que abrir una aplicación.

- [Notificaciones activas](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-activenotifications) muestra cómo usar la API `NotificationManager` para indicarle el número de notificaciones que se muestran actualmente en la aplicación.

- [Acceso a directorios con ámbito](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-scopeddirectoryaccess) Muestra cómo usar la API de acceso a directorios de ámbito para acceder fácilmente a directorios específicos. Esto sirve como alternativa a tener que definir `READ_EXTERNAL_STORAGE` permisos o `WRITE_EXTERNAL_STORAGE` en el manifiesto.

- [Arranque directo](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-directboot) Muestra cómo almacenar los datos en un almacenamiento cifrado de dispositivo que siempre está disponible mientras el dispositivo se arranca antes y después de que se escriban las credenciales de usuario (PIN/patrón/contraseña).

## <a name="summary"></a>Resumen

En este artículo se presentaron Android nougat y se explicó cómo instalar y configurar las herramientas y los paquetes más recientes para el desarrollo de Xamarin. Android en Android nougat. También se proporciona información general sobre las características clave disponibles en Android nougat, con vínculos a código fuente de ejemplo para ayudarle a empezar a crear aplicaciones para Android nougat.

## <a name="related-links"></a>Vínculos relacionados

- [Android 7,1 para desarrolladores](https://developer.android.com/about/versions/nougat/android-7.1.html)
- [Notas de la versión de Xamarin Android 7,0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md)
