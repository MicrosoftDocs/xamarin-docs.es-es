---
title: Introducción al procesamiento en segundo plano en iOS
description: 'En este documento se describe el procesamiento en iOS: Estados de la aplicación, métodos del ciclo de vida de la aplicación y actualización de la aplicación en segundo plano.'
ms.prod: xamarin
ms.assetid: E214F2C7-E74E-46C7-B5BA-080B30D61250
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/24/2018
ms.openlocfilehash: 1899ee225d0ea285a38ba7b4d341e5d3a4452639
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521387"
---
# <a name="introduction-to-backgrounding-in-ios"></a>Introducción al procesamiento en segundo plano en iOS

iOS regula el procesamiento en segundo plano de manera muy estrecha y ofrece tres enfoques para implementarlo:

- **Registrar una tarea en segundo plano** : Si una aplicación necesita completar una tarea importante, puede pedir a iOS que interrumpa la tarea cuando la aplicación pase a segundo plano. Por ejemplo, es posible que una aplicación necesite terminar el registro de un usuario o terminar de descargar un archivo grande.
- **Registrarse como una aplicación necesaria** para el fondo: una aplicación se puede registrar como un tipo específico de aplicación que conoce los requisitos de fondo específicos, como *Audio* , *VoIP* , *accesorio externo* , *NewsStand* , y *Ubicación* . Estas aplicaciones tienen privilegios de procesamiento en segundo plano continuos, siempre y cuando realicen tareas que estén dentro de los parámetros del tipo de aplicación registrado.
- **Habilitar actualizaciones en segundo plano** : las aplicaciones pueden desencadenar actualizaciones en segundo plano con *supervisión de regiones* o mediante la escucha de *cambios de ubicación significativos* . A partir de iOS 7, las aplicaciones también se pueden registrar para actualizar el contenido en segundo plano mediante la *captura en segundo plano* o las *notificaciones remotas* .


## <a name="application-states-and-application-delegate-methods"></a>Estados de aplicación y métodos de delegado de aplicación

Antes de profundizar en el código para el procesamiento en segundo plano en iOS, es necesario saber cómo afecta el fondo al ciclo de vida de una aplicación de iOS.

