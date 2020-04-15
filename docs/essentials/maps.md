---
title: 'Xamarin.Essentials: Map'
description: La clase Map de Xamarin.Essentials permite que una aplicación abra la aplicación de mapas instalada en una ubicación o marca de posición específica.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.custom: video
ms.openlocfilehash: c0875534d88ea5b66b3072c35b9d38894fe98934
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "61354590"
---
# <a name="xamarinessentials-map"></a>Xamarin.Essentials: Asignación

La clase **Map** permite que una aplicación abra la aplicación de mapas instalada en una ubicación o marca de posición específica.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-map"></a>Uso de Map

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Map funciona mediante una llamada al método `OpenAsync` con `Location` o `Placemark` abierto con `MapLaunchOptions` opcional.

```csharp
public class MapTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapLaunchOptions { Name = "Microsoft Building 25" };

        await Map.OpenAsync(location, options);
    }
}
```

Cuando se abre con `Placemark`, se requiere la siguiente información:

- `CountryName`
- `AdminArea`
- `Thoroughfare`
- `Locality`

```csharp
public class MapTest
{
    public async Task NavigateToBuilding25()
    {
        var placemark = new Placemark
            {
                CountryName = "United States",
                AdminArea = "WA",
                Thoroughfare = "Microsoft Building 25",
                Locality = "Redmond"
            };
        var options =  new MapLaunchOptions { Name = "Microsoft Building 25" };

        await Map.OpenAsync(placemark, options);
    }
}
```

## <a name="extension-methods"></a>Métodos de extensión.

Si ya tiene una referencia a `Location` o a `Placemark`, puede usar el método de extensión integrado `OpenMapAsync` con `MapLaunchOptions` opcional:

```csharp
public class MapTest
{
    public async Task OpenPlacemarkOnMap(Placemark placemark)
    {
        await placemark.OpenMapAsync();
    }
}
```

## <a name="directions-mode"></a>Modo de instrucciones

Si llama a `OpenMapAsync` sin `MapLaunchOptions`, el mapa se iniciará en la ubicación especificada. Si quiere, puede hacer que se calcule una ruta de navegación desde la posición actual del dispositivo. Para ello, establezca `NavigationMode` en `MapLaunchOptions`:

```csharp
public class MapTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapLaunchOptions { NavigationMode = NavigationMode.Driving };

        await Map.OpenAsync(location, options);
    }
}
```

## <a name="platform-differences"></a>Diferencias entre plataformas

# <a name="android"></a>[Android](#tab/android)

- `NavigationMode` admite Bicycling (Bicicleta), Driving (Automóvil) y Walking (A pie).

# <a name="ios"></a>[iOS](#tab/ios)

- `NavigationMode` admite Driving (Automóvil), Transit (Transporte público) y Walking (A pie).

# <a name="uwp"></a>[UWP](#tab/uwp)

- `NavigationMode` admite Driving (Automóvil), Transit (Transporte público) y Walking (A pie).

--------------

## <a name="platform-implementation-specifics"></a>Detalles de implementación de la plataforma

# <a name="android"></a>[Android](#tab/android)

Android usa el esquema `geo:` de URI para iniciar la aplicación de mapas en el dispositivo. Esto podría pedirle al usuario que seleccione de una aplicación existente que admite este esquema de URI.  Xamarin.Essentials se probó con Google Maps, que admite este esquema.

# <a name="ios"></a>[iOS](#tab/ios)

Sin detalles de implementación específicos para la plataforma.

# <a name="uwp"></a>[UWP](#tab/uwp)

Sin detalles de implementación específicos para la plataforma.

--------------

## <a name="api"></a>API

- [Código fuente de Map](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Map)
- [Documentación de API para Map](xref:Xamarin.Essentials.Map)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Maps-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
