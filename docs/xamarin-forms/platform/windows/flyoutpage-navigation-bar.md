---
title: Barra de navegación FlyoutPage en Windows
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir la plataforma específica de Windows que contrae la barra de navegación en un FlyoutPage.
ms.prod: xamarin
ms.assetid: 0E7436C9-FA3E-40CD-801C-3F7ED95C412D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e2f6a4c3eaf2668eff7e2123378175f7844a3413
ms.sourcegitcommit: 1decf2c65dc4c36513f7dd459a5df01e170a036f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98115150"
---
# <a name="flyoutpage-navigation-bar-on-windows"></a>Barra de navegación FlyoutPage en Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta Plataforma universal de Windows específica de la plataforma se usa para contraer la barra de navegación en un [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) y se consume en XAML estableciendo las [`FlyoutPage.CollapseStyle`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.FlyoutPage.CollapseStyleProperty) [`FlyoutPage.CollapsedPaneWidth`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.FlyoutPage.CollapsedPaneWidthProperty) propiedades adjuntas y:

```xaml
<FlyoutPage ...
                  xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
                  windows:FlyoutPage.CollapseStyle="Partial"
                  windows:FlyoutPage.CollapsedPaneWidth="48">
  ...
</FlyoutPage>

```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetCollapseStyle(CollapseStyle.Partial).CollapsedPaneWidth(148);
```

El `FlyoutPage.On<Windows>` método especifica que este específico de la plataforma solo se ejecutará en Windows. [ `Page.SetCollapseStyle` ] (XREF: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. FlyoutPage. SetCollapseStyle ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . FlyoutPage}, Xamarin.Forms . PlatformConfiguration. WindowsSpecific. CollapseStyle)), en el [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se usa para especificar el estilo de contraer, con la [`CollapseStyle`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle) enumeración que proporciona dos valores: [`Full`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Full) y [`Partial`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Partial) . [ `FlyoutPage.CollapsedPaneWidth` ] (XREF: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. FlyoutPage. CollapsedPaneWidth ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . El método FlyoutPage}, System. Double)) se usa para especificar el ancho de una barra de navegación contraída parcialmente.

El resultado es que [`CollapseStyle`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle) se aplica un especificado a la [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) instancia, pero también se especifica el ancho:

[![Barra de navegación contraída específica de la plataforma](flyoutpage-navigation-bar-images/collapsed-navigation-bar.png)](flyoutpage-navigation-bar-images/collapsed-navigation-bar-large.png#lightbox "Barra de navegación contraída Platform-Specific")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)