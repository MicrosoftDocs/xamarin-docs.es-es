---
title: Resumen del capítulo 28. Ubicación y mapas
description: 'Crear Mobile Apps con Xamarin. Forms: Resumen del capítulo 28. Ubicación y mapas'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F6E20077-687C-45C4-A375-31D4F49BBFA4
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 8c0b60a5064bbcf605f66a7b9dcce5da1f84a015
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770891"
---
# <a name="summary-of-chapter-28-location-and-maps"></a>Resumen del capítulo 28. Ubicación y mapas

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)

> [!NOTE]
> Notas de esta página indican áreas donde se ha dividido Xamarin.Forms desde el material presentado en el libro.

Xamarin.Forms es compatible con un [ `Map` ](xref:Xamarin.Forms.Maps.Map) elemento que se deriva de `View`. Debido a los requisitos de plataforma especial que implica el uso de mapas, se implementan en un ensamblado independiente, **xamarin.Forms.Maps para**e implicar a otro espacio de nombres: `Xamarin.Forms.Maps`.

## <a name="the-geographic-coordinate-system"></a>El sistema de coordenadas geográfico

Un sistema de coordenadas geográfico identifica las posiciones en un objeto Esférico (o casi esférica) como la tierra. Una coordenada consta de un *latitud* y *longitud* expresado en ángulos.

Llama un círculo el `equator` está comprendido entre los dos polos a través del cual se extiende conceptualmente eje de la tierra.

### <a name="parallels-and-latitude"></a>Parallels y latitude

Ángulo medido Norte o al sur del Ecuador desde el centro de las marcas de tierra líneas de latitud igual conocido como *parallels*. Estas van de 0 grados en el Ecuador a 90 grados en el norte y sur polos. Por convención, las latitudes al norte del Ecuador son los valores positivos y al sur del Ecuador son valores negativos.

### <a name="longitude-and-meridians"></a>Latitud y meridianos

Mitades de círculos great desde el Polo Norte al Polo Sur son las líneas de igual longitud, también conocido como *meridianos*. Estos son relativos del meridiano de Greenwich, Inglaterra. Por convención, longitudes al este del meridiano son valores positivos de 0 grados a 180 grados y longitudes oeste del meridiano son valores negativos desde 0 grados a &ndash;180 grados.

### <a name="the-equirectangular-projection"></a>La proyección equirectangular

Cualquier mapa de la tierra plana presenta las distorsiones. Si todas las líneas de latitud y longitud son rectas y, si las diferencias iguales en ángulos de latitud y longitud corresponden a distancias iguales en el mapa, el resultado es un *proyección equirectangular*. Esta asignación distorsiona áreas más cercanos a los polos porque se estiran horizontalmente.

### <a name="the-mercator-projection"></a>La proyección de Mercator

El popular *proyección Mercator* intenta compensar la ampliación horizontal ajustándolo también estas áreas verticalmente. Esto da como resultado un mapa donde aparecen mucho mayores que son realmente, pero cualquier área local se ajusta de manera bastante estrecha con el área real áreas próximas a los polos.

### <a name="map-services-and-tiles"></a>Servicios de asignación y los iconos

Servicios de mapa usan una variación de la proyección de Mercator llamada `Web Mercator`. Los servicios de mapa entregar los iconos de mapa de bits a un cliente basado en el nivel de zoom y la ubicación.

## <a name="getting-the-users-location"></a>Introducción a la ubicación del usuario

Xamarin.Forms `Map` clases no incluyen una utilidad para obtener la ubicación geográfica del usuario, pero a menudo es deseable cuando se trabaja con mapas, así que un servicio de dependencia debe controlar.

> [!NOTE]
> Las aplicaciones de Xamarin.Forms en su lugar, pueden usar el [ `Geolocation` ](~/essentials/geolocation.md) clase incluida en Xamarin.Essentials.

### <a name="the-location-tracker-api"></a>La API de seguimiento de ubicación

El [ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) solución contiene código para una API de seguimiento de ubicación. El [ `GeographicLocation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs) estructura encapsula una latitud y longitud. El [ `ILocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs) interfaz define dos métodos para iniciar o pausar el seguimiento de ubicación y un evento cuando está disponible una nueva ubicación.

#### <a name="the-ios-location-manager"></a>El Administrador de la ubicación de iOS

La implementación de iOS de `ILocationTracker` es un [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs) clase que hace uso de iOS [ `CLLocationManager` ](xref:CoreLocation.CLLocationManager).

