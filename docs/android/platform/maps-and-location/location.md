---
title: Servicios de ubicación en Android
description: En esta guía se presenta el reconocimiento de ubicación en las aplicaciones de Android y se muestra cómo obtener la ubicación del usuario mediante la API del servicio de ubicación de Android, así como el proveedor de ubicaciones con fusibles disponible con Google Location Services API.
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/22/2018
ms.openlocfilehash: 61532eb1e31db6a862275180394b2b5ba9b05f8e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761728"
---
# <a name="location-services-on-android"></a>Servicios de ubicación en Android

_En esta guía se presenta el reconocimiento de ubicación en las aplicaciones de Android y se muestra cómo obtener la ubicación del usuario mediante la API del servicio de ubicación de Android, así como el proveedor de ubicaciones con fusibles disponible con Google Location Services API._

Android proporciona acceso a varias tecnologías de ubicación como la ubicación de la torre de celda, Wi-Fi y GPS. Los detalles de cada tecnología de ubicación se abstraen a través de los *proveedores de ubicación*, lo que permite que las aplicaciones obtengan ubicaciones de la misma manera, independientemente del proveedor que se use. En esta guía se presenta el proveedor de ubicación con fusibles, una parte de la Google Play Services, que determina de forma inteligente la mejor manera de obtener la ubicación de los dispositivos en función de qué proveedores están disponibles y cómo se usa el dispositivo. API del servicio de ubicación de Android y muestra cómo comunicarse con el servicio de ubicación `LocationManager`del sistema mediante. La segunda parte de la guía explora el Location Services API Android mediante `LocationManager`.

Como norma general, las aplicaciones prefieren usar el proveedor de ubicación con fusibles, revirtiendo la antigua API del servicio de ubicación de Android solo cuando sea necesario.

## <a name="location-fundamentals"></a>Aspectos básicos de la ubicación

En Android, independientemente de la API que elija para trabajar con los datos de ubicación, varios conceptos siguen siendo los mismos. En esta sección se presentan los proveedores de ubicación y los permisos relacionados con la ubicación.

### <a name="location-providers"></a>Proveedores de ubicación

Se usan varias tecnologías internamente para identificar la ubicación del usuario. El hardware utilizado depende del tipo de *proveedor de ubicación* seleccionado para el trabajo de recopilación de datos. Android usa tres proveedores de Ubicación:

- **Proveedor de GPS** &ndash; GPS ofrece la ubicación más precisa, usa la mayor potencia y funciona mejor en el exterior. Este proveedor usa una combinación de GPS y GPS asistido ([AGPS](https://en.wikipedia.org/wiki/Assisted_GPS)), que devuelve los datos de GPS recopilados por las torres de telefonía móvil.

- **Proveedor de red** &ndash; Proporciona una combinación de datos de Wi-Fi y móviles, incluidos los datos de AGPS recopilados por los Torres de celdas. Utiliza menos energía que el proveedor de GPS, pero devuelve datos de ubicación de precisión variable.

- **Proveedor pasivo** &ndash; Una opción "superpuesta" mediante proveedores solicitados por otras aplicaciones o servicios para generar datos de ubicación en una aplicación. Esta es una opción menos confiable pero de ahorro de energía ideal para aplicaciones que no requieren que funcionen las actualizaciones de ubicación constantes.

Los proveedores de ubicación no están siempre disponibles. Por ejemplo, es posible que quieramos usar GPS para nuestra aplicación, pero GPS podría estar desactivado en la configuración o el dispositivo podría no tener GPS. Si un proveedor específico no está disponible, la elección del proveedor podría `null`devolver.

### <a name="location-permissions"></a>Permisos de ubicación

Una aplicación con reconocimiento de ubicación necesita tener acceso a los sensores de hardware de un dispositivo para recibir datos de GPS, Wi-Fi y móviles. El acceso se controla a través de los permisos adecuados en el manifiesto de Android de la aplicación.
Hay dos permisos disponibles &ndash; en función de los requisitos de la aplicación y de la elección de la API, por lo que querrá permitir uno:

- `ACCESS_FINE_LOCATION`&ndash; Permite a una aplicación tener acceso a GPS.
    Necesario para las opciones *proveedor de GPS* y *proveedor pasivo* (el*proveedor pasivo necesita permiso para acceder a los datos de GPS recopilados por otra aplicación o servicio*). Permiso opcional para el *proveedor de red*.

- `ACCESS_COARSE_LOCATION`&ndash; Permite el acceso de una aplicación a la ubicación de red de telefonía móvil y Wi-Fi. Requerido para el proveedor de `ACCESS_FINE_LOCATION` *red* si no se ha establecido.

En el caso de las aplicaciones que tienen como destino la versión 21 de la API (Android `ACCESS_FINE_LOCATION` 5,0 Lollipop) o superior, puede habilitar y seguir ejecutando en dispositivos que no tienen hardware GPS. Si su aplicación requiere hardware GPS, debe agregar explícitamente un `android.hardware.location.gps` `uses-feature` elemento al manifiesto de Android. Para obtener más información, consulte la referencia de elementos [de características de uso de](https://developer.android.com/guide/topics/manifest/uses-feature-element.html) Android.

Para establecer los permisos, expanda la carpeta **propiedades** en el **Panel de solución** y haga doble clic en **archivo AndroidManifest. XML**. Los permisos se enumerarán en **permisos necesarios**:

[![Captura de pantalla de la configuración de permisos necesarios del manifiesto de Android](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

Establecer cualquiera de estos permisos indica a Android que la aplicación necesita permiso del usuario para acceder a los proveedores de ubicación. Los dispositivos que ejecutan el nivel de API 22 (Android 5,1) o inferior le pedirán al usuario que conceda estos permisos cada vez que se instale la aplicación. En los dispositivos que ejecutan el nivel de API 23 (Android 6,0) o superior, la aplicación debe realizar una comprobación de permisos en tiempo de ejecución antes de realizar una solicitud del proveedor de ubicación. 

> [!NOTE]
>Nota: La `ACCESS_FINE_LOCATION` configuración implica el acceso a los datos de ubicación aproximados y finos. Nunca debe tener que establecer ambos permisos, solo el permiso *mínimo* que requiere la aplicación para que funcione.

Este fragmento de código es un ejemplo de cómo comprobar que una aplicación tiene permiso para `ACCESS_FINE_LOCATION` el permiso:

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

Las aplicaciones deben ser tolerantes del escenario en el que el usuario no conceda el permiso (o que haya revocado el permiso) y tenga una manera de solucionar correctamente esa situación. Consulte la [Guía de permisos](~/android/app-fundamentals/permissions.md) para obtener más detalles sobre cómo implementar comprobaciones de permisos en tiempo de ejecución en Xamarin. Android.

## <a name="using-the-fused-location-provider"></a>Usar el proveedor de ubicaciones con fusibles

El proveedor de ubicaciones fusionadas es el método preferido para que las aplicaciones de Android reciban actualizaciones de ubicación del dispositivo, ya que de forma eficaz seleccionará el proveedor de ubicación durante el tiempo de ejecución para proporcionar la mejor información de ubicación de manera eficaz. Por ejemplo, un usuario que recorre el exterior obtiene la mejor ubicación de lectura con GPS. Si, a continuación, el usuario se dirige a las puertas, donde el GPS funciona de forma deficiente (si se hace en absoluto), el proveedor de ubicación con fusibles puede cambiar automáticamente a Wi-Fi, lo que funciona mejor en el interior.

La API del proveedor de ubicación con fusibles proporciona una variedad de otras herramientas para proporcionar a las aplicaciones compatibles con la ubicación, incluidas la geovalla y la supervisión de actividades. En esta sección, nos centraremos en los aspectos básicos de la configuración de, el `LocationClient`establecimiento de proveedores y la obtención de la ubicación del usuario.

El proveedor de ubicaciones con fusibles forma parte de [Google Play Services](https://developer.android.com/google/play-services/index.html).
El paquete de Google Play Services debe instalarse y configurarse correctamente en la aplicación para que funcione la API del proveedor de ubicación con fusibles, y el dispositivo debe tener instalado el Google Play Services APK.

Antes de que una aplicación de Xamarin. Android pueda usar el proveedor de ubicaciones con fusibles, debe agregar el paquete **Xamarin. GooglePlayServices. Maps** al proyecto. Además, se deben agregar `using` las siguientes instrucciones a cualquier archivo de código fuente que haga referencia a las clases que se describen a continuación:

```csharp
using Android.Gms.Common;
using Android.Gms.Location;
```

### <a name="checking-if-google-play-services-is-installed"></a>Comprobando si Google Play Services está instalado

Un Xamarin. Android se bloqueará si intenta usar el proveedor de ubicación con fusibles Cuando Google Play Services no está instalado (o no está actualizado), de lo contrario, se produciría una excepción en tiempo de ejecución.  Si Google Play Services no está instalado, la aplicación debería revertir al servicio de ubicación de Android descrito anteriormente. Si Google Play Services no está actualizado, la aplicación podría mostrar un mensaje al usuario pidiéndole que actualice la versión instalada de Google Play Services.

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

Para interactuar con el proveedor de ubicaciones con fusibles, una aplicación de Xamarin. Android debe tener `FusedLocationProviderClient`una instancia de. Esta clase expone los métodos necesarios para suscribirse a las actualizaciones de ubicación y recuperar la última ubicación conocida del dispositivo.

El `OnCreate` método de una actividad es un lugar adecuado para obtener una referencia `FusedLocationProviderClient`a, tal y como se muestra en el siguiente fragmento de código:

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

### <a name="getting-the-last-known-location"></a>Obtener la última ubicación conocida

El `FusedLocationProviderClient.GetLastLocationAsync()` método proporciona una manera sencilla y sin bloqueo para que una aplicación de Xamarin. Android obtenga rápidamente la última ubicación conocida del dispositivo con una sobrecarga de codificación mínima.

Este fragmento de código muestra cómo usar `GetLastLocationAsync` el método para recuperar la ubicación del dispositivo:

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

Una aplicación de Xamarin. Android también puede suscribirse a las actualizaciones de ubicación del proveedor de `FusedLocationProviderClient.RequestLocationUpdatesAsync` ubicaciones fusionadas mediante el método, tal como se muestra en este fragmento de código:

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```

Este método toma dos parámetros:

- **`Android.Gms.Location.LocationRequest`** &ndash; Un`LocationRequest` objeto es la forma en que una aplicación de Xamarin. Android pasa los parámetros en la forma en que el proveedor de la ubicación fusionada debe funcionar. `LocationRequest` Contiene información como la frecuencia con que se deben realizar las solicitudes o la importancia de una actualización de ubicación precisa. Por ejemplo, una solicitud de ubicación importante hará que el dispositivo use el GPS y, por consiguiente, más potencia, al determinar la ubicación. Este fragmento de código muestra cómo crear un `LocationRequest` para una ubicación con una precisión alta, comprobando aproximadamente cada cinco minutos para una actualización de ubicación (pero no antes de dos minutos entre solicitudes). El proveedor `LocationRequest` de ubicaciones con fusibles usará como guía el proveedor de ubicación que se va a usar al intentar determinar la ubicación del dispositivo:

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```

- **`Android.Gms.Location.LocationCallback`** Para recibir actualizaciones de ubicación, una aplicación de Xamarin. Android debe subclase de `LocationProvider` la clase abstracta. &ndash; Esta clase expone dos métodos que es posible que el proveedor de ubicación con fusibles invoque para actualizar la aplicación con información de ubicación. Esto se tratará con más detalle a continuación.

Para notificar a una aplicación de Xamarin. Android de una actualización de ubicación, el proveedor de `LocationCallBack.OnLocationResult(LocationResult result)`ubicación con fusible invocará el. El `Android.Gms.Location.LocationResult` parámetro contendrá la información de la ubicación de actualización.

Cuando el proveedor de ubicación con fusible detecta un cambio en la disponibilidad de los datos de ubicación, llamará al `LocationProvider.OnLocationAvailability(LocationAvailability
locationAvailability)` método. Si la `LocationAvailability.IsLocationAvailable` propiedad devuelve `true`, se puede suponer que los resultados de ubicación del dispositivo notificados por `OnLocationResult` son tan precisos y actualizados según sea necesario `LocationRequest`. Si `IsLocationAvailable` es false, no se devolverá ningún resultado de `OnLocationResult`la ubicación.

Este fragmento de código es una implementación de ejemplo `LocationCallback` del objeto:

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

## <a name="using-the-android-location-service-api"></a>Uso de la API de servicio de ubicación de Android

El servicio de ubicación de Android es una API anterior para usar información de ubicación en Android. Los sensores de hardware recopilan los datos de ubicación y los recopila un servicio de sistema, al que se tiene acceso `LocationManager` en la aplicación `ILocationListener`con una clase y un.

El servicio de ubicación es más adecuado para las aplicaciones que se deben ejecutar en dispositivos que no tienen Google Play Services instalado.

Location Service es un tipo especial de [servicio](https://developer.android.com/guide/components/services.html) administrado por el sistema. Un servicio del sistema interactúa con el hardware del dispositivo y siempre se está ejecutando. Para aprovechar las actualizaciones de ubicación en nuestra aplicación, suscribiremos a las actualizaciones de ubicación del servicio de ubicación del `LocationManager` sistema mediante `RequestLocationUpdates` una llamada a y.

Para obtener la ubicación del usuario mediante el servicio de ubicación de Android, debe realizar varios pasos:

1. Obtiene una referencia al `LocationManager` servicio.
2. Implemente `ILocationListener` la interfaz y controle los eventos cuando cambie la ubicación.
3. `LocationManager` Utilice para solicitar actualizaciones de ubicación para un proveedor especificado. El `ILocationListener` del paso anterior se utilizará para recibir devoluciones `LocationManager`de llamada de.
4. Detener las actualizaciones de ubicación cuando la aplicación ya no sea adecuada para recibir actualizaciones.

### <a name="location-manager"></a>Administrador de ubicación

Se puede tener acceso al servicio de ubicación del sistema con una `LocationManager` instancia de la clase. `LocationManager`es una clase especial que nos permite interactuar con el servicio de ubicación del sistema y llamar a métodos en él. Una aplicación puede obtener una referencia a llamando `LocationManager` a `GetSystemService` y pasando un tipo de servicio, como se muestra a continuación:

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate`es un buen lugar para obtener una referencia a `LocationManager`.
Se recomienda mantener `LocationManager` como una variable de clase, de modo que podamos llamarlo en varios puntos del ciclo de vida de la actividad.

### <a name="request-location-updates-from-the-locationmanager"></a>Actualización de la ubicación de la solicitud desde LocationManager

Una vez que la aplicación tiene una referencia `LocationManager`a, debe indicar a qué `LocationManager` tipo de información de ubicación se requiera y con qué frecuencia se actualizará la información. Para ello, llame `RequestLocationUpdates` a en `LocationManager` el objeto y pase algunos criterios para las actualizaciones y una devolución de llamada que recibirá las actualizaciones de la ubicación. Esta devolución de llamada es un tipo que debe `ILocationListener` implementar la interfaz (se describe con más detalle más adelante en esta guía).

El `RequestLocationUpdates` método indica al servicio de ubicación del sistema que la aplicación desea empezar a recibir actualizaciones de ubicación. Este método permite especificar el proveedor, así como los umbrales de tiempo y distancia para controlar la frecuencia de actualización. Por ejemplo, el método siguiente solicita actualizaciones de ubicación del proveedor de ubicación de GPS cada 2000 milisegundos y solo cuando la ubicación cambia más de 1 metro:

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

Una aplicación debe solicitar actualizaciones de ubicación solo con la frecuencia necesaria para que la aplicación funcione correctamente. Esto conserva la duración de la batería y crea una mejor experiencia para el usuario.

### <a name="responding-to-updates-from-the-locationmanager"></a>Responder a las actualizaciones de LocationManager

Una vez que una aplicación ha solicitado actualizaciones `LocationManager`desde el, puede recibir información del servicio implementando la [`ILocationListener`](xref:Android.Locations.ILocationListener) interfaz. Esta interfaz proporciona cuatro métodos para escuchar el servicio de ubicación y el proveedor de ubicación `OnLocationChanged`,. El sistema llamará `OnLocationChanged` cuando la ubicación del usuario cambie lo suficiente como para calificarse como un cambio de ubicación según los criterios establecidos al solicitar actualizaciones de ubicación. 

En el código siguiente se muestran los métodos `ILocationListener` de la interfaz:

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

Para conservar los recursos del sistema, una aplicación debe cancelar la suscripción a las actualizaciones de ubicación lo antes posible. El `RemoveUpdates` método indica a `LocationManager` que detenga el envío de actualizaciones a la aplicación.  Por ejemplo, una actividad puede llamar a `RemoveUpdates` en el `OnPause` método para que podamos ahorrar energía si una aplicación no necesita actualizaciones de ubicación mientras su actividad no está en la pantalla:

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

Si la aplicación necesita obtener actualizaciones de ubicación en segundo plano, querrá crear un servicio personalizado que se suscriba al servicio de ubicación del sistema. Consulte la guía de procesamiento en [segundo plano con servicios Android](~/android/app-fundamentals/services/index.md) para obtener más información.

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>Determinar el mejor proveedor de ubicación para el LocationManager

La aplicación anterior establece GPS como proveedor de ubicación. Sin embargo, es posible que GPS no esté disponible en todos los casos, por ejemplo, si el dispositivo es interior o no tiene un receptor de GPS. Si es así, el resultado es un `null` valor devuelto por el proveedor.

Para que la aplicación funcione cuando GPS no esté disponible, use el método para `GetBestProvider` solicitar el mejor proveedor de ubicación disponible (compatible con el dispositivo y habilitado para el usuario) en el inicio de la aplicación. En lugar de pasar un proveedor concreto, puede indicar `GetBestProvider` los requisitos del proveedor, como la precisión y la potencia de un [ `Criteria` objeto](xref:Android.Locations.Criteria). `GetBestProvider`Devuelve el mejor proveedor para los criterios especificados.

En el código siguiente se muestra cómo obtener el mejor proveedor disponible y usarlo al solicitar actualizaciones de Ubicación:

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
> Si el usuario ha deshabilitado todos los `GetBestProvider` proveedores de `null`ubicación, devolverá. Para ver cómo funciona este código en un dispositivo real, asegúrese de habilitar las redes GPS, Wi-Fi y telefonía móvil en **configuración de Google > el modo de ubicación >** como se muestra en esta captura de pantalla:
>
> [![Pantalla de modo de ubicación de configuración en un teléfono Android](location-images/location-02.png)](location-images/location-02.png#lightbox)
>
> En la captura de pantalla siguiente se muestra la `GetBestProvider`aplicación de ubicación que se ejecuta mediante:
>
> [![Aplicación GetBestProvider que muestra latitud, longitud y proveedor](location-images/location-03.png)](location-images/location-03.png#lightbox)
>
> Tenga en cuenta que `GetBestProvider` no cambia el proveedor dinámicamente. En su lugar, determina el mejor proveedor disponible una vez durante el ciclo de vida de la actividad. Si el estado del proveedor cambia después de que se haya establecido, la aplicación requerirá código adicional `ILocationListener` en &ndash; los `OnProviderDisabled`métodos `OnProviderEnabled`, `OnStatusChanged` y &ndash; para controlar todas las posibilidades relacionadas con el modificador de proveedor.

## <a name="summary"></a>Resumen

En esta guía se ha tratado la obtención de la ubicación del usuario mediante el servicio de ubicación de Android y el proveedor de ubicación con fusible de Google Location Services API.

## <a name="related-links"></a>Vínculos relacionados

- [Ubicación (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/location)
- [FusedLocationProvider (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fusedlocationprovider)
- [Google Play Services](https://developer.android.com/google/play-services/index.html)
- [Clase de criterios](xref:Android.Locations.Criteria)
- [Clase LocationManager](xref:Android.Locations.LocationManager)
- [Clase LocationListener](xref:Android.Locations.ILocationListener)
- [API de LocationClient](https://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [API de LocationListener](https://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [API de LocationRequest](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)
