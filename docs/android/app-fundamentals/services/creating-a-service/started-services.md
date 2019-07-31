---
title: Servicios iniciados con Xamarin. Android
ms.prod: xamarin
ms.assetid: 8CC3A850-4CD2-4F93-98EE-AF3470794000
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 9f3ac33df34f5046fad6d392a6b7edf8a9a7f23f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644139"
---
# <a name="started-services-with-xamarinandroid"></a>Servicios iniciados con Xamarin. Android

## <a name="started-services-overview"></a>Introducción a los servicios iniciados

Los servicios iniciados suelen realizar una unidad de trabajo sin proporcionar comentarios directos o resultados al cliente. Un ejemplo de una unidad de trabajo es un servicio que carga un archivo en un servidor. El cliente realizará una solicitud a un servicio para cargar un archivo desde el dispositivo a un sitio Web. El servicio cargará silenciosamente el archivo (incluso si la aplicación no tiene ninguna actividad en primer plano) y se terminará cuando se haya completado la carga. Es importante saber que un servicio iniciado se ejecutará en el subproceso de interfaz de usuario de una aplicación. Esto significa que si un servicio va a realizar el trabajo que bloqueará el subproceso de la interfaz de usuario, debe crear y eliminar los subprocesos según sea necesario.

A diferencia de un servicio enlazado, no hay ningún canal de comunicación entre un servicio iniciado "puro" y sus clientes. Esto significa que un servicio iniciado implementará algunos métodos de ciclo de vida distintos de un servicio enlazado. En la lista siguiente se resaltan los métodos de ciclo de vida comunes en un servicio iniciado:

- `OnCreate`&ndash; Se llama una vez cuando se inicia el servicio por primera vez. Aquí es donde se debe implementar el código de inicialización.
- `OnBind`&ndash; Este método debe ser implementado por todas las clases de servicio; sin embargo, un servicio iniciado no tiene normalmente un cliente enlazado a él. Por este motivo, un servicio iniciado simplemente devuelve `null`. En cambio, un servicio híbrido (que es la combinación de un servicio enlazado y un servicio iniciado) tiene que implementar y devolver `Binder` un para el cliente.
- `OnStartCommand`Se llama para cada solicitud de inicio del servicio, ya sea en respuesta a una `StartService` llamada a o a un reinicio del sistema. &ndash; Aquí es donde el servicio puede iniciar cualquier tarea de ejecución prolongada. El método devuelve un `StartCommandResult` valor que indica cómo o si el sistema debe controlar el reinicio del servicio después de un cierre debido a la falta de memoria. Esta llamada se realiza en el subproceso principal. Este método se describe con más detalle a continuación.
- `OnDestroy`&ndash; Se llama a este método cuando se destruye el servicio. Se utiliza para realizar una limpieza final requerida.

El método importante para un servicio iniciado es el `OnStartCommand` método. Se invocará cada vez que el servicio reciba una solicitud para realizar algún trabajo. El siguiente fragmento de código es un ejemplo `OnStartCommand`de: 

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

- `StartCommandFlag.Redelivery`Esto significa que es una nueva entrega de una anterior `Intent`. `Intent` &ndash; Este valor se proporciona cuando se devolvió `StartCommandResult.RedeliverIntent` el servicio pero se detuvo antes de que se pudiera apagar correctamente.
-`StartCommandFlag.Retry`Este valor se recibe cuando se produce `OnStartCommand` un error en una llamada anterior y Android intenta iniciar el servicio de nuevo con la misma intención que el intento con error anterior. &dash;
 
