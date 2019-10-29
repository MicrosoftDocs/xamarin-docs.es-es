---
title: Android 9 Pie
description: Cómo empezar a desarrollar aplicaciones para el gráfico circular de Android 9 con Xamarin. Android.
ms.prod: xamarin
ms.assetid: 6575DD32-9DC8-44E6-85EF-1F8BD07D3780
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2018
ms.openlocfilehash: 0105b43116df697bc6688becb77298c236dfa601
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019884"
---
# <a name="android-pie-features"></a>Características de gráficos circulares de Android

_Cómo empezar a desarrollar aplicaciones para el gráfico circular de Android 9 con Xamarin. Android._

El [gráfico circular de Android 9](https://developer.android.com/about/versions/pie/) ahora está disponible en Google. Una serie de nuevas características y API están disponibles en esta versión, y muchas de ellas son necesarias para aprovechar las nuevas capacidades de hardware de los dispositivos Android más recientes.

![Imagen del héroe del gráfico de Android](pie-images/01-android-p-logo.png)

Este artículo está estructurado para ayudarle a empezar a desarrollar aplicaciones de Xamarin. Android para el gráfico circular de Android. Explica cómo instalar las actualizaciones necesarias, configurar el SDK y preparar un emulador o un dispositivo para realizar pruebas. También proporciona un esquema de las nuevas características de los gráficos circulares de Android y proporciona código fuente de ejemplo que muestra cómo usar algunas de las características clave de los sectores de Android.

Xamarin. Android 9,0 proporciona compatibilidad con el gráfico circular de Android. Para obtener más información sobre la compatibilidad de Xamarin. Android con el gráfico circular de Android, consulte las notas de la versión de [Android P Developer Preview 3](https://docs.microsoft.com/xamarin/android/release-notes/9/9.0/#android-p-dp1) .

## <a name="requirements"></a>Requisitos

La lista siguiente es necesaria para usar las características de gráficos circulares de Android en aplicaciones basadas en Xamarin:

- Se recomienda **visual studio** &ndash; visual Studio 2019.
    Si usa Visual Studio 2017, en Windows Update a Visual Studio 2017, versión 15,8 o posterior. En macOS, actualice a Visual Studio 2017 para Mac versión 7,6 o posterior.

- **Xamarin. android** &ndash; Xamarin. Android 9.0.0.17 o posterior debe instalarse con Visual Studio (Xamarin. Android se instala automáticamente como parte de la carga de trabajo **desarrollo para dispositivos móviles con .net** ).

- El **Kit para desarrolladores de Java** &ndash; desarrollo de Xamarin Android 9,0 requiere [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (o puede probar la versión preliminar de la distribución de Microsoft de la [OpenJDK](~/android/get-started/installation/openjdk.md)). JDK8 se instala automáticamente como parte de la carga de trabajo **desarrollo para dispositivos móviles con .net** .

- **Android SDK** &ndash; Android SDK API 28 o posterior deben instalarse a través del administrador de Android SDK.

## <a name="getting-started"></a>Introducción

Para empezar a desarrollar aplicaciones de gráficos circulares de Android con Xamarin. Android, debe descargar e instalar las herramientas y los paquetes de SDK más recientes antes de poder crear su primer proyecto de circular de Android:

1. Se recomienda Visual Studio 2019. Si usa Visual Studio 2017, actualice a [visual studio 2017 versión 15,8](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) o posterior. Si usa Visual Studio para Mac, actualice a [Visual Studio 2017 para Mac versión 7,6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) o posterior.

2. Instale paquetes y herramientas de **gráficos circulares de Android (API 28)** mediante el administrador de SDK.

3. Cree un nuevo proyecto de Xamarin. Android que tenga como destino **Android 9,0**.

4. Configurar un emulador o un dispositivo para probar aplicaciones de gráficos circulares Android.

En las secciones siguientes se explica cada uno de estos pasos:

### <a name="update-visual-studio"></a>Actualizar Visual Studio

Se recomienda Visual Studio 2019 para compilar aplicaciones de gráficos circulares de Android con Xamarin.

Si usa Visual Studio 2017, actualice a Visual Studio 2017 versión 15,8 o posterior (para obtener instrucciones, consulte [actualización de Visual studio 2017 a la versión más reciente](https://docs.microsoft.com/visualstudio/install/update-visual-studio)). En macOS, actualice a Visual Studio 2017 para Mac 7,6 o posterior (para obtener instrucciones, consulte [configuración e instalación Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/installation)).

### <a name="install-the-android-sdk"></a>Instalar el Android SDK

Para crear un proyecto con Xamarin. Android 9,0, primero debe usar el administrador de Android SDK para instalar la plataforma SDK para el **círculo de Android (nivel de API 28)** o posterior.

1. Inicie el administrador de SDK. En Visual Studio, haga clic en **herramientas > Android > Android SDK Manager**. En Visual Studio para Mac, haga clic en **herramientas > administrador de SDK**.

2. En la esquina inferior derecha, haga clic en el icono de engranaje y seleccione **repositorio > Google (no compatible)** :

    [![configuración del repositorio en Google](pie-images/vs/set-repo-sml.png)](pie-images/vs/set-repo.png#lightbox)

3. Instale los paquetes del SDK de **gráficos circulares de Android** , que se muestran como **Android SDK plataforma 28** en la pestaña **plataformas** (para obtener más información acerca del uso del administrador de SDK, consulte [Android SDK Setup](~/android/get-started/installation/android-sdk.md)):

    [![instalación de paquetes de gráficos circulares de Android](pie-images/vs/sdk-manager-sml.png)](pie-images/vs/sdk-manager.png#lightbox)

4. Si usa un emulador, cree un dispositivo virtual que admita el **nivel de API 28**. Para obtener más información acerca de la creación de dispositivos virtuales, consulte [Administración de dispositivos virtuales con el Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).

### <a name="start-a-xamarinandroid-project"></a>Iniciar un proyecto de Xamarin. Android

Cree un nuevo proyecto de Xamarin. Android. Si no está familiarizado con el desarrollo de Android con Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para obtener información sobre la creación de proyectos de Xamarin. Android.

Al crear un proyecto de Android, debe configurar las opciones de versión para que tengan como destino Android 9,0 o posterior. Por ejemplo, para tener como destino el gráfico circular de Android, debe configurar el nivel de API de Android de destino de su proyecto a **android 9,0** (API 28). Se recomienda que también establezca el nivel de plataforma de destino en API 28 o posterior. Para más información sobre la configuración de los niveles de la API de Android, consulte [Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md).

### <a name="configure-a-device-or-emulator"></a>Configurar un dispositivo o emulador

Si usa un dispositivo físico, como un nexo o un píxel, puede actualizar el dispositivo a un gráfico circular de Android siguiendo las instrucciones de [imágenes de fábrica para dispositivos de nexo y píxel](https://developers.google.com/android/images).

Si usa un emulador, cree un dispositivo virtual para el nivel de API 28 y seleccione una imagen basada en x86. Para obtener información sobre el uso de la Android Device Manager para crear y administrar dispositivos virtuales, consulte [Administración de dispositivos virtuales con la Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).
Para obtener información sobre cómo usar el emulador de Android para probar y depurar, consulte [depuración en el Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md).

## <a name="new-features"></a>Características nuevas

El gráfico circular de Android presenta una variedad de características nuevas. Algunas de estas nuevas características están pensadas para aprovechar las nuevas capacidades de hardware que ofrecen los dispositivos Android más recientes, mientras que otras están diseñadas para mejorar aún más la experiencia del usuario de Android:

- **Mostrar compatibilidad con Recortes** &ndash; proporciona API para encontrar la ubicación y la forma del _recorte_ en la parte superior de la pantalla en dispositivos Android más recientes.

- Las mejoras en las **notificaciones** &ndash; mensajes de notificación ahora pueden mostrar imágenes, y se usa una nueva clase de `Person` para simplificar los participantes de la conversación.

- **Posicionamiento interior** &ndash; la compatibilidad de la plataforma con el protocolo WiFi de tiempo de ida y vuelta, lo que permite que las aplicaciones usen dispositivos wifi para la navegación en la configuración de interior.

- La **compatibilidad con varias** cámaras &ndash; ofrece la capacidad de obtener acceso a las secuencias de forma simultánea desde varias cámaras físicas (como cámaras duales y de doble cara).

En las secciones siguientes se resaltan estas características y se proporcionan ejemplos de código breves para ayudarle a empezar a usarlas en la aplicación.

### <a name="display-cutout-support"></a>Mostrar compatibilidad con recortes

Muchos dispositivos Android más recientes con pantallas de borde a borde tienen un *recorte de pantalla* (o "muesca") en la parte superior de la pantalla de la cámara y el altavoz.
La captura de pantalla siguiente proporciona un ejemplo de emulador de un recorte:

[![emulador de Android simulando un recorte](pie-images/02-example-cutout-sml.png)](pie-images/02-example-cutout.png#lightbox)

Para administrar el modo en que la ventana de la aplicación muestra su contenido en los dispositivos con un recorte de pantalla, el gráfico circular de Android ha agregado un nuevo atributo de diseño de ventana de [LayoutInDisplayCutoutMode](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#layoutInDisplayCutoutMode) . Este atributo se puede establecer en uno de los siguientes valores:

- [LayoutInDisplayCutoutModeNever](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_NEVER) &ndash; la ventana nunca se puede superponer con el área de recorte.

- [LayoutInDisplayCutoutModeShortEdges](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES) &ndash; la ventana se puede extender en el área de recorte, pero solo en los bordes cortos de la pantalla. 

- [LayoutInDisplayCutoutModeDefault](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_DEFAULT) &ndash; la ventana se puede extender en el área de recorte si el recorte está incluido en una barra del sistema.

Por ejemplo, para evitar que la ventana de la aplicación se superponga con el área de recorte, establezca el modo de recorte del diseño en *nunca*: 

```csharp
Window.Attributes.LayoutInDisplayCutoutMode =
    Android.Views.LayoutInDisplayCutoutMode.Never;
```

En los ejemplos siguientes se proporcionan ejemplos de estos modos de recorte. La primera captura de pantalla de la izquierda es la aplicación en modo sin pantalla completa. En la captura de pantalla del centro, la aplicación se rellenará con `LayoutInDisplayCutoutMode` establecida en `LayoutInDisplayCutoutModeShortEdges`. Tenga en cuenta que el fondo blanco de la aplicación se extiende en el área Mostrar recorte:

[![ejemplo muestran los modos de recorte en el emulador](pie-images/03-cutout-modes-sml.png)](pie-images/03-cutout-modes.png#lightbox)

En la captura de pantalla final (encima de la derecha), `LayoutInDisplayCutoutMode` se establece en `LayoutInDisplayCutoutModeShortNever` antes de que pase a la pantalla completa.
Tenga en cuenta que no se permite que el fondo blanco de la aplicación se extienda en el área Mostrar recorte.

Si necesita información más detallada sobre el área de recorte en el dispositivo, puede usar la nueva clase [DisplayCutout](https://developer.android.com/reference/android/view/DisplayCutout.html) . `DisplayCutout` representa el área de la pantalla que no se puede usar para mostrar el contenido. Puede usar esta información para recuperar la ubicación y la forma del recorte para que la aplicación no intente Mostrar contenido en este área no funcional.

Para obtener más información acerca de las nuevas características de recorte en Android P, consulte [Mostrar compatibilidad con recortes](https://developer.android.com/about/versions/pie/android-9.0#cutout).

### <a name="notifications-enhancements"></a>Mejoras en las notificaciones

El gráfico circular de Android presenta las siguientes mejoras para mejorar la experiencia de mensajería:

- Los canales de notificación (introducidos en [Android Oreo](~/android/platform/oreo.md)) ahora admiten el bloqueo de grupos de canales.

- El sistema de notificación tiene tres nuevas categorías de no molestar (priorizar alarmas, sonidos del sistema y orígenes multimedia). Además, hay siete nuevos modos de no molestar que se pueden usar para suprimir las interrupciones visuales (como los distintivos, las luces de notificación, la apariencia de la barra de estado y el inicio de las actividades de pantalla completa).

- Se ha agregado una nueva clase [Person](https://developer.android.com/reference/android/app/Person.html) para representar al remitente de un mensaje. El uso de esta clase ayuda a optimizar la representación de cada notificación al identificar a las personas implicadas en una conversación (incluidos los avatares y los URI).

- Las notificaciones ahora pueden mostrar imágenes. 

En el ejemplo siguiente se muestra cómo usar las nuevas API para generar una notificación que contiene una imagen. En las siguientes capturas de pantallas, se publica una notificación de texto y va seguida de una notificación con una imagen incrustada. Cuando las notificaciones se expanden (como se muestra a la derecha), se muestra el texto de la primera notificación y se amplía la imagen incrustada en la segunda notificación:

[![notificación de ejemplo con imagen](pie-images/04-example-notifications-sml.png)](pie-images/04-example-notifications.png#lightbox)

En el ejemplo siguiente se muestra cómo incluir una imagen en una notificación circular de Android y se muestra el uso de la nueva `Person` clase:

1. Cree un objeto `Person` que represente al remitente. Por ejemplo, el nombre y el icono del remitente se incluyen en `fromPerson`:

    ```csharp
    Icon senderIcon = Icon.CreateWithResource(this, Resource.Drawable.sender_icon);
    Person fromPerson = new Person.Builder()
        .SetIcon(senderIcon)
        .SetName("Mark Sender")
        .Build();
    ```

2. Cree una `Notification.MessagingStyle.Message` que contenga la imagen que se va a enviar, pasando la imagen al nuevo método [Notification. MessagingStyle. Message. SetData](https://developer.android.com/reference/android/app/Notification.MessagingStyle.Message.html#setData%28java.lang.String,%20android.net.Uri) .
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

Para obtener más información sobre la creación de notificaciones, consulte [notificaciones locales](~/android/app-fundamentals/notifications/local-notifications.md).

### <a name="indoor-positioning"></a>Posicionamiento interior

El gráfico circular de Android proporciona compatibilidad con IEEE 802.11 MC (también conocido como _tiempo de ida y vuelta_ _WiFi),_ lo que permite que las aplicaciones detecten la distancia a uno o varios puntos de acceso Wi-Fi. Con esta información, es posible que la aplicación aproveche el *posicionamiento interior* con una precisión de uno a dos metros. En los dispositivos Android que proporcionan compatibilidad de hardware para IEEE 801.11 MC, la aplicación puede ofrecer características de navegación, como el control basado en la ubicación de dispositivos inteligentes o las instrucciones que se activan a través de un almacén:

[![ejemplo de navegación en interiores con RTT de WiFi](pie-images/05-wifi-rtt-sml.png)](pie-images/05-wifi-rtt.png#lightbox)

La nueva clase [WifiRttManager](https://developer.android.com/reference/android/net/wifi/rtt/WifiRttManager) y varias clases auxiliares proporcionan los medios para medir la distancia a los dispositivos Wi-Fi. Para obtener más información sobre las API de posicionamiento interior introducidas en Android P, consulte [Android .net. WiFi. RTT](https://developer.android.com/reference/android/net/wifi/rtt/package-summary).

### <a name="multi-camera-support"></a>Compatibilidad con varias cámaras

Muchos dispositivos Android más recientes tienen cámaras duales y/o de doble retroceso que son útiles para características como la visión estéreo, los efectos visuales mejorados y la capacidad de zoom mejorada. Android P incorpora una nueva API de [varias cámaras](https://developer.android.com/about/versions/pie/android-9.0#camera) que permite que la aplicación use una *cámara lógica* (o *varias cámaras lógicas*) que esté respaldada por dos o más cámaras físicas.
Para determinar si el dispositivo es compatible con una cámara lógica múltiple, puede ver las capacidades de cada cámara en el dispositivo para ver si es compatible con [RequestAvailableCapabilitiesLogicalMultiCamera](https://developer.android.com/reference/android/hardware/camera2/CameraMetadata#REQUEST_AVAILABLE_CAPABILITIES_LOGICAL_MULTI_CAMERA).

Android circular también incluye una nueva clase [configuracióndesesión](https://developer.android.com/reference/android/hardware/camera2/params/SessionConfiguration.html) que se puede usar para ayudar a reducir los retrasos durante la captura inicial y eliminar la necesidad de iniciar e iniciar el flujo de la cámara.

Para obtener más información sobre la compatibilidad con varias cámaras en Android P, consulte [compatibilidad con múltiples cámaras y actualizaciones](https://developer.android.com/about/versions/pie/android-9.0#camera)de la cámara.

### <a name="other-features"></a>Otras características

Además, el gráfico circular de Android admite otras características nuevas:

- La nueva clase [AnimatedImageDrawable](https://developer.android.com/reference/android/graphics/drawable/AnimatedImageDrawable.html) , que se puede usar para dibujar y mostrar imágenes animadas.

- Nueva clase [ImageDecoder](https://developer.android.com/reference/android/graphics/ImageDecoder.html) que reemplaza `BitmapFactory`. `ImageDecoder` se puede usar para descodificar un `AnimatedImageDrawable`.

- Compatibilidad con imágenes HDR (alto rango dinámico) y vídeo HEIF (formato de archivo de imagen de alta eficiencia).

- El [JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html) se ha mejorado para controlar de forma más inteligente los trabajos relacionados con la red. El nuevo método [GetNetwork](https://developer.android.com/reference/android/app/job/JobParameters#getNetwork%28%29) de la clase [JobParameters](https://developer.android.com/reference/android/app/job/JobParameters) devuelve la mejor red para realizar cualquier solicitud de red para un trabajo determinado.

Para obtener más información sobre las características más recientes de los gráficos circulares de Android, consulte [características y API de Android 9](https://developer.android.com/about/versions/pie/android-9.0).

## <a name="behavior-changes"></a>Cambios de comportamiento

Cuando la versión de Android de destino se establece en el nivel de API 28, hay varios cambios de plataforma que pueden afectar al comportamiento de la aplicación incluso si no está implementando las nuevas características descritas anteriormente. La lista siguiente es un breve resumen de estos cambios:

- Las aplicaciones deben solicitar ahora permisos de primer plano antes de usar los servicios en primer plano.

- Si la aplicación tiene más de un proceso, no puede compartir un único directorio de datos de [vista](xref:Android.Webkit.WebView) única en todos los procesos.

- Ya no se permite acceder directamente al directorio de datos de otra aplicación mediante la ruta de acceso.

Para más información sobre los cambios de comportamiento de las aplicaciones destinadas a Android P, consulte [cambios de comportamiento](https://developer.android.com/about/versions/pie/android-9.0-changes-all#p-apps).

## <a name="sample-code"></a>Código de ejemplo

[AndroidPMiniDemo](https://github.com/xamarin/monodroid-samples/tree/master/android-p/AndroidPMiniDemo) es una aplicación de ejemplo de Xamarin. Android para un gráfico circular de Android que muestra cómo establecer los modos de recorte de pantalla, cómo usar la nueva clase de `Person` y cómo enviar una notificación que incluye una imagen.

## <a name="summary"></a>Resumen

En este artículo se presentó Android circular y se explica cómo instalar y configurar las herramientas y los paquetes más recientes para el desarrollo de Xamarin. Android con el gráfico circular de Android. Proporciona información general sobre las características clave disponibles en el gráfico circular de Android, con código fuente de ejemplo para algunas de estas características.
Incluye vínculos a la documentación de la API y temas para desarrolladores de Android que le ayudarán a empezar a crear aplicaciones para el gráfico circular de Android. También se resaltan los cambios de comportamiento de los sectores de Android más importantes que podrían afectar a las aplicaciones existentes.

## <a name="related-links"></a>Vínculos relacionados

- [Gráfico circular de Android 9](https://developer.android.com/about/versions/pie/)
