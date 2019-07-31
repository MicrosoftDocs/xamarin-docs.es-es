---
title: Selección de ubicación y color de la barra de herramientas TabbedPage en Android
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el específico de la plataforma Android que establece la posición y el color de la barra de herramientas en un TabbedPage.
ms.prod: xamarin
ms.assetid: A5C68D6A-9A5F-42EE-845D-1E5B0CB1544E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 045a2dda350a8d8fc60d8985d94907157f1bcb49
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649826"
---
# <a name="tabbedpage-toolbar-placement-and-color-on-android"></a>Selección de ubicación y color de la barra de herramientas TabbedPage en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

> [!IMPORTANT]
> Las características específicas de la plataforma que establecen el color de la barra de [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) herramientas en un están ahora obsoletas y se han [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor) reemplazado por las propiedades y [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor) . Para obtener más información, vea [crear un TabbedPage](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md#creating-a-tabbedpage).

Estas funcionalidades específicas de plataforma se utilizan para establecer la selección de ubicación y color de la barra de herramientas en un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Que se consumen en XAML estableciendo el [ `TabbedPage.ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.ToolbarPlacementProperty) propiedad adjunta a un valor de la [ `ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) enumeración y el [ `TabbedPage.BarItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarItemColorProperty) y [ `TabbedPage.BarSelectedItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarSelectedItemColorProperty) adjunta propiedades a un [ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.ToolbarPlacement="Bottom"
            android:TabbedPage.BarItemColor="Black"
            android:TabbedPage.BarSelectedItemColor="Red">
    ...
</TabbedPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetToolbarPlacement(ToolbarPlacement.Bottom)
             .SetBarItemColor(Color.Black)
             .SetBarSelectedItemColor(Color.Red);
```

El `TabbedPage.On<Android>` método especifica que solo se ejecutarán estas funcionalidades específicas de plataforma en Android. El [ `TabbedPage.SetToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetToolbarPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement)) método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para establecer la posición de la barra de herramientas en un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), con el [ `ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) enumeración que proporciona los siguientes valores:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Default) : indica que la barra de herramientas se coloca en la ubicación predeterminada en la página. Se trata de la parte superior de la página en teléfonos y la parte inferior de la página en otras expresiones de dispositivo.
- [`Top`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Top) : indica que la barra de herramientas se coloca en la parte superior de la página.
- [`Bottom`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Bottom) : indica que la barra de herramientas se coloca en la parte inferior de la página.

Además, el [ `TabbedPage.SetBarItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetBarItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage},Xamarin.Forms.Color)) y [ `TabbedPage.SetBarSelectedItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetBarSelectedItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage},Xamarin.Forms.Color)) métodos se usan para establecer el color de elementos de barra de herramientas y elementos de barra de herramientas seleccionado, respectivamente.

> [!NOTE]
> El [ `GetToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.GetToolbarPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})), [ `GetBarItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.GetBarItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})), y [ `GetBarSelectedItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.GetBarSelectedItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})) métodos pueden usarse para recuperar la selección de ubicación y color de la [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) barra de herramientas.

El resultado es que se pueden establecer la posición de la barra de herramientas, el color de los elementos de la barra de herramientas y el color del elemento seleccionado de la barra de herramientas en un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage):

![](tabbedpage-toolbar-placement-color-images/tabbedpage-toolbar-placement.png)

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
