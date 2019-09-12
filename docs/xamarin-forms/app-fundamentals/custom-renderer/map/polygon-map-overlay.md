---
title: Resaltado de una región en un mapa
description: En este artículo se explica cómo agregar una superposición de polígono a un mapa para resaltar una región del mapa. Los polígonos son una forma cerrada y tienen relleno en su interior.
ms.prod: xamarin
ms.assetid: E79EB2CF-8DD6-44A8-B47D-5F0A94FB0A63
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 103d4f40a1c368f576276c4cdcbdc585d2a1536a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771889"
---
# <a name="highlighting-a-region-on-a-map"></a>Resaltado de una región en un mapa

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-polygon)

_En este artículo se explica cómo agregar una superposición de polígono a un mapa para resaltar una región del mapa. Los polígonos son una forma cerrada y tienen relleno en su interior._

## <a name="overview"></a>Información general

Una superposición es un gráfico superpuesto en una capa en un mapa. Las superposiciones permiten dibujar contenido gráfico que se escala con el mapa al ampliarlo o reducirlo. En las capturas de pantalla siguientes, se muestra el resultado de agregar una superposición de polígono a un mapa:

![](polygon-map-overlay-images/screenshots.png)

Cuando se representa un control [`Map`](xref:Xamarin.Forms.Maps.Map) mediante una aplicación de Xamarin.Forms, se crea una instancia de la clase `MapRenderer` en iOS que, a su vez, crea una instancia de un control `MKMapView` nativo. En la plataforma de Android, la clase `MapRenderer` crea una instancia de un control `MapView` nativo. En la Plataforma universal de Windows (UWP), la clase `MapRenderer` crea una instancia de un elemento `MapControl` nativo. El proceso de representación se puede aprovechar para implementar personalizaciones de mapa específicas de cada plataforma mediante la creación de un representador personalizado para un elemento `Map` en cada plataforma. Para hacerlo, siga este procedimiento:

1. [Cree](#Creating_the_Custom_Map) un mapa personalizado de Xamarin.Forms.
1. [Use](#Consuming_the_Custom_Map) el mapa personalizado desde Xamarin.Forms.
1. [Personalice](#Customizing_the_Map) el mapa; para hacerlo, cree un representador personalizado para el mapa en cada plataforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) tiene que inicializarse y configurarse antes de cada uso. Para obtener más información, vea [`Maps Control`](~/xamarin-forms/user-interface/map.md).

Para obtener información sobre cómo personalizar un mapa mediante un representador personalizado, vea [Customizing a Map Pin](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md) (Personalizar un marcador de mapa).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Crear un mapa personalizado

Cree una subclase de la clase [`Map`](xref:Xamarin.Forms.Maps.Map) que agregue una propiedad `ShapeCoordinates`:

```csharp
public class CustomMap : Map
{
  public List<Position> ShapeCoordinates { get; set; }

  public CustomMap ()
  {
    ShapeCoordinates = new List<Position> ();
  }
}
```

La propiedad `ShapeCoordinates` almacenará una colección de coordenadas que definen la región que se resaltará.

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
    customMap.ShapeCoordinates.Add (new Position (37.797513, -122.402058));
    customMap.ShapeCoordinates.Add (new Position (37.798433, -122.402256));
    customMap.ShapeCoordinates.Add (new Position (37.798582, -122.401071));
    customMap.ShapeCoordinates.Add (new Position (37.797658, -122.400888));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (0.1)));
  }
}
```

Esta inicialización especifica una serie de coordenadas de latitud y longitud para definir la región que se resaltará en el mapa. Después, coloque la vista del mapa con el método [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*), que cambia la posición y el nivel de zoom del mapa mediante la creación de un elemento [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) desde los elementos [`Position`](xref:Xamarin.Forms.Maps.Position) y [`Distance`](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalización del mapa

Ahora, es necesario agregar un representador personalizado a cada proyecto de aplicación para agregar la superposición de polígono al mapa.

#### <a name="creating-the-custom-renderer-on-ios"></a>Creación del representador personalizado en iOS

Cree una subclase de la clase `MapRenderer` y reemplace su método `OnElementChanged` para agregar la superposición de polígono:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        MKPolygonRenderer polygonRenderer;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveOverlays(nativeMap.Overlays);
                    nativeMap.OverlayRenderer = null;
                    polygonRenderer = null;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;

                nativeMap.OverlayRenderer = GetOverlayRenderer;

                CLLocationCoordinate2D[] coords = new CLLocationCoordinate2D[formsMap.ShapeCoordinates.Count];

                int index = 0;
                foreach (var position in formsMap.ShapeCoordinates)
                {
                    coords[index] = new CLLocationCoordinate2D(position.Latitude, position.Longitude);
                    index++;
                }

                var blockOverlay = MKPolygon.FromCoordinates(coords);
                nativeMap.AddOverlay(blockOverlay);
            }
        }
        ...
    }
}

```

Este método realiza la siguiente configuración, siempre que el representador personalizado esté asociado a un nuevo elemento de Xamarin.Forms:

