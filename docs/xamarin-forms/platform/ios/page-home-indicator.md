---
title: Visibilidad del indicador de inicio en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo usar la plataforma específica de iOS que establece la visibilidad del indicador de inicio en una página.
ms.prod: xamarin
ms.assetid: F81022E0-3C6C-49C0-A000-FAF6574D3FB7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: c8acf826eb5b3f42f62803ac1caaaa5929c5ea75
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648859"
---
# <a name="home-indicator-visibility-on-ios"></a>Visibilidad del indicador de inicio en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma específica de iOS establece la visibilidad del indicador de inicio en un [`Page`](xref:Xamarin.Forms.Page). Se consume en XAML estableciendo la [`Page.PrefersHomeIndicatorAutoHidden`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Page.PrefersHomeIndicatorAutoHiddenProperty) propiedad Bindable `boolean`en:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersHomeIndicatorAutoHidden="true">
    ...
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersHomeIndicatorAutoHidden(true);
```

El `Page.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El [`Page.SetPrefersHomeIndicatorAutoHidden`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Page.SetPrefersHomeIndicatorAutoHidden(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Page},System.Boolean)) método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, controla la visibilidad del indicador de inicio. Además, se puede [`Page.PrefersHomeIndicatorAutoHidden`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Page.PrefersHomeIndicatorAutoHidden(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Page})) usar el método para recuperar la visibilidad del indicador de inicio.

El resultado es que se puede controlar la visibilidad del indicador de [`Page`](xref:Xamarin.Forms.Page) Inicio en un:

![Captura de pantalla de la visibilidad del indicador de inicio en una página de iOS](page-home-indicator-images/home-indicator-visibility.png "Visibilidad del indicador de inicio de página")

> [!NOTE]
> Este específico de la plataforma se puede aplicar [`ContentPage`](xref:Xamarin.Forms.ContentPage)a [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)los [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)objetos, [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) , y.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
