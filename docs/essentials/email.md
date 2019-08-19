---
title: 'Xamarin.Essentials: Correo electrónico'
description: La clase Email de Xamarin.Essentials permite que una aplicación abra la aplicación de correo electrónico predeterminada con información especificada incluido el asunto, el cuerpo y los destinatarios (PARA, CC, CCO).
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.openlocfilehash: f2c275260625fe3842b4473e404f49c71d1d28ae
ms.sourcegitcommit: 9f37dc00c2adab958025ad1cdba9c37f0acbccd0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2019
ms.locfileid: "69012487"
---
# <a name="xamarinessentials-email"></a>Xamarin.Essentials: Correo electrónico

La clase **Email** permite que una aplicación abra la aplicación de correo electrónico predeterminada con información especificada incluido el asunto, el cuerpo y los destinatarios (PARA, CC, CCO).

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

> [!TIP]
> Para usar la API de correo electrónico en iOS, debe ejecutarla en un dispositivo físico, si no, se producirá una excepción.

## <a name="using-email"></a>Uso de Email

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Para usar la funcionalidad de Email se llama al método `ComposeAsync` con un valor `EmailMessage` que contiene información sobre el correo electrónico:

```csharp
public class EmailTest
{
    public async Task SendEmail(string subject, string body, List<string> recipients)
    {
        try
        {
            var message = new EmailMessage
            {
                Subject = subject,
                Body = body,
                To = recipients,
                //Cc = ccRecipients,
                //Bcc = bccRecipients
            };
            await Email.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException fbsEx)
        {
            // Email is not supported on this device
        }
        catch (Exception ex)
        {
            // Some other exception occurred
        }
    }
}
```


## <a name="file-attachments"></a>Datos adjuntos

![Características en versión preliminar](~/media/shared/preview.png)

El envío de archivos por correo electrónico está disponible como versión preliminar experimental en Xamarin.Essentials versión 1.1.0. Esta característica permite que una aplicación envíe archivos por correo electrónico a través de clientes de correo electrónico en el dispositivo. Para habilitar esta característica, establezca la siguiente propiedad en el código de inicio de la aplicación:

```csharp
ExperimentalFeatures.Enable(ExperimentalFeatures.EmailAttachments);
```

Una vez que se haya habilitado la característica, se puede enviar por correo electrónico cualquier archivo. Xamarin.Essentials detectará automáticamente el tipo de archivo (MIME) y solicitará que el archivo se agregue como datos adjuntos. Cada cliente de correo electrónico es diferente y podría admitir únicamente algunas extensiones de archivo o ninguna en absoluto.

A continuación se muestra un ejemplo en el que se escribe texto en el disco y se agrega como datos adjuntos a un correo electrónico:

```csharp
var message = new EmailMessage
{
    Subject = "Hello",
    Body = "World",
};

var fn = "Attachment.txt";
var file = Path.Combine(FileSystem.CacheDirectory, fn);
File.WriteAllText(file, "Hello World");

message.Attachments.Add(new EmailAttachment(file));

await Email.ComposeAsync(message);
```

## <a name="platform-differences"></a>Diferencias entre plataformas

# <a name="androidtabandroid"></a>[Android](#tab/android)

No todos los clientes de correo electrónico para Android admiten `Html`. Puesto que no hay ninguna manera de detectar este problema, recomendamos usar `PlainText` para enviar correos electrónicos.

# <a name="iostabios"></a>[iOS](#tab/ios)

No hay diferencias entre las plataformas.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Solo admite `PlainText`, ya que el formato `BodyFormat` que intenta enviar `Html` producirá una excepción `FeatureNotSupportedException`.

No todos los clientes de correo electrónico admiten el envío de datos adjuntos. Para obtener más información, consulte la [documentación](https://docs.microsoft.com/windows/uwp/contacts-and-calendar/sending-email).

-----

## <a name="api"></a>API

- [Código fuente de Email](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [Documentación de API para Email](xref:Xamarin.Essentials.Email)
