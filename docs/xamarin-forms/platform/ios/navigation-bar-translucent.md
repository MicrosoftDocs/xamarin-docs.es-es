---
title: NavigationPage bar translucidez en iOS
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir el específico de la plataforma iOS que cambia la transparencia de la barra de navegación en un NavigationPage.
ms.prod: xamarin
ms.assetid: 1150941B-56DB-4781-BE36-A4C4F9F2C500
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 99e163364161287a8506bfc741d737edfaf88e4c
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556768"
---
# <a name="navigationpage-bar-translucency-on-ios"></a>NavigationPage bar translucidez en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma específica de iOS se usa para cambiar la transparencia de la barra de navegación en un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) y se utiliza en XAML estableciendo la [`NavigationPage.IsNavigationBarTranslucent`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucentProperty) propiedad adjunta en un `boolean` valor:

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                BackgroundColor="Blue"
                ios:NavigationPage.IsNavigationBarTranslucent="true">
  ...
</NavigationPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

(App.Current.MainPage as Xamarin.Forms.NavigationPage).BackgroundColor = Color.Blue;
(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().EnableTranslucentNavigationBar();
```

El `NavigationPage.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. [ `NavigationPage.EnableTranslucentNavigationBar` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. EnableTranslucentNavigationBar ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage})), en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para que la barra de navegación sea translúcida. Además, la [`NavigationPage`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage) clase del espacio de `Xamarin.Forms.PlatformConfiguration.iOSSpecific` nombres también tiene [ `DisableTranslucentNavigationBar` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. DisableTranslucentNavigationBar ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage})) que restaura la barra de navegación a su estado predeterminado y a [ `SetIsNavigationBarTranslucent` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. SetIsNavigationBarTranslucent ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage}, System. Boolean)) que se puede usar para alternar la transparencia de la barra de navegación llamando a [ `IsNavigationBarTranslucent` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. IsNavigationBarTranslucent ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage})):

```csharp
(App.Current.MainPage as Xamarin.Forms.NavigationPage)
  .On<iOS>()
  .SetIsNavigationBarTranslucent(!(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().IsNavigationBarTranslucent());
```

El resultado es que se puede cambiar la transparencia de la barra de navegación:

![Barra de navegación traslúcida específica de la plataforma](navigation-bar-translucent-images/translucent-navigation-bar.png)

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)