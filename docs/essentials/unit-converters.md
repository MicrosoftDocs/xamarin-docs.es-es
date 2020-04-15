---
title: Convertidores de unidades de Xamarin.Essentials
description: La clase UnitConverters de Xamarin.Essentials proporciona varios convertidores de unidades para ayudar a los desarrolladores que usan Xamarin.Essentials.
ms.assetid: 35DE2704-E730-4337-9476-66CD53376943
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: c07e0c7d9645c22f0d70c75fd7d8dffdec8cde04
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "77545038"
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
var celsius = UnitConverters.FahrenheitToCelsius(32.0);
```

Esta es una lista de las conversiones disponibles:

- FahrenheitToCelsius
- CelsiusToFahrenheit
- CelsiusToKelvin
- KelvinToCelsius
- MilesToMeters
- MilesToMeters
- KilometersToMiles
- MetersToInternationalFeet
- InternationalFeetToMeters
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
- KilogramsToPounds
- PoundsToKilograms
- StonesToPounds
- PoundsToStones

## <a name="api"></a>API

- [Código fuente de los convertidores de unidades](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/UnitConverters.shared.cs)
- [Documentación sobre la API de los convertidores de unidades](xref:Xamarin.Essentials.UnitConverters)
