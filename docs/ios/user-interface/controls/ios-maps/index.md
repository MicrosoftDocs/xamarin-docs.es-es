---
title: Mapas en Xamarin. iOS
description: En este documento se describe el marco de trabajo de MapKit de iOS y cómo se usa con Xamarin. iOS. En él se explica cómo agregar un mapa, cómo aplicarle estilo, cómo usar el zoom y la ubicación del usuario, cómo agregar anotaciones, cómo trabajar con llamadas y superposiciones, y cómo realizar la búsqueda local.
ms.prod: xamarin
ms.assetid: 5DD8E56D-51C1-4AFA-B387-79B5734698ED
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 177701b8b50edea965e97da225265912f1f0c198
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86932332"
---
# <a name="maps-in-xamarinios"></a>Mapas en Xamarin. iOS

Las asignaciones son una característica común en todos los sistemas operativos móviles modernos. iOS ofrece compatibilidad de asignación de forma nativa a través del marco del kit de mapas. Con el kit de mapa, las aplicaciones pueden agregar fácilmente mapas completos e interactivos. Estas asignaciones pueden personalizarse de varias formas, como agregar anotaciones para marcar ubicaciones en un mapa y superponer gráficos de formas arbitrarias. El kit de mapas incluso tiene compatibilidad integrada para mostrar la ubicación actual de un dispositivo.

## <a name="adding-a-map"></a>Agregar un mapa

Para agregar una asignación a una aplicación, se agrega una `MKMapView` instancia a la jerarquía de vistas, como se muestra a continuación:

```csharp
// map is an MKMapView declared as a class variable
map = new MKMapView (UIScreen.MainScreen.Bounds);
View = map;
```

`MKMapView`es una `UIView` subclase que muestra un mapa. Simplemente agregar el mapa con el código anterior genera un mapa interactivo:

![Un mapa de ejemplo](images/00-map.png)

## <a name="map-style"></a>Estilo de mapa

`MKMapView`admite 3 estilos diferentes de asignaciones. Para aplicar un estilo de mapa, basta con establecer la `MapType` propiedad en un valor de la `MKMapType` enumeración:

```csharp
map.MapType = MKMapType.Standard; //road map
map.MapType = MKMapType.Satellite;
map.MapType = MKMapType.Hybrid;
```

En la captura de pantalla siguiente se muestran los distintos estilos de mapa que están disponibles:

![Esta captura de pantalla muestra los distintos estilos de mapa que están disponibles](images/01-mapstyles.png)

## <a name="panning-and-zooming"></a>Panorámica y zoom

`MKMapView`incluye compatibilidad con las características de interactividad de mapa, como:

- Zoom a través de un gesto de pinch
- Movimiento panorámico a través de un movimiento de pan

Estas características se pueden habilitar o deshabilitar con solo establecer `ZoomEnabled` las `ScrollEnabled` propiedades y de la `MKMapView` instancia, donde el valor predeterminado es true para ambos. Por ejemplo, para mostrar una asignación estática, simplemente establezca las propiedades adecuadas en false:

```csharp
map.ZoomEnabled = false;
map.ScrollEnabled = false;
```

## <a name="user-location"></a>Ubicación del usuario

Además de la interacción del usuario, `MKMapView` también tiene compatibilidad integrada para mostrar la ubicación del dispositivo. Para ello, se usa el marco de trabajo de *ubicación principal* . Para poder tener acceso a la ubicación del usuario, debe preguntar al usuario. Para ello, cree una instancia de `CLLocationManager` y llame a `RequestWhenInUseAuthorization` .

```csharp
CLLocationManager locationManager = new CLLocationManager();
locationManager.RequestWhenInUseAuthorization();
//locationManager.RequestAlwaysAuthorization(); //requests permission for access to location data while running in the background
```

Tenga en cuenta que en las versiones de iOS anteriores a 8,0, si intenta llamar a, `RequestWhenInUseAuthorization` se producirá un error. Asegúrese de comprobar la versión de iOS antes de realizar esa llamada si tiene previsto admitir versiones anteriores a 8.

