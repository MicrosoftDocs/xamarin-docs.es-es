---
title: 'Xamarin.Essentials: Información del dispositivo'
description: En este documento se describe la clase DeviceInfo de Xamarin.Essentials, que proporciona información sobre el dispositivo en el que se ejecuta la aplicación.
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 1cab4ea8ea3f98def4830e101783db1554efa69c
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "78295422"
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials: Información del dispositivo

La clase **DeviceInfo** proporciona información sobre el dispositivo en el que se ejecuta la aplicación.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-deviceinfo"></a>Uso de DeviceInfo

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La información siguiente se expone a través de la API:

```csharp
// Device Model (SMG-950U, iPhone10,6)
var device = DeviceInfo.Model;

// Manufacturer (Samsung)
var manufacturer = DeviceInfo.Manufacturer;

// Device Name (Motz's iPhone)
var deviceName = DeviceInfo.Name;

// Operating System Version Number (7.0)
var version = DeviceInfo.VersionString;

// Platform (Android)
var platform = DeviceInfo.Platform;

// Idiom (Phone)
var idiom = DeviceInfo.Idiom;

// Device Type (Physical)
var deviceType = DeviceInfo.DeviceType;
```

## <a name="platforms"></a>Plataformas

[`DeviceInfo.Platform`](xref:Xamarin.Essentials.DeviceInfo.Platform) se correlaciona con una cadena de constante que se asigna al sistema operativo. Los valores se pueden comprobar con el struct `DevicePlatform`:

- **DevicePlatform.iOS**: iOS
- **DevicePlatform.Android**: Android
- **DevicePlatform.UWP**: UWP
- **DevicePlatform.Unknown**: desconocido

## <a name="idioms"></a>Expresiones

[`DeviceInfo.Idiom`](xref:Xamarin.Essentials.DeviceInfo.Idiom) se correlaciona con una cadena de constante que se asigna al tipo de dispositivo en el que se ejecuta la aplicación. Los valores se pueden comprobar con el struct `DeviceIdiom`:

- **DeviceIdiom.Phone**: teléfono
- **DeviceIdiom.Tablet**: tableta
- **DeviceIdiom.Desktop**: escritorio
- **DeviceIdiom.TV**: TV
- **DeviceIdiom.Watch**: reloj
- **DeviceIdiom.Unknown**: desconocido

## <a name="device-type"></a>Tipo de dispositivo

`DeviceInfo.DeviceType` pone en correlación una enumeración para determinar si la aplicación se ejecuta en un dispositivo físico o virtual. Un dispositivo virtual es un simulador o emulador.

## <a name="platform-implementation-specifics"></a>Detalles de implementación de la plataforma

# <a name="ios"></a>[iOS](#tab/ios)

iOS no expone una API para que los desarrolladores obtengan el modelo del dispositivo iOS específico. En su lugar, se devuelve un identificador de hardware, como _iPhone10,6_ que hace referencia al iPhone X. Apple no proporciona una asignación de estos identificadores, pero se puede encontrar en [The iPhone Wiki](https://www.theiphonewiki.com/wiki/Models) y [Get iOS Model](https://github.com/dannycabrera/Get-iOS-Model) (fuentes no oficiales).

--------------

## <a name="api"></a>API

- [Código fuente de DeviceInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [Documentación de API para DeviceInfo](xref:Xamarin.Essentials.DeviceInfo)
