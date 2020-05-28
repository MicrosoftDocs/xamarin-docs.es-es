---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c420fe65b020067169230dd06dbcd5ce65c036ab
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128627"
---
# <a name="swipeview-swipe-transition-mode-on-android"></a>SwipeView deslizar el modo de transición en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este control específico de la plataforma Android controla la transición que se usa al abrir `SwipeView` . Se consume en XAML estableciendo la `SwipeView.SwipeTransitionMode` propiedad Bindable en un valor de la `SwipeTransitionMode` enumeración:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core" >
    <StackLayout>
        <SwipeView android:SwipeView.SwipeTransitionMode="Drag">
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
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

SwipeView swipeView = new Xamarin.Forms.SwipeView();
swipeView.On<Android>().SetSwipeTransitionMode(SwipeTransitionMode.Drag);
// ...
```

El `SwipeView.On<Android>` método especifica que este específico de la plataforma solo se ejecutará en Android. El `SwipeView.SetSwipeTransitionMode` método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se utiliza para controlar la transición que se utiliza al abrir `SwipeView` . La `SwipeTransitionMode` enumeración proporciona dos valores posibles:

- `Reveal`indica que los elementos que se van a deslizar se revelarán cuando se deslice el contenido por el dedo `SwipeView` y es el valor predeterminado de la `SwipeView.SwipeTransitionMode` propiedad.
- `Drag`indica que los elementos que se van a deslizar se arrastrarán hasta la vista a medida que `SwipeView` se deslice el contenido.

Además, el `SwipeView.GetSwipeTransitionMode` método se puede utilizar para devolver el `SwipeTransitionMode` que se aplica a `SwipeView` .

El resultado es que un `SwipeTransitionMode` valor especificado se aplica a `SwipeView` , que controla la transición que se utiliza al abrir `SwipeView` :

[![Captura de pantalla de SwipeView SwipeTransitionModes, en Android](swipeview-swipetransitionmode-images/swipetransitionmode.png "SwipeTransitionModes en Android")](swipeview-swipetransitionmode-images/swipetransitionmode-large.png#lightbox "SwipeTransitionModes en Android")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