El acceso a la ubicación del usuario también requiere modificaciones en **info. plist**. Se deben establecer las siguientes claves relacionadas con los datos de ubicación:

- **NSLocationWhenInUseUsageDescription**: Al obtener acceso a la ubicación del usuario mientras interactúa con la aplicación.
- **NSLocationAlwaysUsageDescription**: Al obtener acceso la aplicación a la ubicación del usuario en segundo plano.

Puede agregar esas claves abriendo **info. plist** y seleccionando *origen* en la parte inferior del editor.

Una vez actualizado **info. plist** y solicitado al usuario permiso para obtener acceso a su ubicación, puede mostrar la ubicación del usuario en el mapa estableciendo la `ShowsUserLocation` propiedad en true:

```csharp
map.ShowsUserLocation = true;
```

 ![Alerta de permiso de acceso de ubicación](images/02-location-alert.png)

## <a name="annotations"></a>Anotaciones

 `MKMapView`también admite la visualización de imágenes, conocidas como anotaciones, en un mapa. Pueden ser imágenes personalizadas o PIN definidos por el sistema de varios colores. Por ejemplo, en la siguiente captura de pantalla se muestra un mapa con un PIN y una imagen personalizada:

 ![Esta captura de pantalla muestra un mapa con un PIN y una imagen personalizada](images/03-annotations.png)

### <a name="adding-an-annotation"></a>Agregar una anotación

Una anotación tiene dos partes:

- `MKAnnotation`Objeto, que incluye los datos del modelo sobre la anotación, como el título y la ubicación de la anotación.
- `MKAnnotationView`Que contiene la imagen que se va a mostrar y, opcionalmente, una llamada que se muestra cuando el usuario puntea la anotación.

El kit de mapas usa el patrón de delegación de iOS para agregar anotaciones a un mapa, donde la `Delegate` propiedad de `MKMapView` se establece en una instancia de `MKMapViewDelegate` . Es la implementación de este delegado la que es responsable de devolver el `MKAnnotationView` para una anotación.

Para agregar una anotación, primero se agrega la anotación llamando a `AddAnnotations` en la instancia de `MKMapView` :

```csharp
// add an annotation
map.AddAnnotations (new MKPointAnnotation (){
    Title="MyAnnotation",
    Coordinate = new CLLocationCoordinate2D (42.364260, -71.120824)
});
```

Cuando la ubicación de la anotación se vuelve visible en el mapa, `MKMapView` llamará al método del delegado `GetViewForAnnotation` para obtener el `MKAnnotationView` que se va a mostrar.

Por ejemplo, el código siguiente devuelve un proporcionado por el sistema `MKPinAnnotationView` :

```csharp
string pId = "PinAnnotation";

public override MKAnnotationView GetViewForAnnotation (MKMapView mapView, NSObject annotation)
{
    if (annotation is MKUserLocation)
        return null;

    // create pin annotation view
    MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);

    if (pinView == null)
        pinView = new MKPinAnnotationView (annotation, pId);

    ((MKPinAnnotationView)pinView).PinColor = MKPinAnnotationColor.Red;
    pinView.CanShowCallout = true;

    return pinView;
}
```

### <a name="reusing-annotations"></a>Reutilizar anotaciones

Para conservar memoria, `MKMapView` permite que la vista de anotaciones se vuelva a agrupar para su reutilización, de manera similar a la forma en que se reutilizan las celdas de la tabla. La obtención de una vista de anotación del grupo se realiza con una llamada a `DequeueReusableAnnotation` :

```csharp
MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);
```

#### <a name="showing-callouts"></a>Mostrar llamadas

Como se mencionó anteriormente, una anotación puede mostrar opcionalmente una llamada. Para mostrar una llamada, simplemente establezca `CanShowCallout` en true en `MKAnnotationView` . Esto da como resultado que se muestre el título de la anotación cuando se puntee en la anotación, como se muestra a continuación:

 ![Título de las anotaciones que se muestra](images/04-callout.png)

