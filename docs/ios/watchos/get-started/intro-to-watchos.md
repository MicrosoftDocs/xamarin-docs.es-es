---
title: Introducción a watchOS
description: Este documento proporciona información general de watchOS, que describe el ciclo de vida de la aplicación, los tipos de interfaz de usuario, tamaños de pantalla, limitaciones y mucho más.
ms.prod: xamarin
ms.assetid: 99c316d6-6707-40f6-bec9-801d05888759
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/13/2016
ms.openlocfilehash: 59d02db9fa2787e93ad88e4b6f37e0fef50572a5
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70293111"
---
# <a name="introduction-to-watchos"></a>Introducción a watchOS

> [!NOTE]
> Consulte la [Introducción a watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md) para obtener información general de las características más recientes.

## <a name="about-watchos"></a>Acerca de watchOS

Una solución de aplicación para watchOS tiene 3 proyectos:

- **Ver extensión** : un proyecto que contiene el código de la aplicación del reloj.
- **Ver aplicación** : contiene el guión gráfico de la interfaz de usuario y los recursos.
- **iOS App primario** : esta aplicación es una aplicación de iPhone normal. La aplicación de inspección y extensión se agrupan en la aplicación de iPhone para su entrega a la inspección del usuario.

En las aplicaciones para watchOS 1, el código de la extensión se ejecuta en el iPhone: el Apple Watch es realmente una pantalla externa. las aplicaciones para watchOS 2 y 3 se ejecutan completamente en el Apple Watch. Esta diferencia se muestra en el diagrama siguiente:

