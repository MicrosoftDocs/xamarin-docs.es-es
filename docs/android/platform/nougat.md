---
title: Características de Nougat
description: Introducción al uso de Xamarin.Android para desarrollar aplicaciones para Android Nougat.
ms.prod: xamarin
ms.assetid: 5C74ABE2-C862-4ED0-8EA5-C7FEE5251D4B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/02/2018
ms.openlocfilehash: 135303743cce1f23bb1d70731f6e760188df11e2
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457216"
---
# <a name="nougat-features"></a>Características de Nougat

_Introducción al uso de Xamarin.Android para desarrollar aplicaciones para Android Nougat._

En este artículo se proporciona un esquema de las características introducidas en Android Nougat, se explica cómo preparar Xamarin.Android para el desarrollo de Android Nougat y se proporcionan vínculos a aplicaciones de ejemplo que muestran cómo usar las características de Android Nougat en aplicaciones de Xamarin.Android.

## <a name="overview"></a>Información general

[Android Nougat](https://developer.android.com/about/versions/nougat/android-7.0.html) es el seguimiento de Google para Android 6.0 Marshmallow. Xamarin.Android proporciona compatibilidad con **enlaces de Android 7.x** en Xamarin Android 7.0 y versiones posteriores. Android Nougat agrega muchas API nuevas para las características de Nougat que se describen a continuación; estas API están disponibles para las aplicaciones de Xamarin.Android cuando se usa Xamarin.Android 7.0.

[![Imágenes de Hero de tabletas y teléfonos Android con Android Nougat](nougat-images/android-n-hero-sml.png)](nougat-images/android-n-hero.png#lightbox)

Para más información sobre las API de Android 7.x, consulte [Android 7.1 para desarrolladores](https://developer.android.com/preview/api-overview.html).
Para obtener una lista de problemas conocidos de Xamarin.Android 7.0, consulte las [notas de la versión](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md).

Android Nougat proporciona muchas características nuevas de interés para los desarrolladores de Xamarin.Android. Entre ellas se incluyen:

- **Compatibilidad con varias ventanas**: esta mejora permite que los usuarios abran dos aplicaciones en la pantalla al mismo tiempo.

- **Mejoras en las notificaciones**: el sistema de notificaciones rediseñado en Android Nougat incluye una característica de *respuesta directa* que permite a los usuarios responder rápidamente a los mensajes de texto directamente desde la interfaz de usuario de notificaciones. Además, si la aplicación crea notificaciones para los mensajes recibidos, la nueva característica de *notificaciones agrupadas* puede agrupar las notificaciones como un solo grupo cuando se recibe más de un mensaje.

- **Protector de datos**: esta característica es un nuevo servicio del sistema que ayuda a reducir el uso que las aplicaciones hacen de los datos móviles; proporciona el control a los usuarios sobre el modo en el que las aplicaciones usan los datos móviles.

Además, Android Nougat ofrece muchas otras mejoras de interés a los desarrolladores de aplicaciones, como una nueva característica de configuración de seguridad de red, Doze sobre la marcha, atestación de clave, nuevas API de configuración rápida, compatibilidad con múltiples configuraciones regionales, API de ICU4J, mejoras de WebView, acceso a las características del lenguaje Java 8, acceso a directorios con ámbito, una API de puntero personalizada, compatibilidad con la realidad virtual de la plataforma, archivos virtuales y optimizaciones de procesamiento en segundo plano.

En este artículo se explica cómo empezar a compilar aplicaciones con Android Nougat para probar las nuevas características y planear la migración o el trabajo de las características para emplear la nueva plataforma Android Nougat como destino.

## <a name="requirements"></a>Requisitos

Para usar las nuevas características de Android Nougat en aplicaciones basadas en Xamarin, se requiere lo siguiente:

- **Visual Studio o Visual Studio para Mac**: si usa Visual Studio, se requiere la versión 4.2.0.628, o posterior, de Visual Studio Tools para Xamarin. Si usa Visual Studio para Mac, se requiere la versión 6.1.0, o posterior, de Visual Studio para Mac.

- **Xamarin.Android**: Xamarin.Android 7.0 o posterior debe estar instalado y configurado con Visual Studio o Visual Studio para Mac.

- **Android SDK**: Android SDK 7.0 (API 24) o posterior debe estar instalado a través del Administrador de Android SDK.

- **Kit para desarrolladores de Java**: el desarrollo de Xamarin.Android 7.0 requiere [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o posterior si va a desarrollar para el nivel de API 24 o superior (JDK 8 también admite niveles de API anteriores a 24). Se requiere la versión de 64 bits del JDK 8 si usa controles personalizados o el controlador de vista previa de formularios.

> [!IMPORTANT]
> Xamarin.Android no admite JDK 9.

Tenga en cuenta que las aplicaciones se deben volver a generar con Xamarin C6SR4 o una versión posterior para que funcionen de forma confiable con Android Nougat. Dado que Android Nougat solo se puede vincular a [bibliotecas nativas proporcionadas por NDK](https://developer.android.com/about/versions/nougat/android-7.0-changes.html), es posible que las aplicaciones existentes que usan bibliotecas como **mono.Data.SQLite.dll** se bloqueen al ejecutarse en Android Nougat si no se vuelven a generar correctamente.

## <a name="getting-started"></a>Introducción

Para empezar a usar Android Nougat con Xamarin.Android, debe descargar e instalar las herramientas y los paquetes de SDK más recientes antes de poder crear un proyecto de Android Nougat:

1. Instale las últimas actualizaciones más recientes de Xamarin.Android desde Xamarin.

2. Instale los paquetes de **Android 7.0 (API 24)** y las herramientas o posterior.

3. Cree un nuevo proyecto de Xamarin.Android que tenga como destino Android Nougat.

4. Configure un emulador o un dispositivo para Android Nougat.

En las siguientes secciones se explican cada uno de estos pasos:

### <a name="install-xamarin-updates"></a>Instalación de las actualizaciones de Xamarin

Para agregar compatibilidad con Xamarin para Android Nougat, cambie el canal de actualizaciones en Visual Studio o Visual Studio para Mac al canal estable y aplique las actualizaciones más recientes. Si también necesita características que actualmente solo están disponibles en el canal Alfa o Beta, puede cambiar a dichos canales, que también proporcionan compatibilidad con Android 7.x. Para información sobre cómo cambiar el canal de actualizaciones (versiones), consulte [Cambio del canal de actualizaciones](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel).

### <a name="install-the-android-sdk"></a>Instalación de Android SDK

Para crear un proyecto con Xamarin Android 7.0, primero debe usar el Administrador de Android SDK para instalar **SDK Platform Android N (API 24)** o posterior. También debe instalar el componente **Android SDK Tools** más reciente:

1. Inicie el Administrador de Android SDK (en Visual Studio para Mac, use **Herramientas > Abrir el Administrador de Android SDK&hellip;** ; en Visual Studio, use **Herramientas > Android > Administrador de Android SDK**).

2. Instale **Android 7.0 (API 24)** o posterior:

    [![Selección de paquetes de Android 7.0 en el Administrador de Android SDK](nougat-images/preview-packages.png)](nougat-images/preview-packages.png#lightbox)

3. Instale las herramientas de Android SDK más recientes:

    [![Selección de las herramientas de Android SDK más recientes en el Administrador de Android SDK](nougat-images/preview-tools.png)](nougat-images/preview-tools.png#lightbox)

    Debe instalar la revisión 25.2.2 o posterior de Android SDK Tools, las herramientas de la plataforma de Android SDK 24.0.3 o posterior y las herramientas de compilación de Android SDK 24.0.2 o posterior.

4. Compruebe que la **ubicación del kit de desarrollo de Java** está configurada para JDK 1.8:

    [![Configuración de la ruta de acceso de JDK 8 en las opciones de herramientas](nougat-images/use-jdk-1.8.png)](nougat-images/use-jdk-1.8.png#lightbox)

    Para ver esta configuración en Visual Studio, haga clic en **Herramientas > Opciones > Xamarin > Configuración de Android**. En Visual Studio para Mac, haga clic en **Preferencias > Proyectos > Ubicaciones de SDK > Android**.

### <a name="start-a-xamarinandroid-project"></a>Inicio de un proyecto de Xamarin.Android

Creación de un proyecto de Xamarin.Android Si no está familiarizado con el desarrollo de Android con Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para aprender sobre la creación de proyectos de Xamarin.Android.

Cuando cree un proyecto de Android, debe configurar las opciones de versión para utilizar Android 7.0, o posterior, como destino. Por ejemplo, para que el proyecto tenga como destino Android 7.0, debe configurar el nivel de API de Android de destino del proyecto para **Android 7.0 (API 24 - Nougat)** . Se recomienda establecer el nivel de plataforma de destino en API 24 o posterior. Para más información sobre los niveles de API de Android, vea [Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md).

> [!NOTE]
> Actualmente, debe establecer la **versión mínima de Android** en **Android 7.0 (API 24 - Nougat)** para implementar la aplicación en dispositivos o emuladores de Android Nougat.

### <a name="configure-an-emulator-or-device"></a>Configuración de un emulador o un dispositivo

Si usa un emulador, inicie Android AVD Manager y cree un nuevo dispositivo con la siguiente configuración:

- Dispositivo: Nexus 5X, Nexus 6, Nexus 6P, Nexus Player, Nexus 9 o Pixel C.
- Destino: Android 7.0: nivel de API 24
- ABI: x86 o x86\_64

Por ejemplo, este dispositivo virtual está configurado para emular un dispositivo Nexus 6:

[![Configuración de AVD con un dispositivo Nexus 6, Android 7.0 como destino y CPU/ABI x86 Atom de Intel](nougat-images/android-n-avd.png)](nougat-images/android-n-avd.png#lightbox)

Si usa un dispositivo físico, como un Nexus 5X, 6 o 9, puede actualizar el dispositivo a través de las actualizaciones automáticas por aire (OTA) o descargar una imagen del sistema y sobrescribir la memoria del dispositivo directamente. Para más información sobre cómo actualizar manualmente el dispositivo a Android Nougat, consulte [Imágenes OTA para dispositivos Nexus](https://developers.google.com/android/nexus/ota).

Tenga en cuenta que Android Nougat no admite los dispositivos Nexus 5.

## <a name="new-features"></a>Características nuevas

Android Nougat presenta numerosas características y funcionalidades nuevas, como la compatibilidad con varias ventanas, las mejoras en las notificaciones y el protector de datos. En las secciones siguientes se resaltan estas características y se proporcionan vínculos para ayudarle a empezar a usarlas en la aplicación.

### <a name="multi-window-mode"></a>Modo de varias ventanas

El modo de varias ventanas permite a los usuarios abrir dos aplicaciones a la vez con una compatibilidad completa con varias tareas. Estas aplicaciones pueden ejecutarse en paralelo (horizontal) o una encima de otra (vertical) en el modo de pantalla dividida.
Los usuarios pueden arrastrar un divisor entre las aplicaciones para cambiar su tamaño y pueden cortar y pegar contenido entre las aplicaciones. Cuando se presentan dos aplicaciones en el modo de varias ventanas, la actividad seleccionada continúa ejecutándose, mientras la actividad no seleccionada se pausa pero sigue estando visible. El modo de varias ventanas no modifica el ciclo de vida de la actividad de Android.

[![Aplicaciones de ejemplo que se ejecutan en modo de varias ventanas tanto en vertical como en horizontal](nougat-images/multi-window-mode.png)](nougat-images/multi-window-mode.png#lightbox)

Puede configurar cómo las actividades de la aplicación de Xamarin.Android admiten el modo de varias ventanas. Por ejemplo, puede configurar atributos que establezcan el tamaño mínimo y el alto y ancho predeterminados de la aplicación en el modo de varias ventanas. Puede usar la nueva propiedad `Activity.IsInMultiWindowMode` para determinar si la actividad está en modo de varias ventanas. Por ejemplo:

```csharp
if (!IsInMultiWindowMode) {
    multiDisabledMessage.Visibility = ViewStates.Visible;
} else {
    multiDisabledMessage.Visibility = ViewStates.Gone;
}
```

La aplicación de ejemplo [MultiWindowPlayground](/samples/xamarin/monodroid-samples/android-n-multiwindowplayground) incluye código de C# que muestra cómo sacar provecho de las interfaces de usuario de varias ventana con la aplicación.

Para obtener más información sobre el modo de varias ventanas, vea la [Compatibilidad multiventana](https://developer.android.com/guide/topics/ui/multi-window.html).

### <a name="enhanced-notifications"></a>Notificaciones mejoradas

Android Nougat presenta un sistema de notificaciones rediseñado. Incluye una nueva característica de respuesta directa que permite a los usuarios responder rápidamente a las notificaciones de mensajes de texto entrantes directamente en la interfaz de usuario de notificaciones. A partir de Android 7.0, los mensajes de notificación se pueden agrupar como un solo grupo cuando se recibe más de un mensaje. Además, los desarrolladores pueden personalizar las vistas de notificación, aprovechar las decoraciones del sistema en las notificaciones y sacar partido a las nuevas plantillas de notificación al generar notificaciones.

#### <a name="direct-reply"></a>Respuesta directa

Cuando un usuario recibe una notificación de mensaje entrante, Android Nougat posibilita la respuesta a dicho mensaje dentro de la notificación (en lugar de abrir la aplicación de mensajería para enviar una respuesta).
Esta característica de respuesta insertada permite a los usuarios responder rápidamente a un mensaje SMS o de texto directamente dentro de la interfaz de notificación:

[![Captura de pantalla de una notificación con un campo de respuesta directa insertada](nougat-images/notifications-inline-reply-sml.png)](nougat-images/notifications-inline-reply.png#lightbox)

Para admitir esta característica en la aplicación, debe agregar *acciones de respuesta insertadas* a la aplicación a través de un objeto [RemoteInput](xref:Android.App.RemoteInput) para que los usuarios puedan responder a través del texto directamente desde la interfaz de usuario de notificaciones.
Por ejemplo, el código siguiente genera `RemoteInput` para recibir la entrada de texto, crea un intento pendiente para la acción de respuesta y crea una acción habilitada mediante de entrada remota:

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

La aplicación de ejemplo [Servicio de mensajería](/samples/xamarin/monodroid-samples/android-n-messagingservice) incluye código de C# que muestra cómo extender las notificaciones con un objeto `RemoteInput`. Para más información sobre cómo agregar acciones de respuesta insertadas a la aplicación para Android 7.0 o posterior, consulte el tema [Respuesta a las notificaciones](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#direct) de Android.

#### <a name="bundled-notifications"></a>Notificaciones agrupadas

Android Nougat pueden agrupar los mensajes de notificación juntos (por ejemplo, por tema de mensaje) y mostrar el grupo en lugar de cada mensaje independiente.
Esta característica de *notificaciones agrupadas* permite a los usuarios descartar o archivar un grupo de notificaciones en una acción. El usuario puede desplazarse hacia abajo para expandir la agrupación de notificaciones para ver cada notificación en detalle:

[![Ejemplo de captura de pantalla de notificaciones agrupadas](nougat-images/bundled-notifications-sml.png)](nougat-images/bundled-notifications.png#lightbox)

Para admitir notificaciones agrupadas, la aplicación puede usar el método [Builder.SetGroup](xref:Android.App.Notification.Builder.SetGroup*) para agrupar notificaciones similares. Para más información sobre los grupos de notificaciones agrupadas en Android N, consulte el tema [Agrupación de notificaciones](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#bundle) de Android.

#### <a name="custom-views"></a>Vistas personalizadas

Android Nougat permite crear vistas de notificación personalizadas con acciones, diseños expansibles y encabezados de notificación del sistema. Para más información sobre las vistas de notificación personalizadas en Android Nougat, consulte el tema [Mejoras en las notificaciones](https://developer.android.com/about/versions/nougat/android-7.0.html#notification_enhancements) de Android.

### <a name="data-saver"></a>Protector de datos

A partir de Android Nougat, los usuarios pueden habilitar una nueva configuración *Protector de datos* que bloquea el uso de los datos en segundo plano. Esta configuración también indica a la aplicación que use menos datos en primer plano siempre que sea posible. [ConnectivityManager](xref:Android.Net.ConnectivityManager) se ha ampliado en Android Nougat para que la aplicación pueda comprobar si el usuario ha habilitado el protector de datos para que la aplicación pueda hacer un esfuerzo por limitar el uso de datos cuando esta característica se habilita.

Para más información sobre la nueva característica Protector de datos en Android Nougat, consulte el tema [Optimización del uso de datos de red](https://developer.android.com/training/basics/network-ops/data-saver.html) de Android.

### <a name="app-shortcuts"></a>Accesos directos de aplicación

Android 7.1 presentó la característica *Accesos directos de aplicación* que permite a los usuarios iniciar rápidamente tareas comunes o recomendadas con la aplicación.
Para activar el menú de accesos directos, el usuario presiona el icono de la aplicación durante un segundo o más (el menú aparece con una vibración rápida).
Al dejar de presionar el menú se queda:

[![Pantalla de ejemplo de un menú de acceso directo de aplicación para una aplicación de mensajería](nougat-images/app-shortcuts-sml.png)](nougat-images/app-shortcuts.png#lightbox)

Esta característica solo está disponible en el nivel de API 25 o superior.
Para más información sobre la nueva característica de accesos directos de aplicación en Android 7.1, consulte el tema [Accesos directos de aplicación](https://developer.android.com/guide/topics/ui/shortcuts.html) de Android.

### <a name="sample-code"></a>Código de ejemplo

Hay varios ejemplos de Xamarin.Android disponibles para mostrarle cómo sacar provecho de las características de Android Nougat:

- [MultiWindowPlayground](/samples/xamarin/monodroid-samples/android-n-multiwindowplayground) muestra el uso de la API de varias ventanas disponible en Android Nougat. Puede cambiar la aplicación de ejemplo en modo de varias ventanas para ver cómo afecta al ciclo de vida y el comportamiento de la aplicación.

- [Servicio de mensajería](/samples/xamarin/monodroid-samples/android-n-messagingservice) es un servicio sencillo que envía notificaciones mediante `NotificationCompatManager`. También extiende la notificación con un objeto `RemoteInput` para permitir que los dispositivos Android Nougat respondan a través del texto directamente desde la notificación sin tener que abrir una aplicación.

- [Notificaciones activas](/samples/xamarin/monodroid-samples/android-n-activenotifications) muestra cómo usar la API `NotificationManager` para indicarle el número de notificaciones que la aplicación muestra actualmente.

- [Acceso a directorios con ámbito](/samples/xamarin/monodroid-samples/android-n-scopeddirectoryaccess) muestra cómo usar la API de acceso a directorios con ámbito para acceder fácilmente a directorios específicos. Esto sirve como alternativa a tener que definir permisos `READ_EXTERNAL_STORAGE` o `WRITE_EXTERNAL_STORAGE` en el manifiesto.

- [Arranque directo](/samples/xamarin/monodroid-samples/android-n-directboot) muestra cómo almacenar los datos en un almacenamiento cifrado de dispositivo que siempre está disponible mientras el dispositivo arranca antes y después de que se escriban las credenciales de usuario (PIN, patrón o contraseña).

## <a name="summary"></a>Resumen

En este artículo se presentó Android Nougat y se explicó cómo instalar y configurar las herramientas y los paquetes más recientes para el desarrollo de Xamarin.Android en Android Nougat. También se proporciona información general sobre las características clave disponibles en Android Nougat, con vínculos a código fuente de ejemplo para ayudarle a empezar a crear aplicaciones para Android Nougat.

## <a name="related-links"></a>Vínculos relacionados

- [Android 7.1 para desarrolladores](https://developer.android.com/about/versions/nougat/android-7.1.html)
- [Notas de la versión de Xamarin Android 7.0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md)