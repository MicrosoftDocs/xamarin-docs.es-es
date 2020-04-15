---
title: 'Xamarin.Essentials: detección de agitaciones'
description: La clase Accelerometer de Xamarin.Essentials permite detectar movimientos de agitación en el dispositivo.
ms.assetid: 07513D32-120F-4F12-8757-A47802A8027B
author: jamesmontemagno
ms.author: jamont
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: d2aef2c32b3fe2815f1176779a8c28ae8d421ed1
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "67832598"
---
# <a name="xamarinessentials-detect-shake"></a>Xamarin.Essentials: detección de agitaciones

La clase **[Accelerometer](accelerometer.md)** permite supervisar el sensor del acelerómetro del dispositivo, que indica la aceleración del dispositivo en un espacio tridimensional. Además, le permite registrar eventos que se realizarán cuando el usuario agite el dispositivo.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-detect-shake"></a>Uso de la detección de agitaciones

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Para detectar la agitación del dispositivo, debe usar la funcionalidad Accelerometer mediante una llamada a los métodos `Start` y `Stop` para realizar cambios en la aceleración y para detectar movimientos de agitación. Siempre que se detecte agitación, se desencadenará un evento `ShakeDetected`. Se recomienda usar `Game` o faster para `SensorSpeed`. A continuación le mostramos un ejemplo de uso:

```csharp

public class DetectShakeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Game;

    public DetectShakeTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Accelerometer.ShakeDetected  += Accelerometer_ShakeDetected ;
    }

    void Accelerometer_ShakeDetected (object sender, EventArgs e)
    {
        // Process shake event
    }

    public void ToggleAccelerometer()
    {
        try
        {
            if (Accelerometer.IsMonitoring)
              Accelerometer.Stop();
            else
              Accelerometer.Start(speed);
        }
        catch (FeatureNotSupportedException fnsEx)
        {
            // Feature not supported on device
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="implementation-details"></a>Detalles de implementación

La API para la detección de agitaciones usa lecturas sin procesar del acelerómetro para calcular la aceleración. Utiliza un mecanismo de cola simple para detectar si tres cuartos de los eventos recientes del acelerómetro se han producido durante el último medio segundo. La aceleración se calcula añadiendo el cuadrado de las lecturas X, Y y Z del acelerómetro y comparándolo con un umbral determinado.

## <a name="api"></a>API

- [Código fuente de Accelerometer](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Accelerometer)
- [Documentación de API de Accelerometer](xref:Xamarin.Essentials.Accelerometer)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Detect-Shake-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
