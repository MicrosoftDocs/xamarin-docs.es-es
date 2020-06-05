---
title: "Xamarin.Essentials: geolocalización" description: "En este documento se describe la clase Geolocation de Xamarin.Essentials, que proporciona las API para recuperar las coordenadas de geolocalización actuales del dispositivo."
ms.assetid: 8F66092C-13F0-4FEE-8AA5-901D5F79B357 author: jamesmontemagno ms.custom: video ms.author: jamont ms.date: 03/13/2019 no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="xamarinessentials-geolocation"></a>Xamarin.Essentials: Geolocalización

La clase **Geolocation** proporciona las API para recuperar las coordenadas de geolocalización actuales del dispositivo.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

Para acceder a la funcionalidad de **Geolocation**, se requiere la siguiente configuración específica para la plataforma:

# <a name="android"></a>[Android](#tab/android)

Los permisos Coarse y Fine Location son requeridos y se deben configurar en el proyecto de Android. Además, si la aplicación tiene como destino Android 5.0 (nivel de API 21) o versiones posteriores, debe declarar que la aplicación usa las características de hardware en el archivo de manifiesto. Se puede agregar de las siguientes maneras:

Abra el archivo **AssemblyInfo.cs** de la carpeta **Propiedades** y agregue lo siguiente:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessCoarseLocation)]
[assembly: UsesPermission(Android.Manifest.Permission.AccessFineLocation)]
[assembly: UsesFeature("android.hardware.location", Required = false)]
[assembly: UsesFeature("android.hardware.location.gps", Required = false)]
[assembly: UsesFeature("android.hardware.location.network", Required = false)]
```

O bien, actualice el manifiesto de Android:

Abra el archivo **AndroidManifest.xml** de la carpeta **Propiedades** y agregue lo siguiente dentro del nodo **manifest**:

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-feature android:name="android.hardware.location" android:required="false" />
<uses-feature android:name="android.hardware.location.gps" android:required="false" />
<uses-feature android:name="android.hardware.location.network" android:required="false" />
```

O bien, haga clic con el botón derecho en el proyecto de Android y abra las propiedades del proyecto. En **Manifiesto de Android**, busque el área **Permisos requeridos:** y active los permisos **ACCESS_COARSE_LOCATION** y **ACCESS_FINE_LOCATION**. Esto actualizará automáticamente el archivo **AndroidManifest.xml**.

[!include[](~/essentials/includes/android-permissions.md)]

# <a name="ios"></a>[iOS](#tab/ios)

El archivo **Info.plist** de la aplicación debe contener la clave `NSLocationWhenInUseUsageDescription` para poder acceder a la ubicación del dispositivo.

Abra el editor de plist, agregue la propiedad **Privacidad: Ubicación cuando está en uso Descripción** y, después, complete un valor para mostrar al usuario.

