---
title: Servicios de intención en Xamarin. Android
ms.prod: xamarin
ms.assetid: A5B86FE4-C8E2-4B0A-84CA-EF8F5119E31B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: c58787a051bfc965cb7493138ed6114ac23ed04d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024843"
---
# <a name="intent-services-in-xamarinandroid"></a>Servicios de intención en Xamarin. Android

## <a name="intent-services-overview"></a>Información general sobre servicios de intención

Tanto los servicios iniciados como los enlazados se ejecutan en el subproceso principal, lo que significa que para mantener el rendimiento sin problemas, un servicio necesita realizar el trabajo de forma asincrónica. Una de las formas más sencillas de solucionar este problema es con un _patrón de procesador de cola de trabajo_, en el que el trabajo que se va a hacer se coloca en una cola a la que presta servicio un solo subproceso.

El [`IntentService`](xref:Android.App.IntentService) es una subclase de la clase `Service` que proporciona una implementación específica de Android de este patrón. Administrará el trabajo de puesta en cola, iniciará un subproceso de trabajo para atender la cola y eliminará las solicitudes de la cola que se va a ejecutar en el subproceso de trabajo. Un `IntentService` se detendrá de forma silenciosa y quitará el subproceso de trabajo cuando no haya más trabajo en la cola.

El trabajo se envía a la cola creando un `Intent` y, a continuación, pasando ese `Intent` al método `StartService`.

No es posible detener o interrumpir el método de `OnHandleIntent` `IntentService` mientras está funcionando. Debido a este diseño, un `IntentService` debe mantenerse sin estado &ndash; no debe basarse en una conexión activa o en la comunicación desde el resto de la aplicación. Una `IntentService` está pensada para statelessly procesar las solicitudes de trabajo.

Existen dos requisitos para la subclase `IntentService`:

1. El nuevo tipo (creado por la creación de subclases `IntentService`) solo invalida el método `OnHandleIntent`.
2. El constructor del nuevo tipo requiere una cadena que se usa para asignar un nombre al subproceso de trabajo que controlará las solicitudes. El nombre de este subproceso de trabajo se usa principalmente al depurar la aplicación.

En el código siguiente se muestra una implementación de `IntentService` con el método `OnHandleIntent` invalidado:

```csharp
[Service]
public class DemoIntentService: IntentService
{
    public DemoIntentService () : base("DemoIntentService")
    {
    }

    protected override void OnHandleIntent (Android.Content.Intent intent)
    {
        Console.WriteLine ("perform some long running work");
        ...
        Console.WriteLine ("work complete");
    }
}
```

El trabajo se envía a un `IntentService` mediante la creación de una instancia de un `Intent` y, a continuación, la llamada al método [`StartService`](xref:Android.Content.Context.StartService*) con ese intento como parámetro. La intención se pasará al servicio como un parámetro en el método `OnHandleIntent`. Este fragmento de código es un ejemplo de envío de una solicitud de trabajo a un intento: 

```csharp
// This code might be called from within an Activity, for example in an event
// handler for a button click.
Intent downloadIntent = new Intent(this, typeof(DemoIntentService));

// This is just one example of passing some values to an IntentService via the Intent:
downloadIntent.Put
("file_to_download", "http://www.somewhere.com/file/to/download.zip");

StartService(downloadIntent);
```

El `IntentService` puede extraer los valores de la intención, tal como se muestra en este fragmento de código:  

```csharp
protected override void OnHandleIntent (Android.Content.Intent intent)
{
    string fileToDownload = intent.GetStringExtra("file_to_download");

    Log.Debug("DemoIntentService", $"File to download: {fileToDownload}.");
}
```

## <a name="related-links"></a>Vínculos relacionados

- [IntentService](xref:Android.App.IntentService)
- [StartService](xref:Android.Content.Context.StartService*)