#### <a name="the-android-location-manager"></a>El Administrador de la ubicación de Android

La implementación de Android `ILocationTracker` es un [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs) clase que hace uso de Android [ `LocationManager` ](xref:Android.Locations.LocationManager) clase.

#### <a name="the-uwp-geo-locator"></a>El localizador UWP geográfica

La implementación de la plataforma Universal de Windows de `ILocationTracker` es un [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs) clase que hace uso de la UWP [ `Geolocator` ](/uwp/api/Windows.Devices.Geolocation.Geolocator).

### <a name="display-the-phones-location"></a>Mostrar la ubicación del teléfono

El [ **WhereAmI** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI) ejemplo usa la herramienta de seguimiento de ubicación para mostrar la ubicación del teléfono tanto en el texto en un mapa equirectangular.

### <a name="the-required-overhead"></a>El requiere sobrecarga

Es necesaria para la sobrecarga **WhereAmI** para usar la herramienta de seguimiento de ubicación. En primer lugar, todos los proyectos en el **WhereAmI** solución debe tener referencias a los proyectos correspondientes en **Xamarin.FormsBook.Platform**y cada **WhereAmI** proyecto debe llamar a la `Toolkit.Init` método.

Se requiere una sobrecarga adicional de específicos de la plataforma, en forma de los permisos de ubicación.

#### <a name="location-permission-for-ios"></a>Permiso de ubicación para iOS

Para iOS, el **info.plist** archivo debe incluir elementos que contiene el texto de una pregunta al usuario que permita obtener la ubicación del usuario.

#### <a name="location-permissions-for-android"></a>Permisos de ubicación para Android

Las aplicaciones de Android que obtener la ubicación del usuario deben tener un permiso ACCESS_FILE_LOCATION en el archivo AndroidManifest.xml.

#### <a name="location-permissions-for-the-uwp"></a>Permisos de ubicación para UWP

Una aplicación de plataforma Universal de Windows debe tener un `location` marcados como la funcionalidad del dispositivo en el archivo Package.appxmanifest.

## <a name="working-with-xamarinformsmaps"></a>Trabajar con xamarin.Forms.Maps para

Están implicados varios requisitos en utilizando la `Map` clase.

### <a name="the-nuget-package"></a>El paquete de NuGet

El **xamarin.Forms.Maps para** biblioteca NuGet debe agregarse a la solución de aplicación. El número de versión debe ser el mismo que el **Xamarin.Forms** paquete instalado actualmente.

### <a name="initializing-the-maps-package"></a>Inicializando el paquete de mapas

Los proyectos de aplicación deben llamar a la `Xamarin.FormsMaps.Init` método después de realizar una llamada a `Xamarin.Forms.Forms.Init`.

### <a name="enabling-map-services"></a>Habilitar los servicios de mapa

Dado que el `Map` puede obtener la ubicación del usuario, la aplicación debe obtener el permiso del usuario de la manera descrita anteriormente en este capítulo:

#### <a name="enabling-ios-maps"></a>Asigna la habilitación de iOS

Una aplicación de iOS con `Map` necesita dos líneas en el archivo info.plist.

#### <a name="enabling-android-maps"></a>Habilitación de Android asigna

Falta una clave de autorización para usar los servicios de mapas de Google. Esta clave se inserta en la **AndroidManifest.xml** archivo. Además, el **AndroidManifest.xml** archivo requiere `manifest` etiquetas implicados en la obtención de la ubicación del usuario.

#### <a name="enabling-uwp-maps"></a>Habilitación de UWP asigna

Una aplicación de plataforma Universal de Windows requiere una clave de autorización para el uso de mapas de Bing. Esta clave se pasa como argumento a la `Xamarin.FormsMaps.Init` método. La aplicación también debe habilitarse para los servicios de ubicación.

### <a name="the-unadorned-map"></a>La asignación de tipos sencilla

El [ **MapDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos) ejemplo consta de un [MapsDemoHomePage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml) archivo y [MapsDemoHomePage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs) archivo de código subyacente que permite navegar a varios programas de demostración.

El [BasicMapPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml) archivo muestra cómo mostrar la [ `Map` ](xref:Xamarin.Forms.Maps.Map) vista. De forma predeterminada, muestra la ciudad de Roma, pero el usuario puede manipular el mapa.

Para deshabilitar el desplazamiento horizontal y vertical, establezca el [ `HasScrollEnabled` ](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) propiedad `false`. Para deshabilitar el zoom, establezca [ `HasZoomEnabled` ](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) a `false`. Estas propiedades no funcionen en todas las plataformas.

