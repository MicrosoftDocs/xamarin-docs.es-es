---
title: Inicialización y configuración del mapa de Xamarin. Forms
description: El paquete de NuGet Xamarin. Forms. Maps es necesario para usar la funcionalidad de Maps en una aplicación. Además, el acceso a la ubicación del usuario requiere que se hayan concedido permisos de ubicación a la aplicación.
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/31/2019
ms.openlocfilehash: dd451ae1acd233c1d3de675357bb172f25716f59
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426292"
---
# <a name="xamarinforms-map-initialization-and-configuration"></a>Inicialización y configuración del mapa de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

El control [`Map`](xref:Xamarin.Forms.Maps.Map) utiliza el control de mapa nativo en cada plataforma. Esto proporciona una experiencia de mapas rápida y familiar a los usuarios, pero significa que se necesitan algunos pasos de configuración para cumplir los requisitos de la API de cada plataforma.

## <a name="map-initialization"></a>Inicialización de asignación

El control de [`Map`](xref:Xamarin.Forms.Maps.Map) se proporciona mediante el paquete de NuGet [Xamarin. Forms. Maps](https://www.nuget.org/packages/Xamarin.Forms.Maps/) , que debe agregarse a todos los proyectos de la solución.

Después de instalar el paquete de NuGet [Xamarin. Forms. Maps](https://www.nuget.org/packages/Xamarin.Forms.Maps/) , debe inicializarse en cada proyecto de plataforma.

En iOS, esto debe ocurrir en **AppDelegate.CS** al invocar el método `Xamarin.FormsMaps.Init` *después* del método `Xamarin.Forms.Forms.Init`:

```csharp
Xamarin.FormsMaps.Init();
```

En Android, esto debe ocurrir en **MainActivity.CS** al invocar el método `Xamarin.FormsMaps.Init` *después* del método `Xamarin.Forms.Forms.Init`:

```csharp
Xamarin.FormsMaps.Init(this, savedInstanceState);
```

En el Plataforma universal de Windows (UWP), esto debe ocurrir en **mainpage.Xaml.CS** invocando el método `Xamarin.FormsMaps.Init` desde el constructor `MainPage`:

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

Una vez que se ha agregado el paquete NuGet y se ha llamado al método de inicialización dentro de cada aplicación, se pueden usar `Xamarin.Forms.Maps` API en el proyecto de código compartido.

## <a name="platform-configuration"></a>Configuración de plataforma

Se requiere configuración adicional en Android y en el Plataforma universal de Windows (UWP) antes de que se muestre la asignación. Además, en iOS, Android y UWP, el acceso a la ubicación del usuario requiere que se hayan concedido permisos de ubicación a la aplicación.

### <a name="ios"></a>iOS

La visualización e interacción con un mapa en iOS no requiere ninguna configuración adicional. Sin embargo, para tener acceso a los servicios de ubicación, debe establecer las siguientes claves en **info. plist**:

- iOS 11 y versiones posteriores
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) : para usar los servicios de ubicación cuando la aplicación está en uso
  - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/corelocation/choosing_the_authorization_level_for_location_services/requesting_always_authorization?language=objc) : para usar servicios de ubicación en todo momento
- iOS 10 y versiones anteriores
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) : para usar los servicios de ubicación cuando la aplicación está en uso
  - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) : para usar servicios de ubicación en todo momento    

Para admitir iOS 11 y versiones anteriores, puede incluir las tres claves: `NSLocationWhenInUseUsageDescription`, `NSLocationAlwaysAndWhenInUseUsageDescription` y `NSLocationAlwaysUsageDescription`.

A continuación se muestra la representación XML para estas claves en **info. plist** . Debe actualizar los valores de `string` para que reflejen el modo en que la aplicación usa la información de Ubicación:

