---
title: "Xamarin.Essentials: barómetro" description: "La clase Barometer de Xamarin.Essentials permite supervisar el sensor del barómetro del dispositivo, que mide la presión."
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1 author: jamesmontemagno ms.author: jamont ms.date: 11/04/2018 no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="xamarinessentials-barometer"></a>Xamarin.Essentials: Barómetro

La clase **Barometer** permite supervisar el sensor del barómetro del dispositivo, que mide la presión.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-barometer"></a>Uso de Barometer

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

Barometer funciona mediante una llamada a los métodos `Start` y `Stop` para escuchar los cambios en la lectura de presión del barómetro en hectopascales. Los cambios se enviarán a través del evento `ReadingChanged`. A continuación le mostramos un ejemplo de uso:

```csharp

public class BarometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public BarometerTest()
    {
        // Register for reading changes.
        Barometer.ReadingChanged += Barometer_ReadingChanged;
    }

    void Barometer_ReadingChanged(object sender, BarometerChangedEventArgs e)
    {
        var data = e.Reading;
        // Process Pressure
        Console.WriteLine($"Reading: Pressure: {data.PressureInHectopascals} hectopascals");
    }

    public void ToggleBarometer()
    {
        try
        {
            if (Barometer.IsMonitoring)
              Barometer.Stop();
            else
              Barometer.Start(speed);
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

## <a name="platform-implementation-specifics"></a>Detalles de implementación de la plataforma

# <a name="android"></a>[Android](#tab/android)

Sin detalles de implementación específicos para la plataforma.

# <a name="ios"></a>[iOS](#tab/ios)

Esta API usa [CMAltimeter](https://developer.apple.com/documentation/coremotion/cmaltimeter#//apple_ref/occ/cl/CMAltimeter) para supervisar los cambios de presión, una característica de hardware que se agregó en iPhone 6 y dispositivos más nuevos. Se producirá una excepción `FeatureNotSupportedException` en los dispositivos que no admiten el altímetro.

No se usa `SensorSpeed` porque no se admite en iOS.

# <a name="uwp"></a>[UWP](#tab/uwp)

Sin detalles de implementación específicos para la plataforma.

-----

## <a name="api"></a>API

- [Código fuente de Barometer](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Barometer)
- [Documentación de API para Barometer](xref:Xamarin.Essentials.Barometer)
