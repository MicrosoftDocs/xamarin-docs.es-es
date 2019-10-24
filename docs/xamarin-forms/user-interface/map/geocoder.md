---
title: Geocodificación del mapa de Xamarin. Forms
description: En este artículo se explica cómo codificar y descodificar datos de mapas geocode mediante la clase de Geocoder de Xamarin. Forms. Maps.
ms.prod: xamarin
ms.assetid: DE7DB31A-8921-4614-8B49-DAEF1E7B03B3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/22/2019
ms.openlocfilehash: ce1f6751c0381ed41058784fbea3ebedefbdac6d
ms.sourcegitcommit: e4c23187874488ff55794d0e81a9bba30d2c2cd6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2019
ms.locfileid: "72778797"
---
# <a name="xamarinforms-map-geocoding"></a>Geocodificación del mapa de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

Xamarin. Forms. Maps proporciona la clase [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) , que convierte entre las direcciones de cadena y las coordenadas de latitud y longitud que se almacenan en objetos [`Position`](xref:Xamarin.Forms.Maps.Position) .

## <a name="geocode-an-address"></a>Codificación de una dirección en geocode

Una dirección postal se puede codificar en coordenadas de latitud y longitud mediante la creación de una instancia de [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) y la llamada al método [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*) en la instancia de `Geocoder`:

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder;

IEnumerable<Position> approximateLocations = await geoCoder.GetPositionsForAddressAsync("Pacific Ave, San Francisco, California");
Position position = approximateLocations.FirstOrDefault();
string coordinates = $"{position.Latitude}, {position.Longitude}";
```

El método [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*) toma un argumento `string` que representa la dirección y devuelve de forma asincrónica una colección de objetos [`Position`](xref:Xamarin.Forms.Maps.Position) que podrían representar la dirección.

## <a name="reverse-geocode-an-address"></a>Inverso de una dirección con geocode

Las coordenadas de latitud y longitud pueden estar invertidas geocodificadas en una dirección postal mediante la creación de una instancia de [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) y la llamada al método [`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*) en la instancia de `Geocoder`:

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder;

Position position = new Position(37.8044866, -122.4324132);
IEnumerable<string> possibleAddresses = await geoCoder.GetAddressesForPositionAsync(position);
string address = possibleAddresses.FirstOrDefault();
```

El método [`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*) toma un argumento [`Position`](xref:Xamarin.Forms.Maps.Position) formado por coordenadas de latitud y longitud, y devuelve de forma asincrónica una colección de cadenas que representan las direcciones cercanas a la posición.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [API del codificador](xref:Xamarin.Forms.Maps.Geocoder)