### <a name="streets-and-terrain"></a>Calles y el terreno

Puede mostrar diferentes tipos de mapas de estableciendo el `Map` propiedad [ `MapType` ](xref:Xamarin.Forms.Maps.Map.MapType) typu [ `MapType` ](xref:Xamarin.Forms.Maps.MapType), una enumeración con tres miembros:

- [`Street`](xref:Xamarin.Forms.Maps.MapType.Street), el valor predeterminado
- [`Satellite`](xref:Xamarin.Forms.Maps.MapType.Satellite)
- [`Hybrid`](xref:Xamarin.Forms.Maps.MapType.Hybrid)

El [MapTypesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml) archivo muestra cómo usar un botón de radio para seleccionar el tipo de mapa. Hace uso de la [ `RadioButtonManager` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca y una clase según la [MapTypeRadioButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml) archivo.

### <a name="map-coordinates"></a>Coordenadas de mapa

Un programa puede obtener el área actual el `Map` muestra a través de la [ `VisibleRegion` ](xref:Xamarin.Forms.Maps.Map.VisibleRegion) propiedad. Esta propiedad es *no* respaldada por una propiedad enlazable, y no hay ningún mecanismo de notificación para indicar cuándo ha cambiado, por lo que un programa que desea supervisar la propiedad probablemente debería utilizar un temporizador para ese propósito.

`VisibleRegion` es de tipo [ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan), una clase con cuatro propiedades de solo lectura:

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center) de tipo [`Position`](xref:Xamarin.Forms.Maps.Position)
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees) de tipo `double`, que indica el alto del área de la asignación mostrado
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees) de tipo `double`, que indica el ancho del área de la asignación mostrado
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius) de tipo [ `Distance` ](xref:Xamarin.Forms.Maps.Distance), que indica el tamaño de la mayor área circular visible en el mapa

`Position` y `Distance` son ambas estructuras. `Position` define dos propiedades de solo lectura establecidas a través de la [ `Position` constructor](xref:Xamarin.Forms.Maps.Position.%23ctor(System.Double,System.Double)):

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)

`Distance` está pensado para proporcionar una distancia de unidad independiente mediante la conversión entre unidades métricas e inglesas. Un `Distance` valor se puede crear de varias maneras:

- [`Distance` constructor](xref:Xamarin.Forms.Maps.Distance.%23ctor(System.Double)) con una distancia en metros
- [`Distance.FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters(System.Double)) método estático
- [`Distance.FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers(System.Double)) método estático
- [`Distance.FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles(System.Double)) método estático

El valor está disponible en tres propiedades:

- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters) de tipo `double`
- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers) de tipo `double`
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles) de tipo `double`

El [MapCoordinatesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml) archivo contiene varias `Label` elementos para mostrar el `MapSpan` información. El [MapCoordinatesPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs) archivo de código subyacente usa un temporizador para mantener la información que se actualiza a medida que el usuario manipula el mapa.

### <a name="position-extensions"></a>Extensiones de posición

Una nueva biblioteca para este libro denominado [ **Xamarin.FormsBook.Toolkit.Maps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps) contiene tipos específicos de mapas, pero independiente de la plataforma. El [ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) clase tiene un `ToString` método para `Position`y un método para calcular la distancia entre dos `Position` valores.

### <a name="setting-an-initial-location"></a>Una ubicación inicial de la configuración

Puede llamar a la [ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)) método `Map` para establecer mediante programación un nivel de zoom y la ubicación en el mapa. El argumento es de tipo `MapSpan`. Puede crear un `MapSpan` objeto mediante cualquiera de las siguientes:

- [`MapSpan` constructor](xref:Xamarin.Forms.Maps.MapSpan.%23ctor(Xamarin.Forms.Maps.Position,System.Double,System.Double)) con un `Position`y el intervalo de latitud y longitud
- [`MapSpan.FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius(Xamarin.Forms.Maps.Position,Xamarin.Forms.Maps.Distance)) con un `Position` y radius

También es posible crear un nuevo `MapSpan` desde otra existente mediante los métodos [ `ClampLatitude` ](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude(System.Double,System.Double)) o [ `WithZoom` ](xref:Xamarin.Forms.Maps.MapSpan.WithZoom(System.Double)).

El [WyomingPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml) archivo y [WyomingPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs) archivo de código muestra cómo utilizar el `MoveToRegion` método para mostrar el estado de Wyoming.

