---
title: Xamarin.Forms Control de mapa
description: El control de mapa es una vista multiplataforma para mostrar y anotar asignaciones. Usa el control de mapa nativo para cada plataforma, lo que proporciona una experiencia de mapas rápida y familiar a los usuarios.
ms.prod: xamarin
ms.assetid: 22C99029-0B16-43A6-BF58-26B48C4AED38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: df3189455102cedab232e0c629e63f65e242c9b5
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560057"
---
# <a name="no-locxamarinforms-map-control"></a>Xamarin.Forms Control de mapa

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

El [`Map`](xref:Xamarin.Forms.Maps.Map) control es una vista multiplataforma para mostrar y anotar asignaciones. Usa el control de asignación nativa para cada plataforma, lo que proporciona una experiencia de mapas rápida y familiar a los usuarios:

[![Captura de pantalla del control de mapa, en iOS y Android](map-images/map-default.png "Control de mapa")](map-images/map-default-large.png#lightbox "Control de mapa")

La [`Map`](xref:Xamarin.Forms.Maps.Map) clase define las siguientes propiedades que controlan la apariencia y el comportamiento de los mapas:

- [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser), de tipo `bool` , indica si la asignación muestra la ubicación actual del usuario.
- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource), de tipo `IEnumerable` , que especifica la colección de `IEnumerable` elementos que se van a mostrar.
- [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate), de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , que especifica la [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que se va a aplicar a cada elemento de la colección de elementos mostrados.
- `ItemTemplateSelector`, de tipo [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) , que especifica [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) que se usará para elegir un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objeto para un elemento en tiempo de ejecución.
- [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled), de tipo `bool` , determina si se permite desplazar el mapa.
- [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled), de tipo `bool` , determina si el mapa puede hacer zoom.
- `MapElements`, de tipo `IList<MapElement>` , representa la lista de elementos del mapa, como polígonos y polilíneas.
- [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType), de tipo [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) , indica el estilo de presentación del mapa.
- `MoveToLastRegionOnLayoutChange`, de tipo `bool` , controla si la región de mapa mostrada se moverá de su región actual a su región establecida anteriormente cuando se produzca un cambio de diseño.
- [`Pins`](xref:Xamarin.Forms.Maps.Map.Pins), de tipo `IList<Pin>` , representa la lista de marcadores en el mapa.
- `TrafficEnabled`, de tipo `bool` , indica si los datos de tráfico están superpuestos en el mapa.
- [`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion), de tipo [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) , devuelve la región de la asignación que se muestra actualmente.

Estas propiedades, con la excepción de las `MapElements` `Pins` propiedades, y `VisibleRegion` , están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de los enlaces de datos.

La [`Map`](xref:Xamarin.Forms.Maps.Map) clase también define un `MapClicked` evento que se desencadena cuando se puntea en la asignación. El `MapClickedEventArgs` objeto que acompaña al evento tiene una propiedad única denominada `Position` , de tipo [`Position`](xref:Xamarin.Forms.Maps.Position) . Cuando se desencadena el evento, la `Position` propiedad se establece en la ubicación de asignación que se ha punteado. Para obtener más información sobre el [`Position`](xref:Xamarin.Forms.Maps.Position) struct, consulte [asignación de posición y distancia](position-distance.md).

Para obtener información sobre [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource) las [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate) propiedades, y `ItemTemplateSelector` , vea [Mostrar una colección de PIN](pins.md#display-a-pin-collection).

## <a name="display-a-map"></a>Mostrar un mapa

[`Map`](xref:Xamarin.Forms.Maps.Map)Se puede mostrar si se agrega a un diseño o una página:

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
    <maps:Map x:Name="map" />
</ContentPage>
```

> [!NOTE]
> `xmlns`Se requiere una definición de espacio de nombres adicional para hacer referencia a Xamarin.Forms . Asigna controles. En el ejemplo anterior, `Xamarin.Forms.Maps` se hace referencia al espacio de nombres a través de la `maps` palabra clave.

El código de C# equivalente es el siguiente:

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Maps;

namespace WorkingWithMaps
{
    public class MapTypesPageCode : ContentPage
    {
        public MapTypesPageCode()
        {
            Map map = new Map();
            Content = map;
        }
    }
}
```

En este ejemplo se llama al [`Map`](xref:Xamarin.Forms.Maps.Map) constructor predeterminado, que centra el mapa en Roma:

[![Captura de pantalla del control de mapa con ubicación predeterminada, en iOS y Android](map-images/map-default.png "Control de mapa con ubicación predeterminada")](map-images/map-default-large.png#lightbox "Control de mapa con ubicación predeterminada")

Como alternativa, [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) se puede pasar un argumento a un [`Map`](xref:Xamarin.Forms.Maps.Map) constructor para establecer el punto central y el nivel de zoom del mapa cuando se carga. Para obtener más información, vea [Mostrar una ubicación específica en un mapa](#display-a-specific-location-on-a-map).

## <a name="map-types"></a>Tipos de asignación

La [`Map.MapType`](xref:Xamarin.Forms.Maps.Map.MapType) propiedad se puede establecer en un [`MapType`](xref:Xamarin.Forms.Maps.MapType) miembro de enumeración para definir el estilo de presentación del mapa. La enumeración `MapType` define los miembros siguientes:

- `Street` Especifica que se mostrará un mapa de calles.
- `Satellite` Especifica que se mostrará un mapa que contiene imágenes de satélite.
- `Hybrid` Especifica que se mostrará un mapa que combina una calle y datos satélite.

De forma predeterminada, un [`Map`](xref:Xamarin.Forms.Maps.Map) mostrará un mapa de calle si la [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) propiedad no está definida. Como alternativa, la `MapType` propiedad se puede establecer en uno de los [`MapType`](xref:Xamarin.Forms.Maps.MapType) miembros de enumeración:

```xaml
<maps:Map MapType="Satellite" />
```

El código de C# equivalente es el siguiente:

```csharp
Map map = new Map
{
    MapType = MapType.Satellite
};
```

Las capturas de pantallas siguientes muestran [`Map`](xref:Xamarin.Forms.Maps.Map) cuando la [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) propiedad está establecida en `Street` :

[![Captura de pantalla del control de mapa con el tipo de mapa de calle, en iOS y Android](map-images/maptype-street.png "Control de mapa con la calle mapType")](map-images/maptype-street-large.png#lightbox "Control de mapa con el tipo de asignación calle")

Las capturas de pantallas siguientes muestran [`Map`](xref:Xamarin.Forms.Maps.Map) cuando la [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) propiedad está establecida en `Satellite` :

[![Captura de pantalla del control de mapa con el tipo de asignación satélite, en iOS y Android](map-images/maptype-satellite.png "Control de mapa con el mapType satélite")](map-images/maptype-satellite-large.png#lightbox "Control de mapa con el tipo de asignación satélite")

Las capturas de pantallas siguientes muestran [`Map`](xref:Xamarin.Forms.Maps.Map) cuando la [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) propiedad está establecida en `Hybrid` :

[![Captura de pantalla del control de mapa con el tipo de mapa híbrido, en iOS y Android](map-images/maptype-hybrid.png "Control de mapa con el mapType híbrido")](map-images/maptype-hybrid-large.png#lightbox "Control de mapa con el tipo de mapa híbrido")

## <a name="display-a-specific-location-on-a-map"></a>Mostrar una ubicación específica en un mapa

La región de un mapa que se va a mostrar cuando se carga una asignación se puede establecer pasando un [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) argumento al [`Map`](xref:Xamarin.Forms.Maps.Map) constructor:

```xaml
<maps:Map>
    <x:Arguments>
        <maps:MapSpan>
            <x:Arguments>
                <maps:Position>
                    <x:Arguments>
                        <x:Double>36.9628066</x:Double>
                        <x:Double>-122.0194722</x:Double>
                    </x:Arguments>
                </maps:Position>
                <x:Double>0.01</x:Double>
                <x:Double>0.01</x:Double>
            </x:Arguments>
        </maps:MapSpan>
    </x:Arguments>
</maps:Map>
```

El código de C# equivalente es el siguiente:

```csharp
Position position = new Position(36.9628066, -122.0194722);
MapSpan mapSpan = new MapSpan(position, 0.01, 0.01);
Map map = new Map(mapSpan);
```

En este ejemplo se crea un [`Map`](xref:Xamarin.Forms.Maps.Map) objeto que muestra la región especificada por el [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) objeto. El `MapSpan` objeto se centra en la latitud y la longitud representada por un [`Position`](xref:Xamarin.Forms.Maps.Position) objeto y abarca los grados 0,01 latitud y 0,01 de longitud. Para obtener más información sobre el [`Position`](xref:Xamarin.Forms.Maps.Position) struct, consulte [asignación de posición y distancia](position-distance.md). Para obtener información sobre cómo pasar argumentos en XAML, vea [pasar argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md).

El resultado es que, cuando se muestra el mapa, se centra en una ubicación específica y abarca un número específico de grados de latitud y longitud:

[![Captura de pantalla del control de mapa con la ubicación especificada, en iOS y Android](map-images/map-region.png "Control de mapa con una ubicación especificada")](map-images/map-region-large.png#lightbox "Control de mapa con una ubicación especificada")

## <a name="create-a-mapspan-object"></a>Creación de un objeto MapSpan

Existen varios enfoques para crear [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) objetos. Un enfoque común es proporcionar los argumentos necesarios para el `MapSpan` constructor. Se trata de una latitud y una longitud representada por un [`Position`](xref:Xamarin.Forms.Maps.Position) objeto, y `double` valores que representan los grados de latitud y longitud que abarca `MapSpan` . Para obtener más información sobre el [`Position`](xref:Xamarin.Forms.Maps.Position) struct, consulte [asignación de posición y distancia](position-distance.md).

Como alternativa, hay tres métodos en la [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) clase que devuelven nuevos `MapSpan` objetos:

1. [`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude*) Devuelve un `MapSpan` con el mismo `LongitudeDegrees` que la instancia de la clase del método y un radio definido por `north` sus `south` argumentos y.
1. [`FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius*) Devuelve un `MapSpan` que se define por sus [`Position`](xref:Xamarin.Forms.Maps.Position) [`Distance`](xref:Xamarin.Forms.Maps.Distance) argumentos y.
1. [`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*) Devuelve un `MapSpan` con el mismo centro que la instancia de la clase del método, pero con un radio multiplicado por su `double` argumento.

Para obtener más información sobre el [`Distance`](xref:Xamarin.Forms.Maps.Distance) struct, consulte [asignación de posición y distancia](position-distance.md).

Una vez que se ha [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) creado un, se puede tener acceso a las propiedades siguientes para recuperar datos sobre ella:

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center), que representa el [`Position`](xref:Xamarin.Forms.Maps.Position) en el centro geográfico de `MapSpan` .
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees), que representa los grados de latitud que abarca `MapSpan` .
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees), que representa los grados de longitud que abarca `MapSpan` .
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius), que representa el `MapSpan` radio.

