---
title: SwipeView deslizar el modo de transición en Android
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el específico de la plataforma Android que controla la transición que se usa al abrir un SwipeView.
ms.prod: xamarin
ms.assetid: 6B1F8213-9D62-4C40-9F04-881F1371B5AA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
ms.openlocfilehash: 077d4a8a9530bf074fde710dd08c1fbea49668ef
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490479"
---
# <a name="swipeview-swipe-transition-mode-on-android"></a>SwipeView deslizar el modo de transición en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este control específico de la plataforma Android controla la transición que se usa al abrir una `SwipeView`. Se consume en XAML estableciendo la propiedad `SwipeView.SwipeTransitionMode` enlazable en un valor de la enumeración `SwipeTransitionMode`:

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

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

SwipeView swipeView = new Xamarin.Forms.SwipeView();
swipeView.On<Android>().SetSwipeTransitionMode(SwipeTransitionMode.Drag);
// ...
```

El `SwipeView.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El método `SwipeView.SetSwipeTransitionMode`, en el espacio de nombres [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , se utiliza para controlar la transición que se utiliza al abrir una `SwipeView`. La enumeración `SwipeTransitionMode` proporciona dos valores posibles:

- `Reveal` indica que los elementos que se van a deslizar rápidamente se revelarán cuando se deslice el `SwipeView` contenido, y es el valor predeterminado de la propiedad `SwipeView.SwipeTransitionMode`.
- `Drag` indica que los elementos que se desplazan al dedo se arrastrarán en la vista a medida que se deslice el contenido de la `SwipeView`.

Además, se puede utilizar el método `SwipeView.GetSwipeTransitionMode` para devolver el `SwipeTransitionMode` que se aplica a la `SwipeView`.

El resultado es que un valor de `SwipeTransitionMode` especificado se aplica al `SwipeView`, que controla la transición que se utiliza al abrir el `SwipeView`:

[![Captura de pantalla de SwipeView SwipeTransitionModes, en Android](swipeview-swipetransitionmode-images/swipetransitionmode.png "SwipeTransitionModes en Android")](swipeview-swipetransitionmode-images/swipetransitionmode-large.png#lightbox "SwipeTransitionModes en Android")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
