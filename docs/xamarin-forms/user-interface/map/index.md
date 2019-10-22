---
title: Inicialización y configuración del mapa de Xamarin. Forms
description: En este artículo se explica cómo usar la clase de mapa de Xamarin. Forms para usar las API de asignación nativa en cada plataforma para proporcionar una experiencia de mapas familiar a los usuarios.
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/07/2019
ms.openlocfilehash: d9b1b93b0667415281bb04e2c4264f03be19bd83
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697714"
---
# <a name="xamarinforms-map-initialization-and-configuration"></a>Inicialización y configuración del mapa de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

_Xamarin. Forms usa las API de asignación nativa en cada plataforma._

Xamarin. Forms. Maps usa las API de asignación nativa en cada plataforma. Esto proporciona una experiencia de mapas rápida y familiar a los usuarios, pero significa que se necesitan algunos pasos de configuración para cumplir los requisitos de la API de cada plataforma.
Una vez configurado, el control de `Map` funciona igual que cualquier otro elemento de Xamarin. Forms en el código común.

El control de mapa se ha usado en el ejemplo [MapsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps) , que se muestra a continuación.

 [![Maps en el ejemplo MobileCRM](map-images/maps-zoom-sml.png "Ejemplo de Control de mapa")](map-images/maps-zoom.png#lightbox "Ejemplo de Control de mapa")

La funcionalidad de asignación se puede mejorar aún más mediante la creación de un [representador personalizado de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md).

<a name="Maps_Initialization" />

## <a name="map-initialization"></a>Inicialización de asignación

Al agregar mapas a una aplicación de Xamarin. Forms, **Xamarin. Forms. Maps** es un paquete de NuGet independiente que debe agregar a todos los proyectos de la solución.
En Android, esto también tiene una dependencia en GooglePlayServices (otro NuGet) que se descarga automáticamente al agregar Xamarin. Forms. Maps.

Después de instalar el paquete NuGet, se requiere algún código de inicialización en cada proyecto de aplicación, *después* de la llamada al método `Xamarin.Forms.Forms.Init`. Para iOS, use el código siguiente:

```csharp
Xamarin.FormsMaps.Init();
```

En Android, debe pasar los mismos parámetros que `Forms.Init`:

```csharp
Xamarin.FormsMaps.Init(this, bundle);
```

Para el Plataforma universal de Windows (UWP), use el código siguiente:

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

Agregue esta llamada en los siguientes archivos para cada plataforma:

- el archivo **iOS** -AppDelegate.CS, en el método `FinishedLaunching`.
- Archivo **Android** -MainActivity.CS, en el método `OnCreate`.
- El archivo **UWP** -mainpage.Xaml.CS, en el constructor `MainPage`.

Una vez que se ha agregado el paquete NuGet y se ha llamado al método de inicialización dentro de cada aplicación, se pueden usar `Xamarin.Forms.Maps` API en el proyecto de biblioteca de .NET Standard común o en el código del proyecto compartido.

<a name="Platform_Configuration" />

## <a name="platform-configuration"></a>Configuración de plataforma

Se requieren pasos de configuración adicionales en algunas plataformas antes de que se muestre la asignación.

### <a name="ios"></a>iOS

Para acceder a los servicios de ubicación en iOS, debe establecer las siguientes claves en **info. plist**:

- iOS 11
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
<string>Can we use your location when your app is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

Las entradas **info. plist** también se pueden agregar en la vista **código fuente** mientras se edita el archivo **info. plist** :

![Info. plist para iOS 8](map-images/ios8-map-permissions.png "Entradas de info. plist necesarias de iOS 8")

### <a name="android"></a>Android

Para usar [Google Maps API V2](https://developers.google.com/maps/documentation/android/) en Android, debe generar una clave de API y agregarla al proyecto de Android.
Siga las instrucciones que aparecen en el documento de Xamarin sobre [Cómo obtener una clave de Google Maps API V2](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).
Después de estas instrucciones, pegue la clave de API en el archivo **Properties/archivo AndroidManifest. XML** (View source y busque/actualice el siguiente elemento):

```xml
<application ...>
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
</application>
```

Sin una clave de API válida, el control de mapas se mostrará como un cuadro gris en Android.

> [!NOTE]
> Tenga en cuenta que, para que el APK tenga acceso a Google Maps, debe incluir las huellas digitales y los nombres de paquete SHA-1 para cada almacén de claves (depuración y lanzamiento) que use para firmar el APK. Por ejemplo, si usa un equipo para la depuración y otro equipo para generar la versión APK, debe incluir la huella digital del certificado SHA-1 desde el almacén de claves de depuración del primer equipo y la huella digital del certificado SHA-1 del almacén de claves de la versión de el segundo equipo. Recuerde también editar las credenciales clave si cambia el **nombre del paquete** de la aplicación. Consulte [obtención de una clave de la API de Google Maps V2](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

También deberá habilitar los permisos adecuados haciendo clic con el botón derecho en el proyecto de Android y seleccionando **opciones > Compilar > aplicación de Android** y marcando lo siguiente:

- `AccessCoarseLocation`
- `AccessFineLocation`
- `AccessLocationExtraCommands`
- `AccessMockLocation`
- `AccessNetworkState`
- `AccessWifiState`
- `Internet`

Algunos de ellos se muestran en la siguiente captura de pantalla:

![Permisos necesarios para Android](map-images/android-map-permissions.png "Permisos necesarios para Android")

Los dos últimos son necesarios porque las aplicaciones requieren una conexión de red para descargar datos de mapa. Lea acerca de [los permisos](https://developer.android.com/reference/android/Manifest.permission.html) de Android para obtener más información.

Además, Android 9 ha quitado la biblioteca de cliente de Apache HTTP de bootclasspath y, por tanto, no está disponible para las aplicaciones que tienen como destino la API 28 o una versión posterior. Se debe agregar la siguiente línea al nodo `application` del archivo **archivo AndroidManifest. XML** para seguir usando el cliente http de Apache en aplicaciones que tienen como destino la API 28 o posterior:

```xml
<application ...>
    ...
    <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

Para usar las asignaciones en el Plataforma universal de Windows debe generar un token de autorización. Para obtener más información, consulte [solicitar una clave de autenticación de Maps](https://msdn.microsoft.com/library/windows/apps/mt219694.aspx) en MSDN.

Después, el token de autenticación debe especificarse en la llamada al método `FormsMaps.Init("AUTHORIZATION_TOKEN")` para autenticar la aplicación con mapas de Bing.

<a name="Using_Maps" />

## <a name="map-configuration"></a>Configuración de asignación

Vea [MapPage.CS](https://github.com/xamarin/xamarin-forms-samples/blob/master/MobileCRM/MobileCRM.Shared/Pages/MapPage.cs) en el ejemplo MobileCRM para obtener un ejemplo de cómo se puede usar el control de mapa en el código. Una clase de `MapPage` simple podría tener el siguiente aspecto: Observe que se crea una nueva `MapSpan` para colocar la vista del mapa:

```csharp
public class MapPage : ContentPage {
    public MapPage() {
        var map = new Map(
            MapSpan.FromCenterAndRadius(
                    new Position(37,-122), Distance.FromMiles(0.3))) {
                IsShowingUser = true,
                HeightRequest = 100,
                WidthRequest = 960,
                VerticalOptions = LayoutOptions.FillAndExpand
            };
        var stack = new StackLayout { Spacing = 0 };
        stack.Children.Add(map);
        Content = stack;
    }
}
```

### <a name="map-type"></a>Tipo de asignación

También se puede cambiar el contenido del mapa si se establece la propiedad `MapType`, para mostrar un mapa de calles normal (el valor predeterminado), una imagen de satélite o una combinación de ambos.

```csharp
map.MapType = MapType.Street;
```

Los valores de `MapType` válidos son:

- `Hybrid`
- `Satellite`
- `Street` (valor predeterminado)

### <a name="map-region-and-mapspan"></a>Región de mapa y MapSpan

Tal como se muestra en el fragmento de código anterior, al proporcionar una instancia de `MapSpan` a un constructor de mapa, se establece la vista inicial (punto central y nivel de zoom) del mapa cuando se carga. Hay dos maneras de crear una nueva instancia de `MapSpan`:

- **MapSpan. FromCenterAndRadius ()** : método estático para crear un intervalo a partir de un `Position` y especificar un `Distance`.
- **nuevo MapSpan ()** : constructor que usa una `Position` y los grados de latitud y longitud que se van a mostrar.

El método `MoveToRegion` de la clase MAP se puede usar para cambiar la posición o el nivel de zoom del mapa. Para cambiar el nivel de zoom del mapa sin modificar la ubicación, cree una nueva `MapSpan` mediante la ubicación actual desde la propiedad `VisibleRegion.Center` del control de mapa. Un `Slider` se puede utilizar para controlar el zoom de mapa como este (sin embargo, al hacer zoom directamente en el control de mapa no se puede actualizar el valor del control deslizante actualmente):

```csharp
Slider slider = new Slider (1, 18, 1);
slider.ValueChanged += (sender, e) =>
{
    var zoomLevel = e.NewValue; // between 1 and 18
    var latlongdegrees = 360 / (Math.Pow(2, zoomLevel));
    map.MoveToRegion(new MapSpan (map.VisibleRegion.Center, latlongdegrees, latlongdegrees));
};
```

[![Mapas con zoom](map-images/maps-zoom-sml.png "Zoom de Control de mapa")](map-images/maps-zoom.png#lightbox "Zoom de Control de mapa")

Además, la clase [`Map`](xref:Xamarin.Forms.Maps.Map) tiene una propiedad `MoveToLastRegionOnLayoutChange` de tipo `bool`, que está respaldada por una propiedad enlazable. De forma predeterminada, esta propiedad es `true`, lo que indica que la región de mapa mostrada pasará de su región actual a su región establecida anteriormente cuando se produzca un cambio de diseño, como en la rotación de dispositivos. Cuando esta propiedad se establece en `false`, la región de mapa que se muestra permanecerá centrada cuando se produzca un cambio de diseño. En el ejemplo siguiente se muestra cómo establecer esta propiedad:

```csharp
map.MoveToLastRegionOnLayoutChange = false;
```

### <a name="map-clicks"></a>Clics de mapa

`Map` define un evento `MapClicked` que se desencadena cuando se puntea en la asignación. El objeto `MapClickedEventArgs` que acompaña al evento `MapClicked` tiene una propiedad única denominada `Position`, de tipo `Position`. Cuando se desencadena el evento, el valor de la propiedad `Position` se establece en la ubicación de asignación que se ha punteado.

En el ejemplo de código siguiente se muestra un controlador de eventos para el evento `MapClicked`:

```csharp
map.MapClicked += OnMapClicked;

void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

En este ejemplo, el controlador de eventos `OnMapClicked` devuelve la latitud y la longitud que representa la ubicación de asignación punteada.

<a name="Using_Xaml" />

### <a name="create-a-map-in-xaml"></a>Crear un mapa en XAML

Las asignaciones también se pueden crear en XAML, tal como se muestra en este ejemplo:

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="MapDemo.MapPage">
    <StackLayout VerticalOptions="StartAndExpand" Padding="30">
        <maps:Map x:Name="MyMap"
                  Clicked="OnMapClicked"
                  WidthRequest="320"
                  HeightRequest="200"                  
                  IsShowingUser="true"
                  MapType="Hybrid" />
    </StackLayout>
</ContentPage>
```

> [!NOTE]
> Se requiere una definición de espacio de nombres `xmlns` adicional para hacer referencia a los controles Xamarin. Forms. Maps. En el ejemplo anterior, se hace referencia al espacio de nombres `Xamarin.Forms.Maps` a través de la palabra clave `maps`.

El `MapRegion` se puede establecer en el código utilizando la referencia con nombre para el `Map`:

```csharp
MyMap.MoveToRegion(
    MapSpan.FromCenterAndRadius(
        new Position(37,-122), Distance.FromMiles(1)));
```

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Clavijas de Xamarin. Forms. Maps](~/xamarin-forms/user-interface/map/pins.md).
- [API de Maps](xref:Xamarin.Forms.Maps)
- [Representador personalizado de asignación](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
