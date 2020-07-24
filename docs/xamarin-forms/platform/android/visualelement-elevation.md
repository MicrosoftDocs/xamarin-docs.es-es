---
title: Elevación de VisualElement en Android
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir el específico de la plataforma Android que controla la elevación de VisualElements en las aplicaciones que tienen como destino la API 21 o posterior.
ms.prod: xamarin
ms.assetid: 5BFD6175-2BBD-41CD-B8F9-521B4750B708
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- ':::no-loc(Xamarin.Forms):::'
- ':::no-loc(Xamarin.Essentials):::'
ms.openlocfilehash: a721f51d3f59bc166a48f5cc3a3eec9712ace837
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2020
ms.locfileid: "86996687"
---
# <a name="visualelement-elevation-on-android"></a>Elevación de VisualElement en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma Android específica se usa para controlar la elevación, o el orden Z, de los elementos visuales en las aplicaciones que tienen como destino la API 21 o posterior. La elevación de un elemento visual determina su orden de dibujo, con elementos visuales con valores Z más altos occluding elementos visuales con valores Z inferiores. Se consume en XAML estableciendo la `VisualElement.Elevation` propiedad adjunta en un `boolean` valor:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific;assembly=:::no-loc(Xamarin.Forms):::.Core"
             Title="Elevation">
    <StackLayout>
        <Grid>
            <Button Text="Button Beneath BoxView" />
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>        
        <Grid Margin="0,20,0,0">
            <Button Text="Button Above BoxView - Click Me" android:VisualElement.Elevation="10"/>
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using :::no-loc(Xamarin.Forms):::.PlatformConfiguration;
using :::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific;
...

public class AndroidElevationPageCS : ContentPage
{
    public AndroidElevationPageCS()
    {
        ...
        var aboveButton = new Button { Text = "Button Above BoxView - Click Me" };
        aboveButton.On<Android>().SetElevation(10);

        Content = new StackLayout
        {
            Children =
            {
                new Grid
                {
                    Children =
                    {
                        new Button { Text = "Button Beneath BoxView" },
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                },
                new Grid
                {
                    Margin = new Thickness(0,20,0,0),
                    Children =
                    {
                        aboveButton,
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                }
            }
        };
    }
}
```

El `Button.On<Android>` método especifica que este específico de la plataforma solo se ejecutará en Android. El `VisualElement.SetElevation` método, en el [`:::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para establecer la elevación del elemento visual en un valor que acepta valores NULL `float` . Además, `VisualElement.GetElevation` se puede usar el método para recuperar el valor de elevación de un elemento visual.

El resultado es que la elevación de los elementos visuales se puede controlar para que los elementos visuales con valores Z más altos tapaba elementos visuales con valores Z inferiores. Por lo tanto, en este ejemplo, el segundo [`Button`](xref::::no-loc(Xamarin.Forms):::.Button) se representa encima de [`BoxView`](xref::::no-loc(Xamarin.Forms):::.BoxView) porque tiene un valor de elevación superior:

![Captura de pantalla de elevación de VisualElement](visualelement-elevation-images/elevation.png)

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.AppCompat)
