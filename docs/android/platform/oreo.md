---
title: Características de Oreo
description: Introducción al uso de Xamarin.Android para desarrollar aplicaciones para la versión más reciente Android.
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: davidortinau
ms.author: daortin
ms.date: 07/06/2018
ms.openlocfilehash: 56430f8c4988c16a31f9806b0ffb8b6355d6340b
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "73020001"
---
# <a name="oreo-features"></a>Características de Oreo

_Introducción al uso de Xamarin.Android para desarrollar aplicaciones para la versión más reciente Android._

[Android 8.0 Oreo](https://developer.android.com/index.html) es la versión más reciente de Android que está disponible en Google. Android Oreo ofrece muchas nuevas características de interés para los desarrolladores de Xamarin.Android. Entre otras, estas características incluyen canales de notificación, señales de notificación, fuentes personalizadas de XML, fuentes descargables, autorelleno e imagen en imagen (PIP). Android Oreo incluye nuevas API para estas nuevas funcionalidades y estas API están disponibles para las aplicaciones de Xamarin.Android cuando se usa Xamarin.Android 8.0 y posterior.

[![Imagen prominente de Android Oreo](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

Este artículo está estructurado para ayudarle a empezar a desarrollar aplicaciones de Xamarin.Android para Android 8.0 Oreo. Explica cómo instalar las actualizaciones necesarias, configurar el SDK y preparar un emulador o (un dispositivo) para realizar pruebas. También ofrece un esquema de las nuevas características de Android 8.0 Oreo, con vínculos a aplicaciones de ejemplo que muestran cómo usar las características de Android Oreo en aplicaciones de Xamarin.Android.

## <a name="requirements"></a>Requisitos

Para usar las características de Android Oreo en aplicaciones basadas en Xamarin, se requiere lo siguiente:

- **Visual Studio**: si usa Visual Studio para Mac, se requiere la versión 15.5 o posterior de Visual Studio para Mac.  Si utiliza un equipo Mac, se necesita Visual Studio para Mac versión 7.2.0.

- **Xamarin.Android**: Xamarin.Android 8.0 o posterior debe estar instalado y configurado con Visual Studio.

- **Android SDK**: es necesario instalar Android SDK 8.0 (API 26) o posterior mediante el Administrador de SDK.

## <a name="getting-started"></a>Introducción

Para empezar a usar Android Oreo con Xamarin.Android, debe descargar e instalar las herramientas y los paquetes de SDK más recientes para poder crear un proyecto de Android Oreo:

1. Actualice a la versión más reciente de Visual Studio.

2. Instale los paquetes las y herramientas **Android 8.0.0 (API 26)** con el Administrador de SDK.

3. Cree un nuevo proyecto de Xamarin.Android que tenga como destino Android Oreo (API 26).

4. Configure un emulador o un dispositivo para probar las aplicaciones de Android Oreo.

En las siguientes secciones se explican cada uno de estos pasos:

### <a name="update-visual-studio-and-xamarinandroid"></a>Actualización de Visual Studio y Xamarin.Android

Para agregar compatibilidad con Android Oreo a Visual Studio, haga lo siguiente:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

- En Visual Studio 2019, use el [Administrador de SDK](~/android/get-started/installation/android-sdk.md) para instalar el nivel de API 26.0 o posterior.

- Si usa Visual Studio 2017:

    1. Actualice a Visual Studio 2017 versión 15.7 o posterior (consulte [Actualización de Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/update-visual-studio)).

    2. Use el [Administrador de SDK](~/android/get-started/installation/android-sdk.md) para instalar el nivel de API 26.0 o posterior.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

- Actualice a la versión estable más reciente de Visual Studio para Mac, tal y como se explica en [Actualización de Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/update).

-----

Para más información sobre la compatibilidad de Xamarin con Android Oreo, consulte las [notas de la versión de Xamarin.Android 8.0](https://docs.microsoft.com/xamarin/android/release-notes/8/8.0/).

### <a name="install-the-android-sdk"></a>Instalación de Android SDK

Para crear un proyecto con Xamarin.Android 8.0, primero debe usar el Administrador de Xamarin Android SDK para instalar la plataforma SDK para **Android 8.0: Oreo** o posterior. También debe instalar Android SDK Tools 26.0 o posterior.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Para iniciar el Administrador de SDK (en Visual Studio, haga clic en **Herramientas > Android > Administrador de SDK**).

2. Instale los paquetes de **Android 8.0: Oreo**. Si usa el emulador de Android SDK, asegúrese de incluir las imágenes del sistema **x86** que necesitará:

    [![Selección de herramientas de Android 8.0 en el Administrador de SDK](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. Instale **Android SDK Tools 26.0.2** o posterior, **Android SDK Platform-Tools 26.0.0** o posterior y **Android SDK Build-Tools 26.0.0** o posterior:

    [![Selección de Android SDK Tools 26 en el Administrador de SDK](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Para iniciar el Administrador de SDK (en Visual Studio para Mac, haga clic en **Herramientas > Administrador de SDK**).

2. Instale los paquetes del SDK de **Android 8.0: Oreo**. Si usa el emulador de Android SDK, asegúrese de incluir las imágenes del sistema **x86** que necesitará:

    [![Selección de paquetes de Android 8.0 en el Administrador de SDK](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. Instale **Android SDK Tools 26.0.2** o posterior, **Android SDK Platform-Tools 26.0.0** o posterior y **Android SDK Build-Tools 26.0.0** o posterior:

    [![Selección de Android SDK Tools 26 en el Administrador de SDK](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----

### <a name="start-a-xamarinandroid-project"></a>Inicio de un proyecto de Xamarin.Android

Creación de un proyecto de Xamarin.Android Si no está familiarizado con el desarrollo de Android con Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para obtener información sobre la creación de proyectos de Xamarin.Android.

Cuando cree un proyecto de Android, asegúrese de configurar las opciones de versión para Android 8.0 o posterior. Por ejemplo, para que el proyecto tenga como destino Android 8.0, debe configurar el nivel de API de Android de destino del proyecto como **Android 8.0 (API 26)** . Se recomienda establecer también el nivel de plataforma de destino en API 26 o posterior. Para obtener más información sobre la configuración de niveles del nivel de API de Android, consulte [Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md).

### <a name="configure-an-emulator-or-device"></a>Configuración de un emulador o un dispositivo

Si intenta iniciar el AVD Manager predeterminado basado en la GUI de Google después de instalar Android SDK Tools 26.0 o posterior, es posible que aparezca el siguiente cuadro de diálogo de error, que le indicará que use la herramienta AVD Manager de línea de comandos **avdmanager** en su lugar:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Cuadro de diálogo de advertencia de Android Emulator Manager](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![Cuadro de diálogo de advertencia de Android Emulator Manager](oreo-images/mac/03-avd-warning.png)

-----

Este mensaje se muestra porque Google ya no ofrece un AVD Manager de GUI independiente que admita la API 26.0 y posterior. Para Android 8.0 Oreo, debe usar el administrador de Xamarin Android SDK o la herramienta de línea de comandos `avdmanager` para crear dispositivos virtuales para Android Oreo.

Para obtener más información sobre el uso del Administrador de dispositivos Android para crear y administrar dispositivos virtuales, consulte [Administración de dispositivos virtuales con el Administrador de dispositivos Android](~/android/get-started/installation/android-emulator/device-manager.md).
Para crear dispositivos virtuales sin el Administrador de dispositivos Android, siga los pasos descritos en la sección siguiente.

#### <a name="creating-virtual-devices-using-avdmanager"></a>Creación de dispositivos virtuales con avdmanager

Para usar **avdmanager** para crear un dispositivo virtual, siga estos pasos:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Abra una ventana de símbolo del sistema y establezca `JAVA_HOME` en la ubicación del SDK de Java del equipo. En el caso de una instalación típica de Xamarin, puede usar el siguiente comando:

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2. Agregue la ubicación de la carpeta `bin` de Android SDK a la `PATH`.
    En el caso de una instalación típica de Xamarin, puede usar el siguiente comando:

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3. Cierre la ventana de símbolo del sistema y abra una nueva ventana de símbolo del sistema. Cree otro dispositivo virtual mediante el comando [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html). Por ejemplo, para crear una AVD denominada **AVD-Oreo-8.0** con la imagen del sistema x86 para el nivel de API 26, use el siguiente comando:

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4. Cuando se le pregunte si **quiere crear un perfil de hardware personalizado [no]** , puede escribir **no** y aceptar el perfil de hardware predeterminado. Si dice **sí**, **avdmanager** le pedirá una lista de preguntas para personalizar el perfil de hardware.

Después de usar **avdmanager** para crear el dispositivo virtual, este se incluirá en el menú desplegable del dispositivo:

[![Se agregó un nuevo AVD al menú desplegable de dispositivos](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Abra una ventana de **terminal** y cambie a la ubicación del directorio Android SDK Tools en el equipo Mac. En el caso de una instalación típica de Xamarin, puede usar el siguiente comando:

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2. Cree otro dispositivo virtual mediante el comando [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html). Por ejemplo, para crear una AVD denominada **AVD-Oreo-8.0** con la imagen del sistema x86 para el nivel de API 26, use el siguiente comando:

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3. Cuando se le pregunte si **quiere crear un perfil de hardware personalizado [no]** , puede escribir **no** y aceptar el perfil de hardware predeterminado. Si dice **sí**, **avdmanager** le pedirá una lista de preguntas para personalizar el perfil de hardware.

Después de usar **avdmanager** para crear el dispositivo virtual, este se incluirá en el menú desplegable del dispositivo:

[![Se agregó un nuevo AVD al menú desplegable de dispositivos](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

Para obtener información sobre cómo iniciar y configurar una instancia de Android Emulator, consulte [Depuración en Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md).

Si usa un dispositivo físico, como Nexus o Pixel, puede actualizarlo a través de las actualizaciones automáticas de forma inalámbrica (OTA) o descargar una imagen del sistema y sobrescribir la memoria del dispositivo directamente. Para más información sobre cómo actualizar manualmente el dispositivo a Android Oreo, consulte [Imágenes de fábrica para dispositivos Nexus y Pixel](https://developers.google.com/android/images).

## <a name="new-features"></a>Características nuevas

Android Oreo presenta diversas nuevas características y funcionalidades, como canales de notificación, señales de notificación, fuentes personalizadas de XML, fuentes descargables, autorelleno e imagen en imagen. En las secciones siguientes se resaltan estas características y se proporcionan vínculos para ayudarle a empezar a usarlas en la aplicación.

### <a name="notification-channels"></a>Canales de notificación

Los *canales de notificación* son categorías definidas por la aplicación para las notificaciones.
Puede crear un canal de notificación para cada tipo de notificación que tenga que enviar y puede crear canales de notificación que reflejen las elecciones realizadas por los usuarios de la aplicación. La nueva característica de canales de notificación permite proporcionar a los usuarios un control minucioso de los distintos tipos de notificaciones. Por ejemplo, si está implementando una aplicación de mensajería, puede crear canales de notificación independientes para cada grupo de conversación creado por un usuario.

[Canales de notificación](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan) explica cómo crear un canal de notificación y usarlo para publicar notificaciones locales. Para ver un ejemplo de código del mundo real, consulte el ejemplo [NotificationChannels](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-notificationchannels); en esta aplicación de ejemplo se administran dos canales y se establecen otras opciones de notificación.

### <a name="notification-badges"></a>Señales de notificación

Las señales de notificación consisten en pequeños puntos que aparecen en los iconos de la aplicación, tal y como se muestra en esta captura de pantalla:

[![Señales de notificación de ejemplo en iconos de la aplicación](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

Estos puntos indican que hay nuevas notificaciones para uno o varios canales de notificación en la aplicación asociada a ese icono de aplicación &ndash;, se trata de notificaciones que el usuario aún no ha descartado o sobre las que no ha actuado. Los usuarios pueden realizar una pulsación larga en un botón para echar un vistazo a las notificaciones asociadas a una señal de notificación, descartando o actuando en notificaciones desde el menú de larga duración que aparece.

Para obtener más información sobre las señales de notificación, consulte el tema [Señales de notificación](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges) para desarrolladores de Android.

### <a name="custom-fonts-in-xml"></a>Fuentes personalizadas de XML

Android Oreo presenta *fuentes de XML*, lo que permite incorporar fuentes personalizadas como recursos. Se admiten los formatos de fuente OpenType ( **.otf**) y TrueType ( **.ttf**). Para agregar fuentes como recursos, haga lo siguiente:

1. Cree una carpeta **Resources/font**.

2. Copie los archivos de fuente (por ejemplo, archivos **.ttf** y **.otf** ) en **Resources/font**. 

3. Si es necesario, cambie el nombre de cada archivo de fuente para que siga las convenciones de nomenclatura de archivos de Android (es decir, usar solo minúsculas *a-z*, *0-9* y caracteres de subrayado en los nombres de archivo). Por ejemplo, se podría cambiar el nombre del archivo de fuente `Pacifico-Regular.ttf` a algo parecido a `pacifico.ttf`.

4. Aplique la fuente personalizada mediante el nuevo atributo `android:fontFamily` en el XML de diseño. Por ejemplo, la siguiente declaración de `TextView` utiliza el recurso de fuente agregado **pacifico.ttf**:

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

También puede crear un archivo XML de familias de fuentes que describa varias fuentes, así como los detalles de estilo y grosor. Para obtener más información, consulte el tema sobre [fuentes de XML](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html) para desarrolladores de Android.

### <a name="downloadable-fonts"></a>Fuentes descargables

A partir de Android Oreo, las aplicaciones pueden solicitar fuentes de un proveedor en lugar de agruparlas en el APK. Las fuentes se descargan de la red sólo cuando es necesario. Esta característica reduce el tamaño de APK, lo que conserva la memoria del teléfono y el uso de datos móviles. También puede usar esta característica en las versiones 14 y posteriores de la API de Android instalando el paquete Android Support Library 26.

Cuando la aplicación necesita una fuente, se crea un objeto `FontsRequest` (que especifica la fuente que se va a descargar) que luego pasa a un método `FontsContract` para descargar la fuente. En los siguientes pasos se describe el proceso de descarga de fuentes con más detalles:

1. Creación de una instancia de un objeto [FontRequest](https://developer.android.com/reference/android/provider/FontRequest.html). 

2. Creación de una subclase y una instancia de [FontsContract.FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html).

3. Implementación del método [FontRequestCallback.OnTypeFaceRetrieved](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29), que se usa para controlar la finalización de la solicitud de fuente.

4. Implementación del de[FontRequestCallback.OnTypeFaceRequestFailed](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29), que se usa para informar a la aplicación de los errores que se produzcan durante el proceso de solicitud de fuentes.

5. Llamada al método [FontsContract.RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback)) para recuperar la fuente del proveedor de fuentes. 

Cuando se llama al método `RequestFonts`, este comprueba primero si la fuente está guardada en la caché local (de una llamada anterior a `RequestFont`). Si no está guardada en caché, llama al proveedor de fuentes, recupera la fuente de forma asincrónica y luego pasa los resultados de nuevo a la aplicación invocando el método `OnTypeFaceRetrieved`.

En el ejemplo de [fuentes descargables](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-downloadablefonts) se muestra cómo usar la característica de fuentes descargables que se presenta en Android Oreo. 

Para obtener más información sobre la descarga de fuentes, consulte el tema [Fuentes descargables](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html) para desarrolladores de Android.

### <a name="autofill"></a>Autorrelleno

Nueva característica _Autorrelleno de Framework_ de Android Oreo facilita a los usuarios el control de tareas repetitivas tales como el inicio de sesión, la creación de cuentas y las transacciones de tarjetas de crédito. Los usuarios pierden menos tiempo volviendo a escribir la información (lo que puede dar lugar a errores de entrada). Para que la aplicación pueda trabajar con la característica Autorrelleno de Framework, se debe habilitar un servicio de autorrelleno en la configuración del sistema (los usuarios pueden habilitar o deshabilitar el autorelleno).

En el ejemplo [AutofillFramework](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-autofillframework) se muestra el uso de la característica Autorrelleno de Framework. Incluye implementaciones de actividades de cliente con vistas que se deben autorellenar y un servicio que puede proporcionar datos de autorrelleno a las actividades de cliente.

Para obtener más información sobre la nueva característica Autorrelleno y cómo optimizar la aplicación para autorelleno, consulte el tema [Autorrelleno de Framework](https://developer.android.com/guide/topics/text/autofill.html) para desarrolladores de Android.

### <a name="picture-in-picture-pip"></a>Imagen en imagen (PIP)

Android Oreo permite que una actividad se inicie en modo de imagen en imagen (PIP), que superpone la pantalla de otra actividad. Esta característica está destinada a la reproducción de vídeo.

Para especificar que la actividad de la aplicación puede usar el modo PIP, establezca la marca siguiente en true en el manifiesto de Android:

```xml
android:supportsPictureInPicture
```

Para especificar cómo debe comportarse la actividad cuando está en modo PIP, use el nuevo objeto [PictureInPictureParams](https://developer.android.com/reference/android/app/PictureInPictureParams.html). `PictureInPictureParams` representa un conjunto de parámetros que se usan para inicializar y actualizar una actividad en modo PIP (por ejemplo, la relación de aspecto preferida de la actividad). Se han agregado los nuevos métodos PIP siguientes a `Activity` en Android Oreo:

- [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29): coloca la actividad en modo PIP. La actividad se coloca en la esquina de la pantalla y el resto de la pantalla se rellena con la actividad anterior que estaba en la pantalla.

- [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29): actualiza los valores de configuración de PIP de la actividad (por ejemplo, un cambio en la relación de aspecto).

En el ejemplo [PictureInPicture](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-pictureinpicture) se muestra el uso básico del modo de imagen en imagen (PiP) para dispositivos de mano que se presenta en Oreo. En el ejemplo se reproduce un vídeo que continúa ininterrumpidamente mientras cambia de un modo de visualización a otro o de una actividad a otra.

### <a name="other-features"></a>Otras características

Android Oreo contiene muchas otras nuevas características tales como la biblioteca de compatibilidad con Emoji, la API de ubicación, los límites de fondo, el color de gama amplia para aplicaciones, los nuevos códecs de audio, las mejoras de WebView, la compatibilidad mejorada con la navegación mediante el teclado y una nueva API de AAudio (audio de Pro) para audio de baja latencia de alto rendimiento. Para obtener más información sobre estas características, consulte el tema sobre [características e interfaces API de Android Oreo](https://developer.android.com/about/versions/oreo/android-8.0.html) para desarrolladores de Android.

## <a name="behavior-changes"></a>Cambios de comportamiento

Android Oreo incluye diversos cambios de comportamiento del sistema y de la API que pueden afectar a la funcionalidad de las aplicaciones existentes. Los valores se describen de la siguiente manera.

### <a name="background-execution-limits"></a>Límites de ejecución en segundo plano

Para mejorar la experiencia del usuario, Android Oreo impone limitaciones en lo que las aplicaciones pueden hacer mientras se ejecutan en segundo plano. Por ejemplo, si el usuario está viendo un vídeo o jugando, una aplicación que se ejecute en segundo plano podría perjudicar el rendimiento de una aplicación de uso intensivo de vídeo que se ejecute en primer plano. Como resultado, Android Oreo coloca las siguientes restricciones en las aplicaciones que no interactúan directamente con el usuario:

1. **Limitaciones del servicio en segundo plano**: cuando una aplicación se ejecuta en segundo plano, tiene una ventana de varios minutos en la que todavía se permite crear y usar los servicios. Al final de la ventana, Android detiene el servicio en segundo plano de la aplicación y lo trata como _inactivo_.

2. **Limitaciones de difusión**: Android 7.0 (API 25) pone limitaciones en las difusiones que una aplicación registra que recibe. Android Oreo hace que estas limitaciones sean más rigurosas. Por ejemplo, las aplicaciones de Android Oreo ya no pueden registrar receptores de difusión para difusiones implícitas en sus manifiestos.

Para obtener más información sobre los nuevos límites de ejecución en segundo plano, consulte el tema [Límites de ejecución en segundo plano](https://developer.android.com/about/versions/oreo/background.html) para desarrolladores de Android.

### <a name="breaking-changes"></a>Cambios importantes

Las aplicaciones que tienen como destino Android Oreo o posterior deben modificar sus aplicaciones para admitir los siguientes cambios, si procede:

- Android Oreo deja en desuso la capacidad de establecer la prioridad de notificaciones individuales. En su lugar, el usuario establece un nivel de importancia recomendado al crear un canal de notificación. El nivel de importancia que se asigna a un canal de notificación se aplica a todos los mensajes de notificación que se publican en él.

- En el caso de las aplicaciones que tienen como destino Android Oreo, `PendingIntent.GetService()` no funciona debido a nuevos límites puestos en los servicios iniciados en segundo plano. Si tiene como destino Android Oreo, debe usar [PendingIntent.GetBroadcast](xref:Android.App.PendingIntent.GetBroadcast*) en su lugar.  

## <a name="sample-code"></a>Código de ejemplo

Hay varios ejemplos de Xamarin.Android disponibles para mostrarle cómo sacar provecho de las características de Android Oreo:

- [NotificationsChannels](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-notificationchannels) muestra el uso del nuevo sistema de canales de notificación que se presenta en Android Oreo. En este ejemplo se administran dos canales de notificaciones: uno con la importancia predeterminada y el otro con importancia alta.

- [PictureInPicture](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-pictureinpicture) muestra el uso básico del modo de imagen en imagen (PiP) para dispositivos de mano que se presenta en Oreo. En el ejemplo se reproduce un vídeo que continúa ininterrumpidamente mientras cambia de un modo de visualización a otro o de una actividad a otra.

- [AutofillFramework](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-autofillframework) muestra el uso del la característica Autorrelleno de Framework. Incluye implementaciones de actividades de cliente con vistas que se deben autorellenar y un servicio que puede proporcionar datos de autorrelleno a las actividades de cliente.

- [Fuentes descargables](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-downloadablefonts) proporciona un ejemplo de cómo usar la característica de fuentes descargables descrita anteriormente.

- [EmojiCompat](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-emojicompat) muestra el uso de la biblioteca de compatibilidad con EmojiCompat. Puede usar esta biblioteca para impedir que la aplicación muestre caracteres Emoji que faltan como caracteres "tofu".

- [Intención de actualizaciones de ubicación pendientes](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-androidplaylocation-locupdpendintent) muestra el uso de la API de ubicación para obtener actualizaciones sobre la ubicación de un dispositivo mediante un `PendingIntent`.

- El [servicio de actualizaciones de ubicación en primer plano](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-androidplaylocation-locupdfgservice) muestra cómo usar la API de ubicación para obtener actualizaciones sobre la ubicación de un dispositivo mediante un servicio en primer plano enlazado e iniciado.

## <a name="video"></a>Vídeo

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**Desarrollo de Android 8.0 Oreo con C#**

## <a name="summary"></a>Resumen

En este artículo se presenta Android Oreo y se explica cómo instalar y configurar las herramientas y los paquetes más recientes para el desarrollo de Xamarin.Android en Android Oreo. Ofrece información general sobre las características clave disponibles en Android Oreo, con vínculos a código fuente de ejemplo para algunas nuevas características. Incluye vínculos a la documentación de la API y temas para desarrolladores de Android que le ayudarán a empezar a crear aplicaciones para Android Oreo. También se destacan los cambios de comportamiento más importantes de Android Oreo que pueden afectar a las aplicaciones existentes.

## <a name="related-links"></a>Vínculos relacionados

- [Android 8.0 Oreo](https://developer.android.com/index.html)
