---
title: Xamarin.Forms Geocodificación de mapas
description: En este artículo se explica cómo codificar y descodificar datos de mapas geocode mediante el Xamarin.Forms . Asigna la clase de Geocoder.
ms.prod: xamarin
ms.assetid: DE7DB31A-8921-4614-8B49-DAEF1E7B03B3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/22/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 12ba6e8f9bb1c547ad5a15e2e79f74c29b74ad12
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375452"
---
# <a name="xamarinforms-map-geocoding"></a>Xamarin.Forms Geocodificación de mapas

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/workingwithmaps)

El [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) espacio de nombres proporciona una [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) clase, que convierte entre las direcciones de cadena y las coordenadas de latitud y longitud que se almacenan en [`Position`](xref:Xamarin.Forms.Maps.Position) objetos. Para obtener más información sobre el [`Position`](xref:Xamarin.Forms.Maps.Position) struct, vea [asignación de posición y distancia](position-distance.md).

> [!NOTE]
> Se disponibles una API de geocodificación alternativa en Xamarin.Essentials . La Xamarin.Essentials `Geocoding` API ofrece datos de dirección estructurados al geocodificar direcciones, en contraposición a las cadenas devueltas por esta API. Para obtener más información, vea [ Xamarin.Essentials : geocodificación](~/essentials/geocoding.md).

## <a name="geocode-an-address"></a>Codificación de una dirección en geocode

Una dirección postal se puede codificar en coordenadas de latitud y longitud mediante la creación de una [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) instancia de y la llamada al [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*) método en la `Geocoder` instancia de:

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder = new Geocoder();

IEnumerable<Position> approximateLocations = await geoCoder.GetPositionsForAddressAsync("Pacific Ave, San Francisco, California");
Position position = approximateLocations.FirstOrDefault();
string coordinates = $"{position.Latitude}, {position.Longitude}";
```

El [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*) método toma un `string` argumento que representa la dirección y devuelve de forma asincrónica una colección de [`Position`](xref:Xamarin.Forms.Maps.Position) objetos que podrían representar la dirección.

## <a name="reverse-geocode-an-address"></a>Inverso de una dirección con geocode

Las coordenadas de latitud y longitud pueden estar invertidas geocodificadas en una dirección postal mediante la creación de una [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) instancia de y la llamada al [`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*) método en la `Geocoder` instancia de:

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder = new Geocoder();

Position position = new Position(37.8044866, -122.4324132);
IEnumerable<string> possibleAddresses = await geoCoder.GetAddressesForPositionAsync(position);
string address = possibleAddresses.FirstOrDefault();
```

El [`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*) método toma un [`Position`](xref:Xamarin.Forms.Maps.Position) argumento compuesto de coordenadas de latitud y longitud, y devuelve de forma asincrónica una colección de cadenas que representan las direcciones cercanas a la posición.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Maps](/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Xamarin.Forms Posición y distancia del mapa](position-distance.md)
- [API del codificador](xref:Xamarin.Forms.Maps.Geocoder)