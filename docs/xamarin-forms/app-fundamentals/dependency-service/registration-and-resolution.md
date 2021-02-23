---
title: Registro y resolución de DependencyService de Xamarin.Forms
description: En este artículo se explica cómo registrar las implementaciones de plataforma con la clase DependencyService de Xamarin.Forms para invocar la funcionalidad nativa de la plataforma.
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/05/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 632d2256ec30602aa9d5ed4dee9e781864b2f462
ms.sourcegitcommit: e7a5d1ec9e50a09b3b24f4c57850a4763c3406d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/18/2021
ms.locfileid: "101087454"
---
# <a name="xamarinforms-dependencyservice-registration-and-resolution"></a>Registro y resolución de DependencyService de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/dependencyservice/)

Al usar [`DependencyService`](xref:Xamarin.Forms.DependencyService) de Xamarin.Forms para invocar la funcionalidad de la plataforma nativa, las implementaciones de la plataforma deben estar registradas con `DependencyService` y, después, resolverse desde código compartido para poder invocarse.

## <a name="register-platform-implementations"></a>Registro de las implementaciones de la plataforma

Las implementaciones de la plataforma deben estar registradas con [`DependencyService`](xref:Xamarin.Forms.DependencyService) para que Xamarin.Forms pueda localizarlas en tiempo de ejecución.

El registro se puede realizar con [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) o con los métodos [`Register`](xref:Xamarin.Forms.DependencyService.Register*) y `RegisterSingleton`.

> [!IMPORTANT]
> Las compilaciones de versión de los proyectos de UWP que usan la compilación nativa de .NET deben registrar las implementaciones de plataforma con los métodos [`Register`](xref:Xamarin.Forms.DependencyService.Register*).

### <a name="registration-by-attribute"></a>Registro por atributo

