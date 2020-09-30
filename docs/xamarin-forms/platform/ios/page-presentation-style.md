---
title: Estilo de presentación de página modal en iOS
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir la plataforma específica de iOS que establece el estilo de presentación de una página modal.
ms.prod: xamarin
ms.assetid: C791F7CF-330A-44BA-987A-4CFCCBB9278B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9ed0c2b9b0916349b11f64e83c57f2737c302e92
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557470"
---
# <a name="modal-page-presentation-style-on-ios"></a>Estilo de presentación de página modal en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma específica de iOS se usa para establecer el estilo de presentación de una página modal y, además, se puede usar para mostrar páginas modales con fondos transparentes. Se consume en XAML estableciendo la `Page.ModalPresentationStyle` propiedad Bindable en un `UIModalPresentationStyle` valor de enumeración:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.ModalPresentationStyle="OverFullScreen">
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
        On<iOS>().SetModalPresentationStyle(UIModalPresentationStyle.OverFullScreen);
        ...
    }
}
```

El `Page.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. El `Page.SetModalPresentationStyle` método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para establecer el estilo de Presentación modal en un [`Page`](xref:Xamarin.Forms.Page) especificando uno de los siguientes `UIModalPresentationStyle` valores de enumeración:

- `FullScreen`, que establece el estilo de Presentación modal para abarcar toda la pantalla. De forma predeterminada, las páginas modales se muestran con este estilo de presentación.
- `FormSheet`, que establece el estilo de Presentación modal en centrada en y menor que la pantalla.
- `Automatic`, que establece el estilo de Presentación modal en el valor predeterminado elegido por el sistema. Para la mayoría de los controladores de vista, `UIKit` asigna este a `UIModalPresentationStyle.PageSheet` , pero algunos controladores de vistas del sistema pueden asignarlo a un estilo diferente.
- `OverFullScreen`, que establece el estilo de Presentación modal para cubrir la pantalla.
- `PageSheet`, que establece el estilo de Presentación modal para cubrir el contenido subyacente.

Además, `GetModalPresentationStyle` se puede usar el método para recuperar el valor actual de la `UIModalPresentationStyle` enumeración que se aplica a [`Page`](xref:Xamarin.Forms.Page) .

El resultado es que se puede establecer el estilo de Presentación modal en un [`Page`](xref:Xamarin.Forms.Page) :

[![Estilos de presentación modales](page-presentation-style-images/modal-presentation-style-small.png)](page-presentation-style-images/modal-presentation-style-large.png#lightbox "Estilos de presentación modales")

> [!NOTE]
> Las páginas que usan este tipo específico de la plataforma para establecer el estilo de Presentación modal deben usar la navegación modal. Para obtener más información, vea [ Xamarin.Forms páginas modales](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)