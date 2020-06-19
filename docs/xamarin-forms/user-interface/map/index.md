---
title: Mapa de Xamarin.Forms
description: El control de mapa muestra un mapa y requiere Xamarin.Forms . Asigna el paquete NuGet.
ms.prod: xamarin
ms.assetid: B669B5EE-D24C-4C69-93E1-2CA5CC9108B5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/29/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2461ffa8168207e6a57fae005f752be48772a34a
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139833"
---
# <a name="xamarinforms-map"></a>Mapa de Xamarin.Forms

## <a name="initialization-and-configuration"></a>[Inicialización y configuración](setup.md)

El [ Xamarin.Forms . ](https://www.nuget.org/packages/Xamarin.Forms.Maps/)El paquete NuGet de Maps es necesario para usar la funcionalidad de Maps en una aplicación. Además, el acceso a la ubicación del usuario requiere que se hayan concedido permisos de ubicación a la aplicación.

## <a name="map-control"></a>[Control de mapa](map.md)

El [`Map`](xref:Xamarin.Forms.Maps.Map) control es una vista multiplataforma para mostrar y anotar asignaciones. Usa el control de mapa nativo para cada plataforma, lo que proporciona una experiencia de mapas rápida y familiar a los usuarios.

## <a name="position-and-distance"></a>[Posición y distancia](position-distance.md)

La [`Position`](xref:Xamarin.Forms.Maps.Position) estructura se usa normalmente al colocar una asignación y sus clavijas, y el [`Distance`](xref:Xamarin.Forms.Maps.Distance) struct que se puede usar opcionalmente al colocar un mapa.

## <a name="pins"></a>[Marcas](pins.md)

El [`Map`](xref:Xamarin.Forms.Maps.Map) control permite marcar ubicaciones con [`Pin`](xref:Xamarin.Forms.Maps.Pin) objetos. Un `Pin` es un marcador de mapa que abre una ventana de información cuando se puntea.

## <a name="polygons-polylines-and-circles"></a>[Polígonos, polilíneas y círculos](polygons.md)

`Polygon``Polyline` `Circle` los elementos, y permiten resaltar áreas específicas en un mapa. Un `Polygon` es una forma totalmente adjunta que puede tener un trazo y un color de relleno. Una `Polyline` es una línea que no rodea completamente un área. Un `Circle` resalta un área circular del mapa.

## <a name="geocoding"></a>[Codificación geográfica](geocoder.md)

La [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) clase convierte entre las direcciones de cadena y las coordenadas de latitud y longitud que se almacenan en los [`Position`](xref:Xamarin.Forms.Maps.Position) objetos.

## <a name="launch-the-native-map-app"></a>[Inicio de la aplicación de mapa nativa](native-map-app.md)

La aplicación de asignación nativa en cada plataforma se puede iniciar desde una Xamarin.Forms aplicación mediante la Xamarin.Essentials `Launcher` clase.
