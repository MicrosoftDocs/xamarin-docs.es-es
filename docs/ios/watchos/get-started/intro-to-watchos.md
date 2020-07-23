---
title: Introducción a watchOS
description: En este documento se proporciona información general de watchos, que describe el ciclo de vida de la aplicación, los tipos de interfaz de usuario, los tamaños de pantalla, las limitaciones, etc.
ms.prod: xamarin
ms.assetid: 99c316d6-6707-40f6-bec9-801d05888759
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: 1b71ff60ea0e23ce9d631286aec624a84f163ce5
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937517"
---
# <a name="introduction-to-watchos"></a>Introducción a watchOS

> [!NOTE]
> Consulte la [Introducción a watchos 3](~/ios/watchos/platform/introduction-to-watchos3/index.md) para obtener información general sobre las características más recientes.

## <a name="about-watchos"></a>Acerca de watchos

Una solución de aplicación de watchos tiene 3 proyectos:

- **Extensión de inspección** : proyecto que contiene el código de la aplicación de inspección.
- **Aplicación de inspección** : contiene el guión gráfico y los recursos de la interfaz de usuario.
- **aplicación primaria de iOS** : esta aplicación es una aplicación de iPhone normal. La aplicación de inspección y la extensión se agrupan en la aplicación de iPhone para entregarlas en el reloj del usuario.

En las aplicaciones de watchos 1, el código de la extensión se ejecuta en el iPhone: el Apple Watch es realmente una pantalla externa. las aplicaciones watchos 2 y 3 se ejecutan completamente en el Apple Watch. Esta diferencia se muestra en el diagrama siguiente:

[![En este diagrama se muestra la diferencia entre watchos 1 y watchos 2 (y superior).](intro-to-watchos-images/arch-sml.png)](intro-to-watchos-images/arch.png#lightbox)

Independientemente de la versión de watchos de destino, en Visual Studio para Mac Panel de solución una solución completa tendrá un aspecto similar al siguiente:

[![El Panel de solución](intro-to-watchos-images/projectstructure-sml.png)](intro-to-watchos-images/projectstructure.png#lightbox)

La *aplicación primaria* en una solución de watchos es una aplicación de iOS normal. Este es el único proyecto de la solución que es visible **en el teléfono**. Los casos de uso de esta aplicación incluyen tutoriales, pantallas administrativas y filtrado de nivel medio, almacenamiento en caché, etc. Sin embargo, es posible que el usuario instale y ejecute la aplicación o la extensión de inspección **sin tener** que abrir la aplicación primaria, por lo que si necesita que la aplicación primaria se ejecute para la inicialización o la administración de un solo uso, debe programar la aplicación de inspección o la extensión para indicar al usuario que.

Aunque la aplicación principal ofrece la aplicación inspección y la extensión, se ejecutan en diferentes espacios aislados.

En watchos 1, pueden compartir datos a través de un grupo de aplicaciones compartido o a través de la función estática `WKInterfaceController.OpenParentApplication` , lo que desencadenará el `UIApplicationDelegate.HandleWatchKitExtensionRequest` método en la aplicación primaria `AppDelegate` (vea [trabajar con la aplicación primaria](~/ios/watchos/app-fundamentals/parent-app.md)).

En watchos 2 o posterior, el marco de conectividad de Watch se usa para comunicarse con la aplicación primaria, mediante la `WCSession` clase.

## <a name="application-lifecycle"></a>Ciclo de vida de la aplicación

En la extensión de inspección, se crea una subclase de la `WKInterfaceController` clase para cada escena de guion gráfico.

Estas `WKInterfaceController` clases son análogas a los `UIViewController` objetos en la programación de iOS pero no tienen el mismo nivel de acceso a la vista.
Por ejemplo, no puede Agregar controles de forma dinámica a la interfaz de usuario ni reestructurarla.
Sin embargo, puede ocultar y mostrar controles y, con algunos controles, cambiar su tamaño, transparencia y opciones de apariencia.

El ciclo de vida de un `WKInterfaceController` objeto implica las siguientes llamadas:

- [Activo](xref:WatchKit.WKInterfaceController.Awake*) : debe realizar la mayor parte de la inicialización en este método.
- [WillActivate](xref:WatchKit.WKInterfaceController.WillActivate) : se llama justo antes de que la aplicación de inspección aparezca ante el usuario. Utilice este método para realizar la inicialización del último momento, animaciones de inicio, etc.
- En este punto, la aplicación de inspección aparece y la extensión comienza a responder a la entrada del usuario y a actualizar la pantalla de la aplicación de inspección según la lógica de la aplicación.
- [DidDeactivate](xref:WatchKit.WKInterfaceController.DidDeactivate) Después de que el usuario haya descartado la aplicación de inspección, se llama a este método. Una vez que este método devuelve, no se pueden modificar los controles de interfaz de usuario hasta que `WillActivate` se llama a la próxima vez. También se llamará a este método si se interrumpe la conexión al iPhone.
- Una vez desactivada la extensión, el programa no puede tener acceso a ella. **No** se llamará a las funciones asincrónicas pendientes. Es posible que las extensiones del kit de inspección no usen modos de procesamiento en segundo plano. Si el usuario reactiva el programa pero el sistema operativo no ha terminado la aplicación, el primer método llamado será `WillActivate` .

![Introducción al ciclo de vida de la aplicación](intro-to-watchos-images/wkinterfacecontrollerlifecycle.png)

## <a name="types-of-user-interface"></a>Tipos de interfaz de usuario

Hay tres tipos de interacción que el usuario puede tener con la aplicación de inspección.
Todos se programan mediante subclases personalizadas de `WKInterfaceController` , por lo que la secuencia de ciclo de vida descrita anteriormente se aplica universalmente (las notificaciones se programan con `WKUserNotificationController` subclases de, que a su vez es una subclase de `WKInterfaceController` ):

### <a name="normal-interaction"></a>Interacción normal

La mayoría de la interacción de la aplicación o la extensión de inspección será con subclases de `WKInterfaceController` que escriba para que se correspondan con escenas en el **guión gráfico**de la aplicación de inspección. Esto se trata en detalle en los artículos de [instalación](~/ios/watchos/get-started/installation.md) y [Introducción](~/ios/watchos/get-started/index.md) .
En la imagen siguiente se muestra una parte del guion gráfico del ejemplo de [Catálogo del kit de inspección](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) . Para cada escena mostrada aquí, hay un personalizado correspondiente `WKInterfaceController` ( `LabelDetailController` , `ButtonDetailController` , `SwitchDetailController` , etc.) en el proyecto de extensión.

![Ejemplos de interacción normal](intro-to-watchos-images/scenes.png)

### <a name="notifications"></a>Notificaciones

Las [notificaciones](~/ios/watchos/platform/notifications.md) son un caso de uso principal para el Apple Watch. Se admiten las notificaciones locales y remotas. La interacción con las notificaciones se produce en dos fases, denominadas a corto y largo plazo.

La apariencia breve se muestra brevemente y muestra el icono de la aplicación de inspección, su nombre y el título (tal y como se especifica con `WKInterfaceController.SetTitle` ).

El aspecto largo combina un área de **franja de Marcos** proporcionada por el sistema y el botón descartar con el contenido personalizado basado en guiones gráficos.

`WKUserNotificationInterfaceController`extiende `WKInterfaceController` con los métodos `DidReceiveLocalNotification` y `DidReceiveRemoteNotification` .
Invalide estos métodos para reaccionar a los eventos de notificación.

Para obtener más información sobre el diseño de la interfaz de usuario de notificaciones, consulte las directrices de la [interfaz humana Apple Watch](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Notifications.html#//apple_ref/doc/uid/TP40014992-CH20-SW1)

![Notificaciones de ejemplo](intro-to-watchos-images/notifications.png)

## <a name="screen-sizes"></a>Tamaños de pantalla

El Apple Watch tiene dos tamaños de caras: 38mm y 42mm, ambos con una relación de visualización 5:4 y una pantalla retina. Sus tamaños utilizables son:

- 38mm: 136 x 170 píxeles lógicos (272 x 340 píxeles físicos)
- 42mm: 156 x 195 píxeles lógicos (312 x 390 píxeles físicos).

Use `WKInterfaceDevice.ScreenBounds` para determinar en qué pantalla se está ejecutando la aplicación de inspección.

Por lo general, es más fácil desarrollar el diseño de texto y diseño con la pantalla de 38mm más restringida y, después, escalar verticalmente.
Si empieza con un entorno más grande, el escalado vertical puede provocar una superposición o un truncamiento de texto desagradable.

Obtenga más información sobre cómo [trabajar con tamaños de pantalla](~/ios/watchos/app-fundamentals/screen-sizes.md).

## <a name="limitations-of-watchos"></a>Limitaciones de watchos

Hay algunas limitaciones de watchos que se deben tener en cuenta al desarrollar aplicaciones de watchos:

- Apple Watch dispositivos tienen almacenamiento limitado: tenga en cuenta el espacio disponible antes de descargar archivos de gran tamaño (por ejemplo, archivos de audio o de películas).

- Muchos [controles](~/ios/watchos/user-interface/index.md) de watchos tienen análogos en UIKit, pero son clases diferentes ( `WKInterfaceButton` en lugar de, `UIButton` `WKInterfaceSwitch` para `UISwitch` , etc.) y tienen un conjunto limitado de métodos en comparación con sus equivalentes de UIKit. Además, watchos tiene algunos controles como `WKInterfaceDate` (para mostrar una fecha y hora) que UIKit no tiene.

  - No se pueden enrutar notificaciones solo a la inspección o al iPhone (el tipo de control que el usuario tiene sobre el enrutamiento no se ha anunciado por Apple).

Otras limitaciones conocidas y preguntas más frecuentes:

- Apple no permitirá caras de inspección personalizada de terceros.

- Las API que permiten a la inspección controlar iTunes en el teléfono conectado son privadas.

## <a name="further-reading"></a>Lecturas adicionales

Consulte la documentación de Apple:

- [Desarrollo para el kit de inspección](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html#//apple_ref/doc/uid/TP40014969-CH8-SW1)

- [Guía de programación del kit de inspección](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/DesigningaWatchKitApp.html)

- [Directrices de Apple Watch interfaz humana](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/index.html#//apple_ref/doc/uid/TP40014992-CH3-SW1)

## <a name="related-links"></a>Vínculos relacionados

- [Catálogo de watchos 3 (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Catálogo de watchos 1 (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Configuración e instalación](~/ios/watchos/get-started/installation.md)
- [Vídeo de la primera aplicación de inspección](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Guía del kit de desarrollo de Apple para Watch](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html)
- [Sugerencias de WatchKit de Apple](https://developer.apple.com/watchkit/tips/)
- [Introducción a watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