### <a name="customizing-the-callout"></a>Personalización de la llamada

La llamada también se puede personalizar para mostrar las vistas de accesorio izquierda y derecha, como se muestra a continuación:

```csharp
pinView.RightCalloutAccessoryView = UIButton.FromType (UIButtonType.DetailDisclosure);
pinView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile ("monkey.png"));
```

Este código da como resultado la siguiente llamada:

 ![Una llamada de ejemplo](images/05-callout-accessories.png)

Para controlar el usuario que pulsa el accesorio derecho, simplemente implemente el `CalloutAccessoryControlTapped` método en `MKMapViewDelegate` :

```csharp
public override void CalloutAccessoryControlTapped (MKMapView mapView, MKAnnotationView view, UIControl control)
{
    ...
}
```

### <a name="overlays"></a>Superposiciones

Otra manera de disponer los gráficos en capas en un mapa es usar superposiciones. Las superposiciones admiten dibujar contenido gráfico que se escala con el mapa a medida que se amplía. iOS proporciona compatibilidad con varios tipos de superposiciones, entre las que se incluyen:

- Polígonos: se usa normalmente para resaltar alguna región en un mapa.
- Polilíneas: se ven a menudo cuando se muestra una ruta.
- Círculos: se usa para resaltar un área circular de un mapa.

Además, se pueden crear superposiciones personalizadas para mostrar geometrías arbitrarias con código de dibujo granular y personalizado. Por ejemplo, el radar meteorológico sería una buena candidata para una superposición personalizada.

#### <a name="adding-an-overlay"></a>Agregar una superposición

De forma similar a las anotaciones, agregar una superposición implica 2 partes:

- Crear un objeto de modelo para la superposición y agregarlo a `MKMapView` .
- Crear una vista para la superposición en `MKMapViewDelegate` .

El modelo para la superposición puede ser cualquier `MKShape` subclase. Xamarin. iOS incluye `MKShape` subclases para polígonos, polilíneas y círculos, a través de las `MKPolygon` `MKPolyline` clases, y, `MKCircle` respectivamente.

Por ejemplo, el código siguiente se usa para agregar un `MKCircle` :

```csharp
var circleOverlay = MKCircle.Circle (mapCenter, 1000);
map.AddOverlay (circleOverlay);
```

La vista de una superposición es una `MKOverlayView` instancia devuelta por el `GetViewForOverlay` en `MKMapViewDelegate` . Cada `MKShape` tiene un correspondiente `MKOverlayView` que sabe cómo mostrar la forma especificada. En el caso de `MKPolygon` `MKPolygonView` . Del mismo modo, `MKPolyline` se corresponde con `MKPolylineView` y en el caso de `MKCircle` `MKCircleView` .

Por ejemplo, el código siguiente devuelve un `MKCircleView` para un `MKCircle` :

```csharp
public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
{
    var circleOverlay = overlay as MKCircle;
    var circleView = new MKCircleView (circleOverlay);
    circleView.FillColor = UIColor.Blue;
    return circleView;
}
```

Esto muestra un círculo en el mapa, como se muestra a continuación:

 ![Un círculo mostrado en el mapa](images/06-circle-overlay.png)

## <a name="local-search"></a>Búsqueda local

iOS incluye una API de búsqueda local con el kit de mapa, que permite búsquedas asincrónicas de puntos de interés en una región geográfica específica.

Para realizar una búsqueda local, una aplicación debe seguir estos pasos:

1. Crear `MKLocalSearchRequest` objeto.
1. Cree un `MKLocalSearch` objeto a partir de `MKLocalSearchRequest` .
1. Llame al `Start` método en el `MKLocalSearch` objeto.
1. Recupera el `MKLocalSearchResponse` objeto en una devolución de llamada.

