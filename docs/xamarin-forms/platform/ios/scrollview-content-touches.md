---
title: El contenido de ScrollView se toca en iOS
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir el específico de la plataforma iOS que controla si un ScrollView controla un gesto táctil o lo pasa a su contenido.
ms.prod: xamarin
ms.assetid: 99F823DB-B379-40F0-A343-A9783C341120
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e028c506745bfd61aaff8e530a4f13d2429864ff
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373919"
---
# <a name="scrollview-content-touches-on-ios"></a>El contenido de ScrollView se toca en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Un temporizador implícito se desencadena cuando un gesto táctil comienza en un [`ScrollView`](xref:Xamarin.Forms.ScrollView) en iOS y el `ScrollView` decide, en función de la acción del usuario dentro del intervalo del temporizador, si debe controlar el gesto o pasarlo a su contenido. De forma predeterminada, iOS `ScrollView` retrasa los toques de contenido, pero puede causar problemas en algunas circunstancias con el `ScrollView` contenido que no gana el gesto cuando debería. Por lo tanto, este control es específico de la plataforma, tanto si `ScrollView` controla un gesto táctil como si lo pasa a su contenido. Se consume en XAML estableciendo la `ScrollView.ShouldDelayContentTouches` propiedad adjunta en un `boolean` valor:

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <MasterDetailPage.Master>
        <ContentPage Title="Menu" BackgroundColor="Blue" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <ContentPage>
            <ScrollView x:Name="scrollView" ios:ScrollView.ShouldDelayContentTouches="false">
                <StackLayout Margin="0,20">
                    <Slider />
                    <Button Text="Toggle ScrollView DelayContentTouches" Clicked="OnButtonClicked" />
                </StackLayout>
            </ScrollView>
        </ContentPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

scrollView.On<iOS>().SetShouldDelayContentTouches(false);
```

El `ScrollView.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. El `ScrollView.SetShouldDelayContentTouches` método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se utiliza para controlar si un control [`ScrollView`](xref:Xamarin.Forms.ScrollView) controla un gesto táctil o lo pasa a su contenido. Además, el `SetShouldDelayContentTouches` método se puede utilizar para alternar el retraso del contenido mediante una llamada al `ShouldDelayContentTouches` método para devolver si se retrasa el contenido de los toques:

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

El resultado es que [`ScrollView`](xref:Xamarin.Forms.ScrollView) puede deshabilitar el retraso de la recepción de contenido que recibe los toques, de modo que en este escenario [`Slider`](xref:Xamarin.Forms.Slider) reciba el gesto en lugar de la [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) Página de [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) :

[![El contenido de retraso de ScrollView toca la plataforma específica](scrollview-content-touches-images/scrollview-delay-content-touches.png)](scrollview-content-touches-images/scrollview-delay-content-touches-large.png#lightbox "ScrollView retrasar el contenido Platform-Specific")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)