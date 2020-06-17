---
title: "Asistentes de plataforma de doble pantalla de Xamarin.Forms" description: "En esta guía se explica cómo usar la clase DualScreenHelper de Xamarin.Forms para optimizar la experiencia de la aplicación para dispositivos de doble pantalla como Surface Duo y Surface Neo."
ms.prod: xamarin ms.assetid: 5aa184c2-5611-427d-85c7-1c56486c3e1b ms.technology: xamarin-forms author: davidortinau ms.author: daortin ms.date: 02/08/2020 no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="xamarinforms-dual-screen-platform-helpers"></a>Asistentes de plataforma de doble pantalla de Xamarin.Forms

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)

La clase `DualScreenHelper` se puede usar para detectar si un dispositivo admite el modo Imagen en imagen y, después, permite abrir una página `ContentPage` como ventana Imagen en imagen. Si se ejecuta en un dispositivo Neo, la página se abrirá en la barra táctil en el modo de redacción.

## <a name="properties"></a>Propiedades

- `HasCompactModeSupport` se usa para comprobar si la plataforma admite que se abra una página `ContentPage` en modo Compact.
- `OpenCompactMode` abre una página `ContentPage` en modo Compact si la plataforma admite esta opción.

## <a name="example"></a>Ejemplo

```csharp
async void OpenCompactWindowClicked(object sender, EventArgs e)
{
    if (!DualScreenHelper.HasCompactModeSupport())
    {
        await DisplayAlert("Unsupported", "This platform doesn't support this feature", "Ok");
        return;
    }

    ContentPage page = new ContentPage() { BackgroundColor = Color.Purple };

    var button = new Button()
    {
        Text = "Close this Window"
    };

    var layout = new StackLayout()
    {
        Children =
        {
            new Label(){ Text = "Welcome to your Compact Mode Window" },
            button
        },
        BackgroundColor = Color.Yellow,
        HorizontalOptions = LayoutOptions.Fill,
        VerticalOptions = LayoutOptions.Fill
    };

    page.Content = new ScrollView() { Content = layout };
    var args = await DualScreenHelper.OpenCompactMode(page);

    button.Command = new Command(async () =>
    {
        await args.CloseAsync();
    });
}
```
