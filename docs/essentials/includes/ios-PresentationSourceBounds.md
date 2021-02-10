---
ms.topic: include
author: jamont
ms.author: jamont
ms.date: 01/16/2020
ms.openlocfilehash: ecce7ce05a680e668eefe6b482e7be7314470c85
ms.sourcegitcommit: 2a7bbe9cbee3727ba20ee755c1713bcfdb4d8ecb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/28/2021
ms.locfileid: "98950966"
---
Al solicitar un recurso compartido o abrir el iniciador en iPadOS, tiene la posibilidad de presentar un control emergente. Esto especifica dónde aparecerá la ventana emergente y dónde apuntará directamente una flecha. Esta ubicación suele ser el control que inició la acción. Puede especificar la ubicación con la propiedad `PresentationSourceBounds`:

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

```csharp
await Launcher.OpenAsync(new OpenFileRequest
{
    File = new ReadOnlyFile(file),
    PresentationSourceBounds = DeviceInfo.Platform== DevicePlatform.iOS && DeviceInfo.Idiom == DeviceIdiom.Tablet
                            ? new System.Drawing.Rectangle(0, 20, 0, 0)
                            : System.Drawing.Rectangle.Empty
});
```

Todo lo que se describe aquí funciona igual para `Share` y `Launcher`.

Si usa Xamarin.Forms, podrá pasar `View` y calcular los límites:

```csharp
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
