---
title: Xamarin.Forms Polígonos de mapa, polilíneas y círculos
description: En este artículo se explica cómo crear polígonos, polilíneas y círculos en una Xamarin.Forms instancia de mapa.
ms.prod: xamarin
ms.assetid: CDAF0B02-1AA8-4AD6-94A7-ABFC18006A2D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1cb0b229af4ac819b33602e2249f20d308ec111c
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374789"
---
# <a name="no-locxamarinforms-map-polygons-and-polylines"></a>Xamarin.Forms Rectángulos de mapa y polilíneas

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/workingwithmaps)

`Polygon``Polyline` `Circle` los elementos, y permiten resaltar áreas específicas en un mapa. Un `Polygon` es una forma totalmente adjunta que puede tener un trazo y un color de relleno. Una `Polyline` es una línea que no rodea completamente un área. Un `Circle` resalta un área circular del mapa:

[!["Captura de pantalla de un polígono de mapa y Polyline, en iOS y Android"](polygons-images/polygon-polyline.png "Polígono y Polyline en un mapa")](polygons-images/polygon-polyline-large.png#lightbox "Polígono y Polyline en un mapa") 
 [ !["Captura de pantalla de un círculo de mapa, en iOS y Android"](polygons-images/circle.png "Círculo en un mapa")](polygons-images/circle-large.png#lightbox "Círculo en un mapa")

Las `Polygon` `Polyline` clases, y se `Circle` derivan de la `MapElement` clase, que expone las siguientes propiedades enlazables:

- `StrokeColor` es un `Color` objeto que determina el color de la línea.
- `StrokeWidth` es un `float` objeto que determina el ancho de línea.

La `Polygon` clase define una propiedad enlazable adicional:

- `FillColor` es un `Color` objeto que determina el color de fondo del polígono.

Además, las `Polygon` clases y `Polyline` definen una `GeoPath` propiedad, que es una lista de [`Position`](xref:Xamarin.Forms.Maps.Position) objetos que especifican los puntos de la forma.

La `Circle` clase define las siguientes propiedades enlazables:

- `Center` es un [`Position`](xref:Xamarin.Forms.Maps.Position) objeto que define el centro del círculo, en latitud y longitud.
- `Radius` es un [`Distance`](xref:Xamarin.Forms.Maps.Distance) objeto que define el radio del círculo en metros, kilómetros o millas.
- `FillColor` es una `Color` propiedad que determina el color del perímetro del círculo.

> [!NOTE]
> Si `StrokeColor` no se especifica la propiedad, el trazo se establecerá de forma predeterminada en negro. Si `FillColor` no se especifica la propiedad, el relleno se establecerá de forma predeterminada en transparente. Por lo tanto, si no se especifica ninguna propiedad, la forma tendrá un contorno negro sin relleno.

## <a name="create-a-polygon"></a>Crear un polígono

`Polygon`Se puede Agregar un objeto a un mapa creando una instancia de él y agregándolo a la colección del mapa `MapElements` . Esto se puede lograr en XAML de la siguiente manera:

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

`StrokeColor` `StrokeWidth` Se especifican las propiedades y para personalizar el contorno del polígono. El `FillColor` valor de propiedad coincide con el valor de la `StrokeColor` propiedad, pero tiene un valor alfa especificado para que sea transparente, lo que permite que el mapa subyacente sea visible a través de la forma. La `GeoPath` propiedad contiene una lista de `Position` objetos que definen las coordenadas geográficas de los puntos del polígono. Un `Polygon` objeto se representa en el mapa una vez que se ha agregado a la `MapElements` colección de `Map` .

> [!NOTE]
> Un `Polygon` es una forma totalmente adjunta. Los puntos primero y último se conectarán automáticamente si no coinciden.

## <a name="create-a-polyline"></a>Crear una polilínea

`Polyline`Se puede Agregar un objeto a un mapa creando una instancia de él y agregándolo a la colección del mapa `MapElements` . Esto se puede lograr en XAML de la siguiente manera:

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

`StrokeColor` `StrokeWidth` Se especifican las propiedades y para personalizar la línea. La `GeoPath` propiedad contiene una lista de `Position` objetos que definen las coordenadas geográficas de los puntos de polilínea. Un `Polyline` objeto se representa en el mapa una vez que se ha agregado a la `MapElements` colección de `Map` .

## <a name="create-a-circle"></a>Crear un círculo

`Circle`Se puede Agregar un objeto a un mapa creando una instancia de él y agregándolo a la colección del mapa `MapElements` . Esto se puede lograr en XAML de la siguiente manera:

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
      <maps:Map>
          <maps:Map.MapElements>
              <maps:Circle StrokeColor="#88FF0000"
                           StrokeWidth="8"
                           FillColor="#88FFC0CB">
                  <maps:Circle.Center>
                      <maps:Position>
                          <x:Arguments>
                              <x:Double>37.79752</x:Double>
                              <x:Double>-122.40183</x:Double>
                          </x:Arguments>
                      </maps:Position>
                  </maps:Circle.Center>
                  <maps:Circle.Radius>
                      <maps:Distance>
                          <x:Arguments>
                              <x:Double>250</x:Double>
                          </x:Arguments>
                      </maps:Distance>
                  </maps:Circle.Radius>
              </maps:Circle>             
          </maps:Map.MapElements>
          ...
      </maps:Map>
</ContentPage>
```

El código de C# equivalente es el siguiente:

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map();

// Instantiate a Circle
Circle circle = new Circle
{
    Center = new Position(37.79752, -122.40183),
    Radius = new Distance(250),
    StrokeColor = Color.FromHex("#88FF0000"),
    StrokeWidth = 8,
    FillColor = Color.FromHex("#88FFC0CB")
};

// Add the Circle to the map's MapElements collection
map.MapElements.Add(circle);
```

La ubicación de `Circle` en el mapa viene determinada por el valor de las `Center` propiedades y `Radius` . La `Center` propiedad define el centro del círculo, en latitud y longitud, mientras que la `Radius` propiedad define el radio del círculo en metros. `StrokeColor` `StrokeWidth` Se especifican las propiedades y para personalizar el contorno del círculo. El `FillColor` valor de propiedad especifica el color del perímetro del círculo. Ambos valores de color especifican un canal alfa, lo que permite que el mapa subyacente sea visible a través del círculo. El `Circle` objeto se representa en el mapa una vez que se ha agregado a la `MapElements` colección de `Map` .

> [!NOTE]
> La `GeographyUtils` clase tiene un `ToCircumferencePositions` método de extensión que convierte un `Circle` objeto (que define `Center` `Radius` los valores de las propiedades y) en una lista de `Position` objetos que constituyen las coordenadas de latitud y longitud del perímetro del círculo.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Maps](/samples/xamarin/xamarin-forms-samples/workingwithmaps)