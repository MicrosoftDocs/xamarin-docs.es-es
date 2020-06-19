---
title: VisualElement desenfoque en iOS
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir el específico de la plataforma iOS que aplica el desenfoque a un VisualElement.
ms.prod: xamarin
ms.assetid: 2DE3B65E-B96E-4ECD-92DF-AA42D5205C44
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ce28a9ef2d5cbf4bdf1c12a85ea64ac9bb8bee8b
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137012"
---
# <a name="visualelement-blur-on-ios"></a>VisualElement desenfoque en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma específica de iOS se usa para desenfocar el contenido que se encuentra debajo de él y se puede aplicar a cualquiera [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Se consume en XAML estableciendo la [`VisualElement.BlurEffect`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.BlurEffectProperty) propiedad adjunta en un valor de la [`BlurEffectStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) enumeración:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
  ...
  <AbsoluteLayout HorizontalOptions="Center">
      <Image Source="monkeyface.png" />
      <BoxView x:Name="boxView" ios:VisualElement.BlurEffect="ExtraLight" HeightRequest="300" WidthRequest="300" />
  </AbsoluteLayout>
  ...
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

boxView.On<iOS>().UseBlurEffect(BlurEffectStyle.ExtraLight);
```

El `BoxView.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. [ `VisualElement.UseBlurEffect` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. UseBlurEffect ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement}, Xamarin.Forms . PlatformConfiguration. iOSSpecific. BlurEffectStyle)), en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para aplicar el efecto de desenfoque, y la [`BlurEffectStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) enumeración proporciona cuatro valores: [`None`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.None) , [`ExtraLight`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.ExtraLight) , [`Light`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Light) y [`Dark`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Dark) .

El resultado es que [`BlurEffectStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) se aplica un especificado a la [`BoxView`](xref:Xamarin.Forms.BoxView) instancia de, que desenfoca el [`Image`](xref:Xamarin.Forms.Image) superpuesto por debajo de él:

![](applying-blur-images/blur-effect.png "Blur Effect Platform-Specific")

> [!NOTE]
> Al agregar un efecto de desenfoque a un [`VisualElement`](xref:Xamarin.Forms.VisualElement) , los eventos de toque seguirán recibiendo `VisualElement` .

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
