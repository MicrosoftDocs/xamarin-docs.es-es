---
title: las notificaciones en Xamarin de watchOS
description: Este documento describe cómo trabajar con las notificaciones de watchOS de Xamarin. Trata de crear controladores de notificación, generar notificaciones y probar las notificaciones.
ms.prod: xamarin
ms.assetid: 0BC1306E-0713-4592-996E-7530CCF281E7
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 39c77b4016027171a4e76bc4fb15c77d733cf5ba
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065370"
---
# <a name="watchos-notifications-in-xamarin"></a>las notificaciones en Xamarin de watchOS

Las aplicaciones de Watch pueden recibir notificaciones si la aplicación contenedora de iOS es compatible con ellos. No hay control de notificación integrados, por lo que no *necesita* para agregar la compatibilidad adicional de notificación descrita más adelante, sin embargo si desea personalizar el comportamiento de las notificaciones y apariencia, siga leyendo.

Hacer referencia a la [notificaciones de iOS](~/ios/platform/user-notifications/deprecated/index.md) doc para obtener más información sobre cómo agregar compatibilidad con las notificaciones a la aplicación de iOS en la solución.

## <a name="creating-notification-controllers"></a>Creación de controladores de notificación

En el guión gráfico controladores de las notificaciones tienen un tipo especial de segue desencadenándolos. Al arrastrar un nuevo **controlador de interfaz de notificación** en un guión gráfico automáticamente tendrán un segue adjunto:

![](notifications-images/notification-storyboard1.png "Un nuevo controlador de interfaz de notificación con un segue adjuntado")

Cuando la notificación de segue está seleccionada puede editar sus propiedades:

![](notifications-images/notification-storyboard2.png "La notificación de segue seleccionado")

Después de personalizar el controlador puede parecerse a este ejemplo desde el WatchKitCatalog:

![](notifications-images/notifications-segue.png "Las propiedades de notificación")


Hay dos tipos de notificación:

- Vista estática de **aspecto breve** no desplazable definida por el sistema.

- **Long vistazo** : desplazable, vista personalizable definida por el usuario! Se pueden especificar una versión más sencilla y estática y una versión dinámica más compleja.

### <a name="short-look-notification-controller"></a>Controlador de notificación breve vistazo

La interfaz de usuario breve vistazo consta de simplemente el icono de la aplicación, nombre de la aplicación y la cadena de título de notificación.

Si el usuario no omite la notificación, el sistema cambiará automáticamente a una notificación prolongada vistazo que proporciona más información.


### <a name="long-look-notification-controller"></a>Long-Buscar controlador de notificación

El sistema operativo decide si debe mostrar la vista estática o dinámica en función de una serie de factores. Debe proporcionar una interfaz estática y, opcionalmente, puede incluir también una interfaz dinámica para las notificaciones.

#### <a name="static"></a>Estático

La vista estática debe ser sencillo y rápido mostrar.

![](notifications-images/notification-static.png "La vista estática")

#### <a name="dynamic"></a>Dinámico

La vista dinámica puede mostrar más datos y proporcionar más interactividad.

![](notifications-images/notification-dynamic.png "La vista dinámica")


## <a name="generating-notifications"></a>Generación de notificaciones

Las notificaciones pueden proceder de un servidor remoto ([servicio de notificaciones Push de Apple](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html), o Apple Push Notification Service) o se pueden generar localmente en la aplicación de iOS.

Hacer referencia a la [tutorial de notificaciones de iOS](~/ios/platform/user-notifications/deprecated/local-notifications-in-ios-walkthrough.md) para obtener un ejemplo de cómo generar notificaciones locales y el [WatchNotifications ejemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-watchnotifications) para obtener un ejemplo ilustrativo.

Notificaciones locales deben tener la `AlertTitle` establecer que se mostrará en el Apple Watch - la `AlertTitle` cadena se muestra en la interfaz breve vistazo. Tanto el `AlertTitle` y `AlertBody` se muestran en la lista de notificaciones; y el `AlertBody` se muestra en la interfaz prolongada vistazo.

Esta captura de pantalla muestra la `AlertTitle` que se muestra en la lista de notificaciones y el `AlertBody` muestra en la interfaz prolongada vistazo (mediante el [código de ejemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-watchnotifications)):

![](notifications-images/watch-notificationslist-sml.png "Esta captura de pantalla muestra la que se muestra en la lista de notificaciones de AlertTitle") ![](notifications-images/watch-notificationcontroller-sml.png "AlertBody la muestra en la interfaz prolongada vistazo")