```xml
<key>NSLocationAlwaysUsageDescription</key>
<string>Can we use your location at all times?</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Can we use your location when your application is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

Las entradas **info. plist** también se pueden agregar en la vista **código fuente** mientras se edita el archivo **info. plist** :

![Info. plist para iOS 8](setup-images/ios8-map-permissions.png "Entradas de info. plist necesarias de iOS 8")

A continuación, se muestra un mensaje cuando la aplicación intenta obtener acceso a la ubicación del usuario, solicitando acceso:

[![Captura de pantalla de la solicitud de permiso de ubicación en iOS](setup-images/permission-ios.png "solicitud de permiso de iOS")](setup-images/permission-ios-large.png#lightbox "solicitud de permiso de iOS")

### <a name="android"></a>Android

El proceso de configuración para mostrar e interactuar con un mapa en Android es el siguiente:

1. Obtenga una clave de API de Google Maps y agréguela al manifiesto.
1. Especifique el número de versión de Google Play Services en el manifiesto.
1. Especifique el requisito de la biblioteca de Apache HTTP Legacy en el manifiesto.
1. opta Especifique el permiso WRITE_EXTERNAL_STORAGE en el manifiesto.
1. opta Especifique los permisos de ubicación en el manifiesto.
1. opta Permisos de ubicación en tiempo de ejecución de solicitud en la clase `MainActivity`.

Para obtener un ejemplo de un archivo de manifiesto configurado correctamente, vea [archivo AndroidManifest. XML](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/WorkingWithMaps.Android/Properties/AndroidManifest.xml) de la aplicación de ejemplo.

#### <a name="get-a-google-maps-api-key"></a>Obtención de una clave de API de Google Maps

Para usar la [API de Google Maps](https://developers.google.com/maps/documentation/android/) en Android, debe generar una clave de API. Para ello, siga las instrucciones de [obtención de una clave de API de Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

Una vez que haya obtenido una clave de API, debe agregarse en el elemento `<application>` del archivo **Properties/archivo AndroidManifest. XML** :

```xml
<application ...>
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="PASTE-YOUR-API-KEY-HERE" />
</application>
```

Esto incrusta la clave de API en el manifiesto. Sin una clave de API válida, el control [`Map`](xref:Xamarin.Forms.Maps.Map) mostrará una cuadrícula en blanco.

> [!NOTE]
> Tenga en cuenta que, para que el APK tenga acceso a Google Maps, debe incluir las huellas digitales y los nombres de paquete SHA-1 para cada almacén de claves (depuración y lanzamiento) que use para firmar el APK. Por ejemplo, si usa un equipo para la depuración y otro equipo para generar la versión APK, debe incluir la huella digital del certificado SHA-1 desde el almacén de claves de depuración del primer equipo y la huella digital del certificado SHA-1 del almacén de claves de la versión de el segundo equipo. Recuerde también editar las credenciales clave si cambia el **nombre del paquete** de la aplicación. Consulte [obtención de una clave de API de Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

#### <a name="specify-the-google-play-services-version-number"></a>Especificar el número de versión de los servicios de Google Play

Agregue la siguiente declaración en el elemento `<application>` de **archivo AndroidManifest. XML**:

```xml
<meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
```

Esto incrusta la versión de Google Play servicios con los que se compiló la aplicación, en el manifiesto.

#### <a name="specify-the-requirement-for-the-apache-http-legacy-library"></a>Especificación del requisito de la biblioteca de Apache HTTP Legacy

Si la aplicación de Xamarin. Forms tiene como destino la API 28 o una versión posterior, debe agregar la siguiente declaración en el elemento `<application>` de **archivo AndroidManifest. XML**:

```xml
<uses-library android:name="org.apache.http.legacy" android:required="false" />    
```

Esto indica a la aplicación que use la biblioteca de cliente de Apache HTTP, que se ha quitado del `bootclasspath` en Android 9.

#### <a name="specify-the-write_external_storage-permission"></a>Especificar el permiso WRITE_EXTERNAL_STORAGE

Si su aplicación tiene como destino la API 22 o una inferior, puede que sea necesario agregar el `WRITE_EXTERNAL_STORAGE` permiso al manifiesto, como un elemento secundario del elemento `<manifest>`:

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

Esto no es necesario si la aplicación tiene como destino la API 23 o una versión posterior.

#### <a name="specify-location-permissions"></a>Especificar permisos de ubicación

Si la aplicación necesita tener acceso a la ubicación del usuario, debe solicitar el permiso agregando los permisos `ACCESS_COARSE_LOCATION` o `ACCESS_FINE_LOCATION` al manifiesto (o ambos), como un elemento secundario del elemento `<manifest>`:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.companyname.myapp">
  ...
  <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
  <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
</manifest>
```

