---
title: 'Xamarin.Essentials: Marcador telefónico'
description: La clase PhoneDialer de Xamarin.Essentials permite que una aplicación abra un número de teléfono en el marcador telefónico.
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 07/02/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9bd281a61fd53ef3f6d0d3d2307f78a218f33cf4
ms.sourcegitcommit: db423d51356cf5a2dfa1b3925204797b1baf3cd9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2020
ms.locfileid: "92734771"
---
# <a name="no-locxamarinessentials-phone-dialer"></a>Xamarin.Essentials: Marcador telefónico

La clase **PhoneDialer** permite que una aplicación abra un número de teléfono en el marcador telefónico.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

# <a name="android"></a>[Android](#tab/android)

Si la versión de Android de destino del proyecto se establece en **Android 11 (R API 30)** , debe actualizar el manifiesto de Android con las consultas que se usan con los nuevos [requisitos de visibilidad de los paquetes](https://developer.android.com/preview/privacy/package-visibility).

Abra el archivo **AndroidManifest.xml** de la carpeta **Propiedades** y agregue lo siguiente dentro del nodo **manifest** :

```xml
<queries>
  <intent>
    <action android:name="android.intent.action.DIAL" />
    <data android:scheme="tel"/>
  </intent>
</queries>
```

# <a name="ios"></a>[iOS](#tab/ios)

No se requiere configuración adicional.

# <a name="uwp"></a>[UWP](#tab/uwp)

No hay diferencias entre las plataformas.

-----

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

- [Código fuente del marcador telefónico](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/PhoneDialer)
- [Documentación de API para PhoneDialer](xref:Xamarin.Essentials.PhoneDialer)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Phone-Dialer-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
