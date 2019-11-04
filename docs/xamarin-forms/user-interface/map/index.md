---
title: Mapa de Xamarin. Forms
description: El control de mapa muestra un mapa y requiere el paquete de NuGet Xamarin. Forms. Maps.
ms.prod: xamarin
ms.assetid: B669B5EE-D24C-4C69-93E1-2CA5CC9108B5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/29/2019
ms.openlocfilehash: 013e126b76de08442327707cd0502f207826dad8
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2019
ms.locfileid: "73425592"
---
# <a name="xamarinforms-map"></a>Mapa de Xamarin. Forms

## <a name="initialization-and-configurationsetupmd"></a>[Inicialización y configuración](setup.md)

El paquete de NuGet [Xamarin. Forms. Maps](https://www.nuget.org/packages/Xamarin.Forms.Maps/) es necesario para usar la funcionalidad de Maps en una aplicación. Además, el acceso a la ubicación del usuario requiere que se hayan concedido permisos de ubicación a la aplicación.

## <a name="map-controlmapmd"></a>[Control de mapa](map.md)

El control [`Map`](xref:Xamarin.Forms.Maps.Map) es una vista multiplataforma para mostrar y anotar asignaciones. Usa el control de mapa nativo para cada plataforma, lo que proporciona una experiencia de mapas rápida y familiar a los usuarios.

## <a name="position-and-distanceposition-distancemd"></a>[Posición y distancia](position-distance.md)

El struct [`Position`](xref:Xamarin.Forms.Maps.Position) se utiliza normalmente al colocar una asignación y sus clavijas, y el [`Distance`](xref:Xamarin.Forms.Maps.Distance) struct que se puede usar opcionalmente al colocar un mapa.

## <a name="pinspinsmd"></a>[Chinchetas](pins.md)

El control [`Map`](xref:Xamarin.Forms.Maps.Map) permite marcar las ubicaciones con objetos [`Pin`](xref:Xamarin.Forms.Maps.Pin) . Un `Pin` es un marcador de mapa que abre una ventana de información cuando se puntea.

## <a name="polygons-and-polylinespolygonsmd"></a>[Polígonos y polilíneas](polygons.md)

los elementos `Polygon` y `Polyline` permiten resaltar áreas específicas en un mapa. Un `Polygon` es una forma totalmente adjunta que puede tener un trazo y un color de relleno. Un `Polyline` es una línea que no rodea completamente un área.

## <a name="geocodinggeocodermd"></a>[Codificación geográfica](geocoder.md)

La clase [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) convierte entre las direcciones de cadena y las coordenadas de latitud y longitud que se almacenan en objetos [`Position`](xref:Xamarin.Forms.Maps.Position) .

## <a name="launch-the-native-map-appnative-map-appmd"></a>[Inicio de la aplicación de asignación nativa](native-map-app.md)

La aplicación de asignación nativa en cada plataforma se puede iniciar desde una aplicación de Xamarin. Forms mediante la clase de `Launcher` de Xamarin. Essentials.
