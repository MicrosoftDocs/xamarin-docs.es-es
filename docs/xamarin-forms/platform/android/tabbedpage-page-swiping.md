---
title: Página deslizante de TabbedPage en Android
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el específico de la plataforma Android que permite deslizar rápidamente con un gesto de dedo horizontal entre las páginas de un TabbedPage.
ms.prod: xamarin
ms.assetid: D1C09CCB-7246-41A4-8BD2-FA6FABCF1C72
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 47a941b88ef22a24383f54aad72563a4814ac077
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649945"
---
# <a name="tabbedpage-page-swiping-on-android"></a>Página deslizante de TabbedPage en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma Android específica se usa para habilitar el deslizamiento con un dedo horizontal entre las páginas de [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)un. Se consume en XAML estableciendo el [ `TabbedPage.IsSwipePagingEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty) propiedad adjunta un `boolean` valor:

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.OffscreenPageLimit="2"
            android:TabbedPage.IsSwipePagingEnabled="true">
    ...
</TabbedPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetOffscreenPageLimit(2)
             .SetIsSwipePagingEnabled(true);
```

El `TabbedPage.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El [ `TabbedPage.SetIsSwipePagingEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetIsSwipePagingEnabled(Xamarin.Forms.BindableObject,System.Boolean)) método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se utiliza para habilitar el gesto de deslizar rápidamente entre las páginas de un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Además, el `TabbedPage` clase en el `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` espacio de nombres también tiene un [ `EnableSwipePaging` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.EnableSwipePaging(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})) método que permite este específicos de la plataforma, y un [ `DisableSwipePaging` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.DisableSwipePaging(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})) método que deshabilita Esta plataforma específica. El [ `TabbedPage.OffscreenPageLimit` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty) propiedad adjunta, y [ `SetOffscreenPageLimit` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetOffscreenPageLimit(Xamarin.Forms.BindableObject,System.Int32)) método, se utilizan para establecer el número de páginas que se deben conservar en un estado de inactividad a cada lado de la página actual.

El resultado es que la paginación a través de las páginas se muestran por Deslizar rápidamente un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) está habilitado:

![](tabbedpage-page-swiping-images/tabbedpage-swipe.png)

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
