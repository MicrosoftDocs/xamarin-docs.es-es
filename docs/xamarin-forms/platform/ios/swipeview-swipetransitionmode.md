---
title: SwipeView deslizar el modo de transición en iOS
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir la plataforma específica de iOS que controla la transición que se usa al abrir un SwipeView.
ms.prod: xamarin
ms.assetid: C667F24C-BAD8-47E0-9285-D3546BEF703B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 01e3d98fe92a69337d97f724d6027e33a9684515
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371409"
---
# <a name="swipeview-swipe-transition-mode-on-ios"></a>SwipeView deslizar el modo de transición en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este control específico de la plataforma iOS controla la transición que se usa al abrir `SwipeView` . Se consume en XAML estableciendo la `SwipeView.SwipeTransitionMode` propiedad Bindable en un valor de la `SwipeTransitionMode` enumeración:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SwipeView ios:SwipeView.SwipeTransitionMode="Drag">
            <SwipeView.LeftItems>
                <SwipeItems>
                    <SwipeItem Text="Delete"
                               IconImageSource="delete.png"
                               BackgroundColor="LightPink"
                               Invoked="OnDeleteSwipeItemInvoked" />
                </SwipeItems>
            </SwipeView.LeftItems>
            <!-- Content -->
        </SwipeView>
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

SwipeView swipeView = new Xamarin.Forms.SwipeView();
swipeView.On<iOS>().SetSwipeTransitionMode(SwipeTransitionMode.Drag);
// ...
```

El `SwipeView.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. El `SwipeView.SetSwipeTransitionMode` método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se utiliza para controlar la transición que se utiliza al abrir `SwipeView` . La `SwipeTransitionMode` enumeración proporciona dos valores posibles:

- `Reveal` indica que los elementos que se van a deslizar se revelarán cuando se deslice el contenido por el dedo `SwipeView` y es el valor predeterminado de la `SwipeView.SwipeTransitionMode` propiedad.
- `Drag` indica que los elementos que se van a deslizar se arrastrarán hasta la vista a medida que `SwipeView` se deslice el contenido.

Además, el `SwipeView.GetSwipeTransitionMode` método se puede utilizar para devolver el `SwipeTransitionMode` que se aplica a `SwipeView` .

El resultado es que un `SwipeTransitionMode` valor especificado se aplica a `SwipeView` , que controla la transición que se utiliza al abrir `SwipeView` :

[![Captura de pantalla de SwipeView SwipeTransitionModes, en iOS](swipeview-swipetransitionmode-images/swipetransitionmode.png "SwipeTransitionModes en iOS")](swipeview-swipetransitionmode-images/swipetransitionmode-large.png#lightbox "SwipeTransitionModes en iOS")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)