---
title: 'Xamarin.Essentials: Compartir'
description: La clase Share de Xamarin.Essentials permite que una aplicación comparta datos como texto y vínculos web con otras aplicaciones del dispositivo.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 08/20/2019
ms.custom: video
ms.openlocfilehash: b889573c0cefbd692b1e879c1612a143d4bb7c99
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120751"
---
# <a name="xamarinessentials-share"></a>Xamarin.Essentials: Compartir

La clase **Share** permite que una aplicación comparta datos como texto y vínculos web con otras aplicaciones del dispositivo.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-share"></a>Uso de Share

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Share funciona mediante una llamada al método `RequestAsync` con una carga de solicitud de datos que incluye información para compartir con otras aplicaciones. Se pueden combinar texto y Uri, cada plataforma controlara el filtrado basado en el contenido.

```csharp

public class ShareTest
{
    public async Task ShareText(string text)
    {
        await Share.RequestAsync(new ShareTextRequest
            {
                Text = text,
                Title = "Share Text"
            });
    }

    public async Task ShareUri(string uri)
    {
        await Share.RequestAsync(new ShareTextRequest
            {
                Uri = uri,
                Title = "Share Web Link"
            });
    }
}
```

Interfaz de usuario para compartir con una aplicación externa que aparece cuando se realiza la solicitud:

![Compartir](images/share.png)

## <a name="files"></a>Archivos

Esta característica permite que una aplicación comparta archivos con otras aplicaciones del dispositivo. Xamarin.Essentials detectará automáticamente el tipo de archivo (MIME) y solicitará el uso compartido. Cada plataforma podría admitir únicamente determinadas extensiones de archivo.

A continuación se muestra un ejemplo en el que se escribe texto en el disco y se comparte con otras aplicaciones:

```csharp
var fn =  "Attachment.txt";
var file = Path.Combine(FileSystem.CacheDirectory, fn);
File.WriteAllText(file, "Hello World");

await Share.RequestAsync(new ShareFileRequest
{
    Title = Title,
    File = new ShareFile(file)
});
```

## <a name="platform-differences"></a>Diferencias entre plataformas

# <a name="androidtabandroid"></a>[Android](#tab/android)

- La propiedad `Subject` se usa para el asunto deseado de un mensaje.

# <a name="iostabios"></a>[iOS](#tab/ios)

- `Subject` no se usa.
- `Title` no se usa.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

- El valor predeterminado de `Title` será el nombre de la aplicación si no se establece.
- `Subject` no se usa.

-----


## <a name="api"></a>API

- [Código fuente de Share](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Share)
- [Documentación de API para Share](xref:Xamarin.Essentials.Share)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Share-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
