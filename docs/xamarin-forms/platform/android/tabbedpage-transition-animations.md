---
title: Animaciones de transición de páginas de TabbedPage en Android
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo utilizar el específico de la plataforma Android que deshabilita las animaciones de transición al navegar por las páginas de un TabbedPage.
ms.prod: xamarin
ms.assetid: 2DB4EA6D-9CED-4137-BAB2-B20A457B1CA3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 4f8d6ec2b06855364970bc9b672c3d3f7b9bfdfc
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649838"
---
# <a name="tabbedpage-page-transition-animations-on-android"></a>Animaciones de transición de páginas de TabbedPage en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma Android específica se usa para deshabilitar animaciones de transición al navegar por las páginas, ya sea mediante programación o cuando se usa la barra de [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)pestañas, en. Se consume en XAML estableciendo el `TabbedPage.IsSmoothScrollEnabled` propiedad enlazable a `false`:

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.IsSmoothScrollEnabled="false">
    ...
</TabbedPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetIsSmoothScrollEnabled(false);
```

El `TabbedPage.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El `TabbedPage.SetIsSmoothScrollEnabled` método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para controlar si las animaciones de transición se mostrará al navegar entre páginas en un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Además, el `TabbedPage` clase en el `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` espacio de nombres también tiene los métodos siguientes:

- `IsSmoothScrollEnabled`, que se usa para recuperar datos si las animaciones de transición se mostrará al navegar entre páginas en un `TabbedPage`.
- `EnableSmoothScroll`, que se usa para habilitar animaciones de transición al navegar entre páginas en un `TabbedPage`.
- `DisableSmoothScroll`, que se usa para deshabilitar las animaciones de transición al navegar entre páginas en un `TabbedPage`.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
