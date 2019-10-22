---
title: Resumen del capítulo 28. Ubicación y asignaciones
description: 'Crear Mobile Apps con Xamarin. Forms: Resumen del capítulo 28. Ubicación y asignaciones'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F6E20077-687C-45C4-A375-31D4F49BBFA4
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 5dcd84536cc6d80deb753fc6fe57f9090f6b2dad
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697073"
---
# <a name="summary-of-chapter-28-location-and-maps"></a>Resumen del capítulo 28. Ubicación y asignaciones

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)

> [!NOTE]
> En las notas de esta página se indican las áreas en las que Xamarin. Forms ha diverge del material presentado en el libro.

Xamarin. Forms admite un elemento [`Map`](xref:Xamarin.Forms.Maps.Map) que se deriva de `View`. Debido a los requisitos de la plataforma especial implicados en el uso de asignaciones, se implementan en un ensamblado independiente, **Xamarin. Forms. Maps**, y implican un espacio de nombres diferente: `Xamarin.Forms.Maps`.

## <a name="the-geographic-coordinate-system"></a>Sistema de coordenadas geográfico

Un sistema de coordenadas geográfico identifica las posiciones en un objeto esférico (o casi esférico) como la tierra. Una coordenada consta de una *latitud* y una *longitud* expresadas en ángulos.

Un círculo excelente denominado `equator` está a la mitad del intervalo entre los dos polos a través de los cuales extiende conceptualmente el eje de la tierra.

### <a name="parallels-and-latitude"></a>Parallels y latitud

Un ángulo medido como el norte o el sur del Ecuador desde el centro de la tierra marca las líneas de la latitud igual conocidas como *paralelismos*. Van desde 0 grados en el Ecuador hasta 90 grados en los polos norte y sur. Por Convención, las latitudes del norte del Ecuador son valores positivos y los del Ecuador son valores negativos.

### <a name="longitude-and-meridians"></a>Longitud y meridianos

Las mitades de los círculos excelentes del polo norte al polo sur son líneas de la misma longitud, también conocidas como *meridianos*. Estos son relativos al meridiano de Greenwich de Greenwich, Inglaterra. Por Convención, las longitudes orientales del meridiano de Europa son valores positivos de 0 a 180 grados y las longitudes oeste del meridiano de Europa son valores negativos de 0 grados a &ndash;180 grados.

### <a name="the-equirectangular-projection"></a>Proyección equirectangular

Cualquier mapa plano de la tierra introduce distorsiones. Si todas las líneas de latitud y longitud son rectas y las diferencias en los ángulos de latitud y longitud se corresponden con las mismas distancias en el mapa, el resultado es una *proyección equirectangular*. Este mapa distorsiona las áreas más cerca de los polos porque se ajustan horizontalmente.

### <a name="the-mercator-projection"></a>Proyección Mercator

La popular *proyección de Mercator* intenta compensar la ampliación horizontal mediante la ampliación vertical de estas áreas verticalmente. Esto da como resultado un mapa en el que las áreas cercanas a los polos aparecen mucho más grandes de lo que son en realidad, pero cualquier área local se ajusta de forma bastante estrecha con el área real.

### <a name="map-services-and-tiles"></a>Asignar servicios e iconos

Los servicios de mapa usan una variación de la proyección Mercator denominada `Web Mercator`. Los servicios de mapa entregan iconos de mapa de bits a un cliente en función de la ubicación y el nivel de zoom.

## <a name="getting-the-users-location"></a>Obtener la ubicación del usuario

Las clases de `Map` de Xamarin. Forms no incluyen una instalación para obtener la ubicación geográfica del usuario, pero esto suele ser conveniente cuando se trabaja con mapas, por lo que un servicio de dependencia debe controlarla.

> [!NOTE]
> En su lugar, las aplicaciones de Xamarin. Forms pueden usar la clase [`Geolocation`](~/essentials/geolocation.md) incluida en Xamarin. Essentials.

### <a name="the-location-tracker-api"></a>La API de seguimiento de ubicación

La solución [**Xamarin. FormsBook. Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) contiene código para una API de seguimiento de ubicación. La estructura [`GeographicLocation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs) encapsula una latitud y una longitud. La interfaz [`ILocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs) define dos métodos para iniciar y pausar el seguimiento de la ubicación y un evento cuando hay una nueva ubicación disponible.

#### <a name="the-ios-location-manager"></a>Administrador de ubicación de iOS

