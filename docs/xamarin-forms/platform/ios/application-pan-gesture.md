---
title: Reconocimiento simultáneo de gestos de pan en iOS
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir el específico de la plataforma iOS que permite el reconocimiento simultáneo de gestos de pan para su uso en una aplicación.
ms.prod: xamarin
ms.assetid: 883D89DA-F8FF-4B97-9C3F-2DD05C96A495
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f1baf5d71cc25a1f84ff683e9d0072f34715c0da
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373242"
---
# <a name="simultaneous-pan-gesture-recognition-on-ios"></a>Reconocimiento simultáneo de gestos de pan en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

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

- [PlatformSpecifics (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)