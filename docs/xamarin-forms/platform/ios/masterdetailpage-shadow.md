---
title: MasterDetailPage sombra en iOS
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir el específico de la plataforma iOS que controla si la página de detalles de un MasterDetailPage tiene una sombra aplicada, al revelar la página maestra.
ms.prod: xamarin
ms.assetid: FB907EA2-C00A-43A5-84B1-A43584C867A5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: aaf94536d41da47aec10fc655f9d053b753da5a2
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135972"
---
# <a name="masterdetailpage-shadow-on-ios"></a>MasterDetailPage sombra en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este control específico de la plataforma determina si la página de detalles de una [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) tiene una sombra aplicada, al revelar la página maestra. Se consume en XAML estableciendo la `MasterDetailPage.ApplyShadow` propiedad Bindable en `true` :

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                  ios:MasterDetailPage.ApplyShadow="true">
    ...
</MasterDetailPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSMasterDetailPageCS : MasterDetailPage
{
    public iOSMasterDetailPageCS(ICommand restore)
    {
        On<iOS>().SetApplyShadow(true);
        // ...
    }
}
```

El `MasterDetailPage.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. El `MasterDetailPage.SetApplyShadow` método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se utiliza para controlar si la página de detalles de una [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) tiene una sombra aplicada, al revelar la página maestra. Además, el `GetApplyShadow` método se puede utilizar para determinar si la sombra se aplica a la página de detalles de un `MasterDetailPage` .

El resultado es que la página de detalles de un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) puede tener una sombra aplicada, al revelar la página maestra:

[![Captura de pantalla de un MasterDetailPage con y sin sombra](masterdetailpage-shadow-images/shadow.png "MasterDetailPage con y sin sombra")](masterdetailpage-shadow-images/shadow-large.png#lightbox "MasterDetailPage con y sin sombra")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
