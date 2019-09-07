---
title: Procesamiento en segundo plano de iOS con tareas
description: En este documento se describe cómo usar las tareas en segundo plano para realizar tareas de ejecución prolongada después de que una aplicación se coloque en segundo plano.
ms.prod: xamarin
ms.assetid: 205D230E-C618-4D69-96EE-4B91D7819121
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: 7596f79119f28997cbcda6e7057e682edfd760b8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756358"
---
# <a name="ios-backgrounding-with-tasks"></a>Procesamiento en segundo plano de iOS con tareas

La manera más sencilla de realizar el procesamiento en iOS es dividir los requisitos de fondo en tareas y ejecutar las tareas en segundo plano. Las tareas se encuentran en un límite de tiempo estricto y normalmente obtienen unos 600 segundos (10 minutos) de tiempo de procesamiento después de que una aplicación se haya pasado al fondo en iOS 6, y de menos de 10 minutos en iOS 7 +.

Las tareas en segundo plano se pueden dividir en tres categorías:

1. **Tareas seguras en segundo plano** : se llama en cualquier parte de la aplicación en la que tenga una tarea que no desea interrumpir en caso de que la aplicación entre en el fondo.
1. **Tareas de DidEnterBackground** : se llama `DidEnterBackground` durante el método de ciclo de vida de la aplicación para ayudar en la limpieza y el guardado del estado.
1. **Transferencias en segundo plano (iOS 7 +)** : un tipo especial de tarea en segundo plano que se usa para realizar transferencias de red en iOS 7. A diferencia de las tareas normales, las transferencias en segundo plano no tienen un límite de tiempo predeterminado.

Las tareas seguras y `DidEnterBackground` en segundo plano son seguras para su uso en iOS 6 e iOS 7, con algunas pequeñas diferencias. Vamos a investigar estos dos tipos de tareas con mayor detalle.

## <a name="creating-background-safe-tasks"></a>Crear tareas seguras para el fondo

Algunas aplicaciones contienen tareas que iOS no debe interrumpir en caso de que la aplicación cambie el estado. Una manera de evitar que se interrumpan estas tareas es registrarlas con iOS como tareas de ejecución prolongada. Puede usar este patrón en cualquier parte de la aplicación en la que no quiera que se interrumpa una tarea si el usuario coloca la aplicación en segundo plano. Un excelente candidato para este patrón sería tareas como el envío de la información de registro de un nuevo usuario al servidor o la comprobación de la información de inicio de sesión.

El siguiente fragmento de código muestra cómo registrar una tarea para que se ejecute en segundo plano:

```csharp
nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});

//runs on main or background thread
FinishLongRunningTask(taskID);

UIApplication.SharedApplication.EndBackgroundTask(taskID);
```

El proceso de registro empareja una tarea con un identificador único, `taskID`y, a continuación, la encapsula en `BeginBackgroundTask` las `EndBackgroundTask` llamadas y coincidentes. Para generar el identificador, se realiza una llamada al `BeginBackgroundTask` método en el `UIApplication` objeto y, a continuación, se inicia la tarea de ejecución prolongada, normalmente en un nuevo subproceso. Una vez completada la tarea, se `EndBackgroundTask` llama a y se pasa el mismo identificador. Esto es importante porque iOS finalizará la aplicación si una `BeginBackgroundTask` llamada no tiene una coincidencia. `EndBackgroundTask`

> [!IMPORTANT]
> Las tareas seguras en segundo plano se pueden ejecutar en el subproceso principal o en un subproceso en segundo plano, dependiendo de las necesidades de la aplicación.

## <a name="performing-tasks-during-didenterbackground"></a>Realización de tareas durante DidEnterBackground

Además de hacer que una tarea de ejecución prolongada sea segura para el fondo, el registro se puede usar para iniciar tareas a medida que una aplicación se está poniendo en segundo plano. iOS proporciona un método de evento en la clase AppDelegate `DidEnterBackground` denominada que se puede usar para guardar el estado de la aplicación, guardar los datos de usuario y cifrar el contenido confidencial antes de que una aplicación entre en el fondo. Una aplicación tiene aproximadamente cinco segundos para devolver desde este método o se terminará. Por lo tanto, se puede llamar a las tareas de limpieza que pueden tardar más de cinco `DidEnterBackground` segundos en completarse desde dentro del método. Estas tareas se deben invocar en un subproceso independiente.

El proceso es casi idéntico al registro de una tarea de ejecución prolongada. En el fragmento de código siguiente se muestra esto en acción:

```csharp
public override void DidEnterBackground (UIApplication application) {
  nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});
  new Task ( () => {
    DoWork();
    UIApplication.SharedApplication.EndBackgroundTask(taskID);
  }).Start();
}
```

