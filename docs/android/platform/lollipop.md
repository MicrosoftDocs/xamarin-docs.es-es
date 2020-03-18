---
title: Características de Lollipop
description: En este artículo se proporciona una introducción de alto nivel a las nuevas características incluidas en Android 5.0 (Lollipop). Estas características incluyen un nuevo estilo de interfaz de usuario denominado Tema de materiales, así como nuevas características complementarias, como animaciones, visualización de sombras y matices dibujables. Android 5.0 también incluye notificaciones mejoradas, dos nuevos widgets de interfaz de usuario, un nuevo programador de trabajos y un puñado de nuevas API para mejorar las funcionalidades de almacenamiento, redes, conectividad y multimedia.
ms.prod: xamarin
ms.assetid: 1CE99CFE-FAAC-49FC-AEDC-1A21FC6E946E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 297c7806ce8a880d65c38ef0e4672e41fee5acfe
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "76724447"
---
# <a name="lollipop-features"></a>Características de Lollipop

_En este artículo se proporciona una introducción de alto nivel a las nuevas características incluidas en Android 5.0 (Lollipop). Estas características incluyen un nuevo estilo de interfaz de usuario denominado Tema de materiales, así como nuevas características complementarias, como animaciones, visualización de sombras y matices dibujables. Android 5.0 también incluye notificaciones mejoradas, dos nuevos widgets de interfaz de usuario, un nuevo programador de trabajos y un puñado de nuevas API para mejorar las funcionalidades de almacenamiento, redes, conectividad y multimedia._

## <a name="lollipop-overview"></a>Introducción a Lollipop

Android 5.0 (Lollipop) incorpora un nuevo lenguaje de diseño, *Material Design*, y, con él, un elenco que sustenta las nuevas características para un uso más fácil e intuitivo de las aplicaciones. Con Material Design, Android 5.0 no solo proporciona a los teléfonos Android un lavado de cara, sino también un nuevo conjunto de reglas de diseño para tabletas, equipos de escritorio, relojes y televisores inteligentes basados en Android. Estas reglas de diseño resaltan la simplicidad y el minimalismo, a la vez que hacen uso de atributos táctiles familiares (como señales realistas de borde y superficie) para ayudar a los usuarios a comprender la interfaz de forma rápida e intuitiva.

El *tema de materiales* es la encarnación de estos principios de diseño de la interfaz de usuario de Android. Para comenzar, en este artículo se explican las características que sustentan el tema de materiales:

- **Animaciones**: animaciones de *respuestas táctiles*, animaciones de *transición de actividad*, animaciones de *transición de estado de las vistas* y animaciones de *efecto revelar*.

- **Sombras y elevación de vistas**: las vistas ahora tienen una propiedad `elevation`; las vistas con valores más altos que `elevation` proyectan sombras más grandes en el fondo.

- **Características de color**: *Drawable tinting* (Matices dibujables) permite reutilizar los recursos de imagen con un cambio de color, y *Prominent color extraction* (Extracción del color prominente) ayuda a asignar un tema de forma dinámica a la aplicación según los colores de una imagen.

Muchas características del tema de materiales ya están integradas en la experiencia de interfaz de usuario de Android 5.0, mientras que otras deben agregarse explícitamente a las aplicaciones. Por ejemplo, algunas vistas estándar (como los botones) ya incluyen animaciones de respuestas táctiles, mientras que las aplicaciones deben habilitar la mayoría de las sombras de vistas.

Además de las mejoras de la interfaz de usuario que se han incorporado a través del tema de materiales, Android 5.0 también incluye otras diversas características que se describen en este artículo:

- **Notificaciones mejoradas**: las notificaciones de Android 5.0 se han actualizado de forma significativa con un nuevo aspecto, la compatibilidad con notificaciones de la pantalla de bloqueo y un nuevo formato de presentación de notificaciones de *Aviso*.

- **Nuevos widgets de la interfaz de usuario**: el nuevo widget `RecyclerView` permite a las aplicaciones transmitir fácilmente grandes conjuntos de datos e información compleja; y el nuevo widget `CardView` ofrece un nuevo formato de presentación tipo tarjeta para mostrar texto e imágenes.

- **Nuevas API**: Android 5.0 agrega nuevas API para la compatibilidad con varias redes, mejor conectividad Bluetooth, administración más fácil del almacenamiento y control más flexible de los reproductores multimedia y los dispositivos de cámara. Existe una nueva característica de programación de trabajos para ejecutar tareas de forma asincrónica a las horas programadas. Esta característica ayuda a mejorar la duración de la batería (por ejemplo, programar tareas para que tengan lugar cuando el dispositivo esté conectado y cargándose).

## <a name="requirements"></a>Requisitos

Para usar las nuevas características de Android 5.0 en aplicaciones basadas en Xamarin, se requiere lo siguiente:

- **Xamarin.Android**: Xamarin.Android 4.20 o posterior debe estar instalado y configurado con Visual Studio o Visual Studio para Mac.

