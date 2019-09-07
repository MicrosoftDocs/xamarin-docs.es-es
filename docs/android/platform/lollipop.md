---
title: Características de los círculos
description: En este artículo se proporciona información general de alto nivel sobre las nuevas características introducidas en Android 5,0 (Lollipop). Estas características incluyen un nuevo estilo de interfaz de usuario denominado "tema de material", así como nuevas características auxiliares, como animaciones, ver sombras y matices Dibujables. Android 5,0 también incluye notificaciones mejoradas, dos nuevos widgets de interfaz de usuario, un nuevo programador de trabajos y una serie de nuevas API para mejorar las capacidades de almacenamiento, redes, conectividad y multimedia.
ms.prod: xamarin
ms.assetid: 1CE99CFE-FAAC-49FC-AEDC-1A21FC6E946E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: a14ac0be141a473855dd2e9e19a57fd2d01649bb
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761931"
---
# <a name="lollipop-features"></a>Características de los círculos

_En este artículo se proporciona información general de alto nivel sobre las nuevas características introducidas en Android 5,0 (Lollipop). Estas características incluyen un nuevo estilo de interfaz de usuario denominado "tema de material", así como nuevas características auxiliares, como animaciones, ver sombras y matices Dibujables. Android 5,0 también incluye notificaciones mejoradas, dos nuevos widgets de interfaz de usuario, un nuevo programador de trabajos y una serie de nuevas API para mejorar las capacidades de almacenamiento, redes, conectividad y multimedia._

## <a name="lollipop-overview"></a>Información general sobre el círculo

Android 5,0 (Lollipop) incorpora un nuevo lenguaje de diseño, *diseño del material*y, además, ofrece una conversión de nuevas características para que las aplicaciones sean más fáciles y más intuitivas de usar. Con el diseño de material, Android 5,0 no solo ofrece teléfonos Android a Facelift; también proporciona un nuevo conjunto de reglas de diseño para tabletas basadas en Android, equipos de escritorio, relojes y televisores inteligentes. Estas reglas de diseño resaltan la simplicidad y el mínimo a la vez que usan atributos táctiles familiares (como las señales de borde y de superficie realistas) para ayudar a los usuarios a comprender la interfaz de forma rápida e intuitiva.

El *tema material* es el encarnación de estos principios de diseño de la interfaz de usuario en Android. En este artículo se explican las características auxiliares del tema de material:

- **Animaciones** Animaciones de comentarios táctiles, animaciones de transición de actividad, animaciones de transición de estado de vista y un efecto de revelar. &ndash;

- **Ver sombras y elevación** Las vistas ahora tienen `elevation` una propiedad; &ndash;   las vistas con `elevation` valores mayores proyectan sombras mayores en el fondo.

- **Características de color** El tinte que se puede *dibujar* permite volver a usar los recursos de imagen cambiando su color, y la *extracción de color prominente* le ayuda a hacer tema de forma dinámica a la aplicación en función de los colores de una imagen. &ndash;

Muchas características del tema material ya están integradas en la experiencia de interfaz de usuario de Android 5,0, mientras que otras deben agregarse explícitamente a las aplicaciones. Por ejemplo, algunas vistas estándar (como los botones) ya incluyen animaciones de comentarios táctiles, mientras que las aplicaciones deben habilitar la mayoría de las sombras de la vista.

Además de las mejoras de la interfaz de usuario que se han incorporado a través del tema material, Android 5,0 también incluye otras características nuevas que se describen en este artículo:

- **Notificaciones mejoradas** Las notificaciones en Android 5,0 se han actualizado de forma significativa con un nuevo aspecto, compatibilidad con las notificaciones de la pantalla de bloqueo y un nuevo formato de presentación de notificaciones *emergentes.* &ndash;

- **Widgets de interfaz de usuario nuevos** El nuevo `RecyclerView` widget facilita a las aplicaciones la transmisión de grandes conjuntos de datos y la información compleja, y `CardView` el nuevo widget proporciona un formato de presentación de tipo tarjeta simplificado para mostrar texto e imágenes. &ndash;

- **Nuevas API** &ndash; Android 5,0 agrega nuevas API para la compatibilidad con varias redes, conectividad Bluetooth mejorada, administración de almacenamiento más sencilla y un control más flexible de los reproductores multimedia y los dispositivos de cámara. Hay disponible una nueva característica de programación de trabajos para ejecutar tareas de forma asincrónica a horas programadas. Esta característica ayuda a mejorar la duración de la batería, por ejemplo, la programación de tareas que se deben realizar cuando el dispositivo está conectado y cargándose.

## <a name="requirements"></a>Requisitos

Para usar las nuevas características de Android 5,0 en aplicaciones basadas en Xamarin, se requiere lo siguiente:

- **Xamarin. Android** &ndash; Xamarin. Android 4,20 o posterior debe estar instalado y configurado con Visual Studio o Visual Studio para Mac. 

- **Android SDK** &ndash; Android 5,0 (API 21) o posterior debe instalarse a través del administrador de Android SDK.

