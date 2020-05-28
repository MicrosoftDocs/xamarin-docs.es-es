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
ms.openlocfilehash: 2abc255964df35fbdfeb4191911c57df9be99fd9
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128016"
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

[![](page-presentation-style-images/modal-presentation-style-small.png "Modal Presentation Styles")](page-presentation-style-images/modal-presentation-style-large.png#lightbox "Modal Presentation Styles")

> [!NOTE]
> Las páginas que usan este tipo específico de la plataforma para establecer el estilo de Presentación modal deben usar la navegación modal. Para obtener más información, vea [ Xamarin.Forms páginas modales](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
