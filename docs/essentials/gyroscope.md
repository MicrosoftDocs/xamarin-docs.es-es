---
title: ''Xamarin.Essentials: Giroscopio" description: ms.assetid: author: ms.author: ms.date: no-loc:
- "Xamarin.Forms"
- "Xamarin.Essentials"

---

# <a name="xamarinessentials-gyroscope"></a>Xamarin.Essentials: Giroscopio

La clase **Gyroscope** permite supervisar el sensor de giroscopio del dispositivo, que es la rotación sobre los tres ejes principales del dispositivo.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-gyroscope"></a>Uso de Gyroscope

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad Gyroscope funciona mediante una llamada a los métodos `Start` y `Stop` para realizar escuchas de los cambios realizados en el giroscopio. Los cambios se enviarán a través del evento `ReadingChanged` en rad/s. A continuación le mostramos un ejemplo de uso:

```csharp

public class GyroscopeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public GyroscopeTest()
    {
        // Register for reading changes.
        Gyroscope.ReadingChanged += Gyroscope_ReadingChanged;
    }

    void Gyroscope_ReadingChanged(object sender, GyroscopeChangedEventArgs e)
    {
        var data = e.Reading;
        // Process Angular Velocity X, Y, and Z reported in rad/s
        Console.WriteLine($"Reading: X: {data.AngularVelocity.X}, Y: {data.AngularVelocity.Y}, Z: {data.AngularVelocity.Z}");
    }

    public void ToggleGyroscope()
    {
        try
        {
            if (Gyroscope.IsMonitoring)
              Gyroscope.Stop();
            else
              Gyroscope.Start(speed);
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

## <a name="api"></a>API

- [Código fuente de Gyroscope](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Gyroscope)
- [Documentación de API para Gyroscope](xref:Xamarin.Essentials.Gyroscope)