También puede editar el archivo, agregar lo siguiente y actualizar el razonamiento:

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>Fill in a reason why your app needs access to location.</string>
```

# <a name="uwp"></a>[UWP](#tab/uwp)

Debe establecer el permiso `Location` para la aplicación. Para hacerlo, abra **Package.appxmanifest**, haga clic en la pestaña **Capacidades** y active **Ubicación**.

-----

## <a name="using-geolocation"></a>Uso de Geolocation

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

La API Geolocation también le pedirá permisos al usuario cuando sea necesario.

Puede obtener la última [ubicación](xref:Xamarin.Essentials.Location) conocida del dispositivo mediante una llamada al método `GetLastKnownLocationAsync`. A menudo, esto es más rápido que hacer una consulta completa, pero puede ser menos preciso y probablemente devuelva `null` si no existe ninguna ubicación en caché.

```csharp
try
{
    var location = await Geolocation.GetLastKnownLocationAsync();

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (FeatureNotEnabledException fneEx)
{
    // Handle not enabled on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to get location
}
```

La altitud no siempre está disponible. Si no lo está, es posible que la propiedad `Altitude` sea `null` o que el valor sea cero. Si lo está, el valor se expresa en metros sobre el nivel del mar.

Para consultar las coordenadas de [ubicación](xref:Xamarin.Essentials.Location) del dispositivo actual, se puede usar `GetLocationAsync`. Es mejor pasar un valor `GeolocationRequest` completo y `CancellationToken`, ya que se puede tardar algún tiempo en obtener la ubicación del dispositivo.

```csharp
try
{
    var request = new GeolocationRequest(GeolocationAccuracy.Medium);
    var location = await Geolocation.GetLocationAsync(request);

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (FeatureNotEnabledException fneEx)
{
    // Handle not enabled on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to get location
}
```

## <a name="geolocation-accuracy"></a>Precisión de la ubicación geográfica

En la tabla siguiente se describe la precisión por plataforma:

### <a name="lowest"></a>Mínima

| Plataforma | Distancia (en metros) |
| --- | --- |
| Android | 500 |
| iOS | 3000 |
| UWP | 1000 - 5000 |

### <a name="low"></a>Bajo

| Plataforma | Distancia (en metros) |
| --- | --- |
| Android | 500 |
| iOS | 1000 |
| UWP | 300 - 3000 |

### <a name="medium-default"></a>Media (valor predeterminado)

| Plataforma | Distancia (en metros) |
| --- | --- |
| Android | 100 - 500 |
| iOS | 100 |
| UWP | 30-500 |

### <a name="high"></a>Alto

| Plataforma | Distancia (en metros) |
| --- | --- |
| Android | 0 - 100 |
| iOS | 10 |
| UWP | <= 10 |

### <a name="best"></a>Óptima

| Plataforma | Distancia (en metros) |
| --- | --- |
| Android | 0 - 100 |
| iOS | ~0 |
| UWP | <= 10 |

<a name="calculate-distance" />

## <a name="detecting-mock-locations"></a>Detección de ubicaciones ficticias
Algunos dispositivos pueden devolver una ubicación ficticia desde el proveedor o desde una aplicación que proporciona ubicaciones ficticias. Puede detectarlo usando `IsFromMockProvider` en cualquier [`Location`](xref:Xamarin.Essentials.Location).

```csharp
var request = new GeolocationRequest(GeolocationAccuracy.Medium);
var location = await Geolocation.GetLocationAsync(request);

if (location != null)
{
    if(location.IsFromMockProvider)
    {
        // location is from a mock provider
    }
}
```

## <a name="distance-between-two-locations"></a>Distancia entre dos ubicaciones

Las clases [`Location`](xref:Xamarin.Essentials.Location) y [`LocationExtensions`](xref:Xamarin.Essentials.LocationExtensions) definen métodos `CalculateDistance` que permiten calcular la distancia entre dos ubicaciones geográficas. Esta distancia calculada no tiene en cuenta las carreteras ni otros caminos, y simplemente es la distancia más corta entre los dos puntos a lo largo de la superficie de la Tierra, lo que también se conoce como _distancia ortodrómica_ o coloquialmente, "distancia a vuelo de pájaro".

Por ejemplo:

```csharp
Location boston = new Location(42.358056, -71.063611);
Location sanFrancisco = new Location(37.783333, -122.416667);
double miles = Location.CalculateDistance(boston, sanFrancisco, DistanceUnits.Miles);
```

El constructor `Location` tiene argumentos de latitud y longitud en ese orden. Los valores de latitud positivos están al norte del Ecuador, y los valores de longitud positivos están al este del meridiano de Greenwich. Use el argumento final `CalculateDistance` para especificar millas o kilómetros. La clase `UnitConverters` también define los métodos `KilometersToMiles` y `MilesToKilometers` para la conversión entre las dos unidades.

## <a name="platform-differences"></a>Diferencias entre plataformas

La altitud se calcula de forma diferente en cada plataforma.

# <a name="android"></a>[Android](#tab/android)

En Android, la [altitud](https://developer.android.com/reference/android/location/Location#getAltitude()), si está disponible, se devuelve en metros por encima del elipsoide de referencia WGS 84. Si esta ubicación no tiene ninguna altitud, se devuelve 0.

# <a name="ios"></a>[iOS](#tab/ios)

En iOS, la [altitud](https://developer.apple.com/documentation/corelocation/cllocation/1423820-altitude) se mide en metros. Los valores positivos indican altitudes por encima del nivel del mar, mientras que los valores negativos indican altitudes por debajo.

# <a name="uwp"></a>[UWP](#tab/uwp)

En UWP, la altitud se devuelve en metros. Vea la documentación de [AltitudeReferenceSystem](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geopoint.altitudereferencesystem#Windows_Devices_Geolocation_Geopoint_AltitudeReferenceSystem) para obtener más información.

-----

## <a name="api"></a>API

- [Código fuente de Geolocation](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geolocation)
- [Documentación de API para Geolocation](xref:Xamarin.Essentials.Geolocation)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Geolocation-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