La propia API de búsqueda local no proporciona ninguna interfaz de usuario. Ni siquiera es necesario usar un mapa. Sin embargo, para hacer uso práctico de la búsqueda local, una aplicación debe proporcionar alguna manera de especificar una consulta de búsqueda y mostrar los resultados. Además, dado que los resultados contendrán datos de ubicación, a menudo tendrá sentido mostrarlos en un mapa.

<a name="Adding_a_Local_Search_UI"></a>

### <a name="adding-a-local-search-ui"></a>Agregar una interfaz de usuario de búsqueda local

Una manera de aceptar la entrada de búsqueda es con `UISearchBar` , que proporciona `UISearchController` y mostrará los resultados en una tabla.

En el código siguiente `UISearchController` se agrega (que tiene una propiedad de barra de búsqueda) en el `ViewDidLoad` método de `MapViewController` :

```csharp
//Creates an instance of a custom View Controller that holds the results
var searchResultsController = new SearchResultsViewController (map);

//Creates a search controller updater
var searchUpdater = new SearchResultsUpdator ();
searchUpdater.UpdateSearchResults += searchResultsController.Search;

//add the search controller
searchController = new UISearchController (searchResultsController) {
                SearchResultsUpdater = searchUpdater
            };

//format the search bar
searchController.SearchBar.SizeToFit ();
searchController.SearchBar.SearchBarStyle = UISearchBarStyle.Minimal;
searchController.SearchBar.Placeholder = "Enter a search query";

//the search bar is contained in the navigation bar, so it should be visible
searchController.HidesNavigationBarDuringPresentation = false;

//Ensure the searchResultsController is presented in the current View Controller
DefinesPresentationContext = true;

//Set the search bar in the navigation bar
NavigationItem.TitleView = searchController.SearchBar;
```

Tenga en cuenta que usted es responsable de incorporar el objeto de barra de búsqueda en la interfaz de usuario. En este ejemplo, se asigna al TitleView de la barra de navegación, pero si no se usa un controlador de navegación en la aplicación, tendrá que buscar otro lugar para mostrarlo.

En este fragmento de código, creamos otro controlador de vista personalizado, `searchResultsController` que muestra los resultados de la búsqueda y, a continuación, usamos este objeto para crear nuestro objeto de controlador de búsqueda. También creamos un nuevo actualizador de búsqueda, que se activa cuando el usuario interactúa con la barra de búsqueda. Recibe notificaciones sobre las búsquedas con cada pulsación de tecla y es responsable de actualizar la interfaz de usuario.
Echaremos un vistazo a cómo implementar el `searchResultsController` y el `searchResultsUpdater` más adelante en esta guía.

Esto da como resultado una barra de búsqueda que se muestra sobre el mapa, como se muestra a continuación:

 ![Una barra de búsqueda que se muestra sobre el mapa](images/07-searchbar.png)

### <a name="displaying-the-search-results"></a>Mostrar los resultados de la búsqueda

Para mostrar los resultados de la búsqueda, es necesario crear un controlador de vista personalizado. normalmente es `UITableViewController` . Como se mostró anteriormente, `searchResultsController` se pasa al constructor de `searchController` cuando se crea.
El código siguiente es un ejemplo de cómo crear este controlador de vista personalizado:

```csharp
public class SearchResultsViewController : UITableViewController
{
    static readonly string mapItemCellId = "mapItemCellId";
    MKMapView map;

    public List<MKMapItem> MapItems { get; set; }

    public SearchResultsViewController (MKMapView map)
    {
        this.map = map;

        MapItems = new List<MKMapItem> ();
    }

    public override nint RowsInSection (UITableView tableView, nint section)
    {
        return MapItems.Count;
    }

    public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
    {
        var cell = tableView.DequeueReusableCell (mapItemCellId);

        if (cell == null)
            cell = new UITableViewCell ();

        cell.TextLabel.Text = MapItems [indexPath.Row].Name;
        return cell;
    }

    public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
    {
        // add item to map
        CLLocationCoordinate2D coord = MapItems [indexPath.Row].Placemark.Location.Coordinate;
        map.AddAnnotations (new MKPointAnnotation () {
            Title = MapItems [indexPath.Row].Name,
            Coordinate = coord
        });

        map.SetCenterCoordinate (coord, true);

        DismissViewController (false, null);
    }

    public void Search (string forSearchString)
    {
        // create search request
        var searchRequest = new MKLocalSearchRequest ();
        searchRequest.NaturalLanguageQuery = forSearchString;
        searchRequest.Region = new MKCoordinateRegion (map.UserLocation.Coordinate, new MKCoordinateSpan (0.25, 0.25));

        // perform search
        var localSearch = new MKLocalSearch (searchRequest);

        localSearch.Start (delegate (MKLocalSearchResponse response, NSError error) {
            if (response != null && error == null) {
                this.MapItems = response.MapItems.ToList ();
                this.TableView.ReloadData ();
            } else {
                Console.WriteLine ("local search error: {0}", error);
            }
        });

    }
}
```

