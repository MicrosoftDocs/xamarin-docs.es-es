---
title: Mapa de Xamarin.Forms
description: En este artículo se explica cómo usar la clase Xamarin.Forms Map para usar la API de mapa de nativa en cada plataforma para proporcionar la que experiencia para los usuarios asigna un familiar.
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2019
ms.openlocfilehash: 242673efb38931eb678432a28f24db0ad9b8cb7d
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70228218"
---
# <a name="xamarinforms-map"></a>Mapa de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

_Xamarin.Forms utiliza la asignación nativa API en cada plataforma._

Xamarin.Forms.Maps para utiliza la asignación nativa API en cada plataforma. Esto proporciona una experiencia de mapas rápida y familiar a los usuarios, pero significa que se necesitan algunos pasos de configuración para cumplir los requisitos de la API de cada plataforma.
Una vez configurado, el `Map` funciona igual que cualquier otro elemento de Xamarin.Forms en el código común de control.

Se ha utilizado el control de mapa en el [MapsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps) ejemplo, que se muestra a continuación.

 [![Se asigna en el ejemplo MobileCRM](map-images/maps-zoom-sml.png "ejemplo de Control de mapa")](map-images/maps-zoom.png#lightbox "ejemplo de Control de mapa")

Funcionalidad de asignación se puede mejorar aún más mediante la creación de un [asignar un representador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md).

<a name="Maps_Initialization" />

## <a name="map-initialization"></a>Inicialización de asignación

Al agregar mapas a una aplicación de Xamarin.Forms, **xamarin.Forms.Maps para** es un paquete NuGet independiente que se debe agregar a cada proyecto en la solución.
En Android, esto también tiene una dependencia en GooglePlayServices (NuGet otro) que se descarga automáticamente cuando se agrega xamarin.Forms.Maps para.

Después de instalar el paquete de NuGet, es necesario algún código de inicialización en cada proyecto de aplicación, *después* el `Xamarin.Forms.Forms.Init` llamada al método. Para iOS, use el código siguiente:

```csharp
Xamarin.FormsMaps.Init();
```

En Android debe pasar los mismos parámetros que `Forms.Init`:

```csharp
Xamarin.FormsMaps.Init(this, bundle);
```

Para la plataforma Universal de Windows (UWP) use el código siguiente:

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

Agregue esta llamada en los siguientes archivos para cada plataforma:

- **iOS** -archivo AppDelegate.cs en el `FinishedLaunching` método.
- **Android** -archivo MainActivity.cs, en el `OnCreate` método.
- **UWP** -archivo MainPage.xaml.cs, en el `MainPage` constructor.

Una vez que se ha agregado el paquete NuGet y se ha llamado al método de `Xamarin.Forms.Maps` inicialización dentro de cada aplicación, las API se pueden usar en el proyecto de biblioteca de .net Standard común o en el código del proyecto compartido.

<a name="Platform_Configuration" />

## <a name="platform-configuration"></a>Configuración de plataforma

Se requieren pasos de configuración adicional en algunas plataformas antes de que se mostrará el mapa.

### <a name="ios"></a>iOS

Para obtener acceso a servicios de ubicación en iOS, debe establecer las siguientes claves **Info.plist**:

- iOS 11
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) : para usar los servicios de ubicación cuando la aplicación está en uso
  - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/corelocation/choosing_the_authorization_level_for_location_services/requesting_always_authorization?language=objc) : para usar los servicios de ubicación en todo momento
- iOS 10 y versiones anteriores
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) : para usar los servicios de ubicación cuando la aplicación está en uso
  - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) : para usar los servicios de ubicación en todo momento    

Para admitir iOS 11 y versiones anteriores, puede incluir estas tres claves: `NSLocationWhenInUseUsageDescription`, `NSLocationAlwaysAndWhenInUseUsageDescription`, y `NSLocationAlwaysUsageDescription`.

La representación XML de estas claves en **Info.plist** se muestra a continuación. Debe actualizar el `string` valores para que reflejen cómo la aplicación está utilizando la información de ubicación:

