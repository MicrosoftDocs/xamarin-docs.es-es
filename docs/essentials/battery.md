---
title: 'Xamarin.Essentials: Batería'
description: En este documento se describe la clase Battery de Xamarin.Essentials, que permite comprobar la información de la batería del dispositivo y supervisar los cambios.
ms.assetid: 47EB26D8-8C62-477B-A13C-6977F74E6E43
author: jamesmontemagno
ms.author: jamont
ms.date: 01/22/2019
ms.custom: video
ms.openlocfilehash: cba17707f9129feecc618c9a7c2f144ad40f0168
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "70756921"
---
# <a name="xamarinessentials-battery"></a>Xamarin.Essentials: Batería

La clase **Battery** le permite comprobar la información sobre la batería del dispositivo y supervisar los cambios. Además, ofrece información sobre el estado de ahorro de energía del dispositivo, que indica si este se está ejecutando en el modo de bajo consumo. Las aplicaciones deben evitar el procesamiento en segundo plano si el estado de ahorro de energía del dispositivo está activado.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

Para acceder a la funcionalidad de **Battery**, se requiere la siguiente configuración específica para la plataforma.

# <a name="android"></a>[Android](#tab/android)

El permiso `Battery` es necesario y se debe configurar en el proyecto Android. Se puede agregar de las siguientes maneras:

Abra el archivo **AssemblyInfo.cs** de la carpeta **Propiedades** y agregue lo siguiente:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.BatteryStats)]
```

O BIEN, actualice el manifiesto de Android:

Abra el archivo **AndroidManifest.xml** de la carpeta **Propiedades** y agregue lo siguiente dentro del nodo **manifest**.

```xml
<uses-permission android:name="android.permission.BATTERY_STATS" />
```

O haga clic con el botón derecho en el proyecto de Android y abra las propiedades del proyecto. En **Manifiesto de Android**, busque el área **Permisos requeridos:** y active el permiso **Battery** (Batería). Esto actualizará automáticamente el archivo **AndroidManifest.xml**.

# <a name="ios"></a>[iOS](#tab/ios)

No se requiere configuración adicional.

# <a name="uwp"></a>[UWP](#tab/uwp)

No se requiere configuración adicional.

-----

## <a name="using-battery"></a>Uso de Battery

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Compruebe la información actual de la batería:

```csharp
var level = Battery.ChargeLevel; // returns 0.0 to 1.0 or 1.0 when on AC or no battery.

var state = Battery.State;

switch (state)
{
    case BatteryState.Charging:
        // Currently charging
        break;
    case BatteryState.Full:
        // Battery is full
        break;
    case BatteryState.Discharging:
    case BatteryState.NotCharging:
        // Currently discharging battery or not being charged
        break;
    case BatteryState.NotPresent:
        // Battery doesn't exist in device (desktop computer)
    case BatteryState.Unknown:
        // Unable to detect battery state
        break;
}

var source = Battery.PowerSource;

switch (source)
{
    case BatteryPowerSource.Battery:
        // Being powered by the battery
        break;
    case BatteryPowerSource.AC:
        // Being powered by A/C unit
        break;
    case BatteryPowerSource.Usb:
        // Being powered by USB cable
        break;
    case BatteryPowerSource.Wireless:
        // Powered via wireless charging
        break;
    case BatteryPowerSource.Unknown:
        // Unable to detect power source
        break;
}
```

Cada vez que se cambia cualquiera de las propiedades de la batería, se desencadena un evento:

```csharp
public class BatteryTest
{
    public BatteryTest()
    {
        // Register for battery changes, be sure to unsubscribe when needed
        Battery.BatteryInfoChanged += Battery_BatteryInfoChanged;
    }

    void Battery_BatteryInfoChanged(object sender, BatteryInfoChangedEventArgs   e)
    {
        var level = e.ChargeLevel;
        var state = e.State;
        var source = e.PowerSource;
        Console.WriteLine($"Reading: Level: {level}, State: {state}, Source: {source}");
    }
}
```

Es posible poner los dispositivos que usan baterías en el modo de ahorro en caso de baja energía. A veces, los dispositivos cambian automáticamente a este modo, por ejemplo, cuando la batería cae por debajo del 20 % de su capacidad. El sistema operativo responde al modo de ahorro de energía reduciendo las actividades que tienden a agotar la batería. Para ayudar, las aplicaciones pueden evitar el procesamiento en segundo plano u otras actividades de alta potencia cuando el modo de ahorro de energía está activado.

También puede conocer el estado de ahorro de energía actual del dispositivo con la propiedad estática `Battery.EnergySaverStatus`:

```csharp
// Get energy saver status
var status = Battery.EnergySaverStatus;
```

Esta propiedad devuelve un miembro de la enumeración `EnergySaverStatus`, que es `On`, `Off` o `Unknown`. Si la propiedad devuelve `On`, la aplicación debe evitar el procesamiento en segundo plano o cualquier otra actividad que pueda tener un consumo energético grande.

La aplicación también debe instalar un controlador de eventos. La clase **Battery** expone un evento que se desencadena cuando cambia el estado de ahorro de energía:

```csharp
public class EnergySaverTest
{
    public EnergySaverTest()
    {
        // Subscribe to changes of energy-saver status
        Battery.EnergySaverStatusChanged += OnEnergySaverStatusChanged;
    }

    private void OnEnergySaverStatusChanged(EnergySaverStatusChangedEventArgs e)
    {
        // Process change
        var status = e.EnergySaverStatus;
    }
}
```

Si el estado de ahorro de energía cambia a `On`, la aplicación debe detener el procesamiento en segundo plano. Si el estado cambia a `Unknown` o `Off`, la aplicación puede reanudar el procesamiento en segundo plano.

## <a name="platform-differences"></a>Diferencias entre plataformas

# <a name="android"></a>[Android](#tab/android)

No hay diferencias entre las plataformas.

# <a name="ios"></a>[iOS](#tab/ios)

- Se debe usar el dispositivo para probar las API. 
- Solo devolverá `AC` o `Battery` para `PowerSource`.

# <a name="uwp"></a>[UWP](#tab/uwp)

- Solo devolverá `AC` o `Battery` para `PowerSource`.

-----

## <a name="api"></a>API

- [Código fuente de Battery](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Battery)
- [Documentación de API para Battery](xref:Xamarin.Essentials.Battery)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Battery-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
