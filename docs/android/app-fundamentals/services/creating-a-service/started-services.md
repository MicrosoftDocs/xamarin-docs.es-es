---
title: Servicios iniciados con Xamarin. Android
ms.prod: xamarin
ms.assetid: 8CC3A850-4CD2-4F93-98EE-AF3470794000
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 6075b125f36625a8dec12c041631e3794a71cc6a
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455371"
---
# <a name="started-services-with-xamarinandroid"></a>Servicios iniciados con Xamarin. Android

## <a name="started-services-overview"></a>Introducción a los servicios iniciados

Los servicios iniciados suelen realizar una unidad de trabajo sin proporcionar comentarios directos o resultados al cliente. Un ejemplo de una unidad de trabajo es un servicio que carga un archivo en un servidor. El cliente realizará una solicitud a un servicio para cargar un archivo desde el dispositivo a un sitio Web. El servicio cargará silenciosamente el archivo (incluso si la aplicación no tiene ninguna actividad en primer plano) y se terminará cuando se haya completado la carga. Es importante saber que un servicio iniciado se ejecutará en el subproceso de interfaz de usuario de una aplicación. Esto significa que si un servicio va a realizar el trabajo que bloqueará el subproceso de la interfaz de usuario, debe crear y eliminar los subprocesos según sea necesario.

A diferencia de un servicio enlazado, no hay ningún canal de comunicación entre un servicio iniciado "puro" y sus clientes. Esto significa que un servicio iniciado implementará algunos métodos de ciclo de vida distintos de un servicio enlazado. En la lista siguiente se resaltan los métodos de ciclo de vida comunes en un servicio iniciado:

- `OnCreate`&ndash;Se llama una vez cuando se inicia el servicio por primera vez. Aquí es donde se debe implementar el código de inicialización.
- `OnBind`&ndash;Este método debe ser implementado por todas las clases de servicio; sin embargo, un servicio iniciado no tiene normalmente un cliente enlazado a él. Por este motivo, un servicio iniciado simplemente devuelve `null` . En cambio, un servicio híbrido (que es la combinación de un servicio enlazado y un servicio iniciado) tiene que implementar y devolver un `Binder` para el cliente.
- `OnStartCommand`&ndash;Se llama para cada solicitud de inicio del servicio, ya sea en respuesta a una llamada a `StartService` o a un reinicio del sistema. Aquí es donde el servicio puede iniciar cualquier tarea de ejecución prolongada. El método devuelve un  `StartCommandResult` valor que indica cómo o si el sistema debe controlar el reinicio del servicio después de un cierre debido a la falta de memoria. Esta llamada se realiza en el subproceso principal. Este método se describe con más detalle a continuación.
- `OnDestroy`&ndash;Se llama a este método cuando se destruye el servicio. Se utiliza para realizar una limpieza final requerida.

El método importante para un servicio iniciado es el `OnStartCommand` método. Se invocará cada vez que el servicio reciba una solicitud para realizar algún trabajo. El siguiente fragmento de código es un ejemplo de `OnStartCommand` : 

```csharp
public override StartCommandResult OnStartCommand (Android.Content.Intent intent, StartCommandFlags flags, int startId)
{
    // This method executes on the main thread of the application.
    Log.Debug ("DemoService", "DemoService started");
    ...
    return StartCommandResult.Sticky;
}
```

El primer parámetro es un `Intent` objeto que contiene los metadatos sobre el trabajo que se va a realizar. El segundo parámetro contiene un `StartCommandFlags` valor que proporciona información sobre la llamada al método. Este parámetro tiene uno de dos valores posibles:

- `StartCommandFlag.Redelivery`&ndash;Esto significa que `Intent` es una nueva entrega de una anterior `Intent` . Este valor se proporciona cuando se devolvió el servicio `StartCommandResult.RedeliverIntent` pero se detuvo antes de que se pudiera apagar correctamente.
-`StartCommandFlag.Retry`&dash;Este valor se recibe cuando se `OnStartCommand` produce un error en una llamada anterior y Android intenta iniciar el servicio de nuevo con la misma intención que el intento con error anterior.

