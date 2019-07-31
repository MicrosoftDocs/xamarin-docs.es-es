---
title: 'Tutorial: ubicación en segundo plano en Xamarin. iOS'
description: En este documento se proporciona un tutorial sobre cómo usar la información de ubicación en una aplicación de Xamarin. iOS en el fondo. Describe los Estados de configuración, interfaz de usuario y aplicación necesarios.
ms.prod: xamarin
ms.assetid: F8EEA0FD-5614-47FE-ADAC-80A5BCA6EB5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 20f49f3f0c103791064545311d9f66d409cff357
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656538"
---
# <a name="walkthrough---background-location-in-xamarinios"></a>Tutorial: ubicación en segundo plano en Xamarin. iOS

En este ejemplo, vamos a crear una aplicación de ubicación de iOS que imprime información sobre la ubicación actual: latitud, longitud y otros parámetros en la pantalla. Esta aplicación mostrará cómo realizar correctamente las actualizaciones de ubicación mientras la aplicación está activa o en segundo plano.

En este tutorial se explican algunos conceptos de fondo clave, incluido el registro de una aplicación como una aplicación necesaria para el fondo, la suspensión de las actualizaciones de la interfaz de usuario cuando se `WillEnterBackground` hospeda `WillEnterForeground` la aplicación y el uso de los métodos y `AppDelegate` . .

## <a name="application-set-up"></a>Configuración de la aplicación


1. En primer lugar, cree una nueva aplicación de **> de iOS >C#aplicación de vista única ()** . Llame a la _Ubicación_ de ti y asegúrese de que se han seleccionado iPad y iPhone.

