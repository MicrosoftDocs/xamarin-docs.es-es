---
title: Servicios de ubicación en Android
description: En esta guía se presenta el reconocimiento de ubicación en las aplicaciones de Android y se muestra cómo obtener la ubicación del usuario mediante la API del servicio de ubicación de Android, así como el proveedor de ubicación fusionada disponible con Location Services API de Google.
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/22/2018
ms.openlocfilehash: 37bfed9a2b49be2d04f58dab70d3185418f0f67d
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457476"
---
# <a name="location-services-on-android"></a>Servicios de ubicación en Android

_En esta guía se presenta el reconocimiento de ubicación en las aplicaciones de Android y se muestra cómo obtener la ubicación del usuario mediante la API del servicio de ubicación de Android, así como el proveedor de ubicación fusionada disponible con Location Services API de Google._

Android proporciona acceso a varias tecnologías de ubicación, como la ubicación de la torre de telefonía, Wi-Fi y GPS. Los detalles de cada tecnología de ubicación se abstraen a través de *proveedores de ubicación*, lo que permite que las aplicaciones obtengan ubicaciones de la misma manera, independientemente del proveedor utilizado. En esta guía se presenta el proveedor de ubicación fusionada, una parte de Google Play Services, que determina de forma inteligente la mejor manera de obtener la ubicación de los dispositivos en función de qué proveedores están disponibles y cómo se usa el dispositivo. La API del servicio de ubicación de Android muestra cómo comunicarse con el servicio de ubicación del sistema mediante `LocationManager`. En la segunda parte de la guía se explora Location Services API para Android mediante `LocationManager`.

Como norma general, las aplicaciones prefieren usar el proveedor de ubicación fusionada, recurriendo a la antigua API del servicio de ubicación de Android solo cuando sea necesario.

## <a name="location-fundamentals"></a>Aspectos básicos de la ubicación

En Android, independientemente de la API que elija para trabajar con los datos de ubicación, varios conceptos siguen siendo los mismos. En esta sección se presentan los proveedores de ubicación y los permisos relacionados con la ubicación.

### <a name="location-providers"></a>Proveedores de ubicación

Se usan varias tecnologías internamente para situar con exactitud la ubicación del usuario. El hardware utilizado depende del tipo de *proveedor de ubicación* seleccionado para el trabajo de recopilación de datos. Android usa tres proveedores de ubicación:

- **Proveedor de GPS** &ndash; La tecnología GPS ofrece la ubicación más precisa, utiliza la mayor energía y funciona mejor en exteriores. Este proveedor usa una combinación de GPS y GPS asistido ([aGPS](https://en.wikipedia.org/wiki/Assisted_GPS)), que devuelve los datos GPS recopilados por las torres de telefonía.

- **Proveedor de red** &ndash; Proporciona una combinación de datos Wi-Fi y móviles, incluidos los datos de GPS recopilados por las torres de telefonía. Utiliza menos energía que el proveedor de GPS, pero devuelve los datos de ubicación con una precisión variable.

- **Proveedor pasivo** &ndash; Una opción "intermedia" mediante proveedores solicitados por otras aplicaciones o servicios para generar datos de ubicación en una aplicación. Esta es una opción menos confiable pero con más ahorro de energía, ideal para aplicaciones que no requieren que funcionen las actualizaciones de ubicación constantes.

Los proveedores de ubicación no están siempre disponibles. Por ejemplo, es posible que queramos usar GPS para nuestra aplicación, pero esta tecnología podría estar desactivada en la configuración o el dispositivo podría no contar con ella. Si un proveedor específico no está disponible, la elección de ese proveedor podría devolver `null`.

### <a name="location-permissions"></a>Permisos de ubicación

Una aplicación con reconocimiento de ubicación necesita acceder a los sensores de hardware de un dispositivo para recibir datos de GPS, Wi-Fi y móviles. El acceso se controla a través de los permisos adecuados en el manifiesto de Android de la aplicación.
Hay dos permisos disponibles. En función de los requisitos de la aplicación y de la opción de API que elija, deberá permitir uno:

- `ACCESS_FINE_LOCATION` &ndash; Permite a una aplicación tener acceso a GPS.
    Necesario para las opciones *Proveedor de GPS* y *Proveedor pasivo* (*Proveedor pasivo necesita permiso para acceder a los datos de GPS recopilados por otra aplicación o servicio*). Permiso opcional para el *proveedor de red*.

- `ACCESS_COARSE_LOCATION` &ndash; Permite el acceso de una aplicación a la ubicación de red de telefonía móvil y Wi-Fi. Se requiere para el *proveedor de red* si no se establece `ACCESS_FINE_LOCATION`.

En el caso de las aplicaciones que tienen como destino la versión 21 de la API (Android 5.0 Lollipop) o superior, puede habilitar `ACCESS_FINE_LOCATION` y seguir ejecutándose en dispositivos que no tienen hardware GPS. Si la aplicación requiere hardware GPS, debe agregar explícitamente un elemento `android.hardware.location.gps` `uses-feature` al manifiesto de Android. Para más información, consulte la referencia del elemento [uses-reference](https://developer.android.com/guide/topics/manifest/uses-feature-element.html) de Android.

Para establecer los permisos, expanda la carpeta **Propiedades** en el **Panel de solución** y haga doble clic en **AndroidManifest.xml**. Los permisos se enumerarán en **Permisos necesarios**:

[![Captura de pantalla de la configuración de permisos necesarios del manifiesto de Android](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

El establecimiento de cualquiera de estos permisos indica a Android que la aplicación necesita permiso del usuario para acceder a los proveedores de ubicación. Los dispositivos que ejecutan el nivel 22 de la API (Android 5.1) o inferior le pedirán al usuario que conceda estos permisos cada vez que se instale la aplicación. En los dispositivos que ejecutan el nivel 23 de la API (Android 6.0) o superior, la aplicación debe realizar una comprobación de permisos en tiempo de ejecución antes de realizar una solicitud del proveedor de ubicación. 

> [!NOTE]
>Nota: La configuración de `ACCESS_FINE_LOCATION` implica acceder a los datos generales y específicos de ubicación. Nunca debe tener que establecer ambos permisos, solo el permiso *mínimo* que la aplicación requiere para que funcione.

Este fragmento de código es un ejemplo de cómo comprobar que una aplicación tiene consentimiento para el permiso `ACCESS_FINE_LOCATION`:

```csharp
 if (ContextCompat.CheckSelfPermission(this, Manifest.Permission.AccessFineLocation) == Permission.Granted)
{
    StartRequestingLocationUpdates();
    isRequestingLocationUpdates = true;
}
else
{
    // The app does not have permission ACCESS_FINE_LOCATION 
}
```

Las aplicaciones deben ser tolerantes con el escenario en el que el usuario no conceda el permiso (o lo haya revocado) y tener una manera de abordar esa situación correctamente. Consulte la [guía de permisos](~/android/app-fundamentals/permissions.md) para más detalles sobre cómo implementar las comprobaciones de permisos en tiempo de ejecución en Xamarin.Android.

## <a name="using-the-fused-location-provider"></a>Uso del proveedor de ubicación fusionada

El proveedor de ubicación fusionada es el método preferido para que las aplicaciones de Android reciban actualizaciones de ubicación del dispositivo porque seleccionará de forma eficaz el proveedor de ubicación durante el tiempo de ejecución para proporcionar la mejor información de ubicación de manera eficiente. Por ejemplo, un usuario que camina al aire libre obtiene la mejor ubicación de lectura con GPS. Si, a continuación, el usuario accede a interiores, donde el GPS funciona de forma deficiente (o directamente no funciona), el proveedor de ubicación fusionada puede cambiar automáticamente a Wi-Fi, que funciona mejor en interiores.

La API del proveedor de ubicación fusionada proporciona numerosas herramientas para potenciar las aplicaciones que reconocen la ubicación, incluidas la geovalla y la supervisión de actividades. En esta sección, nos centraremos en los aspectos básicos de la configuración de `LocationClient`, el establecimiento de proveedores y la obtención de la ubicación del usuario.

El proveedor de ubicación fusionada forma parte de [Google Play Services](https://developer.android.com/google/play-services/index.html).
El paquete de Google Play Services debe instalarse y configurarse correctamente en la aplicación para que la API del proveedor de ubicación fusionada funcione, y el dispositivo debe tener instalado el APK de Google Play Services.

Antes de que una aplicación de Xamarin.Android pueda usar el proveedor de ubicación fusionada, debe agregar el paquete **Xamarin.GooglePlayServices.Location** al proyecto. Además, se deben agregar las siguientes instrucciones de `using` a cualquier archivo de código fuente que haga referencia a las clases que se describen a continuación:

```csharp
using Android.Gms.Common;
using Android.Gms.Location;
```

### <a name="checking-if-google-play-services-is-installed"></a>Comprobación de si Google Play Services está instalado

Una instancia de Xamarin.Android se bloqueará si intenta usar el proveedor de ubicación fusionada cuando Google Play Services no está instalado (o no está actualizado); de lo contrario, se produciría una excepción en tiempo de ejecución.  Si Google Play Services no está instalado, la aplicación debería recurrir al servicio de ubicación de Android descrito anteriormente. Si Google Play Services no está actualizado, la aplicación podría mostrar un mensaje al usuario pidiéndole que actualice la versión instalada de Google Play Services.

Este fragmento de código es un ejemplo de cómo una actividad de Android puede comprobar mediante programación si se ha instalado Google Play Services:

```csharp
bool IsGooglePlayServicesInstalled()
{
    var queryResult = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
    if (queryResult == ConnectionResult.Success)
    {
        Log.Info("MainActivity", "Google Play Services is installed on this device.");
        return true;
    }

    if (GoogleApiAvailability.Instance.IsUserResolvableError(queryResult))
    {
        // Check if there is a way the user can resolve the issue
        var errorString = GoogleApiAvailability.Instance.GetErrorString(queryResult);
        Log.Error("MainActivity", "There is a problem with Google Play Services on this device: {0} - {1}",
                  queryResult, errorString);

        // Alternately, display the error to the user.
    }

    return false;
}
```

### <a name="fusedlocationproviderclient"></a>FusedLocationProviderClient

Para interactuar con el proveedor de ubicación fusionada, una aplicación de Xamarin.Android debe tener una instancia de `FusedLocationProviderClient`. Esta clase expone los métodos necesarios para suscribirse a las actualizaciones de ubicación y recuperar la última ubicación conocida del dispositivo.

El método `OnCreate` de una actividad es un lugar adecuado para obtener una referencia a `FusedLocationProviderClient`, tal y como se muestra en el siguiente fragmento de código:

```csharp
public class MainActivity: AppCompatActivity
{
    FusedLocationProviderClient fusedLocationProviderClient;

    protected override void OnCreate(Bundle bundle) 
    {
        fusedLocationProviderClient = LocationServices.GetFusedLocationProviderClient(this);
    }
}
```

### <a name="getting-the-last-known-location"></a>Obtención de la última ubicación conocida

El método `FusedLocationProviderClient.GetLastLocationAsync()` proporciona una manera sencilla y sin bloqueo para que una aplicación de Xamarin.Android obtenga la última ubicación conocida del dispositivo rápidamente con una sobrecarga de codificación mínima.

Este fragmento de código muestra cómo usar el método `GetLastLocationAsync` para recuperar la ubicación del dispositivo:

```csharp
async Task GetLastLocationFromDevice()
{
    // This method assumes that the necessary run-time permission checks have succeeded.
    getLastLocationButton.SetText(Resource.String.getting_last_location);
    Android.Locations.Location location = await fusedLocationProviderClient.GetLastLocationAsync();

    if (location == null)
    {
        // Seldom happens, but should code that handles this scenario
    }
    else
    {
        // Do something with the location 
        Log.Debug("Sample", "The latitude is " + location.Latitude);
    }
}
```

### <a name="subscribing-to-location-updates"></a>Suscripción a actualizaciones de ubicación

Una aplicación de Xamarin.Android también puede suscribirse a las actualizaciones de ubicación del proveedor de ubicación fusionada mediante el método `FusedLocationProviderClient.RequestLocationUpdatesAsync`, como se muestra en este fragmento de código:

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```

Este método toma dos parámetros:

- **`Android.Gms.Location.LocationRequest`** &ndash; Un objeto `LocationRequest` es el modo en que una aplicación de Xamarin.Android pasa los parámetros sobre cómo debería funcionar el proveedor de ubicación fusionada. `LocationRequest` contiene información como la frecuencia con la que se deben realizar las solicitudes o la importancia de una actualización de ubicación precisa. Por ejemplo, una solicitud de ubicación importante hará que el dispositivo use el GPS y, por consiguiente, más energía, a la hora de determinar la ubicación. Este fragmento de código muestra cómo crear `LocationRequest` para una ubicación con una precisión alta, buscando cada cinco minutos aproximadamente una actualización de ubicación (pero no antes de dos minutos entre solicitudes). El proveedor de ubicación fusionada utilizará `LocationRequest` como guía para qué proveedor de ubicación usar cuando intente determinar la ubicación del dispositivo:

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```

- **`Android.Gms.Location.LocationCallback`** &ndash; Para recibir actualizaciones de ubicación, una aplicación de Xamarin.Android debe establecer como subclase la clase abstracta `LocationProvider`. Esta clase expone dos métodos que es posible que el proveedor de ubicación con fusibles invoque para actualizar la aplicación con información de ubicación. Esto se tratará con más detalle a continuación.

Para notificar a una aplicación de Xamarin.Android de una actualización de ubicación, el proveedor de ubicación fusionada invocará a `LocationCallBack.OnLocationResult(LocationResult result)`. El parámetro `Android.Gms.Location.LocationResult` contendrá la información de ubicación de actualización.

Cuando el proveedor de ubicación fusionada detecte un cambio en la disponibilidad de los datos de ubicación, llamará al método `LocationProvider.OnLocationAvailability(LocationAvailability
locationAvailability)`. Si la propiedad `LocationAvailability.IsLocationAvailable` devuelve `true`, se puede suponer que los resultados de ubicación del dispositivo que `OnLocationResult` notifica son tan precisos y tan actualizados como `LocationRequest` requiere. Si `IsLocationAvailable` es false, `OnLocationResult` no devolverá ningún resultado de ubicación.

Este fragmento de código es una implementación de ejemplo del objeto `LocationCallback`:

```csharp
public class FusedLocationProviderCallback : LocationCallback
{
    readonly MainActivity activity;

    public FusedLocationProviderCallback(MainActivity activity)
    {
        this.activity = activity;
    }

    public override void OnLocationAvailability(LocationAvailability locationAvailability)
    {
        Log.Debug("FusedLocationProviderSample", "IsLocationAvailable: {0}",locationAvailability.IsLocationAvailable);
    }

    public override void OnLocationResult(LocationResult result)
    {
        if (result.Locations.Any())
        {
            var location = result.Locations.First();
            Log.Debug("Sample", "The latitude is :" + location.Latitude);
        }
        else
        {
            // No locations to work with.
        }
    }
}
```

## <a name="using-the-android-location-service-api"></a>Uso de la API del servicio de ubicación de Android

El servicio de ubicación de Android es una API antigua para usar información de ubicación en Android. Los sensores de hardware recopilan los datos de ubicación que van a parar a un servicio de sistema al que se accede en la aplicación con una clase `LocationManager` y `ILocationListener`.

El servicio de ubicación es más adecuado para las aplicaciones que se deben ejecutar en dispositivos que no tienen Google Play Services instalado.

El servicio de ubicación es un tipo especial de [servicio](https://developer.android.com/guide/components/services.html) administrado por el sistema. Un servicio del sistema interactúa con el hardware del dispositivo y siempre se está ejecutando. Para acceder a las actualizaciones de ubicación en nuestra aplicación, nos suscribiremos a las actualizaciones de ubicación del servicio de ubicación del sistema mediante `LocationManager` y una llamada `RequestLocationUpdates`.

Para obtener la ubicación del usuario mediante el servicio de ubicación de Android, debe realizar varios pasos:

1. Obtenga una referencia al servicio `LocationManager`.
2. Implemente la interfaz `ILocationListener` y controle los eventos cuando cambie la ubicación.
3. Use `LocationManager` para solicitar actualizaciones de ubicación para un proveedor especificado. La interfaz `ILocationListener` del paso anterior se utilizará para recibir devoluciones de llamada de `LocationManager`.
4. Detenga las actualizaciones de ubicación cuando la aplicación ya no sea adecuada para recibir actualizaciones.

### <a name="location-manager"></a>Administrador de ubicación

Se puede acceder al servicio de ubicación del sistema con una instancia de la clase `LocationManager`. `LocationManager` es una clase especial que nos permite interactuar con el servicio de ubicación del sistema y llamar a métodos en él. Una aplicación puede obtener una referencia a `LocationManager` llamando a `GetSystemService` y pasando un tipo de servicio, como se muestra a continuación:

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate` es un buen lugar para obtener una referencia a `LocationManager`.
Es conveniente mantener `LocationManager` como una variable de clase, de modo que podamos llamarlo en varios puntos del ciclo de vida de la actividad.

### <a name="request-location-updates-from-the-locationmanager"></a>Solicitud de actualizaciones de ubicación desde LocationManager

Una vez que la aplicación tiene una referencia a `LocationManager`, debe indicar a `LocationManager` qué tipo de información de ubicación se requiere y con qué frecuencia se actualizará dicha información. Para ello, llame a `RequestLocationUpdates` en el objeto `LocationManager` y pase algunos criterios para las actualizaciones y una devolución de llamada que recibirá las actualizaciones de ubicación. Esta devolución de llamada es un tipo que debe implementar la interfaz `ILocationListener` (que se describe con más detalle más adelante en esta guía).

El método `RequestLocationUpdates` indica al servicio de ubicación del sistema que la aplicación desearía empezar a recibir actualizaciones de ubicación. Este método permite especificar el proveedor, así como los umbrales de tiempo y distancia para controlar la frecuencia de actualización. Por ejemplo, el método siguiente solicita actualizaciones de ubicación del proveedor de ubicación de GPS cada 2000 milisegundos y solo cuando la ubicación cambia más de un metro:

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

Una aplicación debe solicitar actualizaciones de ubicación solo con la frecuencia necesaria para que la aplicación funcione correctamente. De esta manera se conserva la duración de la batería y se crea una mejor experiencia para el usuario.

### <a name="responding-to-updates-from-the-locationmanager"></a>Respuesta a las actualizaciones de LocationManager

Una vez que una aplicación haya solicitado actualizaciones desde `LocationManager`, puede recibir información del servicio implementando la interfaz [`ILocationListener`](xref:Android.Locations.ILocationListener). Esta interfaz proporciona cuatro métodos para escuchar el servicio de ubicación y el proveedor de ubicación, `OnLocationChanged`. El sistema llamará a `OnLocationChanged` cuando la ubicación del usuario cambie lo suficiente como para calificarse como un cambio de ubicación según los criterios establecidos al solicitar actualizaciones de ubicación. 

En el código siguiente se muestran los métodos de la interfaz `ILocationListener`:

```csharp
public class MainActivity : AppCompatActivity, ILocationListener
{
    TextView latitude;
    TextView longitude;
    
    public void OnLocationChanged (Location location)
    {
        // called when the location has been updated.
    }
    
    public OnProviderDisabled(string locationProvider)
    {
        // called when the user disables the provider
    }
    
    public OnProviderEnabled(string locationProvider)
    {
        // called when the user enables the provider
    }
    
    public OnStatusChanged(string locationProvider, Availability status, Bundle extras)
    {
        // called when the status of the provider changes (there are a variety of reasons for this)
    }
}
```

### <a name="unsubscribing-to-locationmanager-updates"></a>Cancelar la suscripción a las actualizaciones de LocationManager

Para conservar los recursos del sistema, una aplicación debe cancelar la suscripción a las actualizaciones de ubicación lo antes posible. El método `RemoveUpdates` indica a `LocationManager` que deje de enviar actualizaciones a nuestra aplicación.  Por ejemplo, una actividad puede llamar a `RemoveUpdates` en el método `OnPause` para que podamos ahorrar energía si una aplicación no necesita actualizaciones de ubicación mientras su actividad no está en la pantalla:

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

Si la aplicación necesita obtener actualizaciones de ubicación en segundo plano, será interesante crear un servicio personalizado que se suscriba al servicio de ubicación del sistema. Para más información, consulte la guía [Creación de servicios de Android](~/android/app-fundamentals/services/index.md).

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>Determinación del mejor proveedor de ubicación para LocationManager

La aplicación anterior establece GPS como el proveedor de ubicación. Sin embargo, es posible que GPS no esté disponible en todos los casos, por ejemplo, si el dispositivo se encuentra en interiores o no tiene un receptor de GPS. Si es así, el resultado devuelto es `null` para el proveedor.

Para que la aplicación funcione cuando GPS no está disponible, use el método `GetBestProvider` para solicitar el mejor proveedor de ubicación disponible (compatible con el dispositivo y habilitado para el usuario) al iniciarse la aplicación. En lugar de pasar un proveedor específico, puede indicar a `GetBestProvider` los requisitos del proveedor, como la precisión y el consumo, con un [objeto `Criteria`](xref:Android.Locations.Criteria). `GetBestProvider` devuelve el mejor proveedor para los criterios especificados.

En el código siguiente se muestra cómo obtener el mejor proveedor disponible y usarlo al solicitar actualizaciones de ubicación:

```csharp
Criteria locationCriteria = new Criteria();   
locationCriteria.Accuracy = Accuracy.Coarse;
locationCriteria.PowerRequirement = Power.Medium;

locationProvider = locationManager.GetBestProvider(locationCriteria, true);

if(locationProvider != null)
{
    locationManager.RequestLocationUpdates (locationProvider, 2000, 1, this);
}
else
{
    Log.Info(tag, "No location providers available");
}
```

> [!NOTE]
> Si el usuario ha deshabilitado todos los proveedores de ubicación, `GetBestProvider` devolverá `null`. Para ver cómo funciona este código en un dispositivo real, asegúrese de habilitar las redes GPS, Wi-Fi y de telefonía móvil en **Ajustes de Google > Ubicación > Modo** como se muestra en esta captura de pantalla:
>
> [![Pantalla de modo de ubicación de ajustes en un teléfono Android](location-images/location-02.png)](location-images/location-02.png#lightbox)
>
> En la captura de pantalla siguiente se muestra la aplicación de ubicación que se ejecuta con `GetBestProvider`:
>
> [![Aplicación GetBestProvider que muestra la latitud, la longitud y el proveedor](location-images/location-03.png)](location-images/location-03.png#lightbox)
>
> Tenga en cuenta que `GetBestProvider` no cambia el proveedor dinámicamente. En su lugar, determina el mejor proveedor disponible una vez durante el ciclo de vida de la actividad. Si el estado del proveedor cambia una vez establecido, la aplicación necesitará código adicional en los métodos `ILocationListener` (`OnProviderEnabled`, `OnProviderDisabled`y `OnStatusChanged`) para controlar todas las posibilidades relacionadas con el modificador del proveedor.

## <a name="summary"></a>Resumen

En esta guía se ha tratado la obtención de la ubicación del usuario mediante el servicio de ubicación de Android y el proveedor de ubicación fusionada de Location Services API de Google.

## <a name="related-links"></a>Vínculos relacionados

- [Ubicación (ejemplo)](/samples/xamarin/monodroid-samples/location)
- [FusedLocationProvider (ejemplo)](/samples/xamarin/monodroid-samples/fusedlocationprovider)
- [Google Play Services](https://developer.android.com/google/play-services/index.html)
- [Clase Criteria](xref:Android.Locations.Criteria)
- [Clase LocationManager](xref:Android.Locations.LocationManager)
- [Clase LocationListener](xref:Android.Locations.ILocationListener)
- [API LocationClient](https://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [API LocationListener](https://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [API LocationRequest](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)