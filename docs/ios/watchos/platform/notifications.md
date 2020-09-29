---
title: Notificaciones de watchos en Xamarin
description: En este documento se describe cómo trabajar con las notificaciones de watchos en Xamarin. Describe la creación de controladores de notificación, la generación de notificaciones y la prueba de notificaciones.
ms.prod: xamarin
ms.assetid: 0BC1306E-0713-4592-996E-7530CCF281E7
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: c8275811cb61aaa1a033ca414e9abd4d20ecc873
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435423"
---
# <a name="watchos-notifications-in-xamarin"></a>Notificaciones de watchos en Xamarin

Las aplicaciones de inspección pueden recibir notificaciones si la aplicación de iOS que la contiene las admite. Hay un control de notificaciones integrado, por lo que no es *necesario* agregar la compatibilidad de notificación adicional que se describe a continuación, pero si desea personalizar el comportamiento de las notificaciones y la apariencia, lea.

Consulte el documento de [notificaciones de iOS](~/ios/platform/user-notifications/deprecated/index.md) para más información sobre cómo agregar compatibilidad de notificaciones a la aplicación de iOS en la solución.

## <a name="creating-notification-controllers"></a>Crear controladores de notificación

En las notificaciones de guion gráfico, los controladores tienen un tipo especial de segue que los desencadena. Al arrastrar un nuevo controlador de la **interfaz de notificación** a un guion gráfico, se adjuntará automáticamente un segue:

![Un nuevo controlador de interfaz de notificación con un segue adjunto](notifications-images/notification-storyboard1.png)

Cuando se selecciona la segue de notificación, puede editar sus propiedades:

![La notificación segue seleccionada](notifications-images/notification-storyboard2.png)

Después de personalizar el controlador, puede ser similar a este ejemplo de WatchKitCatalog:

![Propiedades de notificación](notifications-images/notifications-segue.png)

Hay dos tipos de notificación:

- Vista estática de **aspecto breve** no desplazable definida por el sistema.

- Vista de **larga duración** desplazable, vista personalizable definida por el usuario. Se puede especificar una versión estática más sencilla y una versión dinámica más compleja.

### <a name="short-look-notification-controller"></a>Controlador de notificación de apariencia breve

La interfaz de usuario de apariencia breve consta solo del icono de la aplicación, el nombre de la aplicación y la cadena del título de la notificación.

Si el usuario no omite la notificación, el sistema cambiará automáticamente a una notificación de gran tamaño que proporciona más información.

### <a name="long-look-notification-controller"></a>Controlador de notificación de gran apariencia

El sistema operativo decide si Mostrar la vista estática o dinámica en función de una serie de factores. Debe proporcionar una interfaz estática y, opcionalmente, también puede incluir una interfaz dinámica para las notificaciones.

#### <a name="static"></a>Estático

La vista estática debe ser sencilla y rápida de mostrar.

![Vista estática](notifications-images/notification-static.png)

#### <a name="dynamic"></a>Dinámico

La vista dinámica puede mostrar más datos y proporcionar más interactividad.

![Vista dinámica](notifications-images/notification-dynamic.png)

## <a name="generating-notifications"></a>Generación de notificaciones

Las notificaciones pueden provienen de un servidor remoto o pueden generarse localmente en la aplicación iOS.

Consulte el [tutorial de notificaciones de iOS](~/ios/platform/user-notifications/deprecated/local-notifications-in-ios-walkthrough.md) para obtener un ejemplo de cómo generar notificaciones locales.

Las notificaciones locales deben tener el `AlertTitle` conjunto que se va a mostrar en el Apple Watch: la `AlertTitle` cadena se muestra en la interfaz de apariencia breve. Tanto `AlertTitle` como `AlertBody` se muestran en la lista de notificaciones, y `AlertBody` se muestra en la interfaz de búsqueda prolongada.

En esta captura de pantalla se muestra el `AlertTitle` que se muestra en la lista de notificaciones y el que se `AlertBody` muestra en la interfaz de búsqueda prolongada:

![En esta captura de pantalla se muestra el AlertTitle que se muestra en la lista de notificaciones.](notifications-images/watch-notificationslist-sml.png) ![AlertBody que se muestra en la interfaz de búsqueda prolongada](notifications-images/watch-notificationcontroller-sml.png)

## <a name="testing-notifications"></a>Probar notificaciones

Las notificaciones (tanto locales como remotas) solo se pueden probar correctamente en un dispositivo, pero se pueden simular mediante un archivo **. JSON** en el simulador de iOS.

### <a name="testing-on-apple-watch"></a>Pruebas en Apple Watch

Al probar las notificaciones en un Apple Watch, recuerde que la [documentación de Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html) indica lo siguiente:

> Cuando llega una de las notificaciones locales o remotas de la aplicación en el iPhone del usuario, iOS decide si Mostrar esa notificación en el iPhone o en el Apple Watch.

Esto se alluding al hecho de que iOS decida si aparecerá una notificación en el iPhone o en el reloj. Si el iPhone emparejado está activo cuando se recibe una notificación, es probable que la notificación se muestre en el iPhone y *no* se enrute al reloj.

Para asegurarse de que la notificación aparece en el reloj, desactive la pantalla del iPhone (Presione el botón de encendido una vez) o deje que pase a suspensión. Si el reloj emparejado está dentro del alcance, tiene energía y se está gastando en su muñeca, la notificación se enrutará allí y aparecerá en el reloj (acompañada de una sutil).

### <a name="testing-on-the-ios-simulator"></a>Pruebas en el simulador de iOS

*Debe* proporcionar una carga de JSON de prueba al probar el modo de notificación en el simulador de iOS. Establezca la ruta de acceso en la ventana **argumentos de ejecución personalizados** en Visual Studio para Mac.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Visual Studio para Mac mostrará opciones adicionales cuando se establezca una extensión de inspección como **proyecto de inicio**.
Haga clic con el botón derecho en el proyecto de extensión de inspección y elija **ejecutar con > parámetros personalizados...**:

[![Ejecutar con propiedades personalizadas](notifications-images/runwith-customparams-sml.png)](notifications-images/runwith-customparams.png#lightbox)

Se abrirá la ventana **argumentos de ejecución** que contiene una pestaña **WatchKit** . Seleccione **notificación** y proporcione una carga JSON y, a continuación, presione **Ejecutar** para iniciar la aplicación de inspección en el simulador:

[![Seleccionar carga de notificación predeterminada](notifications-images/runwith-execargs-sml.png)](notifications-images/runwith-execargs.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para establecer la carga de la notificación de prueba en Visual Studio, haga clic con el botón derecho en la extensión de inspección para editar las **propiedades del proyecto**. Vaya a la sección **depurar** y seleccione un archivo JSON de notificaciones de la lista (enumerará automáticamente todos los archivos JSON incluidos en el proyecto).

[![Seleccionar un archivo JSON de notificaciones](notifications-images/runwith-execargs-sml-vs.png)](notifications-images/runwith-execargs-vs.png#lightbox)

Cuando la extensión de inspección es el **proyecto de inicio**, Visual Studio mostrará opciones adicionales, como se muestra a continuación. Elija una de las opciones de **notificación** para iniciar la aplicación de inspección en modo de **notificación** (mediante el archivo JSON seleccionado en la ventana Propiedades):

![Menú del dispositivo](notifications-images/runwith-vs.png)

-----

El controlador de notificación predeterminado tiene este aspecto cuando se prueba en el simulador con el archivo JSON de carga útil predeterminado:

![Una notificación de ejemplo](notifications-images/notification-debug-sml.png)

También es posible usar la línea de [comandos](~/ios/watchos/troubleshooting.md#command_line) para iniciar el simulador de iOS.

### <a name="example-notification-payload"></a>Ejemplo de carga de notificación

En el ejemplo de [Catálogo del kit de inspección](/samples/xamarin/ios-samples/watchos-watchkitcatalog) hay un archivo JSON ** de carga de ejemploNotificationPayload.jsen** (que se enumeran a continuación).

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

- [WatchKitCatalog (ejemplo)](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Documentos de notificaciones del kit de inspección de Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)