### <a name="updating-the-search-results"></a>Actualizar los resultados de la búsqueda

`SearchResultsUpdater`Actúa como un mediador entre la `searchController` barra de búsqueda y los resultados de la búsqueda.

En este ejemplo, tenemos que crear primero el método de búsqueda en `SearchResultsViewController` . Para ello, se debe crear un `MKLocalSearch` objeto y usarlo para emitir una búsqueda de `MKLocalSearchRequest` , los resultados se recuperan en una devolución de llamada que se pasa al `Start` método del `MKLocalSearch` objeto. Los resultados se devuelven en un `MKLocalSearchResponse` objeto que contiene una matriz de `MKMapItem` objetos:

```csharp
public void Search (string forSearchString)
{
    // create search request
    var searchRequest = new MKLocalSearchRequest ();
    searchRequest.NaturalLanguageQuery = forSearchString;
    searchRequest.Region = new MKCoordinateRegion (map.UserLocation.Coordinate, new MKCoordinateSpan (0.25, 0.25));

    // perform search
    var localSearch = new MKLocalSearch (searchRequest);

    localSearch.Start (delegate (MKLocalSearchResponse response, NSError error) {
        if (response != null && error == null) {
            this.MapItems = response.MapItems.ToList ();
            this.TableView.ReloadData ();
        } else {
            Console.WriteLine ("local search error: {0}", error);
        }
    });

}
```

A continuación, en nuestro `MapViewController` vamos a crear una implementación personalizada de `UISearchResultsUpdating` , que se asigna a la `SearchResultsUpdater` propiedad de nuestro `searchController` en la sección adición de [una interfaz de usuario de búsqueda local](#Adding_a_Local_Search_UI) :

```csharp
public class SearchResultsUpdator : UISearchResultsUpdating
{
    public event Action<string> UpdateSearchResults = delegate {};

    public override void UpdateSearchResultsForSearchController (UISearchController searchController)
    {
        this.UpdateSearchResults (searchController.SearchBar.Text);
    }
}
```

La implementación anterior agrega una anotación a la asignación cuando se selecciona un elemento en los resultados, como se muestra a continuación:

 ![Anotación agregada a la asignación cuando se selecciona un elemento en los resultados.](images/08-search-results.png)

> [!IMPORTANT]
> `UISearchController`se implementó en iOS 8. Si desea admitir dispositivos anteriores a este, tendrá que usar `UISearchDisplayController` .

## <a name="summary"></a>Resumen

En este artículo se ha examinado el marco de trabajo del *Kit* de *mapas* para iOS. En primer lugar, se examinó cómo la `MKMapView` clase permite que las asignaciones interactivas se incluyan en una aplicación. Después, se demostró cómo personalizar aún más las asignaciones mediante anotaciones y superposiciones. Por último, examinó las capacidades de búsqueda local que se agregaron al kit de mapa con iOS 6,1, que muestra cómo usar las consultas basadas en ubicación para los puntos de interés y agregarlas a un mapa.

## <a name="related-links"></a>Vínculos relacionados

- [SearchController](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
- [MapDemo (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/mapdemo)
