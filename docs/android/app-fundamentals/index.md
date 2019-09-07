---
title: Aspectos básicos de la aplicación Xamarin. Android
description: Conceptos básicos de la aplicación
ms.prod: xamarin
ms.assetid: 935B8BFE-23B7-4239-5C87-F4A503B889CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: af7ba83b9026a91028f4ffa9894d564d5ff13eb8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755301"
---
# <a name="xamarinandroid-application-fundamentals"></a>Aspectos básicos de la aplicación Xamarin. Android

En esta sección se proporciona una guía sobre algunas de las tareas más comunes o conceptos que los desarrolladores deben tener en cuenta al desarrollar aplicaciones de Android.

## <a name="accessibilityandroidapp-fundamentalsaccessibilitymd"></a>[Accesibilidad](~/android/app-fundamentals/accessibility.md)

En esta página se describe cómo usar las API de accesibilidad de Android para compilar aplicaciones de acuerdo con la [lista de comprobación de accesibilidad](~/cross-platform/app-fundamentals/accessibility.md).

## <a name="understanding-android-api-levelsandroidapp-fundamentalsandroid-api-levelsmd"></a>[Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md)

En esta guía se describe cómo Android usa los niveles de API para administrar la compatibilidad de aplicaciones en diferentes versiones de Android, y se explica cómo configurar los valores del proyecto de Xamarin. Android para implementar estos niveles de API en la aplicación. Además, en esta guía se explica cómo escribir código en tiempo de ejecución que se ocupe de los distintos niveles de API y se proporciona una lista de referencia de todos los niveles de API de Android, números de versión (como Android 8,0), nombres de código de Android (como Oreo) y códigos de versión de compilación.

## <a name="resources-in-androidandroidapp-fundamentalsresources-in-androidindexmd"></a>[Recursos en Android](~/android/app-fundamentals/resources-in-android/index.md)

En este artículo se presenta el concepto de recursos de Android en Xamarin. Android y se explica cómo usarlos. En él se explica cómo usar los recursos de la aplicación de Android para admitir la localización de aplicaciones y varios dispositivos, incluidos los tamaños y las densidades de pantalla variables.

## <a name="activity-lifecycleandroidapp-fundamentalsactivity-lifecycleindexmd"></a>[Ciclo de vida de la actividad](~/android/app-fundamentals/activity-lifecycle/index.md)

Las actividades son un bloque de creación fundamental de las aplicaciones de Android y pueden existir en varios Estados diferentes. El ciclo de vida de la actividad comienza con la creación de instancias y termina con la destrucción, e incluye muchos Estados entre ellos. Cuando una actividad cambia de estado, se llama al método de evento del ciclo de vida adecuado, que notifica a la actividad el cambio de estado inminente y le permite ejecutar el código para adaptarse a ese cambio. En este artículo se examina el ciclo de vida de las actividades y se explica la responsabilidad que tiene una actividad durante cada uno de estos cambios de estado para que formen parte de una aplicación con un comportamiento correcto y confiable.

## <a name="localizationandroidapp-fundamentalslocalizationmd"></a>[Localización](~/android/app-fundamentals/localization.md)

En este artículo se explica cómo localizar un Xamarin. Android en otros lenguajes traduciendo cadenas y proporcionando imágenes alternativas.

## <a name="servicesandroidapp-fundamentalsservicesindexmd"></a>[Servicios](~/android/app-fundamentals/services/index.md)

En este artículo se tratan los servicios de Android, que son componentes de Android que permiten realizar el trabajo en segundo plano. Explica los diferentes escenarios para los que los servicios son adecuados y muestra cómo implementarlos para realizar tareas en segundo plano de ejecución prolongada, así como para proporcionar una interfaz para llamadas a procedimientos remotos.

## <a name="broadcast-receiversandroidapp-fundamentalsbroadcast-receiversmd"></a>[Receptores de difusión](~/android/app-fundamentals/broadcast-receivers.md)

En esta guía se explica cómo crear y usar receptores de difusión, un componente de Android que responde a las difusiones en todo el sistema, en Xamarin. Android.

