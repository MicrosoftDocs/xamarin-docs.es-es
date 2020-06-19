---
title: VisualElement quitar sombras en iOS
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir el específico de la plataforma iOS que habilita una sombra paralela en un VisualElement.
ms.prod: xamarin
ms.assetid: 2147FD66-058E-4BE5-840A-369842B26EC4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7410386e10f605fdeed452fe37755c1e48e6b9b9
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136999"
---
# <a name="visualelement-drop-shadows-on-ios"></a>VisualElement quitar sombras en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma específica de iOS se usa para habilitar una sombra paralela en un [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Se consume en XAML estableciendo la [`VisualElement.IsShadowEnabled`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsShadowEnabledProperty) propiedad adjunta en `true` , junto con una serie de propiedades adjuntas adicionales opcionales que controlan la sombra paralela:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <BoxView ...
                 ios:VisualElement.IsShadowEnabled="true"
                 ios:VisualElement.ShadowColor="Purple"
                 ios:VisualElement.ShadowOpacity="0.7"
                 ios:VisualElement.ShadowRadius="12">
            <ios:VisualElement.ShadowOffset>
                <Size>
                    <x:Arguments>
                        <x:Double>10</x:Double>
                        <x:Double>10</x:Double>
                    </x:Arguments>
                </Size>
            </ios:VisualElement.ShadowOffset>
         </BoxView>
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var boxView = new BoxView { Color = Color.Aqua, WidthRequest = 100, HeightRequest = 100 };
boxView.On<iOS>()
       .SetIsShadowEnabled(true)
       .SetShadowColor(Color.Purple)
       .SetShadowOffset(new Size(10,10))
       .SetShadowOpacity(0.7)
       .SetShadowRadius(12);
```

El `VisualElement.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. [ `VisualElement.SetIsShadowEnabled` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. SetIsShadowEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement}, System. Boolean)), en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se utiliza para controlar si una sombra paralela está habilitada en `VisualElement` . Además, se pueden invocar los métodos siguientes para controlar la sombra paralela:

- [ `SetShadowColor` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. SetShadowColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement}, Xamarin.Forms . Color): establece el color de la sombra paralela. El color predeterminado es [`Color.Default`](xref:Xamarin.Forms.Color.Default*) .
- [ `SetShadowOffset` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. SetShadowOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement}, Xamarin.Forms . Tamaño): establece el desplazamiento de la sombra paralela. El desplazamiento cambia la dirección en la que se proyecta la sombra y se especifica como un [`Size`](xref:Xamarin.Forms.Size) valor. Los `Size` valores de la estructura se expresan en unidades independientes del dispositivo, donde el primer valor es la distancia a la izquierda (valor negativo) o a la derecha (valor positivo) y el segundo valor es la distancia por encima (valor negativo) o inferior (valor positivo). El valor predeterminado de esta propiedad es (0,0, 0,0), lo que hace que se convierta la sombra en torno a cada lado de `VisualElement` .
- [ `SetShadowOpacity` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. SetShadowOpacity ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement}, System. Double)): establece la opacidad de la sombra paralela, con el valor comprendido entre 0,0 (transparente) y 1,0 (opaco). El valor de opacidad predeterminado es 0,5.
- [ `SetShadowRadius` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. SetShadowRadius ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement}, System. Double)): establece el radio de desenfoque que se usa para representar la sombra paralela. El valor de radio predeterminado es 10,0.

> [!NOTE]
> El estado de una sombra paralela se puede consultar llamando a [ `GetIsShadowEnabled` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. GetIsShadowEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement})), [ `GetShadowColor` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. GetShadowColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement})), [ `GetShadowOffset` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. GetShadowOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement})), [ `GetShadowOpacity` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. GetShadowOpacity ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement})) y [ `GetShadowRadius` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. GetShadowRadius ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement})).

El resultado es que se puede habilitar una sombra paralela en un [`VisualElement`](xref:Xamarin.Forms.VisualElement) :

![](drop-shadow-images/drop-shadow.png "Drop shadow enabled")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
