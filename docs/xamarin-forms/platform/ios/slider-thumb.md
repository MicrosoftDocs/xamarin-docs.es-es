---
title: Puntear en el control deslizante de iOS
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir el específico de la plataforma iOS que permite establecer la propiedad Slider. Value al puntear en la barra deslizante.
ms.prod: xamarin
ms.assetid: D0915D37-9A59-4728-BB6A-FE094A661275
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d8ca0dfb533dc5fb0b7442b85de41dcf7c18fec8
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938546"
---
# <a name="slider-thumb-tap-on-ios"></a>Puntear en el control deslizante de iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este específico de la plataforma iOS permite [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) establecer la propiedad pulsando en una posición en la [`Slider`](xref:Xamarin.Forms.Slider) barra, en lugar de tener que arrastrar el `Slider` control. Se consume en XAML estableciendo la [`Slider.UpdateOnTap`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.UpdateOnTapProperty) propiedad Bindable en `true` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Slider ... ios:Slider.UpdateOnTap="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var slider = new Xamarin.Forms.Slider();
slider.On<iOS>().SetUpdateOnTap(true);
```

El `Slider.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. [ `Slider.SetUpdateOnTap` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Slider. SetUpdateOnTap ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Slider}, System. Boolean)), en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se utiliza para controlar si una pulsación en la `Slider` barra establecerá la [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) propiedad. Además, [ `Slider.GetUpdateOnTap` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Slider. GetUpdateOnTap ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Slider})) que se puede usar para devolver si una pulsación en la `Slider` barra establecerá la `Slider.Value` propiedad.

El resultado es que una pulsación en la [`Slider`](xref:Xamarin.Forms.Slider) barra puede trasladar el `Slider` control de posición y establecer la [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) propiedad:

![Actualización del control deslizante en TAP habilitada](slider-thumb-images/slider-updateontap.png)

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
