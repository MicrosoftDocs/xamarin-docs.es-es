---
title: Puntear en el control deslizante de iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el específico de la plataforma iOS que permite establecer la propiedad Slider. Value al puntear en la barra deslizante.
ms.prod: xamarin
ms.assetid: D0915D37-9A59-4728-BB6A-FE094A661275
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 573b68097724c976ce73b51e3b7ba21b52f7a776
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68651778"
---
# <a name="slider-thumb-tap-on-ios"></a>Puntear en el control deslizante de iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este específico de la plataforma iOS permite [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) establecer la propiedad pulsando en una posición en la [`Slider`](xref:Xamarin.Forms.Slider) barra, en lugar de tener que arrastrar el `Slider` control. Se consume en XAML estableciendo el [ `Slider.UpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.UpdateOnTapProperty) propiedad enlazable a `true`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Slider ... ios:Slider.UpdateOnTap="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var slider = new Xamarin.Forms.Slider();
slider.On<iOS>().SetUpdateOnTap(true);
```

El `Slider.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El [ `Slider.SetUpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.SetUpdateOnTap(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Slider},System.Boolean)) método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para controlar si una derivación de la `Slider` barra establecerá el [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propiedad. Además, el [ `Slider.GetUpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.GetUpdateOnTap(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Slider})) método puede utilizarse para devolver si una derivación de en el `Slider` barra establecerá el `Slider.Value` propiedad.

El resultado es que una derivación de en el [ `Slider` ](xref:Xamarin.Forms.Slider) puede mover la barra de la `Slider` thumb y establezca el [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propiedad:

![](slider-thumb-images/slider-updateontap.png "Actualización del control deslizante al pulsar habilitado")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
