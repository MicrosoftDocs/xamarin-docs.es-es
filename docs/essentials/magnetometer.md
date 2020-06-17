---
title: "Xamarin.Essentials: Magnetómetro" description: "La clase Magnetometer de Xamarin.Essentials permite supervisar el sensor de magnetómetro del dispositivo, que indica la orientación del dispositivo con respecto al campo magnético de la Tierra."
ms.assetid: 64DD0D41-03E2-40DD-9EC8-101CA0ED852B author: jamesmontemagno ms.author: jamont ms.date: 11/04/2018 no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="xamarinessentials-magnetometer"></a>Xamarin.Essentials: Magnetómetro

La clase **Magnetometer** permite supervisar el sensor de magnetómetro del dispositivo, que indica la orientación del dispositivo con respecto al campo magnético de la Tierra.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-magnetometer"></a>Uso de Magnetometer

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad Magnetometer funciona mediante una llamada a los métodos `Start` y `Stop` para realizar escuchas de los cambios en el magnetómetro. Los cambios se enviarán a través del evento `ReadingChanged`. A continuación le mostramos un ejemplo de uso:

```csharp

public class MagnetometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public MagnetometerTest()
    {
        // Register for reading changes.
        Magnetometer.ReadingChanged += Magnetometer_ReadingChanged;
    }

    void Magnetometer_ReadingChanged(object sender, MagnetometerChangedEventArgs e)
    {
        var data = e.Reading;
        // Process MagneticField X, Y, and Z
        Console.WriteLine($"Reading: X: {data.MagneticField.X}, Y: {data.MagneticField.Y}, Z: {data.MagneticField.Z}");
    }

    public void ToggleMagnetometer()
    {
        try
        {
            if (Magnetometer.IsMonitoring)
              Magnetometer.Stop();
            else
              Magnetometer.Start(speed);
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

Todos los datos se devuelven en µT (microteslas).

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Código fuente de Magnetometer](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Magnetometer)
- [Documentación de API para Magnetometer](xref:Xamarin.Essentials.Magnetometer)