El ciclo de vida de la aplicación de iOS es una colección de Estados y métodos de la aplicación para moverse entre ellos. Una aplicación realiza una transición entre Estados en función del comportamiento del usuario y de los requisitos de fondo de la aplicación. El movimiento se ilustra en el siguiente diagrama:

 [![](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png "Diagrama de los Estados de aplicación y métodos de delegado de aplicación")](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png#lightbox)

- **No se está ejecutando** : la aplicación todavía no se ha iniciado en el dispositivo.
- En **ejecución/activa** : la aplicación está en la pantalla y está ejecutando código en primer plano.
- **Inactivo** : la aplicación se interrumpe por una llamada telefónica entrante, texto u otra interrupción.
- En segundo plano: la aplicación se mueve al fondo y continúa ejecutando el código en segundo plano.
- **Suspended** : Si la aplicación no tiene código para ejecutarse en segundo plano, o si se ha completado todo el código, el sistema operativo suspenderá la aplicación. El proceso de una aplicación suspendida se mantiene activo, pero la aplicación no puede ejecutar ningún código en este estado.
- **Volver a no ejecución/terminación (poco frecuente)** : en ocasiones, el proceso de la aplicación se destruye y la aplicación vuelve al estado *no en ejecución* . Esto sucede en situaciones de memoria insuficiente o si el usuario finaliza manualmente la aplicación.


Desde la introducción de la compatibilidad con la multitarea, iOS no suele finalizar las aplicaciones inactivas y, en su lugar, mantiene sus procesos suspendidos en la memoria. Mantener activo el proceso de una aplicación garantiza que la aplicación se inicie rápidamente la próxima vez que el usuario la abra. También significa que las aplicaciones pueden moverse libremente del estado *suspendido* al estado de fondo sin dibujar en los recursos del sistema. iOS 7 aprovecha esta característica con nuevas API que permiten a las aplicaciones pausar las tareas en segundo plano cuando el dispositivo entra en suspensión, actualizar el contenido directamente desde el fondo sin interacción del usuario, etc. Trataremos las nuevas API en las [técnicas de fondo de iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md).

## <a name="application-lifecycle-methods"></a>Métodos de ciclo de vida de la aplicación

Cuando una aplicación cambia el estado, iOS notifica a la aplicación a través de los `AppDelegate` métodos de evento de la clase:

- `OnActivated`: Se llama la primera vez que se inicia la aplicación y cada vez que la aplicación vuelve a estar en primer plano. Este es el lugar donde se coloca el código que debe ejecutarse cada vez que se abre la aplicación.
- `OnResignActivation`-Si el usuario recibe una interrupción, como una llamada de texto o de teléfono, se llama a este método y la aplicación se desactiva temporalmente. Si el usuario acepta la llamada de teléfono, la aplicación se enviará al fondo.
- `DidEnterBackground`-Se llama cuando la aplicación entra en el estado de fondo, este método proporciona a una aplicación unos cinco segundos para prepararse para una posible terminación. Use esta vez para guardar los datos y las tareas de usuario, y para quitar información confidencial de la pantalla.
- `WillEnterForeground`-Cuando un usuario vuelve a una aplicación de fondo o suspendida y lo inicia en primer plano, `WillEnterForeground` se llama a. Este es el momento de preparar la aplicación para que se ponga en primer plano rehidratar cualquier estado `DidEnterBackground` guardado durante.  `OnActivated`se llamará inmediatamente después de que se complete este método.
- `WillTerminate`-La aplicación se cierra y se destruye su proceso. Solo se llama a este método si la multitarea no está disponible en el dispositivo o en la versión del sistema operativo, si hay poca memoria o si el usuario finaliza manualmente una aplicación en el fondo. Tenga en cuenta que las aplicaciones suspendidas que se `WillTerminate` terminan no llamarán.


En el diagrama siguiente se muestra cómo encajan los Estados de la aplicación y los métodos del ciclo de vida:

 [![](introduction-to-backgrounding-in-ios-images/image2.png "En este diagrama se muestra cómo se ajustan los Estados de la aplicación y los métodos del ciclo de vida")](introduction-to-backgrounding-in-ios-images/image2.png#lightbox)

## <a name="user-controls-for-backgrounding-in-ios"></a>Controles de usuario para el procesamiento en segundo plano en iOS

iOS 7 presentó varias características para ofrecer a los usuarios más control sobre el estado de fondo de una aplicación. El conmutador de aplicación y la configuración de actualización de la aplicación en segundo plano afectan al ciclo de vida de la aplicación.

### <a name="app-switcher"></a>Conmutador de aplicación

El conmutador de aplicaciones es una característica de control importante incluida en iOS 7. Se inicia haciendo doble punteo en el botón **Inicio** y muestra las aplicaciones cuyos procesos están activos:

 [![](introduction-to-backgrounding-in-ios-images/app-switcher-.png "Moverse entre aplicaciones mediante el conmutador de aplicaciones")](introduction-to-backgrounding-in-ios-images/app-switcher-.png#lightbox)

Con el conmutador de aplicaciones, los usuarios pueden desplazarse a través de instantáneas de todas las aplicaciones en el fondo y en suspensión. Al puntear en una aplicación, se inicia en primer plano. Al pasar el dedo hacia arriba, se quita la aplicación del fondo, terminando el proceso. Veremos más detenidamente el conmutador de aplicaciones en la [demostración del ciclo de vida](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md) de la aplicación de iOS en la sección siguiente.

> [!IMPORTANT]
> El conmutador de aplicaciones no muestra ninguna diferencia entre las aplicaciones en el fondo y las suspendidas.



### <a name="background-app-refresh-settings"></a>Configuración de actualización de la aplicación en segundo plano

iOS 7 aumenta el control del usuario sobre el ciclo de vida de la aplicación, ya que permite a los usuarios no participar en las aplicaciones [registradas para el procesamiento en segundo plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md). *Esto no impide que las aplicaciones ejecuten tareas en segundo plano*.

Los usuarios pueden cambiar esta configuración Si navega a **configuración > General > actualización de la aplicación en segundo plano** y edita los privilegios de fondo de una aplicación seleccionada. Si la actualización de la aplicación en segundo plano está establecida en desactivado, la aplicación se suspenderá inmediatamente al entrar en segundo plano y evitará que realice cualquier procesamiento en segundo plano:

 [![](introduction-to-backgrounding-in-ios-images/settings-.png "Configuración de actualización de la aplicación en segundo plano")](introduction-to-backgrounding-in-ios-images/settings-.png#lightbox)

Los desarrolladores pueden comprobar el estado de la aplicación de `BackgroundRefreshStatus` actualización en segundo plano con la API. Para obtener un ejemplo, consulte la [receta comprobar la configuración de la actualización en segundo plano](https://github.com/xamarin/recipes/tree/master/Recipes/ios/multitasking/check_background_refresh_setting).

Hemos tratado los aspectos básicos del ciclo de vida de la aplicación de iOS y las características para controlar el ciclo de vida de la aplicación. A continuación, vamos a ver el ciclo de vida de la aplicación de iOS en acción.

