---
title: PIN de mapa de Xamarin. Forms
description: En este artículo se explica cómo crear PIN en un mapa de Xamarin. Forms.
ms.prod: xamarin
ms.assetid: F8FC081B-A811-4FBB-B8F8-30D6FD36BD40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/23/2019
ms.openlocfilehash: 197c48a7a3486d7161d351a6b06101daaa389256
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79306570"
---
# <a name="xamarinforms-map-pins"></a>PIN de mapa de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

El control Xamarin. Forms [`Map`](xref:Xamarin.Forms.Maps.Map) permite marcar ubicaciones con objetos [`Pin`](xref:Xamarin.Forms.Maps.Pin) . Un `Pin` es un marcador de mapa que abre una ventana de información al puntear:

[![Captura de pantalla de un PIN de mapa y su ventana de información, en iOS y Android](pins-images/pin-and-information-window.png "Asignar pin con la ventana de información")](pins-images/pin-and-information-window-large.png#lightbox "Asignar pin con la ventana de información")

Cuando se agrega un objeto [`Pin`](xref:Xamarin.Forms.Maps.Pin) a la colección [`Map.Pins`](xref:Xamarin.Forms.Maps.Pin) , el código PIN se representa en el mapa.

La clase [`Pin`](xref:Xamarin.Forms.Maps.Pin) tiene las siguientes propiedades:

- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address), de tipo `string`, que normalmente representa la dirección de la ubicación del PIN. Sin embargo, puede ser cualquier `string` contenido, no solo una dirección.
- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label), de tipo `string`, que normalmente representa el título del PIN.
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position), de tipo [`Position`](xref:Xamarin.Forms.Maps.Position), que representa la latitud y la longitud del PIN.
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type), de tipo [`PinType`](xref:Xamarin.Forms.Maps.PinType), que representa el tipo de PIN.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que un `Pin` puede ser el destino de los enlaces de datos. Para obtener más información sobre el enlace de datos `Pin` objetos, vea [Mostrar una colección de PIN](#display-a-pin-collection).

Además, la clase [`Pin`](xref:Xamarin.Forms.Maps.Pin) define los eventos `MarkerClicked` y `InfoWindowClicked`. El evento `MarkerClicked` se desencadena cuando se puntea un PIN y el evento `InfoWindowClicked` se desencadena cuando se puntea en la ventana de información. El objeto `PinClickedEventArgs` que acompaña a ambos eventos tiene una sola propiedad `HideInfoWindow`, de tipo `bool`.

## <a name="display-a-pin"></a>Mostrar un PIN

Se puede Agregar un [`Pin`](xref:Xamarin.Forms.Maps.Pin) a un [`Map`](xref:Xamarin.Forms.Maps.Map) en XAML:

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
     <maps:Map x:Name="map"
               IsShowingUser="True"
               MoveToLastRegionOnLayoutChange="False">
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
         <maps:Map.Pins>
             <maps:Pin Label="Santa Cruz"
                       Address="The city with a boardwalk"
                       Type="Place">
                 <maps:Pin.Position>
                     <maps:Position>
                         <x:Arguments>
                             <x:Double>36.9628066</x:Double>
                             <x:Double>-122.0194722</x:Double>
                         </x:Arguments>
                     </maps:Position>
                 </maps:Pin.Position>
             </maps:Pin>
         </maps:Map.Pins>
     </maps:Map>
</ContentPage>
```

Este código XAML crea un objeto [`Map`](xref:Xamarin.Forms.Maps.Map) que muestra la región especificada por el objeto [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) . El objeto `MapSpan` está centrado en la latitud y la longitud representada por un objeto [`Position`](xref:Xamarin.Forms.Maps.Position) , que extiende los grados 0,01 de latitud y longitud. Se agrega un objeto [`Pin`](xref:Xamarin.Forms.Maps.Pin) a la colección [`Map.Pins`](xref:Xamarin.Forms.Maps.Pin) y se dibuja en el `Map` en la ubicación especificada por su propiedad [`Position`](xref:Xamarin.Forms.Maps.Pin.Position) . Para obtener información sobre el [`Position`](xref:Xamarin.Forms.Maps.Position) struct, consulte [asignación de posición y distancia](position-distance.md). Para obtener información sobre cómo pasar argumentos en XAML a objetos que carecen de constructores predeterminados, vea [pasar argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md).

El código de C# equivalente es el siguiente:

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map
{
  // ...
};
Pin pin = new Pin
{
  Label = "Santa Cruz",
  Address = "The city with a boardwalk",
  Type = PinType.Place,
  Position = new Position(36.9628066, -122.0194722)
};
map.Pins.Add(pin);
```

> [!WARNING]
> Si no se establece la propiedad [`Pin.Label`](xref:Xamarin.Forms.Maps.Pin.Label) , se producirá una `ArgumentException` cuando se agregue el [`Pin`](xref:Xamarin.Forms.Maps.Pin) a un [`Map`](xref:Xamarin.Forms.Maps.Map).

Este código de ejemplo da como resultado un solo PIN que se representa en un mapa:

[![Captura de pantalla de un PIN de mapa, en iOS y Android](pins-images/pin-only.png "Anclaje de mapa")](pins-images/pin-only-large.png#lightbox "Anclaje de mapa")

## <a name="interact-with-a-pin"></a>Interacción con un PIN

De forma predeterminada, cuando se abre un [`Pin`](xref:Xamarin.Forms.Maps.Pin) , se muestra la ventana de información:

[![Captura de pantalla de un PIN de mapa y su ventana de información, en iOS y Android](pins-images/pin-and-information-window.png "Asignar pin con la ventana de información")](pins-images/pin-and-information-window-large.png#lightbox "Asignar pin con la ventana de información")

Al puntear en otra parte del mapa, se cierra la ventana de información.

La clase [`Pin`](xref:Xamarin.Forms.Maps.Pin) define un evento `MarkerClicked`, que se desencadena cuando se puntea una `Pin`. No es necesario controlar este evento para mostrar la ventana de información. En su lugar, este evento debe administrarse cuando hay un requisito para recibir una notificación de que se ha punteado con un PIN específico.

La clase [`Pin`](xref:Xamarin.Forms.Maps.Pin) también define un evento `InfoWindowClicked` que se desencadena cuando se puntea una ventana de información. Este evento debe administrarse cuando hay un requisito para que se le notifique que se ha punteado una ventana de información específica.

En el código siguiente se muestra un ejemplo de cómo controlar estos eventos:

```csharp
using Xamarin.Forms.Maps;
// ...
Pin boardwalkPin = new Pin
{
    Position = new Position(36.9641949, -122.0177232),
    Label = "Boardwalk",
    Address = "Santa Cruz",
    Type = PinType.Place
};
boardwalkPin.MarkerClicked += async (s, args) =>
{
    args.HideInfoWindow = true;
    string pinName = ((Pin)s).Label;
    await DisplayAlert("Pin Clicked", $"{pinName} was clicked.", "Ok");
};

Pin wharfPin = new Pin
{
    Position = new Position(36.9571571, -122.0173544),
    Label = "Wharf",
    Address = "Santa Cruz",
    Type = PinType.Place
};
wharfPin.InfoWindowClicked += async (s, args) =>
{
    string pinName = ((Pin)s).Label;
    await DisplayAlert("Info Window Clicked", $"The info window was clicked for {pinName}.", "Ok");
};
```

El objeto `PinClickedEventArgs` que acompaña a ambos eventos tiene una sola propiedad `HideInfoWindow`, de tipo `bool`. Cuando esta propiedad se establece en `true` dentro de un controlador de eventos, la ventana de información se oculta.

## <a name="pin-types"></a>Tipos de PIN

[`Pin`](xref:Xamarin.Forms.Maps.Pin) objetos incluyen una propiedad [`Type`](xref:Xamarin.Forms.Maps.Pin.Type) , de tipo [`PinType`](xref:Xamarin.Forms.Maps.PinType), que representa el tipo de PIN. La enumeración `PinType` define los miembros siguientes:

- `Generic`, representa un PIN genérico.
- `Place`, representa un PIN para un lugar.
- `SavedPin`, representa un PIN para una ubicación guardada.
- `SearchResult`, representa un PIN para un resultado de búsqueda.

Sin embargo, al establecer la propiedad [`Pin.Type`](xref:Xamarin.Forms.Maps.Pin.Type) en cualquier miembro de [`PinType`](xref:Xamarin.Forms.Maps.PinType) no se cambia la apariencia del código PIN representado. En su lugar, debe crear un representador personalizado para personalizar la apariencia del PIN. Para obtener más información, consulte [Personalización de un PIN de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

## <a name="display-a-pin-collection"></a>Mostrar una colección de pines

La clase [`Map`](xref:Xamarin.Forms.Maps.Map) define las siguientes propiedades:

- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource), de tipo `IEnumerable`, que especifica la colección de elementos `IEnumerable` que se van a mostrar.
- [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate), de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que especifica el [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que se va a aplicar a cada elemento de la colección de elementos mostrados.
- `ItemTemplateSelector`, de tipo [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector), que especifica el [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) que se usará para elegir un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) para un elemento en tiempo de ejecución.

> [!IMPORTANT]
> La propiedad [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate) tiene prioridad cuando se establecen las propiedades `ItemTemplate` y `ItemTemplateSelector`.

Un [`Map`](xref:Xamarin.Forms.Maps.Map) se puede rellenar con PIN mediante el enlace de datos para enlazar su propiedad [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource) a una colección `IEnumerable`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="WorkingWithMaps.PinItemsSourcePage">
    <Grid>
        ...
        <maps:Map x:Name="map"
                  ItemsSource="{Binding Locations}">
            <maps:Map.ItemTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="{Binding Description}" />
                </DataTemplate>
            </maps:Map.ItemTemplate>
        </maps:Map>
        ...
    </Grid>
</ContentPage>
```

Los datos de la propiedad [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource) se enlazan a la propiedad `Locations` del ViewModel conectado, que devuelve un `ObservableCollection` de `Location` objetos, que es un tipo personalizado. Cada objeto `Location` define `Address` y `Description` propiedades, de tipo `string`y una propiedad `Position`, de tipo [`Position`](xref:Xamarin.Forms.Maps.Position).

La apariencia de cada elemento de la colección de `IEnumerable` se define estableciendo la propiedad [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate) en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que contiene un [`Pin`](xref:Xamarin.Forms.Maps.Pin) objeto que los datos enlazan a las propiedades adecuadas.

Las capturas de pantallas siguientes muestran un [`Map`](xref:Xamarin.Forms.Maps.Map) mostrar una colección de [`Pin`](xref:Xamarin.Forms.Maps.Pin) mediante el enlace de datos:

[![Captura de pantalla de la asignación con PIN enlazados a datos, en iOS y Android](pins-images/pins-itemsource.png "Asignación con PIN enlazados a datos")](pins-images/pins-itemsource-large.png#lightbox "Asignación con PIN enlazados a datos")

### <a name="choose-item-appearance-at-runtime"></a>Elección de la apariencia del elemento en tiempo de ejecución

La apariencia de cada elemento de la colección de `IEnumerable` se puede elegir en tiempo de ejecución, en función del valor del elemento, estableciendo la propiedad `ItemTemplateSelector` en un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector):

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:WorkingWithMaps"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
    <ContentPage.Resources>
        <local:MapItemTemplateSelector x:Key="MapItemTemplateSelector">
            <local:MapItemTemplateSelector.DefaultTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="{Binding Description}" />
                </DataTemplate>
            </local:MapItemTemplateSelector.DefaultTemplate>
            <local:MapItemTemplateSelector.XamarinTemplate>
                <DataTemplate>
                    <!-- Change the property values, or the properties that are bound to. -->
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="Xamarin!" />
                </DataTemplate>
            </local:MapItemTemplateSelector.XamarinTemplate>    
        </local:MapItemTemplateSelector>
    </ContentPage.Resources>

    <Grid>
        ...
        <maps:Map x:Name="map"
                  ItemsSource="{Binding Locations}"
                  ItemTemplateSelector="{StaticResource MapItemTemplateSelector}" />
        ...
    </Grid>
</ContentPage>
```

En el ejemplo siguiente se muestra la clase `MapItemTemplateSelector`:

```csharp
public class MapItemTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate XamarinTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return ((Location)item).Address.Contains("San Francisco") ? XamarinTemplate : DefaultTemplate;
    }
}
```

La clase `MapItemTemplateSelector` define `DefaultTemplate` y `XamarinTemplate` propiedades de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que se establecen en distintas plantillas de datos. El método `OnSelectTemplate` devuelve el `XamarinTemplate`, que muestra "Xamarin" como etiqueta cuando se puntea un `Pin`, cuando el elemento tiene una dirección que contiene "San Francisco". Cuando el elemento no tiene una dirección que contiene "San Francisco", el método `OnSelectTemplate` devuelve el `DefaultTemplate`.

> [!NOTE]
> Un caso de uso de esta funcionalidad es enlazar propiedades de objetos de [`Pin`](xref:Xamarin.Forms.Maps.Pin) de subclase a propiedades diferentes, en función del subtipo de `Pin`.

Para obtener más información sobre los selectores de plantilla de datos, vea [crear un DataTemplateSelector de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Representador personalizado de asignación](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Pasar argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md)
- [Crear un DataTemplateSelector de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
