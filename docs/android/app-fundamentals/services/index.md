---
title: Creación de servicios de Android
description: En esta guía se describen los servicios de Xamarin. Android, que son componentes de Android que permiten realizar el trabajo sin una interfaz de usuario activa. Los servicios se utilizan muy comúnmente para las tareas que se realizan en segundo plano, como cálculos lentos, descarga de archivos, reproducción de música, etc. Explica los diferentes escenarios para los que los servicios son adecuados y muestra cómo implementarlos para realizar tareas en segundo plano de ejecución prolongada, así como para proporcionar una interfaz para llamadas a procedimientos remotos.
ms.prod: xamarin
ms.assetid: BA371A59-6F7A-F62A-02FC-28253504ACC9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/19/2018
ms.openlocfilehash: b427bbadc72ca557a37c652e853674fdf849c2c8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024556"
---
# <a name="creating-android-services"></a>Creación de servicios de Android

_En esta guía se describen los servicios de Xamarin. Android, que son componentes de Android que permiten realizar el trabajo sin una interfaz de usuario activa. Los servicios se utilizan muy comúnmente para las tareas que se realizan en segundo plano, como cálculos lentos, descarga de archivos, reproducción de música, etc. Explica los diferentes escenarios para los que los servicios son adecuados y muestra cómo implementarlos para realizar tareas en segundo plano de ejecución prolongada, así como para proporcionar una interfaz para llamadas a procedimientos remotos._

## <a name="android-services-overview"></a>Información general de servicios Android

Las aplicaciones móviles no son similares a las aplicaciones de escritorio. Los equipos de escritorio tienen cantidades Grandess de recursos, como el estado real de la pantalla, la memoria, el espacio de almacenamiento y una fuente de alimentación conectada, los dispositivos móviles no. Estas restricciones obligan a las aplicaciones móviles a comportarse de manera diferente. Por ejemplo, la pantalla pequeña de un dispositivo móvil significa normalmente que solo una aplicación (es decir, actividad) está visible a la vez. Otras actividades se mueven al fondo y se insertan en un estado suspendido en el que no pueden realizar ningún trabajo. Sin embargo, solo porque una aplicación Android está en segundo plano, no significa que no sea posible que la aplicación siga funcionando. 

Las aplicaciones de Android se componen de al menos uno de los cuatro componentes principales siguientes: _actividades_, _receptores de difusión_, _proveedores de contenido_y _servicios_. Las actividades son la piedra angular de muchas aplicaciones Android excelentes porque proporcionan la interfaz de usuario que permite a los usuarios interactuar con la aplicación. Sin embargo, cuando se trata de realizar trabajos simultáneos o en segundo plano, las actividades no siempre son la mejor opción.

El mecanismo principal para el trabajo en segundo plano en Android es el _servicio_. Un servicio Android es un componente diseñado para realizar algún trabajo sin una interfaz de usuario. Un servicio puede descargar un archivo, reproducir música o aplicar un filtro a una imagen. Los servicios también se pueden usar para la comunicación entre procesos (_IPC_) entre aplicaciones Android. Por ejemplo, una aplicación Android podría usar el servicio de reproducción de música que procede de otra aplicación o una aplicación puede exponer datos (como la información de contacto de una persona) a otras aplicaciones a través de un servicio. 

Los servicios y su capacidad para realizar el trabajo en segundo plano son cruciales para proporcionar una interfaz de usuario fluida y fluida. Todas las aplicaciones de Android tienen un _subproceso principal_ (también conocido como _subproceso de interfaz de usuario_) en el que se ejecutan las actividades. Para mantener la capacidad de respuesta del dispositivo, Android debe ser capaz de actualizar la interfaz de usuario a la velocidad de 60 fotogramas por segundo. Si una aplicación Android realiza demasiado trabajo en el subproceso principal, Android quitará los fotogramas, lo que a su vez hace que la interfaz de usuario parezca irregular (también denominada _Janky_). Esto significa que cualquier trabajo realizado en el subproceso de la interfaz de usuario se debe completar en el intervalo de tiempo entre dos fotogramas, aproximadamente 16 milisegundos (1 segundo cada 60 fotogramas). 

Para solucionar este problema, un desarrollador puede usar subprocesos en una actividad para realizar algún trabajo que bloquee la interfaz de usuario. Sin embargo, esto podría causar problemas. Es muy posible que Android destruya y vuelva a crear varias instancias de la actividad. Sin embargo, Android no destruirá automáticamente los subprocesos, lo que podría dar lugar a pérdidas de memoria. Un ejemplo primo es cuando el [dispositivo se gira](~/android/app-fundamentals/handling-rotation.md) &ndash; Android intentará destruir la instancia de la actividad y, a continuación, volverá a crear una nueva:

![Cuando el dispositivo gira, la instancia 1 se destruye y se crea la instancia 2](images/image-01.png)

Se trata de una posible fuga de memoria &ndash; el subproceso creado por la primera instancia de la actividad se seguirá ejecutando. Si el subproceso tiene una referencia a la primera instancia de la actividad, esto impedirá que Android recopile el objeto. Sin embargo, la segunda instancia de la actividad todavía se crea (lo que a su vez podría crear un nuevo subproceso). La rotación del dispositivo varias veces en una sucesión rápida puede agotar toda la RAM y forzar que Android termine toda la aplicación para recuperar memoria.

Como regla general, si el trabajo que se va a realizar debe durar más una actividad, se debe crear un servicio para realizar ese trabajo. Sin embargo, si el trabajo solo es aplicable en el contexto de una actividad, la creación de un subproceso para realizar el trabajo podría ser más adecuada. Por ejemplo, la creación de una miniatura para una foto que se acaba de agregar a una aplicación de la Galería fotográfica debería producirse probablemente en un servicio. Sin embargo, es posible que un subproceso sea más adecuado para reproducir música que solo debería escucharse mientras una actividad está en primer plano.

El trabajo en segundo plano puede dividirse en dos clasificaciones amplias:

1. **Tarea de larga ejecución** &ndash; este es el trabajo que está en curso hasta que se detiene explícitamente. Un ejemplo de una _tarea de ejecución prolongada_ es una aplicación que transmite música o que debe supervisar los datos recopilados de un sensor. Estas tareas deben ejecutarse aunque la aplicación no tenga ninguna interfaz de usuario visible.
2. **Tareas periódicas** &ndash; (a veces conocido como _trabajo_) una tarea periódica es aquella de duración relativamente corta (varios segundos) y se ejecuta según una programación (es decir, una vez al día durante una semana o quizás una sola vez en los próximos 60 segundos). Un ejemplo de esto es descargar un archivo de Internet o generar una miniatura para una imagen.

Hay cuatro tipos diferentes de servicios Android:

* **Servicio enlazado** &ndash; un _servicio enlazado_ es un servicio que tiene algún otro componente (normalmente una actividad) enlazado a él. Un servicio enlazado proporciona una interfaz que permite que el componente enlazado y el servicio interactúen entre sí. Una vez que no haya más clientes enlazados al servicio, Android cerrará el servicio. 

* **`IntentService`** &ndash; un _`IntentService`_ es una subclase especializada de la clase `Service` que simplifica la creación y el uso del servicio. Una `IntentService` está pensada para controlar llamadas autónomas individuales. A diferencia de un servicio, que puede controlar simultáneamente varias llamadas, una `IntentService` es más similar a un _procesador de cola de trabajo_ &ndash; trabajo se pone en cola y un `IntentService` procesa cada trabajo de uno en uno en un único subproceso de trabajo. Normalmente, una`IntentService` no está enlazada a una actividad o un fragmento. 

* **Servicio iniciado** &ndash; un _servicio iniciado_ es un servicio que ha iniciado algún otro componente de Android (como una actividad) y se ejecuta continuamente en segundo plano hasta que algo indica explícitamente que se detenga el servicio. A diferencia de un servicio enlazado, un servicio iniciado no tiene ningún cliente enlazado directamente a él. Por esta razón, es importante diseñar los servicios iniciados para que se puedan reiniciar correctamente según sea necesario.

* **Servicio híbrido** &ndash; un _servicio híbrido_ es un servicio que tiene las características de un _servicio iniciado_ y un _servicio enlazado_. Un servicio híbrido puede iniciarse cuando un componente se enlaza a él o se puede iniciar por algún evento. Un componente de cliente puede estar enlazado o no al servicio híbrido. Un servicio híbrido mantendrá en ejecución hasta que se le indique explícitamente que se detenga o hasta que no haya más clientes enlazados a él.

El tipo de servicio que se va a usar depende de los requisitos de la aplicación. Como regla general, un `IntentService` o un servicio enlazado son suficientes para la mayoría de las tareas que debe realizar una aplicación Android, por lo que se debe proporcionar preferencia a uno de esos dos tipos de servicios. Una `IntentService` es una buena opción para las tareas de "One-Shot", como la descarga de un archivo, mientras que un servicio enlazado sería adecuado cuando se requiere interacciones frecuentes con una actividad o un fragmento. 