```xml
<key>NSLocationAlwaysUsageDescription</key>
<string>Can we use your location at all times?</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Can we use your location when your app is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

El **Info.plist** también se pueden agregar entradas en **origen** vista mientras edita el **Info.plist** archivo:

![Info.plist para iOS 8](map-images/ios8-map-permissions.png "iOS 8 entradas de Info.plist necesaria")

### <a name="android"></a>Android

Para usar el [v2 de la API de Google Maps](https://developers.google.com/maps/documentation/android/) en Android debe generar una clave de API y agregarlo al proyecto Android.
Siga las instrucciones de la documentación de Xamarin [obtención de una clave de API de Google Maps v2](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).
Después de seguir estas instrucciones, pegue la clave de API en el **Properties/Androidmanifest.XML** archivo (ver código fuente y buscar o actualizar el elemento siguiente):

```xml
<application ...>
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
</application>
```

Sin una clave de API válida, el control de mapas se mostrará como un cuadro gris en Android.

> [!NOTE]
> Tenga en cuenta que, en orden para el APK tener acceso a Google Maps, debe incluir las huellas digitales de SHA-1 y empaquetar los nombres para cada almacén de claves (debug y release) que usó para firmar el APK. Por ejemplo, si usa un equipo para depuración y otro equipo para generar el APK de lanzamiento, debe incluir la huella digital de certificado de SHA-1 desde el almacén de claves de depuración del primer equipo y la huella digital de certificado de SHA-1 desde el almacén de claves de la versión de el segundo equipo. Recuerde también que modificar las credenciales de clave si la aplicación **nombre del paquete** cambios. Consulte [obtención de una clave de API de Google Maps v2](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

También deberá habilitar los permisos adecuados, con el botón secundario en el proyecto de Android y seleccione **Opciones > compilar > aplicación de Android** y funciona a la perfección lo siguiente:

- `AccessCoarseLocation`
- `AccessFineLocation`
- `AccessLocationExtraCommands`
- `AccessMockLocation`
- `AccessNetworkState`
- `AccessWifiState`
- `Internet`

Algunos de ellos se muestran en la captura de pantalla siguiente:

![Permisos necesarios para Android](map-images/android-map-permissions.png "permisos necesarios para Android")

Los dos últimos son necesarios porque las aplicaciones requieren una conexión de red para descargar datos del mapa. Obtenga información sobre Android [permisos](https://developer.android.com/reference/android/Manifest.permission.html) para obtener más información.

Además, Android 9 ha quitado la biblioteca de cliente de Apache HTTP de bootclasspath y, por tanto, no está disponible para las aplicaciones que tienen como destino la API 28 o una versión posterior. Se debe agregar la siguiente línea al `application` nodo del archivo **archivo AndroidManifest. XML** para seguir usando el cliente de Apache HTTP en aplicaciones que tienen como destino la API 28 o posterior:

```xml
<application ...>
    ...
    <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

