---
title: Separador de barra NavigationPage en iOS
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir la plataforma de iOS específica que oculta la línea de separación y la sombra que se encuentra en la parte inferior de la barra de navegación de un NavigationPage.
ms.prod: xamarin
ms.assetid: 5A45748A-6779-4441-82F2-415BD68473B9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 44bd1ca75220d0aefe2f26bf0e3ba1afcde93150
ms.sourcegitcommit: 40a56bbc1e038a9181101580ad18a4584edb5ab0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2021
ms.locfileid: "93372358"
---
# <a name="navigationpage-bar-separator-on-ios"></a>Separador de barra NavigationPage en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este específico de la plataforma iOS oculta la línea de separación y la sombra que se encuentra en la parte inferior de la barra de navegación en un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . Se consume en XAML estableciendo la [`NavigationPage.HideNavigationBarSeparator`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) propiedad Bindable en `false` :

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ios:NavigationPage.HideNavigationBarSeparator="true">

</NavigationPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;

public class iOSTitleViewNavigationPageCS : Xamarin.Forms.NavigationPage
{
    public iOSTitleViewNavigationPageCS()
    {
        On<iOS>().SetHideNavigationBarSeparator(true);
    }
}
```

El `NavigationPage.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. [ `NavigationPage.SetHideNavigationBarSeparator` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. SetHideNavigationBarSeparator ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage}, System. Boolean)), en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se utiliza para controlar si el separador de la barra de navegación está oculto. Además, [ `NavigationPage.HideNavigationBarSeparator` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. HideNavigationBarSeparator ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage})) que se puede usar para devolver si el separador de la barra de navegación está oculto.

El resultado es que se puede ocultar el separador de la barra de navegación de un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) :

![Barra de navegación NavigationPage oculta](navigation-bar-separator-images/navigationpage-hideseparatorbar.png)

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)