Comenzaremos invalidando el `DidEnterBackground` método `AppDelegate`en, donde registraremos la tarea a través `BeginBackgroundTask` de como hicimos en el ejemplo anterior. A continuación, se genera un nuevo subproceso y se realiza la tarea de ejecución prolongada. Observe que la `EndBackgroundTask` llamada se realiza ahora desde dentro de la tarea de ejecución prolongada, `DidEnterBackground` ya que el método ya se habrá devuelto.

> [!IMPORTANT]
> iOS usa un [mecanismo de guardián](https://developer.apple.com/library/ios/qa/qa1693/_index.html) para asegurarse de que la interfaz de usuario de una aplicación sigue respondiendo. Una aplicación que emplee demasiado tiempo en `DidEnterBackground` dejará de responder en la interfaz de usuario. La puesta al día de las tareas que se ejecutan en segundo plano permite `DidEnterBackground` que se devuelva oportunamente, manteniendo la interfaz de usuario en respuesta e impidiendo que el guardián salga de la aplicación.

## <a name="handling-background-task-time-limits"></a>Administrar límites de tiempo de tareas en segundo plano

iOS coloca límites estrictos sobre cuánto tiempo se puede ejecutar una tarea en segundo plano `EndBackgroundTask` y, si la llamada no se realiza en el tiempo asignado, se terminará la aplicación. Mediante el seguimiento del tiempo de fondo restante y el uso de controladores de expiración cuando sea necesario, podemos evitar que iOS finalice la aplicación.

### <a name="accessing-background-time-remaining"></a>Acceso al tiempo restante en segundo plano

Si una aplicación con tareas registradas se mueve al fondo, las tareas registradas obtendrán unos 600 segundos para ejecutarse. Podemos comprobar cuánto tiempo tiene la tarea para completarse mediante la propiedad estática `BackgroundTimeRemaining` de la `UIApplication` clase. El siguiente código nos dará el tiempo, en segundos, que la tarea en segundo plano ha dejado:

```csharp
double timeRemaining = UIApplication.SharedApplication.BackgroundTimeRemaining;
```

### <a name="avoiding-app-termination-with-expiration-handlers"></a>Evitar la terminación de la aplicación con controladores de expiración

Además de proporcionar acceso a la propiedad `BackgroundTimeRemaining` , iOS proporciona una manera estable de controlar la expiración del tiempo de fondo a través de un **controlador de expiración**. Este es un bloque opcional de código que se ejecutará cuando el tiempo asignado a una tarea esté a punto de expirar. El código del controlador de expiración llama `EndBackgroundTask` a y pasa el identificador de tarea, lo que indica que la aplicación se comporta bien y evita que Ios finalice la aplicación incluso si la tarea se ejecuta sin tiempo. `EndBackgroundTask`se debe llamar al método en el controlador de expiración, así como en el curso normal de la ejecución. 

El controlador de expiración se expresa como una función anónima mediante una expresión lambda, como se muestra a continuación:

```csharp
Task.Factory.StartNew( () => {

    //expirationHandler only called if background time allowed exceeded
    var taskId = UIApplication.SharedApplication.BeginBackgroundTask(() => {
        Console.WriteLine("Exhausted time");
        UIApplication.SharedApplication.EndBackgroundTask(taskId); 
    });
    while(myFlag == true)
    {
        Console.WriteLine(UIApplication.SharedApplication.BackgroundTimeRemaining);
        myFlag = SomeCalculationNeedsMoreTime();
    }
    //Only called if loop terminated due to myFlag and not expiration of time
    UIApplication.SharedApplication.EndBackgroundTask(taskId);
});
```

Aunque los controladores de expiración no son necesarios para que el código se ejecute, siempre debe usar un controlador de expiración con una tarea en segundo plano.

 <a name="background_tasks_in_iOS_7" />

## <a name="background-tasks-in-ios-7"></a>Tareas en segundo plano en iOS 7 +

El cambio más importante en iOS 7 con respecto a las tareas en segundo plano no es cómo se implementan las tareas, pero cuando se ejecutan.

Recuerde que pre-iOS 7, una tarea que se ejecuta en segundo plano tenía 600 segundos en completarse. Uno de los motivos de este límite es que una tarea que se ejecuta en segundo plano mantener el dispositivo activo mientras dure la tarea:

 [![](ios-backgrounding-with-tasks-images/ios6.png "Gráfico de la tarea mantener la aplicación activa antes de iOS 7")](ios-backgrounding-with-tasks-images/ios6.png#lightbox)

el procesamiento en segundo plano de iOS 7 está optimizado para una larga duración de la batería. En iOS 7, el fondo se convierte en oportunista: en lugar de mantener el dispositivo activo, las tareas en las que el dispositivo entra en suspensión y, en su lugar, realizan su procesamiento en fragmentos cuando el dispositivo se reactiva para controlar llamadas telefónicas, notificaciones, correos electrónicos entrantes y otros interrupciones comunes. En el siguiente diagrama se proporciona información sobre cómo se puede dividir una tarea:

 [![](ios-backgrounding-with-tasks-images/ios7.png "Gráfico de la tarea que se divide en fragmentos posteriores a iOS 7")](ios-backgrounding-with-tasks-images/ios7.png#lightbox)

Dado que el tiempo de ejecución de la tarea ya no es continuo, las tareas que realizan transferencias de red deben administrarse de manera diferente en iOS 7. Se recomienda a los desarrolladores que `NSURlSession` usen la API para controlar las transferencias de red. La siguiente sección es una introducción a las transferencias en segundo plano.

 <a name="background-transfers" />

## <a name="background-transfers"></a>Transferencias en segundo plano

La red troncal de las transferencias en segundo plano en `NSURLSession` iOS 7 es la nueva API. `NSURLSession`nos permite crear tareas para:

1. Transferir contenido a través de interrupciones de red y dispositivos.
1. Cargar y descargar archivos grandes ( *servicio de transferencia en segundo plano* ).

Echemos un vistazo más de cerca a cómo funciona esto.

### <a name="nsurlsession-api"></a>API de NSURLSession

 `NSURLSession`es una API eficaz para transferir contenido a través de la red. Proporciona un conjunto de herramientas para administrar la transferencia de datos a través de las interrupciones de red y los cambios en los Estados de la aplicación.

La `NSURLSession` API crea una o varias sesiones que, a su vez, generan tareas para desplazar los bloques de datos relacionados a través de la red. Las tareas se ejecutan de forma asincrónica para transferir datos de forma rápida y confiable. Dado `NSURLSession` que es asincrónico, cada sesión requiere un bloque de controlador de finalización para permitir que el sistema y la aplicación sepan cuándo se completa una transferencia.

Para realizar una transferencia de red que sea válida en las versiones anteriores a iOS 7 y posteriores a iOS 7, compruebe `NSURLSession` si un está disponible para poner en cola las transferencias y use una tarea en segundo plano normal para realizar la transferencia si no lo está:

```csharp
if ([NSURLSession class]) {
  // Create a background session and enqueue transfers
}
else {
  // Start a background task and transfer directly
  // Do NOT make calls to update the UI here!
}
```

> [!IMPORTANT]
> Evite realizar llamadas para actualizar la interfaz de usuario desde el fondo en código compatible con iOS 6, ya que iOS 6 no es compatible con las actualizaciones de la interfaz de usuario en segundo plano y finalizará la aplicación.

La `NSURLSession` API incluye un amplio conjunto de características para controlar la autenticación, administrar las transferencias con error y notificar los errores del lado cliente, pero no de los servidores. Ayuda a salvar las interrupciones en el tiempo de ejecución de la tarea introducido en iOS 7, y también proporciona compatibilidad para transferir archivos grandes de forma rápida y confiable. En la siguiente sección se explora esta segunda característica.

### <a name="background-transfer-service"></a>Servicio de transferencia en segundo plano

Antes de iOS 7, la carga o descarga de archivos en segundo plano no era confiable. Las tareas en segundo plano obtienen un tiempo limitado para ejecutarse, pero el tiempo que se tarda en transferir un archivo varía según la red y el tamaño del archivo. En iOS 7, podemos usar `NSURLSession` para cargar y descargar correctamente archivos grandes. El tipo `NSURLSession` de sesión concreto que controla las transferencias de red de archivos grandes en segundo plano se conoce como *servicio de transferencia en segundo plano*.

El sistema operativo administra las transferencias iniciadas mediante el servicio de transferencia en segundo plano y proporciona las API para controlar la autenticación y los errores. Dado que las transferencias no están enlazadas por un límite de tiempo arbitrario, se pueden usar para cargar o descargar archivos de gran tamaño, actualizar el contenido de forma automática en segundo plano, etc. Consulte el [tutorial de transferencia en segundo plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/background-transfer-walkthrough.md) para obtener más información sobre cómo implementar el servicio.

A menudo, el servicio de transferencia en segundo plano se empareja con la captura en segundo plano o las notificaciones remotas para ayudar a las aplicaciones a actualizar el contenido en segundo plano. En las dos secciones siguientes, se presenta el concepto de registro de aplicaciones completas para que se ejecuten en segundo plano en iOS 6 e iOS 7.
