---
title: PIN de mapa de Xamarin. Forms
description: En este artículo se explica cómo crear PIN en un mapa de Xamarin. Forms.
ms.prod: xamarin
ms.assetid: F8FC081B-A811-4FBB-B8F8-30D6FD36BD40
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 09/23/2019
ms.openlocfilehash: 76535f9c31a9dc138e132a3e582b986daf89bdb0
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697672"
---
# <a name="xamarinforms-map-pins"></a>PIN de mapa de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

El control Xamarin. Forms `Maps` permite marcar ubicaciones con objetos `Pin`. Un `Pin` es un marcador de mapa que abre una ventana de información al hacer clic o tocar.

La clase `Pin` tiene las siguientes propiedades:

- `Type` es un valor de enumeración `PinType`: Generic, Place, SavedPin o SearchResult.
- `Position` es una instancia de `Position` que contiene la latitud y la longitud del PIN.
- `Label` es una `string` que se muestra normalmente como el título del PIN.
- `Address` es un `string` que se mostrará en la ventana de información. Puede ser cualquier `string` contenido, no solo una dirección.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que el `Pin` puede ser el destino de los enlaces de datos. Para obtener más información, consulte [Create PIN with Data Binding](#create-pins-with-data-binding).

## <a name="create-map-pins"></a>Crear PIN de mapa

Se puede crear una instancia de `Pin` en el código y agregarla a un mapa:

```csharp
Pin pin1 = new Pin
{
    Type = PinType.Place,
    Position = new Position(47.6368678, -122.137305),
    Label = "Example Pin 1",
    Address = "Example custom details..."
};
map.Pins.Add(pin1);
```

> [!NOTE]
> El valor `PinType` afecta al modo en que se representan los pin según la plataforma. Para personalizar la apariencia de un PIN, debe crear un representador personalizado. Para obtener más información, consulte [Personalización de un PIN de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

## <a name="create-pins-with-data-binding"></a>Crear pin con enlace de datos

La clase [`Map`](xref:Xamarin.Forms.Maps.Map) expone las siguientes propiedades:

- `ItemsSource`: especifica la colección de elementos de `IEnumerable` que se va a mostrar.
- `ItemTemplate`: especifica el [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que se va a aplicar a cada elemento de la colección de elementos mostrados.
- `ItemTemplateSelector`: especifica el [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) que se usará para elegir un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) para un elemento en tiempo de ejecución.

> [!NOTE]
> La propiedad `ItemTemplate` tiene prioridad cuando se establecen las propiedades `ItemTemplate` y `ItemTemplateSelector`.

Un [`Map`](xref:Xamarin.Forms.Maps.Map) se puede rellenar con datos mediante el enlace de datos para enlazar su propiedad `ItemsSource` a una colección `IEnumerable`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="WorkingWithMaps.PinItemsSourcePage">
    <Grid>
        ...
        <maps:Map x:Name="map"
                  MoveToLastRegionOnLayoutChange="false"
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

Los datos de la propiedad `ItemsSource` se enlazan a la propiedad `Locations` del modelo de vista conectado, que devuelve un `ObservableCollection` de `Location` objetos, que es un tipo personalizado. Cada objeto `Location` define `Address` y `Description` propiedades, de tipo `string` y una propiedad `Position`, de tipo [`Position`](xref:Xamarin.Forms.Maps.Position).

La apariencia de cada elemento de la colección de `IEnumerable` se define estableciendo la propiedad `ItemTemplate` en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que contiene un [`Pin`](xref:Xamarin.Forms.Maps.Pin) objeto que los datos enlazan a las propiedades adecuadas.

Las capturas de pantallas siguientes muestran un [`Map`](xref:Xamarin.Forms.Maps.Map) mostrar una colección de [`Pin`](xref:Xamarin.Forms.Maps.Pin) mediante el enlace de datos:

[![Captura de pantalla de la asignación con PIN enlazados a datos, en iOS y Android](map-images/pins-itemssource.png "Asignación con PIN enlazados a datos")](map-images/pins-itemssource-large.png#lightbox "Asignación con PIN enlazados a datos")

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

Para obtener más información sobre los selectores de plantilla de datos, vea [crear un DataTemplateSelector de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="pin-events"></a>Anclar eventos

La clase `Pin` proporciona dos eventos:

- `MarkerClicked` se desencadena cuando se hace clic o se puntea en el PIN.
- `InfoWindowClicked` se desencadena cuando se hace clic o se puntea en la ventana información.

Los controladores de eventos se pueden adjuntar a un PIN en el código:

```csharp
public class PinEvents: ContentPage
{
    public PinEvents()
    {
        // ...

        pin1.MarkerClicked += OnMarkerClickedAsync;
        pin1.InfoWindowClicked += OnInfoWindowClickedAsync;
    }

    async void OnMarkerClickedAsync(object sender, PinClickedEventArgs e)
    {
        string pinName = ((Pin)sender).Label;
        await DisplayAlert("Pin Clicked", $"{pinName} was clicked.", "Ok");
    }

    async void OnInfoWindowClickedAsync(object sender, PinClickedEventArgs e)
    {
        string pinName = ((Pin)sender).Label;
        await DisplayAlert("Info Window Clicked", $"The info window was clicked for {pinName}.", "Ok");
    }
}
```

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Representador personalizado de asignación](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Crear un DataTemplateSelector de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
