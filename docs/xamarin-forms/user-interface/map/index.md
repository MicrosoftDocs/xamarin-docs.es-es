---
title: Mapa de Xamarin. Forms
description: El control de mapa muestra un mapa y requiere el paquete de NuGet Xamarin. Forms. Maps.
ms.prod: xamarin
ms.assetid: B669B5EE-D24C-4C69-93E1-2CA5CC9108B5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/29/2019
ms.openlocfilehash: ffd8f7cc31707d09bb3442c180a867d31afcef0f
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517492"
---
# <a name="xamarinforms-map"></a>Mapa de Xamarin. Forms

## <a name="initialization-and-configuration"></a>[Inicialización y configuración](setup.md)

El paquete de NuGet [Xamarin. Forms. Maps](https://www.nuget.org/packages/Xamarin.Forms.Maps/) es necesario para usar la funcionalidad de Maps en una aplicación. Además, el acceso a la ubicación del usuario requiere que se hayan concedido permisos de ubicación a la aplicación.

## <a name="map-control"></a>[Control de mapa](map.md)

El [`Map`](xref:Xamarin.Forms.Maps.Map) control es una vista multiplataforma para mostrar y anotar asignaciones. Usa el control de mapa nativo para cada plataforma, lo que proporciona una experiencia de mapas rápida y familiar a los usuarios.

## <a name="position-and-distance"></a>[Posición y distancia](position-distance.md)

La [`Position`](xref:Xamarin.Forms.Maps.Position) estructura se usa normalmente al colocar una asignación y sus clavijas, y [`Distance`](xref:Xamarin.Forms.Maps.Distance) el struct que se puede usar opcionalmente al colocar un mapa.

## <a name="pins"></a>[Dobla](pins.md)

El [`Map`](xref:Xamarin.Forms.Maps.Map) control permite marcar ubicaciones con [`Pin`](xref:Xamarin.Forms.Maps.Pin) objetos. Un `Pin` es un marcador de mapa que abre una ventana de información cuando se puntea.

## <a name="polygons-polylines-and-circles"></a>[Polígonos, polilíneas y círculos](polygons.md)

`Polygon`los `Polyline`elementos, `Circle` y permiten resaltar áreas específicas en un mapa. Un `Polygon` es una forma totalmente adjunta que puede tener un trazo y un color de relleno. Una `Polyline` es una línea que no rodea completamente un área. Un `Circle` resalta un área circular del mapa.

## <a name="geocoding"></a>[Codificación geográfica](geocoder.md)

La [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) clase convierte entre las direcciones de cadena y las coordenadas de latitud y longitud que se [`Position`](xref:Xamarin.Forms.Maps.Position) almacenan en los objetos.

## <a name="launch-the-native-map-app"></a>[Inicio de la aplicación de asignación nativa](native-map-app.md)

La aplicación de asignación nativa en cada plataforma se puede iniciar desde una aplicación de Xamarin. Forms mediante la `Launcher` clase Xamarin. Essentials.
