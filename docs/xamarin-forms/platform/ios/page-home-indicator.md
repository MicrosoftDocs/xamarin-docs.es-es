---
title: Visibilidad del indicador de inicio en iOS
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo usar la plataforma específica de iOS que establece la visibilidad del indicador de inicio en una página.
ms.prod: xamarin
ms.assetid: F81022E0-3C6C-49C0-A000-FAF6574D3FB7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e76684ffb293380c283153c35c907acc50e40aab
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128081"
---
# <a name="home-indicator-visibility-on-ios"></a>Visibilidad del indicador de inicio en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma específica de iOS establece la visibilidad del indicador de inicio en un [`Page`](xref:Xamarin.Forms.Page) . Se consume en XAML estableciendo la [`Page.PrefersHomeIndicatorAutoHidden`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Page.PrefersHomeIndicatorAutoHiddenProperty) propiedad Bindable en `boolean` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersHomeIndicatorAutoHidden="true">
    ...
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersHomeIndicatorAutoHidden(true);
```

El `Page.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. [ `Page.SetPrefersHomeIndicatorAutoHidden` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Page. SetPrefersHomeIndicatorAutoHidden ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Page}, System. Boolean)), en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres controla la visibilidad del indicador de inicio. Además, [ `Page.PrefersHomeIndicatorAutoHidden` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Page. PrefersHomeIndicatorAutoHidden ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Page})) que se puede usar para recuperar la visibilidad del indicador de inicio.

El resultado es que se puede controlar la visibilidad del indicador de inicio en un [`Page`](xref:Xamarin.Forms.Page) :

![Captura de pantalla de la visibilidad del indicador de inicio en una página de iOS](page-home-indicator-images/home-indicator-visibility.png "Visibilidad del indicador de página principal")

> [!NOTE]
> Este específico de la plataforma se puede aplicar a los [`ContentPage`](xref:Xamarin.Forms.ContentPage) [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) objetos,, [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) y [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) .

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
