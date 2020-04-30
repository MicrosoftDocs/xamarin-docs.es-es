---
title: Posición y distancia del mapa de Xamarin. Forms
description: El espacio de nombres de Xamarin. Forms. Maps contiene un struct de posición que se usa normalmente al colocar una asignación y sus clavijas, y una estructura de distancia que se puede usar opcionalmente al colocar un mapa.
ms.prod: xamarin
ms.assetid: 2F4EA3D2-1351-40AD-A71D-CF7F1F18F1E8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2020
ms.openlocfilehash: 567e1b5620378f0c1b64d17c56c8fb451f18abc3
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517445"
---
# <a name="xamarinforms-map-position-and-distance"></a>Posición y distancia del mapa de Xamarin. Forms

[![Descargar el](~/media/shared/download.png) ejemplo descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

El [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) espacio de nombres [`Position`](xref:Xamarin.Forms.Maps.Position) contiene un struct que se usa normalmente al colocar una asignación y sus clavijas [`Distance`](xref:Xamarin.Forms.Maps.Distance) , y un struct que se puede usar opcionalmente al colocar un mapa.

## <a name="position"></a>Posición

La [`Position`](xref:Xamarin.Forms.Maps.Position) estructura encapsula una posición almacenada como valores de latitud y longitud. Este struct define dos propiedades de solo lectura:

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude), de tipo `double`, que representa la latitud de la posición en grados decimales.
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude), de tipo `double`, que representa la longitud de la posición en grados decimales.

[`Position`](xref:Xamarin.Forms.Maps.Position)los objetos se crean con `Position` el constructor, que requiere que los argumentos de latitud y `double` longitud se especifiquen como valores:

```csharp
Position position = new Position(36.9628066, -122.0194722);
```

Al crear un `Position` objeto, el valor de latitud se unirá entre-90,0 y 90,0, y el valor de longitud se fijará entre-180,0 y 180,0.

> [!NOTE]
> La `GeographyUtils` clase tiene un `ToRadians` método de extensión que convierte un `double` valor de grados a radianes y un `ToDegrees` método de extensión que convierte un `double` valor de radianes en grados.

## <a name="distance"></a>Distancia

El [`Distance`](xref:Xamarin.Forms.Maps.Distance) struct encapsula una distancia almacenada como un `double` valor, que representa la distancia en metros. Este struct define tres propiedades de solo lectura:

- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers), de tipo `double`, que representa la distancia en kilómetros que abarca `Distance`.
- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters), de tipo `double`, que representa la distancia en metros que abarca `Distance`.
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles), de tipo `double`, que representa la distancia en millas que abarca `Distance`.

[`Distance`](xref:Xamarin.Forms.Maps.Distance)los objetos se pueden crear con `Distance` el constructor, que requiere un argumento de metros especificado `double`como:

```csharp
Distance distance = new Distance(1450.5);
```

Como alternativa, [`Distance`](xref:Xamarin.Forms.Maps.Distance) los objetos se pueden crear con [`FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers*)los [`FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters*)métodos [`FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles*)de generador `BetweenPositions` ,, y:

```csharp
Distance distance1 = Distance.FromKilometers(1.45); // argument represents the number of kilometers
Distance distance2 = Distance.FromMeters(1450.5);   // argument represents the number of meters
Distance distance3 = Distance.FromMiles(0.969);     // argument represents the number of miles
Distance distance4 = Distance.BetweenPositions(position1, position2);
```

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