## <a name="permissionsandroidapp-fundamentalspermissionsmd"></a>[Permisos](~/android/app-fundamentals/permissions.md)

Puede usar la compatibilidad de herramientas integrada en Visual Studio para Mac o Visual Studio para crear y agregar permisos al manifiesto de Android. En este documento se describe cómo agregar permisos en Visual Studio y Xamarin Studio.

## <a name="graphics-and-animationandroidapp-fundamentalsgraphics-and-animationmd"></a>[Gráficos y animación](~/android/app-fundamentals/graphics-and-animation.md)

Android proporciona un marco de trabajo muy rico y diverso para admitir animaciones y gráficos 2D. En este documento se presentan estos marcos de trabajo y se describe cómo crear animaciones y gráficos personalizados y cómo usarlos en una aplicación de Xamarin. Android.

## <a name="cpu-architecturesandroidapp-fundamentalscpu-architecturesmd"></a>[Arquitecturas de CPU](~/android/app-fundamentals/cpu-architectures.md)

Xamarin. Android admite varias arquitecturas de CPU, incluidos los dispositivos de 32 bits y de 64 bits. En este artículo se explica cómo dirigir una aplicación a una o varias arquitecturas de CPU compatibles con Android.

## <a name="handling-rotationandroidapp-fundamentalshandling-rotationmd"></a>[Control de giro](~/android/app-fundamentals/handling-rotation.md)

En este artículo se describe cómo controlar los cambios de orientación de dispositivos en Xamarin. Android. En él se explica cómo trabajar con el sistema de recursos de Android para cargar automáticamente recursos para una orientación de dispositivo determinada, así como cómo controlar mediante programación los cambios de orientación. A continuación, se describen las técnicas para mantener el estado cuando se gira un dispositivo.

## <a name="android-audioandroidapp-fundamentalsandroid-audiomd"></a>[Audio de Android](~/android/app-fundamentals/android-audio.md)

El sistema operativo Android proporciona una amplia compatibilidad para multimedia, que engloba audio y vídeo. Esta guía se centra en el audio en Android y trata la reproducción y grabación de audio mediante el reproductor de audio integrado y las clases de grabadora, así como la API de audio de bajo nivel. También se explica cómo trabajar con eventos de audio difundidos por otras aplicaciones, para que los desarrolladores puedan compilar aplicaciones con el comportamiento correcto.

## <a name="notificationsandroidapp-fundamentalsnotificationsindexmd"></a>[Notificaciones](~/android/app-fundamentals/notifications/index.md)

En esta sección se explica cómo implementar notificaciones locales y remotas en Xamarin. Android. Describe los distintos elementos de la interfaz de usuario de una notificación de Android y describe las API implicadas en la creación y visualización de una notificación. En el caso de las notificaciones remotas, se explican la mensajería en la nube de Google Cloud Messaging y Firebase. Se incluyen tutoriales paso a paso y ejemplos de código.

## <a name="touchandroidapp-fundamentalstouchindexmd"></a>[Entrada táctil](~/android/app-fundamentals/touch/index.md)

En esta sección se explican los conceptos y los detalles de la implementación de gestos táctiles en Android. Las API táctiles se introducen y explican seguido de una exploración de reconocedores de gestos.

## <a name="httpclient-stack-and-ssltlsandroidapp-fundamentalshttp-stackmd"></a>[Pila HttpClient y SSL/TLS](~/android/app-fundamentals/http-stack.md)

En esta sección se explican los selectores de la pila HttpClient y de la implementación de SSL/TLS para Android. Estas opciones determinan la implementación de HttpClient y SSL/TLS que usarán las aplicaciones de Xamarin. Android.

## <a name="writing-responsive-applicationswriting-responsive-appsmd"></a>[Escribir aplicaciones con capacidad de respuesta](writing-responsive-apps.md)

En este artículo se describe cómo usar subprocesos para mantener la capacidad de respuesta de una aplicación de Xamarin. Android moviendo tareas de ejecución prolongada a un subproceso en segundo plano.
