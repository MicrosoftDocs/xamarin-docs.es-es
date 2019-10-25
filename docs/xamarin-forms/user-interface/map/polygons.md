---
title: Polígonos y polilíneas del mapa de Xamarin. Forms
description: En este artículo se explica cómo crear polígonos y polilíneas en una instancia de mapa de Xamarin. Forms.
ms.prod: xamarin
ms.assetid: CDAF0B02-1AA8-4AD6-94A7-ABFC18006A2D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 09/20/2019
ms.openlocfilehash: 42c00a060e0477aff4ea02f6213fa751b2adf4dd
ms.sourcegitcommit: 5c22097bed2a8d51ecaf6ca197bf4d449dfe1377
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/23/2019
ms.locfileid: "72810488"
---
# <a name="xamarinforms-map-polygons-and-polylines"></a>Polígonos y polilíneas del mapa de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[!["demostración de polígono y polilínea en iOS y Android"](polygons-images/polygon-app-cropped.png)](polygons-images/polygon-app.png#lightbox)

los elementos `Polygon` y `Polyline` permiten resaltar áreas específicas en un mapa. Un `Polygon` es una forma totalmente adjunta que puede tener un trazo y un color de relleno. Un `Polyline` es una línea que no rodea completamente un área.

> [!NOTE]
> Los ejemplos de `Polygon` y `Polyline` se encuentran en **PolygonsPage** en el proyecto de ejemplo.

Las clases `Polygon` y `Polyline` derivan de `MapElement`, que expone las siguientes propiedades de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) :

- `StrokeColor` es una propiedad `Color` que determina el color de la línea.
- `StrokeWidth` es una propiedad `float` que determina el ancho de línea.
- `Geopath` se define en `Polygon` y `Polyline`, y es una lista de objetos [`Position`](xref:Xamarin.Forms.Maps.Position) que especifican los puntos de la forma.

La clase `Polygon` define una propiedad adicional:

- `FillColor` es una propiedad `Color` que determina el color de fondo del polígono.

> [!NOTE]
> Si no se especifica la propiedad `StrokeColor`, el trazo se establecerá de forma predeterminada en negro. Si no se especifica la propiedad `FillColor`, el relleno se establecerá de forma predeterminada en transparente. Por lo tanto, si no se especifica ninguna propiedad, la forma tendrá un contorno negro sin relleno.

## <a name="create-a-polygon"></a>Crear un polígono

Se puede Agregar un objeto `Polygon` a un mapa creando una instancia de él y agregándolo a la colección de `MapElements` del mapa. Esto se puede lograr en XAML de la siguiente manera:

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
     <maps:Map>
         <maps:Map.MapElements>
             <maps:Polygon StrokeColor="#FF9900"
                           StrokeWidth="8"
                           FillColor="#88FF9900">
                 <maps:Polygon.Geopath>
                     <maps:Position>
                         <x:Arguments>
                             <x:Double>47.6368678</x:Double>
                             <x:Double>-122.137305</x:Double>
                         </x:Arguments>
                     </maps:Position>
                     ...
                 </maps:Polygon.Geopath>
             </maps:Polygon>
         </maps:Map.MapElements>
     </maps:Map>
</ContentPage>
```

El código de C# equivalente es el siguiente:

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map
{
  // ...
};

// instantiate a polygon
Polygon polygon = new Polygon
{
    StrokeWidth = 8,
    StrokeColor = Color.FromHex("#1BA1E2"),
    FillColor = Color.FromHex("#881BA1E2"),
    Geopath =
    {
        new Position(47.6368678, -122.137305),
        new Position(47.6368894, -122.134655),
        new Position(47.6359424, -122.134655),
        new Position(47.6359496, -122.1325521),
        new Position(47.6424124, -122.1325199),
        new Position(47.642463,  -122.1338932),
        new Position(47.6406414, -122.1344833),
        new Position(47.6384943, -122.1361248),
        new Position(47.6372943, -122.1376912)
    }
};

// add the polygon to the map's MapElements collection
map.MapElements.Add(polygon);
```

Se especifican las propiedades `StrokeColor` y `StrokeWidth` para personalizar el contorno del polígono. El valor de la propiedad `FillColor` coincide con el valor de la propiedad `StrokeColor` pero tiene un valor alfa especificado para que sea transparente, lo que permite que el mapa subyacente sea visible a través de la forma. La propiedad `GeoPath` contiene una lista de objetos de `Position` que definen las coordenadas geográficas de los puntos de polígono. Un objeto `Polygon` se representa en el mapa una vez que se ha agregado a la colección `MapElements` de la `Map`.

> [!NOTE]
> Un `Polygon` es una forma totalmente adjunta. Los puntos primero y último se conectarán automáticamente si no coinciden.

## <a name="create-a-polyline"></a>Crear una polilínea

Se puede Agregar un objeto `Polyline` a un mapa creando una instancia de él y agregándolo a la colección de `MapElements` del mapa. Esto se puede lograr en XAML de la siguiente manera:

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
     <maps:Map>
         <maps:Map.MapElements>
             <maps:Polyline StrokeColor="Blue"
                            StrokeWidth="12">
                 <maps:Polyline.Geopath>
                     <maps:Position>
                         <x:Arguments>
                             <x:Double>47.6381401</x:Double>
                             <x:Double>-122.1317367</x:Double>
                         </x:Arguments>
                     </maps:Position>
                     ...
                 </maps:Polyline.Geopath>
             </maps:Polyline>
         </maps:Map.MapElements>
     </maps:Map>
</ContentPage>
```

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map
{
  // ...
};
// instantiate a polyline
Polyline polyline = new Polyline
{
    StrokeColor = Color.Blue,
    StrokeWidth = 12,
    Geopath =
    {
        new Position(47.6381401, -122.1317367),
        new Position(47.6381473, -122.1350841),
        new Position(47.6382847, -122.1353094),
        new Position(47.6384582, -122.1354703),
        new Position(47.6401136, -122.1360819),
        new Position(47.6403883, -122.1364681),
        new Position(47.6407426, -122.1377019),
        new Position(47.6412558, -122.1404056),
        new Position(47.6414148, -122.1418647),
        new Position(47.6414654, -122.1432702)
    }
};

// add the polyline to the map's MapElements collection
map.MapElements.Add(polyline);
```

Se especifican las propiedades `StrokeColor` y `StrokeWidth` para personalizar la línea. La propiedad `GeoPath` contiene una lista de objetos de `Position` que definen las coordenadas geográficas de los puntos de polilínea. Un objeto `Polyline` se representa en el mapa una vez que se ha agregado a la colección `MapElements` de la `Map`.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
