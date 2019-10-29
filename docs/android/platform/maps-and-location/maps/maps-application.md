---
title: Inicio de la aplicación Maps
description: Cómo iniciar la aplicación de mapas integrada desde dentro de la aplicación de Xamarin. Android.
ms.prod: xamarin
ms.assetid: 929EACB8-8950-50E1-093C-43FB5F1F1CD5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 7b74f564f2b6e9613874a774258a7e999002e61a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027077"
---
# <a name="launching-the-maps-application"></a>Inicio de la aplicación Maps

La manera más sencilla de trabajar con mapas en Xamarin. Android es aprovechar la aplicación de mapas integrada que se muestra a continuación:

[![captura de pantalla de ejemplo de la aplicación integrada de Google Maps](maps-application-images/01-mapsapplication.png)](maps-application-images/01-mapsapplication.png#lightbox)

Cuando se usa la aplicación Maps, la asignación no formará parte de la aplicación. En su lugar, la aplicación iniciará la aplicación Maps y cargará el mapa externamente. En la siguiente sección se examina cómo usar Xamarin. Android para iniciar mapas como el anterior.

## <a name="creating-the-intent"></a>Crear el intento

Trabajar con la aplicación Maps es tan sencillo como crear una intención con un URI adecuado, establecer la acción en ActionView y llamar al método StartActivity. Por ejemplo, el código siguiente inicia la aplicación Maps centrada en una latitud y longitud determinada:

```csharp
var geoUri = Android.Net.Uri.Parse ("geo:42.374260,-71.120824");
var mapIntent = new Intent (Intent.ActionView, geoUri);
StartActivity (mapIntent);
```

Este código es todo lo que se necesita para iniciar el mapa que se muestra en la captura de pantalla anterior. Además de especificar la latitud y la longitud, el esquema de URI para Maps admite otras opciones.

## <a name="geo-uri-scheme"></a>Esquema de URI geo

El código anterior usaba el esquema geográfico para crear un URI. Este esquema de URI admite varios formatos, como se muestra a continuación:

- en `geo:latitude,longitude` &ndash; se abre la aplicación Maps centrada en lat/lon. 

- en `geo:latitude,longitude?z=zoom` &ndash; se abre la aplicación Maps Center en una lat/lon y se amplía el nivel especificado. El nivel de zoom puede oscilar entre 1 y 23:1 muestra toda la tierra y 23 es el nivel de zoom más cercano.

- en `geo:0,0?q=my+street+address` &ndash; se abre la aplicación Maps en la ubicación de una dirección postal. 

- en `geo:0,0?q=business+near+city` &ndash; se abre la aplicación Maps y se muestran los resultados de la búsqueda anotados. 

Las versiones del URI que toman una consulta (es decir, la dirección postal o los términos de búsqueda) usan el servicio de webencoder de Google para recuperar la ubicación que se muestra en el mapa. Por ejemplo, el URI `geo:0,0?q=coop+Cambridge` resultados en el mapa que se muestra a continuación:

[![captura de pantalla de ejemplo que muestra Google Maps con un término de búsqueda](maps-application-images/02-mapsearch.png)](maps-application-images/02-mapsearch.png#lightbox)

Para obtener más información sobre los esquemas de URI geográfico, consulte [Mostrar una ubicación en un mapa](https://developer.android.com/guide/components/intents-common.html#Maps).

## <a name="street-view"></a>Vista de calle

Además del esquema geográfico, Android también admite la carga de vistas de calles desde un intento. A continuación se muestra un ejemplo de la aplicación calle View iniciada desde Xamarin. Android:

[![captura de pantalla de ejemplo de una vista de calle](maps-application-images/03-streetview.png)](maps-application-images/03-streetview.png#lightbox)

Para iniciar una vista de calle, simplemente use el esquema de URI `google.streetview`, como se muestra en el código siguiente:

```csharp
var streetViewUri = Android.Net.Uri.Parse (
       "google.streetview:cbll=42.374260,-71.120824&cbp=1,90,,0,1.0&mz=20");  
var streetViewIntent = new Intent (Intent.ActionView, streetViewUri);  
StartActivity (streetViewIntent);
```

El esquema de URI de Google. streetview usado anteriormente tiene el formato siguiente:

```csharp
google.streetview:cbll=lat,lng&cbp=1,yaw,,pitch,zoom&mz=mapZoom
```

Como puede ver, se admiten varios parámetros, como se muestra a continuación:

- `lat` &ndash; la latitud de la ubicación que se va a mostrar en la vista de la calle.

- `lng` &ndash; la longitud de la ubicación que se va a mostrar en la vista de la calle.

- `pitch` &ndash; ángulo de la panorámica de la vista de calle, medido desde el centro en grados donde 90 grados es recto y-90 grados es recto.

- `yaw` &ndash; centro de vista de la panorámica de la vista de la calle, medido en el sentido de las agujas del reloj en grados desde el norte.

- `zoom` &ndash; multiplicador de la vista de calle, donde 1,0 = zoom normal, 2,0 = zoom 2x, 3,0 = zoom 4x, etc.

- `mz` &ndash; el nivel de zoom del mapa que se usará cuando vaya a la aplicación Maps desde la vista calle.

Trabajar con la aplicación de mapas integrada o la vista de calle es una manera fácil de agregar rápidamente compatibilidad con las asignaciones. Sin embargo, la API de Maps de Android ofrece un mayor control sobre la experiencia de asignación.
