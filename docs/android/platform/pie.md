---
title: Android 9 Pie
description: Cómo empezar a desarrollar aplicaciones para Android 9 Pie con Xamarin.Android.
ms.prod: xamarin
ms.assetid: 6575DD32-9DC8-44E6-85EF-1F8BD07D3780
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2018
ms.openlocfilehash: 0105b43116df697bc6688becb77298c236dfa601
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019884"
---
# <a name="android-pie-features"></a>Características de Android Pie

_Cómo empezar a desarrollar aplicaciones para Android 9 Pie con Xamarin.Android._

[Android 9 Pie](https://developer.android.com/about/versions/pie/) ahora está disponible en Google. Una serie de nuevas características y API están disponibles en esta versión, y muchas de ellas son necesarias para aprovechar las nuevas funcionalidades de hardware de los dispositivos Android más recientes.

![Imagen prominente de Android Pie](pie-images/01-android-p-logo.png)

Este artículo está estructurado para ayudarle a empezar a desarrollar aplicaciones de Xamarin.Android para Android Pie. Explica cómo instalar las actualizaciones necesarias, configurar el SDK y preparar un emulador o un dispositivo para realizar pruebas. También proporciona un esquema de las nuevas características de Android Pie y proporciona código fuente de ejemplo que muestra cómo usar algunas de las características clave de Android Pie.

Xamarin.Android 9.0 proporciona compatibilidad con Android Pie. Para más información sobre la compatibilidad de Xamarin.Android para Android Pie, vea las notas de la versión de [Android P Developer Preview 3](https://docs.microsoft.com/xamarin/android/release-notes/9/9.0/#android-p-dp1).

## <a name="requirements"></a>Requisitos

Para usar las características de Android Pie en aplicaciones basadas en Xamarin, se requiere lo siguiente:

- **Visual Studio**: se recomienda Visual Studio 2019.
    Si usa Visual Studio 2017, en Windows, actualice a Visual Studio 2017 15.8 o a una versión posterior. En macOS, actualice a Visual Studio 2017 para Mac 7.6 o una versión posterior.

- **Xamarin.Android**: Xamarin.Android 9.0.0.17 o posterior debe instalarse con Visual Studio (Xamarin.Android se instala automáticamente como parte de la carga de trabajo de **Desarrollo para dispositivos móviles con .NET**).

- **Kit para desarrolladores de Java**: el desarrollo de Xamarin Android 9.0 requiere [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (o puede probar la versión preliminar de la distribución de Microsoft de [OpenJDK](~/android/get-started/installation/openjdk.md)). JDK8 se instala automáticamente como parte de la carga de trabajo de **Desarrollo para dispositivos móviles con .NET**.

- **Android SDK**: Android SDK API 28 o posterior debe estar instalado con el Administrador de Android SDK.

## <a name="getting-started"></a>Introducción

Para empezar a desarrollar aplicaciones de Android Pie con Xamarin.Android, debe descargar e instalar las herramientas y los paquetes de SDK más recientes antes de poder crear su primer proyecto de Android Pie:

1. Se recomienda Visual Studio 2019. Si usa Visual Studio 2017, actualice a [Visual Studio 2017 15.8](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) o a una versión posterior. Si usa Visual Studio para Mac, actualice a [Visual Studio 2017 para Mac 7.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) o posterior.

2. Instale los paquetes y herramientas de **Android Pie (API 28)** con el Administrador de SDK.

3. Cree un proyecto de Xamarin.Android que tenga como destino **Android 9.0**.

4. Configure un emulador o dispositivo para probar las aplicaciones de Android Pie.

En las siguientes secciones se explican cada uno de estos pasos:

### <a name="update-visual-studio"></a>Actualizar Visual Studio

Se recomienda Visual Studio 2019 para crear aplicaciones de Android Pie con Xamarin.

Si usa Visual Studio 2017, actualice a Visual Studio 2017 versión 15.8 o posterior (para obtener instrucciones, vea [Actualización de Visual Studio 2017 a la versión más reciente](https://docs.microsoft.com/visualstudio/install/update-visual-studio)). En macOS, actualice a Visual Studio 2017 para Mac 7.6 o posterior (para obtener instrucciones, vea [Configuración e instalación de Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/installation)).

### <a name="install-the-android-sdk"></a>Instalación de Android SDK

Para crear un proyecto con Xamarin.Android 9.0, primero debe usar el administrador de Android SDK para instalar la plataforma SDK para **Android Pie (nivel de API 28)** o posterior.

1. Inicie el Administrador de SDK. En Visual Studio, haga clic en **Herramientas > Android > Administrador de Android SDK**. En Visual Studio para Mac, haga clic en **Herramientas > Administrador de SDK**.

2. En la esquina inferior derecha, haga clic en el icono de engranaje y seleccione **Repositorio > Google (no compatible)** :

    [![Configuración del repositorio en Google](pie-images/vs/set-repo-sml.png)](pie-images/vs/set-repo.png#lightbox)

3. Instale los paquetes del SDK de **Android Pie**, que se muestran como **Android SDK Platform 28** en la pestaña **Plataformas** (para más información acerca del uso del Administrador de SDK, consulte [Configuración de Android SDK](~/android/get-started/installation/android-sdk.md)):

    [![Instalación de paquetes de Android Pie](pie-images/vs/sdk-manager-sml.png)](pie-images/vs/sdk-manager.png#lightbox)

4. Si usa un emulador, cree un dispositivo virtual que admita el **nivel de API 28**. Para más información sobre cómo crear dispositivos virtuales, vea [Administración de dispositivos virtuales con el Administrador de dispositivos Android](~/android/get-started/installation/android-emulator/device-manager.md).

### <a name="start-a-xamarinandroid-project"></a>Inicio de un proyecto de Xamarin.Android

Cree un proyecto de Xamarin.Android. Si no está familiarizado con el desarrollo de Android con Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para aprender sobre la creación de proyectos de Xamarin.Android.

Cuando cree un proyecto de Android, asegúrese de configurar las opciones de versión para Android 9.0 o posterior. Por ejemplo, para que el proyecto tenga como destino Android Pie, debe configurar el nivel de API de Android de destino del proyecto como **Android 9.0** (API 28). Se recomienda establecer también el nivel de plataforma de destino en API 28 o posterior. Para obtener más información sobre la configuración de los niveles de API de Android, consulte [Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md).

### <a name="configure-a-device-or-emulator"></a>Configuración de un dispositivo o emulador

Si usa un dispositivo físico como Nexus o Pixel, puede actualizarlo a Android Pie siguiendo las instrucciones de [Imágenes de fábrica para dispositivos Nexus y Pixel](https://developers.google.com/android/images).

Si usa un emulador, cree un dispositivo virtual para el nivel de API 28 y seleccione una imagen basada en x86. Para más información sobre el uso del Administrador de dispositivos Android para crear y administrar dispositivos virtuales, consulte [Administración de dispositivos virtuales con el Administrador de dispositivos Android](~/android/get-started/installation/android-emulator/device-manager.md).
Para más información sobre cómo iniciar y depurar con Android Emulator, consulte [Depuración en Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md).

## <a name="new-features"></a>Características nuevas

Android Pie presenta una variedad de características nuevas. Algunas de estas nuevas características están pensadas para aprovechar las nuevas funcionalidades de hardware que ofrecen los dispositivos Android más recientes, mientras que otras están diseñadas para mejorar aún más la experiencia del usuario de Android:

- **Compatibilidad con recortes de pantalla**: proporciona API para buscar la ubicación y la forma del _recorte_ en la parte superior de la pantalla en dispositivos Android más recientes.

- **Mejoras en las notificaciones**: los mensajes de notificación ahora pueden mostrar imágenes, y se usa una nueva clase `Person` para simplificar los participantes de la conversación.

- **Posicionamiento interior**: compatibilidad de la plataforma con el protocolo Wi-Fi Round Trip Time, que permite que las aplicaciones usen dispositivos Wi-Fi para la navegación con configuraciones internas.

- **Compatibilidad con varias cámaras**: ofrece la capacidad de acceder a las secuencias de forma simultánea desde varias cámaras físicas (como cámaras duales delantera y trasera).

En las secciones siguientes se resaltan estas características y se proporcionan ejemplos de código breves para ayudarle a empezar a usarlas en la aplicación.

### <a name="display-cutout-support"></a>Compatibilidad con recortes de pantalla

Muchos dispositivos Android más recientes con pantallas contorno contra contorno tienen un *recorte de pantalla* o una "muesca" en la parte superior de la pantalla para la cámara y el altavoz.
En la siguiente captura de pantalla se ofrece un ejemplo de emulador de un recorte:

[![Android Emulator simulando un recorte](pie-images/02-example-cutout-sml.png)](pie-images/02-example-cutout.png#lightbox)

Para administrar el modo en que la ventana de la aplicación muestra su contenido en los dispositivos con un recorte de pantalla, Android Pie ha agregado un nuevo atributo [LayoutInDisplayCutoutMode](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#layoutInDisplayCutoutMode) para el diseño de la ventana. Este atributo puede establecerse en uno de los siguientes valores:

- [LayoutInDisplayCutoutModeNever](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_NEVER): la ventana nunca se puede superponer con el área de recorte.

- [LayoutInDisplayCutoutModeShortEdges](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES): la ventana se puede extender al área de recorte, pero solo sobre los bordes cortos de la pantalla. 

- [LayoutInDisplayCutoutModeDefault](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_DEFAULT): se permite que la ventana se extienda al área de recorte si el recorte está dentro de una barra del sistema.

Por ejemplo, para evitar que la ventana de la aplicación se superponga con el área de recorte, establezca el modo de recorte de diseño en *nunca*: 

```csharp
Window.Attributes.LayoutInDisplayCutoutMode =
    Android.Views.LayoutInDisplayCutoutMode.Never;
```

En los ejemplos siguientes se proporcionan ejemplos de estos modos de recorte. La primera captura de pantalla de la izquierda es la aplicación en modo de tamaño de pantalla no completa. En la captura de pantalla del centro, la aplicación pasará al modo de pantalla completa con `LayoutInDisplayCutoutMode` establecido en `LayoutInDisplayCutoutModeShortEdges`. Tenga en cuenta que el fondo blanco de la aplicación se extiende al área de recorte de pantalla:

[![Ejemplo de los modos de recorte de pantalla en el emulador](pie-images/03-cutout-modes-sml.png)](pie-images/03-cutout-modes.png#lightbox)

En la última captura de pantalla (arriba a la derecha), `LayoutInDisplayCutoutMode` se establece en `LayoutInDisplayCutoutModeShortNever` antes de que pase a pantalla completa.
Tenga en cuenta que no se permite que el fondo blanco de la aplicación se extienda al área de recorte de pantalla.

Si necesita información más detallada sobre el área de recorte en el dispositivo, puede usar la nueva clase [DisplayCutout](https://developer.android.com/reference/android/view/DisplayCutout.html). `DisplayCutout` representa el área de la pantalla que no se puede usar para mostrar el contenido. Puede usar esta información para recuperar la ubicación y la forma del recorte, para que la aplicación no intente mostrar contenido en esta área no funcional.

Para obtener más información sobre las nuevas características de recorte en Android P, vea [Compatibilidad con recortes de pantalla](https://developer.android.com/about/versions/pie/android-9.0#cutout).

### <a name="notifications-enhancements"></a>Mejoras en las notificaciones

Android Pie introduce las siguientes mejoras para optimizar la experiencia de mensajería:

- Los canales de notificación (introducidos en [Android Oreo](~/android/platform/oreo.md)) ahora admiten el bloqueo de grupos de canales.

- El sistema de notificación tiene tres nuevas categorías de no molestar (priorizar alarmas, sonidos del sistema y orígenes multimedia). Además, hay siete nuevos modos de no molestar que se pueden usar para suprimir las interrupciones visuales (como los distintivos, las luces de notificación, la apariencia de la barra de estado y el inicio de las actividades de pantalla completa).

- Se ha agregado una nueva clase [Person](https://developer.android.com/reference/android/app/Person.html) para representar al remitente de un mensaje. El uso de esta clase ayuda a optimizar la representación de cada notificación al identificar a las personas implicadas en una conversación (incluidos los avatares y los identificadores URI).

- Las notificaciones ahora pueden mostrar imágenes. 

En el ejemplo siguiente se muestra cómo usar las nuevas API para generar una notificación que contiene una imagen. En las siguientes capturas de pantallas, se publica una notificación de texto y va seguida de una notificación con una imagen insertada. Cuando las notificaciones se expanden (como se muestra a la derecha), se muestra el texto de la primera notificación y se amplía la imagen insertada en la segunda notificación:

[![Ejemplo de notificación con imagen](pie-images/04-example-notifications-sml.png)](pie-images/04-example-notifications.png#lightbox)

En el ejemplo siguiente se muestra cómo incluir una imagen en una notificación de Android Pie y se muestra el uso de la nueva clase `Person`:

1. Cree un objeto `Person` que representa al remitente. Por ejemplo, el nombre y el icono del remitente se incluyen en `fromPerson`:

    ```csharp
    Icon senderIcon = Icon.CreateWithResource(this, Resource.Drawable.sender_icon);
    Person fromPerson = new Person.Builder()
        .SetIcon(senderIcon)
        .SetName("Mark Sender")
        .Build();
    ```

2. Cree un elemento `Notification.MessagingStyle.Message` que contenga la imagen que se va a enviar, pasando la imagen al nuevo método [Notification.MessagingStyle.Message.SetData](https://developer.android.com/reference/android/app/Notification.MessagingStyle.Message.html#setData%28java.lang.String,%20android.net.Uri).
   Por ejemplo:

    ```csharp
    Uri imageUri = Uri.Parse("android.resource://com.xamarin.pminidemo/drawable/example_image");
    Notification.MessagingStyle.Message message = new Notification.MessagingStyle
            .Message("Here's a picture of where I'm currently standing", 0, fromPerson)
            .SetData("image/", imageUri);
    ```

3. Agregue el mensaje a un objeto `Notification.MessagingStyle`. Por ejemplo:

    ```csharp
    Notification.MessagingStyle style = new Notification.MessagingStyle(fromPerson)
            .AddMessage(message);
    ```

4. Conecte este estilo al generador de notificaciones. Por ejemplo:

    ```csharp
    builder = new Notification.Builder(this, MY_CHANNEL)
        .SetContentTitle("Tour of the Colosseum")
        .SetContentText("I'm standing right here!")
        .SetSmallIcon(Resource.Mipmap.ic_notification)
        .SetStyle(style)
        .SetChannelId(MY_CHANNEL);
    ```

5. Publique la notificación. Por ejemplo:

    ```csharp
    const int notificationId = 1000;
    notificationManager.Notify(notificationId, builder.Build());
    ```

Para más información sobre cómo crear notificaciones, consulte [Notificaciones locales](~/android/app-fundamentals/notifications/local-notifications.md).

### <a name="indoor-positioning"></a>Posicionamiento interior

Android Pie proporciona compatibilidad con IEEE 802.11mc (también conocido como _Wi-Fi Round-Trip-Time_ o _Wi-Fi RTT_), lo que permite que las aplicaciones detecten la distancia a uno o varios puntos de acceso Wi-Fi. Con esta información, es posible que la aplicación aproveche las ventajas de *posicionamiento interior* con una precisión de uno a dos metros. En los dispositivos Android que proporcionan compatibilidad de hardware con IEEE 801.11mc, la aplicación puede ofrecer características de navegación, como el control basado en la ubicación de dispositivos inteligentes o las instrucciones paso a paso a través de un almacén:

[![Ejemplo de navegación interior con Wi-Fi RTT](pie-images/05-wifi-rtt-sml.png)](pie-images/05-wifi-rtt.png#lightbox)

La nueva clase [WifiRttManager](https://developer.android.com/reference/android/net/wifi/rtt/WifiRttManager) y varias clases auxiliares proporcionan los medios para medir la distancia a los dispositivos Wi-Fi. Para obtener más información sobre las API de posicionamiento interior introducidas en Android P, consulte [Android.Net.Wifi.Rtt](https://developer.android.com/reference/android/net/wifi/rtt/package-summary).

### <a name="multi-camera-support"></a>Compatibilidad con varias cámaras

Muchos dispositivos Android más recientes tienen cámaras duales delanteras y traseras que son útiles para características como la visión estéreo, los efectos visuales mejorados y la capacidad de zoom mejorada. Android P introduce una nueva [Multi-Camera](https://developer.android.com/about/versions/pie/android-9.0#camera) API que permite que su aplicación use una *cámara lógica* o una *multicámara lógica* respaldada por una o varias cámaras físicas.
Para determinar si el dispositivo es compatible con una multicámara lógica, puede consultar las funcionalidades de cada cámara del dispositivo para ver si admite [RequestAvailableCapabilitiesLogicalMultiCamera](https://developer.android.com/reference/android/hardware/camera2/CameraMetadata#REQUEST_AVAILABLE_CAPABILITIES_LOGICAL_MULTI_CAMERA).

Android Pie también incluye una nueva clase [SessionConfiguration](https://developer.android.com/reference/android/hardware/camera2/params/SessionConfiguration.html) que se puede usar para ayudar a reducir los retrasos durante la captura inicial y eliminar la necesidad de iniciar e iniciar el flujo de la cámara.

Para más información sobre la compatibilidad con multicámaras en Android P, vea [Compatibilidad con multicámaras y actualizaciones para cámaras](https://developer.android.com/about/versions/pie/android-9.0#camera).

### <a name="other-features"></a>Otras características

Además, Android Pie admite otras características nuevas:

- La nueva clase [AnimatedImageDrawable](https://developer.android.com/reference/android/graphics/drawable/AnimatedImageDrawable.html), que se puede usar para dibujar y mostrar imágenes animadas.

- Una nueva clase [ImageDecoder](https://developer.android.com/reference/android/graphics/ImageDecoder.html) que sustituye a `BitmapFactory`. `ImageDecoder` se puede usar para descodificar un elemento `AnimatedImageDrawable`.

- Compatibilidad con vídeo HDR (alto rango dinámico) e imágenes HEIF (formato de archivo de imagen de alta eficiencia).

- [JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html) se ha mejorado para controlar de forma más inteligente los trabajos relacionados con la red. El nuevo método [GetNetwork](https://developer.android.com/reference/android/app/job/JobParameters#getNetwork%28%29) de la clase [JobParameters](https://developer.android.com/reference/android/app/job/JobParameters) devuelve la mejor red para realizar cualquier solicitud de red para un trabajo determinado.

Para obtener más información sobre las características más recientes de Android Pie, consulte [Características y API de Android 9](https://developer.android.com/about/versions/pie/android-9.0).

## <a name="behavior-changes"></a>Cambios de comportamiento

Cuando la versión de Android de destino se establece en el nivel de API 28, hay varios cambios de plataforma que se verán afectados por el comportamiento de la aplicación, incluso si no está implementando las nuevas características descritas anteriormente. La lista siguiente es un breve resumen de estos cambios:

- Las aplicaciones deben solicitar ahora permisos de primer plano antes de usar los servicios en primer plano.

- Si la aplicación tiene más de un proceso, no puede compartir un único directorio de datos [WebView](xref:Android.Webkit.WebView) entre los procesos.

- Ya no se permite acceder directamente al directorio de datos de otra aplicación mediante la ruta de acceso.

Para más información sobre los cambios de comportamiento de las aplicaciones destinadas a Android P, vea [Cambios de comportamiento](https://developer.android.com/about/versions/pie/android-9.0-changes-all#p-apps).

## <a name="sample-code"></a>Código de ejemplo

[AndroidPMiniDemo](https://github.com/xamarin/monodroid-samples/tree/master/android-p/AndroidPMiniDemo) es una aplicación de ejemplo de Xamarin.Android para Android Pie que muestra cómo establecer los modos de recorte de pantalla, cómo usar la nueva clase `Person` y cómo enviar una notificación que incluye una imagen.

## <a name="summary"></a>Resumen

En este artículo se presentó Android Pie y se explicó cómo instalar y configurar las herramientas y los paquetes más recientes para el desarrollo de Xamarin.Android en Android Pie. Proporciona información general sobre las características clave disponibles en Android Pie, con código fuente de ejemplo para algunas de estas características.
Incluye vínculos a la documentación de la API y temas para desarrolladores de Android que le ayudarán a empezar a crear aplicaciones para Android Pie. También se resaltan los cambios de comportamiento más importantes de Android Pie que pueden afectar a las aplicaciones existentes.

## <a name="related-links"></a>Vínculos relacionados

- [Android 9 Pie](https://developer.android.com/about/versions/pie/)
