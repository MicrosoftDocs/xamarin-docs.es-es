---
title: Actualización de una aplicación de Xamarin. iOS en segundo plano
description: En este documento se describen varias formas de actualizar una aplicación de Xamarin. iOS que se encuentra en segundo plano, como la supervisión de regiones, la captura en segundo plano y las notificaciones remotas.
ms.prod: xamarin
ms.assetid: A2B2231A-C045-4C11-8176-F9966485197A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: 2e0bb4fc0468f938e7a4403513fe101db2282561
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286988"
---
# <a name="updating-a-xamarinios-app-in-the-background"></a>Actualización de una aplicación de Xamarin. iOS en segundo plano

La actualización en segundo plano es el proceso de activar una aplicación que está suspendida o no y actualizarla con el nuevo contenido. iOS proporciona tres opciones para actualizar el contenido en segundo plano:

1. *Supervisión de regiones* y *cambios de ubicación significativos* las API que tienen en cuenta la ubicación del servicio desencadenan actualizaciones en segundo plano en función de los cambios en la ubicación del usuario. Estas API se pueden usar con prudencia para actualizar el contenido en aplicaciones de iOS 6 no basadas en ubicación, donde otras opciones no están disponibles.
1. *Captura en segundo plano (iOS 7 +)* : un enfoque temporal para actualizar contenido *no crítico* que se actualiza *con frecuencia* .
1. *Notificaciones remotas (iOS 7 +)* : las aplicaciones que reciben notificaciones de envío pueden usar las notificaciones para desencadenar actualizaciones de contenido en segundo plano. Este método se puede usar para actualizar con contenido *importante que depende del tiempo* y que se actualiza *esporádicamente* .


En las secciones siguientes se tratan los conceptos básicos de estas opciones.

## <a name="region-monitoring-and-significant-location-changes"></a>Supervisión de regiones y cambios de ubicación significativos

iOS proporciona dos API que tienen en cuenta la ubicación con capacidades de fondo:

1. La *supervisión* de regiones es el proceso de configuración de regiones con límites y la activación del dispositivo cuando el usuario entra o sale de una región. Las regiones son circulares y pueden ser de tamaño variable. Cuando el usuario cruza un límite de región, el dispositivo se activa para controlar el evento, normalmente desencadenando una notificación o iniciando una tarea. La supervisión de regiones requiere GPS y aumenta el uso de la batería y los datos.
1. El *servicio de cambios de ubicación significativos* es una opción más sencilla y de ahorro de energía disponible para dispositivos con radio de telefonía móvil. Una aplicación que escucha los cambios de ubicación significativos se le notificará cuando el dispositivo cambie las torres de celda. Este servicio se puede usar para reactivar una aplicación suspendida o finalizada y proporciona una oportunidad para comprobar si hay contenido nuevo en segundo plano. La actividad en segundo plano se limita a unos 10 segundos, a menos que se empareje con una [tarea en segundo plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md) .


Una aplicación no necesita la ubicación `UIBackgroundMode` para usar estas API con reconocimiento de ubicación. Dado que iOS no realiza un seguimiento de los tipos de tareas que se pueden ejecutar cuando el dispositivo se reactivarán por los cambios en la ubicación del usuario, estas API proporcionan una solución alternativa para actualizar el contenido en segundo plano en iOS 6. *Tenga en cuenta que la activación de actualizaciones en segundo plano con API basadas en la ubicación se basará en los recursos del dispositivo y puede confundir a los usuarios que no entienden por qué una aplicación requiere acceso a su ubicación*. Use discreción al implementar la supervisión de regiones o cambios de ubicación significativos para el procesamiento en segundo plano en aplicaciones que aún no usan las API de ubicación.

Las aplicaciones que usan la supervisión de ubicación para el procesamiento en segundo plano exponen un error en iOS 6: si las necesidades de una aplicación no caben en una categoría necesaria para el fondo, tienen opciones de fondo limitadas. Con la introducción de dos nuevas API, la *captura en segundo plano* y las *notificaciones remotas*, iOS 7 (y versiones posteriores) proporciona oportunidades de fondo a más aplicaciones. En las dos secciones siguientes se presentan estas nuevas API.

<a name="background_fetch" />

## <a name="background-fetch-ios-7-and-greater"></a>Captura en segundo plano (iOS 7 y versiones posteriores)

En iOS 6, una aplicación que escribe el tiempo de primer plano necesario para cargar contenido nuevo, presenta brevemente a los usuarios el contenido que ya han visto. La captura en segundo plano permite a las aplicaciones cargar datos nuevos *antes* de que un usuario inicie la aplicación y proporcionar al usuario el contenido más actualizado.

