---
title: Características de Oreo
description: Cómo empezar a usar Xamarin. Android para desarrollar aplicaciones para la versión más reciente de Android.
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: conceptdev
ms.author: crdun
ms.date: 07/06/2018
ms.openlocfilehash: 798819176c741d8afc07f9cd097a61d753debbc9
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523776"
---
# <a name="oreo-features"></a>Características de Oreo

_Cómo empezar a usar Xamarin. Android para desarrollar aplicaciones para la versión más reciente de Android._

[Android 8,0 Oreo](https://developer.android.com/index.html) es la versión más reciente de Android disponible en Google. Android Oreo ofrece muchas características nuevas de interés para los desarrolladores de Xamarin. Android. Estas características incluyen canales de notificación, notificaciones de notificación, fuentes personalizadas en XML, fuentes descargables, Autorrellenar e imagen en imagen (PIP). Android Oreo incluye nuevas API para estas nuevas funcionalidades y estas API están disponibles para las aplicaciones de Xamarin. Android cuando se usa Xamarin. Android 8,0 y versiones posteriores.

[![Imagen de héroe de Android Oreo](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

Este artículo está estructurado para ayudarle a empezar a desarrollar aplicaciones de Xamarin. Android para Android 8,0 Oreo. Se explica cómo instalar las actualizaciones necesarias, configurar el SDK y crear un emulador (o dispositivo) para las pruebas. También proporciona un esquema de las nuevas características de Android 8,0 Oreo, con vínculos a aplicaciones de ejemplo que muestran cómo usar las características de Android Oreo en aplicaciones de Xamarin. Android.


## <a name="requirements"></a>Requisitos

Se requiere lo siguiente para usar las características de Oreo de Android en aplicaciones basadas en Xamarin:

- **Visual Studio** &ndash; Si usa Windows, se requiere la versión 15,5 o posterior de Visual Studio.  Si utiliza un equipo Mac, se necesita Visual Studio para Mac versión 7.2.0.

- **Xamarin. Android** &ndash; Xamarin. Android 8,0 o posterior debe estar instalado y configurado con Visual Studio.

- **Android SDK** &ndash; Android SDK 8,0 (API 26) o posterior debe instalarse a través del administrador de Android SDK.



## <a name="getting-started"></a>Introducción

Para empezar a usar Android Oreo con Xamarin. Android, debe descargar e instalar las herramientas y los paquetes de SDK más recientes antes de poder crear un proyecto de Oreo de Android:

1. Actualice a la versión más reciente de Visual Studio.

2. Instale las herramientas y los paquetes de **Android 8.0.0 (API 26)** o posterior a través del administrador de SDK.

3. Cree un nuevo proyecto de Xamarin. Android que tenga como destino Android Oreo (API 26).

4. Configurar un emulador o un dispositivo para probar aplicaciones Android Oreo.

En las secciones siguientes se explica cada uno de estos pasos:

### <a name="update-visual-studio-and-xamarinandroid"></a>Actualización de Visual Studio y Xamarin. Android

Para agregar compatibilidad con Android Oreo a Visual Studio, haga lo siguiente:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

- Para Visual Studio 2019, use el [Administrador de SDK](~/android/get-started/installation/android-sdk.md) para instalar el nivel de API 26,0 o posterior.

- Si usa Visual Studio 2017:

    1. Actualice a Visual Studio 2017 versión 15,7 o posterior (consulte [Actualizar visual studio 2017](https://docs.microsoft.com/visualstudio/install/update-visual-studio)).

    2. Use el [Administrador de SDK](~/android/get-started/installation/android-sdk.md) para instalar el nivel de API 26,0 o posterior.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

- Actualice a la versión estable más reciente de Visual Studio para Mac como se explica en [actualizar Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/update).

-----

Para obtener más información sobre la compatibilidad de Xamarin con Android Oreo, consulte las notas de la [versión de Xamarin. Android 8,0](https://docs.microsoft.com/xamarin/android/release-notes/8/8.0/).



### <a name="install-the-android-sdk"></a>Instalar el Android SDK

Para crear un proyecto con Xamarin. Android 8,0, primero debe usar el administrador de Android SDK de Xamarin para instalar la plataforma SDK para **Android 8,0-Oreo** o posterior. También debe instalar Android SDK Tools 26,0 o posterior.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Inicie el administrador de SDK (en Visual Studio, haga clic en **herramientas > Android > Android SDK Manager**).

2. Instale los paquetes **de Android 8,0-Oreo** . Si usa el emulador de Android SDK, asegúrese de incluir las imágenes del sistema **x86** que necesitará:

    [![Selección de paquetes de Android 8,0 en el administrador de Android SDK](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. Instale **Android SDK Tools 26.0.2** o posterior, **Android SDK Platform-Tools 26.0.0** o posterior y **Android SDK build-tools 26.0.0** (o posterior):

    [![Selección de Android SDK Tools 26 en el administrador de Android SDK](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Inicie el administrador de SDK (en Visual Studio para Mac, haga clic en **herramientas > administrador de SDK**).

2. Instale los paquetes **de SDK de Android 8,0-Oreo** . Si usa el emulador de Android SDK, asegúrese de incluir las imágenes del sistema **x86** que necesitará:

    [![Selección de paquetes de Android 8,0 en SDK Manager](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. Instale **Android SDK Tools 26.0.2** o posterior, **Android SDK Platform-Tools 26.0.0** o posterior y **Android SDK build-tools 26.0.0** (o posterior):

    [![Selección de Android SDK Tools 26 en SDK Manager](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----



### <a name="start-a-xamarinandroid-project"></a>Iniciar un proyecto de Xamarin. Android

Cree un nuevo proyecto de Xamarin. Android. Si no está familiarizado con el desarrollo de Android con Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para obtener información sobre la creación de proyectos de Xamarin. Android.

Al crear un proyecto de Android, debe configurar las opciones de versión para que tengan como destino Android 8,0 o posterior. Por ejemplo, para tener como destino el proyecto para Android 8,0, debe configurar el nivel de API de Android de destino de su proyecto en **android 8,0 (API 26)** . Se recomienda que también establezca el nivel de plataforma de destino en la API 26 o posterior. Para más información sobre la configuración de niveles de nivel de API de Android, consulte [Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md).


### <a name="configure-an-emulator-or-device"></a>Configurar un emulador o un dispositivo

Si intenta iniciar el administrador de AVD basado en la GUI de Google predeterminado después de instalar Android SDK Tools 26,0 o una versión posterior, es posible que aparezca el siguiente cuadro de diálogo de error, que le indica que debe usar la herramienta de administrador de la línea de comandos **avdmanager** en su lugar:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Cuadro de diálogo de advertencia del administrador de Android Emulator](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Cuadro de diálogo de advertencia del administrador de Android Emulator](oreo-images/mac/03-avd-warning.png)

-----

Este mensaje se muestra porque Google ya no proporciona un administrador AVD de GUI independiente que admite la API 26,0 y versiones posteriores. Para Android 8,0 Oreo, debe usar el administrador de Android Emulator de Xamarin o la herramienta de línea `avdmanager` de comandos para crear dispositivos virtuales para Android Oreo.

Para usar la Android Device Manager para crear y administrar dispositivos virtuales, consulte [Administración de dispositivos virtuales con la Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).
Para crear dispositivos virtuales sin el Android Device Manager, siga los pasos descritos en la sección siguiente.


#### <a name="creating-virtual-devices-using-avdmanager"></a>Creación de dispositivos virtuales con avdmanager

Para usar **avdmanager** para crear un nuevo dispositivo virtual, siga estos pasos:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Abra una ventana del símbolo del sistema `JAVA_HOME` y establezca la ubicación del SDK de Java en el equipo. Para una instalación típica de Xamarin, puede usar el siguiente comando:

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2. Agregue la ubicación de la carpeta `bin` Android SDK a su `PATH`.
    Para una instalación típica de Xamarin, puede usar el siguiente comando:

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3. Cierre la ventana del símbolo del sistema y abra una nueva ventana del símbolo del sistema. Cree un nuevo dispositivo virtual mediante el comando [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) . Por ejemplo, para crear un AVD denominado **AVD-Oreo-8,0** con la imagen de sistema x86 para el nivel de API 26, use el siguiente comando:

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4. Cuando se le pregunte si desea **crear un perfil de hardware personalizado [no]** , puede escribir **no** y aceptar el perfil de hardware predeterminado. En caso **afirmativo**, **avdmanager** le pedirá una lista de preguntas para personalizar el perfil de hardware.

Después de **avdmanager** para crear el dispositivo virtual, se incluirá en el menú desplegable del dispositivo:

[![Nuevo AVD agregado al menú desplegable del dispositivo](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Abra una ventana de **terminal** y cambie a la ubicación del directorio de Android SDK herramientas en el equipo Mac. Para una instalación típica de Xamarin, puede usar el siguiente comando:

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2. Cree un nuevo dispositivo virtual mediante el comando [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) . Por ejemplo, para crear un AVD denominado **AVD-Oreo-8,0** con la imagen de sistema x86 para el nivel de API 26, use el siguiente comando:

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3. Cuando se le pregunte si desea **crear un perfil de hardware personalizado [no]** , puede escribir **no** y aceptar el perfil de hardware predeterminado. En caso **afirmativo**, **avdmanager** le pedirá una lista de preguntas para personalizar el perfil de hardware.

Después de usar **avdmanager** para crear el dispositivo virtual, se incluirá en el menú desplegable del dispositivo:

[![Nuevo AVD agregado al menú desplegable del dispositivo](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

Para obtener más información sobre la configuración de un emulador de Android para pruebas y depuración, consulte depuración [en el Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md).

Si usa un dispositivo físico, como un nexo o un píxel, puede actualizar el dispositivo a través de las actualizaciones automáticas por aire (OTA) o descargar una imagen del sistema y hacer que el dispositivo parpadee directamente. Para obtener más información sobre cómo actualizar manualmente el dispositivo a Android Oreo, consulte [imágenes de fábrica para dispositivos de nexo y píxel](https://developers.google.com/android/images).



## <a name="new-features"></a>Características nuevas

Android Oreo presenta una variedad de nuevas características y funcionalidades, como canales de notificación, notificaciones de notificación, fuentes personalizadas en XML, fuentes descargables, Autorrellenar e imagen en imagen. En las secciones siguientes se resaltan estas características y se proporcionan vínculos para ayudarle a empezar a usarlas en la aplicación.



### <a name="notification-channels"></a>Canales de notificación

Los *canales de notificación* son categorías definidas por la aplicación para las notificaciones.
Puede crear un canal de notificación para cada tipo de notificación que necesite enviar, y puede crear canales de notificación para reflejar las elecciones realizadas por los usuarios de la aplicación. La nueva característica de canales de notificación permite proporcionar a los usuarios un control minucioso de los distintos tipos de notificaciones. Por ejemplo, si está implementando una aplicación de mensajería, puede crear canales de notificación independientes para cada grupo de conversación creado por un usuario.

[Canales de notificación](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan) explica cómo crear un canal de notificación y usarlo para publicar notificaciones locales. Para obtener un ejemplo de código del mundo real, consulte el ejemplo [NotificationChannels](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-notificationchannels) . en esta aplicación de ejemplo se administran dos canales y se establecen otras opciones de notificación.



### <a name="notification-badges"></a>Notificaciones de notificación

Los distintivos de notificación son pequeños puntos que aparecen en los iconos de la aplicación, tal como se muestra en esta captura de pantalla:

[![Notificaciones de ejemplo de los iconos de la aplicación](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

Estos puntos indican que hay nuevas notificaciones de uno o varios canales de notificación en la aplicación asociada a ese icono &ndash; de la aplicación. se trata de notificaciones que el usuario todavía no ha descartado o que ha actuado. Los usuarios pueden presionar un botón largo para echar un vistazo a las notificaciones asociadas a una notificación de notificación, descartando o actuando en notificaciones del menú de larga duración que appeaars.

Para obtener más información sobre los distintivos de notificación, consulte el tema notificaciones de [notificación](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges) para desarrolladores de Android.



### <a name="custom-fonts-in-xml"></a>Fuentes personalizadas en XML

Android Oreo incluye *fuentes en XML*, lo que permite incorporar fuentes personalizadas como recursos. Se admiten los formatos de fuente OpenType ( **. OTF**) y TrueType ( **. ttf**). Para agregar fuentes como recursos, haga lo siguiente:

1. Cree una carpeta de **recursos/fuente** .

2. Copie los archivos de fuente (por ejemplo, archivos **. ttf** y **. OTF** ) en **recursos/fuente**. 

3. Si es necesario, cambie el nombre de cada archivo de fuente para que se adhiere a las convenciones de nomenclatura de archivos de Android (es decir, use solo minúsculas *a-z*, *0-9*y caracteres de subrayado en los nombres de archivo). Por ejemplo, se podría cambiar `Pacifico-Regular.ttf` el nombre del archivo de fuente `pacifico.ttf`a algo parecido a.

4. Aplique la fuente personalizada con el nuevo `android:fontFamily` atributo en el XML de diseño. Por ejemplo, la siguiente `TextView` declaración utiliza el recurso de fuente **Pacifico. ttf** agregado:

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

También puede crear un archivo XML de la familia de fuentes que describa varias fuentes, así como los detalles de estilo y peso. Para obtener más información, vea el tema sobre las [fuentes de desarrollador de Android en XML](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html) .


### <a name="downloadable-fonts"></a>Fuentes descargables

A partir de Android Oreo, las aplicaciones pueden solicitar fuentes de un proveedor en lugar de agruparlas en el APK. Las fuentes se descargan de la red solo según sea necesario. Esta característica reduce el tamaño de APK, lo que mantiene la memoria del teléfono y el uso de datos móviles. También puede usar esta característica en las versiones 14 y posteriores de la API de Android mediante la instalación del paquete de la biblioteca de compatibilidad de Android 26.

Cuando la aplicación necesita una fuente, se crea un `FontsRequest` objeto (que especifica la fuente que se va a descargar) y, a `FontsContract` continuación, se pasa a un método para descargar la fuente. En los pasos siguientes se describe con más detalle el proceso de descarga de fuentes:

1. Cree una instancia de un objeto [FontRequest](https://developer.android.com/reference/android/provider/FontRequest.html) . 

2. Subclase y cree una instancia de [FontsContract. FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html).

3. Implemente el método [FontRequestCallback. OnTypeFaceRetrieved](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29) , que se usa para controlar la finalización de la solicitud de fuente.

4. Implemente el método [FontRequestCallback. OnTypeFaceRequestFailed](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29) , que se usa para informar a la aplicación de los errores que se produzcan durante el proceso de solicitud de fuentes.

5. Llame al método [FontsContract. RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback)) para recuperar la fuente del proveedor de fuentes. 

Cuando se llama al `RequestFonts` método, primero comprueba si la fuente se almacena en caché localmente (desde una llamada anterior a `RequestFont`). Si no se almacena en caché, llama al proveedor de fuentes, recupera la fuente de forma asincrónica y, a continuación, devuelve los resultados a la aplicación invocando `OnTypeFaceRetrieved` el método.

En el ejemplo de [fuentes](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-downloadablefonts) descargables se muestra cómo usar la característica de fuentes descargables introducida en Android Oreo. 

Para obtener más información acerca de la descarga de fuentes, vea el tema sobre las [fuentes](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html) descargables para desarrolladores de Android.



### <a name="autofill"></a>Rellena

El nuevo marco de _Autorrelleno_ en Android Oreo facilita a los usuarios la administración de tareas repetitivas como el inicio de sesión, la creación de cuentas y las transacciones de tarjetas de crédito. Los usuarios dedican menos tiempo a escribir información (lo que puede provocar errores de entrada). Para que la aplicación pueda trabajar con el marco de Autorrelleno, se debe habilitar un servicio de Autorrelleno en la configuración del sistema (los usuarios pueden habilitar o deshabilitar Autorrellenar).

En el ejemplo [AutofillFramework](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-autofillframework) se muestra el uso del marco de Autorrelleno. Incluye implementaciones de las actividades de cliente con vistas que se deben rellenar de autocompletar y un servicio que puede proporcionar datos de Autorrelleno a las actividades de cliente.

Para obtener más información sobre la nueva característica Autorrellenar y cómo optimizar la aplicación para Autorrellenar, vea el tema sobre el [marco de Autorrelleno](https://developer.android.com/guide/topics/text/autofill.html) para desarrolladores de Android.



### <a name="picture-in-picture-pip"></a>Imagen en imagen (PIP)

Android Oreo permite que una actividad se inicie en modo de imagen en imagen (PIP), superponiendo la pantalla de otra actividad. Esta característica está destinada a la reproducción de vídeo.

Para especificar que la actividad de la aplicación puede usar el modo PIP, establezca la marca siguiente en true en el manifiesto de Android:

```xml
android:supportsPictureInPicture
```

Para especificar cómo debe comportarse la actividad cuando está en modo PIP, use el nuevo objeto [PictureInPictureParams](https://developer.android.com/reference/android/app/PictureInPictureParams.html) . `PictureInPictureParams`representa un conjunto de parámetros que se usan para inicializar y actualizar una actividad en modo PIP (por ejemplo, la relación de aspecto preferida de la actividad). Se han agregado los siguientes métodos PIP nuevos `Activity` a en Android Oreo:

- [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29) &ndash; coloca la actividad en modo PIP. La actividad se coloca en la esquina de la pantalla y el resto de la pantalla se rellena con la actividad anterior que estaba en la pantalla.

- [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29) &ndash; Actualiza los valores de configuración de PIP de la actividad (por ejemplo, un cambio en la relación de aspecto).

En el ejemplo [PictureInPicture](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-pictureinpicture) se muestra el uso básico del modo de imagen en imagen (PIP) para dispositivos de mano introducidos en Oreo. En el ejemplo se reproduce un vídeo que continúa sin interrupciones mientras se alterna entre los modos de visualización u otras actividades.



### <a name="other-features"></a>Otras características

Android Oreo contiene muchas otras características nuevas, como la biblioteca de compatibilidad de Emoji, la API de ubicación, los límites de fondo, el color de gama amplia para las aplicaciones, los nuevos códecs de audio, las mejoras de WebView, la compatibilidad con la navegación mediante el teclado mejorada y una nueva API de AAudio (audio de Pro) para audio de baja latencia de alto rendimiento. para obtener más información sobre estas características, vea el tema Android Developer [Android Oreo features and API](https://developer.android.com/about/versions/oreo/android-8.0.html) .



## <a name="behavior-changes"></a>Cambios de comportamiento

Android Oreo incluye una variedad de cambios de comportamiento del sistema y de la API que pueden afectar a la funcionalidad de las aplicaciones existentes. Estos cambios se describen como se indica a continuación.


### <a name="background-execution-limits"></a>Límites de ejecución en segundo plano

Para mejorar la experiencia del usuario, Android Oreo impone limitaciones en lo que las aplicaciones pueden hacer mientras se ejecuta en segundo plano. Por ejemplo, si el usuario ve un vídeo o juega un juego, una aplicación que se ejecuta en segundo plano podría perjudicar el rendimiento de una aplicación de uso intensivo de vídeo que se ejecuta en primer plano. Como resultado, Android Oreo coloca las siguientes restricciones en las aplicaciones que no interactúan directamente con el usuario:

1. **Limitaciones del servicio en segundo plano** &ndash; Cuando una aplicación se ejecuta en segundo plano, tiene una ventana de varios minutos en la que todavía se permite crear y usar los servicios. Al final de la ventana, Android detiene el servicio en segundo plano de la aplicación y lo tratacomo inactivo.

2. **Limitaciones de difusión** &ndash; Android 7,0 (API 25) colocaba limitaciones en las difusiones que una aplicación registra para recibir. Android Oreo hace que estas limitaciones sean más rigurosas. Por ejemplo, las aplicaciones de Oreo de Android ya no pueden registrar receptores de difusión para difusiones implícitas en sus manifiestos.

Para obtener más información sobre los nuevos límites de ejecución en segundo plano, vea el tema sobre los [límites de ejecución en segundo plano](https://developer.android.com/about/versions/oreo/background.html) para desarrolladores de Android.


### <a name="breaking-changes"></a>Cambios importantes

Las aplicaciones que tienen como destino Android Oreo o posterior deben modificar sus aplicaciones para admitir los siguientes cambios, si procede:

- Android Oreo deja de tener la capacidad de establecer la prioridad de las notificaciones individuales. En su lugar, establezca un nivel de importancia recomendado al crear un canal de notificación. El nivel de importancia que se asigna a un canal de notificación se aplica a todos los mensajes de notificación que se publican en él.

- En el caso de las aplicaciones destinadas a Android Oreo, `PendingIntent.GetService()` no funciona debido a los nuevos límites de los servicios iniciados en segundo plano. Si tiene como destino Android Oreo, debe usar [PendingIntent. GetBroadcast](xref:Android.App.PendingIntent.GetBroadcast*) en su lugar.  


## <a name="sample-code"></a>Código de ejemplo

Hay varios ejemplos de Xamarin. Android disponibles para mostrarle cómo sacar provecho de las características de Oreo de Android:

- [NotificationsChannels](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-notificationchannels) muestra cómo usar el nuevo sistema de canales de notificación introducido en Android Oreo. En este ejemplo se administran dos canales de notificaciones: uno con la importancia predeterminada y el otro con importancia alta.

- [PictureInPicture](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-pictureinpicture) muestra el uso básico del modo de imagen en imagen (PIP) para dispositivos de mano introducidos en Oreo. En el ejemplo se reproduce un vídeo que continúa sin interrupciones mientras se alterna entre los modos de visualización u otras actividades.

- [AutofillFramework](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-autofillframework) muestra el uso del marco de Autorrelleno. Incluye implementaciones de las actividades de cliente con vistas que se deben rellenar de autocompletar y un servicio que puede proporcionar datos de Autorrelleno a las actividades de cliente.

- [Fuentes](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-downloadablefonts) descargables proporciona un ejemplo de cómo usar la característica de fuentes descargables que se ha descrito anteriormente.

- [EmojiCompat](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-emojicompat) muestra el uso de la biblioteca de compatibilidad con EmojiCompat. Puede usar esta biblioteca para impedir que la aplicación muestre caracteres de Emoji que faltan como caracteres "tofu".

- La [intención de actualizaciones de ubicación pendientes](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-androidplaylocation-locupdpendintent) muestra el uso de la API de ubicación para obtener actualizaciones de la ubicación `PendingIntent`de un dispositivo mediante.

- El [servicio en primer plano actualizaciones de ubicación](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-androidplaylocation-locupdfgservice) muestra cómo usar la API de ubicación para obtener actualizaciones sobre la ubicación de un dispositivo mediante un servicio en primer plano enlazado e iniciado.


## <a name="video"></a>Vídeo

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**Desarrollo de Android 8,0 Oreo conC#**


## <a name="summary"></a>Resumen

En este artículo se presentaron Android Oreo y se explicó cómo instalar y configurar las herramientas y los paquetes más recientes para el desarrollo de Xamarin. Android en Android Oreo. Proporciona información general sobre las características clave disponibles en Android Oreo, con vínculos a código fuente de ejemplo para varias características nuevas. Incluye vínculos a la documentación de la API y temas para desarrolladores de Android que le ayudarán a empezar a crear aplicaciones para Android Oreo. También se resaltan los cambios de comportamiento de Android Oreo más importantes que podrían afectar a las aplicaciones existentes.


## <a name="related-links"></a>Vínculos relacionados

- [Android 8,0 Oreo](https://developer.android.com/index.html)
