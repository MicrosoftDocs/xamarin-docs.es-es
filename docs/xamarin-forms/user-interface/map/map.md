---
title: Control de mapa de Xamarin. Forms
description: El control de mapa es una vista multiplataforma para mostrar y anotar asignaciones. Usa el control de mapa nativo para cada plataforma, lo que proporciona una experiencia de mapas rápida y familiar a los usuarios.
ms.prod: xamarin
ms.assetid: 22C99029-0B16-43A6-BF58-26B48C4AED38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/29/2019
ms.openlocfilehash: 1cfda90360557af1160d421f18807f8b534967a8
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426352"
---
# <a name="xamarinforms-map-control"></a>Control de mapa de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

El control [`Map`](xref:Xamarin.Forms.Maps.Map) es una vista multiplataforma para mostrar y anotar asignaciones. Usa el control de asignación nativa para cada plataforma, lo que proporciona una experiencia de mapas rápida y familiar a los usuarios:

[![Captura de pantalla del control de mapa, en iOS y Android](map-images/map-default.png "Control de mapa")](map-images/map-default-large.png#lightbox "Control de mapa")

La clase [`Map`](xref:Xamarin.Forms.Maps.Map) define las siguientes propiedades que controlan la apariencia y el comportamiento de los mapas:

- [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser), de tipo `bool`, indica si la asignación muestra la ubicación actual del usuario.
- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource), de tipo `IEnumerable`, que especifica la colección de elementos `IEnumerable` que se van a mostrar.
- [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate), de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que especifica el [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que se va a aplicar a cada elemento de la colección de elementos mostrados.
- `ItemTemplateSelector`, de tipo [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector), que especifica el [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) que se usará para elegir un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) para un elemento en tiempo de ejecución.
- [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled), de tipo `bool`, determina si se permite el desplazamiento del mapa.
- [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled), de tipo `bool`, determina si la asignación puede hacer zoom.
- `MapElements`, de tipo `IList<MapElement>`, representa la lista de elementos del mapa, como polígonos y polilíneas.
- [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType), de tipo [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType), indica el estilo de presentación del mapa.
- `MoveToLastRegionOnLayoutChange`, de tipo `bool`, controla si la región de mapa mostrada se moverá de su región actual a su región establecida anteriormente cuando se produzca un cambio de diseño.
- [`Pins`](xref:Xamarin.Forms.Maps.Map.Pins), de tipo `IList<Pin>`, representa la lista de PIN en el mapa.
- [`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion), de tipo [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan), devuelve la región de la asignación que se muestra actualmente.

Estas propiedades, con la excepción de las propiedades `MapElements`, `Pins`y `VisibleRegion`, están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que pueden ser destinos de los enlaces de datos.

La clase [`Map`](xref:Xamarin.Forms.Maps.Map) también define un evento `MapClicked` que se desencadena cuando se puntea en la asignación. El objeto `MapClickedEventArgs` que acompaña al evento tiene una propiedad única denominada `Position`, de tipo [`Position`](xref:Xamarin.Forms.Maps.Position). Cuando se desencadena el evento, la propiedad `Position` se establece en la ubicación del mapa que se ha punteado. Para obtener información sobre el [`Position`](xref:Xamarin.Forms.Maps.Position) struct, consulte [asignación de posición y distancia](position-distance.md).

Para obtener información sobre las propiedades [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource), [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)y `ItemTemplateSelector`, vea [Mostrar una colección de PIN](pins.md#display-a-pin-collection).

## <a name="display-a-map"></a>Mostrar un mapa

Para mostrar una [`Map`](xref:Xamarin.Forms.Maps.Map) , puede agregarla a un diseño o una página:

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
    <maps:Map x:Name="map" />
</ContentPage>
```

> [!NOTE]
> Se requiere una definición de espacio de nombres `xmlns` adicional para hacer referencia a los controles Xamarin. Forms. Maps. En el ejemplo anterior, se hace referencia al espacio de nombres `Xamarin.Forms.Maps` a través de la palabra clave `maps`.

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

En este ejemplo se llama al constructor [`Map`](xref:Xamarin.Forms.Maps.Map) predeterminado, que centra el mapa en Roma:

[![Captura de pantalla del control de mapa con ubicación predeterminada, en iOS y Android](map-images/map-default.png "Control de mapa con ubicación predeterminada")](map-images/map-default-large.png#lightbox "Control de mapa con ubicación predeterminada")

Como alternativa, se puede pasar un argumento [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) a un constructor [`Map`](xref:Xamarin.Forms.Maps.Map) para establecer el punto central y el nivel de zoom del mapa cuando se carga. Para obtener más información, vea [Mostrar una ubicación específica en un mapa](#display-a-specific-location-on-a-map).

## <a name="map-types"></a>Tipos de asignación

La propiedad [`Map.MapType`](xref:Xamarin.Forms.Maps.Map.MapType) se puede establecer en un miembro de enumeración [`MapType`](xref:Xamarin.Forms.Maps.MapType) para definir el estilo de presentación del mapa. La enumeración `MapType` define los miembros siguientes:

- `Street` especifica que se mostrará un mapa de calles.
- `Satellite` especifica que se mostrará un mapa que contiene imágenes de satélite.
- `Hybrid` especifica que se mostrará un mapa que combina una calle y datos satélite.

De forma predeterminada, un [`Map`](xref:Xamarin.Forms.Maps.Map) mostrará un mapa de calle si la propiedad [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) no está definida. Como alternativa, la propiedad `MapType` se puede establecer en uno de los miembros de enumeración [`MapType`](xref:Xamarin.Forms.Maps.MapType) :

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

Las capturas de pantallas siguientes muestran un [`Map`](xref:Xamarin.Forms.Maps.Map) cuando la propiedad [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) está establecida en `Street`:

[![Captura de pantalla del control de mapa con el tipo de mapa de calle, en iOS y Android](map-images/maptype-street.png "Control de mapa con la calle mapType")](map-images/maptype-street-large.png#lightbox "Map control with the street map type")

Las capturas de pantallas siguientes muestran un [`Map`](xref:Xamarin.Forms.Maps.Map) cuando la propiedad [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) está establecida en `Satellite`:

[![Captura de pantalla del control de mapa con el tipo de asignación satélite, en iOS y Android](map-images/maptype-satellite.png "Control de mapa con el mapType satélite")](map-images/maptype-satellite-large.png#lightbox "Map control with the satellite map type")

Las capturas de pantallas siguientes muestran un [`Map`](xref:Xamarin.Forms.Maps.Map) cuando la propiedad [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) está establecida en `Hybrid`:

[![Captura de pantalla del control de mapa con el tipo de mapa híbrido, en iOS y Android](map-images/maptype-hybrid.png "Control de mapa con el mapType híbrido")](map-images/maptype-hybrid-large.png#lightbox "Map control with the hybrid map type")

## <a name="display-a-specific-location-on-a-map"></a>Mostrar una ubicación específica en un mapa

La región de un mapa que se va a mostrar cuando se carga una asignación se puede establecer pasando un argumento [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) al constructor [`Map`](xref:Xamarin.Forms.Maps.Map) :

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

En este ejemplo se crea un objeto [`Map`](xref:Xamarin.Forms.Maps.Map) que muestra la región especificada por el objeto [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) . El objeto `MapSpan` está centrado en la latitud y la longitud representada por un objeto [`Position`](xref:Xamarin.Forms.Maps.Position) , y abarca los grados 0,01 latitud y 0,01 de longitud. Para obtener información sobre el [`Position`](xref:Xamarin.Forms.Maps.Position) struct, consulte [asignación de posición y distancia](position-distance.md). Para obtener información sobre cómo pasar argumentos en XAML, vea [pasar argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md).

El resultado es que, cuando se muestra el mapa, se centra en una ubicación específica y abarca un número específico de grados de latitud y longitud:

[![Captura de pantalla del control de mapa con la ubicación especificada, en iOS y Android](map-images/map-region.png "Control de mapa con una ubicación especificada")](map-images/map-region-large.png#lightbox "Control de mapa con una ubicación especificada")

## <a name="create-a-mapspan-object"></a>Creación de un objeto MapSpan

Existen varios enfoques para crear objetos de [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) . Un enfoque común es proporcionar los argumentos necesarios para el constructor de `MapSpan`. Se trata de una latitud y una longitud representada por un objeto [`Position`](xref:Xamarin.Forms.Maps.Position) y `double` valores que representan los grados de latitud y longitud que se distribuyen por la `MapSpan`. Para obtener información sobre el [`Position`](xref:Xamarin.Forms.Maps.Position) struct, consulte [asignación de posición y distancia](position-distance.md).

Como alternativa, hay tres métodos en la clase [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) que devuelven nuevos objetos `MapSpan`:

1. [`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude*) devuelve un `MapSpan` con la misma `LongitudeDegrees` que la instancia de la clase del método y un radio definido por sus argumentos `north` y `south`.
1. [`FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius*) devuelve un `MapSpan` definido por sus argumentos [`Position`](xref:Xamarin.Forms.Maps.Position) y [`Distance`](xref:Xamarin.Forms.Maps.Distance) .
1. [`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*) devuelve un `MapSpan` con el mismo centro que la instancia de la clase del método, pero con un radio multiplicado por su argumento `double`.

Para obtener información sobre el [`Distance`](xref:Xamarin.Forms.Maps.Distance) struct, consulte [asignación de posición y distancia](position-distance.md).

Una vez que se ha creado un [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) , se puede tener acceso a las siguientes propiedades para recuperar datos sobre ella:

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center), que representa el [`Position`](xref:Xamarin.Forms.Maps.Position) en el centro geográfico de la `MapSpan`.
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees), que representa los grados de latitud que abarca el `MapSpan`.
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees), que representa los grados de longitud que abarca el `MapSpan`.
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius), que representa el radio `MapSpan`.

## <a name="move-the-map"></a>Movimiento del mapa

Se puede llamar al método [`Map.MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) para cambiar la posición y el nivel de zoom de un mapa. Este método acepta un argumento de [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) que define la región del mapa que se va a mostrar y su nivel de zoom.

En el código siguiente se muestra un ejemplo de cómo mover la región mostrada en un mapa:

```csharp
MapSpan mapSpan = MapSpan.FromCenterAndRadius(position, Distance.FromKilometers(0.444));
map.MoveToRegion(mapSpan);
```

## <a name="zoom-the-map"></a>Zoom del mapa

El nivel de zoom de un [`Map`](xref:Xamarin.Forms.Maps.Map) se puede cambiar sin alterar su ubicación. Esto puede realizarse mediante la interfaz de usuario de asignación o mediante programación llamando al método [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) con un argumento [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) que usa la ubicación actual como el argumento [`Position`](xref:Xamarin.Forms.Maps.Position) :

```csharp
double zoomLevel = 0.5;
double latlongDegrees = 360 / (Math.Pow(2, zoomLevel));
if (map.VisibleRegion != null)
{
    map.MoveToRegion(new MapSpan(map.VisibleRegion.Center, latlongDegrees, latlongDegrees));
}
```

En este ejemplo, se llama al método [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) con un argumento [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) que especifica la ubicación actual del mapa, a través de la propiedad [`Map.VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion) y el nivel de zoom como grados de latitud y longitud. El resultado general es que se cambia el nivel de zoom del mapa, pero su ubicación no lo es. Un enfoque alternativo para implementar zoom en un mapa es usar el método [`MapSpan.WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*) para controlar el factor de zoom.

> [!IMPORTANT]
> El zoom de un mapa, ya sea a través de la interfaz de usuario del mapa o mediante programación, requiere que la propiedad [`Map.HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) sea `true`. Para obtener más información sobre esta propiedad, vea [deshabilitar zoom](#disable-zoom).

## <a name="customize-map-behavior"></a>Personalizar el comportamiento del mapa

El comportamiento de un [`Map`](xref:Xamarin.Forms.Maps.Map) se puede personalizar estableciendo algunas de sus propiedades y controlando el evento `MapClicked`.

> [!NOTE]
> Se puede lograr un comportamiento de mapa adicional customizatin mediante la creación de un representador personalizado de mapa. Para obtener más información, vea [personalizar un mapa de Xamarin. Forms](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md).

### <a name="disable-scroll"></a>Deshabilitar desplazamiento

La clase [`Map`](xref:Xamarin.Forms.Maps.Map) define una propiedad [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) de tipo `bool`. De forma predeterminada, esta propiedad es `true`, lo que indica que la asignación puede desplazarse. Cuando esta propiedad se establece en `false`, la asignación no se desplazará. En el ejemplo siguiente se muestra cómo establecer esta propiedad:

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

La clase [`Map`](xref:Xamarin.Forms.Maps.Map) define una propiedad [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) de tipo `bool`. De forma predeterminada, esta propiedad es `true`, lo que indica que se puede realizar un zoom en el mapa. Cuando esta propiedad se establece en `false`, no se puede hacer zoom en el mapa. En el ejemplo siguiente se muestra cómo establecer esta propiedad:

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

La clase [`Map`](xref:Xamarin.Forms.Maps.Map) define una propiedad [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser) de tipo `bool`. De forma predeterminada, esta propiedad es `false`, lo que indica que la asignación no muestra la ubicación actual del usuario. Cuando esta propiedad se establece en `true`, el mapa muestra la ubicación actual del usuario. En el ejemplo siguiente se muestra cómo establecer esta propiedad:

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

La clase [`Map`](xref:Xamarin.Forms.Maps.Map) define una propiedad `MoveToLastRegionOnLayoutChange` de tipo `bool`. De forma predeterminada, esta propiedad es `true`, lo que indica que la región de mapa mostrada pasará de su región actual a su región establecida anteriormente cuando se produzca un cambio de diseño, como en la rotación de dispositivos. Cuando esta propiedad se establece en `false`, la región de mapa que se muestra permanecerá centrada cuando se produzca un cambio de diseño. En el ejemplo siguiente se muestra cómo establecer esta propiedad:

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

La clase [`Map`](xref:Xamarin.Forms.Maps.Map) define un evento `MapClicked` que se desencadena cuando se puntea en la asignación. El objeto `MapClickedEventArgs` que acompaña al evento tiene una propiedad única denominada `Position`, de tipo [`Position`](xref:Xamarin.Forms.Maps.Position). Cuando se desencadena el evento, la propiedad `Position` se establece en la ubicación del mapa que se ha punteado. Para obtener información sobre el [`Position`](xref:Xamarin.Forms.Maps.Position) struct, consulte [asignación de posición y distancia](position-distance.md).

En el ejemplo de código siguiente se muestra un controlador de eventos para el evento `MapClicked`:

```csharp
void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

En este ejemplo, el controlador de eventos `OnMapClicked` devuelve la latitud y la longitud que representa la ubicación de asignación punteada. El controlador de eventos se puede registrar con el evento `MapClicked` de la siguiente manera:

```xaml
<maps:Map MapClicked="OnMapClicked" />
```

El código de C# equivalente es el siguiente:

```csharp
Map map = new Map();
map.MapClicked += OnMapClicked;
```

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Posición y distancia del mapa](position-distance.md)
- [Personalización de un mapa de Xamarin. Forms](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Pasar argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md)
