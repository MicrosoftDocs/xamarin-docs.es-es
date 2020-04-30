---
title: PIN de mapa de Xamarin. Forms
description: En este artículo se explica cómo crear PIN en un mapa de Xamarin. Forms.
ms.prod: xamarin
ms.assetid: F8FC081B-A811-4FBB-B8F8-30D6FD36BD40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/23/2019
ms.openlocfilehash: 3df78a7c8eaf12306ade182f134f8d294d203af5
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517582"
---
# <a name="xamarinforms-map-pins"></a>PIN de mapa de Xamarin. Forms

[![Descargar el](~/media/shared/download.png) ejemplo descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

El control Xamarin. [`Map`](xref:Xamarin.Forms.Maps.Map) Forms permite marcar ubicaciones con [`Pin`](xref:Xamarin.Forms.Maps.Pin) objetos. Un `Pin` es un marcador de mapa que abre una ventana de información al puntear:

[![Captura de pantalla de un PIN de mapa y su ventana de información, en iOS y Android](pins-images/pin-and-information-window.png "Asignar pin con la ventana de información")](pins-images/pin-and-information-window-large.png#lightbox "Asignar pin con la ventana de información")

Cuando se [`Pin`](xref:Xamarin.Forms.Maps.Pin) agrega un objeto a la [`Map.Pins`](xref:Xamarin.Forms.Maps.Pin) colección, el PIN se representa en el mapa.

La [`Pin`](xref:Xamarin.Forms.Maps.Pin) clase tiene las siguientes propiedades:

- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address), de tipo `string`, que normalmente representa la dirección de la ubicación del PIN. Sin embargo, puede ser cualquier `string` contenido, no solo una dirección.
- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label), de tipo `string`, que normalmente representa el título del PIN.
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position), de tipo [`Position`](xref:Xamarin.Forms.Maps.Position), que representa la latitud y la longitud del PIN.
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type), de tipo [`PinType`](xref:Xamarin.Forms.Maps.PinType), que representa el tipo de PIN.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa `Pin` que puede ser el destino de los enlaces de datos. Para obtener más información sobre los `Pin` objetos de enlace de datos, vea [Mostrar una colección de PIN](#display-a-pin-collection).

Además, la [`Pin`](xref:Xamarin.Forms.Maps.Pin) clase define `MarkerClicked` los eventos `InfoWindowClicked` y. El `MarkerClicked` evento se desencadena cuando se puntea un PIN y el `InfoWindowClicked` evento se desencadena cuando se puntea en la ventana de información. El `PinClickedEventArgs` objeto que acompaña a ambos eventos tiene una propiedad `HideInfoWindow` única, de tipo `bool`.

## <a name="display-a-pin"></a>Mostrar un PIN

Se [`Pin`](xref:Xamarin.Forms.Maps.Pin) puede Agregar un a [`Map`](xref:Xamarin.Forms.Maps.Map) en XAML:

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

Este código XAML crea [`Map`](xref:Xamarin.Forms.Maps.Map) un objeto que muestra la región especificada por el [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) objeto. El `MapSpan` objeto se centra en la latitud y la longitud representada por [`Position`](xref:Xamarin.Forms.Maps.Position) un objeto, que extiende los grados 0,01 de latitud y longitud. Un [`Pin`](xref:Xamarin.Forms.Maps.Pin) objeto se agrega a la [`Map.Pins`](xref:Xamarin.Forms.Maps.Pin) colección y se dibuja en `Map` en la ubicación especificada por su [`Position`](xref:Xamarin.Forms.Maps.Pin.Position) propiedad. Para obtener más información [`Position`](xref:Xamarin.Forms.Maps.Position) sobre el struct, consulte [asignación de posición y distancia](position-distance.md). Para obtener información sobre cómo pasar argumentos en XAML a objetos que carecen de constructores predeterminados, vea [pasar argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md).

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
> Si [`Pin`](xref:Xamarin.Forms.Maps.Pin) no se establece [`Pin.Label`](xref:Xamarin.Forms.Maps.Pin.Label) la propiedad, se produce `ArgumentException` una excepción cuando se agrega a. [`Map`](xref:Xamarin.Forms.Maps.Map)

Este código de ejemplo da como resultado un solo PIN que se representa en un mapa:

[![Captura de pantalla de un PIN de mapa, en iOS y Android](pins-images/pin-only.png "Anclaje de mapa")](pins-images/pin-only-large.png#lightbox "Anclaje de mapa")

## <a name="interact-with-a-pin"></a>Interacción con un PIN

De forma predeterminada, cuando [`Pin`](xref:Xamarin.Forms.Maps.Pin) se puntea en, se muestra la ventana de información:

[![Captura de pantalla de un PIN de mapa y su ventana de información, en iOS y Android](pins-images/pin-and-information-window.png "Asignar pin con la ventana de información")](pins-images/pin-and-information-window-large.png#lightbox "Asignar pin con la ventana de información")

Al puntear en otra parte del mapa, se cierra la ventana de información.

La [`Pin`](xref:Xamarin.Forms.Maps.Pin) clase define un `MarkerClicked` evento, que se desencadena cuando `Pin` se puntea en. No es necesario controlar este evento para mostrar la ventana de información. En su lugar, este evento debe administrarse cuando hay un requisito para recibir una notificación de que se ha punteado con un PIN específico.

La [`Pin`](xref:Xamarin.Forms.Maps.Pin) clase también define un `InfoWindowClicked` evento que se desencadena cuando se puntea una ventana de información. Este evento debe administrarse cuando hay un requisito para que se le notifique que se ha punteado una ventana de información específica.

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

El `PinClickedEventArgs` objeto que acompaña a ambos eventos tiene una propiedad `HideInfoWindow` única, de tipo `bool`. Cuando esta propiedad se establece en `true` dentro de un controlador de eventos, se oculta la ventana de información.

## <a name="pin-types"></a>Tipos de PIN

[`Pin`](xref:Xamarin.Forms.Maps.Pin)los objetos incluyen [`Type`](xref:Xamarin.Forms.Maps.Pin.Type) una propiedad, de [`PinType`](xref:Xamarin.Forms.Maps.PinType)tipo, que representa el tipo de PIN. La enumeración `PinType` define los miembros siguientes:

- `Generic`, representa un PIN genérico.
- `Place`, representa un PIN para un lugar.
- `SavedPin`, representa un PIN para una ubicación guardada.
- `SearchResult`, representa un PIN para un resultado de búsqueda.

Sin embargo, si [`Pin.Type`](xref:Xamarin.Forms.Maps.Pin.Type) se establece la [`PinType`](xref:Xamarin.Forms.Maps.PinType) propiedad en cualquier miembro, no se cambia la apariencia del código PIN representado. En su lugar, debe crear un representador personalizado para personalizar la apariencia del PIN. Para obtener más información, consulte [Personalización de un PIN de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md).

## <a name="display-a-pin-collection"></a>Presentación de una colección de pin

La [`Map`](xref:Xamarin.Forms.Maps.Map) clase define las siguientes propiedades:

- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource), de tipo `IEnumerable`, que especifica la colección de `IEnumerable` elementos que se van a mostrar.
- [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate), de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que especifica la [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que se va a aplicar a cada elemento de la colección de elementos mostrados.
- `ItemTemplateSelector`, de tipo [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector), que especifica [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) que se usará para elegir un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objeto para un elemento en tiempo de ejecución.

> [!IMPORTANT]
> La [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate) propiedad tiene prioridad cuando se establecen `ItemTemplate` las `ItemTemplateSelector` propiedades y.

Un [`Map`](xref:Xamarin.Forms.Maps.Map) se puede rellenar con PIN mediante el enlace de datos [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource) para enlazar `IEnumerable` su propiedad a una colección:

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

Los [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource) datos de la propiedad se enlazan a la `Locations` propiedad del ViewModel conectado, que `ObservableCollection` devuelve `Location` una de objetos, que es un tipo personalizado. Cada `Location` objeto define `Address` las `Description` propiedades y, de `string`tipo y una `Position` propiedad, de tipo [`Position`](xref:Xamarin.Forms.Maps.Position).

La apariencia de cada elemento de la `IEnumerable` colección se define estableciendo la [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate) propiedad en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que contiene un [`Pin`](xref:Xamarin.Forms.Maps.Pin) objeto que los datos enlazan a las propiedades adecuadas.

Las capturas de pantallas [`Map`](xref:Xamarin.Forms.Maps.Map) siguientes muestran [`Pin`](xref:Xamarin.Forms.Maps.Pin) cómo mostrar una colección mediante el enlace de datos:

[![Captura de pantalla de la asignación con PIN enlazados a datos, en iOS y Android](pins-images/pins-itemsource.png "Asignación con PIN enlazados a datos")](pins-images/pins-itemsource-large.png#lightbox "Asignación con PIN enlazados a datos")

### <a name="choose-item-appearance-at-runtime"></a>Elección de la apariencia del elemento en tiempo de ejecución

La apariencia de cada elemento de la `IEnumerable` colección se puede elegir en tiempo de ejecución, en función del valor del elemento, `ItemTemplateSelector` estableciendo la propiedad [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)en:

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

En el ejemplo siguiente se `MapItemTemplateSelector` muestra la clase:

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

La `MapItemTemplateSelector` clase define `DefaultTemplate` las `XamarinTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) propiedades y que se establecen en distintas plantillas de datos. El `OnSelectTemplate` método devuelve `XamarinTemplate`, que muestra "Xamarin" como etiqueta cuando `Pin` se puntea en, cuando el elemento tiene una dirección que contiene "San Francisco". Cuando el elemento no tiene una dirección que contiene "San Francisco", el `OnSelectTemplate` método devuelve `DefaultTemplate`.

> [!NOTE]
> Un caso de uso de esta funcionalidad consiste en enlazar las propiedades de [`Pin`](xref:Xamarin.Forms.Maps.Pin) los objetos de subclase a propiedades diferentes `Pin` , en función del subtipo.

Para obtener más información sobre los selectores de plantilla de datos, vea [crear un DataTemplateSelector de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Representador personalizado de asignación](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)
- [Pasar argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md)
- [Creación de un elemento DataTemplateSelector de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