Por último, el tercer parámetro es un valor entero que es único para la aplicación que identifica la solicitud. Es posible que varios llamadores puedan invocar el mismo objeto de servicio. Este valor se usa para asociar una solicitud para detener un servicio con una solicitud determinada para iniciar un servicio. Se tratará con más detalle en la sección [detener el servicio](#Stopping_the_Service). 

El `StartCommandResult` servicio devuelve el valor como una sugerencia para Android sobre qué hacer si se elimina el servicio debido a las restricciones de recursos. Hay tres valores posibles para `StartCommandResult` :

- **[StartCommandResult. NotSticky](xref:Android.App.StartCommandResult.NotSticky)** &ndash; este valor indica a Android que no es necesario reiniciar el servicio que ha eliminado. Como ejemplo, considere un servicio que genera miniaturas para una galería en una aplicación. Si se elimina el servicio, no es fundamental volver a crear la miniatura inmediatamente &ndash; la miniatura se puede volver a crear la próxima vez que se ejecute la aplicación.
- **[StartCommandResult. la permanencia](xref:Android.App.StartCommandResult.Sticky)** &ndash; indica a Android que reinicie el servicio, pero no que entregue el último intento que inició el servicio. Si no hay ningún intento pendiente de control, se `null` proporcionará para el parámetro de intención. Un ejemplo de esto podría ser una aplicación de reproductor de música; el servicio se reiniciará listo para reproducir música, pero reproducirá la última canción.
- **[StartCommandResult. RedeliverIntent](xref:Android.App.StartCommandResult.RedeliverIntent)** &ndash; este valor indica a Android que reinicie el servicio y vuelva a ofrecer el último `Intent` . Un ejemplo de esto es un servicio que descarga un archivo de datos para una aplicación. Si se elimina el servicio, todavía es necesario descargar el archivo de datos. Al volver `StartCommandResult.RedeliverIntent` , cuando Android reinicia el servicio, también proporcionará la intención (que contiene la dirección URL del archivo que se va a descargar) al servicio. Esto permitirá que la descarga se reinicie o se reanude (dependiendo de la implementación exacta del código).

Hay un cuarto valor para `StartCommandResult` &ndash; `StartCommandResult.ContinuationMask` . Devuelve este valor `OnStartCommand` y describe cómo Android continuará el servicio que ha eliminado. Este valor no se usa normalmente para iniciar un servicio.

Los eventos de ciclo de vida clave de un servicio iniciado se muestran en este diagrama: 

![Diagrama que muestra el orden en el que se llama a los métodos de ciclo de vida](started-services-images/started-service-01.png "Diagrama que muestra el orden en el que se llama a los métodos de ciclo de vida.")

<a name="Stopping_the_Service"></a>

## <a name="stopping-the-service"></a>Detener el servicio

Un servicio iniciado se mantendrá en ejecución indefinidamente. Android mantendrá el servicio en ejecución siempre que haya suficientes recursos del sistema. El cliente debe detener el servicio o el servicio puede detenerse cuando se realiza su trabajo. Hay dos maneras de detener un servicio: 

- **[Android. Content. Context. StopService ()](xref:Android.Content.Context.StopService*)** &ndash; Un cliente (por ejemplo, una actividad) puede solicitar que se detenga un servicio llamando al `StopService` método:

    ```csharp
    StopService(new Intent(this, typeof(DemoService));
    ```

- **[Android. app. Service. StopSelf ()](xref:Android.App.Service.StopSelf*)** &ndash; Un servicio puede apagarse inactivamente mediante la invocación del `StopSelf` :

    ```csharp
    StopSelf();
    ```

### <a name="using-startid-to-stop-a-service"></a>Uso de startId para detener un servicio

Varios llamadores pueden solicitar que se inicie un servicio. Si hay una solicitud de inicio pendiente, el servicio puede usar el `startId` que se pasa a `OnStartCommand` para evitar que el servicio se detenga prematuramente. `startId`Se corresponderá con la llamada más reciente a `StartService` , y se incrementará cada vez que se llame a. Por consiguiente, si una solicitud subsiguiente a `StartService` aún no ha dado como resultado una llamada a `OnStartCommand` , el servicio puede llamar a `StopSelfResult` y pasarle el valor más reciente de `startId` que ha recibido (en lugar de simplemente llamar a `StopSelf` ). Si una llamada a `StartService` aún no ha dado como resultado una llamada correspondiente a `OnStartCommand` , el sistema no detendrá el servicio porque el `startId` usado en la `StopSelf` llamada no se corresponderá con la `StartService` llamada más reciente.

## <a name="related-links"></a>Vínculos relacionados

- [StartedServicesDemo (ejemplo)](/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-startedservicesdemo)
- [Android. app. Service](xref:Android.App.Service)
- [Android. app. StartCommandFlags](xref:Android.App.StartCommandFlags)
- [Android. app. StartCommandResult](xref:Android.App.StartCommandResult)
- [Android. Content. BroadcastReceiver](xref:Android.Content.BroadcastReceiver)
- [Android. Content. Intent](xref:Android.Content.Intent)
- [Android. OS. handler](xref:Android.OS.Handler)
- [Android. widget. notificación del sistema](xref:Android.Widget.Toast)
- [Iconos de la barra de estado](https://developer.android.com/guide/practices/ui_guidelines/icon_design_status_bar.html)