## <a name="move-the-map"></a>Movimiento del mapa

[`Map.MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*)Se puede llamar al método para cambiar la posición y el nivel de zoom de un mapa. Este método acepta un [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) argumento que define la región del mapa que se va a mostrar y su nivel de zoom.

En el código siguiente se muestra un ejemplo de cómo mover la región mostrada en un mapa:

```csharp
MapSpan mapSpan = MapSpan.FromCenterAndRadius(position, Distance.FromKilometers(0.444));
map.MoveToRegion(mapSpan);
```

## <a name="zoom-the-map"></a>Acercamiento/alejamiento del mapa

El nivel de zoom de un [`Map`](xref:Xamarin.Forms.Maps.Map) se puede cambiar sin alterar su ubicación. Esto puede realizarse mediante la interfaz de usuario de asignación o mediante programación llamando al [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) método con un [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) argumento que utiliza la ubicación actual como [`Position`](xref:Xamarin.Forms.Maps.Position) argumento:

```csharp
double zoomLevel = 0.5;
double latlongDegrees = 360 / (Math.Pow(2, zoomLevel));
if (map.VisibleRegion != null)
{
    map.MoveToRegion(new MapSpan(map.VisibleRegion.Center, latlongDegrees, latlongDegrees));
}
```

En este ejemplo, [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) se llama al método con un [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) argumento que especifica la ubicación actual del mapa, a través de [`Map.VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion) la propiedad y el nivel de zoom como grados de latitud y longitud. El resultado general es que se cambia el nivel de zoom del mapa, pero su ubicación no lo es. Un enfoque alternativo para implementar zoom en un mapa es usar el [`MapSpan.WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*) método para controlar el factor de zoom.

> [!IMPORTANT]
> El zoom de un mapa, ya sea a través de la interfaz de usuario del mapa o mediante programación, requiere que la [`Map.HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) propiedad sea `true` . Para obtener más información sobre esta propiedad, vea [deshabilitar zoom](#disable-zoom).

## <a name="customize-map-behavior"></a>Personalizar el comportamiento del mapa

El comportamiento de [`Map`](xref:Xamarin.Forms.Maps.Map) se puede personalizar estableciendo algunas de sus propiedades y controlando el `MapClicked` evento.

> [!NOTE]
> Se puede lograr una personalización del comportamiento de mapa adicional mediante la creación de un representador personalizado de mapa. Para obtener más información, consulte [Personalización de un Xamarin.Forms mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md).

### <a name="show-traffic-data"></a>Mostrar datos del tráfico

La [`Map`](xref:Xamarin.Forms.Maps.Map) clase define una `TrafficEnabled` propiedad de tipo `bool` . De forma predeterminada, esta propiedad es `false` , que indica que los datos de tráfico no se superpondrán en el mapa. Cuando esta propiedad está establecida en `true` , los datos de tráfico se superpondrán en el mapa. En el ejemplo siguiente se muestra cómo establecer esta propiedad:

```xaml
<maps:Map TrafficEnabled="true" />
```

El código de C# equivalente es el siguiente:

```csharp
Map map = new Map
{
    TrafficEnabled = true
};
```

### <a name="disable-scroll"></a>Deshabilitar desplazamiento

La [`Map`](xref:Xamarin.Forms.Maps.Map) clase define una [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) propiedad de tipo `bool` . De forma predeterminada, esta propiedad es `true` , que indica que la asignación puede desplazarse. Cuando esta propiedad se establece en `false` , el mapa no se desplazará. En el ejemplo siguiente se muestra cómo establecer esta propiedad:

```xaml
<maps:Map HasScrollEnabled="false" />
```

El código de C# equivalente es el siguiente:

```csharp
Map map = new Map
{
    HasScrollEnabled = false
};
```

### <a name="disable-zoom"></a>Deshabilitar zoom

La [`Map`](xref:Xamarin.Forms.Maps.Map) clase define una [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) propiedad de tipo `bool` . De forma predeterminada, esta propiedad es `true` , que indica que se puede realizar un zoom en el mapa. Cuando esta propiedad se establece en `false` , no se puede hacer zoom en el mapa. En el ejemplo siguiente se muestra cómo establecer esta propiedad:

```xaml
<maps:Map HasZoomEnabled="false" />
```

El código de C# equivalente es el siguiente:

```csharp
Map map = new Map
{
    HasZoomEnabled = false
};
```

### <a name="show-the-users-location"></a>Mostrar la ubicación del usuario

La [`Map`](xref:Xamarin.Forms.Maps.Map) clase define una [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser) propiedad de tipo `bool` . De forma predeterminada, esta propiedad es `false` , que indica que la asignación no muestra la ubicación actual del usuario. Cuando esta propiedad se establece en `true` , el mapa muestra la ubicación actual del usuario. En el ejemplo siguiente se muestra cómo establecer esta propiedad:

```xaml
<maps:Map IsShowingUser="true" />
```

El código de C# equivalente es el siguiente:

```csharp
Map map = new Map
{
    IsShowingUser = true
};
```

> [!IMPORTANT]
> En iOS, Android y el Plataforma universal de Windows, el acceso a la ubicación del usuario requiere la concesión de permisos de ubicación a la aplicación. Para obtener más información, consulte Configuración de la [plataforma](setup.md#platform-configuration).

### <a name="maintain-map-region-on-layout-change"></a>Mantener la región de mapa en el cambio de diseño

La [`Map`](xref:Xamarin.Forms.Maps.Map) clase define una `MoveToLastRegionOnLayoutChange` propiedad de tipo `bool` . De forma predeterminada, esta propiedad es `true` , que indica que la región de mapa mostrada pasará de su región actual a su región establecida anteriormente cuando se produzca un cambio de diseño, como en la rotación de dispositivos. Cuando esta propiedad está establecida en `false` , la región de mapa que se muestra permanecerá centrada cuando se produzca un cambio de diseño. En el ejemplo siguiente se muestra cómo establecer esta propiedad:

```xaml
<maps:Map MoveToLastRegionOnLayoutChange="false" />
```

El código de C# equivalente es el siguiente:

```csharp
Map map = new Map
{
    MoveToLastRegionOnLayoutChange = false
};
```

### <a name="map-clicks"></a>Clics de mapa

La [`Map`](xref:Xamarin.Forms.Maps.Map) clase define un `MapClicked` evento que se desencadena cuando se puntea la asignación. El `MapClickedEventArgs` objeto que acompaña al evento tiene una propiedad única denominada `Position` , de tipo [`Position`](xref:Xamarin.Forms.Maps.Position) . Cuando se desencadena el evento, la `Position` propiedad se establece en la ubicación de asignación que se ha punteado. Para obtener más información sobre el [`Position`](xref:Xamarin.Forms.Maps.Position) struct, consulte [asignación de posición y distancia](position-distance.md).

En el ejemplo de código siguiente se muestra un controlador de eventos para el `MapClicked` evento:

```csharp
void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

En este ejemplo, el `OnMapClicked` controlador de eventos genera la latitud y la longitud que representa la ubicación de asignación punteada. El controlador de eventos se puede registrar con el `MapClicked` evento de la siguiente manera:

```xaml
<maps:Map MapClicked="OnMapClicked" />
```

El código de C# equivalente es el siguiente:

```csharp
Map map = new Map();
map.MapClicked += OnMapClicked;
```

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Maps](/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Posición y distancia del mapa](position-distance.md)
- [Personalización de un Xamarin.Forms mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)
- [Paso de argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md)