- **Android SDK**: Android SDK 5.0 (API 21) o posterior debe estar instalado mediante el Administrador de Android SDK.

- **Kit para desarrolladores de Java**: Xamarin.Android requiere [JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o posterior si va a desarrollar para el nivel de API 24 o superior (JDK 1.8 también admite niveles de API anteriores a 24, incluido Lollipop). Se requiere la versión de 64 bits del JDK 1.8 si usa controles personalizados o el controlador de vista previa de Forms.

Si va a desarrollar contenido específicamente para el nivel de API 23 o uno anterior, puede continuar usando [JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html).

## <a name="setting-up-an-android-50-project"></a>Configuración de un proyecto de Android 5.0

Para crear un proyecto de Android 5.0, debe instalar las herramientas y los paquetes de SDK más recientes. Siga estos pasos para configurar un proyecto de Xamarin.Android dirigido a Android 5.0:

1. Instale las herramientas de Xamarin.Android y active la licencia de Xamarin. Consulte [Configuración e instalación](~/android/get-started/installation/index.md) para más información sobre la instalación de Xamarin.Android.

2. Si usa Visual Studio para Mac, instale las últimas actualizaciones de Android 5.0.

3. Inicie el Administrador de Android SDK (en Visual Studio for Mac, use **Herramientas &gt; Abrir el Administrador de Android SDK&hellip;** ) e instale Android SDK Tools 23.0.5 o posterior:

    [![Selección de herramientas de Android SDK en el Administrador de Android SDK](lollipop-images/android-l-tools-sml.png)](lollipop-images/android-l-tools.png#lightbox)

   Además, instale los paquetes del SDK de Android 5.0 más recientes (API 21 o posterior):

    [![Instalación de paquetes del SDK de Android 5.0 en el Administrador de Android SDK](lollipop-images/android-l-sdk-pkgs-sml.png)](lollipop-images/android-l-sdk-pkgs.png#lightbox)

   Para más información sobre el uso del Administrador de Android SDK, consulte [Administrador de SDK](https://developer.android.com/tools/help/sdk-manager.html).

4. Cree un proyecto de Xamarin.Android. Si no está familiarizado con el desarrollo de Android con Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para aprender sobre la creación de proyectos de Android. Cuando cree un proyecto de Android, asegúrese de configurar las opciones de versión para Android 5.0.
   En Visual Studio para Mac, vaya a **Opciones de proyecto &gt; Compilar &gt; General** y establezca **Plataforma de destino** en **Android 5.0 (Lollipop)** o posterior:

    ![Establecimiento del marco de destino en Android 5.0 Lollipop](lollipop-images/target-framework.png)

   En **Opciones de proyecto &gt; Compilar &gt; Aplicación Android**, establezca la versión de Android mínima y de destino en **Automatic - use target framework version** (Automática: usar la versión de la plataforma de destino):

    ![Establecimiento de las versiones de Android mínima y de destino en Automática](lollipop-images/minimum-android-version.png)

5. Configure un emulador o un dispositivo Android para probar la aplicación. Si usa un emulador, consulte [Configuración de Android Emulator](~/android/get-started/installation/android-emulator/index.md) para aprender a configurar un emulador de Android para su uso con Xamarin Studio o Visual Studio. Si usa un dispositivo Android, consulte [Configuración de la versión preliminar del SDK](https://developer.android.com/preview/setup-sdk.html) para saber cómo actualizar el dispositivo para Android 5.0. Para configurar el dispositivo Android para ejecutar y depurar aplicaciones Xamarin.Android, consulte [Configurar el dispositivo para el desarrollo](~/android/get-started/installation/set-up-device-for-development.md).

Nota: Si va a actualizar un proyecto de Android existente que tiene como destino la versión preliminar de Android L, debe actualizar la **plataforma de destino** y la **versión de Android** a los valores descritos anteriormente.

## <a name="important-changes"></a>Cambios importantes

Las aplicaciones Android publicadas anteriormente podrían verse afectadas por los cambios de Android 5.0. En concreto, Android 5.0 usa un nuevo entorno de ejecución y un formato de notificación que cambia significativamente.

### <a name="android-runtime"></a>Entorno de ejecución de Android

Android 5.0 usa el nuevo entorno de ejecución de Android (ART) como entorno de ejecución predeterminado en lugar de Dalvik. ART implementa varias características principales nuevas:

- **Compilación Ahead-of-time (AOT)** : AOT puede compilar el código de la aplicación antes de iniciar esta para mejorar el rendimiento. Cuando se instala una aplicación, ART genera un archivo ejecutable de aplicación compilado para el dispositivo de destino.

- **Recolección de elementos no utilizados (GC) mejorada**: las mejoras de GC en ART también pueden mejorar el rendimiento de las aplicaciones. La recolección de elementos no utilizados ahora usa una pausa de GC en lugar de dos, y las operaciones de GC simultáneas se completan de manera más oportuna.

- **Depuración de aplicaciones mejorada**: ART proporciona más detalles de diagnóstico para ayudar a analizar excepciones e informes de bloqueo.

Las aplicaciones existentes funcionarán sin cambios en ART, excepto aquellas que aprovechan técnicas únicas del entorno de ejecución de Dalvik anterior, que es posible que no funcionen. Para más información sobre estos cambios, consulte [Verificar el comportamiento de la aplicación en el tiempo de ejecución de Android (ART)](https://developer.android.com/guide/practices/verifying-apps-art.html).

### <a name="notification-changes"></a>Cambios de notificación

Las notificaciones han cambiado considerablemente en Android 5.0:

- **Los sonidos y las vibraciones se tratan de forma diferente**: los sonidos y las vibraciones de la notificación se controlan ahora mediante `Notification.Builder` en lugar de `Ringtone`, `MediaPlayer` y `Vibrator`.

- **Nueva combinación de colores**: de acuerdo con el tema de materiales, las notificaciones se representan con texto oscuro sobre fondo blanco o muy claro. Además, Android puede modificar los canales alfa de los iconos de notificación para que se coordinen con las combinaciones de colores del sistema.

- **Notificaciones de pantalla de bloqueo**: las notificaciones ahora pueden aparecer en la pantalla de bloqueo del dispositivo.

- **Avisos**: las notificaciones de prioridad alta ahora aparecen en una pequeña ventana flotante (notificación de avisos) cuando el dispositivo está desbloqueado y la pantalla está activada.

En la mayoría de los casos, para migrar la funcionalidad de notificación en la aplicación existente a Android 5.0 se necesitan los pasos siguientes:

1. Convierta el código para usar `Notification.Builder` (o `NotificationsCompat.Builder`) para crear notificaciones.

2. Compruebe que los recursos de notificación existentes estén visibles en la nueva combinación de colores del tema de materiales.

3. Decida qué visibilidad deben tener las notificaciones cuando se presentan en la pantalla de bloqueo. Si una notificación no es pública, ¿qué contenido debe aparecer en la pantalla de bloqueo?

4. Establezca la categoría de las notificaciones para que se controlen correctamente en el nuevo modo *No molestar* de Android 5.0.

Si las notificaciones presentan controles de transporte, muestran el estado de reproducción multimedia, usan `RemoteControlClient` o llaman a `ActivityManager.GetRecentTasks`, consulte [Cambios importantes en el comportamiento](https://developer.android.com/preview/api-overview.html#Behaviors) para más información sobre cómo actualizar las notificaciones para Android 5.0.

Para información sobre cómo crear notificaciones en Android, consulte [Notificaciones locales](~/android/app-fundamentals/notifications/local-notifications.md).

## <a name="material-theme"></a>Tema de materiales

El nuevo tema de materiales de Android 5.0 supone cambios radicales en la apariencia de la interfaz de usuario de Android. Los elementos visuales ahora usan superficies táctiles que toman los gráficos en negrita, la tipografía y los colores brillantes del diseño basado en la impresión. En las siguientes capturas de pantallas se representan ejemplos del tema de materiales:

[![Capturas de pantalla de la pantalla principal del tema de materiales, pantalla de aplicaciones y pantalla de configuración](lollipop-images/android-5-gallery-labeled-sml.png)](lollipop-images/android-5-gallery-labeled.png#lightbox)

Android 5.0 le saluda con la pantalla principal que aparece a la izquierda. La captura de pantalla central es la primera pantalla de la lista de aplicaciones y la captura de pantalla de la derecha es la pantalla de **Configuración**. La especificación [Material Design](https://material.io/guidelines/material-design/introduction.html) de Google explica las reglas de diseño que subyacen al nuevo concepto de tema de materiales.

El tema de materiales incluye tres tipos integrados que puede usar en la aplicación: el tema oscuro `Theme.Material` (el valor predeterminado), el tema `Theme.Material.Light` y el tema `Theme.Material.Light.DarkActionBar`:

[![Capturas de pantallas de los temas Oscuro, Claro y DarkActionBar](lollipop-images/three-material-themes-sml.png)](lollipop-images/three-material-themes.png#lightbox)

Para más información sobre el uso de las características del tema de materiales en las aplicaciones Xamarin.Android, consulte [Tema del materiales](~/android/user-interface/material-theme.md).

## <a name="animations"></a>Animaciones

Android 5.0 proporciona animaciones de respuestas táctiles, animaciones de transición de actividad y animaciones de transición de estado de las vistas que permiten un uso intuitivo de las interfaces de aplicaciones. Además, las aplicaciones de Android 5.0 pueden usar animaciones de *efecto revelar* para ocultar o mostrar vistas. Se puede usar la configuración de *movimiento curvo* para configurar cómo de rápido o lento se representan las animaciones.

### <a name="touch-feedback-animations"></a>Animaciones de respuestas táctiles

Las animaciones de respuestas táctiles proporcionan a los usuarios comentarios visuales cuando se toca una vista. Por ejemplo, los botones ahora muestran un efecto de ondas cuando se tocan: esta es la animación de comentarios táctiles predeterminada en Android 5.0. La animación de ondas se implementa con la nueva clase `RippleDrawable`. El efecto de ondas se puede configurar para que termine en los límites de la vista o se extienda más allá. Por ejemplo, la siguiente secuencia de capturas de pantalla ilustra el efecto de ondas en un botón durante la animación táctil:

![Capturas de pantalla fotograma a fotograma de la animación de ondas en un botón](lollipop-images/touch-animation.png)

El contacto táctil inicial con el botón se produce en la primera imagen de la izquierda, mientras que el resto de la secuencia (de izquierda a derecha) ilustra cómo se expande el efecto de ondas al borde del botón. Cuando finaliza la animación de ondas, la vista vuelve a su apariencia original. La animación de ondas predeterminada tiene lugar en una fracción de segundo, pero la longitud de la animación se puede personalizar con más o menos tiempo.

Para más información sobre animaciones de respuestas táctiles en Android 5.0, consulte [Personalizar la respuesta táctil](https://developer.android.com/training/material/animations.html#Touch).

### <a name="activity-transition-animations"></a>Animaciones de transición de actividad

Las animaciones de transición de actividad proporcionan a los usuarios una sensación de continuidad visual cuando una actividad realiza una transición a otra. Las aplicaciones pueden especificar tres tipos de animaciones de transición:

- **Enter transition** (Entrar en la transición): para cuando una actividad entra en la escena.

- **Exit transition** (Salir de la transición): para cuando una actividad sale de la escena.

- **Shared element transition** (Transición de elementos compartidos): para cuando una vista que es común a dos actividades cambia cuando la primera actividad realiza la transición a la siguiente.

Por ejemplo, la siguiente secuencia de capturas de pantallas ilustra una transición de elementos compartidos:

[![Capturas de pantalla fotograma a fotograma de una animación de transición de elementos compartidos](lollipop-images/activity-transition-sml.png)](lollipop-images/activity-transition.png#lightbox)

Un elemento compartido (una fotografía de una oruga) es una de las diversas vistas de la primera actividad; se amplía para convertirse en la única vista de la segunda actividad a medida que la primera actividad realiza la transición a la segunda.

#### <a name="enter-transition-animation-types"></a>Tipos de animación de transición de entrada

En el caso de las transiciones de entrada, Android 5.0 proporciona tres tipos de animaciones:

- **Explode animation** (Expandir animación): amplía una vista desde el centro de la escena.

- **Slide animation** (Deslizar animación): mueve una vista desde uno de los bordes de la pantalla.

- **Fade animation** (Atenuar animación): atenúa una vista en la escena.

#### <a name="exit-transition-animation-types"></a>Tipos de animación de transición de salida

En el caso de las transiciones de salida, Android 5.0 proporciona tres tipos de animaciones:

- **Explode animation** (Expandir animación): contrae una vista al centro de la escena.

- **Slide animation** (Deslizar animación): mueve una vista fuera de uno de los bordes de una escena.

- **Fade animation** (Atenuar animación): atenúa una vista fuera de la escena.

#### <a name="shared-element-transition-animation-types"></a>Tipos de animación de transición de elementos compartidos

Las transiciones de elementos compartidas admiten varios tipos de animaciones, como:

- Cambiar el diseño o los límites de recorte de una vista.

- Cambiar la escala y la rotación de una vista.

- Cambiar el tamaño y el tipo de escala de una vista.

Para más información sobre las animaciones de transición de actividad en Android 5.0, consulte [Personaliza las transiciones de actividades](https://developer.android.com/training/material/animations.html#Transitions).

### <a name="view-state-transition-animations"></a>Animaciones de transición de estado de las vistas

Android 5.0 permite que las animaciones se ejecuten cuando cambia el estado de una vista. Puede animar las transiciones de estado de las vistas mediante una de las técnicas siguientes:

- Cree elementos dibujables que animen los cambios de estado asociados a una vista determinada. La nueva clase `AnimatedStateListDrawable` permite crear elementos dibujables que muestran animaciones entre cambios del estado de las vistas.

- Defina la funcionalidad de animación que se ejecuta cuando cambia el estado de una vista. La nueva clase `StateListAnimator` permite definir un animador que se ejecuta cuando cambia el estado de una vista.

Para más información sobre las animaciones de transición de estado de las vistas en Android 5.0, consulte [Anima los cambios de estado de las vistas](https://developer.android.com/training/material/animations.html#ViewState).

### <a name="reveal-effect"></a>Efecto revelar

El *efecto revelar* es un círculo de recorte que cambia el radio para mostrar u ocultar una vista. Para controlar este efecto, puede establecer el radio inicial y final del círculo de recorte. La siguiente secuencia de capturas de pantalla ilustra una animación de efecto revelar desde el centro de la pantalla:

[![Capturas de pantalla fotograma a fotograma de la animación de revelar](lollipop-images/reveal-center-sml.png)](lollipop-images/reveal-center.png#lightbox).

La secuencia siguiente ilustra una animación de efecto revelar que tiene lugar desde la esquina inferior izquierda de la pantalla:

[![Capturas de pantalla fotograma a fotograma de la animación de recorte](lollipop-images/reveal-left-sml.png)](lollipop-images/reveal-left.png#lightbox)

Se pueden invertir las animaciones de revelar; es decir, el círculo de recorte puede reducirse para ocultar la vista en lugar de ampliarlo para revelarla.

Para más información sobre el efecto revelar de Android 5.0, consulte [Usa el efecto revelar](https://developer.android.com/training/material/animations.html#Reveal).

### <a name="curved-motion"></a>Movimiento curvo

Además de estas características de animación, Android 5.0 también proporciona nuevas API que le permiten especificar las curvas de tiempo y movimiento de las animaciones. Android 5.0 emplea estas curvas para interpolar el movimiento temporal y espacial durante las animaciones. Se definen tres curvas en Android 5.0:

- **Fast\_out\_linear\_in**: acelera rápidamente y continúa acelerando hasta el final de la animación.

- **Fast\_out\_slow\_in**: acelera rápidamente y desacelera lentamente hacia el final de la animación.

- **Linear\_out\_slow\_in**: comienza con una velocidad máxima y desacelera lentamente hasta el final de la animación.

Puede usar la nueva clase `PathInterpolator` para especificar cómo tiene lugar la interpolación del movimiento. `PathInterpolator` es un interpolador que atraviesa las rutas de animación según los puntos de control y las curvas de movimiento especificados. Para más información sobre cómo especificar la configuración del movimiento curvo en Android 5.0, consulte [Usa movimientos curvos](https://developer.android.com/training/material/animations.html#CurvedMotion).

## <a name="view-shadows--elevation"></a>Sombras y elevación de vistas

En Android 5.0, puede especificar el valor de *elevation* de una vista mediante el establecimiento de una nueva propiedad `Z`. Un valor `Z` mayor hace que la vista proyecte una sombra más grande sobre el fondo, lo que hace que parezca que flota más alto sobre este. Puede establecer la elevación inicial de una vista mediante la configuración de su atributo `elevation` en el diseño.

En el ejemplo siguiente se ilustra la proyección de sombras mediante un control `TextView` vacío cuando su atributo de elevación se establece en 2 dp, 4 dp y 6 dp, respectivamente:

[![Capturas de pantallas de sombras de vistas cada vez más grandes](lollipop-images/view-shadows-sml.png)](lollipop-images/view-shadows.png#lightbox)

La configuración de sombras de vistas puede ser estática (como se ha mostrado anteriormente) o se puede usar en animaciones para que parezca que una vista se eleva temporalmente por encima del fondo. Puede usar la clase `ViewPropertyAnimator` para animar la elevación de una vista. La elevación de una vista es la suma de su valor de `elevation` de diseño más una propiedad `translationZ` que se puede establecer mediante una llamada al método `ViewPropertyAnimator`.

Para más información sobre las sombras de vistas en Android 5.0, consulte [Crea vistas de recorte y sombras](https://developer.android.com/training/material/shadows-clipping.html).

## <a name="color-features"></a>Características de color

Android 5.0 proporciona dos nuevas características para administrar el color de las aplicaciones:

- *Drawable tinting* (Matices dibujables) le permite modificar los colores de los recursos de imágenes cambiando un atributo de diseño.

- *Prominent color extraction* (Extracción del color prominente) permite personalizar dinámicamente el tema de color de la aplicación para que se coordine con la paleta de colores de una imagen mostrada.

### <a name="drawable-tinting"></a>Matices dibujables

Los diseños de Android 5.0 reconocen un nuevo atributo `tint` que puede usar para establecer el color de los elementos dibujables sin tener que crear varias versiones de estos recursos para mostrar distintos colores. Para usar esta característica, debe definir un mapa de bits como una máscara alfa y usar el atributo `tint` para definir el color del recurso. Esto permite crear activos una vez y colorearlos en el diseño para que coincidan con el tema.

En el ejemplo siguiente, se usa un único recurso de imagen (un logotipo blanco con un fondo transparente) para crear variaciones de matiz:

![Logotipo de Xamarin blanco con fondo transparente](lollipop-images/xamarin-logo-white.png)

Este logotipo se muestra sobre un fondo circular azul, tal como se ilustra en los ejemplos siguientes. La imagen de la izquierda es la forma en que aparece el logotipo sin un valor de `tint`. En la imagen central, el atributo `tint` del logotipo se establece en un gris oscuro. En la imagen de la derecha, `tint` se establece en un gris claro:

![Ejemplos del logotipo anterior con una configuración de matiz diferente](lollipop-images/drawable-tinting.png)

Para más información sobre los matices dibujables en Android 5.0, consulte [Matices dibujables](https://developer.android.com/training/material/drawables.html#DrawableTint).

### <a name="prominent-color-extraction"></a>Extracción del color prominente

La nueva clase `Palette` de Android 5.0 le permite extraer colores de una imagen para que pueda aplicarlos dinámicamente a una paleta de colores personalizada. La clase `Palette` extrae seis colores de una imagen y los etiqueta según sus niveles relativos de saturación de color y luminosidad:

- Vibrante

- Vibrante oscuro

- Vibrante claro

- Tenue

- Tenue oscuro

- Tenue claro

Por ejemplo, en las siguientes capturas de pantalla, una aplicación de visualización de fotografías extrae los colores prominentes de la imagen en pantalla y los usa para adaptar la combinación de colores de la aplicación de forma que coincida con la imagen:

[![Capturas de pantallas de extracciones de colores de tema verde, rosa y azul](lollipop-images/prominent-color-extraction-sml.png)](lollipop-images/prominent-color-extraction.png#lightbox)

En las capturas de pantallas anteriores, la barra de acción se establece en el color "vibrante claro" extraído y el fondo se establece en el color "vibrante oscuro" extraído. En cada ejemplo anterior, se incluye una fila de pequeños cuadrados de color para ilustrar los colores de la paleta que se extrajeron de la imagen.

Para más información sobre la extracción de colores en Android 5.0, consulte [Extracción de colores prominentes de una imagen](https://developer.android.com/training/material/drawables.html#ColorExtract).

## <a name="new-ui-widgets"></a>Nuevos widgets de interfaz de usuario

Android 5.0 introduce dos nuevos widgets de interfaz de usuario:

- `RecyclerView` : un grupo de vistas que muestra una lista de elementos desplazables.

- `CardView`: un diseño básico con esquinas redondeadas.

Ambos widgets incluyen compatibilidad integrada con las características del tema de materiales; por ejemplo, `RecyclerView` usa animaciones para agregar y quitar vistas, y `CardView` usa sombras de vistas para que cada tarjeta parezca que flote encima del fondo. En las siguientes capturas de pantallas se muestran ejemplos de estos nuevos widgets:

[![Capturas de pantallas de aplicaciones compiladas con RecyclerView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

La captura de pantalla de la izquierda es un ejemplo de `RecyclerView` como se usa en una aplicación de correo electrónico, y la captura de pantalla de la derecha es un ejemplo de `CardView` como se usa en una aplicación de reservas de viajes.

### <a name="recyclerview"></a>RecyclerView

`RecyclerView` es similar a `ListView,` pero es más adecuado para grandes conjuntos de vistas o listas con elementos que cambian dinámicamente. Al igual que con `ListView,`, se especifica un adaptador para tener acceso al conjunto de datos subyacente. Sin embargo, a diferencia de `ListView,`, se usa un *administrador de diseños* para colocar elementos en `RecyclerView`. El administrador de diseños también se encarga del reciclaje de las vistas: administra la reutilización de las vistas de elementos que ya no son visibles para el usuario.

Cuando se usa un widget `RecyclerView`, se debe especificar `LayoutManager` y un adaptador. Como se muestra en esta ilustración, `LayoutManager` es el intermediario entre el adaptador y `RecyclerView`:

![Diagrama de RecyclerView con LayoutManager, Adapter y Dataset como complementos](lollipop-images/recyclerview-diagram.png)

Las siguientes capturas de pantallas ilustran un widget `RecyclerView` que contiene 100 elementos (cada elemento consta de `ImageView` y `TextView`):

[![Capturas de pantallas de una aplicación RecyclerView que se desplaza a través de imágenes](lollipop-images/recyclerview-scroll-sml.png)](lollipop-images/recyclerview-scroll.png#lightbox)

`RecyclerView` controla este conjunto de datos de gran tamaño con facilidad: solo tarda unos segundos en desplazarse desde el principio hasta el final de la lista en esta aplicación de ejemplo. `RecyclerView` también admite animaciones; de hecho, las animaciones para agregar y quitar elementos están habilitadas de forma predeterminada. Cuando se agrega un elemento a `RecyclerView`, se atenúa como se muestra en esta secuencia de capturas de pantallas:

[![Captura de pantalla fotograma a fotograma de un elemento de fotografía que se atenúa](lollipop-images/recyclerview-animation-sml.png)](lollipop-images/recyclerview-animation.png#lightbox)

Para más información sobre `RecyclerView`, consulte [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).

### <a name="cardview"></a>CardView

`CardView` es una vista simple que simula una tarjeta flotante con esquinas redondeadas. Dado que `CardView` tiene sombras de vistas integradas, proporciona una manera sencilla de agregar profundidad visual a la aplicación. Las capturas de pantallas siguientes muestran tres ejemplos de `CardView` orientados a texto:

[![Capturas de pantallas de ejemplo de aplicaciones que usan RecyclerView con elementos basados en CardView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

Cada una de las tarjetas del ejemplo anterior contiene un elemento `TextView`; el color de fondo se establece mediante el atributo `cardBackgroundColor`.

Para más información sobre `CardView`, consulte [CardView](~/android/user-interface/controls/card-view.md).

## <a name="enhanced-notifications"></a>Notificaciones mejoradas

El sistema de notificación de Android 5.0 se ha actualizado considerablemente con un nuevo formato visual y nuevas características. Las notificaciones tienen una nueva apariencia en Android 5.0. Por ejemplo, ahora usan texto oscuro sobre un fondo claro:

![Ejemplo de una notificación de Android 5.0 no expandida](lollipop-images/expanded-notification-contracted.png)

Cuando aparece un icono grande en una notificación (como se muestra en el ejemplo anterior), Android 5.0 presenta el icono pequeño como un distintivo sobre el icono grande.

En Android 5.0, las notificaciones también pueden aparecer en la pantalla de bloqueo del dispositivo.
Por ejemplo, esta es una captura de pantalla de ejemplo de un bloqueo de pantalla con una sola notificación:

[![Captura de pantalla de la notificación que aparece en la pantalla de bloqueo](lollipop-images/lockscreen-notification-sml.png)](lollipop-images/lockscreen-notification.png#lightbox)

Los usuarios pueden pulsar dos veces en una notificación en la pantalla de bloqueo para desbloquear el dispositivo y saltar a la aplicación que originó esa notificación, o bien deslizar para descartarla. Las notificaciones tienen una nueva configuración de *visibilidad* que determina la cantidad de contenido que se puede mostrar en la pantalla de bloqueo. Los usuarios pueden elegir si desean permitir que se muestre contenido confidencial en las notificaciones de la pantalla de bloqueo.

Android 5.0 introduce un nuevo formato de presentación de notificaciones de alta prioridad denominado *Avisos*. Las notificaciones de avisos se desplazan hacia abajo desde la parte superior de la pantalla durante unos segundos y, luego, se repliegan al sombreado de las notificaciones en la parte superior de la pantalla. Las notificaciones de avisos permiten que la interfaz de usuario del sistema coloque información importante delante del usuario sin interrumpir la actividad que se está ejecutando actualmente.
En el ejemplo siguiente se ilustra una notificación de avisos sencilla que aparece en la parte superior de una aplicación:

[![Ejemplo de una notificación de avisos](lollipop-images/heads-up-notification-sml.png)](lollipop-images/heads-up-notification.png#lightbox)

Las notificaciones de avisos se suelen usar para los siguientes eventos:

- Un nuevo mensaje de texto

- Una llamada telefónica entrante

- Indicación de batería baja

- Una alarma

Android 5.0 solo muestra una notificación en formato de avisos cuando tiene una configuración de prioridad alta o máxima.

En Android 5.0, puede proporcionar metadatos de notificación para ayudar a Android a ordenar y mostrar notificaciones de forma más inteligente. Android 5.0 organiza las notificaciones según la prioridad, la visibilidad y la categoría.
Las categorías de notificación se usan para filtrar las notificaciones que se pueden presentar cuando el dispositivo está en modo *No molestar*.

Para información detallada sobre la creación y el inicio de notificaciones con las características de Android 5.0 más recientes, consulte [Notificaciones locales](~/android/app-fundamentals/notifications/local-notifications.md).

## <a name="new-apis"></a>Nuevas API.

Además de las nuevas características de apariencia que se han descrito anteriormente, Android 5.0 agrega nuevas API que amplían las capacidades de la funcionalidad multimedia, de almacenamiento y de conectividad por cable o inalámbrica existente. Además, Android 5.0 incluye nuevas API que proporcionan compatibilidad con una nueva característica de programador de trabajos.

### <a name="camera"></a>Cámara

Android 5.0 proporciona varias nuevas API que mejoran las capacidades de la cámara. El nuevo espacio de nombres `Android.Hardware.Camera2` incluye funcionalidad para acceder a dispositivos de cámara individuales conectados a un dispositivo Android. Además, `Android.Hardware.Camera2` modela cada dispositivo de cámara como una canalización: acepta una solicitud de captura, captura la imagen y, luego, genera el resultado. Este enfoque permite a las aplicaciones poner en cola varias solicitudes de captura en un dispositivo de cámara.

Las siguientes API hacen posibles estas nuevas características:

- `CameraManager.GetCameraIdList`: ayuda a acceder mediante programación a los dispositivos de cámara; usará `CameraManager.OpenCamera` para conectarse a un dispositivo de cámara específico.

- `CameraCaptureSession`: captura o transmite imágenes desde el dispositivo de cámara. Se implementará una interfaz `CameraCaptureSession.CaptureListener` para controlar los nuevos eventos de captura de imágenes.

- `CaptureRequest`: define los parámetros de captura.

- `CaptureResult`: proporciona los resultados de una operación de captura de imágenes.

Para más información sobre las nuevas API de cámara en Android 5.0, consulte [Medios](https://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="audio-playback"></a>Reproducción de audio

Android 5.0 actualiza la clase `AudioTrack` para mejorar la reproducción de audio:

- `ENCODING_PCM_FLOAT`: configura `AudioTrack` para aceptar los datos de audio en formato de punto flotante a fin de mejorar el intervalo dinámico y aumentar el espacio y la calidad (gracias a la mayor precisión). Además, el formato de punto flotante ayuda a evitar el recorte de audio.

- `ByteBuffer`: ahora puede proporcionar datos de audio para `AudioTrack` como una matriz de bytes.

- `WRITE_NON_BLOCKING`: esta opción simplifica el almacenamiento en búfer y el multithreading para algunas aplicaciones.

Para más información sobre las mejoras de `AudioTrack` en Android 5.0, consulte [Medios](https://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="media-playback-control"></a>Control de reproducción multimedia

Android 5.0 presenta la nueva clase `Android.Media.MediaController`, que reemplaza a `RemoteControlClient`. `Android.Media.MediaController` proporciona API de control de transporte simplificadas y ofrece control seguro para subprocesos de la reproducción fuera del contexto de la interfaz de usuario. Las siguientes API nuevas administran el control de transporte:

- `Android.Media.Session.MediaSession`: una sesión de control multimedia que administra varios controladores. Llamará a `MediaSession.GetSessionToken` para solicitar un token que usa la aplicación para interactuar con la sesión.

- `MediaController.TransportControls`: administra los comandos de transporte, como **Reproducir**, **Detener** y **Omitir**.

Además, puede usar la nueva clase `Android.App.Notification.MediaStyle` para asociar una sesión multimedia a contenido de notificaciones enriquecido (como la extracción y visualización de carátulas de álbum).

Para más información sobre las nuevas características de control de reproducción multimedia en Android 5.0, consulte [Medios](https://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="storage"></a>Almacenamiento

Android 5.0 actualiza el marco de acceso de almacenamiento para que las aplicaciones funcionen con directorios y documentos más fácilmente:

- Para seleccionar un subárbol de directorio, puede compilar y enviar una intención `Android.Intent.Action.OPEN_DOCUMENT_TREE`. Esta intención hace que el sistema muestre todas las instancias de proveedor que admiten la selección de subárbol; luego, el usuario examina y selecciona un directorio.

- Para crear y administrar nuevos documentos o directorios en cualquier parte de un subárbol, use los nuevos métodos `CreateDocument`, `RenameDocument` y `DeleteDocument` de `DocumentsContract`.

- Para obtener las rutas de acceso a los directorios de medios de todos los dispositivos de almacenamiento compartido, llame al nuevo método `Android.Content.Context.GetExternalMediaDirs`.

Para más información sobre las nuevas API de almacenamiento de Android 5.0, consulte [Almacenamiento](https://developer.android.com/preview/api-overview.html#Storage).

### <a name="wireless--connectivity"></a>Conectividad por cable e inalámbrica

Android 5.0 agrega las siguientes mejoras de API para la conectividad por cable e inalámbrica:

- Nuevas API de *varias redes* que permiten que las aplicaciones busquen y seleccionen redes con capacidades específicas antes de establecer una conexión.

- Funcionalidad de difusión por Bluetooth que permite que un dispositivo Android 5.0 actúe como periférico Bluetooth de bajo consumo de energía.

- Mejoras de NFC que facilitan el uso de la funcionalidad de transmisión de datos en proximidad para compartir datos con otros dispositivos.

Para más información sobre las nuevas API de conectividad por cable e inalámbrica en Android 5.0, consulte [Conectividad por cable e inalámbrica](https://developer.android.com/preview/api-overview.html#Wireless).

### <a name="job-scheduling"></a>Programación de trabajos

Android 5.0 introduce una nueva API `JobScheduler` que puede ayudar a los usuarios a minimizar la descarga de la batería al poderse programar determinadas tareas para que solo se ejecuten cuando el dispositivo está enchufado y en carga. Esta característica de programador de trabajos también puede usarse para programar una tarea para que se ejecute cuando las condiciones sean más adecuadas para ella, por ejemplo, descargar un archivo grande cuando el dispositivo esté conectado a través de una red Wi-Fi en lugar de una red de uso medido.

Para más información sobre las nuevas API de programación de trabajos en Android 5.0, consulte [Programación de trabajos](https://developer.android.com/preview/api-overview.html#JobScheduler).

## <a name="summary"></a>Resumen

En este artículo se proporciona información general sobre las nuevas características importantes de Android 5.0 para los desarrolladores de aplicaciones de Xamarin.Android:

- Tema de materiales

- Animaciones

- Sombras y elevación de vistas

- Características de color, como matices dibujables y extracción del color prominente

- Los nuevos widgets `RecyclerView` y `CardView`

- Mejoras de las notificaciones

- Nuevas API para la cámara, reproducción de audio, control de medios, almacenamiento, conectividad por cable e inalámbrica y programación de trabajos

Si no está familiarizado con el desarrollo de Xamarin Android, lea [Configuración e instalación](~/android/get-started/installation/index.md) para ayudarle a empezar a trabajar con Xamarin.Android.
[Hello, Android](~/android/get-started/hello-android/index.md) es una excelente introducción para aprender a crear proyectos de Android.

## <a name="related-links"></a>Vínculos relacionados

- [Versión preliminar de Android L para desarrolladores](https://developer.android.com/preview/index.html)
- [Obtener Android SDK](https://developer.android.com/sdk/index.html#Other)
- [Material Design](https://developer.android.com/preview/material/index.html)
