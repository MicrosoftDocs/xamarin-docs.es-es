---
title: Resaltar una ruta en un mapa
description: En este artículo, se explica cómo agregar una superposición de polilínea a un mapa. Una superposición de polilínea es una serie de segmentos de línea conectados que suelen usarse para mostrar una ruta en un mapa o para componer cualquier forma necesaria.
ms.prod: xamarin
ms.assetid: FBFDC715-1654-4188-82A0-FC522548BCFF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: af6e491725ac3dad843238c1adb547ac76a9c9ea
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771865"
---
# <a name="highlighting-a-route-on-a-map"></a>Resaltar una ruta en un mapa

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-polyline)

_En este artículo, se explica cómo agregar una superposición de polilínea a un mapa. Una superposición de polilínea es una serie de segmentos de línea conectados que suelen usarse para mostrar una ruta en un mapa o para componer cualquier forma necesaria._

## <a name="overview"></a>Información general

Una superposición es un gráfico superpuesto en una capa en un mapa. Las superposiciones permiten dibujar contenido gráfico que se escala con el mapa al ampliarlo o reducirlo. En las capturas de pantalla siguientes, se muestra el resultado de agregar una superposición de polilínea a un mapa:

![](polyline-map-overlay-images/screenshots.png)

Cuando se representa un control [`Map`](xref:Xamarin.Forms.Maps.Map) mediante una aplicación de Xamarin.Forms, se crea una instancia de la clase `MapRenderer` en iOS que, a su vez, crea una instancia de un control `MKMapView` nativo. En la plataforma de Android, la clase `MapRenderer` crea una instancia de un control `MapView` nativo. En la Plataforma universal de Windows (UWP), la clase `MapRenderer` crea una instancia de un elemento `MapControl` nativo. El proceso de representación se puede aprovechar para implementar personalizaciones de mapa específicas de cada plataforma mediante la creación de un representador personalizado para un elemento `Map` en cada plataforma. Para hacerlo, siga este procedimiento:

1. [Cree](#Creating_the_Custom_Map) un mapa personalizado de Xamarin.Forms.
1. [Use](#Consuming_the_Custom_Map) el mapa personalizado desde Xamarin.Forms.
1. [Personalice](#Customizing_the_Map) el mapa; para hacerlo, cree un representador personalizado para el mapa en cada plataforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) tiene que inicializarse y configurarse antes de cada uso. Para obtener más información, vea [`Maps Control`](~/xamarin-forms/user-interface/map.md).

Para obtener información sobre cómo personalizar un mapa mediante un representador personalizado, vea [Customizing a Map Pin](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md) (Personalizar un marcador de mapa).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Crear un mapa personalizado

Cree una subclase de la clase [`Map`](xref:Xamarin.Forms.Maps.Map) que agregue una propiedad `RouteCoordinates`:

```csharp
public class CustomMap : Map
{
  public List<Position> RouteCoordinates { get; set; }

  public CustomMap ()
  {
    RouteCoordinates = new List<Position> ();
  }
}
```

La propiedad `RouteCoordinates` almacenará una colección de coordenadas que definen la ruta que se resaltará.

<a name="Consuming_the_Custom_Map" />

### <a name="consuming-the-custom-map"></a>Usar el mapa personalizado

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

Inicialice el control `CustomMap` según sea necesario:

```csharp
public partial class MapPage : ContentPage
{
  public MapPage ()
  {
    ...
    customMap.RouteCoordinates.Add (new Position (37.785559, -122.396728));
    customMap.RouteCoordinates.Add (new Position (37.780624, -122.390541));
    customMap.RouteCoordinates.Add (new Position (37.777113, -122.394983));
    customMap.RouteCoordinates.Add (new Position (37.776831, -122.394627));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (1.0)));
  }
}
```

Esta inicialización especifica una serie de coordenadas de latitud y longitud para definir la ruta que se resaltará en el mapa. Después, coloca la vista del mapa con el método [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*), que cambia la posición y el nivel de zoom del mapa mediante la creación de un elemento [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) desde los elementos [`Position`](xref:Xamarin.Forms.Maps.Position) y [`Distance`](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalizar el mapa

Ahora, es necesario agregar un representador personalizado a cada proyecto de aplicación para agregar la superposición de polilínea al mapa.

#### <a name="creating-the-custom-renderer-on-ios"></a>Crear un representador personalizado en iOS

Cree una subclase de la clase `MapRenderer` y reemplace su método `OnElementChanged` para agregar la superposición de polilínea:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        MKPolylineRenderer polylineRenderer;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveOverlays(nativeMap.Overlays);
                    nativeMap.OverlayRenderer = null;
                    polylineRenderer = null;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                nativeMap.OverlayRenderer = GetOverlayRenderer;

                CLLocationCoordinate2D[] coords = new CLLocationCoordinate2D[formsMap.RouteCoordinates.Count];
                int index = 0;
                foreach (var position in formsMap.RouteCoordinates)
                {
                    coords[index] = new CLLocationCoordinate2D(position.Latitude, position.Longitude);
                    index++;
                }

                var routeOverlay = MKPolyline.FromCoordinates(coords);
                nativeMap.AddOverlay(routeOverlay);
            }
        }
        ...
    }
}

