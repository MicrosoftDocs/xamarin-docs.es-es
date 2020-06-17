---
title: "Xamarin.Essentials: SMS'' description: "La clase Sms de Xamarin.Essentials permite que una aplicación abra la aplicación SMS predeterminada con un mensaje especificado para enviarlo a un destinatario."
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58 author: jamesmontemagno ms.custom: video ms.author: jamont ms.date: 11/04/2018 no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="xamarinessentials-sms"></a>Xamarin.Essentials: SMS

La clase **SMS** permite que una aplicación abra la aplicación SMS predeterminada con un mensaje especificado para enviar un destinatario.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-sms"></a>Uso de SMS

Agregue una referencia a Xamarin.Essentials en la clase:

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

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/SMS-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