Para utilizar asignaciones en la plataforma Universal de Windows debe generar un token de autorización. Para obtener más información, consulte [solicitar una clave de autenticación de asignaciones](https://msdn.microsoft.com/library/windows/apps/mt219694.aspx) en MSDN.

El token de autenticación, a continuación, se debe especificar en el `FormsMaps.Init("AUTHORIZATION_TOKEN")` llamada al método, para autenticar la aplicación con Bing Maps.

<a name="Using_Maps" />

## <a name="map-configuration"></a>Configuración de asignación

Consulte la [MapPage.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/MobileCRM/MobileCRM.Shared/Pages/MapPage.cs) en el ejemplo MobileCRM para obtener un ejemplo de cómo se puede usar el control de mapa en el código. Un simple `MapPage` clase podría parecerse a este: tenga en cuenta que un nuevo `MapSpan` se crea para colocar la vista del mapa:

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

También se puede cambiar el contenido del mapa estableciendo el `MapType` propiedad para mostrar un mapa de calle normal (predeterminado), imágenes de satélite o una combinación de ambos.

```csharp
map.MapType = MapType.Street;
```

Válido `MapType` los valores son:

- `Hybrid`
- `Satellite`
- `Street` (el valor predeterminado)

### <a name="map-region-and-mapspan"></a>Región de mapa y MapSpan

Como se muestra en el fragmento de código anterior, proporcionando un `MapSpan` instancia a un constructor de asignación establece la vista inicial (punto central y el nivel de zoom) de la asignación cuando se cargue. Hay dos maneras de crear un nuevo `MapSpan` instancia:

- **MapSpan.FromCenterAndRadius()** -método estático para crear un intervalo de un `Position` y especificando un `Distance` .
- **New () de MapSpan** -constructor que usa un `Position` y los grados de latitud y longitud para mostrar.

El `MoveToRegion` método en la clase map, a continuación, puede utilizarse para cambiar el nivel de posición o zoom del mapa. Para cambiar el nivel de zoom del mapa sin modificar la ubicación, cree un nuevo `MapSpan` utilizando la ubicación actual de la `VisibleRegion.Center` propiedad del control de mapa. Se `Slider` puede utilizar para controlar el zoom de mapa como este (sin embargo, al hacer zoom directamente en el control de mapa no se puede actualizar el valor del control deslizante actualmente):

```csharp
Slider slider = new Slider (1, 18, 1);
slider.ValueChanged += (sender, e) =>
{
    var zoomLevel = e.NewValue; // between 1 and 18
    var latlongdegrees = 360 / (Math.Pow(2, zoomLevel));
    map.MoveToRegion(new MapSpan (map.VisibleRegion.Center, latlongdegrees, latlongdegrees));
};
```

[![Mapas con zoom](map-images/maps-zoom-sml.png "Zoom del Control de mapa")](map-images/maps-zoom.png#lightbox "Zoom del Control de mapa")

Además, la [`Map`](xref:Xamarin.Forms.Maps.Map) clase tiene una `MoveToLastRegionOnLayoutChange` propiedad de tipo `bool`, que está respaldada por una propiedad enlazable. De forma predeterminada, esta `true`propiedad es, que indica que la región de mapa mostrada pasará de su región actual a su región establecida anteriormente cuando se produzca un cambio de diseño, como en la rotación de dispositivos. Cuando esta propiedad está establecida en `false`, la región de mapa que se muestra permanecerá centrada cuando se produzca un cambio de diseño. En el ejemplo siguiente se muestra cómo establecer esta propiedad:

```csharp
map.MoveToLastRegionOnLayoutChange = false;
```

### <a name="map-pins"></a>Anclajes de mapa

Se pueden marcar las ubicaciones en el mapa con `Pin` objetos.

```csharp
var position = new Position(37,-122); // Latitude, Longitude
var pin = new Pin {
            Type = PinType.Place,
            Position = position,
            Label = "custom pin",
            Address = "custom detail info"
        };
map.Pins.Add(pin);
```

`PinType`se puede establecer en uno de los valores siguientes, lo que puede afectar a la manera en que se representa el PIN (dependiendo de la plataforma):

- Genérico
- Lugar
- SavedPin
- SearchResult

### <a name="map-clicks"></a>Clics de mapa

`Map`define un `MapClicked` evento que se desencadena cuando se puntea en el mapa. El `MapClickedEventArgs` objeto que acompaña al `MapClicked` evento tiene una propiedad única denominada `Position`, de tipo `Position`. Cuando se desencadena el evento, el valor de la `Position` propiedad se establece en la ubicación de asignación que se ha punteado.

En el ejemplo de código siguiente se muestra un controlador `MapClicked` de eventos para el evento:

```csharp
map.MapClicked += OnMapClicked;

void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

En este ejemplo, el `OnMapClicked` controlador de eventos genera la latitud y la longitud que representa la ubicación de asignación punteada.

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
> Se requiere `xmlns` una definición de espacio de nombres adicional para hacer referencia a los controles de Xamarin. Forms. Maps.

Y se pueden establecer en el código utilizando `Map`la referencia con nombre para: `Pins` `MapRegion`

```csharp
MyMap.MoveToRegion(
    MapSpan.FromCenterAndRadius(
        new Position(37,-122), Distance.FromMiles(1)));
```

## <a name="populate-a-map-with-data-using-data-binding"></a>Rellenar un mapa con datos mediante el enlace de datos

La [`Map`](xref:Xamarin.Forms.Maps.Map) clase también expone las siguientes propiedades:

- `ItemsSource`: especifica la colección de `IEnumerable` elementos que se van a mostrar.
- `ItemTemplate`: especifica el [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que se va a aplicar a cada elemento de la colección de elementos mostrados.
- `ItemTemplateSelector`: especifica el [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) que se usará para elegir una [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) para un elemento en tiempo de ejecución.

> [!NOTE]
> La propiedad `ItemTemplate` tiene prioridad cuando están establecidas ambas propiedades `ItemTemplate` e `ItemTemplateSelector`.

Un [`Map`](xref:Xamarin.Forms.Maps.Map) se puede rellenar con datos mediante el enlace de datos `ItemsSource` para enlazar `IEnumerable` su propiedad a una colección:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="WorkingWithMaps.PinItemsSourcePage">
    <Grid>
        ...
        <maps:Map x:Name="map"
                  MoveToLastRegionOnLayoutChange="false"
                  ItemsSource="{Binding Locations}">
            <maps:Map.ItemTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="{Binding Description}" />
                </DataTemplate>
            </maps:Map.ItemTemplate>
        </maps:Map>
        ...
    </Grid>
</ContentPage>
```

Los `ItemsSource` datos de la propiedad se enlazan a la `Locations` propiedad del modelo de vista conectado, `ObservableCollection` que `Location` devuelve una de objetos, que es un tipo personalizado. Cada `Location` objeto define `Address` las `Description` propiedades y, de `string`tipo y una `Position` propiedad, de tipo [`Position`](xref:Xamarin.Forms.Maps.Position).

La apariencia de cada elemento de la `IEnumerable` colección se define estableciendo la `ItemTemplate` propiedad en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que contiene un [`Pin`](xref:Xamarin.Forms.Maps.Pin) objeto que los datos enlazan a las propiedades adecuadas.

Las capturas de pantallas [`Map`](xref:Xamarin.Forms.Maps.Map) siguientes muestran [`Pin`](xref:Xamarin.Forms.Maps.Pin) cómo mostrar una colección mediante el enlace de datos:

[ ![Captura de pantalla de la asignación con PIN enlazados a datos, en iOS y Android](map-images/pins-itemssource.png "map con PIN enlazados a datos") ] (map-images/pins-itemssource-large.png#lightbox "Asignación con PIN enlazados a datos")

### <a name="choose-item-appearance-at-runtime"></a>Elección de la apariencia del elemento en tiempo de ejecución

La apariencia de cada elemento de la `IEnumerable` colección se puede elegir en tiempo de ejecución, en función del valor del elemento, `ItemTemplateSelector` estableciendo la propiedad [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)en:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:WorkingWithMaps"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
    <ContentPage.Resources>
        <local:MapItemTemplateSelector x:Key="MapItemTemplateSelector">
            <local:MapItemTemplateSelector.DefaultTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="{Binding Description}" />
                </DataTemplate>
            </local:MapItemTemplateSelector.DefaultTemplate>
            <local:MapItemTemplateSelector.XamarinTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="Xamarin!" />
                </DataTemplate>
            </local:MapItemTemplateSelector.XamarinTemplate>    
        </local:MapItemTemplateSelector>
    </ContentPage.Resources>

    <Grid>
        ...
        <maps:Map x:Name="map"
                  ItemsSource="{Binding Locations}"
                  ItemTemplateSelector="{StaticResource MapItemTemplateSelector}" />
        ...
    </Grid>
</ContentPage>
```

En el ejemplo siguiente se `MapItemTemplateSelector` muestra la clase:

```csharp
public class MapItemTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate XamarinTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return ((Location)item).Address.Contains("San Francisco") ? XamarinTemplate : DefaultTemplate;
    }
}
```

La clase `MapItemTemplateSelector` define las propiedades `DefaultTemplate` y `XamarinTemplate` de tipo [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) que se establecen para diferentes plantillas de datos. El `OnSelectTemplate` método `Pin` devuelve, que muestra "Xamarin" como etiqueta cuando se puntea en, cuando el elemento tiene una dirección que contiene "San Francisco". `XamarinTemplate` Cuando el elemento no tiene una dirección que contiene "San Francisco", el `OnSelectTemplate` método `DefaultTemplate`devuelve.

Para obtener más información sobre los selectores de plantilla de datos, vea [crear un DataTemplateSelector de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Vínculos relacionados

- [MapsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Asignar a un representador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Xamarin.Forms Samples](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms) (Ejemplos de Xamarin.Forms)
- [Crear un DataTemplateSelector de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
- [API de Maps](xref:Xamarin.Forms.Maps)
