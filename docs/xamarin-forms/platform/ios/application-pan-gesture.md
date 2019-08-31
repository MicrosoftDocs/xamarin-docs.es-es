---
title: Reconocimiento simultáneo de gestos de pan en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el específico de la plataforma iOS que permite el reconocimiento simultáneo de gestos de pan para su uso en una aplicación.
ms.prod: xamarin
ms.assetid: 883D89DA-F8FF-4B97-9C3F-2DD05C96A495
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 64a39fc8a3a1bd764df424271493d95c5863590f
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2019
ms.locfileid: "70197997"
---
# <a name="simultaneous-pan-gesture-recognition-on-ios"></a>Reconocimiento simultáneo de gestos de pan en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Cuando un [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) se adjunta a una vista dentro de una vista desplazable, todo el pan gestos capturados por el `PanGestureRecognizer` y no se pasan a la vista de desplazamiento. Por lo tanto, ya no se desplazará la vista de desplazamiento.

Este específico de la plataforma iOS permite `PanGestureRecognizer` a un en una vista de desplazamiento capturar y compartir el gesto de panorámica con la vista de desplazamiento. Se consume en XAML estableciendo el [ `Application.PanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.PanGestureRecognizerShouldRecognizeSimultaneouslyProperty) propiedad adjunta `true`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.PanGestureRecognizerShouldRecognizeSimultaneously="true">
    ...
</Application>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetPanGestureRecognizerShouldRecognizeSimultaneously(true);
```

El `Application.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El [ `Application.SetPanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.SetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application},System.Boolean)) método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para controlar si un reconocedor de movimiento panorámico en una vista desplazable capturará el movimiento panorámico, o capturar y compartir el desplazamiento panorámico con la vista desplazable de gestos. Además, el [ `Application.GetPanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.GetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application})) método puede utilizarse para devolver si se comparte el movimiento panorámico con la vista de desplazamiento que contiene el [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer).

Por lo tanto, con este específicos de la plataforma habilitada, cuando un [ `ListView` ](xref:Xamarin.Forms.ListView) contiene un [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer), tanto el `ListView` y `PanGestureRecognizer` recibirán el movimiento panorámico y procesarlo. Sin embargo, con este específicos de la plataforma deshabilitada, cuando un `ListView` contiene un `PanGestureRecognizer`, el `PanGestureRecognizer` capturará el movimiento panorámico y procesarlos y el `ListView` no recibirá el movimiento panorámico.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
