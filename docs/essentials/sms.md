---
title: 'Xamarin.Essentials: SMS'
description: La clase SMS en Xamarin.Essentials permite que una aplicación abra la aplicación SMS predeterminada con un mensaje especificado para enviar a un destinatario.
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: a7b52bac0e9e2061cf9ff277db044ab232b1e9e5
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "61347916"
---
# <a name="xamarinessentials-sms"></a>Xamarin.Essentials: SMS

La clase **SMS** permite que una aplicación abra la aplicación SMS predeterminada con un mensaje especificado para enviar un destinatario.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-sms"></a>Uso de SMS

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad SMS funciona mediante una llamada al método `ComposeAsync`, un `SmsMessage` que contiene el destinatario del mensaje y el cuerpo del mismo, ambos opcionales.

```csharp
public class SmsTest
{
    public async Task SendSms(string messageText, string recipient)
    {
        try
        {
            var message = new SmsMessage(messageText, new []{ recipient });
            await Sms.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException ex)
        {
            // Sms is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

Si quiere, puede pasar varios destinatarios a un `SmsMessage`:

```csharp
public class SmsTest
{
    public async Task SendSms(string messageText, string[] recipients)
    {
        try
        {
            var message = new SmsMessage(messageText, recipients);
            await Sms.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException ex)
        {
            // Sms is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

## <a name="api"></a>API

- [Código fuente de SMS](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Sms)
- [Documentación de API para SMS](xref:Xamarin.Essentials.Sms)
