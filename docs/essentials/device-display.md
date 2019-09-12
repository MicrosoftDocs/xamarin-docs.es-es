---
title: 'Xamarin.Essentials: Información de pantalla del dispositivo'
description: En este documento se describe la clase DeviceDisplay de Xamarin.Essentials, que proporciona las métricas de pantalla del dispositivo en el que se ejecuta la aplicación.
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 77af173bc3297ac9ccdef22dccbeab054895f772
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756901"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials: Información de pantalla del dispositivo

La clase **DeviceDisplay** proporciona información sobre las métricas de la pantalla del dispositivo que determinan cómo se ejecuta la aplicación. También puede solicitar que la pantalla no se apague mientras la aplicación se esté ejecutando.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-devicedisplay"></a>Uso de DeviceDisplay

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

## <a name="main-display-info"></a>Información de la pantalla principal

Además de información básica del dispositivo, la clase **DeviceDisplay** contiene información sobre la pantalla y la orientación del dispositivo.

```csharp
// Get Metrics
var mainDisplayInfo = DeviceDisplay.MainDisplayInfo;

// Orientation (Landscape, Portrait, Square, Unknown)
var orientation = mainDisplayInfo.Orientation;

// Rotation (0, 90, 180, 270)
var rotation = mainDisplayInfo.Rotation;

// Width (in pixels)
var width = mainDisplayInfo.Width;

// Height (in pixels)
var height = mainDisplayInfo.Height;

// Screen density
var density = mainDisplayInfo.Density;
```

La clase **DeviceDisplay** también expone un evento al que se puede suscribir para que se desencadene siempre que cambie cualquier métrica de pantalla:

```csharp
public class DisplayInfoTest
{
    public DisplayInfoTest()
    {
        // Subscribe to changes of screen metrics
        DeviceDisplay.MainDisplayInfoChanged += OnMainDisplayInfoChanged;
    }

    void OnMainDisplayInfoChanged(object sender, DisplayInfoChangedEventArgs  e)
    {
        // Process changes
        var displayInfo = e.DisplayInfo;
    }
}
```

La clase **DeviceDisplay** expone una propiedad `bool` con el nombre `KeepScreenOn`. Esta propiedad puede establecerse para que intente impedir que la pantalla del dispositivo se apague o bloquee.

```csharp
public class KeepScreenOnTest
{
    public void ToggleScreenLock()
    {
        DeviceDisplay.KeepScreenOn = !DeviceDisplay.KeepScreenOn;
    }
}
```

## <a name="platform-differences"></a>Diferencias entre plataformas

# <a name="androidtabandroid"></a>[Android](#tab/android)

No hay diferencias.

# <a name="iostabios"></a>[iOS](#tab/ios)

- El acceso a `DeviceDisplay` se debe realizar en el subproceso de la interfaz de usuario o se iniciará una excepción. Puede usar el método [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) para ejecutar ese código en el subproceso de la interfaz de usuario.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

No hay diferencias.

--------------

## <a name="api"></a>API

- [Código fuente de DeviceDisplay](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceDisplay)
- [Documentación de API de DeviceDisplay](xref:Xamarin.Essentials.DeviceDisplay)