1. Una aplicación de ubicación se califica como una aplicación necesaria para el fondo en iOS. Registre la aplicación como una aplicación de ubicación editando el archivo **info. plist** del proyecto.

    En Explorador de soluciones, haga doble clic en el archivo **info. plist** para abrirlo y desplácese hasta la parte inferior de la lista. Active las casillas **Habilitar modos en segundo plano** y **actualizaciones de ubicación** .

    En Visual Studio para Mac, tendrá un aspecto similar al siguiente:

    [![](location-walkthrough-images/image7.png "Active la casilla habilitar los modos en segundo plano y las actualizaciones de ubicación")](location-walkthrough-images/image7.png#lightbox)

    En Visual Studio, **info. plist** debe actualizarse manualmente agregando el siguiente par clave-valor:

    ```xml
    <key>UIBackgroundModes</key>
    <array>
      <string>location</string>
    </array>
    ```

1. Ahora que la aplicación está registrada, puede obtener datos de ubicación del dispositivo. En iOS, la `CLLocationManager` clase se usa para tener acceso a la información de ubicación y puede generar eventos que proporcionen actualizaciones de ubicación.

1. En el código, cree una nueva clase denominada `LocationManager` que proporcione un único lugar para varias pantallas y código para suscribirse a las actualizaciones de la ubicación. En la `LocationManager` clase, cree una instancia `CLLocationManager` del llamado `LocMgr`:

    ```csharp
    public class LocationManager
    {
        protected CLLocationManager locMgr;

        public LocationManager () {
            this.locMgr = new CLLocationManager();
            this.locMgr.PausesLocationUpdatesAutomatically = false;

            // iOS 8 has additional permissions requirements
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                locMgr.RequestAlwaysAuthorization (); // works in background
                //locMgr.RequestWhenInUseAuthorization (); // only in foreground
            }

            if (UIDevice.CurrentDevice.CheckSystemVersion (9, 0)) {
                locMgr.AllowsBackgroundLocationUpdates = true;
            }
        }

        public CLLocationManager LocMgr {
            get { return this.locMgr; }
        }
    }
    ```

    El código anterior establece una serie de propiedades y permisos en la clase [CLLocationManager](xref:CoreLocation.CLLocationManager) :

    - `PausesLocationUpdatesAutomatically`: Es un valor booleano que se puede establecer en función de si se permite al sistema pausar las actualizaciones de ubicación. En algunos dispositivos `true`, el valor predeterminado es, lo que puede hacer que el dispositivo deje de obtener actualizaciones de ubicación en segundo plano después de unos 15 minutos.
    - `RequestAlwaysAuthorization`-Debe pasar este método para proporcionar al usuario de la aplicación la opción de permitir el acceso a la ubicación en segundo plano. `RequestWhenInUseAuthorization`también se puede pasar si desea proporcionar al usuario la opción de permitir el acceso a la ubicación solo cuando la aplicación está en primer plano.
    - `AllowsBackgroundLocationUpdates`: Se trata de una propiedad booleana, introducida en iOS 9 que se puede establecer para permitir que una aplicación Reciba actualizaciones de ubicación cuando se suspende.

    > [!IMPORTANT]
    > iOS 8 (y versiones posteriores) también requiere una entrada en el archivo **info. plist** para mostrar el usuario como parte de la solicitud de autorización.

1. Agregue una clave `NSLocationAlwaysUsageDescription` o `NSLocationWhenInUseUsageDescription` una cadena que se mostrará al usuario en la alerta que solicita el acceso a los datos de ubicación.

1. iOS 9 requiere que, al `AllowsBackgroundLocationUpdates` usar **info. plist** , incluya la clave `UIBackgroundModes` con el valor `location`. Si ha completado el paso 2 de este tutorial, ya debería estar en el archivo info. plist.


1. Dentro de `LocationManager` la clase, cree un método `StartLocationUpdates` llamado con el código siguiente. Este código muestra cómo empezar a recibir actualizaciones de ubicación de `CLLocationManager`:

    ```csharp
    if (CLLocationManager.LocationServicesEnabled) {
        //set the desired accuracy, in meters
        LocMgr.DesiredAccuracy = 1;
        LocMgr.LocationsUpdated += (object sender, CLLocationsUpdatedEventArgs e) =>
        {
            // fire our custom Location Updated event
            LocationUpdated (this, new LocationUpdatedEventArgs (e.Locations [e.Locations.Length - 1]));
        };
        LocMgr.StartUpdatingLocation();
    }
    ```

    Hay varias cosas importantes que se producen en este método. En primer lugar, se realiza una comprobación para ver si la aplicación tiene acceso a los datos de ubicación en el dispositivo. Para comprobarlo, `LocationServicesEnabled` se llama `CLLocationManager`a en. Este método devolverá **false** si el usuario ha denegado el acceso de la aplicación a la información de ubicación.

1. A continuación, indique al administrador de ubicación la frecuencia de actualización. `CLLocationManager`proporciona muchas opciones para filtrar y configurar los datos de ubicación, incluida la frecuencia de las actualizaciones. En este ejemplo, establezca `DesiredAccuracy` para que se actualice siempre que la ubicación cambie por un medidor. Para obtener más información sobre la configuración de la frecuencia de actualización de ubicación y otras preferencias, consulte la referencia de la [clase CLLocationManager](https://developer.apple.com/library/ios/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html) en la documentación de Apple.

1. Por último, `StartUpdatingLocation` llame a `CLLocationManager` en la instancia de. Esto indica al administrador de ubicación que obtenga una corrección inicial en la ubicación actual y que empiece a enviar actualizaciones.

Hasta ahora, se ha creado el administrador de ubicación, configurado con los tipos de datos que queremos recibir y ha determinado la ubicación inicial. Ahora el código debe representar los datos de ubicación en la interfaz de usuario. Podemos hacer esto con un evento personalizado que toma `CLLocation` como argumento:

```csharp
// event for the location changing
public event EventHandler<LocationUpdatedEventArgs>LocationUpdated = delegate { };
```

El siguiente paso consiste en suscribirse a las actualizaciones de `CLLocationManager`ubicación desde y generar el `LocationUpdated` evento personalizado cuando los nuevos datos de ubicación estén disponibles, pasando la ubicación como argumento. Para ello, cree una nueva clase **LocationUpdateEventArgs.CS**. Este código es accesible dentro de la aplicación principal y devuelve la ubicación del dispositivo cuando se genera el evento:

```csharp
public class LocationUpdatedEventArgs : EventArgs
{
    CLLocation location;

    public LocationUpdatedEventArgs(CLLocation location)
    {
       this.location = location;
    }

    public CLLocation Location
    {
       get { return location; }
    }
}
```

## <a name="user-interface"></a>Interfaz de usuario

1. Use el diseñador de iOS para compilar la pantalla que mostrará la información de la ubicación. Haga doble clic en el archivo **Main. Storyboard** para comenzar.

    En el guión gráfico, arrastre varias etiquetas a la pantalla para que actúen como marcadores de posición para la información de ubicación. En este ejemplo, hay etiquetas para latitud, longitud, altitud, curso y velocidad.

    El diseño debe ser similar al siguiente:

    ![](location-walkthrough-images/image8.png "Diseño de la interfaz de usuario de ejemplo en el diseñador de iOS")

1. En el panel de solución, haga doble clic en `ViewController.cs` el archivo y edítelo para crear una nueva instancia de LocationManager y llamar `StartLocationUpdates`a en ella.
  Cambie el código para que tenga un aspecto similar al siguiente:

    ```csharp
    #region Computed Properties
    public static bool UserInterfaceIdiomIsPhone {
                get { return UIDevice.CurrentDevice.UserInterfaceIdiom == UIUserInterfaceIdiom.Phone; }
            }

    public static LocationManager Manager { get; set;}
    #endregion

    #region Constructors
    public ViewController (IntPtr handle) : base (handle)
    {
    // As soon as the app is done launching, begin generating location updates in the location manager
        Manager = new LocationManager();
        Manager.StartLocationUpdates();
    }

    #endregion
    ```

    Se iniciarán las actualizaciones de ubicación en el inicio de la aplicación, aunque no se mostrará ningún dato.

1. Ahora que se han recibido las actualizaciones de ubicación, actualice la pantalla con la información de ubicación. El método siguiente obtiene la ubicación de nuestro `LocationUpdated` evento y la muestra en la interfaz de usuario:

    ```csharp
    #region Public Methods
    public void HandleLocationChanged (object sender, LocationUpdatedEventArgs e)
    {
        // Handle foreground updates
        CLLocation location = e.Location;

        LblAltitude.Text = location.Altitude + " meters";
        LblLongitude.Text = location.Coordinate.Longitude.ToString ();
        LblLatitude.Text = location.Coordinate.Latitude.ToString ();
        LblCourse.Text = location.Course.ToString ();
        LblSpeed.Text = location.Speed.ToString ();

        Console.WriteLine ("foreground updated");
    }
    #endregion
    ```

Todavía es necesario suscribirse al `LocationUpdated` evento en nuestro AppDelegate y llamar al nuevo método para actualizar la interfaz de usuario. Agregue el código `ViewDidLoad,` siguiente justo después de la `StartLocationUpdates` llamada a:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // It is better to handle this with notifications, so that the UI updates
    // resume when the application re-enters the foreground!
    Manager.LocationUpdated += HandleLocationChanged;

}
```


Ahora, cuando se ejecute la aplicación, debería tener un aspecto similar al siguiente:

[![](location-walkthrough-images/image5.png "Ejecución de una aplicación de ejemplo")](location-walkthrough-images/image5.png#lightbox)

## <a name="handling-active-and-background-states"></a>Controlar los Estados activo y en segundo plano

1. La aplicación está generando actualizaciones de ubicación mientras está en primer plano y activo. Para demostrar lo que sucede cuando la aplicación entra en el fondo, `AppDelegate` invalide los métodos que realizan el seguimiento de los cambios de estado de la aplicación para que la aplicación escriba en la consola cuando realice la transición entre el primer plano y el fondo:

    ```csharp
    public override void DidEnterBackground (UIApplication application)
    {
        Console.WriteLine ("App entering background state.");
    }

    public override void WillEnterForeground (UIApplication application)
    {
        Console.WriteLine ("App will enter foreground");
    }
    ```

    Agregue el código siguiente en `LocationManager` para imprimir continuamente los datos de ubicación actualizados en la salida de la aplicación, para comprobar que la información de la ubicación todavía está disponible en segundo plano:

    ```csharp
    public class LocationManager
    {
        public LocationManager ()
        {
        ...
        LocationUpdated += PrintLocation;
        }
        ...

        //This will keep going in the background and the foreground
        public void PrintLocation (object sender, LocationUpdatedEventArgs e) {
        CLLocation location = e.Location;
        Console.WriteLine ("Altitude: " + location.Altitude + " meters");
        Console.WriteLine ("Longitude: " + location.Coordinate.Longitude);
        Console.WriteLine ("Latitude: " + location.Coordinate.Latitude);
        Console.WriteLine ("Course: " + location.Course);
        Console.WriteLine ("Speed: " + location.Speed);
        }
    }
    ```

1. Hay un problema restante con el código: si se intenta actualizar la interfaz de usuario cuando la aplicación está en segundo plano, iOS la terminará. Cuando la aplicación entra en segundo plano, el código debe cancelar la suscripción a las actualizaciones de ubicación y dejar de actualizar la interfaz de usuario.

    iOS nos proporciona notificaciones cuando la aplicación está a punto de pasar a un estado de aplicación diferente. En este caso, se puede suscribir a `ObserveDidEnterBackground` la notificación.

    En el fragmento de código siguiente se muestra cómo usar una notificación para permitir que la vista sepa cuándo se deben detener las actualizaciones de la interfaz de usuario. Esto pasará `ViewDidLoad`a la siguiente:

    ```csharp
    UIApplication.Notifications.ObserveDidEnterBackground ((sender, args) => {
        Manager.LocationUpdated -= HandleLocationChanged;
    });
    ```

    Cuando la aplicación se está ejecutando, la salida tendrá un aspecto similar al siguiente:

    ![](location-walkthrough-images/image6.png "Ejemplo de la salida de ubicación en la consola")

1. La aplicación imprime las actualizaciones de ubicación en la pantalla cuando se trabaja en primer plano y continúa imprimiendo los datos en la ventana de salida de la aplicación mientras se trabaja en segundo plano.

Solo queda un problema pendiente: la pantalla inicia las actualizaciones de la interfaz de usuario cuando la aplicación se carga por primera vez, pero no tiene ninguna manera de saber cuándo se ha vuelto a escribir la aplicación en primer plano. Si la aplicación de fondo se vuelve a colocar en primer plano, las actualizaciones de la interfaz de usuario no se reanudarán.

Para corregir esto, anide una llamada para iniciar actualizaciones de la interfaz de usuario dentro de otra notificación, que se activará cuando la aplicación entre en el estado activo:

```csharp
UIApplication.Notifications.ObserveDidBecomeActive ((sender, args) => {
  Manager.LocationUpdated += HandleLocationChanged;
});
```

Ahora la interfaz de usuario comenzará a actualizarse cuando se inicie la aplicación por primera vez y se reanudará la actualización cada vez que la aplicación vuelva a estar en primer plano.

En este tutorial, creamos una aplicación de iOS correcta y con reconocimiento de fondo que imprime los datos de ubicación en la pantalla y en la ventana de salida de la aplicación.


## <a name="related-links"></a>Vínculos relacionados

- [Ubicación (parte 4) (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/location)
- [Referencia de Framework de la ubicación principal](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CoreLocation_Framework/_index.html)
