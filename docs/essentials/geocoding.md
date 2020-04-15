---
title: 'Xamarin.Essentials: Codificación geográfica'
description: La clase Geocoding de Xamarin.Essentials proporciona API para geocodificar una marca de posición en coordenadas de posición e invertir las coordenadas de código geográfico a una marca de posición.
ms.assetid: 3ADC440C-B000-4708-A2CC-296F5160AF90
author: jamesmontemagno
ms.author: jamont
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: 157eb3116f09268790036f8983543114e7a58276
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "66354106"
---
# <a name="xamarinessentials-geocoding"></a>Xamarin.Essentials: Codificación geográfica

La clase **Geocoding** proporciona API para geocodificar una marca de posición en coordenadas de posición e invertir las coordenadas de código geográfico a una marca de posición.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

Para acceder a la funcionalidad de **Geocoding**, se requiere la siguiente configuración específica para la plataforma.

# <a name="android"></a>[Android](#tab/android)

No se requiere configuración adicional.

# <a name="ios"></a>[iOS](#tab/ios)

No se requiere configuración adicional.

# <a name="uwp"></a>[UWP](#tab/uwp)

Se requiere una clave de API de Bing Maps para usar la funcionalidad de codificación geográfica. Regístrese para obtener una cuenta de [Bing Maps](https://www.bingmapsportal.com/). En **Mi cuenta > Mis claves**, cree una clave y rellene la información en función del tipo de la aplicación (que debe ser **Aplicación pública de Windows (UWP, 8.x y versiones anteriores)** para las aplicaciones para UWP).

En una fase inicial de la vida de la aplicación, establezca la clave de API antes de llamar a los métodos de **Geocoding**, que solo está disponible en UWP:

```csharp
Platform.MapServiceToken = "YOUR-KEY-HERE";
```

-----

## <a name="using-geocoding"></a>Uso de Geocoding

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Obtención de coordenadas de [ubicación](xref:Xamarin.Essentials.Location) para una dirección:

```csharp
try
{
    var address =  "Microsoft Building 25 Redmond WA USA";
    var locations = await Geocoding.GetLocationsAsync(address);

    var location = locations?.FirstOrDefault();
    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Handle exception that may have occurred in geocoding
}
```

La altitud no siempre está disponible. Si no lo está, es posible que la propiedad `Altitude` sea `null` o que el valor sea cero. Si lo está, el valor se expresa en metros sobre el nivel del mar.

## <a name="using-reverse-geocoding"></a>Uso la geocodificación inversa

La geocodificación inversa es el proceso de obtener [marcas de posición](xref:Xamarin.Essentials.Placemark) para un conjunto de coordenadas existente:

```csharp
try
{
    var lat = 47.673988;
    var lon = -122.121513;

    var placemarks = await Geocoding.GetPlacemarksAsync(lat, lon);

    var placemark = placemarks?.FirstOrDefault();
    if (placemark != null)
    {
        var geocodeAddress =
            $"AdminArea:       {placemark.AdminArea}\n" +
            $"CountryCode:     {placemark.CountryCode}\n" +
            $"CountryName:     {placemark.CountryName}\n" +
            $"FeatureName:     {placemark.FeatureName}\n" +
            $"Locality:        {placemark.Locality}\n" +
            $"PostalCode:      {placemark.PostalCode}\n" +
            $"SubAdminArea:    {placemark.SubAdminArea}\n" +
            $"SubLocality:     {placemark.SubLocality}\n" +
            $"SubThoroughfare: {placemark.SubThoroughfare}\n" +
            $"Thoroughfare:    {placemark.Thoroughfare}\n";

        Console.WriteLine(geocodeAddress);
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Handle exception that may have occurred in geocoding
}
```

## <a name="distance-between-two-locations"></a>Distancia entre dos ubicaciones

Las clases [`Location`](xref:Xamarin.Essentials.Location) y [`LocationExtensions`](xref:Xamarin.Essentials.LocationExtensions) definen métodos para calcular la distancia entre dos ubicaciones. Consulte el artículo [**Xamarin.Essentials: Geolocalización**](geolocation.md#calculate-distance) para ver un ejemplo.

## <a name="api"></a>API

- [Código fuente de Geocoding](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geocoding)
- [Documentación de API para Geocoding](xref:Xamarin.Essentials.Geocoding)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Geocoding-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
