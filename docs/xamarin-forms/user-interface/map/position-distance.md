---
title: Posición y distancia del mapa de Xamarin. Forms
description: El espacio de nombres de Xamarin. Forms. Maps contiene un struct de posición que se usa normalmente al colocar una asignación y sus clavijas, y una estructura de distancia que se puede usar opcionalmente al colocar un mapa.
ms.prod: xamarin
ms.assetid: 2F4EA3D2-1351-40AD-A71D-CF7F1F18F1E8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/30/2019
ms.openlocfilehash: a68eab7bb3e6da764f5a35af4461d6af2be50ebe
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426286"
---
# <a name="xamarinforms-map-position-and-distance"></a>Posición y distancia del mapa de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

El espacio de nombres [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) contiene un [`Position`](xref:Xamarin.Forms.Maps.Position) struct que se usa normalmente al colocar una asignación y sus clavijas, y un struct [`Distance`](xref:Xamarin.Forms.Maps.Distance) que se puede usar opcionalmente al colocar un mapa.

## <a name="position"></a>Posición

La estructura [`Position`](xref:Xamarin.Forms.Maps.Position) encapsula una posición almacenada como valores de latitud y longitud. Este struct define dos propiedades de solo lectura:

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude), de tipo `double`, que representa la latitud de la posición en grados decimales.
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude), de tipo `double`, que representa la longitud de la posición en grados decimales.

[`Position`](xref:Xamarin.Forms.Maps.Position) objetos se crean con el constructor `Position`, que requiere los argumentos latitud y longitud especificados como valores `double`:

```csharp
Position position = new Position(36.9628066, -122.0194722);
```

> [!NOTE]
> Al crear un objeto de `Position`, el valor de latitud se unirá entre-90,0 y 90,0, y el valor de longitud se fijará entre-180,0 y 180,0.

## <a name="distance"></a>Posición

La estructura [`Distance`](xref:Xamarin.Forms.Maps.Distance) encapsula una distancia almacenada como un valor `double`, que representa la distancia en metros. Este struct define tres propiedades de solo lectura:

- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers), de tipo `double`, que representa la distancia en kilómetros que abarca la `Distance`.
- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters), de tipo `double`, que representa la distancia en metros que abarca la `Distance`.
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles), de tipo `double`, que representa la distancia en millas que abarca la `Distance`.

[`Distance`](xref:Xamarin.Forms.Maps.Distance) objetos se pueden crear con el constructor `Distance`, que requiere un argumento metros especificado como `double`:

```csharp
Distance distance = new Distance(1450.5);
```

Como alternativa, se pueden crear [`Distance`](xref:Xamarin.Forms.Maps.Distance) objetos con los métodos de generador [`FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers*), [`FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters*)y [`FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles*) :

```csharp
Distance distance1 = Distance.FromKilometers(1.45); // argument represents the number of kilometers
Distance distance2 = Distance.FromMeters(1450.5);   // argument represents the number of meters
Distance distance3 = Distance.FromMiles(0.969);     // argument represents the number of miles
```

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