[![](intro-to-watchos-images/arch-sml.png "En este diagrama se muestra la diferencia entre watchos 1 y watchos 2 (y superior).")](intro-to-watchos-images/arch.png#lightbox)

Independientemente de qué versión de watchOS está dirigida, en Visual Studio para panel del Mac de solución una solución completa tendrá un aspecto similar al siguiente:

[![](intro-to-watchos-images/projectstructure-sml.png "El panel de solución")](intro-to-watchos-images/projectstructure.png#lightbox)

El *aplicación primaria* en un watchOS solución es una aplicación de iOS normales. Este es el único proyecto en la solución que sea visible **en el teléfono**. Casos de uso para esta aplicación incluye tutoriales, las pantallas administrativas y filtrado, cacheing, etcetera de nivel intermedio. Sin embargo, es posible que el usuario instalar y ejecutar la aplicación/extensión de inspección sin **nunca** haber abierto la aplicación primaria, por lo que si necesita la aplicación principal que se ejecuta para la inicialización única o administración, deberá programar su reloj / extensión de la aplicación para indicar al usuario que.

Aunque la aplicación principal proporciona la aplicación de inspección y extensión, se ejecutan en espacios aislados diferentes.

En watchOS 1 pueden compartir datos a través de un grupo de aplicación compartida o a través de la función estática `WKInterfaceController.OpenParentApplication`, lo que desencadenará la `UIApplicationDelegate.HandleWatchKitExtensionRequest` método en la aplicación primaria `AppDelegate` (consulte [trabajar con la aplicación primaria](~/ios/watchos/app-fundamentals/parent-app.md)).

En watchOS 2 o posterior, el marco de conectividad de inspección se utiliza para comunicarse con la aplicación primaria, con el `WCSession` clase.

## <a name="application-lifecycle"></a>Ciclo de vida de la aplicación

En la extensión de inspección, una subclase de la `WKInterfaceController` se crea una clase para cada escena de guión gráfico.

Estos `WKInterfaceController` clases son análogas a los `UIViewController` objetos en la programación de iOS pero no tienen el mismo nivel de acceso a la vista.
Por ejemplo, no se puede agregar controles a dinámicamente o reestructurar la interfaz de usuario.
Puede, sin embargo, ocultar y mostrar controles y, con algunos controles, cambiar su tamaño, la transparencia y opciones de apariencia.

El ciclo de vida de un `WKInterfaceController` objeto implica las siguientes llamadas:

- [Activo](xref:WatchKit.WKInterfaceController.Awake*) : Debe realizar la mayor parte de la inicialización en este método.
- [WillActivate](xref:WatchKit.WKInterfaceController.WillActivate) : Se llama justo antes de que la aplicación de inspección aparezca ante el usuario. Utilice este método para realizar la inicialización de último momento, iniciar las animaciones, etcetera.
- En este momento, aparece la aplicación del reloj y la extensión empieza a responder a la información de usuario y actualizar la presentación de la aplicación Watch por la lógica de aplicación.
- [DidDeactivate](xref:WatchKit.WKInterfaceController.DidDeactivate) después de la aplicación del reloj se ha descartado por el usuario, se llama a este método. Cuando se devuelve este método, no se puede modificar los controles de interfaz de usuario hasta la próxima vez `WillActivate` se llama. Este método también se llamará si se interrumpe la conexión para el iPhone.
- Después de la extensión se ha desactivado, no es accesible al programa. Las funciones asincrónicas pendientes **no** llamarse. Las extensiones de inspección Kit no puede utilizar los modos de procesamiento en segundo plano. Si se vuelve a activar el programa por el usuario, pero no se terminó la aplicación por el sistema operativo, será el primer método llamado `WillActivate`.

![](intro-to-watchos-images/wkinterfacecontrollerlifecycle.png "Información general sobre el ciclo de vida de aplicaciones")

## <a name="types-of-user-interface"></a>Tipos de interfaz de usuario

Hay tres tipos de interacción con que el usuario pueda tener con la aplicación del reloj.
Todos se programan mediante subclases personalizados de `WKInterfaceController`, por lo que la secuencia de ciclo de vida se explicó anteriormente se aplica universalmente (las notificaciones se programan con subclases de `WKUserNotificationController`, que a su vez es una subclase de `WKInterfaceController`):

### <a name="normal-interaction"></a>Interacción normal

La mayoría de interacción de aplicación/extensión de inspección serán con subclases de `WKInterfaceController` que escribir para que coincida con el segundo plano en la aplicación de inspección **Interface.storyboard**. Este tema se trata con detalle en la [instalación](~/ios/watchos/get-started/installation.md) y [Introducción](~/ios/watchos/get-started/index.md) artículos.
La siguiente imagen muestra una parte de la [inspección Kit catálogo](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) guión gráfico del ejemplo. Para cada escena mostrado aquí, hay un personalizado correspondiente `WKInterfaceController` (`LabelDetailController`, `ButtonDetailController`, `SwitchDetailController`, etc.) en el proyecto de extensión.

![](intro-to-watchos-images/scenes.png "Ejemplos de interacción normal")

### <a name="notifications"></a>Notificaciones

[Las notificaciones](~/ios/watchos/platform/notifications.md) son un caso de uso principal para el Apple Watch. Se admiten las notificaciones locales y remotas. Interacción con las notificaciones se produce en dos fases, llama a la apariencia de corta y larga.

Parece corta se muestran brevemente y mostrar el icono de la aplicación de inspección, su nombre y el título (como se especifica en `WKInterfaceController.SetTitle`).

El aspecto largo combina proporcionada por el sistema **franja** área y un botón Descartar con contenido personalizado basado en el guión gráfico.

`WKUserNotificationInterfaceController` extiende `WKInterfaceController` con los métodos `DidReceiveLocalNotification` y `DidReceiveRemoteNotification`.
Invalidar estos métodos para reaccionar ante eventos de notificación.

Para obtener más información sobre el diseño de interfaz de usuario de notificación, consulte el [directrices de interfaz humana de Apple Watch](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Notifications.html#//apple_ref/doc/uid/TP40014992-CH20-SW1)

![](intro-to-watchos-images/notifications.png "Notificaciones de ejemplo")

## <a name="screen-sizes"></a>Tamaños de pantalla

El Apple Watch tiene dos tamaños de caras: 38mm y 42mm, ambos con una relación de visualización 5:4 y una pantalla retina. Sus tamaños utilizables son:

- 38mm 136 x 170 píxeles lógicos (272 x 340 píxeles físicos)
- 42mm: 156 x 195 píxeles lógicos (312 x 390 píxeles físicos).

Use `WKInterfaceDevice.ScreenBounds` para determinar en qué pantalla se está ejecutando la aplicación del reloj.

Por lo general, es más fácil de desarrollar el diseño de texto y el diseño con la presentación de 38mm más restringido y, a continuación, escalar verticalmente.
Si empieza con el entorno de mayor tamaño, reducir verticalmente puede provocar que se superponen feo o un truncamiento de texto.

Obtenga más información sobre [trabajar con tamaños de pantalla](~/ios/watchos/app-fundamentals/screen-sizes.md).


## <a name="limitations-of-watchos"></a>Limitaciones de watchOS

Existen algunas limitaciones de watchOS a tener en cuenta al desarrollar aplicaciones para watchOS:

- Dispositivos de Apple Watch tienen un almacenamiento limitado: tenga en cuenta el espacio disponible antes de descargar archivos de gran tamaño (p ej. archivos de audio o película).

- Muchos watchOS [controles](~/ios/watchos/user-interface/index.md) tienen análogas en UIKit, pero varias clases (`WKInterfaceButton` lugar `UIButton`, `WKInterfaceSwitch` para `UISwitch`, etc.) y tienen un conjunto limitado de métodos en comparación con su UIKit equivalentes. Además, watchOS tiene algunos controles, como `WKInterfaceDate` (para mostrar una fecha y hora) no tiene ese UIKit.

  - No puede enrutar las notificaciones para la inspección sólo o solo el iPhone (qué tipo de control tiene el usuario a través de enrutamiento no se ha anunciado por Apple).

Algunas otras limitaciones conocidos y preguntas más frecuentes:

- Apple no permitirá la supervisión personalizada 3rd-party caras.

- Las API que permiten la inspección controlar iTunes en el teléfono conectado son privadas.


## <a name="further-reading"></a>Información adicional

Consulte la documentación de Apple:

- [Desarrollo para el Kit de inspección](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html#//apple_ref/doc/uid/TP40014969-CH8-SW1)

- [Vea la Guía de programación de Kit](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/DesigningaWatchKitApp.html)

- [Directrices de interfaz humana de Apple Watch](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/index.html#//apple_ref/doc/uid/TP40014992-CH3-SW1)


## <a name="related-links"></a>Vínculos relacionados

- [watchOS 3 catálogo (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [watchOS 1 catálogo (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Configuración e instalación](~/ios/watchos/get-started/installation.md)
- [Primer vídeo de la aplicación del reloj](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Del desarrollo de Apple para guía Kit de inspección](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html)
- [Sugerencias de WatchKit de Apple](https://developer.apple.com/watchkit/tips/)
- [Introducción a watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
