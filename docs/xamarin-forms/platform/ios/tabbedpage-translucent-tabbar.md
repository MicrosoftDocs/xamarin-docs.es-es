---
title: TabbedPage barra de pestañas translúcidas en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir la plataforma específica de iOS que establece el modo translucidez de la barra de pestañas en un TabbedPage.
ms.prod: xamarin
ms.assetid: 9581AE81-9557-47AD-8B07-25A1AC5F055B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
ms.openlocfilehash: 55fda0be2e260c5aa4a34ab2dcc1ac3cac33b92a
ms.sourcegitcommit: 6c60914b380ff679bbffd7790edd4d5e18005d0a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/21/2020
ms.locfileid: "80070288"
---
# <a name="tabbedpage-translucent-tab-bar-on-ios"></a>TabbedPage barra de pestañas translúcidas en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma específica de iOS se usa para establecer el modo translucidez de la barra de pestañas en un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage). Se consume en XAML estableciendo la propiedad `TabbedPage.TranslucencyMode` enlazable en un valor de enumeración de `TranslucencyMode`:

```xaml
<TabbedPage ...
            xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
            ios:TabbedPage.TranslucencyMode="Opaque">
    ...
</TabbedPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetTranslucencyMode(TranslucencyMode.Opaque);
```

El método `TabbedPage.On<iOS>` especifica que este específico de la plataforma solo se ejecutará en iOS. El método `TabbedPage.SetTranslucencyMode`, en el espacio de nombres [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , se usa para establecer el modo translucidez de la barra de pestañas en un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) especificando uno de los siguientes valores de enumeración de `TranslucencyMode`:

- `Default`, que establece la barra de pestañas en su modo predeterminado translucidez. Este es el valor predeterminado de la propiedad `TabbedPage.TranslucencyMode`.
- `Translucent`, que establece que la barra de pestañas sea traslúcida.
- `Opaque`, que establece que la barra de pestañas sea opaca.

Además, se puede utilizar el método `GetTranslucencyMode` para recuperar el valor actual de la enumeración `TranslucencyMode` que se aplica a la [`TabbedPage`](xref:Xamarin.Forms.TabbedPage).

El resultado es que se puede establecer el modo translucidez de la barra de pestañas en un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) :

![Captura de pantalla de barras de pestaña translúcidas y opacas en iOS](tabbedpage-translucent-tabbar-images/translucencymodes.png "Barras de pestañas translúcidas y opacas")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