Como alternativa, puede usar el [ `Map` constructor](xref:Xamarin.Forms.Maps.Map.%23ctor(Xamarin.Forms.Maps.MapSpan)) con un `MapSpan` objeto para inicializar la ubicación del mapa. El [XamarinHQPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml) archivo muestra cómo hacer esto completamente en XAML para mostrar la sede central de Xamarin en San Francisco.

### <a name="dynamic-zooming"></a>Zoom dinámico

Puede usar un `Slider` zoom dinámicamente un mapa. El [RadiusZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml) archivo y [RadiusZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs) archivo de código se muestra cómo cambiar el radio de un mapa según la `Slider` valor.

El [LongitudeZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml) archivo y [LongitudeZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs) archivo de código se muestra un enfoque alternativo que mejor funcione en Android, pero ningún enfoque funciona bien en el Windows plataformas.

### <a name="the-phones-location"></a>La ubicación del teléfono

El [ `IsShowingUser` ](xref:Xamarin.Forms.Maps.Map.IsShowingUser) propiedad de `Map` funciona un poco distinto en cada plataforma, como el [ShowLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml) archivo de muestra:

- En iOS, un punto azul indica la ubicación del teléfono pero manualmente debe ir allí
- En Android, se muestra un icono que, cuando inserta mueve la asignación a la ubicación del teléfono
- UWP es similar a iOS, pero a veces, automáticamente se desplaza a la ubicación

El **MapDemos** proyecto intenta imitar el enfoque de Android mediante la primera definición de un botón basado en el icono en función de la [MyLocationButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml) archivo y [MyLocationButton.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs) archivo de código subyacente.

El [GoToLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml) archivo y [GoToLocationPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs) archivo de código subyacente se use este botón para navegar a la ubicación del teléfono.

### <a name="pins-and-science-museums"></a>Los PIN y museos ciencia

Por último, el `Map` clase define un [ `Pins` ](xref:Xamarin.Forms.Maps.Map.Pins) propiedad de tipo `IList<Pin>`. El [ `Pin` ](xref:Xamarin.Forms.Maps.Pin) clase define cuatro propiedades:

- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label) de tipo `string`, una propiedad necesaria
- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address) de tipo `string`, una dirección legible opcional
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position) de tipo `Position`, que indica que el pin se muestra en el mapa
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type) de tipo [ `PinType` ](xref:Xamarin.Forms.Maps.PinType), una enumeración, que no se usa

El **MapDemos** proyecto contiene el archivo [ScienceMuseums.xml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml), que enumera los museos de ciencia de los Estados Unidos, y [ `Locations` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs) y [ `Site` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs) clases para deserializar estos datos.

El [ScienceMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml) archivo y [ScienceMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs) pines de mostrar archivo de código subyacente para estos museos Ciencia del mapa. Cuando el usuario puntea un pin, muestra la dirección y un sitio Web para el Museo.

### <a name="the-distance-between-two-points"></a>La distancia entre dos puntos

El [ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) clase contiene un [ `DistanceTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88) método con un cálculo simplificado de la distancia entre dos ubicaciones geográficas.

Esto se usa en el [LocalMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml) archivo y [LocalMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs) archivo de código subyacente también mostrar la distancia al Museo de la ubicación del usuario:

[![Captura de pantalla triple de página Local de museos](images/ch28fg28-small.png "distancia a una ubicación")](images/ch28fg28-large.png#lightbox "distancia a una ubicación")

El programa también muestra cómo restringir dinámicamente el número de bolos según la ubicación del mapa.

## <a name="geocoding-and-back-again"></a>Geocodificación y volver

El [ **xamarin.Forms.Maps para** ](xref:Xamarin.Forms.Maps) ensamblado también contiene un [ `Geocoder` ](xref:Xamarin.Forms.Maps.Geocoder) clase con un [ `GetPositionsForAddressAsync` ](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync(System.String)) método que convierte una dirección de texto en cero o más posibles posiciones geográficas y otro método [ `GetAddressesForPositionAsync` ](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync(Xamarin.Forms.Maps.Position)) que se convierte en la otra dirección.

El [GeocoderRoundTrip.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml) archivo y [GeocoderRoundTrip.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs) archivo de código subyacente demostrar esta función.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 28 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [Capítulo 28 ejemplos](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [Mapa de Xamarin.Forms](~/xamarin-forms/user-interface/map.md)
