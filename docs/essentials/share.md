---
title: 'Xamarin.Essentials: Compartir'
description: La clase Share de Xamarin.Essentials permite que una aplicación comparta datos como texto y vínculos web con otras aplicaciones del dispositivo.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 93ad745790a746924f7037e490985c53c332c089
ms.sourcegitcommit: dac04cec56290fb19034f3e135708f6966a8f035
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2020
ms.locfileid: "92169922"
---
# <a name="no-locxamarinessentials-share"></a>Xamarin.Essentials: Compartir

La clase **Share** permite que una aplicación comparta datos como texto y vínculos web con otras aplicaciones del dispositivo.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-share"></a>Uso de Share

Agregue una referencia a Xamarin.Essentials en la clase:

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

## <a name="presentation-location"></a>Ubicación de la presentación

Al solicitar un recurso compartido en iPadOS, tiene la posibilidad de presentar un control emergente. Esto especifica dónde aparecerá la ventana emergente y dónde apuntará directamente una flecha. Esta ubicación suele ser el control que inició la acción. Puede especificar la ubicación con la propiedad `PresentationSourceBounds`:

```csharp
await Share.RequestAsync(new ShareFileRequest
{
    Title = Title,
    File = new ShareFile(file),
    PresentationSourceBounds = DeviceInfo.Platform== DevicePlatform.iOS && DeviceInfo.Idiom == DeviceIdiom.Tablet
                            ? new System.Drawing.Rectangle(0, 20, 0, 0)
                            : System.Drawing.Rectangle.Empty
});
```

Si usa Xamarin.Forms, podrá pasar `View` y calcular los límites:


```
public static class ViewHelpers
{
    public static Rectangle GetAbsoluteBounds(this Xamarin.Forms.View element)
    {
        Element looper = element;

        var absoluteX = element.X + element.Margin.Top;
        var absoluteY = element.Y + element.Margin.Left;

        // Add logic to handle titles, headers, or other non-view bars

        while (looper.Parent != null)
        {
            looper = looper.Parent;
            if (looper is Xamarin.Forms.View v)
            {
                absoluteX += v.X + v.Margin.Top;
                absoluteY += v.Y + v.Margin.Left;
            }
        }

        return new Rectangle(absoluteX, absoluteY, element.Width, element.Height);
    }

    public static System.Drawing.Rectangle ToSystemRectangle(this Rectangle rect) =>
        new System.Drawing.Rectangle((int)rect.X, (int)rect.Y, (int)rect.Width, (int)rect.Height);
}
```

Después, se puede usar al llamar a `RequstAsync`:

```csharp
public Command<Xamarin.Forms.View> ShareCommand { get; } = new Command<Xamarin.Forms.View>(Share);
async void Share(Xamarin.Forms.View element)
{
    try
    {
        Analytics.TrackEvent("ShareWithFriends");
        var bounds = element.GetAbsoluteBounds();

        await Share.RequestAsync(new ShareTextRequest
        {
            PresentationSourceBounds = bounds.ToSystemRectangle(),
            Title = "Title",
            Text = "Text"
        });
    }
    catch (Exception)
    {
        // Handle exception that share failed
    }
}
```

Puede pasar el elemento que realiza la llamada cuando se desencadene `Command`:

```xml
<Button Text="Share"
        Command="{Binding ShareWithFriendsCommand}"
        CommandParameter="{Binding Source={RelativeSource Self}}"/>
```

## <a name="platform-differences"></a>Diferencias entre plataformas

# <a name="android"></a>[Android](#tab/android)

- La propiedad `Subject` se usa para el asunto deseado de un mensaje.

# <a name="ios"></a>[iOS](#tab/ios)

- `Subject` no se usa.
- `Title` no se usa.

# <a name="uwp"></a>[UWP](#tab/uwp)

- El valor predeterminado de `Title` será el nombre de la aplicación si no se establece.
- `Subject` no se usa.

-----

## <a name="api"></a>API

- [Código fuente de Share](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Share)
- [Documentación de API para Share](xref:Xamarin.Essentials.Share)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Share-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
