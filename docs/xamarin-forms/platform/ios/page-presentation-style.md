---
title: Estilo de presentación de página modal en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo usar el modo de presentación específico de la plataforma iOS para establecer el estilo de presentación de una página modal.
ms.prod: xamarin
ms.assetid: C791F7CF-330A-44BA-987A-4CFCCBB9278B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 3b1a88968334bed42be53119c26de43ef9cd1419
ms.sourcegitcommit: eb23b7d745d1090376f9def07e0f11cb089494d0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2019
ms.locfileid: "72171068"
---
# <a name="modal-page-presentation-style-on-ios"></a>Estilo de presentación de página modal en iOS

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma específica de iOS se usa para establecer el estilo de presentación de una página modal. Se consume en XAML estableciendo el `Page.ModalPresentationStyle` propiedad enlazable para un `UIModalPresentationStyle` valor de enumeración:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.ModalPresentationStyle="FormSheet">
    ...
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSModalFormSheetPageCS : ContentPage
{
    public iOSModalFormSheetPageCS()
    {
        On<iOS>().SetModalPresentationStyle(UIModalPresentationStyle.FormSheet);
        ...
    }
}
```

El `Page.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El `Page.SetModalPresentationStyle` método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para establecer el estilo de presentación modal en un [ `Page` ](xref:Xamarin.Forms.Page) especificando uno de los siguientes `UIModalPresentationStyle` (enumeración) valores:

- `FullScreen`, que establece el estilo de presentación modal para abarcar toda la pantalla. De forma predeterminada, las páginas modales se muestran con este estilo de presentación.
- `FormSheet`, que establece el estilo de presentación modal sea menor que la pantalla y centrado en.

Además, el `GetModalPresentationStyle` método puede utilizarse para recuperar el valor actual de la `UIModalPresentationStyle` enumeración que se aplica a la [ `Page` ](xref:Xamarin.Forms.Page).

El resultado es que el estilo de presentación modal en un [ `Page` ](xref:Xamarin.Forms.Page) se pueden establecer:

[![](page-presentation-style-images/modal-presentation-style-small.png "Estilos de presentación modales en un iPad")](page-presentation-style-images/modal-presentation-style-large.png#lightbox "Estilos de presentación modales en un iPad")

> [!NOTE]
> Las páginas que usan este específicos de la plataforma para establecer el estilo de presentación modal deben usar la navegación modal. Para obtener más información, consulte [páginas modales de Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