- La propiedad `MKMapView.OverlayRenderer` se establece en un delegado correspondiente.
- La colección de coordenadas de latitud y longitud se recupera desde la propiedad `CustomMap.ShapeCoordinates` y se almacena como una matriz de instancias de `CLLocationCoordinate2D`.
- Para crear el polígono, se realiza una llamada al método `MKPolygon.FromCoordinates` estático, que especifica la latitud y longitud de cada punto.
- El polígono se agrega al mapa mediante una llamada al método `MKMapView.AddOverlay`. Este método cierra automáticamente el polígono dibujando una línea que conecta el primer y el último punto.

Después, se implementa el método `GetOverlayRenderer` para personalizar la representación de la superposición:

```csharp
public class CustomMapRenderer : MapRenderer
{
  MKPolygonRenderer polygonRenderer;
  ...

  MKOverlayRenderer GetOverlayRenderer(MKMapView mapView, IMKOverlay overlayWrapper)
  {
      if (polygonRenderer == null && !Equals(overlayWrapper, null)) {
          var overlay = Runtime.GetNSObject(overlayWrapper.Handle) as IMKOverlay;
          polygonRenderer = new MKPolygonRenderer(overlay as MKPolygon) {
              FillColor = UIColor.Red,
              StrokeColor = UIColor.Blue,
              Alpha = 0.4f,
              LineWidth = 9
          };
      }
      return polygonRenderer;
  }
}
```

#### <a name="creating-the-custom-renderer-on-android"></a>Creación del representador personalizado en Android

Cree una subclase de la clase `MapRenderer` y reemplace sus métodos `OnElementChanged` y `OnMapReady` para agregar la superposición de polígono:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.Droid
{
    public class CustomMapRenderer : MapRenderer
    {
        List<Position> shapeCoordinates;

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
                shapeCoordinates = formsMap.ShapeCoordinates;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(Android.Gms.Maps.GoogleMap map)
        {
            base.OnMapReady(map);

            var polygonOptions = new PolygonOptions();
            polygonOptions.InvokeFillColor(0x66FF0000);
            polygonOptions.InvokeStrokeColor(0x660000FF);
            polygonOptions.InvokeStrokeWidth(30.0f);

            foreach (var position in shapeCoordinates)
            {
                polygonOptions.Add(new LatLng(position.Latitude, position.Longitude));
            }
            NativeMap.AddPolygon(polygonOptions);
        }
    }
}
```

El método `OnElementChanged` recupera la colección de coordenadas de latitud y longitud de la propiedad `CustomMap.ShapeCoordinates` y las almacena en una variable miembro. Después, realiza una llamada al método `MapView.GetMapAsync`, que obtiene el `GoogleMap` subyacente vinculado a la vista, siempre que el representador personalizado esté asociado a un nuevo elemento de Xamarin.Forms. Cuando la instancia de `GoogleMap` esté disponible, se invocará al método `OnMapReady`, que genera el polígono mediante la creación de una instancia de un objeto `PolygonOptions` que especifica la latitud y longitud de cada punto. Después el polígono se agrega al mapa mediante una llamada al método `NativeMap.AddPolygon`. Este método cierra automáticamente el polígono dibujando una línea que conecta el primer y el último punto.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Creación del representador personalizado en la Plataforma universal de Windows

Cree una subclase de la clase `MapRenderer` y reemplace su método `OnElementChanged` para agregar la superposición de polígono:

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
                foreach (var position in formsMap.ShapeCoordinates)
                {
                    coordinates.Add(new BasicGeoposition() { Latitude = position.Latitude, Longitude = position.Longitude });
                }

                var polygon = new MapPolygon();
                polygon.FillColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polygon.StrokeColor = Windows.UI.Color.FromArgb(128, 0, 0, 255);
                polygon.StrokeThickness = 5;
                polygon.Path = new Geopath(coordinates);
                nativeMap.MapElements.Add(polygon);
            }
        }
    }
}
```

Este método realiza las operaciones siguientes, siempre que el representador personalizado esté asociado a un nuevo elemento de Xamarin.Forms:

- La colección de coordenadas de latitud y longitud se recupera desde la propiedad `CustomMap.ShapeCoordinates` y se convierte a un elemento `List` de coordenadas de `BasicGeoposition`.
- El polígono se genera mediante la creación de una instancia de un objeto `MapPolygon`. La clase `MapPolygon` se utiliza para mostrar una forma de varios puntos en el mapa; para ello, se establece su propiedad `Path` en un objeto `Geopath` que contiene las coordenadas de la forma.
- Para representar el polígono en el mapa, se agrega a la colección `MapControl.MapElements`. Tenga en cuenta que el polígono se cerrará automáticamente dibujando una línea que conecta el primer y el último punto.

## <a name="summary"></a>Resumen

En este artículo se explicó cómo agregar una superposición de polígono a un mapa para resaltar una región del mapa. Los polígonos son una forma cerrada y tienen relleno en su interior.

## <a name="related-links"></a>Vínculos relacionados

- [Polygon Map Overlay (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-polygon) (Superposición de mapa de polígono [ejemplo])
- [Personalización de un anclado de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
