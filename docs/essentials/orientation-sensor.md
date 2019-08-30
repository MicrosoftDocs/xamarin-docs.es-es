---
title: 'Xamarin.Essentials: OrientationSensor'
description: La clase OrientationSensor permite supervisar la orientación de un dispositivo en un espacio tridimensional.
ms.assetid: F3091D93-E779-41BA-8696-23D296F2F6F5
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 5423872da7966bc7e4bb88e278d76b709f114158
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120714"
---
# <a name="xamarinessentials-orientationsensor"></a>Xamarin.Essentials: OrientationSensor

La clase **OrientationSensor** permite supervisar la orientación de un dispositivo en un espacio tridimensional.

> [!NOTE]
> Esta clase se usa para determinar la orientación de un dispositivo en un espacio tridimensional. Si tiene que determinar si la visualización de vídeo del dispositivo está en modo vertical u horizontal, use la propiedad `Orientation` del objeto `ScreenMetrics` disponible en la clase [`DeviceDisplay`](device-display.md).

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-orientationsensor"></a>Uso de OrientationSensor

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

`OrientationSensor` se habilita mediante una llamada al método `Start` para supervisar los cambios en la orientación del dispositivo y se deshabilita al llamar al método `Stop`. Los cambios se enviarán a través del evento `ReadingChanged`. Este es un uso de ejemplo:

```csharp

public class OrientationSensorTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public OrientationSensorTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        OrientationSensor.ReadingChanged += OrientationSensor_ReadingChanged;
    }

    void OrientationSensor_ReadingChanged(object sender, OrientationSensorChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: X: {data.Orientation.X}, Y: {data.Orientation.Y}, Z: {data.Orientation.Z}, W: {data.Orientation.W}");
        // Process Orientation quaternion (X, Y, Z, and W)
    }

    public void ToggleOrientationSensor()
    {
        try
        {
            if (OrientationSensor.IsMonitoring)
                OrientationSensor.Stop();
            else
                OrientationSensor.Start(speed);
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

Las lecturas de `OrientationSensor` se informan como un [`Quaternion`](xref:System.Numerics.Quaternion) que describe la orientación del dispositivo en función de dos sistemas de coordenadas tridimensionales:

El dispositivo (generalmente un teléfono o tableta) tiene un sistema de coordenadas tridimensional con los siguientes ejes:

- El eje X positivo apunta a la derecha de la visualización en modo vertical.
- El eje Y positivo apunta a la parte de arriba del dispositivo en modo horizontal.
- El eje Z positivo apunta fuera de la pantalla.

El sistema de coordenadas tridimensional de la Tierra tiene estos ejes:

- El eje X positivo es tangente a la superficie de la Tierra y apunta al este.
- El eje Y positivo también es tangente a la superficie de la Tierra y apunta al norte.
- El eje Z positivo es perpendicular a la superficie de la Tierra y apunta hacia arriba.

`Quaternion` describe la rotación del sistema de coordenadas del dispositivo en relación con el sistema de coordenadas de la Tierra.

Un valor `Quaternion` está estrechamente relacionado con la rotación alrededor de un eje. Si un eje de rotación es el vector normalizado (a<sub>x</sub>, a<sub>y</sub>, a<sub>z</sub>) y el ángulo de rotación es Θ, los componentes (X, Y, Z, W) del cuaternión son:

(a<sub>x</sub>·sin(Θ/2), a<sub>y</sub>·sin(Θ/2), a<sub>z</sub>·sin(Θ/2), cos(Θ/2))

Estos son los sistemas de coordenadas de la derecha por lo que, con el pulgar de la mano derecha apuntando en la dirección positiva del eje de rotación, la curva de los dedos indica la dirección de la rotación de los ángulos positivos.

Ejemplos:

- Cuando el dispositivo está pantalla arriba sobre una mesa, con la parte superior del mismo (en el modo vertical) apuntando al norte, los dos sistemas de coordenadas están alineados. El valor `Quaternion` representa el cuaternión de identidad (0, 0, 0, 1). Todas las rotaciones se pueden analizar en relación con esta posición.

- Cuando el dispositivo está pantalla arriba sobre una mesa y la parte superior del mismo (en el modo vertical) apunta al oeste, el valor de `Quaternion` es (0, 0, 0.707, 0.707). El dispositivo se giró 90 grados alrededor del eje Z de la Tierra.

- Cuando el dispositivo se sostiene de manera vertical, con la parte superior (en el modo vertical) apuntando al cielo y la parte posterior orientada al norte, es porque el dispositivo se giró 90 grados alrededor del eje X. El valor de `Quaternion` es (0.707, 0, 0, 0.707).

- Si el dispositivo se coloca de manera tal que el borde izquierdo esté sobre una mesa y la parte superior apunte al norte, es porque el dispositivo se giró &ndash;90 grados alrededor del eje Y (o 90 grados alrededor del eje Y negativo). El valor de `Quaternion` es (0, -0.707, 0, 0.707).

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Código fuente de OrientationSensor](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/OrientationSensor)
- [Documentación de API de OrientationSensor](xref:Xamarin.Essentials.OrientationSensor)