La implementación de iOS de `ILocationTracker` es una clase [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs) que hace uso de la [`CLLocationManager`](xref:CoreLocation.CLLocationManager)de iOS.

#### <a name="the-android-location-manager"></a>El administrador de ubicación de Android

La implementación de Android de `ILocationTracker` es una clase [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs) que hace uso de la clase [`LocationManager`](xref:Android.Locations.LocationManager) de Android.

#### <a name="the-uwp-geo-locator"></a>Localizador geográfico de UWP

La implementación de Plataforma universal de Windows de `ILocationTracker` es una clase [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs) que hace uso de la [`Geolocator`](/uwp/api/Windows.Devices.Geolocation.Geolocator)de UWP.

### <a name="display-the-phones-location"></a>Mostrar la ubicación del teléfono

En el ejemplo [**WhereAmI**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI) se usa el seguimiento de ubicación para mostrar la ubicación del teléfono, tanto en el texto como en un mapa equirectangular.

### <a name="the-required-overhead"></a>La sobrecarga necesaria

Se requiere cierta sobrecarga para que **WhereAmI** use el seguimiento de la ubicación. En primer lugar, todos los proyectos de la solución **WhereAmI** deben tener referencias a los proyectos correspondientes de **Xamarin. FormsBook. Platform**y cada proyecto de **WhereAmI** debe llamar al método `Toolkit.Init`.

Se requiere una sobrecarga adicional específica de la plataforma, en forma de permisos de ubicación.

#### <a name="location-permission-for-ios"></a>Permiso de ubicación para iOS

En el caso de iOS, el archivo **info. plist** debe incluir elementos que contengan el texto de una pregunta que pida al usuario que permita obtener la ubicación de ese usuario.

#### <a name="location-permissions-for-android"></a>Permisos de ubicación para Android

Las aplicaciones de Android que obtienen la ubicación del usuario deben tener un permiso ACCESS_FILE_LOCATION en el archivo archivo AndroidManifest. Xml.

#### <a name="location-permissions-for-the-uwp"></a>Permisos de ubicación para UWP

Una aplicación Plataforma universal de Windows debe tener una funcionalidad de dispositivo `location` marcada en el archivo package. appxmanifest.

## <a name="working-with-xamarinformsmaps"></a>Trabajar con Xamarin. Forms. Maps

Hay varios requisitos implicados en el uso de la clase `Map`.

### <a name="the-nuget-package"></a>El paquete NuGet

La biblioteca de NuGet **Xamarin. Forms. Maps** debe agregarse a la solución de aplicación. El número de versión debe ser el mismo que el paquete de **Xamarin. Forms** instalado actualmente.

### <a name="initializing-the-maps-package"></a>Inicializar el paquete de asignaciones

Los proyectos de aplicación deben llamar al método `Xamarin.FormsMaps.Init` después de hacer una llamada a `Xamarin.Forms.Forms.Init`.

### <a name="enabling-map-services"></a>Habilitación de servicios de mapa

Dado que el `Map` puede obtener la ubicación del usuario, la aplicación debe obtener permiso para el usuario de la manera descrita anteriormente en este capítulo:

#### <a name="enabling-ios-maps"></a>Habilitación de mapas de iOS

Una aplicación de iOS que usa `Map` necesita dos líneas en el archivo info. plist.

#### <a name="enabling-android-maps"></a>Habilitación de mapas de Android

Se requiere una clave de autorización para usar los servicios de mapa de Google. Esta clave se inserta en el archivo **archivo AndroidManifest. XML** . Además, el archivo **archivo AndroidManifest. XML** requiere etiquetas `manifest` implicadas para obtener la ubicación del usuario.

#### <a name="enabling-uwp-maps"></a>Habilitación de mapas de UWP

Una aplicación Plataforma universal de Windows requiere una clave de autorización para el uso de mapas de Bing. Esta clave se pasa como argumento al método `Xamarin.FormsMaps.Init`. La aplicación también debe estar habilitada para los servicios de ubicación.

### <a name="the-unadorned-map"></a>Asignación no adornada

El ejemplo [**MapDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos) consta de un archivo [MapsDemoHomePage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml) y un archivo de código subyacente [MapsDemoHomePage.Xaml.CS](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs) que permite navegar a varios programas de demostración.

El archivo [BasicMapPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml) muestra cómo mostrar la vista [`Map`](xref:Xamarin.Forms.Maps.Map) . De forma predeterminada, muestra la ciudad de Roma, pero el usuario puede manipular la asignación.