[`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) se puede usar para registrar una implementación de la plataforma con [`DependencyService`](xref:Xamarin.Forms.DependencyService). El atributo indica que el tipo especificado proporciona una implementación concreta de la interfaz.

En el siguiente ejemplo se usa [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) para registrar la implementación de iOS de la interfaz `IDeviceOrientationService`:

```csharp
using Xamarin.Forms;

[assembly: Dependency(typeof(DeviceOrientationService))]
namespace DependencyServiceDemos.iOS
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            ...
        }
    }
}
```

En este ejemplo, [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) registra `DeviceOrientationService` con [`DependencyService`](xref:Xamarin.Forms.DependencyService). Esto da como resultado el tipo concreto que se va a registrar en la interfaz que implementará.

De forma similar, las implementaciones de la interfaz de `IDeviceOrientationService` en otras plataformas se debe registrar con [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute).

> [!NOTE]
> El registro con [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) se realiza en el nivel de espacio de nombres.

### <a name="registration-by-method"></a>Registro por método

Los métodos [`DependencyService.Register`](xref:Xamarin.Forms.DependencyService.Register*), y el método `RegisterSingleton`, se pueden usar para registrar una implementación de la plataforma con [`DependencyService`](xref:Xamarin.Forms.DependencyService).

En el siguiente ejemplo se usa el método [`Register`](xref:Xamarin.Forms.DependencyService.Register*) para registrar la implementación de iOS de la interfaz `IDeviceOrientationService`:

```csharp
[Register("AppDelegate")]
public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
{
    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.Init();
        LoadApplication(new App());
        DependencyService.Register<IDeviceOrientationService, DeviceOrientationService>();
        return base.FinishedLaunching(app, options);
    }
}
```

En este ejemplo, el método [`Register`](xref:Xamarin.Forms.DependencyService.Register*) registra el tipo concreto, `DeviceOrientationService`, en la interfaz de `IDeviceOrientationService`. De forma alternativa, se puede usar una sobrecarga del método [`Register`](xref:Xamarin.Forms.DependencyService.Register*) para registrar una implementación de plataforma con [`DependencyService`](xref:Xamarin.Forms.DependencyService):

```csharp
DependencyService.Register<DeviceOrientationService>();
```

En este ejemplo, el método [`Register`](xref:Xamarin.Forms.DependencyService.Register*) registra `DeviceOrientationService` con [`DependencyService`](xref:Xamarin.Forms.DependencyService). Esto da como resultado el tipo concreto que se va a registrar en la interfaz que implementará.

Como alternativa, se puede registrar una instancia de objeto existente como singleton con el método `RegisterSingleton`:

```csharp
var service = new DeviceOrientationService();
DependencyService.RegisterSingleton<IDeviceOrientationService>(service);
```

En este ejemplo, el método `RegisterSingleton` registra la instancia del objeto `DeviceOrientationService` en la interfaz `IDeviceOrientationService`, como singleton.

De forma similar, las implementaciones de la interfaz `IDeviceOrientationService` en otras plataformas se puede registrar con los métodos [`Register`](xref:Xamarin.Forms.DependencyService.Register*), o el método `RegisterSingleton`.

> [!IMPORTANT]
> El registro con los métodos [`Register`](xref:Xamarin.Forms.DependencyService.Register*) y `RegisterSingleton` se debe realizar en los proyectos de la plataforma antes de que se invoque la funcionalidad que ha proporcionado la implementación de la plataforma mediante el código compartido.

## <a name="resolve-the-platform-implementations"></a>Resolución de las implementaciones de la plataforma

Las implementaciones de la plataforma deben resolverse antes de invocarse. Normalmente, esto se realiza en código compartido mediante el método [`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*). Sin embargo, también se puede lograr con el método [`DependencyService.Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*).

De forma predeterminada, [`DependencyService`](xref:Xamarin.Forms.DependencyService) solo resuelve implementaciones de plataforma con constructores sin parámetros. Aun así, se puede incorporar a Xamarin.Forms un método de resolución de dependencias que use un contenedor de inserción de dependencias o métodos de generador para resolver implementaciones de plataforma. Este enfoque puede usarse para resolver las implementaciones de plataforma que tienen constructores con parámetros. Para obtener más información, vea [Inserción de dependencias en Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md).

> [!IMPORTANT]
> La invocación de una implementación de la plataforma que no se haya registrado con [`DependencyService`](xref:Xamarin.Forms.DependencyService) tendrá como resultado la generación de `NullReferenceException`.

### <a name="resolve-using-the-getlttgt-method"></a>Resolución mediante el método Get&lt;T&gt;

El método [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) recupera la implementación de la plataforma de la interfaz de `T` en tiempo de ejecución y luego hace una de estas acciones:

- Crea una instancia de ella como singleton.
- Devuelve una instancia existente como singleton, que se registró con `DependencyService` por el método `RegisterSingleton`.

En ambos casos, esta instancia estará activa durante la vigencia de la aplicación, y las llamadas subsiguientes para resolver la misma implementación de la plataforma recuperarán la misma instancia.

En el código siguiente, se muestra un ejemplo de llamada al método [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) para resolver la interfaz de `IDeviceOrientationService` y, después, invocar su método `GetOrientation`:

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Como alternativa, este código se puede comprimir en una sola línea:

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> El método [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) devuelve una instancia de la implementación de la plataforma de la interfaz de `T` como singleton de forma predeterminada. No obstante, se puede modificar este comportamiento. Para obtener más información, vea [Administración de la vigencia de los objetos resueltos](#manage-the-lifetime-of-resolved-objects).

### <a name="resolve-using-the-resolvelttgt-method"></a>Resolución mediante el método Resolve&lt;T&gt;

El método [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) recupera la implementación de la plataforma de la interfaz de `T` en tiempo de ejecución mediante un método de resolución de dependencias que se ha insertado en Xamarin.Forms con la clase [`DependencyResolver`](xref:Xamarin.Forms.Internals.DependencyResolver). Si un método de resolución de dependencias no se ha insertado en Xamarin.Forms, el método `Resolve<T>` llamará al método [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) como solución alternativa para recuperar la implementación de la plataforma. Para obtener más información sobre la inserción de métodos de resolución de dependencias en Xamarin.Forms, vea [Resolución de dependencias en Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md).

En el código siguiente, se muestra un ejemplo de llamada al método [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) para resolver la interfaz de `IDeviceOrientationService` y, después, invocar su método `GetOrientation`:

```csharp
IDeviceOrientationService service = DependencyService.Resolve<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Como alternativa, este código se puede comprimir en una sola línea:

```csharp
DeviceOrientation orientation = DependencyService.Resolve<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> Cuando el método [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) llama al método [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) como solución alternativa, devuelve una instancia de la implementación de la plataforma de la interfaz de `T` como singleton de forma predeterminada. No obstante, se puede modificar este comportamiento. Para obtener más información, vea [Administración de la vigencia de los objetos resueltos](#manage-the-lifetime-of-resolved-objects).

## <a name="manage-the-lifetime-of-resolved-objects"></a>Administración de la vigencia de los objetos resueltos

El comportamiento predeterminado de la clase [`DependencyService`](xref:Xamarin.Forms.DependencyService) es resolver las implementaciones de la plataforma como elementos singleton. Por lo tanto, las implementaciones de la plataforma permanecerán activas durante la vigencia de la aplicación en cuestión.

Este comportamiento se especifica con el argumento opcional [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) en los métodos [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) y [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*). La enumeración [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) define dos miembros:

- `GlobalInstance`, que devuelve la implementación de la plataforma como singleton.
- `NewInstance`, que devuelve una nueva instancia de la implementación de la plataforma. La aplicación es responsable de administrar la vigencia de la instancia de la implementación de la plataforma.

Los métodos [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) y [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) establecen sus argumentos opcionales en [`DependencyFetchTarget.GlobalInstance`](xref:Xamarin.Forms.DependencyFetchTarget), por lo que las implementaciones de la plataforma siempre se resuelven como elementos singleton. Este comportamiento se puede cambiar especificando [`DependencyFetchTarget.NewInstance`](xref:Xamarin.Forms.DependencyFetchTarget) como argumentos para los métodos `Get<T>` y `Resolve<T>` con el fin de que crear las instancias de las implementaciones de la plataforma:

```csharp
ITextToSpeechService service = DependencyService.Get<ITextToSpeechService>(DependencyFetchTarget.NewInstance);
```

En este ejemplo, [`DependencyService`](xref:Xamarin.Forms.DependencyService) crea una instancia de la implementación de la plataforma para la interfaz de `ITextToSpeechService`. Las llamadas subsiguientes para resolver `ITextToSpeechService` también crearán nuevas instancias.

La consecuencia de crear siempre una nueva instancia de una implementación de la plataforma es que la aplicación adquiere la responsabilidad de administrar la vigencia de las instancias. Esto significa que, si se suscribe a un evento definido en una implementación de la plataforma, deberá cancelar la suscripción al evento cuando ya no se requiera la implementación de la plataforma. Además, significa que puede ser necesario que las implementaciones de la plataforma implementen `IDisposable` y realicen una limpieza de sus recursos en los métodos `Dispose`. La aplicación de ejemplo muestra este escenario en sus implementaciones de la plataforma de `TextToSpeechService`.

Cuando una aplicación termina de utilizar una implementación de la plataforma que implementa `IDisposable`, debe llamar a la implementación del objeto `Dispose`. Una manera de realizar esta acción es usar una instrucción `using`:

```csharp
ITextToSpeechService service = DependencyService.Get<ITextToSpeechService>(DependencyFetchTarget.NewInstance);
using (service as IDisposable)
{
    await service.SpeakAsync("Hello world");
}
```

En este ejemplo, una vez que se ha invocado el método `SpeakAsync`, la instrucción `using` desecha automáticamente el objeto de la implementación de la plataforma. Esto da como resultado la invocación del método `Dispose` del objeto, que realiza la limpieza requerida.

Para obtener más información sobre cómo llamar al método `Dispose` de un objeto, vea [Uso de objetos que implementan IDisposable](/dotnet/standard/garbage-collection/using-objects).

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de DependencyService (ejemplo)](/samples/xamarin/xamarin-forms-samples/dependencyservice/)
- [Resolución de dependencias Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md)