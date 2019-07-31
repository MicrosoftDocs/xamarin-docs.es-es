---
title: VisualElement desenfoque en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el específico de la plataforma iOS que aplica el desenfoque a un VisualElement.
ms.prod: xamarin
ms.assetid: 2DE3B65E-B96E-4ECD-92DF-AA42D5205C44
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 2536902a03618fd50fad5019f79cb834b0c748f0
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642965"
---
# <a name="visualelement-blur-on-ios"></a>VisualElement desenfoque en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma específica de iOS se usa para desenfocar el contenido que se encuentra debajo de él y se puede [`VisualElement`](xref:Xamarin.Forms.VisualElement)aplicar a cualquiera. Se consume en XAML estableciendo el [ `VisualElement.BlurEffect` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.BlurEffectProperty) propiedad adjunta a un valor de la [ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) enumeración:

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

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

boxView.On<iOS>().UseBlurEffect(BlurEffectStyle.ExtraLight);
```

El `BoxView.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El [ `VisualElement.UseBlurEffect` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.UseBlurEffect(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle)) método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para aplicar el efecto de desenfoque, con el [ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) enumeración que proporciona cuatro valores: [ `None` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.None), [ `ExtraLight` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.ExtraLight), [ `Light` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Light), y [ `Dark` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Dark).

El resultado es que un determinado [ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) se aplica a la [ `BoxView` ](xref:Xamarin.Forms.BoxView) instancia que desenfoca la [ `Image` ](xref:Xamarin.Forms.Image) por niveles por debajo de ella:

![](applying-blur-images/blur-effect.png "Específico de la plataforma efecto de desenfoque")

> [!NOTE]
> Al agregar un efecto de desenfoque a una [ `VisualElement` ](xref:Xamarin.Forms.VisualElement), eventos de toque, todavía recibirán el `VisualElement`.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
