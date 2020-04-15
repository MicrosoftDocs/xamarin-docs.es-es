---
title: Inicio de la aplicación de mapas
description: Cómo iniciar la aplicación de mapas integrada desde la aplicación Xamarin.Android.
ms.prod: xamarin
ms.assetid: 929EACB8-8950-50E1-093C-43FB5F1F1CD5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 7b74f564f2b6e9613874a774258a7e999002e61a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027077"
---
# <a name="launching-the-maps-application"></a>Inicio de la aplicación de mapas

La manera más sencilla de trabajar con mapas en Xamarin.Android es utilizar la aplicación de mapas integrada que se muestra a continuación:

[![Captura de pantalla de ejemplo de la aplicación integrada de Google Maps](maps-application-images/01-mapsapplication.png)](maps-application-images/01-mapsapplication.png#lightbox)

Cuando se usa la aplicación de mapas, el mapa no formará parte de la aplicación. En su lugar, la aplicación iniciará la aplicación de mapas y cargará el mapa externamente. En la siguiente sección se examina cómo usar Xamarin.Android para iniciar mapas como el anterior.

## <a name="creating-the-intent"></a>Creación de la intención

Trabajar con la aplicación de mapas es tan sencillo como crear una intención con un URI adecuado, establecer la acción en ActionView y llamar al método StartActivity. Por ejemplo, el código siguiente inicia la aplicación de mapas centrada en una latitud y longitud determinada:

```csharp
var geoUri = Android.Net.Uri.Parse ("geo:42.374260,-71.120824");
var mapIntent = new Intent (Intent.ActionView, geoUri);
StartActivity (mapIntent);
```

Este código es todo lo que se necesita para iniciar el mapa que se muestra en la captura de pantalla anterior. Además de especificar la latitud y la longitud, el esquema de URI para los mapas admite otras opciones.

## <a name="geo-uri-scheme"></a>Esquema geo URI

El código anterior usa el esquema geográfico para crear un URI. Este esquema URI admite varios formatos, como se muestra a continuación:

- `geo:latitude,longitude`: abre la aplicación de mapas centrada en una latitud y longitud. 

- `geo:latitude,longitude?z=zoom`: abre la aplicación de mapas centrada en una latitud y longitud, y se amplía hasta el nivel especificado. El nivel de zoom puede ser de 1 a 23: 1 muestra toda la tierra y 23 es el nivel de zoom más cercano.

- `geo:0,0?q=my+street+address`: abre la aplicación de mapas en la ubicación de una dirección. 

- `geo:0,0?q=business+near+city`: abre la aplicación de mapas y muestra los resultados de la búsqueda anotados. 

Las versiones del URI que toman una consulta (es decir, la dirección o los términos de búsqueda) usan el servicio geocodificador de Google para recuperar la ubicación que se muestra en el mapa. Por ejemplo, el URI `geo:0,0?q=coop+Cambridge` produce el mapa que se muestra a continuación:

[![Captura de pantalla de ejemplo que muestra Google Maps con un término de búsqueda](maps-application-images/02-mapsearch.png)](maps-application-images/02-mapsearch.png#lightbox)

Para obtener más información sobre los esquemas geo URI, consulte [Mostrar una ubicación en un mapa](https://developer.android.com/guide/components/intents-common.html#Maps).

## <a name="street-view"></a>Vista de calle

Además del esquema geográfico, Android también admite la carga de vistas de calle desde una intención. A continuación, se muestra un ejemplo de la aplicación de vista de calle iniciada desde Xamarin.Android:

[![Captura de pantalla de ejemplo de una vista de calle](maps-application-images/03-streetview.png)](maps-application-images/03-streetview.png#lightbox)

Para iniciar una vista de calle, simplemente use el esquema URI `google.streetview`, como se muestra en el código siguiente:

```csharp
var streetViewUri = Android.Net.Uri.Parse (
       "google.streetview:cbll=42.374260,-71.120824&cbp=1,90,,0,1.0&mz=20");  
var streetViewIntent = new Intent (Intent.ActionView, streetViewUri);  
StartActivity (streetViewIntent);
```

El esquema URI google.streetview usado más arriba tiene el formato siguiente:

```csharp
google.streetview:cbll=lat,lng&cbp=1,yaw,,pitch,zoom&mz=mapZoom
```

Como puede ver, se admiten varios parámetros, como se muestra a continuación:

- `lat`: la latitud de la ubicación que se va a mostrar en la vista de calle.

- `lng`: la longitud de la ubicación que se va a mostrar en la vista de calle.

- `pitch`: ángulo de la vista panorámica de la calle, medido desde el centro en grados, donde 90 grados corresponde a un ángulo recto hacia abajo y -90 grados a un ángulo recto hacia arriba.

- `yaw`: centro de la vista panorámica de la calle, medido en el sentido de las agujas del reloj en grados desde el norte.

- `zoom`: multiplicador del zoom para la vista panorámica de la calle, donde 1.0 = zoom normal, 2.0 = zoom 2x, 3.0 = zoom 4x, etc.

- `mz`: el nivel de zoom del mapa que se usará cuando se vaya a la aplicación de mapas desde la vista de calle.

Trabajar con la aplicación de mapas integrada o la vista de calle es una manera fácil de agregar rápidamente compatibilidad con los mapas. Sin embargo, la API Maps de Android ofrece un mayor control sobre la experiencia con los mapas.
