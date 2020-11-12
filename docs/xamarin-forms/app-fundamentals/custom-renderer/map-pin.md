---
title: Personalización de un anclado de mapa
description: En este artículo se explica cómo crear un representador personalizado para el control de mapa, que muestra un mapa nativo con una marca personalizada y una vista personalizada de los datos de marca en cada plataforma.
ms.prod: xamarin
ms.assetid: C5481D86-80E9-4E3D-9FB6-57B0F93711A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/06/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f4e0387b6b64c5a3bf801a31c6e333b6f514df1d
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93367376"
---
# <a name="customizing-a-map-pin"></a>Personalización de un anclado de mapa

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/customrenderers-map-pin)

_En este artículo se explica cómo crear un representador personalizado para el control de mapa, que muestra un mapa nativo con una marca personalizada y una vista personalizada de los datos de marca en cada plataforma._

Todas las vistas de Xamarin.Forms tienen un representador que las acompaña para cada plataforma y que crea una instancia de un control nativo. Cuando una aplicación de Xamarin.Forms representa un [`Map`](xref:Xamarin.Forms.Maps.Map) en iOS, se crea la instancia de la clase `MapRenderer`, que a su vez crea una instancia del control `MKMapView` nativo. En la plataforma de Android, la clase `MapRenderer` crea una instancia de un control `MapView` nativo. En la Plataforma universal de Windows (UWP), la clase `MapRenderer` crea una instancia de un elemento `MapControl` nativo. Para obtener más información sobre el representador y las clases de control nativo a las que se asignan los controles de Xamarin.Forms, vea [Clases base y controles nativos del representador](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

El siguiente diagrama muestra la relación entre la clase [`Map`](xref:Xamarin.Forms.Maps.Map) y los controles nativos correspondientes que la implementan:

![Relación entre el control de mapa y los controles nativos de implementación](map-pin-images/map-classes.png)

El proceso de representación puede usarse para implementar personalizaciones específicas de plataforma al crear un representador personalizado para una clase [`Map`](xref:Xamarin.Forms.Maps.Map) en cada plataforma. Para hacerlo, siga este procedimiento:

1. [Cree](#creating-the-custom-map) un mapa personalizado de Xamarin.Forms.
1. [Consuma](#consuming-the-custom-map) el mapa personalizado de Xamarin.Forms.
1. [Cree](#creating-the-custom-renderer-on-each-platform) el representador personalizado para el mapa en cada plataforma.

Se explicará cada elemento uno por uno, para implementar un representador `CustomMap` que muestra un mapa nativo con una marca personalizada y una vista personalizada de los datos de marcas en cada plataforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) tiene que inicializarse y configurarse antes de cada uso. Para obtener más información, vea [`Maps Control`](~/xamarin-forms/user-interface/map/index.md).

## <a name="creating-the-custom-map"></a>Crear un mapa personalizado

Se puede crear un control de mapa personalizado mediante la creación de subclases de la clase [`Map`](xref:Xamarin.Forms.Maps.Map), como se muestra en el siguiente ejemplo de código:

```csharp
public class CustomMap : Map
{
    public List<CustomPin> CustomPins { get; set; }
}
```

El control `CustomMap` se crea en el proyecto de biblioteca de .NET Standard y define la API para el mapa personalizado. El mapa personalizado expone la propiedad `CustomPins` que representa la colección de objetos `CustomPin` que va a representar el control de mapa nativo en cada plataforma. La clase `CustomPin` se muestra en el siguiente ejemplo de código:

```csharp
public class CustomPin : Pin
{
    public string Name { get; set; }
    public string Url { get; set; }
}
```

Esta clase define que un objeto `CustomPin` hereda las propiedades de la clase [`Pin`](xref:Xamarin.Forms.Maps.Pin) y agrega las propiedades `Name` y `Url`.

## <a name="consuming-the-custom-map"></a>Usar el mapa personalizado

En XAML puede hacerse referencia al control personalizado `CustomMap` en el proyecto de biblioteca de .NET Standard declarando un espacio de nombres para su ubicación y usando el prefijo del espacio de nombres en el control de mapa personalizado. El siguiente ejemplo de código muestra cómo el control personalizado `CustomMap` puede utilizarse en una página XAML:

```xaml
<ContentPage ...
                   xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer">
    <local:CustomMap x:Name="customMap"
                   MapType="Street" />
</ContentPage>

```

El prefijo del espacio de nombres `local` puede tener cualquier nombre. Empero, los valores deben `clr-namespace` y `assembly` coincidir con los detalles del mapa personalizado. Una vez que se declare el espacio de nombres, el prefijo se utiliza para hacer referencia al mapa personalizado.

El siguiente ejemplo de código muestra cómo el control personalizado `CustomMap` puede utilizarse en una página C#:

```csharp
public class MapPageCS : ContentPage
{
    public MapPageCS()
    {
        CustomMap customMap = new CustomMap
        {
            MapType = MapType.Street
        };
        // ...
        Content = customMap;
    }
}
```

La instancia `CustomMap` se usará para mostrar el mapa nativo en cada plataforma. La propiedad [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) establece el estilo de presentación del [`Map`](xref:Xamarin.Forms.Maps.Map) y los valores posibles que se definen en la enumeración [`MapType`](xref:Xamarin.Forms.Maps.MapType).

La ubicación del mapa y las marcas que contiene se inicializan como se muestra en el siguiente ejemplo de código:

```csharp
public MapPage()
{
    // ...
    CustomPin pin = new CustomPin
    {
        Type = PinType.Place,
        Position = new Position(37.79752, -122.40183),
        Label = "Xamarin San Francisco Office",
        Address = "394 Pacific Ave, San Francisco CA",
        Name = "Xamarin",
        Url = "http://xamarin.com/about/"
    };
    customMap.CustomPins = new List<CustomPin> { pin };
    customMap.Pins.Add(pin);
    customMap.MoveToRegion(MapSpan.FromCenterAndRadius(new Position(37.79752, -122.40183), Distance.FromMiles(1.0)));
}
```

Esta inicialización agrega una marca personalizada y coloca la vista del mapa con el método [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*), que cambia la posición y el nivel de zoom del mapa mediante la creación de un [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) desde un [`Position`](xref:Xamarin.Forms.Maps.Position) y un [`Distance`](xref:Xamarin.Forms.Maps.Distance).

Ahora se puede agregar un representador personalizado a cada proyecto de aplicación para personalizar los controles de mapa nativos.

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creación del representador personalizado en cada plataforma

El proceso de creación de la clase de representador personalizada es el siguiente:

1. Cree una subclase de la clase `MapRenderer` que represente el mapa personalizado.
1. Invalide el método `OnElementChanged` que representa el mapa personalizado y escriba una lógica para personalizarlo. Se llama a este método cuando se crea el mapa personalizado de Xamarin.Forms correspondiente.
1. Agregue un atributo `ExportRenderer` a la clase de representador personalizada para especificar que se usará para representar el mapa personalizado de Xamarin.Forms. Este atributo se usa para registrar al representador personalizado con Xamarin.Forms.

> [!NOTE]
> Proporcionar un representador personalizado en cada proyecto de la plataforma es un paso opcional. Si no se registra un representador personalizado, se usará el representador predeterminado de la clase base del control.

El siguiente diagrama muestra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con las relaciones entre ellos:

![Responsabilidades del proyecto de representador personalizado CustomMap](map-pin-images/solution-structure.png)

Las clases del representador específico de la plataforma, que se derivan de la clase `MapRenderer` para cada plataforma, representan el control `CustomMap`. Esto da como resultado que cada control `CustomMap` se represente con controles específicos de la plataforma, como se muestra en las siguientes capturas de pantalla:

![CustomMap en cada plataforma](map-pin-images/screenshots.png)

La clase `MapRenderer` expone el método `OnElementChanged`, al que se llama cuando se crea el mapa personalizado de Xamarin.Forms para representar el control nativo correspondiente. Este método toma un parámetro `ElementChangedEventArgs` que contiene propiedades `OldElement` y `NewElement`. Estas propiedades representan al elemento de Xamarin.Forms al que *estaba* asociado el representador y al elemento de Xamarin.Forms al que *está* asociado el representador, respectivamente. En la aplicación de ejemplo, la propiedad `OldElement` es `null` y la propiedad `NewElement` contiene una referencia a la instancia de `CustomMap`.

El lugar para realizar la personalización de controles nativos es una versión reemplazada del método `OnElementChanged` en cada clase de representador específica de la plataforma. Una referencia con tipo para el control nativo que se usa en la plataforma puede obtenerse a través de la propiedad `Control`. Además, mediante la propiedad Xamarin.Forms se puede obtener una referencia al control de `Element` que se representa.

Debe tener cuidado al suscribirse a los controladores de eventos en el método `OnElementChanged`, como se muestra en el siguiente ejemplo de código:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.View> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null)
  {
      // Unsubscribe from event handlers
  }

  if (e.NewElement != null)
  {
      // Configure the native control and subscribe to event handlers
  }
}
```

Solo se debe configurar el control nativo y suscribir a los controladores de eventos cuando se adjunta el representador personalizado a un nuevo elemento de Xamarin.Forms. De forma similar, solo se debe cancelar la suscripción de los controladores de eventos que se han suscrito cuando cambia el elemento al que está asociado el presentador. Adoptar este enfoque facilita crear un representador personalizado que no sufra pérdidas de memoria.

Cada clase de representador personalizado se decora con un atributo `ExportRenderer` que registra el representador con Xamarin.Forms. El atributo toma dos parámetros: el nombre de tipo del control personalizado de Xamarin.Forms que se representa y el nombre de tipo del representador personalizado. El prefijo `assembly` para el atributo especifica que el atributo se aplica a todo el ensamblado.

En las secciones siguientes se describe la implementación de cada clase de representador personalizado específico de plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Creación del representador personalizado en iOS

Las siguientes capturas de pantalla muestran el mapa antes y después de la personalización:

![Control de mapa antes y después de la personalización](map-pin-images/map-layout-ios.png)

En iOS la marca se denomina *anotación* y puede ser una imagen personalizada o una marca definida por el sistema de varios colores. Opcionalmente las anotaciones pueden mostrar una *llamada* , que se muestra en respuesta a que el usuario seleccione la anotación. La llamada muestra las propiedades `Label` y `Address` de la instancia `Pin`, con vistas adicionales a la derecha y a la izquierda. En la captura de pantalla anterior, la vista adicional izquierda es la imagen de un mono y la vista adicional derecha es el botón *Información*.

El siguiente ejemplo de código muestra el representador personalizado para la plataforma iOS:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        UIView customPinView;
        List<CustomPin> customPins;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null)
                {
                    nativeMap.RemoveAnnotations(nativeMap.Annotations);
                    nativeMap.GetViewForAnnotation = null;
                    nativeMap.CalloutAccessoryControlTapped -= OnCalloutAccessoryControlTapped;
                    nativeMap.DidSelectAnnotationView -= OnDidSelectAnnotationView;
                    nativeMap.DidDeselectAnnotationView -= OnDidDeselectAnnotationView;
                }
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                customPins = formsMap.CustomPins;

                nativeMap.GetViewForAnnotation = GetViewForAnnotation;
                nativeMap.CalloutAccessoryControlTapped += OnCalloutAccessoryControlTapped;
                nativeMap.DidSelectAnnotationView += OnDidSelectAnnotationView;
                nativeMap.DidDeselectAnnotationView += OnDidDeselectAnnotationView;
            }
        }
        // ...
    }
}
```

El método `OnElementChanged` realiza la siguiente configuración de la instancia [`MKMapView`](xref:MapKit.MKMapView), siempre que se adjunte el presentador personalizado a un nuevo elemento de Xamarin.Forms:

- La propiedad [`GetViewForAnnotation`](xref:MapKit.MKMapView.GetViewForAnnotation*) se establece en el método `GetViewForAnnotation`. Se llama a este método cuando la ubicación de la anotación [ se vuelve visible en el mapa ](#displaying-the-annotation) y se usa para personalizar la anotación antes de mostrarla.
- Los controladores de eventos para los eventos `CalloutAccessoryControlTapped`, `DidSelectAnnotationView` y `DidDeselectAnnotationView` se registran. Estos eventos se activan cuando el usuario [pulsa el accesorio derecho de la llamada](#tapping-on-the-right-callout-accessory-view) y cuando el usuario [selecciona](#selecting-the-annotation) y [anula la selección de](#deselecting-the-annotation) la anotación, respectivamente. Se cancela la suscripción de los eventos solo cuando cambia el representador al que está adjunto el elemento.

#### <a name="displaying-the-annotation"></a>Mostrar la anotación

Se llama al método `GetViewForAnnotation` cuando la ubicación de la anotación se vuelve visible en el mapa y se usa para personalizar la anotación antes de mostrarla. Una anotación tiene dos partes:

- `MkAnnotation`: incluye el título, el subtítulo y la ubicación de la anotación.
- `MkAnnotationView`: contiene la imagen para representar la anotación y, opcionalmente, una llamada que se muestra cuando el usuario pulsa la anotación.

El método `GetViewForAnnotation` acepta un `IMKAnnotation` que contiene los datos de la anotación y devuelve un `MKAnnotationView` para su presentación en el mapa. Se muestra en el siguiente ejemplo de código:

```csharp
protected override MKAnnotationView GetViewForAnnotation(MKMapView mapView, IMKAnnotation annotation)
{
    MKAnnotationView annotationView = null;

    if (annotation is MKUserLocation)
        return null;

    var customPin = GetCustomPin(annotation as MKPointAnnotation);
    if (customPin == null)
    {
        throw new Exception("Custom pin not found");
    }

    annotationView = mapView.DequeueReusableAnnotation(customPin.Name);
    if (annotationView == null)
    {
        annotationView = new CustomMKAnnotationView(annotation, customPin.Name);
        annotationView.Image = UIImage.FromFile("pin.png");
        annotationView.CalloutOffset = new CGPoint(0, 0);
        annotationView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile("monkey.png"));
        annotationView.RightCalloutAccessoryView = UIButton.FromType(UIButtonType.DetailDisclosure);
        ((CustomMKAnnotationView)annotationView).Name = customPin.Name;
        ((CustomMKAnnotationView)annotationView).Url = customPin.Url;
    }
    annotationView.CanShowCallout = true;

    return annotationView;
}
```

Este método garantiza que la anotación se muestre como una imagen personalizada, en lugar de como una marca definida por el sistema y que, cuando se pulsa la anotación, se muestre una llamada que incluye contenido adicional a la izquierda y a la derecha del título y la dirección de la anotación. Esto se logra de la siguiente manera:

1. Se llama al método `GetCustomPin` para devolver los datos de marca personalizada para la anotación.
1. Para ahorrar memoria, la vista de la anotación se agrupa para volver a usarla con la llamada a [`DequeueReusableAnnotation`](xref:MapKit.MKMapView.DequeueReusableAnnotation*).
1. La clase `CustomMKAnnotationView` extiende la clase `MKAnnotationView` con las propiedades `Name` y `Url` que corresponden a las propiedades idénticas en la instancia `CustomPin`. Se crea una nueva instancia de la `CustomMKAnnotationView`, siempre que la anotación sea `null`:
    - La propiedad `CustomMKAnnotationView.Image` se establece en la imagen que representará la anotación en el mapa.
    - La propiedad `CustomMKAnnotationView.CalloutOffset` se establece en un `CGPoint` que especifica que la llamada se centrará por encima de la anotación.
    - La propiedad `CustomMKAnnotationView.LeftCalloutAccessoryView` se establece en una imagen de un mono que aparecerá a la izquierda del título y la dirección de la anotación.
    - La propiedad `CustomMKAnnotationView.RightCalloutAccessoryView` se establece en un botón *Información* que aparecerá a la derecha del título y la dirección de la anotación.
    - La propiedad `CustomMKAnnotationView.Name` se establece en la propiedad `CustomPin.Name` devuelta por el método `GetCustomPin`. Esto permite que la anotación pueda identificarse de forma que su [llamada pueda personalizarse aún más](#selecting-the-annotation) si así lo desea.
    - La propiedad `CustomMKAnnotationView.Url` se establece en la propiedad `CustomPin.Url` devuelta por el método `GetCustomPin`. La dirección URL se abrirá cuando el usuario [pulse el botón que se muestra en la vista de accesorios de llamada correcta](#tapping-on-the-right-callout-accessory-view).
1. La propiedad [`MKAnnotationView.CanShowCallout`](xref:MapKit.MKAnnotationView.CanShowCallout*) se establece en `true` para que se muestre la llamada cuando se pulsa la anotación.
1. La anotación se devuelve para su visualización en el mapa.

#### <a name="selecting-the-annotation"></a>Seleccionar la anotación

Cuando el usuario pulsa en la anotación, se desencadena el evento `DidSelectAnnotationView`, que a su vez ejecuta el método `OnDidSelectAnnotationView`:

```csharp
void OnDidSelectAnnotationView(object sender, MKAnnotationViewEventArgs e)
{
    CustomMKAnnotationView customView = e.View as CustomMKAnnotationView;
    customPinView = new UIView();

    if (customView.Name.Equals("Xamarin"))
    {
        customPinView.Frame = new CGRect(0, 0, 200, 84);
        var image = new UIImageView(new CGRect(0, 0, 200, 84));
        image.Image = UIImage.FromFile("xamarin.png");
        customPinView.AddSubview(image);
        customPinView.Center = new CGPoint(0, -(e.View.Frame.Height + 75));
        e.View.AddSubview(customPinView);
    }
}
```

Este método extiende la llamada existente (que contiene las vistas adicionales izquierdas y derechas) mediante la adición de una instancia de `UIView` a ella que contiene una imagen del logotipo de Xamarin, siempre que la anotación seleccionada tenga su propiedad `Name` establecida en `Xamarin`. Esto permite escenarios donde se pueden mostrar llamadas diferentes para distintas anotaciones. La instancia `UIView` se mostrará centrada por encima de la llamada existente.

#### <a name="tapping-on-the-right-callout-accessory-view"></a>Pulsar en la vista adicional de llamada derecha

Cuando el usuario pulsa el botón *Información* en la vista adicional de llamada derecha, se desencadena el evento `CalloutAccessoryControlTapped`, que a su vez ejecuta el método `OnCalloutAccessoryControlTapped`:

```csharp
void OnCalloutAccessoryControlTapped(object sender, MKMapViewAccessoryTappedEventArgs e)
{
    CustomMKAnnotationView customView = e.View as CustomMKAnnotationView;
    if (!string.IsNullOrWhiteSpace(customView.Url))
    {
        UIApplication.SharedApplication.OpenUrl(new Foundation.NSUrl(customView.Url));
    }
}
```

Este método abre un explorador web y navega a la dirección almacenada en la propiedad `CustomMKAnnotationView.Url`. Tenga en cuenta que la dirección se definió al crear la colección `CustomPin` en el proyecto de biblioteca de .NET Standard.

#### <a name="deselecting-the-annotation"></a>Anule la selección de la anotación

Cuando la anotación se muestra y el usuario pulsa en el mapa, se desencadena el evento `DidDeselectAnnotationView`, que a su vez ejecuta el método `OnDidDeselectAnnotationView`:

```csharp
void OnDidDeselectAnnotationView(object sender, MKAnnotationViewEventArgs e)
{
    if (!e.View.Selected)
    {
        customPinView.RemoveFromSuperview();
        customPinView.Dispose();
        customPinView = null;
    }
}
```

Este método garantiza que cuando la llamada existente no está seleccionada, la parte extendida de la llamada (la imagen del logotipo de Xamarin) también dejará de mostrarse y se liberarán sus recursos.

Para obtener más información sobre cómo personalizar una instancia de `MKMapView`, vea [Maps in Xamarin.iOS](~/ios/user-interface/controls/ios-maps/index.md) (Mapas en Xamarin.iOS).

### <a name="creating-the-custom-renderer-on-android"></a>Creación del representador personalizado en Android

Las siguientes capturas de pantalla muestran el mapa antes y después de la personalización:

![Control de mapa antes y después de la personalización](map-pin-images/map-layout-android.png)

En Android la marca se denomina *marcador* y puede ser una imagen personalizada o un marcador definido por el sistema de varios colores. Los marcadores pueden mostrar una *ventana de información* , que se muestra en la respuesta para el usuario que pulsa en el marcador. Muestra la ventana de información de las propiedades `Label` y `Address` de la instancia `Pin` y se pueden personalizar para incluir otro tipo de contenido. Con todo, solo una ventana de información puede mostrarse al mismo tiempo.

En el ejemplo de código siguiente se muestra el representador personalizado para la plataforma Android:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.Droid
{
    public class CustomMapRenderer : MapRenderer, GoogleMap.IInfoWindowAdapter
    {
        List<CustomPin> customPins;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                NativeMap.InfoWindowClick -= OnInfoWindowClick;
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                customPins = formsMap.CustomPins;
            }
        }

        protected override void OnMapReady(GoogleMap map)
        {
            base.OnMapReady(map);

            NativeMap.InfoWindowClick += OnInfoWindowClick;
            NativeMap.SetInfoWindowAdapter(this);
        }
        ...
    }
}
```

Siempre que el representador personalizado esté asociado a un nuevo elemento de Xamarin.Forms, el método `OnElementChanged` recupera la lista de marcas personalizadas del control. Una vez que la instancia `GoogleMap` esté disponible, se invocará la invalidación `OnMapReady`. Este método registra un controlador de eventos para el evento `InfoWindowClick`, que se desencadena cuando [se hace clic en la ventana de información](#clicking-on-the-info-window) y cuya suscripción solo se cancela cuando cambia el elemento al que está adjunto el representador. La invalidación `OnMapReady` también llama al método `SetInfoWindowAdapter` para especificar que la instancia de la clase `CustomMapRenderer` proporcionará los métodos para personalizar la ventana de información.

La clase `CustomMapRenderer` implementa la interfaz `GoogleMap.IInfoWindowAdapter` para [personalizar la ventana de información](#customizing-the-info-window). Esta interfaz especifica que se deben implementar los siguientes métodos:

- `public Android.Views.View GetInfoWindow(Marker marker)`: se llama a este método para devolver una ventana de información personalizada para un marcador. Si se devuelve `null`, se usará la representación de la ventana predeterminada. Si se devuelve un `View`, `View` se colocará dentro del marco de la ventana de información.
- `public Android.Views.View GetInfoContents(Marker marker)`: se llama a este método para devolver un `View` que contiene el contenido de la ventana de información, y solo se llamará si el método `GetInfoWindow` devuelve `null`. Si devuelve `null`, se usará la representación predeterminada del contenido de la ventana de información.

En la aplicación de ejemplo, solo se personaliza el contenido de la ventana de información, de forma que el método `GetInfoWindow` devuelve `null` para habilitar esto.

#### <a name="customizing-the-marker"></a>Personalización del marcador

El icono utilizado para representar un marcador puede personalizarse mediante una llamada al método `MarkerOptions.SetIcon`. Esto puede realizarse invalidando el método `CreateMarker`, que se invoca para cada `Pin` que se agrega al mapa:

```csharp
protected override MarkerOptions CreateMarker(Pin pin)
{
    var marker = new MarkerOptions();
    marker.SetPosition(new LatLng(pin.Position.Latitude, pin.Position.Longitude));
    marker.SetTitle(pin.Label);
    marker.SetSnippet(pin.Address);
    marker.SetIcon(BitmapDescriptorFactory.FromResource(Resource.Drawable.pin));
    return marker;
}
```

Este método crea una nueva instancia de `MarkerOption` para cada instancia de `Pin`. Después de establecer la posición, la etiqueta y la dirección del marcador, su icono se establece con el método `SetIcon`. Este método toma un objeto `BitmapDescriptor` que contiene los datos necesarios para representar el icono y la clase `BitmapDescriptorFactory` proporciona métodos auxiliares para simplificar la creación de la `BitmapDescriptor`. Para obtener más información sobre el uso de la clase `BitmapDescriptorFactory` para personalizar un marcador, vea [Customizing a Marker](~/android/platform/maps-and-location/maps/maps-api.md) (Personalización de un marcador).

> [!NOTE]
> Si es necesario, el método `GetMarkerForPin` se puede invocar en el representador de mapa para recuperar un `Marker` de una `Pin`.

#### <a name="customizing-the-info-window"></a>Personalización de la ventana de información

Cuando un usuario pulsa en el marcador, el método `GetInfoContents` se ejecuta, siempre que el método `GetInfoWindow` devuelva `null`. El siguiente ejemplo de código muestra el método `GetInfoContents`:

```csharp
public Android.Views.View GetInfoContents(Marker marker)
{
    var inflater = Android.App.Application.Context.GetSystemService(Context.LayoutInflaterService) as Android.Views.LayoutInflater;
    if (inflater != null)
    {
        Android.Views.View view;

        var customPin = GetCustomPin(marker);
        if (customPin == null)
        {
            throw new Exception("Custom pin not found");
        }

        if (customPin.Name.Equals("Xamarin"))
        {
            view = inflater.Inflate(Resource.Layout.XamarinMapInfoWindow, null);
        }
        else
        {
            view = inflater.Inflate(Resource.Layout.MapInfoWindow, null);
        }

        var infoTitle = view.FindViewById<TextView>(Resource.Id.InfoWindowTitle);
        var infoSubtitle = view.FindViewById<TextView>(Resource.Id.InfoWindowSubtitle);

        if (infoTitle != null)
        {
            infoTitle.Text = marker.Title;
        }
        if (infoSubtitle != null)
        {
            infoSubtitle.Text = marker.Snippet;
        }

        return view;
    }
    return null;
}
```

Este método devuelve un `View` con el contenido de la ventana de información. Esto se logra de la siguiente manera:

- Se recupera una instancia de `LayoutInflater`. Esta se usa para crear una instancia de un archivo XML de diseño en su `View` correspondiente.
- Se llama al método `GetCustomPin` para devolver los datos de marca personalizada para la ventana de información.
- Se aumenta el diseño de `XamarinMapInfoWindow` si la propiedad `CustomPin.Name` es igual a `Xamarin`. En caso contrario, se aumenta el diseño de `MapInfoWindow`. Esto permite escenarios donde se pueden mostrar diferentes diseños de ventana de información para distintos marcadores.
- Se recuperan los recursos `InfoWindowTitle` y `InfoWindowSubtitle` desde el diseño aumentado y sus propiedades `Text` se establecen en los datos correspondientes de la instancia de `Marker`, siempre que los recursos no sean `null`.
- La instancia de `View` se devuelve para su visualización en el mapa.

> [!NOTE]
> Una ventana de información no es una `View` dinámica. En su lugar, Android convertirá la `View` a mapa de bits estático y la mostrará como una imagen. Esto significa que, mientras que una ventana de información puede responder a un evento de clic, no puede responder a los eventos de toque o gestos, y los controles individuales en la ventana de información no pueden responder a sus propios eventos de clic.

#### <a name="clicking-on-the-info-window"></a>Hacer clic en la ventana de información

Cuando el usuario hace clic en la ventana de información, se desencadena el evento `InfoWindowClick`, que a su vez ejecuta el método `OnInfoWindowClick`:

```csharp
void OnInfoWindowClick(object sender, GoogleMap.InfoWindowClickEventArgs e)
{
    var customPin = GetCustomPin(e.Marker);
    if (customPin == null)
    {
        throw new Exception("Custom pin not found");
    }

    if (!string.IsNullOrWhiteSpace(customPin.Url))
    {
        var url = Android.Net.Uri.Parse(customPin.Url);
        var intent = new Intent(Intent.ActionView, url);
        intent.AddFlags(ActivityFlags.NewTask);
        Android.App.Application.Context.StartActivity(intent);
    }
}
```

Este método abre un explorador web y navega a la dirección almacenada en la propiedad `Url` de la instancia `CustomPin` recuperada para el `Marker`. Tenga en cuenta que la dirección se definió al crear la colección `CustomPin` en el proyecto de biblioteca de .NET Standard.

Para obtener más información sobre cómo personalizar una instancia de `MapView`, vea [Using the Google Maps API in your application](~/android/platform/maps-and-location/maps/maps-api.md) (Uso de la API de Google Maps en su aplicación).

### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Creación del representador personalizado en la Plataforma universal de Windows

Las siguientes capturas de pantalla muestran el mapa antes y después de la personalización:

![Control de mapa antes y después de la personalización](map-pin-images/map-layout-uwp.png)

En la UWP la marca se denomina *icono de mapa* y puede ser una imagen personalizada o la imagen predeterminada definida por el sistema. Un icono de mapa puede mostrar un `UserControl`, que se muestra en la respuesta para el usuario que pulsa en el icono de mapa. El `UserControl` puede mostrar cualquier contenido, incluyendo las propiedades `Label` y `Address` de la instancia `Pin`.

El siguiente ejemplo de código muestra el representador personalizado de UWP:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
        MapControl nativeMap;
        List<CustomPin> customPins;
        XamarinMapOverlay mapOverlay;
        bool xamarinOverlayShown = false;

        protected override void OnElementChanged(ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                nativeMap.MapElementClick -= OnMapElementClick;
                nativeMap.Children.Clear();
                mapOverlay = null;
                nativeMap = null;
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                nativeMap = Control as MapControl;
                customPins = formsMap.CustomPins;

                nativeMap.Children.Clear();
                nativeMap.MapElementClick += OnMapElementClick;

                foreach (var pin in customPins)
                {
                    var snPosition = new BasicGeoposition { Latitude = pin.Pin.Position.Latitude, Longitude = pin.Pin.Position.Longitude };
                    var snPoint = new Geopoint(snPosition);

                    var mapIcon = new MapIcon();
                    mapIcon.Image = RandomAccessStreamReference.CreateFromUri(new Uri("ms-appx:///pin.png"));
                    mapIcon.CollisionBehaviorDesired = MapElementCollisionBehavior.RemainVisible;
                    mapIcon.Location = snPoint;
                    mapIcon.NormalizedAnchorPoint = new Windows.Foundation.Point(0.5, 1.0);

                    nativeMap.MapElements.Add(mapIcon);                    
                }
            }
        }
        ...
    }
}
```

El método `OnElementChanged` realiza las siguientes operaciones, siempre que se adjunte el presentador personalizado a un nuevo elemento de Xamarin.Forms:

- Borra la colección de `MapControl.Children` para quitar los elementos de interfaz de usuario existentes del mapa y después registra un controlador de eventos para el evento de `MapElementClick`. Este evento se desencadena cuando el usuario pulsa o hace clic en un `MapElement` en el `MapControl` y solo se cancela su suscripción cuando cambia el elemento al que está adjunto el representador.
- Cada marca en la colección de `customPins` se muestra en la ubicación geográfica correcta en el mapa como sigue:
  - La ubicación para la marca se crea como una instancia de `Geopoint`.
  - Una instancia de `MapIcon` se crea para representar la marca.
  - La imagen utilizada para representar el `MapIcon` se especifica estableciendo la propiedad `MapIcon.Image`. Con todo, no siempre se puede garantizar que se muestre la imagen del icono de mapa, ya que puede estar ocultada por otros elementos del mapa. Por lo tanto, la propiedad `CollisionBehaviorDesired` del icono del mapa se establece en `MapElementCollisionBehavior.RemainVisible`, para asegurarse de que está visible.
  - La ubicación del `MapIcon` se especifica configurando la propiedad `MapIcon.Location`.
  - La propiedad `MapIcon.NormalizedAnchorPoint` se establece en la ubicación aproximada del puntero en la imagen. Si esta propiedad conserva su valor predeterminado de (0,0), que representa la esquina superior izquierda de la imagen, los cambios en el nivel de zoom del mapa pueden dar lugar a que la imagen apunte a una ubicación distinta.
  - La instancia `MapIcon` se agrega a la colección `MapControl.MapElements`. Esto da como resultado que el icono de mapa se muestre en el `MapControl`.

> [!NOTE]
> Cuando se usa la misma imagen para varios iconos de mapa, la instancia de `RandomAccessStreamReference` debe declararse en el nivel de página o aplicación para mejorar el rendimiento.

#### <a name="displaying-the-usercontrol"></a>Mostrar el UserControl

El método `OnMapElementClick` se ejecuta cuando un usuario pulsa en el icono de mapa. El siguiente ejemplo de código muestra este método:

```csharp
private void OnMapElementClick(MapControl sender, MapElementClickEventArgs args)
{
    var mapIcon = args.MapElements.FirstOrDefault(x => x is MapIcon) as MapIcon;
    if (mapIcon != null)
    {
        if (!xamarinOverlayShown)
        {
            var customPin = GetCustomPin(mapIcon.Location.Position);
            if (customPin == null)
            {
                throw new Exception("Custom pin not found");
            }

            if (customPin.Name.Equals("Xamarin"))
            {
                if (mapOverlay == null)
                {
                    mapOverlay = new XamarinMapOverlay(customPin);
                }

                var snPosition = new BasicGeoposition { Latitude = customPin.Position.Latitude, Longitude = customPin.Position.Longitude };
                var snPoint = new Geopoint(snPosition);

                nativeMap.Children.Add(mapOverlay);
                MapControl.SetLocation(mapOverlay, snPoint);
                MapControl.SetNormalizedAnchorPoint(mapOverlay, new Windows.Foundation.Point(0.5, 1.0));
                xamarinOverlayShown = true;
            }
        }
        else
        {
            nativeMap.Children.Remove(mapOverlay);
            xamarinOverlayShown = false;
        }
    }
}
```

Este método crea una instancia de `UserControl` que muestra información sobre la marca. Esto se logra de la siguiente manera:

- Se recupera la instancia de `MapIcon`.
- Se llama al método `GetCustomPin` para devolver los datos de marca personalizada que se mostrarán.
- Se crea una instancia de `XamarinMapOverlay` para mostrar los datos de marca personalizada. Esta clase es un control de usuario.
- Se crea la ubicación geográfica en la que se mostrará la instancia de `XamarinMapOverlay` en el `MapControl` como una instancia de `Geopoint`.
- La instancia `XamarinMapOverlay` se agrega a la colección `MapControl.Children`. Esta colección contiene elementos de interfaz de usuario de XAML que se mostrarán en el mapa.
- La ubicación geográfica de la instancia de `XamarinMapOverlay` en el mapa se establece mediante una llamada al método `SetLocation`.
- La ubicación relativa de la instancia de `XamarinMapOverlay` que corresponde a la ubicación especificada se establece mediante una llamada al método `SetNormalizedAnchorPoint`. Esto garantiza que los cambios en el nivel de zoom del mapa tendrán como resultado que la instancia de `XamarinMapOverlay` siempre se muestre en la ubicación correcta.

Como alternativa, si ya se muestra información sobre la marca en el mapa, pulsar en el mapa quita la instancia de `XamarinMapOverlay` de la colección de `MapControl.Children`.

#### <a name="tapping-on-the-information-button"></a>Pulsar en el botón Información

Cuando el usuario pulsa el botón *Información* en el control de usuario de `XamarinMapOverlay`, se desencadena el evento `Tapped`, que a su vez ejecuta el método `OnInfoButtonTapped`:

```csharp
private async void OnInfoButtonTapped(object sender, TappedRoutedEventArgs e)
{
    await Launcher.LaunchUriAsync(new Uri(customPin.Url));
}
```

Este método abre un explorador web y navega a la dirección almacenada en la propiedad `Url` de la instancia de `CustomPin`. Tenga en cuenta que la dirección se definió al crear la colección `CustomPin` en el proyecto de biblioteca de .NET Standard.

Para obtener más información sobre cómo personalizar una instancia de `MapControl`, vea [Introducción a ubicación y mapas](/windows/uwp/maps-and-location/) en MSDN.

## <a name="related-links"></a>Vínculos relacionados

- [Xamarin.Forms Map](~/xamarin-forms/user-interface/map/index.md) (Mapa de Xamarin.Forms)
- [Maps in Xamarin.iOS](~/ios/user-interface/controls/ios-maps/index.md) (Mapas en Xamarin.iOS)
- [API de Maps](~/android/platform/maps-and-location/maps/maps-api.md)
- [Customized Pin (sample)](/samples/xamarin/xamarin-forms-samples/customrenderers-map-pin) (Marca personalizada [ejemplo])