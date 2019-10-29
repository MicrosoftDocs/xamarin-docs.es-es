---
title: Uso de la API de Google Maps en la aplicación
description: Cómo implementar las características de Google Maps API V2 en la aplicación de Xamarin. Android.
ms.prod: xamarin
ms.assetid: C0589878-2D04-180E-A5B9-BB41D5AF6E02
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/07/2018
ms.openlocfilehash: adcfb1457742d343f87a602885566107cf327e2d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027150"
---
# <a name="using-the-google-maps-api-in-your-application"></a>Uso de la API de Google Maps en la aplicación

El uso de la aplicación Maps es genial, pero a veces quiere incluir asignaciones directamente en la aplicación. Además de la aplicación de mapas integrada, Google también ofrece una [API de asignación nativa para Android](https://developers.google.com/maps/documentation/android-sdk/intro).
La API de Maps es adecuada para los casos en los que desea mantener más control sobre la experiencia de asignación. Entre los elementos que son posibles con la API de Maps se incluyen:

- Cambiar mediante programación el punto de vista de la asignación.
- Adición y personalización de marcadores.
- Anotar un mapa con superposiciones.

A diferencia de la API de Android de Google Maps, que ahora está en desuso, la API de Android v2 de Google Maps es parte de [Google Play Services](https://developers.google.com/android/guides/overview).
Una aplicación de Xamarin. Android debe cumplir algunos requisitos previos obligatorios antes de poder usar la API de Android de Google Maps.

## <a name="google-maps-api-prerequisites"></a>Requisitos previos de la API de Google Maps

Es necesario realizar varios pasos para poder usar la API de Maps, entre las que se incluyen:

- [Obtención de una clave de API de Maps](#obtain-maps-key)
- [Instalación del SDK de Google Play Services](#install-gps-sdk)
- [Instalación del paquete Xamarin. GooglePlayServices. Maps desde NuGet](#install-gpsmaps-nuget)
- [Especificar los permisos necesarios](#declare-permissions)
- [Opcionalmente, cree un emulador con las API de Google](#create-emulator-with-google-api)

### <a name="a-nameobtain-maps-key-obtain-a-google-maps-api-key"></a><a name="obtain-maps-key" />obtener una clave de API de Google Maps

El primer paso es obtener una clave de API de Google Maps (tenga en cuenta que no puede reutilizar una clave de API de la API de Google Maps v1 heredada). Para obtener información sobre cómo obtener y usar la clave de API con Xamarin. Android, consulte [obtención de una clave de API de Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

### <a name="a-nameinstall-gps-sdk--install-the-google-play-services-sdk"></a><a name="install-gps-sdk" /> instalar el SDK de Google Play Services

Google Play Services es una tecnología de Google que permite a las aplicaciones Android aprovechar las ventajas de diversas características de Google, como Google +, In-App Billing y Maps. Estas características son accesibles en dispositivos Android como servicios en segundo plano, que se encuentran en el [Google Play Services apk](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en).

Las aplicaciones de Android interactúan con Google Play Services a través de la biblioteca de cliente de Google Play Services. Esta biblioteca contiene las interfaces y las clases de los servicios individuales, como Maps. En el diagrama siguiente se muestra la relación entre una aplicación de Android y Google Play Services:

![Diagrama que ilustra el Google Play Store actualizar el Google Play Services APK](maps-api-images/play-services-diagram.png)

La API de mapas de Android se proporciona como parte de Google Play Services.
Antes de que una aplicación de Xamarin. Android pueda usar la API de Maps, el SDK de Google Play Services debe instalarse con el [Administrador de Android SDK](~/android/get-started/installation/android-sdk.md). En la captura de pantalla siguiente se muestra en qué lugar del administrador de Android SDK se puede encontrar el cliente de Google Play Services:

![Google Play Services aparece en extras en el administrador de Android SDK](maps-api-images/image01.png)

> [!NOTE]
> Google Play Services APK es un producto con licencia que puede no estar presente en todos los dispositivos. Si no está instalado, Google Maps no funcionará en el dispositivo.

### <a name="a-nameinstall-gpsmaps-nuget--install-the-xamaringoogleplayservicesmaps-package-from-nuget"></a><a name="install-gpsmaps-nuget" /> instalar el paquete Xamarin. GooglePlayServices. Maps desde NuGet

El [paquete Xamarin. GooglePlayServices. Maps](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Maps) contiene los enlaces de Xamarin. Android para la API de Google Play Services Maps.
Para agregar el paquete de asignación de Google Play Services, haga clic con el botón derecho en la carpeta **referencias** del proyecto en el explorador de soluciones y haga clic en **administrar paquetes NuGet...** :

![Explorador de soluciones muestra el elemento de menú contextual administrar paquetes NuGet en referencias](maps-api-images/image02.png)

Se abrirá el **Administrador de paquetes NuGet**. Haga clic en **examinar** y escriba **Xamarin Google Play Services Maps** en el campo de búsqueda. Seleccione **Xamarin. GooglePlayServices. Maps** y haga clic en **instalar**. (Si este paquete se ha instalado previamente, haga clic en **Actualizar**):

[![administrador de paquetes NuGet con el paquete Xamarin. GooglePlayServices. Maps seleccionado](maps-api-images/image03-sml.png)](maps-api-images/image03.png#lightbox)

Tenga en cuenta que también se instalan los siguientes paquetes de dependencia:

- **Xamarin. GooglePlayServices. base**
- **Xamarin. GooglePlayServices. sótano**
- **Xamarin. GooglePlayServices. Tasks**

### <a name="a-namedeclare-permissions--specify-the-required-permissions"></a><a name="declare-permissions" /> especificar los permisos necesarios

Las aplicaciones deben identificar los requisitos de hardware y permisos para poder usar la API de Google Maps.  El SDK de Google Play Services concede algunos permisos automáticamente y no es necesario que un desarrollador los agregue explícitamente a **AndroidManfest. XML**:

- El **acceso al estado de la red** &ndash; la API de Maps debe ser capaz de comprobar si puede descargar los iconos del mapa.

- El acceso a **internet** &ndash; acceso a Internet es necesario para descargar los iconos del mapa y comunicarse con los servidores Google Play para el acceso a la API.

Los siguientes permisos y características deben especificarse en el **archivo archivo AndroidManifest. XML** de la API Android de Google Maps:

- **OpenGL es v2** &ndash; la aplicación debe declarar el requisito de OpenGL es V2.

- La **clave de API de Google Maps** &ndash; la clave de API se usa para confirmar que la aplicación está registrada y autorizada para usar Google Play Services. Consulte [obtención de una clave de API de Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md) para más información sobre esta clave.

- **Solicite al cliente http de Apache heredado las** aplicaciones &ndash; que tengan como destino Android 9,0 (nivel de API 28) o superior deben especificar que el cliente Apache HTTP heredado es una biblioteca opcional que se debe usar.

- El **acceso a los servicios basados en Web de google** &ndash; la aplicación necesita permisos para acceder a los servicios Web de Google que respaldan la API de mapas de Android.

- Los **permisos para las notificaciones de Google Play Services** &ndash; la aplicación deben tener permiso para recibir notificaciones remotas de Google Play Services.

- El **acceso a los proveedores de ubicación** &ndash; estos son permisos opcionales.
   Permitirán que la clase `GoogleMap` muestre la ubicación del dispositivo en el mapa.

Además, Android 9 ha quitado la biblioteca de cliente de Apache HTTP de bootclasspath y, por tanto, no está disponible para las aplicaciones que tienen como destino la API 28 o una versión posterior. Se debe agregar la siguiente línea al nodo `application` del archivo **archivo AndroidManifest. XML** para seguir usando el cliente http de Apache en aplicaciones que tienen como destino la API 28 o posterior:

```xml
<application ...>
   ...
   <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

> [!NOTE]
> Las versiones muy antiguas del SDK de Google Play requirieron que una aplicación solicitara el permiso de `WRITE_EXTERNAL_STORAGE`. Este requisito ya no es necesario con los enlaces de Xamarin recientes para Google Play Services.

El siguiente fragmento de código es un ejemplo de la configuración que se debe agregar a **archivo AndroidManifest. XML**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionName="4.5" package="com.xamarin.docs.android.mapsandlocationdemo2" android:versionCode="6">
    <uses-sdk android:minSdkVersion="23" android:targetSdkVersion="28" />

    <!-- Google Maps for Android v2 requires OpenGL ES v2 -->
    <uses-feature android:glEsVersion="0x00020000" android:required="true" />

    <!-- Necessary for apps that target Android 9.0 or higher -->
    <uses-library android:name="org.apache.http.legacy" android:required="false" />

    <!-- Permission to receive remote notifications from Google Play Services -->
    <!-- Notice here that we have the package name of our application as a prefix on the permissions. -->
    <uses-permission android:name="<PACKAGE NAME>.permission.MAPS_RECEIVE" />
    <permission android:name="<PACKAGE NAME>.permission.MAPS_RECEIVE" android:protectionLevel="signature" />

    <!-- These are optional, but recommended. They will allow Maps to use the My Location provider. -->
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />

    <application android:label="@string/app_name">
        <!-- Put your Google Maps V2 API Key here. -->
        <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
        <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
        <!-- Necessary for apps that target Android 9.0 or higher -->
        <uses-library android:name="org.apache.http.legacy" android:required="false" />
    </application>
</manifest>
```

Además de solicitar los permisos **archivo AndroidManifest. XML**, una aplicación también debe realizar comprobaciones de permisos en tiempo de ejecución para el `ACCESS_COARSE_LOCATION` y los permisos de `ACCESS_FINE_LOCATION`. Consulte la guía de [permisos de Xamarin. Android](~/android/app-fundamentals/permissions.md) para obtener más información sobre cómo realizar comprobaciones de permisos en tiempo de ejecución.

### <a name="a-namecreate-emulator-with-google-api-create-an-emulator-with-google-apis"></a><a name="create-emulator-with-google-api" />crear un emulador con las API de Google

En caso de que no se instale un dispositivo Android físico con Google Play Services, es posible crear una imagen de emulador para el desarrollo. Para obtener más información, vea el [Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).

## <a name="the-googlemap-class"></a>La clase GoogleMap

Una vez que se cumplen los requisitos previos, es el momento de empezar a desarrollar la aplicación y usar la API de mapas de Android. La clase [googlemap](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap) es la API principal que utilizará una aplicación de Xamarin. Android para mostrar e interactuar con Google Maps para Android. Esta clase tiene las siguientes responsabilidades:

- Interacción con Google Play Services para autorizar la aplicación con el servicio Web de Google.

- Descargar, almacenar en caché y mostrar los mosaicos de mapa.

- Mostrar controles de interfaz de usuario, como pan y zoom, al usuario.

- Dibujar marcadores y formas geométricas en mapas.

El `GoogleMap` se agrega a una actividad de una de estas dos maneras:

- **MapFragment** : [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) es un fragmento especializado que actúa como host para el objeto `GoogleMap`. El `MapFragment` requiere el nivel de API de Android 12 o superior.
   Las versiones anteriores de Android pueden usar [SupportMapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/SupportMapFragment).  Esta guía se centrará en el uso de la clase `MapFragment`.

- **MapView** : el [MapView](https://developers.google.com/android/reference/com/google/android/gms/maps/MapView) es una subclase de vista especializada, que puede actuar como host para un objeto de `GoogleMap`. Los usuarios de esta clase deben reenviar todos los métodos del ciclo de vida de la actividad a la clase `MapView`.

Cada uno de estos contenedores expone una propiedad `Map` que devuelve una instancia de `GoogleMap`. Se debe dar preferencia a la clase [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) , ya que es una API más sencilla que reduce la cantidad de código reutilizable que un desarrollador debe implementar manualmente.

### <a name="adding-a-mapfragment-to-an-activity"></a>Agregar un MapFragment a una actividad

La captura de pantalla siguiente es un ejemplo de un `MapFragment`simple:

[![captura de pantalla de un dispositivo que muestra un fragmento de mapa de Google](maps-api-images/image05-sml.png)](maps-api-images/image05.png#lightbox)

De forma similar a otras clases de fragmentos, hay dos maneras de agregar una `MapFragment` a una actividad:

- **Mediante declaración** : el `MapFragment` se puede agregar mediante el archivo de diseño XML para la actividad. El siguiente fragmento XML muestra un ejemplo de cómo utilizar el elemento `fragment`:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/map"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              class="com.google.android.gms.maps.MapFragment" />
    ```

- **Mediante programación** : se pueden crear instancias del `MapFragment` mediante programación con el método [`MapFragment.NewInstance`](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment.html#newInstance()) y, a continuación, agregar a una actividad. Este fragmento de código muestra la manera más sencilla de crear una instancia de un `MapFragment` objeto y agregarlo a una actividad:

    ```csharp
        var mapFrag = MapFragment.NewInstance();
        activity.FragmentManager.BeginTransaction()
                                .Add(Resource.Id.map_container, mapFrag, "map_fragment")
                                .Commit();

    ```

    Es posible configurar el objeto `MapFragment` pasando un objeto [`GoogleMapOptions`](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) a `NewInstance`. Esto se describe en la sección [propiedades de googlemap](#googlemap_object) que aparece más adelante en esta guía.

El método `MapFragment.GetMapAsync` se usa para inicializar el [`GoogleMap`](#googlemap_object) hospedado por el fragmento y obtener una referencia al objeto de mapa hospedado por el `MapFragment`. Este método toma un objeto que implementa la interfaz `IOnMapReadyCallback`.

Esta interfaz tiene un solo método, `IMapReadyCallback.OnMapReady(MapFragment map)` que se invocará cuando sea posible que la aplicación interactúe con el objeto `GoogleMap`. En el fragmento de código siguiente se muestra cómo una actividad de Android puede inicializar un `MapFragment` e implementar la interfaz de `IOnMapReadyCallback`:

```csharp
public class MapWithMarkersActivity : AppCompatActivity, IOnMapReadyCallback
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.MapLayout);

        var mapFragment = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.map);
        mapFragment.GetMapAsync(this);

        // remainder of code omitted
    }

    public void OnMapReady(GoogleMap map)
    {
        // Do something with the map, i.e. add markers, move to a specific location, etc.
    }
}
```

### <a name="map-types"></a>Tipos de asignación

Hay cinco tipos diferentes de mapas disponibles en la API de Google Maps:

- **Normal** : este es el tipo de asignación predeterminado. Muestra carreteras y características naturales importantes junto con algunos puntos de interés artificiales (como edificios y puentes).

- **Satellite** : este mapa muestra la fotografía por satélite.

- **Híbrido** : este mapa muestra la fotografía de satélite y los mapas de carreteras.

- **Terrain** : Esto muestra principalmente características topográficas con algunas carreteras.

- **Ninguno** : este mapa no carga ningún mosaico, se representa como una cuadrícula vacía.

En la imagen siguiente se muestran tres de los distintos tipos de mapas, de izquierda a derecha (normal, híbrido, Terrain):

[![tres capturas de pantallas de ejemplo de mapa: normal, híbrida y Terrain](maps-api-images/map-types-sml.png)](maps-api-images/map-types.png#lightbox)

La propiedad `GoogleMap.MapType` se utiliza para establecer o cambiar el tipo de mapa que se muestra. En el fragmento de código siguiente se muestra cómo mostrar una asignación satélite.

```csharp
public void OnMapReady(GoogleMap map)
{
    map.MapType = GoogleMap.MapTypeHybrid;
}
```

### <a name="a-namegooglemap_object-googlemap-properties"></a><a name="googlemap_object" />propiedades de GoogleMap

`GoogleMap` define varias propiedades que pueden controlar la funcionalidad y la apariencia del mapa. Una manera de configurar el estado inicial de una `GoogleMap` es pasar un objeto [GoogleMapOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) al crear un `MapFragment`. El siguiente fragmento de código es un ejemplo del uso de un objeto `GoogleMapOptions` al crear un `MapFragment`:

```csharp
GoogleMapOptions mapOptions = new GoogleMapOptions()
    .InvokeMapType(GoogleMap.MapTypeSatellite)
    .InvokeZoomControlsEnabled(false)
    .InvokeCompassEnabled(true);

FragmentTransaction fragTx = FragmentManager.BeginTransaction();
mapFragment = MapFragment.NewInstance(mapOptions);
fragTx.Add(Resource.Id.map, mapFragment, "map");
fragTx.Commit();
```

La otra forma de configurar una `GoogleMap` es manipular propiedades en el [UiSettings](https://developers.google.com/android/reference/com/google/android/gms/maps/UiSettings) del objeto de mapa. En el siguiente ejemplo de código se muestra cómo configurar un `GoogleMap` para mostrar los controles de zoom y una brújula:

```csharp
public void OnMapReady(GoogleMap map)
{
    map.UiSettings.ZoomControlsEnabled = true;
    map.UiSettings.CompassEnabled = true;
}
```

## <a name="interacting-with-the-googlemap"></a>Interacción con GoogleMap

La API de mapas de Android proporciona API que permiten a una actividad cambiar el punto de vista, agregar marcadores, colocar superposiciones personalizadas o dibujar formas geométricas. En esta sección se explica cómo realizar algunas de estas tareas en Xamarin. Android.

### <a name="changing-the-viewpoint"></a>Cambiar el punto de vista

Las asignaciones se modelan como un plano plano en la pantalla, en función de la proyección Mercator. La vista del mapa es la de una *cámara* que mira hacia abajo en este plano. La posición de la cámara se puede controlar cambiando la ubicación, el zoom, la inclinación y el cojinete. La clase [CameraUpdate](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdate) se usa para trasladar la ubicación de la cámara. no se crean instancias de los objetos `CameraUpdate` directamente, sino que la API de Maps proporciona la clase [CameraUpdateFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdateFactory) .

Una vez creado un objeto de `CameraUpdate`, se pasa como parámetro a los métodos [googlemap. MoveCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#moveCamera(com.google.android.gms.maps.CameraUpdate)) o [googlemap. AnimateCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#animateCamera(com.google.android.gms.maps.CameraUpdate)) . El método `MoveCamera` actualiza la asignación al instante, mientras que el método `AnimateCamera` proporciona una transición suave y animada.

Este fragmento de código es un ejemplo sencillo de cómo usar la `CameraUpdateFactory` para crear una `CameraUpdate` que aumentará el nivel de zoom del mapa en un nivel de zoom:

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...

public void OnMapReady(GoogleMap map)
{   
    map.MoveCamera(CameraUpdateFactory.ZoomIn());
}
```

La API de Maps proporciona un [CameraPosition](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.html) que agregará todos los valores posibles para la posición de la cámara. Se puede proporcionar una instancia de esta clase al método [CameraUpdateFactory. NewCameraPosition](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdateFactory#newCameraPosition%28com.google.android.gms.maps.model.CameraPosition%29) , que devolverá un objeto `CameraUpdate`. La API de Maps también incluye la clase [CameraPosition. Builder](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.Builder.html) que proporciona una API fluida para crear objetos `CameraPosition`.
En el fragmento de código siguiente se muestra un ejemplo de cómo crear un `CameraUpdate` a partir de un `CameraPosition` y usarlo para cambiar la posición de la cámara en un `GoogleMap`:

```csharp
public void OnMapReady(GoogleMap map)
{
    LatLng location = new LatLng(50.897778, 3.013333);

    CameraPosition.Builder builder = CameraPosition.InvokeBuilder();
    builder.Target(location);
    builder.Zoom(18);
    builder.Bearing(155);
    builder.Tilt(65);

    CameraPosition cameraPosition = builder.Build();

    CameraUpdate cameraUpdate = CameraUpdateFactory.NewCameraPosition(cameraPosition);

    map.MoveCamera(cameraUpdate);
}
```

En el fragmento de código anterior, la clase [LatLng](https://developers.google.com/android/reference/com/google/android/gms/maps/model/LatLng) representa una ubicación específica en el mapa. El nivel de zoom se establece en 18, que es una medida arbitraria de zoom usada por Google Maps. El cojinete es la medida de brújula en el sentido de las agujas del reloj desde el norte. La propiedad Tilt controla el ángulo de visualización y especifica un ángulo de 25 grados desde el vertical. En la captura de pantalla siguiente se muestra el `GoogleMap` después de ejecutar el código anterior:

[![ejemplo de Google Map que muestra una ubicación especificada con un ángulo de visualización inclinado](maps-api-images/image06-sml.png)](maps-api-images/image06.png#lightbox)

### <a name="drawing-on-the-map"></a>Dibujo en el mapa

La API de mapas de Android proporciona API para dibujar los siguientes elementos en un mapa:

- **Marcadores** : son iconos especiales que se usan para identificar una ubicación única en un mapa.

- **Superposiciones** : se trata de una imagen que se puede usar para identificar una colección de ubicaciones o áreas en el mapa.

- **Líneas, polígonos y círculos** : son las API que permiten a las actividades agregar formas a un mapa.

#### <a name="markers"></a>Markers

La API de Maps proporciona una clase de [marcador](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker) que encapsula todos los datos sobre una ubicación única en un mapa. De forma predeterminada, la clase de marcador usa un icono estándar proporcionado por Google Maps. Es posible personalizar la apariencia de un marcador y responder a los clics del usuario.

##### <a name="adding-a-marker"></a>Agregar un marcador

Para agregar un marcador a un mapa, es necesario crear un nuevo objeto [MarkerOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/MarkerOptions) y, a continuación, llamar al método [AddMarker](https://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addMarker%28com.google.android.gms.maps.model.MarkerOptions%29) en una instancia de `GoogleMap`. Este método devolverá un objeto de [marcador](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker) .

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");

    map.AddMarker(markerOpt1);
}
```

El título del marcador se mostrará en una ventana de *información* cuando el usuario pulse en el marcador. En la captura de pantalla siguiente se muestra el aspecto de este marcador:

[![ejemplo de Google Map con un marcador y una ventana de información para Vimy rugoso](maps-api-images/image07-sml.png)](maps-api-images/image07.png#lightbox)

##### <a name="customizing-a-marker"></a>Personalización de un marcador

Es posible personalizar el icono utilizado por el marcador llamando al método `MarkerOptions.InvokeIcon` al agregar el marcador al mapa.
Este método toma un objeto [BitmapDescriptor](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptor) que contiene los datos necesarios para representar el icono. La clase [BitmapDescriptorFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptorFactory) proporciona algunos métodos auxiliares para simplificar la creación de un `BitmapDescriptor`. En la siguiente lista se presentan algunos de estos métodos:

- `DefaultMarker(float colour)` &ndash; usar el marcador predeterminado de Google Maps, pero cambiar el color.

- `FromAsset(string assetName)` &ndash; usar un icono personalizado del archivo especificado en la carpeta assets.

- `FromBitmap(Bitmap image)` &ndash; usar el mapa de bits especificado como icono.

- `FromFile(string fileName)` &ndash; crear el icono personalizado a partir del archivo en la ruta de acceso especificada.

- `FromResource(int resourceId)` &ndash; crear un icono personalizado a partir del recurso especificado.

En el fragmento de código siguiente se muestra un ejemplo de cómo crear un marcador predeterminado con color aguamarina:

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");

    var bmDescriptor = BitmapDescriptorFactory.DefaultMarker (BitmapDescriptorFactory.HueCyan);
    markerOpt1.InvokeIcon(bmDescriptor);

    map.AddMarker(markerOpt1);
}
```

#### <a name="info-windows"></a>Ventanas de información

Las *ventanas de información* son ventanas especiales que muestran información al usuario al puntear en un marcador específico. De forma predeterminada, la ventana de información mostrará el contenido del título del marcador. Si no se ha asignado el título, no aparecerá ninguna ventana de información. Solo se puede mostrar una ventana de información cada vez.

Es posible personalizar la ventana de información implementando la interfaz [googlemap. IInfoWindowAdapter](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.InfoWindowAdapter) . Hay dos métodos importantes en esta interfaz:

- `public View GetInfoWindow(Marker marker)` &ndash; se llama a este método para obtener una ventana de información personalizada para un marcador. Si devuelve `null`, se utilizará la representación de ventana predeterminada. Si este método devuelve una vista, esa vista se colocará dentro del marco de la ventana de información.

- `public View GetInfoContents(Marker marker)` &ndash; este método solo se llamará si GetInfoWindow devuelve `null`. Este método puede devolver un valor `null` si se va a usar la representación predeterminada del contenido de la ventana de información. De lo contrario, este método debe devolver una vista con el contenido de la ventana de información.

Una ventana de información no es una vista activa: en su lugar, Android convertirá la vista en un mapa de bits estático y la mostrará en la imagen. Esto significa que una ventana de información no puede responder a ningún gesto o evento de toque, ni tampoco se actualiza automáticamente. Para actualizar una ventana de información, es necesario llamar al método [googlemap. ShowInfoWindow](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker.html#showInfoWindow()) .

En la imagen siguiente se muestran algunos ejemplos de algunas ventanas de información personalizadas. La imagen de la izquierda tiene el contenido personalizado, mientras que la imagen de la derecha tiene la ventana y el contenido personalizados con esquinas redondeadas:

![Ejemplos de ventanas de marcador para Melbourne, incluidos el icono y el rellenado. La ventana derecha tiene esquinas redondeadas.](maps-api-images/marker-infowindows.png)

#### <a name="groundoverlays"></a>GroundOverlays

A diferencia de los marcadores, que identifican una ubicación específica en un mapa, un [GroundOverlay](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlay) es una imagen que se usa para identificar una colección de ubicaciones o un área en el mapa.

##### <a name="adding-a-groundoverlay"></a>Adición de un GroundOverlay

Agregar una superposición a un mapa es similar a agregar un marcador a un mapa. En primer lugar, se crea un objeto [GroundOverlayOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlayOptions) . A continuación, este objeto se pasa como un parámetro al método [`GoogleMap.AddGroundOverlay`](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addGroundOverlay(com.google.android.gms.maps.model.GroundOverlayOptions)) , que devolverá un objeto `GroundOverlay`. Este fragmento de código es un ejemplo de cómo agregar una superposición a un mapa:

```csharp
BitmapDescriptor image = BitmapDescriptorFactory.FromResource(Resource.Drawable.polarbear);
GroundOverlayOptions groundOverlayOptions = new GroundOverlayOptions()
    .Position(position, 150, 200)
    .InvokeImage(image);
GroundOverlay myOverlay = googleMap.AddGroundOverlay(groundOverlayOptions);
```

En la captura de pantalla siguiente se muestra esta superposición en un mapa:

[![mapa de ejemplo con una imagen superpuesta de un osito polar](maps-api-images/image09-sml.png)](maps-api-images/image09.png#lightbox)

#### <a name="lines-circles-and-polygons"></a>Líneas, círculos y polígonos

Hay tres tipos simples de figuras geométricas que se pueden agregar a un mapa:

- **Polyline** : se trata de una serie de segmentos de línea conectados. Puede marcar una ruta de acceso en un mapa o crear una forma geométrica.

- **Círculo** : dibujará un círculo en el mapa.

- **Polygon** : se trata de una forma cerrada para marcar áreas en un mapa.

##### <a name="polylines"></a>Polilíneas

Una [polilínea](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Polyline) es una lista de objetos de `LatLng` consecutivos que especifican los vértices de cada segmento de línea. Una polilínea se crea creando primero un `PolylineOptions` objeto y agregando los puntos. A continuación, el objeto de `PolylineOption` se pasa a un objeto `GoogleMap` llamando al método `AddPolyline`.

```csharp
PolylineOption rectOptions = new PolylineOption();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
rectOptions.Add(new LatLng(37.35, -122.0)); // close the polyline - this makes a rectangle.

googleMap.AddPolyline(rectOptions);
```

##### <a name="circles"></a>Vueltas

Los círculos se crean creando primero una instancia de un objeto [CircleOption](https://developers.google.com/android/reference/com/google/android/gms/maps/model/CircleOptions) que especificará el centro y el radio del círculo en metros. El círculo se dibuja en el mapa mediante una llamada a [googlemap. AddCircle](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addCircle(com.google.android.gms.maps.model.CircleOptions)).
En el fragmento de código siguiente se muestra cómo dibujar un círculo:

```csharp
CircleOptions circleOptions = new CircleOptions ();
circleOptions.InvokeCenter (new LatLng(37.4, -122.1));
circleOptions.InvokeRadius (1000);

googleMap.AddCircle (circleOptions);
```

##### <a name="polygons"></a>Polígonos

`Polygon`s son similares a `Polyline`s, sin embargo, no están abiertos. `Polygon`s son un bucle cerrado y su interior se rellena.
`Polygon`s se crean de la misma manera que una `Polyline`, excepto el método [googlemap. AddPolygon](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addPolygon(com.google.android.gms.maps.model.PolygonOptions)) invocado.

A diferencia de un `Polyline`, un `Polygon` es autocierre. El método `AddPolygon` cerrará el polígono dibujando una línea que conecta los puntos primero y último. El fragmento de código siguiente creará un rectángulo sólido en la misma área que el fragmento de código anterior en el ejemplo `Polyline`.

```csharp
PolygonOptions rectOptions = new PolygonOptions();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
// notice we don't need to close off the polygon

googleMap.AddPolygon(rectOptions);
```

## <a name="responding-to-user-events"></a>Responder a eventos de usuario

Hay tres tipos de interacciones que un usuario puede tener con un mapa:

- **Clic de marcador** : el usuario hace clic en un marcador.

- **Arrastre de marcador** : el usuario ha pulsado un tiempo en un mparger

- **Ventana de información haga clic** en: el usuario ha pulsado en una ventana de información.

Cada uno de estos eventos se tratará con más detalle a continuación.

### <a name="marker-click-events"></a>Eventos de clic de marcador

El evento `MarkerClicked` se genera cuando el usuario puntea en un marcador. Este evento acepta un objeto de `GoogleMap.MarkerClickEventArgs` como parámetro. Esta clase contiene dos propiedades:

- `GoogleMap.MarkerClickEventArgs.Handled` &ndash; esta propiedad debe establecerse en `true` para indicar que el controlador de eventos ha consumido el evento. Si se establece en `false`, se producirá el comportamiento predeterminado además del comportamiento personalizado del controlador de eventos.

- `Marker` &ndash; esta propiedad es una referencia al marcador que ha generado el evento `MarkerClick`.

En este fragmento de código se muestra un ejemplo de un `MarkerClick` que cambiará la posición de la cámara a una nueva ubicación en el mapa:

```csharp
void MapOnMarkerClick(object sender, GoogleMap.MarkerClickEventArgs markerClickEventArgs)
{
    markerClickEventArgs.Handled = true;

    var marker = markerClickEventArgs.Marker;
    if (marker.Id.Equals(gotMauiMarkerId))
    {
        LatLng InMaui = new LatLng(20.72110, -156.44776);

        // Move the camera to look at Maui.
        PositionPolarBearGroundOverlay(InMaui);
        googleMap.AnimateCamera(CameraUpdateFactory.NewLatLngZoom(InMaui, 13));
        gotMauiMarkerId = null;
        polarBearMarker.Remove();
        polarBearMarker = null;
    }
    else
    {
        Toast.MakeText(this, $"You clicked on Marker ID {marker.Id}", ToastLength.Short).Show();
    }
}
```

### <a name="marker-drag-events"></a>Eventos de arrastre de marcador

Este evento se desencadena cuando el usuario desea arrastrar el marcador. De forma predeterminada, los marcadores no se pueden arrastrar. Un marcador puede establecerse como arrastrable estableciendo la propiedad `Marker.Draggable` en `true` o invocando el método `MarkerOptions.Draggable` con `true` como parámetro.

Para arrastrar el marcador, el usuario debe hacer clic en primer lugar en el marcador y, a continuación, el dedo debe permanecer en el mapa. Cuando se arrastra el dedo del usuario por la pantalla, el marcador se mueve. Cuando el dedo del usuario se levanta de la pantalla, el marcador permanecerá en su lugar.

En la lista siguiente se describen los diversos eventos que se generarán para un marcador arrastrable:

- `GoogleMap.MarkerDragStart(object sender, GoogleMap.MarkerDragStartEventArgs e)` &ndash; este evento se genera cuando el usuario arrastra el marcador por primera vez.

- `GoogleMap.MarkerDrag(object sender, GoogleMap.MarkerDragEventArgs e)` &ndash; este evento se genera cuando se arrastra el marcador.

- `GoogleMap.MarkerDragEnd(object sender, GoogleMap.MarkerDragEndEventArgs e)` &ndash; se genera este evento cuando el usuario ha terminado de arrastrar el marcador.

Cada una de las `EventArgs` contiene una propiedad única denominada `P0` que es una referencia al objeto `Marker` que se está arrastrando.

### <a name="info-window-click-events"></a>Eventos de clic en la ventana de información

Solo se puede mostrar una ventana de información cada vez. Cuando el usuario hace clic en una ventana de información de un mapa, el objeto de mapa generará un evento `InfoWindowClick`. En el fragmento de código siguiente se muestra cómo conectar un controlador al evento:

```csharp
public void OnMapReady(GoogleMap map)
{
    map.InfoWindowClick += MapOnInfoWindowClick;
}

private void MapOnInfoWindowClick (object sender, GoogleMap.InfoWindowClickEventArgs e)
{
    Marker myMarker = e.Marker;
    // Do something with marker.
}
```

Recuerde que una ventana de información es una `View` estática que se representa como una imagen en el mapa. Los widgets como botones, casillas o vistas de texto que se colocan dentro de la ventana de información serán inertes y no podrán responder a ninguno de sus eventos de usuario integrales.

## <a name="related-links"></a>Vínculos relacionados

- [SimpleMapDemo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3/SimpleMapDemo)
- [Google Play Services](https://developers.google.com/android/guides/overview)
- [API de Android de Google Maps V2](https://developers.google.com/maps/documentation/android-sdk/intro)
- [Google Play Services APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)
- [Obtención de una clave de API de Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [Use-Library](https://developer.android.com/guide/topics/manifest/uses-library-element)
- [Use-Feature](https://developer.android.com/guide/topics/manifest/uses-feature-element)