## <a name="testing-notifications"></a>Probar las notificaciones

Notificaciones (locales y remotas) solo se pueden correctamente probar en un dispositivo, sin embargo, puede simular mediante un **.json** archivo en el simulador de iOS.

### <a name="testing-on-apple-watch"></a>Las pruebas en Apple Watch

Al probar las notificaciones en un Apple Watch, recuerde que [la documentación de Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html) indica lo siguiente:

> Cuando llega a una de las notificaciones locales o remotos de la aplicación de iPhone del usuario, iOS decide si debe mostrar esa notificación en el iPhone o en el Apple Watch.

Esto es alluding al hecho de que iOS decide si aparecerá una notificación en el iPhone o en el reloj. Si el iPhone emparejado está activo cuando se recibe una notificación, la notificación es probable que se muestren en el iPhone y *no* enruta a la inspección.

Para asegurarse de que aparezca la notificación en el reloj, desactive la pantalla de iPhone (al presionar el botón de encendido una vez) o déjalo pasar al modo de suspensión. Si la inspección emparejada esté dentro del alcance, dispone de alimentación y se está agotada en la muñeca, la notificación se enrutarán allí y aparecen en el reloj (acompañado por un sutil).

### <a name="testing-on-the-ios-simulator"></a>Las pruebas en el simulador de iOS

Le *debe* proporcionar una carga JSON de prueba al modo de notificación de prueba en el simulador de iOS. Establecer la ruta de acceso en el **argumentos de ejecución personalizado** ventana de Visual Studio para Mac.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Visual Studio para Mac mostrará opciones adicionales cuando una extensión de inspección se establece como el **proyecto de inicio**.
Haga doble clic en el proyecto de extensión de inspección y elija **ejecutar con > parámetros personalizados...** :

[![](notifications-images/runwith-customparams-sml.png "Ejecución con propiedades personalizadas")](notifications-images/runwith-customparams.png#lightbox)

Se abrirá el **argumentos de ejecución** ventana que contiene un **WatchKit** ficha. Seleccione **notificación** y proporcionar una carga JSON, a continuación, presione **Execute** para iniciar la aplicación del reloj en el simulador:

[![](notifications-images/runwith-execargs-sml.png "Seleccione predeterminado de la carga de notificación")](notifications-images/runwith-execargs.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para establecer la carga de notificación de prueba en el botón secundario de Visual Studio en la extensión de inspección para editar el **las propiedades del proyecto**. Vaya a la **depurar** sección y seleccione un archivo JSON de notificaciones en la lista (mostrará automáticamente todos los archivos JSON que se incluye en el proyecto).

[![](notifications-images/runwith-execargs-sml-vs.png "Seleccione un archivo JSON de notificaciones")](notifications-images/runwith-execargs-vs.png#lightbox)

Cuando la extensión de inspección es la **proyecto de inicio**, Visual Studio mostrará opciones adicionales, como se muestra a continuación. Elija uno de los **notificación** opciones para iniciar la aplicación del reloj en **notificación** modo (mediante el archivo JSON seleccionado en la ventana Propiedades):

![](notifications-images/runwith-vs.png "El menú del dispositivo")

-----

El controlador de notificación predeterminado tiene este aspecto al realizar pruebas en el simulador con el archivo JSON de carga de forma predeterminada:

![](notifications-images/notification-debug-sml.png "Una notificación de ejemplo")

También es posible usar el [línea de comandos](~/ios/watchos/troubleshooting.md#command_line) para iniciar el simulador de iOS.

### <a name="example-notification-payload"></a>Carga de notificación de ejemplo

En el [inspección Kit catálogo](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) ejemplo existe es un archivo de ejemplo carga JSON **NotificationPayload.json** (indicadas a continuación).

```json
{
    "aps": {
        "alert": "Test message content",
        "title": "Optional title",
        "category": "myCategory"
        },

        "WatchKit Simulator Actions": [
        {
            "title": "First Button",
            "identifier": "firstButtonAction"
        }
        ],

        "customKey": "Use this file to define a testing payload for your notifications. The aps dictionary specifies the category, alert text and title. The WatchKit Simulator Actions array can provide info for one or more action buttons in addition to the standard Dismiss button. Any other top level keys are custom payload. If you have multiple such JSON files in your project, you'll be able to choose between them in when selecting to debug the notification interface of your Watch App."
    }
```



## <a name="related-links"></a>Vínculos relacionados

- [WatchNotifications (notificaciones locales) (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-watchnotifications)
- [WatchKitCatalog (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [La documentación de Apple Watch Kit notificaciones](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)
