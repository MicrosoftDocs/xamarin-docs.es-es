---
title: "Xamarin.Essentials: Marcador telefónico" description "La clase PhoneDialer de Xamarin.Essentials permite que una aplicación abra un número de teléfono en el marcador telefónico" ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F author: jamesmontemagno ms.custom: video ms.author: jamont ms.date: 07/02/2019 no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials: Marcador telefónico

La clase **PhoneDialer** permite que una aplicación abra un número de teléfono en el marcador telefónico.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-phone-dialer"></a>Uso del marcador telefónico

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad de marcador telefónico funciona mediante una llamada al método `Open` con un número de teléfono con el que abrir el marcador. Cuando se solicita `Open`, la API intentará automáticamente dar formato al número en función del código de país, si se especifica.

```csharp
public class PhoneDialerTest
{
    public void PlacePhoneCall(string number)
    {
        try
        {
            PhoneDialer.Open(number);
        }
        catch (ArgumentNullException anEx)
        {
            // Number was null or white space
        }
        catch (FeatureNotSupportedException ex)
        {
            // Phone Dialer is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

## <a name="api"></a>API

- [Código fuente del marcador telefónico](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/PhoneDialer)
- [Documentación de API para PhoneDialer](xref:Xamarin.Essentials.PhoneDialer)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Phone-Dialer-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