Para deshabilitar el desplazamiento horizontal y vertical, establezca la propiedad [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) en `false`. Para deshabilitar el zoom, establezca [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) en `false`. Es posible que estas propiedades no funcionen en todas las plataformas.

### <a name="streets-and-terrain"></a>Calles y terreno

Puede mostrar diferentes tipos de mapas estableciendo la propiedad `Map` [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) de tipo [`MapType`](xref:Xamarin.Forms.Maps.MapType), una enumeración con tres miembros:

- [`Street`](xref:Xamarin.Forms.Maps.MapType.Street), el valor predeterminado
- [`Satellite`](xref:Xamarin.Forms.Maps.MapType.Satellite)
- [`Hybrid`](xref:Xamarin.Forms.Maps.MapType.Hybrid)

El archivo [MapTypesPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml) muestra cómo usar un botón de radio para seleccionar el tipo de mapa. Hace uso de la clase [`RadioButtonManager`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs) en la biblioteca de [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) y una clase basada en el archivo [MapTypeRadioButton. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml) .

### <a name="map-coordinates"></a>Coordenadas de mapa

Un programa puede obtener el área actual en la que se muestra el `Map` a través de la propiedad [`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion) . Esta propiedad *no* está respaldada por una propiedad enlazable y no hay ningún mecanismo de notificación para indicar cuándo ha cambiado, por lo que un programa que desee supervisar la propiedad probablemente debería usar un temporizador para ese propósito.

`VisibleRegion` es de tipo [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan), una clase con cuatro propiedades de solo lectura:

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center) de tipo [`Position`](xref:Xamarin.Forms.Maps.Position)
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees) de tipo `double`, que indica el alto del área mostrada del mapa.
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees) de tipo `double`, que indica el ancho del área mostrada del mapa.
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius) de tipo [`Distance`](xref:Xamarin.Forms.Maps.Distance), que indica el tamaño del área circular más grande visible en el mapa.

`Position` y `Distance` son estructuras. `Position` define dos propiedades de solo lectura establecidas mediante el [constructor `Position`](xref:Xamarin.Forms.Maps.Position.%23ctor(System.Double,System.Double)):

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)

`Distance` está diseñado para proporcionar una distancia independiente de la unidad mediante la conversión entre las unidades métricas y inglesas. Un valor `Distance` se puede crear de varias maneras:

- [`Distance` constructor](xref:Xamarin.Forms.Maps.Distance.%23ctor(System.Double)) con una distancia en metros
- [`Distance.FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters(System.Double)) método estático
- [`Distance.FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers(System.Double)) método estático
- [`Distance.FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles(System.Double)) método estático

El valor está disponible en tres propiedades:

- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters) de tipo `double`
- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers) de tipo `double`
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles) de tipo `double`

El archivo [MapCoordinatesPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml) contiene varios elementos `Label` para mostrar la información de `MapSpan`. El archivo de código subyacente [MapCoordinatesPage.Xaml.CS](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs) usa un temporizador para mantener la información actualizada cuando el usuario manipula la asignación.

### <a name="position-extensions"></a>Extensiones de posición

Una nueva biblioteca para este libro denominada [**Xamarin. FormsBook. Toolkit. Maps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps) contiene tipos específicos del mapa pero independientes de la plataforma. La clase [`PositionExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) tiene un método `ToString` para `Position` y un método para calcular la distancia entre dos valores de `Position`.

### <a name="setting-an-initial-location"></a>Establecer una ubicación inicial

Puede llamar al método [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)) de `Map` para establecer mediante programación una ubicación y un nivel de zoom en el mapa. El argumento es de tipo `MapSpan`. Puede crear un objeto de `MapSpan` con una de las siguientes opciones:

- [`MapSpan` constructor](xref:Xamarin.Forms.Maps.MapSpan.%23ctor(Xamarin.Forms.Maps.Position,System.Double,System.Double)) con un `Position` y un intervalo de latitud y longitud
- [`MapSpan.FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius(Xamarin.Forms.Maps.Position,Xamarin.Forms.Maps.Distance)) con un `Position` y RADIUS

También es posible crear una nueva `MapSpan` a partir de una existente usando los métodos [`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude(System.Double,System.Double)) o [`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom(System.Double)).

El archivo [WyomingPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml) y el archivo de código subyacente [WyomingPage.Xaml.CS](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs) muestran cómo usar el método `MoveToRegion` para mostrar el estado de Wyoming.

