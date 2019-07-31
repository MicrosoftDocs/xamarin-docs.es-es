---
title: Barra de navegación MasterDetailPage en Windows
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir la plataforma específica de Windows que contrae la barra de navegación en un MasterDetailPage.
ms.prod: xamarin
ms.assetid: 0E7436C9-FA3E-40CD-801C-3F7ED95C412D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: dc79421f7be3a35fe19f239fa24f6a14429953ac
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656889"
---
# <a name="masterdetailpage-navigation-bar-on-windows"></a>Barra de navegación MasterDetailPage en Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma universal de Windows específica de la plataforma se usa para contraer la barra de [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)navegación en un y se consume en XAML [`MasterDetailPage.CollapseStyle`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapseStyleProperty) estableciendo [`MasterDetailPage.CollapsedPaneWidth`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidthProperty) las propiedades adjuntas y:

```xaml
<MasterDetailPage ...
                  xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
                  windows:MasterDetailPage.CollapseStyle="Partial"
                  windows:MasterDetailPage.CollapsedPaneWidth="48">
  ...
</MasterDetailPage>

```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetCollapseStyle(CollapseStyle.Partial).CollapsedPaneWidth(148);
```

El `MasterDetailPage.On<Windows>` método especifica que solo se ejecutarán este específicos de la plataforma en Windows. El [ `Page.SetCollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.SetCollapseStyle(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle)) método, en el [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se usa para especificar el estilo de contraer, con el [ `CollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle) enumeración que proporciona dos valores: [ `Full` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Full) y [ `Partial` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Partial). El [ `MasterDetailPage.CollapsedPaneWidth` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage},System.Double)) método se utiliza para especificar el ancho de una barra de navegación parcialmente contraído.

El resultado es que un determinado [ `CollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle) se aplica a la [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) instancia, con el ancho también se especifica:

[![](masterdetailpage-navigation-bar-images/collapsed-navigation-bar.png "Contraer barra de navegación específicos de la plataforma")](masterdetailpage-navigation-bar-images/collapsed-navigation-bar-large.png#lightbox "Contraer barra de navegación específicos de la plataforma")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
