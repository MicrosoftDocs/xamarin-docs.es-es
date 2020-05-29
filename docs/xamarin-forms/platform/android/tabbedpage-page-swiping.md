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
ms.openlocfilehash: 61c0137788303363769fdec80a16542e2d8bea5e
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128614"
---
# <a name="tabbedpage-page-swiping-on-android"></a>Página deslizante de TabbedPage en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma Android específica se usa para habilitar el deslizamiento con un dedo horizontal entre las páginas de un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Se consume en XAML estableciendo la [`TabbedPage.IsSwipePagingEnabled`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty) propiedad adjunta en un `boolean` valor:

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.OffscreenPageLimit="2"
            android:TabbedPage.IsSwipePagingEnabled="true">
    ...
</TabbedPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetOffscreenPageLimit(2)
             .SetIsSwipePagingEnabled(true);
```

El `TabbedPage.On<Android>` método especifica que este específico de la plataforma solo se ejecutará en Android. [ `TabbedPage.SetIsSwipePagingEnabled` ] (XREF: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. SetIsSwipePagingEnabled ( Xamarin.Forms . BindableObject, System. Boolean)), en el [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para habilitar el deslizamiento entre las páginas de un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Además, la `TabbedPage` clase del espacio de `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` nombres también tiene [ `EnableSwipePaging` ] (XREF: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. EnableSwipePaging ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage})) que habilita este método específico de la plataforma y un [ `DisableSwipePaging` ] (XREF: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. DisableSwipePaging ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage})) que deshabilita este específico de la plataforma. La [`TabbedPage.OffscreenPageLimit`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty) propiedad adjunta y [ `SetOffscreenPageLimit` ] (XREF: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. SetOffscreenPageLimit ( Xamarin.Forms . BindableObject, System. Int32)), se usan para establecer el número de páginas que deben conservarse en un estado de inactividad en cualquier lado de la página actual.

El resultado es que está habilitada la paginación de deslizamiento a través de las páginas que muestra un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) :

![](tabbedpage-page-swiping-images/tabbedpage-swipe.png)

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
