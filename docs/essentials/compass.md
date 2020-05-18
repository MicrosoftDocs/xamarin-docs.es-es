---
title: 'Xamarin.Essentials: Brújula'
description: En este documento se describe la clase Compass de Xamarin.Essentials, que permite supervisar la dirección del norte magnético del dispositivo.
ms.assetid: BF85B0C3-C686-43D9-811A-07DCAF8CDD86
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 54ce725a319e0222179945ece558338c8a152653
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/13/2020
ms.locfileid: "83150122"
---
# <a name="xamarinessentials-compass"></a>Xamarin.Essentials: Brújula

La clase **Compass** permite supervisar la dirección del norte magnético del dispositivo.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-compass"></a>Uso de Compass

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad Compass funciona mediante una llamada a los métodos `Start` y `Stop` para realizar escuchas de los cambios realizados en la brújula. Los cambios se enviarán a través del evento `ReadingChanged`. A continuación se muestra un ejemplo:

```csharp
public class CompassTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public CompassTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Compass.ReadingChanged += Compass_ReadingChanged;
    }

    void Compass_ReadingChanged(object sender, CompassChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: {data.HeadingMagneticNorth} degrees");
        // Process Heading Magnetic North
    }

    public void ToggleCompass()
    {
        try
        {
            if (Compass.IsMonitoring)
              Compass.Stop();
            else
              Compass.Start(speed);
        }
        catch (FeatureNotSupportedException fnsEx)
        {
            // Feature not supported on device
        }
        catch (Exception ex)
        {
            // Some other exception has occurred
        }
    }
}
```

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="platform-implementation-specifics"></a>Detalles de implementación de la plataforma

# <a name="android"></a>[Android](#tab/android)

Android no proporciona una API para recuperar la dirección de la brújula. El acelerómetro y el magnetómetro se usan para calcular la dirección del norte magnético, lo que recomienda Google.

En casos poco habituales, es posible que vea resultados incoherentes porque sea necesario calibrar los sensores, lo que implica mover el dispositivo describiendo un ocho. La mejor manera de hacerlo es abrir Google Maps, pulsar en el punto de la ubicación y seleccionar **Calibrar la brújula**.

La ejecución simultánea de varios sensores desde la aplicación puede ajustar la velocidad del sensor.

## <a name="low-pass-filter"></a>Filtro de paso bajo

Debido a la forma de calcular y actualizar los valores de la brújula de Android, es posible que sea necesario suavizarlos. Se puede aplicar un _filtro de paso bajo_ que calcula el promedio de los valores de seno y coseno de los ángulos y que se puede activar si se sobrecarga el método `Start`, que acepta el parámetro `bool applyLowPassFilter`:

```csharp
Compass.Start(SensorSpeed.UI, applyLowPassFilter: true);
```

Esto solo se aplica a la plataforma Android; el parámetro se ignora en iOS y UWP.  Puede leer más información [aquí](https://github.com/xamarin/Essentials/pull/354#issuecomment-405316860).

--------------

## <a name="api"></a>API

- [Código fuente de Compass](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Compass)
- [Documentación de API para Compass](xref:Xamarin.Essentials.Compass)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Compass-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