También puede utilizar el [constructor `Map`](xref:Xamarin.Forms.Maps.Map.%23ctor(Xamarin.Forms.Maps.MapSpan)) con un objeto `MapSpan` para inicializar la ubicación de la asignación. En el archivo [XamarinHQPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml) se muestra cómo hacerlo completamente en XAML para mostrar las oficinas centrales de Xamarin en San Francisco.

### <a name="dynamic-zooming"></a>Zoom dinámico

Puede usar un `Slider` para ampliar dinámicamente un mapa. El archivo [RadiusZoomPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml) y el archivo de código subyacente [RadiusZoomPage.Xaml.CS](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs) muestran cómo cambiar el radio de una asignación en función del valor de `Slider`.

El archivo [LongitudeZoomPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml) y el archivo de código subyacente [LongitudeZoomPage.Xaml.CS](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs) muestran un enfoque alternativo que funciona mejor en Android, pero ningún enfoque funciona bien en las plataformas de Windows.

### <a name="the-phones-location"></a>La ubicación del teléfono

La propiedad [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser) de `Map` funciona de forma ligeramente diferente en cada plataforma, como se muestra en el archivo [ShowLocationPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml) :

- En iOS, un punto azul indica la ubicación del teléfono, pero debe navegar manualmente
- En Android, se muestra un icono que cuando la inserción mueve el mapa a la ubicación del teléfono
- UWP es similar a iOS, pero a veces navega automáticamente a la ubicación

El proyecto **MapDemos** intenta imitar el enfoque de Android definiendo primero un botón basado en iconos basado en el archivo [MyLocationButton. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml) y el archivo de código subyacente [MyLocationButton.Xaml.CS](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs) .

El archivo [GoToLocationPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml) y el archivo de código subyacente [GoToLocationPage.Xaml.CS](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs) usan este botón para desplazarse a la ubicación del teléfono.

### <a name="pins-and-science-museums"></a>Clavijas y museos de ciencia

Por último, la clase `Map` define una propiedad [`Pins`](xref:Xamarin.Forms.Maps.Map.Pins) de tipo `IList<Pin>`. La clase [`Pin`](xref:Xamarin.Forms.Maps.Pin) define cuatro propiedades:

- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label) de tipo `string`, una propiedad necesaria
- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address) de tipo `string`, una dirección legible opcional.
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position) de tipo `Position`, que indica dónde se muestra el PIN en el mapa
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type) de tipo [`PinType`](xref:Xamarin.Forms.Maps.PinType), una enumeración, que no se utiliza

El proyecto **MapDemos** contiene el archivo [ScienceMuseums. XML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml), que enumera los museos de ciencia en el Estados Unidos, y las clases [`Locations`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs) y [`Site`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs) para deserializar estos datos.

El archivo [ScienceMuseumsPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml) y el archivo de código subyacente [ScienceMuseumsPage.Xaml.CS](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs) muestran PIN para estos museos de ciencia en el mapa. Cuando el usuario pulsa un PIN, muestra la dirección y un sitio web para el Museo.

### <a name="the-distance-between-two-points"></a>Distancia entre dos puntos

La clase [`PositionExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) contiene un método de [`DistanceTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88) con un cálculo simplificado de la distancia entre dos ubicaciones geográficas.

Se usa en el archivo [LocalMuseumsPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml) y en el archivo de código subyacente de [LocalMuseumsPage.Xaml.CS](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs) para mostrar también la distancia al Museo desde la ubicación del usuario:

[![Captura de pantalla triple de la página de museos locales](images/ch28fg28-small.png "Distancia a una ubicación")](images/ch28fg28-large.png#lightbox "Distancia a una ubicación")

El programa también muestra cómo restringir dinámicamente el número de clavijas en función de la ubicación del mapa.

## <a name="geocoding-and-back-again"></a>Geocodificación y viceversa

El ensamblado de [**Xamarin. Forms. Maps**](xref:Xamarin.Forms.Maps) también contiene una [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) clase con un método [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync(System.String)) que convierte una dirección de texto en cero o más posibles posiciones geográficas y otro método [`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync(Xamarin.Forms.Maps.Position)) que convierte en el otro. direcciona.

El archivo [GeocoderRoundTrip. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml) y el archivo de código subyacente [GeocoderRoundTrip.Xaml.CS](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs) demuestran esta utilidad.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 28 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [Ejemplos de capítulo 28](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [Mapa de Xamarin. Forms](~/xamarin-forms/user-interface/map/index.md)
