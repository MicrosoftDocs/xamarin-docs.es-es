---
title: Selección de ubicación y color de la barra de herramientas TabbedPage en Android
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir el específico de la plataforma Android que establece la posición y el color de la barra de herramientas en un TabbedPage.
ms.prod: xamarin
ms.assetid: A5C68D6A-9A5F-42EE-845D-1E5B0CB1544E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f5a2be4bd61056a42593fc45e1abdd3679795bc0
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139989"
---
# <a name="tabbedpage-toolbar-placement-and-color-on-android"></a>Selección de ubicación y color de la barra de herramientas TabbedPage en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

> [!IMPORTANT]
> Las características específicas de la plataforma que establecen el color de la barra de herramientas en un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) están ahora obsoletas y se han reemplazado por las [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor) [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor) propiedades y. Para obtener más información, vea [crear un TabbedPage](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md#create-a-tabbedpage).

Estas características específicas de la plataforma se usan para establecer la posición y el color de la barra de herramientas en un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Se consumen en XAML estableciendo la [`TabbedPage.ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.ToolbarPlacementProperty) propiedad adjunta en un valor de la [`ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) enumeración y [`TabbedPage.BarItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarItemColorProperty) y las [`TabbedPage.BarSelectedItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarSelectedItemColorProperty) propiedades adjuntas a un [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.ToolbarPlacement="Bottom"
            android:TabbedPage.BarItemColor="Black"
            android:TabbedPage.BarSelectedItemColor="Red">
    ...
</TabbedPage>
```

Como alternativa, se pueden usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetToolbarPlacement(ToolbarPlacement.Bottom)
             .SetBarItemColor(Color.Black)
             .SetBarSelectedItemColor(Color.Red);
```

El `TabbedPage.On<Android>` método especifica que estas características específicas de la plataforma solo se ejecutarán en Android. [ `TabbedPage.SetToolbarPlacement` ] (XREF: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. SetToolbarPlacement ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage}, Xamarin.Forms . PlatformConfiguration. AndroidSpecific. ToolbarPlacement)), en el [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para establecer la posición de la barra de herramientas en un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) , con la [`ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) enumeración que proporciona los valores siguientes:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Default): indica que la barra de herramientas se coloca en la ubicación predeterminada de la página. Esta es la parte superior de la página en teléfonos y la parte inferior de la página en otras expresiones de dispositivo.
- [`Top`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Top): indica que la barra de herramientas se coloca en la parte superior de la página.
- [`Bottom`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Bottom): indica que la barra de herramientas se coloca en la parte inferior de la página.

Además, [ `TabbedPage.SetBarItemColor` ] (XREF: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. SetBarItemColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage}, Xamarin.Forms . Color)) y [ `TabbedPage.SetBarSelectedItemColor` ] (XREF: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. SetBarSelectedItemColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage}, Xamarin.Forms . Color)) para establecer el color de los elementos de la barra de herramientas y los elementos de la barra de herramientas seleccionados, respectivamente.

> [!NOTE]
> [ `GetToolbarPlacement` ] (XREF: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. GetToolbarPlacement ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage})), [ `GetBarItemColor` ] (XREF: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. GetBarItemColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage})) y [ `GetBarSelectedItemColor` ] (XREF: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. GetBarSelectedItemColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage})). los métodos se pueden usar para recuperar la posición y el color de la [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) barra de herramientas.

El resultado es que la ubicación de la barra de herramientas, el color de los elementos de la barra de herramientas y el color del elemento de la barra de herramientas seleccionado se pueden establecer en un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) :

![](tabbedpage-toolbar-placement-color-images/tabbedpage-toolbar-placement.png)

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
