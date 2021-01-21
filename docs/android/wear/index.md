---
title: Android Wear
description: Compilar aplicaciones para dispositivos portátil de Android.
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/16/2018
ms.openlocfilehash: 9f9e886d6e72faee6ba579fc4c9ab92dfbfb0cf4
ms.sourcegitcommit: e27e29c14b783263e063baaa65d4eecb8dd31f57
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98628935"
---
# <a name="android-wear"></a>Android Wear

El desgaste de Android es una versión de Android que está diseñada para dispositivos portátil, como las inspecciones inteligentes. En esta sección se incluyen instrucciones sobre cómo instalar y configurar las herramientas necesarias para el desarrollo de desgaste, un tutorial paso a paso para crear su primer dispositivo de desgaste y una lista de ejemplos a los que puede hacer referencia para crear sus propias aplicaciones de desgaste.

## <a name="getting-started"></a>[Introducción](~/android/wear/get-started/index.md)

Presenta el desgaste de Android, describe cómo instalar y configurar el equipo para el desarrollo de desgaste y proporciona pasos para ayudarle a crear y ejecutar su primera aplicación de desgaste de Android en un emulador o dispositivo de desgaste.

## <a name="user-interface"></a>[Interfaz de usuario](~/android/wear/user-interface/index.md)

Explica los controles específicos del desgaste de Android y proporciona vínculos a ejemplos que muestran cómo usar estos controles.

## <a name="platform-features"></a>[Características de la plataforma](~/android/wear/platform/index.md)

Los documentos de esta sección cubren características específicas del desgaste de Android. Aquí encontrará un tema en el que se describe cómo crear un WatchFace.

## <a name="screen-sizes"></a>[Tamaños de pantalla](~/android/wear/screen-sizes.md)

Obtenga una vista previa y optimice la interfaz de usuario para los tamaños de pantalla disponibles.

## <a name="deployment--testing"></a>[Implementación y pruebas](~/android/wear/deploy-test/index.md)

Explica cómo implementar la aplicación de desgaste de Android en un dispositivo de desgaste de Android o en un emulador de Android configurado para el desgaste. También incluye sugerencias de depuración e información sobre cómo configurar una conexión Bluetooth entre el equipo de desarrollo y un dispositivo Android.

## <a name="wear-apis"></a>[Desgaste de las API](https://developer.android.com/reference/android/support/wearable)

El sitio para desarrolladores de Android proporciona información detallada sobre las API de desgaste de claves, como la [actividad de portátil](https://developer.android.com/reference/android/support/wearable/activity/package-summary.html), los [intentos](https://developer.android.com/reference/com/google/android/wearable/intent/package-summary.html), la [autenticación](https://developer.android.com/reference/android/support/wearable/authentication/package-summary.html), las [complicaciones](https://developer.android.com/reference/android/support/wearable/complications/package-summary.html), la [representación de complicaciones](https://developer.android.com/reference/android/support/wearable/complications/rendering/package-summary.html), las [notificaciones](https://developer.android.com/reference/android/support/wearable/notifications/package-summary.html), las [vistas](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)y la [WatchFace](https://developer.android.com/reference/android/support/wearable/watchface/package-summary.html).

## <a name="samples"></a>Ejemplos

Puede encontrar una serie de [ejemplos](/samples/browse/?products=xamarin&term=Xamarin.Android%2bwear) con el desgaste de Android (o ir directamente a [GitHub](https://github.com/xamarin/monodroid-samples/tree/master/wear)).

|Muestra|Descripción|Instantánea|
|--- |--- |--- |
|[SkeletonWear](/samples/xamarin/monodroid-samples/wear-skeletonwear)|Un ejemplo sencillo de los aspectos básicos de los proyectos de portátil, incluidas las notificaciones de GridViewPager y interactivas.|![Captura de pantalla de Skeletonwear](images/skeleton.png)|
|[WatchViewStub](/samples/xamarin/monodroid-samples/wear-watchviewstub)|Demostración sencilla del control WatchViewStub que detecta la forma de pantalla y carga automáticamente el diseño correcto. Vea cómo funciona WatchViewStub en el diseño de **recursos, diseño y main_activity.xml** .|![Captura de pantalla de WatchViewStub](images/watchview.png)|
|[RecipeAssistant](/samples/xamarin/monodroid-samples/wear-recipeassistant)|Demostración de las páginas de notificación de desgaste, en forma de pasos de recetas. Las notificaciones se crean en RecipeService.cs.|![Captura de pantalla de RecipeAssistant](images/recipeassist.png)|
|[ElizaChat](/samples/xamarin/monodroid-samples/wear-elizachat)|Ejemplo divertido de interactuar con un "asistente personal" denominado Eliza, mediante el uso de notificaciones interactivas para crear una conversación mediante respuestas preparadas.|![Captura de pantalla de ElizaChat](images/eliza.png)|
|[GridViewPager](/samples/xamarin/monodroid-samples/wear-gridviewpager)|GridViewPager implementa el modelo de navegación 2D, donde el usuario se desliza verticalmente y, a continuación, horizontalmente para navegar por las opciones y el contenido.|![Captura de pantalla de GridViewPager](images/gridviewpager.png)|
|[WatchFace](/samples/xamarin/monodroid-samples/wear-watchface)|WatchFace es una esfera de inspección personalizada con una hora, un minuto y dos manos de estilo analógico. En este ejemplo se muestra cómo crear un servicio de reloj de inspección que dibuja la hora actual y controla los eventos de cambio de visibilidad y el modo ambiente. Incluye un receptor de difusión que escucha los cambios de zona horaria y actualiza automáticamente la hora según corresponda.|![Captura de pantalla de WatchFace](images/gridviewpager.png)|

## <a name="videos"></a>Vídeos

Consulte estos vínculos de vídeo que describen Xamarin. Android con soporte técnico de uso:

|Descripción|Instantánea|
|--- |--- |
|[Android L y mucho más](https://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/) &ndash; La versión preliminar para desarrolladores de Android L presentó una gran cantidad de nuevas API para que los desarrolladores aprovechen las ventajas de, incluido el diseño de materiales, las notificaciones y las nuevas animaciones, por nombrar algunas.|![Captura de pantalla de vídeo de Android L y muchas más presentaciones.](images/video-android-l.png)|
|[C# está en mis oídos y en mis ojos: el desgaste](https://www.youtube.com/watch?v=80H8tXByZQc) &ndash; de Google Glass y Android La informática de portátil podría parecer algo en el futuro (o un episodio de gadget de inspector), pero muchas personas ya están adoptando el futuro hoy mismo. Los desarrolladores de C# lo saben y ya tienen las herramientas y los conocimientos necesarios para aprovechar la eficacia de los dispositivos portátil (desde la evolución 2014).|![La captura de pantalla de vídeo de C# está en la presentación mis oídos y en mis ojos.](images/video-eyes-ears.png)|
|[Novedades de Xamarin. Android](https://www.youtube.com/watch?v=Gpqc2XZIQfU) &ndash; Android L, desgaste de Android, televisión de Android, Android auto, diseño de material y arte; ¿Qué significa esto como desarrollador de Xamarin? de evolucionar 2014.|![Vídeo captura de pantalla de las novedades de la presentación de Android de Xamarin dot.](Images/video-whats-new.png)|

<!--

March 18
https://blog.xamarin.com/android-wear/

August 14
https://blog.xamarin.com/android-l-developer-preview-android-wear-support/

August 27
https://blog.xamarin.com/tips-for-your-first-android-wear-app/

Watch Face
https://github.com/Redth/Xamarin.Wear.WatchFace
-->