El permiso `ACCESS_COARSE_LOCATION` permite a la API usar datos de Wi-Fi o móviles, o ambos, para determinar la ubicación del dispositivo. Los permisos `ACCESS_FINE_LOCATION` permiten a la API usar el sistema de posicionamiento global (GPS), Wi-Fi o los datos móviles para determinar una ubicación precisa como sea posible.

Como alternativa, estos permisos se pueden habilitar mediante el editor de manifiestos para agregar los permisos siguientes:

- `AccessCoarseLocation`
- `AccessFineLocation`

Se muestran en la siguiente captura de pantalla:

![Permisos necesarios para Android](setup-images/android-map-permissions.png "Permisos necesarios para Android")

#### <a name="request-runtime-location-permissions"></a>Permisos de ubicación en tiempo de ejecución de solicitud

Si su aplicación tiene como destino la API 23 o una versión posterior y necesita acceder a la ubicación del usuario, debe comprobar si tiene el permiso necesario en tiempo de ejecución y solicitarlo si no lo tiene. Esto se puede lograr de la siguiente manera:

1. En la clase `MainActivity`, agregue los siguientes campos:

    ```csharp
    const int RequestLocationId = 0;

    readonly string[] LocationPermissions =
    {
        Manifest.Permission.AccessCoarseLocation,
        Manifest.Permission.AccessFineLocation
    };
    ```

1. En la clase `MainActivity`, agregue la siguiente invalidación de `OnStart`:

    ```csharp
    protected override void OnStart()
    {
        base.OnStart();

        if ((int)Build.VERSION.SdkInt >= 23)
        {
            if (CheckSelfPermission(Manifest.Permission.AccessFineLocation) != Permission.Granted)
            {
                RequestPermissions(LocationPermissions, RequestLocationId);
            }
            else
            {
                // Permissions already granted - display a message.
            }
        }
    }
    ```

    Siempre que la aplicación esté destinada a la API 23 o una versión posterior, este código realiza una comprobación de permiso en tiempo de ejecución para el permiso `AccessFineLocation`. Si no se ha concedido el permiso, se realiza una solicitud de permiso mediante una llamada al método `RequestPermissions`.

1. En la clase `MainActivity`, agregue la siguiente invalidación de `OnRequestPermissionsResult`:

    ```csharp
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Permission[] grantResults)
    {
        if (requestCode == RequestLocationId)
        {
            if ((grantResults.Length == 1) && (grantResults[0] == (int)Permission.Granted))
                // Permissions granted - display a message.
            else
                // Permissions denied - display a message.
        }
        else
        {
            base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
        }
    }
    ```

    Esta invalidación controla el resultado de la solicitud de permiso.

El efecto general de este código es que cuando la aplicación solicita la ubicación del usuario, se muestra el siguiente cuadro de diálogo que solicita permiso:

[![Captura de pantalla de la solicitud de permiso de ubicación en Android](setup-images/permission-android.png "Solicitud de permiso de Android")](setup-images/permission-android-large.png#lightbox "Solicitud de permiso de Android")

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

En UWP, la aplicación debe autenticarse para poder mostrar un mapa y consumir servicios de mapa. Para autenticar la aplicación, debe especificar una clave de autenticación de maps. Para obtener más información, consulte [solicitar una clave de autenticación de Maps](/windows/uwp/maps-and-location/authentication-key). El token de autenticación debe especificarse en la llamada al método `FormsMaps.Init("AUTHORIZATION_TOKEN")` para autenticar la aplicación con mapas de Bing.

Además, si la aplicación necesita tener acceso a la ubicación del usuario, debe habilitar la funcionalidad de ubicación en el manifiesto del paquete. Esto se puede lograr de la siguiente manera:

1. En **Explorador de soluciones**, haga doble clic en **Package. appxmanifest** y seleccione la pestaña **capacidades** .
1. En la lista **capacidades** , active la casilla **Ubicación**. Esto agrega la capacidad del dispositivo `location` al archivo de manifiesto del paquete.

    ```xml
    <Capabilities>
      <!-- DeviceCapability elements must follow Capability elements (if present) -->
      <DeviceCapability Name="location"/>
    </Capabilities>
    ```

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Clavijas de Xamarin. Forms. Maps](~/xamarin-forms/user-interface/map/pins.md).
- [API de Maps](xref:Xamarin.Forms.Maps)
- [Representador personalizado de asignación](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