Para implementar la captura en segundo plano, edite *info. plist* y active las casillas **Habilitar modos en segundo plano** y **captura en segundo plano** :

 [![](updating-an-application-in-the-background-images/fetch.png "Edite el archivo info. plist y active las casillas habilitar modos en segundo plano y captura en segundo plano")](updating-an-application-in-the-background-images/fetch.png#lightbox)

A continuación, en `AppDelegate`el, invalide el `FinishedLaunching` método para establecer el intervalo de captura mínimo. En este ejemplo, se permite que el sistema operativo decida con qué frecuencia se debe capturar el contenido nuevo:

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
  UIApplication.SharedApplication.SetMinimumBackgroundFetchInterval (UIApplication.BackgroundFetchIntervalMinimum);
  return true;
}
```

Por último, realice la búsqueda invalidando `PerformFetch` el método `AppDelegate`en y pasando un controlador de *finalización*. El controlador de finalización es un delegado que `UIBackgroundFetchResult`toma un:

```csharp
public override void PerformFetch (UIApplication application, Action<UIBackgroundFetchResult> completionHandler)
{
  // Check for new data, and display it
  ...
  
  // Inform system of fetch results
  completionHandler (UIBackgroundFetchResult.NewData);
}
```

Cuando terminemos de actualizar el contenido, permitiremos que el sistema operativo lo sepa llamando al controlador de finalización con el estado adecuado. iOS ofrece tres opciones para el estado del controlador de finalización:

1. `UIBackgroundFetchResult.NewData`-Se le llama cuando se ha capturado contenido nuevo y se ha actualizado la aplicación.
1. `UIBackgroundFetchResult.NoData`-Se le llama cuando se ha producido la captura del nuevo contenido, pero no hay contenido disponible.
1. `UIBackgroundFetchResult.Failed`-Útil para el control de errores, al que se llama cuando la captura no pudo pasar.


Las aplicaciones que usan la captura en segundo plano pueden realizar llamadas para actualizar la interfaz de usuario desde el fondo. Cuando el usuario abre la aplicación, esta se actualiza y muestra el contenido nuevo. También se actualizará la instantánea del conmutador de aplicaciones de la aplicación, de modo que el usuario pueda ver cuándo tiene contenido nuevo.

> [!IMPORTANT]
> Una `PerformFetch` vez que se llama a, la aplicación tiene aproximadamente 30 segundos para iniciar la descarga del nuevo contenido y llamar al bloque del controlador de finalización. Si esto tarda demasiado, se terminará la aplicación. Considere la posibilidad de usar la captura en segundo plano con el _servicio de transferencia en segundo plano_ al descargar medios u otros archivos grandes.


### <a name="backgroundfetchinterval"></a>BackgroundFetchInterval

En el código de ejemplo anterior, se permite que el sistema operativo decida con qué frecuencia se debe capturar el nuevo contenido estableciendo `BackgroundFetchIntervalMinimum`el intervalo de captura mínimo en. iOS ofrece tres opciones para el intervalo de captura:

1. `BackgroundFetchIntervalNever`-Indique al sistema que no recupere nunca el nuevo contenido. Use esta opción para desactivar la captura en determinadas situaciones, por ejemplo, cuando el usuario no ha iniciado sesión. Este es el valor predeterminado para el intervalo de captura. 
1. `BackgroundFetchIntervalMinimum`: Permita que el sistema decida con qué frecuencia se realiza la captura en función de los patrones de usuario, la duración de la batería, el uso de los datos y las necesidades de otras aplicaciones.
1. `BackgroundFetchIntervalCustom`-Si sabe con qué frecuencia se actualiza el contenido de una aplicación, puede especificar un intervalo de "suspensión" después de cada captura, durante el cual la aplicación no podrá obtener contenido nuevo. Una vez que ese intervalo está activo, el sistema determinará cuándo se debe capturar el contenido.


`BackgroundFetchIntervalMinimum` Y`BackgroundFetchIntervalCustom` dependen del sistema para programar las capturas. Este intervalo es dinámico, lo que se adapta a las necesidades del dispositivo, así como a los hábitos del usuario individual. Por ejemplo, si un usuario comprueba una aplicación cada mañana y otro comprueba cada hora, iOS garantizará que el contenido esté actualizado para ambos usuarios cada vez que abra la aplicación.

La captura en segundo plano debe usarse para las aplicaciones que se actualizan con frecuencia con contenido no crítico. En el caso de las aplicaciones con actualizaciones críticas, deben usarse notificaciones remotas. Las notificaciones remotas se basan en la captura en segundo plano y comparten el mismo controlador de finalización. A continuación, profundizaremos en las notificaciones remotas.

 <a name="remote_notifications" />


## <a name="remote-notifications-ios-7-and-greater"></a>Notificaciones remotas (iOS 7 y versiones posteriores)

Las notificaciones push son mensajes JSON enviados desde un proveedor a un dispositivo mediante el *servicio de notificaciones push de Apple (APNs)* .

En iOS 6, las notificaciones de entrada entrantes indican al sistema que avise al usuario de que se ha producido algo interesante en una aplicación. Al hacer clic en la notificación, se extrae la aplicación del estado suspendido o terminado y la aplicación comenzaría a actualizar el contenido. iOS 7 (y versiones posteriores) amplía las notificaciones de entrega normales al dar a las aplicaciones una oportunidad para actualizar el contenido en segundo plano *antes* de notificar al usuario, de modo que el usuario pueda abrir la aplicación y presentar el nuevo contenido de forma inmediata.

Para implementar notificaciones remotas, edite *info. plist* y active las casillas **Habilitar modos en segundo plano** y **notificaciones remotas** :

 [![](updating-an-application-in-the-background-images/remote.png "Modo en segundo plano establecido para habilitar los modos en segundo plano y las notificaciones remotas")](updating-an-application-in-the-background-images/remote.png#lightbox)

A continuación, establezca `content-available` la marca en la notificación de la extracción en 1. Esto permite que la aplicación sepa capturar contenido nuevo antes de mostrar la alerta:

```csharp
'aps' {
  'content-available': 1,
  'alert': 'Something new has happened in your app!''
}
```

En *AppDelegate*, invalide `DidReceiveRemoteNotification` el método para comprobar la carga de notificación del contenido disponible y llamar al bloque del controlador de finalización adecuado:

```csharp
public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
{
  if([content-available]) {
    // fetch content
    completionHandler (UIBackgroundFetchResult.NewData);
  }
}
```

Las notificaciones remotas deben usarse para las actualizaciones poco frecuentes con contenido que es fundamental para la funcionalidad de la aplicación. Para obtener más información sobre las notificaciones remotas, consulte la guía de las [notificaciones de envío de Xamarin en iOS](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md) .

> [!IMPORTANT]
> Dado que el mecanismo de actualización en las notificaciones remotas se basa en la captura en segundo plano, la aplicación debe iniciar la descarga del nuevo contenido y llamar al bloque del controlador de finalización en un plazo de 30 segundos a la recepción de la notificación, o iOS finalizará la aplicación. Considere la posibilidad de emparejar notificaciones remotas con el _servicio de transferencia en segundo plano_ al descargar medios u otros archivos de gran tamaño en segundo plano.


### <a name="silent-remote-notifications"></a>Notificaciones remotas silenciosas

Las notificaciones remotas son una forma sencilla de notificar a las aplicaciones de las actualizaciones y de iniciar la captura de contenido nuevo, pero hay casos en los que no es necesario notificar al usuario que algo ha cambiado. Por ejemplo, si un usuario marca un archivo para la sincronización, no es necesario notificárselo cada vez que se actualiza el archivo. La sincronización de archivos no es un evento sorprendente, ni requiere la atención inmediata del usuario. Los usuarios solo esperan que el archivo esté actualizado cuando lo abran.

En el caso de los casos como el anterior, iOS permite que las notificaciones de extracción se envíen silenciosamente, es decir, sin una alerta. Para convertir una notificación normal en silenciosa, solo tiene que quitar la alerta de la carga de notificación:

```csharp
'aps' {
  'content-available': 1
}
```

#### <a name="rate-limits"></a>Límites de velocidad

La diferencia más importante entre las notificaciones normal y silenciosa desde la perspectiva del desarrollador es que las inserciones silenciosas están limitadas por la velocidad. APNs retrasará la entrega de inserciones silenciosas al dispositivo si la velocidad de envío es demasiado alta. Esto es para asegurarse de que las aplicaciones no agoten los recursos de dispositivo con demasiadas notificaciones silenciosas.

Sin embargo, APNs permitirá que las notificaciones silenciosas "se superpongan" junto con una respuesta de notificación remota normal o de mantenimiento de conexión. Dado que las notificaciones normales no tienen una velocidad limitada, se pueden usar para enviar notificaciones silenciosas almacenadas desde el APN al dispositivo, tal como se muestra en el diagrama siguiente:

 [![](updating-an-application-in-the-background-images/silent.png "Las notificaciones normales se pueden usar para enviar notificaciones silenciosas almacenadas desde el APN al dispositivo, tal como se muestra en este diagrama.")](updating-an-application-in-the-background-images/silent.png#lightbox)

> [!IMPORTANT]
> Apple anima a los desarrolladores a que envíen notificaciones push silenciosas cada vez que la aplicación lo requiera y permiten que APNs Programe su entrega.


En esta sección, hemos tratado las diversas opciones para actualizar el contenido en segundo plano para ejecutar tareas que no se ajustan a una categoría necesaria para el fondo. Ahora, vamos a ver algunas de estas API en acción.

 [Siguiente: Parte 4: tutoriales de fondo de iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
