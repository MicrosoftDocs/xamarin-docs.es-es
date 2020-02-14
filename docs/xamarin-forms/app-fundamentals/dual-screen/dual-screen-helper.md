---
title: DualScreenHelper de Xamarin.Forms
description: En esta guía se explica cómo usar DualScreenHelper de Xamarin.Forms para optimizar la experiencia de la aplicación para dispositivos de doble pantalla como Surface Duo y Surface Neo.
ms.prod: xamarin
ms.assetid: 5aa184c2-5611-427d-85c7-1c56486c3e1b
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: b06e105560de635a21b42e8366bb47842372cf6a
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145549"
---
# <a name="xamarinforms-dualscreenhelper"></a>DualScreenHelper de Xamarin.Forms
[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/UserInterface/DualScreenDemos)
_DualScreenHelper se puede usar para detectar si un dispositivo admite el modo Imagen en imagen y, después, permite abrir una página de contenido en una ventana Imagen en imagen. Si se ejecuta en un dispositivo Neo, la página se abrirá en la barra táctil en el modo de redacción._
## <a name="properties"></a>Propiedades
- `HasCompactModeSupport` se usa para comprobar si la plataforma admite que se abra una página de contenido en modo Compacto.
- `OpenCompactMode` si la plataforma lo admite, se abrirá una página de contenido en modo Compacto.
## <a name="example-usage"></a>Ejemplo de uso
```c#
async void OpenCompactWindowClicked(object sender, EventArgs e)
{
    if(!DualScreenHelper.HasCompactModeSupport())
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