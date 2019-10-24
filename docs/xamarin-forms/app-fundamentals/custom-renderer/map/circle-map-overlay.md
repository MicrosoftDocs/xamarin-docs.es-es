---
title: Resaltado de un área circular en un mapa
description: En este artículo se explica cómo agregar una superposición circular a un mapa, para resaltar un área circular del mapa. Si bien iOS y Android proporcionan las API para agregar la superposición circular al mapa, en UWP la superposición se representa como un polígono.
ms.prod: xamarin
ms.assetid: 6FF8BD15-074E-4E6A-9522-F9E2BE32EF12
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 1fe2611e26d357d910cc85800355b42d11e1104b
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697180"
---
# <a name="highlighting-a-circular-area-on-a-map"></a>Resaltado de un área circular en un mapa

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-circle)

_En este artículo se explica cómo agregar una superposición circular a un mapa, para resaltar un área circular del mapa._

## <a name="overview"></a>Información general

Una superposición es un gráfico superpuesto en una capa en un mapa. Las superposiciones admiten dibujar contenido gráfico que se escala con el mapa a medida que se amplía. En las capturas de pantalla siguientes se muestra el resultado de agregar una superposición circular a un mapa:

![](circle-map-overlay-images/screenshots.png)

Cuando una aplicación de Xamarin.Forms representa un control [`Map`](xref:Xamarin.Forms.Maps.Map), en iOS se crea la instancia de la clase `MapRenderer`, que a su vez crea una instancia del control `MKMapView` nativo. En la plataforma de Android, la clase `MapRenderer` crea una instancia de un control `MapView` nativo. En la Plataforma universal de Windows (UWP), la clase `MapRenderer` crea una instancia de un elemento `MapControl` nativo. El proceso de representación se puede aprovechar para implementar personalizaciones de mapa específicas de cada plataforma mediante la creación de un representador personalizado para un elemento `Map` en cada plataforma. Para hacerlo, siga este procedimiento:

