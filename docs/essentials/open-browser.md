---
title: "Xamarin.Essentials: apertura del explorador" description: "La clase Browser de Xamarin.Essentials permite que una aplicación abra un vínculo web en el explorador optimizado preferido del sistema o en el explorador externo."
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33 author: jamesmontemagno ms.author: jamont ms.date: 04/02/2019 ms.custom: video no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="xamarinessentials-browser"></a>Xamarin.Essentials: Explorador

La clase **Browser** permite que una aplicación abra un vínculo web en el explorador optimizado preferido del sistema o en el explorador externo.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-browser"></a>Uso de Browser

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad Browser funciona mediante una llamada al método `OpenAsync` con `Uri` y `BrowserLaunchMode`.

```csharp

public class BrowserTest
{
    public async Task OpenBrowser(Uri uri)
    {
        await Browser.OpenAsync(uri, BrowserLaunchMode.SystemPreferred);
    }
}
```

Este método devuelve un valor después de que el usuario _inicie_ el explorador, a veces sin que tenga que llegar a _cerrarlo_.  El resultado `bool` indica si el inicio ha sido correcto o no.

## <a name="customization"></a>Personalización

Al usar el explorador preferido por el sistema, tiene disponibles varias opciones de personalización para iOS y Android, por ejemplo, `TitleMode` (solo en Android), preferencias para las opciones de color para la `Toolbar` (en iOS y Android) y `Controls` que se muestran (solo en iOS).

Estas opciones se especifican usando `BrowserLaunchOptions` al llamar a `OpenAsync`.

```csharp
await Browser.OpenAsync(uri, new BrowserLaunchOptions
                {
                    LaunchMode = BrowserLaunchMode.SystemPreferred,
                    TitleMode = BrowserTitleMode.Show,
                    PreferredToolbarColor = Color.AliceBlue,
                    PreferredControlColor = Color.Violet
                });
```

![Opciones del explorador](images/browser-options.png)

## <a name="platform-implementation-specifics"></a>Detalles de implementación de la plataforma

# <a name="android"></a>[Android](#tab/android)

El modo de inicio determina cómo se inicia el explorador:

## <a name="system-preferred"></a>Preferencia del sistema

Se intenta usar [Pestañas personalizadas](https://developer.chrome.com/multidevice/android/customtabs) para cargar el Uri y mantener el reconocimiento de la navegación.

## <a name="external"></a>Externo

Se usará `Intent` para solicitar que se abra el URI a través del explorador normal del sistema.

# <a name="ios"></a>[iOS](#tab/ios)

## <a name="system-preferred"></a>Preferencia del sistema

[SFSafariViewController](xref:SafariServices.SFSafariViewController) se usa para cargar el URI y mantener el reconocimiento de la navegación.

## <a name="external"></a>Externo

El `OpenUrl` estándar de la aplicación principal se usa para iniciar el explorador predeterminado fuera de la aplicación.

# <a name="uwp"></a>[UWP](#tab/uwp)

El explorador predeterminado del usuario se usará siempre, independientemente de `BrowserLaunchMode`.

--------------

## <a name="api"></a>API

- [Código fuente de Browser](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Browser)
- [Documentación de API para Browser](xref:Xamarin.Essentials.Browser)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Open-Browser-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
