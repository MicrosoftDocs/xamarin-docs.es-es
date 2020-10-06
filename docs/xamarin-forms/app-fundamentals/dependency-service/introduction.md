---
title: Introducción a DependencyService de Xamarin.Forms
description: En este artículo se explica cómo se usa la clase DependencyService de Xamarin.Forms para invocar la funcionalidad nativa de la plataforma.
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/12/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2d63ac91031fc4a2cc127272a075f15a8ea81f4e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555287"
---
# <a name="no-locxamarinforms-dependencyservice-introduction"></a>Introducción a DependencyService de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)

La clase [`DependencyService`](xref:Xamarin.Forms.DependencyService) es un localizador de servicios que habilita las aplicaciones de Xamarin.Forms para invocar la funcionalidad nativa de la plataforma desde código compartido.

El proceso para usar [`DependencyService`](xref:Xamarin.Forms.DependencyService) para invocar la funcionalidad nativa de la plataforma es el siguiente:

1. Cree una interfaz para la funcionalidad de la plataforma nativa en el código compartido. Para más información, vea [Creación de una interfaz](#create-an-interface).
1. Implemente la interfaz en los proyectos de la plataforma requeridos. Para obtener más información, vea [Implementación de la interfaz en cada plataforma](#implement-the-interface-on-each-platform).
1. Registro de las implementaciones de la plataforma con [`DependencyService`](xref:Xamarin.Forms.DependencyService). Esto permite que Xamarin.Forms localice las implementaciones de la plataforma en tiempo de ejecución. Para obtener más información, vea [Registro de las implementaciones de la plataforma](#register-the-platform-implementations).
1. Resuelva las implementaciones de la plataforma desde el código compartido e invóquelas. Para obtener más información, vea [Resolución de las implementaciones de la plataforma](#resolve-the-platform-implementations).

En el siguiente diagrama, se muestra cómo se invoca la funcionalidad nativa de la plataforma en una aplicación de Xamarin.Forms:

![Información general de la ubicación del servicio mediante la clase DependencyService de Xamarin.Forms](introduction-images/dependency-service.png "Ubicación del servicio de DependencyService")

## <a name="create-an-interface"></a>Creación de una interfaz

El primer paso para poder invocar la funcionalidad nativa de la plataforma desde código compartido es crear una interfaz que defina la API para interactuar con la funcionalidad nativa de la plataforma. Esta interfaz debe colocarse en su proyecto de código compartido.

En el ejemplo siguiente, se muestra una interfaz para una API que puede usarse para recuperar la orientación de un dispositivo:

```csharp
public interface IDeviceOrientationService
{
    DeviceOrientation GetOrientation();
}
```

## <a name="implement-the-interface-on-each-platform"></a>Implementación de la interfaz en cada plataforma

Después de crear la interfaz que define la API para interactuar con la funcionalidad nativa de la plataforma, la interfaz deberá implementarse en cada proyecto de la plataforma.

### <a name="ios"></a>iOS

En el siguiente ejemplo de código, se muestra la implementación de la interfaz de `IDeviceOrientationService` en iOS:

```csharp
namespace DependencyServiceDemos.iOS
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            UIInterfaceOrientation orientation = UIApplication.SharedApplication.StatusBarOrientation;

            bool isPortrait = orientation == UIInterfaceOrientation.Portrait ||
                orientation == UIInterfaceOrientation.PortraitUpsideDown;
            return isPortrait ? DeviceOrientation.Portrait : DeviceOrientation.Landscape;
        }
    }
}
```

### <a name="android"></a>Android

En el siguiente ejemplo de código, se muestra la implementación de la interfaz de `IDeviceOrientationService` en Android:

```csharp
namespace DependencyServiceDemos.Droid
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            IWindowManager windowManager = Android.App.Application.Context.GetSystemService(Context.WindowService).JavaCast<IWindowManager>();

            SurfaceOrientation orientation = windowManager.DefaultDisplay.Rotation;
            bool isLandscape = orientation == SurfaceOrientation.Rotation90 ||
                orientation == SurfaceOrientation.Rotation270;
            return isLandscape ? DeviceOrientation.Landscape : DeviceOrientation.Portrait;
        }
    }
}
```

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

En el siguiente ejemplo de código, se muestra la implementación de la interfaz de `IDeviceOrientationService` en la Plataforma universal de Windows (UWP):

```csharp
namespace DependencyServiceDemos.UWP
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            ApplicationViewOrientation orientation = ApplicationView.GetForCurrentView().Orientation;
            return orientation == ApplicationViewOrientation.Landscape ? DeviceOrientation.Landscape : DeviceOrientation.Portrait;
        }
    }
}
```

## <a name="register-the-platform-implementations"></a>Registro de las implementaciones de la plataforma

Después de implementar la interfaz en cada proyecto de la plataforma, las implementaciones de la plataforma deberán registrarse con [`DependencyService`](xref:Xamarin.Forms.DependencyService) para que Xamarin.Forms pueda ubicarlas en tiempo de ejecución. Normalmente, esto se realiza con [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute), lo que indica que el tipo especificado proporciona una implementación de la interfaz.

En el siguiente ejemplo se muestra el uso de [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) para registrar la implementación en iOS de la interfaz de `IDeviceOrientationService`:

```csharp
using Xamarin.Forms;

[assembly: Dependency(typeof(DependencyServiceDemos.iOS.DeviceOrientationService))]
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

En este ejemplo, [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) registra `DeviceOrientationService` con [`DependencyService`](xref:Xamarin.Forms.DependencyService). De forma similar, las implementaciones de la interfaz de `IDeviceOrientationService` en otras plataformas se debe registrar con [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute).

Para obtener más información sobre el registro de implementaciones de plataforma con [`DependencyService`](xref:Xamarin.Forms.DependencyService), vea [Registro y resolución de DependencyService de Xamarin.Forms](registration-and-resolution.md).

## <a name="resolve-the-platform-implementations"></a>Resolución de las implementaciones de la plataforma

Tras el registro de las implementaciones de la plataforma con [`DependencyService`](xref:Xamarin.Forms.DependencyService), las implementaciones deberán resolverse antes de invocarse. Normalmente, esto se realiza en código compartido mediante el método [`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*).

En el código siguiente, se muestra un ejemplo de llamada al método [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) para resolver la interfaz de `IDeviceOrientationService` y, después, invocar su método `GetOrientation`:

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Como alternativa, este código se puede comprimir en una sola línea:

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

Para obtener más información sobre la resolución de implementaciones de plataforma con [`DependencyService`](xref:Xamarin.Forms.DependencyService), vea [Registro y resolución de DependencyService de Xamarin.Forms](registration-and-resolution.md).

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de DependencyService (ejemplo)](/samples/xamarin/xamarin-forms-samples/dependencyservice/)
- [Registro y resolución de DependencyService de Xamarin.Forms](registration-and-resolution.md)