```

Este método realiza la siguiente configuración, siempre que el representador personalizado esté asociado a un nuevo elemento de Xamarin.Forms:

- La propiedad `MKMapView.OverlayRenderer` se establece en un delegado correspondiente.
- La colección de coordenadas de latitud y longitud se recupera desde la propiedad `CustomMap.RouteCoordinates` y se almacena como una matriz de instancias de `CLLocationCoordinate2D`.
- Para crear la polilínea, se realiza una llamada al método `MKPolyline.FromCoordinates` estático, que especifica la latitud y longitud de cada punto.
- La polilínea se agrega al mapa mediante una llamada al método `MKMapView.AddOverlay`.

Después, se implementa el método `GetOverlayRenderer` para personalizar la representación de la superposición:

```csharp
public class CustomMapRenderer : MapRenderer
{
  MKPolylineRenderer polylineRenderer;
  ...

  MKOverlayRenderer GetOverlayRenderer(MKMapView mapView, IMKOverlay overlayWrapper)
  {
      if (polylineRenderer == null && !Equals(overlayWrapper, null)) {
          var overlay = Runtime.GetNSObject(overlayWrapper.Handle) as IMKOverlay;
          polylineRenderer = new MKPolylineRenderer(overlay as MKPolyline) {
              FillColor = UIColor.Blue,
              StrokeColor = UIColor.Red,
              LineWidth = 3,
              Alpha = 0.4f
          };
      }
      return polylineRenderer;
  }
}
```

#### <a name="creating-the-custom-renderer-on-android"></a>Crear un representador personalizado en Android

Cree una subclase de la clase `MapRenderer` y reemplace sus métodos `OnElementChanged` y `OnMapReady` para agregar la superposición de polilínea:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.Droid
{
    public class CustomMapRenderer : MapRenderer
    {
        List<Position> routeCoordinates;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                routeCoordinates = formsMap.RouteCoordinates;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(Android.Gms.Maps.GoogleMap map)
        {
            base.OnMapReady(map);

            var polylineOptions = new PolylineOptions();
            polylineOptions.InvokeColor(0x66FF0000);

            foreach (var position in routeCoordinates)
            {
                polylineOptions.Add(new LatLng(position.Latitude, position.Longitude));
            }

            NativeMap.AddPolyline(polylineOptions);
        }
    }
}
```

El método `OnElementChanged` recupera la colección de coordenadas de latitud y longitud de la propiedad `CustomMap.RouteCoordinates` y las almacena en una variable miembro. Después, realiza una llamada al método `MapView.GetMapAsync`, que obtiene el elemento `GoogleMap` subyacente vinculado a la vista, siempre que el representador personalizado esté asociado a un nuevo elemento de Xamarin.Forms. Cuando la instancia de `GoogleMap` esté disponible, se invocará al método `OnMapReady`, que genera la polilínea mediante la creación de una instancia de un objeto `PolylineOptions` que especifica la latitud y longitud de cada punto. Después, la polilínea se agrega al mapa mediante una llamada al método `NativeMap.AddPolyline`.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Crear el representador personalizado en la Plataforma universal de Windows

Cree una subclase de la clase `MapRenderer` y reemplace su método `OnElementChanged` para agregar la superposición de polilínea:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
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

                var coordinates = new List<BasicGeoposition>();
                foreach (var position in formsMap.RouteCoordinates)
                {
                    coordinates.Add(new BasicGeoposition() { Latitude = position.Latitude, Longitude = position.Longitude });
                }

                var polyline = new MapPolyline();
                polyline.StrokeColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polyline.StrokeThickness = 5;
                polyline.Path = new Geopath(coordinates);
                nativeMap.MapElements.Add(polyline);
            }
        }
    }
}
```

Este método realiza las operaciones siguientes, siempre que el representador personalizado esté asociado a un nuevo elemento de Xamarin.Forms:

- La colección de coordenadas de latitud y longitud se recupera desde la propiedad `CustomMap.RouteCoordinates` y se convierte a un elemento `List` de coordenadas de `BasicGeoposition`.
- La polilínea se genera mediante la creación de una instancia de un objeto `MapPolyline`. La clase `MapPolygon` se usa para mostrar una línea en el mapa mediante el ajuste de su propiedad `Path` en un objeto `Geopath` que contiene las coordenadas de línea.
- Para representar la polilínea en el mapa, se agrega a la colección `MapControl.MapElements`.

## <a name="summary"></a>Resumen

En este artículo, se ha explicado cómo agregar una superposición de polilínea a un mapa para mostrar una ruta en un mapa o para componer cualquier forma necesaria.

## <a name="related-links"></a>Vínculos relacionados

- [Superposición de mapa de polilínea (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-polyline)
- [Personalización de un anclado de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
