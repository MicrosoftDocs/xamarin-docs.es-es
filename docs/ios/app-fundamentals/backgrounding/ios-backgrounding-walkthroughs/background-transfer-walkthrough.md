---
title: Transferencia en segundo plano y NSURLSession en Xamarin. iOS
description: En este documento se proporciona un tutorial que muestra cómo usar la transferencia en segundo plano y NSUrlSession para iniciar la descarga de una imagen grande y continuar la descarga cuando la aplicación se coloca en segundo plano.
ms.prod: xamarin
ms.assetid: 6960E025-3D5C-457A-B893-25B734F8626D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/02/2020
ms.openlocfilehash: b95b78ed36fcec122006cab7ce9663c43f9b3096
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436661"
---
# <a name="background-transfer-and-nsurlsession-in-xamarinios"></a>Transferencia en segundo plano y NSURLSession en Xamarin. iOS

Una transferencia en segundo plano se inicia mediante la configuración de un fondo `NSURLSession` y la puesta en cola de las tareas de carga o descarga. Si las tareas se completan mientras la aplicación se encuentra en el fondo, se suspende o se finaliza, iOS enviará una notificación a la aplicación mediante una llamada al controlador de finalización en el *AppDelegate*de la aplicación. En el diagrama siguiente se muestra esto en acción:

 [![Una transferencia en segundo plano se inicia mediante la configuración de un NSURLSession de fondo y la puesta en cola de las tareas de carga o descarga.](background-transfer-walkthrough-images/transfer.png)](background-transfer-walkthrough-images/transfer.png#lightbox)

## <a name="configuring-a-background-session"></a>Configurar una sesión en segundo plano

Para crear una sesión en segundo plano, cree una nueva `NSUrlSession` y configúrela mediante un `NSUrlSessionConfiguration` objeto.

El objeto de configuración determina lo que puede hacer la sesión y los tipos de tareas que puede ejecutar.
Las sesiones configuradas con el `CreateBackgroundSessionConfiguration` método se ejecutarán en un proceso independiente y realizarán transferencias discrecionales (Wi-Fi) para conservar los datos y la duración de la batería.
En el ejemplo de código siguiente se muestra la configuración apropiada de una sesión de transferencia en segundo plano mediante el `CreateBackgroundSessionConfiguration` método y un identificador de cadena único:

```csharp
public partial class SimpleBackgroundTransferViewController : UIViewController
{
  NSUrlSession session = null;

  NSUrlSessionConfiguration configuration =
      NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.SimpleBackgroundTransfer.BackgroundSession");
  session = NSUrlSession.FromConfiguration
      (configuration, new MySessionDelegate(), new NSOperationQueue());

}
```

Además de un objeto de configuración, una sesión también requiere un delegado de sesión y una cola.
La cola determina el orden en que se completarán las tareas. El delegado de la sesión chaperones el proceso de transferencia y controla la autenticación, el almacenamiento en caché y otros problemas relacionados con la sesión.

## <a name="working-with-tasks-and-delegates"></a>Trabajar con tareas y delegados

Ahora que hemos configurado una sesión en segundo plano, vamos a comenzar las tareas para controlar la transferencia. Podemos realizar un seguimiento de estas tareas mediante el uso de una `NSUrlSessionDelegate` instancia denominada delegado de sesión. El delegado de la sesión es responsable de activar una aplicación finalizada o suspendida en segundo plano para controlar la autenticación, los errores o la finalización de la transferencia.

Un `NSUrlSessionDelegate` proporciona los siguientes métodos básicos para comprobar el estado de la transferencia:

- *DidFinishEventsForBackgroundSession* : se llama a este método cuando finalizan todas las tareas y se completa la transferencia.
- *DidReceiveChallenge* : se le llama para solicitar credenciales cuando se requiere autorización.
- *DidBecomeInvalidWithError* : se llama a este método si  `NSURLSession` se invalida.

Las sesiones en segundo plano requieren delegados más especializados en función de los tipos de tareas que se están ejecutando. Las sesiones en segundo plano se limitan a dos tipos de tareas:

- *Tareas de carga* : las tareas de tipo  `NSUrlSessionUploadTask` usan la `INSUrlSessionTaskDelegate` interfaz, que implementa `INSUrlSessionDelegate` . Esto proporciona métodos adicionales para realizar el seguimiento del progreso de la carga, controlar el redireccionamiento HTTP y mucho más.
- *Tareas de descarga* : tareas de tipo  `NSUrlSessionDownloadTask` use la `INSUrlSessionDownloadDelegate` interfaz, que implementa `INSUrlSessionDelegate` y `INSUrlSessionTaskDelegate` . Esto proporciona todos los métodos para las tareas de carga, así como métodos específicos de descarga para realizar el seguimiento del progreso de la descarga y determinar cuándo se ha reanudado o completado una tarea de descarga.

En el código siguiente se define una tarea que se puede usar para descargar una imagen de una dirección URL. La tarea se inicia llamando a `CreateDownloadTask` en la sesión en segundo plano y pasando la solicitud de la dirección URL:

```csharp
const string DownloadURLString = "http://xamarin.com/images/xamarin.png"; // or other hosted file
public NSUrlSessionDownloadTask downloadTask;

NSUrl downloadURL = NSUrl.FromString (DownloadURLString);
NSUrlRequest request = NSUrlRequest.FromUrl (downloadURL);
downloadTask = session.CreateDownloadTask (request);
```

A continuación, cree un nuevo delegado de descarga de sesión para realizar un seguimiento de todas las tareas de descarga en esta sesión. La clase delegada debe heredar de `NSObject` e implementar la interfaz necesaria:

```csharp
public class MySessionDelegate : NSObject, INSUrlSessionDownloadDelegate
{
  public void DidWriteData (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, long bytesWritten, long totalBytesWritten, long totalBytesExpectedToWrite)
  {
    Console.WriteLine (string.Format ("DownloadTask: {0}  progress: {1}", downloadTask, progress));
    InvokeOnMainThread( () => {
      // update UI with progress bar, if desired
    });
  }
  ...
}
```

Para averiguar el progreso de una tarea de descarga, invalide el `DidWriteData` método para realizar un seguimiento del progreso e, incluso, actualice la interfaz de usuario. Las actualizaciones de la interfaz de usuario aparecerán inmediatamente si la aplicación está en primer plano, o bien esperarán al usuario la próxima vez que abran la aplicación.

La API de delegado de sesión proporciona un amplio kit de herramientas para interactuar con las tareas. Para obtener una lista completa de los métodos de delegado de sesión, consulte la documentación de la `NSUrlSessionDelegate` API.

> [!IMPORTANT]
> Las sesiones en segundo plano se inician en un subproceso en segundo plano, por lo que cualquier llamada para actualizar la interfaz de usuario se debe ejecutar explícitamente en el subproceso de la interfaz de usuario llamando `InvokeOnMainThread` a para evitar que Ios finalice la aplicación. 

## <a name="handling-transfer-completion"></a>Control de la finalización de la transferencia

El paso final consiste en dejar que la aplicación sepa cuándo se han completado todas las tareas asociadas a la sesión y controlar el nuevo contenido.

En el `AppDelegate` , suscríbase al `HandleEventsForBackgroundUrl` evento. Cuando la aplicación entra en el fondo y se está ejecutando una sesión de transferencia, se llama a este método y el sistema lo pasa a un controlador de finalización:

```csharp
public System.Action backgroundSessionCompletionHandler;

public void HandleEventsForBackgroundUrl (UIApplication application, string sessionIdentifier, System.Action completionHandler)
{
  this.backgroundSessionCompletionHandler = completionHandler;
}
```

Use el controlador de finalización para permitir que iOS sepa cuándo se realiza el procesamiento de la aplicación.

Recuerde que una sesión puede generar varias tareas para procesar una transferencia. Cuando se completa la última tarea, se vuelve a iniciar una aplicación suspendida o finalizada en segundo plano. A continuación, la aplicación se vuelve a conectar a `NSURLSession` mediante el identificador de sesión único y llama a `DidFinishEventsForBackgroundSession` en el delegado de la sesión. Este método es la oportunidad de la aplicación para controlar el nuevo contenido, incluida la actualización de la interfaz de usuario para reflejar los resultados de la transferencia:

```csharp
public void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  // Handle new information, update UI, etc.
}
```

Una vez que haya terminado de administrar el nuevo contenido, llame al controlador de finalización para que el sistema sepa que es seguro tomar una instantánea de la aplicación y volver a la suspensión:

```csharp
public void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  var appDelegate = UIApplication.SharedApplication.Delegate as AppDelegate;

  // Handle new information, update UI, etc.

  // call completion handler when you're done
  if (appDelegate.backgroundSessionCompletionHandler != null) {
    NSAction handler = appDelegate.backgroundSessionCompletionHandler;
    appDelegate.backgroundSessionCompletionHandler = null;
    handler.Invoke ();
  }
}
```

En este tutorial se han tratado los pasos básicos para implementar el servicio de transferencia en segundo plano en iOS 7 y versiones más recientes.

## <a name="related-links"></a>Vínculos relacionados

- [Transferencia de fondo simple (ejemplo)](/samples/xamarin/ios-samples/simplebackgroundtransfer)