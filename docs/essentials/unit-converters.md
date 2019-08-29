---
title: Convertidores de unidades de Xamarin.Essentials
description: La clase UnitConverters de Xamarin.Essentials proporciona varios convertidores de unidades para ayudar a los desarrolladores que usan Xamarin.Essentials.
ms.assetid: 35DE2704-E730-4337-9476-66CD53376943
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: be560a156647274932265597ae5b83f22255d061
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120135"
---
# <a name="xamarinessentials-unit-converters"></a>Xamarin.Essentials: convertidores de unidades

La clase **UnitConverters** proporciona varios convertidores de unidades para ayudar a los desarrolladores que usan Xamarin.Essentials.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-unit-converters"></a>Uso de convertidores de unidades

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Todos los convertidores de unidades están disponibles mediante el uso de la clase estática `UnitConverters` de Xamarin.Essentials. Por ejemplo, puede convertir fácilmente grados Fahrenheit a Celsius.

```csharp
var celcius = UnitConverters.FahrenheitToCelsius(32.0);
```

Esta es una lista de las conversiones disponibles:

- FahrenheitToCelsius
- CelsiusToFahrenheit
- CelsiusToKelvin
- KelvinToCelsius
- MilesToMeters
- MilesToMeters
- KilometersToMiles
- DegreesToRadians
- RadiansToDegrees
- DegreesPerSecondToRadiansPerSecond
- RadiansPerSecondToDegreesPerSecond
- DegreesPerSecondToHertz
- DegreesPerSecondToHertz
- HertzToDegreesPerSecond
- HertzToDegreesPerSecond
- KilopascalsToHectopascals
- HectopascalsToKilopascals
- KilopascalsToPascals
- HectopascalsToKilopascals
- AtmospheresToPascals
- PascalsToAtmospheres
- CoordinatesToMiles
- CoordinatesToKilometers

## <a name="api"></a>API

- [Código fuente de los convertidores de unidades](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/UnitConverters.shared.cs)
- [Documentación sobre la API de los convertidores de unidades](xref:Xamarin.Essentials.UnitConverters)