1. [Cree](#Creating_the_Custom_Map) un mapa personalizado de Xamarin.Forms.
1. [Use](#Consuming_the_Custom_Map) el mapa personalizado desde Xamarin.Forms.
1. [Personalice](#Customizing_the_Map) el mapa; para hacerlo, cree un representador personalizado para el mapa en cada plataforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) debe inicializarse y configurarse antes de su uso. Para obtener más información, consulte [`Maps Control`](~/xamarin-forms/user-interface/map/index.md)

Para obtener información sobre cómo personalizar un mapa mediante un representador personalizado, consulte [Personalización de un anclado de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Creación del mapa personalizado

Cree una clase `CustomCircle` que tenga las propiedades `Position` y `Radius`:

```csharp
public class CustomCircle
{
  public Position Position { get; set; }
  public double Radius { get; set; }
}
```

A continuación, cree una subclase de la clase [`Map`](xref:Xamarin.Forms.Maps.Map) que agregue una propiedad de tipo `CustomCircle`:

```csharp
public class CustomMap : Map
{
  public CustomCircle Circle { get; set; }
}
```

<a name="Consuming_the_Custom_Map" />

### <a name="consuming-the-custom-map"></a>Consumo del mapa personalizado

Para usar el control `CustomMap`, declare una instancia de este en la instancia de la página de XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MapOverlay;assembly=MapOverlay"
             x:Class="MapOverlay.MapPage">
    <ContentPage.Content>
        <local:CustomMap x:Name="customMap" MapType="Street" WidthRequest="{x:Static local:App.ScreenWidth}" HeightRequest="{x:Static local:App.ScreenHeight}" />
    </ContentPage.Content>
</ContentPage>
```

También puede usar el control `CustomMap` mediante la declaración de una instancia de este en la instancia de la página de C#:

```csharp
public class MapPageCS : ContentPage
{
    public MapPageCS ()
    {
        var customMap = new CustomMap {
            MapType = MapType.Street,
            WidthRequest = App.ScreenWidth,
            HeightRequest = App.ScreenHeight
        };
        ...
        Content = customMap;
    }
}
```

Inicialice el control `CustomMap` como sea necesario:

```csharp
public partial class MapPage : ContentPage
{
  public MapPage ()
  {
    ...
    var pin = new Pin {
      Type = PinType.Place,
      Position = new Position (37.79752, -122.40183),
      Label = "Xamarin San Francisco Office",
      Address = "394 Pacific Ave, San Francisco CA"
    };

    var position = new Position (37.79752, -122.40183);
    customMap.Circle = new CustomCircle {
      Position = position,
      Radius = 1000
    };

    customMap.Pins.Add (pin);
    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (position, Distance.FromMiles (1.0)));
  }
}
```

Esta inicialización agrega instancias de [`Pin`](xref:Xamarin.Forms.Maps.Pin) y `CustomCircle`al mapa personalizado y coloca la vista del mapa con el método [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*), que cambia la posición y el nivel de zoom del mapa mediante la creación de un [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) desde una [`Position`](xref:Xamarin.Forms.Maps.Position) y una [`Distance`](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalizar el mapa

Ahora debe agregarse un representador personalizado a cada proyecto de aplicación para agregar la superposición circular al mapa.

#### <a name="creating-the-custom-renderer-on-ios"></a>Creación del representador personalizado en iOS

Cree una subclase de la clase `MapRenderer` e invalide su método `OnElementChanged` para agregar la superposición circular:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        MKCircleRenderer circleRenderer;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveOverlays(nativeMap.Overlays);
                    nativeMap.OverlayRenderer = null;
                    circleRenderer = null;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                var circle = formsMap.Circle;

                nativeMap.OverlayRenderer = GetOverlayRenderer;

                var circleOverlay = MKCircle.Circle(new CoreLocation.CLLocationCoordinate2D(circle.Position.Latitude, circle.Position.Longitude), circle.Radius);
                nativeMap.AddOverlay(circleOverlay);
            }
        }
        ...
    }
}

```

Este método realiza la configuración siguiente, siempre que el representador personalizado se adjunte a un elemento de Xamarin.Forms nuevo:

- La propiedad `MKMapView.OverlayRenderer` se establece en un delegado correspondiente.
- Para crear el círculo, se debe establecer un objeto `MKCircle` estático que especifica el centro del círculo y su radio en metros.
- Para agregar el círculo al mapa, llame al método `MKMapView.AddOverlay`.

A continuación, implemente el método `GetOverlayRenderer` para personalizar la representación de la superposición:

```csharp
public class CustomMapRenderer : MapRenderer
{
  MKCircleRenderer circleRenderer;
  ...

  MKOverlayRenderer GetOverlayRenderer(MKMapView mapView, IMKOverlay overlayWrapper)
  {
      if (circleRenderer == null && !Equals(overlayWrapper, null)) {
          var overlay = Runtime.GetNSObject(overlayWrapper.Handle) as IMKOverlay;
          circleRenderer = new MKCircleRenderer(overlay as MKCircle) {
              FillColor = UIColor.Red,
              Alpha = 0.4f
          };
      }
      return circleRenderer;
  }
}
```

#### <a name="creating-the-custom-renderer-on-android"></a>Creación del representador personalizado en Android

Cree una subclase de la clase `MapRenderer` e invalide sus métodos `OnElementChanged` y `OnMapReady` para agregar la superposición circular:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.Droid
{
    public class CustomMapRenderer : MapRenderer
    {
        CustomCircle circle;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Xamarin.Forms.Maps.Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                circle = formsMap.Circle;
            }
        }

        protected override void OnMapReady(Android.Gms.Maps.GoogleMap map)
        {
            base.OnMapReady(map);

            var circleOptions = new CircleOptions();
            circleOptions.InvokeCenter(new LatLng(circle.Position.Latitude, circle.Position.Longitude));
            circleOptions.InvokeRadius(circle.Radius);
            circleOptions.InvokeFillColor(0X66FF0000);
            circleOptions.InvokeStrokeColor(0X66FF0000);
            circleOptions.InvokeStrokeWidth(0);

            NativeMap.AddCircle(circleOptions);
        }
    }
}
```

El método `OnElementChanged` recupera los datos de círculo personalizados, siempre que el representador personalizado esté asociado a un nuevo elemento de Xamarin.Forms. Una vez la instancia de `GoogleMap` está disponible, se invoca el método `OnMapReady`, en que el círculo se crea mediante la instancia de un objeto `CircleOptions` que especifica el centro del círculo y su radio en metros. A continuación, se llama al método `NativeMap.AddCircle` para agregar el círculo al mapa.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Creación de un representador personalizado en la Plataforma universal de Windows

Cree una subclase de la clase `MapRenderer` e invalide su método `OnElementChanged` para agregar la superposición circular:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
        const int EarthRadiusInMeteres = 6371000;

        protected override void OnElementChanged(ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }
            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MapControl;
                var circle = formsMap.Circle;

                var coordinates = new List<BasicGeoposition>();
                var positions = GenerateCircleCoordinates(circle.Position, circle.Radius);
                foreach (var position in positions)
                {
                    coordinates.Add(new BasicGeoposition { Latitude = position.Latitude, Longitude = position.Longitude });
                }

                var polygon = new MapPolygon();
                polygon.FillColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polygon.StrokeColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polygon.StrokeThickness = 5;
                polygon.Path = new Geopath(coordinates);
                nativeMap.MapElements.Add(polygon);
            }
        }
        // GenerateCircleCoordinates helper method (below)
    }
}
```

Este método realiza las operaciones siguientes, siempre que el representador personalizado se adjunte a un nuevo elemento de Xamarin.Forms:

- La posición del círculo y el radio se recuperan de la propiedad `CustomMap.Circle` y se pasan al método `GenerateCircleCoordinates`, que genera las coordenadas de latitud y longitud para el perímetro del círculo. El código para este método auxiliar se muestra a continuación.
- Las coordenadas del perímetro del círculo se convierten en coordenadas de una `List` de `BasicGeoposition`.
- El círculo se crea mediante la instancia de un objeto `MapPolygon`. La clase `MapPolygon` se utiliza para mostrar una forma de varios puntos en el mapa; para ello, se establece su propiedad `Path` en un objeto `Geopath` que contiene las coordenadas de la forma.
- Para representar el polígono en el mapa, se agrega a la colección `MapControl.MapElements`.

```
List<Position> GenerateCircleCoordinates(Position position, double radius)
{
    double latitude = position.Latitude.ToRadians();
    double longitude = position.Longitude.ToRadians();
    double distance = radius / EarthRadiusInMeteres;
    var positions = new List<Position>();

    for (int angle = 0; angle <=360; angle++)
    {
        double angleInRadians = ((double)angle).ToRadians();
        double latitudeInRadians = Math.Asin(Math.Sin(latitude) * Math.Cos(distance) + Math.Cos(latitude) * Math.Sin(distance) * Math.Cos(angleInRadians));
        double longitudeInRadians = longitude + Math.Atan2(Math.Sin(angleInRadians) * Math.Sin(distance) * Math.Cos(latitude), Math.Cos(distance) - Math.Sin(latitude) * Math.Sin(latitudeInRadians));

        var pos = new Position(latitudeInRadians.ToDegrees(), longitudeInRadians.ToDegrees());
        positions.Add(pos);
    }

    return positions;
}
```

## <a name="summary"></a>Resumen

En este artículo se explica cómo agregar una superposición circular a un mapa, para resaltar un área circular del mapa.

## <a name="related-links"></a>Vínculos relacionados

- [Superposición de mapa circular (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-circle)
- [Personalización de un anclado de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
