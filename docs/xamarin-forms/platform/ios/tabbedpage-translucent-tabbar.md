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
ms.openlocfilehash: 8127191aab80d81fc2e532e3d5e14931b834eeae
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137038"
---
# <a name="tabbedpage-translucent-tab-bar-on-ios"></a>TabbedPage barra de pestañas translúcidas en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma específica de iOS se usa para establecer el modo translucidez de la barra de pestañas en un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Se consume en XAML estableciendo la `TabbedPage.TranslucencyMode` propiedad Bindable en un `TranslucencyMode` valor de enumeración:

```xaml
<TabbedPage ...
            xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
            ios:TabbedPage.TranslucencyMode="Opaque">
    ...
</TabbedPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetTranslucencyMode(TranslucencyMode.Opaque);
```

El `TabbedPage.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. El `TabbedPage.SetTranslucencyMode` método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para establecer el modo translucidez de la barra de pestañas en un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) especificando uno de los siguientes `TranslucencyMode` valores de enumeración:

- `Default`, que establece la barra de pestañas en su modo predeterminado translucidez. Este es el valor predeterminado de la propiedad `TabbedPage.TranslucencyMode`.
- `Translucent`, que establece que la barra de pestañas sea traslúcida.
- `Opaque`, que establece que la barra de pestañas sea opaca.

Además, `GetTranslucencyMode` se puede usar el método para recuperar el valor actual de la `TranslucencyMode` enumeración que se aplica a [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) .

El resultado es que se puede establecer el modo translucidez de la barra de pestañas de un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) :

![Captura de pantalla de barras de pestaña translúcidas y opacas en iOS](tabbedpage-translucent-tabbar-images/translucencymodes.png "Barras de pestañas translúcidas y opacas")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
