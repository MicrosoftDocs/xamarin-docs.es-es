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
ms.openlocfilehash: 125685150243ba8e8099cbfbdfec90e5a0b4d6b7
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138585"
---
# <a name="simultaneous-pan-gesture-recognition-on-ios"></a>Reconocimiento simultáneo de gestos de pan en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Cuando un [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) se adjunta a una vista dentro de una vista de desplazamiento, los movimientos de la panorámica se capturan mediante `PanGestureRecognizer` y no se pasan a la vista de desplazamiento. Por lo tanto, la vista de desplazamiento ya no se desplazará.

Este específico de la plataforma iOS permite `PanGestureRecognizer` a un en una vista de desplazamiento capturar y compartir el gesto de panorámica con la vista de desplazamiento. Se consume en XAML estableciendo la [`Application.PanGestureRecognizerShouldRecognizeSimultaneously`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.PanGestureRecognizerShouldRecognizeSimultaneouslyProperty) propiedad adjunta en `true` :

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.PanGestureRecognizerShouldRecognizeSimultaneously="true">
    ...
</Application>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetPanGestureRecognizerShouldRecognizeSimultaneously(true);
```

El `Application.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. [ `Application.SetPanGestureRecognizerShouldRecognizeSimultaneously` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Application. SetPanGestureRecognizerShouldRecognizeSimultaneously ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Application}, System. Boolean)), en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se utiliza para controlar si un reconocedor de gestos panorámico en una vista de desplazamiento capturará el gesto de movimiento panorámico, o capturará y compartirá el movimiento de panorámica con la vista de desplazamiento. Además, [ `Application.GetPanGestureRecognizerShouldRecognizeSimultaneously` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Application. GetPanGestureRecognizerShouldRecognizeSimultaneously ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Application})) que se puede usar para devolver si el gesto de movimiento panorámico se comparte con la vista de desplazamiento que contiene [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) .

Por lo tanto, con esta opción específica de la plataforma habilitada, cuando un [`ListView`](xref:Xamarin.Forms.ListView) contiene un [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) , tanto `ListView` como `PanGestureRecognizer` recibirán el gesto de pan y lo procesarán. Sin embargo, con esta plataforma específica deshabilitada, cuando un `ListView` contiene `PanGestureRecognizer` , el `PanGestureRecognizer` capturará el gesto de pan y lo procesará, y `ListView` no recibirá el movimiento de la panorámica.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
