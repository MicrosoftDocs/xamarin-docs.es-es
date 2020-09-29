---
title: Introducción a Android Wear
description: Con la introducción del desgaste de Android de Google, ya no está restringido a los teléfonos y las tabletas cuando se trata de desarrollar excelentes aplicaciones Android. La compatibilidad de Xamarin. Android con el desgaste de Android permite ejecutar código de C# en su muñeca. Esta introducción proporciona información general básica sobre el desgaste de Android, describe sus características clave y ofrece información general sobre las características disponibles en el desgaste de Android 2,0. En él se enumeran algunos de los dispositivos de desgaste de Android más populares y se proporcionan vínculos a la documentación esencial sobre el desgaste de Android de Google para su posterior lectura.
ms.prod: xamarin
ms.assetid: EAEF99F0-8FBE-47E4-8644-E7244CFAF464
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: a0e352850dced5c78c68859912de8f41c1fe87f9
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458136"
---
# <a name="introduction-to-android-wear"></a>Introducción a Android Wear

_Con la introducción del desgaste de Android de Google, ya no está restringido a los teléfonos y las tabletas cuando se trata de desarrollar excelentes aplicaciones Android. La compatibilidad de Xamarin. Android con el desgaste de Android permite ejecutar código de C# en su muñeca. Esta introducción proporciona información general básica sobre el desgaste de Android, describe sus características clave y ofrece información general sobre las características disponibles en el desgaste de Android 2,0. En él se enumeran algunos de los dispositivos de desgaste de Android más populares y se proporcionan vínculos a la documentación esencial sobre el desgaste de Android de Google para su posterior lectura._

## <a name="overview"></a>Información general

El desgaste de Android se ejecuta en una variedad de dispositivos, entre los que se incluyen la primera generación de Motorola 360, la inspección G de LG y la marcha de Samsung en vivo. También se ha lanzado una segunda generación, que incluye el SmartWatch 3 de Sony, con capacidades adicionales, como la reproducción de música sin conexión y GPS integrada. Para el desgaste de Android 2,0, Google se ha agrupado con LG para dos nuevos relojes: el deporte de inspección de LG y el estilo de inspección de LG.

![Dispositivos de desgaste de Android 2,0](intro-to-wear-images/hero-image.png "Ejemplo de dispositivos de desgaste de Android 2,0")

Xamarin. Android 5,0 y versiones posteriores admiten el desgaste de Android a través de la compatibilidad con Android 4.4 W (API 20) y un paquete de NuGet que agrega controles de interfaz de usuario específicos para el desgaste adicional. Xamarin. Android 5,0 y versiones posteriores también incluyen funcionalidad para empaquetar las aplicaciones de desgaste. Los paquetes NuGet también están disponibles para el desgaste de Android 2,0 como se describe más adelante en esta guía.

## <a name="android-wear-basics"></a>Conceptos básicos sobre el desgaste de Android

El desgaste de Android tiene un paradigma de interfaz de usuario que difiere del de las aplicaciones de bolsillo de Android. La primera ola de aplicaciones de desgaste se diseñó para extender una aplicación de mano complementaria de algún modo, pero a partir del desgaste de Android 2,0, las aplicaciones de desgaste se pueden usar de forma independiente. Cuando se implementa una aplicación de desgaste, se empaqueta con una aplicación de mano complementaria. Dado que la mayoría de las aplicaciones de desgaste dependen de una aplicación complementaria de mano, necesitan alguna manera de comunicarse con aplicaciones de mano. En las secciones siguientes se describen estos escenarios de uso y se describen las características esenciales del desgaste de Android. 

### <a name="usage-scenarios"></a>Escenarios de uso

La primera versión del desgaste de Android se centró principalmente en la extensión de aplicaciones de mano actuales con notificaciones mejoradas y la sincronización de datos entre la aplicación de mano y la aplicación portátil. Por lo tanto, estos escenarios son relativamente sencillos de implementar.

#### <a name="wearable-notifications"></a>Notificaciones de portátil

La manera más sencilla de admitir el desgaste de Android es aprovechar la naturaleza compartida de las notificaciones entre el dispositivo de mano y el dispositivo portátil. Mediante el uso de la API de notificación de support V4 y la `WearableExtender` clase (disponible en la [biblioteca de compatibilidad de Android de Xamarin](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)), puede aprovechar las características nativas de la plataforma, como las tarjetas de estilo de bandeja de entrada o la entrada de voz. El ejemplo [RecipeAssistant](/samples/xamarin/monodroid-samples/wear-recipeassistant) proporciona código de ejemplo que muestra cómo enviar una lista de notificaciones a un dispositivo de desgaste de Android. 

