---
title: Anotaciones y superposiciones en Xamarin. iOS
description: En este artículo se presenta un tutorial paso a paso en el que se muestra cómo trabajar con las características de anotación y superposición del kit de mapa. Muestra cómo agregar una asignación a una aplicación que muestra una anotación y superposición en la ubicación de la Conferencia de Xamarin evolucion 2013.
ms.prod: xamarin
ms.assetid: 1BC4F7FC-AE3C-46D7-A4D3-18E142F55B8E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: dba8fc3c239a8c20795913d6d1e5409a478c4072
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642728"
---
# <a name="annotations-and-overlays-in-xamarinios"></a>Anotaciones y superposiciones en Xamarin. iOS

La aplicación que vamos a crear en este tutorial se muestra a continuación:

 [![](ios-maps-walkthrough-images/00-map-overlay.png "Una aplicación de ejemplo de MapKit")](ios-maps-walkthrough-images/00-map-overlay.png#lightbox)
 
Puede encontrar el código completado en el [ejemplo de tutorial de Maps](https://docs.microsoft.com/samples/xamarin/ios-samples/mapswalkthrough).

Comencemos con la creación de un nuevo **proyecto de iOS vacío**y asignándole un nombre relevante. Comenzaremos agregando código a nuestro controlador de vistas para mostrar el MapView y, a continuación, crearemos nuevas clases para MapDelegate y las anotaciones personalizadas. Para ello, siga los pasos indicados a continuación:

## <a name="viewcontroller"></a>ViewController


1. Agregue los siguientes espacios de nombres al `ViewController`:

    ```csharp
    using MapKit;
    using CoreLocation;
    using UIKit
    using CoreGraphics
    ```

1. Agregue una `MKMapView` variable de instancia a la clase, junto con `MapDelegate` una instancia de. Crearemos lo `MapDelegate` siguiente:

    ```csharp
    public partial class ViewController : UIViewController
    {
        MKMapView map;
        MapDelegate mapDelegate;
        ...
    ```

1. En el método del `LoadView` controlador, `MKMapView` agregue y establézcalo en la `View` propiedad del controlador:

    ```csharp
    public override void LoadView ()
    {
        map = new MKMapView (UIScreen.MainScreen.Bounds);
        View = map;
    }
    ```

    A continuación, agregaremos código para inicializar la asignación en el método ' ViewDidLoad ' '.

1. En `ViewDidLoad` agregar código para establecer el tipo de asignación, muestre la ubicación del usuario y permita el zoom y la panorámica:

    ```csharp
    // change map type, show user location and allow zooming and panning
    map.MapType = MKMapType.Standard;
    map.ShowsUserLocation = true;
    map.ZoomEnabled = true;
    map.ScrollEnabled = true;
    
    ```

1. Después, agregue el código para centrar el mapa y establecer su región:

    ```csharp
    double lat = 30.2652233534254;
    double lon = -97.73815460962083;
    CLLocationCoordinate2D mapCenter = new CLLocationCoordinate2D (lat, lon);
    MKCoordinateRegion mapRegion = MKCoordinateRegion.FromDistance (mapCenter, 100, 100);
    map.CenterCoordinate = mapCenter;
    map.Region = mapRegion;
    
    ```

1. Cree una nueva instancia de `MapDelegate` y asígnela `Delegate` al de `MKMapView`. De `MapDelegate` nuevo, nos implcodeent en breve:

    ```csharp
    mapDelegate = new MapDelegate ();
    map.Delegate = mapDelegate;     
    ```

1. A partir de iOS 8, debe solicitar la autorización del usuario para usar su ubicación, así que vamos a agregarlo a nuestro ejemplo. En primer lugar, `CLLocationManager` defina una variable de nivel de clase:

    ```csharp
    CLLocationManager locationManager = new CLLocationManager();
    ```

1. En el `ViewDidLoad` método, queremos comprobar si el dispositivo que ejecuta la aplicación está usando iOS 8 y, si es así, solicitaremos la autorización cuando la aplicación esté en uso:

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion(8,0)){
                    locationManager.RequestWhenInUseAuthorization ();
                }
    ```

1. Por último, es necesario editar el archivo **info. plist** para avisar a los usuarios del motivo de la solicitud de su ubicación. En el menú **origen** de **info. plist**, agregue la siguiente clave:
    
    `NSLocationWhenInUseUsageDescription` 
    
    y cadena: 

    `Maps Walkthrough Docs Sample`


## <a name="conferenceannotationcs--a-class-for-custom-annotations"></a>ConferenceAnnotation.cs: una clase para anotaciones personalizadas


1. Vamos a usar una clase personalizada para la anotación denominada `ConferenceAnnotation`. Agregue la siguiente clase al proyecto:

    ```csharp
    using System;
    using CoreLocation;
    using MapKit;
    
    namespace MapsWalkthrough
    {
        public class ConferenceAnnotation : MKAnnotation
        {
            string title;
            CLLocationCoordinate2D coord;
    
            public ConferenceAnnotation (string title,
            CLLocationCoordinate2D coord)
            {
                this.title = title;
                this.coord = coord;
            }
    
            public override string Title {
                get {
                    return title;
                }
            }
    
            public override CLLocationCoordinate2D Coordinate {
                get {
                    return coord;
                }
            }
        }
    }   
    ```

## <a name="viewcontroller---adding-the-annotation-and-overlay"></a>ViewController: agregar la anotación y la superposición

1. Con el `ConferenceAnnotation` en su lugar, podemos agregarlo al mapa. De nuevo en `ViewDidLoad` el método `ViewController`de, agregue la anotación en la coordenada central del mapa:

    ```csharp
    map.AddAnnotations (new ConferenceAnnotation ("Evolve Conference", mapCenter)); 
    ```

1. También queremos tener una superposición del hotel. Agregue el código siguiente para crear el `MKPolygon` mediante las coordenadas del Hotel proporcionado y agréguelo al mapa mediante una llamada `AddOverlay`a:

    ```csharp
    // add an overlay of the hotel
    MKPolygon hotelOverlay = MKPolygon.FromCoordinates(
        new CLLocationCoordinate2D[]{
        new CLLocationCoordinate2D(30.2649977168594, -97.73863627705),
        new CLLocationCoordinate2D(30.2648461170005, -97.7381627734755),
        new CLLocationCoordinate2D(30.2648355402574, -97.7381750192576),
        new CLLocationCoordinate2D(30.2647791309417, -97.7379872505988),
        new CLLocationCoordinate2D(30.2654525150319, -97.7377341711021),
        new CLLocationCoordinate2D(30.2654807195004, -97.7377994819399),
        new CLLocationCoordinate2D(30.2655089239607, -97.7377994819399),
        new CLLocationCoordinate2D(30.2656428950368, -97.738346460207),
        new CLLocationCoordinate2D(30.2650364981811, -97.7385709662122),
        new CLLocationCoordinate2D(30.2650470749025, -97.7386199493406)
    });
    
    map.AddOverlay (hotelOverlay);  
    ```
Esto completa el código en `ViewDidLoad`. Ahora debemos implementar nuestra `MapDelegate` clase para controlar la creación de las vistas de anotación y superposición, respectivamente.


## <a name="mapdelegate"></a>MapDelegate

1. Cree una clase denominada `MapDelegate` que herede de `MKMapViewDelegate` e incluya una `annotationId` variable que se utilizará como identificador de reutilización para la anotación:

    ```csharp
    class MapDelegate : MKMapViewDelegate
    {
        static string annotationId = "ConferenceAnnotation";
        ...
    }
    ```
    Aquí solo tenemos una anotación para que el código de reutilización no sea estrictamente necesario, pero es recomendable incluirlo.

1. Implemente `GetViewForAnnotation` el método para devolver una vista para `ConferenceAnnotation` la con la imagen de **Conference. png** incluida en este tutorial:

    ```csharp
    public override MKAnnotationView GetViewForAnnotation (MKMapView mapView, NSObject annotation)
    {
        MKAnnotationView annotationView = null;
    
        if (annotation is MKUserLocation)
            return null; 
    
        if (annotation is ConferenceAnnotation) {
    
            // show conference annotation
            annotationView = mapView.DequeueReusableAnnotation (annotationId);
    
            if (annotationView == null)
                annotationView = new MKAnnotationView (annotation, annotationId);
        
            annotationView.Image = UIImage.FromFile ("images/conference.png");
            annotationView.CanShowCallout = true;
        } 
    
        return annotationView;
    }
    ```

1. Cuando el usuario puntea en la anotación, queremos mostrar una imagen que muestre la ciudad de Austin. Agregue las siguientes variables al `MapDelegate` para la imagen y la vista para mostrarla:

    ```csharp
    UIImageView venueView;
    UIImage venueImage;
    ```

1. A continuación, para mostrar la imagen cuando se puntea la anotación, implemente `DidSelectAnnotation` el método de la siguiente manera:

    ```csharp
    public override void DidSelectAnnotationView (MKMapView mapView, MKAnnotationView view)
    {
        // show an image view when the conference annotation view is selected
        if (view.Annotation is ConferenceAnnotation) {
    
            venueView = new UIImageView ();
            venueView.ContentMode = UIViewContentMode.ScaleAspectFit;
            venueImage = UIImage.FromFile ("image/venue.png");
            venueView.Image = venueImage;
            view.AddSubview (venueView);
    
            UIView.Animate (0.4, () => {
            venueView.Frame = new CGRect (-75, -75, 200, 200); });
        }
    }
    ```

1. Para ocultar la imagen cuando el usuario anule la selección de la anotación punteando en cualquier otra parte del mapa, implemente el método de la `DidSelectAnnotationView` siguiente manera:

    ```csharp
    public override void DidDeselectAnnotationView (MKMapView mapView, MKAnnotationView view)
    {
        // remove the image view when the conference annotation is deselected
        if (view.Annotation is ConferenceAnnotation) {
    
            venueView.RemoveFromSuperview ();
            venueView.Dispose ();
            venueView = null;
        }
    }
    ```
    Ahora tenemos el código para la anotación en contexto. Lo único que queda es agregar código a `MapDelegate` para crear la vista para la superposición de Hotel.

1. Agregue la siguiente implementación de `GetViewForOverlay` `MapDelegate`a:

    ```csharp
    public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
    {
        // return a view for the polygon
        MKPolygon polygon = overlay as MKPolygon;
        MKPolygonView polygonView = new MKPolygonView (polygon);
        polygonView.FillColor = UIColor.Blue;
        polygonView.StrokeColor = UIColor.Red;
        return polygonView;
    }
    ```

Ejecute la aplicación. Ahora tenemos un mapa interactivo con una anotación personalizada y una superposición. Pulse en la anotación y se mostrará la imagen de Austin, como se muestra a continuación:

 [![](ios-maps-walkthrough-images/01-map-image.png "Pulse en la anotación y se mostrará la imagen de Austin")](ios-maps-walkthrough-images/01-map-image.png#lightbox)

## <a name="summary"></a>Resumen

En este artículo hemos visto cómo agregar una anotación a un mapa y cómo agregar una superposición para un polígono especificado. También hemos mostrado cómo agregar compatibilidad táctil a la anotación para animar una imagen sobre un mapa.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de tutorial de Maps](https://docs.microsoft.com/samples/xamarin/ios-samples/mapswalkthrough)
- [Ejemplo de demostración de mapa](https://docs.microsoft.com/samples/xamarin/ios-samples/mapdemo)
- [Maps in Xamarin.iOS](~/ios/user-interface/controls/ios-maps/index.md) (Mapas en Xamarin.iOS)