- **Kit para desarrolladores de Java** Xamarin. Android requiere [JDK 1,8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o posterior si está desarrollando para el nivel de API 24 o superior (JDK 1,8 también admite niveles de API anteriores a 24, incluido el círculo). &ndash; Se requiere la versión de 64 bits de JDK 1,8 Si usa controles personalizados o la vista previa de formularios.

Puede seguir usando [JDK 1,7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) si está desarrollando específicamente para el nivel de API 23 o una versión anterior.

## <a name="setting-up-an-android-50-project"></a>Configuración de un proyecto de Android 5,0

Para crear un proyecto de Android 5,0, debe instalar las herramientas y los paquetes de SDK más recientes. Siga estos pasos para configurar un proyecto de Xamarin. Android que tenga como destino Android 5,0:

1. Instale las herramientas de Xamarin. Android y active la licencia de Xamarin. Consulte [configuración e instalación](~/android/get-started/installation/index.md) para obtener más información sobre la instalación de Xamarin. Android.

2. Si usa Visual Studio para Mac, instale las últimas actualizaciones de Android 5,0.

3. Inicie el administrador de Android SDK (en Visual Studio para Mac, **use &gt; herramientas abrir Android SDK&hellip;Manager**) e instale Android SDK Tools 23.0.5 o posterior:

    [![Selección de Android SDK herramientas en el administrador de Android SDK](lollipop-images/android-l-tools-sml.png)](lollipop-images/android-l-tools.png#lightbox)

   Además, instale los paquetes de SDK de Android 5,0 más recientes (API 21 o posterior):

    [![Instalación de paquetes de SDK de Android 5,0 en el administrador de Android SDK](lollipop-images/android-l-sdk-pkgs-sml.png)](lollipop-images/android-l-sdk-pkgs.png#lightbox)

   Para obtener más información acerca del uso del administrador de Android SDK, consulte [SDK Manager](https://developer.android.com/tools/help/sdk-manager.html).

4. Cree un nuevo proyecto de Xamarin. Android. Si no está familiarizado con el desarrollo de Android con Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para obtener información sobre la creación de proyectos de Android. Cuando cree un proyecto de Android, asegúrese de configurar las opciones de versión para Android 5,0.
   En Visual Studio para Mac, vaya a **opciones &gt; de proyecto &gt; generar general** y establezca **plataforma de destino** en **Android 5,0 (Lollipop)** o posterior:

    ![Establecimiento del Framwework de destino en Android 5,0 Lollipop](lollipop-images/target-framework.png)

   En **opciones &gt; de proyecto &gt; compilar aplicación de Android**, establecer versión de Android mínima y de destino en **la versión de .NET Framework de destino de uso automático**:

    ![Establecimiento de las versiones de Android mínima y de destino en automático](lollipop-images/minimum-android-version.png)

5. Configure un emulador o un dispositivo Android para probar la aplicación. Si usa un emulador, consulte [Android Emulator configuración](~/android/get-started/installation/android-emulator/index.md) para obtener información sobre cómo configurar un emulador de Android para su uso con Xamarin Studio o Visual Studio. Si usa un dispositivo Android, consulte [configuración del SDK de versión preliminar](https://developer.android.com/preview/setup-sdk.html) para obtener información sobre cómo actualizar el dispositivo para Android 5,0. Para configurar el dispositivo Android para ejecutar y depurar aplicaciones de Xamarin. Android, consulte [configurar el dispositivo para el desarrollo](~/android/get-started/installation/set-up-device-for-development.md).

Nota: Si va a actualizar un proyecto de Android existente que tenía como destino la versión preliminar de Android, debe actualizar la versión de **.NET Framework de destino** y **Android** a los valores descritos anteriormente.

## <a name="important-changes"></a>Cambios importantes

Las aplicaciones Android publicadas anteriormente podrían verse afectadas por los cambios en Android 5,0. En concreto, Android 5,0 usa un nuevo tiempo de ejecución y un formato de notificación que cambia significativamente.

### <a name="android-runtime"></a>Tiempo de ejecución de Android

Android 5,0 usa el nuevo tiempo de ejecución de Android (ART) como el tiempo de ejecución predeterminado en lugar de Dalvik. ART implementa varias características principales nuevas:

- **Compilación de antemano del tiempo (AOT)** &ndash; AOT puede mejorar el rendimiento de las aplicaciones compilando el código de la aplicación antes de que se inicie la aplicación por primera vez. Cuando se instala una aplicación, el arte genera un archivo ejecutable de aplicación compilada para el dispositivo de destino.

- **Recolección de elementos no utilizados mejorada (GC)** &ndash; Las mejoras de GC en arte también pueden mejorar el rendimiento de las aplicaciones. La recolección de elementos no utilizados ahora usa una pausa de GC en lugar de dos, y las operaciones de GC simultáneas se completan de manera más oportuna.

- **Depuración de aplicaciones mejorada** &ndash; Art proporciona más detalles de diagnóstico para ayudar a analizar excepciones e informes de bloqueo.

Las aplicaciones existentes deberían funcionar sin cambios en &ndash; el material gráfico, excepto para las aplicaciones que aprovechan técnicas únicas del tiempo de ejecución de Dalvik anterior, que puede que no funcionen en arte. Para obtener más información sobre estos cambios, consulte [comprobar el comportamiento de la aplicación en el tiempo de ejecución de Android (arte)](https://developer.android.com/guide/practices/verifying-apps-art.html).

### <a name="notification-changes"></a>Cambios de notificación

Las notificaciones han cambiado significativamente en Android 5,0:

- Los **sonidos y las vibraciones se tratan de forma diferente** `Ringtone` `Notification.Builder` `Vibrator` `MediaPlayer`Ahora se administran los sonidos y las vibraciones en lugar de, y. &ndash;

- **Nueva combinación de colores** &ndash; De acuerdo con el tema material, las notificaciones se representan con texto oscuro sobre el fondo blanco o muy ligero. Además, Android puede modificar los canales alfa en los iconos de notificación para que se coordinen con las combinaciones de colores del sistema. 

- **Notificaciones** de la pantalla de bloqueo &ndash; Las notificaciones ahora pueden aparecer en la pantalla de bloqueo del dispositivo.

- **Cabezas** &ndash; Las notificaciones de prioridad alta ahora aparecen en una pequeña ventana flotante (notificación de cierre) cuando el dispositivo está desbloqueado y la pantalla está activada.

En la mayoría de los casos, la migración de la funcionalidad de notificación de aplicación existente a Android 5,0 requiere los pasos siguientes:

1. Convierta el código para `Notification.Builder` usar ( `NotificationsCompat.Builder`o) para crear notificaciones. 

2. Compruebe que los recursos de notificación existentes estén visibles en la nueva combinación de colores del tema del material.

3. Decida qué visibilidad deben tener las notificaciones cuando se presentan en la pantalla de bloqueo. Si una notificación no es pública, ¿qué contenido debe aparecer en la pantalla de bloqueo?

4. Establezca la categoría de las notificaciones para que se controlen correctamente en el nuevo modo Android 5,0 no *molestar* .

Si las notificaciones presentan controles de transporte, muestran el estado de reproducción `RemoteControlClient`multimedia, el `ActivityManager.GetRecentTasks`uso o la llamada, consulte [cambios importantes](https://developer.android.com/preview/api-overview.html#Behaviors) en el comportamiento para obtener más información sobre cómo actualizar las notificaciones para Android 5,0.

Para obtener información sobre cómo crear notificaciones en Android, consulte [notificaciones locales](~/android/app-fundamentals/notifications/local-notifications.md).

## <a name="material-theme"></a>Tema de materiales

El nuevo tema material de Android 5,0 aporta cambios en la apariencia de la interfaz de usuario de Android. Los elementos visuales ahora usan superficies táctiles que toman los gráficos en negrita, la tipografía y los colores brillantes del diseño basado en la impresión. Los ejemplos de temas de material se representan en las siguientes capturas de pantallas:

[![Capturas de pantalla de la pantalla principal del tema del material, pantalla de aplicaciones y pantalla de configuración](lollipop-images/android-5-gallery-labeled-sml.png)](lollipop-images/android-5-gallery-labeled.png#lightbox)

Android 5,0 le muestra la pantalla principal que aparece a la izquierda. La captura de pantalla de Center es la primera pantalla de la lista de aplicaciones y la captura de pantalla de la derecha es la pantalla de **configuración** . La especificación de [diseño de material](https://material.io/guidelines/material-design/introduction.html) de Google explica las reglas de diseño subyacentes en el nuevo concepto de tema de materiales.

El tema material incluye tres tipos integrados que puede usar en la aplicación: el `Theme.Material` tema oscuro (el valor predeterminado), el `Theme.Material.Light` tema y el `Theme.Material.Light.DarkActionBar` tema: 

[![Capturas de pantallas de temas oscuros, claros y DarkActionBar](lollipop-images/three-material-themes-sml.png)](lollipop-images/three-material-themes.png#lightbox)

Para obtener más información sobre el uso de las características del tema material en las aplicaciones de Xamarin. Android, consulte el [tema material](~/android/user-interface/material-theme.md).

## <a name="animations"></a>Animaciones

Android 5,0 proporciona animaciones de comentarios táctiles, animaciones de transición de actividad y animaciones de transición de estado de vista para que las interfaces de aplicaciones sean más intuitivas de usar. Además, las aplicaciones de Android 5,0 pueden usar animaciones de *efecto de visualización* para ocultar o mostrar vistas. Puede usar la configuración de *movimiento curvado* para configurar el modo en que se representan las animaciones rápidamente o lentas.

### <a name="touch-feedback-animations"></a>Animaciones de comentarios táctiles

Las animaciones de comentarios táctiles proporcionan a los usuarios comentarios visuales cuando se toca una vista. Por ejemplo, los botones ahora muestran un efecto de rizo cuando se &ndash; tocan esta es la animación de comentarios táctiles predeterminada en Android 5,0. La nueva `RippleDrawable` clase implementa la animación Ripple. El efecto de rizo puede configurarse para que termine en los límites de la vista o se extienda más allá de los límites de la vista. Por ejemplo, la siguiente secuencia de capturas de pantalla muestra el efecto de rizo en un botón durante la animación táctil:

![Capturas de fotogramas fotogramas de la animación de rizo en un botón](lollipop-images/touch-animation.png)

El contacto táctil inicial con el botón aparece en la primera imagen de la izquierda, mientras que la secuencia restante (de izquierda a derecha) muestra cómo se distribuye el efecto de rizo al borde del botón. Cuando finaliza la animación Ripple, la vista vuelve a su apariencia original. La animación de rizo predeterminada tiene lugar en una fracción de segundo, pero la longitud de la animación se puede personalizar para una longitud de tiempo mayor o menor.

Para obtener más información sobre animaciones de comentarios táctiles en Android 5,0, consulte [Personalización de comentarios táctiles](https://developer.android.com/training/material/animations.html#Touch).

### <a name="activity-transition-animations"></a>Animaciones de transición de actividad

Las animaciones de transición de actividad proporcionan a los usuarios una sensación de continuidad visual cuando una actividad realiza una transición a otra. Las aplicaciones pueden especificar tres tipos de animaciones de transición:

- **Escribir transición** &ndash; Cuando una actividad entra en la escena.

- **Transición de salida** &ndash; Para cuando una actividad sale de la escena.

- **Transición de elementos compartidos** &ndash; Para cuando una vista que es común a dos actividades cambia a medida que la primera actividad pasa al siguiente.

Por ejemplo, la siguiente secuencia de capturas de pantallas muestra una transición de elementos compartidos:

[![Capturas de fotogramas fotogramas de una animación de transición de elementos compartidos](lollipop-images/activity-transition-sml.png)](lollipop-images/activity-transition.png#lightbox)

Un elemento compartido (una fotografía de un Caterpillar) es una de las diversas vistas de la primera actividad; se amplía para convertirse en la única vista de la segunda actividad a medida que la primera actividad pasa al segundo.

#### <a name="enter-transition-animation-types"></a>Escribir tipos de animación de transición

En el caso de las transiciones de entrada, Android 5,0 proporciona tres tipos de animaciones:

- **Expandir animación** &ndash; Amplía una vista desde el centro de la escena.

- **Animación de diapositivas** &ndash; Mueve una vista de uno de los bordes de una escena.

- **Animación de atenuación** &ndash; Atenúa una vista en la escena.

#### <a name="exit-transition-animation-types"></a>Tipos de animación de transición de salida

En el caso de las transiciones de salida, Android 5,0 proporciona tres tipos de animaciones:

- **Expandir animación** &ndash; Reduce una vista al centro de la escena.

- **Animación de diapositivas** &ndash; Mueve una vista a uno de los bordes de una escena.

- **Animación de atenuación** &ndash; Atenúa una vista fuera de la escena.

#### <a name="shared-element-transition-animation-types"></a>Tipos de animación de transición de elementos compartidos

Las transiciones de elementos compartidas admiten varios tipos de animaciones, como:

- Cambiar los límites de diseño o recorte de una vista.

- Cambiar la escala y la rotación de una vista.

- Cambiar el tamaño y el tipo de escala de una vista.

Para más información sobre las animaciones de transición de actividad en Android 5,0, consulte [Personalización de las transiciones de actividad](https://developer.android.com/training/material/animations.html#Transitions).

### <a name="view-state-transition-animations"></a>Ver animaciones de transición de estado

Android 5,0 permite que las animaciones se ejecuten cuando cambia el estado de una vista. Puede animar las transiciones de estado de vista mediante una de las técnicas siguientes:

- Cree drawables que animen los cambios de estado asociados a una vista determinada. La nueva `AnimatedStateListDrawable` clase permite crear drawables que muestran animaciones entre los cambios del estado de vista.

- Definir la funcionalidad de animación que se ejecuta cuando cambia el estado de una vista. La nueva `StateListAnimator` clase permite definir un animador que se ejecuta cuando cambia el estado de una vista.

Para más información sobre las animaciones de transición de estado de vista en Android 5,0, consulte [animar los cambios de estado de vista](https://developer.android.com/training/material/animations.html#ViewState).

### <a name="reveal-effect"></a>Mostrar efecto

El *efecto revelar* es un círculo de recorte que cambia el radio para mostrar u ocultar una vista. Puede controlar este efecto estableciendo el radio inicial y final del círculo de recorte. La siguiente secuencia de capturas de pantalla muestra una animación de efecto de visualización desde el centro de la pantalla:

[![Capturas de fotogramas de fotogramas de animación de revelar](lollipop-images/reveal-center-sml.png)](lollipop-images/reveal-center.png#lightbox)

La secuencia siguiente muestra una animación de efecto de visualización que tiene lugar desde la esquina inferior izquierda de la pantalla:

[![Capturas de fotogramas fotogramas de animación de recorte](lollipop-images/reveal-left-sml.png)](lollipop-images/reveal-left.png#lightbox)

Se pueden invertir animaciones de visualización. es decir, el círculo de recorte puede reducirse para ocultar la vista en lugar de ampliarla para mostrar la vista.

Para obtener más información sobre el efecto de revelar de Android 5,0 en, consulte [usar el efecto de revelar](https://developer.android.com/training/material/animations.html#Reveal).

### <a name="curved-motion"></a>Movimiento curvo

Además de estas características de animación, Android 5,0 también proporciona nuevas API que le permiten especificar las curvas de tiempo y movimiento de animaciones. Android 5,0 usa estas curvas para interpolar el movimiento temporal y espacial durante las animaciones. Se definen tres curvas en Android 5,0:

- **Fast\_out\_linealen\_** acelera&ndash; rápidamente y continúa acelerando hasta el final de la animación.

- **La velocidad\_de\_salida rápida rápida\_y lenta** se ralentiza rápidamente y lentamente hacia el final de la animación. &ndash;

- **La tecnología\_linealde\_baja velocidad comienza con una velocidad máxima y ralentiza lentamente hasta el final de la animación.\_** &ndash;

Puede usar la nueva `PathInterpolator` clase para especificar cómo se realiza la interpolación de movimiento. `PathInterpolator`es un interpolador que atraviesa las rutas de animación según los puntos de control y las curvas de movimiento especificados. Para obtener más información sobre cómo especificar la configuración de movimiento curvado en Android 5,0, consulte [usar movimiento curvo](https://developer.android.com/training/material/animations.html#CurvedMotion).

## <a name="view-shadows--elevation"></a>Ver sombras & elevación

En Android 5,0, puede especificar la *elevación* de una vista mediante el establecimiento de una `Z` nueva propiedad. Un valor `Z` mayor hace que la vista convierta una sombra más grande en segundo plano, lo que hace que la vista parezca más flotante encima del fondo. Puede establecer la elevación inicial de una vista configurando su `elevation` atributo en el diseño.

En el ejemplo siguiente se muestran las sombras convertidas por `TextView` un control vacío cuando su atributo de elevación está establecido en 2DP, 4DP y 6DP, respectivamente:

[![Capturas de pantallas de progessively de vista más grande](lollipop-images/view-shadows-sml.png)](lollipop-images/view-shadows.png#lightbox)

La configuración de sombra de vista puede ser estática (como se mostró anteriormente) o se puede usar en animaciones para que aparezca una vista que se encuentra por encima del fondo de la vista. Puede utilizar la `ViewPropertyAnimator` clase para animar la elevación de una vista. La elevación de una vista es la suma de su configuración `elevation` de diseño más `translationZ` una propiedad que se puede establecer a `ViewPropertyAnimator` través de una llamada al método.

Para obtener más información sobre cómo ver las sombras en Android 5,0, consulte [definir sombras y vistas de recorte](https://developer.android.com/training/material/shadows-clipping.html).

## <a name="color-features"></a>Características de color

Android 5,0 proporciona dos nuevas características para administrar el color de las aplicaciones:

- La *tinción dibujable* permite modificar los colores de los recursos de imagen mediante el cambio de un atributo de diseño.

- La *extracción de color prominente* permite personalizar dinámicamente el tema de color de la aplicación para que se coordine con la paleta de colores de una imagen mostrada.

### <a name="drawable-tinting"></a>Tinción dibujable

Los diseños de Android 5,0 reconocen un `tint` nuevo atributo que puede usar para establecer el color de drawables sin tener que crear varias versiones de estos recursos para mostrar distintos colores. Para usar esta característica, debe definir un mapa de bits como una máscara alfa y `tint` usar el atributo para definir el color del recurso. Esto permite crear activos una vez y colorearlos en el diseño para que coincidan con el tema.

En el ejemplo siguiente, se usa un único &ndash; recurso de imagen con un logotipo blanco &ndash; con un fondo transparente para crear variaciones de matiz:

![Logotipo de Xamarin blanco con fondo transparente](lollipop-images/xamarin-logo-white.png)

Este logotipo se muestra sobre un fondo circular azul, tal como se muestra en los ejemplos siguientes. La imagen de la izquierda es la forma en que aparece el `tint` logotipo sin una configuración. En la imagen central, el atributo del `tint` logotipo se establece en un gris oscuro. En la imagen de la derecha, `tint` se establece en gris claro:

![Ejemplos del logotipo anterior con una configuración de matiz diferente](lollipop-images/drawable-tinting.png)

Para obtener más información sobre la tinción dibujable en Android 5,0, consulte [creación de matices Dibujables](https://developer.android.com/training/material/drawables.html#DrawableTint).

### <a name="prominent-color-extraction"></a>Extracción de color prominente

La nueva clase de `Palette` Android 5,0 le permite extraer colores de una imagen para que pueda aplicarlos dinámicamente a una paleta de colores personalizada. La `Palette` clase extrae seis colores de una imagen y etiqueta estos colores según sus niveles relativos de saturación de color y brillo:

- Pantallas

- Vibrante oscuro

- Luz vibrante

- Silenciada

- Silenciado oscuro

- Luz silenciada

Por ejemplo, en las siguientes capturas de pantalla, una aplicación de visualización de fotografías extrae los colores destacados de la imagen en la pantalla y usa estos colores para adaptar la combinación de colores de la aplicación para que coincida con la imagen:

[![Capturas de pantallas de extracciones de color de tema verde, rosa y azul](lollipop-images/prominent-color-extraction-sml.png)](lollipop-images/prominent-color-extraction.png#lightbox)

En las capturas de pantallas anteriores, la barra de acción se establece en el color "vibrante claro" extraído y el fondo se establece en el color "oscuro oscuro" extraído. En cada ejemplo anterior, se incluye una fila de pequeños cuadrados de color para ilustrar los colores de la paleta que se extrajeron de la imagen.

Para más información sobre la extracción de color en Android 5,0, consulte [extracción de colores destacados de una imagen](https://developer.android.com/training/material/drawables.html#ColorExtract).

## <a name="new-ui-widgets"></a>Widgets de interfaz de usuario nuevos

Android 5,0 presenta dos nuevos widgets de interfaz de usuario:

- `RecyclerView`&ndash; Un grupo de vistas que muestra una lista de elementos desplazables.

- `CardView`&ndash; Diseño básico con esquinas redondeadas.

Ambos widgets incluyen compatibilidad integrada con las características del tema de materiales; por ejemplo, `RecyclerView` usa animaciones para agregar y quitar vistas, y `CardView` usa las sombras de la vista para que cada tarjeta parezca flotar encima del fondo. En las siguientes capturas de pantallas se muestran ejemplos de estos nuevos widgets:

[![Capturas de pantallas de aplicaciones compiladas con RecyclerView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

La captura de pantalla de la izquierda es un `RecyclerView` ejemplo de tal como se usa en una aplicación de correo electrónico, y la captura de `CardView` pantalla de la derecha es un ejemplo de tal como se usa en una aplicación de reserva de viajes.

### <a name="recyclerview"></a>RecyclerView

`RecyclerView`es similar a `ListView,` , pero es más adecuado para conjuntos grandes de vistas o listas con elementos que cambian dinámicamente. Al `ListView,` igual que especifica un adaptador para tener acceso al conjunto de datos subyacente. Sin embargo, a `ListView,` diferencia de se usa un *Administrador de diseño* para `RecyclerView`colocar los elementos dentro de. El administrador de diseño también se encarga del reciclaje de vistas; administra la reutilización de las vistas de elementos que ya no son visibles para el usuario.

Cuando se usa un `RecyclerView` widget, debe especificar un `LayoutManager` y un adaptador. Como se muestra en esta ilustración `LayoutManager` , es el intermediario entre el adaptador `RecyclerView`y:

![Diagrama de RecyclerView con LayoutManager, Adapter y DataSet de compatibilidad](lollipop-images/recyclerview-diagram.png)

Las siguientes capturas de `RecyclerView` pantallas muestran un que contiene 100 elementos (cada elemento `ImageView` consta de `TextView`un y un):

[![Capturas de pantallas de una aplicación RecyclerView desplazarse a través de imágenes](lollipop-images/recyclerview-scroll-sml.png)](lollipop-images/recyclerview-scroll.png#lightbox)

`RecyclerView`controla este conjunto de datos de gran &ndash; tamaño con un desplazamiento sencillo desde el principio de la lista hasta el final de la lista en esta aplicación de ejemplo solo tarda unos segundos. `RecyclerView`también admite animaciones; de hecho, las animaciones para agregar y quitar elementos están habilitadas de forma predeterminada. Cuando se agrega un elemento a un `RecyclerView`, se atenúa como se muestra en esta secuencia de capturas de pantallas:

[![Captura de pantalla de fotogramas de fotogramas de un elemento de fotografía fundido](lollipop-images/recyclerview-animation-sml.png)](lollipop-images/recyclerview-animation.png#lightbox)

Para obtener más `RecyclerView`información sobre, vea [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).

### <a name="cardview"></a>CardView

`CardView`es una vista simple que simula una tarjeta flotante con esquinas redondeadas. Dado `CardView` que tiene sombras de vistas integradas, proporciona una manera sencilla de agregar profundidad visual a la aplicación. Las capturas de pantallas siguientes muestran tres ejemplos orientados a texto de `CardView`:

[![Capturas de pantallas de ejemplo de aplicaciones que usan RecyclerView con elementos basados en CardView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

Cada una de las tarjetas del ejemplo anterior contiene un `TextView`; el color de fondo se establece mediante `cardBackgroundColor` el atributo.

Para obtener más `CardView`información acerca de, consulte [CardView](~/android/user-interface/controls/card-view.md).

## <a name="enhanced-notifications"></a>Notificaciones mejoradas

El sistema de notificación en Android 5,0 se ha actualizado significativamente con un nuevo formato visual y nuevas características. Las notificaciones tienen una nueva apariencia en Android 5,0. Por ejemplo, las notificaciones en Android 5,0 ahora usan texto oscuro sobre un fondo claro:

![Ejemplo de una notificación de Android 5,0 no expandida](lollipop-images/expanded-notification-contracted.png)

Cuando se muestra un icono grande en una notificación (como se muestra en el ejemplo anterior), Android 5,0 presenta el icono pequeño como un distintivo en el icono de gran tamaño. 

En Android 5,0, las notificaciones también pueden aparecer en la pantalla de bloqueo del dispositivo.
Por ejemplo, a continuación se muestra una captura de pantalla de ejemplo de un bloqueo de pantalla con una sola notificación:

[![Captura de pantalla de la notificación que aparece en la pantalla de bloqueo](lollipop-images/lockscreen-notification-sml.png)](lollipop-images/lockscreen-notification.png#lightbox)

Los usuarios pueden hacer doble punteo en una notificación en la pantalla de bloqueo para desbloquear el dispositivo y saltar a la aplicación que originó esa notificación, o bien deslizar para descartar la notificación. Las notificaciones tienen una nueva configuración de *visibilidad* que determina la cantidad de contenido que se puede mostrar en la pantalla de bloqueo. Los usuarios pueden elegir si desea permitir que se muestre contenido confidencial en las notificaciones de la pantalla de bloqueo.

Android 5,0 presenta un nuevo formato de presentación de notificaciones de alta prioridad denominado " *Head-up*". Las notificaciones de los cabezales se desplazan hacia abajo desde la parte superior de la pantalla durante unos segundos y, a continuación, se revierten a la sombra de la notificación en la parte superior de la pantalla. Las notificaciones de cabezales permiten que la interfaz de usuario del sistema Coloque información importante delante del usuario sin interrumpir la actividad que se está ejecutando actualmente. En el ejemplo siguiente se muestra una notificación de suscripción sencilla que se muestra en la parte superior de una aplicación:

[![Ejemplo de una notificación de cabeza](lollipop-images/heads-up-notification-sml.png)](lollipop-images/heads-up-notification.png#lightbox)

Las notificaciones de suscripción se suelen usar para los siguientes eventos:

- Nuevo mensaje siguiente

- Una llamada telefónica entrante

- Indicación de batería baja

- Una alarma

Android 5,0 solo muestra una notificación en formato de cara al día cuando tiene un valor de prioridad alto o máximo.

En Android 5,0, puede proporcionar metadatos de notificación para ayudar a Android a ordenar y Mostrar notificaciones de forma más inteligente. Android 5,0 organiza las notificaciones según la prioridad, la visibilidad y la categoría.
Las categorías de notificación se usan para filtrar las notificaciones que se pueden presentar cuando el dispositivo está en modo *no molesta* .

Para obtener información detallada sobre la creación y el inicio de notificaciones con las últimas características de Android 5,0, consulte [notificaciones locales](~/android/app-fundamentals/notifications/local-notifications.md).

## <a name="new-apis"></a>Nuevas API.

Además de las nuevas características que se han descrito anteriormente, Android 5,0 agrega nuevas API que amplían las capacidades de la funcionalidad de multimedia, almacenamiento y conectividad inalámbrica existente. Además, Android 5,0 incluye nuevas API que proporcionan compatibilidad con una nueva característica de programador de trabajos.

### <a name="camera"></a>Cámara

Android 5,0 proporciona varias API nuevas para mejorar las capacidades de la cámara. El nuevo `Android.Hardware.Camera2` espacio de nombres incluye funcionalidad para tener acceso a dispositivos de cámara individuales conectados a un dispositivo Android. Además, `Android.Hardware.Camera2` modela cada dispositivo de cámara como una canalización: acepta una solicitud de captura, captura la imagen y, a continuación, genera el resultado. Este enfoque permite a las aplicaciones poner en cola varias solicitudes de captura en un dispositivo de cámara.

Las siguientes API hacen posibles estas nuevas características:

- `CameraManager.GetCameraIdList`Ayuda a obtener acceso mediante programación a los dispositivos de cámara `CameraManager.OpenCamera` ; se usa para conectarse a un dispositivo de cámara específico. &ndash;

- `CameraCaptureSession`&ndash; Captura o transmite imágenes desde el dispositivo de cámara. La `CameraCaptureSession.CaptureListener` interfaz se implementa para controlar los nuevos eventos de captura de imagen.

- `CaptureRequest`&ndash; Define los parámetros de captura.

- `CaptureResult`&ndash; Proporciona los resultados de una operación de captura de imagen.

Para obtener más información acerca de las nuevas API de cámara en Android 5,0, consulte [multimedia](https://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="audio-playback"></a>Reproducción de audio

Android 5,0 actualiza la `AudioTrack` clase para una mejor reproducción de audio:

- `ENCODING_PCM_FLOAT`&ndash; Configura para aceptar los datos de audio en formato de punto flotante para un mejor intervalo dinámico, mayor espacio y mayor calidad (gracias a la mayor precisión). `AudioTrack` Además, el formato de punto flotante ayuda a evitar el recorte de audio.

- `ByteBuffer`Ahora puede proporcionar datos de audio a `AudioTrack` como una matriz de bytes. &ndash;

- `WRITE_NON_BLOCKING`&ndash; Esta opción simplifica el almacenamiento en búfer y multithreading para algunas aplicaciones.

Para obtener más `AudioTrack` información sobre las mejoras en Android 5,0, consulte [multimedia](https://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="media-playback-control"></a>Control de reproducción multimedia

Android 5,0 presenta la nueva `Android.Media.MediaController` clase, que reemplaza `RemoteControlClient`a. `Android.Media.MediaController`proporciona API de control de transporte simplificado y ofrece control seguro para subprocesos de la reproducción fuera del contexto de la interfaz de usuario. Las siguientes API nuevas administran el control de transporte:

- `Android.Media.Session.MediaSession`&ndash; Una sesión de control multimedia que controla varios controladores. Llame `MediaSession.GetSessionToken` a para solicitar un token que la aplicación use para interactuar con la sesión.

- `MediaController.TransportControls`Controla los comandos de transporte, como **reproducir**, **detener**y **omitir.** &ndash;

Además, puede usar la nueva `Android.App.Notification.MediaStyle` clase para asociar una sesión multimedia a un contenido de notificación enriquecido (como la extracción y visualización de carátulas de álbum).

Para obtener más información acerca de las nuevas características de control de reproducción multimedia en Android 5,0, consulte [multimedia](https://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="storage"></a>Almacenamiento

Android 5,0 actualiza el marco de acceso de almacenamiento para que las aplicaciones funcionen con directorios y documentos más fácilmente:

- Para seleccionar un subárbol de directorio, puede crear y enviar un `Android.Intent.Action.OPEN_DOCUMENT_TREE` intento. Esta intención hace que el sistema muestre todas las instancias de proveedor que admiten la selección de subárbol. a continuación, el usuario examina y selecciona un directorio.

- Para crear y administrar nuevos documentos o directorios en cualquier parte de un subárbol, use los `CreateDocument`nuevos `RenameDocument`métodos, `DeleteDocument` y de `DocumentsContract`.

- Para obtener las rutas de acceso a los directorios de medios en todos los dispositivos de `Android.Content.Context.GetExternalMediaDirs` almacenamiento compartido, se llama al método New.

Para más información sobre las nuevas API de almacenamiento en Android 5,0, consulte [almacenamiento](https://developer.android.com/preview/api-overview.html#Storage).

### <a name="wireless--connectivity"></a>Conectividad de & inalámbrica

Android 5,0 agrega las siguientes mejoras de API para la conectividad inalámbrica y la conectividad:

- Nuevas API de *múltiples redes* que permiten a las aplicaciones buscar y seleccionar redes con capacidades específicas antes de establecer una conexión.

- Funcionalidad de difusión de Bluetooth que permite a un dispositivo Android 5,0 actuar como periféricos Bluetooth de bajo consumo de energía.

- Mejoras de NFC que facilitan el uso de la funcionalidad de comunicaciones de campo Near para compartir datos con otros dispositivos.

Para más información sobre las nuevas API de conectividad y redes inalámbricas en Android 5,0, consulte [conectividad inalámbrica y conectividad](https://developer.android.com/preview/api-overview.html#Wireless).

### <a name="job-scheduling"></a>Programación de trabajos

Android 5,0 introduce una nueva `JobScheduler` API que puede ayudar a los usuarios a minimizar la duración de la batería mediante la programación de determinadas tareas que se ejecutan solo cuando el dispositivo está conectado y cargándose. Esta característica de programador de trabajos también puede usarse para programar una tarea para que se ejecute cuando las condiciones sean más adecuadas para esa tarea, como la descarga de un archivo grande cuando el dispositivo está conectado a través de una red Wi-Fi en lugar de una red de uso medido.

Para más información sobre las nuevas API de programación de trabajos en Android 5,0, consulte [programación de trabajos](https://developer.android.com/preview/api-overview.html#JobScheduler).

## <a name="summary"></a>Resumen

En este artículo se proporciona información general sobre las nuevas características importantes de Android 5,0 para los desarrolladores de aplicaciones de Xamarin. Android:

- Tema de materiales

- Animaciones

- Ver sombras y elevación

- Características de color, como la tinción dibujable y la extracción de color prominente

- Los nuevos `RecyclerView` widgets y `CardView`

- Mejoras en las notificaciones

- Nuevas API para la cámara, la reproducción de audio, el control de medios, el almacenamiento, la conectividad inalámbrica y la programación de trabajos

Si no está familiarizado con el desarrollo de Xamarin Android, lea [instalación e instalación](~/android/get-started/installation/index.md) para ayudarle a empezar a trabajar con Xamarin. Android.
[Hello, Android](~/android/get-started/hello-android/index.md) es una excelente introducción para aprender a crear proyectos de Android.

## <a name="related-links"></a>Vínculos relacionados

- [Versión preliminar para desarrolladores de Android L](https://developer.android.com/preview/index.html)
- [Obtener la Android SDK](https://developer.android.com/sdk/index.html#Other)
- [Diseño de materiales](https://developer.android.com/preview/material/index.html)
- [Principios del diseño de materiales](http://static.googleusercontent.com/media/www.google.com/en/us/design/material-design.pdf)