Aunque la mayoría de los servicios se ejecutan en segundo plano, hay una subcategoría especial conocida como _servicio en primer plano_. Se trata de un servicio al que se le asigna una prioridad más alta (en comparación con un servicio normal) para realizar algún trabajo para el usuario (por ejemplo, reproducir música). 

También es posible ejecutar un servicio en su propio proceso en el mismo dispositivo, lo que a veces se conoce como _servicio remoto_ o como _servicio fuera de proceso_. Esto requiere más esfuerzo para crear, pero puede ser útil cuando una aplicación necesita compartir funcionalidad con otras aplicaciones y, en algunos casos, mejorar la experiencia del usuario de una aplicación. 

### <a name="background-execution-limits-in-android-80"></a>Límites de ejecución en segundo plano en Android 8,0

A partir de Android 8,0 (nivel de API 26), una aplicación de Android ya no tiene la capacidad de ejecutarse libremente en segundo plano. Cuando está en primer plano, una aplicación puede iniciar y ejecutar servicios sin restricciones. Cuando una aplicación se mueve al fondo, Android concederá a la aplicación una cantidad de tiempo determinada para iniciar y usar los servicios. Una vez transcurrido ese tiempo, la aplicación ya no puede iniciar ningún servicio y los servicios que se iniciaron se terminarán. En este momento, la aplicación no puede realizar ningún trabajo. Android considera que una aplicación está en primer plano si se cumple alguna de las siguientes condiciones:

* Hay una actividad visible (ya sea iniciada o en pausa).
* La aplicación ha iniciado un servicio en primer plano.
* Otra aplicación está en primer plano y usa componentes de una aplicación que, en caso contrario, estarían en segundo plano. Un ejemplo de esto es si la aplicación A, que está en primer plano, se enlaza a un servicio proporcionado por la aplicación B. la aplicación B también se considerará en primer plano y no finalizará con Android para estar en segundo plano.

Hay algunas situaciones en las que, aunque una aplicación está en segundo plano, Android reactivará la aplicación y relajará estas restricciones durante unos minutos, lo que permite que la aplicación realice algún trabajo:

* La aplicación recibe un mensaje de nube Firebase de alta prioridad.
* La aplicación recibe una difusión. 
* La aplicación recibe y ejecuta un `PendingIntent` en respuesta a una notificación.

Es posible que las aplicaciones de Xamarin. Android existentes tengan que cambiar el modo en que realizan el trabajo en segundo plano para evitar los problemas que puedan surgir en Android 8,0. Estas son algunas alternativas prácticas para un servicio Android:

* **Programar el trabajo para que se ejecute en segundo plano mediante el programador de trabajos de Android o el [distribuidor de trabajos de Firebase](~/android/platform/firebase-job-dispatcher.md)**  &ndash; estas dos bibliotecas proporcionan un marco para que las aplicaciones segregan el trabajo en segundo plano en los _trabajos_, una unidad de trabajo discreta. Después, las aplicaciones pueden programar el trabajo con el sistema operativo junto con algunos criterios sobre cuándo se puede ejecutar el trabajo.
* **Iniciar el servicio en primer plano** &ndash; un servicio en primer plano resulta útil cuando la aplicación debe realizar alguna tarea en segundo plano y el usuario puede tener que interactuar periódicamente con esa tarea. El servicio de primer plano mostrará una notificación persistente para que el usuario sea consciente de que la aplicación está ejecutando una tarea en segundo plano y también proporciona una forma de supervisar la tarea o interactuar con ella. Un ejemplo de esto sería una aplicación de podcast que reproduce un podcast al usuario o quizás descarga un episodio de podcast para que se pueda disfrutar más adelante. 
* **Use un mensaje Firebase Cloud Message (FCM &ndash;) de alta prioridad** cuando Android reciba un FCM de alta prioridad para una aplicación, permitirá que esa aplicación ejecute servicios en segundo plano durante un breve período de tiempo. Esto sería una buena alternativa a tener un servicio en segundo plano que sondea una aplicación en segundo plano. 
* **Aplazar el trabajo cuando la aplicación entra en el primer plano** &ndash; si ninguna de las soluciones anteriores es viable, las aplicaciones deben desarrollar su propia forma de pausar y reanudar el trabajo cuando la aplicación llega al primer plano.

## <a name="related-links"></a>Vínculos relacionados

* [Límites de ejecución en segundo plano de Android Oreo](https://www.youtube.com/watch?v=Pumf_4yjTMc)