Por último, el tercer parámetro es un valor entero que es único para la aplicación que identifica la solicitud. Es posible que varios llamadores puedan invocar el mismo objeto de servicio. Este valor se usa para asociar una solicitud para detener un servicio con una solicitud determinada para iniciar un servicio. Se tratará con más detalle en la sección [detener el servicio](#Stopping_the_Service). 

El servicio `StartCommandResult` devuelve el valor como una sugerencia para Android sobre qué hacer si se elimina el servicio debido a las restricciones de recursos. Hay tres valores posibles para `StartCommandResult`:

- **[StartCommandResult. NotSticky](xref:Android.App.StartCommandResult.NotSticky)** &ndash; este valor indica a Android que no es necesario reiniciar el servicio que ha eliminado. Como ejemplo, considere un servicio que genera miniaturas para una galería en una aplicación. Si se elimina el servicio, no es fundamental volver a crear la miniatura inmediatamente &ndash; la miniatura se puede volver a crear la próxima vez que se ejecute la aplicación.
- **[StartCommandResult.](xref:Android.App.StartCommandResult.Sticky)** &ndash; la permanencia indica a Android que reinicie el servicio, pero no que entregue el último intento que inició el servicio. Si no hay ningún intento pendiente de control, `null` se proporcionará para el parámetro de intención. Un ejemplo de esto podría ser una aplicación de reproductor de música; el servicio se reiniciará listo para reproducir música, pero reproducirá la última canción.
- **[StartCommandResult. RedeliverIntent](xref:Android.App.StartCommandResult.RedeliverIntent)** &ndash; este valor indica a Android que reinicie el servicio y vuelva a ofrecer el último `Intent`. Un ejemplo de esto es un servicio que descarga un archivo de datos para una aplicación. Si se elimina el servicio, todavía es necesario descargar el archivo de datos. Al volver `StartCommandResult.RedeliverIntent`, cuando Android reinicia el servicio, también proporcionará la intención (que contiene la dirección URL del archivo que se va a descargar) al servicio. Esto permitirá que la descarga se reinicie o se reanude (dependiendo de la implementación exacta del código).

Hay un cuarto valor para `StartCommandResult`. &ndash; `StartCommandResult.ContinuationMask` Devuelve este valor `OnStartCommand` y describe cómo Android continuará el servicio que ha eliminado. Este valor no se usa normalmente para iniciar un servicio.

Los eventos de ciclo de vida clave de un servicio iniciado se muestran en este diagrama: 

![Diagrama que muestra el orden en el que se llama a los métodos de ciclo de vida](started-services-images/started-service-01.png "Diagrama que muestra el orden en el que se llama a los métodos de ciclo de vida.")

<a name="Stopping_the_Service" />

## <a name="stopping-the-service"></a>Detener el servicio

Un servicio iniciado se mantendrá en ejecución indefinidamente. Android mantendrá el servicio en ejecución siempre que haya suficientes recursos del sistema. El cliente debe detener el servicio o el servicio puede detenerse cuando se realiza su trabajo. Hay dos maneras de detener un servicio: 

- **[Android. Content. Context. StopService ()](xref:Android.Content.Context.StopService*)** Un cliente (por ejemplo, una actividad) puede solicitar que se detenga un servicio `StopService` llamando al método: &ndash;

    ```csharp
    StopService(new Intent(this, typeof(DemoService));
    ```

- **[Android. app. Service. StopSelf ()](xref:Android.App.Service.StopSelf*)** Un servicio puede apagarse inactivamente mediante la invocación del `StopSelf`: &ndash;

    ```csharp
    StopSelf();
    ```

### <a name="using-startid-to-stop-a-service"></a>Uso de startId para detener un servicio

Varios llamadores pueden solicitar que se inicie un servicio. Si hay una solicitud de inicio pendiente, el servicio puede usar el `startId` que se `OnStartCommand` pasa a para evitar que el servicio se detenga prematuramente. Se corresponderá con la llamada más reciente `StartService`a, y se incrementará cada vez que se llame a. `startId` Por consiguiente, si una solicitud subsiguiente `StartService` a aún no ha dado como resultado una `OnStartCommand`llamada a, el servicio `StopSelfResult`puede llamar a y pasarle el `startId` valor más reciente de que ha recibido (en `StopSelf`lugar de simplemente llamar a). Si una llamada a `StartService` aún no ha dado como resultado una llamada correspondiente `OnStartCommand`a, el sistema no detendrá el servicio porque el `startId` usado en la `StopSelf` llamada no se corresponderá con la `StartService` llamada más reciente.

## <a name="related-links"></a>Vínculos relacionados

- [StartedServicesDemo (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-startedservicesdemo)
- [Android.App.Service](xref:Android.App.Service)
- [Android.App.StartCommandFlags](xref:Android.App.StartCommandFlags)
- [Android.App.StartCommandResult](xref:Android.App.StartCommandResult)
- [Android.Content.BroadcastReceiver](xref:Android.Content.BroadcastReceiver)
- [Android.Content.Intent](xref:Android.Content.Intent)
- [Android.OS.Handler](xref:Android.OS.Handler)
- [Android.Widget.Toast](xref:Android.Widget.Toast)
- [Iconos de la barra de estado](https://developer.android.com/guide/practices/ui_guidelines/icon_design_status_bar.html)
