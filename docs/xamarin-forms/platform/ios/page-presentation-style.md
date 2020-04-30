---
title: Estilo de presentación de página modal en iOS
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo usar el modo de presentación específico de la plataforma iOS para establecer el estilo de presentación de una página modal.
ms.prod: xamarin
ms.assetid: C791F7CF-330A-44BA-987A-4CFCCBB9278B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2020
ms.openlocfilehash: 5078b280499929e0e2e3691539cf1927b4c79fe7
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517534"
---
# <a name="modal-page-presentation-style-on-ios"></a>Estilo de presentación de página modal en iOS

[![Descargar el](~/media/shared/download.png) ejemplo descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma específica de iOS se usa para establecer el estilo de presentación de una página modal. Se consume en XAML estableciendo la `Page.ModalPresentationStyle` propiedad Bindable en un `UIModalPresentationStyle` valor de enumeración:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.ModalPresentationStyle="FormSheet">
    ...
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

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

El `Page.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. El `Page.SetModalPresentationStyle` método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para establecer el estilo de presentación [`Page`](xref:Xamarin.Forms.Page) modal en un especificando uno de `UIModalPresentationStyle` los siguientes valores de enumeración:

- `FullScreen`, que establece el estilo de Presentación modal para abarcar toda la pantalla. De forma predeterminada, las páginas modales se muestran con este estilo de presentación.
- `FormSheet`, que establece el estilo de Presentación modal en centrada en y menor que la pantalla.
- `Automatic`, que establece el estilo de Presentación modal en el valor predeterminado elegido por el sistema. Para la mayoría de los `UIKit` controladores de vista `UIModalPresentationStyle.PageSheet`, asigna este a, pero algunos controladores de vistas del sistema pueden asignarlo a un estilo diferente.
- `OverFullScreen`, que establece el estilo de Presentación modal para cubrir la pantalla.
- `PageSheet`, que establece el estilo de Presentación modal para cubrir el contenido subyacente.

Además, se puede `GetModalPresentationStyle` usar el método para recuperar el valor actual de la `UIModalPresentationStyle` enumeración que se aplica a. [`Page`](xref:Xamarin.Forms.Page)

El resultado es que se puede establecer el estilo de [`Page`](xref:Xamarin.Forms.Page) Presentación modal en un:

[![](page-presentation-style-images/modal-presentation-style-small.png "Modal Presentation Styles")](page-presentation-style-images/modal-presentation-style-large.png#lightbox "Modal Presentation Styles")

> [!NOTE]
> Las páginas que usan este tipo específico de la plataforma para establecer el estilo de Presentación modal deben usar la navegación modal. Para obtener más información, vea [páginas modales de Xamarin. Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
