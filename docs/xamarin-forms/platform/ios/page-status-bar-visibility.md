---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 69594924f26afff133d8f211199cac44e66254d9
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128034"
---
# <a name="page-status-bar-visibility-on-ios"></a>Visibilidad de la barra de estado de la página en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma específica de iOS se usa para establecer la visibilidad de la barra de estado en un [`Page`](xref:Xamarin.Forms.Page) e incluye la capacidad de controlar el modo en que la barra de estado entra o sale de `Page` . Se consume en XAML estableciendo la `Page.PrefersStatusBarHidden` propiedad adjunta en un valor de la `StatusBarHiddenMode` enumeración y, opcionalmente, la `Page.PreferredStatusBarUpdateAnimation` propiedad adjunta en un valor de la `UIStatusBarAnimation` enumeración:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

El `Page.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. El `Page.SetPrefersStatusBarHidden` método, en el `Xamarin.Forms.PlatformConfiguration.iOSSpecific` espacio de nombres, se usa para establecer la visibilidad de la barra de estado en un [`Page`](xref:Xamarin.Forms.Page) especificando uno de los `StatusBarHiddenMode` valores de enumeración: `Default` , `True` o `False` . Los `StatusBarHiddenMode.True` `StatusBarHiddenMode.False` valores y establecen la visibilidad de la barra de estado independientemente de la orientación del dispositivo y el `StatusBarHiddenMode.Default` valor oculta la barra de estado en un entorno de compactación vertical.

El resultado es que se puede establecer la visibilidad de la barra de estado en una [`Page`](xref:Xamarin.Forms.Page) :

![](page-status-bar-visibility-images/hide-status-bar.png "Status Bar Visibility Platform-Specific")

> [!NOTE]
> En [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) , el valor de `StatusBarHiddenMode` enumeración especificado también actualizará la barra de estado en todas las páginas secundarias. En todos los demás [`Page`](xref:Xamarin.Forms.Page) tipos derivados de, el `StatusBarHiddenMode` valor de enumeración especificado solo actualizará la barra de estado en la página actual.

El `Page.SetPreferredStatusBarUpdateAnimation` método se usa para establecer el modo en que la barra de estado entra o sale [`Page`](xref:Xamarin.Forms.Page) especificando uno de los `UIStatusBarAnimation` valores de enumeración: `None` , `Fade` o `Slide` . Si `Fade` `Slide` se especifica el valor de enumeración o, se ejecuta una animación de 0,25 segundos a medida que la barra de estado entra o sale de `Page` .

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