#### <a name="companion-applications"></a>Aplicaciones complementarias

Otra estrategia consiste en crear una aplicación completa que se ejecute de forma nativa en el dispositivo portátil y pares con una aplicación de mano complementaria. Un buen ejemplo de este enfoque es la aplicación de ejemplo de [cuestionario](/samples/xamarin/monodroid-samples/wear-quiz) , que muestra cómo crear una prueba que se ejecuta en un dispositivo de mano y realiza preguntas de prueba en el dispositivo portátil. 

### <a name="user-interface"></a>Interfaz de usuario

El patrón de navegación principal para el desgaste es una serie de tarjetas organizadas verticalmente. Cada una de estas tarjetas puede tener acciones asociadas que se superponen en la misma fila. La `GridViewPager` clase proporciona esta funcionalidad; se adhiere al mismo concepto de adaptador que `ListView` . Normalmente, se asocia `GridViewPager` con un `FragmentGridPagerAdaptor` (o `GridPagerAdaptor` ) que permite representar cada celda de fila y columna como `Fragment` : 

[![Navegación por desgaste](intro-to-wear-images/2d-picker-sml.png "Navegación por desgaste")](intro-to-wear-images/2d-picker.png#lightbox)

El desgaste también hace uso de botones de acción que se componen de un círculo de color grande con un pequeño texto de Descripción debajo (como se muestra arriba).  En el ejemplo [GridViewPager](/samples/xamarin/monodroid-samples/wear-gridviewpager) se muestra cómo usar `GridViewPager` y `GridPagerAdapter` en una aplicación de desgaste.

El desgaste de Android 2,0 agrega un cajón de navegación, un cajón de acciones y botones de acción insertados a la interfaz de usuario de desgaste. Para más información sobre los elementos de la interfaz de usuario de 2,0 de Android, consulte el tema sobre la [anatomía](https://www.google.com/design/spec-wear/system-overview/anatomy.html) de Android. 

### <a name="communications"></a>Comunicaciones

El desgaste de Android proporciona dos API de comunicación diferentes para facilitar la comunicación entre las aplicaciones de portátil y las aplicaciones de mano complementarias: 

**API** &ndash; de datos Esta API es similar a un almacén de datos sincronizado entre el dispositivo portátil y el dispositivo de mano. Android se encarga de propagar los cambios entre portátil y el dispositivo de mano cuando es óptimo hacerlo. Cuando el portátil está fuera del intervalo, pone en cola la sincronización para más adelante. El punto de entrada principal para esta API es `WearableClass.DataApi` . Para obtener más información acerca de esta API, vea el tema Android [Syncing Data items](https://developer.android.com/training/wearables/data-layer/data-items.html) . 

**API** &ndash; de mensajes Esta API permite usar una ruta de comunicación de nivel inferior: una carga pequeña se envía de forma unidireccional sin sincronización entre las aplicaciones de mano y portátil.
El punto de entrada principal para esta API es `WearableClass.MessageApi` .
Para obtener más información acerca de esta API, consulte el tema [envío y recepción de mensajes](https://developer.android.com/training/wearables/data-layer/messages.html) de Android.

Puede optar por registrar devoluciones de llamada para recibir esos mensajes a través de cada una de las interfaces del agente de escucha de API o, como alternativa, implementar un servicio en la aplicación que se derive de `WearableListenerService` .
Se crearán instancias de este servicio automáticamente con el desgaste de Android.
En el ejemplo [FindMyPhone](/samples/xamarin/monodroid-samples/wear-findmyphonesample) se muestra cómo implementar un `WearableListenerService` .

### <a name="deployment"></a>Implementación

Cada aplicación portátil se implementa con su propio archivo APK insertado en la aplicación principal APK. Este empaquetado se administra automáticamente en Xamarin. Android 5,0 y versiones posteriores, pero debe realizarse manualmente para las versiones de Xamarin. Android anteriores a la versión 5,0. 
[Trabajar con el empaquetado](~/android/wear/deploy-test/packaging.md) explica la implementación con más detalle. 

## <a name="going-further"></a>Ampliar conocimientos 

La mejor manera de familiarizarse con el desgaste de Android consiste en compilar y probar la primera aplicación. La lista siguiente proporciona un orden de lectura recomendado para ayudarle a ponerse al día rápidamente:

1. Instalación [& instalación](~/android/wear/get-started/installation.md) proporciona instrucciones detalladas para instalar y configurar el entorno de desarrollo para compilar aplicaciones de desgaste de Xamarin. Android. 

2. Una vez que haya instalado los paquetes necesarios y haya configurado un emulador o un dispositivo, consulte  [Hola, desgaste](~/android/wear/get-started/hello-wear.md) de las instrucciones paso a paso que explican cómo crear un pequeño proyecto de desgaste de Android que controla los clics de los botones y muestra un contador de clics en el dispositivo de desgaste. 

3. Las [pruebas de & de implementación](~/android/wear/deploy-test/index.md) proporcionan información más detallada sobre la configuración e implementación en emuladores y dispositivos, incluidas instrucciones sobre cómo implementar la aplicación en un dispositivo de desgaste a través de Bluetooth.

4. [Trabajar con tamaños de pantalla](~/android/wear/screen-sizes.md) explica cómo obtener una vista previa y optimizar la interfaz de usuario para los distintos tamaños de pantalla disponibles en los dispositivos de desgaste. 

5. [Trabajar con el empaquetado](~/android/wear/deploy-test/packaging.md) describe los pasos para empaquetar aplicaciones de desgaste de forma manual para su distribución en Google Play.

Una vez que haya creado su primera aplicación de desgaste, puede intentar crear una superficie de inspección personalizada para el desgaste de Android. 
[La creación de una esfera de inspección](~/android/wear/platform/creating-a-watchface.md) proporciona instrucciones paso a paso y código de ejemplo para desarrollar un servicio de caras de supervisión digital de bajada, seguido de más código que lo mejora en una superficie de inspección de estilo analógico con características adicionales. 

## <a name="android-wear-20"></a>Desgaste de Android 2,0

El desgaste de Android 2,0 presenta una variedad de nuevas características y funcionalidades, como *complicaciones*, diseños curvos, dibujados de navegación y acciones, y notificaciones ampliadas. Además, el desgaste de 2,0 permite crear aplicaciones independientes que funcionen de forma independiente de las aplicaciones de mano. La nueva funcionalidad de *gestos de muñecas* permite interacciones de una sola mano con la aplicación. En las secciones siguientes se resaltan estas características y se proporcionan vínculos para ayudarle a empezar a usarlas en la aplicación.

### <a name="install-wear-20-packages"></a>Instalación de desgaste de paquetes 2,0

Para compilar una aplicación de desgaste de 2,0 con Xamarin. Android, debe agregar el paquete **Xamarin. Android. desgaste v 2.0** al proyecto (haga clic en la **pestaña examinar**):

[![Xamarin. Android. desgaste v 2.0](intro-to-wear-images/wear-nuget-2.0-sml.png "Instalar el NuGet de Xamarin. Android. desgaste v 2.0")](intro-to-wear-images/wear-nuget-2.0.png#lightbox)

Este paquete de NuGet contiene enlaces para las bibliotecas de compatibilidad de Android portátil y desgaste.

Además de **Xamarin. Android. desgaste**, se recomienda instalar el NuGet **Xamarin. GooglePlayServices. portátil** : 

[![Xamarin. GooglePlayServices. portátil](intro-to-wear-images/gpsw-nuget-sml.png "Instalar el NuGet Xamarin. GooglePlayServices. portátil")](intro-to-wear-images/gpsw-nuget.png#lightbox)

### <a name="key-features-of-wear-20"></a>Características clave del desgaste 2,0

El desgaste de Android 2,0 es la actualización más importante para el desgaste de Android desde su lanzamiento inicial en 2014. En las secciones siguientes se resaltan las características clave del desgaste de Android 2,0 y se proporcionan vínculos para ayudarle a empezar a usar estas nuevas características en la aplicación. 

#### <a name="complications"></a>Complicaciones

Las *complicaciones* son widgets de reloj pequeños que se pueden ver de un vistazo sin tener que deslizarse por el reloj. Las complicaciones son similares a los widgets del panel de estilo de escritorio; muestran información como el tiempo, la duración de la batería, los eventos de calendario y las estadísticas de la aplicación de idoneidad: 

![Ejemplo de complicaciones](intro-to-wear-images/complications.png "Ejemplo de complicaciones")

Para obtener más información sobre las complicaciones, vea el tema sobre las complicaciones de las caras de la [observación](https://developer.android.com/wear/preview/features/complications.html) de Android. 

#### <a name="navigation-and-action-drawers"></a>Navegación y dibujados de acciones 

Dos nuevos alimentadores se incluyen en el desgaste de 2,0. El *cajón de navegación*, que aparece en la parte superior de la pantalla, permite a los usuarios navegar entre las vistas de la aplicación (como se muestra a continuación). El *cajón de acciones*, que aparece en la parte inferior de la pantalla (como se muestra a la derecha), permite a los usuarios elegir en una lista de acciones. 

![Navegación y dibujados de acciones](intro-to-wear-images/drawers.png "Navegación y dibujados de acciones")

Para obtener más información sobre estos dos nuevos interactivos interactivos, consulte el tema [navegación y acciones de desgaste](https://developer.android.com/wear/preview/features/ui-nav-actions.html) de Android. 

#### <a name="curved-layouts"></a>Diseños curvos 

Desgaste 2,0 presenta nuevas características para mostrar diseños curvos en dispositivos de desgaste de ida y vuelta. En concreto, la nueva `WearableRecyclerView` clase está optimizada para mostrar una lista de elementos verticales en pantallas redondas: 

![Ejemplo de diseño curvo](intro-to-wear-images/curved-layout.png "Ejemplo de diseño curvo")

`WearableRecyclerView` extiende la `RecyclerView` clase para admitir diseños curvos y gestos de desplazamiento circular. Para obtener más información, consulte la documentación de la API de Android [WearableRecyclerView](https://developer.android.com/reference/android/support/wearable/view/WearableRecyclerView.html) . 

#### <a name="standalone-apps"></a>Aplicaciones independientes 

Las aplicaciones de desgaste de Android 2,0 pueden funcionar independientemente de las aplicaciones de mano. Esto significa que, por ejemplo, una inspección inteligente puede seguir ofreciendo funcionalidad completa aunque el dispositivo de mano complementario esté apagado o lejos del dispositivo portátil. Para obtener más información sobre esta característica, consulte el tema [aplicaciones independientes](https://developer.android.com/wear/preview/features/standalone-apps.html) de Android.

#### <a name="wrist-gestures"></a>Gestos de muñecas 

Los gestos de muñecas permiten a los usuarios interactuar con la aplicación sin usar la pantalla táctil &ndash; los usuarios pueden responder a la aplicación con una sola mano. Se admiten dos gestos de muñecas: 

- Desplazarse hacia afuera
- Gesto de muñeca en

Para obtener más información, consulte el tema sobre [gestos de muñecas](https://developer.android.com/wear/preview/features/gestures.html) de Android. 

Hay muchas más características de desgaste 2,0, como acciones insertadas, respuesta inteligente, entrada remota, notificaciones expandidas y un nuevo modo de puente para las notificaciones. Para obtener más información sobre las nuevas características de desgaste 2,0, consulte la [Introducción a la API](https://developer.android.com/wear/preview/api-overview.html)de Android. 

## <a name="devices"></a>Dispositivos

Estos son algunos ejemplos de los dispositivos que pueden ejecutar el desgaste de Android:

- [Motorola 360](https://moto360.motorola.com/)
- [Reloj de LG G](https://www.lg.com/us/smart-watches/lg-W100-g-watch)
- [Inspección R de LG G](https://www.lg.com/us/smartwatch/g-watch-r)
- [En directo de Samsung Gear](https://www.samsung.com/global/microsite/gear/gearlive_design.html)
- [SONY SmartWatch 3](https://www.sonymobile.com/global-en/products/smartwear/smartwatch-3-swr50/)
- [ASUS ZenWatch](https://www.asus.com/us/Phones/ASUS_ZenWatch_WI500Q/)

## <a name="further-reading"></a>Lecturas adicionales

Consulte la documentación sobre el desgaste de Android de Google:

- [Acerca del desgaste de Android](https://www.android.com/wear/)
- [Diseño de aplicaciones de desgaste de Android](https://developer.android.com/design/wear/index.html)
- [Biblioteca Android. support. portátil](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [Desgaste de Android 2,0](https://developer.android.com/wear/preview/index.html)

## <a name="summary"></a>Resumen

Esta introducción proporciona información general sobre el desgaste de Android. Se describen las características básicas del desgaste de Android y se incluye información general sobre las características introducidas en el desgaste de Android 2,0. Proporciona vínculos a la lectura básica para ayudar a los desarrolladores a empezar a trabajar con el desarrollo de uso de Xamarin. Android y a mostrar ejemplos de algunos de los dispositivos de desgaste de Android actualmente en el mercado.

## <a name="related-links"></a>Vínculos relacionados

- [Instalación y configuración](~/android/wear/get-started/installation.md)
- [Introducción](~/android/wear/get-started/index.md)