---
title: FlyoutPage sombra en iOS
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir el específico de la plataforma iOS que controla si la página de detalles de un FlyoutPage tiene una sombra aplicada, al revelar la página de control flotante.
ms.prod: xamarin
ms.assetid: FB907EA2-C00A-43A5-84B1-A43584C867A5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0b05cb37a2399f438b9003e39341feb138bce490
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940821"
---
# <a name="flyoutpage-shadow-on-ios"></a>FlyoutPage sombra en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este control específico de la plataforma determina si la página de detalles de una [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) tiene una sombra aplicada, al revelar la página de control flotante. Se consume en XAML estableciendo la `FlyoutPage.ApplyShadow` propiedad Bindable en `true` :

```xaml
<FlyoutPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                  ios:FlyoutPage.ApplyShadow="true">
    ...
</FlyoutPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSFlyoutPageCS : FlyoutPage
{
    public iOSFlyoutPageCS(ICommand restore)
    {
        On<iOS>().SetApplyShadow(true);
        // ...
    }
}
```

El `FlyoutPage.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. El `FlyoutPage.SetApplyShadow` método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se utiliza para controlar si la página de detalles de una [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) tiene una sombra aplicada, al revelar la página de control flotante. Además, el `GetApplyShadow` método se puede utilizar para determinar si la sombra se aplica a la página de detalles de un `FlyoutPage` .

El resultado es que la página de detalles de un [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) puede tener una sombra aplicada, al revelar la página de control flotante:

[![Captura de pantalla de un FlyoutPage con y sin sombra](flyoutpage-shadow-images/shadow.png "FlyoutPage con y sin sombra")](flyoutpage-shadow-images/shadow-large.png#lightbox "FlyoutPage con y sin sombra")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
