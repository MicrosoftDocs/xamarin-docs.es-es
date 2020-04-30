---
title: Control de mapa de Xamarin. Forms
description: El control de mapa es una vista multiplataforma para mostrar y anotar asignaciones. Usa el control de mapa nativo para cada plataforma, lo que proporciona una experiencia de mapas rápida y familiar a los usuarios.
ms.prod: xamarin
ms.assetid: 22C99029-0B16-43A6-BF58-26B48C4AED38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/29/2019
ms.openlocfilehash: 3861200446ea9c0e368aa251f3e7ec3f992c7152
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517522"
---
# <a name="xamarinforms-map-control"></a>Control de mapa de Xamarin. Forms

[![Descargar el](~/media/shared/download.png) ejemplo descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

El [`Map`](xref:Xamarin.Forms.Maps.Map) control es una vista multiplataforma para mostrar y anotar asignaciones. Usa el control de asignación nativa para cada plataforma, lo que proporciona una experiencia de mapas rápida y familiar a los usuarios:

[![Captura de pantalla del control de mapa, en iOS y Android](map-images/map-default.png "Control de mapa")](map-images/map-default-large.png#lightbox "Control de mapa")

La [`Map`](xref:Xamarin.Forms.Maps.Map) clase define las siguientes propiedades que controlan la apariencia y el comportamiento de los mapas:

- [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser), de tipo `bool`, indica si la asignación muestra la ubicación actual del usuario.
- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource), de tipo `IEnumerable`, que especifica la colección de `IEnumerable` elementos que se van a mostrar.
- [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate), de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que especifica la [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que se va a aplicar a cada elemento de la colección de elementos mostrados.
- `ItemTemplateSelector`, de tipo [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector), que especifica [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) que se usará para elegir un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objeto para un elemento en tiempo de ejecución.
- [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled), de tipo `bool`, determina si se permite desplazar el mapa.
- [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled), de tipo `bool`, determina si el mapa puede hacer zoom.
- `MapElements`, de tipo `IList<MapElement>`, representa la lista de elementos del mapa, como polígonos y polilíneas.
- [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType), de tipo [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType), indica el estilo de presentación del mapa.
- `MoveToLastRegionOnLayoutChange`, de tipo `bool`, controla si la región de mapa mostrada se moverá de su región actual a su región establecida anteriormente cuando se produzca un cambio de diseño.
- [`Pins`](xref:Xamarin.Forms.Maps.Map.Pins), de tipo `IList<Pin>`, representa la lista de marcadores en el mapa.
- [`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion), de tipo [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan), devuelve la región de la asignación que se muestra actualmente.

Estas propiedades, con la excepción de las `MapElements`propiedades `Pins`, y `VisibleRegion` , están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de los enlaces de datos.

La [`Map`](xref:Xamarin.Forms.Maps.Map) clase también define un `MapClicked` evento que se desencadena cuando se puntea en la asignación. El `MapClickedEventArgs` objeto que acompaña al evento tiene una propiedad única denominada `Position`, de tipo. [`Position`](xref:Xamarin.Forms.Maps.Position) Cuando se desencadena el evento, la `Position` propiedad se establece en la ubicación de asignación que se ha punteado. Para obtener más información [`Position`](xref:Xamarin.Forms.Maps.Position) sobre el struct, consulte [asignación de posición y distancia](position-distance.md).

Para obtener información sobre [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource)las [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)propiedades, `ItemTemplateSelector` y, vea [Mostrar una colección de PIN](pins.md#display-a-pin-collection).

## <a name="display-a-map"></a>Mostrar un mapa

Se [`Map`](xref:Xamarin.Forms.Maps.Map) puede mostrar si se agrega a un diseño o una página:

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
    <maps:Map x:Name="map" />
</ContentPage>
```

> [!NOTE]
> Se requiere `xmlns` una definición de espacio de nombres adicional para hacer referencia a los controles de Xamarin. Forms. Maps. En el ejemplo anterior, `Xamarin.Forms.Maps` se hace referencia al espacio de `maps` nombres a través de la palabra clave.

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

En este ejemplo se llama [`Map`](xref:Xamarin.Forms.Maps.Map) al constructor predeterminado, que centra el mapa en Roma:

[![Captura de pantalla del control de mapa con ubicación predeterminada, en iOS y Android](map-images/map-default.png "Control de mapa con ubicación predeterminada")](map-images/map-default-large.png#lightbox "Control de mapa con ubicación predeterminada")

Como alternativa, se [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) puede pasar un argumento a un [`Map`](xref:Xamarin.Forms.Maps.Map) constructor para establecer el punto central y el nivel de zoom del mapa cuando se carga. Para obtener más información, vea [Mostrar una ubicación específica en un mapa](#display-a-specific-location-on-a-map).

## <a name="map-types"></a>Tipos de asignación

La [`Map.MapType`](xref:Xamarin.Forms.Maps.Map.MapType) propiedad se puede establecer en un [`MapType`](xref:Xamarin.Forms.Maps.MapType) miembro de enumeración para definir el estilo de presentación del mapa. La enumeración `MapType` define los miembros siguientes:

- `Street`Especifica que se mostrará un mapa de calles.
- `Satellite`Especifica que se mostrará un mapa que contiene imágenes de satélite.
- `Hybrid`Especifica que se mostrará un mapa que combina una calle y datos satélite.

De forma predeterminada, [`Map`](xref:Xamarin.Forms.Maps.Map) un mostrará un mapa de calle si [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) la propiedad no está definida. Como alternativa, la `MapType` propiedad se puede establecer en uno de los [`MapType`](xref:Xamarin.Forms.Maps.MapType) miembros de enumeración:

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

Las capturas de pantallas [`Map`](xref:Xamarin.Forms.Maps.Map) siguientes muestran [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) cuando la propiedad está `Street`establecida en:

[![Captura de pantalla del control de mapa con el tipo de mapa de calle, en iOS y Android](map-images/maptype-street.png "Control de mapa con la calle mapType")](map-images/maptype-street-large.png#lightbox "Control de mapa con el tipo de asignación calle")

Las capturas de pantallas [`Map`](xref:Xamarin.Forms.Maps.Map) siguientes muestran [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) cuando la propiedad está `Satellite`establecida en:

[![Captura de pantalla del control de mapa con el tipo de asignación satélite, en iOS y Android](map-images/maptype-satellite.png "Control de mapa con el mapType satélite")](map-images/maptype-satellite-large.png#lightbox "Control de mapa con el tipo de asignación satélite")

Las capturas de pantallas [`Map`](xref:Xamarin.Forms.Maps.Map) siguientes muestran [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) cuando la propiedad está `Hybrid`establecida en:

[![Captura de pantalla del control de mapa con el tipo de mapa híbrido, en iOS y Android](map-images/maptype-hybrid.png "Control de mapa con el mapType híbrido")](map-images/maptype-hybrid-large.png#lightbox "Control de mapa con el tipo de mapa híbrido")

## <a name="display-a-specific-location-on-a-map"></a>Mostrar una ubicación específica en un mapa

La región de un mapa que se va a mostrar cuando se carga una asignación se puede establecer [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) pasando un argumento [`Map`](xref:Xamarin.Forms.Maps.Map) al constructor:

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

En este ejemplo se [`Map`](xref:Xamarin.Forms.Maps.Map) crea un objeto que muestra la región especificada por el [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) objeto. El `MapSpan` objeto se centra en la latitud y la longitud representada por [`Position`](xref:Xamarin.Forms.Maps.Position) un objeto y abarca los grados 0,01 latitud y 0,01 de longitud. Para obtener más información [`Position`](xref:Xamarin.Forms.Maps.Position) sobre el struct, consulte [asignación de posición y distancia](position-distance.md). Para obtener información sobre cómo pasar argumentos en XAML, vea [pasar argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md).

El resultado es que, cuando se muestra el mapa, se centra en una ubicación específica y abarca un número específico de grados de latitud y longitud:

[![Captura de pantalla del control de mapa con la ubicación especificada, en iOS y Android](map-images/map-region.png "Control de mapa con una ubicación especificada")](map-images/map-region-large.png#lightbox "Control de mapa con una ubicación especificada")

## <a name="create-a-mapspan-object"></a>Creación de un objeto MapSpan

Existen varios enfoques para crear [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) objetos. Un enfoque común es proporcionar los argumentos necesarios para el `MapSpan` constructor. Se trata de una latitud y una longitud representada por [`Position`](xref:Xamarin.Forms.Maps.Position) un `double` objeto, y valores que representan los grados de latitud y longitud que abarca `MapSpan`. Para obtener más información [`Position`](xref:Xamarin.Forms.Maps.Position) sobre el struct, consulte [asignación de posición y distancia](position-distance.md).

Como alternativa, hay tres métodos en la [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) clase que devuelven `MapSpan` nuevos objetos:

1. [`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude*)Devuelve un `MapSpan` con el mismo `LongitudeDegrees` que la instancia de la clase del método y un radio definido por `north` sus `south` argumentos y.
1. [`FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius*)Devuelve un `MapSpan` que se define por sus [`Position`](xref:Xamarin.Forms.Maps.Position) argumentos [`Distance`](xref:Xamarin.Forms.Maps.Distance) y.
1. [`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*)Devuelve un `MapSpan` con el mismo centro que la instancia de la clase del método, pero con un radio multiplicado por su `double` argumento.

Para obtener más información [`Distance`](xref:Xamarin.Forms.Maps.Distance) sobre el struct, consulte [asignación de posición y distancia](position-distance.md).

Una vez [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) que se ha creado un, se puede tener acceso a las propiedades siguientes para recuperar datos sobre ella:

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center), que representa el [`Position`](xref:Xamarin.Forms.Maps.Position) en el centro geográfico de `MapSpan`.
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees), que representa los grados de latitud que abarca `MapSpan`.
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees), que representa los grados de longitud que abarca `MapSpan`.
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius), que representa el `MapSpan` radio.

## <a name="move-the-map"></a>Movimiento del mapa

Se [`Map.MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) puede llamar al método para cambiar la posición y el nivel de zoom de un mapa. Este método acepta un [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) argumento que define la región del mapa que se va a mostrar y su nivel de zoom.

En el código siguiente se muestra un ejemplo de cómo mover la región mostrada en un mapa:

```csharp
MapSpan mapSpan = MapSpan.FromCenterAndRadius(position, Distance.FromKilometers(0.444));
map.MoveToRegion(mapSpan);
```

## <a name="zoom-the-map"></a>Acercamiento/alejamiento del mapa

El nivel de zoom de [`Map`](xref:Xamarin.Forms.Maps.Map) un se puede cambiar sin alterar su ubicación. Esto puede realizarse mediante la interfaz de usuario de asignación o mediante programación [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) llamando al método [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) con un argumento que utiliza la ubicación actual [`Position`](xref:Xamarin.Forms.Maps.Position) como argumento:

```csharp
double zoomLevel = 0.5;
double latlongDegrees = 360 / (Math.Pow(2, zoomLevel));
if (map.VisibleRegion != null)
{
    map.MoveToRegion(new MapSpan(map.VisibleRegion.Center, latlongDegrees, latlongDegrees));
}
```

En este ejemplo, se [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) llama al método con un [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) argumento que especifica la ubicación actual del mapa, a través de [`Map.VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion) la propiedad y el nivel de zoom como grados de latitud y longitud. El resultado general es que se cambia el nivel de zoom del mapa, pero su ubicación no lo es. Un enfoque alternativo para implementar zoom en un mapa es usar el [`MapSpan.WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*) método para controlar el factor de zoom.

> [!IMPORTANT]
> El zoom de un mapa, ya sea a través de la interfaz de usuario del [`Map.HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) mapa o `true`mediante programación, requiere que la propiedad sea. Para obtener más información sobre esta propiedad, vea [deshabilitar zoom](#disable-zoom).

## <a name="customize-map-behavior"></a>Personalizar el comportamiento del mapa

El comportamiento de se [`Map`](xref:Xamarin.Forms.Maps.Map) puede personalizar estableciendo algunas de sus propiedades y controlando el `MapClicked` evento.

> [!NOTE]
> Se puede lograr una personalización del comportamiento de mapa adicional mediante la creación de un representador personalizado de mapa. Para obtener más información, vea [personalizar un mapa de Xamarin. Forms](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md).

### <a name="disable-scroll"></a>Deshabilitar desplazamiento

La [`Map`](xref:Xamarin.Forms.Maps.Map) clase define una [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) propiedad de tipo `bool`. De forma predeterminada, esta `true`propiedad es, que indica que la asignación puede desplazarse. Cuando esta propiedad se establece en `false`, el mapa no se desplazará. En el ejemplo siguiente se muestra cómo establecer esta propiedad:

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

La [`Map`](xref:Xamarin.Forms.Maps.Map) clase define una [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) propiedad de tipo `bool`. De forma predeterminada, esta `true`propiedad es, que indica que se puede realizar un zoom en el mapa. Cuando esta propiedad se establece en `false`, no se puede hacer zoom en el mapa. En el ejemplo siguiente se muestra cómo establecer esta propiedad:

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

La [`Map`](xref:Xamarin.Forms.Maps.Map) clase define una [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser) propiedad de tipo `bool`. De forma predeterminada, esta `false`propiedad es, que indica que la asignación no muestra la ubicación actual del usuario. Cuando esta propiedad se establece en `true`, el mapa muestra la ubicación actual del usuario. En el ejemplo siguiente se muestra cómo establecer esta propiedad:

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

La [`Map`](xref:Xamarin.Forms.Maps.Map) clase define una `MoveToLastRegionOnLayoutChange` propiedad de tipo `bool`. De forma predeterminada, esta `true`propiedad es, que indica que la región de mapa mostrada pasará de su región actual a su región establecida anteriormente cuando se produzca un cambio de diseño, como en la rotación de dispositivos. Cuando esta propiedad está establecida en `false`, la región de mapa que se muestra permanecerá centrada cuando se produzca un cambio de diseño. En el ejemplo siguiente se muestra cómo establecer esta propiedad:

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

La [`Map`](xref:Xamarin.Forms.Maps.Map) clase define un `MapClicked` evento que se desencadena cuando se puntea la asignación. El `MapClickedEventArgs` objeto que acompaña al evento tiene una propiedad única denominada `Position`, de tipo. [`Position`](xref:Xamarin.Forms.Maps.Position) Cuando se desencadena el evento, la `Position` propiedad se establece en la ubicación de asignación que se ha punteado. Para obtener más información [`Position`](xref:Xamarin.Forms.Maps.Position) sobre el struct, consulte [asignación de posición y distancia](position-distance.md).

En el ejemplo de código siguiente se muestra un controlador `MapClicked` de eventos para el evento:

```csharp
void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

En este ejemplo, el `OnMapClicked` controlador de eventos genera la latitud y la longitud que representa la ubicación de asignación punteada. El controlador de eventos se puede registrar con `MapClicked` el evento de la siguiente manera:

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
- [Personalizar un mapa de Xamarin.Forms](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)
- [Pasar argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md)
